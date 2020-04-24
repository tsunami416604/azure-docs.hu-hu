---
title: Azure Batch készlet átméretezésének befejezése esemény
description: A Batch-készlet átméretezésének befejezési eseménye. Tekintse meg a méretet megnövelt és sikeresen befejezett készletre mutató példát.
ms.topic: article
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: 4268c9d840aa9dfadd785d74811e9d12ac32ec31
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115890"
---
# <a name="pool-resize-complete-event"></a>Készlet átméretezése kész esemény

 Ezt az eseményt akkor bocsátja ki a rendszer, ha a készlet átméretezése befejeződött vagy meghiúsult.

 A következő példa egy készlet-átméretezési esemény törzsét mutatja be egy olyan készlet esetében, amely megnövelte a méretet, és sikeresen befejeződött.

```
{
    "id": "myPool",
    "nodeDeallocationOption": "invalid",
        "currentDedicatedNodes": 10,
        "targetDedicatedNodes": 10,
    "currentLowPriorityNodes": 5,
        "targetLowPriorityNodes": 5,
    "enableAutoScale": false,
    "isAutoPool": false,
    "startTime": "2016-09-09T22:13:06.573Z",
    "endTime": "2016-09-09T22:14:01.727Z",
    "resultCode": "Success",
    "resultMessage": "The operation succeeded"
}
```

|Elem|Típus|Megjegyzések|
|-------------|----------|-----------|
|`id`|Sztring|A készlet azonosítója.|
|`nodeDeallocationOption`|Sztring|Megadja, hogy a rendszer mikor távolítsa el a csomópontokat a készletből, ha a készlet mérete csökken.<br /><br /> Lehetséges értékek:<br /><br /> **újravárólista** – leállítja a futó feladatokat, és újravárólistára helyezi őket. A feladatok akkor futnak újra, amikor a feladat engedélyezve van. A csomópontokat a feladatok leállítása után távolítsa el.<br /><br /> **megszakítás** – futó feladatok leállítása. A feladatok nem futnak újra. A csomópontokat a feladatok leállítása után távolítsa el.<br /><br /> **taskcompletion** – a jelenleg futó feladatok befejezésének engedélyezése. A várakozás közben nem ütemezhet új feladatokat. Csomópontok eltávolítása, ha az összes feladat befejeződött.<br /><br /> **Retaineddata** – lehetővé teszi a jelenleg futó feladatok befejezését, majd várjon, amíg az összes feladat adatmegőrzési időszaka lejár. A várakozás közben nem ütemezhet új feladatokat. Csomópontok eltávolítása, ha az összes tevékenység megőrzési időszaka lejárt.<br /><br /> Az alapértelmezett érték az újraüzenetsor.<br /><br /> Ha a készlet mérete növekszik, az érték **érvénytelenre**van állítva.|
|`currentDedicatedNodes`|Int32|A készlethez jelenleg hozzárendelt dedikált számítási csomópontok száma.|
|`targetDedicatedNodes`|Int32|A készlethez igényelt dedikált számítási csomópontok száma.|
|`currentLowPriorityNodes`|Int32|A készlethez jelenleg hozzárendelt alacsony prioritású számítási csomópontok száma.|
|`targetLowPriorityNodes`|Int32|A készlethez igényelt alacsony prioritású számítási csomópontok száma.|
|`enableAutoScale`|Logikai|Meghatározza, hogy a készlet mérete automatikusan igazodik-e az idő múlásával.|
|`isAutoPool`|Logikai|Azt határozza meg, hogy a készlet a feladatok autopool mechanizmusán keresztül lett-e létrehozva.|
|`startTime`|DateTime|A készlet átméretezésének időpontja.|
|`endTime`|DateTime|A készlet átméretezésének időpontja.|
|`resultCode`|Sztring|Az átméretezés eredménye.|
|`resultMessage`|Sztring| Részletes üzenet az eredményről.<br /><br /> Ha az átméretezés sikeresen befejeződött, az azt jelzi, hogy a művelet sikeres volt.|
