---
title: Azure-beli virtuális hálózatok általános PowerShell-parancsai
description: Gyakori PowerShell-parancsok a virtuális hálózatok és a virtuális gépekhez kapcsolódó erőforrások létrehozásának megkezdéséhez.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 78aac1e49b23cf7fd294314f335aa429e8458639
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84233371"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Azure-beli virtuális hálózatok általános PowerShell-parancsai

Ha virtuális gépet szeretne létrehozni, létre kell hoznia egy [virtuális hálózatot](../../virtual-network/virtual-networks-overview.md) , vagy tudnia kell egy olyan meglévő virtuális hálózatról, amelybe a virtuális gép hozzá tud adni. A virtuális gépek létrehozásakor általában meg kell fontolnia a cikkben ismertetett erőforrások létrehozását is.

Az Azure PowerShell legfrissebb verziójának telepítésével, a kívánt előfizetés kiválasztásával és a fiókjába való bejelentkezéssel kapcsolatos információkért lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).

Bizonyos változók hasznosak lehetnek, ha a cikkben szereplő parancsok közül többet futtat:

- $location – a hálózati erőforrások helye. A [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) használatával megkeresheti az Ön számára megfelelő [földrajzi régiót](https://azure.microsoft.com/regions/) .
- $myResourceGroup – annak az erőforráscsoportnak a neve, ahol a hálózati erőforrások találhatók.

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása

| Tevékenység | Parancs |
| ---- | ------- |
| Alhálózat-konfigurációk létrehozása |$subnet 1 = [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -Name "mySubnet1" – AddressPrefix XX. X. X. X/XX<BR>$subnet 2 = New-AzVirtualNetworkSubnetConfig-Name "mySubnet2" – AddressPrefix XX. X. X. X/XX<BR><BR>Egy tipikus hálózat rendelkezhet alhálózattal az [internetkapcsolattal rendelkező terheléselosztó](../../load-balancer/load-balancer-internet-overview.md) és egy különálló alhálózat számára a [belső terheléselosztó](../../load-balancer/load-balancer-internal-overview.md)számára. |
| Virtuális hálózat létrehozása |$vnet = [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) -Name "myVNet"-ResourceGroupName $MyResourceGroup-Location $Location-AddressPrefix XX. X. X. X/XX – alhálózat $subnet 1, $subnet 2 |
| Egyedi tartománynév tesztelése |[Test-AzDnsAvailability](https://docs.microsoft.com/powershell/module/az.network/test-azdnsavailability) -Domainnamelabel értékkel "myDNS" – hely $Location<BR><BR>Megadhat egy DNS-tartománynevet egy [nyilvános IP-erőforráshoz](../../virtual-network/public-ip-addresses.md), amely létrehoz egy leképezést a nyilvános IP-címhez az Azure által FELÜGYELt DNS-kiszolgálók domainname.location.cloudapp.Azure.com. A név csak betűket, számokat és kötőjelet tartalmazhat. Az első és az utolsó karakternek betűnek vagy számnak kell lennie, és a tartománynévnek egyedinek kell lennie az Azure-helyen belül. Ha a függvény **igaz** értéket ad vissza, a javasolt név globálisan egyedi. |
| Hozzon létre egy nyilvános IP-címet |$pip = [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) -Name "myPublicIp"-ResourceGroupName $MyResourceGroup-Domainnamelabel értékkel "myDNS" – location $Location – AllocationMethod Dynamic<BR><BR>A nyilvános IP-cím a korábban tesztelt tartománynevet használja, amelyet a terheléselosztó előtér-konfigurációja használ. |
| Előtér-IP-konfiguráció létrehozása |$frontendIP = [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -Name "myFrontendIP"-PublicIpAddress $pip<BR><BR>A előtér-konfiguráció tartalmazza azt a nyilvános IP-címet, amelyet korábban a bejövő hálózati forgalomhoz hozott létre. |
| Hátércímkészlet létrehozása |$beAddressPool = [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>Belső címeket biztosít a terheléselosztó olyan hátteréhez, amely hálózati adapteren keresztül érhető el. |
| Mintavétel létrehozása |$healthProbe = [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) -Name "myProbe"-RequestPath "healthProbe. aspx"-Protocol http-port 80-IntervalInSeconds 15-ProbeCount 2<BR><BR>A háttér-címkészlet virtuálisgép-példányainak rendelkezésre állásának vizsgálatához használt állapot-mintavételt tartalmaz. |
| Terheléselosztási szabály létrehozása |$lbRule = [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) -Name http-frontendipconfiguration osztály $FrontendIP-BackendAddressPool $BeAddressPool-szonda $HealthProbe-Protocol TCP-FrontendPort 80-BackendPort 80<BR><BR>Olyan szabályokat tartalmaz, amelyek a terheléselosztó nyilvános portját hozzárendelik a háttér-címkészlet egy portjához. |
| Bejövő NAT-szabály létrehozása |$inboundNATRule = [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -Name "myInboundRule1"-frontendipconfiguration osztály $FrontendIP-Protocol TCP-FrontendPort 3441-BackendPort 3389<BR><BR>Olyan szabályokat tartalmaz, amelyek a terheléselosztó nyilvános portját hozzárendelik egy adott virtuális gép portjához a háttér-címkészlet során. |
| Terheléselosztó létrehozása |$loadBalancer = [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) -ResourceGroupName $MyResourceGroup-Name "myLoadBalancer"-location $Location-frontendipconfiguration osztály $FrontendIP-InboundNatRule $InboundNATRule-LoadBalancingRule $LbRule-BackendAddressPool $beAddressPool-mintavétel $healthProbe |
| Hálózati adapter létrehozása |$nic 1 = [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) -ResourceGroupName $MyResourceGroup-Name "myNIC" – location $Location-privateipaddress tulajdonságot XX. X. X. X – alhálózat $subnet 2 – LoadBalancerBackendAddressPool $loadBalancer. BackendAddressPools [0]-LoadBalancerInboundNatRule $loadBalancer. InboundNatRules [0]<BR><BR>Hozzon létre egy hálózati adaptert a korábban létrehozott nyilvános IP-cím és virtuális hálózati alhálózat használatával. |

## <a name="get-information-about-network-resources"></a>Hálózati erőforrásokkal kapcsolatos információk beolvasása

| Tevékenység | Parancs |
| ---- | ------- |
| Virtuális hálózatok listázása |[Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>Az erőforráscsoport összes virtuális hálózatát listázza. |
| Virtuális hálózattal kapcsolatos információk beolvasása |Get-AzVirtualNetwork-Name "myVNet" – ResourceGroupName $myResourceGroup |
| Virtuális hálózatban lévő alhálózatok listázása |Get-AzVirtualNetwork-Name "myVNet" – ResourceGroupName $myResourceGroup &#124; alhálózatok kiválasztása |
| Alhálózat adatainak beolvasása |[Get-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -Name "mySubnet1" – VirtualNetwork $vnet<BR><BR>Beolvassa a megadott virtuális hálózat alhálózatának adatait. A $vnet érték a Get-AzVirtualNetwork által visszaadott objektumot jelöli. |
| IP-címek listázása |[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>Felsorolja az erőforráscsoport nyilvános IP-címeit. |
| Load balancerek listázása |[Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>Az erőforráscsoport összes terheléselosztó listáját listázza. |
| Hálózati adapterek listázása |[Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>Az erőforráscsoport összes hálózati adapterét listázza. |
| Hálózati csatolóval kapcsolatos információk beolvasása |Get-AzNetworkInterface-Name "myNIC" – ResourceGroupName $myResourceGroup<BR><BR>Egy adott hálózati adapter információinak beolvasása. |
| Hálózati adapter IP-konfigurációjának beolvasása |[Get-AzNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterfaceipconfig) -Name "myNICIP" – hálózati adaptere $NIC<BR><BR>A megadott hálózati adapter IP-konfigurációjával kapcsolatos információk beolvasása. A $nic érték a Get-AzNetworkInterface által visszaadott objektumot jelöli. |

## <a name="manage-network-resources"></a>Hálózati erőforrások kezelése

| Tevékenység | Parancs |
| ---- | ------- |
| Alhálózat hozzáadása virtuális hálózathoz |[Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig) -AddressPrefix XX. X. X. X/XX – név: "mySubnet1" – VirtualNetwork $vnet<BR><BR>Alhálózatot rendel egy meglévő virtuális hálózathoz. A $vnet érték a Get-AzVirtualNetwork által visszaadott objektumot jelöli. |
| Virtuális hálózat törlése |[Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork) -Name "myVNet" – ResourceGroupName $myResourceGroup<BR><BR>Eltávolítja a megadott virtuális hálózatot az erőforráscsoporthoz. |
| Hálózati adapter törlése |[Remove-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworkinterface) -Name "myNIC" – ResourceGroupName $myResourceGroup<BR><BR>Eltávolítja a megadott hálózati adaptert az erőforráscsoporthoz. |
| Terheléselosztó törlése |[Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer) -Name "myLoadBalancer" – ResourceGroupName $myResourceGroup<BR><BR>Eltávolítja a megadott terheléselosztó-csoportot az erőforráscsoporthoz. |
| Nyilvános IP-cím törlése |[Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress)-Name "myIPAddress" – ResourceGroupName $myResourceGroup<BR><BR>Eltávolítja a megadott nyilvános IP-címet az erőforráscsoporthoz. |

## <a name="next-steps"></a>Következő lépések
Használja az imént létrehozott hálózati adaptert a [virtuális gép létrehozásakor](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


