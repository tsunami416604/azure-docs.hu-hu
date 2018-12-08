---
title: 'Microsoft társviszony-létesítés útvonalszűrőinek konfigurálása: Azure ExpressRoute - portálon |} A Microsoft Docs'
description: Ez a cikk ismerteti a Microsoft társviszony-létesítés az Azure portal használatával útvonalszűrőinek konfigurálása.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 09/26/2018
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 0515b5e85c3bcf56f1f238620d6036d1be0bec7e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104204"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>Microsoft társviszony-létesítés útvonalszűrőinek konfigurálása: az Azure Portalon
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Az útvonalszűrők lehetővé teszik a támogatott szolgáltatások egy részének felhasználását Microsoft-társviszony-létesítésen keresztül. A jelen cikkben ismertetett lépések segítségével konfigurálhatja és kezelheti az ExpressRoute-Kapcsolatcsoportok útvonalszűrőinek.

Dynamics 365-szolgáltatások, valamint a Office 365-szolgáltatásokhoz, például az Exchange Online, SharePoint Online és Skype for Business és az Azure-szolgáltatások például a storage és SQL DB a Microsoft társviszony-létesítésen keresztül érhetők el. Az ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítés konfigurálásakor a ezekhez a szolgáltatásokhoz kapcsolódó összes előtagokat hirdet meg a BGP-munkamenetek létesített keresztül. Minden előtaghoz egy BGP-közösségérték van csatolva, amely azonosítja az előtag keretében nyújtott szolgáltatást. A BGP-Közösség értékét, és a szolgáltatások leképezik a listáját lásd: [BGP-Közösségek](expressroute-routing.md#bgp).

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


## <a name="prefixes"></a>1. lépés: Az előtagok és BGP-Közösség értékét listájának lekérése

### <a name="1-get-a-list-of-bgp-community-values"></a>1. A BGP-Közösség értékét tartalmazó lista beolvasása

A Microsoft társviszony-létesítésen keresztül elérhető szolgáltatások társított BGP-Közösség értékét érhető el a [az ExpressRoute útválasztási követelményei](expressroute-routing.md) lapot.

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Győződjön meg a használni kívánt értékek listáját

Ellenőrizze a BGP-Közösség értékét az útvonalszűrőt használni kívánt listáját. Tegyük fel a Dynamics 365-szolgáltatásokhoz a BGP-közösségérték 12076:5040.

## <a name="filter"></a>2. lépés: Egy útvonalszűrőhöz és a egy Állapotszűrő szabály létrehozása

Egy útvonalszűrőhöz lehet csak egy szabályt, és a szabály "Engedélyezés" típusúnak kell lennie. Ez a szabály is van egy listája azokról a BGP-Közösség értékét társítva.

### <a name="1-create-a-route-filter"></a>1. Hozzon létre egy útvonalszűrőhöz
Létrehozhat egy útvonalszűrőhöz hozzon létre egy új erőforrást a beállítás kiválasztásával. Kattintson a **erőforrás létrehozása** > **hálózatkezelés** > **útvonalszűrőt**, ahogy az alábbi képen látható:

![Hozzon létre egy útvonalszűrőhöz](./media/how-to-routefilter-portal/CreateRouteFilter1.png)

Az útvonalszűrőt egy erőforráscsoportban kell elhelyeznie. 

![Hozzon létre egy útvonalszűrőhöz](./media/how-to-routefilter-portal/CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2. Szűrési szabály létrehozása

Hozzáadhat és szabályainak frissítése az útvonalszűrőt kezelés szabály lapjára kattintva.

![Hozzon létre egy útvonalszűrőhöz](./media/how-to-routefilter-portal/ManageRouteFilter.png)


Kiválaszthatja a kívánt szolgáltatást, a legördülő listából csatlakozik, és ha elkészült a szabály mentéséhez.

![Hozzon létre egy útvonalszűrőhöz](./media/how-to-routefilter-portal/AddRouteFilterRule.png)


## <a name="attach"></a>3. lépés: Az útvonalszűrőt csatlakoztatása egy ExpressRoute-kapcsolatcsoporttal

Jelölje ki a "kapcsolatcsoport hozzáadása" gombra, és a legördülő listából válassza ki az ExpressRoute-kapcsolatcsoport útvonalszűrőt csatolhat egy kapcsolatcsoporthoz.

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

Az útvonalszűrőt kapcsolatcsoport leválasztása, a kapcsolatcsoport kattintson a jobb gombbal, majd kattintson a "szüntesse meg az".

![Hozzon létre egy útvonalszűrőhöz](./media/how-to-routefilter-portal/DetachRouteFilter.png) 


### <a name="delete"></a>Egy útvonalszűrőhöz törlése

Egy útvonalszűrőhöz a Törlés gombra kattintva törölheti. 

![Hozzon létre egy útvonalszűrőhöz](./media/how-to-routefilter-portal/DeleteRouteFilter.png) 

## <a name="next-steps"></a>További lépések

További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).