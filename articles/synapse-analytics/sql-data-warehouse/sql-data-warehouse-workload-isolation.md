---
title: Számítási feladatok elkülönítése
description: Útmutató a számítási feladatok elkülönítésének beállításához az Azure Synapse Analytics számítási feladatcsoportokkal.
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
ms.openlocfilehash: d5acdab9fb6eec585c53cfe0d7149aafa7cdc6f9
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350104"
---
# <a name="azure-synapse-analytics-workload-group-isolation-preview"></a>Azure Synapse Analytics számítási feladatok csoportelkülönítése (előzetes verzió)

Ez a cikk bemutatja, hogyan számítási feladatok csoportjai számítási feladatok elkülönítésének konfigurálására, erőforrások tárolására és futásidejű szabályok alkalmazásával a lekérdezés végrehajtásához használható.

## <a name="workload-groups"></a>Számítási feladatok csoportjai

A számítási feladatok csoportjai egy kérelemkészlet tárolói, és a számítási feladatok kezelésének , beleértve a munkaterhelés-elkülönítést is, a rendszeren konfigurálásának alapját képezik.  A munkaterhelési csoportok a [MUNKATERHELÉSCSOPORT LÉTREHOZÁSA](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) szintaxissal jönnek létre.  Egy egyszerű munkaterhelés-kezelési konfiguráció képes kezelni az adatbetöltéseket és a felhasználói lekérdezéseket.  Például egy elnevezett `wgDataLoads` számítási feladat csoport határozza meg a számítási feladatok szempontjait a rendszerbe betöltött adatokhoz. Emellett egy elnevezett `wgUserQueries` munkaterhelési csoport határozza meg a számítási feladatok szempontjait a lekérdezéseket futtató felhasználók számára az adatok olvasásához a rendszerből.

A következő szakaszok kiemelik, hogy a számítási feladatok csoportjai hogyan biztosítják az elkülönítés, elszigetelés, erőforrás-definíció kérésének és a végrehajtási szabályok betartásának lehetőségét.

## <a name="workload-isolation"></a>Számítási feladatok elkülönítése

A munkaterhelés elkülönítése azt jelenti, hogy az erőforrások kizárólag egy számítási feladatcsoport számára vannak lefoglalva.  A munkaterhelés elkülönítése úgy érhető el, hogy a MIN_PERCENTAGE_RESOURCE paramétert nullánál nagyobbra állítja a [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) szintaxisban.  A szoros SLA-khoz való folyamatos végrehajtási számítási feladatok hoz, amelyeknek szoros SLA-knak kell lenniük, az elkülönítés biztosítja, hogy az erőforrások mindig elérhetők legyenek a számítási feladatok csoport számára. 

A számítási feladatok elkülönítésének implicit módon történő konfigurálása meghatározza az egyidejűség garantált szintjét. Például egy számítási feladatok `MIN_PERCENTAGE_RESOURCE` csoport egy 30%-os és `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 2%-os beállított garantált 15 egyidejűség garantált.  Az egyidejűség szintje garantált, mert az erőforrások 15–2%-os rése mindig le `REQUEST_*MAX*_RESOURCE_GRANT_PERCENT` van foglalva a munkaterhelési csoporton belül (függetlenül attól, hogy hogyan van konfigurálva).  Ha `REQUEST_MAX_RESOURCE_GRANT_PERCENT` nagyobb, `REQUEST_MIN_RESOURCE_GRANT_PERCENT` `CAP_PERCENTAGE_RESOURCE` mint és `MIN_PERCENTAGE_RESOURCE` nagyobb, mint a további erőforrások hozzáadása kérésenként.  Ha `REQUEST_MAX_RESOURCE_GRANT_PERCENT` `REQUEST_MIN_RESOURCE_GRANT_PERCENT` és egyenlő, `CAP_PERCENTAGE_RESOURCE` és `MIN_PERCENTAGE_RESOURCE`nagyobb, mint , további egyidejűség lehetséges.  A garantált egyidejűség meghatározásához vegye figyelembe az alábbi módszert:

[Garantált egyidejűség] =`MIN_PERCENTAGE_RESOURCE`[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`] / [ ]

> [!NOTE] 
> A min_percentage_resource speciális szolgáltatási szintminimális életképes értékeket tartalmaz.  További információ: [Effective Values](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values) for further details.

A számítási feladatok elkülönítésének hiányában a kérelmek az erőforrások [megosztott készletében](#shared-pool-resources) működnek.  A megosztott készletben lévő erőforrásokhoz való hozzáférés nem garantált, és [fontossági](sql-data-warehouse-workload-importance.md) alapon van hozzárendelve.

Számítási feladatok elkülönítésének konfigurálását óvatosan kell elvégezni, mivel az erőforrások a számítási feladatok csoportjához vannak rendelve, még akkor is, ha nincsenek aktív kérelmek a munkaterhelési csoportban. Az elkülönítés túlzott konfigurálása a rendszer általános kihasználtságának csökkenéséhez vezethet.

A felhasználóknak kerülniük kell egy olyan munkaterhelés-kezelési megoldást, amely 100%-os számítási feladatok elkülönítését konfigurálja: a 100%-os elkülönítés akkor érhető el, ha az összes számítási feladat csoportban konfigurált min_percentage_resource összege 100%.  Ez a konfigurációtípus túlságosan korlátozó és merev, így kevés hely marad a véletlenül rosszul minősített erőforrás-kérelmek számára. Van egy olyan rendelkezés, amely lehetővé teszi egy kérelem végrehajtását a számítási feladatok csoportjai nem konfigurált elkülönítés. A kérelemhez rendelt erőforrások nulla ként jelennek meg a rendszerekdmv-ekben, és a rendszer által fenntartott erőforrásokból kisrc szintű erőforrás-támogatást kölcsönöznek.

> [!NOTE] 
> Az optimális erőforrás-kihasználtság érdekében fontolja meg egy számítási feladatok kezelésére szolgáló megoldás, amely kihasználja az elkülönítést annak érdekében, hogy az SLO-k teljesüljenek és keveredjenek a megosztott erőforrásokkal, amelyek a [számítási feladatok fontossága](sql-data-warehouse-workload-importance.md)alapján érhetők el.

## <a name="workload-containment"></a>A munkaterhelés megszorítása

A munkaterhelés-elszigetelés a számítási feladatok csoportja által felhasználható erőforrások mennyiségének korlátozására utal.  A munkaterhelés megszorítása úgy érhető el, hogy a CAP_PERCENTAGE_RESOURCE paramétert 100-nál kisebb értékre állítja a [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) szintaxisban.  Vegye figyelembe azt a forgatókönyvet, amelyben a felhasználóknak olvasási hozzáférést kell biztosítaniuk a rendszerhez, hogy ad-hoc lekérdezéseken keresztül futtathassanak egy "mi lenne, ha" elemzést.  Az ilyen típusú kérelmek negatív hatással lehetnek a rendszeren futó más számítási feladatokra.  Az elszigetelés konfigurálása biztosítja, hogy az erőforrások mennyisége korlátozott legyen.

A számítási feladatok elszigetelésének implicit módon történő konfigurálása határozza meg az egyidejűség maximális szintjét.  Ha a CAP_PERCENTAGE_RESOURCE 60%-ra, a REQUEST_MIN_RESOURCE_GRANT_PERCENT pedig 1%-ra van állítva, a számítási feladatok csoportjához legfeljebb 60 egyidejűségi szint engedélyezett.  A maximális egyidejűség meghatározásához vegye figyelembe az alábbi módszert:

[Maximális egyidejűség]`CAP_PERCENTAGE_RESOURCE`= [`REQUEST_MIN_RESOURCE_GRANT_PERCENT`] / [ ]

> [!NOTE] 
> A számítási feladatok csoportjának hatékony CAP_PERCENTAGE_RESOURCE nem éri el a 100%-ot, ha a számítási MIN_PERCENTAGE_RESOURCE nullánál nagyobb szinten lévő számítási feladatcsoportok jönnek létre.  Tekintse meg [a sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest) hatékony futásidejű értékeket.

## <a name="resources-per-request-definition"></a>Erőforrások kérésenkénti definíciója

A számítási feladatok csoportjai egy olyan mechanizmust biztosítanak, amely meghatározza a kérésenként lefoglalt erőforrások maz és maximális mennyiségét a REQUEST_MIN_RESOURCE_GRANT_PERCENT és REQUEST_MAX_RESOURCE_GRANT_PERCENT paraméterekkel a [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) szintaxisban.  Az erőforrások ebben az esetben a PROCESSZOR és a memória.  Ezeknek az értékeknek a konfigurálása azt diktálja, hogy mennyi erőforrás és milyen szintű egyidejűség érhető el a rendszeren.

> [!NOTE] 
> REQUEST_MAX_RESOURCE_GRANT_PERCENT egy választható paraméter, amely alapértelmezés szerint ugyanazt az értéket adja meg, mint REQUEST_MIN_RESOURCE_GRANT_PERCENT.

Egy erőforrásosztály kiválasztásához hasonlóan a REQUEST_MIN_RESOURCE_GRANT_PERCENT konfigurálja a kérés által használt erőforrások értékét.  A beállított érték által jelzett erőforrások mennyisége garantált a kérelemhez való hozzárendeléshez a végrehajtás megkezdése előtt.  Az erőforrásosztályokból munkaterhelés-csoportokba átköltözött ügyfelek esetében érdemes a [Hogyan cikket](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md) követve az erőforrásosztályokról a munkaterhelés-csoportokra való leképezéskiindulópontként.

Ha REQUEST_MAX_RESOURCE_GRANT_PERCENT REQUEST_MIN_RESOURCE_GRANT_PERCENT-nél nagyobb értékre állítja be, a rendszer kérésenként több erőforrást foglal le.  A kérelem ütemezése közben a rendszer meghatározza a kérelem hez tartozó tényleges erőforrás-hozzárendelést, amely REQUEST_MIN_RESOURCE_GRANT_PERCENT és REQUEST_MAX_RESOURCE_GRANT_PERCENT között van, a megosztott készletben lévő erőforrás rendelkezésre állása és a kérelem aktuális terhelése alapján. Rendszer.  Az erőforrásoknak a [megosztott erőforráskészletben](#shared-pool-resources) kell létezniük a lekérdezés ütemezésekor.  

> [!NOTE] 
> REQUEST_MIN_RESOURCE_GRANT_PERCENT és REQUEST_MAX_RESOURCE_GRANT_PERCENT hatékony értékekkel rendelkeznek, amelyek a tényleges MIN_PERCENTAGE_RESOURCE és CAP_PERCENTAGE_RESOURCE értékektől függenek.  Tekintse meg [a sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest) hatékony futásidejű értékeket.

## <a name="execution-rules"></a>Végrehajtási szabályok

Az ad-hoc jelentési rendszereken az ügyfelek véletlenül olyan elszabadult lekérdezéseket hajthatnak végre, amelyek súlyosan befolyásolják mások termelékenységét.  A rendszeradminisztrátorok kénytelenek időt tölteni a futó lekérdezések megölésével a rendszererőforrások felszabadítása érdekében.  A számítási feladatok csoportjai lehetővé teszik a lekérdezés végrehajtási időtúllépési szabályának konfigurálását a megadott értéket meghaladó lekérdezések megszakításához.  A szabály úgy van `QUERY_EXECUTION_TIMEOUT_SEC` konfigurálva, hogy a paramétert a [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) szintaxisban állítja be.

## <a name="shared-pool-resources"></a>Megosztott készlet erőforrásai

A megosztott készlet erőforrásai azok az erőforrások, amelyek nincsenek konfigurálva az elkülönítéshez.  Számítási feladatok csoportok egy MIN_PERCENTAGE_RESOURCE nullára van állítva a megosztott készlet erőforrásait a kérelmek végrehajtásához.  A CAP_PERCENTAGE_RESOURCE rendelkező munkaterhelés-csoportok MIN_PERCENTAGE_RESOURCE is használtak megosztott erőforrásokat.  A megosztott készletben rendelkezésre álló erőforrások mennyisége a következőképpen történik.

[Megosztott készlet] = 100 `MIN_PERCENTAGE_RESOURCE` - [az összes számítási feladat csoportban]

A megosztott készleterőforrásaihoz való hozzáférés [fontos](sql-data-warehouse-workload-importance.md) ság alapján történik.  Az azonos fontossági szintű kérelmek első bejövő/első kimenő alapon férhetnek hozzá a megosztott készlet erőforrásaihoz.

## <a name="next-steps"></a>További lépések

- [Rövid útmutató: számítási feladatok elkülönítésének konfigurálása](quickstart-configure-workload-isolation-tsql.md)
- [SZÁMÍTÁSI FELADATCSOPORT LÉTREHOZÁSA](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)
- [Erőforrásosztályok átalakítása munkaterhelés-csoportokká.](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md)
- [A számítási feladatok kezelése portál figyelése](sql-data-warehouse-workload-management-portal-monitor.md).  
