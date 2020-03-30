---
title: Tárolt eljárások használata
description: Tippek a tárolt eljárások megvalósításához az Azure SQL Data Warehouse-ban a megoldások fejlesztéséhez.
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
ms.openlocfilehash: 83c3187c580bda33df8780a0e36f0fb9f2a4f484
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351575"
---
# <a name="using-stored-procedures-in-sql-data-warehouse"></a>Tárolt eljárások használata az SQL Data Warehouse-ban
Tippek a tárolt eljárások megvalósításához az Azure SQL Data Warehouse-ban a megoldások fejlesztéséhez.

## <a name="what-to-expect"></a>Mire számíthat

Az SQL Data Warehouse számos, az SQL Server ben használt T-SQL-szolgáltatást támogat. Ennél is fontosabb, hogy vannak horizontális felskálázási speciális funkciók, amelyek segítségével maximalizálhatja a megoldás teljesítményét.

Az SQL Data Warehouse méretezésének és teljesítményének fenntartása érdekében azonban vannak olyan funkciók és funkciók is, amelyek viselkedésbeli különbségekkel rendelkeznek, és mások, amelyek nem támogatottak.


## <a name="introducing-stored-procedures"></a>A tárolt eljárások bevezetése
A tárolt eljárások nagyszerű megoldást jelentenek az SQL-kód beágyazására; az adatok közelében tárolhatja az adattárházban. A tárolt eljárások segítségével a fejlesztők modulárissá teszik megoldásaikat azáltal, hogy a kódot kezelhető egységekbe ágyazják; a kód nagyobb újrafelhasználhatóságának elősegítése. Minden tárolt eljárás is elfogadja paramétereket, hogy azok még rugalmasabb.

Az SQL Data Warehouse egyszerűsített és egyszerűsített tárolt eljárásmegvalósítást biztosít. Az SQL Server hez képest a legnagyobb különbség az, hogy a tárolt eljárás nem előre lefordított kód. Az adatraktárakban a fordítási idő kicsi a nagy adatkötetek lekérdezéseinek futtatásához szükséges időhöz képest. Sokkal fontosabb, hogy a tárolt eljáráskód megfelelően legyen optimalizálva a nagy lekérdezésekhez. A cél az, hogy mentse óra, perc és másodperc, nem ezredmásodperc. Ezért hasznosabb a tárolt eljárásokat az SQL-logika tárolóiként gondolni.     

Amikor az SQL Data Warehouse végrehajtja a tárolt eljárást, az SQL-utasítások elemzésre kerülnek, lefordítva és optimalizálva lesznek futásidőben. A folyamat során minden utasítás elosztott lekérdezésekké alakul át. Az adatokkal végrehajtott SQL-kód eltér a beküldött lekérdezéstől.

## <a name="nesting-stored-procedures"></a>Tárolt eljárások beágyazása
Ha a tárolt eljárások más tárolt eljárásokat hívnak meg, vagy dinamikus SQL-t hajtanak végre, akkor a belső tárolt eljárás vagy kódmeghívás a beágyazottnak lesz kitéve.

Az SQL Data Warehouse legfeljebb nyolc beágyazási szintet támogat. Ez némileg eltér az SQL Server kiszolgálótól. Az SQL Server fészekszintje 32.

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

Megjegyzés: az SQL Data Warehouse jelenleg nem támogatja [a @@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql). Le kell nyomoznia a fészek szintjét. Nem valószínű, hogy túllépi a nyolc fészekszint korlátját, de ha igen, át kell dolgoznia a kódot, hogy elférjen a beágyazási szintek ezen a korláton belül.

## <a name="insertexecute"></a>Beszúrása.. Végre
Az SQL Data Warehouse nem teszi lehetővé az INSERT utasítással tárolt eljárás eredménykészletének felhasználását. Van azonban egy alternatív megközelítés, amelyet használhat. Például az [ideiglenes táblákról](sql-data-warehouse-tables-temporary.md)szóló cikket. 

## <a name="limitations"></a>Korlátozások
Vannak olyan szempontok a Transact-SQL tárolt eljárások, amelyek nem valósíthatók meg az SQL Data Warehouse.

Ezek a következők:

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

