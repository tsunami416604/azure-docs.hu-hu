---
title: Az Azure belső terheléselosztó létrehozása a PowerShell használatával
titlesuffix: Azure Load Balancer
description: Ismerje meg, hogyan hozható létre belső terheléselosztó az Azure PowerShell-modullal az Azure Resource Managerben
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 36543bf50cb015993841267fdac61ed42297d27e
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594372"
---
# <a name="create-an-internal-load-balancer-by-using-the-azure-powershell-module"></a>Belső terheléselosztó létrehozása az Azure PowerShell-modullal

> [!div class="op_single_selector"]
> * [Azure Portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Sablon](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="get-started-with-the-configuration"></a>A konfiguráció első lépései

Ez a cikk leírja, hogyan hozhat létre belső terheléselosztót az Azure Resource Manager használatával az Azure PowerShell-modulban. A Resource Manager-alapú üzemi modellben a belső terheléselosztó létrehozásához szükséges objektumok konfigurálása egyenként történik. A rendszer a létrehozott és konfigurált objektumok kombinálásával létrehoz egy terheléselosztót.

A terheléselosztó üzembe helyezéséhez létre kell hoznia a következő objektumokat:

* Előtérbeli IP-címkészlet: A magánhálózati IP-cím az összes bejövő hálózati forgalomhoz.
* Háttér-címkészlet: A hálózati adaptereket, az elosztott terhelésű forgalom fogadásához az előtér-IP-címről.
* Terheléselosztási szabályok: A terheléselosztó portjának (forrás és helyi) konfigurációja.
* Mintavételi konfiguráció: Virtuális gépek a állapotának mintavételei.
* Bejövő NAT-szabályok: A virtuális gépek közvetlen elérésére vonatkozó portszabályokat.

A terheléselosztó összetevőkről az [Azure Resource Manager terheléselosztó-támogatását](load-balancer-arm.md) ismertető cikkben talál további információt.

A következő lépések elmagyarázzák, hogyan kell terheléselosztót konfigurálni két virtuális gép között.

## <a name="set-up-powershell-to-use-resource-manager"></a>A PowerShell beállítása a Resource Manager használatához

Győződjön meg arról, hogy az Azure PowerShell-modul legújabb üzemi verziójával rendelkezik. A PowerShellnek megfelelően kell konfigurálva lennie az Azure-előfizetés eléréséhez.

### <a name="step-1-start-powershell"></a>1. lépés: A PowerShell elindítása

Indítsa el az Azure Resource Manager PowerShell-modulját.

```powershell
Connect-AzAccount
```

### <a name="step-2-view-your-subscriptions"></a>2. lépés: Az előfizetések megtekintése

Ellenőrizze az elérhető Azure-előfizetéseket.

```powershell
Get-AzSubscription
```

Amikor a rendszer a hitelesítést kéri, adja meg a hitelesítő adatait.

### <a name="step-3-select-the-subscription-to-use"></a>3. lépés: Válassza ki a használni kívánt előfizetést

Válassza ki, hogy melyik Azure-előfizetését használja a terheléselosztó üzembe helyezéséhez.

```powershell
Select-AzSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4-choose-the-resource-group-for-the-load-balancer"></a>4. lépés: Válassza ki az erőforráscsoportot, a terheléselosztó

Hozzon létre egy új erőforráscsoportot a terheléselosztóhoz. Hagyja ki ezt a lépést, ha meglévő erőforráscsoportot használ.

```powershell
New-AzResourceGroup -Name NRP-RG -location "West US"
```

Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport megadjon egy helyet. A hely lesz az erőforráscsoport összes erőforrásának alapértelmezett értéke. Mindig ugyanazt az erőforráscsoportot használja a terheléselosztó létrehozásához kapcsolódó összes parancshoz.

Az előző példában létrehoztunk egy **NRP-RG** nevű erőforráscsoportot, amelynek a helye az USA nyugati régiója.

## <a name="create-the-virtual-network-and-ip-address-for-the-front-end-ip-pool"></a>Virtuális hálózat és IP-cím létrehozása az előtérbeli IP-címkészlethez

Hozzon létre egy alhálózatot a virtuális hálózat számára, és rendelje hozzá a **$backendSubnet** változóhoz.

```powershell
$backendSubnet = New-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
```

Hozzon létre egy virtuális hálózatot.

```powershell
$vnet= New-AzVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
```

Létrejön a virtuális hálózat. A rendszer hozzáadja az **LB-Subnet-BE** alhálózatot az **NRPVNet** virtuális hálózathoz. Ezek az értékek a **$vnet** változóhoz vannak rendelve.

## <a name="create-the-front-end-ip-pool-and-back-end-address-pool"></a>Az előtérbeli IP-címkészlet és háttércímkészlet létrehozása

Hozzon létre egy előtérbeli IP-címkészletet a bejövő forgalomhoz és egy háttércímkészletet az elosztott terhelésű forgalomhoz.

### <a name="step-1-create-a-front-end-ip-pool"></a>1. lépés: Hozzon létre egy előtérbeli IP-címkészlet

Hozzon létre egy előtérbeli IP-címkészletet a 10.0.2.5 magánhálózati IP-címmel a 10.0.2.0/24 alhálózathoz. Ez a cím a bejövő hálózati forgalom végpontja.

```powershell
$frontendIP = New-AzLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id
```

### <a name="step-2-create-a-back-end-address-pool"></a>2. lépés: Háttércímkészlet létrehozása

Hozzon létre egy háttércímkészletet az előtérbeli IP-címkészletből bejövő forgalom fogadásához:

```powershell
$beaddresspool= New-AzLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
```

## <a name="create-the-configuration-rules-probe-and-load-balancer"></a>Konfigurációs szabályok, mintavétel és terheléselosztó létrehozása

Az előtérbeli IP-címkészlet és a háttércímkészlet létrehozása után adja meg a terheléselosztó erőforrás szabályait.

### <a name="step-1-create-the-configuration-rules"></a>1. lépés: A konfigurációs szabályok létrehozása

A példa a következő négy szabályobjektumot hozza létre:

* Bejövő NAT-szabály a távoli asztal protokoll (RDP) esetében: Átirányítja a 3441-es porton bejövő összes forgalmat a 3389-es portot.
* Egy második bejövő NAT-szabály RDP-hez: Átirányítja a 3442-es porton bejövő összes forgalmat a 3389-es portot.
* Állapotminta-szabály: A HealthProbe.aspx elérési út állapotát ellenőrzi.
* Egy terheléselosztó-szabályt: Elosztja a nyilvános 80-as portra 80-as helyi porton a háttér-címkészletben levő összes bejövő forgalmat.

```powershell
$inboundNATRule1= New-AzLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

$inboundNATRule2= New-AzLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

$healthProbe = New-AzLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

$lbrule = New-AzLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

### <a name="step-2-create-the-load-balancer"></a>2. lépés: A terheléselosztó létrehozása

Hozza létre a terheléselosztót, és kombinálja a szabályobjektumokat (bejövő NAT RDP-hez, terheléselosztó és állapotminta):

```powershell
$NRPLB = New-AzLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
```

## <a name="create-the-network-interfaces"></a>A hálózati adapterek létrehozása

Miután létrehozta a belső terheléselosztót, határozza meg azokat a hálózati adaptereket (NIC-ket), amelyek a bejövő, elosztott terhelésű hálózati forgalmat, NAT-szabályokat és mintákat fogják fogadni. Mindegyik hálózati adaptert önállóan kell konfigurálni, és később hozzá kell rendelni őket egy virtuális géphez.

### <a name="step-1-create-the-first-network-interface"></a>1. lépés: Az első hálózati adapter létrehozása

Kérje le az erőforrásul szolgáló virtuális hálózatot és alhálózatot. Ezekkel az értékekkel hozhatók létre a hálózati adapterek:

```powershell
$vnet = Get-AzVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

$backendSubnet = Get-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
```

Hozza létre az első hálózati adaptert **lb-nic1-be** néven. Rendelje az adaptert a terheléselosztó háttérkészletéhez. Társítsa az RDP első NAT-szabályát ezzel az NIC-vel:

```powershell
$backendnic1= New-AzNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
```

### <a name="step-2-create-the-second-network-interface"></a>2. lépés: A második hálózati adapter létrehozása

Hozza létre a második hálózati adaptert **lb-nic2-be** néven. Rendelje a második adaptert ugyanahhoz a terheléselosztó háttérkészlethez, amelyhez az első adaptert rendelte. Társítsa a második NIC-t az RDP második NAT-szabályával:

```powershell
$backendnic2= New-AzNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
```

Tekintse át a konfigurációt:

    $backendnic1

A beállításoknak a következőknek kell lenniük:

    Name                 : lb-nic1-be
    ResourceGroupName    : NRP-RG
    Location             : westus
    Id                   : /subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
    Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
    ProvisioningState    : Succeeded
    Tags                 :
    VirtualMachine       : null
    IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
                           "PublicIpAddress": {
                             "Id": null
                           },
                           "LoadBalancerBackendAddressPools": [
                             {
                               "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                             }
                           ],
                           "LoadBalancerInboundNatRules": [
                             {
                               "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                             }
                           ],
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                           "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
    DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
    AppliedDnsSettings   :
    NetworkSecurityGroup : null
    Primary              : False



### <a name="step-3-assign-the-nic-to-a-vm"></a>3. lépés: Az NIC hozzárendelése virtuális Géphez

Rendelje az NIC-t virtuális géphez az `Add-AzVMNetworkInterface` paranccsal.

A virtuális gépek létrehozásának és az NIC hozzárendelésének lépésenkénti útmutatójáért lásd: [Azure-beli virtuális gép létrehozása a PowerShell használatával](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

## <a name="add-the-network-interface"></a>Hálózati adapter hozzáadása

A virtuális gép létrehozása után adja hozzá a hálózati adaptert.

### <a name="step-1-store-the-load-balancer-resource"></a>1. lépés: A terheléselosztó-erőforráshoz Store

Tárolja a terheléselosztó-erőforrást egy változóban (ha még nem tette meg). Az **$lb** nevű változót használjuk. A szkriptben lévő attribútumértékekhez használja az előző lépésben létrehozott terheléselosztó-erőforrások neveit.

```powershell
$lb = Get-AzLoadBalancer –name NRP-LB -resourcegroupname NRP-RG
```

### <a name="step-2-store-the-back-end-configuration"></a>2. lépés: A háttér-konfiguráció Store

Tárolja a háttér-konfigurációt a **$backend** változóban.

```powershell
$backend = Get-AzLoadBalancerBackendAddressPoolConfig -name LB-backend -LoadBalancer $lb
```

### <a name="step-3-store-the-network-interface"></a>3. lépés: A hálózati adapter Store

Tárolja a hálózati adaptert egy másik változóban. Ez az adapter „A hálózati adapterek létrehozása, 1. lépés” című szakaszban jött létre. A **$nic1** nevű változót használjuk. Az előző példában használt hálózatiadapter-nevet használja.

```powershell
$nic = Get-AzNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG
```

### <a name="step-4-change-the-back-end-configuration"></a>4. lépés: A háttér-konfiguráció módosítása

Módosítsa a hálózati adapter háttér-konfigurációját.

```powershell
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
```

### <a name="step-5-save-the-network-interface-object"></a>5. lépés: A hálózati adapter objektumot mentése

Mentse a hálózati adapter objektumot.

```powershell
Set-AzNetworkInterface -NetworkInterface $nic
```

Miután hozzáadta az adaptert a háttérkészlethez, a hálózati forgalom terhelése ezen szabályok alapján oszlik el. Ezek a szabályok a „Konfigurációs szabályok, mintavétel és terheléselosztó létrehozása” című szakaszban lettek konfigurálva.

## <a name="update-an-existing-load-balancer"></a>Meglévő terheléselosztó frissítése

### <a name="step-1-assign-the-load-balancer-object-to-a-variable"></a>1. lépés: A terheléselosztó objektumot hozzárendelése egy változóhoz

Rendelje hozzá a terheléselosztó objektumot (az előző példából) az **$slb** változóhoz a `Get-AzLoadBalancer` paranccsal:

```powershell
$slb = Get-AzLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
```

### <a name="step-2-add-a-nat-rule"></a>2. lépés: NAT-szabály hozzáadása

Adjon egy új bejövő NAT-szabályt egy meglévő terheléselosztóhoz. Az előtérkészlet 81-es portját és a háttérkészlet 8181-es portját használja:

```powershell
$slb | Add-AzLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp
```

### <a name="step-3-save-the-configuration"></a>3. lépés: A konfiguráció mentése

Mentse az új konfigurációt a `Set-AzureLoadBalancer` paranccsal:

```powershell
$slb | Set-AzLoadBalancer
```

## <a name="remove-an-existing-load-balancer"></a>Meglévő terheléselosztó eltávolítása

Törölje az **NRP-LB** terheléselosztót az **NRP-RG** erőforráscsoportból a `Remove-AzLoadBalancer` paranccsal:

```powershell
Remove-AzLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
```

> [!NOTE]
> A választható **-Force** kapcsolóval megakadályozhatja a törlés megerősítésének kérését.

## <a name="next-steps"></a>További lépések

* [A terheléselosztó elosztási módjának konfigurálása](load-balancer-distribution-mode.md)
* [A terheléselosztó üresjárati TCP-időtúllépési beállításainak konfigurálása](load-balancer-tcp-idle-timeout.md)