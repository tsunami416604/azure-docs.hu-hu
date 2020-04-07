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
ms.openlocfilehash: 0658a775e40c1fc433c7c2e1d853493544e74ee4
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743217"
---
# <a name="monitor-workload---azure-portal"></a>Munkaterhelés figyelése – Azure portal

Ez a cikk ismerteti, hogyan használhatja az Azure Portalon a számítási feladatok figyelése. Ez magában foglalja az Azure Monitor Naplók beállítását a lekérdezések végrehajtásának és a számítási feladatok tendenciáinak vizsgálatára a [Synapse SQL](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/)naplóelemzési használatával.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.
- SQL-készlet: Egy SQL-készlet naplóit gyűjtjük. Ha nincs kiépítve SQL-készlete, olvassa el az [SQL-készlet létrehozása](load-data-from-azure-blob-storage-using-polybase.md)című témakör utasításait.

## <a name="create-a-log-analytics-workspace"></a>A Log Analytics-munkaterület létrehozása

Keresse meg a Log Analytics-munkaterületek tallózási paneljét, és hozzon létre egy munkaterületet

![Log Analytics-munkaterületek](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![Analytics-munkaterület hozzáadása](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![Analytics-munkaterület hozzáadása](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

A munkaterületekkel kapcsolatos további részletekért látogasson el az alábbi [dokumentációba.](../../azure-monitor/learn/quick-create-workspace.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsond#create-a-workspace)

## <a name="turn-on-diagnostic-logs"></a>Diagnosztikai naplók bekapcsolása

Konfigurálja a diagnosztikai beállításokat az SQL-készletből származó naplók kitöltéséhez. A naplók telemetriai nézetekből állnak, amelyek egyenértékűek a leggyakrabban használt teljesítményhibaelhárítási dmv-kkel. Jelenleg a következő nézetek támogatottak:

- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

![Diagnosztikai naplók engedélyezése](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

A naplók kiadhatók az Azure Storage, a Stream Analytics vagy a Log Analytics számára. Ebben az oktatóanyagban válassza a Log Analytics lehetőséget.

![Naplók megadása](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Lekérdezések futtatása a Log Analytics szolgáltatásban

Nyissa meg a Log Analytics-munkaterületet, ahol a következőket teheti:

- Naplók elemzése naplólekérdezésekkel és lekérdezések mentése újrafelhasználásra
- Lekérdezések mentése újrafelhasználásra
- Naplóriasztások létrehozása
- Lekérdezési eredmények rögzítése irányítópulton

A naplólekérdezések képességeiről az alábbi [dokumentációban](../../azure-monitor/log-query/query-language.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)tájékform.

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

Most, hogy beállította és konfigurálta az Azure-figyelőnaplókat, [testreszabhatja az Azure-irányítópultokat,](../../azure-portal/azure-portal-dashboards.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) hogy megosszák a csapaton keresztül.
