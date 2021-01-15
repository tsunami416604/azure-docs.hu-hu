---
title: Árképzési szintek kiválasztása
titleSuffix: Azure Cognitive Search
description: 'Az Azure Cognitive Search az alábbi szinteken hozhatók létre: az ingyenes, az alapszintű és a standard, valamint a standard szint számos erőforrás-konfigurációban és kapacitási szinten érhető el.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 4c58968cb6a38a10433915ec8fa00336ccad301e
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216410"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Válasszon árképzési szintet az Azure Cognitive Search

[Keresési szolgáltatás létrehozásakor](search-create-service-portal.md)ki kell választania a szolgáltatás élettartamára rögzített díjszabási szintet. A kiválasztott rétegek a következőket határozzák meg:

+ Indexek és egyéb létrehozott objektumok mennyisége (maximális korlát)
+ Partíciók mérete és sebessége (fizikai tárterület)
+ Számlázható arány, rögzített havi költség, valamint növekményes költség, ha partíciókat vagy replikákat ad hozzá

Emellett a prémium szintű [funkciók](#premium-features) is megtalálhatók a szint követelményeivel.

## <a name="tier-descriptions"></a>Szintek leírása

A rétegek közé tartoznak az **ingyenes**, az **alapszintű**, a **standard** és a **Storage optimalizált** funkciók. A standard és a Storage optimalizált szolgáltatás számos konfigurációval és kapacitással érhető el.

A következő képernyőkép a Azure Portal megjeleníti az elérhető csomagokat, mínusz a díjszabás (amely a Portálon és a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/search/)található. 

![Az Azure Cognitive Search díjszabási szintjei](media/search-sku-tier/tiers.png "Az Azure Cognitive Search díjszabási szintjei")

Az **ingyenes** szolgáltatás korlátozott keresési szolgáltatást hoz létre kisebb projektekhez, például oktatóanyagokat és mintakód-mintákat futtat. Belsőleg a replikák és partíciók megosztása több előfizető között történik. Az ingyenes szolgáltatás nem méretezhető, és nem futtathat jelentős számítási feladatokat.

Az **alapszintű és a** **standard** a leggyakrabban használt számlázó rétegek, amelyek alapértelmezett értéke a **standard** . A vezérlőhöz tartozó dedikált erőforrásokkal nagyobb projekteket helyezhet üzembe, optimalizálhatja a teljesítményt, és növelheti a kapacitást.

Egyes rétegek bizonyos típusú munkákra vannak optimalizálva. Például a **Standard 3 nagy sűrűségű (S3 HD)** az S3 *üzemeltetési módja* , ahol a mögöttes hardver nagy számú kisebb indexre van optimalizálva, és bérlős forgatókönyvekhez készült. Az S3 HD azonos egységenkénti díjszabással rendelkezik, de a hardver nagy számú kisebb index esetén a gyors fájlokra van optimalizálva.

A **tárolásra optimalizált** csomagok nagyobb tárolókapacitást biztosítanak, mint a standard szinteken a TB-onként alacsonyabb áron. Az elsődleges kompromisszum nagyobb lekérdezési késés, amelyet az adott alkalmazásra vonatkozó követelmények érvényesítéséhez kell érvényesíteni. Ha többet szeretne megtudni a rétegek teljesítményével kapcsolatos megfontolásokról, tekintse meg a [teljesítmény-és optimalizálási szempontokat](search-performance-optimization.md).

További információt a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/search/), a [szolgáltatási korlátok az Azure Cognitive Search](search-limits-quotas-capacity.md) cikkben, valamint a portál lapon talál, amikor egy szolgáltatást kiépít.

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>A szolgáltatás rendelkezésre állása rétegek szerint

A legtöbb funkció minden szinten elérhető, beleértve az ingyenes szintet is. Néhány esetben a kiválasztott rétegek hatással lesznek a funkciók megvalósítására. A következő táblázat a szolgáltatási szintjéhez kapcsolódó szolgáltatásokra vonatkozó korlátozásokat ismerteti.

| Funkció | Korlátozások |
|---------|-------------|
| [indexelők](search-indexer-overview.md) | Az indexelő nem érhető el az S3 HD-ben.  |
| [MI-bővítés](search-security-manage-encryption-keys.md) | Az ingyenes szinten fut, de nem ajánlott. |
| [Ügyfél által felügyelt titkosítási kulcsok](search-security-manage-encryption-keys.md) | Az ingyenes szinten nem érhető el. |
| [IP-tűzfal-hozzáférés](service-configure-firewall.md) | Az ingyenes szinten nem érhető el. |
| [Privát végpont (az Azure Private Linktel való integráció)](service-create-private-endpoint.md) | A keresési szolgáltatáshoz való bejövő kapcsolatok esetében az ingyenes szinten nem érhető el. Az indexelő által más Azure-erőforrásokhoz való kimenő kapcsolatok esetén az ingyenes vagy az S3 HD-on nem érhető el. Az szakértelmével-t használó indexelő esetében az ingyenes, az alapszintű, az S1 vagy az S3 HD nem érhető el.|

Előfordulhat, hogy az erőforrás-igényes funkciók nem működnek megfelelően, hacsak nem biztosít elegendő kapacitást. Például az [AI-dúsítás](cognitive-search-concept-intro.md) olyan hosszan futó képességekkel rendelkezik, amelyek egy ingyenes szolgáltatás esetében időtúllépést mutatnak, kivéve, ha az adatkészlet kicsi.

## <a name="billable-events"></a>Számlázható események

Az Azure Cognitive Search-ra épülő megoldások a következő módokon vehetik igénybe a költségeket:

+ Magának [a szolgáltatásnak a díja](#service-costs) , a nonstop futtatása minimális konfigurációval (egy partíció és egy replika) az alaparányban. Ezt a szolgáltatás futtatásának fix díjaként tekintheti át.

+ Kapacitás (replikák vagy partíciók) hozzáadása, ahol a költségek a számlázható arány növekményei szerint növekednek

+ Sávszélességgel kapcsolatos díjak (kimenő adatforgalom)

+ Adott funkciókhoz vagy szolgáltatásokhoz szükséges kiegészítő szolgáltatások:

  + AI-gazdagodás ( [Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/)szükséges)
  + Knowledge Store ( [Azure Storage](https://azure.microsoft.com/pricing/details/storage/)szükséges)
  + növekményes gazdagodás (az [Azure Storage](https://azure.microsoft.com/pricing/details/storage/)-t igényli, az AI-bővítésre vonatkozik)
  + ügyfél által felügyelt kulcsok és kettős titkosítás ( [Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/)szükséges)
  + privát végpontok nem Internet-hozzáférési modellhez ( [Azure Private-hivatkozást](https://azure.microsoft.com/pricing/details/private-link/)igényel)

### <a name="service-costs"></a>Szolgáltatás költségei

A virtuális gépekkel vagy más, a díjak elkerülésére szolgáló egyéb erőforrásokkal ellentétben az Azure Cognitive Search szolgáltatás mindig a kizárólagos használatra dedikált hardveren érhető el. Ennek megfelelően a szolgáltatás létrehozása olyan számlázható esemény, amely a szolgáltatás létrehozásakor kezdődik, és a szolgáltatás törlésekor lejár. 

A minimális díj az első keresési egység (az egyik replika x egy partíció) a számlázható sebességgel. Ez a minimális érték a szolgáltatás élettartamára van javítva, mert a szolgáltatás nem futhat ennél a konfigurációnál kisebb mértékben. A minimumon túl egymástól függetlenül is hozzáadhat replikákat és partíciókat. A kapacitás replikák és partíciók révén történő növekményes növekedése a következő képlet alapján növeli a számla mennyiségét: [(replikák x Partitions x Rate)](#search-units), ahol a felszámított díj a kiválasztott árképzési szinttől függ.

Ha megbecsüli egy keresési megoldás költségét, ne feledje, hogy a díjszabás és a kapacitás nem lineáris. (A kapacitás megkettőzése meghaladja a költségeket.) A képlet működéséről a következő témakörben talál példát: [replikák és partíciók lefoglalása](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

### <a name="bandwidth-charges"></a>Sávszélességgel kapcsolatos díjak

Az [Indexelő](search-indexer-overview.md) használata hatással lehet a számlázásra, ha az Azure-adatforrás az Azure Cognitive Searchtól eltérő régióban található. Ebben az esetben a kimenő adatok Azure-adatforrásból az Azure-Cognitive Searchba való áthelyezésének díja lehet. Részletekért tekintse meg az Azure-beli adatplatform díjszabási oldalát.

Ha az Azure Cognitive Search szolgáltatást ugyanabban a régióban hozza létre, mint az adatait, teljes mértékben kiküszöbölheti az adatforgalom díját. Íme néhány információ a sávszélesség- [díjszabási lapról](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Bejövő adatforgalom: a Microsoft nem számít fel díjat semmilyen, az Azure-beli szolgáltatásba beérkező adatforgalomért. 

+ Kimenő adatforgalom: a kimenő adatforgalom a lekérdezési eredményekre hivatkozik. Cognitive Search a kimenő adatokért nem számítunk fel díjat, de az Azure-beli kimenő díjak akkor is lehetségesek, ha a szolgáltatások különböző régiókban találhatók. Ezek a díjak valójában nem részei az Azure Cognitive Search-számlájának. Itt említik, mert ha más régióknak vagy nem Azure-alkalmazásoknak küldi az eredményeket, láthatja, hogy ezek a költségek a teljes számlán szerepelnek.

### <a name="ai-enrichment-with-cognitive-services"></a>AI-gazdagítás Cognitive Services

Az [AI](cognitive-search-concept-intro.md)-bővítés esetében érdemes megtervezni, hogy [egy számlázható Azure Cognitive Services-erőforrást](cognitive-search-attach-cognitive-services.md), ugyanabban a régióban, mint az Azure Cognitive Searcht, az utólagos elszámolású feldolgozás S0 díjszabási szintjére kell terveznie. Nincs rögzített díj a Cognitive Services csatolásával kapcsolatban. Csak a szükséges feldolgozásért kell fizetnie.

| Művelet | Számlázási hatás |
|-----------|----------------|
| Dokumentum repedése, szöveg kinyerése | Ingyenes |
| A dokumentumok repedése, a képek kinyerése | A számlázás a dokumentumokból kinyert képek száma alapján történik. Az [Indexelő konfigurációjában](/rest/api/searchservice/create-indexer#indexer-parameters)a **imageAction** az a paraméter, amely a képek kinyerését váltja ki. Ha a **imageAction** értéke "None" (alapértelmezett), nem számítunk fel díjat a képek kinyeréséhez. A képek kinyerésének díja az Azure Cognitive Search [díjszabási részleteit ismertető](https://azure.microsoft.com/pricing/details/search/) oldalon található.|
| [Beépített kognitív képességek](cognitive-search-predefined-skills.md) | A számlázás ugyanolyan sebességgel történik, mint ha a feladatot a Cognitive Services közvetlen használatával végezte el. |
| Egyéni készségek | Az egyéni képességek az Ön által megadott funkciók. Az egyéni képességek használatának díja teljes mértékben attól függ, hogy az egyéni kód más mért szolgáltatásokat hív-e meg. |

A [növekményes bővítés (előzetes verzió)](cognitive-search-incremental-indexing-conceptual.md) funkció lehetővé teszi egy olyan gyorsítótár megadását, amely lehetővé teszi, hogy az indexelő hatékonyabban fusson, és csak azokat a kognitív képességeket futtassa, amelyekre szükség van, ha később módosítja a készségkészlet, így időt és pénzt takarít meg.

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>Számlázási képlet (R x P = SU)

Az Azure Cognitive Search műveletekkel kapcsolatos legfontosabb számlázási koncepció a *keresési egység* (su). Mivel az Azure Cognitive Search az indexeléshez és a lekérdezésekhez tartozó replikák és partíciók függ, nem kell csupán egyet vagy a másikat számláznia. Ehelyett a számlázás a kettő összetett példányán alapul.

A SU a szolgáltatás által használt *replikák* és *partíciók* terméke: **(R x P = su)**.

Minden szolgáltatás egy SU-val kezdődik (az egyik replikát egy partícióval szorozva) a minimális értékkel. Bármely szolgáltatás esetében a maximális érték 36 SUs. A maximális érték több módon is elérhető: 6 partíció x 6 replika vagy 3 partíció x 12 replika, például. Gyakori, hogy a teljes kapacitásnál kevesebbet használ (például egy 3 replika, 3 partíciós szolgáltatás, amely 9 SUs-ként lett kiszámlázva). Tekintse meg a [partíciós és replika kombinációk](search-capacity-planning.md#chart) diagramot az érvényes kombinációk esetében.

A számlázási díj óradíja/SU. Az egyes szintek fokozatosan magasabb arányban jelennek meg. A magasabb szintek nagyobb és gyorsabb partíciókkal rendelkeznek, és ez az adott szinten a legmagasabb szintű óradíjat is segíti. Az egyes szintek díjszabását a [díjszabás részletei](https://azure.microsoft.com/pricing/details/search/) lapon tekintheti meg.

A legtöbb ügyfél csak egy részét a teljes kapacitás online állapotba helyezi, a többi pedig tartalékban tartja. A számlázáshoz a SU-képlet alapján kiszámított partíciók és replikák száma határozza meg, hogy mit fizet óránként.

## <a name="next-steps"></a>Következő lépések

A Cost Management a kapacitás megtervezésének szerves részét képezi. Következő lépésként folytassa a következő cikkel, amely útmutatást nyújt a kapacitás becsléséhez és a költségek kezeléséhez.

> [!div class="nextstepaction"]
> [A költségek és a becsült kapacitások kezelése az Azure-ban Cognitive Search](search-sku-manage-costs.md)