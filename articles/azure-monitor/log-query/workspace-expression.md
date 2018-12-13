---
title: az Azure Log Analytics lekérdezési Workspace() kifejezés |} A Microsoft Docs
description: A munkaterület kifejezés szolgál a Log Analytics-lekérdezés-adatokat lekérni egy adott munkaterület ugyanazt az erőforráscsoportot, egy másik erőforráscsoportot vagy egy másik előfizetést.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/10/2018
ms.author: bwren
ms.openlocfilehash: 24a737a728b0a249fda76cbff481bea284ac24aa
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53182944"
---
# <a name="workspace-expression-in-log-analytics-query"></a>a Log Analytics-lekérdezés Workspace() kifejezés

A `workspace` kifejezés szolgál a Log Analytics-lekérdezés adatokat lekérni egy adott munkaterület ugyanazt az erőforráscsoportot, egy másik erőforráscsoportot vagy egy másik előfizetést. Ez akkor hasznos, foglalja több munkaterületről hajthat végre, a napló lekérdezésben Teljesítménynapló-adatok az Application Insights-lekérdezés, valamint a használatával adatokat lekérdezni.


## <a name="syntax"></a>Szintaxis

`workspace(`*azonosító*`)`

## <a name="arguments"></a>Argumentumok

- *Azonosító*: A munkaterület az alábbi táblázat a formátumok valamelyikével azonosítja.

| Azonosító | Leírás | Példa
|:---|:---|:---|
| Erőforrás neve | A munkaterület (AVAGY "összetevő neve") emberi olvasható neve | Workspace("contosoretail") |
| Minősített név | A munkaterület az űrlap a teljes név: "subscriptionName/resourceGroup/componentName" | Workspace('Contoso/ContosoResource/ContosoWorkspace') |
| ID (Azonosító) | A munkaterület GUID | Workspace("b438b3f6-912a-46d5-9db1-b42069242ab4") |
| Azure-erőforrás azonosítója | Az Azure-erőforrás azonosítója | Workspace("/Subscriptions/e4227-645-44e-9c67-3b84b5982/resourcegroups/ContosoAzureHQ/Providers/Microsoft.OperationalInsights/Workspaces/contosoretail") |


## <a name="notes"></a>Megjegyzések

* Olvasási hozzáféréssel kell rendelkeznie a munkaterületre.
* Egy kapcsolódó kifejezés `app` , amely lehetővé teszi, hogy az Application Insights-alkalmazások lekérdezésére.

## <a name="examples"></a>Példák

```Kusto
workspace("contosoretail").Update | count
```
```Kusto
workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count
```
```Kusto
workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
```
```Kusto
union 
(workspace("myworkspace").Heartbeat | where Computer contains "Con"),
(app("myapplication").requests | where cloud_RoleInstance contains "Con")
| count  
```
```Kusto
union 
(workspace("myworkspace").Heartbeat), (app("myapplication").requests)
| where TimeGenerated between(todatetime("2018-02-08 15:00:00") .. todatetime("2018-12-08 15:05:00"))
```

## <a name="next-steps"></a>További lépések

- Tekintse meg a [alkalmazás kifejezés](workspace-expression.md) , tekintse meg az Application Insights alkalmazást.
- Megtudhatja, hogyan lehet [Log Analytics-adatok](../../azure-monitor/log-query/log-query-overview.md) tárolja.