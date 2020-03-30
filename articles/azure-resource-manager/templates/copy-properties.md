---
title: Egy tulajdonság több példányának definiálása
description: Az Azure Resource Manager-sablonban használt másolási műveletet többször idotere, amikor egy erőforráson hoz létre egy tulajdonságot.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: e86d38b0e5d2e39d54b3c419b6eebdcda74022db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258107"
---
# <a name="property-iteration-in-arm-templates"></a>Tulajdonság ismétlése ARM sablonokban

Ez a cikk bemutatja, hogyan hozhat létre egynél több példányt egy tulajdonság az Azure Resource Manager (ARM) sablonban. Ha a sablonban lévő erőforrás tulajdonságszakaszában hozzáadja a **másolási** elemet, dinamikusan beállíthatja egy tulajdonság elemeinek számát a telepítés során. Azt is elkerülheti, hogy meg kell ismételnie a sablon szintaxisát.

A [másolást erőforrásokkal](copy-resources.md), [változókkal](copy-variables.md)és kimenetekkel is [használhatja.](copy-outputs.md)

## <a name="property-iteration"></a>Tulajdonság ismétlése

A másolási elem általános formátuma a következő:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

A **névhez**adja meg a létrehozni kívánt erőforrástulajdonság nevét. A **Count** tulajdonság megadja a tulajdonsághoz kívánt ismétlések számát.

A **bemeneti** tulajdonság határozza meg az ismétlődő tulajdonságokat. A **bemeneti** tulajdonság értékéből létrehozott elemek tömbjét hozza létre.

A következő példa bemutatja, hogyan alkalmazható `copy` a dataDisks tulajdonságra egy virtuális gépen:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberOfDataDisks": {
      "type": "int",
      "minValue": 0,
      "maxValue": 16,
      "defaultValue": 16,
      "metadata": {
        "description": "The number of dataDisks to create."
      }
    },
    ...
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2017-03-30",
      ...
      "properties": {
        "storageProfile": {
          ...
          "copy": [
            {
              "name": "dataDisks",
              "count": "[parameters('numberOfDataDisks')]",
              "input": {
                "diskSizeGB": 1023,
                "lun": "[copyIndex('dataDisks')]",
                "createOption": "Empty"
              }
            }
          ]
        }
      }
    }
  ]
}
```

Figyelje meg, hogy egy tulajdonságiteráción belül történő használat során `copyIndex` meg kell adnia az iteráció nevét.

> [!NOTE]
> A tulajdonságismétlés egy eltolási argumentumot is támogat. Az eltolásnak az iteráció neve után kell származnia, például copyIndex('dataDisks', 1).
>

Az Erőforrás-kezelő `copy` kibővíti a tömböt az üzembe helyezés során. A tömb neve lesz a tulajdonság neve. A bemeneti értékek válnak az objektum tulajdonságait. Az üzembe helyezett sablon a következőlesz:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": 1023
        }
      ],
      ...
```

A másolási elem egy tömb, így az erőforráshoz több tulajdonságot is megadhat.

```json
{
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "name": "exampleLB",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

Használhatja az erőforrás- és tulajdonságiteráció együtt. Hivatkozzon a tulajdonság iterációjára név szerint.

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "apiVersion": "2018-04-01",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

## <a name="copy-limits"></a>Másolási korlátok

A szám nem haladhatja meg a 800-at.

A szám nem lehet negatív szám. Ha az Azure PowerShell 2.6-os vagy újabb, az Azure CLI 2.0.74-es vagy újabb, illetve a REST API **2019-05-10-es** vagy újabb verziójával telepít egy sablont, beállíthatja a számlálót nullára. A PowerShell, a CLI és a REST API korábbi verziói nem támogatják a nullát a számláláshoz.

## <a name="example-templates"></a>Példa sablonok

A következő példa egy általános forgatókönyvet mutat be egy tulajdonság egynél több érték létrehozásához.

|Sablon  |Leírás  |
|---------|---------|
|[Virtuális gép telepítése változó számú adatlemezzel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Több adatlemez központi telepítése egy virtuális géppel. |

## <a name="next-steps"></a>További lépések

* Ha egy oktatóanyagon szeretne átmenni, olvassa el [az Oktatóanyag: Több erőforráspéldány létrehozása ARM-sablonokkal című témakört.](template-tutorial-create-multiple-instances.md)
* A másolási elem egyéb felhasználási ideje:
  * [Erőforrás-ismétlés ARM-sablonokban](copy-resources.md)
  * [Változó iteráció arm sablonokban](copy-variables.md)
  * [Kimeneti ismétlés ARM sablonokban](copy-outputs.md)
* Ha a sablonok szakaszairól szeretne többet megtudni, olvassa el [az ARM-sablonok készítése című témakört.](template-syntax.md)
* A sablon üzembe helyezéséről az [Alkalmazás telepítése ARM sablonnal (Alkalmazás telepítése ARM sablonnal) témakörben](deploy-powershell.md)olvashat.

