---
title: "XTP memórián belüli tároló figyelése |} Microsoft Docs"
description: "Becsült és a figyelő XTP memórián belüli tároló használatához kapacitás; Hárítsa el a kapacitás hiba 41823"
services: sql-database
documentationcenter: 
author: jodebrui
manager: jhubbard
editor: 
ms.assetid: b617308e-692c-4938-8fa2-070034a3ecef
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jodebrui
ms.openlocfilehash: 613a9ced91d71cc9a65ea67e6ede1a78a03b4bd5
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="monitor-in-memory-oltp-storage"></a>A figyelő a memórián belüli online Tranzakciófeldolgozási tároló
Használata esetén [memórián belüli online Tranzakciófeldolgozási](sql-database-in-memory.md), a memóriaoptimalizált táblák és Táblaváltozók adatainak memórián belüli online Tranzakciófeldolgozási tárolóban található. Minden prémium szolgáltatásszintet felső korlátja memórián belüli online Tranzakciófeldolgozási tároló, amely ismertetett [egyetlen adatbázis erőforrás korlátok](sql-database-resource-limits.md#single-database-storage-sizes-and-performance-levels) és [rugalmas készlet erőforrás korlátok](sql-database-resource-limits.md#elastic-pool-change-storage-size). Ha túllépi ezt a határt, beszúrási és a frissítési műveletek kezdheti el hiba miatt sikertelenül (41823). Ezen a ponton fog kell bármelyik törli memória felszabadításához adatokat, illetve a teljesítményszintet az adatbázis frissítéséhez.

## <a name="determine-whether-data-will-fit-within-the-in-memory-storage-cap"></a>Határozza meg, hogy adatokat illeszkedni fog-e a memórián belüli tároló kap
A tárolási biztosít, amelyet a különböző Premium szolgáltatásszintek határozza meg. Lásd: [egyetlen adatbázis erőforrás korlátok](sql-database-resource-limits.md#single-database-storage-sizes-and-performance-levels) és [rugalmas készlet erőforrás korlátok](sql-database-resource-limits.md#elastic-pool-change-storage-size).

Memóriakövetelményei memóriaoptimalizált táblák működik, mert az SQL Server ugyanúgy nem Azure SQL Database becslése. Tekintse át a témakör a néhány percet igénybe vehet [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Vegye figyelembe, hogy a tábla és változó táblázat sorait, valamint indexek száma felé a maximális felhasználói adatok mérete. Emellett az ALTER TABLE kell elég hely a teljes táblázat és az indexeket új verziót hoz létre.

## <a name="monitoring-and-alerting"></a>Figyelés és riasztás
A tárolási kap százalékában memórián belüli tároló használata a teljesítmény szinthez a figyelheti a [Azure-portálon](https://portal.azure.com/): 

1. Az adatbázis panelen keresse meg az Erőforrás kihasználtsága mezőbe, majd kattintson a Szerkesztés.
2. Válassza ki a `In-Memory OLTP Storage percentage`.
3. Adja hozzá a riasztást, az erőforrás-használat párbeszédpanel megnyitásához a metrika panelen kattintson, majd kattintson a Hozzáadás riasztás.

Vagy a memóriában lévő tárhely kihasználtságát megjelenítéséhez használja a következő lekérdezést:

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## <a name="correct-out-of-memory-situations---error-41823"></a>Javítsa ki a kevés memória helyzetek - 41823 hiba
Kevés a memória eredmények futó INSERT, UPDATE és LÉTREHOZÁSI műveletek 41823 hiba miatt sikertelen.

Hibaüzenet 41823 azt jelzi, hogy a memóriaoptimalizált táblák és Táblaváltozók túllépte a maximális méretet.

Ez a hiba vagy megoldása:

* A memóriaoptimalizált táblák, potenciálisan kiszervezésével a hagyományos, a lemezalapú táblák; az adatok adatok törlése vagy,
* A szolgáltatási rétegben váltson egy, az elegendő memórián belüli tároló memóriaoptimalizált táblák megtartja a szükséges adatok.

## <a name="next-steps"></a>Következő lépések
Figyelés útmutatást, lásd: [figyelése Azure SQL Database dinamikus felügyeleti nézetekkel](sql-database-monitoring-with-dmvs.md).
