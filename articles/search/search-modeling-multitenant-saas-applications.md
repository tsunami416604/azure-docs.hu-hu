---
title: Több-bérlős és tartalomelkülönítés
titleSuffix: Azure Cognitive Search
description: Ismerje meg a több-bérlős SaaS-alkalmazások közös tervezési mintáit az Azure Cognitive Search használata közben.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d37abd1b5d212c3d920cb68b6236029b2112ae24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113270"
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-cognitive-search"></a>Több-bérlős SaaS-alkalmazások és az Azure Cognitive Search tervezési mintáinak tervezése
A több-bérlős alkalmazás az egyik, amely ugyanazokat a szolgáltatásokat és képességeket, hogy tetszőleges számú bérlők, akik nem látják vagy nem osztják meg az adatokat bármely más bérlő. Ez a dokumentum ismerteti a bérlők elkülönítési stratégiák több-bérlős alkalmazások az Azure Cognitive Search.

## <a name="azure-cognitive-search-concepts"></a>Az Azure Cognitive Search fogalmai
A szolgáltatásként való keresésként való keresésként az Azure Cognitive Search lehetővé teszi a fejlesztők számára, hogy gazdag keresési élményeket adjanak hozzá az alkalmazásokhoz anélkül, hogy bármilyen infrastruktúrát kezelne, vagy az információ-lekérés szakértőjévé válnak. Az adatok feltöltése a szolgáltatásba, majd a felhőben tárolódik. Az Azure Cognitive Search API-hoz érkező egyszerű kérelmek használatával az adatok ezután módosíthatók és kereshetők. A szolgáltatás áttekintése ebben a [cikkben](https://aka.ms/whatisazsearch)található. Mielőtt a tervezési minták megvitatása, fontos, hogy megismerjék az Azure Cognitive Search egyes fogalmak.

### <a name="search-services-indexes-fields-and-documents"></a>Keresési szolgáltatások, indexek, mezők és dokumentumok
Az Azure Cognitive Search használatakor előfizet egy *keresési szolgáltatásra.* Az azure cognitive search-be feltöltött adatok egy *indexben* tárolják a keresési szolgáltatáson belül. Egyetlen szolgáltatáson belül több index is lehet. Az adatbázisok ismert fogalmainak használatához a keresési szolgáltatás egy adatbázishoz hasonlítható, míg a szolgáltatáson belüli indexek az adatbázistábláihoz hasonlóan.

A keresési szolgáltatáson belül minden index saját sémával rendelkezik, amelyet számos testreszabható *mező*határoz meg. Az adatok az Azure Cognitive Search indexéhez adottadatokat egyedi *dokumentumok*formájában. Minden dokumentumot fel kell tölteni egy adott indexbe, és illeszkednie kell az index sémájához. Az Azure Cognitive Search használatával történő keresés során a teljes szöveges keresési lekérdezések egy adott indexhez kerülnek.  Ha ezeket a fogalmakat össze szeretné hasonlítani egy adatbázis éváival, a mezők a tábla oszlopaihoz hasonlíthatók, a dokumentumok pedig sorokhoz hasonlíthatók.

### <a name="scalability"></a>Méretezhetőség
A standard [díjszabási szint](https://azure.microsoft.com/pricing/details/search/) bármely Azure Cognitive Search szolgáltatása két dimenzióban skálázható: tárolás és rendelkezésre állás.

* *A keresési* szolgáltatások tárolásának növelése érdekében partíciókadhatók.
* *Replikák adhatók* hozzá egy szolgáltatáshoz a keresési szolgáltatás által kezelhető kérelmek átviteli értékének növelése érdekében.

Partíciók és replikák hozzáadása és eltávolítása lehetővé teszi, hogy a keresési szolgáltatás kapacitása növekszik az adatok mennyisége és az alkalmazás igényeinek forgalma. Ahhoz, hogy egy keresési szolgáltatás elérhesse az olvasási [SLA-t,](https://azure.microsoft.com/support/legal/sla/search/v1_0/)két replikát igényel. Ahhoz, hogy egy szolgáltatás írás-olvasási [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)eléréséhez három replikát igényel.

### <a name="service-and-index-limits-in-azure-cognitive-search"></a>Szolgáltatási és indexkorlátozások az Azure Cognitive Search szolgáltatás- és indexkorlátaiban
Az Azure Cognitive Search néhány különböző [tarifacsomag](https://azure.microsoft.com/pricing/details/search/) közül van, mindegyik szint különböző [korlátokkal és kvótákkal rendelkezik.](search-limits-quotas-capacity.md) Ezek közül a korlátok közül néhány a szolgáltatás szintjén, néhány az index szintjén, és néhány a partíció szintjén.

|  | Basic | 1. szabvány | 2. szabvány | Normál 3 | Standard3 HD |
| --- | --- | --- | --- | --- | --- |
| Replikák maximális replikái szolgáltatásonként |3 |12 |12 |12 |12 |
| Maximális partíciók szolgáltatásonként |1 |12 |12 |12 |3 |
| Maximális keresési egységek (replikák*partíciók) szolgáltatásonként |3 |36 |36 |36 |36 (max 3 partíció) |
| Maximális tárhely szolgáltatásonként |2 GB |300 GB |1,2 TB |2,4 TB |600 GB |
| Maximális tárhely partíciónként |2 GB |25 GB |100 GB |200 GB |200 GB |
| Maximális indexek szolgáltatásonként |5 |50 |200 |200 |3000 (max. 1000 index/partíció) |

#### <a name="s3-high-density"></a>S3 Nagy sűrűségű"
Az Azure Cognitive Search S3 tarifacsomagjában van egy lehetőség a nagy sűrűségű (HD) mód kifejezetten több-bérlős forgatókönyvek. Sok esetben szükség van, hogy támogassa a nagyszámú kisebb bérlők egyetlen szolgáltatás előnyeinek elérése érdekében az egyszerűség és a költséghatékonyság.

Az S3 HD lehetővé teszi, hogy a sok kis index egyetlen keresési szolgáltatás felügyelete alatt legyen csomagolva azáltal, hogy partíciók használatával kiosztja az indexeket, hogy több indexet tudjon üzemeltetni egyetlen szolgáltatásban.

Konkrétan, egy S3 szolgáltatás lehetett között 1 és 200 indexek, amelyek együttesen befogadó akár 1,4 milliárd dokumentumot. Az S3 HD ezzel szemben lehetővé tenné, hogy az egyes indexek csak 1 millió dokumentumot menjenek fel, de partíciónként akár 1000 indexet is képes kezelni (szolgáltatásonként akár 3000-ig), partíciónként 200 millió dokumentumszámmal (szolgáltatásonként akár 600 millió).

## <a name="considerations-for-multitenant-applications"></a>A több-bérlős alkalmazások szempontjai
A több-bérlős alkalmazásoknak hatékonyan kell elosztaniuk az erőforrásokat a bérlők között, miközben meg kell őrizniük a különböző bérlők közötti adatvédelem bizonyos szintjét. Az ilyen alkalmazások architektúrájának tervezésekor néhány szempontot figyelembe kell venni:

* *Bérlő elkülönítése:* Az alkalmazásfejlesztőknek meg kell tenniük a megfelelő intézkedéseket annak biztosítására, hogy egyetlen bérlő se férhessen hozzá jogosulatlanul vagy nem kívánt anno más bérlők adataihoz. Az adatvédelmi szempontokon túl a bérlők elkülönítési stratégiái megkövetelik a megosztott erőforrások hatékony kezelését és a zajos szomszédok elleni védelmet.
* *Felhőbeli erőforrás költsége:* Mint minden más alkalmazás, a szoftvermegoldásoknak is költségversenyképesnek kell maradniuk a több-bérlős alkalmazások összetevőjeként.
* *Könnyű kezelhetőség:* A több-bérlős architektúra kialakításakor az alkalmazás műveleteire és összetettségére gyakorolt hatás fontos szempont. Az Azure Cognitive Search [99,9%-os SLA-val](https://azure.microsoft.com/support/legal/sla/search/v1_0/)rendelkezik.
* *Globális lábnyom:* Több-bérlős alkalmazások szükség lehet hatékonyan kiszolgálni a bérlők, amelyek a világ minden tájáról elosztott.
* *Méretezhetőség:* Az alkalmazásfejlesztőknek meg kell fontolniuk, hogyan egyeztetik össze a kellően alacsony szintű alkalmazásösszetettségét és az alkalmazás tervezése a bérlők számával való méretezés, valamint a bérlők adatainak és munkaterhelésének mérete között.

Az Azure Cognitive Search néhány olyan határokat kínál, amelyek a bérlők adatainak és munkaterhelésének elkülönítésére használhatók.

## <a name="modeling-multitenancy-with-azure-cognitive-search"></a>Modellezés többdimenziós azure kognitív kereséssel
Több-bérlős forgatókönyv esetén az alkalmazásfejlesztő egy vagy több keresési szolgáltatást használ fel, és felosztja a bérlőket a szolgáltatások, az indexek vagy mindkettő között. Az Azure Cognitive Search néhány gyakori minta modellezése során egy több-bérlős forgatókönyv:

1. *Index bérlőnként:* Minden bérlő saját indexegy keresési szolgáltatáson belül, amely más bérlőkkel megosztott.
2. *Szolgáltatás bérlőnként:* Minden bérlő rendelkezik saját dedikált Azure Cognitive Search szolgáltatással, amely a legmagasabb szintű adatokat és a munkaterhelés elkülönítését kínálja.
3. *A kettő keveréke:* A nagyobb, aktívabb bérlők dedikált szolgáltatásokat kapnak, míg a kisebb bérlők a megosztott szolgáltatásokon belül egyéni indexeket kapnak.

## <a name="1-index-per-tenant"></a>1. Index bérlőnként
![A bérlőnkénti indexmodell ábrázolása](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

Egy index-bérlős modellben több bérlő egyetlen Azure Cognitive Search szolgáltatást foglalnak el, ahol minden bérlő saját indexszel rendelkezik.

A bérlők azért érnek el adatelkülönítést, mert az Azure Cognitive Search indexszintjén minden keresési és dokumentumművelet indexszinten van kiadva. Az alkalmazásrétegben szükség van annak, hogy a különböző bérlők forgalmát a megfelelő indexek, miközben az erőforrások kezelése a szolgáltatás szintjén az összes bérlő között.

A bérlőnkénti indexmodell egyik kulcsattribútuma az, hogy az alkalmazásfejlesztő túliratkozhat egy keresési szolgáltatás kapacitásán az alkalmazás bérlői között. Ha a bérlők egyenlőtlen számítási feladatok elosztása, a bérlők optimális kombinációja lehet elosztani a keresési szolgáltatás indexek elhelyezésére számos rendkívül aktív, erőforrás-igényes bérlők, miközben egyszerre szolgáló hosszú farok kevesebb aktív bérlők számára. A kompromisszum az, hogy a modell nem képes kezelni azokat a helyzeteket, amelyekben minden bérlő egyidejűleg rendkívül aktív.

A bérlőnkénti indexmodell egy változó költségmodell alapját képezi, ahol egy teljes Azure Cognitive Search-szolgáltatást előre megvásárolnak, majd ezt követően bérlőkkel töltik fel. Ez lehetővé teszi, hogy a kihasználatlan kapacitást próbaverziókhoz és ingyenes fiókokhoz jelöljék ki.

A globális lábnyommal rendelkező alkalmazások esetében előfordulhat, hogy a bérlőnkénti indexmodell nem a leghatékonyabb. Ha egy alkalmazás bérlői világszerte vannak elosztva, minden régióhoz külön szolgáltatásra lehet szükség, amely megkettőzheti a költségeket mindegyikben.

Az Azure Cognitive Search lehetővé teszi az egyes indexek és az indexek teljes számának növekedését. Ha megfelelő tarifacsomagot választ, partíciók és replikák adhatók hozzá a teljes keresési szolgáltatáshoz, ha a szolgáltatáson belüli egyedi index a tárolás vagy a forgalom tekintetében túl nagyra nő.

Ha az indexek teljes száma túl nagy egy szolgáltatáshoz, egy másik szolgáltatást kell kiépíteni az új bérlők befogadására. Ha az indexeket át kell helyezni a keresési szolgáltatások között, amikor új szolgáltatásokat ad hozzá, az indexből származó adatokat manuálisan kell átmásolni az egyik indexből a másikba, mivel az Azure Cognitive Search nem teszi lehetővé az index áthelyezését.

## <a name="2-service-per-tenant"></a>2. Szolgáltatás bérlőnként
![A bérlőnkénti szolgáltatás modell ábrázolása](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

A bérlőnkénti szolgáltatásarchitektúrában minden bérlő saját keresési szolgáltatással rendelkezik.

Ebben a modellben az alkalmazás eléri a bérlők elkülönítésének maximális szintjét. Minden szolgáltatás dedikált tárolási és átviteli kapacitással rendelkezik a keresési kérelmek kezeléséhez, valamint a külön API-kulcsokkezeléséhez.

Olyan alkalmazások esetében, ahol minden bérlő nagy alapterületű, vagy a számítási feladatok kevés változékonyság a bérlőtől a bérlőig, a szolgáltatás-bérlőnkénti modell hatékony választás, mivel az erőforrások nem vannak megosztva a különböző bérlők számítási feladatok között.

A bérlői modellenkénti szolgáltatás egy kiszámítható, fix költségű modell előnyeit is kínálja. Nincs előzetes befektetés egy teljes keresési szolgáltatásban, amíg nincs bérlő, amely kitölti, azonban a bérlőnkénti költség magasabb, mint a bérlőnkénti indexmodell.

A bérlőnkénti szolgáltatásmodell hatékony választás a globális lábnyommal rendelkező alkalmazások számára. Földrajzilag elosztott bérlők, könnyen rendelkezik az egyes bérlői szolgáltatás a megfelelő régióban.

A minta méretezésével szembeni kihívások akkor merülnek fel, amikor az egyes bérlők kinövik a szolgáltatásukat. Az Azure Cognitive Search jelenleg nem támogatja a keresési szolgáltatás tarifacsomagjának frissítését, így az összes adatot manuálisan kell átmásolni egy új szolgáltatásba.

## <a name="3-mixing-both-models"></a>3. Mindkét modell keverése
Egy másik minta a többbérlős modellezése is keverési index-per-bérlő és a szolgáltatás-bérlőnkénti stratégiák.

A két minta keverésével az alkalmazás legnagyobb bérlői dedikált szolgáltatásokat foglalhatnak el, míg a kevésbé aktív, kisebb bérlők hosszú farka egy megosztott szolgáltatásban foglalhat el indexeket. Ez a modell biztosítja, hogy a legnagyobb bérlők következetesen nagy teljesítményt a szolgáltatás, miközben segít megvédeni a kisebb bérlők a zajos szomszédok.

Ez a stratégia megvalósítása azonban előrelátást igényel annak előrejelzésében, hogy mely bérlők nek lesz szüksége egy dedikált szolgáltatásra és egy megosztott szolgáltatás indexére. Az alkalmazások összetettsége növeli a két többfelhasználós modell kezelésének szükségességét.

## <a name="achieving-even-finer-granularity"></a>Még finomabb részletesség elérése
A fenti tervezési minták modellezésére több-bérlős forgatókönyvek az Azure Cognitive Search feltételezik, hogy egy egységes hatókört, ahol minden bérlő egy egész példánya egy alkalmazás. Az alkalmazások azonban néha számos kisebb hatókört is kezelhetnek.

Ha a bérlőnkénti szolgáltatás és a bérlőnkénti index-modellek nem elég kis hatókörök, lehetőség van egy index modellezésére, hogy még finomabb részletességet érjen el.

Ha azt szeretné, hogy egyetlen index eltérően viselkedjen a különböző ügyfélvégpontok esetében, egy mező hozzáadható egy indexhez, amely minden lehetséges ügyfél számára egy bizonyos értéket jelöl meg. Minden alkalommal, amikor egy ügyfél meghívja az Azure Cognitive Search egy index lekérdezése vagy módosítása, az ügyfélalkalmazásból származó kód megadja a megfelelő értéket a mező az Azure Cognitive Search [szűrési](https://msdn.microsoft.com/library/azure/dn798921.aspx) képesség lekérdezési időben.

Ezzel a módszerrel különböző felhasználói fiókok, külön jogosultsági szintek és akár teljesen különálló alkalmazások is használhatók.

> [!NOTE]
> A fent leírt módszer használata egyetlen index konfigurálásához több bérlő kiszolgálására befolyásolja a keresési eredmények relevanciáját. Keresési relevancia-pontszámok egy indexszintű hatókör, nem egy bérlői szintű hatókör, így az összes bérlői adatok beépülnek a relevancia pontszámok alapjául szolgáló statisztikák, például a kifejezés gyakorisága.
> 
> 

## <a name="next-steps"></a>További lépések
Az Azure Cognitive Search számos alkalmazás számára vonzó választás. A több-bérlős alkalmazások különböző tervezési mintáinak kiértékelésekor vegye figyelembe a [különböző tarifacsomagokat](https://azure.microsoft.com/pricing/details/search/) és a megfelelő [szolgáltatáskorlátokat,](search-limits-quotas-capacity.md) hogy az Azure Cognitive Search a lehető legjobban testre szabhatja az alkalmazás-munkaterhelések és architektúrák minden méretben.

Az Azure Cognitive Search és a több-bérlős azuresearch_contact@microsoft.comforgatókönyvekkel kapcsolatos kérdések a.

