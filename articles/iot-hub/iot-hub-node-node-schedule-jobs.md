---
title: Feladatok ütemezhetnek az Azure IoT Hub (node) segítségével | Microsoft Docs
description: Azure IoT Hub-feladatok ütemezésének beütemezés közvetlen metódus több eszközön való meghívásához. A Node. js-hez készült Azure IoT SDK-k segítségével megvalósíthatja a szimulált eszköz alkalmazásait és egy szolgáltatási alkalmazást a feladatok futtatásához.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 10/06/2017
ms.openlocfilehash: 243f4e63cc04bca018c2bf69492dccf163e92b73
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780833"
---
# <a name="schedule-and-broadcast-jobs-nodejs"></a>Feladatok ütemezett és szórása (node. js)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a háttérbeli alkalmazások számára, hogy több millió eszközt ütemezzen és frissítsen feladatok létrehozásához és nyomon követéséhez.  A feladatokat a következő műveletekhez használhatja:

* Eszköz kívánt tulajdonságainak frissítése
* Címkék frissítése
* Közvetlen metódusok meghívása

Elméletileg a feladatok az alábbi műveletek egyikét betakarják, és nyomon követik a végrehajtás előrehaladását egy adott eszközön, amelyet az eszközök kettős lekérdezése határoz meg.  Egy háttérbeli alkalmazás például felhasználhat egy feladatot egy újraindítási módszer meghívására 10 000-eszközökön, egy eszköz kettős lekérdezésével és egy későbbi időpontban ütemezve. Az alkalmazás ezután nyomon követheti az előrehaladást, mivel az egyes eszközök megkapják és végrehajtják az újraindítási módszert.

További információk a következő cikkekben felsorolt lehetőségekről:

* Eszköz Twin és tulajdonságai: [Ismerkedés az eszközök ikrekkel](iot-hub-node-node-twin-getstarted.md) és [oktatóanyaggal: Az eszköz Twin tulajdonságainak használata](tutorial-device-twins.md)

* Közvetlen metódusok: [IoT hub fejlesztői útmutató – közvetlen](iot-hub-devguide-direct-methods.md) metódusok és [oktatóanyag: közvetlen metódusok](quickstart-control-device-node.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a következőket mutatja be:

* Hozzon létre egy olyan, a Node. js által szimulált eszköz alkalmazást, amely közvetlen metódussal rendelkezik, amely lehetővé teszi a **lockDoor**, amelyet a megoldás hátterében hívhat meg.

* Hozzon létre egy Node. js-konzol alkalmazást, amely meghívja a **lockDoor** Direct metódust a szimulált eszköz alkalmazásban egy feladattal, és a kívánt tulajdonságokat egy eszköz feladattal frissíti.

Az oktatóanyag végén két Node. js-alkalmazás található:

* **simDevice. js**, amely az IoT hubhoz csatlakozik az eszköz identitásával, és egy **lockDoor** Direct metódust kap.

* **scheduleJobService. js**, amely egy közvetlen metódust hív meg a szimulált eszköz alkalmazásban, és feladatokkal frissíti az eszközhöz tartozó dupla kívánt tulajdonságokat.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* A Node. js 10.0. x vagy újabb verziójának [előkészítése a fejlesztési környezet előkészítéséhez](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) leírja, hogyan telepítheti a Node. js-t ehhez az oktatóanyaghoz Windows vagy Linux rendszeren.

* Aktív Azure-fiók. (Ha nincs fiókja, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) .)

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hub-ban

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban egy Node. js-konzol alkalmazást hoz létre, amely a felhő által meghívott közvetlen metódusra válaszol, amely egy szimulált **lockDoor** metódust indít el.

1. Hozzon létre egy új, **simDevice**nevű üres mappát.  A **simDevice** mappában hozzon létre egy Package. JSON fájlt a következő parancs parancssorba való beírásával.  Fogadja el az összes alapértelmezett beállítást:

   ```
   npm init
   ```

2. A **simDevice** mappában a parancssorban futtassa a következő parancsot az **Azure-IOT-Device** eszközoldali SDK csomag és az **Azure-IOT-Device-mqtt** csomag telepítéséhez:
   
   ```
   npm install azure-iot-device azure-iot-device-mqtt --save
   ```

3. Egy szövegszerkesztővel hozzon létre egy új **simDevice. js** fájlt a **simDevice** mappában.

4. Adja hozzá a következő "require" utasítást a **simDevice. js** fájl elejéhez:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Adjon hozzá egy **connectionString** változót, és ezzel hozzon létre egy **Ügyfél** példányt.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Adja hozzá a következő függvényt a **lockDoor** metódus kezeléséhez.
   
    ```
    var onLockDoor = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        console.log('Locking Door!');
    };
    ```

7. Adja hozzá a következő kódot a kezelő **lockDoor** metódushoz való regisztrálásához.

   ```
   client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub. Register handler for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
   });
   ```

8. Mentse és zárjuk be a **simDevice. js** fájlt.

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban az újrapróbálkozási szabályzatokat (például egy exponenciális leállítási) kell megvalósítani, ahogy azt a cikkben is ismertetjük, az [átmeneti hibák kezelésére](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Feladatok ütemezhetnek közvetlen metódus hívásához és az eszközök Twin tulajdonságainak frissítéséhez

Ebben a szakaszban egy Node. js-konzol alkalmazást hoz létre, amely egy közvetlen metódus használatával kezdeményez egy távoli **lockDoor** az eszközön, és frissíti az eszköz Twin tulajdonságait.

1. Hozzon létre egy új, **scheduleJobService**nevű üres mappát.  A **scheduleJobService** mappában hozzon létre egy Package. JSON fájlt a következő parancs parancssorba való beírásával.  Fogadja el az összes alapértelmezett beállítást:

    ```
    npm init
    ```

2. A **scheduleJobService** mappában a parancssorban futtassa a következő parancsot az **Azure-iothub** Device SDK csomag és az **Azure-IOT-Device-mqtt** csomag telepítéséhez:
   
    ```
    npm install azure-iothub uuid --save
    ```

3. Egy szövegszerkesztővel hozzon létre egy új **scheduleJobService. js** fájlt a **scheduleJobService** mappában.

4. Adja hozzá a következő "require" utasítást a **dmpatterns_gscheduleJobServiceetstarted_service. js** fájl elejéhez:
   
    ```
    'use strict';
   
    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Adja hozzá a következő változó deklarációkat, és cserélje le a helyőrző értékeket:
   
    ```
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```

6. Adja hozzá a következő függvényt, amely a feladat végrehajtásának figyelésére szolgál:
   
    ```
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```

7. Adja hozzá a következő kódot az eszköz metódusát meghívó feladatokhoz:
   
    ```
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        responseTimeoutInSeconds: 15 // Timeout after 15 seconds if device is unable to process method
    };
   
    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                queryCondition,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```

8. Adja hozzá a következő kódot a feladatok ütemezett frissítéséhez az eszköz kettős frissítése céljából:
   
    ```
    var twinPatch = {
       etag: '*',
       properties: {
           desired: {
               building: '43',
               floor: 3
           }
       }
    };
   
    var twinJobId = uuid.v4();
   
    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                queryCondition,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```

9. Mentse és zárjuk be a **scheduleJobService. js** fájlt.

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A **simDevice** mappában a parancssorban futtassa a következő parancsot, hogy megkezdje az újraindítási közvetlen metódus figyelését.
   
    ```
    node simDevice.js
    ```

2. A **scheduleJobService** mappában található parancssorban futtassa a következő parancsot a feladatok elindításához az ajtó zárolásához és a Twin frissítéséhez.
   
    ```
    node scheduleJobService.js
    ```

3. Megjelenik az eszköz válasza a Direct metódusra a konzolon.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy olyan feladatot használt, amely egy közvetlen metódust ütemez egy eszközre, és az eszköz Twin tulajdonságainak frissítését.

Ha továbbra is szeretné megkezdeni a IoT hub és az eszközkezelés mintáit, például a távoli belső [vezérlőprogram frissítését, tekintse meg az oktatóanyagot: A belső vezérlőprogram frissítése](tutorial-firmware-update.md).

A IoT Hub első lépéseinek folytatásához tekintse meg a [Azure IoT Edge első lépéseivel](../iot-edge/tutorial-simulate-device-linux.md)foglalkozó témakört.
