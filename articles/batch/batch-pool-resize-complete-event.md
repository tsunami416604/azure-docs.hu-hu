---
title: "Azure Batch-készlet átméretezése befejeződésének eseményét. |} Microsoft Docs"
description: "Útmutató a Batch-készlet átméretezése befejeződésének eseményét."
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
ms.openlocfilehash: 7072293d98526812cb42ce9c2f8e33bfcafaa149
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="pool-resize-complete-event"></a>Készlet átméretezési befejeződésének eseményét.

 Ez az esemény is ki lesz adva a készlet átméretezési rendelkezik befejezése után, illetve nem sikerült.

 Az alábbi példában a szervezet egy alkalmazáskészlet átméretezési teljes esemény készlet mérete megnövekedett, és sikeresen befejeződött.

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

|Elem|Típus|Megjegyzések|
|-------------|----------|-----------|
|id|Karakterlánc|A készlet azonosítóját.|
|nodeDeallocationOption|Karakterlánc|Itt adhatja meg, ha lehetséges, hogy lehet csomópontokat eltávolítani a készletből, ha a készlet méretének csökkentése.<br /><br /> Lehetséges értékek:<br /><br /> **requeue** – leállítja a futó tevékenységeket, és újra a várólistába helyezi őket. A feladatok a feladat engedélyezésekor fognak újra futni. Tevékenységek leállítása után rögtön csomópontjának eltávolítására.<br /><br /> **Állítsa le** – az éppen futó feladatok megszakítását. A tevékenységeket nem futtatja újra. Tevékenységek leállítása után rögtön csomópontjának eltávolítására.<br /><br /> **taskcompletion** – engedélyezése jelenleg futó feladatok elvégzéséhez. Nem ütemez újabb tevékenységeket való várakozás során. Távolítsa el a csomópontok, ha minden feladat befejeződött.<br /><br /> **Retaineddata** -lehetővé teszi a futó feladatok befejeződését, majd várja meg, hogy minden tevékenység adatmegőrzés időszakait lejár. Nem ütemez újabb tevékenységeket való várakozás során. Csomópontjának eltávolítására, ha az összes feladat megőrzési időszak lejárt.<br /><br /> Az alapértelmezett érték: requeue.<br /><br /> Ha a készlet mérete növekszik, akkor a változó értéke **érvénytelen**.|
|currentDedicated|Int32|A számítási csomópontok a készlethez rendelt száma.|
|targetDedicated|Int32|A kért a készlet számítási csomópontok száma.|
|enableAutoScale|logikai érték|Meghatározza, hogy a készlet mérete automatikusan igazodni adott idő alatt.|
|isAutoPool|logikai érték|Meghatározza, hogy a készlet egy feladat AutoPool mechanizmus révén hozott-e.|
|startTime|Dátum és idő|A készlet átméretezése indulásakor.|
|Befejezés időpontja|Dátum és idő|Az az idő a készlet átméretezése befejeződött.|
|ResultCode|Karakterlánc|Az átméretezés eredményét.|
|resultMessage|Karakterlánc|Az átméretezés hiba a az eredmény tartalmazza.<br /><br /> Ha az átméretezés sikeresen befejeződött, hogy a művelet sikeresen befejeződött.|