---
title: app() kifejezés az Azure Monitor naplólekérdezésében | Microsoft dokumentumok
description: Az alkalmazáskifejezés egy Azure Monitor naplólekérdezésben használatos egy adott Application Insights-alkalmazásból ugyanabban az erőforráscsoportban, egy másik erőforráscsoportban vagy egy másik előfizetésben.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/25/2019
ms.openlocfilehash: 5502df1cd119c0f63c65945d73431a17282ebc0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670256"
---
# <a name="app-expression-in-azure-monitor-query"></a>app() kifejezés az Azure Monitor lekérdezésben

A `app` kifejezés egy Azure Monitor-lekérdezésben használatával adatokat kérhet le egy adott Application Insights-alkalmazásból ugyanabban az erőforráscsoportban, egy másik erőforráscsoportban vagy egy másik előfizetésben. Ez akkor hasznos, ha az alkalmazásadatokat egy Azure Monitor naplólekérdezésben tartalmazza, és az Application Insights-lekérdezésben több alkalmazás adatait is lekérdezi.



## <a name="syntax"></a>Szintaxis

`app(`*Azonosító*`)`


## <a name="arguments"></a>Argumentumok

- *Azonosító*: Az alkalmazást az alábbi táblázatban szereplő formátumok egyikével azonosítja.

| Azonosító | Leírás | Példa
|:---|:---|:---|
| Erőforrás neve | Az alkalmazás olvasható neve (más néven "összetevő neve") | app("fabrikamapp") |
| Minősített név | Az alkalmazás teljes neve az űrlapon: "subscriptionName/resourceGroup/componentName" | app('AI-Prototype/Fabrikam/fabrikamapp') |
| ID (Azonosító) | Az alkalmazás GUID azonosítója | alkalmazás("988ba129-363e-4415-8fe7-8cbab5447518") |
| Azure-erőforrás azonosítója | Az Azure-erőforrás azonosítója |app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>Megjegyzések

* Olvasási hozzáféréssel kell rendelkeznie az alkalmazáshoz.
* Egy alkalmazás név alapján történő azonosítása feltételezi, hogy az összes elérhető előfizetésben egyedi. Ha több alkalmazás a megadott nevet, a lekérdezés sikertelen lesz, mert a kétértelműség. Ebben az esetben a többi azonosító egyikét kell használnia.
* A kapcsolódó kifejezés [munkaterület](workspace-expression.md) használatával lekérdezés a Log Analytics-munkaterületeken.
* Az app() kifejezés jelenleg nem támogatott a keresési lekérdezésben, ha az Azure Portal on hozzon létre egy [egyéni napló keresési riasztási szabályt,](../platform/alerts-log.md)kivéve, ha egy Application Insights-alkalmazás a riasztási szabály erőforrásaként.

## <a name="examples"></a>Példák

```Kusto
app("fabrikamapp").requests | count
```
```Kusto
app("AI-Prototype/Fabrikam/fabrikamapp").requests | count
```
```Kusto
app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count
```
```Kusto
app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
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

- Tekintse meg a [munkaterület-kifejezést,](workspace-expression.md) amely a Log Analytics-munkaterületre hivatkozik.
- További információ az [Azure Monitor adatainak](../../azure-monitor/log-query/log-query-overview.md) tárolásáról.
- A [Kusto lekérdezési nyelvének](/azure/kusto/query/)teljes dokumentációjának elérése.
