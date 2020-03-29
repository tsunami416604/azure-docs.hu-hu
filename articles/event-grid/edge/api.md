---
title: REST API – Azure Event Grid IoT Edge | Microsoft dokumentumok
description: REST API az Event Grid en IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 19f86b1d8233e05844201e1095c1f79324955cd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841829"
---
# <a name="rest-api"></a>REST API
Ez a cikk az Azure Event Grid REST API-jait ismerteti az IoT Edge-en

## <a name="common-api-behavior"></a>Az API gyakori viselkedése

### <a name="base-url"></a>Kiindulási URL-cím
Az IoT Edge Eseményrács a következő API-kat (5888-as port) és HTTPS-kapcsolaton keresztül teszi elérhetővé (4438-as port).

* HTTP alap URL-címe:http://eventgridmodule:5888
* A HTTPS alap URL-címe:https://eventgridmodule:4438

### <a name="request-query-string"></a>Lekérdezési karakterlánc kérése
Minden API-kérelemhez a következő lekérdezési karakterlánc-paraméter szükséges:

```?api-version=2019-01-01-preview```

### <a name="request-content-type"></a>Tartalomtípus kérése
Minden API-kérelemnek **tartalomtípussal kell rendelkeznie.**

**EventGridSchema** vagy **CustomSchema**esetén a Content-Type értéke a következő értékek egyike lehet:

```Content-Type: application/json```

```Content-Type: application/json; charset=utf-8```

Strukturált módban **CloudEventSchemaV1_0** esetén a Content-Type értéke a következő értékek egyike lehet:

```Content-Type: application/cloudevents+json```
    
```Content-Type: application/cloudevents+json; charset=utf-8```
    
```Content-Type: application/cloudevents-batch+json```
    
```Content-Type: application/cloudevents-batch+json; charset=utf-8```

Ha **CloudEventSchemaV1_0** bináris módban van, a részleteket a [dokumentációban](https://github.com/cloudevents/spec/blob/master/http-protocol-binding.md) találja.

### <a name="error-response"></a>Hibaválasz
Minden API a következő hasznos adattal ad vissza hibát:

```json
{
    "error":
    {
        "code": "<HTTP STATUS CODE>",
        "details": 
        {
            "code": "<Detailed Error Code>",
            "message": "..."
        }
    }
}
```

## <a name="manage-topics"></a>Témakörök kezelése

### <a name="put-topic-create--update"></a>Tedd téma (create / update)

**Kérelem**:``` PUT /topics/<topic_name>?api-version=2019-01-01-preview ```

**Hasznos teher**:

```json
    {
        "name": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "properties":
        {
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // optional
        }
    }
```

**Válasz**: HTTP 200

**Hasznos teher**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<get_request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // populated with EventGridSchema if not explicitly specified in PUT request
    }
}
```

### <a name="get-topic"></a>Témakör beszereznie

**Kérelem**:``` GET /topics/<topic_name>?api-version=2019-01-01-preview ```

**Válasz**: HTTP 200

**Hasznos teher**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
    }
}
```

### <a name="get-all-topics"></a>Az összes témakör bekerülése

**Kérelem**:``` GET /topics?api-version=2019-01-01-preview ```

**Válasz**: HTTP 200

**Hasznos teher**:
```json
[
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    },
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    }
]
```

### <a name="delete-topic"></a>Témakör törlése

**Kérelem**:``` DELETE /topics/<topic_name>?api-version=2019-01-01-preview ```

**Válasz**: HTTP 200, üres hasznos teher

## <a name="manage-event-subscriptions"></a>Esemény-előfizetések kezelése
Az ebben a `EndpointType=Webhook;`szakaszban található minták a . A json `EndpointType=EdgeHub / EndpointType=EventGrid` minták a következő részben vannak. 

### <a name="put-event-subscription-create--update"></a>Esemény-előfizetés (létrehozás/frissítés)

**Kérelem**:``` PUT /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Hasznos teher**:
```json
{
    "name": "<subscription_name>", // optional, inferred from URL. If specified must match URL subscription_name
    "properties":
    {
        "topicName": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0", // optional
        "retryPolicy":  //optional
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "persistencePolicy": "true",
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // optional
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

**Válasz**: HTTP 200

**Hasznos teher**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```


### <a name="get-event-subscription"></a>Esemény-előfizetés beszerezni

**Kérelem**:``` GET /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Válasz**: HTTP 200

**Hasznos teher**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

### <a name="get-event-subscriptions"></a>Esemény-előfizetések beszereznie

**Kérelem**:``` GET /topics/<topic_name>/eventSubscriptions?api-version=2019-01-01-preview ```

**Válasz**: HTTP 200

**Hasznos teher**:
```json
[
    {
        // same event-subscription json as that returned from Get-EventSubscription above
    },
    {
    },
    ...
]
```

### <a name="delete-event-subscription"></a>Esemény-előfizetés törlése

**Kérelem**:``` DELETE /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Válasz**: HTTP 200, nincs hasznos teher


## <a name="publish-events-api"></a>Események közzététele API

### <a name="send-batch-of-events-in-event-grid-schema"></a>Események kötegének küldése (az Event Grid sémájában)

**Kérelem**:``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        "id": "<user-defined-event-id>",
        "topic": "<topic_name>",
        "subject": "",
        "eventType": "",
        "eventTime": ""
        "dataVersion": "",
        "metadataVersion": "1",
        "data": 
            ...
    }
]
```

**Válasz**: HTTP 200, üres hasznos teher


**Hasznos mező leírásai**
- ```Id```kötelező. Ez bármilyen karakterlánc-érték lehet, amelyet a hívó feltölt. Az Event Grid NEM végez duplikáltelem-észlelést, és nem kényszeríti ki a szemantikát ezen a mezőn.
- ```Topic```nem kötelező, de ha meg van adva, meg kell egyeznie a kérelem URL-címéről származó topic_name
- ```Subject```kötelező, bármilyen karakterlánc-érték lehet
- ```EventType```kötelező, bármilyen karakterlánc-érték lehet
- ```EventTime```kötelező, nem érvényesítve, de megfelelő DateTime-nak kell lennie.
- ```DataVersion```kötelező
- ```MetadataVersion```nem kötelező, ha meg van adva, akkor egy karakterláncnak kell lennie az értékkel```"1"```
- ```Data```nem kötelező, és bármilyen JSON token lehet (szám, karakterlánc, logikai, tömb, objektum)

### <a name="send-batch-of-events-in-custom-schema"></a>Események kötegének küldése (egyéni sémában)

**Kérelem**:``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        ...
    }
]
```

**Válasz**: HTTP 200, üres hasznos teher


**Hasznos teher korlátozásai**
- Kell egy sor események.
- Minden tömbbejegyzésnek JSON-objektumnak kell lennie.
- Nincs más megkötések (kivéve a hasznos adat méretét).

## <a name="examples"></a>Példák

### <a name="set-up-topic-with-eventgrid-schema"></a>Témakör beállítása az EventGrid sémával
Úgy állít be egy témakört, hogy az eseményeket közzé kell tenni az **eventgridschema-ban.**

```json
    {
        "name": "myeventgridtopic",
        "properties":
        {
            "inputSchema": "EventGridSchema"
        }
    }
```

### <a name="set-up-topic-with-custom-schema"></a>Témakör beállítása egyéni sémával
Úgy állít be egy témakört, `customschema`hogy az eseményeket közzé kell tenni a-ban.

```json
    {
        "name": "mycustomschematopic",
        "properties":
        {
            "inputSchema": "CustomSchema"
        }
    }
```

### <a name="set-up-topic-with-cloud-event-schema"></a>Témakör beállítása felhőalapú eseménysémával
Úgy állít be egy témakört, `cloudeventschema`hogy az eseményeket közzé kell tenni a-ban.

```json
    {
        "name": "mycloudeventschematopic",
        "properties":
        {
            "inputSchema": "CloudEventSchemaV1_0"
        }
    }
```

### <a name="set-up-webhook-as-destination-events-to-be-delivered-in-eventgridschema"></a>WebHook beállítása célként, eseményrácschemában kézbesítendő események
Ezzel a céltípussal eseményeket küldhet bármely más modulnak (amely HTTP-végpontot üzemeltet) vagy a hálózat/internet bármely HTTP-címezhető végpontjára.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "eventDeliverySchema": "eventgridschema",
            }
        }
    }
}
```

Az `endpointUrl` attribútum korlátai:
- Nem lehet null értékű.
- Abszolút URL-nek kell lennie.
- Ha outbound__webhook__httpsOnly értéke igaz az EventGridModule beállításokban, akkor csak HTTPS-nek kell lennie.
- Ha outbound__webhook__httpsOnly értéke hamis, akkor lehet HTTP vagy HTTPS.

Az ingatlanra vonatkozó `eventDeliverySchema` korlátozások:
- Meg kell egyeznie az előfizetési témakör bemeneti sémájával.
- Lehet null. Alapértelmezés szerint a témakör bemeneti sémája.

### <a name="set-up-iot-edge-as-destination"></a>Az IoT Edge beállítása célként

Ezzel a célponttal eseményeket küldhet az IoT Edge Hubba, és peremhálózati hub útválasztási/szűrési/továbbítási alrendszerének kell alávetni.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EdgeHub",
            "properties":
            {
                "outputName": "<eventgridmodule_output_port_name>"
            }
        }
    }
}
```

### <a name="set-up-event-grid-cloud-as-destination"></a>Az Event Grid Cloud beállítása úti célként

Ezzel a célállomásra eseményeket küldhet az Event Gridbe a felhőben (Azure). Először be kell állítania egy felhasználói témakört a felhőben, amelybe az eseményeket el kell küldeni, mielőtt esemény-előfizetést hozna létre a peremhálózaton.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EventGrid",
            "properties":
            {
                "endpointUrl": "<eventgrid_user_topic_url>",
                "sasKey": "<user_topic_sas_key>",
                "topicName": "<new value to populate in forwarded EventGridEvent.Topic>" // if not specified, the Topic field on every event gets nulled out before being sent to Azure Event Grid
            }
        }
    }
}
```

EndpointUrl:
- Nem lehet null értékű.
- Abszolút URL-nek kell lennie.
- Az `/api/events` elérési utat a kérelem URL-elérési útján kell definiálni.
- A lekérdezési karakterláncban kell lennie. `api-version=2018-01-01`
- Ha outbound__eventgrid__httpsOnly értéke igaz az EventGridModule beállításokban (alapértelmezés szerint igaz), akkor csak HTTPS-nek kell lennie.
- Ha outbound__eventgrid__httpsOnly értéke hamis, akkor lehet HTTP vagy HTTPS.
- Ha outbound__eventgrid__allowInvalidHostnames értéke hamis (alapértelmezés szerint hamis), akkor a következő végpontok egyikét kell megcéloznia:
   - `eventgrid.azure.net`
   - `eventgrid.azure.us`
   - `eventgrid.azure.cn`

SasKey:
- Nem null értékűnek kell lennie.

Témakör neve:
- Ha a Subscription.EventDeliverySchema értéke EventGridSchema, az ebből a mezőből származó érték minden esemény Témakör mezőjébe kerül, mielőtt továbbítanák az Event Grid beépülanak a felhőben.
- Ha a Subscription.EventDeliverySchema beállítása CustomEventSchema, ez a tulajdonság figyelmen kívül hagyja, és az egyéni esemény hasznos adatát továbbítja pontosan úgy, ahogy az érkezett.

## <a name="set-up-event-hubs-as-a-destination"></a>Az Eseményközpontok beállítása úti célként

Ha közzé szeretne tenni egy `endpointType` `eventHub` Eseményközpontban, állítsa be a következőt, és adja meg:

* connectionString: A megosztott hozzáférési házirend által létrehozott adott eseményközpont kapcsolati karakterlánca.

    >[!NOTE]
    > A kapcsolati karakterláncnak entitásspecifikusnak kell lennie. A névtér kapcsolati karakterláncának használata nem fog működni. Létrehozhat egy entitásspecifikus kapcsolati karakterláncot, ha az Azure Portalon közzétenni kívánt eseményközpontra navigál, és a **megosztott hozzáférési szabályzatok** elemre kattint va, hogy új entitásspecifikus connecection-karakterláncot hozzon létre.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-queues-as-a-destination"></a>Szolgáltatásbusz-várólisták beállítása célként

Ha közzé szeretne tenni egy `endpointType` szolgáltatásbusz-várólistában, állítsa be a következőt, `serviceBusQueue` és adja meg:

* connectionString: A megosztott hozzáférési házirend en keresztül létrehozott adott service bus-várólistához tartozó kapcsolati karakterlánc.

    >[!NOTE]
    > A kapcsolati karakterláncnak entitásspecifikusnak kell lennie. A névtér kapcsolati karakterláncának használata nem fog működni. Hozzon létre egy entitásspecifikus kapcsolati karakterláncot az Azure Portalon közzétenni kívánt adott Service Bus-várólistára való navigálással, és kattintson a **Megosztott hozzáférési szabályzatok** elemre egy új entitásspecifikus connecection karakterlánc létrehozásához.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-topics-as-a-destination"></a>Szolgáltatásbusz-témakörök beállítása úti célként

A Service Bus-témakörben való `endpointType` `serviceBusTopic` közzétételhez állítsa be a következőt, és adja meg:

* connectionString: Kapcsolati karakterlánc a megosztott hozzáférési szabályzat on keresztül létrehozott adott Service Bus-témakörhöz.

    >[!NOTE]
    > A kapcsolati karakterláncnak entitásspecifikusnak kell lennie. A névtér kapcsolati karakterláncának használata nem fog működni. Hozzon létre egy entitásspecifikus kapcsolati karakterláncot az Azure Portalon közzétenni kívánt adott Service Bus-témakörre való navigálással, és kattintson a **megosztott hozzáférési szabályzatok** elemre egy új entitásspecifikus connecection karakterlánc létrehozásához.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-storage-queues-as-a-destination"></a>A tárolási várólisták beállítása célként

A tárolóvárólistában való közzétételhez állítsa be a `endpointType` következőt, `storageQueue` és adja meg:

* queueName: Annak a tárolóvárólistának a neve, amelynek közzé teszi a közzétételt.
* connectionString: A storage-várólista által tárolt tárfiók kapcsolati karakterlánca.

    >[!NOTE]
    > Unline Event Hubs, Service Bus várólisták, és a Service Bus-témakörök, a tárolási várólisták használt kapcsolati karakterlánc nem entitásspecifikus. Ehelyett meg kell, de a kapcsolati karakterlánc a tárfiók.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```