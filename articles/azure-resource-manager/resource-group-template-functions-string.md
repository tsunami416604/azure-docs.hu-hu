---
title: Azure Resource Manager template functions-string | Microsoft Docs
description: A Azure Resource Manager-sablonban a sztringekkel való együttműködéshez használt függvényeket ismerteti.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: tomfitz
ms.openlocfilehash: c30bb47f3f35663a6ffcfc0126758eb82c9dec4e
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194780"
---
# <a name="string-functions-for-azure-resource-manager-templates"></a>Karakterlánc-függvények Azure Resource Manager-sablonokhoz

A Resource Manager a következő függvényeket biztosítja a karakterláncok használatához:

* [base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [concat](#concat)
* [contains](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [empty](#empty)
* [endsWith](#endswith)
* [first](#first)
* [format](#format)
* [guid](#guid)
* [indexOf](#indexof)
* [last](#last)
* [lastIndexOf](#lastindexof)
* [length](#length)
* [newGuid](#newguid)
* [padLeft](#padleft)
* [replace](#replace)
* [skip](#skip)
* [split](#split)
* [startsWith](#startswith)
* [string](#string)
* [substring](#substring)
* [take](#take)
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

A bemeneti karakterlánc Base64-ábrázolását adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| inputString |Igen |Karakterlánc |Az az érték, amelyet Base64-ábrázolásként kell visszaadni. |

### <a name="return-value"></a>Vrácená hodnota

A Base64-ábrázolást tartalmazó karakterlánc.

### <a name="examples"></a>Példák

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) a Base64 függvény használatát mutatja be.

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

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| base64Output | Sztring | b25lLCB0d28sIHRocmVl |
| toStringOutput | Sztring | egy két há' |
| toJsonOutput | Object | {"One": "a", "kettő": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

Base64-leképezést alakít át egy JSON-objektumra.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| Base64value tulajdonsága |Igen |Karakterlánc |A JSON-objektumra konvertálandó Base64-ábrázolás. |

### <a name="return-value"></a>Vrácená hodnota

Egy JSON-objektum.

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) a base64ToJson függvényt használja egy Base64 érték átalakítására:

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

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| base64Output | Sztring | b25lLCB0d28sIHRocmVl |
| toStringOutput | Sztring | egy két há' |
| toJsonOutput | Object | {"One": "a", "kettő": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

Base64-ábrázolást konvertál karakterlánccá.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| Base64value tulajdonsága |Igen |Karakterlánc |A Base64-ábrázolás, amelyet karakterlánccá kell alakítani. |

### <a name="return-value"></a>Vrácená hodnota

Az átalakított Base64-érték karakterlánca.

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) a base64ToString függvényt használja egy Base64 érték átalakítására:

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

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| base64Output | Sztring | b25lLCB0d28sIHRocmVl |
| toStringOutput | Sztring | egy két há' |
| toJsonOutput | Object | {"One": "a", "kettő": "b"} |

## <a name="concat"></a>concat

`concat (arg1, arg2, arg3, ...)`

Több karakterlánc-értéket egyesít, és visszaadja az összefűzött karakterláncot, vagy kombinálja a több tömböt, és visszaadja az összefűzött tömböt.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |karakterlánc vagy tömb |Az Összefűzés első értéke. |
| További argumentumok |Nem |Karakterlánc |További értékek szekvenciális sorrendben az összefűzéshez. |

### <a name="return-value"></a>Vrácená hodnota
Az összefűzött értékek karakterlánca vagy tömbje.

### <a name="examples"></a>Példák

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| concatOutput | Sztring | prefix-5yj4yjf5mbg72 |

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| visszatérési | Array | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

## <a name="contains"></a>contains

`contains (container, itemToFind)`

Ellenőrzi, hogy egy tömb tartalmaz-e értéket, egy objektum tartalmaz-e kulcsot, vagy egy karakterlánc tartalmaz-e alkarakterláncot. A karakterlánc-összehasonlítás megkülönbözteti a kis-és nagybetűket. Ha azonban egy objektum kulcsot tartalmaz, az összehasonlítás a kis-és nagybetűk megkülönböztetése nélkül történik.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| container |Igen |tömb, objektum vagy karakterlánc |A keresendő értéket tartalmazó érték. |
| itemToFind |Igen |karakterlánc vagy int |A keresendő érték. |

### <a name="return-value"></a>Vrácená hodnota

**Igaz** , ha az adott tétel megtalálható; Ellenkező esetben **hamis**.

### <a name="examples"></a>Példák

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| stringTrue | Bool | True |
| stringFalse | Bool | False (Hamis) |
| objectTrue | Bool | True |
| objectFalse | Bool | False (Hamis) |
| arrayTrue | Bool | True |
| arrayFalse | Bool | False (Hamis) |

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

Egy értéket konvertál egy adaturi-ra.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| stringToConvert |Igen |Karakterlánc |Az adaturi-ra konvertálandó érték. |

### <a name="return-value"></a>Vrácená hodnota

Az adaturi-ként formázott karakterlánc.

### <a name="examples"></a>Példák

A következő [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) egy értéket konvertál egy adaturi-ra, és átalakítja az adaturi-t egy karakterlánccá:

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

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| dataUriOutput | Sztring | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | Sztring | helló világ! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

Az adaturi-formázott értéket karakterlánccá alakítja át.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |Igen |Karakterlánc |Az átalakítandó adaturi-érték. |

### <a name="return-value"></a>Vrácená hodnota

Az átalakított értéket tartalmazó sztring.

### <a name="examples"></a>Példák

A következő [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) egy értéket konvertál egy adaturi-ra, és átalakítja az adaturi-t egy karakterlánccá:

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

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| dataUriOutput | Sztring | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | Sztring | helló világ! |

## <a name="empty"></a>empty

`empty(itemToTest)`

Meghatározza, hogy egy tömb, objektum vagy karakterlánc üres-e.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| itemToTest |Igen |tömb, objektum vagy karakterlánc |Az érték, amely alapján ellenőrizhető, hogy üres-e. |

### <a name="return-value"></a>Vrácená hodnota

**Igaz** értéket ad vissza, ha az érték üres. Ellenkező esetben **hamis**.

### <a name="examples"></a>Példák

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True |
| objectEmpty | Bool | True |
| stringEmpty | Bool | True |

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

Meghatározza, hogy egy karakterlánc egy értékkel végződik-e. Az összehasonlítás megkülönbözteti a kis-és nagybetűket.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| stringToSearch |Igen |Karakterlánc |A keresendő elemeket tartalmazó érték. |
| stringToFind |Igen |Karakterlánc |A keresendő érték. |

### <a name="return-value"></a>Vrácená hodnota

**Igaz** , ha a sztring utolsó karaktere vagy karaktere megegyezik az értékkel; Ellenkező esetben **hamis**.

### <a name="examples"></a>Példák

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) a startsWith és a endsWith függvények használatát mutatja be:

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

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| startsTrue | Bool | True |
| startsCapTrue | Bool | True |
| startsFalse | Bool | False (Hamis) |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False (Hamis) |

## <a name="first"></a>első

`first(arg1)`

A karakterlánc első karakterét vagy a tömb első elemét adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |tömb vagy karakterlánc |Az első elem vagy karakter beolvasására szolgáló érték. |

### <a name="return-value"></a>Vrácená hodnota

Egy tömb első elemének első karakterének vagy típusának (karakterlánc, int, Array vagy Object) a karakterlánca.

### <a name="examples"></a>Példák

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| arrayOutput | Sztring | egy |
| stringOutput | Sztring | O |

## <a name="format"></a>format

`format(formatString, arg1, arg2, ...)`

Létrehoz egy formázott karakterláncot a bemeneti értékek közül.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| formatString | Igen | Karakterlánc | Az összetett formátum karakterlánca |
| arg1 | Igen | string, integer vagy boolean | A formázott sztringbe felvenni kívánt érték. |
| További argumentumok | Nem | string, integer vagy boolean | A formázott sztringbe foglalandó további értékek. |

### <a name="remarks"></a>Megjegyzések

Ezzel a függvénnyel formázhatja a sablonban található karakterláncot. Ugyanazokat a formázási beállításokat használja, mint a [System. string. Format](/dotnet/api/system.string.format) metódus a .net-ben.

### <a name="examples"></a>Példák

A következő példa a Format függvény használatát mutatja be.

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

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| formatTest | Sztring | Hello, felhasználó. Formázott szám: 8 175 133 |

## <a name="guid"></a>GUID

`guid(baseString, ...)`

Egy globálisan egyedi azonosító formájában létrehoz egy értéket a paraméterként megadott értékek alapján.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| baseString |Igen |Karakterlánc |A kivonatoló függvényben a GUID létrehozásához használt érték. |
| további paraméterek igény szerint |Nem |Karakterlánc |Az egyediség szintjét meghatározó érték létrehozásához tetszőleges számú karakterláncot adhat hozzá. |

### <a name="remarks"></a>Megjegyzések

Ez a függvény akkor hasznos, ha globálisan egyedi azonosító formátumban kell létrehoznia egy értéket. Olyan paramétereket adhat meg, amelyek korlátozzák az eredmény egyediségi hatókörét. Megadhatja, hogy a név egyedi-e az előfizetés, az erőforráscsoport vagy a központi telepítés számára.

A visszaadott érték nem véletlenszerű karakterlánc, hanem egy kivonatoló függvény eredménye a paramétereknél. A visszaadott érték 36 karakter hosszú. Globálisan nem egyedi. Ha olyan új GUID azonosítót szeretne létrehozni, amely nem a paraméterek kivonatán alapul, használja a [newGuid](#newguid) függvényt.

Az alábbi példák azt mutatják be, hogyan használható a GUID a gyakran használt szintek egyedi értékének létrehozásához.

Az előfizetéshez tartozó egyedi hatókör

```json
"[guid(subscription().subscriptionId)]"
```

Az erőforráscsoport egyedi hatóköre

```json
"[guid(resourceGroup().id)]"
```

Egy erőforráscsoport üzembe helyezésének egyedi hatóköre

```json
"[guid(resourceGroup().id, deployment().name)]"
```

### <a name="return-value"></a>Vrácená hodnota

Egy 36 karaktert tartalmazó karakterlánc, amely globálisan egyedi azonosító formátumban van.

### <a name="examples"></a>Példák

A következő [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json) az eredményeket adja vissza a GUID azonosítóból:

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

Egy érték első pozícióját adja vissza egy karakterláncon belül. Az összehasonlítás megkülönbözteti a kis-és nagybetűket.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| stringToSearch |Igen |Karakterlánc |A keresendő elemeket tartalmazó érték. |
| stringToFind |Igen |Karakterlánc |A keresendő érték. |

### <a name="return-value"></a>Vrácená hodnota

Egy egész szám, amely a keresett tétel pozícióját jelöli. Az érték nulla-alapú. Ha az elem nem található, a-1 értéket adja vissza.

### <a name="examples"></a>Példák

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) a indexOf és a lastIndexOf függvények használatát mutatja be:

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

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

## <a name="last"></a>utolsó

`last (arg1)`

A sztring utolsó karakterét vagy a tömb utolsó elemét adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |tömb vagy karakterlánc |Az utolsó elem vagy karakter beolvasására szolgáló érték. |

### <a name="return-value"></a>Vrácená hodnota

Egy tömb utolsó elemének utolsó karakterének vagy típusának (karakterlánc, int, Array vagy Object) a karakterlánca.

### <a name="examples"></a>Példák

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| arrayOutput | Sztring | három |
| stringOutput | Sztring | e |

## <a name="lastindexof"></a>lastIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

Egy érték utolsó pozícióját adja vissza egy karakterláncon belül. Az összehasonlítás megkülönbözteti a kis-és nagybetűket.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| stringToSearch |Igen |Karakterlánc |A keresendő elemeket tartalmazó érték. |
| stringToFind |Igen |Karakterlánc |A keresendő érték. |

### <a name="return-value"></a>Vrácená hodnota

Egy egész szám, amely a keresendő tétel utolsó pozícióját jelöli. Az érték nulla-alapú. Ha az elem nem található, a-1 értéket adja vissza.

### <a name="examples"></a>Példák

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) a indexOf és a lastIndexOf függvények használatát mutatja be:

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

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

## <a name="length"></a>length

`length(string)`

Egy sztringben szereplő karakterek számát, egy tömb elemeinek vagy egy objektum gyökérszintű tulajdonságait adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |tömb, karakterlánc vagy objektum |Az elemek számának beolvasásához használandó tömb, a karakterek számának beolvasásához használandó karakterlánc, vagy a gyökérszintű tulajdonságok számának beolvasásához használandó objektum. |

### <a name="return-value"></a>Vrácená hodnota

Egy int. 

### <a name="examples"></a>Példák

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

## <a name="newguid"></a>newGuid

`newGuid()`

Egy globálisan egyedi azonosító formájában megadott értéket ad vissza. **Ez a függvény csak a paraméter alapértelmezett értékében használható.**

### <a name="remarks"></a>Megjegyzések

Ezt a függvényt csak egy paraméter alapértelmezett értékére használhatja egy kifejezésen belül. Ha ezt a funkciót a sablonban bárhol máshol használja, hibaüzenetet ad vissza. A függvény nem engedélyezett a sablon más részeiben, mert minden egyes híváskor más értéket ad vissza. Ugyanazon sablon ugyanazon paraméterekkel való üzembe helyezése nem eredményezi megbízhatóan ugyanazt az eredményt.

A newGuid függvény eltér a [GUID](#guid) függvénytől, mert nem végez paramétereket. Ha ugyanazzal a paraméterrel hívja meg a GUID azonosítót, minden alkalommal ugyanazt az azonosítót adja vissza. Használja a GUID azonosítót, ha megbízhatóan kell létrehoznia egy adott környezethez tartozó GUID azonosítót. Akkor használja a newGuid, ha minden alkalommal eltérő azonosítóra van szükség, például erőforrások üzembe helyezése tesztkörnyezetben.

Ha a [korábbi sikeres központi telepítés](resource-group-template-deploy-rest.md#redeploy-when-deployment-fails)újbóli üzembe helyezését használja, és a korábbi telepítés newGuid-t használó paramétert tartalmaz, a paraméter nem lett újraértékelve. Ehelyett a korábbi központi telepítés paraméterének értékét a rendszer automatikusan újra felhasználja a visszaállítási telepítésben.

Tesztelési környezetben szükség lehet az olyan erőforrások ismételt üzembe helyezésére, amelyek csak rövid ideig élnek. Az egyedi nevek létrehozása helyett a newGuid és a [uniqueString](#uniquestring) segítségével egyedi neveket hozhat létre.

Ügyeljen arra, hogy egy alapértelmezett érték esetén a newGuid függvényre támaszkodó sablont telepítse újra. Ha újratelepíti, és nem ad meg értéket a paraméterhez, a függvény újraértékelése megtörténik. Ha egy meglévő erőforrást nem új létrehozása helyett szeretne frissíteni, adja át a paraméter értékét a korábbi telepítésből.

### <a name="return-value"></a>Vrácená hodnota

Egy 36 karaktert tartalmazó karakterlánc, amely globálisan egyedi azonosító formátumban van.

### <a name="examples"></a>Példák

Az alábbi példában szereplő sablon egy új azonosítójú paramétert mutat be.

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

Az előző példa kimenete az egyes központi telepítések esetében változik, de a következőhöz hasonló lesz:

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| guidOutput | Karakterlánc | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

A következő példa a newGuid függvényt használja a Storage-fiók egyedi nevének létrehozásához. Ez a sablon olyan tesztkörnyezetben is működhet, ahol a Storage-fiók egy rövid ideig létezik, és nem települ újra.

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

Az előző példa kimenete az egyes központi telepítések esetében változik, de a következőhöz hasonló lesz:

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| nameOutput | Karakterlánc | storagenziwvyru7uxie |


## <a name="padleft"></a>padLeft

`padLeft(valueToPad, totalLength, paddingCharacter)`

Egy jobbra igazított karakterláncot ad vissza, amely a megadott teljes hossz eléréséig a bal oldali karaktereket adja hozzá.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| valueToPad |Igen |karakterlánc vagy int |Az érték jobbra igazítása. |
| totalLength |Igen |int |A visszaadott karakterláncban szereplő karakterek teljes száma. |
| paddingCharacter |Nem |egyetlen karakter |A bal oldali margóhoz használandó karakter, amíg el nem éri a teljes hosszt. Az alapértelmezett érték egy szóköz. |

Ha az eredeti sztring hosszabb, mint a pad karaktereinek száma, a rendszer nem adja hozzá a karaktereket.

### <a name="return-value"></a>Vrácená hodnota

Egy sztring, amely legalább a megadott karakterek számát adja meg.

### <a name="examples"></a>Példák

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) azt mutatja be, hogyan lehet a felhasználó által megadott paraméter értékét felvenni a nulla karakter hozzáadásával, amíg el nem éri a karakterek teljes számát. 

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

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| stringOutput | Sztring | 0000000123 |

## <a name="replace"></a>csere

`replace(originalString, oldString, newString)`

Egy új karakterláncot ad vissza, amely egy sztring összes példányát egy másik sztring váltja fel.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| originalString |Igen |Karakterlánc |Egy sztring összes példányát egy másik sztring váltja fel. |
| Régikarakterlánc |Igen |Karakterlánc |Az eredeti sztringből eltávolítandó karakterlánc. |
| Újkarakterlánc |Igen |Karakterlánc |Az eltávolított karakterlánc helyére felvenni kívánt karakterlánc. |

### <a name="return-value"></a>Vrácená hodnota

A lecserélt karaktereket tartalmazó karakterlánc.

### <a name="examples"></a>Példák

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) azt mutatja be, Hogyan távolítható el az összes kötőjel a felhasználó által megadott karakterláncból, és hogyan cserélhető le a karakterlánc egy része egy másik karakterláncra.

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
        "secondOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'1234', 'xxxx')]"
        }
    }
}
```

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| firstOutput | Sztring | 1231231234 |
| secondOutput | Sztring | 123-123-xxxx |

## <a name="skip"></a>kihagyás

`skip(originalValue, numberToSkip)`

Egy olyan sztringet ad vissza, amely a megadott számú karakter után az összes karaktert tartalmazza, vagy egy tömböt a megadott számú elem után.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| originalValue |Igen |tömb vagy karakterlánc |A kihagyáshoz használandó tömb vagy karakterlánc. |
| numberToSkip |Igen |int |A kihagyni kívánt elemek vagy karakterek száma. Ha ez az érték 0 vagy kevesebb, az értékben szereplő összes elemet vagy karaktert adja vissza a rendszer. Ha a tömb vagy a karakterlánc hossza nagyobb, akkor a rendszer üres tömböt vagy karakterláncot ad vissza. |

### <a name="return-value"></a>Vrácená hodnota

Tömb vagy karakterlánc.

### <a name="examples"></a>Példák

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| arrayOutput | Array | ["három"] |
| stringOutput | Sztring | 2 3 |

## <a name="split"></a>split

`split(inputString, delimiter)`

Karakterláncok tömbjét adja vissza, amely a megadott elválasztó karakterrel tagolt bemeneti sztring alsztringjét tartalmazza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| inputString |Igen |Karakterlánc |A felosztani kívánt karakterlánc. |
| elválasztó |Igen |karakterlánc vagy karakterláncok tömbje |A karakterlánc felosztásához használandó elválasztó karakter. |

### <a name="return-value"></a>Vrácená hodnota

Karakterláncok tömbje.

### <a name="examples"></a>Példák

A következő [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) a bemeneti karakterláncot vesszővel, vesszővel vagy pontosvesszővel osztja szét.

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

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| firstOutput | Array | ["egy", "kettő", "három"] |
| secondOutput | Array | ["egy", "kettő", "három"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

Meghatározza, hogy egy karakterlánc egy értékkel kezdődik-e. Az összehasonlítás megkülönbözteti a kis-és nagybetűket.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| stringToSearch |Igen |Karakterlánc |A keresendő elemeket tartalmazó érték. |
| stringToFind |Igen |Karakterlánc |A keresendő érték. |

### <a name="return-value"></a>Vrácená hodnota

**Igaz** , ha a karakterlánc első karaktere vagy karaktere megegyezik az értékkel; Ellenkező esetben **hamis**.

### <a name="examples"></a>Példák

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) a startsWith és a endsWith függvények használatát mutatja be:

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

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| startsTrue | Bool | True |
| startsCapTrue | Bool | True |
| startsFalse | Bool | False (Hamis) |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False (Hamis) |

## <a name="string"></a>Karakterlánc

`string(valueToConvert)`

A megadott értéket karakterlánccá alakítja.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| valueToConvert |Igen | Any |A sztringre konvertálandó érték. Bármilyen típusú érték konvertálható, beleértve az objektumokat és a tömböket is. |

### <a name="return-value"></a>Vrácená hodnota

Az átalakított érték karakterlánca.

### <a name="examples"></a>Példák

Az alábbi [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) bemutatja, hogyan alakíthat át különböző típusú értékeket karakterláncokra:

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

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| objectOutput | Sztring | {"valuea": 10, "valueB": "példa szövege"} |
| arrayOutput | Sztring | ["a", "b", "c"] |
| intOutput | Sztring | 5 |

## <a name="substring"></a>substring

`substring(stringToParse, startIndex, length)`

Egy olyan alsztringet ad vissza, amely a megadott karakteres pozíciónál kezdődik, és a megadott számú karaktert tartalmazza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| stringToParse |Igen |Karakterlánc |Az az eredeti karakterlánc, amelyből a rendszer kinyeri az alkarakterláncot. |
| startIndex |Nem |int |Az alsztring nulla alapú kiindulási karakterének pozíciója. |
| length |Nem |int |Az alkarakterlánc karaktereinek száma. A karakterláncon belüli helyre kell hivatkoznia. Nulla vagy nagyobb értéknek kell lennie. |

### <a name="return-value"></a>Vrácená hodnota

Az alkarakterlánc. Vagy ha a hossz nulla, akkor üres karakterláncot kell megadni.

### <a name="remarks"></a>Megjegyzések

A függvény meghiúsul, ha az alkarakterlánc a karakterlánc végénél hosszabb ideig is kinyúlik, vagy ha a hossz nullánál kisebb. Az alábbi példa a következő hibával meghiúsul: "az index és a Length paraméternek a karakterláncon belüli helyre kell vonatkoznia. Az index paraméter: "0", a Length paraméter: "11", a sztring paraméter hossza: "10". "

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

### <a name="examples"></a>Példák

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json) egy alsztringet kibont egy paraméterből.

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

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| substringOutput | Sztring | kettő |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

Egy olyan karakterláncot ad vissza, amely a megadott számú karaktert tartalmazza a karakterlánc elejétől, vagy egy tömböt a tömb elejétől megadott számú elemmel.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| originalValue |Igen |tömb vagy karakterlánc |Az elemeket tartalmazó tömb vagy karakterlánc. |
| numberToTake |Igen |int |A felvenni kívánt elemek vagy karakterek száma. Ha ez az érték 0 vagy kevesebb, üres tömböt vagy karakterláncot ad vissza. Ha az érték nagyobb, mint a megadott tömb vagy karakterlánc hossza, a rendszer a tömb vagy karakterlánc összes elemét visszaadja. |

### <a name="return-value"></a>Vrácená hodnota

Tömb vagy karakterlánc.

### <a name="examples"></a>Példák

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| arrayOutput | Array | ["egy", "Two"] |
| stringOutput | Sztring | be |

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

A megadott sztringet kisbetűvé alakítja.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| stringToChange |Igen |Karakterlánc |Az kisbetűsre konvertálandó érték. |

### <a name="return-value"></a>Vrácená hodnota

A karakterláncot kisbetűvé alakítja.

### <a name="examples"></a>Példák

A következő [példában a sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) egy paraméter értékét kisbetűvé és nagybetűvé alakítja.

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

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| toLowerOutput | Sztring | egy két há' |
| toUpperOutput | Sztring | EGY KÉT HÁ' |

## <a name="toupper"></a>toUpper

`toUpper(stringToChange)`

A megadott sztringet nagybetűvé alakítja.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| stringToChange |Igen |Karakterlánc |A nagybetűre konvertálandó érték. |

### <a name="return-value"></a>Vrácená hodnota

A karakterlánc a nagybetűre lett konvertálva.

### <a name="examples"></a>Példák

A következő [példában a sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) egy paraméter értékét kisbetűvé és nagybetűvé alakítja.

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

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| toLowerOutput | Sztring | egy két há' |
| toUpperOutput | Sztring | EGY KÉT HÁ' |

## <a name="trim"></a>Trim

`trim (stringToTrim)`

Eltávolítja az összes kezdő és záró szóközöket a megadott karakterláncból.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| stringToTrim |Igen |Karakterlánc |A vágási érték. |

### <a name="return-value"></a>Vrácená hodnota

A karakterlánc kezdő és záró szóközök nélkül.

### <a name="examples"></a>Példák

Az alábbi [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) a fehér szóközöket vágja le a paraméterből.

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

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| visszatérési | Sztring | egy két há' |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

Létrehoz egy determinisztikus-kivonat karakterláncot a paraméterként megadott értékek alapján. 

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| baseString |Igen |Karakterlánc |Egy egyedi karakterlánc létrehozásához a kivonatoló függvényben használt érték. |
| további paraméterek igény szerint |Nem |Karakterlánc |Az egyediség szintjét meghatározó érték létrehozásához tetszőleges számú karakterláncot adhat hozzá. |

### <a name="remarks"></a>Megjegyzések

Ez a függvény akkor hasznos, ha egyedi nevet kell létrehoznia egy erőforráshoz. Olyan paramétereket adhat meg, amelyek korlátozzák az eredmény egyediségi hatókörét. Megadhatja, hogy a név egyedi-e az előfizetés, az erőforráscsoport vagy a központi telepítés számára. 

A visszaadott érték nem egy véletlenszerű karakterlánc, hanem egy kivonatoló függvény eredménye. A visszaadott érték 13 karakter hosszúságú. Globálisan nem egyedi. Érdemes lehet az értéket az elnevezési konvenció egy előtaggal egyesíteni, hogy olyan nevet hozzon létre, amely értelmes. Az alábbi példában a visszaadott érték formátuma látható. A tényleges érték a megadott paraméterektől függ.

    tcvhiyu5h2o5o

Az alábbi példák bemutatják, hogyan használhatja a uniqueString-t egyedi érték létrehozására a gyakran használt szintekhez.

Az előfizetéshez tartozó egyedi hatókör

```json
"[uniqueString(subscription().subscriptionId)]"
```

Az erőforráscsoport egyedi hatóköre

```json
"[uniqueString(resourceGroup().id)]"
```

Egy erőforráscsoport üzembe helyezésének egyedi hatóköre

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

Az alábbi példa bemutatja, hogyan hozhat létre egyedi nevet egy Storage-fiókhoz az erőforráscsoport alapján. Az erőforráscsoport belsejében a név nem egyedi, ha a kiépítés ugyanúgy történik.

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```

Ha minden alkalommal új egyedi nevet kell létrehoznia, amikor sablont telepít, és nem kívánja frissíteni az erőforrást, használhatja a [utcNow](#utcnow) függvényt a uniqueString. Ezt a megközelítést tesztkörnyezetben is használhatja. Példaként tekintse meg a következőt: [utcNow](#utcnow).

### <a name="return-value"></a>Vrácená hodnota

13 karaktert tartalmazó sztring.

### <a name="examples"></a>Példák

A következő [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) a uniquestring eredményeit adja vissza:

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

Abszolút URI-t hoz létre a baseUri és a relativeUri karakterlánc kombinálásával.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| baseUri |Igen |Karakterlánc |Az alap URI-karakterlánc. |
| relativeUri |Igen |Karakterlánc |Az alap URI-karakterlánchoz hozzáadandó relatív URI-karakterlánc. |

A **baseUri** paraméter értéke tartalmazhat egy adott fájlt, de a rendszer csak az alap elérési utat használja az URI összeállításakor. Például a baseUri paraméternek való átadás `http://contoso.com/resources/azuredeploy.json` a (z `http://contoso.com/resources/`) alap URI-ját eredményezi.

### <a name="return-value"></a>Vrácená hodnota

Az alap és a relatív értékek abszolút URI azonosítóját jelölő sztring.

### <a name="examples"></a>Példák

Az alábbi példa bemutatja, hogyan hozhat létre egy beágyazott sablonra mutató hivatkozást a fölérendelt sablon értéke alapján.

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

Az alábbi [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) azt szemlélteti, hogyan használható az URI, a uriComponent és a uriComponentToString:

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

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| uriOutput | Sztring | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | Sztring | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | Sztring | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="uricomponent"></a>uriComponent

`uricomponent(stringToEncode)`

URI kódolása.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| stringToEncode |Igen |Karakterlánc |A kódolni kívánt érték. |

### <a name="return-value"></a>Vrácená hodnota

Az URI-kódolású érték karakterlánca.

### <a name="examples"></a>Példák

Az alábbi [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) azt szemlélteti, hogyan használható az URI, a uriComponent és a uriComponentToString:

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

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| uriOutput | Sztring | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | Sztring | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | Sztring | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

Egy URI-kódolású érték sztringjét adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Igen |Karakterlánc |A karakterláncra konvertálandó URI-kódolású érték. |

### <a name="return-value"></a>Vrácená hodnota

URI-kódolt érték dekódolt karakterlánca.

### <a name="examples"></a>Példák

Az alábbi [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) azt szemlélteti, hogyan használható az URI, a uriComponent és a uriComponentToString:

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

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| uriOutput | Sztring | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | Sztring | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | Sztring | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Az aktuális (UTC) dátum és idő értéket adja vissza a megadott formátumban. Ha nincs megadva formátum, a rendszer az ISO 8601 (yyyyMMddTHHmmssZ) formátumot használja. **Ez a függvény csak a paraméter alapértelmezett értékében használható.**

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| format |Nem |Karakterlánc |A karakterláncra konvertálandó URI-kódolású érték. Használjon [szabványos formázó karakterláncokat](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy [Egyéni formázó karakterláncokat](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Megjegyzések

Ezt a függvényt csak egy paraméter alapértelmezett értékére használhatja egy kifejezésen belül. Ha ezt a funkciót a sablonban bárhol máshol használja, hibaüzenetet ad vissza. A függvény nem engedélyezett a sablon más részeiben, mert minden egyes híváskor más értéket ad vissza. Ugyanazon sablon ugyanazon paraméterekkel való üzembe helyezése nem eredményezi megbízhatóan ugyanazt az eredményt.

Ha a [korábbi sikeres központi telepítés](resource-group-template-deploy-rest.md#redeploy-when-deployment-fails)újbóli üzembe helyezését használja, és a korábbi telepítés utcNow-t használó paramétert tartalmaz, a paraméter nem lett újraértékelve. Ehelyett a korábbi központi telepítés paraméterének értékét a rendszer automatikusan újra felhasználja a visszaállítási telepítésben.

Ügyeljen arra, hogy egy alapértelmezett érték esetén a utcNow függvényre támaszkodó sablont telepítse újra. Ha újratelepíti, és nem ad meg értéket a paraméterhez, a függvény újraértékelése megtörténik. Ha egy meglévő erőforrást nem új létrehozása helyett szeretne frissíteni, adja át a paraméter értékét a korábbi telepítésből.

### <a name="return-value"></a>Vrácená hodnota

Az aktuális UTC datetime érték.

### <a name="examples"></a>Példák

A következő példa sablon a DateTime érték különböző formátumait jeleníti meg.

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

Az előző példa kimenete az egyes központi telepítések esetében változik, de a következőhöz hasonló lesz:

| Name (Név) | Típus | Value |
| ---- | ---- | ----- |
| utcOutput | Karakterlánc | 20190305T175318Z |
| utcShortOutput | Karakterlánc | 03/05/2019 |
| utcCustomOutput | Karakterlánc | 3 5 |

A következő példa azt szemlélteti, hogyan használható a függvény értéke a címke értékének beállításakor.

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

