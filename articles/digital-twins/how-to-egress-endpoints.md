---
title: Kimenő forgalom és az Azure digitális Twins végpontok |} A Microsoft Docs
description: Végpontok létrehozása az Azure digitális Twins való
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: c94d29f16c011a9ff9951d064d7496d3a87f70ef
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636305"
---
# <a name="egress-and-endpoints"></a>Kimenő forgalom és a végpontok

Az Azure digitális Twins támogatja a fogalmat **végpontok**. Minden végpont a felhasználó Azure-előfizetésben egy üzenet vagy esemény közvetítő jelöli. Események és az üzenetek küldhetők az Azure Event Hubs, az Azure Event Grid és az Azure Service Bus-témaköröket.

Események küldése a végpontok előre meghatározott útválasztási beállítások szerint. A felhasználó úgy adhat meg, melyik végponthoz a következő események bármelyike kell kapnia: 

- TopologyOperation
- UdfCustom
- SensorChange
- SpaceChange
- DeviceMessage

Útválasztás események és eseménytípusok alapvető ismeretekkel, tekintse meg a [útválasztás események és az üzenetek](concepts-events-routing.md).

## <a name="event-types-description"></a>Esemény típus leírása

Az alábbi szakaszok az esemény formátumok minden az eseménytípusok ismerteti.

### <a name="topologyoperation"></a>TopologyOperation

**TopologyOperation** metaadatgráf-módosítások vonatkozik. A **tulajdonos** tulajdonság határozza meg az érintett objektum típusa. A következő típusú objektumok is kiválthatják ezt az eseményt: 

- Eszköz
- DeviceBlobMetadata
- DeviceExtendedProperty
- ExtendedPropertyKey
- ExtendedType
- KeyStore
- Jelentés
- RoleDefinition
- Érzékelő
- SensorBlobMetadata
- SensorExtendedProperty
- Űr
- SpaceBlobMetadata
- SpaceExtendedProperty
- SpaceResource
- SpaceRoleAssignment
- Rendszer
- Felhasználó
- UserBlobMetadata
- UserExtendedProperty

#### <a name="example"></a>Példa

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "subject": "ExtendedPropertyKey",
  "data": {
    "SpacesToNotify": [
      "3a16d146-ca39-49ee-b803-17a18a12ba36"
    ],
    "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
    "AccessType": "Create"
  },
  "eventType": "TopologyOperation",
  "eventTime": "2018-04-17T17:41:54.9400177Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Érték | Csere erre |
| --- | --- |
| YOUR_TOPIC_NAME | Az egyéni témakör neve |

### <a name="udfcustom"></a>UdfCustom

**UdfCustom** olyan felhasználó által definiált függvény (UDF) által küldött események. 
  
> [!IMPORTANT]  
> Ezt az eseményt az UDF magát az explicit módon kell elküldeni.

#### <a name="example"></a>Példa

```JSON
{
  "id": "568fd394-380b-46fa-925a-ebb96f658cce",
  "subject": "UdfCustom",
  "data": {
    "TopologyObjectId": "7c799bfc-1bff-4b9e-b15a-669933969d20",
    "ResourceType": "Space",
    "Payload": "\"Room is not available or air quality is poor\"",
    "CorrelationId": "568fd394-380b-46fa-925a-ebb96f658cce"
  },
  "eventType": "UdfCustom",
  "eventTime": "2018-10-02T06:50:15.198Z",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Érték | Csere erre |
| --- | --- |
| YOUR_TOPIC_NAME | Az egyéni témakör neve |

### <a name="sensorchange"></a>SensorChange

**SensorChange** telemetriai végbement változások alapján egy érzékelő állapot frissítése.

#### <a name="example"></a>Példa

```JSON
{
  "id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
  "subject": "SensorChange",
  "data": {
    "Type": "Classic",
    "DataType": "Motion",
    "Id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
    "Value": "False",
    "PreviousValue": "True",
    "EventTimestamp": "2018-04-17T17:46:15.4964262Z",
    "MessageType": "sensor",
    "Properties": {
      "ms-client-request-id": "c9e576b7-5eea-4f61-8617-92a57add5179",
      "ms-activity-id": "ct22YwXEGJ5u.605.0"
    }
  },
  "eventType": "SensorChange",
  "eventTime": "2018-04-17T17:46:18.5452993Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Érték | Csere erre |
| --- | --- |
| YOUR_TOPIC_NAME | Az egyéni témakör neve |

### <a name="spacechange"></a>SpaceChange

**SpaceChange** telemetriai végbement változások alapján a hely állapotának frissítése.

#### <a name="example"></a>Példa

```JSON
{
  "id": "42522e10-b1aa-42ff-a5e7-7181788ffc4b",
  "subject": "SpaceChange",
  "data": {
    "Type": null,
    "DataType": "AvailableAndFresh",
    "Id": "7c799bfc-1bff-4b9e-b15a-669933969d20",
    "Value": "Room is not available or air quality is poor",
    "PreviousValue": null,
    "RawData": null,
    "transactionId": null,
    "EventTimestamp": null,
    "MessageType": null,
    "Properties": null,
    "CorrelationId": "42522e10-b1aa-42ff-a5e7-7181788ffc4b"
  },
  "eventType": "SpaceChange",
  "eventTime": "2018-10-02T06:50:20.128Z",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Érték | Csere erre |
| --- | --- |
| YOUR_TOPIC_NAME | Az egyéni témakör neve |

### <a name="devicemessage"></a>DeviceMessage

Használatával **DeviceMessage**, megadhat egy **EventHub** , amelyhez nyers telemetria-eseményeinek átirányíthatók, valamint az Azure digitális Twins kapcsolat.

> [!NOTE]
> - **DeviceMessage** csak a kombinálható **EventHub**. Nem lehet kombinálni **DeviceMessage** az bármelyik más esemény típusa.
> - Megadhatja, hogy csak egy végpont típusú kombináció **EventHub** vagy **DeviceMessage**.

## <a name="configure-endpoints"></a>Végpontok konfigurálása

Felügyeleti végpontot a végpontok API-n keresztül történik. Az alábbi példák bemutatják, hogyan lehet a különböző támogatott végpontok konfigurálása. Az esemény típusú tömb külön figyelmet szentelnie szerint határozza meg a végpont útválasztás:

```plaintext
POST https://endpoints-demo.azuresmartspaces.net/management/api/v1.0/endpoints
```

- A Service Bus eseménytípusok útvonal **SensorChange**, **SpaceChange**, és **TopologyOperation**:

  ```JSON
  {
    "type": "ServiceBus",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME"
  }
  ```

    | Érték | Csere erre |
    | --- | --- |
    | YOUR_NAMESPACE | A névtér a végpont |
    | YOUR_PRIMARY_KEY | Hitelesítéshez használt elsődleges kapcsolati karakterlánc |
    | YOUR_SECONDARY_KEY | Hitelesítéshez használt másodlagos kapcsolati karakterlánc |
    | YOUR_TOPIC_NAME | Az egyéni témakör neve |

- Event Grid-esemény típusú útvonal **SensorChange**, **SpaceChange**, és **TopologyOperation**:

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME.westus-1.eventgrid.azure.net"
  }
  ```

    | Érték | Csere erre |
    | --- | --- |
    | YOUR_PRIMARY_KEY | Hitelesítéshez használt elsődleges kapcsolati karakterlánc|
    | YOUR_SECONDARY_KEY | Hitelesítéshez használt másodlagos kapcsolati karakterlánc |
    | YOUR_TOPIC_NAME | Az egyéni témakör neve |

- Az Event Hubs eseménytípusok útvonal **SensorChange**, **SpaceChange**, és **TopologyOperation**:

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | Érték | Csere erre |
    | --- | --- |
    | YOUR_NAMESPACE | A névtér a végpont |
    | YOUR_PRIMARY_KEY | Hitelesítéshez használt elsődleges kapcsolati karakterlánc |
    | YOUR_SECONDARY_KEY | Hitelesítéshez használt másodlagos kapcsolati karakterlánc |
    | YOUR_EVENT_HUB_NAME | Az eseményközpont neve |

- Az Event Hubs esemény típusa útvonal **DeviceMessage**. Felvételét `EntityPath` a a **connectionString** kötelező:

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | Érték | Csere erre |
    | --- | --- |
    | YOUR_NAMESPACE | A névtér a végpont |
    | YOUR_PRIMARY_KEY | Hitelesítéshez használt elsődleges kapcsolati karakterlánc |
    | YOUR_SECONDARY_KEY | Hitelesítéshez használt másodlagos kapcsolati karakterlánc |
    | YOUR_EVENT_HUB_NAME | Az eseményközpont neve |

> [!NOTE]  
> Új végpont a létrehozás után is igénybe vehet legfeljebb 5 10 percre elindításához a fogadott események a végponton.

## <a name="primary-and-secondary-connection-keys"></a>Az elsődleges és másodlagos kapcsolati kulcsok

Ha egy elsődleges kapcsolati kulcs illetéktelen válik, a rendszer automatikusan megpróbálja a másodlagos kapcsolati kulcsot. Amely egy biztonsági mentési, és lehetővé teszi, hogy szabályosan hitelesíteni, és frissítse az elsődleges kulcsot a végpontok API-n keresztül lehetőségét.

Ha mind az elsődleges és másodlagos kapcsolati kulcsok nem engedélyezett, a rendszer beírja egy exponenciális visszatartási várakozási idő legfeljebb 30 perc. Események minden egyes aktivált visszatartási várakozási idő a rendszer elveti.

Amikor a rendszer a egy visszatartási várakozási állapot, a végpontok API-n keresztül frissítési kapcsolatok kulcsok érvénybe léptetéséhez akár 30 percet is igénybe vehet.

## <a name="unreachable-endpoints"></a>A végpontok nem érhető el

A végpont elérhetetlenné válik, amikor a rendszer beírja egy exponenciális visszatartási várakozási idő legfeljebb 30 perc. Események minden egyes aktivált visszatartási várakozási idő a rendszer elveti.

## <a name="next-steps"></a>További lépések

- Ismerje meg, [használata az Azure digitális Twins Swagger](how-to-use-swagger.md).

- Tudjon meg többet [útválasztási események és üzenetek](concepts-events-routing.md) az Azure digitális Twins.
