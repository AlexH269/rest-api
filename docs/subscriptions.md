[TOC]

## Subscriptions

Provides an entry point for subscriptions (Tango Controls event system)

| URL                                        | Response           | Desc
|-----------------------------------------|------------|--------------------------
|`POST /tango/subscriptions`             | JSONObject | – creates a new subscription  

**Create a new subscription**

`POST /tango/subscriptions`


```json
{
  "id": 0,
  "events": [],
  "failures": []
}
```

**Create a new subscription with events**

```
POST /tango/subscriptions

[
  {
     "host":"hzgxenvtest:10000",
     "device":"sys/tg_test/1",
     "attribute":"double_scalar",
     "type":"change"
   }
]
```


```json
{
  "id": 0,
  "events": [
    {
      "id": 1,
      "target": {
        "host": "hzgxenvtest:10000",
        "device": "sys/tg_test/1",
        "attribute": "double_scalar",
        "type": "change"
      }
    }
  ],
  "failures": []
}
```

### Subscription

Represents single subscription

| URL                                        | Response           | Desc
|-----------------------------------------|------------|--------------------------
|`GET /tango/subscriptions/{id}`              | JSONObject  | – this subscription as JSON 
|`PUT /tango/subscriptions/{id}`              | JSONArray   | – an array with successfully subscribed events 
|`GET /tango/subscriptions/{id}/event-stream` | text/event-stream  | – events stream
|`DELETE /tango/subscriptions/{id}`           | NULL  | – closes events stream and cancels subscription

**Get subscription**

`GET /tango/subscriptions/0`

```json
{
  "id": 0,
  "events": [
    {
      "id": 1,
      "target": {
        "host": "hzgxenvtest:10000",
        "device": "sys/tg_test/1",
        "attribute": "double_scalar",
        "type": "change"
      }
    }
  ],
  "failures": []
}
```

**failures** array field contains subscription failures

**Add new event to subscription**

`PUT /tango/subscriptions/0`

```json
[
  {
    "host":"hzgxenvtest:10000",
    "device":"sys/tg_test/1",
    "attribute":"long_scalar",
    "type":"periodic"
  }
]
```

```json
[
  {
    "id": 1,
    "host":"hzgxenvtest:10000",
    "device":"sys/tg_test/1",
    "attribute":"long_scalar",
    "type":"periodic"
  }
]
```

#### fallback_to_polling

Implementation may provide *fallback_to_polling* configuration option via maintenance interface. If set to true implementation MUST perform client polling in case Tango event subscription has failed. Polling rate MAY be exported as a configuration option.  

### Event stream

Implementation MUST multiplex all events into a single subscription stream:

```
id: <upstream event time>
event: <event id> 
data: <upstream event data as json text e.g. 3.14>
```

In case of an upstream error:

```
id: <upstream event time or server time when error has occured>
event: <event id> 
data: error: <upstream error cause>
```

In case of an server error:

```
id: <server time when error has occured>
event: error 
data: <server error cause>
```


## Implementations notes

1. Implementation MUST broadcast events from a single upstream
2. Implementation MUST un-subscribe when there is no client for a particular upstream
3. Implementation MAY export reconnection timeout to the configuration
4. Implementation MAY separate subscriptions by client/app/user
5. Implementation MAY NOT implement errors broadcasting as client MUST assume that error event occurs only due to connection lose

## Client samples

Client code samples can be found following [this link](https://github.com/tango-controls/rest-api/tree/master/docs/samples)

## References

[1] [Server sent events overview](https://www.w3schools.com/html/html5_serversentevents.asp)

[2] [Server sent events overview from streamdata.io](https://streamdata.io/blog/server-sent-events/)

[3] [Server sent events@MDN](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events/Using_server-sent_events)
