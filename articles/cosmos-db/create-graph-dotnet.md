---
title: "Azure Cosmos DB .NET-keretrendszer vagy Core-alkalmazás létrehozása a Graph API-val | Microsoft Docs"
description: "Egy .NET-keretrendszer/Core-kódmintát mutat be, amellyel csatlakozhat egy Cosmos DB-adatbázishoz, és lekérdezéseket hajthat végre."
services: cosmos-db
documentationcenter: 
author: luisbosquez
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/02/2018
ms.author: lbosq
ms.openlocfilehash: 29153180da576f144a3f21718c3044b7b843eafb
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="azure-cosmos-db-build-a-net-framework-or-core-application-using-the-graph-api"></a>Azure Cosmos DB: .NET-keretrendszer vagy Core-alkalmazás létrehozása a Graph API használatával

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum, kulcs/érték és gráf típusú adatbázisokat, amelyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket. 

A bevezető bemutatja, hogyan hozhat létre az Azure Portal segítségével Azure Cosmos DB-fiókot, adatbázist és gráfot (tárolót). Ezután megtudhatja hogyan hozhat létre és futtathat egy, a [Graph API-val](graph-sdk-dotnet.md) létrehozott konzolalkalmazást.  

## <a name="prerequisites"></a>Előfeltételek

Ha nincs telepítve a Visual Studio 2017, letöltheti és használhatja az **ingyenes** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)t. Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.

Ha a Visual Studio 2017 már telepítve van, győződjön meg arról, hogy minden frissítés telepítve van a [Visual Studio 2017 Update 3](https://www.visualstudio.com/en-us/news/releasenotes/vs2017-relnotes)-ig bezárólag.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Gráf hozzáadása

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig klónozunk egy Graph API-alkalmazást a GitHubról, beállítjuk a kapcsolati karakterláncot, majd futtatni fogjuk az alkalmazást. Látni fogja, milyen egyszerű az adatokkal programozott módon dolgozni. 

A mintaprojekt a .NET Core projektformátumot használja, és úgy lett beállítva, hogy a következő keretrendszereket célozza:
 - netcoreapp2.0
 - net461

1. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` paranccsal lépjen egy munkakönyvtárba.  

2. Futtassa a következő parancsot a mintatárház klónozásához. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-dotnet-getting-started.git
    ```

3. Ezután nyissa meg a Visual Studiót, majd a megoldásfájlt. 

## <a name="review-the-code"></a>A kód áttekintése

Tekintsük át, hogy mi történik az alkalmazásban. Nyissa meg a Program.cs fájlt: az itt található kódsorok hozzák létre az Azure Cosmos DB erőforrásokat. 

* A DocumentClient inicializálva van. Az előzetes verzióban hozzáadtunk egy gráfbővítmény API-t az Azure Cosmos DB-ügyfélhez. Jelenleg egy különálló gráfügyfélen dolgozunk, amely az Azure Cosmos DB-ügyféltől és annak erőforrásaitól függetlenül működik.

    ```csharp
    using (DocumentClient client = new DocumentClient(
        new Uri(endpoint),
        authKey,
        new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp }))
    ```

* A rendszer létrehozza az új adatbázist.

    ```csharp
    Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "graphdb" });
    ```

* A rendszer létrehozza az új gráfot.

    ```csharp
    DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri("graphdb"),
        new DocumentCollection { Id = "graph" },
        new RequestOptions { OfferThroughput = 1000 });
    ```
* A `CreateGremlinQuery` metódus használatával a program Gremlinnel kapcsolatos lépések sorozatát hajtja végre.

    ```csharp
    // The CreateGremlinQuery method extensions allow you to execute Gremlin queries and iterate
    // results asychronously
    IDocumentQuery<dynamic> query = client.CreateGremlinQuery<dynamic>(graph, "g.V().count()");
    while (query.HasMoreResults)
    {
        foreach (dynamic result in await query.ExecuteNextAsync())
        {
            Console.WriteLine($"\t {JsonConvert.SerializeObject(result)}");
        }
    }

    ```

## <a name="update-your-connection-string"></a>A kapcsolati karakterlánc frissítése

Lépjen vissza az Azure Portalra a kapcsolati karakterlánc adataiért, majd másolja be azokat az alkalmazásba.

1. Az [Azure Portalon](http://portal.azure.com/) kattintson a **Kulcsok** lehetőségre. 

    Másolja az URI érték első részét.

    ![Hozzáférési kulcs megtekintése és másolása az Azure Portal Kulcsok oldalán](./media/create-graph-dotnet/keys.png)

2. Nyissa meg a appsettings.json fájlt a Visual Studio 2017-ben, majd illessze be az értéket a `FILLME` helyére a következőben: `endpoint`. 

    `"endpoint": "https://FILLME.documents.azure.com:443/",`

    A végpontértéknek most így kell kinéznie:

    `"endpoint": "https://testgraphacct.documents.azure.com:443/",`

3. Ha a gráfadatbázis-fiókot 2017. november 27. előtt hozta létre, módosítsa az `endpoint` értékét `documents` helyett `graphs`ra. Ha a gráfadatbázis-fiókot 2017. november 27-én vagy később hozta létre, módosítsa az `endpoint` értékét `documents` helyett a következőre: `gremlin.cosmosdb`.

    A végpontértéknek most így kell kinéznie:

    `"endpoint": "https://testgraphacct.graphs.azure.com:443/",` vagy `"endpoint": "https://testgraphacct.gremlin.cosmosdb.azure.com:443/",`

4. Másolja az **ELSŐDLEGES KULCS** értékét a portálról, és adja meg az App.config fájl AuthKey kulcsaként, majd mentse a módosításokat. 

    `"authkey": "FILLME"`

5. Mentse az appsettings.json fájlt. 

Az alkalmazás frissítve lett minden olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. 

## <a name="run-the-console-app"></a>A konzolalkalmazás futtatása

Az alkalmazás futtatása előtt javasolt a *Microsoft.Azure.Graphs* csomag legújabb verzióra történő frissítése.

1. A Visual Studióban kattintson a jobb gombbal a **GraphGetStarted** projektre a **Megoldáskezelőben**, majd kattintson a **NuGet-csomagok kezelése** elemre. 

2. A NuGet csomagkezelő **Tallózás** mezőjébe írja be *Microsoft.Azure.Graphs* kifejezést, és jelölje be az **Előzetes verzió is** jelölőnégyzetet. 

3. Az eredményekből frissítse a **Microsoft.Azure.Graphs** kódtárat a csomag legújabb verziójára. Ezzel telepíti az Azure Cosmos DB gráfbővítmény kódtárcsomagja és annak összes függőségét.

    Ha a megoldás módosításainak áttekintéséről szóló üzenetet kap, kattintson az **OK** gombra. Ha a licenc elfogadásáról szóló üzenetet kap, kattintson az **Elfogadom** gombra.

4. Az alkalmazás futtatásához nyomja le a CTRL + F5 billentyűkombinációt.

   A konzolablakban megjelennek a gráfhoz hozzáadandó csúcspontok és élek. Miután a parancsfájl futása befejeződött, nyomja meg kétszer az ENTER billentyűt a konzolablak bezárásához.

## <a name="browse-using-the-data-explorer"></a>Tallózás az Adatkezelővel

Ezután visszaléphet az Adatkezelőbe az Azure Portalon, ahol tallózhatja és lekérdezheti az új gráfadatokat.

1. Az Adatkezelőben az új adatbázis a Gráfok ablaktáblán jelenik meg. Bontsa ki a **graphdb**, **graphcollz** pontokat, és kattintson a **Gráf** lehetőségre.

2. Kattintson a **Szűrő alkalmazása** gombra a gráf összes csúcspontjának megtekintéséhez az alapértelmezett lekérdezéssel. A mintaalkalmazás által létrehozott adatokat a Gráfok ablaktáblán találja.

    Szabadon nagyíthatja és kicsinyítheti a gráfot, kibonthatja a gráf megjelenítési területét, további csúcspontokat vehet fel, illetve áthelyezheti a csúcspontokat a megjelenítési felületen.

    ![A gráf megtekintése az Azure Portal Adatkezelőjében](./media/create-graph-dotnet/graph-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az alkalmazást már nem használja, akkor a következő lépésekkel a mintaalkalmazás által létrehozott összes erőforrást törölheti az Azure Portalon: 

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a létrehozott erőforrás nevére. 
2. Az erőforráscsoport lapján kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban bemutattuk, hogyan lehet Azure Cosmos DB-fiókot létrehozni, hogyan lehet az Adatkezelő segítségével gráfot készíteni, és hogyan lehet futtatni az alkalmazást. Most már készen áll arra, hogy a Gremlin használatával összetettebb lekérdezéseket hozzon létre és hatékony gráfbejárási logikákat implementáljon. 

> [!div class="nextstepaction"]
> [Lekérdezés a Gremlin használatával](tutorial-query-graph.md)

