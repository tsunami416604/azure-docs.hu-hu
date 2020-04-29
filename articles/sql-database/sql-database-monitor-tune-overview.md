---
title: Monitorozás és teljesítmény-finomhangolás
description: A monitorozási és teljesítmény-hangolási képességek és módszertan áttekintése Azure SQL Databaseban.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79268729"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database"></a>A monitorozás és a teljesítmény finomhangolása Azure SQL Database

Azure SQL Database-adatbázis teljesítményének figyeléséhez először figyelje a számítási feladatok által használt CPU-és IO-erőforrásokat az adott szolgáltatási szint és teljesítmény szintjének kiválasztása során kiválasztott adatbázis-teljesítmény szintjéhez képest. Ennek elvégzéséhez Azure SQL Database olyan erőforrás-metrikákat bocsát ki, amelyek megtekinthetők a Azure Portal vagy az alábbi SQL Management-eszközök valamelyikével: [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) vagy [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

Az önálló és a készletezett adatbázisok esetében a Azure SQL Database számos adatbázis-tanácsadót biztosít az intelligens teljesítmény-hangolási javaslatok és az Automatikus hangolási beállítások biztosításához a teljesítmény javítása érdekében. Emellett Lekérdezési terheléselemző megjeleníti a legtöbb CPU-és IO-használatért felelős lekérdezések részleteit az önálló és a készletezett adatbázisok esetében.

A Azure SQL Database a mesterséges intelligencia által támogatott további monitorozási és hangolási képességeket biztosít az adatbázisok és megoldások teljesítményének hibaelhárításához és maximalizálásához. Dönthet úgy is, hogy a [Intelligent Insights](sql-database-intelligent-insights.md) és más SQL Database erőforrás-naplók és metrikák [adatfolyam-exportálását](sql-database-metrics-diag-logging.md) több, a felhasználás és az elemzés céljára szolgáló célhelyre konfigurálja, különösen az [SQL Analytics](../azure-monitor/insights/azure-sql.md)használatával). A Azure SQL Analytics egy fejlett felhőalapú figyelési megoldás, amely az összes Azure SQL Database-adatbázis teljesítményét és egyetlen nézetben több előfizetést is figyel. Az exportálható naplók és metrikák listáját az [Exportálás diagnosztikai telemetria](sql-database-metrics-diag-logging.md#diagnostic-telemetry-for-export-for-azure-sql-database) című témakörben tekintheti meg.

Végül az SQL saját figyelési és diagnosztikai képességekkel rendelkezik a [SQL Server Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) -val és a [dinamikus felügyeleti nézetekkel (DMV)](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views). A különböző teljesítménnyel kapcsolatos problémák figyeléséhez tekintse meg a [figyelés DMV használatával](sql-database-monitoring-with-dmvs.md) szkripteket.

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>A Azure Portal funkcióinak figyelése és finomhangolása

A Azure Portal Azure SQL Database az összes központi telepítési típus erőforrás-metrikáinak figyelését. Az önálló és a készletezett adatbázisok esetében az adatbázis-tanácsadók és a Lekérdezési terheléselemző biztosítanak lekérdezés-hangolási javaslatokat és a lekérdezési teljesítmény elemzését. Végül a Azure Portalban engedélyezheti a logikai kiszolgálók és az önálló és a készletezett adatbázisok automatikus alkalmazását.

### <a name="sql-database-resource-monitoring"></a>Erőforrás-figyelés SQL Database

A **metrikák** nézetben gyorsan figyelheti a Azure Portal következő erőforrás-metrikáit:

- **DTU-használat**

  Ellenőrizze, hogy egy adatbázis vagy rugalmas készlet elérte-e a DTU-használat 100%-át hosszabb ideig. A magas DTU-használat azt jelzi, hogy a számítási feladathoz több CPU-vagy IO-erőforrásra lehet szükség. Azt is jelezheti, hogy milyen lekérdezéseket kell optimalizálni.
- **Processzorhasználat**

  Ellenőrizze, hogy az adatbázis, a rugalmas készlet vagy a felügyelt példány eléri-e a CPU-használat 100%-át hosszabb ideig. A magas CPU azt jelzi, hogy a számítási feladathoz több CPU-vagy IO-erőforrásra lehet szükség. Azt is jelezheti, hogy milyen lekérdezéseket kell optimalizálni.
- **I/o-használat**

  Ellenőrizze, hogy az adatbázis, a rugalmas készlet vagy a kezelés példánya eléri-e a mögöttes tároló IO-korlátait. A magas IO-használat azt jelzi, hogy a számítási feladathoz több CPU-vagy IO-erőforrásra lehet szükség. Azt is jelezheti, hogy milyen lekérdezéseket kell optimalizálni.

  ![Erőforrás-metrikák](./media/sql-database-monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors"></a>Adatbázis-tanácsadók
"Azure SQL Database olyan [adatbázis-tanácsadókat](sql-database-advisor.md) tartalmaz, amelyek teljesítmény-hangolási javaslatokat biztosítanak az önálló és a készletezett adatbázisokhoz. Ezek a javaslatok a Azure Portal és a [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseadvisor)használatával is elérhetők. Az [automatikus hangolást](sql-database-automatic-tuning.md) is engedélyezheti, hogy a SQL Database automatikusan implementálja ezeket a hangolási javaslatokat.

### <a name="query-performance-insight"></a>Lekérdezési terheléselemző

A [lekérdezési terheléselemző](sql-database-query-performance.md) megjeleníti a teljesítményt a leggyakrabban használt és a leghosszabb ideig futó lekérdezések Azure Portalában az önálló és a készletezett adatbázisok esetében.

## <a name="generate-intelligent-assessments-of-performance-issues"></a>Teljesítménybeli problémák intelligens értékelésének előállítása

A Azure SQL Database [Intelligent Insights](sql-database-intelligent-insights.md) a beépített intelligenciával folyamatosan figyeli az adatbázis-használatot mesterséges intelligenciával, és felderíti a gyenge teljesítményt okozó zavaró eseményeket. A Intelligent Insights automatikusan észleli a Azure SQL Database-adatbázisokkal kapcsolatos teljesítményproblémák a lekérdezés végrehajtásának várakozási ideje, hibái vagy időtúllépése alapján. Az észlelést követően a rendszer részletes elemzést végez, amely egy erőforrás-naplót (SQLInsights) hoz létre a [problémák intelligens értékelésével](sql-database-intelligent-insights-troubleshoot-performance.md). Ez az értékelés az adatbázis teljesítményével kapcsolatos probléma okának elemzését, és ahol lehetséges, a teljesítménnyel kapcsolatos javításokra vonatkozó javaslatokat tartalmaz.

A Intelligent Insights az Azure beépített intelligenciának egyedülálló funkciója, amely a következő értéket biztosítja:

- Proaktív figyelés
- Testreszabott teljesítmény-felismerés
- Az adatbázis-teljesítmény romlásának korai észlelése
- Az észlelt problémák kiváltó okának elemzése
- Teljesítmény-javító javaslatok
- Több százezer adatbázisra kibővíthető képesség
- Pozitív hatással van a DevOps-erőforrásokra és a teljes tulajdonlási díjakra

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>Metrikák és erőforrás-naplók adatfolyam-exportálásának engedélyezése

Engedélyezheti és konfigurálhatja a [diagnosztikai telemetria adatfolyam-exportálását](sql-database-metrics-diag-logging.md) több célhelyre, beleértve a Intelligent Insights erőforrás-naplót is. Az [SQL Analytics](../azure-monitor/insights/azure-sql.md) és egyéb funkciók használatával felhasználhatja ezt a további diagnosztikai telemetria a teljesítményproblémák azonosításához és megoldásához.

A diagnosztikai beállításokat a mérőszámok és az erőforrás-naplók a következő Azure-erőforrások egyikére történő továbbításához kell konfigurálni: önálló adatbázisok, készletezett adatbázisok, rugalmas készletek, felügyelt példányok és példány-adatbázisok.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Log Analytics munkaterület az Azure monitorban

A metrikák és az erőforrás-naplók a [Azure Monitor log Analytics munkaterületére](../azure-monitor/platform/resource-logs-collect-workspace.md)is továbbíthatók. Az itt továbbított adatfolyamokat az [SQL Analytics](../azure-monitor/insights/azure-sql.md)is felhasználhatja, ami egy kizárólag Felhőbeli figyelési megoldás, amely a teljesítményadatokat, a riasztásokat és a kockázatcsökkentő ajánlásokat tartalmazó adatbázisok intelligens figyelését teszi lehetővé. A Log Analytics munkaterületre továbbított adatok elemezhetők más figyelési adatokkal, és lehetővé teszik más Azure Monitor funkciók, például a riasztások és a vizualizációk kihasználása is.

### <a name="azure-event-hubs"></a>Azure Event Hubs

A metrikákat és az erőforrás-naplókat továbbíthatja az [Azure Event Hubsba](../azure-monitor/platform/resource-logs-stream-event-hubs.md). Adatfolyam-diagnosztikai telemetria az Event hubok számára a következő funkciók biztosításához:

- **Stream-naplók harmadik féltől származó naplózási és telemetria rendszerekhez**

  Továbbítsa az összes mérőszámot és erőforrás-naplót egy adott esemény központba egy harmadik féltől származó SIEM-vagy log Analytics-eszközre.
- **Egyéni telemetria és naplózási platform létrehozása**

  Az Event hubok nagymértékben méretezhető közzétételi-előfizetési természete lehetővé teszi a metrikák és erőforrás-naplók rugalmas betöltését egy egyéni telemetria platformra. A részletekért lásd: [globális méretű telemetria platform tervezése és méretezése az Azure Event Hubsban](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) .
- **A szolgáltatás állapotának megtekintése az adattovábbítási Power BI**

  Event Hubs, Stream Analytics és Power BI használatával alakítsa át diagnosztikai adatait az Azure-szolgáltatások közel valós idejű elemzéséhez. A megoldás részleteiért tekintse meg a [stream Analytics és Power bi: valós idejű elemzési irányítópultot a folyamatos adattovábbításhoz](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard) .

### <a name="azure-storage"></a>Azure Storage

Stream-metrikák és erőforrás-naplók az [Azure Storage](../azure-monitor/platform/resource-logs-collect-storage.md)-ba. Az Azure Storage használatával nagy mennyiségű diagnosztikai telemetria archiválható az előző két folyamatos átviteli lehetőség díjainak töredékéért.

## <a name="use-extended-events-in-the-sql-database-engine"></a>Kiterjesztett események használata az SQL Database Engine-ben

Emellett az SQL-ben [kiterjesztett eseményeket](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) is használhat további speciális figyeléshez és hibaelhárításhoz. A kiterjesztett események architektúrája lehetővé teszi a felhasználók számára, hogy a teljesítménnyel kapcsolatos problémák elhárításához vagy azonosításához szükséges mennyiségű vagy kevés adatot gyűjtsenek. További információ a SQL Database kiterjesztett eseményeinek használatáról: [kiterjesztett események a SQL Databaseban](sql-database-xevent-db-diff-from-svr.md).

## <a name="next-steps"></a>További lépések

- Az önálló és a készletezett adatbázisokra vonatkozó intelligens teljesítménnyel kapcsolatos javaslatokról az [adatbázis-tanácsadó teljesítményével kapcsolatos javaslatok](sql-database-advisor.md)című témakörben olvashat bővebben.
- Az adatbázis teljesítményének automatikus és a teljesítménnyel kapcsolatos problémák kiváltó okának elemzésével kapcsolatos további információkért lásd: [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md).
'''''''''