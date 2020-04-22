---
title: Az Azure IoT Hub-feladatok megismerése | Microsoft dokumentumok
description: Fejlesztői útmutató – ütemezési feladatok futtatásához több eszközön csatlakozik az IoT hub. A feladatok frissíthetik a címkéket és a kívánt tulajdonságokat, és közvetlen metódusokat hívhatnak meg több eszközön.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2019
ms.custom: mqtt
ms.openlocfilehash: 5c14e8cfcbf8df86b0f71d6b12025594d2e648c4
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730108"
---
# <a name="schedule-jobs-on-multiple-devices"></a>Feladatok ütemezése több eszközön

Az Azure IoT Hub számos építőelem, például [az ikereszköz-tulajdonságokat, a címkéket és](iot-hub-devguide-device-twins.md) [a közvetlen metódusokat](iot-hub-devguide-direct-methods.md)teszi lehetővé. A háttéralkalmazások általában lehetővé teszik az eszközadminisztrátorok és -üzemeltetők számára az IoT-eszközök tömeges és ütemezett frissítését és azokkal való interakciót. A feladatok eszköziker-frissítéseket és közvetlen metódusokat hajtanak végre egy eszközkészleten ütemezett időpontban. Például egy operátor egy háttéralkalmazás, amely kezdeményezi és nyomon követi a feladatot, hogy indítsa újra az eszközök egy sor eszközt az épület 43 és a 3.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Fontolja meg a feladatok használatát, amikor az alábbi tevékenységek bármelyikét ütemezi és nyomon követi egy eszközcsoporton:

* Eszköz kívánt tulajdonságainak frissítése
* Címkék frissítése
* Közvetlen metódusok meghívása

## <a name="job-lifecycle"></a>Feladat életciklusa

A feladatokat a megoldás háttér-tartalék vége kezdeményezi, és az IoT Hub karbantartja. A feladatot szolgáltatásfelé néző URI (`PUT https://<iot hub>/jobs/v2/<jobID>?api-version=2018-06-30`) keresztül kezdeményezheti, és a szolgáltatás felé`GET https://<iot hub>/jobs/v2/<jobID?api-version=2018-06-30`néző URI ( keresztül lekérdezheti a végrehajtó feladat előrehaladását. A feladatok futtatása a feladat indítása után a futó feladatok állapotának frissítéséhez futtasson egy feladatlekérdezést.

> [!NOTE]
> Feladat kezdeményezésekénél a tulajdonságnevek és értékek csak US-ASCII nyomtatható alfanumerikus értékeket tartalmazhatnak, kivéve a következő készletben szereplő értékeket:`$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`

## <a name="jobs-to-execute-direct-methods"></a>Közvetlen metódusok végrehajtásának feladatai

A következő kódrészlet a HTTPS 1.1 kérelem részleteit mutatja be a [közvetlen metódus végrehajtásához](iot-hub-devguide-direct-methods.md) egy feladatot használó eszközökön:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8

{
    "jobId": "<jobId>",
    "type": "scheduleDeviceMethod",
    "cloudToDeviceMethod": {
        "methodName": "<methodName>",
        "payload": <payload>,
        "responseTimeoutInSeconds": methodTimeoutInSeconds
    },
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

A lekérdezési feltétel lehet egyetlen eszközazonosítón vagy az eszközazonosítók listáján is, ahogy az a következő példákban látható:

```
"queryCondition" = "deviceId = 'MyDevice1'"
"queryCondition" = "deviceId IN ['MyDevice1','MyDevice2']"
"queryCondition" = "deviceId IN ['MyDevice1']"
```

[Az IoT Hub lekérdezési nyelv](iot-hub-devguide-query-language.md) e további részletekben ismerteti az IoT Hub lekérdezési nyelvét.

A következő kódrészlet a contoso-hub-1 összes eszközén egy testMethod nevű közvetlen metódus hívására ütemezett feladat kérését és válaszát jeleníti meg:

```
PUT https://contoso-hub-1.azure-devices.net/jobs/v2/job01?api-version=2018-06-30 HTTP/1.1
Authorization: SharedAccessSignature sr=contoso-hub-1.azure-devices.net&sig=68iv------------------------------------v8Hxalg%3D&se=1556849884&skn=iothubowner
Content-Type: application/json; charset=utf-8
Host: contoso-hub-1.azure-devices.net
Content-Length: 317

{
    "jobId": "job01",
    "type": "scheduleDeviceMethod",
    "cloudToDeviceMethod": {
        "methodName": "testMethod",
        "payload": {},
        "responseTimeoutInSeconds": 30
    },
    "queryCondition": "*", 
    "startTime": "2019-05-04T15:53:00.077Z",
    "maxExecutionTimeInSeconds": 20
}

HTTP/1.1 200 OK
Content-Length: 65
Content-Type: application/json; charset=utf-8
Vary: Origin
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 03 May 2019 01:46:18 GMT

{"jobId":"job01","type":"scheduleDeviceMethod","status":"queued"}
```

## <a name="jobs-to-update-device-twin-properties"></a>Az ikereszköz tulajdonságainak frissítéséhez

A következő kódrészlet a HTTPS 1.1 kérelem részleteit mutatja az ikereszköz tulajdonságainak feladat használatával történő frissítéséhez:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8

{
    "jobId": "<jobId>",
    "type": "scheduleUpdateTwin",
    "updateTwin": <patch>                 // Valid JSON object
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

> [!NOTE]
> Az *updateTwin* tulajdonsághoz érvényes etag-egyezés szükséges; például `etag="*"`.

A következő kódrészlet a contoso-hub-1 teszteszköz ikereszköz-tulajdonságainak frissítésére ütemezett feladatra vonatkozó kérést és választ mutatja be:

```
PUT https://contoso-hub-1.azure-devices.net/jobs/v2/job02?api-version=2018-06-30 HTTP/1.1
Authorization: SharedAccessSignature sr=contoso-hub-1.azure-devices.net&sig=BN0U-------------------------------------RuA%3D&se=1556925787&skn=iothubowner
Content-Type: application/json; charset=utf-8
Host: contoso-hub-1.azure-devices.net
Content-Length: 339

{
    "jobId": "job02",
    "type": "scheduleUpdateTwin",
    "updateTwin": {
      "properties": {
        "desired": {
          "test1": "value1"
        }
      },
     "etag": "*"
     },
    "queryCondition": "deviceId = 'test-device'",
    "startTime": "2019-05-08T12:19:56.868Z",
    "maxExecutionTimeInSeconds": 20
}

HTTP/1.1 200 OK
Content-Length: 63
Content-Type: application/json; charset=utf-8
Vary: Origin
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 03 May 2019 22:45:13 GMT

{"jobId":"job02","type":"scheduleUpdateTwin","status":"queued"}
```

## <a name="querying-for-progress-on-jobs"></a>Feladatok előrehaladásának lekérdezése

A következő kódrészlet a HTTPS 1.1 kérelem részleteit jeleníti meg a feladatok lekérdezéséhez:

```
GET /jobs/v2/query?api-version=2018-06-30[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
```

A continuationToken a válaszból származik.

A feladat-végrehajtási állapot lekérdezése az egyes eszközöken az [IOt Hub lekérdezési nyelv az eszköz twins, feladatok és üzenet-útválasztás.](iot-hub-devguide-query-language.md)

## <a name="jobs-properties"></a>Feladatok tulajdonságai

Az alábbi lista a tulajdonságokat és a megfelelő leírásokat tartalmazza, amelyek a feladatok vagy a feladateredmények lekérdezésekor használhatók.

| Tulajdonság | Leírás |
| --- | --- |
| **jobId** |Az alkalmazás azonosítót adott meg a feladathoz. |
| **startTime** |Alkalmazás biztosított kezdési időpont (ISO-8601) a feladathoz. |
| **endTime** |Az IoT Hub megadott dátum (ISO-8601) a feladat befejezésének időpontjára. Csak akkor érvényes, ha a feladat eléri a "befejezett" állapotot. |
| **Típus** |Típusú feladatok: |
| | **scheduleUpdateTwin**: A kívánt tulajdonságok vagy címkék készletének frissítésére használt feladat. |
| | **scheduleDeviceMethod**: Eszközmetódus meghívására használt feladat az ikereszközök készletén. |
| **Állapot** |A feladat jelenlegi állapota. Az állapot lehetséges értékei: |
| | **függőben**: Ütemezett és arra vár, hogy a feladatszolgáltatás felvegye. |
| | **ütemezett**: A jövőben egy időre ütemezve. |
| | **futó**: Jelenleg aktív feladat. |
| | **megszakítva:** A feladat megszakítva. |
| | **nem sikerült:** A feladat nem sikerült. |
| | **befejeződött**: A feladat befejeződött. |
| **deviceJobStatistics (Munkastatisztika)** |Statisztika a feladat végrehajtásáról. |
| | **deviceJobStatistics** tulajdonságai: |
| | **deviceJobStatistics.deviceCount**: A feladatban lévő eszközök száma. |
| | **deviceJobStatistics.failedCount**: Azon eszközök száma, amelyeken a feladat nem sikerült. |
| | **deviceJobStatistics.succeededCount**: Azon eszközök száma, amelyeken a feladat sikeres volt. |
| | **deviceJobStatistics.runningCount**: A feladatot jelenleg futtató eszközök száma. |
| | **deviceJobStatistics.pendingCount**: A feladat futtatásához függőben lévő eszközök száma. |

### <a name="additional-reference-material"></a>További referenciaanyagok

Az IoT Hub fejlesztői útmutatójának további referenciatémakörei a következők:

* [Az IoT Hub-végpontok](iot-hub-devguide-endpoints.md) ismerteti a különböző végpontok, amelyek az egyes IoT-központok elérhetővé teszi a futásidejű és felügyeleti műveletek.

* [Szabályozás és kvóták](iot-hub-devguide-quotas-throttling.md) ismerteti a kvótákat, amelyek az IoT Hub-szolgáltatás ra és a szabályozás viselkedése várható a szolgáltatás használatakor.

* [Az Azure IoT-eszközök és szolgáltatások SDK-k](iot-hub-devguide-sdks.md) felsorolja a különböző nyelvi SDK-k segítségével, ha az IoT Hub-szolgáltatást használó eszköz- és szolgáltatásalkalmazásokat is fejleszt.

* [Az IoT Hub lekérdezési nyelve az eszközök twins, feladatok és üzenet-útválasztás](iot-hub-devguide-query-language.md) ismerteti az IoT Hub lekérdezési nyelv. Ezzel a lekérdezési nyelvvel információkat kérhet le az IoT Hubról az eszköztwins és a feladatok használatával.

* [Az IoT Hub MQTT-támogatása](iot-hub-mqtt-support.md) további információt nyújt az MQTT protokoll IoT Hub-támogatásáról.

## <a name="next-steps"></a>További lépések

A cikkben ismertetett fogalmak némelyikének kipróbálásához tekintse meg az IoT Hub következő oktatóanyagát:

* [Feladatok ütemezése és szórása](iot-hub-node-node-schedule-jobs.md)