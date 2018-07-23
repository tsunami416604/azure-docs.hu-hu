---
title: Felhőből az eszközre irányuló üzeneteket az Azure IoT Hub (Node) szolgáltatással |} A Microsoft Docs
description: Annak a felhőből az eszközre irányuló üzeneteket küld egy eszköz az Azure IoT SDK-k használata a node.js-hez készült Azure IoT hubról. Módosítja egy szimulált eszközalkalmazás felhőből az eszközre irányuló üzenetek fogadása és módosíthat egy háttér-alkalmazást a felhőből az eszközre irányuló üzenetek küldéséhez.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.author: dobett
ms.openlocfilehash: 61b1dd31039df0db777e7e35d849889047a6588d
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186682"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-node"></a>Az IoT Hub (Node) szolgáltatással felhőből az eszközre irányuló üzenetek küldéséhez
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Bevezetés
Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amellyel engedélyezheti a megbízható és biztonságos kétirányú kommunikációt több millió eszköz között, és megoldást biztosít a háttérrendszer. A [IoT Hub használatának első lépései] az oktatóanyag bemutatja, hogyan hozzon létre egy IoT hubot, azt az eszközidentitás létrehozását és kód az eszköz a felhőbe irányuló üzeneteket küld egy szimulált eszközalkalmazás.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ebben az oktatóanyagban épül [IoT Hub használatának első lépései]. Azt mutatja, hogy:

* A megoldás háttérrendszerének, a felhőből az eszközre irányuló üzenetek küldése IoT hubon keresztül egy adott eszköz.
* Az eszközön a felhőből az eszközre irányuló üzeneteket fogadni.
* A megoldás háttérrendszerének, a kérelmek kézbesítési nyugtázás (*visszajelzés*) az IoT Hub az eszközökre küldött üzenetek.

A felhőből az eszközre irányuló üzenetek további tájékoztatást talál a [IoT Hub fejlesztői útmutatójának][IoT Hub developer guide - C2D].

Ez az oktatóanyag végén két Node.js-konzolalkalmazással futtassa:

* **SimulatedDevice**, a létrehozott alkalmazás egy módosított verziója [IoT Hub használatának első lépései], amely csatlakozik az IoT hubhoz, és megkapja a felhőből az eszközre.
* **SendCloudToDeviceMessage**, amely a felhőből az eszközre üzenetet küld az IoT hubon keresztül a szimulált eszközalkalmazásnak, és annak kézbesítési nyugtázási majd kap.

> [!NOTE]
> Az IoT Hub SDK számos eszközplatformok és nyelveken (például a C, Java és Javascript) keresztül az Azure IoT eszközoldali SDK-k támogatással rendelkezik. Az eszköz csatlakoztatása, ebben az oktatóanyagban a kódot, és általában az Azure IoT hubba a részletes útmutatót lásd: a [Azure IoT fejlesztői központ].
> 
> 

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* A Node.js 4.0.x vagy újabb verziója.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

## <a name="receive-messages-in-the-simulated-device-app"></a>Üzenetek fogadása a szimulált eszköz alkalmazásban
Ebben a szakaszban módosítsa a szimulált eszközalkalmazás létrehozott [IoT Hub használatának első lépései] felhőből az eszközre irányuló üzenetek fogadása az IoT hubról.

1. Egy szövegszerkesztővel nyissa meg a SimulatedDevice.js fájlt.
2. Módosítsa a **connectCallback** függvény az IoT hubról küldött üzenetek kezelésére. Ebben a példában az eszköz mindig meghívja a **teljes** függvény sikeresen feldolgozta az üzenetet az IoT Hub értesíteni. Új verziójának a **connectCallback** függvény a következő kódrészlethez hasonló néz ki:
   
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
   > Ha az átvitelhez mqtt-ről vagy AMQP helyett a HTTPS PROTOKOLLT használja. a **DeviceClient** példány ellenőrzi az üzeneteket az IoT hub felől ritkán (kevesebb mint 25 percenként). MQTT, AMQP és a HTTPS-támogatás és az IoT Hub szabályozás közötti különbségekkel kapcsolatos további információkért lásd: a [IoT Hub fejlesztői útmutatójának][IoT Hub developer guide - C2D].
   > 
   > 

## <a name="send-a-cloud-to-device-message"></a>Felhőből az eszközre irányuló üzenet küldése
Ebben a szakaszban egy Node.js-konzolalkalmazást, amely a felhőből az eszközre irányuló üzeneteket küld a szimulált eszközalkalmazás létrehozása. Az eszköz azonosítója, az eszköz a hozzáadott van szüksége a [IoT Hub használatának első lépései] oktatóanyag. Emellett az IoT Hub kapcsolati karakterláncra, amely találhatja meg a hub a [Azure Portal].

1. Hozzon létre egy nevű üres mappát **sendcloudtodevicemessage**. Az a **sendcloudtodevicemessage** mappában hozzon létre egy package.json fájlt a következő parancsot a parancssorba. Fogadja el az összes alapértelmezett beállítást:
   
    ```shell
    npm init
    ```
2. A parancssorban a **sendcloudtodevicemessage** mappában futtassa a következő paranccsal telepíthető a **azure-iothub** csomag:
   
    ```shell
    npm install azure-iothub --save
    ```
3. Egy szövegszerkesztővel hozzon létre egy **SendCloudToDeviceMessage.js** fájlt a **sendcloudtodevicemessage** mappát.
4. Adja hozzá a következő `require` elején található utasításokat a **SendCloudToDeviceMessage.js** fájlt:
   
    ```javascript
    'use strict';
   
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```
5. Adja hozzá a következő kódot a **SendCloudToDeviceMessage.js** fájlt. A "{iot hub kapcsolati karakterlánc}" helyőrző értékét cserélje le a létrehozott IoT Hub kapcsolati karakterláncára az [IoT Hub használatának első lépései] oktatóanyag. A "{device id}" helyőrzőt cserélje le az eszköz azonosítója, az eszköz meg a következő lépésben a [IoT Hub használatának első lépései] oktatóanyag:
   
    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';
   
    var serviceClient = Client.fromConnectionString(connectionString);
    ```
6. Adja hozzá a következő függvényt nyomtatása művelet eredményeket a konzolon:
   
    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```
7. Adja hozzá a következő függvényt kézbesítési visszajelzéseket kiírásához a konzolhoz:
   
    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```
8. Adja hozzá a következő kódot egy üzenet küldéséhez az eszköz és az eszközre a felhőből az eszközre irányuló üzenet nyugtázza a visszajelzés üzenet kezelését:
   
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
9. Mentse és zárja be **SendCloudToDeviceMessage.js** fájlt.

## <a name="run-the-applications"></a>Az alkalmazások futtatása
Most már készen áll az alkalmazások futtatására.

1. A parancssorban a **simulateddevice** mappában futtassa a következő parancsot, hogy telemetriát küldjön az IoT Hub és a felhőből az eszközre irányuló üzenetek figyeléséhez:
   
    ```shell
    node SimulatedDevice.js 
    ```
   
    ![A szimulált eszközalkalmazás futtatása][img-simulated-device]
2. A parancsot a parancssorba a **sendcloudtodevicemessage** mappában futtassa a következő parancsot a felhőből az eszközre irányuló üzenetküldés, és várjon, amíg a visszaigazoló visszajelzés:
   
    ```shell
    node SendCloudToDeviceMessage.js 
    ```
   
    ![Futtassa az alkalmazást a felhőből az eszközre irányuló parancs küldése][img-send-command]
   
   > [!NOTE]
   > Az egyszerűség kedvéért a rizspálinkát Ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket (például egy exponenciális leállítást), az MSDN-cikkben leírtak implementálandó [Átmeneti hibák kezelése].
   > 
   > 

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtudhatta, hogyan a felhőből az eszközre irányuló üzenetek küldése és fogadása. 

Példák teljes, végpontok közötti megoldások, amely az IoT Hub használata a megtekintéséhez lásd: [Az Azure IoT távoli figyelési megoldásgyorsító].

Az IoT Hub megoldások fejlesztésével kapcsolatos további tudnivalókért tekintse meg a [Az IoT Hub fejlesztői útmutató].

<!-- Images -->
[img-simulated-device]: media/iot-hub-node-node-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-node-node-c2d/sendc2d.png

<!-- Links -->

[IoT Hub használatának első lépései]: quickstart-send-telemetry-node.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[Az IoT Hub fejlesztői útmutató]: iot-hub-devguide.md
[Azure IoT fejlesztői központ]: http://azure.microsoft.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[Átmeneti hibák kezelése]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure Portal]: https://portal.azure.com
[Az Azure IoT távoli figyelési megoldásgyorsító]: https://azure.microsoft.com/documentation/suites/iot-suite/
