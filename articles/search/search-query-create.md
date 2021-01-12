---
title: Alapszintű lekérdezés létrehozása
titleSuffix: Azure Cognitive Search
description: Megtudhatja, hogyan hozhat létre lekérdezési kéréseket a Cognitive Searchban, mely eszközök és API-k használhatók a teszteléshez és a kódoláshoz, és hogy a lekérdezési döntések hogyan kezdődnek az indexek
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: 9bee391ddb0fa6c270c6d833fb7e81d5f4880497
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98118642"
---
# <a name="create-a-query-in-azure-cognitive-search"></a>Lekérdezés létrehozása az Azure Cognitive Searchban

Ha első alkalommal hoz létre egy lekérdezést, ez a cikk ismerteti azokat az eszközöket és API-kat, amelyekhez szükség van a lekérdezés létrehozásához, valamint arról, hogy az indexelési struktúra és a tartalom milyen hatással lehet a lekérdezések eredményeire. A lekérdezési kérések [és az összeállítások](search-query-overview.md)bevezetéséhez.

## <a name="choose-tools-and-apis"></a>Eszközök és API-k kiválasztása

Lekérdezés létrehozásához eszközre vagy API-ra van szükség. A következő javaslatok bármelyike hasznos lehet tesztelési és éles számítási feladatokhoz.

| Módszertan | Leírás |
|-------------|-------------|
| Portál| A [Search Explorer (portál)](search-explorer.md) a Azure Portal lekérdezési felülete, amely az alapul szolgáló keresési szolgáltatás indexekkel kapcsolatos lekérdezéseket futtat. A portál REST API hívásokat kezdeményez a jelenetek mögött a [keresési dokumentumok](/rest/api/searchservice/search-documents) művelethez, de nem tud automatikus kiegészítést, javaslatot vagy dokumentum-keresést meghívni.<br/><br/> Bármelyik indexet és REST API verziót kiválaszthatja, beleértve az előnézet is. A lekérdezési karakterláncok egyszerű vagy teljes szintaxist használhatnak az összes lekérdezési paraméter (Filter, select, searchFields stb.) támogatásával. A portálon, amikor megnyit egy indexet, az egymás melletti lapfüleken található JSON-definícióval együtt használhatja a keresési Explorert a mezők attribútumaihoz való könnyű hozzáférés érdekében. Vizsgálja meg, hogy mely mezők kereshetők, rendezhető, szűrhető és sokrétűek a lekérdezések tesztelése során. <br/>Korai vizsgálathoz, teszteléshez és érvényesítéshez ajánlott. [Részletek](search-explorer.md) |
| Webes tesztelési eszközök| A [Poster](search-get-started-rest.md) vagy a [Visual Studio Code](search-get-started-vs-code.md) erős választás a [keresési dokumentumok](/rest/api/searchservice/search-documents) iránti kérelmek összeállításához, valamint minden más, a REST-kérelemhez. A REST API-k minden lehetséges programozási műveletet támogatnak az Azure Cognitive Searchban, és ha olyan eszközt használ, mint a Poster vagy a Visual Studio Code, interaktív módon is kiadhatja a kéréseket, hogy megtudja, hogyan működik a szolgáltatás a programkódba való befektetés előtt. A webes tesztelési eszköz jó választás, ha nincs közreműködői vagy rendszergazdai jogosultsága a Azure Portal. Ha a keresési URL-cím és a lekérdezési API-kulcs van, akkor az eszközök segítségével futtathat lekérdezéseket egy meglévő indexen. |
| Azure SDK | Ha készen áll a kód írására, használhatja a .NET, Python, JavaScript vagy Java rendszerhez készült Azure SDK-k Azure.Search.Document. Az SDK-nak saját kiadási ütemterve van, de mindegyikben létrehozhat és lekérdezheti az indexeket. <br/><br/>A [SearchClient (.net)](/dotnet/api/azure.search.documents.searchclient) a keresési indexek lekérdezésére használható a C#-ban.  [Részletek](search-howto-dotnet-sdk.md)<br/><br/>A [SearchClient (Python)](/dotnet/api/azure.search.documents.searchclient) használatával lekérdezhető a Pythonban egy keresési index. [Részletek](search-get-started-python.md)<br/><br/>A [SearchClient (JavaScript)](/dotnet/api/azure.search.documents.searchclient) használatával lekérdezhető a keresési index a JavaScriptben. [Részletek](search-get-started-javascript.md) |

## <a name="set-up-a-search-client"></a>Keresési ügyfél beállítása

A keresési ügyfél hitelesíti a keresési szolgáltatást, küldi a kérelmeket, és kezeli a válaszokat. Függetlenül attól, hogy melyik eszközt vagy API-t használja, a keresési ügyfélnek a következőkkel kell rendelkeznie:

| Tulajdonságok | Leírás |
|------------|-------------|
| Végpont | A keresési szolgáltatás a következő formátumú URL-cím: `https://[service-name].search.windows.net` . |
| API-hozzáférési kulcs (rendszergazda vagy lekérdezés) | Hitelesíti a kérést a keresési szolgáltatásban. |
| Index neve | A lekérdezések mindig egyetlen index dokumentumainak gyűjteményére vannak irányítva. Az indexek nem csatlakoztathatók, és nem hozhatnak létre egyéni vagy ideiglenes adatstruktúrákat lekérdezési célként. |
| API-verzió | A REST-hívások explicit módon igénylik a `api-version` kérést. Ezzel szemben az Azure SDK-ban található ügyféloldali kódtárak egy adott REST API verzióra vonatkoznak. Az SDK-k esetében a `api-version` implicit. |

### <a name="in-the-portal"></a>A portálon

A keresési Explorer és egyéb portál eszközök beépített ügyfélkapcsolatot biztosítanak a szolgáltatáshoz, közvetlen hozzáférési indexekkel és más objektumokkal a portál oldalain. Az eszközökhöz, a varázslókhoz és az objektumokhoz való hozzáféréshez a közreműködő szerepkör vagy a szolgáltatáson felüli tagság szükséges. 

### <a name="using-rest"></a>A REST használata

REST-hívások esetén a [Poster vagy hasonló eszközöket](search-get-started-rest.md) használhatja az ügyfélként a [keresési dokumentumok](/rest/api/searchservice/search-documents) kérésének megadásához. Minden kérelem önálló, ezért minden kérelemnél meg kell adnia a végpontot, az index nevét és az API-verziót. A kérelem fejlécében a többi tulajdonság, a Content-Type és az API-kulcs is át lesz adva. 

A POST vagy a GET paranccsal lekérdezéseket végezhet az indexben. A POST, a kérés törzsében megadott paraméterekkel könnyebben használható. Ha a POST lehetőséget használja, ügyeljen arra, hogy szerepeljen az `docs/search` URL-címben:

```http
POST https://myservice.search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "*"
}
```

### <a name="using-azure-sdks"></a>Azure SDK-k használata

Ha Azure SDK-t használ, a-ügyfelet kódban kell létrehoznia. Az SDK-k minden olyan keresési ügyfelet biztosítanak, amely megtarthatja az állapotot, lehetővé téve a kapcsolatok újrafelhasználását. A lekérdezési műveletekhez a következő tulajdonságokat kell létrehoznia, **`SearchClient`** és értékeket kell megadni: Endpoint, Key, index. Ezután meghívja a **`Search method`** to pass szót a lekérdezési karakterláncban. 

| Nyelv | Ügyfél | Példa |
|----------|--------|---------|
| C# és .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [Az első keresési lekérdezés küldése a C-ben #](/dotnet/api/overview/azure/search.documents-readme#send-your-first-search-query) |
| Python      | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [Az első keresési lekérdezés küldése a Pythonban](/python/api/overview/azure/search-documents-readme#send-your-first-search-request) |
| Java        | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [Az első keresési lekérdezés elküldése Java-ban](/java/api/overview/azure/search-documents-readme#send-your-first-search-query)  |
| JavaScript  | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | [Az első keresési lekérdezés küldése a JavaScriptben](/javascript/api/overview/azure/search-documents-readme#send-your-first-search-query)  |

## <a name="choose-a-parser-simple--full"></a>Válasszon elemzőt: Simple | teljes

Ha a lekérdezés teljes szöveges keresést végez, a rendszer elemzőt használ a keresési paraméter tartalmának feldolgozásához. Az Azure Cognitive Search két lekérdezés-elemzőt kínál. Az egyszerű elemző megérti az [egyszerű lekérdezési szintaxist](query-simple-syntax.md). Ez az elemző lett kiválasztva, mivel a gyorsasága és hatékonysága az ingyenes szöveges lekérdezésekben nem megfelelő. A szintaxis támogatja a gyakori keresési operátorokat (és, vagy nem) a kifejezésre és kifejezésre való keresésekhez, valamint az előtag ( `*` ) kereséshez (mint a "Sea *" a Seattle és a Seaside esetében). Általános javaslat, hogy először az egyszerű elemzőt próbálja ki, majd a teljes elemzőre váltson, ha az alkalmazásra vonatkozó követelmények nagyobb teljesítményű lekérdezéseket hívnak meg.

A kérelemhez való hozzáadáskor engedélyezett [teljes Lucene-lekérdezési szintaxis](query-Lucene-syntax.md#bkmk_syntax) `queryType=full` az [Apache Lucene-elemző](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)alapján történik.

A teljes szintaxis és az egyszerű szintaxis átfedésben van azzal a mértéktel, amely egyszerre támogatja ugyanazt az előtagot és logikai műveleteket, de a teljes szintaxis több operátort is biztosít. Teljes mértékben több operátor is létezik a logikai kifejezésekhez, és további operátorok találhatók a speciális lekérdezésekhez, például a intelligens kereséshez, a helyettesítő karakteres kereséshez, a közelségi kereséshez és a reguláris kifejezésekhez.

## <a name="choose-query-methods"></a>Lekérdezési módszerek kiválasztása

A keresés alapvetően egy felhasználó által vezérelt gyakorlat, amelyben a feltételek vagy kifejezések gyűjtése egy keresőmező alapján történik, vagy az adott oldalon lévő események elemre kattintva. A következő táblázat összefoglalja azokat a mechanizmusokat, amelyekkel összegyűjtheti a felhasználói adatokat, valamint a várt keresési élményt.

| Bevitel | Élmény |
|-------|---------|
| [Keresési módszer](/rest/api/searchservice/search-documents) | A felhasználók kifejezéseket és kifejezéseket is begépelnek egy keresési mezőbe, operátorral vagy anélkül, és a Keresés gombra kattintanak a kérelem elküldéséhez. A Keresés az ugyanazon kérelemben található szűrőkkel is használható, de nem automatikus kiegészítéssel vagy javaslatokkal. |
| [Automatikus kiegészítési módszer](/rest/api/searchservice/autocomplete) | A felhasználók néhány karaktert begépelnek, és a rendszer minden új karakter beírása után kezdeményezi a lekérdezéseket. A válasz egy befejezett sztring az indexből. Ha a megadott karakterlánc érvényes, a felhasználó a Keresés gombra kattintva küldi el a lekérdezést a szolgáltatásnak. |
| [Javaslatok metódusa](/rest/api/searchservice/suggestions) | Az automatikus kiegészítéshez hasonlóan a felhasználók csak néhány karaktert és növekményes lekérdezéseket hoznak létre. A válasz a megfelelő dokumentumok legördülő listája, amelyet jellemzően néhány egyedi vagy leíró mező képvisel. Ha bármelyik kijelölés érvényes, a felhasználó rákattint az egyikre, és a rendszer visszaadja a megfelelő dokumentumot. |
| [Jellemzőalapú navigáció](/rest/api/searchservice/search-documents#query-parameters) | Egy oldalon a keresés hatókörét szűkítő, kattintható navigációs hivatkozások vagy zsemlemorzsa látható. Egy csiszolt navigációs struktúra dinamikusan áll a kezdeti lekérdezés alapján. Például `search=*` egy olyan csiszolt navigációs fa feltöltéséhez, amely minden lehetséges kategóriából tevődik össze. Egy lekérdezési válaszban egy sokoldalú navigációs struktúra jön létre, de a következő lekérdezés kiírására szolgáló mechanizmus is. n REST API hivatkozás a `facets` keresési dokumentumok művelet lekérdezési paramétereként van dokumentálva, de a paraméter nélkül is használható `search` .|
| [Szűrési módszer](/rest/api/searchservice/search-documents#query-parameters) | A szűrők a dimenziók használatával szűkítik az eredményeket. Az oldal mögött egy szűrőt is alkalmazhat, például az oldal inicializálásához nyelvspecifikus mezőkkel. REST API-hivatkozásban a a `$filter` keresési dokumentumok művelet lekérdezési paramétereként van dokumentálva, de a paraméter nélkül is használható `search` .|

## <a name="know-your-field-attributes"></a>A mező attribútumainak megismerése

Ha korábban áttekintette a [lekérdezési kérelem alapjait](search-query-overview.md), megjegyezheti, hogy a lekérdezési kérelem paraméterei attól függnek, hogy a mezők hogyan legyenek indexelve az indexben. A lekérdezésekben, szűrési vagy rendezési sorrendben való használatra például egy mezőnek *kereshetőnek*, *szűrhetőnek* *és rendezhető* kell lennie. Hasonlóképpen, a találatok között csak a *beolvasható* mezők láthatók. A `search` `filter` kérelemben szereplő, és paraméter megadásakor `orderby` ügyeljen arra, hogy a nem várt eredmények elkerülése érdekében ellenőrizze az attribútumokat.

Az alábbi, a [Hotel minta indexét](search-get-started-portal.md)tartalmazó képernyőképen csak az utolsó két mező ("" lastrenovationdate "és" minősítés " `"$orderby"` ) használható egyetlen záradékban.

![A szállodai minta index-definíciója](./media/search-query-overview/hotel-sample-index-definition.png "A szállodai minta index-definíciója")

A Mezőtulajdonságok leírását lásd: [create index (REST API)](/rest/api/searchservice/create-index).

## <a name="know-your-tokens"></a>A jogkivonatok ismerete

Az indexelés során a lekérdezési motor egy elemzőt használ a karakterláncok szöveges elemzéséhez, és maximalizálja a lekérdezési időponthoz való egyeztetés lehetőségét. A karakterláncok minimálisan kisebbek, de morfológiai elemzéshez is lehetnek, és leállíthatók a Word eltávolítása. A nagyobb sztringeket vagy összetett szavakat általában szóközzel, kötőjelekkel vagy kötőjelekkel, valamint különálló tokenként indexelve kell elosztani. 

Az a pont, amellyel elkerülheti, hogy mit gondol az index, és hogy mi valójában, eltérő lehet. Ha a lekérdezések nem adják vissza a várt eredményeket, megvizsgálhatja az analizátor által létrehozott jogkivonatokat az [elemzés szövege alapján (REST API)](/rest/api/searchservice/test-analyzer). További információ a jogkivonatok létrehozása és a lekérdezések hatásáról: [részleges kifejezéses keresés és minták speciális karakterekkel](search-query-partial-matching.md).

## <a name="next-steps"></a>Következő lépések

Most, hogy jobban megértette a lekérdezési kérések kialakítását, próbálja ki a következő gyors útmutatót a gyakorlati élményhez.

+ [Keresési ablak](search-explorer.md)
+ [A lekérdezés a REST-ben](search-get-started-rest.md)
+ [Lekérdezés a .NET-ben](search-get-started-dotnet.md)
+ [Lekérdezés a Pythonban](search-get-started-python.md)
+ [Lekérdezés a JavaScriptben](search-get-started-javascript.md)