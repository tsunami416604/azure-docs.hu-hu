---
title: Munkaterhelés figyelése – Azure portal
description: A Synapse SQL figyelése az Azure Portalon
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 64e61b00ecebec82b465cb13c6df0e323f6c7777
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586555"
---
# <a name="monitor-workload---azure-portal"></a>Munkaterhelés figyelése – Azure portal

Ez a cikk ismerteti, hogyan használhatja az Azure Portalon a számítási feladatok figyelése. Ez magában foglalja az Azure Monitor Naplók beállítását a lekérdezések végrehajtásának és a számítási feladatok tendenciáinak vizsgálatára a [Synapse SQL](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/)naplóelemzési használatával.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.
- SQL-készlet: Egy SQL-készlet naplóit gyűjtjük. Ha nincs kiépítve SQL-készlete, olvassa el az [SQL-készlet létrehozása](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial)című témakör utasításait.

## <a name="create-a-log-analytics-workspace"></a>A Log Analytics-munkaterület létrehozása

Keresse meg a Log Analytics-munkaterületek tallózási paneljét, és hozzon létre egy munkaterületet 

![Log Analytics-munkaterületek](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![Analytics-munkaterület hozzáadása](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![Analytics-munkaterület hozzáadása](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

A munkaterületekkel kapcsolatos további részletekért látogasson el az alábbi [dokumentációba.](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace#create-a-workspace)

## <a name="turn-on-diagnostic-logs"></a>Diagnosztikai naplók bekapcsolása

Konfigurálja a diagnosztikai beállításokat az SQL-készletből származó naplók kitöltéséhez. A naplók telemetriai nézetekből állnak, amelyek egyenértékűek a leggyakrabban használt teljesítményhibaelhárítási dmv-kkel. Jelenleg a következő nézetek támogatottak:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![Diagnosztikai naplók engedélyezése](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

A naplók kiadhatók az Azure Storage, a Stream Analytics vagy a Log Analytics számára. Ebben az oktatóanyagban válassza a Log Analytics lehetőséget.

![Naplók megadása](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Lekérdezések futtatása a Log Analytics szolgáltatásban

Nyissa meg a Log Analytics-munkaterületet, ahol a következőket teheti:

- Naplók elemzése naplólekérdezésekkel és lekérdezések mentése újrafelhasználásra
- Lekérdezések mentése újrafelhasználásra
- Naplóriasztások létrehozása
- Lekérdezési eredmények rögzítése irányítópulton

A naplólekérdezések képességeiről az alábbi [dokumentációban](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)tájékform.

![Log Analytics munkaterület-szerkesztő](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)



![Log Analytics-munkaterületi lekérdezések](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Mintanapló-lekérdezések



```Kusto
//List all queries 
AzureDiagnostics
| where Category contains "ExecRequests"
| project TimeGenerated, StartTime_t, EndTime_t, Status_s, Command_s, ResourceClass_s, duration=datetime_diff('millisecond',EndTime_t, StartTime_t)
```
```Kusto
//Chart the most active resource classes
AzureDiagnostics
| where Category contains "ExecRequests"
| where Status_s == "Completed"
| summarize totalQueries = dcount(RequestId_s) by ResourceClass_s
| render barchart 
```
```Kusto
//Count of all queued queries
AzureDiagnostics
| where Category contains "waits" 
| where Type_s == "UserConcurrencyResourceType"
| summarize totalQueuedQueries = dcount(RequestId_s)
```
## <a name="next-steps"></a>További lépések

Most, hogy beállította és konfigurálta az Azure-figyelőnaplókat, [testreszabhatja az Azure-irányítópultokat,](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) hogy megosszák a csapaton keresztül.
