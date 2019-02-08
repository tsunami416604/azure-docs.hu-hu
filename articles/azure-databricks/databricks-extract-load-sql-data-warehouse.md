---
title: 'Oktatóanyag: Az Azure Databricks segítségével ETL-műveletek végrehajtása'
description: 'Útmutató: adatok kinyerése a Data Lake Storage Gen2 az Azure Databricksbe, átalakítja az adatokat, és majd betölteni az adatokat az Azure SQL Data Warehouse-bA.'
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.workload: Active
ms.date: 01/24/2019
ms.openlocfilehash: 57de2d9c63a4185997ac86056b9e3189ad66e478
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55893132"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Oktatóanyag: A kinyerési, átalakítási és az adatok betöltése az Azure Databricks használatával

Ebben az oktatóanyagban egy ETL (kinyerés, átalakítás és adatok betöltése) hajt végre műveletet az Azure Databricks használatával. Adatok kinyerése az Azure Data Lake Storage Gen2 az Azure Databricksbe, átalakításokat futtat az adatok az Azure Databricksben, és majd betölti az átalakított adatokat az Azure SQL Data Warehouse-bA.

A jelen oktatóanyagban szereplő lépések az Azure Databricks SQL Data Warehouse-összekötőjét használják az adatok Azure Databricksbe való átviteléhez. Az összekötő ezután az Azure Blob Storage-ot használja ideiglenes tárolóként az Azure Databricks-fürt és az Azure SQL Data Warehouse között átvitt adatokhoz.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Hozzon létre egy Azure Databricks szolgáltatást.
> * Spark-fürt létrehozása az Azure Databricksben.
> * Hozzon létre egy fájlrendszert, és töltse fel az adatokat az Azure Data Lake Storage Gen2-re.
> * Hozzon létre egy egyszerű szolgáltatást.
> * Adatok kinyerése a Data Lake Store.
> * Adatátalakítás az Azure Databricksben.
> * Adatok betöltése az Azure SQL Data Warehouse-bA.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre ezeket a feladatokat, ez az oktatóanyag megkezdése előtt:

* Hozzon létre egy Azure SQL data warehouse, hozzon létre egy kiszolgálószintű tűzfalszabályt, és csatlakozzon a kiszolgálóhoz kiszolgáló-rendszergazdaként Lásd: [a rövid útmutató: Hozzon létre egy Azure SQL data warehouse](../sql-data-warehouse/create-data-warehouse-portal.md).

* Hozzon létre egy fő adatbáziskulcsot az Azure SQL data warehouse-hoz. Lásd: [hozzon létre egy fő adatbáziskulcsot](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).

* Hozzon létre egy Azure Data Lake Storage Gen2-fiókot. Lásd: [hozzon létre egy Azure Data Lake Storage Gen2 fiókot](../storage/blobs/data-lake-storage-quickstart-create-account.md).

* Hozzon létre egy Azure Blob Storage-fiókot, benne egy tárolóval. A hozzáférési kulcsot is kérje le a tárfiók eléréséhez. Lásd: [a rövid útmutató: Hozzon létre egy Azure Blob storage-fiók](../storage/blobs/storage-quickstart-blobs-portal.md).

* Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

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

## <a name="create-a-file-system-and-upload-sample-data"></a>Hozzon létre egy fájlrendszert és mintaadatok feltöltése

Először hozza létre az operációs rendszer a Data Lake Storage Gen2-fiók. Ezután a Data Lake Store egy Mintaadat-fájlt is feltölthet. Ezt a fájlt később az Azure Databricksben fogja használni átalakítások futtatására.

1. Töltse le a [small_radio_json.json](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json) Mintaadat-fájlt a helyi fájlrendszerbe.

2. Az a [az Azure portal](https://portal.azure.com/), lépjen a Data Lake Storage Gen2 fiók ebben az oktatóanyagban az előfeltételként létrehozott.

3. Az a **áttekintése** a tárfiókot, válassza az oldal **Megnyitás az Explorerben**.

   ![Nyissa meg a Storage Explorer](./media/databricks-extract-load-sql-data-warehouse/data-lake-storage-open-storage-explorer.png "nyissa meg a Storage Explorerben")

4. Válassza ki **nyissa meg az Azure Storage Explorer** Storage Explorer megnyitásához.

   ![Nyissa meg a Storage Explorer második parancssort](./media/databricks-extract-load-sql-data-warehouse/data-lake-storage-open-storage-explorer-2.png "nyissa meg a Storage Explorer másodjára")

   Megnyílik a Storage Explorerben. Hozzon létre egy fájlrendszert, és ez a témakör útmutatása szerint a mintaadatok feltöltése: [Rövid útmutató: Az Azure Data Lake Storage Gen2-fiókban lévő adatok kezelése az Azure Storage Explorer használatával](../storage/blobs/data-lake-storage-explorer.md).

<a id="service-principal"/>

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Ebben a témakörben található útmutatást követve hozzon létre egy egyszerű szolgáltatást: [Útmutató: A portál használatával hozzon létre egy Azure AD alkalmazás és -szolgáltatásnév erőforrások eléréséhez](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Van néhány dolgot, mivel ebben a cikkben hajtsa végre a lépéseket kell.

:heavy_check_mark: A lépések végrehajtásakor a [alkalmazások szerepkörhöz rendeléséhez](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) szakaszt a cikk, ügyeljen arra, hogy az alkalmazás hozzárendelése a **Blob Storage-közreműködői szerepkör**.

:heavy_check_mark: A lépések végrehajtásakor a [értékek beolvasása bejelentkezés](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) szakaszában a cikk, illessze be a bérlő Azonosítóját, Alkalmazásazonosító és hitelesítési kulcs értékeit egy szövegfájlba. Kell azokat, hamarosan.
Először hozzon létre egy jegyzetfüzetet az Azure Databricks-munkaterületen, és majd a fájlrendszer létrehozása a tárfiókban lévő kódrészleteket futtat.

## <a name="extract-data-from-the-data-lake-store"></a>Adatok kinyerése a Data Lake Store

Ebben a szakaszban létrehoz egy jegyzetfüzetet az Azure Databricks-munkaterület, és futtassa a kódrészleteket adatokat nyerhet ki a Data Lake Store-ból az Azure Databricksbe.

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
   ```

6. A kódblokk, cserélje le a `application-id`, `authentication-id`, és `tenant-id` lépéseit a Set feltöltési storage-fiók konfigurációjának befejezésekor gyűjtött értékek a kódblokk a helyőrző értékeket. Cserélje le a `storage-account-name` helyőrző értéket cserélje a tárfiókja nevére.

7. Nyomja le az **SHIFT + ENTER** kulcsok a kód futtatásához a blokk.

8. Az Azure Databricksben adatok keretként most már betöltheti a json-mintafájlt. Illessze be a következő kódot egy új cellára. Cserélje le a zárójelben látható zárójelben a értékeire.

   ```scala
   val df = spark.read.json("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/small_radio_json.json")
   ```

   * Cserélje le a `file-system-name` helyőrző értéket cserélje a neve, hogy a fájlrendszer a Storage Explorerben.

   * Cserélje le a `storage-account-name` helyőrzőt a tárfiók nevére.

9. Nyomja le az **SHIFT + ENTER** kulcsok a kód futtatásához a blokk.

10. Futtassa a következő kódot az adathalmaz tartalmának megtekintéséhez:

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
   val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
   val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
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
