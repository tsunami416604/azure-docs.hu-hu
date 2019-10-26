---
title: alkalmazás () kifejezés Azure Monitor log lekérdezésekben | Microsoft Docs
description: Az alkalmazás kifejezése egy Azure Monitor naplózási lekérdezésben szerepel, hogy egy adott Application Insights alkalmazásból egy adott erőforráscsoport, egy másik erőforráscsoport vagy egy másik előfizetés adatait olvassa be.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: article
author: bwren
ms.author: bwren
ms.date: 01/25/2019
ms.openlocfilehash: fd6bfd40eadfc09008c992d263b065d7b41ffa1f
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894475"
---
# <a name="app-expression-in-azure-monitor-query"></a>alkalmazás () kifejezés Azure Monitor lekérdezésben

A `app` kifejezés egy Azure Monitor lekérdezésben van használatban, hogy egy adott erőforráscsoport, egy másik erőforráscsoport vagy egy másik előfizetés adatait lekérje egy adott Application Insights alkalmazásból. Ez hasznos lehet az alkalmazásadatok Azure Monitor naplózási lekérdezésbe való felvételéhez, valamint az adatlekérdezés több alkalmazás között egy Application Insights lekérdezésben.



## <a name="syntax"></a>Szintaxis

`app(`*azonosító*`)`


## <a name="arguments"></a>Argumentumok

- *Azonosító*: az alábbi táblázatban szereplő formátumok egyikének használatával azonosítja az alkalmazást.

| Azonosító | Leírás | Példa
|:---|:---|:---|
| Erőforrás neve | Az alkalmazás olvasható neve (más néven "összetevő neve") | alkalmazás ("fabrikamapp") |
| Minősített név | Az alkalmazás teljes neve a következő formában: "subscriptionName/resourceGroup/componentName" | alkalmazás (AI-Prototype/Fabrikam/fabrikamapp) |
| ID (Azonosító) | Az alkalmazás GUID azonosítója | alkalmazás ("988ba129-363e-4415-8fe7-8cbab5447518") |
| Azure-erőforrás azonosítója | Az Azure-erőforrás azonosítója |alkalmazás ("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>Megjegyzések

* Olvasási hozzáféréssel kell rendelkeznie az alkalmazáshoz.
* Ha az alkalmazás neve alapján azonosítja az alkalmazást, azt feltételezi, hogy az összes elérhető előfizetésen belül egyedi. Ha több alkalmazás is van a megadott névvel, a lekérdezés nem fog sikerülni a kétértelműség miatt. Ebben az esetben a többi azonosító egyikét kell használnia.
* A kapcsolódó kifejezés [munkaterülete](workspace-expression.md) log Analytics munkaterületek lekérdezésére használható.
* Az app () kifejezés jelenleg nem támogatott a keresési lekérdezésben, ha a Azure Portal használatával hoz létre [Egyéni napló-keresési riasztási szabályt](../platform/alerts-log.md), kivéve, ha egy Application Insights alkalmazást használ a riasztási szabályhoz tartozó erőforrásként.

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

## <a name="next-steps"></a>Következő lépések

- Tekintse meg a [munkaterület kifejezését](workspace-expression.md) log Analytics munkaterületre való hivatkozáshoz.
- További információ a [Azure monitor adatainak](../../azure-monitor/log-query/log-query-overview.md) tárolásáról.
- A [Kusto lekérdezési nyelv](/azure/kusto/query/)teljes dokumentációjának elérése.
