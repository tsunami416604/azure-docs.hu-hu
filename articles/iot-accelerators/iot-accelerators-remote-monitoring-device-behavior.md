---
title: A szimulált eszköz viselkedésének a távoli figyelési megoldás – Azure |} A Microsoft Docs
description: Ez a cikk ismerteti a távoli figyelési megoldásban a szimulált eszköz viselkedésének megadása a JavaScript használatával.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: 04d2ad2f0e86ee977600af86a2ffd1e9d7680375
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65823420"
---
# <a name="implement-the-device-model-behavior"></a>Az eszközmodell viselkedésének megvalósítása

A cikk [az eszközmodell sémájának megismerése](iot-accelerators-remote-monitoring-device-schema.md) , amely meghatározza egy szimulált eszköz modellje sémáját. A cikkben kétféle típusú JavaScript-fájlt, amely egy szimulált eszköz viselkedésének megvalósítása lehet hivatkozni:

- **Állapot** JavaScript-fájlok, amelyek a belső állapotot, az eszköz frissítéséhez rögzített időközönként futnak.
- **Módszer** JavaScript-fájlok, amelyek futtatását, amikor a megoldás meghív egy metódust az eszközön.

> [!NOTE]
> Modell eszközműködés csak az eszköz szimulálása szolgáltatásban üzemeltetett szimulált eszközök vonatkoznak. Ha szeretne létrehozni egy igazi eszközön, tekintse meg [az eszköz csatlakoztatása a távoli figyelési megoldásgyorsító](iot-accelerators-connecting-devices.md).

Ebben a cikkben az alábbiakkal ismerkedhet meg:

>[!div class="checklist"]
> * Szimulált eszköz állapota
> * Szimulált eszköz reakciója egy metódust hívhat meg, a távoli figyelési megoldás meghatározása
> * A parancsfájlok hibakeresése

## <a name="state-behavior"></a>Állapot viselkedés

A [szimuláció](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#simulation) szakaszában az eszközmodell sémájának meghatározása egy szimulált eszközt a belső állapot:

- `InitialState` az eszköz állapota objektum összes tulajdonsága esetén a kezdeti értékeket határozza meg.
- `Script` azonosítja, hogy az állapot frissítéséhez ütemezés szerint futtatott JavaScript-fájlt.

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
  "Interval": "00:00:05",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

A szimulált eszközt, az állapota a `InitialState` szakaszban, a szimuláció szolgáltatás által a memóriában tárolja. Az állapotinformációkat átadott bemenetként a `main` meghatározott függvény **hűtő-01-state.js**. Ebben a példában a szimuláció szolgáltatás fut a **hűtő-01-state.js** öt másodpercenként fájlt. A parancsfájl módosításával a szimulált eszköz állapotáról.

A következő példa a röviden ismerteti a tipikus `main` függvény:

```javascript
function main(context, previousState, previousProperties) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

A `context` paraméter a következő tulajdonságokkal rendelkezik:

- `currentTime` egy karakterlánc formátumú `yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, például `Simulated.Chiller.123`
- `deviceModel`, például `Chiller`

A `state` paraméter tartalmazza az eszköz állapotát, az eszköz szimulálása szolgáltatás által vezetett. Ez az érték a `state` előző hívás által visszaadott objektum `main`.

Az alábbi példa bemutatja egy tipikus megvalósításában a `main` metódust a szimuláció szolgáltatás által kezelt eszköz állapota:

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

A következő példa bemutatja a `main` metódus szimulálhat telemetriaértékeket idővel változhatnak:

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

Megtekintheti, hogy a teljes [hűtő-01-state.js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js) a Githubon.

## <a name="method-behavior"></a>Módszer viselkedés

A [CloudToDeviceMethods](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#cloudtodevicemethods) az eszközmodell sémájának szakasza határozza meg a szimulált eszköz válaszol módszereket.

Az alábbi példa egy szimulált hűtő eszköz által támogatott módszerek listáját jeleníti meg:

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

Minden egyes módszernek egy kapcsolódó JavaScript-fájlt, amely megvalósítja a metódus viselkedését.

A szimulált eszközt, az állapota a `InitialState` szakasz a séma van a memóriában tárolja a szimuláció szolgáltatás. Az állapotinformációkat átadott bemenetként a `main` függvény definiálva a a JavaScript-fájlt, ha a metódus lehívásra kerül. A parancsfájl módosításával a szimulált eszköz állapotáról.

A következő példa a röviden ismerteti a tipikus `main` függvény:

```javascript
function main(context, previousState, previousProperties) {

}
```

A `context` paraméter a következő tulajdonságokkal rendelkezik:

- `currentTime` egy karakterlánc formátumú `yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, például `Simulated.Chiller.123`
- `deviceModel`, például `Chiller`

A `state` paraméter tartalmazza az eszköz állapotát, az eszköz szimulálása szolgáltatás által vezetett.

A `properties` paraméter az eszközt az IoT Hub ikereszköz jelentett tulajdonságokként írt tulajdonságait tartalmazza.

Nincsenek a metódus viselkedésének megvalósítása érdekében használható három globális függvények:

- `updateState` a szimuláció szolgáltatás által tárolt állapotban frissíteni.
- `updateProperty` egyetlen eszköztulajdonságon frissíteni.
- `sleep` a végrehajtás egy hosszan futó feladatot szimulálásához szüneteltetéséhez.

Az alábbi példa rövidített verzióját mutatja a **IncreasePressure-method.js** a szimulált hűtő eszköz által használt parancsfájl:

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

Nem alkalmas csatoljon egy hibakeresőt a Javascript állapotot és metódus parancsfájlok futtatása az eszköz szimulálása szolgáltatás által használt értelmezője számára készült. Azonban információkat is bejelentkezhetnek a napló. A beépített `log()` funkció lehetővé teszi, nyomon követése és hibakeresése a függvény végrehajtási adatok mentéséhez.

Ha nem sikerül a értelmezője számára készült, szintaktikai hiba van, és írja a `Jint.Runtime.JavaScriptException` a szolgáltatás bejelentkezési bejegyzést.

A [helyben fut a szolgáltatás](https://github.com/Azure/device-simulation-dotnet#running-the-service-locally-eg-for-development-tasks) a Githubon a cikk bemutatja, hogyan helyileg történő futtatása az eszköz szimulálása szolgáltatás. A szolgáltatás helyben fut megkönnyíti a szimulált eszközök hibakeresése a felhőbe való központi telepítésük előtt.

## <a name="next-steps"></a>További lépések

Ez a cikk ismerteti a saját egyéni szimulált eszközmodell viselkedésének definiálása. Ez a cikk bemutatta, hogyan való:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Szimulált eszköz állapota
> * Szimulált eszköz reakciója egy metódust hívhat meg, a távoli figyelési megoldás meghatározása
> * A parancsfájlok hibakeresése

Most, hogy megismerte a szimulált eszköz viselkedésének megadása, a javasolt következő lépésre megtudhatja, hogyan [egy szimulált eszköz létrehozása](iot-accelerators-remote-monitoring-create-simulated-device.md).

A távoli figyelési megoldás fejlesztői ismertetését lásd:

* [Fejlesztői referencia-útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Fejlesztői hibaelhárítási útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
