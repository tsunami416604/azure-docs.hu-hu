---
title: "Alakítsa át az Azure Resource Manager méretezési készlet használt sablon felügyelt lemezes |} Microsoft Docs"
description: "A méretezési készlet sablon átalakítása felügyelt lemezes méretezési készlet sablont."
keywords: "Virtuálisgép-méretezési csoportok"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: bc8c377a-8c3f-45b8-8b2d-acc2d6d0b1e8
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/18/2017
ms.author: negat
ms.openlocfilehash: 2f5cb85703888c5056611d466f508547ee72e44b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>A méretezési készlet sablon átalakítása felügyelt lemezes méretezési sablon beállítása

A Resource Manager-sablon létrehozásához egy méretezési készlet nem használja a felügyelt lemezes rendelkező ügyfelek kíván felügyelt lemezes módosítja. Ez a cikk bemutatja, hogyan, ne használja, mint például a lekérési kérelmet a [Azure gyors üzembe helyezési sablonokat](https://github.com/Azure/azure-quickstart-templates), egy minta Resource Manager-sablonok közösségi szerkesztésű tárháza. A teljes lekérési kérelmet itt találja: [https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998), és a különbözeti vonatkozó részeinek az alábbi magyarázatokat együtt:

## <a name="making-the-os-disks-managed"></a>Az operációs rendszer lemezeknek elvégzése

Az alábbi különbözeti is látható, hogy több tároló lemez és a fiók tulajdonságainak kapcsolódó változók eltávolítottuk. Tárfióktípus már nincs szükség (Standard_LRS érték az alapértelmezett), de azt továbbra is megadhatja, ha azt kíván. Csak Standard_LRS és Premium_LRS felügyelt lemezes használata támogatott. Új tárolási fiók utótag, egyedi karakterlánc-tömbben és sa száma használták a régi sablonban a tárfiókneveket generálni. Ezek a változók már nincs szükség az új sablonban, mert a felügyelt lemezes automatikusan létrehozza a storage-fiókok az ügyfél nevében. Hasonlóképpen vhd-tároló neve és az operációs rendszer lemezének neve már nincs szükség, mert a felügyelt lemezes automatikusan nevet az alapul szolgáló tárolási blob tárolók és lemezek.

```diff
   "variables": {
-    "storageAccountType": "Standard_LRS",
     "namingInfix": "[toLower(substring(concat(parameters('vmssName'), uniqueString(resourceGroup().id)), 0, 9))]",
     "longNamingInfix": "[toLower(parameters('vmssName'))]",
-    "newStorageAccountSuffix": "[concat(variables('namingInfix'), 'sa')]",
-    "uniqueStringArray": [
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '0')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '1')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '2')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '3')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '4')))]"
-    ],
-    "saCount": "[length(variables('uniqueStringArray'))]",
-    "vhdContainerName": "[concat(variables('namingInfix'), 'vhd')]",
-    "osDiskName": "[concat(variables('namingInfix'), 'osdisk')]",
     "addressPrefix": "10.0.0.0/16",
     "subnetPrefix": "10.0.0.0/24",
     "virtualNetworkName": "[concat(variables('namingInfix'), 'vnet')]",
```


Az alábbi különbözeti is látható, hogy a számítási frissítése api-verzió 2016-04-30-előzetes, amely a méretezési készlet által felügyelt lemezes támogatott legkorábbi szükséges verziója. Vegye figyelembe, hogy sikerült továbbra is használjuk nem felügyelt lemezeket a régi használatával az új api-verzióban igény. Ez azt jelenti Ha a számítási csak frissítjük api-verziót, és ne módosítsa bármi más, a sablon továbbra is működnek, mint korábban.

```diff
@@ -86,7 +74,7 @@
       "version": "latest"
     },
     "imageReference": "[variables('osType')]",
-    "computeApiVersion": "2016-03-30",
+    "computeApiVersion": "2016-04-30-preview",
     "networkApiVersion": "2016-03-30",
     "storageApiVersion": "2015-06-15"
   },
```

Az alábbi különbözeti is látható, hogy megszüntetjük a tárolási fiók erőforrások az erőforrások tömbből teljesen. Már nem kell őket felügyelt lemezes őket automatikusan létrehozza a nevünkben óta.

```diff
@@ -113,19 +101,6 @@
       }
     },
-    {
-      "type": "Microsoft.Storage/storageAccounts",
-      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "[variables('storageApiVersion')]",
-      "copy": {
-        "name": "storageLoop",
-        "count": "[variables('saCount')]"
-      },
-      "properties": {
-        "accountType": "[variables('storageAccountType')]"
-      }
-    },
     {
       "type": "Microsoft.Network/publicIPAddresses",
       "name": "[variables('publicIPAddressName')]",
       "location": "[resourceGroup().location]",
```

Az alábbi különbözeti is látható, hogy megszüntetjük a záradékot a méretezési készletben, amely létrehozta a storage-fiókok a hurok a hivatkozó függ. A régi sablonban ezzel biztosítva, hogy a storage-fiókok létrejöttek-e, mielőtt a méretezési megkezdte létrehozása, de ehhez a záradékhoz már nincs szükség a felügyelt lemezes. Azt is távolítsa el a vhd-tárolók tulajdonság, és az operációs rendszer lemez name tulajdonság szerint ezek a tulajdonságok automatikusan kezeli a technikai részletek a felügyelt lemezes. Ha azt igénylését, azt hozzáadhatja `"managedDisk": { "storageAccountType": "Premium_LRS" }` a "osDisk" konfigurációban, ha a prémium szintű operációsrendszer-lemezek meg akartunk. Csak virtuális gépek nagybetűt vagy kisbetűk meg "a virtuális gép sku premium lemezeket is használhasson.

```diff
@@ -183,7 +158,6 @@
       "location": "[resourceGroup().location]",
       "apiVersion": "[variables('computeApiVersion')]",
       "dependsOn": [
-        "storageLoop",
         "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
         "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
       ],
@@ -200,16 +174,8 @@
         "virtualMachineProfile": {
           "storageProfile": {
             "osDisk": {
-              "vhdContainers": [
-                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"
-              ],
-              "name": "[variables('osDiskName')]",
             },
             "imageReference": "[variables('imageReference')]"
           },

```

Nincs explicit tulajdonság méretezési készlet konfigurációjában felügyelt vagy nem felügyelt lemezt használ-e. A méretezési tudja kell használni a tárolási profilban található tulajdonságok alapján. Ezért fontos annak érdekében, hogy a megfelelő tulajdonságok a tárolási profilban, a méretezési sablon módosításakor.


## <a name="data-disks"></a>Az adatlemezek

A fenti módosítások a méretezési készlet által használt kezelt lemez az operációs rendszer lemezre, de mi a helyzet adatlemezek? Adatok lemezt ad hozzá, vegye fel a "dataDisks" tulajdonság "storageProfile" a "osDisk" ugyanazon a szinten. A tulajdonság értéke JSON objektumok listáját, amelyek mindegyikének Tulajdonságok "logikai" (amely a virtuális gép adatok lemezenként egyedinek kell lennie), "createOption" ("empty" jelenleg az egyetlen támogatott beállítás), és a "diskSizeGB" (gigabájtban; a lemez mérete nagyobbnak kell lennie 0 és kisebb, mint 1024) az alábbi példában látható módon: 

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

Ha megad `n` a tömb lemezek, a rendszer minden virtuális gép beállítása lekérdezi `n` adatlemezek. Vegye figyelembe azonban, hogy ezek az adatlemezek-e a nyers eszközökön. Ezek nem formátumú. Esetén a kell csatolni, paritition, és formázza a lemezeket a használatuk előtt. Másik lehetőségként azt is megadhatja `"managedDisk": { "storageAccountType": "Premium_LRS" }` egyes adatok lemez objektumban adhatja meg, hogy legyen-e a prémium szintű adatlemez. Csak virtuális gépek nagybetűt vagy kisbetűk meg "a virtuális gép sku premium lemezeket is használhasson.

Az adatlemezek méretezési csoportok használatával kapcsolatban további tudnivalókért lásd: [Ez a cikk](./virtual-machine-scale-sets-attached-disks.md).


## <a name="next-steps"></a>Következő lépések
Például Resource Manager-sablonok használatával méretezési csoportok keresendő "vmss" a [Azure gyors üzembe helyezési sablonokat github-tárház](https://github.com/Azure/azure-quickstart-templates).

Általános információkért tekintse meg a [méretezési csoportok fő kezdőlapjának](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

