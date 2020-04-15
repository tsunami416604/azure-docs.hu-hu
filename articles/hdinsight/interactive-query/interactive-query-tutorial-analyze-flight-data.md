---
title: 'Oktatóanyag: ETL-műveletek interaktív lekérdezéssel – Azure HDInsight'
description: Oktatóanyag – Ismerje meg, hogyan nyerhetki ki adatokat egy nyers CSV-adatkészletből. Alakítsa át a HDInsight interaktív lekérdezésével. Ezután töltse be az átalakított adatokat az Azure SQL-adatbázisba az Apache Sqoop használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 07/02/2019
ms.openlocfilehash: 7413a32fdddb579bad61c9cfe539be6aaeae9881
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313739"
---
# <a name="tutorial-extract-transform-and-load-data-using-interactive-query-in-azure-hdinsight"></a>Oktatóanyag: Adatok kinyerése, átalakítása és betöltése az Azure HDInsight interaktív lekérdezésével

Ebben az oktatóanyagban a nyilvánosan elérhető repülési adatok nyers CSV-adatfájlját tölti le. Importálja a HDInsight fürttárolóba, majd alakítsa át az adatokat az Azure HDInsight interaktív lekérdezése segítségével. Az adatok átalakítása után az adatokat az Apache [Sqoop](https://sqoop.apache.org/)használatával töltheti be egy Azure SQL-adatbázisba.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * A repülőjárat-mintaadatok letöltése
> * Az adatok feltöltése egy HDInsight-fürtre
> * Az adatok átalakítása az Interaktív lekérdezés sel
> * Tábla létrehozása Azure SQL-adatbázisban
> * Adatok exportálása Azure SQL-adatbázisba a Sqoop használatával

## <a name="prerequisites"></a>Előfeltételek

* Interaktív lekérdezési fürt a HDInsighton. Lásd: [Apache Hadoop-fürtök létrehozása az Azure Portalon,](../hdinsight-hadoop-create-linux-clusters-portal.md) és válassza az **Interaktív lekérdezés** **fürttípushoz lehetőséget.**

* Egy Azure SQL-adatbázis. Egy Azure SQL-adatbázist használ céladattárként. Ha még nem rendelkezik SQL-adatbázissal, olvassa el az [Azure SQL-adatbázis az Azure Portalon történő létrehozását](/azure/sql-database/sql-database-single-database-get-started) ismertető cikket.

* Egy SSH-ügyfél. További információ: [Csatlakozás a HDInsighthoz (Apache Hadoop) az SSH használatával.](../hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="download-the-flight-data"></a>A repülőjárat-adatok letöltése

1. Nyissa meg a [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time) (Kutatási és Innovációs Műszaki Felügyelőség, Közlekedési Statisztikai Hivatal) oldalt.

2. A lapon törölje az összes mezőt, majd jelölje ki a következő értékeket:

   | Név | Érték |
   | --- | --- |
   | Filter Year (Szűrési év) |2019 |
   | Filter Period (Szűrési időszak) |January |
   | Mezők |`Year, FlightDate, Reporting_Airline, DOT_ID_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay`. |

3. Válassza a **Download** (Letöltés) lehetőséget. Egy .zip fájlt kap, amely a kiválasztott adatmezőket tartalmazza.

## <a name="upload-data-to-an-hdinsight-cluster"></a>Az adatok feltöltése egy HDInsight-fürtre

Számos különböző módon tölthet fel adatokat egy HDInsight-fürthöz tartozó tárolóra. Ebben a szakaszban az `scp` segítségével fogja feltölteni az adatokat. Az adatok feltöltésének egyéb módjaival kapcsolatban lásd: [Adatok feltöltése a HDInsightba](../hdinsight-upload-data.md).

1. Töltse fel a .zip fájlt a HDInsight-fürt főcsomópontjára. Az alábbi parancs szerkesztésével cserélje le `FILENAME` a .zip `CLUSTERNAME` fájl nevét és a HDInsight-fürt nevét. Ezután nyisson meg egy parancssort, állítsa a munkakönyvtárat a fájl helyére, majd írja be a parancsot.

    ```cmd
    scp FILENAME.zip sshuser@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.zip
    ```

    Írja be az igen vagy a nem értéket a folytatáshoz, ha a rendszer kéri. A szöveg gépelés közben nem látható az ablakban.

2. Ha a feltöltés befejeződött, csatlakozzon a fürthöz az SSH-val. Az alábbi parancs szerkesztése a HDInsight-fürt nevének cseréjével. `CLUSTERNAME` Ezután írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. SSH-kapcsolat létrehozása után állítsa be a környezeti változót. Cserélje `FILE_NAME` `SQL_SERVERNAME`ki `SQL_DATABASE` `SQL_USER`a `SQL_PASWORD` , , , , és a megfelelő értékeket. Ezután írja be a parancsot:

    ```bash
    export FILENAME=FILE_NAME
    export SQLSERVERNAME=SQL_SERVERNAME
    export DATABASE=SQL_DATABASE
    export SQLUSER=SQL_USER
    export SQLPASWORD='SQL_PASWORD'
    ```

4. Csomagolja ki a .zip fájlt az alábbi parancs megadásával:

    ```bash
    unzip $FILENAME.zip
    ```

5. Hozzon létre egy könyvtárat a HDInsight-tárolón, majd másolja a .csv fájlt a könyvtárba az alábbi parancs megadásával:

    ```bash
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put $FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="transform-data-using-a-hive-query"></a>Adatok átalakítása egy Hive-lekérdezéssel

Számos módon futtathat Hive-feladatokat egy HDInsight-fürtön. Ebben a szakaszban a [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline%E2%80%93CommandLineShell) használatával futtathat egy Hive-feladatot. A Hive-feladat futtatásának egyéb módszereiről az [Apache Hive használata a HDInsight-on című témakörben talál](../hadoop/hdinsight-use-hive.md)további információt.

A Hive-feladat keretében importálja az adatokat a .csv fájlból egy **Delays** (Késések) nevű Hive-táblába.

1. A HDInsight-fürthöz már meglévő SSH-parancsból a következő paranccsal hozhat létre és szerkesztheti a **flightdelays.hql**nevű új fájlt:

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

3. A fájl mentéséhez nyomja le a **Ctrl + X**billentyűkombinációt, majd az **y**billentyűt, majd írja be a gombot.

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

1. A FreeTDS telepítéséhez használja a következő parancsot a nyílt SSH-kapcsolatból a fürthöz:

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

    A `GO` utasítás megadásakor a rendszer kiértékeli az előző utasításokat. Ez az utasítás egy **delays**nevű táblát hoz létre fürtözött indexszel.

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

## <a name="export-data-to-sql-database-using-apache-sqoop"></a>Adatok exportálása SQL-adatbázisba az Apache Sqoop használatával

Az előző szakaszok során átmásolta az átalakított adatokat a következő helyre: `/tutorials/flightdelays/output`. Ebben a szakaszban a Sqoop segítségével fogja exportálni az adatokat az `/tutorials/flightdelays/output` helyről az Azure SQL-adatbázisban létrehozott táblába.

1. Ellenőrizze, hogy a Sqoop láthatja-e az SQL-adatbázist az alábbi parancs megadásával:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433 --username $SQLUSER --password $SQLPASWORD
    ```

    Ez a parancs az adatbázisok listáját adja vissza, `delays` beleértve azt az adatbázist is, amelyben a táblát korábban létrehozta.

2. Exportálja `/tutorials/flightdelays/output` az `delays` adatokat a táblázatba az alábbi parancs megadásával:

    ```bash
    sqoop export --connect "jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433;database=$DATABASE" --username $SQLUSER --password $SQLPASWORD --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    A Sqoop csatlakozik a `delays` táblát tartalmazó adatbázishoz, `/tutorials/flightdelays/output` és `delays` adatokat exportál a könyvtárból a táblába.

3. Miután a sqoop parancs befejeződik, a tsql segédprogrammal csatlakozzon az adatbázishoz az alábbi parancs megadásával:

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

Az oktatóanyag befejezése után érdemes törölni a fürtöt. A HDInsight segítségével az adatok az Azure Storage-ban tárolódnak, így biztonságosan törölheti a fürtöt, ha nincs használatban. A HDInsight-fürtért is díjat kell fizetnie, még akkor is, ha nincs használatban. Mivel a fürt díjai sokszor több, mint a tárolási díjak, célszerű törölni a fürtöket, ha nincsenek használatban.

Fürt törléséről a [HDInsight-fürt törlése a böngésző, a PowerShell vagy az Azure CLI használatával című](../hdinsight-delete-cluster.md)témakörben jelenik meg.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy nyers CSV-adatfájlt vett, importálta egy HDInsight-fürttárolóba, majd átalakította az adatokat az Azure HDInsight interaktív lekérdezése használatával.  Továbbkell lépnie a következő oktatóanyaghoz, és ismerje meg az Apache Hive-raktárösszekötőt.

> [!div class="nextstepaction"]
> [Integrálja az Apache Sparkot és az Apache Hive-t a Hive Raktár összekötővel](./apache-hive-warehouse-connector.md)
