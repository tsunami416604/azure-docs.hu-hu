---
title: Az Azure SQL Data Warehouse tárolt eljárások használatával |} Microsoft Docs
description: Tippek a tárolt eljárások végrehajtása az Azure SQL Data Warehouse adattárházzal történő, megoldások.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/12/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 74e943548ff97a04231e2affb645daa1e4b826a2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="using-stored-procedures-in-sql-data-warehouse"></a>Az SQL Data Warehouse tárolt eljárások használatával
Tippek a tárolt eljárások végrehajtása az Azure SQL Data Warehouse adattárházzal történő, megoldások.

## <a name="what-to-expect"></a>Mi várható?

Az SQL Data Warehouse támogatja a T-SQL funkciókat használt számos SQL Server. Nincsenek ennél is fontosabb, kibővített szolgáltatások, amelyek segítségével maximalizálhatja a megoldás teljesítményére.

Azonban fenntartásához a méretezés és teljesítmény az SQL Data Warehouse hiba történik is egyes szolgáltatások és funkciók, amelyek viselkedési különbségek és mások által nem támogatott.


## <a name="introducing-stored-procedures"></a>Tárolt eljárások bemutatása
Tárolt eljárások nagyszerű módját a és az SQL-kódot; tárolja az adatokat az adatraktárban közelében. Tárolt eljárások segítségével a fejlesztők által kezelhető egységekbe; a kódot és megoldásuk modularize a kód nagyobb újrahasznosításának megkönnyítése. Minden tárolt eljárás is fogadhat rugalmasabb még így ezek a paraméterek.

Az SQL Data Warehouse egy egyszerűsített és zökkenőmentes tárolt eljárás végrehajtása biztosítja. A legfontosabb különbség az SQL Server képest, győződjön meg arról, hogy a tárolt eljárás nem előre lefordított kódot. Az adatraktárban a fordítási idő az képest nagy adatkötetek lekérdezéseinek futtatásához szükséges idő alacsony. Több fontos, hogy a tárolt eljárás kódot megfelelően nagy lekérdezések van optimalizálva. A cél, óra, perc, és a másodperc, ezredmásodperc nem menti. Éppen ezért jobban használható SQL logika tárolójaként tárolt eljárások gondol.     

A tárolt eljárás végrehajtásakor a SQL Data Warehouse az SQL-utasítások elemezni, lefordítva, és optimalizált futási időben. A folyamat során minden utasításhoz konvertálni az elosztott lekérdezések. Az SQL-kódot, amely az adatok alapján végrehajtott y nem egyezik az elküldött lekérdezés.

## <a name="nesting-stored-procedures"></a>A beágyazási tárolt eljárások
Tárolt eljárások hívás az más tárolt eljárások, vagy dinamikus SQL-utasítás végrehajtása, majd a belső tárolt eljárást, vagy a kód hívása, különállónak lehet egymásba ágyazni.

Az SQL Data Warehouse legfeljebb nyolc beágyazási szinttel. Ez kis mértékben eltér az SQL-kiszolgálón. SQL Server szintje kivételblokkokra 32.

A legfelső szintű tárolt eljárás hívása megfelel az 1. szintű beágyazásához.

```sql
EXEC prc_nesting
```
Ha a tárolt eljárás is küld egy másik EXEC hívja, a nest szint két növeli.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Ha a második eljárás majd néhány dinamikus SQL végrehajtása során, a nest szint háromra növeli.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Vegye figyelembe, az SQL Data Warehouse jelenleg nem támogatja a [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql). Kell nyomon követnie a nest szintjét. Nem valószínű, hogy a nyolc nest szintű korláton, de ha így tesz, szeretné-e a kód belül ezt a határt beágyazási szinttel megfelelően átdolgozási.

## <a name="insertexecute"></a>INSERT... VÉGREHAJTÁS
Az SQL Data Warehouse nem teszi lehetővé az eredményhalmaz egy INSERT utasítás a tárolt eljárás felhasználását. Van azonban egy másik módszert is használhat. Egy vonatkozó példáért lásd: a cikk a [az ideiglenes táblák](sql-data-warehouse-tables-temporary.md). 

## <a name="limitations"></a>Korlátozások
Nincsenek a Transact-SQL tárolt eljárások, amelyeket a rendszer nem az SQL Data Warehouse egyes funkcióit.

Ezek a következők:

* az ideiglenes tárolt eljárások
* a számozott tárolt eljárások
* Bővített tárolt eljárások
* A közös nyelvi futtató környezet tárolt eljárások
* 
* a titkosítási beállítás
* replikációs beállítás
* tábla értékű paraméter
* csak olvasható paraméterek
* alapértelmezett paraméterek
* végrehajtási környezet
* térjen vissza a utasítás

## <a name="next-steps"></a>További lépések
További fejlesztési tippek, lásd: [fejlesztői áttekintés](sql-data-warehouse-overview-develop.md).

