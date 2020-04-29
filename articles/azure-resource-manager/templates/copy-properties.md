---
title: Egy tulajdonság több példányának meghatározása
description: A másolási művelettel egy Azure Resource Manager sablonban több alkalommal is megismételheti a tulajdonságok egy erőforráson való létrehozásakor.
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 831ae1af202a1cdf52bdd2bdf0d9a042a97ba52f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81391341"
---
# <a name="property-iteration-in-arm-templates"></a>Tulajdonság-iteráció az ARM-sablonokban

Ebből a cikkből megtudhatja, hogyan hozhat létre egy tulajdonság több példányát a Azure Resource Manager-(ARM-) sablonban. Ha hozzáadja a **Másolás** elemet a sablon egyik erőforrásának Properties (Tulajdonságok) szakaszához, akkor dinamikusan állíthatja be a tulajdonság elemeinek számát a telepítés során. Emellett ne kelljen megismételni a sablon szintaxisát.

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

A **név mezőben**adja meg a létrehozni kívánt erőforrás-tulajdonság nevét.

A **Count** tulajdonság megadja a tulajdonsághoz használni kívánt iterációk számát.

A **bemeneti** tulajdonság határozza meg a megismételni kívánt tulajdonságokat. A **bemeneti** tulajdonság értékével létrehozott elemek tömbjét hozza létre.

Az alábbi példa bemutatja, hogyan alkalmazható `copy` a virtuális gép dataDisks tulajdonságára:

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

Figyelje meg, hogy `copyIndex` ha egy tulajdonság-iteráción belül használ, meg kell adnia az iteráció nevét. A tulajdonság iterációja egy eltolási argumentumot is támogat. Az eltolásnak az iteráció neve után kell érkeznie, például copyIndex (' dataDisks ', 1).

A Resource Manager kibontja a tömböt az `copy` üzembe helyezés során. A tömb neve lesz a tulajdonság neve. A bemeneti értékek az objektum tulajdonságai lesznek. A központilag telepített sablon a következőket válik:

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

A másolási művelet hasznos lehet a tömbök használatakor, mert a tömb minden elemén megismételhető. Használja a `length` tömb függvényét az iterációk számának megadásához, valamint `copyIndex` a tömb aktuális indexének lekéréséhez.

A következő példa sablon létrehoz egy feladatátvételi csoportot a tömbként átadott adatbázisok számára.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "primaryServerName": {
            "type": "string"
        },
        "secondaryServerName": {
            "type": "string"
        },
        "databaseNames": {
            "type": "array",
            "defaultValue": [
                "mydb1",
                "mydb2",
                "mydb3"
            ]
        }
    },
    "variables": {
        "failoverName": "[concat(parameters('primaryServerName'),'/', parameters('primaryServerName'),'failovergroups')]"
    },
    "resources": [
        {
            "type": "Microsoft.Sql/servers/failoverGroups",
            "apiVersion": "2015-05-01-preview",
            "name": "[variables('failoverName')]",
            "properties": {
                "readWriteEndpoint": {
                    "failoverPolicy": "Automatic",
                    "failoverWithDataLossGracePeriodMinutes": 60
                },
                "readOnlyEndpoint": {
                    "failoverPolicy": "Disabled"
                },
                "partnerServers": [
                    {
                        "id": "[resourceId('Microsoft.Sql/servers', parameters('secondaryServerName'))]"
                    }
                ],
                "copy": [
                    {
                        "name": "databases",
                        "count": "[length(parameters('databaseNames'))]",
                        "input": "[resourceId('Microsoft.Sql/servers/databases', parameters('primaryServerName'), parameters('databaseNames')[copyIndex('databases')])]"
                    }
                ]
            }
        }
    ],
    "outputs": {
    }
}
```

A másolási elem egy tömb, így több tulajdonság is megadható az erőforráshoz.

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

## <a name="example-templates"></a>Példák sablonokra

Az alábbi példa egy olyan általános forgatókönyvet mutat be, amely egy tulajdonság egynél több értékét hozza létre.

|Sablon  |Leírás  |
|---------|---------|
|[VM-telepítés változó számú adatlemezzel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Több adatlemez üzembe helyezése virtuális géppel. |

## <a name="next-steps"></a>További lépések

* Az oktatóanyag lépéseinek megismeréséhez tekintse meg az [oktatóanyag: több erőforrás-példány létrehozása ARM-sablonok használatával](template-tutorial-create-multiple-instances.md)című témakört.
* A másolási elem egyéb felhasználási módjaiért lásd:
  * [Erőforrás-iteráció az ARM-sablonokban](copy-resources.md)
  * [Változó iteráció az ARM-sablonokban](copy-variables.md)
  * [Kimeneti iteráció az ARM-sablonokban](copy-outputs.md)
* Ha szeretne többet megtudni egy sablon fejezeteiről, tekintse meg a következő témakört: [ARM-sablonok készítése](template-syntax.md).
* A sablon üzembe helyezésével kapcsolatos további információkért lásd: [alkalmazás üzembe helyezése ARM-sablonnal](deploy-powershell.md).

