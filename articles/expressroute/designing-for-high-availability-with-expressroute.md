---
title: Az Azure ExpressRoute használatával magas rendelkezésre állás tervezése |} A Microsoft Docs
description: Ezen a lapon ajánlásokkal architekturális magas rendelkezésre állás érdekében az Azure ExpressRoute használata során.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 06/28/2019
ms.author: rambala
ms.openlocfilehash: 4984b30daf6170873cad9472bfed2d879af57efe
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466650"
---
# <a name="designing-for-high-availability-with-expressroute"></a>Az expressroute-tal magas rendelkezésre állás tervezése

Magas rendelkezésre állású szolgáltató Microsoft erőforrások szintű magánhálózati kapcsolatot biztosít ExpressRoute tervezték. Más szóval nincs nem az ExpressRoute elérési úton belül a Microsoft hálózati meghibásodási pont. A rendelkezésre állás maximalizálása érdekében, hogy az ügyfél és a service provider szegmens az ExpressRoute-kapcsolatcsoport kell is lehet tervezésnek magas rendelkezésre állás érdekében. A jelen cikk, először nézzük megvizsgáljuk a hálózati architektúra szempontjai egy ExpressRoute-tal robusztus hálózati kapcsolat létrehozásához, majd vizsgáljuk meg, a finomhangolási funkciókat, amelyek segítenek az ExpressRoute-kapcsolatcsoport a magas rendelkezésre állásának javítása érdekében.


## <a name="architecture-considerations"></a>Architektúra szempontjai

Az alábbi ábra az ajánlott módszer a csatlakozás ExpressRoute-kapcsolatcsoport segítségével a lehető legnagyobb ExpressRoute-kapcsolatcsoport rendelkezésre állását.

 [![1]][1]

A magas rendelkezésre állás érdekében elengedhetetlen a redundancia, az ExpressRoute-kapcsolatcsoport a teljes körű hálózaton karbantartása. Más szóval, a helyszíni hálózaton belüli redundancia biztosítani kell, és nem veszélyezteti a redundancia szolgáltatás szolgáltató hálózaton belül. Kerülje a hibaérzékeny pont hálózati hibák fenntartja a redundanciát, a minimális jelenti. Redundáns tápellátás kapcsolatban, és a hálózati eszközök tovább fog hűtéssel javíthatja a magas rendelkezésre állás.

### <a name="first-mile-physical-layer-design-considerations"></a>Első mérföld fizikai réteg kialakításával kapcsolatos szempontok

 Ha mind az elsődleges és másodlagos kapcsolatok, egy ExpressRoute-Kapcsolatcsoportok a az azonos ügyfél helyi berendezések (CPE), a helyszíni hálózaton belüli, Ön veszélyeztetése a magas rendelkezésre állás. Emellett ha az elsődleges és másodlagos kapcsolatok keresztül ugyanazt a portot egy (vagy lezárja a két különböző alkapcsolatainak kapcsolatok vagy egyesítés a két kapcsolat a partneri hálózaton belül) CPE konfigurál, kényszeríti a partner magas rendelkezésre állású, valamint a hálózati szegmensen kényszerülnie. A biztonsági sérülés az alábbi ábra mutatja be.

[![2]][2]

Másrészről Ha az elsődleges és másodlagos kapcsolatainak egy ExpressRoute-Kapcsolatcsoportok földrajzilag különböző helyeken, majd, sikerült kell veszélyeztetése a hálózati teljesítmény, a kapcsolat. Ha forgalom aktívan kell osztani az elsődleges és másodlagos kapcsolatok, amely földrajzilag különböző helyeken vannak befejeződött, a két útvonalon lehetséges jelentős különbség a hálózati késés optimálisnál hálózati eredményez teljesítmény. 

Georedundáns megtervezésével, lásd: [tervezése vészhelyreállításhoz az expressroute-tal][DR].

### <a name="active-active-connections"></a>Aktív – aktív kapcsolatok

A Microsoft hálózati az aktív – aktív üzemmód az ExpressRoute-Kapcsolatcsoportok elsődleges és másodlagos kapcsolatok működésre van konfigurálva. Azonban a bejövő útvonalhirdetést keresztül kényszerítheti a redundáns kapcsolatokat, az ExpressRoute-kapcsolatcsoport, aktív-passzív módban. Hirdetési pontosabb útvonalak és BGP path előtag-Beillesztés vannak a gyakran használt módszerek, hogy a másik preferált egy elérési utat.

Magas rendelkezésre állás javítása érdekében javasoljuk, hogy a kapcsolatok egy ExpressRoute-kapcsolatcsoport aktív-aktív módban működik. Ha hagyja, hogy a kapcsolat aktív-aktív módban működik, a Microsoft hálózati fog terheléselosztásához a forgalom a kapcsolatokat, folyamat-alapon.

Futó ExpressRoute-Kapcsolatcsoportok elsődleges és másodlagos kapcsolatok aktív-passzív mód face aktív elérési hiba után sikertelen a kapcsolatok kockázatát. A Váltás hiba gyakori okok: aktív és kezelését, valamint a passzív kapcsolat elavult útvonalak meghirdetése passzív kapcsolat hiánya.

Azt is megteheti, aktív-aktív üzemmódban működő ExpressRoute-Kapcsolatcsoportok elsődleges és másodlagos kapcsolatok eredményezi, csak a körülbelül felét folyamatok sikertelen, és a kezdeti átirányítva, a következő ExpressRoute-kapcsolat hibát. Ebből kifolyólag aktív – aktív üzemmód jelentősen javítja a fordított időt, helyreállítása (MTTR).

### <a name="nat-for-microsoft-peering"></a>A Microsoft társviszony-létesítés NAT 

Microsoft társviszony-létesítés nyilvános végpontok közötti kommunikációra szolgál. Ezért általában a helyszíni privát végpontok hálózati cím lefordított (gelt) rendelkező, az ügyfél vagy partner Networkben a nyilvános IP-cím előtt a Microsoft társviszony-létesítésen keresztül kommunikálnak. Ha az elsődleges és másodlagos kapcsolatok aktív-aktív módban használja, hol és hogyan, NAT hatással van milyen gyorsan helyreállítani az ExpressRoute-kapcsolatok valamelyik hiba után. Két különböző NAT-beállítások a következő ábra mutatja be:

[![3]][3]

Az 1. lehetőség, a NAT az expressroute az elsődleges és másodlagos kapcsolatok közötti forgalom felosztása után alkalmazza. Független NAT-készletek NAT állapot-nyilvántartó követelményeinek, szolgálnak az elsődleges és másodlagos eszközök között, hogy az azonos edge-eszközön, amelyen keresztül a flow egressed érkezik, a visszatérő forgalmat.

A 2. lehetőség, a közös NAT-készletet a forgalom az expressroute az elsődleges és másodlagos kapcsolatok közötti felosztásával előtt használják. Fontos, hogy nem jelenti azt, a közös NAT-készletet a forgalom felosztása előtt különbséget tenni rendszerkritikus meghibásodási ezáltal feltörheti a magas rendelkezésre állás egyetlen pont bemutatása.

Az 1. lehetőség – a következő ExpressRoute-kapcsolat hibát a képes elérni a megfelelő NAT-készlet nem működik. Ezért a hibás folyamatok kell lennie az összes újbóli létrehozása vagy a TCP vagy a megfelelő ablak időtúllépést követően alkalmazásrétegre. Ha vagy a NAT-készletek előtéri használt a helyszíni kiszolgálók egyikén sincs, és ha a megfelelő csatlakozási meghibásodik, a helyszíni kiszolgálók nem érhetők el az Azure-ból amíg a kapcsolat nem oldódik.

Mivel az a 2. lehetőség, a NAT érhető el elsődleges vagy másodlagos kapcsolati meghibásodás után is. Emiatt a hálózati réteg maga is irányítsa át a hiba a következő csomagok és a Súgó gyorsabb helyreállítás. 

> [!NOTE]
> Ha 1 (független NAT-készletek az elsődleges és másodlagos ExpressRoute-kapcsolatok) a NAT beállítást használja, és a NAT-címkészlet egyik IP-címét, egy helyszíni kiszolgálóhoz portját, a kiszolgáló nem lehet az expressroute-on keresztül elérhető kapcsolatcsoport-Ha a megfelelő csatlakozás sikertelen lesz.
> 

## <a name="fine-tuning-features-for-private-peering"></a>A privát társviszony-létesítés finomhangolási funkciókat

Ebben a szakaszban mondja el (az Azure-alapú, és hogyan bizalmas Ön az MTTR) függően nem kötelező felülvizsgálati szolgáltatásokat, az ExpressRoute-kapcsolatcsoport magas rendelkezésre állásának javítása érdekében. Pontosabban tekintsük át az ExpressRoute virtuális hálózati átjárók és a kétirányú továbbítás észlelése (BFD) zóna együttműködő telepítését.

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>Rendelkezésre állási zónában tisztában az ExpressRoute virtuális hálózati átjárók

Egy rendelkezésre állási zónában az Azure-régióban a tartalék tartomány és a egy frissítési tartományt. Ha úgy dönt, a zónaredundáns Azure IaaS-telepítéshez, is érdemes leállítani, ExpressRoute privát társviszony-létesítés zónaredundáns virtuális hálózati átjárók konfigurálása. További tudnivalókért lásd: [kapcsolatos zónaredundáns virtuális hálózati átjárók az Azure-beli rendelkezésre állási zónák][zone redundant vgw]. To configure zone-redundant virtual network gateway, see [Create a zone-redundant virtual network gateway in Azure Availability Zones][conf zone redundant vgw].

### <a name="improving-failure-detection-time"></a>Észlelés ideje hiba javítása

Az ExpressRoute privát társviszony-létesítésen keresztül BFD támogatja. BFD csökkenti a hiba észlelés időpontja között a Microsoft vállalati peremhálózati (Msee) és a BGP-szomszédok helyszíni oldalán a 2. rétegbeli hálózaton körülbelül 3 perc (alapértelmezett) kisebb, mint egy másik. Gyors hiba észlelés ideje segít a hiba utáni helyreállítás hastening. További tudnivalókért lásd: [BFD konfigurálása expressroute-on keresztül][BFD].

## <a name="next-steps"></a>További lépések

Ebben a cikkben már volt szó hogyan tervezhető egy ExpressRoute-kapcsolatcsoport kapcsolat magas rendelkezésre állását. Egy ExpressRoute-kapcsolatcsoport társviszony-létesítési pont rögzítve van egy földrajzi helyet, és ezért sikerült negatív hatással lehet Katasztrofális hiba, amely hatással van a teljes helyre. 

Tervezési megfontolások a Microsoft gerinchálózatán keresztül, amely képes elviselni a katasztrofális hibák, amely egy teljes régióra hatással, georedundáns hálózati kapcsolat létrehozásához lásd: [vész-helyreállítási az ExpressRoute privát társviszony-létesítésitervezése][DR].

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "ajánlott módja a csatlakozás ExpressRoute használatával"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "Suboptimal mérföld utolsó kapcsolat"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "NAT-beállítások"


<!--Link References-->
[zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways
[conf zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[DR]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering




