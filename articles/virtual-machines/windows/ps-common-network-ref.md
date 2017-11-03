---
title: "Azure virtuális hálózatok általános PowerShell-parancsokat |} Microsoft Docs"
description: "Általános PowerShell-parancsok az első lépések egy virtuális hálózat és a kapcsolódó erőforrások virtuális gépek létrehozása."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 56e1a73c-8299-4996-bd03-f74585caa1dc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: davidmu
ms.openlocfilehash: 26378ff9f16af9724db08cc4013654557f1c838b
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2017
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Azure virtuális hálózatok általános PowerShell-parancsok

Ha azt szeretné, a virtuális gép létrehozásához, létrehozásához szükséges egy [virtuális hálózati](../../virtual-network/virtual-networks-overview.md) vagy egy meglévő virtuális hálózathoz, ahol felveheti a virtuális Géphez tudnia. Általában a virtuális gépek létrehozásakor szükség is érdemes lehet létrehozni a jelen cikkben ismertetett erőforrásokat.

Az Azure PowerShell legfrissebb verziójának telepítésével, a kívánt előfizetés kiválasztásával és a fiókjába való bejelentkezéssel kapcsolatos információkért lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).

Egyes változók akkor lehet hasznos, ha fut a parancsok egynél több cikkben:

- $location - hálózati erőforrások helyét. Használhat [Get-AzureRmLocation](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermlocation) található egy [földrajzi régió](https://azure.microsoft.com/regions/) meg működik.
- $myResourceGroup - hol találhatók a hálózati erőforrások az erőforráscsoport nevét.

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása

| Tevékenység | Parancs |
| ---- | ------- |
| Az alhálózati beállítások létrehozása |$Alhalozat_1 = [új AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) -Name "mySubnet1" - AddressPrefix XX. X.X.X/XX<BR>$Alhalozat_2 = új AzureRmVirtualNetworkSubnetConfig-Name "mySubnet2" - AddressPrefix XX. X.X.X/XX<BR><BR>Előfordulhat, hogy egy tipikus hálózati az alhálózat egy [internetre irányuló terheléselosztót](../../load-balancer/load-balancer-internet-overview.md) és egy külön alhálózatot a egy [belső terheléselosztó](../../load-balancer/load-balancer-internal-overview.md). |
| Virtuális hálózat létrehozása |$vnet = [New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetwork) -Name "myVNet" - ResourceGroupName $myResourceGroup-hely $location - AddressPrefix XX. X.X.X/XX-Alhalozat_2 $ $Alhalozat_1, alhálózati |
| Egy egyedi nevet a teszt |[Teszt-AzureRmDnsAvailability](https://docs.microsoft.com/powershell/module/azurerm.network/test-azurermdnsavailability) - DomainNameLabel "myDNS"-$location helye<BR><BR>Megadhatja, hogy a DNS-tartománynevet a [nyilvános IP-erőforrás](../../virtual-network/virtual-network-ip-addresses-overview-arm.md), amely a nyilvános IP-cím domainname.location.cloudapp.azure.com társítás hoz létre az Azure által kezelt DNS-kiszolgálók. A név csak betűket, számokat és kötőjeleket tartalmazhat. Az első és az utolsó karakternek betűnek kell lennie, vagy száma és a tartomány nevét az Azure-beli hely belül egyedieknek kell lenniük. Ha **igaz** lett visszaadva, akkor a választott név globálisan egyedi. |
| Hozzon létre egy nyilvános IP-címet |$pip = [New-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermpublicipaddress) -Name "myPublicIp" - ResourceGroupName $myResourceGroup - DomainNameLabel "myDNS"-hely $location - AllocationMethod dinamikus<BR><BR>A nyilvános IP-cím, amelyet korábban tesztelése, és az előtérbeli konfigurációja a terheléselosztó által használt tartománynevet használja. |
| Előtérbeli IP-konfiguráció létrehozása |$frontendIP = [New-AzureRmLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) -Name "myFrontendIP" - PublicIpAddress $pip<BR><BR>Az előtér konfiguráció tartalmazza a nyilvános IP-cím, amelyet korábban hozott létre a bejövő hálózati forgalmat. |
| Háttér-címkészlet létrehozása |$beAddressPool = [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>Belső címek biztosít a háttérkiszolgálón a terheléselosztó, amely egy hálózati adapteren keresztül érhetők el. |
| A mintavétel létrehozása |$healthProbe = [New-AzureRmLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) -Name "myProbe" - RequestPath "HealthProbe.aspx"-protokoll http – 80-as Port - IntervalInSeconds 15 - ProbeCount 2<BR><BR>A háttér címkészletet példánya virtuális gépek rendelkezésre állásának ellenőrzésére használt állapotfigyelő mintavételt tartalmazza. |
| Terheléselosztási szabály létrehozása |$lbRule = [New-AzureRmLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) -név HTTP - FrontendIpConfiguration $frontendIP - BackendAddressPool $beAddressPool-$healthProbe mintavételi-protokoll Tcp - FrontendPort 80 - BackendPort 80<BR><BR>A terheléselosztó a nyilvános port hozzárendelése egy háttér címkészletet port szabályokat tartalmaz. |
| Egy bejövő NAT-szabály létrehozása |$inboundNATRule = [New-AzureRmLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerinboundnatruleconfig) -Name "myInboundRule1" - FrontendIpConfiguration $frontendIP-protokoll TCP - FrontendPort 3441 - BackendPort 3389-es<BR><BR>A load balancer nyilvános port leképezése egy virtuális gépet a háttér címkészletet port szabályokat tartalmaz. |
| Terheléselosztó létrehozása |$loadBalancer = [New-AzureRmLoadBalancer](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancer) - ResourceGroupName $myResourceGroup-Name "myLoadBalancer"-hely $location - FrontendIpConfiguration $frontendIP - InboundNatRule $inboundNATRule - LoadBalancingRule $ lbRule - BackendAddressPool $beAddressPool-$healthProbe mintavétel |
| A hálózati illesztő létrehozása |$nic1 = [New-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermnetworkinterface) - ResourceGroupName $myResourceGroup-Name "myNIC" - hely $location - privateipaddress tulajdonságot XX. X.X.X-alhálózat $Alhalozat_2 - LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] - LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Egy adott hálózati csatoló a nyilvános IP-cím és a korábban létrehozott virtuális hálózati alhálózat létrehozása. |

## <a name="get-information-about-network-resources"></a>Hálózati erőforrás adatainak beolvasása

| Tevékenység | Parancs |
| ---- | ------- |
| Virtuális hálózatok listája |[Get-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetwork) - ResourceGroupName $myResourceGroup<BR><BR>Megjeleníti a virtuális hálózatok az erőforráscsoportban. |
| A virtuális hálózat adatainak megjelenítése |Get-AzureRmVirtualNetwork-Name "myVNet" - ResourceGroupName $myResourceGroup |
| Egy virtuális hálózat alhálózatai lista |Get-AzureRmVirtualNetwork-Name "myVNet" - ResourceGroupName $myResourceGroup &#124; Válassza ki az alhálózatot |
| Egy alhálózat adatainak beolvasása |[Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig) -Name "mySubnet1" - VirtualNetwork $vnet<BR><BR>Az alhálózat információ lekérése a megadott virtuális hálózat. A $vnet érték azt jelenti, hogy a Get-AzureRmVirtualNetwork által visszaadott objektum. |
| IP-címeinek listáját |[Get-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermpublicipaddress) - ResourceGroupName $myResourceGroup<BR><BR>Az erőforráscsoport nyilvános IP-címek listája. |
| Lista terheléselosztók |[Get-AzureRmLoadBalancer](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermloadbalancer) - ResourceGroupName $myResourceGroup<BR><BR>Az erőforráscsoport összes terheléselosztók sorolja fel. |
| Hálózati illesztők listája |[Get-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkinterface) - ResourceGroupName $myResourceGroup<BR><BR>Az erőforráscsoport a hálózati adapterek listája. |
| Egy adott hálózati csatoló adatainak beolvasása |Get-AzureRmNetworkInterface-Name "myNIC" - ResourceGroupName $myResourceGroup<BR><BR>Egy adott hálózati illesztőn információ lekérése. |
| A hálózati illesztő IP-konfigurációjának beolvasása |[Get-AzureRmNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkinterfaceipconfig) -Name "myNICIP" - Kezdőértékről $nic<BR><BR>A megadott hálózati adapter IP-konfigurációja információ lekérése. A $nic érték azt jelenti, hogy a Get-AzureRmNetworkInterface által visszaadott objektum. |

## <a name="manage-network-resources"></a>Hálózati erőforrások kezelése

| Tevékenység | Parancs |
| ---- | ------- |
| Adjon hozzá egy alhálózatot a virtuális hálózat |[Adja hozzá AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig) - AddressPrefix XX. X.X.X/XX-Name "mySubnet1" - VirtualNetwork $vnet<BR><BR>Alhálózat hozzáadása egy meglévő virtuális hálózatot. A $vnet érték azt jelenti, hogy a Get-AzureRmVirtualNetwork által visszaadott objektum. |
| A virtuális hálózat törlése |[Remove-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermvirtualnetwork) -Name "myVNet" - ResourceGroupName $myResourceGroup<BR><BR>Eltávolítja a megadott virtuális hálózat az erőforráscsoportot. |
| A hálózati illesztő törlése |[Remove-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermnetworkinterface) -Name "myNIC" - ResourceGroupName $myResourceGroup<BR><BR>A megadott hálózati adapter eltávolítása az erőforráscsoportot. |
| Terheléselosztó törlése |[Remove-AzureRmLoadBalancer](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermloadbalancer) -Name "myLoadBalancer" - ResourceGroupName $myResourceGroup<BR><BR>Az erőforráscsoport eltávolítja a megadott terheléselosztóhoz. |
| A nyilvános IP-cím törlése |[Remove-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermpublicipaddress)-Name "myIPAddress" - ResourceGroupName $myResourceGroup<BR><BR>A megadott nyilvános IP-cím eltávolítása az erőforráscsoportot. |

## <a name="next-steps"></a>Következő lépések
* Az imént létrehozott mikor hálózati felületen meg [hozzon létre egy virtuális Gépet](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* További tudnivalók: hogyan zajlik [több hálózati adapterrel rendelkező virtuális gép létrehozása](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md).

