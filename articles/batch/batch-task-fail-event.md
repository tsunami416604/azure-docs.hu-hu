---
title: Azure Batch feladat meghiúsulása esemény | Microsoft Docs
description: A Batch feladat sikertelen eseményének hivatkozása.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/15/2019
ms.author: lahugh
ms.openlocfilehash: aa56b0ead8edc17efe74547f6374a3f8888970b5
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624193"
---
# <a name="task-fail-event"></a>Tevékenység meghiúsult esemény

 Ezt az eseményt akkor bocsátja ki a rendszer, ha egy feladat hibát jelez. A nullától eltérő kilépési kódok jelenleg nem minősülnek hibáknak. Ez az esemény a feladat teljes eseményén *kívül* lesz kibocsátva, és felhasználható arra, hogy észlelje, ha egy feladat meghiúsult.


 A következő példa egy feladat sikertelen eseményének törzsét mutatja be.

```
{
    "jobId": "job-0000000001",
    "id": "task-5",
    "taskType": "User",
    "systemTaskVersion": 0,
    "nodeInfo": {
        "poolId": "pool-001",
        "nodeId": "tvm-257509324_1-20160908t162728z"
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 2
    },
    "executionInfo": {
        "startTime": "2016-09-08T16:32:23.799Z",
        "endTime": "2016-09-08T16:34:00.666Z",
        "exitCode": 1,
        "retryCount": 2,
        "requeueCount": 0
    }
}
```

|Elem neve|Type|Megjegyzések|
|------------------|----------|-----------|
|jobId|Sztring|A feladatot tartalmazó feladat azonosítója.|
|id|Sztring|A feladat azonosítója.|
|taskType|Sztring|A feladat típusa. Ez lehet "JobManager", amely azt jelzi, hogy egy Feladatkezelő feladat vagy "felhasználó", amely azt jelzi, hogy a feladat nem Feladatkezelő feladat. Ez az esemény nem lett kibocsátva a feladat-előkészítési feladatokhoz, a feladat-felszabadítási feladatokhoz és a tevékenységek indításához|
|systemTaskVersion|Int32|Ez a feladat belső újrapróbálkozási számlálója. A Batch szolgáltatás belsőleg újra tud próbálkozni az átmeneti problémák miatti feladatokkal. Ezek a problémák belső ütemezési hibákat tartalmazhatnak, vagy helytelen állapotú számítási csomópontokból történő helyreállításra tett kísérleteket okozhatnak.|
|[nodeInfo](#nodeInfo)|Összetett típus|A feladat futtatására szolgáló számítási csomóponttal kapcsolatos információkat tartalmazza.|
|[multiInstanceSettings](#multiInstanceSettings)|Összetett típus|Megadja, hogy a feladat több számítási csomópontot igénylő többpéldányos feladat.  További részletekért lásd: [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) .|
|[korlátok](#constraints)|Összetett típus|A feladatra érvényes végrehajtási megkötések.|
|[executionInfo](#executionInfo)|Összetett típus|A feladat végrehajtásával kapcsolatos információkat tartalmaz.|

###  <a name="nodeInfo"></a>nodeInfo

|Elem neve|Type|Megjegyzések|
|------------------|----------|-----------|
|poolId|Sztring|Annak a készletnek az azonosítója, amelyen a feladat futott.|
|nodeId|Sztring|Annak a csomópontnak az azonosítója, amelyen a feladat futott.|

###  <a name="multiInstanceSettings"></a>multiInstanceSettings

|Elem neve|Type|Megjegyzések|
|------------------|----------|-----------|
|numberOfInstances|Int32|A feladat által igényelt számítási csomópontok száma.|

###  <a name="constraints"></a>korlátok

|Elem neve|Type|Megjegyzések|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|A feladat újrapróbálkozásának maximális száma. A Batch szolgáltatás újrapróbálkozik a feladattal, ha a kilépési kód nem nulla.<br /><br /> Vegye figyelembe, hogy ez az érték kifejezetten meghatározza az újrapróbálkozások számát. A Batch szolgáltatás egyszer próbálkozik a feladattal, és ezt követően újra próbálkozik a korláttal. Ha például az újrapróbálkozások maximális száma 3, a Batch 4 alkalommal próbálkozik a feladattal (egy kezdeti próbálkozás és 3 újrapróbálkozás).<br /><br /> Ha a maximális újrapróbálkozások száma 0, a Batch szolgáltatás nem próbálkozik újra a tevékenységekkel.<br /><br /> Ha a maximális újrapróbálkozások száma-1, a Batch szolgáltatás korlátozás nélkül újrapróbálkozik a feladatokkal.<br /><br /> Az alapértelmezett érték: 0 (nincs újrapróbálkozás).|


###  <a name="executionInfo"></a>executionInfo

|Elem neve|Type|Megjegyzések|
|------------------|----------|-----------|
|startTime|DateTime|Az az idő, amikor a feladat futása megkezdődött. A "Running" érték a **futó** állapotnak felel meg, így ha a feladat erőforrás-fájlokat vagy alkalmazáscsomagokat határoz meg, akkor a kezdési időpont azt az időpontot jelzi, amikor a feladat megkezdte a letöltését vagy üzembe helyezését.  Ha a feladat újraindult vagy újrapróbálkozott, ez a legutóbbi időpont, amikor a feladat elindult.|
|endTime|DateTime|Az az idő, amikor a feladat befejeződött.|
|exitCode|Int32|A feladat kilépési kódja.|
|retryCount|Int32|A Batch szolgáltatás által újrapróbált feladatok száma. A feladat újra próbálkozik, ha a nullától eltérő kilépési kóddal kilép a megadott MaxTaskRetryCount.|
|requeueCount|Int32|A Batch szolgáltatás által a felhasználói kérelem eredményeképpen újravárólistázott feladatok száma.<br /><br /> Ha a felhasználó eltávolít egy készletből származó csomópontokat (a készlet átméretezésével vagy kicsinyítésével), vagy ha a feladat le van tiltva, a felhasználó megadhatja, hogy a csomópontokon futó feladatokat a rendszer újravárólistára helyezi-e a végrehajtáshoz. Ez a szám azt követi nyomon, hogy a feladat hányszor lett újravárólistázott ezen okok miatt.|
