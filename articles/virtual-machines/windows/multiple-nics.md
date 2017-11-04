---
title: "Létrehozása és kezelése Windows virtuális gépek Azure-ban több hálózati adaptert használó |} Microsoft Docs"
description: "Megtudhatja, hogyan hozhatja létre és kezelheti a Windows virtuális gép nem csatlakoztatható az Azure PowerShell vagy a Resource Manager-sablonok segítségével több hálózati adapterrel rendelkező."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: 9bff5b6d-79ac-476b-a68f-6f8754768413
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: iainfou
ms.openlocfilehash: 941791ba398a3abbaa5137c36391fd23789cd3b1
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2017
---
# <a name="create-and-manage-a-windows-virtual-machine-that-has-multiple-nics"></a>Létrehozása és kezelése a Windows rendszerű virtuális gép, amely több hálózati adapterrel rendelkezik.
Virtuális gépek (VM) az Azure-ban rendelkezhet több virtuális hálózati adapterek (NIC) kapcsolódik. Egy gyakori forgatókönyv, hogy az előtér- és kapcsolat, vagy a hálózaton, figyelési vagy biztonsági mentési megoldásra dedikált különböző alhálózatokon. Ez a cikk részletesen létrehozása, amely rendelkezik a több hálózati adapter nem csatlakoztatható. Azt is megtudhatja, hogyan lehet hozzáadni vagy eltávolítani a hálózati adapter egy meglévő virtuális gépről. Különböző [Virtuálisgép-méretek](sizes.md) több hálózati adapter támogatja, így méretezés ennek megfelelően a virtuális Gépet.

## <a name="prerequisites"></a>Előfeltételek
Győződjön meg arról, hogy rendelkezik a [legújabb Azure PowerShell-verzió telepítve és konfigurálva](/powershell/azure/overview).

A következő példákban cserélje le a saját értékeit példa paraméterek nevei. Példa paraméter nevek a következők *myResourceGroup*, *myVnet*, és *myVM*.


## <a name="create-a-vm-with-multiple-nics"></a>Több hálózati adapterrel rendelkező virtuális gép létrehozása
Először hozzon létre egy erőforráscsoportot. Az alábbi példa létrehoz egy erőforráscsoportot *myResourceGroup* a a *EastUs* helye:

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### <a name="create-virtual-network-and-subnets"></a>Virtuális hálózati és alhálózatok létrehozásával
Egy általános forgatókönyv van egy virtuális hálózat két vagy több alhálózattal rendelkezik. Egy alhálózat lehet előtér-forgalom esetén a másik a háttér-forgalmat. Mindkét alhálózat csatlakozhat, ezt követően az több hálózati adaptert a virtuális gépen.

1. Adja meg a két virtuális hálózati alhálózat [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Az alábbi példa meghatározza az alhálózatok *mySubnetFrontEnd* és *mySubnetBackEnd*:

    ```powershell
    $mySubnetFrontEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. Hozza létre a virtuális hálózat és alhálózat [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Az alábbi példa létrehoz egy virtuális hálózatot nevű *myVnet*:

    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### <a name="create-multiple-nics"></a>Több hálózati adapter létrehozása
Hozzon létre két hálózati adapterrel [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). Az előtér-alhálózathoz egyetlen hálózati Adapterrel és egy hálózati adapter csatlakoztatása a háttér-alhálózat. Az alábbi példakód létrehozza nevű hálózati adaptert *myNic1* és *myNic2*:

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic1" `
    -Location "EastUs" `
    -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic2" `
    -Location "EastUs" `
    -SubnetId $backEnd.Id
```

Általában akkor is létrehozhat egy [hálózati biztonsági csoport](../../virtual-network/virtual-networks-nsg.md) a virtuális gép hálózati forgalmának szűrésére és egy [terheléselosztó](../../load-balancer/load-balancer-overview.md) forgalom szét több virtuális géphez.

### <a name="create-the-virtual-machine"></a>A virtuális gép létrehozása
Most indítsa el a Virtuálisgép-konfiguráció létrehozásához. Minden virtuális gép méretét a teljes száma, adhat hozzá egy virtuális hálózati adapterrel van korlátozva. További információkért lásd: [Windows Virtuálisgép-méretek](sizes.md).

1. A virtuális gép hitelesítő adatok beállítása a `$cred` változót az alábbiak szerint:

    ```powershell
    $cred = Get-Credential
    ```

2. Adja meg a virtuális Gépet a [új AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). Az alábbi példa meghatározza egy nevű virtuális gép *myVM* és használ, amely támogatja a több mint két hálózati adapter Virtuálisgép-méretet (*Standard_DS3_v2*):

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. Hozzon létre a Virtuálisgép-konfiguráció, a többi [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) és [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage). Az alábbi példa létrehoz egy Windows Server 2016 virtuális Gépet:

    ```powershell
    $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig `
        -Windows `
        -ComputerName "myVM" `
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
    $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig `
        -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" `
        -Skus "2016-Datacenter" `
        -Version "latest"
   ```

4. A két hálózati adaptert, amely a korábban létrehozott csatolása [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

    ```powershell
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. Végezetül hozza létre a virtuális Gépet a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm):

    ```powershell
    New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

## <a name="add-a-nic-to-an-existing-vm"></a>A hálózati adapter hozzáadása egy meglévő virtuális Gépen
A virtuális hálózati adapter hozzáadása egy meglévő virtuális Gépre, a virtuális gép felszabadítása adja hozzá a virtuális hálózati Adaptert, majd indítsa el a virtuális Gépet. Különböző [Virtuálisgép-méretek](sizes.md) több hálózati adapter támogatja, így méretezés ennek megfelelően a virtuális Gépet. Ha szükséges, akkor [méretezze át a virtuális gépek](resize-vm.md).

1. A virtuális Géphez a felszabadítani [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). Az alábbi példa felszabadítja a nevű virtuális gép *myVM* a *myResourceGroup*:

    ```powershell
    Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. A virtuális Géphez a meglévő konfigurációjának beolvasása [Get-AzureRmVm](/powershell/module/azurerm.compute/get-azurermvm). Az alábbi példa nevű virtuális gép adatainak beolvasása *myVM* a *myResourceGroup*:

    ```powershell
    $vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Az alábbi példakód létrehozza a virtuális hálózati adapter a [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) nevű *myNic3* társított *mySubnetBackEnd*. A virtuális hálózati adapter majd csatolva van a virtuális gép nevű *myVM* a *myResourceGroup* rendelkező [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

    ```powershell
    # Get info for the back end subnet
    $myVnet = Get-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
    $backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}

    # Create a virtual NIC
    $myNic3 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic3" `
        -Location "EastUs" `
        -SubnetId $backEnd.Id

    # Get the ID of the new virtual NIC and add to VM
    $nicId = (Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" -Name "MyNic3").Id
    Add-AzureRmVMNetworkInterface -VM $vm -Id $nicId | Update-AzureRmVm -ResourceGroupName "myResourceGroup"
    ```

    ### <a name="primary-virtual-nics"></a>Elsődleges virtuális hálózati adapter
    A hálózati adaptert a virtuális gép több hálózati Adapterrel egyikét kell lennie az elsődleges. Ha egy meglévő virtuális hálózati adaptert a virtuális Gépen már be van állítva elsődleges, kihagyhatja ezt a lépést. Az alábbi példa feltételezi, hogy két virtuális hálózati adapter most a virtuális gép jelen, és hozzáadja az első hálózati adapter (`[0]`) elsődleges:
        
    ```powershell
    # List existing NICs on the VM and find which one is primary
    $vm.NetworkProfile.NetworkInterfaces
    
    # Set NIC 0 to be primary
    $vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
    $vm.NetworkProfile.NetworkInterfaces[1].Primary = $false
    
    # Update the VM state in Azure
    Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. Indítsa el a virtuális Géphez a [Start-AzureRmVm](/powershell/module/azurerm.compute/start-azurermvm):

    ```powershell
    Start-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

## <a name="remove-a-nic-from-an-existing-vm"></a>Egy meglévő virtuális gép egy hálózati adapter eltávolítása
A virtuális hálózati adapter eltávolítása egy meglévő virtuális Gépre, a virtuális gép felszabadítása, távolítsa el a virtuális hálózati Adaptert, majd indítsa el a virtuális Gépet.

1. A virtuális Géphez a felszabadítani [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). Az alábbi példa felszabadítja a nevű virtuális gép *myVM* a *myResourceGroup*:

    ```powershell
    Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. A virtuális Géphez a meglévő konfigurációjának beolvasása [Get-AzureRmVm](/powershell/module/azurerm.compute/get-azurermvm). Az alábbi példa nevű virtuális gép adatainak beolvasása *myVM* a *myResourceGroup*:

    ```powershell
    $vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. A hálózati adapter eltávolítása a adatainak beolvasása [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface). Az alábbi példa információ lekérése *myNic3*:

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. Távolítsa el a hálózati adapter [Remove-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/remove-azurermvmnetworkinterface) majd frissítse a virtuális Géphez a [frissítés-AzureRmVm](/powershell/module/azurerm.compute/update-azurermvm). A következő példában eltávolítjuk *myNic3* módon nyert `$nicId` az előző lépésben:

    ```powershell
    Remove-AzureRmVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzureRmVm -ResourceGroupName "myResourceGroup"
    ```   

5. Indítsa el a virtuális Géphez a [Start-AzureRmVm](/powershell/module/azurerm.compute/start-azurermvm):

    ```powershell
    Start-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## <a name="create-multiple-nics-with-templates"></a>Több hálózati adapterrel létrehozott sablonok
Azure Resource Manager-sablonok segítségével hozzon létre egy erőforrás több példánya központi telepítést végez, például több hálózati adapter létrehozása során. Resource Manager-sablonok deklaratív JSON-fájlok segítségével határozza meg a környezetben. További információkért lásd: [áttekintése Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Használhat *másolási* létrehozásához példányok száma:

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

További információkért lásd: [használatával több példány létrehozásával *másolási*](../../resource-group-create-multiple.md). 

Is `copyIndex()` több hozzáfűzése erőforrás neve. Ezután létrehozhat *myNic1*, *MyNic2* és így tovább. A következő kódot az értéket fűznek példáját mutatja be:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Átfogó példát olvasható [több hálózati adapter létrehozása a Resource Manager-sablonok segítségével](../../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Következő lépések
Felülvizsgálati [Windows Virtuálisgép-méretek](sizes.md) próbál, ha több hálózati adapterrel rendelkező virtuális gép létrehozása. Nagy figyelmet fordítani az egyes Virtuálisgép-méretet támogató hálózati adapterek maximális száma. 


