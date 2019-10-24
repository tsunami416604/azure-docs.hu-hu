---
title: Egyéni szinkronizálás megvalósítása az Azure Cosmos DB magasabb rendelkezésre állásának és teljesítményének optimalizálása érdekében
description: Ismerje meg, hogyan valósítható meg az egyéni szinkronizálás, hogy jobbá legyen a magasabb rendelkezésre állás és a teljesítmény a Azure Cosmos DBban.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 1fdd05f8a4757a49414a2a03c8f991a80186ed44
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755045"
---
# <a name="implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>Egyéni szinkronizálás megvalósítása a magasabb rendelkezésre állás és a teljesítmény optimalizálása érdekében

A Azure Cosmos DB [öt jól meghatározott konzisztencia-szintet](consistency-levels.md) kínál a konzisztencia, a teljesítmény és a rendelkezésre állás közötti kompromisszum kiegyensúlyozásához. Az erős konzisztencia segít biztosítani, hogy az adatkezelés szinkronban legyen, és tartósan minden olyan régióban, ahol az Azure Cosmos-fiók elérhető. Ez a konfiguráció biztosítja a legmagasabb szintű tartósságot, de a teljesítmény és a rendelkezésre állás költségeit is tartalmazza. Ha azt szeretné, hogy az alkalmazás a rendelkezésre állás veszélyeztetése nélkül szabályozza vagy kihasználhassa az adattartósságot, az alkalmazás rétegében *Egyéni szinkronizálást* használhat a kívánt tartósság eléréséhez.

Az alábbi ábra vizuálisan ábrázolja az egyéni szinkronizálási modellt:

![Egyéni szinkronizálás](./media/how-to-custom-synchronization/custom-synchronization.png)

Ebben az esetben az Azure Cosmos-tárolót világszerte több régióban replikálja a rendszer, több kontinensen. Az ebben a forgatókönyvben található összes régió esetében erős konzisztencia-használat befolyásolja a teljesítményt. Annak érdekében, hogy az írási késleltetés veszélyeztetése nélkül magasabb szintű adattartósságot biztosítson, az alkalmazás két olyan ügyfelet használhat, amelyek ugyanazt a [munkamenet-tokent](how-to-manage-consistency.md#utilize-session-tokens)használják.

Az első ügyfél írhat adatbevitelt a helyi régióba (például USA nyugati régiója). A második ügyfél (például az USA keleti régiójában) egy olvasási ügyfél, amely a szinkronizálás biztosítására szolgál. Ha a munkamenet-tokent az írási válaszból a következő olvasásra hajtja végre, az olvasás biztosítja az USA keleti régióba való írások szinkronizálását. Azure Cosmos DB biztosítja, hogy az írások legalább egy régióban láthatók legyenek. Ha az eredeti írási régió leáll, a rendszer a regionális kimaradást is megmaradja. Ebben a forgatókönyvben minden írás szinkronizálva van az USA keleti régiójában, így csökkentve a késést az összes régióban való erős konzisztencia alkalmazása során. Több főkiszolgálós forgatókönyv esetén, ahol az írások minden régióban megtörténnek, kiterjesztheti a modellt úgy, hogy párhuzamosan szinkronizáljon több régióval.

## <a name="configure-the-clients"></a>Az ügyfelek konfigurálása

Az alábbi minta egy adatelérési réteget mutat be, amely két ügyfelet hoz létre az egyéni szinkronizáláshoz:

### <a name="net-v2-sdk"></a>.NET v2 SDK
```csharp
class MyDataAccessLayer
{
    private DocumentClient writeClient;
    private DocumentClient readClient;

    public async Task InitializeAsync(Uri accountEndpoint, string key)
    {
        ConnectionPolicy writeConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp,
            UseMultipleWriteLocations = true
        };
        writeConnectionPolicy.SetCurrentLocation(LocationNames.WestUS);

        ConnectionPolicy readConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp
        };
        readConnectionPolicy.SetCurrentLocation(LocationNames.EastUS);

        writeClient = new DocumentClient(accountEndpoint, key, writeConnectionPolicy);
        readClient = new DocumentClient(accountEndpoint, key, readConnectionPolicy, ConsistencyLevel.Session);

        await Task.WhenAll(new Task[]
        {
            writeClient.OpenAsync(),
            readClient.OpenAsync()
        });
    }
}
```

### <a name="net-v3-sdk"></a>.Net v3 SDK
```csharp
class MyDataAccessLayer
{
    private CosmosClient writeClient;
    private CosmosClient readClient;

    public void InitializeAsync(string accountEndpoint, string key)
    {
        CosmosClientOptions writeConnectionOptions = new CosmosClientOptions();
        writeConnectionOptions.ConnectionMode = ConnectionMode.Direct;
        writeConnectionOptions.ApplicationRegion = "West US";

        CosmosClientOptions readConnectionOptions = new CosmosClientOptions();
        readConnectionOptions.ConnectionMode = ConnectionMode.Direct;
        readConnectionOptions.ApplicationRegion = "East US";


        writeClient = new CosmosClient(accountEndpoint, key, writeConnectionOptions);
        readClient = new CosmosClient(accountEndpoint, key, readConnectionOptions);
    }
}
```

## <a name="implement-custom-synchronization"></a>Egyéni szinkronizálás implementálása

Az ügyfelek inicializálását követően az alkalmazás elvégezheti az írásokat a helyi régióban (USA nyugati régiója), és a következő módon kényszerítheti az írásokat az USA keleti régiójába.

### <a name="net-v2-sdk"></a>.NET v2 SDK
```csharp
class MyDataAccessLayer
{
    public async Task CreateItem(string containerLink, Document document)
    {
        ResourceResponse<Document> response = await writeClient.CreateDocumentAsync(
            containerLink, document);

        await readClient.ReadDocumentAsync(response.Resource.SelfLink,
            new RequestOptions { SessionToken = response.SessionToken });
    }
}
```

### <a name="net-v3-sdk"></a>.Net v3 SDK
```csharp
class MyDataAccessLayer
{
     public async Task CreateItem(string databaseId, string containerId, dynamic item)
     {
        ItemResponse<dynamic> response = await writeClient.GetContainer("containerId", "databaseId")
            .CreateItemAsync<dynamic>(
                item,
                new PartitionKey("test"));

        await readClient.GetContainer("containerId", "databaseId").ReadItemAsync<dynamic>(
            response.Resource.id,
            new PartitionKey("test"),
            new ItemRequestOptions { SessionToken = response.Headers.Session, ConsistencyLevel = ConsistencyLevel.Session });
    }
}
```

A modellt kiterjesztheti párhuzamosan több régióba való szinkronizálásra is.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Azure Cosmos DB globális eloszlásáról és konzisztenciájáról, olvassa el a következő cikkeket:

* [Válassza ki a megfelelő konzisztencia-szintet Azure Cosmos DB](consistency-levels-choosing.md)
* [A konzisztencia, a rendelkezésre állás és a teljesítménybeli kompromisszumok Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Konzisztencia kezelése Azure Cosmos DBban](how-to-manage-consistency.md)
* [Particionálás és Adatelosztás a Azure Cosmos DBban](partition-data.md)
