---
title: Azure Batch-készlet átméretezése befejeződésének eseményét. |} Microsoft Docs
description: Útmutató a Batch-készlet átméretezése befejeződésének eseményét.
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
ms.openlocfilehash: e91ba664a69d28cae1f82710d427bd2a391305a2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="pool-resize-complete-event"></a>Készlet átméretezése kész esemény

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
|enableAutoScale|Logikai|Meghatározza, hogy a készlet mérete automatikusan igazodni adott idő alatt.|
|isAutoPool|Logikai|Meghatározza, hogy a készlet egy feladat AutoPool mechanizmus révén hozott-e.|
|startTime|DateTime|A készlet átméretezése indulásakor.|
|endTime|DateTime|Az az idő a készlet átméretezése befejeződött.|
|resultCode|Karakterlánc|Az átméretezés eredményét.|
|resultMessage|Karakterlánc|Az átméretezés hiba a az eredmény tartalmazza.<br /><br /> Ha az átméretezés sikeresen befejeződött, hogy a művelet sikeresen befejeződött.|