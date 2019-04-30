---
title: Az Apache Hadoop – Azure HDInsight MapReduce és a PowerShell használata
description: Ismerje meg, hogyan lehet távolról futtatni a HDInsight az Apache Hadoop MapReduce-feladatok PowerShell használatával.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 21b56d32-1785-4d44-8ae8-94467c12cfba
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
origin.date: 05/09/2018
ms.date: 04/15/2019
ms.author: v-yiso
ms.openlocfilehash: 29e23d5919a953566c803f2b7825a75a2993723c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129025"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-powershell"></a>Az Apache Hadoop MapReduce feladatok futtatása HDInsight PowerShell használatával

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]



Ez a dokumentum azt szemlélteti, egy MapReduce-feladatot futtatni a Hadoop HDInsight-fürtön az Azure PowerShell használatával.

## <a id="prereq"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* **Egy Azure HDInsight (Hadoop on HDInsight)-fürt**

  > [!IMPORTANT]
  > A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Munkaállomás Azure PowerShell-lel**.

## <a id="powershell"></a>A MapReduce-feladat futtatása

Az Azure PowerShell biztosít *parancsmagok* , amelyek engedélyezik a HDInsight MapReduce-feladatok távoli futtatását. Belsőleg, PowerShell, REST-hívást hajt végre [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (korábbi nevén templeton eszközön keresztül végzett) a HDInsight-fürtön futó.

A következő parancsmagok használhatók MapReduce-feladatok egy távoli HDInsight-fürtön való futtatáskor.

* **Connect-AzAccount**: Azure PowerShell-lel hitelesíti az Azure-előfizetéshez.

* **New-AzHDInsightMapReduceJobDefinition**: Létrehoz egy új *feladat definíciójának* MapReduce megadott információk segítségével.

* **Start-AzHDInsightJob**: A feladatdefiníció HDInsight küld, és elindítja a feladatot. A *feladat* objektumot ad vissza.

* **Wait-AzHDInsightJob**: A feladat állapotának ellenőrzéséhez használja a feladatobjektum. Arra vár, amíg a feladat befejeződik, vagy túllépi a várakozási idő.

* **Get-AzHDInsightJobOutput**: A feladat kimenetének lekéréséhez használja.

A következő lépések bemutatják, hogyan használja ezeket a parancsmagokat futtathat feladatokat a HDInsight-fürtben.

1. Egy szerkesztővel, mentse a következő kódot, **mapreducejob.ps1**.

    ```powershell
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount -EnvironmentName AzureChinaCloud
    }

    # Get cluster info
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    $creds=Get-Credential -Message "Enter the login for the cluster"

    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    #NOTE: This assumes that the storage account is in the same resource
    #      group as the cluster. If it is not, change the
    #      --ResourceGroupName parameter to the group that contains storage.
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
    -ResourceGroupName $resourceGroup)[0].Value

    #Create a storage context
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey

    #Define the MapReduce job
    #NOTE: If using an HDInsight 2.0 cluster, use hadoop-examples.jar instead.
    # -JarFile = the JAR containing the MapReduce application
    # -ClassName = the class of the application
    # -Arguments = The input file, and the output directory
    $wordCountJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
        -JarFile "/example/jars/hadoop-mapreduce-examples.jar" `
        -ClassName "wordcount" `
        -Arguments `
            "/example/data/gutenberg/davinci.txt", `
            "/example/data/WordCountOutput"

    #Submit the job to the cluster
    Write-Host "Start the MapReduce job..." -ForegroundColor Green
    $wordCountJob = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $wordCountJobDefinition `
        -HttpCredential $creds

    #Wait for the job to complete
    Write-Host "Wait for the job to complete..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds
    # Download the output
    Get-AzureStorageBlobContent `
        -Blob 'example/data/WordCountOutput/part-r-00000' `
        -Container $container `
        -Destination output.txt `
        -Context $context
    # Print the output of the job.
    Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds
    ```

2. Nyisson meg egy új **Azure PowerShell-lel** parancssort. Módosítsa a könyvtárat a helyét a **mapreducejob.ps1** fájlt, majd futtassa a parancsfájlt a következő paranccsal:

        .\mapreducejob.ps1

    A szkript futtatásakor a rendszer kéri a HDInsight-fürt és a fürt bejelentkezési neve. Emellett felkérheti hitelesítéséhez az Azure-előfizetéshez.

3. Ha a feladat befejeződik, az alábbi szöveghez hasonló kimenet jelenhet meg:

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    Ez a kimenet azt jelzi, hogy a feladat sikeresen befejeződött-e.

    > [!NOTE]
    > Ha a **ExitCode** egy érték nem 0, lásd: [hibaelhárítás](#troubleshooting).

    Ebben a példában is tárolja a letöltött fájlokat, és egy **kimenet.txt** fájl a könyvtárban, amely futtatja a szkriptet.

### <a name="view-output"></a>A kimeneti nézet

A szavak és a feladat által előállított counts megtekintéséhez nyissa meg a **kimenet.txt** fájlt egy szövegszerkesztőben.

> [!NOTE]
> A kimeneti fájlokat egy MapReduce-feladatot, nem módosíthatók. Ezért ha újra futtatja ezt a mintát, módosítani szeretné a kimeneti fájl nevét.

## <a id="troubleshooting"></a>Hibaelhárítás

Ha semmilyen adatot nem ad vissza, ha a feladat befejeződik, tekintse meg a feladat által jelzett hibákat. A feladat hibaadatok megtekintéséhez adja hozzá a következő parancs végéhez a **mapreducejob.ps1** fájlt, és mentse, majd futtassa újra.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Ez a parancsmag az információkat arról, hogy STDERR a feladat futása adja vissza.

## <a id="summary"></a>Summary (Összefoglalás)

Látható, az Azure PowerShell MapReduce-feladatok futtatása egy HDInsight-fürtön, a feladat állapotának nyomon és lekérése a kimeneti egyszerű módszert biztosít.

## <a id="nextsteps"></a>Következő lépések

HDInsight MapReduce-feladatok általános tájékoztatást:

* [HDInsight hadoop MapReduce használata](hdinsight-use-mapreduce.md)

Egyéb módjaival kapcsolatos további információk a HDInsight Hadoop-keretrendszerrel használhatja:

* [Apache Hive használata a HDInsight Apache Hadoop-keretrendszerrel](hdinsight-use-hive.md)
* [Az Apache Pig használata a HDInsight Apache Hadoop-keretrendszerrel](hdinsight-use-pig.md)
