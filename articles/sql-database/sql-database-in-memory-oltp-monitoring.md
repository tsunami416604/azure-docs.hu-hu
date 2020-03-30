---
title: XtP memóriabeli tároló monitora
description: Az XTP memórián belüli tárhelyhasználatának, kapacitásának becslése és monitorozása; 41823-as kapacitáshiba megoldása
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: 22ff83b1ccd009624082e45073123a45006df70f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79209441"
---
# <a name="monitor-in-memory-oltp-storage"></a>Memóriaen belüli OLTP-tároló monitora

A [memórián belüli OLTP](sql-database-in-memory.md)használatakor a memóriaoptimalizált táblákban és táblaváltozókban lévő adatok a memórián belüli OLTP tárolóban találhatók. Minden prémium szintű és üzleti legkritikusabb szolgáltatási szint maximális memóriabeli OLTP-tárolómérettel rendelkezik. Lásd: [DTU-alapú erőforráskorlátok – egyetlen adatbázis](sql-database-dtu-resource-limits-single-databases.md), [DTU-alapú erőforráskorlátok – rugalmas készletek,](sql-database-dtu-resource-limits-elastic-pools.md)[virtuálismagalapú erőforráskorlátok – egyetlen adatbázis](sql-database-vcore-resource-limits-single-databases.md) és [virtuálismag-alapú erőforráskorlátok – rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md).

Ha ezt a korlátot túllépte, a beszúrási és frissítési műveletek megkezdhetik a 41823-as hibát az egyes adatbázisok és a 41840-es hiba esetén a rugalmas készletek esetében. Ezen a ponton vagy törölnie kell az adatokat a memória visszaszerzéséhez, vagy frissítenie kell a szolgáltatási szintet vagy az adatbázis számítási méretét.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Annak meghatározása, hogy az adatok elférnek-e a memórián belüli OLTP tárolósapkában

Határozza meg a különböző szolgáltatási szintek tárolási sapkáit. Lásd: [DTU-alapú erőforráskorlátok – egyetlen adatbázis](sql-database-dtu-resource-limits-single-databases.md), [DTU-alapú erőforráskorlátok – rugalmas készletek,](sql-database-dtu-resource-limits-elastic-pools.md)[virtuálismagalapú erőforráskorlátok – egyetlen adatbázis](sql-database-vcore-resource-limits-single-databases.md) és [virtuálismag-alapú erőforráskorlátok – rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md).

A memóriaoptimalizált tábla memóriakövetelményeinek becslése ugyanúgy működik az SQL Server esetében, mint az Azure SQL Database esetében. Szánjon néhány percet az [MSDN-ről](https://msdn.microsoft.com/library/dn282389.aspx)szóló cikk áttekintésre.

A tábla- és táblaváltozósorok, valamint az indexek beleszámítanak a felhasználói adatok maximális méretébe. Ezenkívül az ALTER TABLE-nek elegendő helyszükséges a teljes tábla és indexeinek új verziójának létrehozásához.

## <a name="monitoring-and-alerting"></a>Figyelés és riasztás
Az Azure Portalon a számítási méret tárolási plafonjának százalékában figyelheti a memórián belüli [tárhelyhasználatot:](https://portal.azure.com/) 

1. Az Adatbázis panelen keresse meg az Erőforrás-kihasználtságot mezőben, és kattintson a Szerkesztés gombra.
2. Válassza ki `In-Memory OLTP Storage percentage`a mérőszámot.
3. Riasztás hozzáadásához kattintson az Erőforrás-kihasználtság mezőre a Metrikus panel megnyitásához, majd kattintson a Riasztás hozzáadása gombra.

Vagy használja a következő lekérdezést a memórián belüli tároló kihasználtságának megjelenítéséhez:

```sql
    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats
```

## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>A memórián kívüli OLTP-tárolási helyzetek javítása - 41823 és 41840-es hibák

Az adatbázis ban a memórián belüli OLTP-tárolókorlát elérése esetén az INSERT, UPDATE, ALTER és CREATE műveletek sikertelenek lesznek a 41823-as hibaüzenettel (egyetlen adatbázis esetén) vagy a 41840-es hiba (rugalmas készletek esetén). Mindkét hiba az aktív tranzakció megszakítását okozza.

A 41823-as és 41840-es hibaüzenetek azt jelzik, hogy az adatbázisban vagy készletben lévő memóriaoptimalizált táblák és táblaváltozók elérték a maximális memóriaen belüli OLTP-tárolóméretet.

A hiba elhárításához:

* Adatok törlése a memóriaoptimalizált táblákból, amelyek potenciálisan kiszervezhetik az adatokat a hagyományos, lemezalapú táblákba; Vagy
* Frissítse a szolgáltatási réteget egy olyanra, amely elegendő memórián belüli tárolóval rendelkezik a memóriaoptimalizált táblákban való tároláshoz szükséges adatokhoz.

> [!NOTE] 
> Ritka esetekben a 41823-as és 41840-es hibák átmenetiek lehetnek, ami azt jelenti, hogy elegendő memóriaen belüli OLTP-tároló áll rendelkezésre, és a művelet újbóli megkísérlése sikeres. Ezért azt javasoljuk, hogy mind a teljes rendelkezésre álló memóriaolt OLTP-tároló figyelése, mind a 41823-as vagy 41840-es hiba első találkozásakor. Az újrapróbálkozási logikáról további információt az [Ütközésészlelési és -újrapróbálkozási logika a memórián belüli OLTP protokollal](https://docs.microsoft.com/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic)című témakörben talál.

## <a name="next-steps"></a>További lépések
A figyelési útmutatót az [Azure SQL Database figyelése dinamikus felügyeleti nézetek használatával című témakörben talál.](sql-database-monitoring-with-dmvs.md)
