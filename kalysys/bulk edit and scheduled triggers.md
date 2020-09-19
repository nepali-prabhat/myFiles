# Config for bulk execute + scheduled workflow

This doc describes bulk execution and scheduled triggers from the start. The scheduled triggers need to have a trigger type of `scheduled` and `startup` to run properly.

Read the gatchas and notes at the bottom at least once.

## Motivation of bulk execute:

The main significance of bulk execute is we might want to perform some action on n number of entries of a table. 

For eg, imagine a scenario in customers record(recordId:130) where we want to send email to all the customer entries where `next-payment-date` field's value is before next 5 days. During that time we need to execute a flow that sends email to all of those entries.

We make use of savedSearchId to filter all the entries that we are interested in and then execute a flow for each of the entry.

To do that, in `trigger-config`, we set `"bulkExecution":true` and pass the savedSearchId and recordId to the bulkConfig as:

```
"bulkExecution": true,
"bulkConfig":{
    "savedSearchId": 71741,
    "recordId": 130
},
```

## Motivation of scheduled workflow:

With bulk execition, we can now execute a flow for our filtered entries. Now we want to go one step further and execute the bulk execution at certain time intervals.

For example, 5 days before the start of each month, we want to send emails for invoices to each of our employees. We do that through scheduled workflow.

We do this through a cron time string that defines the interval we want to run the flow for all the entries and we are done! We set this up in `trigger-config` as:

```
"scheduled": true,
"schedulerConfig": {
    "cron": "30 5 4 16 8 *"
},
```

## Gotchas

- The trigger needs to be made/changed from "manage record"-->"trigger" and not directly from triggers record.
    
- The cron string's time is set based on Australia/Sydney meaning the cron time string needs to be relative to that.
    
- The month's number starts from 0. (i.e. September is not 9, its 8)

## Notes

- If cron string is confusing, its incredibly easy. More about it [here](https://snippets.kalysys.com.au/snippets?id=5f610e2efc887215fdbe0663)

- Use [this](https://crontab.guru/) to generate cron string. But be sure to do -1 on the month value because this site uses 1-12 to indicate month whereas ours uses 0-11.

- Although here scheduled triggers and bulkExecution are discussed together, they can be run independent of each other. A bulk execution can be set up for button trigger and a scheduled trigger can be set up without bulk execution.

------------------------------------------------
### The complete trigger config

You can find this sample trigger config [here too](https://snippets.kalysys.com.au/snippets?id=5f61035bfc887215fdbe0661)

```
{
	"condition":"function(context){return true}",
    "renderCondition": "function(context){ return true; }",
	"bulkExecution": true,
  	"bulkConfig":{
    	"savedSearchId": 71741,
      	"recordId": 130
    },
	"scheduled": true,
    "schedulerConfig": {
        "cron": "30 5 4 16 8 *"
    },
  	"inputs":[]
}
```

### The flow config

You can find this sample flow config [here too](https://snippets.kalysys.com.au/snippets?id=5f6102ebfc887215fdbe0660)
```
[
    {
        "key": "SEND_EMAIL",
        "description": "Send scheduled email",
        "name": "Send scheduled email",
        "condition": "function(context){ return true; }",
        "inputs": [
            {
                "name": "recordId",
                "source": "trigger-inputs",
                "value": "entry.recordId"
            },
            {
                "name": "entryId",
                "source": "trigger-inputs",
                "value": "entry.recordValueId"
            },
            {
                "name": "from",
                "source": "hard-coded",
                "value": {
                    "type": "hard-coded",
                    "name": "Kalysys Notifications",
                    "address": "noreply-notification@kalysys.com.au"
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
                "source": "hard-coded",
                "value": "31272"
            }
        ]
    }
]
```

Here the recordId and entryId are being taken from triggerInputs in path entry.recordId and entry.recordValueId respectively. 

This is a common scenerio for most flow with bulkExecution:true. If you need any information of the entry the action is being run for, you get it from triggerInputs.entry.`someFieldId|recordId|entryId|formId|...`