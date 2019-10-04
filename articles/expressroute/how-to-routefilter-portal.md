---
title: 'Útválasztási szűrők konfigurálása a Microsoft-partnerek számára: Azure ExpressRoute – portál | Microsoft Docs'
description: Ez a cikk ismerteti a Microsoft társviszony-létesítés az Azure portal használatával útvonalszűrőinek konfigurálása.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 07/01/2019
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: c49b1fa1e2e8421146f5d5012de983c14934c23c
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122940"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>Útválasztási szűrők konfigurálása a Microsoft-partnerek számára: Azure Portal
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Az útvonalszűrők lehetővé teszik a támogatott szolgáltatások egy részének felhasználását Microsoft-társviszony-létesítésen keresztül. A jelen cikkben ismertetett lépések segítségével konfigurálhatja és kezelheti az ExpressRoute-Kapcsolatcsoportok útvonalszűrőinek.

Az Office 365-szolgáltatások, például az Exchange Online, a SharePoint Online és a Skype vállalati verzió, valamint az Azure-szolgáltatások, például a Storage és az SQL Database, a Microsoft-partneri kapcsolaton keresztül érhetők el. Az ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítés konfigurálásakor a ezekhez a szolgáltatásokhoz kapcsolódó összes előtagokat hirdet meg a BGP-munkamenetek létesített keresztül. Minden előtaghoz egy BGP-közösségérték van csatolva, amely azonosítja az előtag keretében nyújtott szolgáltatást. A BGP-Közösség értékét, és a szolgáltatások leképezik a listáját lásd: [BGP-Közösségek](expressroute-routing.md#bgp).

Minden szolgáltatásokhoz való kapcsolódás van szükség, ha az előtagok sok BGP-n keresztüli hirdesse meg. Ez jelentősen növeli az útválasztási táblázatokat a hálózaton belüli útválasztók által fenntartott méretét. Ha azt tervezi, a Microsoft társviszony-létesítés keresztül felajánlott szolgáltatásokhoz csak egy részhalmazát felhasználását, csökkentheti az útválasztási táblázatban kétféleképpen méretét. A következőket teheti:

- A BGP-Közösségek útvonalszűrők alkalmazásával szűrése nemkívánatos előtagokat ki. Ez egy szabványos hálózatkezelési eljárás és belül túl sok hálózathoz gyakran használják.

- Útvonal szűrőket határozhat meg, és az ExpressRoute-kapcsolatcsoport alkalmazza őket. Egy útvonalszűrőhöz egy új erőforrást, amely lehetővé teszi szolgáltatások tervez használni a Microsoft társviszony-létesítésen keresztül listájában válassza ki. Csak az ExpressRoute útválasztók továbbítják az útvonalszűrőt szerepelt a szolgáltatásokhoz tartozó előtaglistát.

### <a name="about"></a>Útvonalszűrők kapcsolatban

Ha az ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítés van konfigurálva, a Microsoft peremhálózati útválasztói létesíteni két BGP-munkamenetet a peremhálózati útválasztóhoz (Öné vagy a kapcsolatszolgáltató). Nincsenek útvonalak meghirdetve a hálózatán. Ha engedélyezni szeretné az útvonalhirdetéseket a hálózaton, társítania kell egy útvonalszűrőt.

Az útvonalszűrőkkel azonosíthatja az ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítésén keresztül használni kívánt szolgáltatásokat. Lényegében a BGP-Közösség összes olyan értékének listája, amelyet engedélyezni szeretne. Miután meghatározott és egy ExpressRoute-kapcsolatcsoporthoz csatolt egy útvonalszűrő erőforrást, a BGP-közösségértékekhez rendelt összes előtag meg van hirdetve a hálózaton.

Rendeljen hozzá útvonalszűrőket az Office 365-szolgáltatások rajtuk legyen, engedélyezési felhasználásához az Office 365 szolgáltatás expressroute-on keresztül kell rendelkeznie. Ha nem jogosult az Office 365 szolgáltatás expressroute-on keresztül felhasználásához, rendeljen hozzá útvonalszűrőket a művelet sikertelen lesz. Az engedélyezési folyamat kapcsolatos további információkért lásd: [Office 365-höz készült Azure ExpressRoute](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> Microsoft társviszony-létesítés voltak beállítva a 2017. augusztus 1. ExpressRoute-Kapcsolatcsoportok az összes szolgáltatás előtagkészletet hirdeti meg a Microsoft társviszony-létesítéshez, akkor is, ha nincsenek meghatározva útvonalszűrők fog rendelkezni. Microsoft társviszony-létesítésre vannak konfigurálva, vagy 2017. augusztus 1. után az ExpressRoute-Kapcsolatcsoportok, nem rendelkezik minden olyan előtagok mindaddig, amíg egy útvonalszűrőhöz csatolva van a kapcsolatcsoport hirdetve.
> 
> 

### <a name="workflow"></a>A munkafolyamat

Az, hogy sikeresen csatlakozni a Microsoft társviszony-létesítés keresztül, az alábbi konfigurációs lépéseket kell elvégeznie:

- Aktív ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítést kiépített rendelkező kell rendelkeznie. Az alábbi utasítások segítségével a fenti feladatok elvégzéséhez:
  - [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-portal-resource-manager.md) , és engedélyeztesse a kapcsolatcsoportot kapcsolatszolgáltatójával, mielőtt a folytatáshoz. Az ExpressRoute-kapcsolatcsoport kiosztott és engedélyezett állapotban kell lennie.
  - [A Microsoft társviszony-létesítés](expressroute-howto-routing-portal-resource-manager.md) kezeléséhez közvetlenül a BGP-munkamenetben. Vagy a kapcsolatszolgáltató kell kiépíteni a Microsoft társviszony-létesítést a kapcsolatcsoporthoz.

-  Hozzon létre és egy útvonalszűrőhöz konfigurálnia kell.
    - A szolgáltatás azonosítására, és felhasználásához a Microsoft társviszony-létesítésen keresztül
    - Azonosítsa a BGP-Közösség értékét vett szolgáltatások listája
    - Hozzon létre egy szabályt, hogy az előtagok listáját a BGP-Közösség értékét megfelelő

-  Az ExpressRoute-kapcsolatcsoport az útvonalszűrőt kell csatolnia.

## <a name="before-you-begin"></a>Előkészületek

A konfigurálás elkezdése előtt ellenőrizze a következő feltételeknek:

 - Tekintse át a [Előfeltételek](expressroute-prerequisites.md) és [munkafolyamatok](expressroute-workflows.md) konfigurálás megkezdése előtt.

 - Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-portal-resource-manager.md) részben foglalt lépéseket, és engedélyeztesse a kapcsolatcsoportot kapcsolatszolgáltatójával, mielőtt továbblépne. Az ExpressRoute-kapcsolatcsoport kiosztott és engedélyezett állapotban kell lennie.

 - Rendelkeznie kell egy aktív Microsoft társviszony-létesítés. Kövesse az utasításokat, [létrehozása és a társviszony-létesítési konfigurációjának módosítása](expressroute-howto-routing-portal-resource-manager.md)


## <a name="prefixes"></a>1. lépés: Az előtagok és a BGP közösségi értékek listájának beolvasása

### <a name="1-get-a-list-of-bgp-community-values"></a>1. A BGP-Közösség értékét tartalmazó lista beolvasása

A Microsoft társviszony-létesítésen keresztül elérhető szolgáltatások társított BGP-Közösség értékét érhető el a [az ExpressRoute útválasztási követelményei](expressroute-routing.md) lapot.

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Győződjön meg a használni kívánt értékek listáját

Készítse el az útválasztási szűrőben használni kívánt [BGP-közösségi értékek](expressroute-routing.md#bgp) listáját. 

## <a name="filter"></a>2. lépés: Útvonal-szűrő és egy szűrési szabály létrehozása

Egy útvonalszűrőhöz lehet csak egy szabályt, és a szabály "Engedélyezés" típusúnak kell lennie. Ez a szabály is van egy listája azokról a BGP-Közösség értékét társítva.

### <a name="1-create-a-route-filter"></a>1. Hozzon létre egy útvonalszűrőhöz
Létrehozhat egy útvonalszűrőhöz hozzon létre egy új erőforrást a beállítás kiválasztásával. Kattintson a **erőforrás létrehozása** > **hálózatkezelés** > **útvonalszűrőt**, ahogy az alábbi képen látható:

![Hozzon létre egy útvonalszűrőhöz](./media/how-to-routefilter-portal/CreateRouteFilter1.png)

Az útvonalszűrőt egy erőforráscsoportban kell elhelyeznie. 

![Hozzon létre egy útvonalszűrőhöz](./media/how-to-routefilter-portal/CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2. Szűrési szabály létrehozása

Hozzáadhat és szabályainak frissítése az útvonalszűrőt kezelés szabály lapjára kattintva.

![Hozzon létre egy útvonalszűrőhöz](./media/how-to-routefilter-portal/ManageRouteFilter.png)


Kiválaszthatja azokat a szolgáltatásokat, amelyekhez csatlakozni szeretne a legördülő listából, és ha elkészült, mentse a szabályt.

![Hozzon létre egy útvonalszűrőhöz](./media/how-to-routefilter-portal/AddRouteFilterRule.png)


## <a name="attach"></a>3. lépés: Az útvonal-szűrő csatolása ExpressRoute-áramkörhöz

Az útvonal-szűrőt csatolhatja egy áramkörhöz az "áramkör hozzáadása" gomb kiválasztásával, majd a ExpressRoute áramkör kiválasztásával a legördülő listából.

![Hozzon létre egy útvonalszűrőhöz](./media/how-to-routefilter-portal/AddCktToRouteFilter.png)

Ha a kapcsolatszolgáltató konfigurálja a társviszony-létesítést az ExpressRoute-kapcsolatcsoport frissítése a kapcsolatcsoportot az ExpressRoute-kapcsolatcsoport panelen a "kapcsolatcsoport hozzáadása" gomb kiválasztása előtt.

![Hozzon létre egy útvonalszűrőhöz](./media/how-to-routefilter-portal/RefreshExpressRouteCircuit.png)

## <a name="tasks"></a>Gyakori feladatok

### <a name="getproperties"></a>Hogy egy útvonalszűrőhöz tulajdonságainak beolvasása

Ha a portálon nyissa meg az erőforrást egy útvonalszűrőhöz tulajdonságait tekintheti meg.

![Hozzon létre egy útvonalszűrőhöz](./media/how-to-routefilter-portal/ViewRouteFilter.png)


### <a name="updateproperties"></a>Egy útvonalszűrőhöz tulajdonságainak frissítése

A BGP-Közösség értékét "Manage szabály" gomb kiválasztásával csatlakoztatása egy kapcsolatcsoporthoz csatlakoztatott listája frissítheti.


![Hozzon létre egy útvonalszűrőhöz](./media/how-to-routefilter-portal/ManageRouteFilter.png)

![Hozzon létre egy útvonalszűrőhöz](./media/how-to-routefilter-portal/AddRouteFilterRule.png) 


### <a name="detach"></a>Egy útvonalszűrőhöz az ExpressRoute-kapcsolatcsoport leválasztása

Ha egy áramkört szeretne leválasztani az útvonal-szűrőből, kattintson a jobb gombbal az áramkörre, és kattintson a "leválasztás" elemre.

![Hozzon létre egy útvonalszűrőhöz](./media/how-to-routefilter-portal/DetachRouteFilter.png) 


### <a name="delete"></a>Egy útvonalszűrőhöz törlése

Egy útvonalszűrőhöz a Törlés gombra kattintva törölheti. 

![Hozzon létre egy útvonalszűrőhöz](./media/how-to-routefilter-portal/DeleteRouteFilter.png) 

## <a name="next-steps"></a>További lépések

* További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).

* További információ az útválasztó-konfigurációs mintákról: [útválasztó-konfigurációs minták az Útválasztás beállításához és kezeléséhez](expressroute-config-samples-routing.md). 
