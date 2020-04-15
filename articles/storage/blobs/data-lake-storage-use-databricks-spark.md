---
title: 'Oktatóanyag: Azure Data Lake Storage Gen2, Azure Databricks & Spark | Microsoft dokumentumok'
description: Ez az oktatóanyag bemutatja, hogyan futtathat Spark-lekérdezéseket egy Azure Databricks-fürtön az Azure Data Lake Storage Gen2 tárfiókban lévő adatok eléréséhez.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 5889afa033b30606f8981ddb826aa192f24efa10
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312916"
---
# <a name="tutorial-azure-data-lake-storage-gen2-azure-databricks--spark"></a>Oktatóanyag: Azure Data Lake Storage Gen2, Azure Databricks & Spark

Ez az oktatóanyag bemutatja, hogyan kapcsolhatja össze az Azure Databricks-fürtöt egy Olyan Azure storage-fiókban tárolt adatokkal, amelyen engedélyezve van az Azure Data Lake Storage Gen2. Ez a kapcsolat lehetővé teszi, hogy natív módon futtasson lekérdezéseket és elemzéseket a fürtből az adatokon.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Databricks-fürt létrehozása
> * Strukturálatlan adatok betöltése egy tárfiókba
> * Elemzés futtatása az adatokon a Blob storage-ban

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy Azure Data Lake Storage Gen2-fiókot.

  Lásd: [Azure Data Lake Storage Gen2 fiók létrehozása.](data-lake-storage-quickstart-create-account.md)

* Győződjön meg arról, hogy a felhasználói fiók rendelkezik a [Storage Blob Data Contributor szerepkör](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) hozzárendelve.

* Telepítse az AzCopy v10-et. Lásd: [Adatátvitel az AzCopy v10-el](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

* Hozzon létre egy egyszerű szolgáltatás. [Lásd: Hogyan: A portál használatával hozzon létre egy Azure AD-alkalmazást és egyszerű szolgáltatást, amely képes hozzáférni az erőforrásokhoz.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

  Van néhány konkrét dolog, amit meg kell tennie, ahogy végrehajtja a cikkben leírt lépéseket.

  :heavy_check_mark: Az [alkalmazás hozzárendelése a](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) cikk szerepkörhöz szakaszában leírt lépések végrehajtásakor győződjön meg arról, hogy a Storage Blob Data **Contributor** szerepkört hozzárendeli az egyszerű szolgáltatáshoz.

  > [!IMPORTANT]
  > Győződjön meg arról, hogy rendelje hozzá a szerepkört a Data Lake Storage Gen2 tárfiók hatókörében. Szerepkört rendelhet a fölérendelt erőforráscsoporthoz vagy -előfizetéshez, de engedélyekkel kapcsolatos hibákat fog kapni, amíg ezek a szerepkör-hozzárendelések propagálnak a tárfiókba.

  :heavy_check_mark: A cikk bejelentkezési [értékeinek bekerülési értékeinek](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) végrehajtásakor illessze be a bérlői azonosítót, az alkalmazásazonosítót és az ügyféltitkos értékeket egy szöveges fájlba. Hamarosan szükséged lesz rá.

### <a name="download-the-flight-data"></a>A repülőjárat-adatok letöltése

Ez az oktatóanyag a Közlekedési Statisztikai Hivatal repülési adatait használja az ETL-műveletek végrehajtásának bemutatására. Az oktatóanyag befejezéséhez le kell töltenie ezeket az adatokat.

1. Tovább a [Kutatási és Innovatív Technológiai Adminisztráció, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Jelölje be a **Prezipped File** jelölőnégyzetet az összes adatmező kijelöléséhez.

3. Válassza a **Letöltés gombot,** és mentse az eredményeket a számítógépre. 

4. Csomagolja ki a tömörített fájl tartalmát, és jegyezze fel a fájl nevét és elérési útját. Szüksége van erre az információra egy későbbi lépésben.

## <a name="create-an-azure-databricks-service"></a>Azure Databricks-szolgáltatás létrehozása

Ebben a szakaszban hozzon létre egy Azure Databricks-szolgáltatást az Azure Portal használatával.

1. Az Azure Portalon válassza az**Analytics** > Erőforrás > létrehozása**Azure Databricks** **lehetőséget.**

    ![Databricks az Azure Portalon](./media/data-lake-storage-use-databricks-spark/azure-databricks-on-portal.png "Databricks az Azure Portalon")

2. Az **Azure Databricks Service alatt**adja meg a következő értékeket a Databricks szolgáltatás létrehozásához:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |**Munkaterület neve**     | Adja meg a Databricks-munkaterület nevét.  |
    |**Előfizetés**     | Válassza ki a legördülő menüből a saját Azure-előfizetését.        |
    |**Erőforráscsoport**     | Adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne használni. Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. További információért olvassa el az [Azure-erőforráscsoportok áttekintését](../../azure-resource-manager/management/overview.md). |
    |**Helyen**     | Válassza az **USA 2. nyugati régióját**. A további elérhető régiókért tekintse meg az [elérhető Azure-szolgáltatások régiók szerinti bontását](https://azure.microsoft.com/regions/services/).       |
    |**Árképzési szint**     |  Válassza a **Normál**lehetőséget.     |

    ![Azure Databricks-munkaterület létrehozása](./media/data-lake-storage-use-databricks-spark/create-databricks-workspace.png "Azure Databricks-szolgáltatás létrehozása")

3. A fiók létrehozása eltarthat néhány percig. A művelet állapotának figyeléséhez tekintse meg a folyamatjelző sávot a tetején.

4. Válassza a **Rögzítés az irányítópulton**, majd a **Létrehozás** lehetőséget.

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Spark-fürt létrehozása az Azure Databricksben

1. Az Azure Portalon nyissa meg a létrehozott Databricks szolgáltatást, és válassza **a Munkaterület indítása**lehetőséget.

2. A rendszer átirányítja az Azure Databricks-portálra. A portálon válassza a **Fürt** elemet.

    ![Databricks az Azure-ban](./media/data-lake-storage-use-databricks-spark/databricks-on-azure.png "Databricks az Azure-ban")

3. Az **Új fürt** lapon adja meg a fürt létrehozásához szükséges értékeket.

    ![Databricks Spark-fürt létrehozása az Azure-ban](./media/data-lake-storage-use-databricks-spark/create-databricks-spark-cluster.png "Databricks Spark-fürt létrehozása az Azure-ban")

    Adjon meg értékeket a következő mezőkben, és fogadja el az alapértelmezett értékeket a többi mezőben:

    - Adjon egy nevet a fürtnek.
     
    - Mindenképpen jelölje be a **Leállítás 120 percnyi tétlenség után** jelölőnégyzetet. Adja meg az időtartamot (percben), amelynek elteltével le kell állítani a fürtöt, amennyiben az használaton kívül van.

4. Válassza a **Fürt létrehozása** lehetőséget. A fürt futása után csatlakoztathatja a jegyzetfüzeteket a fürthöz, és spark-feladatok futtatásához.

## <a name="ingest-data"></a>Adatok betöltése

### <a name="copy-source-data-into-the-storage-account"></a>Forrásadatok másolása a tárfiókba

Az AzCopy segítségével adatokat másolhat a *.csv* fájlból a Data Lake Storage Gen2 fiókjába.

1. Nyisson meg egy parancssori ablakot, és írja be a következő parancsot a tárfiókba való bejelentkezéshez.

   ```bash
   azcopy login
   ```

   A felhasználói fiók hitelesítéséhez kövesse a parancssorablakban megjelenő utasításokat.

2. Ha adatokat szeretne másolni a *.csv* fiókból, írja be a következő parancsot.

   ```bash
   azcopy cp "<csv-folder-path>" https://<storage-account-name>.dfs.core.windows.net/<container-name>/folder1/On_Time.csv
   ```

   * Cserélje `<csv-folder-path>` le a helyőrző értéket a *.csv* fájl elérési parancsára.

   * Cserélje `<storage-account-name>` le a helyőrző értéket a tárfiók nevére.

   * Cserélje `<container-name>` le a helyőrzőt egy tároló nevére a tárfiókban.

## <a name="create-a-container-and-mount-it"></a>Hozzon létre egy tárolót, és csatolja

Ebben a szakaszban hozzon létre egy tárolót és egy mappát a tárfiókban.

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a létrehozott Azure Databricks szolgáltatást, és válassza **a Munkaterület indítása**lehetőséget.

2. A bal oldalon válassza a **Munkaterület**lehetőséget. A **Munkaterület** legördülő menüből válassza a **Létrehozás** > **Jegyzetfüzet** lehetőséget.

    ![Jegyzetfüzet létrehozása a Databricks-ben](./media/data-lake-storage-use-databricks-spark/databricks-create-notebook.png "Jegyzetfüzet létrehozása a Databricks-ben")

3. A **Jegyzetfüzet létrehozása** párbeszédpanelen adja meg a jegyzetfüzet nevét. Válassza ki a **Python** nyelvet, majd válassza ki a korábban létrehozott Spark-fürtöt.

4. Kattintson a **Létrehozás** gombra.

5. Másolja és illessze be a következő kódblokkot az első cellába, de még ne futtassa ezt a kódot.

    ```Python
    configs = {"fs.azure.account.auth.type": "OAuth",
           "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
           "fs.azure.account.oauth2.client.id": "<appId>",
           "fs.azure.account.oauth2.client.secret": "<clientSecret>",
           "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant>/oauth2/token",
           "fs.azure.createRemoteFileSystemDuringInitialization": "true"}

    dbutils.fs.mount(
    source = "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/folder1",
    mount_point = "/mnt/flightdata",
    extra_configs = configs)
    ```

18. Ebben a kódblokkban `appId` `clientSecret`cserélje `tenant`le `storage-account-name` a , , , és helyőrző értékeket ebben a kódblokkban az oktatóanyag előfeltételeinek végrehajtása során gyűjtött értékekre. Cserélje `container-name` le a helyőrző értéket a tároló nevére.

19. A **BLOKKban** lévő kód futtatásához nyomja le a SHIFT + ENTER billentyűket.

   Tartsa nyitva ezt a jegyzetfüzetet, ahogy később parancsokat ad hozzá.

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>CSV konvertálása parquetté a Databricks-jegyzetfüzet használatával

A korábban létrehozott jegyzetfüzetbe vegyen fel egy új cellát, és illessze be a következő kódot a cellába. 

```python
# Use the previously established DBFS mount point to read the data.
# create a data frame to read data.

flightDF = spark.read.format('csv').options(
    header='true', inferschema='true').load("/mnt/flightdata/*.csv")

# read the airline csv file and write the output to parquet format for easy query.
flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
print("Done")
```

## <a name="explore-data"></a>Adatok megismerése

Egy új cellába illessze be a következő kódot az AzCopy-on keresztül feltöltött CSV-fájlok listájának beillesztéséhez.

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata"))
```

Új fájl létrehozásához és fájlok listázásához a *parquet/flights* mappában futtassa ezt a szkriptet:

```python
dbutils.fs.put("/mnt/flightdata/1.txt", "Hello, World!", True)
dbutils.fs.ls("/mnt/flightdata/parquet/flights")
```

Ezekkel a kódmintákkal megismerte a HDFS hierarchikus jellegét a Data Lake Storage Gen2-kompatiblis tárfiókban tárolt adatok használatával.

## <a name="query-the-data"></a>Adatok lekérdezése

Következő lépésként megkezdheti a tárfiókba feltöltött adatok lekérdezését. Írja be a következő kódblokkok mindegyikét a **Cmd 1** területre, majd nyomja le a **Cmd + ENTER** billentyűkombinációt a Python-szkript futtatásához.

Ha adatkereteket szeretne létrehozni az adatforrásokhoz, futtassa a következő parancsfájlt:

* Cserélje `<csv-folder-path>` le a helyőrző értéket a *.csv* fájl elérési parancsára.

```python
# Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(
    header='true', inferschema='true').load("/mnt/flightdata/On_Time.csv")
acDF.write.parquet('/mnt/flightdata/parquet/airlinecodes')

# read the existing parquet file for the flights database that was created earlier
flightDF = spark.read.format('parquet').options(
    header='true', inferschema='true').load("/mnt/flightdata/parquet/flights")

# print the schema of the dataframes
acDF.printSchema()
flightDF.printSchema()

# print the flight database size
print("Number of flights in the database: ", flightDF.count())

# show the first 20 rows (20 is the default)
# to show the first n rows, run: df.show(n)
acDF.show(100, False)
flightDF.show(20, False)

# Display to run visualizations
# preferably run this in a separate cmd cell
display(flightDF)
```

Adja meg ezt a parancsfájlt néhány alapvető elemzési lekérdezés futtatásához az adatokon.

```python
# Run each of these queries, preferably in a separate cmd cell for separate analysis
# create a temporary sql view for querying flight information
FlightTable = spark.read.parquet('/mnt/flightdata/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

# create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet('/mnt/flightdata/parquet/airlinecodes')
AirlineCodes.createOrReplaceTempView('AirlineCodes')

# using spark sql, query the parquet file to return total flights in January and February 2016
out1 = spark.sql("SELECT * FROM FlightTable WHERE Month=1 and Year= 2016")
NumJan2016Flights = out1.count()
out2 = spark.sql("SELECT * FROM FlightTable WHERE Month=2 and Year= 2016")
NumFeb2016Flights = out2.count()
print("Jan 2016: ", NumJan2016Flights, " Feb 2016: ", NumFeb2016Flights)
Total = NumJan2016Flights+NumFeb2016Flights
print("Total flights combined: ", Total)

# List out all the airports in Texas
out = spark.sql(
    "SELECT distinct(OriginCityName) FROM FlightTable where OriginStateName = 'Texas'")
print('Airports in Texas: ', out.show(100))

# find all airlines that fly from Texas
out1 = spark.sql(
    "SELECT distinct(Reporting_Airline) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölje az erőforráscsoportot és az összes kapcsolódó erőforrást. Ehhez jelölje ki a tárfiók erőforráscsoportját, és válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Adatok kinyerése, átalakítása és betöltése az Azure HDInsight-alapú Apache Hive használatával](data-lake-storage-tutorial-extract-transform-load-hive.md)
