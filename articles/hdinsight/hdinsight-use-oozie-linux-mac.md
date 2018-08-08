---
title: A Linux-alapú Azure HDInsight Hadoop Oozie munkafolyamatok használata
description: Linux-alapú HDInsight a Hadoop az Oozie használata. Megtudhatja, hogyan Oozie munkafolyamatokat, és az Oozie-feladatok elküldéséhez.
services: hdinsight
ms.service: hdinsight
ms.custom: hdinsightactive
author: omidm1
ms.author: omidm
editor: jasonwhowell
ms.topic: conceptual
ms.date: 06/26/2018
ms.openlocfilehash: a8a1b45d28b8d762a659ccdcc6986dcacb3a9d3e
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39599173"
---
# <a name="use-oozie-with-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>Az Oozie használata a Hadooppal és a Linux-alapú Azure HDInsight munkafolyamat futtatása

[!INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Ismerje meg, hogyan lehet Apache Oozie használata az Azure HDInsight Hadoop-keretrendszerrel. Az Oozie egy rendszer munkafolyamat és összehangoláshoz, amely a Hadoop-feladatokat kezeli. Az Oozie integrálva van a Hadoop-veremmel, és támogatja a következő feladatokat:

* Az Apache MapReduce
* Az Apache Pig
* Az Apache Hive
* Az Apache sqoop használatával

Az Oozie használatával a rendszer, például Java programok vagy héjparancsfájlok ütemezésére adott feladatok ütemezéséhez.

> [!NOTE]
> HDInsight munkafolyamatok meghatározásához egy másik lehetőség, hogy az Azure Data Factory használata. A Data Factory kapcsolatos további információkért lásd: [Hive és a Data Factory és a Pig használata a][azure-data-factory-pig-hive].


## <a name="prerequisites"></a>Előfeltételek

* **Egy HDInsight-fürt**: lásd: [HDInsight Linux első lépések](/hadoop/apache-hadoop-linux-tutorial-get-started.md)

> [!IMPORTANT]
> A dokumentum lépéseinek elvégzéséhez egy Linux-alapú HDInsight-fürt szükséges. Linux az egyetlen operációs rendszer használt a HDInsight 3.4-es vagy újabb verzió. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="example-workflow"></a>Példa-munkafolyamat

Az itt bemutatott munkafolyamat két műveleteket tartalmaz. A műveletek olyan feladatokhoz, például a Hive, Sqoop, a MapReduce vagy egyéb folyamatok futtatása definíciókat:

![A munkafolyamat diagramja][img-workflow-diagram]

1. Egy Hive-művelet bontsa ki a rekordok egy HiveQL-parancsfájlt futtat a **hivesampletable** , amely HDInsight foglalt. Minden egyes sorára adatokat egy adott mobileszközzel meglátogatása ismerteti. A rekord formátumban jelenik meg, például a következő szöveget:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    A Hive-parancsfájl ebben a dokumentumban használt egyes platformokon, például Android vagy iPhone-on, a teljes látogatások száma, és a számát, az új Hive tábla tárolja.

    További információ a Hive-ról: [A Hive használata a HDInsightban][hdinsight-use-hive].

2. A Sqoop művelet exportálja az új Hive tábla tartalmát egy Azure SQL Database-ben létrehozott tábla. Sqoop használatával kapcsolatos további információkért lásd: [Hadoop Sqoop használata a HDInsight-][hdinsight-use-sqoop].

> [!NOTE]
> Tekintse meg a HDInsight-fürtökön támogatott Oozie verziók [a HDInsight által biztosított Hadoop-fürtverziók újdonságai][hdinsight-versions].

## <a name="create-the-working-directory"></a>A munkakönyvtárban létrehozása

Az Oozie vár, hogy ugyanabban a címtárban a feladat végrehajtásához szükséges összes erőforrást tárolja. Ez a példa **wasb: / / / oktatóprogramok/useoozie**. Ez a könyvtár létrehozásához hajtsa végre az alábbi lépéseket:

1. Csatlakozás a HDInsight-fürthöz SSH használatával:

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Cserélje le az `sshuser` elemet a fürt SSH-felhasználónevére. Cserélje le `clustername` a fürt nevére. További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

2. A címtár létrehozásához használja a következő parancsot:

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]
    > A `-p` paraméter okoz az összes könyvtár létrehozása az elérési utat. A **adatok** könyvtárat a által használt adatok tárolásához használja a **useooziewf.hql** parancsfájlt.

3. Győződjön meg arról, hogy az Oozie megszemélyesíthet-e a felhasználói fiók, használja a következő parancsot:

    ```bash
    sudo adduser username users
    ```

    Cserélje le `username` az SSH-felhasználónévvel.

    > [!NOTE]
    > Hibák, amelyek jelzik, hogy a felhasználó tagja már figyelmen kívül hagyhatja a `users` csoport.

## <a name="add-a-database-driver"></a>Egy adatbázis-illesztőprogram hozzáadása

Mivel ez a munkafolyamat Sqoop segítségével exportál adatokat az SQL database, meg kell adnia a JDBC-illesztővel kommunikál az SQL database egy példányát. A JDBC-illesztőprogram a munkakönyvtárban történő másolásához használja az SSH-munkamenetből a következő parancsot:

```bash
hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /tutorials/useoozie/
```

> [!NOTE]
> Előfordulhat, hogy kap egy üzenetet, hogy a fájl már létezik.

Ha a munkafolyamat más erőforrások, például egy jar, amely tartalmaz egy MapReduce-alkalmazást használja, hozzá kell ezeket az erőforrásokat is.

## <a name="define-the-hive-query"></a>A Hive-lekérdezés megadása

A következő lépések segítségével hozzon létre egy Hive lekérdezés (HiveQL) nyelvi parancsfájlt, amely meghatározza egy lekérdezést. A lekérdezés az Oozie-munkafolyamatokkal, ez a dokumentum későbbi szakaszában fogja használni.

1. Az SSH-kapcsolatot, a következő parancs használatával hozzon létre egy fájlt `useooziewf.hql`:

    ```bash
    nano useooziewf.hql
    ```

3. Miután megnyílik a GNU nano szerkesztő, használja a fájl tartalmát a következő lekérdezést:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    Nincsenek a szkriptben használt két változót:

    * `${hiveTableName}`: A létrehozandó tábla nevét tartalmazza.

    * `${hiveDataFolder}`: A tábla az adatfájlok tárolására helyét tartalmazza.

    A munkafolyamat-definíciós fájlt, ebben az oktatóanyagban workflow.xml továbbítja ezeket az értékeket a futásidőben HiveQL-parancsfájlt.

4. Zárja be a szerkesztőt, válassza a Ctrl + X. Amikor a rendszer kéri, válassza ki a `Y` mentse a fájlt, írja be a következőt `useooziewf.hql` a fájl nevét, és válassza ki, **Enter**.

5. Az alábbi parancsokkal másolja `useooziewf.hql` való `wasb:///tutorials/useoozie/useooziewf.hql`:

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    Ezzel a paranccsal tárolhatók a `useooziewf.hql` a fürt HDFS-kompatibilis a tárterületen található fájl.

## <a name="define-the-workflow"></a>Határozhat meg munkafolyamatot

Oozie munkafolyamat-meghatározások a Hadoop folyamat adatdefiníciós nyelv (hPDL), amely folyamat XML-definíció nyelven íródtak. A következő lépések használatával határozhat meg munkafolyamatot:

1. A következő utasítás használatával hozhat létre és módosíthat egy új fájlt:

    ```bash
    nano workflow.xml
    ```

2. Miután megnyílik a nano szerkesztő, adja meg a következő XML formátumú, a fájl tartalmát:

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

    Nincsenek definiálva a munkafolyamat két műveletet:

   * `RunHiveScript`: Ez a művelet a kezdő művelet és futtat az `useooziewf.hql` Hive-szkriptet.

   * `RunSqoopExport`: Ez a művelet exportálja az adatokat a Hive-parancsfájl alapján létrehozott SQL-adatbázishoz a Sqoop használatával. Ez a művelet csak akkor fut, ha a `RunHiveScript` művelet létrejött.

     A munkafolyamat tartozik több bejegyzést, például `${jobTracker}`. Ezek a bejegyzések azokra az értékekre, használhatja a feladatdefiníciót le fogja cserélni. Ez a dokumentum későbbi szakaszában a feladatdefiníciót fog létrehozni.

     Azt is vegye figyelembe a `<archive>sqljdbc4.jar</archive>` bejegyzést a Sqoop szakaszban. Ez a bejegyzés arra utasítja a Oozie elérhetővé archívum Sqoop számára ez a művelet futtatásakor.

3. Mentse a fájlt, jelölje ki a Ctrl + X, adja meg `Y`, majd válassza ki **Enter**. 

4. A következő parancs használatával másolja a `workflow.xml` fájlt `/tutorials/useoozie/workflow.xml`:

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-the-database"></a>Az adatbázis létrehozása

SQL-adatbázis létrehozásához kövesse a lépéseket a [SQL-adatbázis létrehozása](../sql-database/sql-database-get-started.md) dokumentumot. Az adatbázis létrehozásakor `oozietest` az adatbázis neveként. Továbbá jegyezze fel az adatbázis-kiszolgáló nevét.

### <a name="create-the-table"></a>A tábla létrehozásához

> [!NOTE]
> Számos módon csatlakozhat az SQL-adatbázist hozzon létre egy táblát. A következő lépések során a [FreeTDS](http://www.freetds.org/) eszközt használjuk a HDInsight-fürtről.


1. A következő paranccsal pedig a freetds a HDInsight-fürtön:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. FreeTDS telepítését követően használja a következő parancsot a korábban létrehozott SQL database-kiszolgálóhoz csatlakozni:

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -P <sqlPassword> -p 1433 -D oozietest
    ```

    A következő szöveg hasonló kimenet jelenhet meg:

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

    A `GO` utasítás megadásakor a rendszer kiértékeli az előző utasításokat. Ezek az utasítások, hozzon létre egy táblát **mobiledata**, a munkafolyamat által használt.

    Győződjön meg arról, hogy a táblázat létrejött, használja a következő parancsokat:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    A következő szöveg hasonló kimenet jelenik meg:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo     mobiledata      BASE TABLE

4. Adja meg a kilépéshez a tsql-segédprogram `exit` , a `1>` kérdés.

## <a name="create-the-job-definition"></a>A feladatdefiníció létrehozása

A feladat definíciója, hogy hol található a workflow.xml ismerteti. Azt is bemutatja, hogy hol található egyéb fájlok, például a munkafolyamat által használt `useooziewf.hql`. Ezenkívül azt határozza meg a munkafolyamat és a hozzájuk társított fájlokat belül használt tulajdonságok értékeit.

1. Az alapértelmezett tároló teljes címének lekéréséhez használja az alábbi parancsot. Ezzel a címmel a konfigurációs fájlban a következő lépésben létrehozott.

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    Ez a parancs visszaadja az információkat, például a következő XML-kódot:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]
    > Ha a HDInsight-fürt az alapértelmezett tárolóként használja az Azure Storage a `<value>` elem tartalmát kezdődhet `wasb://`. Az Azure Data Lake Store-használata esetén inkább kezdődik, `adl://`.

    Mentse a tartalmát a `<value>` elem, ahogy a következő lépésben szolgál.

2. Hozza létre az Oozie feladat definíciójának konfigurációt, használja a következő parancsot:

    ```bash
    nano job.xml
    ```

3. Miután megnyílik a nano szerkesztő, használja a következő XML-kódot a fájl tartalmát:

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

   * Cserélje le az összes példányát `wasb://mycontainer@mystorageaccount.blob.core.windows.net` az alapértelmezett tároló korábban kapott értékkel.

     > [!WARNING]
     > Ha az elérési út egy `wasb` elérési útja, teljes elérési útját kell használnia. Nem Rövidítse le, hogy csak `wasb:///`.

   * Cserélje le `YourName` a bejelentkezési nevét, a HDInsight-fürt számára.
   * Cserélje le `serverName`, `adminLogin`, és `adminPassword` az SQL-adatbázis adataival.

     A legtöbb információ a fájlban a workflow.xml vagy ooziewf.hql fájlok, például a használt értékek feltöltésére használt `${nameNode}`.

     > [!NOTE]
     > A `oozie.wf.application.path` a bejegyzés határozza meg, hogy hol található a workflow.xml fájlt. Ez a fájl tartalmazza a feladat által futtatott munkafolyamat.

5. Mentse a fájlt, jelölje ki a Ctrl + X, adja meg `Y`, majd válassza ki **Enter**.

## <a name="submit-and-manage-the-job"></a>Küldje el és kezelheti a feladat

Az alábbi lépéseket az Oozie-munkafolyamatok a fürtön kezelheti és beküldheti az Oozie parancs segítségével. Az Oozie parancs egy egyszerű felületen keresztül a [Oozie REST API-val](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [!IMPORTANT]
> Az Oozie parancs használatakor a HDInsight fő csomópont teljes Tartománynevét kell használnia. Ez a teljes tartománynév csak érhető el a fürtöt, vagy ha a fürt ugyanazon a hálózaton lévő más gépről egy Azure virtuális hálózaton.


1. Az Oozie-szolgáltatás URL-Címének megszerzéséhez használja a következő parancsot:

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Ez visszaadja az információkat, például a következő XML-kódot:

    ```xml
    <name>oozie.base.url</name>
    <value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    A `http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie` rész nem az URL-cím, az Oozie-parancs használata.

2. Az URL-környezeti változó létrehozásához használja a következőt, így nem kell minden parancshoz meg:

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

    Cserélje le a korábban kapott egy URL-CÍMÉT.
3. A feladat elküldéséhez használja a következő:

    ```bash
    oozie job -config job.xml -submit
    ```

    Ez a parancs betölti a feladat adatainak `job.xml` és elküldi az Oozie, de nem futtatja a programot.

    A parancs befejeződését követően kell visszaadnia a feladat azonosítója például `0000005-150622124850154-oozie-oozi-W`. Ez az azonosító segítségével kezelheti a feladat.

4. A feladat állapotának megtekintéséhez használja a következő parancsot:

    ```bash
    oozie job -info <JOBID>
    ```

    > [!NOTE]
    > Cserélje le `<JOBID>` adja vissza az előző lépésben azonosítóval.

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

    Ez a feladat állapota `PREP`. Ez az állapot azt jelzi, hogy a feladat lett létrehozva, de nem indult el.

5. Indítsa el a feladatot, használja a következő parancsot:

    ```bash
    oozie job -start JOBID
    ```

    > [!NOTE]
    > Cserélje le `<JOBID>` a korábban visszaadott azonosítója.

    Ez a parancs után ellenőrizze az állapotot, ha egy futó állapotban van, és információk jelennek-e a műveletek a feladaton belül.

6. A feladat sikeres befejeződése után ellenőrizheti, hogy az adatok jön létre, és a következő parancs használatával az SQL-adatbázistáblába exportálni:

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D oozietest
    ```

    Jelenleg a `1>` kéri, írja be a következő lekérdezést:

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    A visszaadott információ olyan, mint a következő szöveget:

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Az Oozie parancs további információkért lásd: [Oozie parancssori eszköz](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

## <a name="oozie-rest-api"></a>Az Oozie REST API-val

Az Oozie REST API-val hozhat létre a saját eszközök, amelyek együttműködnek az Oozie. A következő egy HDInsight-specifikus az Oozie REST API használatával kapcsolatos információt:

* **URI-t**: a REST API, a fürtön kívülről is elérheti `https://CLUSTERNAME.azurehdinsight.net/oozie`.

* **Hitelesítési**: hitelesítést végezni, használja az API-t a fürt HTTP-fiókjának (rendszergazda) és a jelszót. Példa:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Az Oozie REST API használatáról további információkért lásd: [Oozie webszolgáltatási API-ra](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>Az Oozie webes felhasználói felületen

Az Oozie webes felület lehetővé teszi a webes az Oozie-feladat állapotát a fürtön. A webes felhasználói felületének tekintheti meg a következő információkat:

   * Feladat állapota
   * Feladat definíciója
   * Konfiguráció
   * A műveletek a feladat grafikonja
   * A feladathoz tartozó naplók

A tevékenységek a feladat részleteit is megtekintheti.

Hozzáférhet a Oozie webes felhasználói felületen, a következő lépéseket:

1. A HDInsight-fürthöz az SSH-alagút létrehozása. További információkért lásd: [SSH-bújtatással való HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

2. Miután létrehozott egy alagúton, a böngészőben nyissa meg az Ambari webes felület. Az Ambari hely az URI-ja `https://CLUSTERNAME.azurehdinsight.net`. Cserélje le `CLUSTERNAME` a Linux-alapú HDInsight-fürt nevére.

3. Válassza a lap bal oldalán, **Oozie** > **Gyorshivatkozások** > **Oozie webes felhasználói felület**.

    ![A menük képe](./media/hdinsight-use-oozie-linux-mac/ooziewebuisteps.png)

4. Az Oozie webes felület alapértelmezés szerint megjeleníti a futó munkafolyamat-feladatot. A munkafolyamat-feladatok megtekintéséhez válasszon **az összes feladat**.

    ![Minden feladat jelenik meg](./media/hdinsight-use-oozie-linux-mac/ooziejobs.png)

5. Egy feladattal kapcsolatos további információk megtekintéséhez válassza ki a feladatot.

    ![Munkakör adatai](./media/hdinsight-use-oozie-linux-mac/jobinfo.png)

6. Az a **feladat adatainak** lapon láthatja a feladat alapvető információkat és az egyes műveletek a feladaton belül. Használhatja a lap tetején megtekintéséhez a **feladatdefiníció**, **feladat konfigurációs**, hozzáférés a **Job Log**, vagy megtekintheti az egy irányított aciklikus diagramhoz (DAG) a feldolgozás alatt **DAG feladat**.

   * **Job Log**: válassza ki a **naplók lekérése** gombra kattint, hogy a feladat összes naplójának beolvasása, vagy használja a **adja meg a keresési szűrő** mezőt, a naplók szűrése.

       ![Job Log](./media/hdinsight-use-oozie-linux-mac/joblog.png)

   * **Feladat DAG**: A DAG az adatelérési utak venni a munkafolyamaton keresztül egy grafikus áttekintése.

       ![Feladat DAG](./media/hdinsight-use-oozie-linux-mac/jobdag.png)

7. Ha kiválaszt egy a művelet a **feladat adatainak** lap lehetővé teszi adatait. a műveletet. Jelölje be például a **RunSqoopExport** művelet.

    ![A művelet adatai](./media/hdinsight-use-oozie-linux-mac/action.png)

8. A művelet, például mutató hivatkozás részleteit láthatja a **konzol URL-címe**. A hivatkozás segítségével a feladat feladat tracker információinak megtekintése.

## <a name="schedule-jobs"></a>Feladatok ütemezése

A koordinátor segítségével adja meg egy kezdő, a vége és a feladatok előfordulási gyakoriságát. A munkafolyamat ütemezés meghatározása, hajtsa végre az alábbi lépéseket:

1. A következő paranccsal hozzon létre egy fájlt **coordinator.xml**:

    ```bash
    nano coordinator.xml
    ```

    A fájl tartalmát a következő XML-kódot használja:

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
    > A `${...}` változók futásidőben a feladatdefiníciót szereplő értékek helyébe. A változók az alábbiak:
    >
    > * `${coordFrequency}`: A futó a feldolgozás példányai között eltelt idő.
    > * `${coordStart}`: A feladat megkezdési ideje.
    > * `${coordEnd}`: A feladat befejezési ideje.
    > * `${coordTimezone}`: Feladatok koordinátora egy fix időzóna nem nyári időszámításra, az általában képviseli az UTC találhatók. Ezt az időzónát nevezik a *Oozie feldolgozási időzóna.*
    > * `${wfPath}`: A workflow.xml az elérési útja.

2. Mentse a fájlt, jelölje ki a Ctrl + X, adja meg `Y`, majd válassza ki **Enter**.

3. Másolja a fájlt a munkakönyvtárban történő a feladatot, használja a következő parancsot:

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Módosíthatja a `job.xml` fájlt, használja a következő parancsot:

    ```
    nano job.xml
    ```

    A következő módosításokat:

   * Utasíthatja az Oozie-koordinátor fájl helyett a munkafolyamat futtatásához, módosítsa `<name>oozie.wf.application.path</name>` való `<name>oozie.coord.application.path</name>`.

   * Beállítása a `workflowPath` a koordinátor által használt, adja hozzá a következő XML formátumú változó:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Cserélje le a `wasb://mycontainer@mystorageaccount.blob.core.windows` SMS-t a job.xml fájlban tételek használt érték.

   * Határozza meg a kezdő, végfelhasználók és a koordinátor gyakoriságát, adja hozzá a következő XML-kódot:

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

       Ezeket az értékeket a 12:00-kor 2017. május 10., a kezdési időpontot, és a záró időpont 2017. május 12. Állítsa be. A feladat futtatásához időköze napi. Gyakoriság van percek alatt, így a 24 óra × 60 perc = 1440 perc. Végül az adott időzóna UTC van beállítva.

5. Mentse a fájlt, jelölje ki a Ctrl + X, adja meg `Y`, majd válassza ki **Enter**.

6. A feladat futtatásához a következő paranccsal:

    ```
    oozie job -config job.xml -run
    ```

    Ezzel a paranccsal küldi el, és elindítja a feladatot.

7. Ha az Oozie webes felhasználói Felületet, és válassza ki a **koordinátor feladatok** lapon információkat, például az alábbi képen látható:

    ![Koordinátor feladatok lap](./media/hdinsight-use-oozie-linux-mac/coordinatorjob.png)

    A **tovább Materialization** bejegyzést tartalmaz, amelyek a feladat fut, amikor legközelebb.

8. Például a korábbi munkafolyamat-feladatot Ha a feladat-bejegyzés a webes felhasználói felületen, információkat jeleníti meg a feladat:

    ![Koordinátor feladat adatai](./media/hdinsight-use-oozie-linux-mac/coordinatorjobinfo.png)

    > [!NOTE]
    > Ez a rendszerkép csak a feladat sikeres futtatások nem ütemezett a munkafolyamaton belüli egyes műveleteket jeleníti meg. Az egyes műveletek megtekintéséhez válasszon ki egy, a **művelet** bejegyzéseket.

    ![A művelet adatai](./media/hdinsight-use-oozie-linux-mac/coordinatoractionjob.png)

## <a name="troubleshooting"></a>Hibaelhárítás

Az Oozie felhasználói felületén az Oozie-naplók használatával megtekintheti. Az Oozie felhasználói felületén a JobTracker naplókat, a MapReduce-feladatok a munkafolyamat által elindított hivatkozásokat is tartalmaz. A minta hibaelhárítási kell lennie:

   1. Tekintse meg a feladatot az Oozie webes felhasználói felületen.

   2. Ha egy hiba vagy egy bizonyos művelet sikertelen volt, válassza ki annak ellenőrzéséhez, hogy a művelet a **hibaüzenet** mező a hibával kapcsolatban további információkat biztosít.

   3. Ha elérhető, az URL-címet a művelet segítségével további részleteket, például a JobTracker naplókat, a művelet.

Az alábbiakban a találkozhat bizonyos hibákat és azok megoldását.

### <a name="ja009-cannot-initialize-cluster"></a>JA009: Nem lehet inicializálni a fürt

**A jelenség**: A feladat állapota **FELFÜGGESZTETT**. A feladat megjelenítése a részletek a `RunHiveScript` állapotjelentése **START_MANUAL**. Kiválasztja a műveletet a következő hibaüzenet jelenik meg:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**OK**: az Azure Blob storage a használt címek a **job.xml** fájl nem tartalmaz, a storage-tároló vagy tárfiók neve. A Blob storage-cím formátumú lehet `wasb://containername@storageaccountname.blob.core.windows.net`.

**Feloldási**: módosítsa a Blob storage címeket, amelyek a feladat használ.

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltuser"></a>JA002: Oozie nem engedélyezett megszemélyesíteni &lt;felhasználó >

**A jelenség**: A feladat állapota **FELFÜGGESZTETT**. A feladat megjelenítése a részletek a `RunHiveScript` állapotjelentése **START_MANUAL**. Ha a művelet, a következő hibaüzenetet jeleníti meg:

    JA002: User: oozie is not allowed to impersonate <USER>

**OK**: az aktuális engedély-beállítások a megadott felhasználói fiók megszemélyesítése Oozie tiltása.

**Feloldási**: Oozie tudja megszemélyesíteni a felhasználók a **felhasználók** csoport. Használja a `groups USERNAME` a csoportokat, amelyek a felhasználói fiók tagja megtekintheti. Ha a felhasználó nem tagja a **felhasználók** csoportjában adja hozzá a felhasználót a csoporthoz a következő paranccsal:

    sudo adduser USERNAME users

> [!NOTE]
> HDInsight felismeri, hogy a felhasználó hozzáadta-e a csoport előtt több percet is igénybe vehet.

### <a name="launcher-error-sqoop"></a>Hiba (Sqoop) indítója

**A jelenség**: A feladat állapota **KILLED**. A feladat megjelenítése a részletek a `RunSqoopExport` állapotjelentése **hiba**. Ha a művelet, a következő hibaüzenetet jeleníti meg:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**OK**: Sqoop nem tudja betölteni az adatbázis eléréséhez szükséges adatbázis-illesztőprogramját.

**Feloldási**: az Oozie-feladatok a Sqoop használata esetén meg kell adni az adatbázis-illesztőt is a további erőforrások, például a workflow.xml, a feladat által használt. Az archívumot, amely tartalmazza az adatbázis-illesztőprogramot az is hivatkozhat a `<sqoop>...</sqoop>` a workflow.xml szakaszában.

Például a feladat ebben a dokumentumban, akkor használja az alábbi lépéseket:

1. Másolás a `sqljdbc4.1.jar` fájlt a **/oktatóprogramok/useoozie** könyvtár:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc41.jar /tutorials/useoozie/sqljdbc41.jar
    ```

2. Módosítsa a `workflow.xml` hozzáadásához egy új sort a következő XML formátumú `</sqoop>`:

    ```xml
    <archive>sqljdbc41.jar</archive>
    ```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, az Oozie-munkafolyamatokkal definiálása és az Oozie-feladatok futtatása. További információ a HDInsight használatáról, tekintse meg a következő cikkeket:

* [Időalapú Oozie-koordinátor használata a HDInsight][hdinsight-oozie-coordinator-time]
* [A HDInsight Hadoop-feladatok adatok feltöltése][hdinsight-upload-data]
* [A Sqoop használata a HDInsight Hadoop-keretrendszerrel][hdinsight-use-sqoop]
* [A Hive használata a HDInsight Hadoop-keretrendszerrel][hdinsight-use-hive]
* [A Pig használata a HDInsight Hadoop-keretrendszerrel][hdinsight-use-pig]
* [Java MapReduce programok fejlesztése a HDInsight][hdinsight-develop-mapreduce]

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
