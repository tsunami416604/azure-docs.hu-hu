---
title: Egy tulajdonság több példányának meghatározása
description: A másolási művelettel egy Azure Resource Manager sablonban több alkalommal is megismételheti a tulajdonságok egy erőforráson való létrehozásakor.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: b759389cd1065c399658bd8d0c1ddd263054697c
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77622863"
---
# <a name="property-iteration-in-azure-resource-manager-templates"></a>Tulajdonság-iteráció Azure Resource Manager-sablonokban

Ez a cikk bemutatja, hogyan hozhat létre egy tulajdonság több példányát a Azure Resource Manager-sablonban. Ha hozzáadja a **Másolás** elemet a sablon egyik erőforrásának Properties (Tulajdonságok) szakaszához, akkor dinamikusan állíthatja be a tulajdonság elemeinek számát a telepítés során. Emellett ne kelljen megismételni a sablon szintaxisát.

A másolás [erőforrásokat](copy-resources.md), [változókat](copy-variables.md)és [kimeneteket](copy-outputs.md)is használhat.

## <a name="property-iteration"></a>Tulajdonság iterációja

A másolási elem a következő általános formátumú:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

A **név mezőben**adja meg a létrehozni kívánt erőforrás-tulajdonság nevét. A **Count** tulajdonság megadja a tulajdonsághoz használni kívánt iterációk számát.

A **bemeneti** tulajdonság határozza meg a megismételni kívánt tulajdonságokat. A **bemeneti** tulajdonság értékével létrehozott elemek tömbjét hozza létre.

Az alábbi példa bemutatja, hogyan alkalmazhatja a `copy`t a virtuális gép dataDisks tulajdonságára:

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

Figyelje meg, hogy amikor `copyIndex` használ a tulajdonságok iterációjában, meg kell adnia az iteráció nevét.

> [!NOTE]
> A tulajdonság iterációja egy eltolási argumentumot is támogat. Az eltolásnak az iteráció neve után kell érkeznie, például copyIndex (' dataDisks ', 1).
>

A Resource Manager kibővíti a `copy` tömböt az üzembe helyezés során. A tömb neve lesz a tulajdonság neve. A bemeneti értékek az objektum tulajdonságai lesznek. A központilag telepített sablon a következőket válik:

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

A másolási elem egy tömb, így több tulajdonság is megadható az erőforráshoz.

```json
{
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "name": "examleLB",
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

Az erőforrások és a tulajdonságok ismétlését együtt is használhatja. Hivatkozzon a tulajdonság iterációra név alapján.

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

A szám nem lehet nagyobb, mint 800.

A darabszám nem lehet negatív szám. Ha Azure PowerShell 2,6-as vagy újabb, Azure CLI-2.0.74 vagy újabb verzióval, REST API vagy a **2019-05-10** -es vagy újabb verziójával rendelkező sablont telepít, akkor a Count értéket nullára állíthatja. A PowerShell, a CLI és a REST API korábbi verziói nem támogatják a nulla értéket a darabszámhoz.

## <a name="example-templates"></a>Példa sablonok

Az alábbi példa egy olyan általános forgatókönyvet mutat be, amely egy tulajdonság egynél több értékét hozza létre.

|Sablon  |Leírás  |
|---------|---------|
|[VM-telepítés változó számú adatlemezzel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Több adatlemez üzembe helyezése virtuális géppel. |

## <a name="next-steps"></a>Következő lépések

* Az oktatóanyag lépéseinek megismeréséhez lásd [: oktatóanyag: több erőforrás-példány létrehozása Resource Manager-sablonok használatával](template-tutorial-create-multiple-instances.md).
* A másolási elem egyéb felhasználási módjaiért lásd:
  * [Erőforrás-iteráció Azure Resource Manager-sablonokban](copy-resources.md)
  * [Változó iteráció Azure Resource Manager-sablonokban](copy-variables.md)
  * [Kimeneti iteráció Azure Resource Manager-sablonokban](copy-outputs.md)
* Ha szeretné megismerni a sablonok egyes részeit, olvassa el a [Azure Resource Manager sablonok készítése](template-syntax.md)című szakaszt.
* A sablon üzembe helyezésével kapcsolatos további információkért lásd: [alkalmazások központi telepítése Azure Resource Manager sablonnal](deploy-powershell.md).

