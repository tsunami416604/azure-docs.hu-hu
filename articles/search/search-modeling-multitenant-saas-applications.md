---
title: Bérlős modellezése egy szolgáltatásbeli tartalom elkülönítéséhez – Azure Search
description: Ismerje meg a több-bérlős SaaS-alkalmazások általános kialakítási mintáit Azure Search használata közben.
manager: nitinme
author: LiamCavanagh
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: liamca
ms.openlocfilehash: 8b7538da41241f005298537c2969e5fce72b3c38
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182230"
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-search"></a>Tervezési minták több-bérlős SaaS-alkalmazásokhoz és Azure Search
Egy több-bérlős alkalmazás az egyik, amely ugyanazokat a szolgáltatásokat és képességeket biztosítja a bérlők számára, akik nem láthatják és nem oszthatják meg más bérlők információit. Ez a dokumentum a Azure Search-vel létrehozott több-bérlős alkalmazások bérlői elkülönítési stratégiáit tárgyalja.

## <a name="azure-search-concepts"></a>Azure Search fogalmak
A szolgáltatásként nyújtott keresési megoldás lehetővé teszi Azure Search, hogy a fejlesztők gazdag keresési funkciókat adjanak az alkalmazásokhoz anélkül, hogy bármilyen infrastruktúrát kellene kezelniük, vagy az adatok lekérése nélkül kellene foglalkoznia. A szolgáltatás feltölti az adattárat, majd a felhőben tárolja azokat. A Azure Search API-hoz egyszerű kérelmeket használva az adatai módosíthatók és kereshetők. A szolgáltatás áttekintése [ebben a cikkben](https://aka.ms/whatisazsearch)található. A tervezési minták megtárgyalása előtt fontos megérteni, hogy milyen fogalmakat Azure Search.

### <a name="search-services-indexes-fields-and-documents"></a>Szolgáltatások, indexek, mezők és dokumentumok keresése
Azure Search használatakor az egyik a *keresési szolgáltatásra*van előfizetve. Az adatAzure Searchba való feltöltéskor a rendszer a keresési szolgáltatás egy indexében tárolja azt. Egy szolgáltatáson belül több index is lehet. Az adatbázisok ismerős fogalmait használva a keresési szolgáltatás egy adatbázishoz hasonlítható, míg a szolgáltatásban lévő indexek az adatbázisban lévő táblákhoz is hasonlóvá lehetnek.

A keresési szolgáltatásban található minden egyes index saját sémával rendelkezik, amelyet számos testreszabható *mező*határoz meg. Az adatmennyiséget külön *dokumentumok*formájában adja hozzá a rendszer egy Azure Search indexhez. Minden dokumentumot fel kell tölteni egy adott indexbe, és hozzá kell férnie az index sémájának. Amikor Azure Search használatával keres adatokat, a teljes szöveges keresési lekérdezések egy adott indexre lesznek kibocsátva.  Ha össze szeretné hasonlítani ezeket a fogalmakat egy adatbázishoz, a mezőket a táblázat oszlopaihoz lehet hasonlítani, és a dokumentumok a sorokhoz is összehasonlíthatók.

### <a name="scalability"></a>Méretezhetőség
A standard szintű díjszabásban Azure Search [](https://azure.microsoft.com/pricing/details/search/) szolgáltatások két dimenzióban méretezhetők: tárterület és rendelkezésre állás.

* A partíciók hozzáadhatók a keresési szolgáltatás tárterületének növeléséhez.
* A replikák hozzáadhatók a szolgáltatásokhoz, így növelhetik a keresési szolgáltatás által kezelhető kérelmek átviteli sebességét.

A partíciók és replikák hozzáadásával és eltávolításával lehetővé válik a keresési szolgáltatás kapacitása, hogy az adatok mennyisége és az alkalmazás által igényelt forgalom növekedni fog. Ahhoz, hogy egy keresési szolgáltatás elér egy olvasási [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)-t, két replikára van szükség. Ahhoz, hogy egy szolgáltatás olvasási és írási [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)-t érjen el, három replikára van szükség.

### <a name="service-and-index-limits-in-azure-search"></a>A szolgáltatás és az index korlátai a Azure Search
Azure Search különböző [díjszabási szintjei](https://azure.microsoft.com/pricing/details/search/) vannak, a rétegek mindegyike különböző [korlátozásokkal és kvótákkal](search-limits-quotas-capacity.md)rendelkezik. A korlátozások némelyike a szolgáltatás szintjén van, néhány pedig az index szintjén, néhány pedig a partíció szintjén.

|  | Alapszintű | Standard1 | Standard2 | Standard3 | Standard3 HD |
| --- | --- | --- | --- | --- | --- |
| Replikák maximális száma szolgáltatás szerint |3 |12 |12 |12 |12 |
| Partíciók maximális száma szolgáltatás szerint |1 |12 |12 |12 |3 |
| Keresési egységek (replikák * partíciók) maximális száma szolgáltatásban |3 |36 |36 |36 |36 (legfeljebb 3 partíció) |
| Tárterület maximális száma szolgáltatás alapján |2 GB |300 GB |1,2 TB |2,4 TB |600 GB |
| Tárolók maximális száma partíción |2 GB |25 GB |100 GB |200 GB |200 GB |
| Indexek maximális száma (szolgáltatás) |5 |50 |200 |200 |3000 (max. 1000 indexek/partíció) |

#### <a name="s3-high-density"></a>Nagy sűrűségű S3
Azure Search S3 árképzési szinten a nagy sűrűségű (HD) üzemmódot kifejezetten több-bérlős forgatókönyvek számára tervezték. Sok esetben az egyszerűség és a költséghatékonyság előnyeinek elérése érdekében számos kisebb bérlőt kell támogatni egyetlen szolgáltatásban.

Az S3 HD lehetővé teszi, hogy a sok kisméretű indexet egyetlen keresési szolgáltatás felügyelete alá lehessen csomagolni azáltal, hogy a partíciók használatával kibővíti az indexeket, így több indexet is tárolhat egyetlen szolgáltatásban.

Konkrétan egy S3-szolgáltatásnak 1 és 200 index között kell lennie, amelyek együttesen akár 1 400 000 000 dokumentumot is tárolhatnak. Az S3 HD viszont lehetővé teszi, hogy az egyes indexek csak a 1 000 000-es dokumentumokra lépjenek, de a partíción (akár 3000-ig) akár 1000 indexeket is kezelhetnek, a teljes összegű 200 000 000-összeggel (legfeljebb 600 000 000).

## <a name="considerations-for-multitenant-applications"></a>Több-bérlős alkalmazások szempontjai
A több-bérlős alkalmazásoknak hatékonyan kell kiosztaniuk az erőforrásokat a bérlők között, miközben a különböző bérlők között bizonyos fokú adatvédelemre van szükség. Az ilyen alkalmazások architektúrájának tervezésekor néhány szempontot figyelembe kell venni:

* *Bérlő elkülönítése:* Az alkalmazás fejlesztőknek megfelelő intézkedéseket kell tenniük annak biztosításához, hogy a bérlők ne legyenek jogosulatlan vagy nemkívánatos hozzáférésük más bérlők adatához. Az adatvédelem szempontjából a bérlők elkülönítési stratégiái a megosztott erőforrások hatékony kezelését és a zajos szomszédok elleni védelmet igénylik.
* *Felhőalapú erőforrás díja:* A többi alkalmazáshoz hasonlóan a szoftveres megoldásoknak is versenyképesnek kell lenniük a több-bérlős alkalmazások összetevőjeként.
* *Egyszerű műveletek:* Több-bérlős architektúra fejlesztésekor fontos szempont az alkalmazás működésére és összetettségére gyakorolt hatás. A Azure Search [99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/)-os SLA-val rendelkezik.
* *Globális helyigény:* Előfordulhat, hogy a több-bérlős alkalmazásoknak hatékonyan kell kiszolgálni a világ bármely részén elosztott bérlőket.
* *Méretezhetőség* Az alkalmazás-fejlesztőknek meg kell fontolniuk, hogyan egyeztethetők össze az alkalmazások kellően alacsony szintjének fenntartása és az alkalmazás megtervezése között a bérlők számával, valamint a bérlők adatai és a számítási feladatok méretétől függően.

Azure Search a bérlők adatai és a számítási feladatok elkülönítésére szolgáló néhány határt biztosít.

## <a name="modeling-multitenancy-with-azure-search"></a>Bérlős modellezése Azure Search
Több-bérlős forgatókönyv esetén az alkalmazás fejlesztői egy vagy több keresési szolgáltatást használnak, és a bérlőket a szolgáltatások, az indexek vagy mindkettő között osztják el. Azure Search a több-bérlős forgatókönyv modellezésének néhány gyakori mintája van:

1. *Index/bérlő:* Minden bérlő saját indexet tartalmaz a más Bérlővel megosztott keresési szolgáltatáson belül.
2. *Szolgáltatás/bérlő:* Minden bérlő saját dedikált Azure Search szolgáltatást kínál, amely a legmagasabb szintű adatmennyiséget és a munkaterhelések elkülönítését kínálja.
3. *Kettő kombinációja:* Nagyobb, aktívabb bérlők dedikált szolgáltatásokat kapnak, míg a kisebb bérlők egyedi indexeket kapnak a megosztott szolgáltatásokon belül.

## <a name="1-index-per-tenant"></a>1. Index/bérlő
![Az index/bérlői modell ábrázolása](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

Egy index/bérlői modellben több bérlő is foglal le egyetlen Azure Search szolgáltatást, ahol minden egyes bérlő saját indextel rendelkezik.

A bérlők az adatok elkülönítését érik el, mivel az összes keresési kérelem és dokumentum-művelet Azure Search index szintjén van kiállítva. Az alkalmazási rétegben arra van szükség, hogy a különböző bérlők forgalmát a megfelelő indexekre irányítsa, miközben a szolgáltatási szinten lévő erőforrásokat is az összes bérlőn keresztül kezeli.

Az index/bérlői modell egyik fő attribútuma az, hogy az alkalmazás fejlesztője előfizesse a keresési szolgáltatás kapacitását az alkalmazás bérlői között. Ha a bérlők a számítási feladatok egyenlőtlen eloszlásával rendelkeznek, a bérlők optimális kombinációja a keresési szolgáltatás indexei között terjeszthető ki, így számos igen aktív, erőforrás-igényes bérlőt használhat, miközben a hosszú farok kevesebb aktív bérlők. A kikapcsolás a modell nem képes olyan helyzetek kezelésére, amikor az egyes bérlők egyidejűleg igen aktívak.

Az index/bérlői modell a változó szintű modell alapját képezi, ahol a teljes Azure Search szolgáltatás megvásárolható, és ezt követően a bérlők töltik fel. Ez lehetővé teszi, hogy a fel nem használt kapacitás kijelölhető legyen a próbaverziók és az ingyenes fiókok számára.

Globális helyigényű alkalmazások esetében előfordulhat, hogy az index/bérlői modell nem a leghatékonyabb. Ha egy alkalmazás bérlői szét vannak osztva a világ bármely régiójában, külön szolgáltatásra lehet szükség az egyes régiók esetében, amelyek mindegyike megismétli a költségeket.

Azure Search lehetővé teszi az egyéni indexek és a növekedő indexek teljes számának méretezését. Ha a megfelelő árképzési szintet választja, akkor a partíciók és a replikák a teljes keresési szolgáltatásba felvehetők, ha a szolgáltatáson belüli egyes indexek túl nagy mennyiségű tárterületet vagy forgalmat foglalnak magukban.

Ha az indexek teljes száma túl nagyra nő egyetlen szolgáltatás esetében, egy másik szolgáltatást kell kiépíteni az új bérlők befogadásához. Ha az indexeket új szolgáltatások hozzáadásakor kell áthelyezni a keresési szolgáltatások között, az indexből származó adatoknak kézzel kell átmásolnia az egyik indexből a másikba, mivel a Azure Search nem teszi lehetővé az index áthelyezését.

## <a name="2-service-per-tenant"></a>2. Szolgáltatás/bérlő
![A szolgáltatás/bérlői modell ábrázolása](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

A szolgáltatás-bérlői architektúrában minden bérlő saját keresési szolgáltatással rendelkezik.

Ebben a modellben az alkalmazás a bérlők számára a maximális elkülönítési szintet éri el. Minden szolgáltatás dedikált tárterületet és átviteli sebességet biztosít a keresési kérések kezelésére, valamint különálló API-kulcsokra.

Azokban az alkalmazásokban, amelyekben az egyes bérlők nagy helyigénysel rendelkeznek, vagy a munkaterhelésnek a bérlőtől a bérlőig kevés a változékonysága, a szolgáltatás-bérlői modell olyan hatékony megoldás, mint a különböző bérlők munkaterhelései között.

A bérlői modelleken alapuló szolgáltatás a kiszámítható és rögzített költségtakarékos modell előnyeit is biztosítja. A teljes keresési szolgáltatásban nincs kezdeti beruházás, amíg a bérlő kitölti azt, azonban a bérlői díj magasabb, mint az index-bérlői modell.

A szolgáltatás/bérlő modell hatékony választás a globális helyigényű alkalmazások számára. Földrajzilag elosztott bérlők esetében a bérlők szolgáltatásait egyszerűen megoszthatja a megfelelő régióban.

A minta skálázásának kihívásai akkor jelentkeznek, amikor az egyes bérlők kibővítik a szolgáltatást. A Azure Search jelenleg nem támogatja a keresési szolgáltatás díjszabási szintjét, ezért az összes adatmennyiséget manuálisan kell átmásolni egy új szolgáltatásba.

## <a name="3-mixing-both-models"></a>3. Mindkét modell keverése
A modellezési bérlős egy másik mintázata a bérlői és a szolgáltatás-bérlői stratégiák keveredését is felkeveri.

A két minta összekeverésével az alkalmazás legnagyobb bérlői dedikált szolgáltatásokat foglalhatnak el, míg a kevésbé aktív, kisebb bérlők egy megosztott szolgáltatásban foglalhatnak le indexeket. Ez a modell biztosítja, hogy a legnagyobb bérlők következetesen nagy teljesítményt biztosítanak a szolgáltatástól, miközben a kisebb bérlők számára a zajos szomszédoktól való védekezést segítik.

Ennek a stratégiának a megvalósítása azonban előrelátó módon feltételezi, hogy mely bérlők igényelnek dedikált szolgáltatást, illetve egy megosztott szolgáltatásbeli indexet. Az alkalmazások összetettsége a két bérlős-modell kezelésének szükségességével nő.

## <a name="achieving-even-finer-granularity"></a>Még finomabb részletesség elérése
A fenti tervezési minták több-bérlős forgatókönyvek modellezésére Azure Search feltételezik, hogy az egyes bérlők egy alkalmazás teljes példánya. Az alkalmazások azonban néha több kisebb hatókört is kezelhetnek.

Ha a szolgáltatás-/bérlői és az index/bérlői modellek nem eléggé kis hatókörű, az index modellezése még finomabb részletességi fokú részletességgel valósítható meg.

Ahhoz, hogy egyetlen index eltérő módon viselkedjen a különböző ügyféloldali végpontok esetében, egy mező hozzáadható egy olyan indexhez, amely minden lehetséges ügyfél esetében egy bizonyos értéket jelöl ki. Minden alkalommal, amikor egy ügyfél meghívja a Azure Search egy index lekérdezésére vagy módosítására, az ügyfélalkalmazás kódja megadja a mező megfelelő értékét az Azure Search [szűrési](https://msdn.microsoft.com/library/azure/dn798921.aspx) funkciója alapján a lekérdezési időpontnál.

Ezzel a módszerrel különböző felhasználói fiókok, különálló jogosultsági szintek és akár teljesen különálló alkalmazások funkciói is elérhetők.

> [!NOTE]
> A fent ismertetett módszer használatával egyetlen indexet állíthat be több bérlő kiszolgálására, ami befolyásolja a keresési eredmények relevanciáját. A keresés relevanciás pontszámait index-szintű hatókörre számítjuk ki, nem pedig a bérlői szintű hatókörre, így az összes bérlő adatai bekerülnek a releváns pontszámok alapjául szolgáló statisztikai adatokba, például a kifejezés gyakoriságával.
> 
> 

## <a name="next-steps"></a>További lépések
Azure Search számos alkalmazás számára meggyőző választás, [További információ a szolgáltatás robusztus képességeiről](https://aka.ms/whatisazsearch). A több-bérlős alkalmazások különböző tervezési mintáinak kiértékelése során vegye figyelembe a [különböző díjszabási](https://azure.microsoft.com/pricing/details/search/) csomagokat és a megfelelő [szolgáltatási korlátokat](search-limits-quotas-capacity.md) a legmegfelelőbb személyre szabott Azure Search az alkalmazások számítási feladatainak és architektúráinak méretének megfelelően.

Azure Search és több-bérlős forgatókönyvekkel kapcsolatos kérdések a azuresearch_contact@microsoft.comkövetkezőre irányíthatók:.

