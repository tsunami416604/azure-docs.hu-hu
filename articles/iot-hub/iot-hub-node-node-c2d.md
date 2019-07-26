---
title: Felhőből az eszközre irányuló üzenetek az Azure IoT Hub (node) használatával | Microsoft Docs
description: A felhőből az eszközre irányuló üzenetek küldése egy Azure IoT hub-eszközről a Node. js-hez készült Azure IoT SDK-k használatával. Módosít egy szimulált eszközt a felhőből az eszközre irányuló üzenetek fogadására és a felhőből az eszközre irányuló üzenetek küldésére szolgáló háttérbeli alkalmazás módosítására.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.openlocfilehash: b1aa8f2ce7d271187657d57993032069639ca9c7
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404100"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-node"></a>Felhőből az eszközre irányuló üzenetek küldése IoT Hub (node)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Bevezetés

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a megbízható és biztonságos kétirányú kommunikációt több millió eszköz és egy megoldás hátterében. A [telemetria küldése az eszközről egy IoT hub](quickstart-send-telemetry-node.md) -gyors üzembe helyezési útmutató bemutatja, hogyan hozhat létre egy IoT hubot, kiépítheti a benne lévő eszköz identitását, valamint kódot készíthet egy szimulált eszközről, amely az eszközről a felhőbe irányuló üzeneteket küld.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a [telemetria küldött eszközről egy IoT hubhoz](quickstart-send-telemetry-node.md)épít. A következőket mutatja be:

* A megoldás hátterében a felhőből az eszközre irányuló üzeneteket a IoT Hub használatával egyetlen eszközre küldheti.
* A felhőből az eszközre irányuló üzenetek fogadása az eszközön.
* A megoldási háttérből kérjen kézbesítési visszaigazolást (*visszajelzés*) a IoT hub eszközről küldött üzenetekhez.

A felhőből az eszközre irányuló üzenetekről a [IoT hub fejlesztői útmutatójában](iot-hub-devguide-messaging.md)talál további információt.

Az oktatóanyag végén két Node. js-konzol alkalmazást futtat:

* A **SimulatedDevice**az alkalmazás egy olyan módosított verziója, amely a [telemetria küldése az eszközről egy IoT hubhoz](quickstart-send-telemetry-node.md), amely csatlakozik a IoT hubhoz, és fogadja a felhőből az eszközre irányuló üzeneteket.

* A **SendCloudToDeviceMessage**, amely egy felhőből az eszközre irányuló üzenetet küld a szimulált eszköz alkalmazásnak IoT hubon keresztül, majd megkapja a kézbesítési visszaigazolást.

> [!NOTE]
> A IoT Hub számos eszköz-platform és nyelv (például C, Java és JavaScript) támogatásával rendelkezik az Azure IoT Device SDK-k használatával. Az eszköznek az oktatóanyag kódjához való csatlakoztatásának részletes ismertetését, és általában az Azure IoT Hub az Azure [IoT fejlesztői](https://azure.microsoft.com/develop/iot)központját tekintheti meg.
>

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Node. js 10.0. x vagy újabb verzió.
* Aktív Azure-fiók. (Ha nincs fiókja, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial) .)

## <a name="receive-messages-in-the-simulated-device-app"></a>Üzenetek fogadása a szimulált eszköz alkalmazásban

Ebben a szakaszban módosítania kell a szimulált eszközt, amelyet a [telemetria küldése eszközről egy IoT-hubhoz](quickstart-send-telemetry-node.md) hozott létre, hogy fogadja a felhőből az eszközre irányuló üzeneteket az IoT hub-ból.

1. Egy szövegszerkesztővel nyissa meg a SimulatedDevice. js fájlt.

2. Módosítsa a **connectCallback** függvényt a IoT hub küldött üzenetek kezelésére. Ebben a példában az eszköz mindig a Complete  függvényt hívja meg, hogy értesítse IoT hub arról, hogy feldolgozta az üzenetet. A **connectCallback** függvény új verziója a következő kódrészlethez hasonlít:

    ```javascript
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
        client.on('message', function (msg) {
          console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
          client.complete(msg, printResultFor('completed'));
        });
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var temperature = 20 + (Math.random() * 15);
            var humidity = 60 + (Math.random() * 20);
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', temperature: temperature, humidity: humidity });
            var message = new Message(data);
            message.properties.add('temperatureAlert', (temperature > 30) ? 'true' : 'false');
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```
  
   > [!NOTE]
   > Ha MQTT vagy AMQP helyett HTTPS-t használ, akkor a **DeviceClient** -példányok ritkán keresnek IoT hub üzeneteket (kevesebb, mint 25 percenként). A MQTT, a AMQP és a HTTPS támogatásával, valamint a szabályozás IoT Hubával kapcsolatos további információkért tekintse meg a [IoT hub fejlesztői útmutató](iot-hub-devguide-messaging.md)című témakört.
   >

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

Ebből a cikkből megtudhatja, hogyan küldhet el egy háttér-szolgáltatást a felhőből az eszközre irányuló üzenetek küldéséhez a IoT hub használatával, amelyet a [telemetria küldése az eszközről az IoT hubhoz](quickstart-send-telemetry-node.md)hozott létre. A felhőből az eszközre irányuló üzenetek küldéséhez a szolgáltatásnak szüksége van a **szolgáltatás kapcsolódási** engedélyére. Alapértelmezés szerint minden IoT Hub a **szolgáltatás** nevű közös hozzáférési házirenddel jön létre, amely megadja ezt az engedélyt.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Felhőből az eszközre irányuló üzenet küldése

Ebben a szakaszban egy Node. js-konzol alkalmazást hoz létre, amely a felhőből az eszközre irányuló üzeneteket küld a szimulált eszköz alkalmazásnak. Szüksége lesz annak az eszköznek az eszköz-AZONOSÍTÓJÁRA, amelyet a [telemetria küldése az eszközről az IoT hub](quickstart-send-telemetry-node.md) -gyors üzembe helyezéshez adott meg. Szüksége lesz a korábban átmásolt IoT hub-kapcsolatok karakterláncára is a [IoT hub-kapcsolatok karakterláncának](#get-the-iot-hub-connection-string)beolvasása című részében.

1. Hozzon létre egy **sendcloudtodevicemessage**nevű üres mappát. A **sendcloudtodevicemessage** mappában hozzon létre egy Package. JSON fájlt a következő parancs parancssorba való beírásával. Fogadja el az összes alapértelmezett beállítást:

    ```shell
    npm init
    ```

2. A **sendcloudtodevicemessage** mappában a parancssorban futtassa a következő parancsot az **Azure-iothub** csomag telepítéséhez:

    ```shell
    npm install azure-iothub --save
    ```

3. Egy szövegszerkesztővel hozzon létre egy **SendCloudToDeviceMessage. js** fájlt a **SendCloudToDeviceMessage** mappában.

4. Adja hozzá a `require` következő utasításokat a **SendCloudToDeviceMessage. js** fájl elejéhez:

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Adja hozzá a következő kódot a **SendCloudToDeviceMessage. js** fájlhoz. Cserélje le a "{IOT hub-beli kapcsolatok karakterlánc}" és "{Device id}" helyőrző értékeket az IoT hub-beli kapcsolatok karakterláncára és a korábban feljegyzett eszköz AZONOSÍTÓra:

    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Adja hozzá a következő függvényt a művelet eredményeinek a konzolra való nyomtatásához:

    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Adja hozzá a következő függvényt a kézbesítési visszajelzési üzenetek kinyomtatásához a konzolon:

    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Adja hozzá a következő kódot, hogy üzenetet küldjön az eszköznek, és kezelje a visszajelzési üzenetet, amikor az eszköz tudomásul veszi a felhőből az eszközre irányuló üzenetet:

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

9. Mentse és zárjuk be a **SendCloudToDeviceMessage. js** fájlt.

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A **simulateddevice** mappában található parancssorban futtassa a következő parancsot, hogy telemetria küldjön a IoT Hubnek, és figyelje a felhőből az eszközre irányuló üzeneteket:

    ```shell
    node SimulatedDevice.js
    ```

    ![A szimulált eszköz alkalmazásának futtatása](./media/iot-hub-node-node-c2d/receivec2d.png)

2. A **sendcloudtodevicemessage** mappában egy parancssorban futtassa a következő parancsot egy felhőből az eszközre irányuló üzenet küldéséhez, és várjon a visszaigazolási visszajelzésre:

    ```shell
    node SendCloudToDeviceMessage.js
    ```

    ![Futtassa az alkalmazást a felhőből az eszközre irányuló parancs küldéséhez.](./media/iot-hub-node-node-c2d/sendc2d.png)

   > [!NOTE]
   > Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg újrapróbálkozási házirendet. Az éles kódban az újrapróbálkozási szabályzatokat (például az exponenciális leállítási) kell megvalósítani, ahogy azt a cikkben is ismertetjük, az [átmeneti hibák kezelésére](/azure/architecture/best-practices/transient-faults).
   >

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan küldhet és fogadhat üzeneteket a felhőből az eszközre.

Ha szeretné megtekinteni a IoT Hubt használó teljes körű megoldásokat, tekintse meg az [Azure IoT távoli monitorozási megoldásának gyorsítása](https://azure.microsoft.com/documentation/suites/iot-suite/)című témakört.

Ha többet szeretne megtudni a IoT Hub-megoldások fejlesztéséről, tekintse meg a [IoT hub fejlesztői útmutatót](iot-hub-devguide.md).
