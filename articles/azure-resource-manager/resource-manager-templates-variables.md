---
title: Az Azure Resource Manager-sablon változóinak |} A Microsoft Docs
description: Ismerteti, hogyan lehet egy Azure Resource Manager-sablonok deklaratív JSON-szintaxist használva a változókat határozhat meg.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/14/2019
ms.author: tomfitz
ms.openlocfilehash: 50feca90d375d6afd3b04afe019ad9f9025f19dc
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56308571"
---
# <a name="variables-section-of-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonok a változók szakaszban
A változók szakaszban, a sablon egész értékek, amelyek segítségével hozhatnak létre. Nem kell definiálnia a változókat, de azok összetett kifejezések csökkentésével gyakran egyszerűbb a sablont.

## <a name="define-and-use-a-variable"></a>Definiálja és egy változó

Az alábbi példa bemutatja a változó definícióját. Létrehoz egy karakterláncértéket a tárfiók neve. Több sablonokban használható függvények használatával jelenik meg a paraméter értékét, és fűzze össze az azt egy egyedi karakterlánccá.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Az erőforrás definiálásakor használja a változót.

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

## <a name="available-definitions"></a>Rendelkezésre álló definíciók

Az előző példa bemutatta, adjon meg egy változót az egyik lehetőség. Az alábbi definíciókat bármelyikét használhatja:

```json
"variables": {
    "<variable-name>": "<variable-value>",
    "<variable-name>": { 
        <variable-complex-type-value> 
    },
    "<variable-object-name>": {
        "copy": [
            {
                "name": "<name-of-array-property>",
                "count": <number-of-iterations>,
                "input": <object-or-value-to-repeat>
            }
        ]
    },
    "copy": [
        {
            "name": "<variable-array-name>",
            "count": <number-of-iterations>,
            "input": <object-or-value-to-repeat>
        }
    ]
}
```

## <a name="configuration-variables"></a>Konfigurációs változók

Használhatja a komplex JSON-típusok meghatározása a kapcsolódó értékeket egy adott környezetben. 

```json
"variables": {
    "environmentSettings": {
        "test": {
            "instanceSize": "Small",
            "instanceCount": 1
        },
        "prod": {
            "instanceSize": "Large",
            "instanceCount": 4
        }
    }
},
```

A paraméterek hozzon létre egy értéket, amely jelzi, hogy melyik konfigurációs értékeket kell használni.

```json
"parameters": {
    "environmentName": {
        "type": "string",
        "allowedValues": [
          "test",
          "prod"
        ]
    }
},
```

A jelenlegi beállítások kérheti le:

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="use-copy-element-in-variable-definition"></a>A változó definícióját másolási eleme használata

Egy változó több példány létrehozásához használja a `copy` tulajdonság a változók szakaszban. Létrehozhat értéke értékekből összeállított elemek tömbjét a `input` tulajdonság. Használhatja a `copy` tulajdonság egy változóban, vagy a változók szakaszban, a legfelső szinten. Használata esetén `copyIndex` egy változó iteráció belül meg kell adnia az iteráció nevét.

Az alábbi példa bemutatja, hogyan használhatja a másolás:

```json
"variables": {
  "disk-array-on-object": {
    "copy": [
      {
        "name": "disks",
        "count": 3,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('disks')]"
        }
      }
    ]
  },
  "copy": [
    {
      "name": "disks-top-level-array",
      "count": 3,
      "input": {
        "name": "[concat('myDataDisk', copyIndex('disks-top-level-array', 1))]",
        "diskSizeGB": "1",
        "diskIndex": "[copyIndex('disks-top-level-array')]"
      }
    },
    {
      "name": "top-level-string-array",
      "count": 5,
      "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
    }
  ]
},
```

A másolási kifejezés kiértékelése után a változó **lemez-tömb-a-objektum** tartalmazza a következő objektum nevű tömbben **lemezek**:

```json
{
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
    }
  ]
}
```

A változó **lemezek – legfelső-szintű-tömb** tartalmazza az alábbi tömböt:

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
  }
]
```

A változó **top-szint – karakterlánc-tömbben** tartalmazza az alábbi tömböt:

```json
[
  "myDataDisk1",
  "myDataDisk2",
  "myDataDisk3",
  "myDataDisk4",
  "myDataDisk5"
]
```

Másolással működik jól, ha a paraméter értékeit, és megfeleltet az erőforrás-értékeket kell. Az alábbi példa formázza a biztonsági szabályok definiálásához paraméterértékeket:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "securityRules": {
      "type": "array"
    }
  },
  "variables": {
    "copy": [
      {
        "name": "securityRules",
        "count": "[length(parameters('securityRules'))]",
        "input": {
          "name": "[parameters('securityRules')[copyIndex('securityRules')].name]",
          "properties": {
            "description": "[parameters('securityRules')[copyIndex('securityRules')].description]",
            "priority": "[parameters('securityRules')[copyIndex('securityRules')].priority]",
            "protocol": "[parameters('securityRules')[copyIndex('securityRules')].protocol]",
            "sourcePortRange": "[parameters('securityRules')[copyIndex('securityRules')].sourcePortRange]",
            "destinationPortRange": "[parameters('securityRules')[copyIndex('securityRules')].destinationPortRange]",
            "sourceAddressPrefix": "[parameters('securityRules')[copyIndex('securityRules')].sourceAddressPrefix]",
            "destinationAddressPrefix": "[parameters('securityRules')[copyIndex('securityRules')].destinationAddressPrefix]",
            "access": "[parameters('securityRules')[copyIndex('securityRules')].access]",
            "direction": "[parameters('securityRules')[copyIndex('securityRules')].direction]"
          }
        }
      }
    ]
  },
  "resources": [
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "NSG1",
      "location": "[resourceGroup().location]",
      "properties": {
        "securityRules": "[variables('securityRules')]"
      }
    }
  ],
  "outputs": {}
}
```

## <a name="example-templates"></a>Példa sablonok

A példa sablonok bizonyos változók használatára vonatkozó forgatókönyvek bemutatása. Tesztelje a változók kezelésének módját a különböző helyzetekben való telepítéséhez. 

|Sablon  |Leírás  |
|---------|---------|
| [változó definíciók](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Azt ismerteti, hogy a különböző típusú változót. A sablon nem üzembe erőforrásokat. Ez változóértékek entitástörzséhez, és ezeket az értékeket ad vissza. |
| [konfigurációs változó](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Bemutatja, hogy egy változó, amely a konfigurációs értékeket határozza meg. A sablon nem üzembe erőforrásokat. Ez változóértékek entitástörzséhez, és ezeket az értékeket ad vissza. |
| [hálózati biztonsági szabály](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) és [alkalmazásparaméter-fájlt](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Létrehoz egy tömböt a biztonsági szabályok rendel egy hálózati biztonsági csoportot a megfelelő formátumban. |


## <a name="next-steps"></a>További lépések
* A különböző megoldástípusokhoz használható teljes sablonok megtekintéséhez lásd: [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/).
* A sablonon belül használhatja függvényeivel kapcsolatos részletekért lásd: [Azure Resource Manager-Sablonfüggvények](resource-group-template-functions.md).
* Sablonok létrehozásával kapcsolatos ajánlások, lásd: [gyakorlati tanácsok az Azure Resource Manager-sablon](template-best-practices.md).
* Szükség lehet a belül egy másik erőforráscsoportban található erőforrások. Ebben a forgatókönyvben nem gyakori, ha a storage-fiókok vagy a virtuális hálózatokat, amelyek egynél több erőforráscsoport vannak megosztva. További információkért lásd: a [resourceId függvény](resource-group-template-functions-resource.md#resourceid).