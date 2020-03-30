---
title: Erőforráscsoport és erőforrások törlése
description: Erőforráscsoportok és -erőforrások törlésének ismertetése. Azt ismerteti, hogy az Azure Resource Manager hogyan rendeli el az erőforrások törlését egy erőforráscsoport törlésekor. Leírja a válaszkódokat, és azt, hogy az Erőforrás-kezelő hogyan kezeli őket annak megállapítására, hogy a törlés sikeres volt-e.
ms.topic: conceptual
ms.date: 09/03/2019
ms.custom: seodec18
ms.openlocfilehash: db56cf0897cd90f1e6e51199032d0d9712530f1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274020"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Az Azure Resource Manager erőforráscsoport és az erőforrás törlése

Ez a cikk bemutatja, hogyan lehet törölni az erőforráscsoportokat és az erőforrásokat. Azt ismerteti, hogy az Azure Resource Manager hogyan rendeli el az erőforrások törlését egy erőforráscsoport törlésekor.

## <a name="how-order-of-deletion-is-determined"></a>A törlés sorrendjének meghatározása

Erőforráscsoport törlésekor az Erőforrás-kezelő határozza meg az erőforrások törlésének sorrendjét. A következő sorrendet használja:

1. Az összes gyermek (beágyazott) erőforrás törlődik.

2. A többi erőforrást kezelő erőforrások a következőkben törlődnek. Egy erőforrás beállíthatja a `managedBy` tulajdonságot, hogy jelezze, hogy egy másik erőforrás kezeli azt. Ha ez a tulajdonság be van állítva, a másik erőforrást kezelő erőforrás a többi erőforrás előtt törlődik.

3. A fennmaradó erőforrások az előző két kategória után törlődnek.

A rendelés meghatározása után az Erőforrás-kezelő minden erőforráshoz kiad egy DELETE műveletet. A folytatás előtt megvárja, amíg befejeződnek a függőségek.

Szinkron műveletek esetén a várt sikeres válaszkódok a következők:

* 200
* 204
* 404

Aszinkron műveletek esetén a várt sikeres válasz 202. Az Erőforrás-kezelő nyomon követi a helyfejlécet vagy az azure-async művelet fejlécét az aszinkron törlési művelet állapotának meghatározásához.
  
### <a name="deletion-errors"></a>Törlési hibák

Ha egy törlési művelet hibát ad vissza, az Erőforrás-kezelő újrapróbálkozik a DELETE hívással. Az 5xx, 429 és 408 állapotkódok újrapróbálkozásai. Alapértelmezés szerint az újrapróbálkozás idotartama 15 perc.

## <a name="after-deletion"></a>Törlés után

Az Erőforrás-kezelő minden olyan erőforráshoz get-hívást ad ki, amelyet törölni próbált. A válasz ennek a GET hívás várhatóan 404. Amikor az Erőforrás-kezelő 404-es kap, úgy véli, hogy a törlés sikeresen befejeződött. Az Erőforrás-kezelő eltávolítja az erőforrást a gyorsítótárból.

Ha azonban a GET hívás az erőforrás200-at vagy 201-et ad vissza, az Erőforrás-kezelő újralétrehozza az erőforrást.

Ha a GET művelet hibát ad vissza, az Erőforrás-kezelő újrapróbálkozik a GET-vel a következő hibakód hoz:

* Kevesebb mint 100
* 408
* 429
* 500-nál nagyobb

Más hibakódok esetén az Erőforrás-kezelő nem tudja elsikkasztani az erőforrást.

## <a name="delete-resource-group"></a>Erőforráscsoport törlése

Az erőforráscsoport törléséhez használja az alábbi módszerek egyikét.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A [portálon](https://portal.azure.com)jelölje ki a törölni kívánt erőforráscsoportot.

1. Válassza az **Erőforráscsoport törlése** elemet.

   ![Erőforráscsoport törlése](./media/delete-resource-group/delete-group.png)

1. A törlés megerősítéséhez írja be az erőforráscsoport nevét.

---

## <a name="delete-resource"></a>Erőforrás törlése

Az erőforrások törléséhez használja az alábbi módszerek egyikét.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

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

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A [portálon](https://portal.azure.com)jelölje ki a törölni kívánt erőforrást.

1. Válassza a **Törlés** elemet. A következő képernyőképen a virtuális gépek felügyeleti beállításai láthatók.

   ![Erőforrás törlése](./media/delete-resource-group/delete-resource.png)

1. A rendszer kérésére erősítse meg a törlést.

---


## <a name="next-steps"></a>További lépések

* Az Erőforrás-kezelő vel kapcsolatos fogalmak megértéséhez olvassa el az [Azure Resource Manager áttekintése című témakört.](overview.md)
* A törlési parancsokról a [PowerShell,](/powershell/module/az.resources/Remove-AzResourceGroup)az Azure CLI és [a REST API.for](/rest/api/resources/resourcegroups/delete)deletion commands, see PowerShell , [Azure CLI](/cli/azure/group?view=azure-cli-latest#az-group-delete), and REST API .
