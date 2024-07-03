# Webhooks

There are currently 3 types of webhook events that will be sent if you have a webhook url set

## Ingestion

This event will be triggered when a model is finished ingestion. The returned data will look like the following json.

```json
// Analysis data
{
  "event": "ingestion",
  "details": "Completed model ingestion",
  "model": "hash",
  "customUID": "A custom UID you can set on upload",
  "labels": "{
    \"labels\": [] #A list of labels and their corresponding confidence scores 
    \"moderation_labels\": [] #A list of explicit labels and their corresponding confidence scores 
  }"
}
```
```json
// Successful upload data (upload, validation, etc)
{
  "event": "ingestion",
  "details": "Model validation",
  "model": "hash",
  "customUID": "A custom UID you can set on upload",
  "result": "successful/unsuccessful" // Will be one or the other
}
```
  

## Compare

This event will be triggered when two models are finished being compared. The data returned will look like the following json.

```json
{
  "event": "compare",
  "details": "Details about the event",
  "model_1": "Model 1's hash",
  "model_1_customUID": "A custom UID you can set on upload",
  "model_2": "Model 2's hash",
  "model_2_customUID": "A custom UID you can set on upload",
  "results": 1.0 //The similarity percentage as a float
}
```

## Moderation

This event will be triggered when a models moderation status gets changed. The data returned will look like the following json.

```json
// Data set by our internal system
{
  "event": "moderation",
  "details": "Initial moderation data",
  "model": "hash",
  "customUID": "A custom UID you can set on upload",
  "status": "Moderation status set by system",
  "priority": "Priority if not automatically approved",
}
```
```json
// Event triggered when a user manually updates the status
{
  "event": "moderation",
  "details": "Updated moderation data",
  "model": "hash",
  "customUID": "A custom UID you can set on upload",
  "status": "New status set by user",
  "user": "The user who made the change"
}
```
