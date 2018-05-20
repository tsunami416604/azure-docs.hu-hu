---
title: Telepítheti és használhatja a HDInsight - Azure Hadoop-fürtök Giraph |} Microsoft Docs
description: Ismerje meg, hogyan szabhatja testre a HDInsight fürt Giraph és Giraph használata.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 77a1d0e0-55de-4e61-98a0-060914fb7ca0
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2016
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: c4cd643d4bdd95493f63bb5b1c1f855bc95bf226
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="install-and-use-giraph-on-windows-based-hdinsight-clusters"></a>Telepítheti és használhatja a Windows-alapú HDInsight-fürtök Giraph

Megismerheti a Giraph parancsfájlművelet használata a Windows-alapú HDInsight-fürtök testreszabása, és nagy méretű diagramjait feldolgozni Giraph használandó. Egy Linux-alapú fürttel Giraph használatáról információkért lásd: [Giraph telepítése a HDInsight Hadoop-fürtök (Linux)](hdinsight-hadoop-giraph-install-linux.md).

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések csak a Windows-alapú HDInsight-fürtök dolgozhat. HDInsight csak érhető el a Windows korábbi, mint a HDInsight 3.4-es verziójához. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement). A Linux-alapú HDInsight-fürt Giraph telepítésével információkért lásd: [Giraph telepítése a HDInsight Hadoop-fürtök (Linux)](hdinsight-hadoop-giraph-install-linux.md).


Telepíthető Giraph bármilyen típusú on Azure HDInsight (Hadoop-, Storm, HBase, Spark) fürt segítségével *parancsfájlművelet*. Egy minta parancsfájlt a HDInsight-fürtök Giraph telepítendő érhető el, csak olvasható az Azure storage-blobból [ https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1). A parancsfájlpéldát csak HDInsight-fürt verziószáma 3.1-es verziójával működik. A HDInsight-fürt verziókról további információkért lásd: [HDInsight-fürt verziókról](hdinsight-component-versioning.md).

**Kapcsolódó cikkek**

* [Giraph telepítse a HDInsight Hadoop-fürtök (Linux)](hdinsight-hadoop-giraph-install-linux.md)
* [Hdinsight Hadoop-fürtök létrehozása](hdinsight-provision-clusters.md): általános információk a HDInsight-fürtök létrehozása.
* [Testre szabhatja a HDInsight-fürtjéhez parancsfájlművelet][hdinsight-cluster-customize]: parancsfájlművelet HDInsight-fürtök testreszabása általános tájékoztatást.
* [Parancsfájlművelet-parancsfájlok fejlesztése a HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-giraph"></a>Mi az a Giraph?
<a href="http://giraph.apache.org/" target="_blank">Apache Giraph</a> lehetővé teszi a végrehajtását diagramfeldolgozás Hadoop használatával, és az Azure HDInsight használható. Diagramokat modellhez tartozó objektumok, például a kapcsolatokat, például az Internet nagy hálózati útválasztók, vagy a közösségi hálózatokon (más néven a közösségi diagramhoz) személyek közötti kapcsolatok között közötti kapcsolatokat. Graph feldolgozás lehetővé teszi a ok egy grafikonon objektumok közötti kapcsolatok, mint:

* A jelenlegi kapcsolatok alapján lehetséges ismerősök azonosítása.
* Azonosítja a legrövidebb útvonal hálózatban két számítógép között.
* A lap rangot a weblapok kiszámítása.

## <a name="install-giraph-using-portal"></a>Telepítse a portál használatával Giraph
1. Indítsa el a fürt létrehozása a **egyéni létrehozás** beállítást, a részben ismertetett módon [Hadoop létrehozása a HDInsight-fürtök](hdinsight-provision-clusters.md).
2. A a **Parancsfájlműveletek** lapon kattintson a varázsló **parancsfájlművelet hozzáadása** adni a parancsfájlművelet alább látható módon:

    ![Parancsfájlművelet használni ahhoz, hogy a fürt](./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "parancsfájlművelet használja a fürt testreszabása")

    <table border='1'>
        <tr><th>Tulajdonság</th><th>Érték</th></tr>
        <tr><td>Name (Név)</td>
            <td>Adja meg a parancsfájlművelet nevét. Például <b>telepítése Giraph</b>.</td></tr>
        <tr><td>A parancsfájl URI azonosítója</td>
            <td>Adja meg az egységes erőforrás-azonosító (URI) a parancsfájlt, amelyet a fürt testreszabásához. Például <i>https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1</i></td></tr>
        <tr><td>Csomóponttípus</td>
            <td>Adja meg a csomópontok, amelyen fut a testreszabási parancsfájl. Választhat <b>csomópontjaihoz</b>, <b>csak Átjárócsomópontokat</b>, vagy <b>csak a feldolgozó csomópontok</b>.
        <tr><td>Paraméterek</td>
            <td>Adja meg a paraméterek, ha a parancsfájl által igényelt. Giraph telepítéséhez a parancsfájlt nem szükséges paramétereket, ezért üresen hagyhatja, ez.</td></tr>
    </table>

    Több összetevőinek telepítése a fürt több parancsfájlművelet adhat hozzá. Miután hozzáadta a parancsfájlok, kattintson a pipára a fürt létrehozásához.

## <a name="use-giraph"></a>A Giraph használata
A basic bemutatásához használjuk a SimpleShortestPathsComputation példa <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> megvalósítása a legrövidebb elérési út egy grafikonon objektumok közötti kereséséhez. Az alábbi lépések segítségével a mintaadatok és a minta jar feltöltése, a SimpleShortestPathsComputation példa használatával egy feladat futtatása, és nézze meg az eredményeket.

1. Egy minta adatfájl feltöltése az Azure Blob storage. A helyi munkaállomáson, hozzon létre egy új fájlt **tiny_graph.txt**. A következő sorokat kell tartalmaznia:

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    A tiny_graph.txt fájlt feltölteni a HDInsight-fürt elsődleges tárolóhelyét. Hogyan tölthet fel adatokat, lásd: [feltölteni az adatokat a HDInsight Hadoop-feladatok](hdinsight-upload-data.md).

    Ezek az adatok irányított gráf, a következő formátumban objektumok közötti kapcsolatot ismerteti [forrás\_azonosító, a forrás\_érték, [[cél\_azonosítója], [peremhálózati\_érték],...]]. Minden egyes sorban közötti kapcsolatot jelent a **forrás\_azonosító** objektum és egy vagy több **cél\_azonosító** objektumok. A **peremhálózati\_érték** (vagy súly)-re a erőssége vagy a kapcsolat közötti távolság **source_id** és **cél\_azonosító**.

    Jelenik meg, és objektumok közötti távolságot a érték (vagy súly) használ, a fenti adatokat nézhet ki:

    ![különböző távolságát sornyi kör Megrajzolás tiny_graph.txt](./media/hdinsight-hadoop-giraph-install/giraph-graph.png)
2. Futtassa a SimpleShortestPathsComputation példa. A következő Azure PowerShell-parancsmagok segítségével futtathatja a példa az tiny_graph.txt fájllal bemenetként.

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

    A fenti példában cserélje le a **clustername** a HDInsight-fürt, amelyen telepítve Giraph nevével.
3. Tekintse meg az eredményeket. Ha a feladat befejeződött, az eredményeket fogja tárolni két a kimenő fájlok a **wasb: / / / Példa/out/shotestpaths** mappa. A fájlok nevezzük **rész-m-00001** és **rész-m-00002**. A következő lépésekkel töltse le és eredményének megtekintéséhez:

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

    Ekkor létrejön az **példa/kimeneti/shortestpaths** könyvtárstruktúrát a munkaállomáson, a két letöltési kimeneti fájlokat erre a helyre az aktuális könyvtárban található.

    Használja a **Cat** parancsmag használatával jelenítse meg a fájlok tartalma:

        Cat example/output/shortestpaths/part*

    A kimenet az alábbihoz hasonlóan kell megjelennie:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    A kódolt kezdődnie példája rögzített SimpleShortestPathComputation azonosítója 1 objektum, és keresse meg a legrövidebb más objektumok elérési útja. A kimeneti kell olvasni, ezért `destination_id distance`, ahol a távolság objektum azonosítója 1 és a célként megadott azonosító közötti távolság széleit érték (vagy súlya)

    Ez megjelenítése, ellenőrizheti az eredményeket a legrövidebb elérési utak utazás azonosítója 1 és egyéb objektumok között. Vegye figyelembe, hogy a lehető legrövidebb azonosítója 1 és 4 azonosító közötti elérési út 5. Ez a teljes távolságát <span style="color:orange">azonosítója 1. és 3</span>, majd <span style="color:red">azonosító 3. és 4</span>.

    ![Az objektumok rajzolási körök mint a legrövidebb elérési utak között](./media/hdinsight-hadoop-giraph-install/giraph-graph-out.png)

## <a name="install-giraph-using-aure-powershell"></a>Giraph segítségével a következőkre PowerShell telepítése
Lásd: [testreszabása HDInsight-fürtök használata parancsfájlművelet](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).  A Spark az Azure PowerShell telepítése mutatja be. Meg kell adnia, hogy a használandó parancsfájlt [ https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="install-giraph-using-net-sdk"></a>Telepítse a .NET SDK használatával Giraph
Lásd: [testreszabása HDInsight-fürtök használata parancsfájlművelet](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell). A minta bemutatja, hogyan telepítse a .NET SDK használatával Spark. Meg kell adnia, hogy a használandó parancsfájlt [ https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="see-also"></a>Lásd még
* [Giraph telepítse a HDInsight Hadoop-fürtök (Linux)](hdinsight-hadoop-giraph-install-linux.md)
* [Hdinsight Hadoop-fürtök létrehozása](hdinsight-provision-clusters.md): általános információk a HDInsight-fürtök létrehozása.
* [Testre szabhatja a HDInsight-fürtjéhez parancsfájlművelet][hdinsight-cluster-customize]: parancsfájlművelet HDInsight-fürtök testreszabása általános tájékoztatást.
* [Parancsfájlművelet-parancsfájlok fejlesztése a HDInsight](hdinsight-hadoop-script-actions.md).
* [Telepítse, és válassza a Spark on HDInsight-fürtök][hdinsight-install-spark]: Spark telepítéséről parancsfájlművelet-mintát.
* [Solr telepíthető a HDInsight-fürtök](hdinsight-hadoop-solr-install.md): parancsfájlművelet minta Solr telepítésével kapcsolatban.

[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
