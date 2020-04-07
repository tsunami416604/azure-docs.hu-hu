---
title: Helyettesítő kulcsok létrehozása az IDENTITY használatával
description: Javaslatok és példák az IDENTITY tulajdonság használatával helyettesítő kulcsok létrehozásához a Synapse SQL-készlet táblákon.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/30/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: e681e8ad655c31d5078b56b8f1a49cfd7c664533
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742642"
---
# <a name="using-identity-to-create-surrogate-keys-in-synapse-sql-pool"></a>Helyettesítő kulcsok létrehozása a szinapszis SQL-készletben az IDENTITY használatával

Javaslatok és példák az IDENTITY tulajdonság használatával helyettesítő kulcsok létrehozásához a Synapse SQL-készlet táblákon.

## <a name="what-is-a-surrogate-key"></a>Mi az a helyettesítő kulcs

A táblázat helyettesítő kulcsa egy oszlop, amely minden sorhoz egyedi azonosítót ad. A kulcs nem jön létre a táblaadatokból. Az adatmodellezők szeretnek helyettesítő kulcsokat létrehozni a tábláikban, amikor adattárház-modelleket terveznek. Az IDENTITY tulajdonság gal egyszerűen és hatékonyan elérheti ezt a célt anélkül, hogy befolyásolná a terhelési teljesítményt.  

## <a name="creating-a-table-with-an-identity-column"></a>IDENTITÁSoszlopot tartalmazó tábla létrehozása

Az IDENTITY tulajdonság úgy van kialakítva, hogy a synapse SQL-készlet összes disztribúciója között horizontálisfelskálázásra kerüljön, anélkül, hogy befolyásolná a terhelési teljesítményt. Ezért az IDENTITY megvalósítása e célok elérésére irányul.

A táblát az alábbi utasításhoz hasonló szintaxissal határozhatja meg úgy, hogy az IDENTITY tulajdonsággal rendelkezik:

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

Ezután feltöltheti `INSERT..SELECT` a táblázatot.

Ez a szakasz további része kiemeli a megvalósítás árnyalatait, hogy segítsen jobban megérteni őket.  

### <a name="allocation-of-values"></a>Az értékek felosztása

Az IDENTITY tulajdonság nem garantálja a helyettesítő értékek lefoglalásának sorrendjét, ami az SQL Server és az Azure SQL Database viselkedését tükrözi. Azonban a Synapse SQL-készletben a garancia hiánya hangsúlyosabb.

A következő példa illusztráció:

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

Az előző példában két sor landolt az 1. Az első sor helyettesítő értéke 1 `C1`az oszlopban, a második sor helyettesítő értéke 61. Mindkét értéket az IDENTITY tulajdonság hozta létre. Az értékek felosztása azonban nem összefüggő. Ez a működésmód szándékos.

### <a name="skewed-data"></a>Ferde adatok

Az adattípus értéktartománya egyenletesen oszlik el a disztribúciók között. Ha egy elosztott tábla ferde adatoktól szenved, akkor az adattípus számára elérhető értéktartomány idő előtt kimerülhet. Ha például az összes adat egyetlen eloszlásba kerül, akkor a tábla gyakorlatilag csak az adattípus értékeinek egyhatvandához fér hozzá. Emiatt az IDENTITY tulajdonság csak `INT` `BIGINT` adattípusokra korlátozódik.

### <a name="selectinto"></a>Válassza ki.. INTO INTO (BE)

Ha egy meglévő IDENTITY oszlop ot jelöl ki egy új táblába, az új oszlop örökli az IDENTITY tulajdonságot, kivéve, ha az alábbi feltételek valamelyike teljesül:

- A SELECT utasítás illesztést tartalmaz.
- Több SELECT utasítás csatlakozik az UNION használatával.
- Az IDENTITY oszlop többször is szerepel a SELECT listában.
- Az IDENTITY oszlop egy kifejezés része.

Ha a feltételek bármelyike teljesül, az oszlop nem null jön létre az IDENTITY tulajdonság öröklése helyett.

### <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

HOZZON LÉTRE TÁBLÁZAT VÁLASZTÓKÉNT (CTAS) ugyanazt az SQL Server-viselkedést követi, mint a SELECT.. INTO. Azonban nem adhat meg IDENTITY tulajdonságot az utasítás `CREATE TABLE` részének oszlopdefiníciójában. Az IDENTITY függvény a CTAS `SELECT` részében sem használható. Táblázat feltöltéséhez meg kell határoznia `CREATE TABLE` a táblázatot, majd `INSERT..SELECT` fel kell használnia azt.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Értékek explicit beszúrása IDENTITY oszlopba

A szinapszis SQL-készlet támogatja a `SET IDENTITY_INSERT <your table> ON|OFF` szintaxist. Ezzel a szintaxissal explicit módon szúrhat be értékeket az IDENTITY oszlopba.

Sok adatmodellező szeretne előre definiált negatív értékeket használni a dimenzióik bizonyos soraihoz. Erre példa az -1 vagy az "ismeretlen tag" sor.

A következő parancsfájl bemutatja, hogyan lehet explicit módon hozzáadni ezt a sort a SET IDENTITY_INSERT használatával:

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

Az IDENTITY tulajdonság jelenléte hatással van az adatbetöltési kódra. Ez a szakasz kiemeli az adatok táblákba való betöltésének néhány alapvető mintáit az IDENTITY használatával.

Ha adatokat szeretne betölteni egy táblába, és az IDENTITY használatával helyettesítő kulcsot szeretne létrehozni, hozza létre a táblát, majd használja az INSERT parancsot. SELECT vagy INSERT.. értékeket a terhelés végrehajtásához.

A következő példa kiemeli az alapvető mintát:

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
> Jelenleg nem használható, `CREATE TABLE AS SELECT` ha adatokat tölt be egy IDENTITY oszlopot tartalmazó táblába.
>

Az adatok betöltésével kapcsolatos további információkért [lásd: Kivonat, Betöltés és Átalakítás (ELT) a Synapse SQL-készlethez](design-elt-data-loading.md) és [az ajánlott eljárások betöltéséhez.](guidance-for-loading-data.md)

## <a name="system-views"></a>Rendszernézetek

A [sys.identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) katalógusnézetben azonosíthatja az IDENTITY tulajdonsággal rendelkező oszlopot.

Az adatbázisséma jobb megismerése érdekében ez a példa bemutatja, hogyan integrálható a sys.identity_column" más rendszerkatalógus-nézetekkel:

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
- Ha az oszlop egyben a terjesztési kulcs is
- Ha a táblázat külső tábla

A következő kapcsolódó függvények nem támogatottak a Synapse SQL készletben:

- [IDENTITÁS()](/sql/t-sql/functions/identity-function-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="common-tasks"></a>Gyakori feladatok

Ez a szakasz néhány mintakódot tartalmaz, amelyekkel gyakori feladatokat hajthat végre, amikor IDENTITY-oszlopokkal dolgozik.

A C1 oszlop az identitás a következő feladatok mindegyikében.

### <a name="find-the-highest-allocated-value-for-a-table"></a>Tábla legmagasabb kiosztott értékének megkeresése

A `MAX()` függvény segítségével határozhatja meg az elosztott táblához rendelt legmagasabb értéket:

```sql
SELECT MAX(C1)
FROM dbo.T1
```

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Az IDENTITY tulajdonság vetőmagjának és növekményének megkeresése

A katalógusnézetek segítségével felderítheti egy tábla identitásnövekvésés kezdőkonfigurációs értékeit a következő lekérdezés használatával:

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

- [Táblázat – áttekintés](sql-data-warehouse-tables-overview.md)
- [TÁBLA (Transact-SQL) IDENTITY (tulajdonság) LÉTREHOZÁSA](/sql/t-sql/statements/create-table-transact-sql-identity-property?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [DBCC BEHÚZÁS](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
