---
title: Töltse fel a VHD-fájlt több virtuális gép létrehozása az Azure-ban generalize |} Microsoft Docs
description: Töltse fel a Resource Manager üzembe helyezési modellben használandó Windows virtuális gép létrehozása Azure storage-fiók egy általánosított virtuális Merevlemezt.
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
ms.openlocfilehash: be2ec6df33f5756dc080195bfad32e0c9079453c
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="upload-a-generalized-vhd-to-azure-to-create-a-new-vm"></a>Egy általánosított virtuális Merevlemezt feltöltése az Azure-bA egy új virtuális gép létrehozása

Ez a témakör egy általánosított nem felügyelt lemezt feltöltése a tárfiókba, és majd létrehoz egy új virtuális Gépet, a feltöltött lemez használatával. Egy általánosított virtuális Merevlemezt lemezkép volt-e az összes személyes fiókadatait a Sysprep segítségével távolítja el. 

Ha szeretne létrehozni egy virtuális gép olyan virtuális merevlemezről speciális tárfiókokban, [hozzon létre egy virtuális Gépet egy speciális virtuális merevlemezről](sa-create-vm-specialized.md).

Ez a témakör ismerteti a storage-fiókok használatával, de ügyfelek áthelyezés felügyelt lemezek használata ajánlott. Teljes segédlet előkészítése, töltse fel, és hozzon létre egy új virtuális Gépet felügyelt lemezt használ, tekintse meg [hozzon létre egy új virtuális Gépet egy általánosított virtuális Merevlemezt a feltöltött Azure-ban kezelt lemezek](upload-generalized-managed.md).



## <a name="prepare-the-vm"></a>A virtuális gép előkészítése

Egy általánosított virtuális Merevlemezt volt-e az összes személyes fiókadatait a Sysprep segítségével távolítja el. Ha azt tervezi, a virtuális merevlemez használata képként az új virtuális gépek létrehozásához, a következőket:
  
  * [Az Azure-bA feltölteni egy Windows virtuális merevlemez előkészítése](prepare-for-upload-vhd-image.md). 
  * A virtuális gépet a Sysprep használatával generalize

### <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>A Windows virtuális gépek Sysprep generalize
Ez a szakasz bemutatja, hogyan általánosítja a Windows rendszerű virtuális gép képként használatra. A Sysprep eltávolítja a személyes adatok, többek között, és előkészíti a számítógépet, hogy képként használni. A Sysprep kapcsolatos részletekért lásd: [hogyan használja a Sysprep: Bevezetés](http://technet.microsoft.com/library/bb457073.aspx).

Győződjön meg arról, hogy a Sysprep által a gépen futó kiszolgálói szerepkörök támogatottak. További információkért lásd: [Sysprep támogatási kiszolgálói szerepköre tekintetében](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Ha előtt a virtuális merevlemez feltöltése az Azure-bA az első alkalommal futtatja a Sysprep, győződjön meg arról, hogy [a virtuális gép előkészített](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a Sysprep futtatása előtt. 
> 
> 

1. Jelentkezzen be a Windows rendszerű virtuális gép.
2. Nyissa meg a parancssort rendszergazdaként. Lépjen be **%windir%\system32\sysprep**, majd futtassa a `sysprep.exe`.
3. Az a **rendszer-előkészítő eszköz** párbeszédpanelen jelölje ki **adja meg a rendszer Out-of-Box élmény (OOBE)**, és győződjön meg arról, hogy a **Generalize** jelölőnégyzet be van jelölve.
4. A **leállítási beállítások**, jelölje be **leállítási**.
5. Kattintson az **OK** gombra.
   
    ![Indítsa el a Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. A Sysprep befejezését követően a virtuális gép leáll. 

> [!IMPORTANT]
> Ne indítsa újra a virtuális gép, amíg elkészült, a virtuális merevlemez feltöltése az Azure-bA vagy lemezkép létrehozása a virtuális gépről. Ha a virtuális gép véletlenül lekérdezi újra, futtassa a Sysprep általánosítja azt újra.
> 
> 


## <a name="upload-the-vhd"></a>A virtuális merevlemez feltöltéséhez

A VHD-fájlt feltölti egy Azure storage-fiókot.

### <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Ha még nem rendelkezik PowerShell 1.4-es verzió vagy újabb operációs rendszerre telepíteni, olvassa el [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview).

1. Nyissa meg az Azure PowerShell, és jelentkezzen be az Azure-fiókjával. Egy előugró ablak nyílik meg Azure-fiók hitelesítő adatait.
   
    ```powershell
    Connect-AzureRmAccount
    ```
2. Az előfizetési azonosítók az elérhető előfizetések beolvasása.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Állítsa be a megfelelő előfizetés az előfizetés-azonosítójával. Cserélje le `<subscriptionID>` , azonosító: a megfelelő előfizetés.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

### <a name="get-the-storage-account"></a>A storage-fiók beszerzése
Kell egy a feltöltött Virtuálisgép-lemezkép tárolásához Azure storage-fiókot. Meglévő tárfiók használata, vagy hozzon létre egy újat. 

A rendelkezésre álló tárfiókok megjelenítéséhez írja be:

```powershell
Get-AzureRmStorageAccount
```

Ha egy meglévő tárfiókot használni szeretne, ugorjon a [a Virtuálisgép-lemezkép feltöltése](#upload-the-vm-vhd-to-your-storage-account) szakasz.

Ha létrehoz egy tárfiókot van szüksége, kövesse az alábbi lépéseket:

1. Az erőforráscsoport, ahol kell létrehozni a tárfiók neve van szüksége. Tudja meg, amelyek az előfizetésében szereplő összes erőforráscsoport, írja be:
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Nevű erőforráscsoport létrehozásához **myResourceGroup** a a **USA nyugati régiója** régió, írja be:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Hozzon létre egy tárfiókot, nevű **mystorageaccount** Ez az erőforráscsoport segítségével a a [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) parancsmagot:
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
 
### <a name="start-the-upload"></a>Feltöltés indításához 

Használja a [Add-AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd) parancsmag segítségével feltölti a lemezképet a tárfiókban lévő tárolót. Ez a példa feltölti **myVHD.vhd** a `"C:\Users\Public\Documents\Virtual hard disks\"` tárfiókba nevű **mystorageaccount** a a **myResourceGroup** erőforráscsoportot. A fájl bekerülnek a nevű tárolót **mycontainer** és az új fájlnevet lesz **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
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

Attól függően, hogy a hálózati kapcsolat és a VHD-fájl mérete a parancs is igénybe vehet igénybe.


## <a name="create-a-new-vm"></a>Új virtuális gép létrehozása 

A feltöltött virtuális merevlemez segítségével most hozzon létre egy új virtuális Gépet. 

### <a name="set-the-uri-of-the-vhd"></a>A virtuális merevlemez URI beállítása

URI-JÁNAK a VHD-fájlt használjon a formátumban kell megadni: https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**.vhd. Ebben a példában a virtuális merevlemez nevű **myVHD** szerepel a tárfiók **mystorageaccount** tárolóban **mycontainer**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Hozza létre a virtuális hálózat és az alhálózati a [virtuális hálózati](../../virtual-network/virtual-networks-overview.md).

1. Hozza létre az alhálózatot. Az alábbi minta létrehoz egy nevű alhálózat **mySubnet** erőforráscsoportban **myResourceGroup** a címelőtagot rendelkező **10.0.0.0/24**.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Hozza létre a virtuális hálózatot. Az alábbi minta létrehoz egy virtuális hálózati nevű **myVnet** a a **USA nyugati régiója** hellyel, amely a címelőtagot **10.0.0.0/16**.  
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Hozzon létre egy nyilvános IP-cím és a hálózati kapcsolat
A virtuális hálózaton a virtuális géppel való kommunikáció biztosításához egy [nyilvános IP-cím](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) és egy hálózati adapter szükséges.

1. Hozzon létre egy nyilvános IP-címet. Ez a példa létrehoz egy nyilvános IP-cím nevű **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Hozzon létre a hálózati adaptert. Ez a példa létrehoz egy hálózati adapter nevű **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>A hálózati biztonsági csoport és egy RDP-szabály létrehozása
Nem fogja tudni jelentkezzen be a virtuális gép RDP Funkciót használnak, olyan biztonsági szabályt, amely lehetővé teszi a hozzáférést RDP a 3389-es porton kell. 

Ez a példa létrehoz egy NSG nevű **myNsg** nevű szabályt tartalmaz, amelyek **myRdpRule** RDP-forgalmát, amely engedélyezi a 3389-es porton keresztül. Az NSG-k kapcsolatos további információkért lásd: [az Azure PowerShell használatával egy virtuális gép portok megnyitása](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>A virtuális hálózat változó létrehozása
Hozzon létre egy változót, a befejezett virtuális hálózat. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>Virtuális gép létrehozása
A következő PowerShell-parancsfájl bemutatja, hogyan állítsa be a virtuális gépek konfigurációit, és a feltöltött Virtuálisgép-lemezkép az új telepítés számára a forrásként használt.



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
    $storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

    # Set the VM name and size
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows

    # Create the new VM
    New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>Győződjön meg arról, hogy létrejött-e a virtuális gép
Amikor végzett, megjelenik az újonnan létrehozott virtuális gép a [Azure-portálon](https://portal.azure.com) alatt **Tallózás** > **virtuális gépek**, vagy a következő PowerShell-parancsok használatával:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>További lépések
Az új virtuális gépet az Azure PowerShell kezeléséhez, tekintse meg a [kezelése az Azure Resource Manager és a PowerShell használatával virtuális gépek](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


