---
title: A Hadoop – Azure HDInsight MapReduce és a PowerShell használata
description: Ismerje meg, hogyan lehet távolról futtatni a HDInsight Hadoop-MapReduce-feladatok PowerShell használatával.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: jasonh
ms.openlocfilehash: cab6fc652fa11db7dd1e9e9ae7f0a1a634dca3b0
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39591820"
---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-powershell"></a>MapReduce-feladatok futtatása és a Hadoop HDInsight PowerShell használatával

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Ez a dokumentum azt szemlélteti, egy MapReduce-feladatot futtatni a Hadoop HDInsight-fürtön az Azure PowerShell használatával.

## <a id="prereq"></a>Előfeltételek

* **Egy Azure HDInsight (Hadoop on HDInsight)-fürt**

  > [!IMPORTANT]
  > A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Munkaállomás Azure PowerShell-lel**.

## <a id="powershell"></a>A MapReduce-feladat futtatása

Az Azure PowerShell biztosít *parancsmagok* , amelyek engedélyezik a HDInsight MapReduce-feladatok távoli futtatását. Belsőleg, PowerShell, REST-hívást hajt végre [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (korábbi nevén templeton eszközön keresztül végzett) a HDInsight-fürtön futó.

A következő parancsmagok használhatók MapReduce-feladatok egy távoli HDInsight-fürtön való futtatáskor.

* **Connect-AzureRmAccount**: az Azure PowerShell végzi a hitelesítést az Azure-előfizetéshez.

* **Új AzureRmHDInsightMapReduceJobDefinition**: létrehoz egy új *feladat definíciójának* MapReduce megadott információk segítségével.

* **Start-AzureRmHDInsightJob**: a feladat definíciója HDInsight küld, és elindítja a feladatot. A *feladat* objektumot ad vissza.

* **Wait-AzureRmHDInsightJob**: a feladat objektumot használ a feladat állapotának ellenőrzéséhez. Arra vár, amíg a feladat befejeződik, vagy túllépi a várakozási idő.

* **Get-AzureRmHDInsightJobOutput**: a feladat kimenetének beolvasása.

A következő lépések bemutatják, hogyan használja ezeket a parancsmagokat futtathat feladatokat a HDInsight-fürtben.

1. Egy szerkesztővel, mentse a következő kódot, **mapreducejob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

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
Get-AzureRmHDInsightJobOutput `
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

* [A Hive használata a HDInsight Hadoop-keretrendszerrel](hdinsight-use-hive.md)
* [A Pig használata a HDInsight Hadoop-keretrendszerrel](hdinsight-use-pig.md)
