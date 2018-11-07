---
title: Több IP-konfiguráció az Azure-ban a terheléselosztási |} A Microsoft Docs
description: Terheléselosztás több elsődleges és másodlagos IP-konfigurációkban.
services: load-balancer
documentationcenter: na
author: anavinahar
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: annahar
ms.openlocfilehash: 8abbab59bc2df739774001f53419968337090c90
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2018
ms.locfileid: "51218695"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-powershell"></a>Terheléselosztás több IP-konfiguráció PowerShell-lel

> [!div class="op_single_selector"]
> * [Portál](load-balancer-multiple-ip.md)
> * [Parancssori felület](load-balancer-multiple-ip-cli.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)


Ez a cikk több, a másodlagos hálózati adapter (NIC) IP-címekkel rendelkező Azure Load Balancer használatát ismerteti. Ebben a forgatókönyvben két virtuális gépet futtató Windows, minden elsődleges és a egy másodlagos hálózati adapterre van Mindegyik másodlagos hálózati adapter két IP-konfigurációval rendelkezik. Minden virtuális gép, amelyen webhelyek a contoso.com és fabrikam.com. Minden webhely van kötve, az IP-konfigurációk közül a másodlagos hálózati adapteren. Az Azure Load Balancer használatával két előtérbeli IP-cím, egy minden olyan webhelyhez, a megfelelő IP-konfigurációhoz a webhely forgalom elosztását teszi közzé. Ebben a forgatókönyvben használja ugyanazt a portszámot is előtérrendszer, valamint háttérbeli címkészlet IP-címe között.

![LB-forgatókönyv kép](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Terheléselosztás több IP-konfiguráció lépések

A következő cikkben ismertetett forgatókönyvben eléréséhez az alábbi lépésekkel:

1. Az Azure PowerShell telepítése. Az Azure PowerShell legfrissebb verziójának telepítésével, a kívánt előfizetés kiválasztásával és a fiókjába való bejelentkezéssel kapcsolatos információkért lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).
2. Hozzon létre egy erőforráscsoportot, a következő beállításokkal:

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    További információkért lásd a 2. lépés [hozzon létre egy erőforráscsoportot](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

3. [Hozzon létre egy rendelkezésre állási csoport](../virtual-machines/windows/tutorial-availability-sets.md?toc=%2fazure%2fload-balancer%2ftoc.json) tartalmazza a virtuális gépek. A jelen esetben használja a következő parancsot:

    ```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. Kövesse az utasításokat lépéseket 3 – 5 a [Windows virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) cikk egyetlen hálózati adapterrel. a virtuális gép létrehozásának előkészítése 6.1-es lépés végrehajtása, és használja a következő lépés 6.2 helyett:

    ```powershell
    $availset = Get-AzureRmAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzureRmVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    Végezze el [Windows virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) lépések 6.3 6.8 keresztül.

5. A második IP-konfiguráció hozzáadása a virtuális gépek mindegyike. Kövesse a [több IP-cím hozzárendelése a virtuális gépek](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add) cikk. Használja a következő beállításokat:

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    Nem kell a másodlagos IP-konfigurációk társítani a jelen oktatóanyag nyilvános IP-címek. A parancsban távolítsa el a nyilvános IP-társítás rész.

6. Hajtsa végre a 4 – 6, ez a cikk újra VM2 számára. Feltétlenül cserélje le a virtuális gép nevét, a VM2, ennek során. Vegye figyelembe, hogy nem kell a második virtuális gép virtuális hálózat létrehozása. Előfordulhat, hogy Ön, vagy nem hozható létre egy új alhálózatot a használati eset alapján.

7. Hozzon létre két nyilvános IP-címet, és tárolhatja őket a megfelelő változók látható módon:

    ```powershell
    $publicIP1 = New-AzureRmPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel contoso
    $publicIP2 = New-AzureRmPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel fabrikam

    $publicIP1 = Get-AzureRmPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam
    $publicIP2 = Get-AzureRmPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam
    ```

8. Hozzon létre két előtérbeli IP-konfigurációk:

    ```powershell
    $frontendIP1 = New-AzureRmLoadBalancerFrontendIpConfig -Name contosofe -PublicIpAddress $publicIP1
    $frontendIP2 = New-AzureRmLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2
    ```

9. Hozza létre a háttércímkészletek, mintavétel és a terheléselosztási szabályok:

    ```powershell
    $beaddresspool1 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzureRmLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzureRmLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. Ha már rendelkezik a következő erőforrások jönnek létre, a load balancer létrehozása:

    ```powershell
    $mylb = New-AzureRmLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. Adja hozzá az újonnan létrehozott terheléselosztó a második háttérrendszer cím-készlet és előtérbeli IP-konfiguráció:

    ```powershell
    $mylb = Get-AzureRmLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzureRmLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzureRmLoadBalancer

    $mylb | Add-AzureRmLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzureRmLoadBalancer
    
    Add-AzureRmLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzureRmLoadBalancer
    ```

12. Az alábbi parancsokat a hálózati adapter lekérése, és adja hozzá a load balancer-háttércímkészlet mindkét minden másodlagos hálózati adapter IP-konfigurációja:

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

13. Végül konfigurálnia kell DNS-erőforrásrekordok, a terheléselosztó megfelelő előtérbeli IP-címre mutasson. You may host your domains in Azure DNS. Azure DNS használata a Load Balancer kapcsolatos további információkért lásd: [Azure DNS használata más Azure-szolgáltatásokkal](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>További lépések
- Többféle terheléselosztási szolgáltatás az Azure-ban történő használatáról további [terheléselosztási szolgáltatás használata az Azure-ban](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Ismerje meg, hogyan használhatja naplók különböző típusú Azure-beli kezeléséhez, és a load balancer hibaelhárítása [Log analytics az Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).
