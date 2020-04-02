---
title: Szolgáltatási korlátok a rétegek és a skus
titleSuffix: Azure Cognitive Search
description: A kapacitástervezéshez használt szolgáltatáskorlátok, valamint az Azure Cognitive Search kéréseinek és válaszeinek maximális korlátozása.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: b54905e201ee7a6dbf4c6837960a6e0b63057ea9
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549055"
---
# <a name="service-limits-in-azure-cognitive-search"></a>Az Azure Cognitive Search szolgáltatási korlátai

A tárolási, számítási feladatok és indexek és egyéb objektumok mennyiségének maximális korlátozása attól függ, hogy ingyenes, **alapszintű,** **szabványos**vagy **tárolási optimalizált** tarifacsomagokat biztosít-e az [Azure Cognitive Search](search-create-service-portal.md) szolgáltatásban. **Free**

+ **Az Ingyenes** egy több-bérlős megosztott szolgáltatás, amely az Azure-előfizetéssel együtt érkezik. Az indexelési és lekérdezési kérelmek végrehajtása más bérlők által használt replikákon és partíciókon.

+ **Az alapszintű** speciális számítási erőforrásokat biztosít az éles számítási feladatok hoz kisebb mértékben, de megoszt néhány hálózati infrastruktúrát más bérlőkkel.

+ **A standard** dedikált gépeken fut, amelyek minden szinten nagyobb tárolási és feldolgozási kapacitással rendelkeznek. A standard négy szinten kapható: S1, S2, S3 és S3 HD.

+ **A Tárolásra optimalizált** gépeken fut, amelyek teljes tárhelyet, tárolási sávszélességet és memóriát használnak, mint **a Standard**. Az optimalizált tárhely két szinten kapható: L1 és L2

> [!NOTE]
> Július 1-jéig az összes szint általánosan elérhető, beleértve a Storage-optimalizált szintet is. Minden díjszabás megtalálható az [Árképzés részletei](https://azure.microsoft.com/pricing/details/search/) oldalon.

  Az S3 nagy sűrűségű (S3 HD) speciális számítási feladatokhoz készült: [több-bérlős](search-modeling-multitenant-saas-applications.md) és nagy mennyiségű kis indexek (szolgáltatásonként háromezer index). Ez a szint nem biztosítja az [indexelő funkciót.](search-indexer-overview.md) Az S3 HD-n az adatbetöltésnek ki kell használnia a leküldéses megközelítést, és API-hívásokhasználatával le kell adnia az adatokat a forrásból az indexbe. 

> [!NOTE]
> Egy szolgáltatás egy adott szinten van kiépítve. Ugrás szintek kapacitás megszerzéséhez magában foglalja egy új szolgáltatás kiépítése (nincs helyben frissítés). További információ: [Termékváltozat vagy szint kiválasztása.](search-sku-tier.md) Ha többet szeretne tudni a már kiépített szolgáltatás kapacitásának módosításáról, olvassa el [az Erőforrásszintek méretezése lekérdezési és indexelési számítási feladatokhoz.](search-capacity-planning.md)
>

## <a name="subscription-limits"></a>Előfizetés korlátai
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Tárolási korlátok
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Indexkorlátok

| Erőforrás | Ingyenes | &nbsp;<sup>1.</sup> alap  | S1 | S2 | S3 | S3&nbsp;HD | 1. | 2. |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Indexek maximális száma |3 |5 vagy 15 |50 |200 |200 |1000 partíciónként vagy 3000 szolgáltatásonként |10 |10 |
| Egyszerű mezők maximális indexenként |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Összetett gyűjteménymezők maximális indexenként |40 |40 |40 |40 |40 |40 |40 |40 |
| Az összes összetett gyűjtemény maximális&nbsp;elemei dokumentumonként<sup>2</sup> |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Összetett mezők maximális mélysége |10 |10 |10 |10 |10 |10 |10 |10 |
| Maximális [javaslatajánlók](https://docs.microsoft.com/rest/api/searchservice/suggesters) indexenként |1 |1 |1 |1 |1 |1 |1 |1 |
| Maximális [pontozási profilok](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) indexenként |100 |100 |100 |100 |100 |100 |100 |100 |
| Maximális funkciók profilonként |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> A 2017 decembere előtt létrehozott alapszolgáltatások indexein alacsonyabb (15 helyett 5) alacsonyabb szintű ek vannak. Az alapszintű szint az egyetlen termékváltozat, amelyindexenként 100 mező alsó határral rendelkező.

<sup>2</sup> Mivel dokumentumonként nagyon sok elem található összetett gyűjteményekben, jelenleg nagy tárolási kihasználtságot okoz. Ez egy ismert probléma. Addig is a 3000-es korlát az összes szolgáltatási szint biztonságos felső határa. Ez a korlát csak olyan indexelési műveletekesetén van érvényben, amelyek a legkorábbi`2019-05-06`általánosan elérhető (GA) API-verziót használják, amely támogatja az összetett típusmezőket ( ). Ha nem szakítjuk meg azokat az ügyfeleket, akik esetleg korábbi előzetes API-verziókat használnak (amelyek összetett típusmezőket támogatnak), nem érvényesítjük ezt a korlátot az ilyen előzetes API-verziókat használó indexelési műveletekre. Vegye figyelembe, hogy az előzetes API-verziók nem éles környezetben való használatra szolgálnak, és javasoljuk, hogy az ügyfelek a legújabb GA API-verzióra lépjenek.

> [!NOTE]
> Bár egyetlen index maximális kapacitását általában korlátozza a rendelkezésre álló tárterület, az egyetlen indexben tárolható dokumentumok teljes számán maximális felső határok vannak. Ez a korlát az Alapszintű, S1, S2 és S3 keresési szolgáltatások indexenként körülbelül 24 milliárd dokumentumot tartalmaz, és 2 milliárd dokumentumot indexenként az S3HD keresési szolgáltatásokhoz. Az összetett gyűjtemények minden eleme külön dokumentumnak számít e korlátok alkalmazásában.

<a name="document-limits"></a>

## <a name="document-limits"></a>Dokumentumkorlátok 

2018 októberétől már nem léteznek olyan új szolgáltatások, amelyek bármely számlázható szinten (Alapszintű, S1, S2, S3, S3 HD) bármely régióban létrehozott új szolgáltatásokhoz nem tartoznak. A 2018 októbere előtt létrehozott régebbi szolgáltatásokra továbbra is vonatkozhatnak a dokumentumok száma.

Annak megállapításához, hogy a szolgáltatás rendelkezik-e dokumentumkorlátokkal, használja a [GET Service Statistics REST API-t.](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics) A dokumentumkorlátok a válaszban `null` is megjelennek, és nem jeleznek korlátokat.

### <a name="document-size-limits-per-api-call"></a>Dokumentumméret-korlátozások API-hívásonként

Az Index API-k hívása esetén a dokumentum maximális mérete körülbelül 16 megabájt.

A dokumentum mérete valójában az Index API-kérelem törzsének méretére vonatkozó korlát. Mivel egyszerre több dokumentum kötegét is átadhatja az Index API-nak, a méretkorlát reálisan függ attól, hogy hány dokumentum van a kötegben. Egyetlen dokumentummal rendelkező köteg esetén a dokumentum maximális mérete 16 MB JSON.

A dokumentum méretének leépítéséhez ne felejtse el kizárni a nem lekérdezhető adatokat a kérelemből. A képek és más bináris adatok közvetlenül nem kérdezhetők le, és nem tárolhatók az indexben. Ha nem lekérdezhető adatokat szeretne integrálni a keresési eredményekbe, definiáljon egy nem kereshető mezőt, amely az erőforrásURL-hivatkozását tárolja.

## <a name="indexer-limits"></a>Indexelő korlátok

Maximális futási idő áll rendelkezésre, hogy egyensúlyt és stabilitást biztosítson a szolgáltatás egészének, de a nagyobb adatkészletek több indexelési időre lehet szükség, mint a maximális megengedett. Ha egy indexelési feladat nem tudja befejezni a maximálisan engedélyezett időn belül, próbálja meg ütemezés szerint futtatni. Az ütemező nyomon követi az indexelési állapotot. Ha egy ütemezett indexelési feladat bármilyen okból megszakad, az indexelő folytathatja ott, ahol utoljára abbahagyta a következő ütemezett futtatáskor.


| Erőforrás | Ingyenes&nbsp;<sup>1</sup> | Alap&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;&nbsp;HD<sup>3</sup>|1. |2. |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Indexelők maximális száma |3 |5 vagy 15|50 |200 |200 |N/A |10 |10 |
| Adatforrások maximális száma |3 |5 vagy 15 |50 |200 |200 |N/A |10 |10 |
| Maximális skillsets <sup>4</sup> |3 |5 vagy 15 |50 |200 |200 |N/A |10 |10 |
| Maximális indexelési terhelés meghívásonként |10 000 dokumentum |Csak maximális dokumentumok korlátozzák |Csak maximális dokumentumok korlátozzák |Csak maximális dokumentumok korlátozzák |Csak maximális dokumentumok korlátozzák |N/A |Nincs korlátozás |Nincs korlátozás |
| Minimális ütemezés | 5 perc |5 perc |5 perc |5 perc |5 perc |5 perc |5 perc | 5 perc |
| Maximális menetidő <sup>5</sup> | 1-3 perc |24 óra |24 óra |24 óra |24 óra |N/A  |24 óra |24 óra |
| A kognitív keresési képességek vagy a blobindexelés maximális futási ideje képelemzéssel <sup>5</sup> | 3-10 perc |2 óra |2 óra |2 óra |2 óra |N/A  |2 óra |2 óra |
| Blob indexelő: maximális blobméret, MB |16 |16 |128 |256 |256 |N/A  |256 |256 |
| Blob indexelő: a blobból kinyert tartalom maximális karakterei |32,000 |64,000 |4&nbsp;millió |8&nbsp;millió |16&nbsp;millió |N/A |4&nbsp;millió |4&nbsp;millió |

<sup>1</sup> Ingyenes szolgáltatások indexelő maximális végrehajtási idő 3 perc blob források és 1 perc az összes többi adatforrások. A Cognitive Services-be hívásokat tartalmazó AI indexelés esetén az ingyenes szolgáltatások napi 20 ingyenes tranzakcióra korlátozódnak, ahol a tranzakció olyan dokumentumként van definiálva, amely sikeresen halad át a dúsítási folyamaton.

<sup>2</sup> A 2017 decembere előtt létrehozott alapszolgáltatások alacsonyabb határértékekkel rendelkeznek (15 helyett 5) az indexelők, adatforrások és skillsets.

<sup>3</sup> Az S3 HD-szolgáltatások nem tartalmazzák az indexelő támogatást.

<sup>4</sup> Legfeljebb 30 készség egy skillset.

<sup>5</sup> Kognitív keresési számítási feladatok és képelemzés az Azure blob indexelés rövidebb futási idő, mint a rendszeres szöveges indexelés. A képelemzés és a természetes nyelvi feldolgozás számításigényes, és aránytalanul nagy mennyiségű rendelkezésre álló feldolgozási teljesítményt fogyaszt. A futási idő csökkent, így a várólistában lévő többi feladat nak lehetősége volt a futtatásra.  

> [!NOTE]
> Az Index [limitekben foglaltaknak](#index-limits)aszerint az indexelők a 3000 elem felső határát is betartatják az`2019-05-06`összes összetett gyűjteményben dokumentumonként, kezdve a legújabb GA API-verzióval, amely támogatja az összetett típusokat ( ). Ez azt jelenti, hogy ha egy korábbi API-verzióval hozta létre az indexelőt, akkor nem vonatkozik ez a korlát. A maximális kompatibilitás megőrzése érdekében egy korábbi API-verzióval létrehozott, `2019-05-06` majd API-verzióval vagy újabb verzióval frissített indexelő továbbra is ki lesz **zárva** a korlátokból. Az ügyfeleknek tisztában kell lenniük a nagyon nagy összetett gyűjtemények kedvezőtlen hatásaival (ahogy azt korábban említettük), és javasoljuk, hogy hozzon létre új indexelőket a legújabb GA API-verzióval.

## <a name="synonym-limits"></a>Szinonima korlátok

Az engedélyezett szinonimaleképezések maximális száma a tarifacsomagtól függően változik. Minden szabály legfeljebb 20 bővítéssel rendelkezhet, ahol a bővítés egyenértékű kifejezés. Például, meghatározott " macska", egyesülés -val " cica", " macskaféle", és " macskaféle" ( a nemzetség részére macskák) akar számolás mint 3 kiterjedés.

| Erőforrás | Ingyenes | Basic | S1 | S2 | S3 | S3-HD |1. | 2. |
| -------- | -----|------ |----|----|----|-------|---|----|
| Maximális szinonimatérképek |3 |3|5 |10 |20 |20 | 10 | 10 |
| Szabályok maximális száma leképezésenként |5000 |20000|20000 |20000 |20000 |20000 | 20000 | 20000  |

## <a name="queries-per-second-qps"></a>Lekérdezések másodpercenként (QPS)

A QPS becsléseket minden ügyfélnek függetlenül kell kidolgoznia. Az index mérete és összetettsége, a lekérdezés mérete és összetettsége, valamint a forgalom mennyisége a QPS elsődleges meghatározói. Nincs mód arra, hogy értelmes becsléseket, ha ezek a tényezők ismeretlenek.

A becslések kiszámíthatóbbak a dedikált erőforrásokon (alapszintű és standard szintű) futó szolgáltatások alapján számítva. A QPS-t közelebbről is megbecsülheti, mivel több paraméterfelett is szabályozhatja a vezérlést. A becslés megközelítésével kapcsolatos útmutatásért tekintse meg az [Azure Cognitive Search teljesítményét és optimalizálását.](search-performance-optimization.md)

A storage-optimalizált rétegek esetén alacsonyabb lekérdezési átviteli és nagyobb késést kell várnia, mint a standard szintek.  A lekérdezési teljesítmény becslésének módszertana ugyanaz, mint a standard szintek.

## <a name="data-limits-ai-enrichment"></a>Adatkorlátok (AI-dúsítás)

Adatkorlátok vonatkoznak [egy AI-bővítési folyamat,](cognitive-search-concept-intro.md) amely egy Text Analytics-erőforráshoz hív az [entitásfelismerés,](cognitive-search-skill-entity-recognition.md) [a kulcskifejezések kinyerése,](cognitive-search-skill-keyphrases.md) [a hangulatelemzés,](cognitive-search-skill-sentiment.md)a [nyelvfelismerés](cognitive-search-skill-language-detection.md)és a [személyazonosításra alkalmas adatok észlelése céljából.](cognitive-search-skill-pii-detection.md) A rekord maximális méretének 50 000 karakternek [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)kell lennie a szerint mérve. Ha meg kell szakítania az adatokat, mielőtt elküldené azokat a véleményelemzőnek, használja a [Szövegfelosztás (Szövegfelosztás) készséget.](cognitive-search-skill-textsplit.md)

## <a name="throttling-limits"></a>Szabályozási határértékek

A keresési lekérdezési és indexelési kérelmek szabályozása a rendszer csúcskapacitáshoz közeledve. A szabályozás eltérően viselkedik a különböző API-k esetében. Lekérdezési API-k (Search/Suggest/Autocomplete) és indexelő API-k szabályozása dinamikusan a szolgáltatás terhelése alapján. Az index API-k statikus kérelemsebesség-korlátokkal rendelkeznek. 

Az indexhez kapcsolódó műveletek statikus sebességkérelem-korlátai:

+ Listaindexek (GET /indexek): 5 másodpercenként keresési egységenként
+ Index bekéselése (GET /indexes/myindex): 10 másodpercenként keresési egységenként
+ Index létrehozása (POST /indexek): 12 percenként keresési egységenként
+ Index létrehozása vagy frissítése (PUT /indexes/myindex): 6 másodpercenként keresési egységenként
+ Index törlése (DELETE /indexes/myindex): 12 percenként keresési egységenként 

## <a name="api-request-limits"></a>API-kérelmek korlátai
* Kérésenként legfeljebb 16 MB <sup>1</sup>
* Legfeljebb 8 KB URL hossza
* Indexfeltöltések, egyesítések és törlések kötegenkénti maximális 1000 dokumentuma
* Legfeljebb 32 mező $orderby záradékban
* A keresési kifejezés maximális mérete 32 766 bájt (32 KB mínusz 2 bájt) UTF-8 kódolású szövegből

<sup>1</sup> Az Azure Cognitive Search, a törzs egy kérelem van érvényben egy felső határ 16 MB, megszabva a gyakorlati korlátot az egyes mezők vagy gyűjtemények, amelyek egyébként nem korlátozza az elméleti korlátok [(lásd: Támogatott adattípusok](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) további információt a mező összetételét és korlátozások).

## <a name="api-response-limits"></a>API-válaszkorlátok
* A keresési eredmények oldalanként visszaadott összesen legfeljebb 1000 dokumentum
* Legfeljebb 100 javaslat érkezett javasolt API-kérelemenként

## <a name="api-key-limits"></a>API-kulcskorlátok
Az API-kulcsok a szolgáltatás hitelesítéséhez használatosak. Két típusukat különböztetjük meg. A rendszergazdai kulcsok a kérelem fejlécében vannak megadva, és teljes körű írási és olvasási hozzáférést biztosítanak a szolgáltatáshoz. A lekérdezési kulcsok írásvédettek, az URL-címen vannak megadva, és általában az ügyfélalkalmazások között vannak elosztva.

* Szolgáltatásonként legfeljebb 2 rendszergazdai kulcs
* Szolgáltatásonként legfeljebb 50 lekérdezési kulcs