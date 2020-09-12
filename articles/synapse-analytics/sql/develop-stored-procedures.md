---
title: Tárolt eljárások használata
description: Tippek a tárolt eljárások megvalósításához a szinapszis SQL-készletben (adattárházban) a megoldások fejlesztéséhez.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 294652a42d3b6a2468f024ce7ebdbdfc3615f9e1
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89647870"
---
# <a name="use-stored-procedures-in-sql-pool"></a>Tárolt eljárások használata az SQL-készletben

Tippek a tárolt eljárások megvalósításához a szinapszis SQL-készletben (adattárházban) a megoldások fejlesztéséhez.

## <a name="what-to-expect"></a>Amire számíthat

Az SQL-készlet számos olyan T-SQL-funkciót támogat, amelyek a SQL Serverben használatosak. Még ennél is fontosabb, hogy a megoldás teljesítményének maximalizálása érdekében Felskálázási funkciók érhetők el.

Az SQL-készlet méretezésének és teljesítményének fenntartása érdekében azonban vannak olyan funkciók és funkciók is, amelyek viselkedési különbségekkel és másokkal nem támogatottak.

## <a name="stored-procedures-in-sql-pool"></a>Tárolt eljárások az SQL-készletben

A tárolt eljárások nagyszerű lehetőséget biztosítanak az SQL-kód beágyazására; az adatraktárban tárolt adataihoz való közelsége. A tárolt eljárások segítségével a fejlesztők felügyelhető egységekre ágyazva modularize a megoldásaikat. a kód nagyobb újrahasználhatóságának elősegítése. Az egyes tárolt eljárások is elfogadják a paramétereket, hogy azok még rugalmasabbak legyenek.

Az SQL-készlet egyszerűsített és egyszerűsített tárolt eljárás-megvalósítást biztosít. SQL Server képest a legnagyobb különbség az, hogy a tárolt eljárás nem előre lefordított kód. Az adattárházak esetében a fordítási idő kisebb, mint a nagyméretű adatköteteken végzett lekérdezések futtatásához szükséges idő. Fontos, hogy a tárolt eljárás kódja megfelelően legyen optimalizálva a nagyméretű lekérdezésekhez. A cél az óra, perc és másodperc megtakarítása, nem ezredmásodperc. Ezért hasznos, ha a tárolt eljárásokat SQL Logic tárolóként szeretné meggondolni.

Amikor az SQL-készlet végrehajtja a tárolt eljárást, az SQL-utasítások elemzése, lefordítása és futási ideje optimalizált. A folyamat során minden utasítás elosztott lekérdezésekre lesz konvertálva. Az adatbázison végrehajtott SQL-kód eltér az elküldött lekérdezéstől.

## <a name="nesting-stored-procedures"></a>Tárolt eljárások beágyazása

Ha a tárolt eljárás más tárolt eljárásokat hív meg, vagy dinamikus SQL-t hajt végre, akkor a belső tárolt eljárás vagy a kód meghívása beágyazottnak mondható.

Az SQL-készlet legfeljebb nyolc beágyazási szintet támogat. Ez némileg eltér a SQL Server. A SQL Server beágyazási szintje 32.

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
EXEC sp_executesql N'SELECT ''another nest level'''  -- This call is nest level 2
GO
EXEC prc_nesting
```

> [!NOTE]
> Az SQL-készlet jelenleg nem támogatja a [@ @NESTLEVEL ](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)-t. Követnie kell a beágyazási szintet. Nem valószínű, hogy túllépi a nyolcnál nagyobb korlátot, de ha igen, újra kell dolgoznia a kódot, hogy az illeszkedjen a korláton belüli beágyazási szintre.

## <a name="insertexecute"></a>INSERT..EXEARANYOS

Az SQL-készlet nem teszi lehetővé egy tárolt eljárás eredmény-készletének felhasználását INSERT utasítással. Van azonban egy alternatív megközelítés, amelyet használhat. Példaként tekintse meg az [ideiglenes táblákról](develop-tables-temporary.md)szóló cikket.

## <a name="limitations"></a>Korlátozások

Az SQL-készletben nem implementált Transact-SQL tárolt eljárások néhány aspektusa van.

Ezek a következők:

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

## <a name="next-steps"></a>Következő lépések

További fejlesztési tippek: a [fejlesztés áttekintése](develop-overview.md).
