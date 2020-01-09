---
title: Felügyelt lemezek tárterületének konvertálása a standard és a prémium szintű SSD között
description: Az Azure Managed Disks Storage standardról prémium vagy prémium szintre történő konvertálása az Azure CLI használatával.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: cd9bb92b3ed86c3a57b5fc70411a4593335acedb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431507"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Azure Managed Disks Storage konvertálása standard csomagról prémium vagy prémium szintre

Az Azure Managed Disks négyféle lemezből áll: az Azure Ultra SSD (előzetes verzió), a prémium SSD, a standard SSD és a standard HDD. A teljesítmény igényei alapján válthat a három GA-lemez típusa (prémium SSD, standard SSD és standard HDD) között. Még nem tud váltani egy ultra SSD-ről, és telepítenie kell egy újat.

A nem felügyelt lemezek esetében ez a funkció nem támogatott. A nem [felügyelt lemezeket azonban könnyedén átalakíthatja egy felügyelt lemezre](convert-unmanaged-to-managed-disks.md) , hogy képes legyen váltani a lemezek típusai között.

Ez a cikk bemutatja, hogyan alakíthatja át a felügyelt lemezeket standardról prémium vagy prémium szintűre az Azure CLI használatával. Az eszköz telepítéséhez vagy frissítéséhez tekintse meg az [Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört.

## <a name="before-you-begin"></a>Előzetes teendők

* A lemez átalakításához újra kell indítani a virtuális gépet (VM), ezért a lemezes tárolás áttelepítését egy már létező karbantartási időszakban kell ütemezni.
* A nem felügyelt lemezek esetében először [alakítsa át a felügyelt lemezeket](convert-unmanaged-to-managed-disks.md) , hogy át lehessen váltani a tárolási lehetőségek között.


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Egy virtuális gép összes felügyelt lemezének átváltása a prémium és a standard szint között

Ebből a példából megtudhatja, hogyan alakíthatja át a virtuális gép összes lemezét a standard és a Premium Storage-ból, illetve prémiumról standard szintű tárterületre. A prémium szintű felügyelt lemezek használatához a virtuális gépnek olyan virtuálisgép- [méretet](sizes.md) kell használnia, amely támogatja a Premium Storage-ot. Ez a példa a Premium Storage-t támogató méretre is vált.

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
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Egyéni felügyelt lemezek váltása a standard és a prémium között

A fejlesztési és tesztelési feladatok esetében érdemes lehet a standard és a prémium szintű lemezek kombinációját használni a költségek csökkentése érdekében. Dönthet úgy is, hogy csak a jobb teljesítményt igénylő lemezeket frissíti. Ebből a példából megtudhatja, hogyan alakíthat át egyetlen virtuálisgép-lemezt a standard csomagból a Premium Storage-ba vagy a prémiumról standard Storage-ba A prémium szintű felügyelt lemezek használatához a virtuális gépnek olyan virtuálisgép- [méretet](sizes.md) kell használnia, amely támogatja a Premium Storage-ot. Ez a példa a Premium Storage-t támogató méretre is vált.

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

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Felügyelt lemezek váltása standard HDD és standard SSD között

Ebből a példából megtudhatja, hogyan alakíthat át egyetlen VM-lemezt standard HDDról standard SSD vagy standard SSDról standard HDDra.

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

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>Felügyelt lemezek váltása a standard és a prémium között Azure Portal

Kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Válassza ki a virtuális gépet a **virtuális gépek**listájából.
3. Ha a virtuális gép nem áll le, válassza a **Leállítás** elemet a virtuális gép **Áttekintés** paneljének tetején, és várjon, amíg a virtuális gép leáll.
4. A virtuális gép ablaktábláján válassza a menü **lemezek** elemét.
5. Válassza ki az átalakítani kívánt lemezt.
6. Válassza a **konfiguráció** lehetőséget a menüből.
7. Módosítsa a **fióktípus** **standard HDDról** **prémium SSDre** vagy **prémium SSDról** **standard HDDra**.
8. Válassza a **Mentés**lehetőséget, majd a lemez ablaktábla bezárásához.

A lemez típusának frissítése azonnali. Az átalakítás után újraindíthatja a virtuális gépet.

## <a name="next-steps"></a>Következő lépések

[Pillanatképek](snapshot-copy-managed-disk.md)használatával készítse el a virtuális gép írásvédett példányát.
