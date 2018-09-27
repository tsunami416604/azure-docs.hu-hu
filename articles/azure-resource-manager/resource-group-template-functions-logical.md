---
title: Az Azure Resource Manager-sablonfüggvények – logikai |} A Microsoft Docs
description: A functions az Azure Resource Manager-sablon használatával határozza meg a logikai értékeket ismerteti.
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
ms.date: 09/24/2018
ms.author: tomfitz
ms.openlocfilehash: 8745519f1a0fdda7a5feb6ffb3f61e5250bb260a
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47164787"
---
# <a name="logical-functions-for-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonok logikai függvények

Resource Manager összehasonlítások végzett a sablonok számos funkciót biztosít.

* [és](#and)
* [bool](#bool)
* [if](#if)
* [not](#not)
* [vagy](#or)

## <a name="and"></a>és
`and(arg1, arg2)`

Ellenőrzi, hogy mindkét paraméter értéket Igaz.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |logikai |Ellenőrizze, hogy az első értéket e igaz. |
| Arg2 |Igen |logikai |A második érték, ellenőrizze, hogy igaz. |

### <a name="return-value"></a>Vrácená hodnota

Értéket ad vissza **igaz** Ha mindkét érték igaz; ellenkező esetben **hamis**.

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) logikai függvények használatát ismerteti.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

Az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| andExampleOutput | Logikai | False (Hamis) |
| orExampleOutput | Logikai | True (Igaz) |
| notExampleOutput | Logikai | False (Hamis) |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/andornot.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/andornot.json
```

## <a name="bool"></a>Logikai
`bool(arg1)`

A paraméter alakítja át egy logikai érték.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |karakterlánc- vagy int |Az érték egy logikai típusra konvertál. |

### <a name="return-value"></a>Vrácená hodnota
Az átalakított érték logikai érték.

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) bool használata egy string vagy integer jeleníti meg.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "trueString": {
            "value": "[bool('true')]",
            "type" : "bool"
        },
        "falseString": {
            "value": "[bool('false')]",
            "type" : "bool"
        },
        "trueInt": {
            "value": "[bool(1)]",
            "type" : "bool"
        },
        "falseInt": {
            "value": "[bool(0)]",
            "type" : "bool"
        }
    }
}
```

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| trueString | Logikai | True (Igaz) |
| falseString | Logikai | False (Hamis) |
| trueInt | Logikai | True (Igaz) |
| falseInt | Logikai | False (Hamis) |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/bool.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/bool.json
```

## <a name="if"></a>Ha
`if(condition, trueValue, falseValue)`

Hogy értéket adja vissza egy feltétele igaz vagy hamis.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| feltétel |Igen |logikai |Ellenőrizze, hogy ez igaz a érték. |
| trueValue |Igen | karakterlánc, int, objektumot vagy tömböt |A visszatérési érték, amikor a feltétel teljesül. |
| falseValue |Igen | karakterlánc, int, objektumot vagy tömböt |A visszatérési érték, amikor a feltétel nem teljesül. |

### <a name="return-value"></a>Vrácená hodnota

Második paraméterként adja vissza, amikor az első paraméter **igaz**; ellenkező esetben, harmadik paraméterként adja vissza.

### <a name="remarks"></a>Megjegyzések

Ez a funkció segítségével feltételesen beállítása az erőforrás-tulajdonság. A következő példában nem egy teljes sablonunk, de feltételesen állítja a rendelkezésre állási csoporthoz tartozó fontos részeket jeleníti meg.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "availabilitySet": {
            "type": "string",
            "allowedValues": [
                "yes",
                "no"
            ]
        }
    },
    "variables": {
        ...
        "availabilitySetName": "availabilitySet1",
        "availabilitySet": {
            "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
        }
    },
    "resources": [
        {
            "condition": "[equals(parameters('availabilitySet'),'yes')]",
            "type": "Microsoft.Compute/availabilitySets",
            "name": "[variables('availabilitySetName')]",
            ...
        },
        {
            "apiVersion": "2016-03-30",
            "type": "Microsoft.Compute/virtualMachines",
            "properties": {
                "availabilitySet": "[if(equals(parameters('availabilitySet'),'yes'), variables('availabilitySet'), json('null'))]",
                ...
            }
        },
        ...
    ],
    ...
}
```

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) bemutatja, hogyan használható a `if` függvény.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "yesOutput": {
            "type": "string",
            "value": "[if(equals('a', 'a'), 'yes', 'no')]"
        },
        "noOutput": {
            "type": "string",
            "value": "[if(equals('a', 'b'), 'yes', 'no')]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[if(equals('a', 'a'), json('{\"test\": \"value1\"}'), json('null'))]"
        }
    }
}
```

Az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| yesOutput | Sztring | igen |
| noOutput | Sztring | nem |
| objectOutput | Objektum | {"teszt": "érték1"} |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/if.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/if.json
```

## <a name="not"></a>nem
`not(arg1)`

Logikai érték a ellentétes értékké alakítja.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |logikai |Az átalakítandó érték. |

### <a name="return-value"></a>Vrácená hodnota

Értéket ad vissza **igaz** paraméter esetén **hamis**. Értéket ad vissza **hamis** paraméter esetén **igaz**.

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) logikai függvények használatát ismerteti.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

Az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| andExampleOutput | Logikai | False (Hamis) |
| orExampleOutput | Logikai | True (Igaz) |
| notExampleOutput | Logikai | False (Hamis) |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/andornot.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/andornot.json
```

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) használ **nem** a [egyenlő](resource-group-template-functions-comparison.md#equals).

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
```

Az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| checkNotEquals | Logikai | True (Igaz) |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

## <a name="or"></a>vagy
`or(arg1, arg2)`

Ellenőrzi, hogy mindkét paraméter értéke igaz.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |logikai |Ellenőrizze, hogy az első értéket e igaz. |
| Arg2 |Igen |logikai |A második érték, ellenőrizze, hogy igaz. |

### <a name="return-value"></a>Vrácená hodnota

Értéket ad vissza **igaz** Ha bármelyik érték igaz; ellenkező esetben **hamis**.

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) logikai függvények használatát ismerteti.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

Az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| andExampleOutput | Logikai | False (Hamis) |
| orExampleOutput | Logikai | True (Igaz) |
| notExampleOutput | Logikai | False (Hamis) |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/andornot.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/andornot.json
```

## <a name="next-steps"></a>További lépések
* A szakaszok az Azure Resource Manager-sablon ismertetését lásd: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).
* Több sablon egyesíteni, lásd: [kapcsolt sablonok használata az Azure Resource Manager](resource-group-linked-templates.md).
* A megadott számú alkalommal újrafuttathatja egy adott típusú erőforrás létrehozásakor, lásd: [több erőforráspéldány létrehozása az Azure Resource Manager](resource-group-create-multiple.md).
* Ellenőrizze, hogyan helyezheti üzembe a létrehozott sablont, tekintse meg a [alkalmazás üzembe helyezése Azure Resource Manager-sablonnal](resource-group-template-deploy.md).

