---
title: Tárolt eljárások használata az Azure SQL Data Warehouse |} A Microsoft Docs
description: Tippek a tárolt eljárások végrehajtása az Azure SQL Data Warehouse-megoldások fejlesztése.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/02/2019
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 8a53a63b7425935e117d7af951717999bc9340b7
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893369"
---
# <a name="using-stored-procedures-in-sql-data-warehouse"></a>Tárolt eljárások az SQL Data Warehouse használata
Tippek a tárolt eljárások végrehajtása az Azure SQL Data Warehouse-megoldások fejlesztése.

## <a name="what-to-expect"></a>Mi várható?

Az SQL Data Warehouse az SQL Server támogatja a T-SQL-funkciók használt számos. Vannak még fontosabb, kibővített szolgáltatások, amelyek segítségével maximalizálhatja a teljesítményt, hogy a megoldás.

Azonban fenntartása a méretezés és teljesítmény az SQL Data Warehouse van olyan is egyes szolgáltatások és funkciók, amelyek a viselkedésbeli különbségeket, és mások által nem támogatott.


## <a name="introducing-stored-procedures"></a>Tárolt eljárások bemutatása
Tárolt eljárások számára az SQL-kódot; beágyazása nagyszerű lehetőséget jelentenek tárolja az adatokat az adattárház közelében. Tárolt eljárások segítségével a fejlesztők megoldásaikat modularize által kezelhető egységekbe; a kód beágyazása kód nagyobb újrahasznosíthatóság megkönnyítése. Minden egyes tárolt eljárás elfogadhatja, így rugalmasabb még paramétereket is.

Az SQL Data Warehouse biztosít egy egyszerűsített és korszerű tárolt eljárás végrehajtására. A legnagyobb különbség az SQL Server képest akkor, hogy a tárolt eljárás nem előre lefordított programkódját. A data warehouse-adattárházak a fordítási idő az képest nagy méretű adatkötetek lekérdezések futtatásához szükséges idő alacsony. Több fontos annak érdekében, hogy a tárolt eljárás kód megfelelően nagy lekérdezések van optimalizálva. A célja, hogy mentse, óra, perc és másodperc, ezredmásodperc nem. Éppen ezért több hasznos úgy tárolt eljárásokat, mint az SQL logikai tárolói.     

Az SQL Data Warehouse a tárolt eljárást végrehajtja, amikor az SQL-utasítások elemezni, a lefordított, és optimalizált futási időben. Ez a folyamat során minden egyes utasítás elosztott lekérdezések lesz konvertálva. Az SQL-kódot az adatokon végrehajtott eltér a lekérdezés elküldve.

## <a name="nesting-stored-procedures"></a>A beágyazási tárolt eljárások
Tárolt eljárások a más tárolt eljárások hívása, vagy a dinamikus SQL-utasítás végrehajtása, majd a a belső tárolt eljárást, vagy kód meghívási van szólt ágyazható be.

SQL Data Warehouse támogat egy legfeljebb nyolc beágyazási szinttel. Ez némileg eltér az SQL Serverhez. A beágyazott szint az SQL Server 32.

1. szintű beágyazása a legfelső szintű tárolt eljáráshívási adatbázisnak felel meg.

```sql
EXEC prc_nesting
```
Ha a tárolt eljárás is más EXEC hívja, a beágyazott szintet növeli a két.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Ha a második eljárás majd végrehajt néhány dinamikus SQL, a beágyazott szintet növeli a három.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Vegye figyelembe, az SQL Data Warehouse jelenleg nem támogatja [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql). Nyomon követheti a beágyazott szintet kell. Valószínű, hogy a nyolc beágyazott szintű korlátot túllépi, de ha így tesz, kell dolgozni a kód megfelelően a beágyazási szinteket az időkorláton belül.

## <a name="insertexecute"></a>INSERT... HAJTSA VÉGRE
Az SQL Data Warehouse felhasználásához az eredményhalmaz egy INSERT utasítás a tárolt eljárás nem teszi lehetővé. Van azonban egy másik módszert is használhat. Egy vonatkozó példáért tekintse meg a cikket a [ideiglenes táblák](sql-data-warehouse-tables-temporary.md). 

## <a name="limitations"></a>Korlátozások
Nincsenek a Transact-SQL tárolt eljárások, amelyek nem az SQL Data Warehouse egyes funkcióit.

Ezek a következők:

* az ideiglenes tárolt eljárások
* a számozott tárolt eljárások
* Bővített tárolt eljárások
* CLR-beli tárolt eljárások
* Titkosítási beállítás
* replikációs beállítás
* tábla értékű paraméterek
* csak olvasási paraméterei
* alapértelmezett paraméterek
* végrehajtási környezet
* utasítás visszaadása

## <a name="next-steps"></a>További lépések
További fejlesztési tippek: [fejlesztői áttekintés](sql-data-warehouse-overview-develop.md).

