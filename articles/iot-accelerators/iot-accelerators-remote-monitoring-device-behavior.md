---
title: Szimulált eszköz távoli figyelési megoldásban – Azure | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használható a JavaScript a szimulált eszközök viselkedésének megadására a távoli figyelési megoldásban.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: c39ca0a018bd22844cf7e5350e6d3586319aac16
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "73890849"
---
# <a name="implement-the-device-model-behavior"></a>Az eszköz modellje működésének megvalósítása

A cikk az [eszköz modell sémáját](iot-accelerators-remote-monitoring-device-schema.md) ismerteti, amely a szimulált eszköz modelljét definiáló sémát írja le. Ez a cikk két, a szimulált eszköz viselkedését megvalósító JavaScript-fájltípust említett:

- **Állapot** Az eszköz belső állapotának frissítéséhez rögzített időközönként futtatott JavaScript-fájlok.
- **Metódus** JavaScript-fájlok, amelyek akkor futnak, amikor a megoldás meghívja a metódust az eszközön.

> [!NOTE]
> Az eszköz modellje csak az eszköz szimulációs szolgáltatásában üzemeltetett szimulált eszközökön használható. Ha valódi eszközt szeretne létrehozni, tekintse meg [az eszköz csatlakoztatása a távoli figyelési megoldáshoz gyorssegédet](iot-accelerators-connecting-devices.md).

Ebben a cikkben az alábbiakkal ismerkedhet meg:

>[!div class="checklist"]
> * Szimulált eszköz állapotának szabályozása
> * Annak meghatározása, hogy egy szimulált eszköz hogyan válaszol a távoli figyelési megoldástól kapott metódusokra
> * Parancsfájlok hibakeresése

## <a name="state-behavior"></a>Állapot viselkedése

Az eszköz modell sémájának [szimulációs](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#simulation) szakasza egy szimulált eszköz belső állapotát határozza meg:

- `InitialState`meghatározza az eszköz állapot objektumának összes tulajdonságának kezdeti értékeit.
- `Script`egy ütemezett JavaScript-fájlt azonosít, amely frissíti az eszköz állapotát.

Az alábbi példa egy szimulált hűtő eszköz eszköz állapot objektumának definícióját mutatja be:

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
  "Interval": "00:00:05",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

A szimulált eszköznek a szakaszban meghatározott állapotát a `InitialState` szimulációs szolgáltatás a memóriában tartja. Az állapotadatokat a rendszer a `main` **chiller-01-state.jsban **definiált függvény bemenetként adja át. Ebben a példában a szimulációs szolgáltatás öt másodpercenként futtatja a **chiller-01-state.js** fájlt. A parancsfájl módosíthatja a szimulált eszköz állapotát.

Az alábbi ábrán egy tipikus függvény körvonala látható `main` :

```javascript
function main(context, previousState, previousProperties) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

A `context` paraméter a következő tulajdonságokkal rendelkezik:

- `currentTime`formátummal rendelkező sztring`yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, például:`Simulated.Chiller.123`
- `deviceModel`, például:`Chiller`

A `state` paraméter a Device szimulációs szolgáltatás által karbantartott eszköz állapotát tartalmazza. Ez az érték az `state` előző hívás által visszaadott objektum `main` .

Az alábbi példa bemutatja, hogyan `main` kezelhető az eszköz állapota a szimulációs szolgáltatás által karbantartott módon:

```javascript
// Default state
var state = {
  online: true,
  temperature: 75.0,
  temperature_unit: "F",
  humidity: 70.0,
  humidity_unit: "%",
  pressure: 150.0,
  pressure_unit: "psig",
  simulation_state: "normal_pressure"
};

function restoreState(previousState) {
  // If the previous state is null, force a default state
  if (previousState !== undefined && previousState !== null) {
      state = previousState;
  } else {
      log("Using default state");
  }
}

function main(context, previousState, previousProperties) {

  restoreState(previousState);

  // Update state

  return state;
}
```

Az alábbi példa azt szemlélteti, hogy a `main` metódus hogyan szimulálhatja a telemetria értékeket, amelyek az idő múlásával változnak:

```javascript
/**
 * Simple formula generating a random value
 * around the average and between min and max
 */
function vary(avg, percentage, min, max) {
  var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
  value = Math.max(value, min);
  value = Math.min(value, max);
  return value;
}


function main(context, previousState, previousProperties) {

    restoreState(previousState);

    // 75F +/- 5%,  Min 25F, Max 100F
    state.temperature = vary(75, 5, 25, 100);

    // 70% +/- 5%,  Min 2%, Max 99%
    state.humidity = vary(70, 5, 2, 99);

    log("Simulation state: " + state.simulation_state);
    if (state.simulation_state === "high_pressure") {
        // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
        state.pressure = vary(250, 25, 50, 300);
    } else {
        // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
        state.pressure = vary(150, 10, 50, 300);
    }

    return state;
}
```

A teljes [chiller-01-state.js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js) megtekintheti a githubon.

## <a name="method-behavior"></a>Metódus viselkedése

Az [CloudToDeviceMethods](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#cloudtodevicemethods) szakasza határozza meg a szimulált eszköz által válaszoló metódusokat.

A következő példa egy szimulált hűtő eszköz által támogatott metódusok listáját mutatja be:

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
  },
  "FirmwareUpdate": {
    "Type": "javascript",
    "Path": "FirmwareUpdate-method.js"
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

Minden metódushoz tartozik egy társított JavaScript-fájl, amely megvalósítja a metódus viselkedését.

A szimulált eszköznek a séma szakaszában meghatározott állapotát a `InitialState` szimulációs szolgáltatás a memóriában tartja. Az állapotadatok a `main` JavaScript-fájlban megadott függvény bemenetként továbbítódnak a metódus hívásakor. A parancsfájl módosíthatja a szimulált eszköz állapotát.

Az alábbi ábrán egy tipikus függvény körvonala látható `main` :

```javascript
function main(context, previousState, previousProperties) {

}
```

A `context` paraméter a következő tulajdonságokkal rendelkezik:

- `currentTime`formátummal rendelkező sztring`yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, például:`Simulated.Chiller.123`
- `deviceModel`, például:`Chiller`

A `state` paraméter a Device szimulációs szolgáltatás által karbantartott eszköz állapotát tartalmazza.

A `properties` paraméter tartalmazza a jelentett tulajdonságokként írt eszköz tulajdonságait a IoT hub Device Twin.

A metódus működésének megvalósításához három globális funkció használható:

- `updateState`a szimulációs szolgáltatás által őrzött állapot frissítése.
- `updateProperty`egyetlen eszköz tulajdonságának frissítése.
- `sleep`a végrehajtás szüneteltetése egy hosszan futó feladat szimulálása érdekében.

Az alábbi példa a szimulált Chiller-eszközök által használt **IncreasePressure-method.js** szkript rövidített verzióját mutatja be:

```javascript
function main(context, previousState, previousProperties) {

    log("Starting 'Increase Pressure' method simulation (5 seconds)");

    // Pause the simulation and change the simulation mode so that the
    // temperature will fluctuate at ~250 when it resumes
    var state = {
        simulation_state: "high_pressure",
        CalculateRandomizedTelemetry: false
    };
    updateState(state);

    // Increase
    state.pressure = 210;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Increase
    state.pressure = 250;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Resume the simulation
    state.CalculateRandomizedTelemetry = true;
    updateState(state);

    log("'Increase Pressure' method simulation completed");
}
```

## <a name="debugging-script-files"></a>Parancsfájlok hibakeresése

Az eszköz szimulációs szolgáltatása által használt JavaScript-tolmácshoz nem lehet hibakeresőt csatolni az állapot és a metódus parancsfájljainak futtatásához. A szolgáltatás naplójában azonban adatokat is naplózhat. A beépített `log()` funkció lehetővé teszi az adatok mentését a funkciók nyomon követéséhez és hibakereséséhez.

Ha szintaktikai hiba történt, a értelmező hibát jelez, és egy `Jint.Runtime.JavaScriptException` bejegyzést ír a szolgáltatás naplójába.

A [szolgáltatás helyi futtatása](https://github.com/Azure/device-simulation-dotnet#running-the-service-locally-eg-for-development-tasks) a githubon című cikk bemutatja, hogyan futtathatja helyileg az eszköz-szimulációs szolgáltatást. A szolgáltatás helyi futtatása megkönnyíti a szimulált eszközök hibakeresését a felhőbe való üzembe helyezés előtt.

## <a name="next-steps"></a>További lépések

Ez a cikk azt ismerteti, hogyan határozható meg a saját egyéni szimulált eszköz modellje. Ez a cikk a következőket mutatja be:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Szimulált eszköz állapotának szabályozása
> * Annak meghatározása, hogy egy szimulált eszköz hogyan válaszol a távoli figyelési megoldástól kapott metódusokra
> * Parancsfájlok hibakeresése

Most, hogy megismerte, hogyan határozhatja meg egy szimulált eszköz viselkedését, a javasolt következő lépés a [szimulált eszköz létrehozásának](iot-accelerators-remote-monitoring-create-simulated-device.md)megismerése.

A távoli figyelési megoldással kapcsolatos további információkért lásd:

* [Fejlesztői referencia-útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Fejlesztői hibaelhárítási útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
