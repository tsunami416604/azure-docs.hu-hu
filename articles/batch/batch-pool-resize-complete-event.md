---
title: Az Azure Batch készlet átméretezése teljes esemény
description: Hivatkozás a kötegkészlet átméretezéséhez a teljes eseményre. Példa egy készletre, amely mérete növekedett és sikeresen befejeződött.
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
ms.openlocfilehash: e2c66471ad9fe8d917d1ffddceb6e01c339d62dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022222"
---
# <a name="pool-resize-complete-event"></a>Készlet átméretezése kész esemény

 Ez az esemény akkor jelenik meg, ha egy készlet átméretezése befejeződött vagy nem sikerült.

 A következő példa egy készlet testének átméretezése teljes esemény egy készlet, amely nőtt a mérete, és sikeresen befejeződött.

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
|`nodeDeallocationOption`|Sztring|Itt adható meg, hogy a csomópontok mikor távolíthatók el a készletből, ha a készlet mérete csökken.<br /><br /> Lehetséges értékek:<br /><br /> **requeue** – A futó feladatok leállítása és újbóli várólistára állítása. A feladatok a feladat engedélyezésekor ismét futni fognak. A feladatok leállítása után azonnal távolítsa el a csomópontokat.<br /><br /> **terminate** – A futó feladatok leállítása. A feladatok nem futnak újra. A feladatok leállítása után azonnal távolítsa el a csomópontokat.<br /><br /> **feladatkiegészítés** – A jelenleg futó feladatok befejezésének engedélyezése. Várakozás közben ne ütemezzen új tevékenységeket. Távolítsa el a csomópontokat, ha az összes feladat befejeződött.<br /><br /> **Megőrzőadatok** – A jelenleg futó feladatok befejezésének engedélyezése, majd várja meg, amíg az összes feladatadat-megőrzési időszak lejár. Várakozás közben ne ütemezzen új tevékenységeket. Távolítsa el a csomópontokat, ha az összes feladatmegőrzési időszak lejárt.<br /><br /> Az alapértelmezett érték az újraváró sor.<br /><br /> Ha a készlet mérete növekszik, akkor az érték **érvénytelenre**van állítva.|
|`currentDedicatedNodes`|Int32|A készlethez jelenleg hozzárendelt dedikált számítási csomópontok száma.|
|`targetDedicatedNodes`|Int32|A készlethez kért dedikált számítási csomópontok száma.|
|`currentLowPriorityNodes`|Int32|A készlethez jelenleg hozzárendelt alacsony prioritású számítási csomópontok száma.|
|`targetLowPriorityNodes`|Int32|A készlethez kért alacsony prioritású számítási csomópontok száma.|
|`enableAutoScale`|Logikai|Itt adható meg, hogy a készlet mérete automatikusan módosuljon-e az idő múlásával.|
|`isAutoPool`|Logikai|Itt adható meg, hogy a készlet a feladat AutoPool-mechanizmusán keresztül jött-e létre.|
|`startTime`|DateTime|A készlet átméretezésének kezdete.|
|`endTime`|DateTime|A készlet átméretezésének befejezése.|
|`resultCode`|Sztring|Az átméretezés eredménye.|
|`resultMessage`|Sztring| Részletes üzenet az eredményről.<br /><br /> Ha az átméretezés sikeresen befejeződött, azt állítja, hogy a művelet sikeres volt.|
