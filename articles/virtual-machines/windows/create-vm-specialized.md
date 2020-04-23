---
title: Windows virtuális gép létrehozása speciális virtuális merevlemezből az Azure-ban
description: Hozzon létre egy új Windows virtuális gép csatlakoztatásával egy speciális felügyelt lemez, mint az operációs rendszer lemez segítségével az Erőforrás-kezelő telepítési modell.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: 2939726898abc2abc0e62d0e36feedbfe7ba3645
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086402"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-by-using-powershell"></a>Windows rendszerű virtuális gép létrehozása speciális lemezről PowerShell-lel

Hozzon létre egy új virtuális gép csatlakoztatásával egy speciális felügyelt lemez, mint az operációs rendszer lemeze. A speciális lemez egy virtuális merevlemez (VHD) másolata egy meglévő virtuális gépről, amely tartalmazza a felhasználói fiókokat, alkalmazásokat és az eredeti virtuális gép egyéb állapotadatait. 

Ha egy speciális virtuális merevlemez t egy új virtuális gép létrehozásához, az új virtuális gép megtartja az eredeti virtuális gép számítógépnevét. A rendszer más számítógép-specifikus információkat is tárol, és bizonyos esetekben ez az ismétlődő információ problémákat okozhat. Virtuális gép másolásakor vegye figyelembe, hogy milyen típusú számítógép-specifikus információkat az alkalmazások támaszkodnak.

Erre számos lehetősége van:
* [Meglévő felügyelt lemez használata](#option-1-use-an-existing-disk). Ez a beállítás akkor hasznos, ha egy virtuális gép, amely nem működik megfelelően. Törölheti a virtuális gép, majd újra a felügyelt lemezt, hogy hozzon létre egy új virtuális gép. 
* [VHD feltöltése](#option-2-upload-a-specialized-vhd) 
* [Meglévő Azure-virtuális gép másolása pillanatképek használatával](#option-3-copy-an-existing-azure-vm)

Az Azure Portalon [is létrehozhat egy új virtuális gép egy speciális virtuális merevlemez.](create-vm-specialized-portal.md)

Ez a cikk bemutatja a felügyelt lemezek használatát. Ha olyan örökölt központi telepítéssel rendelkezik, amelytárfiókot igényel, olvassa el a [Virtuális gép létrehozása egy speciális virtuális merevlemezből egy tárfiókban című témakört.](sa-create-vm-specialized.md)

Azt javasoljuk, hogy korlátozza az egyidejű központi telepítések számát 20 virtuális gépre egyetlen virtuális merevlemezről vagy pillanatképből. 

## <a name="option-1-use-an-existing-disk"></a>1. lehetőség: Meglévő lemez használata

Ha törölt virtuális gépe volt, és az operációs rendszer lemezét újra fel szeretné használni egy új virtuális gép létrehozásához, használja a [Get-AzDisk programot.](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk)

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
Most már csatolhatja ezt a lemezt, mint az operációs rendszer lemezegy [új virtuális gép](#create-the-new-vm).

## <a name="option-2-upload-a-specialized-vhd"></a>2. lehetőség: Speciális virtuális merevlemez feltöltése

Feltöltheti a virtuális merevlemezt egy speciális virtuális gépről, amelyet egy helyszíni virtualizációs eszközzel, például a Hyper-V-vel vagy egy másik felhőből exportált virtuális géptel hoztak létre.

### <a name="prepare-the-vm"></a>A virtuális gép előkészítése
Használja a virtuális merevlemezt, ahogy van egy új virtuális gép létrehozásához. 
  
  * [Készítsen elő egy Windows vHD-t az Azure-ba való feltöltéshez.](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) **Ne** általánosítsa a virtuális gép segítségével Sysprep.
  * Távolítsa el a virtuális gépre telepített vendégvirtualizációs eszközöket és ügynököket (például a VMware-eszközöket).
  * Győződjön meg arról, hogy a virtuális gép úgy van beállítva, hogy megkapja az IP-címet és a DNS-beállításokat a DHCP-től. Ez biztosítja, hogy a kiszolgáló indításkor megkapja a virtuális hálózaton belüli IP-címet. 


### <a name="upload-the-vhd"></a>A virtuális merevlemez feltöltése

Most már feltöltheti a Virtuális merevlemezt közvetlenül egy felügyelt lemezre. További információt a [VHD feltöltése az Azure-ba az Azure PowerShell használatával (VHD feltöltése) témakörben talál.](disks-upload-vhd-to-managed-disk-powershell.md)

## <a name="option-3-copy-an-existing-azure-vm"></a>3. lehetőség: Meglévő Azure-virtuális gép másolása

Létrehozhat egy virtuális gép másolatát, amely felügyelt lemezeket használ a virtuális gép pillanatképének elkészítésével, majd a pillanatkép használatával hozzon létre egy új felügyelt lemezt és egy új virtuális gép.

Ha egy meglévő virtuális gép másolása egy másik régióba, érdemes lehet az azcopy használatával [másolatot készíteni egy másik régióban lévő lemezről.](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) 

### <a name="take-a-snapshot-of-the-os-disk"></a>Pillanatkép készítése az operációs rendszer lemezéről

Pillanatképet készíthet egy teljes virtuális gépről (beleértve az összes lemezt is) vagy csak egyetlen lemezről. A következő lépések bemutatják, hogyan készíthet pillanatképet a virtuális gép operációs rendszerlemezéről az Új AzSnapshot-parancsmaggal. [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) 

Először állítson be néhány paramétert. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

A virtuális gép objektumának leigazolása.

```powershell
$vm = Get-AzVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
Az operációs rendszer lemezének nevének beszerezése.

 ```powershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

Hozza létre a pillanatkép konfigurációját. 

 ```powershell
$snapshotConfig =  New-AzSnapshotConfig `
   -SourceUri $disk.Id `
   -OsType Windows `
   -CreateOption Copy `
   -Location $location 
```

Vegyük a pillanatképet.

```powershell
$snapShot = New-AzSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


Ha ezzel a pillanatképpel hozzon létre egy virtuális gép, amely nek nagy teljesítményűnek kell lennie, adja hozzá a paramétert `-AccountType Premium_LRS` a New-AzSnapshotConfig parancshoz. Ez a paraméter úgy hozza létre a pillanatképet, hogy prémium szintű felügyelt lemezként legyen tárolva. A prémium szintű felügyelt lemezek drágábbak, mint a Standard, ezért győződjön meg róla, hogy szüksége lesz a Prémium verzióra, mielőtt ezt a paramétert használná.

### <a name="create-a-new-disk-from-the-snapshot"></a>Új lemez létrehozása a pillanatképből

Felügyelt lemez létrehozása a pillanatképből a [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk)segítségével. Ez a példa *a myOSDisk-et* használja a lemez nevéhez.

Hozzon létre egy új erőforráscsoportot az új virtuális géphez.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Állítsa be az operációs rendszer lemezének nevét. 

```powershell
$osDiskName = 'myOsDisk'
```

Hozza létre a felügyelt lemezt.

```powershell
$osDisk = New-AzDisk -DiskName $osDiskName -Disk `
    (New-AzDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>Az új virtuális gép létrehozása 

Hozzon létre hálózati és egyéb virtuális gép erőforrásokat az új virtuális gép által használandó.

### <a name="create-the-subnet-and-virtual-network"></a>Az alhálózat és a virtuális hálózat létrehozása

Hozza létre a [virtuális hálózatot](../../virtual-network/virtual-networks-overview.md) és az alhálózatot a virtuális géphez.

1. Hozza létre az alhálózatot. Ez a példa létrehoz egy *mySubNet*nevű alhálózatot a *sajkaerőforráscsoportban,* és az alhálózati cím előtagot *10.0.0.0/24-re állítja.*
   
    ```powershell
    $subnetName = 'mySubNet'
    $singleSubnet = New-AzVirtualNetworkSubnetConfig `
       -Name $subnetName `
       -AddressPrefix 10.0.0.0/24
    ```
    
2. Hozza létre a virtuális hálózatot. Ez a példa a virtuális hálózat nevét *myVnetName-re*, a helyet *usa nyugati részére,* a virtuális hálózat címelőtagját pedig *10.0.0.0/16-ra állítja.* 
   
    ```powershell
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork `
       -Name $vnetName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AddressPrefix 10.0.0.0/16 `
       -Subnet $singleSubnet
    ```    
    

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>A hálózati biztonsági csoport és egy RDP-szabály létrehozása
Ahhoz, hogy a virtuális géptávoli asztali protokoll (RDP) segítségével, szüksége lesz egy biztonsági szabály, amely lehetővé teszi az RDP hozzáférést a 3389-es porton. A példában a virtuális merevlemez az új virtuális gép egy meglévő speciális virtuális gép, így használhatja a fiók, amely létezett a forrás virtuális gépen rdp.

Ez a példa a hálózati biztonsági csoport (NSG) nevét *myNsg-re,* az RDP-szabály nevét pedig *myRdpRule-re állítja.*

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

A végpontokról és az NSG-szabályokról további információt a [Portok megnyitása az Azure-ban a PowerShell használatával című témakörben talál.](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="create-a-public-ip-address-and-nic"></a>Nyilvános IP-cím és hálózati adapter létrehozása
A virtuális hálózat ban lévő virtuális géppel való kommunikáció engedélyezéséhez [nyilvános IP-címre](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) és hálózati adapterre van szükség.

1. Hozza létre a nyilvános IP-címet. Ebben a példában a nyilvános IP-cím neve myIP .In this example, the public IP address name is set to *myIP*.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress `
       -Name $ipName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AllocationMethod Dynamic
    ```       
    
2. Hozza létre a hálózati adaptert. Ebben a példában a hálózati adapter neve *myNicName*.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName `
       -ResourceGroupName $destinationResourceGroup `
       -Location $location -SubnetId $vnet.Subnets[0].Id `
       -PublicIpAddressId $pip.Id `
       -NetworkSecurityGroupId $nsg.Id
    ```
    


### <a name="set-the-vm-name-and-size"></a>A virtuális gép nevének és méretének beállítása

Ez a példa a virtuális gép nevét *a myVM-re* állítja, a virtuális gép méretét pedig *Standard_A2.*

```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>A hálózati adapter hozzáadása
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>Az operációs rendszer lemezének hozzáadása 

Adja hozzá az operációs rendszer lemezét a konfigurációhoz a [Set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk)segítségével. Ez a példa a lemez méretét *128 GB-ra* állítja, és a felügyelt lemezt *Windows* operációs rendszer lemezeként csatlakoztatja.
 
```powershell
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>A virtuális gép befejezése 

Hozza létre a virtuális gép használatával [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) az imént létrehozott konfigurációk.

```powershell
New-AzVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Ha ez a parancs sikeres, a következő hez hasonló kimenet jelenik meg:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>A virtuális gép létrehozásának ellenőrzése
Az újonnan létrehozott virtuális gépet vagy az [Azure Portalon](https://portal.azure.com) a**Virtuális gépek** **tallózása** > csoportban, vagy a következő PowerShell-parancsok használatával kell látnia.

```powershell
$vmList = Get-AzVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>További lépések
Jelentkezzen be az új virtuális gépre. További információt a [Csatlakozás és bejelentkezés Windows rendszerű Azure-alapú virtuális gépre](connect-logon.md)című témakörben talál.

