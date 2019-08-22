---
title: Általános Node. js-ügyfélalkalmazás összekötése az Azure IoT Central-vel | Microsoft Docs
description: Eszköz-fejlesztőként, hogyan csatlakoztatható egy általános Node. js-eszköz az Azure IoT Central-alkalmazáshoz.
author: dominicbetts
ms.author: dobett
ms.date: 06/14/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 3b73344a233182fe8366795cfa111b706c6d06ac
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876236"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Általános ügyfélalkalmazás összekötése az Azure IoT Central-alkalmazással (node. js)

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Ez a cikk azt ismerteti, hogyan lehet egy eszköz fejlesztőként csatlakozni egy általános Node. js-alkalmazáshoz, amely egy valós eszközt jelképez a Microsoft Azure IoT Central alkalmazáshoz.

## <a name="before-you-begin"></a>Előkészületek

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

1. Azure IoT Central-alkalmazás. További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central.md).
1. A [Node. js](https://nodejs.org/) 4.0.0 vagy újabb verzióját futtató fejlesztői gép. A parancssorban `node --version` futtatva ellenőrizhető a verzió. A Node.js az operációs rendszerek széles körében elérhető.

## <a name="create-a-device-template"></a>Eszköz sablonjának létrehozása

Az Azure IoT Central alkalmazásban szüksége lesz egy eszköz sablonra a következő mértékekkel, eszköz tulajdonságaival, beállításokkal és parancsokkal:

### <a name="telemetry-measurements"></a>Telemetria mérések

Adja hozzá a következő telemetria a **mértékegységek** lapon:

| Megjelenítendő név | Mező neve  | Mértékegységek | Min | Max | Tizedesjegyek |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| Hőmérséklet  | hőmérséklet | P     | 60  | 110 | 0              |
| Nedvességtartalom     | páratartalom    | %     | 0   | 100 | 0              |
| Pressure     | pressure    | kPa   | 80  | 110 | 0              |

> [!NOTE]
> A telemetria-mérés adattípusa lebegőpontos szám.

Adja meg a mezők nevét pontosan úgy, ahogy az a táblázatban látható az eszköz sablonjában. Ha a mezők nevei nem egyeznek a megfelelő eszköz kódjában található nevekkel, a telemetria nem jeleníthető meg az alkalmazásban.

### <a name="state-measurements"></a>Állapot mérései

Adja hozzá a következő állapotot a mérések lapon:

| Megjelenítendő név | Mező neve  | 1\. érték | Megjelenítendő név | 2\. érték | Megjelenítendő név |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Ventilátor mód     | fanmode     | 1       | Fut      | 0       | Leállítva      |

> [!NOTE]
> Az állapot mérésének adattípusa karakterlánc.

Adja meg a mezők nevét pontosan úgy, ahogy az a táblázatban látható az eszköz sablonjában. Ha a mezők nevei nem egyeznek a megfelelő eszköz kódjában található nevekkel, az állapot nem jeleníthető meg az alkalmazásban.

### <a name="event-measurements"></a>Események mérése

Adja hozzá a következő eseményt a **mérések** lapon:

| Megjelenítendő név | Mező neve  | severity |
| ------------ | ----------- | -------- |
| Megelőzhető túlmelegedése  | Túlfűteni    | Hiba    |

> [!NOTE]
> Az esemény mértékének adattípusa karakterlánc.

### <a name="location-measurements"></a>Hely mértékegysége

Adja hozzá a következő hely mértékét a mérések lapon:

| Megjelenítendő név | Mező neve  |
| ------------ | ----------- |
| Location     | location    |

A hely mérési adattípusa két lebegőpontos számból áll a hosszúság és a szélesség számára, és egy opcionális lebegőpontos számot a magassághoz.

Adja meg a mezők nevét pontosan úgy, ahogy az a táblázatban látható az eszköz sablonjában. Ha a mezők nevei nem egyeznek a megfelelő eszköz kódjában található nevekkel, akkor a hely nem jeleníthető meg az alkalmazásban.

### <a name="device-properties"></a>Eszköztulajdonságok

Adja hozzá a következő eszköz tulajdonságait a **Tulajdonságok** lapon:

| Megjelenítendő név        | Mező neve        | Adattípus |
| ------------------- | ----------------- | --------- |
| Sorozatszám       | serialNumber      | text      |
| Eszköz gyártója | gyártó      | text      |

Pontosan úgy adja meg a mezőneveket, ahogy az a táblázatban látható az eszköz sablonjában. Ha a mezők nevei nem egyeznek a megfelelő eszköz kódjában szereplő tulajdonságokkal, a tulajdonságok nem jeleníthetők meg az alkalmazásban.

### <a name="settings"></a>Beállítások

Adja hozzá a következő számú beállítást a **Beállítások** lapon:

| Megjelenítendő név    | Mező neve     | Mértékegységek | Tizedesek | Min | Max  | Kezdeti |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| Ventilátor sebessége       | fanSpeed       | rpm   | 0        | 0   | 3000 | 0       |
| Megadott hőmérséklet | setTemperature | P     | 0        | 20  | 200  | 80      |

Adja meg a mezőnév pontosan úgy, ahogy az a táblázatban látható az eszköz sablonjában. Ha a mezők nevei nem egyeznek a megfelelő eszköz kódjában található nevekkel, az eszköz nem tudja fogadni a beállítás értékét.

### <a name="commands"></a>Parancsok

Adja hozzá a következő parancsot a **parancsok** lapon:

| Megjelenítendő név    | Mező neve     | Alapértelmezett határidő-túllépés | Adattípus |
| --------------- | -------------- | --------------- | --------- |
| Visszaszámlálás       | visszaszámlálás      | 30              | szám    |

Adja hozzá a következő beviteli mezőt a visszaszámlálási parancshoz:

| Megjelenítendő név    | Mező neve     | Adattípus | Value |
| --------------- | -------------- | --------- | ----- |
| Darabszám      | countFrom      | szám    | 10    |

Adja meg a mezőneveket pontosan úgy, ahogy az az eszköz sablonjában látható. Ha a mezők nevei nem egyeznek a megfelelő eszköz kódjában található nevekkel, az eszköz nem tudja feldolgozni a parancsot.

## <a name="add-a-real-device"></a>Valós eszköz hozzáadása

Az Azure IoT Central alkalmazásban adjon hozzá egy valós eszközt az előző szakaszban létrehozott sablonhoz.

Ezután kövesse az "eszköz hozzáadása" oktatóanyag utasításait a [valódi eszközhöz tartozó kapcsolódási karakterlánc létrehozásához](tutorial-add-device.md#generate-connection-string). Ezt a következő szakaszban találja:

### <a name="create-a-nodejs-application"></a>Node.js alkalmazás létrehozása

Az alábbi lépések bemutatják, hogyan hozhat létre olyan ügyfélalkalmazás, amely megvalósítja az alkalmazáshoz hozzáadott valós eszközt. Itt a Node. js-alkalmazás a valódi eszközt jelöli. 

1. Hozzon létre egy `connected-air-conditioner-adv` nevű mappát a gépén. Navigáljon erre a mappára a parancssori környezetben.

1. A Node. js-projekt inicializálásához futtassa a következő parancsokat:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Hozzon létre egy **connectedAirConditionerAdv. js** nevű fájlt `connected-air-conditioner-adv` a mappában.

1. Adja hozzá a `require` következő utasításokat a **connectedAirConditionerAdv. js** fájl elejéhez:

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
    var locLong = -122.1215;
    var locLat = 47.6740;
    var client = clientFromConnectionString(connectionString);
    ```

    Frissítse a helyőrzőt `{your device connection string}` az [eszköz csatlakoztatási karakterláncával](tutorial-add-device.md#generate-connection-string). Ebben a példában nulla értékre állítja `targetTemperature` az inicializálást, az eszközről származó aktuális olvasást vagy a Twin eszköz értékét használhatja.

1. A telemetria, az állapot, az esemény és a hely mérésének az Azure IoT Central-alkalmazásba való küldéséhez adja hozzá a következő függvényt a fájlhoz:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var humidity = 70 + (Math.random() * 10);
      var pressure = 90 + (Math.random() * 5);
      var fanmode = 0;
      var locationLong = locLong - (Math.random() / 100);
      var locationLat = locLat - (Math.random() / 100);
      var data = JSON.stringify({
        temperature: temperature,
        humidity: humidity,
        pressure: pressure,
        fanmode: (temperature > 25) ? "1" : "0",
        overheat: (temperature > 35) ? "ER123" : undefined,
        location: {
            lon: locationLong,
            lat: locationLat }
        });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

1. Az eszköz tulajdonságainak Azure IoT Central-alkalmazásba való küldéséhez adja hozzá a következő függvényt a fájlhoz:

    ```javascript
    // Send device reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. Az eszköz által válaszolt beállítások megadásához adja hozzá a következő definíciót:

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

1. Az Azure IoT Central-alkalmazás frissített beállításainak kezeléséhez adja hozzá a következőt a fájlhoz:

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

1. Adja hozzá a következő kódot a IoT Central alkalmazásból eljuttatott visszaszámlálási parancs kezeléséhez:

    ```javascript
    // Handle countdown command
    function onCountdown(request, response) {
      console.log('Received call to countdown');

      var countFrom = (typeof(request.payload.countFrom) === 'number' && request.payload.countFrom < 100) ? request.payload.countFrom : 10;

      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          client.getTwin((err, twin) => {
            function doCountdown(){
              if ( countFrom >= 0 ) {
                var patch = {
                  countdown:{
                    value: countFrom
                  }
                };
                sendDeviceProperties(twin, patch);
                countFrom--;
                setTimeout(doCountdown, 2000 );
              }
            }

            doCountdown();
          });
        }
      });
    }
    ```

1. Adja hozzá a következő kódot az Azure IoT Central-alkalmazáshoz való csatlakozás befejezéséhez és az ügyfélkódban lévő függvények csatlakoztatásához:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Create handler for countdown command
        client.onDeviceMethod('countdown', onCountdown);

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              serialNumber: '123-ABC',
              manufacturer: 'Contoso'
            };
            sendDeviceProperties(twin, properties);

            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };

    // Start the device (connect it to Azure IoT Central).
    client.open(connectCallback);
    ```

## <a name="run-your-nodejs-application"></a>A Node. js-alkalmazás futtatása

Futtassa a következő parancsot a parancssori környezetben:

```cmd/sh
node connectedAirConditionerAdv.js
```

Az Azure IoT Central-alkalmazásban a valódi eszközhöz tartozó operátorként a következőket teheti:

* Tekintse meg a telemetria a mérések oldalon:

    ![Telemetria megtekintése](media/howto-connect-nodejs/viewtelemetry.png)

* Tekintse meg a helyet a mérések oldalon:

    ![Hely mértékegységének megtekintése](media/howto-connect-nodejs/viewlocation.png)

* A **Tulajdonságok** lapon megtekintheti az eszközről a tulajdonságlapon eljuttatott Eszközállapot-értékeket. Az eszköz tulajdonságlapjának frissítése az eszköz csatlakoztatásakor:

    ![Eszköz tulajdonságainak megtekintése](media/howto-connect-nodejs/viewproperties.png)

* Állítsa be a ventilátor sebességét és a cél hőmérsékletét a **Beállítások** lapról:

    ![Ventilátor sebességének beállítása](media/howto-connect-nodejs/setfanspeed.png)

* Hívja meg a Countdown parancsot a **parancsok** lapról:

    ![Call Countdown parancs](media/howto-connect-nodejs/callcountdown.png)

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan csatlakoztatható egy általános Node. js-ügyfél az Azure IoT Central-alkalmazáshoz, a javasolt következő lépés annak megismerése, hogyan [állíthat be egyéni](howto-set-up-template.md) IoT-eszközt a saját eszközökhöz.
