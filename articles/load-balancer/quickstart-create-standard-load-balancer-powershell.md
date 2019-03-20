---
title: 'Gyors útmutató: Egy Standard Load Balancer létrehozása – Azure PowerShell-lel'
titlesuffix: Azure Load Balancer
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre Standard Load Balancert a PowerShell használatával
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Standard Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: kumud
ms:custom: seodec18
ms.openlocfilehash: 87c1d047e783715b3a5beee4604e064322f965dd
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58101887"
---
# <a name="get-started"></a>Gyors útmutató: Azure PowerShell-lel Standard Load Balancer létrehozása

Ez a rövid útmutató bemutatja, hogyan hozhat létre Standard Load Balancert az Azure PowerShell használatával. A terheléselosztó teszteléséhez két, Windows kiszolgálót futtató virtuális gépet helyez üzembe, és elosztja ezek között egy webalkalmazás terhelését. További információk a Standard Load Balancerről: [Mi a Standard Load Balancer?](load-balancer-standard-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, ehhez a cikkhez az Azure PowerShell-modul 5.4.1-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

A terheléselosztó létrehozásához, létre kell hoznia egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). A következő példában létrehozunk egy *myResourceGroupLB* nevű erőforráscsoportot az *EastUS* helyen:

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS"
```

## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

Az alkalmazás internetes eléréséhez a terheléselosztónak nyilvános IP-címmel kell rendelkeznie. Hozzon létre egy nyilvános IP-címet [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Az alábbi példában létrejön egy *myPublicIP* nevű nyilvános IP-cím a *myResourceGroupLB* erőforráscsoportban:

```azurepowershell-interactive
$publicIP = New-AzPublicIpAddress `
-Name "myPublicIP" `
-ResourceGroupName "myResourceGroupLB" `
-Location "EastUS" `
-Sku "Standard" `
-AllocationMethod "Static"
```

## <a name="create-standard-load-balancer"></a>Standard Load Balancer létrehozása

Ebben a szakaszban az előtérbeli IP-címet és a háttér-címkészletet a load Balancer konfigurálása, és a Standard Load Balancer létrehozása a majd.

### <a name="create-front-end-ip"></a>Előtérbeli IP-cím létrehozása

Hozzon létre egy előtér-IP-Címmel rendelkező [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). A következő példában létrehozunk egy nevű előtérbeli IP-konfigurációt *myFrontEnd* , és csatolja a *myPublicIP* címe:

```azurepowershell-interactive
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEnd" `
  -PublicIpAddress $publicIP
```

### <a name="configure-back-end-address-pool"></a>Háttér-címkészlet konfigurálása

A háttér-címkészlet létrehozása [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). A virtuális gépek csatlakoztatása a hátralévő lépések során a háttérkészlethez. A következő példában létrehozunk egy háttér-címkészletet *myBackEndPool*:

```azurepowershell-interactive
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
```

### <a name="create-a-health-probe"></a>Állapotminta létrehozása
Ahhoz, hogy a terheléselosztó monitorozhassa az alkalmazás állapotát, állapotmintát kell használni. Az állapotminta dinamikusan hozzáadja a virtuális gépeket a terheléselosztó rotációjához vagy eltávolítja belőle azokat, az állapotellenőrzésekre adott válaszuk alapján. Alapértelmezés szerint a virtuális gépeket a rendszer akkor távolítja el a terheléselosztó elosztásából, ha egy 15 másodperces intervallumon belül két, egymást követő hiba jelentkezik. Az állapotmintákat az alkalmazás egy protokollja vagy adott állapotellenőrzési oldala alapján lehet létrehozni.

Az alábbi példában egy TCP-mintavétel jön létre. Egyéni HTTP-mintavételt is létrehozhat részletesebb állapotellenőrzések elvégzéséhez. Egyéni HTTP-mintavétel használatakor létre kell hoznia az állapotellenőrzési oldalt, például: *healthcheck.aspx*. A mintavételnek a **HTTP 200 OK** választ kell visszaadnia ahhoz, hogy a terheléselosztó a gazdagépet a rotációban tartsa.

TCP-állapotminta létrehozásához használja [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig). Az alábbi példában egy *myHealthProbe* nevű állapotmintát hozunk létre, amely a *80*-as *HTTP*-porton lévő virtuális gépeket monitorozza:

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig `
  -Name "myHealthProbe" `
  -RequestPath healthcheck2.aspx `
  -Protocol http `
  -Port 80 `
  -IntervalInSeconds 16 `
  -ProbeCount 2
  ```

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása
A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. Meg kell határoznia az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Ahhoz, hogy csak a megfelelő állapotú virtuális gépek fogadhassanak forgalmat, adja meg a használandó állapotmintát is.

Hozzon létre egy terheléselosztó-szabályt az [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). Az alábbi példa létrehoz egy *myLoadBalancerRule* nevű terheléselosztási szabályt, és elosztja a *80*-as *TCP*-port forgalmát:

```azurepowershell-interactive
$lbrule = New-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

### <a name="create-the-nat-rules"></a>A NAT-szabályok létrehozása

Hozzon létre a NAT-szabályokat a [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig). A következő példában létrehozunk nevű NAT-szabályok *myLoadBalancerRDP1* és *myLoadBalancerRDP2* RDP-kapcsolatai a háttérkiszolgálók 4221 és 4222 port engedélyezéséhez:

```azurepowershell-interactive
$natrule1 = New-AzLoadBalancerInboundNatRuleConfig `
-Name 'myLoadBalancerRDP1' `
-FrontendIpConfiguration $frontendIP `
-Protocol tcp `
-FrontendPort 4221 `
-BackendPort 3389

$natrule2 = New-AzLoadBalancerInboundNatRuleConfig `
-Name 'myLoadBalancerRDP2' `
-FrontendIpConfiguration $frontendIP `
-Protocol tcp `
-FrontendPort 4222 `
-BackendPort 3389
```

### <a name="create-load-balancer"></a>Terheléselosztó létrehozása

A Standard Load Balancer-létrehozása [új AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). Az alábbi példa létrehoz egy nyilvános Standard Load Balancer használatával az előtérbeli IP-konfigurációhoz, háttérkészlet, állapotadat-mintavétel, a terheléselosztási szabály és NAT-szabályok, amelyek az előző lépésekben létrehozott myLoadBalancer nevű:

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
-ResourceGroupName 'myResourceGroupLB' `
-Name 'MyLoadBalancer' `
-Location 'eastus' `
-FrontendIpConfiguration $frontendIP `
-BackendAddressPool $backendPool `
-Probe $probe `
-LoadBalancingRule $lbrule `
-InboundNatRule $natrule1,$natrule2 `
-sku Standard
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása
Mielőtt üzembe helyezne néhány virtuális gépet és tesztelhetné a terheléselosztót, hozza létre a támogató virtuális hálózati erőforrásokat: a virtuális hálózatot és a virtuális hálózati kártyákat. 

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
A virtuális hálózat létrehozása [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Az alábbi példa létrehoz egy *mySubnet* hálózattal rendelkező *myVNet* nevű virtuális hálózatot:

```azurepowershell-interactive
# Create subnet config
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.2.0/24

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS" `
  -Name "myVnet" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnetConfig
```

### <a name="create-network-security-group"></a>Hálózati biztonsági csoport létrehozása
Hozzon létre hálózati biztonsági csoportot a virtuális hálózat bejövő kapcsolatainak meghatározásához.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Hálózati biztonságicsoport-szabály létrehozása a 3389-es porhoz
Hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt az RDP-kapcsolatok engedélyezése a 3389-es porton keresztül [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell-interactive

$rule1 = New-AzNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleRDP' `
-Description 'Allow RDP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 1000 `
-SourceAddressPrefix Internet `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 3389
```

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Hálózati biztonságicsoport-szabály létrehozása a 80-as porhoz
Hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt a bejövő kapcsolatok engedélyezéséhez a 80-as porton keresztül [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleHTTP' `
-Description 'Allow HTTP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 2000 `
-SourceAddressPrefix Internet `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 80
```

#### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hozzon létre egy hálózati biztonsági csoport [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'myNetworkSecurityGroup' `
-SecurityRules $rule1,$rule2
```

### <a name="create-nics"></a>Hálózati adapterek létrehozása
A létrehozott virtuális hálózati adapter létrehozása [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). Az alábbi példa két virtuális hálózati adaptert hoz létre. (Egy virtuális NIC-t minden virtuális géphez, amelyet létre fog hozni az alkalmazáshoz a következő lépések során). Bármikor létrehozhat további virtuális NIC-ket és virtuális gépeket, és hozzáadhatja őket a terheléselosztóhoz:

```azurepowershell-interactive
# Create NIC for VM1
$nicVM1 = New-AzNetworkInterface `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'MyVM1' `
-LoadBalancerBackendAddressPool $backendPool `
-NetworkSecurityGroup $nsg `
-LoadBalancerInboundNatRule $natrule1 `
-Subnet $vnet.Subnets[0]

# Create NIC for VM2
$nicVM2 = New-AzNetworkInterface `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'MyVM2' `
-LoadBalancerBackendAddressPool $backendPool `
-NetworkSecurityGroup $nsg `
-LoadBalancerInboundNatRule $natrule2 `
-Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

A [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) paranccsal állítsa be a virtuális gépek rendszergazdai felhasználónevét és jelszavát:

```azurepowershell-interactive
$cred = Get-Credential
```

Most már létrehozhatja a virtuális gépeket a [New-azvm parancsmag](/powershell/module/az.compute/new-azvm). Az alábbi példa létrehoz két virtuális gépet, és a szükséges virtuális hálózati összetevőket, ha azok nem léteznek. Ebben a példában a hálózati adaptereket (*VM1* és *VM2*) az előző lépésben létrehozott virtuális gépek automatikusan rendelt *VM1* és *VM2*mivel ugyanolyan nevű, és ugyanahhoz a virtuális hálózathoz rendelt (*myVnet*) és az alhálózati (*mySubnet*). Ezenkívül a hálózati adapterek társítva a terheléselosztó háttérkészlethez, mivel a virtuális gépek rendszer automatikusan hozzáadja a háttérkészlethez.

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupLB" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -OpenPorts 80 `
        -Credential $cred `
        -AsJob
}
```

Az `-AsJob` paraméter háttérfeladatként létrehozza a virtuális gépet, így használni tudja a PowerShell-parancssorokat. A háttérfeladatok részleteit a `Job` parancsmaggal tekintheti meg. A két virtuális gép létrehozása és konfigurálása néhány percet vesz igénybe.

### <a name="install-iis-with-custom-web-page"></a>Az IIS telepítése egyéni weblappal

Az IIS egy egyéni lapját két háttérbeli virtuális géppel együtt a következőképpen telepítheti:

1. Kérje le a terheléselosztó nyilvános IP-címét. Kérje le a terheléselosztó nyilvános IP-címét a `Get-AzPublicIPAddress` paranccsal.

   ```azurepowershell-interactive
    Get-AzPublicIPAddress `
    -ResourceGroupName "myResourceGroupLB" `
    -Name "myPublicIP" | select IpAddress
   ```
2. Hozzon létre egy távoli asztali kapcsolatot a VM1 géphez az előző lépésben beszerzett nyilvános IP-címmel. 

   ```azurepowershell-interactive

      mstsc /v:PublicIpAddress:4221  
  
   ```

3. Adja meg a hitelesítő adatokat a *VM1* géphez az RDP-munkamenet elindításához.
4. Indítsa el a Windows PowerShellt a VM1 gépen, majd telepítse az IIS-kiszolgálót, és frissítse az alapértelmezett htm-fájlt az alábbi parancsokkal.

    ```azurepowershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from host" + $env:computername)
    ```

5. Zárja be a *myVM1* géphez létrehozott RDP-kapcsolatot.
6. Hozzon létre egy RDP-kapcsolatot a *myVM2* géphez a `mstsc /v:PublicIpAddress:4222` parancs futtatásával, és ismételje meg a 4. lépést a *VM2* gépnél.

## <a name="test-load-balancer"></a>Terheléselosztó tesztelése
A terheléselosztó a nyilvános IP-cím beszerzése [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). A következő példa a korábban létrehozott *myPublicIP* IP-címét kéri le:

```azurepowershell-interactive
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupLB" `
  -Name "myPublicIP" | select IpAddress
```

A nyilvános IP-címet beírhatja egy böngészőbe. Ekkor megjelenik a webhely, amely tartalmazza annak a virtuális gépnek az eszköznevét, amelyhez a terheléselosztó forgalmat irányított, ahogy az alábbi példában is látható:

![Terheléselosztó tesztelése](media/quickstart-create-basic-load-balancer-powershell/load-balancer-test.png)

Annak megtekintéséhez, hogyan osztja el a terheléselosztó az alkalmazást futtató két virtuális gép között a forgalmat, kényszerítheti a webböngésző frissítését. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhatja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupLB
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy standard terheléselosztót, virtuális gépeket csatolt hozzá, konfigurálta a terheléselosztó forgalmi szabályát és az állapot-mintavételt, majd tesztelte a terheléselosztót. Ha bővebb információra van szüksége az Azure Load Balancerrel kapcsolatban, folytassa az Azure Load Balancerről szóló oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Azure Load Balancer-oktatóanyagok](tutorial-load-balancer-basic-internal-portal.md)
