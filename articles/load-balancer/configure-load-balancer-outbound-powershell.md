---
title: Terheléselosztási és kimenő szabályok konfigurálása az Azure PowerShell használatával
titleSuffix: Azure Load Balancer
description: Ez a cikk bemutatja, hogyan konfigurálhatja a terheléselosztásés a kimenő szabályok standard terheléselosztó az Azure PowerShell használatával.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 5fd68f4559420ca688b3f4d6f6d66ee52db5191e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225443"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-azure-powershell"></a>Terheléselosztás i és kimenő szabályok konfigurálása a standard terheléselosztóban az Azure PowerShell használatával

Ez a cikk bemutatja, hogyan konfigurálhatja a kimenő szabályokat a standard terheléselosztóban az Azure PowerShell használatával.  

Amikor befejezi a cikk forgatókönyvét, a terheléselosztó erőforrás két előtér-és a hozzájuk tartozó szabályokat tartalmaz. Van egy előtér a bejövő forgalom és egy másik előtér a kimenő forgalom.  

Minden előtér egy nyilvános IP-címre hivatkozik. Ebben a forgatókönyvben a bejövő forgalom nyilvános IP-címe eltér a kimenő forgalom címétől.   A terheléselosztási szabály csak bejövő terheléselosztást biztosít. A kimenő szabály szabályozza a kimenő hálózati cím fordítása (NAT) a virtuális gép.  

A forgatókönyv két háttér-készletet használ: egyet a bejövő és egyet a kimenő forgalomhoz. Ezek a készletek jól szemléltetik a képességeket, és rugalmasságot biztosítanak a forgatókönyvhöz.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-azure-account"></a>Csatlakozás az Azure-fiókhoz
Jelentkezzen be Azure-előfizetésbe a [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) paranccsal. Ezután kövesse a képernyőn megjelenő utasításokat.
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Erőforráscsoport létrehozása a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)segítségével. Az Azure-erőforráscsoport egy logikai tároló, amelybe az Azure-erőforrások üzembe helyezése. Az erőforrások kezelése ezután a csoportból.

A következő példa létrehoz egy *myresourcegroupoutbound* nevű erőforráscsoportot az *eastus2* helyen:

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Hozzon létre egy *myvnetoutbound*nevű virtuális hálózatot. Nevezze el az *alhálózatot a mysubnetoutbound .* Helyezze a *myresourcegroupoutbound-ba* a [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) és a [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0)használatával.

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-an-inbound-public-ip-address"></a>Bejövő nyilvános IP-cím létrehozása 

A webalkalmazás interneten való eléréséhez nyilvános IP-címre van szükség a terheléselosztóhoz. A Standard Load Balancer csak a szabványos nyilvános IP-címeket támogatja. 

A [New-AzPublicIpAddress használatával](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) hozzon létre egy szabványos nyilvános IP-címet *mypublicipinbound* néven a *myresourcegroupoutbound kapcsolatban.*

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-outbound-public-ip-address"></a>Kimenő nyilvános IP-cím létrehozása 

Hozzon létre egy szabványos IP-címet a terheléselosztó előtér-kimenő konfigurációjához a [New-AzPublicIpAddress használatával.](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0)

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-azure-load-balancer"></a>Azure-terheléselosztó létrehozása

Ez a szakasz a terheléselosztó következő összetevőinek létrehozását és konfigurálását ismerteti:
  - Előtér-IP, amely fogadja a bejövő hálózati forgalmat a terheléselosztón
  - Egy háttérkészlet, ahol az előtér-IP a terheléselosztásos hálózati forgalmat küldi
  - Háttérkészlet a kimenő kapcsolatokhoz
  - Egy állapotminta, amely meghatározza a háttér-virtuálisgép-példányok állapotát
  - A terheléselosztó bejövő szabály, amely meghatározza, hogyan forgalom van elosztva a virtuális gépek
  - A terheléselosztó kimenő szabály, amely meghatározza, hogyan forgalom elosztott a virtuális gépek

### <a name="create-an-inbound-front-end-ip"></a>Bejövő előtér-IP létrehozása
Hozza létre a bejövő előtér-IP-konfigurációt a terheléselosztóhoz a [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0)használatával. A terheléselosztónak tartalmaznia kell egy bejövő előtér-IP-konfigurációt, amelyet *myfrontendinbound néven neveznek.* Társítsa ezt a konfigurációt a nyilvános IP-cím *mypublicipinbound*.

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-an-outbound-front-end-ip"></a>Kimenő előtér-IP létrehozása
Hozza létre a kimenő előtér-IP-konfigurációt a terheléselosztóhoz a [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0)használatával. Ennek a terheléselosztónak tartalmaznia kell egy kimenő előtér-IP-konfigurációt, *amelyet myfrontendoutbound*néven neveznek el. Társítsa ezt a konfigurációt a nyilvános IP-cím *mypublicipoutbound*.

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-an-inbound-back-end-pool"></a>Bejövő háttérkészlet létrehozása
A [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0)segítségével hozza létre a terheléselosztó háttér-bejövő készletét. Nevezze el a *készletbepoolinbound*nevet.

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-an-outbound-back-end-pool"></a>Kimenő háttérkészlet létrehozása
A következő paranccsal hozzon létre egy másik háttércímkészletet a virtuális gépek készletének kimenő kapcsolatának definiálásához a [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0)használatával. Nevezze el ezt a *készletet bepooloutbound*. 

Egy külön kimenő készlet létrehozásával maximális rugalmasságot biztosít. De kihagyhatja ezt a lépést, és csak a bejövő *bepoolinbound,* ha úgy tetszik.  

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

Az állapotminta ellenőrzi az összes virtuálisgép-példányok győződjön meg arról, hogy küldhetnek hálózati forgalmat. A mintavételi ellenőrzéseket sikertelen virtuálisgép-példány eltávolításra kerül a terheléselosztóból, amíg újra online állapotba nem kerül, és a mintavételi ellenőrzés megállapítja, hogy kifogástalan állapotú.The VM instance that fail the probe checks is removed from the load balancer until it goes back online and a probe check determines that it's healthy. 

A virtuális gépek állapotának figyeléséhez hozzon létre egy állapotminta segítségével [New-AzLoadBalancerProbeConfig.](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0) 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-a-load-balancer-rule"></a>Terheléselosztó szabály létrehozása

A terheléselosztó szabály határozza meg az előtér-IP-konfigurációa a bejövő forgalom és a háttér-készlet a forgalom fogadására. Meghatározza a szükséges forrás- és célportot is. 

Hozzon létre egy *myinboundlbrule* nevű terheléselosztó szabályt a [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0)használatával. Ez a szabály a *myfrontendinbound*előtér-medencében lévő 80-as portot fogja hallgatni. A 80-as portot is használja a terheléselosztásos hálózati forgalom küldéséhez a *bepoolinbound háttércímkészletbe.* 

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

>[!NOTE]
>Ez a terheléselosztási szabály letiltja az automatikus kimenő nat (SNAT) házirendet a **-DisableOutboundSNAT** paraméter miatt. A kimenő NAT-t csak a kimenő szabály biztosítja.

### <a name="create-an-outbound-rule"></a>Kimenő szabály létrehozása

Egy kimenő szabály határozza meg az előtér nyilvános IP-címet, amelyet az *előtér-myfrontendoutbound*képvisel. Ez az előtér lesz használva az összes kimenő NAT-forgalom, valamint a háttér-készlet, amelyre a szabály vonatkozik.  

A következő paranccsal hozzon létre egy kimenő szabályt *myoutboundrule* a kimenő hálózati fordítás a *bepool-os* IP-konfigurációk a bepool háttérkészletben.  A parancs 4-ről 15 percre módosítja a kimenő tétlen idő-outot. 1000 SNAT portot foglal le 1024 helyett. További információ: [New-AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0).

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
Ha nem szeretne külön kimenő készletet használni, módosíthatja az előző parancsban lévő címkészlet argumentumot, hogy *$bepoolin* adjon meg.  Azt javasoljuk, hogy külön készletek, hogy az eredményül kapott konfiguráció rugalmas és olvasható.

### <a name="create-a-load-balancer"></a>Terheléselosztó létrehozása

A következő paranccsal a [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0)használatával hozhat létre terheléselosztót a bejövő IP-címhez. Nevezze el a *terheléselosztót lb*. Tartalmaznia kell egy bejövő előtér-IP-konfigurációt. A háttér-készlet *bepoolinbound* kell társozni a nyilvános IP-címet *mypublicipinbound,* hogy az előző lépésben létrehozott.

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

Ezen a ponton továbbra is hozzáadhat a virtuális gépeket a *bepoolinbound* és *a bepooloutbound* háttérkészletekhez a megfelelő hálózati adapter-erőforrások IP-konfigurációjának frissítésével. Frissítse az erőforrás-konfigurációt az [Add-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest)használatával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoportra, a terheléselosztóra és a kapcsolódó erőforrásokra, az [Eltávolító-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0)segítségével eltávolíthatja őket.

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>További lépések
Ebben a cikkben létrehozott egy szabványos terheléselosztót, konfigurált a bejövő és a kimenő terheléselosztó forgalmi szabályokat, és konfigurált egy állapotminta a virtuális gépek a háttérkészletben. 

További információkért folytassa az [Azure Load Balancer oktatóanyagaival.](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
