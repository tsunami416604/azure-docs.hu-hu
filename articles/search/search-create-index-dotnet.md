---
title: A code használata a .NET API – Azure Search-index létrehozása
description: Ismerje meg, hogyan hozhat létre az Azure Search .NET SDK használatával egy teljes szöveges kereshető indexet és C# mintakódot.
author: brjohnstmsft
manager: jlembicz
tags: azure-portal
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/14/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: e0aa7dce0b3cc4609a995097d8e70f8ec4336809
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226368"
---
# <a name="quickstart-create-load-and-query-an-azure-search-index-using-the-net-sdk"></a>Gyors útmutató: Hozzon létre, betöltését és a .NET SDK használatával egy Azure Search-index lekérdezése
> [!div class="op_single_selector"]
> * [C#](search-create-index-dotnet.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [Postman (REST)](search-fiddler.md)
> * [Portál](search-create-index-portal.md)
> 

Ez a cikk végigvezeti a folyamat létrehozása, betöltése és lekérdezése az Azure Search [index](search-what-is-an-index.md) használatával a [Azure Search .NET SDK](https://aka.ms/search-sdk).

> [!NOTE]
> A cikkben szereplő összes példakód C# nyelven van megírva. A teljes forráskódot a [GitHub](https://aka.ms/search-dotnet-howto) webhelyén találja. Az [Azure Search .NET SDK](search-howto-dotnet-sdk.md) leírásában részletesebb útmutatást kaphat a példakóddal kapcsolatban.

## <a name="prerequisites"></a>Előfeltételek

+ [Az Azure Search szolgáltatás létrehozása](search-create-service-portal.md). Ebben a rövid útmutatóban egy ingyenes szolgáltatás használhatja.

+ [A Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), bármely kiadás esetén. Mintakód és útmutató az ingyenes közösségi kiadása lettek tesztelve.

+ URL-végpontot, és a felügyeleti api-kulcsot a keresési szolgáltatás. Mindkettőhöz létrejön egy keresési szolgáltatás, így ha hozzáadta az előfizetéséhez az Azure Searchöt, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

  1. Az Azure Portalon [keresse meg a szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a szolgáltatások listájában.

  2. A **áttekintése**, URL-címére. A végpontok például a következőképpen nézhetnek ki: `https://my-service-name.search.windows.net`.

  3. A **beállítások** > **kulcsok**, a szolgáltatás a teljes körű rendszergazdai kulcs beszerzése. Nincsenek két felcserélhetők adminisztrációs kulcsot, az üzletmenet folytonosságának megadott abban az esetben egy vihető kell. A kérés használhatja akár az elsődleges vagy másodlagos kulcsot.

     ![Egy HTTP-végpontját és hozzáférési kulcs lekérése](media/search-fiddler/get-url-key.png "HTTP végpontját és hozzáférési kulcs beszerzése")

     Minden kérelemhez szükséges halasztása minden kérelemnél a szolgáltatásnak küldött api-kulcsát. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="1---create-a-new-project"></a>1 – új projekt létrehozása

A Visual Studióban hozzon létre egy új Vizualizációt C# projekt. Ebben a rövid útmutatóban egy jó sablon a Vizualizáció C# > első lépések > webes alkalmazás. Ez a sablon lehetővé teszi az appsettings.json fájlt.  

Az appSettings.JSON fájl, cserélje le a tartalmat az alábbi példa a alapértelmezett, és adja meg a szolgáltatás nevét és a felügyeleti api-kulcsát. A szolgáltatásnév számára a név csak kell. Például, ha az URL-cím https://mydemo.search.windows.net, adjon hozzá `mydemo` a JSON-fájlba.


```json
{
    "SearchServiceName": "Put your search service name here",
    "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
}
```

<a name="CreateSearchServiceClient"></a>

## <a name="2---create-an-instance-of-the-searchserviceclient-class"></a>2 – a SearchServiceClient osztály példányának létrehozása
Az Azure Search .NET SDK használatához létre kell hoznia a `SearchServiceClient` osztály egy példányát. Ez az osztály több konstruktorral rendelkezik. Az, amelyiket Ön szeretne, a keresőszolgáltatása nevét és egy `SearchCredentials` objektumot használ paraméterként. A `SearchCredentials` becsomagolja az API-kulcsot.

Másolja a következő kódot a Program.cs fájlba. Az alábbi kód létrehoz egy új `SearchServiceClient` a search-szolgáltatásnév és api-kulcsot, az alkalmazás konfigurációs fájljában (appsettings.json) tárolt értékekkel.

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

## <a name="3---define-an-index-schema"></a>3 – az indexsémát definiálása
A `Indexes.Create` módszer egyetlen meghívása létrehozza az indexet. Ez a módszer egy `Index` objektumot használ paraméterként, amely meghatározza az Azure Search-indexet. Létre kell hoznia és inicializálnia kell egy `Index` objektumot az alábbiak szerint:

1. Állítsa be az `Index` objektum `Name` tulajdonságát az index nevének.

2. Állítsa be az `Index` objektum `Fields` tulajdonságát a `Field` objektumok tömbjének. A `Field` objektumok létrehozásának legegyszerűbb módja az, ha meghívja a `FieldBuilder.BuildForType` metódust, és a típus paraméternél egy modellosztályt ad meg. A modellosztály olyan tulajdonságokkal rendelkezik, amelyek az index mezőire mutatnak. Ez lehetővé teszi a keresési indexben található dokumentumok modellosztály-példányokhoz kötését is.

> [!NOTE]
> Ha nem tervez modellosztályt használni, közvetlenül `Field` objektumok létrehozásával is meghatározhatja az indexet. A konstruktornak megadhatja a mező nevét és az adattípust (vagy sztringmezők esetében az elemzőnek). Más tulajdonságokat is beállíthat, például: `IsSearchable`, `IsFilterable` stb.
>
>

Fontos, hogy az index megtervezésekor a felhasználóként szerzett keresési tapasztalatának és üzleti igényeinek szem előtt tartásával járjon el, és az egyes mezőkhöz a [megfelelő tulajdonságokat](https://docs.microsoft.com/rest/api/searchservice/Create-Index) rendelje. Ezek a tulajdonságok határozzák meg, hogy melyik mezőkre melyik keresési funkciók (szűrés, értékkorlátozás, rendezés, teljes szöveges keresés stb.) vonatkoznak. Azon tulajdonságok esetében, amelyeket külön nem állított be, a `Field` osztály alapértelmezés szerint letiltja a megfelelő keresési funkciót, kivéve, ha Ön kifejezetten engedélyezi.

A fenti példában az indexnek a „hotels” nevet adtuk, és a mezőket egy modellosztály segítségével definiáltuk. A modellosztály minden tulajdonsága olyan attribútumokkal rendelkezik, amelyek meghatározzák a vonatkozó indexmező kereséssel kapcsolatos viselkedéseit. A modellosztály meghatározása a következőképpen történik:

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

A fenti indexdefiníció egy nyelvi elemzőt használ a `description_fr` mező esetében, mivel annak francia nyelvű szöveget kell tartalmaznia. A nyelvi elemzőkkel kapcsolatos további információkért lásd a [Nyelvi támogatás című témakört](https://docs.microsoft.com/rest/api/searchservice/Language-support), valamint a vonatkozó [blogbejegyzést](https://azure.microsoft.com/blog/language-support-in-azure-search/).

> [!NOTE]
> Alapértelmezés szerint az index minden mezőjének neve megegyezik a modellosztály megfelelő tulajdonságainak a nevével. Ha minden tulajdonságnevet szóközök nélküli, nagybetűs szavakat (CamelCase) tartalmazó mezőnevekhez szeretne hozzárendelni, akkor jelölje meg az osztályt a `SerializePropertyNamesAsCamelCase` attribútummal. Ha másik névhez szeretné őket hozzárendelni, akkor a fenti `DescriptionFr` tulajdonsághoz hasonlóan a `JsonProperty` attribútumot is használhatja. A `JsonProperty` attribútum előnyt élvez a `SerializePropertyNamesAsCamelCase` attribútummal szemben.
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

## <a name="4---create-the-index-on-the-service"></a>4 – hozza létre az indexet a szolgáltatásban
Most, hogy már rendelkezik egy inicializált `Index` objektummal, a `SearchServiceClient` objektumon lévő `Indexes.Create` meghívásával egyszerűen létrehozhatja az indexet:

```csharp
serviceClient.Indexes.Create(definition);
```

Sikeres kérelem esetén a módszer normálisan tér vissza. Ha probléma merül fel, például érvénytelen egy paraméter, a módszer a `CloudException` kivételt fogja kijelezni.

Miután létrehozta az indexet, és törölni szeretné, csak hívja meg a `SearchServiceClient` objektumon lévő `Indexes.Delete` módszert. A „hotels” nevű index például a következőképpen törölhető:

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [!NOTE]
> Ebben a cikkben a példakód az egyszerűség érdekében az Azure Search .NET SDK szinkron módszereit használja. Azt javasoljuk, hogy a méretezhetőség és a gyors válaszadás érdekében használja saját alkalmazásaiban az aszinkron módszereket. Például a fenti esetekben használhatja a `CreateAsync` és a `DeleteAsync` módszert a `Create` és a `Delete` helyett.
> 
> 


## <a name="next-steps"></a>További lépések
Az Azure Search-index létrehozása után készen áll arra, hogy [feltöltse a tartalmát az indexbe](search-what-is-data-import.md), és megkezdje az adatok keresését.

