---
title: A .NET-kódtár használata az Azure Cosmos DB Gremlin API-val
description: Ismerje meg, hogyan használhatja a tömeges végrehajtó könyvtár segítségével tömegesen importálja a gráf adatokat egy Azure Cosmos DB Gremlin API-tárolóba.
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: lbosq
ms.reviewer: sngun
ms.openlocfilehash: adf512fc521ef553f0bbd6ef6dd8ee19e398b37b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982703"
---
# <a name="using-the-graph-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db-gremlin-api"></a>A graph tömeges végrehajtó .NET-kódtár használatával tömeges műveleteket hajthat végre az Azure Cosmos DB Gremlin API-ban

Ez az oktatóanyag az Azure CosmosDB tömeges végrehajtó . Ez a folyamat a tömeges [végrehajtó könyvtár](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview) Graph osztályát használja a Vertex és edge objektumok programozott létrehozásához, hogy hálózati kérelemenként több objektumot szúrjon be. Ez a viselkedés a tömeges végrehajtó könyvtáron keresztül konfigurálható az adatbázis és a helyi memória erőforrások optimális kihasználásához.

A Gremlin-lekérdezések adatbázisba küldésével ellentétben, ahol a parancs kiértékelése, majd végrehajtása egyenként történik, a tömeges végrehajtó könyvtár használatával ehelyett helyileg kell létrehozni és érvényesíteni az objektumokat. Az objektumok létrehozását követően a kódtár használatával a gráfobjektumokat szekvenciálisan küldheti az adatbázis-szolgáltatásba. Ezzel a módszerrel az adatfeldolgozási sebesség akár a százszorosára növelhető, ami ideális módszerré teszi a kezdeti adatmigrálásokhoz és az időszakos adatátviteli műveletekhez. További információ az [Azure Cosmos DB Graph tömeges végrehajtó mintaalkalmazásGitHub-oldalán.](https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started)

## <a name="bulk-operations-with-graph-data"></a>Tömeges gráfadatműveletek

A [tömeges végrehajtó](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet) tár `Microsoft.Azure.CosmosDB.BulkExecutor.Graph` tartalmaz egy névteret, amely biztosítja a diagramobjektumok létrehozásának és importálásának funkcióit. 

Az alábbi folyamat bemutatja, hogyan használható az adatmigrálás a Gremlin API-tárolók esetében:
1. Rekordok lekérése az adatforrásból.
2. Hozzon létre `GremlinVertex`- és `GremlinEdge`-objektumokat a beszerzett rekordokból, és adja hozzá őket egy `IEnumerable`-adatstruktúrához. Az alkalmazásnak ebben a részében kell megvalósítani a kapcsolatok észlelését és hozzáadását végző logikát, arra az esetre, ha az adatforrás nem egy gráfadatbázis.
3. A [Graph BulkImportAsync metódus](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph.graphbulkexecutor.bulkimportasync?view=azure-dotnet) használatával szúrhatja be a gráfobjektumokat a gyűjteménybe.

Ezzel a mechanizmussal jobb adatmigrálási hatékonyság érhető el, mint a Gremlin-ügyfél használata esetében. Azért tapasztalható ez a javulás, mert a Gremlin használata esetén az alkalmazás egyszerre egy lekérdezést küld le, amelyet ellenőrizni kell, ki kell értékelni, majd végre kell hajtani az adatok létrehozásához. A tömeges végrehajtó könyvtár kezeli az érvényesítést az alkalmazásban, és küld több gráf objektumok egy időben minden hálózati kérelmet.

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

A tömeges végrehajtó könyvtár paramétereiről a [BulkImportData az Azure Cosmos DB témakörében olvashat bővebben.](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account)

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
> A tömeges végrehajtó segédprogram nem ellenőrzi automatikusan a meglévő csúcspontokat az Élek hozzáadása előtt. Ezt az alkalmazásban kell ellenőrizni a BulkImport feladatok futtatása előtt.

## <a name="sample-application"></a>Mintaalkalmazás

### <a name="prerequisites"></a>Előfeltételek
* Visual Studio 2019 az Azure fejlesztési munkaterheléssel. A Visual Studio [2019 Community Edition](https://visualstudio.microsoft.com/downloads/) ingyenesen használható.
* Azure-előfizetés. Itt hozhat létre [ingyenes Azure-fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cosmos-db). Másik lehetőségként létrehozhat egy Cosmos-adatbázis-fiókot [az Azure Cosmos DB-vel ingyenesen](https://azure.microsoft.com/try/cosmosdb/) Azure-előfizetés nélkül.
* Azure Cosmos DB Gremlin API-adatbázis **korlátlan gyűjteménnyel**. Ez az útmutató bemutatja, hogy hogyan foghat neki [az Azure Cosmos DB Gremlin API használatának a .NET környezetben](https://docs.microsoft.com/azure/cosmos-db/create-graph-dotnet).
* Git. További információért tekintse meg a [Git letöltési oldalát](https://git-scm.com/downloads).

### <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása
Ebben az oktatóanyagban az [Azure Cosmos DB Graph tömeges végrehajtó minta](https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started) GitHubon üzemeltetett használatával az első lépések végrehajtásával hajtjuk végre. Ez az alkalmazás egy .NET-megoldást tartalmaz, amely véletlenszerűen állít elő csúcspont és él objektumokat, majd tömeges beszúrásokat hajt végre a megadott gráfadatbázis-fiókba. Az alkalmazás lekéréséhez futtassa az alábbi `git clone` parancsot:

```bash
git clone https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started.git
```

Ez az adattár tartalmazza a GraphBulkExecutor mintát a következő fájlokkal:

Fájl|Leírás
---|---
`App.config`|Itt vannak megadva az alkalmazás- és adatbázis-specifikus paraméterek. Ezt a fájlt előbb módosítani kell, hogy a céladatbázishoz és -gyűjteményekhez csatlakozzon.
`Program.cs`| Ez a fájl tartalmazza `DocumentClient` a gyűjtemény létrehozása, a karbantartások kezelése és a tömeges végrehajtó kérelmek küldése mögötti logikát.
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
* A Nuget csomag részleteiről és a tömeges végrehajtó .NET könyvtár kiadási feljegyzéseiről a [tömeges végrehajtó SDK részletei ben](sql-api-sdk-bulk-executor-dot-net.md)olvashat. 
* Tekintse meg a [teljesítménytippek](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-dot-net#performance-tips) a tömeges végrehajtó használatának további optimalizálásához.
* Az ebben a névtérben definiált osztályokkal és névterekkel kapcsolatos további információért olvassa el [a BulkExecutor.Graph áttekintését tartalmazó cikket](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet).
