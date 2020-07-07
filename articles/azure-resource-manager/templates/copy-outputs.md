---
title: Egy kimeneti érték több példányának meghatározása
description: A másolási művelettel Azure Resource Manager sablonban több alkalommal is megismételhető, amikor értéket ad vissza egy központi telepítésből.
ms.topic: conceptual
ms.date: 04/17/2020
ms.openlocfilehash: 50c4b4b8f301ad88d3dfde98ace1aed4431693db
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82583427"
---
# <a name="output-iteration-in-arm-templates"></a>Kimeneti iteráció az ARM-sablonokban

Ez a cikk bemutatja, hogyan hozhat létre egynél több értéket egy kimenethez a Azure Resource Manager (ARM) sablonban. Ha hozzáadja a **Másolás** elemet a sablon outputs (kimenet) szakaszához, az üzembe helyezés során több elemet is dinamikusan visszaállíthat.

A másolást [erőforrásokkal](copy-resources.md), [erőforrásokkal](copy-properties.md)és [változókkal](copy-variables.md)is elvégezheti.

## <a name="syntax"></a>Syntax

A másolási elem a következő általános formátumú:

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

A **Count** tulajdonság megadja a kimeneti értékhez használni kívánt iterációk számát.

A **bemeneti** tulajdonság határozza meg a megismételni kívánt tulajdonságokat. A **bemeneti** tulajdonság értékével létrehozott elemek tömbjét hozza létre. Ez lehet egy tulajdonság (például egy karakterlánc) vagy egy olyan objektum, amely több tulajdonsággal rendelkezik.

## <a name="copy-limits"></a>Másolási korlátok

A szám nem lehet nagyobb, mint 800.

A darabszám nem lehet negatív szám. Ha az Azure CLI, a PowerShell vagy a REST API legújabb verziójával telepíti a sablont, akkor nulla lehet. Pontosabban a következőket kell használnia:

* Azure PowerShell **2,6** vagy újabb
* Azure CLI- **2.0.74** vagy újabb
* REST API **2019-05-10** -es vagy újabb verzió
* A [csatolt központi telepítéseknek](linked-templates.md) a telepítési erőforrástípus **2019-05-10** -es vagy újabb API-verzióját kell használniuk

A PowerShell, a CLI és a REST API korábbi verziói nem támogatják a nulla értéket a darabszámhoz.

## <a name="outputs-iteration"></a>Kimenetek iterációja

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

## <a name="next-steps"></a>További lépések

* Az oktatóanyag lépéseinek megismeréséhez tekintse meg az [oktatóanyag: több erőforrás-példány létrehozása ARM-sablonok használatával](template-tutorial-create-multiple-instances.md)című témakört.
* A másolási elem egyéb felhasználási módjaiért lásd:
  * [Erőforrás-iteráció az ARM-sablonokban](copy-resources.md)
  * [Tulajdonság-iteráció az ARM-sablonokban](copy-properties.md)
  * [Változó iteráció az ARM-sablonokban](copy-variables.md)
* Ha szeretne többet megtudni egy sablon fejezeteiről, tekintse meg a következő témakört: [ARM-sablonok készítése](template-syntax.md).
* A sablon üzembe helyezésével kapcsolatos további információkért lásd: [alkalmazás üzembe helyezése ARM-sablonnal](deploy-powershell.md).

