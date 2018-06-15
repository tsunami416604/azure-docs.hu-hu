---
title: Azure Batch feladat kezdési esemény |} Microsoft Docs
description: Útmutató a kötegelt feladat esemény indítása.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: danlep
ms.openlocfilehash: 0ad0f87df9db39088769579d538b919b42634c4b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
ms.locfileid: "30311855"
---
# <a name="task-start-event"></a>Tevékenység indítása esemény

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
|jobId|Karakterlánc|A feladat a tevékenységet tartalmazó azonosítója.|
|id|Karakterlánc|A feladat azonosítóját.|
|taskType|Karakterlánc|A tevékenység típusa. Ez is, miszerint manager feladata JobManager kell vagy a "User" jelezve, hogy ez nem egy kezelő feladat.|
|systemTaskVersion|Int32|Ez egy, a belső újrapróbálkozási számláló meg olyan feladatra. A Batch szolgáltatás belső újra egy feladatot, hogy figyelembe vegye az átmeneti problémákat. A hibák például belső ütemezési hibák, vagy megpróbálja helyreállítani a számítási csomópontok hibás állapotban.|
|[nodeInfo](#nodeInfo)|Összetett típus|A számítási csomópont, amelyen a feladat futott adatait tartalmazza.|
|[multiInstanceSettings](#multiInstanceSettings)|Összetett típus|Megadja, hogy a feladat több számítási csomópont igénylő többpéldányos feladat.  Lásd: [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) részleteiről.|
|[Megkötések](#constraints)|Összetett típus|Ez a feladat vonatkozó végrehajtási korlátozások.|
|[executionInfo](#executionInfo)|Összetett típus|A feladat végrehajtásának adatait tartalmazza.|

###  <a name="nodeInfo"></a> nodeInfo

|Elem neve|Típus|Megjegyzések|
|------------------|----------|-----------|
|poolId|Karakterlánc|A készlet, amelyen a feladat futott azonosítója.|
|nodeId|Karakterlánc|A csomópont, amelyen a feladat futott azonosítója.|

###  <a name="multiInstanceSettings"></a> multiInstanceSettings

|Elem neve|Típus|Megjegyzések|
|------------------|----------|-----------|
|numberOfInstances|Int|A tevékenységhez szükséges számítási csomópontok száma.|

###  <a name="constraints"></a> Megkötések

|Elem neve|Típus|Megjegyzések|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|A maximális száma a próbálhassa. A Batch szolgáltatás feladat újrapróbálkozik, ha annak kilépési kódja nem nulla.<br /><br /> Vegye figyelembe, hogy ez az érték kifejezetten határozza meg az újbóli próbálkozások számát. A Batch szolgáltatás egyszer megpróbál-e a feladatot, és előfordulhat, hogy ismételje meg ezt a határt legfeljebb. Például ha az újrapróbálkozások maximális száma 3, a feladat kötegelt záma legfeljebb 4 alkalommal (egy kezdeti próbálja és 3 újrapróbálás).<br /><br /> Ha az újrapróbálkozások maximális száma 0, a Batch szolgáltatás nem próbálja meg újra feladatok.<br /><br /> Ha az újrapróbálkozások maximális száma -1, a Batch szolgáltatás korlátozás nélkül feladatok után ismét.<br /><br /> Az alapértelmezett érték: 0 (nincs újrapróbálás).|

###  <a name="executionInfo"></a> executionInfo

|Elem neve|Típus|Megjegyzések|
|------------------|----------|-----------|
|retryCount|Int32|A száma a feladat próbálkozásainak már elérte a Batch szolgáltatás. Rendszer megpróbálja újból végrehajtani a feladatot, ha egy nem nulla kilépési kód, mely legfeljebb a megadott MaxTaskRetryCount kilép|
