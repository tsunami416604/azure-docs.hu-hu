---
title: 'ExpressRoute: útvonal-szűrők – Microsoft-társak: Azure Portal'
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja a Microsoft társközi útvonal-szűrőit a Azure Portal használatával.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 07/01/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 54674be0010bd062cfe6263db4167a24805a9e5a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84727126"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>Útválasztási szűrők konfigurálása a Microsoft-partnerek számára: Azure Portal
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Az útvonalszűrők lehetővé teszik a támogatott szolgáltatások egy részének felhasználását Microsoft-társviszony-létesítésen keresztül. Az ebben a cikkben ismertetett lépések segítséget nyújtanak a ExpressRoute-áramkörök útválasztási szűrőinek konfigurálásához és kezeléséhez.

Az Office 365-szolgáltatások, például az Exchange Online, a SharePoint Online és a Skype vállalati verzió, valamint az Azure-szolgáltatások, például a Storage és az SQL Database, a Microsoft-partneri kapcsolaton keresztül érhetők el. Ha a Microsoft-társítás egy ExpressRoute-áramkörben van konfigurálva, a szolgáltatásokhoz kapcsolódó összes előtagot a létrehozott BGP-munkamenetek hirdetik ki. Minden előtaghoz egy BGP-közösségérték van csatolva, amely azonosítja az előtag keretében nyújtott szolgáltatást. A BGP közösségi értékek és a hozzájuk kapcsolódó szolgáltatások listáját itt tekintheti meg: [BGP-Közösségek](expressroute-routing.md#bgp).

Ha minden szolgáltatáshoz csatlakoznia kell, nagy számú előtagokat a BGP-n keresztül hirdetünk meg. Ez jelentősen növeli a hálózaton belüli útválasztók által karbantartott útválasztási táblázatok méretét. Ha azt tervezi, hogy csak a Microsoft-partnerek által kínált szolgáltatások egy részhalmazát szeretné használni, az útválasztási táblázatok méretét kétféleképpen csökkentheti. A következőket teheti:

- Kiszűri a nemkívánatos előtagokat az útválasztási szűrők alkalmazásával a BGP-közösségeken. Ez egy szabványos hálózatkezelési gyakorlat, és általában számos hálózaton belül használatos.

- Adja meg az útvonal-szűrőket, és alkalmazza őket a ExpressRoute-áramkörre. Az útválasztási szűrő egy új erőforrás, amellyel kiválaszthatja a Microsoft-társon keresztül használni kívánt szolgáltatások listáját. A ExpressRoute-útválasztók csak az útválasztási szűrőben azonosított szolgáltatásokhoz tartozó előtagok listáját küldik el.

### <a name="about-route-filters"></a><a name="about"></a>Az útválasztási szűrők ismertetése

Ha a Microsoft-társak konfigurálása a ExpressRoute-áramkörön történik, a Microsoft Edge útválasztók egy pár BGP-munkamenetet létesítenek a peremhálózati útválasztókkal (a tiéd vagy a kapcsolat szolgáltatója). Nincsenek útvonalak meghirdetve a hálózatán. Ha engedélyezni szeretné az útvonalhirdetéseket a hálózaton, társítania kell egy útvonalszűrőt.

Az útvonalszűrőkkel azonosíthatja az ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítésén keresztül használni kívánt szolgáltatásokat. Lényegében a BGP-Közösség összes olyan értékének listája, amelyet engedélyezni szeretne. Miután meghatározott és egy ExpressRoute-kapcsolatcsoporthoz csatolt egy útvonalszűrő erőforrást, a BGP-közösségértékekhez rendelt összes előtag meg van hirdetve a hálózaton.

Ahhoz, hogy az Office 365-szolgáltatásokkal csatolja az útválasztási szűrőket, rendelkeznie kell az Office 365-szolgáltatások ExpressRoute-en keresztüli használatára vonatkozó engedéllyel. Ha nincs engedélye arra, hogy az Office 365-szolgáltatásokat ExpressRoute-en keresztül használja, az útválasztási szűrők csatlakoztatásának művelete meghiúsul. Az engedélyezési folyamattal kapcsolatos további információkért lásd: [Azure ExpressRoute for Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> Az 2017. augusztus 1. előtt konfigurált ExpressRoute-áramkörök Microsoft-összevonása a Microsoft-társon keresztül meghirdetett összes szolgáltatás előtagja lesz, még akkor is, ha az útvonal-szűrők nincsenek meghatározva. A 2017 augusztus 1-jén vagy azt követően konfigurált ExpressRoute-áramkörök Microsoft-társítása nem rendelkezik olyan előtagokkal, amelyek csak akkor lesznek meghirdetve, ha az áramkörhöz hozzá van rendelve egy útvonal-szűrő.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Munkafolyamat

Ahhoz, hogy sikeresen tudjon csatlakozni a szolgáltatásokhoz a Microsoft-társon keresztül, el kell végeznie az alábbi konfigurációs lépéseket:

- Rendelkeznie kell egy aktív ExpressRoute-áramkörrel, amelyhez Microsoft-társítás van kiépítve. A következő utasításokat követve hajthatja végre ezeket a feladatokat:
  - A folytatás előtt [hozzon létre egy ExpressRoute áramkört](expressroute-howto-circuit-portal-resource-manager.md) , és engedélyezze az áramkört a kapcsolat szolgáltatója számára. A ExpressRoute áramkörnek kiépített és engedélyezett állapotban kell lennie.
  - [Hozzon létre Microsoft-társat](expressroute-howto-routing-portal-resource-manager.md) , ha közvetlenül a BGP-munkamenetet kezeli. Vagy a kapcsolat szolgáltatója kiépíti a Microsoft-társat az áramkörhöz.

-  Létre kell hoznia és konfigurálnia kell egy útvonal-szűrőt.
    - Azonosítsa a Microsoft-társon keresztül használni kívánt szolgáltatásokat
    - A szolgáltatásokhoz társított BGP-közösségi értékek listájának azonosítása
    - Szabály létrehozása a BGP-közösségi értékeknek megfelelő előtag-lista engedélyezéséhez

-  Az ExpressRoute áramkörhöz csatolni kell az útvonal-szűrőt.

## <a name="before-you-begin"></a>Előkészületek

A konfigurálás megkezdése előtt győződjön meg arról, hogy megfelel a következő feltételeknek:

 - A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](expressroute-prerequisites.md) és a [munkafolyamatokat](expressroute-workflows.md) .

 - Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-portal-resource-manager.md) részben foglalt lépéseket, és engedélyeztesse a kapcsolatcsoportot kapcsolatszolgáltatójával, mielőtt továbblépne. A ExpressRoute áramkörnek kiépített és engedélyezett állapotban kell lennie.

 - Aktív Microsoft-társat kell biztosítania. Kövesse a társítási [konfiguráció létrehozása és módosítása](expressroute-howto-routing-portal-resource-manager.md) című témakör utasításait


## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>1. lépés: az előtagok és a BGP közösségi értékek listájának lekérése

### <a name="1-get-a-list-of-bgp-community-values"></a>1. a BGP-közösségi értékek listájának beolvasása

A Microsoft-társon keresztül elérhető szolgáltatásokhoz társított BGP-közösségi értékek a [ExpressRoute útválasztási követelmények](expressroute-routing.md) lapján érhetők el.

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. a használni kívánt értékek listájának létrehozása

Készítse el az útválasztási szűrőben használni kívánt [BGP-közösségi értékek](expressroute-routing.md#bgp) listáját. 

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>2. lépés: útvonal-szűrő és szűrő-szabály létrehozása

Egy útvonal-szűrőnek csak egy szabálya lehet, és a szabálynak "Allow" típusúnak kell lennie. Ez a szabály tartalmazhatja a BGP-közösségi értékek listáját.

### <a name="1-create-a-route-filter"></a>1. útvonal-szűrő létrehozása
Létrehozhat egy útvonal-szűrőt az új erőforrás létrehozásához szükséges lehetőség kiválasztásával. Kattintson az **erőforrás létrehozása**  >  **hálózati**  >  **RouteFilter**elemre, ahogy az a következő képen látható:

![Útvonal-szűrő létrehozása](./media/how-to-routefilter-portal/CreateRouteFilter1.png)

Az útválasztási szűrőt egy erőforráscsoporthoz kell helyeznie. 

![Útvonal-szűrő létrehozása](./media/how-to-routefilter-portal/CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2. szűrési szabály létrehozása

A szabályok hozzáadásához és frissítéséhez válassza az útválasztási szűrő szabály kezelése lapját.

![Útvonal-szűrő létrehozása](./media/how-to-routefilter-portal/ManageRouteFilter.png)


Kiválaszthatja azokat a szolgáltatásokat, amelyekhez csatlakozni szeretne a legördülő listából, és ha elkészült, mentse a szabályt.

![Útvonal-szűrő létrehozása](./media/how-to-routefilter-portal/AddRouteFilterRule.png)


## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>3. lépés: az útvonal-szűrő csatolása egy ExpressRoute-áramkörhöz

Az útvonal-szűrőt csatolhatja egy áramkörhöz az "áramkör hozzáadása" gomb kiválasztásával, majd a ExpressRoute áramkör kiválasztásával a legördülő listából.

![Útvonal-szűrő létrehozása](./media/how-to-routefilter-portal/AddCktToRouteFilter.png)

Ha a kapcsolati szolgáltató konfigurálja a ExpressRoute-áramkör társítását, akkor az "áramkör hozzáadása" gomb megnyomása előtt frissítse az áramkört a ExpressRoute-áramkör paneljéről.

![Útvonal-szűrő létrehozása](./media/how-to-routefilter-portal/RefreshExpressRouteCircuit.png)

## <a name="common-tasks"></a><a name="tasks"></a>Gyakori feladatok

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Egy útvonal szűrő tulajdonságainak beolvasása

Az útválasztási szűrők tulajdonságait megtekintheti, amikor megnyitja az erőforrást a portálon.

![Útvonal-szűrő létrehozása](./media/how-to-routefilter-portal/ViewRouteFilter.png)


### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Egy útvonal szűrő tulajdonságainak frissítése

Az áramkörhöz csatolt BGP-közösségi értékek listáját a "szabály kezelése" gombra kattintva frissítheti.


![Útvonal-szűrő létrehozása](./media/how-to-routefilter-portal/ManageRouteFilter.png)

![Útvonal-szűrő létrehozása](./media/how-to-routefilter-portal/AddRouteFilterRule.png) 


### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Útvonal-szűrő leválasztása egy ExpressRoute-áramkörből

Ha egy áramkört szeretne leválasztani az útvonal-szűrőből, kattintson a jobb gombbal az áramkörre, és kattintson a "leválasztás" elemre.

![Útvonal-szűrő létrehozása](./media/how-to-routefilter-portal/DetachRouteFilter.png) 


### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Útvonal-szűrő törlése

A Delete (Törlés) gombra kattintva törölhet egy útvonal-szűrőt. 

![Útvonal-szűrő létrehozása](./media/how-to-routefilter-portal/DeleteRouteFilter.png) 

## <a name="next-steps"></a>Következő lépések

* A ExpressRoute kapcsolatos további információkért tekintse meg a [ExpressRoute gyakori kérdések](expressroute-faqs.md)című témakört.

* További információ az útválasztó-konfigurációs mintákról: [útválasztó-konfigurációs minták az Útválasztás beállításához és kezeléséhez](expressroute-config-samples-routing.md). 
