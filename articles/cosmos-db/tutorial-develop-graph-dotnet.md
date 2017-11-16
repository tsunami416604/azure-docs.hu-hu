---
title: "Az Azure Cosmos DB: A Graph API a .NET fejlesztés |} Microsoft Docs"
description: "Ismerje meg, hogyan fejleszthet Azure Cosmos DB DocumentDB API-t a .NET használatával"
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: cc8df0be-672b-493e-95a4-26dd52632261
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: denlee
ms.custom: mvc
ms.openlocfilehash: 613956416d35687c5f2fe0123a9a59182390b440
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="azure-cosmos-db-develop-with-the-graph-api-in-net"></a>Az Azure Cosmos DB: A Graph API a .NET fejlesztés
Azure Cosmos-adatbázis egy Microsoft globálisan elosztott több modellre adatbázis szolgáltatás. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum-, kulcs/érték és gráf típusú adatbázisokat, melyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket. 

Ez az oktatóanyag azt mutatja be, az Azure portál használatával egy Cosmos-DB Azure-fiók létrehozása és egy grafikonon adatbázis és a tároló létrehozása. Az alkalmazás ezután létrehoz egy egyszerű közösségi hálózati használatával négy személyekkel a [Graph API](graph-sdk-dotnet.md) (előzetes verzió), akkor lép át, és lekérdezi a graph használatával Gremlin.

Ez az oktatóanyag ismerteti a következő feladatokat:

> [!div class="checklist"]
> * Azure Cosmos DB-fiók létrehozása 
> * Hozzon létre egy grafikonon adatbázis és a tároló
> * Szerializálható csúcsban és a .NET-objektumokhoz
> * Adja hozzá a csúcsban és élei számára
> * A graph használatával Gremlin lekérdezése

## <a name="graphs-in-azure-cosmos-db"></a>Az Azure Cosmos DB grafikonja
Azure Cosmos DB használatával létrehozása, frissítése és diagramjait használatával lekérdezni a [Microsoft.Azure.Graphs](graph-sdk-dotnet.md) könyvtárban. Egy bővítmény módszert biztosít a Microsoft.Azure.Graph könyvtár `CreateGremlinQuery<T>` a a `DocumentClient` osztály Gremlin lekérdezések végrehajtásához.

Gremlin egy funkcionális programozási nyelv, amely támogatja az írási műveletek (DML) és a lekérdezés- és átjárás műveletei. Ebben a cikkben a elindított Gremlin beolvasandó néhány példa azt foglalkozik. Lásd: [Gremlin lekérdezések](gremlin-support.md) Gremlin lehetőségeinek Azure Cosmos DB részletes útmutatást. 

## <a name="prerequisites"></a>Előfeltételek
Győződjön meg róla, hogy rendelkezik az alábbiakkal:

* Aktív Azure-fiók. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/). 
    * Vagy használhatja az [Azure DocumentDB Emulatort](local-emulator.md) ebben az oktatóanyagban.
* [Visual Studio](http://www.visualstudio.com/).

## <a name="create-database-account"></a>Adatbázis-fiók létrehozása

Először hozzon létre egy Azure Cosmos DB fiókot az Azure portálon.  

> [!TIP]
> * Már van Azure Cosmos DB fiókja? Ha igen, ugorjon előre [a Visual Studio megoldás beállítása](#SetupVS)
> * Ha az Azure Cosmos DB Emulator használ, adja kövesse a [Azure Cosmos DB emulátor](local-emulator.md) kell beállítania az emulátor, és ugorjon előre [a Visual Studio megoldás beállítása](#SetupVS). 
>
> 

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a id="SetupVS"></a>A Visual Studio-megoldás beállítása
1. Nyissa meg a **Visual Studiót** a számítógépén.
2. A **Fájl** menüben válassza az **Új**, majd a **Projekt** elemet.
3. Az a **új projekt** párbeszédablakban válassza **sablonok** / **Visual C#** / **Konzolalkalmazás (.NET-keretrendszer)**, nevezze el a projektet, és kattintson a **OK**.
4. A **Megoldáskezelőben** kattintson a jobb gombbal az új konzolalkalmazásra, amely a Visual Studio megoldás alatt található, majd kattintson a **NuGet-csomagok kezelése...** lehetőségre.
5. Az a **NuGet** lapra, majd **Tallózás**, és írja be **Microsoft.Azure.Graphs** a keresőmezőbe, és ellenőrizze a **előzetes verzióját tartalmazzák**.
6. A találatok között található **Microsoft.Azure.Graphs** kattintson **telepítése**.
   
   Ha a megoldás módosításainak áttekintéséről szóló üzenetet kap, kattintson az **OK** gombra. Ha a licenc elfogadásáról szóló üzenetet kap, kattintson az **Elfogadom** gombra.
   
    A `Microsoft.Azure.Graphs` tár egyetlen bővítmény módszert biztosít a `CreateGremlinQuery<T>` Gremlin műveletek hajthatók végre. Gremlin egy funkcionális programozási nyelv, amely támogatja az írási műveletek (DML) és a lekérdezés- és átjárás műveletei. Ebben a cikkben a elindított Gremlin beolvasandó néhány példa azt foglalkozik. [Gremlin lekérdezések](gremlin-support.md) rendelkezik Gremlin képességek részletes útmutató az Azure Cosmos Adatbázisba.

## <a id="add-references"></a>Az alkalmazás kapcsolódni

Adja hozzá ezt a két állandót és a *ügyfél* változó az alkalmazás. 

```csharp
string endpoint = ConfigurationManager.AppSettings["Endpoint"]; 
string authKey = ConfigurationManager.AppSettings["AuthKey"]; 
``` 
A következő biztonsági head a [Azure-portálon](https://portal.azure.com) a végponti URL-cím és az elsődleges kulcs beolvasása. A végponti URL-cím és az elsődleges kulcs ahhoz szükséges, hogy az alkalmazás tudja, hova kell csatlakoznia, az Azure Cosmos DB pedig megbízzon az alkalmazás által létesített kapcsolatban. 

Az Azure portálon lépjen az Azure Cosmos DB fiókjába, kattintson **kulcsok**, és kattintson a **írható-olvasható kulcsok**. 

Az URI a portálról másolása és beillesztése azt `Endpoint` a fenti az endpoint tulajdonság. Ezután másolja az elsődleges kulcsot a portálról, és illessze be azt a `AuthKey` fenti tulajdonság. 

![Képernyőfelvétel a C#-alkalmazás létrehozása az oktatóanyagban használt Azure-portálon. Azt mutatja be egy Azure Cosmos DB fiók az Azure Cosmos DB navigációs KEYS gomb, és a kulcsok panelen lévő URI és PRIMARY KEY értékek](./media/tutorial-develop-graph-dotnet/keys.png) 
 
## <a id="instantiate"></a>A documentclient ügyfél segítségével hozható létre 
Ezután hozzon létre egy új példányt a **DocumentClient**.  

```csharp 
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey); 
``` 

## <a id="create-database"></a>Adatbázis létrehozása 

Ezután hozzon létre egy Azure Cosmos DB [adatbázis](documentdb-resources.md#databases) használatával a [Documentclient](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) metódus vagy [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) metódusában a **DocumentClient** osztályával a [DocumentDB .NET SDK](documentdb-sdk-dotnet.md).  

```csharp 
Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "graphdb" }); 
``` 
 
## <a name="create-a-graph"></a>Hozzon létre egy grafikonon 

Ezután hozzon létre egy grafikonon tároló használatával, a használatával a [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) metódus vagy [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) metódusában a **DocumentClient** osztály. A gyűjtemény egy grafikonon entitások tároló. 

```csharp 
DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync( 
    UriFactory.CreateDatabaseUri("graphdb"), 
    new DocumentCollection { Id = "graphcollz" }, 
    new RequestOptions { OfferThroughput = 1000 }); 
``` 

## <a id="serializing"></a>Szerializálható csúcsban és a .NET-objektumokhoz
Azure Cosmos-adatbázis használja a [GraphSON egybeírt](gremlin-support.md), amely megadja, hogy a csúcsban, szélek és tulajdonságait egy JSON-séma. Az Azure Cosmos DB .NET SDK magában foglalja a függőség beállításához JSON.NET, és ez lehetővé teszi, hogy a szerializálás/deszerializálás GraphSON .NET objektumot, amely azt használható a kódban.

Tegyük fel most dolgozni egy egyszerű közösségi hálózati négy személyekkel. Úgy tekintünk létrehozása `Person` csúcsban, adja hozzá `Knows` köztük lévő viszonyt is, majd a lekérdezés és haladnak át a diagramot úgy, hogy keresse meg a "" Friend "friend" kapcsolatokat. 

A `Microsoft.Azure.Graphs.Elements` névteret biztosít `Vertex`, `Edge`, `Property` és `VertexProperty` osztályok a .NET jól meghatározott objektumok GraphSON válaszokat deszerializálása során.

## <a name="run-gremlin-using-creategremlinquery"></a>Gremlin CreateGremlinQuery használatával futtassa
Gremlin, például az SQL, olvasási, írási és lekérdezési műveletek támogatja. Például az alábbi kódrészletben láthatja a csúcsban, szélek létrehozása, hajtsa végre az egyes mintalekérdezések hogyan `CreateGremlinQuery<T>`, és aszinkron módon iterációt ezekkel az eredményekkel használatával `ExecuteNextAsync` és "HasMoreResults.

```cs
Dictionary<string, string> gremlinQueries = new Dictionary<string, string>
{
    { "Cleanup",        "g.V().drop()" },
    { "AddVertex 1",    "g.addV('person').property('id', 'thomas').property('firstName', 'Thomas').property('age', 44)" },
    { "AddVertex 2",    "g.addV('person').property('id', 'mary').property('firstName', 'Mary').property('lastName', 'Andersen').property('age', 39)" },
    { "AddVertex 3",    "g.addV('person').property('id', 'ben').property('firstName', 'Ben').property('lastName', 'Miller')" },
    { "AddVertex 4",    "g.addV('person').property('id', 'robin').property('firstName', 'Robin').property('lastName', 'Wakefield')" },
    { "AddEdge 1",      "g.V('thomas').addE('knows').to(g.V('mary'))" },
    { "AddEdge 2",      "g.V('thomas').addE('knows').to(g.V('ben'))" },
    { "AddEdge 3",      "g.V('ben').addE('knows').to(g.V('robin'))" },
    { "UpdateVertex",   "g.V('thomas').property('age', 44)" },
    { "CountVertices",  "g.V().count()" },
    { "Filter Range",   "g.V().hasLabel('person').has('age', gt(40))" },
    { "Project",        "g.V().hasLabel('person').values('firstName')" },
    { "Sort",           "g.V().hasLabel('person').order().by('firstName', decr)" },
    { "Traverse",       "g.V('thomas').outE('knows').inV().hasLabel('person')" },
    { "Traverse 2x",    "g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')" },
    { "Loop",           "g.V('thomas').repeat(out()).until(has('id', 'robin')).path()" },
    { "DropEdge",       "g.V('thomas').outE('knows').where(inV().has('id', 'mary')).drop()" },
    { "CountEdges",     "g.E().count()" },
    { "DropVertex",     "g.V('thomas').drop()" },
};

foreach (KeyValuePair<string, string> gremlinQuery in gremlinQueries)
{
    Console.WriteLine($"Running {gremlinQuery.Key}: {gremlinQuery.Value}");

    // The CreateGremlinQuery method extensions allow you to execute Gremlin queries and iterate
    // results asychronously
    IDocumentQuery<dynamic> query = client.CreateGremlinQuery<dynamic>(graph, gremlinQuery.Value);
    while (query.HasMoreResults)
    {
        foreach (dynamic result in await query.ExecuteNextAsync())
        {
            Console.WriteLine($"\t {JsonConvert.SerializeObject(result)}");
        }
    }

    Console.WriteLine();
}
```

## <a name="add-vertices-and-edges"></a>Adja hozzá a csúcsban és élei számára

Vizsgáljuk meg a további részletek az előző szakaszban látható Gremlin utasításokat. Első azt néhány Gremlin tartozó használatával csúcsban `addV` metódust. Például az alábbi kódrészletben hoz létre a "Személy" típusú "Thomas Andersen" csúcspont utónevét, vezetéknevét és kora tulajdonságait.

```cs
// Create a vertex
IDocumentQuery<Vertex> createVertexQuery = client.CreateGremlinQuery<Vertex>(
    graphCollection, 
    "g.addV('person').property('firstName', 'Thomas')");

while (createVertexQuery.HasMoreResults)
{
    Vertex thomas = (await create.ExecuteNextAsync<Vertex>()).First();
}
```

Ezután azt néhány szélek között ezek csúcsban Gremlin tartozó használatával hoz létre `addE` metódust. 

```cs
// Add a "knows" edge
IDocumentQuery<Edge> createEdgeQuery = client.CreateGremlinQuery<Edge>(
    graphCollection, 
    "g.V('thomas').addE('knows').to(g.V('mary'))");

while (create.HasMoreResults)
{
    Edge thomasKnowsMaryEdge = (await create.ExecuteNextAsync<Edge>()).First();
}
```

Egy meglévő csúcspont segítségével frissítheti azt `properties` Gremlin lépést. Azt a hívást végre a lekérdezés keresztül kihagyása `HasMoreResults` és `ExecuteNextAsync` a többi példák.

```cs
// Update a vertex
client.CreateGremlinQuery<Vertex>(
    graphCollection, 
    "g.V('thomas').property('age', 45)");
```

Szegélyek és Gremlin tartozó használatával csúcsban elvetné `drop` lépés. Íme egy kódrészletet, amely bemutatja, hogyan törölhető egy csomópont és él. Vegye figyelembe, hogy társított széleinek kaszkádolt delete csúcspont eldobása végez.

```cs
// Drop an edge
client.CreateGremlinQuery(graphCollection, "g.E('thomasKnowsRobin').drop()");

// Drop a vertex
client.CreateGremlinQuery(graphCollection, "g.V('robin').drop()");
```

## <a name="query-the-graph"></a>A lekérdezés a diagramhoz

Lekérdezések és traversals Gremlin is használatával végezheti el. Például az alábbi kódrészletben mutatja be a grafikonon csúcsban számát:

```cs
// Run a query to count vertices
IDocumentQuery<int> countQuery = client.CreateGremlinQuery<int>(graphCollection, "g.V().count()");
```
Szűrők Gremlin tartozó használatával végezheti el `has` és `hasLabel` lépéseit, és egyesíthet használatával `and`, `or`, és `not` összetettebb szűrők létrehozásához:

```cs
// Run a query with filter
IDocumentQuery<Vertex> personsByAge = client.CreateGremlinQuery<Vertex>(
  graphCollection, 
  "g.V().hasLabel('person').has('age', gt(40))");
```

Kivetítheti az egyes tulajdonságok a lekérdezés eredményében használatával a `values` . lépés:

```cs
// Run a query with projection
IDocumentQuery<string> firstNames = client.CreateGremlinQuery<string>(
  graphCollection, 
  $"g.V().hasLabel('person').values('firstName')");
```

Eddig is csak láttuk lekérdezési operátorok, amelyek működnek a bármely adatbázis. Diagramok esetén gyors és hatékony átjárás műveleteihez kapcsolódó szélek és csúcsban keresse meg kell. Keressük Thomas összes barátok. Jelenleg ezt úgy teheti meg Gremlin `outE` keresheti meg az összes lépést a Thomas, majd a használatával Gremlin tartozó szegélyek a a-csúcsban bejárása a kimenő éleinek `inV` . lépés:

```cs
// Run a traversal (find friends of Thomas)
IDocumentQuery<Vertex> friendsOfThomas = client.CreateGremlinQuery<Vertex>(
  graphCollection,
  "g.V('thomas').outE('knows').inV().hasLabel('person')");
```

A következő lekérdezés hajtja végre két ugrások található összes Thomas' "ismerősök olyan ismerőseinek", a függvény meghívásával `outE` és `inV` kétszer. 

```cs
// Run a traversal (find friends of friends of Thomas)
IDocumentQuery<Vertex> friendsOfFriendsOfThomas = client.CreateGremlinQuery<Vertex>(
  graphCollection,
  "g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')");
```

Összetettebb lekérdezések létrehozhatja és hatékony graph átjárás logika Gremlin, beleértve a keverése szűrőkifejezéseket, ismétlési használatával végez használatával valósítja meg a `loop` lépés, és végrehajtási feltételes navigációs használ a `choose` lépés. További információ a teendők [Gremlin támogatási](gremlin-support.md)!

Ennyi az egész, az Azure Cosmos DB oktatóanyag befejeződött! 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az alkalmazást már nem használja, a következő lépések használatával törölje az oktatóanyag során létrehozott összes erőforrást az Azure Portalon.  

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a létrehozott erőforrás nevére. 
2. Az erőforráscsoport lapján kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban ezt a következők:

> [!div class="checklist"]
> * Egy Azure Cosmos DB-fiók létrehozása 
> * Egy grafikonon adatbázis és a tároló létrehozása
> * A szerializált csúcsban és a .NET-objektumokhoz
> * A hozzáadott csúcsban és élei számára
> * A graph használatával Gremlin lekérdezése

Most már készen áll arra, hogy a Gremlin használatával összetettebb lekérdezéseket hozzon létre és hatékony gráfbejárási logikákat implementáljon. 

> [!div class="nextstepaction"]
> [Lekérdezés a Gremlin használatával](tutorial-query-graph.md)
