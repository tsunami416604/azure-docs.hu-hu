---
title: Felügyelt lemezek tárolásának konvertálása a standard és a prémium szintű SSD között
description: Hogyan konvertálhatja az Azure felügyelt lemeztárolóját standardról prémium vagy prémium prémium szintre az Azure CLI használatával.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: cd9bb92b3ed86c3a57b5fc70411a4593335acedb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431507"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Az Azure által felügyelt lemezek tárházának konvertálása standardról prémiumra vagy prémiumra standardra

Az Azure által felügyelt lemezeknek négy lemeztípusa van: Az Azure ultra SSD-k (előzetes verzió), a prémium szintű SSD, a standard SSD és a szabványos HDD. A három GA-lemeztípus (prémium szintű SSD, szabványos SSD és szabványos HDD) között a teljesítményigények alapján válthat. Még nem tud váltani egy ultra SSD-ről, telepítenie kell egy újat.

Ez a funkció nem felügyelt lemezek esetében nem támogatott. A nem felügyelt lemezeket azonban könnyedén [konvertálhatja felügyelt lemezvé,](convert-unmanaged-to-managed-disks.md) hogy képes legyen váltani a lemeztípusok között.

Ez a cikk bemutatja, hogyan konvertálhatja a felügyelt lemezekstandardról prémium vagy prémium standard ra az Azure CLI használatával. Az eszköz telepítéséhez vagy frissítéséhez olvassa el [az Azure CLI telepítése](/cli/azure/install-azure-cli)című témakört.

## <a name="before-you-begin"></a>Előkészületek

* A lemezkonvertálás a virtuális gép (VM) újraindítását igényli, ezért ütemezze a lemeztároló áttelepítését egy már meglévő karbantartási időszak ban.
* Nem felügyelt lemezek esetén először [konvertáljon felügyelt lemezekké,](convert-unmanaged-to-managed-disks.md) hogy válthataanak a tárolási lehetőségek között.


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>A virtuális gép összes felügyelt lemezének váltása a Prémium és a Standard között

Ez a példa bemutatja, hogyan konvertálhatja a virtuális gép összes lemezét standardról prémium szintű tárhelyre vagy prémium szintű tárolóról standard szintű tárolóra. Prémium szintű felügyelt lemezek használatához a virtuális gép kell használnia a [virtuális gép méretét,](sizes.md) amely támogatja a prémium szintű storage. Ez a példa is átvált egy méret, amely támogatja a prémium szintű storage.

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
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Egyéni felügyelt lemezek váltása standard és prémium lemezek között

A fejlesztési és tesztelési számítási feladatokhoz érdemes lehet standard és prémium szintű lemezek vegyesen a költségek csökkentése érdekében. Dönthet úgy, hogy csak azokat a lemezeket frissíti, amelyeknek jobb teljesítményt kell nyújtaniuk. Ez a példa bemutatja, hogyan konvertálhatja egyetlen virtuális gép lemez standard prémium szintű tároló vagy prémium standard storage. Prémium szintű felügyelt lemezek használatához a virtuális gép kell használnia a [virtuális gép méretét,](sizes.md) amely támogatja a prémium szintű storage. Ez a példa is átvált egy méret, amely támogatja a prémium szintű storage.

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

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Felügyelt lemezek váltása szabványos HDD és standard SSD között

Ez a példa bemutatja, hogyan konvertálhatja egyetlen virtuális gép lemez standard HDD standard SSD vagy standard SSD standard HDD konvertálni.

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

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>Felügyelt lemezek váltása standard és prémium verzió között az Azure Portalon

Kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza ki a virtuális gépet a **virtuális gépek**listájából.
3. Ha a virtuális gép nem áll le, válassza **leállítás** a virtuális gép **áttekintése** ablaktábla tetején, és várja meg, amíg a virtuális gép leáll.
4. A virtuális gép ablaktáblájában válassza a menü **Lemezek parancsát.**
5. Jelölje ki a konvertálni kívánt lemezt.
6. Válassza a menü **Konfiguráció** parancsát.
7. Módosítsa a **fiók típusát** **standard HDD-ről** **Prémium SSD-re** vagy **Prémium SSD-ről** **Standard HDD-re.**
8. Válassza a **Mentés**gombot, és zárja be a lemezablaktáblát.

A lemeztípus frissítése azonnali. A virtuális gép újraindítása után.

## <a name="next-steps"></a>További lépések

A virtuális gépek írásvédett másolatának készítése [pillanatképek](snapshot-copy-managed-disk.md)használatával.
