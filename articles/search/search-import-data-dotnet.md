---
title: "Adatok feltöltése (.NET – Azure Search) | Microsoft Docs"
description: "Megismerkedhet az adatfeltöltéssel az Azure Search szolgáltatás indexébe, a .NET SDK használatával."
services: search
documentationcenter: 
author: brjohnstmsft
manager: jhubbard
editor: 
tags: 
ms.assetid: 0e0e7e7b-7178-4c26-95c6-2fd1e8015aca
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 01/13/2017
ms.author: brjohnst
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: bdd952869143c6ca6374bb9264db5bcba1f32b50
ms.contentlocale: hu-hu
ms.lasthandoff: 07/26/2017

---
# <a name="upload-data-to-azure-search-using-the-net-sdk"></a>Adatfeltöltés az Azure Search szolgáltatásba, a .NET SDK használatával
> [!div class="op_single_selector"]
> * [Áttekintés](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

Jelen cikk bemutatja, hogyan lehet adatokat importálni az Azure Search-indexbe az [Azure Search .NET SDK](https://aka.ms/search-sdk) használatával.

A bemutató elindítása előtt [létre kell hoznia egy Azure Search-indexet](search-what-is-an-index.md). A jelen cikk azt feltételezi, hogy már létrehozott egy `SearchServiceClient` objektumot, az [Azure Search-index létrehozása .NET SDK használatával](search-create-index-dotnet.md#CreateSearchServiceClient) részben megadott módon.

> [!NOTE]
> A cikkben szereplő összes példakód C# nyelven van megírva. A teljes forráskódot a [GitHub](http://aka.ms/search-dotnet-howto) webhelyén találja. Az [Azure Search .NET SDK](search-howto-dotnet-sdk.md) leírásában részletesebb útmutatást kaphat a példakóddal kapcsolatban.

A dokumentumok .NET SDK használatával az indexbe történő küldéséhez a következőket kell tennie:

1. A Search-indexhez történő csatlakozáshoz hozzon létre egy `SearchIndexClient` objektumot.
2. Hozzon létre egy olyan `IndexBatch` objektumot, amely tartalmazza a hozzáadni, módosítani vagy törölni kívánt dokumentumokat.
3. Hívja meg a `SearchIndexClient` `Documents.Index` módszerét az `IndexBatch` Search-indexbe történő elküldéséhez.

## <a name="create-an-instance-of-the-searchindexclient-class"></a>A SearchIndexClient osztály egy példányának létrehozása
Az adatok Azure Search .NET SDK használatával az indexbe történő importálásához létre kell hoznia a `SearchIndexClient` osztály egy példányát. A példány létrehozását saját maga is elvégezheti, ha azonban már rendelkezik egy `SearchServiceClient`-példánnyal, egyszerűbb, ha meghívja annak `Indexes.GetClient` módszerét. Itt például azt láthatja, hogyan történik egy `SearchIndexClient` beszerzése a „hotels” nevű indexhez egy `serviceClient` nevű `SearchServiceClient` osztályból:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [!NOTE]
> A keresőalkalmazásokban az indexkezelést és -feltöltést általában a keresési lekérdezésektől eltérő elem végzi. A `Indexes.GetClient` kényelmes megoldás az index feltöltésére, mivel így nem szükséges újabb `SearchCredentials` objektumot biztosítania. Ezt azon rendszergazdai kulcs átadásával hajtja végre, amelyet a `SearchServiceClient` elemnek az új `SearchIndexClient` objektumban történő létrehozásakor használt. A lekérdezéseket végrehajtó alkalmazás részeként azonban jobb megoldás a `SearchIndexClient` közvetlen létrehozása, így az egy rendszergazdai kulcs helyett lekérdezési kulcs formájában adható át. Ez megfelel a [legalacsonyabb jogosultsági szint elvének](https://en.wikipedia.org/wiki/Principle_of_least_privilege), és segítségével alkalmazása biztonságosabbá tehető. További információk az adminisztrációs és a lekérdezési kulcsokról: [Azure Search REST API-referencia](https://docs.microsoft.com/rest/api/searchservice/).
> 
> 

A `SearchIndexClient` rendelkezik egy `Documents` tulajdonsággal. Ez a tulajdonság biztosítja mindazokat a módszereket, amelyek a dokumentumok indexben történő hozzáadásához, módosításához, törléséhez vagy lekérdezéséhez szükségesek.

## <a name="decide-which-indexing-action-to-use"></a>A használni kívánt indexelési művelet megadása
Az adatok .NET SDK használatával történő importálásához azokat egy `IndexBatch` objektumba kell csomagolnia. Az `IndexBatch` olyan `IndexAction` objektumok gyűjteményét foglalja magában, amelyek mindegyike tartalmaz egy olyan dokumentumot vagy tulajdonságot, amely az Azure Search által az adott dokumentumon végrehajtandó műveletet (például a feltöltést, egyesítést vagy törlést) adja meg. Attól függően, hogy az alábbi műveletek közül melyiket választja ki, az egyes dokumentumok esetében csak bizonyos mezők lesznek kötelezően megjelenítendők:

| Műveletek | Leírás | Az egyes dokumentumok kötelező mezői | Megjegyzések |
| --- | --- | --- | --- |
| `Upload` |Az `Upload` művelet működése hasonló az „upsert” (frissítés/beszúrás) műveletéhez, ahol a rendszer az új dokumentumot beilleszti, ha pedig már létező dokumentumról van szó, akkor frissíti/kicseréli azt. |billentyű, továbbá a meghatározni kívánt egyéb mezők |Létező dokumentum frissítése/cseréje esetén a kérésben nem megadott mezők beállítása a következő lesz: `null`. Ez történik abban az esetben is, ha a mező korábban nem null értékre lett beállítva. |
| `Merge` |Egy meglévő dokumentumot frissít a megadott mezőkkel. Ha a dokumentum nem található az indexben, az egyesítés meg fog hiúsulni. |billentyű, továbbá a meghatározni kívánt egyéb mezők |A rendszer az egyesítési művelet során megadott mezőkre cseréli a dokumentum meglévő mezőit. Ez `DataType.Collection(DataType.String)` típusú mezőket tartalmaz. Ha például a dokumentum egy `["budget"]` értékű `tags` mezőt tartalmaz, és egyesítést hajt végre a `tags` mező `["economy", "pool"]` értékével, a `tags` mező végső értéke `["economy", "pool"]` lesz. Nem pedig a következő lesz: `["budget", "economy", "pool"]`. |
| `MergeOrUpload` |Ha az indexben már létezik az adott kulccsal ellátott dokumentum, ezen művelet viselkedése hasonló lesz a `Merge` műveletéhez. Ha nem létezik ilyen dokumentum, a művelet viselkedése az `Upload` új dokumentum esetében mutatott viselkedésének fog megfelelni. |billentyű, továbbá a meghatározni kívánt egyéb mezők |- |
| `Delete` |Eltávolítja a megadott dokumentumot az indexből. |csak billentyű |A rendszer figyelmen kívül hagyja a kulcsmezőn kívül megadott mezőket. Ha egyetlen mezőt kíván eltávolítani a dokumentumból, e helyett használja a `Merge` műveletet, és a mező számára explicit módon adja meg a null értéket. |

Megadhatja, hogy milyen műveletet kíván az `IndexBatch` és `IndexAction` osztály különböző statikus módszereivel használni, a következő szakaszban látható módon.

## <a name="construct-your-indexbatch"></a>Az IndexBatch létrehozása
A dokumentumon végrehajtani kívánt műveletek tudatában most már készen áll az `IndexBatch` létrehozására. Jelen példa egy köteg különböző műveletekkel történő létrehozását mutatja be. Vegye figyelembe, hogy ez a példa egy `Hotel` nevű egyedi osztályt használ a „hotels” indexben található dokumentum leképezésére.

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

## <a name="import-data-to-the-index"></a>Adatok importálása az indexre
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

<a name="HotelClass"></a>

### <a name="how-the-net-sdk-handles-documents"></a>A .NET SDK dokumentumkezelési módszere
Megfordulhat a fejében, hogy miként képes az Azure Search .NET SDK felhasználó által meghatározott `Hotel` osztályhoz hasonló példányok feltöltésére az indexbe. Ennek megválaszolásához vizsgáljuk meg a `Hotel` osztályt, amely az [Azure Search-index létrehozása .NET SDK használatával](search-create-index-dotnet.md#DefineIndex) részben meghatározott indexsémára végez leképezést:

```csharp
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [Key]
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

    // ToString() method omitted for brevity...
}
```

Az első szembetűnő dolog, hogy a `Hotel` minden egyes nyilvános tulajdonsága az indexdefiníció egy-egy mezőjének felel meg, egy lényeges különbséggel: a mezők neve minden esetben kisbetűvel, míg a `Hotel` nyilvános tulajdonságainak neve nagybetűvel kezdődik. Gyakran kerül sor erre olyan adatkötést végző .NET-alkalmazások esetében, ahol a célséma vezérlése az alkalmazás fejlesztőjének hatáskörén kívül esik. A .NET elnevezési irányelveinek megsértése helyett (a tulajdonságnevek kisbetűs megadásával), utasíthatja az SDK-t a tulajdonságnevek automatikus kisbetűs leképezésére a `[SerializePropertyNamesAsCamelCase]` attribútummal.

> [!NOTE]
> Az Azure Search .NET SDK a [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) könyvtárat használja az egyéni modellek JSON-ból és JSON-ba történő szerializálására és deszerializálására. A szerializálás szükség szerint testre szabható. További információk: [Egyéni szerializálás a JSON.NET használatával](search-howto-dotnet-sdk.md#JsonDotNet). Erre mutat példát a fenti mintakódban a `[JsonProperty]` attribútum használata a `DescriptionFr` tulajdonság esetében.
> 
> 

A `Hotel` osztállyal kapcsolatos másik fontos tényező a nyilvános tulajdonságok adattípusa. Az indexdefinícióban a rendszer ezen .NET tulajdonságtípusokat képezi le a nekik megfelelő mezőtípusokra. Például a rendszer a `DataType.String` típusú `Category` szöveges tulajdonságot a `category` mezőbe képezi le. Hasonló típusleképezés történik a `bool?` és `DataType.Boolean`, illetve a `DateTimeOffset?` és `DataType.DateTimeOffset` stb. között is. A típusleképezés vonatkozó szabályainak dokumentációja az [Azure Search .NET SDK-referenciában](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations#Microsoft_Azure_Search_IDocumentsOperations_GetWithHttpMessagesAsync__1_System_String_System_Collections_Generic_IEnumerable_System_String__Microsoft_Azure_Search_Models_SearchRequestOptions_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_), a `Documents.Get` metódusnál található.

A saját osztályok dokumentumokként történő használatának képessége mindkét irányban működik; lekérdezheti a keresési eredményeket is, majd az SDK-val automatikusan deszerializáltathatja azokat a választott típusra, ahogy az a [következő cikkben](search-query-dotnet.md) látható.

> [!NOTE]
> Az Azure Search .NET SDK támogatja a `Document` osztályt használó, dinamikus dokumentumtípusokat is, amely alatt a mezők neveinek értékekre történő kulcs/érték-leképezését értjük. Ez olyan helyzetekben hasznos, ha például a tervezés időpontjában az indexséma még nem ismert, illetve ha az adott modellosztályokhoz történő kötés nehézkes volna. Az SDK-ban lévő összes, dokumentumokkal foglalkozó módszer a `Document` osztállyal kompatibilis túlterhelésekkel rendelkezik, valamint olyan szigorú típusmegadású túlterhelésekkel, amelyek általános típusú paramétert vesznek fel. A jelen cikkben szereplő mintakódban kizárólag az utóbbiakat használjuk.
> 
> 

**Miért használjon nullázható adattípusokat?**

Az Azure Search-indexre leképezést végző, saját modellosztályok létrehozásakor javasoljuk, hogy például a `bool` és `int` értéktípusok tulajdonságainak megadása nullázhatóként történjen (például `bool` helyett `bool?`). Nem nullázható tulajdonság használatakor **garantálnia** kell, hogy az index egyetlen dokumentuma sem tartalmaz az adott mezőben null értéket. Ennek kényszerítéséhez sem az SDK, sem az Azure Search szolgáltatás nem nyújt segítséget.

Ennek nem csupán elméleti jelentősége van: képzeljünk el például egy olyan alkalmazási helyzetet, ahol egy `DataType.Int32` típusú, meglévő indexhez új mezőt kell hozzáadnunk. Az indexdefiníció frissítését követően ehhez a mezőhöz minden dokumentumban null érték tartozik (mivel az Azure Search szolgáltatásban az összes értéktípus nullázható). Ha ezt követően egy modellosztályt úgy alkalmaz, hogy ehhez a mezőhöz nem nullázható `int` tulajdonságot ad meg, a dokumentumok lekérdezésének megkísérlésekor egy ehhez hasonló `JsonSerializationException` választ kap:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Ezért javasoljuk, hogy a modellosztályokban nullázható értéktípusokat használjon.

## <a name="next-steps"></a>Következő lépések
Az Azure Search-index feltöltését követően készen áll a dokumentumkeresési lekérdezések kiadásának elindítására. Részletes információk: [Az Azure Search-index lekérdezése](search-query-overview.md).


