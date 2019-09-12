---
title: Hadoop Oozie-munkafolyamatok használata Linux-alapú Azure-HDInsight
description: Hadoop Oozie használata Linux-alapú HDInsight. Ismerje meg, hogyan határozhat meg egy Oozie-munkafolyamatot, és hogyan küldhet el egy Oozie-feladatot.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: d601dc1efe8dc3f6f2678f5d4df03f172146cd07
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900646"
---
# <a name="use-apache-oozie-with-apache-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>Az Apache Oozie és a Apache Hadoop használata munkafolyamatok definiálásához és futtatásához Linux-alapú Azure-HDInsight

Ismerje meg, hogyan használhatja az Apache Oozie-t az Apache Hadoop Azure HDInsight. A Oozie olyan munkafolyamat-és koordinációs rendszer, amely a Hadoop-feladatokat kezeli. A Oozie integrálva van a Hadoop-verembe, és a következő feladatokat támogatja:

* Apache Hadoop MapReduce
* Apache Pig
* Apache Hive
* Apache Sqoop

A Oozie segítségével a rendszerre vonatkozó feladatokat is ütemezhet, például a Java-programokat vagy a rendszerhéj-parancsfájlokat.

> [!NOTE]  
> A munkafolyamatok HDInsight való definiálásának másik lehetősége a Azure Data Factory használata. Ha többet szeretne megtudni a Data Factoryről, tekintse meg [az Apache Pig és a Apache Hive használata a Data Factory használatával][azure-data-factory-pig-hive]című témakört. Ha Enterprise Security Package Oozie szeretné használni a fürtökön, tekintse meg a következőt: [Apache Oozie futtatása a HDInsight Hadoop-fürtökön a Enterprise Security Package](domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)használatával.


## <a name="prerequisites"></a>Előfeltételek

* **Hadoop-fürt a HDInsight-on**. Lásd: Ismerkedés [a HDInsight Linux rendszeren](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Egy SSH-ügyfél**. Lásd: [Kapcsolódás HDInsight (Apache Hadoop) SSH használatával](hdinsight-hadoop-linux-use-ssh-unix.md).

* **Egy Azure SQL Database**.  Lásd: [Azure SQL Database létrehozása a Azure Portalban](../sql-database/sql-database-get-started.md).  Ez a cikk egy nevű `oozietest`adatbázist használ.

* A fürtök elsődleges tárolójának [URI-sémája](./hdinsight-hadoop-linux-information.md#URI-and-scheme) . Ez az Azure `wasb://` `abfs://` Storage számára lenne Azure Data Lake Storage Gen2 vagy `adl://` Azure Data Lake Storage Gen1 esetén. Ha az Azure Storage vagy a Data Lake Storage Gen2 esetében engedélyezve van a biztonságos átvitel, az `wasbs://` URI `abfss://`-t vagy a-t, illetve a [biztonságos átvitelt](../storage/common/storage-require-secure-transfer.md)is el kell látnia.


## <a name="example-workflow"></a>Példa munkafolyamat

A dokumentumban használt munkafolyamat két műveletet tartalmaz. A műveletek olyan tevékenységek definíciói, mint például a kaptár, a Sqoop, a MapReduce vagy más folyamatok futtatása:

![Munkafolyamat-diagram][img-workflow-diagram]

1. A kaptárak egy HiveQL parancsfájlt futtatnak a `hivesampletable` HDInsight-ben található rekordok kinyeréséhez. Az egyes adatsorok egy adott mobileszköz egy látogatását írják le. A rekord formátuma az alábbi szöveghez hasonlóan jelenik meg:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    A dokumentumban használt struktúra-szkript megszámolja az egyes platformok, például az Android vagy az iPhone összes látogatását, és a leltárt egy új struktúrás táblára tárolja.

    További információ a Kaptárról: [Apache Hive használata a HDInsight][hdinsight-use-hive].

2. A Sqoop művelet az új struktúra táblázatának tartalmát exportálja Azure SQL Databaseban létrehozott táblába. További információ a Sqoop: az [Apache Sqoop használata a HDInsight][hdinsight-use-sqoop].

> [!NOTE]  
> A HDInsight-fürtök támogatott Oozie-verzióiért lásd: [Újdonságok a Hadoop-fürt HDInsight által biztosított verzióiban][hdinsight-versions].

## <a name="create-the-working-directory"></a>A munkakönyvtár létrehozása

A Oozie arra vár, hogy a feladatokhoz szükséges összes erőforrást ugyanabban a könyvtárban tárolja. Ez a példa `wasbs:///tutorials/useoozie`a következőt használja:. A könyvtár létrehozásához hajtsa végre a következő lépéseket:

1. Szerkessze az alábbi kódot, `sshuser` hogy lecserélje a fürt SSH-felhasználónevét, `clustername` és a helyére a fürt nevét írja.  Ezután írja be a kódot, hogy az [SSH használatával](hdinsight-hadoop-linux-use-ssh-unix.md)csatlakozzon a HDInsight-fürthöz.  

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

2. A könyvtár létrehozásához használja a következő parancsot:

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]  
    > A `-p` paraméter az összes könyvtár létrehozását okozza az elérési úton. A `data` könyvtár a `useooziewf.hql` parancsfájl által használt adattárolásra szolgál.

3. Szerkessze az alábbi kódot, `username` és cserélje le az SSH-felhasználónevét.  Annak érdekében, hogy a Oozie megszemélyesítse a felhasználói fiókját, használja a következő parancsot:

    ```bash
    sudo adduser username users
    ```

    > [!NOTE]  
    > Figyelmen kívül hagyhatja azokat a hibákat, amelyek azt jelzik, hogy `users` a felhasználó már tagja a csoportnak.

## <a name="add-a-database-driver"></a>Adatbázis-illesztőprogram hozzáadása

Mivel ez a munkafolyamat az Sqoop-t használja az SQL Database-be való exportáláshoz, meg kell adnia az SQL Database szolgáltatással való kommunikációhoz használt JDBC-illesztőprogram másolatát. A JDBC-illesztő munkakönyvtárba való másolásához használja az alábbi parancsot az SSH-munkamenetből:

```bash
hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /tutorials/useoozie/
```

> [!IMPORTANT]  
> Ellenőrizze, hogy a jelenlegi JDBC-illesztőprogram `/usr/share/java/`létezik-e.

Ha a munkafolyamata más erőforrásokat (például egy MapReduce-alkalmazást tartalmazó jar) használ, ezeket az erőforrásokat is fel kell vennie.

## <a name="define-the-hive-query"></a>A kaptár lekérdezésének megadása

A következő lépésekkel hozhat létre egy, a-lekérdezést definiáló kaptár-lekérdezési nyelvi (HiveQL) parancsfájlt. A lekérdezést a dokumentum későbbi részében egy Oozie-munkafolyamatban fogja használni.

1. Az SSH-kapcsolatban használja a következő parancsot egy nevű `useooziewf.hql`fájl létrehozásához:

    ```bash
    nano useooziewf.hql
    ```

3. A GNU Nano Editor megnyitása után használja a következő lekérdezést a fájl tartalmaként:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    A parancsfájlban két változó szerepel:

   * `${hiveTableName}`: A létrehozandó tábla nevét tartalmazza.

   * `${hiveDataFolder}`: A tábla adatfájljainak tárolására szolgáló helyet tartalmazza.

     A munkafolyamat-definíciós fájl (workflow. xml) ebben a cikkben a futtatáskor átadja ezeket az értékeket erre a HiveQL-parancsfájlra.

4. A fájl mentéséhez válassza a CTRL + X billentyűkombinációt, `Y`írja be a következőt:, majd válassza az **ENTER**billentyűt.  

5. A következő parancs használatával másolhatja `useooziewf.hql`: `wasbs:///tutorials/useoozie/useooziewf.hql`

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    Ez a parancs a `useooziewf.hql` fürt HDFS-kompatibilis tárolójában tárolja a fájlt.

## <a name="define-the-workflow"></a>A munkafolyamat definiálása

A Oozie munkafolyamat-definíciókat Hadoop Process Definition Language (hPDL) nyelven írták, amely egy XML-folyamat definíciós nyelve. A munkafolyamat definiálásához kövesse az alábbi lépéseket:

1. Új fájl létrehozásához és szerkesztéséhez használja az alábbi utasítást:

    ```bash
    nano workflow.xml
    ```

2. A nano Editor megnyitása után írja be a következő XML-fájlt a fájl tartalmába:

    ```xml
    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>
        <action name="RunHiveScript">
        <hive xmlns="uri:oozie:hive-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.job.queue.name</name>
                <value>${queueName}</value>
            </property>
            </configuration>
            <script>${hiveScript}</script>
            <param>hiveTableName=${hiveTableName}</param>
            <param>hiveDataFolder=${hiveDataFolder}</param>
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
            <arg>${hiveDataFolder}</arg>
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

    A munkafolyamatban két művelet van definiálva:

   * `RunHiveScript`: Ez a művelet az indítási művelet, és futtatja a `useooziewf.hql` kaptár parancsfájlt.

   * `RunSqoopExport`: Ez a művelet a Sqoop használatával exportálja a struktúra-parancsfájlból létrehozott adatait egy SQL-adatbázisba. Ez a művelet csak akkor fut `RunHiveScript` , ha a művelet sikeres.

     A munkafolyamat több bejegyzést tartalmaz, például `${jobTracker}`:. Ezeket a bejegyzéseket a feladatdefiníció által használt értékekkel helyettesíti. A feladattípust a dokumentum későbbi részében fogja létrehozni.

     Jegyezze fel a `<archive>mssql-jdbc-7.0.0.jre8.jar</archive>` bejegyzést is a Sqoop szakaszban. Ez a bejegyzés arra utasítja a Oozie, hogy ezt az archívumot a Sqoop számára elérhetővé tegye a művelet futtatásakor.

3. A fájl mentéséhez válassza a CTRL + X billentyűkombinációt, `Y`írja be a következőt:, majd válassza az **ENTER**billentyűt.  

4. A következő parancs használatával másolja a `workflow.xml` `/tutorials/useoozie/workflow.xml`fájlt a következőre:

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-a-table"></a>Tábla létrehozása

> [!NOTE]  
> A SQL Databasehoz való kapcsolódás számos módon lehetséges a tábla létrehozásához. A következő lépések során a [FreeTDS](https://www.freetds.org/) eszközt használjuk a HDInsight-fürtről.

1. Használja a következő parancsot a FreeTDS telepítéséhez a HDInsight-fürtön:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Szerkessze az alábbi kódot, `<serverName>` és cserélje le az Azure SQL-kiszolgáló `<sqlLogin>` nevét és az Azure SQL Server-bejelentkezést.  Adja meg az előfeltételként szükséges SQL-adatbázishoz való kapcsolódáshoz szükséges parancsot.  Adja meg a jelszót a parancssorban.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    Az alábbi szöveghez hasonló kimenetet kap:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>

3. Az `1>` parancssorban írja be a következő sorokat:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [deviceplatform] [nvarchar](50),
    [count] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
    GO
    ```

    A `GO` utasítás megadásakor a rendszer kiértékeli az előző utasításokat. Ezek az utasítások létrehoznak egy nevű `mobiledata`táblát, amelyet a munkafolyamat használ.

    A tábla létrejöttének ellenőrzéséhez használja a következő parancsokat:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    Az alábbi szöveghez hasonló kimenet jelenik meg:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo             mobiledata      BASE TABLE

4. Lépjen ki a TSQL segédprogramból `exit` a `1>` parancssorba való beírásával.

## <a name="create-the-job-definition"></a>A feladatdefiníció létrehozása

A feladatdefiníció leírja, hol található a munkafolyamat. xml fájl. Azt is leírja, hogy hol található a munkafolyamat által használt egyéb fájlok, például `useooziewf.hql`:. Emellett meghatározza a munkafolyamatban és a társított fájlokban használt tulajdonságok értékeit is.

1. Az alapértelmezett tárterület teljes címe az alábbi paranccsal kérhető le. Ezt a címeket a következő lépésben létrehozott konfigurációs fájlban fogja használni a rendszer.

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    Ez a parancs a következő XML-hez hasonló adatokat ad vissza:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]  
    > Ha a HDInsight-fürt az Azure Storage-t használja alapértelmezett tárolóként, az `<value>` elem tartalma a következőképpen `wasbs://`kezdődik:. Ha a Azure Data Lake Storage Gen1 használja helyette, azzal kezdődik `adl://`. Ha Azure Data Lake Storage Gen2 van használatban, azzal kezdődik `abfs://`.

    Mentse az `<value>` elem tartalmát, ahogy azt a következő lépésekben is használják.

2. Szerkessze az alábbi XML-t az alábbiak szerint:

    |Helyőrző értéke| Lecserélt érték|
    |---|---|
    |wasbs://mycontainer\@mystorageaccount.blob.core.windows.net| Az 1. lépésből kapott érték.|
    |felügyeleti| A HDInsight-fürt bejelentkezési neve, ha a rendszergazda nem.|
    |Kiszolgálónév| Az Azure SQL Database-kiszolgáló neve.|
    |sqlLogin| Azure SQL Database-kiszolgáló bejelentkezése.|
    |sqlPassword| Azure SQL Database-kiszolgáló bejelentkezési jelszava.|

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
        <name>nameNode</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
        </property>

        <property>
        <name>jobTracker</name>
        <value>headnodehost:8050</value>
        </property>

        <property>
        <name>queueName</name>
        <value>default</value>
        </property>

        <property>
        <name>oozie.use.system.libpath</name>
        <value>true</value>
        </property>

        <property>
        <name>hiveScript</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
        </property>

        <property>
        <name>hiveTableName</name>
        <value>mobilecount</value>
        </property>

        <property>
        <name>hiveDataFolder</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
        </property>

        <property>
        <name>sqlDatabaseConnectionString</name>
        <value>"jdbc:sqlserver://serverName.database.windows.net;user=sqlLogin;password=sqlPassword;database=oozietest"</value>
        </property>

        <property>
        <name>sqlDatabaseTableName</name>
        <value>mobiledata</value>
        </property>

        <property>
        <name>user.name</name>
        <value>admin</value>
        </property>

        <property>
        <name>oozie.wf.application.path</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
    </configuration>
    ```

    A fájlban található információk többsége a Workflow. XML vagy ooziewf. HQL fájlokban használt értékek feltöltésére szolgál, például `${nameNode}`:.  Ha az elérési út `wasbs` egy elérési út, akkor a teljes elérési utat kell használnia. Ne rövidítse le egyszerűen `wasbs:///`. A `oozie.wf.application.path` bejegyzés határozza meg, hogy hol található a munkafolyamat. xml fájlja. Ez a fájl tartalmazza a feladatok által futtatott munkafolyamatot.

3. A Oozie-feladatdefiníció konfigurációjának létrehozásához használja a következő parancsot:

    ```bash
    nano job.xml
    ```

4. A nano Editor megnyitása után illessze be a szerkesztett XML-fájlt a fájl tartalmába.

5. A fájl mentéséhez válassza a CTRL + X billentyűkombinációt, `Y`írja be a következőt:, majd válassza az **ENTER**billentyűt.

## <a name="submit-and-manage-the-job"></a>A feladatok elküldése és kezelése

A következő lépések a Oozie parancs használatával küldenek és kezelhetnek Oozie munkafolyamatokat a fürtön. A Oozie parancs egy felhasználóbarát felület a [Oozie REST API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [!IMPORTANT]  
> A Oozie parancs használatakor a HDInsight-fej csomópontjának teljes tartománynevét kell használnia. Ez a teljes tartománynév csak a fürtből érhető el, vagy ha a fürt egy Azure-beli virtuális hálózaton található, ugyanazon a hálózaton lévő más gépekről.

1. A Oozie szolgáltatás URL-címének beszerzéséhez használja a következő parancsot:

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Ez a következő XML-hez hasonló adatokat ad vissza:

    ```xml
    <name>oozie.base.url</name>
    <value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    A `http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie` rész a Oozie paranccsal használandó URL-cím.

2. Szerkessze a kódot, és cserélje le a korábban kapott URL-címet. Az URL-cím környezeti változójának létrehozásához használja a következőt, így nem kell megadnia az összes parancshoz:

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

3. A feladatok elküldéséhez használja a következőt:

    ```bash
    oozie job -config job.xml -submit
    ```

    Ez a parancs betölti a feladatra vonatkozó adatokat `job.xml` , és elküldi azt a Oozie, de nem futtatja azt.

    A parancs befejeződése után vissza kell adni a feladatokhoz tartozó azonosítót, például `0000005-150622124850154-oozie-oozi-W`:. Ez az azonosító a feladatok kezelésére szolgál.

4. Az alábbi kód szerkesztésével cserélje `<JOBID>` le az előző lépésben visszaadott azonosítót.  A feladatok állapotának megtekintéséhez használja a következő parancsot:

    ```bash
    oozie job -info <JOBID>
    ```

    Ez a következő szöveghez hasonló adatokat ad vissza:

        Job ID : 0000005-150622124850154-oozie-oozi-W
        ------------------------------------------------------------------------------------------------------------------------------------
        Workflow Name : useooziewf
        App Path      : wasb:///tutorials/useoozie
        Status        : PREP
        Run           : 0
        User          : USERNAME
        Group         : -
        Created       : 2015-06-22 15:06 GMT
        Started       : -
        Last Modified : 2015-06-22 15:06 GMT
        Ended         : -
        CoordAction ID: -
        ------------------------------------------------------------------------------------------------------------------------------------

    A feladattípus állapota `PREP`:. Ez az állapot azt jelzi, hogy a feladatot létrehozták, de nem indult el.

5. Szerkessze az alábbi kódot, `<JOBID>` és cserélje le a értéket a korábban visszaadott azonosítóra.  A feladatok elindításához használja a következő parancsot:

    ```bash
    oozie job -start <JOBID>
    ```

    Ha a parancs után bejelöli az állapotot, a rendszer futó állapotba kerül, és a feladatban szereplő műveletekhez adatokat ad vissza.  A feladatok elvégzése eltarthat néhány percig.

6. Szerkessze az alábbi kódot, `<serverName>` és cserélje le az Azure SQL-kiszolgáló `<sqlLogin>` nevét és az Azure SQL Server-bejelentkezést.  A feladat sikeres befejeződése után ellenőrizheti, hogy az adatok létrejöttek és exportálhatók az SQL Database-táblázatba az alábbi parancs használatával.  Adja meg a jelszót a parancssorban.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    A `1>` parancssorba írja be a következő lekérdezést:

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    A visszaadott információ az alábbi szöveghez hasonló:

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

További információ a Oozie parancsról: [Apache Oozie parancssori eszköz](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

## <a name="oozie-rest-api"></a>Oozie REST API

A Oozie REST API segítségével saját eszközöket hozhat létre, amelyek a Oozie használatával működnek. Az alábbi HDInsight-specifikus információk a Oozie REST API használatával kapcsolatban:

* **URI**: A REST API a fürtön `https://CLUSTERNAME.azurehdinsight.net/oozie`kívülről is elérheti.

* **Hitelesítés**: A hitelesítéshez használja az API-t a fürt HTTP-fiókjával (admin) és a jelszóval. Példa:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

További információ a Oozie REST API használatáról: [Apache Oozie Web Services API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>Oozie webes felhasználói felület

A Oozie webes felhasználói felülete webes nézetet biztosít a fürt Oozie-feladatai állapotához. A webes felhasználói felülettel a következő információkat tekintheti meg:

   * Feladat állapota
   * Feladat definíciója
   * Konfiguráció
   * A feladatokban szereplő műveletek gráfja
   * A feladatokhoz tartozó naplók

Megtekintheti egy adott feladaton belüli műveletek részleteit is.

A Oozie webes felhasználói felületének eléréséhez hajtsa végre a következő lépéseket:

1. Hozzon létre egy SSH-alagutat a HDInsight-fürthöz. További információ: az [SSH-alagút használata a HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

2. Az alagút létrehozása után nyissa meg a Ambari webes felhasználói felületét a böngészőben az URI `http://headnodehost:8080`használatával.

3. A lap bal oldalán válassza a **Oozie** > **Gyorshivatkozások** > **Oozie webes felhasználói felület**lehetőséget.

    ![A menük képe](./media/hdinsight-use-oozie-linux-mac/hdi-oozie-web-ui-steps.png)

4. A Oozie webes felhasználói felülete alapértelmezés szerint megjeleníti a futó munkafolyamat-feladatokat. A munkafolyamat összes feladatának megtekintéséhez válassza a **minden feladat**lehetőséget.

    ![Az összes megjelenített feladat](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-jobs.png)

5. A feladattal kapcsolatos további információk megtekintéséhez válassza ki a feladatot.

    ![Munkakör adatai](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-info.png)

6. A feladat **adatai** lapon megtekintheti az alapszintű feladatok adatait és a feladaton belüli egyes műveleteket. A felül található lapfülek segítségével megtekintheti **a feladatdefiníció, a** **feladatok konfigurációja**, a **Projektnapló**hozzáférése vagy a feladathoz tartozó aciklikus gráf (Dag) megtekintését **.**

   * **Projektnapló**: Kattintson a **naplók beolvasása** gombra a feladatokhoz tartozó összes napló beolvasásához, vagy használja a **keresési szűrő megadása** mezőt a naplók szűréséhez.

       ![Projektnapló](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-log.png)

   * **Dag-feladatok**: A DAG a munkafolyamaton keresztül végrehajtott adatelérési utak grafikus áttekintése.

       ![DAG-feladatok](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-dag.png)

7. Ha a **Job info (feladatok** ) lapon a műveletek egyikét választja, akkor a műveletre vonatkozó információkat hoz létre. Válassza ki például a **RunSqoopExport** műveletet.

    ![Művelet adatai](./media/hdinsight-use-oozie-linux-mac/oozie-job-action-info.png)

8. Láthatja a művelet részleteit, például a **konzol URL-címére**mutató hivatkozást. Ezzel a hivatkozással megtekintheti a feladathoz tartozó feladatok követési adatait.

## <a name="schedule-jobs"></a>Feladatok ütemezése

A koordinátor használatával megadhatja a feladatok kezdési, befejezési és előfordulási gyakoriságát. A munkafolyamat ütemtervének definiálásához hajtsa végre a következő lépéseket:

1. A következő parancs használatával hozzon létre egy **koordinátor. XML**nevű fájlt:

    ```bash
    nano coordinator.xml
    ```

    Használja a következő XML-fájlt a fájl tartalmához:

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
        <workflow>
            <app-path>${workflowPath}</app-path>
        </workflow>
        </action>
    </coordinator-app>
    ```

    > [!NOTE]  
    > A `${...}` változókat a feladatütemezés értékeit lecseréli futásidőben. A változók a következők:
    >
    > * `${coordFrequency}`: A feladatokhoz tartozó futó példányok közötti idő.
    > * `${coordStart}`: A feladatok kezdési ideje.
    > * `${coordEnd}`: A feladatok befejezési időpontja.
    > * `${coordTimezone}`: A koordinátori feladatok olyan rögzített időzónában vannak, amely nem rendelkezik a nyári időszámítás idejével, jellemzően UTC használatával. Ezt az időzónát a *Oozie feldolgozási időzónájának nevezzük.*
    > * `${wfPath}`: A munkafolyamat. XML fájljának elérési útja.

2. A fájl mentéséhez válassza a CTRL + X billentyűkombinációt, `Y`írja be a következőt:, majd válassza az **ENTER**billentyűt.

3. Ha a fájlt a feladatokhoz tartozó munkakönyvtárba szeretné másolni, használja a következő parancsot:

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. A korábban létrehozott `job.xml` fájl módosításához használja a következő parancsot:

    ```bash
    nano job.xml
    ```

    Hajtsa végre a következő módosításokat:

   * Ha arra utasítja a Oozie, hogy futtassa a koordinátori fájlt a munkafolyamat `<name>oozie.wf.application.path</name>` helyett `<name>oozie.coord.application.path</name>`, váltson a következőre:.

   * A koordinátor által `workflowPath` használt változó beállításához adja hozzá a következő XML-kódot:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Cserélje le `wasbs://mycontainer@mystorageaccount.blob.core.windows` a szöveget a job. xml fájl egyéb bejegyzéseiben használt értékre.

   * A koordinátor indításának, befejezésének és gyakoriságának meghatározásához adja hozzá a következő XML-t:

        ```xml
        <property>
            <name>coordStart</name>
            <value>2018-05-10T12:00Z</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>2018-05-12T12:00Z</value>
        </property>

        <property>
            <name>coordFrequency</name>
            <value>1440</value>
        </property>

        <property>
            <name>coordTimezone</name>
            <value>UTC</value>
        </property>
        ```

       Ezek az értékek a kezdési időpontot 12:00 PM-re, 2018-ra, a befejezési időt pedig 2018-ra állítja. A feladatok futtatásának időköze napi értékre van állítva. A gyakoriság percben, tehát 24 óra x 60 perc = 1440 perc. Végül az időzóna az UTC értékre van állítva.

5. A fájl mentéséhez válassza a CTRL + X billentyűkombinációt, `Y`írja be a következőt:, majd válassza az **ENTER**billentyűt.

6. A művelet elküldéséhez és elindításához használja a következő parancsot:

    ```bash
    oozie job -config job.xml -run
    ```

7. Ha a Oozie webes felhasználói felületére kattint, és kiválasztja a **koordinátori feladatok** lapot, a következő képen látható információk jelennek meg:

    ![Koordinátori feladatok lap](./media/hdinsight-use-oozie-linux-mac/coordinator-jobs-tab.png)

    A **következő megvalósításának gyakorlatban** -bejegyzés a művelet következő futási idejét tartalmazza.

8. A korábbi munkafolyamat-feladathoz hasonlóan, ha a webes felhasználói felületen kiválasztja a feladathoz tartozó bejegyzést, akkor a feladattal kapcsolatos információkat jeleníti meg:

    ![Koordinátori feladatok adatai](./media/hdinsight-use-oozie-linux-mac/coordinator-job-info.png)

    > [!NOTE]  
    > Ez a rendszerkép csak a feladatok sikeres futtatását mutatja, nem az ütemezett munkafolyamaton belüli egyes műveleteket. Az egyes műveletek megtekintéséhez válassza ki az egyik **műveleti** bejegyzést.

    ![Koordinátori művelet adatai](./media/hdinsight-use-oozie-linux-mac/coordinator-action-job.png)

## <a name="troubleshooting"></a>Hibaelhárítás

A Oozie felhasználói felületén megtekintheti a Oozie naplókat. A Oozie felhasználói felülete a munkafolyamat által elindított MapReduce-feladatok JobTracker-naplóira mutató hivatkozásokat is tartalmaz. A hibaelhárítási minta a következő lehet:

   1. Tekintse meg a feladatot a Oozie webes felhasználói felületén.

   2. Ha egy adott művelet hibával vagy hibával rendelkezik, válassza ki a műveletet, és ellenőrizze, hogy a **hibaüzenet** mezője tartalmaz-e további információt a hibáról.

   3. Ha elérhető, a művelet URL-címével megtekintheti a művelethez tartozó további részleteket, például a JobTracker naplókat.

A következő konkrét hibák merülhetnek fel, és a megoldásuk módja.

### <a name="ja009-cannot-initialize-cluster"></a>JA009: Nem lehet inicializálni a fürtöt

**Tünetek**: A feladatok állapota **felfüggesztve**értékre változik. A feladathoz tartozó részletek `RunHiveScript` a **START_MANUAL**állapotot jelenítik meg. A művelet kiválasztásakor a következő hibaüzenet jelenik meg:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Ok**: A **job. XML** fájlban használt Azure Blob Storage-címek nem tartalmazzák a Storage-tárolót vagy a Storage-fiók nevét. A blob Storage-címnek a formátumnak kell lennie `wasbs://containername@storageaccountname.blob.core.windows.net`.

**Feloldási**: Módosítsa a feladatok által használt BLOB Storage-címeket.

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltusergt"></a>JA002: A Oozie nem engedélyezett a &lt;felhasználó megszemélyesítése&gt;

**Tünetek**: A feladatok állapota **felfüggesztve**értékre változik. A feladathoz tartozó részletek `RunHiveScript` a **START_MANUAL**állapotot jelenítik meg. Ha a műveletet választja, a következő hibaüzenet jelenik meg:

    JA002: User: oozie is not allowed to impersonate <USER>

**Ok**: Az aktuális engedélyezési beállítások nem teszik lehetővé, hogy a Oozie megszemélyesítse a megadott felhasználói fiókot.

**Feloldási**: A Oozie megszemélyesítheti a felhasználókat a **felhasználók** csoportban. A használatával megtekintheti azokat a csoportokat, amelyeknek a felhasználói fiók tagja. `groups USERNAME` Ha a felhasználó nem tagja a **felhasználók** csoportnak, használja a következő parancsot a felhasználó a csoportba való felvételéhez:

    sudo adduser USERNAME users

> [!NOTE]  
> Több percet is igénybe vehet, mielőtt a HDInsight észleli, hogy a felhasználó hozzá lett adva a csoporthoz.

### <a name="launcher-error-sqoop"></a>Indító hiba (Sqoop)

**Tünetek**: A feladatok állapotának **leölése**megtörtént. A feladathoz tartozó részletek `RunSqoopExport` az állapotot **hibaként**jelenítik meg. Ha a műveletet választja, a következő hibaüzenet jelenik meg:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Ok**: A Sqoop nem tudja betölteni az adatbázis eléréséhez szükséges adatbázis-illesztőt.

**Feloldási**: Ha a Sqoop-t egy Oozie-feladatokból használja, az adatbázis-illesztőprogramot a többi erőforrással együtt kell szerepeltetni, például a Workflow. xml fájlt, amelyet a rendszer a feladatokhoz használ. Továbbá hivatkozzon arra az archívumra, amely az adatbázis-illesztőprogramot `<sqoop>...</sqoop>` tartalmazza a Workflow. xml fájl szakaszában.

A jelen dokumentumban szereplő feladatokhoz például a következő lépéseket kell használni:

1. Másolja a `mssql-jdbc-7.0.0.jre8.jar` fájlt a **/tutorials/useoozie** könyvtárba:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. Módosítsa a `workflow.xml` t a következő XML-kód hozzáadásához a fenti `</sqoop>`új sorban:

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan határozhat meg egy Oozie-munkafolyamatot, és hogyan futtathat egy Oozie-feladatot. Ha többet szeretne megtudni a HDInsight használatáról, tekintse meg a következő cikkeket:

* [Adatok feltöltése Apache Hadoop feladatok számára a HDInsight-ben][hdinsight-upload-data]
* [Az Apache Sqoop használata Apache Hadoop a HDInsight][hdinsight-use-sqoop]
* [Apache Hive használata a HDInsight Apache Hadoop használatával][hdinsight-use-hive]
* [Az Apache Pig használata a Apache Hadoop on HDInsight][hdinsight-use-pig]
* [Java MapReduce-programok fejlesztése a HDInsight][hdinsight-develop-mapreduce]

[hdinsight-cmdlets-download]: https://go.microsoft.com/fwlink/?LinkID=325563
[azure-data-factory-pig-hive]: ../data-factory/transform-data.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: hdinsight-get-started-emulator.md
[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[sqldatabase-get-started]: sql-database-get-started.md

[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md

[apache-hadoop]: https://hadoop.apache.org/
[apache-oozie-400]: https://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: https://oozie.apache.org/docs/3.3.2/

[powershell-download]: https://azure.microsoft.com/downloads/
[powershell-about-profiles]: https://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/library/ee176961.aspx

[cindygross-hive-tables]: https://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie-linux-mac/oozie-workflow-diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie-linux-mac/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: https://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
