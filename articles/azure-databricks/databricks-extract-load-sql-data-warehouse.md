---
title: Oktatóanyag – ETL-műveletek végrehajtása Azure Databricks használatával
description: Ebből az oktatóanyagból megtudhatja, hogyan nyerheti ki a Data Lake Storage Gen2 adatait a Azure Databricksba, átalakíthatja az adatait, majd betöltheti őket a Azure SQL Data Warehouseba.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.date: 01/29/2020
ms.openlocfilehash: 8c7c9c2e3a1195422db30ba913b1cea3a1a360e4
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78301692"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Oktatóanyag: adatok kinyerése, átalakítása és betöltése a Azure Databricks használatával

Ebben az oktatóanyagban egy ETL-műveletet (kinyerési, átalakítási és betöltési) műveletet hajt végre Azure Databricks használatával. Az adatok kinyerése Azure Data Lake Storage Gen2ból a Azure Databricksba, a Azure Databricks lévő adatokon futtatott átalakítások futtatása, valamint az átalakított adatok Azure SQL Data Warehouseba való betöltése.

A jelen oktatóanyagban szereplő lépések az Azure Databricks SQL Data Warehouse-összekötőjét használják az adatok Azure Databricksbe való átviteléhez. Az összekötő ezután az Azure Blob Storage-ot használja ideiglenes tárolóként az Azure Databricks-fürt és az Azure SQL Data Warehouse között átvitt adatokhoz.

Az alábbi ábrán az alkalmazásfolyam látható:

![Azure Databricks Data Lake Store és SQL Data Warehouse](./media/databricks-extract-load-sql-data-warehouse/databricks-extract-transform-load-sql-datawarehouse.png "Azure Databricks Data Lake Store és SQL Data Warehouse")

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Hozzon létre egy Azure Databricks szolgáltatást.
> * Hozzon létre egy Spark-fürtöt Azure Databricksban.
> * Hozzon létre fájlrendszert a Data Lake Storage Gen2 fiókban.
> * Mintaadatok feltöltése a Azure Data Lake Storage Gen2-fiókba.
> * Egyszerű szolgáltatásnév létrehozása.
> * Adatok kinyerése az Azure Data Lake Storage Gen2-fiókból.
> * Az adatátalakítás Azure Databricks.
> * Betöltés az Azure SQL Data Warehouseba.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!Note]
> Ez az oktatóanyag nem hajtható végre az **Azure ingyenes próbaverziós előfizetésével**.
> Ha ingyenes fiókkal rendelkezik, lépjen a profilba, és változtassa meg az előfizetését **az utólagos**elszámolású verzióra. További információkért lásd az [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) ismertető cikket. Ezután [távolítsa el a](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)költségkeretet, és [igényeljen kvóta-növekedést](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) a régiójában lévő vCPU. A Azure Databricks munkaterületének létrehozásakor kiválaszthatja a **próbaverzió (prémium-14 napos ingyenes dBu)** díjszabását, hogy a munkaterület 14 napig elérhető legyen az ingyenes prémium Azure Databricks dBu.
     
## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt végezze el ezeket a feladatokat:

* Hozzon létre egy Azure SQL-adattárházat, hozzon létre egy kiszolgálói szintű tűzfalszabály-szabályt, és kapcsolódjon a kiszolgálóhoz kiszolgáló-rendszergazdaként. Tekintse [meg a rövid útmutató: Azure SQL-adattárház létrehozása és lekérdezése a Azure Portal](../sql-data-warehouse/create-data-warehouse-portal.md).

* Hozzon létre egy főkulcsot az Azure SQL-adattárházhoz. Lásd: [adatbázis-főkulcs létrehozása](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).

* Hozzon létre egy Azure Blob Storage-fiókot, benne egy tárolóval. A hozzáférési kulcsot is kérje le a tárfiók eléréséhez. Lásd [: rövid útmutató: Blobok feltöltése, letöltése és listázása a Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

* Hozzon létre egy Azure Data Lake Storage Gen2 Storage-fiókot. Tekintse meg a rövid útmutató [: Azure Data Lake Storage Gen2 Storage-fiók létrehozása](../storage/blobs/data-lake-storage-quickstart-create-account.md)című témakört.

* Egyszerű szolgáltatásnév létrehozása. [Útmutató: a portál használatával létrehozhat egy Azure ad-alkalmazást és egy egyszerű szolgáltatást, amely hozzáférhet az erőforrásokhoz](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

   A cikk lépéseinek elvégzése során néhány konkrét dolgot is el kell végeznie.

   * Az [alkalmazás szerepkörhöz való hozzárendelésével](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) kapcsolatos lépések végrehajtásakor ügyeljen arra, hogy hozzárendelje a **Storage blob adatközreműködői** szerepkört az egyszerű szolgáltatásnév számára a Data Lake Storage Gen2 fiók hatókörében. Ha a szerepkört a szülő erőforráscsoporthoz vagy előfizetéshez rendeli hozzá, akkor az engedélyekkel kapcsolatos hibák addig jelentkeznek, amíg a szerepkör-hozzárendelések el nem terjednek a Storage-fiókba.

      Ha egy hozzáférés-vezérlési listát (ACL) szeretne használni az egyszerű szolgáltatás egy adott fájlhoz vagy könyvtárhoz való hozzárendeléséhez, akkor [a Azure Data Lake Storage Gen2 a hozzáférés-vezérlést](../storage/blobs/data-lake-storage-access-control.md).

   * A cikk beléptetési [értékek beolvasása](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) szakaszában szereplő lépések végrehajtásakor illessze be a bérlői azonosítót, az alkalmazás azonosítóját és a titkos értékeket egy szövegfájlba. Ezekre hamarosan szüksége lesz.

* Jelentkezzen be az [Azure Portal](https://portal.azure.com/).

## <a name="gather-the-information-that-you-need"></a>Gyűjtse össze a szükséges információkat

Győződjön meg arról, hogy elvégezte az oktatóanyag előfeltételeit.

   Mielőtt elkezdené, a következő információkat kell megjelennie:

   : heavy_check_mark: az Azure SQL-adattárház adatbázisának neve, adatbázis-kiszolgálójának neve, felhasználóneve és jelszava.

   : heavy_check_mark: a blob Storage-fiók elérési kulcsa.

   : heavy_check_mark: az Data Lake Storage Gen2 Storage-fiók neve.

   : heavy_check_mark: az előfizetés bérlői azonosítója.

   : heavy_check_mark: a Azure Active Directory (Azure AD) szolgáltatásban regisztrált alkalmazás alkalmazás-azonosítója.

   : heavy_check_mark: az Azure AD-ben regisztrált alkalmazás hitelesítési kulcsa.

## <a name="create-an-azure-databricks-service"></a>Azure Databricks szolgáltatás létrehozása

Ebben a szakaszban egy Azure Databricks szolgáltatást hoz létre a Azure Portal használatával.

1. A Azure Portal menüben válassza az **erőforrás létrehozása**lehetőséget.

    ![Erőforrás létrehozása Azure Portal](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-on-portal.png)

    Ezután válassza az **elemzési** > **Azure Databricks**lehetőséget.

    ![Azure Databricks létrehozása Azure Portal](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-resource-create.png)



2. A **Azure Databricks szolgáltatás**területen adja meg a következő értékeket egy Databricks szolgáltatás létrehozásához:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |**Munkaterület neve**     | Adja meg a Databricks-munkaterület nevét.        |
    |**Előfizetés**     | Válassza ki a legördülő menüből a saját Azure-előfizetését.        |
    |**Erőforráscsoport**     | Adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne használni. Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. További információért olvassa el az [Azure-erőforráscsoportok áttekintését](../azure-resource-manager/management/overview.md). |
    |**Hely**     | Válassza az **USA 2. nyugati régióját**.  A további elérhető régiókért tekintse meg az [elérhető Azure-szolgáltatások régiók szerinti bontását](https://azure.microsoft.com/regions/services/).      |
    |**Tarifacsomag**     |  Válassza a **standard**lehetőséget.     |

3. A fiók létrehozása eltarthat néhány percig. A művelet állapotának figyeléséhez tekintse meg a felső folyamatjelző sávot.

4. Válassza a **Rögzítés az irányítópulton**, majd a **Létrehozás** lehetőséget.

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Spark-fürt létrehozása az Azure Databricksben

1. A Azure Portal lépjen a létrehozott Databricks szolgáltatásra, majd válassza a **munkaterület elindítása**lehetőséget.

2. A rendszer átirányítja a Azure Databricks portálra. A portálon válassza a **Fürt** elemet.

    ![Databricks az Azure-ban](./media/databricks-extract-load-sql-data-warehouse/databricks-on-azure.png "Databricks az Azure-ban")

3. Az **Új fürt** lapon adja meg a fürt létrehozásához szükséges értékeket.

    ![Databricks Spark-fürt létrehozása az Azure-ban](./media/databricks-extract-load-sql-data-warehouse/create-databricks-spark-cluster.png "Databricks Spark-fürt létrehozása az Azure-ban")

4. Adjon meg értékeket a következő mezőkben, és fogadja el az alapértelmezett értékeket a többi mezőben:

    * Adjon egy nevet a fürtnek.

    * Győződjön meg arról, hogy a **megszakítás \_\_ perc inaktivitás után** jelölőnégyzet be van állítva. Ha a fürt nincs használatban, adjon meg egy időtartamot (percben) a fürt megszakításához.

    * Válassza a **Fürt létrehozása** lehetőséget. A fürt futása után jegyzetfüzeteket csatolhat a fürthöz, és futtathatja a Spark-feladatokat.

## <a name="create-a-file-system-in-the-azure-data-lake-storage-gen2-account"></a>Fájlrendszer létrehozása a Azure Data Lake Storage Gen2 fiókban

Ebben a szakaszban egy jegyzetfüzetet hoz létre Azure Databricks munkaterületen, majd kódrészleteket futtathat a Storage-fiók konfigurálásához.

1. A [Azure Portal](https://portal.azure.com)lépjen a létrehozott Azure Databricks szolgáltatásra, majd válassza a **munkaterület indítása**elemet.

2. A bal oldalon válassza a **munkaterület**lehetőséget. A **Munkaterület** legördülő menüből válassza a **Létrehozás** > **Jegyzetfüzet** lehetőséget.

    ![Jegyzetfüzet létrehozása a Databricks-ben](./media/databricks-extract-load-sql-data-warehouse/databricks-create-notebook.png "Jegyzetfüzet létrehozása a Databricks-ben")

3. A **Jegyzetfüzet létrehozása** párbeszédpanelen adja meg a jegyzetfüzet nevét. Válassza a **Scala** nyelvet, majd válassza ki a korábban létrehozott Spark-fürtöt.

    ![A Databricks lévő jegyzetfüzet részleteinek megadása](./media/databricks-extract-load-sql-data-warehouse/databricks-notebook-details.png "A Databricks lévő jegyzetfüzet részleteinek megadása")

4. Kattintson a **Létrehozás** gombra.

5. A következő kódrészlet az alapértelmezett egyszerű szolgáltatás hitelesítő adatait állítja be a Spark-munkamenetben elért bármely 2. generációs ADLS-fiókhoz. A második kódú blokk hozzáfűzi a fióknevet a beállításhoz, hogy megadja a hitelesítő adatokat egy adott ADLS 2. generációs fiókhoz.  Másolja és illessze be a kód blokkot a Azure Databricks notebookjának első cellájába.

   **Munkamenet-konfiguráció**

   ```scala
   val appID = "<appID>"
   val password = "<password>"
   val tenantID = "<tenant-id>"

   spark.conf.set("fs.azure.account.auth.type", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id", "<appID>")
   spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   ```

   **Fiók konfigurálása**

   ```scala
   val storageAccountName = "<storage-account-name>"
   val appID = "<app-id>"
   val secret = "<secret>"
   val fileSystemName = "<file-system-name>"
   val tenantID = "<tenant-id>"

   spark.conf.set("fs.azure.account.auth.type." + storageAccountName + ".dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type." + storageAccountName + ".dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id." + storageAccountName + ".dfs.core.windows.net", "" + appID + "")
   spark.conf.set("fs.azure.account.oauth2.client.secret." + storageAccountName + ".dfs.core.windows.net", "" + secret + "")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint." + storageAccountName + ".dfs.core.windows.net", "https://login.microsoftonline.com/" + tenantID + "/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   dbutils.fs.ls("abfss://" + fileSystemName  + "@" + storageAccountName + ".dfs.core.windows.net/")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")
   ```

6. Ebben a kódban a blokkban cserélje le a `<app-id>`, `<secret>`, `<tenant-id>`és `<storage-account-name>` helyőrző értékeit az oktatóanyag előfeltételeinek teljesítése során összegyűjtött értékekre. Cserélje le a `<file-system-name>` helyőrző értékét bármilyen névre, amelyet a fájlrendszerhez szeretne adni.

   * A `<app-id>`és az `<secret>` az Active Directory szolgáltatásban regisztrált alkalmazásból származnak.

   * A `<tenant-id>` az előfizetésből származik.

   * A `<storage-account-name>` a Azure Data Lake Storage Gen2 Storage-fiók neve.

7. Nyomja le a **SHIFT + ENTER** billentyűkombinációt a kód futtatásához ebben a blokkban.

## <a name="ingest-sample-data-into-the-azure-data-lake-storage-gen2-account"></a>Mintaadatok beolvasása a Azure Data Lake Storage Gen2 fiókba

Mielőtt ehhez a szakaszhoz hozzáfogna, a következő előfeltételeknek kell eleget tennie:

Írja be az alábbi kódot egy jegyzetfüzetcellába:

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

A cellában nyomja le a **SHIFT + ENTER** billentyűkombinációt a kód futtatásához.

Most egy új cellában az alábbi kód megadásával írja be a következő kódot, és cserélje le a zárójelben megjelenő értékeket a korábban használt értékekkel:

    dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://" + fileSystemName + "@" + storageAccountName + ".dfs.core.windows.net/")

A cellában nyomja le a **SHIFT + ENTER** billentyűkombinációt a kód futtatásához.

## <a name="extract-data-from-the-azure-data-lake-storage-gen2-account"></a>Adatok kinyerése az Azure Data Lake Storage Gen2-fiókból

1. Most már betöltheti a minta JSON-fájlt Azure Databricks adatkeretként. Illessze be a következő kódot egy új cellába. Cserélje le a zárójelben látható helyőrzőket az értékekre.

   ```scala
   val df = spark.read.json("abfss://" + fileSystemName + "@" + storageAccountName + ".dfs.core.windows.net/small_radio_json.json")
   ```
2. Nyomja le a **SHIFT + ENTER** billentyűkombinációt a kód futtatásához ebben a blokkban.

3. A következő kód futtatásával tekintheti meg az adatkeret tartalmát:

    ```scala
    df.show()
    ```
   Az alábbi kódrészlethez hasonló kimenet jelenik meg:

   ```output
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

A nyers mintaadatok **small_radio_json. JSON** fájl rögzíti a hallgatóságot egy rádióállomás számára, és számos oszloppal rendelkezik. Ebben a szakaszban az adatokat úgy alakítja át, hogy csak bizonyos oszlopokat kérjen le az adatkészletből.

1. Először a létrehozott dataframe csak a **firstName**, a **lastName**, a **gender**, a **Location**és a **Level** oszlopot kéri le.

   ```scala
   val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
   specificColumnsDf.show()
   ```

   A kimenet az alábbi kódrészletben látható módon jelenik meg:

   ```output
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

   A kimenet az alábbi kódrészletben látható módon jelenik meg.

   ```output
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

Ebben a szakaszban feltölti az átalakított adatokat az Azure SQL Data Warehouse-ba. A Azure Databricks Azure SQL Data Warehouse-összekötője segítségével közvetlenül tölthet fel egy dataframe egy SQL-adattárházban lévő táblázatként.

Ahogy korábban említettük, az SQL Data Warehouse-összekötő az Azure Blob Storage-ot használja ideiglenes tárolóként az adatok Azure Databricks és Azure SQL Data Warehouse közötti feltöltéséhez. Ezért első lépésként adja meg a tárfiókhoz való csatlakozáshoz szükséges konfigurációt. A jelen cikk előfeltételeinek részeként már létre kell hoznia a fiókot.

1. Adja meg az Azure Storage-fiók Azure Databricksből való eléréséhez szükséges konfigurációt.

   ```scala
   val blobStorage = "<blob-storage-account-name>.blob.core.windows.net"
   val blobContainer = "<blob-container-name>"
   val blobAccessKey =  "<access-key>"
   ```

2. Az Azure Databricks és Azure SQL Data Warehouse közötti adatáthelyezés során használandó ideiglenes mappa meghatározása.

   ```scala
   val tempDir = "wasbs://" + blobContainer + "@" + blobStorage +"/tempDirs"
   ```

3. Futtassa az alábbi kódrészletet az Azure Blob Storage hozzáférési kulcsainak a konfigurációban való tárolásához. Ez a művelet biztosítja, hogy az egyszerű szövegben nem kell megtartania a hozzáférési kulcsot a jegyzetfüzetben.

   ```scala
   val acntInfo = "fs.azure.account.key."+ blobStorage
   sc.hadoopConfiguration.set(acntInfo, blobAccessKey)
   ```

4. Adja meg az Azure SQL Data Warehouse-példányhoz való csatlakozáshoz szükséges értékeket. Előfeltételként létre kell hoznia egy SQL-adattárházat. Használja a **dwServer**teljes kiszolgálójának nevét. Például: `<servername>.database.windows.net`.

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

5. Futtassa az alábbi kódrészletet az átalakított dataframe ( **renamedColumnsDF**) betöltéséhez egy SQL-adattárházban lévő táblázatként. Ez a kódrészlet létrehoz egy **SampleTable** nevű táblát az SQL-adatbázisban.

   ```scala
   spark.conf.set(
       "spark.sql.parquet.writeLegacyFormat",
       "true")

   renamedColumnsDF.write.format("com.databricks.spark.sqldw").option("url", sqlDwUrlSmall).option("dbtable", "SampleTable")       .option( "forward_spark_azure_storage_credentials","True").option("tempdir", tempDir).mode("overwrite").save()
   ```

   > [!NOTE]
   > Ez a példa a `forward_spark_azure_storage_credentials` jelzőt használja, ami azt eredményezi, hogy a SQL Data Warehouse hozzáférési kulccsal fér hozzá az adatokhoz a blob Storage-ból. Ez az egyetlen támogatott hitelesítési módszer.
   >
   > Ha az Azure Blob Storage a virtuális hálózatok kiválasztására korlátozódik, SQL Data Warehouse a [hozzáférési kulcsok helyett Managed Service Identity](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)szükséges. Ez a következő hibaüzenetet eredményezi: "Ez a kérelem nem jogosult a művelet végrehajtására."

6. Kapcsolódjon az SQL-adatbázishoz, és ellenőrizze, hogy megjelenik-e a **SampleTable**nevű adatbázis.

   ![A minta táblázat ellenőrzése](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table.png "Minta tábla ellenőrzése")

7. Futtasson egy választó lekérdezést a tábla tartalmának ellenőrzéséhez. A táblának ugyanazzal az adattal kell rendelkeznie, mint a **renamedColumnsDF** dataframe.

    ![A minta táblázat tartalmának ellenőrzése](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table-content.png "A minta táblázat tartalmának ellenőrzése")

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag befejezése után leállíthatja a fürtöt. A Azure Databricks munkaterületen kattintson a bal oldali **fürtök** elemre. A fürt megszakításához a **műveletek**területen mutasson a három pontra (...), és válassza a **Befejezés** ikont.

![Databricks-fürt leállítása](./media/databricks-extract-load-sql-data-warehouse/terminate-databricks-cluster.png "Databricks-fürt leállítása")

Ha nem állítja be manuálisan a fürtöt, az automatikusan leáll, ha a fürt létrehozásakor bejelölte **\_\_ perc inaktivitás után** jelölőnégyzetet. Ebben az esetben a fürt automatikusan leáll, ha a megadott ideig inaktív volt.

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Azure Databricks szolgáltatás létrehozása
> * Spark-fürt létrehozása az Azure Databricksben
> * Jegyzetfüzet létrehozása az Azure Databricksben
> * Adatok kinyerése egy Data Lake Storage Gen2-fiókból
> * Adatok átalakítása az Azure Databricksben
> * Adatok betöltése az Azure SQL Data Warehouse-ba

Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan streamelhetők valós időben az adatok az Azure Databricksbe az Azure Event Hubs használatával.

> [!div class="nextstepaction"]
>[Adatok streamelése az Azure Databricksbe az Event Hubs használatával](databricks-stream-from-eventhubs.md)
