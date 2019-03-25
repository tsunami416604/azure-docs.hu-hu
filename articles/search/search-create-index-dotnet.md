---
title: Az index létrehozása C# – Azure Search
description: Ismerje meg, hogyan hozhat létre a kereshető teljes szöveges index C# az Azure Search .NET SDK használatával.
author: heidisteen
manager: cgronlun
ms.author: heidist
tags: azure-portal
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/22/2019
ms.openlocfilehash: a5861faaf26962d34d1c356e29dce1be40f8716b
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370584"
---
# <a name="quickstart-1---create-an-azure-search-index-in-c"></a>Gyors útmutató: 1 – az Azure Search-index létrehozásaC#

Ez a cikk végigvezeti a létrehozásának folyamatán [Azure Search-index](search-what-is-an-index.md) használatával C# és a [.NET SDK-val](https://aka.ms/search-sdk). Ez az első leckéje egy 3. rész – a gyakorlatban a létrehozása, betöltés és lekérdezés az index. Az index létrehozása ezen feladatok végrehajtásával valósul meg:

> [!div class="checklist"]
> * Hozzon létre egy [ `SearchServiceClient` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) objektum egy keresési szolgáltatáshoz való csatlakozáshoz.
> * Hozzon létre egy [ `Index` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) objektumot adja át a paramétert `Indexes.Create`.
> * Hívja a `Indexes.Create` metódust `SearchServiceClient` küldése a `Index` szolgáltatáshoz.

## <a name="prerequisites"></a>Előfeltételek

[Az Azure Search szolgáltatás létrehozása](search-create-service-portal.md) vagy [keresse meg a meglévő service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) az aktuális előfizetésben. Ebben a rövid útmutatóban egy ingyenes szolgáltatás használhatja.

[A Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), bármely kiadás esetén. Mintakód és útmutató az ingyenes közösségi kiadása lettek tesztelve.

A végpont URL-cím és a felügyeleti api-kulcsot a keresési szolgáltatás. Mindkettőhöz létrejön egy keresési szolgáltatás, így ha hozzáadta az előfizetéséhez az Azure Searchöt, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

  1. Az Azure Portalon, a search szolgáltatás **áttekintése** lapon, az URL-cím lekéréséhez. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

  2. A **beállítások** > **kulcsok**, a szolgáltatás a teljes körű rendszergazdai kulcs beszerzése. Nincsenek két felcserélhetők adminisztrációs kulcsot, az üzletmenet folytonosságának megadott abban az esetben egy vihető kell. Használható vagy az elsődleges vagy másodlagos kulcsot a kérések hozzáadása, módosítása és törlése objektumokat.

  ![Egy HTTP-végpontját és hozzáférési kulcs lekérése](media/search-fiddler/get-url-key.png "HTTP végpontját és hozzáférési kulcs beszerzése")

Minden kérelemhez szükséges halasztása minden kérelemnél a szolgáltatásnak küldött api-kulcsát. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="1---open-the-project"></a>1 – a projekt megnyitása

A mintakód letöltése [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) a Githubról. 

Az appSettings.JSON fájl, cserélje le a tartalmat az alábbi példa a alapértelmezett, és adja meg a szolgáltatás nevét és a felügyeleti api-kulcsát. A szolgáltatásnév számára a név csak kell. Például, ha az URL-cím https://mydemo.search.windows.net, adjon hozzá `mydemo` a JSON-fájlba.


```json
{
    "SearchServiceName": "Put your search service name here",
    "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
}
```

Miután beállította ezeket az értékeket, így F5 build a megoldást a Konzolalkalmazás futtatása. Ebben a gyakorlatban, valamint azokat, amelyeket hajtsa végre a fennmaradó lépésekkel olyan áttekintése, ez a kód működését. 

Azt is megteheti, olvassa el [használata az Azure Search .NET-alkalmazásból ](search-howto-dotnet-sdk.md) lefedettségét az SDK működésének részletesebb. 

<a name="CreateSearchServiceClient"></a>

## <a name="2---create-a-client"></a>2 – az ügyfél létrehozása

Az Azure Search .NET SDK használatának megkezdéséhez hozzon létre egy példányt a `SearchServiceClient` osztály. Ez az osztály több konstruktorral rendelkezik. Az, amelyiket Ön szeretne, a keresőszolgáltatása nevét és egy `SearchCredentials` objektumot használ paraméterként. A `SearchCredentials` becsomagolja az API-kulcsot.

A következő kódot a Program.cs fájlban található. Létrehoz egy új `SearchServiceClient` a search-szolgáltatásnév és api-kulcsot, az alkalmazás konfigurációs fájljában (appsettings.json) tárolt értékekkel.

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

`SearchServiceClient``Indexes`tulajdonsággal rendelkezik. Ez a tulajdonság az Azure Search-indexek létrehozásához, listázásához, frissítéséhez vagy törléséhez szükséges összes módszert biztosítja.

> [!NOTE]
> A `SearchServiceClient` osztály kezeli a keresőszolgáltatása kapcsolatait. A túl sok kapcsolat megnyitásának elkerülése érdekében, ha lehetséges, próbálja meg a `SearchServiceClient` egyetlen példányát megosztani az alkalmazásban. A módszerei szálbiztosak az ilyen megosztás engedélyezéséhez.
> 
> 

<a name="DefineIndex"></a>

## <a name="3---construct-index"></a>3 - Index létrehozása
Egyetlen meghívása a `Indexes.Create` módszer létrehoz egy indexet. Ezt a módszert használja paraméterként egy `Index` objektum, amely az Azure Search-index meghatározása. Hozzon létre egy `Index` objektumra, és inicializálni a következőképpen:

1. Állítsa be az `Index` objektum `Name` tulajdonságát az index nevének.

2. Állítsa be az `Index` objektum `Fields` tulajdonságát a `Field` objektumok tömbjének. A `Field` objektumok létrehozásának legegyszerűbb módja az, ha meghívja a `FieldBuilder.BuildForType` metódust, és a típus paraméternél egy modellosztályt ad meg. A modellosztály olyan tulajdonságokkal rendelkezik, amelyek az index mezőire mutatnak. Ez lehetővé teszi a keresési indexben található dokumentumok modellosztály-példányokhoz kötését is.

> [!NOTE]
> Ha nem tervez modellosztályt használni, közvetlenül `Field` objektumok létrehozásával is meghatározhatja az indexet. A konstruktornak megadhatja a mező nevét és az adattípust (vagy sztringmezők esetében az elemzőnek). Például egyéb tulajdonságokat is beállíthat `IsSearchable`, `IsFilterable`, hogy néhányat említsünk.
>
>

Fontos, hogy Ön szem előtt tartani a keresési felhasználói élmény és az üzleti igényeket az index tervezésekor. Minden mezőt hozzá kell rendelni a [attribútumok](https://docs.microsoft.com/rest/api/searchservice/Create-Index) , hogy melyik keresési funkciók (szűrés, értékkorlátozás, rendezés, és így tovább) vezérlőelem alkalmazni a mezőket. Azon tulajdonságok esetében, amelyeket külön nem állított be, a `Field` osztály alapértelmezés szerint letiltja a megfelelő keresési funkciót, kivéve, ha Ön kifejezetten engedélyezi.

Ebben a példában az index neve "hotels" és mezőket egy modellosztály segítségével vannak definiálva. A modellosztály minden tulajdonsága olyan attribútumokkal rendelkezik, amelyek meghatározzák a vonatkozó indexmező kereséssel kapcsolatos viselkedéseit. A modellosztály meghatározása a következőképpen történik:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }

    [IsSearchable]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    [IsFilterable, IsFacetable]
    public bool? SmokingAllowed { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public int? Rating { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }
}
```

Minden tulajdonság esetében annak alapján választottuk ki az attribútumokat, ahogyan szerintünk az alkalmazások használni fogják őket. Valószínű például, hogy a hotelekre kereső felhasználókat érdekelhetik majd a `description` mezőben megadott kulcsszavak, így erre a mezőre vonatkozóan engedélyeztük a teljes szöveges keresést úgy, hogy a `Description` tulajdonsághoz hozzáadtuk az `IsSearchable` attribútumot.

Vegye figyelembe, hogy az indexben csak egy `string` típusú mező lehet kijelölve *kulcsmezőként* a `Key` attribútum hozzáadásával (lásd a fenti példában: `HotelId`).

A fenti indexdefiníció egy nyelvi elemzőt használ a `description_fr` mező esetében, mivel annak francia nyelvű szöveget kell tartalmaznia. További információkért lásd: [nyelvi elemzők hozzáadása az Azure Search-index](index-add-language-analyzers.md).

> [!NOTE]
> Alapértelmezés szerint a modellosztály minden tulajdonsága neve felel meg a mező nevét az indexben. Ha minden tulajdonságnevet szóközök nélküli, nagybetűs szavakat (CamelCase) tartalmazó mezőnevekhez szeretne hozzárendelni, akkor jelölje meg az osztályt a `SerializePropertyNamesAsCamelCase` attribútummal. Ha másik névhez szeretné őket hozzárendelni, akkor a fenti `DescriptionFr` tulajdonsághoz hasonlóan a `JsonProperty` attribútumot is használhatja. A `JsonProperty` attribútum előnyt élvez a `SerializePropertyNamesAsCamelCase` attribútummal szemben.
> 
> 

Most, hogy meghatároztuk a modellosztályt, már egyszerűen létrehozhatunk egy indexdefiníciót:

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = FieldBuilder.BuildForType<Hotel>()
};
```

## <a name="4---call-indexescreate"></a>4 – hívás Indexes.Create
Most, hogy már rendelkezik egy inicializált `Index` objektumazonosító, hozza létre az indexet meghívásával `Indexes.Create` a a `SearchServiceClient` objektum:

```csharp
serviceClient.Indexes.Create(definition);
```

Sikeres kérelem esetén a módszer normálisan tér vissza. Ha probléma merül fel, például érvénytelen egy paraméter, a módszer a `CloudException` kivételt fogja kijelezni.

Ha végzett az index, és törölni szeretné, hívja a `Indexes.Delete` metódust a `SearchServiceClient`. Példa:

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [!NOTE]
> Ebben a cikkben a példakód az egyszerűség érdekében az Azure Search .NET SDK szinkron módszereit használja. Azt javasoljuk, hogy a méretezhetőség és a gyors válaszadás érdekében használja saját alkalmazásaiban az aszinkron módszereket. Például a fenti esetekben használhatja a `CreateAsync` és a `DeleteAsync` módszert a `Create` és a `Delete` helyett.
> 
> 

## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban létrehozott egy üres Azure Search-index adattípusai és működési módokkal definiáló séma alapján. Az index egy "bare csontot" index nevét és a egy kiadott mezők gyűjteménye. Egy realisztikusabb index magában foglalja a más elemek, például [pontozási profilok](index-add-scoring-profiles.md), [javaslattevők](index-add-suggesters.md) typeahead támogatást [szinonimák](search-synonyms.md), és esetleg [ egyéni elemzők](index-add-custom-analyzers.md). Azt javasoljuk, hogy az alapvető munkafolyamat megismerése után tekintse át ezeket a képességeket felülvizsgálni.

Az oktatóanyag-sorozatban a következő rövid útmutató bemutatja, hogyan betölteni az indexet a kereshető tartalmak.

> [!div class="nextstepaction"]
> [Adatok betöltése az Azure Search index használatávalC#](search-import-data-dotnet.md)