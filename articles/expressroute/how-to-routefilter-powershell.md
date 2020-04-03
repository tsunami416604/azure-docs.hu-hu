---
title: 'ExpressRoute: Útvonalszűrők – Microsoft társviszony-létesítés:Azure PowerShell'
description: Ez a cikk azt ismerteti, hogyan konfigurálhatók az útvonalszűrők a Microsoft Társviszony-létesítéséhez a PowerShell használatával
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 3fa53258321b22e1683122edca1816f6d4c291b5
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618607"
---
# <a name="configure-route-filters-for-microsoft-peering-powershell"></a>Útvonalszűrők konfigurálása A Microsoft társviszony-létesítés: PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Az útvonalszűrők lehetővé teszik a támogatott szolgáltatások egy részének felhasználását Microsoft-társviszony-létesítésen keresztül. A cikkben ismertetett lépések segítségével konfigurálhatja és kezelheti az ExpressRoute-áramkörök útvonalszűrőit.

Az Office 365-szolgáltatások, például az Exchange Online, a SharePoint Online és a Skype Vállalati verzió, valamint az Azure-beli közszolgáltatások, például a tárolás és az SQL DB a Microsoft társviszony-létesítésén keresztül érhetők el. Az Azure-közszolgáltatások régiónként választhatók ki, és nem határozhatók meg közszolgáltatásonként.

Ha a Microsoft-társviszony-létesítés ExpressRoute-kapcsolatcsoporton van konfigurálva, és egy útvonalszűrő csatlakozik, az ezekhez a szolgáltatásokhoz kiválasztott összes előtagot a létrehozott BGP-munkameneteken keresztül hirdeti a rendszer. Minden előtaghoz egy BGP-közösségérték van csatolva, amely azonosítja az előtag keretében nyújtott szolgáltatást. A BGP-közösség értékeinek és a hozzáuk uk leképezett szolgáltatásoknak a listáját a [BGP-közösségek című témakörben tetszésszerint.](expressroute-routing.md#bgp)

Ha az összes szolgáltatáshoz szüksége van, a BGP-n keresztül nagy számú előtagot hirdet a szolgáltatás. Ez jelentősen megnöveli a hálózaton belüli útválasztók által fenntartott útvonaltáblák méretét. Ha a Microsoft társviszony-létesítésen keresztül kínált szolgáltatásoknak csak egy részét kívánja felhasználni, kétféleképpen csökkentheti az útvonaltáblák méretét. A következőket teheti:

- A nem kívánt előtagok kiszűrése útvonalszűrők alkalmazásával a BGP-közösségeken. Ez egy szabványos hálózati gyakorlat, és gyakran használják számos hálózaton belül.

- Definiálja az útvonalszűrőket, és alkalmazza őket az ExpressRoute-kapcsolatra. Az útvonalszűrő egy új erőforrás, amely lehetővé teszi a Microsoft-társviszony-létesítés során használni kívánt szolgáltatások listájának kiválasztását. Az ExpressRoute-útválasztók csak az útvonalszűrőben azonosított szolgáltatásokhoz tartozó előtagok listáját küldik el.

### <a name="about-route-filters"></a><a name="about"></a>Útvonalszűrők –

Ha a Microsoft társviszony-létesítése konfigurálva van az ExpressRoute-kapcsolatcsoporton, a Microsoft hálózati peremhálózati útválasztói bgp-munkameneteket hoznak létre a peremhálózati útválasztókkal (az Önével vagy a kapcsolatszolgáltatóéval). Nincsenek útvonalak meghirdetve a hálózatán. Ha engedélyezni szeretné az útvonalhirdetéseket a hálózaton, társítania kell egy útvonalszűrőt.

Az útvonalszűrőkkel azonosíthatja az ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítésén keresztül használni kívánt szolgáltatásokat. Ez lényegében egy engedélyezési lista az összes BGP közösségi értékeket. Miután meghatározott és egy ExpressRoute-kapcsolatcsoporthoz csatolt egy útvonalszűrő erőforrást, a BGP-közösségértékekhez rendelt összes előtag meg van hirdetve a hálózaton.

Ahhoz, hogy útvonalszűrőket csatolhassanon az Office 365-szolgáltatásokhoz, engedéllyel kell rendelkeznie ahhoz, hogy az ExpressRoute-on keresztül office 365-szolgáltatásokat használja. Ha nincs jogosult office 365-szolgáltatások felhasználására az ExpressRoute-on keresztül, az útvonalszűrők csatolására irányuló művelet sikertelen lesz. Az engedélyezési folyamatról további információt az [Office 365-alapú Azure ExpressRoute című témakörben talál.](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)

> [!IMPORTANT]
> 2017. augusztus 1-je előtt konfigurált ExpressRoute-áramkörök Microsoft-társviszony-létesítése az összes szolgáltatáselőtagot a Microsoft társviszony-létesítésén keresztül hirdeti, még akkor is, ha nincsenek meghatározva az útvonalszűrők. 2017. augusztus 1-jén vagy azt követően konfigurált ExpressRoute-áramkörök Microsoft-társviszony-létesítése nem rendelkezik előtagokkal, amíg egy útvonalszűrő nem csatlakozik az áramkörhöz.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Munkafolyamat

Ahhoz, hogy a Microsoft társviszony-létesítésével sikeresen kapcsolódhatna a szolgáltatásokhoz, a következő konfigurációs lépéseket kell végrehajtania:

- Olyan aktív ExpressRoute-kapcsolattal kell rendelkeznie, amelyhez a Microsoft társviszony-létesítése is van. Az alábbi utasításokat használhatja a következő feladatok elvégzéséhez:
  - [Hozzon létre egy ExpressRoute-áramkört,](expressroute-howto-circuit-arm.md) és a folytatás előtt engedélyezve legyen a kapcsolatszolgáltató. Az ExpressRoute-kapcsolatcsoportnak kiépített és engedélyezett állapotban kell lennie.
  - [Hozzon létre Microsoft-társviszony-létesítést,](expressroute-circuit-peerings.md) ha közvetlenül kezeli a BGP-munkamenetet. Vagy a kapcsolatszolgáltató üzembe helyezheti a Microsoft-társviszony-létesítést a kapcsolatcsoporthoz.

-  Létre kell hoznia és konfigurálnia kell egy útvonalszűrőt.
    - Azonosítsa azokat a szolgáltatásokat, amelyeket a Microsoft társviszony-létesítésével szeretne igénybe adni
    - A szolgáltatásokhoz társított BGP-közösségi értékek listájának azonosítása
    - Hozzon létre egy szabályt, amely engedélyezi a BGP-közösség értékeinek megfelelő előtaglistát

-  Az útvonalszűrőt csatolnia kell az ExpressRoute-kapcsolathoz.

## <a name="before-you-begin"></a>Előkészületek

A konfiguráció megkezdése előtt győződjön meg arról, hogy megfelel az alábbi feltételeknek:

 - A konfiguráció megkezdése előtt tekintse át az [előfeltételeket](expressroute-prerequisites.md) és [a munkafolyamatokat.](expressroute-workflows.md)

 - Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-arm.md) részben foglalt lépéseket, és engedélyeztesse a kapcsolatcsoportot kapcsolatszolgáltatójával, mielőtt továbblépne. Az ExpressRoute-kapcsolatcsoportnak kiépített és engedélyezett állapotban kell lennie.

 - Aktív Microsoft-társviszony-létesítésre van szüksége. Kövesse a [társviszony-létesítés konfigurációs](expressroute-circuit-peerings.md) cikk létrehozásáés és módosítása című cikk utasításait.


### <a name="working-with-azure-powershell"></a>Az Azure PowerShell együttműködése

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="log-in-to-your-azure-account"></a>Jelentkezzen be az Azure-fiókjába.

Mielőtt hozzálát a művelethez, be kell jelentkeznie az Azure-fiókjába. A parancsmag kéri az Azure-fiók bejelentkezési hitelesítő adatait. A bejelentkezés után letölti a fiók beállításait, hogy elérhetők legyenek az Azure PowerShell számára.

Nyissa meg emelt szintű jogosultságokkal a PowerShell konzolt, és csatlakozzon a fiókjához. Az alábbi példával segítséget nyújt a csatlakozáshoz. Ha Az Azure Cloud Shell, nem kell futtatni ezt a parancsmacs, ahogy automatikusan be van jelentkezve.

```azurepowershell
Connect-AzAccount
```

Ha több Azure-előfizetéssel is rendelkezik, ellenőrizze a fiók előfizetéseit.

```azurepowershell-interactive
Get-AzSubscription
```

Válassza ki a használni kívánt előfizetést.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>1. lépés: Előtagok és BGP-közösségi értékek listájának beszereznie

### <a name="1-get-a-list-of-bgp-community-values"></a>1. A BGP közösségi értékek listájának beszereznie

Az alábbi parancsmag segítségével leszeretné késni a Microsoft társviszony-létesítésén keresztül elérhető szolgáltatásokhoz társított BGP-közösségi értékek listáját, valamint a hozzájuk társított előtagok listáját:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Készíts egy listát a használni kívánt értékekről

Készítse el az útvonalszűrőben használni kívánt BGP-közösségi értékek listáját.

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>2. lépés: Útvonalszűrő és szűrőszabály létrehozása

Az útvonalszűrőnek csak egy szabálya lehet, és a szabálynak "Engedélyezés" típusúnak kell lennie. Ez a szabály a BGP-közösség értékeit tartalmazó listát tartalmaz.

### <a name="1-create-a-route-filter"></a>1. Útvonalszűrő létrehozása

Először hozza létre az útvonalszűrőt. A "New-AzRouteFilter" parancs csak útvonalszűrő-erőforrást hoz létre. Az erőforrás létrehozása után létre kell hoznia egy szabályt, és csatolnia kell az útvonalszűrő objektumhoz. Útvonalszűrő-erőforrás létrehozásához futtassa a következő parancsot:

```azurepowershell-interactive
New-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
```

### <a name="2-create-a-filter-rule"></a>2. Szűrőszabály létrehozása

A BGP-közösségek készletét vesszővel tagolt listaként is megadhatja, ahogy az a példában is látható. Új szabály létrehozásához futtassa a következő parancsot:
 
```azurepowershell-interactive
$rule = New-AzRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList 12076:5010,12076:5040
```

### <a name="3-add-the-rule-to-the-route-filter"></a>3. A szabály hozzáadása az útvonalszűrőhöz

Futtassa a következő parancsot a szűrőszabály útvonalszűrőhöz való hozzáadásához:
 
```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.Rules.Add($rule)
Set-AzRouteFilter -RouteFilter $routefilter
```

## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>3. lépés: Az útvonalszűrő csatlakoztatása ExpressRoute-áramkörhöz

Futtassa a következő parancsot az útvonalszűrő ExpressRoute-kapcsolathoz való csatolásához, feltéve, hogy csak microsoftos társviszony-létesítési lehetőségvan:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="common-tasks"></a><a name="tasks"></a>Gyakori feladatok

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Útvonalszűrő tulajdonságainak bekésezése

Az útvonalszűrő tulajdonságainak leválasztásához kövesse az alábbi lépéseket:

1. Az útvonalszűrő-erőforrás levételéhez futtassa a következő parancsot:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   ```
2. Az útvonalszűrő-erőforrás útvonalszűrő-szabályainak beszereznie a következő parancs futtatásával:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   $rule = $routefilter.Rules[0]
   ```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Útvonalszűrő tulajdonságainak frissítése

Ha az útvonalszűrő már csatlakoztatva van egy kapcsolatcsoporthoz, a BGP-közösség listájának frissítései automatikusan propagálják a megfelelő előtag-hirdetésmódosításokat a létrehozott BGP-munkameneteken keresztül. Az útvonalszűrő BGP-közösséglistáját a következő paranccsal frissítheti:

```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzRouteFilter -RouteFilter $routefilter
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Útvonalszűrő leválasztása ExpressRoute-áramkörről

Miután egy útvonalszűrő levan választva az ExpressRoute-kapcsolatról, a BGP-munkameneten keresztül nem hirdetnek előtagokat. Az expressroute-áramkörökről a következő paranccsal választhat le útvonalszűrőt:
  
```azurepowershell-interactive
$ckt.Peerings[0].RouteFilter = $null
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Útvonalszűrő törlése

Csak akkor törölheti az útvonalszűrőt, ha az nincs csatolva egyetlen kapcsolathoz sem. A törlés megkísérlése előtt győződjön meg arról, hogy az útvonalszűrő nincs csatolva egyetlen kapcsolatonként sem. Az útvonalszűrőa következő paranccsal törölhető:

```azurepowershell-interactive
Remove-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>Következő lépések

Az ExpressRoute-ról további információt az [ExpressRoute gyakori kérdések című](expressroute-faqs.md)témakörben talál.
