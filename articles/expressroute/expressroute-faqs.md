---
title: Gyakori kérdések – Azure ExpressRoute |} A Microsoft Docs
description: Az ExpressRoute – gyakori kérdések az információ a támogatott Azure-szolgáltatások, a költségek, adatok és kapcsolatok, SLA-t, szolgáltatók és a helyek, sávszélesség és további technikai részleteket tartalmazza.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: jaredro
ms.openlocfilehash: 9f2b106df531dfdf26c2c83b765e3f7270a63df5
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361731"
---
# <a name="expressroute-faq"></a>ExpressRoute – Gyakori kérdések

## <a name="what-is-expressroute"></a>Mi az az ExpressRoute?

Az ExpressRoute egy Azure-szolgáltatás, amely lehetővé teszi, hogy között Microsoft-adatközpontok és infrastruktúra, amely a helyszínen vagy egy közös elhelyezési létesítményből csatlakozó privát kapcsolatok hozhatók létre. Az ExpressRoute-kapcsolatok nem a nyilvános interneten keresztül, és magasabb szintű biztonságra, megbízhatóságra és a kisebb a késésük, mint a szokásos internetkapcsolatoknál megbízhatóbbak kínálnak az interneten keresztül.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Mik azok az ExpressRoute- és magánhálózati kapcsolatok használatának előnyei?

Az ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át. A szokványos internetkapcsolatoknál nagyobb biztonságra, megbízhatóságra és sebessége, alacsonyabb és következetes késése, mint az interneten keresztüli általános kapcsolatok. Bizonyos esetekben az ExpressRoute-kapcsolatok használata közötti adatátvitelhez a helyszíni eszközök és az Azure jelentős árelőnyökkel is.

### <a name="where-is-the-service-available"></a>Hol érhető el a szolgáltatást?

A szolgáltatás helyének és elérhetőségének megtekintéséhez tekintse meg a következő oldalt: [ExpressRoute Partners és Locations](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Hogyan használhatom az ExpressRoute csatlakozni a Microsoft, ha az ExpressRoute-szolgáltató partnereink egyikével partneri nem rendelkezem?

Válassza ki a regionális szolgáltató, és Ethernet-kapcsolatok megnyitja a támogatott exchange egyik kapcsolatszolgáltatói helyek. A Microsoft a szolgáltató helyen majd társviszonyt. Tekintse át az [ExpressRoute-partnerek és-helyszínek](expressroute-locations.md) utolsó szakaszát, és ellenőrizze, hogy a szolgáltató megtalálható-e valamelyik Exchange-helyen. A szolgáltató szeretne csatlakozni az Azure ExpressRoute-kapcsolatcsoport majd rendezheti.

### <a name="how-much-does-expressroute-cost"></a>Mennyibe az ExpressRoute szolgáltatás?

A díjszabással kapcsolatos információk [megtekintéséhez](https://azure.microsoft.com/pricing/details/expressroute/) .

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Az adott sávszélesség ExpressRoute-kapcsolatcsoport kell fizetnem, ha a VPN-kapcsolatot a saját hálózati szolgáltató vásárolhatok rendelkezik kell ugyanazt a sebességet?

Nem. A szolgáltató egy VPN-kapcsolat bármely sebességű vásárolhatnak. Az Azure-bA a kapcsolat azonban korlátozódik vásárolhat ExpressRoute-kapcsolatcsoport sávszélességét.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-necessary"></a>Az adott sávszélesség ExpressRoute-kapcsolatcsoport kell fizetnem, ha kell a nagyobb sebesség, szükség esetén akár eszközcsomag lehetővé teszi?

Igen. Az ExpressRoute-Kapcsolatcsoportok legfeljebb kétszer a sávszélességre vonatkozó korlátját kétszereséig további költségek nélkül is. engedélyezésére vannak konfigurálva. Ellenőrizze a szolgáltató megtekintéséhez, hogy támogatja-e ezt a funkciót. Ez nem hosszabb ideig tart, és nem garantált.  Ha a forgalom egy ExpressRoute-átjárón keresztül áramlik, az SKU sávszélessége rögzített, és nem használható fel.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Használhatom ugyanazt a magánhálózati kapcsolatot rendelkező virtuális hálózatot és más Azure-szolgáltatások egyszerre?

Igen. Egy ExpressRoute-kapcsolatcsoportot, egyszer állítsa be, egy virtuális hálózaton belüli szolgáltatások és más Azure-szolgáltatások eléréséhez egyszerre teszi lehetővé. Csatlakozás virtuális hálózatokhoz keresztül a magánhálózati társviszony-létesítési útvonal és más szolgáltatások keresztül a Microsoft társviszony-létesítési útvonalra.

### <a name="how-are-vnets-advertised-on-expressroute-private-peering"></a>Hogyan hirdetik meg a virtuális hálózatok a ExpressRoute-alapú privát kiszolgálókon?

Az ExpressRoute-átjáró meghirdeti *Az Azure* -VNet, és az alhálózat szintjén nem szerepelhet/nem zárható ki. Mindig a meghirdetett VNet-címtartomány. Továbbá, ha a VNet-társítást használja, és a "távoli átjáró használata" beállítás engedélyezve van, a rendszer a meghirdetett VNet VNet is közzéteszi.

### <a name="can-i-filter-routes-coming-from-my-on-premises-network"></a>Szűrhetim a helyszíni hálózatról érkező útvonalakat?

Az útvonalak szűrésének és belefoglalásának egyetlen módja a helyszíni peremhálózati útválasztó. A felhasználó által megadott útvonalak hozzáadhatók a VNet az adott útválasztásra, de ez statikus, és nem része a BGP-hirdetménynek.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>Kínál az ExpressRoute szolgáltatói szerződés (SLA)?

További információ: [EXPRESSROUTE SLA](https://azure.microsoft.com/support/legal/sla/) oldal.

## <a name="supported-services"></a>Támogatott szolgáltatások

A ExpressRoute [három útválasztási tartományt](expressroute-circuit-peerings.md) támogat a különböző típusú szolgáltatások esetében: privát társítás, Microsoft-társítás és nyilvános társ (elavult).

### <a name="private-peering"></a>Magánhálózati társviszony-létesítés

**Támogatott**

* Virtuális hálózatok, beleértve az összes virtuális gép és a cloud services

### <a name="microsoft-peering"></a>Microsoft társviszony-létesítés

Ha a ExpressRoute-áramkör engedélyezve van az Azure Microsoft-partneri kapcsolathoz, az Azure-ban az áramkörön használt [nyilvános IP-címtartományok](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) is elérhetők. Az Azure Microsoft-partnerek hozzáférést biztosítanak a jelenleg az Azure-ban üzemeltetett szolgáltatásokhoz (az áramköri SKU-tól függően földrajzi korlátozásokkal). Egy adott szolgáltatás rendelkezésre állásának ellenőrzéséhez ellenőrizheti a szolgáltatás dokumentációját, és ellenőrizheti, hogy van-e fenntartott tartomány közzétéve az adott szolgáltatás számára. Ezután keresse meg a cél szolgáltatás IP-tartományait, és hasonlítsa össze az [Azure IP-tartományok és szolgáltatások címkék – nyilvános felhő XML-fájljában](https://www.microsoft.com/download/details.aspx?id=56519)felsorolt tartományokkal. Azt is megteheti, hogy megnyit egy támogatási jegyet a kérdéses szolgáltatáshoz a tisztázás érdekében.

**Támogatott**

* [Office 365](https://aka.ms/ExpressRouteOffice365)
* Power BI az Azure regionális közösségén keresztül érhető el, [itt](https://docs.microsoft.com/power-bi/service-admin-where-is-my-tenant-located) találhatja meg a Power bi bérlő régióját.
* Azure Active Directory
* [Windows rendszerű virtuális asztal](https://azure.microsoft.com/services/virtual-desktop/)
* [Azure DevOps](https://blogs.msdn.microsoft.com/devops/2018/10/23/expressroute-for-azure-devops/) (Azure globális szolgáltatások közössége)
* Azure nyilvános IP-címek a IaaS (Virtual Machines, Virtual Network átjárók, terheléselosztó stb.)  
* A többi Azure-szolgáltatás többsége is támogatott. Tekintse meg közvetlenül a kívánt ellenőrizheti a támogatási szolgáltatással.

**Nem támogatott:**

* Tartalomkézbesítési hálózat (CDN)
* Azure Front Door
* Multi-Factor Authentication kiszolgáló (örökölt)
* Traffic Manager

### <a name="public-peering"></a>Nyilvános társviszony-létesítés

Nyilvános társviszony-létesítés le lett tiltva az új ExpressRoute-kapcsolatcsoportot. Az Azure-szolgáltatások mostantól elérhetők a Microsoft-partnerek számára. Ha olyan áramkört hoz létre, amely a nyilvános társítást megelőzően lett létrehozva, akkor a kívánt szolgáltatástól függően használhatja a Microsoft társközi vagy nyilvános társítását.

További információ és konfigurációs lépések a nyilvános társítással kapcsolatban: [ExpressRoute Public-peering](about-public-peering.md).

### <a name="why-i-see-advertised-public-prefixes-status-as-validation-needed-while-configuring-microsoft-peering"></a>Miért látom a "meghirdetett nyilvános előtagok" állapotát "érvényesítés szükséges" állapotra a Microsoft-társak konfigurálása során?

A Microsoft ellenőrzi, hogy a megadott "meghirdetett nyilvános előtagok" és "peer ASN" (vagy "Customer ASN") hozzá van-e rendelve az Internet Routing beállításjegyzékben. Ha egy másik entitásból kéri le a nyilvános előtagokat, és ha a hozzárendelés nincs rögzítve az útválasztási beállításjegyzékben, akkor az automatikus ellenőrzés nem fejeződik be, és manuális ellenőrzésre van szükség. Ha az automatikus ellenőrzés meghiúsul, az "érvényesítés szükséges" üzenet jelenik meg.

Ha az "érvényesítés szükséges" üzenet jelenik meg, Gyűjtse össze azokat a dokumentumokat, amelyek megjelenítik a nyilvános előtagokat a szervezethez az útválasztási beállításjegyzékben szereplő előtagok tulajdonosának listáján felsorolt entitások alapján, és küldje el ezeket a dokumentumokat kézi ellenőrzésre a következő módon: támogatási jegy megnyitása az alább látható módon.

![](./media/expressroute-faqs/ticket-portal-msftpeering-prefix-validation.png)

### <a name="is-dynamics-365-supported-on-expressroute"></a>Támogatott-e a Dynamics 365 a ExpressRoute-on?

A Dynamics 365-és Common Data Service-(CDS-) környezetek az Azure-ban futnak, ezért az ügyfelek az Azure-erőforrások mögöttes ExpressRoute-támogatás előnyeit élvezik. Ha az útválasztó szűrője tartalmazza azokat az Azure-régiókat, amelyeken a Dynamics 365/CDS-környezetek futnak, csatlakozhat a szolgáltatási végpontokhoz.

> [!NOTE]
> A Dynamics 365-hez az Azure ExpressRoute-kapcsolaton keresztül **nem** szükséges a [ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium) használata.

## <a name="data-and-connections"></a>Adatok és kapcsolatok

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Az ExpressRoute-tal is átvinni adatok mennyisége korlátozva van?

A Microsoft nem állítson be kényszerített korlátot a adatátvitel mennyiségét. A sávszélesség díjszabásával kapcsolatban tekintse meg a [díjszabás részleteit](https://azure.microsoft.com/pricing/details/expressroute/) .

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>Milyen kapcsolati sebességet támogat az ExpressRoute?

Támogatott sávszélesség-ajánlatok:

50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps

### <a name="which-service-providers-are-available"></a>Melyik szolgáltatók érhetők el?

Lásd: [ExpressRoute-partnerek és-helyszínek](expressroute-locations.md) a szolgáltatók és a helyszínek listájához.

## <a name="technical-details"></a>Technikai részletek

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Mik azok a saját helyszíni hely csatlakoztatása az Azure műszaki követelményei?

A követelményekért tekintse meg a [ExpressRoute előfeltételek oldalát](expressroute-prerequisites.md) .

### <a name="are-connections-to-expressroute-redundant"></a>Azok kapcsolatok expressroute redundáns?

Igen. Mindegyik ExpressRoute-kapcsolatcsoport redundáns párjai közötti kapcsolatok magas rendelkezésre állás konfigurálva van.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Elveszítek kapcsolat, ha az egyik saját ExpressRoute-kapcsolatok nem?

Ha egyik közötti kapcsolat nem sikerül nem megszakad a kapcsolat. Redundáns kapcsolat támogatja a terhelés, a hálózat és a magas rendelkezésre állás az ExpressRoute-kapcsolatcsoport érhető el. Emellett egy másik társviszony-létesítési helyszínen kapcsolatcsoport-szintű rugalmasság elérése érdekében a kapcsolatcsoport hozhat létre.

### <a name="how-do-i-implement-redundancy-on-private-peering"></a>Hogyan a redundancia megvalósítását a privát partnereken?

A különböző társi helyekről több ExpressRoute-áramkör csatlakoztatható ugyanahhoz a virtuális hálózathoz, hogy magas rendelkezésre állást biztosítson abban az esetben, ha egyetlen áramkör elérhetetlenné válik. Ezután a helyi kapcsolathoz [nagyobb súlyt rendelhet](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection) hozzá, és előnyben részesített egy adott áramkört. Erősen ajánlott, hogy az ügyfelek legalább két ExpressRoute-áramkört állítsanak be az egyes meghibásodási pontok elkerülése érdekében. 

[Itt](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) megtekintheti a magas rendelkezésre állást és [itt](https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering) megtervezheti a vész-helyreállítást.  

### <a name="how-i-do-implement-redundancy-on-microsoft-peering"></a>Hogyan valósítható meg a redundancia a Microsoft-partnereken?

Erősen ajánlott, ha az ügyfelek a Microsoft-partneri kapcsolatot használják az Azure-beli nyilvános szolgáltatások, például az Azure Storage vagy az Azure SQL eléréséhez, valamint az Office 365-hoz készült Microsoft-társítást használó ügyfelek számára az egypontos meghibásodások elkerülésére szolgáló helyek. Az ügyfelek megtehetik ugyanazt az előtagot mindkét áramkörön, és az [elérési út](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending) alapján vagy különböző előtagokat is megadhatnak a helyi elérési út meghatározásához.

A magas rendelkezésre állás kialakításához [itt](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) talál további információt.

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Hogyan biztosítja a magas rendelkezésre állás az expressroute-hoz csatlakoztatott virtuális hálózaton?

Magas rendelkezésre állást érhet el, a virtuális hálózathoz való csatlakozás ExpressRoute-Kapcsolatcsoportok társviszony-létesítési különböző helyeken (például, Szingapúr, szingapúr2). Ha egy ExpressRoute-kapcsolatcsoport leáll, kapcsolat feladatátvételt hajt végre egy másik ExpressRoute-kapcsolatcsoporthoz. Alapértelmezés szerint a virtuális hálózatot elhagyó forgalomra legyen irányítva a egyenlő költség több útvonalas útválasztás (ECMP) alapú. Kapcsolat súly segítségével egy kapcsolatcsoport inkább egy másikba. További információ: az [ExpressRoute-útválasztás optimalizálása](expressroute-optimize-routing.md).

### <a name="how-do-i-ensure-that-my-traffic-destined-for-azure-public-services-like-azure-storage-and-azure-sql-on-microsoft-peering-or-public-peering-is-preferred-on-the-expressroute-path"></a>Hogyan, hogy az Azure-beli nyilvános szolgáltatásokhoz, például az Azure Storage-hoz és az Azure SQL-hez a Microsoft társközi vagy nyilvános társ-kezeléshez szánt forgalma előnyben részesített a ExpressRoute útvonalon?

Meg kell valósítania az útválasztó (k) *helyi preferencia* attribútumát annak biztosításához, hogy a helyszínről az Azure-ba irányuló útvonal mindig a ExpressRoute-áramkör (ek) re legyen előnyben részesítve.

További részletek [itt](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#path-selection-on-microsoft-and-public-peerings) találhatók a BGP-útvonal kiválasztása és az általános útválasztó-konfigurációk című részben. 

### <a name="onep2plink"></a>Ha a felhőalapú Exchange-ben nem közös helyen vagyok, és a szolgáltató pont-pont típusú kapcsolatot biztosít, akkor két fizikai kapcsolatot kell rendelni a helyszíni hálózat és a Microsoft között?

Ha a szolgáltató hozhatnak létre két virtuális Ethernet-kapcsolatcsoportokat az fizikai-kapcsolaton keresztül, csak azt kell egy fizikai kapcsolattal. A fizikai (például egy optikai fiber) kapcsolat megszakad a réteg 1 (1) eszköz (lásd a képen). A két virtuális Ethernet-kapcsolatcsoportokat eltérő VLAN-azonosítót, egyet az elsődleges kapcsolatcsoportot, és a másodlagos egy megfelelő címkékkel. A külső 802.1Q Ethernet-fejléc el azokat a VLAN-azonosítót. A belső 802.1 Q Ethernet-fejléc (nem látható) egy adott ExpressRoute- [útválasztási tartományra](expressroute-circuit-peerings.md)van leképezve.

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

Igen. Használhat több ExpressRoute-Kapcsolatcsoportok azonos vagy eltérő szolgáltatókkal. Ha a metro rendelkezik több ExpressRoute-társviszony-létesítési helyszínek, és a Kapcsolatcsoportok hozhatók létre, különböző társviszony-létesítési helyszínek, az azonos virtuális hálózatban csatolhatja őket. Ha az áramkör ugyanazon a helyen jön létre, akkor akár 4 áramkört is összekapcsolhat ugyanahhoz a virtuális hálózathoz.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Hogyan létesíthetek kapcsolatot a virtuális hálózatok csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz

Az alapszintű lépések a következők:

* Egy ExpressRoute-kapcsolatcsoportot létrehozni, és engedélyezze a szolgáltató rendelkezik.
* Ön vagy a szolgáltató kell konfigurálnia a BGP társviszony-létesítés (s).
* A virtuális hálózat csatolása az ExpressRoute-kapcsolatcsoporthoz.

További információ: [ExpressRoute-munkafolyamatok az áramkör kiépítés és az áramköri állapotokhoz](expressroute-workflows.md).

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Vannak-e kapcsolat határai saját ExpressRoute-kapcsolatcsoportot?

Igen. A [ExpressRoute partnerek és helyszínek](expressroute-locations.md) című cikk áttekintést nyújt az ExpressRoute-áramkör kapcsolati határairól. Kapcsolat ExpressRoute-kapcsolatcsoport egy geopolitikai régióban korlátozódik. Az ExpressRoute prémium funkció engedélyezésével geopolitikai régiók közötti kapcsolat bővíthetők ki.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Lehet hivatkozni lehet az ExpressRoute-kapcsolatcsoport több mint egy virtuális hálózat?

Igen. Akár 10 virtuális hálózati kapcsolattal is rendelkezhet standard ExpressRoute áramkörben, és akár 100-ig, prémium szintű [ExpressRoute-áramkörön](#expressroute-premium). 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Több Azure-virtuális hálózatokat tartalmazó előfizetéssel rendelkezem. Egyetlen ExpressRoute-kapcsolatcsoporthoz különböző előfizetésben található virtuális hálózat kapcsolható össze?

Igen. Akár 10 virtuális hálózatot is összekapcsolhat ugyanabban az előfizetésben, mint az áramkör vagy a különböző előfizetések egyetlen ExpressRoute áramkör használatával. Ez a korlátozás az ExpressRoute prémium funkció engedélyezésével növelhető.

További információ: ExpressRoute- [áramkör megosztása több előfizetés között](expressroute-howto-linkvnet-arm.md).

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Különböző Azure Active Directory-bérlők vagy a nagyvállalati szerződés regisztrációk társított több Azure-előfizetéssel rendelkezem. A különálló bérlők és a egy ExpressRoute-kapcsolatcsoport nem az azonos bérlőben vagy regisztráció a regisztrációk virtuális hálózat kapcsolható össze?

Igen. ExpressRoute-engedélyek is kiterjedhet előfizetés, a bérlő és a beléptetési határok szükséges további konfiguráció nélkül. 

További információ: ExpressRoute- [áramkör megosztása több előfizetés között](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>Virtuális hálózatok egymástól elkülönített azonos kapcsolatcsoporthoz csatlakoznak?

Nem. Az útválasztási szempontjából minden, az ugyanahhoz az ExpressRoute-kapcsolatcsoporthoz kapcsolt virtuális hálózatok ugyanazon útválasztási tartomány részét képezik és nem különítve egymástól. Ha útvonal elkülönítési van szüksége, hozzon létre egy külön az ExpressRoute-kapcsolatcsoport szeretné.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Használhatok egy virtuális hálózatot több ExpressRoute-kapcsolatcsoporthoz csatlakozik?

Igen. Egyetlen virtuális hálózatot legfeljebb négy ExpressRoute-áramkörhöz kapcsolhat ugyanazon vagy különböző egyenrangú helyeken. 

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>A saját virtuális hálózatok összekötése ExpressRoute-kapcsolatcsoportokkal csatlakoztatott elérhető számomra az interneten?

Igen. Ha nincs közzététel az alapértelmezett útvonalat (0.0.0.0/0) vagy Internet útvonal előtagot a BGP-munkameneten keresztül, csatlakozhat az internethez egy ExpressRoute-kapcsolatcsoporthoz kapcsolt virtuális hálózat.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Letilthatom internetkapcsolat virtuális hálózatokhoz csatlakozó ExpressRoute-Kapcsolatcsoportok?

Igen. Alapértelmezett felé haladó útvonalak meghirdetéséhez (0.0.0.0/0) letiltása az összes üzembe helyezett virtuális hálózaton belüli virtuális gépek internetkapcsolat, és a kimenő összes forgalom az ExpressRoute-kapcsolatcsoport keresztül irányítani.

Alapértelmezett útvonalak meghirdetése akkor, ha azt vissza a helyi kényszerített forgalmát a Microsoft társviszony-létesítés (például az Azure storage és SQL DB) keresztül felajánlott szolgáltatásokhoz. Meg kell konfigurálnia az útválasztókat abból való visszatéréshez forgalmat az Azure-ba, a Microsoft társviszony-létesítési útvonal használatával vagy az interneten keresztül. Ha engedélyezte, hogy egy végpontot a szolgáltatáshoz, a szolgáltatás a forgalom nem kényszeríti a helyszíni eredetű forgalomhoz. A forgalom az Azure gerinchálózatán belül marad. További információ a szolgáltatási végpontokról: [virtuális hálózati szolgáltatás végpontjai](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json)

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Az ugyanahhoz az ExpressRoute-kapcsolatcsoporthoz kapcsolt virtuális hálózatok közötti kommunikáció egymással?

Igen. A virtuális hálózatok ugyanahhoz az ExpressRoute-kapcsolatcsoporthoz csatlakozik üzembe helyezett virtuális gépek kommunikálhatnak egymással.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>Használható az expressroute-tal együtt virtuális hálózatokhoz helyek közötti kapcsolat?

Igen. Az ExpressRoute egyszerre is használható, a site-to-site VPN-eket. Lásd: [a ExpressRoute és a helyek közötti egyidejű kapcsolatok konfigurálása](expressroute-howto-coexist-resource-manager.md).

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Miért van a virtuális hálózat az ExpressRoute-átjárót társított nyilvános IP-cím?

A nyilvános IP-cím csak belső kezelésére szolgál, és nem jelent a virtuális hálózat egy biztonsági kockázatokat.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>E meghirdethet útvonalak száma korlátozva van?

Igen. Legfeljebb 4000 útvonal címelőtagjainak magánhálózati társviszony-létesítés és Microsoft társviszony-létesítéshez 200 fogadunk. 10 000 útvonalak privát társviszony-létesítéshez az ExpressRoute prémium funkció engedélyezésével növelhető.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Tudom a BGP-munkamenetben meghirdetni is IP-címtartományok korlátozások vannak?

A Microsoft társviszony-létesítési BGP-munkamenet nem fogadunk el saját előtagok (RFC1918). A Microsofton és a privát társon is elfogadunk minden előtag-méretet (legfeljebb/32).

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
* Virtuális hálózatok és az ExpressRoute globális elérhetőségű is engedélyezhetők az ExpressRoute-Kapcsolatcsoportok kapcsolatok számának növelése (az alapértelmezett érték 10-es). További információ: [ExpressRoute-korlátok](#limits) tábla.
* Csatlakozás az Office 365-hoz
* Globális kapcsolódás a Microsoft core hálózaton keresztül. Most már kapcsolat egy geopolitikai régióban található virtuális hálózat az ExpressRoute-kapcsolatcsoport egy másik régióban.<br>
    **Példák**

    *  Egy Nyugat-európai régióban létrehozott ExpressRoute-kapcsolatcsoport létrehozása a szilícium-völgy, virtuális hálózatok közötti kapcsolat. 
    *  A Microsoft társviszony-létesítés más geopolitikai régiók előtagokat hirdet meg, hogy a kapcsolatcsoport a szilícium-völgy, például SQL Azure-Európa Nyugat-India csatlakozhat.


### <a name="limits"></a>Hány virtuális hálózatok-és Global Reach ExpressRoute-kapcsolat engedélyezhető a ExpressRoute-áramkörön, ha engedélyezve van a ExpressRoute Premium?

Az alábbi táblázatokban az ExpressRoute-korlátok, és az ExpressRoute-kapcsolatcsoportonként virtuális hálózatok és az ExpressRoute globális elérhetőségű kapcsolatok száma:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Hogyan engedélyezhető az ExpressRoute premium?

Az ExpressRoute prémium szolgáltatásokat is engedélyezhető, ha a szolgáltatás engedélyezve van, és tudja leállítani a kapcsolatcsoport állapota frissítésével. Az ExpressRoute premium engedélyezheti a kapcsolatcsoport létrehozáskor, vagy meghívhatja a REST API vagy PowerShell-parancsmagot.

### <a name="how-do-i-disable-expressroute-premium"></a>Hogyan tilthatom le a az ExpressRoute premium?

Az ExpressRoute premium letilthatja a REST API vagy a PowerShell-parancsmag meghívásával. Győződjön meg arról, hogy méretezte kapcsolódási igényeinek teljesítéséhez az alapértelmezés szerinti korlátozásoknak az ExpressRoute premium letiltása előtt. Ha a kihasználtság meghaladja az alapértelmezett korlátai szerint skálázható, tiltsa le az ExpressRoute premium irányuló kérelem sikertelen lesz.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Is szeretnék kiválasztani, a prémium szintű szolgáltatás készletből kívánt szolgáltatásokat?

Nem. Az funkciók nem választhat. Ha bekapcsolja az ExpressRoute premium engedélyezzük összes funkciót.

### <a name="how-much-does-expressroute-premium-cost"></a>Mennyibe az ExpressRoute prémium szintű szolgáltatás?

Tekintse meg a [díjszabás részleteit](https://azure.microsoft.com/pricing/details/expressroute/) .

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>Kell fizetnem a standard ExpressRoute díjain felül kivetett díjak az ExpressRoute prémium?

Igen. Az ExpressRoute prémium szintű díjak vonatkoznak, és az ExpressRoute kapcsolatcsoport a kapcsolatszolgáltató által igényelt díjakat felett.

## <a name="expressroute-local"></a>Helyi ExpressRoute
### <a name="what-is-expressroute-local"></a>Mi az a ExpressRoute local?
A helyi ExpressRoute a standard SKU és a prémium SKU mellett a ExpressRoute áramkör SKU-jának. A helyi legfontosabb funkciója, hogy egy ExpressRoute-társítási helyen lévő helyi áramkör csak egy vagy két Azure-régióhoz fér hozzá, vagy közel azonos metróhoz. Ezzel szemben a standard szintű áramkör hozzáférést biztosít a geopolitikai területeken lévő összes Azure-régióhoz, valamint egy prémium szintű áramkört az összes Azure-régióhoz. 

### <a name="what-are-the-benefits-of-expressroute-local"></a>Milyen előnyökkel jár a ExpressRoute helyi?
A standard vagy prémium szintű ExpressRoute-áramkör esetében a kimenő adatforgalomért nem kell külön fizetnie a kimenő adatforgalomért a ExpressRoute helyi áramköre számára. Más szóval a ExpressRoute helyi díja adatátviteli díjakat is tartalmaz. A helyi ExpressRoute sokkal gazdaságosabb megoldás, ha nagy mennyiségű adat átvitelére van szükség, és az adatokhoz privát kapcsolaton keresztül kapcsolódhat a kívánt Azure-régiók közelében lévő ExpressRoute-partneri helyhez. 

### <a name="what-features-are-available-and-what-are-not-on-expressroute-local"></a>Milyen funkciók érhetők el, és mi nem a ExpressRoute helyi rendszeren?
A standard ExpressRoute áramkörhöz képest a helyi áramkör ugyanazokkal a szolgáltatásokkal rendelkezik, mint a következő:
* Az Azure-régiókhoz való hozzáférés hatóköre a fent leírtak szerint
* A ExpressRoute Global Reach helyi rendszeren nem érhető el

A helyi ExpressRoute is ugyanazok a korlátozások vonatkoznak az erőforrásokra (például a virtuális hálózatok száma) a standard értékre. 

### <a name="where-is-expressroute-local-available-and-which-azure-regions-is-each-peering-location-mapped-to"></a>Hol van a ExpressRoute helyi elérhetősége, és mely Azure-régiók vannak leképezve az összes társítási helyhez?
A ExpressRoute local (helyi) lehetőség azon a társítási helyeken érhető el, ahol egy vagy két Azure-régiót zártak be. Nem érhető el olyan egyenrangú helyen, ahol az adott államban, tartományban vagy országban nem található Azure-régió. Tekintse meg a pontos leképezéseket a [helyszínek lapon](expressroute-locations-providers.md).  

## <a name="expressroute-for-office-365"></a>Az Office 365 ExpressRoute

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services"></a>Hogyan hozhatok létre egy ExpressRoute-kapcsolatcsoporthoz szeretne csatlakozni az Office 365-szolgáltatásokhoz?

1. Tekintse át a [ExpressRoute előfeltételek oldalát](expressroute-prerequisites.md) , és győződjön meg arról, hogy megfelel a követelményeknek.
2. A kapcsolati igények teljesítésének biztosításához tekintse át a szolgáltatók és a helyszínek listáját a [ExpressRoute-partnerek és-helyszínek](expressroute-locations.md) című cikkben.
3. Tervezze meg a kapacitásra vonatkozó követelményeket az [Office 365 hálózati tervezésének és teljesítmény-finomhangolásának](https://aka.ms/tune/)áttekintésével.
4. A munkafolyamatokban felsorolt lépéseket követve állíthatja be a kapcsolati [ExpressRoute munkafolyamatait az áramköri kiépítési és az áramköri állapotokhoz](expressroute-workflows.md).

> [!IMPORTANT]
> Győződjön meg arról, hogy engedélyezte az ExpressRoute prémium bővítmény konfigurálása az Office 365-szolgáltatásokhoz való kapcsolódás során.
> 
> 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services"></a>Támogathatja a meglévő ExpressRoute-áramkörök az Office 365-szolgáltatásokhoz való kapcsolódást?

Igen. A meglévő ExpressRoute-kapcsolatcsoport beállítható úgy, hogy támogatja az Office 365-szolgáltatásokhoz való kapcsolódás. Győződjön meg arról, hogy rendelkezik-e elegendő kapacitással az Office 365-szolgáltatásokhoz és, hogy engedélyezte a premium bővítményt. [Az Office 365 hálózati tervezése és teljesítmény-finomhangolása](https://aka.ms/tune/) segít megtervezni a kapcsolati igényeket. Lásd még: [ExpressRoute-kör létrehozása és módosítása](expressroute-howto-circuit-classic.md).

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>Mely Office 365 ExpressRoute-kapcsolaton keresztül elérhető szolgáltatások?

Az ExpressRoute-on keresztül támogatott szolgáltatások naprakész listáját az [Office 365 URL-címei és IP-címtartományok](https://aka.ms/o365endpoints) lapján tekintheti meg.

### <a name="how-much-does-expressroute-for-office-365-services-cost"></a>Mennyibe kerül az ExpressRoute az Office 365-szolgáltatások költség?

Office 365-szolgáltatások prémium bővítmény engedélyezésének megkövetelése. A költségekért tekintse meg a [díjszabás részleteit ismertető oldalt](https://azure.microsoft.com/pricing/details/expressroute/) .

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>Mely régiókban támogatott ExpressRoute az Office 365-höz a?

További információ: [ExpressRoute-partnerek és-helyszínek](expressroute-locations.md) .

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Érhetem el az Office 365 az interneten keresztül, akkor is, ha ExpressRoute lett konfigurálva a szervezetem számára?

Igen. Az Office 365-szolgáltatásvégpontokat az interneten keresztül érhető el, annak ellenére, hogy a hálózat ExpressRoute van konfigurálva. Ha a hálózat, a helyen van konfigurálva, az Office 365 szolgáltatás expressroute-on keresztül csatlakozni ellenőrizze a szervezet hálózati csapatával.

### <a name="how-can-i-plan-for-high-availability-for-office-365-network-traffic-on-azure-expressroute"></a>Hogyan tudok megtervezheti a magas rendelkezésre állás az Office 365-forgalmát az Azure expressroute-on?
Tekintse meg a [magas rendelkezésre állásra és a feladatátvételre](https://aka.ms/erhighavailability) vonatkozó javaslatot az Azure ExpressRoute

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>ExpressRoute-kapcsolatcsoporton Azure US Government elérhető számomra az Office 365 US Government Community (GCC) szolgáltatások?

Igen. Az Office 365 GCC Szolgáltatásvégpontok az Azure US Government expressroute-on keresztül érhetők el. Azonban Ön először nyisson egy támogatási jegyet az előtagokat hirdet meg a Microsoftnak szeretne biztosít az Azure Portalon. Az Office 365 GCC-szolgáltatásokhoz való kapcsolódás után fog állni a támogatási jegy jön létre. 

## <a name="route-filters-for-microsoft-peering"></a>Microsoft társviszony-létesítés útvonalszűrőinek

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>E vagyok ne tudják bekapcsolni a Microsoft társviszony-létesítés először, milyen útvonalak látható?

Nem láthatja az összes olyan esetleges útvonalat. Hogy egy útvonalszűrőhöz csatlakoztatása kapcsolatcsoporthoz előtag hirdetmények elindításához. Útmutatásért lásd: [útválasztási szűrők konfigurálása a Microsoft-partnerek](how-to-routefilter-powershell.md)számára.

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Be van kapcsolva a Microsoft társviszony-létesítés és most már a tapasztalataimat szeretném jelölje be az Exchange online-hoz, de azt lehetővé teszi számomra, hogy I nem vagyok jogosult mindez hiba.

Útvonalszűrők használatakor minden ügyfél bekapcsolhatja a Microsoft társviszony-létesítés. Azonban az Office 365-szolgáltatások felhasználásához, továbbra is szeretné az Office 365 által engedélyezett beolvasása.

### <a name="i-enabled-microsoft-peering-prior-to-august-1-2017-how-can-i-take-advantage-of-route-filters"></a>A Microsoft társviszony-létesítés 2017. augusztus 1., hogyan használhatom az útvonalszűrők engedélyezve

A meglévő áramkör továbbra is meghirdeti az Office 365 előtagjait. Ha az Azure-beli nyilvános előtagok hirdetményeit ugyanazon Microsoft-társon szeretné felvenni, akkor létrehozhat egy útvonal-szűrőt, kiválaszthatja a meghirdetett szolgáltatásokat (beleértve az Office 365 szolgáltatás (oka) t), és csatolja a szűrőt a Microsoft-partneri kapcsolathoz. Útmutatásért lásd: [útválasztási szűrők konfigurálása a Microsoft-partnerek](how-to-routefilter-powershell.md)számára.

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>A Microsoft társviszony-létesítés egy helyen van, most már a tapasztalataimat szeretném az engedélyezéshez egy másik helyen, és nem látok bármely előtagok.

* Microsoft társviszony-létesítés voltak beállítva a 2017. augusztus 1. ExpressRoute-Kapcsolatcsoportok az összes szolgáltatás előtagkészletet hirdeti meg a Microsoft társviszony-létesítéshez, akkor is, ha nincsenek meghatározva útvonalszűrők fog rendelkezni.

* Microsoft társviszony-létesítésre vannak konfigurálva, vagy 2017. augusztus 1. után az ExpressRoute-Kapcsolatcsoportok, nem rendelkezik minden olyan előtagok mindaddig, amíg egy útvonalszűrőhöz csatolva van a kapcsolatcsoport hirdetve. Nincs előtagok alapértelmezés szerint megjelenik.

## <a name="expressRouteDirect"></a>Közvetlen ExpressRoute

[!INCLUDE [ExpressRoute Direct](../../includes/expressroute-direct-faq-include.md)]

## <a name="globalreach"></a>Global Reach

[!INCLUDE [Global Reach](../../includes/expressroute-global-reach-faq-include.md)]
