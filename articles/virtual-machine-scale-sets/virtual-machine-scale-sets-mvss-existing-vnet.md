---
title: Egy meglévő virtuális hálózatot az Azure méretezési csoport sablonjában hivatkozhat |} A Microsoft Docs
description: 'Útmutató: virtuális hálózat hozzáadása egy meglévő Azure virtuálisgép-méretezési csoport sablon'
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: manayar
ms.openlocfilehash: 1dcb97a94bd5790edc2e40acf890bb47baec7a4b
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50740093"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Adjon hozzá egy meglévő virtuális hálózatot a hivatkozást az egy Azure méretezésicsoport-sablon

Ez a cikk bemutatja, hogyan lehet módosítani a [minimális működőképes méretezési csoport sablon](./virtual-machine-scale-sets-mvss-start.md) helyezheti üzembe egy meglévő virtuális hálózatot egy új létrehozása helyett.

## <a name="change-the-template-definition"></a>A Sablondefiníció módosítása

A minimális működőképes méretezési csoport sablonjának látható [Itt](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), és láthatja a sablon üzembe helyezéséhez a méretezési csoport meglévő virtuális hálózatban [Itt](https://raw.githubusercontent.com/gatneil/mvss/existing-vnet/azuredeploy.json). Most vizsgálja meg a sablon létrehozásához használt diff (`git diff minimum-viable-scale-set existing-vnet`) darab által darab:

Először adja hozzá a `subnetId` paraméter. Ez a karakterlánc átad a méretezésicsoport-konfigurációt, lehetővé téve a méretezési csoport azonosításához az előre létrehozott alhálózat, virtuális gépek telepítéséhez. Ez a karakterlánc a következő formában kell lennie: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`. Például a méretezési csoport üzembe helyezése állítsa nevű meglévő virtuális hálózatban `myvnet`, alhálózat `mysubnet`, erőforráscsoport `myrg`, és az előfizetés `00000000-0000-0000-0000-000000000000`, a subnetId lenne: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

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

Ezután törölje a virtuális hálózati erőforrás a `resources` tömbben, meglévő virtuális hálózat használata és a egy új telepítése nem szükséges.

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

A virtuális hálózat már létezik a sablon üzembe helyezése előtt, így nem kell megadnia a dependsOn záradékot a méretezési csoportból, a virtuális hálózat. Törölje a következő sorokat:

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

Végül adja át a `subnetId` a felhasználó által megadott paraméter (használata helyett `resourceId` beolvasni a virtuális hálózat Azonosítóját az egyazon telepítésben, azaz, hogy mi a minimális működőképes méretezési csoport sablon does).

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
