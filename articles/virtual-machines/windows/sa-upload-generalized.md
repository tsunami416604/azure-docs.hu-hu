---
title: Töltsön fel egy általánosvirtuális gépet, hogy több virtuális gépet hozzon létre az Azure-ban
description: Töltsön fel egy általános virtuális merevlemezt egy Azure-tárfiókba, és hozzon létre egy Windows virtuális gép et az Erőforrás-kezelő telepítési modelljével.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/18/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 933b648f15418c4838d3da1ea8379267765c784b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073325"
---
# <a name="upload-a-generalized-vhd-to-azure-to-create-a-new-vm"></a>Általános virtuális merevlemez feltöltése az Azure-ba új virtuális gép létrehozásához

Ez a témakör egy általánosnem felügyelt lemez feltöltését ismerteti egy tárfiókba, majd egy új virtuális gép létrehozása a feltöltött lemez használatával. Egy általános ított virtuális merevlemez-lemezkép a Sysprep programmal eltávolította az összes személyes fiókadatot. 

Ha egy tárfiókban egy speciális virtuális merevlemezből szeretne virtuális gépet létrehozni, olvassa el a [Virtuális gép létrehozása speciális virtuális merevlemezről című](sa-create-vm-specialized.md)témakört.

Ez a témakör a tárfiókok használatát ismerteti, de azt javasoljuk, hogy az ügyfelek inkább a Felügyelt lemezek használatára költözzenek. A felügyelt lemezek használatával új virtuális gépek előkészítésének, feltöltésének és létrehozásának teljes útmutatója: [Új virtuális gép létrehozása az Azure-ba felügyelt lemezek használatával feltöltött általános virtuális merevlemezről](upload-generalized-managed.md)című témakörben található.

 

## <a name="prepare-the-vm"></a>A virtuális gép előkészítése

Egy általános ított virtuális merevlemez a Sysprep programmal eltávolította az összes személyes fiókadatot. Ha a virtuális merevlemezt szeretné lemezképként használni új virtuális gépek létrehozásához, a következőket kell tennie:
  
  * [Készítsen elő egy Windows vHD-t az Azure-ba való feltöltéshez.](prepare-for-upload-vhd-image.md) 
  * A virtuális gép általánosítása a Sysprep programmal

### <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Windows virtuális gép általánosítása a Sysprep programmal
Ez a szakasz bemutatja, hogyan általánosíthatja a Windows virtuális gép képként való használatát. A Sysprep többek között minden személyes fiókadatot eltávolít, a gépet pedig előkészíti rendszerképként való használatra. További információ a Sysprepről: [A Sysprep használata: Bevezetés](https://technet.microsoft.com/library/bb457073.aspx).

Győződjön meg arról, hogy a sysprep támogatja a számítógépen futó kiszolgálói szerepköröket. További információ: [Sysprep support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Ha a Sysprep programot futtatja, mielőtt először feltöltené a virtuális merevlemezt az Azure-ba, a Sysprep futtatása előtt [készítse el a virtuális számítógépet.](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 
> 
> 

1. Jelentkezzen be a Windows rendszerű virtuális gépre.
2. Nyissa meg a parancsablakot rendszergazdaként. Módosítsa a könyvtárat **%windir%\system32\sysprep** `sysprep.exe`értékre, majd futtassa a parancsot.
3. A **Rendszer-előkészítő eszköz** párbeszédpanelen válassza **A kezdőélmény indítása** lehetőséget, és győződjön meg róla, hogy be van-e jelölve az **Általánosítás** jelölőnégyzet.
4. A **Leállítási beállítások párbeszédpanelen**válassza a **Leállítás**lehetőséget.
5. Kattintson az **OK** gombra.
   
    ![A Sysprep indítása](./media/upload-generalized-managed/sysprepgeneral.png)
6. A Sysprep a feladat befejezése után leállítja a virtuális gépet. 

> [!IMPORTANT]
> Ne indítsa újra a virtuális gép, amíg nem végzett a virtuális merevlemez feltöltésével az Azure-ba, vagy hozzon létre egy lemezképet a virtuális gépről. Ha a virtuális gép véletlenül újraindul, futtassa a Sysprep programot, hogy újra általánosítsa.
> 
> 


## <a name="upload-the-vhd"></a>A virtuális merevlemez feltöltése

Töltse fel a virtuális merevlemezt egy Azure-tárfiókba.

### <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Ha még nincs telepítve a PowerShell 1.4-es vagy újabb verziója, olvassa el [az Azure PowerShell telepítése és konfigurálása című témakört.](/powershell/azure/overview)

1. Nyissa meg az Azure PowerShellt, és jelentkezzen be Azure-fiókjába. Megnyílik egy előugró ablak, amelyen megadhatja az Azure-fiók hitelesítő adatait.
   
    ```powershell
    Connect-AzAccount
    ```
2. Az elérhető előfizetések előfizetési azonosítóinak beszerzése.
   
    ```powershell
    Get-AzSubscription
    ```
3. Állítsa be a megfelelő előfizetést az előfizetés-azonosító használatával. Cserélje `<subscriptionID>` le a megfelelő előfizetés azonosítójára.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

### <a name="get-the-storage-account"></a>A tárfiók beszerezése
A feltöltött virtuálisgép-lemezkép tárolásához szüksége van egy tárfiókra az Azure-ban. Használhatja a meglévő tárfiókot, vagy hozzon létre egy újat. 

A rendelkezésre álló tárfiókok megjelenítéséhez írja be a következőt:

```powershell
Get-AzStorageAccount
```

Ha egy meglévő tárfiókot szeretne használni, folytassa a Virtuálisgép-lemezkép feltöltése szakasz.

Ha tárfiókot kell létrehoznia, kövesse az alábbi lépéseket:

1. Szüksége van annak az erőforráscsoportnak a nevére, amelyhez a tárfiókot létre kell hozni. Az előfizetésben lévő összes erőforráscsoport megkereséséhez írja be a következőt:
   
    ```powershell
    Get-AzResourceGroup
    ```

    Ha az **USA nyugati régiójában** létre szeretne hozni egy **myResourceGroup** nevű erőforráscsoportot, írja be a következőt:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Hozzon létre egy **mystorageaccount** nevű tárfiókot ebben az erőforráscsoportban a [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) parancsmag használatával:
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
 
### <a name="start-the-upload"></a>A feltöltés indítása 

Az [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) parancsmag használatával töltse fel a lemezképet a tárfiók ban lévő tárolóba. Ez a példa feltölti a **myVHD.vhd** fájlt egy `"C:\Users\Public\Documents\Virtual hard disks\"` **myResourceGroup** **erőforráscsoport myResourceAccount nevű tárfiókba.** A fájl a **mycontainer** nevű tárolóba kerül, és az új fájlnév **a myUploadedVHD.vhd**lesz.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Ha sikeres, a következőhöz hasonló választ kap:

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

A hálózati kapcsolattól és a virtuális merevlemez-fájl méretétől függően ez a parancs eltarthat egy ideig.


## <a name="create-a-new-vm"></a>Hozzon létre egy új virtuális gépet 

Most már használhatja a feltöltött virtuális merevlemez egy új virtuális gép létrehozásához. 

### <a name="set-the-uri-of-the-vhd"></a>A virtuális merevlemez URI-jának beállítása

A vhd-hez használandó URI a következő formátumú: https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**.vhd. Ebben a példában a **myVHD** nevű virtuális merevlemez a **mystorageaccount** tárolóban található a **mystorage**tárolóban.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Hozza létre a [virtuális hálózat](../../virtual-network/virtual-networks-overview.md)virtuális hálózatát és alhálózatát.

1. Hozza létre az alhálózatot. A következő minta létrehoz egy **mySubnet** nevű alhálózatot a **myResourceGroup** erőforráscsoportban **a 10.0.0.0/24**címelőtaggal.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Hozza létre a virtuális hálózatot. A következő minta létrehoz egy **myVnet** nevű virtuális hálózatot az **USA nyugati részén,** **10.0.0.0/16**címelőtaggal.  
   
    ```powershell
    $location = "WestUS"
    $vnetName = "myVnet"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Nyilvános IP-cím és hálózati adapter létrehozása
A virtuális hálózaton a virtuális géppel való kommunikáció biztosításához egy [nyilvános IP-cím](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) és egy hálózati adapter szükséges.

1. Hozzon létre egy nyilvános IP-címet. Ez a példa létrehoz egy nyilvános IP-címet **myPip**néven. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Hozza létre a hálózati adaptert. Ebben a példában létrehoz egy **myNic**nevű nic nevű hálózati adaptert. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>A hálózati biztonsági csoport és egy RDP-szabály létrehozása
Ahhoz, hogy az RDP használatával be tudjon jelentkezni a virtuális gépre, rendelkeznie kell egy biztonsági szabállyal, amely lehetővé teszi az RDP-hozzáférést a 3389-es porton. 

Ebben a példában létrehoz egy **MyNsg** nevű NSG-t, amely egy **myRdpRule** nevű szabályt tartalmaz, amely lehetővé teszi az RDP-forgalmat a 3389-es porton keresztül. Az NSG-kről további információt a [Portok megnyitása az Azure-ban a PowerShell használatával című témakörben talál.](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>Változó létrehozása a virtuális hálózathoz
Hozzon létre egy változót a befejezett virtuális hálózathoz. 

```powershell
$vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>Virtuális gép létrehozása
A következő PowerShell-parancsfájl bemutatja, hogyan állíthatja be a virtuális gép konfigurációit, és a feltöltött virtuálisgép-lemezképet használhatja az új telepítés forrásaként.



```powershell
# Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential

    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"

    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"

    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"

    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"

    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"

    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant
    # storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage,
    # Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"

    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

    # Set the VM name and size
    $vmConfig = New-AzVMConfig -VMName $vmName -VMSize $vmSize

    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzVMNetworkInterface -VM $vm -Id $nic.Id

    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows

    # Create the new VM
    New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>A virtuális gép létrehozásának ellenőrzése
Ha elkészült, meg kell jelennie az újonnan létrehozott virtuális gép az [Azure Portalon](https://portal.azure.com) a**Virtuális gépek** **tallózása** > csoportban, vagy a következő PowerShell-parancsok használatával:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>További lépések
Az új virtuális gép Azure PowerShell használatával történő kezeléséhez olvassa el [a Virtuális gépek kezelése az Azure Resource Manager és a PowerShell használatával.](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


