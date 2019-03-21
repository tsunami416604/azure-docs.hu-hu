---
title: A hálózati biztonsággal kapcsolatos fogalmait és a követelmények az Azure-ban |} A Microsoft Docs
description: A cikk ismerteti a hálózati biztonsági alapfogalmakat és a követelmények és információk alapszintű magyarázatokat a Mi az Azure a következő területeken nyújt.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: terrylan
ms.openlocfilehash: 2aabe3d1fa8a6034c2dab38c8d6fa6da4b00ac1b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58080339"
---
# <a name="azure-network-security-overview"></a>Azure-hálózat biztonsági áttekintése

Hálózati biztonság sikerült adható meg a folyamat-erőforrások védelme az illetéktelen hozzáféréstől és a támadás szabályozza a hálózati forgalom alkalmazásával. A célja annak biztosítása érdekében, hogy csak megbízható forgalom engedélyezve van-e. Az Azure támogatja az alkalmazás és szolgáltatás hálózati kapcsolati követelményeinek robusztus hálózati infrastruktúrát tartalmaz. Hálózati kapcsolat a helyszíni között az Azure-ban található erőforrások között lehetőség, és az Azure-ban üzemeltetett erőforrásokhoz, és hogy, és az internetről és az Azure.

Ez a cikk ismerteti az Azure által kínált hálózati biztonság területén opciók egy része. További információ:

* Az Azure-hálózatok
* Hálózati hozzáférés-vezérlés
* Azure Firewall
* Biztonságos távoli hozzáférést és a létesítmények közötti kapcsolat
* Rendelkezésre állás
* Névfeloldás
* Szegélyhálózat (DMZ) hálózati architektúra
* Azure DDoS Protection
* Az Azure bejárati ajtajának
* Traffic Manager
* Figyelés és a fenyegetésészlelés

## <a name="azure-networking"></a>Az Azure-hálózatok

Azure-beli virtuális hálózathoz csatlakoztatni kívánt virtuális gépek van szüksége. Egy virtuális hálózat egy logikai szerkezet, a fizikai az Azure hálózati háló épülnek. Minden virtuális hálózat el különítve a többi virtuális hálózattól. Ezzel biztosíthatja, hogy a központi telepítések a hálózati forgalom nem érhető el más Azure-ügyfeleknek.

További információ:

* [A Virtual network áttekintése](../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>Hálózati hozzáférés-vezérlés

Hálózati hozzáférés-vezérlés a törvény korlátozására és az egyes eszközök és a egy virtuális hálózat alhálózatainak kapcsolat. A hálózati hozzáférés-vezérlés célja, hogy korlátozza a hozzáférést a virtuális gépek és szolgáltatások jóváhagyott felhasználók és eszközök számára. Hozzáférés-vezérlés döntéseket hozhat, engedélyezik vagy megtagadják a kapcsolatokat, és a virtuális gép vagy szolgáltatás alapulnak.

Az Azure számos különböző típusú hálózati hozzáférés-vezérlés, mint például támogatja:

* Hálózati réteg szabályozása
* Irányíthatja a vezérlő és a kényszerített bújtatás
* Virtuális hálózati biztonsági berendezéseket

### <a name="network-layer-control"></a>Hálózati réteg szabályozása

Minden biztonságos telepítéséhez van szükség egy adott mérték hálózati hozzáférés-vezérlést. A hálózati hozzáférés-vezérlés célja, hogy korlátozni a virtuális gép kommunikációt a szükséges rendszereken. Más kommunikációs kísérlet le vannak tiltva.

> [!NOTE]
> A Storage-tűzfalak terjed ki a [az Azure storage biztonsági áttekintése](security-storage-overview.md) cikk

#### <a name="network-security-rules-nsgs"></a>Hálózati biztonsági szabályok (NSG-k)

Ha alapszintű hálózati szintű hozzáférés-vezérlés (IP-cím és a TCP vagy UDP protokoll alapján), a hálózati biztonsági csoportok (NSG-k) is használhatja. Az NSG-t egy alapszintű, az állapotalapú, a tűzfal csomagot, és lehetővé teszi a hozzáférés alapján szabályozhatja a [5 rekordos](https://www.techopedia.com/definition/28190/5-tuple). Az NSG-k felügyelet egyszerűsítését és konfigurációs hibák esélyének csökkentése funkciókat tartalmazza:

* **Kibővített biztonsági szabályok** NSG-szabály definíciójának egyszerűbb és hozhat létre komplex szabályokat, ahelyett, hogy az azonos eredmények elérése érdekében több egyszerű szabály létrehozásához.
* **Szolgáltatáscímkéket** Microsoft jönnek létre egy csoportot az IP-címek képviselő címkék. Ezek frissítési dinamikusan IP-címtartományokat, amelyek megfelelnek a belefoglalási a címke meghatározó feltételeket tartalmazza. Például ha azt szeretné, hozzon létre egy szabályt, amely az összes Azure storage, a keleti régióban érvényes használható Storage.EastUS
* **Az alkalmazásbiztonsági csoportok** erőforrások üzembe helyezése az alkalmazáscsoport és az erőforrásokhoz való hozzáférésének ezen alkalmazás ezeket a csoportokat használó szabályok létrehozásával. Például ha az "Webservers" csoporthoz telepített webservers létrehozhat egy szabályt, amely lehetővé teszi a 443-as forgalom az internetről az összes rendszeren a "Webservers" alkalmazáscsoporthoz egy NSG vonatkozik.

Az NSG-k application layer ellenőrző nem ad meg, vagy a hitelesített hozzáférés-vezérlést.

További információ:

* [Hálózati biztonsági csoportok](../virtual-network/security-overview.md)

#### <a name="asc-just-in-time-vm-access"></a>Az ASC csak a time VM access

[Az Azure security center](../security-center/security-center-intro.md) kezelheti az NSG-ket a virtuális gépek és a virtuális Géphez való hozzáférés zárolása amíg egy felhasználó a megfelelő szerepköralapú hozzáférés-vezérléssel [RBAC](../role-based-access-control/overview.md) engedélyek hozzáférést kér. Amikor a felhasználó sikeresen hitelesített ASC teszi az NSG-ket kijelölt portok való hozzáférés engedélyezése a kérdéses módosításait. Ha az idő lejár az NSG-t állítja vissza az előző állapot védett.

További információ:

* [Az Azure Security Center csak az idő Access](../security-center/security-center-just-in-time.md)

#### <a name="service-endpoints"></a>Szolgáltatásvégpontok

A Szolgáltatásvégpontok használatával is szabályozhatja a forgalom a alkalmazni. Támogatott szolgáltatások folytatott kommunikáció egy közvetlen kapcsolaton keresztül korlátozhatja csak a virtuális hálózatok. A megadott Azure-szolgáltatás a virtuális hálózat forgalmát a Microsoft Azure gerinchálózatán marad.  

További információ:

* [Szolgáltatásvégpontok](../virtual-network/virtual-network-service-endpoints-overview.md#securing-azure-services-to-virtual-networks)

### <a name="route-control-and-forced-tunneling"></a>Irányíthatja a vezérlő és a kényszerített bújtatás

Arra, hogy a virtuális hálózatokon útválasztási viselkedés vezérléséhez. Útválasztás helytelenül van konfigurálva, alkalmazások és szolgáltatások a virtuális gépen üzemeltetett előfordulhat, hogy csatlakozni jogosulatlan eszközöket, beleértve a tulajdonában lévő és az esetleges támadók által működtetett rendszerekkel is.

Az Azure-hálózatok támogatja a hálózati forgalmat a virtuális hálózatok az útválasztási viselkedés testre szabhatók. Ez lehetővé teszi, hogy módosítja az alapértelmezett útválasztási Színtáblázat bejegyzései a virtuális hálózaton. Útválasztási viselkedés irányítását segítségével győződjön meg arról, hogy egy adott eszköz vagy eszközcsoport érkező minden be- vagy a virtuális hálózaton keresztül egy adott helyen.

Egy virtuális hálózat biztonsági eszközének Előfordulhat például, a virtuális hálózaton. Győződjön meg arról, hogy az összes forgalmat a virtuális hálózat végighalad a virtuális biztonsági berendezés szeretné. Ezt megteheti konfigurálásával is [felhasználó által megadott útvonalak](../virtual-network/virtual-networks-udr-overview.md) (udr-EK) az Azure-ban.

[Kényszerített bújtatás](https://www.petri.com/azure-forced-tunneling) olyan mechanizmus segítségével győződjön meg arról, hogy a szolgáltatások nem engedélyezett az internethez csatlakozó eszközökről való kapcsolatot kezdeményez. Vegye figyelembe, hogy ez eltér a bejövő kapcsolatok fogadását, és ezután válaszol rájuk. Előtér-webkiszolgálók kell válaszolni a kérelmekre internet gazdagépekről, és így internetes forráskódú forgalom engedélyezve van, a következő webes kiszolgálókhoz bejövő és reagálni a webkiszolgálók engedélyezettek.

Egy kimenő kérelem kezdeményezéséhez előtér-webkiszolgálóra, hogy nem szeretné nem. Ilyen kérelmeket reprezentálhatja biztonsági kockázatot jelent, mivel ezek a kapcsolatok kártevő szoftverek letöltésére is használható. Akkor is, ha szeretné, hogy előtér-kiszolgálók kezdeményezheti a kimenő kérelmeket az interneten, érdemes haladhat végig a helyileg üzemeltetett webes proxykat őket. Ez lehetővé teszi, hogy az URL-címe, szűrés és a naplózási előnyeit.

Ehelyett érdemes használni a kényszerített bújtatás elkerüléséhez. Ha engedélyezi a kényszerített bújtatás, az internet felé irányuló összes kapcsolatot a helyszíni átjárón keresztül kényszerítve. Konfigurálhatja a kényszerített bújtatás udr-EK kihasználásával.

További információ:

* [Mik azok a felhasználó által megadott útvonalak és IP-továbbítás](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Virtuális hálózati biztonsági berendezéseket

Amíg az NSG-k, az udr-EK és a kényszerített bújtatás biztosítanak a hálózat és az átviteli rétegeket, a biztonsági szintet a [OSI-modell](https://en.wikipedia.org/wiki/OSI_model), érdemes lehet magasabb, mint a hálózati szintű biztonság engedélyezéséhez.

Ha például a biztonsági követelmények a következők lehetnek:

* Hitelesítés és engedélyezés az alkalmazáshoz való hozzáférés engedélyezése előtt
* Behatolásérzékelés és a behatolás-válasz
* Alkalmazás-réteg vizsgálata magas szintű protokollok
* URL-CÍMEK szűrése
* Hálózati szintű a víruskereső és kártevőirtó
* Anti-bot védelme
* Alkalmazás-hozzáférés-vezérlés
* További DDoS elleni védelem (fent a DDoS protection az Azure-hálót, saját maga által biztosított)

Következő nagyobb hálózati biztonsági funkciók érhetők el az egy Azure-partneri megoldás használatával. Annak a legújabb Azure-partneri hálózati biztonsági megoldások meglátogatják a [Azure Marketplace-en](https://azure.microsoft.com/marketplace/), és keressen a "security" és "hálózati biztonság."

## <a name="azure-firewall"></a>Azure Firewall

Az Azure Firewall egy felügyelt, felhőalapú hálózatbiztonsági szolgáltatás, amely Azure Virtual Network-erőforrásait védi. Ez egy szolgáltatásként nyújtott teljesen állapotalapú tűzfal, beépített magas rendelkezésre állással és korlátlan felhőalapú skálázhatósággal. Egyes funkciók a következők:

* Magas rendelkezésre állás
* Felhő méretezhetősége
* Alkalmazások teljes tartománynevére vonatkozó szűrési szabályok
* Hálózati forgalomra vonatkozó szűrési szabályok

További információ:

* [Az Azure tűzfal áttekintése](../firewall/overview.md)

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Biztonságos távoli hozzáférést és a létesítmények közötti kapcsolat

A telepítő, konfigurációs és felügyeleti feladatot távolról kell elvégezni az Azure-erőforrások igényeihez. Emellett érdemes telepíteni [hibrid informatikai](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) a megoldásokat, amelyek összetevőit a helyszínen és az Azure nyilvános felhő. Ezek a forgatókönyvek biztonságos távoli hozzáférést igényelnek.

Az Azure-hálózatok a következő biztonságos távoli hozzáférést forgatókönyveket támogatja:

* Egyéni munkaállomások csatlakoztatása egy virtuális hálózathoz
* A helyszíni hálózat csatlakoztatása egy virtuális hálózat VPN-nel
* A helyszíni hálózat csatlakoztatása egy virtuális hálózatot és a egy dedikált WAN-kapcsolaton
* Egymáshoz virtuális hálózatok összekapcsolása

### <a name="connect-individual-workstations-to-a-virtual-network"></a>Egyéni munkaállomások csatlakoztatása egy virtuális hálózathoz

Előfordulhat, hogy szeretné engedélyezni az egyéni fejlesztők számára és a műveleti személyzet kezelheti a virtuális gépek és szolgáltatások az Azure-ban. Például tegyük fel, hozzá kell férnie a virtuális hálózat egy virtuális gépet. De a biztonsági házirend nem engedélyezi az egyes virtuális gépekhez RDP vagy SSH távoli hozzáférést. Ebben az esetben használhatja egy [pont – hely VPN](../vpn-gateway/point-to-site-about.md) kapcsolat.

A pont – hely VPN-kapcsolat lehetővé teszi egy védett és biztonságos kapcsolatot a felhasználó és a virtuális hálózat között. A VPN-kapcsolat létrejött, amikor a felhasználó is RDP vagy SSH a VPN-kapcsolaton keresztül, a virtuális hálózaton lévő bármelyik virtuális gépet. (A parancs feltételezi, hogy a felhasználói hitelesítést, és engedélyezett.) Pont – hely VPN-JE támogatja:

* Secure Socket Tunneling Protocol (SSTP), egy egyéni SSL-alapú VPN-protokollt. Az SSL VPN-megoldás behatolásának tűzfalak, mivel a legtöbb tűzfal nyitva TCP 443-as portot, amely az SSL. Az SSTP csak a Windows-eszközökön támogatott. Az Azure Windows SSTP (Windows 7 és újabb) rendelkező összes verziója támogatja.

* IKEv2 VPN, egy szabványalapú IPsec VPN-megoldás. Az IKEv2 VPN segítségével Macről is lehetségessé válik a csatlakozás (OSX 10.11-es vagy újabb verziók használata esetén).

* [OpenVPN](https://azure.microsoft.com/updates/openvpn-support-for-azure-vpn-gateways/)

További információ:

* [PowerShell-lel virtuális hálózathoz pont – hely kapcsolat konfigurálása](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-vpn"></a>A helyszíni hálózat csatlakoztatása egy virtuális hálózat VPN-nel

Érdemes lehet, hogy a teljes vállalati hálózaton, vagy egyes részeiből, csatlakoztatása virtuális hálózathoz. Ez a hibrid informatikai gyakori forgatókönyvek, ahol szervezetek [kiterjesztése a helyszíni adatközpontját az Azure-bA](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). Sok esetben szervezetek üzemeltethet az Azure és helyszíni részek szolgáltatás részei. Például előfordulhat, hogy ehhez a egy megoldás magában foglalja a előtér-webkiszolgálók, az Azure-ban, és a háttér-helyszíni adatbázisok. Ilyen típusú "" kapcsolatokat is győződjön meg arról, felügyeleti található Azure-erőforrások biztonságának, és felhasználási helyzetek kiterjesztése az Active Directory-tartományvezérlőket az Azure-bA.

Egyik módszer elvégzéséhez ezt, hogy használjon egy [site-to-site VPN](https://www.techopedia.com/definition/30747/site-to-site-vpn). A site-to-site VPN és a egy pont – hely VPN közötti különbség, hogy az utóbbi kapcsolódik egy adott eszköz a virtuális hálózathoz. Site-to-site VPN (például a helyszíni hálózat) teljes hálózathoz csatlakozik egy virtuális hálózatot. Virtuális hálózat és egy helyek közötti VPN-eket a rendkívül biztonságos IPsec-alagút módú VPN protokollt használja.

További információ:

* [Resource Manager virtuális hálózat létrehozása helyek közötti VPN-kapcsolattal az Azure portal használatával](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Információk a VPN Gateway-ről](../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-dedicated-wan-link"></a>A helyszíni hálózat csatlakoztatása egy virtuális hálózatot és a egy dedikált WAN-kapcsolaton

Pont – hely és helyek közötti VPN-kapcsolatok létesítmények közötti kapcsolatok engedélyezéséhez érvényesek. Azonban egyes szervezetek vegye figyelembe, hogy a következő hátrányokkal:

* VPN-kapcsolatok az interneten keresztül helyezze át az adatokat. Ez elérhetővé teszi ezeket a kapcsolatokat, az érintett adatok áthelyezése egy nyilvános hálózaton keresztül a potenciális biztonsági problémákat. Ezenkívül megbízhatóságot és rendelkezésre állást, a hálózati kapcsolatok használata esetén nem garantálható.
* Virtuális hálózatok VPN-kapcsolatok nem feltétlenül a sávszélesség az egyes alkalmazások és céljából, amint azok körülbelül 200 Mbps sebességnél, max.

A szervezetek, általában szükség van a legmagasabb szintű biztonság és elérhetőség a létesítmények közötti kapcsolatok dedikált WAN-kapcsolatok használatával távoli helyekhez való csatlakozáshoz. Az Azure lehetővé teszi egy dedikált WAN-kapcsolaton, amellyel a helyszíni hálózat csatlakoztatása egy virtuális hálózatot használja. Az Azure ExpressRoute Express route közvetlen, és az Express route globális jelenléttel engedélyezéséhez.

További információ:

* [Az ExpressRoute technikai áttekintése](../expressroute/expressroute-introduction.md)
* [ExpressRoute direct](../expressroute/expressroute-erdirect-about.md)
* [Express route globális elérhetőséggel](..//expressroute/expressroute-global-reach.md)

### <a name="connect-virtual-networks-to-each-other"></a>Egymáshoz virtuális hálózatok összekapcsolása

A központi telepítések számos virtuális hálózatot használandó lehetőség. Vannak, előfordulhat, hogy miért ez különböző okok miatt. Érdemes lehet leegyszerűsítheti a felügyeletet, vagy érdemes nagyobb biztonság. A különböző virtuális hálózatokon lévő erőforrások üzembe motiváció, függetlenül is azt szeretné, minden való csatlakozáshoz egymással a hálózaton lévő erőforrások.

Az egyik lehetőség van a szolgáltatásokhoz való csatlakozáshoz szolgáltatások egy másik virtuális hálózattal, "hurkolás vissza" egy virtuális hálózaton az interneten keresztül. A kapcsolat elindítja egy virtuális hálózaton, az interneten keresztül halad, és ezután visszatér a cél virtuális hálózat. Ez a beállítás a biztonsági problémák rejlő minden olyan internetes kommunikációs kapcsolat tesz elérhetővé.

Jobb megoldás lehet, amely összeköti a két virtuális hálózat között helyek közötti VPN létrehozása. Ezt a módszert használja, azonos [IPSec-alagút módú](https://technet.microsoft.com/library/cc786385.aspx) a létesítmények közötti site-to-site VPN-kapcsolat a fent említett-protokoll.

Ez a megközelítés előnye, hogy a VPN-kapcsolat létrejött-e az Azure hálózati háló, ne csatlakozzanak az interneten keresztül. Ez, egy további biztonsági réteget nyújt, helyek közötti VPN-eket az interneten keresztül csatlakozó képest.

További információ:

* [A virtuális hálózatok közötti kapcsolat konfigurálása Azure Resource Manager és a PowerShell használatával](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Egy másik módja a virtuális hálózatok [virtuális hálózatok közötti társviszony](../virtual-network/virtual-network-peering-overview.md). Ez a funkció lehetővé teszi két Azure-hálózatok csatlakoztatása, így közötti kommunikáció lehetőségét a Microsoft gerincinfrastruktúráján keresztül történik, anélkül, hogy soha ne legyenek az interneten keresztül. Virtuális hálózatok közötti társviszony vagy is lehet kapcsolódni az azonos régión belüli két virtuális hálózat két virtuális hálózat Azure-régiók között. Az NSG-k különböző alhálózatokhoz vagy rendszerek közötti kapcsolat korlátozására használható.

## <a name="availability"></a>Rendelkezésre állás

Rendelkezésre állás bármilyen biztonsági program egyik fő összetevője. Ha a felhasználók és rendszerek nem sikerül elérnie mit eléréséhez szükséges a hálózaton keresztül, a szolgáltatás lehessen venni sérült a biztonsága. Azure hálózatkezelési technológiákat, amelyek támogatják az alábbi magas rendelkezésre állású mechanizmusok rendelkezik:

* HTTP-alapú terheléselosztási
* Hálózati szintű terheléselosztás
* Terhelés globális kiegyenlítéséhez

Load balancing szolgáltatás egyenlő arányban terjeszteni a kapcsolatokat, többek között a több eszközre tervezett mechanizmus. A terheléselosztás céljai a következők:

* Elérhetőség növeléséhez. Egyenleg kapcsolatok betölteni a több eszközre, amikor egy vagy több olyan eszközt is elérhetetlenné válik a szolgáltatás veszélyeztetése nélkül. A fennmaradó online eszközökön futó szolgáltatások továbbra is a szolgáltatás a tartalmat szolgálnak ki.
* Teljesítmény növelése érdekében. Egyenleg kapcsolatok betölteni a több eszközre, amikor egy adott eszköz nincs minden feldolgozás kezelésére. Ehelyett a feldolgozás és a memória a növekvő igények szerint, de a tartalom van elosztva több eszközön.

### <a name="http-based-load-balancing"></a>HTTP-alapú terheléselosztási

Szervezetek számára, amelyek gyakran webes szolgáltatások futtatásához rendelkeznie egy HTTP-alapú terheléselosztó tartománynévcímkéje webes szolgáltatások igényel. Ez segít a megfelelő szintű teljesítményt és magas rendelkezésre állás biztosítása érdekében. A hagyományos, hálózati terheléselosztók hálózati és az átviteli réteg protokollok támaszkodnak. HTTP-alapú terheléselosztók, másrészről, döntéseket hozhat a HTTP-protokoll jellemzői alapján.

Az Azure Application Gateway HTTP-alapú terheléselosztást biztosít a webes szolgáltatásokhoz. Az Application Gateway támogatja:

* Cookie-alapú munkamenet-affinitás. Ez a funkció gondoskodik arról, hogy a terheléselosztó mögött a kiszolgálóhoz létesített kapcsolatok változatlan marad, az ügyfél és kiszolgáló közötti marad. Ez biztosítja, hogy a tranzakció stabilitását.
* SSL-alapú kiszervezés. Amikor egy ügyfél kapcsolódik a terheléselosztóval való, hogy munkamenet a HTTPS (SSL) protokoll használatával titkosítja. Azonban annak érdekében, hogy a teljesítmény növelése érdekében használhatja (titkosítatlanul) HTTP protokollal csatlakozni a terheléselosztó és a terheléselosztó mögött a webkiszolgáló között. Ezt nevezik "SSL-alapú kiszervezést,", mert a webkiszolgálók, a terheléselosztó mögött a processzor terhelését a titkosítással kapcsolatos nem működik. A webkiszolgálók is ezért szolgáltatáskérések időnél gyorsabban van szüksége.
* URL-alapú tartalom-útválasztás. Ez a funkció megfontoltabb döntéseket hozhat, ha a továbbítási kapcsolatok a célként megadott URL-cím alapján, hogy a terheléselosztó lehetővé teszi. Ez a megoldást kínál, amellyel betölteni a IP-címeken alapuló terheléselosztás döntésekhez, mint sokkal nagyobb rugalmasságot nyújt.

További információ:

* [Application Gateway áttekintése](../application-gateway/application-gateway-introduction.md)

### <a name="network-level-load-balancing"></a>Hálózati szintű terheléselosztás

HTTP-alapú terheléselosztási, szakembereket hálózati szintű terheléselosztás döntéseket IP-címet és portot (TCP vagy UDP) számok alapján.
A hálózati szintű terheléselosztás az Azure-ban az Azure Load Balancer használatával előnyeit is kaphat. Load Balancer néhány főbb jellemzői a következők:

* IP-címet és portot számok alapján hálózati szintű terheléselosztást.
* Bármely Alkalmazásrétegbeli protokoll támogatása.
* Az Azure-beli virtuális gépek elosztja a terhelést, és a felhőszolgáltatások szerepkörpéldányok.
* Internetre irányuló (külső terheléselosztás), mind a kiszolgáló nem internetes is használható (belső terheléselosztás) alkalmazások és a virtuális gépeket.
* Végponti monitorozását, amely azt határozza meg, ha a terheléselosztó mögötti szolgáltatások váltak nem érhető el.

További információ:

* [Internetkapcsolattal rendelkező terheléselosztó több virtuális gép vagy szolgáltatás között](../load-balancer/load-balancer-internet-overview.md)
* [Belső load balancer áttekintése](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>Terhelés globális kiegyenlítéséhez

Egyes szervezetek szeretnék a legmagasabb szintű rendelkezésre állást lehetséges. Egyik módja a cél eléréséhez az alkalmazások üzemeltetését a globálisan elosztott adatközpontokban. Egy alkalmazás a világ számos részén található adatközpontokban üzemeltetett, esetén lehetséges egy teljes geopolitikai régió elérhetetlenné válik, és továbbra is az alkalmazás és futnak.

Ez a terheléselosztó stratégia költségmegtakarítással járhat az teljesítmény előnyeit. A szolgáltatás számára az adatközpontot, amelyben azt az eszközt, hogy a kérés küldője kérelmek irányíthatók.

Az Azure-ban a terhelés globális kiegyenlítéséhez az Azure Traffic Manager előnyeit is kaphat.

További információ:

* [A Traffic Manager ismertetése](../traffic-manager/traffic-manager-overview.md)

## <a name="name-resolution"></a>Névfeloldás

Névfeloldás, akkor egy kritikus fontosságú függvény üzemeltetése az Azure-szolgáltatásokhoz. Biztonsági szempontból a a név feloldása függvény biztonságának sérüléséhez vezethet a támadó helyre a helyekről kérések átirányítása a támadó. Biztonságos névfeloldás az összes üzemeltetett felhőalapú szolgáltatás működik.

Teljesítendő névfeloldás két típusa van:

* Belső névfeloldást. Ez a virtuális hálózatok, a helyszíni hálózat vagy mindkettő szolgáltatások használják. Belső névfeloldást használt nevek nem érhetők el az interneten keresztül. Az optimális biztonság biztosítása érdekében fontos, hogy a belső név feloldása séma nem érhető el a külső felhasználók számára.
* Külső név feloldása. Ezt használja a helyszíni hálózatok és virtuális hálózatok kívüli személyekkel és eszközökkel. Ezek azok a nevek, látható az internethez, és a felhőalapú szolgáltatásokhoz való kapcsolat irányíthatók.

Belső névfeloldást két lehetősége van:

* Virtuális hálózat DNS-kiszolgáló. Amikor létrehoz egy új virtuális hálózatot, egy DNS-kiszolgáló jön létre az Ön számára. A DNS-kiszolgáló fel tudja oldani az adott virtuális hálózaton található gépek nevei. A DNS-kiszolgáló nem konfigurálható, az Azure fabric manager kezeli, és ezért hozzájárulhat a név feloldása megoldás.
* A saját DNS-kiszolgáló használata. Lehetősége van a DNS-kiszolgáló a saját kiválasztása a virtuális hálózaton üzembe. A DNS-kiszolgáló lehet egy Active Directory integrált DNS-kiszolgáló vagy egy dedikált DNS server megoldásának része, amelyet egy Azure-partner, amely az Azure piactérről szerezhető be.

További információ:

* [A Virtual network áttekintése](../virtual-network/virtual-networks-overview.md)
* [Virtuális hálózat által használt DNS-kiszolgálók kezelése](../virtual-network/manage-virtual-network.md#change-dns-servers)

Külső névfeloldás két lehetősége van:

* A saját külső DNS kiszolgáló helyszíni gazdagépekre.
* A saját külső DNS-kiszolgáló üzemeltetése egy szolgáltatónál.

Számos nagyméretű szervezet saját DNS kiszolgálók a helyszíni gazdagépekre. Ehhez a hálózati szakértelemmel és a globális jelenlét ehhez mert.

A legtöbb esetben érdemes futtatni a DNS-névfeloldási szolgáltatást egy szolgáltatónál. Ezek a szolgáltatók rendelkezik a hálózati szakértelemmel és a globális jelenlét a névfeloldási szolgáltatást nagyon magas rendelkezésre állásának biztosításához. Rendelkezésre állási elengedhetetlen a DNS-szolgáltatások esetében, mert ha meghibásodik a névfeloldási szolgáltatást, senki nem tudja elérni az internetkapcsolattal rendelkező szolgáltatások.

Az Azure biztosít egy magas rendelkezésre állású, nagy teljesítményű külső DNS-megoldás az Azure DNS formájában. Ez a külső megoldás a megoldás a világszerte az Azure DNS-infrastruktúra kihasználja. Ez lehetővé teszi, hogy üzemeltessen saját tartományt az Azure-ban, azonos hitelesítő adatokkal, API-kkal, eszközökkel és az egyéb Azure-szolgáltatások számlázási információkkal. Az Azure részeként a platform erős biztonsági ellenőrzéseket is örökli.

További információ:

* [Az Azure DNS áttekintése](../dns/dns-overview.md)
* [Az Azure DNS saját zónáival kapcsolatos](../dns/private-dns-overview.md) konfigurálhatja az Azure-erőforrásokhoz a privát DNS-nevek helyett az automatikusan hozzárendelt nevét, adjon hozzá egy egyéni DNS-megoldás nélkül.

## <a name="perimeter-network-architecture"></a>Szegélyhálózat-alapú hálózati architektúra

Számos nagyméretű szervezet szegmentálása azok hálózatok peremhálózatokon használja, és az internet és szolgáltatásaik között puffer-zóna létrehozása. A hálózati peremhálózati részének számít egy alacsony biztonsági szintű zónában, és nem nagy értékű eszközök kerülnek, a hálózati szegmenst. Általában látni fog egy hálózati adaptert a szegélyhálózat-alapú hálózati szegmensen rendelkező hálózati biztonsági eszközöket. Egy másik hálózati adapter csatlakoztatva van egy hálózathoz, amelyen a virtuális gépek és szolgáltatások, amelyek fogadják az internetről bejövő kapcsolatokat.

Számos különböző módon a szegélyhálózat is tervezhet. A döntést, üzembe helyezéséhez a szegélyhálózaton, majd peremhálózati milyen típusú hálózati használhat, ha úgy dönt, hogy használja, attól függ, hogy a hálózati biztonsági követelmények teljesítéséhez.

További információ:

* [A Microsoft Cloud Services és a hálózati biztonság](../best-practices-network-security.md)

## <a name="azure-ddos-protection"></a>Azure DDoS Protection

Az elosztott szolgáltatásmegtagadásos (DDoS-) támadások az egyik legnagyobb rendelkezésreállási és biztonsági kockázatot jelentik az olyan felhasználók számára, akik alkalmazásaikat a felhőbe helyezik át. A DDoS-támadások próbál lefoglalhat egy alkalmazás-erőforrások, így az alkalmazás nem érhető el a jogosult felhasználók számára. A DDoS-támadások bármilyen, az interneten keresztül nyilvánosan elérhető végpontot megcélozhatnak.
A Microsoft biztosítja a DDoS protection néven **alapszintű** az Azure Platform részeként. Ez díjmentesen érhető el, és mindig tartalmazza a figyelési és valós idejű csökkenti a közös hálózati szintű támadásoktól. Mellett a védelmet, a DDoS protection részét képező **alapszintű** engedélyezheti a **Standard** lehetőséget. A DDoS Protection Standard funkciók:

* **A platform natív integráció:** Natív módon integrálva az Azure-bA. Az Azure Portalon keresztül konfigurációit is beleértve. A DDoS Protection Standard tisztában van azzal erőforrásait és erőforrás-konfigurációhoz.
* **Kulcsrakész védelem:** Egyszerűsített konfiguráció azonnal, amint a DDoS Protection Standard engedélyezett védelmet biztosít egy virtuális hálózaton lévő összes erőforrást. Nincs beavatkozás vagy a felhasználó megadása nem kötelező. A DDoS Protection Standard azonnal és automatikusan csökkenti a támadási után a rendszer azt észlelte.
* **Mindig forgalomfigyelést:** Az alkalmazás forgalmi minták figyelhetők 24 óránként, naponta, hetente, 7 nap DDoS-támadások kijelzőjét keres. Kockázatcsökkentési alkalmazásvédelmi szabályzatok túllépése esetén történik.
* **Kockázatcsökkentési jelentések támadási** támadási kockázatcsökkentési jelentések összesített hálózati forgalmi adatai segítségével az erőforrások irányuló támadások részletes információkat tartalmaznak.
* **Kockázatcsökkentési Flow naplók támadási** támadási kockázatcsökkentési Flow naplók lehetővé teszi, hogy tekintse át az eldobott forgalomhoz továbbított forgalom és a más támadási adatainak közel valós időben aktív DDoS-támadás során.
* **Adaptív hangolás:** Intelligens adatforgalom-profilkészítés képes megtanulni az alkalmazás forgalmának idővel, és kiválasztja, és frissíti a profilt, amely a szolgáltatás a legmegfelelőbb. A profil módosítja, a forgalom változik az idő múlásával. Réteg 3-7. rétegbeli védelem: Teljes verem DDoS elleni védelem, biztosít a webalkalmazás-tűzfal együtt használva.
* **Széles körű kockázatcsökkentési beosztás:** Több mint 60 különböző támadási típusok enyhíthető, globális kapacitással, a legnagyobb ismert DDoS-támadásokkal szembeni védelem érdekében.
* **A támadás metrikák:** Minden támadástól összesített mérőszámok Azure monitoron keresztül érhetők el.
* **A támadás riasztás:** Riasztások konfigurálhatók a kezdő és a egy támadás leállítása és a támadás időtartama alatt a beépített támadási mérőszámok segítségével. Riasztások integrálható a működési szoftver a Microsoft Azure Monitor naplók, Splunk, Azure Storage, e-mailek és az Azure Portalon.
* **A Cost garancia:**  Adatátvitel – és az alkalmazás horizontális felskálázás szolgáltatásokhoz biztosított kreditek a dokumentált DDoS-támadások.
* **DDoS gyors, rugalmas** DDoS Protection Standard használó ügyfelek mostantól hozzáférhet a Rapid Response team aktív támadás során. A támadás vizsgálat során egy támadás és a támadás utáni elemzés egyéni megoldások DRR segíthet.


További információ:

* [A DDOS protection áttekintése](../virtual-network/ddos-protection-overview.md)

## <a name="azure-front-door"></a>Az Azure bejárati ajtajának

Az Azure bejárati ajtajának szolgáltatás lehetővé teszi definiálása, kezelhet és monitorozhat globális továbbítani tudja a webes forgalom. Optimalizálja a forgalom-útválasztást a legjobb teljesítmény és a magas rendelkezésre állás. Az Azure Front Doorral egyéni webalkalmazási tűzfalszabályok (WAF-szabályok) készítésével hozzáférés-vezérlést valósíthat meg, amellyel megvédheti a HTTP/HTTPS-számítási feladatait attól, hogy feltörjék őket az ügyféloldali IP-cím, országkód és HTTP-paraméterek alapján. Ezenkívül bejárati ajtajának lehetővé teszi, hogy a sebesség korlátozása élesben kártékony forgalmával szabályokat létrehozni, az SSL-kiürítés, és a egy – HTTP/HTTPS-kérést, olyan alkalmazásréteg feldolgozás.

Bejárati ajtajának platform magát az Azure DDoS Protection alapszintű védi. További védelemként engedélyezheti a virtuális hálózatain a Standard szintű Azure DDoS Protectiont, így automatikus finomhangolással és kárenyhítéssel megvédheti erőforrásait a hálózati rétegből érkező (TCP/UDP) támadásokkal szemben. Bejárati ajtajának egy 7. rétegbeli fordított proxy, csak lehetővé teszi webes forgalom áthaladását biztonsági vége a kiszolgálók és más típusú forgalom tiltása az alapértelmezés szerint.

További információ:

* További információ az Azure az előtérbeli teljes készletét a nyomtató ajtaja képességek áttekintheti a [Azure bejárati ajtajának áttekintése](../frontdoor/front-door-overview.md)

## <a name="azure-traffic-manager"></a>Az Azure Traffic manager

Az Azure Traffic Manager egy DNS-alapú forgalom-terheléselosztó, amely lehetővé teszi a szolgáltatásokhoz érkező forgalom optimális elosztását a globális Azure-régiókban, miközben magas rendelkezésre állást és válaszkészséget biztosít. A Traffic Manager DNS használatával a leginkább megfelelő szolgáltatási végpontra irányítja az ügyfélkéréseket a forgalom-útválasztási módszer és a végpont állapota alapján. A végpont egy, az Azure-on kívül vagy belül üzemeltetett, internetkapcsolattal rendelkező szolgáltatás. A TRAFFIC manager figyeli a végpontok, és nem irányítja a forgalmat olyan végpontok, amelyek nem érhetők el.

További információ:

* [Az Azure Traffic manager áttekintése](../traffic-manager/traffic-manager-overview.md)

## <a name="monitoring-and-threat-detection"></a>Figyelés és a fenyegetésészlelés

Azure segíti a korai észlelését, figyelés, a kulcs területen képességeket biztosít, és a hálózati forgalom gyűjtése és ellenőrzése.

### <a name="azure-network-watcher"></a>Azure Network Watcher

Az Azure Network Watcher segíthet a hiba elhárításához és a egy teljesen új eszközök, amelyek segítik a biztonsági problémák azonosítása biztosít.

[Biztonsági csoport nézet](../network-watcher/network-watcher-security-group-view-overview.md) segít a naplózási és biztonsági megfelelőségi a virtuális gépek. Ez a funkció segítségével végezze el a programozott eseményeket, a baseline szabályzatokat az egyes virtuális gépek hatékony szabályokat a szervezet által meghatározott összehasonlítása. Ez segíthet azonosítani a konfigurációs eltéréseket.

[A csomagrögzítés](../network-watcher/network-watcher-packet-capture-overview.md) lehetővé teszi, hogy a hálózati forgalmat a virtuális gép rögzítése. Hálózati statisztikákat gyűjthet, és hibaelhárítás alkalmazás, amely lehet a hálózati behatolásokat vizsgálata során hasznos információt. Ez a funkció együtt az Azure Functions használatával hálózati adott Azure-riasztásokra adott válaszként indítandók.

További információ a Network Watcher és tesztelése a funkciók némelyike a gyakorlatot elindítása: [Figyelés áttekintése az Azure network watcher](../network-watcher/network-watcher-monitoring-overview.md).

> [!NOTE]
> A legfrissebb értesítésekért rendelkezésre állás és a szolgáltatás állapotát, ellenőrizze a [Azure frissítéseit tartalmazó lapon](https://azure.microsoft.com/updates/?product=network-watcher).

### <a name="azure-security-center"></a>Azure Security Center

Az Azure Security Center segít megelőzését, észlelését és háríthatja el a fenyegetéseket, és biztosít, nagyobb betekintést, és szabályozhatja, az Azure-erőforrások biztonságát. Az Azure-előfizetések integrált biztonsági monitorozást és felügyeletet biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását szabályzatkezelést, és együttműködik a biztonsági megoldások nagy készletét.

A Security Center segítségével optimalizálásához, figyeléséhez, a hálózati biztonság:

* Hálózati biztonsági javaslatok biztosítása.
* A hálózati biztonsági konfiguráció állapotát figyeli.
* Értesíti Önt hálózati alapú fenyegetések, mind a végpont és a hálózati szinten.

További információ:

* [Az Azure Security Center bemutatása](../security-center/security-center-intro.md)

### <a name="virtual-network-tap"></a>Virtual Network TAP

Az Azure virtuális hálózati TAP (Terminálszolgáltatások hozzáférési pont) lehetővé teszi folyamatosan stream a virtuális gép hálózati forgalmat egy hálózati csomag adatgyűjtőnek vagy az analytics eszközt. A gyűjtő vagy elemzőeszköz egy hálózati virtuális berendezés partnerek biztosítják. Az azonos virtuális hálózatban a forgalom összesítéséhez erőforrás KOPPINTSON az azonos vagy eltérő előfizetésekben lévő több hálózati adapterrel is használhatja.

További információ:

* [Virtuális hálózati TAP](../virtual-network/virtual-network-tap-overview.md)

### <a name="logging"></a>Naplózás

Naplózási hálózati szintjén minden olyan hálózati biztonsági forgatókönyv a fő függvény. Az Azure-ban bejelentkezhet az NSG-ket hálózati szintű naplózási adatok beszerzése kapott adatok alapján. NSG-naplózást, információkat a következőket kínálja:

* [A Tevékenységnaplók](../azure-monitor/platform/activity-logs-overview.md). Ezek a naplók segítségével megtekintheti az Azure-előfizetést, elküldött összes műveletet. Ezek a naplók alapértelmezés szerint engedélyezve vannak, és az Azure Portalon is használható. Akkor is korábbi nevén naplózási vagy a műveleti naplókban.
* Az eseménynaplókat. Ezek a naplók milyen NSG-szabályok alkalmazott adatainak megadása.
* A számláló naplókat. Ezek a naplók lehetővé teszik, hogy az egyes NSG-szabály lett alkalmazva megtagadhatja vagy engedélyezheti a forgalom hány alkalommal.

Is [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), egy hatékony adatvizualizációs eszköz, megtekintése és a naplók elemzéséhez.
További információ:

* [Hálózati biztonsági csoportok (NSG-k) az Azure Monitor naplóira](../virtual-network/virtual-network-nsg-manage-log.md)
