---
title: Linux-alapú Azure hdinsight Hadoop Oozie munkafolyamatok használata |} Microsoft Docs
description: Linux-alapú HDInsight Hadoop Oozie használja. Megtudhatja, hogyan határozza meg az Oozie-munkafolyamat és az Oozie feladat elküldéséhez.
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d7603471-5076-43d1-8b9a-dbc4e366ce5d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: omidm
ms.openlocfilehash: a1fd33ec83208dfd5d90a0fb11557c72a5f02e88
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37019279"
---
# <a name="use-oozie-with-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>A Hadoop Oozie segítségével határozza meg, és a munkafolyamat futtatása a Linux-alapú Azure HDInsight

[!INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Ismerje meg, hogyan használható az Azure HDInsight Hadoop Apache Oozie. Oozie egy munkafolyamat és koordinációs rendszer, amely a Hadoop-feladatokat kezeli. Oozie integrálva van a Hadoop-veremmel, és támogatja a következő feladatokat:

* Apache MapReduce
* Apache Pig
* Apache Hive
* Apache Sqoop

Oozie feladatok, amelyek rendszerspecifikus, például Java programok vagy héjparancsfájlok ütemezésére ütemezését is használhatja.

> [!NOTE]
> A munkafolyamatok és a HDInsight együttes megadása egy másik lehetőség, hogy Azure Data Factory használja. Adat-előállító kapcsolatos további információkért lásd: [használja a Pig és a Data Factory Hive][azure-data-factory-pig-hive].


## <a name="prerequisites"></a>Előfeltételek

* **HDInsight-fürtök**: lásd: [HDInsight Linux első lépések](/hadoop/apache-hadoop-linux-tutorial-get-started.md)

> [!IMPORTANT]
> A dokumentum lépéseinek elvégzéséhez egy Linux-alapú HDInsight-fürt szükséges. Linux az egyetlen operációs rendszer használt a HDInsight 3.4 vagy újabb verziója. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="example-workflow"></a>Példa-munkafolyamat

A munkafolyamat a dokumentumban használt két műveleteket tartalmaz. Műveletek végezhetők, feladatok, például a Hive, a Sqoop, a MapReduce vagy a más folyamatok definícióit:

![Munkafolyamat diagramja][img-workflow-diagram]

1. A Hive művelet a futás bejegyzéseit kibontásához egy HiveQL-parancsfájlt a **hivesampletable** , amely a hdinsight eszközzel tartalmazott. Minden egyes soraiban levő adatok egy adott mobileszközről meglátogatása ismerteti. A bejegyzés formátuma a következő szöveg jelenik meg:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    Ebben a dokumentumban használt Hive-parancsfájl megjeleníti az egyes platformokon, például iPhone, vagy az Android a teljes látogatások, és a számát, az új Hive tábla tárolja.

    További információ a Hive-ról: [A Hive használata a HDInsightban][hdinsight-use-hive].

2. A Sqoop művelet egy táblát az Azure SQL-adatbázis létrehozása a tartalmát az új Hive tábla exportálása. További információ a Sqoop: [Hadoop Sqoop használata a hdinsightban][hdinsight-use-sqoop].

> [!NOTE]
> Tekintse meg a HDInsight-fürtökön támogatott Oozie verziók [What's new in HDInsight által biztosított Hadoop-fürt verziók][hdinsight-versions].

## <a name="create-the-working-directory"></a>A munkakönyvtár létrehozása

Oozie vár, hogy a szükséges összes erőforrást, egy feladat ugyanabban a könyvtárban tárolja. Ez a példa **wasb: / / / oktatóanyagok/useoozie**. A címtár létrehozásához kövesse az alábbi lépéseket:

1. Csatlakozás a HDInsight-fürthöz SSH segítségével:

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Cserélje le az `sshuser` elemet a fürt SSH-felhasználónevére. Cserélje le `clustername` a fürt nevét. További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

2. A következő könyvtár létrehozásakor, a következő paranccsal:

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]
    > A `-p` paraméter hatására az összes könyvtár létrehozása az elérési út. A **adatok** által használt adatok tárolására használt könyvtárat a **useooziewf.hql** parancsfájl.

3. Győződjön meg arról, hogy Oozie megszemélyesíthet-e a felhasználói fiók, használja a következő parancsot:

    ```bash
    sudo adduser username users
    ```

    Cserélje le `username` az SSH-felhasználónév.

    > [!NOTE]
    > Hibák, amelyek jelzik, hogy a felhasználó már tagja figyelmen kívül hagyhatja a `users` csoport.

## <a name="add-a-database-driver"></a>Egy adatbázis-illesztőprogram hozzáadása

Mivel ez a munkafolyamat Sqoop segítségével exportál adatokat az SQL-adatbázis, meg kell adnia egy példányát az SQL-adatbázis együttműködhet használt JDBC-illesztőt. Másolja a JDBC-illesztőt a munkakönyvtárat, használja az SSH-munkamenetet a következő parancsot:

```bash
hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /tutorials/useoozie/
```

> [!NOTE]
> Jelenhet meg, hogy a fájl már létezik egy üzenetet.

Ha a munkafolyamat más erőforrások, például a MapReduce-alkalmazást tartalmazó jar használja, adja hozzá ezeket az erőforrásokat, valamint szeretné.

## <a name="define-the-hive-query"></a>A Hive-lekérdezés megadása

A következő lépésekkel Hive query language (HiveQL) parancsfájl, amely meghatározza a lekérdezés létrehozásához. A lekérdezés egy Oozie munkafolyamatban a dokumentum későbbi szakaszában fogja használni.

1. Az SSH-kapcsolat az alábbi parancs segítségével hozzon létre egy fájlt `useooziewf.hql`:

    ```bash
    nano useooziewf.hql
    ```

3. Megnyílik a GNU nano szerkesztő, miután a következő lekérdezés használata a fájl tartalmát:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    Nincsenek a parancsfájl használt két változót:

    * `${hiveTableName}`: A létrehozandó tábla nevét tartalmazza.

    * `${hiveDataFolder}`: A tábla az adatforrás adatfájljainak tárolási helyét tartalmazza.

    A munkafolyamat-definíciós fájlban workflow.xml ebben az oktatóanyagban továbbítja ezeket az értékeket a futási időben HiveQL-parancsfájlt.

4. Zárja be a szerkesztőt, válassza ki a Ctrl + X. Amikor a rendszer kéri, válassza ki a `Y` mentse a fájlt, írja be a következőt `useooziewf.hql` a fájl nevét, és válassza ki, **Enter**.

5. Másolja az alábbi parancsokkal `useooziewf.hql` való `wasb:///tutorials/useoozie/useooziewf.hql`:

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    Ezzel a paranccsal tárolhatók a `useooziewf.hql` a fürt a HDFS-kompatibilis tároló fájlt.

## <a name="define-the-workflow"></a>Adja meg a munkafolyamat

Oozie munkafolyamat-definícióhoz Hadoop folyamat adatdefiníciós nyelv (hPDL), amely egy XML-folyamat adatdefiníciós nyelv nyelven íródtak. Az alábbi lépések segítségével határozza meg a munkafolyamat:

1. A következő utasítás használatával hozzon létre és szerkeszthet egy új fájlt:

    ```bash
    nano workflow.xml
    ```

2. Után megnyílik a nano szerkesztő, adja meg a következő XML fájl tartalma:

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

    A munkafolyamatban meghatározott két tevékenységek vannak:

   * `RunHiveScript`: Ez a művelet az indítási műveletet, és futtatja a `useooziewf.hql` Hive parancsprogram.

   * `RunSqoopExport`: Ez a művelet exportálja az adatokat a Hive-parancsfájl alapján létrehozott SQL adatbázishoz Sqoop használatával. Ez a művelet csak akkor fut, ha a `RunHiveScript` művelet befejeződött.

     A munkafolyamat rendelkezik több bejegyzést, például a `${jobTracker}`. Ezek a bejegyzések feladatdefinícióban használt értékek lecseréli. A dokumentum későbbi szakaszában hoz létre a feladat definíciójához.

     Azt is vegye figyelembe a `<archive>sqljdbc4.jar</archive>` a Sqoop szakasz bejegyzése. Ez a bejegyzés arra utasítja az Oozie elérhetővé ebből az archívumból Sqoop számára ez a művelet futtatásakor.

3. Mentse a fájlt, jelölje ki a Ctrl + X, adja meg `Y`, majd válassza ki **Enter**. 

4. Másolja az alábbi parancs segítségével a `workflow.xml` fájl `/tutorials/useoozie/workflow.xml`:

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-the-database"></a>Az adatbázis létrehozása

SQL-adatbázis létrehozásához kövesse a [SQL-adatbázis létrehozása](../sql-database/sql-database-get-started.md) dokumentum. Az adatbázis létrehozásakor `oozietest` az adatbázis neve. Is jegyezze fel az adatbázis-kiszolgáló nevét.

### <a name="create-the-table"></a>A tábla létrehozása

> [!NOTE]
> Számos módon hozzon létre egy táblát az SQL-adatbázishoz való kapcsolódáshoz. A következő lépések során a [FreeTDS](http://www.freetds.org/) eszközt használjuk a HDInsight-fürtről.


1. A következő paranccsal FreeTDS a HDInsight-fürt telepítése:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Miután telepítve van a FreeTDS, használja a következő parancsot a korábban létrehozott SQL adatbázis-kiszolgálóhoz való csatlakozáshoz:

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -P <sqlPassword> -p 1433 -D oozietest
    ```

    A következő szöveg hasonló kimenetnek jelenhet meg:

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

    A `GO` utasítás megadásakor a rendszer kiértékeli az előző utasításokat. Ezekre az utasításokra, hozzon létre egy táblát, nevű **mobiledata**, a munkafolyamat által használt.

    Győződjön meg arról, hogy a táblázat létrejött, a következő parancsokat használhatja:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    A következő szöveg kimenet jelenik meg:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo     mobiledata      BASE TABLE

4. Lépjen ki a tsql segédprogram, írja be a következőt `exit` , a `1>` kérdés.

## <a name="create-the-job-definition"></a>A feladat definíciójának létrehozása

A feladat definíciójához hol található a workflow.xml ismerteti. Azt is ismerteti, hol található egyéb fájlok, például a munkafolyamat által használt `useooziewf.hql`. Ezenkívül azt határozza meg a munkafolyamat és a társított fájlok belül használt tulajdonságok értékeit.

1. Ahhoz, hogy a teljes címe az alapértelmezett tároló, a következő paranccsal. Ez a cím szerepel a konfigurációs fájlt hoz létre a következő lépésben.

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    Ez a parancs visszaadja az információkat, például a következő XML-kód:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]
    > Ha a HDInsight-fürt Azure a tárolót használja az alapértelmezett tárolóként a `<value>` elem tartalma kezdődnie `wasb://`. Ha az Azure Data Lake Store használata esetén kezdődik `adl://`.

    Menti a `<value>` elem, mert használatban van a következő lépéseket.

2. Az Oozie feladat definition konfiguráció létrehozásához a következő paranccsal:

    ```bash
    nano job.xml
    ```

3. Után megnyílik a nano szerkesztő, a fájl tartalmát a következő XML-kód használata:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
        <name>nameNode</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net</value>
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
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
        </property>

        <property>
        <name>hiveTableName</name>
        <value>mobilecount</value>
        </property>

        <property>
        <name>hiveDataFolder</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
        </property>

        <property>
        <name>sqlDatabaseConnectionString</name>
        <value>"jdbc:sqlserver://serverName.database.windows.net;user=adminLogin;password=adminPassword;database=oozietest"</value>
        </property>

        <property>
        <name>sqlDatabaseTableName</name>
        <value>mobiledata</value>
        </property>

        <property>
        <name>user.name</name>
        <value>YourName</value>
        </property>

        <property>
        <name>oozie.wf.application.path</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
    </configuration>
    ```

   * Cserélje le az összes példányát `wasb://mycontainer@mystorageaccount.blob.core.windows.net` a alapértelmezett tárolási korábban fogadott értékkel.

     > [!WARNING]
     > Ha az elérési út egy `wasb` elérési útja, a teljes elérési útja kell használnia. Nem rövidíthető úgy, hogy csak `wasb:///`.

   * Cserélje le `YourName` a bejelentkezési névvel a HDInsight-fürthöz.
   * Cserélje le `serverName`, `adminLogin`, és `adminPassword` az SQL-adatbázis adataival.

     A legtöbb a fájlban található információ a workflow.xml vagy ooziewf.hql fájlok, például a használt értékek feltöltésére használt `${nameNode}`.

     > [!NOTE]
     > A `oozie.wf.application.path` a bejegyzés határozza meg, hol található a workflow.xml fájlt. Ez a fájl tartalmazza a munkafolyamatot, amely szerint a feladat futott.

5. Mentse a fájlt, jelölje ki a Ctrl + X, adja meg `Y`, majd válassza ki **Enter**.

## <a name="submit-and-manage-the-job"></a>Küldje el, majd a feladatok kezelése

Az alábbi lépéseket az Oozie-parancs segítségével küldje el, majd a fürt Oozie munkafolyamatok kezelése. A Oozie parancs nem egy egyszerű felületen keresztül a [Oozie REST API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [!IMPORTANT]
> Az Oozie parancs használatakor a teljes Tartománynevét az HDInsight átjárócsomópont kell használnia. Ez a teljes tartománynév csak az elérhető a fürtből, vagy ha a fürt egy Azure virtuális hálózatra, ugyanazon a hálózaton lévő más gépektől.


1. Ha szüksége van az Oozie-szolgáltatás URL-CÍMÉT, a következő paranccsal:

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Ez visszaadja az információkat, például a következő XML-kód:

    ```xml
    <name>oozie.base.url</name>
    <value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    A `http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie` részére az URL-CÍMÉT az Oozie paranccsal.

2. Hozzon létre egy környezeti változó az URL-címhez, használja a következő, így nem kell minden parancs beírásával:

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

    Cserélje le a korábban kapott egy URL-CÍMÉT.
3. A feladat elküldéséhez az alábbi parancsokat használja:

    ```bash
    oozie job -config job.xml -submit
    ```

    Ez a parancs betölti a feladat adatait `job.xml` , és elküldi Oozie, de nem futtatja a programot.

    A parancs befejezése után az visszaküldje-e a feladat Azonosítóját például `0000005-150622124850154-oozie-oozi-W`. Ezt az Azonosítót a feladat kezelésére szolgál.

4. A feladat állapotának megtekintéséhez használja a következő parancsot:

    ```bash
    oozie job -info <JOBID>
    ```

    > [!NOTE]
    > Cserélje le `<JOBID>` az előző lépés eredményeképpen visszakapott azonosítóval.

    Ez visszaadja az információkat, például a következő szöveget:

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

    Ez a feladat állapota `PREP`. Ez az állapot azt jelzi, hogy a feladat lett létrehozva, de nem lett elindítva.

5. Indítsa el a feladatot, használja a következő parancsot:

    ```bash
    oozie job -start JOBID
    ```

    > [!NOTE]
    > Cserélje le `<JOBID>` korábban visszaadott azonosítóval.

    Ez a parancs után ellenőrizze az állapot, ha egy futó állapotban van, és információk jelennek-e a feladat a műveletekhez.

6. A feladat sikeres befejeződése után ellenőrizheti, hogy az adatok jön létre, és az SQL-adatbázistáblában szereplő exportált a következő paranccsal:

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D oozietest
    ```

    : A `1>` kéri, adja meg a következő lekérdezést:

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    A visszakapott információk olyan, mint a következő szöveget:

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

A Oozie parancs további információkért lásd: [Oozie parancssori eszköz](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

## <a name="oozie-rest-api"></a>Oozie REST API-n

Az Oozie REST API-t a saját eszközök, amelyek használhatók a Oozie hozhat létre. A következő egy HDInsight-specifikus Oozie REST API használatával kapcsolatos információt:

* **URI**: érheti el a REST API, a fürtön kívüli `https://CLUSTERNAME.azurehdinsight.net/oozie`.

* **Hitelesítési**: hitelesítést végezni, az API-t a fürt HTTP-fiók (felügyeleti) és jelszót használják. Példa:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Az Oozie REST API-val kapcsolatos további információkért lásd: [Oozie webszolgáltatási API-ra](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>Oozie webes felhasználói felület

Az Oozie webes felhasználói felület révén Oozie feladatok állapotának webes képet a fürtön. A webes felhasználói felület használatával megtekintheti a következő információkat:

   * Feladat állapota
   * Feladat definíciója
   * Konfiguráció
   * A műveletek a feladat grafikonja
   * A feladat naplók

A műveletek a feladat részleteit is megtekintheti.

Szeretne használni az Oozie webes felhasználói felület, kövesse az alábbi lépéseket:

1. A HDInsight-fürthöz SSH-alagút létrehozása. További információkért lásd: [használata SSH Tunneling hdinsight](hdinsight-linux-ambari-ssh-tunnel.md).

2. Miután létrehozott egy alagúton, a böngészőben nyissa meg az Ambari webes felhasználói felület. Az URI-azonosítója az Ambari hely `https://CLUSTERNAME.azurehdinsight.net`. Cserélje le `CLUSTERNAME` a Linux-alapú HDInsight-fürt nevét.

3. Válassza ki a lap bal oldalán, **Oozie** > **Gyorshivatkozások** > **Oozie webes felhasználói felületén**.

    ![A menük képe](./media/hdinsight-use-oozie-linux-mac/ooziewebuisteps.png)

4. Az Oozie webes felhasználói felület alapértelmezés szerint a futó munkafolyamat-feladat megjelenítése. A munkafolyamat-feladatok megtekintéséhez válasszon **összes feladat**.

    ![Minden feladat jelenik meg](./media/hdinsight-use-oozie-linux-mac/ooziejobs.png)

5. További információ a feladatok megtekintéséhez jelölje ki a feladatot.

    ![Munkakör adatai](./media/hdinsight-use-oozie-linux-mac/jobinfo.png)

6. Az a **Feladatinformáció** lapon megtekintheti az alapszintű adatok és az egyes műveletek a feladatban található. Segítségével a lap tetején megtekintése a **feladatdefiníció**, **feladat konfigurációs**, hozzáférés a **Job Log**, vagy megtekintheti a feldolgozás alatt Irányítottaciklikusdiagramhoz(DAG)**DAG feladat**.

   * **Job Log**: válassza ki a **Get Logs** gombra kattint, hogy a feladat összes naplójának beolvasása, vagy használja a **keresési szűrőt adjon meg** a naplók szűrése mező.

       ![Feladat-napló](./media/hdinsight-use-oozie-linux-mac/joblog.png)

   * **Feladat DAG**: az adatbázis-elérhetőségi csoport az adatelérési utak venni a munkafolyamaton keresztül egy grafikus áttekintése.

       ![Feladat DAG](./media/hdinsight-use-oozie-linux-mac/jobdag.png)

7. Ha egy a művelet a **Feladatinformáció** adatait. a művelet mindez lapon. Válassza például a **RunSqoopExport** művelet.

    ![A művelet adatai](./media/hdinsight-use-oozie-linux-mac/action.png)

8. Megtekintheti a részleteket a művelet, például egy hivatkozást a **konzol URL-címe**. Ez a hivatkozás segítségével feladat követő a feladat információinak megtekintése.

## <a name="schedule-jobs"></a>Feladatok ütemezése

A koordinátor segítségével adja meg egy kezdő végpontot és feladatok előfordulási gyakoriságát. A munkafolyamat ütemezését megadásához kövesse az alábbi lépéseket:

1. Az alábbi parancs segítségével hozzon létre egy fájlt **coordinator.xml**:

    ```bash
    nano coordinator.xml
    ```

    A fájl tartalmát a következő XML-kód használata:

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
    > A `${...}` változók futásidőben feladatdefinícióban értékek helyett. A változók az alábbiak:
    >
    > * `${coordFrequency}`: A futó példányait a feladat között eltelt idő.
    > * `${coordStart}`: A feladat kezdési időpontja.
    > * `${coordEnd}`: A feladat befejezési időpontja.
    > * `${coordTimezone}`: Koordinátor feladatok olyan rögzített időzónában nem nyári időszámításra, az általában képviseli UTC formátumot FOGJA használni. Ez az időzóna kezeli a *Oozie feldolgozási időzóna.*
    > * `${wfPath}`: Az a workflow.xml elérési útja.

2. Mentse a fájlt, jelölje ki a Ctrl + X, adja meg `Y`, majd válassza ki **Enter**.

3. A fájl átmásolása a feladat a munkakönyvtár, a következő paranccsal:

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Módosítani a `job.xml` fájlt, a következő paranccsal:

    ```
    nano job.xml
    ```

    A következő módosításokat:

   * Kérje meg a koordinátor-fájl helyett a munkafolyamat futtatásához Oozie, módosítsa `<name>oozie.wf.application.path</name>` való `<name>oozie.coord.application.path</name>`.

   * Beállítása a `workflowPath` használják a koordinátor adja hozzá a következő XML változó:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Cserélje le a `wasb://mycontainer@mystorageaccount.blob.core.windows` az értéket, a többi bejegyzés a job.xml fájl a szöveget.

   * A start, végfelhasználók és a koordinátor gyakorisága megadásához adja hozzá a következő XML:

        ```xml
        <property>
            <name>coordStart</name>
            <value>2017-05-10T12:00Z</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>2017-05-12T12:00Z</value>
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

       Ezeket az értékeket a 12:00 PM 2017. Előfordulhat, hogy 10., a kezdési időt és befejezési időt 2017. május 12. Állítsa be. Ez a feladat futtatási időköze napi értéke. A gyakoriság értéke (percben), így 24 óra x 60 perc = 1440 perc. Végezetül UTC időzóna van beállítva.

5. Mentse a fájlt, jelölje ki a Ctrl + X, adja meg `Y`, majd válassza ki **Enter**.

6. A feladat futtatásához a következő paranccsal:

    ```
    oozie job -config job.xml -run
    ```

    Ez a parancs küldi el, és elindítja a feladatot.

7. Ha az Oozie a webes felhasználói felülete, és válassza ki a **koordinátor feladatok** lapon talál információkat, például az alábbi képen:

    ![Koordinátor feladatok lap](./media/hdinsight-use-oozie-linux-mac/coordinatorjob.png)

    A **következő Materialization** -bejegyzése tartalmazza a feladat fut. a következő újraindításkor.

8. A korábbi munkafolyamat-feladatot Ha a webes felhasználói felületen válassza ki a feladatot bejegyzést megjeleníti az információt a feladathoz:

    ![Koordinátor Feladatinformáció](./media/hdinsight-use-oozie-linux-mac/coordinatorjobinfo.png)

    > [!NOTE]
    > Ez a rendszerkép csak a feladat sikeres futtatása az ütemezett munkafolyamaton belüli egyes műveletek nem jeleníti meg. Az egyes műveletek megtekintéséhez válasszon a **művelet** bejegyzéseket.

    ![A művelet adatai](./media/hdinsight-use-oozie-linux-mac/coordinatoractionjob.png)

## <a name="troubleshooting"></a>Hibaelhárítás

A Oozie felhasználói felületén megtekintheti az Oozie naplókat. Az Oozie felhasználói felületén a Hadoopból keresse meg a MapReduce-feladatok, amelyek a munkafolyamat által indított mutató hivatkozásokat is tartalmaz. A minta hibaelhárítási kell lennie:

   1. Tekintse meg a feladatot Oozie webes felhasználói felület.

   2. Ha egy hiba vagy egy adott művelet sikertelen, válassza ki a művelet, ha a **hibaüzenet** mező további információkat tartalmazza a hiba esetén.

   3. Ha elérhető, használja a művelet az URL-CÍMÉT megtekintheti annak további részleteit, például a Hadoopból naplókat, a művelet.

Bizonyos hibák léphetnek fel, és azok megoldását a következők:

### <a name="ja009-cannot-initialize-cluster"></a>JA009: Nem lehet inicializálni a fürt

**A jelenség**: A feladat állapota **FELFÜGGESZTETT**. A feladat megjelenítése a részletek a `RunHiveScript` állapotának **START_MANUAL**. A művelet kiválasztása a következő hibaüzenetet jeleníti meg:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**OK**: az Azure Blob storage címek szerepel a **job.xml** fájl nem tartalmaz a tároló vagy a tárfiók nevét. A Blob storage-cím formátuma lehet `wasb://containername@storageaccountname.blob.core.windows.net`.

**Megoldási**: módosítsa a Blob storage címek, amely a feladat használja.

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltuser"></a>JA002: Oozie nem lehet megszemélyesíteni &lt;felhasználó >

**A jelenség**: A feladat állapota **FELFÜGGESZTETT**. A feladat megjelenítése a részletek a `RunHiveScript` állapotának **START_MANUAL**. Ha a művelet, a következő hibaüzenetet jeleníti meg:

    JA002: User: oozie is not allowed to impersonate <USER>

**OK**: A jelenlegi engedélybeállítások Oozie megszemélyesíthet-e a megadott felhasználói fiók tiltása.

**Megoldási**: Oozie megszemélyesíthet-e a felhasználók a **felhasználók** csoport. Használja a `groups USERNAME` megtekintéséhez a csoportokat, amelyek a felhasználói fiók tagja. Ha a felhasználó nem tagja a **felhasználók** csoportjában vegye fel a felhasználót a csoportba a következő paranccsal:

    sudo adduser USERNAME users

> [!NOTE]
> Mielőtt HDInsight felismeri, hogy a felhasználó hozzáadta-e a csoport több percig is eltarthat.

### <a name="launcher-error-sqoop"></a>Hiba (Sqoop) indítója

**A jelenség**: A feladat állapota **KILLED**. A feladat megjelenítése a részletek a `RunSqoopExport` állapotának **hiba**. Ha a művelet, a következő hibaüzenetet jeleníti meg:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**OK**: Sqoop nem tudja betölteni az adatbázis eléréséhez szükséges adatbázis-illesztőprogramját.

**Megoldási**: egy Oozie feladatból Sqoop használata esetén meg kell adni a további erőforrások, például a workflow.xml, a feladat használ az adatbázis-illesztőprogramját. Is, hivatkozik, amely tartalmazza az adatbázis-illesztőprogramját az archívum a `<sqoop>...</sqoop>` a workflow.xml szakasza.

Például a feladat ebben a dokumentumban, akkor használja az alábbi lépéseket:

1. Másolás a `sqljdbc4.1.jar` fájlt a **/oktatóanyagok/useoozie** könyvtár:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc41.jar /tutorials/useoozie/sqljdbc41.jar
    ```

2. Módosítsa a `workflow.xml` a következő XML-kód hozzáadása a fenti új sor `</sqoop>`:

    ```xml
    <archive>sqljdbc41.jar</archive>
    ```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan adhat meg egy Oozie munkafolyamat és az Oozie-feladat futtatása. Hogyan működnek a hdinsight eszközzel kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [Az időalapú Oozie-koordinátor használata a hdinsight eszközzel][hdinsight-oozie-coordinator-time]
* [Hdinsight Hadoop-feladatokat az adatok feltöltése][hdinsight-upload-data]
* [Sqoop használata a hadooppal a Hdinsightban][hdinsight-use-sqoop]
* [A Hive használata a hdinsight Hadoop][hdinsight-use-hive]
* [A Pig használata a HDInsight Hadoop][hdinsight-use-pig]
* [Java-MapReduce programok fejlesztése a HDInsight][hdinsight-develop-mapreduce]

[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563
[azure-data-factory-pig-hive]: ../data-factory/transform-data.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
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

[sqldatabase-create-configue]: sql-database-create-configure.md
[sqldatabase-get-started]: sql-database-get-started.md

[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
