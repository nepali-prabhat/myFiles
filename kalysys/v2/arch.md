## Data structures / Architecture for v2

Requirement:
Generic data structures to be able to create new systems without code changes (extra dev work) ...

2 options:

1. Build generic system with configuration
2. Dynamically generate code as needed ???

strapi (Headless node cms)

-   dynamcally generate tables (or collections), model code as required
-   ie. dynamically/programmatically generate code files
-   more as helper to dev
-   db access using mongoose (mongo) and bookshelfjs (sql)

proposal:

Break current Kalysys data structures into the core / core business / kalysys domains

Core -> everything needed to build headless api using configuration only
Core business -> use core + extras + ui to build new systems with little code (or completely configuration only)
Kalysys Business System -> Use Core Business to build replica of current kalysys functionality

similar to orm data mapper pattern

Business objects + database persistor logics
(database persistors related to the business objects, but separate code. Not in logical/business object classes)

Most code only has to deal with logical objects
Logical objects may not not be strictly mapped to db

core

```typescript
interface FieldType {
    internalId: string; // machine readable name; globally unique
    name: string; // human readable name
    description?: string; // human readable description

    _schema: {}; // jsonSchema for validation
    _openApiMeta: {}; // extra info for api docs generation (eg. examples, extended description, schema overrides, etc)
}

interface Field {
    internalId: string; // machine readable name; unique for the record
    record: Record; // each field belongs to a Record
    fieldType: FieldType; // define type of data in corresponding value
    name: string; // human readable name
    settings: {
        mandatory: boolean;
        default?: any;
    };

    _openApiMeta: {}; // overrides for open api info from FieldType for specific use case
}

type PrivilegeExpression = string; // string that can be parsed by permission expression evaluator. this evaluates to boolean

interface RecordLevelPrivilege {
    // permissions for the record itself
    modify: PrivilegeExpression;
    access: PrivilegeExpression;
}

interface EntryLevelPrivilege {
    // permissions for the entries
    read: PrivilegeExpression;
    write: PrivilegeExpression;
    update: PrivilegeExpression;
    delete: PrivilegeExpression;
}

type Privilege = RecordLevelPrivilege & EntryLevelPrivilege;

interface Record {
    internalId: string; // machine readable name; unique at tenant level
    name: string; // human readable name
    owner: Tenant;
    fields: Map<string, Field>; // keyed by field Internal Id
    // fields: { [fieldInternalId: string]: Field }
    extend?: Record; // extend fields from another record
    // other info ? may be added by business objects
    meta: {
        icon?: string;
    };
    // how autoId will be formatted, current max, etc
    autoId: {
        format: {};
        current: number;
    };
    privilege: Privilege;
    subrecords: SubRecord[]; //
}

interface RecordEntry {
    id: string;
    record: Record;
    owner: Tenant;
    user: User;
    autoId: number; // incremental integer id
    source?: any;
    values: Map<string, any>; // keyed by internalId of field, value is any
    //{ [fieldInternalId: string]: any }
}

interface SubRecord extends Record {
    parent: Record;
}

interface SubRecordEntry extends RecordEntry {
    parent: RecordEntry;
    index: number;
}

interface Tenant extends RecordEntry {
    users: { user: User; roles: Role[] }[];
}

interface User extends RecordEntry {
    // Described here as an 'extends' but most likely a User table/collection and a User Record
    // User table/collection holds authentication related info needed by the system
    // User Record is other info dynamic we may/may not use
}

interface Role {
    tenant: Tenant;
    name: string;
    permissions: Permission[];
}

interface Permission {
    // TBD
}
```

### Code vs Configuration

We need to delineate what is defined by code and wat by configuration.
For maximum dymanic system, we want as much of the system to be defined by configuration.
But reducing unnecessary complexity is also an important factor.

If making a valid configuration change breaks the system and requires new code to fix, that is not truly 'configurable'.
By valid, something like making a true -> false. Adding/removing an item from a list, adding/removing a field, etc
If code needs an exact configuration to 'not break' then it is NOT configurable... atleast need to be able to use a reasonable range of values without breaking the system to be considered configurable.

### God record

Existing system is shaped like a tree rooted on a god record. But this implies the system can have multiple 'trees'.
Our code clearly does not support this...
Also this 'tree' is always exactly 3 levels deep: god record -> record -> record entry
god record defines some dynamic fields that all the records share

This architecture will split the 'RecordValues' to 'Records' and 'RecordEntries'
Records will still contain all the dynamic fields required, but they will be defined by code, not configuration

### tenant and record inheritance

Custom fields means adding tenant specific fields on a shared/common record.
This allows the common fields to be changed wihout touching the custom fields of each tenant.
And tenants (customers) can make custom changes without affecting functionality of other tehants.

But we are still essentially 'sharing' a single resource and allowing less previliged tenants (customers) to modify a resource belonging to a higher previliged tenant (kalysys)

Replace this with a sort-of 'inheritance' system: a record can point to another record and inherit those fields.
They are not 'copied'. Rather, code will manage them (by getting parent fields too when retrieving the record)
The record entries will contain values for all the fields.
So updating record -> can use only own fields
reading records -> all fields
record entries -> all fields

So each 'tenant' can get their own copies of the 'record' (with the same internal ids too, if needed)

Initial implementation can be for single level. But this 'concept' can be extended as needed

This should not have too much overhead, as the 'entry' data remains in a single object. The 'inheritance' (multiple data load) is only for the record, which only needs a single load and can potentially be cached in memory

As far as possible, tenants should not 'share' records.
They should get 'inherited copies' of shared records, if any
So they can add/edit fields there without having to actually 'write' to record without access to them.

All their record entries should also be referencing the 'inherited records'

### autoID:

Incremental Ids, one monotonically increasing sequence per record
As most records are under tenant ownership, this is effectively a unique sequence per internal id per tenant

AutoId may be enabled for all records by default (consistency, but some overhead per insert) or only for specific fields (but may bring inconsistency or extra complexity if we enable it after recordEntries are already created)

autoId field in the `Record` contains the `sequence generator` state (current value, etc) and info on how to format the value for display
autoId field in the `RecordEntry` is the generated autoId number for the entry

AutoId generation for documents should be monotonically increasing and unique. ie. no duplicates.
To prevent duplicates due to concurrent Read-Modify-Write operations, during new record inserts, a single `increment and return new value` db operation should be used to increment the `current` value of the sequence generator state in the record first, and the returned value used in the insert operation.

eg:

```js
// mongo (nodejs driver)
collection.findOneAndUpdate({ _id: 123 }, { $inc: { counter_field: 1 } }, { returnOriginal: false });
```

```sql
-- postgres
UPDATE the_table
SET the_column = the_column + 1
WHERE qualifier = X
RETURNING the_column;
```

Note: As this operation is atomic, it is not necessary for increment counter and insert to be in a single transaction. We need monotonicity and uniqueness, but not sequence continuity.

## User management:

User management + Authentication + User Access managenet (Roles, Permissions) is a feature provided by the platform itself.
`Users` are global. Shared across all tenants.
Each user can be added to multiple tenants with different roles.
