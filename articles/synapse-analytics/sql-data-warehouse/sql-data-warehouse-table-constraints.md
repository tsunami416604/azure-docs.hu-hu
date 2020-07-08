---
title: Elsődleges, idegen és egyedi kulcsok
description: Táblázatos korlátozások a szinapszis SQL-készlet támogatása az Azure szinapszis Analyticsben
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 562e2cce317d8774ecf72971d53be4f66f9c3da4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85212768"
---
# <a name="primary-key-foreign-key-and-unique-key-in-synapse-sql-pool"></a>Elsődleges kulcs, külső kulcs és egyedi kulcs a szinapszis SQL-készletben

Ismerje meg a szinapszis SQL-készletben található táblázatos korlátozásokat, beleértve az elsődleges kulcsot, a külső kulcsot és az egyedi kulcsot.

## <a name="table-constraints"></a>Táblakorlátozások

A szinapszis SQL-készlet a következő táblázatos korlátozásokat támogatja: 
- Az elsődleges kulcs csak akkor támogatott, ha nem FÜRTÖZÖTT és nem KÉNYSZERÍTett érték is használatban van.    
- Az egyedi korlátozás csak a nem KÉNYSZERÍTett használata esetén támogatott.

A szintaxishoz kattintson az [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) és a [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse)elemre. 

A FOREIGN KEY korlátozás nem támogatott a szinapszis SQL-készletben.  


## <a name="remarks"></a>Megjegyzések

Az elsődleges kulcs és/vagy egyedi kulcs lehetővé teszi a szinapszis SQL Pool Engine számára, hogy optimális végrehajtási tervet készítsen a lekérdezésekhez.  Az elsődleges kulcs oszlopában vagy egyedi korlátozási oszlopában szereplő összes értéknek egyedinek kell lennie.

Miután létrehozott egy elsődleges kulccsal vagy egyedi megkötéssel rendelkező táblázatot a szinapszis SQL-készletben, a felhasználóknak meg kell győződniük arról, hogy az oszlopok összes értéke egyedi.  Ennek megsértése miatt előfordulhat, hogy a lekérdezés pontatlan eredményt ad vissza.  Ez a példa azt mutatja be, hogyan lehet a lekérdezés pontatlan eredményt adni, ha az elsődleges kulcs vagy az egyedi megkötés oszlop duplikált értékeket tartalmaz.  

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

Hozzon létre egy szinapszis SQL Pool-táblázatot egy elsődleges kulccsal: 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```
A szinapszis SQL Pool-tábla létrehozása egyedi korlátozással:

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>További lépések

Miután létrehozta a szinapszis SQL-készlethez tartozó táblákat, a következő lépés az adatai betöltése a táblába. Betöltési oktatóanyagért lásd: [az adatgyűjtés a SZINAPSZIS SQL-készletbe](load-data-wideworldimportersdw.md).
