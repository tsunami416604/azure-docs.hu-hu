---
title: Kezelhetőség és monitorozás – lekérdezési tevékenység, erőforrás-használat
description: Ismerje meg, hogy mely funkciók érhetők el az Azure szinapszis Analytics felügyeletéhez és figyeléséhez. Az adattárház lekérdezési tevékenységének és erőforrás-kihasználtságának megismeréséhez használja a Azure Portal és a dinamikus felügyeleti nézeteket (DMV).
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/09/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: d38c0df45da3a751a456846813543a4ce5de98eb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416220"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Erőforrás-kihasználtság és lekérdezési tevékenységek figyelése az Azure szinapszis Analyticsben

Az Azure szinapszis Analytics a Azure Portalon belül gazdag monitorozási lehetőségeket biztosít az adattárház-számítási feladatokra vonatkozóan. Az Azure Portal az ajánlott eszköz az adattárház figyelésére, mivel konfigurálható megőrzési időt, riasztásokat, javaslatokat és testreszabható diagramokat és irányítópultokat biztosít a metrikák és naplók számára. A portál lehetővé teszi az integrációt más Azure monitoring-szolgáltatásokkal, például a Azure Monitor (naplókkal) a log Analytics segítségével, amely átfogó figyelési élményt nyújt a nem csupán az adattárházhoz, hanem a teljes Azure Analytics-platformot is, amely integrált figyelési élményt biztosít. Ez a dokumentáció ismerteti, hogy milyen figyelési funkciók érhetők el az elemzési platform SQL Analytics szolgáltatással való optimalizálásához és kezeléséhez.

## <a name="resource-utilization"></a>Erőforrás-használat

A következő metrikák érhetők el az SQL Analytics Azure Portal. Ezek a metrikák [Azure monitoron](../../azure-monitor/platform/data-collection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#metrics)keresztül vannak felszínben.

| Metrika neve             | Leírás                                                  | Aggregáció típusa |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Processzorhasználat (%)          | CPU-kihasználtság az adatraktár összes csomópontja között      | AVG, min, Max    |
| Adat IO kihasználtsága (%)      | I/o-kihasználtság az adatraktár összes csomópontja között       | AVG, min, Max    |
| Memória százaléka       | Memória kihasználtsága (SQL Server) az adatraktár összes csomópontján | AVG, min, Max   |
| Aktív lekérdezések          | A rendszeren futtatott aktív lekérdezések száma             | Összeg              |
| Várólistán lévő lekérdezések          | A végrehajtás megkezdésére váró üzenetsor-lekérdezések száma          | Összeg              |
| Sikeres kapcsolatok  | A sikeres kapcsolatok (bejelentkezések) száma az adatbázison | Összeg, darabszám       |
| Sikertelen kapcsolatok      | Sikertelen kapcsolatok (bejelentkezések) száma az adatbázison | Összeg, darabszám       |
| Tűzfal blokkolja     | A letiltott adatraktárba való bejelentkezések száma     | Összeg, darabszám       |
| DWU korlátja               | Az adatraktár szolgáltatási szintjének célkitűzése                | AVG, min, Max    |
| DWU százalékos aránya          | A CPU-százalék és az adatio-százalék közötti maximális érték        | AVG, min, Max    |
| Használt DWU                | DWU korlát * DWU százalék                                   | AVG, min, Max    |
| Gyorsítótár találati százaléka    | (gyorsítótárbeli találatok/gyorsítótár-kihagyás) * 100, ahol a gyorsítótár-találatok a helyi SSD-gyorsítótár és a gyorsítótár-kihagyott oszlopcentrikus összes találatának összege az összes csomóponton összefoglalt helyi SSD-gyorsítótárban. | AVG, min, Max    |
| Gyorsítótár használt százaléka   | (gyorsítótár-használat/gyorsítótár-kapacitás) * 100, ahol a használt gyorsítótár a helyi SSD gyorsítótárban lévő összes bájt összege az összes csomóponton és a gyorsítótár kapacitása a helyi SSD-gyorsítótár tárolási kapacitása az összes csomóponton belül | AVG, min, Max    |
| Helyi tempdb százalékos aránya | Helyi tempdb kihasználtsága az összes számítási csomóponton – az értékek öt percenként vannak kibocsátva | AVG, min, Max    |
| Adattárolás mérete (GB) | Az adatbázis teljes mérete. Ez magában foglalja a használt, a fenntartott és a nem lefoglalt területet. A nem lefoglalt terület a lekérdezés és a terhelés teljesítményének optimalizálása érdekében az adatbázis számára van tárolva. | Összeg |
| Vész-helyreállítási méret (GB) | A földrajzi biztonsági mentés 24 óránként végzett teljes mérete | Összeg |
| Pillanatkép-tároló mérete (GB) | Az adatbázis-visszaállítási pontok biztosításához készített pillanatképek teljes mérete. Ez magában foglalja az automatizált és a felhasználó által definiált pillanatképeket is. | Összeg |

A metrikák megtekintésekor megfontolandó szempontok és riasztások beállítása:

- A használt DWU csak az SQL-készletben lévő **használat magas szintű ábrázolását** jelöli, és nem a kihasználtság átfogó mutatója. Annak megállapításához, hogy fel vagy le kell-e skálázást, gondolja át a DWU által befolyásolható összes tényezőt, például a párhuzamosságot, a memóriát, a tempdb és az adaptív gyorsítótárazási kapacitást. Azt javasoljuk, hogy [a számítási feladatokat különböző DWU-beállításokon futtassa](sql-data-warehouse-manage-compute-overview.md#finding-the-right-size-of-data-warehouse-units) , hogy megtudja, mi a legmegfelelőbb az üzleti célok teljesítéséhez.
- Egy adott adattárház esetében nem sikerült a sikertelen és sikeres kapcsolatok jelentése – nem a logikai kiszolgálóhoz
- A memória százalékos aránya a kihasználtságot is tükrözi, még akkor is, ha az adatraktár tétlen állapotban van – nem tükrözi az aktív munkaterhelés-memória használatát. A metrikák használata és nyomon követése másokkal (tempdb, Gen2 cache), hogy átfogó döntést hozzon, ha a további gyorsítótár-kapacitás skálázása növeli a munkaterhelés teljesítményét a követelmények teljesítése érdekében.

## <a name="query-activity"></a>Lekérdezési tevékenység

Az SQL Analytics T-SQL használatával történő figyelése során a szolgáltatás a dinamikus felügyeleti nézetek (DMV-EK) készletét biztosítja. Ezek a nézetek akkor hasznosak, ha aktívan hibaelhárítást végez, és azonosítja a teljesítménnyel kapcsolatos szűk keresztmetszeteket.

A szinapszis SQL-re vonatkozó DMV listájának megtekintéséhez tekintse meg ezt a [dokumentációt](../sql/reference-tsql-system-views.md#sql-pool-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Metrikák és diagnosztikai naplózás 

Mindkét metrika és napló exportálható Azure Monitorba, pontosabban a [Azure monitor naplózó](../../azure-monitor/log-query/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) összetevőt, és a [naplózási lekérdezésekkel](../../azure-monitor/log-query/get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)programozott módon férhet hozzá. Az SQL Analytics naplózási késése körülbelül 10-15 percet vesz igénybe. A késést befolyásoló tényezőkkel kapcsolatos további információkért látogasson el a következő dokumentációra.

## <a name="next-steps"></a>További lépések

Az alábbi útmutató a gyakori forgatókönyveket és a használati eseteket ismerteti az adatraktár monitorozása és kezelése során:

- [Az adatraktár munkaterhelésének figyelése a DMV](sql-data-warehouse-manage-monitor.md)
