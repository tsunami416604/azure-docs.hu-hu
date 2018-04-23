---
title: Index lekérdezése (.NET API – Azure Search) | Microsoft Docs
description: Létrehozhat keresési lekérdezést az Azure Search szolgáltatásban, a keresési eredmények szűrését és rendezését pedig keresési paraméterek használatával végezheti el.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/19/2017
ms.openlocfilehash: 7aa372f751cc15e05cd7f118a862275d21627d73
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="query-your-azure-search-index-using-the-net-sdk"></a>Az Azure Search-index lekérdezése a .NET SDK használatával
> [!div class="op_single_selector"]
> * [Áttekintés](search-query-overview.md)
> * [Portál](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

Ebből a cikkből megtudhatja, hogyan történik egy index lekérdezése az [Azure Search .NET SDK](https://aka.ms/search-sdk) használatával.

A bemutató elindítása előtt [létre kell hoznia egy Azure Search-indexet](search-what-is-an-index.md), majd [fel kell töltenie azt adatokkal](search-what-is-data-import.md).

> [!NOTE]
> A cikkben szereplő összes példakód C# nyelven van megírva. A teljes forráskódot a [GitHub](http://aka.ms/search-dotnet-howto) webhelyén találja. Az [Azure Search .NET SDK](search-howto-dotnet-sdk.md) leírásában részletesebb útmutatást kaphat a példakóddal kapcsolatban.

## <a name="identify-your-azure-search-services-query-api-key"></a>Azonosítsa az Azure Search szolgáltatás lekérdezési API-kulcsát
Az Azure Search-index létrehozását követően most már csaknem készen áll lekérdezések kiadására a .NET SDK használatával. Először is az Ön által üzembe helyezett Search szolgáltatás számára létrehozott lekérdezési API-kulcsok egyikére lesz szüksége. A .NET SDK ezt az API-kulcsot minden szolgáltatáskérés alkalmával elküldi. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

1. A szolgáltatás API-kulcsainak megkereséséhez bejelentkezhet az [Azure Portalra](https://portal.azure.com/).
2. Nyissa meg az Azure Search szolgáltatáspaneljét
3. Kattintson a „Kulcsok” ikonra

A szolgáltatás *rendszergazdai kulcsokkal* és *lekérdezési kulcsokkal* fog rendelkezni.

* Az elsődleges és másodlagos *rendszergazdai kulcsok* teljes jogosultságot biztosítanak az összes művelethez, beleértve a szolgáltatás felügyeletének, valamint az indexek, indexelők és adatforrások létrehozásának és törlésének képességét. Két kulcs létezi, tehát ha az elsődleges kulcs újbóli létrehozása mellett dönt, a másodlagos kulcsot továbbra is használhatja (ez fordítva is igaz).
* A *lekérdezési kulcsok* csak olvasási hozzáférést biztosítanak az indexekhez és dokumentumokhoz, és általában a keresési kérelmeket kibocsátó ügyfélalkalmazások kapják meg őket.

Indexlekérdezéshez a lekérdezési kulcsok egyikét használhatja. A rendszergazdai kulcsok szintén használhatók a lekérdezésekhez, az alkalmazáskódban azonban inkább lekérdezési kulcsot használjon, mivel ez a módszer jobban követi a [legalacsonyabb jogosultsági szint elvét](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## <a name="create-an-instance-of-the-searchindexclient-class"></a>A SearchIndexClient osztály egy példányának létrehozása
A lekérdezések Azure Search .NET SDK használatával történő kiadásához létre kell hoznia a `SearchIndexClient` osztály egy példányát. Ez az osztály több konstruktorral rendelkezik. Amelyikre Önnek szüksége van, az paraméterként a Search-szolgáltatás nevét, az indexnevet és egy `SearchCredentials` objektumot használ. A `SearchCredentials` becsomagolja az API-kulcsot.

Az alábbi kód egy új `SearchIndexClient` elemet hoz létre az ([Azure Search-index létrehozása .NET SDK használatával](search-create-index-dotnet.md) című részben létrehozott) „hotels” index számára, az alkalmazás konfigurációs fájljában (a [mintaalkalmazás](http://aka.ms/search-dotnet-howto) esetében az `appsettings.json` fájlban) tárolt Search-szolgáltatásnév és API-kulcs értékeinek használatával:

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

A `SearchIndexClient` rendelkezik egy `Documents` tulajdonsággal. Ezen tulajdonság biztosítja mindazokat a módszereket, amelyek az Azure Search-indexek lekérdezéséhez szükségesek.

## <a name="query-your-index"></a>Az index lekérdezése
A .NET SDK használatával történő keresés ugyanolyan egyszerűen végrehajtható, mint a `Documents.Search` módszer meghívása a következőn: `SearchIndexClient`. Ezen módszer néhány paramétert használ, ide értve a keresett szöveget, a lekérdezés további finomításához használható `SearchParameters` objektummal együtt.

#### <a name="types-of-queries"></a>A lekérdezések típusai
Az itt használt két fő [lekérdezési típus](search-query-overview.md#types-of-queries): `search` és `filter`. A `search` lekérdezés egy vagy több kifejezésre keres rá az index összes *searchable* (kereshető) mezőjében. A `filter` lekérdezés egy logikai kifejezés kiértékelését végzi el az index összes *filterable* (szűrhető) mezőjén. A keresések és a szűrések együtt vagy külön-külön is alkalmazhatók.

A keresések és a szűrések egyaránt a `Documents.Search` módszer használatával vannak végrehajtva. Keresési lekérdezések a `searchText` paraméterben, szűrőkifejezések pedig a `SearchParameters` osztály `Filter` tulajdonságában adhatóak át. A keresés nélküli szűrés végrehajtásához a `searchText` paraméter számára a `"*"` kifejezést adja át. A szűrés nélküli keresés végrehajtásához ne állítsa be a `Filter` tulajdonságot, vagy egyáltalán ne adja át azt egy `SearchParameters`-példányban.

#### <a name="example-queries"></a>Példa a lekérdezésekre
Az alábbi mintakód néhány különböző módját mutatja be az [Azure Search-index létrehozása .NET SDK használatával](search-create-index-dotnet.md#DefineIndex) című részben meghatározott „hotels” index lekérdezésének. Vegye figyelembe, hogy a keresési eredményekkel visszaadott dokumentumok annak a `Hotel` osztálynak a példányai, amely az [Adatok importálása az Azure Search szolgáltatásban .NET SDK használatával](search-import-data-dotnet.md#HotelClass) című részben lett meghatározva. Ez a mintakód a `WriteDocuments` módszer használatával jeleníti meg a keresési eredményeket a konzolon. Ezt a módszert a következő szakasz ismerteti.

```csharp
SearchParameters parameters;
DocumentSearchResult<Hotel> results;

Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);

Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
Console.WriteLine("and return the hotelId and description:\n");

parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
Console.Write("in descending order, take the top two results, and show only hotelName and ");
Console.WriteLine("lastRenovationDate:\n");

parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'motel':\n");

parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

## <a name="handle-search-results"></a>A keresési eredmények kezelése
A `Documents.Search` módszer olyan `DocumentSearchResult` objektumot ad vissza, amely tartalmazza a lekérdezés eredményeit. Az előző szakaszban szereplő példa a `WriteDocuments` módszer használatával jelenítette meg a keresési eredményeket a konzolon:

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Az előző szakasz lekérdezéseinek eredménye a következőképpen fog megjelenni, feltételezve, hogy a „hotels” index az [Adatok importálása az Azure Search szolgáltatásban .NET SDK használatával](search-import-data-dotnet.md) rész mintaadataival van feltöltve:

```
Search the entire index for the term 'budget' and return only the hotelName field:

Name: Roach Motel

Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:

ID: 2   Description: Cheapest hotel in town
ID: 3   Description: Close to town hall and the river

Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Search the entire index for the term 'motel':

ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577
```

A fenti mintakód a keresési eredményeket a konzolon jeleníti meg. A keresési eredményeket hasonlóképpen kell megjeleníteni a saját alkalmazásában is. Az ASP.NET MVC-alapú webalkalmazásokban történő keresésieredmény-rendelerelésre itt láthat példát: [a GitHub-on lévő minta](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample).

