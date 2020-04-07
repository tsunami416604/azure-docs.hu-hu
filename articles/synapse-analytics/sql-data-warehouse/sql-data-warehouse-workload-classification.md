---
title: Számítási feladatok besorolása
description: Útmutató az Azure Synapse Analytics lekérdezéseihez szükséges számítási műveletek osztályozásának kezeléséhez.
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
ms.openlocfilehash: e7aa0c402878c994aabe4e12d811a99e300d7e67
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743649"
---
# <a name="azure-synapse-analytics-workload-classification"></a>Az Azure Synapse Analytics számítási feladatok besorolása

Ez a cikk ismerteti a számítási feladatok besorolási folyamata egy számítási feladat csoport hozzárendelése és fontossága a bejövő kérelmek Synapse SQL-készletek az Azure Synapse.

## <a name="classification"></a>Osztályozás

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

A munkaterhelés-kezelési besorolás lehetővé teszi a számítási feladatok házirendjeinek alkalmazását a kérelmekre [az erőforrásosztályok](resource-classes-for-workload-management.md#what-are-resource-classes) és [a fontosság](sql-data-warehouse-workload-importance.md)hozzárendelésével.

Bár számos módja van az adattárház számítási feladatok osztályozásának, a legegyszerűbb és leggyakoribb besorolás a betöltés és a lekérdezés. Az adatokat beszúrási, frissítési és törlési utasításokkal tölti be.  Az adatokat a kijelölésekkel lehet lekérdezni. Az adattárház-megoldás gyakran rendelkezik a terhelési tevékenység munkaterhelési házirenddel, például egy magasabb erőforrásosztály hozzárendelése több erőforrással. Egy másik számítási feladatok házirend vonatkozhat a lekérdezések, például kisebb fontosságú, mint a betöltési tevékenységek.

A terhelési és lekérdezési munkaterhelések alosztályozása is. Az albesorolás nagyobb ellenőrzést biztosít a számítási feladatok felett. A lekérdezési munkaterhelések például kockafrissítésekből, irányítópult-lekérdezésekből vagy alkalmi lekérdezésekből állhatnak. Ezek a lekérdezési munkaterhelések mindegyike különböző erőforrásosztályok vagy fontossági beállítások kal osztályozható. A terhelés is profitálhat az alosztályozáselőnyeiből. Nagyobb átalakítások rendelhetők nagyobb erőforrásosztályokhoz. Nagyobb fontosság lehet használni annak biztosítására, kulcsfontosságú értékesítési adatok betöltő előtt időjárási adatok vagy a szociális adatcsatorna.

Nem minden utasítás van besorolva, mivel nem igényel erőforrásokat, vagy fontosságot igényel a végrehajtás befolyásolásához.  A DBCC parancsok, A BEGIN, a COMMIT és a ROLLBACK TRANSACTION utasítások nem osztályozva vannak.

## <a name="classification-process"></a>Osztályozási folyamat

Besorolás a Synapse SQL-készlet az Azure Synapse érhető el ma azáltal, hogy a felhasználók hozzárendelése egy szerepkör, amely a megfelelő erőforrás-osztály hozzá rendelt [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)használatával. Ezzel a képességgel az erőforrásosztályba való bejelentkezésen túli kérelmek jellemzése korlátozott. Egy gazdagabb besorolási módszer már elérhető a [CREATE WORKLOAD CLASSIFIER](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) szintaxissal.  Ezzel a szintaxissal a Szinapszis SQL-készlet felhasználói fontosságot rendelhetnek hozzá, `workload_group` és hogy mennyi rendszererőforrás van hozzárendelve egy kérelemhez a paraméteren keresztül.

> [!NOTE]
> A besorolás értékelése kérésenként történik. Egy munkamenetben több kérelem is eltérő en adható.

## <a name="classification-weighting"></a>Osztályozási súlyozás

A besorolási folyamat részeként súlyozás van érvényben annak meghatározására, hogy melyik számítási feladat csoport van rendelve.  A súlyozás a következő:

|Osztályozó paraméter |Tömeg   |
|---------------------|---------|
|TAGNÉV:FELHASZNÁLÓ      |64       |
|TAGNÉV:SZEREPKÖR      |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

A `membername` paraméter kötelező.  Ha azonban a megadott tagnév adatbázis-felhasználó, nem pedig adatbázis-felhasználó, akkor a felhasználó súlyozása magasabb, és így az osztályozó lesz kiválasztva.

Ha egy felhasználó több szerepkör tagja, különböző erőforrásosztályokkal, több osztályozóhoz rendelve vagy egyezve, a felhasználó a legmagasabb erőforrásosztály-hozzárendelést kapja.  Ez a viselkedés összhangban van az erőforrásosztály-hozzárendelés meglévő viselkedésével.

## <a name="system-classifiers"></a>Rendszerosztályozók

A számítási feladatok besorolása rendszerszámítási osztályozók. A rendszerosztályozók a meglévő erőforrásosztály-szerepkör-tagságokat normál fontosságú erőforrás-erőforrás-hozzárendelésekre térképezik le. A rendszerosztályozók nem ejthetők el. A rendszerosztályozók megtekintéséhez futtassa az alábbi lekérdezést:

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>Erőforrásosztály-hozzárendelések összekeverése osztályozókkal

Az Ön nevében létrehozott rendszerosztályozók egyszerű elérési utat biztosítanak a számítási feladatok besorolására való áttelepítéshez. Az erőforrásosztály szerepkör-leképezések besorolási prioritással történő használata téves besoroláshoz vezethet, amikor új fontos osztályozók at kezd létrehozni.

Vegyük példaként a következő esetet:

- Egy meglévő adattárházhoz egy adatbázis-felhasználó, a DBAUser van rendelve a largerc erőforrásosztály-szerepkörhöz. Az erőforrásosztály-hozzárendelés sp_addrolemember történt.
- Az adattárház most frissül a munkaterhelés-kezeléssel.
- Az új besorolásszintaxis teszteléséhez a DBARole adatbázis-szerepkör (amelynek a DBAUser is tagja), rendelkezik egy osztályozóval, amely leképezi őket mediumrc-re és nagy jelentőséggel bír.
- Amikor a DBAUser bejelentkezik és lekérdezést futtat, a lekérdezés a largerc-hoz lesz rendelve. Mivel a felhasználó elsőbbséget élvez a szerepkör-tagsággal szemben.

A hibabesorolás hibaelhárításának egyszerűsítése érdekében javasoljuk, hogy távolítsa el az erőforrásosztály szerepkör-leképezéseit a számítási feladatok osztályozóinak létrehozásakor.  Az alábbi kód visszaadja a meglévő erőforrásosztály-szerepkör-tagságokat.  Futtassa [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) a megfelelő erőforrásosztályból visszaadott minden egyes tagnévhez.

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember '[Resource Class]', membername
```

## <a name="next-steps"></a>További lépések

- Az osztályozó konszern létrehozásáról további információt a [CREATE WORKLOAD CLASSIFIER (Transact-SQL) című témakörben talál.](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  
- Tekintse meg a munkaterhelés-osztályozó létrehozásáról a [Számítási feladatok osztályozójának létrehozásáról a Számítási feladatok osztályozójának létrehozásáról](quickstart-create-a-workload-classifier-tsql.md)című rövid útmutatót.
- Tekintse meg a [számítási feladatok fontosságának konfigurálása](sql-data-warehouse-how-to-configure-workload-importance.md) című útmutatócikkeket, valamint a [Munkaterhelés-kezelés kezelésének és figyelésének módját.](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
- A lekérdezések és a hozzárendelt fontosság megtekintéséhez tekintse meg a [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) című témakört.
