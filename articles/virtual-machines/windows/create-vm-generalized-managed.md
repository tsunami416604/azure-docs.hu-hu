---
title: "Virtuális gép létrehozása az Azure-ban kezelt lemezképéről |} Microsoft Docs"
description: "Windows virtuális gép létrehozása Azure PowerShell vagy az Azure-portál használatával a Resource Manager üzembe helyezési modellel általánosított felügyelt lemezképéről."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: cynthn
ms.openlocfilehash: d8986c71fd0300af385750af898d620e6d3e1f24
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2017
---
# <a name="create-a-vm-from-a-managed-image"></a>Egy felügyelt képre a virtuális gép létrehozása

A PowerShell vagy az Azure-portálon kezelt Virtuálisgép-lemezkép létrehozhat több virtuális géphez. Felügyelt Virtuálisgép-lemezképet a virtuális gépek, beleértve az operációsrendszer- és adatlemezek létrehozásához szükséges információkat tartalmazza. A virtuális merevlemezeket jött létre a lemezképet, beleértve az operációsrendszer-lemezek és adatok lemezzel, felügyelt lemezként tárolja. 

Már van szükség [felügyelt Virtuálisgép-lemezkép létrehozott](capture-image-resource.md) használandó új virtuális gép létrehozása. 

## <a name="use-the-portal"></a>A portál használata

1. Nyissa meg az [Azure portált](https://portal.azure.com).
2. A bal oldali menüben válassza ki a **összes erőforrás**. Rendezheti az erőforrásokat a **típus** könnyen megtalálhatják a képeket.
3. Válassza ki a listából a használni kívánt lemezkép. A kép **áttekintése** lap megnyitásakor.
4. Kattintson a **+ létrehozás VM** a menüből.
5. Adja meg a virtuális gép adatait. Az itt megadott felhasználónévvel és jelszóval bejelentkezhet a virtuális gépbe. Amikor végzett, kattintson az **OK** gombra. Az új virtuális gép létrehozása egy meglévő Resrouce csoportba, vagy válasszon **hozzon létre új** tárolja a virtuális gép új erőforráscsoport létrehozásához.
6. Válasszon méretet a virtuális gép számára. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a **Támogatott lemeztípus** szűrőt. 
7. A **beállítások**, módosítsa szükség szerint, majd kattintson a **OK**. 
8. Az összefoglalás lapon kell megjelennie a lemezkép neve felsorolt **titkos kép**. Kattintson a **Ok** elindítani a virtuális gépek telepítése során.


## <a name="use-powershell"></a>A PowerShell használata

### <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy rendelkezik-e a AzureRM.Compute és AzureRM.Network PowerShell modulok legújabb verzióját. Nyissa meg rendszergazdaként egy PowerShell-parancssorba, és futtassa a következő parancsot, hogy telepítse őket.

```azurepowershell-interactive
Install-Module AzureRM.Compute,AzureRM.Network
```
További információkért lásd: [Azure PowerShell Versioning](/powershell/azure/overview).



### <a name="collect-information-about-the-image"></a>A kép kapcsolatos információk gyűjtése

Először igazolnia kell a lemezkép alapvető információkat gyűjt, és hozzon létre egy változót, a lemezkép. Ez a példa egy felügyelt Virtuálisgép-lemezkép nevű **myImage** , hogy a **myResourceGroup** erőforráscsoportja a **nyugati középső Régiójában** helyét. 

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "West Central US"
$imageName = "myImage"
$image = Get-AzureRMImage -ImageName $imageName -ResourceGroupName $rgName
```

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Hozza létre a virtuális hálózat és az alhálózati a [virtuális hálózati](../../virtual-network/virtual-networks-overview.md).

Hozza létre az alhálózatot. Ez a példa-alhálózatot hoz létre nevű **mySubnet** a címelőtagot rendelkező **10.0.0.0/24**.  
   
```azurepowershell-interactive
$subnetName = "mySubnet"
$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig `
    -Name $subnetName -AddressPrefix 10.0.0.0/24
```

Hozza létre a virtuális hálózatot. Ebben a példában egy virtuális hálózatot nevű hoz létre **myVnet** a címelőtagot rendelkező **10.0.0.0/16**.  
   
```azurepowershell-interactive
$vnetName = "myVnet"
$vnet = New-AzureRmVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $rgName `
    -Location $location `
    -AddressPrefix 10.0.0.0/16 `
    -Subnet $singleSubnet
```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Hozzon létre egy nyilvános IP-cím és a hálózati kapcsolat

A virtuális hálózaton a virtuális géppel való kommunikáció biztosításához egy [nyilvános IP-cím](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) és egy hálózati adapter szükséges.

Hozzon létre egy nyilvános IP-címet. Ez a példa létrehoz egy nyilvános IP-cím nevű **myPip**. 
   
```azurepowershell-interactive
$ipName = "myPip"
$pip = New-AzureRmPublicIpAddress `
    -Name $ipName `
    -ResourceGroupName $rgName `
    -Location $location `
    -AllocationMethod Dynamic
```
       
Hozzon létre a hálózati adaptert. Ez a példa létrehoz egy hálózati adapter nevű **myNic**. 
   
```azurepowershell-interactive
$nicName = "myNic"
$nic = New-AzureRmNetworkInterface `
    -Name $nicName `
    -ResourceGroupName $rgName `
    -Location $location `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $pip.Id
```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>A hálózati biztonsági csoport és egy RDP-szabály létrehozása

Nem fogja tudni jelentkezzen be a virtuális gép RDP Funkciót használnak, meg kell rendelkeznie a hálózati biztonsági szabály (NSG), amely lehetővé teszi a hozzáférést RDP a 3389-es porton. 

Ez a példa létrehoz egy NSG nevű **myNsg** nevű szabályt tartalmaz, amelyek **myRdpRule** RDP-forgalmát, amely engedélyezi a 3389-es porton keresztül. Az NSG-k kapcsolatos további információkért lásd: [az Azure PowerShell használatával egy virtuális gép portok megnyitása](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```azurepowershell-interactive
$nsgName = "myNsg"
$ruleName = "myRdpRule"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name $ruleName -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>A virtuális hálózat változó létrehozása

Hozzon létre egy változót, a befejezett virtuális hálózat. 

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName

```

### <a name="get-the-credentials-for-the-vm"></a>A hitelesítő adatokat lekérni a virtuális géphez

A következő parancsmag megnyit egy ablakot, ahol egy új felhasználónevet és jelszót ehhez a helyi rendszergazda fiókot távoli eléréséhez a virtuális gép módba lép. 

```azurepowershell-interactive
$cred = Get-Credential
```

### <a name="set-variables-for-the-vm-name-computer-name-and-the-size-of-the-vm"></a>A virtuális gép nevét, a számítógép nevét és a virtuális gép méretét változók megadása

A virtuális gép nevét és a számítógép neve változók létrehozása. Ez a példa beállítja a virtuális gép neve, ahogyan **myVM** és a számítógép neve, mint a **Sajátgép**.

```azurepowershell-interactive
$vmName = "myVM"
$computerName = "myComputer"
```

A virtuális gép méretének beállítása. Ez a példa létrehoz **Standard_DS1_v2** méretű virtuális gép. Tekintse meg a [Virtuálisgép-méretek](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) dokumentációjában talál további információt.

```azurepowershell-interactive
$vmSize = "Standard_DS1_v2"
```

A Virtuálisgép-konfiguráció hozzáadása a virtuális gép nevét és méretét.

```azurepowershell-interactive
$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
```

### <a name="set-the-vm-image-as-source-image-for-the-new-vm"></a>A Virtuálisgép-lemezkép beállítása forráslemezkép a új virtuális gép számára

Állítsa be a forrásképből, a felügyelt Virtuálisgép-lemezkép Azonosítóját használja.

```azurepowershell-interactive
$vm = Set-AzureRmVMSourceImage -VM $vm -Id $image.Id
```

### <a name="set-the-os-configuration-and-add-the-nic"></a>Az operációs rendszer konfigurációjának, és adja hozzá a hálózati adaptert.

Adja meg a tároló típusa (PremiumLRS vagy StandardLRS) és az operációs rendszer lemezének mérete. Ebben a példában a fiók típusát állítja **PremiumLRS**, a lemez mérete **128 GB-os** és lemezes gyorsítótárazás **ReadWrite**.

```azurepowershell-interactive
$vm = Set-AzureRmVMOSDisk -VM $vm  `
    -StorageAccountType PremiumLRS `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite

$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $computerName `
-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

### <a name="create-the-vm"></a>Virtuális gép létrehozása

A konfigurációt, amely azt alapul, és tárolja az új virtuális gép létrehozása a **$vm** változó.

```azurepowershell-interactive
New-AzureRmVM -VM $vm -ResourceGroupName $rgName -Location $location
```

### <a name="verify-that-the-vm-was-created"></a>Győződjön meg arról, hogy létrejött-e a virtuális gép
Amikor végzett, megjelenik az újonnan létrehozott virtuális gép a [Azure-portálon](https://portal.azure.com) alatt **Tallózás** > **virtuális gépek**, vagy a következő PowerShell-parancsok használatával:

```azurepowershell-interactive
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Következő lépések
Az új virtuális gépet az Azure PowerShell kezeléséhez, tekintse meg [létrehozása és kezelése Windows virtuális gépek az Azure PowerShell modulra](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

