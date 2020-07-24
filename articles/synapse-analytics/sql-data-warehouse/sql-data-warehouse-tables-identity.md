---
title: IDENTITÁS használata helyettesítő kulcsok létrehozásához
description: Javaslatok és példák az IDENTITY tulajdonság használatára helyettesítő kulcsok létrehozásához a szinapszis SQL-készlet tábláiban.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/20/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: d19f59635920951b506e41884f4ab79be78e247d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080726"
---
# <a name="using-identity-to-create-surrogate-keys-in-synapse-sql-pool"></a>Helyettesítő kulcsok létrehozása identitás használatával a szinapszis SQL-készletben

Ebben a cikkben javaslatokat és példákat talál arra, hogy az IDENTITY tulajdonság használatával helyettesítő kulcsokat hozzon létre a szinapszis SQL-készlet tábláiban.

## <a name="what-is-a-surrogate-key"></a>Mi az a helyettesítő kulcs?

A tábla egy helyettesítő kulcsa az egyes sorok egyedi azonosítóját tartalmazó oszlop. A kulcs nem jön létre a tábla adatainak alapján. Adatmodellek, például helyettesítő kulcsok létrehozása a tábláiban az adatraktár-modellek tervezésekor. Az IDENTITY tulajdonság használatával egyszerűen és hatékonyan érheti el ezt a célt, anélkül, hogy ez befolyásolná a terhelési teljesítményt. Az IDENTITY tulajdonsághoz a [create Table-(Transact-SQL-) identitásban (tulajdonság)](/sql/t-sql/statements/create-table-transact-sql-identity-property?view=azure-sqldw-latest)részletezett korlátozások vonatkoznak. Az identitás egyik korlátja, hogy nem garantált egyedi. Az identitás beállításának kikapcsolásával és az identitás értékének kivetésével a rendszer több egyedi értéket fog eredményezni, de nem garantálja az egyediséget minden helyzetben. Ha az identitás korlátozásai miatt nem használhat identitási értékeket, hozzon létre egy külön táblázatot, amely egy aktuális értéket tárol, és kezelje a tábla és a szám hozzárendelésének hozzáférését az alkalmazással. 

## <a name="creating-a-table-with-an-identity-column"></a>Tábla létrehozása azonosító oszloppal

Az IDENTITY tulajdonság úgy lett kialakítva, hogy a kibővítse a szinapszis SQL-készlet összes eloszlását anélkül, hogy ez befolyásolná a terhelési teljesítményt. Ezért az identitás megvalósítása a célok megvalósítása felé irányul.

A tábla első létrehozásakor a következő utasításhoz hasonló szintaxissal definiálhat egy táblázatot, amely az IDENTITY tulajdonságot használja:

```sql
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1) NOT NULL
,    C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

`INSERT..SELECT`A paranccsal feltöltheti a táblázatot.

Ez a szakasz a megvalósítás árnyalatait emeli ki, hogy könnyebben megértse őket.  

### <a name="allocation-of-values"></a>Értékek kiosztása

Az IDENTITY tulajdonság nem garantálja azt a sorrendet, amelyben a helyettesítő értékek le lesznek foglalva az adatraktár elosztott architektúrája miatt. Az IDENTITY tulajdonság úgy lett kialakítva, hogy a kibővítse a szinapszis SQL-készlet összes eloszlását anélkül, hogy ez befolyásolná a terhelési teljesítményt. 

A következő példa egy illusztráció:

```sql
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1)    NOT NULL
,    C2 VARCHAR(30)                NULL
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

Az előző példában két sor landolt az 1. eloszlásban. Az első sorban 1 érték szerepel az oszlopban `C1` , a második sorban pedig a 61-es helyettesítő érték szerepel. Mindkét értéket az IDENTITY tulajdonság generálta. Azonban az értékek kiosztása nem folytonos. Ez a működésmód szándékos.

### <a name="skewed-data"></a>Elferdített adatértékek

Az adattípus értékeinek tartománya egyenletesen oszlik el az eloszlások között. Ha egy elosztott tábla elferdíti az adatmennyiséget, akkor az adattípushoz elérhető értékek tartománya túl korán is kimeríthető. Ha például az összes adatok egyetlen eloszlásban fejeződik be, akkor a tábla gyakorlatilag csak egy hatvanadik fér hozzá az adattípushoz. Emiatt az IDENTITY tulajdonság `INT` csak a és az `BIGINT` adattípusokra korlátozódik.

### <a name="selectinto"></a>Válassza a.. A

Ha egy meglévő IDENTITY oszlop van kiválasztva egy új táblába, az új oszlop örökli az IDENTITY tulajdonságot, kivéve, ha az alábbi feltételek egyike igaz:

- A SELECT utasítás illesztést tartalmaz.
- A UNION használatával több SELECT utasítást is csatlakoztathat.
- Az IDENTITY oszlop a kiválasztási listán egynél több alkalommal szerepel.
- Az IDENTITY oszlop egy kifejezés részét képezi.

Ha az ilyen feltételek bármelyike igaz, az oszlop létrehozása nem NULL az IDENTITY tulajdonság öröklése helyett.

### <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

CREATE TABLE AS SELECT (CTAS) a SELECT... SQL Server viselkedését követi. A. Azonban nem adhat meg IDENTITY tulajdonságot az utasítás részeként definiált oszlop definíciójában `CREATE TABLE` . A CTAS részében nem használhatja az IDENTITY függvényt is `SELECT` . Egy tábla feltöltéséhez a használatával kell `CREATE TABLE` megadnia a táblázatot, amelyet a `INSERT..SELECT` feltöltéshez kell adnia.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Értékek explicit beszúrása egy IDENTITY oszlopba

A szinapszis SQL-készlet támogatja a `SET IDENTITY_INSERT <your table> ON|OFF` szintaxist. Ezt a szintaxist használva explicit módon szúrhat be értékeket az IDENTITY oszlopba.

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

SELECT     *
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
(    C1 INT IDENTITY(1,1)
,    C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT     C2
FROM    ext.T1
;

SELECT *
FROM   dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE]
> A jelenleg nem használható, `CREATE TABLE AS SELECT` Ha az adatok betöltését egy azonosító oszlopot tartalmazó táblába helyezi.
>

Az adatok betöltésével kapcsolatos további információkért lásd: [a kinyerési, betöltési és átalakítási (elt) tervezése a SZINAPSZIS SQL-készlethez](design-elt-data-loading.md) , valamint az [ajánlott eljárások betöltése](guidance-for-loading-data.md).

## <a name="system-views"></a>Rendszernézetek

Az IDENTITY tulajdonsággal rendelkező oszlop azonosításához a [sys. identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) Catalog nézetet használhatja.

Az adatbázis-séma jobb megismerése érdekében ez a példa bemutatja, hogyan integrálható a sys. identity_column "a Rendszerkatalógus más nézeteivel:

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

A következő kapcsolódó függvények nem támogatottak a szinapszis SQL-készletben:

- [IDENTITÁS ()](/sql/t-sql/functions/identity-function-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="common-tasks"></a>Gyakori feladatok

Ez a szakasz egy olyan mintakód használatát ismerteti, amellyel általános feladatokat hajthat végre az identitás oszlopainak használatakor.

A C1 oszlop a következő feladatok IDENTITÁSa.

### <a name="find-the-highest-allocated-value-for-a-table"></a>Egy tábla legmagasabb lefoglalt értékének megkeresése

Az `MAX()` elosztott táblához lefoglalt legmagasabb érték meghatározásához használja a függvényt:

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

- [Táblázat áttekintése](sql-data-warehouse-tables-overview.md)
- [CREATE TABLE (Transact-SQL) identitás (tulajdonság)](/sql/t-sql/statements/create-table-transact-sql-identity-property?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [DBCC CHECKINDENT](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
