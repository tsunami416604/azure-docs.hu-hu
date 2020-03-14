---
title: Erőforráscsoport és erőforrások törlése
description: Az erőforráscsoportok és erőforrások törlését ismerteti. Leírja, hogy a Azure Resource Manager hogyan rendeli az erőforrások törlését az erőforráscsoport törlésekor. Leírja a válaszkódot, valamint hogyan Resource Manager kezeli őket határozza meg, ha a törlés sikerült.
ms.topic: conceptual
ms.date: 09/03/2019
ms.custom: seodec18
ms.openlocfilehash: db56cf0897cd90f1e6e51199032d0d9712530f1c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274020"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Erőforrás-csoport és erőforrás-törlés Azure Resource Manager

Ez a cikk az erőforráscsoportok és erőforrások törlését ismerteti. Leírja, hogy a Azure Resource Manager hogyan rendeli az erőforrások törlését az erőforráscsoport törlésekor.

## <a name="how-order-of-deletion-is-determined"></a>A törlés sorrendjének meghatározása

Ha töröl egy erőforráscsoportot, a Resource Manager ahhoz, hogy törölje az erőforrást a határozza meg. Használja a következő sorrendben:

1. Az összes gyermek (beágyazott) erőforrások törlődnek.

2. Erőforrások, amelyek egyéb erőforrások kezelése a következő törlődnek. Egy erőforrás rendelkezhet a `managedBy` tulajdonsággal, amely azt jelzi, hogy egy másik erőforrás kezeli azt. Ha ez a tulajdonság értéke, az erőforrást, amely kezeli a többi erőforrás törlődik, mielőtt a többi erőforrást.

3. A további erőforrások az előző két kategóriába után törlődnek.

A sorrend határozza meg, miután a Resource Manager problémák minden erőforrás egy törlési művelet. A folytatás előtt befejezéséhez függőségek vár.

A szinkron műveletek esetében a sikeres válasz várt kódok a következők:

* 200
* 204
* 404

Az aszinkron műveletek a várt a sikeres válasz 202. Erőforrás-kezelő nyomon követi a location fejlécet, vagy az azure-aszinkron művelet fejlécére az aszinkron törlési művelet állapotának megállapítása.
  
### <a name="deletion-errors"></a>Törlési hibák

Egy törlési művelet hibát ad vissza, ha a Resource Manager újrapróbálkozik a törlési hívásban. Az újrapróbálkozások az 5xx, 429-es és 408 állapotkódok fordulhat elő. Alapértelmezés szerint az adott időszakban újra beállítás 15 perc.

## <a name="after-deletion"></a>Törlés után

Erőforrás-kezelő kiad egy GET hívást végrehajtó erőforrásokra helyezett próbált meg törölni. A válasz a GET-hívás várhatóan 404-es. Ha a Resource Manager lekérdezi a 404-es, úgy ítéli meg, a törlés sikeresen befejeződött. Resource Manager az erőforrás eltávolítja a gyorsítótárból.

Azonban az erőforráson a GET-hívás 200-as vagy a 201-es adja vissza, ha a Resource Manager az erőforrást újra létrehozza.

A GET-művelet hibát ad vissza, ha a Resource Manager újrapróbálkozik a GET esetében a következő hibakóddal:

* 100-nál kisebb
* 408
* 429
* Nagyobb, mint 500

Más hibakódok erőforrás-kezelő nem az erőforrás törlését.

## <a name="delete-resource-group"></a>Erőforráscsoport törlése

Az erőforráscsoport törléséhez használja az alábbi módszerek egyikét.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. A [portálon](https://portal.azure.com)válassza ki a törölni kívánt erőforráscsoportot.

1. Válassza az **Erőforráscsoport törlése** elemet.

   ![Erőforráscsoport törlése](./media/delete-resource-group/delete-group.png)

1. A törlés megerősítéséhez írja be az erőforráscsoport nevét.

---

## <a name="delete-resource"></a>Erőforrás törlése

Az erőforrások törléséhez használja az alábbi módszerek egyikét.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResource `
  -ResourceGroupName ExampleResourceGroup `
  -ResourceName ExampleVM `
  -ResourceType Microsoft.Compute/virtualMachines
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az resource delete \
  --resource-group ExampleResourceGroup \
  --name ExampleVM \
  --resource-type "Microsoft.Compute/virtualMachines"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. A [portálon](https://portal.azure.com)válassza ki a törölni kívánt erőforrást.

1. Válassza a **Törlés** elemet. Az alábbi képernyőfelvételen egy virtuális gép felügyeleti lehetőségei láthatók.

   ![Erőforrás törlése](./media/delete-resource-group/delete-resource.png)

1. A rendszer kérésére erősítse meg a törlést.

---


## <a name="next-steps"></a>Következő lépések

* A Resource Manager-fogalmak megismeréséhez tekintse meg az [Azure Resource Manager áttekintése](overview.md)című témakört.
* A törlési parancsokért lásd: [PowerShell](/powershell/module/az.resources/Remove-AzResourceGroup), [Azure CLI](/cli/azure/group?view=azure-cli-latest#az-group-delete)és [REST API](/rest/api/resources/resourcegroups/delete).
