---
title: Felhőből az eszközre irányuló üzenetek az Azure IoT Hub (Node) használatával | Microsoft dokumentumok
description: Felhőből az eszközre irányuló üzenetek küldése egy eszközre egy Azure IoT-központból az Azure IoT SDK-k node.js használatával. Egy szimulált eszközalkalmazást módosítasz a felhőből az eszközre irányuló üzenetek fogadásához, és módosíthat egy háttéralkalmazást a felhőből az eszközre irányuló üzenetek küldéséhez.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.openlocfilehash: 8071ddbc5f6073598daf0a08d359ccd19ccd1e4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110807"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-nodejs"></a>Felhőből az eszközre irányuló üzenetek küldése az IoT Hubbal (Node.js)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a megbízható és biztonságos kétirányú kommunikációt több millió eszköz és egy megoldás háttérkiszolgáló között. A [Telemetriaküldése egy eszközről egy IoT hub](quickstart-send-telemetry-node.md) rövid útmutató bemutatja, hogyan hozhat létre egy IoT hub, egy eszköz identitását, és a kód egy szimulált eszköz alkalmazás, amely elküldi az eszköz-felhő üzeneteket.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a [telemetriai adatok küldése eszközről egy IoT hubra](quickstart-send-telemetry-node.md)épül. Ez megmutatja, hogyan:

* A megoldás háttér-tartalék, felhőből az eszközre üzeneteket küldhet egyetlen eszközre az IoT Hubon keresztül.
* Felhőből az eszközre irányuló üzenetek fogadása az eszközön.
* A megoldás háttérrendszeréről kérje a kézbesítési nyugtázást (*visszajelzést)* az IoT Hubról az eszközre küldött üzenetekhez.

A felhőből az eszközre irányuló üzenetekről az [IoT Hub fejlesztői útmutatójában](iot-hub-devguide-messaging.md)talál további információt.

Az oktatóanyag végén két Node.js konzolalkalmazást futtat:

* **SimulatedDevice**, az alkalmazás módosított verziója létrehozott [telemetriai adatok küldése egy eszközről egy IoT hub,](quickstart-send-telemetry-node.md)amely csatlakozik az IoT hubhoz, és fogadja a felhőből az eszközre üzeneteket.

* **SendCloudToDeviceMessage**, amely egy felhőből eszközre üzenetet küld a szimulált eszköz alkalmazás ioT Hubon keresztül, majd megkapja a kézbesítési nyugtázást.

> [!NOTE]
> Az IoT Hub sdk-támogatással rendelkezik számos eszközplatformhoz és nyelvhez (beleértve a C, Java, Python és Javascript- et) az Azure IoT-eszköz SDK-kon keresztül. Az Azure [IoT Developer Center](https://azure.microsoft.com/develop/iot)című témakörben olvashat részletesen arról, hogyan csatlakoztathatja az eszközt az oktatóanyag kódjához, és általában az Azure IoT Hubhoz.
>

## <a name="prerequisites"></a>Előfeltételek

* Node.js 10.0.x vagy újabb verzió. [A fejlesztői környezet előkészítése](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) leírja, hogyan telepítheti a Node.js-t ehhez az oktatóanyaghoz Windows vagy Linux rendszeren.

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial) néhány perc alatt.)

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. A cikkben szereplő eszközminta az MQTT protokollt használja, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben le van tiltva. A probléma megoldásáról további információt és a probléma megoldásáról a [Csatlakozás az IoT Hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)című témakörben talál.

## <a name="receive-messages-in-the-simulated-device-app"></a>Üzenetek fogadása a szimulált eszközalkalmazásban

Ebben a szakaszban módosítja a szimulált eszköz alkalmazást, amelyet létrehozott [telemetriai adatok küldése egy eszközről egy IoT hub](quickstart-send-telemetry-node.md) felhőből az eszközre üzeneteket az IoT hub.

1. Szövegszerkesztő használatával nyissa meg a **SimulatedDevice.js** fájlt. Ez a fájl az **iot-hub\Quickstarts\simulated-device** mappában található a Node.js mintakód gyökérmappájából, amelyet az eszközről egy IOt hub rövid útmutatóba töltött el, a [Telemetria küldése az eszközről.](quickstart-send-telemetry-node.md)

2. Regisztráljon egy kezelőt az eszközügyféllel az IoT Hubról küldött üzenetek fogadásához. Adja hozzá `client.on` a hívást közvetlenül az eszközügyfelet létrehozó sor után, a következő kódrészletben:

    ```javascript
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);

    client.on('message', function (msg) {
      console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
      client.complete(msg, function (err) {
        if (err) {
          console.error('complete error: ' + err.toString());
        } else {
          console.log('complete sent');
        }
      });
    });
    ```

    Ebben a példában az eszköz meghívja a **teljes** függvényt, hogy értesítse az IoT Hubot, hogy feldolgozta az üzenetet. A **hívás nem** szükséges, ha MQTT átvitelt használ, és elhagyható. Https és AMQP esetén szükséges.
  
   > [!NOTE]
   > Ha https-t használ az MQTT vagy az AMQP helyett átvitelként, a **DeviceClient-példány** ritkán (kevesebb, mint 25 percenként) ellenőrzi az IoT Hubból érkező üzeneteket. Az MQTT, az AMQP és a HTTPS-támogatás és az IoT Hub-szabályozás közötti különbségekről az [IoT Hub fejlesztői útmutatójában](iot-hub-devguide-messaging.md)olvashat bővebben.
   >

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub kapcsolati karakterláncának beszereznie

Ebben a cikkben egy háttérszolgáltatás létrehozása felhőből az eszközre üzenetek küldéséhez az IoT hub on létrehozott [Telemetriai adatok küldése egy eszközről egy IoT hub.](quickstart-send-telemetry-node.md) Felhőből az eszközre irányuló üzenetek küldéséhez a szolgáltatásnak szüksége van a **szolgáltatás csatlakozási engedélyére.** Alapértelmezés szerint minden IoT Hub jön létre egy megosztott hozzáférési szabályzat nevű **szolgáltatás,** amely megadja ezt az engedélyt.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Felhőből eszközre irányuló üzenet küldése

Ebben a szakaszban hozzon létre egy Node.js konzolalkalmazást, amely felhőből az eszközre irányuló üzeneteket küld a szimulált eszközalkalmazásnak. Szüksége van az eszköz azonosítóját az eszköz hozzáadott az [eszköz küldése telemetriai adatok](quickstart-send-telemetry-node.md) at egy eszköz egy IoT hub rövid útmutató. Szüksége van az IoT hub kapcsolati karakterláncra is, amelyet korábban másolt [az IoT hub kapcsolati karakterláncának beszerzése című részben.](#get-the-iot-hub-connection-string)

1. Hozzon létre egy üres mappát nevű **sendcloudtodevicemessage**. A **sendcloudtodevicemessage** mappában hozzon létre egy package.json fájlt a következő paranccsal a parancssorból. Fogadja el az összes alapértelmezett beállítást:

    ```shell
    npm init
    ```

2. A **sendcloudtodevicemessage** mappában lévő parancssorból futtassa a következő parancsot az **azure-iothub** csomag telepítéséhez:

    ```shell
    npm install azure-iothub --save
    ```

3. Szövegszerkesztő használatával hozzon létre egy **SendCloudToDeviceMessage.js** fájlt a **sendcloudtodevicemessage** mappában.

4. Adja hozzá `require` a következő állításokat a **SendCloudToDeviceMessage.js** fájl elején:

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Adja hozzá a következő kódot a **SendCloudToDeviceMessage.js** fájlhoz. Cserélje le a(z) "{iot hub connection string}" és "{device id}" helyőrző értékeket a korábban említett IoT hub kapcsolati karakterláncra és eszközazonosítóra:

    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Adja hozzá a következő függvényt a művelet eredmények nek a konzolra történő nyomtatásához:

    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Adja hozzá a következő funkciót a kézbesítési visszajelzési üzenetek konzolra történő nyomtatásához:

    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Adja hozzá a következő kódot, hogy üzenetet küldjön az eszközére, és kezelje a visszajelzési üzenetet, amikor az eszköz nyugtázza a felhőből az eszközre irányuló üzenetet:

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

9. Mentse és zárja be **a SendCloudToDeviceMessage.js** fájlt.

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A **szimulált eszköz** mappájában lévő parancssorban futtassa a következő parancsot a telemetriai adatok IoT Hubra küldéséhez és a felhőből az eszközre irányuló üzenetek figyeléséhez:

    ```shell
    node SimulatedDevice.js
    ```

    ![A szimulált eszközalkalmazás futtatása](./media/iot-hub-node-node-c2d/receivec2d.png)

2. A **sendcloudtodevicemessage** mappa parancssorában futtassa a következő parancsot a felhőből az eszközre irányuló üzenet küldéséhez, és várja meg a nyugtázási visszajelzést:

    ```shell
    node SendCloudToDeviceMessage.js
    ```

    ![Az alkalmazás futtatása a felhőből az eszközre parancs elküldéséhez](./media/iot-hub-node-node-c2d/sendc2d.png)

   > [!NOTE]
   > Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket (például exponenciális visszamaradást) kell megvalósítania, ahogy azt a cikk, [átmeneti hibakezelés](/azure/architecture/best-practices/transient-faults)című cikk ben javasolt.
   >

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan küldhet és fogadhat felhőből az eszközre irányuló üzeneteket.

Az IoT Hubot használó teljes körű megoldások példáinak megtekintéséhez tekintse meg az [Azure IoT Remote Monitoring megoldásgyorsító című témakört.](https://azure.microsoft.com/documentation/suites/iot-suite/)

Ha többet szeretne megtudni a megoldások IoT Hubkal való fejlesztéséről, tekintse meg az [IoT Hub fejlesztői útmutatóját.](iot-hub-devguide.md)
