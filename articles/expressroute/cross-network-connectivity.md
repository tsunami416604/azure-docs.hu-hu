---
title: Azure-beli hálózatközi kapcsolat
description: Ez a lap egy alkalmazás-forgatókönyv a hálózatok közötti kapcsolat és az Azure hálózati szolgáltatásokon alapuló megoldás.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 48ec26cc98310dfeb61aa17018c940b431cfbcee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644256"
---
# <a name="cross-network-connectivity"></a>Hálózatok közötti kapcsolatok

A Fabrikam Inc. nagy fizikai jelenléttel és Azure-telepítéssel rendelkezik az USA keleti részén. A Fabrikam háttérkapcsolattal rendelkezik a helyszíni és az Azure-telepítések között az ExpressRoute-on keresztül. Hasonlóképpen a Contoso Ltd. is rendelkezik jelenléttel és Azure-telepítéssel az USA nyugati részén. A Contoso háttérkapcsolattal rendelkezik a helyszíni és az Azure-telepítések között az ExpressRoute-on keresztül.  

A Fabrikam Inc. felvásárolja a Contoso Kft.-t. Az egyesülést követően a Fabrikam össze akarja kapcsolni a hálózatokat. Az alábbi ábra a forgatókönyvet mutatja be:

 [![1]][1.]

A fenti ábra közepén lévő szaggatott nyilak jelzik a kívánt hálózati összeköttetéseket. Pontosabban három típusú keresztkapcsolat szükséges: 1) Fabrikam és Contoso virtuális hálózatok keresztcsatlakozás, 2) Cross regionális helyszíni és virtuális hálózatok kereszt csatlakozik (azaz összekötő Fabrikam helyszíni hálózat Contoso Virtuális hálózat és összekötő Contoso helyszíni hálózat Fabrikam VNet), és 3) Fabrikam és Contoso helyszíni hálózati cross connect. 

Az alábbi táblázat a Contoso Ltd. ExpressRoute-jának az egyesülés előtti privát társviszony-létesítésének útvonaltábláját mutatja be.

[![2]][2.]

Az alábbi táblázat egy virtuális gép hatékony útvonalait mutatja be a Contoso-előfizetésben az egyesülés előtt. A tábla szerint a virtuális hálózat virtuális gépe tisztában van a virtuális hálózat címterével és a helyszíni Contoso-hálózattal, az alapértelmezetteken kívül. 

[![4]][4.]

Az alábbi táblázat a Fabrikam Inc. ExpressRoute-jának az egyesülés előtti privát társviszony-létesítésének útvonaltábláját mutatja be.

[![3]][3.]

Az alábbi táblázat egy virtuális gép hatékony útvonalait mutatja be a Fabrikam-előfizetésben, az egyesülés előtt. A tábla szerint a virtuális hálózat virtuális gépe ismeri a virtuális hálózat címterét és a Fabrikam helyszíni hálózatot, az alapértelmezetteken kívül.

[![5]][5]

Ebben a cikkben lépésről lépésre haladjunk, és vitassuk meg, hogyan érheti el a kívánt keresztkapcsolatokat a következő Azure-hálózati szolgáltatások használatával:

* [Virtuális hálózati társviszony-létesítés][Virtual network peering] 
* [Virtuális hálózati ExpressRoute-kapcsolat][connection]
* [Globális elérés][Global Reach] 

## <a name="cross-connecting-vnets"></a>Csatlakozó virtuális hálózatok

Virtuális hálózati társviszony-létesítés (VNet társviszony-létesítés) biztosítja a legoptimálisabb és a legjobb hálózati teljesítményt, ha két virtuális hálózat csatlakoztatása. A virtuális hálózatok társviszony-létesítése két virtuális hálózatot támogat mind ugyanabban az Azure-régióban (közismert nevén virtuális hálózatok társviszony-létesítése) és két különböző Azure-régióban (közismert nevén globális virtuális hálózatok társviszony-létesítésében). 

Konfigurálja a globális virtuális hálózatok társviszony-létesítését a Contoso és a Fabrikam Azure-előfizetések virtuális hálózatai között. A virtuális hálózatok közötti virtuális hálózati társviszony-létesítés létrehozásáról a [Virtuális hálózati társviszony-létesítésről][Configure VNet peering] szóló cikk ben olvashat.

Az alábbi képen a globális virtuális kapcsolatlétesítés konfigurálása után a hálózati architektúra látható.

[![6]][6]

Az alábbi táblázat a Contoso-előfizetés virtuális gép által ismert útvonalakat mutatja be. Ügyeljen a táblázat utolsó bejegyzésére. Ez a bejegyzés a virtuális hálózatok keresztcsatlakozásának eredménye.

[![7]][7]

Az alábbi táblázat a Fabrikam-előfizetés virtuális gépáltal ismert útvonalakat mutatja be. Ügyeljen a táblázat utolsó bejegyzésére. Ez a bejegyzés a virtuális hálózatok keresztcsatlakozásának eredménye.

[![8]][8]

A virtuális hálózatok társviszony-létesítése közvetlenül két virtuális hálózatot kapcsol össze (lásd: a *vNetGlobalPeering* bejegyzéshez nincs következő ugrás a fenti két táblában)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Virtuális hálózatok és a helyszíni hálózatok közötti kapcsolat

Egy ExpressRoute-áramkört több virtuális hálózathoz is csatlakoztathatunk. Tekintse meg [az Előfizetési és szolgáltatáskorlátokat][Subscription limits] az ExpressRoute-kapcsolathoz csatlakoztatható virtuális hálózatok maximális számára vonatkozóan. 

Csatlakoztassuk a Fabrikam ExpressRoute-áramkört a Contoso-előfizetésvirtuális hálózathoz, és hasonlóképpen a Contoso ExpressRoute-áramkört a Fabrikam-előfizetésvirtuális hálózathoz a virtuális hálózatok és a helyszíni hálózatok közötti keresztkapcsolat engedélyezéséhez. Ahhoz, hogy egy virtuális hálózatot egy másik előfizetésben lévő ExpressRoute-kapcsolathoz csatlakoztasson, létre kell hoznunk és használnunk kell egy engedélyezést.  Lásd a cikket: [Virtuális hálózat csatlakoztatása ExpressRoute-kapcsolattal.][Connect-ER-VNet]

Az alábbi képen a hálózati architektúra látható az ExpressRoute keresztkapcsolatának konfigurálása után a virtuális hálózatokkal.

[![9]][9]

Az alábbi táblázat a Contoso Ltd. ExpressRoute-jának privát társviszony-létesítési útvonaltábláját mutatja be, miután a virtuális hálózatokat az ExpressRoute-on keresztül egymásba kötötték a helyszíni hálózatokkal. Tekintse meg, hogy az útvonaltábla mindkét virtuális hálózathoz tartozó útvonalakat rendelkezik-e.

[![10]][10]

Az alábbi táblázat a Fabrikam Inc. ExpressRoute privát társviszony-létesítésének útvonaltábláját mutatja be, miután a virtuális hálózatokat az ExpressRoute-on keresztül egymásba kötötték a helyszíni hálózatokkal. Tekintse meg, hogy az útvonaltábla mindkét virtuális hálózathoz tartozó útvonalakat rendelkezik-e.

[![11]][11]

Az alábbi táblázat a Contoso-előfizetés virtuális gép által ismert útvonalakat mutatja be. Ügyeljen a tábla *virtuális hálózati átjáróbejegyzéseire.* A virtuális gép mindkét helyszíni hálózat útvonalait látja.

[![12]][12]

Az alábbi táblázat a Fabrikam-előfizetés virtuális gépáltal ismert útvonalakat mutatja be. Ügyeljen a tábla *virtuális hálózati átjáróbejegyzéseire.* A virtuális gép mindkét helyszíni hálózat útvonalait látja.

[![13]][13]

>[!NOTE]
>A Fabrikam és/vagy a Contoso-előfizetésekben a megfelelő központi virtuális hálózathoz is rendelkezhet küllővirtuális hálózatokat (a hub és a küllős kialakítás nem jelenik meg a cikkben szereplő architektúradiagramokban). A központi virtuális hálózat expressroute-i átjárók közötti keresztkapcsolatok lehetővé teszik a kelet és a nyugati csomópontok és küllők közötti kommunikációt is.
>

## <a name="cross-connecting-on-premises-networks"></a>A helyszíni hálózatok összekapcsolása

Az ExpressRoute Globális elérés kapcsolatot biztosít a különböző ExpressRoute-áramkörökhöz kapcsolódó helyszíni hálózatok között. Konfiguráljuk a Globális elérést a Contoso és a Fabrikam ExpressRoute-áramkörök között. Mivel az ExpressRoute-áramkörök különböző előfizetésekben vannak, létre kell hoznunk és használnunk kell egy engedélyezést. Tekintse meg [az ExpressRoute globális elérési][Configure Global Reach] cikkét a lépésről lépésre történő útmutatásért.

Az alábbi képen a globális elérés konfigurálása után látható a hálózati architektúra.

[![14]][14]

Az alábbi táblázat a Contoso Ltd. ExpressRoute-jának privát társviszony-létesítési útvonaltábláját mutatja be a Globális elérés konfigurálása után. Tekintse meg, hogy az útvonaltábla mindkét helyszíni hálózathoz tartozó útvonalakat is rendelkezik.See that the route table has associated to both on-premises networks. 

[![15]][15]

Az alábbi táblázat a Fabrikam Inc. ExpressRoute privát társviszony-létesítésének útvonaltábláját mutatja be a Globális elérés konfigurálása után. Tekintse meg, hogy az útvonaltábla mindkét helyszíni hálózathoz tartozó útvonalakat is rendelkezik.See that the route table has associated to both on-premises networks.

[![16]][16]

## <a name="next-steps"></a>További lépések

A [virtuális hálózattal kapcsolatos gyakori kérdéseket][VNet-FAQ]a virtuális hálózattal és a virtuális hálózattárs-társviszony-létesítésével kapcsolatos további kérdésekről. Az ExpressRoute-tal és a virtuális hálózati kapcsolattal kapcsolatos további kérdésekért tekintse meg az [ExpressRoute gyakori kérdéseit.][ER-FAQ]

A Global Reach országonként/régiónként kerül bevezetésre. Ha meg szeretné tudni, hogy a Globális elérés elérhető-e a kívánt országokban/régiókban, olvassa el az [ExpressRoute globális elérési útját.][Global Reach]

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "Az alkalmazás forgatókönyve"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "Contoso ExpressRoute útvonaltábla az egyesülés előtt"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "Fabrikam ExpressRoute útvonaltábla az egyesülés előtt"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "Contoso VM útvonalak egyesülés előtt"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "Fabrikam VM útvonalak egyesülés előtt"
[6]: ./media/cross-network-connectivity/vnet-peering.png "Az architektúra a virtuális hálózatok társviszony-létesítése után"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "Contoso virtuális gép útvonalai a virtuális hálózat társviszony-létesítése után"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "Fabrikam virtuális gép útvonalai a virtuális hálózatok társviszony-létesítése után"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "Az Architektúra az ExpressRoutes keresztkapcsolatután"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "Contoso ExpressRoute útvonaltábla az ExR és a virtuális hálózatok keresztcsatlakozása után"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "Fabrikam ExpressRoute útvonaltábla az ExR és a Virtuális hálózatok keresztcsatlakozása után"
[12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "Contoso virtuális gép útvonalak keresztcsatlakozás után ExR és virtuális hálózatok"
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "Fabrikam VM útvonalak után keresztösszekötő ExR és VNets"
[14]: ./media/cross-network-connectivity/globalreach.png "Az architektúra a Global Reach konfigurálása után"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "Contoso ExpressRoute útvonaltábla globális elérés után"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "Fabrikam ExpressRoute útvonaltábla a globális elérés után"

<!--Link References-->
[Virtual network peering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[connection]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure VNet peering]: https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[Subscription limits]: https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits
[Connect-ER-VNet]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq