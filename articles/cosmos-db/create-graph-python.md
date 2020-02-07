---
title: 'Gyors útmutató: Gremlin API a Pythonnal – Azure Cosmos DB használatával'
description: Ez a rövid útmutató azt ismerteti, hogy miként használható az Azure Cosmos DB Gremlin API konzolalkalmazások az Azure Portal és a Python használatával történő létrehozására.
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: python
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: lbosq
ms.openlocfilehash: b1286daaa76c71f88d44ea387a92876a8676783c
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062231"
---
# <a name="quickstart-create-a-graph-database-in-azure-cosmos-db-using-python-and-the-azure-portal"></a>Gyors útmutató: Graph-adatbázis létrehozása Azure Cosmos DB a Python és a Azure Portal használatával

> [!div class="op_single_selector"]
> * [Gremlin-konzol](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Ebben a rövid útmutatóban egy Azure Cosmos DB Gremlin (Graph) API-fiókot hoz létre és kezel a Azure Portalból, és az adatok hozzáadását a GitHubról klónozott Python-alkalmazás használatával végezheti el. A Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi a dokumentumok, tábla, kulcs-érték és gráf adatbázisok gyors létrehozását és lekérdezését globális terjesztési és horizontális méretezési képességekkel.

## <a name="prerequisites"></a>Előfeltételek
- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Vagy [próbálja ki Azure Cosmos db](https://azure.microsoft.com/try/cosmosdb/) ingyen Azure-előfizetés nélkül.
- [Python 3.5 +](https://www.python.org/downloads/) beleértve a [pip](https://pip.pypa.io/en/stable/installing/) Package telepítőt.
- [Python-illesztőprogram a Gremlin-hez](https://github.com/apache/tinkerpop/tree/master/gremlin-python).
- [Git](https://git-scm.com/downloads).

> [!NOTE]
> Ehhez a rövid útmutatóhoz 2017. december 20. után készült gráfadatbázis-fiókra van szükség. A meglévő fiókok azután támogatják a Pythont, hogy az általános elérhetőség érdekében migrálva lettek.

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

A gráfadatbázis létrehozásához először létre kell hoznia egy Gremlin- (Graph-) adatbázisfiókot az Azure Cosmos DB segítségével.

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Gráf hozzáadása

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig váltsunk át kódok használatára. A következő lépésekben elvégezheti a Gremlin API-alkalmazás klónozását a GitHubról, beállíthatja a kapcsolati sztringet, és futtathatja az alkalmazást. Látni fogja, milyen egyszerű az adatokkal programozott módon dolgozni.  

1. Nyisson meg egy parancssort, hozzon létre egy git-samples nevű új mappát, majd zárja be a parancssort.

    ```bash
    md "C:\git-samples"
    ```

2. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson a mappára, ahol telepíteni szeretné a mintaalkalmazást.  

    ```bash
    cd "C:\git-samples"
    ```

3. Az alábbi parancs futtatásával klónozhatja a mintatárházat. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-python-getting-started.git
    ```

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha meg szeretné ismerni, hogyan jönnek létre az adatbázis erőforrásai a kódban, tekintse át a következő kódrészleteket. A kódrészletek mind a *C:\git-samples\azure-cosmos-db-graph-python-getting-started\\* mappában található *Connect.py* -fájlból származnak. Egyéb esetben ugorhat [A kapcsolati sztring frissítése](#update-your-connection-information) szakaszra. 

* A Gremlin `client` inicializálása a 104-as sorban történik a *Connect.py*-ben:

    ```python
    ...
    client = client.Client('wss://<YOUR_ENDPOINT>.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ...
    ```

* A *Connect.py* -fájl elején megjelenő Gremlin lépések sorozata. Végrehajtásuk a `client.submitAsync()` metódus használatával történik:

    ```python
    client.submitAsync(_gremlin_cleanup_graph)
    ```

## <a name="update-your-connection-information"></a>A kapcsolati adatok frissítése

Lépjen vissza az Azure Portalra a kapcsolati adatokért, majd másolja be azokat az alkalmazásba. Ezek a beállítások lehetővé teszik az alkalmazás számára, hogy kommunikáljon az üzemeltetett adatbázissal.

1. A [Azure Portal](https://portal.azure.com/)Azure Cosmos db-fiókjában válassza a **kulcsok**lehetőséget. 

    Másolja az URI érték első részét.

    ![Hozzáférési kulcs megtekintése és másolása az Azure Portal Kulcsok oldalán](./media/create-graph-python/keys.png)

2. Nyissa meg a *Connect.py* fájlt, és az 104-es sorban illessze be az URI-értéket `<YOUR_ENDPOINT>` itt:

    ```python
    client = client.Client('wss://<YOUR_ENDPOINT>.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ```

    Az ügyfélobjektum URI részének most a következőhöz hasonlóan kell kinéznie:

    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ```

3. Módosítsa a `client` objektum második paraméterét a `<YOUR_DATABASE>` és `<YOUR_COLLECTION_OR_GRAPH>` sztringek lecseréléséhez. Ha a javasolt értékeket használta, a paraméternek ehhez a kódhoz hasonlóan kell kinéznie:

    `username="/dbs/sample-database/colls/sample-graph"`

    A teljes `client` objektumnak ehhez a kódhoz hasonlóan kell kinéznie:

    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/sample-database/colls/sample-graph", 
        password="<YOUR_PASSWORD>")
    ```

4. A **kulcsok** lapon a másolás gombbal másolja az elsődleges kulcsot, és illessze be `<YOUR_PASSWORD>`re a `password=<YOUR_PASSWORD>` paraméterben.

    A teljes `client` objektumdefiníciónak ehhez a kódhoz hasonlóan kell kinéznie:
    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/sample-database/colls/sample-graph", 
        password="asdb13Fadsf14FASc22Ggkr662ifxz2Mg==")
    ```

6. Mentse a *Connect.py* fájlt.

## <a name="run-the-console-app"></a>A konzolalkalmazás futtatása

1. A git terminálablakban a `cd` paranccsal lépjen az azure-cosmos-db-graph-python-getting-started mappába.

    ```git
    cd "C:\git-samples\azure-cosmos-db-graph-python-getting-started"
    ```

2. A git terminálablakában futtassa a következő parancsot a szükséges Python-csomagok telepítéséhez.

   ```
   pip install -r requirements.txt
   ```

3. A git terminálablakában futtassa a következő parancsot a Python-alkalmazás elindításához.
    
    ```
    python connect.py
    ```

    A terminálablakban megjelennek a gráfhoz hozzáadandó csúcspontok és élek. 
    
    Ha időtúllépési hibákat észlel, ellenőrizze, hogy megfelelően frissítette-e a kapcsolati adatokat [A kapcsolati adatok frissítése](#update-your-connection-information) szakaszban, és próbálja meg újból futtatni az utolsó parancsot. 
    
    Amikor a program leáll, nyomja le az Enter billentyűt, és lépjen vissza az Azure Portalra a webböngészőben.

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Áttekintés és mintaadatok hozzáadása

A csúcspontok és élek beszúrása után visszatérhet a Adatkezelőra, és megtekintheti a gráfhoz hozzáadott csúcspontokat, és további adatpontokat is hozzáadhat.

1. A Azure Portal Azure Cosmos DB-fiókjában válassza a **adatkezelő**, majd a **minta-gráf**csomópontot, válassza a **gráf**lehetőséget, majd válassza a **szűrő alkalmazása**lehetőséget. 

   ![Új dokumentumok létrehozása az Adatkezelővel az Azure Portalon](./media/create-graph-python/azure-cosmosdb-data-explorer-expanded.png)

2. Figyelje meg, hogy az **eredmények** listájában három új felhasználó kerül be a gráfba. A csúcspontokat szabadon mozgathatja húzással, nagyíthat és kicsinyíthet az egérgörgő görgetésével, illetve megnövelheti a gráf méretét a duplaszárú nyíllal. 

   ![Új csúcspontok az Azure Portal Adatkezelőjében megjelenő gráfban](./media/create-graph-python/azure-cosmosdb-graph-explorer-new.png)

3. Adjunk hozzá néhány új felhasználót. Az **új csúcspont** gomb kiválasztásával adhat hozzá adatait a gráfhoz.

   ![Új dokumentumok létrehozása az Adatkezelővel az Azure Portalon](./media/create-graph-python/azure-cosmosdb-data-explorer-new-vertex.png)

4. Adja meg a *person* címkét.

5. Válassza a **tulajdonság hozzáadása** lehetőséget a következő tulajdonságok hozzáadásához. Egyedi tulajdonságokat hozhat létre a gráfban található minden egyes személy számára. Csak az id kulcsot kötelező megadni.

    kulcs|érték|Megjegyzések
    ----|----|----
    PK|/pk| 
    id|ashley|A csúcspont egyedi azonosítója. Ha nem ad meg azonosítót, a rendszer létrehoz egyet.
    gender|female| 
    tech | java | 

    > [!NOTE]
    > Ebben a rövid útmutatóban egy nem particionált gyűjteményt hozunk létre. Ha azonban a gyűjtemény létrehozásakor megad egy partíciókulcsot és particionált gyűjteményt hoz létre, minden új csúcspontban meg kell adnia kulcsként a partíciókulcsot. 

6. Kattintson az **OK** gombra. Előfordulhat, hogy ki kell terjesztenie a képernyőt a képernyő alján lévő **OK** gomb megjelenítéséhez.

7. Válassza újra az **új csúcspontot** , és adjon hozzá egy további új felhasználót. 

8. Adja meg a *person* címkét.

9. Válassza a **tulajdonság hozzáadása** lehetőséget a következő tulajdonságok hozzáadásához:

    kulcs|érték|Megjegyzések
    ----|----|----
    PK|/pk| 
    id|rakesh|A csúcspont egyedi azonosítója. Ha nem ad meg azonosítót, a rendszer létrehoz egyet.
    gender|male| 
    school|MIT| 

10. Kattintson az **OK** gombra. 

11. Válassza a **szűrő alkalmazása** gombot az alapértelmezett `g.V()` szűrővel a gráf összes értékének megjelenítéséhez. Most már az összes felhasználó megjelenik a **Találatok** listában. 

    Ha további adatokat ad meg, szűrőkkel csökkentheti a találatok számát. Az Adatkezelő alapértelmezés szerint a `g.V()` lekérdezést használja a gráf összes csúcspontjának lekéréséhez. Ezt más [gráflekérdezésre](tutorial-query-graph.md) is módosíthatja, például a `g.V().count()` lekérdezésre, ha azt szeretné, hogy a rendszer JSON formátumban adja vissza a gráf csúcspontjainak számát. Ha módosította a szűrőt, váltson vissza `g.V()`re, és válassza a **szűrő alkalmazása** lehetőséget az összes eredmény ismételt megjelenítéséhez.

12. Most már összekapcsolhatjuk a rakesh és az ashley elemet. Győződjön meg arról, hogy az **Ashley** ki van választva az **eredmények** listájában, majd kattintson a jobb alsó sarokban a **célok** elem melletti Szerkesztés gombra. Előfordulhat, hogy szélesebbre kell állítania az ablakot a **Tulajdonságok** terület megjelenítéséhez.

    ![Gráfcsúcspont céljának módosítása](./media/create-graph-python/azure-cosmosdb-data-explorer-edit-target.png)

13. A **cél** *mezőbe írja be*a következőt:, majd a **peremhálózat** mezőjébe írja be a *Knows*elemet, majd jelölje be a jelölőnégyzetet.

    ![ashley és rakesh közötti kapcsolat hozzáadása az Adatkezelőben](./media/create-graph-python/azure-cosmosdb-data-explorer-set-target.png)

14. Ezután válassza ki a **rakesh** elemet a találatok listájából. Láthatja, hogy az ashley és a rakesh elem össze van kapcsolva. 

    ![Két összekapcsolt csúcspont az Adatkezelőben](./media/create-graph-python/azure-cosmosdb-graph-explorer.png)

Ezzel befejezte az oktatóanyag erőforrások létrehozásra vonatkozó részét. A gráfhoz továbbra is hozzáadhat csúcspontokat, módosíthatja a meglévő csúcspontokat, és megváltoztathatja a lekérdezéseket. Most pedig tekintsük át az Azure Cosmos DB által biztosított mérőszámokat, majd távolítsuk el az erőforrásokat. 

## <a name="review-slas-in-the-azure-portal"></a>Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan hozhat létre egy Azure Cosmos DB fiókot, hogyan hozhat létre egy gráfot a Adatkezelő használatával, és hogyan futtathat egy Python-alkalmazást a gráfhoz való adatfelvételhez. Az útmutató információira támaszkodva összetett lekérdezéseket hozhat létre és hatékony gráfbejárási logikákat helyezhet üzembe a Gremlin használatával. 

> [!div class="nextstepaction"]
> [Lekérdezés a Gremlin használatával](tutorial-query-graph.md)

