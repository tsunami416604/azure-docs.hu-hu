---
title: IDENTITÁS használata helyettesítő kulcsok létrehozásához
description: Javaslatok és példák az IDENTITY tulajdonság használatára a helyettesítő kulcsok létrehozásához a Azure SQL Data Warehouse tábláiban.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/30/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 0ee15b975b5513077b26cceeb80ea3fb8c02456b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692471"
---
# <a name="using-identity-to-create-surrogate-keys-in-azure-sql-data-warehouse"></a>Helyettesítő kulcsok létrehozása a Azure SQL Data Warehouseban identitás használatával

Javaslatok és példák az IDENTITY tulajdonság használatára a helyettesítő kulcsok létrehozásához a Azure SQL Data Warehouse tábláiban.

## <a name="what-is-a-surrogate-key"></a>Mi az a helyettesítő kulcs?

A tábla egy helyettesítő kulcsa az egyes sorok egyedi azonosítóját tartalmazó oszlop. A kulcs nem jön létre a tábla adatainak alapján. Adatmodellek, például helyettesítő kulcsok létrehozása a tábláiban az adatraktár-modellek tervezésekor. Az IDENTITY tulajdonság használatával egyszerűen és hatékonyan érheti el ezt a célt, anélkül, hogy ez befolyásolná a terhelési teljesítményt.  

## <a name="creating-a-table-with-an-identity-column"></a>Tábla létrehozása azonosító oszloppal

Az IDENTITY tulajdonság úgy lett kialakítva, hogy az adatraktárban lévő összes eloszláson át tudja méretezni a terhelési teljesítmény befolyásolása nélkül. Ezért az identitás megvalósítása a célok megvalósítása felé irányul.

A tábla első létrehozásakor a következő utasításhoz hasonló szintaxissal definiálhat egy táblázatot, amely az IDENTITY tulajdonságot használja:

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1) NOT NULL
,   C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

Ezután a `INSERT..SELECT` használatával feltöltheti a táblázatot.

Ez a szakasz a megvalósítás árnyalatait emeli ki, hogy könnyebben megértse őket.  

### <a name="allocation-of-values"></a>Értékek kiosztása

Az IDENTITY tulajdonság nem garantálja a helyettesítő értékek lefoglalásának sorrendjét, amely a SQL Server és Azure SQL Database viselkedését tükrözi. Azure SQL Data Warehouse azonban a garancia hiánya sokkal erősebb.

A következő példa egy illusztráció:

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)    NOT NULL
,   C2 VARCHAR(30)              NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

INSERT INTO dbo.T1
VALUES (NULL);

INSERT INTO dbo.T1
VALUES (NULL);

SELECT *
FROM dbo.T1;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

Az előző példában két sor landolt az 1. eloszlásban. Az első sorban a `C1`oszlop 1 értékének helyettesítő értéke szerepel, a második sorban pedig az 61-es helyettesítő érték szerepel. Mindkét értéket az IDENTITY tulajdonság generálta. Azonban az értékek kiosztása nem folytonos. Ez a működésmód szándékos.

### <a name="skewed-data"></a>Elferdített adatértékek

Az adattípus értékeinek tartománya egyenletesen oszlik el az eloszlások között. Ha egy elosztott tábla elferdíti az adatmennyiséget, akkor az adattípushoz elérhető értékek tartománya túl korán is kimeríthető. Ha például az összes adatok egyetlen eloszlásban fejeződik be, akkor a tábla gyakorlatilag csak egy hatvanadik fér hozzá az adattípushoz. Emiatt az IDENTITY tulajdonság csak `INT` és `BIGINT` adattípusokra korlátozódik.

### <a name="selectinto"></a>Válassza a.. A

Ha egy meglévő IDENTITY oszlop van kiválasztva egy új táblába, az új oszlop örökli az IDENTITY tulajdonságot, kivéve, ha az alábbi feltételek egyike igaz:

- A SELECT utasítás illesztést tartalmaz.
- A UNION használatával több SELECT utasítást is csatlakoztathat.
- Az IDENTITY oszlop a kiválasztási listán egynél több alkalommal szerepel.
- Az IDENTITY oszlop egy kifejezés részét képezi.

Ha az ilyen feltételek bármelyike igaz, az oszlop létrehozása nem NULL az IDENTITY tulajdonság öröklése helyett.

### <a name="create-table-as-select"></a>CREATE TABLE VÁLASSZA KI

CREATE TABLE AS SELECT (CTAS) a SELECT... SQL Server viselkedését követi. A. Azonban nem adhat meg egy IDENTITY tulajdonságot az utasítás `CREATE TABLE` része oszlopának definíciójában. A CTAS `SELECT` részében nem használhatja az IDENTITY függvényt is. Egy tábla feltöltéséhez `CREATE TABLE` kell használnia a tábla megadásához, majd a `INSERT..SELECT` a feltöltéshez.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Értékek explicit beszúrása egy IDENTITY oszlopba

SQL Data Warehouse támogatja `SET IDENTITY_INSERT <your table> ON|OFF` szintaxist. Ezt a szintaxist használva explicit módon szúrhat be értékeket az IDENTITY oszlopba.

Számos adatmodell, például az előre meghatározott negatív értékek használata a dimenziókban megadott sorokhoz. Ilyen például az-1 vagy az "ismeretlen tag" sor.

A következő szkript azt mutatja be, hogyan lehet explicit módon felvenni ezt a sort a SET IDENTITY_INSERT használatával:

```sql
SET IDENTITY_INSERT dbo.T1 ON;

INSERT INTO dbo.T1
(   C1
,   C2
)
VALUES (-1,'UNKNOWN')
;

SET IDENTITY_INSERT dbo.T1 OFF;

SELECT  *
FROM    dbo.T1
;
```

## <a name="loading-data"></a>Adatok betöltése

Az IDENTITY tulajdonság jelenléte bizonyos hatással van az adatok betöltésére szolgáló kódra. Ez a szakasz néhány alapvető mintázatot mutat be az adatoknak a táblákba való betöltéséhez identitás használatával.

Az adatok táblába való betöltéséhez és a helyettesítő kulcs identitás használatával történő létrehozásához hozza létre a táblát, majd használja az INSERT.. SELECT vagy INSERT.. A betöltés végrehajtásához szükséges értékek.

Az alábbi példa az alapszintű mintát emeli ki:

```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)
,   C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT  C2
FROM    ext.T1
;

SELECT *
FROM   dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE]
> Az adatok nem használhatók `CREATE TABLE AS SELECT` jelenleg, ha azonosító oszlopot tartalmazó táblába töltenek be adatbevitelt.
>

Az adatok betöltésével kapcsolatos további információkért lásd: [a kinyerési, betöltési és átalakítási (elt) tervezés a Azure SQL Data Warehouse és az](design-elt-data-loading.md) [ajánlott eljárások betöltéséhez](guidance-for-loading-data.md).

## <a name="system-views"></a>Rendszernézetek

A [sys. identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql) katalógus nézettel azonosítható az Identity tulajdonságot tartalmazó oszlop.

Az adatbázis-séma jobb megismerése érdekében ez a példa bemutatja, hogyan integrálhatja a sys. identity_column-t más Rendszerkatalógus-nézetekkel:

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       CASE WHEN ic.column_id IS NOT NULL
             THEN 1
        ELSE 0
        END AS is_identity
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
LEFT JOIN   sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="limitations"></a>Korlátozások

Az IDENTITY tulajdonság nem használható:

- Ha az oszlop adattípusa nem INT vagy BIGINT
- Ha az oszlop is a terjesztési kulcs
- Ha a tábla külső tábla

A következő kapcsolódó függvények nem támogatottak SQL Data Warehouseban:

- [IDENTITÁS ()](/sql/t-sql/functions/identity-function-transact-sql)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql)
- [IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql)

## <a name="common-tasks"></a>Gyakori műveletek

Ez a szakasz egy olyan mintakód használatát ismerteti, amellyel általános feladatokat hajthat végre az identitás oszlopainak használatakor.

A C1 oszlop a következő feladatok IDENTITÁSa.

### <a name="find-the-highest-allocated-value-for-a-table"></a>Egy tábla legmagasabb lefoglalt értékének megkeresése

A `MAX()` függvény használatával meghatározhatja az elosztott táblák legmagasabb értékét:

```sql
SELECT MAX(C1)
FROM dbo.T1
```

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Az IDENTITY tulajdonság vetőmagjának és növekményének megkeresése

A katalógus nézeteivel a következő lekérdezéssel derítheti fel egy tábla identitásának növekményét és a mag konfigurációs értékeit:

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       ic.seed_value
,       ic.increment_value
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
JOIN        sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="next-steps"></a>További lépések

- [táblák áttekintésével](/azure/sql-data-warehouse/sql-data-warehouse-tables-overview)
- [CREATE TABLE (Transact-SQL) identitás (tulajdonság)](/sql/t-sql/statements/create-table-transact-sql-identity-property?view=azure-sqldw-latest)
- [DBCC CHECKINDENT](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql)
