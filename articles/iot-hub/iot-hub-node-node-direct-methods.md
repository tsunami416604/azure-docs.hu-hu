---
title: "Az Azure IoT Hub közvetlen módszerek (csomópont) |} Microsoft Docs"
description: "Hogyan használható az Azure IoT Hub közvetlen módszerek. Az Azure IoT SDK for Node.js használatával megvalósítható a szimulált eszköz alkalmazást, amely magában foglalja a közvetlen módszer és a service-alkalmazást, amely hívja meg a közvetlen módszer."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: ea9c73ca-7778-4e38-a8f1-0bee9d142f04
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: nberdy
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9a73c25724a239e56c3ea62a8452bb7c3a2b51be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-direct-methods-on-your-iot-device-with-nodejs"></a>Az IoT-eszközön a Node.js közvetlen módszerekkel
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

Ez az oktatóanyag végén két Node.js konzol alkalmazások közül választhat:

* **CallMethodOnDevice.js**, amely egy metódus meghívja a szimulált eszköz alkalmazás és a válasz megjeleníti.
* **SimulatedDevice.js**, amely a korábban létrehozott eszközidentitás az IoT hub kapcsolódik, és válaszol-e a felhő által meghívott metódus.

> [!NOTE]
> Az Azure IoT SDK-kat használhatja az eszközökön és a megoldás háttérrendszerén futó alkalmazások összeállításához egyaránt. Ezekről az [Azure IoT SDK-k][lnk-hub-sdks] című témakörben talál további információt.
> 
> 

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* A Node.js 4.0.x vagy újabb verziója.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása
Ebben a szakaszban egy Node.js-Konzolalkalmazás, amely válaszol a felhő által meghívott metódus hoz létre.

1. Hozzon létre egy új, **simulateddevice** nevű üres mappát. A **simulateddevice** mappában hozza létre a package.json fájlt úgy, hogy beírja a következő parancsot a parancssorba. Fogadja el az összes alapértelmezett beállítást:
   
    ```
    npm init
    ```
2. Telepítse az **azure-iot-device** eszközoldali SDK csomagot és az **azure-iot-device-mqtt** csomagot. Ehhez futtassa a parancssorban a következő parancsot a **simulateddevice** mappában:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Egy szövegszerkesztővel hozzon létre egy új **SimulatedDevice.js** fájlt a **simulateddevice** mappában.
4. Adja hozzá a következő `require` utasításokat a **SimulatedDevice.js** fájl elejéhez:
   
    ```
    'use strict';
   
    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```
5. Adja hozzá a **connectionString** változó, és hozzon létre egy **DeviceClient** példány. Cserélje le **{eszköz kapcsolati karakterlánc}** az eszköz kapcsolattal karakterlánc Ön hozott létre a *hozzon létre egy eszközidentitás* szakasz:
   
    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```
6. Adja hozzá a következő függvény az eszközön a metódus végrehajtásához:
   
    ```
    function onWriteLine(request, response) {
        console.log(request.payload);
   
        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error ocurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```
7. Nyissa meg a kapcsolatot az IoT-központ és kezdő inicializálni a metódus figyelőt:
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```
8. Mentse és zárja be a **SimulatedDevice.js** fájlt.

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban, meg kell valósítania újrapróbálkozási szabályzatok (például újrapróbálkozási), az MSDN-cikkben leírtak [átmeneti hiba kezelése][lnk-transient-faults].
> 
> 

## <a name="call-a-method-on-a-device"></a>A metódus hívása az eszközön
Ebben a szakaszban hozzon létre egy Node.js-Konzolalkalmazás, amely egy metódus meghívja a szimulált eszköz alkalmazás és a válasz megjeleníti.

1. Hozzon létre egy új üres nevű **callmethodondevice**. Az a **callmethodondevice** mappa, hozzon létre egy package.json fájlt parancsot a parancssorba az alábbi parancs segítségével. Fogadja el az összes alapértelmezett beállítást:
   
    ```
    npm init
    ```
2. A parancssorba a **callmethodondevice** mappa telepítéséhez a következő parancsot a **azure-IOT hubbal** csomag:
   
    ```
    npm install azure-iothub --save
    ```
3. Egy szövegszerkesztő használatával hozzon létre egy **CallMethodOnDevice.js** fájlt a **callmethodondevice** mappa.
4. Adja hozzá a következő `require` elején utasítások a **CallMethodOnDevice.js** fájlt:
   
    ```
    'use strict';
   
    var Client = require('azure-iothub').Client;
    ```
5. Adja hozzá a következő változódeklarációt, és a helyőrző értékét cserélje le az IoT Hub kapcsolati karakterláncára:
   
    ```
    var connectionString = '{iothub connection string}';
    var methodName = 'writeLine';
    var deviceId = 'myDeviceId';
    ```
6. Az ügyfél számára, nyissa meg a kapcsolatot az IoT hub létrehozása.
   
    ```
    var client = Client.fromConnectionString(connectionString);
    ```
7. Adja hozzá a következő működnek, mint az eszköz metódus meghívása, és nyomtassa ki az eszköz válasza a konzolhoz:
   
    ```
    var methodParams = {
        methodName: methodName,
        payload: 'hello world',
        timeoutInSeconds: 30
    };
   
    client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
        if (err) {
            console.error('Failed to invoke method \'' + methodName + '\': ' + err.message);
        } else {
            console.log(methodName + ' on ' + deviceId + ':');
            console.log(JSON.stringify(result, null, 2));
        }
    });
    ```
8. Mentse és zárja be a **CallMethodOnDevice.js** fájlt.

## <a name="run-the-apps"></a>Az alkalmazások futtatása
Most már készen áll az alkalmazások futtatására.

1. A parancsot a parancssorba a **simulateddevice** mappa megkezdeni a figyelést az IoT Hub a metódushívások a következő parancsot:
   
    ```
    node SimulatedDevice.js
    ```
   
    ![][7]
2. A parancsot a parancssorba a **callmethodondevice** mappa, a következő parancsot az IoT hub figyelés megkezdéséhez:
   
    ```
    node CallMethodOnDevice.js 
    ```
   
    ![][8]
3. A metódus által az üzenet és az alkalmazást, amely az eszközről a válasz a metódus megjelenítési nevű nyomtasson reagálni az eszköz jelenik meg:
   
    ![][9]

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban egy új IoT Hubot konfigurált az Azure-portálon, majd létrehozott egy eszközidentitást az IoT Hub identitásjegyzékében. Ez az eszközazonosító segítségével engedélyezheti a felhő által meghívott módszerek reagálni a szimulált eszköz alkalmazás. Létrehozott egy alkalmazást, amely meghívja a módszerek az eszközön, és megjeleníti az eszköz válaszára is. 

További bevezetés az IoT Hub használatába, valamint egyéb IoT-forgatókönyvek megismerése:

* [Ismerkedés az IoT-központ]
* [Több eszközön feladatok ütemezése][lnk-devguide-jobs]

Megtudhatja, hogyan terjeszthető ki az IoT-megoldás és az ütemezések metódushívások több eszközön, tekintse meg a [ütemezés és a szórásos feladatok] [ lnk-tutorial-jobs] oktatóanyag.

<!-- Images. -->
[7]: ./media/iot-hub-node-node-direct-methods/run-simulated-device.png
[8]: ./media/iot-hub-node-node-direct-methods/run-callmethodondevice.png
[9]: ./media/iot-hub-node-node-direct-methods/methods-output.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Ismerkedés az IoT-központ]: iot-hub-node-node-getstarted.md
