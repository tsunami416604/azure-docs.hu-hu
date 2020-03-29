---
title: munkaterület() kifejezés az Azure Monitor naplólekérdezésében | Microsoft dokumentumok
description: A munkaterület-kifejezés egy Azure Monitor naplólekérdezésben használatos az adatok lekéréséhez egy adott munkaterületugyanabban az erőforráscsoportban, egy másik erőforráscsoportban vagy egy másik előfizetésben.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 255888acf5da6149b6a964b23ed038b99715481c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75364951"
---
# <a name="workspace-expression-in-azure-monitor-log-query"></a>munkaterület() kifejezés az Azure Monitor naplólekérdezésében

A `workspace` kifejezés egy Azure Monitor-lekérdezésben használatos adatok lekérése ugyanazon erőforráscsoport, egy másik erőforráscsoport vagy egy másik előfizetés egy adott munkaterületről. Ez akkor hasznos, ha naplóadatokat foglal bele egy Application Insights-lekérdezésbe, és több munkaterületen is lekérdezi az adatokat egy naplólekérdezésben.


## <a name="syntax"></a>Szintaxis

`workspace(`*Azonosító*`)`

## <a name="arguments"></a>Argumentumok

- *Azonosító*: A munkaterületet az alábbi táblázatban szereplő formátumok egyikével azonosítja.

| Azonosító | Leírás | Példa
|:---|:---|:---|
| Erőforrás neve | A munkaterület ember által olvasható neve (más néven "összetevő neve") | munkaterület("contosoretail") |
| Minősített név | A munkaterület teljes neve az űrlapon: "subscriptionName/resourceGroup/componentName" | munkaterület('Contoso/ContosoResource/ContosoWorkspace') |
| ID (Azonosító) | A munkaterület GUID azonosítója | munkaterület("b438b3f6-912a-46d5-9db1-b42069242ab4") |
| Azure-erőforrás azonosítója | Az Azure-erőforrás azonosítója | munkaterület("/előfizetések/e4227-645-44e-9c67-3b84b5982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail") |


## <a name="notes"></a>Megjegyzések

* Olvasási hozzáféréssel kell rendelkeznie a munkaterülethez.
* A kapcsolódó `app` kifejezés, amely lehetővé teszi az Application Insights-alkalmazások lekérdezése.

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

- Tekintse meg az [alkalmazáskifejezés,](app-expression.md) hogy egy Application Insights-alkalmazás.
- További információ az [Azure Monitor adatainak](log-query-overview.md) tárolásáról.
- A [Kusto lekérdezési nyelvének](/azure/kusto/query/)teljes dokumentációjának elérése.
