---
title: Adatelemzési folyamat – Azure üzembe helyezése
description: Állítsa be, és a egy példa adatok folyamat, amely új adatok által aktivált és tömör eredményt ad.
services: hdinsight
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/11/2018
ms.openlocfilehash: 93c2808dc244a86f7a58aa65d649e9c3e8c17f7c
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53251708"
---
# <a name="operationalize-a-data-analytics-pipeline"></a>Adatelemzési folyamat üzembe helyezése

*Adatfolyamatok* underly számos data analytics megoldás. Javasol a neve, ahogy egy adatfolyamat fogadja a nyers adatokat, törli a reshapes, igény szerint, és általában végez számítást vagy összesítéseket a feldolgozott adatok tárolása előtt. A feldolgozott adatokat az ügyfelek, jelentések és API-kat használja fel. Egy adatfolyamat ismételhető eredményekkel kell megadnia, ütemezés szerint, vagy aktivált új adatokkal.

Ez a cikk ismerteti az adatfolyamatok az ismételhetőség használata a HDInsight Hadoop-fürtökön futó Oozie üzembe helyezése. A példaforgatókönyv végigvezeti előkészít és légitársaság repülési idősorozat-adatokat feldolgozó adatfolyamat.

A következő esetben a bemeneti adatokat tartalmazó repülési adatok kötegelt egy hónapig egybesimított fájl. A flight data közé tartozik a forrás és cél repülőtér, a besorolásával mérföld, az indulási és érkezési idő és így tovább. Az ehhez a folyamathoz célja összefoglalva napi légitársaság teljesítmény, ahol minden egyes légitársaság rendelkezik minden nap, az átlagos indulási és érkezési késések percben és az adott napon repült összes mérföld egy sort.

| YEAR | MONTH | DAY_OF_MONTH | SZOLGÁLTATÓ |AVG_DEP_DELAY | AVG_ARR_DELAY |TOTAL_DISTANCE |
| --- | --- | --- | --- | --- | --- | --- |
| 2017 | 1 | 3 | AA | 10.142229 | 7.862926 | 2644539 |
| 2017 | 1 | 3 | AS | 9.435449 | 5.482143 | 572289 |
| 2017 | 1 | 3 | DL | 6.935409 | -2.1893024 | 1909696 |

A példa folyamat megvárja, amíg a egy új időszakban flight data érkezik, akkor a részletes repülőjáratokkal kapcsolatos információkat tartalmaz a hosszú távú elemzések Hive-data warehouse-bA tárolja. A folyamat is létrehoz egy sokkal kisebb adatkészletet, amely csak a napi flight data foglalja össze. A napi repülési összefoglaló adatokat egy SQL Database-adatbázishoz küldött adja meg a jelentések, például webhelyekhez.

A következő ábra szemlélteti a példa folyamat.

![Repülési adatfolyamat](./media/hdinsight-operationalize-data-pipeline/pipeline-overview.png)

## <a name="oozie-solution-overview"></a>Az Oozie-megoldás áttekintése

Ez a folyamat egy HDInsight Hadoop-fürtön futó Apache Oozie használja.

Az Oozie ismerteti, hogy a folyamatai *műveletek*, *munkafolyamatok*, és *koordinátorok*. Műveletek meghatározása a tényleges munka elvégzéséhez, például a Hive-lekérdezések futtatása. A munkafolyamatok határozzák meg a műveletek sorrendjét. Koordinátorok meghatározásához a ütemezését a munkafolyamat futtatásakor. Koordinátorok is megvárhatja a rendelkezésre álló új adatokat a munkafolyamat-példány indítása előtt.

Az alábbi ábrán látható, a magas szintű tervezési ebben a példában az Oozie folyamat.

![Az Oozie repülési adatfolyamat](./media/hdinsight-operationalize-data-pipeline/pipeline-overview-oozie.png)

### <a name="provision-azure-resources"></a>Az Azure-erőforrások kiépítése

Ez a folyamat az Azure SQL Database és a egy HDInsight Hadoop-fürt ugyanazon a helyen van szükség. Az Azure SQL Database tárolja a folyamat és az Oozie-metaadatok store mindkét az összesítő adatok.

#### <a name="provision-azure-sql-database"></a>Az Azure SQL-adatbázis kiépítése

1. Az Azure portal használatával hozzon létre egy új erőforráscsoportot, nevű `oozie` ebben a példában által használt összes erőforrást tartalmaz.
2. Belül a `oozie` erőforráscsoportot, egy Azure SQL-kiszolgáló üzembe helyezése és adatbázis. Nem kell egy adatbázis nagyobb, mint a S1 Standard tarifacsomagot.
3. Az Azure portal használatával, az újonnan telepített SQL-adatbázis a panelen keresse meg, és válassza ki **eszközök**.

    ![Eszközök gomb](./media/hdinsight-operationalize-data-pipeline/sql-db-tools.png)

4. Válassza ki **Lekérdezésszerkesztő**.

    ![A Lekérdezésszerkesztő gomb](./media/hdinsight-operationalize-data-pipeline/sql-db-query-editor.png)

5. Az a **Lekérdezésszerkesztő** ablaktáblán válassza előbb **bejelentkezési**.

    ![Bejelentkezési gomb](./media/hdinsight-operationalize-data-pipeline/sql-db-login1.png)

6. Adja meg az SQL-adatbázis hitelesítő adatait, és válassza ki **OK**.

   ![Bejelentkezési űrlap](./media/hdinsight-operationalize-data-pipeline/sql-db-login2.png)

7. A Lekérdezésszerkesztő szöveg területen adja meg a következő SQL-utasítások használatával hozzon létre a `dailyflights` tábla, amely a folyamat minden egyes futtatásához összesített adatait fogja tárolni.

    ```
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

8. Válassza ki **futtatása** az SQL-utasítások végrehajtásához.

    ![Futtatás gombra](./media/hdinsight-operationalize-data-pipeline/sql-db-run.png)

Most már készen áll az Azure SQL Database.

#### <a name="provision-an-hdinsight-hadoop-cluster"></a>Egy HDInsight Hadoop-fürt üzembe helyezése

1. Az Azure Portalon válassza ki a **+ új** és keresse meg a HDInsight.
2. Kattintson a **Létrehozás** gombra.
3. Alapvető beállítások panelen adjon meg egy egyedi nevet a fürt számára, és válassza ki az Azure-előfizetését.

    ![HDInsight-fürt neve és az előfizetés](./media/hdinsight-operationalize-data-pipeline/hdi-name-sub.png)

4. Az a **fürt típusa** panelen válassza a **Hadoop** fürt típusa, **Linux** operációs rendszer és a HDInsight-fürt a legújabb verzióra. Hagyja a **fürt szint** , **Standard**.

    ![A HDInsight-fürt típusa](./media/hdinsight-operationalize-data-pipeline/hdi-cluster-type.png)

5. Válasszon **kiválasztása** a alkalmazni a fürt adattípus-választást.
6. Végezze el a **alapjai** ablaktáblán egy bejelentkezési jelszó megadása, és válassza a `oozie` erőforrás csoportot a listából, majd válassza a **tovább**.

    ![HDInsight alapvető beállítások panel](./media/hdinsight-operationalize-data-pipeline/hdi-basics.png)

7. Az a **tárolási** ablaktáblán, az elsődleges tárolási típus van megadva, hagyja **Azure Storage**, jelölje be **új létrehozása**, és adjon meg egy nevet az új fiókhoz.

    ![HDInsight Tárfiók-beállítások](./media/hdinsight-operationalize-data-pipeline/hdi-storage.png)

8. Az a **Metaadattár beállításai**alatt **SQL-adatbázis kiválasztása a Hive**, válassza ki a korábban létrehozott adatbázis.

    ![HDInsight Hive-Metaadattár beállításai](./media/hdinsight-operationalize-data-pipeline/hdi-metastore-hive.png)

9. Válassza ki **SQL Database hitelesítése**.

    ![HDInsight Hive-Metaadattár hitelesítése](./media/hdinsight-operationalize-data-pipeline/hdi-authenticate-sql.png)

10. Adja meg az SQL database-felhasználónevével és jelszavával, és válassza a **kiválasztása**. 

       ![HDInsight Hive-Metaadattár bejelentkezés hitelesítéséhez](./media/hdinsight-operationalize-data-pipeline/hdi-authenticate-sql-login.png)

11. Lépjen vissza a **Metaadattár beállításai** panelen válassza ki az adatbázist az Oozie-metaadatok tárolására, és ahogy korábban hitelesítéséhez. 

       ![HDInsight-Metaadattár beállításai](./media/hdinsight-operationalize-data-pipeline/hdi-metastore-settings.png)

12. Kattintson a **Tovább** gombra.
13. Az a **összefoglalás** ablaktáblán válassza **létrehozás** a fürt üzembe helyezéséhez.

### <a name="verify-ssh-tunneling-setup"></a>SSH-bújtatás a telepítés ellenőrzése

Az Oozie Webkonzol segítségével megtekintheti az állapotát a koordinátor és a munkafolyamat-példányok, állítsa be a HDInsight-fürthöz egy SSH-alagutat. További információkért lásd: [SSH-alagút](hdinsight-linux-ambari-ssh-tunnel.md).

> [!NOTE]
> A Chrome-ban is használhatja a [Foxy Proxy](https://getfoxyproxy.org/) bővítmény keresse meg a fürt webes erőforrások között az SSH-alagút használatával. Konfigurálja a proxykiszolgáló a gazdagépen keresztül az összes kérelem `localhost` 9876 az alagút porton. Ez a megközelítés akkor a Windows alrendszer, Linux, más néven a Bash on Windows 10-kompatibilis.

1. Futtassa a következő parancsot a fürthöz az SSH-alagút megnyitásához:

    ```
    ssh -C2qTnNf -D 9876 sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net
    ```

2. Győződjön meg arról az alagút működési Ambari lépjen a fő csomópont tallózással:

    http://headnodehost:8080

3. Hozzáférés a **Oozie Webkonzol** Ambari belül válassza **Oozie**, **Gyorshivatkozások**, majd válassza ki **Oozie Webkonzol**.

### <a name="configure-hive"></a>Hive konfigurálása

1. Töltse le egy példa egy CSV-fájl, amely egy hónapig repülési adatokat tartalmaz. Töltse le a ZIP-fájl `2017-01-FlightData.zip` származó a [HDInsight GitHub-adattár](https://github.com/hdinsight/hdinsight-dev-guide) és bontsa ki a CSV-fájl `2017-01-FlightData.csv`. 

2. Másolja a CSV-fájl, akár az Azure Storage-fiókot a HDInsight-fürthöz csatlakozik, és elhelyezheti a `/example/data/flights` mappát.

Másolhatja az az SCP-fájlt a `bash` rendszerhéj-munkamenet.

1. Szolgáltatáskapcsolódási pont használatával másolja a fájlokat a helyi gépen a HDInsight-fürt fő csomópontjának helyi tárolására.

    ```bash
    scp ./2017-01-FlightData.csv sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:2017-01-FlightData.csv
    ```

2. A HDFS parancs használatával másolja a fájlt egy fő csomópontja helyszíni tárolóból az Azure Storage.

    ```bash
    hdfs dfs -put ./2017-01-FlightData.csv /example/data/flights/2017-01-FlightData.csv
    ```

A mintaadatok már elérhető. Azonban a folyamat csak feldolgozásra, egyet a bejövő adatok két Hive-táblák (`rawFlights`) és a egy, az összesített adatok (`flights`). Ezek a táblák az Ambari a következőképpen hozhat létre.

1. Jelentkezzen be az Ambari az [ http://headnodehost:8080 ](http://headnodehost:8080).
2. A szolgáltatások listájából válassza **Hive**.

    ![Az Ambari Hive kiválasztása](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive.png)

3. Válassza ki **nyissa meg a nézet** mellett a Hive-nézet 2.0 címkét.

    ![Az Ambari Hive nézete kiválasztása](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-summary.png)

4. A lekérdezés szöveges területen illessze be az alábbi utasításokat, hozzon létre a `rawFlights` tábla. A `rawFlights` táblázat tartalmazza a séma olvasási belül a CSV-fájlokat a `/example/data/flights` az Azure Storage-mappát. 

    ```
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

5. Válassza ki **Execute** a tábla létrehozásához.

    ![Az Ambari Hive-lekérdezés](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-query.png)

6. Hozhat létre a `flights` táblában, cserélje le a szöveget a lekérdezés szöveges területen az alábbi utasításokat. A `flights` táblában egy Hive-felügyelt táblát, amely particionálja az adatokat, betölti év, hónap és hónap szerint. Ez a táblázat minden korábbi flight olyan adat, soronként flight egy adatforrás adatai tartalmazzák a legalacsonyabb granularitással fogja tartalmazni.

    ```
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

7. Válassza ki **Execute** a tábla létrehozásához.

### <a name="create-the-oozie-workflow"></a>Az Oozie-munkafolyamatokkal létrehozása

A folyamatok általában fel, és kötegekben adatokat egy adott időtartam alatt. Ebben az esetben a folyamat dolgozza fel a flight data naponta. Ez a megközelítés lehetővé teszi, hogy a bemeneti CSV-fájlokhoz naponta, hetente, havonta vagy évente kimarad.

A munkafolyamat-minta dolgozza fel a flight data--naponta, a három fő lépést:

1. Adatokat szeretne kinyerni az adott napon dátumtartományban a CSV-fájlból forrás jelölt Hive-lekérdezés futtatása a `rawFlights` táblát, és helyezze be az adatokat a `flights` tábla.
2. A dinamikusan hozhat létre egy előkészítési táblába a Hive, a nap és a szolgáltató által összesített repülési adatokat tartalmazó napi egy Hive-lekérdezések futtatásához.
3. Az Apache Sqoop használatával összes adatot másol a napi előkészítési táblába, a Hive a cél a `dailyflights` az Azure SQL Database tábla. Sqoop a Forrássorok beolvassa az adatokat a Hive-táblában, az Azure Storage szolgáltatásban tárolt mögött, és betölti őket az SQL Database JDBC-kapcsolattal.

Az Oozie-munkafolyamatokkal kezel fenti három lépést. 

1. Hozzon létre egy lekérdezést a fájl `hive-load-flights-partition.hql`.

    ```
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

    Az Oozie változók szintaxissal `${variableName}`. Ezek a változók vannak beállítva, a `job.properties` fájlt egy későbbi lépésben leírtak szerint. Az Oozie helyettesíti a tényleges értékek futásidőben.

2. Hozzon létre egy lekérdezést a fájl `hive-create-daily-summary-table.hql`.

    ```
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

    Ez a lekérdezés hoz létre egy előkészítési táblába egy nap csak összesített adatok tárolására, jegyezze fel a SELECT utasítástípusnál, amely kiszámítja az átlagos késések és a szolgáltató által naponta repült távolság összesen lesz. Ez egy ismert (az elérési utat a hiveDataFolder változó által megadott) helyen tárolt táblába beszúrt adatokat, hogy a használat forrásaként a sqoop használatával a következő lépésben.

3. Futtassa a következő Sqoop parancsot.

    ```
    sqoop export --connect ${sqlDatabaseConnectionString} --table ${sqlDatabaseTableName} --export-dir ${hiveDataFolder} -m 1 --input-fields-terminated-by "\t"
    ```

Három külön műveletként használható a következő Oozie munkafolyamat fájlban nevű ki, a fenti három lépést `workflow.xml`.

```
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
            <archive>sqljdbc41.jar</archive>
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

A két Hive-lekérdezések érhetők el az elérési út alapján az Azure Storage-ban, és a fennmaradó változó értéke a következő által biztosított `job.properties` fájlt. Ez a fájl úgy konfigurálja a munkafolyamatot, hogy a dátum 2017. január 3. futtassa.

```
nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
jobTracker=hn0-[CLUSTERNAME].[UNIQUESTRING].dx.internal.cloudapp.net:8050
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

Az alábbi táblázat foglalja össze az egyes tulajdonságok, és azt jelzi, hogy hol találhatók az értékeket a saját környezetének.

| Tulajdonság | Érték forrása |
| --- | --- |
| nameNode | A HDInsight-fürt csatolva az Azure Storage-tároló teljes elérési útja. |
| jobTracker | A belső állomásnév való a active fürt YARN átjárócsomópont csomópont. Az Ambari kezdőlapján válassza ki a YARN-szolgáltatások a listából, majd válassza a aktív Resource Manager. Az állomásnév URI, amely a lap tetején jelenik meg. A port 8050 hozzáfűzése. |
| queueName | A YARN-várólista használatos, ha a Hive-műveletek neve. Hagyja az alapértelmezett. |
| oozie.use.system.libpath | Hagyja üresen, igaz értékként. |
| Základu | Az elérési útja, amelyen központi telepítését az Oozie-munkafolyamatokkal, és a támogatási fájlokat az Azure Storage-ban a kívánt almappába. |
| oozie.wf.application.path | Az Oozie-munkafolyamatokkal helyét `workflow.xml` futtatásához. |
| hiveScriptLoadPartition | Az Azure Storage-fájl elérési útját a Hive query `hive-load-flights-partition.hql`. |
| hiveScriptCreateDailyTable | Az Azure Storage-fájl elérési útját a Hive query `hive-create-daily-summary-table.hql`. |
| hiveDailyTableName | A dinamikusan létrehozott nevét, az átmeneti tárolási tábla. |
| hiveDataFolder | Az elérési útja az Azure Storage-ban az átmeneti tárolási tábla által tárolt adatok. |
| sqlDatabaseConnectionString | A JDBC szintaxis kapcsolati karakterláncot az Azure SQL Database-adatbázishoz. |
| sqlDatabaseTableName | Az Azure SQL Database, amelybe egészül ki összefoglaló sorokat a tábla neve. Hagyja meg `dailyflights`. |
| év | Az év összetevőt, a nap melyik repülési összegzéseket számítja ki. Hagyja üresen, mivel. |
| hónap | A hónap összetevőt, a nap melyik repülési összegzéseket számítja ki. Hagyja üresen, mivel. |
| nap | Azon napja, a nap melyik repülési összegzéseket számítja ki a hónap összetevőt. Hagyja üresen, mivel. |

> [!NOTE]
> Ne felejtse el frissíteni a példány a `job.properties` az adott környezetre jellemző, mielőtt üzembe helyezése és az Oozie-munkafolyamatokkal értékeket tartalmazó fájl.

### <a name="deploy-and-run-the-oozie-workflow"></a>Üzembe helyezése és az Oozie-munkafolyamatokkal

Használja a szolgáltatáskapcsolódási pont a bash-munkamenetből üzembe helyezéséhez az Oozie-munkafolyamatokkal (`workflow.xml`), a Hive-lekérdezések (`hive-load-flights-partition.hql` és `hive-create-daily-summary-table.hql`) és a feladat konfigurálása (`job.properties`).  Az Oozie, csak a `job.properties` fájl is létezik, az átjárócsomópont helyi tárolására. Minden más fájlnál hdfs, ez megkülönbözteti a kis Azure Storage-ban kell tárolni. A munkafolyamat által használt Sqoop művelet függ a JDBC-illesztőprogram az SQL-adatbázis, amely kell átmásolni a fő csomópontot HDFS való kommunikációhoz.

1. Hozzon létre a `load_flights_by_day` almappát a felhasználó elérési útját a helyi tárolóban a fő csomópont alá.

        ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net 'mkdir load_flights_by_day'

2. Az aktuális könyvtárban található összes fájl másolása (a `workflow.xml` és `job.properties` fájlok) akár a `load_flights_by_day` almappába.

        scp ./* sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:load_flights_by_day

3. SSH-t a fő csomópontot, és keresse meg a `load_flights_by_day` mappát.

        ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net
        cd load_flights_by_day

4. A munkafolyamat-fájlok másolása a HDFS-be.

        hdfs dfs -put ./* /oozie/load_flights_by_day

5. Másolás `sqljdbc41.jar` a HDFS-ben a munkafolyamat mappába a helyi átjárócsomóponthoz:

        hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /oozie/load_flights_by_day

6. A munkafolyamat futtatása.

        oozie job -config job.properties -run

7. Látni fogja, az Oozie webkonzollal állapota. Az Ambari, válassza **Oozie**, **Gyorshivatkozások**, majd **Oozie Webkonzol**. Alatt a **munkafolyamat-feladatokat** lapon jelölje be **az összes feladat**.

    ![Az Oozie Web Console munkafolyamatok](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-workflows.png)

8. A sikeres állapotát, a lekérdezés az SQL database tábla a beillesztett sorok megtekintéséhez. Az SQL-adatbázishoz, válassza ki a panelen lépjen az Azure portal használatával **eszközök**, és nyissa meg a **Lekérdezésszerkesztő**.

        SELECT * FROM dailyflights

Most, hogy a munkafolyamat fut, az egyetlen gazdagépes nap, akkor futtathatja ezt a munkafolyamatot, amely a munkafolyamat ütemezését, így naponta fut egy koordinátorral.

### <a name="run-the-workflow-with-a-coordinator"></a>A munkafolyamat futtatása a koordinátor

Ez a munkafolyamat ütemezése, futtatása napi (vagy egy adott időtartományban minden nap), használhatja a koordinátor. Koordinátor határozzák meg egy XML-fájlt, például `coordinator.xml`:

```
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

Ahogy látható, a koordinátor többsége, csupán továbbítja konfigurációs adatait a munkafolyamat-példányhoz. Vannak azonban néhány fontos elemek hívásához.

* 1. pont: A `start` és `end` az attribútumok a `coordinator-app` maga az elem szabályozhatja az időintervallum, amelyen keresztül a koordinátor futtatja.

    ```
    <coordinator-app ... start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" ...>
    ```

    Koordinátor felelős műveletek belül a `start` és `end` dátumtartomány szerint a megadott időköz az `frequency` attribútum. Minden ütemezett művelethez ezután futtatja a munkafolyamatot a konfigurált. A fenti koordinátor definíciójában a koordinátor van konfigurálva műveleteket hajtson végre 2017. január 1-től 2017. január 5-én. A gyakoriság 1 nap értékre van állítva a [Oozie kifejezés nyelve](http://oozie.apache.org/docs/4.2.0/CoordinatorFunctionalSpec.html#a4.4._Frequency_and_Time-Period_Representation) gyakorisága kifejezés `${coord:days(1)}`. Ez a művelet ütemezés koordinátor eredményez (és így a munkafolyamat) naponta egyszer. Dátumtartományokat, amelyek korábban, mint ebben a példában a művelet lesz ütemezve késedelem nélkül fusson. A dátum, amelyen művelet való futásra van ütemezve kezdetét nevezzük a *névleges idő*. Ha például az adatok feldolgozása a 2017. január 1-től a koordinátor ütemeznek művelet után névleges költséget számítunk időpont 2017-01-01T00:00:00 GMT.

* 2. pont: A munkafolyamat dátum keretein belül a `dataset` elem megadja, hogy legyen egy adott dátumtartományra vonatkozóan az adatokat HDFS-ben keresse meg, és konfigurálja, hogyan Oozie határozza meg, hogy az adatok rendelkezésre áll-e még feldolgozásra.

    ```
    <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
        <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
        <done-flag></done-flag>
    </dataset>
    ```

    Az adatokat hdfs elérési útja a megadott kifejezésnek megfelelően dinamikusan épül a `uri-template` elemet. A koordinátor a gyakoriságot, egy nap is szolgál az adatkészletben. A kezdő és záró dátuma, a koordinátor elem a vezérlő során, ha a műveletek ütemezett (és meghatározása után névleges költséget számítunk időponthoz képest), a `initial-instance` és `frequency` adatkészletén szabályozhatja a számítás, a dátum a felépítéséhezhasznált`uri-template`. Ebben az esetben állítsa be a kezdeti példány egy nappal a koordinátor annak biztosítása érdekében a megkezdése előtt, hogy buildkonfigurációtól első napja a (1/1/2017) adat tekinthető meg. Az adatkészlet számítás előregörgeti a értékét `initial-instance` a koordinátor (12/31/2016) speciális lépésekben az adatkészlet-gyakoriság (1 napon belül) úgy találja, a legutóbbi dátum, amely nem felel meg a névleges idő beállítása (2017-01-01T00:00:00 GMT az első művelet).

    Az üres `done-flag` elem azt jelzi, hogy Oozie keres az bemeneti adatok a kijelölt időpontban, amikor az Oozie meghatározza adatok elérhető-e fájlok vagy könyvtárak jelenlétét. Ebben az esetben egy csv-fájl jelenléte. Ha egy csv-fájl jelen, Oozie azt feltételezi, készen áll az adatok, és elindítja egy munkafolyamat-példány feldolgozni a fájlt. Ha nem áll csv fájl található, az Oozie feltételezi, hogy az adatok még nem kész, és a munkafolyamat futtatását várakozó állapotba kerül.

* 3. pontok: A `data-in` elem azt határozza meg az adott időbélyeg névleges adatokként meg, amikor az az értékeket cserélje le `uri-template` kapcsolódó adatkészlet.

    ```
    <data-in name="event_input1" dataset="ds_input1">
        <instance>${coord:current(0)}</instance>
    </data-in>
    ```

    Ebben az esetben állítson be egy példányt a kifejezést `${coord:current(0)}`, amelyet a rendszer lefordítja arra az eredetileg a koordinátor ütemezett művelet után névleges költséget számítunk ideje. Más szóval ha a koordinátor névleges időpont 2017-01-01-futtatását ütemezi, majd 2017-01-01 a mi használt cserélje le a ÉVBEN (2017) és a hónap (01) változót az URI-sablonban. Az URI-sablon ezen a példányon jön létre, ha a Oozie ellenőrzi, hogy a várt könyvtárat vagy fájlt érhető el, és ennek megfelelően ütemezi a következő futtatáskor a munkafolyamat.

Az előző három pont eddig is számtalan előnyét egy olyan helyzetet, ahol a koordinátor ütemezi a feldolgozás a forrásadatok a napi – naponta módon össze. 

* 1. pont: A koordinátor kezdődik, a 2017-01-01 névleges dátuma.

* 2. pont: Az Oozie megkeresi az elérhető adat `sourceDataFolder/2017-01-FlightData.csv`.

* 3. pontok: Az Oozie ezt a fájlt talál, ütemezi a fel az adatokat a 2017-01-01-munkafolyamat egy példányát. Az Oozie majd 2017-01-02 feldolgozása továbbra is. Ezt a próbaidőszakot akár, de nem tartalmazza a 2017-01-05 ismétlődik.

A munkafolyamatok, a konfiguráció a koordinátor meghatározottak szerint egy `job.properties` fájlt, amely rendelkezik felülbírálja a munkafolyamat által használt beállításokat.

```
nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
jobTracker=hn0-[CLUSTERNAME].[UNIQUESTRING].dx.internal.cloudapp.net:8050
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

A csak az új tulajdonságok a-ben bevezetett `job.properties` fájlban található:

| Tulajdonság | Érték forrása |
| --- | --- |
| oozie.coord.application.path | Azt jelzi, hogy a helyét a `coordinator.xml` fájlt, amely tartalmazza az Oozie-koordinátor futtatásához. |
| hiveDailyTableNamePrefix | Az előtag, az átmeneti tárolási tábla a tábla neve dinamikusan létrehozásakor használt. |
| hiveDataFolderPrefix | Az összes előkészítési tábla tárolására szolgáló elérési út előtagja. |

### <a name="deploy-and-run-the-oozie-coordinator"></a>Üzembe helyezése és futtatása az Oozie-koordinátor

Futtathatja a folyamatot a koordinátor, folytatható hasonlóan végezhető, mint a munkafolyamat, azzal a különbséggel dolgozik egy mappából a mappába, amelyben a munkafolyamat fent egy szinttel. A mappa egyezmény így egyetlen koordinátor társíthat másik gyermek munkafolyamat lemezen, a munkafolyamatok elválasztja az összekötőket.

1. A helyi gépen SCP-je használatával másolja a koordinátor-fájlokat a helyi tárterület, a fürt fő csomópontjának.

    ```bash
    scp ./* sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:~
    ```

2. SSH-t a fő csomópontot.

    ```bash
    ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net 
    ```

3. Másolja a koordinátor fájlokat HDFS.

    ```bash
    hdfs dfs -put ./* /oozie/
    ```

4. Futtassa a koordinátor.

    ```bash
    oozie job -config job.properties -run
    ```

5. Ellenőrizze az állapotát a webkonzollal Oozie, ez alkalommal válassza a **koordinátor feladatok** lapon, majd **az összes feladat**.

    ![Az Oozie webes konzol koordinátor feladatok](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-jobs.png)

6. Válassza ki a koordinátor-példány az ütemezett műveletek listájának megjelenítéséhez. Ebben az esetben kell megjelennie a 1/1/2017 és 1/4/2017 eső névleges rövidebb négy művelet.

    ![Az Oozie Web Console koordinátor feladat](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-instance.png)

    Ez a lista minden egyes művelethez egy példányt a munkafolyamat által feldolgozott egy nap alatt az adatokat, ahol jelzi, hogy a nap kezdete után névleges költséget számítunk idő felel meg.

## <a name="next-steps"></a>További lépések

* [Apache Oozie-dokumentáció](http://oozie.apache.org/docs/4.2.0/index.html)

<!-- * Build the same pipeline [using Azure Data Factory](tbd.md).  -->
