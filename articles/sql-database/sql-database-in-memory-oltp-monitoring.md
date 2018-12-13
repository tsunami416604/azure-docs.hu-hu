---
title: XTP memóriabeli tárolás monitorozása |} A Microsoft Docs
description: Becslés és figyelő XTP memóriabeli tárolás használata esetén kapacitás; kapacitás hiba 41823 megoldása
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jodebrui
ms.author: jodebrui
ms.reviewer: genemi
manager: craigg
ms.date: 09/14/2018
ms.openlocfilehash: ac7b568d95b9a2c382b1c167965942f0733012c4
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52874923"
---
# <a name="monitor-in-memory-oltp-storage"></a>A figyelő In-Memory OLTP-tár
Használata esetén [In-Memory OLTP](sql-database-in-memory.md), a memóriaoptimalizált táblák és Táblaváltozók adatok találhatók az In-Memory OLTP storage-ban. Minden prémium és üzletileg kritikus szolgáltatási szint In-Memory OLTP storage mérete tartalmaz. Lásd: [DTU-alapú erőforráskorlátok – önálló adatbázis](sql-database-dtu-resource-limits-single-databases.md), [DTU-alapú erőforráskorlátok – rugalmas készletek](sql-database-dtu-resource-limits-elastic-pools.md),[Virtuálismag-alapú erőforráskorlátok – önálló adatbázisok](sql-database-vcore-resource-limits-single-databases.md) és [Virtuálismag-alapú erőforráskorlátok – rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md).

Ha meghaladja a korlátot, beszúrási és frissítési műveletek kezdheti el hiba 41823 az önálló adatbázisok és rugalmas készletekhez 41840 hiba miatt sikertelenül működő. Ekkor vagy törölni szeretne felszabadítani a memória, vagy frissítse a szolgáltatási rétegben, vagy az adatbázis méretét a számítási adatokat.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Határozza meg, hogy adat megfelel-e a memórián belüli online Tranzakciófeldolgozási Tárhelykorlát
A tárolási korlátokat a különböző szolgáltatásszintek határozza meg. Lásd: [DTU-alapú erőforráskorlátok – önálló adatbázis](sql-database-dtu-resource-limits-single-databases.md), [DTU-alapú erőforráskorlátok – rugalmas készletek](sql-database-dtu-resource-limits-elastic-pools.md),[Virtuálismag-alapú erőforráskorlátok – önálló adatbázisok](sql-database-vcore-resource-limits-single-databases.md) és [Virtuálismag-alapú erőforráskorlátok – rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md).

Memóriakövetelményei egy memóriaoptimalizált tábla működik, mert az SQL Server ugyanúgy Azure SQL Database nem szükséges. Várjon néhány percet, ellenőrizze, hogy a cikk a [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Tábla és táblasorokat változó, valamint az indexek, a felhasználó maximális adatméret beleszámítanak. Emellett az ALTER TABLE szüksége van elég hely az új verzió a teljes tábla-és az indexek létrehozása.

## <a name="monitoring-and-alerting"></a>Figyelés és riasztás
A számítási méret a memórián belüli tárhely kihasználtsága a Tárhelykorlát százalékában figyelheti a [az Azure portal](https://portal.azure.com/): 

1. Az adatbázis panelen keresse meg az Erőforrás kihasználtsága mezőbe, majd kattintson a Szerkesztés.
2. Válassza ki a metrikát `In-Memory OLTP Storage percentage`.
3. Adjon hozzá egy riasztást, az erőforrás-használatot, ha azt szeretné, hogy a metrika panel megnyitásához kattintson, majd kattintson a riasztás hozzáadása.

Vagy használja a következő lekérdezést a memórián belüli tárterület kihasználtsága megjelenítéséhez:

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Megfelelő out-az-In-Memory OLTP storage helyzetekben - hibák 41823 és 41840
Szerezze meg a memórián belüli online Tranzakciófeldolgozási Tárhelykorlát a Beszúrás adatbázis eredményezi, frissítés, az ALTER és -LÉTREHOZÁSI műveletek hibaüzenet 41823 (az önálló adatbázisok), vagy (a rugalmas készletek) 41840 hiba miatt sikertelenül működő. Mindkét hibák miatt megszakítja az aktív tranzakció.

Hibaüzenetek 41823 és 41840 azt jelzik, hogy a memóriaoptimalizált táblák és az adatbázis vagy készlet Táblaváltozók elérte a maximális In-Memory OLTP-tároló mérete.

Ez a hiba vagy megoldása:

* A memóriaoptimalizált táblákban, potenciálisan a hagyományos, a lemezalapú táblák; az adatok kiszervezése adatok törlése vagy,
* Frissítse a szolgáltatási rétegben egyik az elegendő memórián belüli storage az adatok megőrzése a memóriaoptimalizált táblák.

> [!NOTE] 
> Bizonyos ritkán előforduló esetekben 41823 és 41840 hiba lehet átmeneti, ami azt jelenti, nincs elég rendelkezésre álló In-Memory OLTP-tár és sikeres megismételni a műveletet. Javasoljuk, hogy mind a teljes rendelkezésre álló In-Memory OLTP storage figyelése, és próbálkozzon újra, amikor először 41823 vagy 41840 hiba. Újrapróbálkozási logika kapcsolatos további információkért lásd: [ütközésészlelés és az újrapróbálkozási logika az In-Memory OLTP](https://docs.microsoft.com/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>További lépések
Figyelés útmutatást lásd: [Azure SQL Database monitorozása a dinamikus felügyeleti nézetek használatával](sql-database-monitoring-with-dmvs.md).
