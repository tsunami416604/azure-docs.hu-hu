---
title: T-SQL hurkok használata
description: Tippek a T-SQL hurkok használatához és a kurzorok cseréjéhez az Azure SQL Data Warehouse-ban a megoldások fejlesztéséhez.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: afb2160cb9b4e34d3d17db86bac9cd3be79886d0
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351592"
---
# <a name="using-t-sql-loops-in-sql-data-warehouse"></a>T-SQL hurkok használata az SQL Data Warehouse-ban
Tippek a T-SQL hurkok használatához és a kurzorok cseréjéhez az Azure SQL Data Warehouse-ban a megoldások fejlesztéséhez.

## <a name="purpose-of-while-loops"></a>A WHILE hurkok célja

Az SQL Data Warehouse támogatja a [WHILE](/sql/t-sql/language-elements/while-transact-sql) hurkot az utasításblokkok ismételt végrehajtásához. Ez a WHILE ciklus addig folytatódik, amíg a megadott feltételek teljesülnek, vagy amíg a kód kifejezetten le nem állítja a ciklust a BREAK kulcsszó használatával. A hurkok az SQL-kódban definiált kurzorok cseréjéhez hasznosak. Szerencsére, szinte minden kurzorok, amelyek írt SQL-kód a gyors előre, csak olvasható fajta. Ezért a [WHILE] hurkok nagyszerű alternatívát jelentenek a kurzorok cseréjére.

## <a name="replacing-cursors-in-sql-data-warehouse"></a>Kurzorok cseréje az SQL Data Warehouse-ban
Azonban, mielőtt búvárkodás a fejét először fel kell tennie magának a következő kérdést: "Lehet ezt a kurzort átírni, hogy használja set-alapú műveletek?." Sok esetben a válasz igen, és gyakran a legjobb megközelítés. A set-alapú műveletek gyakran gyorsabban hajtják végre, mint egy iteratív, sorról sorra megközelítés.

A gyors előremutató írásvédett kurzorok könnyen cserélhetők egy hurokszerkezettel. A következő egy egyszerű példa. Ez a példakód frissíti az adatbázis minden táblájának statisztikáit. A ciklusban lévő táblák ismétlésével minden parancs sorrendben hajtható végre.

Először hozzon létre egy ideiglenes táblát, amely egyedi sorszámot tartalmaz az egyes kimutatások azonosítására:

```
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

Másodszor, inicializálja a változó végrehajtásához szükséges ciklus:

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Most hurok alatt nyilatkozatok végrehajtó őket egyenként:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Végül dobja el az első lépésben létrehozott ideiglenes táblát

```
DROP TABLE #tbl;
```

## <a name="next-steps"></a>További lépések
További fejlesztési tippeket a [fejlesztés áttekintése című témakörben talál.](sql-data-warehouse-overview-develop.md)

