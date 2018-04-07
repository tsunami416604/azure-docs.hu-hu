---
title: A különböző séma felhő az adatbázisok közötti lekérdezés |} Microsoft Docs
description: függőleges partíció keresztül közötti adatbázis-lekérdezések beállítása
services: sql-database
manager: craigg
author: MladjoA
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: mlandzic
ms.openlocfilehash: 243110e47ab7c98c9fec9b2747fde73ccb775fbc
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Más sémák (előzetes verzió) felhő adatbázisokkal átfogó lekérdezése
![A különböző adatbázisokhoz táblák átfogó lekérdezése][1]

Adatbázisok függőleges particionált táblák más-más részhalmazához különböző adatbázist használja. Ez azt jelenti, hogy a séma nem egyezik, a különböző adatbázisokhoz. Például a készlet összes tábla esetén egy adatbázis míg nyilvántartási kapcsolatos összes táblázatot egy második adatbázison. 

## <a name="prerequisites"></a>Előfeltételek
* A felhasználónak rendelkeznie kell ALTER ANY külső ADATFORRÁS engedéllyel. Ez az engedély megtalálható az ALTER DATABASE engedéllyel.
* Az ALTER ANY külső ADATFORRÁS engedélyekre van szükség az alapul szolgáló adatforrásban hivatkozik.

## <a name="overview"></a>Áttekintés

> [!NOTE]
> Ellentétben a vízszintes particionálás, a DDL-utasításokban nem függ egy adatrétegbeli keresztül a rugalmas adatbázis ügyféloldali kódtára a shard leképezést definiáló.
>

1. [A FŐKULCS LÉTREHOZÁSA](https://msdn.microsoft.com/library/ms174382.aspx)
2. [HOZZON LÉTRE AZ ADATBÁZISHOZ KÖTŐDŐ HITELESÍTŐ ADATOK](https://msdn.microsoft.com/library/mt270260.aspx)
3. [KÜLSŐ ADATFORRÁS LÉTREHOZÁSA](https://msdn.microsoft.com/library/dn935022.aspx)
4. [KÜLSŐ TÁBLA LÉTREHOZÁSA](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="create-database-scoped-master-key-and-credentials"></a>Adatbázis hatókörű főkulcs és hitelesítő adatainak létrehozása
A hitelesítő adatok segítségével a rugalmas lekérdezés csatlakozzon a távoli adatbázishoz.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'master_key_password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Győződjön meg arról, hogy a `<username>` nem vonatkozik a **"@servername"** utótag. 
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
> A típusparaméter értékre kell állítani **RDBMS**. 
>

### <a name="example"></a>Példa
Az alábbi példában látható a külső adatforrások a CREATE utasítás használatát. 

    CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
    WITH 
    ( 
        TYPE=RDBMS, 
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ReferenceData', 
        CREDENTIAL= SqlUser 
    ); 

Az aktuális külső adatforrások listájának beolvasása: 

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

A következő példa bemutatja, hogyan lehet lekérni a külső táblák listáját az aktuális adatbázisban: 

    select * from sys.external_tables; 

### <a name="remarks"></a>Megjegyzések
Rugalmas lekérdezési kibővíti a meglévő külső tábla szintaxisát, és adja meg a külső adatforrások RDBMS típusú használó külső táblákon. A vertikális particionálás egy külső tábla definícióját tartalmazza a következő szempontokat: 

* **Séma**: A külső tábla DDL definiálja a séma, a lekérdezéseket használhat. A távoli, a tényleges adatokat tároló adatbázis azon tábláit sémája egyezniük kell a külső tábla definíciójában megadott séma. 
* **Távoli adatbázis hivatkozás**: A külső tábla DDL hivatkozik egy külső adatforrásból. A külső adatforrás határozza meg, a logikai kiszolgáló nevét és a távoli adatbázis, a tényleges adatait tároló nevét. 

Külső adatforrás használja, az előző szakaszban leírt módon, a külső táblák létrehozására szintaxisa a következő: 

A DATA_SOURCE záradékot a külső tábla használt külső adatforrás (azaz a távoli adatbázis esetén a vertikális particionálás) határozza meg.  

A SCHEMA_NAME és OBJECT_NAME záradékok hozzárendelését a külső tábla definíciójában egy táblához, a távoli adatbázis egy másik séma, vagy egy táblához, más néven, illetve lehetőséget adja meg. Ez akkor hasznos, ha be szeretné állítani a külső tábla egy katalógusnézet használatával derítheti ki vagy DMV a távoli adatbázis - vagy bármely más olyan esetben, ha a távoli tábla neve már használatban van helyileg.  

A következő DDL-utasítás egy meglévő külső tábla definícióját a helyi katalógus elutasítja azokat. A távoli adatbázis nem befolyásolja. 

    DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**A külső tábla létrehozása/DROP engedélyeinek**: ALTER ANY külső ADATFORRÁS engedélyekre van szükség a külső tábla DDL is szükség van az alapul szolgáló adatforrásban hivatkozik.  

## <a name="security-considerations"></a>Biztonsági szempontok
A külső tábla hozzáféréssel rendelkező felhasználók automatikusan hozzáférhetnek az alapul szolgáló távoli táblák a külső adatforrás-definíciót a megadott hitelesítő adatok alapján. A külső tábla elérése gondosan kezelje a hitelesítő adatokat, a külső adatforrás keresztül nemkívánatos jogok kiterjesztésének elkerülése érdekében. Rendszeres SQL engedélyek segítségével vagy egy külső táblájához való hozzáférés VISSZAVONÁSÁHOZ, ugyanúgy, mintha az lenne normál táblákhoz.  

## <a name="example-querying-vertically-partitioned-databases"></a>Példa: adatbázisok függőleges lekérdezése particionálva.
A következő lekérdezés az ügyfelek a két helyi tábla rendelések és a sorok és a távoli tábla háromutas illesztést végez. Ez az a hivatkozás adatok használati eset rugalmas lekérdezés példát: 

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
Rugalmas lekérdezési is vezet be a tárolt eljárás, amely közvetlen hozzáférést biztosít a távoli adatbázishoz. A tárolt eljárás neve [sp\_hajtható végre \_távoli](https://msdn.microsoft.com/library/mt703714) és a távoli adatbázis távoli tárolt eljárások, illetve a T-SQL kód végrehajtásához használható. Ez a következő paramétereket fogadja: 

* Adatforrás neve (nvarchar): a külső adatforrás RDBMS típusú nevét. 
* Lekérdezés (nvarchar): A T-SQL-lekérdezést hajtható végre a távoli adatbázishoz. 
* Paraméterdeklarációhoz (nvarchar) – nem kötelező: adatok típusdefiníciók a paraméterek (például sp_executesql) a következő lekérdezésparaméter használt karakterlánc. 
* Paraméter értéklistát - választható: paraméterértékek (például sp_executesql) vesszővel tagolt listája.

Az sp\_hajtható végre\_távoli a meghívás paramétereit szerepel a külső adatforrás használja az adott T-SQL-utasítás végrehajtása a távoli adatbázishoz. A külső adatforrás hitelesítő adatait használja a távoli adatbázishoz való kapcsolódáshoz.  

Példa: 

    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 



## <a name="connectivity-for-tools"></a>Eszközök kapcsolattal
Használhatja a szokásos SQL Server kapcsolati karakterláncok adatbázisaihoz való kapcsolódásra a BI és az integráció eszközök az SQL-adatbázis a kiszolgálón, amelyen engedélyezve van a Rugalmas lekérdezési és definiált külső táblák. Győződjön meg arról, hogy az SQL Server támogatja-e az eszköz adatforrásként. Majd tekintse át a Rugalmas lekérdezési adatbázis és a külső táblákra csakúgy, mint bármely más SQL Server-adatbázis, amely az eszköz kíván csatlakozni. 

## <a name="best-practices"></a>Ajánlott eljárások
* Győződjön meg arról, hogy a Rugalmas lekérdezési végpont adatbázis adott hozzáférés a távoli adatbázis Azure-szolgáltatások hozzáférésének engedélyezéséhez az SQL-adatbázis a tűzfal-konfigurációjában. Bizonyosodjon meg arról, hogy a külső adatforrása definíciója a megadott hitelesítő adatok sikeresen be tud jelentkezni a távoli adatbázis-e, és a távoli tábla elérésére jogosult.  
* Rugalmas lekérdezési működik a legjobban az lekérdezések ahol a számítási a legtöbb a a távoli adatbázis végezhető. A legjobb lekérdezési teljesítmény és a távoli adatbázisokhoz vagy csatlakozik, amely teljesen hajtható végre a távoli adatbázis kiértékelhető szelektív szűrő predikátumok általában kap. Más lekérdezési mintáinak esetleg nagy mennyiségű adatok betöltése a távoli adatbázisról, és rosszul hajthatja végre. 

## <a name="next-steps"></a>További lépések

* Rugalmas lekérdezési áttekintését lásd: [rugalmas lekérdezési áttekintése](sql-database-elastic-query-overview.md).
* Függőleges particionálási oktatóanyagért lásd a [első lépések (a vertikális particionálás) közötti adatbázis-lekérdezés](sql-database-elastic-query-getting-started-vertical.md).
* Vízszintes particionálás (horizontális) oktatóanyagért lásd a [Ismerkedés a vízszintes particionálására (horizontális) rugalmas lekérdezési](sql-database-elastic-query-getting-started.md).
* A szintaxis és a minta lekérdezések vízszintesen particionált adatok, lásd: [adatok vízszintesen lekérdezése particionálva)](sql-database-elastic-query-horizontal-partitioning.md)
* Lásd: [sp\_hajtható végre \_távoli](https://msdn.microsoft.com/library/mt703714) tárolt eljárás, amely végrehajtja a Transact-SQL-utasítás egy egyetlen távoli Azure SQL Database vagy az adatbázisok egy vízszintes particionálási sémát a szilánkok szolgál.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->
