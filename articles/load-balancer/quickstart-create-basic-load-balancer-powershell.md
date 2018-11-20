---
title: 'Rövid útmutató: Alapszintű terheléselosztó létrehozása – Azure PowerShell | Microsoft Docs'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre alapszintű terheléselosztót a PowerShell használatával
services: load-balancer
documentationcenter: na
author: KumudD
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/20/2018
ms.author: kumud
ms:custom: mvc
ms.openlocfilehash: c21d5618b3e3223297ddd97dc5c98e5eb8c18c0b
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2018
ms.locfileid: "51974818"
---
# <a name="get-started"></a>Rövid útmutató: Nyilvános terheléselosztó létrehozása az Azure PowerShell használatával
Ez a rövid útmutató bemutatja, hogyan hozhat létre alapszintű terheléselosztót az Azure PowerShell használatával. A terheléselosztó teszteléséhez két, Windows kiszolgálót futtató virtuális gépet helyez üzembe, és elosztja ezek között egy webalkalmazás terhelését.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, ehhez a cikkhez az Azure PowerShell-modul 5.4.1-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral. 

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Mielőtt létrehozhatna egy terheléselosztót, létre kell hoznia egy erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) paranccsal. A következő példában létrehozunk egy *myResourceGroupLB* nevű erőforráscsoportot az *EastUS* helyen:

```azurepowershell-interactive
New-AzureRmResourceGroup `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS"
```
## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet
Az alkalmazás internetes eléréséhez a terheléselosztónak nyilvános IP-címmel kell rendelkeznie. Hozzon létre egy nyilvános IP-címet a [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) paranccsal. Az alábbi példában létrejön egy *myPublicIP* nevű nyilvános IP-cím a *myResourceGroupLB* erőforráscsoportban:

```azurepowershell-interactive
$publicIP = New-AzureRmPublicIpAddress `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS" `
  -AllocationMethod "Static" `
  -Name "myPublicIP"
```
## <a name="create-basic-load-balancer"></a>Alapszintű Load Balancer létrehozása
 Ennek a szakasznak a követésével konfigurálhatja a terheléselosztó előtérbeli IP-címét és háttérbeli címkészletét, majd létrehozhatja az alapszintű terheléselosztót.
 
### <a name="create-frontend-ip"></a>Előtérbeli IP-cím létrehozása
Hozzon létre egy előtérbeli IP-címet a [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) paranccsal. Az alábbi példa követésével létrehoz egy *myFrontEnd* nevű előtérbeli IP-konfigurációt, majd csatolja a *myPublicIP* címet: 

```azurepowershell-interactive
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig `
  -Name "myFrontEnd" `
  -PublicIpAddress $publicIP
```

### <a name="configure-backend-address-pool"></a>A hátércímkészlet konfigurálása

Hozzon létre egy háttércímkészletet a [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) paranccsal. A virtuális gépek ehhez a háttérkészlethez lesznek csatolva a hátralévő lépések során. A következő példában egy *myBackEndPool* nevű háttércímkészlet jön létre:

```azurepowershell-interactive
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
```
### <a name="create-a-health-probe"></a>Állapotminta létrehozása
Ahhoz, hogy a terheléselosztó monitorozhassa az alkalmazás állapotát, állapotmintát kell használni. Az állapotminta dinamikusan hozzáadja a virtuális gépeket a terheléselosztó rotációjához vagy eltávolítja belőle azokat, az állapotellenőrzésekre adott válaszuk alapján. Alapértelmezés szerint a virtuális gépeket a rendszer akkor távolítja el a terheléselosztó elosztásából, ha egy 15 másodperces intervallumon belül két, egymást követő hiba jelentkezik. Az állapotmintákat az alkalmazás egy protokollja vagy adott állapotellenőrzési oldala alapján lehet létrehozni. 

Az alábbi példában egy TCP-mintavétel jön létre. Egyéni HTTP-mintavételt is létrehozhat részletesebb állapotellenőrzések elvégzéséhez. Egyéni HTTP-mintavétel használatakor létre kell hoznia az állapotellenőrzési oldalt, például: *healthcheck.aspx*. A mintavételnek a **HTTP 200 OK** választ kell visszaadnia ahhoz, hogy a terheléselosztó a gazdagépet a rotációban tartsa.

TCP-állapotminta létrehozásához használja az [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig) parancsot. Az alábbi példában egy *myHealthProbe* nevű állapotmintát hozunk létre, amely a *80*-as *HTTP*-porton lévő virtuális gépeket monitorozza:

```azurepowershell-interactive
$probe = New-AzureRmLoadBalancerProbeConfig `
  -Name "myHealthProbe" `
  -RequestPath healthcheck2.aspx `
  -Protocol http `
  -Port 80 `
  -IntervalInSeconds 16 `
  -ProbeCount 2
  ```

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása
A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. Meg kell határoznia az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Ahhoz, hogy csak a megfelelő állapotú virtuális gépek fogadhassanak forgalmat, adja meg a használandó állapotmintát is.

Hozzon létre egy terheléselosztási szabályt az [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig) paranccsal. Az alábbi példa létrehoz egy *myLoadBalancerRule* nevű terheléselosztási szabályt, és elosztja a *80*-as *TCP*-port forgalmát:

```azurepowershell-interactive
$lbrule = New-AzureRmLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

### <a name="create-the-nat-rules"></a>A NAT-szabályok létrehozása

Hozzon NAT-szabályt az [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerinboundnatruleconfig) paranccsal. Az alábbi példák követésével létrehozhatja a *myLoadBalancerRDP1* és *myLoadBalancerRDP2* nevű NAT-szabályokat a háttérkiszolgáló RDP-kapcsolatainak 4221-es és 4222-es portokon történő engedélyezésével:

```azurepowershell-interactive
$natrule1 = New-AzureRmLoadBalancerInboundNatRuleConfig `
-Name 'myLoadBalancerRDP1' `
-FrontendIpConfiguration $frontendIP `
-Protocol tcp `
-FrontendPort 4221 `
-BackendPort 3389

$natrule2 = New-AzureRmLoadBalancerInboundNatRuleConfig `
-Name 'myLoadBalancerRDP2' `
-FrontendIpConfiguration $frontendIP `
-Protocol tcp `
-FrontendPort 4222 `
-BackendPort 3389
```

### <a name="create-load-balancer"></a>Terheléselosztó létrehozása

Hozza létre az alapszintű terheléselosztót a [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) paranccsal. Az alábbi példát követve létrehozhat egy myLoadBalancer nevű nyilvános alapszintű terheléselosztót az előző lépésekben létrehozott előtérbeli IP-konfiguráció, háttérkészlet, állapotminta, terheléselosztási szabály és NAT-szabályok használatával:

```azurepowershell-interactive
$lb = New-AzureRmLoadBalancer `
-ResourceGroupName 'myResourceGroupLB' `
-Name 'MyLoadBalancer' `
-Location 'eastus' `
-FrontendIpConfiguration $frontendIP `
-BackendAddressPool $backendPool `
-Probe $probe `
-LoadBalancingRule $lbrule `
-InboundNatRule $natrule1,$natrule2
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása
Mielőtt üzembe helyezne néhány virtuális gépet és tesztelhetné a terheléselosztót, hozza létre a támogató virtuális hálózati erőforrásokat: a virtuális hálózatot és a virtuális hálózati kártyákat. 

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Hozzon létre egy virtuális hálózatot a [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) paranccsal. Az alábbi példa létrehoz egy *mySubnet* hálózattal rendelkező *myVNet* nevű virtuális hálózatot:

```azurepowershell-interactive
# Create subnet config
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.2.0/24

# Create the virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS" `
  -Name "myVnet" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnetConfig
```
### <a name="create-network-security-group"></a>Hálózati biztonsági csoport létrehozása
Hozzon létre hálózati biztonsági csoportot a virtuális hálózat bejövő kapcsolatainak meghatározásához.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Hálózati biztonságicsoport-szabály létrehozása a 3389-es porhoz
Hozzon létre egy hálózati biztonságicsoport-szabályt a [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) paranccsal, amely engedélyezi az RDP-kapcsolatot a 3389-es porton keresztül.

```azurepowershell-interactive

$rule1 = New-AzureRmNetworkSecurityRuleConfig `
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
Hozzon létre egy hálózati biztonságicsoport-szabályt a [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) paranccsal, amely engedélyezi a bejövő kapcsolatokat a 80-as porton keresztül.

```azurepowershell-interactive
$rule2 = New-AzureRmNetworkSecurityRuleConfig `
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

Hozzon létre egy hálózati biztonsági csoportot a [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) paranccsal.

```azurepowershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'myNetworkSecurityGroup' `
-SecurityRules $rule1,$rule2
```

### <a name="create-nics"></a>Hálózati adapterek létrehozása
Hozzon létre virtuális hálózati adaptereket a [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) paranccsal. Az alábbi példa két virtuális hálózati adaptert hoz létre. (Egy virtuális NIC-t minden virtuális géphez, amelyet létre fog hozni az alkalmazáshoz a következő lépések során). Bármikor létrehozhat további virtuális NIC-ket és virtuális gépeket, és hozzáadhatja őket a terheléselosztóhoz:

```azurepowershell-interactive
# Create NIC for VM1
$nicVM1 = New-AzureRmNetworkInterface `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'MyNic1' `
-LoadBalancerBackendAddressPool $backendPool `
-NetworkSecurityGroup $nsg `
-LoadBalancerInboundNatRule $natrule1 `
-Subnet $vnet.Subnets[0]

# Create NIC for VM2
$nicVM2 = New-AzureRmNetworkInterface `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'MyNic2' `
-LoadBalancerBackendAddressPool $backendPool `
-NetworkSecurityGroup $nsg `
-LoadBalancerInboundNatRule $natrule2 `
-Subnet $vnet.Subnets[0]

```

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása
Az alkalmazás magas rendelkezésre állásának növeléséhez helyezze a virtuális gépeket egy rendelkezésre állási csoportba.

Hozzon létre egy rendelkezésre állási csoportot a [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) paranccsal. Az alábbi példa egy *myAvailabilitySet* nevű rendelkezésre állási csoportot hoz létre:

```azurepowershell-interactive
$availabilitySet = New-AzureRmAvailabilitySet `
  -ResourceGroupName "myResourceGroupLB" `
  -Name "myAvailabilitySet" `
  -Location "EastUS" `
  -Sku aligned `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2
```

A [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) paranccsal állítsa be a virtuális gépek rendszergazdai felhasználónevét és jelszavát:

```azurepowershell-interactive
$cred = Get-Credential
```

Most már létrehozhatja a virtuális gépeket a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) paranccsal. Az alábbi példa létrehoz két virtuális gépet, és a szükséges virtuális hálózati összetevőket, ha azok nem léteznek. Során az alábbi példában a virtuális gépek létrehozása, a korábban létrehozott hálózati adapterek társítva a virtuális gépek hozzá vannak rendelve az azonos virtuális hálózatban óta (*myVnet*) és az alhálózati (*mySubnet*):

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzureRmVm `
        -ResourceGroupName "myResourceGroupLB" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -OpenPorts 80 `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred `
        -AsJob
}
```

Az `-AsJob` paraméter háttérfeladatként létrehozza a virtuális gépet, így használni tudja a PowerShell-parancssorokat. A háttérfeladatok részleteit a `Job` parancsmaggal tekintheti meg. A két virtuális gép létrehozása és konfigurálása néhány percet vesz igénybe.

### <a name="install-iis-with-custom-web-page"></a>Az IIS telepítése egyéni weblappal
 
Telepítése az IIS-t egyéni weblappal mindkét háttérszolgáltatás nyújtó virtuális gépen az alábbiak szerint:

1. Kérje le a terheléselosztó nyilvános IP-címét. Kérje le a terheléselosztó nyilvános IP-címét a `Get-AzureRmPublicIPAdress` paranccsal.

  ```azurepowershell-interactive
    Get-AzureRmPublicIPAddress `
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
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello from" + $env:computername)
    ```
5. Zárja be a *myVM1* géphez létrehozott RDP-kapcsolatot.
6. Hozzon létre egy RDP-kapcsolatot a *myVM2* géphez a `mstsc /v:PublicIpAddress:4222` parancs futtatásával, és ismételje meg a 4. lépést a *VM2* gépnél.

## <a name="test-load-balancer"></a>Terheléselosztó tesztelése
Kérje le a terheléselosztó nyilvános IP-címét a [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) paranccsal. A következő példa a korábban létrehozott *myPublicIP* IP-címét kéri le:

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
  -ResourceGroupName "myResourceGroupLB" `
  -Name "myPublicIP" | select IpAddress
```

A nyilvános IP-címet beírhatja egy böngészőbe. Ekkor megjelenik a webhely, amely tartalmazza annak a virtuális gépnek az eszköznevét, amelyhez a terheléselosztó forgalmat irányított, ahogy az alábbi példában is látható:

![Terheléselosztó tesztelése](media/quickstart-create-basic-load-balancer-powershell/load-balancer-test.png)

Annak megtekintéséhez, hogyan osztja el a terheléselosztó az alkalmazást futtató két virtuális gép között a forgalmat, kényszerítheti a webböngésző frissítését.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroupLB
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy alapszintű terheléselosztót, virtuális gépeket csatolt hozzá, konfigurálta a terheléselosztó forgalmi szabályát és az állapot-mintavételt, majd tesztelte a terheléselosztót. Ha bővebb információra van szüksége az Azure Load Balancerrel kapcsolatban, folytassa az Azure Load Balancerről szóló oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Azure Load Balancer-oktatóanyagok](tutorial-load-balancer-basic-internal-portal.md)
