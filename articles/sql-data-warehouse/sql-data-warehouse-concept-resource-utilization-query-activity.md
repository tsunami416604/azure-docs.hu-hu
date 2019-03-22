---
title: Az Azure SQL Data Warehouse kezelhetőségi és figyelés – lekérdezési tevékenység, erőforrás-használat |} A Microsoft Docs
description: Ismerje meg, milyen lehetőségek állnak rendelkezésére, kezelhetők és figyelhetők Azure SQL Data warehouse-bA. Az Azure portal és a dinamikus felügyeleti nézetekkel (DMV-kkel) segítségével megismerheti a lekérdezési tevékenység- és erőforrás-használat a data warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/21/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 69b33b9629863702d576eac39fb4097a2bff2e57
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337570"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse erőforrás kihasználtságát és a lekérdezés tevékenység figyelése
Az Azure SQL Data warehouse-bA az Azure Portalon, a surface elemzéseket nyújt az adatraktár-számítási feladat gazdag figyelési élményt nyújt. Az Azure Portalon az ajánlott eszköz esetén az adattárház figyelését, konfigurálható adatmegőrzési pontokat, riasztások, javaslatok, és testre szabható diagramokat és irányítópultokat a metrikák és naplók biztosít. A portál lehetővé teszi, hogy az egyéb Azure figyelő szolgáltatási, például az Operations Management Suite (OMS) és az Azure Monitor (naplók) egy átfogó figyelési élményt nyújt a nem csupán az adattárház, hanem a teljes Azure analytics is integrálhatja Platform integrált figyelési megoldást. Ez a dokumentáció ismerteti, milyen monitorozási funkciók érhetők el optimalizálása és az elemzési platform az SQL Data Warehouse segítségével kezelheti. 

## <a name="resource-utilization"></a>Erőforrás-használat 
A következő metrikák érhetők el az SQL Data warehouse-hoz az Azure Portalon. Ezek a metrikák végzetesnek [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics).

> [!NOTE]
> 2018. November a mérnöki csapathoz hibát okoz a Processzor- és adat IO százalékos underreport foglalkozik. Ennek hatására a DWU használt, és százalékos aránya, valamint underreport. 

| Metrika neve                           | Leírás     | Aggregáció típusa |
| --------------------------------------- | ---------------- | --------------------------------------- |
| Processzorhasználat (%)                          | Az adatraktár összes csomópont CPU-kihasználtság | Maximum      |
| Adat IO kihasználtsága (%)                      | Az adatraktár összes csomópont i/o-kihasználtsága | Maximum   |
| Sikeres kapcsolatok                  | Az adatok sikeres kapcsolatok száma | Összes            |
| Sikertelen kapcsolatok                      | A data warehouse-bA sikertelen kapcsolatok száma | Összes            |
| Tűzfal által blokkolva                     | A data warehouse, amely le lett tiltva a bejelentkezések száma | Összes            |
| DWU-korlát                              | Szolgáltatási szint célkitűzésének az adatraktár | Maximum   |
| Százalékos DWU                          | Maximális között a CPU és adat IO (%) | Maximum   |
| Alkalmazott DWU                                | DWU limit * DWU percentage | Maximum   |
| Gyorsítótár találati százaléka | (a találatok gyorsítótárazása / gyorsítótár-tévesztés) * 100, ahol a gyorsítótárbeli találatok egyezik meg az oszlopcentrikus szegmensek összes találat, a helyi SSD-gyorsítótárban, és a gyorsítótár-tévesztés az oszlopcentrikus szegmensek tévesztések összegzi az összes csomópont helyi SSD-gyorsítótárban | Maximum |
| Felhasznált gyorsítótár százalékos aránya | (gyorsítótár használt / kapacitás gyorsítótár) * csomópontokon gyorsítótárazza 100, ahol használt gyorsítótár a helyi SSD-gyorsítótár összes bájt összege az összes csomópont között pedig gyorsítótár kapacitás egyezik meg a tárolási kapacitás, helyi SSD | Maximum |
| Helyi tempdb százalékos aránya | Helyi tempdb kihasználtsági összes számítási csomópontokon – értékek vannak kibocsátva 5 percenként | Maximum |

## <a name="query-activity"></a>Leggyakoribb hosszú idejű lekérdezések
Egy programozott élményt, amikor az SQL Data Warehouse a T-SQL-n keresztül a szolgáltatás számos új dinamikus felügyeleti nézetekkel (DMV-kkel). Ezek a nézetek hasznosak, ha aktívan hibaelhárítás és a teljesítmény szűk azonosítja a számítási feladatok.

Tekintse meg a DMV-k használatával, amely az SQL Data Warehouse biztosít a listáját, tekintse meg a [dokumentáció](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Metrikák és diagnosztikai naplózás
Metrikák és naplók is exportálhatók az Azure Monitor, kifejezetten a [naplózza az Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) összetevő programozott módon keresztül érhetők el, és [lekérdezések naplózását](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata). A naplózási késését az SQL Data Warehouse nagyjából 10 – 15 perc alatt van. A késés érintő tényezők a további részletekért látogasson el az alábbi dokumentációt.


## <a name="next-steps"></a>További lépések
A következő útmutatók gyakori forgatókönyveket ismertetik, és a használati esetek, amikor figyelése és az adatraktár kezelése:

- [Figyelheti az adatraktározás számítási feladatáról DMV-k használatával](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)

