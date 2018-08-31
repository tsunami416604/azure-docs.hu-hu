---
title: IDENTITÁS használatával helyettes kulcsok – Azure SQL Data Warehouse létrehozása |} A Microsoft Docs
description: Javaslatok és a példák az azonosító tulajdonságot helyettes kulcsok létrehozásához az Azure SQL Data Warehouse táblákon.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: abe245e6174cb947e78252941c71ce6857b77f77
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306791"
---
# <a name="using-identity-to-create-surrogate-keys-in-azure-sql-data-warehouse"></a>Helyettes kulcsok létrehozásához az Azure SQL Data Warehouse IDENTITÁS használata
Javaslatok és a példák az azonosító tulajdonságot helyettes kulcsok létrehozásához az Azure SQL Data Warehouse táblákon.

## <a name="what-is-a-surrogate-key"></a>Mit jelent a helyettes kulcs?
Egy tábla helyettes kulcs minden egyes sorához egy egyedi azonosítót tartalmazó oszlop. A kulcs nem jön létre a tábla adataiból. Adatmodellezők, ha azokat a data warehouse modellek tervezésekor a táblák helyettes-kulcsok létrehozása. Az azonosító tulajdonság segítségével egyszerűen és hatékonyan betöltési teljesítmény befolyásolása nélkül a cél elérése érdekében.  

## <a name="creating-a-table-with-an-identity-column"></a>Az IDENTITY oszlopot tartalmazó tábla létrehozása
Az adatraktár összes a disztribúciók közötti anélkül, hogy befolyásolná a betöltési teljesítmény horizontális felskálázása célja az azonosító tulajdonságot. Az IDENTITÁS megvalósítását ezért objektumorientált felé ezen célok eléréséhez. 

Egy táblázat, hogy az azonosító tulajdonságot, ha a tábla létrehozásához, amely a következő utasítás hasonló szintaxissal definiálhatja:

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

Ez a szakasz további része a vállalatánál segítenek megérteni azok teljes körűen megvalósítása emeli ki.  

### <a name="allocation-of-values"></a>Foglalási érték
Az azonosító tulajdonság nem garantálja a sorrendet, amelyben a helyettes értékek le van foglalva, amely tükrözi a működését, az SQL Server és az Azure SQL Database. Azonban az Azure SQL Data Warehouse garancia hiányában hangsúlyozottan. 

Az alábbi példa egy ábra:

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

Az előző példában két sort terjesztési 1 érkezett. Az első sor érték szerepel-e helyettesítő 1 oszlopban `C1`, és a második sor 61 helyettes érték szerepel-e. Az azonosító tulajdonság által generált mindkét ezeket az értékeket. Az értékek a felosztás azonban nem áll egybefüggően áll rendelkezésre. Ez a működésmód szándékos.

### <a name="skewed-data"></a>Magokon adatok 
Az adattípus értékek egyenlően oszlik el a disztribúciók közötti. Ha egy elosztott tábla romlik a magokon adatokból, majd a datatype számára elérhető értékek is felhasználhatók, idő előtt. Például minden adat egyetlen terjesztési fejeződik be, ha majd hatékonyan a tábla hozzáfér csak-hatvanad értékei közül melyik adattípus. Ezért az azonosító tulajdonság korlátozva `INT` és `BIGINT` adattípusok csak.

### <a name="selectinto"></a>VÁLASSZON... BE
Egy meglévő azonosító oszlop van kijelölve, egy új táblába, az új oszlop örökli az azonosító tulajdonságot, ha a következő feltételek valamelyike teljesül:
- A SELECT utasítás illesztést tartalmaz.
- Több SELECT utasítás UNION használatával csatlakoznak.
- Az azonosító oszlop szerepel a kiválasztási listán egynél több alkalommal.
- Az azonosító oszlop egy kifejezés részét képezi.
    
Ha ezek a feltételek bármelyike teljesül, az oszlop nem helyett örökli az azonosító tulajdonság NULL értékű jön létre.

### <a name="create-table-as-select"></a>TABLE AS SELECT LÉTREHOZÁSA
CREATE TABLE AS SELECT (CTAS) a következő SQL Server viselkedést, válassza ki a szerződését... . Azonban nem adhat meg az azonosító tulajdonság oszlop definíciójában a `CREATE TABLE` része az utasítást. Az IDENTITY függvény is használható a `SELECT` a CTAS része. Egy táblázat feltöltéséhez kell használnia `CREATE TABLE` meghatározásához a tábla követ `INSERT..SELECT` feltölti azt.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Explicit módon a egy identitásoszlop értékek beszúrása 
Támogatja az SQL Data Warehouse `SET IDENTITY_INSERT <your table> ON|OFF` szintaxist. Ez a szintaxis segítségével explicit módon értékeket beszúrni az identitásoszlop.

Számos adatmodellezők, ha előre definiált negatív értékek használata a dimenziók bizonyos sorokat. Ilyen például, a -1 vagy az "ismeretlen tag" sort. 

A következő szkript bemutatja, hogyan kifejezetten hozzáadnia a sor az IDENTITY_INSERT beállítása:

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

Az azonosító tulajdonságot jelenléte néhány következményeket vonhat a Adatbetöltési kódhoz. Ez a szakasz néhány alapvető mintázatokból az adatok betöltése a táblákba IDENTITÁS használatával emeli ki. 

Adatok betöltése egy táblába, és hozzon létre egy helyettes kulcs IDENTITÁS használatával, a tábla létrehozásához, majd használja az INSERT... Válassza ki, vagy INSERT... ÉRTÉKEK a terhelés végrehajtásához.

Az alábbi példa az alapvető mintája emeli ki:
 
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
> Már nem tábla AS KÖZ létrehozásához használandó "jelenleg, amikor az adatok betöltését az IDENTITY oszlopot tartalmazó tábla.
> 

Az adatok betöltéséhez további információkért lásd: [tervezése kinyerési, betöltési és átalakítási (ELT) az Azure SQL Data Warehouse](design-elt-data-loading.md) és [ajánlott eljárások betöltése](guidance-for-loading-data.md).


## <a name="system-views"></a>Rendszernézetek
Használhatja a [sys.identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql) nézetre, és a egy oszlop, amely az azonosító tulajdonság azonosítása a katalógus.

Segítségével jobban megismerheti az adatbázissémát, ez a példa bemutatja, hogyan integrálhatja sys.identity_column "az egyéb Rendszerkatalógus-nézetek:

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
- Ha az oszlop adattípusa nem INT, BIGINT, vagy
- Ha az oszlop is az elosztási kulcs
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

Ez a témakör néhány mintakódját hajthat végre gyakori feladatokat, ha IDENTITY oszlopot használhatja. 

Oszlop C1 az IDENTITÁS, az a következő feladatokat.
 
 
### <a name="find-the-highest-allocated-value-for-a-table"></a>Egy tábla a legmagasabb lefoglalt értéket keresi
Használja a `MAX()` függvény a legmagasabb érték egy elosztott tábla számára lefoglalt meghatározásához:

```sql
SELECT  MAX(C1)
FROM    dbo.T1
``` 

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Az azonosító tulajdonságot a kezdőértékek és növekményértékek keresése
A katalógus nézetek segítségével identitás növelési és kezdőérték konfigurációs értékeit tábla felderítése a következő lekérdezés használatával: 

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

* Táblák fejlesztésével kapcsolatos további tudnivalókért lásd: [táblázat áttekintése] [áttekintése].  

