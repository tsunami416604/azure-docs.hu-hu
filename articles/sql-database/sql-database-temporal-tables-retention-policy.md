---
title: Előzményadatok kezelése időbeli táblázatokban
description: Ismerje meg, hogyan használhatja az időbeli adatmegőrzési szabályzatot a korábbi adatok felügyelet alatt tartására.
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
ms.openlocfilehash: 3c2460c6f5e0905f45106148ecc3e8a949cf221f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820690"
---
# <a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Előzményadatok kezelése a temporális táblákban adatmegőrzési házirenddel

A historikus táblák növelhetik az adatbázis méretét, mint a normál táblák, különösen akkor, ha a korábbi adatokat hosszabb ideig őrzi meg. Ezért a korábbi adatok adatmegőrzési házirendje fontos szempont az egyes időbeli tábla életciklusának tervezéséhez és kezeléséhez. Az Azure SQL Database időbeli táblái könnyen használható adatmegőrzési mechanizmussal vannak ellátva, amely segít a feladat elvégzésében.

Az időbeli előzmények megőrzése az egyes táblaszinten konfigurálható, amely lehetővé teszi a felhasználók számára, hogy rugalmas öregedési házirendeket hozzanak létre. Az időbeli megtartás alkalmazása egyszerű: csak egy paramétert kell beállítani a tábla létrehozása vagy a séma módosítása során.

Az adatmegőrzési szabályzat meghatározása után az Azure SQL Database rendszeresen elkezdi ellenőrizni, hogy vannak-e olyan előzménysorok, amelyek jogosultak az automatikus adattisztításra. Az egyező sorok azonosítása és az előzménytáblából való eltávolításuk transzparens módon történik a rendszer által ütemezett és futtatott háttérfeladatban. A program a történelemtábla sorainak korfeltételét az SYSTEM_TIME időszak végét jelző oszlop alapján ellenőrzi. Ha például a megőrzési időszak hat hónapra van állítva, a karbantartásra alkalmas táblasorok megfelelnek a következő feltételeknek:

```
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
```

Az előző példában azt feltételeztük, hogy **a ValidTo** oszlop SYSTEM_TIME időszak végének felel meg.

## <a name="how-to-configure-retention-policy"></a>Az adatmegőrzési házirend konfigurálása

Mielőtt adatmegőrzési házirendet konfigurálna egy időbeli táblához, először ellenőrizze, hogy *az adatbázis szintjén*engedélyezve van-e az időbeli előzménymegőrzés.

```
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
```

Az adatbázisjelző **is_temporal_history_retention_enabled** alapértelmezés szerint BE értékre van állítva, de a felhasználók megváltoztathatják az ALTER DATABASE utasítással. Azt is automatikusan be van kapcsolva, hogy OFF [időpont után az idő-visszaállítási](sql-database-recovery-using-backups.md) művelet. Az adatbázis időbeli előzménymegőrzési karbantartásának engedélyezéséhez hajtsa végre a következő utasítást:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

> [!IMPORTANT]
> Beállíthatja az időbeli táblák megtartását akkor is, ha **is_temporal_history_retention_enabled** ki van kapcsolva, de az automatikus karbantartás az elévülött sorok esetén nem aktiválódik.

Az adatmegőrzési házirend a tábla létrehozása során a HISTORY_RETENTION_PERIOD paraméter értékének megadásával van konfigurálva:

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

Az Azure SQL Database lehetővé teszi, hogy megőrzési időszakot adjon meg különböző időegységek használatával: NAPOK, HETEK, HÓNAPOK ÉS ÉVEK. Ha HISTORY_RETENTION_PERIOD nincs megadva, a függvény infinite retenciót feltételez. Az INFINITE kulcsszót explicit módon is használhatja.

Bizonyos esetekben előfordulhat, hogy a tábla létrehozása után szeretné konfigurálni az adatmegőrzést, vagy módosítani szeretné a korábban konfigurált értéket. Ebben az esetben használja alter table utasítás:

```sql
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
```

> [!IMPORTANT]
> A SYSTEM_VERSIONING beállítása KI *értékre nem őrzi meg* a megőrzési időszak értékét. Ha SYSTEM_VERSIONING be van állítva HISTORY_RETENTION_PERIOD kifejezetten meghatározott HISTORY_RETENTION_PERIOD nélkül, az INFINITE megőrzési időszakot eredményez.

Az adatmegőrzési szabály aktuális állapotának áttekintéséhez használja a következő lekérdezést, amely az adatbázis szintjén egyesíti az időbeli megőrzési engedélyezésjelzőt az egyes táblák megőrzési időszakaival:

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


## <a name="how-sql-database-deletes-aged-rows"></a>Hogyan törli az SQL Database az elévültek sorokat?

A törlési folyamat az előzménytábla indexelrendezésétől függ. Fontos figyelembe venni, hogy *csak a fürtözött indexszel (B-fával vagy oszlopcentrikussal) rendelkező előzménytáblák rendelkezhetnek véges adatmegőrzési házirendkonfigurálásával.* Egy háttérfeladat jön létre, hogy végre érlelési adatok karbantartása az összes időbeli táblák véges megőrzési időszak.
A sortároló (B-fa) fürtözött indexének törlési logikája kisebb adattömbökben (legfeljebb 10 K) törli az elévült sorokat, minimalizálva az adatbázisnapló és az IO-alrendszer terhelését. Bár a törlési logika a szükséges B-fa indexet használja, a megőrzési időtartamnál régebbi sorok törlési sorrendje nem garantálható szilárdan. Ezért *ne vegyen semmilyen függőséget az alkalmazások tisztítási sorrendjétől*.

A fürtözött oszlopcentrikus törlési feladat egyszerre teljes [sorcsoportokat](https://msdn.microsoft.com/library/gg492088.aspx) távolít el (általában egyenként 1 millió sort tartalmaz), ami nagyon hatékony, különösen akkor, ha az előzményadatok nagy ütemben jönnek létre.

![Fürtözött oszlopcentrikus adatmegőrzés](./media/sql-database-temporal-tables-retention-policy/cciretention.png)

A kiváló adattömörítés és a hatékony adatmegőrzési karbantartás tökéletes választássá teszi a fürtözött oszlopcentrikus indexet olyan esetekben, amikor a munkaterhelés gyorsan nagy mennyiségű előzményadatot generál. Ez a minta jellemző az intenzív [tranzakciós feldolgozási számítási feladatok, amelyek időbeli táblák](https://msdn.microsoft.com/library/mt631669.aspx) változáskövetés és naplózás, trendelemzés vagy IoT-adatok betöltése.

## <a name="index-considerations"></a>Az indexre vonatkozó szempontok

A rowstore fürtözött indexszel rendelkező táblák törlési feladatához az indexnek a SYSTEM_TIME időszak végének megfelelő oszloppal kell kezdenie. Ha ilyen index nem létezik, nem konfigurálhat véges megőrzési időszakot:

*Msg 13765, Level 16, State 1 <br> </br> A véges megőrzési időszak beállítása nem sikerült a rendszerverziójú temporal table "temporalstagetestdb.dbo.WebsiteUserInfo" rendszeren, mert a "temporalstagetestdb.dbo.WebsiteUserHistory" előzménytábla nem tartalmazza a szükséges fürtözött indexet. Fontolja meg egy fürtözött oszlopcentrikus vagy B-fa index létrehozása kezdve az oszlop, amely megfelel SYSTEM_TIME időszak végén, az előzmények táblázatban.*

Fontos figyelembe venni, hogy az Azure SQL Database által létrehozott alapértelmezett előzménytábla már rendelkezik fürtözött index, amely megfelel az adatmegőrzési szabályzat. Ha egy véges megőrzési időszakkal rendelkező táblából próbálja eltávolítani az indexet, a művelet a következő hibával sikertelen:

*Msg 13766, Level 16, State 1 <br> </br> Nem ejtheti a fürtözött index "WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory", mert használják az automatikus karbantartása aged adatok. Fontolja meg HISTORY_RETENTION_PERIOD beállítása INFINITE a megfelelő rendszerverziójú időbeli tábla, ha el kell dobni ezt az indexet.*

A fürtözött oszlopcentrikus index karbantartása optimálisan működik, ha a korábbi sorokat növekvő sorrendbe szúrja be (az időszak végére rendezve), ami mindig akkor áll fenn, ha az előzménytáblát kizárólag a SYSTEM_VERSIONIOING-mechanizmus tölti fel. Ha az előzménytábla sorai nem vannak megrendezve az időszak végére oszlop szerint (ami előfordulhat, ha meglévő előzményadatokat telepített át), akkor újra létre kell hoznia a fürtözött oszlopcentrikus indexet a megfelelően rendezett B-fa sortárindex e-alapú tetején, hogy optimális legyen. Teljesítmény.

Kerülje a fürtözött oszlopcentrikus index újraépítését az előzménytáblában a véges megőrzési időszakkal, mert megváltoztathatja a rendezést a rendszerverzió-művelet által természetesen előírt sorcsoportokban. Ha újra kell építenie a fürtözött oszlopcentrikus indexet az előzménytáblában, ezt úgy kell megtennie, hogy újra létrehozza azt a megfelelő B-fa indexen, megőrizve a rendszeres adatkarbantartáshoz szükséges sorcsoportok sorrendjét. Ugyanezt a megközelítést kell alkalmazni, ha olyan hisspektális táblát hoz létre, amely nek fürtözött oszlopindexe garantált adatsorrend nélkül van:

```sql
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

Ha véges megőrzési időszak van konfigurálva az előzménytáblához a fürtözött oszlopcentrikus indexszel, nem hozhat létre további nem fürtözött B-fa indexeket a táblában:

```sql
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
```

A fenti utasítás végrehajtására tett kísérlet a következő hibával sikertelen:

*Msg 13772, Level 16, State 1 <br> </br> Nem hozható létre nem fürtözött index a "WebsiteUserInfoHistory" időbeli előzménytáblában, mivel véges megőrzési időszakkal és fürtözött oszlopcentrikus indexdefiniálva van.*

## <a name="querying-tables-with-retention-policy"></a>Táblák lekérdezése adatmegőrzési házirenddel

Az időbeli tábla összes lekérdezése automatikusan kiszűri a véges adatmegőrzési házirendnek megfelelő előzménysorokat, hogy elkerülje az előre nem látható és következetlen eredményeket, mivel az elévült sorokat a törlési feladat *bármikor és tetszőleges sorrendben törölheti.*

Az alábbi képen egy egyszerű lekérdezés lekérdezési terve látható:

```sql
SELECT * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME ALL;
```

A lekérdezési terv további szűrőt tartalmaz az időszak végére (ValidTo) az előzménytábla fürtözött indexvizsgálat-operátorában (kiemelve). Ez a példa feltételezi, hogy egy hónap megőrzési időszak volt beállítva a WebsiteUserInfo táblában.

![Adatmegőrzési lekérdezés szűrő](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

Ha azonban közvetlenül az előzménytáblát kérdezi le, előfordulhat, hogy a megadott megőrzési időszaknál régebbi sorokat lát, de az ismételhető lekérdezési eredményekre vonatkozó garanciát nem. Az alábbi képen az előzménytáblában lévő lekérdezés végrehajtási terve további szűrők alkalmazása nélkül látható:

![Előzmények lekérdezése adatmegőrzési szűrő nélkül](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

Ne támaszkodjon az üzleti logika olvasási előzménytábla túl megőrzési időszak, mert előfordulhat, hogy inkonzisztens vagy váratlan eredményeket kaphat. Azt javasoljuk, hogy a FOR SYSTEM_TIME záradékkal rendelkező időbeli lekérdezéseket használjon az adatok időbeli táblákban való elemzéséhez.

## <a name="point-in-time-restore-considerations"></a>Időpont-visszaállítási szempontok

Amikor új adatbázist hoz létre [a meglévő adatbázis adott időpontra való visszaállításával,](sql-database-recovery-using-backups.md)az adatbázis szintjén le van tiltva az időbeli megőrzés. (**is_temporal_history_retention_enabled** jelző jeoff). Ez a funkció lehetővé teszi, hogy vizsgálja meg az összes történelmi sorok visszaállítása, nem kell aggódnia, hogy az idős sorok eltávolítása előtt kap, hogy a lekérdezés őket. Segítségével ellenőrizheti az *előzményadatokat a beállított megőrzési időszakon túl.*

Tegyük fel, hogy egy időbeli tábla egy hónap megőrzési időszak van megadva. Ha az adatbázis prémium szolgáltatási szinten lett létrehozva, létrehozhat adatbázis-másolatot az adatbázis állapotával, legfeljebb 35 nappal a múltban. Ez hatékonyan lehetővé tenné, hogy elemezze a legfeljebb 65 napos előzménysorokat az előzménytábla közvetlen lekérdezésével.

Ha aktiválni szeretné az időbeli visszatartási karbantartást, futtassa a következő Transact-SQL utasítást az idő visszaállítása után:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

## <a name="next-steps"></a>További lépések

Ha meg szeretné tudni, hogyan használhatja a historikus táblákat az alkalmazásokban, olvassa [el az Azure SQL Database –, az Első lépések a historikus táblákkal](sql-database-temporal-tables.md)című részt.

Látogasson el a Channel 9 hallani egy [igazi ügyfél időbeli végrehajtási sikertörténet,](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) és nézni egy [élő temporális demonstráció](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

A temporális táblákkal kapcsolatos részletes információkért tekintse át az [MSDN dokumentációját.](https://msdn.microsoft.com/library/dn935015.aspx)
