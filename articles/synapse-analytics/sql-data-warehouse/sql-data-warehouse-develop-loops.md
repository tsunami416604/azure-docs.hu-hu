---
title: T-SQL hurkok használata
description: Tippek a T-SQL hurkok használatával történő megoldásfejlesztéshez és a kurzorok cseréjéhez a Synapse SQL-készletben.
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
ms.openlocfilehash: 4cec4801f2a15ebf858f50377c9718fdacac4e29
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619003"
---
# <a name="using-t-sql-loops-in-synapse-sql-pool"></a>T-SQL hurkok használata a Szinapszis SQL-készletében
Ez a cikk a T-SQL-hurkok használatával és a kurzorok cseréjével történő SQL-készletmegoldások fejlesztésére vonatkozó tippeket tartalmazza.

## <a name="purpose-of-while-loops"></a>A WHILE hurkok célja

A Szinapszis SQL-készlet támogatja a [WHILE](https://docs.microsoft.com/sql/t-sql/language-elements/while-transact-sql?view=sql-server-ver15) ciklust a kimutatásblokkok ismételt végrehajtásához. Ez a WHILE ciklus addig folytatódik, amíg a megadott feltételek teljesülnek, vagy amíg a kód kifejezetten le nem állítja a ciklust a BREAK kulcsszó használatával. 

A hurkok az SQL-kódban definiált kurzorok cseréjéhez hasznosak. Szerencsére, szinte minden kurzorok, amelyek írt SQL-kód a gyors előre, csak olvasható fajta. Tehát, While hurkok egy nagyszerű alternatíva helyett kurzorok.

## <a name="replacing-cursors-in-synapse-sql-pool"></a>Kurzorok cseréje a Synapse SQL-készletben
Azonban, mielőtt búvárkodás a fejét először fel kell tennie magának a következő kérdést: "Lehet ezt a kurzort átírni, hogy használja set-alapú műveletek?" 

Sok esetben a válasz igen, és gyakran a legjobb megközelítés. A set-alapú műveletek gyakran gyorsabban hajtják végre, mint egy iteratív, sorról sorra megközelítés.

A gyors előremutató írásvédett kurzorok könnyen cserélhetők egy hurokszerkezettel. A következő példa egy egyszerű. Ez a példakód frissíti az adatbázis minden táblájának statisztikáit. A ciklusban lévő táblák ismétlésével minden parancs sorrendben hajtható végre.

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

