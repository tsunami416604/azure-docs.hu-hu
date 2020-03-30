---
title: Jelentés készítés kibővített felhőalapú adatbázisok között
description: rugalmas lekérdezések beállítása vízszintes partíciókon
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 01/03/2019
ms.openlocfilehash: 79abaade22fc107fa4c848607ff48232eeeb58ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823761"
---
# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Jelentéskészítés kibővített felhőalapú adatbázisok között (előzetes verzió)

![Lekérdezés szilánkok között][1]

Szilánkos adatbázisok sorokat osztanak el egy kibővített adatrétegen. A séma megegyezik az összes részt vevő adatbázis, más néven horizontális particionálás. Rugalmas lekérdezés használatával jelentéseket hozhat létre, amelyek egy szilánkos adatbázis összes adatbázisára kiterjedő.

Rövid kezdés: [Jelentés kicsinyített felhőalapú adatbázisokban.](sql-database-elastic-query-getting-started.md)

A nem szilánkos adatbázisok, [lásd: Lekérdezés felhőalapú adatbázisok különböző sémákkal.](sql-database-elastic-query-vertical-partitioning.md)

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy shard térkép et a rugalmas adatbázis-ügyfélkódtár használatával. lásd: [Shard térképkezelés](sql-database-elastic-scale-shard-map-management.md). Vagy használja a mintaalkalmazást az [Első lépések a rugalmas adatbázis-eszközökkel](sql-database-elastic-scale-get-started.md)című részben.
* Másik lehetőségként olvassa el [A meglévő adatbázisok áttelepítése kibővített adatbázisokba (Migrate-out database) (A meglévő adatbázisok áttelepítése kicsinyített adatbázisokba) (Kicsinyített adatbázisokba) témakört.](sql-database-elastic-convert-to-use-elastic-tools.md)
* A felhasználónak rendelkeznie kell alter minden külső adatforrás engedéllyel. Ez az engedély az ALTER DATABASE engedély része.
* Alter Bármely külső adatforrás-engedély szükséges az alapul szolgáló adatforrásra való hivatkozáshoz.

## <a name="overview"></a>Áttekintés

Ezek az utasítások a rugalmas lekérdezési adatbázisban a szilánkos adatréteg metaadat-ábrázolását hozzák létre.

1. [FŐKULCS LÉTREHOZÁSA](https://msdn.microsoft.com/library/ms174382.aspx)
2. [ADATBÁZIS HATÓKÖRE HITELESÍTŐ ADATOK LÉTREHOZÁSA](https://msdn.microsoft.com/library/mt270260.aspx)
3. [KÜLSŐ ADATFORRÁS LÉTREHOZÁSA](https://msdn.microsoft.com/library/dn935022.aspx)
4. [KÜLSŐ TÁBLA LÉTREHOZÁSA](https://msdn.microsoft.com/library/dn935021.aspx)

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1.1 Adatbázis hatókörrel létrehozott főkulcsának és hitelesítő adatainak létrehozása

A hitelesítő adatokat a rugalmas lekérdezés használja a távoli adatbázisokhoz való csatlakozáshoz.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Győződjön meg arról, hogy a *"\<felhasználónév\>"* nem tartalmaz *"kiszolgálónév"\@* utótagot.

## <a name="12-create-external-data-sources"></a>1.2 Külső adatforrások létrehozása

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

Az aktuális külső adatforrások listájának beolvasása:

    select * from sys.external_data_sources;

A külső adatforrás a shard térképre hivatkozik. Ezután egy rugalmas lekérdezés a külső adatforrás t és az alapul szolgáló shard leképezést használja az adatrétegben részt vevő adatbázisok számbavételéhez.
Ugyanazokat a hitelesítő adatokat használják a shard térkép olvasásához és a szegmenseken lévő adatok eléréséhez egy rugalmas lekérdezés feldolgozása során.

## <a name="13-create-external-tables"></a>1.3 Külső táblázatok létrehozása

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

A külső táblák listájának beolvasása az aktuális adatbázisból:

    SELECT * from sys.external_tables;

Külső táblák eldobása:

    DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]

### <a name="remarks"></a>Megjegyzések

Az\_ADATFORRÁS záradék a külső táblához használt külső adatforrást (shard-leképezést) határozza meg.  

A SCHEMA\_NAME\_és AZ OBJECT NAME záradékok a külső tábladefiníciót egy másik séma táblájához kell leképezni. Ha nincs megadva, a távoli objektum sémája "dbo"-nak lesz, és a neve megegyezik a definiált külső táblanévvel. Ez akkor hasznos, ha a távoli tábla neve már elvan készítve abban az adatbázisban, amelyen létre szeretné hozni a külső táblát. Például meg szeretne határozni egy külső táblát, hogy összesített nézetet kapjon a katalógusnézetekről vagy a kibővített adatréteg DRV-jeiről. Mivel a katalógusnézetek és a DMV-k már léteznek helyileg, nem használhatja a nevüket a külső tábladefinícióhoz. Ehelyett használjon másik nevet, és használja a katalógusnézet vagy a DMV\_nevét a\_SCHEMA NAME és/vagy OBJECT NAME záradékokban. (Lásd az alábbi példát.)

A DISTRIBUTION záradék a táblához használt adateloszlást határozza meg. A lekérdezésfeldolgozó a DISTRIBUTION záradékban megadott információkat használja a leghatékonyabb lekérdezési tervek létrehozásához.

1. **A SHARDED** azt jelenti, hogy az adatok horizontálisan particionáltak az adatbázisok között. Az adatelosztás particionálási kulcsa a **<sharding_column_name>** paraméter.
2. **A REPLIKÁLt** azt jelenti, hogy a tábla azonos példányai minden adatbázisban szerepelnek. Az Ön felelőssége annak biztosítása, hogy a replikák azonosak legyenek az adatbázisokban.
3. **A\_ROUND ROBIN** azt jelenti, hogy a tábla horizontálisan particionált egy alkalmazásfüggő terjesztési módszerrel.

**Adatréteg-hivatkozás**: A külső DDL tábla külső adatforrásra hivatkozik. A külső adatforrás egy shard-leképezést határoz meg, amely a külső tábla számára biztosítja az adatrétegben lévő összes adatbázis megkereséséhez szükséges információkat.

### <a name="security-considerations"></a>Biztonsági szempontok

A külső táblához hozzáféréssel rendelkező felhasználók automatikusan hozzáférnek az alapul szolgáló távoli táblákhoz a külső adatforrás-definícióban megadott hitelesítő adatok alatt. Kerülje a jogosultságok nem kívánt kiterjesztését a külső adatforrás hitelesítő adataival. Használja a GRANT vagy revoke egy külső tábla, mintha egy normál tábla.  

Miután definiálta a külső adatforrást és a külső táblákat, most már használhatja a teljes T-SQL-t a külső táblákon.

## <a name="example-querying-horizontal-partitioned-databases"></a>Példa: horizontális particionált adatbázisok lekérdezése

A következő lekérdezés háromirányú illesztést hajt végre a raktárak, a rendelések és a rendelési sorok között, és több aggregátumot és egy szelektív szűrőt használ. Azt feltételezi, hogy (1) horizontális particionálás (horizontális) és (2) a raktárak, rendelések és rendelési sorok horizontálisan a raktári azonosító oszlop, és hogy a rugalmas lekérdezés együtt keresse meg az illesztések a szilánkok és a feldolgozás a lekérdezés drága része a szilánkok Párhuzamos.

```sql
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
```

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>Tárolt eljárás távoli T-SQL végrehajtáshoz: sp\_execute_remote

Rugalmas lekérdezés is bevezet egy tárolt eljárást, amely közvetlen hozzáférést biztosít a szegmensek. A tárolt eljárás [neve\_ \_SP execute remote](https://msdn.microsoft.com/library/mt703714) és használható távoli tárolt eljárások vagy T-SQL kódot a távoli adatbázisok. A következő paramétereket veszi igénybe:

* Adatforrás neve (nvarchar): Az RDBMS típusú külső adatforrás neve.
* Lekérdezés (nvarchar): Az egyes szegmenseken végrehajtandó T-SQL-lekérdezés.
* Paraméterdeklaráció (nvarchar) - nem kötelező: A Query paraméterben használt paraméterek (például sp_executesql) adattípus-definícióival rendelkező karakterlánc.
* Paraméterérték-lista - nem kötelező: a paraméterértékek vesszővel tagolt listája (például sp_executesql).

Az\_sp\_execute remote a meghívási paraméterekben megadott külső adatforrást használja a megadott T-SQL utasítás távoli adatbázisokon történő végrehajtásához. A külső adatforrás hitelesítő adatait használja a shardmap-kezelő adatbázishoz és a távoli adatbázisokhoz való csatlakozáshoz.  

Példa:

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse'
```

## <a name="connectivity-for-tools"></a>Eszközök csatlakoztatása

Az alkalmazás, az üzletiintelligencia- és adatintegrációs eszközök a külső tábladefiníciókkal való csatlakoztatásához használjon rendszeres SQL Server-kapcsolati karakterláncokat. Győződjön meg arról, hogy az SQL Server támogatja az eszköz adatforrásaként. Ezután hivatkozzon a rugalmas lekérdezési adatbázisra, mint bármely más SQL Server-adatbázis, amely az eszközhöz csatlakozik, és használja az eszköz vagy alkalmazás külső tábláit, mintha azok helyi táblák lennének.

## <a name="best-practices"></a>Ajánlott eljárások

* Győződjön meg arról, hogy a rugalmas lekérdezésvégpont-adatbázis hozzáférést kapott a shardmap adatbázishoz és az ÖSSZES szegmenshez az SQL DB tűzfalakon keresztül.  
* A külső tábla által meghatározott adatelosztás ellenőrzése vagy érvényesítése. Ha a tényleges adatelosztás eltér a tábladefinícióban megadott eloszlástól, a lekérdezések nem várt eredményeket eredményezhetnek.
* Rugalmas lekérdezés jelenleg nem hajt végre shard megszüntetése, ha a szilánkok a szilánkok kulcs on-ra, lehetővé tenné, hogy biztonságosan kizárja bizonyos szegmensek feldolgozása.
* Rugalmas lekérdezés működik a legjobban a lekérdezések, ahol a legtöbb számítás végezhető el a szegmensek. Általában a legjobb lekérdezési teljesítményt szelektív szűrő predikátumok, amelyek kiértékelhetők a szilánkok, vagy csatlakozik a particionálási kulcsok, amelyek egy partíció-igazított módon az összes szegmensek. Más lekérdezési minták előfordulhat, hogy nagy mennyiségű adatot kell betöltenie a szegmensekből a főcsomópontra, és gyengén teljesíthetnek

## <a name="next-steps"></a>További lépések

* A rugalmas lekérdezés áttekintését a [Rugalmas lekérdezés áttekintése című témakörben találja.](sql-database-elastic-query-overview.md)
* Függőleges particionálási oktatóanyag: [Első lépések adatbázisközi lekérdezéssel (függőleges particionálás)](sql-database-elastic-query-getting-started-vertical.md).
* A vertikálisan particionált adatok szintaxisát és mintalekérdezéseit a [Vertikálisan particionált adatok lekérdezése) témakörben találja.](sql-database-elastic-query-vertical-partitioning.md)
* Horizontális particionálási (horizontális) oktatóanyag: [A rugalmas lekérdezés horizontális particionálás (horizontális sáfálás) című témakörben található.](sql-database-elastic-query-getting-started.md)
* [Lásd:\_ \_sp távoli végrehajtása](https://msdn.microsoft.com/library/mt703714) egy tárolt eljárás, amely végrehajtja a Transact-SQL utasításegyetlen távoli Azure SQL-adatbázis vagy egy horizontális particionálási séma szegmensként szolgáló adatbázisok készlete.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
