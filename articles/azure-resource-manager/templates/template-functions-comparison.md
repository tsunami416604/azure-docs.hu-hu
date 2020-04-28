---
title: Sablon functions – összehasonlítás
description: A Azure Resource Manager-sablonban az értékek összehasonlításához használandó függvényeket ismerteti.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: a9b7b32475695e5222b87c8fe75e8982f34ebb21
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192331"
---
# <a name="comparison-functions-for-arm-templates"></a>ARM-sablonok összehasonlító függvények

A Resource Manager számos funkciót biztosít a Azure Resource Manager-(ARM-) sablonokban való összehasonlításhoz.

* [egyenlő](#equals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)
* [less](#less)
* [lessOrEquals](#lessorequals)

## <a name="equals"></a>egyenlő

`equals(arg1, arg2)`

Ellenőrzi, hogy két érték egyenlő-e egymással.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |int, string, Array vagy Object |Az egyenlőség keresésének első értéke. |
| arg2 |Igen |int, string, Array vagy Object |A második érték az egyenlőség kereséséhez. |

### <a name="return-value"></a>Visszatérítési érték

**Igaz** értéket ad vissza, ha az értékek egyenlőek; Ellenkező esetben **hamis**.

### <a name="remarks"></a>Megjegyzések

Az Equals függvényt gyakran használják az `condition` elemmel annak teszteléséhez, hogy van-e üzembe helyezett erőforrás.

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

### <a name="example"></a>Példa

Az alábbi [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/equals.json) különböző típusú értékeket keres az egyenlőséghez. Az összes alapértelmezett érték igaz értéket ad vissza.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 1
        },
        "firstString": {
            "type": "string",
            "defaultValue": "a"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "firstObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[equals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[equals(parameters('firstString'), parameters('secondString'))]"
        },
        "checkArrays": {
            "type": "bool",
            "value": "[equals(parameters('firstArray'), parameters('secondArray'))]"
        },
        "checkObjects": {
            "type": "bool",
            "value": "[equals(parameters('firstObject'), parameters('secondObject'))]"
        }
    }
}
```

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| checkInts | Logikai | True (Igaz) |
| checkStrings | Logikai | True (Igaz) |
| checkArrays | Logikai | True (Igaz) |
| checkObjects | Logikai | True (Igaz) |

A következő [példában](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) a sablon [nem](template-functions-logical.md#not) **egyenlő**.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "checkNotEquals": {
            "type": "bool",
            "value": "[not(equals(1, 2))]"
        }
    }
}
```

Az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| checkNotEquals | Logikai | True (Igaz) |

## <a name="greater"></a>greater

`greater(arg1, arg2)`

Ellenőrzi, hogy az első érték nagyobb-e, mint a második érték.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |int vagy sztring |A nagyobb összehasonlítás első értéke. |
| arg2 |Igen |int vagy sztring |A nagyobb összehasonlítás második értéke. |

### <a name="return-value"></a>Visszatérítési érték

**Igaz** értéket ad vissza, ha az első érték nagyobb, mint a második érték; Ellenkező esetben **hamis**.

### <a name="example"></a>Példa

Az alábbi [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json) ellenőrzi, hogy az egyik érték nagyobb-e, mint a másik.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greater(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greater(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| checkInts | Logikai | False (Hamis) |
| checkStrings | Logikai | True (Igaz) |

## <a name="greaterorequals"></a>greaterOrEquals

`greaterOrEquals(arg1, arg2)`

Ellenőrzi, hogy az első érték nagyobb-e vagy egyenlő-e a második értékkel.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |int vagy sztring |A nagyobb vagy egyenlő összehasonlítás első értéke. |
| arg2 |Igen |int vagy sztring |A nagyobb vagy egyenlő összehasonlítás második értéke. |

### <a name="return-value"></a>Visszatérítési érték

**Igaz** értéket ad vissza, ha az első érték nagyobb vagy egyenlő, mint a második érték; Ellenkező esetben **hamis**.

### <a name="example"></a>Példa

Az alábbi [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json) ellenőrzi, hogy az egyik érték nagyobb-e vagy egyenlő-e a másikkal.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| checkInts | Logikai | False (Hamis) |
| checkStrings | Logikai | True (Igaz) |

## <a name="less"></a>less

`less(arg1, arg2)`

Ellenőrzi, hogy az első érték kisebb-e a második értéknél.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |int vagy sztring |A kevésbé hasonlítható összehasonlítás első értéke. |
| arg2 |Igen |int vagy sztring |A kisebb összehasonlítás második értéke. |

### <a name="return-value"></a>Visszatérítési érték

**Igaz** értéket ad vissza, ha az első érték kisebb, mint a második érték; Ellenkező esetben **hamis**.

### <a name="example"></a>Példa

Az alábbi [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json) ellenőrzi, hogy az egyik érték kisebb-e, mint a másik.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[less(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[less(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| checkInts | Logikai | True (Igaz) |
| checkStrings | Logikai | False (Hamis) |

## <a name="lessorequals"></a>lessOrEquals

`lessOrEquals(arg1, arg2)`

Ellenőrzi, hogy az első érték kisebb-e vagy egyenlő-e a második értékkel.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |int vagy sztring |A kisebb vagy egyenlő összehasonlítás első értéke. |
| arg2 |Igen |int vagy sztring |A kisebb vagy egyenlő összehasonlítás második értéke. |

### <a name="return-value"></a>Visszatérítési érték

**Igaz** értéket ad vissza, ha az első érték kisebb vagy egyenlő, mint a második érték; Ellenkező esetben **hamis**.

### <a name="example"></a>Példa

Az alábbi [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json) ellenőrzi, hogy az egyik érték kisebb-e vagy egyenlő-e a másikkal.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| checkInts | Logikai | True (Igaz) |
| checkStrings | Logikai | False (Hamis) |

## <a name="next-steps"></a>További lépések

* Egy Azure Resource Manager sablonban található részekről az [ARM-sablonok szerkezetének és szintaxisának megismerését](template-syntax.md)ismertető cikk nyújt tájékoztatást.
