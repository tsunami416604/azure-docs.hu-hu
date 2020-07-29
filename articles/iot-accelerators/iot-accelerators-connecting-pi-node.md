---
title: Málna PI összekötése távoli figyelési megoldással – Node.js – Azure | Microsoft Docs
description: Azt ismerteti, hogyan csatlakoztathatók a málna PI-eszközök a távoli figyelési megoldási gyorsító eszközhöz Node.jsban írt alkalmazás használatával.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: dobett
ms.custom: mqtt
ms.openlocfilehash: 9335c45688752ea41801e988157740f4170cfcb4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81683942"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-nodejs"></a>A málna PI-eszköz csatlakoztatása a távoli figyelési megoldáshoz (Node.js)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ebből az oktatóanyagból megtudhatja, hogyan csatlakoztatható valódi eszköz a távoli figyelési megoldáshoz. Ebben az oktatóanyagban a Node.js-t használja, ami jó megoldás a minimális erőforrás-korlátozásokkal rendelkező környezetekhez.

Ha inkább egy eszközt szeretne szimulálni, tekintse meg [az új szimulált eszköz létrehozását és tesztelését](iot-accelerators-remote-monitoring-create-simulated-device.md)ismertető témakört.

### <a name="required-hardware"></a>Szükséges hardver

Egy asztali számítógép, amely lehetővé teszi, hogy távolról kapcsolódjon a málna PI parancssorához.

[Microsoft IoT Starter Kit málna PI 3](https://azure.microsoft.com/develop/iot/starter-kits/) vagy azzal egyenértékű összetevőkhöz. Ez az oktatóanyag a készlet következő elemeit használja:

- Málna PI 3
- MicroSD-kártya (NOOBs)
- Egy USB-s mini-kábel
- Egy Ethernet-kábel

### <a name="required-desktop-software"></a>Szükséges asztali szoftverek

Az asztali gépen szükség van az SSH-ügyfélre, hogy távolról hozzáférhessen a parancssorhoz a málna PI-on.

- A Windows nem tartalmaz SSH-ügyfelet. A [Putty](https://www.putty.org/)használatát javasoljuk.
- A legtöbb Linux-disztribúció és Mac OS tartalmazza a parancssori SSH-segédprogramot. További információ: [SSH Linux vagy Mac os használatával](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Szükséges málna PI szoftver

Ha még nem tette meg, telepítse Node.js 4.0.0 vagy újabb verziót a málna PI-ra. A következő lépések bemutatják, hogyan telepítheti Node.js v6-ot a málna PI-re:

1. Kapcsolódjon az málna PI-hez a használatával `ssh` . További információ: [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) a [málna PI webhelyén](https://www.raspberrypi.org/).

1. A következő parancs használatával frissítheti a málna PI-t:

    ```sh
    sudo apt-get update
    ```

1. A következő parancsokkal távolítsa el a Node.js meglévő telepítését a málna PI-ből:

    ```sh
    sudo apt-get remove nodered -y
    sudo apt-get remove nodejs nodejs-legacy -y
    sudo apt-get remove npm  -y
    ```

1. A következő parancs használatával töltse le és telepítse a Node.js v6-ot a málna PI-re:

    ```sh
    curl -sL https://deb.nodesource.com/setup_6.x | sudo bash -
    sudo apt-get install nodejs npm
    ```

1. A következő parancs használatával ellenőrizheti, hogy sikeresen telepítette-e Node.js v 6.11.4:

    ```sh
    node --version
    ```

## <a name="create-a-nodejs-solution"></a>Node.js megoldás létrehozása

Végezze el a következő lépéseket a `ssh` málna PI-val létesített kapcsolódás használatával:

1. Hozzon létre egy nevű mappát `remotemonitoring` a Home mappában a málna PI-ben. Navigáljon a parancssorba a következő mappába:

    ```sh
    cd ~
    mkdir remotemonitoring
    cd remotemonitoring
    ```

1. A minta alkalmazás végrehajtásához szükséges csomagok letöltéséhez és telepítéséhez futtassa a következő parancsokat:

    ```sh
    npm install async azure-iot-device azure-iot-device-mqtt
    ```

1. A `remotemonitoring` mappában hozzon létre egy **remote_monitoring.js**nevű fájlt. Nyissa meg ezt a fájlt egy szövegszerkesztőben. A málna PI-ban használhatja a `nano` vagy a `vi` text szerkesztőt.

1. A **remote_monitoring.js** fájlban adja hozzá a következő `require` utasításokat:

    ```javascript
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var async = require('async');
    ```

1. Adja hozzá a következő változódeklarációkat az `require` utasítások után. Cserélje le a helyőrző értékét a `{device connection string}` távoli figyelési megoldásban kiépített eszközre feljegyzett értékre:

    ```javascript
    var connectionString = '{device connection string}';
    ```

1. Az alapszintű telemetria-adatok definiálásához adja hozzá a következő változókat:

    ```javascript
    var temperature = 50;
    var temperatureUnit = 'F';
    var humidity = 50;
    var humidityUnit = '%';
    var pressure = 55;
    var pressureUnit = 'psig';
    ```

1. Egyes tulajdonságértékek definiálásához adja hozzá a következő változókat:

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

1. Adja hozzá a következő változót a megoldásnak küldendő jelentett tulajdonságok meghatározásához. Ezek a tulajdonságok a webes felhasználói felületen megjelenítendő metaadatokat tartalmazzák:

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

1. A művelet eredményeinek kinyomtatásához adja hozzá a következő segítő funkciót:

    ```javascript
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. Adja hozzá a következő Helper-függvényt a telemetria-értékek véletlenszerű megadásához:

     ```javascript
     function generateRandomIncrement() {
         return ((Math.random() * 2) - 1);
     }
     ```

1. Adja hozzá a következő általános függvényt a közvetlen metódus hívásának kezeléséhez a megoldásból. A függvény megjeleníti a meghívott közvetlen metódus adatait, de ebben a példában nem módosítja az eszközt semmilyen módon. A megoldás közvetlen metódusok használatával cselekszik az eszközökön:

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

1. Adja hozzá a következő függvényt a **FirmwareUpdate** Direct metódus hívásának kezeléséhez a megoldásból. A függvény ellenőrzi, hogy a közvetlen metódus adattartalma megfelel-e az átadott paramétereknek, majd aszinkron módon futtatja a belső vezérlőprogram frissítési szimulációját:

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

1. Adja hozzá a következő függvényt egy hosszú ideig futó belső vezérlőprogram-frissítési folyamat szimulálásához, amely a megoldásnak visszalépést jelent:

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

1. Adja hozzá a következő kódot, hogy telemetria-adatküldés a megoldásnak. Az ügyfélalkalmazás tulajdonságok hozzáadásával azonosítja az üzenet-sémát:

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

1. Adja hozzá a következő kódot egy ügyfél-példány létrehozásához:

     ```javascript
     var client = Client.fromConnectionString(connectionString, Protocol);
     ```

1. Adja hozzá az alábbi kódot a következőhöz:

    * Nyissa meg a-kapcsolatokat.
    * Kezelő beállítása a kívánt tulajdonságokhoz.
    * Jelentett tulajdonságok küldése.
    * Regisztrálja a közvetlen metódusok kezelőit. A minta egy külön kezelőt használ a belső vezérlőprogram-frissítés közvetlen módszeréhez.
    * Telemetria küldésének megkezdése.

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

1. Mentse a módosításokat a **remote_monitoring.js** fájlba.

1. A minta alkalmazás indításához futtassa a következő parancsot a parancssorban a málna PI-ben:

     ```sh
     node remote_monitoring.js
     ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
