---
title: 'Az Azure Cosmos DB: Alkalmazás létrehozása a Python és az SQL API-val'
description: Egy Python-kódmintát mutat be, amellyel csatlakozni lehet az Azure Cosmos DB SQL API-hoz, és lekérdezést lehet végezni vele
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.custom: quick start connect, mvc, devcenter
ms.devlang: python
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: sngun
ms.openlocfilehash: a12107fe0dd25a383e7be1adbc717c7c1c654fa5
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52846538"
---
# <a name="azure-cosmos-db-build-a-sql-api-app-with-python-and-the-azure-portal"></a>Azure Cosmos DB: SQL API-alkalmazás létrehozása a Python és az Azure Portal használatával

> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [.NET (előzetes verzió)](create-sql-api-dotnet-preview.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum, kulcs/érték és gráf típusú adatbázisokat, amelyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket. 

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Azure Cosmos DB [SQL API](sql-api-introduction.md)-fiókot, dokumentum-adatbázist és tárolót az Azure Portal segítségével. Ezután megtudhatja, hogyan hozhat létre és futtathat egy, a Python SDK for [SQL API](sql-api-sdk-python.md)-val létrehozott konzolalkalmazást. Ez a rövid útmutató a [Python SDK] 3.0-s verzióját használja.(https://pypi.org/project/azure-cosmos)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Python 3.6](https://www.python.org/downloads/), a PATH környezeti változóhoz hozzáadott \<telepítési hely\>\Python36 és \<telepítési hely>\Python36\Scripts útvonallal. 
* [Visual Studio Code](https://code.visualstudio.com/)
* [Python-bővítmény a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview)

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Gyűjtemény hozzáadása

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Mintaadatok hozzáadása

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Adatok lekérdezése

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig klónozzon egy SQL API-alkalmazást a GitHubról, állítsa be a kapcsolati sztringet, és futtassa az alkalmazást.

1. Nyisson meg egy parancssort, hozzon létre egy git-samples nevű új mappát, majd zárja be a parancssort.

    ```bash
    md "C:\git-samples"
    ```

2. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson az új mappára, ahol telepíteni szeretné a mintaalkalmazást.

    ```bash
    cd "C:\git-samples"
    ```

3. Futtassa a következő parancsot a minta tárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-python-getting-started.git
    ```  
    
## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha meg szeretné ismerni, hogyan jönnek létre az adatbázis erőforrásai a kódban, tekintse át a következő kódrészleteket. Egyéb esetben ugorhat [A kapcsolati sztring frissítése](#update-your-connection-string) szakaszra. 

Megjegyzés: Ha már ismeri a Python SDK korábbi verzióját, ismerősek lehetnek a „gyűjtemény” és a „dokumentum” kifejezések. Mivel az Azure Cosmos DB több API-modellt támogat, a Python SDK 3.0-s vagy újabb verziói az általános „tároló” (gyűjtemény, gráf vagy tábla) és az „elem” (a tárolók elemei) kifejezést használják.

Az alábbi kódrészletek mind a `CosmosGetStarted.py` fájlból származnak.

* A CosmosClient inicializálva van.

    ```python
    # Initialize the Cosmos client
    client = cosmos_client.CosmosClient(url_connection=config['ENDPOINT'], auth={'masterKey': config['MASTERKEY']})
    ```

* A rendszer létrehozza az új adatbázist.

    ```python
    # Create a database
    db = client.CreateDatabase({ 'id': config['DATABASE'] })
    ```

* A rendszer létrehozza az új gyűjteményt.

    ```python
    # Create collection options
    options = {
        'offerThroughput': 400
    }

    # Create a container
    container = client.CreateContainer(db['_self'], container_definition, options)
    ```

* Egyes elemek hozzá vannak adva a tárolóhoz.

    ```python
    # Create and add some items to the container
    item1 = client.CreateItem(container['_self'], {
        'serverId': 'server1',
        'Web Site': 0,
        'Cloud Service': 0,
        'Virtual Machine': 0,
        'message': 'Hello World from Server 1!'
        }
    )

    item2 = client.CreateItem(container['_self'], {
        'serverId': 'server2',
        'Web Site': 1,
        'Cloud Service': 0,
        'Virtual Machine': 0,
        'message': 'Hello World from Server 2!'
        }
    )
    ```

* A rendszer végrehajt egy lekérdezést az SQL használatával

    ```python
    query = {'query': 'SELECT * FROM server s'}

    options = {}
    options['enableCrossPartitionQuery'] = True
    options['maxItemCount'] = 2

    result_iterable = client.QueryItems(container['_self'], query, options)
    for item in iter(result_iterable):
        print(item['message'])
    ```

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Lépjen vissza az Azure Portalra a kapcsolati sztring adataiért, majd másolja be azokat az alkalmazásba.

1. Az [Azure Portalon](http://portal.azure.com/), az Azure Cosmos DB-fiók bal oldali navigációs sávján kattintson a **Kulcsok** elemre. A következő lépésben a képernyő jobb oldalán lévő másolási gombokkal másolhatja az **URI** és az **Elsődleges kulcs** értékét a `CosmosGetStarted.py` fájlba.

    ![Hozzáférési kulcs megtekintése és másolása az Azure Portal kulcsok paneljén](./media/create-sql-api-dotnet/keys.png)

2. Nyissa meg a `CosmosGetStarted.py` fájlt a C:\git-samples\azure-cosmos-db-python-getting-started elérési úton a Visual Studio Code-ban.

3. A másolási gomb használatával másolja ki az **URI** érteket a Portalról, és azt adja meg a **végpont** kulcs értékeként a ``CosmosGetStarted.py`` fájlban. 

    `'ENDPOINT': 'https://FILLME.documents.azure.com',`

4. Másolja az **ELSŐDLEGES KULCS** értékét a Portalról, és azt adja meg a **config.PRIMARYKEY** értékeként a ``CosmosGetStarted.py`` fájlban. Az alkalmazás frissítve lett minden olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. 

    `'PRIMARYKEY': 'FILLME',`

5. Mentse a ``CosmosGetStarted.py`` fájlt.
    
## <a name="run-the-app"></a>Az alkalmazás futtatása

1. Válassza a Visual Studio Code-ban a **Nézet**>**Parancskatalógus** lehetőséget. 

2. A megjelenő panelen írja be a **Python: Select Interpreter** szöveget, majd válassza ki a használandó Python-verziót.

    A választott értelmező ekkor megjelenik a Visual Studio Code ablakának állapotsorában. 

3. Válassza a **Nézet** > **Beépített terminál** parancsot a Visual Studio Code beépített termináljának megnyitásához.

4. A beépített terminál ablakában győződjön meg róla, hogy az „azure-cosmos-db-python-getting-started” mappában van. Ha más mappában van, akkor váltson erre a mintamappára az alábbi parancs futtatásával. 

    ```
    cd "C:\git-samples\azure-cosmos-db-python-getting-started"`
    ```

5. Futtassa az alábbi parancsot az azure-cosmos csomag telepítéséhez. 

    ```
    pip3 install azure-cosmos
    ```

    Ha az azure-cosmos csomag telepítésekor „hozzáférés megtagadva” hibaüzenetet kap, akkor [rendszergazdaként kell futtatnia a VS Code alkalmazást](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights).

6. Hajtsa végre az alábbi parancsot az Azure Cosmos dB-ben az új dokumentumokat létrehozó és mentő mintakód futtatásához.

    ```
    python CosmosGetStarted.py
    ```

7. Az új elemek létrehozásának és mentésének megerősítéséhez az Azure Portalon válassza az **Adatkezelő** lehetőséget, bontsa ki a **coll** elemet, bontsa ki a **Documents** elemet, és válassza a **server1** dokumentumot. A server1 dokumentum tartalmának meg kell egyeznie a beépített terminál ablakában visszaadott tartalommal. 

    ![Új dokumentumok megtekintése az Azure Portalon](./media/create-sql-api-python/azure-cosmos-db-confirm-documents.png)

## <a name="review-slas-in-the-azure-portal"></a>Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban bemutattuk, hogyan lehet Azure Cosmos DB-fiókot létrehozni, hogyan lehet az Adatkezelő segítségével gyűjteményt készíteni, és hogyan lehet futtatni az alkalmazást. Így már további adatokat importálhat a Cosmos DB-fiókba. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be az SQL API-hoz](import-data.md)


