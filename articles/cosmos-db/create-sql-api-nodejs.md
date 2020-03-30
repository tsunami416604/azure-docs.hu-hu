---
title: Gyorsútmutató – A Node.js fájl használata lekérdezésaz Azure Cosmos DB SQL API-fiókból
description: Node.js használatával hozzon létre egy alkalmazást, amely csatlakozik az Azure Cosmos DB SQL API-fiókhoz, és lekérdezi az adatokat.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: dech
ms.openlocfilehash: 0b29f9c1f395e079c97d5877d08bd7bd73c7ea53
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80240321"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Rövid útmutató: A Node.js használatával adatokat csatlakoztathat és kérdezhessen le az Azure Cosmos DB SQL API-fiókból

> [!div class="op_single_selector"]
> - [.NET V3](create-sql-api-dotnet.md)
> - [.NET V4](create-sql-api-dotnet-V4.md)
> - [Java](create-sql-api-java.md)
> - [Node.js](create-sql-api-nodejs.md)
> - [Python](create-sql-api-python.md)
> - [Xamarin](create-sql-api-xamarin-dotnet.md)

Ebben a rövid útmutatóban hozzon létre és kezeljen egy Azure Cosmos DB SQL API-fiókot az Azure Portalról, és a GitHubról klónozott Node.js alkalmazás használatával. Az Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi a dokumentumok, a tábla, a kulcsérték és a grafikonadatbázisok gyors létrehozását és lekérdezését globális terjesztési és horizontális méretezési képességekkel.

## <a name="walkthrough-video"></a>Forgatókönyv videó

Tekintse meg ezt a videót a cikk tartalmának teljes átjárásához.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Quickstart-Use-Nodejs-to-connect-and-query-data-from-Azure-Cosmos-DB-SQL-API-account/player]

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Vagy [próbálja ki az Azure Cosmos DB-t ingyenesen](https://azure.microsoft.com/try/cosmosdb/) Azure-előfizetés nélkül. Az [Azure Cosmos DB emulátort](https://aka.ms/cosmosdb-emulator) is `https://localhost:8081` használhatja `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`a kulcs URI-jával és a kulccsal.
- [Node.js 6.0.0+](https://nodejs.org/).
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-cosmos-account"></a>Azure Cosmos-fiók létrehozása

Ebben a rövid útmutató célja, használhatja a [próbálja azure Cosmos DB ingyenes](https://azure.microsoft.com/try/cosmosdb/) lehetőséget, hogy hozzon létre egy Azure Cosmos-fiókot.

1. Keresse meg az [Azure Cosmos DB ingyenes lapját.](https://azure.microsoft.com/try/cosmosdb/)

1. Válassza az **SQL** API-fiókot, és válassza **a Létrehozás**lehetőséget. Jelentkezzen be a Microsoft-fiókjával.

1. A bejelentkezés sikeres befejezése után az Azure Cosmos-fiók készen áll. Válassza **a Megnyitás az Azure Portalon** lehetőséget az újonnan létrehozott fiók megnyitásához.

Az "ingyenesen próbálja ki az Azure Cosmos DB-t" lehetőség nem igényel Azure-előfizetést, és korlátozott ideig kínál Egy Azure Cosmos-fiókot. Ha hosszabb ideig szeretné használni az Azure Cosmos-fiókot, ehelyett létre kell [hoznia a fiókot az](create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account) Azure-előfizetésen belül.

## <a name="add-a-container"></a>Tároló hozzáadása

Most már használhatja a Data Explorer eszközt az Azure Portalon egy adatbázis és tároló létrehozásához.

1. Válassza az **Adatkezelő** > **új tárolóját**.

   A **Tároló hozzáadása** terület a jobb szélen jelenik meg, előfordulhat, hogy jobbra kell görgetnie, hogy láthassa.

   ![Az Azure Portal Adatkezelője a Tároló hozzáadása panellel](./media/create-sql-api-nodejs/azure-cosmosdb-data-explorer.png)

2. A **Tároló hozzáadása** lapon adja meg az új tároló beállításait.

   | Beállítás           | Ajánlott érték | Leírás                                                                                                                                                                                                                                                                                                                                                                           |
   | ----------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
   | **Adatbázis azonosítója**   | Feladatok           | Az új adatbázisnak adja a _Feladatok_ nevet. Az adatbázisneveknek 1 és 255 karakter `/, \\, #, ?`között kell lennie, és nem tartalmazhatnak , illetve záró szóközt. Ellenőrizze a **létesítési adatbázis átviteli beállítás,** lehetővé teszi, hogy megosszák az adatbázis számára kiosztott átviteli átmenő az adatbázis összes tárolója között. Ez az opció is segít a költségmegtakarítást. |
   | **Teljesítmény**    | 400             | Hagyja az átviteli értéket 400 kérelemegység/másodperc (RU/s) értéken. Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében.                                                                                                                                                                                                                                                    |
   | **Tároló azonosítója**  | Elemek           | Írja be _az elemeket_ az új tároló neveként. A tárolóazonosítók nevére ugyanazok a karakterkorlátozások vonatkoznak, mint az adatbázisnevekre.                                                                                                                                                                                                                                                               |
   | **Partíciókulcs** | /kategória       | A cikkben ismertetett minta a _/category_ kapcsolót használja partíciókulcsként.                                                                                                                                                                                                                                                                                                           |

   Az előző beállításokon kívül szükség esetén **egyedi kulcsokat** is hozzáadhat a tárolóhoz. Ebben a példában az erre szolgáló mezőt hagyja üresen. Az egyedi kulcsok lehetőséget nyújtanak a fejlesztők számára, hogy adatintegritási réteget adjanak az adatbázishoz. Ha egy tároló létrehozása közben létrehoz egy egyedi kulcsházirendet, biztosítja partíciókulcsonként egy vagy több érték egyediségét. További információt az [Azure Cosmos DB-ben egyedi kulcsaival](unique-keys.md) kapcsolatos cikkben talál.

   Válassza **az OK gombot.** Az Adatkezelő megjeleníti az új adatbázist és tárolót.

## <a name="add-sample-data"></a>Mintaadatok hozzáadása

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Adatok lekérdezése

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most klónozzunk egy Node.js alkalmazást a GitHubról, állítsuk be a kapcsolati karakterláncot, és futtassuk.

1. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
   ```

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha érdekli, hogy hogyan jönnek létre az Azure Cosmos adatbázis-erőforrások a kódban, tekintse át a következő kódrészletek. Egyéb esetben ugorhat [A kapcsolati sztring frissítése](#update-your-connection-string) szakaszra.

Ha ismeri az SQL JavaScript SDK korábbi verzióját, akkor a kifejezések _gyűjteményének_ és _dokumentumának_megtekintéséhez használható. Mivel az Azure Cosmos DB [több API-modellt](introduction.md)is támogat, [a JavaScript SDK 2.0+-os verziója](https://www.npmjs.com/package/@azure/cosmos) az általános _kifejezéstárolót_használja, amely lehet gyűjtemény, grafikon vagy tábla, és _elem_ a tároló tartalmának leírásához.

A Cosmos DB JavaScript SDK neve "@azure/cosmos" és telepíthető npm...

```bash
npm install @azure/cosmos
```

Az alábbi kódrészletek mind az _app.js_ fájlból származnak.

- Az `CosmosClient` importálása `@azure/cosmos` az npm csomagból történik.

  ```javascript
  const CosmosClient = require("@azure/cosmos").CosmosClient;
  ```

- Egy `CosmosClient` új objektum inicializálása.

  ```javascript
  const client = new CosmosClient({ endpoint, key });
  ```

- Válassza ki a "Feladatok" adatbázist.

  ```javascript
  const database = client.database(databaseId);
  ```

- Válassza ki az "Elemek" tárolót/gyűjteményt.

  ```javascript
  const container = database.container(containerId);
  ```

- Jelölje ki az "Elemek" tárolóban lévő összes elemet.

  ```javascript
  // query to return all items
  const querySpec = {
    query: "SELECT * from c"
  };

  const { resources: items } = await container.items
    .query(querySpec)
    .fetchAll();
  ```

- Új elem létrehozása

  ```javascript
  const { resource: createdItem } = await container.items.create(newItem);
  ```

- Elem frissítése

  ```javascript
  const { id, category } = createdItem;

  createdItem.isComplete = true;
  const { resource: updatedItem } = await container
    .item(id, category)
    .replace(createdItem);
  ```

- Elem törlése

  ```javascript
  const { resource: result } = await container.item(id, category).delete();
  ```

> [!NOTE]
> A "frissítés" és a "törlés" módszerben az elemet a `container.item()`hívással kell kiválasztani az adatbázisból. A két megadott paraméter az elem azonosítója és az elem partíciókulcsa. Ebben az esetben a parition kulcs a "kategória" mező értéke.

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Most lépjen vissza az Azure Portalon az Azure Cosmos-fiók kapcsolati karakterláncrészleteinek leéséhez. Másolja a kapcsolati karakterláncot az alkalmazásba, hogy az az adatbázishoz kapcsolódjon.

1. Az Azure Cosmos DB-fiókjában az [Azure Portalon](https://portal.azure.com/)válassza a **kulcsok lehetőséget** a bal oldali navigációs sávon, majd válassza az **Olvasási és írási kulcsok lehetőséget.** A képernyő jobb oldalán található másolási gombokkal másolja az URI-t és az elsődleges kulcsot az _app.js_ fájlba a következő lépésben.

   ![Hozzáférési kulcs megtekintése és másolása az Azure Portal Kulcsok paneljén](./media/create-sql-api-dotnet/keys.png)

2. A _Config.js_ fájl megnyitása csoportban.

3. Másolja az URI-értéket a portálról (a másolás gombhasználatával), és tegye a végpontkulcs értékére a _config.js_fájlban.

   `endpoint: "<Your Azure Cosmos account URI>"`

4. Ezután másolja a PRIMARY KEY értéket a portálról, és tegye a `config.key` _config.js ._ Ezzel frissítette az alkalmazást az összes olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges.

   `key: "<Your Azure Cosmos account key>"`

## <a name="run-the-app"></a>Az alkalmazás futtatása

1. Futtassa `npm install` a terminálon@azure/cosmosa " " npm csomag telepítéséhez

2. Futtassa a `node app.js` parancsot egy terminálban a node-alkalmazás elindításához.

3. A rövid útmutatóban korábban létrehozott két elem fel van sorolva. Új elem jön létre. Az elem "isComplete" jelzője "true"-ra frissül, majd végül az elem törlődik.

Folytathatja a kísérletezést ezzel a mintaalkalmazással, vagy visszatérhet az Adatkezelőhöz, módosíthatja és használhatja az adatokat.

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Azure Cosmos DB-fiókot, hogyan hozhat létre egy tárolót az Adatkezelővel, és hogyan futtathatja a Node.js alkalmazást. Így már további adatokat importálhat az Azure Cosmos DB-fiókba.

> [!div class="nextstepaction"]
> [adatok importálása az azure cosmos-ba db](import-data.md)
