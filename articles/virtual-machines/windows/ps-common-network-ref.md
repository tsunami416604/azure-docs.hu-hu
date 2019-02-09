---
title: Gyakori PowerShell-parancsok az Azure Virtual Network |} A Microsoft Docs
description: Gyakori PowerShell-parancsot az első lépések a virtuális gépek egy virtuális hálózatot és az összes kapcsolódó erőforrás létrehozásához.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 56e1a73c-8299-4996-bd03-f74585caa1dc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 31a0d486f2540ea75a57b29b8f1da21839783468
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984638"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Gyakori PowerShell-parancsok az Azure-beli virtuális hálózatok

Hozzon létre egy virtuális gépet szeretne, ha szeretne létrehozni egy [virtuális hálózat](../../virtual-network/virtual-networks-overview.md) vagy egy meglévő virtuális hálózatot, amelyben a virtuális gép is hozzáadhatók ismertek. Általában egy virtuális Gépet hoz létre, amikor szükség is érdemes lehet létrehozni az erőforrásokat, ebben a cikkben leírt.

Az Azure PowerShell legfrissebb verziójának telepítésével, a kívánt előfizetés kiválasztásával és a fiókjába való bejelentkezéssel kapcsolatos információkért lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).

Bizonyos változókat lehet hasznos, ha több, mint a parancsok egyikét futtatja a ebben a cikkben:

- $location – a hálózati erőforrások helyét. Használhat [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) található egy [földrajzi régió](https://azure.microsoft.com/regions/) is működik, az Ön számára.
- $myResourceGroup – a nevet az erőforráscsoport, amelyben a hálózati erőforrások találhatók.

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása

| Tevékenység | Parancs |
| ---- | ------- |
| Alhálózat-konfigurációk létrehozása |$subnet1 = [új AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -Name "mySubnet1" - AddressPrefix XX. X.X.X/XX<BR>$subnet2 = New-AzVirtualNetworkSubnetConfig -Name "mySubnet2" -AddressPrefix XX.X.X.X/XX<BR><BR>Egy tipikus hálózati előfordulhat, hogy rendelkezik egy alhálózatot egy [internetkapcsolattal rendelkező load balancer](../../load-balancer/load-balancer-internet-overview.md) és a egy külön alhálózatot egy [belső load balancer](../../load-balancer/load-balancer-internal-overview.md). |
| Virtuális hálózat létrehozása |$vnet = [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup -Location $location -AddressPrefix XX.X.X.X/XX -Subnet $subnet1, $subnet2 |
| Egy egyedi tartománynevet tesztelése |[Test-AzDnsAvailability](https://docs.microsoft.com/powershell/module/az.network/test-azdnsavailability) -DomainNameLabel "myDNS" -Location $location<BR><BR>A DNS-tartománynevet is megadhat egy [nyilvános IP-erőforrásból](../../virtual-network/virtual-network-ip-addresses-overview-arm.md), amely a nyilvános IP-címet domainname.location.cloudapp.azure.com hozzárendelést hoz létre az Azure által felügyelt DNS-kiszolgálók. A név csak betűket, számokat és kötőjelet tartalmazhat. Az első és utolsó karakternek betűnek kell lennie, vagy száma és a tartomány nevét az Azure-helyen belül egyedinek kell lennie. Ha **igaz** ad vissza, a választott név globálisan egyedi. |
| Hozzon létre egy nyilvános IP-címet |$pip = [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) -Name "myPublicIp" -ResourceGroupName $myResourceGroup -DomainNameLabel "myDNS" -Location $location -AllocationMethod Dynamic<BR><BR>A nyilvános IP-címet, amelyet korábban teszteltük, és a előtér-konfiguráció a terheléselosztó által használt tartománynevet használja. |
| Hozzon létre egy előtérbeli IP-konfiguráció |$frontendIP = [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -Name "myFrontendIP" -PublicIpAddress $pip<BR><BR>Az előtér-konfiguráció tartalmazza a nyilvános IP-címet, amelyet korábban hozott létre, a bejövő hálózati forgalomhoz. |
| Hátércímkészlet létrehozása |$beAddressPool = [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>Belső címek biztosít a háttérrendszer a terheléselosztó, amely egy hálózati adapter keresztül érhetők el. |
| Mintavétel létrehozása |$healthProbe = [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) – neve "myProbe" - RequestPath "HealthProbe.aspx"-protokoll http – 80-as Port - IntervalInSeconds 15 - ProbeCount 2<BR><BR>A háttér címkészletben levő virtuálisgép-példányok rendelkezésre állásának ellenőrzéséhez állapotfigyelő mintavételezőket tartalmaz. |
| Terheléselosztási szabály létrehozása |$lbRule = [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>A terheléselosztó nyilvános portjait hozzárendelése a háttér címkészletben levő porthoz szabályokat tartalmaz. |
| Bejövő NAT-szabály létrehozása |$inboundNATRule = [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -Name "myInboundRule1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Egy adott virtuális gép a háttér címkészletben levő porthoz a terheléselosztó nyilvános portjait leképezési szabályokat tartalmaz. |
| Load Balancer létrehozása |$loadBalancer = [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) - ResourceGroupName $myResourceGroup – neve "myLoadBalancer"-hely $location - FrontendIpConfiguration $frontendIP - InboundNatRule $inboundNATRule - LoadBalancingRule $lbRule - Értékre $beAddressPool-mintavételi $healthProbe |
| Hozzon létre egy hálózati adaptert |$nic1 = [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) - ResourceGroupName $myResourceGroup – neve "myNIC" - hely $location - PrivateIpAddress XX. X.X.X-alhálózat $subnet2 - LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] - LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Hozzon létre egy hálózati adaptert a nyilvános IP-cím és a korábban létrehozott virtuális hálózat alhálózatához. |

## <a name="get-information-about-network-resources"></a>Hálózati erőforrás adatainak beolvasása

| Tevékenység | Parancs |
| ---- | ------- |
| Virtuális hálózatok listája |[Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>Listázza az erőforráscsoportban lévő összes virtuális hálózatot. |
| Virtuális hálózat adatainak lekérése |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup |
| Virtuális hálózat alhálózatai lista |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup &#124; Select Subnets |
| Egy alhálózat adatainak lekérése |[Get-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>A megadott virtuális hálózatban az alhálózat olyan adatainak beolvasása. A $vnet érték jelzi a Get-AzVirtualNetwork által visszaadott objektum. |
| IP-címeinek listáját |[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>Az erőforráscsoportban lévő nyilvános IP-címeket sorolja fel. |
| Terheléselosztók listája |[Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>Az erőforráscsoport összes a terheléselosztók listája. |
| Hálózati illesztők listája |[Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>Listázza az erőforráscsoportban lévő összes hálózati adaptert. |
| Hálózati adapter adatainak lekérése |Get-AzNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Egy adott hálózati adapter adatainak beolvasása. |
| A hálózati adapter IP-konfiguráció beolvasása |[Get-AzNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterfaceipconfig) -Name "myNICIP" -NetworkInterface $nic<BR><BR>A megadott hálózati adapter IP-konfiguráció adatainak beolvasása. A $nic érték jelzi a Get-AzNetworkInterface által visszaadott objektum. |

## <a name="manage-network-resources"></a>Hálózati erőforrások kezelése

| Tevékenység | Parancs |
| ---- | ------- |
| Adjon hozzá egy alhálózatot a virtuális hálózathoz |[Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig) -AddressPrefix XX.X.X.X/XX -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Alhálózat hozzáadása egy meglévő virtuális hálózatot. A $vnet érték jelzi a Get-AzVirtualNetwork által visszaadott objektum. |
| Virtuális hálózat törlése |[Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup<BR><BR>A megadott virtuális hálózat távolít el az erőforráscsoportot. |
| Egy hálózati adapter törlése |[Remove-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworkinterface) -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>A megadott hálózati adapter távolít el az erőforráscsoportot. |
| Terheléselosztó törlése |[Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer) -Name "myLoadBalancer" -ResourceGroupName $myResourceGroup<BR><BR>A megadott terheléselosztó távolít el az erőforráscsoportot. |
| Egy nyilvános IP-cím törlése |[Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress)-Name "myIPAddress" -ResourceGroupName $myResourceGroup<BR><BR>A megadott nyilvános IP-cím távolít el az erőforráscsoportot. |

## <a name="next-steps"></a>További lépések
* Az imént létrehozott mikor hálózati adaptert használja, [hozzon létre egy virtuális Gépet](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Ismerje meg, hogyan zajlik [több hálózati adapterrel rendelkező virtuális gép létrehozása](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md).

