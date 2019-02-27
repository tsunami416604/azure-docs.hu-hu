---
title: Nem felügyelt rendszerkép egy általánosított virtuális gép létrehozása az Azure-ban |} A Microsoft Docs
description: Hozzon létre egy virtuális gép több példányának létrehozása az Azure-ban használandó általános Windows virtuális gép unmanged képe.
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
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: a7181c91424e1d905329eefd13f926d0b2700081
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56864974"
---
# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>Egy nem felügyelt Virtuálisgép-rendszerkép létrehozása Azure virtuális gépből

Ez a cikk ismerteti a storage-fiókot használni. Azt javasoljuk, hogy használt felügyelt lemezek és felügyelt lemezképek helyett egy storage-fiókot. További információkért lásd: [az Azure-beli általánosított virtuális gép felügyelt rendszerképének rögzítése](capture-image-resource.md).

Ez a cikk bemutatja, hogyan általánosított Azure VM-lemezkép létrehozása az Azure PowerShell használatával egy storage-fiók használatával. Ezután használhatja a kép egy másik virtuális gép létrehozásához. A rendszerkép tartalmazza az operációsrendszer-lemez és a virtuális géphez csatolt adatlemezek. A kép nem tartalmazza a virtuális hálózati erőforrásokat, úgy kell beállítani ezeket az erőforrásokat, ha az új virtuális Gépet hoz létre. 

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="generalize-the-vm"></a>A virtuális gép általánosítása 
Ez a szakasz bemutatja, hogyan általánossá tétele a Windows virtuális gép lemezképként való használatra. Virtuális gép általánosítása eltávolít minden személyes fiókadatot, többek között, és előkészíti a számítógépet, hogy képként használni. További információ a Sysprepről: [Sysprep használata: Bevezetés](https://technet.microsoft.com/library/bb457073.aspx).

Ellenőrizze, hogy a Sysprep a gépen futó kiszolgálói szerepkörök támogatottak. További információkért lásd: [Sysprep támogatási kiszolgálói szerepköre tekintetében](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Ha tölt fel a VHD-t az Azure-ban először, ellenőrizze, hogy [készíteni a virtuális gép](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a Sysprep futtatása előtt. 
> 
> 

Linux virtuális gép is általánosítása `sudo waagent -deprovision+user` és a PowerShell segítségével a virtuális gép rögzítése. További információ a parancssori felület használatával a virtuális gép rögzítésére: [generalize és az Azure parancssori felületével Linux rendszerű virtuális gép rögzítése](../linux/capture-image.md).


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

## <a name="log-in-to-azure-powershell"></a>Bejelentkezés az Azure PowerShellbe
1. Nyissa meg az Azure PowerShell-lel, és jelentkezzen be az Azure-fiókjával.
   
    ```powershell
    Connect-AzAccount
    ```
   
    Egy előugró ablak nyílik meg, hogy adja meg az Azure-fiók hitelesítő adatait.
2. Az előfizetés azonosítókat az elérhető előfizetések beolvasása.
   
    ```powershell
    Get-AzSubscription
    ```
3. Állítsa be a megfelelő előfizetés használatával az előfizetés azonosítóját.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Szabadítsa fel a virtuális Gépet, és az általános állapotának beállítása

> [!IMPORTANT] 
> Nem adja hozzá, szerkessze vagy távolítsa el az címkéket a virtuális gép után általánosként van megjelölve. Ha azt szeretné, egy címke hozzáadása a virtuális Gépen, győződjön meg arról, általánosként való megjelölése előtt adja hozzá a címkéket.
> 

1. Szabadítsa fel a virtuális gép erőforrásait.
   
    ```powershell
    Stop-AzVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    A *állapot* a virtuális gép az Azure Portalon a változik **leállítva** való **leállítva (felszabadítva)**.
2. A virtuális gép állapotának beállítása **Általánosítottra**. 
   
    ```powershell
    Set-AzVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. A virtuális gép állapotának ellenőrzéséhez. A **OSState/általánosítva** szakaszban látható a virtuális Gépet kell rendelkeznie a **DisplayStatus** beállítása **virtuális gép általánosítva**.  
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>A rendszerkép létrehozása

A cél tároló, a következő paranccsal hozzon létre egy nem felügyelt virtuálisgép-lemezkép. A kép ugyanazt a tárfiókot, az eredeti virtuális gép jön létre. A `-Path` paramétert a JSON-sablon a forrás virtuális gép másolatának mentése a helyi számítógépen. A `-DestinationContainerName` paraméter, amely a szeretné megtartani a képek a tároló neve. Ha a tároló nem létezik, akkor jön létre az Ön számára.
   
```powershell
Save-AzVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
A JSON-fájl sablonból beszerezheti a kép URL-CÍMÉT. Nyissa meg a **erőforrások** > **storageProfile** > **osDisk** > **kép**  >  **uri** szakaszban található a rendszerkép teljes elérési útja. Az URL-címét a képen láthatóhoz hasonló: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
   
Az URI-t a portálon is ellenőrizheti. A kép másolódik nevű tárolóban **rendszer** a storage-fiókban. 

## <a name="create-a-vm-from-the-image"></a>Virtuális gép létrehozása a rendszerképből

Most egy vagy több virtuális gépeket hozhat létre a nem felügyelt rendszerképből.

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
    $location = "West US"
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
Az alábbi PowerShell-lel a virtuális gép konfigurációja befejeződött, és nem felügyelt rendszerkép használja forrásként az új telepítéshez.

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

### <a name="verify-that-the-vm-was-created"></a>Győződjön meg arról, hogy a virtuális gép létrejött-e.
Amikor végzett, megjelenik az újonnan létrehozott virtuális gép a [az Azure portal](https://portal.azure.com) alatt **Tallózás** > **virtuális gépek**, vagy a következő PowerShell-lel parancsok:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>További lépések
Az új virtuális gépet az Azure PowerShell-lel kezelhető, lásd: [virtuális gépek kezelése az Azure Resource Managerrel és Powershellel](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


