---
title: 'Oktatóanyag: Hozzáférés a 2. generációs Azure Data Lake Storage előzetes verziójának adataihoz az Azure Databricks és a Spark használatával | Microsoft Docs'
description: Ez az oktatóanyag bemutatja, hogyan futtathat Spark lekérdezéseket egy Azure Databricks-fürtön az Azure Data Lake Storage Gen2 tárfiókban lévő adatok eléréséhez.
services: storage
author: dineshmurthy
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 01/14/2019
ms.author: dineshm
ms.openlocfilehash: 31d18d7ea4ee195f7ffcfa04fb247b5dfd525c6a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251477"
---
# <a name="tutorial-access-data-lake-storage-gen2-preview-data-with-azure-databricks-using-spark"></a>Oktatóanyag: Data Lake Storage Gen2 előzetes verzió az adatok elérhetők az Azure Databricks Spark használatával

Az oktatóanyag bemutatja, hogyan csatlakozhat egy Azure storage-fiókot, amely rendelkezik az Azure Data Lake Storage Gen2-ban tárolt adatok az Azure Databricks-fürt (előzetes verzió) engedélyezve van. Ez a kapcsolat lehetővé teszi a natív lekérdezések és futtathatók elemzések a fürtről, az adatok.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Databricks-fürt létrehozása
> * Strukturálatlan adatok betöltése egy tárfiókba
> * Elemzés futtatása a blobtárolóban található adatokon

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag bemutatja, hogyan használhatja fel és kérdezheti le légitársaságok repülőjárat-adatait, amelyeket az [Egyesült Államok Közlekedési Minisztériuma](https://transtats.bts.gov/DL_SelectFields.asp) tett közzé. 

1. Válassza ki a **Prezipped fájl** jelölőnégyzet bejelölésével kijelölheti az összes adatmezőket.
2. Válassza ki **letöltése** és mentheti az eredményeket a gépre.
3. Jegyezze fel a fájl neve és elérési útját a letöltés; Ez egy későbbi lépésben információra van szüksége.

Ez az oktatóanyag egy elemzési lehetőségeket a tárfiókra van szükség. Javasoljuk, hogy végrehajtása az [rövid](data-lake-storage-quickstart-create-account.md) annak érdekében, hogy hozzon létre egyet a tárgyban. 

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

## <a name="create-a-databricks-cluster"></a>Databricks-fürt létrehozása

A következő lépés, hogy hozzon létre egy Databricks-fürt adatok munkaterület létrehozásához.

1. Az a [az Azure portal](https://portal.azure.com)válassza **erőforrás létrehozása**.
2. Adja meg **Azure Databricks** a keresőmezőbe.
3. Válassza ki **létrehozás** az Azure Databricks panelen.
4. Nevezze el a Databricks szolgáltatást **myFlightDataService** (ügyeljen arra, hogy ellenőrizze, hogy a *rögzítés az irányítópulton* jelölőnégyzetet, hogy a szolgáltatás létrehozása).
5. Válassza ki **munkaterület indítása** , nyissa meg a munkaterületet egy új böngészőablakban.
6. Válassza ki **fürtök** a bal oldali navigációs sávon.
7. Válassza ki **-fürt létrehozása**.
8. Írjon be egy **myFlightDataCluster** értéket a **Fürt neve** mezőbe.
9. A **Feldolgozó típusa** mezőben válassza a következőt: **Standard_D8s_v3**.
10. Állítsa a **Min. feldolgozók** értéket **4**-re.
11. Válassza ki **-fürt létrehozása** az oldal tetején. (Ez a folyamat eltarthat akár 5 percet.)
12. A folyamat befejezése után válassza **Azure Databricks** a bal felső részén a navigációs sávon.
13. A lap alsó felén található **Új** területen válassza a **Notebook** lehetőséget.
14. Adjon meg egy nevet a választott a **neve** mezőt, és válassza **Python** nyelve.
15. Az összes többi mező maradhat az alapértelmezett értéken.
16. Kattintson a **Létrehozás** gombra.
17. Másolja és illessze be az alábbi kódblokkot az első olyan cellára, de még ne futtassa ezt a kódot.

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
18. A kódblokk, cserélje le a `storage-account-name`, `application-id`, `authentication-id`, és `tenant-id` lépéseinek végrehajtását összegyűjtött értékek a kódblokk a helyőrző értékeket a [félretett storage-fiók konfigurációs](#config) és [egyszerű szolgáltatás létrehozása](#service-principal) Ez a cikk szakaszainak. Cserélje le a `file-system-name` bármilyen nevet kíván rendelni a fájlrendszer helyőrzőt.

19. Nyomja le az **SHIFT + ENTER** kulcsok a kód futtatásához a blokk.

## <a name="ingest-data"></a>Adatok betöltése

### <a name="copy-source-data-into-the-storage-account"></a>Forrásadatok másolása a tárfiókba

A következő lépés az adatok másolása a *.csv* fájlból az Azure-beli tárolóba az AzCopy segítségével. Nyissa meg a parancsablakot, és írja be a következő parancsokat. Győződjön meg arról, hogy cserélje le a helyőrzőket `<DOWNLOAD_FILE_PATH>`, `<ACCOUNT_NAME>`, és `<ACCOUNT_KEY>` , félretett az előző lépésben a megfelelő értékekkel.

```bash
set ACCOUNT_NAME=<ACCOUNT_NAME>
set ACCOUNT_KEY=<ACCOUNT_KEY>
azcopy cp "<DOWNLOAD_FILE_PATH>" https://<ACCOUNT_NAME>.dfs.core.windows.net/dbricks/folder1/On_Time --recursive 
```

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>CSV konvertálása parquetté a Databricks-jegyzetfüzet használatával

Nyissa meg újra a Databrickst a böngészőben, majd hajtsa végre a következő lépéseket:

1. Válassza ki **Azure Databricks** a bal felső részén a navigációs sávon.
2. A lap alsó felén található **Új** területen válassza a **Notebook** lehetőséget.
3. A **Név** mezőbe írja a következőt: **CSV2Parquet**.
4. Az összes többi mező maradhat az alapértelmezett értéken.
5. Kattintson a **Létrehozás** gombra.
6. Illessze be az alábbi kódot a **Cmd 1** cella. (Ez a kód automatikus – menti a szerkesztőben.)

    ```python
    # Use the previously established DBFS mount point to read the data
    # create a dataframe to read data
    flightDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/On_Time_On_Time*.csv")
    # read the all the airline csv files and write the output to parquet format for easy query
    flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
    print("Done")
    ```

## <a name="explore-data"></a>Adatok megismerése

Térjen vissza a Databricks-munkaterülethez, és válassza ki a **legutóbbi** ikonra a bal oldali navigációs sávon.

1. Válassza ki a **Flight Data Analytics** notebookot.
2. Új cella létrehozásához nyomja le a **CTRL + ALT + N** billentyűkombinációt.

Írja be a következő kódblokkok mindegyikét a **Cmd 1** területre, majd nyomja le a **Cmd + ENTER** billentyűkombinációt a Python-szkript futtatásához.

Az AzCopyn keresztül feltöltött CSV-fájlok listájának eléréséhez futtassa a következő szkriptet:

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata/temp/"))
```

Új fájl létrehozásához és fájlok listázásához a *parquet/flights* mappában futtassa ezt a szkriptet:

```python
dbutils.fs.put("/mnt/flightdata/temp/1.txt", "Hello, World!", True)
dbutils.fs.ls("/mnt/flightdata/temp/parquet/flights")
```

Ezekkel a kódmintákkal megismerte a HDFS hierarchikus jellegét a Data Lake Storage Gen2-kompatiblis tárfiókban tárolt adatok használatával.

## <a name="query-the-data"></a>Adatok lekérdezése

Következő lépésként megkezdheti a tárfiókba feltöltött adatok lekérdezését. Írja be a következő kódblokkok mindegyikét a **Cmd 1** területre, majd nyomja le a **Cmd + ENTER** billentyűkombinációt a Python-szkript futtatásához.

### <a name="run-simple-queries"></a>Egyszerű lekérdezések futtatása

Az adatforrások adathalmazainak létrehozásához futtassa a következő szkriptet:

> [!IMPORTANT]
> Ne felejtse lecserélni a **<YOUR_CSV_FILE_NAME>** helyőrzőt az oktatóanyag megkezdésekor letöltött fájl nevére.

```python
#Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/<YOUR_CSV_FILE_NAME>.csv")
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

Elemző lekérdezések futtatásához futtassa az adatokon a következő szkriptet:

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
out1 = spark.sql("SELECT distinct(Carrier) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```

### <a name="run-complex-queries"></a>Összetett lekérdezések futtatása

Az alábbi összetettebb lekérdezések futtatásához minden szegmenst futtasson külön a Notebookban, és vizsgálja meg az eredményeket.

```python
#find the airline with the most flights

#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v")
spark.sql("CREATE TEMPORARY VIEW v AS SELECT Carrier, count(*) as NumFlights from FlightTable group by Carrier, UniqueCarrier order by NumFlights desc LIMIT 10")
output = spark.sql("SELECT AirlineName FROM AirlineCodes WHERE AirlineCode in (select Carrier from v)")

#show the top row without truncation
output.show(1, False)

#show the top 10 airlines
output.show(10, False)

#Determine which is the least on time airline

#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v")
spark.sql("CREATE TEMPORARY VIEW v AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE DepDelay>60 or ArrDelay>60 group by Carrier, UniqueCarrier order by NumFlights desc LIMIT 10")
output = spark.sql("select * from v")
#output = spark.sql("SELECT AirlineName FROM AirlineCodes WHERE AirlineCode in (select Carrier from v)")
#show the top row without truncation
output.show(1, False)

#which airline improved its performance
#find the airline with the most improvement in delays
#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v1")
spark.sql("DROP VIEW IF EXISTS v2")
spark.sql("CREATE TEMPORARY VIEW v1 AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE (DepDelay>0 or ArrDelay>0) and Year=2016 group by Carrier order by NumFlights desc LIMIT 10")
spark.sql("CREATE TEMPORARY VIEW v2 AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE (DepDelay>0 or ArrDelay>0) and Year=2017 group by Carrier order by NumFlights desc LIMIT 10")
output = spark.sql("SELECT distinct ac.AirlineName, v1.Carrier, v1.NumFlights, v2.NumFlights from v1 INNER JOIN v2 ON v1.Carrier = v2.Carrier INNER JOIN AirlineCodes ac ON v2.Carrier = ac.AirlineCode WHERE v1.NumFlights > v2.NumFlights")
#show the top row without truncation
output.show(10, False)

#display for visual analysis
display(output)
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Amikor szükség van rájuk már nem, törölje az erőforráscsoportot és az összes kapcsolódó erőforrás. Ehhez válassza ki a storage-fiókhoz tartozó erőforráscsoportot, és válassza ki **törlése**.

## <a name="next-steps"></a>További lépések

[!div class="nextstepaction"] 
> [Adatok kinyerése, átalakítása és betöltése az Azure HDInsight-alapú Apache Hive használatával](data-lake-storage-tutorial-extract-transform-load-hive.md)

