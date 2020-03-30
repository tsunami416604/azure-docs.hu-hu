---
title: Sablonfüggvények - tömbök és objektumok
description: Ismerteti az Azure Resource Manager-sablonban a tömbök és objektumok használatához használandó függvényeket.
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: 0b4bb80f6d7a7cc20a8b2dcc71e890f2ada7c5be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156375"
---
# <a name="array-and-object-functions-for-arm-templates"></a>Tömb- és objektumfüggvények ARM-sablonokhoz

A Resource Manager számos funkciót biztosít az Azure Resource Manager (ARM) sablontömbök és objektumok használatával való munkához.

* [tömb](#array)
* [összeolvad](#coalesce)
* [Concat](#concat)
* [Tartalmaz](#contains)
* [createArray](#createarray)
* [Üres](#empty)
* [Első](#first)
* [Kereszteződés](#intersection)
* [Json](#json)
* [Utolsó](#last)
* [Hossza](#length)
* [Max](#max)
* [Min](#min)
* [Tartomány](#range)
* [Ugrál](#skip)
* [venni](#take)
* [Unió](#union)

Ha egy értékkel határolt karakterlánc-értékektömbjét szeretné leválasztani, olvassa el a [Felosztás](template-functions-string.md#split).

## <a name="array"></a>tömb

`array(convertToArray)`

Az értéket tömbté alakítja.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| convertToArray |Igen |int, string, array vagy object |Tömbté konvertálandó érték. |

### <a name="return-value"></a>Visszatérítési érték

Egy tömb.

### <a name="example"></a>Példa

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/array.json) bemutatja, hogyan használhatja a tömbfüggvényt különböző típusokkal.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "intToConvert": {
            "type": "int",
            "defaultValue": 1
        },
        "stringToConvert": {
            "type": "string",
            "defaultValue": "efgh"
        },
        "objectToConvert": {
            "type": "object",
            "defaultValue": {"a": "b", "c": "d"}
        }
    },
    "resources": [
    ],
    "outputs": {
        "intOutput": {
            "type": "array",
            "value": "[array(parameters('intToConvert'))]"
        },
        "stringOutput": {
            "type": "array",
            "value": "[array(parameters('stringToConvert'))]"
        },
        "objectOutput": {
            "type": "array",
            "value": "[array(parameters('objectToConvert'))]"
        }
    }
}
```

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| intOutput | Tömb | [1] |
| karakterlánckimenet | Tömb | ["efgh"] |
| objectOutput | Tömb | [{"a": "b", "c": "d"}] |

Ha ezt a példasablont az Azure CLI-vel szeretné telepíteni, használja a következőket:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/array.json
```

Ha ezt a példasablont a PowerShell használatával szeretné telepíteni, használja a következőket:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/array.json
```

## <a name="coalesce"></a>összeolvad

`coalesce(arg1, arg2, arg3, ...)`

Az első nem null értéket adja vissza a paraméterekből. Az üres karakterláncok, üres tömbök és üres objektumok nem null értékűek.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |int, string, array vagy object |Null tesztelső értéke. |
| további args |Nem |int, string, array vagy object |Null tesztet további értékek. |

### <a name="return-value"></a>Visszatérítési érték

Az első nem null paraméterek értéke, amelyek lehetnek karakterláncok, int, tömb vagy objektumok. Null, ha az összes paraméter null.

### <a name="example"></a>Példa

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/coalesce.json) az egyesítés különböző felhasználási adatainak kimenetét mutatja be.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "objectToTest": {
            "type": "object",
            "defaultValue": {
                "null1": null,
                "null2": null,
                "string": "default",
                "int": 1,
                "object": {"first": "default"},
                "array": [1]
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').string)]"
        },
        "intOutput": {
            "type": "int",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').int)]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').object)]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').array)]"
        },
        "emptyOutput": {
            "type": "bool",
            "value": "[empty(coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2))]"
        }
    }
}
```

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| karakterlánckimenet | Sztring | alapértelmezett |
| intOutput | Int | 1 |
| objectOutput | Objektum | {"first": "alapértelmezett"} |
| tömbkimenet | Tömb | [1] |
| üreskimenet | Logikai | True (Igaz) |

Ha ezt a példasablont az Azure CLI-vel szeretné telepíteni, használja a következőket:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/coalesce.json
```

Ha ezt a példasablont a PowerShell használatával szeretné telepíteni, használja a következőket:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/coalesce.json
```

## <a name="concat"></a>Concat

`concat(arg1, arg2, arg3, ...)`

Több tömböt egyesít, és az összefont tömböt adja vissza, vagy több karakterláncértéket egyesít, és az összefont karakterláncot adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |tömb vagy karakterlánc |Az összefűzés első tömbje vagy karakterlánca. |
| további argumentumok |Nem |tömb vagy karakterlánc |További tömbök vagy karakterláncok egymás után egymás után. |

Ez a függvény tetszőleges számú argumentumot vehet fel, és elfogadhatja a paraméterek karakterláncait vagy tömbjeit. Azonban nem adhat meg tömböket és karakterláncokat a paraméterekhez. A tömbök csak más tömbökkel vannak összefűzve.

### <a name="return-value"></a>Visszatérítési érték

Összefektett értékek karakterlánca vagy tömbje.

### <a name="example"></a>Példa

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) bemutatja, hogyan kombinálható két tömb.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstArray": {
            "type": "array",
            "defaultValue": [
                "1-1",
                "1-2",
                "1-3"
            ]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": [
                "2-1",
                "2-2",
                "2-3"
            ]
        }
    },
    "resources": [
    ],
    "outputs": {
        "return": {
            "type": "array",
            "value": "[concat(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| Vissza | Tömb | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

Ha ezt a példasablont az Azure CLI-vel szeretné telepíteni, használja a következőket:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

Ha ezt a példasablont a PowerShell használatával szeretné telepíteni, használja a következőket:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) bemutatja, hogyan egyesítheti a két karakterlánc-értéket, és hogyan ad vissza egy összefont karakterláncot.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "prefix": {
            "type": "string",
            "defaultValue": "prefix"
        }
    },
    "resources": [],
    "outputs": {
        "concatOutput": {
            "value": "[concat(parameters('prefix'), '-', uniqueString(resourceGroup().id))]",
            "type" : "string"
        }
    }
}
```

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| concatOutput | Sztring | előtag-5yj4yjf5mbg72 |

Ha ezt a példasablont az Azure CLI-vel szeretné telepíteni, használja a következőket:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

Ha ezt a példasablont a PowerShell használatával szeretné telepíteni, használja a következőket:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

## <a name="contains"></a>tartalmazza a következőt:

`contains(container, itemToFind)`

Ellenőrzi, hogy egy tömb tartalmaz-e értéket, objektum tartalmaz-e kulcsot, vagy egy karakterlánc részkarakterláncot. A karakterlánc-összehasonlítás ban a kis- és nagybetűk et is figyelembe kell. Ha azonban egy objektum kulcstartalmat tesztel, az összehasonlítás nem imitot jelent.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| tároló |Igen |tömb, objektum vagy karakterlánc |A megkeresandó értéket tartalmazó érték. |
| itemToFind között |Igen |karakterlánc vagy int |A megtalálandó érték. |

### <a name="return-value"></a>Visszatérítési érték

**Igaz,** ha az elem megtalálható; ellenkező **esetben, Hamis**.

### <a name="example"></a>Példa

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) bemutatja, hogyan használható a különböző típusú tartalmazók használata:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "OneTwoThree"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringTrue": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'e')]"
        },
        "stringFalse": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'z')]"
        },
        "objectTrue": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'one')]"
        },
        "objectFalse": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'a')]"
        },
        "arrayTrue": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'three')]"
        },
        "arrayFalse": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'four')]"
        }
    }
}
```

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| karakterláncIgaz | Logikai | True (Igaz) |
| karakterláncFalse | Logikai | False (Hamis) |
| objectTrue (objektumIgaz) | Logikai | True (Igaz) |
| objectFalse | Logikai | False (Hamis) |
| tömbIgaz | Logikai | True (Igaz) |
| tömbFalse | Logikai | False (Hamis) |

Ha ezt a példasablont az Azure CLI-vel szeretné telepíteni, használja a következőket:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

Ha ezt a példasablont a PowerShell használatával szeretné telepíteni, használja a következőket:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

## <a name="createarray"></a>létrehozás

`createArray (arg1, arg2, arg3, ...)`

Tömböt hoz létre a paraméterekből.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |Karakterlánc, egész, tömb vagy objektum |A tömb első értéke. |
| további argumentumok |Nem |Karakterlánc, egész, tömb vagy objektum |További értékek a tömbben. |

### <a name="return-value"></a>Visszatérítési érték

Egy tömb.

### <a name="example"></a>Példa

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/createarray.json) bemutatja, hogyan használható a createArray különböző típusokkal:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringArray": {
            "type": "array",
            "value": "[createArray('a', 'b', 'c')]"
        },
        "intArray": {
            "type": "array",
            "value": "[createArray(1, 2, 3)]"
        },
        "objectArray": {
            "type": "array",
            "value": "[createArray(parameters('objectToTest'))]"
        },
        "arrayArray": {
            "type": "array",
            "value": "[createArray(parameters('arrayToTest'))]"
        }
    }
}
```

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| karakterlánctömb | Tömb | ["a", "b", "c"] |
| intArray | Tömb | [1, 2, 3] |
| objectArray | Tömb | [{"egy": "a", "kettő": "b", "három": "c"}] |
| tömbtömb | Tömb | ["egy", "kettő", "három"]] |

Ha ezt a példasablont az Azure CLI-vel szeretné telepíteni, használja a következőket:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/createarray.json
```

Ha ezt a példasablont a PowerShell használatával szeretné telepíteni, használja a következőket:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/createarray.json
```

## <a name="empty"></a>üres

`empty(itemToTest)`

Azt határozza meg, hogy egy tömb, objektum vagy karakterlánc üres-e.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| itemToTest |Igen |tömb, objektum vagy karakterlánc |Annak ellenőrzése, hogy üres-e az érték. |

### <a name="return-value"></a>Visszatérítési érték

**Igaz** értéket ad vissza, ha az érték üres; ellenkező **esetben, Hamis**.

### <a name="example"></a>Példa

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) ellenőrzi, hogy egy tömb, objektum és karakterlánc üres-e.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": []
        },
        "testObject": {
            "type": "object",
            "defaultValue": {}
        },
        "testString": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testArray'))]"
        },
        "objectEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testObject'))]"
        },
        "stringEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testString'))]"
        }
    }
}
```

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| arrayEmpty | Logikai | True (Igaz) |
| objectEmpty | Logikai | True (Igaz) |
| karakterláncÜres | Logikai | True (Igaz) |

Ha ezt a példasablont az Azure CLI-vel szeretné telepíteni, használja a következőket:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

Ha ezt a példasablont a PowerShell használatával szeretné telepíteni, használja a következőket:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

## <a name="first"></a>Első

`first(arg1)`

A tömb első elemét vagy a karakterlánc első karakterét adja eredményül.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |tömb vagy karakterlánc |Az első elem vagy karakter beolvasásához. |

### <a name="return-value"></a>Visszatérítési érték

A tömb első elemének vagy a karakterlánc első karakterének a típusa (karakterlánc, int, tömb vagy objektum).

### <a name="example"></a>Példa

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) bemutatja, hogyan használható az első függvény tömbbel és karakterlánccal.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[first(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[first('One Two Three')]"
        }
    }
}
```

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| tömbkimenet | Sztring | egy |
| karakterlánckimenet | Sztring | O |

Ha ezt a példasablont az Azure CLI-vel szeretné telepíteni, használja a következőket:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

Ha ezt a példasablont a PowerShell használatával szeretné telepíteni, használja a következőket:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

## <a name="intersection"></a>Kereszteződés

`intersection(arg1, arg2, arg3, ...)`

Egyetlen tömböt vagy objektumot ad eredményül a paraméterek közös elemeivel.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |tömb vagy objektum |A közös elemek keresésére használt első érték. |
| arg2 |Igen |tömb vagy objektum |A közös elemek kereséséhez használandó második érték. |
| további argumentumok |Nem |tömb vagy objektum |A közös elemek kereséséhez használandó további értékek. |

### <a name="return-value"></a>Visszatérítési érték

Tömb vagy objektum a közös elemekkel.

### <a name="example"></a>Példa

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/intersection.json) bemutatja, hogyan használható a metszéspont tömbökkel és objektumokkal:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "z", "three": "c"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "objectOutput": {
            "type": "object",
            "value": "[intersection(parameters('firstObject'), parameters('secondObject'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[intersection(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| objectOutput | Objektum | {"egy": "a", "három": "c"} |
| tömbkimenet | Tömb | ["kettő", "három"] |

Ha ezt a példasablont az Azure CLI-vel szeretné telepíteni, használja a következőket:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/intersection.json
```

Ha ezt a példasablont a PowerShell használatával szeretné telepíteni, használja a következőket:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/intersection.json
```

## <a name="json"></a>json

`json(arg1)`

JSON-objektumot ad eredményül.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |sztring |A JSON-ra konvertálandó érték. |

### <a name="return-value"></a>Visszatérítési érték

A json-objektum a megadott karakterláncból, vagy egy üres objektum, ha **null** érték van megadva.

### <a name="remarks"></a>Megjegyzések

Ha paraméterértéket vagy változót kell megadnia a JSON-objektumban, a [concat](template-functions-string.md#concat) függvénnyel hozza létre a függvénynek átadott karakterláncot.

### <a name="example"></a>Példa

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/json.json) bemutatja, hogyan használható a json függvény tömbökkel és objektumokkal:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testValue": {
            "type": "string",
            "defaultValue": "demo value"
        }
    },
    "resources": [
    ],
    "outputs": {
        "jsonOutput": {
            "type": "object",
            "value": "[json('{\"a\": \"b\"}')]"
        },
        "nullOutput": {
            "type": "bool",
            "value": "[empty(json('null'))]"
        },
        "paramOutput": {
            "type": "object",
            "value": "[json(concat('{\"a\": \"', parameters('testValue'), '\"}'))]"
        }
    }
}
```

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| jsonKimenet | Objektum | {"a": "b"} |
| nullOutput | Logikai | True (Igaz) |
| paramKimenet | Objektum | {"a": "demo érték"}

Ha ezt a példasablont az Azure CLI-vel szeretné telepíteni, használja a következőket:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/json.json
```

Ha ezt a példasablont a PowerShell használatával szeretné telepíteni, használja a következőket:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/json.json
```

## <a name="last"></a>Utolsó

`last (arg1)`

A tömb utolsó elemét vagy a karakterlánc utolsó karakterét adja eredményül.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |tömb vagy karakterlánc |Az utolsó elem vagy karakter beolvasásához. |

### <a name="return-value"></a>Visszatérítési érték

A tömb utolsó elemének vagy egy karakterlánc utolsó karakterének típusa (karakterlánc, int, tömb vagy objektum).

### <a name="example"></a>Példa

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) bemutatja, hogyan használható az utolsó függvény tömbbel és karakterlánccal.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[last(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[last('One Two Three')]"
        }
    }
}
```

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| tömbkimenet | Sztring | Három |
| karakterlánckimenet | Sztring | e |

Ha ezt a példasablont az Azure CLI-vel szeretné telepíteni, használja a következőket:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

Ha ezt a példasablont a PowerShell használatával szeretné telepíteni, használja a következőket:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

## <a name="length"></a>hossz

`length(arg1)`

Egy tömb elemeinek, egy karakterlánc karaktereinek vagy egy objektum gyökérszintű tulajdonságainak számát adja eredményül.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |tömb, karakterlánc vagy objektum |Az elemek számának, a karakterek számának lekérő karakterláncnak vagy a gyökérszintű tulajdonságok számának beszerzéséhez használandó objektumnak a lekérnie szükséges tömb. |

### <a name="return-value"></a>Visszatérítési érték

Egy int.

### <a name="example"></a>Példa

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) bemutatja, hogyan használható a hossz tömbökkel és karakterláncokkal:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "stringToTest": {
            "type": "string",
            "defaultValue": "One Two Three"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {
                "propA": "one",
                "propB": "two",
                "propC": "three",
                "propD": {
                    "propD-1": "sub",
                    "propD-2": "sub"
                }
            }
        }
    },
    "resources": [],
    "outputs": {
        "arrayLength": {
            "type": "int",
            "value": "[length(parameters('arrayToTest'))]"
        },
        "stringLength": {
            "type": "int",
            "value": "[length(parameters('stringToTest'))]"
        },
        "objectLength": {
            "type": "int",
            "value": "[length(parameters('objectToTest'))]"
        }
    }
}
```

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| tömbhossz | Int | 3 |
| karakterlánchossz | Int | 13 |
| objectLength (objektumhossza) | Int | 4 |

Ha ezt a példasablont az Azure CLI-vel szeretné telepíteni, használja a következőket:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

Ha ezt a példasablont a PowerShell használatával szeretné telepíteni, használja a következőket:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

Ezt a függvényt tömbbel együtt használhatja az ismétlések számának megadására az erőforrások létrehozásakor. A következő példában a **siteNames** paraméter a webhelyek létrehozásakor használandó nevek tömbjére hivatkozik.

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
}
```

A függvény tömbökkel való használatáról az [Erőforrások több példányának létrehozása az Azure Resource Manager ben című témakörben](copy-resources.md)talál további információt.

## <a name="max"></a>Max

`max(arg1)`

Az egész számok tömbjéből vagy egy vesszővel tagolt egész számból álló tömbből származó maximális értéket adja eredményül.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |egész számok tömbje, vagy vesszővel tagolt egész számok listája |A gyűjtemény a maximális érték bekerüléséhez. |

### <a name="return-value"></a>Visszatérítési érték

A maximális értéket képviselő int.

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

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

## <a name="min"></a>p

`min(arg1)`

A minimális értéket egész számokból vagy vesszővel tagolt egész számokból származó értéket adja eredményül.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |egész számok tömbje, vagy vesszővel tagolt egész számok listája |A gyűjtemény a minimális érték begyűjtéséhez. |

### <a name="return-value"></a>Visszatérítési érték

A minimális értéket képviselő int.

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

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

## <a name="range"></a>Tartomány

`range(startIndex, count)`

Kezdő egész számból származó egész tömböt hoz létre, amely több elemet tartalmaz.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| startIndex |Igen |int |A tömb első egész száma. A startIndex és a darabszám összege nem lehet nagyobb, mint 2147483647. |
| count |Igen |int |Az egész számok száma a tömbben. 10000-ig nem negatív egész számnak kell lennie. |

### <a name="return-value"></a>Visszatérítési érték

Egész számok bólika.

### <a name="example"></a>Példa

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/range.json) bemutatja a tartományfüggvény használatát:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "startingInt": {
            "type": "int",
            "defaultValue": 5
        },
        "numberOfElements": {
            "type": "int",
            "defaultValue": 3
        }
    },
    "resources": [],
    "outputs": {
        "rangeOutput": {
            "type": "array",
            "value": "[range(parameters('startingInt'),parameters('numberOfElements'))]"
        }
    }
}
```

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| rangeOutput | Tömb | [5, 6, 7] |

Ha ezt a példasablont az Azure CLI-vel szeretné telepíteni, használja a következőket:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/range.json
```

Ha ezt a példasablont a PowerShell használatával szeretné telepíteni, használja a következőket:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/range.json
```

## <a name="skip"></a>Ugrál

`skip(originalValue, numberToSkip)`

Egy tömböt ad vissza az összes elemet a megadott szám után a tömbben, vagy egy karakterláncot ad vissza az összes karakterrel a megadott szám után a karakterláncban.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| originalValue (eredeti érték) |Igen |tömb vagy karakterlánc |A kihagyáshoz használandó tömb vagy karakterlánc. |
| számKiugrás |Igen |int |Az áthagyandó elemek vagy karakterek száma. Ha ez az érték 0 vagy kevesebb, az érték összes elemét vagy karakterét visszaadja a rendszer. Ha nagyobb, mint a tömb vagy karakterlánc hossza, a rendszer üres tömböt vagy karakterláncot ad vissza. |

### <a name="return-value"></a>Visszatérítési érték

Tömb vagy karakterlánc.

### <a name="example"></a>Példa

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) kihagyja a tömbben megadott számú elemet és a karakterláncban megadott számú karaktert.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToSkip": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToSkip": {
            "type": "int",
            "defaultValue": 4
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[skip(parameters('testArray'),parameters('elementsToSkip'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[skip(parameters('testString'),parameters('charactersToSkip'))]"
        }
    }
}
```

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| tömbkimenet | Tömb | ["három"] |
| karakterlánckimenet | Sztring | két három |

Ha ezt a példasablont az Azure CLI-vel szeretné telepíteni, használja a következőket:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

Ha ezt a példasablont a PowerShell használatával szeretné telepíteni, használja a következőket:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

## <a name="take"></a>venni

`take(originalValue, numberToTake)`

Egy tömböt ad vissza a megadott számú elemből a tömb elejétől, vagy egy karakterláncot, amelynek a karakterlánc elejétől a megadott számú karaktere van.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| originalValue (eredeti érték) |Igen |tömb vagy karakterlánc |Az a tömb vagy karakterlánc, amelyből az elemeket ki kell venni. |
| számTake |Igen |int |A figyelembe vethet elemek vagy karakterek száma. Ha ez az érték 0 vagy kevesebb, a függvény üres tömböt vagy karakterláncot ad vissza. Ha nagyobb, mint a megadott tömb vagy karakterlánc hossza, a rendszer a tömb vagy karakterlánc összes elemét visszaadja. |

### <a name="return-value"></a>Visszatérítési érték

Tömb vagy karakterlánc.

### <a name="example"></a>Példa

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) a megadott számú elemet veszi fel a tömbből, és karaktereket egy karakterláncból.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToTake": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToTake": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[take(parameters('testArray'),parameters('elementsToTake'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[take(parameters('testString'),parameters('charactersToTake'))]"
        }
    }
}
```

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| tömbkimenet | Tömb | ["egy", "kettő"] |
| karakterlánckimenet | Sztring | itt: |

Ha ezt a példasablont az Azure CLI-vel szeretné telepíteni, használja a következőket:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

Ha ezt a példasablont a PowerShell használatával szeretné telepíteni, használja a következőket:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

## <a name="union"></a>Unió

`union(arg1, arg2, arg3, ...)`

Egyetlen tömböt vagy objektumot ad eredményül a paraméterek összes elemével. Az ismétlődő értékek vagy kulcsok csak egyszer szerepelnek.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |tömb vagy objektum |Az elemek illesztéséhez használt első érték. |
| arg2 |Igen |tömb vagy objektum |Az elemek illesztéséhez használandó második érték. |
| további argumentumok |Nem |tömb vagy objektum |Az elemek illesztéséhez használandó további értékek. |

### <a name="return-value"></a>Visszatérítési érték

Tömb vagy objektum.

### <a name="example"></a>Példa

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/union.json) bemutatja, hogyan használható az egyesítés tömbökkel és objektumokkal:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c1"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"three": "c2", "four": "d", "five": "e"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["three", "four"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "objectOutput": {
            "type": "object",
            "value": "[union(parameters('firstObject'), parameters('secondObject'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[union(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| objectOutput | Objektum | {"egy": "a", "kettő": "b", "három": "c2", "négy": "d", "öt": "e"} |
| tömbkimenet | Tömb | ["egy", "kettő", "három", "négy"] |

Ha ezt a példasablont az Azure CLI-vel szeretné telepíteni, használja a következőket:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/union.json
```

Ha ezt a példasablont a PowerShell használatával szeretné telepíteni, használja a következőket:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/union.json
```

## <a name="next-steps"></a>További lépések

* Az Azure Resource Manager-sablon szakaszainak leírását az [Azure Resource Manager-sablonok készítése című témakörben találja.](template-syntax.md)
* Több sablon egyesítéséhez olvassa el [a Csatolt sablonok használata az Azure Resource Manager rel.](linked-templates.md)
* Ha egy erőforrástípus létrehozásakor meghatározott számú alkalommal szeretne meghaladni, olvassa [el az Erőforrások több példányának létrehozása az Azure Resource Manager ben című témakört.](copy-resources.md)
* A létrehozott sablon központi telepítéséről az Alkalmazás üzembe helyezése az [Azure Resource Manager sablonnal című témakörben](deploy-powershell.md)olvashat.

