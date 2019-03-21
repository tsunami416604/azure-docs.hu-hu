---
title: Windows virtuális gép létrehozása az Azure-ban speciális virtuális merevlemezből |} A Microsoft Docs
description: Új Windows virtuális gép létrehozása specializált felügyelt lemez csatlakoztatásával operációsrendszer-lemezként a Resource Manager-alapú üzemi modell használatával.
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
ms.date: 10/10/2018
ms.author: cynthn
ms.openlocfilehash: 364dca8d7cab3698e501e74d0c2817aac1a36f88
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58137977"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-by-using-powershell"></a>Windows virtuális gép létrehozása speciális lemezről a PowerShell használatával

Hozzon létre egy új virtuális Gépet az operációsrendszer-lemezként speciális felügyelt lemez csatlakoztatásával. Egy speciális lemez, egy virtuális merevlemezt (VHD) egy példányát, amely tartalmazza a felhasználói fiókok, alkalmazások és más állapot adatait az eredeti virtuális gép meglévő virtuális gépről. 

Amikor egy specializált virtuális merevlemez használatával hozzon létre egy új virtuális Gépet, az új virtuális gép megőrzi az eredeti virtuális gép számítógépnevét. Egyéb az adott számítógépre szóló információ is marad, és néhány esetben ezt az ismétlődő információt hibát okozhat. Ha egy virtuális Gépet másol, vegye figyelembe milyen számítógép-specifikus információkat az alkalmazások támaszkodnak.

Több lehetősége van:
* [Használjon olyan meglévő felügyelt lemezről](#option-1-use-an-existing-disk). Ez a beállítás akkor hasznos, ha egy virtuális Gépet, amely nem működik megfelelően. Törölje a virtuális Gépet, és a felügyelt lemez új virtuális gép létrehozásához használja fel újra. 
* [VHD feltöltése](#option-2-upload-a-specialized-vhd) 
* [Egy meglévő Azure virtuális gép másolása pillanatképek használatával](#option-3-copy-an-existing-azure-vm)

Használhatja az Azure Portalon is [új virtuális gép létrehozása speciális virtuális merevlemezből](create-vm-specialized-portal.md).

Ez a cikk bemutatja, hogyan felügyelt lemezek használata. Ha rendelkezik egy örökölt üzembe helyezési igénylő storage-fiók használatával, lásd: [virtuális gép létrehozása speciális virtuális merevlemezből egy storage-fiókban lévő](sa-create-vm-specialized.md).

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="option-1-use-an-existing-disk"></a>Option 1: Meglévő lemez használata

Ha egy virtuális Gépet, amelyet törölt kellett, és hozzon létre egy új virtuális Gépet, használja az operációsrendszer-lemez újbóli szeretne [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk?view=azurermps-6.8.1).

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
Most már csatolhatja ezt a lemezt az operációsrendszer-lemezként egy [új virtuális gép](#create-the-new-vm).

## <a name="option-2-upload-a-specialized-vhd"></a>Option 2: Egyéni VHD feltöltése

A virtuális Merevlemezt egy specializált virtuális eszközzel létrehozott egy a helyszíni virtualizálási, mint a Hyper-V vagy a virtuális gép exportál egy másik felhőalapú tölthet fel.

### <a name="prepare-the-vm"></a>A virtuális gép előkészítése
Használja, a VHD-, ha létrehoz egy új virtuális Gépet. 
  
  * [Töltse fel az Azure Windows virtuális merevlemez előkészítése](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Ne** a virtuális gép általánosításához a Sysprep használatával.
  * Távolítsa el a Vendég virtualizációs eszközök és a virtuális gépen (például a VMware-eszközök) telepített ügynökök.
  * Győződjön meg arról, hogy a virtuális gép van konfigurálva az IP-cím és DNS-beállítások beszerzése a DHCP-Kiszolgálótól. Ez biztosítja, hogy a kiszolgáló a virtuális hálózaton belül egy IP-címet kap, indításkor. 


### <a name="get-the-storage-account"></a>A storage-fiók létrehozása
Szüksége lesz egy storage-fiókot az Azure-ban, a feltöltött virtuális merevlemez tárolásához. Használjon egy meglévő tárfiókot, vagy hozzon létre egy újat. 

A rendelkezésre álló tár fiókokat jeleníti meg.

```powershell
Get-AzStorageAccount
```

Egy meglévő tárfiókot használja, továbbléphet a [töltse fel a virtuális merevlemez](#upload-the-vhd-to-your-storage-account) szakaszban.

Tárfiók létrehozása.

1. Szüksége lesz az erőforráscsoport, ahol létrejön a tárfiók nevére. Használja a Get-AzResourceGroup tekintse meg az összes erőforráscsoport az előfizetésben található.
   
    ```powershell
    Get-AzResourceGroup
    ```

    Hozzon létre egy erőforráscsoportot *myResourceGroup* a a *USA nyugati RÉGIÓJA* régióban.

    ```powershell
    New-AzResourceGroup `
       -Name myResourceGroup `
       -Location "West US"
    ```

2. Hozzon létre egy tárfiókot, nevű *mystorageaccount* használatával új erőforráscsoportban a [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) parancsmagot.
   
    ```powershell
    New-AzStorageAccount `
       -ResourceGroupName myResourceGroup `
       -Name mystorageaccount `
       -Location "West US" `
       -SkuName "Standard_LRS" `
       -Kind "Storage"
    ```

### <a name="upload-the-vhd-to-your-storage-account"></a>A virtuális lemezek feltöltése a storage-fiókba 
Használja a [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) parancsmaggal töltse fel a VHD-t a tárfiókban lévő tárolóba. Ez a példa feltölti a fájlt *myVHD.vhd* a "C:\Users\Public\Documents\Virtual merevlemezek\" tárfiókhoz nevű *mystorageaccount* a a  *myResourceGroup* erőforráscsoportot. A fájl tárolva van nevű tárolót *mycontainer* és az új fájl neve lesz *myUploadedVHD.vhd*.

```powershell
$resourceGroupName = "myResourceGroup"
$urlOfUploadedVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $resourceGroupName `
   -Destination $urlOfUploadedVhd `
   -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Ha a parancs nem sikeres, kap egy választ, amely ehhez hasonló:

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

Ez a parancs eltarthat egy ideig, a hálózati kapcsolatot és a VHD-fájl méretétől függően.

### <a name="create-a-managed-disk-from-the-vhd"></a>Felügyelt lemez létrehozása virtuális merevlemezről

Felügyelt lemez létrehozása speciális virtuális merevlemezből a tárfiókban található használatával [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk). Ez a példa *myOSDisk1* a lemez neve helyezi a lemez *Standard_LRS* tárolási és használja *https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd* , a forrás virtuális merevlemez URI Azonosítóját.

Hozzon létre egy új erőforráscsoportot a új virtuális gép számára.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Az új operációsrendszer-lemez létrehozása a feltöltött virtuális merevlemezből. 

```powershell
$sourceUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd'
$osDiskName = 'myOsDisk'
$osDisk = New-AzDisk -DiskName $osDiskName -Disk `
    (New-AzDiskConfig -AccountType Standard_LRS  `
    -Location $location -CreateOption Import `
    -SourceUri $sourceUri) `
    -ResourceGroupName $destinationResourceGroup
```

## <a name="option-3-copy-an-existing-azure-vm"></a>3. lehetőség: Egy meglévő Azure virtuális gép másolása

Létrehozhat egy virtuális Gépet, amely szerint pillanatkép készítése a virtuális gép a managed disks szolgáltatást használ, és majd a pillanatkép használatával hozzon létre egy új felügyelt lemez és a egy új virtuális Gépet egy példányát.


### <a name="take-a-snapshot-of-the-os-disk"></a>Az operációsrendszer-lemez pillanatfelvételének

Csak egyetlen lemez vagy -beli virtuális gépek (beleértve az összes lemez) teljes pillanatkép is igénybe vehet. A következő lépések bemutatják, hogyan csak a virtuális gép operációsrendszer-lemez pillanatfelvételének a [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) parancsmagot. 

Először állítsa be az egyes paramétereket. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

A Virtuálisgép-objektum beolvasása.

```powershell
$vm = Get-AzVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
Az operációs rendszer lemezének neve beolvasása.

 ```powershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

Hozza létre a pillanatkép-konfigurációt. 

 ```powershell
$snapshotConfig =  New-AzSnapshotConfig `
   -SourceUri $disk.Id `
   -OsType Windows `
   -CreateOption Copy `
   -Location $location 
```

A pillanatkép elkészítésére.

```powershell
$snapShot = New-AzSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


A pillanatkép használatával hoz létre egy virtuális gép, kell lennie a nagy teljesítményű, adja hozzá a paraméter `-AccountType Premium_LRS` a New-AzSnapshotConfig parancshoz. Ez a paraméter a pillanatképet hoz létre, úgy, hogy prémium szintű felügyelt lemez lesz tárolva. Prémium szintű Managed Disks drágább, mint a standard szintű, ezért mindenképpen kell prémium Ez a paraméter használata előtt.

### <a name="create-a-new-disk-from-the-snapshot"></a>Hozzon létre egy új lemezt a pillanatképből

Felügyelt lemez létrehozása pillanatképből a használatával [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk). Ez a példa *myOSDisk* a lemez neve.

Hozzon létre egy új erőforráscsoportot a új virtuális gép számára.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Állítsa be az operációs rendszer lemezének neve. 

```powershell
$osDiskName = 'myOsDisk'
```

A felügyelt lemez létrehozása.

```powershell
$osDisk = New-AzDisk -DiskName $osDiskName -Disk `
    (New-AzDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>Az új virtuális gép létrehozása 

Az új virtuális gép által használt hálózati és más Virtuálisgép-erőforrások létrehozásához.

### <a name="create-the-subnet-and-virtual-network"></a>Az alhálózat és virtuális hálózat létrehozása

Hozzon létre a [virtuális hálózat](../../virtual-network/virtual-networks-overview.md) és a Virtuálisgép-alhálózatot.

1. Hozza létre az alhálózatot. Ez a példa létrehoz egy nevű alhálózatot *mySubNet*, az erőforráscsoport *myDestinationResourceGroup*, és az alhálózati cím előtag *10.0.0.0/24*.
   
    ```powershell
    $subnetName = 'mySubNet'
    $singleSubnet = New-AzVirtualNetworkSubnetConfig `
       -Name $subnetName `
       -AddressPrefix 10.0.0.0/24
    ```
    
2. Hozza létre a virtuális hálózatot. Ebben a példában a virtuális hálózat nevének beállítása *myVnetName*, a hely *USA nyugati RÉGIÓJA*, és a címelőtag a virtuális hálózat *10.0.0.0/16*. 
   
    ```powershell
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork `
       -Name $vnetName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AddressPrefix 10.0.0.0/16 `
       -Subnet $singleSubnet
    ```    
    

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>A hálózati biztonsági csoport és a egy RDP-szabály létrehozása
Hogy jelentkezzen be a virtuális Géphez a távoli asztal protokoll (RDP), szüksége lesz egy biztonsági szabályt, amely lehetővé teszi a 3389-es RDP-hozzáférést. Ebben a példában a VHD-t az új virtuális gép alapján készült egy meglévő specializált virtuális Gépet, így használhatja, amely létezett a forrás virtuális gép RDP-hez.

Ebben a példában állítja be a hálózati biztonsági csoport (NSG) neve *myNsg* és az RDP-szabály neve, *myRdpRule*.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -Name $nsgName -SecurityRules $rdpRule
    
```

További információ a végpontok és NSG-szabályok: [egy virtuális gép portjainak megnyitása az Azure-ban, a PowerShell-lel](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Hozzon létre egy nyilvános IP-cím és a hálózati adapter
Engedélyezi a kommunikációt a virtuális hálózatban a virtuális géppel, szüksége lesz egy [nyilvános IP-cím](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) és a egy hálózati adaptert.

1. A nyilvános IP-cím létrehozása. Ebben a példában a nyilvános IP-cím neve van beállítva, *myIP*.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress `
       -Name $ipName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AllocationMethod Dynamic
    ```       
    
2. Hozzon létre a hálózati adaptert. Ebben a példában a hálózati adapter neve van beállítva, *myNicName*.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName `
       -ResourceGroupName $destinationResourceGroup `
       -Location $location -SubnetId $vnet.Subnets[0].Id `
       -PublicIpAddressId $pip.Id `
       -NetworkSecurityGroupId $nsg.Id
    ```
    


### <a name="set-the-vm-name-and-size"></a>A virtuális gép neve és mérete

Ebben a példában a virtuális gép nevének beállítása *myVM* és a virtuális gép méretét *Standard_A2*.

```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>A hálózati adapter hozzáadása
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>Az operációsrendszer-lemez hozzáadása 

Az operációsrendszer-lemez hozzáadása a konfiguráció használatával [Set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk). Ebben a példában a lemez méretének megadása *128 GB-os* , és csatolja a felügyelt lemez egy *Windows* operációsrendszer-lemez.
 
```powershell
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>Végezze el a virtuális gép 

A virtuális gép létrehozása használatával [New-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) az imént létrehozott konfigurációkkal.

```powershell
New-AzVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Ha ez a parancs végrehajtása sikeres, akkor a következőhöz hasonló kimenetnek jelenik meg:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Győződjön meg arról, hogy a virtuális gép létrejött-e.
Megjelenik az újonnan létrehozott virtuális gép vagy a a [az Azure portal](https://portal.azure.com) alatt **Tallózás** > **virtuális gépek**, vagy a következő PowerShell-parancsok használatával.

```powershell
$vmList = Get-AzVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>További lépések
Jelentkezzen be az új virtuális gépet. További információkért lásd: [csatlakoztatása és bejelentkezés Windows rendszert futtató Azure virtuális gép](connect-logon.md).

