---
title: Egyéni optimalizálása a magasabb rendelkezésre állás és teljesítmény az Azure Cosmos DB-szinkronizálás megvalósítása
description: Ismerje meg, hogyan optimalizálható a magasabb rendelkezésre állás és teljesítmény az Azure Cosmos DB egyéni-szinkronizálás megvalósítása.
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: rimman
ms.openlocfilehash: de66149a2ea3e01e62aa8e33ea5a99121a21524f
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67986081"
---
# <a name="implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>A magasabb rendelkezésre állás és teljesítmény optimalizálása érdekében egyéni-szinkronizálás megvalósítása

Az Azure Cosmos DB-ajánlatok [öt jól definiált konzisztenciaszintet](consistency-levels.md) választhat a konzisztencia, a teljesítmény és a rendelkezésre állási közötti elosztása érdekében. Erős konzisztencia lehetővé teszi, hogy adatokat szinkronban replikálva lettek, és minden régióban, ahol érhető el az Azure Cosmos-fiók tartósan tárolja. Ez a konfiguráció biztosítja a legnagyobb szintű tartósságot biztosítja, de a megoldás a teljesítmény és rendelkezésre állás. Ha azt szeretné, hogy az alkalmazás szabályozhatja, vagy az adatok megőrzését enyhíteni az alkalmazás igényeinek megfelelően kell rendelkezésre állási veszélyeztetése nélkül, akkor használhatja *egyéni szinkronizálási* az alkalmazási rétegben a tartóssági szintjének eléréséhez, szeretné.

Az alábbi képen vizuálisan ábrázolja az egyéni szinkronizálási modell:

![Egyéni szinkronizálási](./media/how-to-custom-synchronization/custom-synchronization.png)

Ebben a forgatókönyvben egy Azure Cosmos-tárolóhoz replikál a rendszer globálisan számos régióban lévő több kontinensen. Erős konzisztencia használata ebben a forgatókönyvben minden régió esetében befolyásolja a teljesítményt. Nélkül veszélyeztetése írási késés magasabb szintű az adatok tartóssága érdekében az alkalmazás két olyan ügyfelek, amelyek azonos használható [tokenu relace](how-to-manage-consistency.md#utilize-session-tokens).

Az első ügyfél tudjon adatokat írni a helyi régió (például USA nyugati régiója). A második ügyfél (például az USA keleti régiója) olvasási ügyfél, amely a szinkronizálás biztosítja. Által a munkamenet-jogkivonat a következő olvasási, írási válaszból áramló, az olvasási biztosítja a szinkronizálást az írását az USA keleti régiójában. Az Azure Cosmos DB biztosítja, hogy az írási műveletek láthatók-e legalább egy régió szerint. Hogy garantáltan stabilitást biztosít egy regionális kimaradás, ha az eredeti írási régió leáll. Ebben a forgatókönyvben minden írási szinkronizálva van az USA keleti régiójához, így csökkentve az erős konzisztencia alkalmazó minden régióban. Több főkiszolgálós forgatókönyvekben, ahol írási minden régióban történik, ez a modell párhuzamosan több régióban való szinkronizálása bővítheti.

## <a name="configure-the-clients"></a>Az ügyfelek konfigurálása

Az alábbi mintában látható, amely két ügyfelek egyéni szinkronizálási példányosítja adatelérési réteg:

### <a name="net-v2-sdk"></a>.Net V2 SDK
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

### <a name="net-v3-sdk"></a>.Net V3 SDK
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
        writeClient = new CosmosClient(accountEndpoint, key, writeConnectionOptions);
    }
}
```

## <a name="implement-custom-synchronization"></a>Egyéni szinkronizálás implementálása

Után az ügyfelek inicializálása, az alkalmazás elvégezhet a helyi régió (USA nyugati régiója) és a kényszerített-szinkronizálási írási műveletek a írását az USA keleti régiójában módon.

### <a name="net-v2-sdk"></a>.Net V2 SDK
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

### <a name="net-v3-sdk"></a>.Net V3 SDK
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

Szinkronizálni kívánt párhuzamosan több régióban a modell bővítheti.

## <a name="next-steps"></a>További lépések

Globális terjesztés és konzisztencia az Azure Cosmos DB kapcsolatos további információkért olvassa el ezeket a cikkeket:

* [Válassza ki a megfelelő konzisztenciaszint Azure Cosmos DB-ben](consistency-levels-choosing.md)
* [Konzisztencia, a rendelkezésre állás és a teljesítmény és kompromisszumot kínál a Azure Cosmos DB-ben](consistency-levels-tradeoffs.md)
* [Az Azure Cosmos DB konzisztencia kezelése](how-to-manage-consistency.md)
* [Particionálás és az adatok terjesztési Azure Cosmos DB-ben](partition-data.md)
