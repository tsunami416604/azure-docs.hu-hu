---
title: Terheléselosztás több IP-konfiguráción – Azure CLI
titleSuffix: Azure Load Balancer
description: Ebben a cikkben az Azure CLI használatával az elsődleges és másodlagos IP-konfigurációk terheléselosztása.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 6ac9e362314cc45e6adbdcf1390f70cbe6b05de8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74075962"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-powershell"></a>Terheléselosztás több IP-konfiguráción a PowerShell használatával

> [!div class="op_single_selector"]
> * [Portál](load-balancer-multiple-ip.md)
> * [parancssori felület](load-balancer-multiple-ip-cli.md)
> * [Powershell](load-balancer-multiple-ip-powershell.md)


Ez a cikk ismerteti, hogyan használhatja az Azure Load Balancer több IP-címegy másodlagos hálózati adapter (NIC). Ebben a forgatókönyvben két Windows-t futtató virtuális gépvan, mindegyik elsődleges és másodlagos hálózati adapterrel. A másodlagos hálózati adapterek mindegyike két IP-konfigurációval rendelkezik. Minden virtuális gép contoso.com és fabrikam.com webhelyeket is üzemeltet. Minden webhely a másodlagos hálózati adapter egyik IP-konfigurációjához van kötve. Az Azure Load Balancer segítségével két előtér-IP-címet, egyet-egyet a webhelyeken, a forgalom a megfelelő IP-konfiguráció a webhely reked. Ebben a forgatókönyvben ugyanazt a portszámot használja mindkét előtér-rendszerek között, valamint mindkét háttérkészlet IP-címét.

![LB-forgatókönyv képe](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Több IP-konfiguráció terhelésének lépései

Az alábbi lépéseket követve hajtsa végre a cikkben ismertetett forgatókönyvet:

1. Az Azure PowerShell telepítése. Az Azure PowerShell legfrissebb verziójának telepítésével, a kívánt előfizetés kiválasztásával és a fiókjába való bejelentkezéssel kapcsolatos információkért lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).
2. Hozzon létre erőforráscsoportot az alábbi beállításokkal:

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    További információt az [Erőforráscsoport létrehozása](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)című 2.

3. [Hozzon létre egy rendelkezésre állási készletet](../virtual-machines/windows/tutorial-availability-sets.md?toc=%2fazure%2fload-balancer%2ftoc.json) a virtuális gépek tárolására. Ebben az esetben használja a következő parancsot:

    ```powershell
    New-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. Kövesse a Windows [virtuális gép](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) ről szóló cikk 3–5. Hajtsa végre a 6.1.

    ```powershell
    $availset = Get-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    Ezután végezze [el a Windows virtuális gép](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) 6.3–6.8.

5. Adjon hozzá egy második IP-konfigurációt az egyes virtuális gépekhez. Kövesse a [több IP-cím hozzárendelése a virtuális gépekhez](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add) cikkben található utasításokat. Használja a következő konfigurációs beállításokat:

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    Ebben az oktatóanyagban nem kell társítania a másodlagos IP-konfigurációkat nyilvános IP-címekkel. A parancs szerkesztésével távolítsa el a nyilvános IP-társítási részt.

6. Hajtsa végre a cikk 4–6. Győződjön meg róla, hogy cserélje ki a virtuális gép nevét a VM2 erre. Vegye figyelembe, hogy nem kell virtuális hálózatot létrehoznia a második virtuális géphez. Előfordulhat, hogy a használati eset alapján új alhálózatot hoz létre.

7. Hozzon létre két nyilvános IP-címet, és tárolja őket a megfelelő változókban az ábrán látható módon:

    ```powershell
    $publicIP1 = New-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel contoso
    $publicIP2 = New-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel fabrikam

    $publicIP1 = Get-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam
    $publicIP2 = Get-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam
    ```

8. Hozzon létre két előtér-IP-konfigurációt:

    ```powershell
    $frontendIP1 = New-AzLoadBalancerFrontendIpConfig -Name contosofe -PublicIpAddress $publicIP1
    $frontendIP2 = New-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2
    ```

9. Hozza létre a háttérrendszer címkészleteit, a mintavételt és a terheléselosztási szabályokat:

    ```powershell
    $beaddresspool1 = New-AzLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. Miután létrehozta ezeket az erőforrásokat, hozza létre a terheléselosztót:

    ```powershell
    $mylb = New-AzLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. Adja hozzá a második háttércímkészletet és az előtér IP-konfigurációját az újonnan létrehozott terheléselosztóhoz:

    ```powershell
    $mylb = Get-AzLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzLoadBalancer

    $mylb | Add-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzLoadBalancer
    
    Add-AzLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzLoadBalancer
    ```

12. Az alábbi parancsok leírják a hálózati adaptereket, majd hozzáadják az egyes másodlagos hálózati adapterek mindkét IP-konfigurációját a terheléselosztó háttércímkészletéhez:

    ```powershell
    $nic1 = Get-AzNetworkInterface -Name "VM1-NIC2" -ResourceGroupName "MyResourcegroup";
    $nic2 = Get-AzNetworkInterface -Name "VM2-NIC2" -ResourceGroupName "MyResourcegroup";

    $nic1.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic1.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);
    $nic2.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic2.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);

    $mylb = $mylb | Set-AzLoadBalancer

    $nic1 | Set-AzNetworkInterface
    $nic2 | Set-AzNetworkInterface
    ```

13. Végül be kell állítania a DNS-erőforrásrekordokat úgy, hogy a terheléselosztó megfelelő előtér-IP-címére mutassanak. A tartományokat az Azure DNS-ben üzemeltetheti. Az Azure DNS és a terheléselosztó használatával kapcsolatos további tudnivalókért olvassa el [az Azure DNS használata más Azure-szolgáltatásokkal című témakört.](../dns/dns-for-azure-services.md)

## <a name="next-steps"></a>További lépések
- További információ a terheléselosztási szolgáltatások Azure-beli kombinálásáról [az Azure terheléselosztási szolgáltatásainak használata során.](../traffic-manager/traffic-manager-load-balancing-azure.md)
- Megtudhatja, hogy miként használhat különböző típusú naplókat az Azure-ban a terheléselosztó kezeléséhez és hibaelhárításához az [Azure Load Balancer naplóiban.](../load-balancer/load-balancer-monitor-log.md)
