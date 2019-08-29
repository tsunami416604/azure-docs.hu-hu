---
title: Azure Batch feladat indítása esemény | Microsoft Docs
description: A Batch-feladat indítási eseményének hivatkozása.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: efad9e71b986156c6d8e95208d50ac8d5a4d7e7f
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094366"
---
# <a name="task-start-event"></a>Tevékenység indítása esemény

 Ezt az eseményt akkor bocsátja ki a rendszer, ha az ütemező egy számítási csomóponton indítja el a feladatot. Vegye figyelembe, hogy ha a feladat újrapróbálkozása vagy újbóli várólistára kerül, az esemény ugyanarra a feladatra kerül, de az újrapróbálkozások száma és a rendszerfeladat verziója ennek megfelelően frissül.


 A következő példa egy feladat-indítási esemény törzsét mutatja be.

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
        "retryCount": 0
    }
}
```

|Elem neve|Type|Megjegyzések|
|------------------|----------|-----------|
|jobId|Sztring|A feladatot tartalmazó feladat azonosítója.|
|id|Sztring|A feladat azonosítója.|
|taskType|Sztring|A feladat típusa. Ez lehet "JobManager", amely azt jelzi, hogy egy Feladatkezelő feladat vagy "felhasználó", amely azt jelzi, hogy a feladat nem Feladatkezelő feladat.|
|systemTaskVersion|Int32|Ez a feladat belső újrapróbálkozási számlálója. A Batch szolgáltatás belsőleg újra tud próbálkozni az átmeneti problémák miatti feladatokkal. Ezek a problémák belső ütemezési hibákat tartalmazhatnak, vagy helytelen állapotú számítási csomópontokból történő helyreállításra tett kísérleteket okozhatnak.|
|[nodeInfo](#nodeInfo)|Összetett típus|A feladat futtatására szolgáló számítási csomóponttal kapcsolatos információkat tartalmazza.|
|[multiInstanceSettings](#multiInstanceSettings)|Összetett típus|Azt adja meg, hogy a feladat több számítási csomópontot igénylő többpéldányos feladat.  További részletekért lásd: [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) .|
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
|numberOfInstances|Int|A feladat által igényelt számítási csomópontok száma.|

###  <a name="constraints"></a>korlátok

|Elem neve|Type|Megjegyzések|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|A feladat újrapróbálkozásának maximális száma. A Batch szolgáltatás újrapróbálkozik a feladattal, ha a kilépési kód nem nulla.<br /><br /> Vegye figyelembe, hogy ez az érték kifejezetten meghatározza az újrapróbálkozások számát. A Batch szolgáltatás egyszer próbálkozik a feladattal, és ezt követően újra próbálkozik a korláttal. Ha például az újrapróbálkozások maximális száma 3, a Batch 4 alkalommal próbálkozik a feladattal (egy kezdeti próbálkozás és 3 újrapróbálkozás).<br /><br /> Ha a maximális újrapróbálkozások száma 0, a Batch szolgáltatás nem próbálkozik újra a tevékenységekkel.<br /><br /> Ha a maximális újrapróbálkozások száma-1, a Batch szolgáltatás korlátozás nélkül újrapróbálkozik a feladatokkal.<br /><br /> Az alapértelmezett érték: 0 (nincs újrapróbálkozás).|

###  <a name="executionInfo"></a>executionInfo

|Elem neve|Type|Megjegyzések|
|------------------|----------|-----------|
|retryCount|Int32|A Batch szolgáltatás által újrapróbált feladatok száma. A feladat újra próbálkozik, ha a nullától eltérő kilépési kóddal kilép a megadott MaxTaskRetryCount|
