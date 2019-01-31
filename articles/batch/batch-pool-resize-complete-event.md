---
title: Az Azure Batch-készlet átméretezése kész esemény |} A Microsoft Docs
description: Referencia a Batch-készlet átméretezése kész esemény.
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
ms.openlocfilehash: 87c98b89a49adbad88841dccbd4ba47d370b2be7
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55474309"
---
# <a name="pool-resize-complete-event"></a>Készlet átméretezése kész esemény

 Ez az esemény bocsásson ki, ha a készlet átméretezése befejeződött vagy meghiúsult.

 Az alábbi példa bemutatja a törzse a készlet átméretezése kész esemény-készlet, amely nagyobb méretű, és sikeresen befejeződött.

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

|Elem|Typo|Megjegyzések|
|-------------|----------|-----------|
|id|String|A készlet azonosítója.|
|nodeDeallocationOption|String|Itt adható meg, amikor csomópontok lehet, hogy eltávolítja a készletből, ha a készlet méretének csökkenésekor.<br /><br /> Lehetséges értékek:<br /><br /> **újbóli várólistázás** – futó tevékenységek leállítása és újbóli várólistázása. A tevékenységek a feladat engedélyezésekor fognak újra futni. Távolítsa el a csomópontokat, amint a tevékenységek leállítása után.<br /><br /> **leállítja** – futó tevékenységek leállítása. A tevékenységek nem fognak újra futni. Távolítsa el a csomópontokat, amint a tevékenységek leállítása után.<br /><br /> **taskcompletion** – engedélyezése jelenleg futó feladatok végrehajtásához. Ne ütemezzen új feladatokat való várakozás során. Távolítsa el a csomópontok, feladatok befejezését.<br /><br /> **Retaineddata** – lehetővé teszi a futó tevékenységek befejeződését, majd megvárja, hogy minden tevékenység adatmegőrzési ideje leteljen lejár. Ne ütemezzen új feladatokat való várakozás során. Távolíthat el csomópontokat, amikor az összes feladat megőrzési időszak lejárt.<br /><br /> Az alapértelmezett érték: újbóli várólistázás.<br /><br /> Ha a készlet méretét növekszik, akkor a értéke **érvénytelen**.|
|currentDedicated|Int32|A készlethez rendelt számítási csomópontok száma.|
|targetDedicated|Int32|Számítási csomópontok a készlet kért száma.|
|enableAutoScale|Bool|Itt adhatja meg, hogy a készlet mérete automatikusan alkalmazkodik a kijelző idővel.|
|isAutoPool|Bool|Megadja, hogy a készlet hozták-e egy feladat AutoPool mechanizmus révén.|
|startTime|DateTime|Az idő a készlet átméretezése elindult.|
|endTime|DateTime|Az idő a készlet méretezése befejeződött.|
|Eredménykód|String|Az átméretezés eredménye.|
|resultMessage|String|Az átméretezési hibát jelez az eredmény részletezi.<br /><br /> Ha az átméretezés sikeresen befejeződött, hogy a művelet sikeresen befejeződött.|