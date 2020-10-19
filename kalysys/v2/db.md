```typescript

// each interface represents a mongo collection

interface fieldTypes {
    _id: ObjectId
    internalId: string
    name: string
    description: string

    _schema: {}
    _openApiMeta: {}
}

interface records {
    _id: ObjectId
    internalId: string
    tenantId: ObjectId              // _id of owner tenant
    extendRecordId?: ObjectId       // _id of extended reccord
    subrecords: ObjectId[]
    parentRecordId?: ObjectId       // null if not subrecord
    autoId: {
        format: {}
        current: number
    }
    meta: {
        icon?: string
    }
    fields: {
        [fieldInternalId: string]: {
            fieldTypeId: ObjectId
            name: string
            settings: {
                mandatory: boolean
                default?: any
            }
            _openApiMeta: {}
        }
    }
}

interface recordEntries {
    _id: ObjectId
    recordId: ObjectId
    tenantId: ObjectId
    userId: ObjectId                // ObjectId of user who created this entry
    autoId: number
    values: { [fieldInternalId: string]: any }


    parentRecordEntryId?: ObjectId  // null if not subrecord entry
    index?: number                  // null if not subrecord entry
}

interface tenants {
    _id: ObjectId
    recordId: ObjectId      // _id of corresponding recordValue 

    // tenant users, roles, etc can be split off to separate collection if required
    users: {
        userId: ObjectId
        roles: {
            name: string
            permissons: {
                // TBD
            }[]
        }[]
    }[]
}

interface users {
    _id: ObjectId
}

```



```typescript

interface fieldTypes {
    id: serial
    internalId: varchar
    name: varchar
    descriptin: text
    schema: jsonb
    openApiMeta: jsonb
}

interface records {
    id: serial
    internalId: string
    tenantId: int

}

```