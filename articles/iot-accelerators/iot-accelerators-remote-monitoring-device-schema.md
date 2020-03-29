---
title: Eszközséma a távoli figyelési megoldásban - Azure | Microsoft dokumentumok
description: Ez a cikk a JSON-sémát ismerteti, amely egy szimulált eszközt határoz meg a távoli figyelési megoldásban.
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 12/18/2018
ms.topic: conceptual
ms.openlocfilehash: 0f9669d491648ecc621aab27d0908dcc3dc84438
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65823330"
---
# <a name="understand-the-device-model-schema"></a>Az eszközmodell sémájának megismerése

A távoli figyelési megoldásban szimulált eszközök keltheti a viselkedését. A távoli figyelési megoldás tartalmaz egy eszközszimulációs szolgáltatást szimulált eszközök futtatásához. A távoli figyelési megoldás telepítésekor a szimulált eszközök gyűjteménye automatikusan kiépítésre kerül. Testreszabhatja a meglévő szimulált eszközöket, vagy létrehozhat sajátot.

Ez a cikk az eszközmodell sémáját ismerteti, amely egy szimulált eszköz képességeit és viselkedését határozza meg. Az eszközmodell jsonfájlban van tárolva.

> [!NOTE]
> Ez az eszközmodell-séma csak az eszközszimulációs szolgáltatásban tárolt szimulált eszközökre szolgál. Ha valódi eszközt szeretne létrehozni, olvassa el [Az eszköz csatlakoztatása a távfigyelési megoldás gyorsítóhoz című témakört.](iot-accelerators-connecting-devices.md)

A következő cikkek kapcsolódnak az aktuális cikkhez:

* [Valósítsa meg az eszközmodell viselkedését,](iot-accelerators-remote-monitoring-device-behavior.md) amely a szimulált eszközök viselkedésének megvalósításához használt JavaScript-fájlokat írja le.
* [Hozzon létre egy új szimulált eszközt,](iot-accelerators-remote-monitoring-create-simulated-device.md) amely mindent összeállít, és megmutatja, hogyan helyezhet üzembe egy új szimulált eszköztípust a megoldásra.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

>[!div class="checklist"]
> * Szimulált eszközmodell definiálása JSON-fájl használatával
> * A szimulált eszköz tulajdonságainak megadása
> * Adja meg a szimulált eszköz által küldött telemetriai adatokat
> * Adja meg azokat a felhőből az eszközre irányuló metódusokat, amelyekre az eszköz válaszol

## <a name="the-parts-of-the-device-model-schema"></a>Az eszközmodell sémájának részei

Minden eszközmodell, például egy hűtő vagy teherautó meghatározza, hogy a szimulációs szolgáltatás milyen típusú eszközt szimulálhat. Minden eszközmodell egy JSON-fájlban van tárolva a következő legfelső szintű sémával:

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

Megtekintheti az alapértelmezett szimulált eszközök sémafájljait a GitHub [devicemodels mappájában.](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels)

Az alábbi táblázat a legfelső szintű sémabejegyzéseket ismerteti:

| Sémabejegyzés | Leírás |
| -- | --- |
| `SchemaVersion` | A séma verziója `1.0.0` mindig a fájl formátumára jellemző. |
| `Id` | Az eszközmodell egyedi azonosítója. |
| `Version` | Az eszközmodell verzióját azonosítja. |
| `Name` | Az eszközmodell rövid neve. |
| `Description` | Az eszközmodell leírása. |
| `Protocol` | Az eszköz által használt kapcsolati protokoll. Lehet az `AMQP`, `MQTT`és `HTTP`a . |

A következő szakaszok a JSON-séma többi szakaszát ismertetik:

## <a name="simulation"></a>Szimuláció

A `Simulation` szakaszban megadhatja a szimulált eszköz belső állapotát. Az eszköz által küldött telemetriai értékeknek az eszköz állapotának részét kell, hogy legyenek.

Az eszközállapot definíciójának két eleme van:

* `InitialState`az eszközállapot-objektum összes tulajdonságának kezdeti értékeit határozza meg.
* `Script`azonosítja az eszközállapotának frissítéséhez ütemezett JavaScript-fájlt. Ezzel a parancsfájlfájllal véletlenszerűen az eszköz által küldött telemetriai értékeket.

Ha többet szeretne megtudni az eszközállapot-objektumot szerkesztő JavaScript-fájlról, [olvassa el Az eszközmodell viselkedésének ismertetése című témakört.](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md)

A következő példa az eszközállapot-objektum definícióját mutatja be egy szimulált hűtőeszközhöz:

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

A szimulációs szolgáltatás öt másodpercenként futtatja a **chiller-01-state.js** fájlt az eszköz állapotának frissítéséhez. A GitHub [parancsfájlmappájában](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) láthatja az alapértelmezett szimulált eszközök JavaScript-fájljait. Konvenció szerint ezek a JavaScript-fájlok utótag **-állapottal** rendelkeznek, hogy megkülönböztessék őket a metódusviselkedést megvalósító fájloktól.

## <a name="properties"></a>Tulajdonságok

A `Properties` séma szakasza határozza meg az eszköz által a megoldásnak jelentéseknek adott tulajdonságértékeket. Példa:

```json
"Properties": {
  "Type": "Elevator",
  "Location": "Building 2",
  "Latitude": 47.640792,
  "Longitude": -122.126258
}
```

Amikor a megoldás elindul, lekérdezi az összes szimulált eszközt, hogy a felhasználói felületen használandó `Type` értékek listáját építse fel. A megoldás `Latitude` a `Longitude` és a tulajdonságok segítségével adja hozzá az eszköz helyét az irányítópulton lévő térképhez.

## <a name="telemetry"></a>Telemetria

A `Telemetry` tömb felsorolja az összes telemetriai típust, amelyet a szimulált eszköz küld a megoldásnak.

A következő példa 10 másodpercenként json telemetriai üzenetet küld `floor`a , `vibration`és `temperature` adatokat küld a lift érzékelőiből:

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

`MessageTemplate`meghatározza a szimulált eszköz által küldött JSON-üzenet szerkezetét. A helyőrzők `MessageTemplate` a `${NAME}` szintaxist használják, ahol `NAME` az [eszközállapot-objektum](#simulation)kulcsa. A karakterláncokat meg kell adni, a számokat nem.

`MessageSchema`meghatározza a szimulált eszköz által küldött üzenet sémáját. Az üzenetséma is közzé van téve az IoT Hub, hogy a háttér-alkalmazások újra felhasználják az információkat a bejövő telemetriai adatok értelmezéséhez.

Jelenleg csak JSON-üzenetsémák at használhatja. A sémában felsorolt mezők a következő típusúak lehetnek:

* Objektum - szerializálva a JSON használatával
* Bináris - szerializált a base64 használatával
* Szöveg
* Logikai
* Egész szám
* Double
* DateTime

Telemetriai üzenetek küldéséhez különböző időközönként, adjon hozzá `Telemetry` több telemetriai típusok a tömbhöz. A következő példa 10 másodpercenként küldi a hőmérséklet- és páratartalom-adatokat, és percenként jelzi a fény állapotát:

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

## <a name="cloudtodevicemethods"></a>CloudToDeviceMethods (Felhőalapúeszköz-metódusok)

A szimulált eszköz válaszolhat az IoT hubról hívott felhőből az eszközre metódusokra. Az `CloudToDeviceMethods` eszközmodell sémafájljának szakasza:

* Azokat a módszereket határozza meg, amelyekre a szimulált eszköz válaszolhat.
* Azonosítja a végrehajtandó logikát tartalmazó JavaScript-fájlt.

A szimulált eszköz elküldi az általa támogatott metódusok listáját az IoT hubhoz, amelyhez csatlakozik.

Ha többet szeretne megtudni az eszköz működését megvalósító JavaScript-fájlról, [olvassa el Az eszközmodell viselkedésének ismertetése](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md)című témakört.

A következő példa három támogatott módszert és a metódusokat megvalósító JavaScript-fájlokat adja meg:

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

A GitHub [parancsfájlmappájában](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) láthatja az alapértelmezett szimulált eszközök JavaScript-fájljait. Konvenció szerint ezek a JavaScript-fájlok az utótag **-módszer** megkülönböztetni őket a fájlokat, amelyek végre állapot viselkedését.

## <a name="next-steps"></a>További lépések

Ez a cikk ismerteti, hogyan hozhat létre saját egyéni szimulált eszköz modell. Ez a cikk megmutatta, hogyan:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Szimulált eszközmodell definiálása JSON-fájl használatával
> * A szimulált eszköz tulajdonságainak megadása
> * Adja meg a szimulált eszköz által küldött telemetriai adatokat
> * Adja meg azokat a felhőből az eszközre irányuló metódusokat, amelyekre az eszköz válaszol

Most, hogy tudomást szerzett a JSON-sémáról, a javasolt következő lépés [a szimulált eszköz viselkedésének megvalósítása.](iot-accelerators-remote-monitoring-device-behavior.md)

A távoli figyelési megoldással kapcsolatos fejlesztői információk a következő témakörben találhatóak:

* [Fejlesztői referencia-útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Fejlesztői hibaelhárítási útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)
