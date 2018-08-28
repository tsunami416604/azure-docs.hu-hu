---
title: Az Azure SQL Data Warehouse kezelhetőségi és figyelés – lekérdezési tevékenység, erőforrás-használat |} A Microsoft Docs
description: Ismerje meg, milyen lehetőségek állnak rendelkezésére, kezelhetők és figyelhetők Azure SQL Data warehouse-bA. Az Azure portal és a dinamikus felügyeleti nézetekkel (DMV-kkel) segítségével megismerheti a lekérdezési tevékenység- és erőforrás-használat a data warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/26/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 7ff304fa478942254cca372282a30a1a3f00f354
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43113008"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse erőforrás kihasználtságát és a lekérdezés tevékenység figyelése
Az Azure SQL Data warehouse-bA az Azure Portalon, a surface elemzéseket nyújt az adatraktár-számítási feladat gazdag figyelési élményt nyújt. Az Azure Portalon az ajánlott eszköz esetén az adattárház figyelését, konfigurálható visszatartások időszakok, riasztások, javaslatok, és testre szabható diagramokat és irányítópultokat a metrikákat és naplókat biztosít. A portál lehetővé teszi, hogy integrálása más Azure-szolgáltatások, például az Operations Management Suite (OMS) Log Analytics és az Azure Monitor biztosít olyan átfogó figyelési élmény a nem csupán az adattárház, hanem a teljes Azure is / integrált figyelési megoldást elemzési platform. Ez a dokumentáció ismerteti, milyen monitorozási funkciók érhetők el optimalizálása és az elemzési platform az SQL Data Warehouse segítségével kezelheti. 

## <a name="resource-utilization"></a>Erőforrás-használat 
A következő metrikák érhetők el az Azure Portalon.

| Metrika neve                           | Leírás     | Aggregáció típusa |
| --------------------------------------- | ---------------- | --------------------------------------- |
| Processzorhasználat (%)                          | Az adatraktár összes csomópont CPU-kihasználtság | Maximum      |
| Adat IO kihasználtsága (%)                      | Az adatraktár összes csomópont i/o-kihasználtsága | Maximum   |
| Sikeres kapcsolatok                  | Az adatok sikeres kapcsolatok száma | Összes            |
| Sikertelen kapcsolatok                      | A data warehouse-bA sikertelen kapcsolatok száma | Összes            |
| Tűzfal által blokkolva                     | A data warehouse, amely le lett tiltva a bejelentkezések száma | Összes            |
| DWU-korlát                              | Szolgáltatási szint célkitűzésének az adatraktár | Maximum   |
| Százalékos DWU                          | Maximális között a CPU és adat IO (%) | Maximum   |
| Alkalmazott DWU                                | DWU-korlát * százalékos DWU | Maximum   |
| Gyorsítótár találati százaléka | (a találatok gyorsítótárazása / gyorsítótár-tévesztés) * 100, ahol a gyorsítótárbeli találatok egyezik meg az oszlopcentrikus szegmensek összes találat, a helyi SSD-gyorsítótárban, és a gyorsítótár-tévesztés az oszlopcentrikus szegmensek tévesztések összegzi az összes csomópont helyi SSD-gyorsítótárban | Maximum |
| Felhasznált gyorsítótár százalékos aránya | (gyorsítótár használt / kapacitás gyorsítótár) * csomópontokon gyorsítótárazza 100, ahol használt gyorsítótár a helyi SSD-gyorsítótár összes bájt összege az összes csomópont között pedig gyorsítótár kapacitás egyezik meg a tárolási kapacitás, helyi SSD | Maximum |

## <a name="query-activity"></a>Leggyakoribb hosszú idejű lekérdezések
Egy programozott élményt, amikor az SQL Data Warehouse a T-SQL-n keresztül a szolgáltatás számos új dinamikus felügyeleti nézetekkel (DMV-kkel). Ezek a nézetek hasznosak, ha aktívan hibaelhárítás és a teljesítmény szűk azonosítja a számítási feladatok.

Tekintse meg a DMV-k használatával, amely az SQL Data Warehouse biztosít a listáját, tekintse meg a [dokumentáció](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Metrikák és diagnosztikai naplózás
Metrikák és naplók is exportálhatók [Operations Management Suite](https://azure.microsoft.com/resources/videos/operations-management-suite-oms-overview/) (OMS), kifejezetten a [Log analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) összetevő programozott módon keresztül érhetők el, és [naplókkeresése](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata).


> [!NOTE]
> 2018 augusztus naplók jelenleg hajtják végre az SQL Data warehouse-hoz

## <a name="next-steps"></a>További lépések
A következő útmutatók gyakori forgatókönyveket ismertetik, és a használati esetek, amikor figyelése és az adatraktár kezelése:

- [Figyelheti az adatraktározás számítási feladatáról DMV-k használatával](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)

