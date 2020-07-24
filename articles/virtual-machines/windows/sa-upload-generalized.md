---
title: Több virtuális gép létrehozása az Azure-ban egy általánosított virtuális merevlemez feltöltése
description: Töltse fel az általánosított virtuális merevlemezt egy Azure Storage-fiókba, és hozzon létre egy Windows rendszerű virtuális gépet a Resource Manager-alapú üzemi modell használatával.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/18/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.custom: storage-accounts
ms.openlocfilehash: d340e37cf64961971c03af8d08a669c27d758116
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87074191"
---
# <a name="upload-a-generalized-vhd-to-azure-to-create-a-new-vm"></a>Általános VHD feltöltése az Azure-ba új virtuális gép létrehozásához

Ez a témakör egy általánosított, nem felügyelt lemez egy Storage-fiókba való feltöltését, majd egy új virtuális gép a feltöltött lemezzel történő létrehozását ismerteti. Az általános VHD-rendszerkép az összes személyes fiók adatait eltávolította a Sysprep használatával. 

Ha virtuális merevlemezt szeretne létrehozni egy Storage-fiókban található speciális VHD-ből, tekintse meg [a virtuális gép létrehozása speciális virtuális merevlemezről](sa-create-vm-specialized.md)című témakört.

Ez a témakör a Storage-fiókok használatát mutatja be, de javasoljuk, hogy az ügyfelek a Managed Disks használatát használják helyette. A felügyelt lemezeket használó új virtuális gépek előkészítésének, feltöltésének és létrehozásának teljes körű ismertetését az [új virtuális gép létrehozása az Azure-ba a Managed Disks használatával feltöltött általánosított virtuális merevlemezről](upload-generalized-managed.md)című témakörben tekintheti meg.

 

## <a name="prepare-the-vm"></a>A virtuális gép előkészítése

Az általános VHD-k az összes személyes fiókadatok el lettek távolítva a Sysprep használatával. Ha a virtuális merevlemezt képként szeretné létrehozni új virtuális gépek létrehozásához, tegye a következőket:
  
  * [Készítse elő a Windows VHD-t az Azure-ba való feltöltéshez](prepare-for-upload-vhd-image.md). 
  * A virtuális gép általánosítása a Sysprep használatával

### <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Windows rendszerű virtuális gép általánosítása a Sysprep használatával
Ez a szakasz bemutatja, hogyan általánosíthatja a Windows rendszerű virtuális gépet képként való használatra. A Sysprep többek között minden személyes fiókadatot eltávolít, a gépet pedig előkészíti rendszerképként való használatra. További információ a Sysprepről: [A Sysprep használata: Bevezetés](/previous-versions/windows/it-pro/windows-xp/bb457073(v=technet.10)).

Győződjön meg arról, hogy a Sysprep támogatja a számítógépen futó kiszolgálói szerepköröket. További információ: a [Sysprep-támogatás a kiszolgálói szerepkörökhöz](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Ha a Sysprept először a virtuális merevlemez Azure-ba való feltöltése előtt futtatja, győződjön meg arról, hogy [előkészítette a virtuális gépet](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a Sysprep futtatása előtt. 
> 
> 

1. Jelentkezzen be a Windows rendszerű virtuális gépre.
2. Nyissa meg a parancsablakot rendszergazdaként. Módosítsa a könyvtárat a **%WINDIR%\system32\sysprep**értékre, majd futtassa a parancsot `sysprep.exe` .
3. A **Rendszer-előkészítő eszköz** párbeszédpanelen válassza **A kezdőélmény indítása** lehetőséget, és győződjön meg róla, hogy be van-e jelölve az **Általánosítás** jelölőnégyzet.
4. A **leállítási beállítások**területen válassza a **Leállítás**lehetőséget.
5. Kattintson az **OK** gombra.
   
    ![A Sysprep elindítása](./media/upload-generalized-managed/sysprepgeneral.png)
6. A Sysprep a feladat befejezése után leállítja a virtuális gépet. 

> [!IMPORTANT]
> Ne indítsa újra a virtuális gépet, amíg el nem végzi a virtuális merevlemez feltöltését az Azure-ba, vagy rendszerkép létrehozása a virtuális gépről. Ha a virtuális gép újraindítása véletlenül megtörténik, futtassa újra a Sysprep eszközt, hogy újra általánosítsa.
> 
> 


## <a name="upload-the-vhd"></a>A VHD feltöltése

Töltse fel a VHD-t egy Azure Storage-fiókba.

### <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Ha még nincs telepítve a PowerShell 1,4-es vagy újabb verziója, olvassa el [a Azure PowerShell telepítése és konfigurálása című témakört](/powershell/azure/).

1. Nyissa meg Azure PowerShell, és jelentkezzen be az Azure-fiókjába. Megnyílik egy előugró ablak, amelyen megadhatja az Azure-fiókja hitelesítő adatait.
   
    ```powershell
    Connect-AzAccount
    ```
2. Szerezze be a rendelkezésre álló előfizetések előfizetési azonosítóit.
   
    ```powershell
    Get-AzSubscription
    ```
3. Állítsa be a megfelelő előfizetést az előfizetés-azonosító használatával. A helyére írja be a `<subscriptionID>` megfelelő előfizetés azonosítóját.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

### <a name="get-the-storage-account"></a>A Storage-fiók beszerzése
A feltöltött virtuálisgép-rendszerkép tárolásához egy Azure Storage-fiókra van szüksége. Használhat meglévő Storage-fiókot, vagy létrehozhat egy újat. 

A rendelkezésre álló Storage-fiókok megjelenítéséhez írja be a következőt:

```powershell
Get-AzStorageAccount
```

Ha meglévő Storage-fiókot szeretne használni, folytassa a virtuális gép rendszerképének feltöltésével foglalkozó szakaszt.

Ha létre kell hoznia egy Storage-fiókot, kövesse az alábbi lépéseket:

1. Szüksége lesz annak az erőforráscsoport-csoportnak a nevére, amelyben létre szeretné hozni a Storage-fiókot. Az előfizetéshez tartozó összes erőforráscsoport megkereséséhez írja be a következőt:
   
    ```powershell
    Get-AzResourceGroup
    ```

    A **myResourceGroup** nevű erőforráscsoport létrehozásához az **USA nyugati** régiójában írja be a következőt:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Hozzon létre egy **mystorageaccount** nevű Storage-fiókot ebben az erőforráscsoporthoz a [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) parancsmag használatával:
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
 
### <a name="start-the-upload"></a>A feltöltés elindítása 

Az [Add-AzVhd](/powershell/module/az.compute/add-azvhd) parancsmag használatával töltse fel a rendszerképet a Storage-fiókjában található tárolóba. Ez a példa feltölti a **myVHD. vhd** fájlt `"C:\Users\Public\Documents\Virtual hard disks\"` egy **mystorageaccount** nevű Storage-fiókba a **myResourceGroup** -erőforráscsoporthoz. A fájl a **mycontainer** nevű tárolóba kerül, és az új fájlnév **myUploadedVHD. vhd**lesz.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Ha a művelet sikeres, a következőhöz hasonló választ kap:

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

A hálózati kapcsolatban és a VHD-fájl méretétől függően ez a parancs hosszabb időt is igénybe vehet.


## <a name="create-a-new-vm"></a>Új virtuális gép létrehozása 

Most már használhatja a feltöltött VHD-t egy új virtuális gép létrehozásához. 

### <a name="set-the-uri-of-the-vhd"></a>A VHD URI-azonosítójának beállítása

A használni kívánt virtuális merevlemez URI-ja a következő formátumban van: https://**mystorageaccount**. blob.Core.Windows.net/**mycontainer** / **MyVhdName**. vhd. Ebben a példában a **myVHD** nevű virtuális merevlemez a tároló **mycontainer**található **mystorageaccount** van.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Hozza létre a [virtuális hálózat](../../virtual-network/virtual-networks-overview.md)vNet és alhálózatát.

1. Hozza létre az alhálózatot. A következő minta létrehoz egy **mySubnet** nevű alhálózatot az erőforráscsoport **myResourceGroup** a **10.0.0.0/24**előtaggal.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Hozza létre a virtuális hálózatot. Az alábbi minta létrehoz egy **myVnet** nevű virtuális hálózatot az **USA nyugati** régiójában, a **10.0.0.0/16**előtaggal.  
   
    ```powershell
    $location = "WestUS"
    $vnetName = "myVnet"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Nyilvános IP-cím és hálózati adapter létrehozása
A virtuális hálózaton a virtuális géppel való kommunikáció biztosításához egy [nyilvános IP-cím](../../virtual-network/public-ip-addresses.md) és egy hálózati adapter szükséges.

1. Hozzon létre egy nyilvános IP-címet. Ez a példa egy **myPip**nevű nyilvános IP-címet hoz létre. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Hozza létre a hálózati adaptert. Ez a példa egy **myNic**nevű hálózati adaptert hoz létre. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>A hálózati biztonsági csoport és egy RDP-szabály létrehozása
Ahhoz, hogy az RDP használatával be tudja jelentkezni a virtuális gépre, rendelkeznie kell egy olyan biztonsági szabállyal, amely engedélyezi az RDP-hozzáférést a 3389-es porton. 

Ez a példa egy **myNsg** nevű NSG hoz létre, amely egy **myRdpRule** nevű szabályt tartalmaz, amely engedélyezi az RDP-forgalmat az 3389-as porton keresztül. További információ a NSG: [portok megnyitása virtuális gépen az Azure-ban a PowerShell használatával](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

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
Hozzon létre egy változót a befejezett virtuális hálózat számára. 

```powershell
$vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>A virtuális gép létrehozása
A következő PowerShell-szkript bemutatja, hogyan állíthatja be a virtuálisgép-konfigurációkat, és hogyan használhatja a feltöltött virtuális gép rendszerképét az új telepítés forrásaként.



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

## <a name="verify-that-the-vm-was-created"></a>Annak ellenőrzése, hogy a virtuális gép létrejött-e
Ha elkészült, az újonnan létrehozott virtuális gépet a virtuális gépek **böngészése**vagy a következő PowerShell-parancsok használatával tekintheti meg a [Azure Portalban](https://portal.azure.com)  >  **Virtual machines**:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>További lépések
Az új virtuális gép Azure PowerShellval való kezeléséhez lásd: [virtuális gépek kezelése Azure Resource Manager és PowerShell használatával](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
