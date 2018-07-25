---
title: Azure IoT Hub-feladatok ismertetése |} A Microsoft Docs
description: Az IoT hub fejlesztői útmutató – több eszközön futtatandó feladatok ütemezése csatlakoztatva. Feladatok frissítheti a címkéket és kívánt tulajdonságok és több eszközön közvetlen metódusok meghívása.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 460c7d24b2810de41e20ea803ded2ea988613f10
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223796"
---
# <a name="schedule-jobs-on-multiple-devices"></a>Feladatok ütemezése több eszközön

Az Azure IoT Hub lehetővé teszi, hogy a építőelemek, például számos [eszköz-ikertulajdonságok és címkék] [ lnk-twin-devguide] és [közvetlen metódusok][lnk-dev-methods].  Általában a háttér-alkalmazások engedélyezése eszközadminisztrátorok és üzemeltetői, módosíthatja és használhatja az IoT-eszközök tömeges és a egy megadott időpontban.  Feladatok ütemezett időpontban ikereszköz-frissítések és az eszközök ellen közvetlen metódusok végrehajtása.  Az operátor például egy háttér-alkalmazást, amely kezdeményezi és követi nyomon egy feladatot, amely az eszközök, amelyek nem lenne zavaró a az épület a műveletek egyszerre 43 és emelet 3 épületben újraindítás használja.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Vegye figyelembe, hogy bármelyikével feladatok ütemezését és nyomon követheti a szolgálatot a következő tevékenységeket meg az eszközök:

* Eszköz kívánt tulajdonságainak frissítése
* A címkék frissítése
* Közvetlen metódusok meghívása

## <a name="job-lifecycle"></a>Feladat életciklusa
Feladatok a megoldás háttérrendszere által kezdeményezett, és az IoT Hub által karbantartott.  Egy feladat keresztül a szolgáltatás által használt URI-t is kezdeményezhető (`{iot hub}/jobs/v2/{device id}/methods/<jobID>?api-version=2016-11-14`), és folyamatban van egy végrehajtó feladaton keresztül a szolgáltatás által használt URI lekérdezése (`{iot hub}/jobs/v2/<jobId>?api-version=2016-11-14`). Feladat lekérdezés futtatása a futó feladatok, ha egy feladat állapotának frissítéséhez.

> [!NOTE]
> Ha olyan feladatot kezdeményez, nevét és értékeit tartalmazhat US-ASCII nyomtatható alfanumerikus, kivéve a következő set: `$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`.

## <a name="jobs-to-execute-direct-methods"></a>Közvetlen metódusok végrehajtása feladatok
Az alábbi kódrészlet bemutatja a HTTPS 1.1 a kérelem részletes adatainak végre egy [közvetlen metódus] [ lnk-dev-methods] meg az eszközök egy feladat használatával:

    PUT /jobs/v2/<jobId>?api-version=2016-11-14

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleDirectRequest', 
        cloudToDeviceMethod: {
            methodName: '<methodName>',
            payload: <payload>,                 
            responseTimeoutInSeconds: methodTimeoutInSeconds 
        },
        queryCondition: '<queryOrDevices>', // query condition
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        
    }

A lekérdezési feltétel is lehet egyetlen Eszközazonosítót vagy eszköz a következő példákban szemléltetett módon azonosítók listáját:

```
queryCondition = "deviceId = 'MyDevice1'"
queryCondition = "deviceId IN ['MyDevice1','MyDevice2']"
queryCondition = "deviceId IN ['MyDevice1']
```
[IoT Hub lekérdezési nyelv] [ lnk-query] IoT Hub lekérdezési nyelv további részletesen ismerteti.

## <a name="jobs-to-update-device-twin-properties"></a>Eszköz-ikertulajdonságok frissíteni feladatok
A következő kódrészlet azt mutatja be, HTTPS 1.1 kérés részleteinek frissítése egy feladat használatával eszköz-ikertulajdonságok:

    PUT /jobs/v2/<jobId>?api-version=2016-11-14
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleTwinUpdate', 
        updateTwin: <patch>                 // Valid JSON object
        queryCondition: '<queryOrDevices>', // query condition
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        // format TBD
    }

## <a name="querying-for-progress-on-jobs"></a>A folyamatban lévő feladatok lekérdezése
Az alábbi kódrészlet bemutatja a feladatok lekérdezése HTTPS 1.1 kérelem részletei:

    GET /jobs/v2/query?api-version=2016-11-14[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

A válaszból biztosítja a continuationtoken argumentumot használja.  

A feladat végrehajtási állapotát minden egyes eszköz segítségével lekérdezhető a [az IoT Hub lekérdezési nyelv az ikereszközökhöz, feladatokkal és üzenet-útválasztása][lnk-query].

## <a name="jobs-properties"></a>Feladat tulajdonságai
Az alábbi lista a tulajdonságait, és a vonatkozó leírásokat, amely használható lekérdezésekor feladatok vagy a feladat eredményeinek tartalmazza.

| Tulajdonság | Leírás |
| --- | --- |
| **jobId** |Alkalmazás azonosítója a feladatra vonatkozóan megadott. |
| **startTime** |Alkalmazás a megadott kezdési idő (ISO-8601) a feladathoz. |
| **endTime** |Az IoT Hub dátuma (ISO-8601) amikor a feladat befejeződött-e megadva. Csak azt követően a feladat eléri a "kész" állapot érvényes. |
| **type** |Feladatok típusai: |
| | **scheduledUpdateTwin**: egy feladatot, kívánt tulajdonságok vagy címkék frissítése. |
| | **scheduledDeviceMethod**: ikereszközök különböző eszköz metódus meghívásához használt feladat. |
| **Állapot** |A feladat jelenlegi állapota. Lehetséges értékek a állapota: |
| | **függőben lévő**: ütemezett és váró észlelnie kell a feladatokat végző szolgáltatás. |
| | **ütemezett**: ütemezi későbbi időpontra. |
| | **futó**: a jelenleg aktív feladat. |
| | **meg lett szakítva**: feladat meg lett szakítva. |
| | **nem sikerült**: a feladat nem sikerült. |
| | **Befejezett**: feladat befejeződött. |
| **deviceJobStatistics** |A feladat-végrehajtási statisztikája. |
| | **deviceJobStatistics** tulajdonságai: |
| | **deviceJobStatistics.deviceCount**: a feladat lévő eszközök száma. |
| | **deviceJobStatistics.failedCount**: Ha a feladat nem sikerült az eszközök száma. |
| | **deviceJobStatistics.succeededCount**: készülékek, ahol a feladat sikeresen befejeződött. |
| | **deviceJobStatistics.runningCount**: a feladat jelenleg futó eszközök számát. |
| | **deviceJobStatistics.pendingCount**: a feladat futtatása függőben lévő eszközök száma. |

### <a name="additional-reference-material"></a>További – referenciaanyag
Az IoT Hub fejlesztői útmutató más referencia témakörei a következők:

* [IoT Hub-végpontok] [ lnk-endpoints] ismerteti a különféle végpontok, amely minden IoT-központ közzéteszi a futásidejű és felügyeleti műveletekhez.
* [Sávszélesség-szabályozási és kvóták] [ lnk-quotas] ismerteti a kvótákat, az IoT Hub szolgáltatás és a szabályozás működésék, számítson, ha a szolgáltatás használatához.
* [Az Azure IoT eszköz- és szolgáltatásspecifikus SDK-k] [ lnk-sdks] felsorolja a különböző nyelvű SDK-ban is használhatja az IoT Hub szolgáltatással kommunikáló eszközt és szolgáltatást is alkalmazások fejlesztése során.
* [Az IoT Hub lekérdezési nyelv az ikereszközökhöz, feladatokkal és üzenet-útválasztása] [ lnk-query] ismerteti az IoT Hub lekérdezési nyelv. A lekérdező nyelv használata az IoT Hub az ikereszközökhöz és feladatokhoz kapcsolatos információk lekérése.
* [IoT Hub MQTT-támogatás] [ lnk-devguide-mqtt] további információ az IoT Hub-támogatásról nyújt az MQTT protokoll.

## <a name="next-steps"></a>További lépések
Próbálja ki a jelen cikkben ismertetett fogalmakat, tekintse meg a következő IoT Hub-oktatóanyag:

* [Feladatok ütemezése és szórása][lnk-jobs-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-jobs-tutorial]: iot-hub-node-node-schedule-jobs.md
[lnk-c2d-methods]: quickstart-control-device-node.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-devguide]: iot-hub-devguide-device-twins.md
