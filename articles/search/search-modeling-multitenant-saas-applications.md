---
title: Az Azure Search szolgáltatásban a több-Bérlős modellezés |} A Microsoft Docs
description: További információ a gyakori tervezési minták több-bérlős SaaS-alkalmazások Azure Search használata során.
manager: jlembicz
author: LiamCavanagh
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: liamca
ms.custom: seodec2018
ms.openlocfilehash: 1da9756df4fa05b367665a5fe024528939f22578
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53313037"
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-search"></a>Tervezési minták több-bérlős SaaS-alkalmazások és az Azure Search az
Egy több-bérlős alkalmazás, amelyik azonos szolgáltatásokat és képességeket biztosít a bérlők számára, akik nem látható, vagy ossza meg az adatokat semmilyen más bérlővel, tetszőleges számú. Ez a dokumentum ismerteti a bérlő elkülönítési stratégiák az Azure Search-alapú több-bérlős alkalmazásokhoz.

## <a name="azure-search-concepts"></a>Az Azure Search-fogalmak
Azure Search keresési--szolgáltatásként megoldás, lehetővé teszi a segítségével a fejlesztők sokoldalú keresési funkciókkal bővíthetik az alkalmazások bármely-infrastruktúra kezelésére, és láthatja az információk kiolvasásához nélkül. Adatok feltöltődtek a szolgáltatásra és a felhőben tárolja. Egyszerű kérelmek Azure Search API használata esetén az adatok ezután módosíthatók és keres. A szolgáltatás áttekintése található [Ez a cikk](https://aka.ms/whatisazsearch). Tervezési minták ismertetése, előtt fontos fontos tudni, hogy néhány olyan fogalmat, az Azure Search szolgáltatásban.

### <a name="search-services-indexes-fields-and-documents"></a>Szolgáltatások, indexek, mezők és dokumentumok keresése
Azure Search használata esetén az egyik feliratkozik egy *keresési szolgáltatás*. Adatfeltöltés az Azure Search, kerülnek a egy *index* a keresési szolgáltatásban. Indexek egyetlen szolgáltatáson belül számos lehet. A jól ismert fogalmakat,-adatbázisok használatához a közben az indexek a szolgáltatáson belül is lehet likened adatbázisban lévő táblák adatbázishoz a keresési szolgáltatás is likened.

Minden egyes index belül egy keresési szolgáltatás rendelkezik a saját sémáját, testre szabható számos által definiált *mezők*. Azure Search-index, az egyéni űrlap az adatok bekerülnek *dokumentumok*. Minden egyes dokumentum adott index fel kell tölteni, és adott indexsémát el kell férnie. Ha adatokat az Azure Search használatával keres, a teljes szöveges keresési lekérdezések egy adott index vannak állították.  Hasonlítsa össze ezeket a fogalmakat és az érintett adatbázis, mezők is likened, a táblázatban levő oszlopkészleteket, és a dokumentumok is likened, sorokra.

### <a name="scalability"></a>Méretezhetőség
Minden olyan Azure Search szolgáltatásra a szabványos [tarifacsomag](https://azure.microsoft.com/pricing/details/search/) két dimenzióban méretezheti: tárolás és rendelkezésre állás.

* *A partíciók* növelni a keresési szolgáltatás tárolására is hozzáadhatók.
* *Replikák* növelhető a kérések a search service képes kezelni egy szolgáltatás lehet hozzáadni.

Partíciók és a replikán hozzáadása és eltávolítása lehetővé teszi a kapacitás, a keresési szolgáltatásnak az adatok mennyisége növekszik és a forgalom az alkalmazások számára. Ahhoz, hogy egy keresési szolgáltatás egy olvasási eléréséhez [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), két replika van szükség. Ahhoz, hogy egy szolgáltatás eléréséhez egy olvasási és írási [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), három replika van szükség.

### <a name="service-and-index-limits-in-azure-search"></a>Az Azure Search szolgáltatás és az index korlátok
Van néhány másik [tarifacsomagok](https://azure.microsoft.com/pricing/details/search/) az Azure Search szolgáltatásban a rétegek mindegyike rendelkezik másik [korlátok és kvóták](search-limits-quotas-capacity.md). Ezek a korlátozások némelyike a szolgáltatási szintű, az index szintjén néhány és néhány, a partíció-szinten.

|  | Alapszintű | Standard1 | Standard (2) | Standard (3) | Standard3 HD |
| --- | --- | --- | --- | --- | --- |
| Maximális replikák szolgáltatásonként |3 |12 |12 |12 |12 |
| Maximális partíciók szolgáltatásonként |1 |12 |12 |12 |3 |
| Maximális keresési egységekre (replikák * partíciók) szolgáltatásonként |3 |36 |36 |36 |36 (legfeljebb 3 partíció) |
| Maximális tárhely szolgáltatásonként |2 GB |300 GB |1,2 TB |2,4 TB |600 GB |
| Maximális tárterület partíciónként |2 GB |25 GB |100 GB |200 GB |200 GB |
| Indexek maximális száma szolgáltatásonként |5 |50 |200 |200 |3000 (maximum 1000 indexek/partíció) |

#### <a name="s3-high-density"></a>S3 Nagy kapacitású "
Az Azure Search S3 tarifacsomagra nincs lehetőség a nagy sűrűségű (HD) üzemmód kifejezetten a több-bérlős helyzetekben készült. Sok esetben fontos az egyszerűség és költséget hatékonyság előnyeinek eléréséhez egy egyetlen szolgáltatást a kisebb bérlők nagy számú támogatásához szükséges.

S3 HD lehetővé teszi a sok kis indexet horizontális felskálázása az indexek használata lehetővé teszi a partíciók egy szolgáltatásban több index üzemeltetésére képes kereskedelmi szerint kell becsomagolni, egyetlen keresési szolgáltatás a felügyeleti csoportban.

Az S3 szintű szolgáltatáscsomagban konkrétan, 1 és 200 indexeket, amelyek együtt sikerült akár 1,4 milliárd dokumentumok között lehet. Az S3 HD azonban lehetővé teszik, hogy csak a legfeljebb 1 millió dokumentumot go egyedi indexek, de a teljes számát a 200 millió partíciónként (legfeljebb 3000 szolgáltatásonként) partíciónként akár 1000 indexet is képes kezelni (legfeljebb 600 millió szolgáltatásonként).

## <a name="considerations-for-multitenant-applications"></a>Szempontok a több-bérlős alkalmazásokban
Több-bérlős alkalmazások hatékony el kell juttatnia a erőforrások a bérlők közötti adatvédelmi a különböző bérlők között bizonyos fokú megőrzése mellett. Ilyen alkalmazás architektúrájának tervezése során, van néhány szempont:

* *Bérlő elszigetelése:* Alkalmazás a fejlesztőknek kell a megfelelő intézkedéseket annak érdekében, hogy nem a bérlők számára nem engedélyezett vagy nemkívánatos más bérlők adataihoz való hozzáférés. Túl az adatvédelem szempontjából a bérlő elkülönítési stratégiák szükség a megosztott erőforrások és a védelem a zajos szomszédok hatékony kezelése.
* *Felhőalapú erőforrások költség:* Mint minden más alkalmazás, a szoftveres megoldások költség versenyképes egy több-bérlős alkalmazás részeként kell maradnia.
* *A könnyű műveletek:* Egy több-bérlős architektúra fejlesztésével, az alkalmazás-műveletek és összetettségi gyakorolt esetén fontos szempont. Az Azure Search rendelkezik egy [99,9 %-os SLA-t](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
* *Globális lefedettséggel:* Több-bérlős alkalmazások hatékony szolgálja ki a bérlők számára, amely a világ különböző pontjain szükségessé.
* *Méretezhetőség:* Az alkalmazásfejlesztők kell figyelembe venni, hogyan azok egyeztetése karbantartása elég alacsony szintű alkalmazás összetettségét és az alkalmazás méretezése a bérlők száma és a bérlők adatok méretétől és számítási feladatot tervez között.

Az Azure Search kínál néhány határok bérlők adatait és a számítási feladatok elkülönítése használható.

## <a name="modeling-multitenancy-with-azure-search"></a>Az Azure Search szolgáltatással a több-bérlős modellezés
Egy több-bérlős forgatókönyvben az alkalmazás fejlesztőjének egy vagy több keresési szolgáltatást használ fel, és felosztani a bérlőik számára többek között a szolgáltatások, az indexek vagy mindkettőt. Az Azure Search rendelkezik néhány gyakori minták, ha egy több-bérlős forgatókönyvben modellezés:

1. *Index bérlőnként:* Minden bérlőnek a saját index belül egy keresési szolgáltatás, amely a többi bérlő van megosztva.
2. *Bérlőnként szolgáltatás:* Minden bérlő rendelkezik a saját dedikált Azure Search szolgáltatást kínál az adatok és a számítási feladatok elkülönítése legmagasabb szintű.
3. *Mindkét vegyesen:* Nagyobb, több aktív bérlők dedikált szolgáltatások vannak rendelve, amíg a kisebb bérlőknek vannak hozzárendelve a megosztott szolgáltatások az egyedi indexek.

## <a name="1-index-per-tenant"></a>1. Bérlőnként indexelése
![Az index bérlőnkénti modell egy portrayal](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

Az index bérlőnkénti modell több bérlő foglalhat el egyetlen Azure Search szolgáltatás ahol minden egyes bérlő saját indexszel rendelkezik-e.

Bérlők számára az adatok elkülönítését érhet el, mivel minden keresési kérelmek és a dokumentum műveletek adják ki az Azure Search-index szintjén. Az alkalmazási rétegre van a szükséges figyelése is kezelni az erőforrásokat a szolgáltatási szinten az összes bérlőre kiterjedő irányítani a forgalmat a különböző bérlők számára a megfelelő indexek.

A bérlőnkénti index modell kulcsattribútum arra, hogy az az alkalmazás fejlesztőjének túlléphető a kapacitás, a keresési szolgáltatásnak az alkalmazás bérlők között. Ha a bérlők a számítási feladatok eloszlása egyenletlen rendelkezik, a bérlők számára optimális kombinációja egy keresési szolgáltatás indexek magas aktív, az erőforrás-igényes a bérlőknek számos befogadásához közben egyszerre egy hosszú tail kisebb szét lehetnek osztva aktív bérlők számára. Kompromisszumot kötni a rendszer a modell a nem képes kezelni az olyan helyzetekben, ahol minden egyes bérlő egyidejűleg nagyon aktív.

Az index bérlőnkénti modell alapjául szolgáló változót a költségmodell, ahol az egész Azure Search szolgáltatást vásárolt induló, majd ezt követően kitöltését bérlőkkel. Ez lehetővé teszi a fel nem használt kapacitás kísérletek és az ingyenes fiók ki kell jelölni.

Egy globális lefedettséggel rendelkező alkalmazások a bérlőnkénti index modell nem lehet a leghatékonyabban. Ha egy alkalmazás bérlők vannak elosztva a világ, egy különálló szolgáltatás szükséges minden olyan régióhoz, ami előfordulhat, hogy ismétlődő költségek azok között lehet.

Az Azure Search szolgáltatással a méretezési csoport, az egyedi indexek és az indexek száma nő. Ha egy megfelelő árképzési szint ki van választva, partíciókat és -replikákat is hozzáadhatók a teljes keresési szolgáltatást, amikor egy egyedi index a szolgáltatásban túl hosszúvá, storage és a forgalom tekintetében.

Indexek száma túl hosszúvá egy egyetlen szolgáltatást, ha egy másik szolgáltatásnak van, úgy kell létrehozni, hogy megfeleljen az új bérlők számára. Ha az indexek a keresési szolgáltatások helyezhetők át, az új szolgáltatással bővül, az adatokat az indexből manuálisan átmásolni egy index a másik, mert az Azure Search nem engedélyezett egy index áthelyezésének rendelkezik.

## <a name="2-service-per-tenant"></a>2. Bérlőnként szolgáltatás
![A szolgáltatás bérlőnkénti modell egy portrayal](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

A szolgáltatás bérlőnként felügyelt architektúrában minden bérlő rendelkezik a saját keresési szolgáltatás.

Ebben a modellben az alkalmazás eléri-e a legnagyobb szintű a bérlők elkülönítését. Minden szolgáltatáshoz rendelkezik dedikált dokumentumtárolási és adattovábbítási kapacitással keresési kérelmet, valamint a különálló API-kulcsok kezelésére.

Alkalmazások, ahol minden egyes bérlő rendelkezik egy nagy erőforrás-igényű, vagy a számítási feladatok kis változékonyságát bérlő bérlői rendelkezik a bérlőnkénti szolgáltatási modell különböző bérlők számítási feladatok nem megosztott erőforrások, akkor egy hatékony választani.

Egy bérlő modell szolgáltatást is kínál az előnye, hogy egy előre jelezhető, rögzített költségmodellt. Csak akkor adja meg, a bérlő azonban a cost bérlőnkénti magasabb, mint az index bérlőnkénti modell, nincs nincs kezdeti beruházást, egy teljes search-szolgáltatásban.

A bérlőnkénti szolgáltatási modell egy-egy globális erőforrás-igényű alkalmazásokhoz hatékony lehetőség. Földrajzilag elosztott bérlőkkel könnyedék minden bérlő szolgáltatás rendelkezik a megfelelő régióban található.

A legnagyobb kihívás méretezése, ez a minta akkor keletkezik, amikor az egyes bérlők elszaporodó szolgáltatást. Az Azure Search jelenleg nem támogatja a tarifacsomagot a keresési szolgáltatás frissítése, ezért minden adatot meg kell manuálisan kell átmásolni egy új szolgáltatás.

## <a name="3-mixing-both-models"></a>3. Mindkét modellt keverése
Egy másik minta a több-bérlős modellezés van keverése bérlőnkénti index és a szolgáltatás bérlőnkénti stratégiák.

A két mintát úgy, egy alkalmazás legnagyobb bérlők lefoglalhatja dedikált szolgáltatások közben a hosszú kisebb, kevésbé aktív, kisebb bérlők indexek lefoglalhatja a megosztott szolgáltatáshoz. Ez a modell biztosítja, hogy a legnagyobb bérlők számára a kisebb bérlőknek szembeni bármely zajos szomszédok a szolgáltatás folyamatosan magas teljesítmény.

Azonban ez a stratégia megvalósításához támaszkodik előretekintés előrejelzésére, mely a bérlők és a egy indexet a megosztott szolgáltatáshoz dedikált szolgáltatás lesz szükség. Alkalmazás összetettségét is több-bérlős modellek felügyelnie egyenes arányban növekszik.

## <a name="achieving-even-finer-granularity"></a>Még nagyobb részletességgel elérése
A fenti tervezési minták több-bérlős forgatókönyvek az Azure Search modellezésére azt feltételezik, hogy egy egységes hatókörhöz, ahol minden egyes bérlő egy alkalmazás egy teljes példánya. Alkalmazások azonban néha kezelhetik a sok kisebb hatókörét.

Ha bérlőnkénti szolgáltatás és a bérlőnkénti index modellek nem elég kis hatókörök, egy granularitási még nagyobb fokú eléréséhez index modellezésére.

Ahhoz, hogy egy egyetlen index eltérően viselkednek a különböző ügyfél-végpontok, egy mezőt az indexbe, amely egy bizonyos értéket minden lehetséges ügyfél is hozzáadhatók. Minden alkalommal, amikor egy ügyfél hívja lekérdezésére és módosítására index, Azure Search a kódot az ügyfélalkalmazás, adja meg a megfelelő értéket a mezőhöz, használja az Azure Search [szűrő](https://msdn.microsoft.com/library/azure/dn798921.aspx) képesség a lekérdezések során.

Ezzel a módszerrel külön felhasználói fiókok, külön jogosultsági szintek esetén funkció eléréséhez használható, és akár teljesen különálló alkalmazásokat.

> [!NOTE]
> A fent leírt módszer használatával több bérlő kiszolgálására egy egyetlen index konfigurálása hatással van az a relevancia alapján végzett keresés eredményeit. Keresés relevancia alapján végzett pontszámok arra az esetre vonatkoznak az index-szintű hatókörben, nem bérlői szintű hatókör, így az összes bérlő adatait a relevancia alapján végzett pontszámok alapul szolgáló statisztikáit, például a kifejezés gyakoriság a részét képezik.
> 
> 

## <a name="next-steps"></a>További lépések
Az Azure Search számos alkalmazás, meggyőző döntés, [találhat további információt a szolgáltatási hatékony képesség](https://aka.ms/whatisazsearch). A különböző kialakítási minták a több-bérlős alkalmazások kiértékelését, vegye figyelembe a [különböző tarifacsomagok](https://azure.microsoft.com/pricing/details/search/) és a megfelelő [szolgáltatási korlátozásaival](search-limits-quotas-capacity.md) leginkább megfelelő Azure Search-alkalmazás igazítás számítási feladatok és architektúrákat, bármilyen méretűek.

Minden olyan kérdések az Azure Search és a több-bérlős helyzetekben lehet irányítani azuresearch_contact@microsoft.com.

