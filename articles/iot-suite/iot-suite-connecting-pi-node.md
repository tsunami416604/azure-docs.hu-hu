---
title: "A node.js - Azure távoli megfigyeléshez Raspberry Pi kiépítése |} Microsoft Docs"
description: "Ismerteti egy málna Pi eszköz csatlakoztatása az Azure IoT Suite előre konfigurált távoli figyelési megoldást Node.js nyelven írt alkalmazás segítségével."
services: iot-suite
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2017
ms.author: dobett
ms.openlocfilehash: d401dde25bf4ab430ac045fb6cfd90050a7ec2e7
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>A Pi málna eszköz csatlakozni a távoli felügyeleti előkonfigurált megoldás (Node.js)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ez az oktatóanyag bemutatja, hogyan egy fizikai eszköz csatlakozni a távoli felügyeleti előkonfigurált megoldás. Ebben az oktatóanyagban akkor az Node.js, amely minimális erőforrás-korlátozásokkal rendelkező környezetek jó választás.

### <a name="required-hardware"></a>Szükséges hardver

Ahhoz, hogy a parancssor a málna Pi a távoli csatlakozás egy asztali számítógépen.

[Microsoft IoT Starter Kit málna Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) vagy ezzel egyenértékű összetevőket. Ez az oktatóanyag a csomagot a következő elemeket használja:

- Raspberry Pi 3
- (A NOOBS) MicroSD-kártyán
- A USB Mini-kábellel
- Kábel

### <a name="required-desktop-software"></a>Szükséges asztali szoftverek

SSH-ügyfél van szüksége az asztali gépen ahhoz, hogy a parancssor a málna Pi a érheti el távolról.

- A Windows tartalmaz egy SSH-ügyfél. Azt javasoljuk, [PuTTY](http://www.putty.org/).
- A legtöbb Linux terjesztéseket, a Mac OS közé tartoznak az SSH parancssori segédprogramot. További információkért lásd: [SSH használatával Linux és Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Szükséges málna Pi szoftver

Ha még nem tette meg, telepítse a Node.js verzió 4.0.0, vagy később a málna Pi. A következő lépések bemutatják a Node.js v6.11.4 telepíthető a málna Pi:

1. Kapcsolódás a málna Pi `ssh`. További információkért lásd: [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) a a [málna Pi webhely](https://www.raspberrypi.org/).

1. A málna Pi frissítéséhez használja a következő parancsot:

    ```sh
    sudo apt-get update
    ```

1. Az alábbi parancs segítségével töltse le a Node.js bináris fájlok a málna Pi:

    ```sh
    wget https://nodejs.org/dist/v6.11.4/node-v6.11.4-linux-armv7l.tar.gz
    ```

1. A következő paranccsal telepítse a bináris fájlok:

    ```sh
    sudo tar -C /usr/local --strip-components 1 -xzf node-v6.11.4-linux-armv7l.tar.gz
    ```

1. A következő paranccsal ellenőrizheti a Node.js v6.11.4 sikeresen telepítette:

    ```sh
    node --version
    ```

## <a name="create-a-nodejs-solution"></a>Node.js megoldás létrehozása

Az alábbi lépésekkel használatával a `ssh` a málna Pi kapcsolatot:

1. Hozzon létre egy nevű `RemoteMonitoring` a málna Pi a saját mappájában. Keresse meg a mappát, a parancssorban:

    ```sh
    cd ~
    mkdir RemoteMonitoring
    cd RemoteMonitoring
    ```

1. Töltse le és telepítse a csomagokat, végre kell hajtania a mintaalkalmazást, futtassa a következő parancsokat:

    ```sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Az a `RemoteMonitoring` mappa, hozzon létre egy nevű fájlt **remote_monitoring.js**. Nyissa meg ezt a fájlt egy szövegszerkesztőben. A málna Pi használhatja a `nano` vagy `vi` szöveg szerkesztők.

1. Az a **remote_monitoring.js** fájlt, adja hozzá a következő `require` utasításokat:

    ```nodejs
    'use strict';

    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

1. Adja hozzá a következő változódeklarációkat az `require` utasítások után. Cserélje le a helyőrző értékeket `{Device Id}` és `{Device Key}` értékekkel, az eszköz feljegyzett kiépítve a távoli felügyeleti megoldás. Cserélje le az IoT Hub állomásnevet a megoldásban való használatával `{IoTHub Name}`. Például, ha az IoT Hub állomásnevet `contoso.azure-devices.net`, cserélje le `{IoTHub Name}` rendelkező `contoso`:

    ```nodejs
    var connectionString = 'HostName={IoTHub Name}.azure-devices.net;DeviceId={Device Id};SharedAccessKey={Device Key}';
    var deviceId = ConnectionString.parse(connectionString).DeviceId;
    ```

1. Néhány alapvető telemetriai adatok megadásához adja hozzá a következő változókat:

    ```nodejs
    var temperature = 50;
    var temperatureUnit = 'F';
    var humidity = 50;
    var humidityUnit = '%';
    var pressure = 55;
    var pressureUnit = 'psig';
    ```

1. Néhány tulajdonság értékek megadásához adja hozzá a következő változókat:

    ```nodejs
    var temperatureSchema = 'chiller-temperature;v1';
    var humiditySchema = 'chiller-humidity;v1';
    var pressureSchema = 'chiller-pressure;v1';
    var interval = "00:00:05";
    var deviceType = "Chiller";
    var deviceFirmware = "1.0.0";
    var deviceFirmwareUpdateStatus = "";
    var deviceLocation = "Building 44";
    var deviceLatitude = 47.638928;
    var deviceLongitude = -122.13476;
    ```

1. Adja hozzá a következő változót a megoldás küldendő jelentett tulajdonságainak definiálásához. E tulajdonságok közé tartozik a metaadatokra ahhoz, hogy milyen módszereket, és az eszköz telemetriai használja:

    ```nodejs
    var reportedProperties = {
      "Protocol": "MQTT",
      "SupportedMethods": "Reboot,FirmwareUpdate,EmergencyValveRelease,IncreasePressure",
      "Telemetry": {
        "TemperatureSchema": {
          "Interval": interval,
          "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\"}",
          "MessageSchema": {
            "Name": temperatureSchema,
            "Format": "JSON",
            "Fields": {
              "temperature": "Double",
              "temperature_unit": "Text"
            }
          }
        },
        "HumiditySchema": {
          "Interval": interval,
          "MessageTemplate": "{\"humidity\":${humidity},\"humidity_unit\":\"${humidity_unit}\"}",
          "MessageSchema": {
            "Name": humiditySchema,
            "Format": "JSON",
            "Fields": {
              "humidity": "Double",
              "humidity_unit": "Text"
            }
          }
        },
        "PressureSchema": {
          "Interval": interval,
          "MessageTemplate": "{\"pressure\":${pressure},\"pressure_unit\":\"${pressure_unit}\"}",
          "MessageSchema": {
            "Name": pressureSchema,
            "Format": "JSON",
            "Fields": {
              "pressure": "Double",
              "pressure_unit": "Text"
            }
          }
        }
      },
      "Type": deviceType,
      "Firmware": deviceFirmware,
      "FirmwareUpdateStatus": deviceFirmwareUpdateStatus,
      "Location": deviceLocation,
      "Latitude": deviceLatitude,
      "Longitude": deviceLongitude
    }
    ```

1. A művelet eredménye nyomtatni, hozzáadhatja az alábbi:

    ```nodejs
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. Adja hozzá a következő segítő függvény használatával a telemetriai adatok értékek ügyfélfuttatási:

    ```nodejs
    function generateRandomIncrement() {
        return ((Math.random() * 2) - 1);
    }
    ```

1. Adja hozzá a következő függvény közvetlen metódushívások a megoldásban való kezelése. A megoldás közvetlen módszert használ, hogy intézkedjen eszközök:

    ```nodejs
    function onDirectMethod(request, response) {
      // Implement logic asynchronously here.
      console.log('Simulated ' + request.methodName);

      // Complete the response
      response.send(200, request.methodName + ' was called on the device', function (err) {
        if (!!err) {
          console.error('An error ocurred when sending a method response:\n' +
            err.toString());
        } else {
          console.log('Response to method \'' + request.methodName +
            '\' sent successfully.');
        }
      });
    }
    ```

1. Adja hozzá a következő kódot a telemetrikus adatokat küldeni a megoldás. Az ügyfélalkalmazás azonosítására az üzenet séma az üzenet tulajdonságainak hozzáadása:

    ```node.js
    function sendTelemetry(data, schema) {
      var d = new Date();
      var payload = JSON.stringify(data);
      var message = new Message(payload);
      message.properties.add('$$CreationTimeUtc', d.toISOString());
      message.properties.add('$$MessageSchema', schema);
      message.properties.add('$$ContentType', 'JSON');

      console.log('Sending device message data:\n' + payload);
      client.sendEvent(message, printErrorFor('send event'));
    }
    ```

1. Adja hozzá az ügyfél-példány létrehozása a következő kódot:

    ```nodejs
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

1. Adja hozzá a következő kódot:

    - Nyissa meg a kapcsolatot.
    - A kezelő kívánt tulajdonságok beállítása.
    - Jelentett tulajdonságok küldése.
    - A közvetlen módszer kezelők regisztrálni.
    - Indítsa el a telemetriai adatok küldését.

    ```nodejs
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
              console.log('twin state reported');
            });

            // Register handlers for all the method names we are interested in.
            // Consider separate handlers for each method.
            client.onDeviceMethod('Reboot', onDirectMethod);
            client.onDeviceMethod('FirmwareUpdate', onDirectMethod);
            client.onDeviceMethod('EmergencyValveRelease', onDirectMethod);
            client.onDeviceMethod('IncreasePressure', onDirectMethod);
          }
        });

        // Start sending telemetry
        var sendTemperatureInterval = setInterval(function () {
          temperature += generateRandomIncrement();
          var data = {
            'temperature': temperature,
            'temperature_unit': temperatureUnit
          };
          sendTelemetry(data, temperatureSchema)
        }, 5000);

        var sendHumidityInterval = setInterval(function () {
          humidity += generateRandomIncrement();
          var data = {
            'humidity': humidity,
            'humidity_unit': humidityUnit
          };
          sendTelemetry(data, humiditySchema)
        }, 5000);

        var sendPressureInterval = setInterval(function () {
          pressure += generateRandomIncrement();
          var data = {
            'pressure': pressure,
            'pressure_unit': pressureUnit
          };
          sendTelemetry(data, pressureSchema)
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

1. A módosítások mentése a **remote_monitoring.js** fájlt.

1. A mintaalkalmazás elindításához futtassa a málna Pi parancsot a parancssorba a következő parancsot:

    ```sh
    node remote_monitoring.js
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
