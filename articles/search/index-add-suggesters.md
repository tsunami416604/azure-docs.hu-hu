---
title: Typeahead-lekérdezések hozzáadása indexhez – Azure Search
description: Adja meg a típus-előre lekérdezési műveleteket a Azure Search a javaslatok létrehozásával, valamint az automatikus kiegészítést vagy az automatikus javaslattal rendelkező lekérdezési feltételeket meghívó kérelmek összeállításával.
ms.date: 05/02/2019
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
ms.openlocfilehash: 22b53000fa2eebdd8f9cf7fd9f1a2d00763c035b
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533201"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Javaslatok hozzáadása a typeahead indexéhez Azure Search

A **javaslatok** egy olyan [Azure Search](search-what-is-an-index.md) -indexben lévő szerkezet, amely támogatja a "keresési típust". Tartalmazza azon mezők listáját, amelyekhez engedélyezni szeretné a typeahead-lekérdezés bemeneteit. Egy indexen belül ugyanaz a javaslat a két typeahead-változat egyikét vagy mindkettőt is támogatja: az *automatikus kiegészítés* befejezi a beírt kifejezést vagy kifejezést, a *javaslatok* rövid listát biztosítanak az eredményekről. 

A következő képernyőfelvétel a typeahead funkcióit mutatja be. Ebben az Xbox-keresési lapon az automatikus kiegészítési elemek a lekérdezés új keresési eredmények lapjára kerülnek, míg a javaslatok olyan tényleges eredmények, amelyek az adott játék oldalára mutatnak. Az automatikus kiegészítést a keresősáv egyik elemére korlátozhatja, vagy megadhat egy listát, például az itt láthatót. A javaslatok esetében egy olyan dokumentum bármely részét felhasználhatja, amely a legjobban leírja az eredményt.

![Az automatikus kiegészítés és a javasolt lekérdezések vizuális összehasonlítása](./media/index-add-suggesters/visual-comparison-suggest-complete.png "Az automatikus kiegészítés és a javasolt lekérdezések vizuális összehasonlítása")

A viselkedés Azure Searchban való megvalósításához van egy index és egy lekérdezési összetevő. 

+ Az index összetevő egy javaslat. A portál, REST API vagy .NET SDK használatával létrehozhat egy javaslatot. 

+ A lekérdezési összetevő a lekérdezési kérelemben megadott művelet (javaslat vagy automatikus kiegészítés művelet). 

A keresés a típus szerinti támogatás engedélyezve van a mező alapján. Mindkét typeahead-viselkedést ugyanazon keresési megoldáson belül implementálhatja, ha a képernyőképen láthatóhoz hasonló élményre van szüksége. Mindkét kérelem célja az adott index és válaszok dokumentumainak gyűjteménye, miután egy felhasználó legalább három karakterből álló bemeneti karakterláncot adott meg.

## <a name="create-a-suggester"></a>Javaslattevő létrehozása

Bár a javaslatok több tulajdonsággal rendelkeznek, elsősorban olyan mezők gyűjteménye, amelyekhez typeahead-élményt engedélyez. Előfordulhat például, hogy egy utazási alkalmazás lehetővé szeretné tenni a typeahead keresését a célhelyeken, a városokban és a látnivalókban. Így mindhárom mező a mezők gyűjteményében fog megjelenni.

A javaslatok létrehozásához adjon hozzá egyet egy index sémához. Egy indexben egy mutató is szerepelhet (konkrétan egy javaslat a javaslatok gyűjteményében). 

### <a name="use-the-rest-api"></a>A REST API használata

A REST API a [create index](https://docs.microsoft.com/rest/api/searchservice/create-index) vagy az [Update index](https://docs.microsoft.com/rest/api/searchservice/update-index)használatával adhat hozzá javaslatokat. 

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
A javaslat létrehozása után adja hozzá a [javaslatok API](https://docs.microsoft.com/rest/api/searchservice/suggestions) -t vagy az [automatikus kiegészítési API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) -t a lekérdezési logikában a funkció meghívásához.

### <a name="use-the-net-sdk"></a>A .NET SDK használata

A C#alkalmazásban adjon meg egy [javaslat objektumot](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters`egy gyűjtemény, de csak egyetlen tételt vehet igénybe. 

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

## <a name="property-reference"></a>Tulajdonság leírása

A javaslatokkal kapcsolatos fontos pontok azt jelzik, hogy van-e név (a javaslatok neve egy kérésre hivatkozik), egy searchMode (jelenleg csak egy, "analyzingInfixMatching") és azon mezők listája, amelyekhez engedélyezve van a typeahead. 

A javaslatot meghatározó tulajdonságok közé tartoznak a következők:

|Tulajdonság      |Leírás      |
|--------------|-----------------|
|`name`        |A javaslat neve. A [javaslatok REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) vagy [automatikus kiegészítési Rest APIkor](https://docs.microsoft.com/rest/api/searchservice/autocomplete)történő meghívásakor használja a javaslat nevét.|
|`searchMode`  |A jelölt kifejezésekre való kereséshez használt stratégia. Az egyetlen jelenleg támogatott `analyzingInfixMatching`mód a (z), amely a mondatok elején vagy végén a mondatok rugalmas megfeleltetését végzi.|
|`sourceFields`|Egy vagy több olyan mező listája, amely a javaslatok forrását képezi. Csak típusú `Edm.String` mezők szerepelhetnek `Collection(Edm.String)` , és a javaslatok forrása lehet. Csak olyan mezőket lehet használni, amelyek nem rendelkeznek egyéni nyelvi elemző készlettel.<p/>Csak azokat a mezőket határozza meg, amelyek a várt és a megfelelő választ adnak neki, legyen szó egy keresési sávon vagy egy legördülő listában szereplő befejezett sztringről.<p/>A Hotel neve jó jelölt, mert pontossággal rendelkezik. A részletes mezők, például a leírások és a megjegyzések túl sűrűk. Ehhez hasonlóan az ismétlődő mezők, például a kategóriák és a címkék kevésbé hatékonyak. A példákban bemutatjuk a "kategória" kifejezést is, amely azt mutatja be, hogy több mezőt is tartalmazhat. |

#### <a name="analysis-of-sourcefields-in-a-suggester"></a>SourceFields elemzése egy javaslatban

Azure Search elemzi a mező tartalmát, hogy engedélyezze az egyes feltételek lekérdezését. A javaslatok végrehajtásához az előtagokat a feltételek teljesítése mellett kell indexelni, és további elemzésre van szükség a forrás mezőin. Az egyéni analizátor-konfigurációk kombinálják a különböző tokenizers és szűrőket, gyakran olyan módokon, amelyek a javaslatok számára szükséges előtagokat is lehetetlenné tennék. Emiatt Azure Search meggátolja, hogy a rendszer az **Egyéni elemzőket tartalmazó mezőket a javaslatba foglalja**.

> [!NOTE] 
>  A fenti korlátozás megkerüléséhez ajánlott módszer két különálló mező használata ugyanahhoz a tartalomhoz. Ez lehetővé teszi, hogy az egyik mező legyen a javaslat, a másik pedig egyéni elemző konfigurációval.

## <a name="when-to-create-a-suggester"></a>Mikor hozzon létre egy javaslatot

Az indexek újraépítésének elkerüléséhez egy javaslatot és a benne `sourceFields` megadott mezőket is létre kell hozni.

Ha meglévő indexhez ad hozzá egy mutatót, ahol a meglévő mezők szerepelnek `sourceFields`a rendszerben, a mező definíciója alapvetően módosul, és Újraépítés szükséges. További információ: [Azure Search index újraépítése](search-howto-reindex.md).

## <a name="how-to-use-a-suggester"></a>Javaslatok használata

Ahogy korábban már említettük, használhat egy javaslatot a javasolt lekérdezésekhez, az automatikus kiegészítéshez vagy mindkettőhöz. 

A kérelemre a művelettel együtt hivatkozik. Például egy Get Rest-hívás esetén a dokumentumok gyűjteményben `suggest` adhatja `autocomplete` meg a következőt: vagy. REST esetén a javaslat létrehozása után használja a [javaslatok API](https://docs.microsoft.com/rest/api/searchservice/suggestions) -t vagy az [automatikus kiegészítés API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) -t a lekérdezési logikában.

.NET esetén használja a [SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet) vagy a [AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet).

A két kérést bemutató példát a [példa az automatikus kiegészítés és a javaslatok Azure Searchban](search-autocomplete-tutorial.md)való hozzáadására című részben talál.

## <a name="sample-code"></a>Mintakód

A [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) minta a és C# a Java kódot is tartalmazza, és bemutatja a javaslatok kialakítását, a javasolt lekérdezéseket, az automatikus kiegészítést és a dimenziós navigálást. 

Egy sandbox Azure Search szolgáltatást és egy előre betöltött indexet használ, így mindössze annyit kell tennie, hogy lenyomja az F5 billentyűt a futtatásához. Nincs szükség előfizetés vagy bejelentkezésre.

## <a name="next-steps"></a>További lépések

Azt javasoljuk, hogy tekintse át a kérelmek összeállításának módját a következő példán.

> [!div class="nextstepaction"]
> [Javaslatok és automatikus kiegészítési példák](search-autocomplete-tutorial.md) 
