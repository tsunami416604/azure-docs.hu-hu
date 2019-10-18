---
title: Gyermek erőforrások – Azure Resource Manager sablonok
description: Ismerteti, hogyan lehet a Azure Resource Manager sablonban megadhatja a gyermek-erőforrások nevét és típusát.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: tomfitz
ms.openlocfilehash: 3a90b2155b11d4c12bc1f571af3f15fdbceb12b9
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72532299"
---
# <a name="set-name-and-type-for-child-resources"></a>Adja meg a gyermek erőforrások nevét és típusát

A gyermek erőforrások olyan erőforrások, amelyek csak egy másik erőforrás kontextusában vannak. A virtuálisgép- [bővítmények](/azure/templates/microsoft.compute/2019-03-01/virtualmachines/extensions) például nem létezhetnek [virtuális gép](/azure/templates/microsoft.compute/2019-03-01/virtualmachines)nélkül. A bővítmény erőforrás a virtuális gép gyermeke.

Egy Resource Manager-sablonban megadhatja a gyermek erőforrást a szülő erőforráson belül vagy a szülő erőforráson kívül is. Az alábbi példa a szülő erőforrás erőforrások tulajdonságában található gyermek erőforrást mutatja be.

```json
"resources": [
  {
    <parent-resource>
    "resources": [
      <child-resource>
    ]
  }
]
```

A következő példa a alárendelt erőforrást a szülő erőforráson kívül mutatja. Ezt a módszert akkor érdemes használni, ha a fölérendelt erőforrás nem ugyanabban a sablonban van telepítve, vagy ha a [Másolás](resource-group-create-multiple.md) használatával több alárendelt erőforrást szeretne létrehozni.

```json
"resources": [
  {
    <parent-resource>
  },
  {
    <child-resource>
  }
]
```

Az erőforrás neve és típusa számára megadott értékek attól függően változnak, hogy a gyermek erőforrás a szülő erőforráson belül vagy kívül van-e definiálva.

## <a name="within-parent-resource"></a>Szülő erőforráson belül

Ha a szülő erőforrástípus definiálja, a típus és a név értékeket perjel nélkül formázhatja egyetlen szóval.

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

Az alábbi példa egy virtuális hálózatot és egy alhálózatot mutat be. Figyelje meg, hogy az alhálózat szerepel a virtuális hálózathoz tartozó erőforrások tömbben. A név a **Subnet1** értékre van állítva, a típus pedig **alhálózatokra**van beállítva. A gyermek erőforrás a szülő erőforrástól függőként van megjelölve, mert a szülő erőforrásnak léteznie kell a gyermek erőforrás üzembe helyezése előtt.

```json
"resources": [
  {
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    },
    "resources": [
      {
        "apiVersion": "2018-10-01",
        "type": "subnets",
        "location": "[parameters('location')]",
        "name": "Subnet1",
        "dependsOn": [
          "VNet1"
        ],
        "properties": {
          "addressPrefix": "10.0.0.0/24"
        }
      }
    ]
  }
]
```

A teljes erőforrástípus továbbra is **Microsoft. Network/virtualNetworks/Subnets**. Nem biztosítja a **Microsoft. Network/virtualNetworks/** , mert a szülő erőforrástípus feltételezi.

A gyermek erőforrás neve **Subnet1** értékre van állítva, de a teljes név tartalmazza a szülő nevét. Nem biztosít **VNet1** , mert a szülő erőforrásból feltételezhető.

## <a name="outside-parent-resource"></a>Fölérendelt erőforráson kívül

Ha a szülő erőforráson kívül van definiálva, a típust és a perjelet formázva adja meg a szülő típusát és nevét.

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

Az alábbi példa egy virtuális hálózatot és alhálózatot mutat be, amely a legfelső szinten van meghatározva. Figyelje meg, hogy az alhálózat nem része a virtuális hálózathoz tartozó erőforrások tömbnek. A név a **VNet1/Subnet1** értékre van beállítva, és a típus a **Microsoft. Network/virtualNetworks/Subnets**értékre van beállítva. A gyermek erőforrás a szülő erőforrástól függőként van megjelölve, mert a szülő erőforrásnak léteznie kell a gyermek erőforrás üzembe helyezése előtt.

```json
"resources": [
  {
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    }
  },
  {
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Network/virtualNetworks/subnets",
    "location": "[parameters('location')]",
    "name": "VNet1/Subnet1",
    "dependsOn": [
      "VNet1"
    ],
    "properties": {
      "addressPrefix": "10.0.0.0/24"
    }
  }
]
```

## <a name="next-steps"></a>Következő lépések

* Azure Resource Manager sablonok létrehozásával kapcsolatos további tudnivalókért lásd: [sablonok készítése](resource-group-authoring-templates.md). 

* Ha többet szeretne megtudni az erőforrás nevének formátumáról az erőforrásra való hivatkozáskor, tekintse meg a [Reference függvényt](resource-group-template-functions-resource.md#reference).
