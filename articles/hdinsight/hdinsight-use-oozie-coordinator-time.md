---
title: Hdinsight Hadoop Oozie coordinator időalapú használata |} Microsoft Docs
description: Időalapú Hadoop Oozie-koordinátor használata a Hdinsightban, big data-szolgáltatása. Megtudhatja, hogyan Oozie munkafolyamatok és a koordinátor és a feladatok elküldéséhez.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 00c3a395-d51a-44ff-af2d-1f116c4b1c83
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 10/04/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: c5819d39bf3ab7c0f4af32171aadea56e4f6a241
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063527"
---
# <a name="use-time-based-oozie-coordinator-with-hadoop-in-hdinsight-to-define-workflows-and-coordinate-jobs"></a>A HDInsight Hadoop határozza meg a munkafolyamatok és feladatok időalapú Oozie-koordinátor használata
Ebből a cikkből megtudhatja, hogyan adhat meg a munkafolyamatok és a koordinátor, és hogyan indítható el, a koordinátor feladatok, ideje alapján. Hasznos lehet, hogy a [és a HDInsight együttes használata Oozie] [ hdinsight-use-oozie] Ez a cikk olvasása előtt. Oozie, mellett is ütemezheti az Azure Data Factory használatával feladatok. Azure Data Factory kapcsolatban [használja a Pig és a Data Factory Hive](../data-factory/transform-data.md).

> [!NOTE]
> Ez a cikk egy Windows-alapú HDInsight-fürt szükséges. Oozie, többek között a feladatok időalapú egy Linux-alapú fürtön használatáról lásd: [hadooppal határozza meg, és egy munkafolyamat futtatása a Linux-alapú HDInsight használata Oozie](hdinsight-use-oozie-linux-mac.md)

## <a name="what-is-oozie"></a>Mi az az Oozie
Apache Oozie egy munkafolyamat/koordinációs rendszer, amely a Hadoop-feladatokat kezeli. Integrálva van a Hadoop-veremmel, és támogatja a Hadoop-feladatokat Apache MapReduce, Apache Pig, Apache Hive és Apache Sqoop. Is használható, például Java programok vagy héjparancsfájlok ütemezésére rendszerspecifikus feladatok ütemezése.

A következő kép bemutatja a munkafolyamat fogja végrehajtani:

![Munkafolyamat diagramja][img-workflow-diagram]

A munkafolyamat két műveletet tartalmaz:

1. A Hive művelet a log4j naplófájl naplózási szintű típusonkénti előfordulások megszámlálásához HiveQL parancsfájlt futtatja. A mezők típusát és súlyosságát, például megjelenítése [NAPLÓZÁSI szint] mező tartalmazó sor minden log4j napló foglalja magában:

        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...

    A Hive parancsfájl kimenete hasonló:

        [DEBUG] 434
        [ERROR] 3
        [FATAL] 1
        [INFO]  96
        [TRACE] 816
        [WARN]  4

    További információ a Hive-ról: [A Hive használata a HDInsightban][hdinsight-use-hive].
2. A Sqoop művelet exportálja a HiveQL műveleti kimenet egy Azure SQL adatbázis egyik táblája. További információ a Sqoop: [és a HDInsight együttes használata Sqoop][hdinsight-use-sqoop].

> [!NOTE]
> Tekintse meg a HDInsight-fürtökön támogatott Oozie verziók [What's new in HDInsight által biztosított fürt verziók?] [hdinsight-versions].
>
>

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Munkaállomás Azure PowerShell-lel**.

    > [!IMPORTANT]
    > A HDInsight-erőforrások Azure Service Managerrel történő kezelésének Azure PowerShell-támogatása **elavult**, így 2017. január 1-től megszűnik. A jelen dokumentumban leírt lépések az új HDInsight-parancsmagokat használják, amelyek az Azure Resource Managerrel működnek.
    >
    > Kérjük, kövesse az alábbi cikkben leírt lépéseket az Azure PowerShell legújabb verziójának telepítéséhez: [Install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs) (Az Azure PowerShell letöltése és konfigurálása). Ha vannak olyan parancsprogramjai, amelyeket módosítani kell az új, az Azure Resource Managerrel működő parancsmagok használatához, tekintse meg az alábbi cikket: [Migrating to Azure Resource Manager-based development tools for HDInsight clusters](hdinsight-hadoop-development-using-azure-resource-manager.md) (Az Azure Resource Manager-alapú fejlesztési eszközökre való áttérés HDInsight-fürtök esetén).

* **HDInsight-fürtök**. HDInsight-fürtök létrehozásával kapcsolatos további információkért lásd: [HDInsight-fürtök létrehozása][hdinsight-provision], vagy [első lépései a hdinsight eszközzel][hdinsight-get-started]. Szüksége lesz az oktatóanyag végrehajtania az alábbi adatokat:

    <table border = "1">
    <tr><th>Fürt tulajdonság</th><th>A Windows PowerShell-változó neve</th><th>Érték</th><th>Leírás</th></tr>
    <tr><td>A HDInsight-fürt neve</td><td>$clusterName</td><td></td><td>A HDInsight-fürt amelyre ebben az oktatóanyagban fog futni.</td></tr>
    <tr><td>A HDInsight fürt felhasználónév</td><td>$clusterUsername</td><td></td><td>A HDInsight fürt felhasználó felhasználóneve. </td></tr>
    <tr><td>A HDInsight fürt felhasználói jelszó </td><td>$clusterPassword</td><td></td><td>A HDInsight fürt felhasználói jelszavát.</td></tr>
    <tr><td>Az Azure storage-fiók neve</td><td>$storageAccountName</td><td></td><td>Egy Azure Storage-fiók érhető el a HDInsight-fürthöz. A jelen oktatóanyag esetében használja az alapértelmezett tárfiókot, amely a fürt létesítése során megadott.</td></tr>
    <tr><td>Az Azure Blob-tároló neve</td><td>$containerName</td><td></td><td>Ebben a példában az Azure Blob storage tárolót a HDInsight fürt alapértelmezett fájlrendszer használt használja. Alapértelmezés szerint rendelkezik a neve megegyezik a HDInsight-fürthöz.</td></tr>
    </table>

* **Azure SQL-adatbázis**. Konfigurálnia kell egy tűzfalszabályt a SQL Database-kiszolgálóhoz való hozzáférést a munkaállomáson. Egy Azure SQL-adatbázis létrehozása, és a tűzfalon konfigurálásával kapcsolatos útmutatásért lásd: [Azure SQL-adatbázis használatának első][sqldatabase-get-started]. A cikkben egy Windows PowerShell-parancsfájlt, amelyekre szüksége van az oktatóanyag az Azure SQL-adatbázistáblában szereplő létrehozásához.

    <table border = "1">
    <tr><th>SQL-adatbázis tulajdonság</th><th>A Windows PowerShell-változó neve</th><th>Érték</th><th>Leírás</th></tr>
    <tr><td>SQL adatbázis-kiszolgáló neve</td><td>$sqlDatabaseServer</td><td></td><td>Az SQL adatbázis-kiszolgáló, amelyhez Sqoop exportálja az adatokat. </td></tr>
    <tr><td>SQL adatbázis-bejelentkezési név</td><td>$sqlDatabaseLogin</td><td></td><td>SQL adatbázis-bejelentkezési név.</td></tr>
    <tr><td>SQL-adatbázis a bejelentkezés jelszó</td><td>$sqlDatabaseLoginPassword</td><td></td><td>SQL-adatbázis bejelentkezési jelszót.</td></tr>
    <tr><td>SQL-adatbázis neve</td><td>$sqlDatabaseName</td><td></td><td>Az Azure SQL-adatbázis, amelyhez Sqoop exportálja az adatokat. </td></tr>
    </table>

  > [!NOTE]
  > Alapértelmezés szerint az Azure SQL database az Azure-szolgáltatások, például az Azure HDInsight engedélyezi a csatlakozást. Ha a tűzfal beállítás nincs engedélyezve, engedélyeznie kell azt az Azure portálról. Az utasítás SQL-adatbázis létrehozása és a tűzfal-szabályok konfigurálása, lásd: [létrehozása és konfigurálása az SQL-adatbázis][sqldatabase-get-started].

> [!NOTE]
> Kitöltés a táblában az értékeket. Hasznos lehet az oktatóanyag lépéseinek lesz.

## <a name="define-oozie-workflow-and-the-related-hiveql-script"></a>Oozie munkafolyamat és a kapcsolódó HiveQL-parancsfájlt
Oozie munkafolyamatok definíciók hPDL (az XML folyamat definition language) nyelven íródtak. Az alapértelmezett munkafolyamat Fájlnév *workflow.xml*.  Mentse helyileg a munkafolyamat-fájlt fog, és ezután telepítse a HDInsight-fürthöz az oktatóanyag későbbi részében Azure PowerShell használatával.

A munkafolyamat Hive műveletét meghívja a HiveQL-parancsfájlt. A parancsfájl három HiveQL utasításokat tartalmazza:

1. **A DROP TABLE utasítás** törli a log4j Hive táblát, ha van ilyen.
2. **A CREATE TABLE utasítás** táblázatot hoz létre log4j Hive külső, amelyek a helyet a log4j naplófájl;
3. **A log4j naplófájl helye**. A mező határoló ",". Az alapértelmezett sor elválasztó karaktere "\n". A Hive külső tábla elkerülése érdekében az adatfájl távolít el az eredeti helyre, abban az esetben, ha szeretné futtatni az Oozie munkafolyamat többször szolgál.
4. **Az INSERT FELÜLÍRÁSA utasítás** számát a log4j Hive táblát, és a naplózási szint típusonkénti előfordulását menti a kimenetet, egy Azure Blob storage-helyre.

> [!NOTE]
> Nincs olyan ismert Hive útvonallal kapcsolatos probléma. Ha egy Oozie feladat elküldése elindul ezt a problémát. A TechNet Wikin található az utasításokat a probléma kijavítása: [HDInsight Hive-hiba: nem lehet átnevezni][technetwiki-hive-error].

**A HiveQL parancsfájlt kell a munkafolyamat által meghívott meghatározása**

1. Hozzon létre egy szövegfájlt az alábbi tartalommal:

        DROP TABLE ${hiveTableName};
        CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
        INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

    Három változókat a parancsfájl használatban van:

   * ${hiveTableName}
   * ${hiveDataFolder}
   * ${hiveOutputFolder}

     A munkafolyamat-definíciós fájl (ebben az oktatóanyagban workflow.xml) továbbítani fogja ezeket az értékeket a HiveQL-parancsfájlt a futási időben.
2. Mentse a fájlt **C:\Tutorials\UseOozie\useooziewf.hql** ANSI (ASCII) kódolással. (Használja a Jegyzettömböt, ha a szövegszerkesztőben nem adja meg ezt a lehetőséget.) A parancsfájl az oktatóanyag későbbi részében telepíti a HDInsight-fürthöz.

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

    Nincsenek a munkafolyamatban meghatározott két műveletet. A start művelet *RunHiveScript*. Ha a művelet a futás *OK*, a következő művelet *RunSqoopExport*.

    A RunHiveScript több változót tartalmaz. Az értékeket fogja továbbítani, amikor az Oozie-feladatot a munkaállomáson Azure PowerShell használatával.

    Munkafolyamat-változók

    <table border = "1">
    <tr><th>Munkafolyamat-változók</th><th>Leírás</th></tr>
    <tr><td>${jobTracker}</td><td>Adja meg a Hadoop-feladat követő URL-CÍMÉT. Használjon <strong>jobtrackerhost:9010</strong> a HDInsight fürt 3.0-s és 2.0-s verzióját.</td></tr>
    <tr><td>${nameNode}</td><td>Adja meg a Hadoop neve csomópont URL-CÍMÉT. Használja az alapértelmezett fájl rendszer wasb: / / címmel, például <i>wasb: / /&lt;containerName&gt;@&lt;storageAccountName&gt;. blob.core.windows.net</i>.</td></tr>
    <tr><td>${queueName}</td><td>A feladat elküldve a várólista nevét adja meg. Használjon <strong>alapértelmezett</strong>.</td></tr>
    </table>

    Hive művelet változói

    <table border = "1">
    <tr><th>Műveleti változó struktúra</th><th>Leírás</th></tr>
    <tr><td>${hiveDataFolder}</td><td>A Hive Create Table parancs forráskönyvtárat.</td></tr>
    <tr><td>${hiveOutputFolder}</td><td>A kimeneti mappa az FELÜLÍRÁSA INSERT utasítás.</td></tr>
    <tr><td>${hiveTableName}</td><td>A Hive táblát, amely hivatkozik a log4j adatfájlok neve.</td></tr>
    </table>

    Sqoop művelet változói

    <table border = "1">
    <tr><th>Sqoop művelet változó</th><th>Leírás</th></tr>
    <tr><td>${sqlDatabaseConnectionString}</td><td>SQL adatbázis-kapcsolati karakterlánc.</td></tr>
    <tr><td>${sqlDatabaseTableName}</td><td>Az Azure SQL adatbázis tábla hol lesznek exportálva az adatokat.</td></tr>
    <tr><td>${hiveOutputFolder}</td><td>A kimeneti mappa a Hive BESZÚRÁSA FELÜLÍRÁSA utasítás. Ez a Sqoop export (Exportálás-dir) ugyanabban a mappában.</td></tr>
    </table>

    Oozie munkafolyamat és a munkafolyamat-műveleteket használatával kapcsolatos további információkért lásd: [Apache Oozie 4.0 dokumentáció] [ apache-oozie-400] (a HDInsight-fürt verziószáma 3.0) vagy [Apache Oozie 3.3.2 dokumentáció ] [ apache-oozie-332] (a HDInsight-fürt verziószáma 2.1-es).

1. Mentse a fájlt **C:\Tutorials\UseOozie\workflow.xml** ANSI (ASCII) kódolással. (Használja a Jegyzettömböt, ha a szövegszerkesztőben nem adja meg ezt a lehetőséget.)

**A koordinátor meghatározása**

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

    Nincsenek a szolgáltatásdefiníciós fájlban használt öt változók:

   | Változó | Leírás |
   | --- | --- |
   | ${coordFrequency} |A feladat felfüggesztése időpontja. Gyakoriság mindig kifejezett perc múlva. |
   | ${coordStart} |A feladat kezdési időpontja. |
   | ${coordEnd} |A feladat befejezési időpontja. |
   | ${coordTimezone} |Oozie coordinator feladatok rögzített időzónában dolgozza nem nyári időszámítás (általában képviseli UTC használatával). Ez az időzóna kezeli a "Oozie feldolgozási időzónában." |
   | ${wfPath} |A workflow.xml elérési útját.  Ha a munkafolyamat-fájl neve nem az alapértelmezett név (workflow.xml), meg kell adnia. |
2. Mentse a fájlt **C:\Tutorials\UseOozie\coordinator.xml** a ANSI (ASCII) kódolással. (Használja a Jegyzettömböt, ha a szövegszerkesztőben nem adja meg ezt a lehetőséget.)

## <a name="deploy-the-oozie-project-and-prepare-the-tutorial"></a>Telepítse az Oozie-projektet, és az oktatóanyag előkészítéséhez
Egy Azure PowerShell-parancsfájlt, hogy a következő lesz futtatva:

* Másolja a HiveQL-parancsfájlt (useoozie.hql) az Azure Blob storage wasb:///tutorials/useoozie/useoozie.hql.
* Másolja a workflow.xml wasb:///tutorials/useoozie/workflow.xml.
* Másolja a coordinator.xml wasb:///tutorials/useoozie/coordinator.xml.
* Másolja az adatokat (/ example/data/sample.log) wasb:///tutorials/useoozie/data/sample.log számára.
* Hozzon létre egy Azure SQL-adatbázistáblában szereplő Sqoop exportálási adatainak tárolásához. A táblázat neve *log4jLogCount*.

**HDInsight tároló ismertetése**

HDInsight Azure Blob storage-tárolására használ. wasb: / / a Hadoop elosztott fájlrendszer (HDFS) az Azure Blob storage a Microsoft általi implementációja. További információkért lásd: [használata Azure Blob storage a hdinsight eszközzel][hdinsight-storage].

HDInsight-fürtök kiépítése, amikor egy Azure Blob storage-fiókot és egy adott tárolóhoz ebből a fiókból van kijelölve az alapértelmezett fájlrendszer, például a HDFS-ben. Ezen tárfiók mellett is hozzáadhat további tárfiókokat az azonos Azure-előfizetés vagy az Azure-előfizetések a telepítési folyamat során. További tárfiókok hozzáadásáról útmutatásért lásd: [Provision HDInsight clusters][hdinsight-provision]. Ebben az oktatóanyagban használt Azure PowerShell-parancsfájl leegyszerűsítése összes fájlt tárolja az alapértelmezett fájl rendszer tárolóban található */oktatóanyagok/useoozie*. Alapértelmezés szerint ez a tároló neve megegyezik a HDInsight-fürt neve van.
A szintaxis a következő:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [!NOTE]
> Csak a *wasb: / /* szintaxis a HDInsight fürt 3.0-s verziója támogatott. A régebbi *asv: / /* HDInsight 2.1-es és 1.6 fürtök a szintaxis támogatott, de nem támogatott a HDInsight 3.0 fürtök.
>
> A wasb: / / elérési út egy virtuális elérési út. További információ: [használata Azure Blob storage a hdinsight eszközzel][hdinsight-storage].

Az alapértelmezett fájl rendszer tárolóban tárolt fájlt érhetők el a HDInsight-ból a következő URI-azonosítók (használok workflow.xml példaként) egyikével sem:

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
    wasb:///tutorials/useoozie/workflow.xml
    /tutorials/useoozie/workflow.xml

Ha azt szeretné, a fájlt közvetlenül elérje a tárfiókot, a fájl a blob neve van:

    tutorials/useoozie/workflow.xml

**Hive belső és külső táblák ismertetése**

Néhány dolgot Hive belső és külső táblák ismernie kell:

* A CREATE TABLE parancs létrehoz egy belső tábla, más néven a felügyelt. Az adatfájlban az alapértelmezett tárolóban kell lennie.
* A CREATE TABLE parancs áthelyezi az adatfájl/hive/adatraktár/<TableName> az alapértelmezett tároló mappát.
* A külső tábla létrehozása parancs létrehoz egy külső táblát. Az adatfájlban az alapértelmezett tároló kívül is kell elhelyezni.
* A külső tábla létrehozása parancs nem helyezi át az adatfájlban.
* A külső tábla létrehozása parancs a mappában, a hely záradékban megadott almappákban nem engedélyezi. Ez az az oka, miért az oktatóanyag a sample.log fájl másolatot készít.

További információkért lásd: [HDInsight: Hive belső és külső táblák bevezetés][cindygross-hive-tables].

**Az oktatóanyag előkészítéséhez**

1. Nyissa meg a Windows PowerShell ISE (írja be a Windows 8 kezdőképernyőn **PowerShell_ISE**, és kattintson a **Windows PowerShell ISE**. További információkért lásd: [Windows PowerShell elindítása a Windows 8 és Windows][powershell-start]).
2. Az alsó ablaktáblában futtassa az Azure-előfizetéssel kapcsolódni a következő parancsot:

    ```powershell
    Add-AzureAccount
    ```

    Az Azure-fiók hitelesítő adatainak megadását kéri. Ez a módszer az előfizetés-kapcsolat hozzáadása túllépi az időkorlátot, és 12 óra elteltével kell futtassa újra a parancsmagot.

   > [!NOTE]
   > Ha több Azure-előfizetéssel rendelkezik, és az alapértelmezett előfizetés nem szeretné használni, azt a <strong>válasszon-AzureSubscription</strong> parancsmagot, hogy válasszon egy előfizetést.

3. Másolja a következő a parancsfájl ablaktáblára, és utána állítsa be az első hat változók:

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

    A változók további leírását lásd: a [Előfeltételek](#prerequisites) ebben az oktatóanyagban szakasz.

4. A következő hozzáfűzése a parancsfájl a parancsfájl panelen:

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

5. Kattintson a **-parancsfájl futtatása** vagy nyomja le az ENTER **F5** a parancsfájl futtatásához. A kimeneti hasonló lesz:

    ![Útmutató előkészítési kimeneti][img-preparation-output]

## <a name="run-the-oozie-project"></a>Futtassa az Oozie-projektet
Az Azure PowerShell jelenleg nem biztosít semmilyen parancsmagok Oozie feladatok meghatározásához. Használhatja a **Invoke-RestMethod** parancsmag Oozie webszolgáltatás meghívására. Az Oozie webszolgáltatási API-ra egy HTTP REST API-t JSON. Az Oozie-webszolgáltatások API kapcsolatos további információkért lásd: [Apache Oozie 4.0 dokumentáció] [ apache-oozie-400] (a HDInsight-fürt verziószáma 3.0) vagy [Apache Oozie 3.3.2 dokumentáció] [ apache-oozie-332] (a HDInsight-fürt verziószáma 2.1-es).

**Elküldeni egy Oozie feladatot**

1. Nyissa meg a Windows PowerShell ISE (a Windows 8 kezdőképernyőn írja be **PowerShell_ISE**, és kattintson a **Windows PowerShell ISE**. További információkért lásd: [Windows PowerShell elindítása a Windows 8 és Windows][powershell-start]).
2. Másolja a következő a parancsfájl ablaktáblára, és utána állítsa be az első 14 változók (azonban kihagyása **$storageUri**).

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

    A változók további leírását lásd: a [Előfeltételek](#prerequisites) ebben az oktatóanyagban szakasz.

    $coordstart és $coordend a munkafolyamat kezdési és befejezési idő. Az UTC/GMT szerinti idő további tudnivalókért keresse "UTC szerinti idő" bing.com. A $coordFrequency milyen gyakran szeretné futtatni a munkafolyamat percben van.
3. A következő hozzáfűzése a parancsfájlt. Ez a kijelző meghatározza, hogy az Oozie-tartalom:

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
   > A fő különbség az adatfájlban munkafolyamat küldésének képest a változó **oozie.coord.application.path**. Amikor egy munkafolyamat-feladat elküldéséhez használhatja **oozie.wf.application.path** helyette.

4. A következő hozzáfűzése a parancsfájlt. Ez a kijelző Oozie webes szolgáltatás állapotát ellenőrzi:

    ```powershell
    function checkOozieServerStatus()
    {
        Write-Host "Checking Oozie server status..." -ForegroundColor Green
        $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus

        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $oozieServerSatus = $jsonResponse[0].("systemMode")
        Write-Host "Oozie server status is $oozieServerSatus..."

        if($oozieServerSatus -notmatch "NORMAL")
        {
            Write-Host "Oozie server status is $oozieServerSatus...cannot submit Oozie jobs. Check the server status and re-run the job."
        }
    }
    ```

5. A következő hozzáfűzése a parancsfájlt. Ez a kijelző egy Oozie feladat létrehozása:

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
   > Amikor egy munkafolyamat-feladat, meg kell nyitnia egy másik webszolgáltatás-hívás elindítja a feladatot, a feladat létrehozása után. Ebben az esetben a koordinátor feladat idő váltja ki. A feladat automatikusan elindul.

6. A következő hozzáfűzése a parancsfájlt. Ez a kijelző Oozie feladat állapotát ellenőrzi:

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

7. (Választható) A következő hozzáfűzése a parancsfájlt.

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

8. A következő hozzáfűzése a parancsfájlt:

    ```powershell
    checkOozieServerStatus
    # listOozieJobs
    $oozieJobId = createOozieJob($oozieJobId)
    checkOozieJobStatus($oozieJobId)
    # ShowOozieJobLog($oozieJobId)
    # killOozieJob($oozieJobId)
    ```

Távolítsa el a # jelet, ha a további funkciók futtatni szeretné.

9. Ha a HDinsight-fürt 2.1-es verziója, cserélje le a "https://$clusterName.azurehdinsight.net:443/oozie/v2/" a "https://$clusterName.azurehdinsight.net:443/oozie/v1/". HDInsight-fürt verziószáma 2.1 nem támogatja a 2-es verziójának a webszolgáltatások végzi.
10. Kattintson a **-parancsfájl futtatása** vagy nyomja le az ENTER **F5** a parancsfájl futtatásához. A kimeneti hasonló lesz:

     ![Útmutató munkafolyamat kimeneti futtatása][img-runworkflow-output]
11. Csatlakozás az SQL-adatbázis az exportált adatok megtekintéséhez.

**A feladat hibanapló ellenőrzése**

Egy munkafolyamat elhárításához a Oozie naplófájl helyen találhatók C:\apps\dist\oozie-3.3.2.1.3.2.0-05\oozie-win-distro\logs\Oozie.log a a fürt headnode. Az RDP információkért lásd: [felügyelete HDInsight-fürtök az Azure portál használatával][hdinsight-admin-portal].

**Az oktatóanyag újrafuttatása**

A munkafolyamat újrafuttatásához, a következő feladatokat kell elvégeznie:

* A Hive parancsfájl kimeneti fájl törlése.
* Törölje a log4jLogsCount tábla.

Íme egy minta Windows PowerShell-parancsfájl használható:

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
Ebben az oktatóanyagban megtanulta, hogyan adhat meg, az Oozie munkafolyamat és az Oozie-koordinátor és az Oozie-koordinátor feladat futtatása az Azure PowerShell használatával. További tudnivalókért tekintse meg a következő cikkeket:

* [Első lépései a hdinsight eszközzel][hdinsight-get-started]
* [Használhat Azure Blob Storage tárolót a hdinsight eszközzel][hdinsight-storage]
* [HDInsight felügyelete az Azure PowerShell használatával][hdinsight-admin-powershell]
* [Adatok feltöltése a HDInsightba][hdinsight-upload-data]
* [A Sqoop használata a HDInsightban][hdinsight-use-sqoop]
* [A Hive használata a HDInsightban][hdinsight-use-hive]
* [A Pig használata a HDInsightban][hdinsight-use-pig]
* [Java-MapReduce programok fejlesztése a HDInsight][hdinsight-develop-java-mapreduce]

[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563

[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md

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

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: https://docs.microsoft.com/en-us/powershell/scripting/setup/starting-windows-powershell?view=powershell-6
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.RunCoord.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
