---
title: 'Gyors útmutató: Gremlin API a Pythonnal – Azure Cosmos DB használatával'
description: Ez a rövid útmutató azt ismerteti, hogy miként használható az Azure Cosmos DB Gremlin API konzolalkalmazások az Azure Portal és a Python használatával történő létrehozására.
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: python
ms.topic: quickstart
ms.date: 01/22/2018
ms.author: lbosq
ms.openlocfilehash: 5116cea15b30c4248e5942447261f0bc5c375520
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56585944"
---
# <a name="quickstart-create-a-graph-database-in-azure-cosmso-db-using-python-and-the-azure-portal"></a>Gyors útmutató: A gráfadatbázis létrehozásához az Azure Cosmso DB Python és az Azure portal használatával

> [!div class="op_single_selector"]
> * [Gremlin-konzol](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Ez a rövid útmutató azt ismerteti, hogy miként használható a Python és az Azure Cosmos DB [Gremlin API](graph-introduction.md) egy konzolalkalmazás egy GitHubról származó példa klónozásával történő létrehozására. A rövid útmutató emellett azt is bemutatja, hogyan hozható létre egy Azure Cosmos DB-fiók a webes alapú Azure Portal használatával.   

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum, tábla, kulcs-érték és gráf típusú adatbázisokat, amelyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket.  

> [!NOTE]
> Ehhez a rövid útmutatóhoz 2017. december 20. után készült gráfadatbázis-fiókra van szükség. A meglévő fiókok azután támogatják a Pythont, hogy az általános elérhetőség érdekében migrálva lettek.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Alternatív lehetőségként [kipróbálhatja ingyenesen az Azure Cosmos DB-t](https://azure.microsoft.com/try/cosmosdb/) Azure-előfizetés, díjfizetés és elköteleződés nélkül.

Továbbá:
* [Python](https://www.python.org/downloads/) 3.5-ös vagy újabb verzió
* [pip csomagkezelő](https://pip.pypa.io/en/stable/installing/)
* [Git](https://git-scm.com/)
* [Python-illesztő a Gremlinhez](https://github.com/apache/tinkerpop/tree/master/gremlin-python)

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

3. Futtassa a következő parancsot a minta tárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-python-getting-started.git
    ```

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha meg szeretné ismerni, hogyan jönnek létre az adatbázis erőforrásai a kódban, tekintse át a következő kódrészleteket. A kódrészletek mind a C:\git-samples\azure-cosmos-db-graph-python-getting-started\ mappában található connect.py fájlból származnak. Egyéb esetben ugorhat [A kapcsolati sztring frissítése](#update-your-connection-information) szakaszra. 

* A Gremlin `client` inicializálása a `connect.py` 104. sorában történik:

    ```python
    ...
    client = client.Client('wss://<YOUR_ENDPOINT>.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ...
    ```

* A Gremlinnel kapcsolatos lépések sorozata a `connect.py` fájl elején szerepel. Végrehajtásuk a `client.submitAsync()` metódus használatával történik:

    ```python
    client.submitAsync(_gremlin_cleanup_graph)
    ```

## <a name="update-your-connection-information"></a>A kapcsolatadatok frissítése

Lépjen vissza az Azure Portalra a kapcsolati adatokért, majd másolja be azokat az alkalmazásba. Ezek a beállítások lehetővé teszik az alkalmazás számára, hogy kommunikáljon az üzemeltetett adatbázissal.

1. Az [Azure Portalon](https://portal.azure.com/) kattintson a **Kulcsok** lehetőségre. 

    Másolja az URI érték első részét.

    ![Hozzáférési kulcs megtekintése és másolása az Azure Portal Kulcsok oldalán](./media/create-graph-python/keys.png)

2. Nyissa meg a connect.py fájlt, és a 104. sorba illessze be az URI értéket itt a `<YOUR_ENDPOINT>` helyére:

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

4. Az Azure Portalon a másolás gombbal másolja az ELSŐDLEGES KULCSOT, és illessze a `<YOUR_PASSWORD>` helyére a `password=<YOUR_PASSWORD>` paraméterben.

    A teljes `client` objektumdefiníciónak ehhez a kódhoz hasonlóan kell kinéznie:
    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/sample-database/colls/sample-graph", 
        password="asdb13Fadsf14FASc22Ggkr662ifxz2Mg==")
    ```

6. Mentse a `connect.py` fájlt.

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

Ezután visszaléphet az Adatkezelőbe, és megtekintheti a gráfhoz hozzáadott csúcspontokat, valamint további adatpontokat is hozzáadhat.

1. Kattintson az **Adatkezelőre**, bontsa ki a **sample-graph** csomópontot, és kattintson a **Gráf**, majd a **Szűrő alkalmazása** lehetőségre. 

   ![Új dokumentumok létrehozása az Azure Portal Adatkezelőjében](./media/create-graph-python/azure-cosmosdb-data-explorer-expanded.png)

2. A **Találatok** listában megjelennek a gráfhoz hozzáadott új felhasználók. Ha kiválasztja a **ben** elemet, láthatja, hogy a robin elemhez kapcsolódik. A csúcspontokat szabadon mozgathatja húzással, nagyíthat és kicsinyíthet az egérgörgő görgetésével, illetve megnövelheti a gráf méretét a duplaszárú nyíllal. 

   ![Új csúcspontok az Azure Portal Adatkezelőjében megjelenő gráfban](./media/create-graph-python/azure-cosmosdb-graph-explorer-new.png)

3. Adjunk hozzá néhány új felhasználót. Adatok a gráfhoz való hozzáadásához kattintson az **Új csúcspont** gombra.

   ![Új dokumentumok létrehozása az Azure Portal Adatkezelőjében](./media/create-graph-python/azure-cosmosdb-data-explorer-new-vertex.png)

4. Adja meg a *person* címkét.

5. Kattintson a **Tulajdonság hozzáadása** lehetőségre a következő tulajdonságok hozzáadásához. Egyedi tulajdonságokat hozhat létre a gráfban található minden egyes személy számára. Csak az id kulcsot kötelező megadni.

    kulcs|érték|Megjegyzések
    ----|----|----
    id|ashley|A csúcspont egyedi azonosítója. Ha nem ad meg azonosítót, a rendszer létrehoz egyet.
    gender|female| 
    tech | java | 

    > [!NOTE]
    > Ebben a rövid útmutatóban egy nem particionált gyűjteményt hozunk létre. Ha azonban a gyűjtemény létrehozásakor megad egy partíciókulcsot és particionált gyűjteményt hoz létre, minden új csúcspontban meg kell adnia kulcsként a partíciókulcsot. 

6. Kattintson az **OK** gombra. Előfordulhat, hogy ki kell terjesztenie a képernyőt a képernyő alján lévő **OK** gomb megjelenítéséhez.

7. Kattintson ismét az **Új csúcspont** lehetőségre, és adjon hozzá még egy új felhasználót. 

8. Adja meg a *person* címkét.

9. Kattintson a **Tulajdonság hozzáadása** lehetőségre a következő tulajdonságok hozzáadásához:

    kulcs|érték|Megjegyzések
    ----|----|----
    id|rakesh|A csúcspont egyedi azonosítója. Ha nem ad meg azonosítót, a rendszer létrehoz egyet.
    gender|male| 
    school|MIT| 

10. Kattintson az **OK** gombra. 

11. Kattintson a **Szűrő alkalmazása** gombra a gráf összes értékének megtekintéséhez az alapértelmezett `g.V()` szűrővel. Most már az összes felhasználó megjelenik a **Találatok** listában. 

    Ha további adatokat ad meg, szűrőkkel csökkentheti a találatok számát. Az Adatkezelő alapértelmezés szerint a `g.V()` lekérdezést használja a gráf összes csúcspontjának lekéréséhez. Ezt más [gráflekérdezésre](tutorial-query-graph.md) is módosíthatja, például a `g.V().count()` lekérdezésre, ha azt szeretné, hogy a rendszer JSON formátumban adja vissza a gráf csúcspontjainak számát. Ha módosította a szűrőt, állítsa vissza a szűrőt a `g.V()` lekérdezésre, majd kattintson a **Szűrő alkalmazása** lehetőségre az eredmények újbóli megjelenítéséhez.

12. Most már összekapcsolhatjuk a rakesh és az ashley elemet. Győződjön meg arról, hogy az **ashley** elem van kijelölve a **Találatok** listában, majd kattintson a jobb alsó sarokban, a **Célok** elem mellett lévő Szerkesztés gombra. Előfordulhat, hogy szélesebbre kell állítania az ablakot a **Tulajdonságok** terület megjelenítéséhez.

   ![Gráfcsúcspont céljának módosítása](./media/create-graph-python/azure-cosmosdb-data-explorer-edit-target.png)

13. A **Cél** mezőbe írja be a *rakesh* nevet, az **Élcímke** mezőbe pedig a *knows* karakterláncot, majd jelölje be a jelölőnégyzetet.

   ![ashley és rakesh közötti kapcsolat hozzáadása az Adatkezelőben](./media/create-graph-python/azure-cosmosdb-data-explorer-set-target.png)

14. Ezután válassza ki a **rakesh** elemet a találatok listájából. Láthatja, hogy az ashley és a rakesh elem össze van kapcsolva. 

   ![Két összekapcsolt csúcspont az Adatkezelőben](./media/create-graph-python/azure-cosmosdb-graph-explorer.png)

   Ezzel befejezte az oktatóanyag erőforrások létrehozásra vonatkozó részét. A gráfhoz továbbra is hozzáadhat csúcspontokat, módosíthatja a meglévő csúcspontokat, és megváltoztathatja a lekérdezéseket. Most pedig tekintsük át az Azure Cosmos DB által biztosított mérőszámokat, majd távolítsuk el az erőforrásokat. 

## <a name="review-slas-in-the-azure-portal"></a>Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban bemutattuk, hogyan lehet Azure Cosmos DB-fiókot létrehozni, hogyan lehet az Adatkezelő segítségével gráfot készíteni, és hogyan lehet futtatni az alkalmazást. Most már készen áll arra, hogy a Gremlin használatával összetettebb lekérdezéseket hozzon létre és hatékony gráfbejárási logikákat implementáljon. 

> [!div class="nextstepaction"]
> [Lekérdezés a Gremlin használatával](tutorial-query-graph.md)

