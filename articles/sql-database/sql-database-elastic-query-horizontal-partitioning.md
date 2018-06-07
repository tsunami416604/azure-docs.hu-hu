---
title: Kiterjesztett felhő az adatbázisok közötti Reporting |} Microsoft Docs
description: vízszintes partíciók keresztül rugalmas lekérdezések beállítása
services: sql-database
documentationcenter: ''
manager: craigg
author: MladjoA
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: mlandzic
ms.openlocfilehash: fcb498542a496e4a887c825808642d3f586ef1d9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646354"
---
# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Jelentéskészítési közötti kiterjesztett felhő (előzetes verzió)
![Átfogó szilánkok lekérdezése][1]

Horizontálisan skálázott adatbázisok sorok szét méretezett kimenő adatok réteg. A séma összes részt vevő adatbázishoz, más néven vízszintes particionálás megegyezik. Rugalmas lekérdezéssel, jelentéseket is létrehozhat, amelyek több összes adatbázis szilánkos-adatbázisban.

Tekintse meg a gyors üzembe helyezési [kiterjesztett felhő az adatbázisok közötti Reporting](sql-database-elastic-query-getting-started.md).

Nem szilánkos adatbázisok, lásd: [eltérő sémával felhő az adatbázisok közötti lekérdezés](sql-database-elastic-query-vertical-partitioning.md). 

## <a name="prerequisites"></a>Előfeltételek
* A rugalmas adatbázis ügyféloldali kódtár segítségével shard térkép létrehozásához. Lásd: [Shard térkép felügyeleti](sql-database-elastic-scale-shard-map-management.md). A mintaalkalmazás vagy [Ismerkedés a rugalmas adatbáziseszközöket](sql-database-elastic-scale-get-started.md).
* Azt is megteheti, lásd: [áttelepíteni meglévő adatbázisok kiterjesztett adatbázisok](sql-database-elastic-convert-to-use-elastic-tools.md).
* A felhasználónak rendelkeznie kell ALTER ANY külső ADATFORRÁS engedéllyel. Ez az engedély megtalálható az ALTER DATABASE engedéllyel.
* Az ALTER ANY külső ADATFORRÁS engedélyekre van szükség az alapul szolgáló adatforrásban hivatkozik.

## <a name="overview"></a>Áttekintés
Ezekre az utasításokra a szilánkos adatrétegbeli metaadatok ábrázolását hozza létre a Rugalmas lekérdezési adatbázisban. 

1. [A FŐKULCS LÉTREHOZÁSA](https://msdn.microsoft.com/library/ms174382.aspx)
2. [HOZZON LÉTRE AZ ADATBÁZISHOZ KÖTŐDŐ HITELESÍTŐ ADATOK](https://msdn.microsoft.com/library/mt270260.aspx)
3. [KÜLSŐ ADATFORRÁS LÉTREHOZÁSA](https://msdn.microsoft.com/library/dn935022.aspx)
4. [KÜLSŐ TÁBLA LÉTREHOZÁSA](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1.1 adatbázis hatókörű főkulcs és hitelesítő adatainak létrehozása
A hitelesítő adatok segítségével a rugalmas lekérdezés csatlakozzon a távoli adatbázishoz.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Győződjön meg arról, hogy a *"\<felhasználónév\>"* nem vonatkozik a *"@servername"* utótag. 
> 
> 

## <a name="12-create-external-data-sources"></a>1.2 külső adatforrás létrehozása
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

Aktuális külső adatforrások listájának beolvasása: 

    select * from sys.external_data_sources; 

A külső adatforráshoz a shard térképre hivatkozik. Egy rugalmas lekérdezés segítségével a külső adatforrás és a mögöttes shard térkép számba venni az adatbázisok, amelyek az adatréteg. A hitelesítő adatokkal szolgálnak a shard térkép olvasása és rugalmas lekérdezés feldolgozása közben a szilánkok lévő adatok eléréséhez. 

## <a name="13-create-external-tables"></a>1.3 külső táblák létrehozása
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

A jelenlegi adatbázisból külső táblák listájának beolvasása: 

    SELECT * from sys.external_tables; 

A külső táblák eltávolítása:

    DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]

### <a name="remarks"></a>Megjegyzések
Az adatok\_forrás záradékot a külső tábla használt külső adatforrás (szilánkok térképre) határozza meg.  

A SÉMA\_nevét és objektum\_neve záradékot a külső tábla definíciójának leképezése egy tábla egy másik séma. Ha nincs megadva, a távoli objektum sémája feltételezett, hogy a "dbo" kell, és a neve megegyezik a külső tábla neve a múltbeli érték. Ez akkor hasznos, ha a távoli tábla neve már használatban van az adatbázis hol szeretne létrehozni a külső tábla. Például meg szeretné határozni a katalógusnézetekre összesített megtekintheti a külső táblát, vagy dinamikus felügyeleti nézetek méretezett kimenő adatait a réteg. Mivel katalógusnézetekre és dinamikus felügyeleti nézetek létezik helyileg, a külső tábla definíciójának nevük nem használható. Ehelyett használjon egy másik nevet, és használja a katalógusnézet vagy a DMV a séma neve\_név és/vagy az objektum\_záradékok neve. (Lásd az alábbi példában). 

A TERJESZTÉSI záradék határozza meg az ehhez a táblához használt adatok terjesztését. A lekérdezésfeldolgozó használja a leghatékonyabb lekérdezésterveket létrehozásához a TERJESZTÉSI záradékban található információk.  

1. **Horizontálisan SKÁLÁZOTT** azt jelenti, hogy adatok vízszintesen particionálása az adatbázisok között. A particionáló az adatok terjesztési kulcsa a **< sharding_column_name >** paraméter.
2. **REPLIKÁLT** azt jelenti, hogy az egyes adatbázisok jelen-e a tábla azonos másolatait. Feladata a győződjön meg róla, hogy a replikákat megegyeznek az adatbázisok között.
3. **KEREK\_MULTIPLEXELÉS** azt jelenti, hogy a táblázat vízszintes particionálva van egy alkalmazás-függő elosztási módszer használatával. 

**Adatok réteg hivatkozás**: A külső tábla DDL hivatkozik egy külső adatforrásból. A külső adatforrás határozza meg, amely a külső tábla összes adatbázist talált az adatréteg szükséges információkat biztosít a shard térképre. 

### <a name="security-considerations"></a>Biztonsági szempontok
A külső tábla hozzáféréssel rendelkező felhasználók automatikusan hozzáférhetnek az alapul szolgáló távoli táblák a külső adatforrás-definíciót a megadott hitelesítő adatok alapján. Külső adatforrás hitelesítő adatait a nemkívánatos jogok kiterjesztésének elkerülése érdekében. Használja GRANT vagy VISSZAVONNI egy külső tábla ugyanúgy, mintha az lenne normál táblákhoz.  

Miután meghatározta a külső adatforrást és a külső táblák, a külső táblákon végrehajtott most már használhatja a teljes T-SQL.

## <a name="example-querying-horizontal-partitioned-databases"></a>Példa: vízszintes particionált adatbázisok lekérdezése
A következő lekérdezés során, a rendeléseket és a sorok háromutas illesztést végez, és több összesítések és szelektív szűrőt használja. Azt feltételezi, hogy (1) vízszintes particionálási (horizontális skálázási) és (2), hogy során, a rendeléseket és a sorok szilánkos az adatraktár azonosító oszlop szerint, és, hogy a rugalmas lekérdezés közös elhelyezése a szilánkok az illesztések, és feldolgozni a lekérdezést a párhuzamos szilánkok költséges részét. 

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
Rugalmas lekérdezési is vezet be a tárolt eljárás, amely a szilánkok közvetlen hozzáférést biztosít. A tárolt eljárás neve [sp\_hajtható végre \_távoli](https://msdn.microsoft.com/library/mt703714) és a távoli adatbázis távoli tárolt eljárások, illetve a T-SQL kód végrehajtásához használható. Ez a következő paramétereket fogadja: 

* Adatforrás neve (nvarchar): a külső adatforrás RDBMS típusú nevét. 
* Lekérdezés (nvarchar): A T-SQL-lekérdezést minden shard hajtható végre. 
* Paraméterdeklarációhoz (nvarchar) – nem kötelező: adatok típusdefiníciók a paraméterek (például sp_executesql) a következő lekérdezésparaméter használt karakterlánc. 
* Paraméter értéklistát - választható: paraméterértékek (például sp_executesql) vesszővel tagolt listája.

Az sp\_hajtható végre\_távoli a meghívás paramétereit szerepel a külső adatforrás használja az adott T-SQL-utasítás végrehajtása a távoli adatbázis. A külső adatforrás hitelesítő adatait használja a shardmap manager-adatbázis és a távoli adatbázisokhoz való csatlakozáshoz.  

Példa: 

    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 

## <a name="connectivity-for-tools"></a>Eszközök kapcsolattal
Használja a rendszeres SQL Server kapcsolati karakterláncokat az alkalmazás kapcsolódni a BI és az integráció eszközök a külső tábla definíciók az adatbázisba. Győződjön meg arról, hogy az SQL Server támogatja-e az eszköz adatforrásként. Majd hivatkozás a Rugalmas lekérdezési adatbázis, mint bármely más SQL Server-adatbázis az eszközt, és a külső táblák használata keresztül kapcsolódik az eszköz vagy alkalmazás helyi táblákkal, mintha. 

## <a name="best-practices"></a>Ajánlott eljárások
* Győződjön meg arról, hogy a Rugalmas lekérdezési végpont adatbázis megkapta-e hozzáféréssel a shardmap adatbázis és az összes szilánkok az SQL-adatbázis a tűzfalon keresztül.  
* Ellenőrzi, vagy az adatok terjesztési határozzák meg a külső tábla kényszerítéséhez. Ha a tényleges adatok terjesztési eltér a tábla definíciójában megadott terjesztési, a lekérdezések előfordulhat, hogy eredményt várt. 
* Rugalmas lekérdezés jelenleg nem végeznek eltávolítási szilánkok predikátumok keresztül a horizontális kulcs lehetővé tenné, bizonyos szilánkok biztonságosan kizárását feldolgozása.
* Rugalmas lekérdezési működik a legjobban az lekérdezések ahol a szilánkok a számítási többsége végezhető. Általában kap a szelektív szűrő predikátumok kiértékelhető a legjobb lekérdezési teljesítményt a szilánkok vagy illesztések keresztül a particionálási kulcsokhoz, az összes szilánkok partíciójához úgy végezheti el. Más lekérdezési mintáinak nagy mennyiségű adatok betöltése a szilánkok az átjárócsomóponthoz kell, és elvégezheti a kapcsolódó rosszul

## <a name="next-steps"></a>További lépések

* Rugalmas lekérdezési áttekintését lásd: [rugalmas lekérdezési áttekintése](sql-database-elastic-query-overview.md).
* Függőleges particionálási oktatóanyagért lásd a [első lépések (a vertikális particionálás) közötti adatbázis-lekérdezés](sql-database-elastic-query-getting-started-vertical.md).
* A szintaxis és a minta lekérdezések függőleges particionált adatok, lásd: [adatok lekérdezése függőleges particionálva)](sql-database-elastic-query-vertical-partitioning.md)
* Vízszintes particionálás (horizontális) oktatóanyagért lásd a [Ismerkedés a vízszintes particionálására (horizontális) rugalmas lekérdezési](sql-database-elastic-query-getting-started.md).
* Lásd: [sp\_hajtható végre \_távoli](https://msdn.microsoft.com/library/mt703714) tárolt eljárás, amely végrehajtja a Transact-SQL-utasítás egy egyetlen távoli Azure SQL Database vagy az adatbázisok egy vízszintes particionálási sémát a szilánkok szolgál.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
