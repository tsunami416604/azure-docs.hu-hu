---
title: Kezelhetőség és figyelés – lekérdezési tevékenység, erőforrás-kihasználtság
description: Ismerje meg, milyen képességek érhetők el az Azure Synapse Analytics kezeléséhez és figyeléséhez. Az Azure Portal és a dinamikus felügyeleti nézetek (DMV-k) használatával megismerheti a lekérdezési tevékenységet és az adatraktár erőforrás-kihasználtságát.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416220"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Erőforrás-kihasználtság és lekérdezési tevékenység figyelése az Azure Synapse Analytics szolgáltatásban

Az Azure Synapse Analytics gazdag figyelési élményt nyújt az Azure Portalon az adattárház-munkaterheléssel kapcsolatos információk felszínre hozásához. Az Azure Portal az ajánlott eszköz az adattárház figyelése, mivel konfigurálható megőrzési időszakok, riasztások, javaslatok és testreszabható diagramok és irányítópultok metrikák és naplók. A portál azt is lehetővé teszi, hogy integrálja más Azure figyelési szolgáltatások, például az Azure Monitor (naplók) a Log analytics, hogy holisztikus figyelési élményt nem csak az adattárház, hanem a teljes Azure Analytics platform integrált figyelési élményt. Ez a dokumentáció ismerteti, hogy milyen figyelési lehetőségek állnak rendelkezésre az elemzési platform optimalizálásához és kezeléséhez az SQL Analytics segítségével.

## <a name="resource-utilization"></a>Erőforrás-használat

A következő metrikák érhetők el az Azure Portalon az SQL Analytics. Ezek a mérőszámok az [Azure Monitoron](../../azure-monitor/platform/data-collection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#metrics)keresztül kerülnek felszínre.

| Metrikus neve             | Leírás                                                  | Aggregáció típusa |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Processzorhasználat (%)          | PROCESSZOR-kihasználtság az adattárház összes csomópontja között      | Átlagos, Min, Max    |
| Adat IO kihasználtsága (%)      | Io-kihasználtság az adattárház összes csomópontja között       | Átlagos, Min, Max    |
| Memória százaléka       | Memóriakihasználtság (SQL Server) az adattárház összes csomópontjában | Átlagos, Min, Max   |
| Aktív lekérdezések          | A rendszeren végrehajtó aktív lekérdezések száma             | Összeg              |
| Várakozással elévülve lekérdezések          | A végrehajtás megkezdésére váró várakozó várólistán lévő lekérdezések száma          | Összeg              |
| Sikeres kapcsolatok  | Az adatbázissal létesített sikeres kapcsolatok (bejelentkezések) száma | Összeg, Darabszám       |
| Sikertelen kapcsolatok      | Az adatbázishoz létesített sikertelen kapcsolatok (bejelentkezések) száma | Összeg, Darabszám       |
| Tűzfal blokkolja     | Zárolt adattárházba való bejelentkezések száma     | Összeg, Darabszám       |
| DWU-korlát               | Az adattárház szolgáltatási szintre vonatkozó célkitűzése                | Átlagos, Min, Max    |
| DWU százalék          | Maximális cpu-százalék és adat I/o százalék között        | Átlagos, Min, Max    |
| Használt DWU                | DWU határérték * DWU százalék                                   | Átlagos, Min, Max    |
| Gyorsítótár találati százaléka    | (cache hits / cache miss) * 100, ahol a gyorsítótár-találatok összege az összes oszlopcentrikus szegmensek találat a helyi SSD cache és cache hiányzik az oszlopcentrikus szegmensek hiányzik a helyi SSD cache összegezve az összes csomópont | Átlagos, Min, Max    |
| Gyorsítótár által használt százalék   | (használt gyorsítótár / gyorsítótár kapacitása) * 100, ahol a gyorsítótár at a helyi SSD-gyorsítótár ban lévő összes bájt összege az összes csomóponton és a gyorsítótár kapacitásában a helyi SSD-gyorsítótár tárolókapacitásának összege az összes csomóponton | Átlagos, Min, Max    |
| Helyi tempdb százalék | Helyi tempdb-használat az összes számítási csomóponton – az értékek ötpercenként kerülnek kibocsátásra | Átlagos, Min, Max    |
| Adattároló mérete (GB) | Az adatbázis teljes mérete. Ez magában foglalja a használt, fenntartott és szabad területet. A lekérdezési és betöltési teljesítmény optimalizálása érdekében az adatbázis számára a szabad terület megmarad. | Összeg |
| Vész-helyreállítási méret (GB) | A 24 óránként készített geobiztonsági mentés teljes mérete | Összeg |
| Pillanatkép-tároló mérete (GB) | Az adatbázis-visszaállítási pontok biztosításához készített pillanatképek teljes mérete. Ez magában foglalja az automatizált és a felhasználó által definiált pillanatképek. | Összeg |

A mérőszámok megtekintésekor és a riasztások beállításakor figyelembe vethető tudnivalók:

- A használt DWU csak az **SQL-készlet használatának magas szintű megjelenítését** jelenti, és nem a használat átfogó mutatója. Annak meghatározásához, hogy felskálázást vagy lefelé skálázható-e, fontolja meg az összes olyan tényezőt, amelyre hatással lehet a DWU, például az egyidejűség, a memória, a tempdb és az adaptív gyorsítótár kapacitása. Javasoljuk, hogy [futassza a számítási feladatokat különböző DWU-beállításokon](sql-data-warehouse-manage-compute-overview.md#finding-the-right-size-of-data-warehouse-units) annak meghatározásához, hogy mi felel meg a legjobban az üzleti céloknak.
- A rendszer egy adott adattárházhoz nem a logikai kiszolgálóhoz jelenti a sikertelen és sikeres kapcsolatokat
- A memória százaléka akkor is tükrözi a kihasználtságot, ha az adattárház tétlen állapotban van – nem tükrözi az aktív munkaterhelés memóriafelhasználását. Használja és kövesse nyomon ezt a metrikát másokkal (tempdb, gen2 gyorsítótár) együtt, hogy holisztikus döntést hozzon abból, hogy a további gyorsítótár-kapacitás méretezése növeli-e a számítási feladatok teljesítményét a követelményeknek megfelelően.

## <a name="query-activity"></a>Lekérdezési tevékenység

Az SQL Analytics T-SQL-en keresztüli figyelése programozott élménye érdekében a szolgáltatás dinamikus felügyeleti nézetek (DMV-k) készletét biztosítja. Ezek a nézetek akkor hasznosak, ha aktívan hibaelhárításés és a teljesítmény szűk keresztmetszetek azonosítása a számítási feladatok.

A Synapse SQL-re vonatkozó DMV-k listájának megtekintéséhez tekintse meg ezt a [dokumentációt.](../sql/reference-tsql-system-views.md#sql-pool-dynamic-management-views-dmvs) 

## <a name="metrics-and-diagnostics-logging"></a>Metrikák és diagnosztikai naplózás 

A metrikák és a naplók egyaránt exportálhatók az Azure Monitorba, különösen az [Azure Monitor naplói](../../azure-monitor/log-query/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) összetevőbe, és programozott módon érhetők el [a naplólekérdezéseken](../../azure-monitor/log-query/get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)keresztül. Az SQL Analytics naplókése körülbelül 10–15 perc. A késést befolyásoló tényezőkről az alábbi dokumentációban olvashat bővebben.

## <a name="next-steps"></a>További lépések

Az alábbi útmutató ismerteti az adattárház figyelése és kezelése során előforduló gyakori forgatókönyveket és használati eseteket:

- [Az adattárház-munkaterhelés figyelése DMV-kkel](sql-data-warehouse-manage-monitor.md)
