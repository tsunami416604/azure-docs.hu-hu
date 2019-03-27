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
ms.openlocfilehash: 5a46575f6e8a0b05b65dbf49c70bddb570b514b2
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497433"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Az Azure Search typeahead indexet javaslattevők hozzáadása

A **javaslattevő** egy szerkezet az egy [Azure Search-index](search-what-is-an-index.md) , amely támogatja a "Keresés –--beíráskor" felületet. Tartalmaz, amelynek szeretné engedélyezni, typeahead lekérdezés bemenetei között a mezők listája. Typeahead két változata van, melyek: *automatikus kiegészítés* elkezdi beírni a, kifejezéssel befejezése *javaslatok* eredmények rövid listáját tartalmazza. 

Xbox keresési lap az automatikus kiegészítés elemek végigvezetik a keresési eredmények új lapot az adott lekérdezés, míg a tényleges eredmények, amelyek egy oldalt, hogy adott játék. A keresősávba egy elemet az automatikus kiegészítés korlátozza, vagy adja meg itt látható egy hasonló listáját. A javaslatok egy dokumentumot, az eredmény legjobban illő bármely részét-kkel.

![Az automatikus kiegészítés és a javasolt lekérdezések Visual összehasonlítása](./media/index-add-suggesters/visual-comparison-suggest-complete.png "automatikus kiegészítés és a javasolt lekérdezések Visual összehasonlítása")

Ezen viselkedés megvalósítása az Azure Search, egy index és lekérdezés-összetevő van. 

+ Adja hozzá a javaslattevő az indexben. A portal, a REST API vagy a .NET SDK használatával hozzon létre egy javaslattevő. 

+ A lekérdezés adja meg a javaslat vagy sutocomplete művelet. 

> [!Important]
> Automatikus kiegészítési funkciója jelenleg előzetes verziójú, REST API-k előzetes verzióban érhető el, ezért a .NET SDK-t, és az éles környezetben nem támogatott. 

Keresés –--beíráskor támogatása engedélyezve van, mező alapon. Ha azt szeretné, felületet nyújt a képernyőképen jelzett hasonló mindkét typeahead viselkedések belül ugyanazt a keresési megoldást valósíthat meg. Mindkét kérelmek cél a *dokumentumok* után a felhasználó legalább egy három bemeneti sztring biztosított meghatározott index és a válaszok gyűjteményét adja vissza.

## <a name="create-a-suggester"></a>Javaslattevő létrehozása

Habár a javaslattevő számos tulajdonságát, elsősorban engedélyezi egy typeahead élmény mezők gyűjteménye. Például egy utazási alkalmazás érdemes a destinations, város és létesítmények typeahead keresésének lehetővé tételéhez. Ennek megfelelően minden három mezőt a mezők gyűjteménye a helyezkedik el.

Hozzon létre egy javaslattevő, adjon hozzá egy-egy indexsémát. Használhat egy javaslattevő index (pontosabban a javaslattevők gyűjtemény egy javaslattevő). 

### <a name="use-the-rest-api"></a>A REST API használata

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
A javaslattevő létrehozása után adja hozzá a [javaslatok API](https://docs.microsoft.com/rest/api/searchservice/suggestions) vagy [automatikus kiegészítés API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) a lekérdezés logikai meghívni a szolgáltatást.

### <a name="use-the-net-sdk"></a>A .NET SDK használata

A C#, adja meg egy [javaslattevő osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). Javaslattevő olyan gyűjteménye, de csak egy elemet is eltarthat.

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

## <a name="property-reference"></a>Tulajdonsághivatkozás

Figyelje meg, hogy javaslattevők kapcsolatos legfontosabb az, hogy nincs-e (kérelem neve szerint javaslattevők hivatkozott) nevet, egy searchMode (jelenleg csak egyetlen, "analyzingInfixMatching") és a mezőlistában, amelynek typeahead engedélyezve van. 

A javaslattevő meghatározó tulajdonságok a következők:

|Tulajdonság      |Leírás      |
|--------------|-----------------|
|`name`        |A javaslattevő neve. A javaslattevő nevét használja, hívásakor a [javaslatok REST API-val](https://docs.microsoft.com/rest/api/searchservice/suggestions) vagy [automatikus kiegészítés REST API (előzetes verzió)](https://docs.microsoft.com/rest/api/searchservice/autocomplete).|
|`searchMode`  |A jelölt kifejezések keresésére használt stratégia. Mód csak a jelenleg támogatott `analyzingInfixMatching`, amely rugalmas megfelelő mondat eleji vagy mondat közepén hajt végre.|
|`sourceFields`|Egy vagy több mező, amelyek a forrás javaslatok tartalmának listáját. Csak típusú mezők `Edm.String` és `Collection(Edm.String)` lehet, hogy források javaslatokért. Csak állítsa be egyéni nyelvi elemzőt nem rendelkező mezők is használható.<p/>Csak azok a mezők kiválóan várt és megfelelő választ, adja meg, legyen szó egy keresősáv és a egy legördülő lista befejezett karakterlánc.<p/>Egy szállodai Vendég név jó jelöltnek számít egy pontosság, mert. Részletes mezők, például leírásokat és megjegyzéseket, túl sűrű. Hasonlóképpen ismétlődő mezőket, például a kategóriák és a címkék, kevésbé hatékonyak. A példákban tartalmazza "category" ennek ellenére több mezőt tartalmazhat bemutatásához. |

## <a name="when-to-create-a-suggester"></a>Mikor hozzon létre egy javaslattevő

Az index újraépítése, a javaslattevő és megadott mezőket elkerülése érdekében `sourceFields` egyszerre kell létrehozni.

Ha egy meglévő index, amelyben meglévő mezők szerepelnek a javaslattevő hozzá `sourceFields`, alapvetően megváltoztatja a mező-definíció és újjáépítést megadása kötelező. További információkért lásd: [hogyan építse újra az Azure Search-index](search-howto-reindex.md).

## <a name="how-to-use-a-suggester"></a>A javaslattevő használata

Fent említetteknek használhatja a javaslattevő javasolt lekérdezések, az automatikus kiegészítés, illetve mindkettőt. 

A javaslattevő együtt a műveletet a kérésben hivatkozott. Ha például egy GET REST hívást, adja meg vagy `suggest` vagy `autocomplete` a dokumentumok gyűjteményen. A REST, a javaslattevő létrehozása után használja a [javaslatok API](https://docs.microsoft.com/rest/api/searchservice/suggestions) vagy a [automatikus kiegészítés API (előzetes verzió)](https://docs.microsoft.com/rest/api/searchservice/autocomplete) a lekérdezés logikában.

A .NET-hez, használjon [SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet-preview) vagy [AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet-preview&viewFallbackFrom=azure-dotnet).

Mindkét kérelmek bemutatására egy példa: [példa hozzáadása az automatikus kiegészítés és javaslatok az Azure Search](search-autocomplete-tutorial.md).

## <a name="sample-code"></a>Mintakód

A [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) mintát is tartalmaz C# és Java-kódot, és a egy javaslattevő konstrukció, javasolt lekérdezések, az automatikus kiegészítés és értékkorlátozó navigációs mutatja be. 

A tesztkörnyezet Azure Search szolgáltatást használja, és előre betöltött index mindössze annyit kell tennie, nyomja le az F5 a futtatáshoz. Nincs előfizetés vagy szükség esetén jelentkezzen be.

## <a name="next-steps"></a>További lépések

Azt javasoljuk, hogy az alábbi példa megtekintéséhez, hogyan dolgoznak ki a kérelmeket.

> [!div class="nextstepaction"]
> [Automatikus kiegészítéses lekérdezési példa (előzetes verzió)](search-autocomplete-tutorial.md) 
