---
title: erőforrás () kifejezés Azure Monitor napló lekérdezésében | Microsoft Docs
description: Az erőforrás-kifejezés egy erőforrás-központú Azure Monitor-naplózási lekérdezésben használható több erőforrásból származó adatok lekérdezéséhez.
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
ms.openlocfilehash: deca6e7ef1c231a82a73067971d86a6e9cdd0599
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817409"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>erőforrás () kifejezés Azure Monitor napló lekérdezésében

A `resource` kifejezés egy olyan Azure Monitor-lekérdezésben szerepel, amely [egy erőforrásra terjed](scope.md#query-scope) ki, hogy más erőforrásokból kérje le az adatgyűjtést. 


## <a name="syntax"></a>Szintaxis

`resource(`*azonosító*`)`

## <a name="arguments"></a>Argumentumok

- *Azonosító*: Egy erőforrás erőforrás-azonosítója.

| azonosító | Leírás | Példa
|:---|:---|:---|
| Resource | Az erőforráshoz tartozó adatforrásokat tartalmazza. | erőforrás ("/Subscriptions/xxxxxxx-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourcesgroups/myresourcegroup/Providers/Microsoft.Compute/virtualmachines/myvm") |
| Erőforráscsoport vagy előfizetés | Az erőforráshoz és a benne található összes erőforráshoz tartozó adattartalom.  | erőforrás ("/Subscriptions/xxxxxxx-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourcesgroups/myresourcegroup") |


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

- A lekérdezési hatókör részleteiért tekintse meg a [naplózási lekérdezés hatókörét és időtartományát Azure Monitor log Analytics](scope.md) .
- A [Kusto lekérdezési nyelv](/azure/kusto/query/)teljes dokumentációjának elérése.
