---
title: 'Rövid útmutató: Python-alkalmazás létrehozása az Azure Cosmos DB SQL API-fiókkal'
description: Egy Python-kódmintát mutat be, amellyel csatlakozni lehet az Azure Cosmos DB SQL API-hoz, és lekérdezést lehet végezni vele
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 03/09/2020
ms.author: sngun
ms.custom:
- seodec18
- seo-javascript-september2019
- seo-python-october2019
ms.openlocfilehash: 10247e22b3fbe1250a15b06a0cce974905ca6b7f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78942620"
---
# <a name="quickstart-build-a-python-application-using-an-azure-cosmos-db-sql-api-account"></a>Rövid útmutató: Python-alkalmazás létrehozása Egy Azure Cosmos DB SQL API-fiókkal

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Ebben a rövid útmutatóban hozzon létre és kezeljen egy Azure Cosmos DB SQL API-fiókot az Azure Portalról, és a Visual Studio-kódból a GitHubról klónozott Python-alkalmazással. Az Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi a dokumentumok, a tábla, a kulcsérték és a grafikonadatbázisok gyors létrehozását és lekérdezését globális terjesztési és horizontális méretezési képességekkel.

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Vagy [próbálja ki az Azure Cosmos DB-t ingyenesen](https://azure.microsoft.com/try/cosmosdb/) Azure-előfizetés nélkül. Az [Azure Cosmos DB emulátort](https://aka.ms/cosmosdb-emulator) is `https://localhost:8081` használhatja `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`a kulcs URI-jával és a kulccsal.
- [Python 3.6 +](https://www.python.org/downloads/) `python` , a `PATH`futtatható a .
- [Visual Studio kód](https://code.visualstudio.com/).
- A [Visual Studio-kód Python-bővítménye](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview).
- [Git](https://www.git-scm.com/downloads). 

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Tároló hozzáadása

Most már használhatja a Data Explorer eszközt az Azure Portalon egy adatbázis és tároló létrehozásához. 

1. Válassza az **Adatkezelő** > **új tárolóját**. 
    
    A **Tároló hozzáadása** terület a jobb szélen jelenik meg, előfordulhat, hogy jobbra kell görgetnie, hogy láthassa.

    ![Az Azure Portal Adatkezelője a Tároló hozzáadása panellel](./media/create-sql-api-python/azure-cosmosdb-data-explorer.png)

2. A **Tároló hozzáadása** lapon adja meg az új tároló beállításait.

    |Beállítás|Ajánlott érték|Leírás
    |---|---|---|
    |**Adatbázis azonosítója**|Feladatok|Az új adatbázisnak adja a *Feladatok* nevet. Az adatbázisneveknek 1 és 255 karakter `/, \\, #, ?`között kell lennie, és nem tartalmazhatnak , illetve záró szóközt. Ellenőrizze a **létesítési adatbázis átviteli beállítás,** lehetővé teszi, hogy megosszák az adatbázis számára kiosztott átviteli átmenő az adatbázis összes tárolója között. Ez az opció is segít a költségmegtakarítást. |
    |**Teljesítmény**|400|Hagyja az átviteli értéket 400 kérelemegység/másodperc (RU/s) értéken. Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében.| 
    |**Tároló azonosítója**|Elemek|Írja be *az elemeket* az új tároló neveként. A tárolóazonosítók nevére ugyanazok a karakterkorlátozások vonatkoznak, mint az adatbázisnevekre.|
    |**Partíciókulcs**| /kategória| A cikkben ismertetett minta a */category* kapcsolót használja partíciókulcsként.|
    
    Az előző beállításokon kívül szükség esetén **egyedi kulcsokat** is hozzáadhat a tárolóhoz. Ebben a példában az erre szolgáló mezőt hagyja üresen. Az egyedi kulcsok lehetőséget nyújtanak a fejlesztők számára, hogy adatintegritási réteget adjanak az adatbázishoz. Ha egy tároló létrehozása közben létrehoz egy egyedi kulcsházirendet, biztosítja partíciókulcsonként egy vagy több érték egyediségét. További információt az [Azure Cosmos DB-ben egyedi kulcsaival](unique-keys.md) kapcsolatos cikkben talál.
    
    Válassza **az OK gombot.** Az Adatkezelő megjeleníti az új adatbázist és tárolót.

## <a name="add-sample-data"></a>Mintaadatok hozzáadása

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Adatok lekérdezése

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig klónozzon egy SQL API-alkalmazást a GitHubról, állítsa be a kapcsolati sztringet, és futtassa az alkalmazást. Ez a rövid útmutató a [Python SDK](https://pypi.org/project/azure-cosmos/#history)4-es verzióját használja.

1. Nyisson meg egy parancssort, hozzon létre egy git-samples nevű mappát, majd zárja be a parancssort.

    ```cmd
    md "git-samples"
    ```
   Ha bash-sort használ, a következő parancsot kell használnia:

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

1. Az Azure Cosmos DB-fiókjában az [Azure Portalon](https://portal.azure.com/)válassza **a kulcsok a** bal oldali navigációs. A képernyő jobb oldalán található másolási gombokkal másolja az **URI-t** és az **elsődleges kulcsot** a *cosmos_get_started.py* fájlba a következő lépésben.

    ![Hozzáférési kulcs és URI beszereznie az Azure Portal Keys-beállításaiban](./media/create-sql-api-dotnet/access-key-and-uri-in-keys-settings-in-the-azure-portal.png)

2. A Visual Studio-kódban nyissa meg a *cosmos_get_started.py* fájlt *\git-samples\azure-cosmos-db-python-getting-started*mappában.

3. Másolja az **URI-értéket** a portálról (a másolás gombhasználatával), és tegye a **végpontváltozó** értékére *a cosmos_get_started.py*. 

    `endpoint = 'https://FILLME.documents.azure.com',`

4. Ezután másolja a **PRIMARY KEY** értékét a portálról, és tegye a **kulcs** értékére *a cosmos_get_started.py*. Ezzel frissítette az alkalmazást az összes olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. 

    `key = 'FILLME'`

5. Mentse a *cosmos_get_started.py* fájlt.

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ismerje meg a kódban létrehozott adatbázis-erőforrásokat, vagy ugorjon [előre a Kapcsolati karakterlánc frissítése című](#update-your-connection-string)részre.

A következő kódrészletek mind a *cosmos_get_started.py* fájlból származnak.

* A CosmosClient inicializálva van. Győződjön meg arról, hogy frissíti a "végpont" és a "kulcs" értékeket a [kapcsolati karakterlánc frissítése](#update-your-connection-string) szakaszban leírtak szerint. 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_cosmos_client)]

* A rendszer létrehozza az új adatbázist.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_database_if_not_exists)]

* Egy új tároló jön létre, 400 RU/s [kiépített átviteli fokkal.](request-units.md) Úgy `lastName` döntünk, mint a [partíciókulcs](partitioning-overview.md#choose-partitionkey), amely lehetővé teszi számunkra, hogy hatékony lekérdezéseket, amelyek szűrik ezt a tulajdonságot. 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_container_if_not_exists)]

* Egyes elemek hozzá vannak adva a tárolóhoz. A tárolók olyan elemek (JSON-dokumentumok) gyűjteményei, amelyek változatos sémával rendelkezhetnek. A segítő ```get_[name]_family_item``` módszerek az Azure Cosmos DB-ben json-dokumentumokként tárolt család ábrázolásait adják vissza.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_item)]

* A pontolvasások (kulcsérték-keresgések) a `read_item` módszerrel történnek. Kinyomtatjuk az egyes műveletek [vt.-díját.](request-units.md)

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=read_item)]

* A lekérdezés SQL-lekérdezés szintaxisával történik. Mivel a WHERE záradékpartíciókulcs-értékeket ```lastName``` használunk, az Azure Cosmos DB hatékonyan továbbítja ezt a lekérdezést a megfelelő partíciókra, javítva a teljesítményt.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=query_items)]
   
## <a name="run-the-app"></a>Az alkalmazás futtatása

1. A Visual Studio-kódban válassza a**Parancspaletta** **megtekintése** > lehetőséget. 

2. A megjelenő panelen írja be a **Python: Select Interpreter** szöveget, majd válassza ki a használandó Python-verziót.

    A választott értelmező ekkor megjelenik a Visual Studio Code ablakának állapotsorában. 

3. Válassza az**Integrált terminál** **megtekintése** > lehetőséget a Visual Studio Code integrált terminál megnyitásához.

4. Az integrált terminálablakban győződjön meg arról, hogy az *azure-cosmos-db-python-getting-started* mappában van. Ha más mappában van, akkor váltson erre a mintamappára az alábbi parancs futtatásával. 

    ```cmd
    cd "\git-samples\azure-cosmos-db-python-getting-started"`
    ```

5. Futtassa az alábbi parancsot az azure-cosmos csomag telepítéséhez. 

    ```python
    pip install --pre azure-cosmos
    ```

    Ha az azure-cosmos csomag telepítésekor „hozzáférés megtagadva” hibaüzenetet kap, akkor [rendszergazdaként kell futtatnia a VS Code alkalmazást](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights).

6. Futtassa a következő parancsot a minta futtatásához, és hozzon létre és tároljon új dokumentumokat az Azure Cosmos DB-ben.

    ```python
    python cosmos_get_started.py
    ```

7. Az új elemek létrehozásának és mentésének ellenőrzéséhez az Azure Portalon válassza a **Data Explorer** > **AzureSampleFamilyDatabase** > **Items lehetőséget.** A létrehozott elemek megtekintése. Például, itt van egy minta JSON dokumentum az Andersen család:
   
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

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Azure Cosmos DB-fiókot, hogyan hozhat létre egy tárolót az Adatkezelővel, és futtathatja a Python-alkalmazást a Visual Studio-kódban. Így már további adatokat importálhat az Azure Cosmos DB-fiókba. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be az SQL API-hoz](import-data.md)


