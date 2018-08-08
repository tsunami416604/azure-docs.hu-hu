---
title: Giraph telepítése és használata a HDInsight - Azure Hadoop-fürtök
description: Ismerje meg, hogyan szabhatja testre a HDInsight-fürt a Giraph és a Giraph használata.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2016
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: 18469b5e2294a689df166f04b99bf83be122ff2f
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39594287"
---
# <a name="install-and-use-giraph-on-windows-based-hdinsight-clusters"></a>Giraph telepítése és használata a Windows-alapú HDInsight-fürtökön

Ismerje meg a Windows-alapú HDInsight-fürt testreszabása a Script actionnel Giraph, és hogyan lehet a Giraph használata nagyméretű gráfok feldolgozásához. A Giraph használata a Linux-alapú fürt információkért lásd: [telepíteni a Giraph a HDInsight Hadoop-fürtök (Linux)](hdinsight-hadoop-giraph-install-linux.md).

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések csak a Windows-alapú HDInsight-fürtökkel működik. HDInsight csak akkor használható a Windows-verziók alacsonyabb, mint a HDInsight 3.4-es. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement). A Giraph telepítése egy Linux-alapú HDInsight-fürtön információkért lásd: [telepíteni a Giraph a HDInsight Hadoop-fürtök (Linux)](hdinsight-hadoop-giraph-install-linux.md).


Telepítheti a Giraph bármilyen típusú Azure HDInsight (Hadoop-, Storm, HBase, Spark-) fürt segítségével *parancsfájlművelet*. A parancsfájlpéldát a Giraph telepítése egy HDInsight-fürtön érhető el, csak olvasható Azure storage-blobból [ https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1). A minta parancsfájl a csak a HDInsight-fürt verziója 3.1 működik. A HDInsight fürt verziókról további információkért lásd: [HDInsight fürtverziók](hdinsight-component-versioning.md).

**Kapcsolódó cikkek**

* [A Giraph telepítése a HDInsight Hadoop-fürtök (Linux)](hdinsight-hadoop-giraph-install-linux.md)
* [A HDInsight Hadoop-fürtök létrehozása](hdinsight-provision-clusters.md): általános információk a HDInsight-fürtök létrehozása során.
* [Szkriptműveletek használatával HDInsight-fürt testreszabása][hdinsight-cluster-customize]: általános információk a Testreszabás szkriptműveletekkel HDInsight-fürtök.
* [Parancsfájlművelet-parancsfájlok fejlesztése a HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-giraph"></a>Mi az a Giraph?
<a href="http://giraph.apache.org/" target="_blank">Az Apache Giraph</a> diagramfeldolgozási Hadoop használatával végezhető el, és az Azure HDInsight is használható. Gráfok objektumok, például az útválasztók nagyobb hálózatokon, mint például az interneten, vagy a közösségi hálózatokon (más néven egy közösségi diagram) személyek közötti kapcsolatok közötti kapcsolatok közötti kapcsolatok modellezésére. Gráffeldolgozási lehetővé teszi a grafikon, objektumok közötti kapcsolatok vonatkozó döntések meghozatalát például:

* A jelenlegi kapcsolatok alapján lehetséges barátok azonosítása.
* A hálózat két számítógép közötti legrövidebb útvonal azonosítása.
* Weblapok oldalon belüli rangjának kiszámítása.

## <a name="install-giraph-using-portal"></a>Portál használatával, a Giraph telepítése
1. Indítsa el a fürt létrehozása a **egyéni létrehozás** paraméterrel, ahogyan a [Hadoop-fürtök létrehozása a HDInsight](hdinsight-provision-clusters.md).
2. A a **Parancsfájlműveletek** lapon kattintson a varázsló **parancsfájlművelet hozzáadása** , adja meg a parancsfájlművelet adatait, ahogy az alábbi:

    ![Fürt testreszabása Szkriptműveletek használatával](./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "Szkriptműveleteket használata a fürt testreszabása")

    <table border='1'>
        <tr><th>Tulajdonság</th><th>Érték</th></tr>
        <tr><td>Name (Név)</td>
            <td>Adja meg a parancsfájlművelet nevét. Ha például <b>telepíteni a Giraph</b>.</td></tr>
        <tr><td>Szkript URI-ja</td>
            <td>Adja meg az egységes erőforrás-azonosító (URI) a parancsfájlt, amely a fürt testreszabásához meghívott. Ha például <i>https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1</i></td></tr>
        <tr><td>Csomóponttípus</td>
            <td>Adja meg a csomópontok, amelyen fut a testreszabási parancsfájlt. Választhat <b>minden csomópont</b>, <b>csak Átjárócsomópontokhoz</b>, vagy <b>csak a munkavégző csomópontok</b>.
        <tr><td>Paraméterek</td>
            <td>Adja meg a paramétereket, ha a parancsfájl által igényelt. A szkriptet a Giraph telepítése nem szükséges paramétereket, így is ezt üresen hagyja.</td></tr>
    </table>

    Hozzáadhat több parancsfájlművelet több összetevők telepíthetők a fürtön. Miután hozzáadta a parancsfájlok, kattintson a pipa jelre a fürt létrehozásának megkezdéséhez.

## <a name="use-giraph"></a>A Giraph használata
Használjuk a SimpleShortestPathsComputation példa az alapszintű bemutatásához <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> megvalósítása a grafikus objektumok közötti legrövidebb útvonal megkeresése. Az alábbi lépések segítségével a mintaadatok és példa fájlt tölthet fel, a feladat futtatása SimpleShortestPathsComputation példa használatával, és tekintse meg az eredményeket.

1. Egy minta adatfájl feltöltése az Azure Blob storage. A helyi munkaállomáson, hozzon létre egy új fájlt **tiny_graph.txt**. A következő sorokat kell tartalmaznia:

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    Töltse fel a tiny_graph.txt fájlt az elsődleges tárolási, a HDInsight-fürt számára. Adatok feltöltése az utasításokért lásd: [feltölteni az adatokat a HDInsight Hadoop-feladatokhoz](hdinsight-upload-data.md).

    Ezeket az adatokat egy irányított gráfban, a következő formátumban objektumok közötti kapcsolatot ismerteti [forrás\_azonosító, a forrás\_érték, [[cél\_id], [edge\_érték],...]]. Az egyes sorok közötti kapcsolatot jelent egy **forrás\_azonosító** objektum és a egy vagy több **dest\_azonosító** objektumokat. A **edge\_érték** (vagy súlya) erősségét vagy közötti kapcsolat távolság tekinthető **source_id** és **dest\_azonosító**.

    Dolgozni, és az objektumok közötti távolságot a értéket (vagy a súly) használ, a fenti adatokat ehhez hasonló lehet:

    ![különböző távolságát sornyi körök Megrajzolás tiny_graph.txt](./media/hdinsight-hadoop-giraph-install/giraph-graph.png)
2. Futtassa a SimpleShortestPathsComputation példa. A következő Azure PowerShell-parancsmagok használatával futtassa a példa bemenetként a tiny_graph.txt fájl használatával.

    > [!IMPORTANT]
    > A HDInsight-erőforrások Azure Service Managerrel történő kezelésének Azure PowerShell-támogatása **elavult**, így 2017. január 1-től megszűnt. A jelen dokumentumban leírt lépések az új HDInsight-parancsmagokat használják, amelyek az Azure Resource Managerrel működnek.
    >
    > Kérjük, kövesse az alábbi cikkben leírt lépéseket az Azure PowerShell legújabb verziójának telepítéséhez: [Install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs) (Az Azure PowerShell letöltése és konfigurálása). Ha vannak olyan parancsprogramjai, amelyeket módosítani kell az új, az Azure Resource Managerrel működő parancsmagok használatához, tekintse meg az alábbi cikket: [Migrating to Azure Resource Manager-based development tools for HDInsight clusters](hdinsight-hadoop-development-using-azure-resource-manager.md) (Az Azure Resource Manager-alapú fejlesztési eszközökre való áttérés HDInsight-fürtök esetén).

    ```powershell
    $clusterName = "clustername"
    # Giraph examples jar
    $jarFile = "wasb:///example/jars/giraph-examples.jar"
    # Arguments for this job
    $jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation",
                    "-ca", "mapred.job.tracker=headnodehost:9010",
                    "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat",
                    "-vip", "wasb:///example/data/tiny_graph.txt",
                    "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat",
                    "-op",  "wasb:///example/output/shortestpaths",
                    "-w", "2"
    # Create the definition
    $jobDefinition = New-AzureHDInsightMapReduceJobDefinition
        -JarFile $jarFile
        -ClassName "org.apache.giraph.GiraphRunner"
        -Arguments $jobArguments

    # Run the job, write output to the Azure PowerShell window
    $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzureHDInsightJob -Job $job
    Write-Host "STDERR"
    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput
    ```

    Cserélje le a fenti példában **clustername** a HDInsight-fürt, amelyben telepíteni a Giraph nevére.
3. Tekintse meg az eredményeket. A feladat befejeztével az eredmények két a kimeneti fájlok kerülnek a **wasb: / / / például/out/shotestpaths** mappát. A fájlok az úgynevezett **. rész – m-00001** és **. rész – m-00002**. A következő lépésekkel letöltéséhez és a kimenet megtekintéséhez:

    ```powershell
    $subscriptionName = "<SubscriptionName>"       # Azure subscription name
    $storageAccountName = "<StorageAccountName>"   # Azure Storage account name
    $containerName = "<ContainerName>"             # Blob storage container name

    # Select the current subscription
    Select-AzureSubscription $subscriptionName

    # Create the Storage account context object
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    # Download the job output to the workstation
    Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
    Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force
    ```

    Ekkor létrejön a **példa/output/shortestpaths** könyvtárstruktúrát a munkaállomásokon és a kimeneti fájlok letöltése a két az adott helyen az aktuális könyvtárban található.

    Használja a **Cat** parancsmagot, hogy a fájl tartalmának megjelenítése:

        Cat example/output/shortestpaths/part*

    A kimenet az alábbihoz hasonlóan kell megjelennie:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    A SimpleShortestPathComputation, például rögzített, a kezdéshez kódolt azonosító 1 objektum, és más objektumok legrövidebb útvonal megkeresése. Így a kimenet olvassa el `destination_id distance`, ahol a távolságot a szélén, amennyi objektum azonosítója 1 és a cél-azonosítót. közötti érték (vagy súlya)

    Ez megjelenítése, ellenőrizheti az eredményeket a legrövidebb elérési utak utazás azonosító 1 és az összes többi objektum között. Vegye figyelembe, hogy az azonosító 1 és 4 azonosító közötti legrövidebb útvonal 5. Ez a teljes közötti távolság <span style="color:orange">azonosító 1. és 3</span>, majd <span style="color:red">azonosító 3. és 4</span>.

    ![Az objektumok Rajzolás körök, a legrövidebb elérési utak között](./media/hdinsight-hadoop-giraph-install/giraph-graph-out.png)

## <a name="install-giraph-using-aure-powershell"></a>Azure PowerShell-lel a Giraph telepítése
Lásd: [testreszabása HDInsight-fürtök szkriptműveletekkel](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).  A minta azt ismerteti, hogyan telepítheti a Spark, Azure PowerShell-lel. Testre kell szabnia a használandó parancsfájlt [ https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="install-giraph-using-net-sdk"></a>.NET SDK használatával a Giraph telepítése
Lásd: [testreszabása HDInsight-fürtök szkriptműveletekkel](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell). A minta azt ismerteti, hogyan telepítheti a Spark, a .NET SDK használatával. Testre kell szabnia a használandó parancsfájlt [ https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="see-also"></a>Lásd még
* [A Giraph telepítése a HDInsight Hadoop-fürtök (Linux)](hdinsight-hadoop-giraph-install-linux.md)
* [A HDInsight Hadoop-fürtök létrehozása](hdinsight-provision-clusters.md): általános információk a HDInsight-fürtök létrehozása során.
* [Szkriptműveletek használatával HDInsight-fürt testreszabása][hdinsight-cluster-customize]: általános információk a Testreszabás szkriptműveletekkel HDInsight-fürtök.
* [Parancsfájlművelet-parancsfájlok fejlesztése a HDInsight](hdinsight-hadoop-script-actions.md).
* [Telepítse, és válassza a Spark on HDInsight-fürtök][hdinsight-install-spark]: parancsfájlművelet minta Spark telepítésével kapcsolatban.
* [A Solr telepítése HDInsight-fürtökön](hdinsight-hadoop-solr-install.md): parancsfájlművelet minta Solr telepítésével kapcsolatban.

[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
