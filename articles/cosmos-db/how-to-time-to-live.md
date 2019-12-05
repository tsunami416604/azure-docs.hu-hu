---
title: Megtudhatja, hogyan konfigurálhatja és kezelheti az élettartamot Azure Cosmos DB
description: Megtudhatja, hogyan konfigurálhatja és kezelheti az élettartamot Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 8bae2db08a5279225eba7492c63706dc9fc3df02
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74819516"
---
# <a name="configure-time-to-live-in-azure-cosmos-db"></a>Állítsa be az élettartamot Azure Cosmos DB

A Azure Cosmos DBban beállíthatja, hogy az élettartam (TTL) a tároló szintjén legyen beállítva, vagy egy elem szintjén felülbírálhatja a tároló beállítása után. A tárolók ÉLETTARTAMát Azure Portal vagy a nyelvfüggő SDK-k használatával konfigurálhatja. Az elemszintű TTL-felülbírálások az SDK-k használatával konfigurálhatók.

## <a name="enable-time-to-live-on-a-container-using-azure-portal"></a>Az Azure Portal-t használó tárolón való élő idő engedélyezése

A következő lépésekkel engedélyezheti, hogy a lejárati idő nélkül egy tárolón lehessen élni. Ezzel a beállítással engedélyezheti, hogy az élettartam felülbírálható legyen az elemszintű szinten. Az ÉLETTARTAMot úgy is beállíthatja, hogy nem nulla értéket adjon meg másodpercben.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

2. Hozzon létre egy új Azure Cosmos-fiókot, vagy válasszon ki egy meglévő fiókot.

3. Nyissa meg a **adatkezelő** ablaktáblát.

4. Válasszon ki egy meglévő tárolót, bontsa ki, majd módosítsa a következő értékeket:

   * Nyissa meg a **méretezési & beállítások** ablakát.
   * A keresés, **élettartam** **beállítása** területen.
   * Válassza **a be (nincs alapértelmezett)** lehetőséget, vagy válassza **a** be lehetőséget, és állítsa be a TTL értéket
   * Kattintson a **Mentés** gombra a módosítások mentéséhez.

   ![Állítsa be az élettartamot Azure Portal](./media/how-to-time-to-live/how-to-time-to-live-portal.png)

* Ha a DefaultTimeToLive értéke null, akkor az élettartama ki van kapcsolva
* Ha a DefaultTimeToLive értéke-1, akkor az élettartam beállítás értéke (nincs alapértelmezett)
* Ha a DefaultTimeToLive bármely más int értékkel rendelkezik (kivéve a 0 értéket), az élettartam beállítás értéke

## <a name="enable-time-to-live-on-a-container-using-azure-cli-or-powershell"></a>Az Azure CLI vagy a PowerShell használatával engedélyezheti az időt egy tárolón való működésre

TTL létrehozása vagy engedélyezése tárolóban lásd:

* [TTL-tároló létrehozása az Azure CLI használatával](manage-with-cli.md#create-a-container-with-ttl)
* [Hozzon létre egy tárolót az ÉLETTARTAMmal a PowerShell használatával](manage-with-powershell.md#create-container-unique-key-ttl)

## <a name="enable-time-to-live-on-a-container-using-sdk"></a>Az SDK-t használó tárolón való élő idő engedélyezése

### <a id="dotnet-enable-noexpiry"></a>.NET SDK v2 (Microsoft. Azure. DocumentDB)

```csharp
// Create a new container with TTL enabled and without any expiration value
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = -1; //(never expire by default)

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition);
```

### <a id="dotnet-enable-noexpiry"></a>.NET SDK v3 (Microsoft. Azure. Cosmos)

```csharp
// Create a new container with TTL enabled and without any expiration value
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = -1 //(never expire by default)
});
```

## <a name="set-time-to-live-on-a-container-using-sdk"></a>Időtartam beállítása egy tárolón az SDK használatával

Ha meg szeretné adni az időt egy tárolón való működéshez, meg kell adnia egy nullától eltérő pozitív számot, amely a másodpercben megadott időtartamot jelzi. A beállított TTL-érték alapján a tárolóban lévő összes elem törlődik a `_ts` elem utolsó módosításának időbélyegzője után.

### <a id="dotnet-enable-withexpiry"></a>.NET SDK v2 (Microsoft. Azure. DocumentDB)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24 // expire all documents after 90 days

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition;
```

### <a id="dotnet-enable-withexpiry"></a>.NET SDK v3 (Microsoft. Azure. Cosmos)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days
});
```

### <a id="nodejs-enable-withexpiry"></a>NodeJS SDK

```javascript
const containerDefinition = {
          id: "sample container1",
        };

async function createcontainerWithTTL(db: Database, containerDefinition: ContainerDefinition, collId: any, defaultTtl: number) {
      containerDefinition.id = collId;
      containerDefinition.defaultTtl = defaultTtl;
      await db.containers.create(containerDefinition);
}
```

## <a name="set-time-to-live-on-an-item"></a>Élettartam beállítása egy elemen

A tárolók alapértelmezett élettartamának beállítása mellett beállíthatja, hogy az egyes elemek Mikor éljünk. Az elemszintű élettartam beállítása felülbírálja az adott tárolóban lévő elemek alapértelmezett ÉLETTARTAMát.

* Egy elem ÉLETTARTAMának beállításához meg kell adnia egy nullától eltérő pozitív számot, amely azt jelzi, hogy az elem hány másodperc múlva jár le az elem utolsó módosításának időbélyegzője után `_ts`.

* Ha az elem nem rendelkezik TTL mezővel, akkor alapértelmezés szerint a tárolóra beállított TTL-érték lesz érvényes az elemre.

* Ha az élettartam le van tiltva a tároló szintjén, a rendszer figyelmen kívül hagyja az elem TTL mezőjét, amíg az élettartam újra engedélyezve nem lesz a tárolón.

### <a id="portal-set-ttl-item"></a>Azure Portal

A következő lépésekkel engedélyezheti az élettartamot egy elemen:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

2. Hozzon létre egy új Azure Cosmos-fiókot, vagy válasszon ki egy meglévő fiókot.

3. Nyissa meg a **adatkezelő** ablaktáblát.

4. Válasszon ki egy meglévő tárolót, bontsa ki, majd módosítsa a következő értékeket:

   * Nyissa meg a **méretezési & beállítások** ablakát.
   * A keresés, **élettartam** **beállítása** területen.
   * Válassza **a be lehetőséget (nincs alapértelmezett)** , vagy válassza **a** be lehetőséget, és állítsa be a TTL értéket. 
   * Kattintson a **Mentés** gombra a módosítások mentéséhez.

5. Ezután Navigáljon arra az elemre, amelyre az élettartamot szeretné beállítani, adja hozzá a `ttl` tulajdonságot, és válassza a **frissítés**lehetőséget. 

   ```json
   {
    "id": "1",
    "_rid": "Jic9ANWdO-EFAAAAAAAAAA==",
    "_self": "dbs/Jic9AA==/colls/Jic9ANWdO-E=/docs/Jic9ANWdO-EFAAAAAAAAAA==/",
    "_etag": "\"0d00b23f-0000-0000-0000-5c7712e80000\"",
    "_attachments": "attachments/",
    "ttl": 10,
    "_ts": 1551307496
   }
   ```

### <a id="dotnet-set-ttl-item"></a>.NET SDK (bármilyen)

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
    public int? ttl { get; set; }

    //...
}
// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder
{
    Id = "SO05",
    CustomerId = "CO18009186470",
    ttl = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days
};
```

### <a id="nodejs-set-ttl-item"></a>NodeJS SDK

```javascript
const itemDefinition = {
          id: "doc",
          name: "sample Item",
          key: "value",
          ttl: 2
        };
```

## <a name="reset-time-to-live"></a>Élettartam visszaállítása

Az elemre vonatkozó írási vagy frissítési művelet végrehajtásával visszaállíthatja az élettartamot egy adott elemen. Az írási vagy frissítési művelet a `_ts` az aktuális időpontra állítja be, a lejárati idő ÉLETTARTAMa pedig újra elindul. Ha módosítani szeretné egy elem ÉLETTARTAMát, akkor a mezőt ugyanúgy frissítheti, mint bármely más mezőt.

### <a id="dotnet-extend-ttl-item"></a>.NET SDK v2 (Microsoft. Azure. DocumentDB)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = 60 * 30 * 30; // update time to live
response = await client.ReplaceDocumentAsync(readDocument);
```

### <a id="dotnet-extend-ttl-item"></a>.NET SDK v3 (Microsoft. Azure. Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = 60 * 30 * 30; // update time to live
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```

## <a name="turn-off-time-to-live"></a>Az élettartam kikapcsolása

Ha az élettartam értéke be van állítva egy elemre, és már nem szeretné, hogy az elem lejárjon, akkor lekérheti az adott tételt, eltávolíthatja a TTL mezőt, és lecserélheti a kiszolgálón lévő elemre. Ha a TTL mező el lett távolítva az elemből, a rendszer a tárolóhoz rendelt alapértelmezett TTL-értéket alkalmazza az elemre. Állítsa a TTL értéket a-1 értékre, hogy megakadályozza egy elem lejáratát, és nem örökli a TTL értékét a tárolóból.

### <a id="dotnet-turn-off-ttl-item"></a>.NET SDK v2 (Microsoft. Azure. DocumentDB)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = null; // inherit the default TTL of the container

response = await client.ReplaceDocumentAsync(readDocument);
```

### <a id="dotnet-turn-off-ttl-item"></a>.NET SDK v3 (Microsoft. Azure. Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = null; // inherit the default TTL of the container
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```

## <a name="disable-time-to-live"></a>Élettartam letiltása

Ha le szeretné tiltani az időt egy tárolón való működésre, és leállítja a háttérben futó folyamatot a lejárt elemek ellenőrzésével, a tárolóban lévő `DefaultTimeToLive` tulajdonságot törölni kell. A tulajdonság törlése eltér az-1 értéktől. Ha a-1 értékre állítja, a tárolóhoz hozzáadott új elemek örökre életbe kerülnek, de felülbírálhatja ezt az értéket a tároló adott elemein. Ha eltávolítja a TTL tulajdonságot a tárolóból, az elemek soha nem járnak le, még akkor sem, ha az előző alapértelmezett TTL-értéket explicit módon felülbírálták.

### <a id="dotnet-disable-ttl"></a>.NET SDK v2 (Microsoft. Azure. DocumentDB)

```csharp
// Get the container, update DefaultTimeToLive to null
DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
// Disable TTL
collection.DefaultTimeToLive = null;
await client.ReplaceDocumentCollectionAsync(collection);
```

### <a id="dotnet-disable-ttl"></a>.NET SDK v3 (Microsoft. Azure. Cosmos)

```csharp
// Get the container, update DefaultTimeToLive to null
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Disable TTL
containerResponse.Resource.DefaultTimeToLive = null;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

## <a name="next-steps"></a>Következő lépések

További információ a következő cikkben található idő megadásáról:

* [Élettartam](time-to-live.md)
