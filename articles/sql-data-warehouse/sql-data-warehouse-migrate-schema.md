---
title: Séma áttelepítése az SQL Data Warehouse |} A Microsoft Docs
description: A séma migrálása az Azure SQL Data Warehouse-megoldások fejlesztése a tippek.
services: sql-data-warehouse
author: jrowlandjones
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: 51ad7eed0bf37194b1e5ff2c605b39246e9a1191
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301182"
---
# <a name="migrate-your-schemas-to-sql-data-warehouse"></a>A sémák áttelepítése az SQL Data Warehouse
Útmutató az SQL Data Warehouse-ba való migrálás az SQL-sémák. 

## <a name="plan-your-schema-migration"></a>A séma migrálási terv

Szeretne áttelepítést végezni, amikor a [táblák áttekintésével] [ table overview] megismerkedhet a táblázat kialakítási szempontok például statisztika, terjesztési, particionálás, és az indexelés.  Azt is megjeleníti, néhány [nem támogatott funkciók tábla] [ unsupported table features] és azok megoldását ismerteti.

## <a name="use-user-defined-schemas-to-consolidate-databases"></a>Felhasználó által definiált sémák használatával egyesítheti az adatbázisok

A meglévő számítási feladatok valószínűleg több adatbázissal rendelkezik. Például egy SQL Server data warehouse tartalmazhat egy átmeneti adatbázis, adatraktár-adatbázis és néhány adat adatközpont-adatbázis. Ebben a topológiában minden egyes adatbázis fut egy külön számítási feladat külön biztonsági házirendeknek.

Ezzel szemben az SQL Data Warehouse fut, a teljes adatraktározás számítási feladatáról egy adatbázison belül. Adatbázis közötti illesztések nem engedélyezettek. Ezért az SQL Data Warehouse az egyetlen adatbázis tárolja az adatraktár által használt összes táblának vár.

Azt javasoljuk, hogy felhasználó által definiált sémák használatával egyesíthetők a meglévő számítási feladat egy adatbázisba. Példák: [felhasználó által definiált sémák](sql-data-warehouse-develop-user-defined-schemas.md)

## <a name="use-compatible-data-types"></a>Kompatibilis adattípusok használata
Módosítsa az adattípusok kompatibilisek az SQL Data warehouse-bA. Támogatott és nem támogatott adattípusok listáját lásd: [adattípusok][data types]. Szakasza biztosító megoldások a nem támogatott típusokat. Azonosítsa a meglévő típus nem támogatott az SQL Data Warehouse egy lekérdezést is tartalmazza.

## <a name="minimize-row-size"></a>Minimalizálja a sor mérete
A legjobb teljesítmény érdekében minimalizálja a táblák sor hossza. Mivel sor rövidebb hosszúságú vezet jobb teljesítményt, használja a legkisebb adattípusok, amelyek az adatok. 

A tábla sor szélesség a PolyBase esetében 1 MB.  Adatok betöltése az SQL Data Warehouse a polybase-zel tervezi, ha frissíteni szeretné, hogy kevesebb mint 1 MB maximális szélessége a táblák. 

<!--
- For example, this table uses variable length data but the largest possible size of the row is still less than 1 MB. PolyBase will load data into this table.

- This table uses variable length data and the defined row width is less than one MB. When loading rows, PolyBase allocates the full length of the variable-length data. The full length of this row is greater than one MB.  PolyBase will not load data into this table.  

-->

## <a name="specify-the-distribution-option"></a>A telepítési lehetőség kiválasztása
Az SQL Data Warehouse egy elosztott adatbázis rendszer. Minden tábla elosztott vagy replikált számítási csomópontjai között. Nincs a table kapcsoló, amely lehetővé teszi a adja meg, hogyan terjesztheti az adatait. A választási lehetőségek: Ciklikus időszeleteléses replikálja, vagy kivonatoló elosztott. Minden egyes vannak előnyei és hátrányai. Ha nem adja meg a-terjesztési beállítást, az SQL Data Warehouse alapértelmezett használjon ciklikus időszeletelést.

- Ciklikus időszeleteléses az alapértelmezett érték. A legegyszerűbben használható, és betölti az adatokat, de illesztések gyorsabban adatáthelyezés, ami lelassítja a lekérdezési teljesítmény lesz szükség.
- A replikált tároló egy-egy példányát a tábla minden számítási csomóponton. Replikált táblák a nagy teljesítményű, mivel nincs szükségük az adatmozgatás összekapcsolásokhoz és az összesítések. Ezek extra tárterület szükséges, és emiatt leginkább kisebb méretű táblák esetében.
- Kivonatterjesztés a kivonatolási függvény használatával az összes csomópont között osztja el a sorokat. Mivel a célja, hogy a lekérdezési teljesítményt nyújtanak nagy táblák elosztott kivonattáblák, nem az SQL Data Warehouse legfontosabb céljait segítik. Ez a beállítás szükséges néhány tervezi, hogy válassza ki a legjobb oszlopot, amelyen az adatok elosztására. Azonban ha nem a legjobb oszlop először, könnyen újra juttathatja el az adatokat egy másik oszlop alapján. 

Válassza ki a legjobb-terjesztési beállítást minden egyes, lásd: [elosztott táblákról](sql-data-warehouse-tables-distribute.md).


## <a name="next-steps"></a>További lépések
Miután sikeresen migrálva az adatbázissémát az SQL Data Warehouse, folytassa a következő cikkekben:

* [Az adatok áttelepítése][Migrate your data]
* [Kódok migrálása][Migrate your code]

Az SQL Data Warehouse – gyakorlati tanácsok kapcsolatos további információkért tekintse meg a [ajánlott eljárások] [ best practices] cikk.

<!--Image references-->

<!--Article references-->
[Migrate your code]: ./sql-data-warehouse-migrate-code.md
[Migrate your data]: ./sql-data-warehouse-migrate-data.md
[best practices]: ./sql-data-warehouse-best-practices.md
[table overview]: ./sql-data-warehouse-tables-overview.md
[unsupported table features]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[data types]: ./sql-data-warehouse-tables-data-types.md
[unsupported data types]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types

<!--MSDN references-->


<!--Other Web references-->
