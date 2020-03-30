---
title: resource() kifejezés az Azure Monitor naplólekérdezésében | Microsoft dokumentumok
description: Az erőforrás-kifejezés egy erőforrás-központú Azure Monitor naplólekérdezésben használatos több erőforrásból történő lekéréséhez.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 2a729caefe698b13833098ba48df9d4bfbd97356
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665699"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>resource() kifejezés az Azure Monitor naplólekérdezésében

A `resource` kifejezés egy Erőforráshoz tartozó Azure Monitor-lekérdezéshatókörben használatos, más erőforrásokból történő adatok lekéréséhez. [scoped to a resource](scope.md#query-scope) 


## <a name="syntax"></a>Szintaxis

`resource(`*Azonosító*`)`

## <a name="arguments"></a>Argumentumok

- *Azonosító*: egy erőforrás erőforrásazonosítója.

| Azonosító | Leírás | Példa
|:---|:---|:---|
| Erőforrás | Az erőforrás adatait tartalmazza. | resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm") |
| Erőforráscsoport vagy előfizetés | Tartalmazza az erőforrás és a benne lévő összes erőforrás adatait.  | resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup) |


## <a name="notes"></a>Megjegyzések

* Olvasási hozzáféréssel kell rendelkeznie az erőforráshoz.


## <a name="examples"></a>Példák

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>További lépések

- A [lekérdezéshatókör és az időtartomány naplózása](scope.md) az Azure Monitor Log Analytics szolgáltatásban a lekérdezési hatókör részleteiről.
- A [Kusto lekérdezési nyelvének](/azure/kusto/query/)teljes dokumentációjának elérése.
