---
title: Példa a legördülő listából feltételek hozzáadása egy keresőmezőt tartalmazó – Azure Search automatikus kiegészítés
description: Javasolt lekérdezések bemenetek hozzáadása javaslattevők létrehozásával, és a kérelmek, amelyek aktiválják az Azure Search javasolt lekérdezések kidolgozásában.
manager: cgronlun
author: heidisteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: heidist
ms.openlocfilehash: 1db085d61c60d303aaff5c3b0d16998805fcda90
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58373075"
---
# <a name="example-add-autosuggest-for-dropdown-query-selections"></a>Példa: Adja hozzá a legördülő listából lekérdezés beállításokhoz automatikus kiegészítés

Keresési kifejezés bemenetek egy legördülő lista javasolt lekérdezési kifejezések tartalmazhatnak. Megtudhatta, hogy ez a funkció a kereskedelmi keresőkben, és az Azure Search-egy hasonló megoldást valósíthat meg egy [javaslattevő szerkezet](index-add-suggesters.md) és a egy javaslatok műveletet a lekérdezési kérést. Ebben a cikkben példák bemutatják az automatikus kiegészítési lekérdezést, a javaslatok, amelyek már definiált használatával kialakításában. 

## <a name="rest-api"></a>REST API

Használhat [Postman](search-fiddler.md) vagy [PowerShell](search-create-index-rest-api.md) és a [REST API-val](https://docs.microsoft.com/rest/api/searchservice/) próbálhatja ki ebben a példában, de az eredményeket fogja visszaadni JSON-dokumentumok formájában. Egy realisztikus, és vizuális élményt található használatával a [automatikus kiegészítés használt mintakódot](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

### <a name="1---index-with-a-suggester-construct"></a>1 – indexelés egy javaslattevő szerkezet

Automatikus kiegészítés kezdődik a [javaslattevő szerkezet](index-add-suggesters.md) hozzá indexhez, legördülő lista értékeit, hogy tevődik össze. Adja meg a következő mintát követik, javasolt lekérdezések fog szintaxisok a Mezőmeghatározása és a kategória mező értékeit használva.

Ha a minimális minta adatkészletekben található a rövid útmutatók, Szálloda nevek tartalmazzák a "Divatos marad" és "Roach amelyben", és a kategóriák "Engedélyezhető" és "Budget".

Ha már rendelkezik a "Hotels" index, engedje el és hozza létre újra a következő séma használatával. Ebben a sémában a javaslattevő hozzáadja. Ne feledje, valamint az adatok újbóli betöltéséhez.

```json
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ],
  "suggesters": [
    {
      "name": "sg",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["hotelName", "category"]
    }
  ]
}

```

### <a name="2---query-with-suggestions-operator"></a>2 – lekérdezési javaslatok operátor szerinti szűrése

A javaslattevő használja, és meghívja automatikus kiegészítés, el kell küldenie egy [javaslatok API](https://docs.microsoft.com/rest/api/searchservice/suggestions) GET vagy POST kérelem. Az ilyen kérést a keresési szolgáltatás megvizsgálja lehetséges egyezések után fogadja az első három karaktereket. 

A kérelem fejlécét, állítsa be **api-kulcs** egy rendszergazda vagy a lekérdezési kulcsot, és **Content-Type** application/json értékre. 

```http
GET https://mydemo.search.windows.net/indexes/hotels/docs/suggest?search=fan&&suggesterName=sg
```

Az összes mező szerepel a javaslattevő (Mezőmeghatározása és kategóriában), kérelem megkeresi a következő válasz visszaküldése:

```
{
    "@odata.context": "https://mydemo.search.windows.net/indexes('hotels')/$metadata#docs(*)",
    "value": [
        {
            "@search.text": "Fancy Stay",
            "hotelId": "1"
        }
    ]
}
```

Hogy a várt eredményt kapja, az Ügyfélkód akkor jelennek meg az eredményeket, egy keresősáv alatt a legördülő listából.

## <a name="net-sdk-c"></a>.NET SDK (C#)

### <a name="1---index-with-a-suggester-construct"></a>1 – indexelés egy javaslattevő szerkezet

A .NET SDK használatával egy [javaslattevő osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). Javaslattevő olyan gyűjteménye, de csak egy elemet is eltarthat.

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelId", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

### <a name="2---query-with-suggest-method"></a>2 – lekérdezés javasolt módszer

A [DocumentsOperationsExtensions.Suggest metódus](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet) javasolt lekérdezési karakterláncot ad vissza.

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult suggestResult = _indexClient.Documents.Suggest(term, "sg",sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

## <a name="see-also"></a>Lásd még

+ [Ismerkedés a REST API-t Postman használatával](search-fiddler.md)
+ [Példa: Az automatikus kiegészítés](search-autocomplete-tutorial.md)
+ [Adja hozzá a javaslattevők egy indexbe](index-add-suggesters.md)
+ [Adjon hozzá egy Javaslattevők osztályt a .NET használatával](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)
+ [Hívás javaslatok GET vagy POST (REST API) használatával](https://docs.microsoft.com/rest/api/searchservice/suggestions)
+ [Javaslatok SuggestWithHttpMessagesAsync (.NET) használatával hívja](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet-preview) vagy 