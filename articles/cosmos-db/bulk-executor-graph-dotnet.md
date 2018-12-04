---
title: Műveletek tömeges végrehajtása a BulkExecutor .NET-gráfkódtár használatával az Azure Cosmos DB Gremlin API-ban
description: Ismerje meg, hogyan importálhat tömegesen gráfadatokat egy Azure Cosmos DB Gremlin API-tárolóba a BulkExecutor-kódtár használatával.
services: cosmos-db
keywords: gráf, gremlin, tömeges, bulkexecutor, migrálás, adatok, cosmosdb, cosmos, adatbázis, importálás
author: luisbosquez
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: d5a25bd7cab68f77a37b14ba41bf3cc832c2125f
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52836712"
---
# <a name="using-the-graph-bulkexecutor-net-library-to-perform-bulk-operations-in-azure-cosmos-db-gremlin-api"></a>Műveletek tömeges végrehajtása a BulkExecutor .NET-gráfkódtár használatával az Azure Cosmos DB Gremlin API-ban

Ez az oktatóanyag azt ismerteti, hogy hogyan importálhat és frissíthet gráfobjektumokat egy Azure Cosmos DB Gremlin API-tárolóban az Azure Cosmos DB BulkExecutor .NET-kódtára használatával. Ezzel az eljárással programozott módon hozhatók létre Csúcspont és Él objektumok a [BulkExecutor-kódtár](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview) Graph osztályával, amelyekből ezután hálózati kérelmenként több is beilleszthető. Ez a viselkedés a BulkExecutor-kódtárban konfigurálható az adatbázis és a helyi memória-erőforrások optimális kihasználása érdekében.

A Gremlin-lekérdezések az adatbázisnak való küldésével ellentétben (ahol a parancsok kiértékelése és végrehajtása egyesével történik), a BulkExecutor-kódtár használatakor az objektumokat helyileg kell létrehozni és ellenőrizni. Az objektumok létrehozását követően a kódtár használatával a gráfobjektumokat szekvenciálisan küldheti az adatbázis-szolgáltatásba. Ezzel a módszerrel az adatfeldolgozási sebesség akár a százszorosára növelhető, ami ideális módszerré teszi a kezdeti adatmigrálásokhoz és az időszakos adatátviteli műveletekhez. További információért látogasson el a GitHub [az Azure Cosmos DB Graph BulkExecutor mintaalkalmazást tartalmazó oldalára](https://aka.ms/graph-bulkexecutor-sample).

## <a name="bulk-operations-with-graph-data"></a>Tömeges gráfadatműveletek

A [BulkExecutor-kódtár](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet) egy `Microsoft.Azure.CosmosDB.BulkExecutor.Graph` névteret tartalmaz a gráfobjektumok létrehozásával és importálásával kapcsolatos funkciók biztosításához. 

Az alábbi folyamat bemutatja, hogyan használható az adatmigrálás a Gremlin API-tárolók esetében:
1. Rekordok lekérése az adatforrásból.
2. Hozzon létre `GremlinVertex`- és `GremlinEdge`-objektumokat a beszerzett rekordokból, és adja hozzá őket egy `IEnumerable`-adatstruktúrához. Az alkalmazásnak ebben a részében kell megvalósítani a kapcsolatok észlelését és hozzáadását végző logikát, arra az esetre, ha az adatforrás nem egy gráfadatbázis.
3. A [Graph BulkImportAsync metódus](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph.graphbulkexecutor.bulkimportasync?view=azure-dotnet) használatával szúrhatja be a gráfobjektumokat a gyűjteménybe.

Ezzel a mechanizmussal jobb adatmigrálási hatékonyság érhető el, mint a Gremlin-ügyfél használata esetében. Azért tapasztalható ez a javulás, mert a Gremlin használata esetén az alkalmazás egyszerre egy lekérdezést küld le, amelyet ellenőrizni kell, ki kell értékelni, majd végre kell hajtani az adatok létrehozásához. A BulkExecutor-kódtár az ellenőrzést az alkalmazásban végzi el, és mindegyik hálózati kérelem esetében egyszerre több gráfobjektumot küld el.

### <a name="creating-vertices-and-edges"></a>Csúcsok és élek létrehozása

A `GraphBulkExecutor` a `BulkImportAsync` metódust biztosítja, amelyhez a `Microsoft.Azure.CosmosDB.BulkExecutor.Graph.Element` névtérben definiált `GremlinVertex` vagy `GremlinEdge` objektumok egy `IEnumerable`-listája szükséges. A mintában az éleket és a csúcsokat különválasztottuk két BulkExecutor-importálási feladatba. Lásd az alábbi példát:

```csharp

IBulkExecutor graphbulkExecutor = new GraphBulkExecutor(documentClient, targetCollection);

BulkImportResponse vResponse = null;
BulkImportResponse eResponse = null;

try
{
    // Import a list of GremlinVertex objects
    vResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateVertices(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);

    // Import a list of GremlinEdge objects
    eResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateEdges(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);
}
catch (DocumentClientException de)
{
    Trace.TraceError("Document client exception: {0}", de);
}
catch (Exception e)
{
    Trace.TraceError("Exception: {0}", e);
}
```

A BulkExecutor-kódtár paramétereivel kapcsolatos további információért tekintse meg [az adatok az Azure Cosmos DB-be való tömeges importálását (BulkImportData) ismertető témakört](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-dot-net#bulk-import-data-to-azure-cosmos-db).

A hasznos adatokat példányosítani kell `GremlinVertex`- és `GremlinEdge`-objektumokba. Ezeket az objektumokat így lehet létrehozni:

**Csúcspontok**:
```csharp
// Creating a vertex
GremlinVertex v = new GremlinVertex(
    "vertexId",
    "vertexLabel");

// Adding custom properties to the vertex
v.AddProperty("customProperty", "value");

// Partitioning keys must be specified for all vertices
v.AddProperty("partitioningKey", "value");
```

**Élek**:
```csharp
// Creating an edge
GremlinEdge e = new GremlinEdge(
    "edgeId",
    "edgeLabel",
    "targetVertexId",
    "sourceVertexId",
    "targetVertexLabel",
    "sourceVertexLabel",
    "targetVertexPartitioningKey",
    "sourceVertexPartitioningKey");

// Adding custom properties to the edge
e.AddProperty("customProperty", "value");
```

> [!NOTE]
> A BulkExecutor segédprogram az Élek hozzáadása előtt nem ellenőrzi automatikusan a Csúcspontok meglétét. Ezt az alkalmazásban kell ellenőrizni a BulkImport feladatok futtatása előtt.

## <a name="sample-application"></a>Mintaalkalmazás

### <a name="prerequisites"></a>Előfeltételek
* Az Azure-fejlesztési számítási feladatot is tartalmazó Visual Studio 2017. Ingyenesen elkezdheti a [Visual Studio 2017 Community Edition](https://visualstudio.microsoft.com/downloads/) használatát.
* Azure-előfizetés. Itt hozhat létre [ingyenes Azure-fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cosmos-db). Alternatív lehetőségként [ingyenesen kipróbálhatja az Azure Cosmos DB-t](https://azure.microsoft.com/try/cosmosdb/), és Azure-előfizetés nélkül hozhat létre egy Cosmos DB-adatbázisfiókot.
* Azure Cosmos DB Gremlin API-adatbázis **korlátlan gyűjteménnyel**. Ez az útmutató bemutatja, hogy hogyan foghat neki [az Azure Cosmos DB Gremlin API használatának a .NET környezetben](https://docs.microsoft.com/azure/cosmos-db/create-graph-dotnet).
* Git. További információért tekintse meg a [Git letöltési oldalát](https://git-scm.com/downloads).

### <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása
Ebben az oktatóanyagban a kezdő lépéseket a GitHubon elérhető [Azure Cosmos DB Graph BulkExecutor minta](https://aka.ms/graph-bulkexecutor-sample) használatával követjük végig. Ez az alkalmazás egy .NET-megoldást tartalmaz, amely véletlenszerűen állít elő csúcspont és él objektumokat, majd tömeges beszúrásokat hajt végre a megadott gráfadatbázis-fiókba. Az alkalmazás lekéréséhez futtassa az alábbi `git clone` parancsot:

```bash
git clone https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started.git
```

Ez az adattár tartalmazza a GraphBulkExecutor mintát a következő fájlokkal:

Fájl|Leírás
---|---
`App.config`|Itt vannak megadva az alkalmazás- és adatbázis-specifikus paraméterek. Ezt a fájlt előbb módosítani kell, hogy a céladatbázishoz és -gyűjteményekhez csatlakozzon.
`Program.cs`| Ez a fájl tartalmazza a mögöttes logikát, amely a `DocumentClient` gyűjtemény létrehozását, a karbantartások kezelését és a BulkExecutor-kérések küldését végzi.
`Util.cs`| Ez a fájl egy segítőosztályt tartalmaz, amely a tesztadatok létrehozásához szükséges mögöttes logikát biztosítja, valamint az adatbázis és a gyűjtemények meglétének ellenőrzését végzi.

Az `App.config` fájlban az alábbi konfigurációs értékek adhatók meg:

Beállítás|Leírás
---|---
`EndPointUrl`|Ez a **.NET SDK-végpont**, amely az Azure Cosmos DB Gremlin API-adatbázisfiók Áttekintés paneljén található. A formátuma a következő: `https://your-graph-database-account.documents.azure.com:443/`
`AuthorizationKey`|Ez az Azure Cosmos DB-fiók alatt listázott elsődleges vagy másodlagos kulcs. További információért tekintse meg [az Azure Cosmos DB-adatokhoz való hozzáférés biztonságossá tételével foglalkozó cikket](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#master-keys).
`DatabaseName`, `CollectionName`|Ezek a **céladatbázis és -gyűjtemény nevei**. Ha a `ShouldCleanupOnStart` értéke `true`, a rendszer elveti a korábbi neveket, és ezekkel az értékekkel, valamint a `CollectionThroughput` értékkel egy új adatbázist és gyűjteményt hoz létre. Hasonlóképp, ha a `ShouldCleanupOnFinish` értéke `true`, a rendszer az értékek használatával törli az adatbázist, amint az adatok feldolgozása befejeződött. Vegye figyelembe, hogy a célgyűjteménynek **korlátlan gyűjteménynek kell lennie**.
`CollectionThroughput`|A rendszer ezt egy új gyűjtemény létrehozásához használja, ha a `ShouldCleanupOnStart` értéke `true`.
`ShouldCleanupOnStart`|Ez a program futtatása előtt törli az adatbázis-fiókot és -gyűjteményeket, és újakat hoz létre a `DatabaseName`, `CollectionName` és `CollectionThroughput` értékekkel.
`ShouldCleanupOnFinish`|Ez eltávolítja a megadott `DatabaseName` és `CollectionName` értékkel rendelkező adatbázis-fiókot és -gyűjteményeket a program futtatása után.
`NumberOfDocumentsToImport`|Ez határozza meg a mintában létrehozott tesztelési célú csúcsok és élek számát. Ez a szám vonatkozik a csúcsokra és az élekre egyaránt.
`NumberOfBatches`|Ez határozza meg a mintában létrehozott tesztelési célú csúcsok és élek számát. Ez a szám vonatkozik a csúcsokra és az élekre egyaránt.
`CollectionPartitionKey`|Ez a csúcsok és élek tesztelése során lesz majd használva, amikor is a rendszer automatikusan osztja ki ezt a tulajdonságot. A rendszer ezt használja az adatbázis és a gyűjtemények ismételt létrehozásához, ha a `ShouldCleanupOnStart` beállítás értéke `true`.

### <a name="run-the-sample-application"></a>A mintaalkalmazás futtatása

1. Adja hozzá az adott adatbázis konfigurációs paramétereit az `App.config` fájlban. A rendszer ez alapján hoz létre egy DocumentClient-példányt. Ha az adatbázis és a tároló még nem lettek létrehozva, automatikusan létrejönnek.
2. Futtassa az alkalmazást. Ez kétszer hívja meg a `BulkImportAsync` metódust, egyszer a csúcsok és egyszer az élek importálása végett. Ha bármelyik objektum beszúrása során hiba lép fel, a rendszer felveszi az objektumot a `.\BadVertices.txt` vagy a `.\BadEdges.txt` fájlba.
3. Ellenőrizze az eredményeket a gráfadatbázis lekérdezésével. Ha a `ShouldCleanupOnFinish` beállítás értéke true (igaz), az adatbázis automatikusan törölve lesz.

## <a name="next-steps"></a>További lépések
* A Nuget-csomaggal kapcsolatos további információért és a tömeges végrehajtói .NET-kódtár kibocsátási megjegyzéseiért tekintse meg [a tömeges végrehajtói SDK részleteit](sql-api-sdk-bulk-executor-dot-net.md). 
* A BulkExecutor használatának további optimalizálásával kapcsolatban lásd [a teljesítménnyel kapcsolatos tippeket](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-dot-net#performance-tips).
* Az ebben a névtérben definiált osztályokkal és névterekkel kapcsolatos további információért olvassa el [a BulkExecutor.Graph áttekintését tartalmazó cikket](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet).
