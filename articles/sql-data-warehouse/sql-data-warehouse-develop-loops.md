---
title: T-SQL hurkok használatával az Azure SQL Data Warehouse |} Microsoft Docs
description: Tippek a T-SQL hurkok használatával, és a kurzorok az Azure SQL Data Warehouse adattárházzal történő, megoldások cseréje.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: ''
ms.assetid: f3384b81-b943-431b-bc73-90e47e4c195f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 2e49a0de0e4a6aba6639f7f3100f41c8db254220
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
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

