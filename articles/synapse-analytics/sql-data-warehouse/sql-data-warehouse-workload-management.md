---
title: Számítási feladatok kezelése
description: Útmutató a számítási feladatok kezelésének megvalósításához az Azure Synapse Analytics szolgáltatásban.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: dd867d4aa9a9ef5ed73e78a46826a8cd5239039b
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744235"
---
# <a name="what-is-workload-management"></a>Mi a számítási feladatok kezelése?

Vegyes számítási feladatok futtatása erőforrás-kihívásokat jelenthet a forgalmas rendszereken.  Megoldás építészek keresik a módját, hogy külön klasszikus adattárház tevékenységek (például az adatok betöltése, átalakítása és lekérdezése) annak biztosítása érdekében, hogy elegendő erőforrás létezik az SL-ek leválasztásához.  

A fizikai kiszolgálóelkülönítés olyan infrastruktúra-zsebekhez vezethet, amelyek kihasználatlanok, túlfoglaltak vagy olyan állapotban vannak, ahol a gyorsítótárak folyamatosan a hardver indításával és leállításával vannak feltöltve.  A sikeres munkaterhelés-kezelési rendszer hatékonyan kezeli az erőforrásokat, biztosítja a rendkívül hatékony erőforrás-kihasználtságot, és maximalizálja a befektetés megtérülését (ROI).

Az adattárház-munkaterhelés az adattárházhoz kapcsolódó összes műveletre vonatkozik. Ezeknek az összetevőknek a mélysége és szélessége az adattárház lejárati szintjétől függ.  Az adattárház munkaterhelése a következőket foglalja magában:

- Az adatok raktárba töltésének teljes folyamata
- Adattárház-elemzés és jelentéskészítés végrehajtása
- Adatok kezelése az adattárházban
- Adatok exportálása az adattárházból

Az adattárház teljesítménykapacitását az [adattárház egységek határozzák](what-is-a-data-warehouse-unit-dwu-cdwu.md)meg .

- Az összes teljesítményprofilhoz lefoglalt erőforrások megtekintéséhez olvassa el a [Memória és egyidejűségi korlátok című témakört.](memory-concurrency-limits.md)
- A kapacitás beállításához [fel- vagy leskálázhatja.](quickstart-scale-compute-portal.md)

## <a name="workload-management-concepts"></a>Munkaterhelés-kezelési fogalmak

A múltban az SQL Analytics az Azure Synapse felügyelte a lekérdezés teljesítményét [erőforrásosztályokon](resource-classes-for-workload-management.md)keresztül.  Az erőforrásosztályok lehetővé teszik a memória hozzárendelését egy lekérdezéshez a szerepkör-tagság alapján.  Az elsődleges kihívás az erőforrás-osztályok, hogy konfigurálása után nem volt irányítás vagy a számítási feladatok szabályozására képes.  

Például a smallrc ad-hoc felhasználói szerepkör-tagságának megadása lehetővé tette, hogy a felhasználó a rendszer memóriájának 100%-át használja fel.  Az erőforrásosztályok, nincs módja annak, hogy lefoglalja, és biztosítja, hogy az erőforrások rendelkezésre állnak a kritikus számítási feladatokhoz.

Az Azure Synapse synapse-i SQL-készlet számítási feladatainak kezelése három magas szintű koncepcióból áll: [Számítási feladatok besorolása](sql-data-warehouse-workload-classification.md), [Számítási feladatok fontossága](sql-data-warehouse-workload-importance.md) és [munkaterhelés-elkülönítés.](sql-data-warehouse-workload-isolation.md)  Ezek a képességek lehetővé teszik, hogy jobban szabályozhatja, hogy a számítási feladatok hogyan használják a rendszererőforrásait.

A számítási feladatok besorolása a kérelem hozzárendelésének és a fontossági szintek beállításának fogalma.  Történelmileg ez a feladat a [szerepkör-tagságon](resource-classes-for-workload-management.md#change-a-users-resource-class)keresztül történt sp_addrolemember használatával.  Ez most már a [CREATE WORKLOAD CLASSIFER](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)segítségével végezhető el.  A besorolási képesség gazdagabb beállításokat biztosít, például a címkét, a munkamenetet és a kérelmek besorolásához szükséges időt.

A számítási feladatok fontossága befolyásolja, hogy egy kérelem milyen sorrendben kap hozzáférést az erőforrásokhoz.  Foglalt rendszer esetén a nagyobb fontosságú kérelmek először hozzáférnek az erőforrásokhoz.  Fontosság is biztosítja a rendezett hozzáférést zárak.

A munkaterhelés elkülönítése erőforrásokat foglal le egy számítási feladatcsoport számára.  A számítási feladatok csoportjában fenntartott erőforrások kizárólag az adott számítási feladatcsoport számára vannak fenntartva a végrehajtás biztosítása érdekében.  A munkaterhelés-csoportok azt is lehetővé teszik, hogy meghatározza a kérésenként hozzárendelt erőforrások mennyiségét, hasonlóan az erőforrásosztályokhoz.  A számítási feladatok csoportjai lehetővé teszik a kérelmek által felhasználható erőforrások lefoglalását vagy korlátozását.  Végül a számítási feladatok csoportjai olyan mechanizmusok, amelyek szabályokat, például lekérdezési időkéréseket alkalmaznak a kérelmekre.  

## <a name="next-steps"></a>További lépések

- A számítási feladatok besorolásáról további információt a [Számítási feladatok besorolása című témakörben talál.](sql-data-warehouse-workload-classification.md)  
- A számítási feladatok elkülönítéséről további információt a [Számítási feladatok elkülönítése](sql-data-warehouse-workload-isolation.md)című témakörben talál.  
- A számítási feladatok fontosságáról további információt a [Számítási feladatok fontossága című témakörben talál.](sql-data-warehouse-workload-importance.md)  
- A munkaterhelés-kezelés figyeléséről a [Számítási feladatok kezelése portálfigyelés című](sql-data-warehouse-workload-management-portal-monitor.md)témakörben talál további információt.  
