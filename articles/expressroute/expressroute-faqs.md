---
title: GYAKRAN FELTETT KÉRDÉSEK - Azure ExpressRoute | Microsoft dokumentumok
description: Az ExpressRoute gyIK a támogatott Azure-szolgáltatásokkal, a költséggel, az adatokkal és a kapcsolatokkal, az SLA-val, a szolgáltatókkal és helyekkel, a sávszélességgel és a további technikai részletekkel kapcsolatos információkat tartalmazza.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: jaredro
ms.openlocfilehash: 845c53ec970777901ae8d1c0abf5032ac705d3e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264920"
---
# <a name="expressroute-faq"></a>ExpressRoute – Gyakori kérdések

## <a name="what-is-expressroute"></a>Mi az az ExpressRoute?

Az ExpressRoute egy Azure-szolgáltatás, amely lehetővé teszi a Microsoft adatközpontok és infrastruktúra közötti privát kapcsolatok létrehozását, amelyek az Ön telephelyén vagy egy helymegosztási létesítményben találhatók. Az ExpressRoute-kapcsolatok nem mennek át a nyilvános interneten, és nagyobb biztonságot, megbízhatóságot és sebességet kínálnak, mint az interneten keresztüli tipikus kapcsolatok.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Milyen előnyökkel jár az ExpressRoute és a magánhálózati kapcsolatok használata?

Az ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át. Nagyobb biztonságot, megbízhatóságot és sebességet kínálnak, alacsonyabb és konzisztens késést, mint az interneten keresztüli tipikus kapcsolatok. Bizonyos esetekben az ExpressRoute-kapcsolatok használatával adatok átvitele a helyszíni eszközök és az Azure között jelentős költségelőnyöket okozhat.

### <a name="where-is-the-service-available"></a>Hol érhető el a szolgáltatás?

A szolgáltatás helyét és elérhetőségét ezen az oldalon találja: [ExpressRoute-partnerek és -helyek.](expressroute-locations.md)

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Hogyan csatlakozhatok az ExpressRoute segítségével a Microsofthoz, ha nem áll kapcsolatban az ExpressRoute-szolgáltató egyik partnerével?

Kiválaszthatja a regionális szolgáltatót, és ethernet-kapcsolatokat létesíthet a támogatott csereszolgáltatói helyek egyikéhez. Ezután társtársa lehet a Microsofttal a szolgáltató helyén. Ellenőrizze az [ExpressRoute-partnerek és -helyek](expressroute-locations.md) utolsó szakaszát, hogy a szolgáltató jelen van-e valamelyik cserehelyen. Ezután megrendelheti az ExpressRoute-áramkört a szolgáltatón keresztül az Azure-hoz való csatlakozáshoz.

### <a name="how-much-does-expressroute-cost"></a>Mennyibe kerül az ExpressRoute?

Az árképzési információkért tekintse meg az [árképzési részleteket.](https://azure.microsoft.com/pricing/details/expressroute/)

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Ha egy adott sávszélességű ExpressRoute-áramkörért fizetek, a hálózati szolgáltatótól megvásárolt VPN-kapcsolatnak ugyanolyan sebességgel kell rendelkeznie?

Nem. Bármilyen sebességű VPN-kapcsolatot megvásárolhat a szolgáltatótól. Az Azure-ral való kapcsolat azonban a megvásárolt ExpressRoute-áramköri sávszélességre korlátozódik.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-necessary"></a>Ha egy adott sávszélességű ExpressRoute-áramkörért fizetek, szükség esetén nagyobb sebességre is képes vagyok?

Igen. Az ExpressRoute-áramkörök úgy vannak konfigurálva, hogy további költségek nélkül a beszerzett sávszélesség-korlát kétszeresét feltörjék. Érdeklődjön a szolgáltatónál, hogy támogatják-e ezt a funkciót. Ez nem tart fenn, és nem garantált.  Ha a forgalom egy ExpressRoute-átjárón keresztül áramlik, a termékváltozat sávszélessége rögzített és nem robbanékony.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Használhatom ugyanazt a magánhálózati kapcsolatot a virtuális hálózattal és más Azure-szolgáltatásokkal egyszerre?

Igen. Az ExpressRoute-áramkör beállítása után lehetővé teszi a virtuális hálózaton és más Azure-szolgáltatásokon belüli szolgáltatások egyidejű elérését. Virtuális hálózatokhoz csatlakozik a privát társviszony-létesítési útvonalon keresztül, és más szolgáltatásokhoz a Microsoft társviszony-létesítési útvonalon keresztül.

### <a name="how-are-vnets-advertised-on-expressroute-private-peering"></a>Hogyan történik a virtuális hálózatok hirdetése az ExpressRoute privát társviszony-létesítési?

Az ExpressRoute-átjáró az Azure virtuális hálózat *címterét(címeit)* hirdeti, az alhálózati szinten nem vehet fel/zárhatja ki. Mindig a virtuális hálózat címtér, amely hirdetett. Ha virtuális társviszony-létesítést használ, és a társviszony-létesített virtuális hálózat "Távoli átjáró használata" engedélyezve van, a társviszonyba létesített virtuális hálózat címtere is meg lesz hirdetve.

### <a name="how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering"></a>Hány előtag hirdethető egy virtuális hálózatról a helyszíni ExpressRoute privát társviszony-létesítési?

Legfeljebb 200 előtagot hirdetnek egyetlen ExpressRoute-kapcsolaton, vagy átjáró-átvitel használatával virtuális kapcsolatlétesítésen keresztül. Ha például 199 címter van egyetlen virtuális hálózaton, amely egy ExpressRoute-kapcsolathoz csatlakozik, akkor a program mind a 199 előtagot a helyszíni módon fogja meghirdetni. Másik lehetőségként, ha egy virtuális hálózat engedélyezve van az átjáró-átvitel engedélyezéséhez 1 címterülettel és 150 küllős virtuális hálózattal a "Távoli átjáró engedélyezése" beállítással, az átjáróval üzembe helyezett virtuális hálózat 151 előtagot fog hirdetni a helyszíni telepítéshez.

### <a name="what-happens-if-i-exceed-the-prefix-limit-on-an-expressroute-connection"></a>Mi történik, ha túllépem az ExpressRoute-kapcsolat előtagkorlátját?

Az ExpressRoute-kapcsolat és az átjáró (és az átjáró-átvitelt használó társviszony-létesített virtuális hálózatok) közötti kapcsolat megszakad. Az előtagkorlát túllépése esetén újra létrehozza a problémát.  

### <a name="can-i-filter-routes-coming-from-my-on-premises-network"></a>Szűrhetem a helyszíni hálózatomról érkező útvonalakat?

Az útvonalak szűrésének/belefoglalásának egyetlen módja a helyszíni peremhálózati útválasztó. A felhasználó által definiált útvonalak hozzáadhatóak a virtuális hálózathoz, hogy hatással legyenek az adott útválasztásra, de ez statikus lesz, és nem része a BGP-hirdetésnek.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>Az ExpressRoute kínál szolgáltatásiszint-szerződést (SLA)?

További információt az [ExpressRoute SLA-lapon](https://azure.microsoft.com/support/legal/sla/) talál.

## <a name="supported-services"></a>Támogatott szolgáltatások

Az ExpressRoute [három útválasztási tartományt](expressroute-circuit-peerings.md) támogat különböző típusú szolgáltatásokhoz: privát társviszony-létesítés, Microsoft-társviszony-létesítés és nyilvános társviszony-létesítés (elavult).

### <a name="private-peering"></a>Magánhálózati társviszony-létesítés

**Támogatott:**

* Virtuális hálózatok, beleértve az összes virtuális gépet és felhőszolgáltatást

### <a name="microsoft-peering"></a>Microsoft társviszony-létesítés

Ha az ExpressRoute-kapcsolatcsoport engedélyezve van az Azure Microsoft-társviszony-létesítéshez, hozzáférhet az Azure-ban a kapcsolatcsoporton keresztül használt [nyilvános IP-címtartományokhoz.](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) Az Azure Microsoft társviszony-létesítés hozzáférést biztosít az Azure-ban jelenleg üzemeltetett szolgáltatásokhoz (a hálózat termékváltozatától függően földrajzi korlátozásokkal). Egy adott szolgáltatás rendelkezésre állásának ellenőrzéséhez ellenőrizze a szolgáltatás dokumentációjában, hogy van-e az adott szolgáltatáshoz fenntartott tartomány. Ezután keresse meg a célszolgáltatás IP-tartományait, és hasonlítsa össze az [Azure IP-tartományokban és a szolgáltatáscímkék – nyilvános felhőxml-fájlban](https://www.microsoft.com/download/details.aspx?id=56519)felsorolt tartományokkal. Másik lehetőségként megnyithat egy támogatási jegyet a kérdéses szolgáltatáshoz a pontosítás érdekében.

**Támogatott:**

* [Office 365](https://aka.ms/ExpressRouteOffice365)
* Power BI – Az Azure regionális [here](https://docs.microsoft.com/power-bi/service-admin-where-is-my-tenant-located) közösségén keresztül elérhető itt megtudhatja, hogyan tájékszerezheti meg a Power BI-bérlő régióját.
* Azure Active Directory
* [Windows Virtual Desktop](https://azure.microsoft.com/services/virtual-desktop/)
* [Azure DevOps](https://blogs.msdn.microsoft.com/devops/2018/10/23/expressroute-for-azure-devops/) (Azure Global Services-közösség)
* Azure nyilvános IP-címek IaaS-hez (virtuális gépek, virtuális hálózati átjárók, terheléselosztók stb.)  
* A legtöbb más Azure-szolgáltatások is támogatottak. Kérjük, közvetlenül a támogatási szolgáltatáshoz forduljon a használni kívánt szolgáltatáshoz.

**Nem támogatott:**

* Tartalomkézbesítési hálózat (CDN)
* Azure Front Door
* Többtényezős hitelesítési kiszolgáló (örökölt)
* Traffic Manager

### <a name="public-peering"></a>Nyilvános társviszony-létesítés

A nyilvános társviszony-létesítés le van tiltva az új ExpressRoute-áramkörökön. Az Azure-szolgáltatások már elérhetők a Microsoft társviszony-létesítési. Ha olyan áramkört hoz létre, amely et a nyilvános társviszony-létesítés elavultsága előtt hozták létre, a kívánt szolgáltatásoktól függően használhatja a Microsoft társviszony-létesítést vagy a nyilvános társviszony-létesítést.

A nyilvános társviszony-létesítéssel kapcsolatos további információkért és konfigurációs lépésekért lásd: [ExpressRoute nyilvános társviszony-létesítés.](about-public-peering.md)

### <a name="why-i-see-advertised-public-prefixes-status-as-validation-needed-while-configuring-microsoft-peering"></a>Miért jelenik meg a "Hirdetett nyilvános előtagok" állapot "Érvényesítés szükséges" állapot a Microsoft társviszony-létesítésének konfigurálása kor?

A Microsoft ellenőrzi, hogy a megadott "Hirdetett nyilvános előtagok" és a "Társ ASN" (vagy "Customer ASN") hozzá vannak-e rendelve az Internetes útválasztási beállításjegyzékben. Ha a nyilvános előtagokat egy másik entitástól kapja, és a hozzárendelés nincs rögzítve az útválasztási beállításjegyzékben, az automatikus érvényesítés nem fejeződik be, és manuális érvényesítést igényel. Ha az automatikus ellenőrzés sikertelen, az "Érvényesítés szükséges" üzenet jelenik meg.

Ha az "Érvényesítés szükséges" üzenet jelenik meg, gyűjtse össze azokat a dokumentum(okait), amelyek(ek)et az útválasztási jegyzékben az előtagok tulajdonosaként felsorolt entitás a szervezethez rendeli,(/a) gyűjtse össze azokat a dokumentum(okat), amelyek (2) bekezdéssel megegyezésre vannak. támogatási jegy megnyitását az alábbiak szerint.

![](./media/expressroute-faqs/ticket-portal-msftpeering-prefix-validation.png)

### <a name="is-dynamics-365-supported-on-expressroute"></a>Támogatott a Dynamics 365 az ExpressRoute-on?

A Dynamics 365 és a Common Data Service (CDS) környezetek az Azure-ban találhatók, ezért az ügyfelek élvezhetik az Azure-erőforrások ExpressRoute-támogatásának előnyeit. Csatlakozhat a szolgáltatásvégpontokhoz, ha az útválasztó szűrő tartalmazza az Azure-régiók dynamics 365/CDS-környezetek üzemelteti.

> [!NOTE]
> [Az ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium) **szolgáltatás nem** szükséges a Dynamics 365-kapcsolathoz az Azure ExpressRoute-on keresztül.

## <a name="data-and-connections"></a>Adatok és kapcsolatok

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Korlátozva van az ExpressRoute használatával átvihető adatok mennyisége?

Nem szabunk meg korlátozást az adatátvitel mennyiségére vonatkozóan. A sávszélesség-díjakkal kapcsolatos információkért tekintse meg az [árképzésrészleteit.](https://azure.microsoft.com/pricing/details/expressroute/)

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>Milyen kapcsolati sebességet támogat az ExpressRoute?

Támogatott sávszélesség-ajánlatok:

50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps

### <a name="which-service-providers-are-available"></a>Mely szolgáltatók érhetők el?

A szolgáltatók és helyek listáját az [ExpressRoute-partnerek és -helyek](expressroute-locations.md) című témakörben található.

## <a name="technical-details"></a>Technikai részletek

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Milyen műszaki követelmények vonatkoznak a helyszíni helyem azure-hoz való csatlakoztatására?

A követelményekért tekintse meg az [ExpressRoute előfeltételei lapot.](expressroute-prerequisites.md)

### <a name="are-connections-to-expressroute-redundant"></a>Az ExpressRoute-hoz való kapcsolatok redundánsak?

Igen. Minden ExpressRoute-kapcsolat rendelkezik egy redundáns pár keresztkapcsolat van konfigurálva, hogy magas rendelkezésre állást biztosítson.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Megszakad a kapcsolat, ha valamelyik ExpressRoute-kapcsolatom meghibásodik?

Nem veszíti el a kapcsolatot, ha a keresztkapcsolatok egyike meghibásodik. Redundáns kapcsolat áll rendelkezésre a hálózat terhelésének támogatásához és az ExpressRoute-kapcsolat magas rendelkezésre állásának biztosításához. Ezenkívül létrehozhat egy áramkört egy másik társviszony-létesítési helyen az áramkörszintű rugalmasság elérése érdekében.

### <a name="how-do-i-implement-redundancy-on-private-peering"></a>Hogyan valósítható meg redundancia a privát társviszony-létesítés?

Több ExpressRoute-kapcsolati kapcsolatkülönböző helyekről több ExpressRoute-kapcsolati kapcsolati kapcsolati kapcsolati kapcsolattal rendelkező hálózathoz is csatlakoztatható, így magas rendelkezésre állás érhető el abban az esetben, ha egyetlen kapcsolati kör elérhetetlenné válik. Ezután [nagyobb súlyokat rendelhet](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection) a helyi kapcsolathoz, hogy egy adott áramkört részesítsen előnyben. Erősen ajánlott, hogy az ügyfelek legalább két ExpressRoute-áramkört állítsanak be az egypontnyi meghibásodás elkerülése érdekében. 

Lásd [itt](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) a tervezés a magas rendelkezésre állású és [itt](https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering) tervezése katasztrófa utáni helyreállításhoz.  

### <a name="how-i-do-implement-redundancy-on-microsoft-peering"></a>Hogyan valósíthatom meg a redundanciát a Microsoft társviszony-létesítésén?

Erősen ajánlott, ha az ügyfelek microsoftos társviszony-létesítést használnak az Azure-közszolgáltatások, például az Azure Storage vagy az Azure SQL eléréséhez, valamint az Office 365-höz microsoftos társviszony-létesítést használó ügyfelek számára, hogy több áramkört valósítanak meg különböző társviszony-létesítési lehetőségekben. az egyes meghibásodási pontok elkerülése érdekében. Az ügyfelek mindkét kapcsolatcsoporton ugyanazt az előtagot hirdethetik, és [az AS PATH elő-közelgő](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending) használatával, vagy különböző előtagokat hirdethetnek a helyszíni elérési út meghatározásához.

Lásd [itt](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) tervezése a magas rendelkezésre állás.

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Hogyan biztosítható az ExpressRoute-hoz csatlakoztatott virtuális hálózat magas rendelkezésre állása?

Magas rendelkezésre állást érhet el, ha különböző társviszony-létesítési helyeken (például Szingapúrban, Szingapúrban2) csatlakoztatja az ExpressRoute-áramköröket a virtuális hálózathoz. Ha egy ExpressRoute-kapcsolat megszakad, a kapcsolat átmegy egy másik ExpressRoute-kapcsolaton. Alapértelmezés szerint a virtuális hálózatot elhagyó forgalom az egyenlő költségű többútvonalas útválasztás (ECMP) alapján történik. A Kapcsolat súlyával az egyik áramkört részesíti előnyben a másikkal. További információ: [ExpressRoute Routing optimalizálása](expressroute-optimize-routing.md).

### <a name="how-do-i-ensure-that-my-traffic-destined-for-azure-public-services-like-azure-storage-and-azure-sql-on-microsoft-peering-or-public-peering-is-preferred-on-the-expressroute-path"></a>Hogyan biztosíthatom, hogy az Azure Public services, például az Azure Storage és az Azure SQL számára szánt forgalmam a Microsoft társviszony-létesítési vagy nyilvános társviszony-létesítési előnyben részesítése az ExpressRoute elérési útján?

Az útválasztó(k)on végre kell hajtania a *Helyi preferencia* attribútumot annak érdekében, hogy a helyszíni és az Azure-beli elérési út mindig előnyben részesüljön az ExpressRoute-kapcsolat(ok)on.

További részleteket [itt](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#path-selection-on-microsoft-and-public-peerings) olvashat a BGP-útvonal kiválasztásáról és a gyakori útválasztó-konfigurációkról. 

### <a name="if-im-not-co-located-at-a-cloud-exchange-and-my-service-provider-offers-point-to-point-connection-do-i-need-to-order-two-physical-connections-between-my-on-premises-network-and-microsoft"></a><a name="onep2plink"></a>Ha nem vagyok egy felhőalapú adatcserében, és a szolgáltatóm pont-pont közötti kapcsolatot kínál, két fizikai kapcsolatot kell rendelnem a helyszíni hálózatom és a Microsoft között?

Ha a szolgáltató két Ethernet virtuális áramkört tud létrehozni a fizikai kapcsolaton keresztül, csak egy fizikai kapcsolatra van szüksége. A fizikai kapcsolat (például egy optikai szál) egy 1. A két Ethernet virtuális áramkör különböző VLAN-azonosítókkal van címkézve, az egyik az elsődleges áramkörhöz, a másik pedig a másodlagos. Ezek a VLAN azonosítók a külső 802.1Q Ethernet fejlécben találhatók. A belső 802.1Q Ethernet fejléc (nem látható) egy adott [ExpressRoute útválasztási tartományhoz](expressroute-circuit-peerings.md)van rendelve.

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Kiterjeszthetem az egyik VLAN-omat az Azure-ra az ExpressRoute használatával?

Nem. Nem támogatjuk a 2.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>Lehet egynél több ExpressRoute-kapcsolat az előfizetésemben?

Igen. Az előfizetésben több ExpressRoute-kapcsolat is lehet. Az alapértelmezett korlát 10.The default limit is set to 10. Szükség esetén a Microsoft támogatási szolgálatához fordulhat a korlát növeléséhez.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Kaphatok ExpressRoute-áramköröket különböző szolgáltatóktól?

Igen. Az ExpressRoute-áramkörök számos szolgáltatóval rendelkezhetnek. Minden ExpressRoute-kapcsolatcsak egy szolgáltatóhoz van társítva. 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-for-example-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>Két ExpressRoute-társviszony-létesítési helyet látok ugyanazon a metrón, például Szingapúrban és Szingapúrban2. Melyik társviszony-létesítési helyet válasszam az ExpressRoute-kapcsolat létrehozásához?
Ha a szolgáltató mindkét helyen kínál ExpressRoute-ot, a szolgáltatóval együttműködve bármelyik webhelyet kiválaszthatja az ExpressRoute beállításához. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>Lehet több ExpressRoute-kapcsolat ugyanazon a metrón? Összekapcsolhatom őket ugyanahhoz a virtuális hálózathoz?

Igen. Több ExpressRoute-kapcsolattal is rendelkezhet azonos vagy különböző szolgáltatókkal. Ha a metró több ExpressRoute-társviszony-létesítési helyekkel rendelkezik, és az áramkörök különböző társviszony-létesítési helyeken jönnek létre, összekapcsolhatja őket ugyanahhoz a virtuális hálózathoz. Ha az áramkörök ugyanazon a társviszony-létesítési helyen jönnek létre, legfeljebb 4 áramkört csatolhat ugyanahhoz a virtuális hálózathoz.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Hogyan csatlakoztathatom a virtuális hálózataimat egy ExpressRoute-kapcsolathoz?

Az alapvető lépések a következők:

* Hozzon létre egy ExpressRoute-áramkört, és engedélyezze azt a szolgáltató.
* Önnek vagy a szolgáltatónak konfigurálnia kell a BGP-társviszony-létesítés(eke)t.
* Kapcsolja össze a virtuális hálózatot az ExpressRoute-áramkörrel.

További információ: [ExpressRoute-munkafolyamatok a körhálózat-létesítéshez és a körkapcsolati állapotokhoz.](expressroute-workflows.md)

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Vannak kapcsolati határok az ExpressRoute-kapcsolatomhoz?

Igen. Az [ExpressRoute-partnerek és helyek](expressroute-locations.md) cikk áttekintést nyújt az ExpressRoute-kapcsolati határokegy ExpressRoute-kapcsolat i. Az ExpressRoute-áramkörök kapcsolata egyetlen geopolitikai régióra korlátozódik. Az ExpressRoute prémium szolgáltatás engedélyezésével a kapcsolat kiterjeszthető a geopolitikai régiók közötti kapcsolatokra.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Egynél több virtuális hálózathoz tudok kapcsolódni egy ExpressRoute-kapcsolathoz?

Igen. Egy szabványos ExpressRoute-kapcsolaton legfeljebb 10 virtuális hálózati kapcsolat, prémium [szintű ExpressRoute-kapcsolaton](#expressroute-premium)pedig legfeljebb 100.You can have up to 10 virtual networks connections on a standard ExpressRoute circuit, and up to 100 on a premium ExpressRoute circuit. 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Több Azure-előfizetésem van, amelyek virtuális hálózatokat tartalmaznak. Csatlakoztathatok olyan virtuális hálózatokat, amelyek külön előfizetésben vannak egyetlen ExpressRoute-kapcsolattal?

Igen. Egyetlen ExpressRoute-kapcsolat használatával legfeljebb 10 virtuális hálózatot kapcsolhat össze ugyanabban az előfizetésben, mint a kapcsolatcsoportvagy különböző előfizetések. Ez a korlát növelhető az ExpressRoute prémium szolgáltatás engedélyezésével.

További információ: [Egy ExpressRoute-kapcsolat megosztása több előfizetés között.](expressroute-howto-linkvnet-arm.md)

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Több Azure-előfizetést társítottam különböző Azure Active Directory-bérlőkhöz vagy nagyvállalati szerződésbeli regisztrációkhoz. Csatlakoztathatom a virtuális hálózatokat, amelyek külön bérlőkben és regisztrációkban vannak egyetlen ExpressRoute-kapcsolatcsoporthoz, amely nem ugyanabban a bérlőben vagy regisztrációban található?

Igen. Az ExpressRoute-engedélyek átnyúlhatnak az előfizetési, bérlői és regisztrációs határokon további konfiguráció nélkül. 

További információ: [Egy ExpressRoute-kapcsolat megosztása több előfizetés között.](expressroute-howto-linkvnet-arm.md)

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>Az azonos áramkörhöz csatlakoztatott virtuális hálózatok elkülönülnek egymástól?

Nem. Útválasztási szempontból az azonos ExpressRoute-kapcsolatcsoporthoz kapcsolódó összes virtuális hálózat ugyanannak az útválasztási tartománynak a része, és nincsenek elkülönítve egymástól. Ha útvonalelkülönítésre van szüksége, létre kell hoznia egy külön ExpressRoute-áramkört.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Egynél több ExpressRoute-kapcsolathoz is csatlakoztathatok egy virtuális hálózatot?

Igen. Egyetlen virtuális hálózat ot legfeljebb négy ExpressRoute-kapcsolati kapcsolati kapcsolati hellyel kapcsolhat össze azonos vagy különböző társviszony-létesítési helyeken. 

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Hozzáférhetek az internethez az ExpressRoute-hálózatokhoz csatlakoztatott virtuális hálózataimról?

Igen. Ha nem hirdetett alapértelmezett útvonalakat (0.0.0/0) vagy internetes útvonal-előtagokat a BGP-munkameneten keresztül, akkor egy ExpressRoute-kapcsolathoz kapcsolódó virtuális hálózatról csatlakozhat az internethez.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Letilthatom az ExpressRoute-hálózatokhoz csatlakoztatott virtuális hálózatok internetkapcsolatát?

Igen. Az alapértelmezett útvonalak (0.0.0.0/0) hirdetésével letilthatja a virtuális hálózaton belül üzembe helyezett virtuális gépekhez való összes internetkapcsolatot, és az expressroute-áramkörön keresztül továbbítja az összes forgalmat.

Ha alapértelmezett útvonalakat hirdet, a forgalmat a Microsoft-társviszony-létesítésen (például az Azure storage-on és az SQL DB-n) keresztül kínált szolgáltatásokra kényszerítivissza a helyiségbe. Az útválasztókat úgy kell konfigurálnia, hogy a Microsoft társviszony-létesítési útvonalán vagy az interneten keresztül visszaadják az Azure-ba irányuló forgalmat. Ha engedélyezte a szolgáltatás végpontját a szolgáltatáshoz, a szolgáltatás forgalma nem lesz kényszerítve a helyiségbe. A forgalom az Azure gerinchálózatán belül marad. A szolgáltatásvégpontokról a [Virtuális hálózati szolgáltatás végpontjairól](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json)

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Az azonos ExpressRoute-kapcsolatcsoporthoz kapcsolódó virtuális hálózatok beszélhetnek egymással?

Igen. Az azonos ExpressRoute-áramkörhöz csatlakoztatott virtuális hálózatokban telepített virtuális gépek kommunikálhatnak egymással.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>Használhatom a helyek közötti kapcsolatot a virtuális hálózatokhoz az ExpressRoute-tal együtt?

Igen. Az ExpressRoute a helyek közötti VPN-ekkel együtt létezhet. Lásd: [Az ExpressRoute és a helyek közötti kapcsolatok konfigurálása](expressroute-howto-coexist-resource-manager.md).

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Miért van az ExpressRoute-átjáróhoz nyilvános IP-cím társítva egy virtuális hálózaton?

A nyilvános IP-cím csak belső felügyeletre szolgál, és nem jelenti a virtuális hálózat biztonsági kitettségét.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Korlátozva vannak a hirdethető útvonalak száma?

Igen. Legfeljebb 4000 útvonal-előtagot fogadunk el a privát társviszony-létesítéshez és 200-at a Microsoft társviszony-létesítéshez. Ha engedélyezi az ExpressRoute prémium szolgáltatás engedélyezéséhez, ezt 10 000-re növelheti a privát társviszony-létesítéshez.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Vannak-e korlátozások az IP-tartományok tudok hirdetni a BGP-munkamenet?

Nem fogadunk el privát előtagokat (RFC1918) a Microsoft társviszony-létesítési BGP-munkamenethez. A Microsoft és a privát társviszony-létesítés koron (legfeljebb /32) bármilyen előtagméretet elfogadunk.

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>Mi történik, ha túllépem a BGP-határértékeket?

A BGP-munkamenetek el lesznek dobva. A lesznek alaphelyzetbe állítva, ha az előtagok száma a korlát alá csökken.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>Mi az ExpressRoute BGP letartási ideje? Be lehet állítani?

A várakoztatási idő 180. Az életben tartást jelző üzeneteket 60 másodpercenként küldjük. Ezek a Microsoft oldalán található rögzített beállítások, amelyek nem módosíthatók. Lehetőség van különböző időzítők konfigurálására, és a BGP-munkamenet paraméterei ennek megfelelően lesznek egyezve.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Módosíthatom az ExpressRoute-kapcsolat sávszélességét?

Igen, megpróbálhatja növelni az ExpressRoute-kapcsolat sávszélességét az Azure Portalon, vagy a PowerShell használatával. Ha van kapacitás a fizikai porton, amelyen a kapcsolatcsoport jött létre, a módosítás sikeres lesz. 

Ha a módosítás sikertelen, az azt jelenti, hogy nincs elegendő kapacitás az aktuális porton, és létre kell hoznia egy új ExpressRoute-áramkört a nagyobb sávszélességgel, vagy hogy nincs további kapacitás az adott helyen, amely esetben nem fogja tudni növelni a Sávszélesség. 

Azt is meg kell nyomon követnie a kapcsolatszolgáltatóval annak érdekében, hogy a sávszélesség növelése érdekében frissítsék a hálózatukon belüli szabályozásokat. Az ExpressRoute-kapcsolat sávszélessége azonban nem csökkenthető. Létre kell hoznia egy új ExpressRoute-áramkört alacsonyabb sávszélességgel, és törölnie kell a régi áramkört.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Hogyan módosíthatom egy ExpressRoute-kapcsolat sávszélességét?

Az ExpressRoute-kapcsolat sávszélességét a REST API vagy a PowerShell-parancsmag használatával frissítheti.

## <a name="expressroute-premium"></a>ExpressRoute prémium

### <a name="what-is-expressroute-premium"></a>Mi az ExpressRoute prémium?

Az ExpressRoute prémium szolgáltatás a következő funkciók gyűjteménye:

* Megnövelt útválasztási tábla korlát 4000 útvonalak 10 000 útvonalak magántárs-létesítés.
* Az ExpressRoute-kapcsolaton engedélyezhető virtuális hálózatok és ExpressRoute globális elérési kapcsolatok számának növelése (az alapértelmezett érték 10). További információt az [ExpressRoute-korlátok táblázatban talál.](#limits)
* Kapcsolódás az Office 365-höz
* Globális kapcsolat a Microsoft törzshálózatán keresztül. Most már összekapcsolhatja a virtuális hálózat ot az egyik geopolitikai régióban egy expressroute-körrel egy másik régióban.<br>
    **Példák:**

    *  Az Európában létrehozott virtuális hálózatot összekapcsolhatja a Szilícium-völgyben létrehozott ExpressRoute-áramkörökkel. 
    *  A Microsoft-társviszony-létesítés, más geopolitikai régiók előtagok hirdetik, hogy csatlakozhat, például az SQL Azure-hoz Európában Nyugat-egy áramkör a Szilícium-völgyben.


### <a name="how-many-vnets-and-expressroute-global-reach-connections-can-i-enable-on-an-expressroute-circuit-if-i-enabled-expressroute-premium"></a><a name="limits"></a>Hány virtuális hálózatot és ExpressRoute globális elérési kapcsolatot engedélyezhetek egy ExpressRoute-kapcsolaton, ha engedélyezem az ExpressRoute prémium verziót?

Az alábbi táblázatok az ExpressRoute-korlátozásokat, valamint a virtuális hálózatok és az ExpressRoute globális elérési kapcsolatok számát mutatják expressroute-kapcsolatonkénti kapcsolatonként:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Hogyan engedélyezhetem az ExpressRoute prémium verziót?

Az ExpressRoute prémium szintű szolgáltatásai akkor engedélyezhetők, ha a szolgáltatás engedélyezve van, és a kapcsolati hálózat állapotának frissítésével leállíthatók. Engedélyezheti az ExpressRoute-prémiumot a hálózat létrehozásakor, vagy meghívhatja a REST API/PowerShell-parancsmacs.You can enable ExpressRoute premium at circuit creation time, or can call the REST API/PowerShell parancsmag.

### <a name="how-do-i-disable-expressroute-premium"></a>Hogyan tilthatom le az ExpressRoute prémium verziót?

Letilthatja az ExpressRoute prémium verziót a REST API vagy a PowerShell-parancsmag hívásával. Az ExpressRoute prémium letiltása előtt győződjön meg arról, hogy az alapértelmezett korlátok nak való megfeleltetéshez méretezett. Ha a kihasználtság az alapértelmezett korlátokon túl méreteződik, az ExpressRoute-díj letiltására irányuló kérés sikertelen lesz.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Kiválaszthatom a kívánt funkciókat a prémium funkciókészletből?

Nem. Nem választhatod ki a funkciókat. Az ExpressRoute premium bekapcsolásakor minden funkciót engedélyezünk.

### <a name="how-much-does-expressroute-premium-cost"></a>Mennyibe kerül az ExpressRoute prémium díja?

A költségeket az [árképzésrészleteiben](https://azure.microsoft.com/pricing/details/expressroute/) találja.

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>Fizetek az ExpressRoute-díjért a normál ExpressRoute-díjakon kívül?

Igen. Az ExpressRoute díja az ExpressRoute-kapcsolati szolgáltatások díjait a kapcsolódási szolgáltató által igényelt ExpressRoute-kapcsolati díjakon felül számítják fel.

## <a name="expressroute-local"></a>ExpressRoute helyi
### <a name="what-is-expressroute-local"></a>Mi az ExpressRoute local?
ExpressRoute local egy SKU ExpressRoute-kapcsolat, a standard termékváltozat és a prémium termékváltozat mellett. A helyi egyik legfontosabb jellemzője, hogy egy ExpressRoute-társviszony-létesítési helyen egy helyi kapcsolatcsoport csak egy vagy két Azure-régióhoz biztosít hozzáférést ugyanazon a metrón vagy annak közelében. Ezzel szemben a standard áramkör hozzáférést biztosít a geopolitikai terület összes Azure-régiójához, és prémium szintű áramkört biztosít az összes Azure-régióhoz világszerte. 

### <a name="what-are-the-benefits-of-expressroute-local"></a>Milyen előnyei vannak az ExpressRoute Local?
A standard vagy premium ExpressRoute-kapcsolatkimenő adatátvitelért a kimenő adatátvitelt kell fizetnie, de az ExpressRoute helyi áramkörért nem kell külön fizetnie a kimenő adatátvitelt. Más szóval az ExpressRoute Local ára tartalmazza az adatátviteli díjakat. ExpressRoute Local egy gazdaságosabb megoldás, ha hatalmas mennyiségű adatot kell átvinni, és az adatokat egy privát kapcsolaton keresztül egy ExpressRoute-társviszony-létesítési hely közelében a kívánt Azure-régiókban. 

### <a name="what-features-are-available-and-what-are-not-on-expressroute-local"></a>Milyen funkciók érhetők el, és melyek nem az ExpressRoute Local?
A Standard ExpressRoute-áramkörökkel összehasonlítva a helyi áramkör ekkre ugyanazok a funkciók, kivéve:
* Az Azure-régiókhoz való hozzáférés hatóköre a fent leírtak szerint
* Az ExpressRoute globális elérése nem érhető el helyi

ExpressRoute Local is ugyanazokat a korlátokat az erőforrások (például a virtuális hálózatok száma áramkörenként) standard. 

### <a name="where-is-expressroute-local-available-and-which-azure-regions-is-each-peering-location-mapped-to"></a>Hol érhető el az ExpressRoute Local, és mely Azure-régiókhoz vannak hozzárendelve?
ExpressRoute Local érhető el a társviszony-létesítési helyeken, ahol egy vagy két Azure-régiók a közelben érhető el. Nem érhető el egy társviszony-létesítési helyen, ahol nincs Azure-régió az adott államban vagy tartományban vagy országban. A pontos leképezéseket [a Helyek oldalon találja.](expressroute-locations-providers.md)  

## <a name="expressroute-for-office-365"></a>ExpressRoute az Office 365-hez

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services"></a>Hogyan hozhatok létre ExpressRoute-áramkört az Office 365-szolgáltatásokhoz való csatlakozáshoz?

1. Tekintse át az [ExpressRoute előfeltételei lapot,](expressroute-prerequisites.md) és győződjön meg arról, hogy megfelel a követelményeknek.
2. A kapcsolódási igények kielégítéséhez tekintse át a szolgáltatók és helyek listáját az [ExpressRoute-partnerek és -helyek](expressroute-locations.md) című cikkben.
3. Tervezze meg kapacitáskövetelményeit az [Office 365 Hálózattervezés és teljesítményhangolás áttekintésével.](https://aka.ms/tune/)
4. Kövesse a munkafolyamatokban felsorolt lépéseket a kapcsolat [ExpressRoute-munkafolyamatainak beállításához a kapcsolatlétesítéshez és a kapcsolati állapotokhoz.](expressroute-workflows.md)

> [!IMPORTANT]
> Győződjön meg arról, hogy engedélyezte az ExpressRoute prémium szintű bővítményt az Office 365-szolgáltatásokhoz való kapcsolódás konfigurálásakor.
> 
> 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services"></a>A meglévő ExpressRoute-áramkörök támogathatják az Office 365-szolgáltatásokhoz való kapcsolódást?

Igen. Meglévő ExpressRoute-áramköre beállítható úgy, hogy támogassa az Office 365-szolgáltatásokhoz való kapcsolódást. Győződjön meg arról, hogy elegendő kapacitással rendelkezik az Office 365-szolgáltatásokhoz való csatlakozáshoz, és hogy engedélyezte a prémium szintű bővítményeket. [Az Office 365 hálózattervezési és teljesítményhangolási](https://aka.ms/tune/) funkcióival megtervezheti a kapcsolódási igényeket. Lásd még: [ExpressRoute-kapcsolat létrehozása és módosítása.](expressroute-howto-circuit-classic.md)

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>Milyen Office 365-szolgáltatások érhetők el ExpressRoute-kapcsolaton keresztül?

Az ExpressRoute-on támogatott szolgáltatások naprakész listáját az [Office 365 URL-címei és IP-címtartományai](https://aka.ms/o365endpoints) lapon találja.

### <a name="how-much-does-expressroute-for-office-365-services-cost"></a>Mennyibe kerül az ExpressRoute for Office 365-szolgáltatások?

Az Office 365-szolgáltatások használatához engedélyezni kell a prémium szintű bővítményeket. Tekintse meg a költségek [díjszabási részleteit tartalmazó oldalt.](https://azure.microsoft.com/pricing/details/expressroute/)

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>Mely régiókban támogatja az ExpressRoute for Office 365?

További információt az [ExpressRoute-partnerek és -helyek](expressroute-locations.md) című témakörben talál.

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Hozzáférhetek az Office 365-höz az interneten keresztül, még akkor is, ha az ExpressRoute a szervezetemhez van konfigurálva?

Igen. Az Office 365 szolgáltatásvégpontjai az interneten keresztül is elérhetők, még akkor is, ha az ExpressRoute konfigurálva van a hálózathoz. Érdeklődjön a szervezet hálózati csapatával, hogy a tartózkodási helyén lévő hálózat úgy van-e beállítva, hogy az ExpressRoute-on keresztül csatlakozzon az Office 365-szolgáltatásokhoz.

### <a name="how-can-i-plan-for-high-availability-for-office-365-network-traffic-on-azure-expressroute"></a>Hogyan tervezhetem meg, hogy az Office 365 hálózati forgalmának magas rendelkezésre állását tervezhessem az Azure ExpressRoute-on?
Tekintse meg a [magas rendelkezésre állásra és feladatátvételre](https://aka.ms/erhighavailability) vonatkozó javaslatot az Azure ExpressRoute-on

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Elérhetem az Office 365 Egyesült Államok Kormányzati Közösség (GCC) szolgáltatásait egy Azure US Government ExpressRoute-kapcsolaton keresztül?

Igen. Az Office 365 GCC szolgáltatásvégpontok az Azure US Government ExpressRoute-on keresztül érhetőek el. Azonban először meg kell nyitnia egy támogatási jegyet az Azure Portalon, hogy a Microsoft nak hirdetni kívánt előtagokat. Az Office 365 GCC-szolgáltatásokkal való kapcsolata a támogatási jegy feloldása után jön létre. 

## <a name="route-filters-for-microsoft-peering"></a>Útvonalszűrők a Microsoft társviszony-létesítéshez

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>Első alkalommal kapcsolom be a Microsoft társviszony-létesítését, milyen útvonalakat fogok látni?

Nem fog látni semmilyen útvonalat. Az előtaghirdetések indításához útvonalszűrőt kell csatolnia az áramkörhöz. További információt a [Microsoft-társviszony-létesítés útvonalszűrőinek konfigurálása](how-to-routefilter-powershell.md)című témakörben talál.

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Bekapcsoltam a Microsoft társviszony-létesítését, és most az Exchange Online-t próbálom kiválasztani, de hibát okoz, hogy nem vagyok jogosult rá.

Útvonalszűrők használata esetén bármely ügyfél bekapcsolhatja a Microsoft társviszony-létesítését. Az Office 365-szolgáltatások igényesetén azonban továbbra is engedélyeznie kell az Office 365-öt.

### <a name="i-enabled-microsoft-peering-prior-to-august-1-2017-how-can-i-take-advantage-of-route-filters"></a>2017. augusztus 1-je előtt engedélyeztem a Microsoft társviszony-létesítését, hogyan tudom kihasználni az útvonalszűrők előnyeit?

A meglévő áramkör továbbra is hirdeti az Office 365 előtagjait. Ha azure-beli nyilvános előtagokat szeretne hozzáadni ugyanazon a Microsoft-társviszony-létesítésen, létrehozhat egy útvonalszűrőt, kiválaszthatja a hirdetett szolgáltatásokat (beleértve a szükséges Office 365-szolgáltatást), és csatolhatja a szűrőt a Microsoft társviszony-létesítéséhez. További információt a [Microsoft-társviszony-létesítés útvonalszűrőinek konfigurálása](how-to-routefilter-powershell.md)című témakörben talál.

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>Nekem van Mikroszkóp társviszony-létesítés egy helyen, most próbálom engedélyezni, hogy egy másik helyen, és nem látok semmilyen előtagok.

* 2017. augusztus 1-je előtt konfigurált ExpressRoute-áramkörök Microsoft-társviszony-létesítése az összes szolgáltatáselőtagot a Microsoft társviszony-létesítésén keresztül hirdeti, még akkor is, ha nincsenek meghatározva az útvonalszűrők.

* 2017. augusztus 1-jén vagy azt követően konfigurált ExpressRoute-áramkörök Microsoft-társviszony-létesítése nem rendelkezik előtagokkal, amíg egy útvonalszűrő nem csatlakozik az áramkörhöz. Alapértelmezés szerint nem jelennek meg előtagok.

## <a name="expressroute-direct"></a><a name="expressRouteDirect"></a>ExpressRoute Direct

[!INCLUDE [ExpressRoute Direct](../../includes/expressroute-direct-faq-include.md)]

## <a name="global-reach"></a><a name="globalreach"></a>Globális elérés

[!INCLUDE [Global Reach](../../includes/expressroute-global-reach-faq-include.md)]
