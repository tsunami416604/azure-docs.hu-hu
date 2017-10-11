---
title: "Az SQL Data Warehouse tárolt eljárások |} Microsoft Docs"
description: "Tippek a tárolt eljárások végrehajtása az Azure SQL Data Warehouse adattárházzal történő, megoldások."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 9b238789-6efe-4820-bf77-5a5da2afa0e8
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: e42d80f0ca35f3fbb67389c66d072bc40d8a8d2c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="stored-procedures-in-sql-data-warehouse"></a>Az SQL Data Warehouse tárolt eljárások
Az SQL Data Warehouse számos található az SQL Server Transact-SQL funkció támogatja. Nincsenek ennél is fontosabb, hogy a megoldás bővítette ki lesz szeretnénk funkciók kibővítési.

Azonban fenntartásához a méretezés és teljesítmény az SQL Data Warehouse hiba történik is egyes szolgáltatások és funkciók, amelyek viselkedési különbségek és mások által nem támogatott.

Ez a cikk azt ismerteti, hogyan SQL Data warehouse tárolt eljárások végrehajtásához.

## <a name="introducing-stored-procedures"></a>Tárolt eljárások bemutatása
Tárolt eljárások nagyszerű módját a és az SQL-kódot; tárolja az adatokat az adatraktárban közelében. A kód és kezelhető egységekbe által a tárolt eljárások segítségével a fejlesztők modularize megoldásuk; a kód nagyobb re-usability megkönnyítése. Minden tárolt eljárás is fogadhat rugalmasabb még így ezek a paraméterek.

Az SQL Data Warehouse egy egyszerűsített és zökkenőmentes tárolt eljárás végrehajtása biztosítja. A legfontosabb különbség az SQL Server képest, győződjön meg arról, hogy a tárolt eljárás nem előre lefordított kódot. Az adatraktárak dolgozunk általában kevésbé fontos szempont a fordítási idő. Több fontos, hogy a tárolt eljárás kódot megfelelően optimalizálni, ha nagy adatmennyiség elleni működik. A cél, óra, perc és másodperc, ezredmásodperc nem menti. Éppen ezért jobban használható SQL logika tárolójaként tárolt eljárások gondol.     

A tárolt eljárás végrehajtásakor a SQL Data Warehouse az SQL-utasítások elemzésének, lefordított és optimalizált futási időben. A folyamat során minden utasításhoz konvertálni az elosztott lekérdezések. Az SQL-kódot, amely az adatok alapján végrehajtása nem egyezik az elküldött lekérdezéséhez.

## <a name="nesting-stored-procedures"></a>A beágyazási tárolt eljárások
Ha a tárolt eljárások hívás az más tárolt eljárások, vagy dinamikus SQL-utasítás végrehajtása, majd a belső tárolt eljárás vagy a kód hívása, különállónak lehet egymásba ágyazni.

Az SQL Data Warehouse 8 beágyazási szinttel legfeljebb támogat. Ez kis mértékben eltér az SQL-kiszolgálón. SQL Server szintje kivételblokkokra 32.

1. szintű beágyazásához megfelel a legfelső szintű tárolt eljárás hívása

```sql
EXEC prc_nesting
```
Ha a tárolt eljárás is küld egy másik hívás EXEC majd ez megnöveli a nest szint 2

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Ha a második eljárás végrehajtása során majd néhány dinamikus sql majd ez megnöveli a nest szint 3

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Megjegyzés: az SQL Data Warehouse jelenleg nem támogatja a@NESTLEVEL. Nyomon egy a nest szint saját magának kell. Nem valószínű, a 8 nest szintű korlátot elért, de ellenkező esetben szüksége lesz a kódot újra működik, és "egybesimítására" azt, hogy elférjen ezt a határt.

## <a name="insertexecute"></a>INSERT... VÉGREHAJTÁS
Az SQL Data Warehouse nem teszi lehetővé az eredményhalmaz egy INSERT utasítás a tárolt eljárás felhasználását. Van azonban egy másik módszert is használhat.

Tekintse meg az alábbi cikket a [az ideiglenes táblák] ennek példát.

## <a name="limitations"></a>Korlátozások
Nincsenek a Transact-SQL tárolt eljárások, amelyeket a rendszer nem az SQL Data Warehouse egyes funkcióit.

Ezek a következők:

* az ideiglenes tárolt eljárások
* a számozott tárolt eljárások
* Bővített tárolt eljárások
* A közös nyelvi futtató környezet tárolt eljárások
* a titkosítási beállítás
* replikációs beállítás
* tábla értékű paraméter
* csak olvasható paraméterek
* alapértelmezett paraméterek
* végrehajtási környezet
* térjen vissza a utasítás

## <a name="next-steps"></a>Következő lépések
További fejlesztési tippek, lásd: [fejlesztői áttekintés][development overview].

<!--Image references-->

<!--Article references-->
[az ideiglenes táblák]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[development overview]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[nest level]: https://msdn.microsoft.com/library/ms187371.aspx

<!--Other Web references-->
