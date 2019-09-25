---
title: Terheléselosztás és kimenő szabályok konfigurálása Azure PowerShell használatával
titlesuffix: Azure Load Balancer
description: Ez a cikk bemutatja, hogyan konfigurálhatja a terheléselosztási és a kimenő szabályokat egy standard Load Balancer a Azure PowerShell használatával.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 6e85d31e40e35b9d796fc66394a6eb446c7302ad
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262621"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-powershell"></a>Terheléselosztás és kimenő szabályok konfigurálása a standard Load balancerben Azure PowerShell használatával

Ez a cikk bemutatja, hogyan konfigurálhatja a kimenő szabályokat a standard Load balancerben a Azure PowerShell használatával.  

Ha elkészült, a terheléselosztó erőforrás két előtérbeli felületet és szabályt tartalmaz: egyet a bejövő és egy másikat a kimenő számára.  Minden egyes előtérbeli nyilvános IP-cím és a egy másik nyilvános IP-cím a bejövő és kimenő forgatókönyv használ vonatkozó hivatkozás van.   A terheléselosztási szabály csak a bejövő terheléselosztást biztosít, és a kimenő szabály szabályozza a kimenő NAT a virtuális Géphez megadott.  Ez a cikk két különálló háttér-készletet használ, amelyek közül az egyik a bejövő és az egyik a kimenő, a képesség szemléltetése és a rugalmasság biztosítása ehhez a forgatókönyvhöz.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-azure-account"></a>Kapcsolódás Azure-fiókhoz
Jelentkezzen be az Azure-előfizetésbe a [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) paranccsal, és kövesse a képernyőn megjelenő utasításokat:
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy erőforráscsoportot, nevű *myresourcegroupoutbound* a a *eastus2* helye:

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-virtual-network"></a>Virtuális hálózat létrehozása
Hozzon létre egy *myvnetoutbound* nevű virtuális hálózatot egy *mysubnetoutbound* nevű alhálózattal a *Myresourcegroupoutbound* [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) és [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0)használatával:

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-inbound-public-ip-address"></a>Bejövő nyilvános IP-cím létrehozása 

A webalkalmazás internetes eléréséhez a terheléselosztónak nyilvános IP-címmel kell rendelkeznie. A standard Load Balancer csak A standard nyilvános IP-címeket támogatja. A [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) használatával hozzon létre egy *Mypublicipinbound* nevű szabványos nyilvános IP-címet a *myresourcegroupoutbound*-ben.

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-outbound-public-ip-address"></a>Kimenő nyilvános IP-cím létrehozása 

Hozzon létre egy szabványos IP-címet a terheléselosztó előtér-kimeneti konfigurációjához a [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0)használatával.

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-azure-load-balancer"></a>Azure-terheléselosztó létrehozása

Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:
  - Egy előtérbeli IP-Címmel a terheléselosztón a bejövő hálózati forgalmat fogad.
  - Egy háttérbeli készlet, amelyben a előtéri IP-cím elküldi a terheléselosztási hálózati forgalmat.
  - Egy háttérbeli készlet a kimenő kapcsolathoz. 
  - az állapotfigyelő mintavételező, amely a háttérbeli Virtuálisgép-példányok állapotát határozza meg.
  - Egy terheléselosztó bejövő szabályt, amely meghatározza, hogyan ossza el a virtuális gépek forgalmat.
  - Egy terheléselosztó kimenő-szabályt, amely meghatározza, hogyan ossza a virtuális gépekről érkező forgalmat.

### <a name="create-inbound-frontend-ip"></a>Bejövő előtéri IP-cím létrehozása
Hozza létre a Load Balancer kimenő előtérbeli IP-konfigurációját a [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0) , amely tartalmaz egy *myfrontendinbound* nevű bejövő előtérbeli IP-konfigurációt, amely a nyilvános IP-címhez *van társítva. mypublicipinbound*

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-outbound-frontend-ip"></a>Kimenő előtérbeli IP-cím létrehozása
Hozza létre a Load Balancer kimenő előtérbeli IP-konfigurációját a [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0) , amely tartalmaz egy *myfrontendoutbound* nevű kimenő előtérbeli IP-konfigurációt, amely a nyilvános IP-címhez *van társítva. mypublicipoutbound*:

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-inbound-backend-pool"></a>Bejövő háttérrendszer-készlet létrehozása
Hozzon létre egy *bepoolinbound*nevű [új AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0) a terheléselosztó háttérbeli bejövő készletét:

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-outbound-backend-pool"></a>Kimenő háttérrendszer-készlet létrehozása
Hozzon létre egy további háttér-címkészletet a *bepooloutbound*nevű [új AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0) rendelkező virtuális gépek készletének kimenő kapcsolatának definiálásához. Egy különálló kimenő készlet létrehozása maximális rugalmasságot biztosít, de kihagyhatja ezt a lépést, és csak a bejövő *bepoolinbound* is használhatja.  :

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-health-probe"></a>Állapotminta létrehozása

Az állapotfigyelő mintavételező az összes virtuálisgép-példányt ellenőrzi, hogy biztosan képesek legyenek hálózati forgalom küldésére. A mintavételező tesztjén elbukó virtuálisgép-példányokat a rendszer eltávolítja a terheléselosztóból, és így is maradnak, amíg ismét online állapotúak nem lesznek, és a mintavételező tesztje azt nem jelzi, hogy megfelelő az állapotuk. Hozzon létre egy állapot-mintavételt a [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0) a virtuális gépek állapotának figyeléséhez. 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-load-balancing-rule"></a>Terheléselosztási szabály létrehozása

Egy terheléselosztó-szabályt az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérkészlet, a forgalom fogadásához, valamint a szükséges forrás és cél határozza meg. Hozzon létre egy terheléselosztó-szabályt a [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0) *myinboundlbrule* , hogy meghallgassa a 80-es portot a frontend-készlet *myfrontendinbound* , és terheléselosztásos hálózati forgalmat küldjön a háttérbeli címkészlet *felé a bepoolinbound* a 80-es portot is használja. 

>[!NOTE]
>Ez a terheléselosztási szabály letiltja az automatikus kimenő (ka) t a (z) **új választható disableoutboundsnat** paraméterrel rendelkező szabály eredményeképpen. Kimenő NAT csak a kimenő szabály által biztosított.

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

### <a name="create-outbound-rule"></a>Kimenő szabály létrehozása

Kimenő szabály meghatározása a előtérbeli nyilvános IP-cím, az előtérbeli által képviselt *myfrontendoutbound*, amely jelzi a kimenő NAT-forgalom, valamint a háttérkészlethez, amelyhez ez a szabály vonatkozik.  Kimenő szabály létrehozása *myoutboundrule* az összes virtuális gép (NIC IP-konfigurációk) a kimenő hálózati címfordítás *bepool* háttérkészlet.  Az alábbi parancsot is változik a kimenő üresjárat időkorlátja 4 15 perc és lefoglalja a 10000 SNAT helyett 1024 portokat.  További részletekért tekintse át a [New-AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0) .

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
Ha nem szeretne külön kimenő készletet használni, módosítsa a címkészlet argumentumot az előző parancsban a *$bepoolin* megadásához.  Azt javasoljuk, hogy használjon külön készleteket az eredményül kapott konfiguráció rugalmasságának és olvashatóságának érdekében.

### <a name="create-load-balancer"></a>Terheléselosztó létrehozása

Hozzon létre egy Load balancert a bejövő IP-címmel az *LB* nevű [új AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0) használatával, amely egy bejövő előtérbeli IP-konfigurációt és egy, a nyilvános IP-címhez *társított bepoolinbound-készletet tartalmaz.* az előző lépésben létrehozott mypublicipinbound.

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

Ezen a ponton folytathatja a virtuális gépek hozzáadását a háttér-készlet *bepoolinbound* __és__ *bepooloutbound* , ha frissíti a megfelelő NIC-erőforrások IP-konfigurációját a [Add-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest)használatával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0) paranccsal eltávolítható az erőforráscsoport, a terheléselosztó és az összes kapcsolódó erőforrás.

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>További lépések
Ebben a cikkben a standard Load balancert hozta létre, és konfigurálta a beérkező terheléselosztó forgalmi szabályait, a konfigurált és az állapot-mintavételt a háttér-készletben lévő virtuális gépekhez. Ha többet szeretne megtudni a Azure Load Balancerről, folytassa az oktatóanyagok az Azure Load Balancerhez című témakört.

> [!div class="nextstepaction"]
> [Azure Load Balancer-oktatóanyagok](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
