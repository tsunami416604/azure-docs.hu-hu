---
title: Speciális szimulált eszközmodell létrehozása - Azure| Microsoft dokumentumok
description: Ebben az útmutatóútmutatóban megtudhatja, hogyan hozhat létre egy speciális eszközmodellt az eszközszimulációs megoldásgyorsítóhoz.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: troyhop
ms.custom:
- mvc
- amqp
- mqtt
ms.openlocfilehash: c568dddcbbf57ebd6ed5906bb83af01a84dafa41
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683831"
---
# <a name="create-an-advanced-device-model"></a>Speciális eszközmodell létrehozása

Ez az útmutató ismerteti az egyéni eszközmodellt meghatározó JSON- és JavaScript-fájlokat. A cikk tartalmaz néhány mintaeszköz modell definíciófájlokat, és bemutatja, hogyan töltheti fel őket az eszközszimulációs példányba. Speciális eszközmodelleket hozhat létre, hogy valósághűbb eszközviselkedéseket szimuláljon a teszteléshez.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató lépései végrehajtásához az Azure-előfizetésben az Eszközszimuláció üzembe helyezett példányára van szükség.

Ha még nem helyezte üzembe az Eszközszimulációt, végezze el az [Azure-beli IoT-eszközszimuláció üzembe helyezését és futtatását](quickstart-device-simulation-deploy.md) ismertető rövid útmutatóban leírt lépéseket.

### <a name="open-device-simulation"></a>Eszközszimuláció megnyitása

Az Eszközszimuláció böngészőben történő futtatásához először lépjen a [Microsoft Azure IoT-megoldásgyorsítók](https://www.azureiotsolutions.com) területre.

A rendszer arra kérheti, hogy lépjen be Azure-előfizetésének hitelesítő adataival.

Ezután kattintson a **Bevezetés** gombra a telepítésben üzembe helyezett eszközszimulációcsempe-csempén, [és futtasson egy IoT-eszközszimulációt](quickstart-device-simulation-deploy.md) az Azure-ban.

## <a name="device-models"></a>Eszközmodellek

Minden szimulált eszköz egy adott eszközmodellhez tartozik, amely meghatározza a szimuláció viselkedését. Ez a viselkedés magában foglalja, hogy milyen gyakran kell telemetriai adatokat küldeni, milyen típusú üzeneteket küldeni, és a támogatott módszerek.

Az eszközmodellt JSON-eszközdefiníciós fájl és JavaScript-fájlok készletével határozhatja meg. Ezek a JavaScript-fájlok határozzák meg a szimulációs viselkedést, például a véletlenszerű telemetriai adatokat és a metódus logikáját.

Egy tipikus eszközmodell:

* Minden eszközmodellhez egy JSON-fájl (például lift.json).
* Minden eszközmodellhez egy JavaScript viselkedésparancsfájl (például lift-state.js)
* Minden eszközmetódushoz egy JavaScript metódus parancsfájl (például lift-go-down.js)

> [!NOTE]
> Nem minden eszközmodell definiál metódust. Ezért előfordulhat, hogy egy eszközmodell rendelkezik metódusparancsfájlokkal. Azonban minden eszközmodellnek rendelkeznie kell viselkedési parancsfájllal.

## <a name="device-definition-file"></a>Eszközdefiníciós fájl

Minden eszközdefiníciós fájl egy szimulált eszközmodell adatait tartalmazza, beleértve a következő információkat:

* Eszközmodell neve: karakterlánc.
* Protokoll: AMQP | MQTT | HTTP.
* A kezdeti eszközállapot.
* Milyen gyakran frissíti az eszköz állapotát.
* Milyen JavaScript-fájlt használ az eszköz állapotának frissítéséhez.
* Az elküldésre küldő telemetriai üzenetek listája, amelyek mindegyike egy adott gyakorisággal van.
* A telemetriai üzenetek sémája, amelyet a háttéralkalmazás használ a fogadott telemetriai adatok elemzéséhez.
* A támogatott módszerek listája és az egyes módszerek szimulálására használt JavaScript-fájl.

### <a name="file-schema"></a>Fájlséma

A séma verziója mindig "1.0.0", és a fájl formátumára jellemző:

```json
"SchemaVersion": "1.0.0"
```

### <a name="device-model-description"></a>Eszközmodell leírása

Az alábbi tulajdonságok az eszközmodellt ismertetik. Minden típus egyedi azonosítóval, szemantikai verzióval, névvel és leírással rendelkezik:

```json
"Id": "chiller-01",
"Version": "0.0.1",
"Name": "Chiller",
"Description": "Chiller with external temperature and humidity sensors."
```

### <a name="iot-protocol"></a>IoT protokoll

Az IoT-eszközök különböző protokollok használatával csatlakozhatnak. A szimuláció lehetővé teszi **az AMQP,** **MQTT**vagy **HTTP**:

```json
"Protocol": "AMQP"
```

### <a name="simulated-device-state"></a>Szimulált eszközállapota

Minden szimulált eszköz belső állapottal rendelkezik, amelyet meg kell határozni. Az állapot is meghatározza a telemetriai adatokban jelenthető tulajdonságokat. Egy hűtő kezdeti állapota például a következő lehet:

```json
"InitialState": {
    "temperature": 50,
    "humidity": 40
},
```

Egy több érzékelővel rendelkező mozgó eszköz több tulajdonsággal is rendelkezhet, például:

```json
"InitialState": {
    "latitude": 47.445301,
    "longitude": -122.296307,
    "speed": 30.0,
    "speed_unit": "mph",
    "cargotemperature": 38.0,
    "cargotemperature_unit": "F"
}
```

Az eszközállapotát a szimulációs szolgáltatás őrzi a memóriában, és a JavaScript-függvény bemeneteként adja meg. A JavaScript függvény dönthet a következőkről:

* Az állapot figyelmen kívül hagyása és véletlenszerű adatok létrehozása.
* Az eszköz állapotának frissítése valamilyen reális módon egy adott forgatókönyvhöz.

Az állapotot generáló függvény bemenetként is kap:

* Az eszköz azonosítója.
* Az eszköz modellje.
* Az aktuális idő. Ez az érték lehetővé teszi különböző adatok generálását eszköz és idő szerint.

### <a name="generating-telemetry-messages"></a>Telemetriai üzenetek létrehozása

A szimulációs szolgáltatás minden eszközhöz több telemetriai típust küldhet. Általában telemetriai adatokat az eszköz állapota tartalmazza. Egy szimulált helyiség például 10 másodpercenként küldhet adatokat a hőmérsékletről és a páratartalomról. Vegye figyelembe a helyőrzőket a következő kódrészletben, amelyet a rendszer automatikusan lecserél az eszköz állapotából származó értékekre:

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
    }
],
```

A helyőrzők egy **speciális ${NAME}** szintaxist **használnak,** ahol a NAME a JavaScript **fő** függvény által visszaadott eszközállapot-objektum kulcsa. A karakterláncokat meg kell adni, míg a számokat nem.

#### <a name="message-schema"></a>Üzenetséma

Minden üzenettípusnak jól definiált sémával kell rendelkeznie. Az üzenetséma is közzé van téve az IoT Hubon, így a háttéralkalmazások újra felhasználhatják az információkat a bejövő telemetriai adatok értelmezéséhez.

A séma támogatja a JSON formátumot, amely lehetővé teszi a könnyű elemzés, átalakítás és elemzés, több rendszeren és szolgáltatáson keresztül.

A sémában felsorolt mezők a következő típusúak lehetnek:

* Objektum - szerializálva a JSON használatával
* Bináris - szerializált a base64 használatával
* Szöveg
* Logikai
* Egész szám
* Double
* DateTime

### <a name="supported-methods"></a>Támogatott módszerek

A szimulált eszközök is reagálhatnak a metódushívásokra, ebben az esetben valamilyen logikát hajtanak végre, és valamilyen választ adnak. A szimulációhoz hasonlóan a metóduslogika egy JavaScript-fájlban tárolódik, és kölcsönhatásba léphet az eszköz állapotával. Például:

```json
"CloudToDeviceMethods": {
    "Start": {
        "Type": "JavaScript",
        "Path": "release-pressure.js"
    }
}
```

## <a name="create-a-device-definition-file"></a>Eszközdefiníciós fájl létrehozása

Ebben az útmutatóban láthatja, hogyan hozhat létre egy eszközmodellt egy drónhoz. A drón véletlenszerűen fog repülni a kezdeti koordináták körül, megváltoztatva a helyet és a magasságot.

Másolja a következő JSON-t egy szövegszerkesztőbe, és mentse **el drone.json néven.**

### <a name="device-definition-json-example"></a>Példa a JSON-definícióra

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "drone",
  "Version": "0.0.1",
  "Name": "Drone",
  "Description": "Simple drone.",
  "Protocol": "AMQP",
  "Simulation": {
    "InitialState": {
      "velocity": 0.0,
      "velocity_unit": "mm/sec",
      "acceleration": 0.0,
      "acceleration_unit": "mm/sec^2",
      "latitude": 47.476075,
      "longitude": -122.192026,
      "altitude": 0.0
    },
    "Interval": "00:00:05",
    "Scripts": [{
      "Type": "JavaScript",
      "Path": "drone-state.js"
    }]
  },
  "Properties": {
    "Type": "Drone",
    "Firmware": "1.0",
    "Model": "P-96"
  },
  "Tags": {
    "Owner": "Contoso"
  },
  "Telemetry": [{
      "Interval": "00:00:05",
      "MessageTemplate": "{\"velocity\":\"${velocity}\",\"acceleration\":\"${acceleration}\",\"position\":\"${latitude}|${longitude}|${altitude}\"}",
      "MessageSchema": {
        "Name": "drone-event-sensor;v1",
        "Format": "JSON",
        "Fields": {
          "velocity": "double",
          "velocity_unit": "text",
          "acceleration": "double",
          "acceleration_unit": "text",
          "latitude": "double",
          "longitude": "double",
          "altitude": "double"
        }
      }
    }
  ],
    "CloudToDeviceMethods": {
        "RecallDrone": {
            "Type": "JavaScript",
            "Path": "droneRecall-method.js"
        }
    }
}
```

## <a name="behavior-script-files"></a>Viselkedési parancsfájlok

A viselkedési parancsfájlban lévő kód áthelyezi a drónt. A parancsfájl megváltoztatja a drón magasságát és helyét azáltal, hogy manipulálja az eszköz memória állapotban van.

A JavaScript fájloknak **rendelkezniük** kell egy fő funkcióval, amely két paramétert fogad el:

* Három tulajdonságot tartalmazó **környezeti** objektum:
    * **currentTime,** mint egy karakterlánc formátumban **yyy-MM-dd'T'Hh:mm:sszzz**.
    * **deviceId**. Például **simulated.Elevator.123**.
    * **deviceModel .** **Például: Lift**.
* Olyan **állapotobjektum,** amely az előző hívásban a függvény által visszaadott érték. Ezt az eszközállapotot a szimulációs szolgáltatás tartja karban, és telemetriai üzenetek létrehozásához használja.

A **fő** függvény az új eszközállapotot adja vissza. Például:

```JavaScript
function main(context, state) {

    // Use context if the simulation depends on
    // time or device details.
    // Execute some logic, updating 'state'

    return state;
}
```

## <a name="create-a-behavior-script-file"></a>Viselkedési parancsfájl létrehozása

Másolja a következő JavaScript-et egy szövegszerkesztőbe, és mentse **drone-state.js néven.**

### <a name="device-model-javascript-simulation-example"></a>Példa a JavaScript eszközmodellre

```JavaScript
"use strict";

// Position control
const DefaultLatitude = 47.476075;
const DefaultLongitude = -122.192026;
const DistanceVariation = 10;

// Altitude control
const DefaultAltitude = 0.0;
const AverageAltitude = 499.99;
const AltitudeVariation = 5;

// Velocity control
const AverageVelocity = 60.00;
const MinVelocity = 20.00;
const MaxVelocity = 120.00;
const VelocityVariation = 5;

// Acceleration control
const AverageAcceleration = 2.50;
const MinAcceleration = 0.01;
const MaxAcceleration = 9.99;
const AccelerationVariation = 1;

// Display control for position and other attributes
const GeoSpatialPrecision = 6;
const DecimalPrecision = 2;

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: DefaultLatitude,
    longitude: DefaultLongitude,
    altitude: DefaultAltitude
};

// Default device properties
var properties = {};

/**
 * Restore the global state using data from the previous iteration.
 *
 * @param previousState device state from the previous iteration
 * @param previousProperties device properties from the previous iteration
 */
function restoreSimulation(previousState, previousProperties) {
    // If the previous state is null, force a default state
    if (previousState) {
        state = previousState;
    } else {
        log("Using default state");
    }

    if (previousProperties) {
        properties = previousProperties;
    } else {
        log("Using default properties");
    }
}

/**
 * Simple formula generating a random value around the average
 * in between min and max
 */
function vary(avg, percentage, min, max) {
    var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
    value = Math.max(value, min);
    value = Math.min(value, max);
    return value;
}

/**
 * Entry point function called by the simulation engine.
 * Returns updated simulation state.
 * Device property updates must call updateProperties() to persist.
 *
 * @param context             The context contains current time, device model and id
 * @param previousState       The device state since the last iteration
 * @param previousProperties  The device properties since the last iteration
 */
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global state before generating the new telemetry, so that
    // the telemetry can apply changes using the previous function state.
    restoreSimulation(previousState, previousProperties);

    state.acceleration = vary(AverageAcceleration, AccelerationVariation, MinAcceleration, MaxAcceleration).toFixed(DecimalPrecision);
    state.velocity = vary(AverageVelocity, VelocityVariation, MinVelocity, MaxVelocity).toFixed(DecimalPrecision);

    // Use the last coordinates to calculate the next set with a given variation
    var coords = varylocation(Number(state.latitude), Number(state.longitude), DistanceVariation);
    state.latitude = Number(coords.latitude).toFixed(GeoSpatialPrecision);
    state.longitude = Number(coords.longitude).toFixed(GeoSpatialPrecision);

    // Fluctuate altitude between given variation constant by more or less
    state.altitude = vary(AverageAltitude, AltitudeVariation, AverageAltitude - AltitudeVariation, AverageAltitude + AltitudeVariation).toFixed(DecimalPrecision);

    return state;
}

/**
 * Generate a random geolocation at some distance (in miles)
 * from a given location
 */
function varylocation(latitude, longitude, distance) {
    // Convert to meters, use Earth radius, convert to radians
    var radians = (distance * 1609.344 / 6378137) * (180 / Math.PI);
    return {
        latitude: latitude + radians,
        longitude: longitude + radians / Math.cos(latitude * Math.PI / 180)
    };
}
```

## <a name="create-a-method-script-file"></a>Metódusparancsfájl létrehozása

A metódusparancsfájlok hasonlóak a viselkedési parancsfájlokhoz. Ezek határozzák meg az eszköz viselkedését, ha egy adott felhő-eszköz metódus neve.

A drón visszahívási parancsfájlja a drón koordinátáit egy rögzített pontra állítja be, hogy szimulálja a hazatérő drónt.

Másolja a következő JavaScript-et egy szövegszerkesztőbe, és mentse **el droneRecall-method.js néven.**

### <a name="device-model-javascript-simulation-example"></a>Példa a JavaScript eszközmodellre

```JavaScript
"use strict";

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: 0.0,
    longitude: 0.0,
    altitude: 0.0
};

// Default device properties
var properties = {};

/**
 * Restore the global state using data from the previous iteration.
 *
 * @param previousState device state from the previous iteration
 * @param previousProperties device properties from the previous iteration
 */
function restoreSimulation(previousState, previousProperties) {
    // If the previous state is null, force a default state
    if (previousState) {
        state = previousState;
    } else {
        log("Using default state");
    }

    if (previousProperties) {
        properties = previousProperties;
    } else {
        log("Using default properties");
    }
}

/**
 * Entry point function called by the simulation engine.
 *
 * @param context        The context contains current time, device model and id, not used
 * @param previousState  The device state since the last iteration, not used
 * @param previousProperties  The device properties since the last iteration
 */
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global device properties and the global state before
    // generating the new telemetry, so that the telemetry can apply changes
    // using the previous function state.
    restoreSimulation(previousState, previousProperties);

    //simulate the behavior of a drone when recalled
  state.latitude = 47.476075;
  state.longitude = -122.192026;
  return state;
}
```

## <a name="debugging-script-files"></a>Parancsfájlok hibakeresése

Bár nem csatolhat hibakeresőt egy futó viselkedésfájlhoz, a **naplófunkció** használatával adatokat írhat a szolgáltatásnaplóba. Szintaktikai hibák esetén az értelmező meghibásodik, és információt ír a kivételről a naplóba.

Példa naplózásra:

```JavaScript
function main(context, state) {

    log("This message will appear in the service logs.");

    log(context.deviceId);

    if (typeof(state) !== "undefined" && state !== null) {
        log("Previous value: " + state.temperature);
    }

    // ...

    return updateState;
}
```

## <a name="deploy-an-advanced-device-model"></a>Speciális eszközmodell telepítése

A speciális eszközmodell telepítéséhez töltse fel az eszközszimulációs példányfájljait:

A menüsávon válassza az **Eszközmodellek** gombot. Az **Eszközmodellek** lap felsorolja az eszközmodelleket, amelyek az Eszközszimuláció ezen példányában érhetők el:

![Eszközmodellek](media/iot-accelerators-device-simulation-advanced-device/devicemodelnav.png)

Kattintson az **+ Eszközmodellek hozzáadása** elemre a lap jobb felső sarkában:

![Eszközmodell hozzáadása](media/iot-accelerators-device-simulation-advanced-device/devicemodels.png)

A speciális eszközmodell lap megnyitásához kattintson a **Speciális** gombra:

![Speciális lap](media/iot-accelerators-device-simulation-advanced-device/advancedtab.png)

Kattintson a **Tallózás** gombra, és jelölje ki a létrehozott JSON- és JavaScript-fájlokat. Ügyeljen arra, hogy mindhárom fájlt kijelölje. Ha egy fájl hiányzik, az érvényesítés sikertelen:

![Fájlok tallózása](media/iot-accelerators-device-simulation-advanced-device/browse.png)

Ha a fájlok megfelelnek az ellenőrzésen, kattintson a **Mentés** gombra, és az eszközmodell készen áll a szimulációban való használatra. Ellenkező esetben javítsa ki a hibákat, és töltse fel újra a fájlokat:

![Mentés](media/iot-accelerators-device-simulation-advanced-device/validated.png)

## <a name="next-steps"></a>További lépések

Ebben az útmutatóútmutatóban megismerkedett az Eszközszimulációban használt eszközmodell-fájlokkal, valamint a fejlett eszközmodell létrehozásának módjával. Ezután érdemes lehet megvizsgálni, hogyan használhatja a Time Series Insights segítségével [az Eszközszimulációs megoldásgyorsítóból küldött telemetriai adatok megjelenítéséhez.](https://docs.microsoft.com/azure/iot-accelerators/iot-accelerators-device-simulation-time-series-insights)
