---
title: Csomagok és SKU-k – Azure Search szolgáltatási korlátozásai
description: A kapacitástervezés használt szolgáltatási korlátai és a kérelmek és válaszok az Azure Search maximális korlátig.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 5187052316e229273aa49eb784bf200c0f16a0f7
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55165387"
---
# <a name="service-limits-in-azure-search"></a>Az Azure Search szolgáltatási korlátai
Maximális korlátozza a storage, a számítási feladatok és az indexek, dokumentumok, mennyiségét, és más objektumok függenek, hogy Ön [üzembe helyezése az Azure Search](search-create-service-portal.md) , **ingyenes**, **alapszintű**, vagy **Standard** díjcsomagok árából.

+ **Ingyenes** egy több-bérlős megosztott szolgáltatás, amely az Azure-előfizetésében.

+ **Alapszintű** dedikált számítási erőforrásokat biztosít a kisebb léptékű termelési számítási feladatokhoz.

+ **Standard szintű** minden szinten több tárolási és feldolgozási kapacitással rendelkező dedikált gépen üzemel. Standard négy szinten érhető el: S1, S2, S3 és S3 HD.

  Nagy kapacitású S3 (S3 HD) van fejthetők vissza az adott munkaterhelés konkrét: [több-bérlős](search-modeling-multitenant-saas-applications.md) és nagy mennyiségű (1 millió indexenkénti, három példányban indexek szolgáltatásonként) kis méretű indexeket. Ez a szint nem biztosít a [indexelő szolgáltatás](search-indexer-overview.md). Az S3 HD adatbetöltés kell kihasználni a ügyfélleküldéses módszer használatával a forrás és a indexet az adatok leküldéséhez API-hívások. 

> [!NOTE]
> A szolgáltatás egy adott szinten van üzembe helyezve. Kapacitás próbál a jeggyel rétegek lépésközt magában foglalja a (nincs nem helyi frissítése) egy új kiszolgáló üzembe helyezése. További információkért lásd: [Termékváltozat vagy szint kiválasztása](search-sku-tier.md). Már kiépített egy szolgáltatás kapacitásokat módosításával kapcsolatos további információkért lásd: [erőforrásszintek méretezése a lekérdezés és a számítási feladatok indexeléséhez](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Előfizetés korlátai
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Tárolási korlátok
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Index korlátok

| Erőforrás | Ingyenes | Basic&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD |
| -------- | ---- | ------------------- | --- | --- | --- | --- |
| Indexek maximális száma |3 |5 vagy 15 |50 |200 |200 |1000 partíciónként vagy 3000 szolgáltatásonként |
| Indexenkénti maximális mezők |1000 |100 |1000 |1000 |1000 |1000 |
| Maximális [javaslattevők](https://docs.microsoft.com/rest/api/searchservice/suggesters) indexenkénti |1 |1 |1 |1 |1 |1 |
| Maximális [pontozási profilok](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) indexenkénti |100 |100 |100 |100 |100 |100 |
| Profil maximális függvények |8 |8 |8 |8 |8 |8 |

<sup>1</sup> késői 2017 Miután megnövekedett legfeljebb 15 indexeket, adatforrásból és indexelőből létrehozott alapvető szolgáltatások. Korábban létrehozott szolgáltatások 5 rendelkeznek. Alapszintű csomag egyetlen Termékváltozat és a egy alacsonyabb korlátja 100 mezők indexenkénti.

<a name="document-limits"></a>

## <a name="document-limits"></a>A dokumentum korlátok 

2018. október már nem léteznek a dokumentum korlátait minden olyan új szolgáltatás bármely számlázható szint szerint kalkuláljuk (alap, S1, S2, S3 szintű, S3 HD) bármelyik régióban hozza létre. Bár a legtöbb régióban kellett volna 2017 November/December óta száma korlátlan, öt régióban, amely továbbra is a dokumentum készletként volt. Attól függően, hol és mikor egy keresési szolgáltatás hozta létre akkor előfordulhat, hogy fut egy szolgáltatás, amely dokumentumot korlátok továbbra is vonatkozik.

Annak megállapításához, hogy a szolgáltatás rendelkezik-e a dokumentum korlátok, ellenőrizze a szolgáltatás áttekintés oldalán a használati csempét. Dokumentum-száma korlátlan, vagy egy réteg korlát hatálya alá tartozik, a rendszer.

  ![Használat csempe](media/search-limits-quotas-capacity/portal-usage-tile.png)

### <a name="regions-previously-having-document-limits"></a>Korábban a dokumentum korlátok rendelkező régiók

A portál jelzi, hogy egy dokumentumos korlátja, ha a szolgáltatás létre késői 2017., vagy egy data Center alacsonyabb teljesítményű-fürtöket használó Azure Search-szolgáltatások üzemeltetésére lett létrehozva:

+ Kelet-Ausztrália
+ Kelet-Ázsia
+ Közép-India
+ Nyugat-Japán
+ USA nyugati középső régiója

A dokumentum korlátai szolgáltatások esetében a következő korlátozásokat a alkalmazni:

|  Ingyenes | Alapszintű | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10,000 |1 millió |15 millió partíciónként vagy 180 millió szolgáltatásonként |60 millió partíciónként vagy 720 millió szolgáltatásonként |120 millió partíciónként vagy 1,4 milliárd szolgáltatásonként |1 millió indexenként vagy 200 millió partíciónként |

Ha a szolgáltatás, amely a rendszer blokkolja, korlátai, hozzon létre egy új szolgáltatást, és majd újbóli az adott szolgáltatás teljes tartalmát. Nincs új hardverre a színfalak mögött a szolgáltatást zökkenőmentesen reprovisioning mód.

> [!Note] 
> Nagy kapacitású S3 Services késői 2017 után létrehozott a partíciónként 200 millió dokumentumot el lett távolítva, de a / index korlát továbbra is 1 millió dokumentumot.


### <a name="document-size-limits-per-api-call"></a>A dokumentum méretbeli korlátokat API-hívás száma

A maximális dokumentum Index API hívásakor mérete körülbelül 16 megabájt.

Dokumentum mérete ténylegesen az Index API-kérelem törzse mérete korlátozva. Több dokumentumot kötegelt egyszerre az Index API adhat át, mivel a méretkorlátot reálisan hány dokumentumok a Batch olyan függ. Egyetlen dokumentum kötegben dokumentum maximális mérete 16 MB-os JSON.

Ne feledje alacsonyan tartani a dokumentumok méretétől, a kérelem nem lekérdezhető adatok kizárása. Képek és más bináris adatok nem közvetlenül lekérdezhető, és nem tárolható az indexben. Keresési eredmények nem lekérdezhető adatokat integrálhat, adjon meg egy nem kereshető mező, amely tárolja az erőforrás URL-cím hivatkozást.

## <a name="indexer-limits"></a>Az indexelő korlátok

Késedelmes 2017 után létrehozott alapvető szolgáltatások megnövekedett legfeljebb 15 indexeket, adatforrások, szakértelmével és indexelők rendelkezik.

Erőforrás-igényes művelet, például képelemzés, az Azure blob-indexelés vagy a természetes nyelvi feldolgozás, a kognitív keresés rövidebb maximális futó idővel rendelkezik, úgy, hogy más indexelési feladatokat is elhelyezkedhetnek. Ha egy indexelési feladat a maximális rendelkezésre álló időn belül nem tudja végrehajtani, próbálja meg egy ütemezés szerint fut. Az ütemező nyomon követi az indexelés állapotát. Ha egy ütemezett indexelési feladat bármilyen okból megszakad, az indexelő folytathatja a munkát, ahol utolsó abbahagyta a következő ütemezett futáskor.

| Erőforrás | Ingyenes&nbsp;<sup>1</sup> | Basic&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|
| -------- | ----------------- | ----------------- | --- | --- | --- | --- |
| Indexelők maximális száma |3 |5 vagy 15|50 |200 |200 |– |
| Adatforrások maximális száma |3 |5 vagy 15 |50 |200 |200 |– |
| Maximális szakértelmével <sup>4</sup> |3 |5 vagy 15 |50 |200 |200 |– |
| Maximális indexelési terhelés száma indításonként |10 000 dokumentum |Csak a dokumentumok maximális száma korlátozott |Csak a dokumentumok maximális száma korlátozott |Csak a dokumentumok maximális száma korlátozott |Csak a dokumentumok maximális száma korlátozott |– |
| Maximális futási idejét <sup>5</sup> | 1 – 3 perc alatt |24 óra |24 óra |24 óra |24 óra |–  |
| Maximális futási idejét, a kognitív keresés szakértelmével vagy a blob-indexelés a képelemzés <sup>5</sup> | 3 – 10 perc |2 óra |2 óra |2 óra |2 óra |–  |
| BLOB indexelőjével: blob maximális mérete, MB |16 |16 |128 |256 |256 |–  |
| BLOB indexelőjével: blob kinyert tartalom maximális karakter |32,000 |64,000 |4 millió |4 millió |4 millió |– |

<sup>1</sup> ingyenes szolgáltatások indexelő maximális végrehajtási ideje 3 perc alatt van, a blob-forrásoknak és minden más adatforrás esetében 1 perc.

<sup>2</sup> késői 2017 Miután megnövekedett legfeljebb 15 indexeket, adatforrásból és indexelőből létrehozott alapvető szolgáltatások. Korábban létrehozott szolgáltatások 5 rendelkeznek.

<sup>3</sup> S3 HD szolgáltatások nem támogatják az indexelő.

<sup>4</sup> maximális indexmezők / 30 képességek.

<sup>5</sup> cognitive search számítási feladatok és az Azure blob-indexelés képelemzés, mint a rendszeres szöveges indexelés rövidebb futó idővel rendelkezik. Képelemzés és a természetes nyelvi feldolgozás nagy számítási igényű, és elérhető feldolgozási teljesítmény aránytalanul nagy mennyiségű felhasználását. Futtatási idő képfájljának futtatásához lehetőséget ad az üzenetsorban lévő egyéb feladatokhoz.  

## <a name="queries-per-second-qps"></a>Lekérdezések másodpercenkénti (lekérdezési QPS)

QPS becslések egymástól függetlenül minden ügyfélnek kellett kifejleszteni. Index mérete és összetettsége, lekérdezések mérete és összetettsége és forgalom mennyisége olyan elsődleges befolyásoló QPS. Nincs lehetőség a jelentéssel bíró becslések kínáló venni olyan tényezőket, ha ismeretlen.

A becslések olyan kiszámíthatóbb, ha dedikált erőforrások (alap és Standard szinten) futó szolgáltatások alapján számítjuk ki. További QPS megbecsülheti szorosan, mert rendelkezik több paraméter felett. Megközelítés-becslésére módjáról útmutatóért lásd: [Azure Search-teljesítmény és optimalizálás](search-performance-optimization.md).

## <a name="data-limits-cognitive-search"></a>A Data korlátai (cognitive search)

A [cognitive search folyamat](cognitive-search-concept-intro.md) , amely a Text Analytics erőforrás-hívást hajt végre [entitások felismerése](cognitive-search-skill-entity-recognition.md), [kulcsfontosságú kifejezések kinyerése](cognitive-search-skill-keyphrases.md), [hangulatelemzés ](cognitive-search-skill-sentiment.md), és [nyelvfelismerés](cognitive-search-skill-language-detection.md) data korlátai hatálya alá tartozik. Egy rekord maximális mérete 50 000 karakter által mért kell lennie `String.Length`. Ha szeretné feloszthatja az adatokat, mielőtt elküldené a róluk szóló véleményeket elemző, használja a [szöveg felosztása szakértelem](cognitive-search-skill-textsplit.md).

## <a name="api-request-limits"></a>API-kérelmekre vonatkozó korlátok
* Legfeljebb 16 MB-os kérelmenként <sup>1</sup>
* 8 KB-os URL-cím maximális hossza
* Index kötegenként legfeljebb 1000 dokumentumok tölt fel, egyesítése vagy törlése
* $Orderby záradékban legfeljebb 32 mezők
* Maximális keresési kifejezés mérete 32766 bájt (32 KB-os mínusz 2 bájt) UTF-8 kódolású szöveges

<sup>1</sup> az Azure Search szolgáltatásban a kérelem törzsében van egy felső korlátot 16 MB-ot, a tartalmát az egyes mezők vagy gyűjtemények, ellenkező esetben nem korlátozza elméleti korlátok gyakorlati korlátozva betartatásához vonatkoznak (lásd: [támogatott adatok típusok](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) mező felépítés és korlátozások kapcsolatos további részletekért).

## <a name="api-response-limits"></a>API-válasz korlátok
* Legfeljebb 1000 dokumentumot adja vissza a keresési eredmények oldalanként
* Javaslat API kérelmenként visszaadott legfeljebb 100 javaslatok

## <a name="api-key-limits"></a>API-kulcs korlátok
API-kulcsok szolgáltatás hitelesítéshez használhatók. Két típusukat különböztetjük meg. Az adminisztrációs kulcsok a kérelem fejlécében megadott, és a szolgáltatás teljes olvasási és írási hozzáférést. Lekérdezési kulcsok a csak olvasható, az URL-cím a megadott, és az ügyfélalkalmazások általában elosztott rendszer.

* Legfeljebb 2 az adminisztrációs kulcsok szolgáltatásonként
* Legfeljebb 50 lekérdezési kulcsok szolgáltatásonként
