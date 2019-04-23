---
title: Megvalósítása az Azure Databricks egy Cosmos DB-végponttal
description: Ez az oktatóanyag leírja, hogyan valósíthatók meg az Azure Databricks egy Cosmos DB-hez készült engedélyezett végpontot a virtuális hálózatban.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.topic: tutorial
ms.date: 04/17/2019
ms.openlocfilehash: 0d5442a63680227f3a6186330502666c92dc3129
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013166"
---
# <a name="tutorial-implement-azure-databricks-with-a-cosmos-db-endpoint"></a>Oktatóanyag: Megvalósítása az Azure Databricks egy Cosmos DB-végponttal

Ez az oktatóanyag leírja, hogyan valósíthat meg egy Cosmos DB-hez készült engedélyezett szolgáltatásvégponttal injektált Databricks környezet VNet.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Azure Databricks-munkaterület létrehozása egy virtuális hálózaton belül
> * Hozzon létre egy Cosmos DB-szolgáltatásvégpont
> * Hozzon létre egy Cosmos DB-fiókot és az adatok importálása
> * Az Azure Databricks-fürt létrehozása
> * Az Azure Databricks-jegyzetfüzetnek a Cosmos DB lekérdezése

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, tegye a következőket:

* Hozzon létre egy [egy virtuális hálózatot az Azure Databricks-munkaterület](quickstart-create-databricks-workspace-vnet-injection.md).

* Töltse le a [Spark-összekötő](https://search.maven.org/remotecontent?filepath=com/microsoft/azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.4/azure-cosmosdb-spark_2.4.0_2.11-1.3.4-uber.jar).

* Töltse le a mintaadatokat is abból az [NOAA National központok környezeti információkat](https://www.ncdc.noaa.gov/stormevents/). Állam vagy területen válassza ki és **keresési**. A következő oldalon fogadja el az alapértelmezett beállításokat, és válassza ki **keresési**. Válassza ki **CSV letöltése** töltse le az eredményeket a lap bal oldalán.

* Töltse le a [előre lefordított bináris](https://aka.ms/csdmtool) , az Azure Cosmos DB adatáttelepítés eszközzel.

## <a name="create-a-cosmos-db-service-endpoint"></a>Hozzon létre egy Cosmos DB-szolgáltatásvégpont

1. A virtuális hálózatot a virtuális hálózat és egy üzembe helyezett Azure Databricks-munkaterület, keresse meg a [az Azure portal](https://portal.azure.com). Figyelje meg, hogy a nyilvános és titkos alhálózatokat, hogy a Databricks üzembe helyezési lettek létrehozva.

   ![Virtuális hálózat alhálózataiban](./media/service-endpoint-cosmosdb/virtual-network-subnets.png)

2. Válassza ki a *nyilvános alhálózattal* , és hozzon létre egy Cosmos DB végpontot. Ezután **mentése**.
   
   ![A Cosmos DB-szolgáltatásvégpont hozzáadása](./media/service-endpoint-cosmosdb/add-cosmosdb-service-endpoint.png)

## <a name="create-a-cosmos-db-account"></a>Cosmos DB-fiók létrehozása

1. Nyissa meg az Azure Portalt. A képernyő bal felső oldalán válassza **erőforrás létrehozása > adatbázisok > Azure Cosmos DB**.

2. Töltse ki a **példány részletei** a a **alapjai** lapon a következő beállításokkal:

   |Beállítás|Value|
   |-------|-----|
   |Előfizetés|*az Ön előfizetése*|
   |Erőforráscsoport|*Az erőforráscsoport*|
   |Fiók neve|db-vnet-service-endpoint|
   |API|Mag (SQL)|
   |Location egység|USA nyugati régiója|
   |Georedundancia|Letiltás|
   |Többrégiós írások|Bekapcsolás|

   ![A Cosmos DB-szolgáltatásvégpont hozzáadása](./media/service-endpoint-cosmosdb/create-cosmosdb-account-basics.png)

3. Válassza ki a **hálózati** fülre, és konfigurálja a virtuális hálózat. 

   a. Válassza ki az előfeltételként létrehozott virtuális hálózatot, és válassza ki *nyilvános alhálózattal*. Figyelje meg, hogy *privát-alhálózat* rendelkezik a Megjegyzés *hiányzik a "Microsoft AzureCosmosDB" végpont "*. Ennek oka, hogy az csak engedélyezett a Cosmos DB-szolgáltatásvégpontot az *nyilvános alhálózattal*.

   b. Ellenőrizze, hogy **engedélyezze a hozzáférést az Azure portal** engedélyezve van. Ez a beállítás lehetővé teszi a Cosmos DB-fiókja elérését az Azure Portalról. Ha ez a beállítás értéke **Megtagadás**, hibákat fog kapni, amikor megpróbálja elérni a fiókját. 

   > [!NOTE]
   > Nem szükséges ehhez az oktatóanyaghoz, de is engedélyezheti *engedélyezi a hozzáférést a saját IP-cím* Ha azt szeretné, hogy a hozzáférést a Cosmos DB-fiókot a helyi gépen. Például ha a fiókjához, a Cosmos DB SDK-val csatlakozik, akkor engedélyeznie kell ezt a beállítást. Ha le van tiltva, a "Hozzáférés megtagadva" hiba fog kapni.

   ![Cosmos DB-fiók hálózati beállítások](./media/service-endpoint-cosmosdb/create-cosmosdb-account-network.png)

4. Válassza ki **felülvizsgálat + létrehozás**, majd **létrehozás** hozhat létre a Cosmos DB-fiókot, a virtuális hálózaton belül.

5. A Cosmos DB-fiók létrehozása után lépjen **kulcsok** alatt **beállítások**. Másolja az elsődleges kapcsolati karakterláncot, és mentse későbbi használatra egy szövegszerkesztőben.

    ![A cosmos DB-fiók kulcsok oldalán](./media/service-endpoint-cosmosdb/cosmos-keys.png)

6. Válassza ki **adatkezelő** és **új gyűjtemény** hozzáadása a Cosmos DB-fiók egy új adatbázist és gyűjteményt.

    ![A cosmos DB új gyűjtemény](./media/service-endpoint-cosmosdb/new-collection.png)

## <a name="upload-data-to-cosmos-db"></a>Adatok feltöltése a Cosmos DB-hez

1. Nyissa meg a grafikus felület verziója a [adatáttelepítési eszköz Cosmos DB-hez készült](https://aka.ms/csdmtool), **Dtui.exe**.

    ![A cosmos DB az adatok áttelepítési eszköz](./media/service-endpoint-cosmosdb/cosmos-data-migration-tool.png)

2. Az a **adatforrás adatai** lapon jelölje be **CSV-fájl** a a **importálhat** legördülő listából. Válassza ki **fájlok hozzáadása** , és adja hozzá a storm-adatok a letöltött CSV előfeltételként.

    ![Cosmos DB adatáttelepítési eszköz adatforrás adatai](./media/service-endpoint-cosmosdb/cosmos-source-information.png)

3. Az a **cél információk** fülre, megadhatja a kapcsolati karakterláncot. A kapcsolati karakterlánc formátuma `AccountEndpoint=<URL>;AccountKey=<key>;Database=<database>`. A megadni és AccountKey az elsődleges kapcsolati karakterlánc az előző szakaszban mentett szerepelnek. Hozzáfűzés `Database=<your database name>` a kapcsolati karakterláncot, és válassza a végéhez **ellenőrizze**. Adja hozzá a gyűjtemény nevét és a partíciós kulcs.

    ![Cosmos DB adatáttelepítési eszköz célként megadott információk](./media/service-endpoint-cosmosdb/cosmos-target-information.png)

4. Válassza ki **tovább** mindaddig, amíg az összefoglalás lapon jelenik meg. Ezután válassza ki **importálás**.

## <a name="create-a-cluster-and-add-library"></a>Hozzon létre egy fürtöt, és a könyvtár hozzáadása

1. Azure Databricks szolgáltatásban nyissa meg a [az Azure portal](https://portal.azure.com) válassza **munkaterület indítása**.

   ![Indítsa el a Databricks-munkaterület](./media/service-endpoint-cosmosdb/launch-workspace.png)

2. Hozzon létre egy új fürtöt. Válassza ki a fürt nevét, és fogadja el a többi alapértelmezett beállítást.

   ![Új fürtbeállítások](./media/service-endpoint-cosmosdb/create-cluster.png)

3. A fürt létrehozása után nyissa meg a fürt lapot, és válassza ki a **kódtárak** fülre. Válassza ki **telepítése új** , és töltse fel a Spark összekötő jar-fájlját a tár telepítéséhez.

    ![Telepítse a Spark-összekötő könyvtár](./media/service-endpoint-cosmosdb/install-cosmos-connector-library.png)

    Ellenőrizheti, hogy a szalagtár telepítve lett a **kódtárak** fülre.

    ![Databricks-fürt tárak lap](./media/service-endpoint-cosmosdb/installed-library.png)

## <a name="query-cosmos-db-from-a-databricks-notebook"></a>Cosmos DB lekérdezése a Databricks-jegyzetfüzet

1. Keresse meg az Azure Databricks-munkaterület, és hozzon létre egy új python-jegyzetfüzetet.

    ![Databricks-jegyzetfüzet létrehozása](./media/service-endpoint-cosmosdb/new-python-notebook.png)

2. Futtassa a következő python-kód beállítása a Cosmos DB-kapcsolat konfigurálása. Módosítsa a **végpont**, **Masterkey**, **adatbázis**, és **gyűjtemény** ennek megfelelően.

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

3. A következő python-kód használatával az adatok betöltéséhez és a egy ideiglenes nézet létrehozása.

    ```python
    users = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**connectionConfig).load()
    users.createOrReplaceTempView("storm")
    ```

4. A következő varázsparancs segítségével hajtható végre egy SQL-utasítás által visszaadott adatokat.

    ```python
    %sql
    select * from storm
    ```

    Sikeresen csatlakoztatta a VNet-beszúrta Databricks-munkaterület-szolgáltatásvégpontot engedélyezett Cosmos DB erőforrás. Olvassa el a Cosmos dB-t kapcsolódás kapcsolatos információkért tekintse meg [Azure Cosmos DB-összekötő az Apache Spark](https://github.com/Azure/azure-cosmosdb-spark).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, az Azure Databricks-munkaterület és minden kapcsolódó erőforrás. A feladat törlése elkerülhető a felesleges számlázás. Ha a jövőben használni az Azure Databricks-munkaterület, állítsa le a fürtöt, és indítsa újra később. Ha nem kívánja tovább használhatja az Azure Databricks-munkaterület, törölje az ebben az oktatóanyagban az alábbi lépésekkel létrehozott összes erőforrást:

1. Az Azure Portal baloldali menüjében kattintson **erőforráscsoportok** és kattintson a létrehozott erőforráscsoport nevét.

2. Az erőforráscsoport lapján, válassza ki a **törlése**, adja meg az erőforrás törlése a szövegmezőbe, és válassza ki a **törlése** újra.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban, már üzembe helyezett Azure Databricks-munkaterület egy virtuális hálózathoz, és a Cosmos DB Spark-összekötő segítségével lekérdezés Cosmos DB-adatai databricksből. További információ az Azure Databricks egy virtuális hálózaton működik, folytassa az oktatóanyag az SQL Server az Azure Databricks használatával.

> [!div class="nextstepaction"]
> [Oktatóanyag: A lekérdezés egy SQL Server Linux Docker-tárolót egy virtuális hálózaton lévő az Azure Databricks-jegyzetfüzet](vnet-injection-sql-server.md)