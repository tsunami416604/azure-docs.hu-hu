---
title: Nem felügyelt lemezkép egy általánosított virtuális gép létrehozása az Azure-ban |} Microsoft Docs
description: Hozzon létre egy általános windowsos virtuális gép több másolatot a virtuális gépek létrehozása az Azure-ban unmanged képe.
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
ms.openlocfilehash: b416acd9a2a3b03502b7eca11eade9dbd56f3afe
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>Egy nem felügyelt Virtuálisgép-lemezkép az Azure virtuális gép létrehozása

Ez a cikk ismerteti a storage-fiókok használatával. Javasoljuk, hogy használjon felügyelt lemezek és a felügyelt képek helyett egy tárfiókot. További információkért lásd: [egy általánosított virtuális Gépet az Azure-ban a felügyelt lemezképének](capture-image-resource.md).

Ez a cikk bemutatja, hogyan egy általánosított Azure VM-lemezkép létrehozása az Azure PowerShell használatával történő tárfiókot használ. A lemezkép segítségével majd hozzon létre egy másik virtuális Gépet. A lemezkép az operációs rendszer lemezének és az adatlemezek a virtuális géphez csatolt tartalmaz. A kép nem tartalmaz virtuális hálózati erőforrások, úgy kell beállítani ezeket az erőforrásokat, ha az új virtuális gép létrehozása. 

## <a name="prerequisites"></a>Előfeltételek
Szüksége van az Azure PowerShell-verzió 1.0.x vagy újabb verzióját telepítették. Ha még nem telepítette a PowerShell, olvassa el [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview) telepítési lépéseit.

## <a name="generalize-the-vm"></a>A virtuális gép generalize 
Ez a szakasz bemutatja, hogyan általánosítja a Windows rendszerű virtuális gép képként használatra. A virtuális gépek normalizálása eltávolítja a személyes adatok, többek között, és előkészíti a számítógépet, hogy képként használni. A Sysprep kapcsolatos részletekért lásd: [hogyan használja a Sysprep: Bevezetés](http://technet.microsoft.com/library/bb457073.aspx).

Győződjön meg arról, hogy a Sysprep által a gépen futó kiszolgálói szerepkörök támogatottak. További információkért lásd: [Sysprep támogatási kiszolgálói szerepköre tekintetében](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Ha a VHD-t az Azure-bA először, győződjön meg arról, hogy [a virtuális gép előkészített](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a Sysprep futtatása előtt. 
> 
> 

Linux virtuális gépet is általánosítása `sudo waagent -deprovision+user` és a PowerShell segítségével a virtuális gép rögzítése. A virtuális gép rögzítése a parancssori felület használatával kapcsolatos információkért lásd: [generalize és az Azure parancssori felület használatával Linux virtuális gép rögzítése ](../linux/capture-image.md).


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

## <a name="log-in-to-azure-powershell"></a>Bejelentkezés az Azure PowerShellbe
1. Nyissa meg az Azure PowerShell, és jelentkezzen be az Azure-fiókjával.
   
    ```powershell
    Connect-AzureRmAccount
    ```
   
    Egy előugró ablak nyílik meg Azure-fiók hitelesítő adatait.
2. Az előfizetési azonosítók az elérhető előfizetések beolvasása.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Állítsa be a megfelelő előfizetés az előfizetés-azonosítójával.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>A virtuális gép felszabadítása és általános állapotának beállítása

> [!IMPORTANT] 
> Nem hozzáadása, szerkesztése, illetve címkék eltávolítása a virtuális gépek után van megjelölve általánosítva van. Ha szeretne egy címke hozzáadása a virtuális Gépet, győződjön meg arról, általánosítva való megjelölése előtt adja hozzá a címkéket.
> 

1. A Virtuálisgép-erőforrások felszabadítani.
   
    ```powershell
    Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    A *állapot* a virtuális gép az Azure portálon módosítja **leállítva** való **leállítva (felszabadítva)**.
2. A virtuális gép állapotának beállítása **Generalized**. 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. A virtuális gép állapotának ellenőrzése. A **OSState/általánosítva** szakaszban, a virtuális gép kell rendelkeznie a **DisplayStatus** beállítása **virtuális gép általánosítva**.  
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>A rendszerkép létrehozása

Hozzon létre egy nem felügyelt virtuálisgép-lemezkép a cél tárolót használja a következő parancsot. A kép ugyanazt a tárfiókot az eredeti virtuális gépként jön létre. A `-Path` paraméter a forrás virtuális gép a JSON-sablon másolatának mentése a helyi számítógépen. A `-DestinationContainerName` paraméter megadása a tároló, a lemezképek tárolásához használni kívánt nevét. Ha a tároló nem létezik, az Ön létrejön.
   
```powershell
Save-AzureRmVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
A JSON-fájl sablon lekérheti a kép URL-CÍMÉT. Lépjen a **erőforrások** > **storageProfile** > **osDisk** > **kép** > **uri** a lemezkép szakasza a teljes elérési útja. A kép URL-címe a következőhöz hasonló: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
   
Azt is ellenőrizheti, hogy az URI a portálon. A kép másolódik nevű tárolót **rendszer** tárfiókba. 

## <a name="create-a-vm-from-the-image"></a>Hozzon létre egy virtuális Gépet a lemezképből

Most egy vagy több virtuális gépek is létrehozhat a nem felügyelt lemezképből.

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
A következő PowerShell befejeződött a virtuális gépek konfigurációit, és nem felügyelt lemezképet használja forrásként az új telepítés.

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

### <a name="verify-that-the-vm-was-created"></a>Győződjön meg arról, hogy létrejött-e a virtuális gép
Amikor végzett, megjelenik az újonnan létrehozott virtuális gép a [Azure-portálon](https://portal.azure.com) alatt **Tallózás** > **virtuális gépek**, vagy a következő PowerShell-parancsok használatával:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>További lépések
Az új virtuális gépet az Azure PowerShell kezeléséhez, tekintse meg a [kezelése az Azure Resource Manager és a PowerShell használatával virtuális gépek](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


