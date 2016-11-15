---
title: "Internetkapcsolattal rendelkező terheléselosztó létrehozása a Resource Managerben a PowerShell használatával | Microsoft Docs"
description: "Ismerje meg, hogyan hozható létre internetkapcsolattal rendelkező terheléselosztó a Resource Managerben a PowerShell használatával"
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 8257f548-7019-417f-b15f-d004a1eec826
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7b74067b2f174e1242f5eb6c3028af4ef7b2f22e


---
# <a name="a-namegetstartedacreating-an-internetfacing-load-balancer-in-resource-manager-by-using-powershell"></a><a name="get-started"></a>Internetkapcsolattal rendelkező terheléselosztó létrehozása az Azure Resource Managerben a PowerShell használatával
[!INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a Resource Manager-alapú üzemi modellt ismerteti. Emellett [azt is megismerheti, hogyan hozható létre internetkapcsolattal rendelkező terheléselosztó a klasszikus üzemi modell használatával](load-balancer-get-started-internet-classic-cli.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-by-using-azure-powershell"></a>A megoldás telepítése az Azure PowerShell használatával
A következő eljárások bemutatják, hogyan hozható létre internetkapcsolattal rendelkező terheléselosztó az Azure Resource Managerben a PowerShell használatával. Az Azure Resource Manager lehetővé teszi, hogy az egyes erőforrások létrehozása és konfigurálása egyenként történjen, majd az összerakásukkal jöjjön létre egy terheléselosztó.

A terheléselosztó üzembe helyezéséhez a következő objektumokat kell létrehozni és konfigurálni:

* Előtér-IP-konfiguráció: a nyilvános IP-címeket (PIP) tartalmazza a bejövő hálózati forgalomhoz.
* Háttércímkészlet: hálózati adaptereket (NIC) tartalmaz, amelyek segítségével a virtuális gépek fogadhatják a terheléselosztóról érkező hálózati forgalmat.
* Terheléselosztási szabályok: olyan szabályokat tartalmaz, amelyek a terheléselosztó nyilvános portját rendelik hozzá a háttércímkészletben lévő porthoz.
* Bejövő NAT-szabályok: olyan szabályokat tartalmaz, amelyek a terheléselosztó nyilvános portjait rendelik hozzá egy adott virtuális gép portjához a háttércímkészletben.
* Mintavételezők: állapotfigyelő mintavételezőket tartalmaz, amelyek a virtuálisgép-példányok rendelkezésre állását ellenőrzik a háttércímkészletben.

A további információkat az [Azure Resource Manager support for Load Balancer](load-balancer-arm.md) (Azure Resource Manager-támogatás a terheléselosztóhoz) című rész tartalmazza.

## <a name="set-up-powershell-to-use-resource-manager"></a>A PowerShell beállítása a Resource Manager használatához
Győződjön meg arról, hogy az Azure Resource Manager modul legújabb üzemi verziójával rendelkezik a PowerShellhez:

1. Jelentkezzen be az Azure-ba.

        Login-AzureRmAccount

    Amikor a rendszer erre kéri, adja meg a hitelesítő adatait.
2. Keresse meg a fiókot az előfizetésekben.

        Get-AzureRmSubscription
3. Válassza ki, hogy melyek Azure-előfizetését használja.

        Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'
4. Hozzon létre egy erőforráscsoportot. (Hagyja ki ezt a lépést, ha egy meglévő erőforráscsoportot használ.)

        New-AzureRmResourceGroup -Name NRP-RG -location "West US"

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-frontend-ip-pool"></a>Virtuális hálózat és nyilvános IP-cím létrehozása az előtér-IP-címkészlethez
1. Hozzon létre egy alhálózatot és egy virtuális hálózatot.

        $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
        New-AzureRmvirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
2. Hozzon létre egy előtér-IP-címkészlet által használandó Azure-beli nyilvános IP-címet **PublicIP** névvel és **loadbalancernrp.westus.cloudapp.azure.com** DNS-névvel. A következő parancs a statikus felosztástípust használja.

        $publicIP = New-AzureRmPublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location 'West US' –AllocationMethod Static -DomainNameLabel loadbalancernrp

   > [!IMPORTANT]
   > A terheléselosztó a nyilvános IP-cím tartománycímkéjét használja a teljes tartományneve előtagjaként. Ez eltér a klasszikus üzemi modelltől, amely a felhőalapú szolgáltatást használja a terheléselosztó teljes tartományneveként.
   > Ebben a példában a **loadbalancernrp.westus.cloudapp.azure.com** a teljes tartománynév.
   >
   >

## <a name="create-a-frontend-ip-pool-and-a-backend-address-pool"></a>Előtér-IP-címkészlet és háttércímkészlet létrehozása
1. Hozzon létre egy **LB-Frontend** nevű előtér-IP-címkészletet, amely a **PublicIp** erőforrást használja.

        $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP
2. Hozzon létre egy **LB-backend** nevű háttércímkészletet.

        $beaddresspool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name LB-backend

## <a name="create-nat-rules-a-load-balancer-rule-a-probe-and-a-load-balancer"></a>NAT-szabályok, terheléselosztó-szabály, mintavétel és terheléselosztó létrehozása
Ez a példa a következő elemeket hozza létre:

* Egy NAT-szabályt, amely a 3441-es porton bejövő összes forgalmat lefordítja a 3389-es portra
* Egy NAT-szabályt, amely a 3442-es porton bejövő összes forgalmat lefordítja a 3389-es portra
* Egy mintavételi szabályt, amely az állapotot ellenőrzi a **HealthProbe.aspx** nevű oldalon
* Egy terheléselosztó-szabályt, amely elosztja a 80-as porton bejövő összes forgalmat a háttércímkészletben szereplő címekhez tartozó 80-as porton
* Egy terheléselosztót, amely mindezeket az objektumokat használja

Használja az alábbi lépéseket:

1. Hozza létre a NAT-szabályokat.

        $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP1 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

        $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP2 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389
2. Hozzon létre egy állapotmintát. Két módszer van a mintavétel konfigurálására:

    HTTP-mintavétel

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    TCP-mintavétel

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2
3. Hozzon létre egy terheléselosztó-szabályt.

        $lbrule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
4. Hozzon létre egy terheléselosztót a korábban létrehozott objektumokkal.

        $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name NRP-LB -Location 'West US' -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe

## <a name="create-nics"></a>Hálózati adapterek létrehozása
Hozzon létre hálózati adaptereket (vagy módosítsa a meglévőket), majd társítsa őket a NAT-szabályokhoz, a terheléselosztási szabályokhoz és a mintavételekhez:

1. Szerezze be azt a virtuális hálózatot és egy virtuális alhálózatot, ahol a hálózati adaptereket létre kell hozni.

        $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
        $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
2. Hozzon létre egy hálózati adaptert **lb-nic1-be** néven, majd társítsa az első NAT-szabállyal és az első (és egyetlen) háttércímkészlettel.

        $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic1-be -Location 'West US' -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
3. Hozzon létre egy hálózati adaptert **lb-nic2-be** néven, majd társítsa a második NAT-szabállyal és az első (és egyetlen) háttércímkészlettel.

        $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic2-be -Location 'West US' -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
4. Ellenőrizze a hálózati adaptereket.

        $backendnic1

    Várt kimenet:

        Name                 : lb-nic1-be
        ResourceGroupName    : NRP-RG
        Location             : westus
        Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
        ResourceGuid         : 896cac4f-152a-40b9-b079-3e2201a5906e
        ProvisioningState    : Succeeded
        Tags                 :
        VirtualMachine       : null
        IpConfigurations     : [
                            {
                            "Name": "ipconfig1",
                            "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                            "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1",
                            "PrivateIpAddress": "10.0.2.6",
                            "PrivateIpAllocationMethod": "Static",
                            "Subnet": {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                            },
                            "ProvisioningState": "Succeeded",
                            "PrivateIpAddressVersion": "IPv4",
                            "PublicIpAddress": {
                                "Id": null
                            },
                            "LoadBalancerBackendAddressPools": [
                                {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                                }
                            ],
                            "LoadBalancerInboundNatRules": [
                                {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                                }
                            ],
                            "Primary": true,
                            "ApplicationGatewayBackendAddressPools": []
                            }
                        ]
        DnsSettings          : {
                            "DnsServers": [],
                            "AppliedDnsServers": [],
                            "InternalDomainNameSuffix": "prcwibzcuvie5hnxav0yjks2cd.dx.internal.cloudapp.net"
                        }
        EnableIPForwarding   : False
        NetworkSecurityGroup : null
        Primary              :
5. Az `Add-AzureRmVMNetworkInterface` parancsmag használatával rendelje hozzá a hálózati adaptereket különböző virtuális gépekhez.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
A virtuális gép létrehozásával és egy hálózati adapter hozzárendelésével kapcsolatos útmutatásért lásd: [Azure-beli virtuális gép létrehozása a PowerShell használatával](../virtual-machines/virtual-machines-windows-ps-create.md).

## <a name="add-the-network-interface-to-the-load-balancer"></a>A hálózati adapter hozzáadása a terheléselosztóhoz
1. Olvassa be a terheléselosztót az Azure-ból.

    Töltse be a terheléselosztó-erőforrást egy változóba (ha még nem tette meg). A változó neve **$lb**. Használja ugyanazokat a neveket, amelyeket a korábban létrehozott terheléselosztó-erőforrásban használt.

        $lb= get-azurermloadbalancer –name NRP-LB -resourcegroupname NRP-RG
2. Töltse be a háttér-konfigurációt egy változóba.

        $backend=Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb
3. Töltse be a már létrehozott hálózati adaptert egy változóba. A változó neve **$nic**. A hálózati adapter neve ugyanaz, mint a korábbi példában.

        $nic =get-azurermnetworkinterface –name lb-nic1-be -resourcegroupname NRP-RG
4. Módosítsa a hálózati adapter háttér-konfigurációját.

        $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
5. Mentse a hálózati adapter objektumot.

        Set-AzureRmNetworkInterface -NetworkInterface $nic

    Miután hozzáadott egy hálózati adaptert a terheléselosztó háttérkészletéhez, az elkezdi fogadni a hálózati forgalmat az adott terheléselosztó-erőforrásra vonatkozó terheléselosztási szabályok alapján.

## <a name="update-an-existing-load-balancer"></a>Meglévő terheléselosztó frissítése
1. A korábbi példából származó terheléselosztó felhasználásával a `Get-AzureLoadBalancer` paranccsal rendeljen hozzá egy terheléselosztó objektumot az **$slb** változóhoz.

        $slb = get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
2. A következő példában egy bejövő NAT-szabályt ad hozzá – az előtérkészlet 81-es portját és a háttérkészlet 8181-es portját használva – egy meglévő a terheléselosztóhoz.

        $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol TCP
3. Mentse az új konfigurációt a `Set-AzureLoadBalancer` paranccsal.

        $slb | Set-AzureRmLoadBalancer

## <a name="remove-a-load-balancer"></a>Terheléselosztó eltávolítása
A `Remove-AzureLoadBalancer` paranccsal törölje a korábban létrehozott **NRP-LB** nevű terheléselosztót az **NRP-RG** nevű erőforráscsoportból.

    Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

> [!NOTE]
> A választható **-Force** kapcsolóval elkerülheti a törlésre vonatkozó kérdést.
>
>

## <a name="next-steps"></a>Következő lépések
[Bevezetés a belső terheléselosztók konfigurálásába](load-balancer-get-started-ilb-arm-ps.md)

[A terheléselosztó elosztási módjának konfigurálása](load-balancer-distribution-mode.md)

[A terheléselosztó üresjárati TCP-időtúllépési beállításainak konfigurálása](load-balancer-tcp-idle-timeout.md)



<!--HONumber=Nov16_HO2-->


