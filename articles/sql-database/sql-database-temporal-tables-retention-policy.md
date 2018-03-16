---
title: "Előzményadatokat az ideiglenes táblák megőrzési házirend kezelése |} Microsoft Docs"
description: "Útmutató historikus adatmegőrzési előzményadatokat az ellenőrzése alatt tartani."
services: sql-database
author: bonova
manager: craigg
ms.service: sql-database
ms.custom: develop databases
ms.topic: article
ms.date: 10/12/2016
ms.author: bonova
ms.openlocfilehash: 36ce6889cccbf5ae7df519c5c73846f12eed4a08
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Előzményadatokat az ideiglenes táblák megőrzési házirend kezelése
A historikus táblák növelheti reguláris táblák nagyobb adatbázisméret, különösen akkor, ha egy hosszabb ideig a korábbi adatok megőrzése mellett. Emiatt adatmegőrzési korábbi adatok tervezési és kezeléséért a minden historikus tábla fontos eleme. Az Azure SQL Database ideiglenes táblák megőrzési könnyen kezelhető mechanizmust, amely segít ennek a feladatnak rendelkeznek.

A historikus előzmények megőrzési lehet házirendeket az egyes tábla szintjén állítható be, lehetővé teszi a felhasználók rugalmas korosodási létrehozásához. Egyszerű historikus megőrzési alkalmazása: az szükséges, hogy csak egy paraméter tábla létrehozása vagy séma módosítása közben kell beállítani.

Adatmegőrzési házirend meghatározása után az Azure SQL Database elindul, rendszeresen ellenőrzése, hogy van-e korábbi azon sorait, amelyek jogosultak a automatikus adatok törlése. Egyező sorok azonosítása és a előzménytábla számított transzparens módon, a háttérben futó feladat, amely ütemezett, és futtassa a rendszer által történik. Az előzmények táblázat sorait kora feltételét a rendszer ellenőrzi a SYSTEM_TIME időszak végét jelölő oszlop alapján. Ha a megőrzési időtartam, például értéke hat hónapban, tisztítás jogosult tábla sorainak megfelelnek a következő feltételt:

````
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
````

Az előző példában azt feltételezi, hogy **ValidTo** SYSTEM_TIME időszak vége oszlop felel meg.

## <a name="how-to-configure-retention-policy"></a>Adatmegőrzési házirend konfigurálási módjával?
Adatmegőrzési historikus táblához tartozó konfigurálása előtt először ellenőrizze, hogy engedélyezett-e a historikus előzmények megőrzési *az adatbázis szintjén*.

````
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
````

Adatbázis-jelző **is_temporal_history_retention_enabled** alapértelmezés szerint ON értékre van állítva, de a felhasználók módosíthatják az ALTER DATABASE utasítással. Azt is automatikusan beállítása után OFF [időponthoz kötött visszaállítás](sql-database-recovery-using-backups.md) műveletet. Ahhoz, hogy az adatbázis időbeli előzmények megőrzési tisztítása, hajtsa végre a következő utasítást:

````
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
````

> [!IMPORTANT]
> Beállíthatja, hogy ideiglenes táblák még akkor is, ha a megőrzési **is_temporal_history_retention_enabled** értéke OFF, de az automatikus tisztítás elavult sorok nem indulnak el ebben az esetben.
> 
> 

A HISTORY_RETENTION_PERIOD paraméter megadásával tábla létrehozása során van konfigurálva adatmegőrzési szabály:

````
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
````

Az Azure SQL Database lehetővé teszi különböző időegységek használatával adja meg a megőrzési időtartam: nap, hét, hónap és év. Ha nincs megadva HISTORY_RETENTION_PERIOD, végtelen adatmegőrzési feltételezi. VÉGTELEN kulcsszó explicit módon is használható.

Bizonyos esetekben érdemes lehet megőrzési konfigurálása után a tábla létrehozásához, vagy módosíthatja a korábban konfigurált érték. Ebben az esetben használja az ALTER TABLE utasítást:

````
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
````

> [!IMPORTANT]
> System_versioning paramétert OFF *nem őrzi meg a* megőrzési idő értékét. System_versioning ON értékre, nélkül HISTORY_RETENTION_PERIOD megadott explicit módon eredményez a végtelen adatmegőrzési idővel.
> 
> 

Tekintse át a megőrzési házirend aktuális állapotát, használja a következő lekérdezést, amelyhez csatlakozik historikus megőrzési engedélyezése jelzőt az egyes táblák megőrzési időtartamát, az adatbázis szintjén:

````
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
````


## <a name="how-sql-database-deletes-aged-rows"></a>Hogyan SQL-adatbázis törli az elavult sorok?
A kitakarítási folyamatot a előzménytábla index elrendezésének függ. Fontos, hogy figyelje meg, hogy *csak egy fürtözött index, (B-fa vagy oszlopcentrikus) előzmények táblákban lehet konfigurált véges adatmegőrzési*. Háttérfeladat jön létre, azokat az elavult adatokat karbantartást végez a historikus táblák véges adatmegőrzési időszaktól.
Tisztítás logikát a sortárindex (B-fa) fürtözött index törli az elavult sorában kisebb csoportjai (legfeljebb 10 KB-os) minimalizálja az adatbázis naplója és I/O alrendszerbe terhelés. Bár a tisztítás logika szükséges B-fa indexszel, a régebbi, mint a megőrzési időtartam nem garantálható erősen sorok törlések sorrendjének használja. Emiatt *a tisztítás sorrendnek az alkalmazások bármely függőség nem hajtja végre*.

A karbantartási feladatot a fürtözött oszlopcentrikus eltávolítja teljes [csoportok sor](https://msdn.microsoft.com/library/gg492088.aspx) egyszerre (általában tartalmaz minden sorok 1 millió), ez nagyon hatékony, különösen akkor, ha előzményadatokat magas ütemben jön létre.

![Fürtözött oszlopcentrikus megőrzési](./media/sql-database-temporal-tables-retention-policy/cciretention.png)

Kiváló adattömörítés és hatékony megőrzési tisztítás teszi fürtözött oszlopcentrikus index forgatókönyvek tökéletes választás, ha a gyors munkaterhelése nagy mennyiségű előzményadatok. Minta része jellemzően intenzív [ideiglenes táblák használó munkaterhelések tranzakciós feldolgozást](https://msdn.microsoft.com/library/mt631669.aspx) a változások követése és naplózása, trendelemzés vagy IoT adatfeldolgozást.

## <a name="index-considerations"></a>Index kapcsolatos szempontok
A karbantartási feladat sortárindex fürtözött indexszel rendelkező táblák index kezdje az oszlop szükséges megfelelő SYSTEM_TIME időszak vége. Ha ilyen index nem létezik, a véges megőrzési időtartam nem konfigurálhatja:

*Üzenet 13765, szint 16 állapot 1 <br> </br> véges megőrzési időszak beállítása sikertelen volt a rendszerverzióval ellátott historikus tábla "temporalstagetestdb.dbo.WebsiteUserInfo", mert a előzménytábla " temporalstagetestdb.dbo.WebsiteUserInfoHistory "nem tartalmazza a szükséges fürtözött indexszel. Érdemes lehet létrehozni a fürtözött oszlopcentrikus vagy SYSTEM_TIME végének megfelelő oszlopok kezdődő B-fa indexű idő alatt a előzménytáblán.*

Fontos, hogy az alapértelmezett előzménytábla hozta létre az Azure SQL Database már rendelkezik fürtözött index, amely megfelel az adatmegőrzési láthatja. Ha el szeretné távolítani, hogy az index véges megőrzési idővel rendelkező táblán, a művelet sikertelen, a következő hiba miatt:

*Üzenet 13766, szint 16 állapot 1 <br> </br> "WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory" fürtözött index nem dobható el, mert használatban van az automatikus tisztítás az elavult adatokat. Vegye figyelembe, hogy HISTORY_RETENTION_PERIOD beállítása infinite az megfelelő rendszerverzióval ellátott historikus táblán, ha az index dobható el kell.*

A fürtözött oszlopcentrikus indexet a tisztítás optimálisan működik, ha a korábbi sorok szúrja be a növekvő sorrendben (rendezett időtartamoszlop végén), amely mindig a helyzet akkor, ha a előzménytábla fel van töltve, kizárólag a SYSTEM_VERSIONIOING mechanizmus által. Ha a sorokat a tábla rendszeridőtartam-oszlopába (amely esetben is lehet, ha meglévő előzményadatokat áttelepítette) végét szerint nem rendezett, célszerű újra létrehozni a fürtözött oszlopcentrikus index fölött B-fa sortárindex index rendelt megfelelően, optimális teljesítmény eléréséhez.

Kerülje a véges adatmegőrzési időszaktól előzménytáblán fürtözött oszloptárindex újraépítését, mert a természetes elő a rendszerverzió művelet sorcsoportok rendelési módosíthatja. Ha a előzménytáblán fürtözött oszlopcentrikus index újraépítése van szüksége, ehhez ismételt létrehozása megfelelő B-fa index, megőrzi az adatok rendszeres karbantartása szükséges rowgroups szereplő felett. Ugyanezt a megközelítést meglévő táblába rendelkezik fürtözött oszlopindex garantált adatok rendelés nélkül hozunk létre historikus tábla kell venni:

````
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
````

Ha az előzmények a fürtözött oszloptárindexet tartalmazó tábla véges megőrzési időszak van konfigurálva, az adott táblához nem hozható létre a további nem fürtözött B-fa indexek:

````
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
````

Egy újabb utasítás végrehajtása sikertelen a következő hiba miatt:

*Üzenet 13772, szint 16 állapot 1 <br> </br> index nem hozható létre fürtözetlen index historikus előzménytábla "WebsiteUserInfoHistory" mert véges megőrzési időtartam és a fürtözött oszlopcentrikus indexszel rendelkezik.*

## <a name="querying-tables-with-retention-policy"></a>Adatmegőrzési házirend-táblákban lekérdezése
A historikus tábla összes lekérdezés automatikusan kiszűrhetők korábbi véges adatmegőrzési, előre nem látható, inkonzisztens eredmény elkerülésére, mert azokat az elavult sorok törölheti a karbantartási feladat az egyező sorok *időben, tetszőleges sorrendben bármikor*.

Az alábbi képen látható a lekérdezéstervet egy egyszerű lekérdezést:

````
SELECT * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME ALL;
````

A lekérdezésterv szűrője további időtartamoszlop (ValidTo) végét alkalmazza a fürtözött Index vizsgálata operátorban (kiemelt) előzménytáblán. Ez a példa feltételezi, hogy az egy hónap megőrzési időszak WebsiteUserInfo táblán lett beállítva.

![Megőrzési lekérdezés szűrője](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

Azonban ha előzménytábla közvetlenül lekérdezéséhez jelenhet meg sorokat, amelyek a régebbi, mint a megadott megőrzési időszak, de bármely garancia nélkül a ismételhető lekérdezési eredmények. Az alábbi képen látható a lekérdezés végrehajtása lekérdezésterv a előzménytáblán további szűrők alkalmazása nélkül:

![Előzmények megőrzési szűrő nélkül lekérdezése](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

Támaszkodjon az üzleti logikát olvasási előzménytábla megőrzési időtartamon túl, mivel nem várt vagy inkonzisztens eredményeket kaphat. Azt javasoljuk, hogy használ historikus lekérdezések FOR SYSTEM_TIME záradék historikus táblákban adatok elemzéséhez.

## <a name="point-in-time-restore-considerations"></a>Mutasson a idő visszaállítási szempontjai
Új adatbázis létrehozásakor [meglévő adatbázis visszaállítása az adott időben](sql-database-recovery-using-backups.md), historikus megőrzési le van tiltva, az adatbázis szintjén van. (**is_temporal_history_retention_enabled** jelzővel állítható OFF). Ez a funkció lehetővé teszi, hogy vizsgálja meg a visszaállítást, akkor minden korábbi sor nem tartania, hogy elavult sorok előtt távolítsa el a kapott lekérdezni őket. Is használhatja, hogy *vizsgálja meg a konfigurált megőrzési időtartamon túl előzményadatokat*.

Tegyük fel például, hogy a historikus tábla rendelkezik-e a megadott időszak egy hónap megőrzési. Az adatbázis Premium szolgáltatási rétegben jött létre, ha tudná, az adatbázis állapotát az adatbázis-másolat létrehozása mentése vissza az elmúlt 35 napon. Amely hatékonyan lehetővé teszi, amelyek legfeljebb 65 napos a előzménytábla közvetlen lekérdezésével korábbi sorok elemzése.

Ha szeretné aktiválni a historikus megőrzési karbantartása, futtassa a időpontot követően a következő Transact-SQL utasítás visszaállítás egy korábbi időpontra:

````
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
````

## <a name="next-steps"></a>További lépések
Az alkalmazások ideiglenes táblák használata további tudnivalókért tekintse meg [Ismerkedés az Azure SQL Database az ideiglenes táblák](sql-database-temporal-tables.md).

Látogasson el a Channel 9 hallani a [valós felhasználói historikus végrehajtása sikeres szövegegység](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) és figyelési egy [historikus bemutató élő](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

A Historikus táblák kapcsolatos részletes információkért tekintse át [az MSDN dokumentációjában tájékozódhat](https://msdn.microsoft.com/library/dn935015.aspx).

