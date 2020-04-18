---
title: Javaslattevő létrehozása
titleSuffix: Azure Cognitive Search
description: Engedélyezze a típus-ahead lekérdezési műveleteket az Azure Cognitive Search-ben javaslattevők létrehozásával és az automatikus kiegészítést vagy automatikusan javasolt lekérdezési kifejezéseket meghívni megkötő kérelmek kialakításával.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 1e2a837acef976b6b872c2d4002ee49d662ad594
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641321"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>Javaslattevő létrehozása az automatikus kiegészítés és a javasolt eredmények lekérdezésben való engedélyezéséhez

Az Azure Cognitive Search,"keresés-as-you-type" engedélyezve van egy **javaslatajánló** konstrukció hozzá egy [keresési index.](search-what-is-an-index.md) A javaslatajánló két felhasználói élményt támogat: *az automatikus kiegészítést*, amely kiegészíti a kifejezést vagy kifejezést, és *javaslatokat,* amelyek az egyező dokumentumok rövid listáját adják vissza.  

A következő képernyőkép [az Első alkalmazás létrehozása C# nyelven](tutorial-csharp-type-ahead-and-suggestions.md) című részéből mindkettőt szemlélteti. Az automatikus kiegészítés egy lehetséges kifejezést vár, a "tw" "in" kifejezéssel fejezve be. A javaslatok a mini keresési eredmények, ahol egy mező, például a szálloda neve, az indexből származó megfelelő szállodai keresési dokumentumot jelöl. Javaslatok esetén bármilyen mezőt megadhat, amely leíró információkat tartalmaz.

![Az automatikus kiegészítés és a javasolt lekérdezések vizuális összehasonlítása](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Az automatikus kiegészítés és a javasolt lekérdezések vizuális összehasonlítása")

Ezeket a funkciókat külön-külön vagy együttesen is használhatja. Ezek a viselkedések az Azure Cognitive Search, egy index és lekérdezési összetevő. 

+ Az indexben adjon hozzá egy javaslatajánlóegy index. Használhatja a portált, a [REST API-t](https://docs.microsoft.com/rest/api/searchservice/create-index)vagy a [.NET SDK-t.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet) A cikk többi része egy javaslatajánló létrehozására összpontosít.

+ A lekérdezési kérelemben hívja meg az [alább felsorolt API-k](#how-to-use-a-suggester)egyikét.

A "you-as-you-type" támogatás mezőnként engedélyezve van a karakterláncmezőkhöz. Mindkét typeahead viselkedést megvalósíthatja ugyanabban a keresési megoldásban, ha a képernyőképen jelzetthez hasonló élményt szeretne. Mindkét kérelem az adott index *dokumentumok* gyűjteményét célozza meg, és a válaszok at a felhasználó legalább három karakterbeviteli karakterláncának megadása után adja vissza a rendszer.

## <a name="what-is-a-suggester"></a>Mi az a szuggesztáló?

A javaslatajánló olyan adatstruktúra, amely támogatja a beírási viselkedést azáltal, hogy a részleges lekérdezések egyeztetéséhez előtagokat tárol. A tokenizált kifejezésekhez hasonlóan az előtagok fordított indexekben tárolódnak, egyet-egyet a javaslatajánló mezők gyűjteményében megadott mezőkhöz.

Előtagok létrehozásakor a javaslatajánló saját elemzési lánccal rendelkezik, hasonlóan a teljes szöveges kereséshez használthoz. A teljes szöveges keresés elemzésével ellentétben azonban a javaslatajánló csak olyan mezőkön működhet, amelyek a szabványos Lucene analizátort (alapértelmezett) vagy [egy nyelvi analizátort](index-add-language-analyzers.md)használják. Az [egyéni analizátorokat](index-add-custom-analyzers.md) vagy [előre definiált elemzőket](index-add-custom-analyzers.md#predefined-analyzers-reference) használó mezők (a normál Lucene kivételével) kifejezetten nem engedélyezettek a rossz eredmények megelőzése érdekében.

> [!NOTE]
> Ha meg kell kerülnie az analizátor-megkötést, használjon két külön mezőt ugyanahhoz a tartalomhoz. Ez lehetővé teszi, hogy az egyik mezőben legyen egy javaslatajánló, míg a másik egyéni analizátor konfigurációval állítható be.

## <a name="define-a-suggester"></a>Javaslatajánló definiálása

Javaslatajánló létrehozásához vegyen fel egyet egy [indexsémába,](https://docs.microsoft.com/rest/api/searchservice/create-index) és [állítsa be az egyes tulajdonokat.](#property-reference) Az indexben lehet egy javaslatajánló (pontosabban egy javaslatajánló a javaslatajánló gyűjteményben). A javaslatajánló létrehozásának legjobb ideje az, ha meghatározza azt a mezőt is, amely használni fogja.

### <a name="choose-fields"></a>Mezők kiválasztása

Bár a javaslattevő nek több tulajdonsága van, elsősorban olyan mezők gyűjteménye, amelyekhez lehetővé teszi a beírási keresési élményt. Különösen a javaslatok esetén válassza ki azokat a mezőket, amelyek a legjobban egyetlen eredményt képviselnek. A több egyezés között megkülönböztető nevek, címek vagy más egyedi mezők működnek a legjobban. Ha a mezők ismétlődő értékekből állnak, a javaslatok azonos eredményekből állnak, és a felhasználó nem fogja tudni, hogy melyikre kattintson.

Győződjön meg arról, hogy minden mező olyan elemzőt használ, amely lexikális elemzést végez az indexelés során. Használhatja az alapértelmezett szabványos Lucene`"analyzer": null`analizátort ( ) `"analyzer": "en.Microsoft"`vagy egy [nyelvi analizátort](index-add-language-analyzers.md) (például ). 

Az analizátor kiválasztása határozza meg a mezők tokenized és ezt követően előre rögzített. Például egy kötőjellel ellátott karakterlánc, mint a "környezet-érzékeny", egy nyelvi analizátor használatával a következő jogkivonat-kombinációk: "context", "sensitive", "context-sensitive". Ha a szokásos Lucene analizátort használtad volna, az elválasztott karakterlánc nem létezne.

> [!TIP]
> Fontolja meg a [Szöveg elemzése API-t](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) a kifejezések tokenizedés későbbi előtaghoz való betekintéséhez. Miután létrehoz egy indexet, kipróbálhatja a különböző elemzők egy karakterláncot, hogy megtekinthesse a jogkivonatokat bocsát ki.

### <a name="when-to-create-a-suggester"></a>Mikor kell létrehozni egy ajánló

A javaslatajánló létrehozásának legjobb ideje, ha magát a meződefiníciót is létrehozza.

Ha megpróbál létrehozni egy javaslatajánló a már meglévő mezők, az API lehetővé teszi azt. Az előtagok indexelés közben jönnek létre, amikor a részleges kifejezések két vagy több karakterkombinációban a teljes kifejezések mellett tokenizálva vannak. Mivel a meglévő mezők már tokenizáltak, újra kell építenie az indexet, ha hozzá szeretné adni őket egy javaslatajánlóhoz. További információ: [Az Azure Cognitive Search-index újraépítése.](search-howto-reindex.md)

## <a name="create-using-rest"></a>Létrehozás rest használatával

A REST API-ban adjon hozzá javaslatjavasolókat [az Index létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index) vagy az Index [frissítése](https://docs.microsoft.com/rest/api/searchservice/update-index)segítségével. 

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

## <a name="create-using-net"></a>Létrehozás a .NET használatával

A C#-ban definiáljon egy [Javaslatajánló objektumot](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters`gyűjtemény, de csak egy elemet vehet fel. 

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

## <a name="property-reference"></a>Tulajdonság hivatkozása

|Tulajdonság      |Leírás      |
|--------------|-----------------|
|`name`        |A javaslatajánló neve.|
|`searchMode`  |A jelölt kifejezések keresésére használt stratégia. Jelenleg csak támogatott `analyzingInfixMatching`mód , amely jelenleg egyezik a kifejezés elején.|
|`sourceFields`|Egy vagy több mező listája, amelyek a javaslatok tartalmának forrása. A mezőknek `Edm.String` típusúnak kell lenniük, és. `Collection(Edm.String)` Ha egy elemző van megadva a mezőben, akkor [a listából](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) elnevezett analizátornak kell lennie (nem egyéni elemzőnek).<p/> Ajánlott eljárásként csak azokat a mezőket adja meg, amelyek a várt és megfelelő válaszra alkalmasak, legyen szó egy keresősávban vagy egy legördülő listáról.<p/>A szálloda neve jó jelölt, mert precizitású. A részletes mezők, például a leírások és a megjegyzések túl sűrűek. Hasonlóképpen az ismétlődő mezők, például a kategóriák és a címkék kevésbé hatékonyak. A példákban egyébként is felvesszük a "kategória" kifejezést annak bizonyítására, hogy több mezőt is felvehet. |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>Javaslatajánló használata

A javaslatajánló a lekérdezésben használatos. A javaslatajánló létrehozása után hívja fel az alábbi API-k egyikét a beírási kereséshez:

+ [Javaslatok REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [Rest automatikus kiegészítésapi](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync metódus](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Automatikus kiegészítésHttpMessagesAsync metódus](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

A keresési alkalmazásokban az ügyfélkódnak a [jQuery ui automatikus kiegészítéshez](https://jqueryui.com/autocomplete/) hasonló tárt kell használnia a részleges lekérdezés összegyűjtéséhez és az egyezés biztosításához. A feladatról további információt az [Automatikus kiegészítés vagy a javasolt eredmények hozzáadása az ügyfélkódhoz című](search-autocomplete-tutorial.md)témakörben talál.

Az API-használat ot az automatikus kiegészítésrest API következő hívása szemlélteti. Ebből a példából két elvihető rész áll. Először is, mint minden lekérdezésnél, a művelet az index dokumentumok gyűjteményével szemben van, és a lekérdezés tartalmaz egy **keresési** paramétert, amely ebben az esetben biztosítja a részleges lekérdezést. Másodszor, hozzá kell **adnia a javaslatajánlónév** a kérelmet. Ha egy javaslatajánló nincs definiálva az indexben, az automatikus kiegészítésre vagy a javaslatokra irányuló hívás sikertelen lesz.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2019-05-06
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>Mintakód

+ [Hozza létre az első alkalmazást a C# (3. lecke – Keresés a te típusként)](tutorial-csharp-type-ahead-and-suggestions.md) minta bemutatja a javaslatalit, a javasolt lekérdezéseket, az automatikus kiegészítést és a felületes navigációt. Ez a kódminta fut egy sandbox Azure Cognitive Search szolgáltatás, és egy előre betöltött Hotel index, így mindössze annyit kell tennie, hogy nyomja meg az F5 az alkalmazás futtatásához. Nincs szükség előfizetésre vagy bejelentkezésre.

+ [A DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) egy régebbi minta, amely C# és Java kódot is tartalmaz. Azt is bemutatja a javaslatépítő konstrukció, javasolt lekérdezések, automatikus kiegészítés, és a csiszolt navigáció. Ez a kódminta a üzemeltetett [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) mintaadatokat használja. 

## <a name="next-steps"></a>További lépések

A következő példát javasoljuk a kérelmek megfogalmazásának megtekintéséhez.

> [!div class="nextstepaction"]
> [Automatikus kiegészítés és javaslatok hozzáadása az ügyfélkódhoz](search-autocomplete-tutorial.md) 
