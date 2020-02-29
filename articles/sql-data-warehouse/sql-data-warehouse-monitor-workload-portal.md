---
title: Munkaterhelés figyelése – Azure Portal
description: Az SQL Analytics figyelése a Azure Portal használatával
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 7e93aee405d8a66d850a4e3f07f2e788f1004ef8
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197231"
---
# <a name="monitor-workload---azure-portal"></a>Munkaterhelés figyelése – Azure Portal

Ez a cikk azt ismerteti, hogyan használható a Azure Portal a számítási feladatok figyelésére. Ebbe beletartozik Azure Monitor naplók beállítása a lekérdezés-végrehajtás és a számítási feladatok trendjeinek vizsgálatához az [SQL Analytics](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/)log Analytics használatával.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .
- SQL-készlet: a rendszer egy SQL-készlet naplóit gyűjti. Ha nem rendelkezik kiépített SQL-készlettel, tekintse meg az [SQL-készlet létrehozása](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial)című témakör utasításait.

## <a name="create-a-log-analytics-workspace"></a>Log Analytics munkaterület létrehozása

Navigáljon Log Analytics munkaterületek Tallózás paneljére, és hozzon létre egy munkaterületet 

![Log Analytics-munkaterületek](media/sql-data-warehouse-monitor/log_analytics_workspaces.png)

![Elemzési munkaterület hozzáadása](media/sql-data-warehouse-monitor/add_analytics_workspace.png)

![Elemzési munkaterület hozzáadása](media/sql-data-warehouse-monitor/add_analytics_workspace_2.png)

A munkaterületekkel kapcsolatos további információkért tekintse meg az alábbi [dokumentációt](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace#create-a-workspace).

## <a name="turn-on-diagnostic-logs"></a>Diagnosztikai naplók bekapcsolása

A diagnosztikai beállítások konfigurálása az SQL-készletből származó naplók kibocsátása céljából. A naplók a leggyakrabban használt teljesítmény-hibaelhárítási DMV egyenértékű telemetria-nézetekből állnak. Jelenleg a következő nézetek támogatottak:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![Diagnosztikai naplók engedélyezése](media/sql-data-warehouse-monitor/enable_diagnostic_logs.png)

A naplók az Azure Storage, Stream Analytics vagy Log Analytics számára is kiállíthatók. Ebben az oktatóanyagban válassza a Log Analytics lehetőséget.

![Naplók meghatározása](media/sql-data-warehouse-monitor/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Lekérdezések futtatása Log Analytics

Navigáljon a Log Analytics munkaterületre, ahol a következőket teheti:

- Naplók elemzése a naplófájlok használatával és a lekérdezések ismételt újbóli lekérdezése
- Lekérdezések mentése ismételt használatra
- Naplóriasztások létrehozása
- Lekérdezési eredmények rögzítése egy irányítópulton

A naplózott lekérdezések képességeiről a következő [dokumentációban](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)olvashat bővebben.

![Log Analytics munkaterület-szerkesztő](media/sql-data-warehouse-monitor/log_analytics_workspace_editor.png)



![Log Analytics munkaterület-lekérdezések](media/sql-data-warehouse-monitor/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Példák a naplók lekérdezésére



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

Most, hogy beállította és konfigurálta az Azure monitor-naplókat, [testreszabhatja az Azure-irányítópultokat](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) a csapaton belüli megosztáshoz.
