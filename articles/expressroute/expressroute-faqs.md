---
title: Az Azure ExpressRoute – gyakori kérdések |} Microsoft Docs
description: Az ExpressRoute – gyakori kérdések a támogatott Azure-szolgáltatások, költség, adatokat és kapcsolatok, SLA-t, szolgáltatókat és helyek, sávszélesség és további technikai részleteket adatait tartalmazza.
documentationcenter: na
services: expressroute
author: cherylmc
manager: jeconnoc
editor: ''
ms.assetid: 09b17bc4-d0b3-4ab0-8c14-eed730e1446e
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/14/2018
ms.author: cherylmc
ms.openlocfilehash: f68a4d699d1ad6592fe5481cd69795b06e7d8fca
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34160869"
---
# <a name="expressroute-faq"></a>ExpressRoute – Gyakori kérdések

## <a name="what-is-expressroute"></a>Mi az az ExpressRoute?

ExpressRoute az Azure-szolgáltatások, amely lehetővé teszi a Microsoft adatközpontjaiban és infrastruktúra, amely a helyszínen vagy a közös elhelyezést intézményben közötti magánhálózati kapcsolatok létrehozása. Az ExpressRoute-kapcsolatok nem nyissa meg a nyilvános interneten keresztül, és magasabb szintű biztonságra, megbízhatóságra és tipikus kapcsolatok-nál kisebb késések sebesség kínálnak az interneten keresztül.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Mik az ExpressRoute és magánhálózati kapcsolatok használatának előnyeit?

Az ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át. Magasabb szintű biztonságra, megbízhatóságra és sebesség, mint a szokásos kapcsolatok az interneten keresztül alacsonyabb és egységes késések biztosítanak. Bizonyos esetekben ExpressRoute kapcsolattal közötti adattovábbításra a helyszíni eszközök és Azure partíciónként akár jelentős költségmegtakarítást előnyeit.

### <a name="where-is-the-service-available"></a>Hol található a szolgáltatás elérhető?

Ezen a lapon, a szolgáltatás helyét és a rendelkezésre állási lásd: [ExpressRoute partnerek és a helyek](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Hogyan használhatom ExpressRoute Microsoft csatlakozni, ha az egyik partneren ExpressRoute-szolgáltatója nincs partnerségei?

Válassza ki a regionális szolgáltatónként, és Ethernet-kapcsolat megnyílik egy, a támogatott Exchange szolgáltató helyei. A Microsoft a szolgáltatót a helyen majd partnert. Ellenőrizze az utolsó szakasza [ExpressRoute partnerek és a helyek](expressroute-locations.md) megtekintéséhez a szolgáltató megtalálható-e az exchange-helyeken. A service provider csatlakozni Azure ExpressRoute-kapcsolatcsoportot majd rendezheti.

### <a name="how-much-does-expressroute-cost"></a>Mennyibe ExpressRoute költség?

Ellenőrizze [díjszabása](https://azure.microsoft.com/pricing/details/expressroute/) díjszabási információkat.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Ha kell fizetni az adott sávszélesség ExpressRoute-kapcsolatcsoportot, nem a VPN-kapcsolatot a hálózati szolgáltató vásárlása kell lennie a azonos sebességű?

Nem. A szolgáltató minden sebességű VPN-kapcsolat lehet vásárolni. Azonban a kapcsolatot az Azure a megvásárolt ExpressRoute-kapcsolatcsoport sávszélessége korlátozódik.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-necessary"></a>Ha egy adott sávszélesség ExpressRoute-kör kell fizetni, kell értékig nagyobb sebesség szükség esetén képes?

Igen. ExpressRoute-Kapcsolatcsoportok legyenek konfigurálva ahhoz, hogy legfeljebb kétszer a sávszélesség-korlátozás nem jelent többletköltséget rendelkezésre bocsátott kapacitásnövelés. Ellenőrizze a szolgáltató megjelenítéséhez, ha ez a funkció támogatják.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Használható a azonos magánhálózati kapcsolat a virtuális hálózati és más Azure-szolgáltatásokkal egyidejűleg?

Igen. ExpressRoute-kapcsolatcsoportot, beállítása után, lehetővé teszi a virtuális hálózatban lévő szolgáltatások és más Azure-szolgáltatásokkal egyidejűleg. Csatlakozás a virtuális hálózatok a magánhálózati társviszony-létesítési útvonalon keresztül, és a más szolgáltatások nyilvános társviszony-létesítési vezető elérési út.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>Nem ExpressRoute kínálnak a szolgáltatási szint szerződés (SLA)?

További információ: a [ExpressRoute SLA](https://azure.microsoft.com/support/legal/sla/) lap.

## <a name="supported-services"></a>Támogatott szolgáltatások

Támogatja az ExpressRoute [három útválasztási tartományok](expressroute-circuit-peerings.md) különböző típusú szolgáltatásokhoz.

### <a name="private-peering"></a>Magánhálózati társviszony-létesítés

* Virtuális hálózatok, beleértve a virtuális gépek és a cloud services csomag

### <a name="public-peering"></a>Nyilvános társviszony-létesítés

>[!NOTE]
>Microsoft társviszony-létesítés módja az előnyben részesített összes Azure-platformon futó szolgáltatások eléréséhez.
>

* Power BI
* Dynamics 365 pénzügyi és műveletek (korábbi nevén Dynamics AX Online)
* Az Azure-szolgáltatások legtöbb támogatottak. Ellenőrizze, közvetlenül a támogatási ellenőrzésére használt kívánt szolgáltatással.<br>
  A következő szolgáltatások nem támogatottak:
    * CDN
    * A Visual Studio Team Services terhelés tesztelése
    * Multi-Factor Authentication
    * Traffic Manager

### <a name="microsoft-peering"></a>Microsoft társviszony-létesítés

* [Office 365](http://aka.ms/ExpressRouteOffice365)
* Dynamics 365 felhasználói Engagement-alkalmazást (korábbi nevén CRM Online-hoz)
  * Dynamics 365 az értékesítés
  * Dynamics 365 az ügyfélszolgálat
  * Dynamics 365 mező szolgáltatás
  * Dynamics 365 projekt szolgáltatás
* Használatával [útválasztási szűrők](#route-filters-for-microsoft-peering), a Microsoft társviszony-létesítést az azonos nyilvános-szolgáltatásokhoz való hozzáférés elérhetővé:
  * Power BI
  * Dynamics 365 a pénzügyi és műveletek
  * Az Azure-szolgáltatások legtöbb támogatottak. Ellenőrizze, közvetlenül a támogatási ellenőrzésére használt kívánt szolgáltatással.<br>
  A következő szolgáltatások nem támogatottak:
    * CDN
    * A Visual Studio Team Services terhelés tesztelése
    * Multi-Factor Authentication
    * Traffic Manager

## <a name="data-and-connections"></a>Adatok és kapcsolatok

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Vannak-e a adatmennyiséget ExpressRoute segítségével lehet átvinni vonatkozó korlátozások?

Jelenleg nem korlátozhatja az adatforgalom mennyisége. Tekintse meg [díjszabása](https://azure.microsoft.com/pricing/details/expressroute/) sávszélesség díjszabás olvashat.

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>Milyen kapcsolat sebessége ExpressRoute által támogatott?

Sávszélesség ajánlatok támogatja:

50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps

### <a name="which-service-providers-are-available"></a>Mely szolgáltatók állnak rendelkezésre?

Lásd: [ExpressRoute partnerek és a helyek](expressroute-locations.md) szolgáltatók és helyek listáját.

## <a name="technical-details"></a>Technikai részletek

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Mik a csatlakozás a helyszíni hely Azure műszaki követelményei?

Lásd: [ExpressRoute Előfeltételek lapon](expressroute-prerequisites.md) követelményeket.

### <a name="are-connections-to-expressroute-redundant"></a>Amelyek kapcsolatok az expressroute redundáns?

Igen. Minden egyes ExpressRoute-kapcsolatcsoportot közötti kapcsolatok magas rendelkezésre állás biztosításához konfigurált redundáns két rendelkezik.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>I megszakad a kapcsolat, ha az egyik a ExpressRoute-hivatkozások nem?

Ha az alhálózatok közötti kapcsolatok egyike meghibásodik nem megszakad a kapcsolat. A redundáns kapcsolat támogatják a terhelést a hálózat és az ExpressRoute-kapcsolatcsoportot magas rendelkezésre állású érhető el. Emellett expressroute-kapcsolatcsoporthoz létrehozhat egy másik társviszony-létesítési helye áramkör szintű rugalmasság eléréséhez.

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Hogyan biztosítása érdekében a magas rendelkezésre állású ExpressRoute kapcsolódik a virtuális hálózaton?

A virtuális hálózat magas rendelkezésre állású ExpressRoute-Kapcsolatcsoportok társviszony-létesítési különböző helyeken (pl. szingapúri, Singapore2) csatlakozva érhet el. Ha egy ExpressRoute-kapcsolatcsoportot leáll, kapcsolat hajt végre feladatátvételt egy másik ExpressRoute-kapcsolatcsoport számára. Alapértelmezés szerint a virtuális hálózat elhagyó forgalomra történik a egyenlő költség több útvonalas útválasztás (ECMP) alapján. Kapcsolat súly segítségével egy kör inkább egy másikra. Lásd: [ExpressRoute útválasztási optimalizálása](expressroute-optimize-routing.md) további részleteket a kapcsolat súlyt.

### <a name="onep2plink"></a>Ha nem vagyok a felhőalapú exchange a közös helyen, és a szolgáltató biztosít a pontok közötti kapcsolat, kell érdekében saját helyszíni hálózat és a Microsoft között két fizikai kapcsolatot?

A szolgáltató két virtuális Ethernet-kapcsolatok hozhatnak létre, a fizikai kapcsolaton keresztül, ha csak egyetlen fizikai kapcsolat van szükség. A fizikai kapcsolat (például egy optikai szál) megszakad a réteg 1 (1.) eszközön (lásd a kép). A két virtuális Ethernet-kapcsolatok különböző VLAN-azonosítók szerint, egy, az elsődleges kapcsolat, és egy másik pedig a másodlagos címkével rendelkeznek. A külső 802.1Q Ethernet fejléc szerepelnek ezen VLAN-azonosítót. A belső 802.1Q Ethernet fejléc (nincs ábrázolva) hozzá van rendelve egy adott [ExpressRoute útválasztási tartomány](expressroute-circuit-peerings.md).

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Ki lehet terjeszteni a virtuális helyi hálózatok egyike az Azure ExpressRoute segítségével?

Nem. Az Azure réteg 2 kapcsolat bővítmények nem támogatott.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>Az előfizetésben is rendelkezem egynél több ExpressRoute-kapcsolatcsoportot?

Igen. Az előfizetés csak egy ExpressRoute-kapcsolatcsoportot lehet. Az alapértelmezett érték 10. Kérje a Microsoft Support a korlát növeléséhez is, ha szükséges.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Beállítható, hogy a különböző szolgáltatók ExpressRoute-Kapcsolatcsoportok?

Igen. ExpressRoute-Kapcsolatcsoportok sok szolgáltatókkal lehet. Minden egyes ExpressRoute-kapcsolatcsoportot rendelve egy szolgáltató. 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-eg-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>Két ExpressRoute társviszony-létesítési helye a azonos metro, pl. szingapúri és Singapore2 látható. Mely társviszony-létesítési helye kell kiválasztani saját ExpressRoute-kapcsolatcsoportot létrehozni?
A szolgáltató mindkét helyek ExpressRoute biztosít, ha a szolgáltatónál dolgozik, és válassza ki bármelyik hely ExpressRoute beállítása. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>Az azonos metro is rendelkezem több ExpressRoute-Kapcsolatcsoportok? I hivatkozhat rájuk ugyanahhoz a virtuális hálózathoz?

Igen. Több ExpressRoute-Kapcsolatcsoportok ugyanazon vagy másik szolgáltatókkal lehet. Ha a metro több ExpressRoute-társviszony létesítése – helyek és a kapcsolatok jönnek létre különböző társviszony-létesítési helyeken, az azonos virtuális hálózatban társíthatja azokat. A Kapcsolatcsoportok ugyanazon a társviszony-létesítési helyen létrehozott, nem hivatkozhat ugyanarra a virtuális hálózatra. Minden hely neve, az Azure portálon vagy a PowerShell vagy parancssori felület API egy társviszony-létesítési helye jelöli. Például válassza a "Szingapúri" és "Singapore2" társviszony-létesítési helyeket, és a kapcsolatok az egyes virtuan ugyanahhoz a hálózathoz csatlakozzon. 

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Hogyan a virtuális hálózatok csatlakoztatása ExpressRoute-kapcsolatcsoportot

Az alapvető lépések a következők:

* Egy ExpressRoute-kapcsolatcsoportot létrehozni, és a szolgáltató engedélyezni kell.
* Ön vagy a szolgáltató kell konfigurálnia a BGP társviszony-létesítés (s).
* A virtuális hálózat csatolása az ExpressRoute-kapcsolatcsoportot.

További információkért lásd: [kiépítésével és a kapcsolatcsoport állapotok az ExpressRoute-munkafolyamatokat](expressroute-workflows.md).

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Vannak-e kapcsolat határok saját ExpressRoute-kapcsolatcsoportot?

Igen. A [ExpressRoute partnerek és a helyek](expressroute-locations.md) cikk kapcsolat határain áttekintést nyújt az ExpressRoute-kapcsolatcsoportot. Egy ExpressRoute-kapcsolatcsoportot kapcsolattal egy geopolitikai régió korlátozódik. Az ExpressRoute prémium funkció engedélyezésével geopolitikai régiók közötti kapcsolat bővíthető.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>I hozzákapcsolhatja egynél több virtuális hálózat ExpressRoute-kapcsolatcsoportot?

Igen. Legfeljebb 10 virtuális hálózatok kapcsolatok egy szabványos ExpressRoute körön, és legfeljebb 100 lehet egy [prémium ExpressRoute-kapcsolatcsoportot](#expressroute-premium). 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Van több Azure-előfizetések virtuális hálózatokat tartalmaznak. Kapcsolódhatnak a virtuális hálózatok, a különálló előfizetést egyetlen ExpressRoute-kapcsolatcsoportot?

Igen. Legfeljebb 10 más Azure-előfizetések egyetlen ExpressRoute-kapcsolatcsoportot használandó engedélyezhető. Ez a korlát növelhető a ExpressRoute prémium funkció engedélyezése.

További információkért lásd: [ExpressRoute-kapcsolatcsoportot megosztása több előfizetésekhez](expressroute-howto-linkvnet-arm.md).

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Van több Azure-előfizetéssel társított különböző Azure Active Directory-bérlő vagy a nagyvállalati szerződés történő regisztrációt. Kapcsolódhatnak a virtuális hálózatok külön bérlők és nem a bérlő vagy a regisztráció egyetlen ExpressRoute-kapcsolatcsoportot való regisztrációt?

Igen. ExpressRoute engedélyek előfizetés, a bérlő és a beléptetési is kiterjedhet, és nincs szükség további konfigurációra. 

További információkért lásd: [ExpressRoute-kapcsolatcsoportot megosztása több előfizetésekhez](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>Virtuális hálózatok a azonos expressroute-egymástól különítve csatlakoznak?

Nem. Az útválasztási szempontjából ExpressRoute-kapcsolatcsoport kapcsolódó összes virtuális hálózatok ugyanazon útválasztási tartomány részét képezik, és nem el különítve egymástól. Ha az útvonal elkülönítési van szüksége, hozzon létre egy külön ExpressRoute-kapcsolatcsoportot szeretné.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Beállítható, hogy csak egy ExpressRoute-kapcsolatcsoportot csatlakozik egy virtuális hálózati?

Igen. Egyetlen virtuális hálózat legfeljebb négy ExpressRoute-Kapcsolatcsoportok társíthatja. Négy különböző keresztül lehetnek rendezve [helyek ExpressRoute](expressroute-locations.md).

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>I hozzáférjenek az internetes az ExpressRoute-Kapcsolatcsoportok kapcsolódik a virtuális hálózatok?

Igen. Ha nincs közzététel az alapértelmezett útvonalak (0.0.0.0/0) vagy Internet útvonal előtagok a BGP-kapcsolaton keresztül, ExpressRoute-kapcsolatcsoportot kapcsolódó virtuális hálózaton keresztül csatlakozhat az interneten.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Letilthatom az ExpressRoute-Kapcsolatcsoportok csatlakoztatott virtuális hálózatokhoz internetkapcsolat?

Igen. Alapértelmezett útvonalak (0.0.0.0/0) telepített virtuális hálózaton belüli virtuális gépek összes internetes kapcsolat blokkolása hirdetményt, és keresztül az ExpressRoute-kapcsolatcsoport ki az összes forgalmat.

Alapértelmezett útvonalak forgalmaz hirdetményt, ha azt a helyszínen a nyilvános társviszony-létesítés (például az Azure storage és SQL-adatbázis) vissza keresztül felajánlott szolgáltatásokhoz kényszerített forgalom. Meg kell konfigurálnia az útválasztók forgalom vissza az Azure-ba, a nyilvános társviszony-létesítési elérési útján, vagy az interneten keresztül. Ha engedélyezte a szolgáltatás szolgáltatásvégpont (előzetes verzió), a forgalmat a szolgáltatás nem kényszeríti a helyszínen. A forgalom Azure hálózat belül marad. Szolgáltatásvégpontok kapcsolatos további információkért lásd: [virtuális hálózati szolgáltatási végpont](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json)

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Virtuális hálózatok ugyanazon ExpressRoute-kapcsolatcsoport kapcsolódó kommunikálhat egymással?

Igen. Virtuális hálózatok ugyanazon ExpressRoute-kapcsolatcsoport csatlakozik a telepített virtuális gépek kommunikálhatnak egymással.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>Használható együtt ExpressRoute virtuális hálózatok hely-hely kapcsolatot?

Igen. ExpressRoute a pont-pont VPN egyszerre is használható.

### <a name="can-i-move-a-virtual-network-from-site-to-site--point-to-site-configuration-to-use-expressroute"></a>Áthelyezhető egy virtuális hálózati helyek / pont-pont konfigurációból ExpressRoute használandó?

Igen. Hozzon létre egy ExpressRoute-átjárót a virtuális hálózaton belül kell. A folyamathoz társított egy kis állásidő van.

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Miért van egy nyilvános IP-címet az ExpressRoute-átjárót a virtuális hálózaton társított?

A nyilvános IP-cím csak a belső felügyeletére használható, és nem a virtuális hálózat egy biztonsági kockázatot jelent.

### <a name="what-do-i-need-to-connect-to-azure-storage-over-expressroute"></a>Mit kell ExpressRoute keresztül csatlakozni az Azure storage?

Egy ExpressRoute-kapcsolatcsoportot létrehozni és a nyilvános társviszony-létesítéshez útvonalak konfigurálnia kell.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Vannak-e I hirdethető útvonalak száma vonatkozó korlátozások?

Igen. Legfeljebb 4000 útvonal-előtaglistát kezel a magánhálózati társviszony-létesítés és 200 minden nyilvános társviszony-létesítést és a Microsoft társviszony-létesítés fogadunk. Sikerült a magánhálózati társviszony-létesítés az ExpressRoute prémium funkció engedélyezésével 10 000 útvonalait növelhető.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Vannak-e IP-címtartományok I a BGP-munkameneten keresztül hirdethető korlátozásai?

Jelenleg nem fogadja el a privát előtagok (RFC1918) a nyilvános és a Microsoft társviszony-létesítési BGP-munkamenetet.

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>Mi történik, ha szeretnék haladhatja meg a BGP csökkenti?

A program eltávolítja a BGP-munkamenetek. Az előtag száma nem éri a korlát el után visszaáll.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>Mi az az ExpressRoute BGP fenntartási idő? Ez módosítható?

A fenntartási ideje 180. A életben tartási üzenetek küldése történik 60 másodpercenként. Ezek rögzítettek beállításai a Microsoft-oldalon, és nem módosítható. Lehetséges, hogy más időzítők konfigurálhatja, és a BGP-munkamenet paraméterek ennek megfelelően egyezteti.

### <a name="after-i-advertise-the-default-route-00000-to-my-virtual-networks-i-cant-activate-windows-running-on-my-azure-vms-how-to-i-fix-this"></a>Miután a virtuális hálózatok az alapértelmezett útvonalat (0.0.0.0/0) I hirdetményt, az Azure virtuális gépeken futó Windows nem aktiválható. Hogyan a javítható a probléma?

Az alábbi lépéseket a aktiválási kérést Azure segítséget:

1. Az ExpressRoute-kör nyilvános társviszony létesítéséhez.
2. Hajtsa végre a DNS-címkeresést, és keresse meg az IP-címe **kms.core.windows.net**
3. A kulcskezelő szolgáltatás azonosítani kell, hogy az aktiválási kérelem Azure és a kérés elfogadása származnak. Hajtsa végre a következő három feladatok közül:

   * A helyszíni hálózaton irányíthatja az IP-cím, a 2 vissza az Azure nyilvános társviszony keresztül beszerzett irányuló adatforgalmat.
   * Rendelkezik a NSP szolgáltatóhoz haj-PIN-kódot a forgalmat Azure keresztül a nyilvános társviszony-létesítést.
   * Hozzon létre felhasználói útvonalat, amely az IP-cím a következő ugrásként internetkapcsolattal mutat, és alkalmazza azt az alhálózat, amelyben a virtuális gépek is.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Módosíthatja a sávszélesség az ExpressRoute-kapcsolatcsoportot?

Igen, próbálja meg növelni a sávszélesség a ExpressRoute-kapcsolatcsoportot az Azure portálon, vagy a PowerShell használatával az. Ha van kapacitása használható a fizikai port a kapcsolatcsoport létrehozásának időpontja, a módosítás sikeres lesz. 

A módosítás nem sikerül, az azt jelenti, vagy nem áll rendelkezésre elegendő az aktuális port maradt kapacitás és a nagyobb sávszélességet egy új ExpressRoute-kapcsolatcsoportot létrehozni, vagy, hogy nincs további kapacitást az adott helyhez, ebben az esetben sem lesz a sávszélesség növelése érdekében. 

A kapcsolat szolgáltatójánál, annak érdekében, hogy a sávszélesség növelésének támogatása érdekében a hálózatokon belül szabályozások frissítse a további műveleteket is rendelkezik majd. Nem, azonban csökkenti a sávszélesség az ExpressRoute-kapcsolatcsoportot. Akkor hozzon létre egy új ExpressRoute-kapcsolatcsoportot alacsony sávszélességű, és törölje a régi áramkör.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Hogyan változtathatom meg a sávszélesség az ExpressRoute-kapcsolatcsoportot?

A sávszélesség az ExpressRoute-kapcsolatcsoport a REST API-t vagy a PowerShell-parancsmag használatával frissítheti.

## <a name="expressroute-premium"></a>Az ExpressRoute-támogatás

### <a name="what-is-expressroute-premium"></a>Mi az az ExpressRoute-támogatás?

ExpressRoute prémium gyűjteménye a következő szolgáltatásokat:

* A magánhálózati társviszony-létesítés 10 000 útvonalait 4000 útvonalak az útválasztási táblázat korlátját-re növelte.
* Növekedett, amely képes csatlakozni az ExpressRoute-kapcsolatcsoport Vnetek száma (alapértelmezett érték 10). További információkért lásd: a [ExpressRoute korlátok](#limits) tábla.
* Az Office 365 és a Dynamics 365 való kapcsolódást.
* Globális kapcsolatot a Microsoft core hálózaton keresztül. Most hozzákapcsolhatja egy geopolitikai régióban egy Vnetet az ExpressRoute-kapcsolatcsoportot egy másik régióban.<br>
    **Példák:**

    *  Egy VNet létrehozott Európa Nyugat-India szilícium Valley létrehozott ExpressRoute-kapcsolatcsoportot társíthatja. 
    *  A nyilvános társviszony más geopolitikai régiók előtagokat hirdet úgy, hogy Ön kapcsolódhatnak, például az SQL Azure-Európában Nyugat-India szilícium Valley az expressroute-kapcsolatcsoporthoz.


### <a name="limits"></a>Hány Vnetek I hozzákapcsolhatja egy ExpressRoute-kapcsolatcsoportot I ExpressRoute-támogatás engedélyezése?

Az alábbi táblázatok bemutatják az ExpressRoute-korlátok, majd az ExpressRoute-kapcsolatcsoportot Vnetek száma:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Hogyan engedélyezhető az ExpressRoute-támogatás?

ExpressRoute prémium szolgáltatások akkor engedélyezhető, ha a szolgáltatás engedélyezve van, és a kapcsolatcsoport állapotát frissítésével leállíthat. Engedélyezheti a ExpressRoute prémium áramkör létrehozáskor, vagy a REST API meghívása / PowerShell-parancsmagot.

### <a name="how-do-i-disable-expressroute-premium"></a>Hogyan tiltsa le a ExpressRoute prémium?

ExpressRoute prémium letilthatja a REST API-t vagy a PowerShell-parancsmag meghívásával. Meg kell győződnie arról, hogy rendelkezik-e méretezve kapcsolódási igényeinek megfelelő alapértelmezett korlátokat ExpressRoute-támogatás letiltása előtt. Ha a kihasználtsága méretezi beállításban megadott alapértelmezett korlát, az ExpressRoute prémium letiltására irányuló kérelem sikertelen lesz.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Képes vagyok kiválasztani, a prémium funkció készletből kívánt szolgáltatásokat?

Nem. A szolgáltatások nem választhat. Ha bekapcsolja az ExpressRoute-támogatás engedélyezzük minden funkcióját.

### <a name="how-much-does-expressroute-premium-cost"></a>Mennyibe ExpressRoute prémium költség?

Tekintse meg [díjszabása](https://azure.microsoft.com/pricing/details/expressroute/) költség.

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>Kell fizetni az ExpressRoute premium szabványos ExpressRoute költségek mellett?

Igen. ExpressRoute támogatási díjak vonatkoznak fölött ExpressRoute körön és a kapcsolat szolgáltatójánál által igényelt díjakat.

## <a name="expressroute-for-office-365-and-dynamics-365"></a>Az Office 365 és Dynamics 365 ExpressRoute

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services-and-dynamics-365"></a>Hogyan Dynamics 365 és az Office 365-szolgáltatásokhoz való csatlakozás ExpressRoute-kapcsolatcsoportot létrehozni?

1. Tekintse át a [ExpressRoute Előfeltételek lapon](expressroute-prerequisites.md) győződjön meg arról, hogy számítógépe megfelel a követelményeknek.
2. Győződjön meg arról, hogy teljesülnek-e a kapcsolódási igényei, tekintse át a szolgáltatók és a helyek listáját a [ExpressRoute partnerek és a helyek](expressroute-locations.md) cikk.
3. Tervezze meg a kapacitásigények megtekintésével [az alaphálózati topológia tervezése és az Office 365 teljesítményhangolás](http://aka.ms/tune/).
4. A kapcsolat beállítása a munkafolyamatokban ismertetett lépések [kiépítésével és a kapcsolatcsoport állapotok az ExpressRoute-munkafolyamatokat](expressroute-workflows.md).

> [!IMPORTANT]
> Győződjön meg arról, hogy engedélyezte ExpressRoute prémium szintű bővítmény Dynamics 365 és az Office 365-szolgáltatásokhoz való kapcsolat konfigurálásakor.
> 
> 

### <a name="do-i-need-to-enable-azure-public-peering-to-connect-to-office-365-services-and-dynamics-365"></a>Az Azure nyilvános társviszony-létesítés Dynamics 365 és az Office 365-szolgáltatásokhoz való csatlakozás engedélyezése kell?

Nem, csak kell ahhoz, hogy a Microsoft Peering. Az Azure AD hitelesítési forgalom Microsoft Peering keresztül küld el. 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services-and-dynamics-365"></a>A meglévő ExpressRoute-Kapcsolatcsoportok támogathatja a Dynamics 365 és az Office 365-szolgáltatásokhoz való kapcsolatot?

Igen. A meglévő ExpressRoute-kapcsolatcsoportot beállítható úgy, hogy támogatja az Office 365-szolgáltatásokhoz való kapcsolódás. Győződjön meg arról, hogy rendelkezik-e elegendő kapacitással Office 365-szolgáltatásokhoz, és a prémium szintű bővítmény engedélyezte. [Az alaphálózati topológia tervezése és az Office 365 teljesítményhangolás](http://aka.ms/tune/) kell azt tervezi, hogy a kapcsolat segítségével. Lásd még [létrehozása és módosítása az ExpressRoute-kapcsolatcsoportot](expressroute-howto-circuit-classic.md).

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>Milyen Office 365 szolgáltatások ExpressRoute-kapcsolaton keresztül elérhető?

Tekintse meg [Office 365 URL-címei és IP-címtartományok](http://aka.ms/o365endpoints) lap ExpressRoute keresztül támogatott szolgáltatások naprakész listáját.

### <a name="how-much-does-expressroute-for-office-365-services-and-dynamics-365-cost"></a>Mennyibe ExpressRoute az Office 365-szolgáltatásokhoz és Dynamics 365 költség?

Office 365-szolgáltatások és Dynamics 365 szükséges a prémium szintű bővítmény engedélyezni kell. Tekintse meg a [díjszabás részleteit megjelenítő oldalra](https://azure.microsoft.com/pricing/details/expressroute/) költségek.

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>Milyen régiók ExpressRoute az Office 365 támogatott?

Lásd: [ExpressRoute partnerek és a helyek](expressroute-locations.md) információt.

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Elérhető Office 365 az interneten keresztül, akkor is, ha ExpressRoute lett konfigurálva a szervezetem számára?

Igen. Az Office 365 szolgáltatás végpontok azok az interneten keresztül érhető el, annak ellenére, hogy a hálózat konfigurációja ExpressRoute. Tekintse meg a szervezet hálózati csapattal Ha azon a helyen a hálózaton keresztül ExpressRoute Office 365-szolgáltatásokhoz van konfigurálva.

### <a name="how-can-i-plan-for-high-availability-for-office-365-network-traffic-on-azure-expressroute"></a>Hogyan tudom megtervezheti a magas rendelkezésre álláshoz az Office 365 hálózati forgalom az Azure ExpressRoute?
Tekintse meg az ajánlott [magas rendelkezésre állás és a feladatátvétel az Azure ExpressRoute](https://aka.ms/erhighavailability)

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Elérhető Office 365 US Government közösségi (ÖET) szolgáltatások keresztül Azure US Government ExpressRoute-kapcsolatcsoportot?

Igen. Az Office 365 ÖET Szolgáltatásvégpontok az Azure US Government ExpressRoute keresztül érhetők el. Azonban először egy támogatási jegy arra, hogy a Microsoft hirdetési kíván előtagok az Azure portál megnyitásához. Az Office 365 ÖET-szolgáltatásokhoz való kapcsolódás után a támogatási jegy megoldódott jön létre. 

### <a name="can-dynamics-365-for-operations-formerly-known-as-dynamics-ax-online-be-accessed-over-an-expressroute-connection"></a>Dynamics 365 műveletek (korábbi nevén Dynamics AX Online) keresztül is elérhető az ExpressRoute-kapcsolatot?

Igen. [Dynamics 365 műveletekhez](https://www.microsoft.com/dynamics365/operations) Azure-platformon futó. Engedélyezheti az Azure nyilvános társviszony az ExpressRoute-kapcsolatcsoportot a csatlakozáshoz.

## <a name="route-filters-for-microsoft-peering"></a>A Microsoft társviszony-létesítéshez útvonal szűrők

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>I vagyok bekapcsolja a Microsoft társviszony-létesítés először, milyen útvonalak látható?

Bármely útvonalak nem látják. Kell egy útvonal szűrő csatolása a kapcsolatcsoport előtag hirdetmények elindításához. Útmutatásért lásd: [konfigurálása útvonal szűrők a Microsoft társviszony-létesítéshez](how-to-routefilter-powershell.md).

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Be van kapcsolva a Microsoft társviszony-létesítést, és most próbálok, jelölje be az Exchange online-hoz, de, ezért meg, hogy a perceké 'M nem jogosultak erre hiba.

Útvonal szűrők használatakor minden ügyfél bekapcsolja a Microsoft társviszony-létesítés. Azonban az Office 365-szolgáltatásokhoz fel, továbbra is szeretné Office 365 által engedélyezett beolvasása.

### <a name="do-i-need-to-get-authorization-for-turning-on-dynamics-365-over-microsoft-peering"></a>Kell nincs Dynamics 365 több Microsoft társviszony-engedélyezés eléréséhez?

Nem, nem kell engedélyezési Dynamics 365. Hozzon létre egy szabályt, és válassza ki a Dynamics 365 közösségi engedély nélkül.

### <a name="i-enabled-microsoft-peering-prior-to-august-1st-2017-how-can-i-take-advantage-of-route-filters"></a>A Microsoft társviszony-létesítés előtt augusztus 1., 2017, hogyan lehet előnyének kihasználása útvonal szűrők engedélyezve

A meglévő expressroute továbbra is az előtagok hirdetési az Office 365 és Dynamics 365. Ha azt szeretné, az Azure nyilvános előtag hirdetmények hozzáadása a Microsoft társviszony, akkor is útvonal szűrő létrehozása, válassza ki a szolgáltatásokat kell meghirdetett (beleértve az Office 365-szolgáltatásokat kell és Dynamics 365), és csatolja a szűrőt a Microsoft társviszony-létesítés. Útmutatásért lásd: [konfigurálása útvonal szűrők a Microsoft társviszony-létesítéshez](how-to-routefilter-powershell.md).

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>Van a Microsoft társviszony-létesítés egy helyen, most már engedélyezheti egy másik helyen próbálok, és nem látható a előtagokat.

* A Microsoft társviszony-létesítést az ExpressRoute-Kapcsolatcsoportok 2017. augusztus 1. előtt konfigurált meghirdetett Microsoft társviszony-létesítést, még akkor is, ha az útvonal-szűrők nem definiált összes szolgáltatás előtagok fog rendelkezni.

* A Microsoft társviszony-létesítést az ExpressRoute-Kapcsolatcsoportok vannak konfigurálva, vagy azt követően 2017. augusztus 1. nem rendelkezik a előtagokat amíg útvonal szűrő nem csatlakoztatja a kapcsolatcsoport hirdetve. Alapértelmezés szerint nincs előtagok jelenik meg.
