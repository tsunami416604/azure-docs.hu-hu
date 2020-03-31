---
title: Hadoop Oozie-munkafolyamatok használata Linux-alapú Azure HDInsightban
description: Használja a Hadoop Oozie-t linuxos HDInsight-alapú. Ismerje meg, hogyan definiálhat oozie-munkafolyamatot, és hogyan küldhet be oozie-feladatot.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: ece6fdb743035069bc6c666d6e90c76860f63e82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75744905"
---
# <a name="use-apache-oozie-with-apache-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>Az Apache Oozie használata az Apache Hadooppal a munkafolyamatok Linux-alapú Azure HDInsighton való meghatározásához és futtatásához

Ismerje meg, hogyan használhatja az Apache Oozie-t az Apache Hadoop segítségével az Azure HDInsight-on. Az Oozie egy munkafolyamat- és koordinációs rendszer, amely a Hadoop-feladatokat kezeli. Az Oozie integrálva van a Hadoop verembe, és a következő feladatokat támogatja:

* Apache Hadoop MapReduce
* Apache sertés
* Apache Hive
* Apache Sqoop

Az Oozie használatával a rendszerre jellemző feladatokat, például a Java-programokat vagy a rendszerhéj-parancsfájlokat is ütemezheti.

> [!NOTE]  
> A HDInsight-alapú munkafolyamatok definiálásának másik lehetősége az Azure Data Factory használata. Ha többet szeretne megtudni a Data Factory-ról, olvassa el [az Apache Pig és az Apache Hive használata a Data Factory használatával című témakört.][azure-data-factory-pig-hive] Az Oozie vállalati biztonsági csomaggal rendelkező fürtökön való használatához olvassa el [az Apache Oozie futtatása a HDInsight Hadoop fürtökben vállalati biztonsági csomaggal](domain-joined/hdinsight-use-oozie-domain-joined-clusters.md).

## <a name="prerequisites"></a>Előfeltételek

* **Hadoop-fürt a HDInsighton.** Lásd: [Első lépések a HDInsight linuxos alkalmazásával.](hadoop/apache-hadoop-linux-tutorial-get-started.md)

* **Egy SSH-ügyfél**. Lásd: [Csatlakozás a HDInsighthoz (Apache Hadoop) az SSH használatával.](hdinsight-hadoop-linux-use-ssh-unix.md)

* **Egy Azure SQL-adatbázis.**  Lásd: [Azure SQL-adatbázis létrehozása az Azure Portalon.](../sql-database/sql-database-get-started.md)  Ez a cikk **oozietest**nevű adatbázist használ.

* A fürtök [elsődleges tárolóURI-séma.](./hdinsight-hadoop-linux-information.md#URI-and-scheme) Ez az `wasb://` Azure Storage, `abfs://` az Azure Data `adl://` Lake Storage Gen2 vagy az Azure Data Lake Storage Gen1 esetében lesz. Ha a biztonságos átvitel engedélyezve van `wasbs://`az Azure Storage számára, az URI lesz. Lásd még: [biztonságos átvitel](../storage/common/storage-require-secure-transfer.md).

## <a name="example-workflow"></a>Példa munkafolyamatra

A dokumentumban használt munkafolyamat két műveletet tartalmaz. A műveletek olyan feladatok definíciói, mint például a Hive, a Sqoop, a MapReduce vagy más folyamatok futtatása:

![HDInsight oozie munkafolyamat-diagram](./media/hdinsight-use-oozie-linux-mac/oozie-workflow-diagram.png)

1. A Hive-művelet egy HiveQL-parancsfájlt `hivesampletable` futtat, amely a HDInsight részét képező rekordok at kivonata futtatja. Minden adatsor egy adott mobileszközről érkező látogatást ír le. A rekordformátum a következő szövegként jelenik meg:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    A dokumentumban használt Hive-parancsfájl az egyes platformok, például az Android vagy az iPhone összes látogatását számolja, és a számlálókat egy új Hive-táblába tárolja.

    A Hive-ről az [Apache Hive használata a HDInsight segítségével című témakörben talál][hdinsight-use-hive]további információt.

2. A Sqoop művelet exportálja az új Hive-tábla tartalmát az Azure SQL Database-ben létrehozott táblába. A Sqoop ról további információt az [Apache Sqoop használata a HDInsight segítségével című témakörben talál.][hdinsight-use-sqoop]

> [!NOTE]  
> A HDInsight-fürtök támogatott Oozie-verzióit [a HDInsight által biztosított Hadoop-fürtverziók újdonságai című témakörben található.][hdinsight-versions]

## <a name="create-the-working-directory"></a>A munkakönyvtár létrehozása

Az Oozie elvárja, hogy egy feladathoz szükséges összes erőforrást ugyanabban a könyvtárban tárolja. Ez a `wasbs:///tutorials/useoozie`példa a. A könyvtár létrehozásához hajtsa végre az alábbi lépéseket:

1. Az alábbi kódot szerkesztheti a fürt SSH-felhasználónevével való lecseréléshez, `sshuser` és cserélje le `CLUSTERNAME` a fürt nevére.  Ezután adja meg a HDInsight-fürthöz az [SSH használatával](hdinsight-hadoop-linux-use-ssh-unix.md)történő csatlakozáshoz használt kódot.  

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. A könyvtár létrehozásához használja a következő parancsot:

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]  
    > A `-p` paraméter az elérési út összes könyvtárának létrehozását okozza. A `data` könyvtár a parancsfájl által használt `useooziewf.hql` adatok tárolására szolgál.

3. Az alábbi kód szerkesztése az SSH felhasználónevével való helyettesítéshez. `sshuser`  Annak érdekében, hogy az Oozie megszemélyesíthesse a felhasználói fiókját, használja a következő parancsot:

    ```bash
    sudo adduser sshuser users
    ```

    > [!NOTE]  
    > Figyelmen kívül hagyhatja azolyan hibákat, `users` amelyek azt jelzik, hogy a felhasználó már tagja a csoportnak.

## <a name="add-a-database-driver"></a>Adatbázis-illesztőprogram hozzáadása

Mivel ez a munkafolyamat a Sqoop segítségével exportálja az adatokat az SQL-adatbázisba, meg kell adnia az SQL-adatbázissal való interakcióhoz használt JDBC-illesztőprogram másolatát. A JDBC-illesztőprogram munkakönyvtárba másolásához használja az SSH-munkamenet következő parancsát:

```bash
hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /tutorials/useoozie/
```

> [!IMPORTANT]  
> Ellenőrizze a tényleges JDBC-illesztőprogramot, amely a helyen `/usr/share/java/`található.

Ha a munkafolyamat más erőforrásokat is használt, például egy MapReduce alkalmazást tartalmazó jart, ezeket az erőforrásokat is hozzá kell adnia.

## <a name="define-the-hive-query"></a>A Hive-lekérdezés definiálása

A következő lépésekkel hozzon létre egy Hive lekérdezési nyelv (HiveQL) parancsfájlt, amely meghatározza a lekérdezést. A lekérdezést a dokumentum későbbi részében egy Oozie-munkafolyamatban fogja használni.

1. Az SSH-kapcsolatból a következő paranccsal `useooziewf.hql`hozzon létre egy nevű fájlt:

    ```bash
    nano useooziewf.hql
    ```

1. A GNU nanoszerkesztő megnyitása után használja a következő lekérdezést a fájl tartalmaként:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    A parancsfájlban két változó van:

   * `${hiveTableName}`: A létrehozandó tábla nevét tartalmazza.

   * `${hiveDataFolder}`: A tábla adatfájljainak tárolására szolgáló helyet tartalmazza.

     A munkafolyamat-definíciós fájl, a workflow.xml ebben a cikkben, ezeket az értékeket átadja ennek a HiveQL parancsfájlnak futásidőben.

1. A fájl mentéséhez válassza a **Ctrl+X**, **Y**, majd **az Enter**lehetőséget.  

1. Másolja a következő `useooziewf.hql` `wasbs:///tutorials/useoozie/useooziewf.hql`parancsot:

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    Ez a `useooziewf.hql` parancs a fájlt a fürt HDFS-kompatibilis tárolójában tárolja.

## <a name="define-the-workflow"></a>A munkafolyamat meghatározása

Az Oozie munkafolyamat-definíciók a Hadoop folyamatdefiníciós nyelv (hPDL) nyelven íródnak, amely egy XML-folyamatdefiníciós nyelv. A munkafolyamat definiálásához kövesse a következő lépéseket:

1. Új fájl létrehozásához és szerkesztéséhez használja a következő utasítást:

    ```bash
    nano workflow.xml
    ```

2. A nanoszerkesztő megnyitása után adja meg a következő XML-t a fájl tartalmaként:

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

   * `RunHiveScript`: Ez a művelet a `useooziewf.hql` kezdő művelet, és futtatja a Hive-parancsfájlt.

   * `RunSqoopExport`: Ez a művelet exportálja a Hive-parancsfájlból létrehozott adatokat egy SQL-adatbázisba a Sqoop használatával. Ez a művelet `RunHiveScript` csak akkor fut, ha a művelet sikeres.

     A munkafolyamat több bejegyzésből `${jobTracker}`áll, például a . Ezeket a bejegyzéseket a feladatdefinícióban használt értékekre cseréli. A feladatdefiníciót a dokumentum későbbi részében hozza létre.

     Is vegye `<archive>mssql-jdbc-7.0.0.jre8.jar</archive>` figyelembe a bejegyzést a Sqoop részben. Ez a bejegyzés arra utasítja az Oozie-t, hogy ezt az archívumot elérhetővé tegye a Sqoop számára, amikor ez a művelet fut.

3. A fájl mentéséhez válassza a **Ctrl+X**, **Y**, majd **az Enter**lehetőséget.  

4. A `workflow.xml` fájl másolásához használja `/tutorials/useoozie/workflow.xml`a következő parancsot:

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-a-table"></a>Tábla létrehozása

> [!NOTE]  
> A tábla létrehozásához számos módon lehet csatlakozni az SQL Database-hez. A következő lépések során a [FreeTDS](https://www.freetds.org/) eszközt használjuk a HDInsight-fürtről.

1. A FreeTDS hdinsight-fürtre való telepítéséhez használja a következő parancsot:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Az alábbi kódot az `<serverName>` Azure SQL-kiszolgáló nevének és `<sqlLogin>` az Azure SQL-kiszolgáló bejelentkezésének lecseréléséhez szerkesztheti.  Adja meg a parancsot, hogy csatlakozzon az előfeltételsql-adatbázishoz.  Írja be a jelszót a parancssorba.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    A következő szöveghez hasonlóan kap kimenetet:

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

    A `GO` utasítás megadásakor a rendszer kiértékeli az előző utasításokat. Ezek az utasítások létrehoznak `mobiledata`egy táblát, amelyet a munkafolyamat használ.

    A tábla létrehozásának ellenőrzéséhez használja a következő parancsokat:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    A kimenet a következő szöveghez hasonlóan jelenik meg:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo             mobiledata      BASE TABLE

4. Lépjen ki a tsql `1>` segédprogramból a parancssorba való beírással. `exit`

## <a name="create-the-job-definition"></a>A feladatdefiníció létrehozása

A feladatdefiníció leírja, hogy hol található a workflow.xml. Azt is leírja, hogy hol találhatók a munkafolyamat `useooziewf.hql`által használt egyéb fájlok, például a . Emellett meghatározza a munkafolyamatban és a kapcsolódó fájlokban használt tulajdonságok értékeit.

1. Az alapértelmezett tároló teljes címének leéséhez használja a következő parancsot. Ez a cím a következő lépésben létrehozott konfigurációs fájlban használatos.

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    Ez a parancs a következő XML-hez hasonló információkat ad vissza:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]  
    > Ha a HDInsight-fürt az Azure Storage-t használja alapértelmezett tárolóként, az `<value>` elem tartalma a . `wasbs://` Ha az Azure Data Lake Storage Gen1-et használja helyette, az a. `adl://` Ha Az Azure Data Lake Storage Gen2 használatával `abfs://`kezdődik.

    Mentse az elem `<value>` tartalmát, ahogy azt a következő lépésekben használják.

2. Az alábbi xml szerkesztése a következőképpen:

    |Helyőrző értéke| Felülírt érték|
    |---|---|
    |wasbs://mycontainer\@mystorageaccount.blob.core.windows.net| Az 1.|
    |admin| A HDInsight-fürt bejelentkezési neve, ha nem rendszergazda.|
    |Kiszolgálónév| Az Azure SQL adatbázis-kiszolgáló neve.|
    |sqlLogin| Az Azure SQL adatbázis-kiszolgáló bejelentkezése.|
    |sqlPassword| Azure SQL adatbázis-kiszolgáló bejelentkezési jelszava.|

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

    A fájlban található információk többsége a workflow.xml vagy ooziewf.hql fájlokban használt értékek, például `${nameNode}`a .  Ha az elérési `wasbs` út elérési út, akkor a teljes elérési utat kell használnia. Ne rövidítsd `wasbs:///`le, hogy csak . A `oozie.wf.application.path` bejegyzés határozza meg, hogy hol található a workflow.xml fájl. Ez a fájl a feladat által futtatott munkafolyamatot tartalmazza.

3. Az Oozie feladatdefiníciós konfiguráció létrehozásához használja a következő parancsot:

    ```bash
    nano job.xml
    ```

4. A nanoszerkesztő megnyitása után illessze be a szerkesztett XML-t a fájl tartalmaként.

5. A fájl mentéséhez válassza a **Ctrl+X**, **Y**, majd **az Enter**lehetőséget.

## <a name="submit-and-manage-the-job"></a>A feladat beküldése és kezelése

A következő lépések az Oozie paranccsal oozie-munkafolyamatokat küldhetnek el és kezelnek a fürtön. Az Oozie parancs egy barátságos felület az [Oozie REST API-n](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html)keresztül.

> [!IMPORTANT]  
> Az Oozie parancs használatakor a TELJES tartományntartományt kell használnia a HDInsight-főcsomóponthoz. Ez a teljes tartományna csak a fürtből érhető el, vagy ha a fürt egy Azure virtuális hálózaton, más gépek ugyanazon a hálózaton.

1. Az Oozie szolgáltatás URL-címének beszerzéséhez használja a következő parancsot:

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Ez a következő XML-hez hasonló információkat ad vissza:

    ```xml
    <name>oozie.base.url</name>
    <value>http://ACTIVE-HEADNODE-NAME.UNIQUEID.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    A `http://ACTIVE-HEADNODE-NAME.UNIQUEID.cx.internal.cloudapp.net:11000/oozie` rész az Oozie paranccsal használandó URL-cím.

2. A kód szerkesztésével cserélje le az URL-t a korábban kapott url-re. Az URL-cím környezeti változójának létrehozásához használja az alábbiakat, így nem kell minden parancshoz megadnia:

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

3. A feladat elküldéséhez használja a következőket:

    ```bash
    oozie job -config job.xml -submit
    ```

    Ez a parancs betölti a feladat adatait, `job.xml` és elküldi az Oozie-nak, de nem futtatja azt.

    A parancs befejezése után vissza kell adnia a feladat `0000005-150622124850154-oozie-oozi-W`azonosítóját, például . Ez az azonosító a feladat kezelésére szolgál.

4. Az alábbi kódot az `<JOBID>` előző lépésben visszaadott azonosítóval helyettesítheti.  A feladat állapotának megtekintéséhez használja a következő parancsot:

    ```bash
    oozie job -info <JOBID>
    ```

    Ez a következő hez hasonló információkat adja vissza:

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

    A feladat állapota `PREP`. Ez az állapot azt jelzi, hogy a feladat létrejött, de nem indult el.

5. Az alábbi kód szerkesztése a korábban visszaadott azonosítóval való helyettesítéshez. `<JOBID>`  A feladat elindításához használja a következő parancsot:

    ```bash
    oozie job -start <JOBID>
    ```

    Ha a parancs után ellenőrzi az állapotot, az futó állapotban van, és a feladaton belüli műveletekhez ad vissza adatokat.  A feladat néhány percet vesz igénybe.

6. Az alábbi kódot az `<serverName>` Azure SQL-kiszolgáló nevének és `<sqlLogin>` az Azure SQL-kiszolgáló bejelentkezésének lecseréléséhez szerkesztheti.  *A feladat* sikeres befejezése után a következő paranccsal ellenőrizheti, hogy az adatok at létrehozták és exportálták-e az SQL adatbázistáblába.  Írja be a jelszót a parancssorba.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    A `1>` kérdésben írja be a következő lekérdezést:

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    A visszaadott információ a következő szöveghez hasonló:

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Az Oozie parancsról az [Apache Oozie parancssori eszközcímű témakörben](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html)talál további információt.

## <a name="oozie-rest-api"></a>Oozie REST API

Az Oozie REST API-val létrehozhatja saját eszközeit, amelyek együttműködnek az Oozie-val. Az alábbiakban hdinsight-specifikus információkat az Oozie REST API használatával kapcsolatban:

* **URI**: A REST API-t a `https://CLUSTERNAME.azurehdinsight.net/oozie`fürtön kívülről érheti el a.

* **Hitelesítés**: A hitelesítéshez használja az API-t a fürt HTTP-fiókjával (rendszergazda) és jelszóval. Példa:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Az Oozie REST API használatával kapcsolatos további információkért lásd: [Apache Oozie Web Services API.](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html)

## <a name="oozie-web-ui"></a>Oozie webes felhasználói felület

Az Oozie webes felhasználói felület webalapú nézetet biztosít a fürt oozie-feladatainak állapotáról. A webes felhasználói felületen a következő információkat tekintheti meg:

   * Feladat állapota
   * Feladat definíciója
   * Konfiguráció
   * A feladatban végrehajtott műveletek grafikonja
   * Naplók a feladathoz

Megtekintheti a feladaton belüli műveletek részleteit is.

Az Oozie webes felhasználói felületének eléréséhez hajtsa végre az alábbi lépéseket:

1. Hozzon létre egy SSH-alagutat a HDInsight-fürthöz. További információ: [SSH tunneling használata a HDInsight segítségével.](hdinsight-linux-ambari-ssh-tunnel.md)

2. Miután létrehozott egy alagutat, nyissa meg az Ambari webes felhasználói felületét a webböngészőben az URI `http://headnodehost:8080`használatával.

3. A lap bal oldalán válassza az **Oozie** > **Quick Links** > **Oozie Web Felhasználói felületlehetőséget.**

    ![Apache Ambari oozie web ui lépések](./media/hdinsight-use-oozie-linux-mac/hdi-oozie-web-ui-steps.png)

4. Az Oozie webes felhasználói felület alapértelmezés szerint a futó munkafolyamat-feladatok jelennek meg. Az összes munkafolyamat-feladat megtekintéséhez válassza a **Minden feladat lehetőséget.**

    ![Oozie webkonzol munkafolyamat-feladatok](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-jobs.png)

5. Ha további információkat szeretne megtekinteni egy feladatról, jelölje ki a feladatot.

    ![HDInsight Apache Oozie feladatinformációk](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-info.png)

6. A **Feladat adatai** lapon megtekintheti az alapvető feladatinformációkat és a feladaton belüli egyes műveleteket. A lap tetején lévő lapok segítségével megtekintheti a **Feladatdefiníciót,** a **Feladatkonfigurációt,** a **feladatnaplót,** vagy megtekintheti a feladat irányított aciklikus grafikonját a **Feladat dag**csoportban.

   * **Feladatnapló:** A **naplók beszerezése** gombra kattintva leszeretné a feladat összes naplóját, vagy használja a **Keresési szűrő bevitele** mezőt a naplók szűréséhez.

       ![HDInsight Apache Oozie feladatnapló](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-log.png)

   * **Job DAG**: A DAG egy grafikus áttekintést az adatutak a munkafolyamaton keresztül.

       ![HDInsight Apache Oozie feladat dag](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-dag.png)

7. Ha a **Feladat adatai** lapon kiválaszt egy műveletet, az információkat jelenít meg a művelethez. Jelölje be például a **RunSqoopExport** műveletet.

    ![HDInsight oozie feladatművelet-információ](./media/hdinsight-use-oozie-linux-mac/oozie-job-action-info.png)

8. Megtekintheti a művelet részleteit, például a **konzol URL-címére**mutató hivatkozást. Ezen a hivatkozáson keresztül megtekintheti a feladat feladatkövető adatait.

## <a name="schedule-jobs"></a>Feladatok ütemezése

A koordinátor segítségével megadhatja a feladatok kezdési, befejezési és előfordulási gyakoriságát. A munkafolyamat ütemezésének meghatározásához hajtsa végre a következő lépéseket:

1. A **coordinator.xml**nevű fájl létrehozásához használja a következő parancsot:

    ```bash
    nano coordinator.xml
    ```

    A fájl tartalmaként a következő XML-t használja:

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
    > A `${...}` változók at a feladatdefiníció ban futóidőben lévő értékek lépnek fel. A változók a következők:
    >
    > * `${coordFrequency}`: A feladat példányainak futtatása között töltött idő.
    > * `${coordStart}`: A feladat kezdési időpontja.
    > * `${coordEnd}`: A feladat befejezési ideje.
    > * `${coordTimezone}`: A koordinátori feladatok rögzített időzónában vannak, és nincs nyári időszámítás, amelyet általában utc használatával jelölnek. Ezt az időzónát *Oozie feldolgozási időzónának nevezzük.*
    > * `${wfPath}`: A workflow.xml elérési útja.

2. A fájl mentéséhez válassza a **Ctrl+X**, **Y**, majd **az Enter**lehetőséget.

3. Ha a fájlt a feladat munkakönyvtárába szeretné másolni, használja a következő parancsot:

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. A korábban létrehozott `job.xml` fájl módosításához használja a következő parancsot:

    ```bash
    nano job.xml
    ```

    Hajtsa végre a következő módosításokat:

   * Ha azt szeretné, hogy az Oozie a `<name>oozie.wf.application.path</name>` `<name>oozie.coord.application.path</name>`munkafolyamat helyett a koordinátorfájlt futtassa, módosítsa a programra.

   * A koordinátor `workflowPath` által használt változó beállításához adja hozzá a következő XML-t:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Cserélje `wasbs://mycontainer@mystorageaccount.blob.core.windows` le a szöveget a job.xml fájl többi bejegyzésében használt értékre.

   * A koordinátor kezdő, záró és gyakorisági meghatározásához adja hozzá a következő XML-t:

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

       Ezek az értékek 2018. A feladat futtatásának időköze napi értékre van állítva. A frekvencia percben, így 24 óra x 60 perc = 1440 perc. Végül az időzóna UTC-re van állítva.

5. A fájl mentéséhez válassza a **Ctrl+X**, **Y**, majd **az Enter**lehetőséget.

6. A feladat elküldéséhez és elindításához használja a következő parancsot:

    ```bash
    oozie job -config job.xml -run
    ```

7. Ha az Oozie webes felhasználói felületére lép, és a Koordinátori feladatok lapot **választja,** az alábbi képen az alábbihoz hasonló információk jelennek meg:

    ![Oozie webkonzol koordinátor feladatok lap](./media/hdinsight-use-oozie-linux-mac/coordinator-jobs-tab.png)

    A **Következő anyagmentesítés** bejegyzés a feladat következő futtatásakor található.

8. A korábbi munkafolyamat-feladathoz hasonlóan, ha a webes felhasználói felületen kiválasztja a feladatbejegyzést, a feladatról is információkat jelenít meg:

    ![Apache Oozie koordinátor feladat adatai](./media/hdinsight-use-oozie-linux-mac/coordinator-job-info.png)

    > [!NOTE]  
    > Ez a kép csak a feladat sikeres futtatásait mutatja, az ütemezett munkafolyamaton belüli egyes műveleteket nem. Az egyes műveletek megtekintéséhez jelöljön ki egy **Művelet** bejegyzést.

    ![OOzie webkonzol feladat info lap](./media/hdinsight-use-oozie-linux-mac/coordinator-action-job.png)

## <a name="troubleshooting"></a>Hibaelhárítás

Az Oozie felhasználói felületen megtekintheti az Oozie naplókat. Az Oozie felhasználói felület a munkafolyamat által elindított MapReduce feladatok JobTracker naplóira mutató hivatkozásokat is tartalmaz. A hibaelhárítás mintájának a következőnek kell lennie:

   1. Tekintse meg a feladatot az Oozie webes felhasználói felületén.

   2. Ha egy adott műveletnél hiba vagy hiba történt, jelölje ki a műveletet, és nézze meg, hogy a **Hibaüzenet** mező további információt tartalmaz-e a hibáról.

   3. Ha elérhető, használja a művelet URL-címét a művelet további részleteinek megtekintéséhez, például a JobTracker naplók.

Az alábbiakban olyan konkrét hibákat, amelyek et találkozhat, és hogyan lehet megoldani őket.

### <a name="ja009-cannot-initialize-cluster"></a>JA009: A fürt nem inicializálható

**Jelenségek**: A munka állapota FELFÜGGESZTETT állapotra **változik.** A feladat részletei `RunHiveScript` a **START_MANUAL**állapotot mutatják. A művelet kijelölése a következő hibaüzenetet jeleníti meg:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Ok:** A **job.xml** fájlban használt Azure Blob storage-címek nem tartalmazzák a tárolót vagy a tárfiók nevét. A Blob storage címformátumának kell lennie. `wasbs://containername@storageaccountname.blob.core.windows.net`

**Megoldás**: Módosítsa a feladat által használt Blob-tárolási címeket.

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltusergt"></a>JA002: Oozie nem szabad &lt;megszemélyesíteni felhasználó&gt;

**Jelenségek**: A munka állapota FELFÜGGESZTETT állapotra **változik.** A feladat részletei `RunHiveScript` a **START_MANUAL**állapotot mutatják. Ha kiválasztja a műveletet, a következő hibaüzenet jelenik meg:

    JA002: User: oozie is not allowed to impersonate <USER>

**Ok:** Az aktuális engedélybeállítások nem teszik lehetővé, hogy az Oozie megszemélyesítse a megadott felhasználói fiókot.

**Megoldás**: Az Oozie megszemélyesítheti a felhasználók at a **felhasználók** csoportjában. Az `groups USERNAME` segítségével megtekintheti azokkal a csoportokkal, amelyeknek a felhasználói fiók tagja. Ha a felhasználó nem tagja a **felhasználói** csoportnak, a következő paranccsal adja hozzá a felhasználót a csoporthoz:

    sudo adduser USERNAME users

> [!NOTE]  
> Több percig is eltarthat, amíg a HDInsight felismeri, hogy a felhasználó hozzá lett adva a csoporthoz.

### <a name="launcher-error-sqoop"></a>Launcher hiba (Sqoop)

**Tünetek**: A munka állapota **killed-re**változik. A feladat részletei `RunSqoopExport` **hibaállapotként**jelennek meg. Ha kiválasztja a műveletet, a következő hibaüzenet jelenik meg:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Ok**: A Sqoop nem tudja betölteni az adatbázis-illesztőprogram eléréséhez szükséges adatbázis-illesztőprogramot.

**Megoldás**: Ha egy Oozie-feladatból származó Sqoop-ot használ, az adatbázis-illesztőprogramot a többi erőforrással együtt kell használnia, például a workflow.xml fájlt, amelyet a feladat használ. Hivatkozzon arra az archívumra is, `<sqoop>...</sqoop>` amely a workflow.xml szakaszadatbázis-illesztőprogramot tartalmazza.

A dokumentumban szereplő feladathoz például a következő lépéseket kell alkalmazni:

1. Másolja `mssql-jdbc-7.0.0.jre8.jar` a fájlt a **/tutorials/useoozie** könyvtárba:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. Módosítsa `workflow.xml` a fájlt, hogy a következő `</sqoop>`XML-t adja hozzá a fenti új sorhoz:

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan definiálhatja az Oozie-munkafolyamatokat, és hogyan futtatható oozie-feladat. A HDInsight működéséről az alábbi cikkekben olvashat bővebben:

* [Adatok feltöltése apache hadoop feladatok HDInsight][hdinsight-upload-data]
* [Az Apache Sqoop használata az Apache Hadoop segítségével a HDInsightban][hdinsight-use-sqoop]
* [Az Apache Hive használata az Apache Hadoop segítségével a HDInsighton][hdinsight-use-hive]
* [Java MapReduce programok fejlesztése a HDInsight hoz](hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)

[azure-data-factory-pig-hive]: ../data-factory/transform-data.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
