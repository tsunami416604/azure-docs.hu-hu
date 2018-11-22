---
title: Átalakítása az Azure disks storage felügyelt standardról prémium szintre, és fordítva |} A Microsoft Docs
description: Átalakítása az Azure felügyelt lemezes tárhely standardról prémium szintre, és fordítva, Azure CLI-vel.
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
ms.openlocfilehash: 0d777b5dcebfba7dbff7c9ea1f4fedad12b3cf1a
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52283813"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Átalakítása az Azure disks storage felügyelt standardról prémium szintre, és fordítva

A felügyelt lemezek három tárolási lehetőséget kínál az: [prémium szintű SSD](../windows/premium-storage.md), Standard SSD(Preview) és [Standard HDD](../windows/standard-storage.md). Lehetővé teszi a teljesítmény igények alapján minimális állásidővel lehetőségek közötti egyszerű váltást. Nem felügyelt lemezek esetén ez nem támogatott. De könnyen [konvertálás felügyelt lemezekké](convert-unmanaged-to-managed-disks.md) egyszerűen a lemeztípusok közötti váltáshoz.

Ez a cikk bemutatja a felügyelt lemezek konvertálása a standard, prémium szintű, és fordítva az Azure parancssori felület használatával. Ha szeretné telepíteni, vagy frissít a csomagon belül, lásd: [Azure CLI telepítése](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Előkészületek

* Az átalakításhoz újra kell indítani a virtuális gép, ezért ütemezze a lemezek tároló áttelepítése egy már létező karbantartási időszakra. 
* Ha először használja a nem felügyelt lemezek [konvertálás felügyelt lemezekké](convert-unmanaged-to-managed-disks.md) Ez a cikk használatával válthat a tárolási lehetőségek között. 


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>A felügyelt lemezek, a virtuális gépek konvertálása standard prémium szintre, és ez fordítva is igaz

Az alábbi példa bemutatja, hogyan lehet váltani a prémium szintű Storage standard virtuális gép mindegyik lemeze. Prémium szintű felügyelt lemezek használata, a virtuális Gépet kell használnia egy [Virtuálisgép-méret](sizes.md) , amely támogatja a premium storage. Ebben a példában is, amely támogatja a prémium szintű storage vált.

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
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Standard szintű felügyelt lemez konvertálása prémium szintű, és ez fordítva is igaz

A fejlesztési és tesztelési számítási feladatok esetében érdemes a standard és prémium szintű lemezek a költségek csökkentése érdekében keverékét rendelkezik. Ez a premium storage, csak azok a lemezek jobb teljesítményt igénylő frissítésével végezheti el. Az alábbi példa bemutatja, hogyan lehet váltani a prémium szintű Storage, a standard szintű virtuális gépek egyetlen lemezre, és ez fordítva is igaz. Prémium szintű felügyelt lemezek használata, a virtuális Gépet kell használnia egy [Virtuálisgép-méret](sizes.md) , amely támogatja a premium storage. Ebben a példában is, amely támogatja a prémium szintű storage vált.

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

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd-and-vice-versa"></a>Felügyelt lemez konvertálása a standard HDD standard SSD-re és fordítva

Az alábbi példa bemutatja, hogyan egy egyetlen lemezt egy virtuális gép a szabványos HDD standard SSD-re váltani.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Choose between Standard_LRS and StandardSSD_LRS based on your scenario
sku='StandardSSD_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the disk type
az vm deallocate --ids $vmId 

# Update the sku
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="convert-using-the-azure-portal"></a>Konvertálja az Azure portal használatával

Nem felügyelt lemezek is konvertálása felügyelt lemezeket az Azure portal használatával.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A portál virtuális gépek listájából válassza ki a virtuális gép.
3. Válassza ki a virtuális gép paneljén **lemezek** a menüből.
4. Felső részén a **lemezek** panelen válassza ki **áttelepítése a managed Disks szolgáltatásba**.
5. Ha a virtuális gép egy rendelkezésre állási csoportban van, lesz egy figyelmeztetés a a **áttelepítése a managed Disks szolgáltatásba** panel, amelyen a rendelkezésre állási csoportot, először konvertálnia kell. A figyelmeztetés rendelkeznie kell egy hivatkozást is kattinthat a rendelkezésre állási csoport konvertálásához. Miután dátumformátumra alakítja át a rendelkezésre állási csoport, vagy ha a virtuális gép nem egy rendelkezésre állási csoportban, kattintson a **áttelepítése** a lemezek a managed Disks szolgáltatásba való migrálásának a folyamat elindításához. 

A virtuális gép leáll és újraindul a migrálás befejezése után.

## <a name="next-steps"></a>További lépések

Készítsen róla egy írásvédett másolatot a virtuális gépek használatával [pillanatképek](snapshot-copy-managed-disk.md).

