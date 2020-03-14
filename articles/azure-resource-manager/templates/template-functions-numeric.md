---
title: Sablon függvények – numerikus
description: A Azure Resource Manager-sablonban a számokkal való munkához használandó függvényeket ismerteti.
ms.topic: conceptual
ms.date: 11/08/2017
ms.openlocfilehash: 91aa637701acb278e81b7eb86aa3ae2db15acc28
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273656"
---
# <a name="numeric-functions-for-azure-resource-manager-templates"></a>Azure Resource Manager-sablonok numerikus függvények

A Resource Manager a következő függvényeket biztosítja az egész számokkal való használathoz:

* [hozzáadása](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [Max](#max)
* [min](#min)
* [mod](#mod)
* [mul](#mul)
* [Sub](#sub)

<a id="add" />

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="add"></a>add
`add(operand1, operand2)`

A két megadott egész szám összegét adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- | 
|operand1 |Igen |int |A hozzáadandó első szám. |
|operand2 |Igen |int |A hozzáadandó második szám. |

### <a name="return-value"></a>Vrácená hodnota

Egy egész szám, amely a paraméterek összegét tartalmazza.

### <a name="example"></a>Példa

Az alábbi [sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/add.json) két paramétert hoz létre.

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| addResult | Int | 8 |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json 
```

<a id="copyindex" />

## <a name="copyindex"></a>copyIndex
`copyIndex(loopName, offset)`

Egy iterációs hurok indexét adja vissza. 

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| loopName | Nem | sztring | Az iteráció megszerzéséhez használt hurok neve. |
| offset |Nem |int |A nulla alapú iterációs értékhez hozzáadandó szám. |

### <a name="remarks"></a>Megjegyzések

Ezt a függvényt mindig egy **másolási** objektummal használja a rendszer. Ha nincs megadva érték az **eltoláshoz**, a rendszer az aktuális iterációs értéket adja vissza. Az iteráció értéke nullával kezdődik. Az Ismétlési hurkokat az erőforrások vagy változók definiálásához használhatja.

A **loopName** tulajdonság lehetővé teszi annak megadását, hogy a copyIndex erőforrás-iterációra vagy tulajdonság-iterációra hivatkozik-e. Ha a **loopName**nem ad meg értéket, a rendszer az aktuális erőforrástípus iterációját használja. Adjon meg egy értéket a **loopName** , amikor a tulajdonságot megismétli. 
 
A **copyIndex**használatának teljes leírását lásd: [több erőforrás-példány létrehozása Azure Resource Managerban](copy-resources.md).

Ha egy változó meghatározásakor példát kíván használni a **copyIndex** , tekintse meg a [változókat](template-syntax.md#variables).

### <a name="example"></a>Példa

A következő példa egy másolási ciklust és a névben szereplő index értékét mutatja be. 

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

### <a name="return-value"></a>Vrácená hodnota

Egy egész szám, amely az iteráció aktuális indexét jelöli.

<a id="div" />

## <a name="div"></a>div
`div(operand1, operand2)`

A két megadott egész szám egészének osztását adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| operand1 |Igen |int |A felosztott szám. |
| operand2 |Igen |int |A felosztáshoz használt szám. Nem lehet 0. |

### <a name="return-value"></a>Vrácená hodnota

A osztást jelképező egész szám.

### <a name="example"></a>Példa

Az alábbi [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/div.json) egy paramétert oszt szét egy másik paraméterrel.

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| divResult | Int | 2 |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json 
```

<a id="float" />

## <a name="float"></a>lebegőpontos
`float(arg1)`

Az értéket egy lebegőpontos számra konvertálja. Ezt a függvényt csak akkor használja, ha egyéni paramétereket adunk át egy alkalmazásnak, például egy logikai alkalmazásnak.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |karakterlánc vagy int |A lebegőpontos számra konvertálandó érték. |

### <a name="return-value"></a>Vrácená hodnota
Egy lebegőpontos szám.

### <a name="example"></a>Példa

Az alábbi példa bemutatja, hogyan használható az úszó a paraméterek logikai alkalmazásba való átadásához:

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

Egy egész számra konvertálja a megadott értéket.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| valueToConvert |Igen |karakterlánc vagy int |Az egész számra konvertálandó érték. |

### <a name="return-value"></a>Vrácená hodnota

Az átalakított érték egész szám.

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/int.json) a felhasználó által megadott paraméter értékét egész értékre konvertálja.

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| intResult | Int | 4 |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

<a id="max" />

## <a name="max"></a>max.
`max (arg1)`

A maximális értéket adja vissza egész számok tömbje vagy az egész számok vesszővel tagolt listája alapján.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |egész számok tömbje vagy egész számok vesszővel tagolt listája |A gyűjtemény, amely a maximális értéket kapja. |

### <a name="return-value"></a>Vrácená hodnota

Egy egész szám, amely a gyűjteményből származó maximális értéket jelöli.

### <a name="example"></a>Példa

Az alábbi [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) azt szemlélteti, hogyan használható a Max egy tömbvel és egy egész számokból álló listával:

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

<a id="min" />

## <a name="min"></a>min.
`min (arg1)`

A minimális értéket adja vissza egész számok tömbje vagy az egész számok vesszővel tagolt listája alapján.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |egész számok tömbje vagy egész számok vesszővel tagolt listája |A gyűjtemény a minimális érték beolvasásához. |

### <a name="return-value"></a>Vrácená hodnota

Egy egész szám, amely a gyűjtemény minimális értékét jelöli.

### <a name="example"></a>Példa

Az alábbi [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) azt szemlélteti, hogyan használható a min egy tömbvel és egy egész számokból álló listával:

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

<a id="mod" />

## <a name="mod"></a>mod
`mod(operand1, operand2)`

Az egész szám többit adja vissza a két megadott egész szám használatával.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| operand1 |Igen |int |A felosztott szám. |
| operand2 |Igen |int |A felosztáshoz használt szám nem lehet 0. |

### <a name="return-value"></a>Vrácená hodnota
A maradékot jelölő egész szám.

### <a name="example"></a>Példa

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mod.json) egy másik paraméterrel való osztásának hátralévő részét adja vissza.

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| modResult | Int | 1 |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

<a id="mul" />

## <a name="mul"></a>mul
`mul(operand1, operand2)`

A két megadott egész szám szorzását adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| operand1 |Igen |int |A szorzáshoz használandó első szám. |
| operand2 |Igen |int |A szorzáshoz használt második szám. |

### <a name="return-value"></a>Vrácená hodnota

A szorzást jelölő egész szám.

### <a name="example"></a>Példa

A következő [példában](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mul.json) egy paramétert szorozunk egy másik paraméterrel.

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| mulResult | Int | 15 |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

<a id="sub" />

## <a name="sub"></a>sub
`sub(operand1, operand2)`

A két megadott egész szám kivonását adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| operand1 |Igen |int |A következőből kivont szám. |
| operand2 |Igen |int |A kivonni kívánt szám. |

### <a name="return-value"></a>Vrácená hodnota
A kivonást jelképező egész szám.

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/sub.json) egy paramétert kivonja egy másik paraméterből.

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| aleredmény | Int | 4 |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

## <a name="next-steps"></a>Következő lépések
* Egy Azure Resource Manager sablonban található részekkel kapcsolatos leírást a következő témakörben talál: [Azure Resource Manager sablonok készítése](template-syntax.md).
* Több sablon egyesítéséhez tekintse meg [a csatolt sablonok használata Azure Resource Manager használatával](linked-templates.md)című témakört.
* Ha egy adott típusú erőforrás létrehozásakor meghatározott számú alkalommal szeretné megismételni a problémát, tekintse meg a [Azure Resource Manager erőforrások több példányának létrehozása](copy-resources.md)című témakört.
* A létrehozott sablon üzembe helyezésével kapcsolatban lásd: [alkalmazás központi telepítése Azure Resource Manager sablonnal](deploy-powershell.md).

