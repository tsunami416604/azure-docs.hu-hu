---
title: Méretezési csoport sablonjának konvertálása felügyelt lemez használatához
description: Alakítson ki egy Azure Resource Manager virtuálisgép-méretezési csoportsablont felügyelt lemezméretezési készletsablonná.
keywords: virtuálisgép-méretezési csoportok
author: mayanknayar
tags: azure-resource-manager
ms.assetid: bc8c377a-8c3f-45b8-8b2d-acc2d6d0b1e8
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 5/18/2017
ms.author: manayar
ms.openlocfilehash: 4ab5c48c6673a2353c70fe808d09aa15675e0424
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278128"
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>Méretezési csoport sablonjának konvertálása felügyelt lemezméretezési készletsablonná

Előfordulhat, hogy a felügyelt lemezt nem használó méretezési csoport létrehozásához erőforrás-kezelő sablonnal rendelkező ügyfelek módosítani szeretnék azt a felügyelt lemez használatára. Ez a cikk bemutatja, hogyan használható a felügyelt lemezek, példaként egy lekéréses kérelmet az [Azure gyorsindítási sablonok,](https://github.com/Azure/azure-quickstart-templates)a közösség által vezérelt tártára minta Resource Manager-sablonok. A teljes lekéréses kérelem [https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998)itt látható: . és a diff megfelelő részei az alábbiakban találhatók, magyarázatokkal együtt:

## <a name="making-the-os-disks-managed"></a>Az operációs rendszer lemezeinek kezelése

A következő diff, több, a tárfiók és a lemez tulajdonságaihoz kapcsolódó változók törlődnek. A tárfiók típusa már nem szükséges (Standard_LRS az alapértelmezett), de szükség esetén megadhatja. A felügyelt lemez csak Standard_LRS és Premium_LRS támogatott. Új tárfiók-utótagot, egyedi karakterlánctömböt és saszámot használtak a régi sablonban a tárfióknevek létrehozásához. Ezekre a változókra már nincs szükség az új sablonban, mert a felügyelt lemez automatikusan létrehozza a tárfiókokat az ügyfél nevében. Hasonlóképpen a vhd-tároló neve és az operációs rendszer lemezének neve már nem szükséges, mert a felügyelt lemez automatikusan elnevezi az alapul szolgáló tárolóblob-tárolókat és lemezeket.

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


A következő diff, számítási API-t frissítve 2016-04-30-preview verzióra, amely a legkorábbi szükséges verzió a felügyelt lemez támogatás méretezési készletek. Használhatja a nem felügyelt lemezeket az új API-verzió a régi szintaxis, ha szükséges. Ha csak frissíti a számítási API-verziót, és semmi mást nem módosít, a sablonnak továbbra is ugyanúgy kell működnie, mint korábban.

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

A következő diff, a tárfiók erőforrás törlődik az erőforrástömb ből teljesen. Az erőforrásra már nincs szükség, mivel a felügyelt lemez automatikusan létrehozza őket.

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

A következő diff, láthatjuk, hogy eltávolítja a függ a függők a skálázási készletről a készlet, amely a tárfiókok létrehozása. A régi sablonban ez biztosította, hogy a tárfiókok a méretezési csoport létrehozása előtt jöttek létre, de ez a záradék már nincs szükség a felügyelt lemez. A vhd tárolók tulajdonság is törlődik, valamint az operációs rendszer lemeznév tulajdonság, mivel ezek a tulajdonságok automatikusan kezeli a motorháztető alatt felügyelt lemez. Felveheti `"managedDisk": { "storageAccountType": "Premium_LRS" }` az "osDisk" konfiguráció, ha azt szeretné, prémium operációs rendszer lemezek. Csak a virtuális gép termékváltozatában kis vagy kis "s" betűvel rendelkező virtuális gépek használhatnak prémium szintű lemezeket.

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

A méretezési csoport konfigurációjában nincs explicit tulajdonság a felügyelt vagy nem felügyelt lemez használatára. A méretezési készlet tudja, hogy melyiket kell használni a tárolási profilban lévő tulajdonságok alapján. Ezért fontos, hogy a sablon módosításakor győződjön meg arról, hogy a megfelelő tulajdonságok a méretezési csoport tárolási profiljában vannak.


## <a name="data-disks"></a>Adatlemezek

A fenti módosításokkal a méretezési csoport felügyelt lemezeket használ az operációs rendszer lemezéhez, de mi a helyzet az adatlemezekkel? Adatlemezek hozzáadásához adja hozzá a "dataDisks" tulajdonságot a "storageProfile" alatt az "osDisk" szinttel azonos szinten. A tulajdonság értéke egy JSON objektumlista, amelynek tulajdonságai "lun" (amelynek egyedinek kell lennie adatlemezenként a virtuális gépen), "createOption" ("üres" jelenleg az egyetlen támogatott lehetőség), és a "diskSizeGB" (a lemez mérete gigabájtban; nagyobbnak kell lennie, mint a lemez mérete gigabájtban; nagyobbnak kell lennie, mint 0 és kevesebb, mint 1024), mint a következő példában:

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

Ha lemezeket ad meg `n` ebben a tömbben, `n` a méretezési csoport minden virtuális gépe adatlemezeket kap. Vegye figyelembe azonban, hogy ezek az adatlemezek nyers eszközök. Ezek nincsenek formázva. Használat előtt az ügyfél feladata a lemezek csatolása, particionálása és formázása. Minden adatlemez-objektumban `"managedDisk": { "storageAccountType": "Premium_LRS" }` megadhatja azt is, hogy prémium szintű adatlemez legyen. Csak a virtuális gép termékváltozatában kis vagy kis "s" betűvel rendelkező virtuális gépek használhatnak prémium szintű lemezeket.

Ha többet szeretne tudni arról, hogy miként lehet adatlemezeket méretezési csoportokkal használni, olvassa el [ezt a cikket.](./virtual-machine-scale-sets-attached-disks.md)


## <a name="next-steps"></a>További lépések
Például a Resource Manager-sablonok méretezési csoportok használatával keresse meg a "vmss" kifejezést az [Azure Quickstart Templates GitHub-tárházban.](https://github.com/Azure/azure-quickstart-templates)

Általános információkért tekintse meg a [fő céloldalt a méretezési csoportokhoz.](https://azure.microsoft.com/services/virtual-machine-scale-sets/)

