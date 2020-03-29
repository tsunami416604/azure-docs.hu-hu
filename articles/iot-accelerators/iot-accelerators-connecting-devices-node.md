---
title: Eszközök kiépítése a távoli figyeléshez a Node.js fájlban - Azure | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy miként csatlakoztatható eszköz a távfigyelési megoldás gyorsítóhoz a Node.js nyelven írt alkalmazás használatával.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: dobett
ms.openlocfilehash: fdb2bed76a8e23a6034a57b3a5f1358c26e9e990
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61450278"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-nodejs"></a>Az eszköz csatlakoztatása a távfigyelési megoldás gyorsítóhoz (Node.js)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ez az oktatóanyag bemutatja, hogyan csatlakoztathat egy valódi eszközt a távoli figyelési megoldás gyorsítóhoz. Ebben az oktatóanyagban a Node.js programot használja, amely jó lehetőség a minimális erőforrás-korlátokkal rendelkező környezetekben.

Ha egy eszközt szeretne szimulálni, olvassa el az [Új szimulált eszköz létrehozása és tesztelése című témakört.](iot-accelerators-remote-monitoring-create-simulated-device.md)

## <a name="create-a-nodejs-solution"></a>Node.js megoldás létrehozása

Győződjön meg arról, hogy a [Node.js](https://nodejs.org/) 4.0.0-s vagy újabb verziója telepítve van a fejlesztői gépen. Futtathatja `node --version` a parancssorból, hogy ellenőrizze a verziót.

1. Hozzon létre `remotemonitoring` egy mappát a fejlesztői gépen. Keresse meg ezt a mappát a parancssori környezetben.

1. A mintaalkalmazás befejezéséhez szükséges csomagok letöltéséhez és telepítéséhez futtassa a következő parancsokat:

    ```cmd/sh
    npm init
    npm install async azure-iot-device azure-iot-device-mqtt --save
    ```

1. Hozzon `remotemonitoring` létre egy **remote_monitoring.js**nevű fájlt a mappában. Nyissa meg ezt a fájlt egy szövegszerkesztőben.

1. A **remote_monitoring.js** fájlban adja `require` hozzá a következő állításokat:

    ```javascript
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var async = require('async');
    ```

1. Adja hozzá a következő változódeklarációkat az `require` utasítások után. Cserélje le a `{device connection string}` helyőrző értéket a távoli figyelési megoldásban kiépített eszközre vonatkozóan a feljegyzett értékre:

    ```javascript
    var connectionString = '{device connection string}';
    ```

1. Néhány alap telemetriai adat definiálásához adja hozzá a következő változókat:

    ```javascript
    var temperature = 50;
    var temperatureUnit = 'F';
    var humidity = 50;
    var humidityUnit = '%';
    var pressure = 55;
    var pressureUnit = 'psig';
    ```

1. Néhány tulajdonságérték definiálásához adja hozzá a következő változókat:

    ```javascript
    var schema = "real-chiller;v1";
    var deviceType = "RealChiller";
    var deviceFirmware = "1.0.0";
    var deviceFirmwareUpdateStatus = "";
    var deviceLocation = "Building 44";
    var deviceLatitude = 47.638928;
    var deviceLongitude = -122.13476;
    var deviceOnline = true;
    ```

1. Adja hozzá a következő változót a megoldásnak küldendő jelentett tulajdonságok meghatározásához. Ezek a tulajdonságok a webes felhasználói felületen megjelenítendő metaadatok:

    ```javascript
    var reportedProperties = {
      "SupportedMethods": "Reboot,FirmwareUpdate,EmergencyValveRelease,IncreasePressure",
      "Telemetry": {
        [schema]: ""
      },
      "Type": deviceType,
      "Firmware": deviceFirmware,
      "FirmwareUpdateStatus": deviceFirmwareUpdateStatus,
      "Location": deviceLocation,
      "Latitude": deviceLatitude,
      "Longitude": deviceLongitude,
      "Online": deviceOnline
    }
    ```

1. A művelet eredmények nyomtatásához adja hozzá a következő segítő funkciót:

    ```javascript
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. Adja hozzá a következő segítő függvényt a telemetriai értékek véletlenszerűvé tétele érdekében:

     ```javascript
     function generateRandomIncrement() {
         return ((Math.random() * 2) - 1);
     }
     ```

1. Adja hozzá a következő általános függvényt a megoldásból érkező közvetlen metódushívások kezeléséhez. A függvény információkat jelenít meg a megmeghívott közvetlen metódusról, de ebben a példában semmilyen módon nem módosítja az eszközt. A megoldás közvetlen módszereket használ az eszközökön való cselekvésre:

     ```javascript
     function onDirectMethod(request, response) {
       // Implement logic asynchronously here.
       console.log('Simulated ' + request.methodName);

       // Complete the response
       response.send(200, request.methodName + ' was called on the device', function (err) {
         if (err) console.error('Error sending method response :\n' + err.toString());
         else console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');
       });
     }
     ```

1. Adja hozzá a következő függvényt a megoldásból érkező **FirmwareUpdate** közvetlen metódushívások kezeléséhez. A függvény ellenőrzi a közvetlen metódus hasznos adatában átadott paramétereket, majd aszinkron módon futtatja a belső vezérlőprogram frissítési szimulációját:

     ```javascript
     function onFirmwareUpdate(request, response) {
       // Get the requested firmware version from the JSON request body
       var firmwareVersion = request.payload.Firmware;
       var firmwareUri = request.payload.FirmwareUri;
      
       // Ensure we got a firmware values
       if (!firmwareVersion || !firmwareUri) {
         response.send(400, 'Missing firmware value', function(err) {
           if (err) console.error('Error sending method response :\n' + err.toString());
           else console.log('400 Response to method \'' + request.methodName + '\' sent successfully.');
         });
       } else {
         // Respond the cloud app for the device method
         response.send(200, 'Firmware update started.', function(err) {
           if (err) console.error('Error sending method response :\n' + err.toString());
           else {
             console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');

             // Run the simulated firmware update flow
             runFirmwareUpdateFlow(firmwareVersion, firmwareUri);
           }
         });
       }
     }
     ```

1. Adja hozzá a következő függvényt egy hosszú ideig futó belső vezérlőprogram-frissítési folyamat szimulálásához, amely a folyamatot visszajelenti a megoldáshoz:

     ```javascript
     // Simulated firmwareUpdate flow
     function runFirmwareUpdateFlow(firmwareVersion, firmwareUri) {
       console.log('Simulating firmware update flow...');
       console.log('> Firmware version passed: ' + firmwareVersion);
       console.log('> Firmware URI passed: ' + firmwareUri);
       async.waterfall([
         function (callback) {
           console.log("Image downloading from " + firmwareUri);
           var patch = {
             FirmwareUpdateStatus: 'Downloading image..'
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(10000, callback);
         },
         function (callback) {
           console.log("Downloaded, applying firmware " + firmwareVersion);
           deviceOnline = false;
           var patch = {
             FirmwareUpdateStatus: 'Applying firmware..',
             Online: false
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(8000, callback);
         },
         function (callback) {
           console.log("Rebooting");
           var patch = {
             FirmwareUpdateStatus: 'Rebooting..'
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(10000, callback);
         },
         function (callback) {
           console.log("Firmware updated to " + firmwareVersion);
           deviceOnline = true;
           var patch = {
             FirmwareUpdateStatus: 'Firmware updated',
             Online: true,
             Firmware: firmwareVersion
           };
           reportUpdateThroughTwin(patch, callback);
           callback(null);
         }
       ], function(err) {
         if (err) {
           console.error('Error in simulated firmware update flow: ' + err.message);
         } else {
           console.log("Completed simulated firmware update flow");
         }
       });

       // Helper function to update the twin reported properties.
       function reportUpdateThroughTwin(patch, callback) {
         console.log("Sending...");
         console.log(JSON.stringify(patch, null, 2));
         client.getTwin(function(err, twin) {
           if (!err) {
             twin.properties.reported.update(patch, function(err) {
               if (err) callback(err);
             });      
           } else {
             if (err) callback(err);
           }
         });
       }

       function sleep(milliseconds, callback) {
         console.log("Simulate a delay (milleseconds): " + milliseconds);
         setTimeout(function () {
           callback(null);
         }, milliseconds);
       }
     }
     ```

1. Adja hozzá a következő kódot telemetriai adatok küldéséhez a megoldásba. Az ügyfélalkalmazás tulajdonságokat ad az üzenethez az üzenetséma azonosításához:

     ```javascript
     function sendTelemetry(data, schema) {
       if (deviceOnline) {
         var d = new Date();
         var payload = JSON.stringify(data);
         var message = new Message(payload);
         message.properties.add('iothub-creation-time-utc', d.toISOString());
         message.properties.add('iothub-message-schema', schema);

         console.log('Sending device message data:\n' + payload);
         client.sendEvent(message, printErrorFor('send event'));
       } else {
         console.log('Offline, not sending telemetry');
       }
     }
     ```

1. Adja hozzá a következő kódot az ügyfélpéldány létrehozásához:

     ```javascript
     var client = Client.fromConnectionString(connectionString, Protocol);
     ```

1. Adja hozzá a következő kódot:

    * Nyissa meg a kapcsolatot.
    * Állítson be egy kezelőt a kívánt tulajdonságokhoz.
    * Jelentett tulajdonságok küldése.
    * Regisztrálják a közvetlen metódusok kezelőit. A minta külön kezelőt használ a belső vezérlőprogram frissítési közvetlen metódusához.
    * Kezdje el elküldeni a telemetriai adatokat.

      ```javascript
      client.open(function (err) {
      if (err) {
        printErrorFor('open')(err);
      } else {
        // Create device Twin
        client.getTwin(function (err, twin) {
          if (err) {
            console.error('Could not get device twin');
          } else {
            console.log('Device twin created');

            twin.on('properties.desired', function (delta) {
              // Handle desired properties set by solution
              console.log('Received new desired properties:');
              console.log(JSON.stringify(delta));
            });

            // Send reported properties
            twin.properties.reported.update(reportedProperties, function (err) {
              if (err) throw err;
              console.log('Twin state reported');
            });

            // Register handlers for all the method names we are interested in.
            // Consider separate handlers for each method.
            client.onDeviceMethod('Reboot', onDirectMethod);
            client.onDeviceMethod('FirmwareUpdate', onFirmwareUpdate);
            client.onDeviceMethod('EmergencyValveRelease', onDirectMethod);
            client.onDeviceMethod('IncreasePressure', onDirectMethod);
          }
        });

        // Start sending telemetry
        var sendDeviceTelemetry = setInterval(function () {
          temperature += generateRandomIncrement();
          pressure += generateRandomIncrement();
          humidity += generateRandomIncrement();
          var data = {
            'temperature': temperature,
            'temperature_unit': temperatureUnit,
            'humidity': humidity,
            'humidity_unit': humidityUnit,
            'pressure': pressure,
            'pressure_unit': pressureUnit
          };
          sendTelemetry(data, schema)
        }, 5000);

        client.on('error', function (err) {
          printErrorFor('client')(err);
          if (sendTemperatureInterval) clearInterval(sendTemperatureInterval);
          if (sendHumidityInterval) clearInterval(sendHumidityInterval);
          if (sendPressureInterval) clearInterval(sendPressureInterval);
          client.close(printErrorFor('client.close'));
        });
      }
      });
      ```

1. Mentse a módosításokat a **remote_monitoring.js fájlba.**

1. A mintaalkalmazás elindításához futtassa a következő parancsot a parancssorból:

     ```cmd/sh
     node remote_monitoring.js
     ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
