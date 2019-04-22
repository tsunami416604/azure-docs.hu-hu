---
title: Az Azure közötti hálózati kapcsolat |} A Microsoft Docs
description: Ez az oldal egy alkalmazás forgatókönyv közötti hálózati kapcsolatot és az Azure hálózati funkciókat alapuló megoldás ismerteti.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 4923c7f2048b7368af6314d5e2288216115bc3bc
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59784739"
---
# <a name="cross-network-connectivity"></a>Hálózatok közötti kapcsolatok

A Fabrikam, Inc. rendelkezik egy nagy méretű fizikai jelenlét és az Azure-telepítés az USA keleti régiójában. A Fabrikam rendelkezik háttér-kapcsolattal a helyszíni és Azure expressroute-on keresztül-környezetek között. Ehhez hasonlóan a Contoso Ltd. jelenlét érdekében, illetve az USA nyugati Azure-telepítés rendelkezik. Contoso rendelkezik háttér-kapcsolattal a helyszíni és Azure expressroute-on keresztül-környezetek között.  

A Fabrikam, Inc. beszerzi a Contoso Kft. A fúzió, a következő Fabrikam azt szeretné, a hálózatok összekapcsolhatók. Az alábbi ábra a forgatókönyvet mutatja be:

 [![1]][1]

A fenti ábrán közepén szaggatott nyilak jelzik a kívánt hálózati csatlakozás. Pontosabban a kívánt közötti kapcsolatok három típusa van: 1.) a Fabrikam és a Contoso virtuális hálózatok közötti kapcsolódás, 2) közötti regionális helyszíni és a virtuális hálózatok közötti kapcsolódik (amely, a Fabrikam a helyszíni hálózat csatlakoztatása az Contoso virtuális hálózat és a Contoso a helyszíni hálózat csatlakoztatása az Fabrikam VNet), és a Fabrikam 3), és a Contoso a helyszíni hálózat közötti kapcsolatot. 

Az alábbi táblázat az útválasztási táblázatot, a privát társviszony-létesítés, az ExpressRoute a Contoso Ltd., az egyesülés előtt.

[![2]][2]

Az alábbi táblázat az érvényes útvonalak a virtuális gépek a Contoso az előfizetésben az egyesülés előtt. A táblánként a virtuális hálózat címterét, és a Contoso a helyszíni hálózat, az alapértelmezett meglévőket szereplőkkel tisztában a virtuális Gépet a virtuális hálózaton. 

[![4]][4]

Az alábbi táblázat az útválasztási táblázatot, a privát társviszony-létesítés, az ExpressRoute a Fabrikam Inc., az egyesülés előtt.

[![3]][3]

Az alábbi táblázat az érvényes útvonalak a virtuális gépek a Fabrikam az előfizetésben az egyesülés előtt. A táblánként a virtuális hálózat címterét, és a Fabrikam a helyszíni hálózat, az alapértelmezett meglévőket szereplőkkel tisztában a virtuális Gépet a virtuális hálózaton.

[![5]][5]

Ebben a cikkben vegyünk át lépésről lépésre, és ismertetik a kívánt közötti kapcsolatok használatával az alábbi Azure-beli hálózati szolgáltatások eléréséhez:

* [Virtuális hálózatok közötti társviszony][Virtual network peering] 
* [Virtuális hálózat ExpressRoute-kapcsolat][connection]
* [Globális elérhetőség][Global Reach] 

## <a name="cross-connecting-vnets"></a>Tartományok közötti virtuális hálózatok csatlakoztatása

A legoptimálisabb és a legjobb hálózati teljesítmény virtuális hálózatok közötti társviszony (virtuális hálózatok közötti társviszony-létesítés) biztosít, amikor két virtuális hálózat összekapcsolása. Virtuális hálózatok közötti társviszony a társviszony-létesítés két virtuális hálózat (más néven virtuális hálózatok közötti társviszony) azonos Azure-régióban és a két különböző Azure-régió (más néven globális virtuális társhálózatok létesítése) is támogatja. 

A Contoso és Fabrikam Azure-előfizetések virtuális hálózatok közötti társviszony-létesítés globális virtuális végezzük konfigurálását. A virtuális hálózat két közötti társviszony-létesítés virtuális hálózatok létrehozása, olvassa el a [virtuális hálózati társviszony-létesítés] [ Configure VNet peering] cikk.

Az alábbi képen látható a hálózati architektúra konfigurálása a globális virtuális társhálózatok létesítése után.

[![6]][6]

Az alábbi táblázat az útvonalakat a Contoso-előfizetéshez VM ismert. A tábla utolsó bejegyzése figyelmet fordítania. Ez a bejegyzés csatlakoztathatók a virtuális hálózatok közötti eredménye.

[![7]][7]

Az alábbi táblázat az útvonalakat a Fabrikam előfizetéshez VM ismert. A tábla utolsó bejegyzése figyelmet fordítania. Ez a bejegyzés csatlakoztathatók a virtuális hálózatok közötti eredménye.

[![8]][8]

Közvetlenül virtuális hálózatok közötti társviszony létesítés két virtuális hálózatot (lásd: nincsenek nem a következő Ugrás *VNetGlobalPeering* bejegyzést a fenti két tábla)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Tartományok közötti virtuális hálózatok csatlakoztatása a helyszíni hálózatok

Több virtuális hálózat ExpressRoute-kapcsolatcsoport csatlakozhassunk. Lásd: [előfizetés- és Szolgáltatáskorlátok] [ Subscription limits] az ExpressRoute-kapcsolatcsoport lehet csatlakoztatni virtuális hálózatok maximális száma. 

Csatlakozzunk Fabrikam ExpressRoute-kapcsolatcsoport Contoso előfizetés virtuális hálózathoz, és hasonlóképpen Contoso ExpressRoute-kapcsolatcsoport Fabrikam előfizetéshez virtuális hálózatok közötti virtuális hálózatok és a helyszíni hálózatok közötti kapcsolat. A virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporthoz, egy másik előfizetésben, szükségünk hozhat létre és használhat egy engedélyezési.  Tekintse meg a cikket: [Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporthoz][Connect-ER-VNet].

Az alábbi képen látható a hálózati architektúra az ExpressRoute konfigurálása után közötti kapcsolatot a virtuális hálózatok.

[![9]][9]

Az alábbi táblázat az útválasztási táblázatot, a privát társviszony-létesítés, a Contoso Kft. ExpressRoute után közötti virtuális hálózatok összekapcsolása az expressroute-on keresztül a helyszíni hálózatokhoz. Tekintse meg, hogy az útvonaltáblát rendelkezik-e a virtuális hálózatok tartozó útvonalak.

[![10]][10]

Az alábbi táblázat az útválasztási táblázatot, a privát társviszony-létesítés, a Fabrikam ExpressRoute Inc. után közötti virtuális hálózatok összekapcsolása az expressroute-on keresztül a helyszíni hálózatokhoz. Tekintse meg, hogy az útvonaltáblát rendelkezik-e a virtuális hálózatok tartozó útvonalak.

[![11]][11]

Az alábbi táblázat az útvonalakat a Contoso-előfizetéshez VM ismert. Figyelmet fordítani *virtuális hálózati átjáró* bejegyzéseket a tábla. A virtuális gép útvonalakat a helyszíni hálózatok mindkét fog látni.

[![12]][12]

Az alábbi táblázat az útvonalakat a Fabrikam előfizetéshez VM ismert. Figyelmet fordítani *virtuális hálózati átjáró* bejegyzéseket a tábla. A virtuális gép útvonalakat a helyszíni hálózatok mindkét fog látni.

[![13]][13]

>[!NOTE]
>Akár az is lehet a Fabrikam és/vagy Contoso előfizetések küllő virtuális hálózatok a megfelelő agyi virtuális hálózat (egy küllős tervező nem mutatja be ez a cikk az architektúra-diagramok). Keresztszűrés kapcsolatainak a hub virtuális hálózati átjárók az expressroute-hoz is lehetővé teszi a kommunikációt keleti régiójában és Nyugat-hubs és a küllők között.
>

## <a name="cross-connecting-on-premises-networks"></a>A helyszíni hálózatok csatlakoztatását adatbázisközi

Az ExpressRoute globális elérhetőségű biztosítja a helyszíni hálózatokhoz csatlakozó ExpressRoute-Kapcsolatcsoportok különböző közötti kapcsolatot. Konfiguráljuk az globális elérhetőségű a Contoso és Fabrikam ExpressRoute-Kapcsolatcsoportok között. Mivel az ExpressRoute-Kapcsolatcsoportok különböző előfizetésekben találhatóak, hozhat létre és használhat egy engedélyezési kell. Lásd: [konfigurálása ExpressRoute globális elérhetőségű] [ Configure Global Reach] cikkben részletes útmutatást.

Az alábbi képen látható a hálózati architektúra globális elérhetőségű konfigurálása után.

[![14]][14]

Az alábbi táblázat az útválasztási táblázatot, a Contoso Kft. ExpressRoute privát társviszony globális elérhetőségű konfigurálása után. Tekintse meg, hogy az útvonaltáblát útvonalakat a helyszíni hálózatokhoz tartozó rendelkezik-e. 

[![15]][15]

Az alábbi táblázat az útválasztási táblázatot, a privát társviszony-létesítés, a Fabrikam ExpressRoute Inc. globális elérhetőségű konfigurálása után. Tekintse meg, hogy az útvonaltáblát útvonalakat a helyszíni hálózatokhoz tartozó rendelkezik-e.

[![16]][16]

## <a name="next-steps"></a>További lépések

Lásd: [virtuális hálózat – gyakori kérdések][VNet-FAQ], bármilyen további virtuális hálózatok és virtuális hálózatok közötti társviszony kérdés esetén. Lásd: [ExpressRoute – gyakori kérdések] [ ER-FAQ] minden olyan további kérdésekre, az expressroute-on és a virtuális hálózati kapcsolat.

Globális elérhetőség ország szerint ország alapján jelennek meg. Ha a globális elérhetőségű országban érhető el a kívánt megtekintéséhez lásd: [ExpressRoute globális elérhetőségű][Global Reach].

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "az alkalmazási forgatókönyv"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "Contoso ExpressRoute útválasztási táblázat egyesülés előtt"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "Fabrikam ExpressRoute útválasztási táblázat egyesülés előtt"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "előtt egyesülés irányítja a Contoso virtuális gép"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "előtt egyesülés irányítja a Fabrikam VM"
[6]: ./media/cross-network-connectivity/vnet-peering.png "után a virtuális hálózatok közötti társviszony az architektúra"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "Contoso VM irányítja a virtuális társhálózatok létesítése után"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "Fabrikam VM irányítja a virtuális társhálózatok létesítése után"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "az architektúra után útvonalszűrőt közötti kapcsolat"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "Contoso ExpressRoute útválasztási táblázatot követően csatlakozó ExR és virtuális hálózatok közötti"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "Fabrikam ExpressRoute útválasztási táblázatot követően csatlakozó ExR és virtuális hálózatok közötti"
[12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "Contoso Virtuálisgép-útvonalak után csatlakozó ExR és virtuális hálózatok közötti"
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "Fabrikam Virtuálisgép-útvonalak után csatlakozó ExR és virtuális hálózatok közötti"
[14]: ./media/cross-network-connectivity/globalreach.png "globális elérhetőségű konfigurálása után az architektúra"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "Contoso ExpressRoute útválasztási táblázatot követően globális elérhetőségű"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "globális elérhetőségű után a Fabrikam az ExpressRoute útválasztási táblázat"

<!--Link References-->
[Virtual network peering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[connection]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure VNet peering]: https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[Subscription limits]: https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits
[Connect-ER-VNet]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq