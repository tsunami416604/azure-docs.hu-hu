---
title: A csomagokra és az SKU-ra vonatkozó szolgáltatási korlátok
titleSuffix: Azure Cognitive Search
description: A kapacitás megtervezésére és az Azure-Cognitive Search kéréseire adott válaszok maximális korlátozására használt szolgáltatási korlátok.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 6ee339cb709a5d825b39b4accf294761c99ee41a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390363"
---
# <a name="service-limits-in-azure-cognitive-search"></a>Szolgáltatási korlátozások az Azure Cognitive Search

A tárolás, a munkaterhelések és az indexek és egyéb objektumok mennyiségére vonatkozó maximális korlátok attól függnek, hogy az Azure Cognitive Search **ingyenes**, **alapszintű**, **standard**vagy **Storage optimalizált** árképzési szinteken van-e [kiépítve](search-create-service-portal.md) .

+ Az **ingyenes** egy több-bérlős megosztott szolgáltatás, amely az Azure-előfizetéshez tartozik. Az indexelési és lekérdezési kérelmek a más bérlők által használt replikák és partíciók esetében futnak.

+ Az **alapszintű** megoldás dedikált számítástechnikai erőforrásokat biztosít az éles számítási feladatokhoz, de bizonyos hálózati infrastruktúrát más bérlők is megosztanak.

+ A **standard** szintű dedikált gépeken fut, és minden szinten nagyobb a tárterület és a feldolgozási kapacitás. A standard szint négy szinten érhető el: S1, S2, S3 és S3 HD.

+ A Storage-ra **optimalizált tárolók** a **standard szintű**tárterületet, tárolási sávszélességet és memóriát biztosító dedikált gépeken futnak. A tároló optimalizált két szinten érhető el: L1 és L2

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

| Erőforrás | Ingyenes | Alapszintű&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | 2\. |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Indexek maximális száma |3 |5 vagy 15 |50 |200 |200 |1000 partíciónként vagy 3000 szolgáltatásonként |10 |10 |
| Egyszerű mezők maximális száma index szerint |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Összetett gyűjtemény mezőinek maximális száma index szerint |40 |40 |40 |40 |40 |40 |40 |40 |
| Az összes összetett gyűjteményen belüli elemek maximális száma dokumentum&nbsp;<sup>2</sup> |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Összetett mezők maximális mélysége |10 |10 |10 |10 |10 |10 |10 |10 |
| Mutatók maximális száma index [alapján](https://docs.microsoft.com/rest/api/searchservice/suggesters) |1 |1 |1 |1 |1 |1 |1 |1 |
| [Pontozási profilok](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) maximális száma index szerint |100 |100 |100 |100 |100 |100 |100 |100 |
| Függvények maximális száma profilban |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> a december 2017-ig létrehozott alapszintű szolgáltatások alacsonyabb korláttal rendelkeznek (15 helyett 5) az indexeken. Az alapszintű csomag az egyetlen olyan SKU, amelynek alsó korlátja a 100 mező/index.

<sup>2</sup> a nagy számú, összetett gyűjteményekbe tartozó elem jelenleg magas tárterület-kihasználtságot okoz. Ez egy ismert probléma. Addig is az 3000-os korlát minden szolgáltatási szinten biztonságos felső határ. Ez a korlát csak olyan indexelési műveletekre van kikényszerítve, amely a legkorábbi általánosan elérhető (GA) API-verziót használja, amely a komplex típusú mezőket (`2019-05-06`) támogatja. Ha nem szeretné megszakítani a korábbi előzetes verziójú API-verziókat használó ügyfeleket (amelyek támogatják az összetett típusú mezőket), a rendszer nem érvényesíti ezt a korlátot az előzetes verziójú API-verziókat használó indexelési műveletekhez. Vegye figyelembe, hogy az előzetes verziójú API-verziók nem használhatók éles környezetekben való használatra, ezért javasoljuk, hogy az ügyfelek a legújabb GA API-verzióra lépjenek.

<a name="document-limits"></a>

## <a name="document-limits"></a>Dokumentumok korlátai 

Október 2018-én már nem jelennek meg minden olyan új szolgáltatásra vonatkozó dokumentum, amely bármely más számlázandó szinten (alapszintű, S1, S2, S3, S3 HD) létrehozott, bármely régióban. Míg a legtöbb régióban korlátlan számú dokumentum szerepel a 2017 november/december óta, néhány régióban továbbra is érvényesek a dokumentumok korlátai. Attól függően, hogy mikor és hol hozott létre keresési szolgáltatást, lehet, hogy olyan szolgáltatást futtat, amely továbbra is a dokumentum korlátai alá esik.

Annak megállapításához, hogy a szolgáltatás rendelkezik-e dokumentum-korlátozásokkal, használja a [szolgáltatás statisztikáinak Beolvasása REST API](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics). A dokumentum korlátai a válaszban szerepelnek, és `null` a korlátozás nélkül jelezve.

> [!NOTE]
> Annak ellenére, hogy nincs SKU-specifikus dokumentum-korlát, az indexek továbbra is a maximális biztonságos korlátot biztosítják a szolgáltatás stabilitásának biztosítása érdekében. Ez a korlát a Lucene származik. Minden Azure Cognitive Search-dokumentum belsőleg indexelve van egy vagy több Lucene-dokumentumként. A Lucene dokumentumok száma egy keresési dokumentumban az összetett gyűjtemények mezőiben szereplő elemek teljes számától függ. Minden elem külön Lucene-dokumentumként van indexelve. Egy összetett gyűjtemény mezőben 3 elemet tartalmazó dokumentum például 4 Lucene-dokumentumként lesz indexelve – 1 a dokumentumhoz, és 3 az elemekhez. Az Lucene dokumentumok maximális száma nagyjából 25 000 000 000.

### <a name="regions-previously-having-document-limits"></a>Korábban dokumentum-korlátokkal rendelkező régiók

Ha a portálon a dokumentum korlátja látható, akkor a szolgáltatás a 2017-es késői időpontig jött létre, vagy egy adatközpontban lett létrehozva az Azure Cognitive Search szolgáltatásainak üzemeltetéséhez szükséges alacsonyabb kapacitású fürtök használatával:

+ Kelet-Ausztrália
+ Kelet-Ázsia
+ Közép-India
+ Nyugat-Japán
+ USA nyugati középső régiója

A dokumentumok korlátja alá eső szolgáltatások esetében a következő maximális korlátok érvényesek:

|  Ingyenes | Alapszintű | S1 | S2 | S3 | S3&nbsp;HD |
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


| Erőforrás | Ingyenes&nbsp;<sup>1</sup> | Alapszintű&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |2\. |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Indexelők maximális száma |3 |5 vagy 15|50 |200 |200 |N/A |10 |10 |
| Adatforrások maximális száma |3 |5 vagy 15 |50 |200 |200 |N/A |10 |10 |
| Maximális szakértelmével <sup>4</sup> |3 |5 vagy 15 |50 |200 |200 |N/A |10 |10 |
| Az indexelési terhelés maximális száma hívás közben |10 000 dokumentum |Csak a dokumentumok maximális száma korlátozza |Csak a dokumentumok maximális száma korlátozza |Csak a dokumentumok maximális száma korlátozza |Csak a dokumentumok maximális száma korlátozza |N/A |Korlátlan |Korlátlan |
| Minimális ütemterv | 5 perc |5 perc |5 perc |5 perc |5 perc |5 perc |5 perc | 5 perc |
| Maximális futási idő <sup>5</sup> | 1-3 perc |24 óra |24 óra |24 óra |24 óra |N/A  |24 óra |24 óra |
| A kognitív keresési szakértelmével vagy a blob-indexelés maximális futási ideje képanalízissel <sup>5</sup> | 3-10 perc |2 óra |2 óra |2 óra |2 óra |N/A  |2 óra |2 óra |
| BLOB indexelő: blob maximális mérete (MB) |16 |16 |128 |256 |256 |N/A  |256 |256 |
| BLOB indexelő: blobból kinyert tartalom maximális száma |32,000 |64,000 |4&nbsp;millió |8&nbsp;millió |16&nbsp;millió |N/A |4&nbsp;millió |4&nbsp;millió |

<sup>1</sup> az ingyenes szolgáltatások esetében az indexelő maximális végrehajtási ideje 3 perc a blob-forrásokhoz és 1 perc az összes többi adatforráshoz. A Cognitive Servicesba beérkező AI-indexelés esetén az ingyenes szolgáltatások napi 20 ingyenes tranzakcióra korlátozódnak, ahol a tranzakciót olyan dokumentumként határozzák meg, amely sikeresen áthalad a dúsítási folyamaton.

<sup>2</sup> a 2017 decembere előtt létrehozott alapszintű szolgáltatások alacsonyabb korláttal rendelkeznek (15 helyett 5) az indexelő, az adatforrások és a szakértelmével esetében.

<sup>3</sup> az S3 HD-szolgáltatások nem tartalmazzák az indexelő támogatását.

<sup>4</sup> készségkészlet legfeljebb 30 ismeret.

<sup>5</sup> a kognitív keresési munkaterhelések és az Azure-Blobok indexelésének képelemzése rövidebb ideig tart, mint a normál szöveges indexelés. A képek elemzése és a természetes nyelvi feldolgozás számítási igényű, és aránytalanul nagy mennyiségű rendelkezésre álló feldolgozási kapacitást igényel. A futási idő csökkentve lett, hogy a várólistán lévő többi feladat fusson.  

> [!NOTE]
> Ahogy az [index korlátaiban](#index-limits)is említettük, az indexek az összes összetett gyűjteménynél a legújabb GA API-verziótól kezdődően a felső 3000 korlátot is kikényszerítik, amely az összetett típusokat (`2019-05-06`) is támogatja. Ez azt jelenti, hogy ha az indexelő korábbi API-verzióval hozta létre, a korlát nem vonatkozik rá. A maximális kompatibilitás megőrzése érdekében egy korábbi API-verzióval létrehozott indexelő, majd `2019-05-06` vagy újabb API-verzióval frissített, továbbra is ki lesz **zárva** a korlátok közül. Az ügyfeleknek tisztában kell lenniük azzal, hogy milyen negatív hatással vannak a nagyon nagy összetett gyűjtemények (a korábban említettek szerint), és javasoljuk, hogy hozzon létre minden új indexelő a legújabb GA API-verzióval.

## <a name="synonym-limits"></a>Szinonimák korlátai

Az engedélyezett szinonimák maximális száma az árképzési szinten változhat. Minden egyes szabály legfeljebb 20 kiterjesztéssel rendelkezhet, ahol a terjeszkedés egy egyenértékű kifejezés. Például a "Cat", a "Kitty", a "macskaféle" és az "Felis" (a macskák nemhez tartozó) társítása 3 kiterjesztésnek számít.

| Erőforrás | Ingyenes | Alapszintű | S1 | S2 | S3 | S3 – HD |L1 | 2\. |
| -------- | -----|------ |----|----|----|-------|---|----|
| Szinonimák maximális térképe |3 |3|5 |10 |20 |20 | 10 | 10 |
| Szabályok maximális száma leképezés szerint |5000 |20 000|20 000 |20 000 |20 000 |20 000 | 20 000 | 20 000  |

## <a name="queries-per-second-qps"></a>Lekérdezések másodpercenként (QPS)

A QPS-becsléseket minden ügyféltől függetlenül kell kialakítani. Az index mérete és összetettsége, a lekérdezés mérete és összetettsége, valamint a forgalom mennyisége a QPS elsődleges tényezője. Az ilyen tényezők ismeretlenek lehetnek, így nem lehet értelmes becsléseket nyújtani.

A becslések a dedikált erőforrásokon futó szolgáltatásokra (alapszintű és standard csomagokra) számítanak. Megbecsülheti a QPS, mert több paramétert is megadhat. A becslések megközelítésével kapcsolatos útmutatásért lásd: [Azure Cognitive Search teljesítmény és optimalizálás](search-performance-optimization.md).

A tárterületre optimalizált csomagok esetében az alacsonyabb lekérdezési sebességet és a standard szintnél nagyobb késést kell várnia.  A lekérdezési teljesítmény kiszámításának módszertana megegyezik a standard szinttel.

## <a name="data-limits-ai-enrichment"></a>Adatkorlátok (AI-dúsítás)

Egy [mesterséges intelligencia](cognitive-search-concept-intro.md) -bővítési folyamat, amely egy Text Analytics erőforrást hív meg az [entitások felismerése](cognitive-search-skill-entity-recognition.md), a [kulcsfontosságú kifejezés kinyerése](cognitive-search-skill-keyphrases.md), a [hangulat elemzése](cognitive-search-skill-sentiment.md), a [nyelvfelismerés](cognitive-search-skill-language-detection.md)és a személyre szabott adatok [észlelése](cognitive-search-skill-pii-detection.md) az adatkorlátokra vonatkozik. A rekordok maximális méretének 50 000 karakternek kell lennie [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)alapján mérve. Ha meg kell szakítania az adatait, mielőtt elküldené az érzelmeket elemző eszköznek, használja a [szöveg felosztása készséget](cognitive-search-skill-textsplit.md).

## <a name="throttling-limits"></a>Szabályozási korlátok

A keresési lekérdezési és indexelési kérelmeket a rendszer a maximális kapacitásának megfelelően szabályozza. A szabályozás eltérően viselkedik a különböző API-kkal. A szolgáltatás terhelése alapján a lekérdezési API-k (keresés/javaslat/automatikus kiegészítés) és az indexelő API-k szabályozása dinamikusan történik. Az API-k indexe statikus kérelmekre vonatkozó korlátozásokkal rendelkezik. 

Az indexhez kapcsolódó műveletekre vonatkozó statikus díjszabási kérelmek:

+ Indexek listázása (/Indexes beolvasása): 5 másodpercenkénti keresési egységenként
+ Index beolvasása (/Indexes/myindex beolvasása): másodpercenként 10/keresési egység
+ Index létrehozása (/Indexes POST): 12 percenként, keresési egységenként
+ Index létrehozása vagy frissítése (PUT/Indexes/myindex): 6 másodpercenként/keresési egységenként
+ Index törlése (/Indexes/myindex törlése): 12 percenként/keresési egységenként 

## <a name="api-request-limits"></a>API-kérelmek korlátai
* Legfeljebb 16 MB/kérelem <sup>1</sup>
* Legfeljebb 8 KB URL-cím hossza
* Maximális 1000 dokumentum/köteg/index feltöltés, Egyesítés vagy törlés
* Maximális 32 mező a $orderby záradékban
* A maximális keresési kifejezés mérete 32 766 bájt (32 KB mínusz 2 bájt) UTF-8 kódolású szöveg esetén

<sup>1</sup> az Azure Cognitive Search-ban a kérelem törzse 16 MB-os felső korláttal rendelkezik, és az egyes mezők vagy gyűjtemények esetében gyakorlati korlátozást ír elő, amely nem korlátozza az elméleti korlátokat (lásd a [támogatott adattípusokat](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) a mezők összeállításával és korlátozásával kapcsolatos további információkért).

## <a name="api-response-limits"></a>API-válaszok korlátai
* A keresési eredmények által visszaadott maximális 1000-dokumentumok száma
* A javasolt API-kérések száma legfeljebb 100 javaslatot ad vissza

## <a name="api-key-limits"></a>API-kulcsok korlátai
Az API-kulcsok a szolgáltatás-hitelesítéshez használatosak. Két típusukat különböztetjük meg. A felügyeleti kulcsok a kérelem fejlécében vannak megadva, és teljes írási és olvasási hozzáférést biztosítanak a szolgáltatáshoz. A lekérdezési kulcsok csak olvashatók, az URL-címen vannak megadva, és általában az ügyfélalkalmazások számára vannak elosztva.

* Maximum 2 rendszergazdai kulcs/szolgáltatás
* Szolgáltatáson legfeljebb 50 lekérdezési kulcs