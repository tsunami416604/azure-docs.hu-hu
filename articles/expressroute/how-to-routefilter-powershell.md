---
title: 'ExpressRoute: útvonal-szűrők – Microsoft-társak: Azure PowerShell'
description: Ez a cikk bemutatja, hogyan konfigurálhat útválasztási szűrőket a Microsoft-partnerek számára a PowerShell használatával
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: f5a294a051350c4b08b34356abcd883b7580164e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84729302"
---
# <a name="configure-route-filters-for-microsoft-peering-powershell"></a>Útválasztási szűrők konfigurálása a Microsoft-partnerek számára: PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Az útvonalszűrők lehetővé teszik a támogatott szolgáltatások egy részének felhasználását Microsoft-társviszony-létesítésen keresztül. Az ebben a cikkben ismertetett lépések segítséget nyújtanak a ExpressRoute-áramkörök útválasztási szűrőinek konfigurálásához és kezeléséhez.

Az Office 365-szolgáltatások, például az Exchange Online, a SharePoint Online és a Skype vállalati verzió, valamint az Azure nyilvános szolgáltatásai, például a Storage és az SQL Database, a Microsoft-partneri kapcsolaton keresztül érhetők el. Az Azure-beli nyilvános szolgáltatások régiónként választhatók, és nem definiálhatók nyilvános szolgáltatáson belül.

Ha a Microsoft-társítás egy ExpressRoute-áramkörön van konfigurálva, és egy útvonal-szűrő van csatlakoztatva, a szolgáltatásokhoz kiválasztott összes előtag a létrehozott BGP-munkameneteken keresztül kerül meghirdetésre. Minden előtaghoz egy BGP-közösségérték van csatolva, amely azonosítja az előtag keretében nyújtott szolgáltatást. A BGP közösségi értékek és a hozzájuk kapcsolódó szolgáltatások listáját itt tekintheti meg: [BGP-Közösségek](expressroute-routing.md#bgp).

Ha minden szolgáltatáshoz csatlakoznia kell, nagy számú előtagokat a BGP-n keresztül hirdetünk meg. Ez jelentősen növeli a hálózaton belüli útválasztók által karbantartott útválasztási táblázatok méretét. Ha azt tervezi, hogy csak a Microsoft-partnerek által kínált szolgáltatások egy részhalmazát szeretné használni, az útválasztási táblázatok méretét kétféleképpen csökkentheti. A következőket teheti:

- Kiszűri a nemkívánatos előtagokat az útválasztási szűrők alkalmazásával a BGP-közösségeken. Ez egy szabványos hálózatkezelési gyakorlat, és általában számos hálózaton belül használatos.

- Adja meg az útvonal-szűrőket, és alkalmazza őket a ExpressRoute-áramkörre. Az útválasztási szűrő egy új erőforrás, amellyel kiválaszthatja a Microsoft-társon keresztül használni kívánt szolgáltatások listáját. A ExpressRoute-útválasztók csak az útválasztási szűrőben azonosított szolgáltatásokhoz tartozó előtagok listáját küldik el.

### <a name="about-route-filters"></a><a name="about"></a>Az útválasztási szűrők ismertetése

Ha a Microsoft-társak konfigurálva vannak a ExpressRoute-áramkörön, a Microsoft hálózati peremhálózati útválasztók egy pár BGP-munkamenetet létesítenek a peremhálózati útválasztókkal (a tiéd vagy a kapcsolat szolgáltatója). Nincsenek útvonalak meghirdetve a hálózatán. Ha engedélyezni szeretné az útvonalhirdetéseket a hálózaton, társítania kell egy útvonalszűrőt.

Az útvonalszűrőkkel azonosíthatja az ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítésén keresztül használni kívánt szolgáltatásokat. Ez lényegében egy engedélyezési lista az összes BGP közösségi értékről. Miután meghatározott és egy ExpressRoute-kapcsolatcsoporthoz csatolt egy útvonalszűrő erőforrást, a BGP-közösségértékekhez rendelt összes előtag meg van hirdetve a hálózaton.

Ahhoz, hogy az Office 365-szolgáltatásokkal csatolja az útválasztási szűrőket, rendelkeznie kell az Office 365-szolgáltatások ExpressRoute-en keresztüli használatára vonatkozó engedéllyel. Ha nincs engedélye arra, hogy az Office 365-szolgáltatásokat ExpressRoute-en keresztül használja, az útválasztási szűrők csatlakoztatásának művelete meghiúsul. Az engedélyezési folyamattal kapcsolatos további információkért lásd: [Azure ExpressRoute for Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> Az 2017. augusztus 1. előtt konfigurált ExpressRoute-áramkörök Microsoft-összevonása a Microsoft-társon keresztül meghirdetett összes szolgáltatás előtagja lesz, még akkor is, ha az útvonal-szűrők nincsenek meghatározva. A 2017 augusztus 1-jén vagy azt követően konfigurált ExpressRoute-áramkörök Microsoft-társítása nem rendelkezik olyan előtagokkal, amelyek csak akkor lesznek meghirdetve, ha az áramkörhöz hozzá van rendelve egy útvonal-szűrő.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Munkafolyamat

Ahhoz, hogy sikeresen tudjon csatlakozni a szolgáltatásokhoz a Microsoft-társon keresztül, el kell végeznie az alábbi konfigurációs lépéseket:

- Rendelkeznie kell egy aktív ExpressRoute-áramkörrel, amelyhez Microsoft-társítás van kiépítve. A következő utasításokat követve hajthatja végre ezeket a feladatokat:
  - A folytatás előtt [hozzon létre egy ExpressRoute áramkört](expressroute-howto-circuit-arm.md) , és engedélyezze az áramkört a kapcsolat szolgáltatója számára. A ExpressRoute áramkörnek kiépített és engedélyezett állapotban kell lennie.
  - [Hozzon létre Microsoft-társat](expressroute-circuit-peerings.md) , ha közvetlenül a BGP-munkamenetet kezeli. Vagy a kapcsolat szolgáltatója kiépíti a Microsoft-társat az áramkörhöz.

-  Létre kell hoznia és konfigurálnia kell egy útvonal-szűrőt.
    - Azonosítsa a Microsoft-társon keresztül használni kívánt szolgáltatásokat
    - A szolgáltatásokhoz társított BGP-közösségi értékek listájának azonosítása
    - Szabály létrehozása a BGP-közösségi értékeknek megfelelő előtag-lista engedélyezéséhez

-  Az ExpressRoute áramkörhöz csatolni kell az útvonal-szűrőt.

## <a name="before-you-begin"></a>Előkészületek

A konfigurálás megkezdése előtt győződjön meg arról, hogy megfelel a következő feltételeknek:

 - A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](expressroute-prerequisites.md) és a [munkafolyamatokat](expressroute-workflows.md) .

 - Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-arm.md) részben foglalt lépéseket, és engedélyeztesse a kapcsolatcsoportot kapcsolatszolgáltatójával, mielőtt továbblépne. A ExpressRoute áramkörnek kiépített és engedélyezett állapotban kell lennie.

 - Aktív Microsoft-társat kell biztosítania. Kövesse a társítási [konfiguráció létrehozása és módosítása](expressroute-circuit-peerings.md) című cikk utasításait.


### <a name="working-with-azure-powershell"></a>A Azure PowerShell használata

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="log-in-to-your-azure-account"></a>Jelentkezzen be az Azure-fiókjába.

Mielőtt hozzálát a művelethez, be kell jelentkeznie az Azure-fiókjába. A parancsmag kéri az Azure-fiók bejelentkezési hitelesítő adatait. A bejelentkezés után letölti a fiók beállításait, hogy elérhetők legyenek az Azure PowerShell számára.

Nyissa meg emelt szintű jogosultságokkal a PowerShell konzolt, és csatlakozzon a fiókjához. Az alábbi példa segítséget nyújt a kapcsolódáshoz. Ha Azure Cloud Shell használ, nem kell futtatnia ezt a parancsmagot, mivel a rendszer automatikusan bejelentkezik.

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

## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>1. lépés: az előtagok és a BGP közösségi értékek listájának lekérése

### <a name="1-get-a-list-of-bgp-community-values"></a>1. a BGP-közösségi értékek listájának beolvasása

A következő parancsmaggal kérheti le a Microsoft-társon keresztül elérhető szolgáltatásokhoz kapcsolódó BGP-közösségi értékek listáját, valamint a hozzájuk rendelt előtagok listáját:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. a használni kívánt értékek listájának létrehozása

Készítse el az útválasztási szűrőben használni kívánt BGP-közösségi értékek listáját.

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>2. lépés: útvonal-szűrő és szűrő-szabály létrehozása

Egy útvonal-szűrőnek csak egy szabálya lehet, és a szabálynak "Allow" típusúnak kell lennie. Ez a szabály tartalmazhatja a BGP-közösségi értékek listáját.

### <a name="1-create-a-route-filter"></a>1. útvonal-szűrő létrehozása

Először hozza létre az útvonal-szűrőt. A "New-AzRouteFilter" parancs csak útvonal-szűrő erőforrást hoz létre. Az erőforrás létrehozása után létre kell hoznia egy szabályt, és csatolni kell az útválasztási szűrő objektumhoz. Futtassa az alábbi parancsot egy útvonal-szűrő erőforrás létrehozásához:

```azurepowershell-interactive
New-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
```

### <a name="2-create-a-filter-rule"></a>2. szűrési szabály létrehozása

A BGP-Közösségek készletét vesszővel tagolt listaként is megadhatja, ahogy az a példában látható. Új szabály létrehozásához futtassa a következő parancsot:
 
```azurepowershell-interactive
$rule = New-AzRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList 12076:5010,12076:5040
```

### <a name="3-add-the-rule-to-the-route-filter"></a>3. adja hozzá a szabályt az útvonal-szűrőhöz

Futtassa a következő parancsot a szűrési szabály az útvonal-szűrőhöz való hozzáadásához:
 
```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.Rules.Add($rule)
Set-AzRouteFilter -RouteFilter $routefilter
```

## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>3. lépés: az útvonal-szűrő csatolása egy ExpressRoute-áramkörhöz

Futtassa a következő parancsot az útvonal-szűrő ExpressRoute-áramkörhöz való csatolásához, feltéve, hogy csak a Microsoft-partneri kapcsolattal rendelkezik:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="common-tasks"></a><a name="tasks"></a>Gyakori feladatok

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Egy útvonal szűrő tulajdonságainak beolvasása

Az útválasztási szűrők tulajdonságainak beszerzéséhez kövesse az alábbi lépéseket:

1. Futtassa a következő parancsot az útvonal-szűrő erőforrás lekéréséhez:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   ```
2. A következő parancs futtatásával szerezze be az útvonal-szűrő erőforrás útválasztási szabályait:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   $rule = $routefilter.Rules[0]
   ```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Egy útvonal szűrő tulajdonságainak frissítése

Ha az útválasztási szűrő már csatolva van egy áramkörhöz, a BGP-közösségi lista frissítései automatikusan propagálják a megfelelő előtag-hirdetményeket a létrejött BGP-munkameneteken keresztül. A következő paranccsal frissítheti az útválasztási szűrő BGP közösségi listáját:

```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzRouteFilter -RouteFilter $routefilter
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Útvonal-szűrő leválasztása egy ExpressRoute-áramkörből

Ha egy útvonal-szűrőt leválasztanak a ExpressRoute áramkörről, a BGP-munkameneten belül nem történik meg az előtagok meghirdetése. A következő parancs használatával leválaszthat egy ExpressRoute áramkörből:
  
```azurepowershell-interactive
$ckt.Peerings[0].RouteFilter = $null
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Útvonal-szűrő törlése

Csak akkor törölhet egy útvonal-szűrőt, ha az nincs csatlakoztatva egyetlen áramkörhöz sem. A törlés megkísérlése előtt gondoskodjon arról, hogy az útválasztási szűrő ne legyen csatlakoztatva egyetlen áramkörhöz sem. Az útvonal-szűrőt a következő paranccsal törölheti:

```azurepowershell-interactive
Remove-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>Következő lépések

A ExpressRoute kapcsolatos további információkért tekintse meg a [ExpressRoute gyakori kérdések](expressroute-faqs.md)című témakört.
