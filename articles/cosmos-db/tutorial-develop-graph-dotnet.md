---
title: 'Azure Cosmos DB: Fejlesztés a Graph API-val .NET-keretrendszerben | Microsoft Docs'
description: Arra vonatkozó ismeretek, hogyan lehet fejlesztési műveleteket végrehajtani az Azure Cosmos DB SQL API-jával a .NET-keretrendszerben
services: cosmos-db
documentationcenter: ''
author: luisbosquez
manager: kfile
editor: ''
ms.assetid: cc8df0be-672b-493e-95a4-26dd52632261
ms.service: cosmos-db
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: a442b6c3c8e2b8a781ee54f41a2e0db5b44b7395
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="azure-cosmos-db-develop-with-the-graph-api-in-net"></a>Azure Cosmos DB: Fejlesztés a Graph API-val .NET-keretrendszerben
Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum, kulcs/érték és gráf típusú adatbázisokat, amelyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket. 

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre az Azure Portalon egy Azure Cosmos DB-fiókot, majd egy gráfadatbázist és egy tárolót. Az alkalmazás ezután a [Graph API](graph-sdk-dotnet.md) használatával létrehoz egy egyszerű közösségi hálózatot négy személlyel, majd bejárja és lekérdezi a gráfot a Gremlin használatával.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Azure Cosmos DB-fiók létrehozása 
> * Gráfadatbázis és tároló létrehozása
> * Csúcsok és élek szerializálása .NET-objektumokká
> * Csúcsok és élek hozzáadása
> * A gráf lekérdezése a Gremlin használatával

## <a name="graphs-in-azure-cosmos-db"></a>Gráfok az Azure Cosmos DB-ben
Az Azure Cosmos DB használatával gráfokat hozhat létre, frissíthet és kérdezhet le a [Microsoft.Azure.Graphs](graph-sdk-dotnet.md) kódtár segítségével. A Microsoft.Azure.Graph kódtár egy egybővítményes `CreateGremlinQuery<T>` metódust biztosít a `DocumentClient` osztályon a Gremlin-lekérdezések végrehajtásához.

A Gremlin egy funkcionális programozási nyelv, amely írási műveleteket (DML), valamint lekérdezési és bejárási műveleteket támogat. Az ebben a cikkben bemutatott példák segítenek elindulni a Gremlin használatával. A Gremlin az Azure Cosmos DB-ben elérhető képességeinek részletes bemutatásáért lásd: [Gremlin-lekérdezések](gremlin-support.md). 

## <a name="prerequisites"></a>Előfeltételek
Győződjön meg róla, hogy rendelkezik az alábbiakkal:

* Aktív Azure-fiók. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/). 
    * Vagy használhatja a [helyi emulátort](local-emulator.md) ebben az oktatóanyagban.
* [Visual Studio](http://www.visualstudio.com/).

## <a name="create-database-account"></a>Adatbázisfiók létrehozása

Először hozzon létre egy Azure Cosmos DB-fiókot az Azure Portalon.  

> [!TIP]
> * Már rendelkezik Azure Cosmos DB-fiókkal? Ez esetben ugorjon [A Visual Studio-megoldás beállítása](#SetupVS) című részhez.
> * Ha az Azure Cosmos DB Emulatort használja, kövesse az [Azure Cosmos DB Emulatornál](local-emulator.md) leírt lépéseket az emulátor telepítéséhez, majd ugorjon előre [A Visual Studio-megoldás beállítása](#SetupVS) című lépésre. 
>
> 

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a id="SetupVS"></a>A Visual Studio-megoldás beállítása
1. Nyissa meg a **Visual Studiót** a számítógépén.
2. A **Fájl** menüben válassza az **Új**, majd a **Projekt** elemet.
3. Az **Új projekt** párbeszédpanelen válassza a **Sablonok** / **Visual C#** / **Konzolalkalmazás (.NET-keretrendszer)** elemet, nevezze el a projektet, majd kattintson az **OK** gombra.
4. A **Megoldáskezelőben** kattintson a jobb gombbal az új konzolalkalmazásra, amely a Visual Studio megoldás alatt található, majd kattintson a **NuGet-csomagok kezelése...** lehetőségre.
5. A **NuGet** lapon kattintson a **Tallózás** elemre, majd írja be a **Microsoft.Azure.Graphs** kifejezést a keresőmezőbe, és jelölje be az **Include prerelease versions** (Előzetes verziók is) beállítást.
6. A találatok között keresse meg a **Microsoft.Azure.Graphs** elemet, majd kattintson a **Telepítés** elemre.
   
   Ha a megoldás módosításainak áttekintéséről szóló üzenetet kap, kattintson az **OK** gombra. Ha a licenc elfogadásáról szóló üzenetet kap, kattintson az **Elfogadom** gombra.
   
    A `Microsoft.Azure.Graphs` kódtár egy egybővítményes `CreateGremlinQuery<T>` metódust biztosít a Gremlin-lekérdezések végrehajtásához. A Gremlin egy funkcionális programozási nyelv, amely írási műveleteket (DML), valamint lekérdezési és bejárási műveleteket támogat. Az ebben a cikkben bemutatott példák segítenek elindulni a Gremlin használatával. A [Gremlin-lekérdezések](gremlin-support.md) cikk részletesen bemutatja a Gremlin az Azure Cosmos DB-ben elérhető képességeit.

## <a id="add-references"></a>Az alkalmazás csatlakoztatása

Adja hozzá ezt a két állandót és a *client* (ügyfél) változót az alkalmazáshoz. 

```csharp
string endpoint = ConfigurationManager.AppSettings["Endpoint"]; 
string authKey = ConfigurationManager.AppSettings["AuthKey"]; 
``` 
Ezután lépjen vissza az [Azure Portalra](https://portal.azure.com) a végponti URL-cím és az elsődleges kulcs beszerzéséért. A végponti URL-cím és az elsődleges kulcs ahhoz szükséges, hogy az alkalmazás tudja, hova kell csatlakoznia, az Azure Cosmos DB pedig megbízzon az alkalmazás által létesített kapcsolatban. 

Az Azure Portalon lépjen az Azure Cosmos DB-fiókra, és kattintson a **Kulcsok**, majd az **Írási/olvasási kulcsok** elemre. 

Másolja ki az URI-t a portálról, és illessze be az `Endpoint` helyére a fenti végpont tulajdonságban. Ezután másolja ki a PRIMARY KEY kulcsot a portálról, és illessze be a fenti `AuthKey` tulajdonságba. 

![Képernyőkép az oktatóanyagban a C#-alkalmazás létrehozásához használt Azure Portalról. Megjelenít egy Azure Cosmos DB-fiókot, amelyen az Azure Cosmos DB navigációs felületén lévő KEYS gomb, valamint a Kulcsok panelen lévő URI és PRIMARY KEY értékek vannak kiemelve](./media/tutorial-develop-graph-dotnet/keys.png) 
 
## <a id="instantiate"></a>A DocumentClient példányának létrehozása 
Ezután hozza létre a **DocumentClient** egy új példányát.  

```csharp 
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey); 
``` 

## <a id="create-database"></a>Adatbázis létrehozása 

Most hozzon létre egy Azure Cosmos DB-[adatbázist](sql-api-resources.md#databases) a **DocumentClient** osztály [CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) vagy [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) metódusának használatával az [SQL .NET SDK](sql-api-sdk-dotnet.md)-ból.  

```csharp 
Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "graphdb" }); 
``` 
 
## <a name="create-a-graph"></a>Gráf létrehozása 

Következő lépésként hozzon létre egy gráftárolót a **DocumentClient** osztály [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) vagy [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) metódusával. A gyűjtemény egy gráfentitásokat tartalmazó tároló. 

```csharp 
DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync( 
    UriFactory.CreateDatabaseUri("graphdb"), 
    new DocumentCollection { Id = "graphcollz" }, 
    new RequestOptions { OfferThroughput = 400 }); 
``` 

## <a id="serializing"></a>Csúcsok és élek szerializálása .NET-objektumokká
Az Azure Cosmos DB a [GraphSON küldési formátumot](gremlin-support.md) használja, amely egy JSON-sémát definiál a csúcsok, élek és tulajdonságok számára. Az Azure Cosmos DB .NET SDK függőségként tartalmazza a JSON.NET-et, aminek köszönhetően a GraphSON .NET-objektumokká szerializálható/deszerializható, amelyek aztán a kódban használhatók.

Példaként vegyünk most egy négy személyt magában foglaló egyszerű közösségi hálózatot. Megvizsgáljuk, hogyan lehet `Person` csúcsokat létrehozni, `Knows` kapcsolatokat felvenni közöttük, majd lekérdezni és bejárni a gráfot az „ismerős ismerőse" kapcsolatok feltérképezésére. 

A `Microsoft.Azure.Graphs.Elements` névtér `Vertex`, `Edge`, `Property` és `VertexProperty` osztályokat biztosít a GraphSON-válaszok jól definiált .NET-objektumokká való deszerializálásához.

## <a name="run-gremlin-using-creategremlinquery"></a>A Gremlin futtatása a CreateGremlinQuery használatával
Az SQL-hez hasonlóan a Gremlin is az olvasás, írás és lekérdezés műveleteket támogatja. Az alábbi kódrészlet például bemutatja, hogyan lehet csúcsokat és éleket létrehozni, néhány mintalekérdezést végrehajtani a `CreateGremlinQuery<T>` használatával, valamint aszinkron módon végigvenni ezeket az eredményeket az `ExecuteNextAsync` és a `HasMoreResults` használatával.

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

## <a name="add-vertices-and-edges"></a>Csúcsok és élek hozzáadása

Most lássuk részletesebben az előző szakaszban bemutatott Gremlin-utasításokat. Először létrehozunk néhány csúcspontot a Gremlin `addV` metódusával. Az alábbi kódrészlet például létrehoz egy „Személy” típusú „Thomas Andersen” csúcspontot utónév, vezetéknév és életkor tulajdonságokkal.

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

Ezután a Gremlin `addE` metódusával létrehozunk néhány élt a csúcspontok közt. 

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

A meglévő csúcspontok a Gremlinben a `properties` lépéssel frissíthetők. A további példákban kihagyjuk azt a részt, amely a `HasMoreResults` és az `ExecuteNextAsync` használatával végrehajtja a lekérdezést.

```cs
// Update a vertex
client.CreateGremlinQuery<Vertex>(
    graphCollection, 
    "g.V('thomas').property('age', 45)");
```

Az élek és a csúcspontok a Gremlin `drop` lépésével vethetők el. Az alábbi kódrészlet a csúcsok és élek törlését mutatja be. Vegye figyelembe, hogy a csúcsok elvetése maga után vonja a társított élek törlését is.

```cs
// Drop an edge
client.CreateGremlinQuery(graphCollection, "g.E('thomasKnowsRobin').drop()");

// Drop a vertex
client.CreateGremlinQuery(graphCollection, "g.V('robin').drop()");
```

## <a name="query-the-graph"></a>A gráf lekérdezése

A lekérdezéseket és bejárásokat szintén a Gremlin használatával hajthatja végre. A következő kódrészlet például azt mutatja be, hogyan számlálható meg a csúcspontok száma a gráfban:

```cs
// Run a query to count vertices
IDocumentQuery<int> countQuery = client.CreateGremlinQuery<int>(graphCollection, "g.V().count()");
```
A Gremlin `has` és `hasLabel` lépéseivel végezhet szűrést, és azokat az `and`, `or` és `not` operátorokkal kombinálva összetettebb szűrőket hozhat létre:

```cs
// Run a query with filter
IDocumentQuery<Vertex> personsByAge = client.CreateGremlinQuery<Vertex>(
  graphCollection, 
  "g.V().hasLabel('person').has('age', gt(40))");
```

A `values` lépéssel megkaphatja bizonyos tulajdonságok vetületét a lekérdezés eredményeiben:

```cs
// Run a query with projection
IDocumentQuery<string> firstNames = client.CreateGremlinQuery<string>(
  graphCollection, 
  $"g.V().hasLabel('person').values('firstName')");
```

Eddig csak minden adatbázisban működő lekérdezési operátorokat láttunk. A gráfok gyors és hatékony módjai az bejárási műveleteknek, amikor kapcsolódó élekhez vagy csúcspontokhoz kell navigálni. Keressük meg Thomas összes barátját. Ezt a Gremlin `outE` lépésével tesszük meg, hogy megtaláljuk Thomas összes külső élét, majd ezekről az élekről áthaladjunk a belső csúcspontokra a Gremlin `inV` lépésével:

```cs
// Run a traversal (find friends of Thomas)
IDocumentQuery<Vertex> friendsOfThomas = client.CreateGremlinQuery<Vertex>(
  graphCollection,
  "g.V('thomas').outE('knows').inV().hasLabel('person')");
```

A következő lekérdezés két ugrást végez Thomas összes „barátja barátainak” megkereséséhez az `outE` és `inV` kétszeri hívásával. 

```cs
// Run a traversal (find friends of friends of Thomas)
IDocumentQuery<Vertex> friendsOfFriendsOfThomas = client.CreateGremlinQuery<Vertex>(
  graphCollection,
  "g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')");
```

A Gremlin használatával összetettebb lekérdezéseket hozhat létre és hatékony gráfbejárási logikákat implementálhat, beleértve a szűrőkifejezések keverését, a hurkolás elvégzését a `loop` lépéssel, valamint a feltételes navigáció implementálását a `choose` lépéssel. A [Gremlin-támogatás](gremlin-support.md) segítségével további lehetőségeket ismerhet meg.

Ennyi az egész. Végeztünk is ezzel az Azure Cosmos DB-oktatóanyaggal! 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az alkalmazást már nem használja, a következő lépések használatával törölje az oktatóanyag során létrehozott összes erőforrást az Azure Portalon.  

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a létrehozott erőforrás nevére. 
2. Az erőforráscsoport lapján kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következőket hajtotta végre:

> [!div class="checklist"]
> * Létrehozott egy Azure Cosmos DB-fiókot 
> * Létrehozott egy gráfadatbázist és egy tárolót
> * Csúcsokat és éleket szerializált .NET-objektumokká
> * Csúcsokat és éleket adott hozzá
> * Lekérdezte a gráfot a Gremlin használatával

Most már készen áll arra, hogy a Gremlin használatával összetettebb lekérdezéseket hozzon létre és hatékony gráfbejárási logikákat implementáljon. 

> [!div class="nextstepaction"]
> [Lekérdezés a Gremlin használatával](tutorial-query-graph.md)
