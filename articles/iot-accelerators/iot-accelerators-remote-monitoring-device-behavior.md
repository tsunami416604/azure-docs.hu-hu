---
title: Szimulált eszköz a távoli figyelési megoldásban - Azure | Microsoft dokumentumok
description: Ez a cikk azt ismerteti, hogyan használhatja a JavaScript et a szimulált eszközök viselkedésének meghatározására a távoli figyelési megoldásban.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: c39ca0a018bd22844cf7e5350e6d3586319aac16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890849"
---
# <a name="implement-the-device-model-behavior"></a>Az eszközmodell viselkedésének megvalósítása

A cikk [Ismerje meg az eszközmodell sémáját,](iot-accelerators-remote-monitoring-device-schema.md) amely a szimulált eszközmodellt meghatározó sémát ismerte el. Ez a cikk a szimulált eszközök viselkedését megvalósító JavaScript-fájlok két típusára vonatkozott:

- **Állam** Rögzített időközönként futtatott JavaScript-fájlok az eszköz belső állapotának frissítéséhez.
- **Módszer** JavaScript-fájlok, amelyek akkor futnak, amikor a megoldás metódust hív meg az eszközön.

> [!NOTE]
> Az eszközmodell viselkedése csak az eszközszimulációs szolgáltatásban tárolt szimulált eszközökre szolgál. Ha valódi eszközt szeretne létrehozni, olvassa el [Az eszköz csatlakoztatása a távfigyelési megoldás gyorsítóhoz című témakört.](iot-accelerators-connecting-devices.md)

Ebben a cikkben az alábbiakkal ismerkedhet meg:

>[!div class="checklist"]
> * Szimulált eszköz állapotának szabályozása
> * Annak meghatározása, hogy a szimulált eszköz hogyan reagáljon a távfigyelési megoldásból érkező metódushívásra
> * A parancsfájlok hibakeresése

## <a name="state-behavior"></a>Állapot viselkedése

Az eszközmodell sémájának [Szimuláció szakasza](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#simulation) határozza meg a szimulált eszköz belső állapotát:

- `InitialState`az eszközállapot-objektum összes tulajdonságának kezdeti értékeit határozza meg.
- `Script`azonosítja az eszközállapotának frissítéséhez ütemezett JavaScript-fájlt.

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
  "Interval": "00:00:05",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

A szimulált eszköz állapotát a `InitialState` szakaszban meghatározottak szerint a szimulációs szolgáltatás a memóriában tartja. Az állapotadatok bemenetként kerül `main` átadásra a **chiller-01-state.js függvényben**definiált függvényhez. Ebben a példában a szimulációs szolgáltatás öt másodpercenként futtatja a **chiller-01-state.js** fájlt. A parancsfájl módosíthatja a szimulált eszköz állapotát.

Az alábbiakban egy tipikus `main` függvény körvonalait mutatják be:

```javascript
function main(context, previousState, previousProperties) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

A `context` paraméter a következő tulajdonságokkal rendelkezik:

- `currentTime`formátumú karakterláncként`yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, például`Simulated.Chiller.123`
- `deviceModel`, például`Chiller`

A `state` paraméter az eszköz szimulációs szolgáltatás által karbantartott állapotát tartalmazza. Ez az `state` érték az az objektum, amelyet az előző hívás visszaadott a programnak. `main`

A következő példa a `main` szimulációs szolgáltatás által fenntartott eszközállapot kezelésére használt módszer tipikus megvalósítását mutatja be:

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

A következő példa `main` bemutatja, hogyan szimulálhatja a módszer az idő múlásával változó telemetriai értékeket:

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

Megtekintheti a teljes [chiller-01-state.js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js) a GitHubon.

## <a name="method-behavior"></a>Metódus viselkedése

Az eszközmodell sémájának [CloudToDeviceMethods](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#cloudtodevicemethods) szakasza határozza meg, hogy a szimulált eszköz nek a szimulált eszköz nek miként válaszol.

A következő példa a szimulált hűtőberendezés által támogatott módszerek listáját mutatja be:

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

Minden módszerhez tartozik egy JavaScript-fájl, amely megvalósítja a metódus viselkedését.

A szimulált eszköz állapotát a `InitialState` séma szakaszában meghatározottak szerint a szimulációs szolgáltatás a memóriában tartja. Az állapotadatok a metódus `main` megnevezésekekénél a JavaScript-fájlban definiált függvény bemeneteként lesznek továbbítva. A parancsfájl módosíthatja a szimulált eszköz állapotát.

Az alábbiakban egy tipikus `main` függvény körvonalait mutatják be:

```javascript
function main(context, previousState, previousProperties) {

}
```

A `context` paraméter a következő tulajdonságokkal rendelkezik:

- `currentTime`formátumú karakterláncként`yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, például`Simulated.Chiller.123`
- `deviceModel`, például`Chiller`

A `state` paraméter az eszköz szimulációs szolgáltatás által karbantartott állapotát tartalmazza.

A `properties` paraméter tartalmazza az eszköz tulajdonságait, amelyek az IoT Hub-eszköz ikereszközjelentett tulajdonságaiként vannak írva.

A módszer működésének megvalósításához három globális függvény használható:

- `updateState`a szimulációs szolgáltatás által birtokolt állapot frissítéséhez.
- `updateProperty`egyetlen eszköztulajdonság frissítéséhez.
- `sleep`a végrehajtás szüneteltetéséhez egy hosszú ideig futó feladat szimulálása érdekében.

A következő példa a szimulált hűtőeszközök által használt **IncreasePressure-method.js** parancsfájl rövidített verzióját mutatja be:

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

Nem lehet hibakeresőt csatolni az eszközszimulációs szolgáltatás által az állapot- és metódusparancsfájlok futtatásához használt Javascript-értelmezőhöz. A szolgáltatásnaplóba azonban naplózhatja az adatokat. A beépített `log()` funkció lehetővé teszi az adatok mentését a függvényvégrehajtásának nyomon követéséhez és hibakereséséhez.

Szintaktikai hiba esetén az értelmező meghibásodik, és bejegyzést `Jint.Runtime.JavaScriptException` ír a szerviznaplóba.

A [szolgáltatás helyi futtatásáról](https://github.com/Azure/device-simulation-dotnet#running-the-service-locally-eg-for-development-tasks) szóló cikk a GitHubon bemutatja, hogyan futtathatja az eszközszimulációs szolgáltatást helyileg. A szolgáltatás helyi futtatása megkönnyíti a szimulált eszközök hibakeresését, mielőtt üzembe helyezne őket a felhőbe.

## <a name="next-steps"></a>További lépések

Ez a cikk ismerteti, hogyan határozhatja meg a viselkedését a saját egyéni szimulált eszköz modell. Ez a cikk megmutatta, hogyan:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Szimulált eszköz állapotának szabályozása
> * Annak meghatározása, hogy a szimulált eszköz hogyan reagáljon a távfigyelési megoldásból érkező metódushívásra
> * A parancsfájlok hibakeresése

Most, hogy megtanulta, hogyan adhatja meg egy szimulált eszköz viselkedését, a javasolt következő lépés a [szimulált eszköz létrehozásának megismerése.](iot-accelerators-remote-monitoring-create-simulated-device.md)

A távoli figyelési megoldással kapcsolatos fejlesztői információk a következő témakörben találhatóak:

* [Fejlesztői referencia-útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Fejlesztői hibaelhárítási útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
