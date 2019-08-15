---
title: Egyéni rendszerkép referenciája egy Azure méretezési csoport sablonjában | Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá egyéni rendszerképet egy meglévő Azure virtuálisgép-méretezési csoport sablonhoz
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: manayar
ms.openlocfilehash: 2ed75a72360253996471034b001e12e8190cf733
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935269"
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Egyéni rendszerkép hozzáadása egy Azure méretezési csoport sablonhoz

Ez a cikk bemutatja, hogyan módosíthatja az alapszintű [méretezési csoport sablonját](virtual-machine-scale-sets-mvss-start.md) az egyéni lemezképből való üzembe helyezéshez.

## <a name="change-the-template-definition"></a>A sablon definíciójának módosítása
Egy [korábbi cikkben](virtual-machine-scale-sets-mvss-start.md) egy alapszintű méretezési csoport sablont hoztunk létre. Most ezt a korábbi sablont fogjuk használni, és módosítjuk egy olyan sablon létrehozásához, amely egy méretezési csoport üzembe helyezését végzi egy egyéni rendszerképből.  

### <a name="creating-a-managed-disk-image"></a>Felügyelt lemez rendszerképének létrehozása

Ha már rendelkezik egy egyéni felügyelt lemezképtel (egy típusú `Microsoft.Compute/images`erőforrással), akkor kihagyhatja ezt a szakaszt.

Először adja hozzá `sourceImageVhdUri` a paramétert, amely az Azure Storage-ban található általánosított blob URI-ja, amely a-ból telepítendő egyéni lemezképet tartalmazza.


```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "sourceImageVhdUri": {
+      "type": "string",
+      "metadata": {
+        "description": "The source of the generalized blob containing the custom image"
+      }
     }
   },
   "variables": {},
```

Ezután adjon hozzá egy típusú `Microsoft.Compute/images`erőforrást, amely a felügyelt lemezkép az URI `sourceImageVhdUri`-n található általánosított blob alapján. Ennek a képnek ugyanabban a régióban kell lennie, mint az azt használó méretezési csoportnak. A rendszerkép tulajdonságainál adja meg az operációs rendszer típusát, a blob helyét (a `sourceImageVhdUri` paraméterből) és a Storage-fiók típusát:

```diff
   "resources": [
     {
+      "type": "Microsoft.Compute/images",
+      "apiVersion": "2019-03-01",
+      "name": "myCustomImage",
+      "location": "[resourceGroup().location]",
+      "properties": {
+        "storageProfile": {
+          "osDisk": {
+            "osType": "Linux",
+            "osState": "Generalized",
+            "blobUri": "[parameters('sourceImageVhdUri')]",
+            "storageAccountType": "Standard_LRS"
+          }
+        }
+      }
+    },
+    {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "location": "[resourceGroup().location]",

```

A méretezési csoport erőforrásban vegyen `dependsOn` fel egy olyan záradékot, amely az egyéni lemezképre hivatkozik, hogy a rendszer létrehozza a lemezképet, mielőtt a méretezési csoport megpróbálja telepíteni a lemezképből:

```diff
       "location": "[resourceGroup().location]",
       "apiVersion": "2019-03-01-preview",
       "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
+        "Microsoft.Network/virtualNetworks/myVnet",
+        "Microsoft.Compute/images/myCustomImage"
       ],
       "sku": {
         "name": "Standard_A1",

```

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Méretezési csoport tulajdonságainak módosítása a felügyelt lemezkép használatára

A méretezési csoporton `storageProfile`belül a `id` platform-rendszerkép közzétevője, ajánlata, SKU-jának és verziójának megadása helyett adja meg `Microsoft.Compute/images` az erőforrást: `imageReference`

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

Ebben a példában a `resourceId` függvénnyel lekérheti az ugyanabban a sablonban létrehozott rendszerkép erőforrás-azonosítóját. Ha korábban már létrehozta a felügyelt lemezképet, adja meg a rendszerkép AZONOSÍTÓját. Ennek az AZONOSÍTÓnak a következő formátumúnak `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`kell lennie:.


## <a name="next-steps"></a>További lépések

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
