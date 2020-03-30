---
title: Sablonfüggvények - összehasonlítás
description: Az Azure Resource Manager-sablonban az értékek összehasonlításához használandó függvények ismertetése.
ms.topic: conceptual
ms.date: 09/05/2017
ms.openlocfilehash: 42009e8543e307f2d3e4643ddaa79f492f9bdfee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156361"
---
# <a name="comparison-functions-for-arm-templates"></a>Arm sablonok összehasonlító funkciói

A Resource Manager számos funkciót biztosít az Azure Resource Manager (ARM) sablonjaiban végzett összehasonlításhoz.

* [egyenlő](#equals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)
* [less](#less)
* [lessOrEquals](#lessorequals)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="equals"></a>egyenlő
`equals(arg1, arg2)`

Ellenőrzi, hogy két érték egyezik-e egymással.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |int, string, array vagy object |Az első érték, amely ellenőrzi az egyenlőséget. |
| arg2 |Igen |int, string, array vagy object |A második érték, hogy ellenőrizze az egyenlőség. |

### <a name="return-value"></a>Visszatérítési érték

**Igaz** értéket ad vissza, ha az értékek egyenlőek; ellenkező **esetben, Hamis**.

### <a name="remarks"></a>Megjegyzések

Az equals függvényt gyakran `condition` használják az elemmel annak tesztelésére, hogy egy erőforrás telepítve van-e.

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

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/equals.json) különböző típusú értékeket ellenőriz az egyenlőség szempontjából. Az összes alapértelmezett érték igaz értéket adja vissza.

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

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| checkInts | Logikai | True (Igaz) |
| ellenőrző karakterláncok | Logikai | True (Igaz) |
| checkArrays | Logikai | True (Igaz) |
| checkObjects (objektumok) ellenőrzése | Logikai | True (Igaz) |

Ha ezt a példasablont az Azure CLI-vel szeretné telepíteni, használja a következőket:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json
```

Ha ezt a példasablont a PowerShell használatával szeretné telepíteni, használja a következőket:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json
```

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) [nem](template-functions-logical.md#not) **egyenlő**.

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

| Név | Típus | Érték |
| ---- | ---- | ----- |
| checkNotEquals | Logikai | True (Igaz) |

Ha ezt a példasablont az Azure CLI-vel szeretné telepíteni, használja a következőket:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

Ha ezt a példasablont a PowerShell használatával szeretné telepíteni, használja a következőket:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

## <a name="greater"></a>greater
`greater(arg1, arg2)`

Ellenőrzi, hogy az első érték nagyobb-e a második értéknél.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |int vagy string |A nagyobb összehasonlítás első értéke. |
| arg2 |Igen |int vagy string |A második érték a nagyobb összehasonlításhoz. |

### <a name="return-value"></a>Visszatérítési érték

**Igaz** értéket ad vissza, ha az első érték nagyobb, mint a második érték; ellenkező **esetben, Hamis**.

### <a name="example"></a>Példa

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json) ellenőrzi, hogy az egyik érték nagyobb-e a másiknál.

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

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| checkInts | Logikai | False (Hamis) |
| ellenőrző karakterláncok | Logikai | True (Igaz) |

Ha ezt a példasablont az Azure CLI-vel szeretné telepíteni, használja a következőket:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json
```

Ha ezt a példasablont a PowerShell használatával szeretné telepíteni, használja a következőket:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json
```

## <a name="greaterorequals"></a>greaterOrEquals
`greaterOrEquals(arg1, arg2)`

Ellenőrzi, hogy az első érték nagyobb vagy egyenlő-e a második értékkel.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |int vagy string |A nagyobb vagy egyenlő összehasonlítás első értéke. |
| arg2 |Igen |int vagy string |A nagyobb vagy egyenlő összehasonlítás második értéke. |

### <a name="return-value"></a>Visszatérítési érték

**Igaz** értéket ad vissza, ha az első érték nagyobb vagy egyenlő a második értékkel; ellenkező **esetben, Hamis**.

### <a name="example"></a>Példa

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json) azt ellenőrzi, hogy az egyik érték nagyobb vagy egyenlő-e a másikkal.

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

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| checkInts | Logikai | False (Hamis) |
| ellenőrző karakterláncok | Logikai | True (Igaz) |

Ha ezt a példasablont az Azure CLI-vel szeretné telepíteni, használja a következőket:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json
```

Ha ezt a példasablont a PowerShell használatával szeretné telepíteni, használja a következőket:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json
```

## <a name="less"></a>less
`less(arg1, arg2)`

Ellenőrzi, hogy az első érték kisebb-e, mint a második érték.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |int vagy string |Az első érték a kevésbé összehasonlítás. |
| arg2 |Igen |int vagy string |A második érték a kevésbé összehasonlítás. |

### <a name="return-value"></a>Visszatérítési érték

**Igaz** értéket ad vissza, ha az első érték kisebb, mint a második érték; ellenkező **esetben, Hamis**.

### <a name="example"></a>Példa

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json) ellenőrzi, hogy az egyik érték kisebb-e a másiknál.

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

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| checkInts | Logikai | True (Igaz) |
| ellenőrző karakterláncok | Logikai | False (Hamis) |

Ha ezt a példasablont az Azure CLI-vel szeretné telepíteni, használja a következőket:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json
```

Ha ezt a példasablont a PowerShell használatával szeretné telepíteni, használja a következőket:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json
```

## <a name="lessorequals"></a>lessOrEquals
`lessOrEquals(arg1, arg2)`

Ellenőrzi, hogy az első érték kisebb vagy egyenlő-e a második értékkel.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |int vagy string |A kisebb vagy egyenlő összehasonlítás első értéke. |
| arg2 |Igen |int vagy string |A kisebb vagy egyenlő összehasonlítás második értéke. |

### <a name="return-value"></a>Visszatérítési érték

**Igaz** értéket ad vissza, ha az első érték kisebb vagy egyenlő a második értékkel; ellenkező **esetben, Hamis**.

### <a name="example"></a>Példa

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json) azt ellenőrzi, hogy az egyik érték kisebb vagy egyenlő-e a másikkal.

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

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| checkInts | Logikai | True (Igaz) |
| ellenőrző karakterláncok | Logikai | False (Hamis) |

Ha ezt a példasablont az Azure CLI-vel szeretné telepíteni, használja a következőket:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json
```

Ha ezt a példasablont a PowerShell használatával szeretné telepíteni, használja a következőket:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json
```

## <a name="next-steps"></a>További lépések
* Az Azure Resource Manager-sablon szakaszainak leírását az [Azure Resource Manager-sablonok készítése című témakörben találja.](template-syntax.md)
* Több sablon egyesítéséhez olvassa el [a Csatolt sablonok használata az Azure Resource Manager rel.](linked-templates.md)
* Ha egy erőforrástípus létrehozásakor meghatározott számú alkalommal szeretne meghaladni, olvassa [el az Erőforrások több példányának létrehozása az Azure Resource Manager ben című témakört.](copy-resources.md)
* A létrehozott sablon központi telepítéséről az Alkalmazás üzembe helyezése az [Azure Resource Manager sablonnal című témakörben](deploy-powershell.md)olvashat.

