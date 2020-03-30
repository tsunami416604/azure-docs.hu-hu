---
title: Az Azure Batch készlet átméretezése indítási esemény
description: 'Hivatkozás a kötegkészlet átméretezési kezdőeseményéhez. Példa: egy készlet átméretezési kezdési eseményének törzse egy 0-ról 2 csomópontra manuális átméretezéssel rendelkező készlet átméretezéséhez.'
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
ms.openlocfilehash: 1866e51da30fe5ed148d019c8720755e99757df7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023582"
---
# <a name="pool-resize-start-event"></a>Készlet átméretezésének indítása esemény

 Ez az esemény akkor jelenik meg, amikor egy készlet átméretezése megkezdődött. Mivel a készlet átméretezése egy aszinkron esemény, a művelet befejezése után a készlet átméretezési teljes esemény kibocsátása várható.

 A következő példa egy készlet átméretezési kezdési eseményének törzsét mutatja be egy 0-ról 2 csomópontra manuális átméretezéssel rendelkező készlet átméretezéséhez.

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

|Elem|Típus|Megjegyzések|
|-------------|----------|-----------|
|`id`|Sztring|A készlet azonosítója.|
|`nodeDeallocationOption`|Sztring|Itt adható meg, hogy a csomópontok mikor távolíthatók el a készletből, ha a készlet mérete csökken.<br /><br /> Lehetséges értékek:<br /><br /> **requeue** – A futó feladatok leállítása és újbóli várólistára állítása. A feladatok a feladat engedélyezésekor ismét futni fognak. A feladatok leállítása után azonnal távolítsa el a csomópontokat.<br /><br /> **terminate** – A futó feladatok leállítása. A feladatok nem futnak újra. A feladatok leállítása után azonnal távolítsa el a csomópontokat.<br /><br /> **feladatkiegészítés** – A jelenleg futó feladatok befejezésének engedélyezése. Várakozás közben ne ütemezzen új tevékenységeket. Távolítsa el a csomópontokat, ha az összes feladat befejeződött.<br /><br /> **Megőrzőadatok** – A jelenleg futó feladatok befejezésének engedélyezése, majd várja meg, amíg az összes feladatadat-megőrzési időszak lejár. Várakozás közben ne ütemezzen új tevékenységeket. Távolítsa el a csomópontokat, ha az összes feladatmegőrzési időszak lejárt.<br /><br /> Az alapértelmezett érték az újraváró sor.<br /><br /> Ha a készlet mérete növekszik, akkor az érték **érvénytelenre**van állítva.|
|`currentDedicatedNodes`|Int32|A készlethez jelenleg hozzárendelt számítási csomópontok száma.|
|`targetDedicatedNodes`|Int32|A készlethez kért számítási csomópontok száma.|
|`currentLowPriorityNodes`|Int32|A készlethez jelenleg hozzárendelt számítási csomópontok száma.|
|`targetLowPriorityNodes`|Int32|A készlethez kért számítási csomópontok száma.|
|`enableAutoScale`|Logikai|Itt adható meg, hogy a készlet mérete automatikusan módosuljon-e az idő múlásával.|
|`isAutoPool`|Logikai|Itt adható meg, hogy a készlet a feladat AutoPool-mechanizmusán keresztül jött-e létre.|
