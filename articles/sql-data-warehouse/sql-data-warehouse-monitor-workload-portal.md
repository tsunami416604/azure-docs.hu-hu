---
title: A figyelő számítási feladat feldolgozása – Azure portal |} A Microsoft Docs
description: A figyelő az Azure SQL Data Warehouse az Azure portal használatával
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 6c8ce090039e3d5cc85c86d920710294de2165f9
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58405782"
---
# <a name="monitor-workload---azure-portal"></a>A figyelő számítási feladat feldolgozása – Azure portal

Ez a cikk ismerteti a számítási feladat monitorozása az Azure portal használatával. Ez magában foglalja az Azure Monitor naplóira vizsgálja meg a lekérdezés végrehajtása és a számítási feladatok trendeket a log analytics használatával való beállításával [Azure SQL Data Warehouse](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
- Azure SQL Data Warehouse: Fogja azt gyűjtése naplók egy SQL data warehouse-hoz. Ha egy SQL data warehouse kiépítése nem rendelkezik, tekintse meg a következő témakör utasításait [hozzon létre egy SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial).

## <a name="create-a-log-analytics-workspace"></a>Log Analytics-munkaterület létrehozása

Keresse meg a Log Analytics-munkaterületek a – Tallózás panel és a egy munkaterület létrehozása 

![Log Analytics-munkaterületek](media/sql-data-warehouse-monitor/log_analytics_workspaces.png)

![Analytics-munkaterület hozzáadása](media/sql-data-warehouse-monitor/add_analytics_workspace.png)

![Analytics-munkaterület hozzáadása](media/sql-data-warehouse-monitor/add_analytics_workspace_2.png)

A munkaterületek további információért látogasson el a következő [dokumentáció](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#create-a-workspace).

## <a name="turn-on-diagnostic-logs"></a>Kapcsolja be a diagnosztikai naplók 

Hozzon létre naplókat az SQL data warehouse-ból a diagnosztikai beállítások konfigurálása. Naplók az egyenértékű a leggyakrabban használt teljesítménnyel kapcsolatos hibaelhárítás a DMV-vel az SQL Data Warehouse adattárházzal telemetriai nézetek állnak. Jelenleg a következő nézet használható:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![Diagnosztikai naplók engedélyezése](media/sql-data-warehouse-monitor/enable_diagnostic_logs.png)

Naplók is az Azure Storage, a Stream Analytics és a Log Analytics bocsátja ki. A jelen oktatóanyag esetében válassza ki a Log Analytics.

![Adja meg a naplók](media/sql-data-warehouse-monitor/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>A Log Analytics-lekérdezések futtatásához

Keresse meg a Log Analytics-munkaterületet, ahol a következőket teheti:

- Napló lekérdezésekkel-naplók elemzése és ismételt használata lekérdezések mentése
- Mentheti a lekérdezéseket újrafelhasználása
- Riasztások létrehozása
- PIN-kód lekérdezés eredményeit az irányítópulton

A naplólekérdezések képességeit a részletekért látogasson el a következő [dokumentáció](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

![Log Analytics-munkaterület szerkesztő](media/sql-data-warehouse-monitor/log_analytics_workspace_editor.png)



![Log Analytics-munkaterület lekérdezések](media/sql-data-warehouse-monitor/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Mintalekérdezések napló



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

Most, hogy állítsa be és konfigurálta az Azure monitor naplóira, [testreszabása az Azure-irányítópultok](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) , megoszthatja munkatársaival.
