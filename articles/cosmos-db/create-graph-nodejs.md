---
title: "Azure Cosmos DB-alapú Node.js-alkalmazás létrehozása a Graph API-val | Microsoft Docs"
description: "A cikk egy Node.js-kódmintát mutat be, amellyel csatlakozhat egy Azure Cosmos DB-adatbázishoz, és lekérdezéseket hajthat végre"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/21/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: b9e8c46ba2f029f8dae2b357f05a806d769d0920
ms.contentlocale: hu-hu
ms.lasthandoff: 06/07/2017


---
<a id="azure-cosmos-db-build-a-nodejs-application-by-using-graph-api" class="xliff"></a>

# Azure Cosmos DB: Node.js-alkalmazás létrehozása a Graph API-val

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum-, kulcs/érték és gráf típusú adatbázisokat, melyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket. 

Ez a rövid útmutató bemutatja, hogy az Azure Portal segítségével hogyan hozhat létre Azure Cosmos DB-fiókot az előzetes verzióként elérhető Graph API-hoz, valamint adatbázist és gráfot. Ezután a nyílt forráskódú [Gremlin Node.js](https://www.npmjs.com/package/gremlin-secure)-illesztőprogram segítségével létrehozhat és futtathat egy konzolalkalmazást.  

> [!NOTE]
> A `gremlin-secure` npm-modul a `gremlin` modul módosított verziója, amely támogatja az Azure Cosmos DB-hez történő csatlakozáshoz szükséges SSL és SASL protokollt. A forráskód elérhető a [GitHubon](https://github.com/CosmosDB/gremlin-javascript).
>

<a id="prerequisites" class="xliff"></a>

## Előfeltételek

Mielőtt futtathatná ezt a mintát, rendelkeznie kell a következő előfeltételekkel:
* [Node.js](https://nodejs.org/en/)-verzió: 0.10.29-es vagy újabb
* [Git](http://git-scm.com/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="create-a-database-account" class="xliff"></a>

## Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

<a id="add-a-graph" class="xliff"></a>

## Gráf hozzáadása

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

<a id="clone-the-sample-application" class="xliff"></a>

## A mintaalkalmazás klónozása

A következő lépésekben elvégezheti a Graph API-alkalmazás klónozását a GitHubról, beállíthatja a kapcsolati sztringet, és futtathatja az alkalmazást. Látni fogja, milyen egyszerű az adatokkal programozott módon dolgozni. 

1. Nyisson meg egy Git-terminálablakot, például a Git Bash eszközt, és a `cd` paranccsal váltson egy munkakönyvtárra.  

2. Futtassa a következő parancsot a mintatárház klónozásához. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. Nyissa meg a megoldásfájlt a Visual Studióban. 

<a id="review-the-code" class="xliff"></a>

## A kód áttekintése

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

<a id="update-your-connection-string" class="xliff"></a>

## A kapcsolati karakterlánc frissítése

Lépjen vissza az Azure Portalra, kérje le a kapcsolati sztring adatait, majd másolja, és illessze be azokat az alkalmazásba.

1. Az [Azure Portalon](http://portal.azure.com/) az Azure Cosmos DB-fiók bal oldali navigációs menüjében kattintson a **Kulcsok**, majd az **Írási/olvasási kulcsok** elemre. A következő lépésben a jobb oldali másolási gombokkal másolja az URI-t és az elsődleges kulcsot, és illessze be őket a `app.js` fájlba.

    ![Kulcsok panel az Azure Portalon](./media/create-graph-nodejs/keys.png)

2. A másolási gomb használatával másolja a Gremlin URI értékét a Portalról, és azt adja meg a config.js fájl `config.endpoint` kulcsának értékeként. A Gremlin-végpont csak protokoll/portszám nélküli gazdagépnév lehet, például `mygraphdb.graphs.azure.com` (és nem `https://mygraphdb.graphs.azure.com` vagy `mygraphdb.graphs.azure.com:433`).

    `config.endpoint = "GRAPHENDPOINT";`

3. Másolja az elsődleges kulcs értékét a Portalról, és azt adja meg a config.primaryKey értékeként a config.js fájlban. Az alkalmazás frissítve lett minden olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. 

    `config.primaryKey = "PRIMARYKEY";`

4. A config.database és a config.collection értékéhez adja meg az adatbázis és a gráf (tároló) nevét. 

Az elkészült config.js fájl olyan lesz, ahogy az alábbi példában látható:

```nodejs
var config = {}

// Note that this must not have HTTPS or the port number
config.endpoint = "mygraphdb.graphs.azure.com";
config.primaryKey = "OjlhK6tjxfSXyKtrmCiM9O6gQQgu5DmgAoauzD1PdPIq1LZJmILTarHvrolyUYOB0whGQ4j21rdAFwoYep7Kkw==";
config.database = "graphdb"
config.collection = "Persons"

module.exports = config;
```

<a id="run-the-console-app" class="xliff"></a>

## A konzolalkalmazás futtatása

1. Nyisson meg egy terminálablakot, és a `cd` parancs használatával váltson a projektben lévő package.json fájl telepítési könyvtárára.  

2. Futtassa az `npm install` parancsot a szükséges npm-modulok (köztük a `gremlin-secure`) telepítéséhez.

3. Futtassa a `node app.js` parancsot egy terminálban a node-alkalmazás elindításához.

<a id="browse-with-data-explorer" class="xliff"></a>

## Tallózás az Adatkezelővel

Most visszaléphet az Adatkezelőbe az Azure Portalon, és megtekintheti, lekérdezheti, módosíthatja, és használatba veheti az új gráfadatokat.

Az Adatkezelőben az új adatbázis a **Gyűjtemények** ablaktáblán jelenik meg. Bontsa ki a **graphdb**, **graphcoll** pontokat, és kattintson a **Gráf** lehetőségre.

A mintaalkalmazás által létrehozott adatokat a **Gráfok** ablaktáblán találja.

<a id="review-slas-in-the-azure-portal" class="xliff"></a>

## Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

<a id="clean-up-your-resources" class="xliff"></a>

## Az erőforrások törlése

Ha nem tervezi az alkalmazás további használatát, törölje a cikkben létrehozott összes erőforrást a következőképpen: 

1. Az Azure Portal bal oldali navigációs menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd a létrehozott erőforrás nevére. 
2. A saját erőforráscsoport oldalán kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét, majd kattintson a **Törlés** elemre.

<a id="next-steps" class="xliff"></a>

## Következő lépések

Ez a cikk bemutatta, hogyan lehet létrehozni egy Azure Cosmos DB-fiókot, létrehozni egy gráfot az Adatkezelő használatával, és futtatni egy alkalmazást. Mostantól a Gremlin használatával összetettebb lekérdezéseket is létrehozhat, és hatékony gráfbejárási logikákat implementálhat. 

> [!div class="nextstepaction"]
> [Lekérdezés a Gremlin használatával](tutorial-query-graph.md)

