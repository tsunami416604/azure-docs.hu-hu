---
title: Azure-hálózat ajánlott biztonsági eljárások |} Microsoft Docs
description: Ismerje meg, néhány biztonságos hálózati környezetben létrehozásához Azure-ban elérhető a legfontosabb jellemzők
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
ms.openlocfilehash: cf015f4857a22b755813d0be1af5a55a8b7b6535
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="microsoft-cloud-services-and-network-security"></a>A Microsoft cloud services és a hálózati biztonság
A Microsoft cloud services csomag kézbesítése kapacitású szolgáltatások és az infrastruktúra, a vállalati szintű képességet és a számos választhat való hibrid kapcsolathoz. Az ügyfelek ezeket a szolgáltatásokat, vagy az interneten keresztül, vagy Azure ExpressRoute, amely kapcsolattal rendelkező eléréséhez választhat. A Microsoft Azure platform lehetővé teszi, hogy az ügyfelek zökkenőmentesen bővítheti az infrastruktúrát kiterjeszti a felhőbe, és hozhat létre többrétegű architektúrák számára. Emellett harmadik felek engedélyezéséhez bővített szolgáltatásokat nyújtó biztonsági szolgáltatásait és a virtuális készülékek. Ez a dokumentum áttekintést nyújt biztonsági és architekturális problémák, az ügyfelek figyelembe kell venni a Microsoft más felhőszolgáltatásaival ExpressRoute keresztül elért használatakor. Azt is magában foglalja biztonságosabb szolgáltatások létrehozása az Azure virtuális hálózatot.

## <a name="fast-start"></a>Gyors indítás
A következő logikai diagram, hogy az Azure platformon rendelkezésre álló számos biztonsági módszereket adott példát utasíthatja. Gyors referenciaként a leginkább az esethez megfelelő példa található. Bővített ismereteket szeretnének elsajátítani folytassa a keresztül a dokumentum olvasásakor.
[![0]][0]

[1. példa: Build a szegélyhálózaton (más néven DMZ, demilitarizált zóna vagy demilitarizált) hálózati biztonsági csoportokkal (NSG-k) alkalmazások védelme érdekében.](#example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs)</br>
[2. példa: Build a szegélyhálózaton a tűzfal és az NSG-k alkalmazások védelme érdekében.](#example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs)</br>
[3. példa: Build a szegélyhálózaton hálózatokat és a tűzfalon, felhasználó által megadott útvonal (UDR) és NSG védelme érdekében.](#example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg)</br>
[4. példa: Egy hibrid kapcsolat-helyek, virtuális berendezés virtuális magánhálózat (VPN) hozzáadása.](#example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn)</br>
[5. példa: Egy pont-pont, Azure VPN-átjáró hibrid kapcsolat hozzáadása.](#example-5-add-a-hybrid-connection-with-a-site-to-site-azure-vpn-gateway)</br>
[6. példa: ExpressRoute hibrid kapcsolat hozzáadása.](#example-6-add-a-hybrid-connection-with-expressroute)</br>
Például a virtuális hálózatok, a magas rendelkezésre állás és a szolgáltatás láncolás közötti kapcsolatok hozzáadása a dokumentumhoz való bekerül a következő néhány hónapon keresztül.

## <a name="microsoft-compliance-and-infrastructure-protection"></a>Microsoft megfelelőségi és infrastruktúra védelme
Segítségével a szervezetek gyűjtésére és felhasználására egyének adatok irányadó nemzeti regionális és iparág-specifikus követelmények ahhoz, hogy a Microsoft több mint 40 tanúsítványokról és tanúsítványok kínál. A legtöbb választékát bármely felhőbeli szolgáltatás szolgáltatója.

További információkért lásd a megfelelőségi információkat a [Microsoft Trust Center][TrustCenter].

A Microsoftnál egy átfogó megközelítés kapacitású globális szolgáltatások futtatásához szükséges felhő-infrastruktúra védelméhez. A Microsoft felhőalapú infrastruktúra tartalmazza a hardverek, szoftverek, hálózatok, és a felügyeleti és a dolgozók, a fizikai adatközpontok mellett.

![2]

Ezt a módszert használja az ügyfelek számára a Microsoft felhőalapú szolgáltatások telepítése több biztonságos alapot nyújt. A következő lépésre van az ügyfelek tervezése, és hozzon létre egy biztonsági architektúrája védelme érdekében ezeket a szolgáltatásokat.

## <a name="traditional-security-architectures-and-perimeter-networks"></a>Hagyományos hálózatbiztonsági architektúrák és szegélyhálózat
Bár Microsoft fokozottan fektet a felhőalapú infrastruktúra védelme, ügyfelek is kell védeni a felhőszolgáltatások és erőforrás-csoportok. A többrétegű megközelítést a legjobb védelmet nyújt. A külső hálózati biztonsági szintű zónában védi a belső hálózati erőforrásokhoz a nem megbízható hálózaton. A szegélyhálózaton a szélén vagy a hálózat részei, amelyek az internetről és a védett vállalati informatikai infrastruktúra között elhelyezkedő hivatkozik.

A vállalati hálózatokban az alapvető infrastruktúra fokozottan dúsítjuk a kialakítását rétege biztonsági eszközök a következő. A határ az egyes rétegek eszközök és a házirend-érvényesítési pontok áll. Minden egyes réteg a következő hálózati biztonsági eszközök a kombinációját tartalmazhatja: tűzfalak, szolgáltatásmegtagadásos (DoS) megelőzése, behatolásérzékelési vagy (Azonosítók/IP-CÍMEK) rendszereket és VPN-eszközök. Házirend betartatása tűzfal házirendek, a hozzáférés-vezérlési listák (ACL) vagy a megadott útválasztási is igénybe vehet. Az első védelmi vonalat a hálózaton, közvetlenül a bejövő forgalom az internetről fogad, ezek a mechanizmusok blokk támadások és káros forgalom kombinációját, miközben lehetővé teszi további jogos kéréseket a hálózatra. Ez az adatforgalom közvetlenül a szegélyhálózaton lévő erőforrások irányítja. Adott erőforrás majd "beszél" erőforrásokhoz a hálózat, a következő határt érvényesítéshez áthaladó mélyebb első. A legkülső réteg hívják a peremhálózaton, mert ezen része a hálózati van közzétéve az interneten, általában valamilyen védelmi mindkét oldalon. Az alábbi ábra a vállalati hálózat, a két biztonsági határokat egyetlen alhálózaton szegélyhálózaton példáját mutatja be.

![3]

Nincsenek a szegélyhálózaton végrehajtásához használt számos architektúra. Ezek architektúrák egyszerű terheléselosztó és közöttiek több alhálózattal szegélyhálózaton, mindegyik kiválasztott határ való kommunikációt blokkoló változatos mechanizmusokkal, és a vállalati hálózat mélyebb rétegek védelmét. Hogyan épül fel a peremhálózaton attól függ, hogy a szervezet és annak teljes kockázattűrése igényeinek.

Mivel az ügyfelek helyezhetik át a munkaterheléseket nyilvános felhők, fontos hasonló lehetőségeket nyújtanak a külső hálózati architektúra megfelelőségi és biztonsági követelmények teljesítéséhez az Azure-ban. Ez a dokumentum hasznos útmutatást ad meg, hogyan hozhat létre az ügyfelek a biztonságos hálózati környezet az Azure-ban. A szegélyhálózat állomásneveit összpontosít, de is magában foglalja a hálózati biztonsági sok szempontból átfogó leírást. A következő kérdéseket ismertető tájékoztatja:

* Hogyan építhető is a szegélyhálózaton, az Azure-ban?
* Melyek azok a peremhálózaton létrehozásához Azure szolgáltatásához?
* Hogyan háttér-munkaterhelések védelme?
* Hogyan szabályozza a munkaterhelések az Azure-ban internetes kommunikáció?
* Hogyan lehet a helyszíni hálózatokhoz védve legyen a központi telepítések az Azure-ban?
* Ha natív Azure biztonsági funkciók használ, és a külső eszközöknek és a szolgáltatások?

Az alábbi ábrán látható a különböző biztonsági réteg alkalmazása Azure biztosít az ügyfél számára. Ezek a rétegek az Azure platformon, maga a natív és a felhasználó által meghatározott funkciókat:

![4]

Bejövő az internetről, Azure DDoS szemben Azure nagyméretű támadások elleni védelmét. A következő réteg az ügyfél által megadott nyilvános IP-címek (végpont), amelyek segítségével meghatározhatja, melyik forgalmat is továbbítása a felhőszolgáltatás a virtuális hálózathoz. Natív Azure virtuális hálózatelkülönítés biztosítja minden más hálózati teljes elkülönítést és, hogy csak a forgalom metódusok és konfigurált felhasználói elérési útját. Ezek elérési útja és módszerek a következő réteg esetében, ahol az NSG-k, UDR és virtuális hálózati berendezések segítségével az alkalmazások központi telepítéseit a védett hálózat védelme érdekében biztonsági határokat hozzon létre.

A következő szakaszban az Azure virtuális hálózatok áttekintése. Ezek a virtuális hálózatok az ügyfelek által létrehozott, és mi az üzembe helyezett munkaterhelések csatlakozik. Virtuális hálózat minden hálózati biztonsági szolgáltatás szükséges a szegélyhálózaton védelméhez az Azure-ban felhasználói telepítés létrehozásához alapját képezi.

## <a name="overview-of-azure-virtual-networks"></a>Az Azure virtuális hálózatok áttekintése
Internetes forgalmat el tudja érni az Azure virtuális hálózataihoz, mielőtt az alábbi két réteg biztonsági rejlő az Azure platformra:

1.    **DDoS-védelem**: DDoS-védelem az Azure-beli fizikai hálózat védelmet nyújt az Azure platformon, maga a nagyméretű Internet-alapú támadások réteget. Ilyen támadások próbál több "bot" csomópont használatával ne terhelje tovább egy internetes szolgáltatást. Azure rendelkezik egy robusztus DDoS védelem háló összes bemeneti, kimeneti és kereszt-Azure régió kapcsolata. DDoS védelem réteg nincsenek felhasználói konfigurálható attribútumai, és nem érhető el az ügyfelének. A DDoS védelmi réteget Azure védi a nagyméretű támadások platformként, azt is figyeli a kimenő kötött és a kereszt-Azure azon régióját forgalom. Hálózati virtuális készülékek használata a virtuális hálózaton, rugalmasság további rétegei konfigurálhatja az ügyfél a kisebb méretezési támadás, amely nem a platform szintű védelmet trip ellen. Példa DDoS működés közben; Ha az internet felé néző IP-cím által a felügyeleti teendők központjaként DDoS-támadások támadásnak kitett erőforrásra, Azure ehhez a források a támadások észlelésére és megtisztítás a hibát okozó forgalmat, mielőtt elérte a kívánt rendeltetési. Szinte minden esetben a megtámadott végpont nincs hatással a támadás. Ritka esetekben, hogy a végpont kihatással van a sincs forgalom végpontja, csak a megtámadott végpont van hatással. Így más ügyfelek és a szolgáltatások jelenik meg, hogy a támadások elleni nincs hatással. Fontos megjegyezni, hogy Azure DDoS csak a felügyeleti teendők központjaként támadások keresi. Akkor lehet, hogy az adott szolgáltatás sikerült túlterhelik, mielőtt a platform szintű védelmet küszöbérték túllépése. Például egy webhely egy A0 IIS kiszolgálón sikerült offline állapotra állítja a DDoS-támadások által előtt. az Azure platform szint DDoS-védelem fenyegetést.

2.  **Nyilvános IP-címek**: nyilvános IP-cím (Szolgáltatásvégpontok, nyilvános IP-cím címek, Alkalmazásátjáró és más Azure-funkció, amely az Internethez csatlakoznak az erőforrás egy nyilvános IP-cím jelenleg engedélyezett) címek engedélyezése felhőszolgáltatások vagy erőforrás csoportok nyilvános Internet IP-cím és port legyen megnyitva. A végpont a forgalom irányításához a belső cím és port az Azure virtuális hálózat hálózati címfordítás (NAT) használ. Ez az elérési út külső adatforgalmát adja át a virtuális hálózat az elsődleges módszer. A nyilvános IP-címek esetében konfigurálhatók, mely forgalom átadott, és hol és hogyan lehet virtuális hálózati lefordított meghatározásához.

Ha forgalom elérte a virtuális hálózat, vannak, amelyek play számos szolgáltatást. Egy Azure virtuális hálózatot az ügyfelek számára a munkaterhelések csatolja, ahol az alapvető hálózati szintű biztonsági alkalmazza foundation rendszer. A következő funkciók és jellemzőkkel rendelkező ügyfelek esetén az Azure-ban egy magánhálózathoz (egy virtuális hálózati területre):

* **A forgalom elkülönítése**: egy virtuális hálózathoz a forgalom elkülönítési határt alkotnak, az Azure platformon. Virtuális gépek (VM) több virtuális hálózat nem kommunikálnak közvetlenül a másik virtuális hálózatot, virtuális gépek akkor is, ha mindkét virtuális hálózat az azonos ügyfél jönnek létre. Elkülönítési kritikus tulajdonság, amely biztosítja a felhasználói virtuális gépeket, kommunikációs titkos virtuális hálózaton belül marad.

>[!NOTE]
>Adatforgalom-elkülönítést csak hivatkozik forgalom *bejövő* a virtuális hálózathoz. Alapértelmezés szerint a vnet kimenő forgalom az internethez engedélyezett, de igény szerint NSG-ket megakadályozása.
>
>

* **Többrétegű topológia**: virtuális hálózatok lehetővé teszi az ügyfeleknek alhálózatok hozzárendelésének és különböző elemek vagy a munkaterhelések "réteg" külön címterek kijelölő többrétegű topológia meghatározása. Ezek logikai csoportok topológiák engedélyezése az ügyfelek számára a különböző alkalmazások és szolgáltatások alapján házirend meghatározása és a rétegek közötti forgalmat is szabályozhatja.
* **Közötti kapcsolatot nyújthassanak**: az ügyfelek közötti kapcsolatot nyújthassanak virtuális hálózat és több helyszíni hely vagy más virtuális hálózatok közötti hozhatnak létre az Azure-ban. A kapcsolat létrehozásához ügyfelek használhatják a Vnetben társviszony-létesítést, Azure VPN Gatewayek, külső hálózat virtuális készülékek vagy ExpressRoute. Azure-helyek (közötti S2S) VPN szabványos IPsec/IKE protokoll és a saját ExpressRoute-kapcsolat használatával támogatja.
* **NSG** lehetővé teszi az ügyfelek szabályok (ACL) létrehozása a kívánt szinten engedélyeknek: hálózati felületek, az egyes virtuális gépeken vagy a virtuális alhálózatok. Az ügyfelek is hozzáférést lehetővé tevő vagy elutasítja a futó alkalmazások és szolgáltatások a virtuális hálózaton, az ügyfél hálózatokon keresztül közötti kapcsolatot nyújthassanak, rendszerek közötti kommunikáció vagy közvetlen Internet-kommunikációt.
* **UDR** és **IP-továbbítás** lehetővé teszi az ügyfeleknek a virtuális hálózaton belül különböző rétegek közötti kommunikációs útvonala meghatározásához. Ügyfelek központi telepítése egy tűzfal, Azonosítók/IP-CÍMEK és más virtuális készülékek, és irányítható a hálózati forgalom keresztül e biztonsági készülékek biztonsági határ házirendek betartatását, a naplózás és a hálózatfelügyeleti.
* **Virtuális készülékekre** az Azure piactéren: biztonsági készülékek, mint például tűzfalak, terheléselosztók, és Azonosítók/IP-CÍMEK találhatók az Azure piactéren, és a VM-lemezkép gyűjteménye. Az ügyfelek e készülékek a virtuális hálózatba szervezheti, vagy kifejezetten, a biztonsági határokat (beleértve a külső hálózati alhálózatok) telepíthetnek egy többrétegű biztonságos hálózati környezetben befejezéséhez.

Ezen funkciók és képességek például olyan hogyan egy külső hálózati architektúra sikerült létrehozni az Azure-ban a következő ábra:

![5]

## <a name="perimeter-network-characteristics-and-requirements"></a>Külső hálózati tulajdonságok és követelmények
A szegélyhálózaton a hálózat közvetlenül kapcsolatba, az internetről érkező kommunikációt az az előtérbeli rendszer. A bejövő csomagok biztonsági készülékek, például a tűzfal, Azonosítók és IP-CÍMEK, a háttér-kiszolgálók elérése előtt kell haladni. Internet adathoz kötött a munkaterhelések is adatcsomagok keresztül a biztonsági készülékek a szegélyhálózaton a házirendek betartatását, vizsgálati és naplózási célokra, mielőtt elhagynák a hálózaton. Emellett a peremhálózaton tárolhatja, létesítmények közötti VPN-átjárók ügyfél virtuális hálózatokat és a helyszíni hálózat között.

### <a name="perimeter-network-characteristics"></a>Külső hálózati jellemzői
Az előző ábra hivatkozik, néhány jó szegélyhálózaton jellemzői a következők:

* Az Internet felé néző:
  * A külső hálózati alhálózatról internetre irányuló, közvetlenül kommunikál az interneten.
  * Nyilvános IP-címek, a virtuális IP-címek és/vagy a végpontok át internetes forgalmat az előtér-hálózati és az eszközöket.
  * Az internetről érkező bejövő forgalom áthalad előtér-hálózati biztonsági eszközök előtt más erőforrások.
  * Kimenő biztonsági engedélyezve van, ha forgalmat haladnak keresztül biztonsági eszközöket, az utolsó lépést, az interneten előtt.
* Védett hálózati:
  * Nincs az alapvető infrastruktúra számára az internetről közvetlen útvonal.
  * Az alapvető infrastruktúra csatornákat kell haladnak át a biztonsági eszközök, például az NSG-k, a tűzfalakon és a VPN-eszközök.
  * Más eszközök nem hidat képeznek, internetes és az alapvető infrastruktúra.
  * Biztonsági eszközök, az internetre irányuló mind a védett hálózati irányuló a szegélyhálózaton (például a két tűzfal ikonok az előző ábrán látható) határain ténylegesen lehet, hogy egy egyetlen virtuális készülék eltérő szabályok vagy felületekkel minden határ. Például egy fizikai eszköz, logikailag el, a peremhálózat mindkét korlátai terhelés kezelésére.
* Egyéb gyakori eljárások és korlátozások:
  * Munkaterhelések nem kell tárolnia a fontos üzleti adatok.
  * Csak akkor engedélyezett, ha a rendszergazdák hozzáférés és a külső hálózati és központi frissítésekről korlátozódnak.

### <a name="perimeter-network-requirements"></a>Külső hálózati követelmények
Ahhoz, hogy a következő jellemzőkkel, kövesse ezeket az irányelveket szegélyhálózaton sikeres végrehajtásához a virtuális hálózati követelmények:

* **Alhálózati architektúra:** adja meg a virtuális hálózati úgy, hogy egy teljes alhálózat van kijelölve, a szegélyhálózat különíteni az azonos virtuális hálózatban lévő más alhálózatokon. Ez az elkülönítés biztosítja a forgalmat a szegélyhálózat és egyéb belső vagy privát alhálózati rétegek folyamatok tűzfalon vagy virtuális készülék Azonosítók/IP-CÍMEK között.  Felhasználó által definiált útvonalak a határ alhálózaton van szükség a forgalom virtuális készülékre továbbítja.
* **NSG:** a külső hálózati alhálózatról engedélyezi a kommunikációt az internetes nyitva kell lennie, de, amely nem jelenti azt, az ügyfelek kell kihagyásával NSG-ket. Kövesse az általános biztonsági eljárásokat a minimalizálása érdekében a hálózati felületek kommunikál az internettel. A távoli címtartományokat hozzáférhetnek a központi telepítéseket vagy adott alkalmazás protokollok és portok, megnyitott zárolását. Előfordulhat, hogy olyan körülmények között, azonban a teljes zár ki nincs lehetőség. Például ha a vevők külső webhelyek az Azure-ban, a peremhálózati lehetővé kell tennie a beérkező webes kérelmek bármely nyilvános IP-címekről, de csak meg kell nyitnia a webes alkalmazás portok: TCP 80-as porton és/vagy a TCP 443-as porton.
* **Útvonaltábla:** a külső hálózati alhálózatról közvetlenül kommunikálnak az interneten képesnek kell lennie, de ne engedélyezze, hogy közvetlen kommunikációt a hátsó vége vagy a helyszíni hálózatokon keresztül egy tűzfal vagy a biztonsági készüléket.
* **Biztonsági készülékek konfigurációs:** történő átirányításához és vizsgálja meg a szegélyhálózat és a védett részeinek között csomagok hálózatok, például a tűzfal, Azonosítók, a biztonsági készülékek és IP-CÍMEK eszközök többhelyű lehet. A peremhálózat és a háttér-alhálózatok elkülönülő hálózati adaptereket is rendelkeznek. A hálózati adapterek, a peremhálózati közvetlenül az internetről érkező vagy, a megfelelő NSG-ket és a külső hálózati útvonaltábla kommunikációhoz. A hálózati adapter csatlakozik a háttér-alhálózatok több korlátozta NSG-ket és a megfelelő háttér-alhálózatok útválasztási táblákat.
* **Biztonsági átjárófunkció:** üzembe helyezve a szegélyhálózaton általában biztonsági készülékek, hajtsa végre az alábbi funkciókat:
  * Tűzfal: Kényszerítése, tűzfal- és hozzáférés-vezérlési házirendeket a bejövő kéréseket.
  * A fenyegetés felderítésére és megelőzésére: észlelésére, és az internetről származó rosszindulatú támadások orvoslása.
  * A vizsgálati és naplózási: részletes naplózási vizsgálati és elemzési karbantartása.
  * Fordított proxy: a bejövő kérelmek átirányítása a megfelelő háttér-kiszolgálókon. Az átirányítást magában foglalja a leképezést, és az előtér-eszközökön, a cél-címek fordítása általában tűzfalak, a háttér-kiszolgálófiók címekre.
  * Proxy továbbítsa: NAT biztosítása, és az internethez a virtuális hálózaton belül kezdeményezett kommunikáció naplózásának végrehajtása.
  * Útválasztó: Továbbítja a bejövő és a döntést az alhálózatok közötti forgalmat a virtuális hálózaton belül.
  * VPN-eszközön: a létesítmények közötti VPN-kapcsolat a helyszíni ügyfélhálózatok és az Azure virtuális hálózatok közötti a létesítmények közötti VPN-átjáróként működik.
  * VPN-kiszolgáló: az Azure virtuális hálózatokhoz való csatlakozás VPN-ügyfelek elfogadásával.

> [!TIP]
> A következő két csoporthoz elkülönítése: a külső hálózati biztonsági fogaskerék és az egyéni felhasználók számára engedélyezett alkalmazások fejlesztési, a telepítés vagy a műveletek rendszergazdaként elérésére jogosult személyek. Mivel külön kezeljük ezeket a csoportokat lehetővé teszi, hogy a feladatok elkülönítése, és megakadályozza, hogy egy személy kihagyásával alkalmazások biztonsági és a hálózati biztonsági vezérlők.
>
>

### <a name="questions-to-be-asked-when-building-network-boundaries"></a>Hálózati határok fejlesztéskor megkéri kérdések
Ebben a szakaszban említett, a "hálózat" kifejezés Azure virtuális magánhálózatok előfizetés rendszergazdák által létrehozott. A kifejezés nem hivatkozik a mögöttes fizikai hálózatok Azure-ban.

Emellett egy Azure virtuális hálózatot gyakran bővítésére szolgálnak olyan hagyományos helyszíni hálózatokhoz. Pont-pont vagy ExpressRoute hibrid hálózatkezelési megoldások szegélyhálózati hálózati architektúra ment keresztül, és lehetőség. A hibrid kapcsolat a hálózati biztonsági határokat felépítése fontos szempont.

A következő három kérdést szempontból kulcsfontosságúként válaszolja meg a szegélyhálózaton és több biztonsági határon tartalmazó fejlesztéskor van.

#### <a name="1-how-many-boundaries-are-needed"></a>1.) hány határok van szükség?
Az első döntési tényező döntse el, hogy hány biztonsági határokat egy adott forgatókönyv esetén van szükség:

* Egyetlen határvonal: egyet az előtér-peremhálózaton, a virtuális hálózat és az Internet között.
* Két határok: egy internetes oldalán a peremhálózaton, és egy másik a szegélyhálózat alhálózaton és a háttér-alhálózat az Azure virtuális hálózatok között.
* Három határok: egy Internet oldalán a szegélyhálózaton található, és az egyik a peremhálózat és a háttér-alhálózatok között, egy pedig a háttér-alhálózatok és a helyszíni hálózat között.
* N határok: egy változó száma. Biztonsági követelményeitől függően egy adott hálózati alkalmazható biztonsági határokat száma nincs korlátozva van.

Számát és típusát a határok szükséges eltérők lehetnek a vállalat kockázattűrése és az adott forgatókönyv megvalósítása alapján. Ehhez a döntéshez gyakran történik a szervezeten belüli gyakran többek között a kockázat és megfelelőségi csoport, a hálózati és platform csoport és egy alkalmazást fejlesztői csapat több csoport együtt. Biztonsági, az érintett adatok és a használt technológiákat ismerete élők kell rendelkezniük a szóbeli ez döntés a megfelelő biztonsági hatékonyabb minden megvalósítása a védelem biztosításához.

> [!TIP]
> Használja a legkevesebb határokat, amelyek megfelelnek egy adott helyzetben a biztonsági követelményeinek. A további határokat, műveletek és a hibaelhárítási telepíthetők nehezebb, valamint a kezelési terhet a szerepet játszó adott idő alatt a több határ házirendek kezelése. Azonban elegendő határok növeljék. Az egyenleg keresése fontos.
>
>

![6]

A fenti ábrán egy három biztonsági határ hálózati vázlatos elrendezése látható. A határok vannak, a peremhálózat és az internethez, Azure előtér- és titkos alhálózatait, és az Azure háttér-alhálózat és a helyszíni vállalati hálózat között.

#### <a name="2-where-are-the-boundaries-located"></a>2.) a határok helyét?
A határok száma határoz meg, ha where megvalósításukhoz van a következő döntési tényező. Nincsenek általában három választási lehetőségek:

* Az Internet alapú közbenső szolgáltatási (például egy felhőalapú webalkalmazási tűzfal, amely nem a dokumentumban ismertetett) használatával
* Natív szolgáltatások és/vagy a virtuális hálózati készülékek használata Azure-ban
* A helyszíni hálózat fizikai eszközök használatával

Csak Azure-hálózatok a beállítások olyan natív Azure-szolgáltatások (például a Azure Load Balancer Terheléselosztók) és a hálózati virtuális készülékekre, a gazdag partner ökoszisztéma Azure (például Check Point tűzfalak).

A határ Azure és a helyszíni hálózat között van szükség, ha a biztonsági eszközök találhatók, vagy a kapcsolat (vagy mindkét oldalon) oldalán. Így döntést kell végezni a hely biztonsági fogaskerék helyezhető el.

Az előző ábrán az Internet-peremhálózathoz és az első biztonsági-közötti határokat teljesen található Azure-ban, és kell natív Azure-szolgáltatások vagy a hálózati virtuális készülékek. Biztonsági eszközök Azure (háttér-alhálózat) és a vállalati hálózat között határán vagy Azure oldalán, vagy a helyszíni oldalon, vagy akár mindkét oldalon eszközök kombinációja lehet. Jelentős előnyei és hátrányai súlyosan figyelembe veendő bármelyiket is lehet.

Például használatával a meglévő fizikai biztonsági eszközökön a helyszíni hálózati oldalon azzal az előnnyel jár, hogy szükség van-e az új állapotban. Csak az újrakonfigurálás van szüksége. A, azonban hátránya, hogy az összes forgalom kell térjen vissza az Azure-ból a helyszíni hálózat számára a biztonsági fogaskerék láthatók. Így fel Önnek Azure-Azure forgalom sikerült jelentős késés, és ronthatja az alkalmazások teljesítményének és a felhasználói élmény, ha vissza a biztonsági házirendek betartását a helyszíni hálózat kénytelen volt.

#### <a name="3-how-are-the-boundaries-implemented"></a>3.) hogyan vannak megvalósítva a határok?
Minden biztonsági határ valószínűleg kell különböző képességekre vonatkozó követelmények (például Azonosítók és a peremhálózaton, de csak hozzáférés-vezérlési listákat a szegélyhálózat és a háttér-alhálózat között Internet oldalán tűzfalszabályok). Annak eldöntése, amelyen eszköz (vagy hogy hány eszköz) használata a forgatókönyv és a biztonsági követelményeket függ. A következő szakaszban például 1, 2 és 3 bizonyos beállítások, amelyek felhasználhatók tárgyalja. A natív Azure hálózati funkciókat és az eszközök, az Azure-ban elérhető a partner ökoszisztémájának áttekintése látható szinte bármilyen forgatókönyv megoldására tárfiókokhoz lehetőségeit.

Kulcs megvalósítási dönteni, hogyan csatlakozzon a helyszíni hálózat az Azure-ral. Használja az Azure virtuális átjáró vagy a hálózati virtuális készülék? Ezek a beállítások ismertetése részletesebben a következő szakaszban (például 4, 5 és 6).

Emellett az Azure virtuális hálózatok közötti forgalom lehet szükség. Ezek a forgatókönyvek a későbbiekben lesz hozzáadva.

Ha az előző kérdésre választ kapott a [gyorsindítási](#fast-start) szakasz segítségével azonosítható, amelyek többek között a legmegfelelőbb az adott forgatókönyvnek.

## <a name="examples-building-security-boundaries-with-azure-virtual-networks"></a>Példák: Épület biztonsági határokat Azure virtuális hálózatokat
### <a name="example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs"></a>1. példa az NSG-ket alkalmazások védelme érdekében szegélyhálózaton összeállítása
[Vissza a gyors kezdési](#fast-start) | [részletes utasításokat ehhez a példához összeállítása][Example1]

[![7]][7]

#### <a name="environment-description"></a>Környezet leírása
Ebben a példában nincs olyan előfizetést, amelyet a következőket tartalmazza:

- Egyetlen erőforráscsoportként működnek
- Két alhálózat virtuális hálózat: "Előtér" és "Háttér"
- Mindkét alhálózat alkalmazott hálózati biztonsági csoport
- A Windows server egy webalkalmazás-kiszolgáló ("IIS01") jelölő
- Két Windows-kiszolgálók, amelyek megfelelnek az alkalmazás háttérkiszolgálók ("AppVM01", "AppVM02")
- A Windows server DNS-kiszolgáló ("DNS01") jelölő
- A társított a webalkalmazás-kiszolgáló nyilvános IP-cím

Parancsfájlok és az Azure Resource Manager-sablon, tekintse meg a [részletes utasításokat build][Example1].

#### <a name="nsg-description"></a>NSG leírása
Ebben a példában egy NSG-csoport összeállítása és hat szabályokkal majd betölteni.

> [!TIP]
> Általánosságban véve kell szabályokat kell létrehozni a megadott "Engedélyezése" először általánosabbá "Deny" szabályokat követ. A prioritáshoz megadott határozzák meg, hogy mely szabályokat értékeli ki a rendszer először. Forgalom kiderül, hogy egy adott szabály vonatkozik, ha nincsenek további szabályok kiértékelése. NSG-szabályok (szempontjából az alhálózat) bejövő vagy kimenő irányban is alkalmazhat.
>
>

Deklaratív módon a bejövő forgalom beépített folyamatban a következő szabályokat:

1. Belső DNS-forgalom (53-as port) engedélyezett.
2. RDP-forgalmát (3389-es port) az internetről bármely virtuális géphez engedélyezett.
3. HTTP-forgalom (80-as port) az internetről (IIS01) webkiszolgálón engedélyezett.
4. Minden forgalmat (minden porthoz) IIS01 AppVM1 engedélyezett.
5. Az összes bejövő forgalom (minden porthoz) az internetről a teljes virtuális hálózatot (alhálózatok mindkét) megtagadva.
6. Az összes bejövő forgalom (minden porthoz) az előtér-alhálózatból a háttér-alhálózat megtagadva.

A következő szabályok kötve alhálózatok, ha a webkiszolgálónak, a szabályokat is 3 az internetről bejövő HTTP-kérelem (engedélyezése) és 5 (megtagadni) szeretné alkalmazni. De %3 szabályt egy magasabb prioritással bír, mert csak akkor vonatkozik, és 5 szabály nem lesz play kerülhet. Így a HTTP-kérelem szeretné engedélyezni kell a webkiszolgálón. Ha ugyanaz a forgalom próbál elérni az DNS01 kiszolgálót, a szabályok 5 (megtagadni) lenne, az első alkalmazásához, és szeretné, hogy a kiszolgáló nem engedélyezett a forgalmat. 6. szabály (megtagadni) az előtér-alhálózathoz van szó, a háttér-alhálózathoz (kivéve a engedélyezett forgalom szabályokban 1 és 4) a blokkolja. A szabálykészlet védi a háttér-hálózatot, abban az esetben, ha egy támadó gyengíti a webes alkalmazás megszüntetni. A támadó volna csak korlátozott hozzáféréssel rendelkeznek a háttér-"védett" hálózatra (csak a AppVM01 kiszolgálón kitett erőforrások).

Nincs olyan alapértelmezett kimenő szabály, amely lehetővé teszi, hogy a kimenő forgalom az internethez. Ebben a példában a Microsoft most átengedi a kimenő forgalmat, és nem módosítja az egyetlen kimenő szabályok. Mindkét irányú forgalmát zárolását, felhasználó által definiált útválasztási szükség (lásd a 3. példa:).

#### <a name="conclusion"></a>Összegzés
Ez a példa módja a viszonylag egyszerű és magától értetődő, a háttér-alhálózathoz, a bejövő forgalom elkülönítésére. További információkért lásd: a [részletes utasításokat build][Example1]. Ezek az utasítások tartalmaznak:

* Hogyan hozhat létre a klasszikus PowerShell-parancsfájlok a szegélyhálózaton.
* Megtudhatja, hogyan hozhat létre egy Azure Resource Manager sablonnal a szegélyhálózaton.
* Minden NSG parancs részletes leírását.
* Részletes forgalom folyamata forgatókönyvek, hogyan forgalom engedélyezett vagy megtagadott a két réteg megjelenítése.


### <a name="example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs"></a>2. példa a szegélyhálózaton segítségével védheti az alkalmazások a tűzfal és az NSG-k létrehozása
[Vissza a gyors kezdési](#fast-start) | [részletes utasításokat ehhez a példához összeállítása][Example2]

[![8]][8]

#### <a name="environment-description"></a>Környezet leírása
Ebben a példában nincs olyan előfizetést, amelyet a következőket tartalmazza:

* Egyetlen erőforráscsoportként működnek
* Két alhálózat virtuális hálózat: "Előtér" és "Háttér"
* Mindkét alhálózat alkalmazott hálózati biztonsági csoport
* Ebben az esetben a tűzfal, a hálózati virtuális készülék csatlakoznak az előtér-alhálózat
* A Windows server egy webalkalmazás-kiszolgáló ("IIS01") jelölő
* Két Windows-kiszolgálók, amelyek megfelelnek az alkalmazás háttérkiszolgálók ("AppVM01", "AppVM02")
* A Windows server DNS-kiszolgáló ("DNS01") jelölő

Parancsfájlok és az Azure Resource Manager-sablon, tekintse meg a [részletes utasításokat build][Example2].

#### <a name="nsg-description"></a>NSG leírása
Ebben a példában egy NSG-csoport összeállítása és hat szabályokkal majd betölteni.

> [!TIP]
> Általánosságban véve kell szabályokat kell létrehozni a megadott "Engedélyezése" először általánosabbá "Deny" szabályokat követ. A prioritáshoz megadott határozzák meg, hogy mely szabályokat értékeli ki a rendszer először. Forgalom kiderül, hogy egy adott szabály vonatkozik, ha nincsenek további szabályok kiértékelése. NSG-szabályok (szempontjából az alhálózat) bejövő vagy kimenő irányban is alkalmazhat.
>
>

Deklaratív módon a bejövő forgalom beépített folyamatban a következő szabályokat:

1. Belső DNS-forgalom (53-as port) engedélyezett.
2. RDP-forgalmát (3389-es port) az internetről bármely virtuális géphez engedélyezett.
3. A hálózati virtuális készülék (tűzfal) bármely internetes forgalmat (minden porthoz) engedélyezett.
4. Minden forgalmat (minden porthoz) IIS01 AppVM1 engedélyezett.
5. Az összes bejövő forgalom (minden porthoz) az internetről a teljes virtuális hálózatot (alhálózatok mindkét) megtagadva.
6. Az összes bejövő forgalom (minden porthoz) az előtér-alhálózatból a háttér-alhálózat megtagadva.

A következő szabályok kötött alhálózatok, ha a tűzfalhoz, 3 szabályokat is az internetről bejövő HTTP-kérelem (engedélyezése) és 5 (megtagadni) szeretné alkalmazni. De %3 szabályt egy magasabb prioritással bír, mert csak akkor vonatkozik, és 5 szabály nem lesz play kerülhet. A HTTP-kérelem így szeretné tenni, hogy a tűzfal. Ha ugyanaz a forgalom próbált IIS01 kiszolgáló eléréséhez, annak ellenére, hogy az előtér-alhálózaton van, a szabályok 5 (megtagadni) szeretné alkalmazni, és szeretné, hogy a kiszolgáló nem engedélyezett a forgalmat. 6. szabály (megtagadni) az előtér-alhálózathoz van szó, a háttér-alhálózathoz (kivéve a engedélyezett forgalom szabályokban 1 és 4) a blokkolja. A szabálykészlet védi a háttér-hálózatot, abban az esetben, ha egy támadó gyengíti a webes alkalmazás megszüntetni. A támadó volna csak korlátozott hozzáféréssel rendelkeznek a háttér-"védett" hálózatra (csak a AppVM01 kiszolgálón kitett erőforrások).

Nincs olyan alapértelmezett kimenő szabály, amely lehetővé teszi, hogy a kimenő forgalom az internethez. Ebben a példában a Microsoft most átengedi a kimenő forgalmat, és nem módosítja az egyetlen kimenő szabályok. Mindkét irányú forgalmát zárolását, felhasználó által definiált útválasztási szükség (lásd a 3. példa:).

#### <a name="firewall-rule-description"></a>Tűzfal szabály leírása
A tűzfalon továbbítási szabályokat kell létrehozni. Mivel ebben a példában csak internetes forgalmat az adathoz kötött a tűzfalhoz továbbítja, és a webkiszolgálón, hogy csak egy továbbító majd hálózati címfordítás (NAT) szabály van szükség.

A továbbítási szabály bármely találatok HTTP (80-as vagy 443-as HTTPS port) elérni tűzfal bejövő forráscím fogad el. Helyi kapcsolat a tűzfalon kívüli küldött, és átirányítja a web server 10.0.1.5 IP-címét.

#### <a name="conclusion"></a>Összegzés
Ez a példa egy viszonylag egyszerű mód a az alkalmazás egy tűzfal védi, és a háttér-alhálózathoz, a bejövő forgalom elkülönítésére. További információkért lásd: a [részletes utasításokat build][Example2]. Ezek az utasítások tartalmaznak:

* Hogyan hozhat létre a klasszikus PowerShell-parancsfájlok a szegélyhálózaton.
* Ebben a példában az Azure Resource Manager-sablonok létrehozásának módját.
* Minden NSG parancs és a tűzfalon szabály részletes leírását.
* Részletes forgalom folyamata forgatókönyvek, hogyan forgalom engedélyezett vagy megtagadott a két réteg megjelenítése.

### <a name="example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg"></a>3. példa Build hálózatokat és a tűzfalon és UDR és NSG védelme érdekében a szegélyhálózaton
[Vissza a gyors kezdési](#fast-start) | [részletes utasításokat ehhez a példához összeállítása][Example3]

[![9]][9]

#### <a name="environment-description"></a>Környezet leírása
Ebben a példában nincs olyan előfizetést, amelyet a következőket tartalmazza:

* Egyetlen erőforráscsoportként működnek
* Három alhálózat virtuális hálózat: "SecNet", "Előtér" és "Háttér"
* Ebben az esetben a tűzfal, a hálózati virtuális készülék a SecNet alhálózathoz csatlakoztatott
* A Windows server egy webalkalmazás-kiszolgáló ("IIS01") jelölő
* Két Windows-kiszolgálók, amelyek megfelelnek az alkalmazás háttérkiszolgálók ("AppVM01", "AppVM02")
* A Windows server DNS-kiszolgáló ("DNS01") jelölő

Parancsfájlok és az Azure Resource Manager-sablon, tekintse meg a [részletes utasításokat build][Example3].

#### <a name="udr-description"></a>UDR leírása
Alapértelmezés szerint a következő rendszerútvonalak is meg van adva:

        Effective routes :
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

A VNETLocal mindig egy vagy több meghatározott címelőtagokat erre a hálózatra a virtuális hálózatot alkotó (Ez azt jelenti, hogy módosítja a virtuális hálózat virtuális hálózat, attól függően, hogy hogyan minden adott virtuális hálózati van definiálva). A fennmaradó rendszerútvonalak statikusak, és a tábla a alapértelmezett.

Ebben a példában két útválasztási táblázatok jönnek létre, egyet az előtér- és alhálózatok. Minden tábla az adott alhálózat megfelelő statikus útvonalakkal be van töltve. Ebben a példában minden egyes táblához három útvonalakat, amelyek az összes forgalmat (0.0.0.0/0) irányítani a tűzfalon keresztül (a következő ugrás = virtuális készülék IP-cím):

1. A következő ugrás a helyi alhálózat forgalmának definiált helyi alhálózat forgalmának megkerüljék a tűzfal engedélyezi.
2. Virtuális hálózati forgalmat a következő ugrásaként tűzfal meghatározva. A következő ugrás felülbírálja az alapértelmezett szabály, amely lehetővé teszi, hogy a helyi virtuális hálózati forgalom továbbításához közvetlenül.
3. Minden fennmaradó forgalmat (0/0) a következő ugrásaként definiálva, a tűzfalon.

> [!TIP]
> Nem rendelkezik a helyi alhálózatra a UDR oldaltörések helyi alhálózati kommunikáció.
>
> * A jelen példában VNETLocal mutató 10.0.1.0/24 fontos! Nélkül, csomag változatlanul a webkiszolgáló (10.0.1.4) egy másik helyi kiszolgáló (például) 10.0.1.25 irányuló sikertelen lesz, mivel azok az NVA kapnak. Az NVA elküld az alhálózathoz, és az alhálózat lesz visszaigazolást az NVA e végtelen ciklusban.
> * Az útválasztási hurok több különböző alhálózatokon csatlakozó hálózati adapterrel rendelkező készülékek jellemzően nagyobb ez gyakran hagyományos, a helyszíni készülékek.
>
>

Az útválasztási táblázatok jönnek létre, ha azok alhálózataikat kell kötni. Az előtér-alhálózat-útválasztási táblázatban, egyszer létre, és az alhálózati kötve a kimenetet jelenne meg:

        Effective routes :
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active

> [!NOTE]
> UDR most az átjáró alhálózatának, amelyre csatlakoztatva van az ExpressRoute-kapcsolatcsoport alkalmazandó.
>
> Ahhoz, hogy a szegélyhálózaton ExpressRoute vagy a pont-pont hálózati példák láthatók példák 3. és 4.
>
>

#### <a name="ip-forwarding-description"></a>Az IP-továbbítás leírása
Az IP-továbbítás UDR egy kiegészítő szolgáltatást. Az IP-továbbítás egy beállítást a virtuális készülék, amely lehetővé teszi, hogy a forgalom a készülék nem kifejezetten fogadását, és majd továbbítja a végső rendeltetési hálózati forgalmat.

Például AppVM01 egy kérést küld a DNS01 kiszolgáló, UDR szeretné továbbítani a forgalmat a tűzfalon. Az IP-továbbítás engedélyezve van a forgalom DNS01 célhelyhez (10.0.2.4) fogadja el a készülék (10.0.0.4), és továbbítja a végső rendeltetési (10.0.2.4). IP-továbbítást, a tűzfal engedélyezve van, nélkül forgalom volna nem fogadja el a készülék annak ellenére, hogy az útvonaltáblát van a tűzfal következő ugrásként. A virtuális készülék használatához elengedhetetlen engedélyezze az IP-továbbítás UDR együtt.

#### <a name="nsg-description"></a>NSG leírása
Ebben a példában egy NSG-csoport összeállítása és kezelhető egyetlen szabállyal majd betölteni. Ez a csoport majd kötött csak az előtér- és alhálózatok (nem a SecNet). Deklaratív módon a következő szabály éppen készül:

* Az összes bejövő forgalom (minden porthoz) az internetről a teljes virtuális hálózathoz (az összes alhálózatot) megtagadva.

Bár ebben a példában NSG-ket használ, a fő célja azoknak a manuális helytelen konfiguráció másodlagos rétegként. A cél, hogy az előtér- vagy a háttér-alhálózatokat az internetről érkező összes bejövő forgalmát blokkolja. Forgalom csak áramlása a tűzfalhoz a SecNet alhálózati (, majd, ha szükséges, az előtér- vagy a háttér-alhálózatok be). Plus helyen, a UDR szabályokkal hajtott végre az előtér- vagy a háttér-alhálózatokra adatforgalmat volna kéri ki (UDR) környezetnek köszönhetően a tűzfalon. A tűzfal jelenik meg a forgalmat egy aszimmetrikus folyamatként, és a kimenő forgalom volna eldobni. Így az alábbi három réteg biztonsági védelmének az alhálózatok:

* Nincs nyilvános IP-címek előtér vagy háttér hálózati adaptert.
* Az NSG-k megtagadása a forgalom az internetről.
* A tűzfal-figyelmen kívül hagyása aszimmetrikus forgalom.

Ebben a példában az NSG vonatkozó egy érdekes pont, csak egy szabály, amely a teljes virtuális hálózathoz, beleértve a biztonság – alhálózati internetforgalom megtagadása tartalmaz. Azonban az NSG-t csak az előtér- és alhálózatok van kötve, mert a szabály nem feldolgozott forgalom a bejövő biztonsági alhálózathoz. Ennek eredményeképpen a forgalom a biztonsági alhálózathoz.

#### <a name="firewall-rules"></a>Tűzfalszabályok
A tűzfalon továbbítási szabályokat kell létrehozni. Mivel a tűzfal blokkolja vagy továbbító minden bejövő, kimenő, és a belüli virtuális hálózati forgalmat, sok tűzfalszabályok van szükség. Minden bejövő forgalom is, a biztonsági szolgáltatás nyilvános IP-cím (különböző portok), a találatok dolgozza fel azt a tűzfalon. Az ajánlott eljárás, hogy az alhálózatok beállítása előtt a logikai flow diagram és tűzfalszabályok elkerülése érdekében átdolgozási később. Az alábbi ábra az ebben a példában a tűzfalszabályok logikai nézetében:

![10]

> [!NOTE]
> A használt hálózati virtuális készülék alapján, a felügyeleti portokat eltérőek lehetnek. Ebben a példában a Barracuda NextGen tűzfal hivatkozik, 22, 801 és 807 portot használó. Tekintse át a készülék gyártó dokumentációjában, a pontos portokat használ az eszköz felügyeletére használható.
>
>

#### <a name="firewall-rules-description"></a>Tűzfal-szabályok leírása
A fenti logikai ábrán a biztonság – alhálózati nem látható, mert a tűzfal be az adott alhálózat csak erőforrás. Az ábrán látható, a tűzfal-szabályok és hogyan azok logikailag engedélyezi vagy megtagadja a forgalmat, a tényleges irányított elérési. Is, a külső portok az RDP-forgalmát magasabbak kiválasztott címkiosztási (8014 – 8026) portok, és lazán megfelel-e az utolsó két oktettjének meg a helyi IP-cím az egyszerűbb olvashatóság sincs kijelölve (például helyi kiszolgáló címe 10.0.1.4 társítva van külső port 8014). Magasabb nem ütköző portja, azonban is használható.

Ebben a példában a hét típusú szabályokat kell:

* Külső szabályainak (bejövő forgalom):
  1. Felügyeleti tűzfalszabály: az alkalmazás átirányítási szabály lehetővé teszi, hogy a forgalom a felügyeleti portokat a hálózati virtuális készülék számára.
  2. RDP-szabályok (az egyes Windows-kiszolgálók): A négy szabályok (kiszolgálónként egyet) engedélyezése az egyes kiszolgálók RDP-kapcsolaton keresztül. A négy RDP-szabályok is sikerült összecsukása egy szabályt, attól függően, hogy a használt hálózati virtuális készülék képességeinek be.
  3. Alkalmazás forgalomra vonatkozó szabályok: kettő ezeket a szabályokat, az első az előtér-forgalom számára, és a második a háttér-forgalom (például web server adatréteg). Ezek a szabályok konfigurálása a hálózati architektúra (ahol a kiszolgálók kerülnek) függ, és a forgalom adatfolyamok (a forgalmat, és azoknak a portoknak használt iránya).
     * Az első szabály lehetővé teszi, hogy a kiszolgáló eléréséhez a tényleges alkalmazás forgalmat. A többi szabály engedélyezni a biztonságot és felügyeletet, alkalmazás forgalomra vonatkozó szabályok is mi engedélyezése külső felhasználót vagy szolgáltatást, az alkalmazások elérésére. Ehhez a példához nincs egyetlen webkiszolgálón való üzemeltetésekor 80-as porton. Így egy alkalmazás egyetlen tűzfalszabályt a külső IP-címhez, a webes kiszolgálók belső IP-címre bejövő forgalom irányítja át. A belső kiszolgáló NAT keresztül a forgalmat munkamenet volna lefordítani.
     * A második szabály a háttér-szabály lehetővé teszi a webkiszolgáló felvegye a AppVM01 kiszolgáló (de nem AppVM02) bármely porton keresztül.
* Belső szabályainak (belüli virtuális hálózati forgalom)
  1. Kimenő Internet szabály: Ez a szabály lehetővé teszi, hogy a kijelölt hálózat minden hálózati forgalmat. Ez a szabály az általában egy alapértelmezett szabály már a tűzfalon, de a letiltott állapotú. Ez a szabály ehhez a példához engedélyezni kell.
  2. DNS-szabály: Ez a szabály lehetővé teszi, hogy csak a DNS (53-as port) forgalom számára a DNS-kiszolgáló. Az ebben a környezetben a háttérben az előtér-legtöbb forgalom le van tiltva. Ez a szabály kifejezetten engedélyezi a DNS, az a helyi alhálózaton.
  3. Alhálózati szabály alhálózati: Ez a szabály az összes olyan kiszolgálónak engedélyezi az előtér-alhálózat (de nem fordítva) bármely kiszolgálóhoz való csatlakozáshoz a háttér-alhálózat szerepel.
* Hibamentes (forgalomra vonatkozó szabály, amely nem felel meg az előző):
  1. Minden forgalmi szabály megtagadása: Ez a megtagadási szabály mindig kell a végső szabály (tekintetében prioritás), és használja, így ha a forgalom áramlását nem felel meg az előző szabályok Ez a szabály a program törli. Ez a szabály az alapértelmezett szabály, és általában helyszíni és az aktív. Ez a szabály módosítása nem általában szükségesek.

> [!TIP]
> A második alkalmazás forgalmi szabály egyszerűsítése ebben a példában bármely portra van engedélyezve. Egy valós forgatókönyv esetén a legjobban megfelel port és címtartomány használandó csökkenteni a támadási felületet, a szabály.
>
>

Az előző szabályok jönnek létre, ha fontos tekintse át a forgalmat engedélyezett vagy megtagadott a kívánt minden egyes szabály prioritását. Ebben a példában a szabályok prioritási sorrendben szerepelnek.

#### <a name="conclusion"></a>Összegzés
Ebben a példában az összetettebb de védelmének és a hálózat az előző példák mint elkülönítése módja befejeződik. (2. példában csak az alkalmazás védi, és 1. példa csak korlátozása alhálózatok). Ez a kialakítás lehetővé teszi, hogy mindkét irányban forgalom figyelése, és nem csak a bejövő-kiszolgáló védi, de a hálózat kiszolgálóinak hálózati biztonsági házirend érvénybe lépteti. Is attól függően, hogy a készülék használt, teljes forgalom naplózás és a tájékoztatási érhető el. További információkért lásd: a [részletes utasításokat build][Example3]. Ezek az utasítások tartalmaznak:

* Hogyan hozhat létre a klasszikus PowerShell-parancsfájlok Ez például szegélyhálózaton.
* Ebben a példában az Azure Resource Manager-sablonok létrehozásának módját.
* Részletes leírását minden UDR NSG parancsot, és a tűzfalszabály.
* Részletes forgalom folyamata forgatókönyvek, hogyan forgalom engedélyezett vagy megtagadott a két réteg megjelenítése.

### <a name="example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn"></a>4. példa egy hibrid kapcsolat hozzáadása az a pont-pont, a virtuális készülék VPN
[Vissza a gyors kezdési](#fast-start) |} Részletes hamarosan elérhető build utasításokat

[![11]][11]

#### <a name="environment-description"></a>Környezet leírása
Hibrid hálózatkezelés használatával a hálózati virtuális készülék (NVA) a peremhálózati hálózati típusok bemutatott példákban 1, 2 vagy 3 lehet hozzáadni.

Az előző ábrán látható, a VPN-kapcsolatot az interneten (pont-pont) használatos NVA keresztül Azure virtuális hálózat egy a helyszíni hálózathoz csatlakozni.

> [!NOTE]
> Ha ExpressRoute használja az Azure nyilvános társviszony-létesítés beállítás engedélyezve van, létre kell hozni egy statikus útvonal. Statikus útvonal kell továbbítani a vállalati internetes és az ExpressRoute-kapcsolaton keresztül nem NVA VPN IP-címre. A NAT ExpressRoute Azure nyilvános társviszony-létesítés esetén szükséges meghibásodásához vezethet a VPN-munkamenet.
>
>

A VPN van beállítva, az NVA lesz az összes hálózatok és alhálózatok központi helyet alakíthatnak. A tűzfalszabályok továbbítási határozza meg, melyik forgalom engedélyezettek, NAT keresztül van lefordítva, a rendszer az vagy dobja (akár még a forgalmat a helyszíni hálózat és az Azure között).

Forgalom kell tekinteni, is lehet optimalizálni, vagy csökken, mert ebben a kialakításban attól függően, hogy az egyes felhasználási eseténél.

A 3. példa a beépített környezet használatával, és a pont-pont VPN hibrid hálózati kapcsolatot, hozza létre a következő tervezési:

[![12]][12]

A helyszíni útválasztót, vagy bármely más hálózati eszköz, amely kompatibilis a VPN-, a NVA lenne a VPN-ügyfél. A fizikai eszköz felelős kezdeményezése és karbantartása a NVA VPN-kapcsolattal.

Logikailag az NVA, hogy a hálózaton a következőképpen néz négy külön "biztonsági zónák" szabályokkal az NVA az elsődleges igazgató zónákhoz közötti forgalom folyamatban lévő:

![13]

#### <a name="conclusion"></a>Összegzés
A pont-pont VPN-hibrid hálózati kapcsolat az Azure virtuális hálózat hozzáadása kibővítheti a helyszíni hálózat az Azure biztonságos módon. A VPN-kapcsolattal, a forgalom titkosítva van, és továbbítja az interneten keresztül. Ebben a példában az NVA érvényesítése és a biztonsági házirend kezelése a központi helyet biztosít. További információkért tekintse meg a részletes build útmutatást (szolgáltatnak). Ezek az utasítások tartalmaznak:

* Ez például szegélyhálózaton PowerShell parancsfájlok létrehozásának módját.
* Ebben a példában az Azure Resource Manager-sablonok létrehozásának módját.
* Részletes forgalom folyamata forgatókönyvek, jelenít meg, hogyan forgalom Ez a kialakítás keresztül zajlik.

### <a name="example-5-add-a-hybrid-connection-with-a-site-to-site-azure-vpn-gateway"></a>5. példa egy hibrid kapcsolat-webhelyek, Azure VPN-átjáró hozzáadása
[Vissza a gyors kezdési](#fast-start) |} Részletes hamarosan elérhető build utasításokat

[![14]][14]

#### <a name="environment-description"></a>Környezet leírása
Az Azure VPN-átjáró használata hibrid hálózatkezelés vagy peremhálózat típusa bemutatott példákban 1 vagy 2 lehet hozzáadni.

A fenti ábrán látható, egy VPN-kapcsolatot az interneten (pont-pont) segítségével csatlakozzon egy helyi hálózaton egy Azure virtuális hálózatot az Azure VPN-átjárón keresztül.

> [!NOTE]
> Ha ExpressRoute használja az Azure nyilvános társviszony-létesítés beállítás engedélyezve van, létre kell hozni egy statikus útvonal. Statikus útvonal, a vállalati internetes, és nem keresztül az ExpressRoute WAN NVA VPN IP-címére kell továbbítani. A NAT ExpressRoute Azure nyilvános társviszony-létesítés esetén szükséges meghibásodásához vezethet a VPN-munkamenet.
>
>

Az alábbi ábrán a két hálózati élt ebben a példában látható. Az első oldal az NVA és NSG-ket ellenőrzés forgalom belüli Azure-hálózatok és az Azure és az Internet között. A második biztonsági az Azure VPN-átjáró, amely egy külön és elkülönített hálózati edge-hez és az Azure között.

Forgalom kell tekinteni, is lehet optimalizálni, vagy csökken, mert ebben a kialakításban attól függően, hogy az egyes felhasználási eseténél.

Az 1. példa a beépített környezet használatával, és a pont-pont VPN hibrid hálózati kapcsolatot, hozza létre a következő tervezési:

[![15]][15]

#### <a name="conclusion"></a>Összegzés
A pont-pont VPN-hibrid hálózati kapcsolat az Azure virtuális hálózat hozzáadása kibővítheti a helyszíni hálózat az Azure biztonságos módon. A natív Azure VPN-átjárót használ, a forgalom IPSec-titkosítású és az interneten keresztül irányítja. Emellett az Azure VPN gateway használatával biztosíthat egy alacsonyabb költségű lehetőséget (nincs további licencelési költségeket, mint a külső NVAs). Ez a beállítás a leggazdaságosabb példában 1, ahol nincs NVA használt. További információkért tekintse meg a részletes build útmutatást (szolgáltatnak). Ezek az utasítások tartalmaznak:

* Ez például szegélyhálózaton PowerShell parancsfájlok létrehozásának módját.
* Ebben a példában az Azure Resource Manager-sablonok létrehozásának módját.
* Részletes forgalom folyamata forgatókönyvek, jelenít meg, hogyan forgalom Ez a kialakítás keresztül zajlik.

### <a name="example-6-add-a-hybrid-connection-with-expressroute"></a>6. példa az ExpressRoute hibrid kapcsolat hozzáadása
[Vissza a gyors kezdési](#fast-start) |} Részletes hamarosan elérhető build utasításokat

[![16]][16]

#### <a name="environment-description"></a>Környezet leírása
Hibrid hálózatkezelés ExpressRoute magánhálózati társviszony-létesítési kapcsolaton keresztül lehet hozzáadni vagy peremhálózat típusa bemutatott példákban 1 vagy 2.

A fenti ábrán látható, a helyszíni hálózat és az Azure virtuális hálózat között közvetlen kapcsolat ExpressRoute magánhálózati társviszony-létesítés biztosít. Forgalom transits csak a szolgáltatás szolgáltató és a Microsoft Azure hálózat, soha nem igazítható hozzá az internethez.

> [!TIP]
> ExpressRoute segítségével tartja a vállalati hálózati forgalom az internetről. Lehetővé teszi a is garantált szolgáltatási szintek az ExpressRoute-szolgáltatótól. Az Azure-átjáró teljen legfeljebb 10 GB/s az ExpressRoute, mivel az a pont-pont VPN, az Azure-átjáró maximális átviteli sebesség 200 MB/s.
>
>

Az alábbi ábrán látható, ezzel a beállítással a környezet most már két hálózati szélén. Az NVA és NSG szabályozhatja forgalom belüli Azure-hálózatok és az Azure és az Internet között, amíg az átjáró egy külön és elkülönített hálózati peremhálózati a helyszíni és az Azure közötti.

Forgalom kell tekinteni, is lehet optimalizálni, vagy csökken, mert ebben a kialakításban attól függően, hogy az egyes felhasználási eseténél.

Az 1. példa a beépített környezet használatával, és a ExpressRoute hibrid hálózati kapcsolat, hozza létre a következő tervezési:

[![17]][17]

#### <a name="conclusion"></a>Összegzés
Egy ExpressRoute privát társviszony-létesítés hálózati kapcsolat hozzáadása kibővítheti a helyszíni hálózat az Azure a biztonságos, alacsonyabb késés, nagyobb végrehajtása módon. Is ebben a példában látható módon a natív Azure átjáró használatával biztosít egy alacsonyabb költségű (nem kiegészítő licencelési, mint a külső NVAs) lehetőséget. További információkért tekintse meg a részletes build útmutatást (szolgáltatnak). Ezek az utasítások tartalmaznak:

* Ez például szegélyhálózaton PowerShell parancsfájlok létrehozásának módját.
* Ebben a példában az Azure Resource Manager-sablonok létrehozásának módját.
* Részletes forgalom folyamata forgatókönyvek, jelenít meg, hogyan forgalom Ez a kialakítás keresztül zajlik.

## <a name="references"></a>Referencia
### <a name="helpful-websites-and-documentation"></a>Hasznos webhelyek és dokumentáció
* Access Azure az Azure Resource Manager eszközzel:
* Hozzáférés az Azure PowerShell használatával: [https://docs.microsoft.com/powershell/azureps-cmdlets-docs/](/powershell/azure/overview)
* Virtuális hálózati dokumentáció: [https://docs.microsoft.com/azure/virtual-network/](https://docs.microsoft.com/azure/virtual-network/)
* Hálózati biztonsági csoport dokumentáció: [https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg](virtual-network/security-overview.md)
* Felhasználó által definiált útválasztási dokumentáció: [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview](virtual-network/virtual-networks-udr-overview.md)
* Az Azure virtuális átjárók: [https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway/)
* Pont-pont VPN: [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell](vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* Az ExpressRoute dokumentációja (mindenképpen tekintse meg az "Első lépések" és "Hogyan a következőnek" szakaszok): [https://docs.microsoft.com/azure/expressroute/](https://docs.microsoft.com/azure/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "biztonsági beállítások folyamatábrája"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "azure biztonsági funkciói"
[3]: ./media/best-practices-network-security/dmzcorporate.png "A Szegélyhálózaton a vállalati hálózat"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "azure biztonsági architektúrája"
[5]: ./media/best-practices-network-security/dmzazure.png "DMZ egy Azure virtuális hálózatban"
[6]: ./media/best-practices-network-security/dmzhybrid.png "három biztonsági határokat hibrid hálózat"
[7]: ./media/best-practices-network-security/example1design.png "DMZ az NSG bejövő"
[8]: ./media/best-practices-network-security/example2design.png "DMZ NVA és NSG bejövő"
[9]: ./media/best-practices-network-security/example3design.png "kétirányú DMZ NVA, NSG és UDR"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "a tűzfalszabályok logikai ábrázolása"
[11]: ./media/best-practices-network-security/example3designoptions.png "az NVA DMZ csatlakoztatott hibrid hálózati"
[12]: ./media/best-practices-network-security/example4designs2s.png "az a pont-pont VPN-nel csatlakoztatott NVA DMZ"
[13]: ./media/best-practices-network-security/example4networklogical.png "NVA szempontjából logikai hálózat"
[14]: ./media/best-practices-network-security/example5designoptions.png "Azure átjáróval DMZ csatlakoztatott pont-pont hibrid hálózati"
[15]: ./media/best-practices-network-security/example5designs2s.png "DMZ Azure átjáró telephelyek közötti VPN használatával"
[16]: ./media/best-practices-network-security/example6designoptions.png "DMZ-átjáróval Azure ExpressRoute hibrid hálózati csatlakoztatva"
[17]: ./media/best-practices-network-security/example6designexpressroute.png "DMZ Azure ExpressRoute kapcsolattal átjáróval"

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
