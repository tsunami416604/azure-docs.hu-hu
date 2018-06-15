---
title: Az Azure Resource Manager sablonfüggvényei - numerikus |} Microsoft Docs
description: Az Azure Resource Manager-sablonok segítségével számok dolgozni funkcióit ismerteti.
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
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: 4fc17b997c44560199e65edb01d20c6a24e49877
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34360002"
---
# <a name="numeric-functions-for-azure-resource-manager-templates"></a>Az Azure Resource Manager sablonokhoz numerikus funkciók

Erőforrás-kezelő a következő funkciókat nyújt egész számok használata:

* [Hozzáadása](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [max](#max)
* [Perc](#min)
* [MOD](#mod)
* [MUL számú](#mul)
* [sub](#sub)

<a id="add" />

## <a name="add"></a>hozzáadás
`add(operand1, operand2)`

A két megadott egész számok összegét adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- | 
|operand1 |Igen |int |Első számú hozzáadásához. |
|operand2 |Igen |int |Adja hozzá a második szám. |

### <a name="return-value"></a>Visszatérési érték

Egész szám, amely tartalmazza a paraméterek számának összege.

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/add.json) két paramétereket ad.

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

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| addResult | Int | 8 |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json 
```

<a id="copyindex" />

## <a name="copyindex"></a>copyIndex
`copyIndex(loopName, offset)`

Egy iteráció hurok indexét adja vissza. 

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| loopName | Nem | karakterlánc | Neve a ciklus ismétléseinek beolvasásakor. |
| offset |Nem |int |Az a szám, a nulla alapú ismétlési érték hozzáadása. |

### <a name="remarks"></a>Megjegyzések

Ez a funkció mindig használatos a **másolási** objektum. Ha nincs érték megadva, a **eltolás**, az aktuális iterációs értéket adja vissza. Az ismétlési érték nulla kezdődik. Iteráció hurkokat is használhatja, vagy az erőforrások, vagy a változók meghatározásakor.

A **loopName** tulajdonság lehetővé teszi adja meg, hogy copyIndex erőforrás iterációs vagy tulajdonság iterációs hivatkozik. Ha nincs érték megadva, a **loopName**, az aktuális erőforrás-típus iteráció szolgál. Adjon meg egy értéket a **loopName** amikor léptetés tulajdonság alapján. 
 
Teljes leírását az használatának **copyIndex**, lásd: [erőforrások több példánya létrehozása az Azure Resource Manager](resource-group-create-multiple.md).

Segítségével például **copyIndex** vonatkozó változó definiálása látható [változók](resource-group-authoring-templates.md#variables).

### <a name="example"></a>Példa

A következő példa bemutatja a másolási ciklust és az értéket a neve tartalmazza. 

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

### <a name="return-value"></a>Visszatérési érték

Jelző egész számot az iteráció aktuális indexét.

<a id="div" />

## <a name="div"></a>DIV
`div(operand1, operand2)`

A két megadott egész számok egész szám hányadosának adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| operand1 |Igen |int |Az a szám felosztják. |
| operand2 |Igen |int |Az a szám, amellyel osztani. Nem lehet 0. |

### <a name="return-value"></a>Visszatérési érték

Egy osztás jelző egész számot.

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/div.json) osztja a másik paraméterrel egy paramétert.

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

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| divResult | Int | 2 |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json 
```

<a id="float" />

## <a name="float"></a>lebegőpontos
`float(arg1)`

Konvertálja az értéket lebegőpontos számnak. Ez a függvény csak ha egyéni paraméterek átadása egy alkalmazást, például a logikai alkalmazás használja.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |karakterlánc- vagy int |Az érték átalakítása lebegőpontos számnak. |

### <a name="return-value"></a>Visszatérési érték
Lebegőpontos szám.

### <a name="example"></a>Példa

A következő példa bemutatja, hogyan lebegőpontos használandó paraméterek átadása egy logikai alkalmazást:

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

A megadott érték konvertálása egy egész számot.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| valueToConvert |Igen |karakterlánc- vagy int |Az érték egész számra konvertálni. |

### <a name="return-value"></a>Visszatérési érték

Az átalakított érték egész szám.

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/int.json) alakítja át a felhasználó által megadott paraméter értékének egész számra.

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

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| intEredmeny | Int | 4 |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

<a id="max" />

## <a name="max"></a>max.
`max (arg1)`

A maximális érték egész számok tömb vagy egészek vesszővel elválasztott listáját adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |a tömb egész szám vagy egészek vesszővel elválasztott felsorolása |A gyűjteményt, amelyben a legnagyobb érték beolvasása. |

### <a name="return-value"></a>Visszatérési érték

Jelző egész számot a maximális érték a gyűjteményből.

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) max. használata egy tömb és az egész számok listáját jeleníti meg:

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

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

<a id="min" />

## <a name="min"></a>perc
`min (arg1)`

Egy számokból álló tömb vagy egészek vesszővel elválasztott listáját a minimális értékét adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |a tömb egész szám vagy egészek vesszővel elválasztott felsorolása |A gyűjteményt, amelyben a minimális érték beolvasása. |

### <a name="return-value"></a>Visszatérési érték

Jelző egész számot minimális érték a gyűjteményből.

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) min használata egy tömb és az egész számok listáját jeleníti meg:

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

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

<a id="mod" />

## <a name="mod"></a>MOD
`mod(operand1, operand2)`

Használja a két megadott egész szám hányadosának egész a maradékot adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| operand1 |Igen |int |Az a szám felosztják. |
| operand2 |Igen |int |A szám, amellyel osztani, nem lehet 0. |

### <a name="return-value"></a>Visszatérési érték
Egy további jelző egész számot.

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mod.json) egy másik paraméterrel egy paraméter felosztása a maradékot adja vissza.

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

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| modResult | Int | 1 |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

<a id="mul" />

## <a name="mul"></a>MUL számú
`mul(operand1, operand2)`

A két megadott egész számok szorzás adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| operand1 |Igen |int |A szorzási első szám. |
| operand2 |Igen |int |A szorzási második szám. |

### <a name="return-value"></a>Visszatérési érték

A szorzás jelölő egész.

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mul.json) szorozza meg egy másik paraméterrel egy paramétert.

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

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| mulResult | Int | 15 |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

<a id="sub" />

## <a name="sub"></a>Sub
`sub(operand1, operand2)`

A kivonás a két megadott egész számokat adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| operand1 |Igen |int |A szám, amelyet a program levonja az. |
| operand2 |Igen |int |A szám, amelyet a program levonja. |

### <a name="return-value"></a>Visszatérési érték
Egy a kivonásnak jelző egész számot.

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/sub.json) kivonja a másik paraméter egy paramétert.

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

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| subResult | Int | 4 |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

## <a name="next-steps"></a>További lépések
* A szakaszok az Azure Resource Manager-sablon ismertetését lásd: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).
* Több sablon egyesíteni, lásd: [kapcsolt sablonok használata az Azure Resource Manager](resource-group-linked-templates.md).
* Megadott számú alkalommal felépítésének egy adott típusú erőforrás létrehozása esetén lásd: [erőforrások több példánya létrehozása az Azure Resource Manager](resource-group-create-multiple.md).
* A sablon létrehozott központi telepítéséről, olvassa el [Azure Resource Manager-sablon az alkalmazás központi telepítését](resource-group-template-deploy.md).

