---
title: A keresési eredmények kelvaló megoldása
titleSuffix: Azure Cognitive Search
description: A keresési eredmények strukturálása és rendezése, a dokumentumok számának leése, valamint a tartalomnavigáció hozzáadása a keresési eredményekhez az Azure Cognitive Search alkalmazásban.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 124f1ce3d30ce87d5e9d8fa027e5a7d6c0b3cb17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481602"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>A keresési eredmények működése az Azure Cognitive Search szolgáltatásban
Ez a cikk útmutatást nyújt a keresési eredményoldal szabványos elemeinek megvalósításához, például az összes számhoz, a dokumentum-visszakereséshez, a rendezési sorrendhez és a navigációhoz. Az oldallal kapcsolatos beállításokat, amelyek adatokat vagy információkat adnak a keresési eredményekhez, az Azure Cognitive Search szolgáltatásnak küldött [keresési dokumentum-kérelmek](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) határozzák meg. 

A REST API-ban a kérelmek tartalmaznak egy GET parancsot, elérési utat és lekérdezési paramétereket, amelyek tájékoztatják a szolgáltatást a kért, és hogyan fogalmazza meg a választ. A .NET SDK-ban az egyenértékű API a [DocumentSearchResult class](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1).

Ha gyorsan szeretne keresési oldalt létrehozni az ügyfél számára, fedezze fel az alábbi lehetőségeket:

+ A [portálalkalmazás-generátor](search-create-app-portal.md) segítségével hozzon létre egy HTML-lapot keresősávval, csiszolt navigációval és eredményterülettel.
+ Kövesse [az első alkalmazás létrehozása a C# oktatóanyagban](tutorial-csharp-create-first-app.md) egy funkcionális ügyfél létrehozásához.

Számos kódminta tartalmaz egy webes előtér-felületet, amely itt található: [New York City Jobs demo app](https://aka.ms/azjobsdemo), [JavaScript mintakód egy élő demo site](https://github.com/liamca/azure-search-javascript-samples), és [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).

> [!NOTE]
> Az érvényes kérelem számos elemet tartalmaz, például egy szolgáltatás `api-version`URL-címét és elérési útját, HTTP-műveletet és így tovább. A rövidség, mi nyírt a példákat, hogy kiemelje csak a szintaxis, amely releváns a tördelés. A kérelem szintaxisáról az [Azure Cognitive Search REST API-k című témakörben olvashat bővebben.](https://docs.microsoft.com/rest/api/searchservice)
>

## <a name="total-hits-and-page-counts"></a>Találatok és oldalak összes száma

A lekérdezésből visszaadott eredmények teljes számának megjelenítése, majd az eredmények kisebb részekben történő visszaadása alapvető fontosságú gyakorlatilag az összes keresési oldal számára.

![][1]

Az Azure Cognitive Search, `$count` `$top`használja `$skip` a , és a paraméterek et adja vissza ezeket az értékeket. A következő példa egy "online-katalógus" nevű index teljes lekérésére vonatkozó mintakérelmet mutat be, amely a következő ként jelenik meg: `@odata.count`

    GET /indexes/online-catalog/docs?$count=true

Dokumentumok beolvasása 15 fős csoportokban, és az első oldaltól kezdődően az összes találatot is megjelenítve:

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

Az eredmények lapozásához `$top` `$skip`a `$top` és a pontra is szükség van, `$skip` hogy hány cikket kell visszaadni egy kötegben, és adja meg, hogy hány elemet kell kihagyni. A következő példában minden oldalon látható a következő 15 elem, `$skip` amelyet a paraméter növekményes ugrásai jeleznek.

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=15&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=30&$count=true

## <a name="layout"></a>Elrendezés

A keresési eredményoldalon megjeleníthetegy miniatűr képet, egy mezőrészhalmazt és egy teljes termékoldalra mutató hivatkozást.

 ![][2]

Az Azure Cognitive Search, `$select` akkor használja, és egy [keresési API-kérelmet,](https://docs.microsoft.com/rest/api/searchservice/search-documents) hogy ezt a felhasználói élményt.

A mozaikszerű elrendezés mezőinek egy részhalmazának visszaadása:

    GET /indexes/online-catalog/docs?search=*&$select=productName,imageFile,description,price,rating

A képek és a médiafájlok közvetlenül nem kereshetőek, és a költségek csökkentése érdekében egy másik tárolási platformon, például az Azure Blob storage-ban kell tárolni őket. Az indexben és a dokumentumokban definiáljon egy mezőt, amely a külső tartalom URL-címét tárolja. Ezután a mezőt képhivatkozásként használhatja. A kép URL-címének a dokumentumban kell lennie.

Ha egy **kattintással elérhető** esemény termékleírásának oldalát szeretné beolvasni, a [Lookup Document segítségével](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) adja át a dokumentum kulcsát a beolvasáshoz. A kulcs adattípusa `Edm.String`. Ebben a példában *ez 246810*.

    GET /indexes/online-catalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>Rendezés relevancia, minősítés vagy ár szerint

A rendezési sorrendek gyakran relevanciát mutatnak, de gyakori, hogy az alternatív rendezési rendelések könnyen elérhetők legyenek, így az ügyfelek gyorsan átrendezhetik a meglévő eredményeket egy másik rangsorolási sorrendbe.

 ![][3]

Az Azure Cognitive Search, rendezése `$orderby` a kifejezés alapján, az `"Sortable": true.` összes `$orderby` olyan mező, amely indexelt egy záradék egy OData-kifejezés. A szintaxisról a [szűrők és a rendelési záradékok OData kifejezés szintaxisa című témakörben talál](query-odata-filter-orderby-syntax.md)további információt.

A relevancia szorosan kapcsolódik a pontozási profilokhoz. Használhatja az alapértelmezett pontozást, amely a szövegelemzésre és a statisztikákra támaszkodik az összes eredmény rangsorolásához, a magasabb pontszámok pedig olyan dokumentumokhoz, amelyek több vagy erősebb egyezést tartalmaznak egy keresési kifejezésen.

Az alternatív rendezési sorrendek általában **olyan onClick-eseményekhez** vannak társítva, amelyek a rendezési sorrendet létrehozó metódusra hívnak vissza. Például, mivel ez az oldal elem:

 ![][4]

Hozzon létre egy módszert, amely elfogadja a kiválasztott rendezési beállítást bemenetként, és egy rendezett listát ad vissza a beállításhoz társított feltételekhez.

 ![][5]

> [!NOTE]
> Bár az alapértelmezett pontozás elegendő számos forgatókönyv esetén, azt javasoljuk, hogy a relevanciát egy egyéni pontozási profilra alapozza. Az egyéni pontozási profil lehetővé teszi a vállalkozás számára előnyösebb elemek kiemelését. További információ: [Pontozási profilok hozzáadása.](index-add-scoring-profiles.md)
>

## <a name="hit-highlighting"></a>Találatok kiemelése

A keresési eredményekben formázást alkalmazhat az egyező kifejezésekre, így könnyen kiszúrhatja az egyezést. A lekéréshez a [lekéréshez](https://docs.microsoft.com/rest/api/searchservice/search-documents)a lekéréshez a lekéréshez található a leemelési utasítások. 

A formázás teljes kifejezéslekérdezésekre vonatkozik. A részleges kifejezésekkel kapcsolatos lekérdezések, például az intelligens keresés vagy a helyettesítő karakteres keresés, amelyek lekérdezésbővítést eredményeznek a motorban, nem használhatják a találatkiemelést.

```http
POST /indexes/hotels/docs/search?api-version=2019-05-06 
    {  
      "search": "something",  
      "highlight": "Description"  
    }
```

> [!IMPORTANT]
> A 2020. Az ezen időpont előtt létrehozott szolgáltatások nem változnak a kiemelési viselkedésükben. Ezzel a módosítással csak a teljes kifejezéslekérdezésnek megfelelő kifejezések et adja vissza a rendszer. Emellett meg lehet adni a kiemeléshez visszaadott töredékméretet is.
>
> Amikor olyan ügyfélkódot ír, amely a leírás kiemelését valósítja meg, vegye figyelembe ezt a változást. Ne feledje, hogy ez nem lesz hatással, ha nem hoz létre egy teljesen új keresési szolgáltatást.

## <a name="faceted-navigation"></a>Jellemzőalapú navigáció

A keresési navigáció gyakori az eredményoldalon, amely gyakran az oldal oldalán vagy tetején található. Az Azure Cognitive Search, a sokoldalú navigáció biztosít önálló irányított keresés alapján előre definiált szűrők. A [részleteket lásd: Faceted navigation az Azure Cognitive Search szolgáltatásban.](search-faceted-navigation.md)

## <a name="filters-at-the-page-level"></a>Szűrők oldalszinten

Ha a megoldás kialakítása adott típusú tartalomra (például egy olyan online kiskereskedelmi alkalmazásra, amelynek részlegei az oldal tetején vannak felsorolva) külön keresési oldalakat tartalmaz, akkor egy **onClick-esemény** mellé [szűrőkifejezést](search-filters.md) szúrhat be, hogy előre szűrt állapotban nyisson meg egy oldalt.

Szűrőt keresési kifejezéssel vagy anélkül is küldhet. A következő kérelem például a márkanévre szűr, és csak azokat a dokumentumokat adja vissza, amelyek megfelelnek az adatoknak.

    GET /indexes/online-catalog/docs?$filter=brandname eq 'Microsoft' and category eq 'Games'

A kifejezésekről további információt a Search `$filter` Documents [(Azure Cognitive Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) című témakörben talál.

## <a name="see-also"></a>Lásd még:

- [Az Azure Cognitive Search REST API-jai](https://docs.microsoft.com/rest/api/searchservice)
- [Indexműveletek](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
- [Dokumentumműveletek](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
- [Kifejezéstelen navigáció az Azure kognitív keresésében](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png
