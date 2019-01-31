---
title: Az Azure Batch-feladat indítása esemény |} A Microsoft Docs
description: Referencia a Batch-feladat indítása esemény.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: d50a0a7082e409084fd966370934a638ca9bb013
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55474428"
---
# <a name="task-start-event"></a>Tevékenység indítása esemény

 Ez az esemény után egy számítási csomóponton induljon el van ütemezve egy feladat az ütemező által kibocsátott. Vegye figyelembe, hogy ha a feladat várólistára vagy újból ezt az eseményt fog bocsátja ki újra ugyanazt a feladatot, de az újrapróbálkozások számát és a rendszer a feladat verziója ennek megfelelően frissül.


 Az alábbi példa bemutatja, hogy a szervezet egy tevékenység indítása esemény.

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

|Elem neve|Typo|Megjegyzések|
|------------------|----------|-----------|
|jobId|String|A feladat a tevékenységet tartalmazó azonosítója.|
|id|String|A feladat azonosítója.|
|taskType|String|A feladat típusát. Ez is jelezve, hogy ez a feladatkezelői tevékenység JobManager, vagy már nem a feladatkezelői tevékenység jelző "felhasználó".|
|systemTaskVersion|Int32|Ez a tevékenység a belső újrapróbálkozási számláló. A Batch szolgáltatás belsőleg áthidalhatók az átmeneti hibák feladat újra. Ezek a helyzetek közé tartoznak a belső ütemezési hibák, vagy a helyreállítás kísérletek számítási csomópontok rossz állapotban.|
|[nodeInfo](#nodeInfo)|Komplex típus|A számítási csomóponton, amelyen a feladat futott kapcsolatos információt tartalmazza.|
|[multiInstanceSettings](#multiInstanceSettings)|Komplex típus|Megadja, hogy a feladatot több számítási csomópontokat igényelnek a többpéldányos tevékenység.  Lásd: [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) részleteiről.|
|[Korlátozások](#constraints)|Komplex típus|A végrehajtási korlátozások, amelyek a alkalmazni ezt a feladatot.|
|[executionInfo](#executionInfo)|Komplex típus|A feladat végrehajtásának kapcsolatos információt tartalmazza.|

###  <a name="nodeInfo"></a> nodeInfo

|Elem neve|Typo|Megjegyzések|
|------------------|----------|-----------|
|poolId|String|A annak a készletnek azonosítója, amelyen a tevékenység futott.|
|nodeId|String|A csomópont, amelyen a feladat futott azonosítója.|

###  <a name="multiInstanceSettings"></a> multiInstanceSettings

|Elem neve|Typo|Megjegyzések|
|------------------|----------|-----------|
|numberOfInstances|Int|A tevékenységhez szükséges számítási csomópontok száma.|

###  <a name="constraints"></a> Korlátozások

|Elem neve|Typo|Megjegyzések|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|A maximális száma a próbálhassa. A Batch szolgáltatás feladat újrapróbálkozik, ha annak kilépési kódja nullától eltérő.<br /><br /> Vegye figyelembe, hogy ez az érték kifejezetten határozza meg az újrapróbálkozások számát. A Batch szolgáltatás egyszer megkísérli a feladat, és előfordulhat, hogy próbálkozzon újra legfeljebb ezt a korlátot. Például ha az újrapróbálkozások maximális száma 3, köteg próbálkozás egy feladat legfeljebb 4 alkalommal (kezdeti próbálkozás és 3 újrapróbálás).<br /><br /> Ha az újrapróbálkozások maximális száma 0, a Batch szolgáltatás nem próbálkozik újra a feladatot.<br /><br /> Ha az újrapróbálkozások maximális száma -1, akkor a Batch szolgáltatás korlátozás nélkül feladatok újrapróbálkozik.<br /><br /> Az alapértelmezett érték: 0 (nincs újrapróbálkozás).|

###  <a name="executionInfo"></a> executionInfo

|Elem neve|Typo|Megjegyzések|
|------------------|----------|-----------|
|RetryCount|Int32|A száma a feladat rendelkezik lett rendszer megpróbálja újból végrehajtani a Batch szolgáltatás által. Rendszer megpróbálja újból végrehajtani a feladatot, ha kilép, egy nullától eltérő kilépési kóddal, legfeljebb a megadott MaxTaskRetryCount|
