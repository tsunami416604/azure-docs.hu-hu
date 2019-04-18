---
title: Az adatokat az Azure Search-index lekérdezése C# (.NET SDK-t) – Azure Search
description: C#mintakód az Azure Search keresési lekérdezés létrehozásához. Keresési paraméterek hozzáadása a szűrés és rendezés a keresési eredmények.
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/20/2019
ms.openlocfilehash: 1194407122123797c2564c96ac452b9582b017a4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59264947"
---
# <a name="quickstart-3---query-an-azure-search-index-in-c"></a>Gyors útmutató: 3 – az Azure Search-index lekérdezéseC#

Ez a cikk bemutatja, hogyan lehet lekérdezést [Azure Search-index](search-what-is-an-index.md) használatával C# és a [.NET SDK-val](https://aka.ms/search-sdk). Az indexben lévő keresése a dokumentumok ezen feladatok végrehajtásával történik:

> [!div class="checklist"]
> * Hozzon létre egy [ `SearchIndexClient` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) kapcsolódni a keresési index csak olvasási jogosultsággal rendelkező objektum.
> * Hozzon létre egy [ `SearchParameters` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet) keresés vagy a szűrő definícióját tartalmazó objektumot.
> * Hívja a `Documents.Search` metódust `SearchIndexClient` a lekérdezések elküldése egy indexbe.

## <a name="prerequisites"></a>Előfeltételek

[Azure Search-index betöltése](search-import-data-dotnet.md) a szállodák mintaadatokkal.

Dokumentumok a csak olvasható hozzáférést használt lekérdezési kulcs beszerzése. Eddig használt egy rendszergazdai API-kulcsot, hogy az objektumokat és tartalmat hozhat létre a keresési szolgáltatás. De a lekérdezés alkalmazások támogatása, javasoljuk, hogy a lekérdezési kulcs használatával. Útmutatásért lásd: [hozzon létre egy lekérdezési kulcsot](search-security-api-keys.md#create-query-keys).

## <a name="create-a-client"></a>-Ügyfél létrehozása
Hozzon létre egy példányt a `SearchIndexClient` osztályhoz, hogy a csak olvasási hozzáférés egy lekérdezési kulcsot is biztosítanak (ruházott jogok írási hozzáféréssel ellentétben a `SearchServiceClient` az előző leckében használt).

Ez az osztály több konstruktorral rendelkezik. A amelyikre Önnek szüksége van a keresőszolgáltatás nevének, az Indexnevet és a egy [ `SearchCredentials` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials?view=azure-dotnet) objektumot használ paraméterként. A `SearchCredentials` becsomagolja az API-kulcsot.

Az alábbi kód létrehoz egy új `SearchIndexClient` a search-szolgáltatásnév és api-kulcsot, az alkalmazás konfigurációs fájljában tárolt értékekkel "hotels" index számára (`appsettings.json` abban az esetben, a [mintaalkalmazás](https://aka.ms/search-dotnet-howto)):

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

`SearchIndexClient` rendelkezik egy [ `Documents` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient.documents?view=azure-dotnet) tulajdonság. Ezen tulajdonság biztosítja mindazokat a módszereket, amelyek az Azure Search-indexek lekérdezéséhez szükségesek.

## <a name="construct-searchparameters"></a>Szerkezet SearchParameters
A .NET SDK használatával történő keresés ugyanolyan egyszerűen végrehajtható, mint a `Documents.Search` módszer meghívása a következőn: `SearchIndexClient`. Ezen módszer néhány paramétert használ, ide értve a keresett szöveget, a lekérdezés további finomításához használható `SearchParameters` objektummal együtt.

### <a name="types-of-queries"></a>A lekérdezések típusai
Az itt használt két fő [lekérdezési típus](search-query-overview.md#types-of-queries): `search` és `filter`. A `search` lekérdezés egy vagy több kifejezésre keres rá az index összes *searchable* (kereshető) mezőjében. A `filter` lekérdezés egy logikai kifejezés kiértékelését végzi el az index összes *filterable* (szűrhető) mezőjén. A keresések és a szűrések együtt vagy külön-külön is alkalmazhatók.

A keresések és a szűrések egyaránt a `Documents.Search` módszer használatával vannak végrehajtva. Keresési lekérdezések a `searchText` paraméterben, szűrőkifejezések pedig a `SearchParameters` osztály `Filter` tulajdonságában adhatóak át. A keresés nélküli szűrés végrehajtásához a `searchText` paraméter számára a `"*"` kifejezést adja át. A szűrés nélküli keresés végrehajtásához ne állítsa be a `Filter` tulajdonságot, vagy egyáltalán ne adja át azt egy `SearchParameters`-példányban.

### <a name="example-queries"></a>Példa a lekérdezésekre
Az alábbi mintakód néhány különböző módszert a meghatározott "hotels" index lekérdezése mutat be [az Azure Search-index létrehozása C# ](search-create-index-dotnet.md#DefineIndex). Vegye figyelembe, hogy a keresési eredményekkel visszaadott dokumentumok példánya a `Hotel` osztály, amely lett definiálva [importálja az adatokat az Azure Search-index C# ](search-import-data-dotnet.md#construct-indexbatch). Ez a mintakód a `WriteDocuments` módszer használatával jeleníti meg a keresési eredményeket a konzolon. Ezt a módszert a következő szakasz ismerteti.

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

Itt látható, az eredmények kinézni a lekérdezésekhez az előző szakaszban, feltéve, hogy a "hotels" index mintaadataival van feltöltve:

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

A fenti mintakód a keresési eredményeket a konzolon jeleníti meg. A keresési eredményeket hasonlóképpen kell megjeleníteni a saját alkalmazásában is. Egy példa bemutatja, hogyan jelennek meg a keresési eredmények egy ASP.NET MVC-alapú webes alkalmazás: a [DotNetSample projekt](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample) a Githubon.

## <a name="next-steps"></a>További lépések

Ha még nem tette meg, tekintse át a mintakódot az [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) a Githubon, az [használata az Azure Search .NET-alkalmazásból](search-howto-dotnet-sdk.md) részletesebb leírását a mintakódot. 