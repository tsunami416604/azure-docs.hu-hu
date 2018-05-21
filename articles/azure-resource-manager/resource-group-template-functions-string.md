---
title: Az Azure Resource Manager sablonfüggvényei - karakterlánc |} Microsoft Docs
description: Az Azure Resource Manager-sablonok segítségével karakterláncok használata funkcióit ismerteti.
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
ms.openlocfilehash: 33a49a9fb66240382b0bb4e0bedbb07b8d78a763
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="string-functions-for-azure-resource-manager-templates"></a>Az Azure Resource Manager sablonokhoz karakterlánc

Erőforrás-kezelő a következő funkciókat nyújt karakterláncok használata.

* [base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [concat](#concat)
* [tartalmazza](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [üres](#empty)
* [megadott módon végződő](#endswith)
* [első](#first)
* [guid](#guid)
* [indexOf](#indexof)
* [utolsó](#last)
* [lastIndexOf](#lastindexof)
* [Hossza](#length)
* [padLeft](#padleft)
* [replace](#replace)
* [skip](#skip)
* [felosztás](#split)
* [startswith elemnek](resource-group-template-functions-string.md#startswith)
* [string](#string)
* [substring](#substring)
* [hajtsa végre a megfelelő](#take)
* [toLower](#tolower)
* [toUpper](#toupper)
* [trim](#trim)
* [uniqueString](#uniquestring)
* [uri](#uri)
* [uriComponent](resource-group-template-functions-string.md#uricomponent)
* [uriComponentToString](resource-group-template-functions-string.md#uricomponenttostring)

<a id="base64" />

## <a name="base64"></a>a Base64
`base64(inputString)`

A bemeneti karakterlánc a base64 alakot adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| inputString |Igen |karakterlánc |A visszatérési érték, mint a Base64 kódolású megjelenítése. |

### <a name="return-value"></a>Visszatérési érték

A base64 tartalmazó karakterlánc.

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) a base64 funkció használatát ismerteti.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| base64Output | Karakterlánc | b25lLCB0d28sIHRocmVl |
| toStringOutput | Karakterlánc | egy két há' |
| toJsonOutput | Objektum | {"egy": "a", "2": "b"} |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/base64.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/base64.json
```

<a id="base64tojson" />

## <a name="base64tojson"></a>base64ToJson
`base64tojson`

A Base64 kódolású megjelenítése konvertál egy JSON-objektum.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| base64Value |Igen |karakterlánc |A base64 ábrázolását, egy JSON-objektum konvertálása. |

### <a name="return-value"></a>Visszatérési érték

A JSON-objektumból.

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) konvertálni az base64 értéket a base64ToJson függvényt használja:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| base64Output | Karakterlánc | b25lLCB0d28sIHRocmVl |
| toStringOutput | Karakterlánc | egy két há' |
| toJsonOutput | Objektum | {"egy": "a", "2": "b"} |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/base64.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/base64.json
```

<a id="base64tostring" />

## <a name="base64tostring"></a>base64ToString
`base64ToString(base64Value)`

A Base64 kódolású megjelenítése karakterlánccá alakítja át.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| base64Value |Igen |karakterlánc |A base64 ábrázolását karakterlánccá konvertálni. |

### <a name="return-value"></a>Visszatérési érték

A konvertált base64 érték karakterlánc.

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) konvertálni az base64 értéket a base64ToString függvényt használja:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| base64Output | Karakterlánc | b25lLCB0d28sIHRocmVl |
| toStringOutput | Karakterlánc | egy két há' |
| toJsonOutput | Objektum | {"egy": "a", "2": "b"} |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/base64.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/base64.json
```

<a id="concat" />

## <a name="concat"></a>Concat
`concat (arg1, arg2, arg3, ...)`

Több karakterlánc-értékek egyesíti, és a összefűzött karakterláncot ad vissza, vagy több tömbök egyesíti, és a összefűzött tömböt ad vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |karakterlánc vagy tömb |A kapott első értéke. |
| További argumentumok |Nem |karakterlánc |További értéket kapott a sorrendben. |

### <a name="return-value"></a>Visszatérési érték
A karakterlánc vagy tömb összefűzött.

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) bemutatja, hogyan kombinálhatja a két karakterlánc-értékeket, és olyan összefűzött karakterláncot adja vissza.

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

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| concatOutput | Karakterlánc | előtag-5yj4yjf5mbg72 |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) bemutatja, hogyan kombinálhatja a két tömbnek.

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

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| térjen vissza | Tömb | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

<a id="contains" />

## <a name="contains"></a>tartalmazza a következőt:
`contains (container, itemToFind)`

Ellenőrzi, hogy egy tömb értéket tartalmaz, objektum kulcsot tartalmaz, vagy egy karakterláncot egy substring tartalmazza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| tároló |Igen |a tömb, objektum vagy karakterlánc |Az érték, amely tartalmazza a keresendő érték. |
| itemToFind |Igen |karakterlánc- vagy int |Az érték kereséséhez. |

### <a name="return-value"></a>Visszatérési érték

**Igaz** Ha az elem található; ellenkező esetben **hamis**.

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) használata tartalmazza a különböző típusú azt mutatja be:

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

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| stringTrue | logikai érték | True (Igaz) |
| stringFalse | logikai érték | False (Hamis) |
| objectTrue | logikai érték | True (Igaz) |
| objectFalse | logikai érték | False (Hamis) |
| arrayTrue | logikai érték | True (Igaz) |
| arrayFalse | logikai érték | False (Hamis) |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

<a id="datauri" />

## <a name="datauri"></a>dataUri
`dataUri(stringToConvert)`

Egy adat-URI azonosító alakít egy értéket.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| stringToConvert |Igen |karakterlánc |Az érték átalakítása egy adat-URI azonosító. |

### <a name="return-value"></a>Visszatérési érték

Egy karakterláncot formázni egy adat-URI azonosító.

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) egy adat-URI azonosító alakít egy értéket, és egy adat-URI azonosító alakít át karakterlánccá:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "dataFormattedString": {
            "type": "string",
            "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
        }
    },
    "resources": [],
    "outputs": {
        "dataUriOutput": {
            "value": "[dataUri(parameters('stringToTest'))]",
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| dataUriOutput | Karakterlánc | adatok: text / egyszerű; charset = utf8; base64, SGVsbG8 = |
| toStringOutput | Karakterlánc | helló világ! |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/datauri.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/datauri.json
```

<a id="datauritostring" />

## <a name="datauritostring"></a>dataUriToString
`dataUriToString(dataUriToConvert)`

Egy adat-URI azonosító formátuma értékét karakterlánccá.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |Igen |karakterlánc |Az adatok URI értéket átalakítani. |

### <a name="return-value"></a>Visszatérési érték

A konvertált értéket tartalmazó karakterlánc.

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) egy adat-URI azonosító alakít egy értéket, és egy adat-URI azonosító alakít át karakterlánccá:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "dataFormattedString": {
            "type": "string",
            "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
        }
    },
    "resources": [],
    "outputs": {
        "dataUriOutput": {
            "value": "[dataUri(parameters('stringToTest'))]",
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| dataUriOutput | Karakterlánc | adatok: text / egyszerű; charset = utf8; base64, SGVsbG8 = |
| toStringOutput | Karakterlánc | helló világ! |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/datauri.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/datauri.json
```

<a id="empty" /> 

## <a name="empty"></a>üres
`empty(itemToTest)`

Meghatározza, hogy egy tömb, az objektum, vagy a karakterlánc üres.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| itemToTest |Igen |a tömb, objektum vagy karakterlánc |Ellenőrizze a esetén üres érték. |

### <a name="return-value"></a>Visszatérési érték

Beolvasása **igaz** értéke üres, ha sikertelen, ha **hamis**.

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) ellenőrzi, hogy egy tömb, az objektumot, és a karakterlánc üres.

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

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| arrayEmpty | logikai érték | True (Igaz) |
| objectEmpty | logikai érték | True (Igaz) |
| stringEmpty | logikai érték | True (Igaz) |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

<a id="endswith" />

## <a name="endswith"></a>megadott módon végződő
`endsWith(stringToSearch, stringToFind)`

Meghatározza, hogy egy karakterláncot végződik-e értéket. Eredményű összehasonlítás esetén azonban nem.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| stringToSearch |Igen |karakterlánc |Az érték, amely tartalmazza az elem található. |
| stringToFind |Igen |karakterlánc |Az érték kereséséhez. |

### <a name="return-value"></a>Visszatérési érték

**Igaz** Ha az utolsó karaktereit a karakterlánc megfelel a érték; ellenkező esetben **hamis**.

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) a megadott módon kezdődő és megadott módon végződő funkciók használatát ismerteti:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| startsTrue | logikai érték | True (Igaz) |
| startsCapTrue | logikai érték | True (Igaz) |
| startsFalse | logikai érték | False (Hamis) |
| endsTrue | logikai érték | True (Igaz) |
| endsCapTrue | logikai érték | True (Igaz) |
| endsFalse | logikai érték | False (Hamis) |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/startsendswith.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/startsendswith.json
```

<a id="first" />

## <a name="first"></a>első
`first(arg1)`

A karakterlánc, vagy a tömb első eleme első karaktert adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |a tömb vagy karakterlánc |Az érték első karakter vagy elem lekéréséhez. |

### <a name="return-value"></a>Visszatérési érték

Egy karakterlánc az első karakter, vagy a tömb első elem típusa (karakterlánc, int, tömb vagy objektum).

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) bemutatja, hogyan használható az első függvényét egy tömb és a karakterlánc.

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

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| arrayOutput | Karakterlánc | egy |
| stringOutput | Karakterlánc | O |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

## <a name="guid"></a>GUID

`guid (baseString, ...)`

Létrehoz egy értéket a paraméterként megadott értékek alapján a globálisan egyedi azonosító formátuma.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| baseString |Igen |karakterlánc |Az értéknek a kivonatoló függvényt a GUID létrehozásához. |
| szükség szerint további paraméterek |Nem |karakterlánc |Az érték, amely meghatározza a egyediségi szintű létrehozásához szükséges annyi karakterláncok adhat hozzá. |

### <a name="remarks"></a>Megjegyzések

Ez a funkció akkor hasznos, ha egy értéket a globálisan egyedi azonosítót kell létrehozásához szükséges. Az eredmény egyediségének hatókörét korlátozó paraméter értékeket ad meg. Megadhatja, hogy egyedi előfizetés, a csoport vagy a központi telepítési le-e a neve.

A visszaadott érték nem véletlenszerű karakterlánc, hanem inkább a kivonatoló függvényt eredményét. A visszaadott érték 36 karakter. Nincs globálisan egyedi.

A következő példák szemléltetik a GUID azonosító használatával hozzon létre egy egyedi értéket a gyakran használt szintek.

Egyedi előfizetés hatóköre

```json
"[guid(subscription().subscriptionId)]"
```

Egyedi erőforráscsoport hatóköre

```json
"[guid(resourceGroup().id)]"
```

Egyedi telepítési erőforrás csoport hatóköre

```json
"[guid(resourceGroup().id, deployment().name)]"
```

### <a name="return-value"></a>Visszatérési érték

A globálisan egyedi azonosítót kell 36 karakter tartalmazó karakterlánc.

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json) eredményeket ad vissza guid:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [],
    "outputs": {
        "guidPerSubscription": {
            "value": "[guid(subscription().subscriptionId)]",
            "type": "string"
        },
        "guidPerResourceGroup": {
            "value": "[guid(resourceGroup().id)]",
            "type": "string"
        },
        "guidPerDeployment": {
            "value": "[guid(resourceGroup().id, deployment().name)]",
            "type": "string"
        }
    }
}
```

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/guid.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/guid.json
```

<a id="indexof" />

## <a name="indexof"></a>indexOf
`indexOf(stringToSearch, stringToFind)`

Egy értékének a karakterláncon belüli első helyét adja vissza. Eredményű összehasonlítás esetén azonban nem.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| stringToSearch |Igen |karakterlánc |Az érték, amely tartalmazza az elem található. |
| stringToFind |Igen |karakterlánc |Az érték kereséséhez. |

### <a name="return-value"></a>Visszatérési érték

Az elem található a pozíció jelölő egész. Az érték nulla alapú. Ha az elem nem található,-1 értéket ad vissza.

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) a indexOf és lastIndexOf funkciók használatát ismerteti:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/indexof.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/indexof.json
```

<a id="last" />

## <a name="last"></a>utolsó
`last (arg1)`

Az utolsó karakter a karakterlánc vagy tömb utolsó eleme adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |a tömb vagy karakterlánc |Az érték utolsó karakter vagy elem lekéréséhez. |

### <a name="return-value"></a>Visszatérési érték

Az utolsó karakter, vagy a tömb utolsó eleme típusa (karakterlánc, int, tömb vagy objektum) formátumú karakterlánc.

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) bemutatja, hogyan használható az utolsó függvény egy tömb és a karakterlánc.

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

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| arrayOutput | Karakterlánc | három |
| stringOutput | Karakterlánc | E |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

<a id="lastindexof" />

## <a name="lastindexof"></a>lastIndexOf
`lastIndexOf(stringToSearch, stringToFind)`

Egy értékének a karakterláncon belüli utolsó helyét adja vissza. Eredményű összehasonlítás esetén azonban nem.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| stringToSearch |Igen |karakterlánc |Az érték, amely tartalmazza az elem található. |
| stringToFind |Igen |karakterlánc |Az érték kereséséhez. |

### <a name="return-value"></a>Visszatérési érték

Az elem található utolsó pozícióját jelölő egész. Az érték nulla alapú. Ha az elem nem található,-1 értéket ad vissza.

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) a indexOf és lastIndexOf funkciók használatát ismerteti:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/indexof.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/indexof.json
```

<a id="length" />

## <a name="length"></a>Hossza
`length(string)`

A karakterlánc vagy tömb elemeinek a számú karaktert adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |a tömb vagy karakterlánc |A tömb használni az első elemek, vagy a karakterlánc első karakterek használata. |

### <a name="return-value"></a>Visszatérési érték

Egy egész szám. 

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) egy tömb és a karakterlánc hossza használatát ismerteti:

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

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

<a id="padleft" />

## <a name="padleft"></a>PadLeft
`padLeft(valueToPad, totalLength, paddingCharacter)`

Karakterek hozzáadásával a bal oldali az összes megadott hosszúságú eléréséig jobbra igazított karakterláncot ad vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| valueToPad |Igen |karakterlánc- vagy int |A jobbra igazított érték. |
| totalLength |Igen |int |A visszaadott karakterláncban szereplő karakterek száma. |
| paddingCharacter |Nem |egyetlen karakter |Bal-kitöltési, amíg a teljes hossza nem csökken használandó karakter. Az alapértelmezett érték: szóközzel. |

Ha az eredeti karakterláncot elválasztásához karakterek áll, akkor egyetlen karakter kerülnek.

### <a name="return-value"></a>Visszatérési érték

A karakterlánc a legalább a megadott karakterek száma.

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) bemutatja, hogyan kitölti a felhasználó által megadott paraméter értéke nulla karakter hozzáadásával, amíg eléri a karakterek száma. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123"
        }
    },
    "resources": [],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[padLeft(parameters('testString'),10,'0')]"
        }
    }
}
```

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| stringOutput | Karakterlánc | 0000000123 |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/padleft.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/padleft.json
```

<a id="replace" />

## <a name="replace"></a>cserélje le
`replace(originalString, oldString, newString)`

Egy másik karakterlánc szerepét egy karakterlánc összes előfordulásának új karakterláncot ad vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| originalString |Igen |karakterlánc |Az érték, amelynek egy karakterlánc másik karakterlánc szerepét összes példányát. |
| oldString |Igen |karakterlánc |Az eredeti karakterláncot eltávolítja a karakterláncot. |
| newString |Igen |karakterlánc |A karakterlánc helyett, az eltávolított karakterlánc hozzáadásához. |

### <a name="return-value"></a>Visszatérési érték

A kicserélt karaktert tartalmazó karakterlánc.

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) jeleníti meg az összes kötőjelek eltávolítása a felhasználó által megadott karakterláncot, és a karakterlánc egy részét lecseréli egy másik karakterláncot.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123-123-1234"
        }
    },
    "resources": [],
    "outputs": {
        "firstOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'-', '')]"
        },
        "secodeOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'1234', 'xxxx')]"
        }
    }
}
```

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| firstOutput | Karakterlánc | 1231231234 |
| secodeOutput | Karakterlánc | 123-123-xxxx |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/replace.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/replace.json
```

<a id="skip" />

## <a name="skip"></a>Kihagyása
`skip(originalValue, numberToSkip)`

Egy karakterláncot ad vissza az összes karakter után a megadott számú karaktert, vagy az összes elem tömb után a megadott számú elemet.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| originalValue |Igen |a tömb vagy karakterlánc |A tömb vagy karakterlánc kihagyása használandó. |
| numberToSkip |Igen |int |Elemek vagy kihagyását karakterek száma. Ha ez az érték 0 vagy kisebb, a elemek vagy az karaktere adott vissza. Ha a tömb vagy karakterlánc hossza nagyobb, üres tömb vagy karakterlánc adja vissza. |

### <a name="return-value"></a>Visszatérési érték

Tömb vagy karakterlánc.

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) kihagyja a megadott számú elemet a tömbben, és a megadott számú karaktert egy karakterláncon belül.

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

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| arrayOutput | Tömb | ["három"] |
| stringOutput | Karakterlánc | két három |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

<a id="split" />

## <a name="split"></a>felosztás
`split(inputString, delimiter)`

Tartalmazza a karakterláncrészletet tartalmazza a bemeneti karakterlánc, amely a megadott elválasztók határolja karakterláncok tömbjét adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| inputString |Igen |karakterlánc |Ossza fel a karakterláncot. |
| Elválasztó |Igen |karakterláncot vagy karakterláncok |A karakterlánc a felosztás használandó elválasztó. |

### <a name="return-value"></a>Visszatérési érték

Karakterláncokból álló tömb.

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) felosztja a bemeneti karakterlánc egy vesszővel válassza el egymástól, és vesszővel vagy pontosvesszővel kell elválasztani őket.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstString": {
            "type": "string",
            "defaultValue": "one,two,three"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "one;two,three"
        }
    },
    "variables": {
        "delimiters": [ ",", ";" ]
    },
    "resources": [],
    "outputs": {
        "firstOutput": {
            "type": "array",
            "value": "[split(parameters('firstString'),',')]"
        },
        "secondOutput": {
            "type": "array",
            "value": "[split(parameters('secondString'),variables('delimiters'))]"
        }
    }
}
```

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| firstOutput | Tömb | ["egy", "két", "három"] |
| secondOutput | Tömb | ["egy", "két", "három"] |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/split.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/split.json
```

<a id="startswith" />

## <a name="startswith"></a>startswith elemnek
`startsWith(stringToSearch, stringToFind)`

Meghatározza, hogy egy karakterlánc értékkel kezdődik-e. Eredményű összehasonlítás esetén azonban nem.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| stringToSearch |Igen |karakterlánc |Az érték, amely tartalmazza az elem található. |
| stringToFind |Igen |karakterlánc |Az érték kereséséhez. |

### <a name="return-value"></a>Visszatérési érték

**Igaz** Ha az első karaktereit a karakterlánc megfelel a érték; ellenkező esetben **hamis**.

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) a megadott módon kezdődő és megadott módon végződő funkciók használatát ismerteti:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| startsTrue | logikai érték | True (Igaz) |
| startsCapTrue | logikai érték | True (Igaz) |
| startsFalse | logikai érték | False (Hamis) |
| endsTrue | logikai érték | True (Igaz) |
| endsCapTrue | logikai érték | True (Igaz) |
| endsFalse | logikai érték | False (Hamis) |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/startsendswith.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/startsendswith.json
```

<a id="string" />

## <a name="string"></a>karakterlánc
`string(valueToConvert)`

A megadott érték konvertálása egy karakterláncot.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| valueToConvert |Igen | Bármelyik |Az érték karakterlánccá konvertálni. Bármely típusú érték lehet konvertálni, többek között az objektumok és tömböket. |

### <a name="return-value"></a>Visszatérési érték

Az átalakított érték karakterlánc.

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) különböző típusú értékeket átalakítani karakterláncok szemlélteti:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testObject": {
            "type": "object",
            "defaultValue": {
                "valueA": 10,
                "valueB": "Example Text"
            }
        },
        "testArray": {
            "type": "array",
            "defaultValue": [
                "a",
                "b",
                "c"
            ]
        },
        "testInt": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "resources": [],
    "outputs": {
        "objectOutput": {
            "type": "string",
            "value": "[string(parameters('testObject'))]"
        },
        "arrayOutput": {
            "type": "string",
            "value": "[string(parameters('testArray'))]"
        },
        "intOutput": {
            "type": "string",
            "value": "[string(parameters('testInt'))]"
        }
    }
}
```

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| objectOutput | Karakterlánc | {"valueA": 10, "valueB": "Példaszöveg"} |
| arrayOutput | Karakterlánc | ["a", "b", "c"] |
| intOutput | Karakterlánc | 5 |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/string.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/string.json
```

<a id="substring" />

## <a name="substring"></a>Substring
`substring(stringToParse, startIndex, length)`

Egy részét, amely a megadott Karakterpozíció kezdődik, és tartalmazza a megadott számú karaktert adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| stringToParse |Igen |karakterlánc |Az eredeti karakterláncot, amely a karakterláncrészletet ki kell olvasni. |
| startIndex |Nem |int |A nulla alapú karakter kezdőpozíciója a karakterláncrészletet. |
| Hossza |Nem |int |A substring karakterek száma. A karakterláncon belüli helyet kell képviselnie. |

### <a name="return-value"></a>Visszatérési érték

A karakterláncrészletet.

### <a name="remarks"></a>Megjegyzések

A parancs nem működik, amikor a substring túlnyúlik a karakterlánc végén. Hibával meghiúsul a következő példa "az index és length paraméterek a karakterláncon belüli helyet kell képviselnie. Az index paraméter: "0", a length paraméter: "11", a karakterlánc-paraméter hossza: "10". ".

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json) karakterláncrész kiolvassa a paramétert.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        }
    },
    "resources": [],
    "outputs": {
        "substringOutput": {
            "value": "[substring(parameters('testString'), 4, 3)]",
            "type": "string"
        }
    }
}
```

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| substringOutput | Karakterlánc | kettő |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/substring.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/substring.json
```

<a id="take" />

## <a name="take"></a>hajtsa végre a megfelelő
`take(originalValue, numberToTake)`

A karakterlánc vagy tömb a megadott számú elemet a tömb kezdetétől a kezdetétől a megadott számú karaktert karakterláncot ad vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| originalValue |Igen |a tömb vagy karakterlánc |A tömb vagy karakterlánc az elemek érvénybe. |
| numberToTake |Igen |int |Elemek és érvénybe karakterek száma. Ha ez az érték 0 vagy kisebb, üres tömb vagy karakterlánc adja vissza. Ha a megadott tömb vagy karakterlánc hossza nagyobb, a tömb vagy karakterlánc összes elemet visszaadja a. |

### <a name="return-value"></a>Visszatérési érték

Tömb vagy karakterlánc.

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) a megadott számú elemet az egy olyan tömbből, valamint a karakterlánc karaktereit.

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

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| arrayOutput | Tömb | ["egy", "két"] |
| stringOutput | Karakterlánc | be |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

<a id="tolower" />

## <a name="tolower"></a>toLower
`toLower(stringToChange)`

Kisbetűsre alakítja át a megadott karakterlánc.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| stringToChange |Igen |karakterlánc |Az érték kisbetűsre konvertálja. |

### <a name="return-value"></a>Visszatérési érték

A karakterlánc kisbetűsre alakítja.

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) alakítja át a paraméter értékét, kisbetű és nagybetű.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "toLowerOutput": {
            "value": "[toLower(parameters('testString'))]",
            "type": "string"
        },
        "toUpperOutput": {
            "type": "string",
            "value": "[toUpper(parameters('testString'))]"
        }
    }
}
```

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| toLowerOutput | Karakterlánc | egy két há' |
| toUpperOutput | Karakterlánc | EGY KÉT HÁ' |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/tolower.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/tolower.json
```

<a id="toupper" />

## <a name="toupper"></a>toUpper
`toUpper(stringToChange)`

A megadott karakterlánc nagybetűvel alakítja.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| stringToChange |Igen |karakterlánc |A nagybetűvel átalakítása érték. |

### <a name="return-value"></a>Visszatérési érték

A karakterlánc nagybetűsre alakítja.

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) alakítja át a paraméter értékét, kisbetű és nagybetű.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "toLowerOutput": {
            "value": "[toLower(parameters('testString'))]",
            "type": "string"
        },
        "toUpperOutput": {
            "type": "string",
            "value": "[toUpper(parameters('testString'))]"
        }
    }
}
```

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| toLowerOutput | Karakterlánc | egy két há' |
| toUpperOutput | Karakterlánc | EGY KÉT HÁ' |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/tolower.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/tolower.json
```

<a id="trim" />

## <a name="trim"></a>Trim
`trim (stringToTrim)`

Eltávolítja az összes kezdő és záró üres karaktereket a megadott karakterlánc.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| stringToTrim |Igen |karakterlánc |A vágásokról érték. |

### <a name="return-value"></a>Visszatérési érték

A karakterlánc nélkül kezdő és záró üres karaktereket.

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) levágja a üres karaktereket és a paraméter.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "    one two three   "
        }
    },
    "resources": [],
    "outputs": {
        "return": {
            "type": "string",
            "value": "[trim(parameters('testString'))]"
        }
    }
}
```

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| térjen vissza | Karakterlánc | egy két há' |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/trim.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/trim.json
```

<a id="uniquestring" />

## <a name="uniquestring"></a>uniqueString
`uniqueString (baseString, ...)`

A paraméterként megadott értékek alapján determinisztikus kivonat karakterláncot hoz létre. 

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| baseString |Igen |karakterlánc |Az értéknek a kivonatoló függvényt egy egyedi karakterlánc létrehozásához. |
| szükség szerint további paraméterek |Nem |karakterlánc |Az érték, amely meghatározza a egyediségi szintű létrehozásához szükséges annyi karakterláncok adhat hozzá. |

### <a name="remarks"></a>Megjegyzések

Ez a funkció akkor hasznos, ha kell létrehoznia egy erőforrást egy egyedi nevet. Az eredmény egyediségének hatókörét korlátozó paraméter értékeket ad meg. Megadhatja, hogy egyedi előfizetés, a csoport vagy a központi telepítési le-e a neve. 

A visszaadott érték nem véletlenszerű karakterlánc, hanem inkább a kivonatoló függvényt eredményét. A visszaadott érték 13 karakterig. Nincs globálisan egyedi. Érdemes lehet az érték kombinálhatja az elnevezési, kifejező nevet létrehozni a előtaggal kezdődik. A következő példa bemutatja a visszaadott érték formátuma. A tényleges érték függ a megadott paraméterek.

    tcvhiyu5h2o5o

A következő példák szemléltetik a uniqueString segítségével hozzon létre egy egyedi értéket a gyakran használt szintek.

Egyedi előfizetés hatóköre

```json
"[uniqueString(subscription().subscriptionId)]"
```

Egyedi erőforráscsoport hatóköre

```json
"[uniqueString(resourceGroup().id)]"
```

Egyedi telepítési erőforrás csoport hatóköre

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

A következő példa bemutatja, hogyan hozzon létre egy tárfiókot, az erőforráscsoport alapján egyedi nevét. Az erőforráscsoport belül a név nincs egyedi, ha a megszokott módon.

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```

### <a name="return-value"></a>Visszatérési érték

13 karaktereket tartalmazó karakterláncot.

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) uniquestring eredményeket ad vissza:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "uniqueRG": {
            "value": "[uniqueString(resourceGroup().id)]",
            "type" : "string"
        },
        "uniqueDeploy": {
            "value": "[uniqueString(resourceGroup().id, deployment().name)]",
            "type" : "string"
        }
    }
}
```

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uniquestring.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uniquestring.json
```

<a id="uri" />

## <a name="uri"></a>uri azonosító
`uri (baseUri, relativeUri)`

A baseUri és a relativeUri karakterlánc kombinálásával hoz létre egy abszolút URI.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| a baseUri |Igen |karakterlánc |Az alap URI-azonosító karakterláncot. |
| relativeUri |Igen |karakterlánc |A relatív uri karakterlánc hozzáadása az alap URI-azonosító karakterláncot. |

Az érték a **baseUri** a paraméter egy adott fájlt tartalmazhat, de csak az alap elérési utat használja az URI konstrukciója során. Például, hogy `http://contoso.com/resources/azuredeploy.json` alap URI-azonosítója a baseUri paraméter kiemelve `http://contoso.com/resources/`.

### <a name="return-value"></a>Visszatérési érték

Egy karakterlánc, amely az alapszintű és a relatív értékek abszolút URI.

### <a name="examples"></a>Példák

A következő példa bemutatja, hogyan egy hivatkozást a beágyazott sablonok alapján a szülő-sablon létrehozásához.

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) uri, uriComponent és uriComponentToString használatát ismerteti:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| uriOutput | Karakterlánc | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | Karakterlánc | HTTP%3a%2f%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.JSON |
| toStringOutput | Karakterlánc | http://contoso.com/resources/nested/azuredeploy.json |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uri.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uri.json
```

<a id="uricomponent" />

## <a name="uricomponent"></a>uriComponent
`uricomponent(stringToEncode)`

Kódolja URI.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| stringToEncode |Igen |karakterlánc |Az érték kódolására. |

### <a name="return-value"></a>Visszatérési érték

A URI karakterlánc kódolt érték.

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) uri, uriComponent és uriComponentToString használatát ismerteti:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| uriOutput | Karakterlánc | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | Karakterlánc | HTTP%3a%2f%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.JSON |
| toStringOutput | Karakterlánc | http://contoso.com/resources/nested/azuredeploy.json |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uri.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uri.json
```

<a id="uricomponenttostring" />

## <a name="uricomponenttostring"></a>uriComponentToString
`uriComponentToString(uriEncodedString)`

Adja vissza a URI karakterlánc kódolású érték.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Igen |karakterlánc |Az URI kódolású értékét karakterlánccá konvertálni. |

### <a name="return-value"></a>Visszatérési érték

A dekódolt karakterlánc URI-kódolt érték.

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) uri, uriComponent és uriComponentToString használatát ismerteti:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| uriOutput | Karakterlánc | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | Karakterlánc | HTTP%3a%2f%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.JSON |
| toStringOutput | Karakterlánc | http://contoso.com/resources/nested/azuredeploy.json |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uri.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uri.json
```

## <a name="next-steps"></a>További lépések
* A szakaszok az Azure Resource Manager-sablon ismertetését lásd: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).
* Több sablon egyesíteni, lásd: [kapcsolt sablonok használata az Azure Resource Manager](resource-group-linked-templates.md).
* Megadott számú alkalommal felépítésének egy adott típusú erőforrás létrehozása esetén lásd: [erőforrások több példánya létrehozása az Azure Resource Manager](resource-group-create-multiple.md).
* A sablon létrehozott központi telepítéséről, olvassa el [Azure Resource Manager-sablon az alkalmazás központi telepítését](resource-group-template-deploy.md).

