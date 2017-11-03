---
title: "Azure Batch-készlet átméretezési esemény indítása |} Microsoft Docs"
description: "Útmutató a Batch-készlet átméretezési esemény indítása."
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
ms.openlocfilehash: 826cd984d26b923ba38562e05a2e75c399be9121
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="pool-resize-start-event"></a>Készlet átméretezési start eseményét.

 Ez az esemény kibocsátott, amikor egy alkalmazáskészlet átméretezési megkezdődött. Mivel a készlet átméretezési aszinkron esemény, számíthat egy alkalmazáskészlet átméretezési teljes esemény kell kibocsátott az átméretezés befejeződése után.

 A következő példa bemutatja a készlet átméretezéséhez 0 2 csomópontokhoz egy manuális méretezze át a készlet átméretezési indító esemény törzsét.

```
{
    "poolId": "myPool1",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 0,
    "targetDedicated": 2,
    "enableAutoScale": false,
    "isAutoPool": false
}
```

|Elem|Típus|Megjegyzések|
|-------------|----------|-----------|
|poolId|Karakterlánc|A készlet azonosítóját.|
|nodeDeallocationOption|Karakterlánc|Itt adhatja meg, ha lehetséges, hogy lehet csomópontokat eltávolítani a készletből, ha a készlet méretének csökkentése.<br /><br /> Lehetséges értékek:<br /><br /> **requeue** – leállítja a futó tevékenységeket, és újra a várólistába helyezi őket. A feladatok a feladat engedélyezésekor fognak újra futni. Tevékenységek leállítása után rögtön csomópontjának eltávolítására.<br /><br /> **Állítsa le** – az éppen futó feladatok megszakítását. A tevékenységeket nem futtatja újra. Tevékenységek leállítása után rögtön csomópontjának eltávolítására.<br /><br /> **taskcompletion** – engedélyezése jelenleg futó feladatok elvégzéséhez. Nem ütemez újabb tevékenységeket való várakozás során. Távolítsa el a csomópontok, ha minden feladat befejeződött.<br /><br /> **Retaineddata** -lehetővé teszi a futó feladatok befejeződését, majd várja meg, hogy minden tevékenység adatmegőrzés időszakait lejár. Nem ütemez újabb tevékenységeket való várakozás során. Csomópontjának eltávolítására, ha az összes feladat megőrzési időszak lejárt.<br /><br /> Az alapértelmezett érték: requeue.<br /><br /> Ha a készlet mérete növekszik, akkor a változó értéke **érvénytelen**.|
|currentDedicated|Int32|A számítási csomópontok a készlethez rendelt száma.|
|targetDedicated|Int32|A kért a készlet számítási csomópontok száma.|
|enableAutoScale|logikai érték|Meghatározza, hogy a készlet mérete automatikusan igazodni adott idő alatt.|
|isAutoPool|logikai érték|Speficies hogy a készlet egy feladat AutoPool mechanizmus révén készült.|
