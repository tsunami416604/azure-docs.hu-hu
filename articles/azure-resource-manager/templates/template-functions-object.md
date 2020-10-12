---
title: Sablon functions – objektumok
description: Az objektumok kezeléséhez Azure Resource Manager sablonban használható függvényeket ismerteti.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: fede4d6c71e45b119e500d4c9c6f91765d052036
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "84676794"
---
# <a name="object-functions-for-arm-templates"></a>ARM-sablonok Object functions

A Resource Manager számos funkciót biztosít a Azure Resource Manager (ARM) sablon objektumainak használatához.

* [tartalmaz](#contains)
* [üres](#empty)
* [kereszteződés](#intersection)
* [JSON](#json)
* [length](#length) (hossz)
* [Union](#union)

## <a name="contains"></a>contains

`contains(container, itemToFind)`

Ellenőrzi, hogy egy tömb tartalmaz-e értéket, egy objektum tartalmaz-e kulcsot, vagy egy karakterlánc tartalmaz-e alkarakterláncot. A karakterlánc-összehasonlítás megkülönbözteti a kis-és nagybetűket. Ha azonban egy objektum kulcsot tartalmaz, az összehasonlítás a kis-és nagybetűk megkülönböztetése nélkül történik.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| tároló |Igen |tömb, objektum vagy karakterlánc |A keresendő értéket tartalmazó érték. |
| itemToFind |Igen |karakterlánc vagy int |A keresendő érték. |

### <a name="return-value"></a>Visszatérési érték

**Igaz** , ha az adott tétel megtalálható; Ellenkező esetben **hamis**.

### <a name="example"></a>Példa

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) azt mutatja be, hogyan használható a különböző típusú tartalmaz:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Név | Típus | Érték |
| ---- | ---- | ----- |
| stringTrue | Logikai | Igaz |
| stringFalse | Logikai | Hamis |
| objectTrue | Logikai | Igaz |
| objectFalse | Logikai | Hamis |
| arrayTrue | Logikai | Igaz |
| arrayFalse | Logikai | Hamis |

## <a name="empty"></a>üres

`empty(itemToTest)`

Meghatározza, hogy egy tömb, objektum vagy karakterlánc üres-e.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| itemToTest |Igen |tömb, objektum vagy karakterlánc |Az érték, amely alapján ellenőrizhető, hogy üres-e. |

### <a name="return-value"></a>Visszatérési érték

**Igaz** értéket ad vissza, ha az érték üres. Ellenkező esetben **hamis**.

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) ellenőrzi, hogy egy tömb, objektum és karakterlánc üres-e.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Név | Típus | Érték |
| ---- | ---- | ----- |
| arrayEmpty | Logikai | Igaz |
| objectEmpty | Logikai | Igaz |
| stringEmpty | Logikai | Igaz |

## <a name="intersection"></a>kereszteződés

`intersection(arg1, arg2, arg3, ...)`

Egyetlen tömböt vagy objektumot ad vissza, amely a paraméterek közös elemeit tartalmazza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
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
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Név | Típus | Érték |
| ---- | ---- | ----- |
| objectOutput | Objektum | {"One": "a", "három": "c"} |
| arrayOutput | Tömb | ["kettő", "három"] |

## <a name="json"></a>json

`json(arg1)`

Egy JSON-objektumot ad vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |sztring |A JSON-ra konvertálandó érték. |

### <a name="return-value"></a>Visszatérési érték

A megadott karakterlánc JSON-objektuma vagy egy üres objektum, ha **Null** van megadva.

### <a name="remarks"></a>Megjegyzések

Ha a JSON-objektumban szerepelnie kell egy paraméter értékének vagy változónak, használja a [concat](template-functions-string.md#concat) függvényt a függvénynek átadott karakterlánc létrehozásához.

### <a name="example"></a>Példa

Az alábbi [példában látható sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/json.json) a JSON-függvény használatát mutatja be. Figyelje meg, hogy egy olyan karakterláncot adhat át, amely az objektumot jelöli, vagy **Null** értéket használ, ha nincs szükség értékre.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "jsonObject1": {
            "type": "string",
            "defaultValue": "null"
        },
        "jsonObject2": {
            "type": "string",
            "defaultValue": "{\"a\": \"b\"}"
        },
        "testValue": {
            "type": "string",
            "defaultValue": "demo value"
        }
    },
    "resources": [
    ],
    "outputs": {
        "jsonOutput1": {
            "type": "bool",
            "value": "[empty(json(parameters('jsonObject1')))]"
        },
        "jsonOutput2": {
            "type": "object",
            "value": "[json(parameters('jsonObject2'))]"
        },
        "paramOutput": {
            "type": "object",
            "value": "[json(concat('{\"a\": \"', parameters('testValue'), '\"}'))]"
        }
    }
}
```

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| jsonOutput1 | Logikai | Igaz |
| jsonOutput2 | Objektum | {"a": "b"} |
| paramOutput | Objektum | {"a": "bemutató értéke"}

## <a name="length"></a>hossz

`length(arg1)`

Egy tömbben lévő elemek számát, egy karakterláncban szereplő karaktereket vagy egy objektum gyökérszintű tulajdonságait adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |tömb, karakterlánc vagy objektum |Az elemek számának beolvasásához használandó tömb, a karakterek számának beolvasásához használandó karakterlánc, vagy a gyökérszintű tulajdonságok számának beolvasásához használandó objektum. |

### <a name="return-value"></a>Visszatérési érték

Egy int.

### <a name="example"></a>Példa

Az alábbi [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) azt szemlélteti, hogyan használható a hossz egy tömb és egy karakterlánc használatával:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Név | Típus | Érték |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

## <a name="union"></a>Union

`union(arg1, arg2, arg3, ...)`

Egyetlen tömböt vagy objektumot ad vissza, amely a paraméterek összes elemét tartalmazza. Ismétlődő értékek vagy kulcsok csak egyszer szerepelnek.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
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
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Név | Típus | Érték |
| ---- | ---- | ----- |
| objectOutput | Objektum | {"One": "a", "kettő": "b", "három": "C2", "Four": "d", "öt": "e"} |
| arrayOutput | Tömb | ["egy", "kettő", "három", "négy"] |

## <a name="next-steps"></a>További lépések

* Egy Azure Resource Manager sablonban található részekről az [ARM-sablonok szerkezetének és szintaxisának megismerését](template-syntax.md)ismertető cikk nyújt tájékoztatást.
