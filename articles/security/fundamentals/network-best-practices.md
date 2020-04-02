---
title: Gyakorlati tanácsok a hálózat biztonságával kapcsolatban – Microsoft Azure
description: Ez a cikk az Azure-képességek beépített használatával a hálózati biztonsággal kapcsolatos gyakorlati tanácsok készletét tartalmazza.
services: security
author: TerryLanfear
manager: barbkess
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2019
ms.author: TomSh
ms.openlocfilehash: 5e155758d19b45d977fcd087bff0ceb85898f8f8
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548299"
---
# <a name="azure-best-practices-for-network-security"></a>Az Azure biztonsági tanácsai a hálózat biztonsága terén
Ez a cikk ismerteti az Azure ajánlott eljárások gyűjteménye a hálózati biztonság növelése érdekében. Ezek az ajánlott eljárások az Azure-hálózatokkal kapcsolatos tapasztalatainkból és az önhöz hasonló ügyfelek tapasztalataiból származnak.

A cikk az egyes ajánlott eljárásokhoz a következőket ismerteti:

* Mi a legjobb gyakorlat?
* Miért szeretné engedélyezni ezt a bevált gyakorlatot?
* Mi lehet az eredmény, ha nem engedélyezi a legjobb gyakorlatot?
* A legjobb gyakorlat lehetséges alternatívái
* Hogyan tanulhatja meg a legjobb gyakorlat engedélyezését?

Ezek az ajánlott eljárások konszenzusos véleményen alapulnak, és az Azure platform képességei és funkciókészletei, acikk írásának időpontjában. A vélemények és a technológiák idővel változnak, és ezt a cikket rendszeresen frissítik, hogy tükrözzék ezeket a változásokat.

## <a name="use-strong-network-controls"></a>Erős hálózati vezérlők használata
[Az Azure virtuális gépeit (VM-eket)](https://azure.microsoft.com/services/virtual-machines/) és készülékeit más hálózati eszközökhöz is csatlakoztathatja, ha [azokat az Azure virtuális hálózatára](../../virtual-network/index.yml)helyezi. Ez azt illeti, virtuális hálózati csatolókártyákat csatlakoztathat egy virtuális hálózathoz, hogy tcp/IP-alapú kommunikációt engedélyezhessen a hálózati eszközök között. Az Azure virtuális hálózathoz csatlakoztatott virtuális gépek ugyanazon virtuális hálózaton, különböző virtuális hálózatokon, az interneten vagy a saját helyszíni hálózatokon lévő eszközökhöz csatlakozhatnak.

A hálózat és a hálózat biztonságának megtervezésekor azt javasoljuk, hogy központosítsa a következőket:

- A törzshálózati funkciók, például az ExpressRoute, a virtuális hálózat és az alhálózat kiépítése, valamint az IP-címzés kezelése.
- A hálózati biztonsági elemek, például a hálózati virtuális berendezés funkcióinak, például az ExpressRoute, a virtuális hálózat és az alhálózat kiépítésének, valamint az IP-címzés szabályozása.

Ha közös felügyeleti eszközöket használ a hálózat és a hálózat biztonságának figyelésére, mindkettőt egyértelműen láthatja. Az egyszerű, egységes biztonsági stratégia csökkenti a hibákat, mivel növeli az emberi megértést és az automatizálás megbízhatóságát.

## <a name="logically-segment-subnets"></a>Logikailag szegmens alhálózatok
Az Azure virtuális hálózatok hasonlóak a helyi hálózatok a helyszíni hálózaton. Az Azure virtuális hálózat mögött megrejlő ötlet az, hogy egyetlen privát IP-címtér alapján hozzon létre egy hálózatot, amelyre az összes Azure virtuális gépét elhelyezheti. A rendelkezésre álló privát IP-címterek az A osztályú (10.0.0.0/8), a B osztályú (172.16.0.0/12) és a C osztály (192.168.0.0/16) tartományba tartoznak.

Az alhálózatok logikai szegmentálásával kapcsolatos gyakorlati tanácsok a következők:

**Ajánlott eljárás:** Ne rendeljen lehetővé széles tartományú szabályokat (például engedélyezze a 0.0.0.0 és 255.255.255.255).  
**Részletes :** Győződjön meg arról, hogy a hibaelhárítási eljárások nem akadályozzák vagy tiltják az ilyen típusú szabályok beállítását. Ezek lehetővé teszik a szabályok hamis biztonságérzetet eredményeznek, és a vörös csapatok gyakran megtalálják és kihasználják őket.

**Ajánlott eljárás**: A nagyobb címteret alhálózatokra szegmentálják.   
**Részlet:** A [CIDR-alapú](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)alhálózati alapelvek használatával hozza létre az alhálózatokat.

**Ajánlott eljárás:** Hálózati hozzáférés-vezérléslétrehozása az alhálózatok között. Az alhálózatok közötti útválasztás automatikusan történik, és nem kell manuálisan konfigurálnia az útválasztási táblákat. Alapértelmezés szerint nincsenek hálózati hozzáférés-vezérlések az Azure virtuális hálózaton létrehozott alhálózatok között.   
**Részlet:** Hálózati [biztonsági csoport](/azure/virtual-network/virtual-networks-nsg) használatával védheti az Azure-alhálózatokkba irányuló kéretlen forgalmat. A hálózati biztonsági csoportok egyszerű, állapotalapú csomagvizsgáló eszközök, amelyek az 5-t, a forrás IP-címét, a forrásportját, a cél IP-címét, a célport és a 4. Engedélyezi vagy letilthatja a forgalmat egyetlen IP-címre, több IP-címre és onnan, illetve teljes alhálózatokba és onnan.

Ha hálózati biztonsági csoportokat használ az alhálózatok közötti hálózati hozzáférés-vezérléshez, az azonos biztonsági zónához vagy szerepkörhöz tartozó erőforrásokat a saját alhálózataikba helyezheti.

**Ajánlott eljárás:** Az egyszerűség és a rugalmasság biztosítása érdekében kerülje a kis virtuális hálózatokat és alhálózatokat.   
**Részletes :** A legtöbb szervezet az eredetileg tervezettnél több erőforrást ad hozzá, és a címek újraelosztása munkaigényes. A kis alhálózatok használata korlátozott biztonsági értéket ad hozzá, és a hálózati biztonsági csoport hozzárendelése az egyes alhálózatokhoz többletterhelést jelent. Határozza meg az alhálózatokat nagyjából, hogy rugalmasan növekedjen.

**Gyakorlati tanácsok**: Egyszerűsítse a hálózati biztonsági csoport szabálykezelését [az alkalmazásbiztonsági csoportok](https://azure.microsoft.com/blog/applicationsecuritygroups/)meghatározásával.  
**Részletes :** Adjon meg egy alkalmazásbiztonsági csoportot olyan IP-címek listáihoz, amelyekről úgy gondolja, hogy a jövőben változhatnak, vagy számos hálózati biztonsági csoportban használhatók. Ügyeljen arra, hogy az alkalmazásbiztonsági csoportokat egyértelműen nevezze el, hogy mások is megértsék azok tartalmát és célját.

## <a name="adopt-a-zero-trust-approach"></a>Zéró megbízhatósági megközelítés elfogadása
A kerületen alapuló hálózatok abból a feltételezésből indulnak ki, hogy a hálózaton belül minden rendszer megbízható. De a mai alkalmazottak bárhonnan hozzáférhetnek a szervezet erőforrásaihoz különböző eszközökön és alkalmazásokon, ami lényegtelenné teszi a kerületbiztonsági vezérlőket. Nem elegendőek azok a hozzáférés-vezérlési házirendek, amelyek csak arra összpontosítanak, hogy ki férhet hozzá egy erőforráshoz. A biztonság és a termelékenység közötti egyensúly elsajátításához a biztonsági rendszergazdáknak is figyelembe kell vennie az erőforrás elérésének *módját.*

A hálózatoknak a hagyományos védelemből kell fejlődniük, mert a hálózatok ki vannak téve a szabálysértéseknek: a támadó kithat egy végpontot a megbízható határon belül, majd gyorsan kibővítheti a lábát a teljes hálózaton. [A Zero Trust](https://www.microsoft.com/security/blog/2018/06/14/building-zero-trust-networks-with-microsoft-365/) hálózatok kiküszöbölik a megbízhatóság fogalmát a hálózaton belüli hely alapján. Ehelyett a Nulla megbízhatósági architektúrák eszköz- és felhasználói megbízhatósági jogcímeket használnak a szervezeti adatokhoz és erőforrásokhoz való hozzáférés lefedéséhez. Új kezdeményezések esetén fogadja el a Zéró megbízhatósági megközelítéseket, amelyek a hozzáférés időpontjában ellenőrzik a bizalmat.

A legjobb gyakorlatok a következők:

**Ajánlott eljárás:** Feltételes hozzáférés biztosítása az erőforrásokhoz az eszköz, az identitás, a biztosítás, a hálózati hely és egyebek alapján.  
**Részletes:** [Az Azure AD feltételes hozzáférés](/azure/active-directory/conditional-access/overview) lehetővé teszi a megfelelő hozzáférés-vezérlések alkalmazását a szükséges feltételek alapján automatikus hozzáférés-vezérlési döntések megvalósításával. További információ: [Access to Azure Management with Conditional Access](../../role-based-access-control/conditional-access-azure-management.md).

**Ajánlott eljárás:** A porthoz való hozzáférés engedélyezése csak a munkafolyamat jóváhagyása után.  
**Részletes:** Az [Azure Security Centerben a just-in-time virtuális gép-hozzáférés](../../security-center/security-center-just-in-time.md) használatával zárolhatja az Azure-beli virtuális gépek bejövő forgalmát, csökkentve a támadásoknak való kitettséget, miközben szükség esetén könnyű hozzáférést biztosít a virtuális gépekhez való csatlakozáshoz.

**Ajánlott eljárás:** Ideiglenes engedélyeket adhat a kiemelt feladatok végrehajtására, ami megakadályozza, hogy a rosszindulatú vagy jogosulatlan felhasználók hozzáférjenek az engedélyek lejárta után. A hozzáférés csak akkor érhető el, ha a felhasználóknak szükségük van rá.  
**Részlet:** Használja a just-in-time hozzáférést az Azure AD kiemelt identitáskezelés vagy egy külső megoldás, hogy engedélyeket adjon a kiemelt feladatok végrehajtására.

A Zero Trust a hálózati biztonság következő fejleménye. A kibertámadások állapota arra ösztönzi a szervezeteket, hogy a "feltételezik megsértése" gondolkodásmódot, de ez a megközelítés nem korlátozza. A Zero Trust hálózatok védik a vállalati adatokat és erőforrásokat, miközben biztosítják, hogy a szervezetek modern munkahelyeket építhessenek olyan technológiák használatával, amelyek lehetővé teszik az alkalmazottak számára, hogy bárhol, bárhol, bármilyen módon termelékenyek legyenek.

## <a name="control-routing-behavior"></a>Útválasztási viselkedés szabályozása
Amikor egy virtuális gépet egy Azure virtuális hálózaton helyez el, a virtuális gép csatlakozhat bármely más virtuális géphez ugyanazon a virtuális hálózaton, még akkor is, ha a többi virtuális gép különböző alhálózatokon található. Ez azért lehetséges, mert az alapértelmezés szerint engedélyezett rendszerútvonalak gyűjteménye lehetővé teszi az ilyen típusú kommunikációt. Ezek az alapértelmezett útvonalak lehetővé teszik, hogy az ugyanazon a virtuális hálózaton lévő virtuális gépek kapcsolatot kezdeményezhessenek egymással és az internettel (csak az internethez való kimenő kommunikáció esetén).

Bár az alapértelmezett rendszerútvonalak számos telepítési forgatókönyv esetén hasznosak, vannak esetek, amikor testre szeretné szabni a központi telepítések útválasztási konfigurációját. Beállíthatja, hogy a következő ugrási cím elérjen bizonyos célokat.

Azt javasoljuk, hogy [konfigurálja a felhasználó által definiált útvonalakat,](../../virtual-network/virtual-networks-udr-overview.md) amikor egy biztonsági berendezést telepít egy virtuális hálózathoz. Erről egy későbbi, a [kritikus Azure-szolgáltatási erőforrásokat csak a virtuális hálózatoknak szóló](network-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks)szakaszban beszélünk.

> [!NOTE]
> A felhasználó által definiált útvonalak nem szükségesek, és az alapértelmezett rendszerútvonalak általában működnek.
>
>

## <a name="use-virtual-network-appliances"></a>Virtuális hálózati készülékek használata
A hálózati biztonsági csoportok és a felhasználó által definiált útválasztás bizonyos mértékű hálózati biztonságot nyújthat az OSI modell hálózati és átviteli [rétegein.](https://en.wikipedia.org/wiki/OSI_model) Bizonyos helyzetekben azonban a verem magas szintjén szeretné vagy kell engedélyeznie a biztonságot. Ilyen esetekben azt javasoljuk, hogy az Azure-partnerek által biztosított virtuális hálózati biztonsági berendezéseket telepítsen.

Az Azure hálózati biztonsági berendezései nagyobb biztonságot nyújtanak, mint amit a hálózati szintű vezérlők nyújtanak. A virtuális hálózati biztonsági berendezések hálózati biztonsági képességei a következők:


* Tűzfalak
* Behatolásészlelés/behatolásmegelőzések megelőzése
* A biztonsági rés kezelése
* Alkalmazás-vezérlés
* Hálózatalapú anomáliadetektálás
* Webszűrés
* Vírusvédelem
* Botnet védelem

Az elérhető Azure virtuális hálózati biztonsági készülékek megkereséséhez keresse meg az [Azure Piactéren](https://azure.microsoft.com/marketplace/) a "biztonság" és a "hálózati biztonság" kifejezést.

## <a name="deploy-perimeter-networks-for-security-zones"></a>Szegélyhálózatok telepítése biztonsági zónákhoz
A [peremhálózat](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) (más néven DMZ) egy fizikai vagy logikai hálózati szegmens, amely további biztonsági réteget biztosít az eszközök és az internet között. A peremhálózat szélén lévő speciális hálózati hozzáférés-vezérlő eszközök csak a kívánt forgalmat engedélyezik a virtuális hálózatba.

A peremhálózatok azért hasznosak, mert a hálózati hozzáférés-vezérlés kezelésére, figyelésére, naplózására és jelentésére összpontosíthat az Azure virtuális hálózat szélén lévő eszközökön. A peremhálózat az a hely, ahol általában engedélyezi az elosztott szolgáltatásmegtagadást (DDoS) a megelőzést, a behatolásészlelési/behatolás-megelőzési rendszereket (IDS/IPS), a tűzfalszabályokat és -házirendeket, a webszűrést, a hálózati kártevőirtót stb. A hálózati biztonsági eszközök az internet és az Azure virtuális hálózata között ülnek, és mindkét hálózaton rendelkeznek egy felülettel.

Bár ez az alapvető design a peremhálózat, sok különböző minták, mint a back-to-back, három-homed, és több-homed.

A korábban említett nulla megbízhatósági koncepció alapján azt javasoljuk, hogy fontolja meg egy szegélyhálózat használatát az összes magas biztonsági szintű telepítéshez a hálózati biztonság és az Azure-erőforrások hozzáférés-vezérlési szintjének növelése érdekében. Az Azure vagy egy külső megoldás segítségével további biztonsági réteget biztosíthat az eszközök és az internet között:

- Az Azure natív vezérlői. [Az Azure Firewall](/azure/firewall/overview) és az [Application Gateway webalkalmazás-tűzfala](../../application-gateway/features.md#web-application-firewall) alapvető biztonságot nyújt egy teljesen állapotalapú tűzfallal, amely beépített magas rendelkezésre állás, korlátlan felhőméretezhetőség, Teljes tartománynhálózat-szűrés, Az OWASP-alapszabálykészletek támogatása, valamint az egyszerű beállítás és konfiguráció.
- Harmadik fél ajánlatai. Keressen az [Azure Piactéren](https://azuremarketplace.microsoft.com/) új generációs tűzfalat (NGFW) és más, harmadik féltől származó ajánlatokat, amelyek ismerős biztonsági eszközöket és jelentősen megnövelt hálózati biztonsági szinteket biztosítanak. A konfiguráció összetettebb lehet, de egy harmadik féltől származó ajánlat lehetővé teheti a meglévő képességek és képességek használatát.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Kerülje az internetnek való kitettséget dedikált WAN-kapcsolatokkal
Számos szervezet választotta a hibrid informatikai útvonalat. A hibrid it-szolgáltatással a vállalat egyes információs eszközei az Azure-ban találhatók, míg mások a helyszínen maradnak. Sok esetben egy szolgáltatás egyes összetevői futnak az Azure-ban, míg más összetevők továbbra is a helyszínen.

Hibrid informatikai forgatókönyv esetén általában van valamilyen telephelyközi kapcsolat. A telephelyek közötti kapcsolat lehetővé teszi a vállalat számára, hogy helyszíni hálózatait az Azure virtuális hálózataihoz csatlakoztassa. Két, több helyiséget is igénybe vehető kapcsolati megoldás áll rendelkezésre:

* [Helyek közötti VPN](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md). Ez egy megbízható, megbízható és megalapozott technológia, de a kapcsolat az interneten keresztül történik. A sávszélesség legfeljebb 1,25 Gb/s-ra van korlátozva. A helyek közötti VPN bizonyos esetekben kívánatos lehetőség.
* **Az Azure ExpressRoute**. Azt javasoljuk, hogy [használja ExpressRoute](../../expressroute/expressroute-introduction.md) a létesítmények közötti kapcsolat. Az ExpressRoute használatával kiterjesztheti helyszíni hálózatait a Microsoft Cloudba egy privát kapcsolaton keresztül, amelyet egy kapcsolatszolgáltató biztosít. Az ExpressRoute segítségével kapcsolatokat létesíthet a Microsoft felhőszolgáltatásaival, például az Azure-ral, az Office 365-tel és a Dynamics 365-tel. Az ExpressRoute egy dedikált WAN-kapcsolat a helyszíni hely vagy a Microsoft Exchange tárhelyszolgáltatója között. Mivel ez egy telco kapcsolat, az adatok nem utaznak az interneten keresztül, így nincs kitéve az internetes kommunikáció lehetséges kockázatainak.

Az ExpressRoute-kapcsolat helye hatással lehet a tűzfal kapacitására, a méretezhetőségre, a megbízhatóságra és a hálózati forgalom láthatóságára. Meg kell határoznia, hogy hol kell leállítania az ExpressRoute-ot a meglévő (helyszíni) hálózatokban. A következőket teheti:

- A tűzfalon kívül (a peremhálózati paradigma) leállítása, ha rá látásra van szüksége a forgalomba, ha folytatnia kell az adatközpontok elkülönítésére vonatkozó meglévő gyakorlatot, vagy ha kizárólag extranetes erőforrásokat helyez el az Azure-ban.
- A tűzfalon belüli leáll (a hálózati bővítmény paradigmája). Ez az alapértelmezett javaslat. Minden más esetben azt javasoljuk, hogy az Azure-t n-edik adatközpontként kezelje.

## <a name="optimize-uptime-and-performance"></a>Az állásidő és a teljesítmény optimalizálása
Ha egy szolgáltatás nem működik, az adatok nem érhetők el. Ha a teljesítmény olyan gyenge, hogy az adatok használhatatlanok, az adatokelérhetetlennek tekinthetők. Biztonsági szempontból mindent meg kell tennie, hogy a szolgáltatások optimális anamnézisben és teljesítményben legyenek.

A rendelkezésre állás és a teljesítmény növelésének népszerű és hatékony módja a terheléselosztás. A terheléselosztás a hálózati forgalom szolgáltatás részét vevő kiszolgálók közötti elosztásának módszere. Ha például a szolgáltatás részeként előtér-webkiszolgálókkal rendelkezik, a terheléselosztás segítségével eloszthatja a forgalmat a több előtér-webkiszolgáló között.

A forgalom elosztása növeli a rendelkezésre állást, mert ha az egyik webkiszolgáló elérhetetlenné válik, a terheléselosztó leállítja a forgalom küldését a kiszolgálóra, és átirányítja azokra a kiszolgálókra, amelyek még mindig online állapotban vannak. A terheléselosztás is segíti a teljesítményt, mivel a processzor, a hálózat és a kérelmek kiszolgálására vonatkozó memóriaterhelés az összes terheléselosztási kiszolgáló között elvan osztva.

Azt javasoljuk, hogy a terheléselosztást, amikor csak lehet, és a szolgáltatásoknak megfelelően alkalmazza. A következőkben forgatókönyvek mind az Azure virtuális hálózat szintjén, mind a globális szinten, valamint a terheléselosztási lehetőségek minden.

**Eset**: Olyan alkalmazással rendelkezik, amely:

- Ugyanazon felhasználói/ügyfélmunkamenetből érkező kérelmeket igényel ugyanattól a háttérrendszerbeli virtuális géphez. Ilyenek például a bevásárlókosár-alkalmazások és a weblevelezési kiszolgálók.
- Csak biztonságos kapcsolatot fogad el, így a kiszolgálóval való titkosítatlan kommunikáció nem elfogadható.
- Ugyanazon a hosszú ideig futó TCP-kapcsolaton több HTTP-kérelmet igényel, vagy a terheléselosztást különböző háttérkiszolgálókra kell irányítani.

**Terheléselosztási lehetőség:** Használja [az Azure Application Gateway](/azure/application-gateway/application-gateway-introduction)http-webforgalom-terheléselosztót. Az Application Gateway támogatja a végpontok közötti TLS-titkosítást és a [TLS-végződést](/azure/application-gateway/application-gateway-introduction) az átjárón. A webkiszolgálók ezután megszabadulhatnak a titkosítástól és a visszafejtési terheléstől, valamint a háttérkiszolgálókra titkosítatlanul áramló forgalomtól.

**Forgatókönyv:** Be kell töltenie az internetről érkező bejövő kapcsolatokat az Azure virtuális hálózatban található kiszolgálók között. A forgatókönyvek a következők:

- Állapotnélküli alkalmazásokkal rendelkezik, amelyek elfogadják az internetről érkező bejövő kérelmeket.
- Nem igényel ragadós ülések vagy TLS tehermentesítés. A sticky munkamenetek az alkalmazás terheléselosztásával a kiszolgáló-affinitás elérésére használt módszer.

**Terheléselosztási lehetőség:** Az Azure Portal használatával [hozzon létre egy külső terheléselosztót,](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) amely a bejövő kérelmeket több virtuális gépközött osztja el a magasabb szintű rendelkezésre állás biztosítása érdekében.

**Forgatókönyv:** Terheléselosztási kapcsolatokat kell betöltenie olyan virtuális gépekről, amelyek nem az interneten találhatók. A legtöbb esetben a terheléselosztásra elfogadott kapcsolatokat egy Azure virtuális hálózaton lévő eszközök, például az SQL Server-példányok vagy a belső webkiszolgálók kezdeményezik.   
**Terheléselosztási lehetőség:** Az Azure Portal használatával [hozzon létre egy belső terheléselosztót,](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) amely a bejövő kérelmeket több virtuális gépközött osztja el a magasabb szintű rendelkezésre állás biztosítása érdekében.

**Eset**: Globális terheléselosztásra van szükség, mert:

- Rendelkezik egy felhőalapú megoldás, amely széles körben több régióban, és megköveteli a lehető legmagasabb szintű rendelkezésre állási (rendelkezésre állási) lehetséges.
- A lehető legmagasabb szintű rendelkezésre állásra van szüksége annak érdekében, hogy a szolgáltatás akkor is elérhető legyen, ha egy teljes adatközpont elérhetetlenné válik.

**Terheléselosztási lehetőség:** Használja az Azure Traffic Manager. A Traffic Manager lehetővé teszi a szolgáltatásokhoz való terheléselosztási kapcsolatok terhelését a felhasználó helye alapján.

Ha például a felhasználó az EU-ból kér az Ön szolgáltatásához, a kapcsolat egy EU-s adatközpontban található szolgáltatásokhoz lesz irányítva. A Traffic Manager globális terheléselosztás ezen része javítja a teljesítményt, mivel a legközelebbi adatközponthoz való csatlakozás gyorsabb, mint a távoli adatközpontokhoz való csatlakozás.

## <a name="disable-rdpssh-access-to-virtual-machines"></a>RdP/SSH hozzáférés letiltása virtuális gépekhez
Az Azure virtuális gépei elérhetők a [Távoli asztali protokoll](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) és a Secure [Shell](https://en.wikipedia.org/wiki/Secure_Shell) (SSH) protokoll használatával. Ezek a protokollok lehetővé teszik a virtuális gépek távoli felügyeletét, és mindennaposak az adatközpontokban használt számítástechnikában.

A lehetséges biztonsági probléma ezekkel a protokollokkal az interneten keresztül, hogy a támadók használhatják [brute force](https://en.wikipedia.org/wiki/Brute-force_attack) technikák hozzáférni az Azure virtuális gépek. Miután a támadó megszerezte a hozzáférést, a virtuális gépet kiindulópontként használva a virtuális hálózat más gépeit is feltörheti, és akár az Azure-on kívüli, hálózatra kötött eszközöket is megtámadhatja.

Azt javasoljuk, hogy tiltsa le a közvetlen RDP és SSH hozzáférést az Azure virtuális gépek az internetről. Miután az internetről való közvetlen RDP- és SSH-hozzáférés le van tiltva, más lehetőségek is elérhetők a virtuális gépek távoli kezeléshez.

**Forgatókönyv:** Egyetlen felhasználó csatlakozhat egy Azure virtuális hálózathoz az interneten keresztül.   
**Option**: [A pont-hely VPN](/azure/vpn-gateway/vpn-gateway-point-to-site-create) a távelérésű VPN-ügyfél-kiszolgáló kapcsolat másik kifejezése. A pont-hely kapcsolat létrejötte után a felhasználó rdp vagy SSH használatával csatlakozhat az Azure virtuális hálózaton található virtuális gépekhez, amelyekhez a felhasználó pont-hely VPN-en keresztül csatlakozik. Ez feltételezi, hogy a felhasználó jogosult elérni ezeket a virtuális gépeket.

A pont-hely VPN biztonságosabb, mint a közvetlen RDP- vagy SSH-kapcsolatok, mivel a felhasználónak kétszer kell hitelesítenie magát, mielőtt virtuális géphez csatlakozna. Először a felhasználónak hitelesítenie kell (és engedélyeznie kell) a pont-hely VPN-kapcsolat létrehozásához. Másodszor, a felhasználónak hitelesítenie kell (és engedélyeznie kell) az RDP vagy Az SSH munkamenet létrehozásához.

**Forgatókönyv:** Engedélyezze a helyszíni hálózat felhasználóinak, hogy az Azure virtuális hálózaton lévő virtuális gépekhez csatlakozzanak.   
**Beállítás:** A [helyek közötti VPN](/azure/vpn-gateway/vpn-gateway-site-to-site-create) a teljes hálózatot egy másik hálózathoz csatlakoztatja az interneten keresztül. A helyek közötti VPN segítségével csatlakoztathatja a helyszíni hálózatot egy Azure virtuális hálózathoz. A helyszíni hálózat felhasználói az RDP vagy SSH protokoll használatával csatlakoznak a helyek közötti VPN-kapcsolaton keresztül. Nem kell közvetlen RDP- vagy SSH-hozzáférést engedélyeznie az interneten keresztül.

**Eset:** Használjon dedikált WAN-kapcsolatot a helyek közötti VPN-hez hasonló funkciók biztosításához.   
**Option**: Használja [az ExpressRoute programot](https://azure.microsoft.com/documentation/services/expressroute/). A helyek közötti VPN-hez hasonló funkciókat biztosít. A legfontosabb különbségek a következők:

- A dedikált WAN-kapcsolat nem halad át az interneten.
- A dedikált WAN-kapcsolatok általában stabilabbak és jobban teljesítenek.

## <a name="secure-your-critical-azure-service-resources-to-only-your-virtual-networks"></a>Biztosítsa kritikus Azure-szolgáltatási erőforrásait csak a virtuális hálózatokon
A virtuális hálózati szolgáltatás végpontjaival kiterjesztheti a virtuális hálózat magáncímterét és a virtuális hálózat identitását az Azure-szolgáltatásokra, közvetlen kapcsolaton keresztül. A végpontok segítségével biztosíthatja, hogy kritikus fontosságú Azure-szolgáltatási erőforrásai csak a virtuális hálózatain legyenek elérhetőek. A virtuális hálózatról az Azure-szolgáltatásra irányuló forgalom mindig a Microsoft Azure gerinchálózatán marad.

A szolgáltatásvégpontok az alábbi előnyöket nyújtják:

- **Nagyobb biztonság az Azure-szolgáltatások erőforrásai számára**: A szolgáltatásvégpontokkal az Azure-szolgáltatások erőforrásai leköthetőek a virtuális hálózathoz. A szolgáltatás-erőforrások virtuális hálózathoz való kötése nagyobb biztonságot eredményez, mivel így az erőforrások egyáltalán nem lesznek elérhetők a nyilvános internetről, és csak a virtuális hálózatból érkező forgalom lesz engedélyezett.
- **Optimális útválasztás az Azure szolgáltatásforgalmára a virtuális hálózatról:** A virtuális hálózat minden olyan útvonala, amely kényszeríti az internetes forgalmat a helyszíni és/vagy virtuális készülékekre, az úgynevezett kényszerített bújtatás, az Azure szolgáltatásforgalmát is arra kényszeríti, hogy ugyanazt az útvonalat válassza, mint az internetes forgalmat. A szolgáltatásvégpontok optimális útválasztást biztosítanak az Azure-forgalom számára.

  A végpontok mindig közvetlenül a virtuális hálózatról a szolgáltatás az Azure gerinchálózaton szolgáltatás forgalmat. Az Azure gerinchálózatának forgalom ának megtartása lehetővé teszi a virtuális hálózatokkimenő internetes forgalmának naplózását és figyelését a kényszerített bújtatás révén, a szolgáltatásforgalom befolyásolása nélkül. További információ a [felhasználó által definiált útvonalakról és a kényszerített bújtatásról.](../../virtual-network/virtual-networks-udr-overview.md)

- **Egyszerűen állítható be kevesebb felügyeleti többletterheléssel:** A virtuális hálózatokban már nincs szükség fenntartott, nyilvános IP-címekre az Azure-erőforrások IP-tűzfalon keresztültörténő védelméhez. A szolgáltatásvégpontok beállításához nincs szükség NAT- és útválasztó eszközökre. A szolgáltatásvégpontok egy egyszerű kattintással konfigurálhatóak az alhálózatokon. A végpontok karbantartásához nincs további többletterhelés.

Ha többet szeretne megtudni a szolgáltatásvégpontokról, valamint az Azure-szolgáltatásokról és -régiókról, amelyekhez szolgáltatásvégpontok érhetők el, olvassa el a Virtuális hálózati szolgáltatás végpontjai című [témakört.](../../virtual-network/virtual-network-service-endpoints-overview.md)

## <a name="next-steps"></a>További lépések
Tekintse meg [az Azure biztonsági gyakorlati tanácsait és mintáit,](best-practices-and-patterns.md) amelyek további biztonsági gyakorlati tanácsokat tartalmaznak a felhőbeli megoldások tervezése, üzembe helyezése és kezelése során az Azure használatával.
