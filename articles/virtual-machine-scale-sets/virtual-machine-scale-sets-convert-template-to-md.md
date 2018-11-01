---
title: Egy Azure Resource Manager méretezési csoport sablonjának használata felügyelt lemez konvertálása |} A Microsoft Docs
description: Egy méretezési csoport sablonjának konvertálása a felügyelt lemezes méretezésicsoport-sablonná.
keywords: virtuálisgép-méretezési csoportok
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: bc8c377a-8c3f-45b8-8b2d-acc2d6d0b1e8
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/18/2017
ms.author: manayar
ms.openlocfilehash: be56fd80229010090216413a7c1833d94e8bac25
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50739566"
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>A méretezési csoport sablonjának konvertálása a felügyelt lemezes méretezésicsoport-sablonná

Előfordulhat, hogy nem használja a felügyelt lemezes méretezési létrehozására szolgáló Resource Manager-sablonnal rendelkező ügyfelek szeretne módosítsa úgy, hogy felügyelt lemez használatához. Ez a cikk bemutatja, hogyan használhatja a felügyelt lemezek, a lekéréses kérelem példaként használva a [Azure gyorsindítási sablonok](https://github.com/Azure/azure-quickstart-templates), a Közösség által készített-adattárat a mintául szolgáló Resource Manager-sablonok. Itt látható a teljes pull-kérelem: [ https://github.com/Azure/azure-quickstart-templates/pull/2998 ](https://github.com/Azure/azure-quickstart-templates/pull/2998), és a különbözeti vonatkozó részeinek az alábbi magyarázatok együtt:

## <a name="making-the-os-disks-managed"></a>Így a felügyelt operációsrendszer-lemezek

A következő különbözeti, a storage-fiók és a lemez tulajdonságainak kapcsolatos számos változók el lesznek távolítva. Tárfiók típusa már nem szükséges (Standard_LRS az alapértelmezett érték), de megadhatja azt, ha szükséges. Felügyelt lemez csak Standard_LRS és Premium_LRS használata támogatott. Új storage-fiókkal utótagja egyedi karakterlánc-tömbben és sa száma is használja a régi sablon olyan tárfiókneveket generálni. Ezeket a változókat nem lesznek az új sablon a szükséges, mert a felügyelt lemezt automatikusan létrehozza a tárfiókokat az ügyfél nevében. Hasonlóképpen vhd-tároló nevének és operációsrendszer-lemez neve nem lesznek szükséges, mert a felügyelt lemezt automatikusan nevet az alapul szolgáló storage-blobtárolók és lemezek.

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


A következő különbözeti, a számítási API frissült a verzió 2016-04-30-preview, ez az a legkorábbi szükséges verzió a felügyelt lemezek támogatásához a méretezési csoportokkal. Az új API-verzióban a régi szintaxissal nem felügyelt lemezek használhatja, ha szükséges. Ha csak a számítási API-verzió frissítése, és nem módosítja a bármi más, a sablon továbbra is működnek, mint korábban.

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

A következő különbözeti, az a tárfiók típusú erőforrást távolítja el az erőforrások tömb teljesen. Az erőforrás már nem szükséges, a felügyelt lemezt automatikusan létrehozza őket.

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

A következő különbözeti, láthatjuk, hogy megszüntetjük a záradékot a méretezési csoportból, hozott létre a storage-fiókok hurok hivatkozó függ. A régi sablonban Ez biztosítja, hogy a storage-fiókok létrejöttek-e, mielőtt a méretezési létrehozása elkezdődött, de ehhez a záradékhoz már nem szükséges felügyelt lemezzel. A vhd-tárolók tulajdonság is törlődnek, és az operációs rendszer lemez név tulajdonság szerint ezek a tulajdonságok automatikusan kezeli a motorháztető alatt felügyelt lemez. Hozzáadhat `"managedDisk": { "storageAccountType": "Premium_LRS" }` a "osDisk" konfigurációban, ha prémium szintű operációsrendszer-lemezek. Csak egy nagybetűt rendelkező virtuális gépek vagy a kisbetűs "a virtuális Gépet a termékváltozat prémium szintű lemez is használható.

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

A méretezésicsoport-konfigurációt a felügyelt vagy nem felügyelt lemez használata nincs explicit tulajdonság szerepel. A méretezési tudja, hogy melyiket kívánja használni, amelyek szerepelnek a tárolóprofil tulajdonsága alapján. Ezért fontos, ha módosítja a sablon annak érdekében, hogy a megfelelő tulajdonságok a tárolási profilban, a méretezési.


## <a name="data-disks"></a>Adatlemezek

A fenti változások a méretezési csoport felügyelt lemezeket használ az operációs rendszer a lemez, de mi a helyzet az adatlemezeket? Hozzáadhat adatlemezeket, adja hozzá a "dataDisks" tulajdonság "storageProfile" a "osDisk" ugyanazon a szinten. A tulajdonság értéke JSON objektumok listáját, tartoznak tulajdonságok "logikai" (amely egy adatlemezt a virtuális gép egyedinek kell lennie), "createOption" ("üres" jelenleg csak a támogatott lehetőség), és a "diskSizeGB" (gigabájtban; a lemez mérete nagyobbnak kell lennie 0 és legfeljebb 1024) az alábbi példában látható módon: 

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

Ha megad `n` a tömbben lévő lemezek a méretezési csoportban lévő mindegyik virtuális gép beállítása lekérdezi `n` adatlemezeket. Vegye figyelembe, azonban, hogy ezek az adatlemezeket nyers eszközökön. Ezek nem formázott. Csatlakoztassa a partíciót, és formázza a lemezeket a használatuk előtt az ügyfél esetén. Igény szerint is megadhatja `"managedDisk": { "storageAccountType": "Premium_LRS" }` az egyes adatobjektum lemez, adja meg, hogy legyen-e a prémium szintű adatlemezt. Csak egy nagybetűt rendelkező virtuális gépek vagy a kisbetűs "a virtuális Gépet a termékváltozat prémium szintű lemez is használható.

Adatlemezek használata méretezési csoportokkal kapcsolatos további információkért lásd: [Ez a cikk](./virtual-machine-scale-sets-attached-disks.md).


## <a name="next-steps"></a>További lépések
Például Resource Manager-sablonok használata a méretezési csoportokat, keressen rá a "vmss" kifejezésre a [Azure gyorsindítási sablonok github-adattárat](https://github.com/Azure/azure-quickstart-templates).

További általános információkért tekintse meg a [kezdőlapja a méretezési csoportok](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

