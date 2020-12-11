---
title: Az Azure Cosmos DB-hez készült Azure Synapse Link konfigurálása és használata
description: Megtudhatja, hogyan engedélyezheti a szinapszis hivatkozását Azure Cosmos DB fiókokhoz, létrehozhat egy tárolót az analitikai tárolóval, és csatlakoztathatja az Azure Cosmos-adatbázist a szinapszis munkaterülethez, és futtathat lekérdezéseket.
author: Rodrigossz
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/30/2020
ms.author: rosouz
ms.custom: references_regions
ms.openlocfilehash: 6f9d8603467ac62d443a181fa45615501023a78b
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093570"
---
# <a name="configure-and-use-azure-synapse-link-for-azure-cosmos-db"></a>Az Azure Cosmos DB-hez készült Azure Synapse Link konfigurálása és használata
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Az [Azure szinapszis hivatkozása Azure Cosmos db](synapse-link.md) egy felhőalapú hibrid tranzakciós és analitikai feldolgozási (HTAP) képesség, amely lehetővé teszi a közel valós idejű elemzések futtatását Azure Cosmos db-ban lévő operatív adaton. A szinapszis-kapcsolat szoros zökkenőmentes integrációt hoz létre Azure Cosmos DB és az Azure szinapszis Analytics között.

Az Azure szinapszis hivatkozás Azure Cosmos DB SQL API-tárolók vagy a Mongo DB-gyűjtemények Azure Cosmos DB API-k számára érhető el. Az alábbi lépések végrehajtásával elemzési lekérdezéseket futtathat a Azure Cosmos DB Azure szinapszis hivatkozásával:

* [Szinapszis-hivatkozás engedélyezése a Azure Cosmos DB-fiókokhoz](#enable-synapse-link)
* [Analitikai tár engedélyezett Azure Cosmos DB tárolójának létrehozása](#create-analytical-ttl)
* [Azure Cosmos DB-adatbázis összekötése egy szinapszis-munkaterülettel](#connect-to-cosmos-database)
* [Az analitikai tároló lekérdezése a szinapszis Spark használatával](#query-analytical-store-spark)
* [Az analitikai tároló lekérdezése kiszolgáló nélküli SQL-készlettel](#query-analytical-store-sql-on-demand)
* [A kiszolgáló nélküli SQL-készlet használata az Power BI lévő adatelemzéshez és megjelenítéséhez](#analyze-with-powerbi)

## <a name="enable-azure-synapse-link-for-azure-cosmos-db-accounts"></a><a id="enable-synapse-link"></a>Azure szinapszis-hivatkozás engedélyezése Azure Cosmos DB fiókokhoz

### <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure-fiókot](create-sql-api-dotnet.md#create-account), vagy válasszon ki egy meglévő Azure Cosmos db fiókot.

1. Navigáljon a Azure Cosmos DB-fiókjához, és nyissa meg a **szolgáltatások** ablaktáblát.

1. Válassza a **szinapszis hivatkozás** elemet a szolgáltatások listából.

   :::image type="content" source="./media/configure-synapse-link/find-synapse-link-feature.png" alt-text="Szinapszis hivatkozási funkciójának keresése":::

1. Ezután felszólítja, hogy engedélyezze a szinapszis hivatkozását a fiókján. Válassza az **Engedélyezés** lehetőséget. A folyamat elvégzése 1 – 5 percet is igénybe vehet.

   :::image type="content" source="./media/configure-synapse-link/enable-synapse-link-feature.png" alt-text="Szinapszis-hivatkozás funkciójának engedélyezése":::

1. A fiókja mostantól engedélyezve van a szinapszis-hivatkozás használatára. Ezután olvassa el az analitikai tárolóval rendelkező tárolók létrehozása a működési adatok tranzakciós tárolóból az analitikai tárolóba való replikálásának automatikus megkezdéséhez című témakört.

> [!NOTE]
> A szinapszis-hivatkozás bekapcsolása nem kapcsolja be automatikusan az analitikai tárolót. Miután engedélyezte a szinapszis hivatkozását a Cosmos DB fiókon, engedélyezze az analitikai tárolót a tárolókban a létrehozásuk során, hogy elindítsa a művelet adatait az analitikai tárolóba. 

## <a name="create-an-azure-cosmos-container-with-analytical-store"></a><a id="create-analytical-ttl"></a> Azure Cosmos-tároló létrehozása analitikai tárolóval

A tároló létrehozásakor bekapcsolhatja az analitikus tárolót egy Azure Cosmos-tárolón. A tároló létrehozása során a Azure Portal vagy a `analyticalTTL` tulajdonságot a Azure Cosmos db SDK-k használatával végezheti el.

> [!NOTE]
> Jelenleg engedélyezheti az analitikai tárolót az **új** tárolók számára (az új és a meglévő fiókokban egyaránt). A meglévő-tárolók adatait áttelepítheti az új tárolókra [Azure Cosmos db áttelepítési eszközök használatával.](cosmosdb-migrationchoices.md)

### <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com/) vagy a [Azure Cosmos db Explorerben](https://cosmos.azure.com/).

1. Navigáljon a Azure Cosmos DB-fiókjához, és nyissa meg a **adatkezelő** lapot.

1. Válassza az **új tároló** lehetőséget, és adja meg az adatbázis nevét, a tárolót, a partíció kulcsát és az átviteli sebesség részleteit. Kapcsolja be az **analitikai tároló** lehetőséget. Az analitikai tároló engedélyezése után egy olyan tárolót hoz létre, amelynek `AnalyicalTTL` a tulajdonsága az alapértelmezett-1 értékre van beállítva (végtelen megőrzés). Ez az analitikai tároló a rekordok összes korábbi verzióját megőrzi.

   :::image type="content" source="./media/configure-synapse-link/create-container-analytical-store.png" alt-text="Az analitikai tár bekapcsolása az Azure Cosmos-tárolóhoz":::

1. Ha korábban nem engedélyezte a szinapszis hivatkozását ezen a fiókon, a rendszer felszólítja erre, mert ez egy, az analitikai tároló engedélyezett tárolójának létrehozásához szükséges előfeltétel. Ha a rendszer kéri, válassza a **szinapszis-hivatkozás engedélyezése** lehetőséget. A folyamat elvégzése 1 – 5 percet is igénybe vehet.

1. Az **OK gombra** kattintva hozzon létre egy analitikai tár által engedélyezett Azure Cosmos-tárolót.

1. A tároló létrehozása után ellenőrizze, hogy az analitikai tároló engedélyezve van-e. ehhez kattintson a **Beállítások** lehetőségre, közvetlenül a adatkezelő lévő dokumentumok elemre, és ellenőrizze, hogy be van-e kapcsolva az **analitikai tár élettartama** beállítás.

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

### <a name="python-v4-sdk"></a>Python v4 SDK

A Python 2,7-es és Azure Cosmos DB SDK-4.1.0 a minimálisan szükséges verziók, és az SDK csak az SQL API-val kompatibilis.

Első lépésként győződjön meg arról, hogy a [Azure Cosmos db PYTHON SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)legalább 4.1.0 verzióját használja:

```python
import azure.cosmos as cosmos

print (cosmos.__version__)
```
A következő lépés egy elemzési tárolót tartalmazó tárolót hoz létre a Azure Cosmos DB Python SDK használatával:

```python
# Azure Cosmos DB Python SDK, for SQL API only.
# Creating an analytical store enabled container.

import azure.cosmos.cosmos_client as cosmos_client
import azure.cosmos.exceptions as exceptions
from azure.cosmos.partition_key import PartitionKey

HOST = 'your-cosmos-db-account-URI'
KEY = 'your-cosmos-db-account-key'
DATABASE = 'your-cosmos-db-database-name'
CONTAINER = 'your-cosmos-db-container-name'

client = cosmos_client.CosmosClient(HOST,  KEY )
# setup database for this sample. 
# If doesn't exist, creates a new one with the name informed above.
try:
    db = client.create_database(DATABASE)

except exceptions.CosmosResourceExistsError:
    db = client.get_database_client(DATABASE)

# Creating the container with analytical store enabled, using the name informed above.
# If a container with the same name exists, an error is returned.
#
# The 3 options for the analytical_storage_ttl parameter are:
# 1) 0 or Null or not informed (Not enabled).
# 2) -1 (The data will be stored in analytical store infinitely).
# 3) Any other number is the actual ttl, in seconds.

try:
    container = db.create_container(
        id=CONTAINER,
        partition_key=PartitionKey(path='/id', kind='Hash'),analytical_storage_ttl=-1
    )
    properties = container.read()
    print('Container with id \'{0}\' created'.format(container.id))
    print('Partition Key - \'{0}\''.format(properties['partitionKey']))

except exceptions.CosmosResourceExistsError:
    print('A container with already exists')
```

### <a name="update-the-analytical-store-time-to-live"></a><a id="update-analytical-ttl"></a> Az analitikai tár élettartamának frissítése

Ha az elemzési tárat egy adott élettartamértékkel engedélyezte, később módosíthatja azt egy másik érvényes értékre. Az értéket az Azure Portalon vagy SDK-k használatával módosíthatja. További információ a különböző analitikai TTL-konfigurációs beállításokról: az [analitikai élettartam támogatott értékei](analytical-store-introduction.md#analytical-ttl) című cikk.

#### <a name="azure-portal"></a>Azure Portal

Ha a Azure Portalon keresztül létrehozott egy analitikai tárolót engedélyező tárolót, az a-1 alapértelmezett analitikai ÉLETTARTAMot tartalmazza. A következő lépésekkel frissítheti ezt az értéket:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com/) vagy a [Azure Cosmos db Explorerben](https://cosmos.azure.com/).

1. Navigáljon a Azure Cosmos DB-fiókjához, és nyissa meg a **adatkezelő** lapot.

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

## <a name="connect-to-a-synapse-workspace"></a><a id="connect-to-cosmos-database"></a> Kapcsolódás egy szinapszis-munkaterülethez

A [Csatlakozás az Azure szinapszishoz hivatkozásra kattintva](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md) megtudhatja, hogyan férhet hozzá egy Azure Cosmos db-adatbázishoz az Azure szinapszis Analytics studióból az Azure szinapszis link használatával.

## <a name="query-analytical-store-using-apache-spark-for-azure-synapse-analytics"></a><a id="query-analytical-store-spark"></a> Elemzési tároló lekérdezése az Azure szinapszis Analytics Apache Spark használatával

Kövesse a [lekérdezés Azure Cosmos db analitikus tároló](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md) című cikk utasításait a szinapszis Spark lekérdezésével kapcsolatban. Ebből a cikkből megtudhatja, hogyan kezelheti az analitikai tárolót a szinapszis-kézmozdulatokkal. Ezek a kézmozdulatok akkor láthatók, ha a jobb gombbal egy tárolóra kattint. A kézmozdulatokkal gyorsan létrehozhatja a kódot, és megszabhatja az igényeinek megfelelően. Emellett ideálisak arra is, hogy egyetlen kattintással felfedezzék az adatgyűjtést.

## <a name="query-the-analytical-store-using-serverless-sql-pool-in-azure-synapse-analytics"></a><a id="query-analytical-store-sql-on-demand"></a> Az analitikai tároló lekérdezése kiszolgáló nélküli SQL-készlettel az Azure szinapszis Analyticsben

A kiszolgáló nélküli SQL-készlet lehetővé teszi, hogy lekérdezze és elemezze az Azure szinapszis-hivatkozással engedélyezett Azure Cosmos DB tárolók adatait. Közel valós időben elemezheti az adatait anélkül, hogy ez hatással lenne a tranzakciós munkaterhelések teljesítményére. Jól ismert T-SQL-szintaxist kínál, amely az analitikus áruházból származó adatok lekérdezését, valamint a BI-és ad-hoc lekérdezési eszközök széles köréhez való integrált csatlakozást biztosít a T-SQL felületen keresztül. További információért tekintse meg a [lekérdezési analitikus áruház kiszolgáló nélküli SQL-készlettel](../synapse-analytics/sql/query-cosmos-db-analytical-store.md) című cikket.

## <a name="use-serverless-sql-pool-to-analyze-and-visualize-data-in-power-bi"></a><a id="analyze-with-powerbi"></a>A kiszolgáló nélküli SQL-készlet használata az Power BI lévő adatelemzéshez és megjelenítéséhez

Felépítheti a kiszolgáló nélküli SQL Pool-adatbázist és a Azure Cosmos DB-re vonatkozó szinapszis-kapcsolaton keresztüli nézeteket. Később lekérdezheti az Azure Cosmos-tárolókat, majd létrehozhat egy modellt Power BI a nézeteken, hogy azok tükrözzék a lekérdezést. További információért lásd: Hogyan lehet kiszolgáló nélküli [SQL-készletet használni az Azure Cosmos db-adatelemzéshez a szinapszis hivatkozásával](synapse-link-power-bi.md) .

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

A [Azure Resource Manager sablon](./manage-with-templates.md#azure-cosmos-account-with-analytical-store) létrehoz egy szinapszis-hivatkozást Azure Cosmos db FIÓKOT az SQL API-hoz. Ez a sablon létrehoz egy core (SQL) API-fiókot az egyik régióban egy olyan tárolóval, amelyen engedélyezve van az analitikai TTL, valamint a manuális vagy automatikus méretezési sebesség használata. A sablon üzembe helyezéséhez kattintson az **üzembe helyezés az Azure** -ra lehetőségre a readme oldalon.

## <a name="getting-started-with-azure-synpase-link---samples"></a><a id="cosmosdb-synapse-link-samples"></a> Bevezetés az Azure Synpase link-Samples használatába

A [githubon](https://aka.ms/cosmosdb-synapselink-samples)megtalálhatja az Azure szinapszis hivatkozásának megkezdéséhez szükséges mintákat. Ezek a teljes körű megoldások a IoT és a kiskereskedelmi helyzetekben. A MongoDB Azure Cosmos DB API-nak megfelelő mintákat is megtalálhatja ugyanabban a tárházban, a [MongoDB](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples) mappában. 

## <a name="next-steps"></a>Következő lépések

További információért lásd a következő dokumentumokat:

* [Az Azure szinapszis hivatkozása Azure Cosmos DB.](synapse-link.md)

* [Azure Cosmos DB analitikus áruház áttekintése.](analytical-store-introduction.md)

* [Gyakran ismételt kérdések a Azure Cosmos DB-ra vonatkozó szinapszis-hivatkozásról.](synapse-link-frequently-asked-questions.md)

* [Apache Spark az Azure szinapszis Analyticsben](../synapse-analytics/spark/apache-spark-concepts.md).

* [Kiszolgáló nélküli SQL Pool Runtime-támogatás az Azure szinapszis Analyticsben](../synapse-analytics/sql/on-demand-workspace-overview.md).
