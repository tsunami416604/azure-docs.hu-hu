---
title: Azure Batch készlet átméretezésének kezdési eseménye
description: A Batch-készlet átméretezési indítási eseményének hivatkozása. Például egy készlet átméretezési indítási esemény törzsét jeleníti meg, ha egy készlet 0 és 2 csomópont között átméretezi a manuális átméretezést.
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
ms.openlocfilehash: 89f4b04f4ef86ffa3978cadb997d6bfb8dae31c9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449751"
---
# <a name="pool-resize-start-event"></a>Készlet átméretezésének indítása esemény

 Ezt az eseményt a rendszer a készlet átméretezésének megkezdése után bocsátja ki. Mivel a készlet átméretezése egy aszinkron esemény, várható, hogy a készlet átméretezése az átméretezési művelet befejezését követően kivezethető teljes eseményt.

 A következő példa egy készlet átméretezési indítási eseményének törzsét mutatja be, ha egy készlet 0 és 2 csomópont között átméretezi a manuális átméretezést.

```
{
    "id": "myPool1",
    "nodeDeallocationOption": "Invalid",
    "currentDedicatedNodes": 0,
    "targetDedicatedNodes": 2,
    "currentLowPriorityNodes": 0,
    "targetLowPriorityNodes": 2,
    "enableAutoScale": false,
    "isAutoPool": false
}
```

|Elem|Type (Típus)|Megjegyzések|
|-------------|----------|-----------|
|`id`|Sztring|A készlet azonosítója.|
|`nodeDeallocationOption`|Sztring|Megadja, hogy a rendszer mikor távolítsa el a csomópontokat a készletből, ha a készlet mérete csökken.<br /><br /> Lehetséges értékek:<br /><br /> **újravárólista** – leállítja a futó feladatokat, és újravárólistára helyezi őket. A feladatok akkor futnak újra, amikor a feladat engedélyezve van. A csomópontokat a feladatok leállítása után távolítsa el.<br /><br /> **megszakítás** – futó feladatok leállítása. A feladatok nem futnak újra. A csomópontokat a feladatok leállítása után távolítsa el.<br /><br /> **taskcompletion** – a jelenleg futó feladatok befejezésének engedélyezése. A várakozás közben nem ütemezhet új feladatokat. Csomópontok eltávolítása, ha az összes feladat befejeződött.<br /><br /> **Retaineddata** – lehetővé teszi a jelenleg futó feladatok befejezését, majd várjon, amíg az összes feladat adatmegőrzési időszaka lejár. A várakozás közben nem ütemezhet új feladatokat. Csomópontok eltávolítása, ha az összes tevékenység megőrzési időszaka lejárt.<br /><br /> Az alapértelmezett érték az újraüzenetsor.<br /><br /> Ha a készlet mérete növekszik, az érték **érvénytelenre**van állítva.|
|`currentDedicatedNodes`|Int32|A készlethez jelenleg hozzárendelt számítási csomópontok száma.|
|`targetDedicatedNodes`|Int32|A készlethez igényelt számítási csomópontok száma.|
|`currentLowPriorityNodes`|Int32|A készlethez jelenleg hozzárendelt számítási csomópontok száma.|
|`targetLowPriorityNodes`|Int32|A készlethez igényelt számítási csomópontok száma.|
|`enableAutoScale`|Logikai|Meghatározza, hogy a készlet mérete automatikusan igazodik-e az idő múlásával.|
|`isAutoPool`|Logikai|Azt határozza meg, hogy a készlet a feladatok autopool mechanizmusán keresztül lett-e létrehozva.|
