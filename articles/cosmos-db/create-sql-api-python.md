---
title: 'Gyors útmutató: Python-alkalmazás létrehozása Azure Cosmos DB SQL API-fiók használatával'
description: Egy Python-kódmintát mutat be, amellyel csatlakozni lehet az Azure Cosmos DB SQL API-hoz, és lekérdezést lehet végezni vele
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 11/03/2019
ms.author: sngun
ms.custom:
- seodec18
- seo-javascript-september2019
- seo-python-october2019
ms.openlocfilehash: 92175e3b9776f90bd50e356a700b2a2d475c8a85
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73602462"
---
# <a name="quickstart-build-a-python-application-using-an-azure-cosmos-db-sql-api-account"></a>Gyors útmutató: Python-alkalmazás létrehozása Azure Cosmos DB SQL API-fiók használatával

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Azure Cosmos DB [SQL API](sql-api-introduction.md)-fiókot, dokumentum-adatbázist és tárolót az Azure Portal segítségével. Ezután megtudhatja, hogyan hozhat létre és futtathat egy, a Python SDK for [SQL API](sql-api-sdk-python.md)-val létrehozott konzolalkalmazást.

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. Gyorsan létrehozhat és lekérhet dokumentumokat, kulcs/érték, széles oszlop-és Graph-adatbázisokat. Az összes ilyen művelet a Azure Cosmos DB elosztásával és méretezésével jár.

Ez a rövid útmutató a [PYTHON SDK](https://pypi.org/project/azure-cosmos/#history)4-es verzióját használja.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Python 3,6](https://www.python.org/downloads/), `python` végrehajtható fájl elérhető a `PATH`.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Python-bővítmény a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview)

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Tároló hozzáadása

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Mintaadatok hozzáadása

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Adatok lekérdezése

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig klónozzon egy SQL API-alkalmazást a GitHubról, állítsa be a kapcsolati sztringet, és futtassa az alkalmazást.

1. Nyisson meg egy parancssort, hozzon létre egy git-samples nevű mappát, majd zárja be a parancssort.

    ```cmd
    md "git-samples"
    ```
   Ha bash-parancssort használ, Ehelyett használja a következő parancsot:

   ```bash
   mkdir "git-samples"
   ```

2. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson az új mappára, ahol telepíteni szeretné a mintaalkalmazást.

    ```bash
    cd "git-samples"
    ```

3. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-python-getting-started.git
    ```  

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Lépjen vissza az Azure Portalra a kapcsolati sztring adataiért, majd másolja be azokat az alkalmazásba.

1. A [Azure Portal](https://portal.azure.com/)az Azure Cosmos-fiókban a bal oldali navigációs menüben válassza a **kulcsok**lehetőséget. A következő lépésben a képernyő jobb oldalán lévő másolási gombokkal másolhatja az **URI** és az **Elsődleges kulcs** értékét a `cosmos_get_started.py` fájlba.

    ![Hozzáférési kulcs és URI beszerzése a Azure Portal kulcsok beállításaiban](./media/create-sql-api-dotnet/access-key-and-uri-in-keys-settings-in-the-azure-portal.png)

2. Nyissa meg a \git-samples\azure-Cosmos-db-Python-Getting-Started található `cosmos_get_started.py` fájlt a Visual Studio Code-ban.

3. Másolja az **URI** -értéket a portálról (a másolás gomb használatával), és adja meg a **végpont** változó értékét a ``cosmos_get_started.py``ban. 

    `endpoint = 'https://FILLME.documents.azure.com',`

4. Ezután másolja ki az **elsődleges kulcs** értékét a portálról, és adja meg a **kulcs** értékét a ``cosmos_get_started.py``ban. Ezzel frissítette az alkalmazást az összes olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. 

    `key = 'FILLME'`

5. Mentse a ``cosmos_get_started.py`` fájlt.

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ismerje meg a kódban létrehozott adatbázis-erőforrásokat, vagy ugorjon előre a [kapcsolódási karakterlánc frissítéséhez](#update-your-connection-string).

Az alábbi kódrészletek mind a `cosmos_get_started.py` fájlból származnak.

* A CosmosClient inicializálva van. Győződjön meg arról, hogy a "végpont" és a "kulcs" értékeket frissíti a [kapcsolódási karakterlánc frissítése](#update-your-connection-string) című szakaszban leírtak szerint. 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_cosmos_client)]

* A rendszer létrehozza az új adatbázist.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_database_if_not_exists)]

* Létrejön egy új tároló, amely a [kiépített átviteli sebesség](request-units.md)400 ru/s. A [partíciós kulcsként](partitioning-overview.md#choose-partitionkey)`lastName` választjuk, ami lehetővé teszi, hogy hatékony lekérdezéseket végezzen a tulajdonság szűrésére. 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_container_if_not_exists)]

* Egyes elemek hozzá vannak adva a tárolóhoz. A tárolók olyan elemek (JSON-dokumentumok) gyűjteményei, amelyek változatos sémával rendelkezhetnek. A segítő metódusok ```get_[name]_family_item``` Azure Cosmos DB JSON-dokumentumként tárolt családhoz tartozó reprezentációkat adnak vissza.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_item)]

* A pont olvasása (a kulcs értékének keresése) a `read_item` metódus használatával történik. Minden egyes művelet esetében ki kell nyomtatni az [ru díját](request-units.md) .
    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=read_item)]

* A lekérdezés SQL-lekérdezési szintaxis használatával történik. Mivel a WHERE záradékban a ```lastName``` partíciós kulcsának értékeit használjuk, Azure Cosmos DB a lekérdezéseket hatékonyan irányítja át a megfelelő partíciókhoz, így javul a teljesítmény.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=query_items)]
   
## <a name="run-the-app"></a>Az alkalmazás futtatása

1. Válassza a Visual Studio Code-ban a **Nézet** > **Parancskatalógus** lehetőséget. 

2. A megjelenő panelen írja be a **Python: Select Interpreter** szöveget, majd válassza ki a használandó Python-verziót.

    A választott értelmező ekkor megjelenik a Visual Studio Code ablakának állapotsorában. 

3. Válassza a **Nézet** > **Beépített terminál** parancsot a Visual Studio Code beépített termináljának megnyitásához.

4. A beépített terminál ablakában győződjön meg róla, hogy az „azure-cosmos-db-python-getting-started” mappában van. Ha más mappában van, akkor váltson erre a mintamappára az alábbi parancs futtatásával. 

    ```cmd
    cd "\git-samples\azure-cosmos-db-python-getting-started"`
    ```

5. Futtassa az alábbi parancsot az azure-cosmos csomag telepítéséhez. 

    ```python
    pip3 install azure-cosmos==4.0.0b5
    ```

    Ha az azure-cosmos csomag telepítésekor „hozzáférés megtagadva” hibaüzenetet kap, akkor [rendszergazdaként kell futtatnia a VS Code alkalmazást](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights).

6. Futtassa a következő parancsot a minta futtatásához, és hozzon létre és tároljon új dokumentumokat Azure Cosmos DBban.

    ```python
    python cosmos_get_started.py
    ```

7. Az új elemek létrehozásának és mentésének megerősítéséhez válassza ki a Azure Portal **Adatkezelő** > **AzureSampleFamilyDatabase** > **elemet**. Megtekintheti a létrehozott elemeket. Íme például egy példa az Andersen családhoz tartozó JSON-dokumentumra:

```json
{
    "id": "Andersen-1569479288379",
    "lastName": "Andersen",
    "district": "WA5",
    "parents": [
        {
            "familyName": null,
            "firstName": "Thomas"
        },
        {
            "familyName": null,
            "firstName": "Mary Kay"
        }
    ],
    "children": null,
    "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
    },
    "registered": true,
    "_rid": "8K5qAIYtZXeBhB4AAAAAAA==",
    "_self": "dbs/8K5qAA==/colls/8K5qAIYtZXc=/docs/8K5qAIYtZXeBhB4AAAAAAA==/",
    "_etag": "\"a3004d78-0000-0800-0000-5d8c5a780000\"",
    "_attachments": "attachments/",
    "_ts": 1569479288
}
```

## <a name="review-slas-in-the-azure-portal"></a>Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Azure Cosmos-fiókot, hogyan hozhat létre tárolót a Adatkezelő használatával, és hogyan futtathat egy alkalmazást. Így már további adatokat importálhat a Cosmos DB-fiókba. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be az SQL API-hoz](import-data.md)


