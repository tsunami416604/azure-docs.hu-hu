---
title: Azure Cosmos DB .NET Framework, Core alkalmazás létrehozása a Gremlin API használatával
description: Egy .NET-keretrendszer/Core-kódmintát mutat be, amellyel csatlakozhat egy Cosmos DB-adatbázishoz, és lekérdezéseket hajthat végre.
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 02/21/2020
ms.author: lbosq
ms.openlocfilehash: bf453587b354b5db3f3ef1a80f974bcb8f8f4e14
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730023"
---
# <a name="quickstart-build-a-net-framework-or-core-application-using-the-azure-cosmos-db-gremlin-api-account"></a>Rövid útmutató: .NET-keretrendszer vagy core alkalmazás létrehozása az Azure Cosmos DB Gremlin API-fiókkal

> [!div class="op_single_selector"]
> * [Gremlin-konzol](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum-, kulcs/érték és gráf típusú adatbázisokat, melyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket. 

Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Azure Cosmos DB [Gremlin API-fiókot,](graph-introduction.md) adatbázist és grafikont (tárolót) az Azure Portalon. Ezután a nyílt forráskódú [Gremlin Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet)-illesztőprogram segítségével létrehozhat és futtathat egy konzolalkalmazást.  

## <a name="prerequisites"></a>Előfeltételek

Ha még nincs telepítve a Visual Studio 2019, letöltheti és használhatja az **ingyenes** [Visual Studio 2019 Community Edition alkalmazást.](https://www.visualstudio.com/downloads/) Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Gráf hozzáadása

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig klónozunk egy Gremlin API-alkalmazást a GitHubról, beállítjuk a kapcsolati sztringet, majd futtatni fogjuk az alkalmazást. Látni fogja, mennyire egyszerű programozott módon dolgozni az adatokkal. 

1. Nyisson meg egy parancssort, hozzon létre egy git-samples nevű mappát, majd zárja be a parancssort.

    ```bash
    md "C:\git-samples"
    ```

2. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson az új mappára, ahol telepíteni szeretné a mintaalkalmazást.

    ```bash
    cd "C:\git-samples"
    ```

3. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

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

* Állítsa be a kapcsolat paramétereit a fent létrehozott fiók alapján: 

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="configureConnectivity":::

* A végrehajtandó Gremlin parancsok egy szótárban vannak felsorolva:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="defineQueries":::

* Hozzon `GremlinServer` létre `GremlinClient` egy új és kapcsolatobjektumokat a fent megadott paraméterekkel:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="defineClientandServerObjects":::

* Minden Gremlin-lekérdezést `GremlinClient` aszinkron feladattal rendelkező objektum használatával hajtson végre. A Gremlin-lekérdezéseket az előző lépésben definiált szótárból olvashatja el, és végrehajthatja őket. Később kap az eredményt, és olvassa el az értékeket, `JsonSerializer` amelyek formázott szótár, az osztály Newtonsoft.Json csomag:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="executeQueries":::

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Lépjen vissza az Azure Portalra a kapcsolati sztring adataiért, majd másolja be azokat az alkalmazásba.

1. Az [Azure Portalon](https://portal.azure.com/) keresse meg a gráfadatbázis-fiókot. Az **Áttekintés** lapon két végpontot lát: 
 
   **.NET SDK URI** – Ez az érték akkor használatos, amikor a Microsoft.Azure.Graphs függvénytár használatával csatlakozik a gráffiókhoz. 

   **Gremlin-végpont** – Ezt az értéket akkor használja, ha Gremlin.Net-kódtárral kapcsolódik a gráffiókhoz.

    ![A végpont másolása](./media/create-graph-dotnet/endpoint.png)

   A minta futtatásához másolja a **Gremlin végpont** értékét, törölje a portszámot `https://<your cosmos db account name>.gremlin.cosmosdb.azure.com`a végén, azaz az URI lesz . A végpontértéknek így kell kinéznie`testgraphacct.gremlin.cosmosdb.azure.com`

1. Ezután keresse meg a **Kulcsok** lapot, és másolja a **PRIMER KULCS** értékét az Azure Portalról. 

1. Miután átmásolta a fiók URI-ját és elsődleges kulcsát, mentse őket egy új környezeti változóba az alkalmazást futtató helyi számítógépen. A környezeti változó beállításához nyisson meg egy parancssorablakot, és futtassa a következő parancsot. Győződjön meg arról, hogy <Your_Azure_Cosmos_account_URI> és Your_Azure_Cosmos_account_PRIMARY_KEY> értékeket <Your_Azure_Cosmos_account_PRIMARY_KEY cseréli le.

   ```console
   setx EndpointUrl "<your Azure Cosmos account name>.gremlin.cosmosdb.azure.com"
   setx PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
   ```

1. Nyissa meg a *Program.cs* fájlt, és frissítse az "adatbázis és a "tároló" változókat a fent létrehozott adatbázis- és tárolónévvel (amely egyben a grafikon neve is).

    `private static string database = "your-database-name";` `private static string container = "your-container-or-graph-name";`

1. Mentse a Program.cs fájlt. 

Ezzel frissítette az alkalmazást az összes olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. 

## <a name="run-the-console-app"></a>A konzolalkalmazás futtatása

Az alkalmazás futtatásához nyomja le a CTRL + F5 billentyűkombinációt. Az alkalmazás megjeleníti a Gremlin lekérdezési parancsait és a konzolon lévő eredményeket is.

   A konzolablakban megjelennek a gráfhoz hozzáadandó csúcspontok és élek. Miután a szkript futása befejeződött, nyomja le az ENTER billentyűt a konzolablak bezárásához.

## <a name="browse-using-the-data-explorer"></a>Tallózás az Adatkezelővel

Ezután visszaléphet az Adatkezelőbe az Azure Portalon, ahol tallózhatja és lekérdezheti az új gráfadatokat.

1. Az Adatkezelőben az új adatbázis a Gráfok ablaktáblán jelenik meg. Bontsa ki az adatbázis és a tároló csomópontjait, és kattintson a **Gráfra**.

2. Kattintson a **Szűrő alkalmazása** gombra a gráf összes csúcspontjának az alapértelmezett lekérdezéssel történő megtekintéséhez. A mintaalkalmazás által létrehozott adatokat a Gráfok ablaktáblán találja.

    Szabadon nagyíthatja és kicsinyítheti a gráfot, kibonthatja a gráf megjelenítési területét, további csúcspontokat vehet fel, illetve áthelyezheti a csúcspontokat a megjelenítési felületen.

    ![A gráf megtekintése az Azure Portal Adatkezelőjében](./media/create-graph-dotnet/graph-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban bemutattuk, hogyan lehet Azure Cosmos DB-fiókot létrehozni, hogyan lehet az Adatkezelő segítségével gráfot készíteni, és hogyan lehet futtatni az alkalmazást. Az útmutató információira támaszkodva összetett lekérdezéseket hozhat létre és hatékony gráfbejárási logikákat helyezhet üzembe a Gremlin használatával. 

> [!div class="nextstepaction"]
> [Lekérdezés a Gremlin használatával](tutorial-query-graph.md)

