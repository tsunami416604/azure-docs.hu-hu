---
title: Oktatóanyag – Valósítsa meg az Azure Databricks-t egy Cosmos DB-végpontdal
description: Ez az oktatóanyag ismerteti, hogyan valósítható meg az Azure Databricks egy virtuális hálózatban egy Service Endpoint engedélyezve van a Cosmos DB.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 04/17/2019
ms.openlocfilehash: 4ac8c01e986cf1f3158c615a0791ba476e5bf1bb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74706166"
---
# <a name="tutorial-implement-azure-databricks-with-a-cosmos-db-endpoint"></a>Oktatóanyag: Valósítsa meg az Azure Databricks egy Cosmos DB-végpont

Ez az oktatóanyag ismerteti, hogyan valósítható meg egy virtuális hálózat injektált Databricks-környezet ben a Cosmos DB szolgáltatásvégpont engedélyezve van.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Azure Databricks-munkaterület létrehozása virtuális hálózatban
> * Cosmos DB szolgáltatásvégpont létrehozása
> * Cosmos DB-fiók létrehozása és adatok importálása
> * Azure Databricks-fürt létrehozása
> * Cosmos DB lekérdezése egy Azure Databricks-jegyzetfüzetből

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, tegye a következőket:

* Hozzon létre egy [Azure Databricks munkaterületet egy virtuális hálózatban.](quickstart-create-databricks-workspace-vnet-injection.md)

* Töltse le a [Spark-összekötőt.](https://search.maven.org/remotecontent?filepath=com/microsoft/azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.4/azure-cosmosdb-spark_2.4.0_2.11-1.3.4-uber.jar)

* Töltse le a minta adatokat a [NOAA National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/). Jelöljön ki egy állapotot vagy területet, és válassza a **Keresés lehetőséget.** A következő lapon fogadja el az alapértelmezett értékeket, és válassza a **Keresés lehetőséget.** Ezután válassza **csv letöltés** a bal oldalon az oldalon, hogy töltse le az eredményeket.

* Töltse le az Azure Cosmos DB adatáttelepítési eszköz [előre lefordított bináris.](https://aka.ms/csdmtool)

## <a name="create-a-cosmos-db-service-endpoint"></a>Cosmos DB szolgáltatásvégpont létrehozása

1. Miután üzembe helyezett egy Azure Databricks-munkaterületet egy virtuális hálózatra, keresse meg a virtuális hálózatot az [Azure Portalon.](https://portal.azure.com) Figyelje meg a databricks-telepítésen keresztül létrehozott nyilvános és privát alhálózatokat.

   ![Virtuális hálózati alhálózatok](./media/service-endpoint-cosmosdb/virtual-network-subnets.png)

2. Válassza ki a *nyilvános alhálózatot,* és hozzon létre egy Cosmos DB szolgáltatásvégpontot. Ezután mentse a **fájlt.**
   
   ![Cosmos DB szolgáltatásvégpont hozzáadása](./media/service-endpoint-cosmosdb/add-cosmosdb-service-endpoint.png)

## <a name="create-a-cosmos-db-account"></a>Cosmos DB-fiók létrehozása

1. Nyissa meg az Azure Portalt. A képernyő bal felső részén válassza az **Erőforrás létrehozása > adatbázisok > az Azure Cosmos DB**lehetőséget.

2. Töltse ki a **Példány részletei** az **Alapok** lapon a következő beállításokkal:

   |Beállítás|Érték|
   |-------|-----|
   |Előfizetés|*az előfizetés*|
   |Erőforráscsoport|*erőforráscsoport*|
   |Fiók neve|db-vnet-szolgáltatás-végpont|
   |API|Core (SQL)|
   |Hely|USA nyugati régiója|
   |Georedundancia|Letiltás|
   |Többrégiós írók|Bekapcsolás|

   ![Cosmos DB szolgáltatásvégpont hozzáadása](./media/service-endpoint-cosmosdb/create-cosmosdb-account-basics.png)

3. Válassza a **Hálózat** lapot, és konfigurálja a virtuális hálózatot. 

   a. Válassza ki az előfeltételként létrehozott virtuális hálózatot, majd válassza a *nyilvános alhálózat lehetőséget.* Figyelje meg, hogy a *magán-alhálózat* rendelkezik a *"Microsoft AzureCosmosDB" végpont hiányzik"* megjegyzéssel. Ennek az az oka, hogy csak a Cosmos DB szolgáltatás végpontját engedélyezte a *nyilvános alhálózaton.*

   b. Győződjön meg arról, hogy engedélyezve van **a hozzáférés engedélyezése az Azure Portalról.** Ez a beállítás lehetővé teszi a Cosmos DB-fiók elérését az Azure Portalról. Ha ez a beállítás **Megtagadás**, hibaüzenetet kap, amikor megpróbál hozzáférni a fiókjához. 

   > [!NOTE]
   > Ez nem szükséges ez a bemutató, de akkor is engedélyezheti *a hozzáférést az én IP,* ha azt szeretné, hogy a cosmos DB fiók eléréséhez a helyi gép. Ha például a Cosmos DB SDK használatával csatlakozik a fiókjához, engedélyeznie kell ezt a beállítást. Ha le van tiltva, "Hozzáférés megtagadva" hibaüzenetek jelennek meg.

   ![Cosmos DB-fiók hálózati beállításai](./media/service-endpoint-cosmosdb/create-cosmosdb-account-network.png)

4. Válassza **a Véleményezés + Létrehozás**lehetőséget, majd a **Létrehozás** lehetőséget a Cosmos DB-fiók virtuális hálózaton belüli létrehozásához.

5. A Cosmos DB-fiók létrehozása után keresse meg a **Kulcsok lapot** a **Beállítások**területen. Másolja az elsődleges kapcsolati karakterláncot, és mentse szövegszerkesztőbe későbbi használatra.

    ![Cosmos DB-fiók kulcsok lap](./media/service-endpoint-cosmosdb/cosmos-keys.png)

6. Válassza **az Adatkezelő** és **az Új gyűjtemény** lehetőséget, ha új adatbázist és gyűjteményt szeretne hozzáadni a Cosmos DB-fiókjához.

    ![Cosmos DB új kollekció](./media/service-endpoint-cosmosdb/new-collection.png)

## <a name="upload-data-to-cosmos-db"></a>Adatok feltöltése a Cosmos DB-ba

1. Nyissa meg a Cosmos DB , **Dtui.exe** [adatáttelepítési eszközének](https://aka.ms/csdmtool)grafikus felületverzióját.

    ![Cosmos DB adatmigrálási eszköz](./media/service-endpoint-cosmosdb/cosmos-data-migration-tool.png)

2. A **Forrásinformáció** lapon válassza a **CSV-fájl(ok)** lehetőséget az **Importálás legördülő menüből.** Ezután válassza **a Fájlok hozzáadása** lehetőséget, és adja hozzá a letöltött viharadatokat.

    ![Cosmos DB adatáttelepítési eszköz forrásinformációi](./media/service-endpoint-cosmosdb/cosmos-source-information.png)

3. A **Célinformáció** lapon adja meg a kapcsolati karakterláncot. A kapcsolati `AccountEndpoint=<URL>;AccountKey=<key>;Database=<database>`karakterlánc formátuma . Az AccountEndpoint és a AccountKey az előző szakaszban mentett elsődleges kapcsolati karakterlánc részét képezi. Fűzz `Database=<your database name>` hozzá a kapcsolati karakterlánc végéhez, és válassza az **Ellenőrzés gombot.** Ezután adja hozzá a gyűjtemény nevét és partíciókulcsát.

    ![Cosmos DB adatáttelepítési eszköz céladatai](./media/service-endpoint-cosmosdb/cosmos-target-information.png)

4. Válassza a **Tovább gombot,** amíg el nem jut az Összegzés lapra. Ezután válassza az **Importálás**lehetőséget.

## <a name="create-a-cluster-and-add-library"></a>Fürt létrehozása és tár hozzáadása

1. Nyissa meg az Azure Databricks-szolgáltatást az [Azure Portalon,](https://portal.azure.com) és válassza **a Munkaterület indítása**lehetőséget.

   ![A Databricks munkaterületének elindítása](./media/service-endpoint-cosmosdb/launch-workspace.png)

2. Hozzon létre egy új fürtöt. Válassza ki a fürt nevét, és fogadja el a fennmaradó alapértelmezett beállításokat.

   ![Új fürtbeállítások](./media/service-endpoint-cosmosdb/create-cluster.png)

3. A fürt létrehozása után keresse meg a fürtlapot, **Install New** és válassza a **Könyvtárak** lapot.

    ![Spark-összekötő tár telepítése](./media/service-endpoint-cosmosdb/install-cosmos-connector-library.png)

    Ellenőrizheti, hogy a tár telepítve **van-e** a Könyvtárak lapon.

    ![Databricks fürtkönyvtárak lap](./media/service-endpoint-cosmosdb/installed-library.png)

## <a name="query-cosmos-db-from-a-databricks-notebook"></a>Cosmos DB lekérdezése Databricks-jegyzetfüzetből

1. Keresse meg az Azure Databricks-munkaterületet, és hozzon létre egy új python-jegyzetfüzetet.

    ![Új Databricks-jegyzetfüzet létrehozása](./media/service-endpoint-cosmosdb/new-python-notebook.png)

2. Futtassa a következő python-kódot a Cosmos DB kapcsolat konfigurációjának beállításához. Ennek megfelelően módosítsa a **végpontot**, **a Főkulcsot**, **az Adatbázist**és **a Gyűjtést.**

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

3. A következő python-kód használatával töltse be az adatokat, és hozzon létre egy ideiglenes nézetet.

    ```python
    users = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**connectionConfig).load()
    users.createOrReplaceTempView("storm")
    ```

4. A következő magic parancsot használja az adatokat visszaadó SQL utasítás végrehajtásához.

    ```python
    %sql
    select * from storm
    ```

    Sikeresen összekapcsolta a Virtuálishálózat által injektált Databricks-munkaterületet egy szolgáltatás-végpontpal engedélyezett Cosmos DB-erőforrással. Ha többet szeretne megtudni aCosmos DB-hez való csatlakozásról, olvassa el az Azure Cosmos DB Connector for Apache Spark című [témakört.](https://github.com/Azure/azure-cosmosdb-spark)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, törölje az erőforráscsoportot, az Azure Databricks munkaterületet és az összes kapcsolódó erőforrást. A feladat törlése szükségtelen számlázást. Ha azt tervezi, hogy az Azure Databricks munkaterületet a jövőben, leállíthatja a fürtöt, és később újraindíthatja. Ha nem fogja tovább használni ezt az Azure Databricks-munkaterületet, törölje az oktatóanyagban létrehozott összes erőforrást az alábbi lépésekkel:

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** elemre, majd a létrehozott erőforráscsoport nevére.

2. Az erőforráscsoport lapján válassza a **Törlés**lehetőséget, írja be a törlendő erőforrás nevét a szövegmezőbe, majd kattintson ismét a **Törlés** gombra.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure Databricks-munkaterületet telepített egy virtuális hálózatra, és a Cosmos DB Spark-összekötő használatával lekérdezi a Cosmos DB-adatokat a Databricks-ből. Ha többet szeretne megtudni az Azure Databricks virtuális hálózatban való használatáról, folytassa az SQL Server Azure Databricks használatával kapcsolatos oktatóanyaggal.

> [!div class="nextstepaction"]
> [Oktatóanyag: SQL Server Linux Docker-tároló lekérdezése egy virtuális hálózatban egy Azure Databricks-jegyzetfüzetből](vnet-injection-sql-server.md)
