---
title: Elsődleges, idegen és egyedi kulcsok
description: A táblakényszerek támogatása a Synapse SQL-készletben az Azure Synapse Analytics-ben
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f97163d02836442430037e18439bcf0724046332
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80990769"
---
# <a name="primary-key-foreign-key-and-unique-key-in-synapse-sql-pool"></a>Elsődleges kulcs, idegen kulcs és egyedi kulcs a Szinapszis SQL-készletében

Ismerje meg a synapse SQL-készlet táblakényszereit, beleértve az elsődleges kulcsot, az idegen kulcsot és az egyedi kulcsot.

## <a name="table-constraints"></a>Táblakorlátozások

A Synapse SQL-készlet támogatja a következő táblakényszereket: 
- A PRIMEP KULCS csak akkor támogatott, ha a NONCLUSTERED és a NOT ENFORCED egyaránt használatos.    
- A UNIQUE megkötés csak a NOT ENFORCED beállítással támogatott.

Szintaxis esetén jelölje be [a TÁBLA MÓDOSÍTÁSA](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) és A TÁBLA LÉTREHOZÁSA [jelölőnégyzetet.](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse) 

Foreign KEY megkötés nem támogatott a Szinapszis SQL-készlet.  


## <a name="remarks"></a>Megjegyzések

Az elsődleges kulcs és/vagy az egyedi kulcs lehetővé teszi, hogy a Synapse SQL-készletmotor optimális végrehajtási tervet hozzon létre egy lekérdezéshez.  Az elsődleges kulcs oszlopában vagy egyedi megkötésoszlopában lévő összes értéknek egyedinek kell lennie.

Miután létrehozott egy táblát elsődleges kulccsal vagy egyedi megkötéssel a Synapse SQL-készletben, a felhasználóknak meg kell győződniük arról, hogy az oszlopokban lévő összes érték egyedi.  Ennek megsértése miatt a lekérdezés pontatlan eredményt adhat vissza.  Ez a példa azt mutatja be, hogy a lekérdezés hogyan adhat vissza pontatlan eredményt, ha az elsődleges kulcs vagy az egyedi megkötésoszlop ismétlődő értékeket tartalmaz.  

```sql
 -- Create table t1
CREATE TABLE t1 (a1 INT NOT NULL, b1 INT) WITH (DISTRIBUTION = ROUND_ROBIN)

-- Insert values to table t1 with duplicate values in column a1.
INSERT INTO t1 VALUES (1, 100)
INSERT INTO t1 VALUES (1, 1000)
INSERT INTO t1 VALUES (2, 200)
INSERT INTO t1 VALUES (3, 300)
INSERT INTO t1 VALUES (4, 400)

-- Run this query.  No primary key or unique constraint.  4 rows returned. Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
1           2
2           1
3           1
4           1

(4 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 ADD CONSTRAINT unique_t1_a1 unique (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, count(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key constraint
ALTER TABLE t1 add CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Manually fix the duplicate values in a1
UPDATE t1 SET a1 = 0 WHERE b1 = 1000

-- Verify no duplicate values in column a1 
SELECT * FROM t1

/*
a1          b1
----------- -----------
2           200
3           300
4           400
0           1000
1           100

(5 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 add CONSTRAINT unique_t1_a1 UNIQUE (a1) NOT ENFORCED  

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) as total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key contraint
ALTER TABLE t1 ADD CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

```

## <a name="examples"></a>Példák

Hozzon létre egy synapse SQL-készlettáblát elsődleges kulccsal: 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```
Hozzon létre egy szinapszis SQL-készlettáblát egyedi megkötéssel:

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>További lépések

A synapse SQL-készlet tábláinak létrehozása után a következő lépés az adatok betöltése a táblába. A betöltési oktatóanyagról az [Adatok betöltése a Synapse SQL-készletbe című](load-data-wideworldimportersdw.md)témakörben található.
