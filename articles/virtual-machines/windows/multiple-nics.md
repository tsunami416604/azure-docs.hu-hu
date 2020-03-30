---
title: Több hálózati adaptert használó Windows-virtuális gépek létrehozása és kezelése az Azure-ban
description: Megtudhatja, hogyan hozhat létre és kezelhet olyan Windows virtuális gépeket, amelyekhez több hálózati adapter van csatolva az Azure PowerShell vagy az Erőforrás-kezelő sablonjainak használatával.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: 9bff5b6d-79ac-476b-a68f-6f8754768413
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: cynthn
ms.openlocfilehash: 20a595e1386a8d33c919ad4ff151d65e30b31eda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249983"
---
# <a name="create-and-manage-a-windows-virtual-machine-that-has-multiple-nics"></a>Több hálózati adapterrel rendelkező Windows virtuális gép létrehozása és kezelése
Az Azure-ban lévő virtuális gépekhez több virtuális hálózati adapter (NIC) is tartozhat. Gyakori forgatókönyv, hogy különböző alhálózatok előtér-és háttér-kapcsolat. A virtuális gépeken több hálózati adaptert is társíthat több alhálózathoz, de ezeknek az alhálózatoknak ugyanabban a virtuális hálózatban (vNet) kell rendelkezniük. Ez a cikk ismerteti, hogyan hozhat létre egy virtuális gép, amely több hálózati adapterek csatolták. Azt is megtudhatja, hogyan adhat hozzá vagy távolíthat el hálózati adaptereket egy meglévő virtuális gépről. A különböző [virtuálisgép-méretek](sizes.md) különböző számú hálózati adaptert támogatnak, így a virtuális gép ennek megfelelően méretezze.

## <a name="prerequisites"></a>Előfeltételek

A következő példákban cserélje le a példaparaméterneveket a saját értékeire. Példa paraméter nevek közé *tartozik a myResourceGroup*, *myVnet*, és *myVM*.

 

## <a name="create-a-vm-with-multiple-nics"></a>Több hálózati adapterrel rendelkező virtuális gép létrehozása
Először hozzon létre egy erőforráscsoportot. A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *EastUs* helyen:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### <a name="create-virtual-network-and-subnets"></a>Virtuális hálózat és alhálózatok létrehozása
Gyakori forgatókönyv, hogy egy virtuális hálózat két vagy több alhálózattal rendelkezik. Az egyik alhálózat lehet az előtér-forgalom, a másik a háttér-forgalom. Mindkét alhálózathoz való csatlakozáshoz majd több hálózati adaptert kell használnia a virtuális gépen.

1. Két virtuális hálózati alhálózat definiálása [a New-AzVirtualNetworkSubnetConfig segítségével.](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) A következő példa a *mySubnetFrontEnd* és a *mySubnetBackEnd*alhálózatait határozza meg:

    ```powershell
    $mySubnetFrontEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. Hozza létre a virtuális hálózatot és az alhálózatokat a [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork)segítségével. A következő példa létrehoz egy virtuális hálózat nevű *myVnet:*

    ```powershell
    $myVnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### <a name="create-multiple-nics"></a>Több hálózati adapter létrehozása
Hozzon létre két hálózati adaptert a [New-AzNetworkInterface segítségével.](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) Csatoljon egy hálózati adaptert az előtér-alhálózathoz és egy hálózati adaptert a háttér-alhálózathoz. A következő példa a *myNic1* és *myNic2*nevű hálózati adaptereket hozza létre:

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

Általában [hozzon](../../virtual-network/security-overview.md) létre egy hálózati biztonsági csoportot a virtuális gép hálózati forgalmának és egy [terheléselosztónak](../../load-balancer/load-balancer-overview.md) a virtuális gép közötti elosztáshoz.

### <a name="create-the-virtual-machine"></a>A virtuális gép létrehozása
Most indítsa el a virtuális gép konfigurációját. Minden virtuális gép mérete rendelkezik egy korlátot a virtuális gépekhez hozzáadható hálózati adapterek teljes számát. További információt a [Windows virtuális gépek méretei című](sizes.md)témakörben talál.

1. Állítsa be a virtuális `$cred` gép hitelesítő adatait a változóra az alábbiak szerint:

    ```powershell
    $cred = Get-Credential
    ```

2. Adja meg a virtuális gép [new-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig). A következő példa egy *myVM* nevű virtuális gépet határoz meg, és olyan virtuális gépméretet használ, amely kettőnél több hálózati adaptert támogat (*Standard_DS3_v2):*

    ```powershell
    $vmConfig = New-AzVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. Hozza létre a vm-konfiguráció többi részét a [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) és [a Set-AzVMSourceImage segítségével.](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) A következő példa létrehoz egy Windows Server 2016 virtuális gép:

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

4. Csatolja az [Add-AzVMNetworkInterface-el](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface)korábban létrehozott két hálózati adaptert:

    ```powershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. A virtuális gép létrehozása az [Új-AzVM-mel:](https://docs.microsoft.com/powershell/module/az.compute/new-azvm)

    ```powershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

6. Adja hozzá a másodlagos hálózati adapterek útvonalait az operációs rendszerhez a Több hálózati [adapter operációs rendszerének konfigurálása](#configure-guest-os-for-multiple-nics)című lépések végrehajtásával.

## <a name="add-a-nic-to-an-existing-vm"></a>Hálózati adapter hozzáadása meglévő virtuális géphez
Virtuális hálózati adapter hozzáadása egy meglévő virtuális géphez, a virtuális gép felszabadítása, adja hozzá a virtuális hálózati adaptert, majd indítsa el a virtuális gépet. A különböző [virtuálisgép-méretek](sizes.md) különböző számú hálózati adaptert támogatnak, így a virtuális gép ennek megfelelően méretezze. Szükség esetén [átméretezheti a virtuális gépeket.](resize-vm.md)

1. A virtuális gép [felszabadítása a Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm)segítségével. A következő példa felszabadítja a *myResourceGroup myVM* nevű virtuális *gépét:*

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. A virtuális gép meglévő konfigurációjának beszereznie a [Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm)segítségével. A következő példa a *myResourceGroup* *myVM* nevű virtuális gép adatait kapja:

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. A következő példa létrehoz egy virtuális hálózati adaptert a *mySubnetBackEnd*hez csatolt [My-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) nevű *myNic3* nevű felülettel. A virtuális hálózati adapter ezután csatlakozik a virtuális gép nevű *myVM* *a myResourceGroup* [add-AzVMNetworkInterface:](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface)

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

    ### <a name="primary-virtual-nics"></a>Elsődleges virtuális hálózati adapterek
    A több hálózati adapterek egyik hálózati adapterének elsődlegesnek kell lennie. Ha a virtuális gép meglévő virtuális hálózati adaptereinek egyike már elsődlegesként van beállítva, kihagyhatja ezt a lépést. A következő példa feltételezi, hogy két virtuális hálózati adapter ek már jelen vannak`[0]`a virtuális gépen, és szeretné hozzáadni az első hálózati adapter ( ) az elsődleges:
        
    ```powershell
    # List existing NICs on the VM and find which one is primary
    $vm.NetworkProfile.NetworkInterfaces
    
    # Set NIC 0 to be primary
    $vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
    $vm.NetworkProfile.NetworkInterfaces[1].Primary = $false
    
    # Update the VM state in Azure
    Update-AzVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. Indítsa el a virtuális gép [start-AzVm:](https://docs.microsoft.com/powershell/module/az.compute/start-azvm)

    ```powershell
    Start-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

5. Adja hozzá a másodlagos hálózati adapterek útvonalait az operációs rendszerhez a Több hálózati [adapter operációs rendszerének konfigurálása](#configure-guest-os-for-multiple-nics)című lépések végrehajtásával.

## <a name="remove-a-nic-from-an-existing-vm"></a>Hálózati adapter eltávolítása meglévő virtuális gépről
Virtuális hálózati adapter eltávolítása egy meglévő virtuális gép, a virtuális gép felszabadítása, távolítsa el a virtuális hálózati adaptert, majd indítsa el a virtuális gépet.

1. A virtuális gép [felszabadítása a Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm)segítségével. A következő példa felszabadítja a *myResourceGroup myVM* nevű virtuális *gépét:*

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. A virtuális gép meglévő konfigurációjának beszereznie a [Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm)segítségével. A következő példa a *myResourceGroup* *myVM* nevű virtuális gép adatait kapja:

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. A hálózati adapter eltávolításáról a [Get-AzNetworkInterface segítségével](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface)kaphat tájékoztatást. A következő példa információt kap a *myNic3-ról:*

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. Távolítsa el a hálózati adaptert az [Remove-AzVMNetworkInterface segítségével,](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmnetworkinterface) majd frissítse a virtuális gépét az [Update-AzVm](https://docs.microsoft.com/powershell/module/az.compute/update-azvm)segítségével. A következő példa eltávolítja a *myNic3-at* `$nicId` az előző lépésben kapott módon:

    ```powershell
    Remove-AzVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzVm -ResourceGroupName "myResourceGroup"
    ```   

5. Indítsa el a virtuális gép [start-AzVm:](https://docs.microsoft.com/powershell/module/az.compute/start-azvm)

    ```powershell
    Start-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## <a name="create-multiple-nics-with-templates"></a>Több hálózati adapter létrehozása sablonokkal
Az Azure Resource Manager-sablonok lehetővé teszik egy erőforrás több példányának létrehozását a telepítés során, például több hálózati adapterek létrehozását. Az Erőforrás-kezelő sablonjai deklaratív JSON-fájlokat használnak a környezet definiálására. További információt [az Azure Resource Manager áttekintése](../../azure-resource-manager/management/overview.md)című témakörben talál. A *másolás* segítségével megadhatja a létrehozandó példányok számát:

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

További információt a [Több példány másolással történő létrehozása *című témakörben talál.*](../../resource-group-create-multiple.md) 

Azt is `copyIndex()` használhatja, hogy egy számot hozzáfűzhet egy erőforrás nevéhez. Ezután *létrehozhatja a myNic1*, *MyNic2* és így tovább. A következő kód egy példát mutat be az indexérték hozzáfűzéséhez:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Az [Erőforrás-kezelő sablonjainak használatával elolvashatja](../../virtual-network/template-samples.md)a több hálózati adapter létrehozására adott teljes példát.

Adja hozzá a másodlagos hálózati adapterek útvonalait az operációs rendszerhez a Több hálózati [adapter operációs rendszerének konfigurálása](#configure-guest-os-for-multiple-nics)című lépések végrehajtásával.

## <a name="configure-guest-os-for-multiple-nics"></a>Vendég operációs rendszer konfigurálása több hálózati adapterhez

Az Azure hozzárendel egy alapértelmezett átjárót a virtuális géphez csatlakoztatott első (elsődleges) hálózati illesztőhöz. Az Azure nem rendel hozzá alapértelmezett átjárót a virtuális géphez csatolt további (másodlagos) hálózati adapterekhez. Alapértelmezés szerint ezért nem lehetséges a kommunikáció olyan erőforrásokkal, amelyek a másodlagos hálózati adaptert tartalmazó alhálózaton kívül vannak. A másodlagos hálózati adapterek azonban kommunikálhatnak az alhálózaton kívüli erőforrásokkal, bár a kommunikáció engedélyezésének lépései a különböző operációs rendszerek esetében eltérőek.

1. A Windows parancssorból `route print` futtassa a parancsot, amely a következő kimenethez hasonló kimenetet ad vissza egy két csatlakoztatott hálózati csatolmányral rendelkező virtuális géphez:

    ```
    ===========================================================================
    Interface List
    3...00 0d 3a 10 92 ce ......Microsoft Hyper-V Network Adapter #3
    7...00 0d 3a 10 9b 2a ......Microsoft Hyper-V Network Adapter #4
    ===========================================================================
    ```
 
    Ebben a példában a **Microsoft Hyper-V hálózati adapter #4** (7- es csatoló) a másodlagos hálózati illesztő, amelyhez nincs alapértelmezett átjáró rendelve.

2. A parancssorból futtassa a `ipconfig` parancsot, és nézze meg, hogy melyik IP-cím van hozzárendelve a másodlagos hálózati adapterhez. Ebben a példában a 192.168.2.4 van rendelve a 7-es felülethez. A másodlagos hálózati csatolóhoz nem ad vissza alapértelmezett átjárócímet.

3. Ha a másodlagos hálózati csatoló alhálózatán kívüli címekre irányuló összes forgalmat az alhálózat átjárójához szeretné irányítani, futtassa a következő parancsot:

    ```
    route add -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5015 IF 7
    ```

    Az alhálózat átjárócíme az alhálózathoz megadott címtartomány első IP-címe (0,1-es végződés). Ha nem szeretné az összes forgalmat az alhálózaton kívülre irányítani, hozzáadhat ja az egyes útvonalakat adott célhelyekhez. Ha például csak a másodlagos hálózati adapterről a 192.168.3.0 hálózatra szeretné irányítani a forgalmat, adja meg a következő parancsot:

      ```
      route add -p 192.168.3.0 MASK 255.255.255.0 192.168.2.1 METRIC 5015 IF 7
      ```
  
4. A 192.168.3.0 hálózat egyik erőforrásával való sikeres kommunikáció megerősítéséhez például írja be a következő parancsot a 192.168.3.4-es pingeléshez a 7-es interfész (192.168.2.4) segítségével:

    ```
    ping 192.168.3.4 -S 192.168.2.4
    ```

    Előfordulhat, hogy az ICMP-t a következő paranccsal kell megnyitnia a pingelt eszköz Windows tűzfalán keresztül:
  
      ```
      netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
      ```
  
5. Annak ellenőrzéséhez, hogy a hozzáadott útvonal `route print` szerepel-e az útvonaltáblában, írja be a parancsot, amely a következő szöveghez hasonló kimenetet ad vissza:

    ```
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4     15
              0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.4   5015
    ```

    A *192.168.1.1* átjáró csoportban felsorolt útvonal az az útvonal, amely alapértelmezés szerint létezik az elsődleges hálózati adapterhez. **Gateway** A **Gateway** *192.168.2.1-es* útvonala a hozzáadott útvonal.

## <a name="next-steps"></a>További lépések
Tekintse át [a Windows virtuális gép méreteit,](sizes.md) amikor több hálózati adapterrel rendelkező virtuális gép létrehozásához próbál létrehozni. Ügyeljen arra, hogy az egyes virtuális gépméretek által támogatott hálózati adapterek maximális számát. 


