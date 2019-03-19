---
title: 'Oktatóanyag: Kinyerési, átalakítási, betöltési (ETL) műveletek végrehajtása az Apache Hive használatával az Azure HDInsight'
description: Ebből az oktatóanyagból megtudhatja, hogyan adatokat nyerhet ki nyers CSV adatkészletből, átalakítja a használatával a az Azure HDInsight az Apache Hive, és majd betölti az átalakított adatokat az Azure SQL Database-be a Sqoop használatával.
services: storage
author: jamesbak
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jamesbak
ms.openlocfilehash: cdb89b552d0e328e6685e2bd62ea135e15e4b074
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58013450"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-apache-hive-on-azure-hdinsight"></a>Oktatóanyag: A kinyerési, átalakítási és az Azure HDInsight segítségével a Apache Hive-adatok betöltése

Ebben az oktatóanyagban egy ETL-műveletet végezhet: kinyerése, átalakítása és betölteni az adatokat. Ön nyers CSV adatfájl igénybe, importálja azt egy Azure HDInsight-fürt, átalakítja az Apache Hive és töltse be az Azure SQL database, az Apache sqoop használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Csomagolja ki, és töltse fel az adatokat egy HDInsight-fürtön.
> * Az adatok átalakítása az Apache Hive segítségével.
> * Betöltheti az adatokat egy Azure SQL Database-adatbázishoz a Sqoop használata.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* **Egy Azure Data Lake Storage Gen2 storage-fiókot, amely konfigurálva van a HDInsight**

    Lásd: [használata Azure Data Lake Storage Gen2 Azure HDInsight-fürtök](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2).

* **A HDInsight Linux-alapú Hadoop-fürt**

    Lásd: [a rövid útmutató: Ismerkedés az Apache Hadoop- és Apache Hive, az Azure HDInsight az Azure portal használatával](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-linux-create-cluster-get-started-portal).

* **Azure SQL Database**: Egy Azure SQL Database-t használ céladattárként. Ha még nem rendelkezik SQL-adatbázissal, olvassa el az [Azure SQL Database az Azure Portalon történő létrehozását](../../sql-database/sql-database-get-started.md) ismertető cikket.

* **Az Azure CLI**: Ha még nem telepítette az Azure CLI-vel, tekintse meg [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* **A Secure Shell (SSH) ügyfél**: További információkért lásd: [HDInsight (Hadoop) SSH használatával csatlakozhat](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]
> A jelen cikkben ismertetett lépések szükség egy HDInsight-fürt által használt Linux. Linux az egyetlen operációs rendszer, amely arra szolgál, az Azure HDInsight 3.4-es vagy újabb verzió. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../../hdinsight/hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="download-the-flight-data"></a>A repülőjárat-adatok letöltése

1. Keresse meg a [kutatásáról és az innovatív technológia felügyeleti, a szállítás statisztikák Hivatala](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Az oldalon válassza ki a következő értékeket:

   | Name (Név) | Érték |
   | --- | --- |
   | Filter Year (Szűrési év) |2013 |
   | Filter Period (Szűrési időszak) |January |
   | Mezők |Év FlightDate, Reporting_Airline, IATA_CODE_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, forrás, OriginCityName, OriginState, DestAirportID, cél, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   
   Az összes többi mező jelölését törölje.

3. Válassza a **Download** (Letöltés) lehetőséget. Egy .zip fájlt kap, amely a kiválasztott adatmezőket tartalmazza.

## <a name="extract-and-upload-the-data"></a>Csomagolja ki, és töltse fel az adatokat

Ebben a szakaszban fogja feltölteni az adatokat a HDInsight-fürt, és másolja az adatokat a Data Lake Storage Gen2-fiók.

1. Nyisson meg egy parancssort, és a következő paranccsal biztonságos másolás (Scp) a .zip-fájlt tölthet fel a HDInsight-fürt fő csomópontjának:

   ```bash
   scp <file-name>.zip <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net:<file-name.zip>
   ```

   * Cserélje le a `<file-name>` helyőrzőt a .zip fájl neve.
   * Cserélje le a `<ssh-user-name>` helyőrző a HDInsight-fürthöz az SSH-bejelentkezéskor.
   * Cserélje le a `<cluster-name>` helyőrzőt a HDInsight-fürt nevére.

   Ha az SSH-bejelentkezést egy jelszóval hitelesíti, a rendszer bekéri a jelszót.

   Nyilvános kulcs használatakor lehetséges, hogy az `-i` paramétert kell használnia, és meg kell adnia a megfelelő titkos kulcs elérési útját. Például: `scp -i ~/.ssh/id_rsa <file_name>.zip <user-name>@<cluster-name>-ssh.azurehdinsight.net:`.

2. Ha a feltöltés befejeződött, csatlakozzon a fürthöz az SSH-val. A parancssorban adja meg a következő parancsot:

   ```bash
   ssh <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net
   ```

3. Használja az alábbi parancsot a .zip fájl kicsomagolásához:

   ```bash
   unzip <file-name>.zip
   ```

   A parancs kibontja a **.csv** fájlt.

4. A következő paranccsal hozzon létre a Data Lake Storage Gen2 fájlrendszer.

   ```bash
   hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/
   ```

   Cserélje le a `<file-system-name>` kíván adni a fájlrendszer neve helyőrzőt.

   Cserélje le a `<storage-account-name>` helyőrzőt a tárfiók nevére.

5. A következő paranccsal hozzon létre egy könyvtárat.

   ```bash
   hdfs dfs -mkdir -p abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data
   ```

6. A következő parancs használatával másolja a *.csv* fájlt a könyvtárba:

   ```bash
   hdfs dfs -put "<file-name>.csv" abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data/
   ```

   Használja az ajánlatok köré a fájl nevét, ha a fájl neve szóközöket és speciális karaktereket tartalmaz.

## <a name="transform-the-data"></a>Az adatok átalakítása

Ebben a szakaszban a Beeline egy Apache Hive-feladat futtatásához használhat.

Az Apache Hive-feladat részeként, az adatokat importálhat a .csv-fájlt nevű Apache Hive-táblába **késések**.

1. A SSH használatával, amely már rendelkezik a HDInsight-fürthöz, a következő paranccsal hozhat létre és szerkeszthet egy új fájlt **flightdelays.hql**:

   ```bash
   nano flightdelays.hql
   ```

2. A következő szöveg módosításához cserélje le a `<file-system-name>` és `<storage-account-name>` helyőrzőket a rendszer és a tárolási fiók nevére. Ezután másolja és illessze be a nano-konzol használatával, az egér jobb gombjával kattintson a gomb mellett a SHIFT billentyű lenyomásával.

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
    LOCATION 'abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays
    LOCATION 'abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/processed'
    AS
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

3. Mentse a fájlt a CTRL + X használja, és írja be `Y` amikor a rendszer kéri.

4. Indítsa el a Hive-ot, és futtassa a **flightdelays.hql** fájlt az alábbi paranccsal:

   ```bash
   beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
   ```

5. Miután a __flightdelays.hql__ szkript lefutott, a következő paranccsal nyisson meg egy interaktív Beeline-munkamenetet:

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

   Ez a lekérdezés lekéri azon városok listáját, ahol időjárás miatti késések történtek, valamint a késések átlagos idejét, és menti ezeket az adatokat a következő helyen: `abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output`. Később a Sqoop erről a helyről olvassa be az adatokat, amelyeket exportál az Azure SQL Database-be.

7. A Beeline-ból való kilépéshez írja be a parancssorba a `!quit` parancsot.

## <a name="create-a-sql-database-table"></a>SQL Database-tábla létrehozása

A kiszolgáló nevét kell ehhez a művelethez az SQL database-ből. Ezeket a lépéseket a kiszolgálónév.

1. Nyissa meg az [Azure Portal](https://portal.azure.com).

2. Válassza ki **SQL-adatbázisok**.

3. Szűrhet a használni kívánt adatbázis nevét. A kiszolgáló neve a **Kiszolgáló neve** oszlopban látható.

4. Szűrhet a használni kívánt adatbázis nevét. A kiszolgáló neve a **Kiszolgáló neve** oszlopban látható.

    ![Az Azure SQL-kiszolgáló részleteinek lekérése](./media/data-lake-storage-tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Az Azure SQL-kiszolgáló részleteinek lekérése")

    Számos módon csatlakozhat az SQL Database-hez, majd hozhat létre egy táblát. A következő lépések során a [FreeTDS](http://www.freetds.org/) eszközt használjuk a HDInsight-fürtről.

5. A FreeTDS telepítéséhez használja a következő parancsot egy, a fürthöz csatlakozó SSH-kapcsolaton:

   ```bash
   sudo apt-get --assume-yes install freetds-dev freetds-bin
   ```

6. A telepítés befejezése után használja a következő parancsot az SQL Database-kiszolgálóhoz való csatlakozáshoz.

   ```bash
   TDSVER=8.0 tsql -H '<server-name>.database.windows.net' -U '<admin-login>' -p 1433 -D '<database-name>'
    ```
   * Cserélje le a `<server-name>` helyőrzőt az SQL Database-kiszolgáló nevével.

   * Cserélje le a `<admin-login>` helyőrzőt az SQL Database-adatbázis rendszergazdai bejelentkezéssel.

   * Cserélje le a `<database-name>` helyőrzőt az adatbázis neve

   Amikor kéri, adja meg a jelszót az SQL Database-rendszergazdai bejelentkezési.

   A kimenet a következő szöveghez fog hasonlítani:

   ```
   locale is "en_US.UTF-8"
   locale charset is "UTF-8"
   using default charset "UTF-8"
   Default database being set to sqooptest
   1>
   ```

7. Jelenleg a `1>` kéri, adja meg az alábbi utasításokat:

   ```hiveql
   CREATE TABLE [dbo].[delays](
   [OriginCityName] [nvarchar](50) NOT NULL,
   [WeatherDelay] float,
   CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
   ([OriginCityName] ASC))
   GO
   ```

8. A `GO` utasítás megadásakor a rendszer kiértékeli az előző utasításokat.

   A lekérdezés nevű táblát hoz létre **késések**, amely egy fürtözött indexszel rendelkezik.

9. Győződjön meg arról, hogy létrejött-e a tábla használja a következő lekérdezést:

   ```hiveql
   SELECT * FROM information_schema.tables
   GO
   ```

   A kimenet az alábbi szöveghez hasonló:

   ```
   TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
   databaseName       dbo             delays        BASE TABLE
   ```

10. A tsql eszközből való kilépéshez írja be az `exit` kifejezést az `1>` parancssorba.

## <a name="export-and-load-the-data"></a>Exportálás és az adatok betöltéséhez

Az előző szakaszokban az átalakított adatok a helyen található másolt `abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output`. Ebben a szakaszban használhatja Sqoop exportálja az adatokat a `abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output` az Azure SQL database-ben létrehozott táblához.

1. A következő paranccsal ellenőrizze, hogy a Sqoop látja-e az SQL-adatbázist:

   ```bash
   sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
   ```

   A parancs olyan adatbázisokhoz, beleértve az adatbázist, amelyben létrehozta listáját adja vissza a **késések** tábla.

2. A következő paranccsal exportálhatja az adatokat a **hivesampletable** táblázatból a **késések** tábla:

   ```bash
   sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<file-system-name>@.dfs.core.windows.net/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
   ```

   Sqoop használatával csatlakozik az adatbázis, amely tartalmazza a **késések** táblázat és az adatok exportálása a `/tutorials/flightdelays/output` könyvtárat a a **késések** tábla.

3. Miután a `sqoop` parancs végez, a tsql segédprogrammal az adatbázishoz való csatlakozáshoz:

   ```bash
   TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
   ```

4. Győződjön meg arról, hogy a korábban exportált adatok az alábbi utasítások segítségével a **késések** tábla:

   ```sql
   SELECT * FROM delays
   GO
   ```

   A táblában látnia kell az adatok listáját. A tábla a városok nevét és az egyes városokhoz tartozó átlagos késések idejét tartalmazza.

5. Adja meg `exit` való kilépéshez a tsql-segédprogramot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A jelen oktatóanyagban használt összes erőforrás már van létező. Nincs karbantartás nem szükséges.

## <a name="next-steps"></a>További lépések

További lehetőségek az adatok használata a HDInsight további információkért tekintse meg a következő cikket:

> [!div class="nextstepaction"]
> [A kinyerési, átalakítási és az adatok betöltése az Azure Databricks használatával](./data-lake-storage-use-hdi-cluster.md)
