---
title: "Alakítsa át az Azure szabványos lemezek tárolási kezelik, prémium, és ez fordítva is igaz |} Microsoft Docs"
description: "Azure átalakítása felügyelt lemezek tárolási standard prémium, és ez fordítva is igaz, az Azure parancssori felület használatával."
services: virtual-machines-linux
documentationcenter: 
author: ramankum
manager: kavithag
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: ramankum
ms.openlocfilehash: c22c2c194cb839c3ec9e3e851768ca19bc6fc443
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2017
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Alakítsa át az Azure szabványos lemezek tárolási kezelik, prémium, és ez fordítva is igaz

Kezelt lemezek két tárolási lehetőségeket kínál: [prémium](../windows/premium-storage.md) (SSD-alapú) és [szabványos](../windows/standard-storage.md) (HDD-alapú). Ez lehetővé teszi, hogy egyszerűen válthat a két lehetőség közül választhat a teljesítmény igények alapján minimális állásidővel között. Ez a funkció nem felügyelt lemezek nem érhető el. Azonban úgy is könnyen [alakítsa át a felügyelt](convert-unmanaged-to-managed-disks.md) egyszerűen a két beállítás közötti váltáshoz.

Ez a cikk bemutatja, hogyan felügyelt lemezek konvertálása standard, premium, és ez fordítva is igaz az Azure parancssori felület használatával. Ha szeretné telepíteni vagy frissíteni, lásd: [Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli.md). 

## <a name="before-you-begin"></a>Előkészületek

* Az átalakítás újra kell indítani a virtuális gép, úgy ütemezni a áttelepítését a lemezek már meglévő karbantartási időszak alatt történjen. 
* Használatakor a nem felügyelt lemezek, először [alakítsa át a felügyelt](convert-unmanaged-to-managed-disks.md) használatára Ez a cikk a két tárolási lehetőségek közötti váltáshoz. 


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>Alakítsa át a virtuális gépek összes felügyelt lemezeket szabványos prémium szintű, és ez fordítva is igaz

A következő példában megmutatjuk, hogyan lehet váltani a prémium szintű Storage-standard a virtuális gép összes lemeze. Felügyelt premium lemezek, a virtuális Gépet kell használnia egy [Virtuálisgép-méretet](sizes.md) , amely támogatja a prémium szintű storage. Ebben a példában is, amely támogatja a prémium szintű storage méretre vált.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the sku of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the sku of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Standard felügyelt lemezes átalakítása prémium, és ez fordítva is igaz

A fejlesztési és tesztelési célú munkaterheléshez érdemes lehet a költségek csökkentése a standard és premium lemezek keverékével van. Prémium szintű Storage, frissítse a lemezek csak az jobb teljesítményt igénylő végrehajtható. A következő példában megmutatjuk, hogyan lehet váltani egyetlen lemezt a virtuális gépek a szabványos prémium szintű Storage, és ez fordítva is igaz. Felügyelt premium lemezek, a virtuális Gépet kell használnia egy [Virtuálisgép-méretet](sizes.md) , amely támogatja a prémium szintű storage. Ebben a példában is, amely támogatja a prémium szintű storage méretre vált.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --ids $vmId --size $size

# Update the sku
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="next-steps"></a>Következő lépések

Tegye meg a virtuális gépek csak olvasható másolatát a használatával [pillanatképek](snapshot-copy-managed-disk.md).

