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
ms.topic: hero-article
ms.date: 08/29/2017
ms.author: denlee
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 60cb187cf40f72fce86c421891bea02d3d6d708a
ms.contentlocale: hu-hu
ms.lasthandoff: 08/30/2017

---
# <a name="azure-cosmos-db-build-a-nodejs-application-by-using-graph-api"></a>Azure Cosmos DB: Node.js-alkalmazás létrehozása a Graph API-val

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum-, kulcs/érték és gráf típusú adatbázisokat, melyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket. 

Ez a rövid útmutató bemutatja, hogy az Azure Portal segítségével hogyan hozhat létre Azure Cosmos DB-fiókot az előzetes verzióként elérhető Graph API-hoz, valamint adatbázist és gráfot. Ezután a nyílt forráskódú [Gremlin Node.js](https://www.npmjs.com/package/gremlin)-illesztőprogram segítségével létrehozhat és futtathat egy konzolalkalmazást.  

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

2. Futtassa a következő parancsot a mintatárház klónozásához. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. Nyissa meg a megoldásfájlt a Visual Studióban. 

## <a name="review-the-code"></a>A kód áttekintése

Tekintsük át, hogy mi történik az alkalmazásban. Nyissa meg az `app.js` fájlt, amelyben a következő kódsorokat találja. 

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

  Minden konfiguráció a `config.js` fájlban van, amelynek a szerkesztését a következő rész írja le.

* A `client.execute` metódussal a program Gremlin-lépések sorozatát hajtja végre.

    ```nodejs
    console.log('Running Count'); 
    client.execute("g.V().count()", { }, (err, results) => {
        if (err) return console.error(err);
        console.log(JSON.stringify(results));
        console.log();
    });
    ```

## <a name="update-your-connection-string"></a>A kapcsolati karakterlánc frissítése

1. Nyissa meg a config.js fájlt. 

2. A config.js fájlban adja meg a config.endpoint kulcsot az Azure Portal **Áttekintés** lapjáról származó **Gremlin URI** értékkel. 

    `config.endpoint = "GRAPHENDPOINT";`

    ![Hozzáférési kulcs megtekintése és másolása az Azure Portal kulcsok paneljén](./media/create-graph-nodejs/gremlin-uri.png)

   Ha a **Gremlin URI** érték üres, létrehozhatja az értéket a portál **Kulcsok** oldalán az **URI** értékkel a https:// előtag eltávolításával és a dokumentumok gráfokká módosításával.

   A Gremlin-végpont csak protokoll/portszám nélküli gazdagépnév lehet, például `mygraphdb.graphs.azure.com` (és nem `https://mygraphdb.graphs.azure.com` vagy `mygraphdb.graphs.azure.com:433`).

3. A config.js fájlban adja meg a config.primaryKey értéket az Azure Portal **Kulcsok** oldaláról származó **Elsődleges kulcs** értékkel. 

    `config.primaryKey = "PRIMARYKEY";`

   ![Kulcsok panel az Azure Portalon](./media/create-graph-nodejs/keys.png)

4. A config.database és a config.collection értékéhez adja meg az adatbázis és a gráf (tároló) nevét. 

Az elkészült config.js fájl olyan lesz, ahogy az alábbi példában látható:

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

Az Adatkezelőben az új adatbázis a **Gráfok** ablaktáblán jelenik meg. Bontsa ki az adatbázist, majd a gyűjteményt, és kattintson a **Gráf** elemre.

A mintaalkalmazás által létrehozott adatok a **Gráf** lap következő panelén jelennek meg, amikor a **Szűrő alkalmazása** gombra kattint.

A szűrő teszteléséhez hajtsa végre a `g.V()` függvényt a következővel: `.has('firstName', 'Thomas')`. Vegye figyelembe, hogy ez az érték megkülönbözteti a kis- és nagybetűket.

## <a name="review-slas-in-the-azure-portal"></a>Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-your-resources"></a>Az erőforrások törlése

Ha nem tervezi az alkalmazás további használatát, törölje a cikkben létrehozott összes erőforrást a következőképpen: 

1. Az Azure Portal bal oldali navigációs menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd a létrehozott erőforrás nevére. 
2. A saját erőforráscsoport oldalán kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét, majd kattintson a **Törlés** elemre.

## <a name="next-steps"></a>Következő lépések

Ez a cikk bemutatta, hogyan lehet létrehozni egy Azure Cosmos DB-fiókot, létrehozni egy gráfot az Adatkezelő használatával, és futtatni egy alkalmazást. Mostantól a Gremlin használatával összetettebb lekérdezéseket is létrehozhat, és hatékony gráfbejárási logikákat implementálhat. 

> [!div class="nextstepaction"]
> [Lekérdezés a Gremlin használatával](tutorial-query-graph.md)

