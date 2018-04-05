---
title: Azure IoT Hub feladatok ismertetése |} Microsoft Docs
description: Az IoT hub fejlesztői útmutató - feladatütemezés több eszközökön való futtatására csatlakoztatva. Feladatok címkék és a kívánt tulajdonságok frissítése, és több eszközön közvetlen metódusok.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: fe78458f-4f14-4358-ac83-4f7bd14ee8da
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 367eb703465b2fbc6f1e06a383bc9df709cabe78
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="schedule-jobs-on-multiple-devices"></a>Feladatok ütemezése több eszközön

Az Azure IoT-központ lehetővé teszi, hogy a építőelemek, például számos [iker eszköztulajdonságok és címkék] [ lnk-twin-devguide] és [módszerek közvetlen][lnk-dev-methods].  Háttér-alkalmazások általában eszközadminisztrátorok és operátorok frissítése, és az IoT-eszközök tömeges és ütemezett időpontban interaktívan tesznek lehetővé.  Feladatok végrehajtási iker eszközfrissítésekhez és a közvetlen módszer alapján az eszközök egy megadott időpontban.  Például egy olyan operátort használja egy háttér-alkalmazást, amely kezdeményezi és követi nyomon a feladat újraindítja az eszközök nem lenne az épület műveletekre zavaró egyszerre 43 és emelet 3 fejlesztése során.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Feladatok esetekben érdemes kell ütemezni, és nyomon követni a következő tevékenységek bármelyike meg az eszközök:

* Eszköz kívánt tulajdonságainak frissítése
* Címkék frissítése
* Közvetlen metódusok

## <a name="job-lifecycle"></a>Feladat életciklusa
Feladatok a megoldás háttérrendszeréhez által kezdeményezett, és az IoT-központ által kezelt.  Egy feladat keresztül elérhető szolgáltatás URI is kezdeményezhető (`{iot hub}/jobs/v2/{device id}/methods/<jobID>?api-version=2016-11-14`) és folyamatban van a egy végrehajtás alatt álló feladat keresztül elérhető szolgáltatás URI-lekérdezés (`{iot hub}/jobs/v2/<jobId>?api-version=2016-11-14`). Feladat lekérdezés futtatása a futó feladatok, ha egy feladat indításakor állapotának frissítéséhez.

> [!NOTE]
> Elindít egy feladatot, nevét és értékeit tartalmazhatnak US-ASCII nyomtatható alfanumerikus, kivéve a következő set: `$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`.

## <a name="jobs-to-execute-direct-methods"></a>Közvetlen módszerek végrehajtandó feladatok
Az alábbi kódrészletben láthatja a HTTPS 1.1 kérelemrészletek hajthatók végre olyan [közvetlen módszer] [ lnk-dev-methods] meg az eszközök feladat használatával:

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

A lekérdezés feltétel lehet egyetlen Eszközazonosítót vagy eszköz a következő példákban azonosítók listáját is:

```
queryCondition = "deviceId = 'MyDevice1'"
queryCondition = "deviceId IN ['MyDevice1','MyDevice2']"
queryCondition = "deviceId IN ['MyDevice1']
```
[Az IoT Hub lekérdezési nyelv] [ lnk-query] IoT-központ lekérdezési nyelv további részletesen ismerteti.

## <a name="jobs-to-update-device-twin-properties"></a>Feladatok eszköz iker tulajdonságainak módosítása
A következő kódrészletet a frissítési feladat használatával kettős eszköztulajdonságok HTTPS 1.1 kérelem részleteit jeleníti meg:

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

## <a name="querying-for-progress-on-jobs"></a>Folyamatban van a feladatok lekérdezése
Az alábbi kódrészletben láthatja a HTTPS 1.1 kérelem részletes [feladatok lekérdezése][lnk-query]:

    GET /jobs/v2/query?api-version=2016-11-14[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

A válaszban szereplő a continuationToken valósul meg.  

## <a name="jobs-properties"></a>Feladatok tulajdonságai
Az alábbi lista a tulajdonságait és a használható lekérdezésekor feladat vagy a feladat eredményeinek megfelelő leírásait tartalmazza.

| Tulajdonság | Leírás |
| --- | --- |
| **jobId** |Alkalmazás azonosítója megadva a feladathoz. |
| **startTime** |Alkalmazás által biztosított a feladat kezdési időpontja (ISO 8601). |
| **endTime** |Az IoT-központ dátuma (ISO 8601) Ha a feladat befejeződött-e megadva. Csak azt követően a feladat eléri a "kész" állapot érvényes. |
| **Típusa** |Feladatok típusai: |
| | **scheduledUpdateTwin**: egy kívánt tulajdonságokkal vagy címkék frissítésére szolgáló feladatot. |
| | **scheduledDeviceMethod**: egy eszköz twins a megfelelő eszköz metódus hívásához használt feladat. |
| **status** |A feladat jelenlegi állapota. Az állapotot a lehetséges értékek: |
| | **függőben lévő**: ütemezett és váró észlelnie kell a feladat szolgáltatás. |
| | **ütemezett**: jövőbeli időpontra ütemezve. |
| | **futó**: jelenleg aktív feladat. |
| | **visszavont**: feladat meg lett szakítva. |
| | **nem sikerült**: feladat sikertelen volt. |
| | **befejeződött**: feladat befejeződött. |
| **deviceJobStatistics** |A feladat végrehajtása statisztikája. |
| | **deviceJobStatistics** tulajdonságok: |
| | **deviceJobStatistics.deviceCount**: a feladat eszközök számát. |
| | **deviceJobStatistics.failedCount**: Ha a feladat nem sikerült az eszközök száma. |
| | **deviceJobStatistics.succeededCount**: Ha a feladat sikeresen befejeződött eszközök számát. |
| | **deviceJobStatistics.runningCount**: a feladat éppen futó eszközök számát. |
| | **deviceJobStatistics.pendingCount**: a feladat futtatásához függőben lévő eszközök száma. |

### <a name="additional-reference-material"></a>További referenciaanyag
Az IoT Hub fejlesztői útmutató más hivatkozás témaköröket tartalmazza:

* [IoT-központok végpontjai] [ lnk-endpoints] ismerteti a különböző végpontok, amelyek minden egyes IoT-központ elérhetővé teszi a futásidejű és felügyeleti műveletek.
* [Sávszélesség-szabályozási és kvóták] [ lnk-quotas] ismerteti a kvótákat, az IoT-központ szolgáltatás és a sávszélesség-szabályozási viselkedését történik, ha a szolgáltatás használatához.
* [Az Azure IoT eszköz és a szolgáltatás SDK-k] [ lnk-sdks] felsorolja a különböző nyelvi használhatja az eszköz és a szolgáltatás alkalmazások gondoskodnak az IoT hubbal fejlesztésekor SDK-k.
* [Az IoT-központ lekérdezési nyelv eszköz twins, feladatok és üzenet útválasztási] [ lnk-query] az IoT-központ lekérdezési nyelv ismerteti. A lekérdezési nyelv használatával IoT Hub eszköz twins és feladatok kapcsolatos adatok lekérését.
* [Az IoT Hub MQTT támogatási] [ lnk-devguide-mqtt] IoT-központ támogatásával kapcsolatos további információkat biztosít a MQTT protokoll.

## <a name="next-steps"></a>További lépések
Próbálja ki azokat a jelen cikkben ismertetett fogalmakat, olvassa el a következő IoT Hub-oktatóanyag:

* [Ütemezés és a feladatok][lnk-jobs-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-jobs-tutorial]: iot-hub-node-node-schedule-jobs.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-devguide]: iot-hub-devguide-device-twins.md
