---
title: "Betöltése Windows virtuális gépek Azure-ban elosztása |} Microsoft Docs"
description: "Az Azure load balancer használata magas rendelkezésre állású és biztonságos-alkalmazás létrehozásának három Windows virtuális gépek között"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/14/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 6eee852e703d25ccc4b13401c3e4ab46d09655da
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2017
---
# <a name="how-to-load-balance-windows-virtual-machines-in-azure-to-create-a-highly-available-application"></a>Betöltése egyenleg Windows virtuális gépek magas rendelkezésre állású alkalmazás létrehozása az Azure-ban
Terheléselosztás biztosít a rendelkezésre állási magasabb szintű bejövő kérelmek elosztásával el több virtuális gépre. Ebben az oktatóanyagban elsajátíthatja az Azure load balancer különböző összetevőit, ossza el a forgalmat, és magas rendelkezésre állás biztosításához. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Hozzon létre egy Azure terheléselosztó
> * A health terheléselosztói mintavétel létrehozása
> * Terheléselosztó forgalomra vonatkozó szabályok létrehozása
> * Az egyéni parancsprogramok futtatására szolgáló bővítmény használatával alapvető IIS-webhely létrehozása
> * Virtuális gépek létrehozása és csatolása a terheléselosztó
> * A művelet egy terhelés-kiegyenlítő megtekintése
> * Adja hozzá, és távolítsa el a virtuális gépek a terheléselosztó

Az oktatóanyaghoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.


## <a name="azure-load-balancer-overview"></a>Az Azure load balancer áttekintése
Egy Azure terheléselosztó a réteg-4 (TCP, UDP) terheléselosztóhoz, amely a magas rendelkezésre állást biztosít azáltal, hogy a bejövő forgalom kifogástalan állapotú virtuális gépek között. Terheléselosztói állapotfigyelő mintavétel az egyes virtuális gépek egy adott portot figyeli, és csak osztja el a forgalmat egy operatív virtuális gépre.

Egy előtér-IP-konfigurációja, amely tartalmaz egy vagy több nyilvános IP-címeket adhat meg. Az előtér-IP-konfiguráció lehetővé teszi, hogy a terheléselosztó és az alkalmazások elérhetők lesznek az interneten keresztül. 

Virtuális gépek csatlakozni a virtuális hálózati kártya (NIC) használatával egy terheléselosztó. A virtuális gépek felé irányuló forgalom terjesztéséhez, egy háttér címkészletet tartalmaz az IP-cím, a virtuális (NIC) címét csatlakozik a terheléselosztóhoz.

A forgalmat szabályozásához adhat meg terhelés terheléselosztó szabályt adott portok és protokollok, amelyek a virtuális gépekre van leképezve.


## <a name="create-azure-load-balancer"></a>Az Azure terheléselosztó létrehozása
Ez a szakasz részletesen, hogyan hozhat létre, és minden összetevője a terheléselosztó konfigurálásához. A terheléselosztó létrehozása előtt hozzon létre egy erőforráscsoportot, a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Az alábbi példa létrehoz egy erőforráscsoportot *myResourceGroupLoadBalancer* a a *EastUS* helye:

```powershell
New-AzureRmResourceGroup `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location EastUS
```

### <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet
Az alkalmazás az Internet eléréséhez a terheléselosztó szükség van egy nyilvános IP-cím. Hozzon létre egy nyilvános IP-cím [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). Az alábbi példa létrehoz egy nyilvános IP-cím nevű *myPublicIP* a a *myResourceGroupLoadBalancer* erőforráscsoport:

```powershell
$publicIP = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location EastUS `
  -AllocationMethod Static `
  -Name myPublicIP
```

### <a name="create-a-load-balancer"></a>Terheléselosztó létrehozása
Hozzon létre egy előtérbeli IP-készlet [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig). Az alábbi példakód létrehozza a nevű előtérbeli IP-címkészlet *myFrontEndPool* , és csatolja a *myPublicIP* cím: 

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig `
  -Name myFrontEndPool `
  -PublicIpAddress $publicIP
```

Hozzon létre egy háttér-címkészlet, amely [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig). A virtuális gépek csatlakoztatása ehhez a háttérkészlethez, a fennmaradó lépéseit. Az alábbi példa létrehoz egy háttér címkészletet nevű *myBackEndPool*:

```powershell
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name myBackEndPool
```

Hozza létre a terheléselosztót, [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer). Az alábbi példa létrehoz egy terhelés-kiegyenlítő nevű *myLoadBalancer* az előző lépésben létrehozott használatakor az előtér- és háttérszolgáltatások IP-készletek:

```powershell
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Name myLoadBalancer `
  -Location EastUS `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### <a name="create-a-health-probe"></a>Hozzon létre egy állapotmintáihoz
Ahhoz, hogy a terheléselosztó a figyelheti az alkalmazás állapotát, használja a állapotmintáihoz. A állapotmintáihoz dinamikusan eltávolítása vagy virtuális gépek állapotát ellenőrzi a válasz alapján terhelés terheléselosztó elforgatási. Alapértelmezés szerint a virtuális gép törlődik a terheléselosztó terheléselosztási 15 másodperces időközönként két egymást követő hibák után. Létrehozhat egy állapotmintáihoz protokoll vagy egy meghatározott állapottal ellenőrzése lapon, az alkalmazás alapján. 

Az alábbi példa létrehoz egy TCP-Hálózatfigyelővel. Egyéni HTTP mintavételt további részletes állapotának ellenőrzésére is létrehozhat. Ha egy egyéni HTTP-vizsgálatot, létre kell hoznia az állapot ellenőrzése lapon, például a *healthcheck.aspx*. A mintavétel kell visszaadnia egy **HTTP 200 OK** válasz megtartja a gazdagép Elforgatás a terheléselosztóhoz.

A TCP állapotmintáihoz létrehozásához használhatja [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig). Az alábbi példa létrehoz egy nevű állapotmintáihoz *myHealthProbe* minden virtuális gép, amely figyeli a *TCP* port *80*:

```powershell
Add-AzureRmLoadBalancerProbeConfig `
  -Name myHealthProbe `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

A állapotmintáihoz alkalmazásához frissíteni a terheléselosztót, [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer):

```powershell
Set-AzureRmLoadBalancer -LoadBalancer $lb
```

### <a name="create-a-load-balancer-rule"></a>Hozzon létre olyan terheléselosztó szabályhoz
Olyan terheléselosztó szabályhoz hogyan adatforgalom elosztása a virtuális gépek azonosítására szolgál. Megadhatja az előtér-IP-konfigurációjának megadása a bejövő forgalom és a háttér IP-címkészlet, valamint a megfelelő forrás és cél portot forgalom fogadására. Győződjön meg arról, hogy csak a megfelelő virtuális gépek forgalom fogadására, hogy is megadhatja a használandó állapotmintáihoz.

Hozzon létre olyan terheléselosztó szabályhoz rendelkező [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig). Az alábbi példa létrehoz egy terheléselosztási szabály nevű *myLoadBalancerRule* és a forgalom egyenlege *TCP* port *80*:

```powershell
$probe = Get-AzureRmLoadBalancerProbeConfig -LoadBalancer $lb -Name myHealthProbe

Add-AzureRmLoadBalancerRuleConfig `
  -Name myLoadBalancerRule `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

Frissítse a terheléselosztót, [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer):

```powershell
Set-AzureRmLoadBalancer -LoadBalancer $lb
```


## <a name="configure-virtual-network"></a>Virtuális hálózat konfigurálása
Mielőtt központilag az egyes virtuális gépek, és tesztelheti a terheléselosztó, hozzon létre a támogató virtuális hálózati erőforrásokat. Virtuális hálózatok kapcsolatos további információkért tekintse meg a [Azure virtuális hálózatok kezelése](tutorial-virtual-network.md) oktatóanyag.

### <a name="create-network-resources"></a>Hálózati erőforrások létrehozása
A virtuális hálózat létrehozása [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Az alábbi példa létrehoz egy virtuális hálózatot nevű *myVnet* rendelkező *mySubnet*:

```powershell
# Create subnet config
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create the virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location EastUS `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

A hálózati biztonsági csoport szabály létrehozása [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig), majd hozzon létre egy hálózati biztonsági csoport [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). A hálózati biztonsági csoport hozzáadása a alhálózat [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) majd frissítse a virtuális hálózat [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork). 

Az alábbi példa létrehoz egy hálózati biztonsági szabály nevű *myNetworkSecurityGroup* , és alkalmazza azt *mySubnet*:

```powershell
# Create security rule config
$nsgRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

# Create the network security group
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location EastUS `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRule

# Apply the network security group to a subnet
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name mySubnet `
  -NetworkSecurityGroup $nsg `
  -AddressPrefix 192.168.1.0/24

# Update the virtual network
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

Virtuális hálózati adapter jönnek létre [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). Az alábbi példakód létrehozza a három virtuális hálózati adapter. (Az egyes virtuális gépek virtuális hálózati adapter egy hoz létre az alkalmazáshoz az alábbi lépéseket a). További virtuális hálózati adapterek és virtuális gépek létrehozása tetszőleges időpontban, és adja hozzá a terheléselosztó:

```powershell
for ($i=1; $i -le 3; $i++)
{
   New-AzureRmNetworkInterface `
     -ResourceGroupName myResourceGroupLoadBalancer `
     -Name myNic$i `
     -Location EastUS `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}
```

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása
Az alkalmazás a magas rendelkezésre állás javítása érdekében helyezze a virtuális gépek rendelkezésre állási csoportba.

Állítsa be a rendelkezésre állási létrehozása [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). Az alábbi példakód létrehozza a rendelkezésre állási készlet elnevezett *myAvailabilitySet*:

```powershell
$availabilitySet = New-AzureRmAvailabilitySet `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Name myAvailabilitySet `
  -Location EastUS `
  -Managed `
  -PlatformFaultDomainCount 3 `
  -PlatformUpdateDomainCount 2
```

Állítsa a Rendszergazda felhasználónévvel és jelszóval rendelkező virtuális gépek [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

Most a virtuális gépek is létrehozhat [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Az alábbi példakód létrehozza a három virtuális gépek:

```powershell
for ($i=1; $i -le 3; $i++)
{
  $vm = New-AzureRmVMConfig `
    -VMName myVM$i `
    -VMSize Standard_D1 `
    -AvailabilitySetId $availabilitySet.Id
  $vm = Set-AzureRmVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
  $vm = Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  $vm = Set-AzureRmVMOSDisk `
    -VM $vm `
    -Name myOsDisk$i `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite
  $nic = Get-AzureRmNetworkInterface `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myNic$i
  $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
  New-AzureRmVM `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Location EastUS `
    -VM $vm
}
```

Hozza létre és konfigurálja a három virtuális gépeinek néhány percet vesz igénybe.

### <a name="install-iis-with-custom-script-extension"></a>Az egyéni parancsprogramok futtatására szolgáló bővítmény IIS telepítése
Az oktatóanyag előző [egy Windows rendszerű virtuális gép testreszabása](tutorial-automate-vm-deployment.md), megtudta, hogyan automatizálható az egyéni parancsfájl kiterjesztése a Windows virtuális gép testreszabása. Használhatja ugyanezt a megközelítést az IIS telepítése és konfigurálása a virtuális gépeken.

Használjon [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) az egyéni parancsprogramok futtatására szolgáló bővítmény telepítése. A bővítmény fut `powershell Add-WindowsFeature Web-Server` az IIS webkiszolgálót, és a frissítések telepítése a *Default.htm* a virtuális gép állomásnevét lapjait:

```powershell
for ($i=1; $i -le 3; $i++)
{
   Set-AzureRmVMExtension `
     -ResourceGroupName myResourceGroupLoadBalancer `
     -ExtensionName IIS `
     -VMName myVM$i `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.4 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
     -Location EastUS
}
```

## <a name="test-load-balancer"></a>Teszt terheléselosztó
Szerezze be a terheléselosztó a nyilvános IP-címe [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Az alábbi példa beolvassa az IP-címek *myPublicIP* korábban létrehozott:

```powershell
Get-AzureRmPublicIPAddress `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Name myPublicIP | select IpAddress
```

Beírhatja a nyilvános IP-címet a webböngésző. A webhely jelenik meg, beleértve az állomásnevet, a virtuális gép, amelyek a terheléselosztó felé irányuló forgalom az alábbi példában látható módon:

![Futó IIS-webhely](./media/tutorial-load-balancer/running-iis-website.png)

Tekintse meg a terheléselosztó forgalom szét az alkalmazást futtató összes három virtuális gépet, akkor is kényszerített frissítési a webböngésző.


## <a name="add-and-remove-vms"></a>Hozzá és távolíthat el a virtuális gépek
Szükség lehet a az alkalmazás, például az operációs rendszer frissítéseinek telepítése futó virtuális gépeket karbantartásához. Az alkalmazás megnövekedett forgalom kezelésére, szükség lehet további virtuális gépek hozzáadása. Ez a szakasz bemutatja, hogyan távolítsa el, vagy adja hozzá a virtuális gépek a terheléselosztóról.

### <a name="remove-a-vm-from-the-load-balancer"></a>A virtuális gép eltávolítása a terheléselosztó
A hálózati kártya első [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface), majd állítsa be a *konfigurációja terheléselosztói Háttércímkészletet* a virtuális hálózati adapterről tulajdonságának *$null*. Végül frissítse a virtuális hálózati adaptert.:

```powershell
$nic = Get-AzureRmNetworkInterface `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myNic2
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Tekintse meg a terheléselosztó elosztják a forgalom a fennmaradó két futó virtuális gépeket az alkalmazás akkor is kényszerített frissítési a webböngésző. Karbantartási is képes lemezvizsgálatok elvégzésére, hogy a virtuális Gépen, például az operációs rendszer frissítéseinek telepítése vagy a virtuális gép újraindítása végrehajtása.

### <a name="add-a-vm-to-the-load-balancer"></a>A virtuális gépek hozzáadása a terheléselosztó
Után VM karbantartást végez, vagy ha kapacitás bővítése céljából kell, állítsa a *konfigurációja terheléselosztói Háttércímkészletet* a virtuális hálózati adapterről tulajdonsága a *BackendAddressPool* a [Get-AzureRMLoadBalancer](/powershell/module/azurerm.network/get-azurermloadbalancer):

A load balancer beolvasása:

```powershell
$lb = Get-AzureRMLoadBalancer `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myLoadBalancer 
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy terhelés-kiegyenlítő létrehozott és a virtuális gépek csatolva. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Hozzon létre egy Azure terheléselosztó
> * A health terheléselosztói mintavétel létrehozása
> * Terheléselosztó forgalomra vonatkozó szabályok létrehozása
> * Az egyéni parancsprogramok futtatására szolgáló bővítmény használatával alapvető IIS-webhely létrehozása
> * Virtuális gépek létrehozása és csatolása a terheléselosztó
> * A művelet egy terhelés-kiegyenlítő megtekintése
> * Adja hozzá, és távolítsa el a virtuális gépek a terheléselosztó

A következő oktatóanyag áttekintésével megismerheti, hogyan kezelheti a virtuális gép hálózati továbblépés.

> [!div class="nextstepaction"]
> [Virtuális gépek és virtuális hálózatok kezelése](./tutorial-virtual-network.md)
