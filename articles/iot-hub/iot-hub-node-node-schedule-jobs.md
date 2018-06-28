---
title: Az Azure IoT Hub (csomópont) feladatok ütemezése |} Microsoft Docs
description: How to Schedule a több eszközre közvetlen metódus egy Azure IoT Hub-feladat ütemezése. Az Azure IoT SDK for Node.js használatával megvalósítható a szimulált eszköz alkalmazások és a service-alkalmazást, a feladat futtatásához.
author: juanjperez
manager: cberlin
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 10/06/2017
ms.author: juanpere
ms.openlocfilehash: 42deb210c55cd4a6c2aa2c7757ed87f8f706c58f
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "34634107"
---
# <a name="schedule-and-broadcast-jobs-node"></a>Ütemezés és a feladatok (csomópont)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi egy háttér-alkalmazás létrehozása és nyomon követheti a feladatok ütemezése és eszközök millióira frissítése.  Feladatok is használható a következő műveleteket:

* Eszköz kívánt tulajdonságainak frissítése
* Címkék frissítése
* Közvetlen metódusok

Egy feladat fogalmilag, becsomagolja az alábbi műveletek egyikét, és nyomon követi a folyamatot való összevetéssel az eszközök, eszköz két lekérdezést által definiált végrehajtás.  Például egy háttér-alkalmazást, egy feladat 10 000 eszközök, eszköz iker lekérdezés által megadott, és egy későbbi időpontban ütemezett újraindítás metódus használható.  Az alkalmazás egyes eszközök kap, és az újraindítás metódus végrehajtása majd előrehaladásának.

További információ az egyes képességek a cikkeiben:

* A két eszköz és a tulajdonságok: [Ismerkedés az eszköz twins] [ lnk-get-started-twin] és [oktatóanyag: kettős eszköztulajdonságok használata][lnk-twin-props]
* Közvetlen módszerek: [IoT Hub fejlesztői útmutató - közvetlen módszerek] [ lnk-dev-methods] és [oktatóanyag: közvetlen módszer][lnk-c2d-methods]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a következőket mutatja be:

* Közvetlen metódus, amely lehetővé teszi a Node.js-szimulált eszköz alkalmazásokat **lockDoor**, amely a megoldás háttérrendszeréhez hívható.
* Hozzon létre egy Node.js-Konzolalkalmazás, amely behívja a **lockDoor** közvetlen módszer a alkalmazásban szimulált eszköz egy feladat és a frissítések a kívánt eszköz feladat használatával tulajdonságokkal.

Ez az oktatóanyag végén két Node.js alkalmazások közül választhat:

**simDevice.js**, amely az IoT hub eszköz identitással csatlakozik, és megkapja a **lockDoor** közvetlen módszer.

**scheduleJobService.js**, amely közvetlen metódus meghívja a szimulált eszköz alkalmazásban, és frissíti az eszköz iker szükséges tulajdonságok feladat használatával.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* NODE.js-verzió 4.0.x vagy újabb verzióját, <br/>  [A fejlesztőkörnyezet előkészítése] [ lnk-dev-setup] ismerteti, hogyan telepítse a Node.js-ebben az oktatóanyagban a Windows vagy Linux.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása
Ebben a szakaszban egy Node.js-Konzolalkalmazás, amely válaszol a felhőbe, amely elindítja a szimulált által meghívott közvetlen metódus létrehozása **lockDoor** metódust.

1. Hozzon létre egy új üres nevű **simDevice**.  Az a **simDevice** mappa, hozzon létre egy package.json fájlt parancsot a parancssorba az alábbi parancs segítségével.  Fogadja el az összes alapértelmezett beállítást:
   
    ```
    npm init
    ```
2. Parancsot a parancssorba a a **simDevice** mappa telepítéséhez a következő parancsot a **azure iot-eszközök** eszköz SDK-csomagot és **azure-iot-eszközök – mqtt** csomag:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Egy szövegszerkesztő használatával hozzon létre egy új **simDevice.js** fájlt a **simDevice** mappa.
4. Adja hozzá a következő "szükséges" utasítások elején a **simDevice.js** fájlt:
   
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
6. Adja hozzá a következő függvény kezelni a **lockDoor** metódust.
   
    ```
    var onLockDoor = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        console.log('Locking Door!');
    };
    ```
7. Az alábbi kódot a kezelőt regisztrálni a **lockDoor** metódust.
   
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
8. Mentse és zárja be a **simDevice.js** fájlt.

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket is meg kell valósítania (például egy exponenciális leállítást) a [tranziens hibakezelést][lnk-transient-faults] ismertető MSDN-cikkben leírtak szerint.
> 
> 

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Ütemezett feladatok közvetlen metódus hívása, és egy eszköz iker tulajdonságainak frissítése
Ebben a szakaszban egy távoli kezdeményező Node.js-Konzolalkalmazás létrehozása **lockDoor** közvetlen módszert használ az eszközön, és az eszköz iker tulajdonságainak frissítéséhez.

1. Hozzon létre egy új üres nevű **scheduleJobService**.  Az a **scheduleJobService** mappa, hozzon létre egy package.json fájlt parancsot a parancssorba az alábbi parancs segítségével.  Fogadja el az összes alapértelmezett beállítást:
   
    ```
    npm init
    ```
2. A parancssorban a a **scheduleJobService** mappa telepítéséhez a következő parancsot a **azure-IOT hubbal** eszköz SDK-csomagot és **azure-iot-eszközök – mqtt** csomag:
   
    ```
    npm install azure-iothub uuid --save
    ```
3. Egy szövegszerkesztő használatával hozzon létre egy új **scheduleJobService.js** fájlt a **scheduleJobService** mappa.
4. Adja hozzá a következő "szükséges" utasítások elején a **dmpatterns_gscheduleJobServiceetstarted_service.js** fájlt:
   
    ```
    'use strict';
   
    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```
5. Adja hozzá a következő változók deklarációja, és cserélje le a helyőrző értékeket:
   
    ```
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```
6. Adja hozzá a következő függvény, amellyel a figyelheti a feladat végrehajtása:
   
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
7. Adja hozzá a ütemezni a feladatot, amely az eszköz metódust hívja a következő kódot:
   
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
8. Adja hozzá a következő kódot az eszköz iker frissítése feladat ütemezése:
   
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
9. Mentse és zárja be a **scheduleJobService.js** fájlt.

## <a name="run-the-applications"></a>Az alkalmazások futtatása
Most már készen áll az alkalmazások futtatására.

1. A parancssorban a **simDevice** mappa, a következő parancsot a rendszer újraindítása közvetlen módszer figyelését.
   
    ```
    node simDevice.js
    ```
2. A parancssorban a **scheduleJobService** mappa, a következő parancsot az ajtó zárolja, és frissíti a kettős feladatok elindítása
   
    ```
    node scheduleJobService.js
    ```
3. A közvetlen módszer a konzolon eszköz válasz megjelenik.

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban egy feladat ütemezése a közvetlen módszer egy eszköz és a két eszköz tulajdonságok frissítése használt.

A folytatáshoz, a légkondicionáló frissítést keresztül Ismerkedés az IoT-központ és az eszköz felügyeleti minták például távolról, lásd:

[Oktatóanyag: Módjáról a belső vezérlőprogram frissítése][lnk-fwupdate]

Ismerkedés az IoT-központ a folytatáshoz tekintse meg a [Ismerkedés az Azure IoT peremhálózati][lnk-iot-edge].

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
