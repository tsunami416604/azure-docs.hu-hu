---
title: Windows virtuális gép létrehozása az Azure-ban speciális virtuális merevlemezből |} A Microsoft Docs
description: Új Windows virtuális gép létrehozása specializált felügyelt lemez csatlakoztatásával használatával a Resource Manager-alapú üzemi modellben operációsrendszer-lemezként.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: cynthn
ms.openlocfilehash: 34bfe7733c60337d6ab7d81c498d2fb0fd15e1fd
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338486"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-using-powershell"></a>Windows virtuális gép létrehozása speciális lemezről a PowerShell használatával

Hozzon létre egy új virtuális Gépet az operációsrendszer-lemezként speciális felügyelt lemez csatlakoztatásával. Egy speciális lemez, a virtuális merevlemez (VHD) egy példányát, amely fenntartja a felhasználói fiókok, alkalmazások és más állapot adatait az eredeti virtuális gép meglévő virtuális gépről. 

Amikor egy specializált virtuális merevlemez használatával hozzon létre egy új virtuális Gépet, az új virtuális gép megőrzi az eredeti virtuális gép számítógépnevét. Egyéb az adott számítógépre szóló információ is kell tartani, és néhány esetben ezt az ismétlődő információt hibát okozhat. Vegye figyelembe az adott számítógépre szóló információk milyen típusú az alkalmazások támaszkodnak, amikor a virtuális gép másolása.

Több lehetősége van:
* [Használjon olyan meglévő felügyelt lemezről](#option-1-use-an-existing-disk). Ez akkor hasznos, ha egy virtuális Gépet, amely nem működik megfelelően. Törölheti a virtuális gép ismételt a felügyelt lemez új virtuális gép létrehozásához. 
* [VHD feltöltése](#option-2-upload-a-specialized-vhd) 
* [Pillanatképek használata meglévő Azure virtuális gép másolása](#option-3-copy-an-existing-azure-vm)

Használhatja az Azure Portalon is [új virtuális gép létrehozása speciális virtuális merevlemezből](create-vm-specialized-portal.md).

Ez a témakör bemutatja, hogyan felügyelt lemezek használata. Ha rendelkezik egy örökölt üzembe helyezési igénylő storage-fiók használatával, lásd: [virtuális gép létrehozása a tárfiókban lévő speciális virtuális merevlemezből](sa-create-vm-specialized.md)

## <a name="before-you-begin"></a>Előkészületek
Ha a PowerShell segítségével, győződjön meg, hogy az AzureRM.Compute PowerShell-modul legújabb verzióját. 

```powershell
Install-Module AzureRM -RequiredVersion 6.0.0
```
További információkért lásd: [Azure PowerShell verziószámozás](/powershell/azure/overview).

## <a name="option-1-use-an-existing-disk"></a>1. lehetőség: A meglévő lemez használata

Ha egy virtuális Gépet, amelyet törölt kellett, és hozzon létre egy új virtuális Gépet, használja az operációsrendszer-lemez újbóli szeretne [Get-AzureRmDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermdisk?view=azurermps-6.8.1).

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzureRmDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
Most már csatolhatja ezt a lemezt az operációsrendszer-lemezként egy [új virtuális gép](#create-the-new-vm).

## <a name="option-2-upload-a-specialized-vhd"></a>2. lehetőség: Az egyéni VHD feltöltése

A virtuális Merevlemezt egy specializált virtuális eszközzel létrehozott egy a helyszíni virtualizálási, mint a Hyper-V vagy a virtuális gép exportál egy másik felhőalapú tölthet fel.

### <a name="prepare-the-vm"></a>A virtuális gép előkészítése
Ha szeretne használni, mint a VHD-t hozzon létre egy új virtuális Gépet, győződjön meg arról, a következő lépéseket. 
  
  * [Töltse fel az Azure Windows virtuális merevlemez előkészítése](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Ne** a Sysprep használata virtuális gép általánosítása.
  * Távolítsa el a Vendég virtualizációs eszközök és a virtuális gépen (például a VMware-eszközök) telepített ügynökök.
  * Győződjön meg arról, kérje le az IP-cím és DNS-beállítások a DHCP-n keresztül a virtuális gép van konfigurálva. Ez biztosítja, hogy a kiszolgáló a virtuális hálózaton belül egy IP-címet kap, indításkor. 


### <a name="get-the-storage-account"></a>A storage-fiók létrehozása
Az Azure-ban, a feltöltött virtuális merevlemez tárolásához egy tárfiókra van szükség. Használjon egy meglévő tárfiókot, vagy hozzon létre egy újat. 

A rendelkezésre álló tár fiókokat jeleníti meg, írja be:

```powershell
Get-AzureRmStorageAccount
```

Ha egy meglévő tárfiókot használni kívánt, folytassa a [töltse fel a virtuális merevlemez](#upload-the-vhd-to-your-storage-account) szakaszban.

Ha szeretne hozzon létre egy tárfiókot, kövesse az alábbi lépéseket:

1. Szüksége lesz az erőforráscsoport, amelyben kell létrehozni a tárfiók nevére. Ismerje meg, hogy minden erőforráscsoport az előfizetésben található, írja be:
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Hozzon létre egy erőforráscsoportot a *myResourceGroup* a a *USA nyugati RÉGIÓJA* régió, írja be:

    ```powershell
    New-AzureRmResourceGroup `
       -Name myResourceGroup `
       -Location "West US"
    ```

2. Hozzon létre egy tárfiókot, nevű *mystorageaccount* használatával az erőforráscsoport a [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) parancsmagot:
   
    ```powershell
    New-AzureRmStorageAccount `
       -ResourceGroupName myResourceGroup `
       -Name mystorageaccount `
       -Location "West US" `
       -SkuName "Standard_LRS" `
       -Kind "Storage"
    ```

### <a name="upload-the-vhd-to-your-storage-account"></a>A virtuális lemezek feltöltése a storage-fiókba 
Használja a [Add-AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd) parancsmaggal töltse fel a VHD-t a tárfiókban lévő tárolóba. Ez a példa feltölti a fájlt *myVHD.vhd* a `"C:\Users\Public\Documents\Virtual hard disks\"` tárfiókhoz nevű *mystorageaccount* a a *myResourceGroup* erőforráscsoportot. A fájl tárolva van nevű tárolót *mycontainer* és az új fájl neve lesz *myUploadedVHD.vhd*.

```powershell
$resourceGroupName = "myResourceGroup"
$urlOfUploadedVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $resourceGroupName `
   -Destination $urlOfUploadedVhd `
   -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Sikeres művelet esetén olyan ehhez hasonló választ kap:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

A hálózati kapcsolat és a VHD-fájl méretétől függően ez a parancs is igénybe vehet igénybe

### <a name="create-a-managed-disk-from-the-vhd"></a>Felügyelt lemez létrehozása virtuális merevlemezről

Felügyelt lemez létrehozása speciális virtuális merevlemezből a az a tárfiók tárfiókkulcsait [New-AzureRMDisk](/powershell/module/azurerm.compute/new-azurermdisk). Ez a példa **myOSDisk1** a lemez neve helyezi a lemez *Standard_LRS* tárolási és használja *https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd* , a forrás virtuális merevlemez URI Azonosítóját.

Hozzon létre egy új erőforráscsoportot a új virtuális gép számára.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzureRmResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Az új operációsrendszer-lemez létrehozása a feltöltött virtuális merevlemezből. 

```powershell
$sourceUri = (https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd)
$osDiskName = 'myOsDisk'
$osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk `
    (New-AzureRmDiskConfig -AccountType Standard_LRS  `
    -Location $location -CreateOption Import `
    -SourceUri $sourceUri) `
    -ResourceGroupName $destinationResourceGroup
```

## <a name="option-3-copy-an-existing-azure-vm"></a>3. lehetőség: A meglévő Azure virtuális gép másolása

Létrehozhat egy virtuális Gépet, hogy a használt felügyelt lemezek által a virtuális gép pillanatképének elkészítése, majd a pillanatkép hozzon létre egy új felügyelt lemez és a egy új virtuális Gépet egy példányát.


### <a name="take-a-snapshot-of-the-os-disk"></a>Az operációsrendszer-lemez pillanatfelvételének

Elvégezhető (beleértve az összes lemez) pillanatkép, és a teljes virtuális gép vagy a csak egyetlen lemezről. A következő lépések bemutatják, hogyan csak az operációsrendszer-lemez a virtuális gép a pillanatkép készítése a [New-AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot) parancsmagot. 

Egyes paraméterek megadása 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

A Virtuálisgép-objektum beolvasása.

```powershell
$vm = Get-AzureRmVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
Az operációs rendszer lemezének neve beolvasása.

 ```powershell
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

Hozza létre a pillanatkép-konfigurációt. 

 ```powershell
$snapshotConfig =  New-AzureRmSnapshotConfig `
   -SourceUri $disk.Id `
   -OsType Windows `
   -CreateOption Copy `
   -Location $location 
```

A pillanatkép elkészítésére.

```powershell
$snapShot = New-AzureRmSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


Ha azt tervezi, a pillanatkép segítségével hozzon létre egy virtuális gép, kell lennie a magas hajt végre, használja ezt a paramétert `-AccountType Premium_LRS` a New-AzureRmSnapshot paranccsal. A paraméter a pillanatkép hoz létre, így a prémium szintű felügyelt lemez lesz tárolva. Prémium szintű Managed Disks olyan drágább, mint a Standard. Ezért mindenképpen a paraméter használata előtt mindenképpen szükséges prémium szintű.

### <a name="create-a-new-disk-from-the-snapshot"></a>Hozzon létre egy új lemezt a pillanatképből

Felügyelt lemez létrehozása a pillanatképet a [New-AzureRMDisk](/powershell/module/azurerm.compute/new-azurermdisk). Ez a példa *myOSDisk* a lemez neve.

Hozzon létre egy új erőforráscsoportot a új virtuális gép számára.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzureRmResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Állítsa be az operációs rendszer lemezének neve. 

```powershell
$osDiskName = 'myOsDisk'
```

A felügyelt lemez létrehozása.

```powershell
$osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk `
    (New-AzureRmDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>Az új virtuális gép létrehozása 

Az új virtuális gép által használt hálózati és más Virtuálisgép-erőforrások létrehozásához.

### <a name="create-the-subnet-and-vnet"></a>Az alhálózat és virtuális hálózat létrehozása

Hozza létre a virtuális hálózat és alhálózat, a [virtuális hálózat](../../virtual-network/virtual-networks-overview.md).

Hozza létre az alhálózatot. Ez a példa létrehoz egy nevű alhálózatot **mySubNet**, az erőforráscsoport **myDestinationResourceGroup**, és az alhálózati cím előtag **10.0.0.0/24**.
   
```powershell
$subnetName = 'mySubNet'
$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig `
   -Name $subnetName `
   -AddressPrefix 10.0.0.0/24
```

A virtuális hálózat létrehozása. Ebben a példában a virtuális hálózat nevét, hogy beállítja **myVnetName**, a hely **USA nyugati RÉGIÓJA**, és a címelőtag a virtuális hálózat **10.0.0.0/16**. 
   
```powershell
$vnetName = "myVnetName"
$vnet = New-AzureRmVirtualNetwork `
   -Name $vnetName -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $singleSubnet
```    


### <a name="create-the-network-security-group-and-an-rdp-rule"></a>A hálózati biztonsági csoport és a egy RDP-szabály létrehozása
Az, hogy jelentkezzen be a virtuális gép RDP-vel, szüksége lesz egy biztonsági szabályt, amely lehetővé teszi a 3389-es RDP-hozzáférést. A VHD-t az új virtuális gép egy meglévő specializált virtuális gép alapján készült, mert egy fiókot a forrás virtuális gép RDP-hez is használhatja.

Ebben a példában állítja be az NSG neve **myNsg** és az RDP-szabály neve, **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -Name $nsgName -SecurityRules $rdpRule
    
```

További információ a végpontok és NSG-szabályok: [egy virtuális gép portjainak megnyitása az Azure PowerShell-lel](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Hozzon létre egy nyilvános IP-cím és a hálózati adapter
A virtuális hálózaton a virtuális géppel való kommunikáció biztosításához egy [nyilvános IP-cím](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) és egy hálózati adapter szükséges.

A nyilvános IP-cím létrehozása. Ebben a példában a nyilvános IP-cím neve van beállítva, **myIP**.
   
```powershell
$ipName = "myIP"
$pip = New-AzureRmPublicIpAddress `
   -Name $ipName -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -AllocationMethod Dynamic
```       

Hozzon létre a hálózati adaptert. Ebben a példában a hálózati adapter neve van beállítva, **myNicName**.
   
```powershell
$nicName = "myNicName"
$nic = New-AzureRmNetworkInterface -Name $nicName `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location -SubnetId $vnet.Subnets[0].Id `
   -PublicIpAddressId $pip.Id `
   -NetworkSecurityGroupId $nsg.Id
```



### <a name="set-the-vm-name-and-size"></a>A virtuális gép neve és mérete

Ebben a példában a virtuális gép nevének beállítása *myVM* és a virtuális gép méretét *Standard_A2*.

```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>A hálózati adapter hozzáadása
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>Az operációsrendszer-lemez hozzáadása 

Az operációsrendszer-lemez hozzáadása a konfigurációs történő [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk). Ebben a példában a lemez méretének megadása *128 GB-os* , és csatolja a felügyelt lemez egy *Windows* operációsrendszer-lemez.
 
```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>Végezze el a virtuális gép 

Hozzon létre a virtuális gépet [New-AzureRMVM](/powershell/module/azurerm.compute/new-azurermvm)az imént létrehozott konfigurációk.

```powershell
New-AzureRmVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Ha ez a parancs sikeres volt, akkor ehhez hasonló kimenet jelenik meg:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Győződjön meg arról, hogy a virtuális gép létrejött-e.
Megjelenik az újonnan létrehozott virtuális gép vagy a a [az Azure portal](https://portal.azure.com)alatt **Tallózás** > **virtuális gépek**, vagy a következő PowerShell-parancsok használatával:

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>További lépések
Jelentkezzen be az új virtuális gépet. További információkért lásd: [csatlakoztatása és bejelentkezés Windows rendszert futtató Azure virtuális gép](connect-logon.md).

