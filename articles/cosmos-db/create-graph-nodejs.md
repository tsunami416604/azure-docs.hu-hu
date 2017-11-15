---
title: "Azure Cosmos DB-alapú Node.js-alkalmazás létrehozása a Graph API-val | Microsoft Docs"
description: "A cikk egy Node.js-kódmintát mutat be, amellyel csatlakozhat egy Azure Cosmos DB-adatbázishoz, és lekérdezéseket hajthat végre"
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 08/29/2017
ms.author: denlee
ms.openlocfilehash: 361f63141a8bf3f901eee6c93742f1a7fdc4348f
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2017
---
# <a name="azure-cosmos-db-build-a-nodejs-application-by-using-graph-api"></a>Azure Cosmos DB: Node.js-alkalmazás létrehozása a Graph API-val

Azure Cosmos-adatbázis a Microsoft globálisan elosztott multimodel adatbázis-szolgáltatás. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum-, kulcs/érték és gráf típusú adatbázisokat, melyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket. 

Ez a gyors üzembe helyezés cikk bemutatja, hogyan Graph API-val (előzetes verzió), az adatbázis és a graph Azure Cosmos DB fiók létrehozása az Azure portál használatával. Ezután a nyílt forráskódú [Gremlin Node.js](https://www.npmjs.com/package/gremlin)-illesztőprogram segítségével létrehozhat és futtathat egy konzolalkalmazást.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt futtathatná ezt a mintát, rendelkeznie kell a következő előfeltételekkel:
* [Node.js](https://nodejs.org/en/)-verzió: 0.10.29-es vagy újabb
* [Git](http://git-scm.com/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Gráf hozzáadása

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

A következő lépésekben elvégezheti a Graph API-alkalmazás klónozását a GitHubról, beállíthatja a kapcsolati sztringet, és futtathatja az alkalmazást. Látni fogja, milyen egyszerű az adatokkal programozott módon dolgozni. 

1. Nyisson meg egy Git-terminálablakot, például a Git Bash eszközt, és a `cd` paranccsal váltson egy munkakönyvtárra.

2. A következő parancsot a minta-tárház klónozása: 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. Nyissa meg a megoldásfájlt a Visual Studióban. 

## <a name="review-the-code"></a>A kód áttekintése

Tekintsük át, hogy mi történik az alkalmazásban. Nyissa meg a `app.js` fájlt, és tekintse meg a következő sorokat. 

* Létrejön a Gremlin-ügyfél.

    ```nodejs
    const client = Gremlin.createClient(
        443, 
        config.endpoint, 
        { 
            "session": false, 
            "ssl": true, 
            "user": `/dbs/${config.database}/colls/${config.collection}`,
            "password": config.primaryKey
        });
    ```

  A konfigurációk tartoznak `config.js`, mely azt a a [a következő szakasz](#update-your-connection-string).

* Funkciók több különböző Gremlin műveletek végrehajtásához vannak definiálva. Ez az egyik legyen:

    ```nodejs
    function addVertex1(callback)
    {
        console.log('Running Add Vertex1'); 
        client.execute("g.addV('person').property('id', 'thomas').property('firstName', 'Thomas').property('age', 44).property('userid', 1)", { }, (err, results) => {
          if (err) callback(console.error(err));
          console.log("Result: %s\n", JSON.stringify(results));
          callback(null)
        });
    }
    ```

* Mindkét funkciót hajt végre egy `client.execute` Gremlin lekérdezési karakterlánc paraméter metódust. Íme egy példa bemutatja, hogyan `g.V().count()` végrehajtása:

    ```nodejs
    console.log('Running Count'); 
    client.execute("g.V().count()", { }, (err, results) => {
        if (err) return console.error(err);
        console.log(JSON.stringify(results));
        console.log();
    });
    ```

* A fájl végén módszer közül mindegyik majd hívják használatával a `async.waterfall()` metódust. Ez hajtja végre őket egymás után:

    ```nodejs
    try{
        async.waterfall([
            dropGraph,
            addVertex1,
            addVertex2,
            addEdge,
            countVertices
            ], finish);
    } catch(err) {
        console.log(err)
    }
    ```


## <a name="update-your-connection-string"></a>A kapcsolati karakterlánc frissítése

1. Nyissa meg a config.js fájlt. 

2. A config.js, töltse ki a `config.endpoint` a kulcs a **Gremlin URI** értéket a **áttekintése** az Azure-portálon oldalán. 

    `config.endpoint = "GRAPHENDPOINT";`

    ![Hozzáférési kulcs megtekintése és másolása az Azure Portal kulcsok paneljén](./media/create-graph-nodejs/gremlin-uri.png)

   Ha a **Gremlin URI** nincs megadva érték, a értéket is létrehozhat a **kulcsok** oldal a portálon. Használja a **URI** érték, távolítsa el a https:// és diagramokat a dokumentumok módosítására.

   A Gremlin-végpont csak protokoll/portszám nélküli gazdagépnév lehet, például `mygraphdb.graphs.azure.com` (és nem `https://mygraphdb.graphs.azure.com` vagy `mygraphdb.graphs.azure.com:433`).

3. A config.js, töltse ki a config.primaryKey értékét a **elsődleges kulcs** értéket a **kulcsok** az Azure-portálon oldalán. 

    `config.primaryKey = "PRIMARYKEY";`

   ![Az Azure portál "Kulcsok" panel](./media/create-graph-nodejs/keys.png)

4. A config.database és a config.collection értékéhez adja meg az adatbázis és a gráf (tároló) nevét. 

Íme egy példa Mi a befejezett config.js fájl hasonlóan kell kinéznie:

```nodejs
var config = {}

// Note that this must not have HTTPS or the port number
config.endpoint = "testgraphacct.graphs.azure.com";
config.primaryKey = "Pams6e7LEUS7LJ2Qk0fjZf3eGo65JdMWHmyn65i52w8ozPX2oxY3iP0yu05t9v1WymAHNcMwPIqNAEv3XDFsEg==";
config.database = "graphdb"
config.collection = "Persons"

module.exports = config;
```

## <a name="run-the-console-app"></a>A konzolalkalmazás futtatása

1. Nyisson meg egy terminálablakot, és a `cd` parancs használatával váltson a projektben lévő package.json fájl telepítési könyvtárára.

2. Futtassa az `npm install` parancsot a szükséges npm-modulok (köztük a `gremlin`) telepítéséhez.

3. Futtassa a `node app.js` parancsot egy terminálban a node-alkalmazás elindításához.

## <a name="browse-with-data-explorer"></a>Tallózás az Adatkezelővel

Most visszaléphet az Adatkezelőbe az Azure Portalon, és megtekintheti, lekérdezheti, módosíthatja, és használatba veheti az új gráfadatokat.

Az Adatkezelőben az új adatbázis a **Gráfok** ablaktáblán jelenik meg. Bontsa ki az adatbázist, kiegészítve a gyűjteményhez, és válassza ki **Graph**.

A mintaalkalmazás által létrehozott adatokat a következő ablaktáblára belül jelenik meg a **Graph** kiválasztásakor lapon **szűrés**.

A szűrő teszteléséhez hajtsa végre a `g.V()` függvényt a következővel: `.has('firstName', 'Thomas')`. Vegye figyelembe, hogy az érték kis-és nagybetűket.

## <a name="review-slas-in-the-azure-portal"></a>Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-your-resources"></a>Az erőforrások törlése

Ha nem tervezi az alkalmazás további használatát, törölje a cikkben létrehozott összes erőforrást a következőképpen: 

1. Válassza ki az Azure portálon a bal oldali navigációs menü **erőforráscsoportok**. Ezután válassza ki a létrehozott erőforrás nevét. 

2. Az erőforráscsoport oldalán kattintson a **Törlés** parancsra. Írja be a törölni kívánt, majd válassza ki az erőforrás nevét **törlése**.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, Azure Cosmos DB-fiók létrehozása, hozzon létre egy grafikonon adatkezelő használatával, és futtassa az alkalmazást. Mostantól a Gremlin használatával összetettebb lekérdezéseket is létrehozhat, és hatékony gráfbejárási logikákat implementálhat. 

> [!div class="nextstepaction"]
> [Gremlin használatával lekérdezése](tutorial-query-graph.md)
