---
title: A távoli felügyeleti megoldás - Azure eszköz séma |} Microsoft Docs
description: Ez a cikk ismerteti a JSON-séma, amely meghatározza a szimulált eszköz a távoli felügyeleti megoldás.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: 1ba73d24aaa113a9124e17ea91946c205b21fba6
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627433"
---
# <a name="understand-the-device-model-schema"></a>Az eszköz modellsémát ismertetése

A távoli figyelésére szolgáló megoldás szimulált eszköz segítségével ellenőrizze a viselkedését. A távoli figyelésére szolgáló megoldás telepítésekor szimulált eszközök automatikusan lett kiépítve. A meglévő szimulált eszközök testreszabása, vagy hozza létre a sajátját.

Ez a cikk ismerteti, amely meghatározza a szolgáltatásait és a szimulált eszköz működését eszköz modellsémát. Az eszköz típusa egy JSON-fájl tárolja.

A jelen cikkben kapcsolódó a következő cikkeket:

* [Az eszköz modell viselkedés](iot-accelerators-remote-monitoring-device-behavior.md) használhatja a szimulált eszköz viselkedés JavaScript fájlokat ismerteti.
* [Hozzon létre egy új szimulált eszköz](iot-accelerators-remote-monitoring-test.md) együtt helyezi, és bemutatja, hogyan telepíthet egy új szimulált eszköz típusa a megoldás.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

>[!div class="checklist"]
> * A szimulált eszköz modellek meghatározásához a JSON-fájl használatával
> * Adja meg a tulajdonságokat a szimulált eszköz
> * A szimulált eszköz küld telemetriai adatok megadása
> * Adja meg az eszköz válaszol-e a felhőből eszközre módszerek

## <a name="the-parts-of-the-device-model-schema"></a>Az eszköz modellsémát részei

Minden eszköz típusa, például hűtő vagy teherautó, a szimulált eszköz kapcsolódni a távoli figyelésére szolgáló megoldás típusának meghatározása. Minden eszköz modell egy JSON-fájl a következő legfelső szintű sémával tárolja:

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

A szimulált alapértelmezett-eszközökhöz a sémafájlok megtekintheti a [devicemodels mappa](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels) a Githubon.

A következő táblázat ismerteti a legfelső szintű séma bejegyzéseket:

| Séma-bejegyzés | Leírás |
| -- | --- |
| `SchemaVersion` | A séma verziószáma mindig `1.0.0` és vonatkoznak, a fájl formátumát. |
| `Id` | A modell egyedi azonosítója. |
| `Version` | Azonosítja a eszközmodell verzióját. |
| `Name` | Az eszköz típusa rövid nevét. |
| `Description` | Az eszköz típusa leírását. |
| `Protocol` | A kapcsolat protokoll az eszközt használja. Egyike lehet `AMQP`, `MQTT`, és `HTTP`. |

A következő szakaszok ismertetik a JSON-séma más részei:

## <a name="simulation"></a>Szimuláció

Az a `Simulation` szakaszban adhat meg a belső állapotot, a szimulált eszköz. Az eszköz által küldött telemetriai értékeket az eszköz állapota részének kell lennie.

Az eszköz állapotát definíciója két elemmel rendelkezik:

* `InitialState` az objektum összes tulajdonságának értéke az eszköz állapota kezdeti értékeinek meghatározása.
* `Script` a JavaScript-fájlt, amely az eszköz állapotát frissítése ütemezés szerint futtatott azonosítja. A parancsfájl segítségével az eszköz által küldött telemetriai értékek ügyfélfuttatási.

A JavaScript-fájlt, amely frissíti az eszköz állapotobjektum kapcsolatos további információkért lásd: [eszköz modell viselkedésének megértése](iot-accelerators-remote-monitoring-device-behavior.md).

A következő példa bemutatja egy szimulált hűtő eszköz számára az eszköz állapota objektum definíciója:

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

A szimuláció szolgáltatás fut a **hűtő-01-state.js** öt másodpercenként fájlt az eszköz állapotának frissítése. A JavaScript-fájlt a szimulált alapértelmezett eszközökkel láthatja a [parancsfájlmappa](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) a Githubon. Egyezmény a JavaScript-fájlok rendelkeznek a utótag **-állapot** megkülönböztetni ezeket a fájlokat, amelyek megvalósítják az metódus viselkedések.

## <a name="properties"></a>Tulajdonságok

A `Properties` szakasz a séma meghatározza a tulajdonságértékek, az eszköz jelentés készít a megoldáshoz. Példa:

```json
"Properties": {
  "Type": "Elevator",
  "Location": "Building 2",
  "Latitude": 47.640792,
  "Longitude": -122.126258
}
```

A megoldás elindításakor lekéri a szimulált eszközök listájának összeállítása `Type` a felhasználói felületen használni kívánt értékeket. A megoldás használ a `Latitiude` és `Longitude` tulajdonságok hozzáadása az eszköz helye a térkép az irányítópulton.

## <a name="telemetry"></a>Telemetria

A `Telemetry` tömb a szimulált eszköz küld a megoldás összes telemetriai típusokat sorolja fel.

Az alábbi példa JSON telemetriai üzenetet küld a 10 másodpercenként `floor`, `vibration`, és `temperature` a foglalhatja érzékelők adatait:

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

`MessageTemplate` a JSON-üzenet, a szimulált eszköz által küldött struktúrát határozza meg. A helyőrzőket `MessageTemplate` a szintaxissal `${NAME}` ahol `NAME` a kulcs a [állapot eszközobjektum](#simulation). Meg kell adni a karakterlánc, szám nem kell.

`MessageSchema` a szimulált eszköz által küldött üzenet sémájának definiálásához. Az üzenet-sémát is háttér alkalmazások értelmezni a bejövő telemetriai adatokat felhasználhatja az IoT-központ van közzétéve.

Jelenleg csak használható JSON üzenet sémák. A séma szereplő mezőket a következő típusúak lehetnek:

* Objektum - szerializált JSON használata
* Bináris - szerializált base64 használatával
* Szöveg
* Logikai
* Egész szám
* Dupla
* DateTime

Telemetriai adatok küldéséhez más időközönként, adja hozzá a telemetriai adatok többféle a `Telemetry` tömb. Az alábbi példa hőmérséklet és a páratartalom adatokat küld minden 10 percenként fény állapotát és a másodperc:

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

A szimulált eszköz tud válaszolni a távoli figyelésére szolgáló megoldás hívása felhő eszközre módszerek. A `CloudToDeviceMethods` az eszköz modell séma fájl:

* A szimulált eszköz válaszolhassanak módszerek meghatározása.
* Azonosítja a JavaScript-fájlt, amely tartalmazza a programot végrehajtani.

A szimulált eszköz támogatja metódusok küld a távoli figyelésére szolgáló megoldás.

A JavaScript-fájlt az eszköz viselkedését meghatározó kapcsolatos további információkért lásd: [eszköz modell viselkedésének megértése](iot-accelerators-remote-monitoring-device-behavior.md).

Az alábbi példa meghatározza a három támogatott módszerek és a JavaScript-fájlok, amelyek megvalósítják az azokat a módszereket:

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

A JavaScript-fájlt a szimulált alapértelmezett eszközökkel láthatja a [parancsfájlmappa](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) a Githubon. Egyezmény a JavaScript-fájlok rendelkeznek a utótag **-metódus** megkülönböztetni ezeket a fájlokat, amelyek megvalósítják az állapot viselkedését.

## <a name="next-steps"></a>További lépések

Ez a cikk ismerteti a saját egyéni szimulált eszköz modell létrehozása. Ez a cikk bemutatta, hogyan számára:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * A szimulált eszköz modellek meghatározásához a JSON-fájl használatával
> * Adja meg a tulajdonságokat a szimulált eszköz
> * A szimulált eszköz küld telemetriai adatok megadása
> * Adja meg az eszköz válaszol-e a felhőből eszközre módszerek

Most a JSON-séma megismerte a javasolt következő lépésre megtudhatja, hogyan [a szimulált eszköz viselkedés](iot-accelerators-remote-monitoring-device-behavior.md).

A távoli figyelésére szolgáló megoldás fejlesztői ismertetését lásd:

* [Fejlesztői referencia-útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Fejlesztői hibaelhárítási útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->