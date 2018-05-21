---
title: Az Azure Resource Manager sablonfüggvényei - összehasonlítása |} Microsoft Docs
description: Az értékek összehasonlítása az Azure Resource Manager sablon használatával funkcióit ismerteti.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/05/2017
ms.author: tomfitz
ms.openlocfilehash: 364a271d84f9abfe99c7c674a6c504ce94318ac9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="comparison-functions-for-azure-resource-manager-templates"></a>Az Azure Resource Manager sablonokhoz összehasonlítás funkciók

Erőforrás-kezelő számos funkciókat nyújt a sablonokban összehasonlításához.

* [egyenlő](#equals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)
* [kevesebb](#less)
* [lessOrEquals](#lessorequals)

## <a name="equals"></a>egyenlő
`equals(arg1, arg2)`

Ellenőrzi, hogy a két érték egyenlő egymással.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |int, string, tömb vagy objektum |Az első érték egyenlő kereséséhez. |
| Arg2 |Igen |int, string, tömb vagy objektum |A második érték egyenlő kereséséhez. |

### <a name="return-value"></a>Visszatérési érték

Beolvasása **igaz** Ha két érték egyenlő; ellenkező esetben **hamis**.

### <a name="remarks"></a>Megjegyzések

A mező értéke függvény gyakran használják a a `condition` elem annak megállapítására, hogy egy erőforrás van telepítve.

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/equals.json) ellenőrzi a különböző típusú érték azonosságát. Az alapértelmezett értékeket adhat vissza IGAZ.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 1
        },
        "firstString": {
            "type": "string",
            "defaultValue": "a"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "firstObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[equals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[equals(parameters('firstString'), parameters('secondString'))]"
        },
        "checkArrays": {
            "type": "bool",
            "value": "[equals(parameters('firstArray'), parameters('secondArray'))]"
        },
        "checkObjects": {
            "type": "bool",
            "value": "[equals(parameters('firstObject'), parameters('secondObject'))]"
        }
    }
}
```

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| checkInts | logikai érték | True (Igaz) |
| checkStrings | logikai érték | True (Igaz) |
| checkArrays | logikai érték | True (Igaz) |
| checkObjects | logikai érték | True (Igaz) |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json 
```

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) használ [nem](resource-group-template-functions-logical.md#not) rendelkező **egyenlő**.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "checkNotEquals": {
            "type": "bool",
            "value": "[not(equals(1, 2))]"
        }
    }
}
```

Az előző példában a kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| checkNotEquals | logikai érték | True (Igaz) |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json 
```

## <a name="greater"></a>nagyobb
`greater(arg1, arg2)`

Ellenőrzi, hogy az első érték nagyobb, mint a második érték.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |egész szám vagy karakterlánc |Az első érték nagyobb összehasonlítására. |
| Arg2 |Igen |egész szám vagy karakterlánc |A második érték nagyobb összehasonlítására. |

### <a name="return-value"></a>Visszatérési érték

Beolvasása **igaz** Ha az első érték nagyobb, mint a második érték; ellenkező esetben **hamis**.

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json) ellenőrzi, hogy az egyik érték nagyobb, mint a többi.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greater(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greater(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| checkInts | logikai érték | False (Hamis) |
| checkStrings | logikai érték | True (Igaz) |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json 
```

## <a name="greaterorequals"></a>greaterOrEquals
`greaterOrEquals(arg1, arg2)`

Ellenőrzi, hogy az első érték kisebb, mint a második érték.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |egész szám vagy karakterlánc |Az első érték kisebb, mint összehasonlítására. |
| Arg2 |Igen |egész szám vagy karakterlánc |A második érték kisebb, mint összehasonlítására. |

### <a name="return-value"></a>Visszatérési érték

Beolvasása **igaz** Ha az első érték nagyobb, mint vagy egyenlő a második érték; ellenkező esetben **hamis**.

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json) ellenőrzi, hogy az egyik érték nagyobb vagy egyenlő a másikra.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| checkInts | logikai érték | False (Hamis) |
| checkStrings | logikai érték | True (Igaz) |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json 
```

## <a name="less"></a>kevesebb
`less(arg1, arg2)`

Ellenőrzi, hogy van-e az első érték kisebb, mint a második érték.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |egész szám vagy karakterlánc |Az első érték kisebb összehasonlítására. |
| Arg2 |Igen |egész szám vagy karakterlánc |A második érték kevesebb összehasonlítására. |

### <a name="return-value"></a>Visszatérési érték

Beolvasása **igaz** Ha az első érték kisebb, mint a második érték; ellenkező esetben **hamis**.

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json) ellenőrzi, hogy az egyik érték kisebb, mint a többi.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[less(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[less(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| checkInts | logikai érték | True (Igaz) |
| checkStrings | logikai érték | False (Hamis) |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json 
```

## <a name="lessorequals"></a>lessOrEquals
`lessOrEquals(arg1, arg2)`

Ellenőrzi, hogy az első érték nagyobb, mint a második érték.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |egész szám vagy karakterlánc |Az első értékét a kevésbé vagy egyenlőségi összehasonlítás. |
| Arg2 |Igen |egész szám vagy karakterlánc |A második érték, annál kisebb a vagy egyenlőségi összehasonlítást. |

### <a name="return-value"></a>Visszatérési érték

Beolvasása **igaz** az első érték kisebb, mint vagy egyenlő a második érték, ha sikertelen, ha **hamis**.

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json) ellenőrzi, hogy egy érték kisebb vagy egyenlő, mint a másikra.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| checkInts | logikai érték | True (Igaz) |
| checkStrings | logikai érték | False (Hamis) |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json 
```

## <a name="next-steps"></a>További lépések
* A szakaszok az Azure Resource Manager-sablon ismertetését lásd: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).
* Több sablon egyesíteni, lásd: [kapcsolt sablonok használata az Azure Resource Manager](resource-group-linked-templates.md).
* Megadott számú alkalommal felépítésének egy adott típusú erőforrás létrehozása esetén lásd: [erőforrások több példánya létrehozása az Azure Resource Manager](resource-group-create-multiple.md).
* A sablon létrehozott központi telepítéséről, olvassa el [Azure Resource Manager-sablon az alkalmazás központi telepítését](resource-group-template-deploy.md).

