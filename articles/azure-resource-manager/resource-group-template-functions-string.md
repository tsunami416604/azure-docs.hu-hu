---
title: Az Azure Resource Manager-sablonfüggvények - karakterlánc |} A Microsoft Docs
description: A functions az Azure Resource Manager-sablon használatával munka karakterláncokkal ismerteti.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/08/2019
ms.author: tomfitz
ms.openlocfilehash: bf9faa34c1f0923761ce583c22ba4084d7bd42a8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59278785"
---
# <a name="string-functions-for-azure-resource-manager-templates"></a>Karakterlánc-függvények az Azure Resource Manager-sablonok

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
* [Formátum](#format)
* [guid](#guid)
* [indexOf](#indexof)
* [last](#last)
* [lastIndexOf](#lastindexof)
* [Hossza](#length)
* [newGuid](#newguid)
* [padLeft](#padleft)
* [replace](#replace)
* [skip](#skip)
* [felosztás](#split)
* [startsWith](#startswith)
* [string](#string)
* [substring](#substring)
* [hajtsa végre a megfelelő](#take)
* [toLower](#tolower)
* [toUpper](#toupper)
* [trim](#trim)
* [uniqueString](#uniquestring)
* [uri](#uri)
* [uriComponent](#uricomponent)
* [uriComponentToString](#uricomponenttostring)
* [utcNow](#utcnow)

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

## <a name="format"></a>Formátum

`format(formatString, arg1, arg2, ...)`

Bemeneti értékek-formátumú karakterláncot hoz létre.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| formatString | Igen | sztring | Összetett formázó karakterlánc. |
| arg1 | Igen | karakterlánc, egész szám vagy logikai érték | A formázott karakterláncot foglalandó értéke. |
| További argumentumok | Nem | karakterlánc, egész szám vagy logikai érték | További értékek a formázott karakterláncot. |

### <a name="remarks"></a>Megjegyzések

Ez a függvény egy karakterláncot a sablonban használja. Formázási beállítások szerint használja a [System.String.Format](/dotnet/api/system.string.format) metódus a .NET-ben.

### <a name="examples"></a>Példák

A következő példa sablon a format függvény használatát szemlélteti.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "greeting": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "name": {
            "type": "string",
            "defaultValue": "User"
        },
        "numberToFormat": {
            "type": "int",
            "defaultValue": 8175133
        }
    },
    "resources": [
    ],
    "outputs": {
        "formatTest": {
            "type": "string",
            "value": "[format('{0}, {1}. Formatted number: {2:N0}', parameters('greeting'), parameters('name'), parameters('numberToFormat'))]"
        }
    }
}
```

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| formatTest | String | Hello, a felhasználó. Formázott száma: 8,175,133 |

## <a name="guid"></a>GUID azonosítója

`guid(baseString, ...)`

Létrehoz egy értéket a paraméterként megadott értékek alapján globálisan egyedi azonosító formátuma.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| baseString |Igen |sztring |A GUID létrehozásához a kivonatolási függvény a használt érték. |
| További paraméterek szükséges |Nem |sztring |Tetszőleges számú karakterláncok létrehozása, amely az egyediség szintjét határozza meg az érték szükség szerint adhat hozzá. |

### <a name="remarks"></a>Megjegyzések

Ez a funkció akkor hasznos, ha szeretne létrehozni egy értéket a globálisan egyedi azonosító formátuma. Azt adja meg a paraméterértékeket, hogy az eredmény az egyediség hatókörének korlátozása. Megadhatja, hogy a név egyediségét lefelé az előfizetés, erőforráscsoport vagy üzembe helyezés.

A visszaadott érték nem egy véletlenszerű karakterlánc, de a paramétereket a kivonatoló függvényt inkább az eredménye. A visszaadott érték a 36 karakterből áll. Globálisan egyedi nem. Hozzon létre egy új GUID Azonosítót, amely a paramétereket a kivonat értékével, használja a [newGuid](#newguid) függvény.

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

## <a name="newguid"></a>newGuid

`newGuid()`

A globálisan egyedi azonosító formátuma értéket ad vissza. **Ez a függvény csak használható az alapértelmezett érték az adott paraméter.**

### <a name="remarks"></a>Megjegyzések

Ez a függvény kifejezésben csak használható egy paraméter alapértelmezett értéke. Hiba történt a sablonban máshol funkció használatával adja vissza. A függvény nem engedélyezett a sablont más részein, mert értéket ad vissza egy másik minden alkalommal, amikor azt nevezzük. Ugyanazt a sablont, ugyanazokkal a paraméterekkel telepítése nem megbízható ugyanazt az eredményt.

A newGuid függvény eltér a [guid](#guid) működik, mert ez nem használ paramétereket. Ugyanezt a paramétert a guid hívásakor adja vissza, ugyanazzal az azonosítóval minden alkalommal, amikor. Guid használja, amikor szüksége van egy adott környezetben ugyanaz a GUID megbízhatóan létrehozásához. NewGuid használja, ha minden alkalommal, amikor, például egy tesztkörnyezetben üzembe helyezni erőforrásokat kell egy másik azonosítót.

Ha használja a [lehetőséget egy korábban sikeres üzembe helyezés újratelepíteni](resource-group-template-deploy-rest.md#redeploy-when-deployment-fails), és a korábbi központi telepítés magában foglalja egy paraméter, amely newGuid használja, a paraméter nem újraértékeli. A paraméter értékét a korábbi telepítésből ehelyett automatikusan újra a visszaállítás telepítésben.

Egy tesztkörnyezetben szükség lehet ismételten üzembe helyezése csak rövid ideig élő erőforrások. Ahelyett, hogy egyedi nevük hozhat létre, használhatja a newGuid [uniqueString](#uniquestring) hozhat létre egyedi névvel.

Ügyeljen arra, hogy újbóli üzembe helyezés egy sablont, amely az alapértelmezett érték newGuid függvény támaszkodik. Ismételt üzembe helyezése, és ne adjon meg egy értéket a paraméterhez, amikor a rendszer újraértékeli a függvény. Ha azt szeretné, hozzon létre egy új helyett egy meglévő erőforrás frissítése, adja át a paraméter értékét a korábbi telepítésből.

### <a name="return-value"></a>Vrácená hodnota

A globálisan egyedi azonosító formátuma 36 karaktert tartalmazó karakterlánc.

### <a name="examples"></a>Példák

Az alábbi példa sablont egy új azonosító paraméter jeleníti meg.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "guidValue": {
            "type": "string",
            "defaultValue": "[newGuid()]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "guidOutput": {
            "type": "string",
            "value": "[parameters('guidValue')]"
        }
    }
}
```

Az előző példa kimenetében az egyes központi telepítések az érték, de hasonló lesz:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| guidOutput | sztring | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

Az alábbi példa a newGuid függvény segítségével hozzon létre egy storage-fiók egy egyedi nevet. Ez a sablon előfordulhat, hogy működik a tesztkörnyezetben, ahol a tárfiók egy rövid ideig létezik, és nem újratelepítése.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "guidValue": {
            "type": "string",
            "defaultValue": "[newGuid()]"
        }
    },
    "variables": {
        "storageName": "[concat('storage', uniqueString(parameters('guidValue')))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "West US",
            "apiVersion": "2018-07-01",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {}
        }
    ],
    "outputs": {
        "nameOutput": {
            "type": "string",
            "value": "[variables('storageName')]"
        }
    }
}
```

Az előző példa kimenetében az egyes központi telepítések az érték, de hasonló lesz:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| nameOutput | sztring | storagenziwvyru7uxie |


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

A visszaadott érték nem egy véletlenszerű karakterlánc, de inkább a kivonatolási függvény eredménye. A visszaadott érték 13 karakterig terjedhet. Globálisan egyedi nem. Érdemes úgy, hogy az érték az elnevezési konvenciókhoz, és hozzon létre egy beszédes nevet a előtaggal. Az alábbi példa bemutatja a visszaadott érték formátuma. A tényleges érték a megadott paraméter alapján változik.

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

Az alábbi példa bemutatja, hogyan hozhat létre egy storage-fiókot az erőforráscsoportban alapján egyedi nevét. Az erőforráscsoportban a név nem egyedi, ugyanúgy, mint ha.

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```

Ha szeretne létrehozni egy új egyedi nevet minden alkalommal, amikor a sablon üzembe helyezése, és nem kívánja frissíteni az erőforrás, akkor használhatja a [utcNow](#utcnow) uniqueString függvényt. Ez a megközelítés egy tesztkörnyezetben használhatja. Egy vonatkozó példáért lásd: [utcNow](#utcnow).

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

## <a name="utcnow"></a>utcNow

`utcNow(format)`

A megadott formátumban az aktuális (UTC) dátum/idő értéket ad vissza. Ha nincs formátum van megadva, az ISO 8601 (ÉÉÉÉHHNNIóóppmmZ) formátumot használja. **Ez a függvény csak használható az alapértelmezett érték az adott paraméter.**

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| Formátum |Nem |sztring |Az URI-ként kódolt karakterlánc alakítandó érték. Mindkét [standard formázási karakterláncokat](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy [egyéni formázási karakterláncokat](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Megjegyzések

Ez a függvény kifejezésben csak használható egy paraméter alapértelmezett értéke. Hiba történt a sablonban máshol funkció használatával adja vissza. A függvény nem engedélyezett a sablont más részein, mert értéket ad vissza egy másik minden alkalommal, amikor azt nevezzük. Ugyanazt a sablont, ugyanazokkal a paraméterekkel telepítése nem megbízható ugyanazt az eredményt.

Ha használja a [lehetőséget egy korábban sikeres üzembe helyezés újratelepíteni](resource-group-template-deploy-rest.md#redeploy-when-deployment-fails), és a korábbi központi telepítés magában foglalja egy paraméter, amely utcNow használja, a paraméter nem újraértékeli. A paraméter értékét a korábbi telepítésből ehelyett automatikusan újra a visszaállítás telepítésben.

Ügyeljen arra, hogy újbóli üzembe helyezés egy sablont, amely az alapértelmezett érték utcNow függvény támaszkodik. Ismételt üzembe helyezése, és ne adjon meg egy értéket a paraméterhez, amikor a rendszer újraértékeli a függvény. Ha azt szeretné, hozzon létre egy új helyett egy meglévő erőforrás frissítése, adja át a paraméter értékét a korábbi telepítésből.

### <a name="return-value"></a>Vrácená hodnota

A jelenlegi UTC-dátum/idő érték.

### <a name="examples"></a>Példák

A következő példa sablon különböző formátumokban datetime értéket jeleníti meg.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcValue": {
            "type": "string",
            "defaultValue": "[utcNow()]"
        },
        "utcShortValue": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "utcCustomValue": {
            "type": "string",
            "defaultValue": "[utcNow('M d')]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "utcOutput": {
            "type": "string",
            "value": "[parameters('utcValue')]"
        },
        "utcShortOutput": {
            "type": "string",
            "value": "[parameters('utcShortValue')]"
        },
        "utcCustomOutput": {
            "type": "string",
            "value": "[parameters('utcCustomValue')]"
        }
    }
}
```

Az előző példa kimenetében az egyes központi telepítések az érték, de hasonló lesz:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| utcOutput | sztring | 20190305T175318Z |
| utcShortOutput | sztring | 03/05/2019 |
| utcCustomOutput | sztring | 3 5 |

A következő példa bemutatja, hogyan használható olyan érték, a függvény, amely címkeértékkel beállításakor.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "rgName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "name": "[parameters('rgName')]",
            "location": "westeurope",
            "tags":{
                "createdDate": "[parameters('utcShort')]"
            },
            "properties":{}
        }
    ],
    "outputs": {
        "utcShort": {
            "type": "string",
            "value": "[parameters('utcShort')]"
        }
    }
}
```

## <a name="next-steps"></a>További lépések
* A szakaszok az Azure Resource Manager-sablon ismertetését lásd: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).
* Több sablon egyesíteni, lásd: [kapcsolt sablonok használata az Azure Resource Manager](resource-group-linked-templates.md).
* A megadott számú alkalommal újrafuttathatja egy adott típusú erőforrás létrehozásakor, lásd: [több erőforráspéldány létrehozása az Azure Resource Manager](resource-group-create-multiple.md).
* A létrehozott sablon üzembe helyezése, olvassa el [alkalmazás üzembe helyezése Azure Resource Manager-sablonnal](resource-group-template-deploy.md).

