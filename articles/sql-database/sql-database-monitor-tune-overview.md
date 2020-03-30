---
title: Monitorozás és teljesítmény-finomhangolás
description: Az Azure SQL Database figyelési és teljesítményhangolási képességeinek és módszertanának áttekintése.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 837d88665c1fdffe902c9c478e5d6dc65a2e402a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268729"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database"></a>Figyelés és teljesítményhangolás az Azure SQL Database-ben

Egy adatbázis teljesítményének figyeléséhez az Azure SQL Database-ben, először figyelze meg a számítási feladatok által használt PROCESSZOR és Io-erőforrások, az adott szolgáltatási szint és teljesítményszint kiválasztásánál választott adatbázis-teljesítmény szintjéhez viszonyítva. Ehhez az Azure SQL Database olyan erőforrás-metrikákat bocsát ki, amelyek megtekinthetők az Azure Portalon vagy az alábbi SQL felügyeleti eszközök: [Az Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) vagy az SQL Server Management [Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) használatával.

Az egy- és készletalapú adatbázisok esetében az Azure SQL Database számos adatbázis-tanácsadót biztosít, amelyek intelligens teljesítményhangolási javaslatokat és automatikus hangolási lehetőségeket biztosítanak a teljesítmény javítása érdekében. Emellett a Query Performance Insight részletesen ismerteti az egy- és készletezésű adatbázisok processzor- és I/O-használatáért felelős lekérdezéseket.

Az Azure SQL Database további figyelési és hangolási funkciókat biztosít a mesterséges intelligencia segítségével, hogy segítsen az adatbázisok és megoldások hibaelhárításában és teljesítményének maximalizálásában. Beállíthatja az [Intelligent Insights](sql-database-intelligent-insights.md) és más SQL Database erőforrásnaplók és metrikák [streamelési exportálását](sql-database-metrics-diag-logging.md) a felhasználás és elemzés számos cél egyikébe, különösen az [SQL Analytics](../azure-monitor/insights/azure-sql.md)használatával). Az Azure SQL Analytics egy fejlett felhőfigyelő megoldás az összes Azure SQL-adatbázis teljesítményének figyelésére, egyetlen nézetben, több előfizetésben. Az exportálható naplók és metrikák listáját az [exportálás diagnosztikai telemetriai adatai](sql-database-metrics-diag-logging.md#diagnostic-telemetry-for-export-for-azure-sql-database)

Végül az SQL saját figyelési és diagnosztikai képességekkel rendelkezik [az SQL Server Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) és a dinamikus felügyeleti nézetek [(DMV-k)](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)segítségével. Lásd: [DMV-k használatával](sql-database-monitoring-with-dmvs.md) a parancsfájlok figyelése a különböző teljesítményproblémák.

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Figyelési és hangolási lehetőségek az Azure Portalon

Az Azure Portalon az Azure SQL Database az összes üzembe helyezési típus erőforrás-metrikáinak figyelését biztosítja. Emellett az egy- és készletezésű adatbázisokhoz az adatbázis-tanácsadók és a Lekérdezési teljesítményinsight lekérdezéshangolási javaslatokat és lekérdezési teljesítményelemzést is biztosítanak. Végül az Azure Portalon engedélyezheti az automatikus logikai kiszolgálók és azok egy- és készletezésű adatbázisok.

### <a name="sql-database-resource-monitoring"></a>SQL-adatbázis erőforrás-figyelése

A **metrikák** nézetben gyorsan figyelheti a következő erőforrás-metrikákat az Azure Portalon:

- **DTU-használat**

  Ellenőrizze, hogy egy adatbázis vagy rugalmas készlet hosszabb ideig eléri-e a DTU-használat 100 százalékát. A magas DTU-használat azt jelzi, hogy a számítási feladatoktöbb PROCESSZOR- vagy I/O-erőforrást igényelnek. Azt is jelezheti, hogy a lekérdezéseket optimalizálni kell.
- **Processzorhasználat**

  Ellenőrizze, hogy egy adatbázis, rugalmas készlet vagy felügyelt példány hosszabb ideig eléri-e a PROCESSZOR-használat 100 százalékát. A magas processzor azt jelzi, hogy a számítási feladatoknak több PROCESSZOR- vagy I/O-erőforrásra lehet szükségük. Azt is jelezheti, hogy a lekérdezéseket optimalizálni kell.
- **Io-használat**

  Ellenőrizze, hogy egy adatbázis, rugalmas készlet vagy manage' példány eléri-e az alapul szolgáló tároló i/o-korlátait. A magas i/o-használat azt jelzi, hogy a számítási feladatoknak több PROCESSZOR- vagy I/O-erőforrásra lehet szükségük. Azt is jelezheti, hogy a lekérdezéseket optimalizálni kell.

  ![Erőforrás-mutatók](./media/sql-database-monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors"></a>Adatbázis-tanácsadók
' Az Azure SQL Database [adatbázis-tanácsadókat](sql-database-advisor.md) tartalmaz, amelyek teljesítményhangolási javaslatokat nyújtanak az egy- és készletalapú adatbázisokhoz. Ezek a javaslatok az Azure Portalon és a [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseadvisor)használatával érhetők el. Az automatikus [hangolást](sql-database-automatic-tuning.md) is engedélyezheti, hogy az SQL Database automatikusan megvalósíthassa ezeket a finomhangolási javaslatokat.

### <a name="query-performance-insight"></a>Lekérdezési terheléselemző

[A Query Performance Insight](sql-database-query-performance.md) az Azure Portalon a legnépszerűbb fogyasztó és leghosszabb ideig futó lekérdezések teljesítményét jeleníti meg az egy- és készletalapú adatbázisokhoz.

## <a name="generate-intelligent-assessments-of-performance-issues"></a>A teljesítményproblémák intelligens felmérésének létrehozása

Az Azure SQL Database [Intelligent Insights](sql-database-intelligent-insights.md) beépített intelligenciával folyamatosan figyeli az adatbázis-használatot mesterséges intelligencián keresztül, és észleli a gyenge teljesítményt okozó zavaró eseményeket. Az Intelligent Insights automatikusan észleli az Azure SQL Database adatbázisaival kapcsolatos teljesítményproblémákat a lekérdezés-végrehajtási várakozási idők, hibák vagy időtúlkérdések alapján. Az észlelést követően részletes elemzés készül, amely létrehoz egy erőforrásnaplót (SQLInsights) [a problémák intelligens felmérésével.](sql-database-intelligent-insights-troubleshoot-performance.md) Ez az értékelés az adatbázis teljesítményével kapcsolatos probléma kiváltó okelemzéséből és lehetőség szerint a teljesítmény javítására vonatkozó javaslatokból áll.

Az Intelligent Insights az Azure beépített intelligenciájának egyedülálló képessége, amely a következő értéket nyújtja:

- Proaktív figyelés
- Személyre szabott teljesítményelemzés
- Az adatbázis teljesítményének romlásának korai felismerése
- Az észlelt problémák kiváltó okainak elemzése
- Teljesítményjavítási javaslatok
- Több százezer adatbázis kiskálázási képessége
- Pozitív hatás a DevOps-erőforrásokra és a teljes tulajdonlási költségre

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>Metrikák és erőforrásnaplók streamelési exportálásának engedélyezése

Engedélyezheti és konfigurálhatja a [diagnosztikai telemetriai adatok streamelési exportálását](sql-database-metrics-diag-logging.md) a számos cél egyikébe, beleértve az Intelligent Insights erőforrásnaplót is. Az [SQL Analytics](../azure-monitor/insights/azure-sql.md) és más képességek segítségével használja ezt a további diagnosztikai telemetriai adatokat a teljesítményproblémák azonosításához és megoldásához.

Diagnosztikai beállításokat az egyes adatbázisok, készletes adatbázisok, rugalmas készletek, felügyelt példányok és példányadatbázisok metrikák és erőforrásnaplók kategóriáinak streamelésére az alábbi Azure-erőforrások egyikére.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Log Analytics-munkaterület az Azure-figyelőben

Metrikákat és erőforrásnaplókat streamelhet egy [Log Analytics-munkaterületre az Azure Monitorban.](../azure-monitor/platform/resource-logs-collect-workspace.md) Az itt streamelt adatokat az [SQL Analytics](../azure-monitor/insights/azure-sql.md)is felhasználhatja, amely egy csak felhőalapú figyelési megoldás, amely az adatbázisok intelligens figyelését biztosítja, amely teljesítményjelentéseket, riasztásokat és kockázatcsökkentési javaslatokat tartalmaz. A Log Analytics-munkaterületre streamelt adatok elemezhetők más összegyűjtött figyelési adatokkal, és lehetővé teszik más Azure Monitor-funkciók, például riasztások és vizualizációk kihasználását is.

### <a name="azure-event-hubs"></a>Azure Event Hubs

Metrikákat és erőforrásnaplókat továbbíthat az [Azure Event Hubs szolgáltatásba.](../azure-monitor/platform/resource-logs-stream-event-hubs.md) Streamelési diagnosztikai telemetria az eseményközpontokba a következő funkciók biztosítása érdekében:

- **Naplóstreamelési adatok külső fakitermelési és telemetriai rendszerekhez**

  Továbbítsa az összes metrikáját és erőforrásnaplóját egyetlen eseményközpontba, hogy a naplóadatokat egy külső Gyártósorba vagy naplóelemző eszközre továbbítsa.
- **Egyéni telemetriai és naplózási platform létrehozása**

  A nagy mértékben méretezhető közzététel-előfizetés jellege eseményközpontok lehetővé teszi, hogy rugalmasan metrikák és erőforrás-naplók egy egyéni telemetriai platformra. A részletekért tekintse meg [a globális méretezési telemetriai platform tervezése és méretezése az Azure Event Hubs-on.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)
- **A szolgáltatás állapotának megtekintése adatok power BI-ba való streamelésével**

  Az Event Hubs, a Stream Analytics és a Power BI segítségével a diagnosztikai adatokat közel valós idejű elemzési adatokká alakíthatja az Azure-szolgáltatásokban. Lásd: Stream Analytics és Power BI: A megoldás részleteiért tekintse meg a Stream Analytics és a Power BI: A megoldással kapcsolatos részletekért tekintse meg a [valós idejű elemzési irányítópultot.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard)

### <a name="azure-storage"></a>Azure Storage

Metrikák és erőforrásnaplók streamelése az [Azure Storage-ba.](../azure-monitor/platform/resource-logs-collect-storage.md) Az Azure storage használatával archiválhatja a nagy mennyiségű diagnosztikai telemetriai adatok az előző két streamelési lehetőség költségének töredékéért.

## <a name="use-extended-events-in-the-sql-database-engine"></a>Kiterjesztett események használata az SQL adatbázismotorban

Emellett [az SQL-ben kiterjesztett eseményeket](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) is használhat további speciális figyelési és hibaelhárítási célokra. A kiterjesztett események architektúrája lehetővé teszi a felhasználók számára, hogy annyi vagy legkevesebb adatot gyűjtsenek, mint amennyi a teljesítményproblémák elhárításához vagy azonosításához szükséges. A kiterjesztett események SQL Database-ben való használatáról a Kiterjesztett események az SQL Database alkalmazásban című [témakörben](sql-database-xevent-db-diff-from-svr.md)talál további információt.

## <a name="next-steps"></a>További lépések

- Az egy- és készletezésű adatbázisokintelligens teljesítményre vonatkozó javaslatairól az [Adatbázis-tanácsadó teljesítményre vonatkozó javaslatai című témakörben talál](sql-database-advisor.md)további információt.
- Az adatbázisok teljesítményének automatikus diagnosztikával és a teljesítményproblémák kiváltó okainak elemzésével kapcsolatos további tudnivalókért tekintse meg az Azure SQL Intelligent Insights című [témakört.](sql-database-intelligent-insights.md)
'''''''''