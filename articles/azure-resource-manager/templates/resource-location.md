---
title: Sablon erőforrás helye
description: Ez a témakör azt ismerteti, hogy miként állítható be az erőforrás-hely egy Azure Resource Manager-sablonban.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: a8324dac1232eecd5624e5f1dc0e6656295c0a10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156429"
---
# <a name="set-resource-location-in-arm-template"></a>Erőforrás helyének beállítása az ARM sablonban

Az Azure Resource Manager (ARM) sablon telepítésekor meg kell adnia egy helyet minden erőforráshoz. A helynek nem kell megegyeznie az erőforráscsoport helyével.

## <a name="get-available-locations"></a>Elérhető helyek beszerzése

A különböző erőforrástípusok at különböző helyeken támogatják. Egy erőforrástípus támogatott helyeinek beszerezéséhez használja az Azure PowerShell vagy az Azure CLI használatát.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes `
  | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az provider show \
  --namespace Microsoft.Batch \
  --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" \
  --out table
```

---

## <a name="use-location-parameter"></a>Helyparaméter használata

A sablon üzembe helyezésekor a rugalmasság érdekében egy paraméter segítségével adja meg az erőforrások helyét. Állítsa a paraméter alapértelmezett `resourceGroup().location`értékét a értékre.

A következő példa egy olyan tárfiókot mutat be, amely paraméterként megadott helyre van telepítve:

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

## <a name="next-steps"></a>További lépések

* A sablonfüggvények teljes listáját az [Azure Resource Manager sablonfüggvényei című témakörben olvashatja.](template-functions.md)
* A sablonfájlokról további információt [az ARM-sablonok szerkezetének és szintaxisának megismerése című témakörben talál.](template-syntax.md)
