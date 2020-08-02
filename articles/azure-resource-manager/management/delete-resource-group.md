---
title: Erőforráscsoport és erőforrások törlése
description: Az erőforráscsoportok és erőforrások törlését ismerteti. Leírja, hogy a Azure Resource Manager hogyan rendeli az erőforrások törlését az erőforráscsoport törlésekor. Leírja a hibakódokat és azt, hogy a Resource Manager hogyan kezeli őket annak megállapításához, hogy a törlés sikeres volt-e.
ms.topic: conceptual
ms.date: 09/03/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 2720a9ae8c0a67d8e6f9c1105b33c0a3c343cf47
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498497"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Erőforrás-csoport és erőforrás-törlés Azure Resource Manager

Ez a cikk az erőforráscsoportok és erőforrások törlését ismerteti. Leírja, hogy a Azure Resource Manager hogyan rendeli az erőforrások törlését az erőforráscsoport törlésekor.

## <a name="how-order-of-deletion-is-determined"></a>A törlés sorrendjének meghatározása

Egy erőforráscsoport törlésekor a Resource Manager meghatározza az erőforrások törlésének sorrendjét. A következő sorrendet használja:

1. Az összes alárendelt (beágyazott) erőforrás törölve.

2. A többi erőforrást kezelő erőforrásokat a következő lépéssel törli a rendszer. Egy erőforrás rendelkezhet úgy `managedBy` , hogy egy másik erőforrás felügyelje azt. Ha ez a tulajdonság be van állítva, a másik erőforrást kezelő erőforrás törlődik a többi erőforrás előtt.

3. A fennmaradó erőforrásokat az előző két kategória után törli a rendszer.

A megrendelés meghatározása után a Resource Manager minden erőforráshoz kiállít egy TÖRLÉSi műveletet. A folytatás előtt megvárja, amíg a függőségek befejeződik.

A szinkron műveletek esetében a várt sikeres válasz kódok a következők:

* 200
* 204
* 404

Aszinkron műveletek esetén a várt sikeres válasz a 202. A Resource Manager a Location (hely) vagy az Azure-aszinkron művelet fejlécét követi nyomon az aszinkron törlési művelet állapotának meghatározásához.
  
### <a name="deletion-errors"></a>Törlési hibák

Ha egy törlési művelet hibát ad vissza, a Resource Manager újrapróbálkozik a TÖRLÉSi hívással. Újrapróbálkozások történnek a 5xx, az 429-es és a 408-es állapotkódok esetében. Alapértelmezés szerint az Újrapróbálkozás időtartama 15 perc.

## <a name="after-deletion"></a>Törlés után

A Resource Manager egy GET hívást indít el minden olyan erőforráson, amelyet törölni próbált. A GET hívás válaszának várható értéke 404. Ha a Resource Manager 404-as lekérést kap, a törlés sikeres befejezését veszi figyelembe. A Resource Manager eltávolítja az erőforrást a gyorsítótárból.

Ha azonban az erőforrás GET hívása egy 200 vagy 201 értéket ad vissza, a Resource Manager újból létrehozza az erőforrást.

Ha a GET művelet hibát ad vissza, a Resource Manager újrapróbálkozik a következő hibakóddal:

* Kevesebb mint 100
* 408
* 429
* Nagyobb, mint 500

Más hibakódok esetén a Resource Manager nem tudja törölni az erőforrást.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza.

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

# <a name="portal"></a>[Portál](#tab/azure-portal)

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

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A [portálon](https://portal.azure.com)válassza ki a törölni kívánt erőforrást.

1. Válassza a **Törlés** elemet. Az alábbi képernyőfelvételen egy virtuális gép felügyeleti lehetőségei láthatók.

   ![Erőforrás törlése](./media/delete-resource-group/delete-resource.png)

1. A rendszer kérésére erősítse meg a törlést.

---


## <a name="next-steps"></a>További lépések

* A Resource Manager-fogalmak megismeréséhez tekintse meg az [Azure Resource Manager áttekintése](overview.md)című témakört.
* A törlési parancsokért lásd: [PowerShell](/powershell/module/az.resources/Remove-AzResourceGroup), [Azure CLI](/cli/azure/group?view=azure-cli-latest#az-group-delete)és [REST API](/rest/api/resources/resourcegroups/delete).
