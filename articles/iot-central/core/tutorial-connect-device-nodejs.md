---
title: Oktatóanyag – Általános Node.js ügyfélalkalmazás csatlakoztatása az Azure IoT Centralhoz | Microsoft dokumentumok
description: Ez az oktatóanyag bemutatja, hogyan, eszközfejlesztőként egy Node.js ügyfélalkalmazást futtató eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz. Eszközsablont úgy hoz létre, hogy importál egy eszközképességi modellt, és olyan nézeteket ad hozzá, amelyek lehetővé teszik a csatlakoztatott eszközzel való interakciót
author: dominicbetts
ms.author: dobett
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: b7aebb5b9c1aa6566cedda869f97f2d1aa20df83
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673956"
---
# <a name="tutorial-create-and-connect-a-nodejs-client-application-to-your-azure-iot-central-application-nodejs"></a>Oktatóanyag: Node.js ügyfélalkalmazás létrehozása és csatlakoztatása az Azure IoT Central alkalmazáshoz (Node.js)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

Ez az oktatóanyag bemutatja, hogyan, eszközfejlesztőként egy Node.js ügyfélalkalmazás csatlakoztatása az Azure IoT Central alkalmazáshoz. A Node.js alkalmazás egy környezeti érzékelő eszköz viselkedését szimulálja. Egy _mintaeszköz képességmodell_ segítségével hozzon létre egy _eszközsablont_ az IoT Centralban. Az eszközsablonhoz nézeteket adhat, hogy az operátor kapcsolatba léphesse az eszközzel.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Eszközképességi modell importálása eszközsablon létrehozásához.
> * Alapértelmezett és egyéni nézetek hozzáadása eszközsablonhoz.
> * Közzétehet egy eszközsablont, és hozzáadhat egy valódi eszközt az IoT Central-alkalmazáshoz.
> * Hozza létre és futtassa a Node.js eszközkódját, és tekintse meg, hogy csatlakozzon az IoT Central alkalmazáshoz.
> * Tekintse meg az eszközről küldött szimulált telemetriai adatokat.
> * Az eszköz tulajdonságainak kezeléséhez használjon nézetet.
> * Az eszköz vezérléséhez hívja meg a szinkron és aszinkron parancsokat.

## <a name="prerequisites"></a>Előfeltételek

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

* Az **Egyéni alkalmazássablon** használatával létrehozott Azure IoT Central alkalmazás. További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central.md).
* Fejlesztői gép, amelyen telepítve van a [Node.js](https://nodejs.org/) 10.0.0-s vagy újabb verziója. Futtathatja `node --version` a parancssorban, hogy ellenőrizze a verziót. Az oktatóanyag utasításai feltételezik, hogy a **csomópontparancsot** a Windows parancssorában futtatja. A Node.js azonban számos más operációs rendszeren is használható.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-nodejs-application"></a>Node.js alkalmazás létrehozása

A következő lépések bemutatják, hogyan hozhat létre egy Node.js ügyfélalkalmazást, amely az alkalmazáshoz hozzáadott valódi eszközhöz csatlakozik. Ez a Node.js alkalmazás egy valódi eszköz viselkedését szimulálja.

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

1. Ha szimulált telemetriát szeretne küldeni az Azure IoT Central alkalmazásnak, adja hozzá a következő függvényt a fájlhoz:

    ```javascript
    // Send simulated device telemetry.
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

    A telemetriai elemek`temp` ( `humid`és ) nevének meg kell egyeznie az eszközsablonban használt nevekkel.

1. Ha ikereszköz-tulajdonságokat szeretne küldeni az Azure IoT Central alkalmazásnak, adja hozzá a következő függvényt a fájlhoz:

    ```javascript
    // Send device twin reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

    Az IoT Central az eszköztwins eszköztwins segítségével szinkronizálja a tulajdonságértékeket az eszköz és az IoT Central alkalmazás között. Az eszköztulajdonság-értékek az ikereszköz által jelentett tulajdonságokat használják. Az írható tulajdonságok az ikereszköz által jelentett és a kívánt tulajdonságokat egyaránt használják.

1. Az eszköz által válaszolandó írható tulajdonságok meghatározásához és kezeléséhez adja hozzá a következő kódot:

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that's called when the writeable property
    // is updated in the IoT Central application.
    var writeableProperties = {
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

    // Handle writeable property updates that come from IoT Central via the device twin.
    function handleWriteablePropertyUpdates(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (writeableProperties[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            writeableProperties[setting](desiredChange[setting].value, (newValue, status) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version
                }
              }
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

    Amikor az operátor beállít egy írható tulajdonságot az IoT Central alkalmazásban, az alkalmazás egy ikereszköz kívánt tulajdonságot használ az érték elküldéséhez az eszköznek. Az eszköz ezután válaszol egy eszköz iker jelentett tulajdonság használatával. Amikor az IoT Central megkapja a jelentett tulajdonság értéket, **szinkronizálva**frissíti a tulajdonságnézetet.

    A tulajdonságok (`name` és `brightness`) nevének meg kell egyeznie az eszközsablonban használt nevekkel.

1. Adja hozzá a következő kódot az IoT Central alkalmazásból küldött parancsok kezeléséhez:

    ```javascript
    // Setup command handlers
    function setupCommandHandlers(twin) {

      // Handle synchronous LED blink command with request and response payload.
      function onBlink(request, response) {
        console.log('Received synchronous call to blink');
        var responsePayload = {
          status: 'Blinking LED every ' + request.payload  + ' seconds'
        }
        response.send(200, responsePayload, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          } else {
            console.log('Blinking LED every ' + request.payload  + ' seconds');
          }
        });
      }

      // Handle synchronous LED turn on command
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

      // Handle synchronous LED turn off command
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

      // Handle asynchronous sensor diagnostics command with response payload.
      function diagnostics(request, response) {
        console.log('Starting asynchronous diagnostics run...');
        response.send(202, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          } else {
            var repetitions = 3;
            var intervalID = setInterval(() => {
              console.log('Generating diagnostics...');
              if (--repetitions === 0) {
                clearInterval(intervalID);
                var properties = {
                  rundiagnostics: {
                    value: 'Diagnostics run complete at ' + new Date().toLocaleString()
                  }
                };
                sendDeviceProperties(twin, properties);
              }
            }, 2000);
          }
        });
      }

      hubClient.onDeviceMethod('blink', onBlink);
      hubClient.onDeviceMethod('turnon', turnOn);
      hubClient.onDeviceMethod('turnoff', turnOff);
      hubClient.onDeviceMethod('rundiagnostics', diagnostics);
    }
    ```

    A parancsok`blink`( , `turnon` `turnoff`, `rundiagnostics`, és ) nevének meg kell egyeznie az eszközsablonban használt nevekkel.

    Jelenleg az IoT Central nem használja az eszközképességi modellben definiált válaszsémát. Szinkron parancs esetén a válasz hasznos adata bármely érvényes JSON lehet. Aszinkron parancs esetén az eszköznek azonnal 202-es választ kell adnia, amelyet a munka befejezésekor jelentett tulajdonságfrissítés követ. A jelentett tulajdonságfrissítés formátuma:

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    Az operátor megtekintheti a válasz hasznos adatát a parancselőzményekben.

1. Adja hozzá a következő kódot az Azure IoT Central-alkalmazáshoz való csatlakozás befejezéséhez és az ügyfélkódban lévő függvények csatlakoztatásához:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Send telemetry to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              state: 'true'
            };
            sendDeviceProperties(twin, properties);

            handleWriteablePropertyUpdates(twin);

            setupCommandHandlers(twin);
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

![Az ügyfélalkalmazás futtatása](media/tutorial-connect-device-nodejs/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

Láthatja, hogy az eszköz hogyan reagál a parancsokra és a tulajdonságfrissítésekre:

![Figyelje meg az ügyfélalkalmazást](media/tutorial-connect-device-nodejs/run-application-2.png)

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az eszközképességi modellekről és a saját eszközsablonok létrehozásáról, folytassa az útmutatóval:

> [!div class="nextstepaction"]
> [Új IoT-eszköztípus definiálása](./howto-set-up-template.md)
