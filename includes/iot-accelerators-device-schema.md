---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 07/26/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: dc87079083b8f07ad18f5f871bff64de8d492ebd
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285646"
---
## <a name="the-parts-of-the-device-model-schema"></a>Az eszközmodell sémájának részei

Minden egyes eszköz modellje, például teherautó, vagy hűtő eszköz szimulálhatja a szimuláció szolgáltatás olyan típusú határozza meg. Minden egyes eszköz modellje egy JSON-fájlt a következő legfelső szintű sémával tárolja:

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "elevator-01",
  "Version": "0.0.1",
  "Name": "Elevator",
  "Description": "Elevator with floor, vibration and temperature sensors.",
  "Protocol": "AMQP",
  "Simulation": {
    // Specify the simulation behavior
  },
  "Properties": {
    // Define properties
  },
  "Telemetry": [
    // Specify telemetry
  ],
  "CloudToDeviceMethods": {
    // Specify methods
  }
}
```

Az alapértelmezett a szimulált eszközök a séma fájljait is megtekintheti a [devicemodels mappa](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels) a Githubon.

A következő táblázat ismerteti a legfelső szintű séma bejegyzések:

| Séma bejegyzés | Leírás |
| -- | --- |
| `SchemaVersion` | A séma verziója mindig `1.0.0` , és csak ez a fájl formátumát. |
| `Id` | Az eszközmodell egyedi Azonosítót. |
| `Version` | Az eszköz modellje verzióját azonosítja. |
| `Name` | Az eszköz modellje rövid nevét. |
| `Description` | Az eszköz modellje leírása. |
| `Protocol` | A kapcsolat az eszköz protokollt használ. Lehetnek `AMQP`, `MQTT`, és `HTTP`. |

A következő szakaszok ismertetik a többi szakasz a JSON-séma:

## <a name="simulation"></a>Szimuláció

Az a `Simulation` szakaszban meghatározhatja a szimulált eszközt a belső állapotot. Az eszköz által küldött telemetriai értékeket az eszköz állapota részének kell lennie.

Az eszköz állapotát definíciójában két elemet:

* `InitialState` az eszköz állapota objektum összes tulajdonsága esetén a kezdeti értékeket határozza meg.
* `Script` azonosítja, hogy az állapot frissítéséhez ütemezés szerint futtatott JavaScript-fájlt. A parancsfájl segítségével véletlenszerűvé tétele az eszköz által küldött telemetriai adatok értékek.

A JavaScript-fájlt, amely frissíti az eszköz állapota objektum kapcsolatos további információkért lásd: [az eszközmodell viselkedésének megismerése](../articles/iot-accelerators/iot-accelerators-device-simulation-device-behavior.md).

Az alábbi példa bemutatja egy szimulált hűtő eszköz az eszköz állapota objektum definíciója:

```json
"Simulation": {
  "InitialState": {
    "online": true,
    "temperature": 75.0,
    "temperature_unit": "F",
    "humidity": 70.0,
    "humidity_unit": "%",
    "pressure": 150.0,
    "pressure_unit": "psig",
    "simulation_state": "normal_pressure"
  },
  "Interval": "00:00:10",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

A szimuláció szolgáltatás fut a **hűtő-01-state.js** fájl öt másodpercenként az állapot frissítéséhez. Láthatja, hogy a JavaScript-fájlok esetében az alapértelmezett a szimulált eszközök a [parancsfájlmappa](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) a Githubon. Szabályok szerint a JavaScript-fájlok rendelkezik az utótag **-állapot** megkülönböztetésükhöz módszer viselkedések megvalósító a fájlokból.

## <a name="properties"></a>Tulajdonságok

A `Properties` szakasz a séma meghatározza a megoldás jelentett tulajdonságértékeket. Példa:

```json
"Properties": {
  "Type": "Elevator",
  "Location": "Building 2",
  "Latitude": 47.640792,
  "Longitude": -122.126258
}
```

Amikor a megoldás elindul, lekérdezi a szimulált eszközök listájának létrehozásához `Type` értékeket kell használnia a felhasználói felületen. A megoldás a `Latitiude` és `Longitude` tulajdonságok hozzáadása az eszköz helyét a térképen az irányítópulton.

## <a name="telemetry"></a>Telemetria

A `Telemetry` tömb a szimulált eszköz által küldött a megoldás összes telemetriai típusokat sorolja fel.

Az alábbi példa JSON telemetriai üzenetet küld a 10 másodpercenként `floor`, `vibration`, és `temperature` a felvonó érzékelők adatait:

```json
"Telemetry": [
  {
    "Interval": "00:00:10",
    "MessageTemplate": "{\"floor\":${floor},\"vibration\":${vibration},\"vibration_unit\":\"${vibration_unit}\",\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\"}",
    "MessageSchema": {
      "Name": "elevator-sensors;v1",
      "Format": "JSON",
      "Fields": {
        "floor": "integer",
        "vibration": "double",
        "vibration_unit": "text",
        "temperature": "double",
        "temperature_unit": "text"
      }
    }
  }
]
```

`MessageTemplate` a JSON-üzenet a szimulált eszköz által küldött struktúráját határozza meg. A helyőrzőket a `MessageTemplate` a szintaxissal `${NAME}` ahol `NAME` a kulcs a [állapot eszközobjektum](#simulation). Meg kell adni a karakterláncokat, a számok nem kell.

`MessageSchema` a szimulált eszköz által küldött üzenet a séma határozza meg. Az üzenet séma is közzé van téve, az IoT hubot, hogy lehetővé teszik az alkalmazások háttér újra felhasználhatja az információkat a bejövő telemetriát értelmezése.

Jelenleg csak használható JSON üzenetsémákat. A séma szereplő mezőket a következő típusú lehet:

* Objektum - szerializált JSON használatával
* Bináris - szerializálni a base64 használatával
* Szöveg
* Logikai
* Egész szám
* Dupla
* DateTime

Különböző időközönként telemetriai üzeneteket küldhet a több telemetriatípusok hozzáadása a `Telemetry` tömb. Az alábbi példa hőmérséklettel és páratartalommal kapcsolatos adatokat küld minden 10 másodperc és a világos percenként állapotát:

```json
"Telemetry": [
  {
    "Interval": "00:00:10",
    "MessageTemplate":
      "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"humidity\":\"${humidity}\"}",
    "MessageSchema": {
      "Name": "RoomComfort;v1",
      "Format": "JSON",
      "Fields": {
        "temperature": "double",
        "temperature_unit": "text",
        "humidity": "integer"
      }
    }
  },
  {
    "Interval": "00:01:00",
    "MessageTemplate": "{\"lights\":${lights_on}}",
    "MessageSchema": {
      "Name": "RoomLights;v1",
      "Format": "JSON",
      "Fields": {
        "lights": "boolean"
      }
    }
  }
],
```

## <a name="cloudtodevicemethods"></a>CloudToDeviceMethods

Felhőből az eszközre metódusokat meghívni egy IoT hubot a szimulált eszköz válaszolhat. A `CloudToDeviceMethods` szakaszt az eszköz modellje séma fájlban:

* Meghatározza a módszereket, a szimulált eszköz válaszolhat.
* A JavaScript-fájlt, amely tartalmazza a logika végrehajtására azonosítja.

A szimulált eszköz támogatja a metódusok küld az IoT hub van csatlakoztatva.

A JavaScript-fájlt, amely megvalósítja az eszköz viselkedésének kapcsolatos további információkért lásd: [az eszközmodell viselkedésének megismerése](../articles/iot-accelerators/iot-accelerators-device-simulation-device-behavior.md).

Az alábbi példa meghatározza a három támogatott módszerek és a JavaScript-fájlok, amelyek alkalmazzák azokat a módszereket:

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
  },
  "EmergencyValveRelease": {
    "Type": "javascript",
    "Path": "EmergencyValveRelease-method.js"
  },
  "IncreasePressure": {
    "Type": "javascript",
    "Path": "IncreasePressure-method.js"
  }
}
```

Láthatja, hogy a JavaScript-fájlok esetében az alapértelmezett a szimulált eszközök a [parancsfájlmappa](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) a Githubon. Szabályok szerint a JavaScript-fájlok rendelkezik az utótag **-metódus** megkülönböztetendő állapot viselkedés fájlokat.