---
title: Oktatóanyag – Azure Databricks implementálása Cosmos DB-végponttal
description: Ez az oktatóanyag azt ismerteti, hogyan valósítható meg a virtuális hálózatban a Cosmos DB számára engedélyezett szolgáltatási végponttal rendelkező Azure Databricks.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 04/17/2019
ms.openlocfilehash: 4ac8c01e986cf1f3158c615a0791ba476e5bf1bb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "74706166"
---
# <a name="tutorial-implement-azure-databricks-with-a-cosmos-db-endpoint"></a>Oktatóanyag: Azure Databricks implementálása Cosmos DB-végponttal

Ez az oktatóanyag azt ismerteti, hogyan valósítható meg a VNet Beinjektált Databricks-környezet egy Cosmos DB számára engedélyezett szolgáltatási végponttal.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Azure Databricks munkaterület létrehozása egy virtuális hálózaton
> * Cosmos DB szolgáltatási végpont létrehozása
> * Cosmos DB fiók létrehozása és az adatimportálás
> * Azure Databricks-fürt létrehozása
> * Cosmos DB lekérdezése egy Azure Databricks jegyzetfüzetből

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, tegye a következőket:

* Hozzon létre egy [Azure Databricks munkaterületet egy virtuális hálózaton](quickstart-create-databricks-workspace-vnet-injection.md).

* Töltse le a [Spark-összekötőt](https://search.maven.org/remotecontent?filepath=com/microsoft/azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.4/azure-cosmosdb-spark_2.4.0_2.11-1.3.4-uber.jar).

* A [NOAA nemzeti központokból származó mintaadatok letöltése környezeti információkhoz](https://www.ncdc.noaa.gov/stormevents/). Válasszon ki egy állapotot vagy egy körzetet, és válassza a **Keresés**lehetőséget. A következő lapon fogadja el az alapértelmezett beállításokat, majd válassza a **Keresés**lehetőséget. Ezután válassza ki a **CSV-Letöltés** elemet a lap bal oldalán az eredmények letöltéséhez.

* Töltse le a Azure Cosmos DB adatáttelepítési eszköz [előre lefordított bináris](https://aka.ms/csdmtool) fájlját.

## <a name="create-a-cosmos-db-service-endpoint"></a>Cosmos DB szolgáltatási végpont létrehozása

1. Miután telepített egy Azure Databricks munkaterületet egy virtuális hálózatra, navigáljon a virtuális hálózathoz a [Azure Portal](https://portal.azure.com). Figyelje meg az Databricks-telepítéssel létrehozott nyilvános és magánhálózati alhálózatokat.

   ![Virtuális hálózati alhálózatok](./media/service-endpoint-cosmosdb/virtual-network-subnets.png)

2. Válassza ki a *nyilvános alhálózatot* , és hozzon létre egy Cosmos db szolgáltatási végpontot. Ezután **mentse**.
   
   ![Cosmos DB szolgáltatási végpont hozzáadása](./media/service-endpoint-cosmosdb/add-cosmosdb-service-endpoint.png)

## <a name="create-a-cosmos-db-account"></a>Cosmos DB-fiók létrehozása

1. Nyissa meg az Azure Portalt. A képernyő bal felső részén válassza az **erőforrás létrehozása > adatbázisok > Azure Cosmos db**lehetőséget.

2. Töltse ki a **példány részleteit** az **alapok** lapon a következő beállításokkal:

   |Beállítás|Érték|
   |-------|-----|
   |Előfizetés|*az előfizetése*|
   |Erőforráscsoport|*az erőforráscsoport*|
   |Fiók neve|db-vnet-Service-Endpoint|
   |API|Core (SQL)|
   |Hely|USA nyugati régiója|
   |Georedundancia|Letiltás|
   |Többrégiós írók|Bekapcsolás|

   ![Cosmos DB szolgáltatási végpont hozzáadása](./media/service-endpoint-cosmosdb/create-cosmosdb-account-basics.png)

3. Válassza a **hálózat** fület, és konfigurálja a virtuális hálózatot. 

   a. Válassza ki az előfeltételként létrehozott virtuális hálózatot, majd válassza a *nyilvános alhálózat*elemet. Figyelje meg, hogy a *privát alhálózat* megjegyzése *hiányzik a "Microsoft AzureCosmosDB" végponttal*. Ennek az az oka, hogy a Cosmos DB szolgáltatási végpontot csak a *nyilvános alhálózaton*engedélyezte.

   b. Győződjön meg arról, hogy engedélyezte **a hozzáférést Azure Portal** engedélyezve. Ezzel a beállítással elérheti Cosmos DB fiókját a Azure Portal. Ha ez a beállítás a **Megtagadás**értékre van állítva, akkor hibaüzenet jelenik meg, amikor megpróbál hozzáférni a fiókjához. 

   > [!NOTE]
   > Ez az oktatóanyag nem szükséges, de engedélyezheti az *IP-címekről való hozzáférés engedélyezését* , ha azt szeretné, hogy a helyi számítógépről is hozzáférhessen a Cosmos db-fiókjához. Ha például az Cosmos DB SDK-val csatlakozik fiókjához, akkor engedélyeznie kell ezt a beállítást. Ha a szolgáltatás le van tiltva, a "hozzáférés megtagadva" hibaüzenetek jelennek meg.

   ![Cosmos DB fiók hálózati beállításai](./media/service-endpoint-cosmosdb/create-cosmosdb-account-network.png)

4. Válassza a **felülvizsgálat + létrehozás**lehetőséget, majd **hozza** létre a Cosmos db-fiókját a virtuális hálózaton belül.

5. Miután létrehozta a Cosmos DB fiókját, navigáljon a **Beállítások**területen a **kulcsok** elemre. Másolja az elsődleges kapcsolatok karakterláncát, és mentse egy szövegszerkesztőben későbbi használatra.

    ![Cosmos DB-fiók kulcsainak lapja](./media/service-endpoint-cosmosdb/cosmos-keys.png)

6. Válassza a **adatkezelő** és az **új gyűjtemény** lehetőséget, ha új adatbázist és gyűjteményt szeretne hozzáadni a Cosmos db-fiókhoz.

    ![Új gyűjtemény Cosmos DB](./media/service-endpoint-cosmosdb/new-collection.png)

## <a name="upload-data-to-cosmos-db"></a>Adatok feltöltése a Cosmos DBba

1. Nyissa meg az [adatáttelepítési eszköz](https://aka.ms/csdmtool)grafikus felületének verzióját a Cosmos db, **Dtui. exe**fájlhoz.

    ![Cosmos DB adatmigrálási eszköz](./media/service-endpoint-cosmosdb/cosmos-data-migration-tool.png)

2. A **forrás adatai** lapon válassza ki a **CSV-fájl (oka)** **t az importálás** legördülő listából. Ezután válassza a **fájlok hozzáadása** lehetőséget, és adja hozzá az előfeltételként letöltött Storm-adatcsv-fájlt.

    ![Cosmos DB adatáttelepítési eszköz forrására vonatkozó információk](./media/service-endpoint-cosmosdb/cosmos-source-information.png)

3. A **cél információi** lapon adja meg a kapcsolatok karakterláncát. A kapcsolatok karakterláncának formátuma: `AccountEndpoint=<URL>;AccountKey=<key>;Database=<database>`. A AccountEndpoint és a AccountKey az előző szakaszban mentett elsődleges kapcsolatok karakterláncában szerepel. Fűzze `Database=<your database name>` hozzá a kapcsolódási karakterlánc végéhez, majd kattintson az **ellenőrzés**gombra. Ezután adja hozzá a gyűjtemény nevét és a partíciós kulcsot.

    ![A Cosmos DB adatáttelepítési eszköz céljának adatai](./media/service-endpoint-cosmosdb/cosmos-target-information.png)

4. Kattintson a **tovább** gombra, amíg el nem jut az Összefoglalás lapra. Ezután válassza az **Importálás**lehetőséget.

## <a name="create-a-cluster-and-add-library"></a>Fürt létrehozása és könyvtár hozzáadása

1. Navigáljon a Azure Databricks szolgáltatáshoz a [Azure Portal](https://portal.azure.com) , és válassza a **munkaterület elindítása**lehetőséget.

   ![Databricks-munkaterület elindítása](./media/service-endpoint-cosmosdb/launch-workspace.png)

2. Hozzon létre egy új fürtöt. Válassza ki a fürt nevét, és fogadja el a többi alapértelmezett beállítást.

   ![Új fürtkonfiguráció](./media/service-endpoint-cosmosdb/create-cluster.png)

3. Miután létrehozta a fürtöt, navigáljon a fürt lapra, és válassza a **könyvtárak** fület. Válassza az **új telepítése** elemet, és töltse fel a Spark Connector jar-fájlt a könyvtár telepítéséhez.

    ![A Spark Connector könyvtárának telepítése](./media/service-endpoint-cosmosdb/install-cosmos-connector-library.png)

    A **kódtárak** lapon ellenőrizheti, hogy telepítve van-e a könyvtár.

    ![Databricks-fürt kódtárai lap](./media/service-endpoint-cosmosdb/installed-library.png)

## <a name="query-cosmos-db-from-a-databricks-notebook"></a>Cosmos DB lekérdezése Databricks-jegyzetfüzetből

1. Navigáljon a Azure Databricks munkaterületre, és hozzon létre egy új Python-jegyzetfüzetet.

    ![Új Databricks-jegyzetfüzet létrehozása](./media/service-endpoint-cosmosdb/new-python-notebook.png)

2. Futtassa a következő Python-kódot a Cosmos DB kapcsolódási konfigurációjának beállításához. Ennek megfelelően módosítsa a **végpontot**, a **MasterKey**, az **adatbázist**és a **gyűjteményt** .

    ```python
    connectionConfig = {
      "Endpoint" : "https://<your Cosmos DB account name.documents.azure.com:443/",
      "Masterkey" : "<your Cosmos DB primary key>",
      "Database" : "<your database name>",
      "preferredRegions" : "West US 2",
      "Collection": "<your collection name>",
      "schema_samplesize" : "1000",
      "query_pagesize" : "200000",
      "query_custom" : "SELECT * FROM c"
    }
    ```

3. A következő Python-kód használatával töltse be az adatmennyiséget, és hozzon létre egy ideiglenes nézetet.

    ```python
    users = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**connectionConfig).load()
    users.createOrReplaceTempView("storm")
    ```

4. A következő Magic parancs használatával hajthat végre egy olyan SQL-utasítást, amely az adatok visszaadása.

    ```python
    %sql
    select * from storm
    ```

    Sikeresen csatlakoztatta a VNet-injektált Databricks-munkaterületet egy szolgáltatás-végponttal rendelkező Cosmos DB erőforráshoz. Ha többet szeretne megtudni a Cosmos DBhoz való csatlakozásról, tekintse meg [a Apache Spark Azure Cosmos db-összekötőt](https://github.com/Azure/azure-cosmosdb-spark).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a Azure Databricks munkaterületet és az összes kapcsolódó erőforrást. A feladatok törlése elkerüli a szükségtelen számlázást. Ha a jövőben a Azure Databricks munkaterület használatát tervezi, akkor leállíthatja a fürtöt, és később újraindíthatja. Ha nem kívánja tovább használni ezt a Azure Databricks munkaterületet, az oktatóanyagban létrehozott összes erőforrást az alábbi lépések segítségével törölheti:

1. A Azure Portal bal oldali menüjében kattintson az **erőforráscsoportok** elemre, majd kattintson a létrehozott erőforráscsoport nevére.

2. Az erőforráscsoport lapon válassza a **Törlés**lehetőséget, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban telepített egy Azure Databricks munkaterületet egy virtuális hálózatra, és használta a Cosmos DB Spark-összekötőt, hogy lekérdezze Cosmos DB adatait a Databricks. Ha többet szeretne megtudni a virtuális hálózat Azure Databricksinak használatáról, folytassa az oktatóanyagot, amely a SQL Server használatát mutatja be a Azure Databricks használatával.

> [!div class="nextstepaction"]
> [Oktatóanyag: SQL Server Linux Docker-tároló lekérdezése egy virtuális hálózaton egy Azure Databricks jegyzetfüzetből](vnet-injection-sql-server.md)
