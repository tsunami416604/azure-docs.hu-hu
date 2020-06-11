---
title: Változó több példányának meghatározása
description: A másolási művelettel Azure Resource Manager sablonban több alkalommal is megismételheti a változókat.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: aca69dd858c7a940592e74123b97b8d364d9e11c
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84678443"
---
# <a name="variable-iteration-in-arm-templates"></a>Változó iteráció az ARM-sablonokban

Ez a cikk bemutatja, hogyan hozhat létre több értéket egy változóhoz a Azure Resource Manager (ARM) sablonban. Ha hozzáadja a **Másolás** elemet a sablon változók szakaszához, az üzembe helyezés során dinamikusan beállíthatja egy változó elemeinek számát. Emellett ne kelljen megismételni a sablon szintaxisát.

A másolást [erőforrásokkal](copy-resources.md), [erőforrásokkal](copy-properties.md)és [kimenetekkel](copy-outputs.md)is elvégezheti.

## <a name="syntax"></a>Szintaxis

A másolási elem a következő általános formátumú:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

A **Name** tulajdonság bármely olyan érték, amely a hurok azonosítására szolgál. A **Count** tulajdonság határozza meg a változóhoz használni kívánt iterációk számát.

A **bemeneti** tulajdonság határozza meg a megismételni kívánt tulajdonságokat. A **bemeneti** tulajdonság értékével létrehozott elemek tömbjét hozza létre. Ez lehet egy tulajdonság (például egy karakterlánc) vagy egy olyan objektum, amely több tulajdonsággal rendelkezik.

## <a name="copy-limits"></a>Másolási korlátok

A szám nem lehet nagyobb, mint 800.

A darabszám nem lehet negatív szám. Ha az Azure CLI, a PowerShell vagy a REST API legújabb verziójával telepíti a sablont, akkor nulla lehet. Pontosabban a következőket kell használnia:

* Azure PowerShell **2,6** vagy újabb
* Azure CLI- **2.0.74** vagy újabb
* REST API **2019-05-10** -es vagy újabb verzió
* A [csatolt központi telepítéseknek](linked-templates.md) a telepítési erőforrástípus **2019-05-10** -es vagy újabb API-verzióját kell használniuk

A PowerShell, a CLI és a REST API korábbi verziói nem támogatják a nulla értéket a darabszámhoz.

## <a name="variable-iteration"></a>Változó iteráció

Az alábbi példa azt szemlélteti, hogyan hozható létre karakterlánc-értékek tömbje:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
     },
    "variables": {
        "copy": [
            {
                "name": "stringArray",
                "count": "[parameters('itemCount')]",
                "input": "[concat('item', copyIndex('stringArray', 1))]"
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('stringArray')]"
        }
    }
}
```

Az előző sablon egy tömböt ad vissza a következő értékekkel:

```json
[
    "item1",
    "item2",
    "item3",
    "item4",
    "item5"
]
```

A következő példa bemutatja, hogyan hozhat létre olyan objektumok tömbjét, amelyeknek három tulajdonsága van: Name, diskSizeGB és diskIndex.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "copy": [
            {
                "name": "objectArray",
                "count": "[parameters('itemCount')]",
                "input": {
                    "name": "[concat('myDataDisk', copyIndex('objectArray', 1))]",
                    "diskSizeGB": "1",
                    "diskIndex": "[copyIndex('objectArray')]"
                }
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('objectArray')]"
        }
    }
}
```

Az előző példa egy tömböt ad vissza a következő értékekkel:

```json
[
    {
        "name": "myDataDisk1",
        "diskSizeGB": "1",
        "diskIndex": 0
    },
    {
        "name": "myDataDisk2",
        "diskSizeGB": "1",
        "diskIndex": 1
    },
    {
        "name": "myDataDisk3",
        "diskSizeGB": "1",
        "diskIndex": 2
    },
    {
        "name": "myDataDisk4",
        "diskSizeGB": "1",
        "diskIndex": 3
    },
    {
        "name": "myDataDisk5",
        "diskSizeGB": "1",
        "diskIndex": 4
    }
]
```

> [!NOTE]
> A változó iteráció egy eltolási argumentumot támogat. Az eltolásnak az iteráció neve után kell érkeznie, például copyIndex (' diskNames ', 1). Ha nem ad meg eltolási értéket, az alapértelmezett érték 0 az első példánynál.
>

A másolás elemet egy változón belül is használhatja. A következő példa egy olyan objektumot hoz létre, amely egy tömböt tartalmaz egy értékként.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "topLevelObject": {
            "sampleProperty": "sampleValue",
            "copy": [
                {
                    "name": "disks",
                    "count": "[parameters('itemCount')]",
                    "input": {
                        "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
                        "diskSizeGB": "1",
                        "diskIndex": "[copyIndex('disks')]"
                    }
                }
            ]
        }
    },
    "resources": [],
    "outputs": {
        "objectResult": {
            "type": "object",
            "value": "[variables('topLevelObject')]"
        }
    }
}
```

Az előző példa egy objektumot ad vissza a következő értékekkel:

```json
{
    "sampleProperty": "sampleValue",
    "disks": [
        {
            "name": "myDataDisk1",
            "diskSizeGB": "1",
            "diskIndex": 0
        },
        {
            "name": "myDataDisk2",
            "diskSizeGB": "1",
            "diskIndex": 1
        },
        {
            "name": "myDataDisk3",
            "diskSizeGB": "1",
            "diskIndex": 2
        },
        {
            "name": "myDataDisk4",
            "diskSizeGB": "1",
            "diskIndex": 3
        },
        {
            "name": "myDataDisk5",
            "diskSizeGB": "1",
            "diskIndex": 4
        }
    ]
}
```

A következő példa bemutatja a másolás változókkal való használatának különböző módszereit.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        },
        {
          "name": "diskNames",
          "count": 5,
          "input": "[concat('myDataDisk', copyIndex('diskNames', 1))]"
        }
      ]
    },
    "copy": [
      {
        "name": "top-level-object-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('top-level-object-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('top-level-object-array')]"
        }
      },
      {
        "name": "top-level-string-array",
        "count": 5,
        "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
      },
      {
        "name": "top-level-integer-array",
        "count": 5,
        "input": "[copyIndex('top-level-integer-array')]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleObjectArray": {
      "value": "[variables('top-level-object-array')]",
      "type" : "array"
    },
    "exampleStringArray": {
      "value": "[variables('top-level-string-array')]",
      "type" : "array"
    },
    "exampleIntegerArray": {
      "value": "[variables('top-level-integer-array')]",
      "type" : "array"
    }
  }
}
```

## <a name="example-templates"></a>Példák sablonokra

Az alábbi példák egy változó több értékének létrehozására vonatkozó gyakori forgatókönyveket mutatnak be.

|Sablon  |Description  |
|---------|---------|
|[Változók másolása](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Bemutatja a változók megismétlésének különböző módszereit. |
|[Több biztonsági szabály](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Több biztonsági szabályt telepít egy hálózati biztonsági csoportra. A biztonsági szabályokat egy paraméter alapján hozza létre. A paraméternél tekintse meg a [több NSG-paramétert tartalmazó fájlt](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Következő lépések

* Az oktatóanyag lépéseinek megismeréséhez tekintse meg az [oktatóanyag: több erőforrás-példány létrehozása ARM-sablonok használatával](template-tutorial-create-multiple-instances.md)című témakört.
* A másolási elem egyéb felhasználási módjaiért lásd:
  * [Erőforrás-iteráció az ARM-sablonokban](copy-resources.md)
  * [Tulajdonság-iteráció az ARM-sablonokban](copy-properties.md)
  * [Kimeneti iteráció az ARM-sablonokban](copy-outputs.md)
* Ha szeretne többet megtudni egy sablon fejezeteiről, tekintse meg a következő témakört: [ARM-sablonok készítése](template-syntax.md).
* A sablon üzembe helyezésével kapcsolatos további információkért lásd: [alkalmazás üzembe helyezése ARM-sablonnal](deploy-powershell.md).

