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
     ms.date="09/12/2016"
     ms.author="dobett"/>


# Ismerkedés az Azure IoT Hub for Node.js szolgáltatással

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

Az oktatóanyag végén három Node.js-konzolalkalmazással fog rendelkezni:

* A **CreateDeviceIdentity.js** egy eszközidentitást, valamint egy társított biztonsági kulcsot hoz létre, amellyel csatlakozhat a szimulált eszközhöz.
* A **ReadDeviceToCloudMessages.js** megjeleníti a szimulált eszköz által küldött telemetriát.
* A **SimulatedDevice.js** csatlakozik az IoT Hubhoz a korábban létrehozott eszközidentitással, és másodpercenként telemetriai üzenetet küld az AMQPS protokoll használatával.

> [AZURE.NOTE] Különböző SDK-kat használhat az alkalmazások összeállításához, hogy eszközökön és a megoldás háttérrendszerén is fussanak. Ezekről az [IoT Hub SDKs][lnk-hub-sdks] című témakörben talál további információt.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

+ A Node.js 0.10.x vagy újabb verziója.

+ Aktív Azure-fiók. (Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása][lnk-free-trial].)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Ezzel létrehozta az IoT Hubot. Ezenkívül rendelkezik az oktatóanyag további részeinek teljesítéséhez szükséges IoT Hub-állomásnévvel és kapcsolati karakterlánccal is.

## Eszközidentitás létrehozása

Ebben a szakaszban egy Node.js-konzolalkalmazást fog létrehozni, amely egy eszközidentitást hoz létre az IoT Hub identitásjegyzékében. Egy eszköz csak akkor tud csatlakozni az IoT Hubhoz, ha be van jegyezve az eszközidentitás-jegyzékbe. További információkért lásd az [Azure IoT Hub fejlesztői útmutató][lnk-devguide-identity] **Eszközidentitás-jegyzék** című szakaszát. A konzolalkalmazás egy egyedi eszközazonosítót állít elő a futtatásakor, valamint egy kulcsot, amellyel az eszköz azonosítani tudja magát, amikor az eszközről a felhőbe irányuló üzeneteket küld az IoT Hubnak.

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

6. A következő kód hozzáadásával hozzon létre egy eszközdefiníciót az IoT Hub eszközidentitás-jegyzékében. Ez a kód létrehozza az eszközt, ha az eszközazonosító nem létezik a jegyzékben, ellenkező esetben pedig a meglévő eszköz kulcsát adja vissza:

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

9. Jegyezze fel az **eszköz azonosítóját** és az **eszköz kulcsát**. Ezekre az értékekre később szüksége lesz, amikor az IoT Hubhoz eszközként csatlakozó alkalmazást hoz létre.

> [AZURE.NOTE] Az IoT Hub-identitásjegyzék csak a hub biztonságos elérésének biztosításához tárolja az eszközidentitást. Az eszközazonosítókat és kulcsokat biztonsági hitelesítő adatokként tárolja, valamint tartalmaz egy engedélyezve/letiltva jelzőt, amellyel letilthatja egy adott eszköz hozzáférését. Ha az alkalmazásnak más eszközspecifikus metaadatokat kell tárolnia, egy alkalmazásspecifikus tárolót kell használnia. További információt az [IoT Hub fejlesztői útmutatóban][lnk-devguide-identity] talál.

## Az eszközről a felhőbe irányuló üzenetek fogadása

Ebben a szakaszban egy Node.js-konzolalkalmazást hoz létre, amely az eszközről a felhőbe irányuló üzeneteket olvas az IoT Hubról. Az IoT Hub egy [Event Hubs][lnk-event-hubs-overview]-kompatibilis végpontot tesz közzé, hogy lehetővé tegye az eszközről a felhőbe irányuló üzenetek olvasását. Az egyszerűség érdekében ez az oktatóanyag egy alapszintű olvasót hoz létre, amely nem alkalmas nagy átviteli sebességű üzemelő példányokhoz. Az eszközről a felhőbe irányuló üzenetek nagy léptékű feldolgozásával kapcsolatban lásd [az eszközről a felhőbe irányuló üzenetek feldolgozását][lnk-process-d2c-tutorial] ismertető oktatóanyagot. Az Event Hubs szolgáltatástól érkező üzenetek feldolgozásával kapcsolatos további információkért lásd a [az Event Hubs használatának első lépéseit][lnk-eventhubs-tutorial] ismertető oktatóanyagot. Ez az IoT Hub Event Hub-kompatibilis végpontjaira érvényes.

> [AZURE.NOTE] Az eszközről a felhőbe irányuló üzenetek olvasásához használt Event Hubs-kompatibilis végpontok mindig az AMQPS protokollt használják.

1. Hozzon létre egy új, **readdevicetocloudmessages** nevű üres mappát. A **readdevicetocloudmessages** mappában hozzon létre egy package.json fájlt úgy, hogy beírja a következő parancsot a parancssorba. Fogadja el az összes alapértelmezett beállítást:

    ```
    npm init
    ```

2. A **readdevicetocloudmessages** mappában egy parancssorban futtassa a következő parancsot az **azure-event-hubs** csomag telepítéséhez:

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

7. Az **EventHubClient** létrehozásához adja hozzá az alábbi kódot, nyissa meg az IoT Hub kapcsolatát, majd hozzon létre fogadót minden egyes partícióhoz. Ez az alkalmazás szűrőt használ a fogadó létrehozásakor, hogy a fogadó csak a fogadó futtatásának megkezdése után az IoT Hubra küldött üzeneteket olvassa. Ez a szűrő tesztkörnyezetben hasznos, mert így csak az aktuális üzenetek láthatók. Éles környezetben azonban a kódnak gondoskodnia kell az összes üzenet feldolgozásáról. További információkért lásd a következő oktatóanyagot: [Eszközről a felhőbe irányuló IoT Hub-üzenetek feldolgozása][lnk-process-d2c-tutorial]:

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

## Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban egy Node.js-konzolalkalmazást fog létrehozni, amely az eszközről a felhőbe irányuló üzeneteket egy IoT Hubra küldő eszközt szimulál.

1. Hozzon létre egy új, **simulateddevice** nevű üres mappát. A **simulateddevice** mappában hozza létre a package.json fájlt úgy, hogy beírja a következő parancsot a parancssorba. Fogadja el az összes alapértelmezett beállítást:

    ```
    npm init
    ```

2. Telepítse az **azure-iot-device** eszközoldali SDK csomagot és az **azure-iot-device-amqp** csomagot. Ehhez futtassa egy parancssorból a következő parancsot a **simulateddevice** mappában:

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

5. Adjon hozzá egy **connectionString** változót, és ezzel hozzon létre eszközügyfelet. A **{youriothostname}** helyére írja be az *IoT Hub létrehozása* című szakaszban létrehozott IoT Hub nevét. A **{yourdevicekey}** helyére írja be az *Eszközidentitás létrehozása* című szakaszban generált eszközkulcsértéket:

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

> [AZURE.NOTE] Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket is meg kell valósítania (például egy exponenciális leállítást) a [tranziens hibakezelést][lnk-transient-faults] ismertető MSDN-cikkben leírtak szerint.


## Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A **readdevicetocloudmessages** mappában egy parancssorban futtassa a következő parancsot az IoT Hub megfigyelésének elkezdéséhez:

    ```
    node ReadDeviceToCloudMessages.js 
    ```

    ![A Node.js IoT Hub szolgáltatás oldali ügyfélalkalmazása az eszközről a felhőbe irányuló üzenetek figyeléséhez][7]

2. A **simulateddevice** mappában egy parancssorban futtassa a következő parancsot, amellyel megkezdheti a telemetriai adatok küldését az IoT Hubnak:

    ```
    node SimulatedDevice.js
    ```

    ![A Node.js IoT Hub eszköz oldali ügyfélalkalmazás az eszközről a felhőbe irányuló üzenetek küldéséhez][8]

3. Az [Azure Portal][lnk-portal] **Használat** csempéje a hubnak küldött üzenetek számát jeleníti meg:

    ![Az Azure Portalon az IoT Hubnak küldött üzenetek számát megjelenítő Használat csempe][43]

## Következő lépések

Ebben az oktatóanyagban egy új IoT Hubot konfigurált a portálon, majd létrehozott egy eszközidentitást a hub identitásjegyzékében. Ennek az eszközidentitásnak a segítségével lehetővé tette a szimulált eszközalkalmazásnak, hogy az eszközről a felhőbe irányuló üzeneteket küldjön a hubnak. Emellett létrehozott egy alkalmazást, amely megjeleníti a hub által fogadott üzeneteket. 

További bevezetés az IoT Hub használatába, valamint egyéb IoT-forgatókönyvek megismerése:

- [Eszköz csatlakoztatása][lnk-connect-device]
- [Eszközfelügyelet – első lépések][lnk-device-management]
- [Átjáró SDK – első lépések][lnk-gateway-SDK]

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

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/



<!--HONumber=Oct16_HO3-->


