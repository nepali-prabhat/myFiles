The motivation here is to see possible ways to make flows an trigger configs

[ eg doc reference](https://docs.google.com/document/d/1yP9jL6rtzUD2cKj7rRzwgxAN39m_AhJI5ao_88_rsHA/edit?ts=5f44b666)

*Note*: In the updates/create (after submit) function, we want to know the format of different field types to use/update them. They can be found in another [file](https://snippets.kalysys.com.au/snippets?id=5f4ca82cfc887215fdbe0652).

# create entry flow 

Here we are sourcing all the inputs through trigger config.
This strategy lets us use same flow for multiple triggers and since trigger is whats linked to the record, this makes it flexible

```
    [
        {
            "key": "CREATE_ENTRY",
            "description": "create entry in the record passed in trigger config",
            "name": "create entry for any record",
            "inputs": [
                {
                    "name": "values",
                    "source": "trigger-config",
                    "value": "values"
                },
                {
                    "name": "recordId",
                    "source": "trigger-config",
                    "value": "recordId"
                }
            ]
        }
    ]
```

# create entry trigger config ( `after_submit` / `before_submit`)

```
    {
        "condition":"function(context){return true}",
    	"inputs":[
          {
          	"name":"values",
            "type":"function",
            "value":"function(context){ function getFormData(fieldIdsInString) { const formDataValues = context.metaData.triggerInputs.data.formData.formValues; let extractedFields = {}; for (let data of formDataValues) { if (fieldIdsInString.includes(data.fieldId + \"\")) { extractedFields[data.fieldId + \"\"] = data; } } return extractedFields; } console.log(\"context\", context); const metadata = context.metaData; const triggerType = metadata.triggerType; const moduleEntries = metadata.moduleEntries; console.log(\"triggerType: \", triggerType); return { \"1308\": \"2020-08-30\", \"1309\": '[\"69631\"]', \"1315\": \"2020-09-02\", \"1316\": \"txt\", \"1317\": '[\"23643\"]', \"3218\": '[\"34120\"]', \"3219\": \"\", \"3220\": \"1asd12a1jfutyj\", \"3221\": \"2020-08-30T19:17:00.000Z\", \"3223\": '[\"34123\"]', \"3224\": \"[]\", \"3225\": '[\"70095\"]', \"3226\": '[\"34131\"]', \"3228\": \"[]\", }; }"
          },
          {
          	"name":"recordId",
            "type":"hard-coded",
            "value":"3915"
          }
        ]
    }
```

the function used to get the value in readable format:

```
function a(context) {
    function getFormData(fieldIdsInString) {
        const formDataValues =
            context.metaData.triggerInputs.data.formData.formValues;
        let extractedFields = {};
        for (let data of formDataValues) {
            if (fieldIdsInString.includes(data.fieldId + "")) {
                extractedFields[data.fieldId + ""] = data;
            }
        }
        return extractedFields;
    }
    console.log("context", context);
    const metadata = context.metaData;
    const triggerType = metadata.triggerType;
    const moduleEntries = metadata.moduleEntries;
    return {
        "1308": "2020-08-30",
        "1309": "['69631']",
        "1315": "2020-09-02",
        "1316": "txt",
        "1317": "[\"23643\"]",
        "3218": "[\"34120\"]",
        "3219": "",
        "3220": "1asd12a1jfutyj",
        "3221": "2020-08-30T19:17:00.000Z",
        "3223": "[\"34123\"]",
        "3224": "[]",
        "3225": "[\"70095\"]",
        "3226": "[\"34131\"]",
        "3228": "[]",
    };
}
```

# send sms:

```
    [
        {
            "key": "SEND_SMS",
            "description": "send sms for all-fields record",
            "name": "Send sms all fields",
            "condition": "function(context){ return true; }",
            "inputs": [
                {
                    "name": "entryId",
                    "source": "trigger-inputs",
                    "value": "entryId"
                },
                {
                    "name": "recordId",
                    "source": "trigger-inputs",
                    "value": "recordId"
                },
                {
                    "name": "to",
                    "source": "hard-coded",
                    "value": [
                        {
                            "type": "dynamic",
                            "phone": "phone"
                        }
                    ]
                },
                {
                    "name": "templateId",
                    "source": "hard-coded",
                    "value": "70110"
                }
            ]
        }
    ]
```

Note the to field whose type can be dynamic. So we can reference the value of the entry by giving it a path.
Meaning we can do sth like this
```
{
    "type":"dynamic",
    "phone": "employee.phone"
}
```
# update entry flow (can be used by online forms to update a value for field not present in the form) (used by `after_submit`)

```
    [
        {
            "key": "UPDATE_ENTRY",
            "description": "test update entry after submit button",
            "name": "test update entry",
            "inputs": [
                {
                    "name": "values",
                    "source": "function",
                    "value": "function(context){ function getFormData(fieldIdsInString) { const formDataValues = context.metaData.triggerInputs.data.formData.formValues; let extractedFields = {}; for (let data of formDataValues) { if (fieldIdsInString.includes(data.fieldId + '')) { extractedFields[data.fieldId + ''] = data; } } return extractedFields; } const formValues = getFormData(['845','859','860']); return { '844': 'Kathmandu Guest House Road, Kathmandu, Nepal', '845': !formValues['845'].value, '860': formValues['860'].value+' '+formValues['859'].value };}"
                },
                {
                    "name": "recordId",
                    "source": "trigger-inputs",
                    "value": "recordId"
                },
                {
                    "name": "entryId",
                    "source": "trigger-inputs",
                    "value": "entryId"
                }
            ]
        }
    ]
```
Function in readable format
```
function a(context) {
    function getFormData(fieldIdsInString) {
        const formDataValues =
            context.metaData.triggerInputs.data.formData.formValues;
        let extractedFields = {};
        for (let data of formDataValues) {
            if (fieldIdsInString.includes(data.fieldId + "")) {
                extractedFields[data.fieldId + ""] = data;
            }
        }
        return extractedFields;
    }
    const formValues = getFormData(["845", "859", "860"]);
    return {
        "844": "Kathmandu Guest House Road, Kathmandu, Nepal",
        "845": !formValues["845"].value,
        "860": formValues["860"].value + " " + formValues["859"].value,
    };
}

```

# Multiple actions in one flow  (button trigger)
    1. send email to the entry's email address
    2. update assigned-to(select field) to some value (hard-coded from trigger_config)
    3. send email to the assigned-to field email (hard-coded from trigger_config)
```
    [
      {
        "key": "SEND_EMAIL",
        "description": "Send email, set assigned to and notify assigned to",
        "name": "Send email, set assigned to and notify assigned to",
        "condition": "function(context){ return true; }",
        "inputs": [
          {
            "name": "recordId",
            "source": "trigger-inputs",
            "value": "recordId"
          },
          {
            "name": "entryId",
            "source": "trigger-inputs",
            "value": "entryId"
          },
          {
            "name": "from",
            "source": "hard-coded",
            "value": {
              "type": "hard-coded",
              "name": "Kalysys",
              "address": "pandeypravat@gmail.com"
            }
          },
          {
            "name": "to",
            "source": "hard-coded",
            "value": [
              {
                "type": "dynamic",
                "address": "email"
              }
            ]
          },
          {
            "name": "templateId",
            "source": "function",
            "value": "function(context){ return 70101; }"
          }
        ]
      },
      {
            "key": "UPDATE_ENTRY",
            "description": "test update entry after submit button",
            "name": "test update entry",
            "inputs": [
                {
                    "name": "values",
                    "source": "trigger-config",
                    "value": "assigned_to_id"
                },
                {
                    "name": "recordId",
                    "source": "trigger-inputs",
                    "value": "recordId"
                },
                {
                    "name": "entryId",
                    "source": "trigger-inputs",
                    "value": "entryId"
                }
            ]
        },
      {
        "key": "SEND_EMAIL",
        "description": "Send email, set assigned to and notify assigned to",
        "name": "Send email, set assigned to and notify assigned to",
        "condition": "function(context){ return true; }",
        "inputs": [
          {
            "name": "recordId",
            "source": "trigger-inputs",
            "value": "recordId"
          },
          {
            "name": "entryId",
            "source": "trigger-inputs",
            "value": "entryId"
          },
          {
            "name": "from",
            "source": "hard-coded",
            "value": {
              "type": "hard-coded",
              "name": "Kalysys",
              "address": "prabhat@kalysys.com.au"
            }
          },
          {
            "name": "to",
            "source": "trigger-config",
            "value": "assigned_to_email"
          },
          {
            "name": "templateId",
            "source": "function",
            "value": "function(context){ return 70105; }"
          }
        ]
      }
    ]
```

Note the to field whose type can be dynamic. So we can reference the value of the entry by giving it a path.
Meaning we can do sth like this
```
{
    "type":"dynamic",
    "phone": "employee.phone"
}
```

# multiple actions config

```
    {
        "condition":"function(context){return true}",
        "renderCondition": "function(context){ return true; }",
        "inputs":[
          {
            "name":"assigned_to_id",
            "type":"function",
            "value": "function(context){ return { \"858\": '[\"23643\"]'};}"
          },
          {
            "name":"assigned_to_email",
            "type":"function",
            "value": "function(context){ return [ { \"type\": \"hard-coded\", \"address\": \"prabhat@kalysys.com.au\" } ];}"
          }
        ]
    }
```


