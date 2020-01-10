---
title: Működővé tenni adatelemzési folyamat – Azure
description: Állítson be és futtasson egy olyan adatfolyamatot, amelyet új adatfeldolgozás aktivál, és rövid eredményeket állít elő.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/25/2019
ms.openlocfilehash: 8217beeda885896adb6ffec2a5dd0670c9622175
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748698"
---
# <a name="operationalize-a-data-analytics-pipeline"></a>Adatelemzési folyamat üzembe helyezése

Az *adatfolyamatok* számos adat-elemzési megoldásban szerepelnek. Ahogy a neve is sugallja, az adatfolyamatok a nyers adatokba kerülnek, megtisztítják és igény szerint átalakítja őket, majd jellemzően számításokat vagy összesítéseket végeznek a feldolgozott adatok tárolása előtt. A feldolgozott adatmennyiséget az ügyfelek, jelentések vagy API-k használják. Az adatfolyamatoknak megismételhető eredményeket kell biztosítaniuk, akár menetrend szerint, akár új adatként aktiválják őket.

Ez a cikk azt ismerteti, hogyan működővé tenni az adatfolyamatok az ismételhetőség érdekében a HDInsight Hadoop-fürtökön futó Oozie használatával. A példa bemutatja egy olyan adatfolyamatot, amely felkészíti és feldolgozza a légitársaság repülési idősorozat-adatsorait.

A következő forgatókönyvben a bemeneti adatok egy egyhónapos repülési adatokat tartalmazó sima fájl. A repülési adatok olyan információkat foglalnak magukban, mint például a forrás-és a célkiszolgáló, a repült mérföldek, az indulási és érkezési időpontok, és így tovább. Ennek a folyamatnak a célja a napi légitársaságok teljesítményének összefoglalása, ahol az egyes légitársaságok minden nap egy sorral rendelkeznek, az átlagos távozási idő és az érkezési késések pedig percek alatt, valamint az aznap elrepült mérföldek száma.

| YEAR | MONTH | HÓNAP_NAPJA | FUVAROZÓ |AVG_DEP_DELAY | AVG_ARR_DELAY |TOTAL_DISTANCE |
| --- | --- | --- | --- | --- | --- | --- |
| 2017 | 1 | 3 | AA | 10,142229 | 7,862926 | 2644539 |
| 2017 | 1 | 3 | AS | 9,435449 | 5,482143 | 572289 |
| 2017 | 1 | 3 | CÍMÉNEK | 6,935409 | – 2,1893024 | 1909696 |

A példában szereplő folyamat megvárja, amíg az új időszak repülési adatai beérkeznek, majd a részletes repülési adatokat a Apache Hive adattárházba tárolja hosszú távú elemzések céljából. A folyamat egy sokkal kisebb adathalmazt is létrehoz, amely csak a napi repülési adatokat összegzi. A napi repülési összefoglalási adatküldés a jelentések, például egy webhely számára történő elküldése SQL Database történik.

Az alábbi ábrán a példa folyamat látható.

![A HDI Flight példa adatfolyamatának áttekintése](./media/hdinsight-operationalize-data-pipeline/flight-pipeline-overview.png)

## <a name="apache-oozie-solution-overview"></a>Apache Oozie-megoldás áttekintése

Ez a folyamat egy HDInsight Hadoop-fürtön futó Apache Oozie-t használ.

A Oozie a *műveletek*, *munkafolyamatok*és *koordinátorok*tekintetében ismerteti a folyamatokat. A műveletek határozzák meg a ténylegesen végrehajtandó munkát, például egy struktúra-lekérdezés futtatását. A munkafolyamatok határozzák meg a műveletek sorát. A koordinátorok határozzák meg a munkafolyamat futtatásának ütemtervét. A koordinátorok is várhatják az új adatmennyiséget, mielőtt elindítja a munkafolyamat egy példányát.

Az alábbi ábrán a példa Oozie folyamatának magas szintű kialakítása látható.

![Oozie repülési példa adatfolyamata](./media/hdinsight-operationalize-data-pipeline/pipeline-overview-oozie.png)

## <a name="provision-azure-resources"></a>Azure-erőforrások kiépítése

Ehhez a folyamathoz egy Azure SQL Database és egy HDInsight Hadoop-fürtnek kell lennie ugyanazon a helyen. A Azure SQL Database a folyamat által előállított összegző adatokat és a Oozie metaadat-tárolót is tárolja.

### <a name="provision-azure-sql-database"></a>Kiépítés Azure SQL Database

1. Hozzon létre egy Azure SQL Database. Lásd: [Azure SQL Database létrehozása a Azure Portalban](../sql-database/sql-database-single-database-get-started.md).

1. Annak biztosítása érdekében, hogy a HDInsight-fürt hozzáférhessen a csatlakoztatott Azure SQL Databasehoz, konfigurálja Azure SQL Database tűzfalszabályok, hogy az Azure-szolgáltatások és-erőforrások hozzáférjenek a kiszolgálóhoz. Ezt a beállítást a Azure Portalban engedélyezheti a **kiszolgáló tűzfalának beállítása**elem kiválasztásával, majd az **Azure-szolgáltatások és-erőforrások elérésének engedélyezése lehetőségre kattintva érheti el ezt a kiszolgálót** a Azure SQL Database-kiszolgálóhoz vagy-adatbázishoz. További információt az [IP-Tűzfalszabályok létrehozásával és kezelésével](../sql-database/sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)foglalkozó témakörben talál.

1. A [query Editor](../sql-database/sql-database-single-database-get-started.md#query-the-database) használatával hajtsa végre a következő SQL-utasításokat a `dailyflights` tábla létrehozásához, amely az összesített adatokat a folyamat minden egyes futtatása során tárolja.

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

A Azure SQL Database most már készen áll.

### <a name="provision-an-apache-hadoop-cluster"></a>Apache Hadoop-fürt kiépítése

Hozzon létre egy Apache Hadoop fürtöt egyéni metaadattár. A fürt létrehozása során a portálon a **Storage (tárolás** ) lapon győződjön meg arról, hogy a SQL Database a **metaadattár beállítások**alatt van kiválasztva. A metaadattár kiválasztásával kapcsolatos további információkért lásd: [Egyéni Metaadattár kiválasztása a fürt létrehozása során](./hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation). További információ a fürtök létrehozásáról: Ismerkedés a [HDInsight Linux rendszeren](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="verify-ssh-tunneling-set-up"></a>Az SSH-alagút beállításának ellenőrzése

Ha a Oozie webkonzol segítségével szeretné megtekinteni a koordinátori és a munkafolyamat-példányok állapotát, állítson be egy SSH-alagutat a HDInsight-fürthöz. További információ: SSH- [alagút](hdinsight-linux-ambari-ssh-tunnel.md).

> [!NOTE]  
> A Chrome és a [Foxy proxy](https://getfoxyproxy.org/) bővítmény használatával is böngészhet a fürt webes erőforrásai között az SSH-alagúton keresztül. Konfigurálja úgy, hogy a gazdagépen lévő összes kérelmet a 9876-es porton keresztül `localhost`. Ez a módszer kompatibilis a Linux Windows alrendszerével, más néven bash-mel a Windows 10-es verzióban.

1. Futtassa az alábbi parancsot egy SSH-alagút fürthöz való megnyitásához, ahol a `CLUSTERNAME` a fürt neve:

    ```cmd
    ssh -C2qTnNf -D 9876 sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Ellenőrizze, hogy az alagút működik-e, ha a fő csomóponton található Ambari navigálva keresse meg a következőt:

    `http://headnodehost:8080`

1. Ha a **Oozie Webkonzolját** a Ambari belül szeretné elérni, navigáljon a **Oozie** > **Gyorshivatkozások** > [Active Server] > **Oozie webes felhasználói felületéhez**.

## <a name="configure-hive"></a>Struktúra konfigurálása

### <a name="upload-data"></a>Adatok feltöltése

1. Töltsön le egy CSV-fájlt, amely egy hónapra vonatkozó repülési adatkészletet tartalmaz. Töltse le a ZIP-fájlt `2017-01-FlightData.zip` a [HDInsight GitHub-tárházból](https://github.com/hdinsight/hdinsight-dev-guide) , és csomagolja ki a CSV-fájlba `2017-01-FlightData.csv`.

1. Másolja ezt a CSV-fájlt a HDInsight-fürthöz csatolt Azure Storage-fiókba, és helyezze a `/example/data/flights` mappába.

    1. Az SCP használatával másolja a fájlokat a helyi gépről a HDInsight-fürt helyi tárolójába.

        ```cmd
        scp ./2017-01-FlightData.csv sshuser@CLUSTERNAME-ssh.azurehdinsight.net:2017-01-FlightData.csv
        ```

    1. A fürthöz való kapcsolódáshoz használja az [SSH-parancsot](./hdinsight-hadoop-linux-use-ssh-unix.md) . Szerkessze az alábbi parancsot úgy, hogy lecseréli `CLUSTERNAME` a fürt nevére, majd beírja a következő parancsot:

        ```cmd
        ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
        ```

    1. Az SSH-munkamenetből a HDFS parancs használatával másolja a fájlt a fő csomópont helyi tárolójából az Azure Storage-ba.

        ```bash
        hadoop fs -mkdir /example/data/flights
        hdfs dfs -put ./2017-01-FlightData.csv /example/data/flights/2017-01-FlightData.csv
        ```

### <a name="create-tables"></a>Táblázatok létrehozása

A mintaadatok mostantól elérhetők. A folyamat azonban két kaptár-táblázatot igényel a feldolgozáshoz, egyet a bejövő adatértékekhez (`rawFlights`), egyet pedig az összegzett adatértékekhez (`flights`). Hozza létre ezeket a táblákat a Ambari az alábbiak szerint.

1. Jelentkezzen be a Ambari-be a `http://headnodehost:8080`való navigálással.

2. A szolgáltatások listájából válassza a **struktúra**elemet.

    ![Az Apache Ambari Services-lista struktúra kiválasztása](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive.png)

3. A struktúra nézet 2,0 címkéjén válassza a **Ugrás** a következőhöz lehetőséget.

    ![Ambari Apache Hive összefoglaló lista](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-summary.png)

4. A lekérdezés szövege területen illessze be a következő utasításokat a `rawFlights` tábla létrehozásához. A `rawFlights` tábla az Azure Storage `/example/data/flights` mappájában található CSV-fájlok sémájának beolvasását tartalmazza.

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

5. Válassza a **végrehajtás** lehetőséget a tábla létrehozásához.

    ![HDI ambari Services-struktúra lekérdezése](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-query.png)

6. A `flights` tábla létrehozásához cserélje le a szöveget a lekérdezés szövege területen az alábbi utasításokkal. A `flights` tábla egy struktúra által felügyelt tábla, amely az évente, a hónapban és a hónap napján betöltött adatmennyiséget. Ez a táblázat tartalmazza az összes korábbi repülési adatokat, amelyek a legalacsonyabb részletességgel jelennek meg a forrásadatok egy sorában.

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

7. Válassza a **végrehajtás** lehetőséget a tábla létrehozásához.

## <a name="create-the-oozie-workflow"></a>A Oozie munkafolyamat létrehozása

A folyamatok általában egy adott időtartamon belül dolgozzák fel az adatkötegekben tárolt adatfeldolgozási folyamatokat. Ebben az esetben a folyamat naponta dolgozza fel a repülési adatátvitelt. Ez a módszer lehetővé teszi, hogy a bemeneti CSV-fájlok naponta, hetente, havonta vagy évente érkezzenek meg.

A minta munkafolyamat naponta, három fő lépésben dolgozza fel a repülési adatjáratot:

1. Futtasson egy kaptár-lekérdezést az adott napi dátumtartomány adatainak kinyeréséhez a `rawFlights` tábla által képviselt forrás CSV-fájlból, és szúrja be az adatait a `flights` táblába.
2. Futtasson egy kaptár-lekérdezést, hogy a nap folyamán dinamikusan hozzon létre egy előkészítési táblát a kaptárban, amely a napi és a szállító által összefoglalt repülési adat másolatát tartalmazza.
3. Az Apache Sqoop használatával másolja a struktúra napi előkészítési táblájában lévő összes adatforrást a Azure SQL Database cél `dailyflights` táblájába. A Sqoop beolvassa az adatokat az Azure Storage-ban található struktúra-táblázat mögötti adatokból, és a SQL Database JDBC-kapcsolatok használatával tölti be őket.

Ezt a három lépést egy Oozie munkafolyamat hangolja össze.

1. A helyi munkaállomáson hozzon létre egy `job.properties`nevű fájlt. Használja az alábbi szöveget a fájl kezdő tartalmaként.
Ezután frissítse az adott környezet értékeit. A szöveg alatti tábla összegzi az egyes tulajdonságokat, és megadja, hogy hol találhatók a saját környezetének értékei.

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

    | Tulajdonság | Érték forrása |
    | --- | --- |
    | nameNode | A HDInsight-fürthöz csatolt Azure Storage-tároló teljes elérési útja. |
    | jobTracker | A belső állomásnév az aktív fürthöz tartozó szál főcsomópontja számára. A Ambari kezdőlapján válassza a szálak lehetőséget a szolgáltatások listájából, majd válassza az Active Resource Manager lehetőséget. Az állomásnév URI-ja megjelenik az oldal tetején. Fűzze hozzá a 8050-es portot. |
    | queueName | A kaptár-műveletek ütemezésekor használt fonal-várólista neve. Hagyja meg az alapértelmezett értéket. |
    | oozie. use. System. Libpath | Igaz értékre kell lépnie. |
    | Alkalmazás gyökérkönyvtárán | Annak az Azure Storage-almappának az elérési útja, ahol a Oozie-munkafolyamatot és a támogató fájlokat telepíti. |
    | oozie. WF. Application. Path | A futtatandó Oozie munkafolyamat helye `workflow.xml`. |
    | hiveScriptLoadPartition | Az Azure Storage-beli elérési út a kaptár-lekérdezési fájlba `hive-load-flights-partition.hql`. |
    | hiveScriptCreateDailyTable | Az Azure Storage-beli elérési út a kaptár-lekérdezési fájlba `hive-create-daily-summary-table.hql`. |
    | hiveDailyTableName | Az előkészítési táblához használni kívánt dinamikusan generált név. |
    | hiveDataFolder | Az Azure Storage elérési útja az előkészítési tábla által tárolt értékekre. |
    | sqlDatabaseConnectionString | A JDBC szintaxisú kapcsolódási karakterlánc a Azure SQL Databasehoz. |
    | sqlDatabaseTableName | Annak a táblának a neve Azure SQL Database, amelyben az összesítő sorok be lesznek szúrva. Hagyja `dailyflights`. |
    | év | A nap azon napjának összetevője, amelyre a rendszer kiszámítja a repülési összefoglalókat. Hagyja, ahogy van. |
    | hónap | Azon nap hónap-összetevője, amelyre a rendszer kiszámítja a repülési összefoglalókat. Hagyja, ahogy van. |
    | napon | Azon nap hónapjának a napja, amelybe a rendszer kiszámítja a repülési összefoglalókat. Hagyja, ahogy van. |

1. A helyi munkaállomáson hozzon létre egy `hive-load-flights-partition.hql`nevű fájlt. Használja az alábbi kódot a fájl tartalmához.

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

    A Oozie változók a `${variableName}`szintaxist használják. Ezek a változók a `job.properties` fájlban vannak beállítva. A Oozie a tényleges értékeket a futásidőben helyettesíti.

1. A helyi munkaállomáson hozzon létre egy `hive-create-daily-summary-table.hql`nevű fájlt. Használja az alábbi kódot a fájl tartalmához.

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

    Ez a lekérdezés olyan előkészítési táblát hoz létre, amely csak egy nap összesített adatait tárolja, jegyezze fel a SELECT utasítást, amely kiszámítja az átlagos késéseket és a szolgáltató által naponta eltelt teljes távolságot. A táblázatba beszúrt, ismert helyen (az hiveDataFolder változó által jelzett útvonalon) tárolt adatforrások, amelyek a következő lépésben használhatók a Sqoop forrásaként.

1. A helyi munkaállomáson hozzon létre egy `workflow.xml`nevű fájlt. Használja az alábbi kódot a fájl tartalmához. A fenti lépések külön műveletként jelennek meg a Oozie munkafolyamat-fájlban.

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

A két kaptár lekérdezését az Azure Storage elérési útja éri el, a többi változó értékét pedig a `job.properties` fájl is megadja. Ez a fájl az 2017-as január 3. dátummal történő futtatásra konfigurálja a munkafolyamatot.

## <a name="deploy-and-run-the-oozie-workflow"></a>A Oozie munkafolyamat üzembe helyezése és futtatása

A bash-munkamenet SZOLGÁLTATÁSKAPCSOLÓDÁSI pontjának használatával üzembe helyezheti a Oozie-munkafolyamatot (`workflow.xml`), a kaptár-lekérdezéseket (`hive-load-flights-partition.hql` és `hive-create-daily-summary-table.hql`) és a feladatok konfigurációját (`job.properties`).  A Oozie-ben csak a `job.properties` fájl létezhet a átjárócsomóponthoz helyi tárolójában. Az összes többi fájlt a HDFS-ben kell tárolni, ebben az esetben az Azure Storage-ban. A munkafolyamat által használt Sqoop művelet egy JDBC-illesztőtől függ, amely a SQL Database kommunikál, amelyet a fő csomópontról a HDFS-be kell másolni.

1. Hozza létre a `load_flights_by_day` almappát a felhasználó elérési útja alatt a fő csomópont helyi tárolójában. Az Open SSH-munkamenetből hajtsa végre a következő parancsot:

    ```bash
    mkdir load_flights_by_day
    ```

1. Másolja az aktuális könyvtárban lévő összes fájlt (a `workflow.xml` és `job.properties` fájlokat) a `load_flights_by_day` almappába. A helyi munkaállomáson hajtsa végre a következő parancsot:

    ```cmd
    scp ./* sshuser@CLUSTERNAME-ssh.azurehdinsight.net:load_flights_by_day
    ```

1. Másolja a munkafolyamat-fájlokat a HDFS. Az Open SSH-munkamenetből hajtsa végre a következő parancsokat:

    ```bash
    cd load_flights_by_day
    hadoop fs -mkdir -p /oozie/load_flights_by_day
    hdfs dfs -put ./* /oozie/load_flights_by_day
    ```

1. Másolja `mssql-jdbc-7.0.0.jre8.jar` a helyi főcsomópontról a HDFS munkafolyamat mappájába. Szükség esetén módosítsa a parancsot, ha a fürt egy másik jar-fájlt tartalmaz. Egy másik jar-fájl tükrözéséhez szükség szerint módosítsa `workflow.xml`. Az Open SSH-munkamenetből hajtsa végre a következő parancsot:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /oozie/load_flights_by_day
    ```

1. Futtassa a munkafolyamatot. Az Open SSH-munkamenetből hajtsa végre a következő parancsot:

    ```bash
    oozie job -config job.properties -run
    ```

1. Figyelje meg az állapotot a Oozie webkonzol használatával. A Ambari-on belül válassza a **Oozie**, a **gyors hivatkozások**, majd a **Oozie webkonzol**lehetőséget. A **munkafolyamat-feladatok** lapon válassza a **minden feladat**lehetőséget.

    ![HDI oozie webkonzol-munkafolyamatok](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-workflows.png)

1. Ha az állapot sikeres, a beszúrt sorok megjelenítéséhez kérdezze le a SQL Database táblát. A Azure Portal használatával navigáljon a SQL Database ablaktáblához, válassza az **eszközök**lehetőséget, majd nyissa meg a **lekérdezés-szerkesztőt**.

        SELECT * FROM dailyflights

Most, hogy a munkafolyamat egy tesztelési napra fut, becsomagolhatja ezt a munkafolyamatot egy olyan koordinátorba, amely a munkafolyamatot naponta futtatja.

## <a name="run-the-workflow-with-a-coordinator"></a>A munkafolyamat futtatása koordinátorral

Ha úgy szeretné ütemezni ezt a munkafolyamatot, hogy naponta (vagy a dátumtartomány összes napján) fusson, a koordinátort is használhatja. A koordinátort egy XML-fájl határozza meg, például `coordinator.xml`:

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

Amint láthatja, a koordinátor többsége csak a konfigurációs adatokat továbbítja a munkafolyamat-példánynak. Van azonban néhány fontos elem, amelyet meghívhat.

* 1\. pont: a `coordinator-app` elem `start` és `end` attribútumai szabályozzák azt az időtartamot, ameddig a koordinátor fut.

    ```
    <coordinator-app ... start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" ...>
    ```

    A koordinátor feladata, hogy az `frequency` attribútum által meghatározott intervallumnak megfelelően a `start` és `end` Dátumtartomány alapján ütemezze a műveleteket. Minden ütemezett művelet a munkafolyamatot konfiguráltként futtatja. A fenti koordinátor-definícióban a koordinátor úgy van konfigurálva, hogy a 2017. január 1-től 2017-ig terjedő műveleteket futtasson. A gyakoriság értéke egy nap a [Oozie kifejezés nyelvi](https://oozie.apache.org/docs/4.2.0/CoordinatorFunctionalSpec.html#a4.4._Frequency_and_Time-Period_Representation) gyakoriságának kifejezése `${coord:days(1)}`. Ennek eredményeképpen a koordinátor naponta egyszer ütemez egy műveletet (és így a munkafolyamatot). A múltban lévő dátumtartomány esetében, ahogy az ebben a példában a művelet az ütemezve lesz, hogy késedelem nélkül fusson. Annak a dátumnak a kezdete, amelyből a művelet futtatása ütemezve van, a *névleges idő*. Ha például az 2017 január 1-től szeretné feldolgozni az adatfeldolgozást, akkor a koordinátor a 2017-01-01T00:00:00 GMT névleges idő szerint fogja ütemezni a műveletet.

* 2\. pont: a munkafolyamaton belül a `dataset` elem megadja, hogy hol kell keresni a HDFS az adott dátumtartomány adataihoz, valamint azt, hogy a Oozie hogyan határozza meg, hogy az adatok még a feldolgozáshoz is elérhetők-e.

    ```xml
    <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
        <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
        <done-flag></done-flag>
    </dataset>
    ```

    A HDFS-ben lévő információk elérési útja dinamikusan van felépítve a `uri-template` elemben megadott kifejezésnek megfelelően. Ebben a koordinátorban az adatkészlet egy nap gyakoriságát is használja. Míg a koordinátor elemnél a műveletek ütemezésekor (és a névleges időpontok meghatározásakor) a kezdő és a záró dátum is látható, az adathalmaz `initial-instance` és `frequency` a `uri-template`összeállításakor használt dátum kiszámítását vezérli. Ebben az esetben állítsa be a kezdeti példányt a koordinátor kezdete előtt egy nappal, hogy az első nap (1/1/2017) értékét is felveszi. Az adatkészlet dátumának kiszámítása a `initial-instance` (12/31/2016) értéket adja vissza az adatkészletek gyakoriságának növekményében (egy nap), amíg meg nem találja a koordinátor által beállított névleges időpontot (2017-01-01T00:00:00 GMT az első művelethez).

    Az üres `done-flag` elem azt jelzi, hogy amikor a Oozie a megadott időpontban ellenőrzi a bemeneti adatok jelenlétét, a Oozie meghatározza, hogy az adatok egy adott könyvtár vagy fájl jelenlétében érhetők-e el. Ebben az esetben ez egy CSV-fájl jelenléte. Ha van egy CSV-fájl, a Oozie feltételezi, hogy az adott objektum készen áll, és elindítja a munkafolyamat-példányt a fájl feldolgozásához. Ha nincs CSV-fájl, a Oozie feltételezi, hogy az adatmennyiség még nem áll készen, és a munkafolyamat futtatása várakozási állapotba kerül.

* 3\. pont: a `data-in` elem adja meg az adott időbélyeget, amelyet a rendszer a társított adatkészlet `uri-template` értékeinek cseréjekor a névleges időpontra használ.

    ```xml
    <data-in name="event_input1" dataset="ds_input1">
        <instance>${coord:current(0)}</instance>
    </data-in>
    ```

    Ebben az esetben állítsa a példányt a következő kifejezésre: `${coord:current(0)}`, amely a művelet névleges időpontját használja, ahogy eredetileg a koordinátor ütemezte. Más szóval, amikor a koordinátor a 01/01/2017-as névleges időpontra ütemezze a műveletet, akkor a 01/01/2017 az év (2017) és a MONTH (01) változók cseréjére szolgál az URI-sablonban. Ha az URI-sablon ki van számítva ehhez a példányhoz, a Oozie ellenőrzi, hogy a várt könyvtár vagy fájl elérhető-e, és ennek megfelelően ütemezze a munkafolyamat következő futtatását.

Az előző három pont kombinálva olyan helyzetet eredményez, amelyben a koordinátor napi módon dolgozza fel a forrásadatok feldolgozását.

* 1\. pont: a koordinátor 2017-01-01-as névleges dátummal kezdődik.

* 2\. pont: a Oozie a `sourceDataFolder/2017-01-FlightData.csv`ban elérhetővé tételeket keresi.

* 3\. pont: amikor a Oozie megkeresi ezt a fájlt, a a munkafolyamat egy példányát ütemezze, amely az 2017-01-01-es adatfeldolgozást fogja feldolgozni. A Oozie ezután a 2017-01-02-es feldolgozást folytatja. Ez az értékelés legfeljebb 2017-01-05-re ismétlődik.

A munkafolyamatokhoz hasonlóan a koordinátor konfigurációja egy `job.properties` fájlban van definiálva, amely a munkafolyamat által használt beállításokkal rendelkezik.

```
nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
<<<<<<< HEAD
jobTracker=[ACTIVE-HEADNODE-NAME].[UNIQUESTRING].dx.internal.cloudapp.net:8050
=======
jobTracker=[ACTIVERESOURCEMANAGER]:8050
>>>>>>> 50a435bd9528fcbaac7bc5ab96745734e63167da
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

Ebben a `job.properties` fájlban csak az alábbi új tulajdonságok jelennek meg:

| Tulajdonság | Érték forrása |
| --- | --- |
| oozie. coord. Application. Path | Megadja a futtatni kívánt Oozie-koordinátort tartalmazó `coordinator.xml` fájl helyét. |
| hiveDailyTableNamePrefix | Az előkészítési tábla tábla nevének dinamikus létrehozásakor használt előtag. |
| hiveDataFolderPrefix | Azon elérési út előtagja, ahol az összes előkészítési tábla tárolva lesz. |

## <a name="deploy-and-run-the-oozie-coordinator"></a>A Oozie-koordinátor üzembe helyezése és futtatása

Ha a folyamatot egy koordinátorral szeretné futtatni, folytassa a munkafolyamathoz hasonló módon, ha a munkafolyamatot tartalmazó mappa egy szintjének egyikével dolgozik. Ez a mappa-egyezmény elválasztja a koordinátorokat a lemezen lévő munkafolyamatokból, így egy koordinátort társíthat különböző alárendelt munkafolyamatokkal.

1. A helyi gépről származó SCP használatával másolja a koordinátori fájlokat a fürt fő csomópontjának helyi tárolójába.

    ```bash
    scp ./* sshuser@CLUSTERNAME-ssh.azurehdinsight.net:~
    ```

2. SSH-t a fő csomópontba.

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Másolja a koordinátor fájljait a HDFS.

    ```bash
    hdfs dfs -put ./* /oozie/
    ```

4. Futtassa a koordinátort.

    ```bash
    oozie job -config job.properties -run
    ```

5. Ellenőrizze az állapotot a Oozie webkonzolon, ezúttal válassza a **koordinátori feladatok** fület, majd az **összes feladatot**.

    ![Oozie webkonzol-koordinátori feladatok](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-jobs.png)

6. Válasszon ki egy koordinátori példányt az ütemezett műveletek listájának megjelenítéséhez. Ebben az esetben négy műveletet kell látnia a névleges időpontok között a 1/1/2017 és 1/4/2017 közötti tartományban.

    ![Oozie webkonzol-koordinátori feladata](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-instance.png)

    Az ebben a listában szereplő műveletek a munkafolyamat egy olyan példányának felelnek meg, amely egy nap értékű adatot dolgoz fel, és az adott nap kezdetét a névleges idő jelzi.

## <a name="next-steps"></a>Következő lépések

[Az Apache Oozie dokumentációja](https://oozie.apache.org/docs/4.2.0/index.html)
