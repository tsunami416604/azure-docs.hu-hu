---
title: 'Oktatóanyag: Az Azure Databricks segítségével ETL-műveletek végrehajtása'
description: 'Útmutató: adatok kinyerése a Data Lake Storage Gen2 az Azure Databricksbe, átalakítja az adatokat, és majd betölteni az adatokat az Azure SQL Data Warehouse-bA.'
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.workload: Active
ms.date: 02/15/2019
ms.openlocfilehash: 6ec32a40cea4f95d9225134cfb36d4930245d1c5
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56750599"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Oktatóanyag: A kinyerési, átalakítási és az adatok betöltése az Azure Databricks használatával

Ebben az oktatóanyagban egy ETL (kinyerés, átalakítás és adatok betöltése) hajt végre műveletet az Azure Databricks használatával. Adatok kinyerése az Azure Data Lake Storage Gen2 az Azure Databricksbe, átalakításokat futtat az adatok az Azure Databricksben, és majd betölti az átalakított adatokat az Azure SQL Data Warehouse-bA.

A jelen oktatóanyagban szereplő lépések az Azure Databricks SQL Data Warehouse-összekötőjét használják az adatok Azure Databricksbe való átviteléhez. Az összekötő ezután az Azure Blob Storage-ot használja ideiglenes tárolóként az Azure Databricks-fürt és az Azure SQL Data Warehouse között átvitt adatokhoz.

Az alábbi ábrán az alkalmazásfolyam látható:

![Az Azure Databricks a Data Lake Store-ral és az SQL Data Warehouse-szal](./media/databricks-extract-load-sql-data-warehouse/databricks-extract-transform-load-sql-datawarehouse.png "Az Azure Databricks a Data Lake Store-ral és az SQL Data Warehouse-szal")

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Hozzon létre egy Azure Databricks szolgáltatást.
> * Spark-fürt létrehozása az Azure Databricksben.
> * A Data Lake Storage Gen2-fiókban létrehozni egy fájlrendszert.
> * Mintaadatok feltöltése az Azure Data Lake Storage Gen2-fiókjába.
> * Hozzon létre egy egyszerű szolgáltatást.
> * Adatok kinyerése az Azure Data Lake Storage Gen2-fiók.
> * Adatátalakítás az Azure Databricksben.
> * Adatok betöltése az Azure SQL Data Warehouse-bA.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre ezeket a feladatokat, ez az oktatóanyag megkezdése előtt:

* Hozzon létre egy Azure SQL data warehouse, hozzon létre egy kiszolgálószintű tűzfalszabályt, és csatlakozzon a kiszolgálóhoz kiszolgáló-rendszergazdaként Lásd: [a rövid útmutató: Hozzon létre egy Azure SQL data warehouse](../sql-data-warehouse/create-data-warehouse-portal.md).

* Hozzon létre egy fő adatbáziskulcsot az Azure SQL data warehouse-hoz. Lásd: [hozzon létre egy fő adatbáziskulcsot](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).

* Hozzon létre egy Azure Blob Storage-fiókot, benne egy tárolóval. A hozzáférési kulcsot is kérje le a tárfiók eléréséhez. Lásd: [a rövid útmutató: Hozzon létre egy Azure Blob storage-fiók](../storage/blobs/storage-quickstart-blobs-portal.md).

* Hozzon létre egy Azure Data Lake Storage Gen2 tárfiókot. Lásd: [hozzon létre egy Azure Data Lake Storage Gen2 fiókot](../storage/blobs/data-lake-storage-quickstart-create-account.md).

*  Hozzon létre egy egyszerű szolgáltatást. Lásd: [hogyan: A portál használatával hozzon létre egy Azure AD alkalmazás és -szolgáltatásnév erőforrások eléréséhez](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

   Van néhány adott tudnivaló, mivel ebben a cikkben hajtsa végre a lépéseket kell.

   * A lépések végrehajtásakor a [alkalmazások szerepkörhöz rendeléséhez](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) szakaszt a cikk, ügyeljen arra, hogy rendelje hozzá a **Storage-Blobadatok Közreműködője** szerepkört a szolgáltatásnévhez.

     > [!IMPORTANT]
     > Ellenőrizze, hogy a szerepkört a Data Lake Storage Gen2 storage-fiók hatókörében. Szerepkör hozzárendelése a szülő erőforráscsoportba vagy előfizetésbe, de kap engedélyekkel kapcsolatos hibákat addig, amíg azokat a szerepkör-hozzárendeléseket a tárfiókhoz való propagálása.

   * A lépések végrehajtásakor a [értékek beolvasása bejelentkezés](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) szakaszában a cikk, illessze be a bérlő Azonosítóját, Alkalmazásazonosító és hitelesítési kulcs értékeit egy szövegfájlba. Kell azokat, hamarosan.

* Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="gather-the-information-that-you-need"></a>Az információk összegyűjtése

Győződjön meg arról, hogy elvégezte-e az előfeltételeket a jelen oktatóanyag.

   Mielőtt hozzákezd, rendelkeznie kell ezeket az elemeket az információkat:

   :heavy_check_mark:  Az adatbázis nevét, adatbázis-kiszolgáló nevét, felhasználónév és jelszó az Azure SQL Data warehouse.

   :heavy_check_mark:  A blob storage-fiók hozzáférési kulcsára.

   :heavy_check_mark:  A Data Lake Storage Gen2 tárfiók neve.

   :heavy_check_mark:  Az előfizetés Bérlőazonosítója.

   :heavy_check_mark:  A regisztrált alkalmazást, az Azure Active Directoryval (Azure AD-) alkalmazás azonosítója.

   :heavy_check_mark:  A hitelesítési kulcsot az Azure AD-vel regisztrált alkalmazás.

## <a name="create-an-azure-databricks-service"></a>Hozzon létre egy Azure Databricks szolgáltatás

Ebben a szakaszban létrehoz egy Azure Databricks szolgáltatást az Azure portal használatával.

1. Az Azure Portalon válassza az **Erőforrás létrehozása** > **Elemzés** > **Azure Databricks** elemet.

    ![Databricks az Azure Portalon](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-on-portal.png "Databricks az Azure Portalon")

2. A **Azure Databricks szolgáltatás**, adja meg a Databricks-szolgáltatás létrehozása a következő értékeket:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |**Munkaterület neve**     | Adja meg a Databricks-munkaterület nevét.        |
    |**Előfizetés**     | Válassza ki a legördülő menüből a saját Azure-előfizetését.        |
    |**Erőforráscsoport**     | Adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne használni. Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. További információért olvassa el az [Azure-erőforráscsoportok áttekintését](../azure-resource-manager/resource-group-overview.md). |
    |**Hely**     | Válassza az **USA 2. nyugati régióját**.  A további elérhető régiókért tekintse meg az [elérhető Azure-szolgáltatások régiók szerinti bontását](https://azure.microsoft.com/regions/services/).      |
    |**Tarifacsomag**     |  Válassza ki **Standard**.     |

3. Válassza a **Rögzítés az irányítópulton**, majd a **Létrehozás** lehetőséget.

4. A fiók létrehozása eltarthat néhány percig. Fiók létrehozása során a portál megjeleníti a **üzemelő példány elküldése az Azure Databricks** csempére a jobb oldalon. Műveleti állapotának figyelése, megtekintheti a folyamatjelző sáv tetején.

    ![Databricks üzembe helyezési csempe](./media/databricks-extract-load-sql-data-warehouse/databricks-deployment-tile.png "Databricks üzembe helyezési csempe")

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Spark-fürt létrehozása az Azure Databricksben

1. Az Azure Portalon nyissa meg a létrehozott Databricks-szolgáltatás, és válassza ki **munkaterület indítása**.

2. A program átirányítja az Azure Databricks portáljára. A portálon válassza a **Fürt** elemet.

    ![Databricks az Azure-on](./media/databricks-extract-load-sql-data-warehouse/databricks-on-azure.png "Databricks az Azure-on")

3. Az **Új fürt** lapon adja meg a fürt létrehozásához szükséges értékeket.

    ![Databricks Spark-fürt létrehozása az Azure-on](./media/databricks-extract-load-sql-data-warehouse/create-databricks-spark-cluster.png "Databricks Spark-fürt létrehozása az Azure-on")

4. Adjon meg értékeket a következő mezőkben, és fogadja el az alapértelmezett értékeket a többi mezőben:

    * Adjon egy nevet a fürtnek.

    * Ebben a cikkben hozzon létre egy fürtöt a **5.1** modul.

    * Mindenképpen jelölje ki a **leállítása után \_ \_ ennyi perc inaktivitás** jelölőnégyzetet. Ha a fürt nem használ, adja meg az időtartamot (percben) a fürt leállításához.

    * Válassza a **Fürt létrehozása** lehetőséget. Miután a fürt fut, notebookokat csatlakoztathat hozzá, a fürt, és a Spark-feladatok futtatása.

## <a name="create-a-file-system-in-the-azure-data-lake-storage-gen2-account"></a>Operációs rendszer létrehozása az Azure Data Lake Storage Gen2-fiókban

Ebben a szakaszban létrehoz egy jegyzetfüzetet az Azure Databricks-munkaterületet, és majd a tárfiók konfigurálása a kódrészleteket futtat

1. Az a [az Azure portal](https://portal.azure.com), keresse meg az Ön által létrehozott Azure Databricks szolgáltatást, és válassza ki **munkaterület indítása**.

2. A bal oldalon válassza ki a **munkaterület**. A **Munkaterület** legördülő menüből válassza a **Létrehozás** > **Jegyzetfüzet** lehetőséget.

    ![Hozzon létre egy notebookot a Databricksben](./media/databricks-extract-load-sql-data-warehouse/databricks-create-notebook.png "notebook létrehozása a Databricksben")

3. A **Jegyzetfüzet létrehozása** párbeszédpanelen adja meg a jegyzetfüzet nevét. Válassza a **Scala** nyelvet, majd válassza ki a korábban létrehozott Spark-fürtöt.

    ![Adja meg adatait egy jegyzetfüzetet a Databricksben](./media/databricks-extract-load-sql-data-warehouse/databricks-notebook-details.png "adja meg adatait egy jegyzetfüzetet a Databricksben")

4. Kattintson a **Létrehozás** gombra.

5. Másolja és illessze be az alábbi kódblokkot az első olyan cellára.

   ```scala
   spark.conf.set("fs.azure.account.auth.type.<storage-account-name>.dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type.<storage-account-name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id.<storage-account-name>.dfs.core.windows.net", "<application-id>")
   spark.conf.set("fs.azure.account.oauth2.client.secret.<storage-account-name>.dfs.core.windows.net", "<authentication-key>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint.<storage-account-name>.dfs.core.windows.net", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   dbutils.fs.ls("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")
   ```

6. A kódblokk, cserélje le a `application-id`, `authentication-id`, `tenant-id`, és `storage-account-name` helyőrző értékeket az előfeltételeket a jelen oktatóanyag végrehajtása során gyűjtött értékek a kód blokk. Cserélje le a `file-system-name` nevét bármilyen, a helyőrző értékét szeretné adni a fájlrendszer.

   * A `application-id`, és `authentication-id` a regisztrált alkalmazást, és az active directory egyszerű szolgáltatás létrehozása során a rendszer.

   * A `tenant-id` az előfizetésből van.

   * A `storage-account-name` az Azure Data Lake Storage Gen2 storage-fiók neve.

7. Nyomja le az **SHIFT + ENTER** kulcsok a kód futtatásához a blokk.

## <a name="ingest-sample-data-into-the-azure-data-lake-storage-gen2-account"></a>Mintaadatok betöltése az Azure Data Lake Storage Gen2-fiókba

Mielőtt ehhez a szakaszhoz hozzáfogna, a következő előfeltételeknek kell eleget tennie:

Írja be az alábbi kódot egy jegyzetfüzetcellába:

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

A cellába, nyomja le az ENTER **SHIFT + ENTER** a kód futtatásához.

Most már alatti Ez egy új cellába, adja meg a következő kódot, és cserélje le az értékeket, amelyeket korábban használt ugyanazon értékekkel zárójelben jelennek meg:

    dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://<file-system>@<account-name>.dfs.core.windows.net/")

A cellába, nyomja le az ENTER **SHIFT + ENTER** a kód futtatásához.

## <a name="extract-data-from-the-azure-data-lake-storage-gen2-account"></a>Adatok kinyerése az Azure Data Lake Storage Gen2-fiók

1. Az Azure Databricksben adatok keretként most már betöltheti a json-mintafájlt. Illessze be a következő kódot egy új cellára. Cserélje le a zárójelben látható zárójelben a értékeire.

   ```scala
   val df = spark.read.json("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/small_radio_json.json")
   ```

   * Cserélje le a `file-system-name` helyőrző értéket cserélje a neve, hogy a fájlrendszer a Storage Explorerben.

   * Cserélje le a `storage-account-name` helyőrzőt a tárfiók nevére.

2. Nyomja le az **SHIFT + ENTER** kulcsok a kód futtatásához a blokk.

3. Futtassa a következő kódot az adathalmaz tartalmának megtekintéséhez:

    ```scala
    df.show()
    ```
   Az alábbi kódrészlethez hasonló kimenet jelenik meg:

   ```bash
   +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
   |               artist|     auth|firstName|gender|itemInSession|  lastName|   length|  level|            location|method|    page| registration|sessionId|                song|status|           ts|userId|
   +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
   | El Arrebato         |Logged In| Annalyse|     F|            2|Montgomery|234.57914| free  |  Killeen-Temple, TX|   PUT|NextSong|1384448062332|     1879|Quiero Quererte Q...|   200|1409318650332|   309|
   | Creedence Clearwa...|Logged In|   Dylann|     M|            9|    Thomas|340.87138| paid  |       Anchorage, AK|   PUT|NextSong|1400723739332|       10|        Born To Move|   200|1409318653332|    11|
   | Gorillaz            |Logged In|     Liam|     M|           11|     Watts|246.17751| paid  |New York-Newark-J...|   PUT|NextSong|1406279422332|     2047|                DARE|   200|1409318685332|   201|
   ...
   ...
   ```

   Ezzel kinyerte az adatokat a 2. generációs Azure Data Lake Storage-ből az Azure Databricksbe.

## <a name="transform-data-in-azure-databricks"></a>Adatok átalakítása az Azure Databricksben

A nyers mintaadatok **small_radio_json.json** fájl rádióállomás hallgatóit rögzíti, és a egy számos oszloppal rendelkezik. Ebben a szakaszban átalakítja az adatokat, hogy csak bizonyos oszlopokat kérjen le az adatkészletből.

1. Először a csak az oszlopok lekéréséhez **firstName**, **lastName**, **nemek**, **hely**, és **szint**létrehozott adathalmazból.

   ```scala
   val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
   specificColumnsDf.show()
   ```

   Az alábbi kódrészletben látható módon kimenetet kapja:

   ```bash
   +---------+----------+------+--------------------+-----+
   |firstname|  lastname|gender|            location|level|
   +---------+----------+------+--------------------+-----+
   | Annalyse|Montgomery|     F|  Killeen-Temple, TX| free|
   |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
   |     Liam|     Watts|     M|New York-Newark-J...| paid|
   |     Tess|  Townsend|     F|Nashville-Davidso...| free|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |Gabriella|   Shelton|     F|San Jose-Sunnyval...| free|
   |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   |     Tess|  Townsend|     F|Nashville-Davidso...| free|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |     Liam|     Watts|     M|New York-Newark-J...| paid|
   |     Liam|     Watts|     M|New York-Newark-J...| paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   +---------+----------+------+--------------------+-----+
   ```

2. Az adatok további átalakításához nevezze át a **level** oszlopot a következőre: **subscription_type**.

   ```scala
   val renamedColumnsDF = specificColumnsDf.withColumnRenamed("level", "subscription_type")
   renamedColumnsDF.show()
   ```

   Kimeneti kap az alábbi kódrészletben látható módon.

   ```bash
   +---------+----------+------+--------------------+-----------------+
   |firstname|  lastname|gender|            location|subscription_type|
   +---------+----------+------+--------------------+-----------------+
   | Annalyse|Montgomery|     F|  Killeen-Temple, TX|             free|
   |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
   |     Liam|     Watts|     M|New York-Newark-J...|             paid|
   |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |Gabriella|   Shelton|     F|San Jose-Sunnyval...|             free|
   |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |     Liam|     Watts|     M|New York-Newark-J...|             paid|
   |     Liam|     Watts|     M|New York-Newark-J...|             paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   +---------+----------+------+--------------------+-----------------+
   ```

## <a name="load-data-into-azure-sql-data-warehouse"></a>Adatok betöltése az Azure SQL Data Warehouse-ba

Ebben a szakaszban feltölti az átalakított adatokat az Azure SQL Data Warehouse-ba. Az Azure Databricks Azure SQL Data Warehouse-összekötő segítségével közvetlenül töltse fel a dataframe egy SQL data warehouse-táblaként.

Ahogy korábban említettük, az SQL Data Warehouse-összekötő használatával az Azure Blob storage ideiglenes tárolóként adatfeltöltés az Azure Databricks és az Azure SQL Data Warehouse között. Ezért első lépésként adja meg a tárfiókhoz való csatlakozáshoz szükséges konfigurációt. Kell már már létrehozta a fiókot a cikk előfeltételeinek részeként.

1. Adja meg az Azure Storage-fiók Azure Databricksből való eléréséhez szükséges konfigurációt.

   ```scala
   val blobStorage = "<blob-storage-account-name>.blob.core.windows.net"
   val blobContainer = "<blob-container-name>"
   val blobAccessKey =  "<access-key>"
   ```

2. Adjon meg egy ideiglenes mappát használni az adatok Azure Databricks és az Azure SQL Data Warehouse közötti áthelyezésekor.

   ```scala
   val tempDir = "wasbs://" + blobContainer + "@" + blobStorage +"/tempDirs"
   ```

3. Futtassa az alábbi kódrészletet az Azure Blob Storage hozzáférési kulcsainak a konfigurációban való tárolásához. Ez a művelet biztosítja, hogy nem kell tárolnia a hozzáférési kulcsot a jegyzetfüzetben szövegként.

   ```scala
   val acntInfo = "fs.azure.account.key."+ blobStorage
   sc.hadoopConfiguration.set(acntInfo, blobAccessKey)
   ```

4. Adja meg az Azure SQL Data Warehouse-példányhoz való csatlakozáshoz szükséges értékeket. Meg kell létrehozni egy SQL data warehouse előfeltétele.

   ```scala
   //SQL Data Warehouse related settings
   val dwDatabase = "<database-name>"
   val dwServer = "<database-server-name>"
   val dwUser = "<user-name>"
   val dwPass = "<password>"
   val dwJdbcPort =  "1433"
   val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
   val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ":" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
   val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ":" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
   ```

5. Futtassa az alábbi kódrészletet az átalakított adathalmaz betöltése **renamedColumnsDF**, egy SQL data warehouse-táblaként. Ez a kódrészlet létrehoz egy **SampleTable** nevű táblát az SQL-adatbázisban.

   ```scala
   spark.conf.set(
       "spark.sql.parquet.writeLegacyFormat",
       "true")

   renamedColumnsDF.write
       .format("com.databricks.spark.sqldw")
       .option("url", sqlDwUrlSmall) 
       .option("dbtable", "SampleTable")
       .option( "forward_spark_azure_storage_credentials","True")
       .option("tempdir", tempDir)
       .mode("overwrite")
       .save()
   ```

6. Az SQL-adatbázishoz csatlakozik, és győződjön meg arról, hogy megjelenik-e egy adatbázist **SampleTable**.

   ![Ellenőrizze a minta tábla](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table.png "mintatábla ellenőrzése")

7. Futtasson egy választó lekérdezést a tábla tartalmának ellenőrzéséhez. A táblában kell ugyanazokat az adatokat a **renamedColumnsDF** dataframe.

    ![A mintatábla tartalmának ellenőrzése](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table-content.png "a mintatábla tartalmának ellenőrzése")

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután elvégezte az oktatóanyagot, leállíthatja a fürtöt. Válassza ki az Azure Databricks-munkaterület **fürtök** a bal oldalon. A fürt leállításához, alatt **műveletek**, mutasson a három pontra (...), és válassza ki a **Leállítás** ikonra.

![Databricks-fürt leállítása](./media/databricks-extract-load-sql-data-warehouse/terminate-databricks-cluster.png "Databricks-fürt leállítása")

Ha Ön nem állítja le manuálisan a fürtöt, automatikusan leáll, feltéve kiválasztott a **után leáll \_ \_ ennyi perc inaktivitás** jelölőnégyzetet, a fürt létrehozásakor. Ebben az esetben a fürt azt automatikusan leáll, ha inaktív volt a megadott ideig.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Hozzon létre egy Azure Databricks szolgáltatás
> * Spark-fürt létrehozása az Azure Databricksben
> * Jegyzetfüzet létrehozása az Azure Databricksben
> * Adatok kinyerése a Data Lake Storage Gen2-fiók
> * Adatok átalakítása az Azure Databricksben
> * Adatok betöltése az Azure SQL Data Warehouse-ba

Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan streamelhetők valós időben az adatok az Azure Databricksbe az Azure Event Hubs használatával.

> [!div class="nextstepaction"]
>[Adatok streamelése az Azure Databricksbe az Event Hubs használatával](databricks-stream-from-eventhubs.md)
