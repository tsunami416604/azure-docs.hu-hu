---
title: Különbségek és szempontok a Managed Disks az Azure Stackben |} A Microsoft Docs
description: Különbségek és szempontok ismerje meg az Azure stack felügyelt lemezek használatakor.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: ade8ce1cc872c4d954d272f79ef9a3113ef0f683
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820076"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Az Azure Stack felügyelt lemezek: különbségek és szempontok

Ez a cikk összefoglalja, ismert [Azure Stack-Managed Disks](azure-stack-manage-vm-disks.md) és [az Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md). Azure Stack és az Azure magas szintű különbségeit kapcsolatos további információkért tekintse meg a [szempontok kulcs](azure-stack-considerations.md) cikk.

A felügyelt lemezek leegyszerűsítik a Lemezkezelés IaaS virtuális gépekhez, mivel kezeli az [tárfiókok](../azure-stack-manage-storage-accounts.md) társított Virtuálisgép-lemezek.

> [!Note]  
> Felügyelt lemezek az Azure Stacken 1808 frissítés érhető el. Ez alapértelmezés szerint engedélyezve van az Azure Stack portálon 1811 Update használó virtuális gépek létrehozásakor.
  
## <a name="cheat-sheet-managed-disk-differences"></a>Hasznos tanácsok: Felügyelt lemez különbségek

| Szolgáltatás | Azure (globális) | Azure Stack |
| --- | --- | --- |
|Az inaktív adatok titkosítása |Azure Storage Service Encryption (SSE), Azure Disk Encryption (ADE)     |A BitLocker 128 bites AES-titkosítás      |
|Kép          | Felügyelt egyéni lemezképek támogatása |Támogatott|
|Biztonsági mentés beállításai |Az Azure Backup szolgáltatás támogatja |Még nem támogatott |
|Vész-helyreállítási lehetőségeket |Az Azure Site Recovery támogatja |Még nem támogatott|
|Lemeztípusok     |Prémium szintű SSD, a standard szintű SSD (előzetes verzió) és a Standard HDD |Prémium szintű SSD, Standard HDD |
|Prémium szintű lemezek  |Teljes mértékben támogatott. |Bővítheti, de nincs teljesítményszint vagy garancia  |
|Prémium szintű lemez iops-érték  |Függ a lemez mérete  |2300 iops-érték lemezenként |
|Prémium szintű lemezek átviteli sebesség |Függ a lemez mérete |Lemezenként 145 MB/s |
|Lemezméret  |Azure Premium Disk: P4 (32 GiB) való P80 (32 TiB)<br>Azure Standard SSD Disk: E10 (128 GiB) való E80 (32 TiB)<br>Az Azure standard szintű HDD lemez: S4 (32 GiB) való S80 (32 TiB) |M4: 32 GiB<br>M6: 64 GiB<br>M10: 128 GiB<br>M15: 256 GiB<br>M20: 512 GiB<br>M30: 1024 GiB |
|Lemezek pillanatkép-másolás|Pillanatkép készítése az Azure felügyelt támogatott futó virtuális Géphez csatolt lemezek|Még nem támogatott |
|Lemezek teljesítményét elemző |Összesített mérőszámok és a lemezenkénti metrikák támogatott |Még nem támogatott |
|Migrálás      |Adja meg az eszköz meglévő nem felügyelt Azure Resource Manager virtuális gépek nélkül hozza létre újra a virtuális gép áttelepítése  |Még nem támogatott |

> [!NOTE]  
> Felügyelt lemez IOPs és átviteli sebesség az Azure Stackben értéke helyett egy kiépített számot, amelyet az üzemállapotára negatív hatással a hardver- és az Azure Stackben futó számítási feladatok maximális szám.

## <a name="metrics"></a>Mérőszámok

A storage-mérőszámok különbségek is vannak:

- Az Azure Stack a tranzakciós adatokat a storage-mérőszámok nem tesznek különbséget a belső vagy külső hálózati sávszélességet.
- A storage-mérőszámok az Azure Stack tranzakciós adatokat nem tartalmazza a virtuális gép hozzá a csatlakoztatott lemezeket.

## <a name="api-versions"></a>API-verziók

Az Azure Stack felügyelt lemezek a következő API-verziókat támogatja:

- 2017-03-30
- 2017-12-01

## <a name="convert-to-managed-disks"></a>Konvertálása felügyelt lemezekké

Használhatja az alábbi szkriptet a saját értékeire a jelenleg üzembe helyezett virtuális gép konvertálás nem felügyeltről felügyelt lemezzé

```powershell
$subscriptionId = 'subid'

#Provide the name of your resource group
$resourceGroupName ='rgmgd'

#Provide the name of the Managed Disk
$diskName = 'unmgdvm'

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '50'

#Provide the URI of the VHD file that will be used to create Managed Disk. 
# VHD file can be deleted as soon as Managed Disk is created.
$vhdUri = 'https://rgmgddisks347.blob.local.azurestack.external/vhds/unmgdvm20181109013817.vhd' 

#Provide the storage type for the Managed Disk. PremiumLRS or StandardLRS.
$accountType = 'StandardLRS'

#Provide the Azure Stack location where Managed Disk will be located. 
#The location should be same as the location of the storage account where VHD file is stored.

$location = 'local'
$virtualMachineName = 'mgdvm'
$virtualMachineSize = 'Standard_D1'
$pipname = 'unmgdvm-ip'
$virtualNetworkName = 'rgmgd-vnet'
$nicname = 'unmgdvm295'

#Set the context to the subscription Id where Managed Disk will be created
Select-AzureRmSubscription -SubscriptionId $SubscriptionId

$diskConfig = New-AzureRmDiskConfig -AccountType $accountType  -Location $location -DiskSizeGB $diskSize -SourceUri $vhdUri -CreateOption Import

#Create Managed disk
New-AzureRmDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
$disk = get-azurermdisk -DiskName $diskName -ResourceGroupName $resourceGroupName
$VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize

#Use the Managed Disk Resource Id to attach it to the virtual machine. Please change the OS type to linux if OS disk has linux OS
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $disk.Id -CreateOption Attach -Linux

#Create a public IP for the VM
$publicIp = Get-AzureRmPublicIpAddress -Name $pipname -ResourceGroupName $resourceGroupName 

#Get the virtual network where virtual machine will be hosted
$vnet = Get-AzureRmVirtualNetwork -Name $virtualNetworkName -ResourceGroupName $resourceGroupName

# Create NIC in the first subnet of the virtual network
$nic = Get-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $resourceGroupName

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nic.Id

#Create the virtual machine with Managed Disk
New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $resourceGroupName -Location $location
```

## <a name="managed-images"></a>Felügyelt képek

Az Azure Stack támogatja *lemezképek kezelt*, amelyek lehetővé teszik, hogy hozzon létre egy felügyelt rendszerképet objektumot egy általánosított virtuális gépen (mindkettő nem felügyelt és felügyelt), amely csak hozhat létre felügyelt lemez a virtuális gépek a jövőben. Felügyelt lemezképek a következő két esetben engedélyezése:

- Általánosítva van a nem felügyelt virtuális gépek és a jövőben felügyelt lemezeket használni szeretne.
- Felügyelt általánosított virtuális gép rendelkezik, és szeretne létrehozni a több, hasonló felügyelt virtuális gépeket.

### <a name="migrate-unmanaged-vms-to-managed-disks"></a>Nem felügyelt virtuális gépek migrálása felügyelt lemezekre

Kövesse az utasításokat [Itt](../../virtual-machines/windows/capture-image-resource.md#create-an-image-from-a-vhd-in-a-storage-account) , létrehozhat egy felügyelt rendszerképet, a storage-fiókban általános virtuális merevlemezből. Ez a rendszerkép a jövőben felügyelt virtuális gépek létrehozásához használható.

### <a name="create-managed-image-from-vm"></a>Felügyelt rendszerkép létrehozása virtuális gépről

Után lemezkép létrehozása a meglévő felügyelt lemez a virtuális gép használata a parancsfájl [Itt](../../virtual-machines/windows/capture-image-resource.md#create-an-image-from-a-managed-disk-using-powershell) , az alábbi példa parancsfájl hasonló Linux virtuális Gépet hoz létre egy meglévő kép objektum:

```powershell
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "redmond"
$vmName = "myVM"
$imagerg = "managedlinuxrg"
$imagename = "simplelinuxvmm-image-2019122"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzureRmResourceGroup -Name $resourceGroup -Location $location

# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

$image = get-azurermimage -ResourceGroupName $imagerg -ImageName $imagename
# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred | `
Set-AzureRmVMSourceImage -Id $image.Id | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

További információkért tekintse meg az Azure által felügyelt rendszerkép cikkek [általánosított virtuális gép felügyelt rendszerképének létrehozása az Azure-ban](../../virtual-machines/windows/capture-image-resource.md) és [virtuális gép létrehozása felügyelt rendszerképből](../../virtual-machines/windows/create-vm-generalized-managed.md).

## <a name="configuration"></a>Konfiguráció

A 1808 alkalmazása után a frissítés vagy újabb, felügyelt lemezek használata előtt a következő konfigurációt kell végrehajtania:

- Ha egy előfizetést a 1808 korábban lett létrehozva, kövesse az alábbi lépések végrehajtásával frissítheti az előfizetést. Ellenkező esetben az előfizetésben található virtuális gépek üzembe helyezéséhez meghiúsulhat egy hibaüzenettel "Belső hiba történt a Lemezkezelőben."
   1. A bérlői portálon lépjen a **előfizetések** , és keresse meg az előfizetés. Kattintson a **erőforrás-szolgáltatók**, majd kattintson az **Microsoft.Compute**, és kattintson a **újraregisztrálni**.
   2. Lépjen az azonos előfizetéshez tartozó **hozzáférés-vezérlés (IAM)**, és ellenőrizze, hogy **Azure Stack – felügyelt lemez** szerepel a listán.
- Ha egy több-bérlős környezetet használ, kérje meg a felhő üzemeltetője (arról, hogy a saját cég vagy a szolgáltató), konfigurálja újra a Vendég címtárak a lépések mindegyike [Ez a cikk](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory). Ellenkező esetben az előfizetéshez tartozó Vendég könyvtárban a virtuális gépek üzembe helyezéséhez meghiúsulhat egy hibaüzenettel "Belső hiba történt a Lemezkezelőben."

## <a name="next-steps"></a>További lépések

- [Azure Stack virtual machines bemutatása](azure-stack-compute-overview.md)
