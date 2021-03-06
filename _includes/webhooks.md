# Webhooks

Webhooks is a way to notify you about new messages or other events in our system.
It's mandatory to use webhooks for 99% integrations with our API.

Endpoint with <code>webhook_url</code> **must** response with http status code `200` if endpoint received webhook.

Webhook could be repeated up to 10 times within 60 sec delay if response status code is not equal 200. After 10 retries webhook will be dropped and nevery delivered.

<aside class="notice">
The configuration of the name display settings does not affect webhooks in any way.
</aside>

## New conversation

> The above command returns JSON structured like this:

```json
{
   "type":"conversation",
   "event":"new",
   "data":{
      "external_id":1,
      "name":"Sender Name",
      "avatar_url": "https://cdn.pact.im/conversations/avatars/000/000/000/original/open-uri00000000-00000-0p0igg0?0000000000",
      "channel_id":1,
      "channel_type":"whatsapp",
      "created_at":"2017-11-11 12:45:53 UTC",
      "created_at_timestamp":1603118584,
      "last_message_at":null,
      "last_income_message_at":null,
      "sender_external_id":"79250000001"
   }
}

```

### When

 - Client sends new message first time.
 - You sent message to new client. Message was delivered, conversation was created.

<aside class="notice">
   <code>sender_external_id</code> attribute is available only for whatsapp and instagram conversations.
</aside>


## Update conversation

> The above command returns JSON structured like this:

```json
{
   "type":"conversation",
   "event":"update",
   "data":{
      "external_id":1,
      "name":"Sender Name",
      "avatar_url": "https://cdn.pact.im/conversations/avatars/000/000/000/original/open-uri00000000-00000-0p0igg0?0000000000",
      "channel_id":1,
      "channel_type":"whatsapp",
      "created_at":"2017-11-11 12:45:53 UTC",
      "created_at_timestamp":1603118584,
      "last_message_at":null,
      "last_income_message_at":null,
      "sender_external_id":"79250000001"
   }
}

```

### When

- When conversation with such an sender_external_id already exists and new data is received the sender_name and sender_avatar_url.

<aside class="notice">
   <code>sender_external_id</code> attribute is available only for whatsapp and instagram conversations.
</aside>

## New message

> The above command returns JSON structured like this:

```json
{
   "type":"message",
   "event":"new",
   "data":{
      "external_id":1,
      "external_public_id":1,
      "channel_id":1,
      "channel_type":"whatsapp",
      "channel":{
        "id": 1,
        "type": "whatsapp"
      },
      "conversation_id":1,
      "message":"Message body",
      "location": {},
      "income":true,
      "created_at":"2017-11-11 12:45:53 UTC",
      "created_at_timestamp":1603118584,
      "ack":1,
      "attachments":[],
      "job_id":1
   }
}

```

### When

 - Client sent new message.
 - You sent message to client.

<aside class="success">
Webhook will be sent ONLY when message received/delivered.
</aside>

<aside class="notice">
<code>job_id</code> attribute is available only in async delivery mode.
</aside>

## Message delivery/read status

> The above command returns JSON structured like this:

```json
{
    "type": "message",
    "event": "ack",
    "data": {
        "external_id": 1,
        "channel": {
            "id": 1,
            "type": "whatsapp"
        },
        "conversation_id": 1,
        "ack": 3
    }
}

```

### When

 - Message was delivered
 - Message was read

Possible statuses:

  - `-1` : Message is in `preparing to deliver` state
  - ` 0` : Message is in `delivering` state
  - ` 1` : Message was sent
  - ` 2` : Message was delivered
  - ` 3` : Message was read

If message in a `-1` state - message may not be delivered. You must to care about message delivering by yourself.

## Delivery job executed

> The above command returns JSON structured like this:

```json
{
   "type":"job",
   "event":"executed",
   "data":{
      "id":1,
      "result":"DELIVERED",
      "message_id":1,
      "date":1510404738
   }
}

```

### When

 - You sent message in async mode. Delivery job was executed and
   delivery result is known.

Possible results:

  - `DELIVERED`
  - `NOT DELIVERED`

If message is `NOT DELIVERED` we append `reason` filed with reason information.

## Whatsapp: new QR-code

> The above command returns JSON structured like this:

```json
{
   "type":"qr_code",
   "event":"new",
   "company_id":1,
   "channel_id":1,
   "data":"BASE64 QR-CODE image string"
}

```

### When

  - You are connecting `whatsapp` channel in your company
  - User logged out in WhatsApp app

## Whatsapp: channel connected sucessfuly

> The above command returns JSON structured like this:

```json
{
   "type":"system",
   "severity":"information",
   "data":{
      "message":"authorized",
      "date":"2017-11-11 12:45:53 UTC",
      "date_timestamp":1603118584,
      "timestamp":1603119138,
      "details":{
         "entity":"channel",
         "entity_id":1
      }
   }
}

```

### When

QR-code was scanned successfuly

<aside class="notice">
<code>date_timestamp</code> is date timestamp;
<code>timestamp</code> is callback timestamp.
</aside>

## Whatsapp: phone offline

> The above command returns JSON structured like this:

```json
{
   "type":"system",
   "severity":"critical",
   "data":{
      "message":"phone offline",
      "date":"2017-11-11 12:45:53 UTC",
      "date_timestamp":1603118584,
      "timestamp":1603119138,
      "details":{
         "entity":"channel",
         "entity_id":1
      }
   }
}

```

### When

Device with WhatsApp application is unreachable. We can't work with whatsapp while phone is offline.

<aside class="notice">
<code>date_timestamp</code> is date timestamp;
<code>timestamp</code> is callback timestamp.
</aside>

## Whatsapp: phone online

> The above command returns JSON structured like this:

```json
{
   "type":"system",
   "severity":"critical",
   "data":{
      "message":"phone online",
      "date":"2017-11-11 12:45:53 UTC",
      "date_timestamp":1603118584,
      "timestamp":1603119138,
      "details":{
         "entity":"channel",
         "entity_id":1
      }
   }
}

```

### When

Device with WhatsApp application is reachable again.

<aside class="notice">
<code>date_timestamp</code> is date timestamp;
<code>timestamp</code> is callback timestamp.
</aside>

## Whatsapp: channel not available

> The above command returns JSON structured like this:

```json
{
   "type":"system",
   "severity":"critical",
   "data":{
      "message":"unavailable",
      "date":"2017-11-11 12:45:53 UTC",
      "date_timestamp":1603118584,
      "timestamp":1603119138,
      "details":{
         "entity":"channel",
         "entity_id":1
      }
   }
}

```

### When

Someone started session at [web.whatsapp.com](https://web.whatsapp.com) or user similar integration.
We can't work with whatsapp while Whatsapp WEB is open.

<aside class="notice">
<code>date_timestamp</code> is date timestamp;
<code>timestamp</code> is callback timestamp.
</aside>

## Whatsapp: trying to resume channel work

> The above command returns JSON structured like this:

```json
{
   "type":"system",
   "severity":"critical",
   "data":{
      "message":"trying_resume_work",
      "date":"2017-11-11 12:45:53 UTC",
      "date_timestamp":1603118584,
      "timestamp":1603119138,
      "details":{
         "entity":"channel",
         "entity_id":1
      }
   }
}

```

### When

We're trying to resume work after previous conflict state

<aside class="notice">
<code>date_timestamp</code> is date timestamp;
<code>timestamp</code> is callback timestamp.
</aside>

## Whatsapp: synchronization completed

> The above command returns JSON structured like this:

```json
{
   "type":"system",
   "severity":"critical",
   "data":{
      "message":"synchronized",
      "date":"2017-11-11 12:45:53 UTC",
      "date_timestamp":1603118584,
      "timestamp":1603119138,
      "details":{
         "entity":"channel",
         "entity_id":1
      }
   }
}

```

### When

  - Whatsapp channel was connected and syncronisation was completed
  - Syncronisation after work resume

<aside class="notice">
<code>date_timestamp</code> is date timestamp;
<code>timestamp</code> is callback timestamp.
</aside>

## Instagram: changed state to disabled

> The above command returns JSON structured like this:

```json
{
   "type":"system",
   "severity":"information",
   "data":{
      "message":"IG was set DISABLED",
      "date":"2017-11-11 12:45:53 UTC",
      "date_timestamp":1603118584,
      "details":{
         "entity":"channel",
         "entity_id":1
      }
   }
}

```

### When

  - Service message successfully delivered
  - Service message delivery failed

## Service messages: delivery status

> The above command returns JSON structured like this:

```json
{
   "type":"service_messages",
   "event":"update",
   "data":{
      "id": 42,
      "delivered": true,
      "delivered_at":"2018-12-5 15:05:14 UTC",
      "delivered_at_timestamp":1603118584,
      "delivered_via":"whatsapp"
   }
}

```

### When

  - Instagram channel state was changed to `disabled`
