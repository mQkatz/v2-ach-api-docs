# Webhook subscriptions

Create a webhook subscription to receive `POST` requests from Dwolla (called webhooks) when events associated with your application occur.  [Webhooks](#webhooks) are sent to a URL which you provide when creating a webhook subscription. If you are a white label partner, you will use these events to notify your customers via email as described in the white label terms of service. Refer to the [events](#available-events) section for the list of events that trigger webhooks.

```noselect
{
  "_links": {
    "self": {
      "href": "https://api.dwolla.com/webhook-subscriptions/034a0ae2-fc7f-4922-8e4e-6fb9855ed43b"
    },
    "webhooks": {
      "href": "https://api.dwolla.com/webhook-subscriptions/034a0ae2-fc7f-4922-8e4e-6fb9855ed43b/webhooks"
    }
  },
  "id": "034a0ae2-fc7f-4922-8e4e-6fb9855ed43b",
  "url": "http://requestb.in/vbxb1bvb",
  "created": "2015-10-06T01:11:36.000Z"
}
```

### Acknowledgement and retries
When your application receives a [webhook](#webhooks), it should respond with a HTTP 2xx status code to indicate successful receipt. If Dwolla receives a status code greater than a HTTP 400, or your application fails to respond within 20 seconds of the attempt, another attempt will be made.

Dwolla will re-attempt delivery 8 times over the course of 72 hours according the backoff schedule below. If a webhook was successfully received but you would like the information again, you can call [retrieve webhook by ID](#get-webhook-by-id).

| Retry number | Interval (relative to last retry) | Interval (relative to original attempt) |
|:------------:|:---------------------------------:|:---------------------------------------:|
|       1      |              15 min               |                  15 min                 |
|       2      |              45 min               |                   1 h                   |
|       3      |               2 h                 |                   3 h                   |
|       4      |               3 h                 |                   6 h                   |
|       5      |               6 h                 |                  12 h                   |
|       6      |              12 h                 |                  24 h                   |
|       7      |              24 h                 |                  48 h                   |
|       8      |              24 h                 |                  72 h                   |

### Webhook resource

| Parameter      | Description                                       |
|----------------|---------------------------------------------------|
| id             | Webhook unique identifier                         |
| topic          | Type of webhook subscription                      |
| accountId      | Account associated with the webhook notification  |
| eventId        | Event id for this webhook                         |
| subscriptionId | Webhook subscription id for this event            |
| attempts       | Array of attempt JSON object                      |

### Attempt JSON object

| Parameter      | Description                             |
|----------------|-----------------------------------------|
| id             | Unique id of webhook delivery attempt.  |
| request        | Request JSON object                     |
| response       | Response JSON object                    |

### Request/response JSON object

| Parameter      | Description                                                                   |
|----------------|-------------------------------------------------------------------------------|
| created        | ISO-8601 timestamp                                                            |
| url            | URL where data was sent to/received from                                      |
| headers        | Array of objects with keys `name` and `value` representative of HTTP headers  |
| body           | Event id for this webhook                                                     |

## Create a webhook subscription

This section details how to create a webhook subscription to deliver [webhooks](#webhooks) to a specified URL.

<ol class="alerts">
    <li class="alert icon-alert-alert">This endpoint <a href="#authentication">requires</a> an OAuth *Application* access token.</li>
</ol>

### HTTP Request
`POST https://api.dwolla.com/webhook-subscriptions`

### Request parameters
| Parameter | Description |
|----------|--------------|
| url | Where Dwolla should deliver the webhook notification. |
| secret | A random, secret key, only known by your application. This secret key should be securely stored and used later when [validating the authenticity](https://developers.dwolla.com/guides/webhooks/03-validating-webhooks.html) of the webhook request from Dwolla. |

### Request and response

```raw
POST https://api-uat.dwolla.com/webhook-subscriptions
Accept: application/vnd.dwolla.v1.hal+json
Content-Type: application/vnd.dwolla.v1.hal+json
Authorization: Bearer 0Sn0W6kzNicvoWhDbQcVSKLRUpGjIdlPSEYyrHqrDDoRnQwE7Q
{
    "url": "http://myapplication.com/webhooks",
    "secret": "sshhhhhh"
}
```
```ruby
subscription = DwollaSwagger::WebhooksubscriptionsApi.create({:body => {
  :url => "http://myawesomeapplication.com/destination",
  :secret => "your webhook secret"
}})

p subscription # => https://api-uat.dwolla.com/webhook-subscriptions/5af4c10a-f6de-4ac8-840d-42cb65454216
```
```javascript
dwolla.then(function(dwolla) {
    dwolla['webhook-subscriptions'].list({
        "url": "http://myapplication.com/webhooks",
        "secret": "sshhhhhh"
    }).then(function(data) {
        console.log(data.obj); // https://api-uat.dwolla.com/webhook-subscriptions/5af4c10a-f6de-4ac8-840d-42cb65454216
    });
});
```
```python
webhook_api = dwollaswagger.WebhooksubscriptionsApi(client)
subscription = webhook_api.create({
    "url": "http://myapplication.com/webhooks",
    "secret": "sshhhhhh"
})

print(subscription) # => https://api-uat.dwolla.com/webhook-subscriptions/5af4c10a-f6de-4ac8-840d-42cb65454216
```
```php
<?php
$webhookApi = new DwollaSwagger\WebhooksubscriptionsApi($apiClient);
$subscription = $webhookApi->create(array (
  'url' => 'http://myapplication.com/webhooks',
  'secret' => 'sshhhhhh',
));

print($subscription); # => https://api-uat.dwolla.com/webhook-subscriptions/5af4c10a-f6de-4ac8-840d-42cb65454216
?>
```

## Delete a subscription

Delete a Webhook Subscription to stop receiving Webhooks at the URL specified. If using an SDK, the request was successful unless an exception was thrown stating otherwise.

<ol class="alerts">
    <li class="alert icon-alert-alert">This endpoint <a href="#authentication">requires</a> an OAuth *Application* access token.</li>
</ol>

### HTTP request
`DELETE https://api.dwolla.com/webhook-subscriptions/{id}`

### Request parameters
| Parameter | Description |
|----------|--------------|
| id | Webhook unique identifier. |

### Errors
| HTTP Status | Message |
|--------------|-------------|
| 404 | Webhook subscription not found. |

### Request and response

```raw
DELETE https://api-uat.dwolla.com/webhook-subscriptions/5af4c10a-f6de-4ac8-840d-42cb65454216
Accept: application/vnd.dwolla.v1.hal+json
Authorization: Bearer pBA9fVDBEyYZCEsLf/wKehyh1RTpzjUj5KzIRfDi0wKTii7DqY
```
```ruby
deleted = DwollaSwagger::WebhooksubscriptionApi.id('https://api-uat.dwolla.com/webhook-subscriptions/5af4c10a-f6de-4ac8-840d-42cb65454216')
```
```javascript
dwolla.then(function(dwolla) {
    dwolla['webhook-subscriptions'].deleteById({
      id: '5af4c10a-f6de-4ac8-840d-42cb65454216'
    }).then(function(data) {
        console.log(data.status); // 200
    });
});
```
```python
webhook_api = dwollaswagger.WebhooksubscriptionsApi(client)
deleted = webhook_api.id('https://api-uat.dwolla.com/webhook-subscriptions/5af4c10a-f6de-4ac8-840d-42cb65454216')
```
```php
<?php
$webhookApi = new DwollaSwagger\WebhooksubscriptionsApi($apiClient);
$deleted = $webhookApi->id('https://api-uat.dwolla.com/webhook-subscriptions/5af4c10a-f6de-4ac8-840d-42cb65454216');
?>
```

## List subscriptions

This section covers how to retrieve a list of webhook subscriptions that belong to an application.

<ol class="alerts">
    <li class="alert icon-alert-alert">This endpoint <a href="#authentication">requires</a> an OAuth *Application* access token.</li>
</ol>

### HTTP request
`GET https://api.dwolla.com/webhook-subscriptions`

### Request and response

```raw
GET https://api.dwolla.com/webhook-subscriptions
Accept: application/vnd.dwolla.v1.hal+json
Authorization: Bearer pBA9fVDBEyYZCEsLf/wKehyh1RTpzjUj5KzIRfDi0wKTii7DqY

...

{
  "_links": {
    "self": {
      "href": "https://api.dwolla.com/webhook-subscriptions"
    }
  },
  "_embedded": {
    "webhook-subscriptions": [
      {
        "_links": {
          "self": {
            "href": "https://api.dwolla.com/webhook-subscriptions/f4d21628-fde2-4d3a-b69a-0a7cb42adc4c"
          },
          "webhooks": {
            "href": "https://api.dwolla.com/webhook-subscriptions/f4d21628-fde2-4d3a-b69a-0a7cb42adc4c/webhooks"
          }
        },
        "id": "f4d21628-fde2-4d3a-b69a-0a7cb42adc4c",
        "url": "https://destination.url",
        "created": "2015-08-19T21:43:49.000Z"
      }
    ]
  },
  "total": 1
}
```
```ruby
retrieved = DwollaSwagger::WebhooksubscriptionApi.list

p retrieved.total # => 1
```
```javascript
dwolla.then(function(dwolla) {
    dwolla['webhook-subscriptions'].list()
    .then(function(data) {
        console.log(data.obj.total); // 1
    });
});
```
```python
webhook_api = dwollaswagger.WebhooksubscriptionsApi(client)
retrieved = webhook_api.list()

print(retrieved.total) # => 1
```
```php
<?php
$webhookApi = new DwollaSwagger\WebhooksubscriptionsApi($apiClient);
$retrieved = $webhookApi->_list();

print($retrieved->total); # => 1
?>
```

## Get a subscription by id

This section details how to retrieve a webhook subscription by its id.

<ol class="alerts">
    <li class="alert icon-alert-alert">This endpoint <a href="#authentication">requires</a> an OAuth *Application* access token.</li>
</ol>

### HTTP request
`GET https://api.dwolla.com/webhook-subscriptions/{id}`

### Errors
| HTTP Status | Message |
|--------------|-------------|
| 404 | Webhook subscription not found. |

### Request and response:

```raw
GET https://api-uat.dwolla.com/webhook-subscriptions/5af4c10a-f6de-4ac8-840d-42cb65454216
Accept: application/vnd.dwolla.v1.hal+json
Authorization: Bearer pBA9fVDBEyYZCEsLf/wKehyh1RTpzjUj5KzIRfDi0wKTii7DqY

...

{
  "_links": {
    "self": {
      "href": "https://api-uat.dwolla.com/webhook-subscriptions/077dfffb-4852-412f-96b6-0fe668066589"
    },
    "webhooks": {
      "href": "https://api-uat.dwolla.com/webhook-subscriptions/077dfffb-4852-412f-96b6-0fe668066589/webhooks"
    }
  },
  "id": "077dfffb-4852-412f-96b6-0fe668066589",
  "url": "http://myapplication.com/webhooks",
  "created": "2015-10-28T16:20:47+00:00"
}
```
```ruby
retrieved = DwollaSwagger::WebhooksubscriptionApi.id('https://api-uat.dwolla.com/webhook-subscriptions/5af4c10a-f6de-4ac8-840d-42cb65454216')

p retrieved.created # => 2015-10-28T16:20:47+00:00
```
```javascript
dwolla.then(function(dwolla) {
    dwolla['webhook-subscriptions'].id({
      id: '5af4c10a-f6de-4ac8-840d-42cb65454216'
    }).then(function(data) {
        console.log(data.obj.created); // 2015-10-28T16:20:47+00:00
    });
});
```
```python
webhook_api = dwollaswagger.WebhooksubscriptionsApi(client)
retrieved = webhook_api.id('https://api-uat.dwolla.com/webhook-subscriptions/5af4c10a-f6de-4ac8-840d-42cb65454216')

print(retrieved.created) # => 2015-10-28T16:20:47+00:00
```
```php
<?php
$webhookApi = new DwollaSwagger\WebhooksubscriptionsApi($apiClient);
$retrieved = $webhookApi->id('https://api-uat.dwolla.com/webhook-subscriptions/5af4c10a-f6de-4ac8-840d-42cb65454216');

print($retrieved); # => 2015-10-28T16:20:47+00:00
?>
```

## Get a subscription's webhooks

This section covers how to view all fired [webhooks](#webhooks) for a webhook subscription.

<ol class="alerts">
    <li class="alert icon-alert-alert">This endpoint <a href="#authentication">requires</a> an OAuth *Application* access token.</li>
</ol>

### HTTP request
`GET https://api.dwolla.com/webhook-subscriptions/{id}/webhooks`

### Request and response

```raw
GET /webhook-subscriptions/10d4133e-b308-4646-b276-40d9d36def1c/webhooks
Accept: application/vnd.dwolla.v1.hal+json
Authorization: Bearer pBA9fVDBEyYZCEsLf/wKehyh1RTpzjUj5KzIRfDi0wKTii7DqY

...

{
  "_links": {},
  "total": 0,
  "items": [
    {
      "_links": {},
      "id": "string",
      "topic": "string",
      "accountId": "string",
      "eventId": "string",
      "subscriptionId": "string",
      "attempts": [
        {
          "id": "string",
          "request": {
            "created": "2015-07-23T14:19:37.006Z",
            "url": "string",
            "headers": [
              {
                "name": "string",
                "value": "string"
              }
            ],
            "body": "string"
          },
          "response": {
            "created": "2015-07-23T14:19:37.006Z",
            "headers": [
              {
                "name": "string",
                "value": "string"
              }
            ],
            "statusCode": 0,
            "body": "string"
          }
        }
      ]
    }
  ]
}
```
```ruby
retrieved = DwollaSwagger::WebhooksApi.hooks_by_id('https://api-uat.dwolla.com/webhook-subscriptions/5af4c10a-f6de-4ac8-840d-42cb65454216')

p retrieved.total # => 5
```
```javascript
dwolla.then(function(dwolla) {
    dwolla.webhooks.hooksById({
      id: '5af4c10a-f6de-4ac8-840d-42cb65454216'
    }).then(function(data) {
        console.log(data.obj.total); // 5
    });
});
```
```python
webhook_api = dwollaswagger.WebhooksApi(client)
retrieved = webhook_api.hooks_by_id('https://api-uat.dwolla.com/webhook-subscriptions/5af4c10a-f6de-4ac8-840d-42cb65454216')

print(retrieved.total) # => 5
```
```php
<?php
$webhookApi = new DwollaSwagger\WebhooksApi($apiClient);
$retrieved = $webhookApi->hooksById('https://api-uat.dwolla.com/webhook-subscriptions/5af4c10a-f6de-4ac8-840d-42cb65454216');

print($retrieved->total); # => 5
?>
```