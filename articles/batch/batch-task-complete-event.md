---
title: Az Azure Batch-feladat befejeződött eseménye
description: Hivatkozás a Kötegelt feladat befejeződött eseményéhez. Ez az esemény a feladat befejezése után kerül kibocsátásra, függetlenül a kilépési kódtól.
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
ms.openlocfilehash: 0fee5f071d5c7005e466bf4c3d0c1d0a7db24731
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022919"
---
# <a name="task-complete-event"></a>Tevékenység kész esemény

 Ez az esemény a feladat befejezése után kerül kibocsátásra, függetlenül a kilépési kódtól. Ezzel az eseménnyel meghatározható egy tevékenység időtartama, a tevékenység helye, valamint a megkísérlése.


 A következő példa egy feladat teljes eseményének törzsét mutatja be.

```
{
    "jobId": "myJob",
    "id": "myTask",
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
        "exitCode": 0,
        "retryCount": 0,
        "requeueCount": 0
    }
}
```

|Elem neve|Típus|Megjegyzések|
|------------------|----------|-----------|
|`jobId`|Sztring|A feladatot tartalmazó feladat azonosítója.|
|`id`|Sztring|A feladat azonosítója.|
|`taskType`|Sztring|A feladat típusa. Ez lehet "JobManager", amely azt jelzi, hogy feladatkezelő feladat, vagy "Felhasználó", amely azt jelzi, hogy nem feladatkezelő feladat. Ez az esemény nem kerül kiadásra a feladat-előkészítési feladatokhoz, a feladatkiadási feladatokhoz vagy a feladatok elindításához.|
|`systemTaskVersion`|Int32|Ez a feladat belső újrapróbálkozási számlálója. A Batch szolgáltatás belsőleg újra próbálkozhat egy feladattal, hogy figyelembe vegye az átmeneti problémákat. Ezek a problémák lehetnek belső ütemezési hibák, vagy megpróbálja helyreállítani a számítási csomópontok rossz állapotban.|
|[`nodeInfo`](#nodeInfo)|Összetett típus|Információt tartalmaz arról a számítási csomópontról, amelyen a feladat futott.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Összetett típus|Itt adhatja meg, hogy a feladat többpéldányos feladat, amely több számítási csomópontot igényel.  Lásd [`multiInstanceSettings`](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) a részleteket.|
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
|`numberOfInstances`|Int32|A feladat által igényelt számítási csomópontok száma.|

###  <a name="constraints"></a><a name="constraints"></a>Korlátok

|Elem neve|Típus|Megjegyzések|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|A feladat újrakísérelhető maximális száma. A Batch szolgáltatás újrapróbálkozik egy feladattal, ha a kilépési kódja nem nulla.<br /><br /> Vegye figyelembe, hogy ez az érték kifejezetten szabályozza az újrapróbálkozások számát. A Batch szolgáltatás egyszer megpróbálja a feladatot, majd újra próbálkozhat ezzel a korláttal. Ha például a maximális újrapróbálkozások száma 3, a Batch legfeljebb 4 alkalommal próbálkozik egy feladattal (egy kezdeti próbálkozás és 3 újrapróbálkozás).<br /><br /> Ha a maximális újrapróbálkozások száma 0, a Batch szolgáltatás nem próbálja meg újra a feladatokat.<br /><br /> Ha a maximális újrapróbálkozások száma -1, a Batch szolgáltatás korlátozás nélkül újrapróbálkozik a feladatokkal.<br /><br /> Az alapértelmezett érték 0 (nincs újrapróbálkozás).|

###  <a name="executioninfo"></a><a name="executionInfo"></a>végrehajtásInfo

|Elem neve|Típus|Megjegyzések|
|------------------|----------|-----------|
|`startTime`|DateTime|A feladat futásának időpontja. A "Futás" a **futó** állapotnak felel meg, így ha a feladat erőforrásfájlokat vagy alkalmazáscsomagokat ad meg, akkor a kezdési időpont azt az időpontot tükrözi, amikor a feladat elkezdte letölteni vagy telepíteni ezeket.  Ha a feladatot újraindították vagy újrapróbálták, ez a legutóbbi időpont, amikor a feladat futni kezdett.|
|`endTime`|DateTime|A feladat befejezésének időpontja.|
|`exitCode`|Int32|A feladat kilépési kódja.|
|`retryCount`|Int32|Azon alkalmak száma, ahányszor a Batch szolgáltatás újrapróbálkozott a feladattal. A feladat újra próbálkozik, ha nem nulla kilépési kóddal lép ki a megadott MaxTaskRetryCount értékig.|
|`requeueCount`|Int32|Azon alkalmak száma, amikor a Batch szolgáltatás felhasználói kérelem miatt újra várólistára került a feladatnak.<br /><br /> Amikor a felhasználó eltávolítja a csomópontokat egy készletből (a készlet átméretezésével vagy zsugorításával), vagy amikor a feladat le van tiltva, a felhasználó megadhatja, hogy a csomópontokon futó feladatok at újra várólistára kell tenni a végrehajtáshoz. Ez a számláló nyomon követi, hogy a feladat ezen okok miatt hányszor került újra várólistára.|
