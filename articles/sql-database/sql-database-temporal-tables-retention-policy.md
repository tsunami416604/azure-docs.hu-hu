---
title: Az időszakos táblákban tárolt korábbi adatok kezelése adatmegőrzési házirenddel | Microsoft Docs
description: Megtudhatja, hogyan használhatja az időszakos adatmegőrzési szabályzatot a vezérlőben lévő korábbi adatok megőrzése érdekében.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
ms.date: 09/25/2018
ms.openlocfilehash: 72022510676548fad79031d4334a2c95571fc16d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566376"
---
# <a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>A múltbeli adatok kezelése adatmegőrzési házirenddel rendelkező időbeli táblákban

Az ideiglenes táblák a hagyományosnál nagyobb méretű adatbázisokat növelhetik, különösen akkor, ha hosszabb ideig őrzi meg a korábbi adatmennyiséget. Ezért a korábbi adatok megőrzési házirendje fontos szempont az egyes időbeli táblázatok életciklusának megtervezéséhez és kezeléséhez. Azure SQL Databasebeli időbeli táblázatok könnyen használható adatmegőrzési mechanizmussal rendelkeznek, amely segít a feladat elvégzésében.

Az időszakos Előzmények megőrzése az egyes táblák szintjén konfigurálható, ami lehetővé teszi a felhasználók számára, hogy rugalmas korosítási házirendeket hozzanak létre. Az időbeli megőrzés alkalmazása egyszerű: ehhez csak egy paramétert kell beállítani a tábla létrehozásakor vagy a séma módosításakor.

Az adatmegőrzési házirend meghatározása után Azure SQL Database rendszeresen megkezdi az ellenőrzést, ha vannak olyan korábbi sorok, amelyek jogosultak az automatikus Adattisztításra. Az egyező sorok azonosítása és az előzményekből való eltávolításuk transzparens módon történik, a rendszer által ütemezett és futtatott háttér-feladatban. Az előzmény tábla sorainak életkori feltétele a SYSTEM_TIME időszak végét jelképező oszlop alapján van bejelölve. Ha például a megőrzési időtartam hat hónapra van állítva, a kitakarításra jogosult táblázat sorai megfelelnek a következő feltételnek:

```
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
```

Az előző példában feltételezzük, hogy a **validto tulajdonságnak** oszlop megfelel a SYSTEM_TIME időszak végének.

## <a name="how-to-configure-retention-policy"></a>Adatmegőrzési szabály konfigurálása

Az időszakos tábla adatmegőrzési szabályzatának konfigurálása előtt először győződjön meg arról, hogy az ideiglenes Előzmények megőrzése engedélyezve van-e *az adatbázis szintjén*.

```
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
```

Az adatbázis-jelző **is_temporal_history_retention_enabled** alapértelmezés szerint be van kapcsolva, de a felhasználók módosíthatják az Alter Database utasítással. A visszaállítási művelet után is automatikusan ki [](sql-database-recovery-using-backups.md) lesz kapcsolva. Az adatbázis időbeli megőrzésének megőrzése érdekében hajtsa végre a következő utasítást:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

> [!IMPORTANT]
> Az időbeli táblák megőrzését akkor is konfigurálhatja, ha a **is_temporal_history_retention_enabled** ki van kapcsolva, de az elavult sorok automatikus tisztítása nem aktiválódik ebben az esetben.

Az adatmegőrzési szabályzat a tábla létrehozásakor van konfigurálva a HISTORY_RETENTION_PERIOD paraméter értékének megadásával:

```sql
CREATE TABLE dbo.WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH
 (
     SYSTEM_VERSIONING = ON
     (
        HISTORY_TABLE = dbo.WebsiteUserInfoHistory,
        HISTORY_RETENTION_PERIOD = 6 MONTHS
     )
 );
```

Azure SQL Database lehetővé teszi a megőrzési idő megadását különböző időegységek használatával: NAP, hét, hónap és év. Ha a HISTORY_RETENTION_PERIOD nincs megadva, a rendszer a végtelen megőrzést feltételezi. A végtelen kulcsszót explicit módon is használhatja.

Bizonyos esetekben előfordulhat, hogy az adatmegőrzést a tábla létrehozása után vagy a korábban konfigurált érték módosításával szeretné konfigurálni. Ebben az esetben használja az ALTER TABLE utasítást:

```sql
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
```

> [!IMPORTANT]
> A SYSTEM_VERSIONING kikapcsolva értékre állítása *nem őrzi* meg a megőrzési időszak értékét. A SYSTEM_VERSIONING beállítása a (z) HISTORY_RETENTION_PERIOD nélkül beállítás megadása explicit módon a végtelen megőrzési időszakot eredményezi.

Az adatmegőrzési szabály aktuális állapotának áttekintéséhez használja a következő lekérdezést, amely az adatbázis szintjén, az egyes táblák megőrzési időtartamával összekapcsolja az időbeli megőrzési engedélyezési jelzőt:

```sql
SELECT DB.is_temporal_history_retention_enabled,
SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema,
T1.name as TemporalTableName,  SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema,
T2.name as HistoryTableName,T1.history_retention_period,
T1.history_retention_period_unit_desc
FROM sys.tables T1  
OUTER APPLY (select is_temporal_history_retention_enabled from sys.databases
where name = DB_NAME()) AS DB
LEFT JOIN sys.tables T2   
ON T1.history_table_id = T2.object_id WHERE T1.temporal_type = 2
```


## <a name="how-sql-database-deletes-aged-rows"></a>Az elavult sorok SQL Database törlése

A karbantartási folyamat az előzmények tábla index-elrendezéstől függ. Fontos megjegyezni, hogy *csak a fürtözött indexszel (B-Tree vagy oszlopcentrikus) rendelkező előzményekkel rendelkező táblák rendelkezhetnek véges*adatmegőrzési szabályzattal. A rendszer háttérbeli feladatot hoz létre, amely az elavult, véges megőrzési időtartamú táblákon végez adattisztítást.
A sortárindex létrehozását (B-Tree) fürtözött index tisztítási logikája törli az elavult sort kisebb adattömbökben (akár 10K-ig), ami minimalizálja az adatbázis-naplót és az IO-alrendszert. Bár a karbantartási logika a szükséges B-fa indexet használja, a megőrzési időtartamnál régebbi sorok törlésének sorrendjét nem lehet határozottan garantálni. Ezért ne *használja az alkalmazásokban a karbantartási sorrendet*.

A fürtözött oszlopcentrikus tisztítási feladata egyszerre eltávolítja a teljes [sorcsoport-csoportokat](https://msdn.microsoft.com/library/gg492088.aspx) (általában az egyes sorok 1 000 000), ami rendkívül hatékony, különösen akkor, ha a korábbi adatok nagy ütemben jönnek létre.

![Fürtözött oszlopcentrikus megőrzése](./media/sql-database-temporal-tables-retention-policy/cciretention.png)

A kiváló adattömörítés és a hatékony megőrzési idő révén a fürtözött oszlopcentrikus index tökéletes választás olyan forgatókönyvek esetén, amikor a munkaterhelés gyorsan nagy mennyiségű korábbi adat generál. Ez a minta olyan intenzív tranzakciós feldolgozási feladatokra jellemző, [amelyek időbeli táblákat használnak](https://msdn.microsoft.com/library/mt631669.aspx) a változások nyomon követéséhez és a naplózáshoz, a trendek elemzéséhez vagy a IoT adatok feldolgozásához.

## <a name="index-considerations"></a>Az index szempontjai

A sortárindex létrehozását fürtözött indextel rendelkező táblák törlési feladatának indexre van szüksége ahhoz, hogy a SYSTEM_TIME időszak végének megfelelő oszlop kezdődjön. Ha az ilyen index nem létezik, nem konfigurálhat véges megőrzési időszakot:

*Msg 13765, 16. szint, 1 <br> </br> . állapot beállítás véges megőrzési időszaka nem sikerült a rendszerverzióval ellátott "temporalstagetestdb. dbo. WebsiteUserInfo" ideiglenes táblán, mert az előzmény tábla " a temporalstagetestdb. dbo. WebsiteUserInfoHistory "nem tartalmazza a szükséges fürtözött indexet. Hozzon létre egy fürtözött oszlopcentrikus vagy B-Tree indexet a SYSTEM_TIME időszak végét megegyező oszlop alapján az előzmények táblázatban.*

Fontos megjegyezni, hogy Azure SQL Database által létrehozott alapértelmezett előzményi tábla már rendelkezik fürtözött indexszel, amely megfelel az adatmegőrzési házirendnek. Ha véges megőrzési időtartamon belül megpróbálja eltávolítani az adott indexet egy táblán, a művelet a következő hibával meghiúsul:

*Msg 13766, 16. szint, az <br> 1 </br> . állapot nem tudja eldobni a (z) "WebsiteUserInfoHistory. IX_WebsiteUserInfoHistory" fürtözött indexet, mert az elavult adatmennyiség automatikus tisztítására használatos. Ha el szeretné dobni ezt az indexet, érdemes lehet a HISTORY_RETENTION_PERIOD-t a megfelelő rendszerverzióval ellátott ideiglenes táblázatra beállítani.*

A fürtözött oszlopcentrikus index tisztítása optimálisan működik, ha a korábbi sorok bekerülnek a növekvő sorrendbe (az időszak végéig elrendezett oszlop szerint rendezve), ami mindig igaz, ha az előzmények táblát kizárólag a SYSTEM_VERSIONIOING mechanizmus tölti fel. Ha az előzmények tábla sorait nem az időszak vége oszlop szerint rendezi a rendszer (ez lehet az eset, ha áttelepítette a meglévő korábbi adatmennyiségeket), akkor az optimális elérés érdekében újra létre kell hoznia egy fürtözött oszlopcentrikus indexet a B-Tree sortárindex létrehozását indexen felül. teljesítmény.

Kerülje a fürtözött oszlopcentrikus indexek újrafordítását az előzmények táblán a véges megőrzési időtartammal, mert a sorrendet a rendszerverziószámozási művelet által természetes módon kiszabva változhat. Ha újra létre kell hoznia egy fürtözött oszlopcentrikus indexet az előzmények táblán, akkor a megfelelő B-fa indexhez való újbóli létrehozásával, a normál adattisztításhoz szükséges sorcsoportokba való tömörítéséhez megőrzi a rendelést. Ugyanezt a megközelítést kell alkalmazni, ha olyan ideiglenes táblázatot hoz létre, amely már rendelkezik egy fürtözött oszlop indextel rendelkező, garantált adatsorrend nélküli, meglévő History táblázattal:

```sql
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

Ha véges megőrzési időszak van beállítva az előzmények táblához a fürtözött oszlopcentrikus indextel, nem hozhat létre további, nem fürtözött B-Tree indexeket a táblán:

```sql
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
```

A fenti utasítás végrehajtására tett kísérlet a következő hibával meghiúsul:

*Msg 13772, 16. szint, 1 <br> </br> . állapot nem hozható létre nem fürtözött index a (z) "WebsiteUserInfoHistory" ideiglenes előzményi táblán, mert véges megőrzési időszakot és fürtözött oszlopcentrikus indexet adott meg.*

## <a name="querying-tables-with-retention-policy"></a>Táblák lekérdezése adatmegőrzési házirenddel

Az időbeli táblázat összes lekérdezése automatikusan kiszűri a véges adatmegőrzési szabályzatnak megfelelő korábbi sorokat, így elkerülhetők a kiszámíthatatlan és inkonzisztens eredmények, mivel a karbantartási feladat az elavult sorokat bármikor törölheti, *és tetszőleges sorrendben*is.

Az alábbi képen egy egyszerű lekérdezésre vonatkozó lekérdezési terv látható:

```sql
SELECT * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME ALL;
```

A lekérdezési terv további szűrőket tartalmaz a (Kiemelt) History (Kiemelt) táblázatban lévő fürtözött index-ellenőrzési operátorban az időszak végére (Validto tulajdonságnak). Ez a példa azt feltételezi, hogy egy hónapos megőrzési időszak lett beállítva a WebsiteUserInfo táblában.

![Adatmegőrzési lekérdezés szűrője](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

Ha azonban közvetlenül kérdezi le a History (előzmények) táblázatot, akkor előfordulhat, hogy a megadott megőrzési időtartamnál régebbi sorok láthatók, de az ismételhető lekérdezési eredmények garantálása nélkül. Az alábbi képen az előzmények tábla lekérdezés-végrehajtási terve látható, további szűrők alkalmazása nélkül:

![Előzmények lekérdezése adatmegőrzési szűrő nélkül](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

Ne bízza az üzleti logikát a megőrzési időszakon túli olvasási előzmények táblára, mert inkonzisztens vagy váratlan eredményekhez vezethet. Javasoljuk, hogy időbeli lekérdezéseket használjon a FOR SYSTEM_TIME záradékhoz az időbeli táblákban lévő adatok elemzéséhez.

## <a name="point-in-time-restore-considerations"></a>Időponthoz való visszaállítás szempontjai

Amikor új adatbázist [hoz létre egy meglévő adatbázis egy adott időpontra való visszaállításával](sql-database-recovery-using-backups.md), az adatbázis szintjén le van tiltva az időbeli megőrzés. (a**is_temporal_history_retention_enabled** jelző értéke kikapcsolva értékre van állítva). Ez a funkció lehetővé teszi a visszaállítás során az összes korábbi sor vizsgálatát anélkül, hogy a lekérdezés előtt el kellene távolítani az elavult sorokat. Ezzel a beállítással megvizsgálhatja a *konfigurált megőrzési időtartamon túli korábbi adatok vizsgálatát*is.

Tegyük fel, hogy egy időbeli táblának van meghatározott egy hónapos megőrzési időtartama. Ha az adatbázis prémium szintű szolgáltatási szinten lett létrehozva, akkor a múltban akár 35 napig is létrehozhat adatbázis-másolatot az adatbázis-állapottal. Ez gyakorlatilag azt teszi lehetővé, hogy a korábbi sorokat akár 65 naposra elemezze az előzmények tábla közvetlen lekérdezésével.

Ha szeretné aktiválni az időbeli megőrzési időt, futtassa a következő Transact-SQL-utasítást az időpontra vonatkozó visszaállítás után:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

## <a name="next-steps"></a>További lépések

Ha szeretné megtudni, hogyan használhatók az időbeli táblázatok az alkalmazásokban, tekintse meg [első lépések a Azure SQL Databaseban található időbeli táblázatokkal](sql-database-temporal-tables.md).

Látogasson el a Channel 9 csatornára, és tekintse meg a valós [](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016)időben zajló, [valós idejű megvalósítási](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) sikertörténetet

Az időszakos táblákkal kapcsolatos részletes információkért tekintse át az [MSDN dokumentációját](https://msdn.microsoft.com/library/dn935015.aspx).
