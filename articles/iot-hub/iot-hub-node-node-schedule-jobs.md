---
title: Feladatok ütemezése az Azure IoT Hub (Csomópont) használatával | Microsoft dokumentumok
description: Egy Azure IoT Hub-feladat ütemezése egy közvetlen metódus meghívására több eszközön. Az Azure IoT SDK-k node.js a szimulált eszköz alkalmazások és egy szolgáltatásalkalmazás a feladat futtatásához.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/16/2019
ms.custom: mqtt
ms.openlocfilehash: d7f9ce37ad85d39388eea90af263f59ce312a6b8
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732270"
---
# <a name="schedule-and-broadcast-jobs-nodejs"></a>Feladatok ütemezése és közvetítése (Node.js)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi, hogy egy háttéralkalmazás olyan feladatokat hozzon létre és kövessen nyomon, amelyek több millió eszközt ütemeznek és frissítenek.  A feladatok a következő műveletekhez használhatók:

* Eszköz kívánt tulajdonságainak frissítése
* Címkék frissítése
* Közvetlen metódusok meghívása

Fogalmilag a feladat betakarja az egyik ilyen műveleteket, és nyomon követi a végrehajtás előrehaladását egy eszközkészlet, amely egy eszköz iker lekérdezés határozza meg.  Például egy háttéralkalmazás segítségével egy feladat meghívására újraindítási módszer 10 000 eszközön, egy eszköz iker lekérdezés által megadott és egy későbbi időpontban ütemezett. Ez az alkalmazás ezután nyomon követheti a folyamatot, ahogy az egyes eszközök megkapják és végrehajtják az újraindítási módszert.

Az alábbi cikkekben további információ az egyes funkciókról:

* Ikereszköz és tulajdonságok: [Ismerkedés az ikereszközökkel](iot-hub-node-node-twin-getstarted.md) és [az oktatóanyaggal: Az ikereszköz tulajdonságainak használata](tutorial-device-twins.md)

* Közvetlen módszerek: [IoT Hub fejlesztői útmutató - közvetlen módszerek](iot-hub-devguide-direct-methods.md) és [oktatóanyag: közvetlen módszerek](quickstart-control-device-node.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a következőket mutatja be:

* Hozzon létre egy Node.js szimulált eszközalkalmazást, amely közvetlen metódussal rendelkezik, amely lehetővé teszi **a lockDoor**alkalmazást, amelyet a megoldás háttér-kezelője meghívhat.

* Hozzon létre egy Node.js konzolalkalmazást, amely meghívja a **lockDoor** közvetlen metódust a szimulált eszközalkalmazásban egy feladat használatával, és frissíti a kívánt tulajdonságokat egy eszközfeladat használatával.

Az oktatóanyag végén két Node.js alkalmazás sal rendelkezik:

* **simDevice.js**, amely csatlakozik az IoT hub az eszköz identitását, és kap egy **lockDoor** közvetlen metódust.

* **scheduleJobService.js**, amely meghívja a szimulált eszközalkalmazásban egy közvetlen metódust, és egy feladat használatával frissíti az ikereszköz kívánt tulajdonságait.

## <a name="prerequisites"></a>Előfeltételek

* Node.js 10.0.x vagy újabb verzió. [A fejlesztői környezet előkészítése](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) leírja, hogyan telepítheti a Node.js-t ehhez az oktatóanyaghoz Windows vagy Linux rendszeren.

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) néhány perc alatt.)

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. A cikkben szereplő eszközminta az MQTT protokollt használja, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben le van tiltva. A probléma megoldásáról további információt és a probléma megoldásáról a [Csatlakozás az IoT Hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)című témakörben talál.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hubban

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban hozzon létre egy Node.js konzolalkalmazást, amely a felhő által megnevezett közvetlen metódusra reagál, amely egy szimulált **lockDoor** metódust indít el.

1. Hozzon létre egy új üres mappát nevű **simDevice**.  A **simDevice** mappában hozzon létre egy package.json fájlt a következő paranccsal a parancssorból.  Fogadja el az összes alapértelmezett beállítást:

   ```console
   npm init
   ```

2. A **simDevice** mappában lévő parancssorból futtassa a következő parancsot az **azure-iot-device** SDK csomag és az **azure-iot-device-mqtt** csomag telepítéséhez:

   ```console
   npm install azure-iot-device azure-iot-device-mqtt --save
   ```

3. Szövegszerkesztő használatával hozzon létre egy új **simDevice.js** fájlt a **simDevice** mappában.

4. A **simDevice.js** fájl elején adja hozzá a következő "szükséges" utasításokat:

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Adjon hozzá egy **connectionString** változót, és ezzel hozzon létre egy **Ügyfél** példányt. Cserélje `{yourDeviceConnectionString}` le a helyőrző értéket a korábban másolt eszközkapcsolati karakterláncra.

    ```javascript
    var connectionString = '{yourDeviceConnectionString}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Adja hozzá a következő függvényt a **lockDoor** metódus kezeléséhez.

    ```javascript
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

7. Adja hozzá a következő kódot a **lockDoor** metódus kezelőjének regisztrálásához.

   ```javascript
   client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub. Register handler for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
   });
   ```

8. Mentse és zárja be a **simDevice.js** fájlt.

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket (például exponenciális visszamaradást) kell megvalósítania, ahogy azt a cikk, [átmeneti hibakezelés](/azure/architecture/best-practices/transient-faults)című cikk ben javasolt.
>

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub kapcsolati karakterláncának beszereznie

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Feladatok ütemezése közvetlen metódus hívásához és az ikereszköz tulajdonságainak frissítéséhez

Ebben a szakaszban hozzon létre egy Node.js konzolalkalmazást, amely egy távoli **lockDoor-t** kezdeményez egy eszközön egy közvetlen metódus használatával, és frissíti az ikereszköz tulajdonságait.

1. Hozzon létre egy **scheduleJobService**nevű új üres mappát.  A **scheduleJobService** mappában hozzon létre egy package.json fájlt a következő paranccsal a parancssorból.  Fogadja el az összes alapértelmezett beállítást:

    ```console
    npm init
    ```

2. A **scheduleJobService** mappában lévő parancssorból futtassa a következő parancsot az **azure-iothub** device SDK-csomag és az **azure-iot-device-mqtt** csomag telepítéséhez:

    ```console
    npm install azure-iothub uuid --save
    ```

3. Szövegszerkesztő használatával hozzon létre egy új **scheduleJobService.js** fájlt a **scheduleJobService** mappában.

4. Adja hozzá a következő "szükséges" utasításokat a **scheduleJobService.js** fájl elején:

    ```javascript
    'use strict';

    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Adja hozzá a következő változódeklarációkat. Cserélje `{iothubconnectionstring}` le a helyőrző értéket az [IoT hub kapcsolati karakterláncának bemásolása](#get-the-iot-hub-connection-string)című részben másolt értékre. Ha a **myDeviceId-tól**eltérő eszközt regisztrált, győződjön meg róla, hogy módosítja azt a lekérdezési feltételben.

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```

6. Adja hozzá a következő függvényt, amely a feladat végrehajtásának figyelésére szolgál:

    ```javascript
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

7. Adja hozzá a következő kódot az eszközmetódust megadó feladat ütemezéséhez:
  
    ```javascript
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

8. Adja hozzá a következő kódot az ikereszköz frissítéséhez:

    ```javascript
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

9. Mentse és zárja be a **scheduleJobService.js** fájlt.

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A **simDevice** mappa parancssorában futtassa a következő parancsot az újraindítási közvetlen metódus figyeléséhez.

    ```console
    node simDevice.js
    ```

2. A **scheduleJobService** mappa parancssorában futtassa a következő parancsot az ajtó zárolásához és az ikerhálózat frissítéséhez

    ```console
    node scheduleJobService.js
    ```

3. A konzolon megjelenik az eszköz közvetlen metódusra adott válasza és a feladat állapota.

   A következőkben látható az eszköz közvetlen módszerre adott válasza:

   ![Szimulált eszközalkalmazás-kimenet](./media/iot-hub-node-node-schedule-jobs/sim-device.png)

   A következőkben a közvetlen metódus és az ikereszköz-frissítés szolgáltatásütemezési feladatait, valamint a befejezésig futó feladatokat mutatja be:

   ![A szimulált eszközalkalmazás futtatása](./media/iot-hub-node-node-schedule-jobs/schedule-job-service.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy feladat segítségével ütemezte a közvetlen metódust egy eszközre, és az ikereszköz tulajdonságainak frissítését.

Az IoT Hub és az eszközfelügyeleti minták , például a távoli belső vezérlőprogram frissítésének megkezdéséhez olvassa el az [Oktatóanyag: Belső vezérlőprogram-frissítés .](tutorial-firmware-update.md)

Az IoT Hub első lépései az [Azure IoT Edge – első lépések.](../iot-edge/tutorial-simulate-device-linux.md)
