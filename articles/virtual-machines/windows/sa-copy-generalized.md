---
title: Általánosított virtuális gép nem felügyelt rendszerképének létrehozása az Azure-ban | Microsoft Docs
description: Hozzon létre egy általánosított Windows-alapú virtuális gép egy nem összefoglalt képét, amely egy virtuális gép több példányának létrehozásához használható az Azure-ban.
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
ms.openlocfilehash: 45c59ccdd45a0c00635c3e0a3919248f33e2919a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102450"
---
# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>Nem felügyelt virtuális gép rendszerképének létrehozása Azure-beli virtuális gépről

Ez a cikk a Storage-fiókok használatát ismerteti. Azt javasoljuk, hogy Storage-fiók helyett felügyelt lemezeket és felügyelt lemezképeket használjon. További információ: általánosított [virtuális gép felügyelt rendszerképének rögzítése az Azure-ban](capture-image-resource.md).

Ez a cikk bemutatja, hogyan hozhat létre egy általános Azure-beli virtuális gép rendszerképét egy Storage-fiók használatával a Azure PowerShell használatával. Ezután egy másik virtuális gép létrehozására használhatja a rendszerképet. A rendszerkép tartalmazza az operációsrendszer-lemezt és a virtuális géphez csatolt adatlemezeket. A rendszerkép nem tartalmazza a virtuális hálózati erőforrásokat, ezért be kell állítania ezeket az erőforrásokat az új virtuális gép létrehozásakor. 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="generalize-the-vm"></a>A virtuális gép általánosítása 
Ez a szakasz bemutatja, hogyan általánosíthatja a Windows rendszerű virtuális gépet képként való használatra. A virtuális gépek általánosítása eltávolítja az összes személyes fiók adatait, többek között, és előkészíti a gépet képként való használatra. Ha további információra van szüksége a Sysprepről, tekintse meg [A Sysprep használata: Bevezetés](https://technet.microsoft.com/library/bb457073.aspx).

Győződjön meg arról, hogy a Sysprep támogatja a számítógépen futó kiszolgálói szerepköröket. További információ: a [Sysprep-támogatás a kiszolgálói szerepkörökhöz](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Ha első alkalommal tölti fel a VHD-t az Azure-ba, győződjön meg arról, hogy előkészítette [a virtuális gépet](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a Sysprep futtatása előtt. 
> 
> 

A Linux rendszerű virtuális gépeket a használatával `sudo waagent -deprovision+user` is általánosíthatja, majd a PowerShell használatával rögzítheti a virtuális gépet. További információ a virtuális gép rögzítéséről a CLI használatával: Linux rendszerű [virtuális gép általánosítása és rögzítése az Azure CLI használatával](../linux/capture-image.md).


1. Jelentkezzen be a Windows rendszerű virtuális gépre.
2. Nyissa meg a parancsablakot rendszergazdaként. Módosítsa a könyvtárat a **%WINDIR%\system32\sysprep**értékre, majd `sysprep.exe`futtassa a parancsot.
3. A **Rendszer-előkészítő eszköz** párbeszédpanelen válassza **A kezdőélmény indítása** lehetőséget, és győződjön meg róla, hogy be van-e jelölve az **Általánosítás** jelölőnégyzet.
4. A **leállítási beállítások**területen válassza a **Leállítás**lehetőséget.
5. Kattintson az **OK** gombra.
   
    ![A Sysprep elindítása](./media/upload-generalized-managed/sysprepgeneral.png)
6. A Sysprep a feladat befejezése után leállítja a virtuális gépet. 

> [!IMPORTANT]
> Ne indítsa újra a virtuális gépet, amíg el nem végzi a virtuális merevlemez feltöltését az Azure-ba, vagy rendszerkép létrehozása a virtuális gépről. Ha a virtuális gép újraindítása véletlenül megtörténik, futtassa újra a Sysprep eszközt, hogy újra általánosítsa.
> 
> 

## <a name="log-in-to-azure-powershell"></a>Bejelentkezés az Azure PowerShellbe
1. Nyissa meg Azure PowerShell, és jelentkezzen be az Azure-fiókjába.
   
    ```powershell
    Connect-AzAccount
    ```
   
    Megnyílik egy előugró ablak, amelyen megadhatja az Azure-fiókja hitelesítő adatait.
2. Szerezze be a rendelkezésre álló előfizetések előfizetési azonosítóit.
   
    ```powershell
    Get-AzSubscription
    ```
3. Állítsa be a megfelelő előfizetést az előfizetés-azonosító használatával.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>A virtuális gép felszabadítása és az állapot beállítása általánosított értékre

> [!IMPORTANT] 
> A virtuális gépről nem adhat hozzá, nem szerkeszthet és nem távolíthat el címkéket, ha az általánosítva értékre van megjelölve. Ha címkét szeretne felvenni a virtuális gépre, ügyeljen rá, hogy az általánosított jelölés előtt adja hozzá a címkéket.
> 

1. Felszabadítsa a virtuális gépek erőforrásait.
   
    ```powershell
    Stop-AzVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    A Azure Portalban a virtuális gép állapota leállítva ( leállítva **)** állapotra módosult.
2. Állítsa a virtuális gép állapotát **általánosítva**értékre. 
   
    ```powershell
    Set-AzVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. A virtuális gép állapotának ellenőrzését. A virtuális gép **OSState/általánosított** szakaszának a **virtuális gép általánosított** **DisplayStatus** kell lennie.  
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>A rendszerkép létrehozása

Hozzon létre egy nem felügyelt virtuálisgép-rendszerképet a cél Storage-tárolóban ezzel a paranccsal. A rendszerkép ugyanabban a Storage-fiókban jön létre, mint az eredeti virtuális gép. A `-Path` paraméter a forrásként szolgáló virtuális gép JSON-sablonjának másolatát menti a helyi számítógépre. A `-DestinationContainerName` paraméter annak a tárolónak a neve, amelynek a képeit tárolni szeretné. Ha a tároló nem létezik, a rendszer létrehozza az Ön számára.
   
```powershell
Save-AzVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
A rendszerkép URL-címét a JSON-sablonfájl használatával szerezheti be. A rendszerkép teljes > elérési útjához nyissa meg a Resources**storageProfile** > **osDisk** > -**rendszerkép** > **URI** szakaszát. A rendszerkép URL-címe a következőképpen néz `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`ki:.
   
Az URI-t a portálon is ellenőrizheti. A rendszer átmásolja a rendszerképet egy **System** nevű tárolóba a Storage-fiókban. 

## <a name="create-a-vm-from-the-image"></a>Virtuális gép létrehozása a rendszerképből

Most létrehozhat egy vagy több virtuális gépet a nem felügyelt rendszerképből.

### <a name="set-the-uri-of-the-vhd"></a>A VHD URI-azonosítójának beállítása

A használni kívánt virtuális merevlemez URI-ja a következő formátumban van: https://**mystorageaccount**. blob.Core.Windows.net/**mycontainer**/**MyVhdName**. vhd. Ebben a példában a **myVHD** nevű virtuális merevlemez a tároló **mycontainer**található **mystorageaccount** van.

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
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Nyilvános IP-cím és hálózati adapter létrehozása
A virtuális hálózaton a virtuális géppel való kommunikáció biztosításához egy [nyilvános IP-cím](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) és egy hálózati adapter szükséges.

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

### <a name="create-the-vm"></a>Virtuális gép létrehozása
A következő PowerShell befejezi a virtuálisgép-konfigurációkat, és a nem felügyelt rendszerképet használja az új telepítés forrásaként.

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

### <a name="verify-that-the-vm-was-created"></a>Annak ellenőrzése, hogy a virtuális gép létrejött-e
Ha elkészült, az újonnan létrehozott virtuális gépet a**virtuális gépek** **böngészése** > vagy a következő PowerShell-parancsok használatával tekintheti meg a [Azure Portalban](https://portal.azure.com) :

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>További lépések
Az új virtuális gép Azure PowerShellval való kezeléséhez lásd: [virtuális gépek kezelése Azure Resource Manager és PowerShell használatával](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


