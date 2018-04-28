---
title: T-SQL hurkok használatával az Azure SQL Data Warehouse |} Microsoft Docs
description: Tippek a T-SQL hurkok használatával, és a kurzorok az Azure SQL Data Warehouse adattárházzal történő, megoldások cseréje.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 8d51c8f18d7c00d21fcc057efcda73e2a6b46cc7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="using-t-sql-loops-in-sql-data-warehouse"></a>Az SQL Data Warehouse T-SQL hurkok használatával
Tippek a T-SQL hurkok használatával, és a kurzorok az Azure SQL Data Warehouse adattárházzal történő, megoldások cseréje.

## <a name="purpose-of-while-loops"></a>Hurkok közben célja

Az SQL Data Warehouse támogatja a [közben](/sql/t-sql/language-elements/while-transact-sql) hurok ismételten a blokkok utasítás végrehajtása. Ez IGÉNYBE hurok továbbra is fennáll az, ha a megadott feltételek igaz, vagy amíg a kód kifejezetten megszakítja a hurok BREAK kulcsszó használatával. Kurzorok SQL kódban megadott helyett hurkokat hasznosak. Szerencsére a gyors előre, csak olvasható fajta vannak szinte minden kurzorok SQL kódban vannak megírva. Ezért [közben] hurkok egy nagyszerű alternatív kurzorok helyettesítésére.

## <a name="replacing-cursors-in-sql-data-warehouse"></a>Az SQL Data Warehouse kurzorok cseréje
Azonban mielőtt belevágna a head először kérdezze meg magát a következő kérdés: "sikerült ezt a kurzort kell írni set-alapú műveletek használandó?." Sok esetben a válasz Igen és gyakran a legjobb módszer. A set-alapú művelet gyakran gyorsabb, mint az ismétlődő, soronként megközelítés hajt végre.

Gyors előre írásvédett kurzorok ismétlési szerkezet könnyen lehet cserélni. Egy egyszerű példa a következő: Ez a Kódpélda frissítése az adatbázisban minden tábla statisztikai adatait. Minden parancs által léptetés keresztül a hurok lévő táblák, sorrendben hajtja végre.

Először hozzon létre egy ideiglenes tábla egyéni utasítások azonosítására használt egyedi sor sorszámát tartalmazza:

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

Második inicializálni a hurok elvégzéséhez szükséges változók:

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Most hurokba építhető utasítás végrehajtása, azokat egy egyszerre:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Végül dobja el az ideiglenes tábla az első lépésben létrehozott

```
DROP TABLE #tbl;
```

## <a name="next-steps"></a>További lépések
További fejlesztési tippek, lásd: [fejlesztői áttekintés](sql-data-warehouse-overview-develop.md).

