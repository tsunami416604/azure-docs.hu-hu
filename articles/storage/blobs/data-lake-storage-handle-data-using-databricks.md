---
title: 'Oktatóanyag: Ismerje meg, hogyan hajtsa végre a kinyerési, betölteni, és átadja a műveleteket az Azure Databricks használatával'
description: Ebben az oktatóanyagban elsajátíthatja az Azure Data Lake Storage Gen2 előzetes verzió az Azure Databricksbe az adatok kinyerése, átalakítja az adatokat, és majd betölteni az adatokat az Azure SQL Data Warehouse-bA.
services: storage
author: jamesbak
ms.service: storage
ms.author: jamesbak
ms.topic: tutorial
ms.date: 01/14/2019
ms.component: data-lake-storage-gen2
ms.openlocfilehash: e4e75c65178c4bbedcf781c2fbf2149a94a702cd
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2019
ms.locfileid: "54321194"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Oktatóanyag: A kinyerési, átalakítási és az adatok betöltése az Azure Databricks használatával

Ebben az oktatóanyagban egy ETL (kinyerés, átalakítás és adatok betöltése) végrehajtásához használhatja az Azure Databricks műveletet. Adatok áthelyezése Azure Storage-fiókot az Azure Data Lake Storage Gen2 engedélyezve van az Azure SQL Data Warehouse.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az Azure Databricks-munkaterület létrehozása.
> * Spark-fürt létrehozása az Azure Databricksben.
> * Hozzon létre egy Azure Data Lake Storage Gen2 képes a fiókot.
> * Adatok feltöltése az Azure Data Lake Storage Gen2-re.
> * Hozzon létre egy jegyzetfüzetet az Azure Databricksben.
> * Adatok kinyerése a Data Lake Storage Gen2.
> * Adatátalakítás az Azure Databricksben.
> * Adatok betöltése az Azure SQL Data Warehouse-bA.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* Hozzon létre egy Azure SQL data warehouse, hozzon létre egy kiszolgálószintű tűzfalszabályt, és csatlakozzon a kiszolgálóhoz kiszolgáló-rendszergazdaként Kövesse az utasításokat a [a rövid útmutató: Hozzon létre egy Azure SQL data warehouse](../../sql-data-warehouse/create-data-warehouse-portal.md) cikk.
* Hozzon létre egy fő adatbáziskulcsot az Azure SQL data warehouse-hoz. Kövesse az utasításokat a [hozzon létre egy fő adatbáziskulcsot](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key) cikk.
* [Hozzon létre egy Azure Data Lake Storage Gen2 fiókot](data-lake-storage-quickstart-create-account.md).
* Töltse le a (**small_radio_json.json**) fájlt a [U-SQL példákat és problémakövetést ](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json) tartalmazó adattárból, és jegyezze fel az elérési utat, ahová a fájlt menti.
* Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="set-aside-storage-account-configuration"></a>Tárfiók-konfiguráció feljegyzése

Szüksége lesz a tárfiók és a egy rendszer végpont URI nevét.

A tárfiók nevének lekérése az Azure Portalon, válassza ki a **minden szolgáltatás** és a kifejezést a szűrő *tárolási*. Ezután válassza ki **tárfiókok** , és keresse meg a storage-fiókjában.

A rendszer végpont URI lekéréséhez válassza **tulajdonságok**, és a Tulajdonságok panelen keresse meg az értékét a **ADLS rendszer elsődleges VÉGPONT** mező.

Illessze be mindkettőt ezeket az értékeket egy szövegfájlba. Szüksége lesz rájuk hamarosan.

<a id="service-principal"/>

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Ebben a témakörben található útmutatást követve hozzon létre egy egyszerű szolgáltatást: [Útmutató: A portál használatával hozzon létre egy Azure AD alkalmazás és -szolgáltatásnév erőforrások eléréséhez](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Van néhány konkrét dolgot, mivel ebben a cikkben hajtsa végre a lépéseket kell.

:heavy_check_mark: A lépések végrehajtásakor a [Azure Active Directory-alkalmazás létrehozása](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application) szakasz a cikk, ügyeljen arra, hogy állítsa be a **bejelentkezési URL-** mezőjében a **létrehozás** párbeszédpanel a végpont URI-t, hogy nemrég összegyűjtött.

:heavy_check_mark: A lépések végrehajtásakor a [alkalmazások szerepkörhöz rendeléséhez](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) szakaszt a cikk, ügyeljen arra, hogy az alkalmazás hozzárendelése a **Blob Storage-közreműködői szerepkör**.

:heavy_check_mark: A lépések végrehajtásakor a [értékek beolvasása bejelentkezés](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) szakaszában a cikk, illessze be a bérlő Azonosítóját, Alkalmazásazonosító és hitelesítési kulcs értékeit egy szövegfájlba. Kell azokat, hamarosan.

## <a name="create-the-workspace"></a>A munkaterület létrehozása

Ebben a szakaszban az Azure portal használatával hoz létre Azure Databricks-munkaterületet.

1. Az Azure Portalon válassza az **Erőforrás létrehozása** > **Elemzés** > **Azure Databricks** elemet.

    ![Databricks az Azure Portalon](./media/data-lake-storage-handle-data-using-databricks/azure-databricks-on-portal.png "Databricks az Azure Portalon")

1. A **Azure Databricks szolgáltatás**, adja meg a Databricks-munkaterület létrehozásához a következő értékeket:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |**Munkaterület neve**     | Adja meg a Databricks-munkaterület nevét.        |
    |**Előfizetés**     | Válassza ki a legördülő menüből a saját Azure-előfizetését.        |
    |**Erőforráscsoport**     | Adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne használni. Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. További információért olvassa el az [Azure-erőforráscsoportok áttekintését](../../azure-resource-manager/resource-group-overview.md). |
    |**Hely**     | Válassza az **USA 2. nyugati régióját**.        |
    |**Tarifacsomag**     |  Válassza ki **Standard**.     |

    ![Azure Databricks-munkaterület létrehozása](./media/data-lake-storage-handle-data-using-databricks/create-databricks-workspace.png "Azure Databricks-munkaterület létrehozása")

1. Válassza a **Rögzítés az irányítópulton**, majd a **Létrehozás** lehetőséget.

1. A fiók létrehozása eltarthat néhány percig. Fiók létrehozása során a portál megjeleníti a **üzemelő példány elküldése az Azure Databricks** csempére a jobb oldalon. Műveleti állapotának figyelése, megtekintheti a folyamatjelző sáv tetején.

    ![Databricks üzembe helyezési csempe](./media/data-lake-storage-handle-data-using-databricks/databricks-deployment-tile.png "Databricks üzembe helyezési csempe")

## <a name="create-the-spark-cluster"></a>A Spark-fürt létrehozása

Ebben az oktatóanyagban a műveletek végrehajtásához szüksége van egy Spark-fürtöt. A következő lépések segítségével hozzon létre a Spark-fürtön.

1. Az Azure Portalon nyissa meg a létrehozott Databricks-munkaterülethez, és válassza ki **munkaterület indítása**.

1. A program átirányítja az Azure Databricks portáljára. A portálon válassza a **Fürt** elemet.

    ![Databricks az Azure-on](./media/data-lake-storage-handle-data-using-databricks/databricks-on-azure.png "Databricks az Azure-on")

1. Az **Új fürt** lapon adja meg a fürt létrehozásához szükséges értékeket.

    ![Databricks Spark-fürt létrehozása az Azure-on](./media/data-lake-storage-handle-data-using-databricks/create-databricks-spark-cluster.png "Databricks Spark-fürt létrehozása az Azure-on")

1. Adjon meg értékeket a következő mezőkben, és fogadja el az alapértelmezett értékeket a többi mezőben:

    * Adjon egy nevet a fürtnek.
    * Ebben a cikkben hozzon létre egy fürtöt a **5.1** modul.
    * Mindenképpen jelölje ki a **leállítása után \_ \_ ennyi perc inaktivitás** jelölőnégyzetet. Ha a fürt nem használ, adja meg az időtartamot (percben) a fürt leállításához.

1. Válassza a **Fürt létrehozása** lehetőséget.

Miután a fürt fut, notebookokat csatlakoztathat hozzá, a fürt, és a Spark-feladatok futtatása.

## <a name="create-a-file-system"></a>Hozzon létre egy fájlrendszer

A Data Lake Storage Gen2 tárfiókban lévő adatok tárolásához, szeretne létrehozni egy fájlrendszert.

Először hozzon létre egy jegyzetfüzetet az Azure Databricks-munkaterületen, és majd a fájlrendszer létrehozása a tárfiókban lévő kódrészleteket futtat.

1. Az a [az Azure portal](https://portal.azure.com)nyissa meg az Ön által létrehozott Azure Databricks-munkaterületet, és válassza ki **munkaterület indítása**.

2. A bal oldalon válassza ki a **munkaterület**. A **Munkaterület** legördülő menüből válassza a **Létrehozás** > **Jegyzetfüzet** lehetőséget.

    ![Hozzon létre egy notebookot a Databricksben](./media/data-lake-storage-handle-data-using-databricks/databricks-create-notebook.png "notebook létrehozása a Databricksben")

3. A **Jegyzetfüzet létrehozása** párbeszédpanelen adja meg a jegyzetfüzet nevét. Válassza a **Scala** nyelvet, majd válassza ki a korábban létrehozott Spark-fürtöt.

    ![Adja meg adatait egy jegyzetfüzetet a Databricksben](./media/data-lake-storage-handle-data-using-databricks/databricks-notebook-details.png "adja meg adatait egy jegyzetfüzetet a Databricksben")

    Kattintson a **Létrehozás** gombra.

4. Másolja és illessze be az alábbi kódblokkot az első olyan cellára, de még ne futtassa ezt a kódot.

    ```scala
    val configs = Map(
    "fs.azure.account.auth.type" -> "OAuth",
    "fs.azure.account.oauth.provider.type" -> "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
    "fs.azure.account.oauth2.client.id" -> "<application-id>",
    "fs.azure.account.oauth2.client.secret" -> "<authentication-key>"),
    "fs.azure.account.oauth2.client.endpoint" -> "https://login.microsoftonline.com/<tenant-id>/oauth2/token",
    "fs.azure.createRemoteFileSystemDuringInitialization"->"true")

    dbutils.fs.mount(
    source = "abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/<directory-name>",
    mountPoint = "/mnt/<mount-name>",
    extraConfigs = configs)
    ```

5. A kódblokk, cserélje le a `storage-account-name`, `application-id`, `authentication-id`, és `tenant-id` lépéseinek végrehajtását összegyűjtött értékek a kódblokk a helyőrző értékeket a [félretett storage-fiók konfigurációs](#config) és [egyszerű szolgáltatás létrehozása](#service-principal) Ez a cikk szakaszainak. Állítsa be a `file-system-name`, `directory-name`, és `mount-name` helyőrző értékeket bármilyen nevek kíván adni a fájlrendszer, a címtár és a csatlakoztatási pont.

6. Nyomja le az **SHIFT + ENTER** kulcsok a kód futtatásához a blokk.

## <a name="upload-the-sample-data"></a>A mintaadatok feltöltése

A következő lépés, hogy egy minta adatfájl feltöltése a storage-fiókot, később az Azure Databricksben átalakítására.

Töltse fel a mintaadatokat, amelyet a tárfiókba töltött le. A módszer, amellyel az adatok feltöltése a storage-fiók különbözik attól függően, hogy a hierarchikus névtér engedélyezve van-e.

Azure Data Factory, distp vagy (10-es verzió) az AzCopy segítségével hajtsa végre a feltöltés. Az AzCopy 10-es verzió jelenleg csak előzetes keresztül érhető el. Az AzCopy használatához illessze be a következő kódot egy parancssori ablakba:

```bash
set ACCOUNT_NAME=<ACCOUNT_NAME>
set ACCOUNT_KEY=<ACCOUNT_KEY>
azcopy cp "<DOWNLOAD_PATH>\small_radio_json.json" https://<ACCOUNT_NAME>.dfs.core.windows.net/data --recursive 
```

## <a name="extract-the-data"></a>Az adatok kinyerése

Dolgozunk a mintaadatokat a Databricksben, az adatok kinyerése tárfiókja kell.

Térjen vissza a Databricks-jegyzetfüzet, és írja be a következő kódot a jegyzetfüzet új cellára.

Adja hozzá a következő kódrészletet egy üres kódcellába. Cserélje le a zárójelben látható zárójelben a tárfiókból korábban mentett értékekkel.

```scala
dbutils.widgets.text("storage_account_name", "STORAGE_ACCOUNT_NAME", "<YOUR_STORAGE_ACCOUNT_NAME>")
dbutils.widgets.text("storage_account_access_key", "YOUR_ACCESS_KEY", "<YOUR_STORAGE_ACCOUNT_SHARED_KEY>")
```

Válassza ki a Shift + Enter kulcsokat, a kód futtatásához.

Most már betöltheti a JSON-mintafájlt az Azure Databricks adathalmazaként. Illessze be a következő kódot egy új cellára. Cserélje le a zárójelben látható zárójelben a értékeire.

```scala
val df = spark.read.json("abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/data/small_radio_json.json")
```

Válassza ki a Shift + Enter kulcsokat, a kód futtatásához.

Futtassa a következő kódot az adathalmaz tartalmának megtekintéséhez:

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

## <a name="transform-the-data"></a>Az adatok átalakítása

A nyers mintaadatok **small_radio_json.json** fájl rádióállomás hallgatóit rögzíti, és a egy számos oszloppal rendelkezik. Ebben a szakaszban átalakítja az adatokat, hogy csak bizonyos oszlopokat kérjen le az adatkészletből.

Először a csak az oszlopok lekéréséhez **firstName**, **lastName**, **nemek**, **hely**, és **szint**létrehozott adathalmazból.

```scala
val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
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

Az adatok további átalakításához nevezze át a **level** oszlopot a következőre: **subscription_type**.

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

## <a name="load-the-data"></a>Az adatok betöltése

Ebben a szakaszban feltölti az átalakított adatokat az Azure SQL Data Warehouse-ba. Az Azure Databricks Azure SQL Data Warehouse-összekötő segítségével közvetlenül töltse fel a dataframe egy SQL data warehouse-táblaként.

Az SQL Data Warehouse-összekötő az ideiglenes tároló Azure Blob storage használatával töltse fel az adatokat az Azure Databricks és az Azure SQL Data Warehouse között. Ezért első lépésként adja meg a tárfiókhoz való csatlakozáshoz szükséges konfigurációt. Kell már hozott létre a fiókot a cikk előfeltételeinek részeként.

Adja meg az Azure Storage-fiók Azure Databricksből való eléréséhez szükséges konfigurációt.

```scala
val storageURI = "<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net"
val fileSystemName = "<FILE_SYSTEM_NAME>"
val accessKey =  "<ACCESS_KEY>"
```

Adjon meg egy ideiglenes mappát használni az adatok Azure Databricks és az Azure SQL Data Warehouse közötti áthelyezésekor.

```scala
val tempDir = "abfs://" + fileSystemName + "@" + storageURI +"/tempDirs"
```

Futtassa az alábbi kódrészletet az Azure Blob Storage hozzáférési kulcsainak a konfigurációban való tárolásához. Ez a művelet biztosítja, hogy nem kell tárolnia a hozzáférési kulcsot a jegyzetfüzetben szövegként.

```scala
val acntInfo = "fs.azure.account.key."+ storageURI
sc.hadoopConfiguration.set(acntInfo, accessKey)
```

Adja meg az Azure SQL Data Warehouse-példányhoz való csatlakozáshoz szükséges értékeket. Meg kell létrehozni egy SQL data warehouse előfeltétele.

```scala
//SQL Data Warehouse related settings
val dwDatabase = "<DATABASE NAME>"
val dwServer = "<DATABASE SERVER NAME>" 
val dwUser = "<USER NAME>"
val dwPass = "<PASSWORD>"
val dwJdbcPort =  "1433"
val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
```

Futtassa az alábbi kódrészletet az átalakított adathalmaz betöltése **renamedColumnsDF**, egy SQL data warehouse-táblaként. Ez a kódrészlet létrehoz egy **SampleTable** nevű táblát az SQL-adatbázisban.

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

Az SQL-adatbázishoz csatlakozik, és győződjön meg arról, hogy megjelenik-e egy adatbázist **SampleTable**.

![Ellenőrizze a minta tábla](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table.png "mintatábla ellenőrzése")

Futtasson egy választó lekérdezést a tábla tartalmának ellenőrzéséhez. A táblában kell ugyanazokat az adatokat a **renamedColumnsDF** dataframe.

![A mintatábla tartalmának ellenőrzése](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table-content.png "a mintatábla tartalmának ellenőrzése")

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután elvégezte az oktatóanyagot, leállíthatja a fürtöt. Válassza ki az Azure Databricks-munkaterület **fürtök** a bal oldalon. A fürt leállításához, alatt **műveletek**, mutasson a három pontra (...), és válassza ki a **Leállítás** ikonra.

![Databricks-fürt leállítása](./media/data-lake-storage-handle-data-using-databricks/terminate-databricks-cluster.png "Databricks-fürt leállítása")

Ha Ön nem állítja le manuálisan a fürtöt, automatikusan leáll, feltéve kiválasztott a **után leáll \_ \_ ennyi perc inaktivitás** jelölőnégyzetet, a fürt létrehozásakor. Ebben az esetben a fürt azt automatikusan leáll, ha inaktív volt a megadott ideig.

## <a name="next-steps"></a>További lépések

Folytassa a következő oktatóanyaggal, megtudhatja, hogyan valós idejű adatok streamelése az Azure Databricksbe az Azure Event Hubs használatával.

> [!div class="nextstepaction"]
>[Stream-adatokat az Azure Databricksbe az Event Hubs használatával](../../azure-databricks/databricks-stream-from-eventhubs.md)
