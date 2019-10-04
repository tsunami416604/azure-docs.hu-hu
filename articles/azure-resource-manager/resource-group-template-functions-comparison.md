---
title: Azure Resource Manager template functions – összehasonlítás | Microsoft Docs
description: A Azure Resource Manager-sablonban az értékek összehasonlításához használandó függvényeket ismerteti.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/05/2017
ms.author: tomfitz
ms.openlocfilehash: 05e51af62be974f925fefc46ad63c489f27fd163
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194828"
---
# <a name="comparison-functions-for-azure-resource-manager-templates"></a>Azure Resource Manager-sablonok összehasonlító funkciói

A Resource Manager számos funkciót biztosít a sablonokban való összehasonlításhoz.

* [equals](#equals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)
* [kisebb](#less)
* [lessOrEquals](#lessorequals)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="equals"></a>equals
`equals(arg1, arg2)`

Ellenőrzi, hogy két érték egyenlő-e egymással.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |int, string, array vagy object |Az egyenlőség keresésének első értéke. |
| arg2 |Igen |int, string, array vagy object |A második érték az egyenlőség kereséséhez. |

### <a name="return-value"></a>Vrácená hodnota

**Igaz** értéket ad vissza, ha az értékek egyenlőek; Ellenkező esetben **hamis**.

### <a name="remarks"></a>Megjegyzések

Az Equals függvényt gyakran használják az `condition` elemmel annak teszteléséhez, hogy van-e üzembe helyezett erőforrás.

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

Az alábbi [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/equals.json) különböző típusú értékeket keres az egyenlőséghez. Az összes alapértelmezett érték igaz értéket ad vissza.

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | True |
| checkArrays | Bool | True |
| checkObjects | Bool | True |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json 
```

A következő [példában](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) a sablon [nem](resource-group-template-functions-logical.md#not) **egyenlő**.

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

Az előző példa kimenete a következő:

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json 
```

## <a name="greater"></a>nagyobb
`greater(arg1, arg2)`

Ellenőrzi, hogy az első érték nagyobb-e, mint a második érték.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |int vagy sztring |A nagyobb összehasonlítás első értéke. |
| arg2 |Igen |int vagy sztring |A nagyobb összehasonlítás második értéke. |

### <a name="return-value"></a>Vrácená hodnota

**Igaz** értéket ad vissza, ha az első érték nagyobb, mint a második érték; Ellenkező esetben **hamis**.

### <a name="example"></a>Példa

Az alábbi [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json) ellenőrzi, hogy az egyik érték nagyobb-e, mint a másik.

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| checkInts | Bool | False (Hamis) |
| checkStrings | Bool | True |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json 
```

## <a name="greaterorequals"></a>greaterOrEquals
`greaterOrEquals(arg1, arg2)`

Ellenőrzi, hogy az első érték nagyobb-e vagy egyenlő-e a második értékkel.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |int vagy sztring |A nagyobb vagy egyenlő összehasonlítás első értéke. |
| arg2 |Igen |int vagy sztring |A nagyobb vagy egyenlő összehasonlítás második értéke. |

### <a name="return-value"></a>Vrácená hodnota

**Igaz** értéket ad vissza, ha az első érték nagyobb vagy egyenlő, mint a második érték; Ellenkező esetben **hamis**.

### <a name="example"></a>Példa

Az alábbi [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json) ellenőrzi, hogy az egyik érték nagyobb-e vagy egyenlő-e a másikkal.

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| checkInts | Bool | False (Hamis) |
| checkStrings | Bool | True |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json 
```

## <a name="less"></a>kevesebb
`less(arg1, arg2)`

Ellenőrzi, hogy az első érték kisebb-e a második értéknél.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |int vagy sztring |A kevésbé hasonlítható összehasonlítás első értéke. |
| arg2 |Igen |int vagy sztring |A kisebb összehasonlítás második értéke. |

### <a name="return-value"></a>Vrácená hodnota

**Igaz** értéket ad vissza, ha az első érték kisebb, mint a második érték; Ellenkező esetben **hamis**.

### <a name="example"></a>Példa

Az alábbi [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json) ellenőrzi, hogy az egyik érték kisebb-e, mint a másik.

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | False (Hamis) |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json 
```

## <a name="lessorequals"></a>lessOrEquals
`lessOrEquals(arg1, arg2)`

Ellenőrzi, hogy az első érték kisebb-e vagy egyenlő-e a második értékkel.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |int vagy sztring |A kisebb vagy egyenlő összehasonlítás első értéke. |
| arg2 |Igen |int vagy sztring |A kisebb vagy egyenlő összehasonlítás második értéke. |

### <a name="return-value"></a>Vrácená hodnota

**Igaz** értéket ad vissza, ha az első érték kisebb vagy egyenlő, mint a második érték; Ellenkező esetben **hamis**.

### <a name="example"></a>Példa

Az alábbi [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json) ellenőrzi, hogy az egyik érték kisebb-e vagy egyenlő-e a másikkal.

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | False (Hamis) |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json 
```

## <a name="next-steps"></a>További lépések
* A szakaszok az Azure Resource Manager-sablon ismertetését lásd: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).
* Több sablon egyesíteni, lásd: [kapcsolt sablonok használata az Azure Resource Manager](resource-group-linked-templates.md).
* A megadott számú alkalommal újrafuttathatja egy adott típusú erőforrás létrehozásakor, lásd: [több erőforráspéldány létrehozása az Azure Resource Manager](resource-group-create-multiple.md).
* A létrehozott sablon üzembe helyezésével kapcsolatban lásd: [alkalmazás központi telepítése Azure Resource Manager sablonnal](resource-group-template-deploy.md).

