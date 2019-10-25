---
title: Typeahead-lekérdezések hozzáadása indexhez
titleSuffix: Azure Cognitive Search
description: Adja meg az Azure Cognitive Search típus-előkészítő lekérdezési műveleteit a javaslatok létrehozásával, valamint az automatikus kiegészítés vagy az automatikus javaslattal rendelkező lekérdezési feltételeket meghívó kérelmek összeállításával.
manager: nitinme
author: Brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: a312068d5c8c574e7b069263cf37e3b855810e4b
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790103"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-cognitive-search"></a>Javaslatok hozzáadása az Azure-beli typeahead indexéhez Cognitive Search

Az Azure Cognitive Search "Search-as-type" vagy typeahead funkció egy [keresési indexhez](search-what-is-an-index.md)hozzáadott **javaslat** -összeállításon alapul. Ez a lista egy vagy több olyan mezőt tartalmaz, amelyhez engedélyezni szeretné a typeahead.

A javaslat két typeahead-változatot támogat: az *automatikus kiegészítést*, amely befejezi a beírt kifejezést vagy kifejezést, és *javaslatokat* ad a megfelelő dokumentumok rövid listájára.  

A következő képernyőkép az [első alkalmazás C# létrehozása](tutorial-csharp-type-ahead-and-suggestions.md) a mintában című rész a typeahead mutatja be. Az automatikus kiegészítéssel megjósolható, hogy a felhasználó milyen begépelheti a keresőmezőbe. A tényleges bemenet a "tw", amely az automatikus kiegészítés "in"-ben fejeződik be, a leendő keresési kifejezésként pedig "Twin" megoldásként. A javaslatok a legördülő listában láthatók. A javaslatok esetében egy olyan dokumentum bármely részét felhasználhatja, amely a legjobban leírja az eredményt. Ebben a példában a javaslatok a szállodai nevek. 

![Az automatikus kiegészítés és a javasolt lekérdezések vizuális összehasonlítása](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Az automatikus kiegészítés és a javasolt lekérdezések vizuális összehasonlítása")

A viselkedés Azure Cognitive Searchban való megvalósításához index és lekérdezési összetevő szükséges. 

+ Az indexben adjon hozzá egy mutatót egy indexhez. Használhatja a portált, a [REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)vagy a [.net SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)-t. A cikk további részében a javaslatok létrehozására összpontosítunk. 

+ A lekérdezési kérelemben hívja meg az [alább felsorolt API](#how-to-use-a-suggester)-k egyikét.

A keresés a típus szerinti támogatás engedélyezve van a mező alapján. Mindkét typeahead-viselkedést ugyanazon keresési megoldáson belül implementálhatja, ha a képernyőképen láthatóhoz hasonló élményre van szüksége. Mindkét kérelem célja az adott index és válaszok *dokumentumainak* gyűjteménye, miután egy felhasználó legalább három karakterből álló bemeneti karakterláncot adott meg.

## <a name="create-a-suggester"></a>Javaslattevő létrehozása

Bár a javaslatok több tulajdonsággal rendelkeznek, elsősorban olyan mezők gyűjteménye, amelyekhez typeahead-élményt engedélyez. Előfordulhat például, hogy egy utazási alkalmazás lehetővé szeretné tenni a typeahead keresését a célhelyeken, a városokban és a látnivalókban. Így mindhárom mező a mezők gyűjteményében fog megjelenni.

A javaslatok létrehozásához adjon hozzá egyet egy index sémához. Egy indexben egy mutató is szerepelhet (konkrétan egy javaslat a javaslatok gyűjteményében). 

### <a name="when-to-create-a-suggester"></a>Mikor hozzon létre egy javaslatot

A javaslatok létrehozásához a legjobb idő az, amikor saját maga is létrehozza a mező definícióját.

Ha már meglévő mezők használatával próbál létrehozni egy javaslatot, az API letiltja azt. A typeahead szövege az indexelés során jön létre, ha két vagy több karakteres kombinációban lévő részleges kifejezéseket a rendszer a teljes kifejezéssel együtt tokenben tárolja. Mivel a meglévő mezők már jogkivonattal rendelkeznek, újra kell építenie az indexet, ha hozzá kívánja adni őket egy javaslathoz. További információ az újraindexelésről: [Azure Cognitive Search index újraépítése](search-howto-reindex.md).

### <a name="create-using-the-rest-api"></a>Létrehozás a REST API használatával

A REST API adjon hozzá javaslatokat a [create index](https://docs.microsoft.com/rest/api/searchservice/create-index) vagy az [Update index](https://docs.microsoft.com/rest/api/searchservice/update-index)használatával. 

  ```json
  {
    "name": "hotels",
    "fields": [
      . . .
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["hotelName", "category"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```


### <a name="create-using-the-net-sdk"></a>Létrehozás a .NET SDK használatával

A C#alkalmazásban adjon meg egy [javaslat objektumot](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters` egy gyűjtemény, de csak egyetlen elemmel rendelkezhet. 

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

### <a name="property-reference"></a>Tulajdonság leírása

|Tulajdonság      |Leírás      |
|--------------|-----------------|
|`name`        |A javaslat neve.|
|`searchMode`  |A jelölt kifejezésekre való kereséshez használt stratégia. Az egyetlen jelenleg támogatott mód `analyzingInfixMatching`, amely a mondatok elején vagy végén a mondatok rugalmas megfeleltetését végzi.|
|`sourceFields`|Egy vagy több olyan mező listája, amely a javaslatok forrását képezi. A mezőknek `Edm.String` és `Collection(Edm.String)`típusúnak kell lenniük. Ha a mezőben egy analizátor van megadva, akkor a [listán szereplő](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) elemzőnek (nem egyéni elemzőnek) kell lennie.<p/>Ajánlott eljárásként csak azokat a mezőket kell megadnia, amelyek a várt és a megfelelő választ adják meg, legyen szó egy keresési sávon vagy egy legördülő listában szereplő befejezett sztringről.<p/>A Hotel neve jó jelölt, mert pontossággal rendelkezik. A részletes mezők, például a leírások és a megjegyzések túl sűrűk. Ehhez hasonlóan az ismétlődő mezők, például a kategóriák és a címkék kevésbé hatékonyak. A példákban bemutatjuk a "kategória" kifejezést is, amely azt mutatja be, hogy több mezőt is tartalmazhat. |

### <a name="analyzer-restrictions-for-sourcefields-in-a-suggester"></a>Elemzői korlátozások a sourceFields egy javaslatban

Az Azure Cognitive Search elemzi a mező tartalmát, hogy engedélyezze az egyes feltételek lekérdezését. A javaslatok végrehajtásához az előtagokat a feltételek teljesítése mellett kell indexelni, és további elemzésre van szükség a forrás mezőin. Az egyéni analizátor-konfigurációk kombinálják a különböző tokenizers és szűrőket, gyakran olyan módokon, amelyek a javaslatok számára szükséges előtagokat is lehetetlenné tennék. Emiatt az Azure Cognitive Search meggátolja, hogy a rendszer az egyéni elemző mezőket a javaslatokba foglalja.

> [!NOTE] 
>  Ha a fenti korlátozás körül kell dolgoznunk, két külön mezőt kell használnia ugyanahhoz a tartalomhoz. Ez lehetővé teszi, hogy az egyik mező legyen egy javaslat, míg a másik egyéni Analyzer-konfigurációval is beállítható.

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester-in-a-query"></a>Javaslat használata lekérdezésben

A javaslat létrehozása után hívja meg a megfelelő API-t a lekérdezési logikában a funkció meghívásához. 

+ [Javaslatok REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [Automatikus kiegészítés REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync metódus](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [AutocompleteWithHttpMessagesAsync metódus](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

Az API-használat a következő, az automatikus kiegészítési REST APIra irányuló hívásban látható. Ebből a példából két elvihetőség van. Először is, ahogy az összes lekérdezés esetében, a művelet egy index dokumentumainak gyűjteménye. Másodszor, lekérdezési paramétereket adhat hozzá. Habár számos lekérdezési paraméter közös mindkét API esetében, a lista egymástól eltérő lehet.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs/autocomplete?[query parameters]  
api-key: [admin or query key]
```

Ha egy javaslat nincs definiálva az indexben, az automatikus kiegészítés vagy a javaslatok sikertelenek lesznek.

## <a name="sample-code"></a>Mintakód

+ [Hozza létre az első alkalmazását C# a](tutorial-csharp-type-ahead-and-suggestions.md) mintában, amely bemutatja a javaslatok kialakítását, a javasolt lekérdezéseket, az automatikus kiegészítést és a sokoldalú navigációt. Ez a mintakód egy sandbox Azure Cognitive Search szolgáltatásban fut, és egy előre betöltött Hotel-indexet használ, így mindössze annyit kell tennie, hogy az alkalmazás futtatásához nyomja le az F5 billentyűt. Nincs szükség előfizetésre vagy bejelentkezésre.

+ A [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) egy régebbi minta C# , amely a és a Java-kódot is tartalmazza. Emellett bemutatja a javaslatok kialakítását, a javasolt lekérdezéseket, az automatikus kiegészítést és a sokoldalú navigációt. Ez a mintakód az üzemeltetett [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) -mintaadatok használatával működik. 


## <a name="next-steps"></a>Következő lépések

Azt javasoljuk, hogy tekintse át a kérelmek összeállításának módját a következő példán.

> [!div class="nextstepaction"]
> [Javaslatok és automatikus kiegészítési példák](search-autocomplete-tutorial.md) 
