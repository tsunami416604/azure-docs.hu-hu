---
title: Tárolt eljárások használata
description: Tippek a megoldások fejlesztéséhez a szinapszis SQL-készletben tárolt eljárások megvalósításával.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/02/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 3ffdf7a66c2562b43fc2ed02bb088ab1095118fb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416162"
---
# <a name="using-stored-procedures-in-synapse-sql-pool"></a>Tárolt eljárások használata a szinapszis SQL-készletben

Ez a cikk tippekkel szolgál az SQL Pool-megoldások fejlesztéséhez tárolt eljárások megvalósításával.

## <a name="what-to-expect"></a>Mire számíthat

Az SQL-készlet számos olyan T-SQL-funkciót támogat, amelyek a SQL Serverben használatosak. Még ennél is fontosabb, hogy a megoldás teljesítményének maximalizálása érdekében Felskálázási funkciók érhetők el.

Emellett az SQL-készlet méretezésének és teljesítményének fenntartása érdekében további funkciókat és funkciókat is tartalmaz, amelyek viselkedési különbségekkel rendelkeznek.

## <a name="introducing-stored-procedures"></a>Tárolt eljárások bevezetése

A tárolt eljárások nagyszerű lehetőséget biztosítanak az SQL-kód beágyazására, amely az SQL-készlet adataihoz közelebb van tárolva. A tárolt eljárások azt is segítik a fejlesztőket, hogy modularize a kódot a felügyelhető egységekbe ágyazva, így könnyebben használhatók a kód újrahasználhatósága. Az egyes tárolt eljárások is elfogadják a paramétereket, hogy azok még rugalmasabbak legyenek.

Az SQL-készlet egyszerűsített és egyszerűsített tárolt eljárás-megvalósítást biztosít. SQL Server képest a legnagyobb különbség az, hogy a tárolt eljárás nincs előre lefordított kód.

Az adatraktárak esetében általánosságban a fordítási idő a nagyméretű adatköteteken való lekérdezések futtatásához képest kicsi. Fontos, hogy a tárolt eljárás kódja megfelelően legyen optimalizálva a nagyméretű lekérdezéseknél.

> [!TIP]
> A cél az óra, perc és másodperc megtakarítása, nem ezredmásodperc. Így hasznos lehet a tárolt eljárások tárolóként való meggondolása az SQL Logic számára.

Amikor az SQL-készlet végrehajtja a tárolt eljárást, az SQL-utasítások elemzése, lefordítása és futási ideje optimalizált. A folyamat során minden utasítás elosztott lekérdezésekre lesz konvertálva. Az adatbázison végrehajtott SQL-kód eltér az elküldött lekérdezéstől.

## <a name="nesting-stored-procedures"></a>Tárolt eljárások beágyazása

Ha a tárolt eljárás más tárolt eljárásokat hív meg, vagy dinamikus SQL-t hajt végre, akkor a belső tárolt eljárás vagy a kód meghívása beágyazottnak mondható.

Az SQL-készlet legfeljebb nyolc beágyazási szintet támogat. Ezzel szemben a SQL Server beágyazási szintje 32.

A legfelső szintű tárolt eljárás az 1. szintű beágyazásnak felel meg.

```sql
EXEC prc_nesting
```

Ha a tárolt eljárás egy másik EXEC hívást is végrehajt, a beágyazási szint két értékre nő.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```

Ha a második eljárás elvégez néhány dinamikus SQL-műveletet, a beágyazási szint háromra nő.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Az SQL-készlet jelenleg nem támogatja a [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)-t. Ezért nyomon kell követnie a beágyazási szintet. Nem valószínű, hogy túllépi a nyolc fészekre vonatkozó korlátot. Ha azonban ezt teszi, újra kell dolgoznia a kódját, hogy az illeszkedjen a korláton belüli beágyazási szintekhez.

## <a name="insertexecute"></a>Beszúrás.. VÉGREHAJTÁSA

Az SQL-készlet nem teszi lehetővé, hogy egy tárolt eljárás eredmény-készletét egy INSERT utasítással használja. Van azonban egy alternatív megközelítés, amelyet használhat. Példaként tekintse meg az [ideiglenes táblákról](sql-data-warehouse-tables-temporary.md)szóló cikket.

## <a name="limitations"></a>Korlátozások

Az SQL-készletben nem implementált Transact-SQL tárolt eljárások néhány aspektusa van, amelyek a következők:

* ideiglenes tárolt eljárások
* számozott tárolt eljárások
* kiterjesztett tárolt eljárások
* CLR tárolt eljárásai
* titkosítási beállítás
* replikálási beállítás
* tábla értékű paraméterek
* csak olvasható paraméterek
* alapértelmezett paraméterek
* végrehajtási környezetek
* visszatérési utasítás

## <a name="next-steps"></a>További lépések

További fejlesztési tippek: a [fejlesztés áttekintése](sql-data-warehouse-overview-develop.md).
