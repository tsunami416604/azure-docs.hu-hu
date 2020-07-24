---
title: Oktatóanyag – általános Node.js-ügyfélalkalmazás összekötése az Azure IoT Centraltal | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan lehet egy eszköz fejlesztőként összekötni egy Node.js ügyfélalkalmazás futtató eszközt az Azure IoT Central-alkalmazásba. Eszköz-sablon létrehozása eszköz-képességi modell importálásával és olyan nézetek hozzáadásával, amelyek lehetővé teszik a csatlakoztatott eszköz használatát
author: dominicbetts
ms.author: dobett
ms.date: 07/07/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mqtt
ms.openlocfilehash: 08df3bce9d1ecce4d4b0cdfc3034355feef6a4ba
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87003372"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-nodejs"></a>Oktatóanyag: ügyfélalkalmazás létrehozása és összekötése az Azure IoT Central alkalmazással (Node.js)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Ez a cikk a megoldás-építők és az eszközök fejlesztőire vonatkozik.*

Ebből az oktatóanyagból megtudhatja, hogyan lehet egy Node.js ügyfélalkalmazás csatlakoztatásához az Azure IoT Central-alkalmazáshoz. A Node.js alkalmazás egy környezeti érzékelő eszköz viselkedését szimulálja. A minta- _eszköz képesség modell_ használatával IoT Centralban hozhat létre egy _eszközt_ . A nézetek hozzáadásával lehetővé teheti, hogy az operátor kommunikáljon az eszközzel.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Eszköz-képesség modell importálása eszköz sablonjának létrehozásához.
> * Adja hozzá az alapértelmezett és az egyéni nézeteket egy eszköz sablonhoz.
> * Tegye közzé az eszközt, és adjon hozzá egy valós eszközt a IoT Central alkalmazáshoz.
> * Hozza létre és futtassa a Node.js eszköz kódját, és tekintse meg a IoT Central alkalmazáshoz való kapcsolódást ismertető témakört.
> * Az eszközről eljuttatott szimulált telemetria megtekintése.
> * Az eszköz tulajdonságainak kezeléséhez használja a nézetet.
> * A szinkron és aszinkron parancsok meghívásával vezérelheti az eszközt.

## <a name="prerequisites"></a>Előfeltételek

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

* Egy Azure IoT Central-alkalmazás, amely az **egyéni alkalmazás** sablonnal lett létrehozva. További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central.md). Az alkalmazást a 07/14/2020-es vagy későbbi, vagy azt követően kell létrehozni.
* [Node.js](https://nodejs.org/) verziójú 10.0.0 vagy újabb verziót futtató fejlesztési gép. A `node --version` parancssorban futtatva ellenőrizhető a verzió. Az oktatóanyagban szereplő utasítások feltételezik, hogy a Windows-parancssorban futtatja a **Node** parancsot. Számos más operációs rendszeren azonban Node.js is használhatja.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-nodejs-application"></a>Node.js-alkalmazás létrehozása

A következő lépések bemutatják, hogyan hozhat létre olyan Node.js ügyfélalkalmazás, amely az alkalmazáshoz hozzáadott valódi eszközhöz csatlakozik. Ez a Node.js alkalmazás egy valós eszköz viselkedését szimulálja.

1. A parancssori környezetben navigáljon a `environmental-sensor` korábban létrehozott mappára.

1. A Node.js-projekt inicializálásához és a szükséges függőségek telepítéséhez futtassa a következő parancsokat – fogadja el az összes alapértelmezett beállítást a futtatásakor `npm init` :

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Hozzon létre egy **environmentalSensor.js** nevű fájlt a `environmental-sensor` mappában.

1. Adja hozzá a következő `require` utasításokat a **environmentalSensor.js** fájl elejéhez:

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

    Frissítse a helyőrzőket `{your Scope ID}` , `{your Device ID}` és a `{your Primary Key}` korábban megjegyzett értékekkel. Ebben a példában `targetTemperature` nulla értékre állítja az inicializálást, az eszközről származó aktuális olvasást vagy a Twin eszköz értékét használhatja.

1. Szimulált telemetria az Azure IoT Central-alkalmazásba való küldéséhez adja hozzá a következő függvényt a fájlhoz:

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

    A telemetria elemek nevének ( `temp` és `humid` ) meg kell egyeznie az eszköz sablonjában használt nevekkel.

1. Ha az eszköz két tulajdonságát szeretné elküldeni az Azure IoT Central-alkalmazásba, adja hozzá a következő függvényt a fájlhoz:

    ```javascript
    // Send device twin reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

    A IoT Central az eszközön lévő Twins használatával szinkronizálja az eszköz és a IoT Central alkalmazás közötti tulajdonságértékeket. Az eszköz tulajdonságainak értékei az eszköz Twin jelentett tulajdonságait használják. Az írható tulajdonságok mind a két eszköz, mind a kívánt tulajdonságokat használják.

1. Az eszköz által válaszolt írható tulajdonságok definiálásához és kezeléséhez adja hozzá a következő kódot. Az az üzenet, amelyet az eszköz az [írható tulajdonság frissítésére](concepts-telemetry-properties-commands.md#writeable-property-types) válaszként küld, tartalmaznia kell a `av` és a `ac` mezőket. A `ad` mező megadása nem kötelező:

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that's called when the writeable property
    // is updated in the IoT Central application.
    var writeableProperties = {
      'name': (newValue, callback) => {
          setTimeout(() => {
            callback(newValue, 'completed', 200);
          }, 1000);
      },
      'brightness': (newValue, callback) => {
        setTimeout(() => {
            callback(newValue, 'completed', 200);
        }, 5000);
      }
    };

    // Handle writeable property updates that come from IoT Central via the device twin.
    function handleWriteablePropertyUpdates(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (writeableProperties[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting]}`);
            writeableProperties[setting](desiredChange[setting], (newValue, status, code) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  ad: status,
                  ac: code,
                  av: desiredChange.$version
                }
              }
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

    Ha a kezelő egy írható tulajdonságot állít be a IoT Central alkalmazásban, az alkalmazás egy, a kívánt eszközhöz tartozó, a kívánt tulajdonságot használva küldi el az értéket az eszköznek. Az eszköz ezután válaszol a Device Twin jelentett tulajdonság használatával. Ha a IoT Central megkapja a jelentett tulajdonság értékét, akkor a rendszer **szinkronizált**állapottal frissíti a tulajdonság nézetét.

    A tulajdonságok nevének ( `name` és `brightness` ) meg kell egyeznie az eszköz sablonjában használt nevekkel.

1. Adja hozzá a következő kódot a IoT Central alkalmazásból eljuttatott parancsok kezeléséhez:

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

    A parancsok nevének (,, `blink` , `turnon` `turnoff` és `rundiagnostics` ) meg kell egyeznie az eszköz sablonjában használt nevekkel.

    A IoT Central jelenleg nem használja az eszköz-képesség modellben definiált válasz sémát. A szinkron parancsok esetében a válasz hasznos JSON lehet. Egy aszinkron parancs esetében az eszköznek azonnal egy 202-es választ kell visszaadnia, amelyet a rendszer a munka befejeződése után jelentett a tulajdonságok frissítése után. A jelentett tulajdonság frissítésének formátuma:

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    Az operátor megtekintheti a válasz adattartalmát a parancs előzményeiben.

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
              state: 'true',
              processorArchitecture: 'ARM',
              swVersion: '1.0.0'
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

Az eszköz ügyfélalkalmazás elindításához futtassa a következő parancsot a parancssori környezetben:

```cmd/sh
node environmentalSensor.js
```

Láthatja, hogy az eszköz csatlakozik az Azure IoT Central-alkalmazáshoz, és elkezdi elküldeni a telemetria:

![Az ügyfélalkalmazás futtatása](media/tutorial-connect-device-nodejs/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

Láthatja, hogy az eszköz hogyan válaszol a parancsokra és a tulajdonságokra:

![Az ügyfélalkalmazás megfigyelése](media/tutorial-connect-device-nodejs/run-application-2.png)

## <a name="view-raw-data"></a>Nyers adattárolók megtekintése

[!INCLUDE [iot-central-monitor-environmental-sensor-raw-data](../../../includes/iot-central-monitor-environmental-sensor-raw-data.md)]

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan hozhat létre egy eszközt a Node.js használatával, néhány javasolt következő lépés:

* Ismerje meg, hogyan csatlakoztatható valódi eszköz a IoT Centralhoz a [MXChip IoT fejlesztői készlet-eszköz csatlakoztatása az Azure IoT Central-alkalmazáshoz](./howto-connect-devkit.md) útmutató című cikkben.
* Olvassa el a [Mi az eszközök sablonjai?](./concepts-device-templates.md) további információ az eszközök sablonjainak szerepéről az eszköz kódjának megvalósításakor.
* Olvassa el az [Azure IoT Centralhoz való csatlakozást](./concepts-get-connected.md) ismertető témakört, amelyből megtudhatja, hogyan regisztrálhat eszközöket a IoT Central, és hogyan IoT Central biztonságossá teszi az eszközök kapcsolatait.

Ha továbbra is szeretne IoT Central oktatóanyagokat használni, és többet szeretne megtudni egy IoT Central megoldás létrehozásáról, tekintse meg a következő témakört:

> [!div class="nextstepaction"]
> [Átjáró-eszközsablon létrehozása](./tutorial-define-gateway-device-type.md)
