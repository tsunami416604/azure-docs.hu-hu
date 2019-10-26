---
title: erőforrás () kifejezés Azure Monitor napló lekérdezésében | Microsoft Docs
description: Az erőforrás-kifejezés egy erőforrás-központú Azure Monitor-naplózási lekérdezésben használható több erőforrásból származó adatok lekérdezéséhez.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: article
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 4787dc8a8cc06e0ad0be88597dab3481284fb58b
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900217"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>erőforrás () kifejezés Azure Monitor napló lekérdezésében

A `resource` kifejezés egy [erőforrásra hatókörön](scope.md#query-scope) belüli Azure monitor lekérdezésben szerepel, hogy más erőforrásokból származó adatok kérhetők le. 


## <a name="syntax"></a>Szintaxis

`resource(`*azonosító*`)`

## <a name="arguments"></a>Argumentumok

- *Azonosító*: erőforrás erőforrás-azonosítója.

| Azonosító | Leírás | Példa
|:---|:---|:---|
| Erőforrás | Az erőforráshoz tartozó adatforrásokat tartalmazza. | erőforrás ("/Subscriptions/xxxxxxx-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourcesgroups/myresourcegroup/Providers/Microsoft.Compute/virtualmachines/myvm") |
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


## <a name="next-steps"></a>Következő lépések

- A lekérdezési hatókör részleteiért tekintse meg a [naplózási lekérdezés hatókörét és időtartományát Azure Monitor log Analytics](scope.md) .
- A [Kusto lekérdezési nyelv](/azure/kusto/query/)teljes dokumentációjának elérése.
