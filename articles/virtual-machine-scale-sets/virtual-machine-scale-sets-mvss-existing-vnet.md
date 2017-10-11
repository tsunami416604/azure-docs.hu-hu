---
title: "Egy már meglévő virtuális hálózatot az Azure méretezési készlet sablonban hivatkozhat |} Microsoft Docs"
description: "Útmutató: virtuális hálózat hozzáadása egy meglévő Azure virtuálisgép-méretezési csoport sablon"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: negat
ms.openlocfilehash: 28117d467b491704aed8d45e5eba42530579dfa2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Adjon hozzá egy meglévő virtuális hálózathoz való hivatkozást Azure méretezési készlet sablonban

Ez a cikk bemutatja, hogyan lehet módosítani a [minimális életképes méretezési sablon](./virtual-machine-scale-sets-mvss-start.md) való üzembe helyezés helyett egy új létrehozása meglévő virtuális hálózat.

## <a name="change-the-template-definition"></a>Módosítsa a sablon-definíciót

A minimális életképes méretezési sablon beállítása látható [Itt](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), és a sablon a méretezési készletben meglévő virtuális hálózatban való üzembe helyezésének látható [Itt](https://raw.githubusercontent.com/gatneil/mvss/existing-vnet/azuredeploy.json). Ez a sablon létrehozásához használt különbözeti vizsgáljuk meg (`git diff minimum-viable-scale-set existing-vnet`) adat által adat:

Első lépésként azt adja meg a `subnetId` paraméter. Ez a karakterlánc továbbítódnak a méretezési készlet konfigurációja, amely lehetővé teszi a méretezés, a virtuális gépek telepítéséhez a korábban létrehozott alhálózati azonosító állíthat be. Ez a karakterlánc a következő formátumban kell lennie: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`. Például a skála telepítendő beállítani egy meglévő virtuális hálózat névvel `myvnet`, alhálózati `mysubnet`, erőforráscsoport `myrg`, és az előfizetés `00000000-0000-0000-0000-000000000000`, a subnetId lenne: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

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

Ezt követően a virtuális hálózati erőforrás törölheti azt a `resources` tömbben, mert azt egy meglévő virtuális hálózatot használ, és nem szükséges telepítenie egy új.

```diff
   "variables": {},
   "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2016-12-01",
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

A virtuális hálózat már létezik a sablon telepítése előtt, nincs szükség a dependsOn záradékot a méretezési készletben, a virtuális hálózathoz meg. Ebből kifolyólag azt ezek a sorok törlése:

```diff
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "location": "[resourceGroup().location]",
       "apiVersion": "2016-04-30-preview",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
```

Végül azt adjon át a `subnetId` a felhasználó által megadott paraméter (használata helyett `resourceId` ahhoz, hogy a virtuális hálózat azonosítóját azonos környezetben, amely, mi a minimális életképes méretezési sablon does).

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




## <a name="next-steps"></a>Következő lépések

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
