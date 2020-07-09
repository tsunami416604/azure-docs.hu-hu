---
title: Sablon függvények – numerikus
description: A Azure Resource Manager-sablonban a számokkal való munkához használandó függvényeket ismerteti.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 00b44d971a487a0bbec27f3fc2d0746cedd6f874
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84677916"
---
# <a name="numeric-functions-for-arm-templates"></a>ARM-sablonok numerikus függvények

A Resource Manager a következő függvényeket biztosítja a Azure Resource Manager (ARM) sablonban található egész számok használatához:

* [hozzáadása](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [Max](#max)
* [min](#min)
* [mod](#mod)
* [mul](#mul)
* [Sub](#sub)

## <a name="add"></a>add

`add(operand1, operand2)`

A két megadott egész szám összegét adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
|operand1 |Yes |int |A hozzáadandó első szám. |
|operand2 |Yes |int |A hozzáadandó második szám. |

### <a name="return-value"></a>Visszatérési érték

Egy egész szám, amely a paraméterek összegét tartalmazza.

### <a name="example"></a>Példa

Az alábbi [sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/add.json) két paramétert hoz létre.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 5,
            "metadata": {
                "description": "First integer to add"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Second integer to add"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "addResult": {
            "type": "int",
            "value": "[add(parameters('first'), parameters('second'))]"
        }
    }
}
```

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Name | Típus | Érték |
| ---- | ---- | ----- |
| addResult | Int | 8 |

## <a name="copyindex"></a>copyIndex

`copyIndex(loopName, offset)`

Egy iterációs hurok indexét adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| loopName | No | sztring | Az iteráció megszerzéséhez használt hurok neve. |
| offset |No |int |A nulla alapú iterációs értékhez hozzáadandó szám. |

### <a name="remarks"></a>Megjegyzések

Ezt a függvényt mindig egy **másolási** objektummal használja a rendszer. Ha nincs megadva érték az **eltoláshoz**, a rendszer az aktuális iterációs értéket adja vissza. Az iteráció értéke nullával kezdődik.

A **loopName** tulajdonság lehetővé teszi annak megadását, hogy a copyIndex erőforrás-iterációra vagy tulajdonság-iterációra hivatkozik-e. Ha a **loopName**nem ad meg értéket, a rendszer az aktuális erőforrástípus iterációját használja. Adjon meg egy értéket a **loopName** , amikor a tulajdonságot megismétli.

A másolás használatával kapcsolatos további információkért lásd:

* [Erőforrás-iteráció az ARM-sablonokban](copy-resources.md)
* [Tulajdonság-iteráció az ARM-sablonokban](copy-properties.md)
* [Változó iteráció az ARM-sablonokban](copy-variables.md)
* [Kimeneti iteráció az ARM-sablonokban](copy-outputs.md)

### <a name="example"></a>Példa

A következő példa egy másolási ciklust és a névben szereplő index értékét mutatja be.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {}
}
```

### <a name="return-value"></a>Visszatérési érték

Egy egész szám, amely az iteráció aktuális indexét jelöli.

## <a name="div"></a>div

`div(operand1, operand2)`

A két megadott egész szám egészének osztását adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| operand1 |Yes |int |A felosztott szám. |
| operand2 |Yes |int |A felosztáshoz használt szám. Nem lehet 0. |

### <a name="return-value"></a>Visszatérési érték

A osztást jelképező egész szám.

### <a name="example"></a>Példa

Az alábbi [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/div.json) egy paramétert oszt szét egy másik paraméterrel.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 8,
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "divResult": {
            "type": "int",
            "value": "[div(parameters('first'), parameters('second'))]"
        }
    }
}
```

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Name | Típus | Érték |
| ---- | ---- | ----- |
| divResult | Int | 2 |

## <a name="float"></a>lebegőpontos

`float(arg1)`

Az értéket egy lebegőpontos számra konvertálja. Ezt a függvényt csak akkor használja, ha egyéni paramétereket adunk át egy alkalmazásnak, például egy logikai alkalmazásnak.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |karakterlánc vagy int |A lebegőpontos számra konvertálandó érték. |

### <a name="return-value"></a>Visszatérési érték

Egy lebegőpontos szám.

### <a name="example"></a>Példa

Az alábbi példa bemutatja, hogyan használható az úszó a paraméterek logikai alkalmazásba való átadásához:

```json
{
    "type": "Microsoft.Logic/workflows",
    "properties": {
        ...
        "parameters": {
            "custom1": {
                "value": "[float('3.0')]"
            },
            "custom2": {
                "value": "[float(3)]"
            },
```

## <a name="int"></a>int

`int(valueToConvert)`

Egy egész számra konvertálja a megadott értéket.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| valueToConvert |Yes |karakterlánc vagy int |Az egész számra konvertálandó érték. |

### <a name="return-value"></a>Visszatérési érték

Az átalakított érték egész szám.

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/int.json) a felhasználó által megadott paraméter értékét egész értékre konvertálja.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToConvert": {
            "type": "string",
            "defaultValue": "4"
        }
    },
    "resources": [
    ],
    "outputs": {
        "intResult": {
            "type": "int",
            "value": "[int(parameters('stringToConvert'))]"
        }
    }
}
```

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Name | Típus | Érték |
| ---- | ---- | ----- |
| intResult | Int | 4 |

## <a name="max"></a>Max

`max (arg1)`

A maximális értéket adja vissza egész számok tömbje vagy az egész számok vesszővel tagolt listája alapján.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |egész számok tömbje vagy egész számok vesszővel tagolt listája |A gyűjtemény, amely a maximális értéket kapja. |

### <a name="return-value"></a>Visszatérési érték

Egy egész szám, amely a gyűjteményből származó maximális értéket jelöli.

### <a name="example"></a>Példa

Az alábbi [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) azt szemlélteti, hogyan használható a Max egy tömbvel és egy egész számokból álló listával:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Name | Típus | Érték |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

## <a name="min"></a>p

`min (arg1)`

A minimális értéket adja vissza egész számok tömbje vagy az egész számok vesszővel tagolt listája alapján.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |egész számok tömbje vagy egész számok vesszővel tagolt listája |A gyűjtemény a minimális érték beolvasásához. |

### <a name="return-value"></a>Visszatérési érték

Egy egész szám, amely a gyűjtemény minimális értékét jelöli.

### <a name="example"></a>Példa

Az alábbi [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) azt szemlélteti, hogyan használható a min egy tömbvel és egy egész számokból álló listával:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Name | Típus | Érték |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

## <a name="mod"></a>mod

`mod(operand1, operand2)`

Az egész szám többit adja vissza a két megadott egész szám használatával.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| operand1 |Yes |int |A felosztott szám. |
| operand2 |Yes |int |A felosztáshoz használt szám nem lehet 0. |

### <a name="return-value"></a>Visszatérési érték

A maradékot jelölő egész szám.

### <a name="example"></a>Példa

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mod.json) egy másik paraméterrel való osztásának hátralévő részét adja vissza.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 7,
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "modResult": {
            "type": "int",
            "value": "[mod(parameters('first'), parameters('second'))]"
        }
    }
}
```

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Name | Típus | Érték |
| ---- | ---- | ----- |
| modResult | Int | 1 |

## <a name="mul"></a>mul

`mul(operand1, operand2)`

A két megadott egész szám szorzását adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| operand1 |Yes |int |A szorzáshoz használandó első szám. |
| operand2 |Yes |int |A szorzáshoz használt második szám. |

### <a name="return-value"></a>Visszatérési érték

A szorzást jelölő egész szám.

### <a name="example"></a>Példa

A következő [példában](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mul.json) egy paramétert szorozunk egy másik paraméterrel.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 5,
            "metadata": {
                "description": "First integer to multiply"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Second integer to multiply"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "mulResult": {
            "type": "int",
            "value": "[mul(parameters('first'), parameters('second'))]"
        }
    }
}
```

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Name | Típus | Érték |
| ---- | ---- | ----- |
| mulResult | Int | 15 |

## <a name="sub"></a>Sub

`sub(operand1, operand2)`

A két megadott egész szám kivonását adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| operand1 |Yes |int |A következőből kivont szám. |
| operand2 |Yes |int |A kivonni kívánt szám. |

### <a name="return-value"></a>Visszatérési érték

A kivonást jelképező egész szám.

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/sub.json) egy paramétert kivonja egy másik paraméterből.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 7,
            "metadata": {
                "description": "Integer subtracted from"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer to subtract"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "subResult": {
            "type": "int",
            "value": "[sub(parameters('first'), parameters('second'))]"
        }
    }
}
```

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Name | Típus | Érték |
| ---- | ---- | ----- |
| aleredmény | Int | 4 |

## <a name="next-steps"></a>További lépések

* Egy Azure Resource Manager sablonban található részekről az [ARM-sablonok szerkezetének és szintaxisának megismerését](template-syntax.md)ismertető cikk nyújt tájékoztatást.
* Ha egy adott típusú erőforrás létrehozásakor meghatározott számú alkalommal szeretné megismételni a problémát, tekintse meg a [Azure Resource Manager erőforrások több példányának létrehozása](copy-resources.md)című témakört.
