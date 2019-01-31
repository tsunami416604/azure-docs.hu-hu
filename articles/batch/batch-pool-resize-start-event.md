---
title: Az Azure Batch-készlet átméretezése esemény indítása |} A Microsoft Docs
description: Referencia a Batch készlet átméretezésének indítása esemény.
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
ms.openlocfilehash: 63abeaca5a9c87945671e79a9c8d7a2512d0d777
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55471334"
---
# <a name="pool-resize-start-event"></a>Készlet átméretezésének indítása esemény

 Ez az esemény bocsásson ki egy készlet átméretezése indításakor. Mivel a készlet átméretezése egy aszinkron eseményt, várható a készlet átméretezése kész esemény az átméretezési művelet befejezése után bocsátjuk rendelkezésre.

 Az alábbi példa bemutatja egy készlet átméretezéséhez 0, 2 csomópont a manuális átméretezése egy készlet átméretezésének indítása esemény törzsét.

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

|Elem|Typo|Megjegyzések|
|-------------|----------|-----------|
|poolId|String|A készlet azonosítója.|
|nodeDeallocationOption|String|Itt adható meg, amikor csomópontok lehet, hogy eltávolítja a készletből, ha a készlet méretének csökkenésekor.<br /><br /> Lehetséges értékek:<br /><br /> **újbóli várólistázás** – futó tevékenységek leállítása és újbóli várólistázása. A tevékenységek a feladat engedélyezésekor fognak újra futni. Távolítsa el a csomópontokat, amint a tevékenységek leállítása után.<br /><br /> **leállítja** – futó tevékenységek leállítása. A tevékenységek nem fognak újra futni. Távolítsa el a csomópontokat, amint a tevékenységek leállítása után.<br /><br /> **taskcompletion** – engedélyezése jelenleg futó feladatok végrehajtásához. Ne ütemezzen új feladatokat való várakozás során. Távolítsa el a csomópontok, feladatok befejezését.<br /><br /> **Retaineddata** – lehetővé teszi a futó tevékenységek befejeződését, majd megvárja, hogy minden tevékenység adatmegőrzési ideje leteljen lejár. Ne ütemezzen új feladatokat való várakozás során. Távolíthat el csomópontokat, amikor az összes feladat megőrzési időszak lejárt.<br /><br /> Az alapértelmezett érték: újbóli várólistázás.<br /><br /> Ha a készlet méretét növekszik, akkor a értéke **érvénytelen**.|
|currentDedicated|Int32|A készlethez rendelt számítási csomópontok száma.|
|targetDedicated|Int32|Számítási csomópontok a készlet kért száma.|
|enableAutoScale|Bool|Itt adhatja meg, hogy a készlet mérete automatikusan alkalmazkodik a kijelző idővel.|
|isAutoPool|Bool|Megadja, hogy a készlet hozták-e egy feladat AutoPool mechanizmus révén.|
