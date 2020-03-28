---
title: 'Oktatóanyag: Adatok kinyerése, átalakítása és betöltése az Azure HDInsight használatával'
description: Ebben az oktatóanyagban megtudhatja, hogyan nyerheti ki az adatokat egy nyers CSV-adatkészletből, hogyan alakíthatja át azokat az Azure HDInsight Apache Hive használatával, majd az átalakított adatokat az Azure SQL Database-be a Sqoop használatával.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: c9ed675dc970b093f6407d15b3db2ac2668c626b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74327561"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-hdinsight"></a>Oktatóanyag: Adatok kinyerése, átalakítása és betöltése az Azure HDInsight használatával

Ebben az oktatóanyagban etl-műveletet hajt végre: adatok kibontása, átalakítása és betöltése. Egy nyers CSV-adatfájlt importál, importál egy Azure HDInsight-fürtbe, átalakítja azt az Apache Hive segítségével, és betölti egy Azure SQL-adatbázisba az Apache Sqoop segítségével.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az adatok kibontása és feltöltése egy HDInsight-fürtbe.
> * Az adatok átalakítása apache hive használatával.
> * Töltse be az adatokat egy Azure SQL-adatbázis sqoop használatával.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

* **A HDInsighthoz konfigurált Azure Data Lake Storage Gen2 tárfiók**

    Lásd: [Azure Data Lake Storage Gen2 használata Az Azure HDInsight-fürtökkel.](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)

* **Linux alapú Hadoop-fürt a HDInsight-on**

    Tekintse meg [a rövid útmutatót: Az Apache Hadoop és az Apache Hive használatának első lépései az Azure HDInsightban az Azure Portalon.](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-linux-create-cluster-get-started-portal)

* **Azure SQL Database:** Egy Azure SQL-adatbázist használ céladattárként. Ha még nem rendelkezik SQL-adatbázissal, olvassa el az [Azure SQL-adatbázis az Azure Portalon történő létrehozását](../../sql-database/sql-database-get-started.md) ismertető cikket.

* **Azure CLI:** Ha még nem telepítette az Azure CLI-t, [olvassa el az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)című témakört.

* **Biztonságos rendszerhéj (SSH) ügyfél**: További információ: [Csatlakozás a HDInsighthoz (Hadoop) az SSH használatával](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>A repülőjárat-adatok letöltése

1. Nyissa meg a [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time) (Kutatási és Innovációs Műszaki Felügyelőség, Közlekedési Statisztikai Hivatal) oldalt.

2. Az oldalon válassza ki a következő értékeket:

   | Név | Érték |
   | --- | --- |
   | Filter Year (Szűrési év) |2013 |
   | Filter Period (Szűrési időszak) |January |
   | Mezők |Év, FlightDate, Reporting_Airline, IATA_CODE_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   
   Az összes többi mező jelölését törölje.

3. Válassza a **Download** (Letöltés) lehetőséget. Egy .zip fájlt kap, amely a kiválasztott adatmezőket tartalmazza.

## <a name="extract-and-upload-the-data"></a>Az adatok kinyerése és feltöltése

Ebben a szakaszban adatokat tölthet fel a HDInsight-fürtbe, majd másolja ezeket az adatokat a Data Lake Storage Gen2-fiókjába.

1. Nyisson meg egy parancssort, és használja a következő Biztonságos másolás (Scp) parancsot a .zip fájl feltöltéséhez a HDInsight fürtfőcsomópontra:

   ```bash
   scp <file-name>.zip <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net:<file-name.zip>
   ```

   * Cserélje `<file-name>` le a helyőrzőt a .zip fájl nevére.
   * Cserélje `<ssh-user-name>` le a helyőrzőt a HDInsight-fürt SSH bejelentkezési adataira.
   * Cserélje `<cluster-name>` le a helyőrzőt a HDInsight-fürt nevére.

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

   A parancs **.csv** fájlt bont ki.

4. A következő paranccsal hozza létre a Data Lake Storage Gen2 tárolót.

   ```bash
   hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/
   ```

   Cserélje `<container-name>` le a helyőrzőt a tárolónak adni kívánt névre.

   Cserélje `<storage-account-name>` le a helyőrzőt a tárfiók nevére.

5. Könyvtár létrehozásához használja a következő parancsot.

   ```bash
   hdfs dfs -mkdir -p abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data
   ```

6. A *.csv* fájl könyvtárba másolásához használja a következő parancsot:

   ```bash
   hdfs dfs -put "<file-name>.csv" abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data/
   ```

   Ha a fájlnév szóközöket vagy speciális karaktereket tartalmaz, használjon idézőjeleket a fájlnév körül.

## <a name="transform-the-data"></a>Az adatok átalakítása

Ebben a szakaszban a Beeline használatával apache Hive-feladat futtatásához.

Az Apache Hive-feladat részeként importálja az adatokat a .csv fájlból egy **késleltetésnek**nevezett Apache Hive-táblába.

1. A HDInsight-fürthöz már meglévő SSH-parancsból a következő paranccsal hozhat létre és szerkesztheti a **flightdelays.hql**nevű új fájlt:

   ```bash
   nano flightdelays.hql
   ```

2. Módosítsa a következő szöveget a tároló- és tárfiók nevére cserélve a `<container-name>` helyőrzőket. `<storage-account-name>` Ezután másolja és illessze be a szöveget a nanokonzolra a SHIFT billentyű és a jobb egérkattintás gomb jának megnyomásával.

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
    LOCATION 'abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays
    LOCATION 'abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/processed'
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

3. Mentse a fájlt a CTRL+X `Y` billentyűkombinációval, majd írja be a szöveget, amikor a program kéri.

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

   Ez a lekérdezés lekéri azon városok listáját, ahol időjárás miatti késések történtek, valamint a késések átlagos idejét, és menti ezeket az adatokat a következő helyen: `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output`. Később a Sqoop erről a helyről olvassa be az adatokat, amelyeket exportál az Azure SQL Database-be.

7. A Beeline-ból való kilépéshez írja be a parancssorba a `!quit` parancsot.

## <a name="create-a-sql-database-table"></a>SQL Database-tábla létrehozása

Ehhez a művelethez az SQL-adatbáziskiszolgáló nevére van szükség. Hajtsa végre az alábbi lépéseket a kiszolgáló nevének megkereséséhez.

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)

2. Válassza az **SQL-adatbázisok lehetőséget.**

3. Szűrje a használni kívánt adatbázis nevét. A kiszolgáló neve a **Kiszolgáló neve** oszlopban látható.

4. Szűrje a használni kívánt adatbázis nevét. A kiszolgáló neve a **Kiszolgáló neve** oszlopban látható.

    ![Az Azure SQL-kiszolgáló részleteinek beszereznie](./media/data-lake-storage-tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Az Azure SQL-kiszolgáló részleteinek beszereznie")

    Számos módon csatlakozhat az SQL Database-hez, majd hozhat létre egy táblát. A következő lépések során a [FreeTDS](https://www.freetds.org/) eszközt használjuk a HDInsight-fürtről.

5. A FreeTDS telepítéséhez használja a következő parancsot egy, a fürthöz csatlakozó SSH-kapcsolaton:

   ```bash
   sudo apt-get --assume-yes install freetds-dev freetds-bin
   ```

6. A telepítés befejezése után a következő paranccsal csatlakozzon az SQL Database-kiszolgálóhoz.

   ```bash
   TDSVER=8.0 tsql -H '<server-name>.database.windows.net' -U '<admin-login>' -p 1433 -D '<database-name>'
    ```
   * Cserélje `<server-name>` le a helyőrzőt az SQL Database kiszolgáló nevére.

   * Cserélje `<admin-login>` le a helyőrzőt az SQL Database rendszergazdai bejelentkezési adataira.

   * A `<database-name>` helyőrző lecserélése az adatbázis nevére

   Amikor a rendszer kéri, adja meg az SQL Database rendszergazdai bejelentkezésének jelszavát.

   A kimenet a következő szöveghez fog hasonlítani:

   ```
   locale is "en_US.UTF-8"
   locale charset is "UTF-8"
   using default charset "UTF-8"
   Default database being set to sqooptest
   1>
   ```

7. A `1>` kérdésben adja meg a következő utasításokat:

   ```hiveql
   CREATE TABLE [dbo].[delays](
   [OriginCityName] [nvarchar](50) NOT NULL,
   [WeatherDelay] float,
   CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
   ([OriginCityName] ASC))
   GO
   ```

8. A `GO` utasítás megadásakor a rendszer kiértékeli az előző utasításokat.

   A lekérdezés létrehoz egy **delays**nevű táblát, amely fürtözött indexszel rendelkezik.

9. A tábla létrehozásának ellenőrzéséhez használja az alábbi lekérdezést:

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

## <a name="export-and-load-the-data"></a>Adatok exportálása és betöltése

Az előző szakaszokban az átalakított adatokat a `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output`helyen másolta. Ebben a szakaszban a Sqoop segítségével `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output` exportálja az adatokat az Azure SQL-adatbázisban létrehozott táblába.

1. A következő paranccsal ellenőrizze, hogy a Sqoop látja-e az SQL-adatbázist:

   ```bash
   sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
   ```

   A parancs visszaadja az adatbázisok listáját, beleértve azt az adatbázist is, amelyben a **delays** táblát létrehozta.

2. A következő paranccsal exportálhatja az adatokat a **struktúramintatáblázatból** a **delays** táblába:

   ```bash
   sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<container-name>@.dfs.core.windows.net/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
   ```

   A Sqoop csatlakozik a **delays** táblát tartalmazó adatbázishoz, és adatokat exportál a `/tutorials/flightdelays/output` könyvtárból a **delays** táblába.

3. A `sqoop` parancs befejezése után a tsql segédprogrammal csatlakozzon az adatbázishoz:

   ```bash
   TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
   ```

4. A következő utasítások segítségével ellenőrizheti, hogy az adatokat exportálták-e a **delays** táblába:

   ```sql
   SELECT * FROM delays
   GO
   ```

   A táblában látnia kell az adatok listáját. A tábla a városok nevét és az egyes városokhoz tartozó átlagos késések idejét tartalmazza.

5. Adja `exit` meg a tsql segédprogramból való kilépéshez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyagban használt összes erőforrás már meglévő. Nincs szükség tisztításra.

## <a name="next-steps"></a>További lépések

Az adatok HDInsight-ban való kezelésének további módjairól az alábbi cikkben olvashat:

> [!div class="nextstepaction"]
> [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
