---
title: Egy Azure internetkapcsolattal rendelkező load balancer létrehozása IPv6 – PowerShell |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy internetkapcsolattal rendelkező terheléselosztó konfigurálása IPv6-tal PowerShell használatával a Resource Managerhez
services: load-balancer
documentationcenter: na
author: KumudD
keywords: IPv6-alapú, az azure load balancer, kettős verem, nyilvános IP-cím, natív ipv6, mobil, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 71164899de9e4351e2da5ce469f0d7ae0373829f
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741334"
---
# <a name="get-started-creating-an-internet-facing-load-balancer-with-ipv6-using-powershell-for-resource-manager"></a>Internetkapcsolattal rendelkező terheléselosztó konfigurálása IPv6-tal PowerShell használatával a Resource Managerhez létrehozásának első lépései

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure CLI](load-balancer-ipv6-internet-cli.md)
> * [Sablon](load-balancer-ipv6-internet-template.md)




Az Azure Load Balancer 4. szintű (TCP, UDP) terheléselosztónak minősül. A terheléselosztó a felhőszolgáltatások vagy virtuális gépek kifogástalan állapotú szolgáltatási példányai között osztja meg a bejövő forgalmat egy terheléselosztói készletben, és ezáltal biztosítja a magas rendelkezésre állást. Az Azure Load Balancer a szolgáltatásokat több portra vagy több IP-címre, illetve portokra és IP-címekre egyaránt továbbíthatja.

## <a name="example-deployment-scenario"></a>A példában üzembe helyezési forgatókönyv

A következő ábra szemlélteti a terheléselosztási megoldás üzembe helyezéséhez ebben a cikkben.

![Terheléselosztói forgatókönyv](./media/load-balancer-ipv6-internet-ps/lb-ipv6-scenario.png)

Ebben a forgatókönyvben a következő Azure-erőforrásokat hoz létre:

* egy internetkapcsolattal rendelkező Load Balancer egy IPv4-és IPv6-alapú nyilvános IP-cím
* Két a nyilvános VIP-címek leképezése a privát végpontok terheléselosztási szabályok betöltése
* a két virtuális gép rendelkezésre állási csoport, amely tartalmazza.
* Két virtuális gép (VM)
* Az egyes virtuális Gépekhez rendelt IPv4- és IPv6-címekkel rendelkező virtuális hálózati adapter

## <a name="deploying-the-solution-using-the-azure-powershell"></a>Az Azure PowerShell használatával a megoldás üzembe helyezése

A következő lépések bemutatják, hogyan hozhat létre egy internetkapcsolattal rendelkező terheléselosztó az Azure Resource Manager PowerShell. Az Azure Resource Manager az egyes erőforrások létrehozása és konfigurálása egyenként történik, majd put együtt az erőforrás létrehozása.

A terheléselosztó üzembe helyezéséhez, hozzon létre, és konfigurálja a következő objektumokat:

* Előtér-IP-konfiguráció – a nyilvános IP-címeket tartalmazza a bejövő hálózati forgalomhoz.
* Háttércímkészlet – hálózati adaptereket (NIC) tartalmaz, amelyek segítségével a virtuális gépek fogadhatják a terheléselosztóról érkező hálózati forgalmat.
* Terheléselosztási szabályok – olyan szabályokat tartalmaz, amelyek a terheléselosztó nyilvános portjait rendelik hozzá háttércímkészlet portjaihoz.
* Bejövő NAT-szabályok – olyan szabályokat tartalmaz, amelyek a terheléselosztó nyilvános portjait rendelik hozzá egy adott virtuális gép portjához a háttércímkészletben.
* Mintavételezők – állapotfigyelő mintavételezőket tartalmaz, amelyek a virtuálisgép-példányok rendelkezésre állását ellenőrzik a háttércímkészletben.

A további információkat az [Azure Resource Manager support for Load Balancer](load-balancer-arm.md) (Azure Resource Manager-támogatás a terheléselosztóhoz) című rész tartalmazza.

## <a name="set-up-powershell-to-use-resource-manager"></a>A PowerShell beállítása a Resource Manager használatához

Győződjön meg arról, hogy a Powershellhez az Azure Resource Manager modul legújabb üzemi verziójával rendelkezik.

1. Bejelentkezés az Azure-bA

    ```powershell
    Connect-AzureRmAccount
    ```

    Amikor a rendszer erre kéri, adja meg a hitelesítő adatait.

2. Keresse meg a fiókot az előfizetésekben

    ```powershell
    Get-AzureRmSubscription
    ```

3. Válassza ki, hogy melyik Azure előfizetést fogja használni.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'
    ```

4. Hozzon létre egy erőforráscsoportot (hagyja ki ezt a lépést, ha egy meglévő erőforráscsoportot használ)

    ```powershell
    New-AzureRmResourceGroup -Name NRP-RG -location "West US"
    ```

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Virtuális hálózat és nyilvános IP-cím létrehozása az előtér-IP-címkészlethez

1. Hozzon létre egy virtuális hálózat alhálózatához.

    ```powershell
    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
    $vnet = New-AzureRmvirtualNetwork -Name VNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
    ```

2. Azure nyilvános IP-cím (PIP) erőforrások az előtér-IP-címkészlet létrehozása.

    ```powershell
    $publicIPv4 = New-AzureRmPublicIpAddress -Name 'pub-ipv4' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Static -IpAddressVersion IPv4 -DomainNameLabel lbnrpipv4
    $publicIPv6 = New-AzureRmPublicIpAddress -Name 'pub-ipv6' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Dynamic -IpAddressVersion IPv6 -DomainNameLabel lbnrpipv6
    ```

    > [!IMPORTANT]
    > A terheléselosztó a nyilvános IP-cím tartománycímkéjét használja a teljes Tartományneve előtagként. Ebben a példában a teljes tartománynevek a következők *lbnrpipv4.westus.cloudapp.azure.com* és *lbnrpipv6.westus.cloudapp.azure.com*.

## <a name="create-a-front-end-ip-configurations-and-a-back-end-address-pool"></a>Hozzon létre egy előtérbeli IP-konfigurációk és a egy háttér-címkészlet

1. Hozzon létre az előtér-címének konfigurációja, amely a létrehozott nyilvános IP-címet használ.

    ```powershell
    $FEIPConfigv4 = New-AzureRmLoadBalancerFrontendIpConfig -Name "LB-Frontendv4" -PublicIpAddress $publicIPv4
    $FEIPConfigv6 = New-AzureRmLoadBalancerFrontendIpConfig -Name "LB-Frontendv6" -PublicIpAddress $publicIPv6
    ```

2. Hozzon létre a háttér-címkészletet.

    ```powershell
    $backendpoolipv4 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv4"
    $backendpoolipv6 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv6"
    ```

## <a name="create-lb-rules-nat-rules-a-probe-and-a-load-balancer"></a>LB szabályok, NAT-szabályok, mintavétel és terheléselosztó létrehozása

Ez a példa a következő elemeket hozza létre:

* egy NAT-szabályt, a port 4443 443-as porton bejövő összes forgalmat lefordítja
* egy terheléselosztó-szabályt, amely elosztja a 80-as porton bejövő összes forgalmat a háttér-címkészletben szereplő címekhez tartozó 80-as porton.
* egy terheléselosztó-szabályt a 3389-es porton lévő virtuális gépek RDP-kapcsolatának engedélyezéséhez.
* egy mintavételi szabályt, az állapot nevű oldalon *HealthProbe.aspx* vagy egy szolgáltatás a 8080-as porton
* egy terheléselosztót, amely mindezeket az objektumokat használja

1. Hozza létre a NAT-szabályokat.

    ```powershell
    $inboundNATRule1v4 = New-AzureRmLoadBalancerInboundNatRuleConfig -Name "NicNatRulev4" -FrontendIpConfiguration $FEIPConfigv4 -Protocol TCP -FrontendPort 443 -BackendPort 4443
    $inboundNATRule1v6 = New-AzureRmLoadBalancerInboundNatRuleConfig -Name "NicNatRulev6" -FrontendIpConfiguration $FEIPConfigv6 -Protocol TCP -FrontendPort 443 -BackendPort 4443
    ```

2. Hozzon létre egy állapotmintát. Két módszer van a mintavétel konfigurálására:

    HTTP-mintavétel

    ```powershell
    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    ```

    vagy a TCP-mintavétel

    ```powershell
    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -Protocol Tcp -Port 8080 -IntervalInSeconds 15 -ProbeCount 2
    $RDPprobe = New-AzureRmLoadBalancerProbeConfig -Name 'RDPprobe' -Protocol Tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2
    ```

    Ebben a példában fogjuk használni a TCP-mintavétel.

3. Hozzon létre egy terheléselosztó-szabályt.

    ```powershell
    $lbrule1v4 = New-AzureRmLoadBalancerRuleConfig -Name "HTTPv4" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
    $lbrule1v6 = New-AzureRmLoadBalancerRuleConfig -Name "HTTPv6" -FrontendIpConfiguration $FEIPConfigv6 -BackendAddressPool $backendpoolipv6 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
    $RDPrule = New-AzureRmLoadBalancerRuleConfig -Name "RDPrule" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $RDPprobe -Protocol Tcp -FrontendPort 3389 -BackendPort 3389
    ```

4. Hozza létre a terheléselosztót, használja a korábban létrehozott objektumokkal.

    ```powershell
    $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name 'myNrpIPv6LB' -Location 'West US' -FrontendIpConfiguration $FEIPConfigv4,$FEIPConfigv6 -InboundNatRule $inboundNATRule1v6,$inboundNATRule1v4 -BackendAddressPool $backendpoolipv4,$backendpoolipv6 -Probe $healthProbe,$RDPprobe -LoadBalancingRule $lbrule1v4,$lbrule1v6,$RDPrule
    ```

## <a name="create-nics-for-the-back-end-vms"></a>Hálózati adapterek létrehozása a háttér-virtuális gépekhez

1. Töltse le a virtuális hálózat és a virtuális hálózati alhálózat, ahol a hálózati adaptereket kell létrehozni.

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -Name VNet -ResourceGroupName NRP-RG
    $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
    ```

2. IP-konfigurációk és a hálózati adapterek létrehozása a virtuális gépekhez.

    ```powershell
    $nic1IPv4 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4 -LoadBalancerInboundNatRule $inboundNATRule1v4
    $nic1IPv6 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6 -LoadBalancerInboundNatRule $inboundNATRule1v6
    $nic1 = New-AzureRmNetworkInterface -Name 'myNrpIPv6Nic0' -IpConfiguration $nic1IPv4,$nic1IPv6 -ResourceGroupName NRP-RG -Location 'West US'

    $nic2IPv4 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4
    $nic2IPv6 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6
    $nic2 = New-AzureRmNetworkInterface -Name 'myNrpIPv6Nic1' -IpConfiguration $nic2IPv4,$nic2IPv6 -ResourceGroupName NRP-RG -Location 'West US'
    ```

## <a name="create-virtual-machines-and-assign-the-newly-created-nics"></a>Virtuális gépek létrehozása és hozzárendelése az újonnan létrehozott hálózati adapter

Virtuális gép létrehozásával kapcsolatos további információkért lásd: [létrehozása és előkonfigurálása egy Windows virtuális gép a Resource Manager és az Azure PowerShell-lel](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)

1. Egy rendelkezésre állási csoport és a Tárfiók létrehozása

    ```powershell
    New-AzureRmAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG -location 'West US'
    $availabilitySet = Get-AzureRmAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG
    New-AzureRmStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct' -Location 'West US' -SkuName "Standard_LRS"
    $CreatedStorageAccount = Get-AzureRmStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct'
    ```

2. Minden virtuális gép létrehozása és hozzárendelése az előzőleg létrehozott hálózati adapter

    ```powershell
    $mySecureCredentials= Get-Credential -Message "Type the username and password of the local administrator account."

    $vm1 = New-AzureRmVMConfig -VMName 'myNrpIPv6VM0' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
    $vm1 = Set-AzureRmVMOperatingSystem -VM $vm1 -Windows -ComputerName 'myNrpIPv6VM0' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
    $vm1 = Set-AzureRmVMSourceImage -VM $vm1 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm1 = Add-AzureRmVMNetworkInterface -VM $vm1 -Id $nic1.Id -Primary
    $osDisk1Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM0osdisk.vhd"
    $vm1 = Set-AzureRmVMOSDisk -VM $vm1 -Name 'myNrpIPv6VM0osdisk' -VhdUri $osDisk1Uri -CreateOption FromImage
    New-AzureRmVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm1

    $vm2 = New-AzureRmVMConfig -VMName 'myNrpIPv6VM1' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
    $vm2 = Set-AzureRmVMOperatingSystem -VM $vm2 -Windows -ComputerName 'myNrpIPv6VM1' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
    $vm2 = Set-AzureRmVMSourceImage -VM $vm2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm2 = Add-AzureRmVMNetworkInterface -VM $vm2 -Id $nic2.Id -Primary
    $osDisk2Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM1osdisk.vhd"
    $vm2 = Set-AzureRmVMOSDisk -VM $vm2 -Name 'myNrpIPv6VM1osdisk' -VhdUri $osDisk2Uri -CreateOption FromImage
    New-AzureRmVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm2
    ```

## <a name="next-steps"></a>További lépések

[Bevezetés a belső terheléselosztók konfigurálásába](load-balancer-get-started-ilb-arm-ps.md)

[A terheléselosztó elosztási módjának konfigurálása](load-balancer-distribution-mode.md)

[A terheléselosztó üresjárati TCP-időtúllépési beállításainak konfigurálása](load-balancer-tcp-idle-timeout.md)
