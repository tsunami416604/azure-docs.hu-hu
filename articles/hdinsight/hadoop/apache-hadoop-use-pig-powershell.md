---
title: Hadoop a Pig használata a HDInsight – Azure PowerShell használatával
description: Ismerje meg, hogyan lehet elküldeni egy Hadoop-fürtöt az Azure PowerShell használatával HDInsight Pig-feladatok.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: jasonh
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: d8a729177328b2f6f4e7e75f133b91ddb4db5a61
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39597592"
---
# <a name="use-azure-powershell-to-run-pig-jobs-with-hdinsight"></a>A HDInsight Pig-feladatok futtatása az Azure PowerShell használatával

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Ez a dokumentum azt szemlélteti, a Hadoop HDInsight-fürtön a Pig-feladatok elküldése az Azure PowerShell-lel. A Pig lehetővé teszi a modellek adatátalakításokat írni MapReduce-feladatok (a Pig latin betűs) nyelv használatával helyett leképezése, és csökkentheti a funkciók.

> [!NOTE]
> Ez a dokumentum nem biztosít a Pig Latin utasításokkal a példákban használt mire részletes leírását. A Pig Latin ebben a példában használt kapcsolatos információkért lásd: [a Pig használata a hadooppal a HDInsight](hdinsight-use-pig.md).

## <a id="prereq"></a>Előfeltételek

* **Egy Azure HDInsight-fürt**

  > [!IMPORTANT]
  > A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Munkaállomás Azure PowerShell-lel**.

## <a id="powershell"></a>A Pig-feladat futtatása

Az Azure PowerShell biztosít *parancsmagok* , amelyek engedélyezik a HDInsight Pig-feladatok távoli futtatását. Belsőleg, használja a PowerShell a REST-hívások [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) a HDInsight-fürtön futó.

A következő parancsmagok használhatók, amikor Pig-feladatokat futtat egy távoli HDInsight-fürtön:

* **Connect-AzureRmAccount**: hitelesíti az Azure PowerShell az Azure-előfizetéshez.
* **Új AzureRmHDInsightPigJobDefinition**: létrehoz egy *feladat definíciójának* -a megadott Pig Latin utasításokkal.
* **Start-AzureRmHDInsightJob**: a feladat definíciója HDInsight küld, és elindítja a feladatot. A *feladat* objektumot ad vissza.
* **Wait-AzureRmHDInsightJob**: a feladat objektumot használ a feladat állapotának ellenőrzéséhez. Arra vár, amíg a feladat befejeződött, vagy a rendszer túllépte a várakozási idő.
* **Get-AzureRmHDInsightJobOutput**: a feladat kimenetének beolvasása.

A következő lépések bemutatják, hogyan lehet ezeket a parancsmagokat használja a HDInsight-fürtön futó feladatok futtatásához.

1. Egy szerkesztővel, mentse a következő kódot, **pigjob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-pig/use-pig.ps1?range=5-51)]

1. Nyisson meg egy új Windows PowerShell-parancssort. Módosítsa a könyvtárat a helyét a **pigjob.ps1** fájlt, majd futtassa a parancsfájlt a következő paranccsal:

        .\pigjob.ps1

    Jelentkezzen be az Azure-előfizetés kéri. Ezt követően a rendszer megkéri a HTTPs vagy rendszergazdai fiók nevét és a HDInsight-fürthöz tartozó jelszót.

2. A feladat befejezését követően kell visszaadnia információkat az alábbi szöveghez hasonló:

        Start the Pig job ...
        Wait for the Pig job to complete ...
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a id="troubleshooting"></a>Hibaelhárítás

Ha semmilyen adatot nem ad vissza, ha a feladat befejeződik, tekintse meg a hibanaplókat. A feladat hibaadatok megtekintéséhez adja hozzá a következő parancs végéhez a **pigjob.ps1** fájlt, és mentse, majd futtassa újra.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Ez a parancsmag adja vissza az információkat arról, hogy STDERR feladat feldolgozása közben.

## <a id="summary"></a>Summary (Összefoglalás)
Látható, az Azure PowerShell Pig-feladatok futtatásához egy HDInsight-fürtön, a feladat állapotának nyomon és lekérni a kimeneti egyszerű módszert biztosít.

## <a id="nextsteps"></a>Következő lépések
A HDInsight Pig általános tájékoztatást:

* [A Pig használata a HDInsight Hadoop-keretrendszerrel](hdinsight-use-pig.md)

Egyéb módjaival kapcsolatos további információk a HDInsight Hadoop-keretrendszerrel használhatja:

* [A Hive használata a HDInsight Hadoop-keretrendszerrel](hdinsight-use-hive.md)
* [A MapReduce használata a HDInsight Hadoop](hdinsight-use-mapreduce.md)
