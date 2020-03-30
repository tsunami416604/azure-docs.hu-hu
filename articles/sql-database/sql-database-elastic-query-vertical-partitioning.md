---
title: Lekérdezés különböző sémával rendelkező felhőalapú adatbázisok ban
description: adatbázisközi lekérdezések beállítása függőleges partíciókon
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: d5983d25685242a696300f293231bbf987e8442d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823733"
---
# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Lekérdezés különböző sémákkal rendelkező felhőalapú adatbázisok között (előzetes verzió)

![Lekérdezés táblák között különböző adatbázisokban][1]

A vertikálisan particionált adatbázisok különböző táblákat használnak a különböző adatbázisokban. Ez azt jelenti, hogy a séma különböző adatbázisokban eltérő. Például a készlet minden táblája egy adatbázisban van, míg az összes könyveléssel kapcsolatos tábla egy második adatbázisban van. 

## <a name="prerequisites"></a>Előfeltételek

* A felhasználónak rendelkeznie kell alter minden külső adatforrás engedéllyel. Ez az engedély az ALTER DATABASE engedély része.
* Alter Bármely külső adatforrás-engedély szükséges az alapul szolgáló adatforrásra való hivatkozáshoz.

## <a name="overview"></a>Áttekintés

> [!NOTE]
> A horizontális particionálással ellentétben ezek a DDL-utasítások nem függnek attól, hogy egy adatréteget definiálnak egy shard leképezéssel a rugalmas adatbázis-ügyfélkódtáron keresztül.
>

1. [FŐKULCS LÉTREHOZÁSA](https://msdn.microsoft.com/library/ms174382.aspx)
2. [ADATBÁZIS HATÓKÖRE HITELESÍTŐ ADATOK LÉTREHOZÁSA](https://msdn.microsoft.com/library/mt270260.aspx)
3. [KÜLSŐ ADATFORRÁS LÉTREHOZÁSA](https://msdn.microsoft.com/library/dn935022.aspx)
4. [KÜLSŐ TÁBLA LÉTREHOZÁSA](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="create-database-scoped-master-key-and-credentials"></a>Adatbázishatókörrel és -hitelesítő adatokkal létrehozott főkulcs és hitelesítő adatok létrehozása

A hitelesítő adatokat a rugalmas lekérdezés használja a távoli adatbázisokhoz való csatlakozáshoz.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'master_key_password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Győződjön `<username>` meg arról, hogy a nem tartalmaz **semmilyen "kiszolgálónév"\@** utótagot. 
>

## <a name="create-external-data-sources"></a>Külső adatforrások létrehozása

Szintaxis:

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = ’<fully_qualified_server_name>’,
                DATABASE_NAME = ‘<remote_database_name>’,  
                CREDENTIAL = <credential_name> 
                ) [;] 

> [!IMPORTANT]
> A TYPE paramétert **RDBMS**-re kell állítani. 
>

### <a name="example"></a>Példa

A következő példa a CREATE utasítás külső adatforrásokhoz való használatát mutatja be. 

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

```sql
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
```

A következő példa bemutatja, hogyan lehet lekérni a külső táblák listáját az aktuális adatbázisból: 

    select * from sys.external_tables; 

### <a name="remarks"></a>Megjegyzések

A rugalmas lekérdezés kiterjeszti a meglévő külső tábla szintaxisát az RDBMS típusú külső adatforrásokat használó külső táblák meghatározásához. A függőleges particionálás külső tábladefiníciója a következő szempontokat fedi le: 

* **Séma**: A külső tábla DDL határozza meg a sémát, amely a lekérdezések használhatják. A külső tábladefinícióban megadott sémának meg kell egyeznie a tényleges adatokat tároló távoli adatbázis tábláinak sémájával. 
* **Távoli adatbázis-hivatkozás**: A külső DDL tábla külső adatforrásra hivatkozik. A külső adatforrás annak a távoli adatbázisnak az SQL Database-kiszolgáló nevét és adatbázisnevét adja meg, amelyben a tényleges táblaadatok at tárolják. 

Az előző szakaszban ismertetett külső adatforrás használatával a külső táblák létrehozásának szintaxisa a következő: 

A DATA_SOURCE záradék határozza meg a külső táblához használt külső adatforrást (azaz függőleges particionálás esetén a távoli adatbázist).  

A SCHEMA_NAME és OBJECT_NAME záradékok lehetővé teszik a külső tábladefiníció hozzárendelését a távoli adatbázis egy másik sémájában lévő táblához, illetve egy másik nevű táblához. Ez akkor hasznos, ha egy katalógusnézethez vagy a távoli adatbázis DMV-jéhez szeretne külső táblát definiálni , vagy bármely más olyan helyzetben, amikor a távoli tábla neve már helyileg elvan tántorítva.  

A következő DDL-utasítás egy meglévő külső tábladefiníciót távoltesz a helyi katalógusból. Nincs hatással a távoli adatbázisra. 

    DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**A CREATE/DROP EXTERNAL TABLE engedélyei:** A külső DDL tábla hoz szükséges külső adatforrás-engedélyek módosítása, amely szintén az alapul szolgáló adatforrásra való hivatkozáshoz szükséges.  

## <a name="security-considerations"></a>Biztonsági szempontok

A külső táblához hozzáféréssel rendelkező felhasználók automatikusan hozzáférnek az alapul szolgáló távoli táblákhoz a külső adatforrás-definícióban megadott hitelesítő adatok alatt. A külső adatforrás hitelesítő adataival gondosan kezelje a külső táblához való hozzáférést, hogy elkerülje a jogosultságok nem kívánt kiterjesztését. A rendszeres SQL-engedélyek egy külső táblához való hozzáférés megadására vagy visszavonására használhatók, mintha egy normál tábla lenne.  

## <a name="example-querying-vertically-partitioned-databases"></a>Példa: vertikálisan particionált adatbázisok lekérdezése

A következő lekérdezés háromirányú illesztést hajt végre a rendelések és rendeléssorok két helyi táblája, valamint a távoli tábla között az ügyfelek számára. Ez egy példa a rugalmas lekérdezés referenciaadat-használati esetére: 

```sql
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
```

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>Tárolt eljárás távoli T-SQL végrehajtáshoz: sp\_execute_remote

A rugalmas lekérdezés egy tárolt eljárást is bevezet, amely közvetlen hozzáférést biztosít a távoli adatbázishoz. A tárolt eljárás [neve\_ \_SP execute remote](https://msdn.microsoft.com/library/mt703714) és használható távoli tárolt eljárások vagy T-SQL kódot a távoli adatbázisban. A következő paramétereket veszi igénybe: 

* Adatforrás neve (nvarchar): Az RDBMS típusú külső adatforrás neve. 
* Lekérdezés (nvarchar): A távoli adatbázisban végrehajtandó T-SQL-lekérdezés. 
* Paraméterdeklaráció (nvarchar) - nem kötelező: A Query paraméterben használt paraméterek (például sp_executesql) adattípus-definícióival rendelkező karakterlánc. 
* Paraméterérték-lista - nem kötelező: a paraméterértékek vesszővel tagolt listája (például sp_executesql).

Az\_sp\_execute remote a meghívási paraméterekben megadott külső adatforrást használja a megadott T-SQL utasítás végrehajtásához a távoli adatbázisban. A külső adatforrás hitelesítő adatait használja a távoli adatbázishoz való csatlakozáshoz.  

Példa: 

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 
```

## <a name="connectivity-for-tools"></a>Eszközök csatlakoztatása

A normál SQL Server kapcsolati karakterláncok segítségével összekapcsolhatja az üzletiintelligencia- és adatintegrációs eszközöket az SQL DB-kiszolgálón található adatbázisokkal, amelyeken rugalmas lekérdezés engedélyezve van, és külső táblákat definiálva. Győződjön meg arról, hogy az SQL Server támogatja az eszköz adatforrásaként. Ezután tekintse meg a rugalmas lekérdezési adatbázis és a külső táblák, mint bármely más SQL Server-adatbázis, amely az eszközhöz csatlakozik. 

## <a name="best-practices"></a>Ajánlott eljárások

* Győződjön meg arról, hogy a rugalmas lekérdezési végpont-adatbázis hozzáférést kapott a távoli adatbázishoz, ha engedélyezi a hozzáférést az Azure Services számára az SQL DB tűzfal konfigurációjában. Győződjön meg arról is, hogy a külső adatforrás-definícióban megadott hitelesítő adatok sikeresen betudnak jelentkezni a távoli adatbázisba, és rendelkeznek a távoli tábla eléréséhez szükséges engedélyekkel.  
* A rugalmas lekérdezés olyan lekérdezések esetén működik a legjobban, ahol a számítás nagy része a távoli adatbázisokon végezhető el. Általában a legjobb lekérdezési teljesítményt szelektív szűrőpredikátumokkal kapja meg, amelyek kiértékelhetők a távoli adatbázisokon vagy a távoli adatbázison teljes mértékben elvégezhető illesztéseken. Előfordulhat, hogy más lekérdezési mintáknak nagy mennyiségű adatot kell betölteniük a távoli adatbázisból, és előfordulhat, hogy rosszul teljesítenek. 

## <a name="next-steps"></a>További lépések

* A rugalmas lekérdezés áttekintését a [Rugalmas lekérdezés áttekintése című témakörben találja.](sql-database-elastic-query-overview.md)
* Függőleges particionálási oktatóanyag: [Első lépések adatbázisközi lekérdezéssel (függőleges particionálás)](sql-database-elastic-query-getting-started-vertical.md).
* Horizontális particionálási (horizontális) oktatóanyag: [A rugalmas lekérdezés horizontális particionálás (horizontális sáfálás) című témakörben található.](sql-database-elastic-query-getting-started.md)
* A vízszintesen particionált adatok szintaxisát és mintalekérdezéseit [lásd: Horizontálisan particionált adatok lekérdezése)](sql-database-elastic-query-horizontal-partitioning.md)
* [Lásd:\_ \_sp távoli végrehajtása](https://msdn.microsoft.com/library/mt703714) egy tárolt eljárás, amely végrehajtja a Transact-SQL utasításegyetlen távoli Azure SQL-adatbázis vagy egy horizontális particionálási séma szegmensként szolgáló adatbázisok készlete.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->
