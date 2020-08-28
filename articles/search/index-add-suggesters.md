---
title: Javaslattevő létrehozása
titleSuffix: Azure Cognitive Search
description: Adja meg az Azure Cognitive Search típus-előkészítő lekérdezési műveleteit a javaslatok létrehozásával, valamint az automatikus kiegészítés vagy az automatikus javaslattal rendelkező lekérdezési feltételeket meghívó kérelmek összeállításával.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/21/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 4889e73e851e285c84d5d4429298e9a7cdacc140
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "89014387"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>Javaslat létrehozása az automatikus kiegészítés és a javasolt eredmények lekérdezésben való engedélyezéséhez

Az Azure Cognitive Searchban a "keresés a típusban" beállítás egy [keresési indexhez](search-what-is-an-index.md)hozzáadott, **javaslattal** rendelkező konstruktoron keresztül érhető el. A javaslatok két szolgáltatást támogatnak: az *automatikus kiegészítést*, amely egy teljes kifejezésből álló lekérdezés részleges bemenetét fejezi be, és *javaslatokat* tesz arra, hogy egy adott egyezésre mutasson. Az automatikus kiegészítés létrehoz egy lekérdezést. A javaslatok megfelelő dokumentumot hoznak létre.

A következő képernyőkép az [első alkalmazás létrehozása a C#-ban](tutorial-csharp-type-ahead-and-suggestions.md) című ábrán látható. Az automatikus kiegészítés egy lehetséges kifejezést is felhasznál, amely a "tw"-t "a" értékkel fejezi ki. A javaslatok a mini keresési eredmények, ahol a Hotel neve a megfelelő szállodai keresési dokumentumot jelöli az indexből. A javaslatok esetében bármilyen olyan mezőt felvehet, amely leíró információkat biztosít.

![Az automatikus kiegészítés és a javasolt lekérdezések vizuális összehasonlítása](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Az automatikus kiegészítés és a javasolt lekérdezések vizuális összehasonlítása")

Ezeket a szolgáltatásokat külön vagy együtt is használhatja. A viselkedés Azure Cognitive Searchban való megvalósításához index és lekérdezési összetevő szükséges. 

+ Az indexben adjon hozzá egy mutatót egy indexhez. Használhatja a portált, a [REST API](/rest/api/searchservice/create-index)vagy a [.net SDK](/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)-t. A cikk további részében a javaslatok létrehozására összpontosítunk.

+ A lekérdezési kérelemben hívja meg az [alább felsorolt API](#how-to-use-a-suggester)-k egyikét.

A keresési típust támogató támogatás engedélyezett a karakterlánc típusú mezők esetében a mező alapján. Mindkét typeahead-viselkedést ugyanazon keresési megoldáson belül implementálhatja, ha a képernyőképen láthatóhoz hasonló élményre van szüksége. Mindkét kérelem célja az adott index és válaszok *dokumentumainak* gyűjteménye, miután egy felhasználó legalább három karakterből álló bemeneti karakterláncot adott meg.

## <a name="what-is-a-suggester"></a>Mi az a javaslat?

A javaslat egy belső adatstruktúra, amely támogatja a keresési típusokat a részleges lekérdezésekhez tartozó előtagok tárolásával. A jogkivonatos kifejezésekhez hasonlóan a rendszer az előtagokat invertált indexekben tárolja, az egyiket a javaslatok mezőinek gyűjteményében megadott mezőkhöz.

## <a name="define-a-suggester"></a>Javaslat definiálása

A javaslatok létrehozásához adjon hozzá egyet egy [index sémához](/rest/api/searchservice/create-index) , és [állítsa be az egyes tulajdonságokat](#property-reference). A javaslatok létrehozásához a legjobb idő az, amikor azt a mezőt is meghatározza, amelyik azt fogja használni.

+ Csak karakterlánc-mezők használata

+ Az alapértelmezett standard Lucene Analyzer ( `"analyzer": null` ) vagy a [Language Analyzer](index-add-language-analyzers.md) (például `"analyzer": "en.Microsoft"` ) használata a mezőben

### <a name="choose-fields"></a>Mezők kiválasztása

Bár a javaslatok több tulajdonsággal rendelkeznek, ez elsősorban olyan karakterlánc-mezők gyűjteménye, amelyekhez egy keresési típust engedélyez. Minden indexhez van egy javaslat, így a javaslatok listájának tartalmaznia kell az összes olyan mezőt, amely a javaslatokhoz és az automatikus kiegészítésekhez is hozzájárul a tartalomhoz.

Az automatikus kiegészítés kihasználja a mezők nagyobb készletének előnyeit, mivel a további tartalomhoz több távú befejezési lehetőség áll rendelkezésre.

A javaslatok azonban jobb eredményeket eredményeznek, ha a kiválasztott mező szelektív. Ne feledje, hogy a javaslat egy keresési dokumentum proxyja, ezért olyan mezőket szeretne használni, amelyek a legjobban egyetlen eredményt képviselnek. Azok a nevek, címek vagy más egyedi mezők, amelyek megkülönböztetik a többszörös egyezések közül a legmegfelelőbbet. Ha a mezők ismétlődő értékekből állnak, a javaslatok azonos eredményekből állnak, és a felhasználók nem tudják, melyikre kattintanak.

Ahhoz, hogy mindkét keresési típust kielégítse, adja hozzá az automatikus kiegészítéshez szükséges összes mezőt, majd **$Select**, **$Top**, **$Filter**és **searchFields** használatával irányítsa a javaslatok eredményeit.

### <a name="choose-analyzers"></a>Elemzők kiválasztása

Az analizátor választása határozza meg, hogy a mezők hogyan legyenek jogkivonatban, és hogyan lesznek előre rögzítve. Például egy olyan elválasztott karakterlánc esetében, mint a "környezetfüggő", a nyelvi elemző használatával a következő jogkivonat-kombinációkat fogja eredményezni: "Context", "szenzitív", "Context-szenzitív". A standard Lucene Analyzert használta, az elválasztott karakterlánc nem létezik. 

Az elemzők kiértékelése során érdemes lehet a [text API elemzése](/rest/api/searchservice/test-analyzer) segítségével betekintést készíteni a feltételek jogkivonatának és a későbbiekben előre rögzített állapotának megismerésére. Miután létrehozta az indexet, különböző elemzőket próbálhat ki egy sztringen a jogkivonat kimenetének megtekintéséhez.

Az [Egyéni elemzőket](index-add-custom-analyzers.md) vagy [előre definiált elemzőket](index-add-custom-analyzers.md#predefined-analyzers-reference) használó mezők (a standard Lucene kivételével) kifejezetten nem teszik lehetővé a gyenge eredmények elkerülését.

> [!NOTE]
> Ha az analizátor korlátozását kell meghoznia, például ha egy kulcsszóra vagy ngram-elemzőre van szüksége bizonyos lekérdezési forgatókönyvekhez, két külön mezőt kell használnia ugyanahhoz a tartalomhoz. Ez lehetővé teszi, hogy az egyik mező legyen egy javaslat, míg a másik egyéni Analyzer-konfigurációval is beállítható.

### <a name="when-to-create-a-suggester"></a>Mikor hozzon létre egy javaslatot

A javaslatok létrehozásához a legjobb idő az, amikor saját maga is létrehozza a mező definícióját.

Ha már meglévő mezők használatával próbál létrehozni egy javaslatot, az API letiltja azt. Az előtagok az indexelés során jönnek létre, ha a két vagy több karakterből álló kombinációk részleges kifejezései a teljes feltételek mellett jogkivonatot kapnak. Mivel a meglévő mezők már jogkivonattal rendelkeznek, újra kell építenie az indexet, ha hozzá kívánja adni őket egy javaslathoz. További információ: [Azure Cognitive Search index újraépítése](search-howto-reindex.md).

## <a name="create-using-rest"></a>Létrehozás a REST használatával

A REST API adjon hozzá javaslatokat a [create index](/rest/api/searchservice/create-index) vagy az [Update index](/rest/api/searchservice/update-index)használatával. 

  ```json
  {
    "name": "hotels-sample-index",
    "fields": [
      . . .
          {
              "name": "HotelName",
              "type": "Edm.String",
              "facetable": false,
              "filterable": false,
              "key": false,
              "retrievable": true,
              "searchable": true,
              "sortable": false,
              "analyzer": "en.microsoft",
              "indexAnalyzer": null,
              "searchAnalyzer": null,
              "synonymMaps": [],
              "fields": []
          },
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["HotelName"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```

## <a name="create-using-net"></a>Létrehozás .NET használatával

A C# nyelvben Definiáljon egy [javaslat objektumot](/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters` egy gyűjtemény, de csak egyetlen tételt vehet igénybe. 

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels-sample-index",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelName", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

## <a name="property-reference"></a>Tulajdonság leírása

|Tulajdonság      |Leírás      |
|--------------|-----------------|
|`name`        |A javaslat neve.|
|`searchMode`  |A jelölt kifejezésekre való kereséshez használt stratégia. Az egyetlen jelenleg támogatott mód a (z `analyzingInfixMatching` ), amely jelenleg a kifejezés kezdetének felel meg.|
|`sourceFields`|Egy vagy több olyan mező listája, amely a javaslatok forrását képezi. A mezőknek Type és típusúnak kell lenniük `Edm.String` `Collection(Edm.String)` . Ha a mezőben egy analizátor van megadva, akkor a [listán szereplő](/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) elemzőnek (nem egyéni elemzőnek) kell lennie.<p/> Ajánlott eljárásként csak azokat a mezőket kell megadnia, amelyek a várt és a megfelelő választ adják meg, legyen szó egy keresési sávon vagy egy legördülő listában szereplő befejezett sztringről.<p/>A Hotel neve jó jelölt, mert pontossággal rendelkezik. A részletes mezők, például a leírások és a megjegyzések túl sűrűk. Ehhez hasonlóan az ismétlődő mezők, például a kategóriák és a címkék kevésbé hatékonyak. A példákban bemutatjuk a "kategória" kifejezést is, amely azt mutatja be, hogy több mezőt is tartalmazhat. |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>Javaslat használata

A rendszer egy lekérdezést használ. A javaslat létrehozása után hívja meg a következő API-k egyikét a keresési típushoz:

+ [Javaslatok REST API](/rest/api/searchservice/suggestions) 
+ [Automatikus kiegészítés REST API](/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync metódus](/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [AutocompleteWithHttpMessagesAsync metódus](/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

Egy keresési alkalmazásban az ügyfél kódjának egy olyan könyvtárat kell kihasználnia, mint a [jQuery felhasználói felületének automatikus kiegészítése](https://jqueryui.com/autocomplete/) a részleges lekérdezés összegyűjtéséhez és a egyezés biztosításához. További információ erről a feladatról: [automatikus kiegészítés hozzáadása vagy javasolt eredmények az ügyfél kódjához](search-autocomplete-tutorial.md).

Az API-használat a következő, az automatikus kiegészítési REST APIra irányuló hívásban látható. Ebből a példából két elvihetőség van. Először is, ahogy az összes lekérdezés esetében, a művelet egy index dokumentumainak gyűjteményén alapul, és a lekérdezés tartalmaz egy **keresési** paramétert, amely ebben az esetben a részleges lekérdezést biztosítja. Másodszor, **suggesterName** kell hozzáadnia a kéréshez. Ha egy javaslat nincs definiálva az indexben, az automatikus kiegészítés vagy a javaslatok sikertelenek lesznek.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>Mintakód

+ [Az első alkalmazás létrehozása a C#-ban (3. lecke – keresési típus hozzáadása)](tutorial-csharp-type-ahead-and-suggestions.md) minta a javaslatok kialakítását, a javasolt lekérdezéseket, az automatikus kiegészítést és a sokoldalú navigációt mutatja be. Ez a mintakód egy sandbox Azure Cognitive Search szolgáltatásban fut, és egy előre betöltött Hotel-indexet használ, így mindössze annyit kell tennie, hogy az alkalmazás futtatásához nyomja le az F5 billentyűt. Nincs szükség előfizetésre vagy bejelentkezésre.

+ A [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) egy régebbi minta, amely a C# és a Java kódot is tartalmazza. Emellett bemutatja a javaslatok kialakítását, a javasolt lekérdezéseket, az automatikus kiegészítést és a sokoldalú navigációt. Ez a mintakód az üzemeltetett [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) -mintaadatok használatával működik. 

## <a name="next-steps"></a>Következő lépések

A kérelmek létrehozásával kapcsolatban a következő cikkből tájékozódhat.

> [!div class="nextstepaction"]
> [Automatikus kiegészítés és javaslatok hozzáadása az ügyfél kódjához](search-autocomplete-tutorial.md)