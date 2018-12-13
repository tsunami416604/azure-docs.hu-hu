---
title: Az adatok exportálása az Azure Event Hubs és Azure Service Bus |} A Microsoft Docs
description: Adatok exportálása az Azure Event Hubs és Azure Service Bus az Azure IoT Central alkalmazásból
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 12/07/2018
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 14b51f109ca76661ac10c99d42002dda45bc0500
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318695"
---
# <a name="export-your-data-in-azure-iot-central"></a>Exportálhatja az adatokat az Azure IoT Central

*Ez a témakör a rendszergazdák vonatkozik.*

Ez a cikk ismertetőinken részletesebben ismerteti, hogyan lehet a folyamatos exportálás funkció használata az Azure IoT Central exportálhatja az adatokat a saját **Azure Event Hubs**, és **Azure Service Bus** példányok. Exportálhatja **mérések**, **eszközök**, és **eszközsablonok** saját célra a meleg folyamatból információkhoz és elemzésekhez juthat. Ez magában foglalja a elindítása az Azure Stream Analytics szolgáltatásban az egyéni szabályok, egyéni munkafolyamatokat az Azure Logic Appsben, elindítása vagy az adatok átalakítása és azt továbbítja az Azure Functions. 

> [!Note]
> Ismét bekapcsolja a folyamatos exportálás, kap csak az adatok ettől a pillanattól kezdve. Jelenleg adatokat nem lehet beolvasni egy alkalommal, amikor folyamatos adatexportálás ki volt kapcsolva. További korábbi adatok megőrzése, kapcsolja be a folyamatos exportálás korai.


## <a name="prerequisites"></a>Előfeltételek

- Egy rendszergazdának kell lennie az IoT-központ alkalmazásában

## <a name="export-to-azure-event-hubs-and-azure-service-bus"></a>Az Azure Event Hubs és Azure Service Bus exportálása

Mértékek, eszközök és eszközadatok sablonok az event hubs vagy Service Bus-üzenetsor vagy témakör, közel valós idejű exportálódik. Exportált mérések adatok tartalmazzák a teljes üzenet küld az IoT-központ, az eszközök nem csak a maguk mérések értékeinek. Exportált eszközök adatok tulajdonságokat és beállításokat az összes eszköz módosításokat tartalmaz, és az exportált eszközsablonok összes eszközsablonok módosításokat tartalmaz. Az exportált adatok a "body" tulajdonságban pedig JSON formátumban.

> [!NOTE]
> Az exportálási cél, az üzenetsorok és témakörök egy Service Bus kiválasztásakor **nem lehet munkamenetet vagy ismétlődő a Fenyegetésészlelés engedélyezve**. Ezek a lehetőségek valamelyikét engedélyezve vannak, ha egyes üzeneteket az üzenetsor vagy témakör nem érkezik.

### <a name="measurements"></a>Mérések

Egy új üzenet gyorsan exportálása után az IoT-központ fogadja az üzenetet az eszközről. Minden exportált üzenetet az Event Hubs és a Service Bus tartalmazza a teljes üzenet az eszközön, a "body" tulajdonságban JSON formátumban küldi el. 

> [!NOTE]
> Az eszközöket, amelyek a mérések küldése (lásd a következő szakaszok) eszköz azonosítóját képviseli. Az eszközök nevei, eszköz-adatok exportálása és vesse össze az egyes felettese használatával a **connectionDeviceId** , amely megfelel a **deviceId** , az üzenetet.

A következő példa bemutatja, hogy mérések adatokkal kapcsolatos üzenet érkezett az event hubs vagy a Service Bus-üzenetsor vagy témakör.

```json
{
  "body": {
    "humidity": 29.06963648666288,
    "temp": 8.4503795661685,
    "pressure": 1075.8334910110093,
    "magnetometerX": 408.6966458887116,
    "magnetometerY": -532.8809796603962,
    "magnetometerZ": 174.70653875528205,
    "accelerometerX": 1481.546749013788,
    "accelerometerY": -1139.4316656437406,
    "accelerometerZ": 811.6928695575307,
    "gyroscopeX": 442.19879163299856,
    "gyroscopeY": 123.23710975717177,
    "gyroscopeZ": 708.5397575786151,
    "deviceState": "DANGER"
  },
  "annotations": {
    "iothub-connection-device-id": "<connectionDeviceId>",
    "iothub-connection-auth-method": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "<generationId>",
    "iothub-enqueuedtime": 1539381029965,
    "iothub-message-source": "Telemetry",
    "x-opt-sequence-number": 25325,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539381030200
  },
  "sequenceNumber": 25325,
  "enqueuedTimeUtc": "2018-10-12T21:50:30.200Z",
  "offset": "<offset>",
  "properties": {
    "content_type": "application/json",
    "content_encoding": "utf-8"
  }
}
```

### <a name="devices"></a>Eszközök

Eszköz-adatokat tartalmazó üzenetek érkeznek az event hubs vagy Service Bus-üzenetsor vagy témakör néhány percenként. Ez azt jelenti, hogy néhány percenként egy üzenetköteget érkezik adatokkal kapcsolatban
- Új hozzáadott eszközöket
- Módosított tulajdonság és értékeinek beállítása

Minden üzenetet egy eszközhöz egy vagy több módosítás óta az utolsó exportált üzenet jelöli. Az egyes üzenetekben küldött adatokat tartalmazza:
- `id` az eszköz az IoT-központ
- `name` az eszköz
- `deviceId` a [Device Provisioning Service](https://aka.ms/iotcentraldocsdps)
- Eszköz sablon adatai
- Tulajdonságok értékei
- Beállításértékek

> [!NOTE]
> Az eszközök történt, a legutóbbi köteg nem exportálható. Jelenleg nincsenek mutatók törölt eszközök exportált üzenetekben.
>
> Az eszköz a sablon, amely minden eszközhöz tartozik egy eszközazonosítót sablon. által jelölt Az eszköz sablon nevének lekérése, ügyeljen arra, eszközadatok sablon exportálása túl.

Az alábbi példa bemutatja a eszközadatok szóló üzenetet az event hubs vagy a Service Bus-üzenetsor vagy témakör:


```json
{
  "body": {
    "id": "<id>",
    "name": "<deviceName>",
    "simulated": true,
    "deviceId": "<deviceId>",
    "deviceTemplate": {
      "id": "<templateId>",
      "version": "1.0.0"
    },
    "properties": {
      "cloud": {
        "location": "Seattle"
      },
      "device": {
        "dieNumber": 5445.5862873026645
      }
    },
    "settings": {
      "device": {
        "fanSpeed": 0
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "devices",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 39740,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274959654
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 39740,
  "enqueuedTimeUtc": "2018-10-11T16:22:39.654Z",
  "offset": "<offset>",
}
```

### <a name="device-templates"></a>Eszköz-sablonok

Eszköz sablonok adatokat tartalmazó üzenetek érkeznek az event hubs vagy Service Bus-üzenetsor vagy témakör néhány percenként. Ez azt jelenti, hogy néhány percenként egy üzenetköteget érkezik adatokkal kapcsolatban
- Hozzáadott új eszközt-sablonok
- Eszközsablonok módosított mérések, tulajdonságot és definíciókat beállítása

Minden üzenetet egy vagy több módosítás eszköz sablonok óta az utolsó exportált üzenet jelöli. Az egyes üzenetekben küldött adatokat tartalmazza:
- `id` az eszköz sablon
- `name` az eszköz sablon
- `version` az eszköz sablon
- Mérési az adattípusok és a minimális/maximális értékei
- A tulajdonság az adattípusok és az alapértelmezett értékek
- A beállítás az adattípusokat és az alapértelmezett értékek

> [!NOTE]
> Törli a legutóbbi köteg óta eszközsablonok nem exportálható. Jelenleg nincsenek mutatók az exportált üzenetekben a törölt sablonokat.

Az alábbi példa bemutatja a sablonok eszközadatok szóló üzenetet az event hubs vagy a Service Bus-üzenetsor vagy témakör:

```json
{
  "body": {
    "id": "<id>",
    "version": "1.0.0",
    "name": "<templateName>",
    "measurements": {
      "telemetry": {
        "humidity": {
          "dataType": "double",
          "name": "humidity"
        },
        "pressure": {
          "dataType": "double",
          "name": "pressure"
        },
        "temp": {
          "dataType": "double",
          "name": "temperature"
        }
      }
    },
    "properties": {
      "cloud": {
        "location": {
          "dataType": "string",
          "name": "Location"
        }
      },
      "device": {
        "dieNumber": {
          "dataType": "double",
          "name": "Die Number"
        }
      }
    },
    "settings": {
      "device": {
        "fanSpeed": {
          "dataType": "double",
          "name": "Fan Speed",
          "initialValue": 0
        }
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "deviceTemplates",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 25315,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274985085
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 25315,
  "enqueuedTimeUtc": "2018-10-11T16:23:05.085Z",
  "offset": "<offset>",
}
```

## <a name="next-steps"></a>További lépések

Most, hogy tudja, hogyan exportálhatja az adatokat az Azure Event Hubs és Azure Service Bus, folytassa a következő lépéssel:

> [!div class="nextstepaction"]
> [Hogyan lehet az Azure Functions aktiválása](howto-trigger-azure-functions.md)
