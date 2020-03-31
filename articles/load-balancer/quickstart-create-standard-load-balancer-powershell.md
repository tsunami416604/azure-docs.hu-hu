---
title: 'Rövid útmutató: Terheléselosztó létrehozása – Azure PowerShell'
titleSuffix: Azure Load Balancer
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre terheléselosztót az Azure PowerShell használatával
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: f169d7694199e496e472a6c32312cf6782270378
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80247214"
---
# <a name="quickstart-create-a-load-balancer-using-azure-powershell"></a>Rövid útmutató: Terheléselosztó létrehozása az Azure PowerShell használatával

Ez a rövid útmutató bemutatja, hogyan hozhat létre Standard Load Balancert az Azure PowerShell használatával. A terheléselosztó teszteléséhez három, Windows server t futtató virtuális gépet (VM)telepít, és a virtuális gépek között egy webalkalmazást kell elosztani. További információk a Standard Load Balancerről: [Mi a Standard Load Balancer?](load-balancer-standard-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, ehhez a cikkhez az Azure PowerShell-modul 5.4.1-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

A terheléselosztó létrehozása előtt létre kell hoznia egy erőforráscsoportot a [New-AzResourceGroup csoporttal.](/powershell/module/az.resources/new-azresourcegroup) A következő példa létrehoz egy *myResourceGroupSLB* nevű erőforráscsoportot az *EastUS* helyen:

```azurepowershell
$rgName='MyResourceGroupSLB'
$location='eastus'
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

Az alkalmazás internetes eléréséhez a terheléselosztónak nyilvános IP-címmel kell rendelkeznie. Hozzon létre egy nyilvános [IP-címet a New-AzPublicIpAddress címmel.](/powershell/module/az.network/new-azpublicipaddress) A következő példa létrehoz egy sajka *nyilvános* IP-címet a *myResourceGroupSLB* erőforráscsoportban:

```azurepowershell
$publicIp = New-AzPublicIpAddress `
 -ResourceGroupName $rgName `
 -Name 'myPublicIP' `
 -Location $location `
 -AllocationMethod static `
 -SKU Standard
```

Az 1.

```azurepowershell
$publicIp = New-AzPublicIpAddress `
 -ResourceGroupName $rgName `
 -Name 'myPublicIP' `
 -Location $location `
 -AllocationMethod static `
 -SKU Standard `
 -zone 1
```

Alapszintű nyilvános IP-cím létrehozásához használható. ```-SKU Basic``` Az alapvető nyilvános IP-k nem kompatibilisek a **standard** terheléselosztóval. A Microsoft azt javasolja, hogy a **Standard** éles számítási feladatokhoz.

> [!IMPORTANT]
> A rövid útmutató többi része feltételezi, hogy a **standard** termékváltozat a fenti termékváltozat-kiválasztási folyamat során kerül kiválasztásra.

## <a name="create-load-balancer"></a>Terheléselosztó létrehozása

Ebben a szakaszban konfigurálja az előtér-IP-címet és a terheléselosztó háttércímkészletét, majd hozza létre a standard terheléselosztót.

### <a name="create-frontend-ip"></a>Előtérbeli IP-cím létrehozása

Hozzon létre egy előtér-IP-t a [New-AzLoadBalancerFrontendIpConfig segítségével.](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) A következő példa létrehoz egy *myFrontEnd* nevű előtér-IP-konfigurációt, és csatolja a *myPublicIP-címet:*

```azurepowershell
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEndPool' -PublicIpAddress $publicIp
```

### <a name="configure-back-end-address-pool"></a>Háttércímkészlet konfigurálása

Hozzon létre egy háttércímkészletet a [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig)segítségével. A virtuális gépek a fennmaradó lépésekben ehhez a háttérkészlethez kapcsolódnak. A következő példa létrehoz egy *myBackEndPool*nevű háttércímkészletet:

```azurepowershell-interactive
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

### <a name="create-a-health-probe"></a>Állapotminta létrehozása
Ahhoz, hogy a terheléselosztó monitorozhassa az alkalmazás állapotát, állapotmintát kell használni. Az állapotminta dinamikusan hozzáadja a virtuális gépeket a terheléselosztó rotációjához vagy eltávolítja belőle azokat, az állapotellenőrzésekre adott válaszuk alapján. Alapértelmezés szerint a virtuális gépeket a rendszer akkor távolítja el a terheléselosztó elosztásából, ha egy 15 másodperces intervallumon belül két, egymást követő hiba jelentkezik. Az állapotmintákat az alkalmazás egy protokollja vagy adott állapotellenőrzési oldala alapján lehet létrehozni.

Az alábbi példában egy TCP-mintavétel jön létre. Egyéni HTTP-mintavételt is létrehozhat részletesebb állapotellenőrzések elvégzéséhez. Egyéni HTTP-mintavétel használatakor létre kell hoznia az állapotellenőrzési oldalt, például: *healthcheck.aspx*. A mintavételnek a **HTTP 200 OK** választ kell visszaadnia ahhoz, hogy a terheléselosztó a gazdagépet a rotációban tartsa.

TCP-állapotminta létrehozásához használja az [Add-AzLoadBalancerProbeConfig programot.](/powershell/module/az.network/add-azloadbalancerprobeconfig) Az alábbi példában egy *myHealthProbe* nevű állapotmintát hozunk létre, amely a *80*-as *HTTP*-porton lévő virtuális gépeket monitorozza:

```azurepowershell
$probe = New-AzLoadBalancerProbeConfig `
 -Name 'myHealthProbe' `
 -Protocol Http -Port 80 `
 -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása
A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. Meg kell határoznia az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Ahhoz, hogy csak a megfelelő állapotú virtuális gépek fogadhassanak forgalmat, adja meg a használandó állapotmintát is.

Hozzon létre egy terheléselosztó szabályt [az Add-AzLoadBalancerRuleConfig segítségével.](/powershell/module/az.network/add-azloadbalancerruleconfig) Az alábbi példa létrehoz egy *myLoadBalancerRule* nevű terheléselosztási szabályt, és elosztja a *80*-as *TCP*-port forgalmát:

```azurepowershell
$rule = New-AzLoadBalancerRuleConfig `
  -Name 'myLoadBalancerRuleWeb' -Protocol Tcp `
  -Probe $probe -FrontendPort 80 -BackendPort 80 `
  -FrontendIpConfiguration $feip `
  -BackendAddressPool $bePool
```

### <a name="create-the-nat-rules"></a>A NAT-szabályok létrehozása

NAT-szabályok létrehozása [a New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig)segítségével. A következő példa létrehozza a *myLoadBalancerRDP1* és *a myLoadBalancerRDP2* nevű NAT-szabályokat, hogy a 4221-es és 4222-es porttal rendelkező háttérkiszolgálókra rdp-kapcsolatokat lehessen létesíteni:

```azurepowershell
$natrule1 = New-AzLoadBalancerInboundNatRuleConfig `
  -Name 'myLoadBalancerRDP1' `
  -FrontendIpConfiguration $feip `
  -Protocol tcp -FrontendPort 4221 `
  -BackendPort 3389

$natrule2 = New-AzLoadBalancerInboundNatRuleConfig `
  -Name 'myLoadBalancerRDP2' `
  -FrontendIpConfiguration $feip `
  -Protocol tcp `
  -FrontendPort 4222 `
  -BackendPort 3389

$natrule3 = New-AzLoadBalancerInboundNatRuleConfig `
  -Name 'myLoadBalancerRDP3' `
  -FrontendIpConfiguration $feip `
  -Protocol tcp `
  -FrontendPort 4223 `
  -BackendPort 3389
```

### <a name="create-load-balancer"></a>Terheléselosztó létrehozása

Hozza létre a Standard Load Balancer standard [terheléselosztót.](/powershell/module/az.network/new-azloadbalancer) A következő példa létrehoz egy nyilvános standard terheléselosztó nevű myLoadBalancer az előtér-IP-konfiguráció, a háttér-készlet, az állapotminta, a terheléselosztási szabály és a NAT-szabályok, amelyek az előző lépésekben létrehozott:

```azurepowershell
$lb = New-AzLoadBalancer `
  -ResourceGroupName $rgName `
  -Name 'MyLoadBalancer' `
  -SKU Standard `
  -Location $location `
  -FrontendIpConfiguration $feip `
  -BackendAddressPool $bepool `
  -Probe $probe `
  -LoadBalancingRule $rule `
  -InboundNatRule $natrule1,$natrule2,$natrule3
```

Alapterhelés-elosztó létrehozására használható. ```-SKU Basic``` A Microsoft azt javasolja, hogy a Standard éles számítási feladatokhoz.

> [!IMPORTANT]
> A rövid útmutató többi része feltételezi, hogy a **standard** termékváltozat a fenti termékváltozat-kiválasztási folyamat során kerül kiválasztásra.

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása
Mielőtt üzembe helyezne néhány virtuális gépet és tesztelhetné a terheléselosztót, hozza létre a támogató virtuális hálózati erőforrásokat: a virtuális hálózatot és a virtuális hálózati kártyákat. 

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Hozzon létre egy virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Az alábbi példa létrehoz egy *mySubnet* hálózattal rendelkező *myVNet* nevű virtuális hálózatot:

```azurepowershell
# Create subnet config
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.2.0/24

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroupSLB" `
  -Location $location `
  -Name "myVnet" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnetConfig
```
### <a name="create-public-ip-addresses-for-the-vms"></a>Nyilvános IP-címek létrehozása a virtuális gépekhez

A virtuális gépek rdp-kapcsolaton keresztül eléréséhez nyilvános IP-cím szükséges a virtuális gépekhez. Mivel ebben a forgatókönyvben szabványos terheléselosztót használ, szabványos nyilvános IP-címeket kell létrehoznia a [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)címcím-címmel rendelkező virtuális gépekhez.

```azurepowershell
$RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $RgName `
  -Location $location  `
  -SKU Standard `
  -AllocationMethod static
 

$RdpPublicIP_2 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_2" `
  -ResourceGroupName $RgName `
  -Location $location  `
  -SKU Standard `
  -AllocationMethod static


$RdpPublicIP_3 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_3" `
  -ResourceGroupName $RgName `
  -Location $location  `
  -SKU Standard `
  -AllocationMethod static

```

Alapszintű nyilvános IP-k létrehozásához használható. ```-SKU Basic``` A Microsoft azt javasolja, hogy a Standard éles számítási feladatokhoz.

### <a name="create-network-security-group"></a>Hálózati biztonsági csoport létrehozása
Hozzon létre hálózati biztonsági csoportot a virtuális hálózat bejövő kapcsolatainak meghatározásához.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Hálózati biztonságicsoport-szabály létrehozása a 3389-es porhoz
Hozzon létre egy hálózati biztonsági csoportszabályt, amely engedélyezi az RDP-kapcsolatokat a 3389-es porton keresztül a [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)segítségével.

```azurepowershell

$rule1 = New-AzNetworkSecurityRuleConfig -Name 'myNetworkSecurityGroupRuleRDP' -Description 'Allow RDP' `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 1000 `
  -SourceAddressPrefix Internet -SourcePortRange * `
  -DestinationAddressPrefix * -DestinationPortRange 3389
```

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Hálózati biztonságicsoport-szabály létrehozása a 80-as porhoz
Hozzon létre egy hálózati biztonsági csoportszabályt, amely engedélyezi a bejövő kapcsolatokat a 80-as porton keresztül a [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)segítségével.

```azurepowershell
$rule2 = New-AzNetworkSecurityRuleConfig -Name 'myNetworkSecurityGroupRuleHTTP' -Description 'Allow HTTP' `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 2000 `
  -SourceAddressPrefix Internet -SourcePortRange * `
  -DestinationAddressPrefix * -DestinationPortRange 80
```

#### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hozzon létre egy hálózati biztonsági csoportot a [New-AzNetworkSecurityGroup csoporttal.](/powershell/module/az.network/new-aznetworksecuritygroup)

```azurepowershell
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $RgName -Location $location `
-Name 'myNetworkSecurityGroup' -SecurityRules $rule1,$rule2
```

### <a name="create-nics"></a>Hálózati adapterek létrehozása
Hozzon létre virtuális hálózati adaptereket, és társítani a [new-aznetworkinterface](/powershell/module/az.network/new-aznetworkinterface)korábbi lépéseiben létrehozott nyilvános IP-címekkel és hálózati biztonsági csoportokkal. Az alábbi példa három virtuális NIC-t hoz létre. (Egy virtuális NIC-t minden virtuális géphez, amelyet létre fog hozni az alkalmazáshoz a következő lépések során). Bármikor létrehozhat további virtuális NIC-ket és virtuális gépeket, és hozzáadhatja őket a terheléselosztóhoz:

```azurepowershell
# Create NIC for VM1
$nicVM1 = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic1' -PublicIpAddress $RdpPublicIP_1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg `
  -LoadBalancerInboundNatRule $natrule1 -Subnet $vnet.Subnets[0]

$nicVM2 = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic2' -PublicIpAddress $RdpPublicIP_2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg `
  -LoadBalancerInboundNatRule $natrule2 -Subnet $vnet.Subnets[0]

$nicVM3 = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic3' -PublicIpAddress $RdpPublicIP_3 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg `
  -LoadBalancerInboundNatRule $natrule3 -Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

A [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) paranccsal állítsa be a virtuális gépek rendszergazdai felhasználónevét és jelszavát:

```azurepowershell
$cred = Get-Credential
```

Most már létrehozhatja a virtuális gépeket a New-AzVM.Now you can create the VMs with [New-AzVM.](/powershell/module/az.compute/new-azvm) A következő példa két virtuális gépet és a szükséges virtuális hálózati összetevőket hoz létre, ha még nem léteznek. Ebben a példában az előző lépésben létrehozott hálózati adapterek (*MyNic1*, *MyNic2*és *MyNic3*) a *myVM1*, *myVM2*és *vm3*virtuális gépekhez vannak rendelve. Emellett a hálózati adapterek a terheléselosztó háttérkészletéhez vannak társítva, a virtuális gépek automatikusan hozzáadódnak a háttérkészlethez.

```azurepowershell

# ############## VM1 ###############

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM1' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM1' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
 | Add-AzVMNetworkInterface -Id $nicVM1.Id

# Create a virtual machine
$vm1 = New-AzVM -ResourceGroupName $rgName -Zone 1 -Location $location -VM $vmConfig

# ############## VM2 ###############

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM2' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM2' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
 | Add-AzVMNetworkInterface -Id $nicVM2.Id

# Create a virtual machine
$vm2 = New-AzVM -ResourceGroupName $rgName -Zone 2 -Location $location -VM $vmConfig

# ############## VM3 ###############

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM3' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM3' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
| Add-AzVMNetworkInterface -Id $nicVM3.Id

# Create a virtual machine
$vm3 = New-AzVM -ResourceGroupName $rgName -Zone 3 -Location $location -VM $vmConfig
```

A három virtuális gép létrehozása és konfigurálása néhány percet vesz igénybe.

### <a name="install-iis-with-a-custom-web-page"></a>Az IIS telepítése egyéni weblappal

Telepítse az IIS-t egyéni weblappal mindkét háttérprogramra az alábbiak szerint:

1. A három virtuális gép nyilvános IP-címének begyűjtése a használatával. `Get-AzPublicIPAddress`

   ```azurepowershell
     $vm1_rdp_ip = (Get-AzPublicIPAddress -ResourceGroupName $rgName -Name "RdpPublicIP_1").IpAddress
     $vm2_rdp_ip = (Get-AzPublicIPAddress -ResourceGroupName $rgName -Name "RdpPublicIP_2").IpAddress
     $vm3_rdp_ip = (Get-AzPublicIPAddress -ResourceGroupName $rgName -Name "RdpPublicIP_3").IpAddress
    ```
2. Távoli asztali kapcsolatok létrehozása *a myVM1,* *myVM2*és *myVM3* segítségével a virtuális gépek nyilvános IP-címeivel az alábbiak szerint: 

   ```azurepowershell    
     mstsc /v:$vm1_rdp_ip
     mstsc /v:$vm2_rdp_ip
     mstsc /v:$vm3_rdp_ip
   
    ```

3. Adja meg az rdp-munkamenet elindításához minden virtuális gép hitelesítő adatait.
4. Indítsa el a Windows PowerShellt minden virtuális gépen, és a következő parancsokkal telepítse az IIS-kiszolgálót, és frissítse az alapértelmezett htm fájlt.

    ```azurepowershell
    # Install IIS
     Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from host " + $env:computername)
    ```

5. Zárja be az RDP-kapcsolatokat a *myVM1,* *a myVM2*és *a myVM3 rendszerrel.*


## <a name="test-load-balancer"></a>Terheléselosztó tesztelése
Szerezze be a terheléselosztó nyilvános IP-címét a [Get-AzPublicIPAddress segítségével.](/powershell/module/az.network/get-azpublicipaddress) A következő példa a korábban létrehozott *myPublicIP* IP-címét kéri le:

```azurepowershell
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupSLB" `
  -Name "myPublicIP" | select IpAddress
```

A nyilvános IP-címet beírhatja egy böngészőbe. Ekkor megjelenik a webhely, amely tartalmazza annak a virtuális gépnek az eszköznevét, amelyhez a terheléselosztó forgalmat irányított, ahogy az alábbi példában is látható:

![Terheléselosztó tesztelése](media/quickstart-create-basic-load-balancer-powershell/load-balancer-test.png)

Annak megtekintéséhez, hogyan osztja el a terheléselosztó az alkalmazást futtató három virtuális gép között a forgalmat, kényszerítheti a webböngésző frissítését. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség rá, az [Eltávolítás-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) paranccsal eltávolíthatja az erőforráscsoportot, a virtuális gépés az összes kapcsolódó erőforrást.

```azurepowershell
Remove-AzResourceGroup -Name myResourceGroupSLB
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy standard terheléselosztót, virtuális gépeket csatolt hozzá, konfigurálta a terheléselosztó forgalmi szabályt, az állapotmintát, majd tesztelte a terheléselosztót. Ha többet szeretne megtudni az Azure Load Balancer-ről, folytassa az [Azure Load Balancer oktatóanyagokkal.](tutorial-load-balancer-standard-public-zone-redundant-portal.md)

További információ a [terheléselosztóról és a rendelkezésre állási zónákról.](load-balancer-standard-availability-zones.md)
