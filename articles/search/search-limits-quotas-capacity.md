---
title: A szintek és az SKU-Azure Search szolgáltatási korlátai
description: A kapacitás megtervezésére, valamint a kérelmek és a Azure Searchre adott válaszok maximális korlátaira vonatkozó szolgáltatási korlátok.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: heidist
ms.openlocfilehash: 9ddc7ad8882b30a17be5820116da72c5ab32fad9
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640613"
---
# <a name="service-limits-in-azure-search"></a>Szolgáltatási korlátozások a Azure Search
A tárterületre, a munkaterhelésekre és az indexek, dokumentumok és egyéb objektumok mennyiségére vonatkozó maximális korlátok attól függnek, hogy a Azure Search **ingyenes**, alapszintű, **standard**vagy **Storage optimalizált** árképzési szinteken van-e kiépítve. [](search-create-service-portal.md)

+ Az **ingyenes** egy több-bérlős megosztott szolgáltatás, amely az Azure-előfizetéshez tartozik.

+ Az alapszintű megoldás dedikált számítástechnikai erőforrásokat biztosít az éles számítási feladatokhoz kisebb méretekben.

+ A **standard** szintű dedikált gépeken fut, és minden szinten nagyobb a tárterület és a feldolgozási kapacitás. A standard szint négy szinten érhető el: S1, S2, S3 és S3 HD.

+ A Storage-ra **optimalizált tárolók** a **standard szintű**tárterületet, tárolási sávszélességet és memóriát biztosító dedikált gépeken futnak. A tárterület optimalizálása két szinten érhető el: L1 és L2

> [!NOTE]
> Július 1-től az összes szintet általánosan elérhetővé kell tennie, beleértve a tárolási optimalizált szintet is. A díjszabás a [díjszabás részletei](https://azure.microsoft.com/pricing/details/search/) lapon található.

  Az S3 nagy sűrűségű (S3 HD) speciális számítási feladatokhoz használható: [több-bérlős](search-modeling-multitenant-saas-applications.md) és nagy mennyiségű kis index (1 000 000-es dokumentum/index, 3000-es indexek/szolgáltatás). Ez a platform nem biztosítja az [Indexelő szolgáltatást](search-indexer-overview.md). Az S3 HD esetében az adatfeldolgozásnak a leküldéses megközelítést kell használnia, amely API-hívások használatával küldi le az adatok forrásról indexbe történő küldését. 

> [!NOTE]
> Egy szolgáltatás egy adott szinten lett kiépítve. A kapacitást biztosító jumping rétegek egy új szolgáltatás üzembe helyezését foglalják magukban (nincs helyben történő frissítés). További információ: [válasszon SKU-t vagy szintet](search-sku-tier.md). Ha többet szeretne megtudni a kapacitásnak a már üzembe helyezett szolgáltatáson belüli beállításáról, tekintse meg [a lekérdezési és indexelési feladatok erőforrás-szintjeinek skálázása](search-capacity-planning.md)című témakört.
>

## <a name="subscription-limits"></a>Előfizetés korlátai
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Tárolási korlátok
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Index korlátai

| Resource | Free | Basic&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | 2\. |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Indexek maximális száma |3 |5 vagy 15 |50 |200 |200 |1000 partíciónként vagy 3000 szolgáltatásonként |10 |10 |
| Egyszerű mezők maximális száma index szerint |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Összetett gyűjtemény mezőinek maximális száma index szerint |40 |40 |40 |40 |40 |40 |40 |40 |
| Az összes összetett gyűjteményen belüli elemek maximális száma dokumentum szerint |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Összetett mezők maximális mélysége |10 |10 |10 |10 |10 |10 |10 |10 |
| Mutatók maximális száma index [alapján](https://docs.microsoft.com/rest/api/searchservice/suggesters) |1 |1 |1 |1 |1 |1 |1 |1 |
| [Pontozási profilok](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) maximális száma index szerint |100 |100 |100 |100 |100 |100 |100 |100 |
| Függvények maximális száma profilban |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> a december 2017-ig létrehozott alapszintű szolgáltatások alacsonyabb korláttal rendelkeznek (15 helyett 5) az indexeken. Az alapszintű csomag az egyetlen olyan SKU, amelynek alsó korlátja a 100 mező/index.

<a name="document-limits"></a>

## <a name="document-limits"></a>Dokumentumok korlátai 

Október 2018-én már nem jelennek meg minden olyan új szolgáltatásra vonatkozó dokumentum, amely bármely más számlázandó szinten (alapszintű, S1, S2, S3, S3 HD) létrehozott, bármely régióban. Míg a legtöbb régióban a 2017 november/december óta korlátlan számú dokumentum szerepelt, a rendszer öt régióban folytatta a dokumentumok korlátozását. Attól függően, hogy mikor és hol hozott létre keresési szolgáltatást, lehet, hogy olyan szolgáltatást futtat, amely továbbra is a dokumentum korlátai alá esik.

Annak megállapításához, hogy a szolgáltatás rendelkezik-e dokumentum-korlátozásokkal, tekintse meg a szolgáltatás áttekintés lapján a használat csempét. A dokumentumok száma korlátlan, vagy a küszöbértéken alapuló korlátra vonatkozik.

  ![Használat csempe](media/search-limits-quotas-capacity/portal-usage-tile.png)

### <a name="regions-previously-having-document-limits"></a>Korábban dokumentum-korlátokkal rendelkező régiók

Ha a portálon a dokumentum korlátja látható, akkor a szolgáltatás a 2017-es késői időpontig jött létre, vagy egy adatközpontban lett létrehozva, amely alacsonyabb kapacitású fürtöket használ Azure Search szolgáltatások üzemeltetéséhez:

+ Kelet-Ausztrália
+ Kelet-Ázsia
+ Közép-India
+ Nyugat-Japán
+ USA nyugati középső régiója

A dokumentumok korlátja alá eső szolgáltatások esetében a következő maximális korlátok érvényesek:

|  Free | Alapszintű | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10,000 |1&nbsp;millió |15 millió partíciónként vagy 180 millió szolgáltatásonként |60 millió partíciónként vagy 720 millió szolgáltatásonként |120 millió partíciónként vagy 1,4 milliárd szolgáltatásonként |1 millió indexenként vagy 200 millió partíciónként |

Ha a szolgáltatás korlátozásokat blokkol, hozzon létre egy új szolgáltatást, majd tegye közzé újra az összes tartalmat a szolgáltatásban. Nincs olyan mechanizmus, amellyel zökkenőmentesen újra kiépítheti a szolgáltatást a színfalak mögötti új hardverre.

> [!Note] 
> A 2017-es végén létrehozott, nagy sűrűségű szolgáltatások esetében az 200 000 000-es dokumentum partíciós bontása el lett távolítva, de a 1 000 000-dokumentum index korlátja továbbra is fennáll.


### <a name="document-size-limits-per-api-call"></a>Dokumentum méretének korlátai API-hívás szerint

Az index API meghívásakor a dokumentum maximális mérete körülbelül 16 megabájt.

A dokumentum mérete valójában korlátozza az index API-kérés törzsének méretét. Mivel egyszerre több dokumentumot is át lehet adni az index API-hoz, a méretkorlát reálisan függ attól, hogy hány dokumentum van a kötegben. Egyetlen dokumentummal rendelkező kötegek esetében a dokumentum maximális mérete 16 MB JSON.

A dokumentum méretének megőrzéséhez ne felejtse el kizárni a kérelemből a nem lekérdezhető adatok körét. A képek és más bináris adatfájlok nem lekérdezhető közvetlenül, és nem tárolhatók az indexben. A nem lekérdezhető adatmennyiség keresési eredményekbe való integrálásához adjon meg egy nem kereshető mezőt, amely az erőforrás URL-hivatkozását tárolja.

## <a name="indexer-limits"></a>Indexelő korlátai

A maximális futási idő a szolgáltatás egészére vonatkozó egyensúlyt és stabilitást biztosít, de a nagyobb adatkészletek esetében a maximálisan megengedettnél több indexelési időt is igénybe vehet. Ha egy indexelési feladatok nem hajthatók végre a maximálisan engedélyezett időtartamon belül, futtassa az ütemezett futtatást. Az ütemező nyomon követi az indexelési állapotot. Ha egy ütemezett indexelési feladatot valamilyen oknál fogva megszakítanak, az indexelő kiválaszthatja, hogy a következő ütemezett futtatásnál melyik utolsó marad.


| Resource | Ingyenes&nbsp;<sup>1</sup> | Basic&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |2\. |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Indexelők maximális száma |3 |5 vagy 15|50 |200 |200 |– |10 |10 |
| Adatforrások maximális száma |3 |5 vagy 15 |50 |200 |200 |– |10 |10 |
| Maximális szakértelmével <sup>4</sup> |3 |5 vagy 15 |50 |200 |200 |– |10 |10 |
| Az indexelési terhelés maximális száma hívás közben |10 000 dokumentum |Csak a dokumentumok maximális száma korlátozza |Csak a dokumentumok maximális száma korlátozza |Csak a dokumentumok maximális száma korlátozza |Csak a dokumentumok maximális száma korlátozza |– |Korlátlan |Korlátlan |
| Minimális ütemterv | 5 perc |5 perc |5 perc |5 perc |5 perc |5 perc |5 perc | 5 perc |
| Maximális futási idő <sup>5</sup> | 1-3 perc |24 óra |24 óra |24 óra |24 óra |–  |24 óra |24 óra |
| A kognitív keresési szakértelmével vagy a blob-indexelés maximális futási ideje képanalízissel <sup>5</sup> | 3-10 perc |2 óra |2 óra |2 óra |2 óra |–  |2 óra |2 óra |
| BLOB indexelő: blob maximális mérete (MB) |16 |16 |128 |256 |256 |–  |256 |256 |
| BLOB indexelő: blobból kinyert tartalom maximális száma |32,000 |64,000 |4&nbsp;millió |4&nbsp;millió |4&nbsp;millió |– |4&nbsp;millió |4&nbsp;millió |

<sup>1</sup> az ingyenes szolgáltatások esetében az indexelő maximális végrehajtási ideje 3 perc a blob-forrásokhoz és 1 perc az összes többi adatforráshoz. A Cognitive Servicesba beérkező AI-indexelés esetén az ingyenes szolgáltatások napi 20 ingyenes tranzakcióra korlátozódnak, ahol a tranzakciót olyan dokumentumként határozzák meg, amely sikeresen áthalad a dúsítási folyamaton.

<sup>2</sup> a 2017 decembere előtt létrehozott alapszintű szolgáltatások alacsonyabb korláttal rendelkeznek (15 helyett 5) az indexelő, az adatforrások és a szakértelmével esetében.

<sup>3</sup> az S3 HD-szolgáltatások nem tartalmazzák az indexelő támogatását.

<sup>4</sup> készségkészlet legfeljebb 30 ismeret.

<sup>5</sup> a kognitív keresési munkaterhelések és az Azure-Blobok indexelésének képelemzése rövidebb ideig tart, mint a normál szöveges indexelés. A képek elemzése és a természetes nyelvi feldolgozás számítási igényű, és aránytalanul nagy mennyiségű rendelkezésre álló feldolgozási kapacitást igényel. A futási idő csökkentve lett, hogy a várólistán lévő többi feladat fusson.  

## <a name="queries-per-second-qps"></a>Lekérdezések másodpercenként (QPS)

A QPS-becsléseket minden ügyféltől függetlenül kell kialakítani. Az index mérete és összetettsége, a lekérdezés mérete és összetettsége, valamint a forgalom mennyisége a QPS elsődleges tényezője. Az ilyen tényezők ismeretlenek lehetnek, így nem lehet értelmes becsléseket nyújtani.

A becslések a dedikált erőforrásokon futó szolgáltatásokra (alapszintű és standard csomagokra) számítanak. Megbecsülheti a QPS, mert több paramétert is megadhat. A becslések megközelítésével kapcsolatos útmutatásért lásd: [Azure Search teljesítmény és optimalizálás](search-performance-optimization.md).

A tárterületre optimalizált csomagok esetében az alacsonyabb lekérdezési sebességet és a standard szintnél nagyobb késést kell várnia.  A lekérdezési teljesítmény kiszámításának módszertana megegyezik a standard szinttel.

## <a name="data-limits-cognitive-search"></a>Adatkorlátok (kognitív keresés)

Egy [olyan kognitív keresési folyamat](cognitive-search-concept-intro.md) , amely egy Text Analytics erőforrás meghívását teszi lehetővé az [entitások felismeréséhez](cognitive-search-skill-entity-recognition.md), a [kulcsfontosságú kifejezés](cognitive-search-skill-keyphrases.md)kinyeréséhez, a [hangulat elemzéséhez](cognitive-search-skill-sentiment.md)és a [nyelvi észleléshez](cognitive-search-skill-language-detection.md) az adatkorlátok vonatkoznak. A rekordok maximális méretének 50 000 karakternek kell lennie, a [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)következőképpen mérve:. Ha meg kell szakítania az adatait, mielőtt elküldené az érzelmeket elemző eszköznek, használja a [szöveg felosztása készséget](cognitive-search-skill-textsplit.md).

## <a name="api-request-limits"></a>API-kérelmek korlátai
* Legfeljebb 16 MB/kérelem <sup>1</sup>
* Legfeljebb 8 KB URL-cím hossza
* Maximális 1000 dokumentum/köteg/index feltöltés, Egyesítés vagy törlés
* Maximális 32 mező a $orderby záradékban
* A maximális keresési kifejezés mérete 32 766 bájt (32 KB mínusz 2 bájt) UTF-8 kódolású szöveg esetén

<sup>1</sup> Azure Searchban a kérelem törzse 16 MB-os felső korláttal rendelkezik, és az egyes mezők vagy gyűjtemények esetében gyakorlati korlátozást ír elő, amely nem korlátozza az elméleti korlátokat (lásd: [támogatott](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) adattípusok a mezők összeállításával és korlátozásával kapcsolatos információk.

## <a name="api-response-limits"></a>API-válaszok korlátai
* A keresési eredmények által visszaadott maximális 1000-dokumentumok száma
* A javasolt API-kérések száma legfeljebb 100 javaslatot ad vissza

## <a name="api-key-limits"></a>API-kulcsok korlátai
Az API-kulcsok a szolgáltatás-hitelesítéshez használatosak. Két típusukat különböztetjük meg. A felügyeleti kulcsok a kérelem fejlécében vannak megadva, és teljes írási és olvasási hozzáférést biztosítanak a szolgáltatáshoz. A lekérdezési kulcsok csak olvashatók, az URL-címen vannak megadva, és általában az ügyfélalkalmazások számára vannak elosztva.

* Maximum 2 rendszergazdai kulcs/szolgáltatás
* Szolgáltatáson legfeljebb 50 lekérdezési kulcs
