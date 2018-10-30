---
title: Kimenő forgalom és az Azure digitális Twins végpontok |} A Microsoft Docs
description: Útmutató a végpontok létrehozása az Azure digitális Twins
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: c09ee84cda5f0a9747d3ee1f8f1b37d1323f2cc2
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212250"
---
# <a name="egress-and-endpoints"></a>Kimenő forgalom és a végpontok

Az Azure digitális Twins támogatja a fogalmat _végpontok_ ahol végpontot jelenti-e egy üzenet/eseményközvetítőből a felhasználó Azure-előfizetésében. Események és az üzenetek elküldhetők **Eseményközpont**, **Event Grid**, és **Service Bus-témakörök**.

Eseményeket küld az előre meghatározott útválasztási beállítások szerint végpontok: a felhasználó úgy adhat meg, melyik végponthoz meg kell kapnia a következő események bármelyike: **TopologyOperation**, **UdfCustom**, **SensorChange**, **SpaceChange**, vagy **DeviceMessage**.

Útválasztás események és eseménytípusok alapvető ismeretekkel, tekintse meg a [útválasztás események és az üzenetek](concepts-events-routing.md).

## <a name="event-types-description"></a>Esemény típus leírása

Az egyes az eseménytípusok esemény formátumok a következők:

- **TopologyOperation**

  Metaadatgráf-módosítások vonatkozik. A *tulajdonos* tulajdonság határozza meg az érintett objektum típusa. Az objektumok, amelyek válthatja ki ezt az eseményt típusok a következők: **eszköz**, **DeviceBlobMetadata**, **DeviceExtendedProperty**, **ExtendedPropertyKey**, **ExtendedType**, **KeyStore**, **jelentés**, **RoleDefinition**, **érzékelő**, **SensorBlobMetadata**, **SensorExtendedProperty**, **terület**, **SpaceBlobMetadata**,  **SpaceExtendedProperty**, **SpaceResource**, **SpaceRoleAssignment**, **rendszer**, **felhasználói**, **UserBlobMetadata**, **UserExtendedProperty**.

  Példa:

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
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Egyéni attribútum neve | Cserélje le |
    | --- | --- |
    | *yourTopicName* | Az egyéni témakör neve |

- **UdfCustom**

  Felhasználó által definiált függvény (UDF) által elküldött esemény. 
  
  > [!IMPORTANT]
  > Ez az esemény rendelkezik explicit módon elküldjék az UDF magát.

  Példa:

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
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Egyéni attribútum neve | Cserélje le |
    | --- | --- |
    | *yourTopicName* | Az egyéni témakör neve |

- **SensorChange**

  Egy frissítés egy érzékelő állapotba telemetriai végbement változások alapján.

  Példa:

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
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Egyéni attribútum neve | Cserélje le |
    | --- | --- |
    | *yourTopicName* | Az egyéni témakör neve |

- **SpaceChange**

  Telemetriai végbement változások alapján a hely állapotának frissítése.

  Példa:

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
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Egyéni attribútum neve | Cserélje le |
    | --- | --- |
    | *yourTopicName* | Az egyéni témakör neve |

- **DeviceMessage**

  Lehetővé teszi, hogy adjon meg egy **EventHub** , amelyhez nyers telemetria-eseményeinek átirányíthatók, valamint az Azure digitális Twins kapcsolat.

> [!NOTE]
> - **DeviceMessage** csak a kombinálható **EventHub**; nem lesz képes egyesíteni **DeviceMessage** az bármelyik más esemény típusa.
> - Adja meg a típus együttes használata csak egy végpont lesz **EventHub** vagy **DeviceMessage**.

## <a name="configuring-endpoints"></a>Végpontok konfigurálása

Felügyeleti végpontot a végpontok API-n keresztül történik. Szívesen adunk néhány ötletet a különböző támogatott végpontok beállításáról. Az esemény típusú tömb külön figyelmet szentelnie szerint határozza meg a végpont útválasztás:

```plaintext
POST https://endpoints-demo.azuresmartspaces.net/management/api/v1.0/endpoints
```

- Irányíthatja a **a Service Bus** eseménytípusok: **SensorChange**, **SpaceChange**, **TopologyOperation**

  ```JSON
  {
    "type": "ServiceBus",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey",
    "path": "yourTopicName"
  }
  ```

    | Egyéni attribútum neve | Cserélje le |
    | --- | --- |
    | *yourNamespace* | A névtér a végpont |
    | *yourPrimaryKey* | Hitelesítéshez használt elsődleges kapcsolati karakterlánc |
    | *yourSecondaryKey* | Hitelesítéshez használt másodlagos kapcsolati karakterlánc |
    | *yourTopicName* | Az egyéni témakör neve |

- Irányíthatja a **Event Grid** eseménytípusok: **SensorChange**, **SpaceChange**, **TopologyOperation**

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "yourPrimaryKey",
    "secondaryConnectionString": "yourSecondaryKey",
    "path": "yourTopicName.westus-1.eventgrid.azure.net"
  }
  ```

    | Egyéni attribútum neve | Cserélje le |
    | --- | --- |
    | *yourPrimaryKey* | Hitelesítéshez használt elsődleges kapcsolati karakterlánc|
    | *yourSecondaryKey* | Hitelesítéshez használt másodlagos kapcsolati karakterlánc |
    | *yourTopicName* | Az egyéni témakör neve |

- Irányíthatja a **Eseményközpont** eseménytípusok: **SensorChange**, **SpaceChange**, **TopologyOperation**

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey",
    "path": "yourEventHubName"
  }
  ```

    | Egyéni attribútum neve | Cserélje le |
    | --- | --- |
    | *yourNamespace* | A névtér a végpont |
    | *yourPrimaryKey* | Hitelesítéshez használt elsődleges kapcsolati karakterlánc |
    | *yourSecondaryKey* | Hitelesítéshez használt másodlagos kapcsolati karakterlánc |
    | *yourEventHubName* | Neve a **Eseményközpont** |

- Irányíthatja a **Eseményközpont** esemény típusa: **DeviceMessage**. Felvételét `EntityPath` a a **connectionString** megadása kötelező.

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey;EntityPath=yourEventHubName",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey;EntityPath=yourEventHubName",
    "path": "yourEventHubName"
  }
  ```

    | Egyéni attribútum neve | Cserélje le |
    | --- | --- |
    | *yourNamespace* | A névtér a végpont |
    | *yourPrimaryKey* | Hitelesítéshez használt elsődleges kapcsolati karakterlánc |
    | *yourSecondaryKey* | Hitelesítéshez használt másodlagos kapcsolati karakterlánc |
    | *yourEventHubName* | Neve a **Eseményközpont** |

> [!NOTE]
> Új végpont a létrehozás után is igénybe vehet legfeljebb 5 10 percre elindításához a fogadott események a végponton.

## <a name="primary-and-secondary-connection-keys"></a>Az elsődleges és másodlagos kapcsolati kulcsok

Ha egy elsődleges kapcsolati kulcs illetéktelen válik, a rendszer automatikusan megpróbálja a másodlagos kapcsolati kulcsot. Amely egy biztonsági mentési, és lehetővé teszi, hogy szabályosan hitelesíteni, és frissítse az elsődleges kulcsot a végpontok API-n keresztül lehetőségét.

Ha mind az elsődleges és másodlagos kapcsolati kulcsok nem engedélyezett, a rendszer beírja egy exponenciális visszatartási várakozási idő legfeljebb 30 perc. Események minden egyes aktivált visszatartási várakozni a rendszer eldobja.

Minden alkalommal, amikor a rendszer a visszatartási várakozási állapot, a frissítési kapcsolatok kulcsok a végpontok API-n keresztül érvénybe léptetéséhez akár 30 percet is igénybe vehet.

## <a name="unreachable-endpoints"></a>A végpontok nem érhető el

Amikor a végpont elérhetetlenné válik, a rendszer Megadja egy exponenciális visszatartási várakozási idő legfeljebb 30 perc. Események minden egyes aktivált visszatartási várakozni a rendszer eldobja.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan használható az Azure digitális Twins Swagger, olvassa el [Azure digitális Twins Swagger](how-to-use-swagger.md).

Útválasztási események és az Azure digitális Twins üzenetek kapcsolatos további információkért olvassa el [útválasztás események és az üzenetek](concepts-events-routing.md).
