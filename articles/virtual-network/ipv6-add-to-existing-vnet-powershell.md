---
title: IPv4-alkalmazás frissítése az IPv6-ra az Azure Virtual Network-PowerShellben
titlesuffix: Azure Virtual Network
description: Ez a cikk bemutatja, hogyan helyezhet üzembe IPv6-címeket egy meglévő Azure Virtual Network-alkalmazásban az Azure PowerShell használatával.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/21/2019
ms.author: kumud
ms.openlocfilehash: 47f73ca8ece8db5fad3f8a7709d8787db42626f4
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791201"
---
# <a name="upgrade-an-ipv4-application-to-ipv6-in-azure-virtual-network---powershell-preview"></a>IPv4-alkalmazás frissítése az IPv6-ra az Azure Virtual Networkben – PowerShell (előzetes verzió)

Ez a cikk bemutatja, hogyan adhat IPv6-címeket olyan alkalmazáshoz, amely IPv4 nyilvános IP-címet használ egy standard Load Balancer Azure-beli virtuális hálózatban. A helyben történő frissítés magában foglalja a virtuális hálózatot és az alhálózatot standard Load Balancer, az IPv4 + IPV6-alapú előtér-konfigurációval rendelkező virtuális gépeket, valamint az IPv4 + IPv6-konfigurációval, a hálózati biztonsági csoporttal és a nyilvános IP-címmel rendelkező virtuális gépeket

> [!Important]
> Az Azure Virtual Network IPv6-támogatása jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítését és használatát választja, akkor ehhez a cikkhez a Azure PowerShell modul 6.9.0 vagy újabb verziójára van szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="prerequisites"></a>Előfeltételek

### <a name="register-the-service"></a>A szolgáltatás regisztrálása

A Dual stack-alkalmazás Azure-ban való üzembe helyezése előtt az alábbi Azure PowerShell használatával kell konfigurálnia az előfizetését ehhez az előzetes verzióhoz:

Regisztráljon a következőképpen:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
A szolgáltatás regisztrációjának befejezéséhez akár 30 percet is igénybe vehet. A regisztráció állapotát a következő Azure PowerShell parancs futtatásával tekintheti meg: a regisztrációt a következőképpen tekintheti meg:
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
A regisztráció befejeződése után futtassa a következő parancsot:

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

### <a name="create-a-standard-load-balancer"></a>Standard Load Balancer létrehozása
Ez a cikk azt feltételezi, hogy üzembe helyezett egy standard Load Balancer a következő témakörben leírtak szerint [: standard Load Balancer-Azure PowerShell létrehozása](../load-balancer/quickstart-create-standard-load-balancer-powershell.md).

## <a name="retrieve-the-resource-group"></a>Az erőforráscsoport beolvasása

A kettős veremből származó virtuális hálózat létrehozása előtt le kell kérnie az erőforráscsoportot a [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup)használatával.

```azurepowershell
 $rg = Get-AzResourceGroup  -ResourceGroupName "myResourceGroupSLB"
```

## <a name="create-an-ipv6-ip-addresses"></a>IPv6 IP-címek létrehozása

Hozzon létre egy nyilvános IPv6-cím a standard Load Balancer [új AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) . Az alábbi példa egy *PublicIP_v6* nevű IPv6 nyilvános IP-címet hoz létre a *myresourcegroupslb erőforráscsoportban* erőforráscsoporthoz:

```azurepowershell
  
  $PublicIP_v6 = New-AzPublicIpAddress `
  -Name "PublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Sku Standard  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6
```

## <a name="configure-load-balancer-frontend"></a>A Load Balancer felületének konfigurálása

Kérje le a meglévő terheléselosztó-konfigurációt, majd konfigurálja az új IPv6 IP-címmel az [Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/Add-AzLoadBalancerFrontendIpConfig) használatával a következőképpen:

```azurepowershell
# Retrieve the load balancer configuration
$lb = Get-AzLoadBalancer -ResourceGroupName $rg.ResourceGroupName -Name "MyLoadBalancer"
# Add IPv6 components to the local copy of the load balancer configuration
$lb | Add-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6
#Update the running load balancer with the new frontend
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-backend-pool"></a>A terheléselosztó háttér-készletének konfigurálása

Hozza létre a háttér-készletet a terheléselosztó konfigurációjának helyi példányán, és frissítse a futó Load balancert az új háttér-készlet konfigurációjának megfelelően a következőképpen:

```azurepowershell
$lb | Add-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6"
# Update the running load balancer with the new backend pool
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-rules"></a>Terheléselosztó-szabályok konfigurálása
Kérje le a meglévő Load Balancer előtér-és háttér-készlet konfigurációját, majd adja hozzá az új terheléselosztási szabályokat az [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/Add-AzLoadBalancerRuleConfig)használatával.

```azurepowershell
# Retrieve the updated (live) versions of the frontend and backend pool
$frontendIPv6 = Get-AzLoadBalancerFrontendIpConfig -Name "dsLbFrontEnd_v6" -LoadBalancer $lb
$backendPoolv6 = Get-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6" -LoadBalancer $lb
# Create new LB rule with the frontend and backend
$lb | Add-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
#Finalize all the load balancer updates on the running load balancer
$lb | Set-AzLoadBalancer
```
## <a name="add-ipv6-address-ranges"></a>IPv6-címtartományok hozzáadása

Adja hozzá az IPv6-címtartományt a virtuális hálózathoz és a terheléselosztó-t futtató alhálózathoz az alábbiak szerint:

```azurepowershell
#Add IPv6 ranges to the VNET and subnet
#Retreive the VNET object
$vnet = Get-AzVirtualNetwork  -ResourceGroupName $rg.ResourceGroupName -Name "myVnet" 
#Add IPv6 prefix to the VNET
$vnet.addressspace.addressprefixes.add("ace:cab:deca::/48")
#Update the running VNET
$vnet |  Set-AzVirtualNetwork

#Retrieve the subnet object from the local copy of the VNET
$subnet= $vnet.subnets[0]
#Add IPv6 prefix to the Subnet (subnet of the VNET prefix, of course)
$subnet.addressprefix.add("ace:cab:deca::/64")
#Update the running VNET with the new subnet configuration
$vnet |  Set-AzVirtualNetwork

```
## <a name="add-ipv6-configuration-to-nic"></a>IPv6-konfiguráció hozzáadása a hálózati adapterhez

Konfigurálja mindkét virtuálisgép-hálózati adaptert IPv6-cím használatával az [Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/Add-AzNetworkInterfaceIpConfig) segítségével a következőképpen:

```azurepowershell

#Retrieve the NIC objects
$NIC_1 = Get-AzNetworkInterface -Name "myNic1" -ResourceGroupName $rg.ResourceGroupName
$NIC_2 = Get-AzNetworkInterface -Name "myNic2" -ResourceGroupName $rg.ResourceGroupName
$NIC_3 = Get-AzNetworkInterface -Name "myNic3" -ResourceGroupName $rg.ResourceGroupName
#Add an IPv6 IPconfig to NIC_1 and update the NIC on the running VM
$NIC_1 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_1 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_2 and update the NIC on the running VM
$NIC_2 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_2 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_3 and update the NIC on the running VM
$NIC_3 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_3 | Set-AzNetworkInterface

```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>IPv6-alapú kettős verem virtuális hálózatának megtekintése Azure Portal
Az IPv6 kettős verem virtuális hálózatát a következőképpen tekintheti meg Azure Portalban:
1. A portál keresési sávján adja meg a *myVnet*.
2. Ha a **myVnet** megjelenik a keresési eredmények között, válassza ki. Ez elindítja a *myVNet*nevű kettős verem virtuális hálózat **Áttekintés** lapját. A kettős verem virtuális hálózata a három hálózati adaptert jeleníti meg, amelyek IPv4-és IPv6-konfigurációval rendelkeznek, amelyek a *mySubnet*nevű kettős verem alhálózatában találhatók.

  ![IPv6-alapú kettős verem virtuális hálózata az Azure-ban](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)

> [!NOTE]
> Az Azure-beli virtuális hálózat IPv6-értéke csak olvashatóként érhető el a Azure Portal ebben az előzetes kiadásban.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhatja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsot az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás eltávolításához.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben frissített egy meglévő standard Load Balancer IPv4-es előtér-IP-konfigurációval egy kettős verem (IPv4 és IPv6) konfigurációra. Az IPv6-konfigurációkat a háttér-készletben található virtuális gépek hálózati adapterei számára is felvette. További információ az Azure-beli virtuális hálózatok IPv6-támogatásáról: [Mi az IPv6 for azure Virtual Network?](ipv6-overview.md)
