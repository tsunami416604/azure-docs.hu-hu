---
title: Azure-hálózat ajánlott biztonsági eljárások |} Microsoft Docs
description: Ez a cikk ajánlott eljárások a hálózati biztonság Azure-képességek a beépített biztosít.
services: security
documentationcenter: na
author: TomShinder
manager: mbaldwin
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 5ebeadd9c0805ac5f6ac543a49cb9ff63d8ded3f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="azure-network-security-best-practices"></a>Azure-hálózat ajánlott biztonsági eljárások
A Microsoft Azure lehetővé teszi, hogy csatlakozhat a virtuális gépek és készülékek más hálózati eszközök párt Azure virtuális hálózatot. Egy Azure virtuális hálózatra olyan konstrukció, amely lehetővé teszi a virtuális hálózati kártyák engedélyezett hálózati eszközök közötti TCP/IP-alapú kommunikáció engedélyezése a virtuális hálózathoz csatlakozni. Egy Azure virtuális hálózatra csatlakozik az Azure virtuális gépek a azonos Azure virtuális hálózaton, különböző Azure virtuális hálózatok, az interneten, vagy még akkor is, a saját helyszíni hálózatokon is elérheti.

A cikk ismerteti az Azure-hálózat ajánlott biztonsági eljárások gyűjteménye. Az alábbi gyakorlati tanácsok az Azure hálózati tapasztalatunk származik, és az ügyfelek feladatokat, például saját maga.

Minden egyes ajánlott eljárás Ez a cikk ismerteti:

* Mi az az ajánlott eljárás szerint
* Miért érdemes, hogy a legjobb engedélyezése
* Milyen lehet az eredmény, ha nem engedélyezi az ajánlott eljárás szerint
* Az ajánlott eljárás szerint lehetséges alternatívák
* Hogyan megismerheti az ajánlott eljárás engedélyezése

Azure hálózati biztonság ajánlott eljárások a cikkben az együttműködési véleményét, és az Azure platform olyan képességeit és a szolgáltatáskészletek, alapján ez a cikk írásának időpontjában léteznek. Vélemények és technológiák változnak az idők, és ez a cikk a változások követése érdekében rendszeresen frissül.

Az Azure hálózati ajánlott biztonsági eljárások cikkben említett a következők:

* Logikailag szegmens alhálózatok
* Útválasztási viselkedés szabályozása
* A kényszerített bújtatás engedélyezése
* Virtuális hálózati készülékek használata
* Biztonsági zónázást DMZ-k telepítése
* Dedikált WAN-kapcsolatok az interneten való kitettség elkerülése érdekében
* Hasznos üzemidő és a teljesítmény optimalizálása
* Használja a globális terheléselosztás
* Az Azure virtuális gépek RDP elérésének letiltása
* Engedélyezze az Azure Security Center
* Az Azure az Adatközpont kiterjesztése

## <a name="logically-segment-subnets"></a>Logikailag szegmens alhálózatok
[Egy Azure virtuális hálózatot](https://azure.microsoft.com/documentation/services/virtual-network/) hasonlóak a helyi hálózaton egy LAN-on. A mögött egy Azure virtuális hálózatra lényege, hogy hozzon létre egy egyetlen magán IP-cím terület-alapú hálózati amelyen minden elhelyezheti a [Azure virtuális gépek](https://azure.microsoft.com/services/virtual-machines/). A privát IP-címterek érhető el az osztály A (10.0.0.0/8), a B osztály (172.16.0.0/12), és C osztályú (192.168.0.0/16) címtartományok.

Hasonló ahhoz, ami így tesz, a helyszíni, meg kell szegmenseket, nagyobb címtartomány alhálózatokra. Használhat [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) alapú alhálózatok alapelvek alhálózatát létrehozásához.

Útválasztás alhálózatok között művelet automatikusan megtörténik, és nem kell manuálisan konfigurálnia az útválasztási táblázatokat. Az alapértelmezett beállítás azonban, hogy vannak-e nincs hálózati hozzáférés-vezérlést az Azure virtuális hálózat létrehozása az alhálózatok között. Hálózati hozzáférés-vezérlést alhálózatok között létrehozásához meg kell importálnia valamit a alhálózatok között.

Ennek a feladatnak segítségével dolog van egy [hálózati biztonsági csoport](../virtual-network/security-overview.md) (NSG). Az NSG-k a 5 rekordos (a forrás IP-címe, forrásport, cél IP-címe, célport és 4. rétegbeli protocol) használó egyszerű csomag ellenőrző eszközök a hálózati forgalom szabályok megközelítés engedélyezése vagy megtagadása létrehozásához. Akkor engedélyezheti vagy tagadhatja meg egyetlen IP-címet, a és több IP-címekről vagy akár a és a teljes alhálózat érkező vagy oda irányuló forgalmat.

Hálózati hozzáférés-vezérlési alhálózatok között NSG-k használata lehetővé teszi a ugyanazt biztonsági zóna vagy a saját alhálózatokon szerepkör tartozó erőforrásaikat. Például egy egyszerű 3 szintű alkalmazás egy webes réteghez, egy alkalmazás logikai rétegből és adatbázis-rétegből gondol. Ezek a rétegek mindegyikének saját alhálózatokra tartozó virtuális gépek állapotba. Majd segítségével NSG-ket az alhálózatok közötti forgalmat:

* Webes réteg virtuális gépein csak az alkalmazás logika gépek kapcsolatot kezdeményezhetnek, és az internetről érkező kapcsolatokat törléshez
* Alkalmazás logika virtuális gépek csak kezdeményezhet az adatbázis-rétegből kapcsolatokhoz, és a webes réteg kapcsolódásának törléshez
* Adatbázis réteg virtuális gépein bármi kívül a saját alhálózati kapcsolat nem indítható el, és az alkalmazás logikai rétegből kapcsolódásának törléshez

Hálózati biztonsági csoportok és hogyan használhatja az Azure virtuális hálózat logikailag szegmentálja kapcsolatos további információkért lásd: [Mi az a hálózati biztonsági csoport](../virtual-network/security-overview.md) (NSG).

## <a name="control-routing-behavior"></a>Útválasztási viselkedés szabályozása
Ha egy virtuális gép elhelyezése egy Azure virtuális hálózatra, láthatja, hogy a virtuális gép kapcsolódni tud-e az azonos Azure virtuális hálózat bármely más virtuális gép akkor is, ha más virtuális gépeket különböző alhálózatokon. Ez akkor lehetséges, mert nincs a rendszerútvonalak, amely alapértelmezés szerint engedélyezve vannak, amelyek lehetővé teszik az ilyen típusú kommunikáció gyűjteménye. Ezek az alapértelmezett útvonalak Azure virtuális hálózaton vannak egymással, és az internetes (a kimenő kommunikáció csak az internethez) kapcsolatok kezdeményezésének engedélyezése a virtuális gépek.

Az alapértelmezett rendszerútvonalak számos telepítési forgatókönyv hasznosak lehetnek, amíg vannak olyan helyzetek, ha azt szeretné testre szabhatja a központi telepítések útválasztási beállításait. Ezek a testreszabások lehetővé teszi meghatározott célok eléréséhez a következő ugrási cím konfigurálásához.

Azt javasoljuk, hogy konfigurált felhasználói útvonalak egy virtuális hálózati biztonsági készülék, amelyről a későbbi célszerű központi telepítésekor.

> [!NOTE]
> felhasználó által megadott útvonal nem szükségesek, és az alapértelmezett rendszerútvonalak működik a legtöbb esetben.
>
>

További információ a felhasználó által definiált útvonalak és hogyan lehet konfigurálni azokat a cikk elolvasása [Mik azok a felhasználó által megadott útvonalak és IP-továbbítás](../virtual-network/virtual-networks-udr-overview.md).

## <a name="enable-forced-tunneling"></a>A kényszerített bújtatás engedélyezése
Jobb megértése érdekében a kényszerített bújtatás, akkor hasznos, ha szeretné megtudni, milyen "felosztása tunneling"-e.
A leggyakoribb példa vegyes alagútkezelést a VPN-kapcsolatok látható. Tegyük fel, hogy Ön a VPN-kapcsolatot a a szállodák hely a vállalati hálózathoz. Ez a kapcsolat lehetővé teszi, hogy a vállalati erőforrások eléréséhez és a hálózati kommunikáció minden esetben nyissa meg a VPN-alagúton keresztül.

Mi történik, ha azt szeretné, az interneten lévő erőforrások eléréséhez? Vegyes Alagútkezelés engedélyezése esetén ezeket a kapcsolatokat lépjen közvetlenül az internethez, és nem a VPN-alagúton keresztül. Néhány biztonsági szakértők fontolja meg, ez kockázatot, és ezért javasoljuk, hogy Alagútkezelés le van tiltva, és az összes kapcsolat. Az internethez és a vállalati erőforrások szánt kapcsolatok szánt kapcsolatok kell nyissa meg a VPN-alagúton keresztül. Ennek előnye, hogy csatlakozik az internethez kényszerítve vannak-e majd a biztonsági nem tudnák akkor fordulhat elő, ha a VPN-ügyfél csatlakozik az internethez, a VPN-alagút kívül vállalati hálózati eszközön.

Most tegyük érdekében ismét ebben az Azure virtuális hálózat virtuális gépei számára. Az alapértelmezett útvonalak egy Azure virtuális hálózat virtuális gépek kezdeményezheti az internetre irányuló forgalom engedélyezése. Ez túl jelenthet biztonsági kockázatot jelent, ezeket a kimenő kapcsolatokat nő a támadási felületet, a virtuális gépek és a támadók javítható.
Ezért azt javasoljuk, hogy kényszerített bújtatást a virtuális gépeken Ha közötti kapcsolatot nyújthassanak az Azure virtuális hálózat és a helyszíni hálózat között engedélyezze. Helyi közötti kapcsolat van később dokumentumban tárgyalt Azure hálózati ajánlott eljárásokat.

Ha még nem rendelkezik a helyszínek közötti kapcsolat, ellenőrizze, hogy használatakor élvezheti a (korábban tárgyalt) hálózati biztonsági csoportok vagy Azure virtuális hálózat biztonsági készülékek (tárgyaltuk mellett) kívánja tagadni a kimenő kapcsolatokat az interneten az Azure virtuális gépek.

Kényszerített bújtatás és az engedélyezéshez kapcsolatos további információkért lásd: [PowerShell és az Azure Resource Manager használatával konfigurálja a kényszerített bújtatás](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md).

## <a name="use-virtual-network-appliances"></a>Virtuális hálózati készülékek használata
Amíg a hálózati biztonsági csoportok és felhasználói útválasztási biztosít egy bizonyos szintű hálózati biztonság, a hálózati és a szállítási réteg a [OSI-modell](https://en.wikipedia.org/wiki/OSI_model), létezhetnek lesz szeretné vagy engedélyezni kell lennie a verem magas szintű biztonsági. Ilyen helyzetekben azt javasoljuk, hogy telepít virtuális hálózati biztonsági készülékek Azure partnerei által biztosított.

Azure hálózati biztonsági készülékek által biztosított fokozott biztonsági szintek mi van hálózati szintű vezérlők által szolgáltatott keresztül. A virtuális hálózati biztonsági készülékek által biztosított hálózati biztonsági képességei többek között:

* Firewalling
* Behatolás-észlelés/behatolás megelőzése
* A biztonsági rés kezelése
* Alkalmazás-vezérlő
* Hálózatalapú anomáliadetektálás
* Webes szűrése
* A víruskereső
* Botnet védelme

Ha szüksége van egy magasabb szintű hálózati biztonság szerezheti be a hálózati szintű hozzáférés-vezérlést, majd azt javasoljuk, hogy vizsgálja meg, és központi telepítése az Azure-beli virtuális hálózat biztonsági készülékek.

Megtudhatja, milyen az Azure virtuális hálózattal kapcsolatos biztonsági készülékek érhetők el, és azok képességeinek kapcsolatban keresse fel a [Azure piactér](https://azure.microsoft.com/marketplace/) , és keressen a "biztonság" és "hálózati biztonság."

## <a name="deploy-dmzs-for-security-zoning"></a>Biztonsági zónázást DMZ-k telepítése
A Szegélyhálózaton, vagy "szegélyhálózaton" fizikai vagy logikai hálózati szegmens, amely az eszközök és az Internet között biztonsági további réteget biztosít. A Szegélyhálózaton célja, hogy csak a kívánt forgalom engedélyezve van, a hálózati biztonsági eszköz túli és az Azure virtuális hálózatra helyezze a DMZ hálózat peremén speciális hálózati hozzáférés-vezérlő eszközök.

DMZ-k hasznosak, mert a hálózati hozzáférés-vezérlési felügyeletet, összpontosíthat, naplózást, a figyelési és jelentéskészítési az eszközöket az Azure virtuális hálózat peremén. Itt volna általában engedélyeznie DDoS megelőzése, behatolás-észlelés/behatolás megelőzési rendszerek (Azonosítók/IP-CÍMEK), a tűzfalszabályok és házirendek, webes szűrési, hálózati kártevőirtó és több. A hálózati biztonsági eszközök az interneten és az Azure virtuális hálózat között elhelyezkedő, és mindkét hálózat illesztőfelület.

Ez nem egy Szegélyhálózaton az alapvető tervezési, nincsenek számos különböző DMZ kialakításokról, például a végpontok közötti, hármas többcímes, többhelyű, stb.

Ajánlott az összes kiemelt biztonságú környezetek, fontolja meg, hogy a hálózat az Azure-erőforrások biztonsági szint emelése DMZ telepítése.

DMZ-k és azokat az Azure-ban telepítésével kapcsolatos további információkért lásd: [Microsoft más Felhőszolgáltatásaival és a hálózati biztonsági](../best-practices-network-security.md).

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Dedikált WAN-kapcsolatok az interneten való kitettség elkerülése érdekében
Számos szervezet a hibrid informatikai útvonal választotta. A hibrid informatikai a vállalat információs eszközeinek némelyike az Azure, míg mások továbbra is a helyszíni. Sok esetben,, a közben egyéb összetevők továbbra is a helyszíni az Azure-ban egy szolgáltatás egyes összetevőinek fog futni.

A hibrid informatikai forgatókönyvet, az általában van valamilyen közötti kapcsolatot nyújthassanak. A létesítmények közötti kapcsolat lehetővé teszi a vállalat a helyszíni hálózatokon Azure virtuális hálózatokhoz való kapcsolódásának. Nincsenek elérhető két létesítmények közötti csatlakozási megoldásokat biztosítják:

* Telephelyek közötti VPN
* ExpressRoute

[Telephelyek közötti VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) a helyszíni hálózat és az Azure virtuális hálózat közötti virtuális magánhálózati kapcsolat jelöli. Ezt a kapcsolatot az interneten keresztül történik, és lehetővé teszi a "tunnel" információ belül a hálózati és az Azure közötti egy titkosított kapcsolat. Telephelyek közötti VPN egy biztonságos, érett technológia, amely különböző méretű vállalatok által évtizedeken van telepítve. Bújtatás titkosítás használatával történik [IPsec-bújtatásos mód](https://technet.microsoft.com/library/cc786385.aspx).

A megbízható, megbízható és a meghatározott technológiája pedig a telephelyek közötti VPN a belül a alagút forgalmi haladnak át az interneten. Ezenkívül sávszélesség viszonylag korlátozódik legfeljebb körülbelül 200 MB/s.

A létesítmények közötti kapcsolatokat. szükséges egy rendkívüli szintű biztonsági vagy a teljesítmény, azt javasoljuk, hogy a létesítmények közötti kapcsolatot használjon Azure ExpressRoute. ExpressRoute dedikált WAN a helyszíni hely vagy egy Exchange-szolgáltató közötti kapcsolat. Mivel ez egy telco kapcsolat, a az adatokat az interneten keresztül nem utazik, és ezért nem érhető el az internetes adatátvitel járó esetleges kockázatokat.

Azure ExpressRoute működésével és telepítésével kapcsolatos további információkért lásd: [ExpressRoute műszaki áttekintés](../expressroute/expressroute-introduction.md).

## <a name="optimize-uptime-and-performance"></a>Hasznos üzemidő és a teljesítmény optimalizálása
Titkosítás, integritás és rendelkezésre állás (CIA) tartalmazza az aktuális legtöbb segíti elő biztonsági modell háromrészes. Bizalmas titkosításával kapcsolatos és adatvédelmi, integritás tárgya annak biztosítása, hogy jogosulatlan személyek nem módosítja a adatok, és rendelkezésre állási kapcsolatos biztosítja, hogy a felhatalmazott személyek legyenek jogosultak hozzáférni a hozzáférhet. Hiba történt a ezek a területek közül bármelyik egy történő lehetséges behatolást biztonsági jelöli.

Rendelkezésre állási elképzelhető, hogy hasznos üzemidő és teljesítményére. Ha a szolgáltatás nem működik, információ nem érhető el. Ha a teljesítmény, gyenge, hogy emiatt használhatatlanná válik az adatokat, akkor azt is fontolóra veheti az adatok nem érhetők el. Ezért biztonsági szempontból, igazolnia kell tennie függetlenül azt is győződjön meg róla szolgáltatások optimális hasznos üzemidő és teljesítmény.
Egy népszerű és hatékony módszer használt rendelkezésre állásának és teljesítményének javítása érdekében, hogy használja a terheléselosztás. Terheléselosztás a szolgáltatás részét képező kiszolgálók közötti hálózati forgalom elosztása módszer. Például ha a szolgáltatás részeként előtér-webkiszolgálók, segítségével terheléselosztás a forgalom terjesztheti a több előtér-webkiszolgáló között.

A forgalom eloszlása növeli a rendelkezésre állási, mivel a webkiszolgálók egyik elérhetetlenné válik, a terheléselosztó leállítja a forgalmat küld erre a kiszolgálóra, majd irányítja át a kiszolgálók, amelyek továbbra is működik. Terheléselosztás lehetővé teszi a teljesítményt, mert a processzor, a hálózati és a memória terhelést jelenthet küldött kérelmek is terjeszthető a terhelés eloszlik a kiszolgálók.

Azt javasoljuk, hogy alkalmaz terheléselosztás együtt, és a szolgáltatások megfelelő. Oldjuk lesz a következő szakaszokban indították: az Azure-beli virtuális hálózat szinten Azure biztosít a három elsődleges betölti terheléselosztási beállításokat:

* HTTP-alapú terheléselosztás
* Külső terheléselosztási
* Belső terheléselosztás

## <a name="http-based-load-balancing"></a>HTTP-alapú terheléselosztás
HTTP-alapú terheléselosztás döntéseket hozzanak arról, hogy melyik kiszolgáló küldhet-jellemzői a HTTP protokollt használó kapcsolatok alapjait. Azure rendelkezik egy HTTP terheléselosztót, ez az Alkalmazásátjáró nevével.

Azt javasoljuk, hogy használja-e Azure Application Gateway során:

* Olyan alkalmazásokhoz, amelyekben egy adott ügyfél- vagy felhasználói munkamenettől érkező kérésnek mindig ugyanarra a virtuális háttérgépre kell eljutnia. Erre példák volna vásárlás, bevásárlókocsiból alkalmazások és a webes levelezési kiszolgálóján.
* Alkalmazások, hogy felszabadítja-webfarmok kiszolgáló SSL-lezárást a terhelés, ha kihasználja a Application Gateway [SSL kiszervezési](https://f5.com/glossary/ssl-offloading) szolgáltatás.
* Olyan alkalmazásokhoz, például a tartalomkézbesítési hálózatokhoz, amelyeken egy hosszú ideig futó TCP-kapcsolat HTTP-kéréseit különböző háttérkiszolgálókra kell irányítani, terheléselosztását azokon elvégezni.

Azure Application Gateway működése, és hogyan használhatja a központi telepítés kapcsolatos további információkért lásd: [átjáró – áttekintés](../application-gateway/application-gateway-introduction.md).

## <a name="external-load-balancing"></a>Külső terheléselosztási
Külső terheléselosztás kerül sor, ha az internetről bejövő kapcsolatok a egy Azure virtuális hálózaton található kiszolgálók között elosztott terhelésű. Az Azure külső terheléselosztó adja meg ezt a lehetőséget, és azt javasoljuk, hogy használja, amikor nem feltétlenül szükséges a kapcsolódó munkamenetek vagy SSL-kiszervezés.

Ellentétben a HTTP-alapú terheléselosztást, a külső terheléselosztó adatokat használja, a hálózati és átviteli rétegek, a hálózati OSI-modell egyenleg kapcsolat betöltése milyen kiszolgálón vonatkozó döntések meghozatalában.

Azt javasoljuk, hogy használjon külső terheléselosztás Ha [állapot nélküli alkalmazások](http://whatis.techtarget.com/definition/stateless-app) fogadja az internetről bejövő kérelmeit.

További információt hogyan az Azure külső terheléselosztó működik, és hogyan telepítheti azt, lásd: [létrehozásához egy Internet Facing terheléselosztót a Resource Manager PowerShell-lel](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="internal-load-balancing"></a>Belső terheléselosztás
Belső terheléselosztás külső terheléselosztás hasonló, és ugyanazt a mechanizmust használ mögött azokat a kiszolgálókat egyenleg kapcsolatok betöltéséhez. Az egyetlen különbség, hogy a terheléselosztó ebben az esetben fogadja a kapcsolatokat a virtuális gépekről, amelyek nem kapcsolódnak az internethez. A legtöbb esetben az elfogadás terheléselosztás kapcsolatokat egy Azure virtuális hálózatán lévő eszközök által kezdeményezett.

Azt javasoljuk, hogy használja-e ezt a képességet, például amikor be kell tölteni az egyenleg kapcsolatok SQL webkiszolgálókon vagy belső előnyeit kihasználó forgatókönyvek belső terheléselosztást.

Azure belső terheléselosztás működése, és hogyan telepítheti azt kapcsolatos további információkért lásd: [létrehozásához egy PowerShell-lel belső terheléselosztót](../load-balancer/load-balancer-get-started-ilb-arm-ps.md).

## <a name="use-global-load-balancing"></a>Használja a globális terheléselosztás
Nyilvános felhő számítási teszi lehetőség az telepítése globálisan elosztott az alkalmazásokat, amelyek található, amelynek világszerte adatközpontok összetevőket. Ez akkor lehetséges, a Microsoft Azure Azure globális adatközpont miatt. Ellentétben a terheléselosztási a korábban említett technológiákkal globális terheléselosztási lehetővé teszi elérhetővé tenni a szolgáltatások a akkor is, ha a teljes adatközpontok válhatnak nem érhető el.

Az ilyen típusú globális terheléselosztásról az Azure kihasználásával kaphat [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/). A TRAFFIC Manager összekapcsolásával lehetőség a szolgáltatásokhoz, a felhasználó helye alapján egyenleg kapcsolatok betöltése.

Például ha a felhasználó, hogy így egy kérelem a szolgáltatás az Európai Unióból, a kapcsolat irányul, a szolgáltatások egy EU-adatközpontban található. Ebben a részben a Traffic Manager globális terheléselosztási segítséget nyújt a teljesítmény javítása, mert gyorsabb, mint az adatközpontokban, amelyek távolságra való kapcsolódás a legközelebbi adatközpont való kapcsolódás nem tölthető be.

A rendelkezésre állási oldalon globális terheléselosztás lehetővé teszi, hogy a szolgáltatás elérhető legyen-e akkor is, ha az egész adatközpont kell válnia nem érhető el.

Például egy Azure-adatközpontban kell válnia környezeti okok miatt, vagy valamilyen okból kimaradás (például a regionális hálózati hibák) miatt nem érhető el, ha a szolgáltatáshoz való csatlakozásokat kellene lennie átirányítása megtörténik a legközelebbi online datacenter. A globális terheléselosztás úgy érhető el, amelyet létrehozhat Traffic Manager DNS-házirendek előnyeit.

Azt javasoljuk, hogy használja-e a Traffic Manager minden fejleszt felhőalapú megoldás, amely széles körben elosztott hatókörrel rendelkezik a különféle régiókban, és a legmagasabb lehetséges hasznos üzemidő igényel.

Azure Traffic Manager és a telepítés módját kapcsolatos további információkért lásd: [Mi az a Traffic Manager](../traffic-manager/traffic-manager-overview.md).

## <a name="disable-rdpssh-access-to-azure-virtual-machines"></a>Tiltsa le az RDP/SSH hozzáférést az Azure virtuális gépek
Azure virtuális gépek eléréséhez lehet a [a távoli asztal protokoll](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) és a [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) (SSH) protokoll. Ezeket a protokollokat könnyebben kezelhetik a virtuális gépek távoli helyekről és standard, datacenter számítástechnikai.

A potenciális biztonsági problémát, amelynek ezeket a protokollokat használ az interneten keresztül, mert a támadók használhatják különböző [találgatásos](https://en.wikipedia.org/wiki/Brute-force_attack) technikák hozzáférést az Azure virtuális gépeken. Miután a támadók hozzáférést szereznek, kiindulási pontot használják a virtuális gép az Azure virtuális hálózaton lévő más gépekkel veszélyeztetése, vagy még akkor is a hálózati eszközök Azure-on kívüli támadás.

Ebből kifolyólag javasoljuk, hogy tiltsa le az RDP és SSH közvetlen hozzáférést az Azure virtuális gépek számára az internetről. Az internetről érkező RDP és SSH közvetlen hozzáférés le van tiltva, után ezek távoli kezelésére szolgál a virtuális gépek eléréséhez használható egyéb beállításokat:

* Pont – hely típusú VPN
* Telephelyek közötti VPN
* ExpressRoute

[Pont – hely típusú VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md) a távelérési VPN-ügyfél/kiszolgáló kapcsolatot egy másik kifejezés. A pont-pont VPN lehetővé teszi, hogy egy felhasználó az interneten keresztül egy Azure virtuális hálózathoz való kapcsolódáshoz. A pont – hely kapcsolat létrejötte után a felhasználó fog tudni RDP és az SSH használatával csatlakozhat a pont-pont VPN-en keresztül kapcsolódik a felhasználó Azure virtuális hálózaton található virtuális gép. A parancs feltételezi, hogy a felhasználó jogosult-e azon virtuális gépek eléréséhez.

Pont – hely típusú VPN, annál biztonságosabb közvetlen RDP és az SSH-kapcsolatok, mert a felhasználó rendelkezik-e a virtuális géphez való csatlakozás előtt kétszer hitelesíteni. Első lépésként a felhasználónak kell hitelesíteni (és engedélyezni kell) a pont-pont; VPN-kapcsolat létrehozása második, a felhasználónak kell engedélyezni (és hitelesítéséhez) hozzon létre az RDP és az SSH-munkamenetet.

A [telephelyek közötti VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) teljes hálózathoz kapcsolódik egy másik hálózati az interneten keresztül. Használhatja a telephelyek közötti VPN a helyszíni hálózat egy Azure virtuális hálózathoz való kapcsolódáshoz. Ha a telephelyek közötti VPN, a felhasználók telepít a helyszíni hálózat fog tudni csatlakozni a virtuális gépek az Azure virtuális hálózat a pont-pont VPN-kapcsolaton keresztül az RDP és az SSH protokoll használatával, és nincs szüksége a közvetlen RDP és az SSH-hozzáférés engedélyezése az interneten keresztül.

Egy dedikált WAN-kapcsolat segítségével a telephelyek közötti VPN hasonló funkciókat biztosítanak. A fő különbség a rendszer az 1. a dedikált WAN-kapcsolat nem haladnak át, az internethez, és a 2. dedikált WAN-kapcsolatok jellemzően további stabil és performant. Azure formájában dedikált WAN-kapcsolaton megoldást kínál, [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="enable-azure-security-center"></a>Engedélyezze az Azure Security Center
Az Azure Security Center segít a megakadályozása, észlelésében és kezelésében fenyegetések, és biztosítja, hogy lássák, és szabályozhatják, az Azure-erőforrások biztonságának nőtt. Az ügyfél összes előfizetésére kiterjedő, integrált biztonsági monitorozást és szabályzatkezelést biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását, és számos biztonsági megoldással együttműködik.

Azure Security Center segítségével optimalizálása és figyelheti a hálózati biztonsági megoldást:

* Hálózati biztonsági javaslatokat biztosít
* A hálózati biztonsági konfiguráció állapotának figyelése
* Hálózatalapú fenyegetések mindkét végpont és a hálózati szintű küld figyelmeztetést

Erősen ajánlott, hogy engedélyezi-e az Azure Security Center az összes Azure-környezetekhez.

Az Azure Security Center és a központi telepítések engedélyezése az kapcsolatos további információkért lásd: [Azure Security Center bemutatása](../security-center/security-center-intro.md).

## <a name="securely-extend-your-datacenter-into-azure"></a>Az Adatközpont biztonságosan kiterjeszti Azure
Sok vállalati informatikai szervezetek szeretnék kibővítheti helyett inkább a helyszíni adatközpontját a felhőre. A bővítés meglévő informatikai infrastruktúra kiterjesztése a nyilvános felhőre jelöli. Ha kihasználja a létesítmények közötti kapcsolati lehetőségek, akkor lehet kezelje az Azure virtuális hálózat egy másik alhálózat a helyszíni hálózati infrastruktúrát.

Vannak azonban tervezése és kialakítása során felmerülő kérdések először figyelembe venni. Ez különösen fontos a hálózati biztonság területén. Legoptimálisabb megérteni, hogyan készíthető elő ilyen tervezési egyik talál példákat.

A Microsoft hozott létre a [Datacenter bővítmény referencia architektúra diagramja](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84#content) és az azt támogató biztosíték megismerheti, mi néz ki egy ilyen adatközpont kiterjesztése. Ez lehetővé teszi egy megvalósítási példát hivatkozás szemléltet, amelyekkel tervezése és kialakítása egy biztonságos vállalati adatközpont kiterjesztése a felhőbe. Azt javasoljuk, hogy tekintse át a jelen dokumentum képet kapjon a a legfontosabb összetevők biztonságos megoldás.

Az Adatközpont biztonságosan kiterjeszti Azure kapcsolatos további tudnivalókért tekintse meg a videót [Your Datacenter kiterjesztése a Microsoft Azure](https://www.youtube.com/watch?v=Th1oQQCb2KA).
