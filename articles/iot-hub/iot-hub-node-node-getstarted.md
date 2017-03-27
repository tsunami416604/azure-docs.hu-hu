---
title: "Ismerkedés az Azure IoT Hub (Node) szolgáltatással | Microsoft Docs"
description: "Eszközről a felhőbe irányuló üzenetek küldésének módja egy eszközről az Azure IoT Hubba a Node.js-hez készült Azure IoT SDK-k használatával. Létre kell hozni egy szimulált eszközalkalmazást üzenetek küldéséhez, egy szolgáltatásalkalmazást az eszközének az identitásjegyzékben történő regisztrálásához, továbbá egy szolgáltatásalkalmazást, amely beolvassa az eszközről a felhőbe irányuló üzeneteket az IoT Hubról."
services: iot-hub
documentationcenter: nodejs
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 56618522-9a31-42c6-94bf-55e2233b39ac
ms.service: iot-hub
ms.devlang: javascript
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/16/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: e5cfa4c0a6964c3722ecaa6727336f5497565885
ms.lasthandoff: 03/17/2017


---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-node"></a>A szimulált eszköz csatlakoztatása az IoT Hubhoz a Node használatával
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

Az oktatóanyag végén három Node.js-konzolalkalmazással fog rendelkezni:

* A **CreateDeviceIdentity.js** egy eszközidentitást, valamint egy társított biztonsági kulcsot hoz létre, amellyel csatlakozhat a szimulált eszközalkalmazáshoz.
* A **ReadDeviceToCloudMessages.js** megjeleníti a szimulált eszközalkalmazások által küldött telemetriát.
* A **SimulatedDevice.js** csatlakozik az IoT Hubhoz a korábban létrehozott eszközidentitással, és az MQTT protokoll használatával másodpercenként telemetriai üzenetet küld.

> [!NOTE]
> Az Azure IoT SDK-kat használhatja az eszközökön és a megoldás háttérrendszerén futó alkalmazások összeállításához egyaránt. Ezekről az [Azure IoT SDK-k][lnk-hub-sdks] című témakörben talál további információt.
> 
> 

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* A Node.js 0.10.x vagy újabb verziója.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Ezzel létrehozta az IoT Hubot. Ezenkívül rendelkezik az oktatóanyag további részeinek teljesítéséhez szükséges IoT Hub-állomásnévvel és kapcsolati karakterlánccal is.

## <a name="create-a-device-identity"></a>Eszközidentitás létrehozása
Ebben a szakaszban egy Node.js-konzolalkalmazást fog létrehozni, amely egy eszközidentitást hoz létre az IoT Hub identitásjegyzékében. Egy eszköz csak akkor tud csatlakozni az IoT Hubhoz, ha be van jegyezve az identitásjegyzékbe. További információkért lásd az [IoT Hub fejlesztői útmutatójának][lnk-devguide-identity] **Identitásjegyzék** című szakaszát. A konzolalkalmazás egy egyedi eszközazonosítót állít elő a futtatásakor, valamint egy kulcsot, amellyel az eszköz azonosítani tudja magát, amikor az eszközről a felhőbe irányuló üzeneteket küld az IoT Hubnak.

1. Hozzon létre egy új, **createdeviceidentity** nevű üres mappát. A **createdeviceidentity** mappában hozzon létre egy package.json fájlt úgy, hogy beírja a következő parancsot a parancssorba. Fogadja el az összes alapértelmezett beállítást:
   
    ```
    npm init
    ```
2. Telepítse az **azure-iothub** szolgáltatásoldali SDK csomagot. Ehhez futtassa egy parancssorból a következő parancsot a **createdeviceidentity** mappában:
   
    ```
    npm install azure-iothub --save
    ```
3. Egy szövegszerkesztővel hozza létre a **CreateDeviceIdentity.js** fájlt a **createdeviceidentity** mappában.
4. Adja hozzá a következő `require` utasítást a **CreateDeviceIdentity.js** fájl elejéhez:
   
    ```
    'use strict';
   
    var iothub = require('azure-iothub');
    ```
5. Adja a következő kódot a **CreateDeviceIdentity.js** fájlhoz, és a helyőrző értékét cserélje le az IoT Hub előző szakaszban létrehozott kapcsolati karakterláncára: 
   
    ```
    var connectionString = '{iothub connection string}';
   
    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```
6. Adja hozzá a következő kódot egy eszközdefiníció létrehozásához az IoT Hub identitásjegyzékében. Ez a kód létrehozza az eszközt, ha az eszközazonosító nem létezik az identitásjegyzékben, ellenkező esetben pedig a meglévő eszköz kulcsát adja vissza:
   
    ```
    var device = new iothub.Device(null);
    device.deviceId = 'myFirstNodeDevice';
    registry.create(device, function(err, deviceInfo, res) {
      if (err) {
        registry.get(device.deviceId, printDeviceInfo);
      }
      if (deviceInfo) {
        printDeviceInfo(err, deviceInfo, res)
      }
    });
   
    function printDeviceInfo(err, deviceInfo, res) {
      if (deviceInfo) {
        console.log('Device ID: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.symmetricKey.primaryKey);
      }
    }
    ```
7. Mentse és zárja be a **CreateDeviceIdentity.js** fájlt.
8. Ha a **createdeviceidentity** alkalmazást szeretné futtatni, futtassa a következő parancsot a parancssorban a createdeviceidentity mappában:
   
    ```
    node CreateDeviceIdentity.js 
    ```
9. Jegyezze fel az **eszköz azonosítóját** és az **eszköz kulcsát**. Ezekre az értékekre később szüksége lesz, amikor az IoT Hubhoz eszközként csatlakozó alkalmazást hoz létre.

> [!NOTE]
> Az IoT Hub-identitásjegyzék csak az IoT Hub biztonságos elérésének biztosításához tárolja az eszközidentitásokat. Az eszközazonosítókat és kulcsokat biztonsági hitelesítő adatokként tárolja, valamint tartalmaz egy engedélyezve/letiltva jelzőt, amellyel letilthatja egy adott eszköz hozzáférését. Ha az alkalmazásnak más eszközspecifikus metaadatokat kell tárolnia, egy alkalmazásspecifikus tárolót kell használnia. További információ: [IoT Hub fejlesztői útmutató][lnk-devguide-identity].
> 
> 

## <a name="receive-device-to-cloud-messages"></a>Az eszközről a felhőbe irányuló üzenetek fogadása
Ebben a szakaszban egy Node.js-konzolalkalmazást hoz létre, amely az eszközről a felhőbe irányuló üzeneteket olvas az IoT Hubról. Az IoT Hub egy [Event Hubs][lnk-event-hubs-overview]-kompatibilis végpontot tesz közzé, hogy lehetővé tegye az eszközről a felhőbe irányuló üzenetek olvasását. Az egyszerűség érdekében ez az oktatóanyag egy alapszintű olvasót hoz létre, amely nem alkalmas nagy átviteli sebességű üzemelő példányokhoz. Az eszközről a felhőbe irányuló üzenetek nagy léptékű feldolgozásával kapcsolatban lásd [az eszközről a felhőbe irányuló üzenetek feldolgozását][lnk-process-d2c-tutorial] ismertető oktatóanyagot. Az Event Hubs szolgáltatástól érkező üzenetek feldolgozásával kapcsolatos további információkért lásd [az Event Hubs használatának első lépéseit][lnk-eventhubs-tutorial] ismertető oktatóanyagot. Ez az IoT Hub Event Hub-kompatibilis végpontjaira érvényes.

> [!NOTE]
> Az eszközről a felhőbe irányuló üzenetek olvasásához használt Event Hub-kompatibilis végpontok mindig az AMQP protokollt használják.
> 
> 

1. Hozzon létre egy **readdevicetocloudmessages** nevű üres mappát. A **readdevicetocloudmessages** mappában hozzon létre egy package.json fájlt úgy, hogy beírja a következő parancsot a parancssorba. Fogadja el az összes alapértelmezett beállítást:
   
    ```
    npm init
    ```
2. A **readdevicetocloudmessages** mappában a parancssorban futtassa a következő parancsot az **azure-event-hubs** csomag telepítéséhez:
   
    ```
    npm install azure-event-hubs --save
    ```
3. Egy szövegszerkesztővel hozza létre a **ReadDeviceToCloudMessages.js** nevű fájlt a **readdevicetocloudmessages** mappában.
4. Adja hozzá a következő `require` utasításokat a **ReadDeviceToCloudMessages.js** fájl elejéhez:
   
    ```
    'use strict';
   
    var EventHubClient = require('azure-event-hubs').Client;
    ```
5. Adja hozzá a következő változódeklarációt, és a helyőrző értékét cserélje le az IoT Hub kapcsolati karakterláncára:
   
    ```
    var connectionString = '{iothub connection string}';
    ```
6. Adja hozzá az alábbi két függvényt, amelyek kinyomtatják a konzol kimenetét:
   
    ```
    var printError = function (err) {
      console.log(err.message);
    };
   
    var printMessage = function (message) {
      console.log('Message received: ');
      console.log(JSON.stringify(message.body));
      console.log('');
    };
    ```
7. Az **EventHubClient** létrehozásához adja hozzá az alábbi kódot, nyissa meg az IoT Hub kapcsolatát, majd hozzon létre fogadót minden egyes partícióhoz. Ez az alkalmazás szűrőt használ a fogadó létrehozásakor, hogy a fogadó csak a fogadó futtatásának megkezdése után az IoT Hubra küldött üzeneteket olvassa. Ez a szűrő tesztkörnyezetben hasznos, mert így csak az aktuális üzenetek láthatók. Éles környezetben azonban a kódnak gondoskodnia kell az összes üzenet feldolgozásáról. További információkért lásd az [eszközről a felhőbe irányuló IoT Hub-üzenetek feldolgozását][lnk-process-d2c-tutorial] ismertető oktatóanyagot:
   
    ```
    var client = EventHubClient.fromConnectionString(connectionString);
    client.open()
        .then(client.getPartitionIds.bind(client))
        .then(function (partitionIds) {
            return partitionIds.map(function (partitionId) {
                return client.createReceiver('$Default', partitionId, { 'startAfterTime' : Date.now()}).then(function(receiver) {
                    console.log('Created partition receiver: ' + partitionId)
                    receiver.on('errorReceived', printError);
                    receiver.on('message', printMessage);
                });
            });
        })
        .catch(printError);
    ```
8. Mentse és zárja be a **ReadDeviceToCloudMessages.js** fájlt.

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása
Ebben a szakaszban egy Node.js-konzolalkalmazást fog létrehozni, amely az eszközről a felhőbe irányuló üzeneteket egy IoT Hubra küldő eszközt szimulál.

1. Hozzon létre egy **simulateddevice** nevű üres mappát. A **simulateddevice** mappában hozza létre a package.json fájlt úgy, hogy beírja a következő parancsot a parancssorba. Fogadja el az összes alapértelmezett beállítást:
   
    ```
    npm init
    ```
2. Telepítse az **azure-iot-device** eszközoldali SDK csomagot és az **azure-iot-device-mqtt** csomagot. Ehhez futtassa a parancssorban a következő parancsot a **simulateddevice** mappában:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Egy szövegszerkesztővel hozzon létre egy **SimulatedDevice.js** fájlt a **simulateddevice** mappában.
4. Adja hozzá a következő `require` utasításokat a **SimulatedDevice.js** fájl elejéhez:
   
    ```
    'use strict';
   
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```
5. Adjon hozzá egy **connectionString** változót, és ezzel hozzon létre egy **Ügyfél** példányt. A **{youriothostname}** helyére írja be az *IoT Hub létrehozása* című szakaszban létrehozott IoT Hub nevét. A **{yourdevicekey}** helyére írja be az *Eszközidentitás létrehozása* című szakaszban generált eszközkulcsértéket:
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myFirstNodeDevice;SharedAccessKey={yourdevicekey}';
   
    var client = clientFromConnectionString(connectionString);
    ```
6. Adja hozzá a következő függvényt az alkalmazás kimenetének megjelenítéséhez:
   
    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```
7. Hozzon létre egy visszahívást, és a **setInterval** függvénnyel küldjön másodpercenként üzenetet az IoT Hubnak:
   
    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
   
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var windSpeed = 10 + (Math.random() * 4);
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```
8. Nyissa meg az IoT Hub kapcsolatát, és kezdje el elküldeni az üzeneteket:
   
    ```
    client.open(connectCallback);
    ```
9. Mentse és zárja be a **SimulatedDevice.js** fájlt.

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket is meg kell valósítania (például egy exponenciális leállítást) a [tranziens hibakezelést][lnk-transient-faults] ismertető MSDN-cikkben leírtak szerint.
> 
> 

## <a name="run-the-apps"></a>Az alkalmazások futtatása
Most már készen áll az alkalmazások futtatására.

1. A **readdevicetocloudmessages** mappában egy parancssorban futtassa a következő parancsot az IoT Hub megfigyelésének elkezdéséhez:
   
    ```
    node ReadDeviceToCloudMessages.js 
    ```
   
    ![Node.js IoT Hub-szolgáltatásalkalmazás az eszközről a felhőbe irányuló üzenetek figyeléséhez][7]
2. A **simulateddevice** mappában egy parancssorban futtassa a következő parancsot, amellyel megkezdheti a telemetriai adatok küldését az IoT Hubnak:
   
    ```
    node SimulatedDevice.js
    ```
   
    ![Node.js IoT Hub-eszközalkalmazás az eszközről a felhőbe irányuló üzenetek küldéséhez][8]
3. Az [Azure Portal][lnk-portal] **Használat** csempéje az IoT Hubnak küldött üzenetek számát jeleníti meg:
   
    ![Az Azure Portalon az IoT Hubnak küldött üzenetek számát megjelenítő Használat csempe][43]

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban egy új IoT Hubot konfigurált az Azure-portálon, majd létrehozott egy eszközidentitást az IoT Hub identitásjegyzékében. Ennek az eszközidentitásnak a segítségével lehetővé tette a szimulált eszközalkalmazásnak, hogy az eszközről a felhőbe irányuló üzeneteket küldjön az IoT Hubnak. Emellett létrehozott egy alkalmazást, amely megjeleníti az IoT Hub által fogadott üzeneteket. 

További bevezetés az IoT Hub használatába, valamint egyéb IoT-forgatókönyvek megismerése:

* [Kapcsolódás az eszközhöz][lnk-connect-device]
* [Eszközfelügyelet – első lépések][lnk-device-management]
* [IoT átjáró SDK – első lépések][lnk-gateway-SDK]

Az IoT-megoldás kibővítésével és az eszközről a felhőbe irányuló üzenetek nagy léptékű feldolgozásával kapcsolatban tekintse meg [az eszközről a felhőbe irányuló üzenetek feldolgozását][lnk-process-d2c-tutorial] ismertető oktatóanyagot.

<!-- Images. -->
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

