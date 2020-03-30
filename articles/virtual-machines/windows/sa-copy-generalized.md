---
title: Általános gép nem felügyelt lemezképének létrehozása az Azure-ban
description: Hozzon létre egy nem manged lemezképet egy általános windowsos virtuális gépről, amelynek segítségével több példányt hozhat létre egy virtuális gépről az Azure-ban.
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
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: f25968fb74f0f10b1d498866c036dd04d4d5d134
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073382"
---
# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>Nem felügyelt virtuálisgép-lemezkép létrehozása Azure-beli virtuális gépről

Ez a cikk a tárfiókok használatával foglalkozik. Azt javasoljuk, hogy a felügyelt lemezek és a felügyelt lemezképek használata a tárfiók helyett. További információ: [Általános gép felügyelt rendszerképének rögzítése az Azure-ban.](capture-image-resource.md)

Ez a cikk bemutatja, hogyan azure PowerShell használatával hozzon létre egy általános Azure-beli virtuális gép egy tárfiók használatával. Ezután a lemezkép segítségével hozzon létre egy másik virtuális gép. A lemezkép tartalmazza az operációs rendszer lemezét és a virtuális géphez csatlakoztatott adatlemezeket. A rendszerkép nem tartalmazza a virtuális hálózati erőforrásokat, ezért ezeket az erőforrásokat az új virtuális gép létrehozásakor kell beállítania. 

 

## <a name="generalize-the-vm"></a>A virtuális gép általánosítása 
Ez a szakasz bemutatja, hogyan általánosíthatja a Windows virtuális gép képként való használatát. A virtuális gép általánosítása eltávolítja az összes személyes fiókadatait, többek között, és előkészíti a gépet, hogy képként használható legyen. További információ a Sysprepről: [A Sysprep használata: Bevezetés](https://technet.microsoft.com/library/bb457073.aspx).

Győződjön meg arról, hogy a sysprep támogatja a számítógépen futó kiszolgálói szerepköröket. További információ: [Sysprep support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Ha először tölti fel a virtuális merevlemezt az Azure-ba, a Sysprep futtatása előtt [készítse el a virtuális gépét.](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 
> 
> 

Általánosíthatja a Linux virtuális `sudo waagent -deprovision+user` gép használatával, majd a PowerShell segítségével a virtuális gép rögzítéséhez. A virtuális gép cli-jének használatával kapcsolatos további tudnivalókért olvassa el [A Linux-alapú virtuális gépek általánosítása és rögzítése az Azure CLI használatával című témakört.](../linux/capture-image.md)


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

## <a name="log-in-to-azure-powershell"></a>Bejelentkezés az Azure PowerShellbe
1. Nyissa meg az Azure PowerShellt, és jelentkezzen be Azure-fiókjába.
   
    ```powershell
    Connect-AzAccount
    ```
   
    Megnyílik egy előugró ablak, amelyen megadhatja az Azure-fiók hitelesítő adatait.
2. Az elérhető előfizetések előfizetési azonosítóinak beszerzése.
   
    ```powershell
    Get-AzSubscription
    ```
3. Állítsa be a megfelelő előfizetést az előfizetés-azonosító használatával.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>A virtuális gép felszabadítása és az állapot általánossá tétele

> [!IMPORTANT] 
> Nem adhat hozzá, nem szerkeszthet és távolíthat el címkéket a virtuális gépekről, miután általánosként van megjelölve. Ha szeretne hozzáadni egy címkét a virtuális géphez, győződjön meg róla, hogy adja hozzá a címkéket, mielőtt megjelöli azt általános.
> 

1. A virtuális gép erőforrásainak felszabadítása.
   
    ```powershell
    Stop-AzVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    A virtuális gép *állapota* az Azure Portalon **leállítva** **(felszabadított)** állapotról változik.
2. Állítsa a virtuális gép állapotát **Generalized**állásba. 
   
    ```powershell
    Set-AzVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Ellenőrizze a virtuális gép állapotát. A virtuális gép **OSState/generalizált** szakaszának a **DisplayStatus beállítást** **a Virtuálisgép általánossá**kell tenni.  
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>A rendszerkép létrehozása

Ezzel a paranccsal hozzon létre egy nem felügyelt virtuálisgép-lemezképet a céltárolóban. A rendszerkép ugyanabban a tárfiókban jön létre, mint az eredeti virtuális gép. A `-Path` paraméter menti egy példányát a JSON sablon a forrás virtuális gép a helyi számítógépre. A `-DestinationContainerName` paraméter annak a tárolónak a neve, amelyet a lemezképek tárolására kíván tárolni. Ha a tároló nem létezik, az Ön számára jön létre.
   
```powershell
Save-AzVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
A kép URL-címét a JSON-fájlsablonból szerezheti be. Nyissa meg az erőforrások > **uri**  > **storageProfile** > **osDisk** > **image**uri szakasza a teljes elérési utat a kép. **resources** A kép URL-címe `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`így néz ki: .
   
Az URI-t a portálon is ellenőrizheti. A rendszerképet a rendszer a tárfiókban egy **rendszer** nevű tárolóba másolja. 

## <a name="create-a-vm-from-the-image"></a>Virtuális gép létrehozása a rendszerképből

Most már létrehozhat egy vagy több virtuális gépet a nem felügyelt lemezképből.

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
    $location = "West US"
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
A következő PowerShell befejezi a virtuális gép konfigurációit, és nem felügyelt lemezképet használ az új telepítés forrásaként.

</br>

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

### <a name="verify-that-the-vm-was-created"></a>A virtuális gép létrehozásának ellenőrzése
Ha elkészült, meg kell jelennie az újonnan létrehozott virtuális gép az [Azure Portalon](https://portal.azure.com) a**Virtuális gépek** **tallózása** > csoportban, vagy a következő PowerShell-parancsok használatával:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>További lépések
Az új virtuális gép Azure PowerShell használatával történő kezeléséhez olvassa el [a Virtuális gépek kezelése az Azure Resource Manager és a PowerShell használatával.](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


