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
ms.openlocfilehash: d8b17472bb531ec799be227706261962d7914d68
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134454"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Gyors útmutató: az Azure Cosmos DB SQL API-fiókból való kapcsolódáshoz és az adatok lekérdezéséhez használja a Node. js-t

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Ebben a rövid útmutatóban egy Azure Cosmos DB SQL API-fiókot hoz létre és felügyel a Azure Portalból, valamint egy, a GitHubról klónozott Node. js-alkalmazással. A Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi a dokumentumok, tábla, kulcs-érték és gráf adatbázisok gyors létrehozását és lekérdezését globális terjesztési és horizontális méretezési képességekkel.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Vagy [próbálja ki Azure Cosmos db](https://azure.microsoft.com/try/cosmosdb/) ingyen Azure-előfizetés nélkül. Használhatja a [Azure Cosmos db emulátort](https://aka.ms/cosmosdb-emulator) is `https://localhost:8081` URI-ja és a kulcs `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [Node. js 6.0.0 +](https://nodejs.org/).
- [Git](https://www.git-scm.com/downloads).

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

1. Nyisson meg egy parancssort, hozzon létre egy git-samples nevű új mappát, majd zárja be a parancssort.

    ```bash
    md "C:\git-samples"
    ```

2. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson az új mappára, ahol telepíteni szeretné a mintaalkalmazást.

    ```bash
    cd "C:\git-samples"
    ```

3. Az alábbi parancs futtatásával klónozhatja a mintatárházat. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha szeretné megtanulni, hogyan jönnek létre az Azure Cosmos Database-erőforrások a kódban, tekintse át az alábbi kódrészleteket. Egyéb esetben ugorhat [A kapcsolati sztring frissítése](#update-your-connection-string) szakaszra. 

Ha már ismeri az SQL JavaScript SDK korábbi verzióját, akkor a feltételek *gyűjteményét* és *dokumentumát*is használhatja. Mivel Azure Cosmos DB [több API-modellt](introduction.md)is támogat, [a JavaScript SDK 2.0](https://www.npmjs.com/package/@azure/cosmos) -s vagy újabb verziója az általános feltételek *tárolóját*használja, amely lehet gyűjtemény, gráf vagy tábla, valamint a tároló tartalmának leírására szolgáló *elem* .

Az alábbi kódrészletek mind az *app.js* fájlból származnak.

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

1. A [Azure Portal](https://portal.azure.com/)Azure Cosmos db-fiókjában válassza a bal oldali navigációs menüben a **kulcsok** elemet, majd válassza az **írási/olvasási kulcsok**elemet. A következő lépésben a képernyő jobb oldalán található másolási gombok használatával másolja az URI-t és az elsődleges kulcsot a *config. js* fájlba.

    ![Hozzáférési kulcs megtekintése és másolása az Azure Portal kulcsok paneljén](./media/create-sql-api-dotnet/keys.png)

2. Nyissa meg a *config. js* fájlt. 

3. Másolja az URI-értéket a portálról (a másolás gombbal), és adja meg a Endpoint kulcs értékét a *config. js fájlban*. 

    `config.endpoint = "<Your Azure Cosmos account URI>"`

4. Ezután másolja ki az elsődleges kulcs értékét a portálról, és adja meg a `config.key` értékét a *config. js fájlban*. Az alkalmazás frissítve lett minden olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. 

    `config.key = "<Your Azure Cosmos account key>"`
    
## <a name="run-the-app"></a>Az alkalmazás futtatása

1. Futtassa az `npm install` parancsot a terminálban a szükséges npm-modulok telepítéséhez

2. Futtassa a `node app.js` parancsot a terminálban a node-alkalmazás elindításához.

Most visszatérhet az új adattal Adatkezelő, módosíthat és dolgozhat.

## <a name="review-slas-in-the-azure-portal"></a>Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Azure Cosmos DB fiókot, hogyan hozhat létre egy tárolót a Adatkezelő használatával, és hogyan futtathat Node. js-alkalmazást. Így már további adatokat importálhat az Azure Cosmos DB-fiókba. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be](import-data.md)


