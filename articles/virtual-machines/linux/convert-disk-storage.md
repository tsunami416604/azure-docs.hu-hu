---
title: Azure konvertálása felügyelt lemezes tárhely standardról prémium vagy standard |} A Microsoft Docs
description: Azure konvertálása felügyelt lemezes tárhely standardról prémium vagy standard az Azure CLI-vel.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 18730f662f36000e1efc826c35bebde79dbf0e79
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58013610"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Azure konvertálása felügyelt lemezes tárhely standardról prémium vagy standard

Négy [lemeztípus](disks-types.md) az Azure-ban felügyelt lemezek: Ultranagy az Azure Disk Storage, a prémium szintű SSD, a Standard SSD és a Standard HDD. Prémium szintű SSD, a Standard SSD és a standard szintű HDD, minimális állásidővel teljesítményét szükség szerint között könnyen válthat. Ez a funkció nem támogatott a nem felügyelt lemezek vagy Ultranagy lemezes tárolás. De könnyen [felügyelt lemezek konvertálása felügyelt](convert-unmanaged-to-managed-disks.md) tudni váltani a lemeztípusok.

Ez a cikk bemutatja, hogyan átalakítása felügyelt lemezek standardról prémium vagy standard az Azure parancssori felület használatával. Telepítse vagy frissítse az eszközt, tekintse meg [Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Előkészületek

* Lemez konvertálása újra kell indítani a virtuális gép (VM), így megfelelően időzítse a lemezes tárolás áttelepítése során egy már létező karbantartási időszakra.
* Nem felügyelt lemezek első [konvertálás felügyelt lemezekké](convert-unmanaged-to-managed-disks.md) úgy válthat a tárolási lehetőségek között.


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>A virtuális gépek között a prémium és standard szintű minden felügyelt lemezek

Ez a példa bemutatja, hogyan alakítják át az összes olyan Virtuálisgép-lemezek a Standard, prémium szintű Storage, illetve prémiumról standard szintű tárolóba. Prémium szintű felügyelt lemezek használata, a virtuális Gépet kell használnia egy [Virtuálisgép-méret](sizes.md) , amely támogatja a Premium storage. Ebben a példában is, amely támogatja a Premium storage vált.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the SKU of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the SKU of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Váltson a Standard és prémium szintű között az egyes felügyelt lemezek

A fejlesztési és tesztelési számítási feladatok esetében érdemes utasításkészletet, Standard és prémium szintű lemezek a költségek csökkentése érdekében. Ha szeretné, csak ezeket a lemezeket, amelyeket jobb teljesítményt kell frissíteni. Ez a példa bemutatja, hogyan egyetlen Virtuálisgép-lemez konvertálása a Standard, prémium szintű Storage, illetve prémiumról standard szintű tárolóba. Prémium szintű felügyelt lemezek használata, a virtuális Gépet kell használnia egy [Virtuálisgép-méret](sizes.md) , amely támogatja a Premium storage. Ebben a példában is, amely támogatja a Premium storage vált.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --ids $vmId --size $size

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Felügyelt lemezek közötti Standard HDD és Standard SSD

Ez a példa bemutatja, hogyan egyetlen Virtuálisgép-lemez konvertálása a Standard HDD Standard SSD-re, illetve a standard szintű HDD Standard SSD.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Choose between Standard_LRS and StandardSSD_LRS based on your scenario
sku='StandardSSD_LRS'

#Get the parent VM ID 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the disk type
az vm deallocate --ids $vmId 

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>Az Azure Portalon között a Standard és prémium szintű felügyelt lemezek

Kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a virtuális Gépet a listából **virtuális gépek**.
3. Ha a virtuális gép nem fut, válassza ki a **leállítása** felső részén a virtuális gép **áttekintése** panelre, és várja meg a virtuális gép leállításához.
4. A virtuális gép panelén válassza **lemezek** a menüből.
5. Válassza ki az átalakítani kívánt lemezt.
6. Válassza ki **konfigurációs** a menüből.
7. Változás a **fiók típusa** a **Standard HDD** való **prémium szintű SSD** vagy **prémium szintű SSD** való **Standard HDD**.
8. Válassza ki **mentése**, és zárja be a lemez panelt.

A lemez típusát, a frissítés akkor azonnali. Az átalakítás után újraindíthatja a virtuális gép.

## <a name="next-steps"></a>További lépések

Győződjön meg arról, egy virtuális Gépet egy csak olvasható példányát használatával [pillanatképek](snapshot-copy-managed-disk.md).
