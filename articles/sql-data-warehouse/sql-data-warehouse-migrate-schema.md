---
title: A séma áttelepítése az SQL Data Warehouse |} Microsoft Docs
description: Tippek a séma áttelepítése az Azure SQL Data Warehouse adattárházzal történő, megoldások.
services: sql-data-warehouse
author: jrowlandjones
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: fb1085450a16acb0f9a06a9dea9d91fc5ca23363
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="migrate-your-schemas-to-sql-data-warehouse"></a>Az SQL Data Warehouse a sémák áttelepítése
Az SQL-sémák áttelepítéséhez az SQL Data Warehouse-útmutatót. 

## <a name="plan-your-schema-migration"></a>A séma áttelepítés tervezése

Áttelepítés tervezése közben, lásd: a [tábla áttekintése] [ table overview] megismerkedhet a táblázat kialakítási szempontok például statisztikáiról, terjesztési, particionálás, és az indexelés.  Azt is soroljuk [táblában funkciók nem támogatott] [ unsupported table features] és azok megoldását ismerteti.

## <a name="use-user-defined-schemas-to-consolidate-databases"></a>Felhasználói sémákkal segítségével adatbázisok összesítése

A meglévő alkalmazások és szolgáltatások valószínűleg tartalmazza az egynél több adatbázisból. Egy SQL Server data warehouse tartalmazhat például egy átmeneti adatbázis, adatraktár-adatbázis és néhány adat adatközpont-adatbázis. Ebben a topológiában minden adatbázisnak külön biztonsági házirendek külön feladatként fut.

Az SQL Data Warehouse ellentétben a teljes adatraktározás számítási feladatáról egy adatbázison belül futtatja. Adatbázis közötti illesztések nem engedélyezettek. Ezért az SQL Data Warehouse egy adatbázis tárolja az adatraktár által használt összes táblának vár.

Azt javasoljuk, felhasználó által definiált sémák vonják össze a meglévő alkalmazások és szolgáltatások egy adatbázisba. Tekintse meg a [felhasználói sémák](sql-data-warehouse-develop-user-defined-schemas.md)

## <a name="use-compatible-data-types"></a>Kompatibilis adattípusok használata
Ahhoz, hogy az SQL Data Warehouse szolgáltatással kompatibilis az adattípusok módosításával. Támogatott és nem támogatott adattípusú listájáért lásd: [adattípusok][data types]. Témakör lehetséges megoldások ad a nem támogatott típusú. A lekérdezés nem támogatott az SQL Data Warehouse típusú létező azonosítására is tartalmazza.

## <a name="minimize-row-size"></a>Sor mérete minimalizálása érdekében
A legjobb teljesítmény érdekében minimalizálása érdekében a táblák sor hossza. Mivel rövidebb sor hosszának előfordulhat, hogy jobb teljesítményt, használja a legkisebb adatok, amelyek működnek az adatok. 

A táblázat sor szélessége a PolyBase van a 1 Megabájtos korlátot.  Adatok betöltése az SQL Data Warehouse PolyBase tervez, ha rendelkezik a legnagyobb sor vonalainak 1 MB-nál kevesebb a táblák frissítésével. 

<!--
- For example, this table uses variable length data but the largest possible size of the row is still less than 1 MB. PolyBase will load data into this table.

- This table uses variable length data and the defined row width is less than one MB. When loading rows, PolyBase allocates the full length of the variable-length data. The full length of this row is greater than one MB.  PolyBase will not load data into this table.  

-->

## <a name="specify-the-distribution-option"></a>Adja meg a terjesztési beállítást
Az SQL Data Warehouse egy olyan elosztott adatbázis rendszer. Minden tábla elosztott vagy a számítási csomópontok replikálódnak. Nincs olyan tábla beállítás, amely lehetővé teszi, hogy miként ossza el az adatokat adja meg. Az alábbiak közül választhat ciklikus multiplexelés replikálva, vagy a kivonatoló elosztott. Minden egyes vannak előnyei és hátrányai. Ha nem adja meg a terjesztő beállítást, az SQL Data Warehouse ciklikus multiplexelés fogja használni az alapértelmezett.

- Ciklikus multiplexelés az alapértelmezett beállítás. A legegyszerűbb használja, és a terhelések lassabbak, de illesztések adatok adatmozgás, ami megnöveli a lekérdezési teljesítmény szükséges.
- A replikált tároló egy-egy példányát a tábla minden számítási csomóponton. Replikált táblák nincsenek performant, mert az összekapcsolásokhoz és az összesítések nem igényelnek adatátvitelt jelölik. Ezek – megnövelt tárhely szükséges, és ezért működhet a legjobban a kisebb táblákat.
- Elosztott kivonat kivonatoló függvényt keresztül a csomópontok a sorok elosztása. A kivonattáblákkal elosztott SQL Data Warehouse lelke, mivel úgy vannak kialakítva, hogy a lekérdezési teljesítmény biztosítható a nagy táblák. Ez a beállítás megköveteli néhány jelölje ki a legjobb oszlopot, amelyen az adatok terjeszteni tervezi. Azonban ha nem a legjobb oszlop először, könnyen újra terjesztheti az adatokat egy másik oszlop alapján. 

A legjobb telepítési lehetőség minden táblához, lásd: [táblák elosztott](sql-data-warehouse-tables-distribute.md).


## <a name="next-steps"></a>További lépések
Miután sikeresen áttelepítette az adatbázissémát az SQL Data Warehouse, folytassa a következő cikkekben:

* [Adatok áttelepítése][Migrate your data]
* [Telepítse át a kódot][Migrate your code]

Az SQL Data Warehouse gyakorlati tanácsokat kapcsolatban bővebben lásd: a [ajánlott eljárások] [ best practices] cikk.

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
