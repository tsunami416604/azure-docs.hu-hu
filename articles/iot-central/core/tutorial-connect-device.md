---
title: Oktatóanyag – Általános Node.js ügyfélalkalmazás csatlakoztatása az Azure IoT Centralhoz | Microsoft dokumentumok
description: Ez az oktatóanyag bemutatja, hogyan, eszközfejlesztőként egy Node.js ügyfélalkalmazást futtató eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz. Eszközsablont úgy hoz létre, hogy importál egy eszközképességi modellt, és olyan nézeteket ad hozzá, amelyek lehetővé teszik a csatlakoztatott eszközzel való interakciót
author: dominicbetts
ms.author: dobett
ms.date: 02/26/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1bcfc949eff0639dd1b4a063687e2c198f480ea3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77624542"
---
# <a name="tutorial-create-and-connect-a-nodejs-client-application-to-your-azure-iot-central-application-nodejs"></a>Oktatóanyag: Node.js ügyfélalkalmazás létrehozása és csatlakoztatása az Azure IoT Central alkalmazáshoz (Node.js)

Ez az oktatóanyag bemutatja, hogyan, eszközfejlesztőként egy Node.js ügyfélalkalmazás csatlakoztatása az Azure IoT Central alkalmazáshoz. A Node.js alkalmazás egy valódi eszköz viselkedését szimulálja. Egy környezeti érzékelő eszköz _mintaeszköz-képességmodelljét_ használja egy _eszközsablon_ létrehozásához az IoT Centralban. Nézeteket adhat az eszközsablonhoz, hogy vizualizálhatja az eszköztelemetriát, kezelheti az eszköz tulajdonságait, és parancsokat használhat az eszközök vezérléséhez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Eszközképességi modell importálása eszközsablon létrehozásához.
> * Alapértelmezett és egyéni nézetek hozzáadása eszközsablonhoz.
> * Közzétehet egy eszközsablont, és hozzáadhat egy valódi eszközt az IoT Central-alkalmazáshoz.
> * Hozza létre és futtassa a Node.js eszközkódját, és tekintse meg, hogy csatlakozzon az IoT Central alkalmazáshoz.
> * Tekintse meg az eszköz által küldött szimulált telemetriai adatokat.
> * Az eszköz tulajdonságainak kezeléséhez használjon nézetet.
> * Az eszköz vezérléséhez hívja a parancsokat.

## <a name="prerequisites"></a>Előfeltételek

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

* Egy Azure IoT Central alkalmazás a **Egyéni alkalmazás **sablon használatával létrehozott. További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central.md).
* Fejlesztői gép, amelyen telepítve van a [Node.js](https://nodejs.org/) 10.0.0-s vagy újabb verziója. Futtathatja `node --version` a parancssorban, hogy ellenőrizze a verziót. A Node.js az operációs rendszerek széles körében elérhető. Az oktatóanyag utasításai feltételezik, hogy a **csomópontparancsot** a Windows parancssorában futtatja. A Node.js számos operációs rendszeren használható.

## <a name="create-a-device-template"></a>Eszközsablon létrehozása

Hozzon létre `environmental-sensor` egy mappát a helyi számítógépen.

Töltse le a [Környezetvédelmi érzékelő képesség modell](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) `environmental-sensor` JSON fájlt, és mentse el a mappába.

A letöltött fájlban a két `{YOUR_COMPANY_NAME_HERE}` példányt szövegszerkesztővel `EnvironmentalSensorInline.capabilitymodel.json` cserélheti le a vállalat nevére.

Az Azure IoT Central alkalmazásban hozzon létre egy környezeti `EnvironmentalSensorInline.capabilitymodel.json` *érzékelő* nevű eszközsablont az eszközképességi modellfájl importálásával:

![Eszközsablon importált eszközképesség-modellel](./media/tutorial-connect-device/device-template.png)

Az eszközképességi modell két interfészt tartalmaz: a szabványos **eszközinformációs interfészt** és az egyéni **környezeti érzékelő** interfészt. A **környezeti érzékelő** felület a következő képességeket határozza meg:

| Típus | Megjelenítendő név | Leírás |
| ---- | ------------ | ----------- |
| Tulajdonság | Eszközállapot     | Az eszköz állapota. Két állam online / offline állnak rendelkezésre. |
| Tulajdonság | Vevő neve    | Az eszközt jelenleg üzemeltető ügyfél neve. |
| Tulajdonság | Fényerő szint | A készülék fényerejének fényereje. Megadható 1 (magas), 2 (közepes), 3 (alacsony). |
| Telemetria | Hőmérséklet | A készülék aktuális hőmérséklete. |
| Telemetria | Páratartalom    | A készülék aktuális páratartalma. |
| Parancs | Pislogás          | Kezdje el villogni a LED-et az adott időintervallumban. |
| Parancs | fordulat         | Kapcsolja be a LED-lámpát a készüléken. |
| Parancs | Kanyart        | Kapcsolja ki a készülék LED-lámpáját. |
| Parancs | rundiagnostics (diagnosztika) | Ez a parancs elindítja a diagnosztika futtatását. |

Ha testre szeretné szabni, hogy az **Eszközállapot** tulajdonság hogyan jelenjen meg az IoT Central alkalmazásban, válassza a **Testreszabás lehetőséget** az eszközsablonban. Bontsa ki az **Eszközállapot-bejegyzést,** írja be az _Online_ nevet **Igaz névként,** offline nevet _pedig_ **hamis névként.** Ezután mentse a módosításokat:

![Az eszközsablon testreszabása](media/tutorial-connect-device/customize-template.png)

## <a name="create-views"></a>Nézetek létrehozása

A nézetek lehetővé teszik az IoT Central alkalmazáshoz csatlakoztatott eszközökkel való interakciót. Rendelkezhet például telemetriai adatokat megjelenítő, tulajdonságokat megjelenítő nézeteket, valamint olyan nézetekkel, amelyek lehetővé teszik az írható és felhőalapú tulajdonságok szerkesztését. A nézetek egy eszközsablon részét képezik.

Ha néhány alapértelmezett nézetet szeretne hozzáadni a **Környezetérzékelő** eszközsablonhoz, keresse meg az eszközsablont, válassza a **Nézetek**lehetőséget, és válassza az **Alapértelmezett nézetek létrehozása csempét.** Győződjön meg arról, hogy **az Áttekintés** és a **Betekintés** beállítás be van **kapcsolva,** majd válassza **az Alapértelmezett irányítópult-nézetek létrehozása**lehetőséget. Most már két alapértelmezett nézet van definiálva a sablonban.

A **Környezeti érzékelő** felület két írható tulajdonságot tartalmaz : Az ügyfél **neve** és **a fényerő szint**. Nézet létrehozásához a következő tulajdonságokat szerkesztheti:

1. Válassza a **Nézetek** lehetőséget, majd válassza az **Eszköz szerkesztése és** a felhőalapú adatok csempét.

1. Adja meg a _Tulajdonságok_ nevet űrlapnévként.

1. Válassza ki a **Fényerő szint** és **a Vevő neve** tulajdonságokat. Ezután válassza **a Hozzáadás szakaszlehetőséget.**

1. Mentse a módosításokat.

![Nézet hozzáadása a tulajdonságszerkesztés engedélyezéséhez](media/tutorial-connect-device/properties-view.png)

## <a name="publish-the-template"></a>A sablon közzététele

Mielőtt hozzáadna egy eszközt az IoT Central alkalmazáshoz, amely a **környezeti érzékelő** eszközsablont használja, közzé kell tennie a sablont.

Az eszközsablonban válassza a **Közzététel**lehetőséget. A közzéteszendő módosításokat megjelenítő panelen válassza a **Közzététel**lehetőséget.

Annak ellenőrzéséhez, hogy a sablon készen áll-e a használatra, keresse meg az **Eszközök** lapot az IoT Central alkalmazásban. Az **Eszközök** szakasz az alkalmazásban közzétett eszközök listáját jeleníti meg:

![Közzétett sablonok az eszközök lapon](media/tutorial-connect-device/published-templates.png)

## <a name="add-a-real-device"></a>Valós eszköz hozzáadása

Az Azure IoT Central-alkalmazásban adjon hozzá egy valódi eszközt az előző szakaszban létrehozott eszközsablonhoz:

1. Az **Eszközök** lapon válassza a **Környezeti érzékelő** eszköz sablont.

1. Válassza **a + Új**lehetőséget.

1. Győződjön meg arról, hogy a **Szimulált** ki van **kapcsolva.** Ezután válassza **a Létrehozás lehetőséget.**

Kattintson az eszköz nevére, majd a **Csatlakozás gombra.** Jegyezze fel az eszközkapcsolat adatait az **Eszközkapcsolat** lapon - **Azonosító hatókör**, **Eszközazonosító**és **Elsődleges kulcs**. Az eszközkód létrehozásakor ezekre az értékekre van szüksége:

![Eszközkapcsolat adatai](./media/tutorial-connect-device/device-connection.png)

### <a name="create-a-nodejs-application"></a>Node.js alkalmazás létrehozása

A következő lépések bemutatják, hogyan hozhat létre egy Node.js ügyfélalkalmazást, amely megvalósítja az alkalmazáshoz hozzáadott valódi eszközt. Ez a Node.js alkalmazás egy valódi eszköz viselkedését szimulálja.

1. A parancssori környezetben keresse `environmental-sensor` meg a korábban létrehozott mappát.

1. A Node.js projekt inicializálásához és a szükséges függőségek telepítéséhez futtassa a következő parancsokat – a futtatáskor `npm init`fogadja el az összes alapértelmezett beállítást:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Hozzon létre egy **environmentalSensor.js** nevű fájlt a `environmental-sensor` mappában.

1. Adja hozzá `require` a következő állításokat az **environmentalSensor.js** fájl elején:

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. Adja a következő változódeklarációkat a fájlhoz:

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = '{your Scope ID}';
    var registrationId = '{your Device ID}';
    var symmetricKey = '{your Primary Key}';
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;
    var targetTemperature = 0;
    var ledOn = true;
    ```

    Frissítse a `{your Scope ID}` `{your Device ID}`helyőrzőket `{your Primary Key}` és a korábban feljegyzett értékekkel. Ebben a példában, `targetTemperature` inicializálja a nulla, használhatja az aktuális olvasás az eszközről, vagy egy értéket az eszköz iker.

1. Telemetriai adatok küldése az Azure IoT Central alkalmazás, adja hozzá a következő függvényt a fájlhoz:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temp = targetTemperature + (Math.random() * 15);
      var humid = 70 + (Math.random() * 10);
      var data = JSON.stringify({
        temp: temp,
        humid: humid,
        });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

1. Ha eszköztulajdonságokat szeretne küldeni az Azure IoT Central alkalmazásnak, adja hozzá a következő függvényt a fájlhoz:

    ```javascript
    // Send device reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. Az eszköz által válaszolandó írható tulajdonságok meghatározásához és kezeléséhez adja hozzá a következő kódot:

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'name': (newValue, callback) => {
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'brightness': (newValue, callback) => {
        setTimeout(() => {
            callback(newValue, 'completed');
      }, 5000);
      }
    };

    // Handle writeable property changes that come from Azure IoT Central via the device twin.
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
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

1. Adja hozzá a következő kódot az IoT Central alkalmazásból küldött parancsok kezeléséhez:

    ```javascript
    // Handle blink command
    function onBlink(request, response) {
      console.log('Received asynchronous call to blink');
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          console.log('Blinking LED every ' + request.payload  + ' seconds');
        }
      });
    }

    // Handle LED turn on command
    function turnOn(request, response) {
      console.log('Received synchronous call to turn on LED');
      if(!ledOn){
        console.log('Turning on the LED');
        ledOn = true;
      }
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        }
      });
    }

    // Handle LED turn off command
    function turnOff(request, response) {
      console.log('Received synchronous call to turn off LED');
      if(ledOn){
        console.log('Turning off the LED');
        ledOn = false;
      }
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        }
      });
    }

    // Handle sensor diagnostics command
    function diagnostics(request, response) {
      console.log('Received asynchronous call to run diagnostics');
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          console.log('Running diagnostics...');
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
        // Create handlers for commands
        hubClient.onDeviceMethod('blink', onBlink);
        hubClient.onDeviceMethod('turnon', turnOn);
        hubClient.onDeviceMethod('turnoff', turnOff);
        hubClient.onDeviceMethod('rundiagnostics', diagnostics);
        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);
        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send Environmental Sensor device properties once on device start up.
            var properties = {
              state: 'true'
            };
            sendDeviceProperties(twin, properties);
            handleSettings(twin);
          }
        });
      }
    };

    // Start the device (register and connect to Azure IoT Central).
    provisioningClient.register((err, result) => {
      if (err) {
        console.log('Error registering device: ' + err);
      } else {
        console.log('Registration succeeded');
        console.log('Assigned hub=' + result.assignedHub);
        console.log('DeviceId=' + result.deviceId);
        var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
        hubClient = Client.fromConnectionString(connectionString, iotHubTransport);
        hubClient.open(connectCallback);
      }
    });
    ```

## <a name="run-your-nodejs-application"></a>A Node.js-alkalmazás futtatása

Az eszközügyfél-alkalmazás elindításához futtassa a következő parancsot a parancssori környezetben:

```cmd/sh
node environmentalSensor.js
```

Láthatja, hogy az eszköz csatlakozik az Azure IoT Central alkalmazáshoz, és elkezdi a telemetriai adatok küldését:

![Az ügyfélalkalmazás futtatása](media/tutorial-connect-device/run-application.png)

Az Azure IoT Central alkalmazás operátoraként a következőket teheti:

* Az eszköz által küldött telemetriai adatok megtekintése az **Áttekintés** lapon:

    ![Telemetria megtekintése](media/tutorial-connect-device/view-telemetry.png)

* Írható tulajdonságértékek frissítése a **Tulajdonságok** lapon:

    ![Tulajdonságok frissítése](media/tutorial-connect-device/update-properties.png)

    ![Tulajdonságok eszközének frissítése](media/tutorial-connect-device/update-properties-device.png)

* Hívja meg a **parancsokat** a Parancsok lapon:

    ![Villogó hívás parancs](media/tutorial-connect-device/call-command.png)

    ![Villogó parancseszköz hívása](media/tutorial-connect-device/call-command-device.png)

* Az eszköz tulajdonságainak megtekintése a **Betekintés** lapon:

    ![Tulajdonságok megtekintése](media/tutorial-connect-device/about-properties.png)

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az eszközképességi modellekről és a saját eszközsablonok létrehozásáról, folytassa az útmutatóval:

> [!div class="nextstepaction"]
> [Új IoT-eszköztípus definiálása](./howto-set-up-template.md)
