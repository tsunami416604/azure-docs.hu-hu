---
title: Tárolt eljárások használata
description: Tippek a megoldások fejlesztéséhez a tárolt eljárások synapse SQL-készletben történő megvalósításával.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/02/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 3283fbeec2226a825625b4e3ede6942a609ae723
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633444"
---
# <a name="using-stored-procedures-in-synapse-sql-pool"></a>Tárolt eljárások használata a Synapse SQL-készletben

Ez a cikk a tárolt eljárások megvalósításával ismerteti az SQL-készletmegoldások fejlesztését.

## <a name="what-to-expect"></a>Mire számíthat

Az SQL-készlet számos, az SQL Server ben használt T-SQL-szolgáltatást támogat. Ennél is fontosabb, hogy vannak horizontális felskálázási speciális funkciók, amelyek segítségével maximalizálhatja a megoldás teljesítményét.

Emellett az SQL-készlet méretezésének és teljesítményének fenntartása érdekében további funkciók és funkciók is vannak, amelyek viselkedésbeli különbségeket mutatnak.

## <a name="introducing-stored-procedures"></a>A tárolt eljárások bevezetése

A tárolt eljárások nagyszerű en-kódbeágyazása az SQL-kód, amely az SQL-készlet adataiközelében tárolódik. A tárolt eljárások abban is segítenek a fejlesztőknek, hogy a kód kezelhető egységekbe ágyazásával modulárissá tegyezhessék megoldásaikat, megkönnyítve ezzel a nagyobb kódújrafelhasználhatóságot. Minden tárolt eljárás is elfogadja paramétereket, hogy azok még rugalmasabb.

Az SQL-készlet egyszerűsített és egyszerűsített tárolt eljárásmegvalósítást biztosít. A legnagyobb különbség az SQL Serverhez képest, hogy a tárolt eljárás nem előre lefordított kód.

Az adatraktárak esetében a fordítási idő általában kicsi a nagy adatkötetek lekérdezéseinek futtatásához szükséges időhöz képest. Sokkal fontosabb, hogy a tárolt eljáráskód megfelelően legyen optimalizálva a nagy lekérdezésekhez.

> [!TIP]
> A cél az, hogy mentse óra, perc és másodperc, nem ezredmásodperc. Ezért hasznos, ha a tárolt eljárásokat az SQL-logika tárolóiként tekintjük.

Amikor az SQL-készlet végrehajtja a tárolt eljárást, az SQL-utasítások elemzésre kerülnek, lefordítva és optimalizálva futásidőben. A folyamat során minden utasítás elosztott lekérdezésekké alakul át. Az adatokkal végrehajtott SQL-kód eltér a beküldött lekérdezéstől.

## <a name="nesting-stored-procedures"></a>Tárolt eljárások beágyazása

Ha a tárolt eljárások más tárolt eljárásokat hívnak meg, vagy dinamikus SQL-t hajtanak végre, akkor a belső tárolt eljárás vagy kódmeghívás a beágyazottnak lesz kitéve.

Az SQL-készlet legfeljebb nyolc beágyazási szintet támogat. Ezzel szemben az SQL Server fészekszintje 32.

A legfelső szintű tárolt eljáráshívás megegyezik az 1-es fészekszinttel.

```sql
EXEC prc_nesting
```

Ha a tárolt eljárás egy másik EXEC hívást is kezdeményez, a fészek szintje kettőre nő.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```

Ha a második eljárás végrehajtja a dinamikus SQL-t, a fészekszint háromra nő.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Az SQL-készlet jelenleg nem támogatja [a @@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). Mint ilyen, nyomon kell követnie a fészek szintjét. Nem valószínű, hogy túllépi a nyolc fészekszint korlátot. Ha azonban igen, át kell dolgoznia a kódot, hogy elférjen a beágyazási szintek ezen a korláton belül.

## <a name="insertexecute"></a>Beszúrása.. Végre

Az SQL-készlet nem teszi lehetővé az INSERT utasítással tárolt eljárás eredménykészletének felhasználását. Van azonban egy alternatív megközelítés, amit használhat. Például az [ideiglenes táblákról](sql-data-warehouse-tables-temporary.md)szóló cikket.

## <a name="limitations"></a>Korlátozások

A Transact-SQL tárolt eljárásainak vannak olyan aspektusai, amelyek nincsenek megvalósítva az SQL készletben, amelyek a következők:

* ideiglenestárolt eljárások
* számozott tárolt eljárások
* kiterjesztett tárolt eljárások
* CLR tárolt eljárások
* titkosítási beállítás
* replikációs beállítás
* táblaértékű paraméterek
* írásvédett paraméterek
* alapértelmezett paraméterek
* végrehajtási környezetek
* visszáru-kimutatás

## <a name="next-steps"></a>További lépések

További fejlesztési tippeket a [fejlesztés áttekintése című témakörben talál.](sql-data-warehouse-overview-develop.md)
