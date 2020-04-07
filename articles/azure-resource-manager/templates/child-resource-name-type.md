---
title: Gyermekerőforrások sablonokban
description: Ez a témakör azt ismerteti, hogy miként állíthatja be a gyermekerőforrások nevét és típusát egy Azure Resource Manager-sablonban.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 3a69829e674925982c618807f49433a033d8c5f9
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743837"
---
# <a name="set-name-and-type-for-child-resources"></a>Gyermekerőforrások nevének és típusának beállítása

A gyermekerőforrások olyan erőforrások, amelyek csak egy másik erőforrás környezetében léteznek. Egy [virtuálisgép-bővítmény](/azure/templates/microsoft.compute/2019-03-01/virtualmachines/extensions) például nem létezhet [virtuális gép](/azure/templates/microsoft.compute/2019-03-01/virtualmachines)nélkül. A bővítmény erőforrás a virtuális gép gyermeke.

Az Erőforrás-kezelő sablonban megadhatja a gyermekerőforrást a szülő erőforráson belül vagy a fölérendelt erőforráson kívül. A következő példa a szülő erőforrás erőforrás-tulajdonságában szereplő gyermekerőforrást mutatja be.

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

A következő példa a fölérendelt erőforráson kívüli gyermekerőforrást mutatja be. Ezt a módszert akkor használhatja, ha a szülőerőforrás nincs telepítve ugyanabban a sablonban, vagy ha egynél több gyermekerőforrás létrehozásához szeretne [másolatot](copy-resources.md) használni.

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

Az erőforrás nevéhez és típusához megadott értékek attól függően változnak, hogy a gyermek erőforrás a szülő erőforráson belül vagy kívül van-e definiálva.

## <a name="within-parent-resource"></a>Szülőerőforráson belül

Ha a szülőerőforrás-típuson belül van definiálva, a szöveg- és névértékeket egyetlen szóként, perések nélkül formázhatja.

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

A következő példa egy virtuális hálózatot és egy alhálózatot mutat be. Figyelje meg, hogy az alhálózat a virtuális hálózat erőforrástömbjének része. A név **alhálózat1,** a típus pedig **alhálózatok.** A gyermekerőforrás a szülőerőforrástól függőként van megjelölve, mert a szülő erőforrásnak léteznie kell a gyermekerőforrás üzembe helyezése előtt.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
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
        "type": "subnets",
        "apiVersion": "2018-10-01",
        "name": "Subnet1",
        "location": "[parameters('location')]",
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

A teljes erőforrástípus továbbra is **Microsoft.Network/virtualNetworks/subnets**. Nem adja meg a **Microsoft.Network/virtualNetworks/** mert a szülő erőforrás típusából feltételezi.

A gyermekerőforrás neve **Alhálózat1,** de a teljes név tartalmazza a szülőnevet. Nem adja meg **a Virtuális hálózat1,** mert a szülő erőforrás feltételezi.

## <a name="outside-parent-resource"></a>Külső fölérendelt erőforrás

Ha a szülőerőforráson kívül van definiálva, formázza a típust, és a perjellel tartalmazza a szülőtípust és a nevet.

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

A következő példa egy virtuális hálózatot és alhálózatot mutat be, amelyek a gyökérszinten vannak definiálva. Figyelje meg, hogy az alhálózat nem szerepel a virtuális hálózat erőforrás-tömbjének. A név **VNet1/Subnet1,** a típus pedig **Microsoft.Network/virtualNetworks/subnets**. A gyermekerőforrás a szülőerőforrástól függőként van megjelölve, mert a szülő erőforrásnak léteznie kell a gyermekerőforrás üzembe helyezése előtt.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
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
    "type": "Microsoft.Network/virtualNetworks/subnets",
    "apiVersion": "2018-10-01",
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

## <a name="next-steps"></a>További lépések

* Az Azure Resource Manager-sablonok létrehozásáról a Sablonok készítése című [témakörben olvashat.](template-syntax.md)

* Az erőforrás nevének formátumáról az erőforrásra való hivatkozáskor a [hivatkozási függvényben](template-functions-resource.md#reference)olvashat.
