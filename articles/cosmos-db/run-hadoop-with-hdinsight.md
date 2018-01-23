---
title: "Egy Azure Cosmos DB és a HDInsight Hadoop-feladat futtatása |} Microsoft Docs"
description: "Útmutató: Azure Cosmos adatbázis és az Azure HDInsight Hive, Pig és MapReduce egyszerű feladat futtatása."
services: cosmos-db
author: dennyglee
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 06f0ea9d-07cb-4593-a9c5-ab912b62ac42
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 01/19/2018
ms.author: denlee
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 181954e4657166db8aa94021ad093437d8c7abfd
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="Azure Cosmos DB-HDInsight"></a>Egy Azure Cosmos DB és HDInsight Apache Hive, Pig vagy Hadoop feladat futtatása
Az oktatóanyag bemutatja, hogyan futtathat [Apache Hive][apache-hive], [Apache Pig][apache-pig], és [Apache Hadoop] [ apache-hadoop] MapReduce-feladatok on Azure HDInsight Cosmos DB Hadoop-összekötővel együtt. Cosmos DB Hadoop összekötő lehetővé teszi, hogy a forrás és a Hive, Pig és MapReduce-feladatok fogadó Cosmos DB. Ez az oktatóanyag fog használni Cosmos DB adatforrás mind az átadó a Hadoop-feladatokat.

> [!IMPORTANT] 
> A Spark on Azure Cosmos DB-összekötőhöz a lehetőség ajánlott Azure HDInsight Azure Cosmos DB való csatlakozáshoz. További információkért lásd: [egyre gyorsabban jelennek meg valós idejű big data elemzések a Spark az Azure Cosmos DB összekötőre](spark-connector.md).

Ez az oktatóanyag befejezése után képes lesz a következő kérdések megválaszolásához:

* Hogyan betölteni az adatokat a Hive, Pig vagy MapReduce feladat használatával Cosmos-Adatbázisból?
* Hogyan adat tárolása Cosmos DB használatával a Hive, Pig vagy MapReduce feladatot?

Azt javasoljuk, hogy Kezdésként tekintse meg az alábbi videót, ahol azt végigfuttatása egy Cosmos-adatbázis és a HDInsight Hive-feladatot.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Use-Azure-DocumentDB-Hadoop-Connector-with-Azure-HDInsight/player]
>
>

Ezt követően térjen vissza a cikkhez, ahol fog kapni a teljes adatait, hogyan futtathat analytics-feladatok az Cosmos DB adatokon.

> [!TIP]
> Ez az oktatóanyag feltételezi, hogy rendelkezik-e előzetes tapasztalata az Apache Hadoop, a Hive és/vagy a Pig használatával kapcsolatban. Ha most ismerkedik az Apache Hadoop, a Hive és a Pig, ajánlott felkeresi a [Apache Hadoop-dokumentáció][apache-hadoop-doc]. Ez az oktatóanyag azt is feltételezi, hogy rendelkezik tapasztalattal a Cosmos DB, és Cosmos DB fiókkal rendelkezik. Ha még nem ismeri a Cosmos DB, vagy Ön nem rendelkezik egy Cosmos-DB-fiókot, vegye ki a [bevezetés] [ getting-started] lap.
>
>

Nincs ideje elvégezni az oktatóanyagot, és csak szeretné, hogy a teljes minta PowerShell-parancsfájlok a Hive, Pig és MapReduce? Nem probléma, azokat [Itt][hdinsight-samples]. A letöltés a következő mintákat hql, a pig és a java fájljait is tartalmazza.

## <a name="NewestVersion"></a>Legújabb verziója
<table border='1'>
    <tr><th>Hadoop Connector ezen verziója</th>
        <td>1.2.0</td></tr>
    <tr><th>Parancsfájl URI azonosítója</th>
        <td>https://portalcontent.blob.core.windows.net/scriptaction/documentdb-hadoop-installer-v04.ps1</td></tr>
    <tr><th>Módosítás dátuma</th>
        <td>04/26/2016</td></tr>
    <tr><th>Támogatott HDInsight-verziókról</th>
        <td>3.1, 3.2</td></tr>
    <tr><th>Módosítási napló</th>
        <td>A frissített Azure Cosmos DB Java SDK használatával 1.6.0</br>
            A forrás-és a fogadó particionált gyűjtemények támogatása</br>
        </td></tr>
</table>

## <a name="Prerequisites"></a>Előfeltételek
Ez az oktatóanyag utasításainak követése, előtt ellenőrizze, hogy a következő:

* Egy Cosmos-DB-fiók, adatbázis és a dokumentumok egy gyűjtemény. További információkért lásd: [Ismerkedés a Cosmos DB][getting-started]. Minta adatok importálása Cosmos DB fiókját a [Cosmos DB import eszközt][import-data].
* Átviteli sebesség. Beolvassa és a HDInsight-ból írások beleszámítanak a meghatározott időn belül kérelemegység a gyűjteményekben felé.
* Egyes belül egy további tárolt eljárást a kapacitás kimeneti gyűjtemény. A tárolt eljárások az eredményül kapott dokumentumok átvitele használnak.
* Az eredményül kapott dokumentumokat a Hive, Pig vagy MapReduce feladatokból kapacitása.
* [*Nem kötelező*] kapacitásának meghatározása egy további gyűjteményt.

> [!WARNING]
> A feladatok az új gyűjtemény létrehozása elkerülése érdekében az eredményeket a stdout nyomtatása, kimenetét mentse a WASB tárolóhoz, vagy adjon meg egy már létező gyűjteményt. Esetén adja meg egy meglévő gyűjteményt, új dokumentumok a gyűjtemény belül jön létre, és már meglévő dokumentumokat csak az érintett ütközése esetén *azonosítók*. **Az összekötő automatikusan felülírja meglévő dokumentumok ütközését**. Ez a szolgáltatás kikapcsolása az upsert beállítást FALSE értékre állítását. Ha upsert értéke false, és ütközés lép fel, a Hadoop-feladat meghiúsul; egy azonosító ütközés hibát jelez.
>
>

## <a name="ProvisionHDInsight"></a>1. lépés:, Hozzon létre egy új HDInsight-fürt
Ez az oktatóanyag az Azure portálról parancsfájlművelet használatával testre szabhatja a HDInsight-fürthöz. Ebben az oktatóanyagban a HDInsight-fürt létrehozása az Azure portál használjuk. PowerShell-parancsmagokkal vagy a HDInsight .NET SDK használatával útmutatásért tekintse meg a [testreszabása HDInsight-fürtök használata parancsfájlművelet] [ hdinsight-custom-provision] cikk.

1. Jelentkezzen be a [Azure-portálon][azure-portal].
2. Kattintson a **+ új** tetején a bal oldali navigációs keressen **HDInsight** a felső keresősávban az új panelen.
3. **HDInsight** által közzétett **Microsoft** az eredményeket a lap tetején fog megjelenni. Kattintson rá, és kattintson a **létrehozása**.
4. Az új HDInsight-fürt létrehozása a panelen, adja meg a **fürtnév** válassza ki a **előfizetés** ki kívánja építeni a ehhez az erőforráshoz.

    <table border='1'>
        <tr><td>Fürt neve</td><td>A fürt nevét.<br/>
DNS-beli név kell elindítani egy alfanumerikus karakterrel végződik és kötőjeleket tartalmazhat.<br/>
A mező 3 és 63 karakter közötti karakterláncnak kell lennie.</td></tr>
        <tr><td>Előfizetés neve</td>
            <td>Ha több Azure-előfizetéssel rendelkezik, válassza ki az előfizetést, amely a HDInsight-fürt üzemelteti. </td></tr>
    </table>
5.Kattintson a **fürt típusának kiválasztása** és állítsa be a következő tulajdonságokat a megadott értékre.

    <table border='1'>
        <tr><td>Fürttípus</td><td><strong>Hadoop</strong></td></tr>
        <tr><td>Fürt réteg</td><td><strong>Standard</strong></td></tr>
        <tr><td>Operációs rendszer</td><td><strong>Windows</strong></td></tr>
        <tr><td>Verzió</td><td>legújabb verzió</td></tr>
    </table>

    Most kattintson **válasszon**.

    ![Adja meg a Hadoop HDInsight a fürtcsomópontok kezdeti adatait][image-customprovision-page1]
6. Kattintson a **hitelesítő adatok** a bejelentkezési és távelérési hitelesítő adatainak beállításához. Válassza ki a **a fürt bejelentkezési felhasználónevének** és **a fürt bejelentkezési jelszó**.

    Ha a távoli a fürtbe, jelölje be *Igen* a panel alján, és adja meg a felhasználónevet és jelszót.
7. Kattintson a **adatforrás** beállítása az adatok az elsődleges helyen. Válassza ki a **kijelöléséről** , és adjon meg egy már meglévő tárfiókot, vagy hozzon létre egy újat.
8. Adja meg az azonos panel egy **alapértelmezett tároló** és egy **hely**. Majd kattintson a gombra **válasszon**.

   > [!NOTE]
   > Válassza ki a teljesítmény növelése érdekében fiók Cosmos DB régiójától legközelebb eső helyet
   >
   >
9. Kattintson a **árazás** számát és típusát csomópont kiválasztásához. Tartani az alapértelmezett konfigurációt, és később a méretezni a feldolgozó csomópontok száma.
10. Kattintson a **opcionális konfigurációs**, majd **parancsfájl-műveletek** az opcionális konfigurációs panelen.

     Adja meg a következő adatokat a HDInsight-fürt testreszabása Parancsfájlműveletek.

     <table border='1'>
         <tr><th>Tulajdonság</th><th>Érték</th></tr>
         <tr><td>Name (Név)</td>
             <td>Adja meg a parancsfájlművelet nevét.</td></tr>
         <tr><td>A parancsfájl URI azonosítója</td>
             <td>Adja meg az URI-t a parancsfájlt, amelyet a fürt testreszabásához.</br></br>
Adja meg: </br> <strong>https://portalcontent.blob.core.windows.net/scriptaction/documentdb-hadoop-installer-v04.ps1</strong>.</td></tr>
         <tr><td>Fej</td>
             <td>Kattintson a jelölőnégyzetbe, az átjárócsomópont a PowerShell parancsfájl futtatásához.</br></br>
             <strong>Ezt a jelölőnégyzetet</strong>.</td></tr>
         <tr><td>Munkavégző</td>
             <td>Kattintson a jelölőnégyzetbe, a munkavégző csomópont a PowerShell parancsfájl futtatásához.</br></br>
             <strong>Ezt a jelölőnégyzetet</strong>.</td></tr>
         <tr><td>Zookeeper</td>
             <td>Kattintson a jelölőnégyzetbe, a Zookeeper a PowerShell parancsfájl futtatásához.</br></br>
             <strong>Nincs szükség</strong>.
             </td></tr>
         <tr><td>Paraméterek</td>
             <td>Adja meg a paraméterek, ha a parancsfájl által igényelt.</br></br>
             <strong>Nem szükséges paramétereket</strong>.</td></tr>
     </table>
11.Vagy hozzon létre egy új **erőforráscsoport** , vagy használjon egy meglévő erőforráscsoportot az Azure-előfizetéshez tartozó.
12. Ellenőrizze, **rögzítés az irányítópulton** nyomon követhető a központi telepítés, és kattintson a **létrehozása**!

## <a name="InstallCmdlets"></a>2. lépés: Telepítse és konfigurálja az Azure PowerShell
1. Az Azure PowerShell telepítése. Útmutatás található [Itt][powershell-install-configure].

   > [!NOTE]
   > Azt is megteheti csak a Hive-lekérdezéseket, használhatja a HDInsight tartozó online Hive szerkesztő. Ehhez jelentkezzen be a [Azure Portal][azure-portal], kattintson a **HDInsight** a HDInsight-fürtök listájának megtekintése a bal oldali ablaktáblán. Kattintson arra a fürtre, Hive-lekérdezések futtatása, és kattintson a kívánt **lekérdezés konzol**.
   >
   >
2. Nyissa meg az Azure PowerShell integrált parancsprogram-kezelési környezetet:

   * A Windows 8 vagy Windows Server 2012 vagy újabb rendszerű számítógépen a beépített keresési is használhatja. Írja be a kezdőképernyőről **powershell ise** kattintson **Enter**.
   * A Start menü használata a Windows 8 vagy Windows Server 2012-nél korábbi rendszerű számítógépen. A Start menüben írja be a **parancssor** a keresési mezőbe, majd az eredmények listájában kattintson **parancssor**. Írja be a parancssorba **powershell_ise** kattintson **Enter**.
3. Adja hozzá az Azure-fiókjával.

   1. Írja be a konzol ablaktáblájában **Add-AzureAccount** kattintson **Enter**.
   2. Írja be az Azure-előfizetéshez társított e-mail címét, és kattintson a **Folytatás**.
   3. Adja meg az Azure-előfizetéshez tartozó jelszót.
   4. Kattintson a **bejelentkezés**.
4. Az alábbi ábra a fontos részek az Azure PowerShell-parancsfájl-kezelési környezet azonosítja.

    ![Az Azure PowerShell diagramja][azure-powershell-diagram]

## <a name="RunHive"></a>3. lépés: Azure Cosmos DB és HDInsight Hive feladat futtatása
> [!IMPORTANT]
> < > Által jelzett minden értéket meg kell adni a konfigurációs beállítások használata.
>
>

1. A következő változók megadása a PowerShell-parancsfájl ablaktáblán.

        # Provide Azure subscription name, the Azure Storage account and container that is used for the default HDInsight file system.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide the HDInsight cluster name where you want to run the Hive job.
        $clusterName = "<HDInsightClusterName>"
2. <p>Most először hoz létre, a lekérdezési karakterlánc. A Microsoft fog írni Hive lekérdezés összes dokumentumot (_ts) rendszer által létrehozott időbélyegeket és egyedi azonosítók (_rid) egy Azure Cosmos DB gyűjteményből, összes dokumentumot összeadja a percenként, majd tárolja az eredményeket egy új Azure Cosmos DB gyűjteménybe.</p>

    <p>Először hozzon létre olyan Hive táblát az Azure Cosmos DB gyűjteményből. Adja hozzá a következő kódrészletet a PowerShell-parancsfájlt tartalmazó ablaktáblájához való <strong>után</strong> a kódrészletet # 1. Győződjön meg arról, a nem kötelező lekérdezési paraméter lehet levágni a dokumentumokat csak _ts és _rid.</p>

   > [!NOTE]
   > **DocumentDB.inputCollections elnevezési nem hiba volt.** Igen, azt felvételének engedélyezése több gyűjtemény bemenetként: </br>
   >
   >

        '*DocumentDB.inputCollections*' = '*\<DocumentDB Input Collection Name 1\>*,*\<DocumentDB Input Collection Name 2\>*' A1A</br> The collection names are separated without spaces, using only a single comma.

        # Create a Hive table using data from Azure Cosmos DB. Pass Azure Cosmos DB the query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "drop table DocumentDB_timestamps; "  +
                            "create external table DocumentDB_timestamps(id string, ts BIGINT) "  +
                            "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' "  +
                            "tblproperties ( " +
                                "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                "'DocumentDB.key' = '<DocumentDB Primary Key>', " +
                                "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                "'DocumentDB.inputCollections' = '<DocumentDB Input Collection Name>', " +
                                "'DocumentDB.query' = 'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "

1. Következő lépésként hozzon létre olyan Hive táblát a kimeneti gyűjteményhez. A kimeneti dokumentumtulajdonságok lesz a hónap, nap, óra, perc és előfordulások száma összesen.

   > [!NOTE]
   > **Még újra DocumentDB.outputCollections elnevezési nem hiba volt.** Igen, azt felvételének engedélyezése több gyűjtemény kimenetként: </br>
   > "*DocumentDB.outputCollections*'='*\<DocumentDB-gyűjtemény Kimenetnév 1\>*,*\<DocumentDB-gyűjtemény Kimenetnév 2\>*" </br> A gyűjtemény nevét nélkül szóközt tartalmaz, csak egyetlen vesszővel válassza el egymástól. </br></br>
   > Dokumentumok lesz elosztott ciklikus multiplexelés több gyűjtemények között. A kötegelt dokumentumok fogja tárolni egy gyűjteményt, majd a második kötegelt dokumentumok fogja tárolni a következő gyűjteményben, és így tovább.
   >
   >

       # Create a Hive table for the output data to Azure Cosmos DB.
       $queryStringPart2 = "drop table DocumentDB_analytics; " +
                             "create external table DocumentDB_analytics(Month INT, Day INT, Hour INT, Minute INT, Total INT) " +
                             "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' " +
                             "tblproperties ( " +
                                 "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                 "'DocumentDB.key' = '<DocumentDB Primary Key>', " +  
                                 "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                 "'DocumentDB.outputCollections' = '<DocumentDB Output Collection Name>' ); "
2. Végül tegyük a dokumentumok egyeztetési hónap, nap, óra és perc által és az eredmények beszúrása vissza a kimeneti Hive tábla.

        # GROUP BY minute, COUNT entries for each, INSERT INTO output Hive table.
        $queryStringPart3 = "INSERT INTO table DocumentDB_analytics " +
                              "SELECT month(from_unixtime(ts)) as Month, day(from_unixtime(ts)) as Day, " +
                              "hour(from_unixtime(ts)) as Hour, minute(from_unixtime(ts)) as Minute, " +
                              "COUNT(*) AS Total " +
                              "FROM DocumentDB_timestamps " +
                              "GROUP BY month(from_unixtime(ts)), day(from_unixtime(ts)), " +
                              "hour(from_unixtime(ts)) , minute(from_unixtime(ts)); "
3. Adja hozzá a következő parancsfájl részlet használatával hozhat létre az előző lekérdezés egy Hive feladat definíciójához.

        # Create a Hive job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

    Is meg kell adni egy HiveQL-parancsfájlt a HDFS a - fájl kapcsoló.
4. Adja hozzá a következő kódrészletet a kezdési időt és elküldeni a Hive feladatot.

        # Save the start time and submit the job to the cluster.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition
5. Adja hozzá a következő, a Hive feladat befejeződésére vár.

        # Wait for the Hive job to complete.
        Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600
6. Adja hozzá a következő, a standard kimenet és a kezdő és záró időpontjának nyomtatása.

        # Print the standard error, the standard output of the Hive job, and the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green
7. **Futtatás** az új parancsfájl! **Kattintson a** a zöld végrehajtás gombra.
8. Ellenőrizze az eredményeket. Jelentkezzen be a [Azure-portálon][azure-portal].

   1. Kattintson a <strong>Tallózás</strong> a bal oldali panelen. </br>
   2. Kattintson a <strong>mindent</strong> , a jobb felső részén a böngészés panelen. </br>
   3. Keresse meg és kattintson a <strong>Azure Cosmos DB fiókok</strong>. </br>
   4. Ezt követően keresse meg a <strong>Azure Cosmos DB fiók</strong>, majd <strong>Azure Cosmos DB adatbázis</strong> és a <strong>Azure Cosmos DB gyűjtemény</strong> a Hive-lekérdezést a megadott kimeneti gyűjtemény társítva.</br>
   5. Végezetül kattintson <strong>dokumentumkezelő</strong> alatt <strong>fejlesztői eszközök</strong>.</br></p>

   A Hive-lekérdezések eredményeinek jelenik meg.

   ![Hive lekérdezés eredményei][image-hive-query-results]

## <a name="RunPig"></a>4. lépés: A Pig feladatot Cosmos DB és HDInsight használatával futtassa
> [!IMPORTANT]
> < > Által jelzett minden értéket meg kell adni a konfigurációs beállítások használata.
>
>

1. A következő változók megadása a PowerShell-parancsfájl ablaktáblán.

        # Provide Azure subscription name.
        $subscriptionName = "Azure Subscription Name"

        # Provide HDInsight cluster name where you want to run the Pig job.
        $clusterName = "Azure HDInsight Cluster Name"
2. <p>Most először hoz létre, a lekérdezési karakterlánc. A Microsoft fog írni Pig lekérdezés összes dokumentumot (_ts) rendszer által létrehozott időbélyegeket és egyedi azonosítók (_rid) egy Azure Cosmos DB gyűjteményből, összes dokumentumot összeadja a percenként, majd tárolja az eredményeket egy új Azure Cosmos DB gyűjteménybe.</p>
    <p>Első lépésként betölteni a HDInsight a Cosmos-Adatbázisból dokumentumok. Adja hozzá a következő kódrészletet a PowerShell-parancsfájlt tartalmazó ablaktáblájához való <strong>után</strong> a kódrészletet # 1. Ügyeljen arra, hogy a lekérdezés hozzáadása a nem kötelező a DocumentDB lekérdezési paraméter lehet levágni a dokumentumokat csak _ts és _rid.</p>

   > [!NOTE]
   > Igen, azt felvételének engedélyezése több gyűjtemény bemenetként: </br>
   > "*\<DocumentDB bemeneti gyűjtemény neve 1\>*,*\<DocumentDB bemeneti gyűjteménynév 2\>*"</br> A gyűjtemény nevét nélkül szóközt tartalmaz, csak egyetlen vesszővel válassza el egymástól. </b>
   >
   >

    Dokumentumok lesz elosztott ciklikus multiplexelés több gyűjtemények között. A kötegelt dokumentumok fogja tárolni egy gyűjteményt, majd a második kötegelt dokumentumok fogja tárolni a következő gyűjteményben, és így tovább.

        # Load data from Cosmos DB. Pass the Azure Cosmos DB query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "DocumentDB_timestamps = LOAD '<DocumentDB Endpoint>' USING com.microsoft.azure.documentdb.pig.DocumentDBLoader( " +
                                                        "'<DocumentDB Primary Key>', " +
                                                        "'<DocumentDB Database Name>', " +
                                                        "'<DocumentDB Input Collection Name>', " +
                                                        "'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "
3. A következő most megegyeznek a dokumentumok a hónap, nap, óra, perc és előfordulások száma szerint.

       # GROUP BY minute and COUNT entries for each.
       $queryStringPart2 = "timestamp_record = FOREACH DocumentDB_timestamps GENERATE `$0#'id' as id:int, ToDate((long)(`$0#'ts') * 1000) as timestamp:datetime; " +
                           "by_minute = GROUP timestamp_record BY (GetYear(timestamp), GetMonth(timestamp), GetDay(timestamp), GetHour(timestamp), GetMinute(timestamp)); " +
                           "by_minute_count = FOREACH by_minute GENERATE FLATTEN(group) as (Year:int, Month:int, Day:int, Hour:int, Minute:int), COUNT(timestamp_record) as Total:int; "
4. Végezetül most az eredmények tárolásához az új kimeneti gyűjteménybe.

   > [!NOTE]
   > Igen, azt felvételének engedélyezése több gyűjtemény kimenetként: </br>
   > "\<DocumentDB-gyűjtemény Kimenetnév 1\>,\<DocumentDB-gyűjtemény Kimenetnév 2\>"</br> A gyűjtemény nevét nélkül szóközt tartalmaz, csak egyetlen vesszővel válassza el egymástól.</br>
   > Dokumentumok elosztott ciklikus multiplexelés lesz a több gyűjtemények között. A kötegelt dokumentumok fogja tárolni egy gyűjteményt, majd a második kötegelt dokumentumok fogja tárolni a következő gyűjteményben, és így tovább.
   >
   >

        # Store output data to Cosmos DB.
        $queryStringPart3 = "STORE by_minute_count INTO '<DocumentDB Endpoint>' " +
                            "USING com.microsoft.azure.documentdb.pig.DocumentDBStorage( " +
                                "'<DocumentDB Primary Key>', " +
                                "'<DocumentDB Database Name>', " +
                                "'<DocumentDB Output Collection Name>'); "
5. Adja hozzá az alábbi parancsfájl kódrészletet az előző lekérdezés egy olyan feladatdefinícióban létrehozásához.

        # Create a Pig job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $queryString -StatusFolder $statusFolder

    Használhatja a - fájl kapcsoló a HDFS Pig-parancsprogram fájlt ad meg.
6. Adja hozzá a következő kódrészletet a kezdési időt és elküldeni a Pig feladatot.

        # Save the start time and submit the job to the cluster.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition
7. Adja hozzá a következő, a Pig futó feladat befejeződésére vár.

        # Wait for the Pig job to complete.
        Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600
8. Adja hozzá a következő, a standard kimenet és a kezdő és záró időpontjának nyomtatása.

        # Print the standard error, the standard output of the Hive job, and the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green
9. **Futtatás** az új parancsfájl! **Kattintson a** a zöld végrehajtás gombra.
10. Ellenőrizze az eredményeket. Jelentkezzen be a [Azure-portálon][azure-portal].

    1. Kattintson a <strong>Tallózás</strong> a bal oldali panelen. </br>
    2. Kattintson a <strong>mindent</strong> , a jobb felső részén a böngészés panelen. </br>
    3. Keresse meg és kattintson a <strong>Azure Cosmos DB fiókok</strong>. </br>
    4. Ezt követően keresse meg a <strong>Azure Cosmos DB fiók</strong>, majd <strong>Azure Cosmos DB adatbázis</strong> és a <strong>Azure Cosmos DB gyűjtemény</strong> a Pig-lekérdezésben megadott kimeneti gyűjtemény társítva.</br>
    5. Végezetül kattintson <strong>dokumentumkezelő</strong> alatt <strong>fejlesztői eszközök</strong>.</br></p>

    Látni fogja a Pig lekérdezés eredményeit.

    ![A Pig lekérdezés eredményei][image-pig-query-results]

## <a name="RunMapReduce"></a>5. lépés: Azure Cosmos DB és HDInsight MapReduce feladat futtatása
1. A következő változók megadása a PowerShell-parancsfájl ablaktáblán.

        $subscriptionName = "<SubscriptionName>"   # Azure subscription name
        $clusterName = "<ClusterName>"             # HDInsight cluster name
2. A MapReduce feladatot, amely minden egyes dokumentum tulajdonság az Azure Cosmos DB gyűjteményből előfordulások számát határozza meg azt fogja végrehajtani. Adja hozzá a parancsfájl részlet **után** a fenti kódrészletet.

        # Define the MapReduce job.
        $TallyPropertiesJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/TallyProperties-v01.jar" -ClassName "TallyProperties" -Arguments "<DocumentDB Endpoint>","<DocumentDB Primary Key>", "<DocumentDB Database Name>","<DocumentDB Input Collection Name>","<DocumentDB Output Collection Name>","<[Optional] DocumentDB Query>"

   > [!NOTE]
   > A Cosmos DB Hadoop összekötő egyéni telepítését TallyProperties-v01.jar tartalmaz.
   >
   >
3. Adja hozzá a következő parancs futtatásával elküldeni a MapReduce feladatot.

        # Save the start time and submit the job.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $TallyPropertiesJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $TallyPropertiesJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

    A MapReduce feladatdefiníció mellett is megadta a HDInsight-fürt nevét, ahol szeretné indítani a MapReduce feladatot, és a hitelesítő adatokat. A Start-AzureHDInsightJob egy aszinkron módjára való átkapcsolás hívás. A feladat befejezésére ellenőrzéséhez használja a *várakozási-AzureHDInsightJob* parancsmag.
4. Adja hozzá a következő parancs futtatásával ellenőrizze a hibákat a MapReduce feladat futtatásával.

        # Get the job output and print the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $TallyPropertiesJob.JobId -StandardError
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green
5. **Futtatás** az új parancsfájl! **Kattintson a** a zöld végrehajtás gombra.
6. Ellenőrizze az eredményeket. Jelentkezzen be a [Azure-portálon][azure-portal].

   1. Kattintson a <strong>Tallózás</strong> a bal oldali panelen.
   2. Kattintson a <strong>mindent</strong> , a jobb felső részén a böngészés panelen.
   3. Keresse meg és kattintson a <strong>Azure Cosmos DB fiókok</strong>.
   4. Ezt követően keresse meg a <strong>Azure Cosmos DB fiók</strong>, majd <strong>Azure Cosmos DB adatbázis</strong> és a <strong>Azure Cosmos DB gyűjtemény</strong> a MapReduce feladatot a megadott kimeneti gyűjtemény társítva.
   5. Végezetül kattintson <strong>dokumentumkezelő</strong> alatt <strong>fejlesztői eszközök</strong>.

      Látni fogja a MapReduce feladatot eredményeit.

      ![MapReduce lekérdezés eredményei][image-mapreduce-query-results]

## <a name="NextSteps"></a>További lépések
Gratulálunk! Csak az első Hive, Pig és MapReduce feladatok Azure Cosmos DB és HDInsight futtatta.

Tudunk nyissa meg a Hadoop összekötő forrása. Ha szeretné használni, akkor a járulhat [GitHub][github].

További tudnivalókért tekintse meg a következő cikkeket:

* [Az Azure Cosmos DB Java-alkalmazások fejlesztése][sql-api-java-application]
* [A hdinsight Hadoop Java MapReduce programok fejlesztése][hdinsight-develop-deploy-java-mapreduce]
* [Hadoop használatának megkezdésében a hdinsight Hive elemzéséhez mobil kézibeszélő használata][hdinsight-get-started]
* [Use MapReduce with HDInsight][hdinsight-use-mapreduce]
* [A Hive használata a HDInsightban][hdinsight-use-hive]
* [A Pig használata a HDInsightban][hdinsight-use-pig]
* [Parancsfájlművelet HDInsight-fürtök testreszabása][hdinsight-hadoop-customize-cluster]

[apache-hadoop]: http://hadoop.apache.org/
[apache-hadoop-doc]: http://hadoop.apache.org/docs/current/
[apache-hive]: http://hive.apache.org/
[apache-pig]: http://pig.apache.org/
[getting-started]: sql-api-get-started.md

[azure-portal]: https://portal.azure.com/
[azure-powershell-diagram]: ./media/run-hadoop-with-hdinsight/azurepowershell-diagram-med.png

[hdinsight-samples]: http://portalcontent.blob.core.windows.net/samples/documentdb-hdinsight-samples.zip
[github]: https://github.com/Azure/azure-documentdb-hadoop
[sql-api-java-application]: sql-api-java-application.md
[import-data]: import-data.md

[hdinsight-custom-provision]: ../hdinsight/hdinsight-provision-clusters.md
[hdinsight-develop-deploy-java-mapreduce]:../hdinsight/hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-hadoop-customize-cluster]: ../hdinsight/hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: ../hdinsight/hdinsight-hadoop-tutorial-get-started-windows.md
[hdinsight-storage]: ../hdinsight/hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]:../hdinsight/hadoop/hdinsight-use-hive.md
[hdinsight-use-mapreduce]:../hdinsight/hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-pig]:../hdinsight/hadoop/hdinsight-use-pig.md

[image-customprovision-page1]: ./media/run-hadoop-with-hdinsight/customprovision-page1.png
[image-hive-query-results]: ./media/run-hadoop-with-hdinsight/hivequeryresults.PNG
[image-mapreduce-query-results]: ./media/run-hadoop-with-hdinsight/mapreducequeryresults.PNG
[image-pig-query-results]: ./media/run-hadoop-with-hdinsight/pigqueryresults.PNG

[powershell-install-configure]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0
