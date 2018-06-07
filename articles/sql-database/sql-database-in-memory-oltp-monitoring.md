---
title: XTP memórián belüli tároló figyelése |} Microsoft Docs
description: Becsült és a figyelő XTP memórián belüli tároló használatához kapacitás; Hárítsa el a kapacitás hiba 41823
services: sql-database
author: jodebrui
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: jodebrui
ms.openlocfilehash: aff0f82f07e9129c8f7c131f055447ad578ad15b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647340"
---
# <a name="monitor-in-memory-oltp-storage"></a>A figyelő a memórián belüli online Tranzakciófeldolgozási tároló
Használata esetén [memórián belüli online Tranzakciófeldolgozási](sql-database-in-memory.md), a memóriaoptimalizált táblák és Táblaváltozók adatainak memórián belüli online Tranzakciófeldolgozási tárolóban található. Minden prémium és fontos üzleti szolgáltatási szinthez tartozik memórián belüli online Tranzakciófeldolgozási tároló mérete, ami ismertetett [DTU-alapú erőforrás korlátok](sql-database-dtu-resource-limits.md) és [vCore-alapú erőforrás korlátok](sql-database-vcore-resource-limits.md). Ha túllépi ezt a határt, beszúrási és a frissítési műveletek kezdheti el önálló adatbázisok 41823 hiba és a rugalmas 41840 hiba miatt sikertelenül működő. Ekkor meg kell memória felszabadításához adatok törlése, illetve a teljesítményszintet az adatbázis frissítéséhez.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Határozza meg, hogy adatok elférje a memórián belüli online Tranzakciófeldolgozási tárolási kap
Határozza meg, hogy a tároló biztosít, amelyet a különböző szolgáltatásrétegeiben használt funkciókkal. Lásd: [DTU-alapú erőforrás korlátok](sql-database-dtu-resource-limits.md) és [vCore-alapú erőforrás korlátok](sql-database-vcore-resource-limits.md).

Memóriakövetelményei memóriaoptimalizált táblák működik, mert az SQL Server ugyanúgy nem Azure SQL Database becslése. Tekintse át az előző cikket a néhány percet igénybe vehet [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Tábla és a tábla változó sorok, valamint az indexek, száma, a maximális felhasználói adatok mérete felé. Emellett az ALTER TABLE kell elég hely a teljes táblázat és az indexeket új verziót hoz létre.

## <a name="monitoring-and-alerting"></a>Figyelés és riasztás
A tárolási kap százalékában tárolóhely-használat memóriában a teljesítmény szinthez a figyelheti a [Azure-portálon](https://portal.azure.com/): 

1. Az adatbázis panelen keresse meg az Erőforrás kihasználtsága mezőbe, majd kattintson a Szerkesztés.
2. Válassza ki a `In-Memory OLTP Storage percentage`.
3. Adja hozzá a riasztást, az erőforrás-használat párbeszédpanel megnyitásához a metrika panelen kattintson, majd kattintson a Hozzáadás riasztás.

Vagy a memórián belüli tárhely kihasználtságát megjelenítéséhez használja a következő lekérdezést:

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Javítsa ki a-memória OLTP tárolási helyzetek - 41823 és 41840 hibák
Elérte-e a memórián belüli online Tranzakciófeldolgozási tárolási kap a Beszúrás adatbázis eredményezi, frissítse, ALTER, hibaüzenet 41823 (az önálló adatbázisok) vagy (a rugalmas) 41840 hiba miatt sikertelenül működő műveletek létrehozása. Mindkét hibákat okozhat az aktív tranzakció megszakítását.

Hibaüzenetek 41823 és 41840 azt jelzi, hogy a memóriaoptimalizált táblák és az adatbázis vagy a készlet Táblaváltozók elérte a memórián belüli online Tranzakciófeldolgozási maximális tárolómérete.

Ez a hiba vagy megoldása:

* A memóriaoptimalizált táblák, potenciálisan kiszervezésével a hagyományos, a lemezalapú táblák; az adatok adatok törlése vagy,
* A szolgáltatási rétegben váltson egy, az elegendő memórián belüli tároló memóriaoptimalizált táblák megtartja a szükséges adatok.

> [!NOTE] 
> Ritka esetekben hibák 41823 és 41840 átmeneti, ami azt jelenti, nincs elég tárterület a memórián belüli online Tranzakciófeldolgozási, és a művelet sikeres lehet. Ezért ajánlott mindkét figyelője az a teljes elérhető memórián belüli online Tranzakciófeldolgozási tárolót, és próbálja meg, amikor először észlelt a 41823 vagy 41840 hiba. Újrapróbálkozási logika kapcsolatos további információkért lásd: [előforduló ütközések észlelésére és logika ismételje meg a memórián belüli online Tranzakciófeldolgozási](https://docs.microsoft.com/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>További lépések
Figyelés útmutatást, lásd: [figyelése Azure SQL Database dinamikus felügyeleti nézetekkel](sql-database-monitoring-with-dmvs.md).
