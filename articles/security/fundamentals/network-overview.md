---
title: Hálózati biztonsági fogalmak és követelmények az Azure-ban | Microsoft dokumentumok
description: Ez a cikk alapvető magyarázatokat tartalmaz a törzshálózati biztonsági fogalmakról és követelményekről, valamint arról, hogy az Azure mit kínál az egyes területeken.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: terrylan
ms.openlocfilehash: 2293618b0685fe71ae553a95797fe8bfe1fe968c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749935"
---
# <a name="azure-network-security-overview"></a>Az Azure hálózati biztonságának áttekintése

A hálózati biztonság úgy határozható meg, mint az erőforrások jogosulatlan hozzáféréssel vagy támadással szembeni védelmének folyamata, amely nek vezérlőket kell alkalmaznia a hálózati forgalomra. A cél annak biztosítása, hogy csak a jogszerű forgalom engedélyezett legyen. Az Azure egy robusztus hálózati infrastruktúrát is tartalmaz az alkalmazás- és szolgáltatáscsatlakozási követelmények támogatásához. A hálózati kapcsolat az Azure-ban található erőforrások, a helyszíni és az Azure által üzemeltetett erőforrások között, valamint az internet és az Azure között lehetséges.

Ez a cikk az Azure által a hálózati biztonság területén elérhető néhány lehetőséget ismerteti. A következőkről a következőkről tudhat meg:

* Azure-hálózatkezelés
* Hálózati hozzáférés-vezérlés
* Azure Firewall
* Biztonságos távelérés és létesítmények közötti kapcsolat
* Rendelkezésre állás
* Névfeloldás
* Peremhálózati (DMZ) architektúra
* Azure DDoS Protection
* Azure Front Door
* Traffic Manager
* Figyelés és fenyegetésészlelés

## <a name="azure-networking"></a>Azure-hálózatkezelés

Az Azure-nak szüksége van a virtuális gépek egy Azure virtuális hálózathoz való csatlakoztatására. A virtuális hálózat egy logikai konstrukció, amely a fizikai Azure hálózati hálóra épül. Minden virtuális hálózat el van különítve az összes többi virtuális hálózattól. Ez segít biztosítani, hogy a központi telepítések hálózati forgalma t nem érhető el más Azure-ügyfelek számára.

További információ:

* [Virtuális hálózat – áttekintés](../../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>Hálózati hozzáférés-vezérlés

A hálózati hozzáférés-vezérlés a virtuális hálózaton belüli adott eszközök vagy alhálózatok közötti kapcsolat korlátozása. A hálózati hozzáférés-vezérlés célja, hogy korlátozza a hozzáférést a virtuális gépek hez és szolgáltatásokhoz a jóváhagyott felhasználók és eszközök számára. A hozzáférés-vezérlés a virtuális géphez vagy szolgáltatáshoz való csatlakozás engedélyezésére vagy megtagadására vonatkozó döntéseken alapul.

Az Azure számos típusú hálózati hozzáférés-vezérlést támogat, például:

* Hálózati réteg vezérlése
* Útvonalvezérlés és kényszerbújtatás
* Virtuális hálózati biztonsági berendezések

### <a name="network-layer-control"></a>Hálózati réteg vezérlése

Minden biztonságos telepítés hez szükség van a hálózati hozzáférés bizonyos mértékére. A hálózati hozzáférés-vezérlés célja, hogy a virtuálisgép-kommunikációt a szükséges rendszerekre korlátozza. Más kommunikációs kísérletek blokkolva vannak.

> [!NOTE]
> A tárolótűzfalakat az [Azure storage biztonsági áttekintése](storage-overview.md) című cikk ismerteti

#### <a name="network-security-rules-nsgs"></a>Hálózati biztonsági szabályok (NSG-k)

Ha alapvető hálózati szintű hozzáférés-vezérlésre van szüksége (az IP-cím és a TCP- vagy UDP-protokollok alapján), használhatja a hálózati biztonsági csoportokat.If you need basic network level access control (based on IP address and the Tcp or UDP protocols), you can use Network Security Groups (NSGs). Az NSG egy alapvető, állapotalapú, csomagszűrő tűzfal, amely lehetővé teszi a hozzáférés [5-t.](https://www.techopedia.com/definition/28190/5-tuple) Az NSG-k olyan funkciókat tartalmaznak, amelyek egyszerűsítik a felügyeletet és csökkentik a konfigurációs hibák esélyét:

* **A kiterjesztett biztonsági szabályok** leegyszerűsítik az NSG-szabályok definícióját, és lehetővé teszik összetett szabályok létrehozását ahelyett, hogy több egyszerű szabályt kellene létrehoznia ugyanazon eredmény eléréséhez.
* **A szolgáltatáscímkék** olyan Microsoft által létrehozott címkék, amelyek IP-címek egy csoportját képviselik. Dinamikusan frissülnek, hogy olyan IP-tartományokat tartalmazzanak, amelyek megfelelnek a címkében való szerepeltetést meghatározó feltételeknek. Ha például olyan szabályt szeretne létrehozni, amely a keleti régió összes Azure-tárhelyére vonatkozik, használhatja a Storage.EastUS
* **Az alkalmazásbiztonsági csoportok** lehetővé teszik az erőforrások alkalmazáscsoportokba való üzembe helyezését és az erőforrásokhoz való hozzáférés szabályozását az alkalmazáscsoportokat használó szabályok létrehozásával. Ha például webkiszolgálókat telepít a "Webservers" alkalmazáscsoportba, létrehozhat egy olyan szabályt, amely az Internetről 443 forgalmat engedélyez a "Webservers" alkalmazáscsoport összes rendszerére.

Az NSG-k nem biztosítanak alkalmazásréteg-ellenőrzést vagy hitelesített hozzáférés-vezérlést.

További információ:

* [Hálózati biztonsági csoportok](../../virtual-network/security-overview.md)

#### <a name="asc-just-in-time-vm-access"></a>ASC éppen időben VM hozzáférés

[Az Azure security center](../../security-center/security-center-intro.md) kezelheti az NSG-k a virtuális gépeken, és zárolhatja a hozzáférést a virtuális géphez, amíg a megfelelő szerepköralapú hozzáférés-vezérlésRBAC-engedélyekkel rendelkező felhasználó hozzáférést nem kér. [RBAC](/azure/role-based-access-control/overview) Ha a felhasználó sikeresen jogosult ASC módosításokat végez az NSG-k, hogy a hozzáférést a kiválasztott portok a megadott ideig. Az idő lejártakor az NSG-k visszaállnak a korábbi biztonságos állapotukba.

További információ:

* [Azure Security Center Just in Time Access](../../security-center/security-center-just-in-time.md)

#### <a name="service-endpoints"></a>Szolgáltatásvégpontok

A szolgáltatásvégpontok egy másik módja a forgalom vezérlése. Korlátozhatja a támogatott szolgáltatásokkal való kommunikációt csak a virtuális hálózatokközvetlen kapcsolaton keresztül. A virtuális hálózatról a megadott Azure-szolgáltatásra irányuló forgalom a Microsoft Azure gerinchálózatán marad.  

További információ:

* [Szolgáltatásvégpontok](../../virtual-network/virtual-network-service-endpoints-overview.md#secure-azure-services-to-virtual-networks)

### <a name="route-control-and-forced-tunneling"></a>Útvonalvezérlés és kényszerbújtatás

Az útválasztási viselkedés szabályozása a virtuális hálózatokon kritikus fontosságú. Ha az útválasztás helytelenül van konfigurálva, a virtuális gépen tárolt alkalmazások és szolgáltatások nem engedélyezett eszközökhöz kapcsolódhatnak, beleértve a potenciális támadók által birtokolt és működtetett rendszereket is.

Az Azure networking támogatja az útválasztási viselkedés testreszabását a virtuális hálózatok hálózati forgalmának. Ez lehetővé teszi a virtuális hálózat alapértelmezett útválasztási táblabejegyzéseinek módosítását. Az útválasztási viselkedés szabályozása segít biztosítani, hogy egy adott eszközről vagy eszközcsoportból érkező összes forgalom egy adott helyen keresztül lépjen be vagy hagyja el a virtuális hálózatot.

Előfordulhat például, hogy van egy virtuális hálózati biztonsági berendezés a virtuális hálózaton. Győződjön meg arról, hogy a virtuális hálózatba irányuló és onnan érkező összes forgalom a virtuális biztonsági berendezésen keresztül történik. Ezt a [felhasználó által definiált útvonalak](../../virtual-network/virtual-networks-udr-overview.md) (UDRs) konfigurálásával az Azure-ban.

[A kényszerített bújtatás](https://www.petri.com/azure-forced-tunneling) olyan mechanizmus, amelynek segítségével biztosíthatja, hogy a szolgáltatások ne kezdeményezhessenek kapcsolatot az interneten lévő eszközökkel. Ne feledje, hogy ez nem képes elfogadni a bejövő kapcsolatokat, majd válaszolni rájuk. Az előtér-webkiszolgálóknak válaszolniuk kell az internetes állomásoktól érkező kérésekre, és így az internetes forrásból származó forgalom bevihető ezekbe a webkiszolgálókba, és a webkiszolgálók válaszolhatnak.

Amit nem szeretne engedélyezni, az egy előtér-webkiszolgáló, amely kimenő kérelmet kezdeményez. Az ilyen kérések biztonsági kockázatot jelenthetnek, mivel ezek a kapcsolatok rosszindulatú programok letöltésére használhatók. Még akkor is, ha azt szeretné, hogy ezek az előtér-kiszolgálók kimenő kérelmeket kezdeményezzenek az internetre, érdemes lehet kényszeríteni őket, hogy a helyszíni webes proxykon keresztül menjenek. Ez lehetővé teszi az URL-szűrés és -naplózás előnyeit.

Ehelyett azt szeretné, hogy a kényszerített bújtatás, hogy megakadályozzák ezt. Ha engedélyezi a kényszerített bújtatás, az összes kapcsolatot az internethez kényszeríti kénytelenek a helyszíni átjárón keresztül. A kényszerített bújtatás az UDRs előnyeinek kihasználásával konfigurálható.

További információ:

* [Mik azok a felhasználó által definiált útvonalak és IP-továbbítás?](../../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Virtuális hálózati biztonsági berendezések

Míg az NSG-k, az UdRs és a kényszerített bújtatás az [OSI-modell](https://en.wikipedia.org/wiki/OSI_model)hálózati és átviteli rétegeinek biztonsági szintjét biztosítja, érdemes lehet a biztonságot a hálózatnál magasabb szinten engedélyezni.

A biztonsági követelmények például a következők lehetnek:

* Hitelesítés és engedélyezés az alkalmazáshoz való hozzáférés engedélyezése előtt
* Behatolásészlelés és behatolás-elhárítás
* Alkalmazási réteg vizsgálata magas szintű protokollokhoz
* URL-szűrés
* Hálózati szintű víruskereső és kártevőirtó
* Anti-bot védelem
* Alkalmazáshozzáférés-vezérlés
* További DDoS-védelem (az Azure-háló által biztosított DDoS-védelem felett)

Ezeket a továbbfejlesztett hálózati biztonsági funkciókat egy Azure-partnermegoldás használatával érheti el. A legfrissebb Azure-partnerhálózati biztonsági megoldásokat az [Azure Piactérre](https://azure.microsoft.com/marketplace/), valamint a "biztonság" és a "hálózati biztonság" kifejezésre keresve találhatja meg.

## <a name="azure-firewall"></a>Azure Firewall

Az Azure Firewall egy felügyelt, felhőalapú hálózatbiztonsági szolgáltatás, amely Azure Virtual Network-erőforrásait védi. Ez egy szolgáltatásként nyújtott teljesen állapotalapú tűzfal, beépített magas rendelkezésre állással és korlátlan felhőalapú skálázhatósággal. Néhány funkció a következők:

* Magas rendelkezésre állás
* Felhőbeli méretezhetőség
* Alkalmazások teljes tartománynevére vonatkozó szűrési szabályok
* Hálózati forgalomra vonatkozó szűrési szabályok

További információ:

* [Az Azure Tűzfal áttekintése](/azure/firewall/overview)

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Biztonságos távelérés és létesítmények közötti kapcsolat

Az Azure-erőforrások beállítását, konfigurálását és kezelését távolról kell elvégezni. Emellett érdemes lehet telepíteni [a hibrid informatikai](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) megoldások, amelyek a helyszíni és az Azure nyilvános felhőben összetevőket. Ezek a forgatókönyvek biztonságos távelérést igényelnek.

Az Azure networking a következő biztonságos távelérési forgatókönyveket támogatja:

* Egyes munkaállomások csatlakoztatása virtuális hálózathoz
* A helyszíni hálózat csatlakoztatása virtuális hálózathoz VPN-nel
* Csatlakoztassa a helyszíni hálózatot egy virtuális hálózathoz egy dedikált WAN-kapcsolattal
* Virtuális hálózatok csatlakoztatása egymáshoz

### <a name="connect-individual-workstations-to-a-virtual-network"></a>Egyes munkaállomások csatlakoztatása virtuális hálózathoz

Előfordulhat, hogy engedélyezni szeretné az egyes fejlesztőknek vagy műveleti személyzetnek a virtuális gépek és szolgáltatások kezelését az Azure-ban. Tegyük fel például, hogy egy virtuális hálózaton lévő virtuális géphez kell hozzáférnie. De a biztonsági házirend nem engedélyezi az RDP vagy Az SSH távoli hozzáférést az egyes virtuális gépek. Ebben az esetben [pont-hely VPN-kapcsolatot](../../vpn-gateway/point-to-site-about.md) is használhat.

A pont-hely VPN kapcsolat lehetővé teszi, hogy a felhasználó és a virtuális hálózat közötti privát és biztonságos kapcsolatot hozzon létre. A VPN-kapcsolat létrehozásakor a felhasználó RDP vagy SSH a VPN-kapcsolaton keresztül a virtuális hálózat bármely virtuális gépére beviheti. (Ez azt feltételezi, hogy a felhasználó hitelesítheti magát, és jogosult.) A pont-hely VPN támogatja:

* Secure Socket Tunneling Protocol (SSTP), egy saját SSL-alapú VPN protokoll. Az SSL VPN-megoldás áthatolhat a tűzfalakon, mivel a legtöbb tűzfal megnyitja a 443-as TCP-portot, amelyet az SSL használ. Az SSTP csak Windows-eszközökön támogatott. Az Azure támogatja a Windows összes SSTP-vel (Windows 7 és újabb) verzióit.

* IKEv2 VPN, egy szabványalapú IPsec VPN-megoldás. Az IKEv2 VPN segítségével Macről is lehetségessé válik a csatlakozás (OSX 10.11-es vagy újabb verziók használata esetén).

* [OpenVPN](https://azure.microsoft.com/updates/openvpn-support-for-azure-vpn-gateways/)

További információ:

* [Virtuális hálózattal létesített pont-hely kapcsolat konfigurálása a PowerShell használatával](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-vpn"></a>A helyszíni hálózat csatlakoztatása virtuális hálózathoz VPN-nel

Előfordulhat, hogy a teljes vállalati hálózatot vagy annak egy részét egy virtuális hálózathoz szeretné csatlakoztatni. Ez gyakori a hibrid informatikai forgatókönyvekben, ahol a szervezetek [kiterjesztik a helyszíni adatközpontjukat az Azure-ra.](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84) Sok esetben a szervezetek üzemeltetik egy szolgáltatás egy része it az Azure-ban, és a helyszíni részek. Például előfordulhat, hogy ezt, ha egy megoldás tartalmazza az azure-beli előtér-webkiszolgálók és a háttér-adatbázisok helyszíni. Az ilyen típusú "telephelyek közötti" kapcsolatok biztonságosabbá teszik az Azure-ban található erőforrások kezelését, és olyan forgatókönyveket tesznek lehetővé, mint például az Active Directory-tartományvezérlők azure-ra való kiterjesztése.

Ennek egyik módja a [helyek közötti VPN](https://www.techopedia.com/definition/30747/site-to-site-vpn)használata. A helyek közötti VPN és a pont-hely VPN közötti különbség az, hogy az utóbbi egyetlen eszközt kapcsol össze egy virtuális hálózattal. A helyek közötti VPN egy teljes hálózatot (például a helyszíni hálózatot) köt össze egy virtuális hálózattal. A virtuális hálózat hoz a helyek közötti VPN-ek a rendkívül biztonságos IPsec bújtatási módú VPN protokollt használják.

További információ:

* [Erőforrás-kezelő virtuális hálózat létrehozása helyek közötti VPN-kapcsolattal az Azure Portal használatával](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Információk a VPN Gateway-ről](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-dedicated-wan-link"></a>Csatlakoztassa a helyszíni hálózatot egy virtuális hálózathoz egy dedikált WAN-kapcsolattal

A helyek és a helyek közötti VPN-kapcsolatok hatékonyak a létesítmények közötti kapcsolatok engedélyezéséhez. Egyes szervezetek azonban úgy vélik, hogy a következő hátrányai vannak:

* A VPN-kapcsolatok az interneten keresztül mozgatják az adatokat. Ez lefedi ezeket a kapcsolatokat a nyilvános hálózaton keresztül történő adatáthelyezéssel kapcsolatos potenciális biztonsági problémáknak. Ezenkívül az internetkapcsolat megbízhatósága és elérhetősége nem garantálható.
* Előfordulhat, hogy a virtuális hálózatokhoz való VPN-kapcsolatok nem rendelkeznek a sávszélességgel egyes alkalmazások és célokra, mivel körülbelül 200 Mbps sebességgel maximálisan kivannak.

Azok a szervezetek, amelyeknek a legmagasabb szintű biztonságra és rendelkezésre állásra van szükségük a telephelyek közötti kapcsolataikhoz, általában dedikált WAN-kapcsolatokat használnak a távoli helyekhez való csatlakozáshoz. Az Azure lehetővé teszi egy dedikált WAN-kapcsolat használatát, amelysegítségével csatlakoztathatja a helyszíni hálózatot egy virtuális hálózathoz. Az Azure ExpressRoute, az Express route direct és az Express route globális elérése ezt teszi lehetővé.

További információ:

* [Az ExpressRoute technikai áttekintése](../../expressroute/expressroute-introduction.md)
* [ExpressRoute közvetlen](../../expressroute/expressroute-erdirect-about.md)
* [Expressz útvonal globális elérése](../../expressroute/expressroute-global-reach.md)

### <a name="connect-virtual-networks-to-each-other"></a>Virtuális hálózatok csatlakoztatása egymáshoz

Számos virtuális hálózat ot használhat a központi telepítésekhez. Számos oka lehet, hogy ezt. Lehet, hogy egyszerűsíteni szeretné a felügyeletet, vagy nagyobb biztonságra van szüksége. Függetlenül attól, hogy milyen motivációt jelent az erőforrások különböző virtuális hálózatokra helyezése, előfordulhat, hogy az egyes hálózatokon lévő erőforrások at szeretné összekötni egymással.

Az egyik lehetőség az, hogy az egyik virtuális hálózaton lévő szolgáltatások egy másik virtuális hálózaton lévő szolgáltatásokhoz kapcsolódjanak, az interneten keresztül ily módon "visszahurkolva". A kapcsolat egy virtuális hálózaton indul, az interneten keresztül, majd visszatér a célvirtuális hálózathoz. Ez a beállítás a kapcsolatot az internetalapú kommunikációban rejlő biztonsági problémáknak teszi ki.

Jobb megoldás lehet egy helyek közötti VPN létrehozása, amely két virtuális hálózat között csatlakozik. Ez a módszer ugyanazt az [IPSec-bújtatási módú](https://technet.microsoft.com/library/cc786385.aspx) protokollt használja, mint a fent említett helyek közötti VPN-kapcsolat.

Ez a megközelítés előnye, hogy a VPN-kapcsolat az Azure-hálózati hálón keresztül jön létre, ahelyett, hogy az interneten keresztül csatlakozna. Ez egy további biztonsági réteget biztosít, mint az interneten keresztül csatlakozó helyek közötti VPN-ek.

További információ:

* [Virtuális hálózat és virtuális hálózat között létesített kapcsolat konfigurálása az Azure Resource Manager és a PowerShell használatával](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

A virtuális hálózatok csatlakoztatásának másik módja a [Virtuálishálózat-társviszony-létesítés](../../virtual-network/virtual-network-peering-overview.md). Ez a funkció lehetővé teszi, hogy két Azure-hálózatot csatlakoztasson, hogy a microsoftos gerincinfrastruktúrán keresztül történjen a kommunikáció anélkül, hogy az interneten keresztül történne. A virtuális hálózatok társviszony-létesítése két virtuális hálózatot kapcsolhat össze ugyanazon a régión belül, vagy két virtuális hálózatot az Azure-régiókban. Az NSG-k a különböző alhálózatok vagy rendszerek közötti kapcsolat korlátozására használhatók.

## <a name="availability"></a>Rendelkezésre állás

A rendelkezésre állás minden biztonsági program kulcsfontosságú eleme. Ha a felhasználók és a rendszerek nem férnek hozzá, amire szükségük van a hálózaton keresztül, a szolgáltatás sérülékenynek tekinthető. Az Azure olyan hálózati technológiákkal rendelkezik, amelyek a következő magas rendelkezésre állású mechanizmusokat támogatják:

* HTTP-alapú terheléselosztás
* Hálózati szintű terheléselosztás
* Globális terheléselosztás

A terheléselosztás egy olyan mechanizmus, amely egyenlően osztja el a kapcsolatokat több eszköz között. A terheléselosztás céljai a következők:

* A rendelkezésre állás növelése. Ha több eszközön keresztül tölti be a kapcsolatokat, egy vagy több eszköz elérhetetlenné válhat a szolgáltatás veszélyeztetése nélkül. A fennmaradó online eszközökön futó szolgáltatások továbbra is kiszolgálhatják a szolgáltatás ból származó tartalmat.
* A teljesítmény növelése érdekében. Ha több eszközön keresztül tölti be a kapcsolatokat, egyetlen eszköznek nem kell kezelnie az összes feldolgozást. Ehelyett a tartalom kiszolgálásához szükséges feldolgozási és memóriaigények több eszközön is elterjednek.

### <a name="http-based-load-balancing"></a>HTTP-alapú terheléselosztás

A webalapú szolgáltatásokat futtató szervezetek gyakran vágynak egy HTTP-alapú terheléselosztóra a webes szolgáltatások előtt. Ez segít biztosítani a megfelelő szintű teljesítményt és a magas rendelkezésre állást. A hagyományos, hálózatalapú terheléselosztók hálózati és átviteli rétegprotokollokra támaszkodnak. A HTTP-alapú terheléselosztók viszont a HTTP-protokoll jellemzői alapján hoznak döntéseket.

Az Azure Application Gateway HTTP-alapú terheléselosztást biztosít a webalapú szolgáltatásokhoz. Az Application Gateway támogatja:

* Cookie-alapú munkamenet-affinitás. Ez a funkció biztosítja, hogy a terheléselosztó mögött ikiszolgálók egyikéhez létesített kapcsolatok érintetlenek maradnak az ügyfél és a kiszolgáló között. Ez biztosítja a tranzakciók stabilitását.
* SSL tehermentesítés. Amikor egy ügyfél csatlakozik a terheléselosztóhoz, a munkamenet a HTTPS (SSL) protokoll használatával lesz titkosítva. A teljesítmény növelése érdekében azonban a HTTP (titkosítatlan) protokoll segítségével csatlakozhat a terheléselosztó és a terheléselosztó mögötti webkiszolgáló között. Ezt "SSL-kiszervezésnek" nevezzük, mivel a terheléselosztó mögött álló webkiszolgálók nem tapasztalják a titkosítással járó processzorterhelést. A webkiszolgálók ezért gyorsabban tudják kiszolgálni a kérelmeket.
* URL-alapú tartalom-útválasztás. Ez a funkció lehetővé teszi, hogy a terheléselosztó döntsön arról, hogy hol továbbítsa a kapcsolatokat a cél URL-cím alapján. Ez sokkal nagyobb rugalmasságot biztosít, mint azok a megoldások, amelyek az IP-címek alapján hozzák meg a terheléselosztási döntéseket.

További információ:

* [Az Application Gateway áttekintése](/azure/application-gateway/application-gateway-introduction)

### <a name="network-level-load-balancing"></a>Hálózati szintű terheléselosztás

A HTTP-alapú terheléselosztással ellentétben a hálózati szintű terheléselosztás az IP-cím és a port (TCP vagy UDP) számok alapján hoz döntéseket.
Az Azure Load Balancer használatával kihasználhatja a hálózati szintű terheléselosztás előnyeit az Azure-ban. A terheléselosztó néhány fő jellemzője:

* Hálózati szintű terheléselosztás az IP-cím és a portszámok alapján.
* Bármely alkalmazásréteg-protokoll támogatása.
* Terheléselosztásaz Azure virtuális gépek és a felhőszolgáltatások szerepkör-példányok.
* Használható internetre néző (külső terheléselosztás) és nem internetfelé néző (belső terheléselosztási) alkalmazásokhoz és virtuális gépekhez.
* Végpontfigyelés, amely annak megállapítására, hogy a terheléselosztó mögött i szolgáltatások bármelyike elérhetetlenné vált-e.

További információ:

* [Internetfelé néző terheléselosztó több virtuális gép vagy szolgáltatás között](/azure/load-balancer/load-balancer-internet-overview)
* [Belső terheléselosztó áttekintése](/azure/load-balancer/load-balancer-internal-overview)

### <a name="global-load-balancing"></a>Globális terheléselosztás

Egyes szervezetek a lehető legmagasabb szintű rendelkezésre állást szeretnék. Ennek a célnak az egyik módja az alkalmazások globálisan elosztott adatközpontokban való üzemeltetése. Ha egy alkalmazás üzemelteti adatközpontok található szerte a világon, lehetséges, hogy egy teljes geopolitikai régió elérhetetlenné válik, és továbbra is az alkalmazás, és fut.

Ez a terheléselosztási stratégia teljesítménybeli előnyökkel is járhat. A szolgáltatás kéréseit a kérést végző eszközhöz legközelebb eső adatközpontba irányíthatja.

Az Azure-ban az Azure Traffic Manager használatával élvezheti a globális terheléselosztás előnyeit.

További információ:

* [A Traffic Manager ismertetése](../../traffic-manager/traffic-manager-overview.md)

## <a name="name-resolution"></a>Névfeloldás

A névfeloldás az Azure-ban üzemeltetett összes szolgáltatás kritikus funkciója. Biztonsági szempontból a névfeloldási funkció feltörése azt eredményezheti, hogy a támadó a webhelyről érkező kéréseket a támadó webhelyére irányítja át. A biztonságos névfeloldás az összes felhőalapú üzemeltetett szolgáltatásra vonatkozó követelmény.

A névfeloldásnak két típusa van:

* Belső névfeloldás. Ezt a virtuális hálózatokon, a helyszíni hálózatokon vagy mindkettőn lévő szolgáltatások használják. A belső névfeloldáshoz használt nevek nem érhetők el az interneten keresztül. Az optimális biztonság érdekében fontos, hogy a belső névfeloldási séma nem érhető el a külső felhasználók számára.
* Külső névfeloldás. Ezt a helyszíni hálózatokon és virtuális hálózatokon kívüli személyek és eszközök használják. Ezek azok a nevek, amelyek láthatók az interneten, és a felhőalapú szolgáltatásokhoz való közvetlen csatlakozásra szolgálnak.

A belső névfeloldáshoz két lehetőség közül választhat:

* Virtuális hálózati DNS-kiszolgáló. Új virtuális hálózat létrehozásakor létrejön egy DNS-kiszolgáló. Ez a DNS-kiszolgáló fel tudja oldani a virtuális hálózaton található gépek nevét. Ez a DNS-kiszolgáló nem konfigurálható, az Azure fabric manager kezeli, és ezért segíthet a névfeloldási megoldás biztonságossá tétele.
* Hozza magával saját DNS-kiszolgálóját. Lehetősége van arra, hogy a virtuális hálózaton saját maga által választott DNS-kiszolgálót helyezzen el. Ez a DNS-kiszolgáló lehet egy Active Directoryintegrált DNS-kiszolgáló, vagy egy Azure-partner által biztosított dedikált DNS-kiszolgálómegoldás, amelyet az Azure Piactérről szerezhet be.

További információ:

* [Virtuális hálózat – áttekintés](../../virtual-network/virtual-networks-overview.md)
* [Virtuális hálózat által használt DNS-kiszolgálók kezelése](../../virtual-network/manage-virtual-network.md#change-dns-servers)

A külső névfeloldáshoz két lehetőség közül választhat:

* Saját külső DNS-kiszolgáló helyszíni üzemeltetése.
* Saját külső DNS-kiszolgáló tanonca legyen egy szolgáltatónál.

Számos nagy szervezet üzemelteti saját DNS-kiszolgálóit a helyszínen. Megtehetik ezt, mert rendelkeznek a hálózati szakértelemmel és a globális jelenléttel.

A legtöbb esetben jobb, ha a DNS-névfeloldási szolgáltatásokat egy szolgáltatónál üzemelteti. Ezek a szolgáltatók rendelkeznek a hálózati szakértelemmel és a globális jelenléttel, hogy nagyon magas rendelkezésre állást biztosítsanak a névfeloldási szolgáltatások számára. A DNS-szolgáltatások elérhetősége alapvető fontosságú, mivel ha a névfeloldási szolgáltatások nem sikerülnek, senki sem fogja tudni elérni az internettel kapcsolatos szolgáltatásokat.

Az Azure magas rendelkezésre állású és jól teljesítő külső DNS-megoldást biztosít az Azure DNS formájában. Ez a külső névfeloldási megoldás kihasználja a világméretű Azure DNS-infrastruktúrát. Lehetővé teszi, hogy a tartomány az Azure-ban, ugyanazokat a hitelesítő adatokat, API-kat, eszközöket és számlázást, mint a többi Azure-szolgáltatások. Az Azure részeként is örökli a platformba beépített erős biztonsági vezérlőket.

További információ:

* [Az Azure DNS áttekintése](../../dns/dns-overview.md)
* [Az Azure DNS-hálózati zónák](../../dns/private-dns-overview.md) lehetővé teszi, hogy az Automatikusan hozzárendelt nevek helyett az Automatikusan hozzárendelt neveket konfigurálja az Azure-erőforrásokhoz anélkül, hogy egyéni DNS-megoldást kellene hozzáadnia.

## <a name="perimeter-network-architecture"></a>Szegély hálózati architektúra

Sok nagy szervezet szegélyhálózatokat használ a hálózatok szegmentálására, és pufferzónát hoz létre az internet és a szolgáltatásaik között. A hálózat kerületi része alacsony biztonsági zónának minősül, és az adott hálózati szegmensben nem helyeznek nagy értékű eszközöket. Általában olyan hálózati biztonsági eszközök jelennek meg, amelyek hálózati csatolóval rendelkeznek a peremhálózati szegmensben. Egy másik hálózati adapter olyan hálózathoz csatlakozik, amely olyan virtuális gépekkel és szolgáltatásokkal rendelkezik, amelyek az internetről érkező kapcsolatokat fogadják.

A szegélyhálózatokat számos különböző módon tervezheti meg. A peremhálózat üzembe helyezésére vonatkozó döntés, majd az, hogy milyen típusú peremhálózatot használjon, ha úgy dönt, hogy használ egyet, a hálózati biztonsági követelményektől függ.

További információ:

* [Microsoft Felhőszolgáltatások és hálózatbiztonság](network-best-practices.md)

## <a name="azure-ddos-protection"></a>Azure DDoS Protection

Az elosztott szolgáltatásmegtagadásos (DDoS-) támadások az egyik legnagyobb rendelkezésreállási és biztonsági kockázatot jelentik az olyan felhasználók számára, akik alkalmazásaikat a felhőbe helyezik át. A DDoS-támadás megpróbálja kimeríteni az alkalmazás erőforrásait, így az alkalmazás nem érhető el a jogos felhasználók számára. A DDoS-támadások bármilyen, az interneten keresztül nyilvánosan elérhető végpontot megcélozhatnak.
A Microsoft az Azure Platform részeként **alapszintű** ddos-védelmet biztosít. Ez díjmentesen érkezik, és mindig magában foglalja a gyakori hálózati szintű támadások figyelését és valós idejű mérséklését. A DDoS protection **Basic** által biztosított védelem mellett engedélyezheti a **Standard** opciót is. DDoS Protection Standard funkciók a következők:

* **Natív platformintegráció:** Natívmódon integrálva az Azure-ba. Az Azure Portalon keresztüli konfigurációt tartalmazza. A DDoS Protection Standard tisztában van az erőforrásokkal és az erőforrás-konfigurációval.
* **Kulcsrakész védelem:** Az egyszerűsített konfiguráció azonnal védi a virtuális hálózaton lévő összes erőforrást, amint a DDoS Protection Standard engedélyezve van. Nincs szükség beavatkozásra vagy felhasználódefinícióra. DDoS Protection Standard azonnal és automatikusan csökkenti a támadást, ha észleli.
* **Mindig forgalomfigyelés:** Az alkalmazás forgalmi minták figyelik a nap 24 órájában, a hét minden napján, a DDoS-támadások mutatóit keresve. A védelmi házirendek túllépése esetén a kockázatcsökkentés történik.
* **Támadásmérséklési jelentések** A támadáscsökkentési jelentések összesített hálózati folyamatadatokat használnak az erőforrásokat célzó támadásokrészletes tájékoztatásához.
* **Támadásmérséklési folyamatnaplók** A támadáscsökkentési folyamatnaplók lehetővé teszik az eldobott forgalom, a továbbított forgalom és más támadási adatok közel valós idejű áttekintését egy aktív DDoS-támadás során.
* **Adaptív hangolás:** Az intelligens forgalmi profilkészítés idővel megtanulja az alkalmazás forgalmát, és kiválasztja és frissíti a szolgáltatásszámára legmegfelelőbb profilt. A profil a forgalom időbeli változásával változik. Réteg 3 réteg 7 védelem: Teljes verem DDoS védelmet biztosít, ha egy webalkalmazás tűzfal.
* **Kiterjedt mérséklési skála:** Több mint 60 különböző támadástípus mérsékelhető globális kapacitással a legnagyobb ismert DDoS-támadások elleni védelem érdekében.
* **Támadási mérőszámok:** Az egyes támadások összesített metrikái az Azure Monitoron keresztül érhetők el.
* **Támadásriasztás:** Riasztások konfigurálható a támadás kezdetén és leállításakor, és a támadás időtartama alatt, beépített támadási metrikák használatával. A riasztások integrálhatók az operatív szoftverekbe, például a Microsoft Azure Monitor naplóiba, a Splunkba, az Azure Storageba, az E-mailbe és az Azure Portalra.
* **Költséggarancia:**  Adattovábbítási és alkalmazás-kibővített szolgáltatás kreditek dokumentált DDoS-támadások.
* **DDoS Gyors válaszkészség** A DDoS Protection Standard ügyfelei mostantól egy aktív támadás során hozzáférhetnek a gyorsreagálású csapathoz. A DRR segíthet a támadások kivizsgálásában, az egyéni megoldások megoldásában a támadás és a támadás utáni elemzés során.


További információ:

* [DDOS védelem – áttekintés](../../virtual-network/ddos-protection-overview.md)

## <a name="azure-front-door"></a>Azure Front Door

Az Azure Bejárati ajtó szolgáltatás lehetővé teszi a webes forgalom globális útválasztásának meghatározását, kezelését és figyelését. Optimalizálja a forgalom útvonalát a legjobb teljesítmény és a magas rendelkezésre állás érdekében. Az Azure Front Doorral egyéni webalkalmazási tűzfalszabályok (WAF-szabályok) készítésével hozzáférés-vezérlést valósíthat meg, amellyel megvédheti a HTTP/HTTPS-számítási feladatait attól, hogy feltörjék őket az ügyféloldali IP-cím, országkód és HTTP-paraméterek alapján. Emellett a Bejárati ajtó lehetővé teszi a sebességkorlátozó szabályok létrehozását a rosszindulatú robotforgalom elleni küzdelemhez, magában foglalja az SSL-kiszervezést és a HTTP/HTTPS-kérelem, az alkalmazásréteg-feldolgozás.

Maga a Front Door platform alapszintű Azure DDoS Protectionnel rendelkezik. További védelemként engedélyezheti a virtuális hálózatain a Standard szintű Azure DDoS Protectiont, így automatikus finomhangolással és kárenyhítéssel megvédheti erőforrásait a hálózati rétegből érkező (TCP/UDP) támadásokkal szemben. Bejárati ajtó egy réteg 7 fordított proxy, ez csak lehetővé teszi a webes forgalom áthaladnak a back end szerverek és blokkolja más típusú forgalom alapértelmezés szerint.

További információ:

* Az Azure Bejárati ajtajának teljes készletéről további információt az [Azure Bejárati ajtajának áttekintése című témakörben talál.](../../frontdoor/front-door-overview.md)

## <a name="azure-traffic-manager"></a>Azure Traffic menedzser

Az Azure Traffic Manager egy DNS-alapú forgalom-terheléselosztó, amely lehetővé teszi a szolgáltatásokhoz érkező forgalom optimális elosztását a globális Azure-régiókban, miközben magas rendelkezésre állást és válaszkészséget biztosít. A Traffic Manager DNS használatával a leginkább megfelelő szolgáltatási végpontra irányítja az ügyfélkéréseket a forgalom-útválasztási módszer és a végpont állapota alapján. A végpont egy, az Azure-on kívül vagy belül üzemeltetett, internetkapcsolattal rendelkező szolgáltatás. A Traffic Manager figyeli a végpontokat, és nem irányítja a forgalmat a nem elérhető végpontokra.

További információ:

* [Az Azure Traffic manager áttekintése](../../traffic-manager/traffic-manager-overview.md)

## <a name="monitoring-and-threat-detection"></a>Figyelés és fenyegetésészlelés

Az Azure olyan lehetőségeket kínál, amelyekkel korai észlelés, figyelés, valamint a hálózati forgalom összegyűjtése és áttekintése segítséget nyújt ezen a kulcsfontosságú területen.

### <a name="azure-network-watcher"></a>Azure Network Watcher

Az Azure Network Watcher segítséget nyújt a hibaelhárításban, és egy teljesen új eszközkészletet biztosít a biztonsági problémák azonosításához.

[A Security Group View](../../network-watcher/network-watcher-security-group-view-overview.md) segít a virtuális gépek naplózásában és biztonsági megfelelőségében. Ezzel a funkcióval programozott naplózást hajthat végre, és összehasonlíthatja a szervezet által meghatározott alapházirendeket az egyes virtuális gépek hatékony szabályaival. Ez segíthet azonosítani a konfigurációs eltolódást.

[A csomagrögzítés](../../network-watcher/network-watcher-packet-capture-overview.md) lehetővé teszi a virtuális gépre irányuló és onnan érkező hálózati forgalom rögzítését. Hálózati statisztikákat gyűjthet, és elháríthatja az alkalmazásokkal kapcsolatos problémákat, amelyek felbecsülhetetlen értékűek lehetnek a hálózati behatolások vizsgálata során. Ezt a funkciót az Azure Functions-szel együtt is használhatja a hálózati rögzítések indításához adott Azure-riasztásokra adott válaszként.

A Network Watcher szolgáltatásról és a laborok egyes funkcióinak teszteléséről az [Azure network watcher monitoring áttekintése című](../../network-watcher/network-watcher-monitoring-overview.md)témakörben olvashat bővebben.

> [!NOTE]
> A szolgáltatás elérhetőségéről és állapotáról szóló legfrissebb értesítésekről az [Azure-frissítések lapon](https://azure.microsoft.com/updates/?product=network-watcher)találja.

### <a name="azure-security-center"></a>Azure Security Center

Az Azure Security Center segítségével megelőzheti, észlelheti és reagálhat a fenyegetésekre, és nagyobb betekintést nyújt az Azure-erőforrások biztonságába, és szabályozhatja azokat. Integrált biztonsági figyelést és szabályzatkezelést biztosít az Azure-előfizetésekben, segít észlelni az egyébként észrevétlenül megjelenő fenyegetéseket, és számos biztonsági megoldással működik.

A Security Center a következők segítségével segíti a hálózati biztonság optimalizálását és figyelését:

* Hálózati biztonsági javaslatok biztosítása.
* A hálózati biztonsági konfiguráció állapotának figyelése.
* Riasztás a hálózati fenyegetésekre, mind a végpont, mind a hálózat szintjén.

További információ:

* [Az Azure Security Center bemutatása](../../security-center/security-center-intro.md)

### <a name="virtual-network-tap"></a>Virtuális hálózati TAP

Az Azure virtuális hálózati TAP (Terminal Access Point) lehetővé teszi a virtuális gép hálózati forgalmának folyamatos streamelését egy hálózati csomaggyűjtő vagy -elemző eszköz felé. A gyűjtő vagy elemző eszközt egy hálózati virtuális berendezés partner biztosítja. Ugyanazt a virtuális hálózati TAP-erőforrást használhatja több hálózati csatolóból származó forgalom összesítésére ugyanabban vagy különböző előfizetésekben.

További információ:

* [Virtuális hálózat TAP-jai](../../virtual-network/virtual-network-tap-overview.md)

### <a name="logging"></a>Naplózás

A hálózati szintű naplózás minden hálózati biztonsági forgatókönyv kulcsfontosságú funkciója. Az Azure-ban naplózhatja az NSG-k beszerzett információkat a hálózati szintű naplózási információk beolvasásához. Az NSG naplózásával a következő információktól kaphat információkat:

* [Tevékenységnaplók](../../azure-monitor/platform/platform-logs-overview.md). Ezekkel a naplókkal megtekintheti az Azure-előfizetéseibe küldött összes műveletet. Ezek a naplók alapértelmezés szerint engedélyezve vannak, és az Azure Portalon belül használhatók. Ezeket korábban audit- vagy működési naplóknak nevezték.
* Eseménynaplók. Ezek a naplók információt nyújtanak az NSG-szabályok alkalmazásáról.
* Számláló naplók. Ezek a naplók tudatják, hogy az egyes NSG-szabályok hányszor voltak alkalmazva a forgalom megtagadására vagy engedélyezésére.

A naplók megtekintéséhez és elemzéséhez használhatja a [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/)hatékony adatvizualizációs eszközt is.
További információ:

* [Az Azure Monitor naplói hálózati biztonsági csoportokhoz (NSG-k)](../../virtual-network/virtual-network-nsg-manage-log.md)
