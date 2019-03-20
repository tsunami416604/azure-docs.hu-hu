---
title: 'Oktatóanyag: Az Azure Data Lake Storage Gen2 adatok eléréséhez az Azure Databricks Spark használatával |} A Microsoft Docs'
description: Ez az oktatóanyag bemutatja, hogyan futtathat Spark lekérdezéseket egy Azure Databricks-fürtön az Azure Data Lake Storage Gen2 tárfiókban lévő adatok eléréséhez.
services: storage
author: dineshmurthy
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: dineshm
ms.openlocfilehash: 7f712bcf3e82005480d4960484cb0ea3ad51fbff
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226759"
---
# <a name="tutorial-access-data-lake-storage-gen2-data-with-azure-databricks-using-spark"></a>Oktatóanyag: Data Lake Storage Gen2-adatok elérhetők az Azure Databricks Spark használatával

Ez az oktatóanyag bemutatja, hogyan csatlakozhat egy Azure storage-fiók, amely rendelkezik az Azure Data Lake Storage Gen2 engedélyezve van a tárolt adatok az Azure Databricks-fürt. Ez a kapcsolat lehetővé teszi a natív lekérdezések és futtathatók elemzések a fürtről, az adatok.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Databricks-fürt létrehozása
> * Strukturálatlan adatok betöltése egy tárfiókba
> * Elemzések futtatására az adatokon a Blob storage-ban

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy Azure Data Lake Storage Gen2-fiókot.

  Lásd: [hozzon létre egy Azure Data Lake Storage Gen2 fiókot](data-lake-storage-quickstart-create-account.md).

* Győződjön meg arról, hogy a felhasználói fiók rendelkezik-e a [Storage-Blobadatok Közreműködője szerepkör](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) rendelve.

* Az AzCopy v10 telepítse. Lásd: [adatok áthelyezése az AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

* Hozzon létre egy egyszerű szolgáltatást. Lásd: [hogyan: A portál használatával hozzon létre egy Azure AD alkalmazás és -szolgáltatásnév erőforrások eléréséhez](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

  Van néhány adott tudnivaló, mivel ebben a cikkben hajtsa végre a lépéseket kell.

  :heavy_check_mark: A lépések végrehajtásakor a [alkalmazások szerepkörhöz rendeléséhez](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) szakaszt a cikk, ügyeljen arra, hogy rendelje hozzá a **Storage-Blobadatok Közreműködője** szerepkört a szolgáltatásnévhez.

  > [!IMPORTANT]
  > Ellenőrizze, hogy a szerepkört a Data Lake Storage Gen2 storage-fiók hatókörében. Szerepkör hozzárendelése a szülő erőforráscsoportba vagy előfizetésbe, de kap engedélyekkel kapcsolatos hibákat addig, amíg azokat a szerepkör-hozzárendeléseket a tárfiókhoz való propagálása.

  :heavy_check_mark: A lépések végrehajtásakor a [értékek beolvasása bejelentkezés](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) szakaszában a cikk, illessze be a bérlő Azonosítóját, Alkalmazásazonosító és hitelesítési kulcs értékeit egy szövegfájlba. Kell azokat, hamarosan.

### <a name="download-the-flight-data"></a>A repülőjárat-adatok letöltése

Ebben az oktatóanyagban a bemutatják, hogyan végezhet ETL-műveletet, a szállítás Hivatala statisztikák repülési adatait használja. Ezeket az adatokat az oktatóanyag elvégzéséhez le kell töltenie.

1. Lépjen a [kutatásáról és az innovatív technológia felügyeleti, a szállítás statisztikák Hivatala](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Válassza ki a **Prezipped fájl** jelölőnégyzet bejelölésével kijelölheti az összes adatmezőket.

3. Válassza ki a **letöltése** gombra, és az eredmények mentése a számítógépre. 

4. Csomagolja ki a tömörített fájl tartalmát, és jegyezze fel a fájl nevét és a fájl elérési útját. Ez egy későbbi lépésben információra van szüksége.

## <a name="create-an-azure-databricks-service"></a>Hozzon létre egy Azure Databricks szolgáltatás

Ebben a szakaszban létrehoz egy Azure Databricks szolgáltatást az Azure portal használatával.

1. Az Azure Portalon válassza az **Erőforrás létrehozása** > **Elemzés** > **Azure Databricks** elemet.

    ![Databricks az Azure Portalon](./media/data-lake-storage-use-databricks-spark/azure-databricks-on-portal.png "Databricks az Azure Portalon")

2. A **Azure Databricks szolgáltatás**, adja meg a Databricks-szolgáltatás létrehozása a következő értékeket:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |**Munkaterület neve**     | Adja meg a Databricks-munkaterület nevét.  |
    |**Előfizetés**     | Válassza ki a legördülő menüből a saját Azure-előfizetését.        |
    |**Erőforráscsoport**     | Adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne használni. Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. További információért olvassa el az [Azure-erőforráscsoportok áttekintését](../../azure-resource-manager/resource-group-overview.md). |
    |**Hely**     | Válassza az **USA 2. nyugati régióját**. A további elérhető régiókért tekintse meg az [elérhető Azure-szolgáltatások régiók szerinti bontását](https://azure.microsoft.com/regions/services/).       |
    |**Tarifacsomag**     |  Válassza ki **Standard**.     |

    ![Az Azure Databricks-munkaterület létrehozása](./media/data-lake-storage-use-databricks-spark/create-databricks-workspace.png "hozzon létre egy Azure Databricks szolgáltatás")

3. Válassza a **Rögzítés az irányítópulton**, majd a **Létrehozás** lehetőséget.

4. A fiók létrehozása eltarthat néhány percig. Fiók létrehozása során a portál megjeleníti a **üzemelő példány elküldése az Azure Databricks** csempére a jobb oldalon. Műveleti állapotának figyelése, megtekintheti a folyamatjelző sáv tetején.

    ![Databricks üzembe helyezési csempe](./media/data-lake-storage-use-databricks-spark/databricks-deployment-tile.png "Databricks üzembe helyezési csempe")

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Spark-fürt létrehozása az Azure Databricksben

1. Az Azure Portalon nyissa meg a létrehozott Databricks-szolgáltatás, és válassza ki **munkaterület indítása**.

2. A program átirányítja az Azure Databricks portáljára. A portálon válassza a **Fürt** elemet.

    ![Databricks az Azure-on](./media/data-lake-storage-use-databricks-spark/databricks-on-azure.png "Databricks az Azure-on")

3. Az **Új fürt** lapon adja meg a fürt létrehozásához szükséges értékeket.

    ![Databricks Spark-fürt létrehozása az Azure-on](./media/data-lake-storage-use-databricks-spark/create-databricks-spark-cluster.png "Databricks Spark-fürt létrehozása az Azure-on")

4. Adjon meg értékeket a következő mezőkben, és fogadja el az alapértelmezett értékeket a többi mezőben:

    * Adjon egy nevet a fürtnek.

    * Ebben a cikkben hozzon létre egy fürtöt a **5.1** modul.

    * Mindenképpen jelölje ki a **leállítása után \_ \_ ennyi perc inaktivitás** jelölőnégyzetet. Ha a fürt nem használ, adja meg az időtartamot (percben) a fürt leállításához.

    * Válassza a **Fürt létrehozása** lehetőséget. Miután a fürt fut, notebookokat csatlakoztathat hozzá, a fürt, és a Spark-feladatok futtatása.

## <a name="create-a-file-system-and-mount-it"></a>Hozzon létre egy fájlrendszert és csatlakoztathatom azokat

Ebben a szakaszban fog létrehozni egy fájlrendszert és a egy mappát a storage-fiókban.

1. Az a [az Azure portal](https://portal.azure.com), keresse meg az Ön által létrehozott Azure Databricks szolgáltatást, és válassza ki **munkaterület indítása**.

2. A bal oldalon válassza ki a **munkaterület**. A **Munkaterület** legördülő menüből válassza a **Létrehozás** > **Jegyzetfüzet** lehetőséget.

    ![Hozzon létre egy notebookot a Databricksben](./media/data-lake-storage-use-databricks-spark/databricks-create-notebook.png "notebook létrehozása a Databricksben")

3. A **Jegyzetfüzet létrehozása** párbeszédpanelen adja meg a jegyzetfüzet nevét. Válassza ki **Python** , a nyelvet, és válassza a Spark-fürt, amely a korábban létrehozott.

4. Kattintson a **Létrehozás** gombra.

5. Másolja és illessze be az alábbi kódblokkot az első olyan cellára, de még ne futtassa ezt a kódot.

    ```Python
    configs = {"fs.azure.account.auth.type": "OAuth",
           "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
           "fs.azure.account.oauth2.client.id": "<application-id>",
           "fs.azure.account.oauth2.client.secret": "<authentication-id>",
           "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant-id>/oauth2/token",
           "fs.azure.createRemoteFileSystemDuringInitialization": "true"}

    dbutils.fs.mount(
    source = "abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/folder1",
    mount_point = "/mnt/flightdata",
    extra_configs = configs)
    ```

18. A kódblokk, cserélje le a `application-id`, `authentication-id`, `tenant-id`, és `storage-account-name` helyőrző értékeket az előfeltételeket a jelen oktatóanyag végrehajtása során gyűjtött értékek a kód blokk. Cserélje le a `file-system-name` nevét bármilyen, a helyőrző értékét szeretné adni a fájlrendszer.

   * A `application-id`, és `authentication-id` a regisztrált alkalmazást, és az active directory egyszerű szolgáltatás létrehozása során a rendszer.

   * A `tenant-id` az előfizetésből van.

   * A `storage-account-name` az Azure Data Lake Storage Gen2 storage-fiók neve.

   > [!NOTE]
   > Éles környezetben, fontolja meg, a hitelesítési kulcs tárolása az Azure Databricksben. Ezután adja hozzá egy keresse meg a kulcs a kódblokk, a hitelesítési kulcs helyett. Ez a rövid útmutató befejezése után tekintse meg a [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) cikk webhelyen az Azure Databricks erre a megközelítésre példa látható.

19. Nyomja le az **SHIFT + ENTER** kulcsok a kód futtatásához a blokk.

   Ez a jegyzetfüzet tartsa nyitva, mivel a hozzáadandó parancs azt később.

## <a name="ingest-data"></a>Adatok betöltése

### <a name="copy-source-data-into-the-storage-account"></a>Forrásadatok másolása a tárfiókba

Az AzCopy használata adatokat másolni a *.csv* fájlt a Data Lake Storage Gen2-fiókba.

1. Nyisson meg egy parancssori ablakot, és adja meg a következő parancsot a tárfiók-ba való bejelentkezéshez.

   ```bash
   azcopy login
   ```

   Kövesse az utasításokat a megjelenjen a parancssori ablakban, a felhasználói fiók hitelesítéséhez.

2. Adatokat másolni a *.csv* fiókra, adja meg a következő parancsot.

   ```bash
   azcopy cp "<csv-folder-path>" https://<storage-account-name>.dfs.core.windows.net/<file-system-name>/folder1/On_Time.csv
   ```

   * Cserélje le a `<csv-folder-path>` helyőrző értékét az elérési útját a *.csv* fájlt.

   * Cserélje le a `storage-account-name` helyőrző értéket cserélje a tárfiókja nevére.

   * Cserélje le a `file-system-name` bármilyen nevet kíván rendelni a fájlrendszer helyőrzőt.

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>CSV konvertálása parquetté a Databricks-jegyzetfüzet használatával

A korábban létrehozott jegyzetfüzet adjon hozzá egy új cellát, és illessze be a következő kódot a cella. 

```python
# Use the previously established DBFS mount point to read the data.
# create a data frame to read data.

flightDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/*.csv")

# read the airline csv file and write the output to parquet format for easy query.
flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
print("Done")
```

## <a name="explore-data"></a>Adatok megismerése

Egy új cellába illessze be a következő kódot az AzCopy segítségével feltöltött CSV-fájlok listájának lekérése.

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

Az adatforrások adatkeretek létrehozásához futtassa az alábbi parancsfájlt:

* Cserélje le a `<csv-folder-path>` helyőrző értékét az elérési útját a *.csv* fájlt.

```python
#Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/On_Time.csv")
acDF.write.parquet('/mnt/flightdata/parquet/airlinecodes')

#read the existing parquet file for the flights database that was created earlier
flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load("/mnt/flightdata/parquet/flights")

#print the schema of the dataframes
acDF.printSchema()
flightDF.printSchema()

#print the flight database size
print("Number of flights in the database: ", flightDF.count())

#show the first 20 rows (20 is the default)
#to show the first n rows, run: df.show(n)
acDF.show(100, False)
flightDF.show(20, False)

#Display to run visualizations
#preferably run this in a separate cmd cell
display(flightDF)
```

Adja meg a szkript néhány alapvető elemzési lekérdezések futtatásához az adatokat.

```python
#Run each of these queries, preferably in a separate cmd cell for separate analysis
#create a temporary sql view for querying flight information
FlightTable = spark.read.parquet('/mnt/flightdata/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

#create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet('/mnt/flightdata/parquet/airlinecodes')
AirlineCodes.createOrReplaceTempView('AirlineCodes')

#using spark sql, query the parquet file to return total flights in January and February 2016
out1 = spark.sql("SELECT * FROM FlightTable WHERE Month=1 and Year= 2016")
NumJan2016Flights = out1.count()
out2 = spark.sql("SELECT * FROM FlightTable WHERE Month=2 and Year= 2016")
NumFeb2016Flights=out2.count()
print("Jan 2016: ", NumJan2016Flights," Feb 2016: ",NumFeb2016Flights)
Total= NumJan2016Flights+NumFeb2016Flights
print("Total flights combined: ", Total)

# List out all the airports in Texas
out = spark.sql("SELECT distinct(OriginCityName) FROM FlightTable where OriginStateName = 'Texas'") 
print('Airports in Texas: ', out.show(100))

#find all airlines that fly from Texas
out1 = spark.sql("SELECT distinct(Reporting_Airline) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Amikor szükség van rájuk már nem, törölje az erőforráscsoportot és az összes kapcsolódó erőforrás. Ehhez válassza ki a storage-fiókhoz tartozó erőforráscsoportot, és válassza ki **törlése**.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Adatok kinyerése, átalakítása és betöltése az Azure HDInsight-alapú Apache Hive használatával](data-lake-storage-tutorial-extract-transform-load-hive.md)
