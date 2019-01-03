---
title: Jelentéskészítés több kiterjesztett felhőalapú adatbázisban |} A Microsoft Docs
description: Hogyan állítható be rugalmas lekérdezések horizontális partíciók keresztül
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 02942cafe6e1532a6829ad7a6761b825739a1e85
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53597970"
---
# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Jelentéskészítés több kiterjesztett felhőalapú adatbázisban (előzetes verzió)
![Szegmensek közötti lekérdezési][1]

Szilánkokra osztott adatbázisok sorok szét a bővítő adatok szint. A séma megegyezik a programban részt vevő adatbázisokat, más néven horizontális particionálást. Az a rugalmas lekérdezés használatával, jelentéseket is létrehozhat, amelyek szilánkokra osztott adatbázis összes adatbázishoz.

A rövid útmutatóban talál [jelentéskészítés több kiterjesztett felhőalapú adatbázisban](sql-database-elastic-query-getting-started.md).

Horizontálisan skálázott adatbázisok esetén, lásd: [lekérdezés több felhőalapú adatbázisban eltérő sémákkal](sql-database-elastic-query-vertical-partitioning.md). 

## <a name="prerequisites"></a>Előfeltételek
* Hozzon létre egy horizontális skálázási térképet, az elastic database ügyfélkódtár használatával. Lásd: [Szilánkleképezés-kezelés](sql-database-elastic-scale-shard-map-management.md). Vagy használja a mintaalkalmazás a [Ismerkedés az elastic database-eszközök](sql-database-elastic-scale-get-started.md).
* Másik megoldásként nézze [meglévő adatbázisok Migrálása, horizontálisan felskálázott adatbázisok](sql-database-elastic-convert-to-use-elastic-tools.md).
* A felhasználónak rendelkeznie kell ALTER ANY EXTERNAL DATA SOURCE engedéllyel. Ez az engedély megtalálható az ALTER DATABASE engedéllyel.
* Az ALTER ANY EXTERNAL DATA SOURCE engedélyekre van szükség, tekintse meg az alapul szolgáló adatforrás.

## <a name="overview"></a>Áttekintés
Ezek az utasítások a horizontálisan skálázott adatok szint metaadatok reprezentációja hozhat létre a rugalmas lekérdezés adatbázisban. 

1. [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx)
2. [HOZZON LÉTRE AZ ADATBÁZISHOZ KÖTŐDŐ HITELESÍTŐ ADATOK](https://msdn.microsoft.com/library/mt270260.aspx)
3. [KÜLSŐ ADATFORRÁS LÉTREHOZÁSA](https://msdn.microsoft.com/library/dn935022.aspx)
4. [KÜLSŐ TÁBLA LÉTREHOZÁSA](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1.1 a hatókörrel rendelkező adatbázis főkulcsának és a hitelesítő adatok létrehozása
A hitelesítő adatokat a rugalmas lekérdezés használják a távoli adatbázisokhoz való csatlakozáshoz.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Győződjön meg arról, hogy a *"\<felhasználónév\>"* nem vonatkozik a *"\@servername"* utótag. 
> 
> 

## <a name="12-create-external-data-sources"></a>1.2-es külső adatforrás létrehozása
Szintaxis:

    <External_Data_Source> ::=    
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH                                              
            (TYPE = SHARD_MAP_MANAGER,
                       LOCATION = '<fully_qualified_server_name>',
            DATABASE_NAME = ‘<shardmap_database_name>',
            CREDENTIAL = <credential_name>, 
            SHARD_MAP_NAME = ‘<shardmapname>’ 
                   ) [;] 

### <a name="example"></a>Példa
    CREATE EXTERNAL DATA SOURCE MyExtSrc 
    WITH 
    ( 
        TYPE=SHARD_MAP_MANAGER,
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ShardMapDatabase', 
        CREDENTIAL= SMMUser, 
        SHARD_MAP_NAME='ShardMap' 
    );

Külső adatforrások aktuális listájának beolvasása: 

    select * from sys.external_data_sources; 

A külső adatforrásból a szegmenstérkép hivatkozik. Az a rugalmas lekérdezés használja fel a külső adatforrást és a mögöttes szegmenstérkép számba venni az adatbázisok, amelyek az adatréteg részt. Ugyanazokat a hitelesítő adatokat használják, olvassa el a szegmenstérkép és a egy rugalmas lekérdezés feldolgozása során az adatokat a szegmensek eléréséhez. 

## <a name="13-create-external-tables"></a>1.3 a külső táblák létrehozása
Szintaxis:  

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
        ( { <column_definition> } [ ,...n ])     
        { WITH ( <sharded_external_table_options> ) }
    ) [;]  

    <sharded_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>,       
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 
      DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN

**Példa**

    CREATE EXTERNAL TABLE [dbo].[order_line]( 
         [ol_o_id] int NOT NULL, 
         [ol_d_id] tinyint NOT NULL,
         [ol_w_id] int NOT NULL, 
         [ol_number] tinyint NOT NULL, 
         [ol_i_id] int NOT NULL, 
         [ol_delivery_d] datetime NOT NULL, 
         [ol_amount] smallmoney NOT NULL, 
         [ol_supply_w_id] int NOT NULL, 
         [ol_quantity] smallint NOT NULL, 
         [ol_dist_info] char(24) NOT NULL 
    ) 

    WITH 
    ( 
        DATA_SOURCE = MyExtSrc, 
         SCHEMA_NAME = 'orders', 
         OBJECT_NAME = 'order_details', 
        DISTRIBUTION=SHARDED(ol_w_id)
    ); 

Külső táblák listájának lekérése az aktuális adatbázisban: 

    SELECT * from sys.external_tables; 

A külső táblák eltávolítása:

    DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]

### <a name="remarks"></a>Megjegyzések
Az adatok\_forrás záradék határozza meg a külső adatforrás (horizontális skálázási térképet), amely a külső tábla szolgál.  

A SÉMA\_nevét és az objektum\_neve záradékot a külső tábla definíciójának leképezése egy tábla egy másik séma. Ha nincs megadva, a távoli objektum sémája adatforrásmérete "dbo", és feltételezhető, hogy a neve úgy, hogy a definiált külső tábla neve. Ez akkor hasznos, ha a távoli tábla neve már használatban van az adatbázis, amelyre a külső tábla létrehozása. Például célszerű meghatározni a Rendszerkatalógus-nézetek egy egyesített képet kaphat a külső tábla, vagy horizontálisan adatait a DMV-kkel réteg. Rendszerkatalógus-nézetek és a dinamikus felügyeleti nézetek létezik helyileg, mivel a nevüket, a külső tábla definíciójában nem használható. Ehelyett használjon másik nevet, és használja a katalógusnézet vagy a DMV a séma neve\_nevét és/vagy az objektum\_záradékok neve. (Lásd az alábbi példát). 

A TERJESZTÉSI záradék megadja az adatok terjesztési használni erre a táblázatra vonatkozóan. A lekérdezésfeldolgozó használja a leghatékonyabb terveket hozhat létre a TERJESZTÉSI záradékban megadott információkat.  

1. **Szilánkokra osztott** azt jelenti, hogy az adatok horizontális particionálása az adatbázis között. A particionálási az adatok terjesztési kulcsa a **< sharding_column_name >** paraméter.
2. **REPLIKÁLT** azt jelenti, hogy azonos példányával a táblában található egyes adatbázisokhoz. A feladata, hogy a replikákat az adatbázisban azonos meg.
3. **ROUND\_ROBIN** azt jelenti, hogy a tábla vízszintesen particionált egy alkalmazás-függő telepítési módszer használatával. 

**Adatok réteg referencia**: A külső tábla DDL-külső adatforrásra hivatkozik. A külső adatforrás megadja egy horizontális skálázási térképet, amely a külső tábla biztosít az adatszinten lévő összes adatbázis megkereséséhez szükséges információt. 

### <a name="security-considerations"></a>Biztonsági szempontok
A külső tábla hozzáféréssel rendelkező felhasználók automatikusan hozzáférhetnek a külső adatforrás-definíciót a megadott hitelesítő adatok az alapul szolgáló távoli táblákba. Kerülje a hitelesítő adatokat a külső adatforrás keresztül nemkívánatos jogok kiterjesztése. Használata biztosítása vagy visszavonása egy külső táblát ugyanúgy, mintha egy normál táblákhoz.  

Miután meghatározta a külső adatforrásban és a külső táblák, a külső táblákon végrehajtott mostantól használhatja a teljes T-SQL.

## <a name="example-querying-horizontal-partitioned-databases"></a>Példa: vízszintesen particionált adatbázisok lekérdezése
A következő lekérdezést hajt végre a háromutas csatlakozzon, adattárházak, a megrendelések és a sorok között, és számos összesítések és a egy szelektív szűrőt használ. Azt feltételezi, hogy (1) horizontális particionálási (sharding) és (2), adattárházak, rendelések és sorok horizontálisan skálázott az adatraktár azonosító oszlop szerint, és, hogy a rugalmas lekérdezés közös elhelyezése a a szegmensek való csatlakozások, és a lekérdezés a szegmensek a költséges részének feldolgozása párhuzamos. 

    select  
         w_id as warehouse,
         o_c_id as customer,
         count(*) as cnt_orderline,
         max(ol_quantity) as max_quantity,
         avg(ol_amount) as avg_amount, 
         min(ol_delivery_d) as min_deliv_date
    from warehouse 
    join orders 
    on w_id = o_w_id
    join order_line 
    on o_id = ol_o_id and o_w_id = ol_w_id 
    where w_id > 100 and w_id < 200 
    group by w_id, o_c_id 

## <a name="stored-procedure-for-remote-t-sql-execution-spexecuteremote"></a>Tárolt eljárás távoli T-SQL-végrehajtásra: sp\_execute_remote
Rugalmas lekérdezés is vezet be, egy tárolt eljárást, amely a szegmensek közvetlen hozzáférést biztosít. A tárolt eljárás neve [sp\_végrehajtása \_távoli](https://msdn.microsoft.com/library/mt703714) és hajthat végre távoli tárolt eljárások, vagy a T-SQL-kódot a távoli adatbázisokhoz is használható. Ez a következő paramétereket fogadja: 

* Adatforrás neve (nvarchar): RDBMS típusú külső adatforrás neve. 
* Lekérdezés (nvarchar): A T-SQL lekérdezés minden egyes szegmens végrehajtását. 
* Deklarace parametru (nvarchar) – nem kötelező: A lekérdezési paramétert (például sp_executesql) használt paraméterek vonatkozó típusdefiníciókat adatokat tartalmazó karakterlánc. 
* Érték paraméterlista – nem kötelező: Paraméter értékét (például sp_executesql) vesszővel tagolt listája.

Az sp\_végrehajtása\_távoli a Meghívási paraméterek megadott külső adatforrás használja a megadott T-SQL-utasítás végrehajtása a távoli adatbázis. A külső adatforrás hitelesítő adatait a shardmap manager-adatbázis és a távoli adatbázisokhoz való csatlakozáshoz használ.  

Példa: 

    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 

## <a name="connectivity-for-tools"></a>Az eszközök kapcsolat
Az alkalmazás rendszeres SQL Server-kapcsolati karakterláncok használatával a BI-ban és integrációs eszközök a külső tábla-definíciók az adatbázisba. Győződjön meg arról, hogy az SQL Server támogatott-e az eszköz adatforrásként. Ezután hivatkozás a rugalmas lekérdezés adatbázis, mint bármely más SQL Server-adatbázis csatlakozik az eszközt, és a külső táblák használata az eszköz vagy alkalmazás úgy, mintha helyi táblákkal. 

## <a name="best-practices"></a>Ajánlott eljárások
* Győződjön meg arról, hogy a rugalmas lekérdezés végpont adatbázis adott hozzáférést az shardmap adatbázis és minden szegmensre az SQL Database-tűzfalon keresztül.  
* Érvényesítéséhez, vagy az adatok terjesztési határozzák meg a külső tábla kényszerítése. Ha a tényleges adatok disztribúció eltér a terjesztés az tábla definíciójában megadott, a lekérdezéseket eredményezhet, nem várt eredmények. 
* Rugalmas lekérdezés jelenleg nem végez megszüntetésére szilánkleképezés predikátumok keresztül a horizontális skálázási kulcs lehetővé tenné, hogy egyes szegmensek biztonságosan kizárása feldolgozása során.
* Rugalmas lekérdezés működik a legjobban a lekérdezésekhez, a számítás a legtöbb teheti a szegmensek. Általában kap a szelektív szűrő predikátumok, amelyek kiértékelése a legjobb lekérdezési teljesítmény a szegmensek vagy illesztések a particionálási kulcsok minden szegmensre illeszkednie módon elvégezhető. Más lekérdezési minták előfordulhat, hogy nagy mennyiségű adat betöltése a szegmensek az átjárócsomóponthoz kell, és előfordulhat, hogy mutattak

## <a name="next-steps"></a>További lépések

* Rugalmas lekérdezés áttekintését lásd: [rugalmas lekérdezés – áttekintés](sql-database-elastic-query-overview.md).
* Függőleges particionálási oktatóanyagért lásd: [adatbázisközi lekérdezések (vertikális partíciók) – első lépések](sql-database-elastic-query-getting-started-vertical.md).
* Függőlegesen particionált adatok szintaxis és a minta lekérdezéseket, lásd: [lekérdezése függőlegesen particionált adatok)](sql-database-elastic-query-vertical-partitioning.md)
* Horizontális particionálást (sharding) foglalkozó oktatóanyagért lásd: [rugalmas lekérdezés horizontális particionálást (sharding) – első lépések](sql-database-elastic-query-getting-started.md).
* Lásd: [sp\_végrehajtása \_távoli](https://msdn.microsoft.com/library/mt703714) egy tárolt eljárás, amely végrehajtja a Transact-SQL-utasítás egy távoli Azure SQL Database vagy adatbázisok horizontális particionálási séma kidolgozásához szegmensek szolgáló készletét.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
