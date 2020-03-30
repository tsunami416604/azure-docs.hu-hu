---
title: Sablonfüggvények - numerikus
description: Az Azure Resource Manager-sablonban a számokkal való munka során használandó függvények ismertetése.
ms.topic: conceptual
ms.date: 11/08/2017
ms.openlocfilehash: 2ca5c539036d002b83b8141132a0ebf2530dc6af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156344"
---
# <a name="numeric-functions-for-arm-templates"></a>Numerikus függvények ARM sablonokhoz

Az Erőforrás-kezelő a következő függvényeket biztosítja az egész számokkal való munkához az Azure Resource Manager (ARM) sablonban:

* [Hozzáadása](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [lebegőpontos](#float)
* [Int](#int)
* [Max](#max)
* [Min](#min)
* [Mod](#mod)
* [Mul](#mul)
* [Al](#sub)

<a id="add" />

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="add"></a>add
`add(operand1, operand2)`

A két megadott egész szám összegét adja eredményül.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
|operandsok1 |Igen |int |Az első hozzáadódó szám. |
|operandsok2 |Igen |int |Második szám hozzá. |

### <a name="return-value"></a>Visszatérítési érték

A paraméterek összegét tartalmazó egész szám.

### <a name="example"></a>Példa

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/add.json) két paramétert ad hozzá.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 5,
            "metadata": {
                "description": "First integer to add"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Second integer to add"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "addResult": {
            "type": "int",
            "value": "[add(parameters('first'), parameters('second'))]"
        }
    }
}
```

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| addResult | Int | 8 |

Ha ezt a példasablont az Azure CLI-vel szeretné telepíteni, használja a következőket:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json
```

Ha ezt a példasablont a PowerShell használatával szeretné telepíteni, használja a következőket:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json
```

<a id="copyindex" />

## <a name="copyindex"></a>copyIndex
`copyIndex(loopName, offset)`

Egy iterációs hurok indexét adja eredményül.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| ciklusneve | Nem | sztring | Az iteráció megszerzéséhez szolgáló hurok neve. |
| offset |Nem |int |A nulla alapú iterációs értékhez hozzáadandó szám. |

### <a name="remarks"></a>Megjegyzések

Ez a függvény mindig másolási objektummal van **használva.** Ha nincs megadva érték az **eltoláshoz,** a program az aktuális iterációs értéket adja vissza. Az iterációs érték nullával kezdődik. Az iterációs hurkok erőforrások vagy változók definiálásakor használható.

A **loopName** tulajdonság lehetővé teszi annak megadását, hogy a copyIndex erőforrás-iterációra vagy tulajdonságiterációra hivatkozik-e. Ha nincs megadva érték a **loopName értékhez,** a rendszer az aktuális erőforrástípus-iterációt használja. Adjon meg egy értéket a **loopName** értékhez tulajdonság ismétlésekénekesetén.

A **copyIndex**használatának teljes leírását az [Erőforrások több példányának létrehozása az Azure Resource Manager ben](copy-resources.md)című témakörben találja.

A **copyIndex** változók definiálásakor a Változók című [témakörben](template-syntax.md#variables)látható.

### <a name="example"></a>Példa

A következő példa egy másolási hurkot és a névben szereplő indexértéket mutatja be.

```json
"resources": [
  {
    "name": "[concat('examplecopy-', copyIndex())]",
    "type": "Microsoft.Web/sites",
    "copy": {
      "name": "websitescopy",
      "count": "[parameters('count')]"
    },
    ...
  }
]
```

### <a name="return-value"></a>Visszatérítési érték

Az iteráció aktuális indexét jelölő egész szám.

<a id="div" />

## <a name="div"></a>div
`div(operand1, operand2)`

A két megadott egész szám egész osztását adja eredményül.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| operandsok1 |Igen |int |A felosztott szám. |
| operandsok2 |Igen |int |Az osztáshoz használt szám. Nem lehet 0. |

### <a name="return-value"></a>Visszatérítési érték

A divíziót jelképező egész szám.

### <a name="example"></a>Példa

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/div.json) elosztja az egyik paramétert egy másik paraméterrel.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 8,
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "divResult": {
            "type": "int",
            "value": "[div(parameters('first'), parameters('second'))]"
        }
    }
}
```

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| divResult | Int | 2 |

Ha ezt a példasablont az Azure CLI-vel szeretné telepíteni, használja a következőket:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json
```

Ha ezt a példasablont a PowerShell használatával szeretné telepíteni, használja a következőket:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json
```

<a id="float" />

## <a name="float"></a>lebegőpontos
`float(arg1)`

Az értéket lebegőpontos számmá alakítja. Ezt a funkciót csak akkor használja, ha egyéni paramétereket ad át egy alkalmazásnak, például egy logikai alkalmazásnak.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |karakterlánc vagy int |Lebegőpontos számmá konvertálandó érték. |

### <a name="return-value"></a>Visszatérítési érték
Lebegőpontos szám.

### <a name="example"></a>Példa

A következő példa bemutatja, hogyan használhatja a lebegőlebegő paraméterek et egy logikai alkalmazásnak:

```json
{
    "type": "Microsoft.Logic/workflows",
    "properties": {
        ...
        "parameters": {
            "custom1": {
                "value": "[float('3.0')]"
            },
            "custom2": {
                "value": "[float(3)]"
            },
```

<a id="int" />

## <a name="int"></a>int
`int(valueToConvert)`

A megadott értéket egész számmá alakítja.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| értékToConvert |Igen |karakterlánc vagy int |Egész számmá konvertálandó érték. |

### <a name="return-value"></a>Visszatérítési érték

A konvertált érték egész száma.

### <a name="example"></a>Példa

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/int.json) a felhasználó által megadott paraméterértéket egész számmá alakítja.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToConvert": {
            "type": "string",
            "defaultValue": "4"
        }
    },
    "resources": [
    ],
    "outputs": {
        "intResult": {
            "type": "int",
            "value": "[int(parameters('stringToConvert'))]"
        }
    }
}
```

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| intResult | Int | 4 |

Ha ezt a példasablont az Azure CLI-vel szeretné telepíteni, használja a következőket:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

Ha ezt a példasablont a PowerShell használatával szeretné telepíteni, használja a következőket:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

<a id="max" />

## <a name="max"></a>Max
`max (arg1)`

Az egész számok tömbjéből vagy egy vesszővel tagolt egész számból álló tömbből származó maximális értéket adja eredményül.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |egész számok tömbje, vagy vesszővel tagolt egész számok listája |A gyűjtemény a maximális érték bekerüléséhez. |

### <a name="return-value"></a>Visszatérítési érték

Egész szám, amely a gyűjtemény maximális értékét jelöli.

### <a name="example"></a>Példa

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) bemutatja, hogyan használható a max.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[max(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[max(0,3,2,5,4)]"
        }
    }
}
```

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| tömbkimenet | Int | 5 |
| intOutput | Int | 5 |

Ha ezt a példasablont az Azure CLI-vel szeretné telepíteni, használja a következőket:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

Ha ezt a példasablont a PowerShell használatával szeretné telepíteni, használja a következőket:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

<a id="min" />

## <a name="min"></a>p
`min (arg1)`

A minimális értéket egész számokból vagy vesszővel tagolt egész számokból származó értéket adja eredményül.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |egész számok tömbje, vagy vesszővel tagolt egész számok listája |A gyűjtemény a minimális érték begyűjtéséhez. |

### <a name="return-value"></a>Visszatérítési érték

A gyűjtemény minimális értékét reprezentatot képviselő egész szám.

### <a name="example"></a>Példa

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) bemutatja, hogyan használható a min tömbhöz és egész számok listájához:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[min(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[min(0,3,2,5,4)]"
        }
    }
}
```

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| tömbkimenet | Int | 0 |
| intOutput | Int | 0 |

Ha ezt a példasablont az Azure CLI-vel szeretné telepíteni, használja a következőket:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

Ha ezt a példasablont a PowerShell használatával szeretné telepíteni, használja a következőket:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

<a id="mod" />

## <a name="mod"></a>Mod
`mod(operand1, operand2)`

Az egész osztás fennmaradó részét adja eredményül a két megadott egész szám használatával.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| operandsok1 |Igen |int |A felosztott szám. |
| operandsok2 |Igen |int |Az osztáshoz használt szám, nem lehet 0. |

### <a name="return-value"></a>Visszatérítési érték
A maradékot jelölő egész szám.

### <a name="example"></a>Példa

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mod.json) az egyik paraméter egy másik paraméterrel való felosztásának fennmaradó részét adja vissza.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 7,
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "modResult": {
            "type": "int",
            "value": "[mod(parameters('first'), parameters('second'))]"
        }
    }
}
```

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| modResult | Int | 1 |

Ha ezt a példasablont az Azure CLI-vel szeretné telepíteni, használja a következőket:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

Ha ezt a példasablont a PowerShell használatával szeretné telepíteni, használja a következőket:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

<a id="mul" />

## <a name="mul"></a>Mul
`mul(operand1, operand2)`

A két megadott egész szám szorzását adja eredményül.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| operandsok1 |Igen |int |Az első szorzandó szám. |
| operandsok2 |Igen |int |Második szorzandó szám. |

### <a name="return-value"></a>Visszatérítési érték

A szorzást jelképező egész szám.

### <a name="example"></a>Példa

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mul.json) megszorozza az egyik paramétert egy másik paraméterrel.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 5,
            "metadata": {
                "description": "First integer to multiply"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Second integer to multiply"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "mulResult": {
            "type": "int",
            "value": "[mul(parameters('first'), parameters('second'))]"
        }
    }
}
```

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| mulResult | Int | 15 |

Ha ezt a példasablont az Azure CLI-vel szeretné telepíteni, használja a következőket:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

Ha ezt a példasablont a PowerShell használatával szeretné telepíteni, használja a következőket:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

<a id="sub" />

## <a name="sub"></a>Al
`sub(operand1, operand2)`

A két megadott egész szám kivonását adja eredményül.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| operandsok1 |Igen |int |A kivont szám. |
| operandsok2 |Igen |int |A kivont szám. |

### <a name="return-value"></a>Visszatérítési érték
A kivonást jelképező egész szám.

### <a name="example"></a>Példa

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/sub.json) kivon egy paramétert egy másik paraméterből.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 7,
            "metadata": {
                "description": "Integer subtracted from"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer to subtract"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "subResult": {
            "type": "int",
            "value": "[sub(parameters('first'), parameters('second'))]"
        }
    }
}
```

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| aleredmény | Int | 4 |

Ha ezt a példasablont az Azure CLI-vel szeretné telepíteni, használja a következőket:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

Ha ezt a példasablont a PowerShell használatával szeretné telepíteni, használja a következőket:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

## <a name="next-steps"></a>További lépések
* Az Azure Resource Manager-sablon szakaszainak leírását az [Azure Resource Manager-sablonok készítése című témakörben találja.](template-syntax.md)
* Több sablon egyesítéséhez olvassa el [a Csatolt sablonok használata az Azure Resource Manager rel.](linked-templates.md)
* Ha egy erőforrástípus létrehozásakor meghatározott számú alkalommal szeretne meghaladni, olvassa [el az Erőforrások több példányának létrehozása az Azure Resource Manager ben című témakört.](copy-resources.md)
* A létrehozott sablon központi telepítéséről az Alkalmazás üzembe helyezése az [Azure Resource Manager sablonnal című témakörben](deploy-powershell.md)olvashat.

