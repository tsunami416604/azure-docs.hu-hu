---
title: Egy generalize több virtuális gép létrehozása az Azure-ban VHD feltöltése |} A Microsoft Docs
description: Általános VHD feltöltése az Azure storage-fiók használata a Resource Manager üzemi modell Windows virtuális gép létrehozásához.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: cd81eb3d62332bba67f0056a6f7b49279bc50c4f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59788812"
---
# <a name="upload-a-generalized-vhd-to-azure-to-create-a-new-vm"></a>Általános VHD feltöltése az új virtuális gép létrehozása Azure-bA

Ez a témakör ismerteti az egy általánosított nem felügyelt lemez feltöltése a storage-fiók, és majd létrehoz egy új virtuális Gépet a feltöltött lemez használatával. Általános VHD rendszerképek esetében minden személyes fiókadatot a Sysprep segítségével távolítja el. 

Ha szeretne egy virtuális gép létrehozása speciális virtuális merevlemezből egy tárfiókban, [virtuális gép létrehozása speciális virtuális merevlemezből](sa-create-vm-specialized.md).

Ez a témakör ismerteti a storage-fiókot használni, de javasoljuk, hogy ügyfeleink áthelyezése a Managed Disks használata esetén inkább. Bemutatja, hogyan készítheti elő, töltse fel, és hozzon létre egy új virtuális gép felügyelt lemezeket használ a teljes lépésenkénti útmutatóért lásd: [létrehozása általános virtuális merevlemezből új virtuális gép az Azure Managed Disks-bA feltöltött](upload-generalized-managed.md).

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="prepare-the-vm"></a>A virtuális gép előkészítése

Egy általánosított virtuális merevlemez esetében minden személyes fiókadatot a Sysprep segítségével távolítja el. Ha szeretne használni a virtuális merevlemez képként hozhat létre az új virtuális gépeket, a következőket kell elvégeznie:
  
  * [Töltse fel az Azure Windows virtuális merevlemez előkészítése](prepare-for-upload-vhd-image.md). 
  * A Sysprep használata virtuális gép általánosítása

### <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>A Sysprep használata Windows virtuális gép általánosítása
Ez a szakasz bemutatja, hogyan általánossá tétele a Windows virtuális gép lemezképként való használatra. A Sysprep többek között minden személyes fiókadatot eltávolít, a gépet pedig előkészíti rendszerképként való használatra. További információ a Sysprepről: [Sysprep használata: Bevezetés](https://technet.microsoft.com/library/bb457073.aspx).

Ellenőrizze, hogy a Sysprep a gépen futó kiszolgálói szerepkörök támogatottak. További információkért lásd: [Sysprep támogatási kiszolgálói szerepköre tekintetében](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Ha a virtuális merevlemez feltöltése az Azure-ban először előtt futtatja a Sysprep, ellenőrizze, hogy [készíteni a virtuális gép](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a Sysprep futtatása előtt. 
> 
> 

1. Jelentkezzen be a Windows virtuális gép.
2. Nyissa meg a parancsablakot rendszergazdaként. Módosítsa a könyvtárat a **%windir%\system32\sysprep**, majd futtassa a `sysprep.exe`.
3. A **Rendszer-előkészítő eszköz** párbeszédpanelen válassza **A kezdőélmény indítása** lehetőséget, és győződjön meg róla, hogy be van-e jelölve az **Általánosítás** jelölőnégyzet.
4. A **leállítási beállítások**válassza **leállítási**.
5. Kattintson az **OK** gombra.
   
    ![Indítsa el a Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. A Sysprep a feladat befejezése után leállítja a virtuális gépet. 

> [!IMPORTANT]
> Ne indítsa újra a virtuális gép mindaddig, amíg a virtuális merevlemez feltöltése az Azure-ba, vagy létrehozná a lemezképet a virtuális gépről végzett. Ha a virtuális gép véletlenül lekérdezi újraindul, futtassa a Sysprep eszközt, azt újra általánosítására.
> 
> 


## <a name="upload-the-vhd"></a>A VHD feltöltése

Töltse fel a VHD-t egy Azure storage-fiókot.

### <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Ha még nem rendelkezik a PowerShell 1.4-es verzió vagy annál újabb telepítve, olvassa el [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview).

1. Nyissa meg az Azure PowerShell-lel, és jelentkezzen be az Azure-fiókjával. Egy előugró ablak nyílik meg, hogy adja meg az Azure-fiók hitelesítő adatait.
   
    ```powershell
    Connect-AzAccount
    ```
2. Az előfizetés azonosítókat az elérhető előfizetések beolvasása.
   
    ```powershell
    Get-AzSubscription
    ```
3. Állítsa be a megfelelő előfizetés használatával az előfizetés azonosítóját. Cserélje le `<subscriptionID>` azonosítójú, a megfelelő előfizetés.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

### <a name="get-the-storage-account"></a>A storage-fiók létrehozása
Az Azure-ban tárolja a virtuális gép feltöltött kép egy tárfiókra van szükség. Használjon egy meglévő tárfiókot, vagy hozzon létre egy újat. 

A rendelkezésre álló tár fiókokat jeleníti meg, írja be:

```powershell
Get-AzStorageAccount
```

Ha egy meglévő tárfiókot használni kívánt, folytassa a feltöltés a virtuális gép rendszerkép szakaszban.

Ha szeretne hozzon létre egy tárfiókot, kövesse az alábbi lépéseket:

1. Szüksége lesz az erőforráscsoport, amelyben kell létrehozni a tárfiók nevére. Ismerje meg, hogy minden erőforráscsoport az előfizetésben található, írja be:
   
    ```powershell
    Get-AzResourceGroup
    ```

    Hozzon létre egy erőforráscsoportot a **myResourceGroup** a a **USA nyugati RÉGIÓJA** régió, írja be:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Hozzon létre egy tárfiókot, nevű **mystorageaccount** használatával az erőforráscsoport a [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) parancsmagot:
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
 
### <a name="start-the-upload"></a>A feltöltés indítása 

Használja a [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) parancsmaggal töltse fel a rendszerképet egy tárolót a tárfiókjában. Ez a példa feltölti a fájlt **myVHD.vhd** a `"C:\Users\Public\Documents\Virtual hard disks\"` tárfiókhoz nevű **mystorageaccount** a a **myResourceGroup** erőforráscsoportot. A fájlt a tárolóba nevű kerülnek **mycontainer** és az új fájl neve lesz **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
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

A hálózati kapcsolat és a VHD-fájl méretétől függően ez a parancs is igénybe vehet igénybe.


## <a name="create-a-new-vm"></a>Új virtuális gép létrehozása 

A feltöltött virtuális merevlemez használatával most hozzon létre egy új virtuális Gépet. 

### <a name="set-the-uri-of-the-vhd"></a>Állítsa be a virtuális merevlemez URI azonosítója

A virtuális merevlemez használata az URI-ja a következő formátumban: https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**.vhd. Ebben a példában a virtuális merevlemez nevű **myVHD** a storage-fiókban található **mystorageaccount** tároló **mycontainer**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Hozza létre a virtuális hálózat és alhálózat, a [virtuális hálózat](../../virtual-network/virtual-networks-overview.md).

1. Hozza létre az alhálózatot. Az alábbi minta létrehoz egy nevű alhálózatot **mySubnet** erőforráscsoportban **myResourceGroup** a cím előtagja **10.0.0.0/24**.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Hozza létre a virtuális hálózatot. Az alábbi minta létrehoz egy virtuális hálózatot nevű **myVnet** a a **USA nyugati RÉGIÓJA** helyét a címelőtagot **10.0.0.0/16**.  
   
    ```powershell
    $location = "WestUS"
    $vnetName = "myVnet"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Hozzon létre egy nyilvános IP-cím és a hálózati kapcsolat
A virtuális hálózaton a virtuális géppel való kommunikáció biztosításához egy [nyilvános IP-cím](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) és egy hálózati adapter szükséges.

1. Hozzon létre egy nyilvános IP-címet. Ez a példa létrehoz egy nyilvános IP-címet **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Hozzon létre a hálózati adaptert. Ez a példa létrehoz egy hálózati Adaptert **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>A hálózati biztonsági csoport és a egy RDP-szabály létrehozása
Az, hogy jelentkezzen be a virtuális gép RDP-vel, szüksége lesz egy biztonsági szabályt, amely lehetővé teszi a 3389-es RDP-hozzáférést. 

Ez a példa létrehoz egy NSG-t nevű **myNsg** nevű szabályt tartalmazó **myRdpRule** , amely lehetővé teszi RDP-forgalmat a 3389-es porton keresztül. NSG-kkel kapcsolatos további információkért lásd: [egy virtuális gép portjainak megnyitása az Azure PowerShell-lel](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>Hozzon létre egy változót, a virtuális hálózat
Hozzon létre egy változót a befejezett virtuális hálózatot. 

```powershell
$vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>Virtuális gép létrehozása
A következő PowerShell-parancsfájl bemutatja, hogyan állíthatja be a virtuális gépek konfigurációit, és a feltöltött Virtuálisgép-rendszerkép használata a forrásként az új telepítéshez.



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

## <a name="verify-that-the-vm-was-created"></a>Győződjön meg arról, hogy a virtuális gép létrejött-e.
Amikor végzett, megjelenik az újonnan létrehozott virtuális gép a [az Azure portal](https://portal.azure.com) alatt **Tallózás** > **virtuális gépek**, vagy a következő PowerShell-lel parancsok:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>További lépések
Az új virtuális gépet az Azure PowerShell-lel kezelhető, lásd: [virtuális gépek kezelése az Azure Resource Managerrel és Powershellel](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


