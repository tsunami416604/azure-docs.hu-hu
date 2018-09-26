---
title: A régebbi adatok az időbeli Verziózású táblák adatmegőrzési házirend kezelése |} A Microsoft Docs
description: Ismerje meg, hogy az Ön kezében előzményadatok historikus megőrzési szabályzat használata.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: f339cadc63d5e5cd934d07e7b0fffc6342ca04c7
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47159098"
---
# <a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>A régebbi adatok az időbeli Verziózású táblák adatmegőrzési házirend kezelése
Historikus táblák növelheti több adatbázis mérete, mint normál táblákat, különösen akkor, ha a korábbi adatok alapján egy hosszabb ideig megőrzi. Ezért a előzményadatok adatmegőrzési fontos szempont a megtervezéséhez és kezeléséhez minden historikus tábla életciklusának. Az Azure SQL Database időbeli verziózású táblák könnyen használható megőrzési mechanizmus, amely segít a feladatok kapható.

Historikus megőrzési lehet egyes táblákat szintjén állítható be, amely lehetővé teszi a felhasználóknak rugalmas elévülési szabályzatok. Egyszerű historikus megőrzési alkalmazása: be kell állítania tábla létrehozása vagy a séma módosítása csak az egyik paraméter szükséges.

Miután a megőrzési házirend határozza meg, az Azure SQL Database indítja el rendszeresen ellenőrzi, hogy vannak-e korábbi sorok kapcsolatos adatok automatikus karbantartása. Egyező sorok azonosítása és azok eltávolítása az előzménytáblából fel a rendszer transzparens módon, a háttérfeladatot, amely ütemezett, és futtassa a rendszer. Az előzmények táblasorokat kora feltételt a rendszer ellenőrzi a SYSTEM_TIME időszak végét jelölő oszlop alapján. Megőrzési idő, például értéke hat hónapban, karbantartási jogosult tábla sorainak megfelelnek-e a következő feltételt:

````
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
````

Az előző példában azt feltételezi, hogy **ValidTo** SYSTEM_TIME időszak végének oszlop felel meg.

## <a name="how-to-configure-retention-policy"></a>Hogyan konfigurálható adatmegőrzési házirend?
Mielőtt egy historikus tábla adatmegőrzési állít be, először ellenőrizze engedélyezve van-e korábbi historikus megőrzési *az adatbázis szintjén*.

````
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
````

Adatbázis-jelző **is_temporal_history_retention_enabled** alapértelmezés szerint be van állítva, de a felhasználók módosíthatják az ALTER DATABASE utasítással. Azt is automatikusan beállítása után OFF [időponthoz kötött visszaállítás](sql-database-recovery-using-backups.md) műveletet. Ahhoz, hogy az adatbázis historikus megőrzési tisztítását, hajtsa végre a következő utasítást:

````
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
````

> [!IMPORTANT]
> Konfigurálhatja az időbeli verziózású táblák még akkor is, ha a megőrzési **is_temporal_history_retention_enabled** ki van KAPCSOLVA, de az automatikus tisztítás elavult sorok nem aktiválódik, ebben az esetben.
> 
> 

A infinite paraméter megadásával tábla létrehozása során van konfigurálva adatmegőrzési:

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

Az Azure SQL Database lehetővé teszi, hogy különböző mértékegységét használatával adja meg a megőrzési időtartam: nap, hét, hónap és év. Ha infinite, a rendszer végtelen adatmegőrzési feltételezi. VÉGTELEN kulcsszó explicit módon is használható.

Bizonyos esetekben érdemes lehet megőrzés konfigurálása után a tábla létrehozásához, vagy módosíthatja a korábban beállított értékét. Ebben az esetben használja az ALTER TABLE utasítást:

````
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
````

> [!IMPORTANT]
> A system_versioning OFF *nem őrzi meg a* megőrzési időszak értéke. A system_versioning ON értékre állítása nélkül infinite megadott explicit módon eredményez a végtelen adatmegőrzési idővel.
> 
> 

Tekintse át a megtartási házirend aktuális állapotát, használja a következő lekérdezést, amely csatlakoztatja az adatbázis szintjén az egyes táblák adatmegőrzési időtartamairól historikus megőrzési engedélyezésre jelző:

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
A karbantartási művelet függ a előzménytábla index elrendezését. Fontos, hogy figyelje meg, hogy *csak egy fürtözött index, (B-fára vagy oszlopcentrikus) előzmények táblák konfigurált véges megőrzési házirend*. A véges megőrzési időszak összes historikus tábla telepítésével kapcsolatos elavult adatokat fölösleges háttérfeladat kell létrehozni.
Karbantartása logikai sortárindex (B-fa) fürtözött index számára törli az elavult sor kisebb adattömbökben (legfeljebb 10 ezer) minimálisra csökkentik az adatbázis naplója és i/o-alrendszer nyomás. Bár a tisztítás logika szükséges B-fa indexet, a régebbi, mint a megőrzési időtartam nem garantálható nyitóbeszélgetést sorok törlések sorrendje használja. Ezért *nem lépnek minden olyan függőséget az alkalmazások karbantartása ahhoz a*.

A karbantartási feladat számára a fürtözött oszlopcentrikus eltávolítja a teljes [csoportok sor](https://msdn.microsoft.com/library/gg492088.aspx) egyszerre (általában tartalmaz 1 millió sor minden), vagyis nagyon hatékony, különösen akkor, ha előzményadatok magas ütemben jön létre.

![Fürtözött oszlopcentrikus megőrzése](./media/sql-database-temporal-tables-retention-policy/cciretention.png)

Kiváló az adattömörítés és a hatékony megőrzési karbantartása teszi fürtözött oszlopcentrikus index forgatókönyvekhez tökéletes választás, ha a gyors munkaterhelése nagy mennyiségű korábbi adatok alapján. A minta akkor jellemző, nagy igényű [tranzakciós feldolgozást számítási feladatokat, amelyek használják az időbeli verziózású táblák](https://msdn.microsoft.com/library/mt631669.aspx) a change tracking és naplózás, trendelemzés vagy IoT-adatok betöltése céljából.

## <a name="index-considerations"></a>Index kapcsolatos szempontok
A karbantartási feladat sortárindex fürtözött indexszel rendelkező táblák többmezős indexelésre van szükség a kezdéshez a SYSTEM_TIME időszak végének megfelelő oszlopban. Ha ilyen index nem létezik, nem konfigurálhatja a véges megőrzési időszak:

*Msg 13765, szint 16, az állapot 1 <br> </br> véges megőrzési időszak beállítása sikertelen a rendszerverzióval ellátott historikus táblán "temporalstagetestdb.dbo.WebsiteUserInfo", mert a előzménytábla " temporalstagetestdb.dbo.WebsiteUserInfoHistory "nem tartalmazza a szükséges fürtözött indexet. Érdemes lehet létrehozni a fürtözött oszlopcentrikus vagy B-fa indexet kezdve az oszlopot, amely végének megfelelő SYSTEM_TIME időszak, az előzménytáblán.*

Fontos, és figyelje meg, hogy az Azure SQL Database által már létrehozott alapértelmezett előzménytábla fürtözött index, amely megfelelő adatmegőrzési házirend. Ha megpróbálja eltávolítani a véges megőrzési időszak tartalmazó tábla indexe, a művelet meghiúsul, a következő hibával:

*Msg 13766, szint 16, az állapot 1 <br> </br> "WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory" fürtözött index nem dobható el, mert elavult adatok automatikus tisztítására használja. Érdemes lehet a history_retention_period elemet infinite értékre a megfelelő rendszerverzióval ellátott historikus táblán, ha el kell vetnie ezt az indexet.*

A karbantartást a fürtözött oszlopcentrikus indexet az optimális működik, ha a növekvő sorrendben (rendezett időtartamoszlop végén), amely mindig a helyzet akkor, ha a előzménytábla fel van töltve, kizárólag a SYSTEM_VERSIONIOING mechanizmus az előzményadatok sorok egészül ki. Az előzménytábla sorait nem időtartamoszlop (ami előfordulhat, ha át meglévő előzményadatok) végén szerint vannak rendezve, ha célszerű újra létrehozni a fürtözött oszlopcentrikus index B-fa sortárindex rendelt megfelelően, optimális elérése felett teljesítmény.

Kerülje a az előzménytáblában a véges megőrzési időszak a fürtözött oszloptárindex újraépítését, mivel előfordulhat, hogy módosítani, a sorcsoportok természetesen a rendszerverzió művelet okozta a rendezése. Építse újra az előzménytábla fürtözött oszlopcentrikus indexe van szüksége, ha ehhez hozza létre újra megfelelő B-fa indexet, megőrzi az adatok rendszeres karbantartása szükséges naplóbájtot rendezése felett. Ugyanezzel a módszerrel kell tenni, ha meglévő előzménytábla fürtözött oszlopindex garantált rendelés nélkül hoz létre historikus tábla:

````
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
````

Az előzménytábla a fürtözött oszlopcentrikus indexszel rendelkező véges megőrzési időszak van konfigurálva, amikor az adott táblához nem hozható létre a további nem fürtözött B-fa indexek:

````
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
````

A fenti utasítás végrehajtása sikertelen a következő hibával:

*Msg 13772, szint 16, az állapot 1 <br> </br> nem hozható létre nem fürtözött index historikus előzménytábla "WebsiteUserInfoHistory" mivel a véges megőrzési időszak és a fürtözött oszlopcentrikus indexszel rendelkezik.*

## <a name="querying-tables-with-retention-policy"></a>Adatmegőrzési házirend rendelkező táblák lekérdezése
A historikus tábla összes lekérdezés automatikusan szűrje ki a megfelelő elkerülése érdekében előre nem látható, az inkonzisztens eredmény elavult sorok törölhető a karbantartási feladat, mivel a véges megőrzési házirend korábbi sorok *időben és a tetszőleges bármikor rendelés*.

Az alábbi képen látható a lekérdezésterv egy egyszerű lekérdezés:

````
SELECT * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME ALL;
````

A lekérdezésterv kiegészítő szűrőt alkalmazza a period oszlopot (ValidTo) végéig (kiemelve) előzménytáblán fürtözött Index vizsgálata operátort tartalmaz. Ez a példa feltételezi, hogy egy HÓNAPOS megőrzési időszak WebsiteUserInfo táblán lett beállítva.

![Adatmegőrzési lekérdezési szűrő](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

Ha előzménytábla közvetlenül lekérdezni, láthatja, amelyen régebbi, mint a megadott megőrzési időtartama alatt, de bármilyen garancia nélkül megismételhető lekérdezés eredményeinek. Az alábbi képen látható lekérdezés végrehajtási terv a lekérdezés az előzménytáblában további szűrők nélkül:

![Előzmények nélküli megőrzési szűrő lekérdezése](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

Ne támaszkodjon kizárólag az üzleti logikára olvasása előzménytábla megőrzési időtartamon túl, inkonzisztens vagy váratlan eredményeket kaphat. Azt javasoljuk, hogy használhatja historikus lekérdezések FOR SYSTEM_TIME záradék historikus táblák az adatelemzés számára.

## <a name="point-in-time-restore-considerations"></a>Időponthoz idő visszaállítási kapcsolatos szempontok
Amikor szerint új adatbázist hoz létre [meglévő adatbázis visszaállítása adott időpontra való időben](sql-database-recovery-using-backups.md), historikus megőrzési le van tiltva, az adatbázis szintjén van. (**is_temporal_history_retention_enabled** jelző OFF értékre állítva). Ez a funkció lehetővé teszi vizsgálja meg az összes korábbi sor változónevét, nem kell, hogy a rendszer eltávolítja az elavult sort a lekérdezhetők megismerése előtt. Használhatja azt, hogy *vizsgálja meg a konfigurált megőrzési időszakot meghaladóan előzményadatok*.

Tegyük fel, hogy egy historikus tábla rendelkezik-e a megadott időszak egy hónapban megőrzési. Ha az adatbázis készült prémium szintű szolgáltatási rétegben, lenne az adatbázis állapotát az adatbázis-másolat létrehozása másolatot vissza az elmúlt 35 napon belül. Amely hatékonyan lehetővé teszi korábbi sorokat, amelyek akár 65 napnál régebbi a előzménytábla közvetlen lekérdezésével elemzése.

Ha szeretné aktiválni a historikus megőrzési tisztítását, futtassa a pont után a következő Transact-SQL utasítást időponthoz kötött visszaállítás:

````
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
````

## <a name="next-steps"></a>További lépések
Historikus táblák használata az alkalmazások a tudnivalókért tekintse meg [első lépései az Azure SQL Database időbeli Verziózású táblák](sql-database-temporal-tables.md).

Látogasson el a Channel 9 felvesszük Önnel egy [valódi ügyfelek historikus megvalósítási sikertörténete](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) , és tekintse meg a [historikus bemutató élő](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

Historikus táblák kapcsolatos részletes információkért tekintse át a [MSDN-dokumentáció](https://msdn.microsoft.com/library/dn935015.aspx).

