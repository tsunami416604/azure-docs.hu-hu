---
title: "Azure Batch feladat kezdési esemény |} Microsoft Docs"
description: "Útmutató a kötegelt feladat esemény indítása."
services: batch
author: tamram
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: tamram
ms.openlocfilehash: c47ab36c99dddd46a14c15018a2a46bf7f873ffa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="task-start-event"></a>A feladat kezdési esemény

 Ez az esemény után a feladat ütemezés szerint indítsa el a számítási csomóponton az ütemező által kibocsátott. Vegye figyelembe, hogy ha a feladatot a rendszer újra, vagy helyezte ezt az eseményt fog kell kibocsátott újra ugyanezt a feladatot, de az újrapróbálkozások maximális számát és a rendszer a feladat verziója ennek megfelelően frissül.


 A következő példa bemutatja, hogy a feladat törzse esemény indítása.

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

|Elem neve|Típus|Megjegyzések|
|------------------|----------|-----------|
|A JobId értékének|Karakterlánc|A feladat a tevékenységet tartalmazó azonosítója.|
|id|Karakterlánc|A feladat azonosítóját.|
|taskType|Karakterlánc|A tevékenység típusa. Ez is, miszerint manager feladata JobManager kell vagy a "User" jelezve, hogy ez nem egy kezelő feladat.|
|systemTaskVersion|Int32|Ez egy, a belső újrapróbálkozási számláló meg olyan feladatra. A Batch szolgáltatás belső újra egy feladatot, hogy figyelembe vegye az átmeneti problémákat. A hibák például belső ütemezési hibák, vagy megpróbálja helyreállítani a számítási csomópontok hibás állapotban.|
|[nodeInfo](#nodeInfo)|Összetett típus|A számítási csomópont, amelyen a feladat futott adatait tartalmazza.|
|[multiInstanceSettings](#multiInstanceSettings)|Összetett típus|Megadja, hogy a feladat több számítási csomópont igénylő többpéldányos feladat.  Lásd: [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) részleteiről.|
|[megkötések](#constraints)|Összetett típus|Ez a feladat vonatkozó végrehajtási korlátozások.|
|[executionInfo](#executionInfo)|Összetett típus|A feladat végrehajtásának adatait tartalmazza.|

###  <a name="nodeInfo"></a>nodeInfo

|Elem neve|Típus|Megjegyzések|
|------------------|----------|-----------|
|poolId|Karakterlánc|A készlet, amelyen a feladat futott azonosítója.|
|nodeId|Karakterlánc|A csomópont, amelyen a feladat futott azonosítója.|

###  <a name="multiInstanceSettings"></a>multiInstanceSettings

|Elem neve|Típus|Megjegyzések|
|------------------|----------|-----------|
|numberOfInstances|int|A tevékenységhez szükséges számítási csomópontok száma.|

###  <a name="constraints"></a>megkötések

|Elem neve|Típus|Megjegyzések|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|A maximális száma a próbálhassa. A Batch szolgáltatás feladat újrapróbálkozik, ha annak kilépési kódja nem nulla.<br /><br /> Vegye figyelembe, hogy ez az érték kifejezetten határozza meg az újbóli próbálkozások számát. A Batch szolgáltatás egyszer megpróbál-e a feladatot, és előfordulhat, hogy ismételje meg ezt a határt legfeljebb. Például ha az újrapróbálkozások maximális száma 3, a feladat kötegelt záma legfeljebb 4 alkalommal (egy kezdeti próbálja és 3 újrapróbálás).<br /><br /> Ha az újrapróbálkozások maximális száma 0, a Batch szolgáltatás nem próbálja meg újra feladatok.<br /><br /> Ha az újrapróbálkozások maximális száma -1, a Batch szolgáltatás korlátozás nélkül feladatok után ismét.<br /><br /> Az alapértelmezett érték: 0 (nincs újrapróbálás).|

###  <a name="executionInfo"></a>executionInfo

|Elem neve|Típus|Megjegyzések|
|------------------|----------|-----------|
|a retryCount|Int32|A száma a feladat próbálkozásainak már elérte a Batch szolgáltatás. Rendszer megpróbálja újból végrehajtani a feladatot, ha egy nem nulla kilépési kód, mely legfeljebb a megadott MaxTaskRetryCount kilép|
