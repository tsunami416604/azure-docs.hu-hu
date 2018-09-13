---
title: Lekérdezés több felhőalapú adatbázisban eltérő sémával |} A Microsoft Docs
description: adatbázisközi lekérdezések beállítása a függőleges partíció keresztül
services: sql-database
manager: craigg
author: MladjoA
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: mlandzic
ms.openlocfilehash: 29f477a5f6c8583f6224cb216356606129fedb81
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35645027"
---
# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Lekérdezés több felhőalapú adatbázisban eltérő sémákkal (előzetes verzió)
![A különböző adatbázisok táblákat lefedő lekérdezés][1]

Függőlegesen particionált adatbázisok táblák más-más részhalmazához különböző adatbázist használja. Amely azt jelenti, hogy a séma különböző különböző adatbázisokon. Például a készlet összes tábla van egy adatbázis Bár minden nyilvántartási kapcsolódó tábla egy második adatbázishoz. 

## <a name="prerequisites"></a>Előfeltételek
* A felhasználónak rendelkeznie kell ALTER ANY EXTERNAL DATA SOURCE engedéllyel. Ez az engedély megtalálható az ALTER DATABASE engedéllyel.
* Az ALTER ANY EXTERNAL DATA SOURCE engedélyekre van szükség, tekintse meg az alapul szolgáló adatforrás.

## <a name="overview"></a>Áttekintés

> [!NOTE]
> Eltérően a horizontális particionálás DDL-utasítások nem függnek a egy adatrétegbeli horizontálispartíció-térkép a rugalmas adatbázis ügyfélkönyvtárának keresztül való meghatározása.
>

1. [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx)
2. [HOZZON LÉTRE AZ ADATBÁZISHOZ KÖTŐDŐ HITELESÍTŐ ADATOK](https://msdn.microsoft.com/library/mt270260.aspx)
3. [KÜLSŐ ADATFORRÁS LÉTREHOZÁSA](https://msdn.microsoft.com/library/dn935022.aspx)
4. [KÜLSŐ TÁBLA LÉTREHOZÁSA](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="create-database-scoped-master-key-and-credentials"></a>Hatókörrel rendelkező adatbázis főkulcsának és a hitelesítő adatok létrehozása
A hitelesítő adatokat a rugalmas lekérdezés használják a távoli adatbázisokhoz való csatlakozáshoz.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'master_key_password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Ügyeljen arra, hogy a `<username>` nem vonatkozik a **"\@servername"** utótag. 
>

## <a name="create-external-data-sources"></a>Külső adatforrás létrehozása
Szintaxis:

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = ’<fully_qualified_server_name>’,
                DATABASE_NAME = ‘<remote_database_name>’,  
                CREDENTIAL = <credential_name> 
                ) [;] 

> [!IMPORTANT]
> A típus paraméternél értékre kell állítani **RDBMS**. 
>

### <a name="example"></a>Példa
Az alábbi példában látható a CREATE utasításban a használatát a külső adatforrásokhoz. 

    CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
    WITH 
    ( 
        TYPE=RDBMS, 
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ReferenceData', 
        CREDENTIAL= SqlUser 
    ); 

Az aktuális külső adatforrások listájának beolvasásához: 

    select * from sys.external_data_sources; 

### <a name="external-tables"></a>Külső táblák
Szintaxis:

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name . ] table_name  
    ( { <column_definition> } [ ,...n ])     
    { WITH ( <rdbms_external_table_options> ) } 
    )[;] 

    <rdbms_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>, 
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 

### <a name="example"></a>Példa
    CREATE EXTERNAL TABLE [dbo].[customer]( 
        [c_id] int NOT NULL, 
        [c_firstname] nvarchar(256) NULL, 
        [c_lastname] nvarchar(256) NOT NULL, 
        [street] nvarchar(256) NOT NULL, 
        [city] nvarchar(256) NOT NULL, 
        [state] nvarchar(20) NULL, 
        [country] nvarchar(50) NOT NULL, 
    ) 
    WITH 
    ( 
           DATA_SOURCE = RemoteReferenceData 
    ); 

Az alábbi példa bemutatja, hogyan külső táblák listájának lekérése az aktuális adatbázisban: 

    select * from sys.external_tables; 

### <a name="remarks"></a>Megjegyzések
Rugalmas lekérdezés kibővíti a meglévő külső tábla szintaxist RDBMS típusú külső adatforrások használó külső táblák meghatározásához. A vertikális particionálás egy külső tábla definícióját mutatja be a következő szempontokat: 

* **Séma**: A külső tábla DDL meghatározása egy sémát, amely a lekérdezések használhatnak. A külső tábla definíciójában megadott séma meg kell felelnie a távoli adatbázis, a tényleges adatok tárolására a táblák sémájával. 
* **Távoli adatbázis referencia**: A külső tábla DDL-külső adatforrásra hivatkozik. A külső adatforrás adja meg a logikai kiszolgáló és adatbázis nevét a távoli adatbázis tényleges táblák adatainak tárolására. 

Egy külső adatforrást használ, az előző szakaszban leírt módon, a külső táblák létrehozása szintaxisa a következő: 

A DATA_SOURCE záradék határozza meg, hogy a külső adatforrás (azaz a távoli adatbázis esetén a vertikális particionálás), amely a külső tábla szolgál.  

A SCHEMA_NAME és OBJECT_NAME záradékok lehetővé teszi, hogy leképezése a külső tábla definíciójában egy másik sémát az a távoli adatbázis-táblára, illetve egy táblázat egy másik nevet, illetve. Ez akkor hasznos, ha be szeretné állítani egy külső táblát egy katalógusnézet vagy DMV a távoli adatbázis - vagy bármely más olyan helyzetet, ahol a távoli tábla neve már használatban van helyileg.  

A következő DDL-utasítást a helyi katalógus egy meglévő külső tábla definíciójának csökken. Ez nincs hatással a távoli adatbázis. 

    DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**KÜLSŐ tábla létrehozása/DROP engedélyeinek**: ALTER ANY EXTERNAL DATA SOURCE engedélyekre van szükség a külső tábla DDL, tekintse meg az alapul szolgáló adatforrás is szükség van.  

## <a name="security-considerations"></a>Biztonsági szempontok
A külső tábla hozzáféréssel rendelkező felhasználók automatikusan hozzáférhetnek a külső adatforrás-definíciót a megadott hitelesítő adatok az alapul szolgáló távoli táblákba. Gondosan kezelje a külső tábla hozzáférést annak érdekében, hogy a hitelesítő adatokat a külső adatforrás keresztül nemkívánatos jogok kiterjesztése. Rendszeres SQL-engedélyek megadására vagy VISSZAVONHATJA a hozzáférést egy külső táblát, ugyanúgy, mintha egy normál táblákhoz használható.  

## <a name="example-querying-vertically-partitioned-databases"></a>Példa: lekérdezése függőlegesen particionált adatbázisok
A következő lekérdezés az ügyfelek hajt végre a háromutas csatlakozzon a két helyi táblában a megrendelések és sorok és a távoli tábla között. Ez a rugalmas lekérdezés referencia adatok használati példát: 

    SELECT      
     c_id as customer,
     c_lastname as customer_name,
     count(*) as cnt_orderline, 
     max(ol_quantity) as max_quantity,
     avg(ol_amount) as avg_amount,
     min(ol_delivery_d) as min_deliv_date
    FROM customer 
    JOIN orders 
    ON c_id = o_c_id
    JOIN  order_line 
    ON o_id = ol_o_id and o_c_id = ol_c_id
    WHERE c_id = 100


## <a name="stored-procedure-for-remote-t-sql-execution-spexecuteremote"></a>Tárolt eljárás távoli T-SQL-végrehajtásra: sp\_execute_remote
Rugalmas lekérdezés is vezet be, egy tárolt eljárást, amely a távoli adatbázis közvetlen hozzáférést biztosít. A tárolt eljárás neve [sp\_végrehajtása \_távoli](https://msdn.microsoft.com/library/mt703714) és a távoli adatbázis távoli tárolt eljárások, vagy a T-SQL kód végrehajtásához használható. Ez a következő paramétereket fogadja: 

* Adatforrás neve (nvarchar): az RDBMS típusú külső adatforrás neve. 
* Lekérdezés (nvarchar): A T-SQL lekérdezés hajtható végre a távoli adatbázishoz. 
* Deklarace parametru (nvarchar) – nem kötelező: a lekérdezési paramétert (például sp_executesql) használt paraméterek az adatok típusdefiníciók karakterlánc. 
* A paraméter értéke lista – nem kötelező: (például sp_executesql) paramétert értékek vesszővel elválasztott listája.

Az sp\_végrehajtása\_távoli a Meghívási paraméterek megadott külső adatforrás használja a megadott T-SQL-utasítás végrehajtása a távoli adatbázis. A távoli adatbázishoz való csatlakozáshoz külső adatforrás hitelesítő adatait használja.  

Példa: 

    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 



## <a name="connectivity-for-tools"></a>Az eszközök kapcsolat
Használhatja a szokásos SQL Server-kapcsolati karakterláncok az üzleti Intelligencia és integrációs eszközök az SQL DB-kiszolgálón, amelyen engedélyezve van a rugalmas lekérdezés és a definiált külső táblák adatbázisaihoz való kapcsolódásra. Győződjön meg arról, hogy az SQL Server támogatott-e az eszköz adatforrásként. Ezután tekintse meg a rugalmas lekérdezés adatbázis és a külső táblák csakúgy, mint bármely más SQL Server-adatbázis, amely az eszközzel kíván csatlakozni. 

## <a name="best-practices"></a>Ajánlott eljárások
* Győződjön meg arról, hogy a rugalmas lekérdezés végpont adatbázis adott hozzáférést a távoli adatbázishoz engedélyezi a hozzáférést az Azure-szolgáltatásokhoz az SQL DB tűzfal-konfigurációval. Emellett győződjön meg arról, hogy a hitelesítő adatokat a külső adatforrás-definíciót az adott felhasználó sikeresen be tud jelentkezni a távoli adatbázist, és rendelkezik engedélyekkel a távoli tábla eléréséhez.  
* Rugalmas lekérdezés működik a legjobban a lekérdezésekhez, a számítás a legtöbb teheti meg a távoli adatbázis. A legjobb lekérdezési teljesítmény szelektív szűrő predikátumok, amely a távoli adatbázisokhoz vagy, amely teljesen hajtható végre a távoli adatbázis illesztések kiértékelése az általában kap. Más lekérdezési minták előfordulhat, hogy nagy mennyiségű adat betöltése a távoli adatbázisról kell, és előfordulhat, hogy rosszul hajthat végre. 

## <a name="next-steps"></a>További lépések

* Rugalmas lekérdezés áttekintését lásd: [rugalmas lekérdezés – áttekintés](sql-database-elastic-query-overview.md).
* Függőleges particionálási oktatóanyagért lásd: [adatbázisközi lekérdezések (vertikális partíciók) – első lépések](sql-database-elastic-query-getting-started-vertical.md).
* Horizontális particionálást (sharding) foglalkozó oktatóanyagért lásd: [rugalmas lekérdezés horizontális particionálást (sharding) – első lépések](sql-database-elastic-query-getting-started.md).
* A szintaxist és a minta lekérdezésekhez vízszintesen particionált adatok, lásd: [lekérdezése vízszintesen particionált adatok)](sql-database-elastic-query-horizontal-partitioning.md)
* Lásd: [sp\_végrehajtása \_távoli](https://msdn.microsoft.com/library/mt703714) egy tárolt eljárás, amely végrehajtja a Transact-SQL-utasítás egy távoli Azure SQL Database vagy adatbázisok horizontális particionálási séma kidolgozásához szegmensek szolgáló készletét.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->
