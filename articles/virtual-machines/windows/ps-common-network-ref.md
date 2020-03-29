---
title: Gyakori PowerShell-parancsok az Azure virtuális hálózatokhoz
description: Közös PowerShell-parancsok a virtuális hálózat és a virtuális gépekhez kapcsolódó erőforrások létrehozásának megkezdéséhez.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 56e1a73c-8299-4996-bd03-f74585caa1dc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: b9debe04759772ef51946dc99943ec4eff6f61dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77911831"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Gyakori PowerShell-parancsok az Azure virtuális hálózatokhoz

Ha virtuális gépet szeretne létrehozni, létre kell hoznia egy [virtuális hálózatot,](../../virtual-network/virtual-networks-overview.md) vagy ismernie kell egy meglévő virtuális hálózatot, amelyben a virtuális gép hozzáadható. Általában virtuális gép létrehozásakor is meg kell fontolnia a cikkben leírt erőforrások létrehozását.

Az Azure PowerShell legfrissebb verziójának telepítésével, a kívánt előfizetés kiválasztásával és a fiókjába való bejelentkezéssel kapcsolatos információkért lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).

Néhány változó akkor lehet hasznos, ha a cikkben szereplő parancsok közül többet futtat:

- $location – a hálózati erőforrások helye. A [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) segítségével megkeresheti az Ön számára működő [földrajzi régiót.](https://azure.microsoft.com/regions/)
- $myResourceGroup – annak az erőforráscsoportnak a neve, amelyben a hálózati erőforrások találhatók.

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása

| Tevékenység | Parancs |
| ---- | ------- |
| Alhálózat-konfigurációk létrehozása |$subnet1 = [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -Name "mySubnet1" -AddressPrefix XX. X.X.X/XX<BR>$subnet2 = New-AzVirtualNetworkSubnetConfig -Name "mySubnet2" -AddressPrefix XX. X.X.X/XX<BR><BR>Egy tipikus hálózat rendelkezhet egy [internetre néző terheléselosztó](../../load-balancer/load-balancer-internet-overview.md) alhálózatával és egy belső terheléselosztó külön alhálózatával. [internal load balancer](../../load-balancer/load-balancer-internal-overview.md) |
| Virtuális hálózat létrehozása |$vnet = [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup -Location $location -AddressPrefix XX. X.X.X/XX -Alhálózat $subnet1, $subnet2 |
| Egyedi tartománynév tesztelése |[Test-AzDnsAvailability](https://docs.microsoft.com/powershell/module/az.network/test-azdnsavailability) -DomainNameLabel "myDNS" -Location $location<BR><BR>Megadhat egy NYILVÁNOS [IP-erőforrás DNS-tartománynevét,](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)amely domainname.location.cloudapp.azure.com leképezést hoz létre az Azure által felügyelt DNS-kiszolgálók nyilvános IP-címéhez. A név csak betűket, számokat és kötőjelet tartalmazhat. Az első és az utolsó karakternek betűnek vagy számnak kell lennie, és a tartománynévnek egyedinek kell lennie az Azure-ban. Ha **igaz** értéket ad vissza, a javasolt név globálisan egyedi. |
| Hozzon létre egy nyilvános IP-címet |$pip = [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) -Name "myPublicIp" -ResourceGroupName $myResourceGroup -DomainNameLabel "myDNS" -Location $location -AllocationMethod Dynamic<BR><BR>A nyilvános IP-cím a korábban tesztelt tartománynevet használja, amelyet a terheléselosztó előtér-konfigurációja használ. |
| Előtér-IP-konfiguráció létrehozása |$frontendIP = [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -Name "myFrontendIP" -PublicIpAddress $pip<BR><BR>Az előtér-konfiguráció tartalmazza a nyilvános IP-címet, amelyet korábban létrehozott a bejövő hálózati forgalomhoz. |
| Hátércímkészlet létrehozása |$beAddressPool = [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>Belső címeket biztosít a hálózati csatolón keresztül elérhető terheléselosztó háttér-kezelőjéhez. |
| Mintavétel létrehozása |$healthProbe = [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) -Name "myProbe" -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>Állapotminta a háttércímkészletben lévő virtuális géppéldányok rendelkezésre állásának ellenőrzésére szolgál. |
| Terheléselosztási szabály létrehozása |$lbRule = [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Olyan szabályokat tartalmaz, amelyek a terheléselosztó nyilvános portját a háttércímkészlet egyik portjához rendelik. |
| Bejövő NAT-szabály létrehozása |$inboundNATRule = [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -Name "myInboundRule1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Szabályokat tartalmaz, amelyek a terheléselosztó nyilvános portját egy adott virtuális gép portjához képezik a háttércímkészletben. |
| Terheléselosztó létrehozása |$loadBalancer = [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) -ResourceGroupName $myResourceGroup -Name "myLoadBalancer" -Location $location -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe |
| Hálózati adapter létrehozása |$nic1= [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) -ResourceGroupName $myResourceGroup -Name "myNIC" -Location $location -PrivateIpAddress XX. X.X.X -Subnet $subnet2 -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Hozzon létre egy hálózati adaptert a korábban létrehozott nyilvános IP-cím és virtuális hálózati alhálózat használatával. |

## <a name="get-information-about-network-resources"></a>Információ a hálózati erőforrásokról

| Tevékenység | Parancs |
| ---- | ------- |
| Virtuális hálózatok listázása |[Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>Az erőforráscsoport összes virtuális hálózatának listája. |
| Információ a virtuális hálózatról |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup |
| Alhálózatok listázása virtuális hálózatban |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup &#124; Select Alhálózatok |
| Információ az alhálózatról |[Get-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>A megadott virtuális hálózat alhálózatával kapcsolatos információk bekéselése. A $vnet érték a Get-AzVirtualNetwork által visszaadott objektumot jelöli. |
| IP-címek listázása |[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>Felsorolja az erőforráscsoport nyilvános IP-címeit. |
| Terheléselosztók listázása |[Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>Az erőforráscsoport összes terheléselosztójának listája. |
| Hálózati adapterek listázása |[Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>Az erőforráscsoport összes hálózati illesztőjének listája. |
| Információ a hálózati adapterről |Get-AzNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Információt kap egy adott hálózati kapcsolatról. |
| Hálózati adapter IP-konfigurációjának beszereznie |[Get-AzNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterfaceipconfig) -Name "myNICIP" -NetworkInterface $nic<BR><BR>Információ bekerül és információ a megadott hálózati adapter IP-konfigurációjáról. A $nic érték a Get-AzNetworkInterface által visszaadott objektumot jelöli. |

## <a name="manage-network-resources"></a>Hálózati erőforrások kezelése

| Tevékenység | Parancs |
| ---- | ------- |
| Alhálózat hozzáadása virtuális hálózathoz |[Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig) -AddressPrefix XX. X.X.X/XX -Név "mySubnet1" -VirtualNetwork $vnet<BR><BR>Alhálózat hozzáadása meglévő virtuális hálózathoz. A $vnet érték a Get-AzVirtualNetwork által visszaadott objektumot jelöli. |
| Virtuális hálózat törlése |[Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup<BR><BR>Eltávolítja a megadott virtuális hálózatot az erőforráscsoportból. |
| Hálózati adapter törlése |[Remove-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworkinterface) -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Eltávolítja a megadott hálózati illesztőt az erőforráscsoportból. |
| Terheléselosztó törlése |[Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer) -Name "myLoadBalancer" -ResourceGroupName $myResourceGroup<BR><BR>Eltávolítja a megadott terheléselosztót az erőforráscsoportból. |
| Nyilvános IP-cím törlése |[Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress)-Name "myIPAddress" -ResourceGroupName $myResourceGroup<BR><BR>Eltávolítja a megadott nyilvános IP-címet az erőforráscsoportból. |

## <a name="next-steps"></a>Következő lépések
Használja a virtuális gép létrehozásakor létrehozott hálózati [adaptert.](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


