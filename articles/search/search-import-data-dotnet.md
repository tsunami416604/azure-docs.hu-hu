---
title: Adatok betöltése az Azure Search-index a C# (.NET SDK-t) – Azure Search
description: Ismerje meg, hogyan tölthet fel adatokat az Azure Search-kereshető teljes szöveges index C# példa kód és a .NET SDK-t.
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/20/2019
ms.openlocfilehash: d2d54d1425bbb67a3f5ba1b6081a9f74ff87f4d6
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286907"
---
# <a name="quickstart-2---load-data-to-an-azure-search-index-using-c"></a>Gyors útmutató: 2 – adatok betöltése az egy Azure Search-indexeket aC#

Ez a cikk bemutatja, hogyan importálhat adatokat az [Azure Search-index](search-what-is-an-index.md) használatával C# és a [.NET SDK-val](https://aka.ms/search-sdk). Dokumentum elküldése az indexbe történő kapcsolódással fejeződik be a feladatok végrehajtására:

> [!div class="checklist"]
> * Hozzon létre egy [ `SearchIndexClient` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) kapcsolódni a keresési index objektum.
> * Hozzon létre egy [ `IndexBatch` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) objektum, amely tartalmazza a dokumentumok hozzáadni, módosítani vagy törölni.
> * Hívja a `Documents.Index` metódust `SearchIndexClient` dokumentumok feltöltése az indexbe.

## <a name="prerequisites"></a>Előfeltételek

[Az Azure Search-index létrehozása](search-create-index-dotnet.md) és a egy `SearchServiceClient` , ahogyan az az objektum ["-ügyfél létrehozása"](search-create-index-dotnet.md#CreateSearchServiceClient).


## <a name="create-a-client"></a>-Ügyfél létrehozása
Adatok importálása egy példányát kell a `SearchIndexClient` osztály. Ez az osztály, beleértve az létrehozásához több megközelítés közül a `SearchServiceClient` példányt, amely már létre van hozva. 

Ahogy az alábbi példa azt mutatja be, használhatja a `SearchServiceClient` példány, és hívja a `Indexes.GetClient` metódust. Ez a kódrészlet beszerez egy `SearchIndexClient` a "hotels" nevű indexhez egy `SearchServiceClient` nevű `serviceClient`.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

A `SearchIndexClient` rendelkezik egy `Documents` tulajdonsággal. Ez a tulajdonság biztosítja mindazokat a módszereket, amelyek a dokumentumok indexben történő hozzáadásához, módosításához, törléséhez vagy lekérdezéséhez szükségesek.

> [!NOTE]
> Egy tipikus keresőalkalmazást, a lekérdezési és indexelési van kezelve. Miközben `Indexes.GetClient` akkor hasznos, mert az objektumok, például felhasználhatja `SearchCredentials`, egy robusztusabb megközelítés magában foglalja a létrehozása a `SearchIndexClient` közvetlenül, hogy egy rendszergazdai kulcs helyett lekérdezési kulcs adhat át. Ez a gyakorlat összhangban az [elvét](https://en.wikipedia.org/wiki/Principle_of_least_privilege) , és segít az alkalmazás még biztonságosabbá teheti. Fog létrehozni egy `SearchIndexClient` a következő gyakorlatból. Kulcsokkal kapcsolatos további információkért lásd: [létrehozása és kezelése az Azure Search szolgáltatás api-kulcsainak](search-security-api-keys.md).
> 
> 

<a name="construct-indexbatch"></a>

## <a name="construct-indexbatch"></a>IndexBatch létrehozása

Adatimportálás a .NET SDK használatával, az adatok becsomagolhatja egy `IndexBatch` objektum. Egy `IndexBatch` magában foglalja egy gyűjteményét `IndexAction` objektumok, amelyek mindegyike tartalmaz egy olyan dokumentumot vagy tulajdonságot, amely az Azure Search (feltöltés, egyesítés, törlése és mergeOrUpload) által az adott dokumentumon végrehajtandó műveletet. Az indexelő műveleteivel kapcsolatos további információkért lásd: [műveletek indexelés: feltöltés, egyesítés, mergeOrUpload, törölje](search-what-is-data-import.md#indexing-actions).

Ha ismeri a dokumentumon végrehajtani kívánt műveletek, készen áll hozhatnak létre a `IndexBatch`. Jelen példa egy köteg különböző műveletekkel történő létrehozását mutatja be. A példában egy nevű egyedi osztályt `Hotel` , amely a "hotels" index dokumentum leképezésére.

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                BaseRate = 199.0,
                Description = "Best hotel in town",
                DescriptionFr = "Meilleur hôtel en ville",
                HotelName = "Fancy Stay",
                Category = "Luxury",
                Tags = new[] { "pool", "view", "wifi", "concierge" },
                ParkingIncluded = false,
                SmokingAllowed = false,
                LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                Rating = 5,
                Location = GeographyPoint.Create(47.678581, -122.131577)
            }),
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                BaseRate = 79.99,
                Description = "Cheapest hotel in town",
                DescriptionFr = "Hôtel le moins cher en ville",
                HotelName = "Roach Motel",
                Category = "Budget",
                Tags = new[] { "motel", "budget" },
                ParkingIncluded = true,
                SmokingAllowed = true,
                LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                Rating = 1,
                Location = GeographyPoint.Create(49.678581, -122.131577)
            }),
        IndexAction.MergeOrUpload(
            new Hotel()
            {
                HotelId = "3",
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

Ebben az esetben az `Upload`, `MergeOrUpload` és `Delete` keresési műveleteket használjuk, az `IndexAction` osztályban meghívott módszerek által megadott módon.

Jelen példában feltételezzük, hogy a „hotels” index már fel van töltve dokumentumokkal. Figyelje meg, hogy az `MergeOrUpload` használatakor nem volt szükséges megadni a dokumentumban szereplő összes mezőt, illetve hogy a `Delete` használatakor kizárólag a dokumentumkulcsot (`HotelId`) adtuk meg.

Vegye figyelembe azt is, hogy egyetlen indexelési kérésbe legfeljebb 1000 dokumentumot foglalhat.

> [!NOTE]
> Jelen példában a különböző dokumentumokon eltérő műveleteket alkalmazunk. Ha a kötegben szereplő össze dokumentumra ugyanazokat a műveleteket szeretné alkalmazni, az `IndexBatch.New` meghívása helyett használhatja az `IndexBatch` egyéb statikus módszereit is. Kötegeket például az `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` vagy `IndexBatch.Delete` művelet meghívásával is létrehozhat. Ezen módszerek `IndexAction` objektumok helyett dokumentumok gyűjteményét (jelen példában a `Hotel` típusú objektumokat) használják.
> 
> 

## <a name="call-documentsindex"></a>Hívás Documents.Index
Most, hogy már rendelkezik egy inicializált `IndexBatch` objektummal, a `SearchIndexClient` objektum `Documents.Index` műveletének meghívásával elküldheti azt az indexbe. A következő példában az `Index` meghívásának módja, valamint a végrehajtandó további lépések láthatók:

```csharp
try
{
    indexClient.Documents.Index(batch);
}
catch (IndexBatchException e)
{
    // Sometimes when your Search service is under load, indexing will fail for some of the documents in
    // the batch. Depending on your application, you can take compensating actions like delaying and
    // retrying. For this simple demo, we just log the failed document keys and continue.
    Console.WriteLine(
        "Failed to index some of the documents: {0}",
        String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
}

Console.WriteLine("Waiting for documents to be indexed...\n");
Thread.Sleep(2000);
```

Figyelje meg az `Index` metódus meghívását körülvevő `try`/`catch` elemet. A „catch” blokk az indexelés egy fontos hibaesetét kezeli. Ha az Azure Search-szolgáltatásnak nem sikerül indexelnie a kötegben szereplő fájlok valamelyikét, a `Documents.Index` rendszer `IndexBatchException` választ ad. Ez akkor történhet meg, ha olyankor végzi a dokumentumok indexelését, amikor a szolgáltatás nagy terhelés alatt áll. **Javasoljuk, hogy a kódban explicit módon kezelje ezt az esetet.** Azon dokumentumok esetében, ahol az indexelés meghiúsult, elhalaszthatja azt, majd később újra megpróbálkozhat az indexeléssel, vagy a mintának megfelelően naplózhatja azt, és folytathatja a munkáját, esetleg – az alkalmazás adatkonzisztencia-követelményeitől függően – más műveletbe kezdhet.

Végül a példában szereplő kód két másodperces késleltetést hajt végre. Az Azure Search-szolgáltatásban az indexelés aszinkron módon történik, így a mintaalkalmazásnak egy rövid ideig várnia kell, amíg a rendszer meggyőződik arról, hogy a dokumentum kereshető. Ilyen mértékű késleltetésre kizárólag demók, tesztek és mintaalkalmazások esetében van szükség.

Dokumentumfeldolgozást kapcsolatos további információkért lásd: ["Hogyan a .NET SDK bizonylatok kezeli"](search-howto-dotnet-sdk.md#how-dotnet-handles-documents).


## <a name="next-steps"></a>További lépések
Után az Azure Search-index feltöltéséhez, a következő lépésben dokumentumok keresése a lekérdezések ad ki. 

> [!div class="nextstepaction"]
> [Az Azure Search-index lekérdezéseC#](search-query-dotnet.md)
