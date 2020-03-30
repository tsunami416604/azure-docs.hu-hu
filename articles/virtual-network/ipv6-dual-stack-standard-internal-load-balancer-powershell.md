---
title: IPv6-alapú kétverű alkalmazás üzembe helyezése standard belső terheléselosztó használatával az Azure-ban – PowerShell
titlesuffix: Azure Virtual Network
description: Ez a cikk bemutatja, hogyan telepíthet egy IPv6-alapú kettős veremű alkalmazást standard belső terheléselosztóval az Azure virtuális hálózatában az Azure Powershell használatával.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2019
ms.author: kumud
ms.openlocfilehash: fdf726fd31e8b92a04a1c136eb5cd7110e0c6d5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72333364"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-standard-internal-load-balancer-in-azure---powershell-preview"></a>IPv6-alapú kétverű alkalmazás üzembe helyezése standard belső terheléselosztó használatával az Azure-ban – PowerShell (előzetes verzió)

Ez a cikk bemutatja, hogyan telepíthet kétverécses (IPv4 + IPv6) alkalmazást az Azure-ban, amely kettős IP-címvel rendelkező kettős veremű virtuális hálózatot és alhálózatot, szabványos belső terheléselosztót kettős (IPv4 + IPv6) előtér-konfigurációval, kettős IP-címvel rendelkező hálózati adapterekkel rendelkező virtuális gépeket tartalmaz konfiguráció, a hálózati biztonsági csoport és a nyilvános IP-k.

> [!Important]
> Az Azure Virtual Network IPv6-támogatása jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az IPv6-kompatibilis belső terheléselosztó létrehozásának folyamata közel azonos az internetfelé néző IPv6-terheléselosztó [létrehozásának folyamatával, amelyet itt](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)ismertetett. A belső terheléselosztó létrehozásának egyetlen különbsége az előtér-konfiguráció, amint azt az alábbi PowerShell-példa szemlélteti:

```azurepowershell
 $frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
 -Name "dsLbFrontEnd_v6" `
 -PrivateIpAddress "ace:cab:deca:deed::100" `
 -PrivateIpAddressVersion "IPv6" `
 -Subnet $DsSubnet
```

A módosítások, amelyek a fenti belső terheléselosztó előtér-konfigurációt eredményezik:
- `PrivateIpAddressVersion` Az "IPv6" értékként van megadva
- Az `-PublicIpAddress` argumentumot elmulasztotta, `-PrivateIpAddress`vagy a helyébe a kifejezés lépett. Vegye figyelembe, hogy a privát címnek annak az alhálózati IP-területnek a tartományában kell lennie, amelyben a belső terheléselosztó telepítve lesz. Ha egy `-PrivateIpAddress` statikus cím nincs megadva, a következő szabad IPv6-cím lesz kiválasztva abból az alhálózatból, amelyben a belső terheléselosztó telepítve van.
- A kétveremalhálózat, amelyben a belső terheléselosztó lesz telepítve, `-Subnet` `-SubnetId` a vagy argumentummal van megadva.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a PowerShellt, ez a cikk az Azure PowerShell-modul 6.9.0-s vagy újabb verzióját igényli. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="prerequisites"></a>Előfeltételek
Mielőtt üzembe helyezne egy kétverű alkalmazást az Azure-ban, konfigurálnia kell az előfizetését ehhez az előzetes verzióhoz a következő Azure PowerShell használatával:

Regisztráljon a következőképpen:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
A funkcióregisztráció befejezéséhez akár 30 perc is igénybe vesszen. A regisztrációs állapot ot a következő Azure PowerShell-parancs futtatásával ellenőrizheti: Ellenőrizze a regisztrációt az alábbiak szerint:
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
A regisztráció befejezése után futtassa a következő parancsot:

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

A kétverkező virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)segítségével. A következő példa létrehoz egy *dsStd_ILB_RG* nevű erőforráscsoportot a *keleti helyen:*

```azurepowershell
$rg = New-AzResourceGroup `
  -ResourceGroupName "dsStd_ILB_RG"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>IPv4- és IPv6-alapú nyilvános IP-címek létrehozása
A virtuális gépek internetről való eléréséhez iPv4- és IPv6-nyilvános IP-címekre van szükség a virtuális gépekhez. Nyilvános IP-címek létrehozása a [New-AzPublicIpAddress címmel.](/powershell/module/az.network/new-azpublicipaddress) A következő példa *RdpPublicIP_1* és *RdpPublicIP_2* nevű IPv4-és IPv6-nyilvános IP-címet hoz létre a *dsStd_ILB_RG* erőforráscsoportban:

```azurepowershell
$RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
  
$RdpPublicIP_2 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
```
## <a name="create-the-virtual-network-and-the-subnet"></a>A virtuális hálózat és az alhálózat létrehozása

Hozzon létre egy virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) használatával, amely kettős kötegal rendelkezik alhálózati konfigurációval a [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)használatával. A következő példa létrehoz egy *dsVnet* nevű virtuális hálózatot *a dsSubnet*segítségével.

```azurepowershell
# Create dual stack subnet config
$DsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name "dsSubnet" `
  -AddressPrefix "10.0.0.0/24","ace:cab:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","ace:cab:deca::/48"  `
  -Subnet $DsSubnet

#Refresh the fully populated subnet for use in load balancer frontend configuration
$DsSubnet = get-AzVirtualNetworkSubnetconfig -name dsSubnet -VirtualNetwork $vnet
```
## <a name="create-standard-load-balancer"></a>Standard Load Balancer létrehozása

Ebben a szakaszban konfigurálja a két előtér-IP-címet (IPv4 és IPv6) és a terheléselosztó háttércímkészletét, majd hozzon létre egy szabványos terheléselosztót.

### <a name="create-front-end-ip"></a>Előtér-IP létrehozása

Hozzon létre egy előtér-IP-t a [New-AzLoadBalancerFrontendIpConfig segítségével.](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) A következő példa *dsLbFrontEnd_v4* és *dsLbFrontEnd_v6*nevű IPv4 és IPv6 előtér-IP-konfigurációkat hoz létre:

```azurepowershell
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PrivateIpAddress "10.0.0.100"  `
  -PrivateIpAddressVersion "IPv4"   `
  -Subnet $DsSubnet

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PrivateIpAddress "ace:cab:deca:deed::100"  `
  -PrivateIpAddressVersion "IPv6"   `
  -Subnet $DsSubnet

```

### <a name="configure-back-end-address-pool"></a>Háttércímkészlet konfigurálása

Hozzon létre egy háttércímkészletet a [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig)segítségével. A virtuális gépek a fennmaradó lépésekben ehhez a háttérkészlethez kapcsolódnak. A következő példa *dsLbBackEndPool_v4* és *dsLbBackEndPool_v6* nevű háttér-címkészleteket hoz létre, amelyek az IPV4 és az IPv6 hálózati adapterkonfigurációval rendelkező virtuális gépeket is tartalmazzák:

```azurepowershell
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v6"
```

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. Meg kell határoznia az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Győződjön meg arról, hogy csak a kifogástalan állapotú virtuális gépek kapforgalmat, opcionálisan megadhat egy állapotminta. Az alapszintű terheléselosztó iPv4-mintavételt használ a virtuális gépek IPv4- és IPv6-végpontjainak állapotának felméréséhez. A standard terheléselosztó támogatja a kifejezetten IPv6-állapotpróbákat.

Hozzon létre egy terheléselosztó szabályt [az Add-AzLoadBalancerRuleConfig segítségével.](/powershell/module/az.network/add-azloadbalancerruleconfig) A következő példa dsLBrule_v4 és *dsLBrule_v6* nevű terheléselosztó-szabályokat hoz *létre,* és kiegyenlíti a *80-as* *TCP-port* és az IPv4-alapú előtér IP-konfigurációi közötti forgalmat:

```azurepowershell
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

### <a name="create-load-balancer"></a>Terheléselosztó létrehozása

Hozzon létre egy standard terheléselosztót a [New-AzLoadBalancer segítségével.](/powershell/module/az.network/new-azloadbalancer) A következő példa létrehoz egy nyilvános standard terheléselosztó nevű *myInternalLoadBalancer* az IPv4 és IPv6 előtér IP-konfigurációk, háttérkészletek és terheléselosztási szabályok, amelyek az előző lépésekben létrehozott:

```azurepowershell
$lb = New-AzLoadBalancer  `
  -ResourceGroupName $rg.ResourceGroupName  `
  -Location $rg.Location  `
  -Name  "MyInternalLoadBalancer"  `
  -Sku "Standard"  `
  -FrontendIpConfiguration  $frontendIPv4,$frontendIPv6  `
  -BackendAddressPool  $backendPoolv4,$backendPoolv6  `
  -LoadBalancingRule  $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása
Mielőtt üzembe helyezne néhány virtuális gépet, és tesztelheti a kiegyensúlyozót, létre kell hoznia támogató hálózati erőforrásokat – rendelkezésre állási készletet, hálózati biztonsági csoportot és virtuális hálózati adaptereket. 

### <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása
Az alkalmazás magas rendelkezésre állásának javítása érdekében helyezze el a virtuális gépeket egy rendelkezésre állási csoportban.

Hozzon létre egy rendelkezésre állási készletet a [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset)segítségével. A következő példa létrehoz egy *dsAVset*nevű rendelkezésre állási készletet:

```azurepowershell
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hozzon létre egy hálózati biztonsági csoportot a virtuális hálózaton lévő bejövő és kimenő kommunikációt szabályozó szabályokhoz.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Hálózati biztonságicsoport-szabály létrehozása a 3389-es porhoz

Hozzon létre egy hálózati biztonsági csoportszabályt, amely engedélyezi az RDP-kapcsolatokat a 3389-es porton keresztül a [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)segítségével.

```azurepowershell
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

```azurepowershell
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

```azurepowershell
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsNSG1"  `
  -SecurityRules $rule1,$rule2
```
### <a name="create-nics"></a>Hálózati adapterek létrehozása

Hozzon létre virtuális hálózati adaptereket a [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)segítségével. A következő példa két virtuális hálózati adaptert hoz létre iPv4- és IPv6-konfigurációkkal egyaránt. (Egy virtuális NIC-t minden virtuális géphez, amelyet létre fog hozni az alkalmazáshoz a következő lépések során).

```azurepowershell

# Create the IPv4 configuration for NIC 1
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_1

# Create the IPv6 configuration
$Ip6Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp6Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv6 `
  -LoadBalancerBackendAddressPool $backendPoolv6

# Create NIC 1
$NIC_1 = New-AzNetworkInterface `
  -Name "dsNIC1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

# Create the IPv4 configuration for NIC 2
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_2

# Create NIC 2 reusing the IPv6 configuration from NIC 1
$NIC_2 = New-AzNetworkInterface `
  -Name "dsNIC2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

```

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

A [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) paranccsal állítsa be a virtuális gépek rendszergazdai felhasználónevét és jelszavát:

```azurepowershell
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VM's"
```

Most már létrehozhatja a virtuális gépeket a New-AzVM.Now you can create the VMs with [New-AzVM.](/powershell/module/az.compute/new-azvm) A következő példa két virtuális gépet és a szükséges virtuális hálózati összetevőket hoz létre, ha még nem léteznek.

```azurepowershell
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
## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Az IPv6 kétletétes virtuális hálózatának megtekintése az Azure Portalon
Az IPv6 kettős veremű virtuális hálózatot az Azure Portalon az alábbiak szerint tekintheti meg:
1. A portál keresősávján írja be a *dsVnet*.
2. Amikor **a dsVnet** megjelenik a keresési eredmények között, jelölje ki azt. Ez elindítja a *dsVnet*nevű kettős verem virtuális hálózat **áttekintése** lapját. A kétverű virtuális hálózat a két hálózati adaptert jeleníti meg, amelyek iPv4- és IPv6-konfigurációkkal is rendelkeznek, és amelyek a *dsSubnet*nevű kettős veremalhálózatban találhatók.

![IPv6 kettős veremű virtuális hálózat standard belső terheléselosztóval](./media/ipv6-dual-stack-standard-internal-load-balancer-powershell/ipv6-dual-stack-virtual-network.png)


> [!NOTE]
> Az IPv6 for Azure virtuális hálózat érhető el az Azure Portalon írásvédett ebben az előzetes verzióban.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség rá, az [Eltávolítás-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) paranccsal eltávolíthatja az erőforráscsoportot, a virtuális gépés az összes kapcsolódó erőforrást.

```azurepowershell
Remove-AzResourceGroup -Name dsStd_ILB_RG
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy standard terheléselosztót kettős előtér-IP-konfigurációval (IPv4 és IPv6). Két virtuális gépet is létrehozott, amelyek két IP-konfigurációval (IPV4 + IPv6) rendelkező hálózati adaptereket tartalmaztak, amelyeket a terheléselosztó háttérkészletéhez adtak hozzá. Ha többet szeretne tudni az IPv6-támogatásról az Azure virtuális hálózatokban, olvassa el [a Mi az IPv6 az Azure virtuális hálózathoz című témakörben?](ipv6-overview.md)