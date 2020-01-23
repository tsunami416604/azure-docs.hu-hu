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
ms.openlocfilehash: a794a9ed35cbbdd36c2cf136b8afc208c3ea0692
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549009"
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

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Gyorsan létrehozhat és lekérhet dokumentumokat, kulcs/érték, széles oszlop-és Graph-adatbázisokat. Az összes ilyen művelet a Azure Cosmos DB elosztásával és méretezésével jár.

Ez a rövid útmutató a [PYTHON SDK](https://pypi.org/project/azure-cosmos/#history)4-es verzióját használja.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Python 3.6 +](https://www.python.org/downloads/), `python` végrehajtható fájl elérhető a `PATH`.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Python-bővítmény a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview)

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Tároló hozzáadása

Most már használhatja a Azure Portal Adatkezelő eszközét egy adatbázis és egy tároló létrehozásához. 

1. Kattintson az **Adatkezelő** > **Új tároló** elemre. 
    
    A jobb szélen megjelenik a **tároló hozzáadása** felület, ezért a jobb oldali görgetéshez jobbra kell görgetni a megjelenítéshez.

    ![Az Azure Portal Adatkezelője a Tároló hozzáadása panellel](./media/create-sql-api-python/azure-cosmosdb-data-explorer.png)

2. A **tároló hozzáadása** lapon adja meg az új tároló beállításait.

    |Beállítás|Ajánlott érték|Leírás
    |---|---|---|
    |**Adatbázis-azonosító**|Feladatok|Adja meg a *ToDoList* nevet az új adatbázis neveként. Az adatbázis nevének 1 és 255 karakter közöttinek kell lennie, és nem tartalmazhat `/, \\, #, ?`vagy záró szóközt. Tekintse meg az **adatbázis átviteli sebességének** kiosztása lehetőséget, amellyel megoszthatja az adatbázison belül kiosztott átviteli sebességet az adatbázis összes tárolóján. Ez a lehetőség a költségmegtakarítást is segíti. |
    |**Átviteli sebesség**|400|Az átviteli sebesség 400 adategység/másodperc (RU/s) esetén. Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében.| 
    |**Tároló azonosítója**|Elemek|Adja meg az *elemeket* az új tároló neveként. A tárolóazonosítók nevére ugyanazok a karakterkorlátozások vonatkoznak, mint az adatbázisnevekre.|
    |**Partíciókulcs**| /kategória| A cikkben ismertetett minta a */category* használja a partíciós kulcsként.|
    
    Az előző beállításokon kívül opcionálisan hozzáadhat **egyedi kulcsokat** a tárolóhoz. Ebben a példában az erre szolgáló mezőt hagyja üresen. Az egyedi kulcsok lehetőséget nyújtanak a fejlesztők számára, hogy adatintegritási réteget adjanak az adatbázishoz. A tárolók létrehozásakor egyedi kulcsokra vonatkozó szabályzat létrehozásával biztosíthatja, hogy a partíciós kulcs egy vagy több értéke egyedi legyen. További információt az [Azure Cosmos DB-ben egyedi kulcsaival](unique-keys.md) kapcsolatos cikkben talál.
    
    Kattintson az **OK** gombra. Az Adatkezelő megjeleníti az új adatbázist és tárolót.

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
    pip install --pre azure-cosmos
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

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Azure Cosmos-fiókot, hogyan hozhat létre tárolót a Adatkezelő használatával, és hogyan futtathat egy alkalmazást. Most további adatokat importálhat a Cosmos DB-fiókba. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be az SQL API-hoz](import-data.md)


