---
title: "Hálózati követelmények az Azure biztonsági fogalmak |} Microsoft Docs"
description: " Ez a cikk könnyen érthető rendelkezik Microsoft Azure hálózati biztonság területén kínálnak. Azt magyarázattal alapszintű hálózati biztonsági Alapfogalmak és követelmények és információk az Azure rendelkezik kínálnak az egyes ezeket a területeket. "
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
ms.date: 04/27/2017
ms.author: terrylan
ms.openlocfilehash: 27243856d0c6b70c7515b6bde66b99ef6160eb36
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-network-security-overview"></a>Az Azure biztonsági áttekintése
A Microsoft Azure tartalmazza az alkalmazás és szolgáltatás hálózati kapcsolati követelményeinek támogatására robusztus hálózati infrastruktúrát. Helyszíni között, az Azure-ban lévő erőforrások közötti hálózati kapcsolatot, és Azure üzemeltetett erőforrásokhoz, és hogy, és az internetről és az Azure.

Ez a cikk célja, könnyebben átláthatja rendelkezik Microsoft Azure hálózati biztonság területén kínálnak. Itt azt magyarázattal alapszintű hálózati biztonsági alapfogalmait és a követelmények. Azt is biztosítanak információkat a Azure rendelkezik kínálnak az egyes ezeket a területeket, valamint a segítségével egy mélyrehatóbb ismereteket szerezhet érdekes területek mutató hivatkozásokat tartalmaz.

Azure biztonsági áttekintése cikkben elsősorban az alábbi területeken:

* Az Azure hálózatkezelés
* Hálózati hozzáférés-vezérlés
* Biztonságos távoli hozzáférés és a létesítmények közötti kapcsolat
* Rendelkezésre állás
* Névfeloldás
* DMZ-architektúra
* Figyelés és a fenyegetések észlelése


## <a name="azure-networking"></a>Azure-hálózatok
Virtuális gépek hálózati kapcsolattal kell. Ez a követelmény kielégítése érdekében az Azure-nak egy Azure virtuális hálózatra kell csatlakoztatni a virtuális gépek. Egy Azure virtuális hálózatra egy logikai szerkezet, a fizikai Azure hálózati háló platformra épül. Minden logikai Azure Virtual Network el különítve a többi Azure virtuális hálózatok. Ezzel a megoldással biztosítja, hogy a hálózati forgalmat a központi telepítés nem érhető el más Microsoft Azure-ügyfelek.

További információ:

* [Virtual Network áttekintése](../virtual-network/virtual-networks-overview.md)


## <a name="network-access-control"></a>Hálózati hozzáférés-vezérlés
Hálózati hozzáférés-vezérlés a folyamat, kapcsolat és konkrét eszközöket vagy egy Azure virtuális hálózatban található alhálózatokhoz való korlátozása. A hálózati hozzáférés-vezérlés célja korlátozza a hozzáférést a virtuális gépek és szolgáltatások jóváhagyott felhasználók és eszközök számára. Hozzáférés-vezérlést alapul a engedélyezi vagy megtagadja a kapcsolatok döntéseinek felé és felől a virtuális gépet vagy szolgáltatást.

Azure számos különböző típusú hálózati hozzáférés-vezérlés például támogatja:

* Hálózati réteg vezérlő
* Útvonal-vezérlés és a kényszerített bújtatás
* Virtuális hálózati biztonsági készülékek

### <a name="network-layer-control"></a>Hálózati réteg vezérlő
Minden biztonságos telepítéséhez szükséges hálózati hozzáférés-vezérlés bizonyos mértékét. Hálózati hozzáférés-vezérlés célja a szükséges rendszerek virtuálisgép-kommunikációt, és arról, hogy más kommunikációs kísérlet vannak-e tiltva.

Ha módosítania kell az alapvető hálózati szintű hozzáférés-vezérlés (az IP-cím és a TCP vagy UDP protokoll alapján), majd használhatja hálózati biztonsági csoportok. A hálózati biztonsági csoport (NSG) egy tűzfal alapszintű csomag, és lehetővé teszi a alapuló hozzáférés-vezérlésének egy [5 rekordos](https://www.techopedia.com/definition/28190/5-tuple). Az NSG-k alkalmazás réteg ellenőrző nem ad meg, vagy hitelesített hozzáférés-vezérlést.

További információ:

* [Hálózati biztonsági csoportok](../virtual-network/virtual-networks-nsg.md)

### <a name="route-control-and-forced-tunneling"></a>Útvonal-vezérléshez és a kényszerített bújtatás
Az Azure virtuális hálózat útválasztási viselkedés képes a kritikus hálózati biztonság és a hozzáférés képessége. Ha útválasztási helytelenül van konfigurálva, a alkalmazások és szolgáltatások a virtuális gépen futó csatlakozhat jogosulatlan eszközt, beleértve a birtokolt és a potenciális támadók által működtetett rendszereket.

Azure hálózatkezelés támogatja a hálózati forgalmat az Azure virtuális hálózat az útválasztási viselkedés testreszabása. Ez lehetővé teszi, hogy módosítsa az alapértelmezett útválasztási táblabejegyzéseket az Azure virtuális hálózatban. Útválasztási viselkedés irányítását segítségével győződjön meg arról, hogy egy adott eszköz vagy eszközcsoportra származó összes forgalmat be- vagy a virtuális hálózaton keresztül egy konkrét helyre.

Például lehetséges, hogy a virtuális hálózati biztonsági készülékek a Azure virtuális hálózaton. Győződjön meg arról, hogy az Azure Virtual Network érkező vagy oda irányuló összes forgalom végighalad az adott virtuális biztonsági készülék szeretné. Ehhez konfigurálásával [felhasználó által megadott útvonalak](../virtual-network/virtual-networks-udr-overview.md) az Azure-ban.

[A kényszerített bújtatás](https://www.petri.com/azure-forced-tunneling) egy mechanizmus segítségével győződjön meg arról, hogy a szolgáltatások használata nem engedélyezett a kezdeményezzen kapcsolatot az internethez csatlakozó eszközökről. Vegye figyelembe, hogy ez eltér a bejövő kapcsolatok fogadását, és ezután válaszol rájuk. Az internetes gazdagépek a kérelmek megválaszolásához szükséges előtér-webkiszolgálók, és így Internet-forrása forgalom engedélyezve van, a webes kiszolgálókhoz történő bejövő és a webkiszolgálók el válaszol.

Hogy nem szeretné az előtér-webkiszolgáló kezdeményezéséhez egy kimenő kérelem. Az ilyen kérelmeket is képviselnek biztonsági kockázatot jelent, mert kártevőket tölthetnek le ezeket a kapcsolatokat lehetett használni. Akkor is, ha szeretné, hogy ezeket a kimenő kérések Internet kezdeményezése előtér-kiszolgálókat, hogy halad át a helyszíni webes proxykat, így kihasználhatja a szűrést és a naplózás URL-érdemes.

Ehelyett volna használni kívánt kényszerített bújtatás Ennek megakadályozására. Ha engedélyezi a kényszerített bújtatást, az internetre létesített összes kapcsolat kényszerítve vannak-e a helyszíni átjárón keresztül. Ha kihasználja a felhasználó által megadott útvonalak kényszerített bújtatás konfigurálása

További információ:

* [Mik azok a felhasználó által megadott útvonalak és IP-továbbítás](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Virtuális hálózati biztonsági készülékek
Amíg a hálózati biztonsági csoportok, a felhasználó által megadott útvonalak és a kényszerített bújtatás biztosítanak, a hálózati és a szállítási réteg biztonsági szintet a [OSI-modell](https://en.wikipedia.org/wiki/OSI_model), előfordulhat, hogy ha azt szeretné engedélyezni a magasabb, mint a hálózati szintű biztonságot alkalommal.

Például a biztonsági követelmények állhatnak:

* Hitelesítési és engedélyezési az alkalmazásokhoz való hozzáférés engedélyezése előtt
* -Behatolásérzékelési és a behatolás-válasz
* Alkalmazás réteg vizsgálatát a magas szintű protokollok
* URL-CÍMEK szűrése
* Hálózati szintű víruskereső és kártevőirtó
* Botot elleni védelem
* Alkalmazás-hozzáférés-vezérlés
* További DDoS-védelem (fent a DDoS-védelem biztosított, az Azure-hálót, maga)

Továbbfejlesztett hálózati biztonsági funkció az Azure partneri megoldás használatával végezheti el. Megtalálhatja a legfrissebb Azure partner hálózati biztonsági megoldások látogasson el a [Azure piactér](https://azure.microsoft.com/marketplace/) és a keresést a "biztonság" és "hálózati biztonság."

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Biztonságos távoli hozzáférést és helyszínek közötti kapcsolat
A telepítő, konfigurálási és kezelése az Azure-erőforrások igényeinek távolról kell elvégezni. Ezenkívül előfordulhat, hogy számára telepíteni kívánja [hibrid informatikai](http://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) összetevők a helyszíni megoldások és az Azure nyilvános felhőjében található. Ezek a forgatókönyvek biztonságos távoli hozzáférést igényelnek.

Az Azure hálózatkezelés a következő biztonságos távoli hozzáférés-forgatókönyveket teszi lehetővé:

* Az egyéni munkaállomások csatlakozni az Azure virtuális hálózat
* A helyszíni hálózat egy Azure virtuális hálózathoz egy VPN-csatlakozás
* A helyszíni hálózat csatlakozni egy Azure virtuális hálózatot egy dedikált WAN-hivatkozás
* Egy Azure virtuális hálózatot csatlakozni egymáshoz

### <a name="connect-individual-workstations-to-an-azure-virtual-network"></a>Az egyéni munkaállomások csatlakozni egy Azure virtuális hálózat
Előfordulhat, ha azt szeretné, ahhoz, hogy egyes fejlesztők és a műveletek csoporthoz, és kezelheti a virtuális gépek és szolgáltatások az Azure-ban. Például hozzá kell férnie egy virtuális gépet egy Azure virtuális hálózatra, és a biztonsági házirend nem engedélyezi az egyes virtuális gépekhez való RDP és az SSH távelérési. Ebben az esetben egy pont – hely VPN-kapcsolatot is használhatja.

A pont-pont VPN-kapcsolat használ a [az SSTP VPN](https://technet.microsoft.com/library/cc731352.aspx) protokoll ahhoz, hogy állítsa be a magán- és biztonságos kapcsolatot a felhasználó és az Azure virtuális hálózat között. Miután a VPN-kapcsolat létrejött, a felhasználó fog tudni RDP és az SSH be a virtuális gépek adatait az Azure virtuális hálózaton a VPN-kapcsolaton keresztül (feltéve, hogy a felhasználó képes hitelesíteni, és engedélyezve van).

További információ:

* [PowerShell virtuális hálózat egy pont – hely kapcsolat beállítása](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-vpn"></a>A helyszíni hálózati csatlakozás VPN-nel rendelkező Azure virtuális hálózat
Érdemes lehet a teljes vállalati hálózaton, vagy részeit, egy Azure virtuális hálózathoz való kapcsolódáshoz. Ez az a hibrid informatikai gyakori forgatókönyvek ahol vállalat [kiterjesztése a helyszíni adatközpontját az Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). Sok esetben a vállalatok a szolgáltatás az Azure és részei a helyszínen, például amikor egy megoldás tartalmaz az Azure és a háttér-adatbázisok a helyszíni előtér-webkiszolgálók részei fogja futtatni. Az ilyen típusú "létesítmények közötti" azt is ellenőrizze található Azure felügyeleti erőforrások több biztonságos, és engedélyezze a forgatókönyvek az Azure Active Directory-tartományvezérlők kiterjesztése például.

Egyirányú elvégzéséhez azt használja a [telephelyek közötti VPN](https://www.techopedia.com/definition/30747/site-to-site-vpn). A telephelyek közötti VPN és a pont-pont típusú VPN közötti különbség, hogy egy pont – hely VPN csatlakozik egyetlen eszközt egy Azure virtuális hálózatra, amíg a telephelyek közötti VPN a teljes hálózatokhoz (például a helyszíni hálózat) kapcsolódik az Azure virtuális hálózat. Egy Azure virtuális hálózatra pont-pont VPN-hálózatokhoz a rendkívül biztonságos IPsec-bújtatásos mód VPN protokollt használja.

További információ:

* [Erőforrás-kezelő VNet létrehozása az Azure portál használatával pont-pont VPN-kapcsolattal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [A VPN Gateway tervezése és kialakítása](../vpn-gateway/vpn-gateway-plan-design.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-dedicated-wan-link"></a>A helyszíni hálózat csatlakozni egy dedikált WAN-kapcsolat az Azure virtuális hálózat
Pont-pont és a pont-pont VPN-kapcsolatok érvényben engedélyezése közötti kapcsolatot nyújthassanak. Azonban egyes szervezetek vegye figyelembe, hogy a következő hátrányokkal:

* VPN-kapcsolatok az interneten keresztül helyezze át adatokat – Ez mutatja, hogy ezek a kapcsolatok potenciális biztonsági problémákat adminisztrációjával kapcsolatos adatmozgatás nyilvános hálózaton keresztül. Ezenkívül megbízhatósági és rendelkezésre állás a hálózati kapcsolatok használata esetén nem biztosítható.
* Az Azure virtuális hálózataihoz VPN-kapcsolatok az egyes alkalmazások és a céljából, amint azok maximális kimenő körülbelül 200 Mbps sebességnél korlátozott sávszélesség tekinthetők meg.

A szervezeteknek, amelyek általában a létesítmények közötti kapcsolatok van szükség a legmagasabb szintű biztonság és elérhetőség dedikált WAN-kapcsolatok használatával kapcsolódni a távoli helyeken. Azure lehetővé teszi egy dedikált WAN-kapcsolaton, melyekkel a helyszíni hálózat egy Azure virtuális hálózathoz való kapcsolódáshoz használja. Ez az Azure ExpressRoute keresztül engedélyezhető.

További információ:

* [ExpressRoute műszaki áttekintés](../expressroute/expressroute-introduction.md)

### <a name="connect-azure-virtual-networks-to-each-other"></a>Egy Azure virtuális hálózatot csatlakozni egymáshoz
Sok Azure virtuális hálózat használata a központi telepítés lehetőség. Számos oka miért erre akkor lehet szükség. Egyik oka lehet, hogy az egyszerűbb kezelés; egy másik biztonsági okok miatt lehet. A kifejlesztésének és az Azure különböző virtuális hálózatokon lévő erőforrások üzembe indoklása, függetlenül előfordulhat Ha azt szeretné, hogy minden kapcsolatot egy másik hálózaton található erőforrásokhoz.

Egy beállítás számára egy Azure virtuális hálózatra csatlakozni egy másik Azure virtuális hálózat szolgáltatások "ismétlési vissza" lenne az interneten keresztül. A kapcsolat lesz egy Azure virtuális hálózatra nyissa meg az interneten keresztül, és ezután térjen vissza a cél Azure-beli virtuális hálózathoz. Ez a beállítás a biztonsági problémák rejlő, bármely internetes kommunikáció a kapcsolatot mutatja.

Jobb megoldás lehet egy Azure virtuális hálózat-Azure virtuális hálózati telephelyek közötti VPN létrehozásához. Az Azure virtuális hálózat-Azure virtuális hálózati telephelyek közötti VPN használ azonos [IPsec-bújtatásos mód](https://technet.microsoft.com/library/cc786385.aspx) protokoll a fent említett létesítmények közötti pont-pont VPN-kapcsolatot.

Egy Azure virtuális hálózat-Azure virtuális hálózati telephelyek közötti VPN használatának előnye, hogy a VPN-kapcsolat létrejött-e az Azure-hálózat hálón ne csatlakozzanak az interneten keresztül. Ez lehetővé teszi egy további helyek VPN-ek az interneten keresztül csatlakozó képest biztonsági réteget.

További információ:

* [Egy VNet – VNet-kapcsolat beállítása az Azure Resource Manager és a PowerShell használatával](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="availability"></a>Rendelkezésre állás
Rendelkezésre állás bármilyen biztonsági program nyilvános kulcsokra épülő. Ha a felhasználók és rendszerek nem férnek hozzá Mi a hálózaton keresztüli eléréséhez szükséges, a szolgáltatás is tekinthető sérült. Azure rendelkezik hálózatkezelési technológiákat, amelyek támogatják a magas rendelkezésre állású következő mechanizmusok alapján:

* HTTP-alapú terheléselosztás
* Hálózati szintű terheléselosztás
* Globális terheléselosztás

Terheléselosztás, akkor egy eszköz úgy tervezték, hogy egyaránt terjeszteni a több eszközök közötti kapcsolatokat. A terheléselosztást céljai a következők:

* Rendelkezésre állás – növelhető, ha betölti egyenleg kapcsolatok több eszközön, egy vagy több olyan eszközt a elérhetetlenné válhat, és a fennmaradó online eszközökön futó szolgáltatások továbbra is a tartalmat a szolgáltatástól
* A teljesítmény növelése – ha egyenleg kapcsolatok több eszközön, egyetlen eszközt a processzor találat érvénybe nem rendelkezik. Ehelyett a feldolgozás és a memória iránti igények kielégítése érdekében, de a tartalom megoszlik több eszközön.

### <a name="http-based-load-balancing"></a>HTTP-alapú terheléselosztás
A szervezeteknek, amelyek gyakran futtassa web-alapú szolgáltatások felügyelni kívánt egy HTTP-alapú elosztott terhelésű megfelelő szintű teljesítmény és a magas rendelkezésre állás biztosítása érdekében webalkalmazás szolgáltatások elé. Hagyományos hálózati terheléselosztók, ellentétben a terheléselosztási döntéseit HTTP-alapú terheléselosztó vannak jellemzők alapján a HTTP protokoll, nem a a hálózat és az átviteli rétegbeli protokollokkal.

Biztosítja, hogy a HTTP-alapú terheléselosztást a web-alapú szolgáltatások, Azure biztosít az Azure Application Gateway. Az Azure Application Gateway támogatja:

* HTTP-alapú terheléselosztás – terhelés terheléselosztási döntéseket alapján a jellemző különleges a HTTP protokollal
* Munkamenet cookie-alapú kapcsolat – Ez a funkció gondoskodik arról, hogy a kiszolgálóhoz, hogy a terheléselosztó mögött fennálló kapcsolatok marad az ügyfél és kiszolgáló közötti változatlanok maradnak. Ez biztosítja, hogy a tranzakciók stabilitását.
* SSL-kiürítés – amikor az ügyfél kapcsolatot létesít a terheléselosztással, hogy a munkamenet az ügyfél és a terheléselosztó titkosítva van-e a HTTPS-kapcsolaton keresztül (SSL /) protokollt. Azonban ahhoz, hogy a teljesítmény növelése érdekében lehetősége van a terheléselosztó és a webkiszolgálón terheléselosztási terheléselosztó használata (titkosítatlanul) HTTP protokollt mögött közötti kapcsolat. Erre hivatkozik "SSL-kiszervezés", mert a webkiszolgálók, a terheléselosztó mögött a processzor van szükség a titkosítás nem észlelnek, és ezért képesnek kell lennie a szolgáltatáskérések gyorsabban.
* URL-alapú tartalom útválasztás – Ez a funkció a terheléselosztó döntések hová továbbítsa a célként megadott URL-címe alapján lehetővé teszi. Ez a mint megoldások, amelyek IP-címek alapján terheléselosztási döntések betöltése sokkal nagyobb rugalmasságot biztosít.

További információ:

* [Átjáró – áttekintés](../application-gateway/application-gateway-introduction.md)

### <a name="network-level-load-balancing"></a>Hálózati szintű terheléselosztás
Ellentétben a HTTP-alapú terheléselosztást, a hálózati szintű terheléselosztás döntéseket terhelés terheléselosztási IP-cím és port (TCP és UDP) számok alapján.
A előnyöket hálózati szintű terheléselosztás az Azure-ban az Azure terheléselosztó használatával. Az Azure Load Balancer néhány főbb jellemzői a következők:

* Hálózati szintű terheléselosztás IP-cím és port számok alapján
* Bármely alkalmazás layer protokoll támogatása
* Felhőalapú szolgáltatások szerepkörpéldányokat és az Azure virtuális gépek egyenlegének betöltése
* Internet felé néző (külső terheléselosztás) és a nem internetes is használható (belső terheléselosztás) alkalmazások és a virtuális gépek
* Figyelés, végpont, határozza meg, ha a terheléselosztó mögött szolgáltatások váltak nem érhető el

További információ:

* [Internetkapcsolattal rendelkező terheléselosztó több virtuális gép vagy szolgáltatás között](../load-balancer/load-balancer-internet-overview.md)
* [Belső terheléselosztó áttekintése](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>Globális terheléselosztás
Egyes szervezetek lehetséges érdemes a legmagasabb szintű elérhetőség. Egyik e cél eléréséhez módja alkalmazások üzemeltetését globálisan elosztott adatközpontokban. Egy alkalmazás világszerte található adatközpontokban üzemel esetén lehetséges egy teljes geopolitikai régió elérhetetlenné válik, és továbbra is fennáll az alkalmazás, és fut.

A rendelkezésre állási előnyeit úgy globálisan elosztott adatközpontokban üzemeltetéséhez, mellett is kaphat a teljesítménybeli előnyökben. A teljesítmény előnyök olyan mechanizmus, amely arra utasítja a szolgáltatás számára az adatközpontban, az eszközre, amelyet a kérés legközelebb esik kérelmek használatával érhető el.

Globális terheléselosztás adja meg mindkét ezek előnyeit. Az Azure ettől kezdve az Azure Traffic Manager globális terheléselosztást előnyeit.

További információ:

* [A Traffic Manager ismertetése](../traffic-manager/traffic-manager-overview.md)


## <a name="name-resolution"></a>Névfeloldás
Névfeloldás, akkor minden szolgáltatás Azure működteti a kritikus függvény. Biztonsági szempontból sérült biztonság esetén a név feloldása függvény a helyek egy támadó helyhez kérelmeinek átirányítása egy támadó vezethet. Biztonságos névfeloldás feltétele a felhőben üzemeltetett szolgáltatások.

Végre kell hajtania névfeloldás két típusa van:

* Belső névfeloldást – belső névfeloldást szolgáltatások az Azure virtuális hálózatok, a helyszíni hálózatokhoz vagy mindkettőt használja. A belső névfeloldáshoz használt nevek nem érhetők el az interneten keresztül. Optimális biztonsági fontos, hogy a belső név feloldása séma nem érhető el a külső felhasználók számára.
* Külső nevek feloldása – külső névfeloldás felhasználókkal és eszközökkel kívül a helyszíni és az Azure virtuális hálózatot használja. Ezek a láthatóak az internethez, és segítségével a kapcsolatot a felhő alapú szolgáltatások neveit.

A belső névfeloldást két lehetőség közül választhat:

* Egy Azure virtuális hálózat DNS-kiszolgáló – egy új, Azure virtuális hálózatban, létrehozásakor a DNS-kiszolgáló létrejön. A DNS-kiszolgáló az adott Azure virtuális hálózaton található gépek képes névfeloldásra. A DNS-kiszolgáló nem konfigurálható, és az Azure-hálót kezelője, így azt egy biztonságos név feloldása megoldás által felügyelt.
* Kapcsolja a saját DNS-kiszolgáló – lehetősége van a saját kiválasztása az Azure virtuális hálózat DNS-kiszolgáló üzembe. A DNS-kiszolgáló lehet egy Active Directory integrált DNS-kiszolgáló vagy egy dedikált DNS-kiszolgáló megoldást, amely az Azure piactérről szerezhető be egy Azure partner által biztosított.

További információ:

* [Virtual Network áttekintése](../virtual-network/virtual-networks-overview.md)
* [Egy virtuális hálózatot (VNet) által használt DNS-kiszolgálók kezelése](../virtual-network/virtual-network-manage-network.md#dns-servers)

A külső DNS-feloldás két lehetőség közül választhat:

* A saját külső DNS kiszolgáló helyszíni üzemeltetéséhez
* A saját külső DNS-kiszolgáló üzemeltetésére szolgáltató

Sok nagy méretű szervezeteknek saját DNS kiszolgálók helyszíni fogja futtatni. Ez akkor végezhető el, mert a hálózati szakértelmét és a globális jelenlét ehhez.

A legtöbb esetben érdemes szolgáltató a DNS-név feloldása szolgáltatások futtatásához. Ezek a szolgáltatók rendelkezik, a hálózati szakértelmét és a globális jelenlét a névfeloldási szolgáltatásokat nagyon magas rendelkezésre állásának biztosításához. Rendelkezésre állási elengedhetetlen a DNS-szolgáltatásokra, mert ha meghibásodik a névfeloldási szolgáltatást, nem fogja tudni elérni az internetes szolgáltatások.

Azure biztosít magas rendelkezésre állású és performant külső DNS-megoldást az Azure DNS formájában. A külső név feloldása megoldás kihasználja a világ Azure DNS-infrastruktúra. Lehetővé teszi az Azure ugyanazon hitelesítő adatokkal, API-k, eszközök segítségével, és az egyéb Azure-szolgáltatások, számlázási tartomány. Az Azure részeként a platform épített erős biztonsági ellenőrzéseket is örököl.

További információ:

* [Az Azure DNS áttekintése](../dns/dns-overview.md)

## <a name="dmz-architecture"></a>DMZ-architektúra
Számos vállalat szervezet DMZ-k segítségével szegmenseket, az internethez és a szolgáltatások közötti puffer-zóna létrehozása a hálózatokon. A hálózati DMZ része egy alacsony biztonsági szintű zónában minősülnek, és nem kiemelten értékesnek kerülnek, a hálózati szegmenst. Láthatja, amelyek egy adott hálózati csatoló a DMZ szegmens és virtuális gépeket és szolgáltatásokat, amelyek fogadják az internetről érkező bejövő kapcsolatokat tartalmazó hálózathoz csatlakozó hálózati illesztő hálózati biztonsági eszközök általában.

Számos DMZ tervezési és központi telepítése egy DMZ, a döntést és majd DMZ használandó, ha úgy dönt, hogy használni, milyen típusú a hálózatbiztonsági követelményei alapján.

További információ:

* [A Microsoft Felhőszolgáltatásai és a hálózati biztonság](../best-practices-network-security.md)


## <a name="monitoring-and-threat-detection"></a>Figyelés és a fenyegetések észlelése

Azure közül választhat a kulcsfontosságú terület korai észlelési képességeket biztosít, figyelés és gyűjtése, és tekintse át a hálózati forgalom.

### <a name="azure-network-watcher"></a>Az Azure hálózati figyelőt
Azure hálózati figyelőt tartalmaz számos képességeket kínál, amelyek segítenek a hibaelhárításban, valamint a adjon meg egy teljesen új eszközöket a biztonsági problémákat azonosítása.

[Biztonsági csoport megtekintése ](/network-watcher/network-watcher-security-group-view-overview.md) elősegíti a naplózás és a biztonsági megfelelőség virtuális gépek és összehasonlítása az alapkonfigurációk házirendeket, a virtuális gépek mindegyikének hatékony szabályokra a szervezet által meghatározott programozott naplózás végrehajtásához használható. Ez segít azonosítani a konfigurációs eltéréseket.

[Csomagrögzítéssel](/network-watcher/network-watcher-packet-capture-overview.md) lehetővé teszi a hálózati forgalmat a virtuális gép rögzítése. Azáltal, hogy hálózati statisztikákat gyűjt, és az alkalmazással kapcsolatos problémák elhárítása segítése mellett csomagrögzítéssel lehet hasznos információt a hálózati behatolások vizsgálata során. Ez a funkció együtt az Azure Functions segítségével indítsa el a hálózati rögzítést adott Azure riasztás.

További információ a Azure hálózati figyelőt és a vizsgálat indítása a fejlesztőlaborokban lévő funkciók némelyike vessen egy pillantást a [Azure hálózati figyelőt figyelési áttekintés](/network-watcher/network-watcher-monitoring-overview.md)

>[!NOTE]
Azure hálózati figyelőt a helyzet továbbra is a nyilvános előzetes verzió még nem rendelkezik azonos szintű rendelkezésre állást és megbízhatóságot, szolgáltatásokat, amelyek általában a rendelkezésre állási kiadási. Egyes funkciók nem támogatott, van, korlátozott képességeit, és előfordulhat, hogy nem érhető el az összes Azure helyét. A rendelkezésre állás és a szolgáltatás állapotát a legfrissebb értesítések, tekintse meg a [Azure frissítések lap](https://azure.microsoft.com/updates/?product=network-watcher)

### <a name="azure-security-center"></a>Azure Security Center
A Security Center segítséget nyújtanak a megakadályozása, észlelésében és kezelésében fenyegetések, és itt a lássák, és szabályozhatják, az Azure-erőforrások biztonságának nőtt. Biztosít integrált biztonsági monitorozást és az Azure-előfizetésekkel, megkönnyíti a nehezen észlelhető fenyegetések azonosítását szabályzatkezelést, és számos biztonsági megoldással együttműködik.

Azure Security Center segítségével optimalizálása és figyelheti a hálózati biztonsági megoldást:

* Hálózati biztonsági javaslatokat biztosít
* A hálózati biztonsági konfiguráció állapotának figyelése
* Hálózatalapú fenyegetések mindkét végpont és a hálózati szintű küld figyelmeztetést

További információ:

* [Azure Security Center bemutatása](../security-center/security-center-intro.md)


### <a name="logging"></a>Naplózás
Hálózati szintű naplózási, akkor egy olyan hálózati biztonsági forgatókönyv kulcs függvény. Az Azure a hálózati biztonsági csoportok hálózati szintű naplózási adatok eléréséhez használt információkat jelentkezhetnek. Az NSG-naplózást is, olvasható be a következőről:

* [Tevékenységi naplóit](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) – ezek a naplók segítségével az Azure-előfizetések küldve minden műveletnél. Ezek a naplók alapértelmezés szerint engedélyezve vannak, és használhatja az Azure-portálon. Ezek volt korábbi nevén "Naplófájlok" vagy "Működési Logs".
* Eseménynaplók – ezek a naplók ismertetik, milyen NSG-szabályok alkalmazása megtörtént.
* Számláló naplók – ezek a naplók lehetővé teszik, hogy tudja, hogy hányszor minden NSG-szabály alkalmazása a forgalom engedélyezése vagy megtagadása.

Is [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), hatékony képi megjelenítés eszköz megtekintése, és ezek a naplók elemzése.

További információ:

* [Naplóelemzési a hálózati biztonsági csoportokkal (NSG-k)](../virtual-network/virtual-network-nsg-manage-log.md)
