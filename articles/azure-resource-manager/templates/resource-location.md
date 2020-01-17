---
title: Sablon erőforrásának helye
description: Útmutató az erőforrás helyének megadásához egy Azure Resource Manager sablonban.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: 24d278df8f71fecfaec4f0fa3a84172bf1db942b
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122406"
---
# <a name="set-resource-location-in-resource-manager-template"></a>Erőforrás helyének beállítása a Resource Manager-sablonban

Sablon telepítésekor meg kell adnia egy helyet az egyes erőforrásokhoz. A helynek nem kell ugyanazon a helyen lennie, mint az erőforráscsoport helye.

## <a name="get-available-locations"></a>Elérhető helyszínek beolvasása

A különböző típusú erőforrástípusok különböző helyszíneken támogatottak. Az erőforrástípus támogatott helyeinek beszerzéséhez használja a Azure PowerShell vagy az Azure CLI-t.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes `
  | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az provider show \
  --namespace Microsoft.Batch \
  --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" \
  --out table
```

---

## <a name="use-location-parameter"></a>Hely paraméterének használata

A sablon üzembe helyezése során a rugalmasság lehetővé tételéhez használjon egy paramétert az erőforrások helyének megadásához. Állítsa `resourceGroup().location`értékre a paraméter alapértelmezett értékét.

A következő példa egy olyan Storage-fiókot mutat be, amely paraméterként megadott helyre van telepítve:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('storage', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2018-07-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

## <a name="next-steps"></a>Következő lépések

* A sablon függvények teljes listájáért lásd: [Azure Resource Manager template functions](template-functions.md).
* További információ a sablonfájlokről: [Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése](template-syntax.md).
