<properties
    pageTitle="Az Azure IoT Hub használata a Node.js környezettel – első lépések | Microsoft Azure"
    description="Az Azure IoT Hub használata a Node.js környezettel – első lépések oktatóanyag. Az Azure IoT Hubot és a Node.js-t a Microsoft Azure IoT SDK-kal együtt használva az eszközök internetes hálózatán alapuló megoldást valósíthat meg."
    services="iot-hub"
    documentationCenter="nodejs"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="03/22/2016"
     ms.author="dobett"/>

# Ismerkedés az Azure IoT Hub for Node.js szolgáltatással

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

## Bevezetés

Az Azure IoT Hub egy teljesen felügyelt szolgáltatás, amely megbízható és biztonságos kétirányú kommunikációt tesz lehetővé az eszközök internetes hálózatához (IoT) csatlakozó több millió eszköz között, valamint megoldást biztosít a háttérrendszer kialakításához. Az IoT-projektek számára az egyik legnagyobb kihívást az jelenti, hogyan lehet megbízható és biztonságos módon csatlakoztatni az eszközöket a megoldás háttérrendszeréhez. A kihívás megoldására az IoT Hub a következőket kínálja:

- Megbízható nagy kapacitású üzenetkezelést kínál az eszközök és a felhő között mindkét irányban.
- Az eszközönkénti biztonsági hitelesítő adatok és hozzáférés-vezérlés segítségével lehetővé teszi a biztonságos kommunikációt.
- Tartalmazza a legnépszerűbb nyelvek és platformok eszközkönyvtárait.

Ez az oktatóanyag a következőket mutatja be:

- Egy IoT Hub létrehozása az Azure Portallal.
- Eszközidentitás létrehozása az IoT Hubban.
- Egy szimulált eszköz létrehozása, amely telemetriai adatokat küld a felhő háttérrendszerének.

Az oktatóanyag végén három Node.js-konzolalkalmazással fog rendelkezni:

* A **CreateDeviceIdentity.js** egy eszközidentitást, valamint egy társított biztonsági kulcsot hoz létre, amellyel csatlakozhat a szimulált eszközhöz.
* A **ReadDEviceToCloudMessages.js** megjeleníti a szimulált eszköz által küldött telemetriát.
* A **SimulatedDevice.js** csatlakozik az IoT Hubhoz a korábban létrehozott eszközidentitással, és másodpercenként telemetriai üzenetet küld az AMQPS protokoll használatával.

> [AZURE.NOTE] Különböző SDK-kat használhat az alkalmazások összeállításához, hogy eszközökön és a megoldás háttérrendszerén is fussanak. Ezekről az [IoT Hub SDKs][lnk-hub-sdks] című témakörben talál további információt.

Az oktatóanyag teljesítéséhez az alábbiakra lesz szüksége:

+ A Node.js 0.12.x vagy újabb verziója. <br/> [A fejlesztési környezet előkészítése][lnk-dev-setup] leírja, hogyan telepíthető a Node.js ehhez az oktatóprogramhoz Windows vagy Linux rendszeren.

+ Aktív Azure-fiók. <br/>Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása][lnk-free-trial].

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Utolsó lépésként kattintson az IoT Hub panel **Beállítások** elemére, majd a **Beállítások** panel **Üzenetküldés** elemére. Az **Üzenetküldés** panelen jegyezze fel az **Event Hub-kompatibilis nevet** és az **Event Hub-kompatibilis végpontot**. Ezekre az értékekre szüksége lesz a **read-d2c-messages** alkalmazása létrehozásakor.

![][6]

Létrehozott egy IoT Hubot, és rendelkezik az oktatóanyag további részeinek teljesítéséhez szükséges IoT Hub-állomásnévvel és kapcsolati karakterlánccal, valamint EventHub-kompatibilis névvel és végpontértékekkel.

## Eszközidentitás létrehozása

Ebben a szakaszban egy Node.js-konzolalkalmazást fog létrehozni, amely egy új eszközidentitást hoz létre az IoT Hub identitásjegyzékében. Egy eszköz csak akkor tud csatlakozni az IoT Hubhoz, ha be van jegyezve az eszközidentitás-jegyzékbe. További információkért lásd az [Azure IoT Hub fejlesztői útmutató][lnk-devguide-identity] **Eszközidentitás-jegyzék** című szakaszát. A konzolalkalmazás egy egyedi eszközazonosítót állít elő a futtatásakor, valamint egy kulcsot, amellyel az eszköz azonosítani tudja magát, amikor az eszközről a felhőbe irányuló üzeneteket küld az IoT Hubnak.

1. Hozzon létre egy új, **createdeviceidentity** nevű üres mappát. A **createdeviceidentity** mappában hozzon létre egy új package.json fájlt a következő parancs beírásával a parancssorba. Fogadja el az összes alapértelmezett beállítást:

    ```
    npm init
    ```

2. A **createdeviceidentity** mappában egy parancssorban futtassa a következő parancsot az **azure-iothub** csomag telepítéséhez:

    ```
    npm install azure-iothub --save
    ```

3. Egy szövegszerkesztővel hozzon létre egy új **CreateDeviceIdentity.js** fájlt a **createdeviceidentity** mappában.

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

6. Adja hozzá a következő kódot egy új eszközdefiníció létrehozásához az IoT Hub eszközidentitás-jegyzékében. Ez a kód új eszközt hoz létre, ha az eszközazonosító nem létezik a jegyzékben, különben pedig a következő eszköz kulcsát adja vissza:

    ```
    var device = new iothub.Device(null);
    device.deviceId = 'myFirstDevice';
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
        console.log('Device id: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.SymmetricKey.primaryKey);
      }
    }
    ```

7. Mentse és zárja be a **CreateDeviceIdentity.js** fájlt.

8. Ha a **createdeviceidentity** alkalmazást szeretné futtatni, futtassa a következő parancsot a parancssorban a createdeviceidentity mappában:

    ```
    node CreateDeviceIdentity.js 
    ```

9. Jegyezze fel az **eszköz azonosítóját** és az **eszköz kulcsát**. Ezekre később szüksége lesz, amikor az IoT Hubhoz eszközként csatlakozó alkalmazást hoz létre.

> [AZURE.NOTE] Az IoT Hub-identitásjegyzék csak a hub biztonságos elérésének biztosításához tárolja az eszközidentitást. Az eszközazonosítókat és kulcsokat biztonsági hitelesítő adatokként tárolja, valamint tartalmaz egy engedélyezve/letiltva jelzőt, amellyel letilthatja egy adott eszköz hozzáférését. Ha az alkalmazásnak más eszközspecifikus metaadatokat kell tárolnia, egy alkalmazásspecifikus tárolót kell használnia. További információt az [IoT Hub fejlesztői útmutatóban][lnk-devguide-identity] talál.

## Az eszközről a felhőbe irányuló üzenetek fogadása

Ebben a szakaszban egy Node.js-konzolalkalmazást fog létrehozni, amely az eszközről a felhőbe irányuló üzeneteket olvas az IoT Hubról. Az IoT Hub egy [Event Hubs][lnk-event-hubs-overview]-kompatibilis végpontot tesz közzé, hogy lehetővé tegye az eszközről a felhőbe irányuló üzenetek olvasását. Az egyszerűség érdekében ez az oktatóanyag egy alapszintű olvasót hoz létre, amely nem alkalmas nagy átviteli sebességű üzemelő példányokhoz. Az eszközről a felhőbe irányuló üzenetek nagy léptékű feldolgozásával kapcsolatban lásd [az eszközről a felhőbe irányuló üzenetek feldolgozását][lnk-process-d2c-tutorial] ismertető oktatóanyagot. Az Event Hubs szolgáltatástól érkező üzenetek feldolgozásával kapcsolatos további információkért lásd a [az Event Hubs használatának első lépéseit][lnk-eventhubs-tutorial] ismertető oktatóanyagot. Ez az IoT Hub Event Hub-kompatibilis végpontjaira érvényes.

> [AZURE.NOTE] Az eszközről a felhőbe irányuló üzenetek olvasásához használt Event Hubs-kompatibilis végpontok mindig az AMQPS protokollt használják.

1. Hozzon létre egy új, **readdevicetocloudmessages** nevű üres mappát. A **readdevicetocloudmessages** mappában hozzon létre egy új package.json fájlt a következő parancs beírásával a parancssorba. Fogadja el az összes alapértelmezett beállítást:

    ```
    npm init
    ```

2. A **readdevicetocloudmessages** mappában egy parancssorban futtassa a következő parancsot az **amqp10** és **bluebird** csomagok telepítéséhez:

    ```
    npm install amqp10 bluebird --save
    ```

3. Egy szövegszerkesztővel hozzon létre egy új **ReadDeviceToCloudMessages.js** fájlt a **readdevicetocloudmessages** mappában.

4. Adja hozzá a következő `require` utasításokat a **ReadDeviceToCloudMessages.js** fájl elejéhez:

    ```
    'use strict';

    var AMQPClient = require('amqp10').Client;
    var Policy = require('amqp10').Policy;
    var translator = require('amqp10').translator;
    var Promise = require('bluebird');
    ```

5. Adja hozzá a következő változódeklarációkat, és cserélje le a helyőrzőket a korábban feljegyzett értékekre. A **{your event hub-compatible namespace}** helyőrző értéke a portál **Event Hub-kompatibilis végpont** mezőjéből származik – a **namespace.servicebus.windows.net** formát veszi fel (az *sb://* előtag nélkül).

    ```
    var protocol = 'amqps';
    var eventHubHost = '{your event hub-compatible namespace}';
    var sasName = 'iothubowner';
    var sasKey = '{your iot hub key}';
    var eventHubName = '{your event hub-compatible name}';
    var numPartitions = 2;
    ```

    > [AZURE.NOTE] Ez a kód azt feltételezi, hogy az IoT hubot az F1 (ingyenes) rétegen hozta létre. Az ingyenes IoT hub két, „0” és „1” nevű partícióval rendelkezik. Ha az IoT Hubot egy másik tarifacsomaggal hozta létre, úgy kell módosítania a kódot, hogy minden partícióhoz **MessageReceiver** fogadót hozzon létre.

6. Adja hozzá a következő szűrődefiníciót. Ez az alkalmazás szűrőt használ a fogadó létrehozásakor, hogy a fogadó csak a fogadó futtatásának megkezdése után az IoT Hubra küldött üzeneteket olvassa. Ez tesztkörnyezetben hasznos, mivel láthatja az üzenetek aktuális készletét, éles környezetben azonban a kódnak biztosítania kell, hogy az összes üzenetet feldolgozza. További információért lásd: [Eszközről a felhőbe irányuló IoT Hub-üzenetek feldolgozása][lnk-process-d2c-tutorial].

    ```
    var filterOffset = new Date().getTime();
    var filterOption;
    if (filterOffset) {
      filterOption = {
      attach: { source: { filter: {
      'apache.org:selector-filter:string': translator(
        ['described', ['symbol', 'apache.org:selector-filter:string'], ['string', "amqp.annotation.x-opt-enqueuedtimeutc > " + filterOffset + ""]])
        } } }
      };
    }
    ```

7. Adja hozzá a következő kódot a fogadócím és az AMQP-ügyfél létrehozásához:

    ```
    var uri = protocol + '://' + encodeURIComponent(sasName) + ':' + encodeURIComponent(sasKey) + '@' + eventHubHost;
    var recvAddr = eventHubName + '/ConsumerGroups/$default/Partitions/';
    
    var client = new AMQPClient(Policy.EventHub);
    ```

8. Adja hozzá az alábbi két függvényt, amelyek kinyomtatják a konzol kimenetét:

    ```
    var messageHandler = function (partitionId, message) {
      console.log('Received(' + partitionId + '): ', message.body);
    };
    
    var errorHandler = function(partitionId, err) {
      console.warn('** Receive error: ', err);
    };
    ```

9. Adja hozzá a következő függvényt, amely fogadóként működik adott partícióhoz a szűrővel:

    ```
    var createPartitionReceiver = function(partitionId, receiveAddress, filterOption) {
      return client.createReceiver(receiveAddress, filterOption)
        .then(function (receiver) {
          console.log('Listening on partition: ' + partitionId);
          receiver.on('message', messageHandler.bind(null, partitionId));
          receiver.on('errorReceived', errorHandler.bind(null, partitionId));
        });
    };
    ```

10. Adja hozzá a következő kódot az Event Hub-kompatibilis végponthoz való csatlakozás és a fogadók elindítása érdekében:

    ```
    client.connect(uri)
      .then(function () {
        var partitions = [];
        for (var i = 0; i < numPartitions; ++i) {
          partitions.push(createPartitionReceiver(i, recvAddr + i, filterOption));
        }
        return Promise.all(partitions);
    })
    .error(function (e) {
        console.warn('Connection error: ', e);
    });
    ```

11. Mentse és zárja be a **ReadDeviceToCloudMessages.js** fájlt.

## Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban egy Java-konzolalkalmazást fog létrehozni, amely az eszközről a felhőbe irányuló üzeneteket egy IoT Hubra küldő eszközt szimulál.

1. Hozzon létre egy új, **simulateddevice** nevű üres mappát. A **simulateddevice** mappában hozzon létre egy új package.json fájlt a következő parancs beírásával a parancssorba. Fogadja el az összes alapértelmezett beállítást:

    ```
    npm init
    ```

2. A **simulateddevice** mappában egy parancssorban futtassa a következő parancsot az **azure-iot-device-amqp** csomag telepítéséhez:

    ```
    npm install azure-iot-device azure-iot-device-amqp --save
    ```

3. Egy szövegszerkesztővel hozzon létre egy új **SimulatedDevice.js** fájlt a **simulateddevice** mappában.

4. Adja hozzá a következő `require` utasításokat a **SimulatedDevice.js** fájl elejéhez:

    ```
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. Adjon hozzá egy **connectionString** változót, és ezzel hozzon létre eszközügyfelet. Cserélje le a **{youriothubname}** helyőrzőt az IoT Hub nevére, és a **{yourdeviceid}** és **{yourdevicekey}** helyőrzőket pedig az *Eszközidentitás létrehozása* szakaszban létrehozott eszközértékekre:

    ```
    var connectionString = 'HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    
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

7. Hozzon létre egy visszahívást, és a **setInterval** függvénnyel küldjön másodpercenként új üzenetet az IoT Hubnak:

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');

        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var windSpeed = 10 + (Math.random() * 4);
            var data = JSON.stringify({ deviceId: 'mydevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 2000);
      }
    };
    ```

8. Nyissa meg az IoT Hub kapcsolatát, és kezdje el elküldeni az üzeneteket:

    ```
    client.open(connectCallback);
    ```

9. Mentse és zárja be a **SimulatedDevice.js** fájlt.

> [AZURE.NOTE] Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket is meg kell valósítania (például egy exponenciális leállítást) a [tranziens hibakezelést][lnk-transient-faults] ismertető MSDN-cikkben leírtak szerint.


## Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A **readdevicetocloudmessages** mappában egy parancssorban futtassa a következő parancsot az IoT Hub megfigyelésének elkezdéséhez:

    ```
    node ReadDeviceToCloudMessages.js 
    ```

    ![][7]

2. A **simulateddevice** mappában egy parancssorban futtassa a következő parancsot, amellyel megkezdheti a telemetriai adatok küldését az IoT Hubnak:

    ```
    node SimulatedDevice.js
    ```

    ![][8]

3. Az [Azure Portal][lnk-portal] **Használat** csempéje a hubnak küldött üzenetek számát jeleníti meg:

    ![][43]

## Következő lépések

Ebben az oktatóanyagban egy új IoT Hubot konfigurált a portálon, majd létrehozott egy eszközidentitást a hub identitásjegyzékében. Ennek az eszközidentitásnak a segítségével lehetővé tette a szimulált eszközalkalmazásnak, hogy az eszközről a felhőbe irányuló üzeneteket küldjön a hubnak, valamint létrehozott egy alkalmazást, amely megjeleníti a hub által fogadott üzeneteket. A következő oktatóanyagokban folytathatja az IoT Hub szolgáltatásainak, valamint további IoT-forgatókönyveknek a felfedezését:

- [A felhőből az eszközre irányuló üzenetek IoT Hubbal való küldését][lnk-c2d-tutorial] ismertető oktatóanyagból megtudhatja, hogyan küldhet üzeneteket eszközökre, és hogyan dolgozhatja fel az IoT Hub által előállított kézbesítési visszajelzéseket.
- [Az eszközről a felhőbe irányuló üzenetek feldolgozását][lnk-process-d2c-tutorial] ismertető oktatóanyag bemutatja, hogyan dolgozhatók fel megbízhatóan az eszközökről érkező telemetriai és interaktív üzenetek.
- A [Fájlok feltöltése eszközökről][lnk-upload-tutorial] című oktatóanyag egy mintázatot ismertet, amelyet a felhőből az eszközre irányuló üzenetek használnak az eszközökről történő fájlfeltöltések elősegítésére.

<!-- Images. -->
[6]: ./media/iot-hub-node-node-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/doc/devbox_setup.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/



<!--HONumber=jun16_HO2-->


