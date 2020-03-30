---
title: Adatelemzési folyamat működőképessé tétele - Azure
description: Állítsa be és futtassa az új adatok által aktivált példa adatfolyamatot, amely tömör eredményeket eredményez.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/25/2019
ms.openlocfilehash: 16c7af4d66bd550eb4a286de7c86c436b1fe10e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75922660"
---
# <a name="operationalize-a-data-analytics-pipeline"></a>Adatelemzési folyamat üzembe helyezése

*Az adatfolyamatok* számos adatelemzési megoldás alatt állnak. Ahogy a neve is sugallja, az adatfolyamat nyers adatokat vesz fel, szükség szerint tisztítja és átalakítja azokat, majd általában számításokat vagy összesítéseket hajt végre a feldolgozott adatok tárolása előtt. A feldolgozott adatokat az ügyfelek, a jelentések vagy az API-k használják fel. Az adatfolyamatnak megismételhető eredményeket kell biztosítania, akár ütemezés szerint, akár új adatok által kiváltott anamminda.

Ez a cikk ismerteti, hogyan lehet üzembe az adatfolyamatok az ismételhetőség, a HDInsight Hadoop-fürtök oozie használatával. A példaforgatókönyv egy adatfolyamaton vezet keresztül, amely előkészíti és feldolgozza a légitársaságok repülési idősorozatainak adatait.

A következő esetben a bemeneti adatok egy egyhónapos repülési adatokköteget tartalmazó egylapos fájl. Ezek a repülési adatok olyan információkat tartalmaznak, mint a kiindulási és a célrepülőtér, a repült mérföldek, az indulási és érkezési idők stb. Ezzel a folyamattal a napi légitársaságteljesítmény összegzése, ahol minden egyes légitársaság nak minden nap egy sora van az átlagos indulási és érkezési késésekkel percben, és az adott napon repült összes mérföld.

| YEAR | MONTH | DAY_OF_MONTH | Fuvarozó |AVG_DEP_DELAY | AVG_ARR_DELAY |TOTAL_DISTANCE |
| --- | --- | --- | --- | --- | --- | --- |
| 2017 | 1 | 3 | Aa | 10.142229 | 7.862926 | 2644539 |
| 2017 | 1 | 3 | AS | 9.435449 | 5.482143 | 572289 |
| 2017 | 1 | 3 | Dl | 6.935409 | -2.1893024 | 1909696 |

A példafolyamat megvárja, amíg egy új időszak repülési adatai megérkeznek, majd tárolja a részletes repülési információkat az Apache Hive adattárházában a hosszú távú elemzésekhez. A folyamat egy sokkal kisebb adatkészletet is létrehoz, amely csak a napi repülési adatokat foglalja össze. Ezt a napi repülési összefoglaló adatokat a rendszer elküldi egy SQL-adatbázisnak, hogy jelentéseket biztosítson, például egy webhelyről.

Az alábbi ábra a példafolyamatot mutatja be.

![HDI repülési példa adatfolyamat – áttekintés](./media/hdinsight-operationalize-data-pipeline/flight-pipeline-overview.png)

## <a name="apache-oozie-solution-overview"></a>Az Apache Oozie megoldás áttekintése

Ez a folyamat egy HDInsight Hadoop-fürtön futó Apache Oozie-t használ.

Az Oozie a folyamatokat *műveletek*, *munkafolyamatok*és *koordinátorok*szerint írja le. A műveletek határozzák meg a ténylegesen végrehajtandó munkát, például egy Hive-lekérdezés futtatását. A munkafolyamatok határozzák meg a műveletek sorrendjét. A koordinátorok határozzák meg a munkafolyamat futtatásának ütemezését. A koordinátorok az új adatok rendelkezésre állását is megvárhatják, mielőtt elindítanák a munkafolyamat egy példányát.

Az alábbi ábrán látható a példa Oozie-folyamat magas szintű tervezése.

![Oozie Flight példa adatfolyamat](./media/hdinsight-operationalize-data-pipeline/pipeline-overview-oozie.png)

## <a name="provision-azure-resources"></a>Azure-erőforrások kiépítése

Ehhez a folyamathoz egy Azure SQL-adatbázis és egy HDInsight Hadoop-fürt szükséges ugyanazon a helyen. Az Azure SQL Database tárolja a folyamat által előállított összegző adatokat és az Oozie metaadat-tárolót.

### <a name="provision-azure-sql-database"></a>Azure SQL-adatbázis kiépítése

1. Hozzon létre egy Azure SQL-adatbázist. Lásd: [Azure SQL-adatbázis létrehozása az Azure Portalon.](../sql-database/sql-database-single-database-get-started.md)

1. Annak érdekében, hogy a HDInsight-fürt hozzáférhessen a csatlakoztatott Azure SQL Database-adatbázishoz, konfigurálja az Azure SQL Database tűzfalszabályait, hogy az Azure-szolgáltatások és -erőforrások hozzáférhessenek a kiszolgálóhoz. Ezt a beállítást az Azure Portalon engedélyezheti a **Kiszolgálótűzfal beállítása**lehetőséget, és **az** **Azure-szolgáltatások és -erőforrások engedélyezése az Azure-szolgáltatások és -erőforrások** alatt az Azure SQL Database-kiszolgáló vagy -adatbázis kiszolgálóeléréséhez lehetőséget választva. További információt az [IP-tűzfalszabályok létrehozása és kezelése](../sql-database/sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)című témakörben talál.

1. A [Lekérdezésszerkesztő](../sql-database/sql-database-single-database-get-started.md#query-the-database) vel hajtsa végre `dailyflights` a következő SQL-utasításokat a folyamat egyes futtatásaiból származó összesített adatokat tároló tábla létrehozásához.

    ```sql
    CREATE TABLE dailyflights
    (
        YEAR INT,
        MONTH INT,
        DAY_OF_MONTH INT,
        CARRIER CHAR(2),
        AVG_DEP_DELAY FLOAT,
        AVG_ARR_DELAY FLOAT,
        TOTAL_DISTANCE FLOAT
    )
    GO

    CREATE CLUSTERED INDEX dailyflights_clustered_index on dailyflights(YEAR,MONTH,DAY_OF_MONTH,CARRIER)
    GO
    ```

Az Azure SQL Database most már készen áll.

### <a name="provision-an-apache-hadoop-cluster"></a>Apache Hadoop-fürt kiépítése

Hozzon létre egy Apache Hadoop-fürtöt egy egyéni metatárolóval. A fürt létrehozása során a portálon, a **Tárolás** lapon győződjön meg arról, hogy válassza ki az SQL-adatbázis t **metastore beállítások .** A metatároló kiválasztásáról további információt az [Egyéni metatár kiválasztása](./hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)a fürt létrehozása során című témakörben talál. A fürt létrehozásáról további információt az [Első lépések a HDInsight linuxos alkalmazásával című](hadoop/apache-hadoop-linux-tutorial-get-started.md)témakörben talál.

## <a name="verify-ssh-tunneling-set-up"></a>SSH-bújtatás beállításának ellenőrzése

Ha az Oozie webkonzol segítségével szeretné megtekinteni a koordinátor és a munkafolyamat-példányok állapotát, állítson be egy SSH-alagutat a HDInsight-fürthöz. További információ: [SSH Tunnel](hdinsight-linux-ambari-ssh-tunnel.md).

> [!NOTE]  
> A Chrome-ot a [Foxy Proxy](https://getfoxyproxy.org/) kiterjesztéssel is használhatja a fürt webes erőforrásainak böngészéséhez az SSH-alagúton keresztül. Konfigurálja úgy, hogy az `localhost` összes kérést a 9876-os porton keresztül proxyként használja. Ez a megközelítés kompatibilis a Windows Alrendszer Linux, más néven Bash a Windows 10.

1. A következő paranccsal nyisson meg egy `CLUSTERNAME` SSH-alagutat a fürthöz, ahol a fürt neve szerepel:

    ```cmd
    ssh -C2qTnNf -D 9876 sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Ellenőrizze, hogy az alagút működőképes-e, ha a fejcsomóponton lévő Ambari-ra navigál, és a következő területre navigál:

    `http://headnodehost:8080`

1. Az **Oozie webkonzol** ambariból történő eléréséhez keresse meg az **Oozie** > **gyorshivatkozások** at > [Active server] > **Oozie Web Felhasználói felületen.**

## <a name="configure-hive"></a>Hive konfigurálása

### <a name="upload-data"></a>Adatok feltöltése

1. Töltsön le egy példa CSV-fájlt, amely egy hónap repülési adatait tartalmazza. Töltse le `2017-01-FlightData.zip` a ZIP-fájlt a [HDInsight GitHub tárházból,](https://github.com/hdinsight/hdinsight-dev-guide) és csomagolja ki a CSV fájlba. `2017-01-FlightData.csv`

1. Másolja ezt a CSV-fájlt a HDInsight-fürthöz csatlakoztatott Azure `/example/data/flights` Storage-fiókba, és helyezze el a mappában.

    1. Az SCP használatával másolja a fájlokat a helyi számítógépről a HDInsight-fürt főcsomópontjának helyi tárolójára.

        ```cmd
        scp ./2017-01-FlightData.csv sshuser@CLUSTERNAME-ssh.azurehdinsight.net:2017-01-FlightData.csv
        ```

    1. Az [ssh paranccsal](./hdinsight-hadoop-linux-use-ssh-unix.md) csatlakozhat a fürthöz. Az alábbi parancs szerkesztése a fürt nevének cseréjével, `CLUSTERNAME` majd írja be a parancsot:

        ```cmd
        ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
        ```

    1. Az ssh munkamenetből a HDFS paranccsal másolja a fájlt a fő csomópont helyi tárolójáról az Azure Storage-ba.

        ```bash
        hadoop fs -mkdir /example/data/flights
        hdfs dfs -put ./2017-01-FlightData.csv /example/data/flights/2017-01-FlightData.csv
        ```

### <a name="create-tables"></a>Táblázatok létrehozása

A mintaadatok már elérhetők. A folyamat azonban két Hive-táblát igényel a feldolgozáshoz, egyet a bejövő adatokhoz (`rawFlights`) és egyet az összesített adatokhoz ( ).`flights` Hozza létre ezeket a táblázatokat ambari az alábbiak szerint.

1. Jelentkezzen be az Ambari-ba `http://headnodehost:8080`a .

2. A szolgáltatások listájában válassza a **Hive**lehetőséget.

    ![Az Apache Ambari szolgáltatások listája a Hive kiválasztása](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive.png)

3. Válassza az **Ugrás a megtekintésre** lehetőséget a Hive View 2.0 címke mellett.

    ![Ambari Apache Hive összefoglaló lista](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-summary.png)

4. A lekérdezés szövegterületén illessze be a következő `rawFlights` állításokat a tábla létrehozásához. A `rawFlights` tábla egy séma-on-read a CSV-fájlok at a mappában az `/example/data/flights` Azure Storage-ban.

    ```sql
    CREATE EXTERNAL TABLE IF NOT EXISTS rawflights (
        YEAR INT,
        MONTH INT,
        DAY_OF_MONTH INT,
        FL_DATE STRING,
        CARRIER STRING,
        FL_NUM STRING,
        ORIGIN STRING,
        DEST STRING,
        DEP_DELAY FLOAT,
        ARR_DELAY FLOAT,
        ACTUAL_ELAPSED_TIME FLOAT,
        DISTANCE FLOAT)
    ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
    WITH SERDEPROPERTIES
    (
        "separatorChar" = ",",
        "quoteChar"     = "\""
    )
    LOCATION '/example/data/flights'
    ```

5. A tábla létrehozásához válassza a **Végrehajtás** lehetőséget.

    ![hdi ambari szolgáltatások struktúra lekérdezés](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-query.png)

6. A `flights` tábla létrehozásához cserélje le a lekérdezés szövegterületén lévő szöveget a következő utasításokkal. A `flights` tábla egy Hive által felügyelt tábla, amely a partíciók adatok at év, hónap és hónap napja. Ez a táblázat az összes korábbi repülési adatot tartalmazza, a legalacsonyabb részletességgel, amely járatonként egy sor forrásadataiban szerepel.

    ```sql
    SET hive.exec.dynamic.partition.mode=nonstrict;

    CREATE TABLE flights
    (
        FL_DATE STRING,
        CARRIER STRING,
        FL_NUM STRING,
        ORIGIN STRING,
        DEST STRING,
        DEP_DELAY FLOAT,
        ARR_DELAY FLOAT,
        ACTUAL_ELAPSED_TIME FLOAT,
        DISTANCE FLOAT
    )
    PARTITIONED BY (YEAR INT, MONTH INT, DAY_OF_MONTH INT)
    ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
    WITH SERDEPROPERTIES 
    (
        "separatorChar" = ",",
        "quoteChar"     = "\""
    );
    ```

7. A tábla létrehozásához válassza a **Végrehajtás** lehetőséget.

## <a name="create-the-oozie-workflow"></a>Az Oozie-munkafolyamat létrehozása

A folyamatok általában kötegekben dolgozzák fel az adatokat egy adott időintervallumban. Ebben az esetben a folyamat naponta dolgozza fel a repülési adatokat. Ez a megközelítés lehetővé teszi, hogy a bemeneti CSV-fájlok naponta, hetente, havonta vagy évente érkezzenek.

A mintamunkafolyamat napról napra dolgozza fel a repülési adatokat, három fő lépésben:

1. Futtatjon egy Hive-lekérdezést az adott napi dátumtartomány adatainak kinyeréséhez a `rawFlights` tábla `flights` által képviselt forrás CSV-fájlból, és szúrja be az adatokat a táblába.
2. Futtatjon egy Hive-lekérdezést, hogy dinamikusan hozzon létre egy átmeneti táblát a Hive-ban a napra, amely tartalmazza a repülési adatok egy példányát a nap és a szállító szerint összegezve.
3. Az Apache Sqoop segítségével másolja az összes adatot a napi `dailyflights` átmeneti tábla Hive az Azure SQL Database céltáblába. A Sqoop beolvassa a forrássorokat az Azure Storage-ban található Hive-tábla mögötti adatokból, és jdbc-kapcsolaton keresztül betölti azokat az SQL-adatbázisba.

Ezt a három lépést egy Oozie-munkafolyamat koordinálja.

1. A helyi munkaállomásról hozzon `job.properties`létre egy fájlt. A fájl kiindulási tartalmaként használja az alábbi szöveget.
Ezután frissítse az adott környezet értékeit. A szöveg alatti táblázat összefoglalja az egyes tulajdonságokat, és jelzi, hogy hol találhatók a saját környezetére vonatkozó értékek.

    ```text
    nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
    jobTracker=[ACTIVERESOURCEMANAGER]:8050
    queueName=default
    oozie.use.system.libpath=true
    appBase=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie
    oozie.wf.application.path=${appBase}/load_flights_by_day
    hiveScriptLoadPartition=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-load-flights-partition.hql
    hiveScriptCreateDailyTable=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-create-daily-summary-table.hql
    hiveDailyTableName=dailyflights${year}${month}${day}
    hiveDataFolder=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/day/${year}/${month}/${day}
    sqlDatabaseConnectionString="jdbc:sqlserver://[SERVERNAME].database.windows.net;user=[USERNAME];password=[PASSWORD];database=[DATABASENAME]"
    sqlDatabaseTableName=dailyflights
    year=2017
    month=01
    day=03
    ```

    | Tulajdonság | Értékforrása |
    | --- | --- |
    | nameNode (névnode) | A HDInsight-fürthöz csatlakoztatott Azure Storage Container teljes elérési útja. |
    | jobTracker | Az aktív fürt YARN-főcsomópontjának belső állomásneve. Az Ambari kezdőlapján válassza a YARN elemet a szolgáltatások listájából, majd válassza az Aktív erőforrás-kezelő lehetőséget. Az állomásnév URI a lap tetején jelenik meg. Fűzte hozzá a 8050-es portot. |
    | queueName (sornév) | A Hive-műveletek ütemezésekéneknél használt YARN-várólista neve. Hagyja alapértelmezettként. |
    | oozie.use.system.libpath | Hagyjuk igazként. |
    | appBase | Az Azure Storage almappájának elérési útja, ahol telepíti az Oozie-munkafolyamatot és a támogató fájlokat. |
    | oozie.wf.application.path | A futtatni hozandó `workflow.xml` Oozie-munkafolyamat helye. |
    | struktúrascriptloadpartíció | Az Azure Storage elérési útja a `hive-load-flights-partition.hql`Hive lekérdezésfájlhoz. |
    | hiveScriptCreateDailyTable tábla | Az Azure Storage elérési útja a `hive-create-daily-summary-table.hql`Hive lekérdezésfájlhoz. |
    | hiveDailyTableName | Az átmeneti táblához használandó dinamikusan létrehozott név. |
    | hiveDataFolder mappa | Az azure storage-ban az átmeneti tábla által tárolt adatok elérési útja. |
    | sqlDatabaseConnectionString | A JDBC szintaxis kapcsolat ia az Azure SQL-adatbázis. |
    | sqlDatabaseTableName | A tábla neve az Azure SQL Database-ben, amelybe az összesítő sorokat beszúrja. Hagyja `dailyflights`a . |
    | év | Annak a napnak az éveleme, amelyre a repülési összefoglalókat kiszámítják. Menj úgy, ahogy van. |
    | hónap | Annak a napnak a hónapeleme, amelyre a repülési összefoglalókat kiszámítják. Menj úgy, ahogy van. |
    | nap | Annak a napnak a hónap napja összetevője, amelyre a repülési összefoglalókat kiszámítják. Menj úgy, ahogy van. |

1. A helyi munkaállomásról hozzon `hive-load-flights-partition.hql`létre egy fájlt. Használja az alábbi kódot a fájl tartalmaként.

    ```sql
    SET hive.exec.dynamic.partition.mode=nonstrict;

    INSERT OVERWRITE TABLE flights
    PARTITION (YEAR, MONTH, DAY_OF_MONTH)
    SELECT 
        FL_DATE,
        CARRIER,
        FL_NUM,
        ORIGIN,
        DEST,
        DEP_DELAY,
        ARR_DELAY,
        ACTUAL_ELAPSED_TIME,
        DISTANCE,
        YEAR,
        MONTH,
        DAY_OF_MONTH
    FROM rawflights
    WHERE year = ${year} AND month = ${month} AND day_of_month = ${day};
    ```

    Az Oozie változók `${variableName}`a szintaxist használják. Ezek a változók `job.properties` a fájlban vannak beállítva. Az Oozie a tényleges értékeket váltja fel futásidőben.

1. A helyi munkaállomásról hozzon `hive-create-daily-summary-table.hql`létre egy fájlt. Használja az alábbi kódot a fájl tartalmaként.

    ```sql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}
    (
        YEAR INT,
        MONTH INT,
        DAY_OF_MONTH INT,
        CARRIER STRING,
        AVG_DEP_DELAY FLOAT,
        AVG_ARR_DELAY FLOAT,
        TOTAL_DISTANCE FLOAT
    )
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName}
    SELECT  year, month, day_of_month, carrier, avg(dep_delay) avg_dep_delay, 
            avg(arr_delay) avg_arr_delay, sum(distance) total_distance 
    FROM flights
    GROUP BY year, month, day_of_month, carrier 
    HAVING year = ${year} AND month = ${month} AND day_of_month = ${day};
    ```

    Ez a lekérdezés létrehoz egy átmeneti táblát, amely csak az egy napra vonatkozó összesített adatokat tárolja, vegye figyelembe a SELECT utasítást, amely kiszámítja a szállítmányozó által napi átlagos késéseket és teljes távolságot. A táblába beszúrt adatok egy ismert helyen (a hiveDataFolder változó által jelzett elérési úton) tárolódnak, így a következő lépésben a Sqoop forrásaként használhatók.

1. A helyi munkaállomásról hozzon `workflow.xml`létre egy fájlt. Használja az alábbi kódot a fájl tartalmaként. Ezek a fenti lépések az Oozie munkafolyamat-fájlban külön műveletekként vannak kifejezve.

    ```xml
    <workflow-app name="loadflightstable" xmlns="uri:oozie:workflow:0.5">
        <start to = "RunHiveLoadFlightsScript"/>
        <action name="RunHiveLoadFlightsScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.job.queue.name</name>
                    <value>${queueName}</value>
                </property>
                </configuration>
                <script>${hiveScriptLoadPartition}</script>
                <param>year=${year}</param>
                <param>month=${month}</param>
                <param>day=${day}</param>
            </hive>
            <ok to="RunHiveCreateDailyFlightTableScript"/>
            <error to="fail"/>
        </action>
    
        <action name="RunHiveCreateDailyFlightTableScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.job.queue.name</name>
                    <value>${queueName}</value>
                </property>
                </configuration>
                <script>${hiveScriptCreateDailyTable}</script>
                <param>hiveTableName=${hiveDailyTableName}</param>
                <param>year=${year}</param>
                <param>month=${month}</param>
                <param>day=${day}</param>
                <param>hiveDataFolder=${hiveDataFolder}/${year}/${month}/${day}</param>
            </hive>
            <ok to="RunSqoopExport"/>
            <error to="fail"/>
        </action>
    
        <action name="RunSqoopExport">
            <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.compress.map.output</name>
                    <value>true</value>
                </property>
                </configuration>
                <arg>export</arg>
                <arg>--connect</arg>
                <arg>${sqlDatabaseConnectionString}</arg>
                <arg>--table</arg>
                <arg>${sqlDatabaseTableName}</arg>
                <arg>--export-dir</arg>
                <arg>${hiveDataFolder}/${year}/${month}/${day}</arg>
                <arg>-m</arg>
                <arg>1</arg>
                <arg>--input-fields-terminated-by</arg>
                <arg>"\t"</arg>
                <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
                </sqoop>
            <ok to="end"/>
            <error to="fail"/>
        </action>
        <kill name="fail">
            <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>
        <end name="end"/>
    </workflow-app>
    ```

A két Hive-lekérdezések az Azure Storage-ban való elérési útjukon `job.properties` érhetők el, és a fennmaradó változóértékeket a fájl biztosítja. Ez a fájl úgy konfigurálja a munkafolyamatot, hogy a 2017.

## <a name="deploy-and-run-the-oozie-workflow"></a>Az Oozie-munkafolyamat üzembe helyezése és futtatása

Használja`workflow.xml`az SCP-t a bash munkamenetből az Oozie-munkafolyamat ( ), a Hive-lekérdezések (`hive-load-flights-partition.hql` és `hive-create-daily-summary-table.hql`a ) és a feladat konfigurációja (`job.properties`telepítéséhez.  Az Oozie-ban `job.properties` csak a fájl létezhet a fejcsomópont helyi tárolóján. Az összes többi fájlt a HDFS-ben kell tárolni, ebben az esetben az Azure Storage-ban. A munkafolyamat által használt Sqoop művelet egy JDBC-illesztőprogramtól függ az SQL-adatbázissal való kommunikációhoz, amelyet a főcsomópontról a HDFS-be kell másolni.

1. Hozza `load_flights_by_day` létre az almappát a felhasználó elérési útja alatt a fejcsomópont helyi tárolójában. A megnyitott ssh munkamenetből hajtsa végre a következő parancsot:

    ```bash
    mkdir load_flights_by_day
    ```

1. Másolja az aktuális könyvtár összes `workflow.xml` `job.properties` fájlját (a `load_flights_by_day` és fájlokat) az almappába. A helyi munkaállomásról hajtsa végre a következő parancsot:

    ```cmd
    scp ./* sshuser@CLUSTERNAME-ssh.azurehdinsight.net:load_flights_by_day
    ```

1. Munkafolyamat-fájlok másolása a HDFS fájlba. A megnyitott ssh munkamenetből hajtsa végre a következő parancsokat:

    ```bash
    cd load_flights_by_day
    hadoop fs -mkdir -p /oozie/load_flights_by_day
    hdfs dfs -put ./* /oozie/load_flights_by_day
    ```

1. Másolás `mssql-jdbc-7.0.0.jre8.jar` a helyi főcsomópontról a HDFS munkafolyamat-mappájába. Szükség szerint vizsgálja felül a parancsot, ha a fürt másik jar fájlt tartalmaz. Szükség `workflow.xml` szerint vizsgálja felül, hogy tükrözze egy másik jar fájlt. A megnyitott ssh munkamenetből hajtsa végre a következő parancsot:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /oozie/load_flights_by_day
    ```

1. Futtassa a munkafolyamatot. A megnyitott ssh munkamenetből hajtsa végre a következő parancsot:

    ```bash
    oozie job -config job.properties -run
    ```

1. Figyelje meg az állapotát az Oozie webkonzol on. Az Ambari területen válassza az **Oozie**, **A gyorshivatkozások**, majd az **Oozie webkonzol lehetőséget.** A **Munkafolyamat-feladatok** lapon válassza a **Minden feladat lehetőséget.**

    ![hdi oozie webkonzol munkafolyamatok](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-workflows.png)

1. Ha az állapot sikeres, a beszúrt sorok megtekintéséhez kérdezze le az SQL Database táblát. Az Azure Portal használatával keresse meg az SQL-adatbázis ablaktábláját, válassza az **Eszközök**lehetőséget, és nyissa meg a **Lekérdezésszerkesztőt.**

        SELECT * FROM dailyflights

Most, hogy a munkafolyamat egyetlen tesztnapon fut, ezt a munkafolyamatot egy koordinátorral csomagolhatja, amely úgy ütemezi a munkafolyamatot, hogy naponta fusson.

## <a name="run-the-workflow-with-a-coordinator"></a>A munkafolyamat futtatása koordinátorral

Ha úgy szeretné ütemezni ezt a munkafolyamatot, hogy az naponta (vagy a dátumtartomány összes napján) futjon, használhat koordinátort. A koordinátort egy XML-fájl határozza `coordinator.xml`meg, például:

```xml
<coordinator-app name="daily_export" start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" timezone="UTC" xmlns="uri:oozie:coordinator:0.4">
    <datasets>
        <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
            <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
            <done-flag></done-flag>
        </dataset>
    </datasets>
    <input-events>
        <data-in name="event_input1" dataset="ds_input1">
            <instance>${coord:current(0)}</instance>
        </data-in>
    </input-events>
    <action>
        <workflow>
            <app-path>${appBase}/load_flights_by_day</app-path>
            <configuration>
                <property>
                    <name>year</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'yyyy')}</value>
                </property>
                <property>
                    <name>month</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'MM')}</value>
                </property>
                <property>
                    <name>day</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>hiveScriptLoadPartition</name>
                    <value>${hiveScriptLoadPartition}</value>
                </property>
                <property>
                    <name>hiveScriptCreateDailyTable</name>
                    <value>${hiveScriptCreateDailyTable}</value>
                </property>
                <property>
                    <name>hiveDailyTableNamePrefix</name>
                    <value>${hiveDailyTableNamePrefix}</value>
                </property>
                <property>
                    <name>hiveDailyTableName</name>
                    <value>${hiveDailyTableNamePrefix}${coord:formatTime(coord:nominalTime(), 'yyyy')}${coord:formatTime(coord:nominalTime(), 'MM')}${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>hiveDataFolderPrefix</name>
                    <value>${hiveDataFolderPrefix}</value>
                </property>
                <property>
                    <name>hiveDataFolder</name>
                    <value>${hiveDataFolderPrefix}${coord:formatTime(coord:nominalTime(), 'yyyy')}/${coord:formatTime(coord:nominalTime(), 'MM')}/${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>sqlDatabaseConnectionString</name>
                    <value>${sqlDatabaseConnectionString}</value>
                </property>
                <property>
                    <name>sqlDatabaseTableName</name>
                    <value>${sqlDatabaseTableName}</value>
                </property>
            </configuration>
        </workflow>
    </action>
</coordinator-app>
```

Mint látható, a koordinátor többsége csak átadja a konfigurációs adatokat a munkafolyamat-példány. Van azonban néhány fontos elem, amit ki kell hívni.

* 1. pont: Maga `start` `coordinator-app` az elem és `end` attribútumai szabályozzák azt az időintervallumot, amely alatt a koordinátor fut.

    ```
    <coordinator-app ... start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" ...>
    ```

    A koordinátor felelős a műveletek `start` `end` ütemezésért a dátumtartományon belül, `frequency` az attribútum által megadott intervallumnak megfelelően. Minden ütemezett művelet a konfigurált módon futtatja a munkafolyamatot. A fenti koordinátordefinícióban a koordinátor úgy van beállítva, hogy 2017. A gyakoriságot az [Oozie Expression Language](https://oozie.apache.org/docs/4.2.0/CoordinatorFunctionalSpec.html#a4.4._Frequency_and_Time-Period_Representation) frequency `${coord:days(1)}`expression határozza meg. Ez azt eredményezi, hogy a koordinátor naponta egyszer ütemez egy műveletet (és így a munkafolyamatot). A múltbeli dátumtartományok esetében, mint ebben a példában, a művelet ütemezése késedelem nélkül fog futni. A művelet futtatásának ütemezése szerinti kezdést *nominális időnek nevezzük.* 2017. január 1-i adatok feldolgozásához például a koordinátor 2017-01-01T00:00:00 GMT névleges idővel ütemezi a műveletet.

* 2. pont: A munkafolyamat dátumtartományán belül az `dataset` elem meghatározza, hogy hol keresse meg a HDFS-ben az adatokat egy adott dátumtartományhoz, és beállítja, hogy az Oozie hogyan határozza meg, hogy az adatok még mindig rendelkezésre állnak-e feldolgozásra.

    ```xml
    <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
        <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
        <done-flag></done-flag>
    </dataset>
    ```

    A HDFS-ben lévő adatok elérési útja dinamikusan épül `uri-template` fel az elemben megadott kifejezésnek megfelelően. Ebben a koordinátorban egy napos gyakoriságot is használ az adatkészlet. Míg a koordinátor elem vezérlőjének kezdő és záró dátumai a műveletek `initial-instance` ütemezésekor (és azok névleges időpontjának meghatározásakor) `frequency` `uri-template`szabályozzák a létrehozásához használt dátum kiszámítását. Ebben az esetben állítsa be az első példányt egy nappal a koordinátor kezdete előtt, hogy megbizonyosodjon arról, hogy felveszi az első napi (1/1/2017) értékű adatokat. Az adatkészlet dátumszámítása előregördül `initial-instance` az adatkészlet gyakoriságának (egy nap) lépésekben történő (12/31/2016) értékéből, amíg meg nem találja a legutóbbi dátumot, amely nem haladja meg a koordinátor által beállított névleges időt (2017-01-01T00:00:00 GMT az első művelethez).

    Az `done-flag` üres elem azt jelzi, hogy amikor oozie ellenőrzi a bemeneti adatok jelenlétét a megadott időpontban, Oozie határozza meg az adatokat, hogy rendelkezésre áll-e egy könyvtár vagy fájl jelenléte. Ebben az esetben ez egy csv fájl jelenléte. Ha van jelen egy csv-fájl, az Oozie feltételezi, hogy az adatok készen állnak, és elindítja a munkafolyamat-példányt a fájl feldolgozásához. Ha nincs jelen csv-fájl, az Oozie feltételezi, hogy az adatok még nem állnak készen, és a munkafolyamat futtatása várakozó állapotba kerül.

* 3. pont: Az `data-in` elem megadja azt az időbélyeget, amelyet névleges időként kell használni a társított adatkészlet értékeinek `uri-template` cseréjekor.

    ```xml
    <data-in name="event_input1" dataset="ds_input1">
        <instance>${coord:current(0)}</instance>
    </data-in>
    ```

    Ebben az esetben állítsa be `${coord:current(0)}`a példányt a kifejezésre, amely a művelet névleges idejét használja a koordinátor által eredetileg ütemezett módon. Más szóval, amikor a koordinátor a műveletet 2017.01.01 névleges idővel ütemezi, akkor az URI-sablonban az ÉV (2017) és a MONTH (01) változók felváltására a 01/01 01. Miután kiszámította az URI-sablont ehhez a példányhoz, az Oozie ellenőrzi, hogy a várt könyvtár vagy fájl elérhető-e, és ennek megfelelően ütemezi-e a munkafolyamat következő futtatását.

Az előző három pont együttesen olyan helyzetet eredményez, amelyben a koordinátor a forrásadatok napi feldolgozását ütemezi.

* 1. pont: A koordinátor a 2017-01-01-es nominális dátummal kezdődik.

* 2. pont: Az Oozie `sourceDataFolder/2017-01-FlightData.csv`a rendszerben elérhető adatokat keres.

* 3. pont: Amikor oozie megtalálja a fájlt, ütemezi a munkafolyamat egy példányát, amely feldolgozza a 2017-01-01-es adatokat. Az Oozie ezután folytatja a feldolgozást a 2017-01-02-re. Ez az értékelés a 2017-01-05-ig, de nem tartalmazza.

A munkafolyamatokhoz ugyanúgy a koordinátor konfigurációja `job.properties` egy fájlban van definiálva, amely a munkafolyamat által használt beállítások at használja.

```text
nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
jobTracker=[ACTIVERESOURCEMANAGER]:8050
queueName=default
oozie.use.system.libpath=true
appBase=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie
oozie.coord.application.path=${appBase}
sourceDataFolder=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/
hiveScriptLoadPartition=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-load-flights-partition.hql
hiveScriptCreateDailyTable=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-create-daily-summary-table.hql
hiveDailyTableNamePrefix=dailyflights
hiveDataFolderPrefix=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/day/
sqlDatabaseConnectionString="jdbc:sqlserver://[SERVERNAME].database.windows.net;user=[USERNAME];password=[PASSWORD];database=[DATABASENAME]"
sqlDatabaseTableName=dailyflights
```

A fájlban csak a `job.properties` következő új tulajdonságok találhatók:

| Tulajdonság | Értékforrása |
| --- | --- |
| oozie.coord.application.path | A futtatni hozandó `coordinator.xml` Oozie-koordinátort tartalmazó fájl helyét jelzi. |
| hiveDailyTableNamePrefix | Az átmeneti tábla táblanevének dinamikus létrehozásakor használt előtag. |
| hiveDataFolderPrefix | Annak az elérési útnak az előtagja, ahol az összes átmeneti tábla tárolódik. |

## <a name="deploy-and-run-the-oozie-coordinator"></a>Az Oozie-koordinátor telepítése és futtatása

Ha a folyamatot koordinátorral szeretné futtatni, folytassa a munkafolyamathoz hasonló módon, kivéve, ha a munkafolyamatot tartalmazó mappa felett egy szinttel feljebb dolgozik. Ez a mappakonvenció elválasztja a koordinátorokat a lemezen lévő munkafolyamatoktól, így egy koordinátort társíthat különböző alárendelt munkafolyamatokhoz.

1. A helyi számítógép SCP használatával másolja a koordinátorfájlokat a fürt főcsomópontjának helyi tárolójára.

    ```bash
    scp ./* sshuser@CLUSTERNAME-ssh.azurehdinsight.net:~
    ```

2. SSH-t a fejedcsomópontba.

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Másolja a koordinátor fájljait a HDFS fájlba.

    ```bash
    hdfs dfs -put ./* /oozie/
    ```

4. Futtasd le a koordinátort.

    ```bash
    oozie job -config job.properties -run
    ```

5. Ellenőrizze az állapotot az Oozie webkonzolon, ezúttal válassza a **Koordinátori feladatok** lapot, majd **az Összes feladatot**.

    ![Oozie webkonzol koordinátori feladatok](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-jobs.png)

6. Jelöljön ki egy koordinátorpéldányt az ütemezett műveletek listájának megjelenítéséhez. Ebben az esetben négy műveletet kell látnia, amelyek névleges ideje az 1/1/2017 és 1/4/2017 között van.

    ![Oozie webkonzol koordinátori feladat](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-instance.png)

    A listában szereplő minden egyes művelet a munkafolyamat egy olyan példányának felel meg, amely egy napi adatértékben dolgozza fel, ahol az adott nap kezdetét a névleges idő jelzi.

## <a name="next-steps"></a>További lépések

[Apache Oozie dokumentáció](https://oozie.apache.org/docs/4.2.0/index.html)
