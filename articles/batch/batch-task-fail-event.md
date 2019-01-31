---
title: Az Azure Batch tevékenység meghiúsult esemény |} A Microsoft Docs
description: Referencia a Batch-feladat sikertelen esemény.
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
ms.openlocfilehash: f37769ceb761b8c8bc4834568813bb1b7af7f66a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55458992"
---
# <a name="task-fail-event"></a>Tevékenység meghiúsult esemény

 Ez az esemény bocsásson ki egy hibával egy feladat befejeződésekor. Jelenleg minden nullától eltérő kilépési kódot minősülnek hibák. Ezt az eseményt a rendszer kibocsátott *mellett* feladat végezze el az eseményt, és használható annak észlelése, ha egy feladat sikertelen volt.


 Az alábbi példa bemutatja, hogy a szervezet egy feladat sikertelen esemény.

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

|Elem neve|Typo|Megjegyzések|
|------------------|----------|-----------|
|jobId|String|A feladat a tevékenységet tartalmazó azonosítója.|
|id|String|A feladat azonosítója.|
|taskType|String|A feladat típusát. Ez is jelezve, hogy ez a feladatkezelői tevékenység JobManager, vagy már nem a feladatkezelői tevékenység jelző "felhasználó". Ez az esemény nem kibocsátott, feladat-előkészítési tevékenységeket, a feladatkiadási tevékenységeket vagy az indítási tevékenységeket.|
|systemTaskVersion|Int32|Ez a tevékenység a belső újrapróbálkozási számláló. A Batch szolgáltatás belsőleg áthidalhatók az átmeneti hibák feladat újra. Ezek a helyzetek közé tartoznak a belső ütemezési hibák, vagy a helyreállítás kísérletek számítási csomópontok rossz állapotban.|
|[nodeInfo](#nodeInfo)|Komplex típus|A számítási csomóponton, amelyen a feladat futott kapcsolatos információt tartalmazza.|
|[multiInstanceSettings](#multiInstanceSettings)|Komplex típus|Megadja, hogy a tevékenység egy többpéldányos tevékenység több számítási csomópontokat igényelnek.  Lásd: [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) részleteiről.|
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
|numberOfInstances|Int32|A tevékenységhez szükséges számítási csomópontok száma.|

###  <a name="constraints"></a> Korlátozások

|Elem neve|Typo|Megjegyzések|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|A maximális száma a próbálhassa. A Batch szolgáltatás feladat újrapróbálkozik, ha annak kilépési kódja nullától eltérő.<br /><br /> Vegye figyelembe, hogy ez az érték kifejezetten határozza meg az újrapróbálkozások számát. A Batch szolgáltatás egyszer megkísérli a feladat, és előfordulhat, hogy próbálkozzon újra legfeljebb ezt a korlátot. Például ha az újrapróbálkozások maximális száma 3, köteg próbálkozás egy feladat legfeljebb 4 alkalommal (kezdeti próbálkozás és 3 újrapróbálás).<br /><br /> Ha az újrapróbálkozások maximális száma 0, a Batch szolgáltatás nem próbálkozik újra a feladatot.<br /><br /> Ha az újrapróbálkozások maximális száma -1, akkor a Batch szolgáltatás korlátozás nélkül feladatok újrapróbálkozik.<br /><br /> Az alapértelmezett érték: 0 (nincs újrapróbálkozás).|


###  <a name="executionInfo"></a> executionInfo

|Elem neve|Typo|Megjegyzések|
|------------------|----------|-----------|
|startTime|DateTime|Amikor a feladat elindításának ideje. "Fut" megfelel a **futó** állapotba, így ha a feladat Erőforrásfájlok vagy az alkalmazáscsomagok határozza meg, majd a kezdési időpont tükrözi az idő, amikor a feladat elindult letöltése vagy telepítése ezeket.  Ha a feladat újraindítása, vagy a rendszer megpróbálja újból végrehajtani, akkor a legutóbbi idő, amikor a feladat elindításának.|
|endTime|DateTime|Az az időpont, amikor a tevékenység befejeződött.|
|exitCode|Int32|A tevékenység kilépési kódját.|
|RetryCount|Int32|A száma a feladat rendelkezik lett rendszer megpróbálja újból végrehajtani a Batch szolgáltatás által. A rendszer megpróbálja újból végrehajtani a feladatot, ha egy nem nulla kilépési kóddal, legfeljebb a megadott MaxTaskRetryCount kilép.|
|requeueCount|Int32|A száma a feladat eredményeként egy felhasználói kérelem rendelkezik a Batch szolgáltatás által lett lefutni.<br /><br /> Amikor a felhasználó eltávolítja csomópontok egy készletet (átméretezése, vagy a készletre kicsinyítésével), vagy ha a feladat letiltása közben, a felhasználó is adja meg, hogy fut a csomópontokon feladatok várólistára kerülnek végrehajtásra. Ez a szám ebből kifolyólag a feladat rendelkezik lett várólistára hány alkalommal követi nyomon.|
