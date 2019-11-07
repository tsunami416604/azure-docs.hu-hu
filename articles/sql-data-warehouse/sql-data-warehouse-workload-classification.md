---
title: Számítási feladatok besorolása
description: Útmutatás a besorolás használatához a Egyidejűség, a fontosság és a számítási erőforrások kezeléséhez Azure SQL Data Warehouseban található lekérdezésekhez.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 05/01/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 15ca4b9fe3c40b7bf49d86464858747642e3cb5a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685381"
---
# <a name="azure-sql-data-warehouse-workload-classification"></a>Azure SQL Data Warehouse munkaterhelés besorolása

Ez a cikk ismerteti az erőforrás-osztály hozzárendelésének SQL Data Warehouse munkaterhelési besorolási folyamatát, valamint a beérkező kérelmek fontosságát.

## <a name="classification"></a>Osztályozás

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

A számítási feladatok kezelésének besorolása lehetővé teszi a munkaterhelési házirendek alkalmazását a kérelmekre az [erőforrás-osztályok](resource-classes-for-workload-management.md#what-are-resource-classes) és a [fontosság](sql-data-warehouse-workload-importance.md)kiosztásával.

Az adattárház-feladatok osztályozásának számos módja van, a legegyszerűbb és leggyakoribb besorolás a betöltés és a lekérdezés. Az adatokat INSERT, Update és DELETE utasításokkal kell betölteni.  Az adatlekérdezés a selects paranccsal végezhető el. Az adattárház-megoldások gyakran munkaterhelési szabályzattal rendelkeznek a betöltési tevékenységhez, például egy magasabb erőforrás-osztály hozzárendeléséhez több erőforrással. A lekérdezésekre más számítási feladatok is alkalmazhatók, például a terhelési tevékenységekhez képest kevésbé fontos.

A terhelési és a lekérdezési számítási feladatok is meghatározhatók. Az albesorolás nagyobb mértékben szabályozza a számítási feladatokat. A lekérdezési munkaterhelések például a kocka frissítése, az irányítópult-lekérdezések és az alkalmi lekérdezések állhatnak. Ezeket a lekérdezési feladatokat különböző erőforrás-osztályokkal vagy fontossági beállításokkal osztályozhatja. A betöltés is kihasználhatja a besorolást. A nagyméretű átalakítások nagyobb erőforrás-osztályokhoz rendelhetők. A legfontosabb értékesítési adatforgalom az időjárási vagy a közösségi adatcsatorna megkezdése előtt nagyobb fontosságot biztosít.

Nem minden utasítás van besorolva, mert nincs szükség erőforrásra, vagy fontos a végrehajtás befolyásolása.  A DBCC parancsok, a BEGIN, a COMMit és a revisszagurítás TRANZAKCIÓs utasítások nincsenek besorolva.

## <a name="classification-process"></a>Besorolási folyamat

A SQL Data Warehouse besorolása ma érhető el, ha olyan szerepkörhöz rendel hozzá felhasználókat, amelyhez hozzá van rendelve egy megfelelő erőforrás-osztály a [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)használatával. Az erőforrás-osztályba való bejelentkezésen túli kérések jellemzésének lehetősége korlátozott ezzel a képességgel. Most már elérhető a számítási [feladatok](/sql/t-sql/statements/create-workload-classifier-transact-sql) besorolása szintaxissal, amely a besoroláshoz használható.  Ezzel a szintaxissal SQL Data Warehouse a felhasználók fontosságot és erőforrás-osztályt rendelhetnek a kérelmekhez.  

> [!NOTE]
> A besorolást a rendszer a kérelmek alapján értékeli ki. Egy munkamenetben több kérelem is besorolva különböző módon.

## <a name="classification-precedence"></a>Besorolási sorrend

A besorolási folyamat részeként a rendszer elsőbbséget biztosít annak meghatározásához, hogy melyik erőforrás-osztály van hozzárendelve. Az adatbázis-felhasználó alapján történő besorolás elsőbbséget élvez a szerepkör tagságával szemben. Ha olyan osztályozó hoz létre, amely leképezi a felhasználó adatbázis-felhasználóját a mediumrc-erőforrás osztályba. Ezután képezze le a szerepkör-adatbázis szerepkört (amely a felhasználó tagja) a largerc-erőforrás osztályának. Az adatbázis-felhasználót a mediumrc erőforrás osztályra leképező osztályozó elsőbbséget élvez az osztályozó felett, amely a szerepkör-adatbázis szerepkört leképezi a largerc-erőforrás osztályba.

Ha a felhasználó több olyan szerepkör tagja, amely különböző osztályokba van rendelve, vagy több besoroláshoz van társítva, akkor a felhasználó a legmagasabb erőforrás-osztály-hozzárendelést kapja.  Ez a viselkedés összhangban van a meglévő erőforrás-osztály hozzárendelési viselkedésével.

## <a name="system-classifiers"></a>Rendszerosztályozók

A számítási feladatok besorolása rendszerterhelési besorolásokkal rendelkezik. A rendszerosztályozók hozzárendelik a meglévő erőforrás-osztály szerepkör-tagságot az erőforrás-osztály erőforrás-hozzárendeléseihez a normál fontossággal. Nem lehet eldobni a rendszer-osztályozók. A rendszerosztályozók megtekintéséhez futtassa az alábbi lekérdezést:

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>Erőforrás-osztályok hozzárendeléseinek összekeverése osztályozók szerint

Az Ön nevében létrehozott rendszerosztályozók egyszerű útvonalat biztosítanak a munkaterhelés-besorolásra való áttéréshez. A besorolási prioritással rendelkező erőforrás-osztály szerepkör-hozzárendelések használata téves besorolást eredményezhet, mivel új osztályozók fontossággal való létrehozásának megkezdése.

Vegye figyelembe a következő helyzetet:

- A meglévő adattárházak adatbázis-felhasználói DBAUser vannak hozzárendelve a largerc erőforrás-osztály szerepkörhöz. Az erőforrás-osztály hozzárendelése a sp_addrolemember-mel történt.
- Az adattárház már frissítve van a munkaterhelés-kezeléssel.
- Az új besorolási szintaxis teszteléséhez a DBARole adatbázis-szerepkör (amely a DBAUser tagja), rendelkezik egy, a számukra a mediumrc és a nagy fontossággal való leképezéshez létrehozott osztályozó.
- Amikor a DBAUser bejelentkezik és futtat egy lekérdezést, a lekérdezés a largerc lesz hozzárendelve. Mivel a felhasználók elsőbbséget élveznek a szerepkör tagságával szemben.

A hibák elhárítása érdekében javasoljuk, hogy távolítsa el az erőforrás-osztály szerepkör-hozzárendeléseket a számítási feladatok besorolásának létrehozásakor.  Az alábbi kód az erőforrás-osztály meglévő szerepkör-tagságát adja vissza.  Futtassa a [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql) a megfelelő erőforrás osztályból visszaadott összes tag nevéhez.

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember ‘[Resource Class]’, membername
```

## <a name="next-steps"></a>További lépések

- Az osztályozó létrehozásával kapcsolatos további információkért lásd a [munkaterhelés-osztályozó létrehozása (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql)című témakört.  
- Tekintse meg a számítási feladatok besorolásának létrehozásával kapcsolatos útmutatót a számítási [feladatok besorolása](quickstart-create-a-workload-classifier-tsql.md)című témakörben.
- Tekintse meg az útmutatókat a számítási [feladatok fontosságának konfigurálásához](sql-data-warehouse-how-to-configure-workload-importance.md) , valamint a számítási [feladatok felügyeletének kezeléséhez és figyeléséhez](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- A lekérdezéseket és a hozzárendelt fontosságot a [sys. DM _pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) tekintheti meg.