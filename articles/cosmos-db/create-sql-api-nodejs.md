---
title: 'Rövid útmutató: a Node. js használatával kérdezheti le Azure Cosmos DB SQL API-fiókból'
description: A Node. js használata olyan alkalmazás létrehozásához, amely Azure Cosmos DB SQL API-fiókhoz csatlakozik, és lekérdezi az adatlekérdezéseket.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: dech
ms.openlocfilehash: 8df78df27ffb7e8bb8fc88567bd0b3d37be20488
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719500"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Gyors útmutató: az Azure Cosmos DB SQL API-fiókból való kapcsolódáshoz és az adatok lekérdezéséhez használja a Node. js-t

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Ez a rövid útmutató azt ismerteti, hogyan használható Node. js-alkalmazás a Azure Cosmos DB [SQL API](sql-api-introduction.md) -fiókjához való kapcsolódáshoz. Ezután Azure Cosmos DB SQL-lekérdezéseket használhat az adatlekérdezéshez és az adatkezeléshez. Az ebben a cikkben felépített Node. js-alkalmazás az [SQL JavaScript SDK](sql-api-sdk-node.md)-t használja. A rövid útmutató a [JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos) 2.0-s verzióját használja.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Továbbá:
    * A [Node.js](https://nodejs.org/en/) 6.0.0-s vagy újabb verziója
    * [Git](https://git-scm.com/)

## <a name="create-a-database"></a>Adatbázis létrehozása 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Tároló hozzáadása

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Mintaadatok hozzáadása

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Adatok lekérdezése

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most hozzon létre egy Node. js-alkalmazást a GitHubról, állítsa be a kapcsolatok karakterláncát, és futtassa.

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha szeretné megtanulni, hogyan jönnek létre az Azure Cosmos Database-erőforrások a kódban, tekintse át az alábbi kódrészleteket. Egyéb esetben ugorhat [A kapcsolati sztring frissítése](#update-your-connection-string) szakaszra. 

Megjegyzés: Ha már ismeri a JavaScript SDK korábbi verzióját, ismerősek lehetnek a „gyűjtemény” és a „dokumentum” kifejezések. Mivel az Azure Cosmos DB [több API-modellt](https://docs.microsoft.com/azure/cosmos-db/introduction) támogat, a JavaScript SDK 2.0-s vagy újabb verziói az általános „tároló” (gyűjtemény, gráf vagy tábla) és az „elem” (a tárolók elemei) kifejezést használják.

Az alábbi kódrészletek mind az **app.js** fájlból származnak.

* A `CosmosClient` objektum inicializálva van.

    ```javascript
    const client = new CosmosClient({ endpoint, key });
    ```

* Hozzon létre egy új Azure Cosmos-adatbázist.

    ```javascript
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    ```

* A rendszer létrehoz egy új tárolót (gyűjteményt) az adatbázison belül.

    ```javascript
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    ```

* A rendszer létrehoz egy új elemet (dokumentumot).

    ```javascript
    const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
    ```

* A rendszer a JSON protokollon keresztül SQL-lekérdezést hajt végre a család adatbázisán. A lekérdezés az "Anderson" család összes gyermekét adja vissza. 

    ```javascript
      const querySpec = {
        query: 'SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName',
        parameters: [
          {
            name: '@lastName',
            value: 'Andersen'
          }
        ]
      }

      const { resources: results } = await client
        .database(databaseId)
        .container(containerId)
        .items.query(querySpec)
        .fetchAll()
      for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult)
        console.log(`\tQuery returned ${resultString}\n`)
      }
    ```    

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Most lépjen vissza a Azure Portalra az Azure Cosmos-fiók kapcsolati sztring adatainak beszerzéséhez. Másolja a kapcsolati karakterláncot az alkalmazásba, hogy az képes legyen csatlakozni az adatbázishoz.

1. A [Azure Portal](https://portal.azure.com/)az Azure Cosmos-fiókban a bal oldali navigációs sávon kattintson a **kulcsok**elemre, majd kattintson az **írási/olvasási kulcsok**elemre. A következő lépésben a képernyő jobb oldalán lévő másolási gombokkal másolhatja az URI-t és az elsődleges kulcsot a `config.js` fájlba.

    ![Hozzáférési kulcs megtekintése és másolása az Azure Portal Kulcsok paneljén](./media/create-sql-api-dotnet/keys.png)

2. Nyissa meg a `config.js` fájlt. 

3. A másolási gomb használatával másolja ki az URI érteket a Portalról, és azt adja meg a végpont kulcs értékeként a `config.js`-ben. 

    `config.endpoint = "<Your Azure Cosmos account URI>"`

4. Ezután másolja ki az ELSŐDLEGES KULCS értékét a Portalról, és adja meg a `config.key` értékeként a `config.js`-ben. Ezzel frissítette az alkalmazást az összes olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. 

    `config.key = "<Your Azure Cosmos account key>"`
    
## <a name="run-the-app"></a>Az alkalmazás futtatása

1. Futtassa a `npm install` parancsot egy terminálban a szükséges npm-modulok telepítéséhez

2. Futtassa a `node app.js` parancsot egy terminálban a node-alkalmazás elindításához.

Most visszatérhet az új adattal Adatkezelő, módosíthat és dolgozhat.

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Azure Cosmos-fiókot, hogyan hozhat létre tárolót az adatkezelő használatával, és hogyan futtathat egy alkalmazást. Mostantól további adatait is importálhatja az Azure Cosmos-adatbázisba. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be](import-data.md)


