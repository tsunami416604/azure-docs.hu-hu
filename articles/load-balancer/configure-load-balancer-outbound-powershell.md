---
title: Terheléselosztás és kimenő szabályok konfigurálása Azure PowerShell használatával
titlesuffix: Azure Load Balancer
description: Ez a cikk bemutatja, hogyan konfigurálhatja a terheléselosztást és a kimenő szabályokat a standard Load Balancerban Azure PowerShell használatával.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: b621d56a76a51f9ce9df10f88f5c3f2f4babfc03
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71816026"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-azure-powershell"></a>Terheléselosztás és kimenő szabályok konfigurálása a standard Load Balancerban Azure PowerShell használatával

Ez a cikk bemutatja, hogyan konfigurálhatja a kimenő szabályokat a standard Load Balancerban Azure PowerShell használatával.  

A cikk forgatókönyvének befejezésekor a terheléselosztó erőforrás két előtért tartalmaz, valamint a hozzájuk tartozó szabályokat. A bejövő forgalomhoz és a kimenő forgalomhoz egy másik előtér tartozik.  

Mindegyik előtér egy nyilvános IP-címet hivatkozik. Ebben az esetben a bejövő forgalom nyilvános IP-címe eltér a kimenő forgalom címétől.   A terheléselosztási szabály csak bejövő terheléselosztást biztosít. A Kimenő szabály a virtuális gép kimenő hálózati címfordítását (NAT) szabályozza.  

A forgatókönyv két háttér-készletet használ: egyet a bejövő forgalomhoz, egy pedig a kimenő forgalomhoz. Ezek a készletek szemléltetik a képességet, és rugalmasságot biztosítanak a forgatókönyvhöz.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-azure-account"></a>Csatlakozás az Azure-fiókhoz
Jelentkezzen be az Azure-előfizetésbe a [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) parancs használatával. Ezután kövesse a képernyőn megjelenő utasításokat.
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)használatával. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi az Azure-erőforrásokat. A rendszer ezután felügyeli az erőforrásokat a csoportból.

A következő példában létrehozunk egy erőforráscsoportot, nevű *myresourcegroupoutbound* a a *eastus2* helye:

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Hozzon létre egy *myvnetoutbound*nevű virtuális hálózatot. Nevezze el az alhálózati *mysubnetoutbound*. Helyezze el a *myresourcegroupoutbound* -ben a [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) és a [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0)használatával.

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-an-inbound-public-ip-address"></a>Bejövő nyilvános IP-cím létrehozása 

Ha az interneten szeretné elérni a webalkalmazást, szüksége lesz egy nyilvános IP-címére a terheléselosztó számára. A standard Load Balancer csak a standard nyilvános IP-címeket támogatja. 

A [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) használatával hozzon létre egy *mypublicipinbound* nevű szabványos nyilvános IP-címet a *myresourcegroupoutbound*-ben.

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-outbound-public-ip-address"></a>Kimenő nyilvános IP-cím létrehozása 

Hozzon létre egy szabványos IP-címet a terheléselosztó előtér-kimenő konfigurációjához a [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0)használatával.

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-azure-load-balancer"></a>Azure-terheléselosztó létrehozása

Ez a szakasz a terheléselosztó következő összetevőinek létrehozását és konfigurálását ismerteti:
  - Egy előtéri IP-cím, amely a terheléselosztó bejövő hálózati forgalmát fogadja
  - Olyan háttérbeli készlet, amelyben az előtér-IP-cím elküldi a terheléselosztási hálózati forgalmat
  - A kimenő kapcsolatok háttér-készlete
  - A háttérbeli virtuálisgép-példányok állapotát meghatározó állapot-mintavétel
  - Egy terheléselosztó bejövő szabálya, amely meghatározza, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között
  - Egy terheléselosztó kimenő szabálya, amely meghatározza, hogy a rendszer hogyan ossza szét a forgalmat a virtuális gépekről

### <a name="create-an-inbound-front-end-ip"></a>Bejövő előtéri IP-cím létrehozása
Hozza létre a terheléselosztó bejövő előtér-IP-konfigurációját a [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0)használatával. A terheléselosztó tartalmaznia kell egy *myfrontendinbound*nevű bejövő ELŐTÉR-IP-konfigurációt. Társítsa ezt a konfigurációt a nyilvános IP- *mypublicipinbound*.

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-an-outbound-front-end-ip"></a>Kimenő előtéri IP-cím létrehozása
Hozzon létre egy kimenő előtér-IP-konfigurációt a terheléselosztó számára a [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0)használatával. A terheléselosztó tartalmaznia kell egy *myfrontendoutbound*nevű kimenő ELŐTÉR-IP-konfigurációt. Társítsa ezt a konfigurációt a nyilvános IP- *mypublicipoutbound*.

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-an-inbound-back-end-pool"></a>Bejövő háttérbeli készlet létrehozása
Hozza létre a terheléselosztó háttérbeli bejövő készletét a [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0)használatával. Nevezze el a készlet *bepoolinbound*.

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-an-outbound-back-end-pool"></a>Kimenő háttérrendszer-készlet létrehozása
A következő parancs használatával hozzon létre egy másik háttér-címkészletet a virtuális gépek készletének kimenő kapcsolatának definiálásához a [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0)használatával. Nevezze el ezt a készletet *bepooloutbound*. 

Egy külön kimenő készlet létrehozásával maximális rugalmasságot biztosít. Ezt a lépést azonban kihagyhatja, és csak a bejövő *bepoolinbound* használhatja, ha szeretné.  

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

Az állapot-mintavétel ellenőrzi az összes virtuálisgép-példányt, hogy képes legyen hálózati forgalmat küldeni. A mintavételi ellenőrzéseket nem tartalmazó virtuálisgép-példány törlődik a terheléselosztó-ből, amíg az online állapotba nem kerül, és a mintavételi ellenőrzés meghatározza, hogy az kifogástalan állapotú-e. 

A virtuális gépek állapotának figyeléséhez hozzon létre egy állapot-mintavételt a [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0)használatával. 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-a-load-balancer-rule"></a>Terheléselosztó szabály létrehozása

A terheléselosztó szabály a bejövő forgalom és a háttérrendszer előtérbeli IP-konfigurációját határozza meg a forgalom fogadásához. Meghatározza továbbá a szükséges forrás-és célport-portot is. 

Hozzon létre egy *myinboundlbrule* nevű terheléselosztó-szabályt a [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0)használatával. Ez a szabály a 80-es portot fogja figyelni az előtér-készlet *myfrontendinbound*. Az 80-es portot is használja, hogy a rendszer elosztott terhelésű hálózati forgalmat továbbítson a háttérbeli címkészlet *bepoolinbound*. 

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

>[!NOTE]
>Ez a terheléselosztási szabály letiltja az automatikus kimenő biztonságos NAT-t (SNAT) a **-új választható disableoutboundsnat** paraméter miatt. A kimenő NAT-t csak a Kimenő szabály kapja meg.

### <a name="create-an-outbound-rule"></a>Kimenő szabály létrehozása

A kimenő szabályok határozzák meg az előtéri nyilvános IP-címet, amelyet az előtér- *myfrontendoutbound*képvisel. Ez az előtér az összes kimenő NAT-forgalomhoz, valamint a szabály hatálya alá eső háttér-készlethez lesz használva.  

A következő parancs használatával hozzon létre egy kimenő *szabályt a* *Myoutboundrule* összes virtuális gép kimenő hálózati FORDÍTÁSÁHOZ (a hálózati adapterek IP-konfigurációjában).  A parancs a kimenő üresjárati időkorlátot 4 – 15 percre módosítja. 1 024 helyett 10 000 SNAT-portot foglal le. További információ: [New-AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0).

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
Ha nem szeretne külön kimenő készletet használni, módosítsa a címkészlet argumentumot az előző parancsban a *$bepoolin* megadásához.  Azt javasoljuk, hogy külön készleteket használjon, hogy a létrejövő konfiguráció rugalmas és olvasható legyen.

### <a name="create-a-load-balancer"></a>Load Balancer létrehozása

A következő paranccsal hozhat létre terheléselosztó-t a bejövő IP-címhez a [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0)használatával. Nevezze el a terheléselosztó *LB*-t. Tartalmaznia kell egy bejövő előtér-IP-konfigurációt. A háttérbeli készlet *bepoolinbound* az előző lépésben létrehozott nyilvános IP- *mypublicipinbound* kell társítani.

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

Ezen a ponton továbbra is hozzáadhatja a virtuális gépeket a *bepoolinbound* és a *bepooloutbound* háttérbeli készletekhez, ha frissíti a megfelelő hálózati adapterek erőforrásainak IP-konfigurációját. Frissítse az erőforrás-konfigurációt a [Add-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest)használatával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoport, a terheléselosztó és a kapcsolódó erőforrások használatára, eltávolíthatja őket a [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0)használatával.

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>További lépések
Ebben a cikkben létrehozta a standard Load balancert, konfigurálta a bejövő és kimenő terheléselosztó forgalmi szabályait, valamint beállította a virtuális gépekhez tartozó állapot-mintavételt a háttér-készletben. 

További információért folytassa a [Azure Load Balancer oktatóanyagokkal](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
