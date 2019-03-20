---
title: Időalapú Hadoop Oozie-koordinátor használata a HDInsight
description: Időalapú Hadoop Oozie-koordinátor használata a HDInsight, a big data-szolgáltatás. Ismerje meg, hogyan Oozie-munkafolyamatok és koordinátorok, valamint feladatok elküldéséhez.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/04/2017
ROBOTS: NOINDEX
ms.openlocfilehash: a47a30995f651204782325a9f984086fdf382a03
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202197"
---
# <a name="use-time-based-apache-oozie-coordinator-with-apache-hadoop-in-hdinsight-to-define-workflows-and-coordinate-jobs"></a>Időalapú Apache Oozie-koordinátor használata a HDInsight Apache hadoop-keretrendszer határozza meg a munkafolyamatok, és a feladatok koordinálása
Ebből a cikkből megtudhatja, hogy hogyan munkafolyamatok és koordinátorok meghatározásához, és hogy miként indítható el a koordinátor feladatok, ideje alapján. Hasznos lehet haladhat végig [Apache Oozie használata a HDInsight-] [ hdinsight-use-oozie] Ez a cikk elolvasása előtt. Oozie, mellett is ütemezhet feladatokat az Azure Data Factory. Azure Data Factory kapcsolatban lásd: [Apache Pig használata és az Apache Hive, a Data Factory](../data-factory/transform-data.md).

> [!NOTE]  
> Ez a cikk egy Windows-alapú HDInsight-fürt szükséges. Az Oozie, beleértve az időalapú feladatok egy Linux-alapú fürt használatával kapcsolatos információkat lásd: [használja az Oozie és munkafolyamat futtatása a Linux-alapú HDInsight Hadoop-keretrendszerrel](hdinsight-use-oozie-linux-mac.md)

## <a name="what-is-oozie"></a>Mi az Oozie
Az Apache Oozie egy munkafolyamat/koordinációs rendszer, amely a Hadoop-feladatokat kezeli. Integrálva van a Hadoop-veremmel, és az Apache Hadoop MapReduce, Apache Pig, Apache Hive- és Apache sqoop használatával támogatja a Hadoop-feladatokat. Is használható a rendszer, például Java programok vagy héjparancsfájlok ütemezésére adott feladatok ütemezéséhez.

Az alábbi képen látható a munkafolyamat fogja végrehajtani:

![A munkafolyamat diagramja][img-workflow-diagram]

A munkafolyamat két műveletet tartalmaz:

1. Egy Hive-művelet fut. a HiveQL-parancsfájlt megszámlálni az egyes log-szintű írjon be egy Apache log4j naplófájlok. A mezők típusát és súlyosságát, például megjelenítése [NAPLÓZÁSI szint] mező tartalmazó sor log4j naplófájlok áll:

        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...

    A Hive-parancsfájl kimenet hasonlít:

        [DEBUG] 434
        [ERROR] 3
        [FATAL] 1
        [INFO]  96
        [TRACE] 816
        [WARN]  4

    Hive-ról további információkért lásd: [Apache Hive használata a HDInsight][hdinsight-use-hive].
2. A Sqoop művelet exportálja a HiveQL művelet kimenete egy Azure SQL database egyik táblájába. Sqoop használatával kapcsolatos további információkért lásd: [használata Apache sqoop használatával HDInsight][hdinsight-use-sqoop].

> [!NOTE]  
> Tekintse meg a HDInsight-fürtökön támogatott Oozie verziók [a HDInsight által biztosított fürtverziók újdonságai?] [hdinsight-versions].
>
>

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Munkaállomás Azure PowerShell-lel**.

    > [!IMPORTANT]  
    > A HDInsight-erőforrások Azure Service Managerrel történő kezelésének Azure PowerShell-támogatása **elavult**, így 2017. január 1-től megszűnik. A jelen dokumentumban leírt lépések az új HDInsight-parancsmagokat használják, amelyek az Azure Resource Managerrel működnek.
    >
    > Kérjük, kövesse az alábbi cikkben leírt lépéseket az Azure PowerShell legújabb verziójának telepítéséhez: [Install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs) (Az Azure PowerShell letöltése és konfigurálása). Ha vannak olyan parancsprogramjai, amelyeket módosítani kell az új, az Azure Resource Managerrel működő parancsmagok használatához, tekintse meg az alábbi cikket: [Migrating to Azure Resource Manager-based development tools for HDInsight clusters](hdinsight-hadoop-development-using-azure-resource-manager.md) (Az Azure Resource Manager-alapú fejlesztési eszközökre való áttérés HDInsight-fürtök esetén).

* **Egy HDInsight-fürt**. Egy HDInsight-fürt létrehozásával kapcsolatos információkért lásd: [létre HDInsight-fürtök][hdinsight-provision], vagy [HDInsight – első lépések][hdinsight-get-started]. Szüksége lesz az oktatóanyagot az alábbi adatokat:

    |Fürt tulajdonság|Windows PowerShell-változó neve|Érték|Leírás|
    |---|---|---|---|
    |HDInsight-fürt neve|$clusterName||A HDInsight-fürt, amelyen ez az oktatóanyag futni fog.|
    |HDInsight-fürt felhasználóneve|$clusterUsername||A HDInsight fürt felhasználó neve. |
    |HDInsight-fürt felhasználói jelszó |$clusterPassword||A HDInsight fürt felhasználó jelszavát.|
    |Az Azure storage-fiók neve|$storageAccountName||Azure Storage-fiókot a HDInsight-fürtön elérhető. A jelen oktatóanyag esetében használja az alapértelmezett tárfiókot, a fürt üzembe helyezése során megadott.|
    |Az Azure Blob-tároló neve|$containerName||Ebben a példában használja az Azure Blob storage-tároló, amely a HDInsight fürt alapértelmezett fájlrendszerének szolgál. Alapértelmezés szerint rendelkezik a HDInsight-fürt azonos néven.|


* **Az Azure SQL database**. Konfigurálnia kell egy tűzfalszabályt az SQL Database-kiszolgálóhoz, hogy engedélyezze a hozzáférést a munkaállomáson. Egy Azure SQL-adatbázis létrehozása, és a tűzfal konfigurálásával kapcsolatos útmutatásért lásd: [első lépései az Azure SQL database-t használó][sqldatabase-get-started]. Ez a cikk egy Windows PowerShell-parancsprogram létrehozásához az Azure SQL database tábla, amely ebben az oktatóanyagban szüksége lesz.

    |Az SQL database tulajdonság|Windows PowerShell-változó neve|Érték|Leírás|
    |---|---|---|---|
    |Az SQL database-kiszolgálónév|$sqlDatabaseServer||Az SQL adatbázis-kiszolgáló, amelyhez a sqoop használatával exportálja az adatokat. |
    |SQL database-bejelentkezési név|$sqlDatabaseLogin||Az SQL Database-bejelentkezési név.|
    |Az SQL database bejelentkezési jelszava|$sqlDatabaseLoginPassword||SQL-adatbázis bejelentkezési jelszót.|
    |SQL-adatbázis neve|$sqlDatabaseName||Az Azure SQL database, amelyhez a sqoop használatával exportálja az adatokat. |

  > [!NOTE]   
  > Alapértelmezés szerint az Azure SQL database az Azure-szolgáltatások, például az Azure HDInsight engedélyezi a csatlakozást. Ha a tűzfal beállítás nincs engedélyezve, engedélyeznie kell azt az Azure Portalról. Egy SQL-adatbázis létrehozása, és a tűzfal-szabályok konfigurálásával kapcsolatos útmutatást lásd: [létrehozása és konfigurálása az SQL Database][sqldatabase-get-started].

> [!NOTE]  
> Kitöltés a táblázatok értékeit. Az oktatóanyag a hasznos lesz.

## <a name="define-oozie-workflow-and-the-related-hiveql-script"></a>Oozie-munkafolyamatokkal, és a kapcsolódó HiveQL-parancsfájlt megadása
Az Oozie-munkafolyamatok definíciók hPDL (az XML folyamat definition language) nyelven íródtak. Az alapértelmezett munkafolyamat-fájl neve *workflow.xml*.  Mentse helyileg a munkafolyamat-fájlt fog, és ezután telepítheti a HDInsight-fürthöz az oktatóanyag későbbi részében az Azure PowerShell használatával.

A Hive-művelet a munkafolyamat meghívja a HiveQL-parancsfájlt. A parancsfájl három HiveQL utasításokat tartalmazza:

1. **A DROP TABLE utasítás** törli a log4j Hive-táblába, ha van ilyen.
2. **A CREATE TABLE utasítás** log4j Hive külső tábla létrehozása, amely log4j naplófájlok; helyére mutat
3. **A log4j naplófájlok helyét**. A mezőhatároló van ",". Az alapértelmezett sor határoló "\n". Külső Hive-tábla elkerülése érdekében az adatfájl távolít el az eredeti helyről, abban az esetben, ha szeretné futtatni az Oozie-munkafolyamatokkal többször szolgál.
4. **A FELÜLÍRÁSA INSERT utasítás** számát a log4j Hive-táblába, és minden napló-szintű típusok menti a kimenetet egy Azure Blob tárolási helyére.

> [!NOTE]  
> Nincs olyan ismert Hive útvonallal kapcsolatos probléma. Az Oozie-feladatok elküldésekor futtatni fogja ezt a problémát. Az utasításokat a probléma kijavítása a TechNet Wikin található: [HDInsight Hive-hiba: Nem lehet átnevezni][technetwiki-hive-error].

**A HiveQL-parancsfájlt, a munkafolyamat által meghívandó meghatározása**

1. Hozzon létre egy szövegfájlt az alábbi tartalommal:

        DROP TABLE ${hiveTableName};
        CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
        INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

    A szkriptben használt három változók vannak:

   * ${hiveTableName}
   * ${hiveDataFolder}
   * ${hiveOutputFolder}

     A munkafolyamat-definíciós fájlja (ebben az oktatóanyagban workflow.xml) ezeket az értékeket továbbítja futtatáskor a HiveQL-parancsfájlt.
2. Mentse a fájlt az **C:\Tutorials\UseOozie\useooziewf.hql** ANSI (ASCII) kódolással. (Használja a Jegyzettömb, ha a szöveges szerkesztő nem biztosítja ezt a lehetőséget.) Ezt a parancsfájlt az oktatóanyag későbbi részében telepíti a HDInsight-fürthöz.

**Egy munkafolyamat meghatározása**

1. Hozzon létre egy szövegfájlt az alábbi tartalommal:

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
                <param>hiveOutputFolder=${hiveOutputFolder}</param>
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
            <arg>${hiveOutputFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\001"</arg>
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

    Nincsenek definiálva a munkafolyamat két műveletet. A kezdő művelet *RunHiveScript*. Ha a művelet a futás *OK*, a következő művelet *RunSqoopExport*.

    A RunHiveScript rendelkezik értékkel a változókat. Az értékek sikeres lesz, az Oozie feladat elküldésekor a munkaállomásáról Azure PowerShell használatával.

    Munkafolyamat-változók

    |Munkafolyamat-változók|Leírás|
    |---|---|
    |${jobTracker}|Adja meg a Hadoop-feladat tracker URL-CÍMÉT. Használat **jobtrackerhost:9010** HDInsight fürtön, 3.0-s és 2.0-s verzióját.|
    |${nameNode}|Adja meg a Hadoop neve csomópont URL-CÍMÉT. Használja az alapértelmezett fájl rendszer wasb: / / cím, például *wasb: / /&lt;containerName&gt;\@&lt;storageAccountName&gt;. blob.core.windows.net*.|
    |${queueName}|Megadja, hogy a feladat a rendszer elküldi a várólista nevét. Használat **alapértelmezett**.|

    Hive-művelet változói

    |Hive-művelet|Leírás|
    |----|----|
    |${hiveDataFolder}|A forráskönyvtár a Hive-tábla létrehozása parancsot.|
    |${hiveOutputFolder}|A kimeneti mappát az FELÜLÍRÁSA INSERT utasítás.|
    |${hiveTableName}|A Hive-tábla, amely hivatkozik a log4j adatfájlok neve.|

    Sqoop művelet változói

    |Műveleti változó sqoop használatával|Leírás|
    |---|---|
    |${sqlDatabaseConnectionString}|SQL-adatbázis kapcsolati karakterláncát.|
    |${sqlDatabaseTableName}|Az Azure SQL database tábla, ahol az adatok lesznek exportálva.|
    |${hiveOutputFolder}|A kimeneti mappát, a Hive-BESZÚRÁSA FELÜLÍRÁSA utasításra vonatkozóan. Ez a Sqoop export (Exportálás-dir) ugyanabban a mappában.|

    Oozie-munkafolyamatokkal, és a munkafolyamat-műveletek használatával kapcsolatos további információkért lásd: [Apache Oozie 4.0 dokumentáció] [ apache-oozie-400] (a HDInsight fürt 3.0-s verzió) vagy [Apache Oozie 3.3.2-dokumentáció ] [ apache-oozie-332] (a HDInsight fürt 2.1-es verzió).

1. Mentse a fájlt az **C:\Tutorials\UseOozie\workflow.xml** ANSI (ASCII) kódolással. (Használja a Jegyzettömb, ha a szöveges szerkesztő nem biztosítja ezt a lehetőséget.)

**Koordinátor meghatározása**

1. Hozzon létre egy szövegfájlt az alábbi tartalommal:

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
            <workflow>
                <app-path>${wfPath}</app-path>
            </workflow>
        </action>
    </coordinator-app>
    ```

    A csomagdefiníciós fájlban lévő használt öt változók vannak:

   | Változó | Leírás |
   | --- | --- |
   | ${coordFrequency} |Feladat szüneteltetési idő. Gyakoriság mindig perc van megadva. |
   | ${coordStart} |Feladat indítási ideje. |
   | ${coordEnd} |A feladat befejezési időpontja. |
   | ${coordTimezone} |Az Oozie coordinator feladatok időzónában rögzített nincs nyári időszámításra (általában jelöli az UTC) az dolgozza fel. Ezt az időzónát nevezik "Oozie feldolgozási időzónáját." |
   | ${wfPath} |A workflow.xml elérési útja.  Ha a munkafolyamat-fájl neve nem az alapértelmezett név (workflow.xml), meg kell adnia. |
2. Mentse a fájlt az **C:\Tutorials\UseOozie\coordinator.xml** kódování ANSI (ASCII) használatával. (Használja a Jegyzettömb, ha a szöveges szerkesztő nem biztosítja ezt a lehetőséget.)

## <a name="deploy-the-oozie-project-and-prepare-the-tutorial"></a>Az Oozie-projekt telepítését, és az oktatóanyag előkészítéséhez
Futtatni fogja az Azure PowerShell-szkript a következő lépések végrehajtásához:

* A HiveQL-parancsfájlt (useoozie.hql) másolása az Azure Blob storage, wasb:///tutorials/useoozie/useoozie.hql.
* Copy workflow.xml to wasb:///tutorials/useoozie/workflow.xml.
* Copy coordinator.xml to wasb:///tutorials/useoozie/coordinator.xml.
* Másolja az adatfájl (/ example/data/sample.log) való wasb:///tutorials/useoozie/data/sample.log.
* Hozzon létre egy Azure SQL-adatbázistáblába, az adatok exportálása a Sqoop tárolására. A tábla neve a *log4jLogCount*.

**HDInsight-storage ismertetése**

HDInsight adatokat tároló Azure Blob storage használ. wasb: / / a Hadoop elosztott fájlrendszer (HDFS) az Azure Blob storage a Microsoft általi implementációja. További információkért lásd: [az Azure Blob storage a HDInsight][hdinsight-storage].

Egy HDInsight-fürt üzembe helyezésekor, egy Azure Blob storage-fiókot és a egy adott tároló ebből a fiókból elsődlegesként lett megjelölve az alapértelmezett fájlrendszer, például HDFS-ben. Ezen tárfiók mellett is hozzáadhat további tárfiókok az Azure-előfizetéshez vagy az Azure-előfizetések a kiépítési folyamat során. További tárfiókok hozzáadásáról útmutatásért lásd: [Provision HDInsight clusters][hdinsight-provision]. A jelen oktatóanyagban használt Azure PowerShell-szkript leegyszerűsítése összes fájlt tárolódnak az alapértelmezett fájl rendszer tárolóban található */oktatóprogramok/useoozie*. Alapértelmezés szerint ez a tároló rendelkezik a HDInsight-fürt neve azonos néven.
A szintaxis a következő:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [!NOTE]  
> Csak a *wasb: / /* szintaxis a HDInsight fürt 3.0-s verzió támogatott. A korábbi *asv: / /* szintaxis HDInsight 2.1-es és 1.6 fürtök támogatott, de nem támogatott a HDInsight 3.0 fürtök.
>
> A wasb: / / a virtuális elérési utat. További információ: [az Azure Blob storage a HDInsight][hdinsight-storage].

Az alapértelmezett fájl rendszer tárolóban tárolt fájl elérhető lesz HDInsight a következő URI-k (használok workflow.xml példaként) egyikének használatával:

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
    wasb:///tutorials/useoozie/workflow.xml
    /tutorials/useoozie/workflow.xml

Ha azt szeretné, a fájl közvetlenül elérhetők a tárfiókban, a blob nevét, a fájl van:

    tutorials/useoozie/workflow.xml

**Hive belső és külső táblák ismertetése**

Néhány dolgot végre kell tudni a Hive-belső és külső táblák:

* A CREATE TABLE parancs létrehoz egy belső tábla, más néven egy felügyelt táblát. Az adatfájl kell működnie az alapértelmezett tároló.
* A CREATE TABLE parancsot a data-fájl áthelyezése/hive/adatraktár/<TableName> az alapértelmezett tároló mappában.
* A CREATE EXTERNAL TABLE parancs létrehoz egy külső táblát. Az adatfájl kívül az alapértelmezett tároló is található.
* A CREATE EXTERNAL TABLE parancs nem helyezi át a fájlt.
* A CREATE EXTERNAL TABLE parancs a mappában, a hely záradékban megadott almappákban nem teszi lehetővé. Ez az az oka, hogy miért az oktatóanyag a sample.log fájlt másolatot készít.

További információkért lásd: [HDInsight: Az Apache Hive belső és külső táblák bevezető][cindygross-hive-tables].

**Az oktatóanyag előkészítéséhez**

1. Nyissa meg a Windows PowerShell ISE-ben (írja be a Windows 8 Start képernyő **PowerShell_ISE**, és kattintson a **Windows PowerShell ISE-ben**. További információkért lásd: [Windows PowerShell indítása a Windows 8 és Windows][powershell-start]).
2. Az alsó ablaktáblában futtassa a következő parancsot az Azure-előfizetéshez csatlakozni:

    ```powershell
    Add-AzureAccount
    ```

    Az Azure-fiók hitelesítő adatainak megadását kéri. Ez az előfizetés-kapcsolat hozzáadása módszer túllépi az időkorlátot, és 12 óra elteltével kell újra futtassa a parancsmagot.

   > [!NOTE]  
   > Ha több Azure-előfizetéssel rendelkezik, és az alapértelmezett előfizetést nem szeretné használni, azt a <strong>Select-AzureSubscription</strong> parancsmagot, hogy válasszon ki egy előfizetést.

3. Másolja a parancsfájl panelen a következő szkriptet, és állítsa az első hat változókat:

    ```powershell
    # WASB variables
    $storageAccountName = "<StorageAccountName>"
    $containerName = "<BlobStorageContainerName>"

    # SQL database variables
    $sqlDatabaseServer = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabaseLoginPassword = "SQLDatabaseLoginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"
    $sqlDatabaseTableName = "log4jLogsCount"

    # Oozie files for the tutorial
    $hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"
    $workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
    $coordDefinition =  "C:\Tutorials\UseOozie\coordinator.xml"

    # WASB folder for storing the Oozie tutorial files.
    $destFolder = "tutorials/useoozie"  # Do NOT use the long path here
    ```

    A változók további leírásáért lásd: a [Előfeltételek](#prerequisites) szakaszt ebben az oktatóanyagban.

4. A parancsfájl a parancsfájl panelen fűzze hozzá a következő:

    ```powershell
    # Create a storage context object
    $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

    function uploadOozieFiles()
    {
        Write-Host "Copy HiveQL script, workflow definition and coordinator definition ..." -ForegroundColor Green
        Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
        Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
        Set-AzureStorageBlobContent -File $coordDefinition -Container $containerName -Blob "$destFolder/coordinator.xml" -Context $destContext
    }

    function prepareHiveDataFile()
    {
        Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
        Start-CopyAzureStorageBlob -SrcContainer $containerName -SrcBlob "example/data/sample.log" -Context $destContext -DestContainer $containerName -destBlob "$destFolder/data/sample.log" -DestContext $destContext
    }

    function prepareSQLDatabase()
    {
        # SQL query string for creating log4jLogsCount table
        $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                [Level] [nvarchar](10) NOT NULL,
                [Total] float,
            CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
            (
            [Level] ASC
            )
            )"

        #Create the log4jLogsCount table
        Write-Host "Create Log4jLogsCount table ..." -ForegroundColor Green
        $conn = New-Object System.Data.SqlClient.SqlConnection
        $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
        $conn.open()
        $cmd = New-Object System.Data.SqlClient.SqlCommand
        $cmd.connection = $conn
        $cmd.commandtext = $cmdCreateLog4jCountTable
        $cmd.executenonquery()

        $conn.close()
    }

    # upload workflow.xml, coordinator.xml, and ooziewf.hql
    uploadOozieFiles;

    # make a copy of example/data/sample.log to example/data/log4j/sample.log
    prepareHiveDataFile;

    # create log4jlogsCount table on SQL database
    prepareSQLDatabase;
    ```

5. Kattintson a **parancsfájl futtatása** vagy nyomja le az **F5** a parancsfájl futtatásához. A kimenet hasonló lesz:

    ![Útmutató előkészítési kimenet][img-preparation-output]

## <a name="run-the-oozie-project"></a>Az Oozie-projekt futtatása
Az Azure PowerShell jelenleg nem biztosít semmilyen parancsmagok Oozie feladatok meghatározása. Használhatja a **Invoke-RestMethod** Oozie webszolgáltatások meghívása parancsmagot. Az Oozie webszolgáltatási API-ra egy HTTP REST API-t JSON. Az Oozie-webszolgáltatások API kapcsolatos további információkért lásd: [Apache Oozie 4.0 dokumentáció] [ apache-oozie-400] (a HDInsight fürt 3.0-s verzió) vagy [Apache Oozie 3.3.2 dokumentáció] [ apache-oozie-332] (a HDInsight fürt 2.1-es verzió).

**Az Oozie-feladatok elküldése**

1. Nyissa meg a Windows PowerShell ISE-ben (a Windows 8 Start képernyő, írja be **PowerShell_ISE**, és kattintson a **Windows PowerShell ISE-ben**. További információkért lásd: [Windows PowerShell indítása a Windows 8 és Windows][powershell-start]).
2. Másolja a parancsfájl panelen a következő szkriptet, és állítsa az első tizennégy változók (azonban kihagyása **$storageUri**).

    ```powershell
    #HDInsight cluster variables
    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterUserPassword>"

    #Azure Blob storage (WASB) variables
    $storageAccountName = "<StorageAccountName>"
    $storageContainerName = "<BlobContainerName>"
    $storageUri="wasb://$storageContainerName@$storageAccountName.blob.core.windows.net"

    #Azure SQL database variables
    $sqlDatabaseServer = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"

    #Oozie WF/coordinator variables
    $coordStart = "2014-03-21T13:45Z"
    $coordEnd = "2014-03-21T13:45Z"
    $coordFrequency = "1440"    # in minutes, 24h x 60m = 1440m
    $coordTimezone = "UTC"    #UTC/GMT

    $oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
    $waitTimeBetweenOozieJobStatusCheck=10

    #Hive action variables
    $hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
    $hiveTableName = "log4jlogs"
    $hiveDataFolder = "$storageUri/tutorials/useoozie/data"
    $hiveOutputFolder = "$storageUri/tutorials/useoozie/output"

    #Sqoop action variables
    $sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServer;password=$sqlDatabaseLoginPassword;database=$sqlDatabaseName"  
    $sqlDatabaseTableName = "log4jLogsCount"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)
    ```

    A változók további leírásáért lásd: a [Előfeltételek](#prerequisites) szakaszt ebben az oktatóanyagban.

    $coordstart $coordend jsou kezdési és befejezési idő a munkafolyamatot. Ismerje meg, az UTC/GMT idő, keresse a "utc idő" a Bing.com oldalon. A $coordFrequency milyen gyakran szeretné futtatni a munkafolyamat percek alatt van.
3. A szkript a következő hozzáfűzése. Ez a rész határozza meg, az Oozie-tartalom:

    ```powershell
    #OoziePayload used for Oozie web service submission
    $OoziePayload =  @"
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
            <name>nameNode</name>
            <value>$storageUrI</value>
        </property>

        <property>
            <name>jobTracker</name>
            <value>jobtrackerhost:9010</value>
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
            <name>oozie.coord.application.path</name>
            <value>$oozieWFPath</value>
        </property>

        <property>
            <name>wfPath</name>
            <value>$oozieWFPath</value>
        </property>

        <property>
            <name>coordStart</name>
            <value>$coordStart</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>$coordEnd</value>
        </property>

        <property>
            <name>coordFrequency</name>
            <value>$coordFrequency</value>
        </property>

        <property>
            <name>coordTimezone</name>
            <value>$coordTimezone</value>
        </property>

        <property>
            <name>hiveScript</name>
            <value>$hiveScript</value>
        </property>

        <property>
            <name>hiveTableName</name>
            <value>$hiveTableName</value>
        </property>

        <property>
            <name>hiveDataFolder</name>
            <value>$hiveDataFolder</value>
        </property>

        <property>
            <name>hiveOutputFolder</name>
            <value>$hiveOutputFolder</value>
        </property>

        <property>
            <name>sqlDatabaseConnectionString</name>
            <value>&quot;$sqlDatabaseConnectionString&quot;</value>
        </property>

        <property>
            <name>sqlDatabaseTableName</name>
            <value>$SQLDatabaseTableName</value>
        </property>

        <property>
            <name>user.name</name>
            <value>admin</value>
        </property>

    </configuration>
    "@
    ```

   > [!NOTE]  
   > Az adatfájlban munkafolyamat küldésének képest jelentős különbség a változó **oozie.coord.application.path**. Amikor egy munkafolyamat-feladat elküldéséhez használhatja **oozie.wf.application.path** helyette.

4. A szkript a következő hozzáfűzése. Ez a rész az Oozie webes szolgáltatás állapotát ellenőrzi:

    ```powershell
    function checkOozieServerStatus()
    {
        Write-Host "Checking Oozie server status..." -ForegroundColor Green
        $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus

        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $oozieServerStatus = $jsonResponse[0].("systemMode")
        Write-Host "Oozie server status is $oozieServerStatus..."

        if($oozieServerStatus -notmatch "NORMAL")
        {
            Write-Host "Oozie server status is $oozieServerStatus...cannot submit Oozie jobs. Check the server status and re-run the job."
        }
    }
    ```

5. A szkript a következő hozzáfűzése. Ez a rész az Oozie-feladatot hoz létre:

    ```powershell
    function createOozieJob()
    {
        # create Oozie job
        Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
        Write-Host "`n--------`n$OoziePayload`n--------"
        $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
        $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName -debug -Verbose

        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $oozieJobId = $jsonResponse[0].("id")
        Write-Host "Oozie job id is $oozieJobId..."

        return $oozieJobId
    }
    ```

   > [!NOTE]  
   > Egy munkafolyamat-feladat elküldésekor egy másik webszolgáltatás hívása indítsa el a feladatot, a feladat létrehozása után el kell végeznie. Ebben az esetben a koordinátor feladat elindul időpontig. A feladat automatikusan elindul.

6. A szkript a következő hozzáfűzése. Ez a rész ellenőrzi az Oozie-feladat állapota:

    ```powershell
    function checkOozieJobStatus($oozieJobId)
    {
        # get job status
        Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
        Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

        Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
        $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $JobStatus = $jsonResponse[0].("status")

        while($JobStatus -notmatch "SUCCEEDED|KILLED")
        {
            Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
            Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $JobStatus = $jsonResponse[0].("status")
        }

        Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!"
        if($JobStatus -notmatch "SUCCEEDED")
        {
            Write-Host "Check logs at http://headnode0:9014/cluster for detais."
        }
    }
    ```

7. (Nem kötelező) A szkript a következő hozzáfűzése.

    ```powershell
    function listOozieJobs()
    {
        Write-Host "Listing Oozie jobs..." -ForegroundColor Green
        $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds

        write-host "Job ID                                   App Name        Status      Started                         Ended"
        write-host "----------------------------------------------------------------------------------------------------------------------------------"
        foreach($job in $response.workflows)
        {
            Write-Host $job.id "`t" $job.appName "`t" $job.status "`t" $job.startTime "`t" $job.endTime
        }
    }

    function ShowOozieJobLog($oozieJobId)
    {
        Write-Host "Showing Oozie job info..." -ForegroundColor Green
        $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/$oozieJobId" + "?show=log"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds
        write-host $response
    }

    function killOozieJob($oozieJobId)
    {
        Write-Host "Killing the Oozie job $oozieJobId..." -ForegroundColor Green
        $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=kill" #Valid values for the 'action' parameter are 'start', 'suspend', 'resume', 'kill', 'dryrun', 'rerun', and 'change'.
        $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders -debug
    }
    ```

8. A parancsfájl fűzze hozzá a következő:

    ```powershell
    checkOozieServerStatus
    # listOozieJobs
    $oozieJobId = createOozieJob($oozieJobId)
    checkOozieJobStatus($oozieJobId)
    # ShowOozieJobLog($oozieJobId)
    # killOozieJob($oozieJobId)
    ```

Távolítsa el a # jelentkezik, ha szeretné futtatni a további funkciók.

1. Ha a HDInsight-fürt 2.1-es verzió, cserélje le a "https://$clusterName.azurehdinsight.net:443/oozie/v2/" a "https://$clusterName.azurehdinsight.net:443/oozie/v1/". HDInsight fürt 2.1-es verziója nem támogatja a 2-es verziója a webes szolgáltatások nem.
1. Kattintson a **parancsfájl futtatása** vagy nyomja le az **F5** a parancsfájl futtatásához. A kimenet hasonló lesz:

    ![Az oktatóanyag futtatásához a munkafolyamat kimenete][img-runworkflow-output]
1. Csatlakozás az SQL Database, az exportált adatok megtekintéséhez.

**Ellenőrizze a feladat-hibanapló**

Egy munkafolyamat hibaelhárítása, az Oozie-naplófájl fürtpéldány C:\apps\dist\oozie-3.3.2.1.3.2.0-05\oozie-win-distro\logs\Oozie.log az átjárócsomóponthoz való. RDP kapcsolatos tudnivalókat lásd: [kezelése az Apache Hadoop-fürtök a HDInsight az Azure portal használatával](hdinsight-administer-use-portal-linux.md).

**Az oktatóanyag újrafuttatása**

Indítsa újra a munkafolyamatot, hajtsa végre a következő feladatokat:

* A Hive-parancsfájl kimeneti fájl törlése.
* Törölje a log4jLogsCount táblában lévő adatokat.

Itt látható egy minta Windows PowerShell-parancsfájlt, amelyet használhat:

```powershell
$storageAccountName = "<AzureStorageAccountName>"
$containerName = "<ContainerName>"

#SQL database variables
$sqlDatabaseServer = "<SQLDatabaseServerName>"
$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
$sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>"
$sqlDatabaseName = "<SQLDatabaseName>"
$sqlDatabaseTableName = "log4jLogsCount"

Write-host "Delete the Hive script output file ..." -ForegroundColor Green
$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $containerName

Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
$conn = New-Object System.Data.SqlClient.SqlConnection
$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
$conn.open()
$cmd = New-Object System.Data.SqlClient.SqlCommand
$cmd.connection = $conn
$cmd.commandtext = "delete from $sqlDatabaseTableName"
$cmd.executenonquery()

$conn.close()
```

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtudhatta, hogyan adhat meg, az Oozie-munkafolyamatokkal, és az Oozie-koordinátor, és az Oozie-koordinátor feladatok futtatása az Azure PowerShell-lel. További tudnivalókért tekintse meg a következő cikkeket:

* [HDInsight – első lépések][hdinsight-get-started]
* [Az Azure Blob storage használata a HDInsight][hdinsight-storage]
* [HDInsight felügyelete az Azure PowerShell használatával][hdinsight-admin-powershell]
* [Adatok feltöltése a HDInsightba][hdinsight-upload-data]
* [A HDInsight Apache Sqoop használata][hdinsight-use-sqoop]
* [Az Apache Hive használata a HDInsight][hdinsight-use-hive]
* [Az Apache Pig használata a HDInsight][hdinsight-use-pig]
* [Java MapReduce programok fejlesztése a HDInsight][hdinsight-develop-java-mapreduce]

[hdinsight-cmdlets-download]: https://go.microsoft.com/fwlink/?LinkID=325563

[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-develop-java-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md

[sqldatabase-get-started]: ../sql-database/sql-database-get-started.md

[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md

[apache-hadoop]: https://hadoop.apache.org/
[apache-oozie-400]: https://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: https://oozie.apache.org/docs/3.3.2/

[powershell-download]: https://azure.microsoft.com/downloads/
[powershell-about-profiles]: https://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: https://docs.microsoft.com/powershell/scripting/setup/starting-windows-powershell?view=powershell-6
[powershell-script]: https://technet.microsoft.com/library/ee176949.aspx

[cindygross-hive-tables]: https://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.RunCoord.Output.png

[technetwiki-hive-error]: https://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
