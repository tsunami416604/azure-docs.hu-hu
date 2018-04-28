---
title: Hadoop a Pig használata a HDInsight - Azure PowerShell |} Microsoft Docs
description: Útmutató az Azure PowerShell hdinsight Hadoop-fürthöz Pig feladatok elküldéséhez.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 737089c1-b494-4387-9def-7b4dac3be532
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 0c06289625ab18dad134c235fc28fd5c775955eb
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="use-azure-powershell-to-run-pig-jobs-with-hdinsight"></a>A Pig-feladatok futtatása a HDInsight az Azure PowerShell használatával

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Ez a dokumentum egy példát az Azure PowerShell elküldeni a Pig-feladatokhoz a Hadoop on HDInsight-fürt számára. A Pig MapReduce-feladatok írni egy nyelv (a Pig latin betűs) használatával adott modellek adatátalakítást helyett hozzárendelését és funkciók teszi lehetővé.

> [!NOTE]
> Ez a dokumentum nem biztosít a Pig Latin utasításokat a példákban szereplő mire részletes leírását. A Pig Latin ebben a példában használt kapcsolatos információkért lásd: [a Pig használata a hdinsight Hadoop](hdinsight-use-pig.md).

## <a id="prereq"></a>Előfeltételek

* **Egy Azure HDInsight-fürt**

  > [!IMPORTANT]
  > A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Munkaállomás Azure PowerShell-lel**.

## <a id="powershell"></a>A Pig-feladat futtatása

Az Azure PowerShell biztosít *parancsmagok* , amelyek lehetővé teszik, hogy távolról ugyanúgy futtathatják a HDInsight a Pig-feladatokhoz. Belsőleg, PowerShell használja a többi hívások [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) futó a HDInsight-fürthöz.

A következő parancsmagok használhatók a Pig-feladatokhoz egy távoli HDInsight-fürt futtatásakor:

* **Connect-AzureRmAccount**: az Azure-előfizetések az Azure PowerShell hitelesíti.
* **Új AzureRmHDInsightPigJobDefinition**: létrehoz egy *definition feladat* a megadott Pig Latin utasítás használatával.
* **Start-AzureRmHDInsightJob**: a feladat definíciójához küld HDInsight, és elindítja a feladatot. A *feladat* objektumot ad vissza.
* **Várjon, amíg-AzureRmHDInsightJob**: a feladat állapotának ellenőrzése a feladatobjektum használja. Arra vár, amíg a feladat befejeződött, vagy a várakozási idő túl lett lépve.
* **Get-AzureRmHDInsightJobOutput**: a feladat kimenetének beolvasása.

A következő lépések bemutatják, hogyan lehet ezeket a parancsmagokat használja a HDInsight-fürt a feladat futtatásához.

1. Egy szerkesztővel, az alábbi kód, Mentés **pigjob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-pig/use-pig.ps1?range=5-51)]

1. Nyisson meg egy új Windows PowerShell parancssort. Módosítsa a könyvtárat, hol található a **pigjob.ps1** fájlt, majd futtassa a parancsfájlt a következő paranccsal:

        .\pigjob.ps1

    Jelentkezzen be az Azure-előfizetéshez kéri. Ezt követően meg kell adnia azokat a a HTTPs/rendszergazda fiók nevét és jelszavát a HDInsight-fürthöz.

2. A feladat befejezése után, az kell visszaadnia információ az alábbihoz hasonló:

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

Ha nem áll rendelkezésre információ ad vissza, ha a feladat befejeződik, tekintse meg a hibanaplókat. Hiba történt a feladat információinak megtekintése, vegye fel a következő parancsot végén a **pigjob.ps1** fájl, mentse, majd futtassa újból.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Ez a parancsmag, feladat feldolgozása közben STDERR írni olyan információkat ad vissza.

## <a id="summary"></a>Summary (Összefoglalás)
Ahogy látja, Azure PowerShell itt egyszerűen futtassa a Pig-feladatokhoz a HDInsight-fürtöt, figyelheti a feladat állapotát és a kimeneti beolvasása.

## <a id="nextsteps"></a>Következő lépések
Általános információk a hdinsight Pig:

* [A Pig használata a HDInsight Hadoop](hdinsight-use-pig.md)

Más módszerekkel kapcsolatos információk a HDInsight Hadoop dolgozhat:

* [A Hive használata a hdinsight Hadoop](hdinsight-use-hive.md)
* [A HDInsight Hadoop MapReduce használata](hdinsight-use-mapreduce.md)
