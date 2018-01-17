---
title: "Azok az adatok analytics csővezeték - Azure |} Microsoft Docs"
description: "Telepítsen és futtasson egy példa adatok folyamatot, amely új adatok váltja ki, és tömör eredményt ad."
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/11/2018
ms.author: ashishth
ms.openlocfilehash: 7a439c9d25a470a2474b427f6b20addb6ff3b0c7
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2018
---
# <a name="operationalize-a-data-analytics-pipeline"></a>Azok az adatok analytics folyamat

*Adatok folyamatok* underly sok adatelemző megoldásokat. A nevet javasol, adatok folyamat a nyers adatokat fogad, megtisztítja reshapes, igény szerint és általában hajtja végre számítások vagy összesítés előtt, hogy a feldolgozott adatok tárolására. A feldolgozott adatok ügyfeleknek, jelentések és API-k által felhasznált. Adatok adatcsatorna ismételhető eredményeket, biztosítania kell az ütemezés szerint vagy -változáskor induló új adatokat.

Ez a cikk ismerteti az adatok folyamatok ismételhetőség használata a HDInsight Hadoop-fürtök futó Oozie azok. A példaforgatókönyv végigvezeti egy adatok folyamatot, amely előkészíti és feldolgozza a légitársaság repülési idősorozat adatokat.

Abban az esetben a bemeneti adatokat tartalmazó repülési adatköteget hónapig egybesimított fájl. Ez felé továbbított adatok közé tartozik a forrás és cél repülőtéren, a besorolásával miles, az indító és érkezési idő, és így tovább. Ez az adatcsatorna a célja, hogy összesítse a napi légitársaság teljesítmény, ahol légitársaság egy sorból áll minden nap a átlagos indulási és érkezési késlelteti a perc és a teljes miles adott napon besorolásával.

| ÉV | HÓNAP | DAY_OF_MONTH | SZOLGÁLTATÓJA |AVG_DEP_DELAY | AVG_ARR_DELAY |TOTAL_DISTANCE |
| --- | --- | --- | --- | --- | --- | --- |
| 2017 | 1 | 3 | AA | 10.142229 | 7.862926 | 2644539 |
| 2017 | 1 | 3 | MIVEL | 9.435449 | 5.482143 | 572289 |
| 2017 | 1 | 3 | DL | 6.935409 | -2.1893024 | 1909696 |

A példa-feldolgozási folyamat megvárja, amíg egy új időszak felé továbbított adatok érkezik, akkor az a Hive data warehouse-hosszú távú elemzések részletes felé továbbított adatokat tárolja. A folyamat is létrehoz sokkal kisebb adatkészletre mutató csak napi felé továbbított adatok összegzése látható. A napi repülési összefoglaló adatküldést SQL adatbázishoz kínálnak jelentéseket, többek között a webhelyhez.

A következő ábra szemlélteti a példa-feldolgozási folyamat.

![Felé továbbított adatok feldolgozási folyamat](./media/hdinsight-operationalize-data-pipeline/pipeline-overview.png)

## <a name="oozie-solution-overview"></a>Oozie megoldási áttekintés

Ez az adatcsatorna egy HDInsight Hadoop-fürt futó Apache Oozie használja.

Oozie ismerteti a folyamatok a *műveletek*, *munkafolyamatok*, és *koordinátor*. Műveletek végrehajtásához, például a Hive-lekérdezések futtatása a tényleges munkát határozza meg. A munkafolyamatok határozzák meg, hogy a műveletek sorrendjét. Koordinátor határozza meg azt a munkafolyamat futtatásakor ütemezését. Koordinátor is, amíg az új adatok rendelkezésre állását a munkafolyamat-példány megnyitása előtt.

Az alábbi ábrán látható, ez például Oozie-feldolgozási folyamat magas szintű tervezését.

![Oozie felé továbbított adatok feldolgozási folyamat](./media/hdinsight-operationalize-data-pipeline/pipeline-overview-oozie.png)

### <a name="provision-azure-resources"></a>Azure-erőforrások kiépítése

Ez az adatcsatorna egy Azure SQL Database és egy HDInsight Hadoop-fürt ugyanott van szükség. Az Azure SQL Database az adatcsatorna és az Oozie-metaadatok tároló által létrehozott mindkét az összefoglaló adatokat tárolja.

#### <a name="provision-azure-sql-database"></a>Kiépítés Azure SQL adatbázis

1. Az Azure-portált használja, hozzon létre egy új erőforráscsoportot `oozie` ebben a példában által használt összes erőforrást tartalmaz.
2. Belül a `oozie` erőforráscsoportot, egy Azure SQL-kiszolgáló üzembe helyezése és az adatbázis. Nem kell egy adatbázis nagyobb, mint a S1 Standard tarifacsomagot.
3. Az Azure portál használatával, az újonnan telepített SQL-adatbázis a panelen keresse meg, és válassza ki **eszközök**.

    ![Eszközök gomb](./media/hdinsight-operationalize-data-pipeline/sql-db-tools.png)

4. Válassza ki **Lekérdezésszerkesztő**.

    ![Lekérdezés-szerkesztő gomb](./media/hdinsight-operationalize-data-pipeline/sql-db-query-editor.png)

5. Az a **Lekérdezésszerkesztő** ablaktáblán válassza előbb **bejelentkezési**.

    ![Bejelentkezési gomb](./media/hdinsight-operationalize-data-pipeline/sql-db-login1.png)

6. SQL-adatbázis hitelesítő adatait, és válassza ki **OK**.

   ![Bejelentkezési űrlap](./media/hdinsight-operationalize-data-pipeline/sql-db-login2.png)

7. A Lekérdezésszerkesztő szöveg területen adja meg a következő SQL-utasítások létrehozásához a `dailyflights` tábla minden egyes futni, hanem a folyamat az összesített adatok tárolásához.

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

    ![Futtatás gomb](./media/hdinsight-operationalize-data-pipeline/sql-db-run.png)

Az Azure SQL-adatbázis most már készen áll.

#### <a name="provision-an-hdinsight-hadoop-cluster"></a>HDInsight Hadoop-fürtök kiépítése

1. Válassza ki az Azure-portálon **+ új** , és keressen a HDInsight.
2. Kattintson a **Létrehozás** gombra.
3. A alapvető beállítások panelen adjon meg egyedi nevet a fürtnek, és válassza ki az Azure-előfizetéséhez.

    ![A HDInsight-fürt neve és az előfizetés](./media/hdinsight-operationalize-data-pipeline/hdi-name-sub.png)

4. Az a **típusú fürt** ablaktáblán válassza előbb a **Hadoop** típusa, a fürt **Linux** operációs rendszer és a legújabb verzióját a HDInsight-fürthöz. Hagyja a **fürt réteg** : **szabványos**.

    ![A HDInsight-fürt típusa](./media/hdinsight-operationalize-data-pipeline/hdi-cluster-type.png)

5. Válasszon **válasszon** alkalmazni a fürt választást.
6. Fejezze be a **alapjai** ablaktábla bejelentkezési jelszó, majd válassza a `oozie` erőforrás csoportot a listából, majd válasszon **következő**.

    ![A HDInsight alapvető beállítások panelen](./media/hdinsight-operationalize-data-pipeline/hdi-basics.png)

7. Az a **tárolási** ablaktáblán, az elsődleges tárolási típusa hagyja **Azure Storage**, jelölje be **hozzon létre új**, és adja meg az új fiók nevét.

    ![HDInsight tároló Fiókbeállítások](./media/hdinsight-operationalize-data-pipeline/hdi-storage.png)

8. Az a **Metaadattárhoz beállítások**a **jelöljön ki egy SQL-adatbázist a Hive**, válassza ki a korábban létrehozott adatbázist.

    ![HDInsight Hive Metaadattárhoz beállítások](./media/hdinsight-operationalize-data-pipeline/hdi-metastore-hive.png)

9. Válassza ki **SQL-adatbázis hitelesítéséhez**.

    ![HDInsight Hive Metaadattárhoz hitelesítéséhez](./media/hdinsight-operationalize-data-pipeline/hdi-authenticate-sql.png)

10. Adja meg az SQL-adatbázis felhasználónevét és jelszavát, és válassza a **válasszon**. 

       ![HDInsight Hive Metaadattárhoz bejelentkezési hitelesítés](./media/hdinsight-operationalize-data-pipeline/hdi-authenticate-sql-login.png)

11. Vissza a **Metaadattárhoz beállítások** panelen válassza ki az adatbázist az Oozie-metaadatok tárolja, és úgy, ahogy korábban hitelesítéséhez. 

       ![HDInsight Metaadattárhoz beállítások](./media/hdinsight-operationalize-data-pipeline/hdi-metastore-settings.png)

12. Válassza ki **következő**.
13. A a **összegzés** ablaktáblán válassza előbb **létrehozása** a fürt telepítéséhez.

### <a name="verify-ssh-tunneling-setup"></a>SSH-alagútkezelési a telepítés ellenőrzése

A koordinátor és a munkafolyamat-példányok állapotának megtekintése az Oozie Webkonzol használatához állítsa be a HDInsight-fürthöz az SSH-alagút. További információkért lásd: [SSH-alagút](hdinsight-linux-ambari-ssh-tunnel.md).

> [!NOTE]
> A Chrome is használhatja a [Foxy Proxy](https://getfoxyproxy.org/) bővítmény keresse meg a fürt webes erőforrásokhoz az SSH-alagúton keresztül. Konfigurálja úgy a proxy a gazdagépen keresztül az összes kérelem `localhost` 9876 az alagutat porton. Ez a megközelítés a Linux Bash Windows 10 más néven Windows alrendszere esetén.

1. A következő parancsot a fürthöz az SSH-alagút megnyitásához:

    ```
    ssh -C2qTnNf -D 9876 sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net
    ```

2. Győződjön meg arról az alagutat működik a központi csomóponton tallózással nyissa meg a Ambari:

    http://headnodehost:8080

3. Hozzáférés a **Oozie Webkonzol** belül Ambari, válassza ki **Oozie**, **Gyorshivatkozások**, majd válassza ki **Oozie Webkonzol**.

### <a name="configure-hive"></a>Hive konfigurálása

1. Töltse le egy példa egy CSV-fájl, amely egy hónapig felé továbbított adatokat tartalmaz. A ZIP-fájl letöltésére `2017-01-FlightData.zip` a a [HDInsight Github-tárházban](https://github.com/hdinsight/hdinsight-dev-guide) és csomagolja ki azt a CSV-fájl `2017-01-FlightData.csv`. 

2. Akár az Azure Storage-fiók a HDInsight-fürthöz csatlakozik a CSV-fájl másolása és elhelyezheti a `/example/data/flights` mappát.

Másolhatja a fájlt a szolgáltatáskapcsolódási pont a `bash` rendszerhéj-munkamenet.

1. Szolgáltatáskapcsolódási pont használatával a fájlok másolását a helyi számítógép a helyi tárolása a HDInsight fürt átjárócsomópontjából.

    ```bash
    scp ./2017-01-FlightData.csv sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:2017-01-FlightData.csv
    ```

2. A HDFS parancs használata a fájl átmásolása a átjárócsomópont helyi tároló Azure Storage.

    ```bash
    hdfs dfs -put ./2017-01-FlightData.csv /example/data/flights/2017-01-FlightData.csv
    ```

A mintaadatok már elérhető. Azonban a feldolgozási sor igényel a feldolgozásához, a bejövő adatok egy két Hive táblák (`rawFlights`), a másik az összegzett adatokat (`flights`). Ezek a táblázatok az alábbiak szerint Ambari létrehozása.

1. Navigáljon a Ambari bejelentkezni [http://headnodehost:8080](http://headnodehost:8080).
2. Válassza ki a listáról a szolgáltatások **Hive**.

    ![Az Ambari struktúra kiválasztása](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive.png)

3. Válassza ki **nyissa meg a nézet** a Hive View 2.0 felirat mellett.

    ![Az Ambari Hive nézete kiválasztása](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-summary.png)

4. A lekérdezés terület, illessze be a következő utasítás létrehozásához a `rawFlights` tábla. A `rawFlights` táblázat egy séma-a-olvasási a CSV-fájl belüli a `/example/data/flights` mappa az Azure Storage. 

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

    ![Az Ambari Hive-lekérdezések](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-query.png)

6. Létrehozásához a `flights` table, cserélje le a szöveget a lekérdezés szöveges területen az alábbi utasításokat. A `flights` tábla, amely betölti év, hónap és a hónap napja adatok particionálja felügyelt Hive táblákat. Ez a táblázat összes korábbi felé továbbított adatok, szerepel a forrásadatok soronként egy felhőszolgáltató közötti átviteléhez a legalacsonyabb lépésköz fogja tartalmazni.

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

### <a name="create-the-oozie-workflow"></a>Az Oozie-munkafolyamat létrehozása

Folyamatok általában feldolgozni az adatokat kötegekben által egy adott időintervallumban. Ebben az esetben a feldolgozási sor dolgozza fel a felé továbbított adatok naponta. Ez a megközelítés lehetővé teszi, hogy a bemeneti CSV-fájlok naponta, hetente, havonta vagy évente érkezésére.

A minta-munkafolyamat dolgozza fel a felé továbbított adatok--naponta, a három fő lépést:

1. A Ha adatokat szeretne kinyerni nap dátumtartományon belül a forrás CSV-fájl által képviselt Hive-lekérdezések futtatása a `rawFlights` tábla, majd szúrja be az adatokat a `flights` tábla.
2. Futtathat Hive-lekérdezéseket dinamikusan átmeneti tárolási tábla létrehozása a Hive a napon, amely a nap, illetve szolgáltatónként összesített felé továbbított adatok másolatát tartalmazza.
3. Apache Sqoop követve másolja át az adatokat a Hive napi átmeneti tárolási tábla a cél `dailyflights` Azure SQL adatbázis táblájában. Sqoop a Forrássorok beolvassa az adatokat a Hive táblában szereplő Azure Storage mögött, és betölti őket az SQL-adatbázisba JDBC-kapcsolaton keresztül.

Három lépések koordinálja az Oozie-munkafolyamat. 

1. Hozzon létre egy lekérdezést a fájlban `hive-load-flights-partition.hql`.

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

    Oozie változók szintaxissal `${variableName}`. Ezek a változók vannak beállítva a `job.properties` fájlt egy későbbi lépésben leírtak szerint. Oozie helyettesíti a tényleges értékek futásidőben.

2. Hozzon létre egy lekérdezést a fájlban `hive-create-daily-summary-table.hql`.

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

    Ez a lekérdezés fog csak a összegzett adatokat tároló egy napon, jegyezze fel a SELECT utasítástípusnál, amely kiszámítja az átlagos késleltetése és napi szállító besorolásával távolság összesen átmeneti tárolási tábla hoz létre. Az adatok tárolása ismert helyén (az elérési utat a hiveDataFolder változó által megadott) a táblába úgy, hogy a használat forrásként a Sqoop a következő lépésben.

3. A következő Sqoop parancsot.

    ```
    sqoop export --connect ${sqlDatabaseConnectionString} --table ${sqlDatabaseTableName} --export-dir ${hiveDataFolder} -m 1 --input-fields-terminated-by "\t"
    ```

Három lépések szerint van megadva a három külön műveletként használható a következő Oozie munkafolyamat fájlban nevű `workflow.xml`.

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

A két Hive-lekérdezések érik el az Azure Storage elérési úttal, és a fennmaradó változó értéke a következő által biztosított `job.properties` fájlt. Ez a fájl a munkafolyamat futtatását dátumra 2017. január 3. konfigurálja.

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

Az alábbi táblázat foglalja össze az egyes tulajdonságok és azt jelzi, hogy hol találhatók az értékeket a saját környezetéhez.

| Tulajdonság | Érték forrása |
| --- | --- |
| NameNode | A teljes elérési útját az Azure-tárolót a HDInsight-fürthöz csatlakozik. |
| jobTracker | A belső állomásnév való a active fürt YARN head csomópont. Az Ambari kezdőlapján válassza ki a YARN szolgáltatások közül, majd kattintson a aktív erőforrás-kezelő. Az állomásnév URI a lap tetején jelenik meg. A port 8050 hozzáfűzése. |
| queueName | A YARN várólista, a Hive műveletek ütemezésekor neve. Hagyja meg az alapértelmezett. |
| oozie.use.system.libpath | Hagyja igaz. |
| alkalmazás gyökérkönyvtárán | Az Azure Storage, ahol telepíteni, az Oozie munkafolyamat kiegészítő fájlokat almappa elérési útja. |
| oozie.wf.application.path | A hely az Oozie-munkafolyamat `workflow.xml` futtatásához. |
| hiveScriptLoadPartition | Az Azure Storage fájl elérési útját a Hive lekérdezés `hive-load-flights-partition.hql`. |
| hiveScriptCreateDailyTable | Az Azure Storage fájl elérési útját a Hive lekérdezés `hive-create-daily-summary-table.hql`. |
| hiveDailyTableName | Az átmeneti tárolási tábla használandó dinamikusan generált név. |
| hiveDataFolder | Az átmeneti tárolási tábla által tárolt adatok az Azure Storage elérési útja |
| sqlDatabaseConnectionString | A JDBC szintaxis kapcsolati karakterláncot az Azure SQL Database adatbázishoz. |
| sqlDatabaseTableName | Az Azure SQL Database, amelybe összesítő sorok szúrja be a tábla neve. Hagyja meg `dailyflights`. |
| év | A nap melyik repülési összesítések arra az esetre vonatkoznak év összetevőjét. Hagyja, mert a rendszer. |
| hónap | A hónap összetevőjét a nap melyik repülési összesítések arra az esetre vonatkoznak. Hagyja, mert a rendszer. |
| nap | A nap hónap összetevőjének a nap melyik repülési összesítések arra az esetre vonatkoznak. Hagyja, mert a rendszer. |

> [!NOTE]
> Ne felejtse el frissíteni a `job.properties` fájl az adott környezetre, telepítése és az Oozie munkafolyamat futtatása előtt.

### <a name="deploy-and-run-the-oozie-workflow"></a>Regisztrálhat és futtathat a Oozie munkafolyamat

Szolgáltatáskapcsolódási pont használata a bash munkamenetből az Oozie-munkafolyamat telepítése (`workflow.xml`), a Hive-lekérdezések (`hive-load-flights-partition.hql` és `hive-create-daily-summary-table.hql`) és a feladat konfigurációját (`job.properties`).  Az Oozie, csak a `job.properties` fájlt a helyi tárolása a headnode lehetnek. Minden más fájlnál HDFS, ez esetben Azure Storage kell tárolni. A munkafolyamat által használt Sqoop művelet attól függ, hogy egy JDBC-illesztőt az SQL-adatbázis, amely át kell másolni az átjárócsomópont a HDFS való kommunikációhoz.

1. Hozzon létre a `load_flights_by_day` almappa alatt a felhasználó a helyi tárolása az átjárócsomópont elérési utat.

        ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net 'mkdir load_flights_by_day'

2. Az aktuális könyvtárban található összes fájl másolása (a `workflow.xml` és `job.properties` fájlok) akár a `load_flights_by_day` almappájában.

        scp ./* sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:load_flights_by_day

3. SSH-ból az átjárócsomóponthoz, és keresse meg a `load_flights_by_day` mappát.

        ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net
        cd load_flights_by_day

4. HDFS munkafolyamat fájlokat másolni.

        hdfs dfs -put ./* /oozie/load_flights_by_day

5. Másolás `sqljdbc41.jar` a helyi központi csomópont HDFS-ben a munkafolyamat-mappába:

        hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /oozie/load_flights_by_day

6. A munkafolyamat futtatása.

        oozie job -config job.properties -run

7. Figyelje meg a Oozie webkonzollal állapotát. Belül Ambari, válassza ki **Oozie**, **Gyorshivatkozások**, majd **Oozie Webkonzol**. Az a **munkafolyamat-feladatok** lapon jelölje be **összes feladat**.

    ![Oozie Web Console munkafolyamatok](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-workflows.png)

8. Amikor az állapot akkor sikeres volt, a lekérdezés az SQL-adatbázistáblában szereplő a beillesztett sorok megtekintéséhez. Az SQL-adatbázisra, válassza ki az Azure-portált használja, navigáljon a panelen **eszközök**, és nyissa meg a **Lekérdezésszerkesztő**.

        SELECT * FROM dailyflights

Most, hogy az egyetlen gazdagépes napon a munkafolyamat fut, akkor a munkafolyamat egy koordinátorral a munkafolyamat ütemezését, így naponta fut végző futtathatja.

### <a name="run-the-workflow-with-a-coordinator"></a>A munkafolyamat futtatása egy koordinátorral

Ez a munkafolyamat ütemezése a napi (vagy minden nap dátumtartomány) futtatása, a koordinátor is használhat. A koordinátor határozzák meg az XML-fájl, például `coordinator.xml`:

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

Ahogy látja, a legtöbb, a koordinátor, csak hogy konfigurációs adatait a munkafolyamat-példányhoz. Van azonban néhány fontos elemek hívásához.

* 1. pont: A `start` és `end` az attribútumok a `coordinator-app` maga az elem szabályozhatja a időtartam alatt, amelyben a koordinátor futtatja.

    ```
    <coordinator-app ... start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" ...>
    ```

    A koordinátor felelős belül műveleteket a `start` és `end` dátumtartományának által megadott a `frequency` attribútum. Minden ütemezett művelethez viszont a munkafolyamat fut, konfigurált módon. A koordinátor-definícióban a fenti a koordinátor 2017. január 1. a műveletek futtatására 2017. január 5. a van konfigurálva. A gyakoriság értéke 1 nap által a [Oozie kifejezés nyelvi](http://oozie.apache.org/docs/4.2.0/CoordinatorFunctionalSpec.html#a4.4._Frequency_and_Time-Period_Representation) gyakoriság kifejezés `${coord:days(1)}`. Ez a művelet ütemezés koordinátor eredményez (és így a munkafolyamat) naponta egyszer. A dátumtartományok, amelyek a múltban, ebben a példában látható módon a művelet ütemezi a késleltetés nélkül futtatásához. A dátum, amelyből művelet ütemezett elindítása elnevezése a *névleges idő*. Például a 2017. január 1. az adatok feldolgozására a koordinátor beütemezett művelet és a névleges idő 2017-01-01T00:00:00 GMT.

* Pont 2: a munkafolyamat dátum közötti tartományba a `dataset` elem megadja, hogy az adatokat egy adott dátumtartományon belül a HDFS-ben kereséséhez, és konfigurálja a hogyan Oozie határozza meg, hogy az adatok rendelkezésre áll-e még feldolgozásra.

    ```
    <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
        <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
        <done-flag></done-flag>
    </dataset>
    ```

    Az adatok elérési útja a HDFS-ben a megadott kifejezés szerinti dinamikusan épül a `uri-template` elemet. A koordinátor egy nap gyakorisággal is használják az adatkészletet. A koordinátor elem vezérlőn a kezdő és záró dátumának során, amikor a műveletek ütemezett (és határozza meg az névleges idő), a `initial-instance` és `frequency` az adatkészlettel szabályozhatja a hozhat létre, a használtdátumkiszámítása`uri-template`. Ebben az esetben állítsa be a kezdeti példányszámnak a koordinátor biztosításához a megkezdése előtt egy nappal (1/1/2017) adat tekinthető meg, hogy azt szerzi be az első napja csomagazonosítóját. A dataset számítás előregörgeti a értékének `initial-instance` (12/31/2016) lehetőségre az adatkészlet-gyakoriság (1 nap) amíg nem talál, a legutóbbi dátum, amely nem felel meg a névleges idő lépésekben állította a koordinátor (2017-01-01T00:00:00 GMT szerint az első művelet).

    Az üres `done-flag` elem azt jelzi, hogy amikor Oozie ellenőrzi, hogy a bemeneti adatok azt a kijelölt időpontban, Oozie határozza meg adatok hogy elérhető-e egy könyvtár vagy fájl jelenlétét. Ebben az esetben egy csv-fájl jelen. Ha egy csv-fájlban található, Oozie azt feltételezi, hogy az adatok készen áll, és elindítja a munkafolyamat-példányok feldolgozni a fájlt. Ha nincs jelen csv-fájl, Oozie feltételezi, hogy az adatok még nincs készen álljon és a rendszert futtató munkafolyamat hiányzóra várakozó állapotban van.

* 3. pontok: A `data-in` elem határozza meg az adott időbélyeg kívánja használni, mint a névleges time értékeinek cseréjekor `uri-template` a kapcsolódó DataSet adatkészlet esetében.

    ```
    <data-in name="event_input1" dataset="ds_input1">
        <instance>${coord:current(0)}</instance>
    </data-in>
    ```

    Ebben az esetben állítson be egy példányt kifejezésre `${coord:current(0)}`, amely az eszköz a koordinátor eredetileg ütemezett művelet névleges ideje. Más szóval a koordinátor és a névleges idő 01/01/2017 futtatását ütemezi, majd 01/01/2017 esetén cserélje le az év (2017) és a hónap (01) változók URI-sablonja használttól. Után az URI-sablonja számított erre a példányra, a Oozie ellenőrzi, hogy a várt könyvtárat vagy fájlt érhető el, és ennek megfelelően ütemezi a következő futtatáskor a munkafolyamat.

A fenti három pont olyan helyzet, ahol a koordinátor ütemezi a forrásadatok--napi módon feldolgozása yield össze. 

* 1. pont: A koordinátor 2017-01-01 névleges dátum kezdődik.

* 2 ponton: Oozie keresi a rendelkezésre álló adatok `sourceDataFolder/2017-01-FlightData.csv`.

* 3. pontok: Oozie megkeresi az adott fájlt, amikor ütemezés, amely feldolgozza az adatokat a 2017-01-01 munkafolyamat egy példányát. Oozie majd folytatja a feldolgozást a 2017-01-02. Ez a kiértékelés legfeljebb, de nem tartalmazza a 2017-01-05 ismétlődik.

A munkafolyamatok, a konfiguráció a koordinátor meghatározottak szerint a `job.properties` fájl, amely felülbírálja a munkafolyamat által használt beállításokat.

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

A csak új tulajdonságok ezen bevezetett `job.properties` fájlban található:

| Tulajdonság | Érték forrása |
| --- | --- |
| oozie.coord.application.path | Annak a helyét a `coordinator.xml` fájlt, amely tartalmazza az Oozie-koordinátor futtatásához. |
| hiveDailyTableNamePrefix | Az előtag a tábla neve az átmeneti tárolási tábla dinamikusan létrehozásakor használt. |
| hiveDataFolderPrefix | Az előtag, az elérési út az előkészítési táblák tárolásához. |

### <a name="deploy-and-run-the-oozie-coordinator"></a>Regisztrálhat és futtathat a Oozie Coordinator

Futtatjuk a folyamatot a koordinátorral, folytassa a munkafolyamat esetében hasonló módon indul ki egy mappát egy szint fölött, amely tartalmazza a munkafolyamat-karakterek kivételével. A mappa egyezmény, a különböző alárendelt munkafolyamatok társíthat egy koordinátor elválasztja a lemezen, a munkafolyamatok az összekötőket.

1. A helyi gép SCP követve másolja át a koordinátora fájlok a fürt átjárócsomópontjához helyi tárolására.

    ```bash
    scp ./* sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:~
    ```

2. SSH-ból az átjárócsomópont.

    ```bash
    ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net 
    ```

3. A koordinátor fájlokat másolhassanak HDFS.

    ```bash
    hdfs dfs -put ./* /oozie/
    ```

4. A koordinátor futtatásához.

    ```bash
    oozie job -config job.properties -run
    ```

5. Ellenőrizze az állapotukat a webkonzollal Oozie, ez alkalommal válassza a **koordinátor feladatok** lapon, majd **összes feladat**.

    ![Oozie webes konzol koordinátor feladatok](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-jobs.png)

6. Válasszon egy koordinátor-példányt az ütemezett műveletek listájának megjelenítéséhez. Ebben az esetben meg kell jelennie az 1/1/2017 és 1/4/2017 közé névleges időpontokat négy művelet közül választhat.

    ![Oozie Web Console koordinátor feladat](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-instance.png)

    Minden művelet ezen a listán, amely feldolgozza a egy nap értékelésével, ha adott napon kezdetét jelzi a névleges idő a munkafolyamat példánya megfelel.

## <a name="next-steps"></a>További lépések

* [Apache Oozie-dokumentáció](http://oozie.apache.org/docs/4.2.0/index.html)

<!-- * Build the same pipeline [using Azure Data Factory](tbd.md).  -->
