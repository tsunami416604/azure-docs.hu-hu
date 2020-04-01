---
title: IPv6-alapú kétverű alkalmazás telepítése – Standard terheléselosztó – PowerShell
titlesuffix: Azure Virtual Network
description: Ez a cikk bemutatja, hogyan telepíti az IPv6 kétverézésű alkalmazást standard terheléselosztóval az Azure Powershell használatával az Azure Powershell használatával.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2020
ms.author: kumud
ms.openlocfilehash: d6b61e27324220fc78ace3e964aed98f9ba114d3
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420933"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure---powershell"></a>IPv6-alapú kétverű alkalmazás üzembe helyezése az Azure-ban – PowerShell

Ez a cikk bemutatja, hogyan telepíthet egy kétverű (IPv4 + IPv6) alkalmazást az Azure-ban, amely kétverű virtuális hálózatot és alhálózatot, egy két (IPv4 + IPv6) előtér-konfigurációval rendelkező hálózati adapterrel rendelkező virtuális gépet, hálózati biztonsági csoportot és nyilvános IP-címeket tartalmaz.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a PowerShellt, ez a cikk az Azure PowerShell-modul 6.9.0-s vagy újabb verzióját igényli. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

A kétverkező virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)segítségével. A következő példa létrehoz egy *myRGDualStack* nevű erőforráscsoportot a *keleti helyen:*

```azurepowershell-interactive
   $rg = New-AzResourceGroup `
  -ResourceGroupName "dsRG1"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>IPv4- és IPv6-alapú nyilvános IP-címek létrehozása
A virtuális gépek internetről való eléréséhez iPv4- és IPv6-nyilvános IP-címekre van szükség a terheléselosztóhoz. Nyilvános IP-címek létrehozása a [New-AzPublicIpAddress címmel.](/powershell/module/az.network/new-azpublicipaddress) A következő példa *dsPublicIP_v4* és *dsPublicIP_v6* nevű IPv4-és IPv6-nyilvános IP-címet hoz létre a *dsRG1* erőforráscsoportban:

```azurepowershell-interactive
$PublicIP_v4 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v4" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4 `
  -Sku Standard
  
$PublicIP_v6 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6 `
  -Sku Standard
```
Ha RDP-kapcsolaton keresztül szeretné elérni a virtuális gépeket, hozzon létre egy IPV4 nyilvános IP-címeket a [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)programmal rendelkező virtuális gépekhez.

```azurepowershell-interactive
  $RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -Sku Standard `
  -IpAddressVersion IPv4
  
  $RdpPublicIP_2 = New-AzPublicIpAddress `
   -Name "RdpPublicIP_2" `
   -ResourceGroupName $rg.ResourceGroupName `
   -Location $rg.Location  `
   -AllocationMethod Static `
   -Sku Standard `
   -IpAddressVersion IPv4
```

## <a name="create-standard-load-balancer"></a>Standard Load Balancer létrehozása

Ebben a szakaszban konfigurálja a két előtér-IP-címet (IPv4 és IPv6) és a terheléselosztó háttércímkészletét, majd hozzon létre egy szabványos terheléselosztót.

### <a name="create-front-end-ip"></a>Előtér-IP létrehozása

Hozzon létre egy előtér-IP-t a [New-AzLoadBalancerFrontendIpConfig segítségével.](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) A következő példa *dsLbFrontEnd_v4* és *dsLbFrontEnd_v6*nevű IPv4 és IPv6 előtér-IP-konfigurációkat hoz létre:

```azurepowershell-interactive
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PublicIpAddress $PublicIP_v4

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

```

### <a name="configure-back-end-address-pool"></a>Háttércímkészlet konfigurálása

Hozzon létre egy háttércímkészletet a [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig)segítségével. A virtuális gépek a fennmaradó lépésekben ehhez a háttérkészlethez kapcsolódnak. A következő példa *dsLbBackEndPool_v4* és *dsLbBackEndPool_v6* nevű háttér-címkészleteket hoz létre, amelyek az IPV4 és az IPv6 hálózati adapterkonfigurációval rendelkező virtuális gépeket is tartalmazzák:

```azurepowershell-interactive
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v6"
```
### <a name="create-a-health-probe"></a>Állapotminta létrehozása
Az [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) használatával hozzon létre egy állapotminta a virtuális gépek állapotának figyeléséhez.
```azurepowershell
$probe = New-AzLoadBalancerProbeConfig -Name MyProbe -Protocol tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2
```
### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. Meg kell határoznia az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Győződjön meg arról, hogy csak a kifogástalan állapotú virtuális gépek kapforgalmat, opcionálisan megadhat egy állapotminta. Az alapszintű terheléselosztó iPv4-mintavételt használ a virtuális gépek IPv4- és IPv6-végpontjainak állapotának felméréséhez. A standard terheléselosztó támogatja a kifejezetten IPv6-állapotpróbákat.

Hozzon létre egy terheléselosztó szabályt [az Add-AzLoadBalancerRuleConfig segítségével.](/powershell/module/az.network/add-azloadbalancerruleconfig) A következő példa dsLBrule_v4 és *dsLBrule_v6* nevű terheléselosztó-szabályokat hoz *létre,* és kiegyenlíti a *80-as* *TCP-port* és az IPv4-alapú előtér IP-konfigurációi közötti forgalmat:

```azurepowershell-interactive
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
   -probe $probe

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
   -probe $probe
```

### <a name="create-load-balancer"></a>Terheléselosztó létrehozása

Hozzon létre egy standard terheléselosztót a [New-AzLoadBalancer segítségével.](/powershell/module/az.network/new-azloadbalancer) A következő példa létrehoz egy *myLoadBalancer* nevű nyilvános standard terheléselosztót az IPv4 és IPv6 előtér-IP-konfigurációk, háttérkészletek és terheléselosztási szabályok használatával, amelyeket az előző lépésekben hozott létre:

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "MyLoadBalancer" `
-Sku "Standard" `
-FrontendIpConfiguration $frontendIPv4,$frontendIPv6 `
-BackendAddressPool $backendPoolv4,$backendPoolv6 `
-LoadBalancingRule $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása
Mielőtt üzembe helyezne néhány virtuális gépet, és tesztelheti a kiegyensúlyozót, létre kell hoznia támogató hálózati erőforrásokat – rendelkezésre állási készletet, hálózati biztonsági csoportot, virtuális hálózatot és virtuális hálózati adaptereket. 
### <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása
Az alkalmazás magas rendelkezésre állásának növeléséhez helyezze a virtuális gépeket egy rendelkezésre állási csoportba.

Hozzon létre egy rendelkezésre állási készletet a [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset)segítségével. Az alábbi példa egy *myAvailabilitySet* nevű rendelkezésre állási csoportot hoz létre:

```azurepowershell-interactive
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hozzon létre egy hálózati biztonsági csoportot a virtuális hálózat bejövő és kimenő kommunikációját szabályozó szabályokhoz.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Hálózati biztonságicsoport-szabály létrehozása a 3389-es porhoz

Hozzon létre egy hálózati biztonsági csoportszabályt, amely engedélyezi az RDP-kapcsolatokat a 3389-es porton keresztül a [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)segítségével.

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleRDP' `
-Description 'Allow RDP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 100 `
-SourceAddressPrefix * `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 3389
```
#### <a name="create-a-network-security-group-rule-for-port-80"></a>Hálózati biztonságicsoport-szabály létrehozása a 80-as porhoz

Hozzon létre egy hálózati biztonsági csoportszabályt, amely lehetővé teszi az internetkapcsolatot a 80-as porton keresztül a [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)segítségével.

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleHTTP' `
  -Description 'Allow HTTP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange 80 `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80
```
#### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hozzon létre egy hálózati biztonsági csoportot a [New-AzNetworkSecurityGroup csoporttal.](/powershell/module/az.network/new-aznetworksecuritygroup)

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "dsNSG1"  `
-SecurityRules $rule1,$rule2
```
### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). A következő példa létrehoz egy virtuális hálózat nevű *dsVnet* *a mySubnet:*

```azurepowershell-interactive
# Create dual stack subnet
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "dsSubnet" `
-AddressPrefix "10.0.0.0/24","ace:cab:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","ace:cab:deca::/48"  `
  -Subnet $subnet
```

### <a name="create-nics"></a>Hálózati adapterek létrehozása

Hozzon létre virtuális hálózati adaptereket a [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)segítségével. A következő példa két virtuális hálózati adaptert hoz létre iPv4- és IPv6-konfigurációkkal egyaránt. (Egy virtuális NIC-t minden virtuális géphez, amelyet létre fog hozni az alkalmazáshoz a következő lépések során).

```azurepowershell-interactive
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_1
      
  $Ip6Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp6Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv6 `
    -LoadBalancerBackendAddressPool $backendPoolv6
    
  $NIC_1 = New-AzNetworkInterface `
    -Name "dsNIC1" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 
    
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_2  

  $NIC_2 = New-AzNetworkInterface `
    -Name "dsNIC2" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 

```

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

A [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) paranccsal állítsa be a virtuális gépek rendszergazdai felhasználónevét és jelszavát:

```azurepowershell-interactive
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VMs."
```

Most már létrehozhatja a virtuális gépeket a New-AzVM.Now you can create the VMs with [New-AzVM.](/powershell/module/az.compute/new-azvm) A következő példa két virtuális gépet és a szükséges virtuális hálózati összetevőket hoz létre, ha még nem léteznek. 

```azurepowershell-interactive
$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2019-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null 
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1 

$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null 
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2
```

## <a name="determine-ip-addresses-of-the-ipv4-and-ipv6-endpoints"></a>Az IPv4- és Az IPv6-végpontok IP-címének meghatározása
Az erőforráscsoport összes hálózati illesztőobjektumának lefolytatása a telepítésben `get-AzNetworkInterface`használt IP-cím összegzéséhez. A terheléselosztó előtér-címeit is lekell szereznie az IPv4- és IPv6-végpontokon. `get-AzpublicIpAddress`

```azurepowershell-interactive
$rgName= "dsRG1"
$NICsInRG= get-AzNetworkInterface -resourceGroupName $rgName 
write-host `nSummary of IPs in this Deployment: 
write-host ******************************************
foreach ($NIC in $NICsInRG) {
 
    $VMid= $NIC.virtualmachine.id 
    $VMnamebits= $VMid.split("/") 
    $VMname= $VMnamebits[($VMnamebits.count-1)] 
    write-host `nPrivate IP addresses for $VMname 
    $IPconfigsInNIC= $NIC.IPconfigurations 
    foreach ($IPconfig in $IPconfigsInNIC) {
 
        $IPaddress= $IPconfig.privateipaddress 
        write-host "    "$IPaddress 
        IF ($IPconfig.PublicIpAddress.ID) {
 
            $IDbits= ($IPconfig.PublicIpAddress.ID).split("/")
            $PipName= $IDbits[($IDbits.count-1)]
            $PipObject= get-azPublicIpAddress -name $PipName -resourceGroup $rgName
            write-host "    "RDP address:  $PipObject.IpAddress
                 }
         }
 }
 
 
 
  write-host `nPublic IP addresses on Load Balancer:
 
  (get-AzpublicIpAddress -resourcegroupname $rgName | where { $_.name -notlike "RdpPublicIP*" }).IpAddress
```
Az alábbi ábrán egy mintakimenet látható, amely a két virtuális gép privát IPv4- és IPv6-címét, valamint a terheléselosztó előtétes IPv4- és IPv6-IP-címeit sorolja fel.

![A kettős verem (IPv4/IPv6) alkalmazás azure-beli telepítésének IP-összegzése](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-application-summary.png)

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Az IPv6 kétletétes virtuális hálózatának megtekintése az Azure Portalon
Az IPv6 kettős veremű virtuális hálózatot az Azure Portalon az alábbiak szerint tekintheti meg:
1. A portál keresősávján írja be a *dsVnet*.
2. Amikor **a dsVnet** megjelenik a keresési eredmények között, jelölje ki azt. Ez elindítja a *dsVnet*nevű kettős verem virtuális hálózat **áttekintése** lapját. A kétverű virtuális hálózat a két hálózati adaptert jeleníti meg, amelyek iPv4- és IPv6-konfigurációkkal is rendelkeznek, és amelyek a *dsSubnet*nevű kettős veremalhálózatban találhatók.

  ![IPv6 kettős halmozott virtuális hálózat az Azure-ban](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség rá, az [Eltávolítás-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) paranccsal eltávolíthatja az erőforráscsoportot, a virtuális gépés az összes kapcsolódó erőforrást.

```azurepowershell-interactive
Remove-AzResourceGroup -Name dsRG1
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy standard terheléselosztót kettős előtér-IP-konfigurációval (IPv4 és IPv6). Két virtuális gépet is létrehozott, amelyek két IP-konfigurációval (IPV4 + IPv6) rendelkező hálózati adaptereket tartalmaztak, amelyeket a terheléselosztó háttérkészletéhez adtak hozzá. Ha többet szeretne tudni az IPv6-támogatásról az Azure virtuális hálózatokban, olvassa el [a Mi az IPv6 az Azure virtuális hálózathoz című témakörben?](ipv6-overview.md)
