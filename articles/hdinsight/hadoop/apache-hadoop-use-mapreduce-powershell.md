---
title: A MapReduce és a PowerShell használata az Apache Hadoop segítségével – Azure HDInsight
description: Megtudhatja, hogyan használhatja a PowerShellt a MapReduce feladatok távoli futtatására az Apache Hadoop segítségével a HDInsighton.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/08/2020
ms.openlocfilehash: b3c1abb7bff54e3e2d294b073b867c6c0e06f482
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75830071"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-powershell"></a>MapReduce feladatok futtatása az Apache Hadoop segítségével a HDInsighton a PowerShell használatával

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Ez a dokumentum egy példa az Azure PowerShell használatával egy MapReduce feladat futtatásához egy Hadoop HDInsight-fürtön.

## <a name="prerequisites"></a>Előfeltételek

* Apache Hadoop-fürt a HDInsighton. Lásd: [Apache Hadoop-fürtök létrehozása az Azure Portalon.](../hdinsight-hadoop-create-linux-clusters-portal.md)

* A PowerShell [Az modul](https://docs.microsoft.com/powershell/azure/overview) telepítve van.

## <a name="run-a-mapreduce-job"></a>MapReduce feladat futtatása

Az Azure PowerShell *olyan parancsmagokat* biztosít, amelyek lehetővé teszik a MapReduce feladatok távoli futtatását a HDInsighton. Belsőleg powershell rest-hívásokat kezdeményez a HDInsight-fürtön futó [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (korábbi nevén Templeton) számára.

A következő parancsmagok a MapReduce feladatok távoli HDInsight-fürtben való futtatásakor használatosak.

|Parancsmag | Leírás |
|---|---|
|Connect-AzAccount|Hitelesíti az Azure PowerShellt az Azure-előfizetésében.|
|Új-AzHDInsightMapReducejobdefinition|Új *feladatdefiníciót* hoz létre a megadott MapReduce információ használatával.|
|Start-AzHDInsightJob|Elküldi a feladatdefiníciót a HDInsightnak, és elindítja a feladatot. A *rendszer egy feladatobjektumot* ad vissza.|
|Wait-AzHDInsightJob|A feladatobjektum segítségével ellenőrzi a feladat állapotát. Megvárja, amíg a feladat befejeződik, vagy a várakozási idő túllépi.|
|Get-AzHDInsight JobOutput|A feladat kimenetének lekéréséhez használt.|

Az alábbi lépések bemutatják, hogyan használhatja ezeket a parancsmagokat a HDInsight-fürtben egy feladat futtatásához.

1. Szerkesztő használatával mentse a következő kódot **mapreducejob.ps1**néven.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. Nyisson meg egy új **Azure PowerShell-parancssort.** Módosítsa a könyvtárakat a **mapreducejob.ps1** fájl helyére, majd a parancsfájl futtatásához használja a következő parancsot:

        .\mapreducejob.ps1

    A parancsfájl futtatásakor a rendszer kéri a HDInsight-fürt nevét és a fürt bejelentkezését. Előfordulhat, hogy az Azure-előfizetéshitelesítésre is rákérdez.

3. Amikor a feladat befejeződik, a következő höz hasonló kimenetet kap:

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    Ez a kimenet azt jelzi, hogy a feladat sikeresen befejeződött.

    > [!NOTE]  
    > Ha az **ExitCode** értéke nem 0, olvassa el a [Hibaelhárítás című témakört.](#troubleshooting)

    Ez a példa a letöltött fájlokat egy **output.txt** fájlba is tárolja abban a könyvtárban, amelyből a parancsfájlt futtatja.

### <a name="view-output"></a>Kimenet megtekintése

A feladat által létrehozott szavak és számok megtekintéséhez nyissa meg a **output.txt** fájlt egy szövegszerkesztőben.

> [!NOTE]  
> A MapReduce feladat kimeneti fájljai nem módosíthatók. Tehát, ha újra futtatja ezt a mintát, meg kell változtatnia a kimeneti fájl nevét.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a feladat befejezésekor nem ad vissza információt, tekintse meg a feladat hibáit. A feladat hibainformációinak megtekintéséhez adja hozzá a következő parancsot a **mapreducejob.ps1** fájl végéhez. Ezután mentse a fájlt, és futtassa újra a parancsfájlt.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Ez a parancsmag a feladat futása közben az STDERR-be írt információt adja vissza.

## <a name="next-steps"></a>További lépések

Mint látható, az Azure PowerShell egy egyszerű módja a MapReduce feladatok futtatásához egy HDInsight-fürtön, a feladat állapotának figyelése és a kimenet lekérése. A Hadoop hdinsight-on való egyéb működéséről a következőket szeretné tudni:

* [A MapReduce használata a HDInsight Hadoopon](hdinsight-use-mapreduce.md)
* [Az Apache Hive használata az Apache Hadoop segítségével a HDInsighton](hdinsight-use-hive.md)
