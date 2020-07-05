
# Activity Log

## How to put logs in activity log
The logs displayed in activity log is comming from `relationship` record. There we make a _many-to-one_ link between entries like:
```
{
  "linkedEntries": [
    {
      "recordId": 4576,
      "entryId": "14178"
    }
  ],
  "primaryEntry": {
    "recordId": 15169,
    "entryId": 15729
  }
}
```
Note: These are generally added by workflow.

## Activity Layout
In any record.settings, we can define how it will be displayed in activity log:

```
{
  "activityLayout": "sent email to {{2040}} at {{2046}}", 

  "activityFieldModifier":
  {
    "2046":"datetime", 
    "2040":"jsonArray"
  }
}
```

If any field needs to be modified, we pass it in "activityFieldModifier". The possible values are "datetime" and "jsonArray" for now. It can be extended easily if needed.

datetime will be displayed in human readable format
