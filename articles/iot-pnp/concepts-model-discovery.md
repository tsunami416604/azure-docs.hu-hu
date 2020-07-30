---
title: A IoT megvalósítása Plug and Play előnézeti modell felderítése | Microsoft Docs
description: Megoldás-szerkesztőként megismerheti, hogyan valósítható meg a IoT Plug and Play modell felderítése a megoldásban.
author: prashmo
ms.author: prashmo
ms.date: 07/23/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 364b85a8ead09858b97d5d7e6ca8c130b9960b2c
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337381"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>A IoT Plug and Play előnézeti modell felderítésének implementálása egy IoT-megoldásban

Ez a cikk azt ismerteti, hogyan lehet megvalósítani a IoT Plug and Play előnézeti modell felderítését egy IoT-megoldásban. A modell felderítése a következőket ismerteti:

- IoT Plug and Play-eszközök regisztrálják a modell AZONOSÍTÓját.
- Az IoT-megoldás az eszköz által megvalósított interfészeket kérdezi le.

A IoT-megoldásnak két nagy kategóriája van:

- A *IoT-megoldás* egy ismert IoT Plug and Play-modellel működik.

- A *modellen alapuló IoT-megoldások* bármilyen IoT Plug and Play eszközzel működhetnek. A modellen alapuló megoldás létrehozása összetettebb, de az előnye, hogy a megoldás a jövőben hozzáadott eszközökkel működik.

    A modellre épülő IoT-megoldás létrehozásához létre kell hoznia egy logikát a IoT Plug and Play felületi primitívek esetében: telemetria, Properties és parancsok. A megoldás logikája olyan eszközt képvisel, amely több telemetria, tulajdonságot és parancs-képességet egyesít.

Ez a cikk bemutatja, hogyan valósítható meg a modell felderítése mindkét típusú megoldásban.

## <a name="model-discovery"></a>Modellfelderítés

Az eszköz által megvalósított modell felderítéséhez a megoldás az eseményvezérelt felderítés vagy a Twin-based Discovery használatával tudja lekérni a modell AZONOSÍTÓját:

### <a name="event-based-discovery"></a>Eseményvezérelt felderítés

Amikor egy IoT Plug and Play eszköz csatlakozik IoT Hubhoz, regisztrálja az általa megvalósított modellt. Ez a regisztráció egy [digitális kettős változási eseményről](concepts-digital-twin.md#digital-twin-change-events) szóló értesítést eredményez. Ha meg szeretné tudni, hogyan engedélyezheti az útválasztást a digitális kettős események esetében, tekintse meg az [eszközről a felhőbe irányuló üzenetek különböző végpontokra való küldéséhez IoT hub üzenet-útválasztás használata](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)témakört

A megoldás az alábbi kódrészletben látható eseményt követve megismerheti a IoT Plug and Play eszközt, amely a csatlakozáshoz és a modell AZONOSÍTÓjának lekéréséhez használható:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/22/2020 8:02:27 PM
iothub-message-source:digitalTwinChangeEvents
correlation-id:100f322dc2c5
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:example:TemperatureController;1"
  }
]
```

Ez az esemény akkor aktiválódik, amikor az eszköz modell-AZONOSÍTÓját hozzáadja vagy frissíti.

### <a name="twin-based-discovery"></a>Twin-alapú felderítés

Ha a megoldás egy adott eszköz képességeiről szeretne többet megtudni, használhatja a [digitális Twin](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin/getdigitaltwin) API lekérése lehetőséget az információk lekéréséhez.

A következő digitális kettős kódrészletben `$metadata.$model` a IoT Plug and Play eszköz modell-azonosítóját tartalmazza:

```json
{
    "$dtId": "sample-device",
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

A megoldás a **Get Twin** paranccsal is lekérheti a modell azonosítóját az eszköz Twin-ből, ahogy az a következő kódrészletben látható:

```json
{
    "deviceId": "sample-device",
    "etag": "AAAAAAAAAAc=",
    "deviceEtag": "NTk0ODUyODgx",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "2020-07-17T06:12:26.8402249Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "modelId": "dtmi:com:example:TemperatureController;1",
    "version": 15,
    "properties": {...}
    }
}
```

## <a name="model-resolution"></a>Modell feloldása

A megoldás a modell feloldásával fér hozzá a modell AZONOSÍTÓját alkotó interfészekhez. 

- A megoldások úgy is dönthetnek, hogy egy helyi mappában található fájlként tárolják ezeket a csatolókat. 
- A megoldások használhatják a [modell tárházát](concepts-model-repository.md).

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte a Model Discovery egy IoT-megoldását, további információkat tudhat meg az [Azure IoT platformról](overview-iot-plug-and-play.md) a megoldás más képességeinek kihasználásához.

- [Kommunikáció egy eszközzel a megoldásból](quickstart-service-node.md)
- [IoT Digital Twin REST API](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin)
- [Azure IoT Explorer](howto-use-iot-explorer.md)
