---
title: Általános Node.js ügyfélalkalmazást Azure IoT központi csatlakozás |} Microsoft Docs
description: Egy eszköz fejlesztőjeként általános Node.js eszköz csatlakoztatása az Azure IoT központi alkalmazás.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 42ede975f2cfde2d9c0a61d15ba1af412a88c556
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628538"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Csatlakozás az Azure IoT központi alkalmazáshoz (Node.js) általános ügyfélalkalmazás

Ez a cikk ismerteti, hogyan eszköz fejlesztőként jelző egy olyan fizikai eszköz a Microsoft Azure IoT központi alkalmazás általános Node.js alkalmazás csatlakozni.

## <a name="before-you-begin"></a>Előkészületek

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

1. Azure IoT központi alkalmazás. További információkért lásd: [létrehozása az Azure IoT központi alkalmazás](howto-create-application.md).
1. Fejlesztési virtuális gép [Node.js](https://nodejs.org/) 4.0.0 verzió vagy újabb verziója. Futtathat `node --version` a parancssorban a verziójának. A Node.js az operációs rendszerek széles körében elérhető.

Az Azure IoT központi-alkalmazás van szüksége a következő mérések és eszköztulajdonságok definiált rendelkező eszköz sablont:

### <a name="telemetry-measurements"></a>Telemetria mérések

Adja hozzá az alábbi telemetriai a **mérések** lap:

| Megjelenítendő név | Mezőnév  | Egység | Min | Max | Tizedeshelyen |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| Hőmérséklet  | hőmérséklet | F     | 60  | 110 | 0              |
| Páratartalom     | nedvességtartalma    | %     | 0   | 100 | 0              |
| Pressure     | pressure    | kPa   | 80  | 110 | 0              |

> [!NOTE]
  A telemetriai adatok mérték adattípusa.

Írja be pontosan megegyezik az eszköz sablonba a táblázatban látható mező nevét. Ha a mezőnevek nem egyezik, a telemetriai nem lehet megjeleníteni az alkalmazásban.

### <a name="state-measurements"></a>Állapot mérések

Adja hozzá a következő állapotot a **mérések** lap:

| Megjelenítendő név | Mezőnév  | 1. érték | Megjelenítendő név | 2. érték | Megjelenítendő név |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Ventilátor mód     | fanmode     | 1       | Fut      | 0       | Leállítva      |

> [!NOTE]
  Az állapot mérési datatype karakterlánc.

Írja be pontosan megegyezik az eszköz sablonba a táblázatban látható mező nevét. Ha a mezőnevek nem egyezik, az állapot nem lehet megjeleníteni az alkalmazásban.

### <a name="event-measurements"></a>Esemény mérések

A következő esemény hozzáadása a **mérések** lap:

| Megjelenítendő név | Mezőnév  | Severity |
| ------------ | ----------- | -------- |
| Túlmelegedése  | melegedjen túl    | Hiba    |

> [!NOTE]
  Az esemény mérési datatype karakterlánc.

### <a name="device-properties"></a>Eszköztulajdonságok

A következő eszköz Tulajdonságok hozzáadása a **tulajdonságlapján**:

| Megjelenítendő név        | Mezőnév        | Adattípus |
| ------------------- | ----------------- | --------- |
| Sorozatszám       | serialNumber      | szöveg      |
| Eszköz gyártója | gyártó      | szöveg      |

Adja meg a mezőnevek pontosan megegyezik az eszköz sablonba a táblázatban látható. Ha a mezőnevek nem egyezik, az alkalmazás a tulajdonság értéke nem tudja megjeleníteni.

### <a name="settings"></a>Beállítások

Adja hozzá a következő **szám** beállításait a **beállítások lapon**:

| Megjelenítendő név    | Mezőnév     | Egység | Tizedesjegyre | Min | Max  | Kezdeti |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| Ventilátor sebessége       | fanSpeed       | rpm   | 0        | 0   | 3000 | 0       |
| Megadott hőmérséklet | setTemperature | F     | 0        | 20  | 200  | 80      |

Mező neve pontosan a eszköz sablonba a táblázatban látható módon. Ha a mezőnevek nem egyezik, az eszköz a beállításérték nem kapnak.

### <a name="add-a-real-device"></a>Valós eszköz hozzáadása

Az Azure IoT központi-alkalmazás létrehozása, és jegyezze fel az eszköz kapcsolati karakterláncot az eszköz sablonból valós eszköz hozzáadásához. További információkért lásd: [valós eszköz hozzáadása az Azure IoT központi alkalmazás](tutorial-add-device.md)

## <a name="create-a-nodejs-application"></a>Node.js alkalmazás létrehozása

A következő lépések bemutatják, hogyan hozzon létre egy ügyfélalkalmazást, amely a valós eszközt, az alkalmazásba felvett.

1. Hozzon létre egy `connected-air-conditioner-adv` nevű mappát a gépén. Keresse meg a parancssori környezetben mappában.

1. A Node.js-projektet inicializálni a következő parancsokat:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Hozzon létre egy nevű fájlt **connectedAirConditionerAdv.js** a a `connected-air-conditioner-adv` mappát.

1. Adja hozzá a következő `require` elején utasítások a **connectedAirConditionerAdv.js** fájlt:

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    ```

1. Adja a következő változódeklarációkat a fájlhoz:

    ```javascript
    var connectionString = '{your device connection string}';
    var targetTemperature = 0;
    var client = clientFromConnectionString(connectionString);
    ```

    Frissítse a helyőrző `{your device connection string}` az eszköz kapcsolati karakterlánccal. Ez az érték a kapcsolat részleteit megjelenítő oldalon másolta, a tényleges eszköz hozzáadásakor. Ez a példa azt inicializálása `targetTemperature` nulla, opcionálisan készíthet az eszköz az aktuális olvasása vagy -érték az az eszköz iker. 

1. Telemetriai adatok, az állapot és a esemény küldése az Azure IoT központi alkalmazáshoz, adja hozzá a fájlhoz a következő függvény:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var humidity = 70 + (Math.random() * 10);
      var pressure = 90 + (Math.random() * 5);
      var fanmode = 0;
      var data = JSON.stringify({ 
        temperature: temperature, 
        humidity: humidity, 
        pressure: pressure,
        fanmode: (temperature > 25) ? "1" : "0",
        overheat: (temperature > 35) ? "ER123" : undefined });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    1. Eszköztulajdonságok küldése az Azure IoT központi alkalmazáshoz, adja hozzá a fájlhoz a következő függvény:

    ```javascript
    // Send device properties.
    function sendDeviceProperties(twin) {
      var properties = {
        serialNumber: '123-ABC',
        manufacturer: 'Contoso'
      };
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. Az eszköz válaszol-e a beállításainak megadásához adja hozzá a következő definícióját:

    ```javascript
    // Add any settings your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'fanSpeed': (newValue, callback) => {
          // Simulate it taking 1 second to set the fan speed.
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'setTemperature': (newValue, callback) => {
        // Simulate the temperature setting taking two steps.
        setTimeout(() => {
          targetTemperature = targetTemperature + (newValue - targetTemperature) / 2;
          callback(targetTemperature, 'pending');
          setTimeout(() => {
            targetTemperature = newValue;
            callback(targetTemperature, 'completed');
          }, 5000);
        }, 5000);
      }
    };
    ```

1. Az Azure IoT központi alkalmazásból frissített beállításainak kezeléséhez, adja hozzá a következő fájl:

    ```javascript
    // Handle settings changes that come from Azure IoT Central via the device twin.
    function handleSettings(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (settings[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            settings[setting](desiredChange[setting].value, (newValue, status, message) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version,
                  message: message
                }
              }
              twin.properties.reported.update(patch, (err) => console.log(`Sent setting update for ${setting}; ` +
                (err ? `error: ${err.toString()}` : `status: success`)));
            });
          }
        }
      });
    }
    ```

1. Adja hozzá a következő, a kapcsolat az Azure IoT központi befejeződését, majd az ügyfélkód a funkciókat a fájlkiszolgálófürtöt:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            sendDeviceProperties(twin);
            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };

    // Start the device (connect it to Azure IoT Central).
    client.open(connectCallback);
    ```

## <a name="run-your-nodejs-application"></a>A Node.js-alkalmazás futtatása

A következő parancsot a parancssori környezetben:

```cmd/sh
node connectedAirConditionerAdv.js
```

Az Azure IoT központi alkalmazásban kezelőként a valódi eszköz a következőket teheti:

* A telemetriai adatok megtekintése a **mérések** lap:

    ![Telemetria megtekintése](media/howto-connect-nodejs/viewtelemetry.png)

* Az eszközről küldött eszköz tulajdonságértéket megtekintése a **tulajdonságok** lap.

    ![Az eszköz tulajdonságai](media/howto-connect-nodejs/viewproperties.png)

* Állítsa be a ventilátor sebesség és a cél hőmérséklet a **beállítások** lap.

    ![Set Ventilátor sebessége](media/howto-connect-nodejs/setfanspeed.png)

## <a name="next-steps"></a>További lépések

Most, hogy megismerte rendelkezik egy általános Node.js ügyfél csatlakoztatása az Azure IoT központi alkalmazás, az alábbiakban a javasolt lépéseket:
* [Raspberry Pi előkészítése és csatlakoztatása](howto-connect-raspberry-pi-python.md)
<!-- Next how-tos in the sequence -->
