---
title: Oktatóanyag – általános Node. js-ügyfélalkalmazás összekötése az Azure IoT Central-vel | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan, mint az eszköz fejlesztője, hogyan csatlakoztatható egy Node. js-ügyfélprogramot futtató eszköz az Azure IoT Central-alkalmazáshoz. Eszköz-sablon létrehozása eszköz-képességi modell importálásával és olyan nézetek hozzáadásával, amelyek lehetővé teszik a csatlakoztatott eszköz használatát
author: dominicbetts
ms.author: dobett
ms.date: 02/26/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1bcfc949eff0639dd1b4a063687e2c198f480ea3
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624542"
---
# <a name="tutorial-create-and-connect-a-nodejs-client-application-to-your-azure-iot-central-application-nodejs"></a>Oktatóanyag: Node. js-ügyfélalkalmazás létrehozása és összekötése az Azure IoT Central-alkalmazással (node. js)

Ez az oktatóanyag bemutatja, hogyan csatlakozhat egy Node. js-ügyfélalkalmazás Azure IoT Central-alkalmazásához az eszköz fejlesztőinek. A Node. js-alkalmazás szimulálja egy valós eszköz viselkedését. A környezeti érzékelő eszközhöz egy minta _eszköz-képesség modell_ használatával hozhat létre IoT Centralban egy _eszköz sablonját_ . A nézetek hozzáadásával megjelenítheti az eszköz telemetria, kezelheti az eszköz tulajdonságait, és parancsokat használhat az eszközök vezérléséhez.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Eszköz-képesség modell importálása eszköz sablonjának létrehozásához.
> * Adja hozzá az alapértelmezett és az egyéni nézeteket egy eszköz sablonhoz.
> * Tegye közzé az eszközt, és adjon hozzá egy valós eszközt a IoT Central alkalmazáshoz.
> * Hozza létre és futtassa a Node. js-eszköz kódját, és tekintse meg az IoT Central alkalmazáshoz való kapcsolódást.
> * Az eszköz által küldött szimulált telemetria megtekintése.
> * Az eszköz tulajdonságainak kezeléséhez használja a nézetet.
> * Az eszköz vezérlésére szolgáló parancsok hívása.

## <a name="prerequisites"></a>Előfeltételek

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

* A * * Custom Application * * sablon használatával létrehozott Azure IoT Central-alkalmazás. További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central.md).
* A [Node. js](https://nodejs.org/) 10.0.0 vagy újabb verzióját futtató fejlesztői gép. Az `node --version` a parancssorban futtatva ellenőrizhető a verzió. A Node.js az operációs rendszerek széles körében elérhető. Az oktatóanyagban szereplő utasítások feltételezik, hogy a Windows-parancssorban futtatja a **Node** parancsot. A Node. js számos operációs rendszeren használható.

## <a name="create-a-device-template"></a>Eszköz sablonjának létrehozása

Hozzon létre egy `environmental-sensor` nevű mappát a helyi gépen.

Töltse le a [környezeti érzékelő képesség modell](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) JSON-fájlját, és mentse azt a `environmental-sensor` mappába.

Szövegszerkesztővel cserélje le a `{YOUR_COMPANY_NAME_HERE}` két példányát a vállalata nevére a letöltött `EnvironmentalSensorInline.capabilitymodel.json` fájlban.

Az Azure IoT Central-alkalmazásban hozzon létre egy *környezeti érzékelő* nevű sablont az `EnvironmentalSensorInline.capabilitymodel.json` eszköz-képesség modell fájljának importálásával:

![Eszköz sablonja az importált eszköz képességeinek modelljével](./media/tutorial-connect-device/device-template.png)

Az eszköz képességeinek modellje két felületet tartalmaz: a szabványos **eszköz információs** felületét és az egyéni **környezeti érzékelő** felületét. A **környezeti érzékelő** felülete a következő képességeket határozza meg:

| Típus | Megjelenítendő név | Leírás |
| ---- | ------------ | ----------- |
| Tulajdonság | Eszközállapot     | Az eszköz állapota. Két állapot érhető el online/offline állapotban. |
| Tulajdonság | Ügyfél neve    | Az eszközt jelenleg működtető ügyfél neve. |
| Tulajdonság | Fényerő szintje | Az eszköz fényének fényerő-szintje. Megadható 1 (magas), 2 (közepes), 3 (alacsony). |
| Telemetria | Hőmérséklet | Az eszköz aktuális hőmérséklete. |
| Telemetria | Páratartalom    | Az eszköz aktuális nedvességtartalma. |
| Parancs | kurzorvillogás          | A LED villogásának megkezdése adott időtartamra. |
| Parancs | turnon         | Kapcsolja be a LED-fényt az eszközön. |
| Parancs | kanyart        | Kapcsolja ki a LED-fényt az eszközön. |
| Parancs | rundiagnostics | Ez a parancs elindítja a diagnosztika futtatását. |

Ha testre szeretné szabni, hogyan jelenjen meg az **eszköz állapota** tulajdonság a IoT Central alkalmazásban, válassza a **Testreszabás** lehetőséget az eszköz sablonjában. Bontsa ki az **eszköz állapota** bejegyzést, adja meg az _online_ értéket a **valódi név** és az _Offline_ értékként a **hamis név**mezőben. Ezután mentse a módosításokat:

![Sablon testreszabása](media/tutorial-connect-device/customize-template.png)

## <a name="create-views"></a>Nézetek létrehozása

A nézetek lehetővé teszik a IoT Central alkalmazáshoz csatlakoztatott eszközök kezelését. Megtekintheti például a telemetria megjelenítő nézeteket, a tulajdonságokat megjelenítő nézeteket, valamint az írható és a felhő tulajdonságainak szerkesztését lehetővé teszi a nézeteket. A nézetek egy eszköz sablon részét képezik.

Ha néhány alapértelmezett nézetet szeretne hozzáadni a **környezeti érzékelő** eszköz sablonhoz, navigáljon az eszköz sablonhoz, válassza a **nézetek**lehetőséget, és válassza az **alapértelmezett nézetek előállítása** csempét. Győződjön meg arról, hogy az **Áttekintés** és a **Névjegy** be van **kapcsolva**, majd válassza az **alapértelmezett irányítópult-nézetek előállítása**lehetőséget. Most már két alapértelmezett nézet van definiálva a sablonban.

A **környezeti érzékelő** felülete két írható tulajdonságot tartalmaz – az **ügyfél nevét** és a **fényerő szintjét**. Nézet létrehozásához a következő tulajdonságokat használhatja:

1. Válassza a **nézetek** lehetőséget, majd válassza ki az **eszköz és a Felhőbeli adatcsempe szerkesztését** .

1. Adja meg a _tulajdonságokat_ az űrlap neveként.

1. Válassza ki a **fényerő szintjét** és az **ügyfél neve** tulajdonságokat. Ezután válassza a **Hozzáadás szakaszt**.

1. Mentse a módosításokat.

![Nézet hozzáadása a tulajdonságok szerkesztésének engedélyezéséhez](media/tutorial-connect-device/properties-view.png)

## <a name="publish-the-template"></a>A sablon közzététele

Mielőtt hozzáad egy eszközt a IoT Central alkalmazáshoz, amely a **környezeti érzékelő** eszköz sablonját használja, közzé kell tennie a sablont.

Az eszköz sablonjában válassza a **Közzététel**lehetőséget. A közzétenni kívánt módosításokat megjelenítő panelen válassza a **Közzététel**lehetőséget.

A sablon használatra kész állapotának megtekintéséhez navigáljon az **eszközök** lapra a IoT Central alkalmazásban. Az **eszközök** szakasz az alkalmazás közzétett eszközeinek listáját jeleníti meg:

![Közzétett sablonok az eszközök lapon](media/tutorial-connect-device/published-templates.png)

## <a name="add-a-real-device"></a>Valós eszköz hozzáadása

Az Azure IoT Central alkalmazásban adjon hozzá egy valós eszközt az előző szakaszban létrehozott sablonhoz:

1. Az **eszközök** lapon válassza ki a **környezeti érzékelő** eszköz sablonját.

1. Válassza az **+ új**lehetőséget.

1. Győződjön meg arról, hogy a **szimulált** érték **ki van kapcsolva**. Ezután kattintson a **Létrehozás** elemre.

Kattintson az eszköz nevére, majd válassza a **Csatlakoztatás**lehetőséget. Jegyezze fel az eszköz csatlakoztatására vonatkozó információkat az eszköz **csatlakoztatása** lapon – **azonosító hatókör**, **eszköz azonosítója**és **elsődleges kulcs**. Ezekre az értékekre szüksége lesz az eszköz kódjának létrehozásakor:

![Eszköz csatlakoztatási adatai](./media/tutorial-connect-device/device-connection.png)

### <a name="create-a-nodejs-application"></a>Node.js alkalmazás létrehozása

A következő lépések bemutatják, hogyan hozhat létre egy Node. js-ügyfélalkalmazás, amely megvalósítja az alkalmazáshoz hozzáadott valós eszközt. Ez a Node. js-alkalmazás szimulálja egy valós eszköz viselkedését.

1. A parancssori környezetben navigáljon a korábban létrehozott `environmental-sensor` mappához.

1. A Node. js-projekt inicializálásához és a szükséges függőségek telepítéséhez futtassa a következő parancsokat – a `npm init`futtatásakor fogadja el az összes alapértelmezett beállítást:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Hozzon létre egy **environmentalSensor. js** nevű fájlt a `environmental-sensor` mappában.

1. Adja hozzá a következő `require` utasításokat a **environmentalSensor. js** fájl elejéhez:

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

    Frissítse a helyőrzőket `{your Scope ID}`, `{your Device ID}`és `{your Primary Key}` a korábban jegyzett értékekkel. Ebben a példában a `targetTemperature`t nulla értékre állítja, az eszköz aktuális olvasását vagy a Twin eszköz értékét használhatja.

1. Ha telemetria szeretne küldeni az Azure IoT Central alkalmazásnak, adja hozzá a következő függvényt a fájlhoz:

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

1. Az eszköz tulajdonságainak Azure IoT Central-alkalmazásba való küldéséhez adja hozzá a következő függvényt a fájlhoz:

    ```javascript
    // Send device reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. Az eszköz által válaszolt írható tulajdonságok definiálásához és kezeléséhez adja hozzá a következő kódot:

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

1. Adja hozzá a következő kódot a IoT Central alkalmazásból eljuttatott parancsok kezeléséhez:

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

## <a name="run-your-nodejs-application"></a>A Node. js-alkalmazás futtatása

Az eszköz ügyfélalkalmazás elindításához futtassa a következő parancsot a parancssori környezetben:

```cmd/sh
node environmentalSensor.js
```

Láthatja, hogy az eszköz csatlakozik az Azure IoT Central-alkalmazáshoz, és elkezdi elküldeni a telemetria:

![Az ügyfélalkalmazás futtatása](media/tutorial-connect-device/run-application.png)

Az Azure IoT Central-alkalmazásban a következőket teheti:

* Tekintse meg az eszköz által az **Áttekintés** lapon eljuttatott telemetria:

    ![Telemetria megtekintése](media/tutorial-connect-device/view-telemetry.png)

* Írható tulajdonságok értékének frissítése a **Tulajdonságok** lapon:

    ![Tulajdonságok frissítése](media/tutorial-connect-device/update-properties.png)

    ![Tulajdonságok frissítése eszköz](media/tutorial-connect-device/update-properties-device.png)

* Hívja meg a parancsokat a **parancsok** lapról:

    ![A Blink parancs hívása](media/tutorial-connect-device/call-command.png)

    ![A Blink parancs eszközének hívása](media/tutorial-connect-device/call-command-device.png)

* Tekintse meg az eszköz tulajdonságait a **Névjegy** oldalon:

    ![Tulajdonságok megtekintése](media/tutorial-connect-device/about-properties.png)

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az eszköz képességeinek modelljeiről, valamint arról, hogyan hozhat létre saját eszközöket, folytassa a következő útmutatóval:

> [!div class="nextstepaction"]
> [Új IoT-eszköz típusának megadása](./howto-set-up-template.md)
