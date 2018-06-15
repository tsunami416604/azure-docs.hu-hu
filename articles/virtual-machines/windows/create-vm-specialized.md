---
title: Windows virtuális gép létrehozása az Azure-ban speciális VHD-ről |} Microsoft Docs
description: Új Windows virtuális gép létrehozása a Resource Manager üzembe helyezési modellel használatával az operációs rendszer lemezeként speciális felügyelt lemezes csatolásával.
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
ms.openlocfilehash: be7933b038fb5a648249e9b0c73415bff778930b
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012784"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-using-powershell"></a>Windows virtuális gép létrehozása a PowerShell segítségével egy speciális lemezről

Új virtuális gép létrehozása az operációs rendszer lemezeként speciális felügyelt lemezes csatolásával. A speciális lemez virtuális merevlemez (VHD) másolatát egy meglévő virtuális gépről, a felhasználói fiókok, alkalmazások és más állapot adatait az eredeti virtuális gép által. 

Ha speciális virtuális merevlemez használatával hozzon létre egy új virtuális Gépet, az új virtuális gép megőrzi az eredeti virtuális gép számítógépneve. Olyan számítógép-specifikus információkat is megmarad, és néhány esetben az ismétlődő adatokat hibát okozhat. Vegye figyelembe, milyen típusú számítógép-specifikus adatok az alkalmazások támaszkodnak, ha egy virtuális Gépet.

Több lehetőség közül választhat:
* [Használjon egy meglévő felügyelt lemezes](#option-1-use-an-existing-disk). Ez akkor hasznos, ha egy virtuális Gépet, amely nem működik megfelelően. Törölheti a virtuális gép újbóli a felügyelt lemezen hozzon létre egy új virtuális Gépet. 
* [VHD feltöltése](#option-2-upload-a-specialized-vhd) 
* [Egy meglévő Azure virtuális gép készített pillanatfelvételek segítségével történő másolása](#option-3-copy-an-existing-azure-vm)

Használhatja az Azure portálon is [hozzon létre egy új virtuális Gépet egy speciális virtuális merevlemezről](create-vm-specialized-portal.md).

Ez a témakör bemutatja, hogyan felügyelt lemezeket használni. Ha egy örökölt üzembe helyezéssel rendelkezik, amelyhez, tárfiókot használ, lásd: [olyan virtuális merevlemezről speciális tárfiók a virtuális gép létrehozása](sa-create-vm-specialized.md)

## <a name="before-you-begin"></a>Előkészületek
Ha a PowerShell segítségével, győződjön meg arról, hogy rendelkezik-e a legújabb verzióját a AzureRM.Compute PowerShell-modult. 

```powershell
Install-Module AzureRM -RequiredVersion 6.0.0
```
További információkért lásd: [Azure PowerShell Versioning](/powershell/azure/overview).

## <a name="option-1-use-an-existing-disk"></a>1. lehetőség: A meglévő lemez használata

Ha egy virtuális Gépet, amelyet törölt volt, és szeretné használni, hozzon létre egy új virtuális Gépet, használja az operációs rendszer lemezének [Get-AzureRmDisk](/azure/powershell/get-azurermdisk).

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzureRmDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
Ez a lemez az operációs rendszer lemezeként való most csatolhat egy [új virtuális gép](#create-the-new-vm).

## <a name="option-2-upload-a-specialized-vhd"></a>2. lehetőség: A speciális virtuális merevlemez feltöltéséhez.

Feltöltheti a virtuális merevlemez eszközzel létrehozott egy helyszíni virtualizációs, például a Hyper-V vagy a virtuális gép egy másik felhőből exportált speciális virtuális gép alapján.

### <a name="prepare-the-vm"></a>A virtuális gép előkészítése
Ha szeretne használni, mint a VHD-t hozzon létre egy új virtuális Gépet, győződjön meg arról, a következő lépéseket. 
  
  * [Az Azure-bA feltölteni egy Windows virtuális merevlemez előkészítése](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Ne** generalize a virtuális Gépet a Sysprep használata.
  * Távolítsa el a Vendég virtualizációs eszközök és a virtuális Gépre (például a VMware-eszközök) telepített ügynökök.
  * Győződjön meg arról, a virtuális Géphez való lekérésére, az IP-cím és DNS-beállítások DHCP van konfigurálva. Ez biztosítja, hogy a kiszolgáló a Vneten belül egy IP-címet kap, indításkor. 


### <a name="get-the-storage-account"></a>A storage-fiók beszerzése
Kell egy a feltöltött virtuális merevlemez tárolásához Azure storage-fiókot. Meglévő tárfiók használata, vagy hozzon létre egy újat. 

A rendelkezésre álló tárfiókok megjelenítéséhez írja be:

```powershell
Get-AzureRmStorageAccount
```

Ha egy meglévő tárfiókot használni szeretne, ugorjon a [a virtuális merevlemez feltöltéséhez](#upload-the-vhd-to-your-storage-account) szakasz.

Ha létrehoz egy tárfiókot van szüksége, kövesse az alábbi lépéseket:

1. Az erőforráscsoport, ahol kell létrehozni a tárfiók neve van szüksége. Tudja meg, amelyek az előfizetésében szereplő összes erőforráscsoport, írja be:
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Nevű erőforráscsoport létrehozásához *myResourceGroup* a a *USA nyugati régiója* régió, írja be:

    ```powershell
    New-AzureRmResourceGroup `
       -Name myResourceGroup `
       -Location "West US"
    ```

2. Hozzon létre egy tárfiókot, nevű *mystorageaccount* Ez az erőforráscsoport segítségével a a [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) parancsmagot:
   
    ```powershell
    New-AzureRmStorageAccount `
       -ResourceGroupName myResourceGroup `
       -Name mystorageaccount `
       -Location "West US" `
       -SkuName "Standard_LRS" `
       -Kind "Storage"
    ```

### <a name="upload-the-vhd-to-your-storage-account"></a>A VHD-fájlt feltölti a tárfiók 
Használja a [Add-AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd) parancsmaggal töltse fel a VHD-t a tárfiókban lévő tárolót. Ez a példa feltölti *myVHD.vhd* a `"C:\Users\Public\Documents\Virtual hard disks\"` tárfiókba nevű *mystorageaccount* a a *myResourceGroup* erőforráscsoportot. A fájl tárolási helye tárolót *mycontainer* és az új fájlnevet lesz *myUploadedVHD.vhd*.

```powershell
$resourceGroupName = "myResourceGroup"
$urlOfUploadedVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $resourceGroupName `
   -Destination $urlOfUploadedVhd `
   -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Sikeres ellenőrzés esetén, a következőhöz hasonló választ kap:

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

Attól függően, hogy a hálózati kapcsolat és a VHD-fájl mérete a parancs is igénybe vehet igénybe

### <a name="create-a-managed-disk-from-the-vhd"></a>Hozzon létre egy felügyelt lemezes virtuális merevlemezről

A speciális VHD-n a tárolási fiók használatával hozhat létre egy felügyelt lemezt [New-AzureRMDisk](/powershell/module/azurerm.compute/new-azurermdisk). Ez a példa **myOSDisk1** a lemez neve, a lemez helyezi *Standard_LRS* tárolási és felhasználási *https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd* mint a forrás VHD URI.

Hozzon létre egy új erőforráscsoportot a új virtuális gép számára.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzureRmResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

A feltöltött virtuális merevlemez létrehozása az új operációsrendszer-lemezképet. 

```powershell
$sourceUri = (https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd)
$osDiskName = 'myOsDisk'
$osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk `
    (New-AzureRmDiskConfig -AccountType Standard_LRS  `
    -Location $location -CreateOption Import `
    -SourceUri $sourceUri) `
    -ResourceGroupName $destinationResourceGroup
```

## <a name="option-3-copy-an-existing-azure-vm"></a>3. lehetőség: Egy meglévő Azure virtuális gép másolása

Létrehozhat egy virtuális gép által használt lemezek kezeli a virtuális gép pillanatképének, majd, hogy a pillanatkép alapján hozzon létre egy új kezelt lemez és az új virtuális gép egy másolatát.


### <a name="take-a-snapshot-of-the-os-disk"></a>Pillanatkép készítése a operációsrendszer-lemez

Választhat (beleértve az összes lemez) pillanatkép, és a teljes virtuális gép vagy egy egyetlen lemez. A következő lépések bemutatják a pillanatképet készít, csak az operációsrendszer-lemezképet a virtuális gép használata a [New-AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot) parancsmag. 

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
Az operációsrendszer-lemez neve beolvasása.

 ```powershell
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

A pillanatkép-konfiguráció létrehozása. 

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


Tervezi a pillanatkép létrehozása, amely kell lennie a magas hajt végre, ha a paraméter használható `-AccountType Premium_LRS` a New-AzureRmSnapshot paranccsal. A paraméter a pillanatkép hoz, hogy prémium felügyelt lemezként tárolja. Prémium szintű kezelt lemezek drágább szabvány. Ezért mindenképpen valóban szükség van prémium a paraméter használata előtt.

### <a name="create-a-new-disk-from-the-snapshot"></a>A pillanatkép hozhat létre egy új lemezt

Hozzon létre egy felügyelt lemezes a pillanatkép a [New-AzureRMDisk](/powershell/module/azurerm.compute/new-azurermdisk). Ez a példa *myOSDisk* a lemez neve.

Hozzon létre egy új erőforráscsoportot a új virtuális gép számára.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzureRmResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Állítsa be az operációsrendszer-lemez neve. 

```powershell
$osDiskName = 'myOsDisk'
```

A kezelt lemez létrehozása.

```powershell
$osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk `
    (New-AzureRmDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>Az új virtuális gép létrehozása 

Hozzon létre az új virtuális gép által használt hálózati és más Virtuálisgép-erőforrások.

### <a name="create-the-subnet-and-vnet"></a>Az alhálózat és a virtuális hálózat létrehozása

Hozza létre a virtuális hálózat és az alhálózati a [virtuális hálózati](../../virtual-network/virtual-networks-overview.md).

Hozza létre az alhálózatot. Ez a példa-alhálózatot hoz létre nevű **mySubNet**, erőforráscsoportban **myDestinationResourceGroup**, és beállítja a cím alhálózati előtagját **10.0.0.0/24**.
   
```powershell
$subnetName = 'mySubNet'
$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig `
   -Name $subnetName `
   -AddressPrefix 10.0.0.0/24
```

A virtuális hálózat létrehozása. Ez a példa beállítja a virtuálishálózat-névnek kell lennie **myVnetName**, a helyet, **USA nyugati régiója**, és a virtuális hálózat címelőtag **10.0.0.0/16**. 
   
```powershell
$vnetName = "myVnetName"
$vnet = New-AzureRmVirtualNetwork `
   -Name $vnetName -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $singleSubnet
```    


### <a name="create-the-network-security-group-and-an-rdp-rule"></a>A hálózati biztonsági csoport és egy RDP-szabály létrehozása
Nem fogja tudni jelentkezzen be a virtuális gép RDP Funkciót használnak, olyan biztonsági szabályt, amely lehetővé teszi a hozzáférést RDP a 3389-es porton kell. A VHD-t az új virtuális gép egy meglévő speciális virtuális gép alapján készült, mert egy fiókot a forrás virtuális gép RDP is használhatja.

Ebben a példában a NSG nevének beállítása **myNsg** és az RDP-szabály neve a **myRdpRule**.

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

Végpontok és NSG-szabályok kapcsolatos további információkért lásd: [az Azure PowerShell használatával egy virtuális gép portok megnyitása](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Nyilvános IP-cím és a hálózati adapter létrehozása
A virtuális hálózaton a virtuális géppel való kommunikáció biztosításához egy [nyilvános IP-cím](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) és egy hálózati adapter szükséges.

A nyilvános IP-cím létrehozása. Ebben a példában a nyilvános IP-cím neve értéke **myIP**.
   
```powershell
$ipName = "myIP"
$pip = New-AzureRmPublicIpAddress `
   -Name $ipName -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -AllocationMethod Dynamic
```       

Hozzon létre a hálózati adaptert. Ebben a példában a hálózati adapter neve legyen **myNicName**.
   
```powershell
$nicName = "myNicName"
$nic = New-AzureRmNetworkInterface -Name $nicName `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location -SubnetId $vnet.Subnets[0].Id `
   -PublicIpAddressId $pip.Id `
   -NetworkSecurityGroupId $nsg.Id
```



### <a name="set-the-vm-name-and-size"></a>Adja meg a virtuális gép neve és mérete

Ebben a példában a virtuális gép nevének beállítása *myVM* , a virtuális gép méretét pedig *Standard_A2*.

```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>A hálózati adapter hozzáadása
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>Az operációsrendszer-lemez hozzáadása 

Az operációsrendszer-lemez hozzáadása a használt konfiguráció [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk). Ebben a példában a lemez méretének beállítása *128 GB-os* , és csatolja a felügyelt lemezt, a *Windows* operációsrendszer-lemezzel.
 
```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>Végezze el a virtuális gép 

Hozzon létre a virtuális gép az [New-AzureRMVM](/powershell/module/azurerm.compute/new-azurermvm)az imént létrehozott konfigurációkat.

```powershell
New-AzureRmVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Ha ez a parancs sikeres, megjelenik a kimenet ehhez hasonló:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Győződjön meg arról, hogy létrejött-e a virtuális gép
Megtekintheti az újonnan létrehozott virtuális gép vagy a a [Azure-portálon](https://portal.azure.com), a **Tallózás** > **virtuális gépek**, vagy a következő PowerShell-parancsok használatával:

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>További lépések
Jelentkezzen be az új virtuális gép. További információkért lásd: [csatlakoztatása, és jelentkezzen be a Windowst futtató Azure virtuális gép](connect-logon.md).

