---
title: Számítási feladatok elkülönítése
description: Útmutató a munkaterhelések elkülönítésének beállításához Azure SQL Data Warehouseban található munkaterhelés-csoportokkal.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 11/27/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 51990e02eada52263006627be803c4073b9361ac
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555404"
---
# <a name="sql-data-warehouse-workload-group-isolation-preview"></a>SQL Data Warehouse munkaterhelési csoport elkülönítése (előzetes verzió)

Ez a cikk azt ismerteti, hogyan használhatók a munkaterhelés-csoportok a munkaterhelés elkülönítésének konfigurálásához, erőforrások tárolásához és futásidejű szabályok alkalmazásához a lekérdezés végrehajtásához.

## <a name="workload-groups"></a>Munkaterhelés-csoportok

A munkaterhelési csoportok egy adott kérelemhez tartozó tárolók, amelyek alapján a számítási feladatok kezelése, beleértve a munkaterhelés elkülönítését, egy rendszeren van konfigurálva.  A munkaterhelés-csoportok a [munkaterhelés-csoport létrehozása](https://review.docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) szintaxis használatával hozhatók létre.  Az egyszerű munkaterhelés-kezelési konfiguráció képes az adatterhelések és a felhasználói lekérdezések kezelésére.  Egy `wgDataLoads` nevű munkaterhelési csoport például meghatározza a rendszerbe betöltött adatok munkaterhelési szempontjait. Emellett az `wgUserQueries` nevű munkaterhelés-csoport munkaterhelési szempontokat határoz meg azon felhasználók számára, akik lekérdezéseket futtatnak a rendszerből származó adatok olvasásához.

A következő szakaszokban bemutatjuk, hogy a munkaterhelési csoportok hogyan határozzák meg az elkülönítést, a tárolást, a kérelem erőforrás-definícióját és a végrehajtási szabályok betartásának lehetőségét.

## <a name="workload-isolation"></a>Számítási feladatok elkülönítése

A számítási feladatok elkülönítése azt jelenti, hogy az erőforrások kizárólag a munkaterhelés-csoportok számára vannak fenntartva.  A számítási feladatok elkülönítése úgy érhető el, hogy a MIN_PERCENTAGE_RESOURCE paramétert nullánál nagyobb értékre konfigurálja a [munkaterhelés-csoport létrehozása](https://review.docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) szintaxisban.  A szűk SLA-kat betartó folyamatos végrehajtást igénylő számítási feladatokhoz az elkülönítés biztosítja, hogy az erőforrások mindig elérhetők legyenek a munkaterhelés csoport számára. 

A számítási feladatok elkülönítésének implicit beállítása implicit módon meghatároz egy garantált párhuzamossági szintet.  Ha egy MIN_PERCENTAGE_RESOURCE 30%-ra van beállítva, és a REQUEST_MIN_RESOURCE_GRANT_PERCENT 2%-ra van beállítva, a munkaterhelés-csoport esetében a 15 párhuzamossági szint garantált.  Vegye figyelembe az alábbi módszert a garantált Egyidejűség meghatározásához:

[Garantált Egyidejűség] = [`MIN_PERCENTAGE_RESOURCE`]/[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE] 
> A min_percentage_resourcehoz meghatározott szolgáltatási szint minimálisan életképes értékekkel rendelkezik.  További információ: a további részletek a [hatályos értékekben](https://review.docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values) találhatók.

A számítási feladatok elkülönítése hiányában a kérelmek az erőforrások [megosztott készletében](#shared-pool-resources) működnek.  A megosztott készlet erőforrásaihoz való hozzáférés nem garantált, és [fontossági](sql-data-warehouse-workload-importance.md) alapon van hozzárendelve.

A számítási feladatok elkülönítésének konfigurálását körültekintően kell megtenni, mivel az erőforrások a munkaterhelés-csoport számára vannak lefoglalva, még akkor is, ha a munkaterhelés csoportban nincsenek aktív kérések.  Az elkülönítés túlzott beállítása a rendszerszintű általános kihasználtság csökkenéséhez vezethet.

A felhasználóknak kerülniük kell a 100%-os munkaterhelés-elkülönítést konfiguráló munkaterhelés-kezelési megoldást: 100%-os elkülönítést akkor éri el a rendszer, ha az összes munkaterhelési csoporton konfigurált min_percentage_resource összegének értéke 100%.  Ez a típusú konfiguráció túlságosan korlátozó és merev, így kevés helyet hagy a véletlenül helytelenül besorolt erőforrás-kérelmek esetében.  Rendelkezésre áll egy olyan rendelkezés, amely lehetővé teszi, hogy egy kérelem végrehajtásához a nem elkülönítésre konfigurált munkaterhelés-csoportok fussanak.  Az ehhez a kérelemhez lefoglalt erőforrások nullaként jelennek meg a rendszerek DMV, és a rendszer által fenntartott erőforrásokból származó erőforrás-támogatás smallrc.

> [!NOTE] 
> Az optimális erőforrás-használat érdekében vegye fontolóra egy olyan munkaterhelés-kezelési megoldást, amely egy bizonyos elkülönítést használ, hogy biztosítsa a SLA-kat, és a számítási [feladatok fontossága](sql-data-warehouse-workload-importance.md)alapján elérhető megosztott erőforrásokkal együtt keverve.

## <a name="workload-containment"></a>Munkaterhelés-tárolás

A munkaterhelés-tárolás a munkaterhelés-csoport által felhasználható erőforrások mennyiségének korlátozására utal.  A munkaterhelés-tárolás úgy érhető el, hogy a CAP_PERCENTAGE_RESOURCE paramétert a MUNKATERHELÉS- [csoport létrehozása](https://review.docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) szintaxisban kevesebb mint 100 értékre konfigurálja.  Vegye figyelembe azt a forgatókönyvet, hogy a felhasználóknak olvasási hozzáféréssel kell rendelkezniük a rendszerhez, hogy a mi-if elemzést alkalmi lekérdezések használatával futtassák.  Az ilyen típusú kérelmek negatív hatással lehetnek a rendszeren futó egyéb munkaterhelésekre.  A tároló konfigurálása biztosítja, hogy az erőforrások mennyisége korlátozott legyen.

A munkaterhelés-tárolás konfigurálása implicit módon meghatározza a maximális párhuzamossági szintet.  Ha egy CAP_PERCENTAGE_RESOURCE 60%-ra van beállítva, és egy REQUEST_MIN_RESOURCE_GRANT_PERCENT 1%-ra van beállítva, a munkaterhelés-csoport számára legfeljebb 60-párhuzamossági szint adható meg.  Vegye figyelembe az alábbi módszert a maximális párhuzamosság meghatározásához:

[Maximális Egyidejűség] = [`CAP_PERCENTAGE_RESOURCE`]/[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE] 
> A munkaterhelési csoport tényleges CAP_PERCENTAGE_RESOURCEa nem éri el a 100%-ot, ha a munkaterhelési csoportok a nullánál nagyobb mértékben jönnek létre MIN_PERCENTAGE_RESOURCE.  Lásd: [sys. dm_workload_management_workload_groups_stats](https://review.docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest) a tényleges futtatókörnyezeti értékekhez.

## <a name="resources-per-request-definition"></a>Erőforrások/kérelmek definíciója

A munkaterhelési csoportok olyan mechanizmust biztosítanak, amellyel a REQUEST_MIN_RESOURCE_GRANT_PERCENT és a REQUEST_MAX_RESOURCE_GRANT_PERCENT paraméterek használatával határozható meg az erőforrások minimális és maximális mennyisége a [munkaterhelés-csoport létrehozása](https://review.docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) szintaxisban.  Ebben az esetben a processzor és a memória erőforrásai.  Ezeknek az értékeknek a beállítása azt határozza meg, hogy mennyi erőforrást és milyen szintű párhuzamosságot érhet el a rendszeren.

> [!NOTE] 
> REQUEST_MAX_RESOURCE_GRANT_PERCENT egy opcionális paraméter, amely alapértelmezés szerint a REQUEST_MIN_RESOURCE_GRANT_PERCENThoz megadott értéket adja meg.

Az erőforrás-osztály kiválasztásához hasonlóan a REQUEST_MIN_RESOURCE_GRANT_PERCENT konfigurálása a kérelem által használt erőforrások értékét állítja be.  A set érték által jelzett erőforrások mennyisége garantált a kérelemnek a végrehajtás megkezdése előtt történő kiosztásához.  Az erőforrás-osztályokból a munkaterhelés-csoportokba áttelepítést végző ügyfelek esetében vegye figyelembe, hogyan kell kiindulási pontként bemutatni az erőforrások osztályairól a munkaterhelés-csoportokba való hozzárendelésének [módját](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md) .

Ha a REQUEST_MAX_RESOURCE_GRANT_PERCENTt a REQUEST_MIN_RESOURCE_GRANT_PERCENTnál nagyobb értékre konfigurálja, a rendszer igény szerint több erőforrást foglal le.  A kérelem ütemezése során a rendszer meghatározza a kérelem tényleges erőforrás-elosztását, amely REQUEST_MIN_RESOURCE_GRANT_PERCENT és REQUEST_MAX_RESOURCE_GRANT_PERCENT között van, a megosztott készlet erőforrásainak rendelkezésre állása és a jelenlegi terhelés alapján. rendszer.  Az erőforrásoknak léteznie kell az erőforrások [megosztott készletében](#shared-pool-resources) , ha a lekérdezés ütemezve van.  

> [!NOTE] 
> REQUEST_MIN_RESOURCE_GRANT_PERCENT és REQUEST_MAX_RESOURCE_GRANT_PERCENT érvényes értékekkel rendelkeznek, amelyek az érvényes MIN_PERCENTAGE_RESOURCE és CAP_PERCENTAGE_RESOURCE értéktől függenek.  Lásd: [sys. dm_workload_management_workload_groups_stats](https://review.docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest) a tényleges futtatókörnyezeti értékekhez.

## <a name="execution-rules"></a>Végrehajtási szabályok

Az alkalmi jelentéskészítési rendszerekben az ügyfelek véletlenül olyan elszabaduló lekérdezéseket futtathatnak, amelyek jelentősen befolyásolhatják mások termelékenységét.  A rendszeradminisztrátorok arra kényszerülnek, hogy időt szabadítanak fel a rendszererőforrások felszabadítására szolgáló Runaway lekérdezésekkel.  A munkaterhelés-csoportok lehetőséget nyújtanak a lekérdezés-végrehajtási időtúllépési szabály konfigurálására a megadott értéket meghaladó lekérdezések megszakításához.  A szabály úgy van konfigurálva, hogy a `QUERY_EXECUTION_TIMEOUT_SEC` paramétert a [munkaterhelés-csoport létrehozása](https://review.docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) szintaxisban állítja be.

## <a name="shared-pool-resources"></a>Megosztott készlet erőforrásai

A megosztott készlet erőforrásai az erőforrások elkülönítésére nincsenek konfigurálva.  A MIN_PERCENTAGE_RESOURCE nulla értékkel rendelkező munkaterhelési csoportok kihasználják a megosztott készletben lévő erőforrásokat a kérelmek végrehajtásához.  A munkaterhelési csoportok CAP_PERCENTAGE_RESOURCE nagyobb, mint MIN_PERCENTAGE_RESOURCE a megosztott erőforrásokat is használták.  A megosztott készletben elérhető erőforrások mennyisége a következőképpen számítható ki.

[Megosztott készlet] = 100-[az összes munkaterhelési csoporton belüli `MIN_PERCENTAGE_RESOURCE` összege]

A megosztott készlet erőforrásaihoz való hozzáférés [fontossági](sql-data-warehouse-workload-importance.md) arányban van kiosztva.  Az azonos fontossági szintű kérelmek a megosztott készlet erőforrásait az első és az első kimenő érték alapján érik el.

## <a name="next-steps"></a>Következő lépések

- [Rövid útmutató: a munkaterhelés elkülönítésének konfigurálása](quickstart-configure-workload-isolation-tsql.md)
- [MUNKATERHELÉS-CSOPORT LÉTREHOZÁSA](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)
- [Erőforrás-osztályok átalakítása munkaterhelési csoportokra](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md).
