---
title: Az Azure SQL Data Warehouse a T-SQL-hurkok használatával |} A Microsoft Docs
description: Tippek a T-SQL-hurkok használatával, és cserélje le az Azure SQL Data Warehouse a kurzorok használható megoldások fejlesztéséhez.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: b7c21566916c9728900e69dc6480098fadae7622
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301208"
---
# <a name="using-t-sql-loops-in-sql-data-warehouse"></a>Az SQL Data Warehouse a T-SQL-hurkok használatával
Tippek a T-SQL-hurkok használatával, és cserélje le az Azure SQL Data Warehouse a kurzorok használható megoldások fejlesztéséhez.

## <a name="purpose-of-while-loops"></a>Hurkok közben célját

SQL Data Warehouse támogat a [közben](/sql/t-sql/language-elements/while-transact-sql) hurok ismételten a blokkok utasítás végrehajtása. Ez KICSIT ciklus addig a, ha a megadott feltétel igaz, vagy amíg a kód kifejezetten megszakítja a hurok BREAK kulcsszó használatával. Hurkok hasznosak a kurzorok meghatározott SQL-kód cseréje. Szerencsére az SQL-kódot írt, szinte az összes kurzorok a gyors előre, csak olvasható fajta vannak. Ezért [BÁR] ciklusok egy remek alternatívát kínál a kurzorok cseréje.

## <a name="replacing-cursors-in-sql-data-warehouse"></a>Az SQL Data Warehouse a kurzorok cseréje
Azonban mielőtt belevágna a fő először meg kell tegye fel magának a következő kérdést: "sikerült a kurzor kell írni, használja a set-alapú operations?." Sok esetben a válasz Igen és gyakran a legjobb módszer. A set-alapú művelet gyakran gyorsabb, mint egy iteratív, soronként megközelítéssel hajt végre.

Gyors előre csak olvasható kurzorok uvozuje konstruktor cyklu egyszerűen lehet cserélni. Az alábbiakban egy egyszerű példa látható. Ez a Kódpélda frissíti az adatbázis minden táblájához statisztikai adatait. Által léptetés keresztül a hurok található táblák, minden egyes parancsot végrehajtja a sorrendben.

Először hozzon létre egy ideiglenes tábla az egyes utasításokat azonosítására használt egyedi sorral tartalmazza:

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

Másodszor inicializálja a változókat a hurok végrehajtásához szükséges:

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Most már végighaladásra utasítások végrehajtása őket egy egyszerre:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Végül dobja el az első lépésben létrehozott ideiglenes táblán

```
DROP TABLE #tbl;
```

## <a name="next-steps"></a>További lépések
További fejlesztési tippek: [fejlesztői áttekintés](sql-data-warehouse-overview-develop.md).

