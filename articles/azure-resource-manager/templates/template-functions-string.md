---
title: Sablonfüggvények - karakterlánc
description: Ismerteti az Azure Resource Manager-sablonban a karakterláncokkal való munkát használandó függvényeket.
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: c0517375b273384f263e8ba421995d4afb6c193b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982414"
---
# <a name="string-functions-for-arm-templates"></a>Karakterlánc-függvények ARM-sablonokhoz

Az Erőforrás-kezelő a következő függvényeket biztosítja az Azure Resource Manager (ARM) sablonjaiban lévő karakterláncok használatával való munkához:

* [base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [Concat](#concat)
* [Tartalmaz](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [Üres](#empty)
* [végződikWith](#endswith)
* [Első](#first)
* [Formátum](#format)
* [Guid](#guid)
* [indexOf](#indexof)
* [Utolsó](#last)
* [lastIndexOf](#lastindexof)
* [Hossza](#length)
* [newGuid között](#newguid)
* [padBalra](#padleft)
* [Helyettesít](#replace)
* [Ugrál](#skip)
* [felosztás](#split)
* [kezdődik](#startswith)
* [Karakterlánc](#string)
* [Substring](#substring)
* [venni](#take)
* [lassabbra](#tolower)
* [toUpper (felső](#toupper)
* [Berendezés](#trim)
* [uniqueString](#uniquestring)
* [Uri](#uri)
* [uriKomponens](#uricomponent)
* [uriComponentToString](#uricomponenttostring)

## <a name="base64"></a>base64

`base64(inputString)`

A bemeneti karakterlánc base64 ábrázolását adja eredményül.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| inputString |Igen |sztring |A bázis64-es ábrázolásként visszaadandó érték. |

### <a name="return-value"></a>Visszatérítési érték

Base64 ábrázolást tartalmazó karakterlánc.

### <a name="examples"></a>Példák

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) bemutatja, hogyan kell használni a base64 függvényt.

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

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| base64Kimenet | Sztring | b25lLCB0d28sIHRocmVl |
| toStringOutput | Sztring | egy két há' |
| toJsonKimenet | Objektum | {"egy": "a", "kettő": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

Base64-ábrázolást JSON-objektummá alakít át.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| base64Érték |Igen |sztring |JSON-objektummá konvertálandó base64-reprezentáció. |

### <a name="return-value"></a>Visszatérítési érték

Egy JSON objektum.

### <a name="examples"></a>Példák

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) a base64ToJson függvényt használja egy base64 érték konvertálásához:

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

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| base64Kimenet | Sztring | b25lLCB0d28sIHRocmVl |
| toStringOutput | Sztring | egy két há' |
| toJsonKimenet | Objektum | {"egy": "a", "kettő": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

Base64 ábrázolást karakterlánclá alakít át.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| base64Érték |Igen |sztring |A karakterlánctá konvertálandó base64-reprezentáció. |

### <a name="return-value"></a>Visszatérítési érték

A konvertált bázis64 érték karakterlánca.

### <a name="examples"></a>Példák

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) a base64ToString függvényt használja egy base64 érték konvertálásához:

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

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| base64Kimenet | Sztring | b25lLCB0d28sIHRocmVl |
| toStringOutput | Sztring | egy két há' |
| toJsonKimenet | Objektum | {"egy": "a", "kettő": "b"} |

## <a name="concat"></a>Concat

`concat (arg1, arg2, arg3, ...)`

Több karakterlánc-értéket egyesít, és az összefont karakterláncot adja vissza, vagy több tömböt egyesít, és az összefont tömböt adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |karakterlánc vagy tömb |Az összefűzés első karakterlánca vagy tömbje. |
| további argumentumok |Nem |karakterlánc vagy tömb |További karakterláncok vagy tömbök egymás után egymás után. |

Ez a függvény tetszőleges számú argumentumot vehet fel, és elfogadhatja a paraméterek karakterláncait vagy tömbjeit. Azonban nem adhat meg tömböket és karakterláncokat a paraméterekhez. A karakterláncok csak más karakterláncokkal vannak összefűzve.

### <a name="return-value"></a>Visszatérítési érték

Összefektett értékek karakterlánca vagy tömbje.

### <a name="examples"></a>Példák

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

## <a name="contains"></a>tartalmazza a következőt:

`contains (container, itemToFind)`

Ellenőrzi, hogy egy tömb tartalmaz-e értéket, objektum tartalmaz-e kulcsot, vagy egy karakterlánc részkarakterláncot. A karakterlánc-összehasonlítás ban a kis- és nagybetűk et is figyelembe kell. Ha azonban egy objektum kulcstartalmat tesztel, az összehasonlítás nem imitot jelent.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| tároló |Igen |tömb, objektum vagy karakterlánc |A megkeresandó értéket tartalmazó érték. |
| itemToFind között |Igen |karakterlánc vagy int |A megtalálandó érték. |

### <a name="return-value"></a>Visszatérítési érték

**Igaz,** ha az elem megtalálható; ellenkező **esetben, Hamis**.

### <a name="examples"></a>Példák

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

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

Értéket adatURI-vá alakít át.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| karakterláncKonvertálás |Igen |sztring |Az adatURI-vá konvertálandó érték. |

### <a name="return-value"></a>Visszatérítési érték

AdatURI-ként formázott karakterlánc.

### <a name="examples"></a>Példák

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) adatURI-vá alakítja át az értéket, és az adatURI-t karakterláncsá alakítja:

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

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| dataUriKimenet | Sztring | adatok:szöveg/sima;charset=utf8;base64,SGVsbG8= |
| toStringOutput | Sztring | helló világ! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

AdatURI-formátumú értéket alakít át karakterláncsá.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |Igen |sztring |A konvertálandó adatURI-érték. |

### <a name="return-value"></a>Visszatérítési érték

A konvertált értéket tartalmazó karakterlánc.

### <a name="examples"></a>Példák

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) adatURI-vá alakítja át az értéket, és az adatURI-t karakterláncsá alakítja:

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

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| dataUriKimenet | Sztring | adatok:szöveg/sima;charset=utf8;base64,SGVsbG8= |
| toStringOutput | Sztring | helló világ! |

## <a name="empty"></a>üres

`empty(itemToTest)`

Azt határozza meg, hogy egy tömb, objektum vagy karakterlánc üres-e.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| itemToTest |Igen |tömb, objektum vagy karakterlánc |Az érték, hogy ellenőrizze, ha üres. |

### <a name="return-value"></a>Visszatérítési érték

**Igaz** értéket ad vissza, ha az érték üres; ellenkező **esetben, Hamis**.

### <a name="examples"></a>Példák

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

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

Azt határozza meg, hogy a karakterlánc értékkel végződik-e. Az összehasonlítás nem tetszés szerint történik.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| karakterláncKereséshez |Igen |sztring |A megkeresandó elemet tartalmazó érték. |
| karakterláncKeresése |Igen |sztring |A megtalálandó érték. |

### <a name="return-value"></a>Visszatérítési érték

**Igaz,** ha a karakterlánc utolsó karaktere vagy karaktere megegyezik az értékkel; ellenkező **esetben, Hamis**.

### <a name="examples"></a>Példák

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) bemutatja, hogyan kell használni a startsWith és végződikFunkciókkal:

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

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| indításIgaz | Logikai | True (Igaz) |
| startsCapTrue | Logikai | True (Igaz) |
| startsFalse (Hamis | Logikai | False (Hamis) |
| endsTrue | Logikai | True (Igaz) |
| endsCapTrue | Logikai | True (Igaz) |
| endsFalse | Logikai | False (Hamis) |

## <a name="first"></a>Első

`first(arg1)`

A karakterlánc első karakterét vagy a tömb első elemét adja eredményül.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |tömb vagy karakterlánc |Az első elem vagy karakter beolvasásához. |

### <a name="return-value"></a>Visszatérítési érték

A tömb első elemének első karakterének vagy típusának (karakterlánc, int, tömb vagy objektum) karakterlánca.

### <a name="examples"></a>Példák

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

## <a name="format"></a>Formátum

`format(formatString, arg1, arg2, ...)`

Formázott karakterláncot hoz létre a bemeneti értékekből.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| formatString | Igen | sztring | Az összetett formátumú karakterlánc. |
| arg1 | Igen | karakterlánc, egész szám vagy logika | A formázott karakterláncba felvenni a felvemi értéket. |
| további argumentumok | Nem | karakterlánc, egész szám vagy logika | A formázott karakterláncba foglalandó további értékek. |

### <a name="remarks"></a>Megjegyzések

Ezzel a függvénnyel formázhatja a sablonban lévő karakterláncot. Ugyanazokat a formázási beállításokat használja, mint a [System.String.Format](/dotnet/api/system.string.format) metódus a .NET-ben.

### <a name="examples"></a>Példák

A következő példasablon bemutatja a formátumfüggvény használatát.

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

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| formatTest | Sztring | Helló, felhasználó. Formázott szám: 8,175,133 |

## <a name="guid"></a>Guid

`guid(baseString, ...)`

Globálisan egyedi azonosító formátumú értéket hoz létre a paraméterekként megadott értékek alapján.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| baseString |Igen |sztring |A kivonatoló függvényben a GUID létrehozásához használt érték. |
| szükség szerint további paraméterek |Nem |sztring |Annyi karakterláncot adhat hozzá, amennyi szükséges az egyediség szintjét megadó érték létrehozásához. |

### <a name="remarks"></a>Megjegyzések

Ez a funkció akkor hasznos, ha globálisan egyedi azonosító formátumú értéket kell létrehoznia. Olyan paraméterértékeket ad meg, amelyek korlátozzák az eredmény egyediségének hatókörét. Megadhatja, hogy a név egyedi-e az előfizetés, az erőforráscsoport vagy a központi telepítés között.

A visszaadott érték nem véletlen karakterlánc, hanem a paraméterek kivonatoló függvényének eredménye. A visszaadott érték 36 karakter hosszú. Ez nem globálisan egyedi. Ha olyan új GUID azonosítót szeretne létrehozni, amely nem a paraméterek kivonatértékén alapul, használja az [új Guid függvényt.](#newguid)

Az alábbi példák bemutatják, hogyan használható guid a gyakran használt szintek egyedi értékének létrehozásához.

Egyedi hatóköraz előfizetéshez

```json
"[guid(subscription().subscriptionId)]"
```

Erőforráscsoport egyedi hatóköre

```json
"[guid(resourceGroup().id)]"
```

Erőforráscsoport központi telepítésének egyedi hatóköre

```json
"[guid(resourceGroup().id, deployment().name)]"
```

### <a name="return-value"></a>Visszatérítési érték

36 karaktert tartalmazó karakterlánc globálisan egyedi azonosító formátumban.

### <a name="examples"></a>Példák

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json) guid találatait adja vissza:

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

Egy érték karakterláncon belüli első pozícióját adja eredményül. Az összehasonlítás nem tetszés szerint történik.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| karakterláncKereséshez |Igen |sztring |A megkeresandó elemet tartalmazó érték. |
| karakterláncKeresése |Igen |sztring |A megtalálandó érték. |

### <a name="return-value"></a>Visszatérítési érték

Egész szám, amely a megkeresandó elem pozícióját jelöli. Az érték nulla alapú. Ha az elem nem található, a rendszer a -1 értéket adja vissza.

### <a name="examples"></a>Példák

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) bemutatja az indexOf és lastIndexOf függvények használatát:

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

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| utolsóT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

## <a name="last"></a>Utolsó

`last (arg1)`

A karakterlánc utolsó karakterét vagy a tömb utolsó elemét adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |tömb vagy karakterlánc |Az utolsó elem vagy karakter beolvasásához. |

### <a name="return-value"></a>Visszatérítési érték

A tömb utolsó elemének utolsó karakterének vagy típusának (karakterlánc, int, tömb vagy objektum) karakterlánca.

### <a name="examples"></a>Példák

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

## <a name="lastindexof"></a>lastIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

Egy érték utolsó pozícióját adja eredményül egy karakterláncon belül. Az összehasonlítás nem tetszés szerint történik.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| karakterláncKereséshez |Igen |sztring |A megkeresandó elemet tartalmazó érték. |
| karakterláncKeresése |Igen |sztring |A megtalálandó érték. |

### <a name="return-value"></a>Visszatérítési érték

Egész szám, amely az elem utolsó általmegtalált pozícióját jelöli. Az érték nulla alapú. Ha az elem nem található, a rendszer a -1 értéket adja vissza.

### <a name="examples"></a>Példák

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) bemutatja az indexOf és lastIndexOf függvények használatát:

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

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| utolsóT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

## <a name="length"></a>hossz

`length(string)`

Egy karakterláncban, tömb elemeiben vagy gyökérszintű tulajdonságainak számát adja eredményül egy objektumban.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |tömb, karakterlánc vagy objektum |Az elemek számának, a karakterek számának lekérő karakterláncnak vagy a gyökérszintű tulajdonságok számának beszerzéséhez használandó objektumnak a lekérnie szükséges tömb. |

### <a name="return-value"></a>Visszatérítési érték

Egy int. 

### <a name="examples"></a>Példák

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

## <a name="newguid"></a>newGuid között

`newGuid()`

Globálisan egyedi azonosító formátumú értéket ad eredményül. **Ez a függvény csak egy paraméter alapértelmezett értékében használható.**

### <a name="remarks"></a>Megjegyzések

Ezt a függvényt csak egy paraméter alapértelmezett értékéhez használhatja egy kifejezésen belül. Ha ezt a függvényt a sablon ban bárhol használja, az hibát ad vissza. A függvény nem engedélyezett a sablon más részein, mert minden egyes megadáskor más értéket ad vissza. Ugyanazt a sablont üzembe helyezése azonos paraméterekkel nem megbízhatóan ugyanazt az eredményt.

A newGuid függvény eltér a [guid](#guid) függvénytől, mert nem vesz igénybe paramétereket. Ha ugyanazzal a paraméterrel hívja meg a GUID-ot, akkor minden alkalommal ugyanazt az azonosítót adja vissza. Akkor használja a guid azonosítót, ha megbízhatóan létre kell hoznia ugyanazt a GUID azonosítót egy adott környezethez. Akkor használja a newGuid azonosítót, ha minden alkalommal más azonosítóra van szüksége, például erőforrásokat telepít egy tesztkörnyezetbe.

Az újGuid függvény [a](/dotnet/api/system.guid) .

Ha egy [korábbi sikeres telepítés újratelepítésének lehetőségével rendelkezik,](rollback-on-error.md)és a korábbi központi telepítés tartalmaz egy paramétert, amely newGuid-ot használ, a paraméter nem lesz újraértékelve. Ehelyett a korábbi központi telepítés paraméterértéke automatikusan újra fellesz használva a visszaállítási központi telepítésben.

Tesztkörnyezetben előfordulhat, hogy többször is üzembe kell helyeznie az erőforrásokat, amelyek csak rövid ideig élnek. Ahelyett, hogy egyedi neveket hozna létre, a newGuid with [uniqueString](#uniquestring) használatával egyedi neveket hozhat létre.

Legyen óvatos egy sablon, amely támaszkodik az újGuid függvény egy alapértelmezett érték. Ha újratelepíti, és nem ad meg értéket a paraméter, a függvény újraértékeli. Ha egy meglévő erőforrást szeretne frissíteni ahelyett, hogy újat hozna létre, adja át a paraméter értékét a korábbi központi telepítésből.

### <a name="return-value"></a>Visszatérítési érték

36 karaktert tartalmazó karakterlánc globálisan egyedi azonosító formátumban.

### <a name="examples"></a>Példák

A következő példasablon egy új azonosítóval rendelkező paramétert mutat be.

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

Az előző példa kimenete az egyes központi telepítésekhez változik, de hasonló lesz a következőkhöz:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| guidOutput | sztring | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

A következő példa az újGuid függvény thasználja a tárfiók egyedi nevének létrehozásához. Ez a sablon működhet a tesztkörnyezetben, ahol a tárfiók létezik egy rövid ideig, és nem újratelepítése.

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

Az előző példa kimenete az egyes központi telepítésekhez változik, de hasonló lesz a következőkhöz:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| nameOutput | sztring | storagenziwvyru7uxie |


## <a name="padleft"></a>padBalra

`padLeft(valueToPad, totalLength, paddingCharacter)`

Jobbra igazított karakterláncot ad vissza úgy, hogy karaktereket ad balra, amíg el nem éri a megadott teljes hosszt.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| értékToPad |Igen |karakterlánc vagy int |A jobbra igazítandó érték. |
| totalLength (összesen) |Igen |int |A visszaadott karakterláncban szereplő karakterek teljes száma. |
| kitöltésKarakter |Nem |egykarakteres |A bal oldali kitöltéshez használandó karakter, amíg el nem éri a teljes hosszt. Az alapértelmezett érték szóköz. |

Ha az eredeti karakterlánc hosszabb, mint a kipárnázandó karakterek száma, a program nem ad hozzá karaktereket.

### <a name="return-value"></a>Visszatérítési érték

Legalább a megadott számú karakterrel rendelkező karakterlánc.

### <a name="examples"></a>Példák

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) bemutatja, hogyan lehet a felhasználó által megadott paraméterértéket a nulla karakter hozzáadásával kiírni, amíg el nem éri a karakterek teljes számát. 

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

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| karakterlánckimenet | Sztring | 0000000123 |

## <a name="replace"></a>Helyettesít

`replace(originalString, oldString, newString)`

Új karakterláncot ad vissza, amelynek összes példányát egy másik karakterlánc váltja fel.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| originalString (eredeti karakterlánc) |Igen |sztring |Az az érték, amelynek egy karakterláncának összes példányát egy másik karakterlánc váltotta fel. |
| oldString |Igen |sztring |Az eredeti karakterláncból eltávolítandó karakterlánc. |
| új karakterlánc |Igen |sztring |Az eltávolított karakterlánc helyett hozzáadandó karakterlánc. |

### <a name="return-value"></a>Visszatérítési érték

A lecserélt karaktereket felváltó karakterlánc.

### <a name="examples"></a>Példák

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) bemutatja, hogyan távolíthatja el az összes kötőjelet a felhasználó által megadott karakterláncból, és hogyan cserélheti le a karakterlánc egy részét egy másik karakterláncra.

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

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| firstOutput | Sztring | 1231231234 |
| második kimenet | Sztring | 123-123-xxxx |

## <a name="skip"></a>Ugrál

`skip(originalValue, numberToSkip)`

Egy karakterláncot ad vissza, amelynek összes karaktere a megadott számú karakter után van, vagy egy tömböt, amely a megadott számú elem után az összes elemet tartalmazza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| originalValue (eredeti érték) |Igen |tömb vagy karakterlánc |A kihagyáshoz használandó tömb vagy karakterlánc. |
| számKiugrás |Igen |int |Az áthagyandó elemek vagy karakterek száma. Ha ez az érték 0 vagy kevesebb, az érték összes elemét vagy karakterét visszaadja a rendszer. Ha nagyobb, mint a tömb vagy karakterlánc hossza, a rendszer üres tömböt vagy karakterláncot ad vissza. |

### <a name="return-value"></a>Visszatérítési érték

Tömb vagy karakterlánc.

### <a name="examples"></a>Példák

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

## <a name="split"></a>felosztás

`split(inputString, delimiter)`

Olyan karakterlánctömböt ad vissza, amely a megadott határolók által határolt bemeneti karakterlánc részkarakterláncait tartalmazza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| inputString |Igen |sztring |A felosztandó karakterlánc. |
| elválasztókarakter |Igen |karakterlánc vagy karakterlánctömb |A karakterlánc felosztásához használandó határolójel. |

### <a name="return-value"></a>Visszatérítési érték

Egy sor húr.

### <a name="examples"></a>Példák

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) a bemeneti karakterláncot vesszővel és vesszővel vagy pontosvesszővel osztja fel.

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

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| firstOutput | Tömb | ["egy", "kettő", "három"] |
| második kimenet | Tömb | ["egy", "kettő", "három"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

Azt határozza meg, hogy egy karakterlánc értékkel kezdődik-e. Az összehasonlítás nem tetszés szerint történik.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| karakterláncKereséshez |Igen |sztring |A megkeresandó elemet tartalmazó érték. |
| karakterláncKeresése |Igen |sztring |A megtalálandó érték. |

### <a name="return-value"></a>Visszatérítési érték

**Igaz,** ha a karakterlánc első karaktere vagy karaktere megegyezik az értékkel; ellenkező **esetben, Hamis**.

### <a name="examples"></a>Példák

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) bemutatja, hogyan kell használni a startsWith és végződikFunkciókkal:

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

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| indításIgaz | Logikai | True (Igaz) |
| startsCapTrue | Logikai | True (Igaz) |
| startsFalse (Hamis | Logikai | False (Hamis) |
| endsTrue | Logikai | True (Igaz) |
| endsCapTrue | Logikai | True (Igaz) |
| endsFalse | Logikai | False (Hamis) |

## <a name="string"></a>sztring

`string(valueToConvert)`

A megadott értéket karakterlánccá alakítja.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| értékToConvert |Igen | Bármelyik |A karakterláncmá konvertálni kívánt érték. Bármilyen típusú érték konvertálható, beleértve az objektumokat és tömböket is. |

### <a name="return-value"></a>Visszatérítési érték

A konvertált érték karakterlánca.

### <a name="examples"></a>Példák

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) bemutatja, hogyan konvertálható a különböző típusú értékek karakterláncokká:

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

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| objectOutput | Sztring | {"valueA":10,"érték B":"Példa szöveg"} |
| tömbkimenet | Sztring | ["a""""b","c"] |
| intOutput | Sztring | 5 |

## <a name="substring"></a>Substring

`substring(stringToParse, startIndex, length)`

Olyan karakterláncot ad vissza, amely a megadott karakterpozícióval kezdődik, és a megadott számú karaktert tartalmazza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| karakterláncToParse |Igen |sztring |Az eredeti karakterlánc, amelyből a karakterláncot kivonják. |
| startIndex |Nem |int |A részkarakterlánc nulla-alapú kezdőkarakter-pozíciója. |
| hossz |Nem |int |A karakterlánc részkarakterláncának karakterszáma. A karakterláncon belüli helyre kell hivatkoznia. Nullának vagy nagyobbnak kell lennie. |

### <a name="return-value"></a>Visszatérítési érték

A felkötött karakterlánc. Vagy üres karakterlánc, ha a hossz nulla.

### <a name="remarks"></a>Megjegyzések

A függvény akkor sikertelen, ha a karakterláncrész túlnyúlik a karakterlánc végén, vagy ha a hossz nulla-nál kisebb. A következő példa sikertelen a következő hiba : "Az index és a hossz paraméterek kell hivatkozni a megadott helyre. Az index paraméter: '0', a hossz paraméter: '11', a karakterlánc-paraméter hossza: '10'.".

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

### <a name="examples"></a>Példák

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json) egy paraméter részkarakterláncát bontja ki.

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

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| karakterlánc-részkimenet | Sztring | kettő |

## <a name="take"></a>venni

`take(originalValue, numberToTake)`

A karakterlánc kezdetétől megadott számú karakterrel rendelkező karakterláncot, vagy a tömb elejétől a megadott számú elemet.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| originalValue (eredeti érték) |Igen |tömb vagy karakterlánc |Az a tömb vagy karakterlánc, amelyből az elemeket ki kell venni. |
| számTake |Igen |int |A figyelembe vethet elemek vagy karakterek száma. Ha ez az érték 0 vagy kevesebb, a függvény üres tömböt vagy karakterláncot ad vissza. Ha nagyobb, mint a megadott tömb vagy karakterlánc hossza, a tömb vagy karakterlánc összes elemét adja vissza. |

### <a name="return-value"></a>Visszatérítési érték

Tömb vagy karakterlánc.

### <a name="examples"></a>Példák

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

## <a name="tolower"></a>lassabbra

`toLower(stringToChange)`

A megadott karakterláncot kisbetűssé alakítja.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| karakterláncváltás |Igen |sztring |A kisbetűssé konvertálandó érték. |

### <a name="return-value"></a>Visszatérítési érték

A karakterlánc kisbetűssé alakul át.

### <a name="examples"></a>Példák

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) egy paraméterértéket kisbetűssé és nagybetűssé alakít át.

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

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| toLowerOutput | Sztring | egy két há' |
| toUpperOutput | Sztring | egy két há' |

## <a name="toupper"></a>toUpper (felső

`toUpper(stringToChange)`

A megadott karakterláncot nagybetűssé alakítja.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| karakterláncváltás |Igen |sztring |A nagybetűssé konvertálandó érték. |

### <a name="return-value"></a>Visszatérítési érték

A karakterlánc nagybetűssé alakult.

### <a name="examples"></a>Példák

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) egy paraméterértéket kisbetűssé és nagybetűssé alakít át.

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

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| toLowerOutput | Sztring | egy két há' |
| toUpperOutput | Sztring | egy két há' |

## <a name="trim"></a>Berendezés

`trim (stringToTrim)`

Eltávolítja az összes kezdő és záró szóközkaraktert a megadott karakterláncból.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| karakterláncToTrim |Igen |sztring |A levágandó érték. |

### <a name="return-value"></a>Visszatérítési érték

A karakterlánc kezdő és záró szóközkarakterek nélkül.

### <a name="examples"></a>Példák

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) levágja a paraméter üres karaktereket.

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

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| Vissza | Sztring | egy két há' |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

Determinisztikus kivonatoló karakterláncot hoz létre a paraméterekként megadott értékek alapján. 

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| baseString |Igen |sztring |A kivonatoló függvényben használt érték egy egyedi karakterlánc létrehozásához. |
| szükség szerint további paraméterek |Nem |sztring |Annyi karakterláncot adhat hozzá, amennyi szükséges az egyediség szintjét megadó érték létrehozásához. |

### <a name="remarks"></a>Megjegyzések

Ez a funkció akkor hasznos, ha egyedi nevet kell létrehoznia egy erőforráshoz. Olyan paraméterértékeket ad meg, amelyek korlátozzák az eredmény egyediségének hatókörét. Megadhatja, hogy a név egyedi-e az előfizetés, az erőforráscsoport vagy a központi telepítés között. 

A visszaadott érték nem véletlen karakterlánc, hanem egy kivonatoló függvény eredménye. A visszaadott érték 13 karakter hosszú. Ez nem globálisan egyedi. Érdemes lehet kombinálni az értéket az elnevezési konvenció előtaggal, hogy értelmes nevet hozzon létre. A következő példa a visszaadott érték formátumát mutatja be. A tényleges érték a megadott paraméterektől függ.

    tcvhiyu5h2o5o

Az alábbi példák bemutatják, hogyan hozhat létre uniqueString használatával egyedi értéket a gyakran használt szintekhez.

Egyedi hatóköraz előfizetéshez

```json
"[uniqueString(subscription().subscriptionId)]"
```

Erőforráscsoport egyedi hatóköre

```json
"[uniqueString(resourceGroup().id)]"
```

Erőforráscsoport központi telepítésének egyedi hatóköre

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

A következő példa bemutatja, hogyan hozhat létre egyedi nevet egy tárfiókhoz az erőforráscsoport alapján. Az erőforráscsoporton belül a név nem egyedi, ha ugyanúgy van kialakítva.

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```

Ha létre kell hoznia egy új egyedi nevet minden alkalommal, amikor egy sablont telepít, és nem kívánja frissíteni az erőforrást, használhatja az [utcNow](template-functions-date.md#utcnow) függvényt uniqueString. Ezt a módszert egy tesztkörnyezetben is használhatja. Például [lásd: utcNow](template-functions-date.md#utcnow).

### <a name="return-value"></a>Visszatérítési érték

13 karaktert tartalmazó karakterlánc.

### <a name="examples"></a>Példák

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) a uniquestring eredményeit adja vissza:

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

## <a name="uri"></a>Uri

`uri (baseUri, relativeUri)`

Abszolút URI-t hoz létre a baseUri és a relativeUri karakterlánc kombinálásával.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| baseUri között |Igen |sztring |Az alap uri húr. Ügyeljen arra, hogy megfigyelje a záró perjel ('/') kezelésével kapcsolatos viselkedést az alábbi táblázatszerint.  |
| relativeUri |Igen |sztring |Az alap uri húrhoz hozzáadni a relatív uri karakterlánc. |

* Ha **baseUri** végződik egy záró perjel, az eredmény egyszerűen **baseUri** majd **relativeUri**.

* Ha **baseUri** nem ér véget a záró perjel két dolog történik.  

   * Ha **baseUri** nincs vágás egyáltalán (eltekintve a "//" az első közelében) az eredmény egyszerűen **baseUri** majd **relativeUri**.

   * Ha **baseUri** van néhány vágás, de nem ér véget egy perjel, mindent az utolsó perjel től eltávolítjuk **baseUri** és az eredmény **baseUri** követi **relativeUri**.
     
Néhány példa:

```
uri('http://contoso.org/firstpath', 'myscript.sh') -> http://contoso.org/myscript.sh
uri('http://contoso.org/firstpath/', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json/', 'myscript.sh') -> http://contoso.org/firstpath/azuredeploy.json/myscript.sh
```
A teljes részleteket illetően a **baseUri** és **relativeUri** paraméterek feloldása a [3986 RFC 5.](https://tools.ietf.org/html/rfc3986#section-5)

### <a name="return-value"></a>Visszatérítési érték

Az alap- és relatív értékek abszolút URI-értékét jelölő karakterlánc.

### <a name="examples"></a>Példák

A következő példa bemutatja, hogyan hozhat létre egy csatolást egy beágyazott sablonra a szülősablon értéke alapján.

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) bemutatja az uri, uriComponent és uriComponentToString használatát:

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

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| uriKimenet | Sztring | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | Sztring | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Sztring | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponent"></a>uriKomponens

`uricomponent(stringToEncode)`

URI-kódol.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| karakterláncToEncode |Igen |sztring |A kódolandó érték. |

### <a name="return-value"></a>Visszatérítési érték

Az URI kódolású érték karakterlánca.

### <a name="examples"></a>Példák

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) bemutatja az uri, uriComponent és uriComponentToString használatát:

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

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| uriKimenet | Sztring | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | Sztring | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Sztring | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

Uri kódolású érték karakterláncát adja eredményül.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Igen |sztring |Az URI kódolású érték karakterlánclá konvertálása. |

### <a name="return-value"></a>Visszatérítési érték

Uri kódolású érték dekódolt karakterlánca.

### <a name="examples"></a>Példák

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) bemutatja az uri, uriComponent és uriComponentToString használatát:

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

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| uriKimenet | Sztring | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | Sztring | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Sztring | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="next-steps"></a>További lépések
* Az Azure Resource Manager-sablon szakaszainak leírását az [Azure Resource Manager-sablonok készítése című témakörben találja.](template-syntax.md)
* Több sablon egyesítéséhez olvassa el [a Csatolt sablonok használata az Azure Resource Manager rel.](linked-templates.md)
* Ha egy erőforrástípus létrehozásakor meghatározott számú alkalommal szeretne meghaladni, olvassa [el az Erőforrások több példányának létrehozása az Azure Resource Manager ben című témakört.](copy-resources.md)
* A létrehozott sablon központi telepítéséről az Alkalmazás üzembe helyezése az [Azure Resource Manager sablonnal című témakörben](deploy-powershell.md)olvashat.

