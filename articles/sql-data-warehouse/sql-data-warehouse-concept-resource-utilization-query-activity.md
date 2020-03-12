---
title: Kezelhetőség és monitorozás – lekérdezési tevékenység, erőforrás-használat
description: Ismerje meg, hogy mely funkciók érhetők el az Azure szinapszis Analytics felügyeletéhez és figyeléséhez. Az adattárház lekérdezési tevékenységének és erőforrás-kihasználtságának megismeréséhez használja a Azure Portal és a dinamikus felügyeleti nézeteket (DMV).
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/11/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 82bf6f9a78a46659cc2e0955895c6e1a6e6eb3aa
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096623"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Erőforrás-kihasználtság és lekérdezési tevékenységek figyelése az Azure szinapszis Analyticsben
Az Azure szinapszis Analytics a Azure Portalon belül gazdag monitorozási lehetőségeket biztosít az adattárház-számítási feladatokra vonatkozóan. Az Azure Portal az ajánlott eszköz az adattárház figyelésére, mivel konfigurálható megőrzési időt, riasztásokat, javaslatokat és testreszabható diagramokat és irányítópultokat biztosít a metrikák és naplók számára. A portál lehetővé teszi a log Analytics szolgáltatással való integrációt a többi Azure monitoring-szolgáltatással, például a Azure Monitor (naplókkal), hogy átfogó figyelési élményt nyújtson a nem csupán az adattárházhoz, hanem a teljes Azure Analytics-platformot az integrált figyelési élmény. Ez a dokumentáció ismerteti, hogy milyen figyelési funkciók érhetők el az elemzési platform SQL Analytics szolgáltatással való optimalizálásához és kezeléséhez. 

## <a name="resource-utilization"></a>Erőforrás-használat 
A következő metrikák érhetők el az SQL Analytics Azure Portal. Ezek a metrikák [Azure monitoron](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics)keresztül vannak felszínben.


| Metrika neve             | Leírás                                                  | Aggregáció típusa |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Processzorhasználat (%)          | CPU-kihasználtság az adatraktár összes csomópontja között      | AVG, min, Max    |
| Adat IO kihasználtsága (%)      | I/o-kihasználtság az adatraktár összes csomópontja között       | AVG, min, Max    |
| Memória százaléka       | Memória kihasználtsága (SQL Server) az adatraktár összes csomópontján | AVG, min, Max   |
| Aktív lekérdezések          | A rendszeren futtatott aktív lekérdezések száma             | Összeg              |
| Várólistán lévő lekérdezések          | A végrehajtás megkezdésére váró üzenetsor-lekérdezések száma          | Összeg              |
| Sikeres kapcsolatok  | Az adatkapcsolat sikeres kapcsolatainak száma                 | Összeg, darabszám       |
| Sikertelen kapcsolatok      | Az adatraktárhoz való sikertelen kapcsolódások száma           | Összeg, darabszám       |
| Tűzfal blokkolja     | A letiltott adatraktárba való bejelentkezések száma     | Összeg, darabszám       |
| DWU korlátja               | Az adatraktár szolgáltatási szintjének célkitűzése                | AVG, min, Max    |
| DWU százalékos aránya          | A CPU-százalék és az adatio-százalék közötti maximális érték        | AVG, min, Max    |
| Használt DWU                | DWU limit * DWU percentage                                   | AVG, min, Max    |
| Gyorsítótár találati százaléka    | (gyorsítótárbeli találatok/gyorsítótár-kihagyás) * 100, ahol a gyorsítótár-találatok a helyi SSD-gyorsítótár és a gyorsítótár-kihagyott oszlopcentrikus összes találatának összege az összes csomóponton összefoglalt helyi SSD-gyorsítótárban. | AVG, min, Max    |
| Gyorsítótár használt százaléka   | (gyorsítótár-használat/gyorsítótár-kapacitás) * 100, ahol a használt gyorsítótár a helyi SSD gyorsítótárban lévő összes bájt összege az összes csomóponton és a gyorsítótár kapacitása a helyi SSD-gyorsítótár tárolási kapacitása az összes csomóponton belül | AVG, min, Max    |
| Helyi tempdb százalékos aránya | Helyi tempdb kihasználtsága az összes számítási csomóponton – az értékek öt percenként vannak kibocsátva | AVG, min, Max    |
| Adattárolás mérete | Az adatbázisba betöltött adatmennyiség teljes mérete. Ide tartoznak a KKU-ban és a nem CCI-táblázatokban tárolt adatmennyiségek, amelyekben a nem CCI-táblázatok mérete a teljes adatbázis-fájlméret alapján mérhető | Összeg |
| Vész-helyreállítási méret | A földrajzi biztonsági mentés 24 óránként végzett teljes mérete | Összeg |
| Pillanatkép-tároló mérete | Az adatbázis-visszaállítási pontok biztosításához készített pillanatképek teljes mérete. Ez magában foglalja az automatizált és a felhasználó által definiált pillanatképeket is. | Összeg |

A metrikák megtekintésekor megfontolandó szempontok és riasztások beállítása:

- A használt DWU csak az SQL-készletben lévő **használat magas szintű ábrázolását** jelöli, és nem a kihasználtság átfogó mutatója. Annak megállapításához, hogy fel vagy le kell-e skálázást, gondolja át a DWU által befolyásolható összes tényezőt, például a párhuzamosságot, a memóriát, a tempdb és az adaptív gyorsítótárazási kapacitást. Azt javasoljuk, hogy [a számítási feladatokat különböző DWU-beállításokon futtassa](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-compute-overview#finding-the-right-size-of-data-warehouse-units) , hogy megtudja, mi a legmegfelelőbb az üzleti célok teljesítéséhez.
- Egy adott adattárház esetében nem sikerült a sikertelen és sikeres kapcsolatok jelentése – nem a logikai kiszolgálóhoz
- A memória százalékos aránya a kihasználtságot is tükrözi, még akkor is, ha az adatraktár tétlen állapotban van – nem tükrözi az aktív munkaterhelés-memória használatát. A metrikák használata és nyomon követése másokkal (tempdb, Gen2 cache), hogy átfogó döntést hozzon, ha a további gyorsítótár-kapacitás skálázása növeli a munkaterhelés teljesítményét a követelmények teljesítése érdekében.


## <a name="query-activity"></a>Lekérdezési tevékenység
Az SQL Analytics T-SQL használatával történő figyelése során a szolgáltatás a dinamikus felügyeleti nézetek (DMV-EK) készletét biztosítja. Ezek a nézetek akkor hasznosak, ha aktívan hibaelhárítást végez, és azonosítja a teljesítménnyel kapcsolatos szűk keresztmetszeteket.

Az SQL Analytics által biztosított DMV listájának megtekintéséhez tekintse meg ezt a [dokumentációt](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Metrikák és diagnosztikai naplózás
Mindkét metrika és napló exportálható Azure Monitorba, pontosabban a [Azure monitor naplózó](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) összetevőt, és a [naplózási lekérdezésekkel](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata)programozott módon férhet hozzá. Az SQL Analytics naplózási késése körülbelül 10-15 percet vesz igénybe. A késést befolyásoló tényezőkkel kapcsolatos további információkért látogasson el a következő dokumentációra.


## <a name="next-steps"></a>Következő lépések
A következő útmutatók ismertetik a gyakori forgatókönyveket és a használati eseteket az adattárház monitorozása és kezelése során:

- [Az adatraktár munkaterhelésének figyelése a DMV](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)
