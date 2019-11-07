---
title: Tárolt eljárások használata
description: Tippek a Azure SQL Data Warehouse tárolt eljárások megvalósításához a megoldások fejlesztéséhez.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/02/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e6e1144043cbbbc8124785351e1e56a776b84527
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692814"
---
# <a name="using-stored-procedures-in-sql-data-warehouse"></a>Tárolt eljárások használata a SQL Data Warehouseban
Tippek a Azure SQL Data Warehouse tárolt eljárások megvalósításához a megoldások fejlesztéséhez.

## <a name="what-to-expect"></a>Mi várható?

A SQL Data Warehouse számos olyan T-SQL-funkciót támogat, amelyek a SQL Serverban használatosak. Még ennél is fontosabb, hogy a megoldás teljesítményének maximalizálása érdekében Felskálázási funkciók érhetők el.

A SQL Data Warehouse méretezésének és teljesítményének fenntartása érdekében azonban vannak olyan funkciók és funkciók is, amelyek viselkedési különbségeket és másokat nem támogatnak.


## <a name="introducing-stored-procedures"></a>Tárolt eljárások bevezetése
A tárolt eljárások nagyszerű lehetőséget biztosítanak az SQL-kód beágyazására; az adatraktárban tárolt adataihoz való közelsége. A tárolt eljárások segítségével a fejlesztők felügyelhető egységekre ágyazva modularize a megoldásaikat. a kód nagyobb újrahasználhatóságának elősegítése. Az egyes tárolt eljárások is elfogadják a paramétereket, hogy azok még rugalmasabbak legyenek.

A SQL Data Warehouse egyszerűsített és egyszerűsített tárolt eljárás-megvalósítást biztosít. SQL Server képest a legnagyobb különbség az, hogy a tárolt eljárás nem előre lefordított kód. Az adattárházak esetében a fordítási idő kisebb, mint a nagyméretű adatköteteken végzett lekérdezések futtatásához szükséges idő. Fontos, hogy a tárolt eljárás kódja megfelelően legyen optimalizálva a nagyméretű lekérdezésekhez. A cél az óra, perc és másodperc megtakarítása, nem ezredmásodperc. Ezért hasznos, ha a tárolt eljárásokat SQL Logic tárolóként szeretné meggondolni.     

Amikor SQL Data Warehouse végrehajtja a tárolt eljárást, az SQL-utasítások elemzése, lefordítása és futási ideje optimalizált. A folyamat során minden utasítás elosztott lekérdezésekre lesz konvertálva. Az adatbázison végrehajtott SQL-kód eltér az elküldött lekérdezéstől.

## <a name="nesting-stored-procedures"></a>Tárolt eljárások beágyazása
Ha a tárolt eljárás más tárolt eljárásokat hív meg, vagy dinamikus SQL-t hajt végre, akkor a belső tárolt eljárás vagy a kód meghívása beágyazottnak mondható.

A SQL Data Warehouse legfeljebb nyolc beágyazási szintet támogat. Ez némileg eltér a SQL Server. A SQL Server beágyazási szintje 32.

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

Vegye figyelembe, hogy SQL Data Warehouse jelenleg nem támogatja a [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql). Követnie kell a beágyazási szintet. Nem valószínű, hogy túllépi a nyolcnál nagyobb korlátot, de ha igen, újra kell dolgoznia a kódot, hogy az illeszkedjen a korláton belüli beágyazási szintre.

## <a name="insertexecute"></a>Beszúrás.. VÉGREHAJTÁSA
A SQL Data Warehouse nem teszi lehetővé, hogy egy tárolt eljárás eredmény-készletét egy INSERT utasítással használja. Van azonban egy alternatív megközelítés, amelyet használhat. Példaként tekintse meg az [ideiglenes táblákról](sql-data-warehouse-tables-temporary.md)szóló cikket. 

## <a name="limitations"></a>Korlátozások
A Transact-SQL tárolt eljárásainak néhány aspektusa nem SQL Data Warehouseban valósul meg.

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

## <a name="next-steps"></a>További lépések
További fejlesztési tippek: a [fejlesztés áttekintése](sql-data-warehouse-overview-develop.md).

