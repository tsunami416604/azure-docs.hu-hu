---
title: Az Azure virtual network - PowerShell IPv6 kettős verem az alkalmazások üzembe helyezése
titlesuffix: Azure Virtual Network
description: Ez a cikk bemutatja hogyan Azure Powershell-lel az Azure virtual network IPv6 kettős verem az alkalmazások üzembe helyezése.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: a9f8066896134072665c3f5b325e033b638bf094
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2019
ms.locfileid: "66000997"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure---powershell-preview"></a>Alkalmazás üzembe helyezése IPv6 kettős verem az Azure-ban – PowerShell (előzetes verzió)

Ez a cikk bemutatja, hogyan egy kettős verem (IPv4 + IPv6) üzembe helyezése az Azure-ban, amely tartalmazza a kettős verem virtuális hálózatot és alhálózatot, kettős (IPv4 + IPv6-alapú) előtérbeli konfigurációkkal, kettős IP-konfigurációval rendelkező hálózati adapterrel rendelkező virtuális gépek egy terheléselosztó hálózati biztonsági csoport és a nyilvános IP-címek.

> [!Important]
> IPv6-támogatás az Azure virtuális hálózat jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha a helyi telepítése és használata PowerShell választja, ehhez a cikkhez az Azure PowerShell-modul verzióját 6.9.0 vagy újabb. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="prerequisites"></a>Előfeltételek
Az Azure-ban kettős verem alkalmazást telepít központilag, konfigurálnia kell az előfizetését az előzetes verziójú funkció, a következő Azure PowerShell-lel:

Regisztrálja az alábbiak szerint:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
```
A szolgáltatás regisztráció befejezése akár 30 percet vesz igénybe. A regisztrációs állapot a következő Azure PowerShell-parancs futtatásával ellenőrizheti: Ellenőrizze a regisztrációt a következőképpen:
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
```
A regisztráció befejezése után futtassa a következő parancsot:

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

A kettős vermű virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). A következő példában létrehozunk egy erőforráscsoportot, nevű *myRGDualStack* a a *kelet USA* helye:

```azurepowershell-interactive
   $rg = New-AzResourceGroup `
  -ResourceGroupName "dsRG1"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>Create IPv4 and IPv6 public IP addresses
A virtuális gépek elérését az internetről, nyilvános IP-címek IPv4 és IPv6-terheléselosztó kell. Hozzon létre nyilvános IP-címek [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). A következő példában létrehozunk IPv4 és IPv6 típusú nyilvános IP-címet *dsPublicIP_v4* és *dsPublicIP_v6* a a *dsRG1* erőforráscsoportot:

```azurepowershell-interactive
$PublicIP_v4 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v4" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
$PublicIP_v6 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv6
```
Hozzáférhet a virtuális gépek RDP-kapcsolattal, hozzon létre egy IPV4 nyilvános IP-címek a virtuális gépek [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress).

```azurepowershell-interactive
  $RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
  $RdpPublicIP_2 = New-AzPublicIpAddress `
   -Name "RdpPublicIP_2" `
   -ResourceGroupName $rg.ResourceGroupName `
   -Location $rg.Location  `
   -AllocationMethod Dynamic `
   -IpAddressVersion IPv4
```

## <a name="create-basic-load-balancer"></a>Alapszintű Load Balancer létrehozása

Ebben a szakaszban kettős frontend-IP (IPv4 és IPv6-alapú) és a háttér-címkészletet a load Balancer konfigurálása, és hozzon létre egy alapszintű Load Balancer.

### <a name="create-front-end-ip"></a>Előtérbeli IP-cím létrehozása

Hozzon létre egy előtér-IP-Címmel rendelkező [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). A következő példában létrehozunk IPv4 és IPv6-előtérbeli IP-konfigurációk nevű *dsLbFrontEnd_v4* és *dsLbFrontEnd_v6*:

```azurepowershell-interactive
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PublicIpAddress $PublicIP_v4

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

```

### <a name="configure-back-end-address-pool"></a>Háttér-címkészlet konfigurálása

A háttér-címkészlet létrehozása [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). A virtuális gépek csatlakoztatása a hátralévő lépések során a háttérkészlethez. A következő példában létrehozunk nevű háttér-címkészletek *dsLbBackEndPool_v4* és *dsLbBackEndPool_v6* szerepeltetni kívánt virtuális gépek IPV4- és IPv6-alapú hálózati konfigurációkat:

```azurepowershell-interactive
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v6"
```

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. Meg kell határoznia az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Győződjön meg arról, hogy csak kifogástalan állapotú virtuális gépek fogadhassanak forgalmat, és igény szerint megadhat egy állapotmintát. Alapszintű load balancer egy IPv4-mintavétel segítségével felmérheti a virtuális gépek IPv4- és IPv6 végpontok állapotát. A standard load balancer explicit módon IPv6 állapotadat-mintavételek támogatását tartalmazza.

Hozzon létre egy terheléselosztó-szabályt az [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). A következő példában létrehozunk load balancer-szabályok nevű *dsLBrule_v4* és *dsLBrule_v6* , és elosztja a forgalmat a *TCP* port *80-as* , az IPv4 és IPv6-előtérbeli IP-konfigurációk:

```azurepowershell-interactive
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

Az alapszintű Load Balancer-létrehozása [új AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). A következő példában létrehozunk egy alapszintű nyilvános Load Balancer nevű *myLoadBalancer* az IPv4 és IPv6-előtérbeli IP-címet használó konfigurációk háttérkészletek és az előző lépésekben létrehozott terheléselosztási szabályok:

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "MyLoadBalancer" `
-Sku "Basic" `
-FrontendIpConfiguration $frontendIPv4,$frontendIPv6 `
-BackendAddressPool $backendPoolv4,$backendPoolv6 `
-LoadBalancingRule $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása
Mielőtt az egyes virtuális gépek üzembe helyezése és tesztelné az elosztót, létre kell hoznia támogató hálózati erőforrások – rendelkezésre állási csoport, a hálózati biztonsági csoport, a virtuális hálózat és a virtuális hálózati adapter. 
### <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása
Az alkalmazás magas rendelkezésre állásának növeléséhez helyezze a virtuális gépeket egy rendelkezésre állási csoportba.

Hozzon létre egy rendelkezésre állási csoport [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). Az alábbi példa egy *myAvailabilitySet* nevű rendelkezésre állási csoportot hoz létre:

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

Hozzon létre egy hálózati biztonsági csoportot a szabályokat, amelyek szabályozzák a virtuális hálózatok közötti bejövő és kimenő kommunikáció.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Hálózati biztonságicsoport-szabály létrehozása a 3389-es porhoz

Hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt az RDP-kapcsolatok engedélyezése a 3389-es porton keresztül [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

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

Hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt internetes kapcsolatok engedélyezéséhez a 80-as porton keresztül [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

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

Hozzon létre egy hálózati biztonsági csoport [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "dsNSG1"  `
-SecurityRules $rule1,$rule2
```
### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

A virtuális hálózat létrehozása [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Az alábbi példa létrehoz egy *mySubnet* hálózattal rendelkező *myVNet* nevű virtuális hálózatot:

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

A virtuális hálózati adapter létrehozása [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). A következő példában két virtuális hálózati adapter is hoz létre az IPv4 és IPv6-konfigurációk. (Egy virtuális NIC-t minden virtuális géphez, amelyet létre fog hozni az alkalmazáshoz a következő lépések során).

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

Most már létrehozhatja a virtuális gépeket a [New-azvm parancsmag](/powershell/module/az.compute/new-azvm). Az alábbi példa létrehoz két virtuális gépet, és a szükséges virtuális hálózati összetevőket, ha azok nem léteznek. 

```azurepowershell-interactive
$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2016-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null 
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1 

$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null 
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2
```

## <a name="determine-ip-addresses-of-the-ipv4-and-ipv6-endpoints"></a>Az IPv4 és IPv6-végpont IP-címek meghatározása
Ez a központi telepítésben használja összes hálózati adapter objektum az erőforráscsoportban összefoglalva a IP-cím lekérése `get-AzNetworkInterface`. Ezenkívül első rendelkező az IPv4 és IPv6-végpontok a terheléselosztó előtérbeli címét `get-AzpublicIpAddress`.

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
A következő ábrán látható egy mintakimenet, amely a két virtuális gép magánhálózati IPv4 és IPv6-címek és a terheléselosztó előtérbeli IPv4 és IPv6-címeket sorolja fel.

![Kettős verem (IPv4/IPv6) alkalmazás telepítése az Azure IP összefoglalása](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-application-summary.png)

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Virtuális hálózat IPv6-kettős verem megtekintése az Azure Portalon
Tekintheti meg az IPv6-kettős verem virtuális hálózatot az Azure Portalon a következő:
1. Adja meg a portál keresősávjában *dsVnet*.
2. Amikor a **myVirtualNetwork** megjelenik a keresési eredmények között, válassza ki. Ezzel elindítja a **áttekintése** a kettős verem nevű virtuális hálózat lapján *dsVnet*. A kettős verem virtuális hálózat látható a két hálózati adapter IPv4- és IPv6-konfigurációval, a kettős verem nevű alhálózat található *dsSubnet*.

  ![IPv6-alapú kettős verem virtuális hálózat az Azure-ban](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> Az IPv6-alapú Azure virtuális hálózat az Azure Portalon érhető el ebben az előzetes kiadásban a csak olvasható.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhatja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurepowershell-interactive
Remove-AzResourceGroup -Name dsRG1
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy alapszintű Load Balancer kettős előtérbeli IP-konfiguráció (IPv4 és IPv6). Emellett létrehozott egy két virtuális gépet, amely a hálózati adapterek mellékelt kettős IP-konfigurációk (IPV4 + IPv6), amely a terheléselosztó háttérkészlet bővült. IPv6-támogatás az Azure virtuális hálózatokkal kapcsolatos további információkért lásd: [Mi az IPv6 és az Azure Virtual Network?](ipv6-overview.md)