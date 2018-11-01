---
title: Egy egyéni rendszerképet egy Azure méretezési csoport sablonjában hivatkozhat |} A Microsoft Docs
description: 'Útmutató: egyéni rendszerkép hozzáadása egy meglévő Azure virtuálisgép-méretezési csoport sablon'
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
ms.date: 5/10/2017
ms.author: manayar
ms.openlocfilehash: 2e3c8177a32082c251be74e597a18730ae1c9d37
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50739642"
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Egyéni rendszerkép hozzáadása egy Azure méretezésicsoport-sablon

Ez a cikk bemutatja, hogyan lehet módosítani a [minimális működőképes méretezési csoport sablon](./virtual-machine-scale-sets-mvss-start.md) egyéni lemezkép alapján üzembe helyezéséhez.

## <a name="change-the-template-definition"></a>A Sablondefiníció módosítása

A minimális működőképes méretezési csoport sablonjának látható [Itt](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), és a sablon üzembe helyezése a méretezési csoport egyéni rendszerképpel beállítása látható [Itt](https://raw.githubusercontent.com/gatneil/mvss/custom-image/azuredeploy.json). Most vizsgálja meg a sablon létrehozásához használt diff (`git diff minimum-viable-scale-set custom-image`) darab által darab:

### <a name="creating-a-managed-disk-image"></a>Felügyelt lemez lemezkép létrehozása

Ha már rendelkezik egy egyéni rendszerképpel felügyelt (típusú erőforrást `Microsoft.Compute/images`), majd ezt a szakaszt kihagyhatja.

Először adja hozzá a `sourceImageVhdUri` paraméter, amely az Azure Storage-ban, amely tartalmazza az egyéni rendszerkép telepítheti az általánosított blob URI-t.


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

Ezután adjon hozzá egy típusú erőforrás `Microsoft.Compute/images`, azaz a felügyelt lemezt az általánosított blob URI-n található található Képalapú `sourceImageVhdUri`. Ez a rendszerkép a méretezési csoportban, ami azt használja ugyanabban a régióban kell lennie. A lemezkép tulajdonságait adja meg az operációs rendszer típusát, a blob helyét (az a `sourceImageVhdUri` paraméter), és a tárfiók típusa:

```diff
   "resources": [
     {
+      "type": "Microsoft.Compute/images",
+      "apiVersion": "2016-04-30-preview",
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

A méretezésicsoport-erőforrás, adjon hozzá egy `dependsOn` az egyéni rendszerképet, győződjön meg arról, hogy a kép hivatkozó záradék előtt a méretezési csoport üzembe helyezése a rendszerképből próbál jön létre:

```diff
       "location": "[resourceGroup().location]",
       "apiVersion": "2016-04-30-preview",
       "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
+        "Microsoft.Network/virtualNetworks/myVnet",
+        "Microsoft.Compute/images/myCustomImage"
       ],
       "sku": {
         "name": "Standard_A1",

```

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Skála módosítása állítsa be a tulajdonságokat a felügyelt lemez-rendszerkép használata

Az a `imageReference` a méretezési csoport beállítása `storageProfile`, helyett adja meg a közzétevő, ajánlat, termékváltozat, és a platformlemezkép verzióját adja meg a `id` , a `Microsoft.Compute/images` erőforrás:

```diff
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
-              "publisher": "Canonical",
-              "offer": "UbuntuServer",
-              "sku": "16.04-LTS",
-              "version": "latest"
+              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

Ebben a példában használja a `resourceId` függvényt annak a lemezképnek ugyanabban a sablonban létrehozott erőforrások Azonosítójának lekéréséhez. Ha előzetesen létrehozta a felügyelt lemez kép, ehelyett a rendszerkép Azonosítóját kell megadnia. Ezt az Azonosítót kell lennie a következő formában: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`.


## <a name="next-steps"></a>További lépések

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
