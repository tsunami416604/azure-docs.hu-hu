---
title: Rövid útmutató – a Node. js használatával kérdezheti le Azure Cosmos DB SQL API-fiókból
description: A Node. js használata olyan alkalmazás létrehozásához, amely Azure Cosmos DB SQL API-fiókhoz csatlakozik, és lekérdezi az adatlekérdezéseket.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: dech
ms.openlocfilehash: 2e1f0313b6e611eac6968c17cececd382a6d45fe
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77664073"
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

Most már használhatja a Azure Portal Adatkezelő eszközét egy adatbázis és egy tároló létrehozásához. 

1. Válassza **Adatkezelő** > **új tároló**elemet. 
    
    A jobb szélen megjelenik a **tároló hozzáadása** felület, ezért a jobb oldali görgetéshez jobbra kell görgetni a megjelenítéshez.

    ![A Azure Portal Adatkezelő, tároló hozzáadása panel](./media/create-sql-api-nodejs/azure-cosmosdb-data-explorer.png)

2. A **tároló hozzáadása** lapon adja meg az új tároló beállításait.

    |Beállítás|Ajánlott érték|Leírás
    |---|---|---|
    |**Adatbázis-azonosító**|Feladatok|Az új adatbázisnak adja a *Feladatok* nevet. Az adatbázis nevének 1 és 255 karakter közöttinek kell lennie, és nem tartalmazhat `/, \\, #, ?`vagy záró szóközt. Tekintse meg az **adatbázis átviteli sebességének** kiosztása lehetőséget, amellyel megoszthatja az adatbázison belül kiosztott átviteli sebességet az adatbázis összes tárolóján. Ez a lehetőség a költségmegtakarítást is segíti. |
    |**Átviteli sebesség**|400|Az átviteli sebesség 400 adategység/másodperc (RU/s) esetén. Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében.| 
    |**Tároló azonosítója**|Elemek|Adja meg az *elemeket* az új tároló neveként. A tároló-azonosítók ugyanazokkal a karakterekkel rendelkeznek, mint az adatbázis neve.|
    |**Partíciós kulcs**| /kategória| A cikkben ismertetett minta a */category* használja a partíciós kulcsként.|
    
    Az előző beállításokon kívül opcionálisan hozzáadhat **egyedi kulcsokat** a tárolóhoz. Ebben a példában az erre szolgáló mezőt hagyja üresen. Az egyedi kulcsok lehetőséget nyújtanak a fejlesztők számára, hogy adatintegritási réteget adjanak az adatbázishoz. A tárolók létrehozásakor egyedi kulcsokra vonatkozó szabályzat létrehozásával biztosíthatja, hogy a partíciós kulcs egy vagy több értéke egyedi legyen. További információt az [Azure Cosmos DB-ben egyedi kulcsaival](unique-keys.md) kapcsolatos cikkben talál.
    
    Kattintson az **OK** gombra. A Adatkezelő megjeleníti az új adatbázist és tárolót.

## <a name="add-sample-data"></a>Mintaadatok hozzáadása

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Adatok lekérdezése

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most hozzon létre egy Node. js-alkalmazást a GitHubról, állítsa be a kapcsolatok karakterláncát, és futtassa.

1. Az alábbi parancs futtatásával klónozhatja a mintatárházat. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
   ```

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha szeretné megtanulni, hogyan jönnek létre az Azure Cosmos Database-erőforrások a kódban, tekintse át az alábbi kódrészleteket. Egyéb esetben ugorhat [A kapcsolati sztring frissítése](#update-your-connection-string) szakaszra.

Ha már ismeri az SQL JavaScript SDK korábbi verzióját, akkor a feltételek _gyűjteményét_ és _dokumentumát_is használhatja. Mivel Azure Cosmos DB [több API-modellt](introduction.md)is támogat, [a JavaScript SDK 2.0](https://www.npmjs.com/package/@azure/cosmos) -s vagy újabb verziója az általános feltételek _tárolóját_használja, amely lehet gyűjtemény, gráf vagy tábla, valamint a tároló tartalmának leírására szolgáló _elem_ .

Az alábbi kódrészletek mind az _app.js_ fájlból származnak.

- A `CosmosClient` objektum inicializálva van.

  ```javascript
  const client = new CosmosClient({ endpoint, key });
  ```

- Válassza ki a "feladatok" adatbázist.

  ```javascript
  const database = await client.databases(databaseId);
  ```

- Válassza ki az "Items" tárolót/gyűjteményt.

  ```javascript
  const container = await client.databases(containerId);
  ```

- Válassza ki az összes elemet az "Items" tárolóban.

  ```javascript
  // query to return all items
  const querySpec = {
    query: "SELECT * from c"
  };

  const { resources: results } = await container.items
    .query(querySpec)
    .fetchAll();

  return results;
  ```

- Új tétel létrehozása

  ```javascript
  const { resource: createdItem } = await container.items.create(newItem);
  ```

- Egy tétel frissítése

  ```javascript
  const { id, category } = createdItem;

  createdItem.isComplete = true;
  const { resource: itemToUpdate } = await container
    .item(id, category)
    .replace(itemToUpdate);

  return result;
  ```

- Elemek törlése

  ```javascript
  const { resource: result } = await this.container.item(id, category).delete();
  ```

> [!NOTE]
> A "frissítés" és a "Törlés" metódusban az elemet a `conatiner.item()`meghívásával kell kiválasztani az adatbázisból. Az átadott két paraméter az elemek és az elemek partíciós kulcsának azonosítója. Ebben az esetben a egyenlőség kulcs a "Category" (kategória) mező értéke.

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Most lépjen vissza a Azure Portalra az Azure Cosmos-fiók kapcsolati sztring adatainak beszerzéséhez. Másolja a kapcsolati karakterláncot az alkalmazásba, hogy az képes legyen csatlakozni az adatbázishoz.

1. A [Azure Portal](https://portal.azure.com/)Azure Cosmos db-fiókjában válassza a bal oldali navigációs menüben a **kulcsok** elemet, majd válassza az **írási/olvasási kulcsok**elemet. A következő lépésben a képernyő jobb oldalán található másolási gombok használatával másolja az URI-t és az elsődleges kulcsot az _app. js_ fájlba.

   ![Hozzáférési kulcs megtekintése és másolása az Azure Portal kulcsok paneljén](./media/create-sql-api-dotnet/keys.png)

2. Nyissa meg a _config. js_ fájlt.

3. Másolja az URI-értéket a portálról (a másolás gombbal), és adja meg a Endpoint kulcs értékét a _config. js fájlban_.

   `endpoint: "<Your Azure Cosmos account URI>"`

4. Ezután másolja ki az elsődleges kulcs értékét a portálról, és adja meg a `config.key` értékét a _config. js fájlban_. Az alkalmazás frissítve lett minden olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges.

   `key: "<Your Azure Cosmos account key>"`

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
