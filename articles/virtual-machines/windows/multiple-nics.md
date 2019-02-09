---
title: Hozzon létre, és a Windows virtuális gépek kezelése az Azure-ban, amely több hálózati adapter használata |} A Microsoft Docs
description: Megtudhatja, hogyan hozhat létre és kezelhet egy Windows Azure PowerShell vagy a Resource Manager-sablonok használatával csatlakozik, több hálózati adapterrel rendelkező virtuális Gépet.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 9bff5b6d-79ac-476b-a68f-6f8754768413
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: cynthn
ms.openlocfilehash: cc4fb07874015112791ef2eaf9c39b31b690006c
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978663"
---
# <a name="create-and-manage-a-windows-virtual-machine-that-has-multiple-nics"></a>Hozzon létre, és több hálózati adapterrel rendelkező Windows virtuális gép kezelése
Azure-beli virtuális gépek (VM) több virtuális hálózati adapterek (NIC) csatlakoztatott adatlemezekkel is rendelkezik. Gyakran előfordul, hogy az előtérbeli és háttérbeli kapcsolat különböző alhálózatokon. Több alhálózaton egy virtuális gépen több hálózati adapter társíthat, de ezekhez az alhálózatokhoz kell az összes található ugyanazon a virtuális hálózaton (vNet). Ez a cikk részletesen csatlakozik, több hálózati adapterrel rendelkező virtuális gép létrehozása. Azt is megtudhatja, hogyan hozzáadása vagy eltávolítása a hálózati adapterek meglévő virtuális gépről. Különböző [Virtuálisgép-méretek](sizes.md) támogatja a hálózati adapterek különböző számú, tehát ennek megfelelően az a virtuális gép méretezéséhez.

## <a name="prerequisites"></a>Előfeltételek

A következő példákban cserélje le a példa a paraméter nevét a saját értékeire. Példa a paraméter nevek a következők *myResourceGroup*, *myVnet*, és *myVM*.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="create-a-vm-with-multiple-nics"></a>Több hálózati adapterrel rendelkező virtuális gép létrehozása
Először hozzon létre egy erőforráscsoportot. A következő példában létrehozunk egy erőforráscsoportot, nevű *myResourceGroup* a a *EastUs* helye:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### <a name="create-virtual-network-and-subnets"></a>Virtuális hálózat és alhálózat létrehozása
Egy gyakori forgatókönyvet szeretné, hogy két vagy több alhálózatot a virtuális hálózat van. Egy alhálózatot az előtér-forgalom, a másik háttér-forgalom lehet. Mindkét alhálózat csatlakozik, majd használhatja több hálózati adapter a virtuális Gépen.

1. Adja meg a két virtuális hálózat alhálózataiban [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Az alábbi példa meghatározza az alhálózatra *mySubnetFrontEnd* és *mySubnetBackEnd*:

    ```powershell
    $mySubnetFrontEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. A virtuális hálózat és alhálózat létrehozása [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork). A következő példában létrehozunk egy nevű virtuális hálózatot *myVnet*:

    ```powershell
    $myVnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### <a name="create-multiple-nics"></a>Több hálózati adapter létrehozása
Hozzon létre két hálózati adapterrel [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface). Az előtérbeli alhálózat egy hálózati adapter és a egy hálózati adapter csatlakoztatása a háttérbeli alhálózat. A következő példában létrehozunk nevű hálózati adaptert *myNic1* és *myNic2*:

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic1" `
    -Location "EastUs" `
    -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic2" `
    -Location "EastUs" `
    -SubnetId $backEnd.Id
```

Általában akkor is létrehozhat egy [hálózati biztonsági csoport](../../virtual-network/security-overview.md) a virtuális gép hálózati forgalom szűrésére és a egy [terheléselosztó](../../load-balancer/load-balancer-overview.md) több virtuális gép között oszthatja el a forgalmat.

### <a name="create-the-virtual-machine"></a>A virtuális gép létrehozása
Most indítsa el a virtuális gép konfigurációjának létrehozása. Minden Virtuálisgép-méret esetében is hozzáadhat egy virtuális gép hálózati adapterekkel teljes számát. További információkért lásd: [Windows Virtuálisgép-méretek](sizes.md).

1. A virtuális gép hitelesítő adatainak beállítása a `$cred` változót az alábbiak szerint:

    ```powershell
    $cred = Get-Credential
    ```

2. Adja meg a virtuális gép [új AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig). Az alábbi példa meghatározza nevű virtuális gép *myVM* , és használja a virtuális gép méretét, amely támogatja a több mint két hálózati adapterrel (*Standard_DS3_v2*):

    ```powershell
    $vmConfig = New-AzVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. Hozzon létre a Virtuálisgép-konfigurációt, a többi [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) és [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage). A következő példában létrehozunk egy Windows Server 2016 virtuális gép:

    ```powershell
    $vmConfig = Set-AzVMOperatingSystem -VM $vmConfig `
        -Windows `
        -ComputerName "myVM" `
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
    $vmConfig = Set-AzVMSourceImage -VM $vmConfig `
        -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" `
        -Skus "2016-Datacenter" `
        -Version "latest"
   ```

4. A korábban létrehozott két hálózati adapter csatolása [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface):

    ```powershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. A virtuális gép létrehozása [új AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm):

    ```powershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

6. Útvonalak hozzáadása másodlagos hálózati adaptereken, az operációs rendszer által ismertetett lépéseket követve [az operációs rendszer konfigurálása több hálózati adapterrel](#configure-guest-os-for-multiple-nics).

## <a name="add-a-nic-to-an-existing-vm"></a>A hálózati adapter hozzáadása egy meglévő virtuális gép
Virtuális hálózati adapter hozzáadása egy meglévő virtuális Gépet, a virtuális gép felszabadítása adja hozzá a virtuális hálózati Adaptert, majd indítsa el a virtuális Gépet. Különböző [Virtuálisgép-méretek](sizes.md) támogatja a hálózati adapterek különböző számú, tehát ennek megfelelően az a virtuális gép méretezéséhez. Ha szükséges, [virtuális gép átméretezése](resize-vm.md).

1. Szabadítsa fel a virtuális Gépet a [Stop-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm). Az alábbi példa felszabadítja a virtuális gép nevű *myVM* a *myResourceGroup*:

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. A virtuális Gépet a meglévő konfigurációjának beszerzéséhez [Get-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/get-azvm). Az alábbi példa lekéri az információkat a virtuális gép nevű *myVM* a *myResourceGroup*:

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Az alábbi példa létrehoz egy virtuális hálózati Adaptert a [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) nevű *myNic3* társított *mySubnetBackEnd*. A virtuális hálózati adapter csatlakozik majd nevű virtuális Gépet *myVM* a *myResourceGroup* a [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface):

    ```powershell
    # Get info for the back end subnet
    $myVnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
    $backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}

    # Create a virtual NIC
    $myNic3 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic3" `
        -Location "EastUs" `
        -SubnetId $backEnd.Id

    # Get the ID of the new virtual NIC and add to VM
    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "MyNic3").Id
    Add-AzVMNetworkInterface -VM $vm -Id $nicId | Update-AzVm -ResourceGroupName "myResourceGroup"
    ```

    ### <a name="primary-virtual-nics"></a>Elsődleges virtuális hálózati adapter
    Egy hálózati adapter a virtuális gép több hálózati Adapterrel kell lennie az elsődleges. Ha egy meglévő virtuális hálózati adaptert a virtuális gép már be van állítva elsődlegesként, kihagyhatja ezt a lépést. Az alábbi példa feltételezi, hogy a két virtuális hálózati adapter már megtalálhatók a virtuális gép, és hozzáadja az első hálózati adapter (`[0]`), az elsődleges:
        
    ```powershell
    # List existing NICs on the VM and find which one is primary
    $vm.NetworkProfile.NetworkInterfaces
    
    # Set NIC 0 to be primary
    $vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
    $vm.NetworkProfile.NetworkInterfaces[1].Primary = $false
    
    # Update the VM state in Azure
    Update-AzVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. Indítsa el a virtuális Gépet a [Start-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/start-azvm):

    ```powershell
    Start-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

5. Útvonalak hozzáadása másodlagos hálózati adaptereken, az operációs rendszer által ismertetett lépéseket követve [az operációs rendszer konfigurálása több hálózati adapterrel](#configure-guest-os-for-multiple-nics).

## <a name="remove-a-nic-from-an-existing-vm"></a>Távolítsa el a hálózati adapter egy meglévő virtuális gép
Egy meglévő virtuális gép virtuális hálózati adapter eltávolításához szabadítsa fel a virtuális Gépet, távolítsa el a virtuális hálózati Adaptert, majd indítsa el a virtuális Gépet.

1. Szabadítsa fel a virtuális Gépet a [Stop-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm). Az alábbi példa felszabadítja a virtuális gép nevű *myVM* a *myResourceGroup*:

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. A virtuális Gépet a meglévő konfigurációjának beszerzéséhez [Get-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/get-azvm). Az alábbi példa lekéri az információkat a virtuális gép nevű *myVM* a *myResourceGroup*:

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Információszerzés a hálózati adapter törlése [Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface). Az alábbi példa információkat kér le *myNic3*:

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. Távolítsa el a hálózati Adaptert a [Remove-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmnetworkinterface) , majd frissítse a virtuális Gépet a [Update-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/update-azvm). A következő példa eltávolítja *myNic3* módon nyert `$nicId` az előző lépésben:

    ```powershell
    Remove-AzVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzVm -ResourceGroupName "myResourceGroup"
    ```   

5. Indítsa el a virtuális Gépet a [Start-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/start-azvm):

    ```powershell
    Start-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## <a name="create-multiple-nics-with-templates"></a>Több hálózati adapter létrehozása sablonokkal
Az Azure Resource Manager-sablonok lehetővé teszik egy erőforrás több példányának létrehozása az üzembe helyezés, például a több hálózati adapter létrehozása során. Resource Manager-sablonok deklaratív JSON-fájlok használatával adja meg a környezetben. További információkért lásd: [Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md). Használhat *másolási* adható meg a példányok létrehozásához:

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

További információkért lásd: [több példány használatával történő létrehozásának *másolási*](../../resource-group-create-multiple.md). 

Is `copyIndex()` számos hozzáfűzni kívánt erőforrás nevét. Ezután létrehozhat *myNic1*, *MyNic2* és így tovább. A következő kód bemutatja az index érték hozzáfűzése egy példát:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Teljes példát olvashat [több hálózati adapter létrehozása a Resource Manager-sablonok használatával](../../virtual-network/template-samples.md).

Útvonalak hozzáadása másodlagos hálózati adaptereken, az operációs rendszer által ismertetett lépéseket követve [az operációs rendszer konfigurálása több hálózati adapterrel](#configure-guest-os-for-multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Több hálózati adapterrel a vendég operációs rendszer konfigurálása

Az Azure hozzárendeli az első (elsődleges) hálózati adaptert a virtuális géphez csatolt alapértelmezett átjárót. Az Azure nem rendel hozzá alapértelmezett átjárót a virtuális géphez csatolt további (másodlagos) hálózati adapterekhez. Alapértelmezés szerint ezért nem lehetséges a kommunikáció olyan erőforrásokkal, amelyek a másodlagos hálózati adaptert tartalmazó alhálózaton kívül vannak. Másodlagos hálózati adapterrel, azonban kommunikálhat erőforrások kívül, ha a szükséges lépések engedélyezheti a kommunikációt, a különböző operációs rendszerekhez.

1. Egy Windows parancssorban futtassa a `route print` parancsot, amely a következő két csatlakoztatott hálózati adapterrel rendelkező virtuális gép kimenethez hasonló kimenetet ad vissza:

    ```
    ===========================================================================
    Interface List
    3...00 0d 3a 10 92 ce ......Microsoft Hyper-V Network Adapter #3
    7...00 0d 3a 10 9b 2a ......Microsoft Hyper-V Network Adapter #4
    ===========================================================================
    ```
 
    Ebben a példában **Microsoft Hyper-V hálózati Adapter 4** (7-illesztő) a másodlagos hálózati adapter nincs hozzárendelve alapértelmezett átjárót.

2. Egy parancssorban futtassa a `ipconfig` paranccsal tekintheti meg, hogy mely IP-cím hozzá van rendelve a másodlagos hálózati adapter. Ebben a példában 192.168.2.4 felület 7 van hozzárendelve. A másodlagos hálózati adapter nincs alapértelmezett átjáró címét adja vissza.

3. Az átjáróhoz, az alhálózat a másodlagos hálózati adapter az alhálózaton kívülre címekre tartó összes forgalmat útvonal, futtassa a következő parancsot:

    ```
    route add -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5015 IF 7
    ```

    A az alhálózat címét a meghatározott az alhálózat címtartományának első IP-címe (a ikonra.1 végződésű). Ha nem kívánja az összes forgalom az alhálózaton kívülre, hozzáadhatja egyéni útvonalak adott célhelyre helyette. Például, ha csak a másodlagos hálózati adapterről irányíthatja a forgalmat a 192.168.3.0 a hálózat, a parancs:

      ```
      route add -p 192.168.3.0 MASK 255.255.255.0 192.168.2.1 METRIC 5015 IF 7
      ```
  
4. Ellenőrizze az erőforrás a 192.168.3.0 sikeres kommunikáció hálózati, írja be például 7 (192.168.2.4) felhasználói felületén 192.168.3.4 pingelni a következő parancsot:

    ```
    ping 192.168.3.4 -S 192.168.2.4
    ```

    Előfordulhat, hogy meg kell nyitnia az ICMP, még a következő paranccsal pingelése az eszköz a Windows tűzfalon keresztül:
  
      ```
      netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
      ```
  
5. Erősítse meg a hozzáadni az útvonalat az útvonaltáblában lévő, adja meg a `route print` parancsot, amely az alábbi szöveghez hasonló kimenetet ad vissza:

    ```
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4     15
              0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.4   5015
    ```

    Az útvonal a felsorolt *192.168.1.1* alatt **átjáró**, a útvonalat, amely alapértelmezés szerint azon az elsődleges hálózati adapter van-e. Az útvonal *192.168.2.1* alatt **átjáró**, a hozzáadott útvonalat.

## <a name="next-steps"></a>További lépések
Felülvizsgálat [Windows Virtuálisgép-méretek](sizes.md) próbál, ha több hálózati adapterrel rendelkező virtuális gép létrehozásához. Figyelje meg, amely minden virtuális gép mérete támogatja a hálózati adapterek maximális számánál. 


