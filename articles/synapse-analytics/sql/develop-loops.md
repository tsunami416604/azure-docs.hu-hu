---
title: T-SQL-hurkok használata
description: Tippek a T-SQL-hurkok használatához, a kurzorok cseréjéhez és a kapcsolódó megoldások fejlesztéséhez az SQL-készlettel a szinapszis SQL-ben.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: ec15f87bdbf381b71faa7cefd986dd5e55e17a19
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2020
ms.locfileid: "85964991"
---
# <a name="use-t-sql-loops-in-synapse-sql"></a>T-SQL-hurkok használata a szinapszis SQL-ben
Ez a cikk a T-SQL-hurkok használatával, a kurzorok cseréjével és az SQL-készlettel kapcsolatos kapcsolódó megoldások fejlesztésével kapcsolatos alapvető tippeket tartalmaz a szinapszis SQL-ben.

## <a name="purpose-of-while-loops"></a>Hurkok célja

A szinapszis SQL a [while](https://docs.microsoft.com/sql/t-sql/language-elements/while-transact-sql?view=sql-server-ver15) ciklust is támogatja az utasítások ismételt végrehajtásához. Ez a ciklus addig folytatódik, amíg a megadott feltételek teljesülnek, vagy amíg a kód kifejezetten leállítja a hurkot a BREAK kulcsszó használatával. 

Az SQL-készletben található hurkok hasznosak az SQL-kódban definiált kurzorok cseréjéhez. Szerencsére az SQL Code-ban írt összes kurzor a gyors továbbítás, csak olvasható fajta. Tehát a [WHILE] hurkok nagyszerű alternatíva a kurzorok cseréjéhez.

## <a name="replacing-cursors-in-sql-pool"></a>Kurzorok cseréje az SQL-készletben

A bekövetkezett merülés előtt a következő kérdést kell figyelembe venni: "lehetséges, hogy ez a kurzor a beállított műveletek használatára lett átírva?" Sok esetben a válasz igen, és gyakran a legjobb megoldás. A készleten alapuló művelet gyakran gyorsabb, mint egy iterációs, soron belüli módszer.

A gyors továbbítást csak olvasható kurzorok egyszerűen lecserélhető hurkos szerkezettel. A következő kód egy egyszerű példa. Ez a kódrészlet frissíti az adatbázis minden táblájának statisztikáit. Ha megismétli a hurokban lévő táblákat, az egyes parancsok végrehajtása sorban történik.

Először hozzon létre egy ideiglenes táblázatot, amely egy egyedi sorszámot tartalmaz, amely az egyes utasítások azonosítására szolgál:

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

Másodszor, inicializálja a hurok végrehajtásához szükséges változókat:

```sql
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Most a következő utasításokon keresztül hajtja végre az egyes utasításokat:

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

További fejlesztési tippek: a [fejlesztés áttekintése](develop-overview.md).
