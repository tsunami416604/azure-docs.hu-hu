---
title: Hozzon létre egy speciális szimulált eszköz modell – Azure |} A Microsoft Docs
description: Ez az útmutató megtudhatja, hogyan hozhat létre egy speciális eszközmodell használatra Eszközszimuláció megoldásgyorsító.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 03/18/2019
ms.author: troyhop
ms.openlocfilehash: 4401d4b93a27e76554368ce72d256b38de61df4c
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286125"
---
# <a name="create-an-advanced-device-model"></a>Hozzon létre egy speciális eszköz modellje

Ez az útmutató azt ismerteti, hogy a JSON és a JavaScript-fájlok, amelyek meghatározzák egy egyéni eszköz modellje. A cikk néhány minta eszköz modell definíciós fájlokat tartalmazza, és bemutatja, hogyan tölthet fel őket az Eszközszimuláció-példány. Speciális eszköz-modellek szimulálása a tesztelési realisztikusabb eszközműködés hozhat létre.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ez az Útmutató lépéseit, szükség Eszközszimuláció telepített példányát az Azure-előfizetésében.

Ha még nem helyezte üzembe az Eszközszimulációt, végezze el az [Azure-beli IoT-eszközszimuláció üzembe helyezését és futtatását](quickstart-device-simulation-deploy.md) ismertető rövid útmutatóban leírt lépéseket.

### <a name="open-device-simulation"></a>Eszközszimuláció megnyitása

Az Eszközszimuláció böngészőben történő futtatásához először lépjen a [Microsoft Azure IoT-megoldásgyorsítók](https://www.azureiotsolutions.com) területre.

A rendszer arra kérheti, hogy lépjen be Azure-előfizetésének hitelesítő adataival.

Kattintson a **indítsa el a** az Eszközszimuláció központilag a csempére a [üzembe helyezése és futtatása az IoT eszköz szimulálása az Azure-ban](quickstart-device-simulation-deploy.md) rövid.

## <a name="device-models"></a>Eszközmodellek

Mindegyik szimulált eszköz tartozik egy adott eszköz modellje, amely meghatározza a szimuláció viselkedését. Ez a viselkedés gyakori, hogy telemetriát küldjön módját, milyen típusú üzenetek küldése és a támogatott módszerek tartalmazza.

Megadhat egy JSON-eszköz csomagdefiníciós fájl- és JavaScript-fájlokat használó eszközök modell. A JavaScript-fájlok például a véletlenszerű telemetriai és a metódus logika szimuláció viselkedésének megadása.

Egy tipikus eszközmodell rendelkezik:

* Egy JSON-fájlban mindegyik eszköz modellje (például elevator.json).
* Egy JavaScript viselkedés parancsfájlt minden egyes eszköz modell (például itt-state.js)
* Egy JavaScript metódus parancsfájlt minden egyes eszköz metódus (például itt-go-down.js)

> [!NOTE]
> Nem minden eszköz modellek metódus megadása. Egy eszköz modellje ezért előfordulhat, hogy, vagy nem rendelkezik metódus parancsfájlokat. Azonban minden eszközmodell viselkedésének parancsfájl kell rendelkeznie.

## <a name="device-definition-file"></a>Eszköz csomagdefiníciós fájl

Minden egyes eszköz csomagdefiníciós fájl részleteit egy szimulált eszköz modellje, többek között a következő információkat tartalmazza:

* Eszköz modell neve: karakterlánc.
* Protokoll: AMQP | MQTT | HTTP.
* A kezdeti állapota.
* Hogyan gyakran frissítheti az eszköz állapotát.
* Melyik JavaScript-fájl az állapot frissítéséhez.
* Telemetriai üzeneteket küldeni, minden egyes megadott gyakorisággal listája.
* A telemetriai üzeneteket, a fogadott telemetriát elemezni a háttér-alkalmazás által használt sémája.
* Támogatott módszerek és a JavaScript-fájl használatával szimulálja az egyes módszerek listája.

### <a name="file-schema"></a>Fájl séma

A séma verziója mindig "1.0.0-s", és csak ez a fájl formátuma:

```json
"SchemaVersion": "1.0.0"
```

### <a name="device-model-description"></a>Eszköz leírása

A következő tulajdonságok írják le, az eszköz típusa. Minden egyedi azonosítója, sémantická verze, egy nevet és leírást rendelkezik:

```json
"Id": "chiller-01",
"Version": "0.0.1",
"Name": "Chiller",
"Description": "Chiller with external temperature and humidity sensors."
```

### <a name="iot-protocol"></a>IoT-protokoll

IoT-eszközök csatlakozhatnak különböző protokollok használatával. A szimuláció használatát teszi lehetővé vagy **AMQP**, **MQTT**, vagy **HTTP**:

```json
"Protocol": "AMQP"
```

### <a name="simulated-device-state"></a>Szimulált eszköz állapota

Mindegyik szimulált eszköz van egy belső állapotot, és meg kell határozni. Az állapot is meghatározza a tulajdonságokat, amelyeket jelenteni lehet a telemetriai adatokat. Például egy hűtő előfordulhat, hogy rendelkezik egy kezdeti állapot például:

```json
"InitialState": {
    "temperature": 50,
    "humidity": 40
},
```

Egy mozgó számos olyan előfordulhat eszköz további tulajdonságok, például:

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

Az eszköz állapotát a szimuláció szolgáltatás által a memóriában tárolja, és a JavaScript-függvény bemenetként. Sikerült döntse el, a JavaScript-függvény:

* Hagyja figyelmen kívül az állapot és a véletlenszerű adatok létrehozásához.
* Az állapot frissítése egy adott forgatókönyv esetén valósághű módon.

A függvény, amely létrehozza az állapot is fogad bemenetként:

* Az eszköz azonosítójával.
* Az eszköz modellje.
* Az aktuális idő. Ez az érték lehetővé teszi különböző adatok létrehozására, az eszköz és időpontig.

### <a name="generating-telemetry-messages"></a>Telemetriai üzeneteket generálása

A szimuláció szolgáltatás minden egyes eszközhöz többféle telemetriai adatokat küldhet. Telemetriai adatok általában az eszköz állapotát adatait tartalmazza. Például egy szimulált szoba küldhet információkat hőmérséklettel és páratartalommal kapcsolatos 10 másodpercenként. Vegye figyelembe a helyőrzőket az alábbi kódrészletben is automatikusan cserélni az eszköz állapotát a értékekkel:

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

A helyőrzők speciális szintaxissal **${NAME}** ahol **neve** a JavaScript által visszaadott objektum eszköz állapota a kulcs **fő** függvény. Meg kell adni a karakterláncokat, amíg számok nem ajánlott.

#### <a name="message-schema"></a>Üzenet séma

Minden üzenetet egy jól definiált sémával kell rendelkezniük. Az üzenet séma is közzéteszi az IoT hubhoz, így újból felhasználhatja az információkat a bejövő telemetriát értelmezése háttér-alkalmazások.

A séma támogatja a JSON-formátumban, amely lehetővé teszi, hogy könnyen elemzés, átalakítás és elemzés, több rendszerek és szolgáltatások között.

A séma szereplő mezőket a következő típusú lehet:

* Objektum - szerializált JSON használatával
* Bináris - szerializálni a base64 használatával
* Szöveg
* Logikai
* Egész szám
* Double
* DateTime

### <a name="supported-methods"></a>Támogatott módszerek

A szimulált eszközök is reagálhat a metódust hívja, ebben az esetben egy logikai hajtható végre, és bizonyos válasz meg. Hasonlóképpen, a szimulációt a metódus logika egy JavaScript-fájlban tárolt, és használhatják az eszköz állapotát. Példa:

```json
"CloudToDeviceMethods": {
    "Start": {
        "Type": "JavaScript",
        "Path": "release-pressure.js"
    }
}
```

## <a name="create-a-device-definition-file"></a>Egy eszköz csomagdefiníciós fájl létrehozása

Az útmutatóval-to-útmutatóban bemutatjuk, hogyan hozzon létre egy drónt egy eszköz modellje. A drone véletlenszerűen repülővel fog egy hely és a magasság módosítása koordináták kezdeti készleteinek körül.

Másolja a következő JSON-t egy szövegszerkesztőben, és mentse **drone.json**.

### <a name="device-definition-json-example"></a>Eszköz definíció JSON-példa

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

## <a name="behavior-script-files"></a>Viselkedés a parancsfájlok

A kód a viselkedés parancsfájlban a drone helyezi. A szkript módosítja a drone jogosultságszint-emelés és hely szerint az eszköz memória állapotának módosítása.

A JavaScript-fájlok rendelkeznie kell egy **fő** függvény, amely két paramétert fogad el:

* A **környezet** objektum, amely három tulajdonságot tartalmazza:
    * **currentTime** formátumú karakterlánc formájában **yyyy-MM-dd'T'HH:mm:sszzz**.
    * **deviceId**. Ha például **Simulated.Elevator.123**.
    * **deviceModel**. Ha például **felvonó**.
* A **állapot** , amelyet az előző hívás az a függvény által visszaadott objektum. Ez az eszköz állapotát a szimuláció szolgáltatás tartja karban, és telemetriai üzeneteket létrehozásához használt.

A **fő** függvény az új eszköz állapotát adja vissza. Példa:

```JavaScript
function main(context, state) {

    // Use context if the simulation depends on
    // time or device details.
    // Execute some logic, updating 'state'

    return state;
}
```

## <a name="create-a-behavior-script-file"></a>Viselkedés parancsfájl létrehozása

Másolja a következő JavaScript egy szövegszerkesztőbe, és mentse **drónos-state.js**.

### <a name="device-model-javascript-simulation-example"></a>Eszköz modellje JavaScript szimuláció példa

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

## <a name="create-a-method-script-file"></a>Módszer parancsfájl létrehozása

Módszer parancsprogramok viselkedésének parancsfájlok hasonlóak. Egy adott felhőbeli eszköz metódus hívásakor, azok eszköz viselkedésének megadása.

A drone visszaírási parancsfájlt a drone koordináták szimulálása a drone kezdőlap visszaadása egy rögzített pont állítja be.

Másolja a következő JavaScript egy szövegszerkesztőbe, és mentse **droneRecall-method.js**.

### <a name="device-model-javascript-simulation-example"></a>Eszköz modellje JavaScript szimuláció példa

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

Viselkedés fájl futtatása nem csatoljon egy hibakeresőt, miközben is lehet adatokat írni az szolgáltatás napló használata a **log** függvény. A szintaktikai hibákat a értelmezője számára készült meghiúsul, és a kivétel kapcsolatos adatokat ír a naplófájlba.

Naplózási. példa:

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

## <a name="deploy-an-advanced-device-model"></a>Egy speciális eszköz-modell üzembe helyezése

A speciális eszköz-modell üzembe helyezéséhez a fájlok feltöltése az Eszközszimuláció példány:

A menüsávon válassza az **Eszközmodellek** gombot. A **eszközmodellt** lap felsorolja az eszköz érhető el ebben a példányban az Eszközszimuláció modellek:

![Eszközmodellek](media/iot-accelerators-device-simulation-advanced-device/devicemodelnav.png)

Kattintson az **+ Eszközmodellek hozzáadása** elemre a lap jobb felső sarkában:

![Eszközmodell hozzáadása](media/iot-accelerators-device-simulation-advanced-device/devicemodels.png)

Kattintson a **speciális** a speciális eszköz-modell lap megnyitása:

![Advanced Tab](media/iot-accelerators-device-simulation-advanced-device/advancedtab.png)

Kattintson a **Tallózás** , és válassza ki a létrehozott JSON- és JavaScript-fájlokat. Győződjön meg arról, mindhárom fájlt kiválasztásához. Ha bármely fájl hiányzik, az érvényesítés sikertelen:

![Fájlok tallózása](media/iot-accelerators-device-simulation-advanced-device/browse.png)

Ha a fájlok érvényesíteni, kattintson a **mentése** , és az eszköz modellje a egy használatra kész. Ellenkező esetben javítsa ki a hibákat, és a fájlok újrafeltöltése:

![Mentés](media/iot-accelerators-device-simulation-advanced-device/validated.png)

## <a name="next-steps"></a>További lépések

Ez az útmutató megismerkedett az Eszközszimuláció és a egy speciális eszközmodell létrehozása használt eszköz modell-fájlokat. Ezt követően előfordulhat, hogy szeretné hogyan [az Eszközszimuláció megoldásgyorsító által küldött telemetriai adatok megjelenítéséhez használja a Time Series Insights](https://docs.microsoft.com/azure/iot-accelerators/iot-accelerators-device-simulation-time-series-insights).
