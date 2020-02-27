---
title: Egy kimeneti érték több példányának meghatározása
description: A másolási művelettel Azure Resource Manager sablonban több alkalommal is megismételhető, amikor értéket ad vissza egy központi telepítésből.
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: db5c548c7bd4c60357d3656b1273b0192c497459
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624773"
---
# <a name="output-iteration-in-azure-resource-manager-templates"></a>Kimeneti iteráció Azure Resource Manager-sablonokban

Ez a cikk bemutatja, hogyan hozhat létre egynél több értéket egy kimenethez a Azure Resource Manager-sablonban. Ha hozzáadja a **Másolás** elemet a sablon outputs (kimenet) szakaszához, az üzembe helyezés során több elemet is dinamikusan visszaállíthat.

A másolást [erőforrásokkal](copy-resources.md), [erőforrásokkal](copy-properties.md)és [változókkal](copy-variables.md)is elvégezheti.

## <a name="outputs-iteration"></a>Kimenetek iterációja

A másolási elem a következő általános formátumú:

```json
"copy": [
  {
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

A **Count** tulajdonság megadja a kimeneti értékhez használni kívánt iterációk számát.

A **bemeneti** tulajdonság határozza meg a megismételni kívánt tulajdonságokat. A **bemeneti** tulajdonság értékével létrehozott elemek tömbjét hozza létre. Ez lehet egy tulajdonság (például egy karakterlánc) vagy egy olyan objektum, amely több tulajdonsággal rendelkezik.

Az alábbi példa egy változó számú Storage-fiókot hoz létre, és az egyes Storage-fiókok végpontját adja vissza:

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

Az előző sablon egy tömböt ad vissza a következő értékekkel:

```json
[
    "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

A következő példa az új Storage-fiókok három tulajdonságát adja vissza.

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

Az előző példa egy tömböt ad vissza a következő értékekkel:

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

## <a name="next-steps"></a>Következő lépések

* Az oktatóanyag lépéseinek megismeréséhez lásd [: oktatóanyag: több erőforrás-példány létrehozása Resource Manager-sablonok használatával](template-tutorial-create-multiple-instances.md).
* A másolási elem egyéb felhasználási módjaiért lásd:
  * [Erőforrás-iteráció Azure Resource Manager-sablonokban](copy-resources.md)
  * [Tulajdonság-iteráció Azure Resource Manager-sablonokban](copy-properties.md)
  * [Változó iteráció Azure Resource Manager-sablonokban](copy-variables.md)
* Ha szeretné megismerni a sablonok egyes részeit, olvassa el a [Azure Resource Manager sablonok készítése](template-syntax.md)című szakaszt.
* A sablon üzembe helyezésével kapcsolatos további információkért lásd: [alkalmazások központi telepítése Azure Resource Manager sablonnal](deploy-powershell.md).

