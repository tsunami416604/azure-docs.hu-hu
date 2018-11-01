---
title: 'Az Azure ExpressRoute Microsoft társviszony-létesítés útvonalszűrőinek konfigurálása: PowerShell |} A Microsoft Docs'
description: Ez a cikk ismerteti a PowerShell használatával a Microsoft Peering útvonalszűrők konfigurálása
documentationcenter: na
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 10/30/2018
ms.author: ganesr
ms.openlocfilehash: d4ef500185675ab84485c5dd6a9af4034c57b060
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50419265"
---
# <a name="configure-route-filters-for-microsoft-peering-powershell"></a>Microsoft társviszony-létesítés útvonalszűrőinek konfigurálása: PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Az útvonalszűrők lehetővé teszik a támogatott szolgáltatások egy részének felhasználását Microsoft-társviszony-létesítésen keresztül. A jelen cikkben ismertetett lépések segítségével konfigurálhatja és kezelheti az ExpressRoute-Kapcsolatcsoportok útvonalszűrőinek.

Dynamics 365-szolgáltatások és az Office 365-szolgáltatások például az Exchange Online, SharePoint Online és Skype vállalati és a nyilvános Azure-szolgáltatások, például a storage és SQL DB a Microsoft társviszony-létesítésen keresztül érhetők el. Nyilvános Azure-szolgáltatások régió per alapon választható és nyilvános szolgáltatásonként nem definiálható. 

Az ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítésre van konfigurálva, és a egy útvonalszűrőhöz van csatolva, amikor ezen szolgáltatások kiválasztott összes előtag keresztül létesített BGP-munkamenetek hirdesse meg. Minden előtaghoz egy BGP-közösségérték van csatolva, amely azonosítja az előtag keretében nyújtott szolgáltatást. A BGP-Közösség értékét, és a szolgáltatások leképezik a listáját lásd: [BGP-Közösségek](expressroute-routing.md#bgp).

Minden szolgáltatásokhoz való kapcsolódás van szükség, ha az előtagok sok BGP-n keresztüli hirdesse meg. Ez jelentősen növeli az útválasztási táblázatokat a hálózaton belüli útválasztók által fenntartott méretét. Ha azt tervezi, a Microsoft társviszony-létesítés keresztül felajánlott szolgáltatásokhoz csak egy részhalmazát felhasználását, csökkentheti az útválasztási táblázatban kétféleképpen méretét. A következőket teheti:

- A BGP-Közösségek útvonalszűrők alkalmazásával szűrése nemkívánatos előtagokat ki. Ez egy szabványos hálózatkezelési eljárás és belül túl sok hálózathoz gyakran használják.

- Útvonal szűrőket határozhat meg, és az ExpressRoute-kapcsolatcsoport alkalmazza őket. Egy útvonalszűrőhöz egy új erőforrást, amely lehetővé teszi szolgáltatások tervez használni a Microsoft társviszony-létesítésen keresztül listájában válassza ki. Csak az ExpressRoute útválasztók továbbítják az útvonalszűrőt szerepelt a szolgáltatásokhoz tartozó előtaglistát.

### <a name="about"></a>Útvonalszűrők kapcsolatban

Ha az ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítés van konfigurálva, a Microsoft peremhálózati útválasztói létesíteni két BGP-munkamenetet a peremhálózati útválasztóhoz (Öné vagy a kapcsolatszolgáltató). Nincsenek útvonalak meghirdetve a hálózatán. Ha engedélyezni szeretné az útvonalhirdetéseket a hálózaton, társítania kell egy útvonalszűrőt.

Az útvonalszűrőkkel azonosíthatja az ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítésén keresztül használni kívánt szolgáltatásokat. Ez tulajdonképpen az összes BGP-közösségérték engedélyezési listája. Miután meghatározott és egy ExpressRoute-kapcsolatcsoporthoz csatolt egy útvonalszűrő erőforrást, a BGP-közösségértékekhez rendelt összes előtag meg van hirdetve a hálózaton.

Rendeljen hozzá útvonalszűrőket az Office 365-szolgáltatások rajtuk legyen, engedélyezési felhasználásához az Office 365 szolgáltatás expressroute-on keresztül kell rendelkeznie. Ha nem jogosult az Office 365 szolgáltatás expressroute-on keresztül felhasználásához, rendeljen hozzá útvonalszűrőket a művelet sikertelen lesz. Az engedélyezési folyamat kapcsolatos további információkért lásd: [Office 365-höz készült Azure ExpressRoute](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd). Dynamics 365-szolgáltatásokhoz való kapcsolódás nem igényel előzetes engedélyek.

> [!IMPORTANT]
> Microsoft társviszony-létesítés voltak beállítva a 2017. augusztus 1. ExpressRoute-Kapcsolatcsoportok az összes szolgáltatás előtagkészletet hirdeti meg a Microsoft társviszony-létesítéshez, akkor is, ha nincsenek meghatározva útvonalszűrők fog rendelkezni. Microsoft társviszony-létesítésre vannak konfigurálva, vagy 2017. augusztus 1. után az ExpressRoute-Kapcsolatcsoportok, nem rendelkezik minden olyan előtagok mindaddig, amíg egy útvonalszűrőhöz csatolva van a kapcsolatcsoport hirdetve.
> 
> 

### <a name="workflow"></a>A munkafolyamat

Az, hogy sikeresen csatlakozni a Microsoft társviszony-létesítés keresztül, az alábbi konfigurációs lépéseket kell elvégeznie:

- Aktív ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítést kiépített rendelkező kell rendelkeznie. Az alábbi utasítások segítségével a fenti feladatok elvégzéséhez:
  - [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-arm.md) , és engedélyeztesse a kapcsolatcsoportot kapcsolatszolgáltatójával, mielőtt a folytatáshoz. Az ExpressRoute-kapcsolatcsoport kiosztott és engedélyezett állapotban kell lennie.
  - [A Microsoft társviszony-létesítés](expressroute-circuit-peerings.md) kezeléséhez közvetlenül a BGP-munkamenetben. Vagy a kapcsolatszolgáltató kell kiépíteni a Microsoft társviszony-létesítést a kapcsolatcsoporthoz.

-  Hozzon létre és egy útvonalszűrőhöz konfigurálnia kell.
    - A szolgáltatás azonosítására, és felhasználásához a Microsoft társviszony-létesítésen keresztül
    - Azonosítsa a BGP-Közösség értékét vett szolgáltatások listája
    - Hozzon létre egy szabályt, hogy az előtagok listáját a BGP-Közösség értékét megfelelő

-  Az ExpressRoute-kapcsolatcsoport az útvonalszűrőt kell csatolnia.

## <a name="before-you-begin"></a>Előkészületek

A konfigurálás elkezdése előtt ellenőrizze a következő feltételeknek:

 - Tekintse át a [Előfeltételek](expressroute-prerequisites.md) és [munkafolyamatok](expressroute-workflows.md) konfigurálás megkezdése előtt.

 - Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-arm.md) részben foglalt lépéseket, és engedélyeztesse a kapcsolatcsoportot kapcsolatszolgáltatójával, mielőtt továbblépne. Az ExpressRoute-kapcsolatcsoport kiosztott és engedélyezett állapotban kell lennie.

 - Rendelkeznie kell egy aktív Microsoft társviszony-létesítés. Kövesse az utasításokat a [létrehozása és módosítása a társviszony-létesítési konfigurációjának](expressroute-circuit-peerings.md) cikk.


### <a name="working-with-azure-powershell"></a>Az Azure PowerShell használata
[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="log-in-to-your-azure-account"></a>Jelentkezzen be az Azure-fiókjába.

Mielőtt hozzálát a művelethez, be kell jelentkeznie az Azure-fiókjába. A parancsmag kéri az Azure-fiók bejelentkezési hitelesítő adatait. A bejelentkezés után letölti a fiók beállításait, hogy elérhetők legyenek az Azure PowerShell számára.

Nyissa meg emelt szintű jogosultságokkal a PowerShell konzolt, és csatlakozzon a fiókjához. Az alábbi példa használatával segít a kapcsolódásban. Azure Cloud Shellt használja, ha nincs szüksége a parancsmag futtatásához, automatikusan belépteti.

```azurepowershell
Connect-AzureRmAccount
```

Ha több Azure-előfizetéssel is rendelkezik, ellenőrizze a fiók előfizetéseit.

```azurepowershell-interactive
Get-AzureRmSubscription
```

Válassza ki a használni kívánt előfizetést.

```azurepowershell-interactive
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="prefixes"></a>1. lépés: Az előtagok és BGP-Közösség értékét listájának lekérése

### <a name="1-get-a-list-of-bgp-community-values"></a>1. A BGP-Közösség értékét tartalmazó lista beolvasása

A BGP-Közösség értékét társított szolgáltatások, a Microsoft társviszony-létesítésekhez listáját, és a hozzájuk társított előtaglistát beolvasásához használja a következő parancsmagot:

```azurepowershell-interactive
Get-AzureRmBgpServiceCommunity
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Győződjön meg a használni kívánt értékek listáját

Ellenőrizze a BGP-Közösség értékét az útvonalszűrőt használni kívánt listáját. Tegyük fel a Dynamics 365-szolgáltatásokhoz a BGP-közösségérték 12076:5040.

## <a name="filter"></a>2. lépés: Egy útvonalszűrőhöz és a egy Állapotszűrő szabály létrehozása

Egy útvonalszűrőhöz lehet csak egy szabályt, és a szabály "Engedélyezés" típusúnak kell lennie. Ez a szabály is van egy listája azokról a BGP-Közösség értékét társítva.

### <a name="1-create-a-route-filter"></a>1. Hozzon létre egy útvonalszűrőhöz

Először hozza létre az útvonalszűrőt. A parancs a "New-AzureRmRouteFilter" csak létrehoz egy útvonal-szűrő erőforrás. Az erőforrás létrehozása után kell majd hozzon létre egy szabályt és csatlakoztassa azt az útvonalat szűrő objektum. Futtassa a következő parancsot egy útvonal-szűrő erőforrás létrehozásához:

```azurepowershell-interactive
New-AzureRmRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
```

### <a name="2-create-a-filter-rule"></a>2. Szűrési szabály létrehozása

Megadhatja egy BGP-Közösségek készlete egy vesszővel tagolt lista formájában, a példában látható módon. Futtassa a következő parancsot egy új szabály létrehozása:
 
```azurepowershell-interactive
$rule = New-AzureRmRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList "12076:5010,12076:5040"
```

### <a name="3-add-the-rule-to-the-route-filter"></a>3. Adja hozzá a szabályt az útvonalszűrőt

Futtassa a következő parancsot a szűrési szabály hozzáadása az útvonalszűrőt:
 
```azurepowershell-interactive
$routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.Rules.Add($rule)
Set-AzureRmRouteFilter -RouteFilter $routefilter
```

## <a name="attach"></a>3. lépés: Az útvonalszűrőt csatlakoztatása egy ExpressRoute-kapcsolatcsoporttal

Futtassa a következő parancsot az útvonalszűrőt csatolása az ExpressRoute-kapcsolatcsoporthoz, feltéve, hogy csak a Microsoft társviszony-létesítés rendelkezik:

```azurepowershell-interactive
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="tasks"></a>Gyakori feladatok

### <a name="getproperties"></a>Hogy egy útvonalszűrőhöz tulajdonságainak beolvasása

Egy útvonalszűrőhöz tulajdonságainak lekéréséhez használja az alábbi lépéseket:

1. A következő parancsot az útvonal szűrő erőforrás lekérése:

  ```azurepowershell-interactive
  $routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
  ```
2. Az útvonal Állapotszűrő-szabályok a route-filter erőforrás beolvasása a következő parancs futtatásával:

  ```azurepowershell-interactive
  $routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
  $rule = $routefilter.Rules[0]
  ```

### <a name="updateproperties"></a>Egy útvonalszűrőhöz tulajdonságainak frissítése

Az útvonalszűrőt már csatolva van egy kapcsolatcsoporthoz, ha a BGP-Közösség listához frissítések automatikusan propagálása a létesített BGP-munkamenetek a megfelelő előtaggal hirdetmény változásoknak. A BGP közösségi listája az útvonalszűrőt, a következő paranccsal frissítheti:

```azurepowershell-interactive
$routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzureRmRouteFilter -RouteFilter $routefilter
```

### <a name="detach"></a>Egy útvonalszűrőhöz az ExpressRoute-kapcsolatcsoport leválasztása

Miután egy útvonalszűrőhöz az ExpressRoute-kapcsolatcsoport le van választva, nincs előtagokat hirdet meg a BGP-munkameneten keresztül. Az ExpressRoute-kapcsolatcsoport a következő parancsot egy útvonalszűrőhöz leválaszthatja:
  
```azurepowershell-interactive
$ckt.Peerings[0].RouteFilter = $null
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="delete"></a>Egy útvonalszűrőhöz törlése

Egy útvonalszűrőhöz csak törölheti, ha bármely kapcsolatcsoporthoz nincs csatolva. Győződjön meg arról, hogy az útvonalszűrőt nincs csatolva bármely kapcsolatcsoport azt törlése megkísérlése előtt. Egy útvonalszűrőhöz, a következő paranccsal törölheti:

```azurepowershell-interactive
Remove-AzureRmRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>További lépések

További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).
