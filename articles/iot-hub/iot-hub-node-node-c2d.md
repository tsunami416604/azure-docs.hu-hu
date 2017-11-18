---
title: "Az Azure IoT Hub (csomópont) felhő eszközre üzenetek |} Microsoft Docs"
description: "Hogyan küldhetők felhő-eszközre küldött üzenetek eszközökre az Azure IoT-központ az Azure IoT SDK for Node.js használatával. A szimulált eszköz alkalmazásnak, hogy a felhő-eszközre küldött üzenetek fogadására és módosíthat egy háttér-alkalmazást a felhőből eszközre küldéséhez módosítása."
services: iot-hub
documentationcenter: nodejs
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3ca8a78f-ade2-46e8-8a49-d5d599cdf1f1
ms.service: iot-hub
ms.devlang: javascript
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: dobett
ms.openlocfilehash: 80f65e8e7fe562030c1e39787b910e2564969882
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2017
---
# <a name="send-cloud-to-device-messages-with-iot-hub-node"></a>Az IoT-központ (csomópont) felhő eszközre-üzenetek
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Bevezetés
Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amellyel eszközök millióira közötti megbízható és biztonságos kétirányú kommunikáció engedélyezése és a megoldás háttérrendszere. A [Ismerkedés az IoT-központ] oktatóanyag bemutatja, hogyan létrehoz egy IoT-központot, azt egy eszközidentitás kiépítéséhez és kód egy szimulált eszköz alkalmazást, amelyet az eszköz a felhőbe küldött üzeneteket küld.

Ez az oktatóanyag épül [Ismerkedés az IoT-központ]. Megmutatja, hogyan számára:

* A megoldás háttérrendszeréhez, az IoT-központ keresztül egyetlen eszközt felhő eszközre üzeneteket küldeni.
* Az eszközön a felhőből eszközre üzeneteket fogadni.
* A megoldás háttérrendszeréhez, a kérelem kézbesítési nyugtázási (*visszajelzés*) IoT-központot egy eszközre küldött üzenetek esetében.

Felhő eszközre üzenetek további információt a [IoT Hub fejlesztői útmutató][IoT Hub developer guide - C2D].

Ez az oktatóanyag végén, futtatja a Node.js-konzol két alkalmazásokat:

* **SimulatedDevice**, az alkalmazás létrehozása a módosított változatát [Ismerkedés az IoT-központ], amely csatlakozik az IoT hub és felhő eszközre üzeneteket fogad.
* **SendCloudToDeviceMessage**, melyik felhőalapú eszközre üzenetet küld az IoT-központ szimulált eszköz alkalmazás, és a szállítási nyugtázási majd megkapja.

> [!NOTE]
> Az IoT-központ rendelkezik sok eszköz platformok és nyelvek (például C, Java és Javascript) keresztül Azure IoT eszközoldali SDK-k SDK támogatása. Csatlakoztassa az eszközt, az oktatóanyag kódot, és általában Azure IoT Hub kapcsolatos lépésenkénti útmutatót lásd: a [Azure IoT fejlesztői központ].
> 
> 

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* A Node.js 4.0.x vagy újabb verziója.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

## <a name="receive-messages-in-the-simulated-device-app"></a>A szimulált eszköz alkalmazás üzeneteket fogadni
Ebben a szakaszban módosítsa a szimulált eszköz alkalmazás létrehozott [Ismerkedés az IoT-központ] felhő eszközre üzenetek fogadása az IoT-központot.

1. Egy szövegszerkesztőben nyissa meg a SimulatedDevice.js fájlt.
2. Módosítsa a **connectCallback** működnek, mint az IoT-központ által küldött üzenetek kezeléséhez. Ebben a példában az eszköz mindig meghívja a **teljes** függvény értesíteni az IoT-központot, feldolgozta-e az üzenetet. Új verziójának a **connectCallback** függvény néz ki a következő kódrészletet:
   
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
   > Ha a átvitelhez MQTT vagy AMQP helyett a HTTPS PROTOKOLLT használja. a **DeviceClient** példány ellenőrzi az üzeneteket az IoT-központ ritkán (kevesebb mint 25 percenként). MQTT, az amqp-t és a HTTPS PROTOKOLLT támogatja, és az IoT-központ sávszélesség-szabályozási közötti különbségekről további információkért lásd: a [IoT Hub fejlesztői útmutató][IoT Hub developer guide - C2D].
   > 
   > 

## <a name="send-a-cloud-to-device-message"></a>Felhő eszközre üzenet küldése
Ebben a szakaszban egy Node.js-Konzolalkalmazás, amely felhő eszközre üzeneteket küld a szimulált eszköz alkalmazást hoz létre. A hozzáadott eszköz az Eszközazonosítót van szüksége a [Ismerkedés az IoT-központ] oktatóanyag. Az IoT-központ kapcsolati karakterlánc, amely megtalálható a központ is kell a [Azure-portálon].

1. Hozzon létre egy üres nevű **sendcloudtodevicemessage**. Az a **sendcloudtodevicemessage** mappa, hozzon létre egy package.json fájlt parancsot a parancssorba az alábbi parancs segítségével. Fogadja el az összes alapértelmezett beállítást:
   
    ```shell
    npm init
    ```
2. A parancssorba a **sendcloudtodevicemessage** mappa telepítéséhez a következő parancsot a **azure-IOT hubbal** csomag:
   
    ```shell
    npm install azure-iothub --save
    ```
3. Egy szövegszerkesztő használatával hozzon létre egy **SendCloudToDeviceMessage.js** fájlt a **sendcloudtodevicemessage** mappa.
4. Adja hozzá a következő `require` elején utasítások a **SendCloudToDeviceMessage.js** fájlt:
   
    ```javascript
    'use strict';
   
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```
5. Adja hozzá a következő kódot a **SendCloudToDeviceMessage.js** fájlt. Cserélje le a "{iot hub kapcsolati karakterlánc}" helyőrző értékét az IoT-központ kapcsolati karakterlánccal létrehozott központ a [Ismerkedés az IoT-központ] oktatóanyag. A "{eszközazonosító}" helyőrzőt cserélje le a hozzáadott eszköz az Eszközazonosítót az [Ismerkedés az IoT-központ] oktatóanyag:
   
    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';
   
    var serviceClient = Client.fromConnectionString(connectionString);
    ```
6. Adja hozzá a következő függvény nyomtatni művelet eredményeit a konzolhoz:
   
    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```
7. Adja hozzá a következő függvény nyomtatni kézbesítési visszajelzés üzenetek a konzolhoz:
   
    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```
8. Adja hozzá a következő kódot üzenetet küldeni az eszközt, és a visszajelzés üzenet kezelését, amikor az eszköz elfogadja a felhőből eszközre üzenet:
   
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

1. A parancssorban a **simulateddevice** mappa, futtassa a következő parancsot, telemetriai adatokat küldhet az IoT-központot, és a felhő-eszközre küldött üzenetek figyelésére:
   
    ```shell
    node SimulatedDevice.js 
    ```
   
    ![A szimulált eszköz alkalmazás futtatása][img-simulated-device]
2. A parancsot a parancssorba a **sendcloudtodevicemessage** mappa, a következő parancsot a felhőből eszközre küldött, és várjon, amíg a visszaigazolás-visszajelzés:
   
    ```shell
    node SendCloudToDeviceMessage.js 
    ```
   
    ![Futtassa az alkalmazásnak, hogy a felhő eszközre vonatkozó parancs küldése][img-send-command]
   
   > [!NOTE]
   > Az egyszerűség kedvéért tartozó szakét Ez az oktatóanyag nem valósítja meg a bármely újrapróbálkozási házirendje. Az éles kódban, meg kell valósítania újrapróbálkozási házirendek (például az exponenciális leállítási), az MSDN-cikkben leírtak [átmeneti hiba kezelése].
   > 
   > 

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóprogramban megismerte felhő eszközre üzeneteket küldjön és fogadjon. 

Példák teljes végpontok közötti megoldások, amelyek használják az IoT-központot, lásd: [Azure IoT Suite].

Az IoT hubbal megoldások fejlesztésével kapcsolatos további tudnivalókért tekintse meg a [IoT Hub fejlesztői útmutató].

<!-- Images -->
[img-simulated-device]: media/iot-hub-node-node-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-node-node-c2d/sendc2d.png

<!-- Links -->

[Ismerkedés az IoT-központ]: iot-hub-node-node-getstarted.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[IoT Hub fejlesztői útmutató]: iot-hub-devguide.md
[Azure IoT fejlesztői központ]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[átmeneti hiba kezelése]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure-portálon]: https://portal.azure.com
[Azure IoT Suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
