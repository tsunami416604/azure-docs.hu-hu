---
title: Azure Batch feladat indítási esemény
description: A Batch feladatindítási esemény hivatkozási információi. Ezt az eseményt akkor bocsátja ki a program, ha az ütemező egy számítási csomóponton való kezdésre van ütemezve.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: bed3749e29867298f3e8258a08448b7b094055ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022817"
---
# <a name="task-start-event"></a>Tevékenység indítása esemény

 Ezt az eseményt akkor bocsátja ki a program, ha az ütemező egy számítási csomóponton való kezdésre van ütemezve. Vegye figyelembe, hogy ha a feladatot újra megkísérli vagy újra várólistára helyezi, az esemény újra megjelenik ugyanahhoz a feladathoz, de az újrapróbálkozások száma és a rendszerfeladat verziója ennek megfelelően frissül.


 A következő példa egy feladatindítási esemény törzsét mutatja be.

```
{
    "jobId": "myJob",
    "id": "myTask",
    "taskType": "User",
    "systemTaskVersion": 220192842,
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

|Elem neve|Típus|Megjegyzések|
|------------------|----------|-----------|
|`jobId`|Sztring|A feladatot tartalmazó feladat azonosítója.|
|`id`|Sztring|A feladat azonosítója.|
|`taskType`|Sztring|A feladat típusa. Ez lehet "JobManager", amely azt jelzi, hogy feladatkezelő feladat, vagy "Felhasználó", amely azt jelzi, hogy nem feladatkezelő feladat.|
|`systemTaskVersion`|Int32|Ez a feladat belső újrapróbálkozási számlálója. A Batch szolgáltatás belsőleg újra próbálkozhat egy feladattal, hogy figyelembe vegye az átmeneti problémákat. Ezek a problémák lehetnek belső ütemezési hibák, vagy megpróbálja helyreállítani a számítási csomópontok rossz állapotban.|
|[`nodeInfo`](#nodeInfo)|Összetett típus|Információt tartalmaz arról a számítási csomópontról, amelyen a feladat futott.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Összetett típus|Itt adhatja meg, hogy a feladat többpéldányos feladat, amely több számítási csomópontot igényel.  A részletekért tekintse meg a [multiInstanceSettings-t.](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task)|
|[`constraints`](#constraints)|Összetett típus|A feladatra vonatkozó végrehajtási korlátok.|
|[`executionInfo`](#executionInfo)|Összetett típus|A feladat végrehajtásával kapcsolatos információkat tartalmazza.|

###  <a name="nodeinfo"></a><a name="nodeInfo"></a>nodeInfo

|Elem neve|Típus|Megjegyzések|
|------------------|----------|-----------|
|`poolId`|Sztring|Annak a készletnek az azonosítója, amelyen a feladat futott.|
|`nodeId`|Sztring|Annak a csomópontnak az azonosítója, amelyen a feladat futott.|

###  <a name="multiinstancesettings"></a><a name="multiInstanceSettings"></a>multiInstanceSettings

|Elem neve|Típus|Megjegyzések|
|------------------|----------|-----------|
|`numberOfInstances`|Int|A feladat által igényelt számítási csomópontok száma.|

###  <a name="constraints"></a><a name="constraints"></a>Korlátok

|Elem neve|Típus|Megjegyzések|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|A feladat újrakísérelhető maximális száma. A Batch szolgáltatás újrapróbálkozik egy feladattal, ha a kilépési kódja nem nulla.<br /><br /> Vegye figyelembe, hogy ez az érték kifejezetten szabályozza az újrapróbálkozások számát. A Batch szolgáltatás egyszer megpróbálja a feladatot, majd újra próbálkozhat ezzel a korláttal. Ha például a maximális újrapróbálkozások száma 3, a Batch legfeljebb 4 alkalommal próbálkozik egy feladattal (egy kezdeti próbálkozás és 3 újrapróbálkozás).<br /><br /> Ha a maximális újrapróbálkozások száma 0, a Batch szolgáltatás nem próbálja meg újra a feladatokat.<br /><br /> Ha a maximális újrapróbálkozások száma -1, a Batch szolgáltatás korlátozás nélkül újrapróbálkozik a feladatokkal.<br /><br /> Az alapértelmezett érték 0 (nincs újrapróbálkozás).|

###  <a name="executioninfo"></a><a name="executionInfo"></a>végrehajtásInfo

|Elem neve|Típus|Megjegyzések|
|------------------|----------|-----------|
|`retryCount`|Int32|Azon alkalmak száma, ahányszor a Batch szolgáltatás újrapróbálkozott a feladattal. A feladat újra próbálkozik, ha nem nulla kilépési kóddal lép ki a megadott MaxTaskRetryCount értékig.|
