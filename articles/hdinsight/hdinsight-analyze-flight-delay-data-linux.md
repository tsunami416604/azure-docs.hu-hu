---
title: 'Oktatóanyag: Hajtsa végre a kinyerési, átalakítási, betöltési (ETL) operations Hive használata a HDInsight – Azure '
description: Megtudhatja, hogyan nyerhet ki adatokat egy nyers CSV-adatkészletből, hogyan alakíthatja át az adatokat a Hive on HDInsight használatával, és hogyan töltheti be őket az Azure SQL-adatbázisba az Apache Sqoop segítségével.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 05/15/2019
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive,mvc
ms.openlocfilehash: ac1ae7ed761099a19accf55e9e4dab61193c2de7
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967802"
---
# <a name="tutorial-extract-transform-and-load-data-using-apache-hive-in-azure-hdinsight"></a>Oktatóanyag: A kinyerési, átalakítási és adatok betöltése az Azure HDInsight az Apache Hive használatával

Ebben az oktatóanyagban a nyilvánosan elérhető flight data nyers CSV adatfájl igénybe, importálás egy HDInsight-fürt storage-ba, és akkor átalakítja az adatokat az [Apache Hive](https://hive.apache.org/) az Azure HDInsight. Az adatok átalakítására kerül, ha az adatokat betöltheti egy Azure SQL database-adatbázishoz [Apache Sqoop](https://sqoop.apache.org/).

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * A repülőjárat-mintaadatok letöltése
> * Az adatok feltöltése egy HDInsight-fürtre
> * Az adatok átalakítása a Hive használatával
> * Hozzon létre egy táblát egy Azure SQL database-ben
> * Egy Azure SQL database-adatok exportálása a Sqoop használatával

Az alábbi ábrán egy tipikus ETL-alkalmazásfolyam látható.

![ETL-műveletek az Azure HDInsight-alapú Apache Hive használatával](./media/hdinsight-analyze-flight-delay-data-linux/hdinsight-etl-architecture.png "ETL-műveletek az Azure HDInsight-alapú Apache Hive használatával")

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Egy HDInsight az Apache Hadoop-fürtöt. Lásd: [HDInsight Linux első lépések](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* Az Azure SQL-adatbázis. Egy Azure SQL-adatbázist használ céladattárként. Ha még nem rendelkezik SQL-adatbázissal, olvassa el az [Azure SQL-adatbázis az Azure Portalon történő létrehozását](../sql-database/sql-database-single-database-get-started.md) ismertető cikket.

* Egy SSH-ügyfél. További információkért lásd: [HDInsight (az Apache Hadoop) SSH-val csatlakozhat](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>A repülőjárat-adatok letöltése

1. Keresse meg a [kutatásáról és az innovatív technológia felügyeleti, a szállítás statisztikák Hivatala](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. A lapon törölje az összes mezőt, és válassza ki a következő értékeket:

   | Name (Név) | Érték |
   | --- | --- |
   | Filter Year (Szűrési év) |2019 |
   | Filter Period (Szűrési időszak) |January |
   | Mezők |Év FlightDate, Reporting_Airline, DOT_ID_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, forrás, OriginCityName, OriginState, DestAirportID, cél, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |

3. Válassza a **Download** (Letöltés) lehetőséget. Egy .zip fájlt kap, amely a kiválasztott adatmezőket tartalmazza.

## <a name="upload-data-to-an-hdinsight-cluster"></a>Az adatok feltöltése egy HDInsight-fürtre

Számos különböző módon tölthet fel adatokat egy HDInsight-fürthöz tartozó tárolóra. Ebben a szakaszban az `scp` segítségével fogja feltölteni az adatokat. Az adatok feltöltésének egyéb módjaival kapcsolatban lásd: [Adatok feltöltése a HDInsightba](hdinsight-upload-data.md).

1. Töltse fel a .zip-fájlt a HDInsight-fürt fő csomópontjának. Az alábbi parancsot szerkesztése lecserélésével `FILENAME` nevét a .zip-fájlt, és `CLUSTERNAME` a HDInsight-fürt nevére. Ezután nyisson meg egy parancssort, és a munkakönyvtárban állítsa a fájl helyének majd írja be a parancsot.

    ```cmd
    scp FILENAME.zip sshuser@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.zip
    ```

2. Ha a feltöltés befejeződött, csatlakozzon a fürthöz az SSH-val. Az alábbi parancsot szerkesztése lecserélésével `CLUSTERNAME` a HDInsight-fürt nevére. Ezután írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Állítsa be a környezeti változót, egy SSH-kapcsolat létrehozása után. Cserélje le `FILE_NAME`, `SQL_SERVERNAME`, `SQL_DATABASE`, `SQL_USER`, és `SQL_PASWORD` megfelelő értékeivel. Ezután írja be a parancsot:

    ```bash
    export FILENAME=FILE_NAME
    export SQLSERVERNAME=SQL_SERVERNAME
    export DATABASE=SQL_DATABASE
    export SQLUSER=SQL_USER
    export SQLPASWORD='SQL_PASWORD'
    ```

4. Bontsa ki a .zip-fájlt az alábbi parancs beírásával:

    ```bash
    unzip $FILENAME.zip
    ```

5. Hozzon létre egy könyvtárat a HDInsight tárolási, és másolja a .csv-fájlt a könyvtárba az alábbi parancs beírásával:

    ```bash
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put $FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="transform-data-using-a-hive-query"></a>Adatok átalakítása egy Hive-lekérdezéssel

Számos módon futtathat Hive-feladatokat egy HDInsight-fürtön. Ebben a szakaszban használhatja [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline%E2%80%93CommandLineShell) futtathat Hive-feladatokat. Információk az egyéb módszerek egy Hive-feladat futtatása: [az Apache Hive használata a HDInsight](./hadoop/hdinsight-use-hive.md).

A Hive-feladat keretében importálja az adatokat a .csv fájlból egy **Delays** (Késések) nevű Hive-táblába.

1. A SSH használatával, amely már rendelkezik a HDInsight-fürthöz, a következő paranccsal hozhat létre, és egy új fájlt szerkesztése **flightdelays.hql**:

    ```bash
    nano flightdelays.hql
    ```

2. A fájl tartalma legyen a következő szöveg:

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION '/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

3. Mentse a fájlt, nyomja le a **Ctrl + X**, majd **y**, majd adja meg.

4. Indítsa el a Hive-ot, és futtassa a **flightdelays.hql** fájlt az alábbi paranccsal:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

5. Miután a **flightdelays.hql** szkript lefutott, a következő paranccsal nyisson meg egy interaktív Beeline-munkamenetet:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

6. Amikor a `jdbc:hive2://localhost:10001/>` parancssor megjelenik, a következő lekérdezéssel nyerhet ki adatokat az importált repülőjárat-késési adatokból:

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    Ez a lekérdezés lekéri azon városok listáját, ahol időjárás miatti késések történtek, valamint a késések átlagos idejét, és menti ezeket az adatokat a következő helyen: `/tutorials/flightdelays/output`. Később a Sqoop erről a helyről olvassa be az adatokat, amelyeket exportál az Azure SQL Database-be.

7. A Beeline-ból való kilépéshez írja be a parancssorba a `!quit` parancsot.

## <a name="create-a-sql-database-table"></a>SQL Database-tábla létrehozása

Számos módon csatlakozhat az SQL Database-hez, majd hozhat létre egy táblát. A következő lépések során a [FreeTDS](http://www.freetds.org/) eszközt használjuk a HDInsight-fürtről.

1. A freetds, használja a fürtön nyissa meg az SSH-kapcsolatot a következő parancsot:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. A telepítés végeztével futtassa a következő parancsot az SQL-adatbáziskiszolgálóhoz való csatlakozáshoz.

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    A kimenet a következő szöveghez fog hasonlítani:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to <yourdatabase>
    1>
    ```

3. Az `1>` parancssorban írja be a következő sorokat:

    ```hiveql
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
    ([origin_city_name] ASC))
    GO
    ```

    A `GO` utasítás megadásakor a rendszer kiértékeli az előző utasításokat. Ez az utasítás létrehoz egy tábla **késések**, fürtözött indexszel.

    Az alábbi lekérdezéssel ellenőrizheti, hogy a tábla létrejött-e:

    ```hiveql
    SELECT * FROM information_schema.tables
    GO
    ```

    A kimenet az alábbi szöveghez hasonló:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo             delays        BASE TABLE
    ```

4. A tsql eszközből való kilépéshez írja be az `exit` kifejezést az `1>` parancssorba.

## <a name="export-data-to-sql-database-using-apache-sqoop"></a>Adatok exportálása az SQL database az Apache Sqoop használatával

Az előző szakaszok során átmásolta az átalakított adatokat a következő helyre: `/tutorials/flightdelays/output`. Ebben a szakaszban a Sqoop segítségével fogja exportálni az adatokat az `/tutorials/flightdelays/output` helyről az Azure SQL-adatbázisban létrehozott táblába.

1. Győződjön meg arról, hogy a Sqoop látja-e az SQL database az alábbi parancs beírásával:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433 --username $SQLUSER --password $SQLPASWORD
    ```

    Ez a parancs olyan adatbázisokhoz, beleértve az adatbázist, amelyben létrehozta listáját adja vissza a `delays` korábban tábla.

2. Az adatok exportálása `/tutorials/flightdelays/output` , a `delays` tábla az alábbi parancs beírásával:

    ```bash
    sqoop export --connect "jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433;database=$DATABASE" --username $SQLUSER --password $SQLPASWORD --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    Sqoop használatával csatlakozik az adatbázis, amely tartalmazza a `delays` táblázat és az adatok exportálása a `/tutorials/flightdelays/output` könyvtárat a a `delays` tábla.

3. A sqoop parancs befejeződését követően a tsql segédprogram használatával csatlakozzon az adatbázishoz az alábbi parancs beírásával:

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    A következő utasításokkal ellenőrizheti, hogy az adatok exportálva lettek-e a delays táblába:

    ```sql
    SELECT * FROM delays
    GO
    ```

    A táblában látnia kell az adatok listáját. A tábla a városok nevét és az egyes városokhoz tartozó átlagos késések idejét tartalmazza. 

    A tsql eszközből való kilépéshez írja be az `exit` parancsot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag befejezése után érdemes törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket.

Törölje a fürtöt, tekintse meg a [törlése egy HDInsight-fürtön a böngészőben, a PowerShell vagy az Azure CLI](./hdinsight-delete-cluster.md).

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtette a nyers adatok CSV-fájl, importálta, akkor egy HDInsight-fürt storage-ba, és majd alakította át az adatokat az Azure HDInsight az Apache Hive használatával.  A következő oktatóanyagból megtudhatja, hogyan hozhat létre HDInsight Hadoop-fürtöket igény szerint az Azure Data Factory használatával.

> [!div class="nextstepaction"]
>[Az Azure Data Factory használatával HDInsight igény szerinti Apache Hadoop-fürtök létrehozása](hdinsight-hadoop-create-linux-clusters-adf.md)

A HDInsight használatának további módjaival kapcsolatban lásd a következő cikkeket:

* [Oktatóanyag: A kinyerési, átalakítási és adatok betöltése az Azure HDInsight az Apache Hive használata](../storage/blobs/data-lake-storage-tutorial-extract-transform-load-hive.md)
* [Az Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)
* [Java MapReduce programok fejlesztése a HDInsight Apache hadoop](hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Az Apache Oozie használata a HDInsight](hdinsight-use-oozie-linux-mac.md)
* [A HDInsight Apache Sqoop használata](hadoop/apache-hadoop-use-sqoop-mac-linux.md)