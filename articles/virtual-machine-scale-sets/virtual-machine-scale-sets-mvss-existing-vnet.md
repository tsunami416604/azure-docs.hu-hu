---
title: Meglévő virtuális hálózat hivatkozása Egy Azure-méretezési csoport sablonjában
description: Megtudhatja, hogyan adhat hozzá virtuális hálózatot egy meglévő Azure Virtuálisgép-méretezési készletsablonhoz
author: mayanknayar
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: e725e75b8b19fd8b3295226639b5e5aeb3736e34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275532"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Hivatkozás hozzáadása meglévő virtuális hálózathoz egy Azure-méretezési csoport sablonjában

Ez a cikk bemutatja, hogyan módosíthatja az [alapméretezési csoport sablonüzembe](virtual-machine-scale-sets-mvss-start.md) helyezése egy meglévő virtuális hálózat létrehozása helyett egy újat.

## <a name="change-the-template-definition"></a>A sablondefiníció módosítása

Egy [korábbi cikkben](virtual-machine-scale-sets-mvss-start.md) létrehoztunk egy egyszerű méretezési csoport sablont. Most antól ezt a korábbi sablont használjuk, és módosítjuk azt, hogy hozzon létre egy sablont, amely egy méretezési csoportot telepít egy meglévő virtuális hálózatba. 

Először adjon `subnetId` hozzá egy paramétert. Ez a karakterlánc a méretezési csoport konfigurációjába kerül, így a méretezési csoport azonosítja az előre létrehozott alhálózatot a virtuális gépek üzembe helyezéséhez. Ennek a karakterláncnak a következő formátumúnak kell lennie:`/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`

Ha például a méretezési csoportot egy meglévő `myvnet`virtuális `mysubnet`hálózatba `myrg`szeretné telepíteni `00000000-0000-0000-0000-000000000000`névvel , alhálózattal `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`, erőforráscsoporttal és előfizetéssel, az alhálózatazonosító a következő lenne: .

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "subnetId": {
+      "type": "string"
     }
   },
```

Ezután törölje a virtuális `resources` hálózati erőforrást a tömbből, mivel egy meglévő virtuális hálózatot használ, és nem kell újat telepítenie.

```diff
   "variables": {},
   "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2018-11-01",
-      "properties": {
-        "addressSpace": {
-          "addressPrefixes": [
-            "10.0.0.0/16"
-          ]
-        },
-        "subnets": [
-          {
-            "name": "mySubnet",
-            "properties": {
-              "addressPrefix": "10.0.0.0/16"
-            }
-          }
-        ]
-      }
-    },
```

A virtuális hálózat már létezik a sablon telepítése előtt, így nincs szükség a méretezési csoport ból a virtuális hálózatra vonatkozó dependsOn záradék megadására. Törölje a következő sorokat:

```diff
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "location": "[resourceGroup().location]",
       "apiVersion": "2019-03-01",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
```

Végül adja át `subnetId` a felhasználó által beállított `resourceId` paramétert (ahelyett, hogy egy virtuális hálózat azonosítóját szeretné leadni ugyanabban a központi telepítésben, ami az alapvető életképes méretezési csoport sablonja.

```diff
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
-                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                          "id": "[parameters('subnetId')]"
                         }
                       }
                     }
```




## <a name="next-steps"></a>További lépések

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
