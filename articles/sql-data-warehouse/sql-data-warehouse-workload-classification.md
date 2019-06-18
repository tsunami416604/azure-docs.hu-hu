---
title: Az Azure SQL Data Warehouse besorolási |} A Microsoft Docs
description: Útmutató a fájlbesorolás segítségével egyidejűséget, fontos, kezeléséhez, és a számítási erőforrásokat az Azure SQL Data Warehouse lekérdezések.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 05/01/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 3edae23183896651efcbf7f867204a618a10c85d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66236901"
---
# <a name="azure-sql-data-warehouse-workload-classification"></a>Az Azure SQL Data Warehouse számítási feladatok besorolás

Ez a cikk bemutatja az SQL Data Warehouse számítási feladatok besorolási folyamata egy erőforrásosztály és a fontosság hozzárendelése a bejövő kérelmeket.

## <a name="classification"></a>Besorolás

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

Számítási feladat felügyeleti osztályozás lehetővé teszi, hogy a számítási feladatok házirendek hozzárendelése keresztüli kérelmeket alkalmazandó [erőforrásosztályok](resource-classes-for-workload-management.md#what-are-resource-classes) és [fontosság](sql-data-warehouse-workload-importance.md).

Míg számos módon adatraktározás számítási besorolásához, a legegyszerűbb és leggyakrabban használt besorolási, betöltés és lekérdezés. Tölt be adatokat az insert, update és delete utasításokat.  Lekérdezheti, ha kiválasztja az adatokat. Egy adattárház-megoldások gyakran kell egy számítási feladat házirend betöltése tevékenységeit, például egy nagyobb erőforrásosztály több erőforrást hozzárendelése. A különböző számítási feladatok a házirend-lekérdezés, például a tevékenységek betöltésére képest alacsonyabb fontossági lehet alkalmazni.

Akkor is subclassify a betöltés és lekérdezés számítási feladatokat is. Subclassification biztosítja a számítási feladatok vezérlése. Lekérdezési számítási feladatok például állhat, adatkocka frissül, irányítópult-lekérdezések vagy ad hoc lekérdezéseket. Ezen lekérdezési számítási feladatok különböző erőforrás osztályok vagy fontosság beállítással mindegyike osztályozhatja. Betöltés subclassification is kihasználhatják. A nagyobb erőforrásosztályok nagy átalakításokat is hozzárendelhető. Magasabb fontossági használható annak biztosítása érdekében fontos értékesítési adatokat betöltő időjárási adatok előtt, vagy egy közösségi adatcsatornát.

Nem az összes utasítást, nem szükséges erőforrásokat, és nem befolyásolhatja az végrehajtási fontosság kell sorolni.  A DBCC paranccsal a BEGIN, VÉGLEGESÍTÉSI és ROLLBACK TRANSACTION utasítások nem besorolt.

## <a name="classification-process"></a>Minősítési folyamat

Az SQL Data Warehouse besorolási úgy érhető el még ma felhasználók hozzárendelése egy szerepkör, amely rendelkezik egy megfelelő erőforrásosztály használata hozzárendelt [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). Ez a képesség korlátozott arra, hogy segítségével jól jellemezhető egy jelentkezzen be egy erőforrásosztály túli kérelmek. Gazdagabb módszert a besorolási már elérhető a a [MUNKATERHELÉS-OSZTÁLYOZÓ létrehozása](/sql/t-sql/statements/create-workload-classifier-transact-sql) szintaxist.  Ezt a szintaxist a SQL Data Warehouse-felhasználók rendelhet fontosság és a egy erőforrásosztály-kérelmekre.  

> [!NOTE]
> Besorolási kérelmek száma alapján lesz kiértékelve. Egyetlen munkamenetben több kérés eltérően kell besorolni.

## <a name="classification-precedence"></a>Besorolási sorrend

A minősítési folyamat részeként a sorrend van meghatározni, melyik erőforrásosztályhoz van rendelve. Adatbázis-felhasználó alapján besorolási élvez szerepkör tagságát. Ha létrehoz egy osztályozó, amely leképezi a Felhasználóa adatbázis-felhasználót a mediumrc erőforrásosztályhoz. A RoleA adatbázis-szerepkör (amely "a" felhasználó egyik tagja), majd képezze le a largerc erőforrásosztályhoz. Az osztályozó által igénybe vett, amely az adatbázis-felhasználót a mediumrc erőforrásosztály elsőbbséget élvez az osztályozó által igénybe vett, amely leképezi a RoleA adatbázis-szerepkör a largerc erőforrásosztályhoz.

Ha egy felhasználó több különböző erőforrásosztályok lévő több deklarációkkal matched vagy hozzárendelt szerepkörök tagjának, a felhasználó kap a legnagyobb erőforrás-osztály hozzárendelés.  Meglévő erőforrás osztály hozzárendelésének viselkedését összhangban az ezt a viselkedést.

## <a name="system-classifiers"></a>Rendszer osztályozás

Számítási feladatok besorolás a rendszer számítási feladatok osztályozó eszközökkel rendelkezik. A rendszer deklarációkkal meglévő erőforrás osztály szerepkörtagságai osztály erőforrás-hozzárendelések erőforrás a normál rendelje. Rendszer-deklarációkkal nem dobható el. Rendszer deklarációkkal megtekintéséhez futtassa az alábbi lekérdezést:

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>Erőforrás osztály-hozzárendelések a besorolások keverése

Rendszer osztályozó eszközökkel, az Ön nevében létrehozott egy egyszerű elérési migrálása a számítási feladatok besorolási megadása. Szerepkör-hozzárendelések erőforrás osztály használatával besorolási prioritással, vezethet téves besorolás hozzon létre új deklarációkkal fontosság megkezdésekor.

Vegye figyelembe az alábbi forgatókönyvet:

- Egy meglévő data warehouse egy adatbázis-felhasználót a largerc erőforrásosztály-szerepkörhöz rendelt DBAUser rendelkezik. Az erőforrás osztály-hozzárendelési sp_addrolemember volt végzett.
- Az adatraktár számítási feladatok kezelése most frissült.
- Ha tesztelni szeretné az új besorolást szintaxist, az adatbázis-szerepkör (amely DBAUser tagja) DBARole, rendelkezik egy osztályozó jönnek létre számukra a leképezése mediumrc és nagyon fontosként megjelölve.
- Amikor lekérdezést DBAUser bejelentkezik, a lekérdezés largerc rendel. Mivel a felhasználó élvez a szerepkör tagságát.

Hibaelhárítási téves besorolás egyszerűsítése érdekében, javasoljuk, számítási feladatok osztályozó létrehozása erőforrás osztály szerepkör-hozzárendelések eltávolítása.  Az alábbi kódot az osztály szerepkörtagságai meglévő erőforrás adja vissza.  Futtatás [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql) megfelelő erőforrásosztály által visszaadott minden tag név.

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

- Besorolás létrehozásával kapcsolatos további információkért lásd: a [MUNKATERHELÉS OSZTÁLYOZÓ létrehozása (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql).  
- Tekintse meg a rövid útmutatóban egy számítási feladat osztályozó létrehozása [hozzon létre egy számítási feladat osztályozó](quickstart-create-a-workload-classifier-tsql.md).
- Tekintse meg az útmutatók a [konfigurálása számítási feladatok fontossági](sql-data-warehouse-how-to-configure-workload-importance.md) és annak [felügyelheti és figyelheti a számítási feladatok kezeléséhez](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- Lásd: [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) a lekérdezések és a hozzárendelt fontosságát.