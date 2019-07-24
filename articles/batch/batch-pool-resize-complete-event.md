---
title: Azure Batch készlet átméretezése kész esemény | Microsoft Docs
description: A Batch-készlet átméretezésének befejezési eseménye.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: c2544bd2be683b731c3dac0bea651d4b64dff75e
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323200"
---
# <a name="pool-resize-complete-event"></a>Készlet átméretezése kész esemény

 Ezt az eseményt akkor bocsátja ki a rendszer, ha a készlet átméretezése befejeződött vagy meghiúsult.

 A következő példa egy készlet-átméretezési esemény törzsét mutatja be egy olyan készlet esetében, amely megnövelte a méretet, és sikeresen befejeződött.

```
{
    "id": "p_1_0_01503750-252d-4e57-bd96-d6aa05601ad8",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 4,
    "targetDedicated": 4,
    "enableAutoScale": false,
    "isAutoPool": false,
    "startTime": "2016-09-09T22:13:06.573Z",
    "endTime": "2016-09-09T22:14:01.727Z",
    "result": "Success",
    "resizeError": "The operation succeeded"
}
```

|Elem|Type|Megjegyzések|
|-------------|----------|-----------|
|id|Sztring|A készlet azonosítója.|
|nodeDeallocationOption|Sztring|Megadja, hogy a rendszer mikor távolítsa el a csomópontokat a készletből, ha a készlet mérete csökken.<br /><br /> Lehetséges értékek a következők:<br /><br /> **újravárólista** – leállítja a futó feladatokat, és újravárólistára helyezi őket. A feladatok akkor futnak újra, amikor a feladat engedélyezve van. A csomópontokat a feladatok leállítása után távolítsa el.<br /><br /> **megszakítás** – futó feladatok leállítása. A feladatok nem futnak újra. A csomópontokat a feladatok leállítása után távolítsa el.<br /><br /> **taskcompletion** – a jelenleg futó feladatok befejezésének engedélyezése. A várakozás közben nem ütemezhet új feladatokat. Csomópontok eltávolítása, ha az összes feladat befejeződött.<br /><br /> **Retaineddata** – lehetővé teszi a jelenleg futó feladatok befejezését, majd várjon, amíg az összes feladat adatmegőrzési időszaka lejár. A várakozás közben nem ütemezhet új feladatokat. Csomópontok eltávolítása, ha az összes tevékenység megőrzési időszaka lejárt.<br /><br /> Az alapértelmezett érték az újraüzenetsor.<br /><br /> Ha a készlet mérete növekszik, az érték érvénytelenre van állítva .|
|currentDedicated|Int32|A készlethez jelenleg hozzárendelt számítási csomópontok száma.|
|targetDedicated|Int32|A készlethez igényelt számítási csomópontok száma.|
|enableAutoScale|Bool|Meghatározza, hogy a készlet mérete automatikusan igazodik-e az idő múlásával.|
|isAutoPool|Bool|Azt határozza meg, hogy a készlet a feladatok autopool mechanizmusán keresztül lett-e létrehozva.|
|startTime|Datetime|A készlet átméretezésének időpontja.|
|endTime|Datetime|A készlet átméretezésének időpontja.|
|resultCode|Sztring|Az átméretezés eredménye.|
|resultMessage|Sztring|Az átméretezési hiba az eredmény részleteit tartalmazza.<br /><br /> Ha az átméretezés sikeresen befejeződött, az azt jelzi, hogy a művelet sikeres volt.|