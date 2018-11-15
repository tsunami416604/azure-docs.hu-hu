---
title: Ismerje meg, hogyan konfigurálhatja és kezelheti az Azure Cosmos DB élettartama
description: Ismerje meg, hogyan konfigurálhatja és kezelheti az Azure Cosmos DB élettartama
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/14/2018
ms.author: mjbrown
ms.openlocfilehash: 25824b9959a4fef536ca9fe247f29fc9aaece1f7
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51626464"
---
# <a name="how-to-configure-time-to-live-in-azure-cosmos-db"></a>Az Azure Cosmos DB élettartam konfigurálása

Az Azure Cosmos DB élettartamot (TTL) konfigurálása a tároló szintjén is választja, vagy felülírhatja, az elemek szintjén a tároló beállítását követően. Élettartam egy tárolót az Azure portal vagy a megfelelő nyelvfüggő SDK-k használatával konfigurálható. Elem szintű TTL felülbírálások az SDK-k használatával konfigurálható.

## <a name="enable-time-to-live-on-a-container-using-azure-portal"></a>Az Azure portal használatával egy tárolón élettartam engedélyezése

Használja az alábbi lépéseket egy tárolón lejárat nélkül élettartam engedélyezéséhez. Ezzel a TTL bírálható felül az elemek szintjén való engedélyezéséhez. Az élettartam másodpercig egy nem nulla érték megadásával is beállíthatja.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Hozzon létre egy új Azure Cosmos-fiókot, vagy válasszon ki egy meglévő fiókot.

3. Nyissa meg a **adatkezelő** ablaktáblán.

4. Jelöljön ki egy meglévő tárolót, bontsa ki azt, és módosítsa a következő értékeket:

   * Nyissa meg a **méretezés és beállítások** ablak.
   * A **beállítás** megkereséséhez **élettartama**.
   * Válassza ki **bekapcsolva (alapértelmezett) nincs** , vagy válasszon **a** és a TTL értékének beállítása
   * Kattintson a **Mentés** gombra a módosítások mentéséhez.

   ![Az Azure Portalon élettartam konfigurálása](./media/how-to-time-to-live/how-to-time-to-live-portal.png)

## <a name="enable-time-to-live-on-a-container-using-sdk"></a>Egy tároló, SDK-val élettartam engedélyezése

### <a id="dotnet-enable-noexpiry"></a>.NET SDK

```csharp
// Create a new collection with TTL enabled and without any expiration value
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = -1; //(never expire by default)

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition,
    new RequestOptions { OfferThroughput = 20000 });
```

## <a name="set-time-to-live-on-a-container-using-sdk"></a>Live SDK-val egy tárolón idő beállítása

### <a id="dotnet-enable-withexpiry"></a>.NET SDK

A tároló élő idejének beállítása, meg kell adnia a nullától eltérő pozitív szám, amely azt jelzi, hogy az adott időszakban másodpercek alatt. A konfigurált TTL-érték alapján, az összes elem a tároló után az utolsó elem időbélyegző módosításának `_ts` törlődnek.

```csharp
// Create a new collection with TTL enabled and a 90 day expiration
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition,
    new RequestOptions { OfferThroughput = 20000 });
```

## <a name="set-time-to-live-on-an-item"></a>Az elem élettartam beállítása

Egy alapértelmezett élettartama egy tárolón beállításnál mellett beállíthatja egy elem élettartamot. Az elemek szintjén élettartam beállítás felülbírálja az alapértelmezett élettartam az elem a tárolóban.

* Az élettartam beállítani egy elem, meg kell adnia egy nullától eltérő pozitív szám, amely megadja, hogy az az időtartam másodpercben, az elem lejár, miután az utolsó elem időbélyegző módosításának `_ts`.

* Ha az elem nem rendelkezik egy TTL-mezőt, majd alapértelmezés szerint az Élettartamon, állítsa be a tárolóba érvényesek az elemet.

* Ha TTL le van tiltva, a tároló szintjén, a cikk a TTL mező figyelmen kívül mindaddig, amíg az élettartam (TTL) újra engedélyezni a tárolón.

### <a id="dotnet-set-ttl-item"></a>.NET SDK

```csharp
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }
    [JsonProperty(PropertyName="cid")]
    public string CustomerId { get; set; }
    // used to set expiration policy
    [JsonProperty(PropertyName = "ttl", NullValueHandling = NullValueHandling.Ignore)]
    public int? TimeToLive { get; set; }

    //...
}
// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder
{
    Id = "SO05",
    CustomerId = "CO18009186470",
    TimeToLive = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days
};
```

## <a name="reset-time-to-live"></a>Élettartam alaphelyzetbe állítása

Alaphelyzetbe állíthatja a írási végrehajtásával egy elemet az élő vagy az elemre a frissítés ideje. Az írási vagy frissítési műveletben fogja beállítani a `_ts` az aktuális idő lejár a cikk az élettartam újra megkezdődik. Ha szeretné módosítani az élettartam egy elem, frissítheti a mező, ugyanúgy, mint bármely más mező frissíti.

### <a id="dotnet-extend-ttl-item"></a>.NET SDK

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.TimeToLive = 60 * 30 * 30; // update time to live
response = await client.ReplaceDocumentAsync(readDocument);
```

## <a name="turn-off-time-to-live"></a>Kapcsolja ki az élettartam

Ha élettartam elem lett beállítva, és már nem szeretné, hogy a cikk hamarosan lejár, majd is lekérni az elemet, távolítsa el az élettartam mezőt, és cserélje le a cikk a kiszolgálón. Ha a TTL mező eltávolítják az elemet, az alapértelmezett TTL-érték a tárolóhoz rendelt alkalmazva az elemet. Állítsa be az élettartam értéke-1, megakadályozhatja, hogy lejárjanak egy elemet, és az élettartam értéke nem örököl a tárolót.

### <a id="dotnet-turn-off-ttl-item"></a>.NET SDK

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.TimeToLive = null; // inherit the default TTL of the collection

response = await client.ReplaceDocumentAsync(readDocument);
```

## <a name="disable-time-to-live"></a>Élettartam letiltása

A tároló élő, és állítsa le a háttérben futó folyamatot lejárt elemek keresése letiltása a `DefaultTimeToLive` a tárolón a tulajdonság azt törölni kell. Ez a tulajdonság törlése nem azonos a -1 értékre állítaná. -1 értéket állítsa be, amikor a tároló új elemek megtalálhatók lesznek tartja, azonban felülbírálhatja ezt az értéket az adott elem a tárolóban. A TTL tulajdonsága a tárolóból való eltávolításakor az elemek lejár, akkor sem, ha azok explicit módon kell felülbírálni, az előző alapértelmezett TTL-érték.

### <a id="dotnet-disable-ttl"></a>.NET SDK

```csharp
// Get the collection, update DefaultTimeToLive to null
DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
// Disable TTL
collection.DefaultTimeToLive = null;
await client.ReplaceDocumentCollectionAsync(collection);
```

## <a name="next-steps"></a>További lépések

További információ a következő cikkben élettartam:

* [élettartam](time-to-live.md)