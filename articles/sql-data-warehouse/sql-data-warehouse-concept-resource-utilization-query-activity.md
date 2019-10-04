---
title: Kezelhetőség és monitorozás Azure SQL Data Warehouse – lekérdezési tevékenység, erőforrás-használat | Microsoft Docs
description: Ismerje meg, hogy milyen képességek érhetők el a Azure SQL Data Warehouse kezeléséhez és figyeléséhez. Az adattárház lekérdezési tevékenységének és erőforrás-kihasználtságának megismeréséhez használja a Azure Portal és a dinamikus felügyeleti nézeteket (DMV).
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/09/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 786ae1f18d52c6763b60f5019ecfe365f1cd540a
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71334108"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-sql-data-warehouse"></a>Erőforrás-kihasználtság és lekérdezési tevékenységek figyelése Azure SQL Data Warehouse
A Azure SQL Data Warehouse az adattárház számítási feladatainak kiszámításához a Azure Portalon belül széles körű monitorozási lehetőségeket biztosít. Az Azure Portal az ajánlott eszköz az adattárház figyelésére, mivel konfigurálható megőrzési időt, riasztásokat, javaslatokat és testreszabható diagramokat és irányítópultokat biztosít a metrikák és naplók számára. A portál lehetővé teszi az integrációt más Azure-figyelési szolgáltatásokkal, például az Operations Management Suite (OMS) és a Azure Monitor (naplók) használatával, amelyek átfogó figyelési élményt biztosítanak nemcsak az adattárházhoz, hanem a teljes Azure-elemzéshez is platform az integrált monitorozási élményhez. Ez a dokumentáció ismerteti, hogy milyen figyelési képességek érhetők el az elemzési platform SQL Data Warehouse való optimalizálásához és kezeléséhez. 

## <a name="resource-utilization"></a>Erőforrás-felhasználás 
A következő metrikák érhetők el SQL Data Warehouse Azure Portalban. Ezek a metrikák Azure Monitoron keresztül [](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics)vannak felszínben.


| Metrika neve             | Leírás                                                  | Aggregáció típusa |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Processzorhasználat (%)          | CPU-kihasználtság az adatraktár összes csomópontja között      | Maximum          |
| Adat IO százalékos aránya      | I/o-kihasználtság az adatraktár összes csomópontja között       | Maximum          |
| Memóriahasználat (%)       | Memória kihasználtsága (SQL Server) az adatraktár összes csomópontján | Maximum          |
| Sikeres kapcsolatok  | Az adatkapcsolat sikeres kapcsolatainak száma                 | Összes            |
| Sikertelen kapcsolatok      | Az adatraktárhoz való sikertelen kapcsolódások száma           | Összes            |
| Tűzfal blokkolja     | A letiltott adatraktárba való bejelentkezések száma     | Összes            |
| DWU korlátja               | Az adatraktár szolgáltatási szintjének célkitűzése                | Maximum          |
| DWU százalékos aránya          | A CPU-százalék és az adatio-százalék közötti maximális érték        | Maximum          |
| Használt DWU                | DWU limit * DWU percentage                                   | Maximum          |
| Gyorsítótár találati százaléka    | (gyorsítótárbeli találatok/gyorsítótár-kihagyás) * 100, ahol a gyorsítótár-találatok a helyi SSD-gyorsítótár és a gyorsítótár-kihagyott oszlopcentrikus összes találatának összege az összes csomóponton összefoglalt helyi SSD-gyorsítótárban. | Maximum          |
| Gyorsítótár használt százaléka   | (gyorsítótár-használat/gyorsítótár-kapacitás) * 100, ahol a használt gyorsítótár a helyi SSD gyorsítótárban lévő összes bájt összege az összes csomóponton és a gyorsítótár kapacitása a helyi SSD-gyorsítótár tárolási kapacitása az összes csomóponton belül | Maximum          |
| Helyi tempdb százalékos aránya | Helyi tempdb kihasználtsága az összes számítási csomóponton – az értékek öt percenként vannak kibocsátva | Maximum          |

> A metrikák megtekintésekor megfontolandó szempontok és riasztások beállítása:
>
> - Egy adott adattárház esetében nem sikerült a sikertelen és sikeres kapcsolatok jelentése – nem a logikai kiszolgálóhoz
> - A memória százalékos aránya a kihasználtságot is tükrözi, még akkor is, ha az adatraktár tétlen állapotban van – nem tükrözi az aktív munkaterhelés-memória használatát. A metrikák használata és nyomon követése másokkal (tempdb, Gen2 cache), hogy átfogó döntést hozzon, ha a további gyorsítótár-kapacitás skálázása növeli a munkaterhelés teljesítményét a követelmények teljesítése érdekében.


## <a name="query-activity"></a>Lekérdezési tevékenység
A SQL Data Warehouse T-SQL-n keresztül történő figyelésének programozási élményéhez a szolgáltatás a dinamikus felügyeleti nézetek (DMV) készletét biztosítja. Ezek a nézetek akkor hasznosak, ha aktívan hibaelhárítást végez, és azonosítja a teljesítménnyel kapcsolatos szűk keresztmetszeteket.

A SQL Data Warehouse által biztosított DMV listájának megtekintéséhez tekintse meg ezt a [dokumentációt](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Metrikák és diagnosztikai naplózás
Mindkét metrika és napló exportálható Azure Monitorba, pontosabban a [Azure monitor naplózó](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) összetevőt, és a naplózási lekérdezésekkel programozott [](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata)módon férhet hozzá. A SQL Data Warehouse naplózási késése körülbelül 10-15 percet vesz igénybe. A késést befolyásoló tényezőkkel kapcsolatos további információkért látogasson el a következő dokumentációra.


## <a name="next-steps"></a>További lépések
A következő útmutatók ismertetik a gyakori forgatókönyveket és a használati eseteket az adattárház monitorozása és kezelése során:

- [Az adatraktár munkaterhelésének figyelése a DMV](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)
