---
title: Oktatóanyag – ETL-műveletek végrehajtása az Azure Databricks használatával
description: Ebben az oktatóanyagban megtudhatja, hogyan nyerheti ki az adatokat a Data Lake Storage Gen2-ből az Azure Databricks-be, hogyan alakíthatja át az adatokat, majd töltheti be az adatokat az Azure SQL Data Warehouse-ba.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.date: 01/29/2020
ms.openlocfilehash: 8819b79a105b7a654a34e47c5ba9b3d351a1d926
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239410"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Oktatóanyag: Adatok kinyerése, átalakítása és betöltése az Azure Databricks használatával

Ebben az oktatóanyagban egy ETL (kibontás, átalakítás és adatok betöltése) műveletet hajt végre az Azure Databricks használatával. Az Azure Data Lake Storage Gen2-ből azure Databricks-be bontja ki az adatokat, átalakításokat futtat az Azure Databricks-ben, és betölti az átalakított adatokat az Azure SQL Data Warehouse-ba.

A jelen oktatóanyagban szereplő lépések az Azure Databricks SQL Data Warehouse-összekötőjét használják az adatok Azure Databricksbe való átviteléhez. Az összekötő ezután az Azure Blob Storage-ot használja ideiglenes tárolóként az Azure Databricks-fürt és az Azure SQL Data Warehouse között átvitt adatokhoz.

Az alábbi ábrán az alkalmazásfolyam látható:

![Azure Databricks a Data Lake Store és az SQL Data Warehouse](./media/databricks-extract-load-sql-data-warehouse/databricks-extract-transform-load-sql-datawarehouse.png "Azure Databricks a Data Lake Store és az SQL Data Warehouse")

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Hozzon létre egy Azure Databricks-szolgáltatást.
> * Hozzon létre egy Spark-fürtaz Azure Databricks.
> * Hozzon létre egy fájlrendszert a Data Lake Storage Gen2 fiókban.
> * Mintaadatok feltöltése az Azure Data Lake Storage Gen2 fiókba.
> * Hozzon létre egy egyszerű szolgáltatás.
> * Adatok kinyerése az Azure Data Lake Storage Gen2 fiókból.
> * Adatok átalakítása az Azure Databricks-ben.
> * Adatok betöltése az Azure SQL Data Warehouse ba.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

> [!Note]
> Ez az oktatóanyag nem hajtható végre **az Azure ingyenes próba-előfizetésével.**
> Ha van ingyenes fiókja, nyissa meg a profilját, és módosítsa az előfizetését **a felosztó-kirovó szolgáltatásra.** További információkért lásd az [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) ismertető cikket. Ezután [távolítsa el a költségkeretet,](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)és kérjen [kvótanövelést](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) a régióban a vCPU-khoz. Az Azure Databricks-munkaterület létrehozásakor kiválaszthatja a **próbaverziós (prémium – 14 napos ingyenes dbári)** díjszabási szintet, hogy a munkaterület 14 napig hozzáférést biztosítson az ingyenes prémium szintű Azure Databricks KBB-khoz.
     
## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt végezze el az alábbi feladatokat:

* Hozzon létre egy Azure SQL-adatraktárt, hozzon létre egy kiszolgálószintű tűzfalszabályt, és csatlakozzon a kiszolgálóhoz kiszolgálói rendszergazdaként. Lásd: [Rövid útmutató: Azure SQL-adattárház létrehozása és lekérdezése az Azure Portalon.](../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md)

* Hozzon létre egy főkulcsot az Azure SQL-adattárházhoz. Lásd: [Adatbázis-főkulcs létrehozása](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).

* Hozzon létre egy Azure Blob Storage-fiókot, benne egy tárolóval. A hozzáférési kulcsot is kérje le a tárfiók eléréséhez. Lásd: [Gyorsútmutató: Blobok feltöltése, letöltése és listázása az Azure Portalon.](../storage/blobs/storage-quickstart-blobs-portal.md)

* Hozzon létre egy Azure Data Lake Storage Gen2 tárfiókot. Lásd: [Rövid útmutató: Hozzon létre egy Azure Data Lake Storage Gen2 tárfiókot.](../storage/blobs/data-lake-storage-quickstart-create-account.md)

* Hozzon létre egy egyszerű szolgáltatás. [Lásd: Hogyan: A portál használatával hozzon létre egy Azure AD-alkalmazást és egyszerű szolgáltatást, amely képes hozzáférni az erőforrásokhoz.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

   Van néhány konkrét dolog, amit meg kell tennie, ahogy végrehajtja a cikkben leírt lépéseket.

   * Az [alkalmazás hozzárendelése a](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) cikk szerepkörszakaszához című lépések végrehajtásakor rendelje hozzá a **Storage Blob Data Contributor** szerepkört a Data Lake Storage Gen2 fiók hatókörében lévő egyszerű szolgáltatáshoz. Ha hozzárendeli a szerepkört a fölérendelt erőforráscsoporthoz vagy -előfizetéshez, engedélyekkel kapcsolatos hibákat fog kapni, amíg ezek a szerepkör-hozzárendelések propagálnak a tárfiókba.

      Ha egy hozzáférés-vezérlési listát (ACL) szeretne használni a szolgáltatásnév egy adott fájlhoz vagy könyvtárhoz társítására, hivatkozzon [az Azure Data Lake Storage Gen2 hozzáférés-vezérlésére.](../storage/blobs/data-lake-storage-access-control.md)

   * Amikor végrehajtja a cikk [bejelentkezési értékeinek bekerülési értékeit,](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) illessze be a bérlői azonosítót, az alkalmazásazonosítót és a titkos értékeket egy szöveges fájlba. Hamarosan szükséged lesz rá.

* Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

## <a name="gather-the-information-that-you-need"></a>Gyűjtse össze a szükséges információkat

Győződjön meg arról, hogy az oktatóanyag előfeltételeit befejezte.

   Mielőtt elkezdené, rendelkeznie kell az alábbi információkkal:

   :heavy_check_mark: Az adatbázis neve, adatbázis-kiszolgáló neve, felhasználóneve és az Azure SQL Data-raktár jelszava.

   :heavy_check_mark: A blob storage-fiók hozzáférési kulcsa.

   :heavy_check_mark: A Data Lake Storage Gen2 tárfiók neve.

   :heavy_check_mark: Az előfizetés bérlői azonosítója.

   :heavy_check_mark: Az Azure Active Directoryban (Azure AD) regisztrált alkalmazásazonosítója.

   :heavy_check_mark: Az Azure AD-vel regisztrált alkalmazás hitelesítési kulcsa.

## <a name="create-an-azure-databricks-service"></a>Azure Databricks-szolgáltatás létrehozása

Ebben a szakaszban hozzon létre egy Azure Databricks-szolgáltatást az Azure Portal használatával.

1. Az Azure Portal menüjében válassza az **Erőforrás létrehozása** elemet.

    ![Erőforrás létrehozása az Azure Portalon](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-on-portal.png)

    Ezután válassza **az Analytics** > **Azure Databricks lehetőséget.**

    ![Azure Databricks létrehozása az Azure Portalon](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-resource-create.png)



2. Az **Azure Databricks Service alatt**adja meg a következő értékeket a Databricks szolgáltatás létrehozásához:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |**Munkaterület neve**     | Adja meg a Databricks-munkaterület nevét.        |
    |**Előfizetés**     | Válassza ki a legördülő menüből a saját Azure-előfizetését.        |
    |**Erőforráscsoport**     | Adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne használni. Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. További információért olvassa el az [Azure-erőforráscsoportok áttekintését](../azure-resource-manager/management/overview.md). |
    |**Helyen**     | Válassza az **USA 2. nyugati régióját**.  A további elérhető régiókért tekintse meg az [elérhető Azure-szolgáltatások régiók szerinti bontását](https://azure.microsoft.com/regions/services/).      |
    |**Árképzési szint**     |  Válassza a **Normál**lehetőséget.     |

3. A fiók létrehozása eltarthat néhány percig. A művelet állapotának figyeléséhez tekintse meg a folyamatjelző sávot a tetején.

4. Válassza a **Rögzítés az irányítópulton**, majd a **Létrehozás** lehetőséget.

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Spark-fürt létrehozása az Azure Databricksben

1. Az Azure Portalon nyissa meg a létrehozott Databricks szolgáltatást, és válassza **a Munkaterület indítása**lehetőséget.

2. A rendszer átirányítja az Azure Databricks-portálra. A portálon válassza a **Fürt** elemet.

    ![Databricks az Azure-ban](./media/databricks-extract-load-sql-data-warehouse/databricks-on-azure.png "Databricks az Azure-ban")

3. Az **Új fürt** lapon adja meg a fürt létrehozásához szükséges értékeket.

    ![Databricks Spark-fürt létrehozása az Azure-ban](./media/databricks-extract-load-sql-data-warehouse/create-databricks-spark-cluster.png "Databricks Spark-fürt létrehozása az Azure-ban")

4. Adjon meg értékeket a következő mezőkben, és fogadja el az alapértelmezett értékeket a többi mezőben:

    * Adjon egy nevet a fürtnek.

    * Győződjön meg arról, hogy bejelöli a **Percek utáni inaktivitás után \_ \_ ** jelölőnégyzetet. Ha a fürt nincs használatban, adjon meg egy időtartamot (percben) a fürt leállításához.

    * Válassza a **Fürt létrehozása** lehetőséget. A fürt futása után csatlakoztathatja a jegyzetfüzeteket a fürthöz, és spark-feladatok futtatásához.

## <a name="create-a-file-system-in-the-azure-data-lake-storage-gen2-account"></a>Fájlrendszer létrehozása az Azure Data Lake Storage Gen2 fiókban

Ebben a szakaszban hozzon létre egy jegyzetfüzetet az Azure Databricks-munkaterületen, majd kódrészleteket futtatjon a tárfiók konfigurálásához.

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a létrehozott Azure Databricks szolgáltatást, és válassza **a Munkaterület indítása**lehetőséget.

2. A bal oldalon válassza a **Munkaterület**lehetőséget. A **Munkaterület** legördülő menüből válassza a **Létrehozás** > **Jegyzetfüzet** lehetőséget.

    ![Jegyzetfüzet létrehozása a Databricks-ben](./media/databricks-extract-load-sql-data-warehouse/databricks-create-notebook.png "Jegyzetfüzet létrehozása a Databricks-ben")

3. A **Jegyzetfüzet létrehozása** párbeszédpanelen adja meg a jegyzetfüzet nevét. Válassza a **Scala** nyelvet, majd válassza ki a korábban létrehozott Spark-fürtöt.

    ![Jegyzetfüzet részleteinek biztosítása a Databricks-ben](./media/databricks-extract-load-sql-data-warehouse/databricks-notebook-details.png "Jegyzetfüzet részleteinek biztosítása a Databricks-ben")

4. Kattintson a **Létrehozás** gombra.

5. A következő kódblokk a Spark-munkamenetben elért bármely ADLS Gen 2-fiók alapértelmezett egyszerű szolgáltatáshitelesítő adatait állítja be. A második kódblokk hozzáfűzi a fiók nevét a beállításhoz, hogy megadja egy adott ADLS Gen 2 fiók hitelesítő adatait.  Másolja és illessze be bármelyik kódblokkot az Azure Databricks-jegyzetfüzet első cellájába.

   **Munkamenet-konfiguráció**

   ```scala
   val appID = "<appID>"
   val secret = "<secret>"
   val tenantID = "<tenant-id>"

   spark.conf.set("fs.azure.account.auth.type", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id", "<appID>")
   spark.conf.set("fs.azure.account.oauth2.client.secret", "<secret>")
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

6. Ebben a kódblokkban `<app-id>` `<secret>`cserélje `<tenant-id>`le `<storage-account-name>` a , , , és helyőrző értékeket ebben a kódblokkban az oktatóanyag előfeltételeinek végrehajtása során gyűjtött értékekre. Cserélje `<file-system-name>` le a helyőrző értéket a fájlrendszernek adni kívánt névre.

   * A `<app-id>`, `<secret>` és az alkalmazásból származik, amelyet az active directoryval regisztrált egy egyszerű szolgáltatás létrehozásának részeként.

   * Az `<tenant-id>` az előfizetéséből származik.

   * Az `<storage-account-name>` Azure Data Lake Storage Gen2 tárfiók neve.

7. A **BLOKKban** lévő kód futtatásához nyomja le a SHIFT + ENTER billentyűket.

## <a name="ingest-sample-data-into-the-azure-data-lake-storage-gen2-account"></a>Mintaadatok betöltése az Azure Data Lake Storage Gen2 fiókba

Mielőtt ehhez a szakaszhoz hozzáfogna, a következő előfeltételeknek kell eleget tennie:

Írja be az alábbi kódot egy jegyzetfüzetcellába:

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

A cellában nyomja le a **SHIFT + ENTER billentyűkombinációt** a kód futtatásához.

Most egy új cellában írja be a következő kódot, és cserélje le a zárójelben megjelenő értékeket a korábban használt értékekkel:

    dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://" + fileSystemName + "@" + storageAccountName + ".dfs.core.windows.net/")

A cellában nyomja le a **SHIFT + ENTER billentyűkombinációt** a kód futtatásához.

## <a name="extract-data-from-the-azure-data-lake-storage-gen2-account"></a>Adatok kinyerése az Azure Data Lake Storage Gen2 fiókból

1. Most már betöltheti a minta json fájlt adatkeretként az Azure Databricks-ben. Illessze be a következő kódot egy új cellába. Cserélje le a zárójelben látható helyőrzőket az értékekre.

   ```scala
   val df = spark.read.json("abfss://" + fileSystemName + "@" + storageAccountName + ".dfs.core.windows.net/small_radio_json.json")
   ```
2. A **BLOKKban** lévő kód futtatásához nyomja le a SHIFT + ENTER billentyűket.

3. Futtassa a következő kódot az adatkeret tartalmának megtekintéséhez:

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

A nyers mintaadatok **small_radio_json.json** fájl rögzíti a közönséget egy rádióállomás, és a különböző oszlopok. Ebben a szakaszban úgy alakítja át az adatokat, hogy csak bizonyos oszlopokat olvassanak be az adatkészletből.

1. Először csak a létrehozott adatkeretből olvassa be a **keresztnév**, **a vezetéknév**, a **nem,** a **hely**és a **szint** oszlopokat.

   ```scala
   val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
   specificColumnsDf.show()
   ```

   Kimenet et kap, ahogy az a következő kódrészletben látható:

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

   Kimenet et kap, ahogy az a következő kódrészletben látható.

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

Ebben a szakaszban feltölti az átalakított adatokat az Azure SQL Data Warehouse-ba. Az Azure Databricks Azure SQL DataWarehouse-összekötőhasználatával közvetlenül feltöltheti az adatkeretet egy SQL-adattárházban lévő táblaként.

Ahogy korábban említettük, az SQL Data Warehouse-összekötő az Azure Blob storage-ot használja ideiglenes tárolóként az Azure Databricks és az Azure SQL Data Warehouse közötti adatok feltöltéséhez. Ezért első lépésként adja meg a tárfiókhoz való csatlakozáshoz szükséges konfigurációt. A cikk előfeltételeinek részeként már létre kell hoznia a fiókot.

1. Adja meg az Azure Storage-fiók Azure Databricksből való eléréséhez szükséges konfigurációt.

   ```scala
   val blobStorage = "<blob-storage-account-name>.blob.core.windows.net"
   val blobContainer = "<blob-container-name>"
   val blobAccessKey =  "<access-key>"
   ```

2. Adjon meg egy ideiglenes mappát, amelyet az Azure Databricks és az Azure SQL Data Warehouse közötti adatok áthelyezése közben használhat.

   ```scala
   val tempDir = "wasbs://" + blobContainer + "@" + blobStorage +"/tempDirs"
   ```

3. Futtassa az alábbi kódrészletet az Azure Blob Storage hozzáférési kulcsainak a konfigurációban való tárolásához. Ez a művelet biztosítja, hogy a hozzáférési kulcsot ne kelljen a jegyzetfüzetben egyszerű szövegként tartania.

   ```scala
   val acntInfo = "fs.azure.account.key."+ blobStorage
   sc.hadoopConfiguration.set(acntInfo, blobAccessKey)
   ```

4. Adja meg az Azure SQL Data Warehouse-példányhoz való csatlakozáshoz szükséges értékeket. Előfeltételként létre kell hoznia egy SQL-adatraktárt. Használja a **dwServer**teljesen minősített kiszolgálónevét. Például: `<servername>.database.windows.net`.

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

5. Futtassa a következő kódrészletet az átalakított **adatkeret (ColumnsDF**) sql adatraktárban lévő táblaként való betöltéséhez. Ez a kódrészlet létrehoz egy **SampleTable** nevű táblát az SQL-adatbázisban.

   ```scala
   spark.conf.set(
       "spark.sql.parquet.writeLegacyFormat",
       "true")

   renamedColumnsDF.write.format("com.databricks.spark.sqldw").option("url", sqlDwUrlSmall).option("dbtable", "SampleTable")       .option( "forward_spark_azure_storage_credentials","True").option("tempdir", tempDir).mode("overwrite").save()
   ```

   > [!NOTE]
   > Ez a `forward_spark_azure_storage_credentials` minta a jelzőt használja, amely nek köszönhetően az SQL Data Warehouse access key használatával fér hozzá a blobstorage-ból származó adatokhoz. Ez az egyetlen támogatott hitelesítési módszer.
   >
   > Ha az Azure Blob Storage csak virtuális hálózatok kiválasztására van korlátozva, az SQL Data Warehouse [access keys helyett felügyelt szolgáltatásidentitást](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)igényel. Ez a következő hibaüzenetet okozza: "Ez a kérés nem jogosult a művelet végrehajtására."

6. Csatlakozzon az SQL-adatbázishoz, és ellenőrizze, hogy **megjelenik-e**a SampleTable nevű adatbázis.

   ![A mintatábla ellenőrzése](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table.png "Mintatábla ellenőrzése")

7. Futtasson egy választó lekérdezést a tábla tartalmának ellenőrzéséhez. A táblának ugyanazoknak az adatoknak kell lennie, mint az **átnevezettColumnsDF** adatkeretnek.

    ![A mintatábla tartalmának ellenőrzése](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table-content.png "A mintatábla tartalmának ellenőrzése")

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután befejezte az oktatóanyagot, lemondhatja a fürtöt. Az Azure Databricks munkaterületen válassza **a fürtök** a bal oldalon. A fürt leállításához a **Műveletek**csoportban mutasson a három pontra (...), és kattintson a **Leállítás** ikonra.

![Databricks-fürt leállítása](./media/databricks-extract-load-sql-data-warehouse/terminate-databricks-cluster.png "Databricks-fürt leállítása")

Ha nem állítja le manuálisan a fürtöt, az automatikusan leáll, feltéve, hogy a fürt létrehozásakor bejelölte a **Percek inaktivitás \_ \_ után beálló** gombra jelölőnégyzetet. Ebben az esetben a fürt automatikusan leáll, ha a megadott ideig inaktív volt.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Azure Databricks-szolgáltatás létrehozása
> * Spark-fürt létrehozása az Azure Databricksben
> * Jegyzetfüzet létrehozása az Azure Databricksben
> * Adatok kinyerése Data Lake Storage Gen2 fiókból
> * Adatok átalakítása az Azure Databricksben
> * Adatok betöltése az Azure SQL Data Warehouse-ba

Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan streamelhetők valós időben az adatok az Azure Databricksbe az Azure Event Hubs használatával.

> [!div class="nextstepaction"]
>[Adatok streamelése az Azure Databricksbe az Event Hubs használatával](databricks-stream-from-eventhubs.md)
