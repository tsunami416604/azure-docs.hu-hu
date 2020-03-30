---
title: 'Azure ExpressRoute: Tervezés magas rendelkezésre állásra'
description: Ez a lap az Azure ExpressRoute használata során magas rendelkezésre állásra vonatkozó architekturális javaslatokat tartalmaz.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 06/28/2019
ms.author: rambala
ms.openlocfilehash: 4c3c6ae5fbdd91e6e44438be7fef2a3a91564a34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076682"
---
# <a name="designing-for-high-availability-with-expressroute"></a>Tervezés a magas rendelkezésre állásérdekében az ExpressRoute segítségével

Az ExpressRoute magas rendelkezésre állásra van tervezve, hogy mobilszolgáltatói szintű magánhálózati kapcsolatot biztosítson a Microsoft erőforrásaihoz. Más szóval a Microsoft-hálózaton belül nincs egyetlen meghibásodási pont az ExpressRoute elérési útban. A rendelkezésre állás maximalizálása érdekében az ExpressRoute-áramkör ügyfél- és szolgáltatószegmensét is meg kell terveznünk a magas rendelkezésre állás érdekében. Ebben a cikkben először nézzük meg a hálózati architektúra szempontjait a robusztus hálózati kapcsolat ExpressRoute használatával történő kiépítéséhez, majd vizsgáljuk meg azokat a finomhangolási funkciókat, amelyek segítenek az ExpressRoute-kapcsolat magas rendelkezésre állásának javításában.


## <a name="architecture-considerations"></a>Építészeti szempontok

Az alábbi ábra bemutatja az ExpressRoute-kapcsolaton keresztül imázs ajánlott módját az ExpressRoute-kapcsolat rendelkezésre állásának maximalizálása érdekében.

 [![1]][1.]

A magas rendelkezésre állás érdekében elengedhetetlen az ExpressRoute-kapcsolat i. Más szóval a redundanciát a helyszíni hálózaton belül kell fenntartania, és nem veszélyeztetheti a redundanciát a szolgáltatóhálózaton belül. A redundancia minimális szinten tartása azt jelenti, hogy elkerüljük a hálózati hibák egypontos átvevőpontját. Miután redundáns teljesítmény és hűtés a hálózati eszközök tovább javítja a magas rendelkezésre állást.

### <a name="first-mile-physical-layer-design-considerations"></a>Az első mérföldes fizikai rétegtervezési szempontok

 Ha egy ExpressRoute-kapcsolat elsődleges és másodlagos kapcsolatát is megszakítja ugyanazon az ügyfél-premises-i berendezésen (CPE), akkor veszélyezteti a helyszíni hálózaton belüli magas rendelkezésre állást. Továbbá, ha az elsődleges és másodlagos kapcsolatokat is a CPE ugyanazon portjain keresztül konfigurálja (vagy a két kapcsolat különböző alfelületek en történő megszüntetésével, vagy a partnerhálózaton belüli két kapcsolat egyesítésével), akkor kényszeríti a partnert hogy a hálózati szegmensmagas elérhetőségét is veszélyeztessék. Ezt a kompromisszumot az alábbi ábra szemlélteti.

[![2]][2.]

Másrészt, ha megszakítja az elsődleges és a másodlagos kapcsolatok egy ExpressRoute-kapcsolatok különböző földrajzi helyeken, akkor lehet, hogy veszélyezteti a hálózati teljesítményét a kapcsolat. Ha a forgalom aktívan elosztása az elsődleges és a másodlagos kapcsolatok, amelyek levannak állítva a különböző földrajzi helyeken, a potenciális jelentős különbség a hálózati késés a két útvonal között azt eredményezné, hogy az optimálistól elmaradó hálózat Teljesítmény. 

A georedundáns tervezési szempontokat lásd: [A vész-helyreállítási tervezése az ExpressRoute-on.][DR]

### <a name="active-active-connections"></a>Aktív-aktív kapcsolatok

A Microsoft-hálózat úgy van beállítva, hogy az ExpressRoute-áramkörök elsődleges és másodlagos kapcsolatait aktív-aktív módban működtesse. Az útvonalhirdetéseken keresztül azonban kényszerítheti az ExpressRoute-áramkör redundáns kapcsolatait, hogy aktív-passzív módban működjenek. Reklám konkrétabb útvonalak és BGP AS elérési út előkerülő a közös technikákat használnak, hogy az egyik utat előnyben részesítik a másikkal szemben.

A magas rendelkezésre állás javítása érdekében ajánlott az ExpressRoute-áramkörök mindkét kapcsolatát aktív-aktív módban működtetni. Ha hagyja, hogy a kapcsolatok aktív-aktív módban működjenek, a Microsoft hálózat a kapcsolatok közötti forgalmat folyamatonként fogja elbírálni.

Az ExpressRoute-kapcsolat elsődleges és másodlagos kapcsolatainak aktív-passzív módban való futtatása azzal a kockázattal jár, hogy az aktív elérési út meghibásodása miatt mindkét kapcsolat meghibásodik. A váltás sikertelensítésének gyakori okai a passzív kapcsolat aktív kezelésének hiánya és a passzív kapcsolathirdetések elavult útvonalai.

Másik lehetőségként egy ExpressRoute-kapcsolat elsődleges és másodlagos kapcsolatainak aktív-aktív módban való futtatása a folyamatok meghibásodásának körülbelül felét eredményezi, és az ExpressRoute-kapcsolat hibája után átirányítják őket. Így az aktív-aktív mód jelentősen javítja a helyreállításhoz képest iményidőt (MTTR).

### <a name="nat-for-microsoft-peering"></a>NAT microsoftos társviszony-létesítéshez 

A Microsoft társviszony-létesítése nyilvános végpontok közötti kommunikációra szolgál. Így a helyszíni magánvégpontok általában hálózati cím fordítása (NATed) nyilvános IP az ügyfél vagy partner hálózaton, mielőtt azok a Microsoft-társviszony-létesítési keresztül kommunikálnak. Feltéve, hogy az elsődleges és a másodlagos kapcsolatokat is aktív-aktív módban használja, ahol és hogyan befolyásolja a Hálózati címcím a helyreállítás gyorsan az egyik ExpressRoute-kapcsolat hibáját. Az alábbi ábrán két különböző NAT-beállítás látható:

[![3]][3]

Az 1., nat a rendszer az ExpressRoute elsődleges és másodlagos kapcsolatai közötti forgalom felosztása után alkalmazza a NAT-t. A NAT állapotalapú követelményeinek teljesítése érdekében független NAT-készleteket használnak az elsődleges és a másodlagos eszközök között, hogy a visszatérési forgalom ugyanarra az éleszközre érkezzen, amelyen keresztül az áramlás kijutott.

A 2. Fontos, hogy a különbséget, hogy a közös NAT-készlet a forgalom felosztása előtt nem jelenti azt, hogy egypontos hiba, ami veszélyezteti a magas rendelkezésre állás.

Az 1. Ezért az összes hibás folyamatot újra létre kell hozni a TCP vagy az alkalmazásréteg által a megfelelő ablakidő-túltöltés után. Ha a NAT-készletek bármelyikét a helyszíni kiszolgálók bármelyikének frontendrendszeri kiszolgálóihoz használják, és ha a megfelelő kapcsolat sikertelen, a helyszíni kiszolgálók nem érhetőek el az Azure-ból, amíg a kapcsolat nem lett javítva.

Míg a 2- es lehetőséggel a NAT elsődleges vagy másodlagos csatlakozási hiba után is elérhető. Ezért maga a hálózati réteg is átirányíthatja a csomagokat, és a hiba után gyorsabb helyreállítást tehet lehetővé. 

> [!NOTE]
> Ha az 1. a kapcsolat megszakad.
> 

## <a name="fine-tuning-features-for-private-peering"></a>Finomhangolási funkciók a privát társviszony-létesítéshez

Ebben a szakaszban tekintse át az opcionális (attól függően, hogy az Azure-központi telepítés és mennyire érzékeny az MTTR) funkciók, amelyek segítenek javítani az ExpressRoute-kapcsolat magas rendelkezésre állását. Pontosabban nézzük át az ExpressRoute virtuális hálózati átjárók zónabarát telepítését és a kétirányú továbbításészlelést (BFD).

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>Rendelkezésre állási zóna, expressroute-alapú virtuális hálózati átjárók

Egy Azure-régióban rendelkezésre állási zóna egy tartalék tartomány és egy frissítési tartomány kombinációja. Ha zónaredundáns Azure IaaS-telepítést választ, konfigurálhatja a zónaredundáns virtuális hálózati átjárókat is, amelyek megszakítják az ExpressRoute privát társviszony-létesítését. További információ: [Zónaredundáns virtuális hálózati átjárók az Azure rendelkezésre állási zónáiban.][zone redundant vgw] A zónaredundáns virtuális hálózati átjáró konfigurálásáról a [Zónaredundáns virtuális hálózati átjáró létrehozása az Azure rendelkezésre állási zónáiban][conf zone redundant vgw]című témakörben található.

### <a name="improving-failure-detection-time"></a>A hibaészlelési idő javítása

ExpressRoute támogatja a BFD privát társviszony-létesítés. A BFD körülbelül 3 percről (alapértelmezett) körülbelül 3 percre (alapértelmezett) csökkenti a Microsoft Enterprise Edge (MSE-k) és a helyszíni bgp-szomszédok közötti Layer 2 hálózaton a hibaészlelési időt. A gyors hibaészlelési idő segít a hiba helyreállításának felgyorsítása. További információ: [BfD konfigurálása az ExpressRoute-on keresztül][BFD]című témakörben olvashat.

## <a name="next-steps"></a>További lépések

Ebben a cikkben azt tárgyalta, hogyan tervezhet egy ExpressRoute-kapcsolat magas rendelkezésre állása érdekében. Az ExpressRoute-kapcsolatlétesítési pont egy földrajzi helyre van rögzítve, ezért a teljes helyet érintő katasztrofális hiba hatással lehet. 

A georedundáns hálózati kapcsolat létrehozásának tervezési szempontokat a Microsoft gerinchálózatához, amely képes ellenállni a katasztrofális hibáknak, amelyek egy teljes régiót érintenek, olvassa el a [Vészhelyreállítás tervezése expressroute-alapú privát társviszony-létesítéssel.][DR]

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "Az ExpressRoute használatával történő csatlakozás ajánlott módja"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "Az utolsó kilométerek optimálistól elmaradó csatlakoztatása"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "NAT-beállítás"


<!--Link References-->
[zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways
[conf zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[DR]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering




