---
title: Az Azure Batch-készlet átméretezése esemény indítása |} A Microsoft Docs
description: Referencia a Batch készlet átméretezésének indítása esemény.
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
ms.openlocfilehash: 3cabf18cfd771151e62d64dc1d2b47b250ac5471
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258271"
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

|Elem|Típus|Megjegyzések|
|-------------|----------|-----------|
|poolId|Karakterlánc|A készlet azonosítója.|
|nodeDeallocationOption|Karakterlánc|Itt adható meg, amikor csomópontok lehet, hogy eltávolítja a készletből, ha a készlet méretének csökkenésekor.<br /><br /> Lehetséges értékek:<br /><br /> **újbóli várólistázás** – futó tevékenységek leállítása és újbóli várólistázása. A tevékenységek a feladat engedélyezésekor fognak újra futni. Távolítsa el a csomópontokat, amint a tevékenységek leállítása után.<br /><br /> **leállítja** – futó tevékenységek leállítása. A tevékenységek nem fognak újra futni. Távolítsa el a csomópontokat, amint a tevékenységek leállítása után.<br /><br /> **taskcompletion** – engedélyezése jelenleg futó feladatok végrehajtásához. Ne ütemezzen új feladatokat való várakozás során. Távolítsa el a csomópontok, feladatok befejezését.<br /><br /> **Retaineddata** – lehetővé teszi a futó tevékenységek befejeződését, majd megvárja, hogy minden tevékenység adatmegőrzési ideje leteljen lejár. Ne ütemezzen új feladatokat való várakozás során. Távolíthat el csomópontokat, amikor az összes feladat megőrzési időszak lejárt.<br /><br /> Az alapértelmezett érték: újbóli várólistázás.<br /><br /> Ha a készlet méretét növekszik, akkor a értéke **érvénytelen**.|
|currentDedicated|Int32|A készlethez rendelt számítási csomópontok száma.|
|targetDedicated|Int32|Számítási csomópontok a készlet kért száma.|
|enableAutoScale|Logikai|Itt adhatja meg, hogy a készlet mérete automatikusan alkalmazkodik a kijelző idővel.|
|isAutoPool|Logikai|Megadja, hogy a készlet hozták-e egy feladat AutoPool mechanizmus révén.|
