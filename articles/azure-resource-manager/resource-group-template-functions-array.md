---
title: Azure Resource Manager template functions – tömbök és objektumok
description: Leírja a Azure Resource Manager-sablonban a tömbök és objektumok kezeléséhez használt függvényeket.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: tomfitz
ms.openlocfilehash: 3366a1be88043c792de8efa3b0d0ab735e558e2a
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72528501"
---
# <a name="array-and-object-functions-for-azure-resource-manager-templates"></a>Azure Resource Manager-sablonok tömb-és objektum-funkciói

A Resource Manager számos funkciót biztosít a tömbök és objektumok használatához.

* [tömb](#array)
* [összefonódik](#coalesce)
* [concat](#concat)
* [tartalmaz](#contains)
* [createArray](#createarray)
* [üres](#empty)
* [első](#first)
* [kereszteződés](#intersection)
* [JSON](#json)
* [utolsó](#last)
* [hossza](#length)
* [Max](#max)
* [min](#min)
* [tartomány](#range)
* [kihagyása](#skip)
* [eltarthat](#take)
* [Union](#union)

Ha egy értékkel tagolt karakterlánc-értékeket szeretne kapni, tekintse meg a következőt: [Split](resource-group-template-functions-string.md#split).

<a id="array" />

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="array"></a>tömb
`array(convertToArray)`

Az értéket egy tömbre konvertálja.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Type (Típus) | Leírás |
|:--- |:--- |:--- |:--- |
| convertToArray |Igen |int, string, Array vagy Object |Egy tömbre konvertálandó érték. |

### <a name="return-value"></a>Visszatérési érték

Tömb.

### <a name="example"></a>Példa

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/array.json) azt mutatja be, hogyan használható a Array függvény különböző típusokkal.

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

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Type (Típus) | Value (Díj) |
| ---- | ---- | ----- |
| intOutput | Tömb | 1 |
| stringOutput | Tömb | ["efgh"] |
| objectOutput | Tömb | [{"a": "b", "c": "d"}] |

A példában szereplő sablon Azure CLI-vel történő üzembe helyezéséhez használja a következőt:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/array.json
```

A példa PowerShell használatával történő üzembe helyezéséhez használja a következőt:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/array.json
```

<a id="coalesce" />

## <a name="coalesce"></a>Összefonódik
`coalesce(arg1, arg2, arg3, ...)`

A paraméterekből származó első nem null értéket adja vissza. Az üres karakterláncok, üres tömbök és üres objektumok nem null értékűek.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Type (Típus) | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |int, string, Array vagy Object |A Null érték tesztelésének első értéke. |
| További argumentumok |Nem |int, string, Array vagy Object |A Null érték tesztelésére szolgáló további értékek. |

### <a name="return-value"></a>Visszatérési érték

Az első nem null paraméterek értéke, amely lehet karakterlánc, int, tömb vagy objektum. NULL, ha az összes paraméter null értékű. 

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/coalesce.json) az egyesítés különböző felhasználási eredményeiből származó kimenetet jeleníti meg.

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

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Type (Típus) | Value (Díj) |
| ---- | ---- | ----- |
| stringOutput | Sztring | alapértelmezett |
| intOutput | int | 1 |
| objectOutput | Objektum | {"első": "default"} |
| arrayOutput | Tömb | 1 |
| emptyOutput | bool | True (Igaz) |

A példában szereplő sablon Azure CLI-vel történő üzembe helyezéséhez használja a következőt:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/coalesce.json
```

A példa PowerShell használatával történő üzembe helyezéséhez használja a következőt:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/coalesce.json
```

<a id="concat" />

## <a name="concat"></a>concat
`concat(arg1, arg2, arg3, ...)`

Több tömböt egyesít, és visszaadja az összefűzött tömböt, vagy kombinálja a több sztringet, és visszaadja az összefűzött karakterláncot. 

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Type (Típus) | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |tömb vagy karakterlánc |Az Összefűzés első tömbje vagy karakterlánca. |
| További argumentumok |Nem |tömb vagy karakterlánc |További tömbök vagy karakterláncok egymást követő sorrendben az összefűzéshez. |

Ez a függvény tetszőleges számú argumentumot igénybe vehet, és a paraméterekhez karakterláncokat vagy tömböket is elfogadhat.

### <a name="return-value"></a>Visszatérési érték
Az összefűzött értékek karakterlánca vagy tömbje.

### <a name="example"></a>Példa

Az alábbi [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) bemutatja, hogyan egyesítheti két tömböt.

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

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Type (Típus) | Value (Díj) |
| ---- | ---- | ----- |
| visszatérési | Tömb | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

A példában szereplő sablon Azure CLI-vel történő üzembe helyezéséhez használja a következőt:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

A példa PowerShell használatával történő üzembe helyezéséhez használja a következőt:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

Az alábbi [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) bemutatja, hogyan egyesítheti a két karakterláncot, és hogyan adhat vissza összefűzött karakterláncot.

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

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Type (Típus) | Value (Díj) |
| ---- | ---- | ----- |
| concatOutput | Sztring | előtag – 5yj4yjf5mbg72 |

A példában szereplő sablon Azure CLI-vel történő üzembe helyezéséhez használja a következőt:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

A példa PowerShell használatával történő üzembe helyezéséhez használja a következőt:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

<a id="contains" />

## <a name="contains"></a>tartalmaz
`contains(container, itemToFind)`

Ellenőrzi, hogy egy tömb tartalmaz-e értéket, egy objektum tartalmaz-e kulcsot, vagy egy karakterlánc tartalmaz-e alkarakterláncot. A karakterlánc-összehasonlítás megkülönbözteti a kis-és nagybetűket. Ha azonban egy objektum kulcsot tartalmaz, az összehasonlítás a kis-és nagybetűk megkülönböztetése nélkül történik.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Type (Típus) | Leírás |
|:--- |:--- |:--- |:--- |
| tároló |Igen |tömb, objektum vagy karakterlánc |A keresendő értéket tartalmazó érték. |
| itemToFind |Igen |karakterlánc vagy int |A keresendő érték. |

### <a name="return-value"></a>Visszatérési érték

**Igaz** , ha az adott tétel megtalálható; Ellenkező esetben **hamis**.

### <a name="example"></a>Példa

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) azt mutatja be, hogyan használható a különböző típusú tartalmaz:

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

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Type (Típus) | Value (Díj) |
| ---- | ---- | ----- |
| stringTrue | bool | True (Igaz) |
| stringFalse | bool | False (Hamis) |
| objectTrue | bool | True (Igaz) |
| objectFalse | bool | False (Hamis) |
| arrayTrue | bool | True (Igaz) |
| arrayFalse | bool | False (Hamis) |

A példában szereplő sablon Azure CLI-vel történő üzembe helyezéséhez használja a következőt:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

A példa PowerShell használatával történő üzembe helyezéséhez használja a következőt:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

<a id="createarray" />

## <a name="createarray"></a>createarray
`createArray (arg1, arg2, arg3, ...)`

Létrehoz egy tömböt a paraméterekből.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Type (Típus) | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |Karakterlánc, egész szám, tömb vagy objektum |A tömb első értéke. |
| További argumentumok |Nem |Karakterlánc, egész szám, tömb vagy objektum |További értékek a tömbben. |

### <a name="return-value"></a>Visszatérési érték

Tömb.

### <a name="example"></a>Példa

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/createarray.json) azt mutatja be, hogyan használhatók a createArray különböző típusú típusokkal:

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

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Type (Típus) | Value (Díj) |
| ---- | ---- | ----- |
| stringArray | Tömb | ["a", "b", "c"] |
| intArray | Tömb | [1, 2, 3] |
| objectArray | Tömb | [{"One": "a", "Two": "b", "három": "c"}] |
| arrayArray | Tömb | [["egy", "kettő", "három"]] |

A példában szereplő sablon Azure CLI-vel történő üzembe helyezéséhez használja a következőt:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/createarray.json
```

A példa PowerShell használatával történő üzembe helyezéséhez használja a következőt:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/createarray.json
```

<a id="empty" />

## <a name="empty"></a>üres

`empty(itemToTest)`

Meghatározza, hogy egy tömb, objektum vagy karakterlánc üres-e.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Type (Típus) | Leírás |
|:--- |:--- |:--- |:--- |
| itemToTest |Igen |tömb, objektum vagy karakterlánc |Az az érték, amely alapján ellenőrizhető, hogy üres-e. |

### <a name="return-value"></a>Visszatérési érték

**Igaz** értéket ad vissza, ha az érték üres. Ellenkező esetben **hamis**.

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) ellenőrzi, hogy egy tömb, objektum és karakterlánc üres-e.

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

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Type (Típus) | Value (Díj) |
| ---- | ---- | ----- |
| arrayEmpty | bool | True (Igaz) |
| objectEmpty | bool | True (Igaz) |
| stringEmpty | bool | True (Igaz) |

A példában szereplő sablon Azure CLI-vel történő üzembe helyezéséhez használja a következőt:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

A példa PowerShell használatával történő üzembe helyezéséhez használja a következőt:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

<a id="first" />

## <a name="first"></a>első
`first(arg1)`

A tömb első elemét vagy a karakterlánc első karakterét adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Type (Típus) | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |tömb vagy karakterlánc |Az első elem vagy karakter beolvasására szolgáló érték. |

### <a name="return-value"></a>Visszatérési érték

Egy tömb első elemének típusa (karakterlánc, int, tömb vagy objektum) vagy egy karakterlánc első karaktere.

### <a name="example"></a>Példa

Az alábbi [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) azt szemlélteti, hogyan használható az első függvény egy tömb és egy karakterlánc használatával.

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

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Type (Típus) | Value (Díj) |
| ---- | ---- | ----- |
| arrayOutput | Sztring | egy |
| stringOutput | Sztring | O |

A példában szereplő sablon Azure CLI-vel történő üzembe helyezéséhez használja a következőt:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

A példa PowerShell használatával történő üzembe helyezéséhez használja a következőt:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

<a id="intersection" />

## <a name="intersection"></a>kereszteződés
`intersection(arg1, arg2, arg3, ...)`

Egyetlen tömböt vagy objektumot ad vissza, amely a paraméterek közös elemeit tartalmazza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Type (Típus) | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |tömb vagy objektum |Az általános elemek kereséséhez használandó első érték. |
| arg2 |Igen |tömb vagy objektum |A közös elemek kereséséhez használt második érték. |
| További argumentumok |Nem |tömb vagy objektum |Az általános elemek kereséséhez használandó további értékek. |

### <a name="return-value"></a>Visszatérési érték

Egy tömb vagy objektum a közös elemekkel.

### <a name="example"></a>Példa

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/intersection.json) azt mutatja be, hogyan használhatók az metszetek és objektumok együttes használata:

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

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Type (Típus) | Value (Díj) |
| ---- | ---- | ----- |
| objectOutput | Objektum | {"One": "a", "három": "c"} |
| arrayOutput | Tömb | ["kettő", "három"] |

A példában szereplő sablon Azure CLI-vel történő üzembe helyezéséhez használja a következőt:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/intersection.json
```

A példa PowerShell használatával történő üzembe helyezéséhez használja a következőt:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/intersection.json
```

## <a name="json"></a>JSON
`json(arg1)`

Egy JSON-objektumot ad vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Type (Típus) | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |sztring |A JSON-ra konvertálandó érték. |


### <a name="return-value"></a>Visszatérési érték

A megadott karakterlánc JSON-objektuma vagy egy üres objektum, ha **Null** van megadva.

### <a name="remarks"></a>Megjegyzések

Ha a JSON-objektumban szerepelnie kell egy paraméter értékének vagy változónak, használja a [concat](resource-group-template-functions-string.md#concat) függvényt a függvénynek átadott karakterlánc létrehozásához.

### <a name="example"></a>Példa

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/json.json) azt mutatja be, hogyan használható a JSON függvény a tömbökkel és objektumokkal:

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

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Type (Típus) | Value (Díj) |
| ---- | ---- | ----- |
| jsonOutput | Objektum | {"a": "b"} |
| nullOutput | Logikai | True (Igaz) |
| paramOutput | Objektum | {"a": "bemutató értéke"}

A példában szereplő sablon Azure CLI-vel történő üzembe helyezéséhez használja a következőt:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/json.json
```

A példa PowerShell használatával történő üzembe helyezéséhez használja a következőt:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/json.json
```

<a id="last" />

## <a name="last"></a>utolsó
`last (arg1)`

A tömb utolsó elemét vagy a karakterlánc utolsó karakterét adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Type (Típus) | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |tömb vagy karakterlánc |Az utolsó elem vagy karakter beolvasására szolgáló érték. |

### <a name="return-value"></a>Visszatérési érték

Egy tömb utolsó elemének típusa (karakterlánc, int, tömb vagy objektum) vagy egy karakterlánc utolsó karaktere.

### <a name="example"></a>Példa

Az alábbi [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) azt szemlélteti, hogyan használható az utolsó függvény egy tömb és egy karakterlánc használatával.

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

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Type (Típus) | Value (Díj) |
| ---- | ---- | ----- |
| arrayOutput | Sztring | három |
| stringOutput | Sztring | E |

A példában szereplő sablon Azure CLI-vel történő üzembe helyezéséhez használja a következőt:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

A példa PowerShell használatával történő üzembe helyezéséhez használja a következőt:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

<a id="length" />

## <a name="length"></a>hossza
`length(arg1)`

Egy tömbben lévő elemek számát, egy karakterláncban szereplő karaktereket vagy egy objektum gyökérszintű tulajdonságait adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Type (Típus) | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |tömb, karakterlánc vagy objektum |Az elemek számának beolvasásához használandó tömb, a karakterek számának beolvasásához használandó karakterlánc, vagy a gyökérszintű tulajdonságok számának beolvasásához használandó objektum. |

### <a name="return-value"></a>Visszatérési érték

Egy int. 

### <a name="example"></a>Példa

Az alábbi [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) azt szemlélteti, hogyan használható a hossz egy tömb és egy karakterlánc használatával:

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

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Type (Típus) | Value (Díj) |
| ---- | ---- | ----- |
| arrayLength | int | 3 |
| stringLength | int | 13 |
| objectLength | int | 4 |

A példában szereplő sablon Azure CLI-vel történő üzembe helyezéséhez használja a következőt:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

A példa PowerShell használatával történő üzembe helyezéséhez használja a következőt:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

A függvényt egy tömb használatával adhatja meg az erőforrások létrehozásakor megjelenő ismétlések számát. A következő példában a **siteNames** paraméter a webhelyek létrehozásakor használandó nevek sorára hivatkozik.

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
}
```

További információ a függvény tömbhöz való használatáról: [több erőforrás-példány létrehozása Azure Resource Managerban](resource-group-create-multiple.md).

<a id="max" />

## <a name="max"></a>Max
`max(arg1)`

A maximális értéket adja vissza egész számok tömbje vagy az egész számok vesszővel tagolt listája alapján.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Type (Típus) | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |egész számok tömbje vagy egész számok vesszővel tagolt listája |A gyűjtemény, amely a maximális értéket kapja. |

### <a name="return-value"></a>Visszatérési érték

A maximális értéket jelölő int.

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

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Type (Típus) | Value (Díj) |
| ---- | ---- | ----- |
| arrayOutput | int | 5 |
| intOutput | int | 5 |

A példában szereplő sablon Azure CLI-vel történő üzembe helyezéséhez használja a következőt:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

A példa PowerShell használatával történő üzembe helyezéséhez használja a következőt:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

<a id="min" />

## <a name="min"></a>perc
`min(arg1)`

A minimális értéket adja vissza egész számok tömbje vagy az egész számok vesszővel tagolt listája alapján.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Type (Típus) | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |egész számok tömbje vagy egész számok vesszővel tagolt listája |A gyűjtemény a minimális érték beolvasásához. |

### <a name="return-value"></a>Visszatérési érték

A minimális értéket jelölő int.

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

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Type (Típus) | Value (Díj) |
| ---- | ---- | ----- |
| arrayOutput | int | 0 |
| intOutput | int | 0 |

A példában szereplő sablon Azure CLI-vel történő üzembe helyezéséhez használja a következőt:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

A példa PowerShell használatával történő üzembe helyezéséhez használja a következőt:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

<a id="range" />

## <a name="range"></a>tartomány
`range(startingInteger, numberOfElements)`

Egész számok tömbjét hozza létre egy kezdő egész számból, és több elemet tartalmaz.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Type (Típus) | Leírás |
|:--- |:--- |:--- |:--- |
| startingInteger |Igen |int |A tömb első egésze. |
| numberofElements |Igen |int |A tömbben lévő egész számok száma. |

### <a name="return-value"></a>Visszatérési érték

Egész számok tömbje.

### <a name="example"></a>Példa

A következő [példában látható sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/range.json) a tartomány függvény használatát mutatja be:

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

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Type (Típus) | Value (Díj) |
| ---- | ---- | ----- |
| rangeOutput | Tömb | [5, 6, 7] |

A példában szereplő sablon Azure CLI-vel történő üzembe helyezéséhez használja a következőt:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/range.json
```

A példa PowerShell használatával történő üzembe helyezéséhez használja a következőt:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/range.json
```

<a id="skip" />

## <a name="skip"></a>kihagyása
`skip(originalValue, numberToSkip)`

Egy tömböt ad vissza az összes elemmel a tömbben megadott szám után, vagy egy karakterláncot ad vissza, amely az összes karaktert tartalmazza a karakterláncban megadott szám után.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Type (Típus) | Leírás |
|:--- |:--- |:--- |:--- |
| originalValue |Igen |tömb vagy karakterlánc |A kihagyáshoz használandó tömb vagy karakterlánc. |
| numberToSkip |Igen |int |A kihagyni kívánt elemek vagy karakterek száma. Ha ez az érték 0 vagy kevesebb, az értékben szereplő összes elemet vagy karaktert adja vissza a rendszer. Ha a tömb vagy a karakterlánc hossza nagyobb, akkor a rendszer üres tömböt vagy karakterláncot ad vissza. |

### <a name="return-value"></a>Visszatérési érték

Tömb vagy karakterlánc.

### <a name="example"></a>Példa

A következő [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) kihagyja a tömbben megadott számú elemet és a megadott számú karaktert egy karakterláncban.

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

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Type (Típus) | Value (Díj) |
| ---- | ---- | ----- |
| arrayOutput | Tömb | ["három"] |
| stringOutput | Sztring | 2 3 |

A példában szereplő sablon Azure CLI-vel történő üzembe helyezéséhez használja a következőt:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

A példa PowerShell használatával történő üzembe helyezéséhez használja a következőt:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

<a id="take" />

## <a name="take"></a>Eltarthat
`take(originalValue, numberToTake)`

Egy tömböt ad vissza a megadott számú elemmel a tömb elejétől, vagy egy olyan karakterláncot, amely a karakterlánc elejétől megadott számú karakterből áll.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Type (Típus) | Leírás |
|:--- |:--- |:--- |:--- |
| originalValue |Igen |tömb vagy karakterlánc |Az elemeket tartalmazó tömb vagy karakterlánc. |
| numberToTake |Igen |int |A felvenni kívánt elemek vagy karakterek száma. Ha ez az érték 0 vagy kevesebb, üres tömböt vagy karakterláncot ad vissza. Ha az érték nagyobb, mint a megadott tömb vagy karakterlánc hossza, a rendszer a tömb vagy karakterlánc összes elemét adja vissza. |

### <a name="return-value"></a>Visszatérési érték

Tömb vagy karakterlánc.

### <a name="example"></a>Példa

A következő [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) a tömbben szereplő elemek számát és a karakterláncok karaktereit veszi figyelembe.

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

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Type (Típus) | Value (Díj) |
| ---- | ---- | ----- |
| arrayOutput | Tömb | ["egy", "Two"] |
| stringOutput | Sztring | A |

A példában szereplő sablon Azure CLI-vel történő üzembe helyezéséhez használja a következőt:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

A példa PowerShell használatával történő üzembe helyezéséhez használja a következőt:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

<a id="union" />

## <a name="union"></a>Union
`union(arg1, arg2, arg3, ...)`

Egyetlen tömböt vagy objektumot ad vissza, amely a paraméterek összes elemét tartalmazza. Ismétlődő értékek vagy kulcsok csak egyszer szerepelnek.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Type (Típus) | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |tömb vagy objektum |Az elemek csatlakoztatásának első értéke. |
| arg2 |Igen |tömb vagy objektum |Az elemek csatlakoztatásához használt második érték. |
| További argumentumok |Nem |tömb vagy objektum |Az elemekhez való csatlakozáshoz használandó további értékek. |

### <a name="return-value"></a>Visszatérési érték

Tömb vagy objektum.

### <a name="example"></a>Példa

Az alábbi [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/union.json) azt mutatja be, hogyan használható a Union a tömbökkel és objektumokkal:

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

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Type (Típus) | Value (Díj) |
| ---- | ---- | ----- |
| objectOutput | Objektum | {"One": "a", "kettő": "b", "három": "C2", "Four": "d", "öt": "e"} |
| arrayOutput | Tömb | ["egy", "kettő", "három", "négy"] |

A példában szereplő sablon Azure CLI-vel történő üzembe helyezéséhez használja a következőt:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/union.json
```

A példa PowerShell használatával történő üzembe helyezéséhez használja a következőt:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/union.json
```

## <a name="next-steps"></a>Következő lépések
* Egy Azure Resource Manager sablonban található részekkel kapcsolatos leírást a következő témakörben talál: [Azure Resource Manager sablonok készítése](resource-group-authoring-templates.md).
* Több sablon egyesítéséhez tekintse meg [a csatolt sablonok használata Azure Resource Manager használatával](resource-group-linked-templates.md)című témakört.
* Ha egy adott típusú erőforrás létrehozásakor meghatározott számú alkalommal szeretné megismételni a problémát, tekintse meg a [Azure Resource Manager erőforrások több példányának létrehozása](resource-group-create-multiple.md)című témakört.
* A létrehozott sablon üzembe helyezésével kapcsolatban lásd: [alkalmazás központi telepítése Azure Resource Manager sablonnal](resource-group-template-deploy.md).

