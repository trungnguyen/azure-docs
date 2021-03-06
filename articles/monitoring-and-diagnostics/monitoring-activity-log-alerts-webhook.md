# Webhooks for Azure Activity Log alerts
As part of the definition of an Action Group you are able to configure Webhook endpoints to receive Activity Log Alerts notifications. Webhooks allow you to route these notification to other systems for post-processing or custom actions. This article shows what the payload for the HTTP POST to a webhook looks like.

>[!NOTE]
>This feature is currently in public preview. Not all functionality may be available at this time.
>
>

For information on the setup and schema for Azure Activity Log alerts, [see this page instead](monitoring-activity-log-alerts.md).

For information on the setup and schema for Action Groups, [see this page instead](monitoring-action-groups.md)

## Authenticating the webhook
The webhook can authenticate using either of these methods:

***Needs Review***
1. **Token-based authorization** - The webhook URI is saved with a token ID, for example, `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2. **Basic authorization** - The webhook URI is saved with a username and password, for example, `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## Payload schema
The JSON payload contained in the POST operation differs based on the payload's data.context.activityLog.eventSource field.

###Common
```json
{
    "schemaId": "unknown",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Operation",
                "correlationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "eventSource": "Administrative",
                "eventTimestamp": "2017-03-29T15:43:08.0019532+00:00",
                "eventDataId": "8195a56a-85de-4663-943e-1a2bf401ad94",
                "level": "Informational",
                "operationName": "microsoft.insights/actionGroups/write",
                "operationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "status": "Started",
                "subStatus": "",
                "subscriptionId": "52c65f65-0518-4d37-9719-7dbbfc68c57a",
                "submissionTimestamp": "2017-03-29T15:43:20.3863637+00:00",
                ...
            }
        },
        "properties": {}
    }
}
```
###Administrative
```json
{
    "schemaId": "unknown",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "authorization": {
                    "action": "microsoft.insights/actionGroups/write",
                    "scope": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/microsoft.insights/actionGroups/IncidentActions"
                },
                "claims": "{...}",
                "caller": "me@contoso.com",
                "description": "",
                "httpRequest": "{...}",
                "resourceId": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/microsoft.insights/actionGroups/IncidentActions",
                "resourceGroupName": "CONTOSO-TEST",
                "resourceProviderName": "microsoft.insights",
                "resourceType": "microsoft.insights/actionGroups"
            }
        },
        "properties": {}
    }
}

```
###ServiceHealth
```json
{
    "schemaId": "unknown",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "properties": {
                    "title": "...",
                    "service": "...",
                    "region": "...",
                    "communication": "...",
                    "incidentType": "Incident",
                    "trackingId": "...",
                    "groupId": "...",
                    "impactStartTime": "3/29/2017 3:43:21 PM",
                    "impactMitigationTime": "3/29/2017 3:43:21 PM",
                    "eventCreationTime": "3/29/2017 3:43:21 PM",
                    "impactedServices": "[{...}]",
                    "defaultLanguageTitle": "...",
                    "defaultLanguageContent": "...",
                    "stage": "Active",
                    "communicationId": "...",
                    "version": "0.1"
                },
            }
        },
        "properties": {}
    }
}
```

For specific schema details on Service Health Notification activity log alerts [click here](monitoring-service-notifications.md)
For specific schema details on all other Activity Log alerts [click here](monitoring-overview-activity-logs.md)

| Element Name | Description |
| --- | --- |
| status |Used for metric alerts. Always set to "activated" for Activity Log alerts. |
| context |Context of the event. |
| resourceProviderName |The resource provider of the impacted resource. |
| conditionType |Always "Event." |
| name |Name of the alert rule. |
| id |Resource ID of the alert. |
| description |Alert description as set during creation of the alert. |
| subscriptionId |Azure Subscription ID. |
| timestamp |Time at which the event was generated by the Azure service that processed the request. |
| resourceId |Resource ID of the impacted resource. |
| resourceGroupName |Name of the resource group for the impacted resource |
| properties |Set of `<Key, Value>` pairs (i.e. `Dictionary<String, String>`) that includes details about the event. |
| event |Element containing metadata about the event. |
| authorization |The RBAC properties of the event. These usually include the “action”, “role” and the “scope.” |
| category |Category of the event. Supported values include: Administrative, Alert, Security, ServiceHealth, Recommendation. |
| caller |Email address of the user who performed the operation, UPN claim, or SPN claim based on availability. Can be null for certain system calls. |
| correlationId |Usually a GUID in string format. Events with correlationId belong to the same larger action and usually share a correlationId. |
| eventDescription |Static text description of the event. |
| eventDataId |Unique identifier for the event. |
| eventSource |Name of the Azure service or infrastructure that generated the event. |
| httpRequest |Usually includes the “clientRequestId”, “clientIpAddress” and “method” (HTTP method e.g. PUT). |
| level |One of the following values: “Critical”, “Error”, “Warning”, “Informational” and “Verbose.” |
| operationId |Usually a GUID shared among the events corresponding to single operation. |
| operationName |Name of the operation. |
| properties |Properties of the event. |
| status |String. Status of the operation. Common values include: "Started", "In Progress", "Succeeded", "Failed", "Active", "Resolved". |
| subStatus |Usually includes the HTTP status code of the corresponding REST call. It might also include other strings describing a substatus. Common substatus values include: OK (HTTP Status Code: 200), Created (HTTP Status Code: 201), Accepted (HTTP Status Code: 202), No Content (HTTP Status Code: 204), Bad Request (HTTP Status Code: 400), Not Found (HTTP Status Code: 404), Conflict (HTTP Status Code: 409), Internal Server Error (HTTP Status Code: 500), Service Unavailable (HTTP Status Code: 503), Gateway Timeout (HTTP Status Code: 504) |

## Next steps
* [Learn more about the Activity Log](monitoring-overview-activity-logs.md)
* [Execute Azure Automation scripts (Runbooks) on Azure alerts](http://go.microsoft.com/fwlink/?LinkId=627081)
* [Use Logic App to send an SMS via Twilio from an Azure alert](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). This example is for metric alerts, but could be modified to work with an Activity Log alert.
* [Use Logic App to send a Slack message from an Azure alert](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). This example is for metric alerts, but could be modified to work with an Activity Log alert.
* [Use Logic App to send a message to an Azure Queue from an Azure alert](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). This example is for metric alerts, but could be modified to work with an Activity Log alert.
