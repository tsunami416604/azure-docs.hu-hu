---
title: Terheléskezelés
description: Útmutató a számítási feladatok kezelésének megvalósításához Azure SQL Data Warehouseban.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 01/13/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 287ad5467f9f3aac7eb8c9d7c19ea15c380c6879
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935416"
---
# <a name="what-is-workload-management"></a>Mi az a számítási feladatok kezelése?

A vegyes munkaterhelések futtatása erőforrás-kihívásokat jelenthet a forgalmas rendszereken.  A megoldás-építészek megkeresik a klasszikus adatraktározási tevékenységek (például az adatbetöltések,-átalakítások és-lekérdezések) elkülönítésének módjait, így biztosítva, hogy elegendő erőforrás legyen a találatok eléréséhez.  

A fizikai kiszolgáló elkülönítése olyan infrastruktúrához vezethet, amely nincs kihasználva, túlfoglalt, vagy olyan állapotban, amelyben a gyorsítótárak állandóan a hardver indításával és leállásával vannak kitöltve.  A sikeres munkaterhelés-felügyeleti séma hatékonyan kezeli az erőforrásokat, biztosítja a hatékony erőforrás-használatot, és maximalizálja a befektetés megtérülését (ROI).

Az adatraktár-munkaterhelések az adatraktárral kapcsolatban áttekintett összes műveletre vonatkoznak. Ezeknek az összetevőknek a mélysége és szélessége az adatraktár lejárati szintjétől függ.  Az adatraktár munkaterhelése a következőket foglalja magában: 
- Az adatraktárba való betöltés teljes folyamata 
- Az adatraktár-elemzés és jelentéskészítés végrehajtása
- Az adatraktárban tárolt adatkezelés 
- Adatok exportálása az adatraktárból

Az adatraktár teljesítménybeli kapacitását az [adatraktár-egységek](what-is-a-data-warehouse-unit-dwu-cdwu.md)határozzák meg.
- Az összes teljesítménnyel kapcsolatos profilhoz lefoglalt erőforrások megtekintéséhez lásd: [memória és egyidejűségi korlátok](memory-concurrency-limits.md).
- A kapacitás módosításához vertikális [fel-vagy leskálázást](quickstart-scale-compute-portal.md)végez.


## <a name="workload-management-concepts"></a>Munkaterhelés-kezelési fogalmak
A múltban a lekérdezés teljesítményét SQL Data Warehouse [erőforrás-osztályokon](resource-classes-for-workload-management.md)keresztül felügyelte.  Erőforrás-osztályok, amelyek lehetővé teszik a memória hozzárendelését a szerepkör-tagság alapján a lekérdezésekhez.  Az erőforrás-osztályok elsődleges kihívása, hogy a konfigurálása után nem volt irányítás vagy a munkaterhelés szabályozása.  

Például egy ad-hoc felhasználói szerepkör tagságának megadása lehetővé teszi, hogy a felhasználó a rendszeren lévő memória 100%-át használja fel a smallrc.  Az erőforrás-osztályok esetében az erőforrások nem foglalhatók le és biztosíthatók a kritikus fontosságú számítási feladatok számára.

A számítási feladatok kezelése SQL Data Warehouse három magas szintű fogalomból áll: a számítási [feladatok besorolása](sql-data-warehouse-workload-classification.md), a számítási [feladatok fontossága](sql-data-warehouse-workload-importance.md) és a számítási [feladatok elkülönítése](sql-data-warehouse-workload-isolation.md).  Ezek a képességek részletesebben szabályozzák, hogy a számítási feladatok hogyan használják a rendszererőforrásokat.

A számítási feladatok besorolása egy kérelem munkaterhelési csoportba való hozzárendelésének és a fontossági szintek beállításának a fogalma.  Ez a hozzárendelés a [sp_addrolemember](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#change-a-users-resource-class)használatával történt a szerepkör-tagságon keresztül.  Ez mostantól a [munkaterhelés létrehozása CLASSIFER](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql)keresztül végezhető el.  A besorolási képesség több lehetőséget kínál, mint például a címkék, a munkamenetek és az idő a kérelmek besorolásához.

A számítási feladatok fontossága befolyásolja a kérések erőforrásokhoz való hozzáférésének sorrendjét.  A forgalmas rendszerekben a nagyobb fontosságú kérések először férnek hozzá az erőforrásokhoz.  A fontosság a zárolásokhoz való rendezett hozzáférés biztosítását is lehetővé teszi. 

A munkaterhelés elkülönítése erőforrásokat foglal le egy munkaterhelés-csoport számára.  A munkaterhelési csoport számára fenntartott erőforrások kizárólag erre a számítási feladatokra vonatkoznak a végrehajtás biztosításához.  A munkaterhelési csoportok lehetővé teszik a kérelmekhez hozzárendelt erőforrások mennyiségének meghatározását is, hasonlóan az erőforrás-osztályokhoz.  A munkaterhelési csoportok lehetővé teszi az erőforrások mennyiségének lefoglalását vagy letiltását.  Végezetül a munkaterhelési csoportok olyan mechanizmust alkalmaznak, amely a kérelmekre vonatkozó szabályokat, például a lekérdezés időtúllépését alkalmazza.  


## <a name="next-steps"></a>Következő lépések

- A számítási feladatok besorolásával kapcsolatos további információkért lásd: [munkaterhelés besorolása](sql-data-warehouse-workload-classification.md).  
- További információ a munkaterhelés elkülönítéséről: [munkaterhelés elkülönítése](sql-data-warehouse-workload-isolation.md).  
- A számítási feladatok fontosságával kapcsolatos további információkért lásd: [munkaterhelés fontossága](sql-data-warehouse-workload-importance.md).  
- További információ a munkaterhelés-kezelés figyeléséről: [munkaterhelés felügyeleti portál monitorozása](sql-data-warehouse-workload-management-portal-monitor.md).  
