---
title: Elsődleges, idegen és egyedi kulcsok
description: A Table korlátozásai támogatják az SQL Analytics használatát az Azure szinapszis Analyticsben
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 0379bed08c3ee6931e931a78a2d2c91664535250
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198133"
---
# <a name="primary-key-foreign-key-and-unique-key-in-sql-analytics"></a>Elsődleges kulcs, külső kulcs és egyedi kulcs az SQL Analyticsben

Ismerje meg az SQL-elemzések táblázatos korlátozásait, beleértve az elsődleges kulcsot, a külső kulcsot és az egyedi kulcsot.

## <a name="table-constraints"></a>Táblakorlátozások 
Az SQL Analytics a következő táblázatos korlátozásokat támogatja: 
- Az elsődleges kulcs csak akkor támogatott, ha nem FÜRTÖZÖTT és nem KÉNYSZERÍTett érték is használatban van.    
- Az egyedi korlátozás csak a nem KÉNYSZERÍTett használata esetén támogatott.   

A FOREIGN KEY korlátozás nem támogatott az SQL Analyticsben.  

## <a name="remarks"></a>Megjegyzések
Az elsődleges kulcs és/vagy egyedi kulcs lehetővé teszi, hogy az SQL Analytics motor optimális végrehajtási tervet készítsen a lekérdezésekhez.  Az elsődleges kulcs oszlopában vagy egyedi korlátozási oszlopában szereplő összes értéknek egyedinek kell lennie. 

Az SQL Analyticsben elsődleges kulccsal vagy egyedi megkötéssel rendelkező tábla létrehozása után a felhasználóknak meg kell győződniük arról, hogy az oszlopok összes értéke egyedi.  Ennek megsértése miatt előfordulhat, hogy a lekérdezés pontatlan eredményt ad vissza.  Ez a példa azt mutatja be, hogyan lehet a lekérdezés pontatlan eredményt adni, ha az elsődleges kulcs vagy az egyedi megkötés oszlop duplikált értékeket tartalmaz.  

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
SQL Analytics-tábla létrehozása elsődleges kulccsal: 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```
Hozzon létre egy egyedi korlátozással rendelkező SQL Analytics-táblázatot:

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>További lépések

Miután létrehozta az SQL Analytics-adatbázishoz tartozó táblákat, a következő lépésben be kell töltenie az adatait a táblába. A betöltési oktatóanyagért lásd: [az SQL Analytics-adatbázisok betöltése](load-data-wideworldimportersdw.md).
