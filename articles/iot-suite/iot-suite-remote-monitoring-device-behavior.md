---
title: "A távoli felügyeleti megoldás - Azure eszközviselkedés szimulált |} Microsoft Docs"
description: "Ez a cikk ismerteti a szimulált eszköz viselkedésének meghatározása a távoli felügyeleti megoldás a JavaScript használatával."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/29/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: e5846893166c3e65b75e84d02849c2b8ab78e079
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="implement-the-device-model-behavior"></a>Az eszköz modell viselkedés

A cikk [megérteni az eszköz modellsémát](iot-suite-remote-monitoring-device-schema.md) leírt a séma, amely a szimulált eszköz modelljét határozza meg. Kétféle JavaScript-fájlt, amely a szimulált eszköz viselkedés, ha a cikkben említett:

- **Állapot** JavaScript-fájlokat, amelyek a belső állapotot, az eszköz frissítése rögzített időközönként futnak.
- **Módszer** JavaScript fájlok futtatását, amikor a megoldás hív meg, egy metódust az eszközön.

Ebből a cikkből megismerheti, hogyan:

>[!div class="checklist"]
> * A szimulált eszköz állapotának ellenőrzése
> * Adja meg, hogyan felelnek meg a szimulált eszköz reponds metódusra, és a távoli felügyeleti megoldás
> * A parancsfájlok hibakeresése

## <a name="state-behavior"></a>Állapot viselkedése

A [szimuláció](iot-suite-remote-monitoring-device-schema.md#simulation) eszköz modellsémát szakasza határozza meg a belső állapotot, a szimulált eszköz:

- `InitialState`az objektum összes tulajdonságának értéke az eszköz állapota kezdeti értékeinek meghatározása.
- `Script`a JavaScript-fájlt, amely az eszköz állapotát frissítése ütemezés szerint futtatott azonosítja.

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
  "Script": {
    "Type": "javascript",
    "Path": "chiller-01-state.js",
    "Interval": "00:00:05"
  }
}
```

A szimulált eszköz, az állapota a `InitialState` területen van a szimuláció szolgáltatás a memóriában tartja. Az állapotadatokat az kapott bemenetként a `main` definiált függvény **hűtő-01-state.js**. Ebben a példában a szimuláció szolgáltatás fut a **hűtő-01-state.js** öt másodpercenként fájlt. A parancsfájl módosításával a szimulált eszköz állapotát.

A következő példa egy tipikus vázlat `main` függvény:

```javascript
function main(context, previousState) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

A `context` paraméter a következő tulajdonságokkal rendelkeznek:

- `currentTime`formátummal karakterláncként`yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, például`Simulated.Chiller.123`
- `deviceModel`, például`Chiller`

A `state` a paraméter tartalmazza az eszköz állapotát, az eszköz szimuláció szolgáltatás által kezelt. Ez az érték a `state` a korábbi hívás által visszaadott objektum `main`.

A következő példa bemutatja egy tipikus megvalósításában a `main` metódust a szimuláció szolgáltatás által kezelt eszköz állapota:

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

function main(context, previousState) {

  restoreState(previousState);

  // Update state

  return state;
}
```

A következő példa bemutatja a `main` metódus szimulálhatja telemetriai értékek, amelyek módosítják a idővel:

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


function main(context, previousState) {

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

Megtekintheti a teljes [hűtő-01-state.js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js) a Githubon.

## <a name="method-behavior"></a>Módszer viselkedése

A [CloudToDeviceMethods](iot-suite-remote-monitoring-device-schema.md#cloudtodevicemethods) eszköz modellsémát szakasza határozza meg a módszereket, a szimulált eszköz válaszol-e.

A következő példa egy szimulált hűtő eszköz által támogatott módszerek listáját jeleníti meg:

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

Minden egyes metódusnak egy kapcsolódó JavaScript-fájlt a metódus viselkedését.

A szimulált eszköz, az állapota a `InitialState` szakasz a séma birtokolja a memóriában a szimuláció szolgáltatás. Az állapotadatokat az kapott bemenetként a `main` metódus hívásakor a JavaScript-fájl definiált függvény. A parancsfájl módosításával a szimulált eszköz állapotát.

A következő példa egy tipikus vázlat `main` függvény:

```javascript
function main(context, previousState) {

}
```

A `context` paraméter a következő tulajdonságokkal rendelkeznek:

- `currentTime`formátummal karakterláncként`yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, például`Simulated.Chiller.123`
- `deviceModel`, például`Chiller`

A `state` a paraméter tartalmazza az eszköz állapotát, az eszköz szimuláció szolgáltatás által kezelt.

A metódus viselkedés érdekében használható két globális függvények van:

- `updateState`a szimuláció szolgáltatás tartja az állapot frissítésére.
- `sleep`szimulálása hosszan futó feladat végrehajtásának felfüggesztése.

A következő példa bemutatja egy rövidített a **IncreasePressure-method.js** a szimulált hűtő eszköz által használt parancsfájlt:

```javascript
function main(context, previousState) {

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

## <a name="debugging-script-files"></a>Parancsprogram-hibakeresés

Nincs lehetőség a hibakereső csatolása a Javascript értelmező állapotát és metódus parancsfájlok futtatása az eszköz szimuláció szolgáltatás által használt. Azonban a szolgáltatás bejelentkezési adatokat is bejelentkezhetnek. A beépített `log()` funkció lehetővé teszi a menteni nyomon követése és hibakeresése függvény végrehajtása adatait.

Ha nem sikerül a értelmező, szintaktikai hiba van, és írja a `Jint.Runtime.JavaScriptException` a szolgáltatás bejelentkezési bejegyzést.

A [a szimulált eszköz létrehozásához](iot-suite-remote-monitoring-test.md) cikkből megtudhatja, hogyan az szimuláció szolgáltatást helyi futtatásához. Helyben fut a szolgáltatás révén egyszerűbben debug a szimulált eszköz a felhőre központi telepítésük előtt.

## <a name="next-steps"></a>További lépések

Ez a cikk ismerteti a saját egyéni szimulált eszköz modell viselkedését definiálása. Ez a cikk bemutatta, hogyan számára:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * A szimulált eszköz állapotának ellenőrzése
> * Adja meg, hogyan felelnek meg a szimulált eszköz reponds metódusra, és a távoli felügyeleti megoldás
> * A parancsfájlok hibakeresése

Most már rendelkezik megtudta, hogyan adja meg, a szimulált eszköz, a javasolt következő lépésre megtudhatja, hogyan [a szimulált eszköz létrehozásához](iot-suite-remote-monitoring-test.md).

Fejlesztői kapcsolatos további információkért a távoli felügyeleti megoldás az alábbi témakörben talál:

* [Fejlesztői referencia-útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Fejlesztői hibaelhárítási útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->