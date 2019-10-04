---
title: T-SQL-hurkok használata a Azure SQL Data Warehouseban | Microsoft Docs
description: Tippek a T-SQL-hurkok használatához és a Azure SQL Data Warehouse kurzorok cseréjéhez a megoldások fejlesztéséhez.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: e27edcc1383a235fbdb9513066e69e2f680ea2f9
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479630"
---
# <a name="using-t-sql-loops-in-sql-data-warehouse"></a>T-SQL-hurkok használata a SQL Data Warehouseban
Tippek a T-SQL-hurkok használatához és a Azure SQL Data Warehouse kurzorok cseréjéhez a megoldások fejlesztéséhez.

## <a name="purpose-of-while-loops"></a>Hurkok célja

A SQL Data Warehouse a [while](/sql/t-sql/language-elements/while-transact-sql) ciklust is támogatja az utasítások ismételt végrehajtásához. Ez a ciklus addig folytatódik, amíg a megadott feltételek teljesülnek, vagy amíg a kód kifejezetten leállítja a hurkot a BREAK kulcsszó használatával. A hurkok hasznosak az SQL-kódban definiált kurzorok cseréjéhez. Szerencsére az SQL Code-ban írt összes kurzor a gyors továbbítás, csak olvasható fajta. Ezért a [WHILE] hurkok nagyszerű alternatíva a kurzorok cseréjéhez.

## <a name="replacing-cursors-in-sql-data-warehouse"></a>Kurzorok cseréje SQL Data Warehouse
Először azonban a következő kérdéssel kell megtennie a merülés előtt: "Lehet, hogy ez a kurzor a set-based Operations használatával lett újraírva?" Sok esetben a válasz igen, és gyakran a legjobb megoldás. A készleten alapuló művelet gyakran gyorsabb, mint egy iterációs, soron belüli módszer.

A gyors továbbítást csak olvasható kurzorok egyszerűen lecserélhetik hurkos szerkezettel. A következő egy egyszerű példa. Ez a kódrészlet frissíti az adatbázis minden táblájának statisztikáit. Ha megismétli a hurokban lévő táblákat, az egyes parancsok végrehajtása sorban történik.

Először hozzon létre egy ideiglenes táblázatot, amely egy egyedi sorszámot tartalmaz, amely az egyes utasítások azonosítására szolgál:

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

Másodszor, inicializálja a hurok végrehajtásához szükséges változókat:

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Most a következő utasításokon keresztül hajtja végre az egyes utasításokat:

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
További fejlesztési tippek: a [fejlesztés áttekintése](sql-data-warehouse-overview-develop.md).

