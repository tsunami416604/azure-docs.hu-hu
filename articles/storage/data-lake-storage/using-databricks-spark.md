---
title: Hozzáférés a 2. generációs Azure Data Lake Storage előzetes verziójának adataihoz az Azure Databricks és a Spark használatával | Microsoft Docs
description: Ismerje meg, hogyan futtathat Spark-lekérdezéseket egy Azure Databricks-fürtön 2. generációs Azure Data Lake Storage-tárfiókban tárolt adatokhoz való hozzáféréshez.
services: hdinsight,storage
tags: azure-portal
author: dineshm
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 6/27/2018
ms.author: dineshm
ms.openlocfilehash: 42bf3fca0e30c21e5f8737e37f4ac35db188ee90
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2018
ms.locfileid: "42616490"
---
# <a name="tutorial-access-azure-data-lake-storage-gen2-preview-data-with-azure-databricks-using-spark"></a>Oktatóanyag: Hozzáférés a 2. generációs Azure Data Lake Storage előzetes verziójának adataihoz az Azure Databricks és a Spark használatával

Ez az oktatóanyag bemutatja, hogyan futtathat Spark-lekérdezéseket egy Azure Databricks-fürtön adatok lekérdezéséhez egy 2. generációs Azure Data Lake Storage előzetes verziójával kompatibilis fiókban.

> [!div class="checklist"]
> * Databricks-fürt létrehozása
> * Strukturálatlan adatok betöltése egy tárfiókba
> * Egy Azure-beli függvény aktiválása az adatok feldolgozásához
> * Elemzés futtatása a blobtárolóban található adatokon

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag bemutatja, hogyan használhatja fel és kérdezheti le légitársaságok repülőjárat-adatait, amelyeket az [Egyesült Államok Közlekedési Minisztériuma](https://transtats.bts.gov/Tables.asp?DB_ID=120&DB_Name=Airline%20On-Time%20Performance%20Data&DB_Short_Name=On-Time) tett közzé. Töltsön le legalább két évnyi repülési adatot (az összes mezőt kiválasztva), és mentse az eredményt számítógépére. Jegyezze fel a fájl nevét és elérési útját, mert szüksége lesz erre az információra egy későbbi lépésben.

> [!NOTE]
> Kattintson a **Prezipped file** (Előre tömörített fájl) jelölőnégyzetre az összes adatmező kiválasztásához. A letöltés mérete több gigabájtnyi, de ez az adatmennyiség szükséges az elemzéshez.

## <a name="create-an-azure-data-lake-storage-gen2-account"></a>2. generációs Azure Data Lake Storage-fiók létrehozása

Elsőként hozzon létre egy új [2. generációs Azure Data Lake Storage-fiókot](quickstart-create-account.md), és adjon meg hozzá egy egyedi nevet. Ezután lépjen a tárfiókra a konfigurációs beállítások lekéréséhez.

> [!IMPORTANT]
> Az előzetes verzióban az Azure Functions csak strukturálatlan névtérrel létrehozott 2. generációs Azure Data Lake Storage-fiókokkal működik.

1. A **Beállítások** oldalon kattintson a **Hozzáférési kulcsok** gombra.
3. Kattintson a **Másolás** gombra a **key1** elem mellett a kulcs értékének másolásához.

A fióknév és a kulcs egyaránt szükséges az oktatóanyag későbbi lépéseihez. Nyisson meg egy szövegszerkesztőt, és jegyezze fel a fióknevet és a kulcsot a későbbi felhasználáshoz.

## <a name="create-a-databricks-cluster"></a>Databricks-fürt létrehozása

A következő lépésben egy [Databricks-fürtöt](https://docs.azuredatabricks.net/) kell létrehozni adat-munkaterület létrehozásához.

1. Hozzon létre egy [Databricks-szolgáltatást](https://ms.portal.azure.com/#create/Microsoft.Databricks), és nevezze el a következőképp: **myFlightDataService** (győződjön meg arról, hogy bejelölte a *Rögzítés az irányítópulton* jelölőnégyzetet a szolgáltatás létrehozásakor).
2. A munkaterület új böngészőablakban történő megnyitásához kattintson a **Munkaterület indítása** elemre.
3. A bal oldali navigációs sávban kattintson a **Fürtök** elemre.
4. Kattintson a **Fürt létrehozása** parancsra.
5. Írjon be egy **myFlightDataCluster** értéket a *Fürt neve* mezőbe.
6. A *Feldolgozó típusa* mezőben válassza a következőt: **Standard_D8s_v3**.
7. Állítsa a **Min. feldolgozók** értéket *4*-re.
8. Kattintson a **Fürt létrehozása** parancsra az oldal tetején (ez a folyamat akár 5 percig is eltarthat).
9. Amikor a folyamat befejeződött, válassza az **Azure Databricks** lehetőséget a navigációs sáv bal felső részén.
10. A lap alsó felén található **Új** területen válassza a **Notebook** lehetőséget.
11. Írjon be egy szabadon választott nevet a **Név** mezőbe, majd válassza ki a **Python** nyelvet.
12. Az összes többi mező maradhat az alapértelmezett értéken.
13. Kattintson a **Létrehozás** gombra.
14. Másolja az alábbi kódot a **Cmd 1** cellába, és cserélje le az értékeket a tárfiókjából megőrzöttekre.

    ```bash
    spark.conf.set("fs.azure.account.key.<account_name>.dfs.core.windows.net", "<account_key>") 
    spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
    dbutils.fs.ls("abfs://<file_system>@<account_name>.dfs.core.windows.net/")
    spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")
    ```

## <a name="ingest-data"></a>Adatok betöltése

### <a name="copy-source-data-into-the-storage-account"></a>Forrásadatok másolása a tárfiókba

A következő lépés az adatok másolása a *.csv* fájlból az Azure-beli tárolóba az AzCopy segítségével. Nyissa meg a parancsablakot, és írja be a következő parancsokat. Győződjön meg arról, hogy a `<DOWNLOAD_FILE_PATH>`, `<ACCOUNT_NAME>` és `<ACCOUNT_KEY>` helyőrzőket lecserélte az előző lépésben feljegyzett megfelelő értékekre.

```bash
set ACCOUNT_NAME=<ACCOUNT_NAME>
set ACCOUNT_KEY=<ACCOUNT_KEY>
azcopy cp "<DOWNLOAD_FILE_PATH>" https://<ACCOUNT_NAME>.dfs.core.windows.net/dbricks/folder1/On_Time --recursive 
```

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>CSV konvertálása parquetté a Databricks-jegyzetfüzet használatával

Nyissa meg újra a Databrickst a böngészőben, majd hajtsa végre a következő lépéseket:

1. Válassza az **Azure Databricks** lehetőséget a navigációs sáv bal felső részén.
2. A lap alsó felén található **Új** területen válassza a **Notebook** lehetőséget.
3. A **Név** mezőbe írja a következőt: **CSV2Parquet**.
4. Az összes többi mező maradhat az alapértelmezett értéken.
5. Kattintson a **Létrehozás** gombra.
6. Másolja az alábbi kódot a **Cmd 1** cellába (ez a kód automatikusan el lesz mentve a szerkesztőben).

    ```python
    #mount Azure Blob Storage as an HDFS file system to your databricks cluster
    #you need to specify a storage account and container to connect to. 
    #use a SAS token or an account key to connect to Blob Storage.  
    accountname = "<insert account name>"
    accountkey = " <insert account key>"
    fullname = "fs.azure.account.key." +accountname+ ".dfs.core.windows.net"
    accountsource = "abfs://dbricks@" +accountname+ ".dfs.core.windows.net/folder1"
    #create a dataframe to read data
    flightDF = spark.read.format('csv').options(header='true', inferschema='true').load(accountsource + "/On_Time_On_Time*.csv")
    #read the all the airline csv files and write the output to parquet format for easy query
    flightDF.write.mode("append").parquet(accountsource + '/parquet/flights')

    #read the flight details parquet file 
    #flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load(accountsource + "/parquet/flights")
    print("Done")
    ```

## <a name="explore-data-using-hadoop-distributed-file-system"></a>Adatok felfedezése a Hadoop elosztott fájlrendszer használatával

Lépjen vissza a Databricks-munkaterületre, és kattintson a bal oldali navigációs sávban lévő **Legutóbbiak** ikonra.

1. Kattintson a **Flight Data Analytics** (Repülőjárat-adatok elemzése) jegyzetfüzetre.
2. Új cella létrehozásához nyomja le a **CTRL + ALT + N** billentyűkombinációt.

Írja be a következő kódblokkok mindegyikét a **Cmd 1** területre, majd nyomja le a **Cmd + ENTER** billentyűkombinációt a Python-szkript futtatásához.

Az AzCopyn keresztül feltöltött CSV-fájlok listájának eléréséhez futtassa a következő szkriptet:

```python
import os.path
import IPython
from pyspark.sql import SQLContext
source = "abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/"
dbutils.fs.ls(source + "/temp")
display(dbutils.fs.ls(source + "/temp/"))
```

Új fájl létrehozásához és fájlok listázásához a *parquet/flights* mappában futtassa ezt a szkriptet:

```python
source = "abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/"

dbutils.fs.help()

dbutils.fs.put(source + "/temp/1.txt", "Hello, World!", True)
dbutils.fs.ls(source + "/temp/parquet/flights")
```
Ezekkel a kódmintákkal megismerte a Hadoop elosztott fájlrendszer hierarchikus jellegét a 2. generációs Azure Data Lake Storage-kompatibilis fiókban tárolt adatok használatának segítségével.

## <a name="query-the-data"></a>Adatok lekérdezése

Ezt követően megkezdheti az Azure Data Lake Storage-be feltöltött adatok lekérdezését. Írja be a következő kódblokkok mindegyikét a **Cmd 1** területre, majd nyomja le a **Cmd + ENTER** billentyűkombinációt a Python-szkript futtatásához.

### <a name="simple-queries"></a>Egyszerű lekérdezések

Az adatforrások adathalmazainak létrehozásához futtassa a következő szkriptet:

> [!IMPORTANT]
> Ne felejtse lecserélni a **<YOUR_CSV_FILE_NAME>** helyőrzőt az oktatóanyag megkezdésekor letöltött fájl nevére.

```python
#Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(header='true', inferschema='true').load(accountsource + "/<YOUR_CSV_FILE_NAME>.csv")
acDF.write.parquet(accountsource + '/parquet/airlinecodes')

#read the existing parquet file for the flights database that was created via the Azure Function
flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load(accountsource + "/parquet/flights")

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
#preferrably run this in a separate cmd cell
display(flightDF)
```

Elemző lekérdezések futtatásához futtassa az adatokon a következő szkriptet:

```python
#Run each of these queries, preferrably in a separate cmd cell for separate analysis
#create a temporary sql view for querying flight information
FlightTable = spark.read.parquet(accountsource + '/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

#create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet(accountsource + '/parquet/airlinecodes')
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
### <a name="complex-queries"></a>Összetett lekérdezések

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

## <a name="next-steps"></a>További lépések

* [Adatok kinyerése, átalakítása és betöltése az Azure HDInsight-alapú Apache Hive használatával](tutorial-extract-transform-load-hive.md)