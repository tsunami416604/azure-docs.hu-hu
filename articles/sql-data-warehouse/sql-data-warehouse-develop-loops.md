---
title: "Használja ki az Azure SQL Data Warehouse T-SQL hurkok |} Microsoft Docs"
description: "Tippek a Transact-SQL hurkok és az Azure SQL Data Warehouse adattárházzal történő, megoldások tagjára kurzorok."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: f3384b81-b943-431b-bc73-90e47e4c195f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 40a872ff310f48bfd543ac184fe7301b85b50258
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="loops-in-sql-data-warehouse"></a>Az SQL Data Warehouse hurkok
Az SQL Data Warehouse támogatja a [közben][közben] hurok ismételten a blokkok utasítás végrehajtása. Ez továbbra is a mindaddig, amíg a megadott feltételek teljesülnek, vagy amíg a kód kifejezetten megszakítja a hurok segítségével a `BREAK` kulcsszó. Hurkok különösen hasznosak a kurzorok meghatározott SQL-kód cseréje. Szerencsére szinte minden kurzorok SQL kódban vannak megírva, a gyors előre olvasható csak különböző. Ezért [közben] hurkok kiváló alternatív esetben, ha veszi észre magát felülírni kellene.

## <a name="leveraging-loops-and-replacing-cursors-in-sql-data-warehouse"></a>Hurkok követését és az SQL Data Warehouse kurzorok cseréje
Azonban mielőtt belevágna a head először kérdezze meg magát a következő kérdés: "Sikerült ezt a kurzort újra írni alapú set műveletek használata?". Sok esetben a válasz Igen, és gyakran a legjobb módszer. Gyakran-alapú beállítási művelet jelentősen gyorsabb, mint az ismétlődő, soronként megközelítés hajt végre.

Gyors előre írásvédett kurzorok ismétlési szerkezet könnyen lehet cserélni. Az alábbiakban látható egy egyszerű példa. Ez a Kódpélda frissítése az adatbázisban minden tábla statisztikai adatait. A hurok lévő táblák keresztül léptetés által dolgozunk hajtható végre az egyes parancsok sorrendben.

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


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Következő lépések
További fejlesztési tippek, lásd: [fejlesztői áttekintés][development overview].

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[közben]: https://msdn.microsoft.com/library/ms178642.aspx


<!--Other Web references-->
