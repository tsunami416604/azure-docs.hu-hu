---
title: Egyéni lemezkép hivatkozása Azure-méretezési csoportsablonban
description: Megtudhatja, hogyan adhat hozzá egyéni lemezképet egy meglévő Azure Virtuálisgép-méretezési készletsablonhoz
author: mayanknayar
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: manayar
ms.openlocfilehash: fd1a567af1c35cf6b659995e998b11a61a526508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275593"
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Egyéni rendszerkép hozzáadása egy Azure méretezésicsoport-sablonhoz

Ez a cikk bemutatja, hogyan módosíthatja az [alapvető méretezési csoport sablont](virtual-machine-scale-sets-mvss-start.md) az egyéni lemezkép üzembe helyezéséhez.

## <a name="change-the-template-definition"></a>A sablondefiníció módosítása
Egy [korábbi cikkben](virtual-machine-scale-sets-mvss-start.md) létrehoztunk egy egyszerű méretezési csoport sablont. Most antól ezt a korábbi sablont használjuk, és módosítjuk azt, hogy hozzon létre egy sablont, amely egy egyéni lemezképből származó méretezési készletet telepít.  

### <a name="creating-a-managed-disk-image"></a>Felügyelt lemezkép létrehozása

Ha már rendelkezik egyéni felügyelt lemezképpel `Microsoft.Compute/images`(egy erőforrás típus), akkor kihagyhatja ezt a szakaszt.

Először adjon `sourceImageVhdUri` hozzá egy paramétert, amely az URI az azure storage-ban az általános blobhoz, amely tartalmazza az egyéni lemezképet, amelyből üzembe helyezhető.


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

Ezután adjon hozzá `Microsoft.Compute/images`egy típusú erőforrást , amely az URI-ban `sourceImageVhdUri`található általános blobon alapuló felügyelt lemezlemez-lemezkép. Ennek a lemezképnek ugyanabban a régióban kell lennie, mint az azt használó méretezési készletnek. A lemezkép tulajdonságai között adja meg az operációs rendszer típusát, a blob helyét (a `sourceImageVhdUri` paraméterből) és a tárfiók típusát:

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

A méretezési csoport erőforrásban adjon hozzá egy, `dependsOn` az egyéni lemezképre hivatkozó záradékot, amely biztosítja, hogy a lemezkép létrejön, mielőtt a méretezési csoport megpróbálna telepíteni az adott lemezképről:

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

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>A méretezési csoport tulajdonságainak módosítása a felügyelt lemezkép használatára

A `imageReference` méretezési csoportban `storageProfile`a közzétevő, az ajánlat, a termékváltozat és a `id` platformlemezkép verziójának megadása helyett adja meg az `Microsoft.Compute/images` erőforrás t:

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

Ebben a példában `resourceId` használja a függvényt az ugyanabban a sablonban létrehozott lemezkép erőforrás-azonosítójának leéséhez. Ha a felügyelt lemezképet előzetesen létrehozta, akkor inkább a lemezkép azonosítóját adja meg. Ennek az azonosítónak a `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`következő formában kell lennie: .


## <a name="next-steps"></a>Következő lépések

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
