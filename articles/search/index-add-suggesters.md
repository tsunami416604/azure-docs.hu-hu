---
title: Javaslattevő létrehozása
titleSuffix: Azure Cognitive Search
description: Engedélyezze a típus-ahead lekérdezési műveleteket az Azure Cognitive Search-ben javaslattevők létrehozásával és az automatikus kiegészítést vagy automatikusan javasolt lekérdezési kifejezéseket meghívni megkötő kérelmek kialakításával.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: a6c4051a5b3d557f9ac2927a62492425e7636c0d
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113474"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggestions-in-azure-cognitive-search"></a>Javaslattevő létrehozása az automatikus kiegészítés és javaslatok engedélyezéséhez az Azure Cognitive Search szolgáltatásban

Az Azure Cognitive Search,"keresés-as-you-type" vagy typeahead funkció alapja egy **javaslatajánló** konstrukció, amely hozzáad egy [keresési index.](search-what-is-an-index.md) A javaslatajánló két, a beírt szövegként idotartamú változatot támogat: *az automatikus kiegészítést,* amely kiegészíti a beírt kifejezést vagy kifejezést, valamint *olyan javaslatokat,* amelyek az egyező dokumentumok rövid listáját adják vissza.  

A következő képernyőkép, az [Első alkalmazás létrehozása c# mintában,](tutorial-csharp-type-ahead-and-suggestions.md) mindkét élményt szemlélteti. Az automatikus kiegészítés előre jelzi, hogy a felhasználó mit írhat be, és a "tw" szót a leendő keresési kifejezésként az "in" szöveggel fejezi be. A javaslatok a tényleges keresési eredmények, amelyek mindegyike egy megfelelő dokumentumot jelöl. Javaslatok esetén a dokumentum bármely olyan részét felis teheti, amely a legjobban leírja az eredményt. Ebben a példában a javaslatokat a szálloda név mezője képviseli.

![Az automatikus kiegészítés és a javasolt lekérdezések vizuális összehasonlítása](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Az automatikus kiegészítés és a javasolt lekérdezések vizuális összehasonlítása")

Ezek a viselkedések az Azure Cognitive Search, egy index és lekérdezési összetevő. 

+ Az indexben adjon hozzá egy javaslatajánlóegy index. Használhatja a portált, a [REST API-t](https://docs.microsoft.com/rest/api/searchservice/create-index)vagy a [.NET SDK-t.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet) A cikk többi része egy javaslatajánló létrehozására összpontosít.

+ A lekérdezési kérelemben hívja meg az [alább felsorolt API-k](#how-to-use-a-suggester)egyikét.

A keresés beírása mezőnként engedélyezve van. Mindkét typeahead viselkedést megvalósíthatja ugyanabban a keresési megoldásban, ha a képernyőképen jelzetthez hasonló élményt szeretne. Mindkét kérelem az adott index *dokumentumok* gyűjteményét célozza meg, és a válaszok at a felhasználó legalább három karakterbeviteli karakterláncának megadása után adja vissza a rendszer.

## <a name="what-is-a-suggester"></a>Mi az a szuggesztáló?

A javaslatajánló olyan adatstruktúra, amely támogatja a beírási viselkedést azáltal, hogy a részleges lekérdezések egyeztetéséhez előtagokat tárol. A tokenizált kifejezésekhez hasonlóan az előtagok fordított indexekben tárolódnak, egyet-egyet a javaslatajánló mezők gyűjteményében megadott mezőkhöz.

Előtagok létrehozásakor a javaslatajánló saját elemzési lánccal rendelkezik, hasonlóan a teljes szöveges kereséshez használthoz. A teljes szöveges keresés elemzésével ellentétben azonban a javaslatajánló csak előre definiált elemzőket (szabványos Lucene, [nyelvi elemzők](index-add-language-analyzers.md)vagy más elemzők az [előre definiált analizátorlistában](index-add-custom-analyzers.md#predefined-analyzers-reference)) használhat. [Egyéni elemzők](index-add-custom-analyzers.md) és konfigurációk kifejezetten tilos elkerülni a véletlenszerű konfigurációk, amelyek rossz eredményeket eredményez.

> [!NOTE]
> Ha meg kell kerülnie az analizátor-megkötést, használjon két külön mezőt ugyanahhoz a tartalomhoz. Ez lehetővé teszi, hogy az egyik mezőben legyen egy javaslatajánló, míg a másik egyéni analizátor konfigurációval állítható be.

## <a name="create-a-suggester"></a>Javaslattevő létrehozása

Bár a javaslattevő nek több tulajdonsága van, elsősorban olyan mezők gyűjteménye, amelyekhez typeahead élményt engedélyez. Előfordulhat például, hogy egy utazási alkalmazás engedélyezni szeretné a typeahead keresést az úti célokon, városokon és látnivalókon. Így mindhárom mező a mezők gyűjteményébe kerülne.

Javaslatajánló létrehozásához vegyen fel egyet egy indexsémához. Egy indexben egy javaslatajánló lehet (pontosabban a javaslatajánlógyűjteményegyik).

### <a name="when-to-create-a-suggester"></a>Mikor kell létrehozni egy ajánló

A javaslatajánló létrehozásának legjobb ideje, ha magát a meződefiníciót is létrehozza.

Ha megpróbál létrehozni egy javaslatajánló a már meglévő mezők, az API lehetővé teszi azt. A Typeahead szöveg az indexelés során jön létre, amikor két vagy több karakterkombináció részleges kifejezései a teljes kifejezések mellett tokenizálva vannak. Mivel a meglévő mezők már tokenizáltak, újra kell építenie az indexet, ha hozzá szeretné adni őket egy javaslatajánlóhoz. Az újraindexelésről az [Azure Cognitive Search indexének újraépítése](search-howto-reindex.md)című témakörben olvashat bővebben.

### <a name="create-using-the-rest-api"></a>Létrehozás a REST API használatával

A REST API-ban adjon hozzá javaslatjavasolókat [az Index létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index) vagy az Index [frissítése](https://docs.microsoft.com/rest/api/searchservice/update-index)segítségével. 

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

A C#-ban definiáljon egy [Javaslatajánló objektumot](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters`gyűjtemény, de csak egy elemet vehet fel. 

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

### <a name="property-reference"></a>Tulajdonság hivatkozása

|Tulajdonság      |Leírás      |
|--------------|-----------------|
|`name`        |A javaslatajánló neve.|
|`searchMode`  |A jelölt kifejezések keresésére használt stratégia. Jelenleg csak a `analyzingInfixMatching`támogatott mód az , amely a mondatok elején vagy közepén rugalmasan egyezteti a kifejezéseket.|
|`sourceFields`|Egy vagy több mező listája, amelyek a javaslatok tartalmának forrása. A mezőknek `Edm.String` típusúnak kell lenniük, és. `Collection(Edm.String)` Ha egy elemző van megadva a mezőben, akkor [a listából](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) elnevezett analizátornak kell lennie (nem egyéni elemzőnek).<p/>Ajánlott eljárásként csak azokat a mezőket adja meg, amelyek a várt és megfelelő válaszra alkalmasak, legyen szó egy keresősávban vagy egy legördülő listáról.<p/>A szálloda neve jó jelölt, mert precizitású. A részletes mezők, például a leírások és a megjegyzések túl sűrűek. Hasonlóképpen az ismétlődő mezők, például a kategóriák és a címkék kevésbé hatékonyak. A példákban egyébként is felvesszük a "kategória" kifejezést annak bizonyítására, hogy több mezőt is felvehet. |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester-in-a-query"></a>Javaslatajánló használata lekérdezésben

A javaslatajánló létrehozása után hívja meg a megfelelő API-t a lekérdezési logikában a szolgáltatás meghívásához. 

+ [Javaslatok REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [Rest automatikus kiegészítésapi](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync metódus](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Automatikus kiegészítésHttpMessagesAsync metódus](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

Az API-használat ot az automatikus kiegészítésrest API következő hívása szemlélteti. Ebből a példából két elvihető rész áll. Először is, mint minden lekérdezés, a művelet egy index dokumentumgyűjteményével ellentétes. Másodszor, hozzáadhat lekérdezési paramétereket. Bár számos lekérdezési paraméter közös mindkét API-ban, a lista mindegyiknél eltérő.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs/autocomplete?[query parameters]  
api-key: [admin or query key]
```

Ha egy javaslatajánló nincs definiálva az indexben, az automatikus kiegészítésre vagy a javaslatokra irányuló hívás sikertelen lesz.

## <a name="sample-code"></a>Mintakód

+ [Az első alkalmazás létrehozása C#](tutorial-csharp-type-ahead-and-suggestions.md) mintában bemutatja a javaslatajánló felépítését, a javasolt lekérdezéseket, az automatikus kiegészítést és a felületes navigációt. Ez a kódminta fut egy sandbox Azure Cognitive Search szolgáltatás, és egy előre betöltött Hotel index, így mindössze annyit kell tennie, hogy nyomja meg az F5 az alkalmazás futtatásához. Nincs szükség előfizetésre vagy bejelentkezésre.

+ [A DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) egy régebbi minta, amely C# és Java kódot is tartalmaz. Azt is bemutatja a javaslatépítő konstrukció, javasolt lekérdezések, automatikus kiegészítés, és a csiszolt navigáció. Ez a kódminta a üzemeltetett [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) mintaadatokat használja. 


## <a name="next-steps"></a>További lépések

A következő példát javasoljuk a kérelmek megfogalmazásának megtekintéséhez.

> [!div class="nextstepaction"]
> [Javaslatok és automatikus kiegészítési példák](search-autocomplete-tutorial.md) 
