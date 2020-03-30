---
title: Kapcsolatfelvétel Kínával az Azure Virtual WAN és a biztonságos központ használatával
description: Ismerje meg a Virtual WAN automatizált skálázható ág-ág kapcsolat, a rendelkezésre álló régiók és partnerek.
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: sukishen
ms.openlocfilehash: e05aa6e8f656001eb9eb2a3717dde6c16f030e33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337155"
---
# <a name="interconnect-with-china-using-azure-virtual-wan-and-secure-hub"></a>Kapcsolatfelvétel Kínával az Azure Virtual WAN és a Secure Hub használatával

Ha megnézzük a közös autóipar, gyártás, logisztikai iparágak vagy más intézetek, mint a nagykövetségek, gyakran az a kérdés, hogyan lehetne javítani az összekapcsolás Kínával. Ezek a fejlesztések többnyire relevánsak a Felhőszolgáltatások, például az Office 365, az Azure Global Services vagy a Kínán belüli ágak és az ügyfelek gerincének összekapcsolása szempontjából.

Az esetek többségében az ügyfelek nagy késésekkel, alacsony sávszélességgel, instabil kapcsolattal és Kínán kívüli (például Európával vagy az Egyesült Államokkal) való csatlakozással küzdenek.

Ennek oka ezek a küzdelmek a "Nagy Tűzfal Kína", amely védi a kínai része az internet és a szűrők forgalmat Kínába. Szinte minden forgalom fut Kína szárazföldi kívül Kína, kivéve a különleges adminisztrációs zónák, mint Hong Kong és Makaó, átmegy a Nagy Tűzfal. A Hong Kongon és Makaón áthaladó forgalom nem éri el teljes erővel a Nagy Tűzfalat, hanem a Nagy Tűzfal egy része kezeli.

![Szolgáltató összekapcsolása](./media/interconnect-china/provider.png)

A Virtual WAN használatával az ügyfél a kínai kiberbiztonsági törvény megszegése nélkül hozhat létre hatékonyabb és stabilabb kapcsolatot a Microsoft Cloud Services szolgáltatással és a vállalati hálózattal.

## <a name="requirements-and-workflow"></a><a name="requirements"></a>Követelmények és munkafolyamat

Ha meg szeretné felelni a kínai kiberbiztonsági törvénynek, meg kell felelnie bizonyos feltételeknek.

Először együtt kell működnie egy olyan hálózattal és internetszolgáltatóval, amely Rendelkezik Egy ICP (Internet Content Provider) licenccel Kínában. A legtöbb esetben az alábbi szolgáltatók valamelyike lesz a következő:

* Kína Telecom Global Ltd.
* China Mobil Kft.
* Kína Unicom Kft.
* PCCW Globális Kft.
* Hong Kong Telecom Kft.

A szolgáltatótól és az igényeitől függően most meg kell vásárolnia az alábbi hálózati kapcsolati szolgáltatások egyikét a Kínán belüli fiókok összekapcsolásához.

* MPLS/IPVPN-hálózat 
* Szoftveresen definiált WAN (SDWAN)
* Dedikált internet-hozzáférés

Ezután meg kell állapodnia a szolgáltatóval, hogy a Microsoft Global Network és az Edge Network Hong Kong-ban, nem Pekingben vagy Sanghajban. Ebben az esetben, Hong Kong nagyon fontos, mert a fizikai kapcsolat és helyét Kínával.

Míg a legtöbb ügyfél úgy gondolja, szingapúri összekapcsolására a legjobb esetben, mert úgy néz ki, közelebb Kínához, ha ránézünk a térképen, ez nem igaz. Ha követi a hálózati fiber térképek, szinte az összes hálózati csatlakozik megy keresztül Peking, Sanghaj és Hong Kong. Ez teszi Hong Kong jobb hely választás, hogy csatlakozzanak Kínához.

A szolgáltatótól függően különböző szolgáltatási ajánlatokat kaphat. Az alábbi táblázat egy példát mutat be a szolgáltatókra és az általuk kínált szolgáltatásra, a cikk megírásának időpontjában található információk alapján.

| Szolgáltatás | Példák szolgáltatóra |
| --- | --- |
| MPLS/IPVPN hálózat |PCCW, Kína Telecom Globális |
|SDWAN között| PCCW, Kína Telecom Globális|
| Dedikált internet-hozzáférés | PCCW, Hong Kong Telecom, Kína Mobil, PCCW |

A szolgáltatónál megállapodhat amicrosoft globális gerincének eléréséhez az alábbi két megoldás közül:

* A Microsoft Azure ExpressRoute beszerzése Hongkongban leállítva. Ez lenne a helyzet az MPLS / IPVPN használata esetében. Jelenleg csak az egyetlen ICP licencszolgáltató ExpressRoute Hong Kong a China Telecom Global. Azonban akkor is beszélhetnek a többi szolgáltatóval, ha olyan felhőalapú csereszolgáltatókat használnak, mint a Megaport vagy az InterCloud. További információ: [ExpressRoute-kapcsolatszolgáltatók](../expressroute/expressroute-locations-providers.md#partners).

* Dedikált internet-hozzáférés használata közvetlenül az alábbi internetes exchange pontok egyikén, vagy magánhálózati összeköttetés használata.

Az alábbi lista a Hongkongban lehetséges internetes cseréket mutatja be:

* AMS-IX Hongkong
* BBIX Hongkong
* Equinix Hongkong
* HKIX

A csatlakozás használataesetén a Microsoft-szolgáltatások következő BGP-ugrásának Microsoft Autonomous System Number (AS#) 8075-nek kell lennie. Ha egyetlen helyet vagy SDWAN megoldást használ, az a kapcsolat választása lenne.

Akárhogy is, még mindig javasoljuk, hogy van egy második és rendszeres Internet Breakout a kínai anyaország. Ez a nagyvállalati forgalom és a felhőszolgáltatások, például a Microsoft 365 és az Azure közötti forgalom, valamint a jogszabály által szabályozott internetes forgalom közötti forgalom felosztása.

A Kínán belüli megfelelő hálózati architektúra a következő példához hasonlóan nézhet ki:

![Több ág](./media/interconnect-china/multi-branch.png)

Ebben a példában, amelynek egy kapcsolat a Microsoft Global Network Hong Kong, most már elkezdheti kihasználni az [Azure Virtual WAN global transit architektúra](virtual-wan-global-transit-network-architecture.md) és további szolgáltatások, például az Azure biztonságos Virtual WAN hub, a szolgáltatások felhasználása és az ágak és adatközpontok Kínán kívül.

## <a name="hub-to-hub-communication"></a><a name="hub-to-hub"></a>Hub-to-hub kommunikáció

Ebben a szakaszban a Virtual WAN hub-to-hub kommunikációt használjuk az összekapcsoláshoz. Ebben a forgatókönyvben egy új Virtual WAN hub-erőforrást hoz létre, amely egy hongkongi virtual WAN-központhoz, más régiókhoz, egy olyan régióhoz csatlakozik, ahol már rendelkezik Azure-erőforrásokkal, vagy ahol csatlakozni szeretne.

A mintaarchitektúra a következő példához hasonlóan nézhet ki:

![WAN-minta](./media/interconnect-china/sample.png)

Ebben a példában a kínai ágak vpn- vagy MPLS-kapcsolatok használatával csatlakoznak az Azure Cloud China-hoz és egymáshoz. A globális szolgáltatásokhoz kapcsolódó fiókok MPLS-t vagy közvetlenül Hongkonghoz kapcsolódó internetalapú szolgáltatásokat használnak. Ha az ExpressRoute-ot Hongkongban és a másik régióban is szeretné használni, konfigurálnia kell az [ExpressRoute globális elérési útját](../expressroute/expressroute-global-reach.md) a két ExpressRoute-áramkör összekapcsolásához.

Az ExpressRoute globális elérése egyes régiókban nem érhető el. Ha például brazíliával vagy Indiával kell kapcsolatba lépnie, a [felhőalapú csereszolgáltatókat](../expressroute/expressroute-locations.md#connectivity-through-exchange-providers) kell használnia az útválasztási szolgáltatások biztosításához.

Az alábbi ábra mindkét példát bemutatja erre a forgatókönyvre.

![Globális elérés](./media/interconnect-china/global.png)

## <a name="secure-internet-breakout-for-office-365"></a><a name="secure"></a>Biztonságos internetes kitörés az Office 365-ben

Egy másik szempont a hálózati biztonság, valamint a Kína és a Virtual WAN létrehozott gerincösszetevő, valamint az ügyfél gerince közötti belépési pont naplózása. A legtöbb esetben szükség van a hongkongi internetre való kitörésre, hogy közvetlenül elérd a Microsoft Edge-hálózatot, és ezzel együtt a Microsoft 365-szolgáltatásokhoz használt Azure bejárati ajtókiszolgálókat.

A Virtual WAN mindkét forgatókönyvesetén az [Azure Virtual WAN biztonságos hubját használhatja](../firewall-manager/secured-virtual-hub.md)ki. Az Azure Firewall Manager használatával módosíthatja a rendszeres Virtual WAN hub egy biztonságos hub, majd üzembe helyezése és kezelése az Azure tűzfal az adott hubon belül.

Az alábbi ábra egy példát mutat be erre a forgatókönyvre:

![Internetes kitörés webes és Microsoft-szolgáltatási forgalomesetén](./media/interconnect-china/internet.png)

## <a name="architecture-and-traffic-flows"></a><a name="traffic"></a>Építészet és forgalom

Attól függően, hogy a választás tekintetében a kapcsolatot Hong Kong, a teljes architektúra kissé változhat. Ez a szakasz három elérhető architektúrát mutat be különböző kombinációban VPN vagy SDWAN és/vagy ExpressRoute kombinációban.

Mindezek a lehetőségek az Azure Virtual WAN biztonságos hubját használják a közvetlen M365-ös kapcsolathoz Hongkongban. Ezek az architektúrák az [Office 365 Multi-Geo](https://docs.microsoft.com/office365/enterprise/office-365-multi-geo) megfelelőségi követelményeit is támogatják, és a forgalmat az Office 365 bejárati ajtajának következő helyén tartják. Ennek eredményeképpen a Microsoft 365 kínán kívüli használatának is javulása.

Ha az Azure Virtual WAN-t és az internetkapcsolatot használja, minden kapcsolat további szolgáltatásokat vehet igénybe, például a [Microsoft Azure társviszony-létesítési szolgáltatásokból (MAPS).](https://docs.microsoft.com/azure/peering-service/about) A MAPS-et arra építették, hogy optimalizálja a Microsoft Global Network harmadik fél től származó internetszolgáltatóktól érkező forgalmat.

### <a name="option-1-sdwan-or-vpn"></a><a name="option-1"></a>1. lehetőség: SDWAN vagy VPN

Ez a szakasz egy Olyan tervet tárgyal, amely SDWAN-t vagy VPN-t használ Hongkongban és más fiókokban. Ez a beállítás a virtuális WAN gerinchálózatmindkét helyén tiszta internetkapcsolat használata esetén mutatja a használatot és a forgalmat. Ebben az esetben a kapcsolat dedikált internet-hozzáféréssel vagy Egy ICP-szolgáltató SDWAN megoldással kerül Hongkongba. Más ágak is tiszta internetet vagy SDWAN Solutions-t használnak.

![Kína hong kongi forgalom](./media/interconnect-china/china-traffic.png)

Ebben az architektúrában minden hely vpn és Azure Virtual WAN használatával csatlakozik a Microsoft Global Networkhálózathoz. A helyszínek és Hongkong közötti forgalmat a Microsoft Network önúton továbbítják, és csak az utolsó mérföldön rendszeres internetkapcsolatot használ.

### <a name="option-2-expressroute-and-sdwan-or-vpn"></a><a name="option-2"></a>2. lehetőség: ExpressRoute és SDWAN vagy VPN

Ez a szakasz az ExpressRoute-ot Hongkongban és más, VPN/SDWAN ágakkal rendelkező ágakat használó látványtervet ismerteti. Ez a beállítás a Hongkongban és más SDWAN-on vagy VPN-en keresztül csatlakoztatott ágakban leállított ExpressRoute használatát mutatja. ExpressRoute Hong Kong jelenleg csak egy rövid listát a szolgáltatók, amely megtalálható a listán az [Express Route Partners](../expressroute/expressroute-locations-providers.md#global-commercial-azure).

![Kína Hong Kong forgalom ExpressRoute](./media/interconnect-china/expressroute.png)

Az ExpressRoute leállítására is lehetőség van Kínából, például Dél-Koreában vagy Japánban. De, tekintettel a megfelelőség, a szabályozás, és a látencia, Hong Kong jelenleg a legjobb választás.

### <a name="option-3-expressroute-only"></a><a name="option-3"></a>3. lehetőség: Csak ExpressRoute

Ez a szakasz egy olyan látványtervet tárgyal, amelyet az ExpressRoute hongkongi és más ágakhoz használ. Ez a beállítás mindkét végén az ExpressRoute használatával való összeköttetést jeleníti meg. Itt van egy másik forgalom, mint a másik. A Microsoft 365-forgalom az Azure virtuális WAN biztonságos hubjára, majd onnan a Microsoft Edge-hálózatba és az internetre fog áramlani.

A forgalom, hogy megy az összekapcsolt ágak, vagy tőlük a helyszínek Kínában fog követni egy másik megközelítést, hogy az építészet. Jelenleg a virtuális WAN nem támogatja az ExpressRoute expressroute-i átvitelt. A forgalom az ExpressRoute Global Reach vagy a harmadik fél közötti összeköttetést használja a virtuális WAN Hub átadása nélkül. Közvetlenül az egyik Microsoft Enterprise Edge (MSEE) rendszerből a másikba fog áramlani.

![Az ExpressRoute Global Reach](./media/interconnect-china/expressroute-virtual.png)

Jelenleg az ExpressRoute Globális elérés nem minden országban érhető el, de az Azure Virtual WAN használatával konfigurálhat egy megoldást.

Konfigurálhat például egy ExpressRoute-ot a Microsoft társviszony-létesítéssel, és csatlakozhat egy VPN-alagúthoz az azure-beli virtuális WAN-hoz való társviszony-létesítésen keresztül. Most már engedélyezte, ismét a VPN és expressroute között a Global Reach és a harmadik fél szolgáltató és szolgáltatás nélkül, például a Megaport Cloud közötti átvitelt.

## <a name="next-steps"></a>További lépések

További információt az alábbi cikkekben talál:

* [Globális tranzithálózati architektúra az Azure Virtual WAN-nal](virtual-wan-global-transit-network-architecture.md)

* [Virtuális WAN-elosztó létrehozása](virtual-wan-site-to-site-portal.md)

* [Virtuális WAN-nal védett elosztó konfigurálása](../firewall-manager/secure-cloud-network.md)

* [Az Azure-társviszony-létesítési szolgáltatás előzetes verziója – áttekintés](https://docs.microsoft.com/azure/peering-service/about)
