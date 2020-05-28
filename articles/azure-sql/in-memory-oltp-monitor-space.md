---
title: Memóriában tárolt XTP figyelése
description: Megbecsülheti és figyelheti a XTP memóriabeli tárolási használatát, a kapacitást; kapacitás feloldása 41823
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: 6ab303c06f1ca6c7ab6a7a192532b79505676811
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84046879"
---
# <a name="monitor-in-memory-oltp-storage-in-azure-sql-database-and-azure-sql-managed-instance"></a>Memóriában tárolt OLTP-tárolók figyelése Azure SQL Database és az Azure SQL felügyelt példányain
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Memóriában tárolt [OLTP](in-memory-oltp-overview.md)használata esetén a memóriában optimalizált táblák és a táblázat változói a memóriában tárolt OLTP-tárolóban találhatók.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Annak megállapítása, hogy a memóriában lévő OLTP-tárolóban található adatmennyiség megfelel-e

Határozza meg a különböző szolgáltatási rétegek tárolási sapkáit. Az egyes prémium és üzletileg kritikus szolgáltatási szinteken a memóriában tárolt OLTP maximális mérete található.

- [DTU-alapú erőforrás-korlátok – önálló adatbázis](database/resource-limits-dtu-single-databases.md)
- [DTU-alapú erőforrás-korlátok – rugalmas készletek](database/resource-limits-dtu-elastic-pools.md)
- [Virtuális mag-alapú erőforrás-korlátok – önálló adatbázisok](database/resource-limits-vcore-single-databases.md)
- [Virtuális mag-alapú erőforrás-korlátok – rugalmas készletek](database/resource-limits-vcore-elastic-pools.md)
- [Virtuális mag-alapú erőforrás-korlátok – felügyelt példány](managed-instance/resource-limits.md)

A memóriára optimalizált tábla memóriára vonatkozó követelményeinek becslése ugyanúgy működik, mint a Azure SQL Database és az Azure SQL felügyelt példányának SQL Server. Szánjon néhány percet a [becsült memória-követelmények](/sql/relational-databases/in-memory-oltp/estimate-memory-requirements-for-memory-optimized-tables)áttekintésére.

A tábla és a tábla változó sorai, valamint az indexek a maximális felhasználói adatméret irányába mutatnak. Emellett az ALTER TABLE elég helyet igényel a teljes tábla és az indexek új verziójának létrehozásához.

Ha túllépte a korlátot, az INSERT és a Update művelet az Azure SQL felügyelt példányában lévő Azure SQL Database-és adatbázis-adatbázisokban a 41823-as hiba miatt meghiúsul, és a 41840-es hiba a rugalmas készletek esetében Azure SQL Database. Ezen a ponton törölnie kell az adatok mennyiségét a memória visszaigényléséhez, vagy frissítenie kell az adatbázis szolgáltatási szintjét vagy számítási méretét.

## <a name="monitoring-and-alerting"></a>Figyelés és riasztás

A memóriában tárolt tárolók használatát a [Azure Portal](https://portal.azure.com/)számítási méretének százalékos értékeként is megfigyelheti:

1. Az adatbázis panelen keresse meg az Erőforrás kihasználtsága mezőt, és kattintson a Szerkesztés gombra.
2. Válassza ki a metrikát `In-Memory OLTP Storage percentage` .
3. Riasztás hozzáadásához kattintson az Erőforrás kihasználtsága mezőre a metrika panel megnyitásához, majd kattintson a riasztás hozzáadása lehetőségre.

Vagy használja a következő lekérdezést a memóriában tárolt tárterület használatának megjelenítéséhez:

```sql
    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats
```

## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>A memóriában tárolt OLTP-tárolási helyzetek javítása – 41823-es és 41840-es hibák

Az adatbázis memóriában lévő OLTP-tárolási korlátjának beírásával a rendszer beilleszti, FRISSÍTI, módosítja és létrehozza a műveleteket, a 41823-as hibaüzenettel (az önálló adatbázisok esetében) vagy a 41840-es hibával (rugalmas készletek esetén). Mindkét hiba miatt az aktív tranzakció megszakad.

A 41823-es és a 41840-es hibaüzenetek azt jelzik, hogy az adatbázisban vagy a készletben lévő memória-optimalizált táblák és Table változók elérte a maximális memóriabeli OLTP-tárolási méretet.

A hiba elhárításához a következők egyikét kell tennie:

- Adatok törlése a memóriára optimalizált táblákból, potenciálisan az adatok kiszervezése a hagyományos, lemezes táblákba; vagy
- Frissítse a szolgáltatási szintet úgy, hogy az a memóriára optimalizált táblákban tárolt adatokhoz elég memóriabeli tárterületet biztosítson.

> [!NOTE]
> Ritka esetekben a 41823-es és a 41840-es hibák átmenetiek lehetnek, ami azt jelenti, hogy elegendő memóriabeli OLTP-tárterület áll rendelkezésre, és a művelet sikeres újrapróbálkozik. Ezért javasoljuk, hogy figyelje a memóriában elérhető teljes OLTP-tárolót, és próbálkozzon újra a 41823-es vagy 41840-os hiba esetén. Az újrapróbálkozási logikával kapcsolatos további információkért lásd: [ütközések észlelése és újrapróbálkozási logika a memóriában tárolt OLTP](https://docs.microsoft.com/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>További lépések

A figyelési útmutató: a [dinamikus felügyeleti nézetek használatával történő figyelés](database/monitoring-with-dmvs.md).
