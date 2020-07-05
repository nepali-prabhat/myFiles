# Worflow Sign now integration (sample)

## Background: 

[scope video by Lez](https://www.loom.com/share/6411033a7e844fe3877508d9d4011678)

What this does is it uploads a document to signNow and creates an invite link to sign the document. 

The uploaded document is made by our document creator. 

We can add some tags in the body of the document from UI itself. These tags will be converted to input fields to sign by the signNow api.

## Some flow configurations:

1. Hard coded from:

  ```
    [
      {
        "key": "INVITE_ESIGNATURE",
        "description": "Send invite to sign a document",
        "name": "Send invite to sign a document",
        "inputs": [
          {
            "name": "entryId",
            "source": "trigger-inputs",
            "value": "entryId"
          },
          {
            "name": "templateId",
            "source": "trigger-config",
            "value": "templateId"
          },
          {
            "name": "from",
            "source": "hard-coded",
            "value": {
                "type": "hard-coded",
                "address": "prabhat@kalysys.com.au"
              }
          },
          {
            "name": "to",
            "source": "hard-coded",
            "value": [
                  {
                "role": "Client1",
                "address": {
                    "source": "entry",
                    "value": "email"
                  }
              }
            ]
          }
        ]
      }
    ]
  ```

2. Dynamic from (passed from trigger config)

This way, we can use same flow to invite for signature from any record

  ```
    [
      {
        "key": "INVITE_ESIGNATURE",
        "description": "Send invite to sign a document",
        "name": "Send invite to sign a document",
        "inputs": [
          {
            "name": "entryId",
            "source": "trigger-inputs",
            "value": "entryId"
          },
          {
            "name": "templateId",
            "source": "trigger-config",
            "value": "templateId"
          },
          {
            "name": "from",
            "source": "trigger-config",
            "value": "from"
          },
          {
            "name": "to",
            "source": "hard-coded",
            "value": [
                  {
                "role": "Client1",
                "address": {
                    "source": "entry",
                    "value": "email"
                  }
              }
            ]
          }
        ]
      }
    ]
  ```

**NOTE**: 
  * In both above flows, the templateId is comming from trigger-config 
  
  * To has only one value for role Client1 meaning we can’t have role other than Client1
  
  * To make flow for multiple clients, we need to configure another flow.

## Some Triggers:

These triggers are of type buttons and generally set up by any client in any record from manageRecord/triggers.

The templateId must be valid template made by client. 
For eg, demo massage therapist may have templateId of 29506 that is used here.

  * using flow with hard coded from value
    ```
      {
        "inputs":[
          {
              "name": "templateId",
              "type":"hard-coded",
              "value": 29506
          }
        ]
      }
    ```

  * Using flow with dynamic from value (passing static address from trigger-config)

    ```
      {
        "inputs":[
          {
              "name": "templateId",
              "type":"hard-coded",
              "value": 29506
          },
          {
              "name": "from",
              "type": "hard-coded",
              "value": {
                  "type": "hard-coded",
                  "address": "kit@kalysys.com.au"
                }
          }
        ]
      }
    ```

  * Using flow with dynamic from value (passing company-settings address from trigger-config)

    ```
      {
        "inputs":[
          {
              "name": "templateId",
              "type":"hard-coded",
              "value": 29506
          },
          {
              "name": "from",
              "type": "hard-coded",
              "value": {
                  "type": "company-settings"
                }
          }
        ]
      }
    ```