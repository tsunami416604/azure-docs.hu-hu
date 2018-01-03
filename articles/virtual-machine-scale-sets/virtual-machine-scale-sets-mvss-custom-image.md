---
title: "Egyéni lemezképként az Azure méretezési készlet sablonná hivatkozhat |} Microsoft Docs"
description: "Útmutató egyéni lemezkép hozzáadása egy meglévő Azure virtuálisgép-méretezési csoport sablon"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/10/2017
ms.author: negat
ms.openlocfilehash: 28d2c080048a7f82e83ad9c1794c9757b330a8c7
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2017
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Az Azure méretezési készlet sablont egyéni lemezkép hozzáadása

Ez a cikk bemutatja, hogyan lehet módosítani a [minimális életképes méretezési sablon](./virtual-machine-scale-sets-mvss-start.md) egyéni lemezképről való telepítéséhez.

## <a name="change-the-template-definition"></a>Módosítsa a sablon-definíciót

A minimális életképes méretezési sablon beállítása látható [Itt](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), és a sablon az egyéni lemezkép központi telepítése a skála beállítása látható [Itt](https://raw.githubusercontent.com/gatneil/mvss/custom-image/azuredeploy.json). Ez a sablon létrehozásához használt különbözeti vizsgáljuk meg (`git diff minimum-viable-scale-set custom-image`) adat által adat:

### <a name="creating-a-managed-disk-image"></a>Felügyelt lemezes lemezkép létrehozása

Ha már van egy egyéni felügyelt lemezképet (típusú erőforrást `Microsoft.Compute/images`), akkor kihagyhatja ezt a részt.

Először adja hozzá a `sourceImageVhdUri` paraméter, amely az Azure Storage a telepíteni kívánt az egyéni lemezképet tartalmazó általánosított blob URI.


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

Ezután adja hozzá a típusú erőforrást `Microsoft.Compute/images`, vagyis a felügyelt lemezképét a általánosított blob URI-címen alapuló `sourceImageVhdUri`. Ez a rendszerkép és az azt használó méretezési ugyanabban a régióban kell lennie. A lemezkép tulajdonságait, adja meg az operációs rendszer típusa, a blob helyét (a a `sourceImageVhdUri` paraméter), és a tárfiók típusa:

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

A méretezési készletben erőforrás, adja hozzá a `dependsOn` az egyéni lemezképet győződjön meg arról, hogy a kép hivatkozó záradék előtt erről a képről telepíteni megpróbálja a méretezési jön létre:

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

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Skála módosítása a felügyelt lemezes lemezképen tulajdonságainak beállítása

Az a `imageReference` a skála beállítása `storageProfile`, helyett adja meg a kiadó, kínálnak, sku, és adja meg, a platform-lemezképek verziószámának az `id` , a `Microsoft.Compute/images` erőforrás:

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

Ebben a példában, használja a `resourceId` függvény az erőforrás-azonosítója a kép ugyanazt a sablont létrehozni. Ha előzetesen létrehozta a felügyelt lemezképe, ehelyett kell biztosítania, hogy a kép azonosítója. Ezt az Azonosítót a formátumúnak kell lennie: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`.


## <a name="next-steps"></a>További lépések

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
