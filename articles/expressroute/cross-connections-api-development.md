---
title: Az Azure ExpressRoute CrossConnnections API fejlesztése és integrációja
description: Ez a cikk részletes áttekintést nyújt az ExpressRoute-partnerek nek az expressRouteCrossConnections erőforrástípusról.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: mialdrid
ms.openlocfilehash: b4a83013d2cbeb2871a3963ec0c95144c02f4d66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77187012"
---
# <a name="expressroute-crossconnnections-api-development-and-integration"></a>ExpressRoute CrossConnnections API fejlesztés és integráció

Az ExpressRoute Partner Resource Manager API lehetővé teszi az ExpressRoute-partnerek számára az ügyfél ExpressRoute-áramkörök 2- es és 3-as rétegkonfigurációjának kezelését. Az ExpressRoute Partner Resource Manager API egy új erőforrástípust, **az expressRouteCrossConnections-t**vezet be. A partnerek ezt az erőforrást használják az ügyfelek ExpressRoute-áramköri viszonyainak kezelésére.

## <a name="workflow"></a>Munkafolyamat

Az expressRouteCrossConnections erőforrás az ExpressRoute-kapcsolat árnyékerőforrása. Amikor egy Azure-ügyfél létrehoz egy ExpressRoute-csoportot, és kiválaszt egy adott ExpressRoute-partnert, a Microsoft létrehoz egy expressRouteCrossConnections erőforrást a partner Azure ExpressRoute-felügyeleti előfizetésében. Ennek során a Microsoft egy erőforráscsoportot határoz meg az expressRouteCrossConnections erőforrás létrehozásához. Az erőforráscsoport elnevezési szabványa **CrossConnection-* Társviszony-létesítés***; ahol peeringlocation = az ExpressRoute helye. Ha például egy ügyfél létrehoz egy ExpressRoute-csoportot Denverben, a CrossConnection a partner Azure-előfizetésében jön létre a következő erőforráscsoportban: **CrossConnnection-Denver**.

Az ExpressRoute-partnerek a 2- es és 3-as rétegkonfigurációt rest-műveletek kiadásával kezelik az expressRouteCrossConnections erőforrással szemben.

## <a name="benefits"></a>Előnyök

Az expressRouteCrossConnections erőforrásra való áttérés előnyei:

* Az ExpressRoute-partnerek jövőbeli fejlesztései elérhetők lesznek az ExpressRouteCrossConnection erőforráson.

* A partnerek [szerepköralapú hozzáférés-vezérlést](https://docs.microsoft.com/azure/role-based-access-control/overview) alkalmazhatnak az expressRouteCrossConnection erőforrásra. Ezek a vezérlők meghatározhatják azokat az engedélyeket, amelyekhez a felhasználói fiókok módosíthatják az expressRouteCrossConnection erőforrást, és hozzáadhatnak/frissíthetnek/törölhetnek társviszony-létesítési konfigurációkat.

* Az expressRouteCrossConnection erőforrás olyan API-kat tár fel, amelyek hasznosak lehetnek az ExpressRoute-kapcsolatok hibaelhárításában. Ez magában foglalja az ARP-táblát, a BGP-útvonaltábla összegzését és a BGP-útvonaltábla részleteit. Ezt a funkciót a klasszikus üzembe helyezési API-k nem támogatják.

* A partnerek a *RouteFilter-erőforrás* használatával a Microsoft társviszony-létesítésén is kereshetik a hirdetett közösségeket.

## <a name="api-development-and-integration-steps"></a>API-fejlesztési és -integrációs lépések

A Partner API-val való fejlesztéshez az ExpressRoute-partnerek egy tesztügyfelet és tesztpartner-beállítást használhatnak. A tesztügyfél-beállítás expressroute-áramkörök létrehozására szolgál a próbaeszközökhöz és portokhoz leképező teszttársviszony-létesítési helyeken. A tesztpartner beállítása a teszttárs-létesítési helyen létrehozott ExpressRoute-áramkörök kezelésére szolgál.

### <a name="1-enlist-subscriptions"></a>1. Besorozás előfizetések

A tesztpartner és az ügyfél beállításának igényléséhez kérjen két azure-os fizetéses Azure-előfizetést expressroute-i mérnöki kapcsolattartójára:
* **ExpressRoute_API_Dev_Provider_Sub:** Ez az előfizetés a próbaeszközökön és portokon teszttársviszony-létesítési helyeken létrehozott ExpressRoute-áramkörök kezelésére szolgál.

* **ExpressRoute_API_Dev_Customer_Sub:** Ez az előfizetés expressroute-áramkörök létrehozására szolgál a próbaverzióhelyeken, amelyek a hamis eszközökhöz és portokhoz vannak leképezve.

A teszttársviszony-létesítési helyek: a hamis eszközök és a portok alapértelmezés szerint nincsenek kitéve az éles ügyfeleknek. Annak érdekében, hogy ExpressRoute-áramköröket hozzon létre, amelyek leképezik a tesztbeállításokat, engedélyezni kell egy előfizetési szolgáltatásjelzőt.

### <a name="2-register-the-dev_provider-subscription-to-access-the-expressroutecrossconnections-api"></a>2. Regisztrálja a Dev_Provider előfizetést az expressRouteCrossConnections API eléréséhez

Az expressRouteCrossConnections API eléréséhez a partner-előfizetést be kell jegyezni a **Microsoft.Network Resource Provider**szolgáltatásba. Kövesse az [Azure-erőforrás-szolgáltatók](/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal) lépéseit, és írja be a cikket a regisztrációs folyamat befejezéséhez.

### <a name="3-set-up-authentication-for-azure-resource-manager-rest-api-calls"></a>3. Hitelesítés beállítása az Azure Resource Manager REST API-hívásokhoz

A legtöbb Azure-szolgáltatás ügyfélkódra van szükség az Erőforrás-kezelővel való hitelesítéshez, érvényes hitelesítő adatok használatával, a szolgáltatás API-jainak hívása előtt. A hitelesítést az Azure AD koordinálja a különböző szereplők között, és az ügyfél számára egy hozzáférési jogkivonatot biztosít a hitelesítés igazolásaként.

A hitelesítési folyamat két fő lépésből áll:

1. [Regisztrálja az ügyfelet](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad).
2. [Hozza létre a hozzáférési kérelmet.](https://docs.microsoft.com/rest/api/azure/#create-the-request)

### <a name="4-provide-network-contributor-permission-to-the-client-application"></a>4. Hálózati közreműködői engedély biztosítása az ügyfélalkalmazásszámára

A hitelesítés sikeres konfigurálása után a hálózati közreműködőnek hozzáférést kell biztosítania az ügyfélalkalmazáshoz a Dev_Provider_Sub alatt. Az engedély megadásához jelentkezzen be az [Azure Portalra,](https://ms.portal.azure.com/#home) és hajtsa végre az alábbi lépéseket:

1. Nyissa meg az Előfizetések elemet, és válassza ki a Dev_Provider_Sub
2. Navigálás a hozzáférés-vezérlés (IAM) pontjára
3. Szerepkör-hozzárendelés hozzáadása
4. A hálózati közreműködői szerepkör kiválasztása
5. Hozzáférés hozzárendelése az Azure AD-felhasználóhoz, csoporthoz vagy egyszerű szolgáltatáshoz
6. Az ügyfélalkalmazás kiválasztása
7. Módosítások mentése

### <a name="5-develop"></a>5. Fejlesztés

Dolgozzon az [expressRouteCrossConnections API-val.](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections)

## <a name="rest-api"></a>REST API

Lásd: [ExpressRoute CrossConnections REST API](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections) rest API-t a REST API dokumentációját.

## <a name="next-steps"></a>További lépések

Az ExpressRoute REST összes API-járól további információt az [ExpressRoute REST API-k című témakörben talál.](https://docs.microsoft.com/rest/api/expressroute/)