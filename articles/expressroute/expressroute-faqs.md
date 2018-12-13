---
title: Gyakori kérdések – Azure ExpressRoute |} A Microsoft Docs
description: Az ExpressRoute – gyakori kérdések az információ a támogatott Azure-szolgáltatások, a költségek, adatok és kapcsolatok, SLA-t, szolgáltatók és a helyek, sávszélesség és további technikai részleteket tartalmazza.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: 0621c2d1632dead41463810b9e71ebf0404a8cbe
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53077749"
---
# <a name="expressroute-faq"></a>ExpressRoute – Gyakori kérdések

## <a name="what-is-expressroute"></a>Mi az az ExpressRoute?

Az ExpressRoute egy Azure-szolgáltatás, amely lehetővé teszi, hogy között Microsoft-adatközpontok és infrastruktúra, amely a helyszínen vagy egy közös elhelyezési létesítményből csatlakozó privát kapcsolatok hozhatók létre. Az ExpressRoute-kapcsolatok nem a nyilvános interneten keresztül, és magasabb szintű biztonságra, megbízhatóságra és a kisebb a késésük, mint a szokásos internetkapcsolatoknál megbízhatóbbak kínálnak az interneten keresztül.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Mik azok az ExpressRoute- és magánhálózati kapcsolatok használatának előnyei?

Az ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át. A szokványos internetkapcsolatoknál nagyobb biztonságra, megbízhatóságra és sebessége, alacsonyabb és következetes késése, mint az interneten keresztüli általános kapcsolatok. Bizonyos esetekben az ExpressRoute-kapcsolatok használata közötti adatátvitelhez a helyszíni eszközök és az Azure jelentős árelőnyökkel is.

### <a name="where-is-the-service-available"></a>Hol érhető el a szolgáltatást?

Szolgáltatáskeresés és rendelkezésre állás érdekében ezt oldal jelenik meg: [ExpressRoute-partnerek és helyek](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Hogyan használhatom az ExpressRoute csatlakozni a Microsoft, ha az ExpressRoute-szolgáltató partnereink egyikével partneri nem rendelkezem?

Válassza ki a regionális szolgáltató, és Ethernet-kapcsolatok megnyitja a támogatott exchange egyik kapcsolatszolgáltatói helyek. A Microsoft a szolgáltató helyen majd társviszonyt. Ellenőrizze az utolsó szakaszában [ExpressRoute-partnerek és helyek](expressroute-locations.md) megtekintéséhez, ha a szolgáltató megtalálható-e az exchange-helyeken. A szolgáltató szeretne csatlakozni az Azure ExpressRoute-kapcsolatcsoport majd rendezheti.

### <a name="how-much-does-expressroute-cost"></a>Mennyibe az ExpressRoute szolgáltatás?

Ellenőrizze [díjszabás](https://azure.microsoft.com/pricing/details/expressroute/) a díjszabásról.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Az adott sávszélesség ExpressRoute-kapcsolatcsoport kell fizetnem, ha a VPN-kapcsolatot a saját hálózati szolgáltató vásárolhatok rendelkezik kell ugyanazt a sebességet?

Nem. A szolgáltató egy VPN-kapcsolat bármely sebességű vásárolhatnak. Az Azure-bA a kapcsolat azonban korlátozódik vásárolhat ExpressRoute-kapcsolatcsoport sávszélességét.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-necessary"></a>Az adott sávszélesség ExpressRoute-kapcsolatcsoport kell fizetnem, ha kell a nagyobb sebesség, szükség esetén akár eszközcsomag lehetővé teszi?

Igen. Az ExpressRoute-Kapcsolatcsoportok legfeljebb kétszer a sávszélességre vonatkozó korlátját kétszereséig további költségek nélkül is. engedélyezésére vannak konfigurálva. Ellenőrizze a szolgáltató megtekintéséhez, hogy támogatja-e ezt a funkciót.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Használhatom ugyanazt a magánhálózati kapcsolatot rendelkező virtuális hálózatot és más Azure-szolgáltatások egyszerre?

Igen. Egy ExpressRoute-kapcsolatcsoportot, egyszer állítsa be, egy virtuális hálózaton belüli szolgáltatások és más Azure-szolgáltatások eléréséhez egyszerre teszi lehetővé. Csatlakozás virtuális hálózatokhoz keresztül a magánhálózati társviszony-létesítési útvonal és más szolgáltatások keresztül a Microsoft társviszony-létesítési útvonalra.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>Kínál az ExpressRoute szolgáltatói szerződés (SLA)?

További információ: a [ExpressRoute SLA](https://azure.microsoft.com/support/legal/sla/) lap.

## <a name="supported-services"></a>Támogatott szolgáltatások

Az ExpressRoute támogatja [három útválasztási tartományt](expressroute-circuit-peerings.md) különféle típusú szolgáltatások.

### <a name="private-peering"></a>Magánhálózati társviszony-létesítés

* Virtuális hálózatok, beleértve az összes virtuális gép és a cloud services

### <a name="public-peering"></a>Nyilvános társviszony-létesítés

>[!NOTE]
>Nyilvános társviszony-létesítés le lett tiltva az új ExpressRoute-kapcsolatcsoportot. Azure-szolgáltatások érhetők el a Microsoft társviszony-létesítés.
>

* Power BI
* Dynamics 365 for Finance and Operations (korábbi nevén Dynamics AX Online-hoz)
* Az Azure-szolgáltatások többsége támogatottak. Tekintse meg közvetlenül a kívánt ellenőrizheti a támogatási szolgáltatással.<br><br>
  **A következő szolgáltatások nem támogatottak**:
    * Tartalomkézbesítési hálózat (CDN)
    * Multi-Factor Authentication
    * Traffic Manager

### <a name="microsoft-peering"></a>Microsoft társviszony-létesítés

* [Office 365](https://aka.ms/ExpressRouteOffice365)
* Dynamics 365 
* Power BI
* Azure Active Directory
* [Az Azure DevOps](https://blogs.msdn.microsoft.com/devops/2018/10/23/expressroute-for-azure-devops/) (globális Azure-szolgáltatások közösségi)
* Az Azure-szolgáltatások többsége támogatottak. Tekintse meg közvetlenül a kívánt ellenőrizheti a támogatási szolgáltatással.<br><br>**A következő szolgáltatások nem támogatottak**:
    * Tartalomkézbesítési hálózat (CDN)
    * Multi-Factor Authentication
    * Traffic Manager

## <a name="data-and-connections"></a>Adatok és kapcsolatok

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Az ExpressRoute-tal is átvinni adatok mennyisége korlátozva van?

A Microsoft nem állítson be kényszerített korlátot a adatátvitel mennyiségét. Tekintse meg [díjszabás](https://azure.microsoft.com/pricing/details/expressroute/) információk a sávszélesség-használati díjat.

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>Milyen kapcsolati sebességet támogat az ExpressRoute?

Támogatott sávszélesség-ajánlatok:

50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps

### <a name="which-service-providers-are-available"></a>Melyik szolgáltatók érhetők el?

Lásd: [ExpressRoute-partnerek és helyek](expressroute-locations.md) szolgáltatók és helyek listáját.

## <a name="technical-details"></a>Technikai részletek

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Mik azok a saját helyszíni hely csatlakoztatása az Azure műszaki követelményei?

Lásd: [ExpressRoute előfeltételeit ismertető lap](expressroute-prerequisites.md) követelményeket.

### <a name="are-connections-to-expressroute-redundant"></a>Azok kapcsolatok expressroute redundáns?

Igen. Mindegyik ExpressRoute-kapcsolatcsoport redundáns párjai közötti kapcsolatok magas rendelkezésre állás konfigurálva van.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Elveszítek kapcsolat, ha az egyik saját ExpressRoute-kapcsolatok nem?

Ha egyik közötti kapcsolat nem sikerül nem megszakad a kapcsolat. Redundáns kapcsolat támogatja a terhelés, a hálózat és a magas rendelkezésre állás az ExpressRoute-kapcsolatcsoport érhető el. Emellett egy másik társviszony-létesítési helyszínen kapcsolatcsoport-szintű rugalmasság elérése érdekében a kapcsolatcsoport hozhat létre.

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Hogyan biztosítja a magas rendelkezésre állás az expressroute-hoz csatlakoztatott virtuális hálózaton?

Magas rendelkezésre állást érhet el, a virtuális hálózathoz való csatlakozás ExpressRoute-Kapcsolatcsoportok társviszony-létesítési különböző helyeken (például, Szingapúr, szingapúr2). Ha egy ExpressRoute-kapcsolatcsoport leáll, kapcsolat feladatátvételt hajt végre egy másik ExpressRoute-kapcsolatcsoporthoz. Alapértelmezés szerint a virtuális hálózatot elhagyó forgalomra legyen irányítva a egyenlő költség több útvonalas útválasztás (ECMP) alapú. Kapcsolat súly segítségével egy kapcsolatcsoport inkább egy másikba. További információkért lásd: [ExpressRoute-útválasztás optimalizálása](expressroute-optimize-routing.md).

### <a name="onep2plink"></a>Ha nem vagyok, felhőalapú adatcsere közös elhelyezésű, és a szolgáltató a pontok közötti kapcsolatot biztosít, kell order két fizikai kapcsolattal a helyszíni hálózat és a Microsoft között?

Ha a szolgáltató hozhatnak létre két virtuális Ethernet-kapcsolatcsoportokat az fizikai-kapcsolaton keresztül, csak azt kell egy fizikai kapcsolattal. A fizikai (például egy optikai fiber) kapcsolat megszakad a réteg 1 (1) eszköz (lásd a képen). A két virtuális Ethernet-kapcsolatcsoportokat eltérő VLAN-azonosítót, egyet az elsődleges kapcsolatcsoportot, és a másodlagos egy megfelelő címkékkel. A külső 802.1Q Ethernet-fejléc el azokat a VLAN-azonosítót. A belső 802.1Q Ethernet-fejléc (nem látható) le van képezve egy adott [ExpressRoute útválasztási tartomány](expressroute-circuit-peerings.md).

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Ki lehet terjeszteni a saját virtuális helyi hálózatok egyike az Azure ExpressRoute használatával?

Nem. Réteg 2 kapcsolat bővítmények nem támogatott az Azure-bA.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>Használhatok több ExpressRoute-kapcsolatcsoport az előfizetésemben?

Igen. Egynél több ExpressRoute-kapcsolatcsoportot is rendelkezik az előfizetésében. Az alapértelmezett érték 10. Szükség esetén kapcsolatot is a Microsoft Support a korlát növelését.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Lehetnek-e az ExpressRoute-Kapcsolatcsoportok eltérő szolgáltatóktól származó?

Igen. Az ExpressRoute-Kapcsolatcsoportok számos szolgáltatókkal rendelkezhet. Egyes ExpressRoute-kapcsolatcsoport társítva egy szolgáltatót. 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-for-example-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>Két ExpressRoute-társviszonylétesítési helyszínek a az azonos metro például Szingapúr, szingapúr2 látható. Melyik társviszony-létesítési helyszínen válasszam saját ExpressRoute-kapcsolatcsoportot létrehozni?
Ha a szolgáltató biztosít ExpressRoute mindkét helyen, a szolgáltatónál dolgozik, és válassza ki mindkét helyen állíthatja be az ExpressRoute. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>Használhatok több ExpressRoute-kapcsolatcsoporttal a az azonos metro? Is lehet hivatkozni őket az ugyanazon a virtuális hálózaton?

Igen. Használhat több ExpressRoute-Kapcsolatcsoportok azonos vagy eltérő szolgáltatókkal. Ha a metro rendelkezik több ExpressRoute-társviszony-létesítési helyszínek, és a Kapcsolatcsoportok hozhatók létre, különböző társviszony-létesítési helyszínek, az azonos virtuális hálózatban csatolhatja őket. A Kapcsolatcsoportok társviszony-létesítési ugyanott lehet létrehozni, ha nem kapcsolja a hálózatokat az azonos virtuális hálózatban. Minden hely neve az Azure Portalon vagy a PowerShell vagy a parancssori felületen API társviszony-létesítési helyszínen jelöli. Például válassza ki a társviszony-létesítési helyszínek "Szingapúr" és "Szingapúr2", és a Kapcsolatcsoportok csatlakozzon az egyes ugyanahhoz a virtuális hálózathoz. 

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Hogyan létesíthetek kapcsolatot a virtuális hálózatok csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz

Az alapszintű lépések a következők:

* Egy ExpressRoute-kapcsolatcsoportot létrehozni, és engedélyezze a szolgáltató rendelkezik.
* Ön vagy a szolgáltató kell konfigurálnia a BGP társviszony-létesítés (s).
* A virtuális hálózat csatolása az ExpressRoute-kapcsolatcsoporthoz.

További információkért lásd: [Kapcsolatcsoportok kiépítésével és állapotával kapcsolatos az ExpressRoute-munkafolyamatokat](expressroute-workflows.md).

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Vannak-e kapcsolat határai saját ExpressRoute-kapcsolatcsoportot?

Igen. A [ExpressRoute-partnerek és helyek](expressroute-locations.md) cikk kapcsolat határain áttekintést nyújt az ExpressRoute-kapcsolatcsoport. Kapcsolat ExpressRoute-kapcsolatcsoport egy geopolitikai régióban korlátozódik. Az ExpressRoute prémium funkció engedélyezésével geopolitikai régiók közötti kapcsolat bővíthetők ki.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Lehet hivatkozni lehet az ExpressRoute-kapcsolatcsoport több mint egy virtuális hálózat?

Igen. Legfeljebb 10 virtuális hálózati kapcsolatok standard ExpressRoute-kapcsolatcsoportot, és legfeljebb 100 lehet egy [prémium szintű ExpressRoute-kapcsolatcsoport](#expressroute-premium). 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Több Azure-virtuális hálózatokat tartalmazó előfizetéssel rendelkezem. Egyetlen ExpressRoute-kapcsolatcsoporthoz különböző előfizetésben található virtuális hálózat kapcsolható össze?

Igen. Legfeljebb 10 más Azure-előfizetések egy ExpressRoute-kapcsolatcsoport használatával engedélyezhető. Ez a korlátozás az ExpressRoute prémium funkció engedélyezésével növelhető.

További információkért lásd: [ExpressRoute-kapcsolatcsoport megosztása több előfizetés között](expressroute-howto-linkvnet-arm.md).

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Különböző Azure Active Directory-bérlők vagy a nagyvállalati szerződés regisztrációk társított több Azure-előfizetéssel rendelkezem. A különálló bérlők és a egy ExpressRoute-kapcsolatcsoport nem az azonos bérlőben vagy regisztráció a regisztrációk virtuális hálózat kapcsolható össze?

Igen. ExpressRoute-engedélyek is kiterjedhet előfizetés, a bérlő és a beléptetési határok szükséges további konfiguráció nélkül. 

További információkért lásd: [ExpressRoute-kapcsolatcsoport megosztása több előfizetés között](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>Virtuális hálózatok egymástól elkülönített azonos kapcsolatcsoporthoz csatlakoznak?

Nem. Az útválasztási szempontjából minden, az ugyanahhoz az ExpressRoute-kapcsolatcsoporthoz kapcsolt virtuális hálózatok ugyanazon útválasztási tartomány részét képezik és nem különítve egymástól. Ha útvonal elkülönítési van szüksége, hozzon létre egy külön az ExpressRoute-kapcsolatcsoport szeretné.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Használhatok egy virtuális hálózatot több ExpressRoute-kapcsolatcsoporthoz csatlakozik?

Igen. Egyetlen virtuális hálózattal az ExpressRoute-Kapcsolatcsoportok legfeljebb négy kapcsolat. Négy különböző keresztül kell rendelni [ExpressRoute-helyek](expressroute-locations.md).

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>A saját virtuális hálózatok összekötése ExpressRoute-kapcsolatcsoportokkal csatlakoztatott elérhető számomra az interneten?

Igen. Ha nincs közzététel az alapértelmezett útvonalat (0.0.0.0/0) vagy Internet útvonal előtagot a BGP-munkameneten keresztül, csatlakozhat az internethez egy ExpressRoute-kapcsolatcsoporthoz kapcsolt virtuális hálózat.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Letilthatom internetkapcsolat virtuális hálózatokhoz csatlakozó ExpressRoute-Kapcsolatcsoportok?

Igen. Alapértelmezett felé haladó útvonalak meghirdetéséhez (0.0.0.0/0) letiltása az összes üzembe helyezett virtuális hálózaton belüli virtuális gépek internetkapcsolat, és a kimenő összes forgalom az ExpressRoute-kapcsolatcsoport keresztül irányítani.

Alapértelmezett útvonalak meghirdetése akkor, ha azt vissza a helyi kényszerített forgalmát a Microsoft társviszony-létesítés (például az Azure storage és SQL DB) keresztül felajánlott szolgáltatásokhoz. Meg kell konfigurálnia az útválasztókat abból való visszatéréshez forgalmat az Azure-ba, a Microsoft társviszony-létesítési útvonal használatával vagy az interneten keresztül. Ha engedélyezte, hogy egy végpontot a szolgáltatáshoz, a szolgáltatás a forgalom nem kényszeríti a helyszíni eredetű forgalomhoz. A forgalom az Azure gerinchálózatán belül marad. A Szolgáltatásvégpontok kapcsolatos további információkért lásd: [virtuális hálózati Szolgáltatásvégpontok](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json)

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Az ugyanahhoz az ExpressRoute-kapcsolatcsoporthoz kapcsolt virtuális hálózatok közötti kommunikáció egymással?

Igen. A virtuális hálózatok ugyanahhoz az ExpressRoute-kapcsolatcsoporthoz csatlakozik üzembe helyezett virtuális gépek kommunikálhatnak egymással.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>Használható az expressroute-tal együtt virtuális hálózatokhoz helyek közötti kapcsolat?

Igen. Az ExpressRoute egyszerre is használható, a site-to-site VPN-eket. Lásd: [konfigurálása ExpressRoute- és helyek közötti egyidejű kapcsolatok](expressroute-howto-coexist-resource-manager.md).

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Miért van a virtuális hálózat az ExpressRoute-átjárót társított nyilvános IP-cím?

A nyilvános IP-cím csak belső kezelésére szolgál, és nem jelent a virtuális hálózat egy biztonsági kockázatokat.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>E meghirdethet útvonalak száma korlátozva van?

Igen. Legfeljebb 4000 útvonal címelőtagjainak magánhálózati társviszony-létesítés és Microsoft társviszony-létesítéshez 200 fogadunk. 10 000 útvonalak privát társviszony-létesítéshez az ExpressRoute prémium funkció engedélyezésével növelhető.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Tudom a BGP-munkamenetben meghirdetni is IP-címtartományok korlátozások vannak?

A Microsoft társviszony-létesítési BGP-munkamenet nem fogadunk el saját előtagok (RFC1918).

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>Mi történik, ha túllépem a BGP-t korlátai?

BGP-munkamenetek a rendszer eldobja. Miután az előtag száma a határérték alá csökken visszaáll.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>Mi az az ExpressRoute BGP fenntartási idő? Ez módosítható?

A fenntartási ideje 180. Életben tartási üzenetek érkeznek minden 60 másodpercben. Nem lehet módosítani a Microsoft oldalon ezek rögzített beállításai. Lehetséges, hogy különböző időzítők konfigurálását, és a BGP-munkamenet paraméterek ennek megfelelően egyezteti.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Módosíthatja a ExpressRoute-kapcsolatcsoport sávszélességét?

Igen, próbálja meg növelni az Azure Portalon, vagy a PowerShell-lel az ExpressRoute-kapcsolatcsoport sávszélességét. Ha kapacitás érhető el a fizikai port, amelyen a kapcsolatcsoport lett létrehozva, a módosítás sikeres lesz. 

Ha a módosítás nem sikerül, azt jelenti, vagy nincs elegendő kapacitás a jelenlegi porton left és szeretne létrehozni egy új ExpressRoute-kapcsolatcsoporthoz a nagyobb sávszélesség, illetve, hogy nincsenek további kapacitást az adott helyhez, ebben az esetben nem képes növelni a a sávszélesség. 

Győződjön meg arról, hogy frissítse a szabályozások sávszélesség növelésének támogatása érdekében a hálózatokon belül kapcsolatszolgáltató szerepeltetendő is megkapják. Nem, azonban csökkenthető az ExpressRoute-kapcsolatcsoport sávszélességét. Hozzon létre egy új ExpressRoute-kapcsolatcsoport alacsonyabb sávszélességet, és törölje a régi kapcsolatcsoportot kell.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Hogyan változtatható meg az ExpressRoute-kapcsolatcsoport sávszélességét?

A REST API vagy a PowerShell-parancsmag használatával az ExpressRoute-kapcsolatcsoport sávszélességét frissítheti.

## <a name="expressroute-premium"></a>Az ExpressRoute prémium

### <a name="what-is-expressroute-premium"></a>Mi az ExpressRoute premium?

Az ExpressRoute prémium szintű gyűjteménye, a következő funkciókat:

* Nagyobb 10 000 útvonalak privát társviszony-létesítéshez a 4000 útvonalakat útválasztási táblázat korlátozva.
* Virtuális hálózatok és az ExpressRoute globális elérhetőségű is engedélyezhetők az ExpressRoute-Kapcsolatcsoportok kapcsolatok számának növelése (az alapértelmezett érték 10-es). További információkért lásd: a [ExpressRoute korlátok](#limits) tábla.
* Kapcsolódás az Office 365 és Dynamics 365.
* Globális kapcsolódás a Microsoft core hálózaton keresztül. Most már kapcsolat egy geopolitikai régióban található virtuális hálózat az ExpressRoute-kapcsolatcsoport egy másik régióban.<br>
    **Példák:**

    *  Egy Nyugat-európai régióban létrehozott ExpressRoute-kapcsolatcsoport létrehozása a szilícium-völgy, virtuális hálózatok közötti kapcsolat. 
    *  A Microsoft társviszony-létesítés más geopolitikai régiók előtagokat hirdet meg, hogy a kapcsolatcsoport a szilícium-völgy, például SQL Azure-Európa Nyugat-India csatlakozhat.


### <a name="limits"></a>Hány virtuális hálózatok és a globális elérhetőségű ExpressRoute-kapcsolatok lehet engedélyezni az ExpressRoute-kapcsolatcsoport tudok ExpressRoute premium engedélyezésével?

Az alábbi táblázatokban az ExpressRoute-korlátok, és az ExpressRoute-kapcsolatcsoportonként virtuális hálózatok és az ExpressRoute globális elérhetőségű kapcsolatok száma:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Hogyan engedélyezhető az ExpressRoute premium?

Az ExpressRoute prémium szolgáltatásokat is engedélyezhető, ha a szolgáltatás engedélyezve van, és tudja leállítani a kapcsolatcsoport állapota frissítésével. Az ExpressRoute premium engedélyezheti a kapcsolatcsoport létrehozáskor, vagy meghívhatja a REST API vagy PowerShell-parancsmagot.

### <a name="how-do-i-disable-expressroute-premium"></a>Hogyan tilthatom le a az ExpressRoute premium?

Az ExpressRoute premium letilthatja a REST API vagy a PowerShell-parancsmag meghívásával. Győződjön meg arról, hogy méretezte kapcsolódási igényeinek teljesítéséhez az alapértelmezés szerinti korlátozásoknak az ExpressRoute premium letiltása előtt. Ha a kihasználtság meghaladja az alapértelmezett korlátai szerint skálázható, tiltsa le az ExpressRoute premium irányuló kérelem sikertelen lesz.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Is szeretnék kiválasztani, a prémium szintű szolgáltatás készletből kívánt szolgáltatásokat?

Nem. Az funkciók nem választhat. Ha bekapcsolja az ExpressRoute premium engedélyezzük összes funkciót.

### <a name="how-much-does-expressroute-premium-cost"></a>Mennyibe az ExpressRoute prémium szintű szolgáltatás?

Tekintse meg [díjszabás](https://azure.microsoft.com/pricing/details/expressroute/) költségek mellett.

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>Kell fizetnem a standard ExpressRoute díjain felül kivetett díjak az ExpressRoute prémium?

Igen. Az ExpressRoute prémium szintű díjak vonatkoznak, és az ExpressRoute kapcsolatcsoport a kapcsolatszolgáltató által igényelt díjakat felett.

## <a name="expressroute-for-office-365"></a>Az Office 365 ExpressRoute

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services"></a>Hogyan hozhatok létre egy ExpressRoute-kapcsolatcsoporthoz szeretne csatlakozni az Office 365-szolgáltatásokhoz?

1. Tekintse át a [ExpressRoute előfeltételeit ismertető lap](expressroute-prerequisites.md) , hogy megfeleljen a követelményeknek.
2. Győződjön meg arról, hogy teljesülnek-e a kapcsolódási igényei, tekintse át a szolgáltatók és a helyek listáját a [ExpressRoute-partnerek és helyek](expressroute-locations.md) cikk.
3. A kapacitás-követelmények tervezése áttekintésével [hálózattervezés és teljesítményhangolás az Office 365](https://aka.ms/tune/).
4. A következő lépésekkel a kapcsolat beállításához a munkafolyamatokban [Kapcsolatcsoportok kiépítésével és állapotával kapcsolatos az ExpressRoute-munkafolyamatokat](expressroute-workflows.md).

> [!IMPORTANT]
> Győződjön meg arról, hogy engedélyezte az ExpressRoute prémium bővítmény konfigurálása az Office 365-szolgáltatásokhoz való kapcsolódás során.
> 
> 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services-and-dynamics-365"></a>A meglévő ExpressRoute-Kapcsolatcsoportok támogathatja a csatlakozást az Office 365-szolgáltatások és a Dynamics 365?

Igen. A meglévő ExpressRoute-kapcsolatcsoport beállítható úgy, hogy támogatja az Office 365-szolgáltatásokhoz való kapcsolódás. Győződjön meg arról, hogy rendelkezik-e elegendő kapacitással az Office 365-szolgáltatásokhoz és, hogy engedélyezte a premium bővítményt. [Hálózattervezés és teljesítményhangolás az Office 365](https://aka.ms/tune/) kell azt tervezi, hogy a kapcsolat segítségével. Lásd még [létrehozása és módosítása egy ExpressRoute-kapcsolatcsoport](expressroute-howto-circuit-classic.md).

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>Mely Office 365 ExpressRoute-kapcsolaton keresztül elérhető szolgáltatások?

Tekintse meg [Office 365 URL-címei és IP-címtartományok](https://aka.ms/o365endpoints) oldal expressroute-on keresztül támogatott szolgáltatások naprakész listáját.

### <a name="how-much-does-expressroute-for-office-365-services-cost"></a>Mennyibe kerül az ExpressRoute az Office 365-szolgáltatások költség?

Office 365-szolgáltatások prémium bővítmény engedélyezésének megkövetelése. Tekintse meg a [díjszabási részleteit ismertető oldal](https://azure.microsoft.com/pricing/details/expressroute/) költségek.

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>Mely régiókban támogatott ExpressRoute az Office 365-höz a?

Lásd: [ExpressRoute-partnerek és helyek](expressroute-locations.md) információt.

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Érhetem el az Office 365 az interneten keresztül, akkor is, ha ExpressRoute lett konfigurálva a szervezetem számára?

Igen. Az Office 365-szolgáltatásvégpontokat az interneten keresztül érhető el, annak ellenére, hogy a hálózat ExpressRoute van konfigurálva. Ha a hálózat, a helyen van konfigurálva, az Office 365 szolgáltatás expressroute-on keresztül csatlakozni ellenőrizze a szervezet hálózati csapatával.

### <a name="how-can-i-plan-for-high-availability-for-office-365-network-traffic-on-azure-expressroute"></a>Hogyan tudok megtervezheti a magas rendelkezésre állás az Office 365-forgalmát az Azure expressroute-on?
Tekintse meg az ajánlás [magas rendelkezésre állás és feladatátvétel az Azure expressroute használatával](https://aka.ms/erhighavailability)

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>ExpressRoute-kapcsolatcsoporton Azure US Government elérhető számomra az Office 365 US Government Community (GCC) szolgáltatások?

Igen. Az Office 365 GCC Szolgáltatásvégpontok az Azure US Government expressroute-on keresztül érhetők el. Azonban Ön először nyisson egy támogatási jegyet az előtagokat hirdet meg a Microsoftnak szeretne biztosít az Azure Portalon. Az Office 365 GCC-szolgáltatásokhoz való kapcsolódás után fog állni a támogatási jegy jön létre. 

## <a name="route-filters-for-microsoft-peering"></a>Microsoft társviszony-létesítés útvonalszűrőinek

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>E vagyok ne tudják bekapcsolni a Microsoft társviszony-létesítés először, milyen útvonalak látható?

Nem láthatja az összes olyan esetleges útvonalat. Hogy egy útvonalszűrőhöz csatlakoztatása kapcsolatcsoporthoz előtag hirdetmények elindításához. Útmutatásért lásd: [útvonalszűrőket konfigurálása Microsoft társviszony-létesítés](how-to-routefilter-powershell.md).

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Be van kapcsolva a Microsoft társviszony-létesítés és most már a tapasztalataimat szeretném jelölje be az Exchange online-hoz, de azt lehetővé teszi számomra, hogy I nem vagyok jogosult mindez hiba.

Útvonalszűrők használatakor minden ügyfél bekapcsolhatja a Microsoft társviszony-létesítés. Azonban az Office 365-szolgáltatások felhasználásához, továbbra is szeretné az Office 365 által engedélyezett beolvasása.

### <a name="do-i-need-to-get-authorization-for-turning-on-dynamics-365-over-microsoft-peering"></a>Kell ne tudják bekapcsolni a Dynamics 365 a Microsoft társviszony-létesítésen keresztül engedélyezésének első?

Nem, nem kell engedélyezési a Dynamics 365. Hozzon létre egy szabályt, és válassza ki a Dynamics 365 Közösség engedély nélkül.

### <a name="i-enabled-microsoft-peering-prior-to-august-1-2017-how-can-i-take-advantage-of-route-filters"></a>A Microsoft társviszony-létesítés 2017. augusztus 1., hogyan használhatom az útvonalszűrők engedélyezve

A meglévő expressroute-kapcsolatcsoporttal továbbra is az előtagokat hirdet Office 365 és Dynamics 365. Ha azt szeretné, az Azure nyilvános előtagok hirdetmények hozzáadása az ugyanazon a Microsoft társviszony-létesítésen keresztül is létrehoz egy útvonalszűrőhöz, válassza ki a szükséges szolgáltatásokkal meghirdetett (például az Office 365-ra van szüksége, és a Dynamics 365), és csatolja a szűrőt a Microsoft társviszony-létesítés. Útmutatásért lásd: [útvonalszűrőket konfigurálása Microsoft társviszony-létesítés](how-to-routefilter-powershell.md).

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>A Microsoft társviszony-létesítés egy helyen van, most már a tapasztalataimat szeretném az engedélyezéshez egy másik helyen, és nem látok bármely előtagok.

* Microsoft társviszony-létesítés voltak beállítva a 2017. augusztus 1. ExpressRoute-Kapcsolatcsoportok az összes szolgáltatás előtagkészletet hirdeti meg a Microsoft társviszony-létesítéshez, akkor is, ha nincsenek meghatározva útvonalszűrők fog rendelkezni.

* Microsoft társviszony-létesítésre vannak konfigurálva, vagy 2017. augusztus 1. után az ExpressRoute-Kapcsolatcsoportok, nem rendelkezik minden olyan előtagok mindaddig, amíg egy útvonalszűrőhöz csatolva van a kapcsolatcsoport hirdetve. Nincs előtagok alapértelmezés szerint megjelenik.

## <a name="expressRouteDirect"></a>Az ExpressRoute közvetlen (előzetes verzió)

[!INCLUDE [ExpressRoute Direct](../../includes/expressroute-direct-faq-include.md)]

## <a name="globalreach"></a>Globális elérhetőség (előzetes verzió)

[!INCLUDE [Global Reach](../../includes/expressroute-global-reach-faq-include.md)]
