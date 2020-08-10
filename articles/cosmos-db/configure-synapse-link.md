---
title: Azure szinapszis-hivatkozás konfigurálása és használata Azure Cosmos DB (előzetes verzió)
description: Megtudhatja, hogyan engedélyezheti az Azure Cosmos-fiókok szinapszis-hivatkozását, hogyan hozhat létre olyan tárolót, amelyeken engedélyezve van az analitikai tároló, valamint csatlakoztathatja az Azure Cosmos-adatbázist a szinapszis munkaterülethez
author: Rodrigossz
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/19/2020
ms.author: rosouz
ms.openlocfilehash: 9499fe2140f4a345d48bce6ef010989cfc22c58e
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037082"
---
# <a name="configure-and-use-azure-synapse-link-for-azure-cosmos-db-preview"></a>Azure szinapszis-hivatkozás konfigurálása és használata Azure Cosmos DB (előzetes verzió)

A Azure Cosmos DB szinapszis hivatkozása egy felhőalapú hibrid tranzakciós és analitikai feldolgozási (HTAP) képesség, amely lehetővé teszi a közel valós idejű elemzések futtatását Azure Cosmos DBokban lévő operatív adatokon. A szinapszis-kapcsolat szoros zökkenőmentes integrációt hoz létre Azure Cosmos DB és az Azure szinapszis Analytics között.


> [!IMPORTANT]
> Az Azure szinapszis-hivatkozás használatához győződjön meg arról, hogy az Azure Cosmos-fiókját & Azure szinapszis Analytics-munkaterületen helyezi üzembe a fenti támogatott régiók egyikében. A támogatott régiók listáját az [Azure-szolgáltatások frissítései](https://azure.microsoft.com/updates/)című témakörben tekintheti meg. 

Az alábbi lépések végrehajtásával elemzési lekérdezéseket futtathat a Azure Cosmos DB szinapszis hivatkozásával:

* [Az Azure Cosmos-fiókok szinapszis-hivatkozásának engedélyezése](#enable-synapse-link)
* [Analitikai tár engedélyezett Azure Cosmos-tárolójának létrehozása](#create-analytical-ttl)
* [Az Azure Cosmos-adatbázis összekötése egy szinapszis-munkaterülettel](#connect-to-cosmos-database)
* [Az analitikai tároló lekérdezése a szinapszis Spark használatával](#query-analytical-store)

## <a name="enable-azure-synapse-link-for-azure-cosmos-accounts"></a><a id="enable-synapse-link"></a>Azure-beli szinapszis-hivatkozás engedélyezése az Azure Cosmos-fiókokhoz

### <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure-fiókot](create-sql-api-dotnet.md#create-account), vagy válasszon ki egy meglévő Azure Cosmos-fiókot.

1. Navigáljon az Azure Cosmos-fiókjához, és nyissa meg a **szolgáltatások** ablaktáblát.

1. Válassza a **szinapszis hivatkozás** elemet a szolgáltatások listából.

   :::image type="content" source="./media/configure-synapse-link/find-synapse-link-feature.png" alt-text="A szinapszis-hivatkozás előzetes funkciójának keresése":::

1. Ezután felszólítja, hogy engedélyezze a szinapszis hivatkozását a fiókján. Válassza az Engedélyezés lehetőséget.

   :::image type="content" source="./media/configure-synapse-link/enable-synapse-link-feature.png" alt-text="Szinapszis-hivatkozás funkciójának engedélyezése":::

1. A fiókja mostantól engedélyezve van a szinapszis-hivatkozás használatára. Ezután olvassa el az analitikai tárolóval rendelkező tárolók létrehozása a működési adatok tranzakciós tárolóból az analitikai tárolóba való replikálásának automatikus megkezdéséhez című témakört.

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

A [Azure Resource Manager sablon](manage-sql-with-resource-manager.md#azure-cosmos-account-with-analytical-store) létrehoz egy szinapszis hivatkozást, amely lehetővé teszi az Azure Cosmos-fiók SQL API-hoz való használatát. Ez a sablon létrehoz egy core (SQL) API-fiókot az egyik régióban egy olyan tárolóval, amelyen engedélyezve van az analitikai TTL, valamint a manuális vagy automatikus méretezési sebesség használata. A sablon üzembe helyezéséhez kattintson az **üzembe helyezés az Azure** -ra lehetőségre a readme oldalon.

## <a name="create-an-azure-cosmos-container-with-analytical-store"></a><a id="create-analytical-ttl"></a>Azure Cosmos-tároló létrehozása analitikai tárolóval

A tároló létrehozásakor bekapcsolhatja az analitikus tárolót egy Azure Cosmos-tárolón. A tároló létrehozása során a Azure Portal vagy a `analyticalTTL` tulajdonságot a Azure Cosmos db SDK-k használatával végezheti el.

> [!NOTE]
> Jelenleg engedélyezheti az analitikai tárolót az **új** tárolók számára (az új és a meglévő fiókokban egyaránt).

### <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) vagy az [Azure Cosmos Explorerben](https://cosmos.azure.com/).

1. Navigáljon az Azure Cosmos-fiókjához, és nyissa meg a **adatkezelő** lapot.

1. Válassza az **új tároló** lehetőséget, és adja meg az adatbázis nevét, a tárolót, a partíció kulcsát és az átviteli sebesség részleteit. Kapcsolja be az **analitikai tároló** lehetőséget. Az analitikai tároló engedélyezése után egy olyan tárolót hoz létre, amelynek `AnalyicalTTL` a tulajdonsága az alapértelmezett-1 értékre van beállítva (végtelen megőrzés). Ez az analitikai tároló a rekordok összes korábbi verzióját megőrzi.

   :::image type="content" source="./media/configure-synapse-link/create-container-analytical-store.png" alt-text="Az analitikai tár bekapcsolása az Azure Cosmos-tárolóhoz":::

1. Ha korábban nem engedélyezte a szinapszis hivatkozását ezen a fiókon, a rendszer felszólítja erre, mert ez egy, az analitikai tároló engedélyezett tárolójának létrehozásához szükséges előfeltétel. Ha a rendszer kéri, válassza a **szinapszis-hivatkozás engedélyezése**lehetőséget.

1. Az **OK gombra**kattintva hozzon létre egy analitikai tár által engedélyezett Azure Cosmos-tárolót.

### <a name="net-sdk"></a>.NET SDK

A következő kód egy tárolót hoz létre az analitikai tárolóval a .NET SDK használatával. Állítsa be az analitikai TTL tulajdonságot a szükséges értékre. Az engedélyezett értékek listájának megtekintéséhez tekintse meg az [analitikai TTL által támogatott értékek](analytical-store-introduction.md#analytical-ttl) című cikket:

```csharp
// Create a container with a partition key, and analytical TTL configured to  -1 (infinite retention)
string containerId = “myContainerName”;
int analyticalTtlInSec = -1;
ContainerProperties cpInput = new ContainerProperties()
            {
Id = containerId,
PartitionKeyPath = "/id",
AnalyticalStorageTimeToLiveInSeconds = analyticalTtlInSec,
};
 await this. cosmosClient.GetDatabase("myDatabase").CreateContainerAsync(cpInput);
```

### <a name="java-v4-sdk"></a>Java v4 SDK

A következő kód egy tárolót hoz létre az analitikai tárolóval a Java v4 SDK használatával. Állítsa a `AnalyticalStoreTimeToLiveInSeconds` tulajdonságot a szükséges értékre:

```java
// Create a container with a partition key and  analytical TTL configured to  -1 (infinite retention) 
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

containerProperties.setAnalyticalStoreTimeToLiveInSeconds(-1);

container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

### <a name="python-v3-sdk"></a>Python v3 SDK

A következő kód létrehoz egy tárolót az analitikai tárolóval a Python SDK használatával:

```python
import azure.cosmos.cosmos_client as cosmos_client
def create_collection_if_not_exists(cosmosEndpoint, cosmosKey, databaseName, collectionName):
    client = cosmos_client.CosmosClient(url_connection=cosmosEndpoint, auth={'masterKey': cosmosKey})

db = client.QueryDatabases("select * from c where c.id = '" + databaseName + "'").fetch_next_block()[0]
options = {
    'offerThroughput': 1000
}

container_definition = {
    'id': collectionName,
    "partitionKey": {  
        "paths": [  
        "/id"  
        ],  
        "kind": "Hash" 
    },
    "indexingPolicy": {  
    "indexingMode": "consistent",  
    "automatic": True,  
    "includedPaths": [],  
    "excludedPaths": [{
        "path": "/*"
    }]  
    },
    "defaultTtl": -1,
    "analyticalStorageTtl": -1
}

container = client.CreateContainer(db['_self'], container_definition, options)
```

### <a name="update-the-analytical-store-time-to-live"></a><a id="update-analytical-ttl"></a>Az analitikai tár élettartamának frissítése

Ha az elemzési tárat egy adott élettartamértékkel engedélyezte, később módosíthatja azt egy másik érvényes értékre. Az értéket az Azure Portalon vagy SDK-k használatával módosíthatja. További információ a különböző analitikai TTL-konfigurációs beállításokról: az [analitikai élettartam támogatott értékei](analytical-store-introduction.md#analytical-ttl) című cikk.

#### <a name="azure-portal"></a>Azure Portal

Ha a Azure Portalon keresztül létrehozott egy analitikai tárolót engedélyező tárolót, az a-1 alapértelmezett analitikai ÉLETTARTAMot tartalmazza. A következő lépésekkel frissítheti ezt az értéket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) vagy az [Azure Cosmos Explorerben](https://cosmos.azure.com/).

1. Navigáljon az Azure Cosmos-fiókjához, és nyissa meg a **adatkezelő** lapot.

1. Válasszon ki egy olyan meglévő tárolót, amelyen engedélyezve van az analitikus tároló. Bontsa ki a csomópontot, és módosítsa a következő értékeket:

  * Nyissa meg a **méretezési & beállítások** ablakát.
  * A **beállítás** területen a * * analitikus tárterület élettartama * * értékre.
  * Válassza **a be (nincs alapértelmezett)** lehetőséget, vagy válassza **a** be lehetőséget, és állítsa be a TTL értéket
  * Kattintson a **Mentés** gombra a módosítások mentéséhez.

#### <a name="net-sdk"></a>.NET SDK

A következő kód bemutatja, hogyan frissíthető az élettartam az analitikai tárolóhoz a .NET SDK használatával:

```csharp
// Get the container, update AnalyticalStorageTimeToLiveInSeconds 
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Update analytical store TTL
containerResponse.Resource. AnalyticalStorageTimeToLiveInSeconds = 60 * 60 * 24 * 180  // Expire analytical store data in 6 months;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

#### <a name="java-v4-sdk"></a>Java v4 SDK

A következő kód bemutatja, hogyan frissíthető az élettartam az analitikus tárolóhoz a Java v4 SDK használatával:

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

// Update analytical store TTL to expire analytical store data in 6 months;
containerProperties.setAnalyticalStoreTimeToLiveInSeconds (60 * 60 * 24 * 180 );  
 
// Update container settings
container.replace(containerProperties).block();
```

## <a name="connect-to-a-synapse-workspace"></a><a id="connect-to-cosmos-database"></a>Kapcsolódás egy szinapszis-munkaterülethez

A [Csatlakozás az Azure szinapszishoz hivatkozásra kattintva](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md) megtudhatja, hogyan férhet hozzá egy Azure Cosmos db-adatbázishoz az Azure szinapszis Analytics studióból az Azure szinapszis link használatával.

## <a name="query-using-synapse-spark"></a><a id="query-analytical-store"></a>Lekérdezés a szinapszis Spark használatával

Kövesse a [lekérdezés Azure Cosmos db analitikus tároló](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md) című cikk utasításait a szinapszis Spark lekérdezésével kapcsolatban. Ebből a cikkből megtudhatja, hogyan kezelheti az analitikai tárolót a szinapszis-kézmozdulatokkal. Ezek a kézmozdulatok akkor láthatók, ha a jobb gombbal egy tárolóra kattint. A kézmozdulatokkal gyorsan létrehozhatja a kódot, és megszabhatja az igényeinek megfelelően. Emellett ideálisak arra is, hogy egyetlen kattintással felfedezzék az adatgyűjtést.

## <a name="getting-started-with-azure-synpase-link---samples"></a><a id="cosmosdb-synapse-link-samples"></a>Bevezetés az Azure Synpase link-Samples használatába

A [githubon](https://aka.ms/cosmosdb-synapselink-samples)megtalálhatja az Azure szinapszis hivatkozásának megkezdéséhez szükséges mintákat. Ezek a teljes körű megoldások a IoT és a kiskereskedelmi helyzetekben.

## <a name="next-steps"></a>További lépések

További információért lásd a következő dokumentumokat:

* [Az Azure szinapszis hivatkozása Azure Cosmos DB.](synapse-link.md)

* [Az Azure Cosmos DB elemzési tár áttekintése.](analytical-store-introduction.md)

* [Gyakran ismételt kérdések a Azure Cosmos DB-ra vonatkozó szinapszis-hivatkozásról.](synapse-link-frequently-asked-questions.md)

* [Apache Spark az Azure szinapszis Analyticsben](../synapse-analytics/spark/apache-spark-concepts.md).

* [SQL Server nélküli/igény szerint az Azure szinapszis Analytics szolgáltatásban](../synapse-analytics/sql/on-demand-workspace-overview.md).
