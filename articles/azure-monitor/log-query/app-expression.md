---
title: az Azure Log Analytics lekérdezési App() kifejezés |} A Microsoft Docs
description: Az alkalmazás kifejezés szolgál a Log Analytics-lekérdezés-adatokat lekérni egy adott Application Insights alkalmazást ugyanazt az erőforráscsoportot, egy másik erőforráscsoportot vagy egy másik előfizetést.
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
ms.date: 01/25/2019
ms.author: bwren
ms.openlocfilehash: 2d6f3517e988d6bd66ee9007e072ee87c71d1340
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55104660"
---
# <a name="app-expression-in-log-analytics-query"></a>a Log Analytics-lekérdezés App() kifejezés

A `app` kifejezés szolgál a Log Analytics-lekérdezés adatokat lekérni egy adott Application Insights alkalmazást ugyanazt az erőforráscsoportot, egy másik erőforráscsoportot vagy egy másik előfizetést. Ez hasznos alkalmazás adatait tartalmazzák a Log Analytics-lekérdezés, és adatokat szeretne lekérdezni egy Application Insights-lekérdezés több alkalmazás között.



## <a name="syntax"></a>Szintaxis

`app(`*Identifier*`)`


## <a name="arguments"></a>Argumentumok

- *Azonosító*: Azonosítja az alkalmazást, az alábbi táblázat a formátumok valamelyikével.

| Azonosító | Leírás | Példa
|:---|:---|:---|
| Erőforrás neve | Az alkalmazás (AVAGY "összetevő neve") emberi olvasható neve | app("fabrikamapp") |
| Minősített név | A képernyőn az alkalmazás teljes név: "subscriptionName/resourceGroup/componentName" | app('AI-Prototype/Fabrikam/fabrikamapp') |
| ID (Azonosító) | Az alkalmazás GUID azonosítója | app("988ba129-363e-4415-8fe7-8cbab5447518") |
| Azure-erőforrás azonosítója | Az Azure-erőforrás azonosítója |App("/Subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/Providers/Microsoft.insights/Components/fabrikamapp") |


## <a name="notes"></a>Megjegyzések

* Az alkalmazás olvasási hozzáféréssel kell rendelkeznie.
* Alkalmazások azonosítása a neve alapján feltételezi, hogy azt egyedi az összes elérhető előfizetés. Ha több alkalmazás a megadott névvel, a lekérdezés sikertelen lesz a kétértelműség miatt. Ebben az esetben az egyéb azonosítókhoz kötött egyikét kell használnia.
* A kapcsolódó kifejezés használata [munkaterület](workspace-expression.md) lekérdezni a Log Analytics-munkaterületek között.
* App() kifejezés jelenleg nem támogatott a keresési lekérdezésben. Ha az Azure portal használatával hozhat létre egy [egyéni keresés riasztási szabály](../platform/alerts-log.md), kivéve, ha egy Application Insights-alkalmazás és az erőforrásnak a riasztási szabály szolgál.

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

- Tekintse meg a [munkaterület kifejezés](workspace-expression.md) lehet hivatkozni a Log Analytics-munkaterületet.
- Megtudhatja, hogyan lehet [Log Analytics-adatok](../../azure-monitor/log-query/log-query-overview.md) tárolja.