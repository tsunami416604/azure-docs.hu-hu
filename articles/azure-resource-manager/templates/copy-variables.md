---
title: Változó több példányának definiálása
description: Az Azure Resource Manager-sablonban használt másolási műveletet többször idotere, amikor változót hoz létre.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: ed0c2d87c48a18b0a065f6c76e1e69142a9df048
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153301"
---
# <a name="variable-iteration-in-arm-templates"></a>Változó iteráció arm sablonokban

Ez a cikk bemutatja, hogyan hozhat létre egynél több értéket egy változóhoz az Azure Resource Manager (ARM) sablonban. Ha hozzáadja a **másolási** elemet a sablon változók szakaszához, dinamikusan beállíthatja egy változó elemeinek számát a telepítés során. Azt is elkerülheti, hogy meg kell ismételnie a sablon szintaxisát.

A [másolást erőforrásokkal](copy-resources.md), [erőforrások tulajdonságaival](copy-properties.md)és kimenetekkel is [használhatja.](copy-outputs.md)

## <a name="variable-iteration"></a>Változó iteráció

A másolási elem általános formátuma a következő:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

A **name** tulajdonság bármely olyan érték, amely azonosítja a hurkot. A **count** tulajdonság a változóhoz kívánt ismétlések számát adja meg.

A **bemeneti** tulajdonság határozza meg az ismétlődő tulajdonságokat. A **bemeneti** tulajdonság értékéből létrehozott elemek tömbjét hozza létre. Ez lehet egyetlen tulajdonság (például egy karakterlánc), vagy egy több tulajdonsággal rendelkező objektum.

A következő példa bemutatja, hogyan hozhat létre karakterlánc-értékektömbjét:

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

Az előző sablon a következő értékekkel rendelkező tömböt ad vissza:

```json
[
    "item1",
    "item2",
    "item3",
    "item4",
    "item5"
]
```

A következő példa bemutatja, hogyan hozhat létre három tulajdonsággal rendelkező objektumok tömbjét – név, diskSizeGB és diskIndex.

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

Az előző példa a következő értékekkel rendelkező tömböt ad vissza:

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
> A változó iteráció támogatja az eltolási argumentumot. Az eltolásnak az iteráció neve után kell származnia, például copyIndex('diskNames', 1). Ha nem ad meg eltolási értéket, akkor az első példány esetében alapértelmezés szerint 0 lesz.
>

A másolási elemet egy változón belül is használhatja. A következő példa létrehoz egy olyan objektumot, amelynek egyik értéke tömb.

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

Az előző példa a következő értékekkel rendelkező objektumot adja vissza:

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

A következő példa bemutatja, hogy a változókkal együtt milyen különböző módokon használható a másolás.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

## <a name="copy-limits"></a>Másolási korlátok

A szám nem haladhatja meg a 800-at.

A szám nem lehet negatív szám. Ha az Azure PowerShell 2.6-os vagy újabb, az Azure CLI 2.0.74-es vagy újabb, illetve a REST API **2019-05-10-es** vagy újabb verziójával telepít egy sablont, beállíthatja a számlálót nullára. A PowerShell, a CLI és a REST API korábbi verziói nem támogatják a nullát a számláláshoz.

## <a name="example-templates"></a>Példa sablonok

Az alábbi példák egy változóhoz egynél több érték létrehozásának gyakori forgatókönyveit mutatják be.

|Sablon  |Leírás  |
|---------|---------|
|[Változók másolása](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Bemutatja a változók eltérő iterálási módjait. |
|[Több biztonsági szabály](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Több biztonsági szabályt telepít egy hálózati biztonsági csoportra. A biztonsági szabályokat egy paraméterből építi fel. A paramétert több [NSG paraméterfájlban láthatja.](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) |

## <a name="next-steps"></a>További lépések

* Ha egy oktatóanyagon szeretne átmenni, olvassa el [az Oktatóanyag: Több erőforráspéldány létrehozása ARM-sablonokkal című témakört.](template-tutorial-create-multiple-instances.md)
* A másolási elem egyéb felhasználási ideje:
  * [Erőforrás-ismétlés ARM-sablonokban](copy-resources.md)
  * [Tulajdonság ismétlése ARM sablonokban](copy-properties.md)
  * [Kimeneti ismétlés ARM sablonokban](copy-outputs.md)
* Ha a sablonok szakaszairól szeretne többet megtudni, olvassa el [az ARM-sablonok készítése című témakört.](template-syntax.md)
* A sablon üzembe helyezéséről az [Alkalmazás telepítése ARM sablonnal (Alkalmazás telepítése ARM sablonnal) témakörben](deploy-powershell.md)olvashat.

