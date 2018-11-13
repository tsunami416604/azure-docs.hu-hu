---
title: 'Oktatóanyag: Kinyerési, átalakítási és betöltési (ETL-) feladatok végrehajtása az Azure HDInsight-alapú Hive használatával '
description: Megtudhatja, hogyan nyerhet ki adatokat egy nyers CSV-adatkészletből, hogyan alakíthatja át az adatokat a Hive on HDInsight használatával, és hogyan töltheti be őket az Azure SQL Database-be az Apache Sqoop segítségével.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive,mvc
ms.openlocfilehash: ac56475f39f820c2d2af961a1813859ec42b0a46
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2018
ms.locfileid: "51038451"
---
# <a name="tutorial-extract-transform-and-load-data-using-apache-hive-on-azure-hdinsight"></a>Oktatóanyag: Adatok kinyerése, átalakítása és betöltése az Azure HDInsight-alapú Apache Hive használatával

Ebben az oktatóanyagban egy nyers CSV-adatfájlt fog importálni egy HDInsight-tárolófürtbe, majd átalakítja az adatokat az Azure HDInsight-alapú Apache Hive használatával. Az átalakítást követően pedig betölti az adatokat egy Azure SQL Database-be az Apache Sqoop segítségével. A cikkben nyilvánosan elérhető repülőjárat-adatokat fog használni.

> [!IMPORTANT]
> A dokumentum lépéseinek elvégzéséhez egy Linux-alapú HDInsight-fürt szükséges. A Linux az egyetlen operációs rendszer, amely az Azure HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

Ez az oktatóanyag a következő feladatokat mutatja be: 

> [!div class="checklist"]
> * A repülőjárat-mintaadatok letöltése
> * Az adatok feltöltése egy HDInsight-fürtre
> * Az adatok átalakítása a Hive használatával
> * Tábla létrehozása az Azure SQL Database-ben
> * Az adatok exportálása az Azure SQL Database-be a Sqoop segítségével


Az alábbi ábrán egy tipikus ETL-alkalmazásfolyam látható.

![ETL-műveletek az Azure HDInsight-alapú Apache Hive használatával](./media/hdinsight-analyze-flight-delay-data-linux/hdinsight-etl-architecture.png "ETL-műveletek az Azure HDInsight-alapú Apache Hive használatával")

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* **Egy Linux-alapú Hadoop-fürt a HDInsighton**. Egy új Linux-alapú HDInsight-fürt létrehozásához lásd [a Hadoop a HDInsight-beli első lépéseit](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Azure SQL Database** Egy Azure SQL Database-t használ céladattárként. Ha még nem rendelkezik SQL-adatbázissal, olvassa el az [Azure SQL Database az Azure Portalon történő létrehozását](../sql-database/sql-database-get-started.md) ismertető cikket.

* **Azure parancssori felület (CLI)**. Ha még nem telepítette az Azure CLI-t, a lépéseket [az Azure CLI telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ismertető cikkben találja.

* **Egy SSH-ügyfél**. További információért lásd: [Csatlakozás a HDInsighthoz (Hadoop) SSH-val](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>A repülőjárat-adatok letöltése

1. Nyissa meg a [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website] (Kutatási és Innovációs Műszaki Felügyelőség, Közlekedési Statisztikai Hivatal) oldalt.

2. Az oldalon válassza ki a következő értékeket:

   | Name (Név) | Érték |
   | --- | --- |
   | Filter Year (Szűrési év) |2013 |
   | Filter Period (Szűrési időszak) |January |
   | Mezők |Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   Az összes többi mező jelölését törölje. 

3. Válassza a **Download** (Letöltés) lehetőséget. Egy .zip fájlt kap, amely a kiválasztott adatmezőket tartalmazza.

## <a name="upload-data-to-an-hdinsight-cluster"></a>Az adatok feltöltése egy HDInsight-fürtre

Számos különböző módon tölthet fel adatokat egy HDInsight-fürthöz tartozó tárolóra. Ebben a szakaszban az `scp` segítségével fogja feltölteni az adatokat. Az adatok feltöltésének egyéb módjaival kapcsolatban lásd: [Adatok feltöltése a HDInsightba](hdinsight-upload-data.md).

1. Nyisson meg egy parancssort, és a következő paranccsal töltse fel a .zip fájlt a HDInsight-fürt fejcsomópontjára:

    ```bash
    scp <FILENAME>.zip <SSH-USERNAME>@<CLUSTERNAME>-ssh.azurehdinsight.net:<FILENAME.zip>
    ```

    Cserélje le a *FILENAME* kifejezést a .zip fájl nevére. Cserélje le a *USERNAME* kifejezést a HDInsight-fürthöz tartozó SSH-s bejelentkezési névre. Cserélje le a *CLUSTERNAME* kifejezést a HDInsight-fürt nevére.

   > [!NOTE]
   > Ha az SSH-bejelentkezést egy jelszóval hitelesíti, a rendszer bekéri a jelszót. Nyilvános kulcs használatakor lehetséges, hogy az `-i` paramétert kell használnia, és meg kell adnia a megfelelő titkos kulcs elérési útját. Például: `scp -i ~/.ssh/id_rsa FILENAME.zip USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

2. Ha a feltöltés befejeződött, csatlakozzon a fürthöz az SSH-val. A parancssorban adja meg a következő parancsot:

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

3. Használja az alábbi parancsot a .zip fájl kicsomagolásához:

    ```bash
    unzip FILENAME.zip
    ```

    Ekkor egy körülbelül 60 MB méretű .csv fájlt kap.

4. Az alábbi parancsokkal hozzon létre egy könyvtárat a HDInsight-tárolón, majd másolja a könyvtárba a .csv fájlt:

    ```bash
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put <FILENAME>.csv /tutorials/flightdelays/data/
    ```

## <a name="transform-data-using-a-hive-query"></a>Adatok átalakítása egy Hive-lekérdezéssel

Számos módon futtathat Hive-feladatokat egy HDInsight-fürtön. Ebben a szakaszban a Beeline segítségével fog futtatni egy Hive-feladatot. További információk a Hive-feladatok futtatásának további módjairól: [A Hive használata a HDInsightban](./hadoop/hdinsight-use-hive.md).

A Hive-feladat keretében importálja az adatokat a .csv fájlból egy **Delays** (Késések) nevű Hive-táblába.

1. A HDInsight-fürthöz már megnyitott SSH-parancssorba írja be a következő parancsot, amely létrehoz egy **flightdelays.hql** nevű a szerkesztéséhez:

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

2. A fájl mentéséhez nyomja le az **Esc** billentyűt, majd írja be a `:x` kifejezést.

3. Indítsa el a Hive-ot, és futtassa a **flightdelays.hql** fájlt az alábbi paranccsal:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

4. Miután a __flightdelays.hql__ szkript lefutott, a következő paranccsal nyisson meg egy interaktív Beeline-munkamenetet:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

5. Amikor a `jdbc:hive2://localhost:10001/>` parancssor megjelenik, a következő lekérdezéssel nyerhet ki adatokat az importált repülőjárat-késési adatokból:

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

6. A Beeline-ból való kilépéshez írja be a parancssorba a `!quit` parancsot.

## <a name="create-a-sql-database-table"></a>SQL Database-tábla létrehozása

Ez a szakasz feltételezi, hogy korábban már létrehozott egy Azure SQL Database-t. Ha még nem rendelkezik SQL-adatbázissal, hozzon létre egyet az [Azure SQL Database az Azure Portalon történő létrehozását](../sql-database/sql-database-get-started.md) ismertető cikkben leírt módon.

Ha már van egy SQL-adatbázisa, le kell kérnie a kiszolgáló nevét. A kiszolgáló nevének megkereséséhez az [Azure Portalon](https://portal.azure.com) válassza ki az **SQL-adatbázisok** elemet, majd végezzen szűrést a használni kívánt adatbázis nevére. A kiszolgáló neve a **Kiszolgáló neve** oszlopban látható.

![Az Azure SQL-kiszolgáló részleteinek lekérése](./media/hdinsight-analyze-flight-delay-data-linux/get-azure-sql-server-details.png "Az Azure SQL-kiszolgáló részleteinek lekérése")

> [!NOTE]
> Számos módon csatlakozhat az SQL Database-hez, majd hozhat létre egy táblát. A következő lépések során a [FreeTDS](http://www.freetds.org/) eszközt használjuk a HDInsight-fürtről.


1. A FreeTDS telepítéséhez használja a következő parancsot egy, a fürthöz csatlakozó SSH-kapcsolaton:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

3. A telepítés végeztével futtassa a következő parancsot az SQL Database-kiszolgálóhoz való csatlakozáshoz. Cserélje le a **serverName** kifejezést az SQL Database-kiszolgáló nevére. Cserélje le az **adminLogin** és **adminPassword** kifejezést az SQL Database-hez tartozó bejelentkezési adataira. Cserélje le a **databaseName** kifejezést az adatbázis nevére.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -p 1433 -D <databaseName>
    ```

    Ha a rendszer kéri, adja meg az SQL Database rendszergazdai bejelentkezési nevéhez tartozó jelszavát.

    A kimenet a következő szöveghez fog hasonlítani:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to sqooptest
    1>
    ```

4. Az `1>` parancssorban írja be a következő sorokat:

    ```hiveql
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
    ([origin_city_name] ASC))
    GO
    ```

    A `GO` utasítás megadásakor a rendszer kiértékeli az előző utasításokat. Ez a lekérdezés létrehoz egy **delays** nevű táblát, amelyhez egy fürtözött index tartozik.

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

5. A tsql eszközből való kilépéshez írja be az `exit` kifejezést az `1>` parancssorba.

## <a name="export-data-to-sql-database-using-sqoop"></a>Adatok exportálása az SQL Database-be a Sqoop használatával

Az előző szakaszok során átmásolta az átalakított adatokat a következő helyre: `/tutorials/flightdelays/output`. Ebben a szakaszban a Sqoop segítségével fogja exportálni az adatokat a „/tutorials/flightdelays/output” helyről az Azure SQL Database-ben létrehozott táblába. 

1. A következő paranccsal ellenőrizze, hogy a Sqoop látja-e az SQL-adatbázist:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>
    ```

    Ez a parancs egy listát ad vissza az adatbázisokról, köztük azt az adatbázist is, amelyben korábban létrehozta a delays táblát.

2. A következő paranccsal exportálhatja az adatokat a hivesampletable táblából a delays táblába:

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=<databaseName>' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    A Sqoop csatlakozik a delays táblát tartalmazó adatbázishoz, és exportálja az adatokat a `/tutorials/flightdelays/output` könyvtárból a delays táblába.

3. Miután a Sqoop-parancs lefutott, csatlakozzon az adatbázishoz a tsql eszközzel:

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>
    ```

    A következő utasításokkal ellenőrizheti, hogy az adatok exportálva lettek-e a delays táblába:

    ```sql
    SELECT * FROM delays
    GO
    ```

    A táblában látnia kell az adatok listáját. A tábla a városok nevét és az egyes városokhoz tartozó átlagos késések idejét tartalmazza. 

    A tsql eszközből való kilépéshez írja be az `exit` parancsot.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag azt mutatta be, hogyan végezheti el az adatok kinyerési, átalakítási és betöltési (ETL-) műveleteit egy Apache Hadoop-fürt a HDInsighton történő használatával. A következő oktatóanyagból megtudhatja, hogyan hozhat létre HDInsight Hadoop-fürtöket igény szerint az Azure Data Factory használatával.

> [!div class="nextstepaction"]
>[Igény szerinti Hadoop-fürtök létrehozása a HDInsightban az Azure Data Factory használatával](hdinsight-hadoop-create-linux-clusters-adf.md)

A HDInsight használatának további módjaival kapcsolatban lásd a következő cikkeket:

* [Oktatóanyag: Adatok kinyerése, átalakítása és betöltése az Azure HDInsight-alapú Apache Hive használatával](../storage/data-lake-storage/tutorial-extract-transform-load-hive.md)
* [A Hive használata a HDInsightban][hdinsight-use-hive]
* [A Pig használata a HDInsightban][hdinsight-use-pig]
* [Java MapReduce-programok fejlesztése a Hadoophoz a HDInsightban][hdinsight-develop-mapreduce]
* [Python MapReduce-streamprogramok fejlesztése a HDInsightban][hdinsight-develop-streaming]
* [Az Oozie használata a HDInsightban][hdinsight-use-oozie]
* [A Sqoop használata a HDInsightban][hdinsight-use-sqoop]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-develop-streaming]:hadoop/apache-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
