---
title: Azure-beli hálózati biztonsági eljárások |} A Microsoft Docs
description: Bemutatjuk a segítségével biztonságos hálózati környezetek létrehozásához Azure-ban elérhető legfontosabb funkcióit
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: d169387a-1243-4867-a602-01d6f2d8a2a1
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: 74a46c7788b0a0dc729ab977489ed6d97a387a6e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60619535"
---
# <a name="microsoft-cloud-services-and-network-security"></a>A Microsoft cloud services és a hálózati biztonság
A Microsoft felhőalapú szolgáltatások rendkívül nagy kapacitású szolgáltatások és az infrastruktúra, a vállalati szintű képességet és a számos választási való hibrid kapcsolathoz nyújthat. Ügyfelek eldönthetik, ezek az interneten keresztül, vagy az Azure expressroute használatával privát hálózati kapcsolatot biztosít a szolgáltatások eléréséhez. A Microsoft Azure platform lehetővé teszi, hogy az ügyfelek zökkenőmentesen infrastruktúrán kiterjesztheti a felhőbe, és több rétegből álló architektúrákat hozhat létre. Emellett harmadik felek engedélyezéséhez fejlett funkciókat kínál a biztonsági szolgáltatások és virtuális berendezések. Ez a tanulmány áttekintése biztonsági és architekturális expressroute-on keresztül érhető el a Microsoft cloud services használatával ügyfeleink megfontolandó problémákat. Emellett ismerteti biztonságosabb services létrehozása az Azure virtuális hálózatok.

## <a name="fast-start"></a>Gyors indítása
A következő logikai diagramot, hogy egy olyan példát, és az Azure platform elérhető számos biztonsági technikák irányíthatók. Gyors referenciaként a leginkább megfelel az Ön esetében példát talál. Kibontott magyarázatát továbbra is a tanulmány elolvasása.
[![0]][0]

[1. példa: Hozhat létre a szegélyhálózaton (más néven DMZ-t, vagy demilitarizált zónában vagy demilitarizált) hálózati biztonsági csoportokkal (NSG-k) alkalmazások védelme érdekében.](#example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs)</br>
[2. példa: Hozhat létre alkalmazásokat a tűzfallal és NSG-k védelme érdekében a szegélyhálózaton.](#example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs)</br>
[3. példa: Hozzon létre egy tűzfallal, felhasználó által megadott útvonal (UDR) és NSG-t a hálózatok védelme érdekében a szegélyhálózaton.](#example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg)</br>
[4. példa: Vegyen fel egy hibrid kapcsolatot site-to-site, virtuális berendezés virtuális magánhálózat (VPN).](#example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn)</br>
[5. példa: A site-to-site, az Azure VPN gateway a hibrid kapcsolat hozzáadása.](#example-5-add-a-hybrid-connection-with-a-site-to-site-azure-vpn-gateway)</br>
[6. példa: Az expressroute-tal a hibrid kapcsolat hozzáadása.](#example-6-add-a-hybrid-connection-with-expressroute)</br>
Példák a virtuális hálózatok, a magas rendelkezésre állás és a szolgáltatásláncolás közötti kapcsolatok hozzáadásával a következő néhány hónapban felveszi ezt a dokumentumot.

## <a name="microsoft-compliance-and-infrastructure-protection"></a>A Microsoft megfelelőségi és az infrastruktúra védelme
Megkönnyíti a szervezetek számára a gyűjtemény és az egyének adatok használatát szabályozó országos, regionális és iparág-specifikus követelményeknek megfelelő, a Microsoft kínál a több mint 40 minősítéseket és tanúsítványokat. A legátfogóbb szolgáltatás felhőszolgáltatók közül.

További információkért tekintse meg a megfelelőségi adatokat a [Microsoft Trust Center][TrustCenter].

A Microsoft rendelkezik egy átfogó megközelítés a felhő-infrastruktúra nagy kapacitású – globális szolgáltatások futtatásához szükséges védelme érdekében. A Microsoft felhőalapú infrastruktúra tartalmazza a hardverek, szoftverek, hálózatok, és a felügyeleti és a munkatársak, a fizikai adatközpontok mellett.

![2]

Ez a megközelítés az ügyfelek számára, hogy azok a Microsoft cloud services üzembe helyezése biztonságosabb alapjait ismerteti. A következő lépésre van az ügyfelek tervezését, és hozzon létre egy biztonsági architektúra, ezek a szolgáltatások védelme érdekében.

## <a name="traditional-security-architectures-and-perimeter-networks"></a>A hagyományos biztonsági architektúrák és szegélyhálózatok
Bár a Microsoft erősen fektet a felhőalapú infrastruktúra védelmét, ügyfelek is kell levédenie a felhőalapú szolgáltatások és -erőforráscsoportok. Egy többrétegű biztonsági megközelítés a legjobb védelmet biztosít. A szegélyhálózat-alapú hálózati biztonsági zónához védi a belső hálózati erőforrásokhoz a nem megbízható hálózaton. Szegélyhálózaton hivatkozik az élek vagy a hálózat részei, az Internet és a védett vállalati informatikai infrastruktúra között található.

A vállalati hálózatokban az alapvető infrastruktúra erősen dúsítjuk, a régebben a biztonsági eszközök több rétege. A határ az egyes rétegek eszközök és a házirend-kényszerítési pontok áll. Az egyes rétegek tartalmazhatják a következő hálózati biztonsági eszközök kombinációját: tűzfalak, szolgáltatásmegtagadásos (DoS) megelőzése, behatolásérzékelési vagy a védelmi rendszereket (IDS/IPS) és VPN-eszközök. A házirend betartatása tűzfalházirendeket, hozzáférés-vezérlési listák (ACL) vagy adott útválasztási formájában is igénybe vehet. Az első sor a hálózatban, közvetlenül a bejövő forgalom az internetről fogad védelmi vonalként ezek a mechanizmusok blokk támadások és kártékony forgalom további a jogos kérésének engedélyezése a hálózaton történő együttes. Ez a forgalom közvetlenül a szegélyhálózaton található erőforrások irányítja. Ennek az erőforrásnak majd "beszél" a hálózatban, a következő határt érvényesítéshez áthaladó mélyebb erőforrások első. A legkülső réteg hívják a peremhálózaton, mert ez a hálózat részét van közzétéve az interneten, általában valamilyen védelmi mindkét oldalon. Az alábbi ábra egy vállalati hálózaton, két biztonsági határokat egyetlen alhálózat szegélyhálózaton példán látható.

![3]

Nincsenek szegélyhálózaton megvalósításához használt számos architektúrában. Architektúrák is terjedhet egyszerű load balancer és a egy több alhálózattal szegélyhálózat változatos mechanizmusokkal, mindegyik kiválasztott határ blokkolja a forgalmat, és a mélyebb rétegeket, a vállalati hálózat védelme. A konkrét igényeinek a szervezet és a teljes szervezet kockázattűrési határát függ, hogyan épül fel a peremhálózaton.

Ügyfelek nyilvános felhők számítási feladataik át, mivel rendkívül fontos a hasonló funkciók támogatása a szegélyhálózat-alapú hálózati architektúra az Azure-ban, megfelelőségi és biztonsági követelmények teljesítéséhez. Ez a dokumentum útmutatásokat tartalmaz az ügyfelek hogyan lehet létrehozni egy biztonságos hálózati környezetben, az Azure-ban. A peremhálózaton összpontosít, de a hálózati biztonsági szempontból sok átfogó leírást is tartalmaz. Az alábbi kérdésekre adott vitafórum tájékoztatja:

* Milyen az Azure-ban szegélyhálózaton építhetők fel?
* Mik az Azure-funkciók rendelkezésre álló hozhat létre a peremhálózaton?
* Hogyan lehet védeni háttér-számítási feladatokhoz?
* Hogyan irányítható internetes kommunikáció a számítási feladatok Azure-ban?
* Hogyan a helyszíni hálózatok biztosítható az Azure-ban üzemelő példányok?
* Mikor ajánlott a natív Azure biztonsági funkciók használata és a külső berendezések vagy a szolgáltatások?

Az alábbi ábrán látható a különböző biztonsági rétegeket, az Azure biztosít az ügyfelek számára. Ezek a rétegek, mind a natív az Azure platformon, magát, és a felhasználó által meghatározott funkciókat:

![4]

Bejövő az internetről, Azure DDoS elleni Azure nagyméretű támadások elleni védelem segít. A következő réteg a felhasználó által meghatározott nyilvános IP-címek (végpontok), amelyek segítségével meghatározhatja, mely forgalom tevékenységeken keresztül a felhőalapú szolgáltatás, a virtuális hálózathoz. Natív Azure-beli virtuális hálózatok elkülönítését biztosítja a többi teljes elkülönítés és, hogy csak a forgalom, aki elérési utak és módszerek. Ezen elérési utakat és módszerek a következő réteg, ahol az NSG-k, az udr-t és a hálózati virtuális berendezések használható az alkalmazások központi telepítésének a védett hálózat védelme érdekében biztonsági határokat kíván létrehozni.

A következő szakaszban az Azure virtuális hálózatok áttekintése. Ezek a virtuális hálózatok ügyfelek által létrehozott, és mi az üzembe helyezett munkaterhelések csatlakoznak. Virtuális hálózatok hálózati biztonsági funkciókat meg kell határoznia egy szegélyhálózaton védelme az Azure-beli felhasználói telepítések alapját képezi.

## <a name="overview-of-azure-virtual-networks"></a>Azure virtuális hálózatok áttekintése
Internetes forgalom érheti el az Azure virtuális hálózatok, mielőtt van két biztonsági rétegeket, az Azure platform beépített szolgáltatásai:

1.    **A DDoS protection**: A DDoS protection, amely egy Azure fizikai hálózathoz, amely a nagy méretű internetes támadások ellen védi a az Azure platform magát. Ezeket a támadásokat kísérlet terhelje túl az internetes szolgáltatás több "bot"-csomópontok használhatók. Az Azure DDoS protection robusztus rácsvonal be az összes bejövő kimenő és több Azure-régió kapcsolatban tartalmaz. A DDoS elleni védelmi réteget nem konfigurálható felhasználói attribútum tartozik, és nem érhető el az ügyfélnek. A DDoS elleni védelmi réteget az Azure platform nagy méretű támadások ellen védelmet biztosít, azt is figyeli a kimenő irányú kötött és a forgalmat több Azure-régió. Hálózati virtuális berendezések használata a virtuális hálózaton, rugalmasság további rétegeit egy kisebb méretezési támadásokkal szemben, amelyek nem a platform-szintű védelmet kivételre szemben az ügyfél által konfigurálható. A művelet; DDoS példa Ha egy internetkapcsolattal rendelkező IP-cím volt megtámadott nagyméretű DDoS-támadás által, az Azure lenne a forrásból a támadások észlelését és el kell távolítani a hibát okozó forgalmat, mielőtt elérte a kívánt rendeltetési. A támadás szinte minden esetben a megtámadott végpont nem érvényesek. Az, hogy a végpont érintett ritka esetben nincs forgalom más végpontokat, a megtámadott végpont van hatással. Más ügyfelek és a szolgáltatások így jelennének meg, hogy a támadás nincs hatással. Fontos megjegyezni, hogy az Azure DDoS csak nagy méretű támadások keres. Akkor lehet, hogy az adott szolgáltatás sikerült túlterhelte, mielőtt a platform-szintű védelmet küszöbérték túllépése. Például egy webhely egyetlen A0 IIS kiszolgálón sikerült kell kapcsolat nélküli módba tette DDoS-támadás előtt. a DDoS protection az Azure platform szintű fenyegetést.

2.  **Nyilvános IP-címek**: Nyilvános IP-cím (engedélyezi a szolgáltatásvégpontokat, nyilvános IP-cím címeket, az Application Gateway és más Azure-szolgáltatások, amelyek az internetre irányítja az erőforrás nyilvános IP-cím-n keresztül)-címek lehetővé teszik cloud services vagy az erőforrás csoportokat szeretné, hogy a nyilvános internetes IP-cím címek és portok közzétéve. A végpont hálózati címfordítás (NAT) használatával irányíthatja a forgalmat a belső cím és port, az Azure-beli virtuális hálózaton. Ez az elérési út a külső forgalmat a virtuális hálózatban módját. A nyilvános IP-címeket is meghatározhatja az adatforgalom átadott, és hol és hogyan lehet a virtuális hálózaton keresztül lefordított konfigurálható.

Miután a forgalom eléri a virtuális hálózat, nincsenek méretéből számos funkciót. Azure virtuális hálózatok alapját az ügyfelek számára csatolása a számítási feladatok, és ahol az alapvető hálózati szintű biztonsági alkalmazza a rendszer. A következő funkciókat és jellemzőkkel rendelkező ügyfelek számára az Azure-ban egy magánhálózaton (egy virtuális hálózat felirat):

* **Forgalom elkülönítése**: Egy virtuális hálózat a forgalomelkülönítési határok az Azure platformon. Virtuális gépek (VM) egy virtuális hálózat nem útján kommunikálnak közvetlenül egy másik virtuális hálózatot, virtuális gépek akkor is, ha az adott ügyfél által létrehozott két virtuális hálózatnak. Elkülönítés kritikus tulajdonság, amely biztosítja az ügyfél virtuális gépei, kommunikációs privát virtuális hálózaton belül marad.

>[!NOTE]
>Forgalom elkülönítése csak hivatkozik forgalom *bejövő* a virtuális hálózathoz. Alapértelmezés szerint a virtuális hálózatról az internetre irányuló kimenő forgalom engedélyezett, de megelőzhető, ha szükséges, az NSG-k által.
>
>

* **Többrétegű topológia**: Virtuális hálózatok lehetővé teszik ügyfeleink számára alhálózatok kiosztása és kijelölése a különálló címterek a különböző elemek vagy számítási feladataik "szint" többrétegű topológia meghatározása. Ezek logikai csoportosításán topológiák ügyfeleink a számítási feladatok típusa alapján különböző hozzáférési szabályzat definiálása és a rétegek közötti forgalom mennyiségét is szabályozhatja.
* **Létesítmények közötti kapcsolatok**: Ügyfeleink az Azure-ban is létrehozhat egy virtuális hálózat és több helyszíni hely vagy más virtuális hálózatok közötti létesítmények közötti kapcsolat. A kapcsolat létrehozására, ügyfelek használhatják a virtuális hálózatok közötti társviszony-létesítés, az Azure VPN-átjárók, külső hálózati virtuális berendezések vagy ExpressRoute. Az Azure támogatja a helyek közötti (S2S) VPN-eket szabványos IPsec/IKE-protokollok és a magánhálózati ExpressRoute-kapcsolat használatával.
* **NSG-t** lehetővé teszi a felhasználóknak (ACL) szabályok létrehozása a kívánt szinten, a részletesség: hálózati adapterek, a különálló virtuális gépek vagy a virtuális alhálózatok. Az ügyfelek hozzáférésének szabályozása lehetővé tevő vagy tagadja meg a virtuális hálózatban, az ügyfél-hálózatokon keresztül létesítmények közötti kapcsolatok, a rendszer a számítási feladatok közötti kommunikáció vagy közvetlen Internet-kommunikációt.
* **UDR** és **IP-továbbítás** lehetővé teszik az ügyfelek számára, hogy a virtuális hálózaton belüli különböző rétegek közötti kommunikációs utak meghatározása. Ügyfeleink a tűzfal, IDS/IPS és más virtuális berendezések üzembe helyezése, és a hálózati forgalom irányítása révén e biztonsági készülékek biztonsági határ házirendek betartását, naplózás és vizsgálat.
* **Hálózati virtuális berendezések** az Azure piactéren: Biztonsági eszközökkel, például a tűzfalak, terheléselosztók és IDS/IPS érhető el az Azure Marketplace-en és a virtuális gép rendszerkép-katalógusában. Ügyfelek telepíthetnek virtuális hálózatait, és kifejezetten, a biztonsági határoknak köszönhetően (a szegélyhálózat-alapú alhálózatokat is beleértve), ezek a készülékek végrehajtásához egy többrétegű biztonságos hálózati környezetben.

Ezek a funkciók és képességek például hogyan a szegélyhálózat-alapú hálózati architektúra sikerült létrehozni az Azure-ban a következő ábra:

![5]

## <a name="perimeter-network-characteristics-and-requirements"></a>Szegélyhálózat-alapú hálózati jellemzőit és követelményeit
A szegélyhálózaton az előtér, a hálózat közvetlenül kapcsolatba, az internetről érkező kommunikációt. A bejövő csomagok áramlásának keresztül biztonsági készülékek, például tűzfal, Azonosítók és IP-CÍMEK, a háttérkiszolgálók elérése előtt. A számítási feladatok az internetre irányuló csomagokat is áthaladhat a biztonsági berendezéseket a szegélyhálózaton a házirendek betartatását, vizsgálati és naplózási célokra, mielőtt a hálózaton. Ezenkívül a peremhálózaton létesítmények közötti VPN-átjárók ügyfél virtuális hálózatokat és a helyszíni hálózatok között is üzemeltethet.

### <a name="perimeter-network-characteristics"></a>Szegélyhálózat-alapú hálózati jellemzői
Az előző ábra hivatkozik, néhány jó szegélyhálózaton jellemzői a következők:

* Internet-facing:
  * A szegélyhálózat-alapú hálózat alhálózatán internetkapcsolattal, közvetlenül kommunikál az interneten.
  * Nyilvános IP-címeket, a virtuális IP-címek, illetve a végpontok internetes forgalmat át, az előtér-hálózati és az eszközök.
  * Az internetről bejövő forgalmat áthalad az előtér-hálózati egyéb erőforrások biztonsági eszközöket.
  * Ha engedélyezve van a kimenő biztonsági, adatforgalom áthalad biztonsági eszközöket, az utolsó lépést, az internethez való előtt.
* Védett hálózat:
  * Nincs az alapvető infrastruktúra, az internetről érkező közvetlen útvonal.
  * Az alapvető infrastruktúra csatornákat kell bejárni biztonsági eszközök, például az NSG-k, a tűzfalakon és a VPN-eszközök.
  * Egyéb eszközök nem kell áthidalhatók az internetes és az alapvető infrastruktúra.
  * Az internetre irányuló és a védett hálózat határait a szegélyhálózaton (például a két tűzfal ikon az előző ábrán látható) irányuló biztonsági eszközök ténylegesen lehet egyetlen virtuális berendezés egyedi szabályok vagy felületek minden határ. Ha például egy fizikai eszközt, logikailag elkülönített, a peremhálózaton egyaránt határait a terhelés kezelésére.
* Egyéb gyakori eljárások és korlátozások:
  * Számítási feladatok nem kell tárolnia a kritikus fontosságú üzleti adatok.
  * Hozzáférés és a szegélyhálózat-alapú hálózati konfigurációkat és a központi telepítések frissítéseit csak a meghatalmazott rendszergazdák korlátozódnak.

### <a name="perimeter-network-requirements"></a>Szegélyhálózat-alapú hálózati követelmények
Ahhoz, hogy a következő jellemzőkkel, kövesse ezeket az irányelveket a virtuális hálózati követelmények sikeres szegélyhálózaton megvalósításához:

* **Alhálózat-architektúra:** Adja meg a virtuális hálózat, úgy, hogy egy teljes alhálózathoz van kijelölve, mint az azonos virtuális hálózatban lévő alhálózatok elkülöníti a szegélyhálózaton. Az elkülönítés biztosítja, hogy a forgalom között a szegélyhálózat és más belső vagy privát alhálózatra rétegek folyamatok IDS/IPS virtuális berendezésre vagy a tűzfalon keresztül.  Felhasználó által megadott útvonalak a határ alhálózatok van szükség a forgalom virtuális készülékre továbbítja.
* **NSG-T:** A szegélyhálózat-alapú hálózati alhálózatról engedélyezi a kommunikációt az internetes nyitva kell lennie, de az, hogy nem jelenti azt kell ügyfelek megkerüli az NSG-ket. Kövesse az általános biztonsági eljárások az internettel érintkező hálózati felületek minimalizálása érdekében. A központi telepítéseket vagy adott alkalmazás protokollok és portok, a nyitott elérésére jogosult távoli címtartományok zárolását. Előfordulhat, hogy körülmények között azonban egy teljes zár le nem lehetséges. Például ha ügyfelek az Azure-ban egy külső webhelyre rendelkezik, a peremhálózaton engedélyeznie kell a beérkező webes kérelmek bármely nyilvános IP-címekről, de csak a webes alkalmazás portok nyisson meg: TCP-port a 80-as és/vagy a TCP 443-as porton.
* **Útválasztási táblázat:** A szegélyhálózat-alapú hálózat alhálózatán közvetlen kommunikációra az internettel képesnek kell lennie, de nem engedélyezheti a közvetlen kommunikációt, és vissza a teljes vagy a helyszíni hálózatot egy tűzfal vagy biztonsági berendezés áthaladás nélkül.
* **Biztonsági berendezés konfigurációjának:** Eszközök irányítása, és vizsgálja meg a csomagok a szegélyhálózat és a többi a védett hálózatokon, a biztonsági eszközökkel, például tűzfal, Azonosítók és IP-CÍMEK között lehet többhelyű. Előfordulhat, hogy rendelkeznek a szegélyhálózat és a háttérbeli alhálózatok elkülönülő hálózati adaptereket. A hálózati adaptereket a peremhálózaton kommunikálnak közvetlenül, és az internetről, a megfelelő NSG-k és a szegélyhálózat-alapú hálózati útválasztási táblázatához. A hálózati adapter csatlakozik a háttérbeli alhálózatok további korlátozta az NSG-k és a megfelelő háttér-alhálózatok táblákat.
* **Biztonsági berendezés funkciókat:** A biztonsági készülékek jellemzően üzembe helyezve a szegélyhálózaton hajtsa végre a következő funkciókat:
  * Tűzfal: Tűzfal- és hozzáférés-vezérlési házirendeket a bejövő kérések kényszerítése.
  * Fenyegetések észlelése és megelőzése: Észlelni, és az internetről származó rosszindulatú támadások kiküszöbölni.
  * A vizsgálati és naplózási: Részletes naplók vizsgálati és elemzési karbantartása.
  * Fordított proxy: A bejövő kérelmek átirányítása a megfelelő háttérkiszolgálóra. Az átirányítást magában foglalja a leképezést, és az előtér-eszközökön a cél-címek fordítása általában tűzfalak, a háttérkiszolgáló-címeket.
  * Forward proxy: NAT biztosít, és az internethez a virtuális hálózaton belül kezdeményezett kommunikáció a naplózás végrehajtása.
  * Útválasztó: Továbbítja a bejövő és az alhálózatok közötti forgalmat a virtuális hálózaton belül.
  * VPN-eszköz: A létesítmények közötti VPN Gateway-átjárók létesítmények közötti VPN-kapcsolat helyszíni ügyfélhálózatok és Azure virtuális hálózatok közötti átjáróként.
  * VPN-kiszolgáló: Az Azure virtuális hálózatokhoz csatlakozó VPN-ügyfelek fogad.

> [!TIP]
> A következő két csoporthoz különválasztásában: a felhasználók jogosult hozzáférni a szegélyhálózat-alapú hálózati biztonsági fogaskerék és az egyéni felhasználók számára engedélyezni, fejlesztési, üzembe helyezési vagy műveletek rendszergazdái. Mivel külön kezeljük ezeket a csoportokat lehetővé teszi, hogy a feladatkörök elkülönítése, és megakadályozza, hogy egyetlen személy megkerüli az alkalmazások biztonsági és a hálózati biztonsági vezérlők.
>
>

### <a name="questions-to-be-asked-when-building-network-boundaries"></a>Kérdések beérkezésekor hálózathatárok létrehozásához
Ebben a szakaszban említett, a "hálózat" kifejezés egy előfizetés-rendszergazda által létrehozott privát Azure virtuális hálózatok. A kifejezés nem hivatkozik a mögöttes fizikai hálózatok Azure-ban.

Azure virtuális hálózatok is, gyakran használják a hagyományos helyi hálózatokat bővíteni. Site-to-site vagy ExpressRoute hibrid hálózati megoldásokat szegélyhálózat-alapú hálózati architektúrák átfogó lehetőség. A hibrid kapcsolat hálózati biztonsági határok létrehozásakor fontos szempont.

Az alábbi három kérdések különösen fontos megválaszolni a szegélyhálózaton és több biztonsági határokat tartalmazó készítésekor.

#### <a name="1-how-many-boundaries-are-needed"></a>1.) hány határok van szükség?
A legelső döntés, hogy úgy dönt, hogy hány biztonsági határokat egy adott helyzetben van szükség:

* Egy határ: Az egyik az előtérbeli peremhálózaton, a virtuális hálózat és az Internet között.
* Két határok: Az egyik internetes oldalán a peremhálózaton, a másik pedig a szegélyhálózat-alapú hálózati alhálózat és a háttérbeli alhálózataihoz az Azure virtuális hálózatok között.
* Három határok: Egy internetes oldalán a szegélyhálózaton található, és az egyik a szegélyhálózat és a háttérbeli alhálózatok között, és egy háttérbeli alhálózatok és a helyszíni hálózat között.
* N határok: Egy változó szám. Biztonsági követelményeitől függően nem alkalmazható az adott hálózati biztonsági határok száma nincs korlátozva.

Száma és típusa szükséges eltérőek lehetnek határok alapján a vállalat szervezet kockázattűrési határát, és az adott forgatókönyv végrehajtása alatt. A döntést gyakran együtt több csoport a szervezeten belüli gyakran többek között a kockázat és megfelelőségi csapat, a hálózati és a platform csapata és a egy alkalmazásfejlesztési csoportnak. Biztonság, az érintett adatok és a használt technológiák ismerete rendelkező személyek ezt a döntést, annak biztosítása érdekében a megfelelő biztonsági forgalmazóval egyes végrehajtásához egy say kell rendelkezniük.

> [!TIP]
> A legkisebb számát, amely megfelel egy adott helyzetben a biztonsági határokat használja. További határokkal, műveleteket és elhárítása lehet bonyolultabb, valamint a felügyeleti terheket részt vevő és idővel a több határcsoport-házirendek kezelése. Azonban elegendő határok növeljék. Keresés az egyenleg, kritikus fontosságú.
>
>

![6]

Az előző ábrán három biztonsági határ hálózati egy magas szintű nézetét mutatja be. A határok a szegélyhálózat és az internethez, Azure előtérbeli és háttérbeli titkos alhálózatait, és az Azure háttér-alhálózat és a helyszíni vállalati hálózat között.

#### <a name="2-where-are-the-boundaries-located"></a>2.) hol találhatók a határok?
Határok száma a rendszer úgy döntött, miután végrehajtja, hol van a következő döntési pont. Nincsenek általában három választási lehetőségek:

* Egy internetalapú köztes szolgáltatások (például egy felhőalapú webalkalmazási tűzfal, amely nem az ebben a dokumentumban tárgyalt) használatával
* Natív szolgáltatásait és/vagy a hálózati virtuális berendezések használata az Azure-ban
* A helyszíni hálózat fizikai eszközök használata

Csak Azure-hálózatok a beállítások vonatkoznak a natív Azure-szolgáltatások (például a Azure Load Balancer Terheléselosztók) vagy a hálózati virtuális berendezések a gazdag ökoszisztémája az Azure (például a Check Point tűzfalak).

Ha a határok az Azure és a egy helyszíni hálózat között van szükség, a biztonsági eszközök a kapcsolat (vagy mindkét) egyik oldalán is találhatók. Így döntést kell végezni a helyen helyezi el a biztonsági fogaskerék.

Az előző ábrán az Internet-peremhálózati és az előtér-az-háttér-határok teljes mértékben Azure-ban található, és kell lennie vagy a natív Azure-funkciókról, vagy a hálózati virtuális berendezések. Biztonsági eszközök között (háttér-alhálózat) az Azure és a vállalati hálózat határán vagy az Azure oldalán, vagy a helyszíni oldalon, vagy akár mindkét oldalon eszközök kombinációja lehet. Jelentős előnyei és hátrányai bármelyik beállítást is választja, komolyan meg kell fontolni is lehet.

Például a helyszíni hálózati oldalán a meglévő fizikai biztonsági fogaskerék használva, azt az előnyt, hogy nincs új fogaskerék van szükség. Csak az újrakonfigurálás van szüksége. Ennek a hátránya azonban nem, hogy minden forgalom kell térjen vissza az Azure-ból a helyszíni hálózathoz is lássák a biztonsági fogaskerék. Így merülnek fel az Azure – Azure traffic sikerült jelentős késést, és hatással alkalmazásteljesítmény és a felhasználói élmény, ha vissza a biztonsági házirendek betartását a helyszíni hálózat kénytelen volt.

#### <a name="3-how-are-the-boundaries-implemented"></a>3.) hogyan valósíthatók meg a határok?
Minden biztonsági határ valószínűleg fog rendelkezni a különböző képességekre vonatkozó követelmények (például Azonosítók és tűzfalszabályok Internet oldalán található a peremhálózaton, de csak hozzáférés-vezérlési listák a szegélyhálózat és a háttérbeli alhálózat között). Annak eldöntése, amelyen eszköz (vagy hány eszközt) használata a forgatókönyv és a biztonsági követelményeitől függ. A következő szakaszban található példák 1, 2 és 3 bizonyos beállítások, amelyek felhasználhatók tárgyalják. Az Azure natív hálózati funkciókat és a partneri ökoszisztémánkat az Azure-ban elérhető eszközök áttekintése a számtalan lehetőség legyen elérhető a megoldásában gyakorlatilag bármilyen forgatókönyvhöz jeleníti meg.

Egy másik fő végrehajtása, hogy a helyszíni hálózat csatlakoztatása az Azure-ral. Használja az Azure virtuális átjáró vagy egy hálózati virtuális berendezésen? Ezek a beállítások a következő szakaszban (4, 5 és 6. példák) részletesebben ismertetik.

Emellett Azure-on belüli virtuális hálózatok közötti forgalom lehet szükség. Ezek a forgatókönyvek felveszi a jövőben.

Ha már tudja, hogy az előző kérdésekre adott válaszokat a [Fast Start](#fast-start) szakasz segít azonosítani, hogy milyen példák egy adott forgatókönyvhöz leginkább megfelelő.

## <a name="examples-building-security-boundaries-with-azure-virtual-networks"></a>Példák: Az Azure virtual Network szolgáltatással biztonsági határok létrehozása
### <a name="example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs"></a>1. példa szegélyhálózaton segítségével védheti az alkalmazásokat az NSG-ket hozhat létre.
[Vissza a gyors kezdési](#fast-start) | [részletes utasításokat ebben a példában hozhat létre.][Example1]

[![7]][7]

#### <a name="environment-description"></a>Környezet leírása
Ebben a példában van egy előfizetést, amely tartalmazza az alábbi forrásanyagokat:

- Egyetlen erőforráscsoportra
- Egy virtuális hálózatot két alhálózattal: "Előtér" és "Háttér"
- Mindkét alhálózat alkalmazott hálózati biztonsági csoport
- A Windows server, amely egy webalkalmazás-kiszolgáló ("IIS01")
- Háttér-alkalmazáskiszolgálókat ("AppVM01", "AppVM02") képviselő két Windows-kiszolgálókból
- A Windows server DNS-kiszolgáló ("DNS01") jelölő
- Az alkalmazás webes kiszolgálóhoz társított nyilvános IP-cím

Parancsprogramok és Azure Resource Manager-sablon: a [részletes útmutatásokat a build][Example1].

#### <a name="nsg-description"></a>NSG-t leírása
Ebben a példában egy NSG-csoport beépített, és aztán betölti a hat szabályokkal.

> [!TIP]
> Általánosan fogalmazva létre kell hoznia az adott "Engedélyezése" szabályok először az "Elutasítás" általánosabb érvényűvé szabályokat követ. A megadott prioritás szabja meg, mely szabályok először értékeli ki. Forgalom kiderül, hogy egy adott szabály vonatkozik, ha nincsenek további szabályok értékeli ki. Az NSG-szabályok (az alhálózat szempontjából) bejövő vagy kimenő irányban alkalmazhatja.
>
>

Deklaratív a következő szabályok beépített folyamatban van a bejövő forgalmat:

1. Belső DNS-forgalom (53-as port) használata engedélyezett.
2. RDP-forgalmat (3389-es port) az internetről bármely virtuális gép használata engedélyezett.
3. HTTP (80-as port) az internetről forgalmat web Server (IIS01) használata engedélyezett.
4. Minden újabb forgalmát (minden porthoz) IIS01 AppVM1 használata engedélyezett.
5. Az összes bejövő forgalom (minden porthoz) az internetről a teljes virtuális hálózat (mindkét alhálózat) a rendszer megtagadja.
6. Az összes bejövő forgalom (minden porthoz) az előtérbeli alhálózatból a háttérbeli alhálózat megtagadva.

A következő szabályok kötött mindegyik olyan alhálózatban, ha HTTP-kérést a webkiszolgáló, 3 szabályokat is az internetről bejövő (engedélyezése) és 5 (elutasítása) vonatkoznak. De %3 szabályt egy magasabb prioritással bír, mivel csak akkor vonatkozik, és 5 szabály lenne nem jut. Így a HTTP-kérelem szeretné engedélyezni a webkiszolgáló. Ha ugyanaz a forgalom próbál elérni az DNS01 kiszolgálót, a szabály 5 (elutasítása) lenne a alkalmazni az első, és szeretne átadni a kiszolgálónak nem engedélyezi a forgalmat. 6. szabály (elutasítása) letiltja az előtérbeli alhálózat léptünk a háttérbeli alhálózat (kivéve az 1. és 4 szabályok engedélyezett forgalom). A szabálykészlet védi a háttér-hálózat, abban az esetben a támadó károsan befolyásolja a webalkalmazás az előtérrendszerben. A támadó volna csak korlátozott hozzáféréssel rendelkeznek a háttér-"védett" hálózathoz (csak az erőforrások elérhetővé tett a AppVM01 kiszolgálón).

Nincs olyan alapértelmezett kimenő szabály, amely lehetővé teszi, hogy az internetre irányuló forgalom. Ebben a példában folyamatban van lehetővé teszi a kimenő forgalom és nem módosítja a kimenő szabályok. Forgalom mindkét irányba zárolását, hogy a felhasználó által meghatározott útválasztás szükség (lásd a 3. példa:).

#### <a name="conclusion"></a>Összegzés
Ebben a példában egy viszonylag egyszerű és átlátható módszer elkülönítése a bejövő forgalmat a háttérbeli alhálózat. További információkért lásd: a [részletes útmutatásokat a build][Example1]. Ezek az utasítások tartalmaznak:

* Hogyan hozhat létre a klasszikus PowerShell-parancsfájlok a peremhálózaton.
* Hogyan hozhat létre egy Azure Resource Manager-sablonnal a peremhálózaton.
* Minden NSG parancs részletes leírása.
* Részletes adatforgalom folyamat-forgatókönyvekhez, hogyan van engedélyezett vagy tiltott forgalmat minden egyes rétegben megjelenítése.


### <a name="example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs"></a>2. példa hozhat létre alkalmazásokat a tűzfallal és NSG-k védelme érdekében a szegélyhálózaton
[Vissza a gyors kezdési](#fast-start) | [részletes utasításokat ebben a példában hozhat létre.][Example2]

[![8]][8]

#### <a name="environment-description"></a>Környezet leírása
Ebben a példában van egy előfizetést, amely tartalmazza az alábbi forrásanyagokat:

* Egyetlen erőforráscsoportra
* Egy virtuális hálózatot két alhálózattal: "Előtér" és "Háttér"
* Mindkét alhálózat alkalmazott hálózati biztonsági csoport
* Az előtérbeli alhálózat egy hálózati virtuális berendezésen, ebben az esetben egy tűzfal, csatlakoztatva
* A Windows server, amely egy webalkalmazás-kiszolgáló ("IIS01")
* Háttér-alkalmazáskiszolgálókat ("AppVM01", "AppVM02") képviselő két Windows-kiszolgálókból
* A Windows server DNS-kiszolgáló ("DNS01") jelölő

Parancsprogramok és Azure Resource Manager-sablon: a [részletes útmutatásokat a build][Example2].

#### <a name="nsg-description"></a>NSG-t leírása
Ebben a példában egy NSG-csoport beépített, és aztán betölti a hat szabályokkal.

> [!TIP]
> Általánosan fogalmazva létre kell hoznia az adott "Engedélyezése" szabályok először az "Elutasítás" általánosabb érvényűvé szabályokat követ. A megadott prioritás szabja meg, mely szabályok először értékeli ki. Forgalom kiderül, hogy egy adott szabály vonatkozik, ha nincsenek további szabályok értékeli ki. Az NSG-szabályok (az alhálózat szempontjából) bejövő vagy kimenő irányban alkalmazhatja.
>
>

Deklaratív a következő szabályok beépített folyamatban van a bejövő forgalmat:

1. Belső DNS-forgalom (53-as port) használata engedélyezett.
2. RDP-forgalmat (3389-es port) az internetről bármely virtuális gép használata engedélyezett.
3. Minden internetes forgalom (minden porthoz) a hálózati virtuális berendezésre (tűzfal) használata engedélyezett.
4. Minden újabb forgalmát (minden porthoz) IIS01 AppVM1 használata engedélyezett.
5. Az összes bejövő forgalom (minden porthoz) az internetről a teljes virtuális hálózat (mindkét alhálózat) a rendszer megtagadja.
6. Az összes bejövő forgalom (minden porthoz) az előtérbeli alhálózatból a háttérbeli alhálózat megtagadva.

A következő szabályok kötött mindegyik olyan alhálózatban, ha HTTP-kérést a tűzfalhoz, 3 szabályokat is az internetről bejövő (engedélyezése) és 5 (elutasítása) vonatkoznak. De %3 szabályt egy magasabb prioritással bír, mivel csak akkor vonatkozik, és 5 szabály lenne nem jut. Így a HTTP-kérelem szeretné engedélyezni a tűzfalon. Ha ugyanaz a forgalom próbál elérni az IIS01 kiszolgálót, akkor is, ha az előtér-alhálózat, a szabály 5 (nem engedélyezi a) a alkalmazni szeretné, és szeretne átadni a kiszolgálónak nem engedélyezi a forgalmat. 6. szabály (elutasítása) letiltja az előtérbeli alhálózat léptünk a háttérbeli alhálózat (kivéve az 1. és 4 szabályok engedélyezett forgalom). A szabálykészlet védi a háttér-hálózat, abban az esetben a támadó károsan befolyásolja a webalkalmazás az előtérrendszerben. A támadó volna csak korlátozott hozzáféréssel rendelkeznek a háttér-"védett" hálózathoz (csak az erőforrások elérhetővé tett a AppVM01 kiszolgálón).

Nincs olyan alapértelmezett kimenő szabály, amely lehetővé teszi, hogy az internetre irányuló forgalom. Ebben a példában folyamatban van lehetővé teszi a kimenő forgalom és nem módosítja a kimenő szabályok. Forgalom mindkét irányba zárolását, hogy a felhasználó által meghatározott útválasztás szükség (lásd a 3. példa:).

#### <a name="firewall-rule-description"></a>Tűzfal szabály leírása
A tűzfalon továbbítási szabályokat kell létrehozni. Mivel ebben a példában csak a kötve, a tűzfal internetes forgalmat irányítja, és a webkiszolgálóhoz, hogy csak egy továbbító majd hálózati címfordítás (NAT) szabályt van szükség.

A továbbítási szabályt fogad el minden olyan bejövő forrás címe, amely eléri a tűzfal megpróbálják elérni az HTTP (port 80-as vagy 443-as HTTPS-hez). Helyi kapcsolat a tűzfalon kívüli küldött, és átirányítja a webkiszolgáló a 10.0.1.5 IP-címét.

#### <a name="conclusion"></a>Összegzés
Ebben a példában egy viszonylag egyszerű módja az alkalmazás egy tűzfal védelmének, és a bejövő forgalmat a háttérbeli alhálózat elkülönítése. További információkért lásd: a [részletes útmutatásokat a build][Example2]. Ezek az utasítások tartalmaznak:

* Hogyan hozhat létre a klasszikus PowerShell-parancsfájlok a peremhálózaton.
* Ebben a példában az Azure Resource Manager-sablonok létrehozásának módját.
* Minden NSG-t a parancs- és tűzfalbeállítások szabály részletes leírása.
* Részletes adatforgalom folyamat-forgatókönyvekhez, hogyan van engedélyezett vagy tiltott forgalmat minden egyes rétegben megjelenítése.

### <a name="example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg"></a>3. példa hozhat létre egy tűzfal és az UDR és NSG-t a hálózatok védelme érdekében a szegélyhálózaton
[Vissza a gyors kezdési](#fast-start) | [részletes utasításokat ebben a példában hozhat létre.][Example3]

[![9]][9]

#### <a name="environment-description"></a>Környezet leírása
Ebben a példában van egy előfizetést, amely tartalmazza az alábbi forrásanyagokat:

* Egyetlen erőforráscsoportra
* Egy virtuális hálózatot három alhálózattal: "SecNet", "Előtér" és "Háttér"
* A SecNet alhálózathoz csatlakoztatott hálózati virtuális berendezés, ebben az esetben egy tűzfal,
* A Windows server, amely egy webalkalmazás-kiszolgáló ("IIS01")
* Háttér-alkalmazáskiszolgálókat ("AppVM01", "AppVM02") képviselő két Windows-kiszolgálókból
* A Windows server DNS-kiszolgáló ("DNS01") jelölő

Parancsprogramok és Azure Resource Manager-sablon: a [részletes útmutatásokat a build][Example3].

#### <a name="udr-description"></a>Udr-t leírása
Alapértelmezés szerint a következő rendszerútvonalak vannak meghatározva:

        Effective routes :
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

A VNETLocal mindig egy vagy több meghatározott címelőtagok a virtuális hálózat számára, hogy bizonyos hálózati alkotó (azt jelenti, változik a virtuális hálózatról virtuális hálózatra, attól függően, hogyan mindegyik megadott virtuális hálózat van meghatározva). A fennmaradó rendszerútvonalak statikusak, és az alapértelmezett jelzett a táblában.

Ebben a példában két útválasztási táblázatok jönnek létre, egyet-egyet az előtér- és alhálózatokat. Minden tábla be vannak töltve az adott alhálózat megfelelő statikus útvonalakat. Ebben a példában mindegyik tábla három útvonalakat, hogy minden adatforgalmat: (0.0.0.0/0) rendelkezik a tűzfalon keresztül (a következő ugrás = a virtuális készülék IP-cím):

1. A következő ugrás a helyi alhálózat forgalmának meghatározva, amely engedélyezi a helyi alhálózat forgalmának, hogy a tűzfal megkerülését.
2. A következő ugrás definiálva, a tűzfal a virtuális hálózati forgalmat. A következő ugrás felülbírálja az alapértelmezett szabályt, amely lehetővé teszi a helyi virtuális hálózati forgalom közvetlenül továbbítani.
3. Az összes többi forgalom (0/0) a következő ugrás definiálva, a tűzfal.

> [!TIP]
> Nem rendelkezik a helyi alhálózaton bejegyzés az UDR oldaltörések helyi alhálózati kommunikáció.
>
> * Ebben a példában a VNETLocal mutató 10.0.1.0/24 fontos! Nélkül, és a egy másik helyi kiszolgáló (például) 10.0.1.25 felé irányuló Web Server (10.0.1.4 cím – ez) csomag sikertelen lesz, mivel ezek az nva-t küld. Az nva-t küld az alhálózathoz, és a végtelen ciklusban az nva-t az alhálózathoz újra elküldi.
> * Az útválasztási hurok általában magasabb, a több hálózati adapterrel, különböző alhálózataihoz, csatlakoztatott készülékeket Ez gyakran hagyományos, helyszíni készülékek.
>
>

Ha az útválasztási táblázatok jönnek létre, azokat az alhálózatokhoz kell kötni. Ez a kimenet az előtérbeli alhálózat útválasztási táblázatban, egyszer létrehozott és az alhálózat kötött jelenne meg:

        Effective routes :
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active

> [!NOTE]
> Udr-t is érvényesek az átjáró-alhálózatot, amelyen az ExpressRoute-kapcsolatcsoporthoz csatlakozik.
>
> Példák a szegélyhálózat-alapú hálózat az ExpressRoute- vagy helyek közötti hálózati engedélyezése a 3. és 4 példák láthatók.
>
>

#### <a name="ip-forwarding-description"></a>IP Forwarding description
Az IP-továbbítás egy udr-t kiegészítő szolgáltatást. IP-továbbítást a virtuális berendezés, amely lehetővé teszi, hogy a forgalom a készülék nem kifejezetten fogadását, és majd továbbítsa a forgalmat a végső rendeltetési a beállítást.

Például AppVM01 kérést küld a DNS01 kiszolgálóhoz, udr-t kellene irányíthatja a forgalmat a tűzfal. Az IP-továbbítás engedélyezve van a forgalmat a DNS01 cél (10.0.2.4 cím) fogadja el a készülék (10.0.0.4), amely ezután továbbítja a végső rendeltetési helyükre (10.0.2.4 cím). Anélkül, hogy az IP-továbbítás engedélyezve van a tűzfalon forgalom lenne nem fogadja el a készülék annak ellenére, hogy az útvonaltáblát tartalmaz a következő ugrás a tűzfalon. Virtuális berendezés használata esetén fontos, hogy az udr-t és az IP-továbbítás engedélyezése.

#### <a name="nsg-description"></a>NSG-t leírása
Ebben a példában egy NSG-csoport beépített, és aztán betölti a egyetlen szabállyal. Ez a csoport majd kötött csak az előtérbeli és háttérbeli alhálózatok (nem a SecNet). Deklaratív a következő szabály létrehozása folyamatban van:

* Az összes bejövő forgalom (minden porthoz) az internetről a teljes virtuális hálózathoz (az összes alhálózat) a rendszer megtagadja.

Bár ebben a példában az NSG-ket használja, a fő célja manuális kiszolgálóhiba elleni védelmet a másodlagos összetevővel. A cél, hogy minden bejövő forgalom az internetről az előtérbeli vagy a háttérbeli alhálózatokhoz blokkolása. Forgalom csak áthaladhat a tűzfalhoz a SecNet alhálózat (, majd, ha szükséges, az előtérbeli vagy a háttérbeli alhálózatok be). Ezenkívül az UDR szabályoknak helyen, tette, hogy az előtér- és háttér-alhálózatra adatforgalmat lenne átirányítja ki (UDR) nak köszönhetően a tűzfalon. A tűzfal jelennének meg ezt a forgalmat egy aszimmetrikus folyamatként, és a kimenő forgalom volna eldobni. Ezért az alábbi három réteg védelmét az alhálózatok biztonsági:

* Nincs nyilvános IP-címek előtér-vagy háttérbeli hálózati adapter.
* Az NSG-k tagadja meg a forgalom az internetről.
* A tűzfal-eldobása aszimmetrikus forgalmat.

Ebben a példában az NSG kapcsolatos egy érdekes pont, hogy csak egy szabályt, amely letiltja a internetes forgalmat a teljes virtuális hálózathoz, például a biztonság – alhálózati tartalmazza. Azonban az NSG-t csak az előtérbeli és háttérbeli alhálózataihoz van kötve, mivel a szabály nem feldolgozott forgalom a bejövő biztonsági az alhálózathoz. Ennek eredményeképpen a forgalom a biztonsági alhálózathoz.

#### <a name="firewall-rules"></a>Tűzfalszabályok
A tűzfalon továbbítási szabályokat kell létrehozni. Mivel a tűzfal blokkolja-e vagy továbbító minden bejövő, kimenő, és on belüli virtuális hálózati forgalmat, számos tűzfalszabályok van szükség. Az összes bejövő forgalmat is, a Security Service nyilvános IP-cím (a különböző portok), eléri a tűzfal által feldolgozandó. Ajánlott eljárás, hogy a logikai folyamatokban az alhálózatok beállítása előtt a diagramon, és később újragyártási tűzfalszabályok elkerülése érdekében. Az alábbi ábra az ebben a példában a tűzfalszabályok logikai nézetében:

![10]

> [!NOTE]
> A használt hálózati virtuális berendezésen alapján, a felügyeleti portokat eltérőek lehetnek. Ebben a példában a Barracuda NextGen tűzfal hivatkozik, amely 801, 807 és a 22-es portot használja. A berendezés gyártó dokumentumaiban keresése a pontos portokat használja az eszköz felügyeletére használható.
>
>

#### <a name="firewall-rules-description"></a>Tűzfal-szabályok leírásai
A fenti logikai ábrán a biztonság – alhálózati nem látható, mert a tűzfalat, hogy az egyetlen erőforrás az alhálózaton. Az ábrán látható a tűzfalszabályokat, és hogyan azok logikailag engedélyezik vagy megtagadják a forgalom adatfolyamait, a tényleges irányított elérési útján nem. Is, a kiválasztott magasabbak, az RDP-forgalmat a külső portok előre portok (8014 – 8026), és lazán igazodnak a helyi IP-cím az egyszerűbb olvashatóság érdekében a két utolsó bájt kijelölve (például helyi kiszolgáló címe 10.0.1.4 cím – ez van társítva külső port 8014). Bármilyen magasabb nem ütköző portok, azonban használható.

Ebben a példában kell hét típusú szabályok:

* Külső szabályok (bejövő forgalom) számára:
  1. Felügyeleti tűzfalszabályt: Az alkalmazás átirányítási szabály lehetővé teszi, hogy a forgalom számára a felügyeleti portokat a hálózati virtuális berendezéshez.
  2. RDP-szabályok (az egyes Windows-kiszolgáló): Ezek a négy szabályok (kiszolgálónként egyet) lehetővé teszik, hogy RDP-n keresztül az egyes kiszolgálók felügyeletét. A négy RDP-szabályok sikerült is össze lehet csukni használt hálózati virtuális berendezésen függően egy szabályba.
  3. Alkalmazás-forgalomra vonatkozó szabályokat: Nincsenek két ezeket a szabályokat, az első az előtérbeli webes forgalom számára, és a második a háttér-forgalom (például web server adatréteg). Ezek a szabályok konfigurálása a hálózati architektúra (ahol a kiszolgálók kerülnek) függ, és folyamatok forgalmi (melyik irányba halad a forgalom és az azoknak a portoknak).
     * Az első szabály lehetővé teszi, hogy az alkalmazás tényleges forgalom elérhesse a kiszolgáló. Amíg a többi szabály engedélyezi a biztonsági és felügyeleti, alkalmazás forgalmi szabályok, az alkalmazások eléréséhez a külső felhasználók vagy szolgáltatások lehetővé teszik. Ebben a példában nincs egyetlen webkiszolgálón 80-as porton. Így egyetlen tűzfalszabályt alkalmazás bejövő forgalmat a külső IP-cím, a webes kiszolgálók belső IP-címre irányítja. A belső kiszolgáló NAT-n keresztül a forgalmat munkamenetet szeretne lefordítani.
     * A második szabálynak a háttér-szabály lehetővé teszi a webkiszolgáló kommunikáljon a AppVM01 kiszolgáló (de nem AppVM02) bármely porton keresztül.
* Belső szabályok (a on belüli virtuális hálózati forgalom)
  1. Kimenő Internet-szabályhoz: Ez a szabály lehetővé teszi, hogy a forgalom a kiválasztott hálózatok átadása bármely hálózatból. Ez a szabály általában az alapértelmezett szabály már a tűzfalon, de letiltott állapotban. Ebben a példában ez a szabály engedélyezni kell.
  2. DNS-szabály: Ez a szabály lehetővé teszi, hogy csak (53-as port) a DNS-forgalom számára a DNS-kiszolgáló. Ebben a környezetben a legtöbb forgalmat az előtértől a háttérbe le van tiltva. Ez a szabály a bármilyen helyi alhálózatról DNS kifejezetten engedélyezi.
  3. Alhálózat alhálózati szabályhoz: Ez a szabály az összes olyan kiszolgálónak engedélyezi a háttér-alhálózat bármely az előtérbeli alhálózat (de nem fordítva) a kiszolgálóhoz való csatlakozáshoz.
* Hibamentes (forgalomra vonatkozó szabály, amely nem felel meg az előző bármely):
  1. Minden forgalmi szabály megtagadása: Ez a megtagadási szabály mindig a végső szabályát (tekintetében prioritás) kell lennie, és így ha az adott nem egyezik meg a fenti szabály helyezés Ez a szabály. Ez a szabály az alapértelmezett szabály, és általában helyszíni és az aktív. Nem módosított előfizetésekben általában ez a szabály van szükség.

> [!TIP]
> A második alkalmazás forgalmának szabályát, az ebben a példában leegyszerűsítése érdekében bármely porton engedélyezve van. A valós forgatókönyvekben a nejvíce specifické port és címtartomány kell használni, ez a szabály a támadási felület csökkentése érdekében.
>
>

Ha az előbbi szabályok jönnek létre, fontos tekintse át az egyes szabályt, hogy a forgalom engedélyezett vagy tiltott igény szerint prioritását. Ebben a példában a szabályok prioritási sorrendben vannak.

#### <a name="conclusion"></a>Összegzés
Ebben a példában egy sokkal összetettebb de védelme és elkülönítése a hálózaton, mint az előző példák befejezéséhez. (2. példa csak az alkalmazás védi meg, és az 1. példa csak közt megadott kezdőkönyvtárra korlátozása alhálózatok). Ez a kialakítás lehetővé teszi, hogy mindkét irányú forgalom figyelése, és nem csak a bejövő-kiszolgáló védi, de érvényesíti a hálózati biztonsági házirend az összes kiszolgáló a hálózaton. Ezenkívül függően használja a készülék teljes forgalom naplózása és figyelése érhető el. További információkért lásd: a [részletes útmutatásokat a build][Example3]. Ezek az utasítások tartalmaznak:

* Hogyan hozhat létre a például szegélyhálózatban klasszikus PowerShell parancsfájlokkal.
* Ebben a példában az Azure Resource Manager-sablonok létrehozásának módját.
* Minden egyes UDR NSG-t leírása részletes parancsot, és a tűzfalszabály.
* Részletes adatforgalom folyamat-forgatókönyvekhez, hogyan van engedélyezett vagy tiltott forgalmat minden egyes rétegben megjelenítése.

### <a name="example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn"></a>4. példa egy hibrid kapcsolat hozzáadása egy VPN-helyek, virtuális berendezéssel
[Vissza a gyors kezdési](#fast-start) |} Hamarosan részletes build útmutatás érhető el

[![11]][11]

#### <a name="environment-description"></a>Környezet leírása
Hibrid hálózatkezelés egy hálózati virtuális készüléket (NVA) használatával a szegélyhálózat-alapú hálózati típusok bemutatott példákban 1, 2 vagy 3 lehet hozzáadni.

Az előző ábrán látható, az interneten (site-to-site) VPN-kapcsolat segítségével a helyszíni hálózat csatlakoztatása az Azure virtual Networkhöz az NVA-n keresztül.

> [!NOTE]
> Ha az ExpressRoute használata az Azure nyilvános társviszony-létesítés beállítás engedélyezve van, egy statikus útvonalat kell létrehozni. Statikus útvonal kell átirányítása az NVA VPN IP-címet a vállalati internetes horizontális fel- és nem az ExpressRoute-kapcsolaton keresztül. A NAT az ExpressRoute az Azure nyilvános társviszony-létesítés esetén szükséges, azzal a VPN-munkamenet.
>
>

A VPN van beállítva, az nva-n lesz az összes hálózatok és alhálózatok a központi agyhoz. A tűzfalszabályok továbbítási határozza meg, hogy mely engedélyezve, le vannak fordítva NAT-n keresztül, a rendszer átirányítja vagy (akár a helyszíni hálózat és az Azure közötti forgalom) esetében a rendszer elveti.

Adatforgalmi folyamatokat kell alaposan megfontolni is lehet optimalizálni vagy csökken, mert ebben a kialakításban függően az adott használati eset.

A 3. példa a beépített környezet használatával, és a site-to-site VPN hibrid hálózati kapcsolat felvesz hoz létre a következő tervezési:

[![12]][12]

A helyszíni útválasztót, vagy bármely más hálózati eszköz, amely kompatibilis a VPN-hez, az nva-t a VPN-ügyfél lenne. A fizikai eszköz felelős kezdeményezése és karbantartása a VPN-kapcsolatot az nva-t.

Logikailag az nva-t, hogy a hálózati hasonlít négy külön "biztonsági zónákat" a szabályoknak folyamatban van az elsődleges igazgató, ezek a zónák közötti forgalom az nva:

![13]

#### <a name="conclusion"></a>Összegzés
Azure-beli virtuális hálózathoz site-to-site VPN hibrid hálózati kapcsolattal is kiterjesztheti a helyszíni hálózathoz az Azure-bA biztonságos módon. A VPN-kapcsolattal, a forgalom titkosítva van, és továbbítja az interneten keresztül. Ebben a példában az NVA kényszerítése és a biztonsági házirend kezelése a központi helyet biztosít. További információkért tekintse át a részletes build utasításait (szolgáltatnak). Ezek az utasítások tartalmaznak:

* Hogyan hozhat létre a példa peremhálózaton, a PowerShell-parancsfájlok.
* Ebben a példában az Azure Resource Manager-sablonok létrehozásának módját.
* Részletes adatforgalom folyamat-forgatókönyvekhez, megjelenítése a forgalom adatfolyamait Ez a kialakítás keresztül.

### <a name="example-5-add-a-hybrid-connection-with-a-site-to-site-azure-vpn-gateway"></a>5. példa: site-to-site, az Azure VPN gateway a hibrid kapcsolat hozzáadása
[Vissza a gyors kezdési](#fast-start) |} Hamarosan részletes build útmutatás érhető el

[![14]][14]

#### <a name="environment-description"></a>Környezet leírása
Az Azure VPN gateway használatával hibrid hálózatkezelés vagy peremhálózat típusa bemutatott példákban 1 vagy 2 lehet hozzáadni.

Az előző ábrán látható, az interneten (site-to-site) VPN-kapcsolatot egy helyszíni hálózat csatlakoztatása egy Azure virtuális hálózaton keresztül az Azure VPN gateway szolgál.

> [!NOTE]
> Ha az ExpressRoute használata az Azure nyilvános társviszony-létesítés beállítás engedélyezve van, egy statikus útvonalat kell létrehozni. Statikus útvonal kell átirányítása az NVA VPN IP-címet a vállalati internetes horizontális fel- és nem az ExpressRoute WAN keresztül. A NAT az ExpressRoute az Azure nyilvános társviszony-létesítés esetén szükséges, azzal a VPN-munkamenet.
>
>

A következő ábrán a két hálózat széle ebben a példában látható. Az első Edge az nva-t és az NSG-k szabályozhatja a forgalom Azure-on belüli hálózatok és az Azure és az Internet között. A második oldal az Azure VPN-átjáró, amely egy különálló és elkülönített hálózat peremén, a helyszíni és az Azure között.

Adatforgalmi folyamatokat kell alaposan megfontolni is lehet optimalizálni vagy csökken, mert ebben a kialakításban függően az adott használati eset.

Használja a környezetet az 1. példában létrehozott és a site-to-site VPN hibrid hálózati kapcsolat hoz létre a következő tervezési:

[![15]][15]

#### <a name="conclusion"></a>Összegzés
Azure-beli virtuális hálózathoz site-to-site VPN hibrid hálózati kapcsolattal is kiterjesztheti a helyszíni hálózathoz az Azure-bA biztonságos módon. A natív Azure VPN gateway használatával, a forgalom IPSec-titkosítású és az interneten keresztül irányítja. Emellett az Azure VPN gateway használatával biztosíthat egy alacsonyabb költségű lehetőséget (nincs további licencelési költség, mint a külső nva-k). Ez a beállítás akkor leggazdaságosabb példában 1, ahol egyetlen nva-t használják. További információkért tekintse át a részletes build utasításait (szolgáltatnak). Ezek az utasítások tartalmaznak:

* Hogyan hozhat létre a példa peremhálózaton, a PowerShell-parancsfájlok.
* Ebben a példában az Azure Resource Manager-sablonok létrehozásának módját.
* Részletes adatforgalom folyamat-forgatókönyvekhez, megjelenítése a forgalom adatfolyamait Ez a kialakítás keresztül.

### <a name="example-6-add-a-hybrid-connection-with-expressroute"></a>6. példa az expressroute-tal a hibrid kapcsolat hozzáadása
[Vissza a gyors kezdési](#fast-start) |} Hamarosan részletes build útmutatás érhető el

[![16]][16]

#### <a name="environment-description"></a>Környezet leírása
Az ExpressRoute privát társviszony-létesítési kapcsolat segítségével hibrid hálózatkezelés vagy peremhálózat típusa bemutatott példákban 1 vagy 2 lehet hozzáadni.

Az előző ábrán látható, a helyszíni hálózat és az Azure virtuális hálózat között közvetlen kapcsolat ExpressRoute privát társviszony-létesítés biztosít. Forgalom transits csak a szolgáltatás-szolgáltató hálózat és a Microsoft Azure-hálózat, soha nem venné az interneten.

> [!TIP]
> Vállalati hálózati forgalom az ExpressRoute használatával tartja tarthatják az Internettől. Lehetővé teszi a szolgáltatói szerződések az ExpressRoute-szolgáltatótól. Az Azure-átjáró továbbíthatja legfeljebb 10 GB/s, az expressroute-tal, mivel a helyek közötti VPN-eket, az Azure-átjáró maximális átviteli sebesség 200 MB/s.
>
>

Ahogy az alábbi ábrán látható, ezzel a beállítással a környezet most már két hálózati élek. Az nva-t és az NSG-t az átjáró pedig egy külön és elkülönített hálózat peremén, a helyszíni és az Azure közötti szabályozhatja a forgalom Azure-on belüli hálózatok és az Azure és az Internet között.

Adatforgalmi folyamatokat kell alaposan megfontolni is lehet optimalizálni vagy csökken, mert ebben a kialakításban függően az adott használati eset.

Az 1. példa a beépített környezet használatával, és felvesz egy ExpressRoute hibrid hálózati kapcsolat hoz létre a következő tervezési:

[![17]][17]

#### <a name="conclusion"></a>Összegzés
Az ExpressRoute privát társviszony-létesítés kapcsolatot is kiterjesztheti a helyszíni hálózat az Azure-bA a biztonságos, kisebb késés, jobb teljesítményű módon. Emellett a natív Azure-átjáró ebben a példában látható módon (nincs további licencelési csakúgy, mint külső nva-k) alacsonyabb költségű lehetőséget kínál. További információkért tekintse át a részletes build utasításait (szolgáltatnak). Ezek az utasítások tartalmaznak:

* Hogyan hozhat létre a példa peremhálózaton, a PowerShell-parancsfájlok.
* Ebben a példában az Azure Resource Manager-sablonok létrehozásának módját.
* Részletes adatforgalom folyamat-forgatókönyvekhez, megjelenítése a forgalom adatfolyamait Ez a kialakítás keresztül.

## <a name="references"></a>Referencia
### <a name="helpful-websites-and-documentation"></a>Hasznos webhelyek és dokumentáció
* Hozzáférés az Azure az Azure Resource Manager:
* Hozzáférés az Azure PowerShell használatával: [https://docs.microsoft.com/powershell/azureps-cmdlets-docs/](/powershell/azure/overview)
* Virtuális hálózati dokumentációja: [https://docs.microsoft.com/azure/virtual-network/](https://docs.microsoft.com/azure/virtual-network/)
* Hálózati biztonsági csoport dokumentáció: [https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg](virtual-network/security-overview.md)
* Felhasználó által meghatározott útválasztási dokumentációja: [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview](virtual-network/virtual-networks-udr-overview.md)
* Az Azure virtuális átjárók: [https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway/)
* Site-to-Site VPN-eket: [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell](vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* Az ExpressRoute dokumentációja, (tekintse meg az "Első lépések" és "Hogyan az" szakasz lehet): [https://docs.microsoft.com/azure/expressroute/](https://docs.microsoft.com/azure/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "biztonsági beállítások folyamatábrája"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "azure biztonsági funkciók"
[3]: ./media/best-practices-network-security/dmzcorporate.png "A DMZ-t a vállalati hálózat"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "azure biztonsági architektúra"
[5]: ./media/best-practices-network-security/dmzazure.png "egy DMZ-t az Azure-beli virtuális hálózathoz"
[6]: ./media/best-practices-network-security/dmzhybrid.png "három biztonsági határokat a hibrid hálózat"
[7]: ./media/best-practices-network-security/example1design.png "Szegélyhálózat NSG bejövő"
[8]: ./media/best-practices-network-security/example2design.png "DMZ nva-t és az NSG bejövő"
[9]: ./media/best-practices-network-security/example3design.png "kétirányú Szegélyhálózat nva-t, az NSG-t és az udr-t"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "logikai nézetében a tűzfalszabályok"
[11]: ./media/best-practices-network-security/example3designoptions.png "DMZ nva-t a csatlakoztatott hibrid hálózat"
[12]: ./media/best-practices-network-security/example4designs2s.png "Site-to-Site VPN használatával összekapcsolt NVA Szegélyhálózat"
[13]: ./media/best-practices-network-security/example4networklogical.png "NVA szempontból logikai hálózat"
[14]: ./media/best-practices-network-security/example5designoptions.png "szegélyhálózat (DMZ) az Azure-átjáró csatlakoztatott helyek közötti hibrid hálózat"
[15]: ./media/best-practices-network-security/example5designs2s.png "szegélyhálózat (DMZ) az Azure-átjáró Site-to-Site VPN használatával"
[16]: ./media/best-practices-network-security/example6designoptions.png "szegélyhálózat (DMZ) az Azure-átjáró csatlakozik az ExpressRoute hibrid hálózat"
[17]: ./media/best-practices-network-security/example6designexpressroute.png "szegélyhálózat (DMZ) az Azure-átjáró egy ExpressRoute-kapcsolat használatával"

<!--Link References-->
[TrustCenter]: https://azure.microsoft.com/support/trust-center/compliance/
[Example1]: ./virtual-network/virtual-networks-dmz-nsg.md
[Example2]: ./virtual-network/virtual-networks-dmz-nsg-fw-asm.md
[Example3]: ./virtual-network/virtual-networks-dmz-nsg-fw-udr-asm.md
[Example4]: ./virtual-network/virtual-networks-hybrid-s2s-nva-asm.md
[Example5]: ./virtual-network/virtual-networks-hybrid-s2s-agw-asm.md
[Example6]: ./virtual-network/virtual-networks-hybrid-expressroute-asm.md
[Example7]: ./virtual-network/virtual-networks-vnet2vnet-direct-asm.md
[Example8]: ./virtual-network/virtual-networks-vnet2vnet-transit-asm.md
