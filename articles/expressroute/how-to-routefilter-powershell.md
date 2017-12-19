---
title: "Be annak az Azure ExpressRoute Microsoft társviszony-létesítés: PowerShell |} Microsoft Docs"
description: "Ez a cikk ismerteti a PowerShell használatával a Microsoft Peering útvonal szűrők konfigurálása"
documentationcenter: na
services: expressroute
author: ganesr
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: ganesr
ms.openlocfilehash: c940d2eab4d8e977b67b3553ab2e3d9110710956
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="configure-route-filters-for-microsoft-peering-powershell"></a>Be annak a Microsoft társviszony-létesítéshez: PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Útvonal-szűrőket, amelyek egy felhasználhatják a Microsoft társviszony-létesítés keresztül támogatott szolgáltatások egy részhalmaza. A cikkben ismertetett segítségével konfigurálhatja, és az ExpressRoute-Kapcsolatcsoportok útvonal szűrők kezeléséhez.

Dynamics 365 szolgáltatások, és a vállalati Exchange Online, SharePoint Online és Skype például Office 365-szolgáltatásokhoz és az Azure-szolgáltatásokkal például a tárolási és SQL-adatbázis a Microsoft társviszony-létesítés keresztül érhetők el. Ha Microsoft társviszony-létesítést az ExpressRoute-kapcsolatcsoportot van konfigurálva, hogy ezekre a szolgáltatásokra vonatkozó összes előtagot van-e hirdetve a BGP-munkamenetek létesített keresztül. BGP közösségi érték a szolgáltatás az előtag keresztül felajánlott azonosításához minden előtag van csatolva. A BGP logikai értékeket és a szolgáltatások leképezik a listája, [BGP Közösségek](expressroute-routing.md#bgp).

Minden szolgáltatásokhoz való kapcsolódás van szüksége, ha nagyszámú előtagok van-e hirdetve BGP keresztül. Ez jelentősen növeli a hálózaton belül útválasztók által fenntartott útvonaltáblák méretét. Ha azt tervezi, csak a Microsoft társviszony-létesítés kínált szolgáltatások egy részhalmaza felhasználását, csökkentheti az útvonaltáblák kétféleképpen méretét. A következőket teheti:

- Nem kívánt előtagok szűrheti a BGP Közösségek útvonal szűrők alkalmazásával. Ez egy szabványos hálózatkezelési eljárás, és sok hálózatok általában arra használják.

- Útvonal-szűrők és alkalmazni azokat az ExpressRoute-kapcsolatcsoportot. Útvonal szűrő egy új erőforrást, amely lehetővé teszi, hogy válassza ki azt tervezi, hogy a Microsoft társviszony-létesítés felhasználását szolgáltatások listájában. ExpressRoute útválasztók küldése csak a szolgáltatás az útvonal szűrő tartozó előtagok listáját.

### <a name="about"></a>Útvonal-szűrők

Amikor a Microsoft társviszony-létesítést az ExpressRoute-kapcsolatcsoportot van beállítva, a Microsoft edge útválasztók BGP-munkamenetek a peremhálózati útválasztó (saját vagy a kapcsolat szolgáltatóját) két hoz létre. Nincs útvonal a hálózathoz van-e hirdetve. Ahhoz, hogy az útvonal-hirdetéseinek a hálózathoz, társítania kell egy útvonal-szűrőt.

Útvonal szűrő lehetővé teszi, hogy azonosíthassa a keresztül az ExpressRoute-kapcsolatcsoportot Microsoft társviszony-létesítés használni kívánt szolgáltatásokat. Lényegében egy fehér lista összes BGP közösségi érték. Amikor egy útvonal szűrő erőforrás van definiálva, és ExpressRoute-kapcsolatcsoportot csatolva, a BGP-Közösség értékek hozzárendelése minden előtagok van-e hirdetve a hálózathoz.

Nem fogja tudni csatolni az Office 365 szolgáltatásaival rajtuk útvonal szűrők, Office 365 szolgáltatásait ExpressRoute keresztül engedélyezési kell rendelkeznie. Ha Ön nem jogosult ExpressRoute keresztül Office 365 szolgáltatásait, útvonal szűrők csatlakoztatni a művelet sikertelen lesz. Az engedélyezési folyamat kapcsolatos további információkért lásd: [Azure ExpressRoute az Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd). Dynamics 365-szolgáltatásokhoz való kapcsolódás nem igényel előzetes engedélyek.

> [!IMPORTANT]
> A Microsoft társviszony-létesítést az ExpressRoute-Kapcsolatcsoportok 2017. augusztus 1. előtt konfigurált meghirdetett Microsoft társviszony-létesítést, még akkor is, ha az útvonal-szűrők nem definiált összes szolgáltatás előtagok fog rendelkezni. A Microsoft társviszony-létesítést az ExpressRoute-Kapcsolatcsoportok vannak konfigurálva, vagy azt követően 2017. augusztus 1. nem rendelkezik a előtagokat amíg útvonal szűrő nem csatlakoztatja a kapcsolatcsoport hirdetve.
> 
> 

### <a name="workflow"></a>Munkafolyamat

Nem fogja tudni kapcsolódni a Microsoft társviszony-létesítés szolgáltatások, az alábbi konfigurációs lépéseket kell végrehajtania:

- Rendelkeznie kell egy aktív van a Microsoft társviszony kiosztott ExpressRoute-kapcsolatcsoportot. Az alábbi utasítások segítségével elvégezni ezeket a feladatokat:
  - [ExpressRoute-kapcsolatcsoportot létrehozni](expressroute-howto-circuit-arm.md) , és folytassa a kapcsolat szolgáltatójánál előtt által engedélyezett körön. Az ExpressRoute-kapcsolatcsoport kiépített és engedélyezett állapotban kell lennie.
  - [Hozzon létre a Microsoft társviszony-létesítés](expressroute-circuit-peerings.md) kezeléséhez közvetlenül a BGP-munkamenetet. Vagy a kapcsolat szolgáltatójánál rendelkezik a kör társviszony Microsoft kiépítéséhez.

-  Hozzon létre és útvonal-szűrő konfigurálnia kell.
    - A szolgáltatás azonosítására, a Microsoft társviszony-létesítés keresztül felhasználásához
    - A szolgáltatásokkal társított BGP közösségi értékek listájának azonosítása
    - A BGP-Közösség értékek megfelelő előtag engedélyezőlistában szabály létrehozása

-  Az ExpressRoute-kapcsolatcsoport hozzá kell rendelni az útvonal-szűrőt.

## <a name="before-you-begin"></a>Előkészületek

Konfigurációs megkezdése előtt győződjön meg arról, hogy a következő feltételeknek:

 - Telepítse az Azure Resource Manager PowerShell-parancsmagjainak legújabb verzióját. További információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-azurerm-ps).

  > [!NOTE]
  > A legújabb verzió letöltése a PowerShell-galériában, nem pedig a telepítő használatával. A telepítő jelenleg nem támogatja a szükséges parancsmagok.
  > 

 - Tekintse át a [Előfeltételek](expressroute-prerequisites.md) és [munkafolyamatok](expressroute-workflows.md) konfigurálás elkezdése előtt.

 - Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-arm.md) részben foglalt lépéseket, és engedélyeztesse a kapcsolatcsoportot kapcsolatszolgáltatójával, mielőtt továbblépne. Az ExpressRoute-kapcsolatcsoport kiépített és engedélyezett állapotban kell lennie.

 - Rendelkeznie kell egy aktív Microsoft társviszony-létesítés. Kövesse az utasításokat, [létrehozása, és társviszony-létesítési konfigurációjának módosítása](expressroute-circuit-peerings.md)

### <a name="log-in-to-your-azure-account"></a>Jelentkezzen be az Azure-fiókjával

Mielőtt hozzálát a művelethez, be kell jelentkeznie az Azure-fiókjába. A parancsmag kéri az Azure-fiók bejelentkezési hitelesítő adatait. A bejelentkezés után letölti a fiók beállításait, hogy elérhetők legyenek az Azure PowerShell számára.

Nyissa meg emelt szintű jogosultságokkal a PowerShell konzolt, és csatlakozzon a fiókjához. A következő példa segít a kapcsolódásban:

```powershell
Login-AzureRmAccount
```

Ha több Azure-előfizetéssel is rendelkezik, ellenőrizze a fiók előfizetéseit.

```powershell
Get-AzureRmSubscription
```

Válassza ki a használni kívánt előfizetést.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="prefixes"></a>1. lépés: Az előtagok és BGP közösségi értékek listájának beolvasása

### <a name="1-get-a-list-of-bgp-community-values"></a>1. BGP-Közösség értékek listájának beolvasása

A következő parancsmag használatával elérhető a Microsoft társviszony-létesítés szolgáltatásokkal társított BGP közösségi értékek listáját és a hozzájuk társított előtagok listáját:

```powershell
Get-AzureRmBgpServiceCommunity
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Ellenőrizze az értékeket, amelyeket használni kívánt listája

Ellenőrizze az útvonal szűrővel használni kívánt BGP közösségi értékek listáját. Tegyük fel a BGP közösségi Dynamics 365 szolgáltatások értéke 12076:5040.

## <a name="filter"></a>2. lépés: Útvonal szűrő, ezért a szűrési szabály létrehozása

Útvonal szűrő lehet csak egy szabályt, és a szabály a "Engedélyezés" típusúnak kell lennie. Ez a szabály társítva BGP közösségi értékből álló lista lehet.

### <a name="1-create-a-route-filter"></a>1. Útvonal szűrő létrehozása

Először hozza létre az útvonal-szűrőt. A parancs a "New-AzureRmRouteFilter" csak létrehoz egy útvonal-szűrő erőforrás. Az erőforrás létrehozása után kell majd hozzon létre egy szabályt és csatlakoztassa azt az útvonalat szűrő objektum. A következő paranccsal létrehozhat egy útvonalat szűrő:

```powershell
New-AzureRmRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
```

### <a name="2-create-a-filter-rule"></a>2. Állapotszűrő szabály létrehozása

Megadhat egy készletében BGP hajtsa végre egy vesszővel tagolt lista formájában, a példában látható módon. A következő paranccsal hozzon létre egy új szabályt:
 
```powershell
$rule = New-AzureRmRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList "12076:5010,12076:5040"
```

### <a name="3-add-the-rule-to-the-route-filter"></a>3. A szabály az útvonal-szűrő hozzáadása

Futtassa a következő parancs futtatásával adja hozzá az Állapotszűrő szabálynak a útvonal szűrő:
 
```powershell
$routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.Rules.Add($rule)
Set-AzureRmRouteFilter -RouteFilter $routefilter
```

## <a name="attach"></a>3. lépés: Az útvonal szűrő csatolása ExpressRoute-kapcsolatcsoportot

A következő parancsot a útvonal szűrő csatlakoztatni az ExpressRoute-kapcsolatcsoport, feltéve, hogy csak a Microsoft társviszony-létesítés rendelkezik:

```powershell
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="tasks"></a>Gyakori feladatok

### <a name="getproperties"></a>Az útvonal szűrő tulajdonságainak beolvasása

Ahhoz, hogy egy útvonal szűrő tulajdonságait, tegye a következőket:

1. A következő parancsot az útvonal szűrő erőforrás:

  ```powershell
  $routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
  ```
2. Érhető el az útvonal Állapotszűrő szabályok az útvonal-szűrő erőforrás a következő parancs futtatásával:

  ```powershell
  $routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
  $rule = $routefilter.Rules[0]
  ```

### <a name="updateproperties"></a>Útvonal-szűrő tulajdonságainak módosítása

Az útvonal szűrő expressroute-kapcsolatcsoporthoz már csatolva van, ha a frissítések a BGP-Közösség listához automatikusan propagálása megfelelő előtag hirdetmény módosítások keresztül létesített a BGP-munkamenetek. Frissítheti a BGP közösségi listáját az útvonal-szűrő a következő parancsot:

```powershell
$routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzureRmRouteFilter -RouteFilter $routefilter
```

### <a name="detach"></a>Az ExpressRoute-kapcsolatcsoportot útvonal szűrő leválasztása

Miután egy útvonal szűrő az ExpressRoute-kapcsolatcsoport le van választva, nincs előtagokat hirdet a BGP-kapcsolaton keresztül. Útvonal szűrőt a következő paranccsal ExpressRoute-kapcsolatcsoportot az választhatják le:
  
```powershell
$ckt.Peerings[0].RouteFilter = $null
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="delete"></a>Útvonal szűrő törlése

Csak törölheti útvonal szűrőt, ha bármely expressroute nincs csatlakoztatva. Győződjön meg arról, hogy az útvonal-szűrő nem kapcsolódik a kapcsolatcsoport azt a törlése megkísérlése előtt. A következő paranccsal útvonal szűrő törlése:

```powershell
Remove-AzureRmRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>Következő lépések

További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).
