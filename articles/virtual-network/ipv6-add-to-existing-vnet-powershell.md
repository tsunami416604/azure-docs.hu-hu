---
title: IPv4-alkalmazás frissítése IPv6-ra az Azure virtuális hálózatban – PowerShell
titlesuffix: Azure Virtual Network
description: Ez a cikk bemutatja, hogyan telepítheti az IPv6-címeket egy meglévő alkalmazásra az Azure Powershell használatával az Azure Powershell használatával.
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
ms.openlocfilehash: d08ce1c382d173ac98a0e61e6117ed50b958ba44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76119839"
---
# <a name="upgrade-an-ipv4-application-to-ipv6-in-azure-virtual-network---powershell-preview"></a>IPv4-alkalmazás frissítése IPv6-ra az Azure virtuális hálózatában – PowerShell (előzetes verzió)

Ez a cikk bemutatja, hogyan adhat hozzá IPv6-kapcsolatot egy meglévő IPv4-alkalmazáshoz egy szabványos terheléselosztóval és nyilvános IP-címvel rendelkező Azure virtuális hálózatban. A helyszíni frissítés a következőket tartalmazza:
- A virtuális hálózat és alhálózat IPv6-címterülete
- Szabványos terheléselosztó IPv4 és IPV6 előtér-konfigurációkkal
- IPv4 + IPv6-konfigurációval rendelkező hálózati adapterekkel rendelkező virtuális gépek
- IPv6 nyilvános IP-cím, így a terheléselosztó internetkapcsolattal rendelkezik

> [!Important]
> Az Azure Virtual Network IPv6-támogatása jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a PowerShellt, ez a cikk az Azure PowerShell-modul 6.9.0-s vagy újabb verzióját igényli. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="prerequisites"></a>Előfeltételek

### <a name="register-the-service"></a>A szolgáltatás regisztrálása

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

### <a name="create-a-standard-load-balancer"></a>Standard Load Balancer létrehozása
Ez a cikk feltételezi, hogy üzembe helyezett egy standard terheléselosztót a rövid útmutatóban leírtak [szerint: Standard terheléselosztó létrehozása – Azure PowerShell.](../load-balancer/quickstart-create-standard-load-balancer-powershell.md)

## <a name="retrieve-the-resource-group"></a>Az erőforráscsoport lekérése

A kétverkező virtuális hálózat létrehozása előtt a [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup)segítségével kell beolvasnia az erőforráscsoportot.

```azurepowershell
 $rg = Get-AzResourceGroup  -ResourceGroupName "myResourceGroupSLB"
```

## <a name="create-an-ipv6-ip-addresses"></a>IPv6-IP-címek létrehozása

Hozzon létre egy nyilvános IPv6-címet a Standard Load Balancer [New-AzPublicIpAddress címmel.](/powershell/module/az.network/new-azpublicipaddress) A következő példa létrehoz egy *PublicIP_v6* nevű IPv6 nyilvános IP-címet a *myResourceGroupSLB* erőforráscsoportban:

```azurepowershell
  
  $PublicIP_v6 = New-AzPublicIpAddress `
  -Name "PublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Sku Standard  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6
```

## <a name="configure-load-balancer-frontend"></a>Terheléselosztó előtétjének konfigurálása

A meglévő terheléselosztó konfigurációjának beolvasása, majd az új IPv6 [IP-cím hozzáadása az Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/Add-AzLoadBalancerFrontendIpConfig) használatával az alábbiak szerint:

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

## <a name="configure-load-balancer-backend-pool"></a>Terheléselosztó háttérkészletének konfigurálása

Hozza létre a háttérkészletet a terheléselosztó konfigurációjának helyi példányán, és frissítse a futó terheléselosztót az új háttérkészlet-konfigurációval az alábbiak szerint:

```azurepowershell
$lb | Add-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6"
# Update the running load balancer with the new backend pool
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-rules"></a>Terheléselosztási szabályok konfigurálása
A meglévő terheléselosztó elő- és háttérkészlet-konfigurációjának beolvasása, majd új terheléselosztási szabályok hozzáadása [az Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/Add-AzLoadBalancerRuleConfig)használatával.

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

Adja hozzá az IPv6-címtartományokat a virtuális hálózathoz és a virtuális gépeket üzemeltető alhálózathoz az alábbiak szerint:

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

Konfigurálja az összes IPv6-címmel rendelkező virtuálisgép-hálózati adaptert [az Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/Add-AzNetworkInterfaceIpConfig) használatával az alábbiak szerint:

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

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Az IPv6 kétletétes virtuális hálózatának megtekintése az Azure Portalon
Az IPv6 kettős veremű virtuális hálózatot az Azure Portalon az alábbiak szerint tekintheti meg:
1. A portál keresősávján adja meg a *myVnet*.
2. Amikor **a myVnet** megjelenik a keresési eredmények között, jelölje ki. Ez elindítja a *myVNet*nevű kettős verem virtuális hálózat **áttekintése** oldalt. A kétverű virtuális hálózat a három hálózati adaptert jeleníti meg, amelyek iPv4- és IPv6-konfigurációkkal is rendelkeznek, és amelyek a *mySubnet*nevű kettős verem alhálózatban találhatók.

  ![IPv6 kettős halmozott virtuális hálózat az Azure-ban](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)

> [!NOTE]
> Az IPv6 for Azure virtuális hálózat érhető el az Azure Portalon írásvédett ebben az előzetes verzióban.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség rá, az [Eltávolítás-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) paranccsal eltávolíthatja az erőforráscsoportot, a virtuális gépés az összes kapcsolódó erőforrást.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy meglévő standard terheléselosztót frissített iPv4 előtér-IP-konfigurációval egy kettős veremkonfigurációra (IPv4 és IPv6). IPv6-konfigurációkat is hozzáadott a háttérkészletben lévő virtuális gépek hálózati adaptereihez és az őket üzemeltető virtuális hálózathoz. Ha többet szeretne tudni az IPv6-támogatásról az Azure virtuális hálózatokban, olvassa el [a Mi az IPv6 az Azure virtuális hálózathoz című témakörben?](ipv6-overview.md)
