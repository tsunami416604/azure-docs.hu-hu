---
title: Egy kimeneti érték több példányának definiálása
description: Az Azure Resource Manager-sablonban használt másolási műveletet többször idoterészévé tetszetősítheti, amikor értéket ad vissza egy központi telepítésből.
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 3889260d02f438274c80e99e99136515499443e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153386"
---
# <a name="output-iteration-in-arm-templates"></a>Kimeneti ismétlés ARM sablonokban

Ez a cikk bemutatja, hogyan hozhat létre egynél több értéket egy kimenethez az Azure Resource Manager (ARM) sablonban. Ha hozzáadja a **másolási** elemet a sablon kimeneti szakaszához, dinamikusan visszaadhat néhány elemet a központi telepítés során.

A [másolást erőforrásokkal](copy-resources.md), [erőforrások tulajdonságaival](copy-properties.md)és változókkal is [használhatja.](copy-variables.md)

## <a name="outputs-iteration"></a>Kimeneti iteráció

A másolási elem általános formátuma a következő:

```json
"copy": [
  {
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

A **count** tulajdonság a kimeneti értékhez kívánt ismétlések számát adja meg.

A **bemeneti** tulajdonság határozza meg az ismétlődő tulajdonságokat. A **bemeneti** tulajdonság értékéből létrehozott elemek tömbjét hozza létre. Ez lehet egyetlen tulajdonság (például egy karakterlánc), vagy egy több tulajdonsággal rendelkező objektum.

A következő példa változó számú tárfiókot hoz létre, és minden tárfiókhoz egy végpontot ad vissza:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageEndpoints": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
            }
        }
    }
}
```

Az előző sablon a következő értékekkel rendelkező tömböt ad vissza:

```json
[
    "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

A következő példa három tulajdonságot ad vissza az új tárfiókokból.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageInfo": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": {
                    "id": "[reference(concat(copyIndex(), variables('baseName')), '2019-04-01', 'Full').resourceId]",
                    "blobEndpoint": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]",
                    "status": "[reference(concat(copyIndex(), variables('baseName'))).statusOfPrimary]"
                }
            }
        }
    }
}
```

Az előző példa a következő értékekkel rendelkező tömböt ad vissza:

```json
[
    {
        "id": "Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
        "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    },
    {
        "id": "Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
        "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    }
]
```

## <a name="next-steps"></a>További lépések

* Ha egy oktatóanyagon szeretne átmenni, olvassa el [az Oktatóanyag: Több erőforráspéldány létrehozása ARM-sablonokkal című témakört.](template-tutorial-create-multiple-instances.md)
* A másolási elem egyéb felhasználási ideje:
  * [Erőforrás-ismétlés ARM-sablonokban](copy-resources.md)
  * [Tulajdonság ismétlése ARM sablonokban](copy-properties.md)
  * [Változó iteráció arm sablonokban](copy-variables.md)
* Ha a sablonok szakaszairól szeretne többet megtudni, olvassa el [az ARM-sablonok készítése című témakört.](template-syntax.md)
* A sablon üzembe helyezéséről az [Alkalmazás telepítése ARM sablonnal (Alkalmazás telepítése ARM sablonnal) témakörben](deploy-powershell.md)olvashat.

