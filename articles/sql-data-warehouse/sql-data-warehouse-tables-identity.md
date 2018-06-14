---
title: Helyettesítő kulcsok - Azure SQL Data Warehouse létrehozásához AZONOSÍTÓJÁNAK használatával |} Microsoft Docs
description: Javaslatok és Példák helyettesítő kulcsok létrehozása az Azure SQL Data Warehouse táblákon azonosító tulajdonsággal.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: ab028705f5af7c37017d2e697240b7d3436f5f71
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31526984"
---
# <a name="using-identity-to-create-surrogate-keys-in-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse helyettesítő kulcsok létrehozásához AZONOSÍTÓJÁNAK használatával
Javaslatok és Példák helyettesítő kulcsok létrehozása az Azure SQL Data Warehouse táblákon azonosító tulajdonsággal.

## <a name="what-is-a-surrogate-key"></a>Mi az a helyettes kulcs?
Helyettes kulcs egy tábla minden egyes sorára egyedi azonosító oszlop. A kulcs nem jön létre a tábla adatai. Adatok modelers, például azok a data warehouse modellek tervezésekor helyettesítő kulcsok létrehozásához a táblákon. Az IDENTITÁS tulajdonsággal e cél eléréséhez egyszerű és hatékony terhelés teljesítmény befolyásolása nélkül.  

## <a name="creating-a-table-with-an-identity-column"></a>Az azonosító oszlop a tábla létrehozása
Az azonosító tulajdonság célja, hogy az nem befolyásolja a terhelés teljesítmény között az adatraktár összes terjesztési kiterjesztése. IDENTITÁS megvalósítása ezért objektumorientált felé ezen célok eléréséhez. 

Egy táblát az azonosító tulajdonság rendelkezőként, amikor először hoz létre a tábla a következő utasítás hasonló szintaxissal definiálhatja:

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

Ezután `INSERT..SELECT` a táblázat feltöltéséhez.

Ez a szakasz a többi teljes körűen jobb megértése érdekében a végrehajtási apró mutatja be.  

### <a name="allocation-of-values"></a>Foglalási értékek
Az azonosító tulajdonság nem biztosítja a sorrendben, amelyben a helyettesítő értékek le van foglalva, amely tükrözi a működését, az SQL Server és az Azure SQL Database. Azonban az Azure SQL Data Warehouse garancia hiányában hangsúlyozottan. 

Az alábbi példában a következő ábrát:

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

Az előző példában két sort terjesztési 1 ki. Az első sort tartalmaz a helyettesítő értéke 1 oszlopban `C1`, és a második sornak 61 helyettesítő értékét. Mindkét ezeket az értékeket az azonosító tulajdonság hoztak létre. Azonban nincs összefüggő a foglalási érték. Ez a működésmód szándékos.

### <a name="skewed-data"></a>Kihasználtságot adatok 
A típusú értékek egyenlően elosztva a terjesztési. Ha egy elosztott táblában romlik a kihasználtságot adatokból, majd az adattípus számára elérhető értékek is felhasználhatók, idő előtt. Például ha az adatok egyetlen terjesztési fejeződik be, majd hatékonyan a tábla el tudja érni csak-hatvanad adattípusú érték. Ezért az azonosító tulajdonság korlátozva `INT` és `BIGINT` adattípus csak.

### <a name="selectinto"></a>VÁLASSZON... A
Meglévő azonosító oszlop van kijelölve, egy új táblába, az új oszlop örökli az azonosító tulajdonság, kivéve, ha a következő feltételek valamelyike teljesül:
- A SELECT utasítás illesztést tartalmaz.
- A UNION összekapcsolhatók több KIVÁLASZTÓ utasítást.
- Az azonosító oszlop szerepel a kiválasztási listán egynél többször.
- Az azonosító oszlop egy kifejezés része.
    
Ha ezek a feltételek bármelyike teljesül, az oszlop nem NULL helyett az azonosító tulajdonság öröklődés jön létre.

### <a name="create-table-as-select"></a>TABLE AS SELECT LÉTREHOZÁSA
A következő dokumentált válassza ki az SQL Server viselkedést létrehozása TABLE AS kiválasztása (CTAS)... . Azonban nem adhat meg egy azonosító tulajdonság oszlop definíciójában a `CREATE TABLE` része az utasítást. Az IDENTITY függvény a is használhatja a `SELECT` a CTAS része. A táblázat feltöltéséhez, kell használnia `CREATE TABLE` megadhatók a tábla követ `INSERT..SELECT` való feltöltése.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Explicit módon értékek beszúrása azonosító oszlop 
Támogatja az SQL Data Warehouse `SET IDENTITY_INSERT <your table> ON|OFF` szintaxist. Ez a szintaxis segítségével explicit módon értékek beszúrása az identitásoszlop.

Sok adatok modelers, például a dimenziók bizonyos soraihoz előre meghatározott negatív értékek használata. Példa: "az ismeretlen tag" sor vagy a -1. 

A következő parancsfájl bemutatja, hogyan explicit módon adja hozzá a sor IDENTITY_INSERT beállítása használatával:

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

Az azonosító tulajdonság jelenléte rendelkezik néhány hatással vannak az Adatbetöltési kód. Ez a szakasz néhány alapvető mintázatokból az adatok táblába töltéséhez identitásával mutatja be. 

Adatok betöltése a következő táblába, és hozzon létre egy helyettesítő kulcsot használva IDENTITY, hozzon létre a táblát, és ezután használja az INSERT... Válassza ki, vagy szúrja be. A betöltése ÉRTÉKEKET.

A következő példa az alapvető mintát információk találhatók:
 
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

SELECT  *
FROM    dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE] 
> Nincs lehetséges létrehozása TABLE AS kiválasztása a "jelenleg, amikor az adatok betöltését azonosító oszlopot tartalmazó tábla.
> 

Az adatok betöltéséhez további információkért lásd: [tervezése bontsa ki, betöltés és átalakítás (ELT) az Azure SQL Data Warehouse](design-elt-data-loading.md) és [gyakorlati tanácsok betöltése](guidance-for-loading-data.md).


## <a name="system-views"></a>Rendszernézetek
Használhatja a [sys.identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql) katalógus nézetre, és azonosíthatja a azonosítótulajdonság tartalmazó oszlop.

Jobb megértése érdekében az adatbázis-séma segítségével a példa bemutatja, hogyan integrálható sys.identity_column "a többi rendszer katalógus nézetek:

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
Az azonosító tulajdonság nem használható:
- Ha az oszlop adattípusához nincs INT vagy BIGINT
- Ha az oszlop értéke is a terjesztési kulcs
- Ha a tábla-e a külső tábla 

A következő kapcsolódó funkciók nem támogatottak az SQL Data Warehouse:

- [IDENTITY()](/sql/t-sql/functions/identity-function-transact-sql)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql)
- [IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql)
- [DBCC CHECK_IDENT()](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql)

## <a name="common-tasks"></a>Gyakori feladatok

Ez a témakör néhány mintakód segítségével végrehajthat olyan gyakori feladatokat azonosító oszlop használata. 

Oszlop C1 az alábbi feladataival IDENTITÁSA.
 
 
### <a name="find-the-highest-allocated-value-for-a-table"></a>A legmagasabb lefoglalt érték található egy táblához
Használja a `MAX()` működnek, mint a legmagasabb érték egy elosztott tábla számára lefoglalt meghatározásához:

```sql
SELECT  MAX(C1)
FROM    dbo.T1
``` 

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Az azonosító tulajdonság a kezdőértékek és növekményértékek keresése
A katalógus nézetek segítségével mezőértékek identitás növekvő, mind a seed konfigurációs tábla a következő lekérdezés segítségével: 

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

* Táblák fejlesztésével kapcsolatos további tudnivalókért lásd: a [táblázat áttekintése] [áttekintése].  

