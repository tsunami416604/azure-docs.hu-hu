---
title: Az Azure Resource Manager sablon változók |} Microsoft Docs
description: Adja meg a változókat az Azure Resource Manager-sablonok deklaratív JSON-szintaxis használatával ismerteti.
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
ms.date: 12/12/2017
ms.author: tomfitz
ms.openlocfilehash: 08728a3c0b4d4578939004e2d1b1ee2d30a682ab
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359288"
---
# <a name="variables-section-of-azure-resource-manager-templates"></a>Változók szakaszban az Azure Resource Manager-sablonok
A változók szakaszban, a sablon egész érték, amely használható hozhat létre. Nem kell meghatároznia a változót, de azok gyakran leegyszerűsítheti a sablon csökkentésével összetett kifejezések.

## <a name="define-and-use-a-variable"></a>Adja meg, és a változók használata

A következő példa bemutatja a változó definícióját. Létrehoz egy a tárfiók nevének karakterlánc-érték. Több sablon függvény használatával jelenik meg a paraméter értékét, és azt egy egyedi karakterlánc összefűzésére.

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

## <a name="available-definitions"></a>Definíciók

Az előző példában bemutatta egyik módja változó. A következő definíciókra bármelyikét használhatja:

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
                "input": {
                    <properties-to-repeat>
                }
            }
        ]
    },
    "copy": [
        {
            "name": "<variable-array-name>",
            "count": <number-of-iterations>,
            "input": {
                <properties-to-repeat>
            }
        }
    ]
}
```

## <a name="configuration-variables"></a>Konfigurációs változók

Összetett JSON-típusok segítségével megadhatja a kapcsolódó értékekről a környezetben. 

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

A paraméterek hozzon létre egy értéket, amely jelzi, hogy melyik konfiguráció értékeit használja a.

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

Az aktuális beállításainak beolvasása:

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="use-copy-element-in-variable-definition"></a>Elem másolása használja a változó definícióját

Használhatja a **másolási** szintaxisát, és hozzon létre egy változót több elem tömbjét. A szám elemek számát adja meg. Minden elem tartalmazza-e a tulajdonságokat a **bemeneti** objektum. Másolás vagy egy változó belül, vagy hozza létre a változót használhatja. Ha meg egy változót, és használjon **másolása** belül ezt a változót hoz létre olyan objektum, amely a tömb tulajdonsága. Amikor **másolási** legfelső szintjén, és hozzon létre egyet, vagy belül több változót hoz létre egy vagy több tömbök. Mindkét megközelítés az alábbi példában látható:

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
        }
    ]
},
```

A változó **-tömb-a-objektumát** tartalmazza a következő objektum nevű tömb **lemezek**:

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

A változó **lemezek legfelső-szintű-tömb** a következő tömböt tartalmaz:

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

Több objektum történő másolás használatával történő létrehozásához változók is megadhat. A következő példában két tömb változók határozza meg. Egy nevű **lemezek legfelső-szintű-tömb** és öt elemmel rendelkezik. A másik nevű **egy másik-tömb** és három elemmel rendelkezik.

```json
"variables": {
    "copy": [
        {
            "name": "disks-top-level-array",
            "count": 5,
            "input": {
                "name": "[concat('oneDataDisk', copyIndex('disks-top-level-array', 1))]",
                "diskSizeGB": "1",
                "diskIndex": "[copyIndex('disks-top-level-array')]"
            }
        },
        {
            "name": "a-different-array",
            "count": 3,
            "input": {
                "name": "[concat('twoDataDisk', copyIndex('a-different-array', 1))]",
                "diskSizeGB": "1",
                "diskIndex": "[copyIndex('a-different-array')]"
            }
        }
    ]
},
```

Ez a módszer jól működik, ha a paraméterértékek eltarthat, és győződjön meg arról, hogy a sablon értéket a megfelelő formátumban kell. Az alábbi példa formázza a biztonsági szabályok definiálásához paraméterértékek:

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

## <a name="recommendations"></a>Javaslatok
Az alábbi információ segítségével esetleg megállapítható, változók használata:

* Változók használata sablonban egynél többször használni kívánt értékeket. Ha az érték csak egyszer legyen használva, kódolt értéket a sablon olvashatóbbá teszi.
* Nem használhatja a [hivatkozás](resource-group-template-functions-resource.md#reference) működni a **változók** a sablon szakasza. A **hivatkozás** függvény az értékét az erőforrás futásidejű állapot osztályból származik. Azonban változók, megtörténik a sablon kezdeti elemzése során. Szerkezet értékei, amelyen a kell a **hivatkozás** működéséhez közvetlenül a **erőforrások** vagy **kimenete** a sablon szakasza.
* Az erőforrás nevének egyedinek kell lennie, változókat tartalmazhat.

## <a name="example-templates"></a>Példa sablonok

A példa sablonok bemutatják, bizonyos esetekben a változók használata. Tesztelje a változók kezelésének módját a különböző helyzetekben való telepítéséhez. 

|Sablon  |Leírás  |
|---------|---------|
| [változó definíciók](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | A különböző típusú változók mutatja be. A sablon nem kell telepítenie minden olyan erőforrásnál. Változók értékeinek hoz létre, és ezeket az értékeket adja vissza. |
| [konfigurációs változó](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Bemutatja, hogy egy változó, amely meghatározza a konfigurációs értékeket. A sablon nem kell telepítenie minden olyan erőforrásnál. Változók értékeinek hoz létre, és ezeket az értékeket adja vissza. |
| [hálózati biztonsági szabályok](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) és [paraméterfájl](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | A biztonsági szabályok hozzárendelése a hálózati biztonsági csoportok a megfelelő formátumban tömb hoz létre. |


## <a name="next-steps"></a>További lépések
* A különböző megoldástípusokhoz használható teljes sablonok megtekintéséhez lásd: [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/).
* A sablonon belül használhatja a functions szolgáltatással kapcsolatos részletekért lásd: [Azure Resource Manager Sablonfüggvényei](resource-group-template-functions.md).
* Egyesítenie több sablon üzembe helyezése során, lásd: [kapcsolt sablonok használata az Azure Resource Manager](resource-group-linked-templates.md).
* Szükség lehet egy másik erőforráscsoportban található erőforrások használatával. Ez a forgatókönyv nem közös, ha a storage-fiókok vagy több erőforrás csoporttal megosztott virtuális hálózatok. További információkért lásd: a [resourceId függvény](resource-group-template-functions-resource.md#resourceid).