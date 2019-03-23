---
title: Typeahead lekérdezések Azure Search - index hozzáadása
description: Engedélyezze a gépelés közbeni lekérdezési műveleteket az Azure Search javaslattevők létrehozásával, és meghívása az automatikus kiegészítés vagy a lekérdezési kifejezések autosuggested kérelmek kidolgozásában.
ms.date: 03/22/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: 877294e80d655ab75be78a5aa57854a03a5f267a
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370652"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Az Azure Search typeahead indexet javaslattevők hozzáadása

A **javaslattevő** egy szerkezet az egy [Azure Search-index](search-what-is-an-index.md) , amely támogatja a "Keresés –--beíráskor" felületet. Tartalmaz, amelynek szeretné engedélyezni, typeahead lekérdezés bemenetei között a mezők listája. Typeahead két változata van, melyek: [ *automatikus kiegészítés* ](search-autocomplete-tutorial.md) elkezdi beírni a, kifejezéssel befejezése [ *autosuggest* ](search-autosuggest-example.md) nyújt rövid szavakat vagy kifejezéseket bemeneti lekérdezésként választható listája. A kereskedelmi keresőkben előtt viselkedést kétségtelenül látott.

![Az automatikus kiegészítés Visual összehasonlítása és automatikus kiegészítés](./media/index-add-suggesters/visual-comparison-suggest-complete.png "Visual összehasonlítása az automatikus kiegészítés és automatikus kiegészítés")

Ezen viselkedés megvalósítása az Azure Search, egy index és lekérdezés-összetevő van. 

+ Adja hozzá a javaslattevő az indexben. A portálon, a javaslattevő létrehozása REST API vagy .NET SDK-t is használhatja. 

+ Lekérdezés adja meg az automatikus kiegészítés vagy az automatikus kiegészítés műveletet. 

> [!Important]
> Automatikus kiegészítési funkciója jelenleg előzetes verziójú, REST API-k előzetes verzióban érhető el, ezért a .NET SDK-t, és az éles környezetben nem támogatott. 

Typeahead támogatása engedélyezve van, mező alapon. Ha azt szeretné, felületet nyújt a képernyőképen jelzett hasonló mindkét typeahead viselkedések belül ugyanazt a keresési megoldást valósíthat meg. Mindkét kérelmek cél a *dokumentumok* után a felhasználó legalább egy három bemeneti sztring biztosított meghatározott index és a válaszok gyűjteményét adja vissza.

## <a name="create-a-suggester"></a>Hozzon létre egy javaslattevő

Habár a javaslattevő számos tulajdonságát, elsősorban engedélyezi egy typeahead élmény mezők gyűjteménye. Például egy utazási alkalmazás érdemes a destinations, város és létesítmények typeahead keresésének lehetővé tételéhez. Ennek megfelelően minden három mezőt a mezők gyűjteménye a helyezkedik el.

Hozzon létre egy javaslattevő, adjon hozzá egy-egy indexsémát. Használhat egy javaslattevő index (pontosabban a javaslattevők gyűjtemény egy javaslattevő). 

A REST API-ban hozzáadhat keresztül javaslattevők [a Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) vagy [Index frissítése](https://docs.microsoft.com/rest/api/searchservice/update-index). 

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

Figyelje meg, hogy javaslattevők kapcsolatos legfontosabb az, hogy nincs-e (kérelem neve szerint javaslattevők hivatkozott) nevet, egy searchMode (jelenleg csak egyetlen, "analyzingInfixMatching") és a mezőlistában, amelynek typeahead engedélyezve van. 

A javaslattevő létrehozása után adja hozzá a [javaslatok API](https://docs.microsoft.com/rest/api/searchservice/suggestions) a lekérdezés logikai meghívni a szolgáltatást.

### <a name="property-reference"></a>Tulajdonsághivatkozás

A javaslattevő meghatározó tulajdonságok a következők:

|Tulajdonság      |Leírás      |
|--------------|-----------------|
|`name`        |A javaslattevő neve. A javaslattevő nevét használja, hívásakor a [javaslatok REST API-val](https://docs.microsoft.com/rest/api/searchservice/suggestions) vagy [automatikus kiegészítés REST API (előzetes verzió)](https://docs.microsoft.com/rest/api/searchservice/autocomplete).|
|`searchMode`  |A jelölt kifejezések keresésére használt stratégia. Mód csak a jelenleg támogatott `analyzingInfixMatching`, amely rugalmas megfelelő mondat eleji vagy mondat közepén hajt végre.|
|`sourceFields`|Egy vagy több mező, amelyek a forrás javaslatok tartalmának listáját. Csak típusú mezők `Edm.String` és `Collection(Edm.String)` lehet, hogy források javaslatokért. Csak állítsa be egyéni nyelvi elemzőt nem rendelkező mezők is használható.<p/>Csak azok a mezők kiválóan várt és megfelelő választ, adja meg, legyen szó egy keresősáv és a egy legördülő lista befejezett karakterlánc.<p/>Egy szállodai Vendég név jó jelöltnek számít egy pontosság, mert. Részletes mezők, például leírásokat és megjegyzéseket, túl sűrű. Hasonlóképpen ismétlődő mezőket, például a kategóriák és a címkék, kevésbé hatékonyak. A példákban tartalmazza "category" ennek ellenére több mezőt tartalmazhat bemutatásához. |

### <a name="index-rebuilds-for-existing-fields"></a>Index újraépíti a meglévő mezők

Javaslattevők mezőket tartalmaz, és ha a javaslattevő hozzáadása egy meglévő index vagy mező összetételének megváltoztatása, nagy valószínűséggel kell építenie az indexet.

| Műveletek | Hatás |
|--------|--------|
| Hozzon létre új mezőket, és hozzon létre egy új javaslattevő egyszerre ugyanazt a frissítést | Legkevésbé érintik. Ha az index tartalmazza a korábban hozzáadott mezők, új mezőket és a egy új javaslattevő nem befolyásolja a meglévő mezőket. |
| A javaslattevő már meglévő mezők hozzáadása | Nagy hatású. Egy mező megváltoztatja a mező-definíció hozzáadása, kialakulhat egy [teljes Újraépítés](search-howto-reindex.md).|

## <a name="use-a-suggester"></a>A javaslattevő használata

Fent említetteknek a javaslattevő is használhatja az automatikus kiegészítés, az automatikus kiegészítés, illetve mindkettőt. 

A javaslattevő együtt a műveletet a kérésben hivatkozott. Ha például egy GET REST hívást, adja meg vagy `suggest` vagy `autocomplete` a dokumentumok gyűjteményen. A REST, a javaslattevő létrehozása után használja a [javaslatok API](https://docs.microsoft.com/rest/api/searchservice/suggestions) vagy a [automatikus kiegészítés API (előzetes verzió)](https://docs.microsoft.com/rest/api/searchservice/autocomplete) a lekérdezés logikában.

A .NET-hez, használjon [SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet-preview) vagy [AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet-preview&viewFallbackFrom=azure-dotnet).

Minden kérelmet bemutatásához példák:

+ [Adja hozzá a legördülő listából lekérdezés beállításokhoz automatikus kiegészítés](search-autosuggest-example.md)

+ [Az automatikus kiegészítés hozzáadása az Azure Search részleges kifejezés bemeneti](search-autocomplete-tutorial.md) (az előzetes verzió) 

## <a name="sample-code"></a>Mintakód

A [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) mintát is tartalmaz C# és Java-kódot, és bemutatja a javaslattevő konstrukció, automatikus kiegészítés, az automatikus kiegészítés, és értékkorlátozó navigációs. 

A tesztkörnyezet Azure Search szolgáltatást használja, és előre betöltött index mindössze annyit kell tennie, nyomja le az F5 a futtatáshoz. Nincs előfizetés vagy szükség esetén jelentkezzen be.

## <a name="next-steps"></a>További lépések

Tekintse át a következő példák a kérések hogyan dolgoznak ki:

+ [Autosuggested lekérdezési példa](search-autosuggest-example.md) 
+ [Automatikus kiegészítéses lekérdezési példa (előzetes verzió)](search-autocomplete-tutorial.md) 
