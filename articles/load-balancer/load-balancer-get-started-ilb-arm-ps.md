---
title: "Belső terheléselosztó létrehozása a PowerShell használatával a Resource Managerben | Microsoft Docs"
description: "Ismerje meg, hogyan hozható létre belső terheléselosztó a PowerShell használatával a Resource Managerben"
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
tags: azure-resource-manager
ms.assetid: c6c98981-df9d-4dd7-a94b-cc7d1dc99369
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 1a1c3c15c51b1e441f21158510e92cc8de057352
ms.openlocfilehash: b9a8b333d688730b5a7773940201178876988ae4

---

# <a name="create-an-internal-load-balancer-using-powershell"></a>Belső terheléselosztó létrehozása a PowerShell használatával

> [!div class="op_single_selector"]
> * [Azure Portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Sablon](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!NOTE]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../resource-manager-deployment-model.md).  Ez a cikk a Resource Manager-alapú üzemi modell használatát ismerteti, amelyet a Microsoft a legtöbb új telepítéshez a [klasszikus üzemi modell](load-balancer-get-started-ilb-classic-ps.md) helyett javasol.

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

A következő lépések elmagyarázzák, hogyan hozható létre belső terheléselosztó az Azure Resource Manager és a PowerShell használatával. Az Azure Resource Managerrel a belső terheléselosztó létrehozásához szükséges elemek konfigurálása egyenként történik, majd a kombinálásukkal létrehozható egy terheléselosztó.

A terheléselosztó üzembe helyezéséhez a következő objektumokat kell létrehozni és konfigurálni:

* Előtér-IP-konfiguráció – a magánhálózati IP-címet fogja konfigurálni a bejövő hálózati forgalomhoz
* Háttércímkészlet – azokat a hálózati adaptereket fogja konfigurálni, amelyek az előtér-IP-címkészlettől érkező elosztott terhelésű forgalmat fogadják
* Terheléselosztási szabályok – a forrás- és a helyi port konfigurációja a terheléselosztóhoz.
* Mintavételek – konfigurálja az állapotmintákat a virtuálisgép-példányok számára.
* Bejövő NAT-szabályok – konfigurálja a virtuálisgép-példányok valamelyikének közvetlen elérésére vonatkozó portszabályokat.

További információkat szerezhet a terheléselosztónak az Azure Resource Managerben használt összetevőiről [Az Azure Resource Manager által nyújtott támogatás a terheléselosztó számára](load-balancer-arm.md) című részben.

A következő lépések elmagyarázzák, hogyan kell terheléselosztót konfigurálni két virtuális gép között.

## <a name="setup-powershell-to-use-resource-manager"></a>A PowerShell beállítása a Resource Manager használatához

Ellenőrizze, hogy a PowerShellhez az Azure-modul legújabb üzemi verziójával rendelkezik-e, és hogy a PowerShell megfelelően van-e beállítva az Azure-előfizetése eléréséhez.

### <a name="step-1"></a>1. lépés

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>2. lépés

Keresse meg a fiókot az előfizetésekben

```powershell
Get-AzureRmSubscription
```

A rendszer arra kéri, hogy végezzen hitelesítést a hitelesítő adataival.

### <a name="step-3"></a>3. lépés

Válassza ki, hogy melyek Azure-előfizetését használja.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="create-resource-group-for-load-balancer"></a>Terheléselosztó erőforráscsoportjának létrehozása

Hozzon létre egy új erőforráscsoportot (hagyja ki ezt a lépést, ha egy meglévő erőforráscsoportot használ)

```powershell
New-AzureRmResourceGroup -Name NRP-RG -location "West US"
```

Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport megadjon egy helyet. Ez szolgál az erőforráscsoport erőforrásainak alapértelmezett helyeként. Győződjön meg arról, hogy a terheléselosztó létrehozására irányuló összes parancs ugyanazt az erőforráscsoportot fogja használni.

A fenti példában létrehoztunk egy „NRP-RG” nevű erőforráscsoportot, amelynek a helye az USA nyugati régiója, „West US”.

## <a name="create-virtual-network-and-a-private-ip-address-for-front-end-ip-pool"></a>Virtuális hálózat és magánhálózati IP-cím létrehozása az előtér-IP-címkészlethez

Létrehoz egy alhálózatot a virtuális hálózathoz, és hozzárendeli a $backendSubnet változóhoz

```powershell
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
```

Virtuális hálózat létrehozása:

```powershell
$vnet= New-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
```

Létrehozza a virtuális hálózatot, és az lb-subnet-be alhálózatot hozzáadja az NRPVNet virtuális hálózathoz, majd hozzárendeli a $vnet változóhoz

## <a name="create-front-end-ip-pool-and-backend-address-pool"></a>Előtér-IP-címkészlet és háttércímkészlet létrehozása

Előtér-IP-címkészlet beállítása a terheléselosztóhoz tartozó bejövő hálózati forgalomhoz, valamint háttér-címkészlet beállítása a kiegyensúlyozott terhelésű forgalom fogadásához.

### <a name="step-1"></a>1. lépés

Előtér IP-címkészlet létrehozása a 10.0.2.5 magánhálózati IP-cím használatával a bejövő hálózati forgalom végpontjául szolgáló 10.0.2.0/24 alhálózathoz.

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id
```

### <a name="step-2"></a>2. lépés

Állítson be egy háttércímkészletet az előtér-IP-címkészletből bejövő forgalom fogadásához:

```powershell
$beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
```

## <a name="create-lb-rules-nat-rules-probe-and-load-balancer"></a>LB-szabályok, NAT-szabályok, mintavétel és terheléselosztó létrehozása

Az előtér-IP-készlet és a háttércímkészlet létrehozása után lére kell hoznia a terheléselosztó-erőforráshoz tartozó szabályokat is:

### <a name="step-1"></a>1. lépés

```powershell
$inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

$inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

$lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

A fenti példa a következő elemeket hozza létre:

* egy NAT-szabályt, amely a 3441-es portra érkező összes bejövő forgalmat a 3389-es portra továbbítja.
* egy második NAT-szabályt, amely a 3442-es portra érkező összes bejövő forgalmat a 3389-es portra továbbítja.
* egy terheléselosztó-szabályt, amely a nyilvános 80-as portra érkező összes bejövő forgalom terhelését elosztja a háttércímkészletben szereplő 80-as helyi porton.
* egy mintavételi szabályt, amely a „HealthProbe.aspx” elérési út állapotát fogja ellenőrizni

### <a name="step-2"></a>2. lépés

Hozza létre a terheléselosztót az összes objektum (NAT-szabályok, terheléselosztó-szabályok, mintavételi konfigurációk) együttes hozzáadásával:

```powershell
$NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
```

## <a name="create-network-interfaces"></a>Hálózati adapterek létrehozása

Miután létrehozta a belső terheléselosztót, meg kell határoznia a NAT-szabályokat és a mintavételeket, illetve hogy mely hálózati adapterek fogadják majd a bejövő, elosztott terhelésű hálózati forgalmat. Ebben az esetben a hálózati adapter önállóan van konfigurálva, és később hozzárendelhető egy virtuális géphez.

### <a name="step-1"></a>1. lépés

A hálózati adapterek létrehozásához olvassa be az erőforrásul szolgáló virtuális hálózatot és alhálózatot:

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

$backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
```

Ez a lépés egy olyan hálózati adaptert hoz létre, amely a terheléselosztó háttérkészletéhez fog tartozni, és társítja azt az adott hálózati adapter RDP-jére vonatkozó első NAT-szabályhoz:

```powershell
$backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
```

### <a name="step-2"></a>2. lépés

Hozzon létre egy második hálózati adaptert LB-Nic2-BE néven:

Ez a lépés létrehoz egy második hálózati adaptert, hozzárendeli azt a terheléselosztó ugyanazon háttérkészletéhez, és társítja az RDP-hez létrehozott második NAT-szabályhoz:

```powershell
$backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
```

A végeredmény a következőképpen fog megjelenni:

    $backendnic1

Várt kimenet:

    Name                 : lb-nic1-be
    ResourceGroupName    : NRP-RG
    Location             : westus
    Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
    Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
    ProvisioningState    : Succeeded
    Tags                 :
    VirtualMachine       : null
    IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
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
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
    DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
    AppliedDnsSettings   :
    NetworkSecurityGroup : null
    Primary              : False



### <a name="step-3"></a>3. lépés

Az Add-AzureRmVMNetworkInterface paranccsal rendelje hozzá a hálózati adaptert egy virtuális géphez.

A virtuális gép létrehozására és egy hálózati adapterhez történő hozzárendelésére vonatkozó lépésenkénti utasításokat a következő dokumentáció tartalmazza: [Azure-beli virtuális gép létrehozása a PowerShell használatával](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

## <a name="add-the-network-interface"></a>Hálózati adapter hozzáadása

Ha már létrehozott egy virtuális gépet, a hálózati adaptert a következő lépések segítségével adhatja hozzá:

### <a name="step-1"></a>1. lépés

Töltse be a terheléselosztó-erőforrást egy változóba (ha még nem tette meg). A használt változó neve $lb, és ugyanazokat a neveket használja, mint amiket a fent létrehozott terheléselosztó tartalmaz.

```powershell
$lb = Get-AzureRmLoadBalancer –name NRP-LB -resourcegroupname NRP-RG
```

### <a name="step-2"></a>2. lépés

Töltse be a háttér-konfigurációt egy változóba.

```powershell
$backend = Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb
```

### <a name="step-3"></a>3. lépés

Töltse be a már létrehozott hálózati adaptert egy változóba. a használt változó neve $nic. A használt hálózati adapter neve ugyanaz, mint a fenti példában.

```powershell
$nic = Get-AzureRmNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG
```

### <a name="step-4"></a>4. lépés

Módosítsa a hálózati adapter háttér-konfigurációját.

```powershell
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
```

### <a name="step-5"></a>5. lépés

Mentse a hálózati adapter objektumot.

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Miután hozzáadott egy hálózati adaptert a terheléselosztó háttérkészlethez, az elkezdi fogadni a hálózati forgalmat az adott terheléselosztó-erőforrásra vonatkozó terheléselosztási szabályok alapján.

## <a name="update-an-existing-load-balancer"></a>Meglévő terheléselosztó frissítése

### <a name="step-1"></a>1. lépés
A fenti példából származó terheléselosztó felhasználásával a Get-AzureRmLoadBalancer paranccsal rendeljen hozzá egy terheléselosztó objektumot az $slb változóhoz

```powershell
$slb = Get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

### <a name="step-2"></a>2. lépés

A következő példában egy új bejövő NAT-szabályt fog hozzáadni egy meglévő terheléselosztóhoz az előtérkészlet 81-es portját és a háttérkészlet 8181-es portját használva

```powershell
$slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp
```

### <a name="step-3"></a>3. lépés

A Set-AzureLoadBalancer paranccsal mentse az új konfigurációt

```powershell
$slb | Set-AzureRmLoadBalancer
```

## <a name="remove-a-load-balancer"></a>Terheléselosztó eltávolítása

A Remove-AzureRmLoadBalancer paranccsal törölje a korábban létrehozott „NRP-LB” nevű terheléselosztót az „NRP-RG” nevű erőforráscsoportból

```powershell
Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

> [!NOTE]
> A választható -Force kapcsolóval elkerülheti a törlésre vonatkozó kérdést.

## <a name="next-steps"></a>Következő lépések

[A terheléselosztó elosztási módjának konfigurálása](load-balancer-distribution-mode.md)

[A terheléselosztó üresjárati TCP-időtúllépési beállításainak konfigurálása](load-balancer-tcp-idle-timeout.md)



<!--HONumber=Nov16_HO3-->


