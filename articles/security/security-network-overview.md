---
title: Hálózati biztonsági fogalmak és a követelmények az Azure-ban |} Microsoft Docs
description: Ez a cikk alapfogalmak hálózati biztonsági követelmények és információk kapcsolatos alapvető magyarázatokat biztosít Mi az Azure minden ezeknek a területeknek kínál.
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: fbd589aedb955ee4bd61dc0ec754d8713a98179a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365627"
---
# <a name="azure-network-security-overview"></a>Az Azure biztonsági áttekintése
Azure tartalmaz egy robusztus hálózati infrastruktúra, az alkalmazás és szolgáltatás hálózati kapcsolati követelményeinek támogatására. Helyszíni között, az Azure-ban lévő erőforrások közötti hálózati kapcsolatot, és Azure üzemeltetett erőforrásokhoz, és hogy, és az internetről és az Azure.

Ez a cikk célja annak magyarázata, hogy mi az Azure hálózati biztonság területén kínál. Hálózati biztonsági Alapfogalmak és követelményeiről egyszerű magyarázata mellett olvashat:

* Az Azure hálózatkezelés
* Hálózati hozzáférés-vezérlés
* Biztonságos távoli hozzáférés és a létesítmények közötti kapcsolat
* Rendelkezésre állás
* Névfeloldás
* Szegélyhálózat (DMZ) hálózati architektúra
* Figyelés és a fenyegetések észlelése

## <a name="azure-networking"></a>Az Azure hálózatkezelés
Virtuális gépek hálózati kapcsolattal kell. Ez a követelmény kielégítése érdekében az Azure-nak virtuális gépek Azure-beli virtuális hálózathoz kell csatlakoztatni. A virtuális hálózat egy logikai szerkezet, a fizikai Azure hálózati háló platformra épül. Minden logikai virtuális hálózati el különítve a többi virtuális hálózatok. Ezzel biztosíthatja, hogy a hálózati forgalmat a központi telepítés nincs más Azure-ügyfél számára elérhető.

További információ:

* [Virtual network áttekintése](../virtual-network/virtual-networks-overview.md)


## <a name="network-access-control"></a>Hálózati hozzáférés-vezérlés
Hálózati hozzáférés-vezérlés a folyamat, korlátozza a kapcsolat és az egyes eszközök és alhálózatokat a virtuális hálózaton belül. A hálózati hozzáférés-vezérlés célja korlátozza a hozzáférést a virtuális gépek és szolgáltatások jóváhagyott felhasználók és eszközök számára. Hozzáférés-szabályozási döntések engedélyezéséhez vagy letiltásához a virtuális gépet vagy szolgáltatást érkező vagy oda irányuló kapcsolatok alapulnak.

Azure számos különböző típusú hálózati hozzáférés-vezérlést, például a támogatja:

* Hálózati réteg vezérlő
* Útvonal-vezérlés és a kényszerített bújtatás
* Virtuális hálózati biztonsági készülékek

### <a name="network-layer-control"></a>Hálózati réteg vezérlő
Minden biztonságos telepítéséhez szükséges hálózati hozzáférés-vezérlés bizonyos mértékét. Hálózati hozzáférés-vezérlés célja a szükséges rendszereken virtuálisgép-kommunikációt. Más kommunikációs kísérlet le vannak tiltva.

Ha módosítania kell az alapvető hálózati szintű hozzáférés-vezérlés (az IP-cím és a TCP vagy UDP protokoll alapján), a hálózati biztonsági csoportokkal (NSG-k) is használhatja. Az NSG-t a basic, az állapotalapú alkalmazások és szolgáltatások, a tűzfal csomagot, és lehetővé teszi a alapján egy [5 rekordos](https://www.techopedia.com/definition/28190/5-tuple). Az NSG-k alkalmazás réteg ellenőrző nem ad meg, vagy hitelesített hozzáférés-vezérlést.

További információ:

* [Hálózati biztonsági csoportok](../virtual-network/security-overview.md)

### <a name="route-control-and-forced-tunneling"></a>Útvonal-vezérlés és a kényszerített bújtatás
A virtuális hálózatokon lévő útválasztási viselkedés képes fontos. Ha útválasztási helytelenül van konfigurálva, alkalmazások és szolgáltatások a virtuális gépen futó kapcsolódását nem hitelesített eszközöket, beleértve a birtokolt és a potenciális támadók által működtetett rendszereket.

Az Azure hálózatkezelés támogatja a hálózati forgalmat a virtuális hálózatokat az útválasztási viselkedés testreszabása. Ez lehetővé teszi, hogy módosítsa az alapértelmezett útválasztási táblabejegyzéseket a virtuális hálózat. Útválasztási viselkedés irányítását segítségével győződjön meg arról, hogy egy adott eszköz vagy eszközcsoportra származó összes forgalmat be- vagy a virtuális hálózaton keresztül egy konkrét helyre.

Például lehetséges, hogy a virtuális hálózati biztonsági készülékek a virtuális hálózaton. Győződjön meg arról, hogy minden forgalmat a virtuális hálózat végighalad az adott virtuális biztonsági készülék szeretné. Ehhez konfigurálásával [felhasználó által megadott útvonalak](../virtual-network/virtual-networks-udr-overview.md) (udr-EK) az Azure-ban.

[A kényszerített bújtatás](https://www.petri.com/azure-forced-tunneling) egy mechanizmus segítségével győződjön meg arról, hogy a szolgáltatások használata nem engedélyezett a kezdeményezzen kapcsolatot az internethez csatlakozó eszközökről. Vegye figyelembe, hogy ez eltér a bejövő kapcsolatok fogadását, és ezután válaszol rájuk. Az internetes gazdagépek a kérelmek megválaszolásához szükséges előtér-webkiszolgálók, és így internet-forrása forgalom engedélyezve van, a webes kiszolgálókhoz történő bejövő és a webkiszolgálók el válaszol.

Hogy nem szeretné az előtér-webkiszolgáló kezdeményezéséhez egy kimenő kérelem. Az ilyen kérelmeket biztonsági kockázatot jelenthet, mert ezek a kapcsolatok is kártevőket tölthetnek le. Akkor is, ha szeretné, hogy ezek a kimenő kérelmek az internethez kezdeményezéséhez előtér-kiszolgálók, előfordulhat, hogy kényszeríteni kívánja őket a helyszíni webes proxykat végrehajtania. Ez lehetővé teszi, hogy előnyeit szűrés és naplózás URL-CÍMÉT.

Ehelyett volna használni kívánt kényszerített bújtatás Ennek megakadályozására. Ha engedélyezi a kényszerített bújtatást, az internetre létesített összes kapcsolat kényszerítve vannak-e a helyszíni átjárón keresztül. Beállíthatja, hogy kényszerített bújtatást udr-EK kihasználásával.

További információ:

* [Mik azok a felhasználó által megadott útvonalak és IP-továbbítás](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Virtuális hálózati biztonsági készülékek
Amíg az NSG-k, a udr-EK és a kényszerített bújtatás biztosít, akkor a szintű biztonságot, a hálózati és a szállítási réteg a [OSI-modell](https://en.wikipedia.org/wiki/OSI_model), érdemes lehet engedélyezni a magasabb, mint a hálózati szintű biztonságot.

Például a biztonsági követelmények állhatnak:

* Hitelesítési és engedélyezési az alkalmazásokhoz való hozzáférés engedélyezése előtt
* -Behatolásérzékelési és a behatolás-válasz
* Alkalmazás réteg vizsgálatát a magas szintű protokollok
* URL-CÍMEK szűrése
* Hálózati szintű víruskereső és kártevőirtó
* Botot elleni védelem
* Alkalmazás-hozzáférés-vezérlés
* További DDoS-védelem (fent nyújtja az Azure-hálót, maga DDoS-védelem)

Továbbfejlesztett hálózati biztonsági funkció az Azure partneri megoldás használatával végezheti el. Megtalálhatja a legfrissebb Azure partner hálózati biztonsági megoldások látogasson el a [Azure piactér](https://azure.microsoft.com/marketplace/), és a keresést a "biztonság" és "hálózati biztonság."

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Biztonságos távoli hozzáférés és a létesítmények közötti kapcsolat
A telepítő, konfigurálási és kezelése az Azure-erőforrások igényeinek távolról kell elvégezni. Ezenkívül előfordulhat, hogy számára telepíteni kívánja [hibrid informatikai](http://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) összetevők a helyszíni megoldások és az Azure nyilvános felhőjében található. Ezek a forgatókönyvek biztonságos távoli hozzáférést igényelnek.

Az Azure hálózatkezelés a következő biztonságos távoli hozzáférés-forgatókönyveket teszi lehetővé:

* Egyéni munkaállomások csatlakozni a virtuális hálózat
* A helyszíni hálózat kapcsolódjon VPN-nel rendelkező virtuális hálózathoz
* A helyszíni hálózat csatlakozni egy dedikált WAN-kapcsolaton egy virtuális hálózat
* Virtuális hálózatok csatlakozni egymáshoz

### <a name="connect-individual-workstations-to-a-virtual-network"></a>Egyéni munkaállomások csatlakozni a virtuális hálózat
Előfordulhat, hogy engedélyezni szeretné az egyes fejlesztők és a műveleti személyzet kezelheti a virtuális gépek és szolgáltatások az Azure-ban. Például tegyük fel, hozzá kell férnie egy virtuális gép olyan virtuális hálózaton. De a biztonsági házirend nem engedélyezi az egyes virtuális gépekhez való RDP és az SSH távelérési. Ebben az esetben egy pont – hely VPN-kapcsolatot is használhatja.

A pont-pont VPN-kapcsolat használ a [az SSTP VPN](https://technet.microsoft.com/library/cc731352.aspx) protokoll ahhoz, hogy állítsa be a magán- és biztonságos kapcsolatot a felhasználó és a virtuális hálózat között. Ha a VPN-kapcsolat létrejött, a felhasználó is RDP és az SSH a VPN-kapcsolaton keresztül a virtuális gépek a virtuális hálózaton történő. (A parancs feltételezi, hogy a felhasználó képes hitelesíteni, és engedélyezve van.)

További információ:

* [PowerShell virtuális hálózat egy pont – hely kapcsolat beállítása](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-vpn"></a>A helyszíni hálózat kapcsolódjon VPN-nel rendelkező virtuális hálózathoz
Érdemes a teljes vállalati hálózaton, vagy részeit, egy virtuális hálózathoz való kapcsolódáshoz. Ez az a hibrid informatikai gyakori forgatókönyvek, ahol szervezetek [kiterjesztése a helyszíni adatközpontját az Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). Sok esetben a szervezetek gazdagép Azure-ban, és részei a helyi szolgáltatás részei. Például előfordulhat, hogy ehhez a megoldás előtér-webkiszolgálók tartalmazza az Azure-ban és a háttér-adatbázisok a helyszínen. Az ilyen típusú "létesítmények közötti" azt is ellenőrizze található Azure felügyeleti erőforrások több biztonságos, és engedélyezze a forgatókönyvek az Azure Active Directory-tartományvezérlők kiterjesztése például.

Egyirányú elvégzéséhez azt használja a [telephelyek közötti VPN](https://www.techopedia.com/definition/30747/site-to-site-vpn). A telephelyek közötti VPN és a pont-pont típusú VPN közötti különbség, hogy az utóbbi egyetlen eszközt egy virtuális hálózathoz csatlakozik. A telephelyek közötti VPN a teljes hálózatokhoz (például a helyszíni hálózat) egy virtuális hálózathoz csatlakozik. Pont-pont VPN egy virtuális hálózathoz a rendkívül biztonságos IPsec-bújtatásos mód VPN protokollt használja.

További információ:

* [Erőforrás-kezelő VNet létrehozása az Azure portál használatával pont-pont VPN-kapcsolattal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [A VPN Gateway tervezése és kialakítása](../vpn-gateway/vpn-gateway-plan-design.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-dedicated-wan-link"></a>A helyszíni hálózat csatlakozni egy dedikált WAN-kapcsolaton egy virtuális hálózat
Pont-pont és a pont-pont VPN-kapcsolatok érvényben engedélyezése közötti kapcsolatot nyújthassanak. Azonban egyes szervezetek vegye figyelembe, hogy a következő hátrányokkal:

* VPN-kapcsolatok az interneten keresztül helyezze át az adatokat. Ez azt mutatja, hogy ezek a kapcsolatok adminisztrációjával kapcsolatos adatmozgatás nyilvános hálózaton keresztül lehetséges biztonsági problémáját. Ezenkívül megbízhatósági és rendelkezésre állás a hálózati kapcsolatok használata esetén nem biztosítható.
* Virtuális hálózatok VPN-kapcsolatok az egyes alkalmazások és a céljából, amint azok maximális kimenő körülbelül 200 Mbps sebességnél sávszélesség esetleg nincs.

A szervezeteknek, amelyek általában a létesítmények közötti kapcsolatok van szükség a legmagasabb szintű biztonság és elérhetőség dedikált WAN-kapcsolatok használatával kapcsolódni a távoli helyeken. Azure lehetővé teszi egy dedikált WAN-kapcsolaton, melyekkel a helyszíni hálózat egy virtuális hálózathoz való kapcsolódáshoz használja. Az Azure ExpressRoute lehetővé teszi, hogy ez.

További információ:

* [ExpressRoute műszaki áttekintés](../expressroute/expressroute-introduction.md)

### <a name="connect-virtual-networks-to-each-other"></a>Virtuális hálózatok csatlakozni egymáshoz
A központi telepítések sok virtuális hálózatok használatát is. Különböző oka miért erre akkor lehet szükség. Egyszerűbbé teheti a kezelését érdemes, vagy a biztonság fokozása érdekében érdemes lehet. Különböző virtuális hálózatokon lévő erőforrások helyezésének kifejlesztésének, függetlenül előfordulhat, ha azt szeretné, hogy minden csatlakozni egymással a hálózaton lévő erőforrások alkalommal.

Egy elem egy virtuális hálózat csatlakozni egy másik virtuális hálózati szolgáltatások "ismétlési vissza" szolgáltatások az interneten keresztül. A kapcsolat elindítja egy virtuális hálózaton, az interneten keresztül halad, és majd ismét elérhető lesz a cél virtuális hálózathoz. Ez a beállítás a biztonsági problémák rejlő bármely internetes kommunikáció a kapcsolatot mutatja.

Jobb megoldás lehet létrehozni, amely összeköti a két virtuális hálózat közötti pont-pont VPN-en. Ezt a módszert használja, azonos [IPsec-bújtatásos mód](https://technet.microsoft.com/library/cc786385.aspx) protokoll a fent említett létesítmények közötti pont-pont VPN-kapcsolatot.

Ez a megközelítés előnye, hogy a VPN-kapcsolat létrejött-e az Azure-hálózat-hálón, ne csatlakozzanak az interneten keresztül. Ez lehetővé teszi egy további biztonsági, pont-pont az interneten keresztül csatlakozó virtuális magánhálózatok képest réteget.

További információ:

* [Egy VNet – VNet-kapcsolat beállítása az Azure Resource Manager és a PowerShell használatával](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="availability"></a>Rendelkezésre állás
Rendelkezésre állás bármilyen biztonsági program nyilvános kulcsokra épülő. Ha a felhasználók és rendszerek nem férnek hozzá Mi a hálózaton keresztüli eléréséhez szükséges, a szolgáltatás is tekinthető sérült. Azure rendelkezik hálózatkezelési technológiákat, amelyek támogatják a magas rendelkezésre állású következő mechanizmusok alapján:

* HTTP-alapú terheléselosztás
* Hálózati szintű terheléselosztás
* Globális terheléselosztás

Terheléselosztás, akkor egy eszköz úgy tervezték, hogy egyaránt terjeszteni a több eszközök közötti kapcsolatokat. A terheléselosztást céljai a következők:

* Magasabb rendelkezésre állását. Ha az egyenleg kapcsolatok több eszközön, egy vagy több olyan eszközt a elérhetetlenné válhat a szolgáltatás veszélyeztetése nélkül. A fennmaradó online eszközökön futó szolgáltatások továbbra is a tartalmat a szolgáltatásból.
* A teljesítmény növelése. Ha az egyenleg kapcsolatok több eszközön, egyetlen eszközt nincs minden feldolgozás kezelésére. Ehelyett a feldolgozás és a memória iránti igények kielégítése érdekében, de a tartalom megoszlik több eszközön.

### <a name="http-based-load-balancing"></a>HTTP-alapú terheléselosztás
A szervezeteknek, amelyek gyakran futtassa web-alapú szolgáltatások felügyelni kívánt egy HTTP-alapú elosztott terhelésű webes szolgáltatások elé. Ez segít a megfelelő szint teljesítménye és magas rendelkezésre állás biztosítása. Hagyományos, a hálózati terheléselosztók függ a hálózat és az átviteli rétegbeli protokollokkal. HTTP-alapú terheléselosztók, másrészt döntéseket a HTTP protokoll jellemzői alapján.

Az Azure Application Gateway HTTP-alapú terheléselosztást biztosít a web-alapú szolgáltatásokhoz. Alkalmazásátjáró támogatja:

* A munkamenet cookie-alapú kapcsolatot. Ez a funkció gondoskodik arról, hogy a kiszolgálóhoz, hogy a terheléselosztó mögött fennálló kapcsolatok marad az ügyfél és kiszolgáló közötti változatlanok maradnak. Ez biztosítja, hogy a tranzakciók stabilitását.
* SSL-kiszervezés. Amikor egy ügyfél kapcsolódik a terheléselosztót, hogy munkamenet titkosítva van-e a HTTPS (SSL) protokoll használatával. Azonban ahhoz, hogy a teljesítmény növelése érdekében használhatja a (titkosítatlanul) HTTP protokollal csatlakozni a terheléselosztó és a terheléselosztó mögött a webkiszolgáló között. Ezt nevezzük "SSL kiszervezési", mert a webkiszolgálók, a terheléselosztó mögött a processzor van szükség a titkosítás nem tapasztalnak. A webkiszolgálók is ezért szolgáltatáskérések gyorsabban.
* URL-alapú tartalom útválasztást. Ez a funkció lehetővé teszi a döntések kapcsolatos Ha a célként megadott URL-címe alapján előre kapcsolatok a terheléselosztóhoz. Ez a mint megoldások, amelyek IP-címek alapján terheléselosztási döntések betöltése sokkal nagyobb rugalmasságot biztosít.

További információ:

* [Átjáró – áttekintés](../application-gateway/application-gateway-introduction.md)

### <a name="network-level-load-balancing"></a>Hálózati szintű terheléselosztás
HTTP-alapú terheléselosztást, ellentétben a hálózati szintű terheléselosztás döntéseket IP-cím és port (TCP és UDP) számok alapján.
A előnyöket hálózati szintű terheléselosztás az Azure-ban Azure terheléselosztó használatával. Terheléselosztó néhány főbb jellemzői a következők:

* IP-cím és port számok alapján szint a terheléselosztás.
* Bármely alkalmazás layer protokoll támogatása.
* Felhőalapú szolgáltatások szerepkörpéldányokat és egyenlegének betöltése az Azure virtuális gépekhez.
* Internet felé néző (külső terheléselosztás) és a nem internetes is használható (belső terheléselosztás) alkalmazások és a virtuális gépek.
* Az Endpoint figyelését, amely azt határozza meg, ha a terheléselosztó mögött szolgáltatások váltak nem érhető el.

További információ:

* [Internetre irányuló terheléselosztót között több virtuális gépeket vagy szolgáltatásokat](../load-balancer/load-balancer-internet-overview.md)
* [Belső terheléselosztó áttekintése](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>Globális terheléselosztás
Egyes szervezetek szeretné lehetséges, hogy a legmagasabb szintű elérhetőség. Egyik e cél eléréséhez módja alkalmazások üzemeltetését globálisan elosztott adatközpontokban. Egy alkalmazás világszerte található adatközpontokban üzemel esetén lehetséges egy teljes geopolitikai régió elérhetetlenné válik, és továbbra is fennáll az alkalmazás, és fut.

A terheléselosztás stratégia is partíciónként akár jobb teljesítményt nyújt. Megadhatja, hogy a szolgáltatás számára az adatközpontban, az eszközre, amelyet a kérés legközelebb esik kérelmeket.

Az Azure ettől kezdve az Azure Traffic Manager globális terheléselosztást előnyeit.

További információ:

* [A Traffic Manager ismertetése](../traffic-manager/traffic-manager-overview.md)


## <a name="name-resolution"></a>Névfeloldás
Névfeloldás, akkor minden szolgáltatás Azure működteti a kritikus függvény. Biztonsági szempontból sérült biztonság esetén a név feloldása függvény a helyek egy támadó helyhez kérelmeinek átirányítása egy támadó vezethet. Biztonságos névfeloldás feltétele a felhőben üzemeltetett szolgáltatások.

Végre kell hajtania névfeloldás két típusa van:

* Belső névfeloldást. A szolgáltatások a virtuális hálózatok, a helyszíni hálózatokhoz vagy mindkettőt használja. A belső névfeloldáshoz használt nevek nem érhetők el az interneten keresztül. Optimális biztonsági fontos, hogy a belső név feloldása séma nem érhető el a külső felhasználók számára.
* Külső névfeloldás. Ez szolgál, személyek és az eszközök a helyszíni hálózatokhoz és a virtuális hálózatok kívül. Ezek a jelenik-e az internethez, és segítségével a kapcsolatot a felhő alapú szolgáltatások neveit.

A belső névfeloldást két lehetőség közül választhat:

* A virtuális hálózat DNS-kiszolgáló. Amikor létrehoz egy új virtuális hálózat, a DNS-kiszolgáló létrejön. A DNS-kiszolgáló képes névfeloldásra a gépet a virtuális hálózat található. A DNS-kiszolgáló nem konfigurálható, az Azure-hálót manager kezeli, és ezért segítségével biztosíthatja az a név feloldása megoldás.
* Kapcsolja a saját DNS-kiszolgáló. Lehetősége van a saját kiválasztása a virtuális hálózat DNS-kiszolgáló helyezésének. A DNS-kiszolgáló lehet egy Active Directory integrált DNS-kiszolgáló vagy egy dedikált DNS-kiszolgáló megoldást, amely az Azure piactérről szerezhető be egy Azure partner által biztosított.

További információ:

* [Virtual network áttekintése](../virtual-network/virtual-networks-overview.md)
* [A virtuális hálózat által használt DNS-kiszolgálók kezelése](../virtual-network/manage-virtual-network.md#change-dns-servers)

A külső névfeloldáshoz két lehetőség közül választhat:

* A saját külső DNS kiszolgáló a helyi gazdagép.
* A saját külső DNS-kiszolgáló üzemeltetésére szolgáltató.

Sok nagy méretű szervezeteknek saját DNS kiszolgálók helyszíni üzemeltetésére. Ez akkor végezhető el, mert a hálózati szakértelmét és a globális jelenlét ehhez.

A legtöbb esetben érdemes szolgáltató a DNS-név feloldása szolgáltatások futtatásához. Ezek a szolgáltatók rendelkezik, a hálózati szakértelmét és a globális jelenlét a névfeloldási szolgáltatásokat nagyon magas rendelkezésre állásának biztosításához. Rendelkezésre állási alapvető fontosságú a DNS-szolgáltatásokra, mert ha meghibásodik a névfeloldási szolgáltatást, nem fogja tudni elérni az internetes szolgáltatások.

Azure biztosít, a magas rendelkezésre állású és performant külső DNS-megoldást az Azure DNS formájában. A külső név feloldása megoldás kihasználja a világ Azure DNS-infrastruktúra. Lehetővé teszi a tartomány ugyanazon hitelesítő adatokkal, API-k, eszközök segítségével, és az egyéb Azure-szolgáltatások, számlázási, az Azure-ban. Az Azure részeként a platform épített erős biztonsági ellenőrzéseket is örököl.

További információ:

* [Az Azure DNS áttekintése](../dns/dns-overview.md)

## <a name="perimeter-network-architecture"></a>Külső hálózati architektúra
Sok nagy méretű szervezeteknek szegélyhálózat használjon szegmentálja a hálózatokon, és az internethez és a szolgáltatások közötti puffer-zóna létrehozása. A hálózaton, a peremhálózati része egy alacsony biztonsági szintű zónában minősül, és nem kiemelten értékesnek kerülnek, az adott hálózati szegmensben. Látni fogja, általában egy adott hálózati csatoló a külső hálózati szegmensen rendelkező hálózati biztonsági eszközöket. Egy másik hálózati adapter csatlakozik-e a hálózathoz, virtuális gépek és szolgáltatások, amelyek az internetről bejövő kapcsolatok fogadására.

Számos különböző módon a szegélyhálózat kialakítani. A döntést és központi telepítése a szegélyhálózaton, majd szegélyhálózati milyen típusú hálózati használata, ha úgy dönt, hogy használja, attól függ, hogy a hálózati biztonsági követelményeinek.

További információ:

* [A Microsoft Felhőszolgáltatásai és a hálózati biztonság](../best-practices-network-security.md)


## <a name="monitoring-and-threat-detection"></a>Figyelés és a fenyegetések észlelése

Azure segítséget nyújt a korai észlelése, figyelés, a kulcsfontosságú terület képességeket biztosít, és a hálózati forgalom gyűjtése és ellenőrzése.

### <a name="azure-network-watcher"></a>Azure Network Watcher
Az Azure hálózati figyelőt segíthet elhárításában, és teljesen új biztonsági problémákat azonosítása eszközöket biztosít.

[Biztonsági csoport megtekintése ](../network-watcher/network-watcher-security-group-view-overview.md) elősegíti a virtuális gépek naplózási és biztonsági megfelelőségét. E szolgáltatás használatával hajtsa végre a programozott eseményeket, a baseline házirendek, a virtuális gépek mindegyikének hatékony szabályokra a szervezet által meghatározott összehasonlítása. Ez segít azonosítani a konfigurációs eltéréseket.

[Csomagrögzítéssel](../network-watcher/network-watcher-packet-capture-overview.md) lehetővé teszi a hálózati forgalmat a virtuális gép rögzítése. Hálózati statisztikákat gyűjt, és elhárítása alkalmazás, amely lehet hasznos információt a hálózati behatolások vizsgálata során. Ez a szolgáltatás együtt az Azure Functions segítségével indítsa el a hálózati rögzítést adott Azure riasztás.

A hálózati figyelőt és teszteléséhez egyes szolgáltatásai a tesztkörnyezetek indítása további információkért lásd: [figyelési áttekintés Azure hálózati figyelőt](../network-watcher/network-watcher-monitoring-overview.md).

>[!NOTE]
A rendelkezésre állás és a szolgáltatás állapotát a legfrissebb értesítések, tekintse meg a [Azure frissítések lap](https://azure.microsoft.com/updates/?product=network-watcher).

### <a name="azure-security-center"></a>Azure Security Center
Az Azure Security Center segít a megakadályozása, észlelésében és kezelésében fenyegetések, és biztosítja, hogy lássák, és szabályozhatják, az Azure-erőforrások biztonságának nőtt. Biztosít integrált biztonsági monitorozást és az Azure-előfizetésekkel, megkönnyíti a nehezen észlelhető fenyegetések azonosítását szabályzatkezelést, és számos biztonsági megoldással együttműködik.

A Security Center segítséget nyújt a optimalizálása és hálózati biztonsági által figyelni:

* Hálózati biztonsági javaslatok biztosítása.
* A hálózati biztonsági konfiguráció állapotának figyelése.
* Fenyegetések, mind a végpont és a hálózati szintű küld figyelmeztetést a hálózati alapján.

További információ:

* [Az Azure Security Center bemutatása](../security-center/security-center-intro.md)


### <a name="logging"></a>Naplózás
Hálózati szintű naplózási, akkor egy olyan hálózati biztonsági forgatókönyv kulcs függvény. Az Azure bejelentkezhet NSG-ket a hálózati szint naplózási adatok eléréséhez használt információkat. Az NSG-naplózást is, olvasható be a következőről:

* [Tevékenységi naplóit](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md). Ezek a naplók segítségével megtekintheti az Azure-előfizetések küldve minden műveletnél. Ezek a naplók alapértelmezés szerint engedélyezve vannak, és használhatja az Azure-portálon. Ezek volt korábbi nevén naplózási és a műveleti naplókat.
* Eseménynaplók. Ezek a naplók milyen NSG-szabályok alkalmazott ismertetik.
* A számláló naplókat. Ezek a naplók lehetővé teszik, hogy tudja, hogy hányszor minden NSG-szabály alkalmazása a forgalom engedélyezése vagy megtagadása.

Is [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), hatékony képi megjelenítés eszköz megtekintése, és ezek a naplók elemzése.

További információ:

* [Naplóelemzési a hálózati biztonsági csoportokkal (NSG-k)](../virtual-network/virtual-network-nsg-manage-log.md)
