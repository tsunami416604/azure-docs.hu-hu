---
title: Az Azure Resource Manager-sablonfüggvények - karakterlánc |} A Microsoft Docs
description: A functions az Azure Resource Manager-sablon használatával munka karakterláncokkal ismerteti.
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
ms.date: 11/08/2018
ms.author: tomfitz
ms.openlocfilehash: 832c7b0b2aab3cbf09b7ea5e099fcf8be7cd1906
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118037"
---
# <a name="string-functions-for-azure-resource-manager-templates"></a>Karakterlánc-függvények az Azure Resource Manager-sablonok

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A Resource Manager az alábbi funkciókat biztosít a karakterláncokkal való munka:

* [base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [concat](#concat)
* [tartalmaz](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [üres](#empty)
* [endsWith](#endswith)
* [első](#first)
* [guid](#guid)
* [indexOf](#indexof)
* [last](#last)
* [lastIndexOf](#lastindexof)
* [Hossza](#length)
* [padLeft](#padleft)
* [replace](#replace)
* [skip](#skip)
* [felosztás](#split)
* [startsWith](resource-group-template-functions-string.md#startswith)
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

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="base64"></a>Base64
`base64(inputString)`

A bemeneti karakterláncot a base64 kódolású karakterláncként adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| inputString |Igen |sztring |A visszaadandó értéket, mint a base64-ábrázolásból. |

### <a name="return-value"></a>Vrácená hodnota

A base64-ábrázolásból tartalmazó karakterlánc.

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) a Base64 kódolású függvény használatát ismerteti.

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | egy két há' |
| toJsonOutput | Objektum | {"egy": "a", "2": "b"} |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/base64.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/base64.json
```

<a id="base64tojson" />

## <a name="base64tojson"></a>base64ToJson
`base64tojson`

A base64-ábrázolásból konvertálja JSON-objektum.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| base64Value |Igen |sztring |A base64-ábrázolásból átalakítása JSON-objektum. |

### <a name="return-value"></a>Vrácená hodnota

JSON-objektum.

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) base64 érték konvertálása a base64ToJson függvényt használja:

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | egy két há' |
| toJsonOutput | Objektum | {"egy": "a", "2": "b"} |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/base64.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/base64.json
```

<a id="base64tostring" />

## <a name="base64tostring"></a>base64ToString
`base64ToString(base64Value)`

Egy karakterláncot egy base64-ábrázolásból alakítja át.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| base64Value |Igen |sztring |A base64-ábrázolásból alakítandó karakterlánc. |

### <a name="return-value"></a>Vrácená hodnota

Az átalakított base64 érték karakterlánc.

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) base64 érték konvertálása a base64ToString függvényt használja:

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | egy két há' |
| toJsonOutput | Objektum | {"egy": "a", "2": "b"} |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/base64.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/base64.json
```

<a id="concat" />

## <a name="concat"></a>Concat
`concat (arg1, arg2, arg3, ...)`

Több karakterlánc-értékek egyesíti, és a összefűzött karakterláncot ad vissza, vagy több tömbök egyesíti, és a összefűzött tömböt ad vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |karakterlánc- vagy tömb |Összefűzés első értéke. |
| További argumentumok |Nem |sztring |További értékek összefűzésével sorrendben. |

### <a name="return-value"></a>Vrácená hodnota
Egy karakterlánc- vagy összefűzött értékek tömbje.

### <a name="examples"></a>Példák

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
| concatOutput | String | prefix-5yj4yjf5mbg72 |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

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

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

<a id="contains" />

## <a name="contains"></a>tartalmazza a következőt:
`contains (container, itemToFind)`

Ellenőrzi, hogy egy tömb értéket tartalmaz, objektum kulcsot tartalmaz, vagy egy karakterlánc részkarakterláncot tartalmaz. Az adatkarakterlánc-összehasonlítás a kis-és nagybetűket. Azonban történő tesztelésekor, ha az objektum tartalmaz egy kulcsot, az összehasonlítást, kis-és nagybetűket.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| tároló |Igen |tömb, objektumot vagy karakterlánc |Az érték, amely tartalmazza a keresendő érték. |
| itemToFind |Igen |karakterlánc- vagy int |Keresendő érték. |

### <a name="return-value"></a>Vrácená hodnota

**Igaz** , ha az elem nem található; ellenkező esetben **hamis**.

### <a name="examples"></a>Példák

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
| stringTrue | Bool | True (Igaz) |
| stringFalse | Bool | False (Hamis) |
| objectTrue | Bool | True (Igaz) |
| objectFalse | Bool | False (Hamis) |
| arrayTrue | Bool | True (Igaz) |
| arrayFalse | Bool | False (Hamis) |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

<a id="datauri" />

## <a name="datauri"></a>dataUri
`dataUri(stringToConvert)`

Adat-URI alakít egy értéket.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| stringToConvert |Igen |sztring |Az adat-URI átalakítandó érték. |

### <a name="return-value"></a>Vrácená hodnota

Egy karakterláncot egy adat-URI-ként formázott.

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) alakít egy értéket egy adat-URI, és a egy adat-URI alakítja át egy karakterlánc:

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| dataUriOutput | String | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | helló világ! |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/datauri.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/datauri.json
```

<a id="datauritostring" />

## <a name="datauritostring"></a>dataUriToString
`dataUriToString(dataUriToConvert)`

Egy adat-URI érték karakterlánc formátumú.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |Igen |sztring |Az adatok átalakítása URI értéket. |

### <a name="return-value"></a>Vrácená hodnota

Az átalakított érték tartalmazó karakterlánc.

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) alakít egy értéket egy adat-URI, és a egy adat-URI alakítja át egy karakterlánc:

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| dataUriOutput | String | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | helló világ! |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/datauri.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/datauri.json
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

### <a name="examples"></a>Példák

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
| arrayEmpty | Bool | True (Igaz) |
| objectEmpty | Bool | True (Igaz) |
| stringEmpty | Bool | True (Igaz) |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

<a id="endswith" />

## <a name="endswith"></a>endsWith
`endsWith(stringToSearch, stringToFind)`

Azt határozza meg, hogy egy karakterláncot egy adott értékre végződik-e. Az összehasonlítás eredménye kis-és nagybetűket.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| stringToSearch |Igen |sztring |Az érték, amely tartalmazza az elem található. |
| stringToFind |Igen |sztring |Keresendő érték. |

### <a name="return-value"></a>Vrácená hodnota

**Igaz** Ha az utolsó karakter vagy karaktert a karakterlánc megfelel a értéket; ellenkező esetben **hamis**.

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) bemutatja, hogyan használja a startsWith és endsWith függvényt:

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| startsTrue | Bool | True (Igaz) |
| startsCapTrue | Bool | True (Igaz) |
| startsFalse | Bool | False (Hamis) |
| endsTrue | Bool | True (Igaz) |
| endsCapTrue | Bool | True (Igaz) |
| endsFalse | Bool | False (Hamis) |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/startsendswith.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/startsendswith.json
```

<a id="first" />

## <a name="first"></a>első
`first(arg1)`

A karakterlánc, vagy a tömb első eleme első karaktert adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |tömb vagy karakterlánc |Az érték első karakter vagy elem lekéréséhez. |

### <a name="return-value"></a>Vrácená hodnota

Egy karakterlánc első karaktere, vagy a típusa (karakterlánc, int, tömb vagy objektum) az első elem a tömbben.

### <a name="examples"></a>Példák

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
| arrayOutput | String | egy |
| stringOutput | String | O |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

## <a name="guid"></a>GUID azonosítója

`guid (baseString, ...)`

Létrehoz egy értéket a paraméterként megadott értékek alapján globálisan egyedi azonosító formátuma.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| baseString |Igen |sztring |A GUID létrehozásához a kivonatolási függvény a használt érték. |
| További paraméterek szükséges |Nem |sztring |Tetszőleges számú karakterláncok létrehozása, amely az egyediség szintjét határozza meg az érték szükség szerint adhat hozzá. |

### <a name="remarks"></a>Megjegyzések

Ez a funkció akkor hasznos, ha szeretne létrehozni egy értéket a globálisan egyedi azonosító formátuma. Azt adja meg a paraméterértékeket, hogy az eredmény az egyediség hatókörének korlátozása. Megadhatja, hogy a név egyediségét lefelé az előfizetés, erőforráscsoport vagy üzembe helyezés.

A visszaadott érték nem egy véletlenszerű karakterlánc, hanem inkább a kivonatolási függvény eredménye. A visszaadott érték a 36 karakterből áll. Nem globálisan egyedi.

Az alábbi példák bemutatják, hogyan hozzon létre egy egyedi értéket a gyakran használt szintek guid használatával.

Egyedi előfizetés hatóköre

```json
"[guid(subscription().subscriptionId)]"
```

Egyedi erőforráscsoport hatóköre

```json
"[guid(resourceGroup().id)]"
```

Egyedi erőforráscsoport üzembe helyezési hatóköre

```json
"[guid(resourceGroup().id, deployment().name)]"
```

### <a name="return-value"></a>Vrácená hodnota

A globálisan egyedi azonosító formátuma 36 karaktert tartalmazó karakterlánc.

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json) eredményeket ad vissza a GUID azonosítót:

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

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/guid.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/guid.json
```

<a id="indexof" />

## <a name="indexof"></a>indexOf
`indexOf(stringToSearch, stringToFind)`

Egy sztringbeli érték első pozícióját adja vissza. Az összehasonlítás eredménye kis-és nagybetűket.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| stringToSearch |Igen |sztring |Az érték, amely tartalmazza az elem található. |
| stringToFind |Igen |sztring |Keresendő érték. |

### <a name="return-value"></a>Vrácená hodnota

Az elem található a pozíció jelölő egész szám. Az érték nulla alapú. Ha az elem nem található,-1 értéket ad vissza.

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) bemutatja, hogyan használja a indexOf és lastIndexOf függvényt:

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| NotFound | Int | -1 |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/indexof.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/indexof.json
```

<a id="last" />

## <a name="last"></a>utolsó
`last (arg1)`

Az utolsó karaktert a karakterlánc, vagy az utolsó elem a tömb értéket ad vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |tömb vagy karakterlánc |Az érték utolsó karakter vagy elem lekéréséhez. |

### <a name="return-value"></a>Vrácená hodnota

Az utolsó karakter, vagy a típusát (karakterlánc, int, tömb vagy objektum) az utolsó elem a tömbben, karakterlánc.

### <a name="examples"></a>Példák

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
| arrayOutput | String | három |
| stringOutput | String | e |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

<a id="lastindexof" />

## <a name="lastindexof"></a>lastIndexOf
`lastIndexOf(stringToSearch, stringToFind)`

Egy sztringbeli érték utolsó pozícióját adja vissza. Az összehasonlítás eredménye kis-és nagybetűket.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| stringToSearch |Igen |sztring |Az érték, amely tartalmazza az elem található. |
| stringToFind |Igen |sztring |Keresendő érték. |

### <a name="return-value"></a>Vrácená hodnota

Egész szám, amely az utolsó pozíciója az elem található jelöli. Az érték nulla alapú. Ha az elem nem található,-1 értéket ad vissza.

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) bemutatja, hogyan használja a indexOf és lastIndexOf függvényt:

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| NotFound | Int | -1 |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/indexof.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/indexof.json
```

<a id="length" />

## <a name="length"></a>Hossza
`length(string)`

Egy karakterlánc vagy egy tömb elemei a karakterek számát adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |tömb vagy karakterlánc |A tömb első karakterek használata az elemeket, vagy a karakterlánc első használatával. |

### <a name="return-value"></a>Vrácená hodnota

Egy: egész szám. 

### <a name="examples"></a>Példák

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

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

<a id="padleft" />

## <a name="padleft"></a>padLeft
`padLeft(valueToPad, totalLength, paddingCharacter)`

Karakterek hozzáadásával a bal oldali amíg végállapotba megadott teljes hossza nem egy jobbra igazított karakterláncot ad vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| valueToPad |Igen |karakterlánc- vagy int |A jobbra igazított érték. |
| totalLength |Igen |int |A visszaadott karakterláncban szereplő karakterek száma. |
| paddingCharacter |Nem |egyetlen karakter |Bal-kitöltés, amíg a teljes hossza nem csökken használandó elválasztó karakter. Az alapértelmezett értéke egy szóközt. |

Ha az eredeti karakterláncot szegélynél karakterből áll, akkor egyetlen karakter kerülnek.

### <a name="return-value"></a>Vrácená hodnota

A karakterlánc a legalább a megadott karakterek száma.

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) bemutatja, hogyan kitölti a felhasználó által megadott paraméter értéke nulla karakter hozzáadásával, addig a teljes karakterszámot. 

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| stringOutput | String | 0000000123 |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/padleft.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/padleft.json
```

<a id="replace" />

## <a name="replace"></a>cserélje le
`replace(originalString, oldString, newString)`

Egy karakterlánc egy másik karakterláncot lecserélve az összes példányát új karakterláncot ad vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| originalString |Igen |sztring |Az érték, amelynek egy karakterlánc egy másik karakterláncot lecserélve az összes példányát. |
| oldString |Igen |sztring |A karakterlánc távolítható el az eredeti karakterláncot. |
| Újkarakterlánc |Igen |sztring |A karakterlánc hozzáadása az eltávolított karakterlánc helyett. |

### <a name="return-value"></a>Vrácená hodnota

Lecserélt karaktert tartalmazó karakterlánc.

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) jeleníti meg az összes kötőjeleket eltávolítása a felhasználó által megadott karakterláncot, és hogyan a karakterlánc egy részét lecseréli egy másik karakterláncot.

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| firstOutput | String | 1231231234 |
| secodeOutput | String | 123-123-xxxx |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/replace.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/replace.json
```

<a id="skip" />

## <a name="skip"></a>kihagyás
`skip(originalValue, numberToSkip)`

Egy karakterláncot ad vissza az összes karakter után a megadott számú karakter vagy elemeket egy tömb a megadott számú elem után.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| originalValue |Igen |tömb vagy karakterlánc |A tömböt vagy karakterláncot, használja a rendszer kihagyja. |
| numberToSkip |Igen |int |Elemek vagy hagyja ki a karakterek száma. Ha ez az érték 0 vagy kisebb, a elemek vagy az érték karaktereinek adott vissza. Ha a tömb vagy karakterlánc hossza nagyobb, egy üres tömb vagy karakterlánc adja vissza. |

### <a name="return-value"></a>Vrácená hodnota

Egy tömb vagy karakterlánc.

### <a name="examples"></a>Példák

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
| stringOutput | String | két három |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

<a id="split" />

## <a name="split"></a>felosztás
`split(inputString, delimiter)`

Karakterláncok tömbje, amely tartalmazza a oszt fel a bemeneti karakterláncot, amely a megadott elválasztók vannak elválasztva adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| inputString |Igen |sztring |A felosztandó karakterlánc. |
| elválasztó karakter |Igen |karakterláncot vagy karakterláncok tömbje |A sztring felosztásához használt elválasztó karaktert. |

### <a name="return-value"></a>Vrácená hodnota

Karakterláncok tömbje.

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) bontja a bemeneti karakterláncot vesszővel válasszon el, és a egy vesszővel vagy pontosvesszővel.

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| firstOutput | Tömb | ["egy", "két", "három"] |
| secondOutput | Tömb | ["egy", "két", "három"] |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/split.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/split.json
```

<a id="startswith" />

## <a name="startswith"></a>startsWith
`startsWith(stringToSearch, stringToFind)`

Azt határozza meg, hogy egy karakterláncot egy adott értékkel kezdődik-e. Az összehasonlítás eredménye kis-és nagybetűket.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| stringToSearch |Igen |sztring |Az érték, amely tartalmazza az elem található. |
| stringToFind |Igen |sztring |Keresendő érték. |

### <a name="return-value"></a>Vrácená hodnota

**Igaz** Ha az első karaktert vagy karaktereket a karakterlánc megfelel a értéket; ellenkező esetben **hamis**.

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) bemutatja, hogyan használja a startsWith és endsWith függvényt:

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| startsTrue | Bool | True (Igaz) |
| startsCapTrue | Bool | True (Igaz) |
| startsFalse | Bool | False (Hamis) |
| endsTrue | Bool | True (Igaz) |
| endsCapTrue | Bool | True (Igaz) |
| endsFalse | Bool | False (Hamis) |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/startsendswith.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/startsendswith.json
```

<a id="string" />

## <a name="string"></a>sztring
`string(valueToConvert)`

A megadott érték konvertálása egy karakterláncot.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| valueToConvert |Igen | Bármelyik |Karakterlánc alakítandó érték. Bármilyen típusú érték lehet konvertálni, többek között az objektumok és tömböket. |

### <a name="return-value"></a>Vrácená hodnota

Az átalakított érték karakterlánc.

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) különböző típusú értékek konvertálása karakterláncokat jeleníti meg:

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| objectOutput | String | {"valueA":10,"valueB":"Example Text"} |
| arrayOutput | String | ["a", "b", "c"] |
| intOutput | String | 5 |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/string.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/string.json
```

<a id="substring" />

## <a name="substring"></a>karakterláncrészlet
`substring(stringToParse, startIndex, length)`

Egy részét, amely a megadott Karakterpozíció kezdődik, és tartalmazza a megadott számú karaktert adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| stringToParse |Igen |sztring |Az eredeti karakterláncot, amely az a karakterláncrészletet ki kell olvasni. |
| startIndex |Nem |int |A nulla alapú kezdő karakter pozíciójának a karakterláncrészletet. |
| Hossza |Nem |int |A substring karakterek száma. A karakterláncon belüli helyre kell hivatkoznia. Lehet nulla vagy nagyobb. |

### <a name="return-value"></a>Vrácená hodnota

A karakterláncrészletet. Vagy ha az érték nulla üres karakterlánc.

### <a name="remarks"></a>Megjegyzések

A függvény sikertelen lesz, ha a substring túlnyúlik a sztring végén, vagy ha hossza kisebb nullánál. A következő hibával meghiúsul a következő példa "az index és a hossz paraméternek a karakterláncon belüli helyre kell hivatkoznia. Az index paraméter: "0", a length paraméter: "11", a karakterlánc-paraméter hossza: '10'.".

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json) részkarakterláncot kigyűjti a paramétert.

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| substringOutput | String | kettő |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/substring.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/substring.json
```

<a id="take" />

## <a name="take"></a>hajtsa végre a megfelelő
`take(originalValue, numberToTake)`

A megadott számú karaktert karakterláncot ad vissza. a karakterlánc vagy egy tömb a megadott számú elem a tömb a kezdetektől az elejéről.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| originalValue |Igen |tömb vagy karakterlánc |A tömböt vagy karakterláncot, hogy az elemek. |
| numberToTake |Igen |int |Elemek vagy elvégzendő karakterek száma. Ha ez az érték 0 vagy kisebb, egy üres tömb vagy karakterlánc adja vissza. Ha nagyobb, mint a megadott tömb vagy karakterlánc hossza, a rendszer tömb vagy karakterlánc összes elemét adja vissza. |

### <a name="return-value"></a>Vrácená hodnota

Egy tömb vagy karakterlánc.

### <a name="examples"></a>Példák

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
| stringOutput | String | be |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

<a id="tolower" />

## <a name="tolower"></a>toLower
`toLower(stringToChange)`

Kisbetűsre alakítja át a megadott karakterlánc.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| stringToChange |Igen |sztring |Kisbetű alakítandó érték. |

### <a name="return-value"></a>Vrácená hodnota

A karakterlánc kisbetűsre konvertálja.

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) kisbetű, nagybetű és a egy paraméter értéke alakítja át.

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| toLowerOutput | String | egy két há' |
| toUpperOutput | String | EGY KÉT HÁ' |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/tolower.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/tolower.json
```

<a id="toupper" />

## <a name="toupper"></a>toUpper
`toUpper(stringToChange)`

A megadott karakterlánc nagybetűs konvertálja.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| stringToChange |Igen |sztring |A nagybetűs alakítandó érték. |

### <a name="return-value"></a>Vrácená hodnota

A karakterlánc nagybetűs dátumformátumra alakítja át.

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) kisbetű, nagybetű és a egy paraméter értéke alakítja át.

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| toLowerOutput | String | egy két há' |
| toUpperOutput | String | EGY KÉT HÁ' |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/tolower.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/tolower.json
```

<a id="trim" />

## <a name="trim"></a>Trim
`trim (stringToTrim)`

A megadott karakterlánc eltávolítja az összes kezdő és záró elválasztó karaktert.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| stringToTrim |Igen |sztring |Az érték, amelyből törölni kell. |

### <a name="return-value"></a>Vrácená hodnota

A karakterlánc nélkül kezdő és záró elválasztó karaktert.

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) az üres karaktereket a paraméterből származó levágja.

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| a visszaadandó | String | egy két há' |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/trim.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/trim.json
```

<a id="uniquestring" />

## <a name="uniquestring"></a>uniqueString
`uniqueString (baseString, ...)`

A paraméterként megadott értékek alapján determinisztikus kivonat karakterláncot hoz létre. 

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| baseString |Igen |sztring |Hozzon létre egy egyedi karakterlánccá, a kivonatoló függvényt a használt érték. |
| További paraméterek szükséges |Nem |sztring |Tetszőleges számú karakterláncok létrehozása, amely az egyediség szintjét határozza meg az érték szükség szerint adhat hozzá. |

### <a name="remarks"></a>Megjegyzések

Ez a funkció akkor hasznos, ha szeretne létrehozni egy egyedi nevet. Azt adja meg a paraméterértékeket, hogy az eredmény az egyediség hatókörének korlátozása. Megadhatja, hogy a név egyediségét lefelé az előfizetés, erőforráscsoport vagy üzembe helyezés. 

A visszaadott érték nem egy véletlenszerű karakterlánc, hanem inkább a kivonatolási függvény eredménye. A visszaadott érték 13 karakterig terjedhet. Nem globálisan egyedi. Érdemes úgy, hogy az érték az elnevezési konvenciókhoz, és hozzon létre egy beszédes nevet a előtaggal. Az alábbi példa bemutatja a visszaadott érték formátuma. A tényleges érték a megadott paraméter alapján változik.

    tcvhiyu5h2o5o

Az alábbi példák bemutatják, hogyan hozzon létre egy egyedi értéket a gyakran használt szintek uniqueString használatával.

Egyedi előfizetés hatóköre

```json
"[uniqueString(subscription().subscriptionId)]"
```

Egyedi erőforráscsoport hatóköre

```json
"[uniqueString(resourceGroup().id)]"
```

Egyedi erőforráscsoport üzembe helyezési hatóköre

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

Az alábbi példa bemutatja, hogyan hozhat létre egy storage-fiókot az erőforráscsoportban alapján egyedi nevét. Az erőforráscsoportban a név nem egyedi, ha azonos módon.

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```

### <a name="return-value"></a>Vrácená hodnota

13 karaktert tartalmazó karakterlánc.

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) uniquestring eredményeket adja vissza:

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

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uniquestring.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uniquestring.json
```

<a id="uri" />

## <a name="uri"></a>uri azonosító
`uri (baseUri, relativeUri)`

Egyesíti a baseUri és a relativeUri karakterláncot hoz létre absolutní identifikátor URI.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| baseUri |Igen |sztring |Az alap uri karakterlánc. |
| relativeUri |Igen |sztring |A relatív uri karakterlánc hozzáadása az alap uri-karakterláncot. |

Az érték a **baseUri** paraméter egy adott fájlt is tartalmazhat, de csak az alapútvonal létrehozásakor az URI-t használja. Például passing `http://contoso.com/resources/azuredeploy.json` egy alap URI-ját baseUri paraméter eredményként `http://contoso.com/resources/`.

### <a name="return-value"></a>Vrácená hodnota

Az abszolút URI Azonosítónak a kiinduló és relatív értékeket képviselő karakterláncot.

### <a name="examples"></a>Példák

Az alábbi példa bemutatja, hogyan hozhat létre az érték a fölérendelt sablon alapján egy beágyazott sablont mutató hivatkozást.

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) uri uriComponent és uriComponentToString használatát ismerteti:

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| uriOutput | String | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | String | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | String | http://contoso.com/resources/nested/azuredeploy.json |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uri.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uri.json
```

<a id="uricomponent" />

## <a name="uricomponent"></a>uriComponent
`uricomponent(stringToEncode)`

Kódol egy URI-t.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| stringToEncode |Igen |sztring |A kódolni kívánt érték. |

### <a name="return-value"></a>Vrácená hodnota

Az URI karakterláncként kódolt érték.

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) uri uriComponent és uriComponentToString használatát ismerteti:

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| uriOutput | String | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | String | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | String | http://contoso.com/resources/nested/azuredeploy.json |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uri.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uri.json
```

<a id="uricomponenttostring" />

## <a name="uricomponenttostring"></a>uriComponentToString
`uriComponentToString(uriEncodedString)`

Visszaadása egy URI karakterláncként kódolt érték.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Igen |sztring |Az URI-ként kódolt karakterlánc alakítandó érték. |

### <a name="return-value"></a>Vrácená hodnota

A dekódolt karakterlánc URI érték kódolva.

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) uri uriComponent és uriComponentToString használatát ismerteti:

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| uriOutput | String | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | String | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | String | http://contoso.com/resources/nested/azuredeploy.json |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uri.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uri.json
```

## <a name="next-steps"></a>További lépések
* A szakaszok az Azure Resource Manager-sablon ismertetését lásd: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).
* Több sablon egyesíteni, lásd: [kapcsolt sablonok használata az Azure Resource Manager](resource-group-linked-templates.md).
* A megadott számú alkalommal újrafuttathatja egy adott típusú erőforrás létrehozásakor, lásd: [több erőforráspéldány létrehozása az Azure Resource Manager](resource-group-create-multiple.md).
* Ellenőrizze, hogyan helyezheti üzembe a létrehozott sablont, tekintse meg a [alkalmazás üzembe helyezése Azure Resource Manager-sablonnal](resource-group-template-deploy.md).

