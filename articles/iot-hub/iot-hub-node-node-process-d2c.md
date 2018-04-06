---
title: Az Azure IoT Hub (csomópont) útválasztási üzenetek |} Microsoft Docs
description: How Azure IoT Hub eszközről a felhőbe üzenetek feldolgozásához átirányítani más háttérszolgáltatások üzenetek útválasztási szabályokat és az egyéni végpontokat használatával.
services: iot-hub
documentationcenter: node
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2017
ms.author: v-masebo;dobett
ms.openlocfilehash: 02edb06d2d871cffac717358e33a6720c444a9b3
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="routing-messages-with-iot-hub-node"></a>Üzenetek (csomópont) az IoT hubbal

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

Ez az oktatóanyag épít, a [Ismerkedés az IoT-központ] oktatóanyag.  Az oktatóanyag:

* Bemutatja, hogyan könnyen és konfigurációalapú az eszközről a felhőbe üzenetek mennyi útválasztási szabályok használatával.
* Bemutatja, hogyan interaktív üzenetek további feldolgozásra a megoldás háttérből azonnali beavatkozást igénylő elkülönítéséhez.  Például egy eszköz előfordulhat, hogy küldése riasztás, amely elindítja a jegy beszúrása egy CRM-rendszerbe.  Adatpont üzenetek, például hőmérséklet telemetriai ellentétben be egy analytics hírcsatorna.

Ez az oktatóanyag végén három Node.js konzol alkalmazások futtatása:

* **SimulatedDevice.js**, az alkalmazás létrehozása a módosított változatát a [Ismerkedés az IoT-központ] oktatóanyagban adatpont eszközről a felhőbe üzeneteket küld minden második és interaktív eszközről a felhőbe üzenetek véletlenszerű időköz. Ez az alkalmazás kommunikáljon az IoT-központ MQTT protokollt használ.
* **ReadDeviceToCloudMessages.js** jeleníti meg az eszköz alkalmazás által küldött telemetriai adatok.
* **ReadCriticalQueue.js** távolít el a Service Bus-üzenetsorba, az IoT hub csatolva a kritikus üzeneteihez.

> [!NOTE]
> Az IoT-központ rendelkezik sok eszköz platformok és nyelvek, beleértve a C, Java és JavaScript SDK támogatása. Az eszköz a fizikai eszköz ebben az oktatóanyagban helyettesítése és eszközök csatlakoztatása az IoT-központ, lásd: a [Azure IoT fejlesztői központ].

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* A teljes működő verziójához a [Ismerkedés az IoT-központ] oktatóanyag.
* A Node.js 4.0.x vagy újabb verziója.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

Javasoljuk továbbá kapcsolatos tájékoztatás [Azure Storage] és [Azure Service Bus].

## <a name="send-interactive-messages-from-a-device-app"></a>Interaktív üzenetek küldése egy eszköz alkalmazásból
Ebben a szakaszban létrehozott eszközalkalmazás módosítja a [Ismerkedés az IoT-központ] alkalmanként küldéséhez azonnali feldolgozást igénylő oktatóanyag.

1. Egy szövegszerkesztővel nyissa meg a **simulateddevice\SimulatedDevice.js** fájlt. Ez a fájl tartalmazza a kódját a **SimulatedDevice** létrehozta az alkalmazást a [Ismerkedés az IoT-központ] oktatóanyag.

2. Cserélje le a **connectCallback** függvény a következő kóddal:

    ```nodejs
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
    
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
        var data, message;
        if (Math.random() > 0.7) {
            if (Math.random() > 0.5) {
                data = "This is a critical message.";
                message = new Message(data);
                message.properties.add("level", "critical");
            } else {
                data = "This is a storage message.";
                message = new Message(data);
                message.properties.add("level", "storage");
            }
            console.log("Sending message: " + message.getData());
        }
        else {
                var temperature = 20 + (Math.random() * 15);
                var humidity = 60 + (Math.random() * 20);            
                data = JSON.stringify({ deviceId: 'myFirstNodeDevice', temperature: temperature, humidity: humidity });
                message = new Message(data);
                message.properties.add('temperatureAlert', (temperature > 30) ? 'true' : 'false');
                console.log("Sending message: " + message.getData());
            }
        client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```
   
    Ez a módszer véletlenszerűen hozzáadja a tulajdonság `"level": "critical"` és `"level": "storage"` az eszköz által küldött üzenetek, amely szimulálja egy üzenetet, amely az alkalmazás háttér-vagy, hogy véglegesen kell tárolni, hogy azonnali beavatkozást igényel. Az alkalmazás az üzenet törzse alapuló útválasztási üzeneteket is támogatja.
   
   > [!NOTE]
   > Több, különböző esetekre, beleértve a cold-path feldolgozási mellett a gyakran használt adatok elérési útja az itt bemutatott példában üzenettulajdonságok üzenetek is használhatja.

2. Mentse és zárja be a **simulateddevice\SimulatedDevice.js** fájlt.

    > [!NOTE]
    > Határozottan javasoljuk, hogy például az exponenciális leállítási, az MSDN-cikkben leírtak újrapróbálkozási házirendje megvalósítása [átmeneti hiba kezelése].

## <a name="add-service-bus-queue-to-your-iot-hub-and-route-messages-to-it"></a>Az IoT hub és útvonal üzenetek hozzá Service Bus-üzenetsorba hozzáadása

Ebben a szakaszban hozzon létre egy Service Bus-üzenetsorba, csatlakoztassa az IoT hub, és konfigurálja az IoT hub, az üzenetek küldése az üzenetsorba, annak alapján, hogy az üzenet-tulajdonság. A Service Bus-üzenetsorok folyamat üzenetek módjáról további információkért lásd: [Ismerkedés a várólisták][lnk-sb-queues-node].

1. Hozzon létre egy Service Bus-üzenetsorba, a [Ismerkedés a várólisták][lnk-sb-queues-node]. Jegyezze fel az a névtér és a várólista nevét.

    > [!NOTE]
    > Service Bus-üzenetsorok és témakörök használatos az IoT-központok végpontjai nem lehet **munkamenetek** vagy **ismétlődő észlelési** engedélyezve van. Ha ezek a lehetőségek valamelyikét engedélyezve vannak, a végpont megjelenik **Unreachable** az Azure portálon.

2. Az Azure portálon, nyissa meg az IoT hub, és kattintson **végpontok**.

    ![Az IoT-központ végpontok][30]

3. Az a **végpontok** panelen kattintson **Hozzáadás** a sor hozzáadása az IoT hub tetején. A végpont neve **CriticalQueue** majd válassza ki a legördülő listákat **Service Bus-üzenetsorba**, ahol a várólista van a Service Bus-névtér és a várólista nevét. Amikor elkészült, kattintson a **OK** alján.  

    ![A végpont hozzáadása][31]

4. Most kattintson **útvonalak** az IoT Hub a. Kattintson a **Hozzáadás** útválasztási szabály, amely továbbítja az üzeneteket a várólista létrehozása a panel tetején az előzőekben adott hozzá. Válassza ki **eszközre küldött üzenetek** a adatforrásként. Adja meg `level="critical"` feltételt, és válassza a **CriticalQueue** útválasztási szabály végpontjának egyéni végpontként. Kattintson a **mentése** alján.  

    ![Egy útvonal hozzáadása][32]

    Győződjön meg arról, hogy a tartalék útvonal értéke **ON**. Ez a beállítás az alapértelmezett beállítása az IoT-központ.

    ![Tartalék útvonal][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(Választható) A várólista végpontról olvasása

Ebben a szakaszban hozzon létre egy Node.js-Konzolalkalmazás, amely olvassa be az IoT Service Bus kritikus üzeneteihez. További információ a [Ismerkedés a várólisták][lnk-sb-queues-node]. 

1. Hozzon létre egy `readcriticalqueue` nevű üres mappát. A(z) `readcriticalqueue` mappában hozzon létre egy package.json fájlt úgy, hogy beírja a következő parancsot a parancssorba. Fogadja el az összes alapértelmezett beállítást:

    ```cmd/sh
    npm init
    ```

2. A parancssorba a `readcriticalqueue` mappa telepítéséhez a következő parancsot a **azure** csomag:

    ```cmd/sh
    npm install azure --save
    ```

3. Egy szövegszerkesztő használatával hozzon létre egy **ReadCriticalQueue.js** fájlt a `readcriticalqueue` mappát.

4. Adja hozzá a következő `require` elején utasítások a **ReadCriticalQueue.js** fájlt:

    ```nodejs
    'use strict';

    var azure = require('azure');
    ```

5. Adja hozzá a következő változó deklarációjában, és cserélje le a helyőrző értékeket IoT Service Bus kapcsolati karakterláncot és a várólista nevét:

    ```nodejs
    var connectionString = '{iotservicebus connection string}';
    var queueName = '{iotservice bus queue name}';
    ```

6. Adja hozzá az alábbi két függvényt, amelyek kinyomtatják a konzol kimenetét:

    ```nodejs
    var serviceBusService = azure.createServiceBusService(connectionString);

    setInterval(function() {serviceBusService.receiveQueueMessage(queueName, function(error, receivedMessage) {
        if(!error){
            // Message received and deleted
            console.log(receivedMessage);
        } else { console.log(error); }
        });
    }, 3000);
    ```

7. Mentse és zárja be a **ReadCriticalQueue.js** fájlt.

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Most már készen áll a három alkalmazások futtatásához.

1. Futtatásához a **ReadDeviceToCloudMessages.js** alkalmazás, a parancssor vagy a rendszerhéj keresse meg a readdevicetocloudmessages mappát, és hajtsa végre a következő parancsot:

   ```cmd/sh
   node ReadDeviceToCloudMessages.js
   ```

   ![Futtassa a üzenetek d2c olvasása][readd2c]

2. Futtatásához a **ReadCriticalQueue.js** alkalmazás, a parancssor vagy a rendszerhéj keresse meg a readcriticalqueue mappát, és hajtsa végre a következő parancsot:

   ```cmd/sh
   node ReadCriticalQueue.js
   ```
   
   ![Olvasási kritikus üzeneteihez futtatása][readqueue]

3. Futtatásához a **SimulatedDevice.js** alkalmazást, a parancssor vagy a rendszerhéj keresse meg a simulateddevice mappát, és hajtsa végre a következő parancsot:

   ```cmd/sh
   node SimulatedDevice.js
   ```
   
   ![Szimulált eszköz futtatása][simulateddevice]

## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(Választható) Az IoT hub és útvonal üzenetek hozzá a tároló hozzáadása

Ebben a szakaszban hozzon létre egy tárfiókot, csatlakoztassa az IoT hub, és konfigurálja az IoT hub üzeneteket küldhet a fiók, az üzenet tulajdonság alapján. Felügyelni a tárolást kapcsolatos további információkért lásd: [Ismerkedés az Azure Storage szolgáltatással][Azure Storage].

 > [!NOTE]
   > Ha nem csak egy **végpont**, előfordulhat, hogy a telepítő a **StorageContainer** kívül a **CriticalQueue** , és mindkét simulatneously futtassa.

1. Hozzon létre egy tárfiókot, a [Azure Storage-dokumentációt][lnk-storage]. Jegyezze fel a fiók nevét.

2. Az Azure portálon, nyissa meg az IoT hub, és kattintson **végpontok**.

3. Az a **végpontok** panelen válassza a **CriticalQueue** végpontot, és kattintson **törlése**. Kattintson a **Igen**, és kattintson a **Hozzáadás**. A végpont neve **StorageContainer** majd válassza ki a legördülő listákat **Azure-tárolót**, és hozzon létre egy **tárfiók** és egy **tárolási tároló**.  Jegyezze fel a neveket.  Amikor elkészült, kattintson a **OK** alján. 

 > [!NOTE]
   > Ha nem csak egy **végpont**, nem kell törölnie a **CriticalQueue**.

4. Kattintson a **útvonalak** az IoT Hub a. Kattintson a **Hozzáadás** útválasztási szabály, amely továbbítja az üzeneteket a várólista létrehozása a panel tetején az előzőekben adott hozzá. Válassza ki **eszközre küldött üzenetek** a adatforrásként. Adja meg `level="storage"` feltételt, és válassza a **StorageContainer** útválasztási szabály végpontjának egyéni végpontként. Kattintson a **mentése** alján.  

    Győződjön meg arról, hogy a tartalék útvonal értéke **ON**. Ez a beállítás az alapértelmezett beállítása az IoT-központ.

1. Győződjön meg arról, hogy az előző alkalmazás **SimulatedDevice.js** futása. 

1. Az Azure portálon lépjen a tárfiókhoz a **Blob szolgáltatás**, kattintson a **blobok Tallózás...** .  Jelölje ki a tárolót, keresse meg és kattintson a JSON-fájlt, és kattintson **letöltése** adatok megtekintéséhez.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudta, hogyan megbízhatóan átirányítani az eszköz a felhőbe küldött üzeneteket az üzenetet az IoT-központ útválasztási funkcióra használatával.

A [IoT hubbal felhő eszközre üzenetek küldése] [ lnk-c2d] bemutatja, hogyan üzenetek küldése az eszközöket a megoldás háttérből.

Példák teljes végpontok közötti megoldások, amelyek használják az IoT-központot, lásd: [Azure IoT Suite][lnk-suite].

Az IoT hubbal megoldások fejlesztésével kapcsolatos további tudnivalókért tekintse meg a [IoT Hub fejlesztői útmutató].

Az üzenetet az IoT hubon útválasztási kapcsolatos további információkért lásd: [üzeneteket küldjön és fogadjon IoT hubbal][lnk-devguide-messaging].

<!-- Images. -->
<!-- TODO: UPDATE PICTURES -->
[simulateddevice]: ./media/iot-hub-node-node-process-d2c/runsimulateddevice.png
[readd2c]: ./media/iot-hub-node-node-process-d2c/runprocessinteractive.png
[readqueue]: ./media/iot-hub-node-node-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-node-node-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-node-node-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-node-node-process-d2c/route-creation.png
[33]: ./media/iot-hub-node-node-process-d2c/fallback-route.png

<!-- Links -->

[lnk-sb-queues-node]: ../service-bus-messaging/service-bus-nodejs-how-to-use-queues.md

[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[IoT Hub fejlesztői útmutató]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Ismerkedés az IoT-központ]: iot-hub-node-node-getstarted.md
[Azure IoT fejlesztői központ]: https://azure.microsoft.com/develop/iot
[átmeneti hiba kezelése]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Átmeneti hiba kezelése]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-c2d]: iot-hub-node-node-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
[lnk-storage]: https://docs.microsoft.com/azure/storage/