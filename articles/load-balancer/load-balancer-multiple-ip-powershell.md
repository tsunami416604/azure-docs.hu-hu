---
title: "Több IP-konfigurációk az Azure-ban a terheléselosztási |} Microsoft Docs"
description: "Terheléselosztás elsődleges és másodlagos IP-konfiguráció között."
services: load-balancer
documentationcenter: na
author: anavinahar
manager: narayan
editor: na
ms.assetid: 244907cd-b275-4494-aaf7-dcfc4d93edfe
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: annahar
ms.openlocfilehash: 2235d007434dabde1639ab19bc6813c818ed5ed7
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="load-balancing-on-multiple-ip-configurations-using-powershell"></a>Hálózati terheléselosztást a PowerShell használatával több IP-konfigurációk

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [Parancssori felület](load-balancer-multiple-ip-cli.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Ez a cikk ismerteti az Azure Load Balancer használata a másodlagos hálózati adapteren (NIC) több IP-címmel. Ebben a forgatókönyvben két virtuális gépeken futó Windows, az elsődleges és másodlagos hálózati tudunk A másodlagos hálózati adapterek mindegyike rendelkezik, két IP-konfigurációk. Minden virtuális gép webhelyeket a contoso.com és fabrikam.com üzemelteti. Minden webhelyre van kötve egy IP-konfigurációk másodlagos hálózati adapteren Azure Load Balancer használatával teszi közzé a két előtérbeli IP-cím, egy, a megfelelő IP-konfiguráció a webhelyre irányuló forgalom terjeszteni minden webhelyre vonatkozóan. Ebben a példában ugyanazt a portszámot is frontends, valamint mindkét háttér címkészletet IP-címek között.

![Terheléselosztó forgatókönyv kép](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>A több IP-konfigurációk terheléselosztásához lépései

A cikkben ismertetett forgatókönyvben eléréséhez az alábbi lépésekkel:

1. Az Azure PowerShell telepítése. Az Azure PowerShell legfrissebb verziójának telepítésével, a kívánt előfizetés kiválasztásával és a fiókjába való bejelentkezéssel kapcsolatos információkért lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).
2. Hozzon létre egy erőforráscsoportot, a következő beállításokkal:

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    További információkért lásd a 2. lépés: [hozzon létre egy erőforráscsoportot](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

3. [Rendelkezésre állási csoport létrehozása](../virtual-machines/windows/tutorial-availability-sets.md?toc=%2fazure%2fload-balancer%2ftoc.json) magában foglalja a virtuális gépek. A jelen esetben használja a következő parancsot:

    ```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. Kövesse az utasításokat lépéseket 3-5 a [Windows virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) cikk készíti elő a virtuális gép egyetlen hálózati és létrehozása 6.1-es lépés végrehajtása, és használja a következő lépés 6.2 helyett:

    ```powershell
    $availset = Get-AzureRmAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzureRmVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    Fejezze be [Windows virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) lépések 6.3 6.8 keresztül.

5. A második IP-konfiguráció hozzáadása a virtuális gépek mindegyikének. Kövesse az utasításokat a [több IP-címek hozzárendelése a virtuális gépek](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add) cikk. A következő konfigurációs beállításokkal:

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    Nem kell a másodlagos IP-konfigurációk társítani a jelen oktatóanyag nyilvános IP-címeket. A parancsban távolítsa el a nyilvános IP-társítás része.

6. Újra 4 – 6. Ez a cikk lépéseinek elvégzését vm2 virtuális gépnek. Feltétlenül cserélje le a vm2 virtuális gépnek a virtuális gép nevét, ennek során. Vegye figyelembe, hogy nem kell virtuális hálózat létrehozása a második virtuális gép számára. Lehet, vagy nem hozható létre egy új alhálózatot a használati eset alapján.

7. Hozzon létre két nyilvános IP-címet, és tárolja őket a megfelelő változók látható módon:

    ```powershell
    $publicIP1 = New-AzureRmPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel contoso
    $publicIP2 = New-AzureRmPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel fabrikam

    $publicIP1 = Get-AzureRmPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam
    $publicIP2 = Get-AzureRmPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam
    ```

8. Hozzon létre két előtérbeli IP-konfigurációkat:

    ```powershell
    $frontendIP1 = New-AzureRmLoadBalancerFrontendIpConfig -Name contosofe -PublicIpAddress $publicIP1
    $frontendIP2 = New-AzureRmLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2
    ```

9. A háttér címkészletet, egy mintavételt és a terheléselosztási szabályok létrehozása:

    ```powershell
    $beaddresspool1 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzureRmLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzureRmLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. Ha már létrehozott ezeket az erőforrásokat, hozza létre a terheléselosztó:

    ```powershell
    $mylb = New-AzureRmLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. A második háttér cím címkészletet és az előtérbeli IP-konfiguráció az újonnan létrehozott terheléselosztó hozzáadása:

    ```powershell
    $mylb = Get-AzureRmLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzureRmLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzureRmLoadBalancer

    $mylb | Add-AzureRmLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzureRmLoadBalancer
    
    Add-AzureRmLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzureRmLoadBalancer
    ```

12. Az alábbi parancsok beolvasása a hálózati adaptert, és adja hozzá a háttér címkészletet, a terheléselosztó minden másodlagos hálózati adapter két IP-konfigurációk:

    ```powershell
    $nic1 = Get-AzureRmNetworkInterface -Name "VM1-NIC2" -ResourceGroupName "MyResourcegroup";
    $nic2 = Get-AzureRmNetworkInterface -Name "VM2-NIC2" -ResourceGroupName "MyResourcegroup";

    $nic1.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic1.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);
    $nic2.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic2.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);

    $mylb = $mylb | Set-AzureRmLoadBalancer

    $nic1 | Set-AzureRmNetworkInterface
    $nic2 | Set-AzureRmNetworkInterface
    ```

13. Végül konfigurálnia kell DNS-erőforrásrekordok a terheléselosztó megfelelő előtérbeli IP-címére mutasson. A tartományok Azure DNS-ben is tartalmazhat. Az Azure DNS-sel terheléselosztással kapcsolatos további információkért lásd: [Azure DNS használata más Azure-szolgáltatásokkal](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>További lépések
- További tudnivalók az Azure a terheléselosztási egyesítése [terheléselosztás szolgáltatások használata az Azure-ban](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Ismerje meg, hogyan használhatja különféle naplók az Azure-ban kezelésére és hibaelhárítására terheléselosztó [analytics keresse meg a Azure terheléselosztó](../load-balancer/load-balancer-monitor-log.md).
