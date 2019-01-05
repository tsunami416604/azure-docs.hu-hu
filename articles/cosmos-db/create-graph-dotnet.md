---
title: A Gremlin API használata az Azure Cosmos DB .NET-keretrendszer vagy Core-alkalmazás létrehozása
description: Egy .NET-keretrendszer/Core-kódmintát mutat be, amellyel csatlakozhat egy Cosmos DB-adatbázishoz, és lekérdezéseket hajthat végre.
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/08/2018
ms.author: lbosq
ms.openlocfilehash: d929eb5d9aa8e2e84f76039a6f0ac73a65078957
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54037854"
---
# <a name="azure-cosmos-db-build-a-net-framework-or-core-application-using-the-gremlin-api"></a>Az Azure Cosmos DB: A Gremlin API-val .NET-keretrendszer vagy Core-alkalmazás létrehozása

> [!div class="op_single_selector"]
> * [Gremlin-konzol](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum, kulcs/érték és gráf típusú adatbázisokat, amelyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket. 

Ez a rövid útmutató bemutatja, hogyan hozhat létre az Azure Portal segítségével Azure Cosmos DB [Gremlin API](graph-introduction.md)-fiókot, adatbázist és gráfot (tárolót). Ezután a nyílt forráskódú [Gremlin Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet)-illesztőprogram segítségével létrehozhat és futtathat egy konzolalkalmazást.  

## <a name="prerequisites"></a>Előfeltételek

Ha nincs telepítve a Visual Studio 2017, letöltheti és használhatja az **ingyenes** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)t. Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.

Ha a Visual Studio 2017 már telepítve van, győződjön meg arról, hogy minden frissítés telepítve van a [Visual Studio 2017 Update 3](https://www.visualstudio.com/en-us/news/releasenotes/vs2017-relnotes)-ig bezárólag.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Gráf hozzáadása

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig klónozunk egy Gremlin API-alkalmazást a GitHubról, beállítjuk a kapcsolati sztringet, majd futtatni fogjuk az alkalmazást. Látni fogja, milyen egyszerű az adatokkal programozott módon dolgozni. 

1. Nyisson meg egy parancssort, hozzon létre egy git-samples nevű új mappát, majd zárja be a parancssort.

    ```bash
    md "C:\git-samples"
    ```

2. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson az új mappára, ahol telepíteni szeretné a mintaalkalmazást.

    ```bash
    cd "C:\git-samples"
    ```

3. Futtassa a következő parancsot a minta tárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-gremlindotnet-getting-started.git
    ```

4. Ezután nyissa meg a Visual Studiót, majd a megoldásfájlt.

5. Állítsa vissza a projektben lévő NuGet-csomagokat. Ennek tartalmaznia kell a Gremlin.Net-illesztőprogramot, valamint a Newtonsoft.Json-csomagot.


6. A Gremlin.Net-illesztőprogramot manuálisan is telepítheti a Nuget csomagkezelővel vagy a [nuget parancssori segédprogrammal](https://docs.microsoft.com/nuget/install-nuget-client-tools): 

    ```bash
    nuget install Gremlin.Net
    ```

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha meg szeretné ismerni, hogyan jönnek létre az adatbázis erőforrásai a kódban, tekintse át a következő kódrészleteket. Egyéb esetben ugorhat [A kapcsolati sztring frissítése](#update-your-connection-string) szakaszra. 

Az alábbi kódrészletek mind a Program.cs fájlból származnak.

* Állítsa be a kapcsolat paramétereit a fent létrehozott fiók alapján (19. sor): 

    ```csharp
    private static string hostname = "your-endpoint.gremlin.cosmosdb.azure.com";
    private static int port = 443;
    private static string authKey = "your-authentication-key";
    private static string database = "your-database";
    private static string collection = "your-graph-container";
    ```

* A végrehajtandó Gremlin-parancsok egy szótárban szerepelnek (26. sor):

    ```csharp
    private static Dictionary<string, string> gremlinQueries = new Dictionary<string, string>
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
        { "Traverse",       "g.V('thomas').out('knows').hasLabel('person')" },
        { "Traverse 2x",    "g.V('thomas').out('knows').hasLabel('person').out('knows').hasLabel('person')" },
        { "Loop",           "g.V('thomas').repeat(out()).until(has('id', 'robin')).path()" },
        { "DropEdge",       "g.V('thomas').outE('knows').where(inV().has('id', 'mary')).drop()" },
        { "CountEdges",     "g.E().count()" },
        { "DropVertex",     "g.V('thomas').drop()" },
    };
    ```


* Hozzon létre egy `GremlinServer` kapcsolati objektumot a fent megadott paraméterekkel (52. sor):

    ```csharp
    var gremlinServer = new GremlinServer(hostname, port, enableSsl: true, 
                                                    username: "/dbs/" + database + "/colls/" + collection, 
                                                    password: authKey);
    ```

* Hozzon létre egy új `GremlinClient` objektumot (56. sor):

    ```csharp
    var gremlinClient = new GremlinClient(gremlinServer);
    ```

* Hajtsa végre az egyes Gremlin-lekérdezéseket a `GremlinClient` objektummal egy aszinkron feladatban (63. sor). Ez a fent meghatározott szótárból (26. sor) olvassa be a Gremlin-lekérdezéseket:

    ```csharp
    var results = await gremlinClient.SubmitAsync<dynamic>(query.Value);
    ```

* Kérje le az eredményt, és olvassa be a szótárként formázott értékeket, a Newtonsoft.Json `JsonSerializer` osztályával:

    ```csharp
    foreach (var result in results)
    {
        // The vertex results are formed as dictionaries with a nested dictionary for their properties
        string output = JsonConvert.SerializeObject(result);
        Console.WriteLine(String.Format("\tResult:\n\t{0}", output));
    }
    ```

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Lépjen vissza az Azure Portalra a kapcsolati sztring adataiért, majd másolja be azokat az alkalmazásba.

1. Az [Azure Portalon](https://portal.azure.com/) keresse meg a gráfadatbázis-fiókot. Az **Áttekintés** lapon két végpontot lát: 
 
   **.Net SDK URI** – Ezt az értéket akkor használja, ha Microsoft.Azure.Graphs-kódtárral kapcsolódik a gráffiókhoz. 

   **Gremlin-végpont** – Ezt az értéket akkor használja, ha Gremlin.Net-kódtárral kapcsolódik a gráffiókhoz.

    ![A végpont másolása](./media/create-graph-dotnet/endpoint.png)

   A minta futtatásához másolja ki a **Gremlin-végpont** értékét, és törölje a port számát a végéről. Így az URI a következő lesz: `https://<your cosmos db account name>.gremlin.cosmosdb.azure.com`

2. A Program.cs fájlban illessze be az értéket a `your-endpoint` helyett a 19. sorban lévő `hostname` változóban. 

    `"private static string hostname = "<your cosmos db account name>.gremlin.cosmosdb.azure.com";`

    A végpontértéknek most így kell kinéznie:

    `"private static string hostname = "testgraphacct.gremlin.cosmosdb.azure.com";`

3. Ezután nyissa meg a **Kulcsok** lapot, másolja a portálról az **ELSŐDLEGES KULCS** értékét, és illessze be azt az `authkey` változóba, lecserélve a `"your-authentication-key"` helyőrzőt a 21. sorban. 

    `private static string authKey = "your-authentication-key";`

4. A fent létrehozott adatbázis adatait használva illessze be az adatbázis nevét a 22. sorban lévő `database` változóba. 

    `private static string database = "your-database";`

5. Hasonlóképpen, a fent létrehozott tároló információival illessze be a gyűjteményt (amely a gráf neve is) a 23. sorban található `collection` változóba. 

    `private static string collection = "your-collection-or-graph";`

6. Mentse a Program.cs fájlt. 

Az alkalmazás frissítve lett minden olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. 

## <a name="run-the-console-app"></a>A konzolalkalmazás futtatása

Az alkalmazás futtatásához nyomja le a CTRL + F5 billentyűkombinációt. Az alkalmazás megjeleníti a Gremlin lekérdezési parancsait és a konzolon lévő eredményeket is.

   A konzolablakban megjelennek a gráfhoz hozzáadandó csúcspontok és élek. Miután a szkript futása befejeződött, nyomja le az ENTER billentyűt a konzolablak bezárásához.

## <a name="browse-using-the-data-explorer"></a>Tallózás az Adatkezelővel

Ezután visszaléphet az Adatkezelőbe az Azure Portalon, ahol tallózhatja és lekérdezheti az új gráfadatokat.

1. Az Adatkezelőben az új adatbázis a Gráfok ablaktáblán jelenik meg. Bontsa ki az adatbázis és a tároló csomópontjait, és kattintson a **Gráfra**.

2. Kattintson a **Szűrő alkalmazása** gombra a gráf összes csúcspontjának az alapértelmezett lekérdezéssel történő megtekintéséhez. A mintaalkalmazás által létrehozott adatokat a Gráfok ablaktáblán találja.

    Szabadon nagyíthatja és kicsinyítheti a gráfot, kibonthatja a gráf megjelenítési területét, további csúcspontokat vehet fel, illetve áthelyezheti a csúcspontokat a megjelenítési felületen.

    ![A gráf megtekintése az Azure Portal Adatkezelőjében](./media/create-graph-dotnet/graph-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban bemutattuk, hogyan lehet Azure Cosmos DB-fiókot létrehozni, hogyan lehet az Adatkezelő segítségével gráfot készíteni, és hogyan lehet futtatni az alkalmazást. Most már készen áll arra, hogy a Gremlin használatával összetettebb lekérdezéseket hozzon létre és hatékony gráfbejárási logikákat implementáljon. 

> [!div class="nextstepaction"]
> [Lekérdezés a Gremlin használatával](tutorial-query-graph.md)

