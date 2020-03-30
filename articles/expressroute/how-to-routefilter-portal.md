---
title: 'ExpressRoute: Útvonalszűrők – Microsoft társviszony-létesítési:Azure-portál'
description: Ez a cikk bemutatja, hogyan konfigurálhatja az útvonalszűrőket a Microsoft társviszony-létesítéséhez az Azure Portal használatával.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 07/01/2019
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 0b8e06ad5688374e5ab4aaa72d8485e6da797afe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74037442"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>Útvonalszűrők konfigurálása a Microsoft társviszony-létesítéséhez: Azure Portal
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Az útvonalszűrők lehetővé teszik a támogatott szolgáltatások egy részének felhasználását Microsoft-társviszony-létesítésen keresztül. A cikkben ismertetett lépések segítségével konfigurálhatja és kezelheti az ExpressRoute-áramkörök útvonalszűrőit.

Az Office 365-szolgáltatások, például az Exchange Online, a SharePoint Online és a Skype Vállalati verzió, valamint az Azure-szolgáltatások, például a tárolás és az SQL DB a Microsoft társviszony-létesítésén keresztül érhetők el. Ha a Microsoft-társviszony-létesítés ExpressRoute-kapcsolati kapcsolatban van konfigurálva, az ezekhez a szolgáltatásokhoz kapcsolódó összes előtagot a létrehozott BGP-munkameneteken keresztül hirdeti a rendszer. Minden előtaghoz egy BGP-közösségérték van csatolva, amely azonosítja az előtag keretében nyújtott szolgáltatást. A BGP-közösség értékeinek és a hozzáuk uk leképezett szolgáltatásoknak a listáját a [BGP-közösségek című témakörben tetszésszerint.](expressroute-routing.md#bgp)

Ha az összes szolgáltatáshoz szüksége van, a BGP-n keresztül nagy számú előtagot hirdet a szolgáltatás. Ez jelentősen megnöveli a hálózaton belüli útválasztók által fenntartott útvonaltáblák méretét. Ha a Microsoft társviszony-létesítésen keresztül kínált szolgáltatásoknak csak egy részét kívánja felhasználni, kétféleképpen csökkentheti az útvonaltáblák méretét. A következőket teheti:

- A nem kívánt előtagok kiszűrése útvonalszűrők alkalmazásával a BGP-közösségeken. Ez egy szabványos hálózati gyakorlat, és gyakran használják számos hálózaton belül.

- Definiálja az útvonalszűrőket, és alkalmazza őket az ExpressRoute-kapcsolatra. Az útvonalszűrő egy új erőforrás, amely lehetővé teszi a Microsoft-társviszony-létesítés során használni kívánt szolgáltatások listájának kiválasztását. Az ExpressRoute-útválasztók csak az útvonalszűrőben azonosított szolgáltatásokhoz tartozó előtagok listáját küldik el.

### <a name="about-route-filters"></a><a name="about"></a>Útvonalszűrők –

Ha a Microsoft társviszony-létesítése konfigurálva van az ExpressRoute-kapcsolatcsoporton, a Microsoft peremhálózati útválasztói bgp-munkameneteket hoznak létre a peremhálózati útválasztókkal (az Ön ével vagy a kapcsolatszolgáltatóéval). Nincsenek útvonalak meghirdetve a hálózatán. Ha engedélyezni szeretné az útvonalhirdetéseket a hálózaton, társítania kell egy útvonalszűrőt.

Az útvonalszűrőkkel azonosíthatja az ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítésén keresztül használni kívánt szolgáltatásokat. Lényegében az összes engedélyezni kívánt BGP-közösségi érték listája. Miután meghatározott és egy ExpressRoute-kapcsolatcsoporthoz csatolt egy útvonalszűrő erőforrást, a BGP-közösségértékekhez rendelt összes előtag meg van hirdetve a hálózaton.

Ahhoz, hogy útvonalszűrőket csatolhassanon az Office 365-szolgáltatásokhoz, engedéllyel kell rendelkeznie ahhoz, hogy az ExpressRoute-on keresztül office 365-szolgáltatásokat használja. Ha nincs jogosult office 365-szolgáltatások felhasználására az ExpressRoute-on keresztül, az útvonalszűrők csatolására irányuló művelet sikertelen lesz. Az engedélyezési folyamatról további információt az [Office 365-alapú Azure ExpressRoute című témakörben talál.](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)

> [!IMPORTANT]
> 2017. augusztus 1-je előtt konfigurált ExpressRoute-áramkörök Microsoft-társviszony-létesítése az összes szolgáltatáselőtagot a Microsoft társviszony-létesítésén keresztül hirdeti, még akkor is, ha nincsenek meghatározva az útvonalszűrők. 2017. augusztus 1-jén vagy azt követően konfigurált ExpressRoute-áramkörök Microsoft-társviszony-létesítése nem rendelkezik előtagokkal, amíg egy útvonalszűrő nem csatlakozik az áramkörhöz.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Munkafolyamat

Ahhoz, hogy a Microsoft társviszony-létesítésével sikeresen kapcsolódhatna a szolgáltatásokhoz, a következő konfigurációs lépéseket kell végrehajtania:

- Olyan aktív ExpressRoute-kapcsolattal kell rendelkeznie, amelyhez a Microsoft társviszony-létesítése is van. Az alábbi utasításokat használhatja a következő feladatok elvégzéséhez:
  - [Hozzon létre egy ExpressRoute-áramkört,](expressroute-howto-circuit-portal-resource-manager.md) és a folytatás előtt engedélyezve legyen a kapcsolatszolgáltató. Az ExpressRoute-kapcsolatcsoportnak kiépített és engedélyezett állapotban kell lennie.
  - [Hozzon létre Microsoft-társviszony-létesítést,](expressroute-howto-routing-portal-resource-manager.md) ha közvetlenül kezeli a BGP-munkamenetet. Vagy a kapcsolatszolgáltató üzembe helyezheti a Microsoft-társviszony-létesítést a kapcsolatcsoporthoz.

-  Létre kell hoznia és konfigurálnia kell egy útvonalszűrőt.
    - Azonosítsa azokat a szolgáltatásokat, amelyeket a Microsoft társviszony-létesítésével szeretne igénybe adni
    - A szolgáltatásokhoz társított BGP-közösségi értékek listájának azonosítása
    - Hozzon létre egy szabályt, amely engedélyezi a BGP-közösség értékeinek megfelelő előtaglistát

-  Az útvonalszűrőt csatolnia kell az ExpressRoute-kapcsolathoz.

## <a name="before-you-begin"></a>Előkészületek

A konfiguráció megkezdése előtt győződjön meg arról, hogy megfelel az alábbi feltételeknek:

 - A konfiguráció megkezdése előtt tekintse át az [előfeltételeket](expressroute-prerequisites.md) és [a munkafolyamatokat.](expressroute-workflows.md)

 - Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-portal-resource-manager.md) részben foglalt lépéseket, és engedélyeztesse a kapcsolatcsoportot kapcsolatszolgáltatójával, mielőtt továbblépne. Az ExpressRoute-kapcsolatcsoportnak kiépített és engedélyezett állapotban kell lennie.

 - Aktív Microsoft-társviszony-létesítésre van szüksége. Kövesse a [társviszony-létesítési konfiguráció létrehozása és módosítása című](expressroute-howto-routing-portal-resource-manager.md) útmutató utasításait


## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>1. lépés: Előtagok és BGP-közösségi értékek listájának beszereznie

### <a name="1-get-a-list-of-bgp-community-values"></a>1. A BGP közösségi értékek listájának beszereznie

A Microsoft-társviszony-létesítésen keresztül elérhető szolgáltatásokhoz társított BGP-közösségi értékek az [ExpressRoute útválasztási követelmények](expressroute-routing.md) lapon érhetők el.

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Készíts egy listát a használni kívánt értékekről

Készítse el az útvonalszűrőben használni kívánt [BGP-közösségi értékek](expressroute-routing.md#bgp) listáját. 

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>2. lépés: Útvonalszűrő és szűrőszabály létrehozása

Az útvonalszűrőnek csak egy szabálya lehet, és a szabálynak "Engedélyezés" típusúnak kell lennie. Ez a szabály a BGP-közösség értékeit tartalmazó listát tartalmaz.

### <a name="1-create-a-route-filter"></a>1. Útvonalszűrő létrehozása
Útvonalszűrőt úgy hozhat létre, hogy új erőforrást hoz létre. Kattintson az > **Erőforrás-hálózati** > **routefilter** **létrehozása**elemre az alábbi képen látható módon:

![Útvonalszűrő létrehozása](./media/how-to-routefilter-portal/CreateRouteFilter1.png)

Az útvonalszűrőt erőforráscsoportba kell helyeznie. 

![Útvonalszűrő létrehozása](./media/how-to-routefilter-portal/CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2. Szűrőszabály létrehozása

A szabályok hozzáadásához és frissítéséhez jelölje ki az útvonalszűrő szabálykezelési fülét.

![Útvonalszűrő létrehozása](./media/how-to-routefilter-portal/ManageRouteFilter.png)


A legördülő listából kiválaszthatja azokat a szolgáltatásokat, amelyekhez csatlakozni szeretne, és ha végzett, mentheti a szabályt.

![Útvonalszűrő létrehozása](./media/how-to-routefilter-portal/AddRouteFilterRule.png)


## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>3. lépés: Az útvonalszűrő csatlakoztatása ExpressRoute-áramkörhöz

Az útvonalszűrőt az "Áramkör hozzáadása" gombra kattintva és az ExpressRoute-kapcsolat kiválasztásával csatolhatja egy áramkörhöz a legördülő listából.

![Útvonalszűrő létrehozása](./media/how-to-routefilter-portal/AddCktToRouteFilter.png)

Ha a kapcsolatszolgáltató konfigurálja az ExpressRoute-kapcsolatlétesítést az ExpressRoute-kapcsolati kapcsolatcsoporthoz, frissítse az áramkört az ExpressRoute-áramköri panelről, mielőtt az "Áramkör hozzáadása" gombot választaná.

![Útvonalszűrő létrehozása](./media/how-to-routefilter-portal/RefreshExpressRouteCircuit.png)

## <a name="common-tasks"></a><a name="tasks"></a>Gyakori feladatok

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Útvonalszűrő tulajdonságainak bekésezése

Az útvonalszűrő tulajdonságait megtekintheti, amikor megnyitja az erőforrást a portálon.

![Útvonalszűrő létrehozása](./media/how-to-routefilter-portal/ViewRouteFilter.png)


### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Útvonalszűrő tulajdonságainak frissítése

A "Szabály kezelése" gombra kattintva frissítheti az áramkörhöz csatolt BGP-közösségi értékek listáját.


![Útvonalszűrő létrehozása](./media/how-to-routefilter-portal/ManageRouteFilter.png)

![Útvonalszűrő létrehozása](./media/how-to-routefilter-portal/AddRouteFilterRule.png) 


### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Útvonalszűrő leválasztása ExpressRoute-áramkörről

Ha le szeretne választani egy áramkört az útvonalszűrőről, kattintson a jobb gombbal az áramkörre, és kattintson a "szétválasztás" gombra.

![Útvonalszűrő létrehozása](./media/how-to-routefilter-portal/DetachRouteFilter.png) 


### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Útvonalszűrő törlése

Az útvonalszűrőt a törlés gombra kattintva törölheti. 

![Útvonalszűrő létrehozása](./media/how-to-routefilter-portal/DeleteRouteFilter.png) 

## <a name="next-steps"></a>Következő lépések

* Az ExpressRoute-ról további információt az [ExpressRoute gyakori kérdések című](expressroute-faqs.md)témakörben talál.

* Az útválasztó konfigurációs mintáiról az [Útválasztó konfigurációs mintái az útválasztás beállításához és kezeléséhez](expressroute-config-samples-routing.md)című témakörben talál további információt. 
