---
title: T-SQL hurkok használata
description: Tippek a T-SQL hurkok használatához, a kurzorok cseréjéhez és a kapcsolódó megoldások fejlesztéséhez a Synapse SQL-ben lévő SQL-készlettel.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: baff2806b1a8c3c99546365c2496238c24b2b243
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429953"
---
# <a name="using-t-sql-loops-in-synapse-sql"></a>T-SQL hurkok használata a Synapse SQL-ben
Ez a cikk alapvető tippeket ad a T-SQL hurkok használatához, a kurzorok cseréjéhez és a kapcsolódó megoldások fejlesztéséhez a Synapse SQL-ben.

## <a name="purpose-of-while-loops"></a>A WHILE hurkok célja

A Synapse SQL támogatja a [WHILE](https://docs.microsoft.com/sql/t-sql/language-elements/while-transact-sql?view=sql-server-ver15) ciklust a kimutatásblokkok ismételt végrehajtásához. Ez a WHILE ciklus addig folytatódik, amíg a megadott feltételek teljesülnek, vagy amíg a kód kifejezetten le nem állítja a ciklust a BREAK kulcsszó használatával. 

Az SQL-készletben lévő hurkok hasznosak az SQL-kódban definiált kurzorok cseréjéhez. Szerencsére, szinte minden kurzorok, amelyek írt SQL-kód a gyors előre, csak olvasható fajta. Tehát a [WHILE] hurkok nagyszerű alternatívát jelentenek a kurzorok cseréjére.

## <a name="replacing-cursors-in-sql-pool"></a>Kurzorok cseréje az SQL-készletben

A merülés előtt a következő kérdést kell figyelembe venni: "Átírható-e ez a kurzor set-alapú műveletek használatára?" Sok esetben a válasz igen, és gyakran a legjobb megközelítés. A set-alapú művelet gyakran gyorsabban hajtható végre, mint egy iteratív, sorról sorra megközelítés.

A gyors előremutató írásvédett kurzorok könnyen helyettesíthetők egy hurokszerkezettel. A következő kód egy egyszerű példa. Ez a példakód frissíti az adatbázis minden táblájának statisztikáit. A ciklusban lévő táblák ismétlésével minden parancs sorrendben hajtható végre.

Először hozzon létre egy ideiglenes táblát, amely egyedi sorszámot tartalmaz az egyes kimutatások azonosítására:

```sql
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

Másodszor inicializálja a ciklus végrehajtásához szükséges változókat:

```sql
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Most hurok alatt nyilatkozatok végrehajtó őket egyenként:

```sql
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Végül dobja el az első lépésben létrehozott ideiglenes táblát

```sql
DROP TABLE #tbl;
```

## <a name="next-steps"></a>További lépések

További fejlesztési tippeket a [fejlesztés áttekintése című témakörben talál.](develop-overview.md)
