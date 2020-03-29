---
title: Kijutás és végpontok - Azure Digital Twins | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre és kiejkezik az eseményvégpontok az Azure Digital Twins-ben.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 3803802a3d81655091d8be543ae9cb17221a98d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511569"
---
# <a name="egress-and-endpoints-in-azure-digital-twins"></a>Kimenő forgalom és végpontok az Azure Digital Twins-ben

Az Azure Digital Twins *végpontok* egy üzenetet vagy eseményközvetítőt képviselnek a felhasználó Azure-előfizetésén belül. Események és üzenetek küldhetők az Azure Event Hubs, az Azure Event Grid és az Azure Service Bus témakörei.

Az események et előre definiált útválasztási beállítások nak megfelelően irányítja a végpontok. A felhasználók határozzák meg, hogy az egyes végpontok milyen *eseménytípusokat* kaphatnak.

Az eseményekről, útválasztási és eseménytípusokról az [Útválasztási események és üzenetek az Azure Digital Twins alkalmazásban](./concepts-events-routing.md)című részben olvashat bővebben.

## <a name="events"></a>Események

Az eseményeket IoT-objektumok (például eszközök és érzékelők) küldik az Azure-üzenetek és eseményközvetítők általi feldolgozásra. Az eseményeket a következő [Azure Event Grid eseményrács-eseményséma-hivatkozás](../event-grid/event-schema.md)határozza meg.

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

| Attribútum | Típus | Leírás |
| --- | --- | --- |
| id | sztring | Az esemény egyedi azonosítója |
| Tárgy | sztring | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| data | objektum | Az erőforrás-szolgáltatóra jellemző eseményadatok. |
| eventType | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| eventTime | sztring | Az esemény létrehozásának időpontja a szolgáltató UTC-ideje alapján. |
| dataVersion | sztring | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| metadataVersion | sztring | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |
| témakör | sztring | Az eseményforrás teljes erőforráselérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |

Az Event Grid eseménysémájával kapcsolatos további tudnivalókért:

- Tekintse át az [Azure Event Grid eseményséma-hivatkozását.](../event-grid/event-schema.md)
- Olvassa el az [Azure EventGrid Node.js SDK EventGridEvent hivatkozását.](https://docs.microsoft.com/javascript/api/@azure/eventgrid/eventgridevent?view=azure-node-latest)

## <a name="event-types"></a>Eseménytípusok

Az eseménytípusok osztályozzák az esemény jellegét, és az **eventType** mezőben vannak beállítva. Az elérhető eseménytípusokat a következő lista sorolja meg:

- TopológiaÜzemeltetés
- UdfCustom
- Érzékelőmódosítása
- Térváltozás
- DeviceMessage

Az egyes eseménytípusok eseményformátumait a következő alszakaszok ismertetik.

### <a name="topologyoperation"></a>TopológiaÜzemeltetés

**TopologyOperation** a grafikonmódosításra vonatkozik. A **tárgy** tulajdonság az érintett objektum típusát határozza meg. Az alábbi típusú objektumok válthatják ki ezt az eseményt:

- Eszköz
- DeviceBlobMetadata
- DeviceExtendedTulajdonság
- ExtendedPropertyKey
- Kiterjesztett típus
- Keystore
- Jelentés
- Szerepkördefiníció
- Érzékelő
- SensorBlobMetadata
- SensorExtendedProperty tulajdonság
- Space (Szóköz)
- SpaceBlobMetadata
- SpaceExtendedTulajdonság
- Szóközerőforrás
- SpaceRoleAssignment (SpaceRoleAssignment)
- Rendszer
- Felhasználó
- UserBlobMetadata (UserBlobMetadata)
- UserExtendedTulajdonság

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
| YOUR_TOPIC_NAME | A testreszabott témakör neve |

### <a name="udfcustom"></a>UdfCustom

**Az UdfCustom** egy felhasználó által definiált függvény (UDF) által küldött esemény.
  
> [!IMPORTANT]  
> Ezt az eseményt kifejezetten magából az UDF-ből kell elküldeni.

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
| YOUR_TOPIC_NAME | A testreszabott témakör neve |

### <a name="sensorchange"></a>Érzékelőmódosítása

**A SensorChange** egy érzékelő állapotának telemetriai változásokon alapuló frissítése.

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
| YOUR_TOPIC_NAME | A testreszabott témakör neve |

### <a name="spacechange"></a>Térváltozás

**A SpaceChange** a tér állapotának telemetriai változásokon alapuló frissítése.

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
| YOUR_TOPIC_NAME | A testreszabott témakör neve |

### <a name="devicemessage"></a>DeviceMessage

A **DeviceMessage**használatával megadhat egy **EventHub-kapcsolatot,** amelyhez a nyers telemetriai események is továbbíthatók az Azure Digital Twins-ből.

> [!NOTE]
> - **A DeviceMessage** csak az **EventHubbal**kombinálható. A **DeviceMessage** nem kombinálható a többi eseménytípussal.
> - **Az EventHub** vagy a **DeviceMessage**típusú kombinációnak csak egy végpontot adhat meg.

## <a name="configure-endpoints"></a>Végpontok konfigurálása

A végpontkezelés az Endpoints API-n keresztül történik.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Az alábbi példák bemutatják, hogyan kell konfigurálni a támogatott végpontokat.

>[!IMPORTANT]
> Fordítson különös figyelmet az **eventTypes** attribútumra. Meghatározza, hogy a végpont mely eseménytípusokat kezeli, és így meghatározza annak útválasztását.

Hitelesített HTTP POST-kérelem:

```URL
YOUR_MANAGEMENT_API_URL/endpoints
```

- Route to Service Bus eseménytípusok **SensorChange**, **SpaceChange**és **TopologyOperation**:

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
    | YOUR_NAMESPACE | A végpont névtere |
    | YOUR_PRIMARY_KEY | A hitelesítéshez használt elsődleges kapcsolati karakterlánc |
    | YOUR_SECONDARY_KEY | A hitelesítéshez használt másodlagos kapcsolati karakterlánc |
    | YOUR_TOPIC_NAME | A testreszabott témakör neve |

- Útvonal az Event Grid **eseménytípusokSensorChange**, **SpaceChange**, és **TopologyOperation**:

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
    | YOUR_PRIMARY_KEY | A hitelesítéshez használt elsődleges kapcsolati karakterlánc|
    | YOUR_SECONDARY_KEY | A hitelesítéshez használt másodlagos kapcsolati karakterlánc |
    | YOUR_TOPIC_NAME | A testreszabott témakör neve |

- Route to Event Hubs eseménytípusok **SensorChange**, **SpaceChange**és **TopologyOperation**:

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
    | YOUR_NAMESPACE | A végpont névtere |
    | YOUR_PRIMARY_KEY | A hitelesítéshez használt elsődleges kapcsolati karakterlánc |
    | YOUR_SECONDARY_KEY | A hitelesítéshez használt másodlagos kapcsolati karakterlánc |
    | YOUR_EVENT_HUB_NAME | Az eseményközpont neve |

- Route to Event Hubs **eseménytípus: DeviceMessage**. A `EntityPath` **connectionString-be** való felvétel kötelező:

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
    | YOUR_NAMESPACE | A végpont névtere |
    | YOUR_PRIMARY_KEY | A hitelesítéshez használt elsődleges kapcsolati karakterlánc |
    | YOUR_SECONDARY_KEY | A hitelesítéshez használt másodlagos kapcsolati karakterlánc |
    | YOUR_EVENT_HUB_NAME | Az eseményközpont neve |

> [!NOTE]  
> Egy új végpont létrehozása kor akár 5–10 percet is igénybe vehet az események fogadása a végponton.

## <a name="primary-and-secondary-connection-keys"></a>Elsődleges és másodlagos kapcsolatkulcsok

Ha egy elsődleges kapcsolatkulcs jogosulatlanná válik, a rendszer automatikusan megpróbálja a másodlagos kapcsolatkulcsot. Ez biztosítja a biztonsági mentést, és lehetővé teszi az elsődleges kulcs szabályos hitelesítését és frissítését az Endpoints API-n keresztül.

Ha az elsődleges és a másodlagos kapcsolatkulcsok nem engedélyezettek, a rendszer egy legfeljebb 30 perces exponenciális visszalépési várakozási időt ad meg. Az események minden aktivált visszalépési várakozási időre lekerülnek.

Amikor a rendszer egy back-off várakozási állapotban van, a kapcsolatok kulcsainak frissítése az Endpoints API-n keresztül akár 30 percet is igénybe vehet.

## <a name="unreachable-endpoints"></a>Nem elérhető végpontok

Amikor egy végpont elérhetetlenné válik, a rendszer egy legfeljebb 30 perces exponenciális visszalépési várakozási időt ír be. Az események minden aktivált visszalépési várakozási időre lekerülnek.

## <a name="next-steps"></a>További lépések

- Ismerje [meg, hogyan használhatja az Azure Digital Twins Swagger.](how-to-use-swagger.md)

- További információ az [útválasztási eseményekről és üzenetekről](concepts-events-routing.md) az Azure Digital Twins ben.
