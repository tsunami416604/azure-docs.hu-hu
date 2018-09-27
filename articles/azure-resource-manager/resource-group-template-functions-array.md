---
title: Functions – Pole és objektumokat az Azure Resource Manager-sablon |} A Microsoft Docs
description: Ismerteti a functions-tömbök és objektumok használata az Azure Resource Manager-sablon használata.
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
ms.openlocfilehash: e0269e17a419c6b611d72a7d00668fe9c9519894
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47166183"
---
# <a name="array-and-object-functions-for-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonok tömb- és objektumfüggvények 

A Resource Manager-tömbök és objektumok használata a számos funkciót biztosít.

* [Pole](#array)
* [Coalesce](#coalesce)
* [concat](#concat)
* [tartalmaz](#contains)
* [createArray](#createarray)
* [üres](#empty)
* [első](#first)
* [Metszet](#intersection)
* [json](#json)
* [utolsó](#last)
* [Hossza](#length)
* [max](#max)
* [Min](#min)
* [range](#range)
* [skip](#skip)
* [hajtsa végre a megfelelő](#take)
* [Union](#union)

Érték elválasztva karakterlánc-értékek tömbje lekéréséhez lásd: [felosztása](resource-group-template-functions-string.md#split).

<a id="array" />

## <a name="array"></a>tömb
`array(convertToArray)`

Az érték konvertálása egy tömb.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| convertToArray |Igen |int, string, tömb vagy objektum |Egy tömb alakítandó érték. |

### <a name="return-value"></a>Vrácená hodnota

Egy tömb.

### <a name="example"></a>Példa

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/array.json) mutatja be a tömb függvénnyel a különböző típusú.

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| intOutput | Tömb | [1] |
| stringOutput | Tömb | ["efgh"] |
| objectOutput | Tömb | [{"a": "b", "c": "d"}] |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/array.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/array.json
```

<a id="coalesce" />

## <a name="coalesce"></a>Coalesce
`coalesce(arg1, arg2, arg3, ...)`

A paraméterek első nem üres értéket adja vissza. Üres karakterláncok, üres tömbök és üres objektumok ne legyenek.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |int, string, tömb vagy objektum |Az első érték null teszteléséhez. |
| További argumentumok |Nem |int, string, tömb vagy objektum |További értékek tesztelésére null értékűek. |

### <a name="return-value"></a>Vrácená hodnota

Az első nem null értékű paraméterek, amely egy karakterlánc, int, tömb vagy objektum lehet értéke. NULL értékű, ha az összes paraméter null értékűek. 

### <a name="example"></a>Példa

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/coalesce.json) a coalesce, a különböző felhasználási kimenetét mutatja be.

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| stringOutput | Sztring | alapértelmezett |
| intOutput | Int | 1 |
| objectOutput | Objektum | {"first": "alapértelmezett"} |
| arrayOutput | Tömb | [1] |
| emptyOutput | Logikai | True (Igaz) |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/coalesce.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/coalesce.json
```

<a id="concat" />

## <a name="concat"></a>Concat
`concat(arg1, arg2, arg3, ...)`

Több tömbök egyesíti és a összefűzött tömböt ad vissza, vagy kombinálja a több karakterlánc-értékeket, és a összefűzött karakterláncot ad vissza. 

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |tömb vagy karakterlánc |Az első tömb vagy karakterlánc az összefűzés. |
| További argumentumok |Nem |tömb vagy karakterlánc |További tömbök vagy karakterláncok összefűző sorrendben. |

Ez a függvény tetszőleges számú argumentumot is igénybe vehet, és karakterláncok vagy a paraméterek tömbök tud fogadni.

### <a name="return-value"></a>Vrácená hodnota
Egy karakterlánc- vagy összefűzött értékek tömbje.

### <a name="example"></a>Példa

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) bemutatja, hogyan kombinálja két tömb.

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| a visszaadandó | Tömb | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) bemutatja, hogyan kombinálja két karakterlánc-értékeket és a egy összefűzött karakterláncot ad vissza.

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| concatOutput | Sztring | előtag-5yj4yjf5mbg72 |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

<a id="contains" />

## <a name="contains"></a>tartalmazza a következőt:
`contains(container, itemToFind)`

Ellenőrzi, hogy egy tömb értéket tartalmaz, objektum kulcsot tartalmaz, vagy egy karakterlánc részkarakterláncot tartalmaz.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| tároló |Igen |tömb, objektumot vagy karakterlánc |Az érték, amely tartalmazza a keresendő érték. |
| itemToFind |Igen |karakterlánc- vagy int |Keresendő érték. |

### <a name="return-value"></a>Vrácená hodnota

**Igaz** , ha az elem nem található; ellenkező esetben **hamis**.

### <a name="example"></a>Példa

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) használata tartalmazza a különböző típusú mutat be:

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| stringTrue | Logikai | True (Igaz) |
| stringFalse | Logikai | False (Hamis) |
| objectTrue | Logikai | True (Igaz) |
| objectFalse | Logikai | False (Hamis) |
| arrayTrue | Logikai | True (Igaz) |
| arrayFalse | Logikai | False (Hamis) |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

<a id="createarray" />

## <a name="createarray"></a>createarray
`createArray (arg1, arg2, arg3, ...)`

Létrehoz egy tömböt a paraméterek közül.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |Karakterlánc, egész szám, tömb vagy objektum |Az első érték a tömbben. |
| További argumentumok |Nem |Karakterlánc, egész szám, tömb vagy objektum |További értékek a tömbben. |

### <a name="return-value"></a>Vrácená hodnota

Egy tömb.

### <a name="example"></a>Példa

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/createarray.json) különböző típusú createArray használatát ismerteti:

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| stringArray | Tömb | ["a", "b", "c"] |
| intArray | Tömb | [1, 2, 3] |
| objectArray | Tömb | [{"egy": "a", "2": "b", "három": "c"}] |
| arrayArray | Tömb | [["egy", "két", "három"]] |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/createarray.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/createarray.json
```

<a id="empty" />

## <a name="empty"></a>üres

`empty(itemToTest)`

Meghatározza, hogy egy tömb, objektumot vagy karakterlánc üres.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| itemToTest |Igen |tömb, objektumot vagy karakterlánc |Ellenőrizze, hogy üres érték. |

### <a name="return-value"></a>Vrácená hodnota

Értéket ad vissza **igaz** Ha az érték üres; ellenkező esetben **hamis**.

### <a name="example"></a>Példa

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) ellenőrzi, hogy egy tömb, az objektum és a karakterlánc üres.

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| arrayEmpty | Logikai | True (Igaz) |
| objectEmpty | Logikai | True (Igaz) |
| stringEmpty | Logikai | True (Igaz) |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

<a id="first" />

## <a name="first"></a>első
`first(arg1)`

Az első elemét a tömb vagy karakterlánc első karaktere adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |tömb vagy karakterlánc |Az érték első karakter vagy elem lekéréséhez. |

### <a name="return-value"></a>Vrácená hodnota

A típusa (karakterlánc, int, tömb vagy objektum) az első elemét a tömb vagy karakterlánc első karaktere.

### <a name="example"></a>Példa

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) bemutatja, hogyan használható az első függvény egy tömböt és egy karakterlánc.

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| arrayOutput | Sztring | egy |
| stringOutput | Sztring | O |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

<a id="intersection" />

## <a name="intersection"></a>Metszet
`intersection(arg1, arg2, arg3, ...)`

A paraméterek egyetlen olyan tömböt vagy objektumot a közös elemeit adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |olyan tömböt vagy objektumot |Az első érték használata közös elemek kereséséhez. |
| Arg2 |Igen |olyan tömböt vagy objektumot |Közös elemek kereséséhez használja a második érték. |
| További argumentumok |Nem |olyan tömböt vagy objektumot |További értékek a közös elemek kereséséhez használandó. |

### <a name="return-value"></a>Vrácená hodnota

Egy tömböt vagy objektumot a közös elemeit.

### <a name="example"></a>Példa

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/intersection.json) bemutatja, hogyan metszet használata a tömbök vagy objektumok:

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| objectOutput | Objektum | {"egy": "a", "három": "c"} |
| arrayOutput | Tömb | ["két", "három"] |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/intersection.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/intersection.json
```

## <a name="json"></a>JSON
`json(arg1)`

Egy JSON-objektumot ad vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |sztring |Az érték átalakítása JSON. |


### <a name="return-value"></a>Vrácená hodnota

A megadott karakterlánc a JSON-objektum, vagy egy üres objektum amikor **null** van megadva.

### <a name="remarks"></a>Megjegyzések

Ha egy paraméter értéke vagy a változó tartalmazza a JSON-objektumban van szüksége, használja a [concat](resource-group-template-functions-string.md#concat) függvény létrehozása a karakterlánc, amely a függvénynek adja át.

### <a name="example"></a>Példa

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/json.json) mutatja be a json-függvény használata a tömbök vagy objektumok:

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| jsonOutput | Objektum | {"a": "b"} |
| nullOutput | Logikai | True (Igaz) |
| paramOutput | Objektum | {"a": "értékét bemutató"}

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/json.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/json.json
```

<a id="last" />

## <a name="last"></a>utolsó
`last (arg1)`

Az utolsó elem a tömb vagy karakterlánc utolsó karakter adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |tömb vagy karakterlánc |Az érték utolsó karakter vagy elem lekéréséhez. |

### <a name="return-value"></a>Vrácená hodnota

A típusa (karakterlánc, int, tömb vagy objektum) az utolsó elem az tömb vagy karakterlánc utolsó karakter.

### <a name="example"></a>Példa

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) bemutatja, hogyan használható az utolsó függvény egy tömböt és egy karakterlánc.

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| arrayOutput | Sztring | három |
| stringOutput | Sztring | e |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

<a id="length" />

## <a name="length"></a>Hossza
`length(arg1)`

Egy tömb vagy karakterlánc karaktereinek az elemek számát adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |tömb vagy karakterlánc |A tömb első karakterek használata az elemeket, vagy a karakterlánc első használatával. |

### <a name="return-value"></a>Vrácená hodnota

Egy: egész szám. 

### <a name="example"></a>Példa

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) bemutatja, hogyan használható egy tömböt és egy karakterlánc hossza:

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
        }
    }
}
```

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

Ez a függvény egy tömböt a segítségével adja meg az ismétlések száma erőforrások létrehozásakor. A következő példában a paraméter **siteNames** lenne tekintse meg a webhely létrehozásakor nevek tömbjét.

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
}
```

Ez a függvény egy tömböt való használatával kapcsolatos további információkért lásd: [több erőforráspéldány létrehozása az Azure Resource Manager](resource-group-create-multiple.md).

<a id="max" />

## <a name="max"></a>max.
`max(arg1)`

A maximális érték egész számok tömbje vagy egészek vesszővel elválasztott listáját adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |egész számoknak vagy olyan vesszővel elválasztott listája egész számok tömbje |A gyűjtemény a maximális értéket. |

### <a name="return-value"></a>Vrácená hodnota

Az int, a maximális értéket jelölő.

### <a name="example"></a>Példa

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) max. használata egy tömböt és az egész számok listáját jeleníti meg:

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
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

<a id="min" />

## <a name="min"></a>perc
`min(arg1)`

A minimális érték egész számok tömbje vagy egészek vesszővel elválasztott listáját adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |egész számoknak vagy olyan vesszővel elválasztott listája egész számok tömbje |A gyűjtemény a minimális érték beolvasása. |

### <a name="return-value"></a>Vrácená hodnota

A minimális értéket jelölő egy int.

### <a name="example"></a>Példa

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) minimális használata egy tömböt és az egész számok listáját jeleníti meg:

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
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

<a id="range" />

## <a name="range"></a>Címtartomány
`range(startingInteger, numberOfElements)`

Egy egész indítása és számos elemet tartalmazó egész számok tömbje hoz létre.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| startingInteger |Igen |int |A tömbben található első egész szám. |
| numberofElements |Igen |int |A tömbben található egész számok száma. |

### <a name="return-value"></a>Vrácená hodnota

Egész számok tömbje.

### <a name="example"></a>Példa

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/range.json) mutatja be a tartomány függvény használatára:

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| rangeOutput | Tömb | [5, 6, 7] |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/range.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/range.json
```

<a id="skip" />

## <a name="skip"></a>kihagyás
`skip(originalValue, numberToSkip)`

A tömb a megadott szám után elemeket tömböt ad vissza, vagy az összes karakter karakterláncot ad vissza. a karakterlánc a megadott szám után.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| originalValue |Igen |tömb vagy karakterlánc |A tömböt vagy karakterláncot, használja a rendszer kihagyja. |
| numberToSkip |Igen |int |Elemek vagy hagyja ki a karakterek száma. Ha ez az érték 0 vagy kisebb, a elemek vagy az érték karaktereinek adott vissza. Ha a tömb vagy karakterlánc hossza nagyobb, egy üres tömb vagy karakterlánc adja vissza. |

### <a name="return-value"></a>Vrácená hodnota

Egy tömb vagy karakterlánc.

### <a name="example"></a>Példa

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) kihagyja a megadott számú elem a tömbben, és a megadott számú karakter a karakterláncban.

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| arrayOutput | Tömb | ["három"] |
| stringOutput | Sztring | két három |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

<a id="take" />

## <a name="take"></a>hajtsa végre a megfelelő
`take(originalValue, numberToTake)`

Egy tömb a megadott számú elem a tömbben, vagy a megadott számú karaktere a karakterlánc-karakterlánc kezdetét adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| originalValue |Igen |tömb vagy karakterlánc |A tömböt vagy karakterláncot, hogy az elemek. |
| numberToTake |Igen |int |Elemek vagy elvégzendő karakterek száma. Ha ez az érték 0 vagy kisebb, egy üres tömb vagy karakterlánc adja vissza. Ha nagyobb, mint a megadott tömb vagy karakterlánc hossza, a rendszer tömb vagy karakterlánc összes elemét adja vissza. |

### <a name="return-value"></a>Vrácená hodnota

Egy tömb vagy karakterlánc.

### <a name="example"></a>Példa

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) vesz igénybe a megadott számú elem a tömbből, és a egy karakterláncból karakterből.

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| arrayOutput | Tömb | ["egy", "két"] |
| stringOutput | Sztring | be |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

<a id="union" />

## <a name="union"></a>Union
`union(arg1, arg2, arg3, ...)`

A paraméterek egyetlen olyan tömböt vagy objektum összes elemét adja vissza. Ismétlődő vagy kulcsok vannak csak egyszer tartalmazza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |olyan tömböt vagy objektumot |Az első érték tartománybeállításait elemeket. |
| Arg2 |Igen |olyan tömböt vagy objektumot |A második érték tartománybeállításait elemeket. |
| További argumentumok |Nem |olyan tömböt vagy objektumot |További értékek tartománybeállításait elemeket. |

### <a name="return-value"></a>Vrácená hodnota

Egy olyan tömböt vagy objektumot.

### <a name="example"></a>Példa

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/union.json) bemutatja, hogyan használható az Unió tömbök és objektumok:

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| objectOutput | Objektum | {"egy": "a", "2": "b", "három": "c2", "négy": "d", "5": "e"} |
| arrayOutput | Tömb | ["egy", "két", "három", "négy"] |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/union.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/union.json
```

## <a name="next-steps"></a>További lépések
* A szakaszok az Azure Resource Manager-sablon ismertetését lásd: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).
* Több sablon egyesíteni, lásd: [kapcsolt sablonok használata az Azure Resource Manager](resource-group-linked-templates.md).
* A megadott számú alkalommal újrafuttathatja egy adott típusú erőforrás létrehozásakor, lásd: [több erőforráspéldány létrehozása az Azure Resource Manager](resource-group-create-multiple.md).
* Ellenőrizze, hogyan helyezheti üzembe a létrehozott sablont, tekintse meg a [alkalmazás üzembe helyezése Azure Resource Manager-sablonnal](resource-group-template-deploy.md).

