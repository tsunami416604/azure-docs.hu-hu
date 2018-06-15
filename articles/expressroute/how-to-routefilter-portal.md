---
title: 'Be annak az Azure ExpressRoute Microsoft társviszony-létesítés: portál |} Microsoft Docs'
description: Ez a cikk ismerteti az Azure portál használatával a Microsoft Peering útvonal szűrők konfigurálása
documentationcenter: na
services: expressroute
author: ganesr
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: ganesr
ms.openlocfilehash: ab0cd45334581f6f5c6dd6e86939e018df5de074
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29387727"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>Be annak a Microsoft társviszony-létesítéshez: Azure-portálon
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

### <a name="workflow"></a>munkafolyamat

Nem fogja tudni kapcsolódni a Microsoft társviszony-létesítés szolgáltatások, az alábbi konfigurációs lépéseket kell végrehajtania:

- Rendelkeznie kell egy aktív van a Microsoft társviszony kiosztott ExpressRoute-kapcsolatcsoportot. Az alábbi utasítások segítségével elvégezni ezeket a feladatokat:
  - [ExpressRoute-kapcsolatcsoportot létrehozni](expressroute-howto-circuit-portal-resource-manager.md) , és folytassa a kapcsolat szolgáltatójánál előtt által engedélyezett körön. Az ExpressRoute-kapcsolatcsoport kiépített és engedélyezett állapotban kell lennie.
  - [Hozzon létre a Microsoft társviszony-létesítés](expressroute-howto-routing-portal-resource-manager.md) kezeléséhez közvetlenül a BGP-munkamenetet. Vagy a kapcsolat szolgáltatójánál rendelkezik a kör társviszony Microsoft kiépítéséhez.

-  Hozzon létre és útvonal-szűrő konfigurálnia kell.
    - A szolgáltatás azonosítására, a Microsoft társviszony-létesítés keresztül felhasználásához
    - A szolgáltatásokkal társított BGP közösségi értékek listájának azonosítása
    - A BGP-Közösség értékek megfelelő előtag engedélyezőlistában szabály létrehozása

-  Az ExpressRoute-kapcsolatcsoport hozzá kell rendelni az útvonal-szűrőt.

## <a name="before-you-begin"></a>Előkészületek

Konfigurációs megkezdése előtt győződjön meg arról, hogy a következő feltételeknek:

 - Tekintse át a [Előfeltételek](expressroute-prerequisites.md) és [munkafolyamatok](expressroute-workflows.md) konfigurálás elkezdése előtt.

 - Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-portal-resource-manager.md) részben foglalt lépéseket, és engedélyeztesse a kapcsolatcsoportot kapcsolatszolgáltatójával, mielőtt továbblépne. Az ExpressRoute-kapcsolatcsoport kiépített és engedélyezett állapotban kell lennie.

 - Rendelkeznie kell egy aktív Microsoft társviszony-létesítés. Kövesse az utasításokat, [létrehozása, és társviszony-létesítési konfigurációjának módosítása](expressroute-howto-routing-portal-resource-manager.md)


## <a name="prefixes"></a>1. lépés: Az előtagok és BGP közösségi értékek listájának beolvasása

### <a name="1-get-a-list-of-bgp-community-values"></a>1. BGP-Közösség értékek listájának beolvasása

Elérhető a Microsoft társviszony-létesítés szolgáltatásokkal társított BGP közösségi értékek érhető el a [ExpressRoute útválasztási követelmények](expressroute-routing.md) lap.

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Ellenőrizze az értékeket, amelyeket használni kívánt listája

Ellenőrizze az útvonal szűrővel használni kívánt BGP közösségi értékek listáját. Tegyük fel a BGP közösségi Dynamics 365 szolgáltatások értéke 12076:5040.

## <a name="filter"></a>2. lépés: Útvonal szűrő, ezért a szűrési szabály létrehozása

Útvonal szűrő lehet csak egy szabályt, és a szabály a "Engedélyezés" típusúnak kell lennie. Ez a szabály társítva BGP közösségi értékből álló lista lehet.

### <a name="1-create-a-route-filter"></a>1. Útvonal szűrő létrehozása
Létrehozhat egy útvonal szűrőt válasszal létrehozhat egy új beállítás kiválasztásával. Kattintson a **hozzon létre egy erőforrást** > **hálózati** > **RouteFilter**, a következő ábrán látható módon:

![Útvonal szűrő létrehozása](.\media\how-to-routefilter-portal\CreateRouteFilter1.png)

Az útvonal szűrő erőforráscsoportban kell elhelyezni. 

![Útvonal szűrő létrehozása](.\media\how-to-routefilter-portal\CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2. Állapotszűrő szabály létrehozása

Adja hozzá, és válassza ki a kezelés szabály fület a útvonal szűrő szabályok módosítása.

![Útvonal szűrő létrehozása](.\media\how-to-routefilter-portal\ManageRouteFilter.png)


Kiválaszthatja a kívánt szolgáltatásokat, a legördülő listából csatlakozhat, és ha befejezte a szabály mentéséhez.

![Útvonal szűrő létrehozása](.\media\how-to-routefilter-portal\AddRouteFilterRule.png)


## <a name="attach"></a>3. lépés: Az útvonal szűrő csatolása ExpressRoute-kapcsolatcsoportot

Az útvonal szűrő csatolhat expressroute-kapcsolatcsoporthoz, válassza a "kör felvétele" gombra, majd válassza az ExpressRoute-kapcsolatcsoport a legördülő listából.

![Útvonal szűrő létrehozása](.\media\how-to-routefilter-portal\AddCktToRouteFilter.png)

Ha a kapcsolat szolgáltatójánál konfigurálja a az ExpressRoute-társviszony létesítése – áramkör frissítése a kapcsolatcsoport a ExpressRoute-kapcsolatcsoport paneljéről a "kör hozzáadása" gomb kiválasztása előtt.

![Útvonal szűrő létrehozása](.\media\how-to-routefilter-portal\RefreshExpressRouteCircuit.png)

## <a name="tasks"></a>Gyakori feladatok

### <a name="getproperties"></a>Az útvonal szűrő tulajdonságainak beolvasása

Útvonal-szűrő tulajdonságait tekintheti meg az erőforrás a portál megnyitásakor.

![Útvonal szűrő létrehozása](.\media\how-to-routefilter-portal\ViewRouteFilter.png)


### <a name="updateproperties"></a>Útvonal-szűrő tulajdonságainak módosítása

BGP-közösségi értéklistájának expressroute-kapcsolatcsoporthoz csatolva a "Szabály kezelése" gombra kattintva frissítheti.


![Útvonal szűrő létrehozása](.\media\how-to-routefilter-portal\ManageRouteFilter.png)

![Útvonal szűrő létrehozása](.\media\how-to-routefilter-portal\AddRouteFilterRule.png) 


### <a name="detach"></a>Az ExpressRoute-kapcsolatcsoportot útvonal szűrő leválasztása

Válassza le a kapcsolat a útvonal szűrőből, kattintson jobb gombbal a körön, majd kattintson a "társítását".

![Útvonal szűrő létrehozása](.\media\how-to-routefilter-portal\DetachRouteFilter.png) 


### <a name="delete"></a>Útvonal szűrő törlése

Törölheti a útvonal szűrő; ehhez válassza a Törlés gomb. 

![Útvonal szűrő létrehozása](.\media\how-to-routefilter-portal\DeleteRouteFilter.png) 

## <a name="next-steps"></a>További lépések

További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).