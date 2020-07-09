---
title: Interconnect Kínával az Azure Virtual WAN és a Secure hub használatával
description: Ismerje meg a virtuális WAN-alapú, automatikusan méretezhető ág-ág kapcsolatot, az elérhető régiókat és a partnereket.
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: sukishen
ms.openlocfilehash: d89a3c65eb8d8bffd4cf87160286d1905bd1ba5b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83747610"
---
# <a name="interconnect-with-china-using-azure-virtual-wan-and-secure-hub"></a>Interconnect Kínával az Azure Virtual WAN és a Secure hub használatával

A közös autóipari, gyártási, logisztikai vagy egyéb intézmények, például a nagykövetségek esetében gyakran a Kínával való összekapcsolással kapcsolatos kérdésekkel foglalkozunk. Ezek a tökéletesítések elsősorban a Cloud Services, például az Office 365, az Azure Global Services vagy a kínai összekötő-ágaknak az ügyfél gerincével való használatához szükségesek.

A legtöbb esetben az ügyfelek nagy késéssel, alacsony sávszélességgel, instabil kapcsolattal és a Kínával (például Európa vagy a Egyesült Államok) való kapcsolódás magas költségeivel küzdenek.

Ezen küzdelmek egyik oka a "kínai nagy tűzfal", amely megvédi az Internet kínai részét, és szűri a forgalmat Kínában. A kínai kontinensről a Kínából kívülre irányuló forgalom csaknem minden, a különleges adminisztrációs zónák, például Hongkong és Makaó esetében, a nagyszerű tűzfalon halad át. A Hongkongon és Makaón futó forgalom nem éri el a nagy tűzfalat a teljes körű működésben, hanem a nagy tűzfal egy részhalmaza kezeli.

![Szolgáltatói összekötő](./media/interconnect-china/provider.png)

A virtuális WAN használatával az ügyfél több teljesítményű és stabil kapcsolatot létesíthet Microsoft Cloud szolgáltatásokkal, és kapcsolatot létesít a vállalati hálózattal a kínai kiberbiztonsági törvény feltörése nélkül.

## <a name="requirements-and-workflow"></a><a name="requirements"></a>Követelmények és munkafolyamat

Ha szeretne megfelelni a kínai kiberbiztonsági törvénynek, meg kell felelnie bizonyos feltételeknek.

Először is együtt kell működnie egy olyan hálózattal és INTERNETSZOLGÁLTATÓval, aki a kínai ICP (Internet Content Provider) licenccel rendelkezik. A legtöbb esetben a következő szolgáltatók egyike jelenik meg:

* Kínai távközlési globális Kft.
* China Mobile Ltd.
* China Unicom Ltd.
* PCCW Global Ltd.
* Hong Kong Telecom Kft.

A szolgáltatótól és az igényeitől függően az alábbi hálózati kapcsolati szolgáltatások egyikét kell megvásárolnia az ágak Kínában való összekapcsolásához.

* MPLS-/IPVPN-hálózat 
* A szoftver által meghatározott WAN (SDWAN)
* Dedikált internet-hozzáférés

Ezután el kell fogadnia a szolgáltatót, hogy kitörést nyújtson a Microsoft globális hálózatának és annak peremhálózati bejövő hálózati forgalom Hongkongnak, nem pedig Pekingben vagy Sanghajban. Ebben az esetben Hongkong nagyon fontos, mert a fizikai kapcsolata és helye Kína.

Habár a legtöbb ügyfél úgy gondolja, hogy a Szingapúr for Interconnect szolgáltatást használja, a legjobb eset, mert a térképhez közeledő Kínában keres, ez nem igaz. Ha követi a hálózati Fiber Maps szolgáltatást, szinte minden hálózati kapcsolat a pekingi, a sanghaji és a Hongkongban is elérhető. Ez lehetővé teszi, hogy Hongkong jobb helyet válasszon a Kínával való összekapcsolódáshoz.

A szolgáltatótól függően különböző szolgáltatási ajánlatokat kaphat. Az alábbi táblázat a szolgáltatók és az általuk kínált szolgáltatás példáját mutatja be a cikk írásának időpontjában található információk alapján.

| Szolgáltatás | Szolgáltatói példák |
| --- | --- |
| MPLS/IPVPN hálózat |PCCW, kínai távközlési globális |
|SDWAN| PCCW, kínai távközlési globális|
| Dedikált internet-hozzáférés | PCCW, Hong Kong Telecom, Kína mobil|

A szolgáltatóval megállapodhat arról, hogy a következő két megoldás közül melyiket használhatja a Microsoft globális gerincének elérésére:

* Microsoft Azure ExpressRoute leállítása Hongkongban. Ez lenne a MPLS/IPVPN használata esetén. Jelenleg csak az egyetlen ICP-licenc szolgáltatója, a ExpressRoute és Hongkong között a kínai távközlési globális. Azonban a többi szolgáltatóval is beszélgethet, ha olyan felhőalapú Exchange-szolgáltatókat használnak, mint a Megaport vagy a felhő. További információ: [ExpressRoute connectivity Providers](../expressroute/expressroute-locations-providers.md#partners).

* A dedikált internet-hozzáférés közvetlenül a következő internetes Exchange-pontok egyikén vagy egy magánhálózati összekötőn keresztül érhető el.

A következő lista a Hongkongban lehetséges internetes cseréket mutatja be:

* AMS-IX Hong Kong
* BBIX Hong Kong
* Equinix Hong Kong
* HKIX

Ha ezt a kapcsolatot használja, a Microsoft-szolgáltatásokhoz tartozó következő BGP-ugrásnak a Microsoft autonóm rendszer számának kell lennie (AS #) 8075. Ha egyetlen helyet vagy SDWAN-megoldást használ, az a választás lenne.

Akárhogy is, javasoljuk, hogy a kínai anyaországban egy második és egy hagyományos internetes kitörés is rendelkezésre áll. Ezt a forgalmat a nagyvállalati forgalom és a felhőalapú szolgáltatások, például a Microsoft 365 és az Azure közötti forgalom, valamint a törvény által szabályozott internetes forgalom felosztására kell osztani.

Kínában a megfelelő hálózati architektúra a következő példához hasonlóan néz ki:

![Több ág](./media/interconnect-china/multi-branch.png)

Ebben a példában a Microsoft Global bejövő hálózati forgalom Hongkonggal való kapcsolattal rendelkezik, mostantól elkezdheti kihasználni az [Azure Virtual WAN globális átviteli architektúráját](virtual-wan-global-transit-network-architecture.md) és további szolgáltatásait, például az Azure Secure Virtual WAN hub szolgáltatást, hogy a szolgáltatásokat és az adatközpontot a Kínában kívüli ágakhoz és adatközpontokhoz is használja.

## <a name="hub-to-hub-communication"></a><a name="hub-to-hub"></a>Központ – központ kommunikáció

Ebben a szakaszban a virtuális WAN hub – hub kommunikációt használjuk az interconnecthez. Ebben a forgatókönyvben egy új virtuális WAN hub-erőforrást hoz létre, amely egy Hongkongban található virtuális WAN-központhoz, más régióhoz, illetve olyan régióhoz csatlakozik, ahol már rendelkezik Azure-erőforrásokkal, vagy ahol szeretne csatlakozni.

A minta architektúra A következő példához hasonlóan néz ki:

![WAN-minta](./media/interconnect-china/sample.png)

Ebben a példában a kínai ágak VPN-vagy MPLS-kapcsolatok használatával csatlakoznak az Azure Cloud China-hoz és egymáshoz. A globális szolgáltatásokhoz csatlakozni kívánó ágak a közvetlenül a Hongkonghoz csatlakozó MPLS-vagy internetalapú szolgáltatásokat használják. Ha a ExpressRoute-t Hongkongban, illetve a másik régióban is használni szeretné, konfigurálnia kell a [ExpressRoute Global REACH](../expressroute/expressroute-global-reach.md) a ExpressRoute-áramkörök összekapcsolásához.

A ExpressRoute Global Reach egyes régiókban nem érhető el. Ha például Brazíliával vagy Indiával szeretné összekapcsolni a kapcsolatot, a [felhőalapú Exchange-szolgáltatókat](../expressroute/expressroute-locations.md#connectivity-through-exchange-providers) kell használnia az útválasztási szolgáltatások biztosításához.

Az alábbi ábrán a forgatókönyv mindkét példája látható.

![Global Reach](./media/interconnect-china/global.png)

## <a name="secure-internet-breakout-for-office-365"></a><a name="secure"></a>Biztonságos internetes breakout az Office 365-hoz

Egy másik szempont a hálózati biztonság, valamint a kínai és a virtuális WAN által létesített, valamint az ügyfél gerince közötti belépési pont naplózása. A legtöbb esetben szükség van arra, hogy Hongkongban az interneten keresztül közvetlenül elérje a Microsoft Edge hálózatot, és ezzel a Microsoft 365-szolgáltatásokhoz használt Azure-beli előtérben lévő kiszolgálókat.

A virtuális WAN esetében mindkét forgatókönyv esetében kihasználhatja az [Azure Virtual WAN biztonságos hubot](../firewall-manager/secured-virtual-hub.md). A Azure Firewall Manager használatával a normál virtuális WAN-hubot egy biztonságos hubhoz módosíthatja, majd üzembe helyezheti és felügyelheti az adott központban található Azure Firewall.

Az alábbi ábrán egy példa látható erre a forgatókönyvre:

![Internetes breakout webes és Microsoft-szolgáltatások forgalmához](./media/interconnect-china/internet.png)

## <a name="architecture-and-traffic-flows"></a><a name="traffic"></a>Architektúra és forgalmi folyamatok

A Hongkonghoz való kapcsolódásra vonatkozó döntéstől függően az általános architektúra némileg megváltozhat. Ez a szakasz három elérhető architektúrát mutat be különböző, VPN-vagy SDWAN-és/vagy ExpressRoute-kombinációkkal.

Az összes lehetőség az Azure Virtual WAN biztonságos hub használatát használja a közvetlen M365-kapcsolathoz Hongkongban. Ezek az architektúrák az [office 365 multi-geo](https://docs.microsoft.com/office365/enterprise/office-365-multi-geo) megfelelőségi követelményeit is támogatják, és a forgalmat a következő Office 365 bejárati hely közelében tartják. Ennek eredményeképpen a Microsoft 365 Kínában való használatának is javulást jelent.

Ha az Azure Virtual WAN-t internetkapcsolattal együtt használja, minden kapcsolat kihasználhatja az olyan további szolgáltatásokat, mint a [Microsoft Azure-társas szolgáltatások (Maps)](https://docs.microsoft.com/azure/peering-service/about). A MAPS szolgáltatás a Microsoft globális hálózata felé irányuló forgalom optimalizálására készült a harmadik féltől származó internetszolgáltatótól.

### <a name="option-1-sdwan-or-vpn"></a><a name="option-1"></a>1. lehetőség: SDWAN vagy VPN

Ez a szakasz egy olyan kialakítást mutat be, amely SDWAN vagy VPN-t használ a Hongkong és más ágak számára. Ez a beállítás a használat és a forgalom folyamatát mutatja be, ha a virtuális WAN-gerinc mindkét helyén tiszta internetkapcsolatot használ. Ebben az esetben a rendszer dedikált internet-hozzáférés vagy egy ICP Provider SDWAN-megoldás használatával kezdeményezi a kapcsolatot Hongkongban. Más ágak tiszta internetes vagy SDWAN megoldásokat is használnak.

![Kína – Hongkong-forgalom](./media/interconnect-china/china-traffic.png)

Ebben az architektúrában minden hely a Microsoft globális hálózatához csatlakozik a VPN és az Azure Virtual WAN használatával. A helyek és a Hongkong közötti forgalom továbbítása a Microsoft-hálózaton keresztül történik, és csak az utolsó mérföldes internetkapcsolatot használja.

### <a name="option-2-expressroute-and-sdwan-or-vpn"></a><a name="option-2"></a>2. lehetőség: ExpressRoute és SDWAN vagy VPN

Ez a szakasz egy olyan kialakítást mutat be, amely ExpressRoute használ Hongkongban és más ágakban VPN-/SDWAN-ágakkal. Ez a lehetőség a Hongkongban és a SDWAN vagy a VPN-en keresztül csatlakozó más ágakban lezárt és ExpressRoute használatát mutatja be. A Hongkongban található ExpressRoute jelenleg a szolgáltatók rövid listájára korlátozódnak, amelyek az [Express Route-partnerek](../expressroute/expressroute-locations-providers.md#global-commercial-azure)listáján találhatók.

![Kína – Hongkong forgalmi ExpressRoute](./media/interconnect-china/expressroute.png)

A Kínából, például Dél-Koreában vagy Japánban is megszüntetheti a ExpressRoute. A megfelelőség, a szabályozás és a késés miatt azonban Hongkong jelenleg a legjobb választás.

### <a name="option-3-expressroute-only"></a><a name="option-3"></a>3. lehetőség: csak ExpressRoute

Ez a szakasz egy olyan kialakítást mutat be, amelyben a ExpressRoute a Hongkong és más ágak esetében használatos. Ez a beállítás mindkét végén megjeleníti az összekötőt a ExpressRoute használatával. Itt van egy másik adatforgalom, mint a másik. A Microsoft 365 forgalom az Azure Virtual WAN biztonságos hubhoz áramlik, és onnan a Microsoft Edge-hálózatra és az internetre kerül.

Az összekapcsolt ágakra vagy a kínai helyekre irányuló forgalom az adott architektúrán belül más megközelítést fog követni. A virtuális WAN jelenleg nem támogatja a ExpressRoute és a ExpressRoute átvitelét. A forgalom a ExpressRoute Global Reach vagy a harmadik féltől származó összekötőt a virtuális WAN-központ átadása nélkül fogja kihasználni. Közvetlenül az egyik Microsoft Enterprise Edge (MSEE) rendszerről egy másikra áramlik.

![Az ExpressRoute Global Reach](./media/interconnect-china/expressroute-virtual.png)

Jelenleg a ExpressRoute Global Reach minden országban/régióban nem érhető el, de az Azure Virtual WAN használatával is konfigurálhat megoldást.

Beállíthat például egy ExpressRoute a Microsoft-partnerekkel, és egy VPN-alagutat is összekapcsolhat az Azure Virtual WAN-hoz. Most, hogy engedélyezte, újra áthaladt a VPN és a ExpressRoute közötti átvitel Global Reach és külső szolgáltató és szolgáltatás nélkül, például a Megaport Cloud.

## <a name="next-steps"></a>További lépések

További információt a következő cikkekben talál:

* [Globális átviteli hálózati architektúra az Azure Virtual WAN-ral](virtual-wan-global-transit-network-architecture.md)

* [Virtuális WAN-központ létrehozása](virtual-wan-site-to-site-portal.md)

* [Virtuális WAN biztonságos központ konfigurálása](../firewall-manager/secure-cloud-network.md)

* [Az Azure peering Service előzetes verziójának áttekintése](https://docs.microsoft.com/azure/peering-service/about)
