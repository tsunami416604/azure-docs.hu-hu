---
title: Hadoop Hive használata a hdinsight - Azure PowerShell |} Microsoft Docs
description: HDInsight Hadoop Hive-lekérdezéseket futtatni a PowerShell segítségével.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: cb795b7c-bcd0-497a-a7f0-8ed18ef49195
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/26/2018
ms.author: larryfr
ms.openlocfilehash: 044c901799ff7acae1e27602b84802f6b5f70f05
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="run-hive-queries-using-powershell"></a>PowerShell-lel Hive-lekérdezések futtatása
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Ez a dokumentum az Azure PowerShell használatával az Azure-erőforráscsoport módban a Hive-lekérdezések futtatásához egy Hadoop on HDInsight-fürt példaként szolgál.

> [!NOTE]
> Ez a dokumentum nem biztosít a HiveQL utasításokat a példákban használt mire részletes leírását. Az ebben a példában használt HiveQL információkért lásd: [használata a hdinsight Hadoop Hive](hdinsight-use-hive.md).

## <a name="prerequisites"></a>Előfeltételek

* A Linux-alapú Hadoop a HDInsight fürt 3.4 vagy újabb verziója.

  > [!IMPORTANT]
  > A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Az Azure PowerShell ügyfél.

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

## <a name="run-a-hive-query"></a>Hive-lekérdezések futtatása

Az Azure PowerShell biztosít *parancsmagok* , amelyek lehetővé teszik, hogy távolról ugyanúgy futtathatják a HDInsight Hive-lekérdezéseket. Belső, a parancsmagok hívások REST való [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) a HDInsight-fürtre.

A következő parancsmagok használhatók egy távoli HDInsight-fürtöt a Hive-lekérdezések futtatásakor:

* `Add-AzureRmAccount`: Az Azure-előfizetések az Azure PowerShell hitelesíti.
* `New-AzureRmHDInsightHiveJobDefinition`: Létrehoz egy *definition feladat* a megadott HiveQL utasítás használatával.
* `Start-AzureRmHDInsightJob`: A feladat definíciójához küld HDInsight, és elindítja a feladatot. A *feladat* objektumot ad vissza.
* `Wait-AzureRmHDInsightJob`: A feladatobjektum használja a feladat állapotának ellenőrzéséhez. Arra vár, amíg a feladat befejeződik, vagy a várakozási ideje lejár.
* `Get-AzureRmHDInsightJobOutput`: A feladat eredményének lekérdezésére használja.
* `Invoke-AzureRmHDInsightHiveJob`: A HiveQL utasítás futtatásához használt. Ez a parancsmag blokkolja a lekérdezés befejeződött, majd az eredményeket ad vissza.
* `Use-AzureRmHDInsightCluster`: A jelenlegi fürthöz való használatra beállítja a `Invoke-AzureRmHDInsightHiveJob` parancsot.

A következő lépések bemutatják, hogyan lehet ezeket a parancsmagokat használja a feladat futtatásához a HDInsight fürt:

1. Egy szerkesztővel, az alábbi kód, Mentés `hivejob.ps1`.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Nyisson meg egy új **Azure PowerShell** parancssort. Módosítsa a könyvtárat, hol található a `hivejob.ps1` fájlt, majd futtassa a parancsfájlt a következő paranccsal:

        .\hivejob.ps1

    A parancsprogram futtatásakor kéri a fürt a fürt neve és a HTTPS/rendszergazdai fiók hitelesítő adatait adja meg. Előfordulhat, hogy is kérni fogja-e jelentkezni az Azure-előfizetéshez.

3. A feladat befejeződik, ha olyan információkat ad vissza az alábbihoz hasonló:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. A korábbiak `Invoke-Hive` segítségével futtassa a lekérdezést, és a válaszra. A következő parancsfájl segítségével tekintse meg az Invoke-struktúra működése:

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    A kimeneti néz ki a következő szöveget:

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]
   > Hosszabb HiveQL lekérdezések esetén használhatja az Azure PowerShell **ide-karakterláncok** parancsmag vagy a HiveQL parancsfájlok. Az alábbi kódrészletben láthatja, hogyan használható a `Invoke-Hive` parancsmag HiveQL parancsfájl futtatásához. Fel kell tölteni a HiveQL-parancsfájlt, a wasb: / /.
   >
   > `Invoke-AzureRmHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > További információ **ide-karakterláncok**, lásd: <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">használata a Windows PowerShell ide-karakterláncok</a>.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha nem áll rendelkezésre információ ad vissza, ha a feladat befejeződik, tekintse meg a hibanaplókat. Hiba történt a feladat információinak megtekintése, adja hozzá a következő végének a `hivejob.ps1` fájl, mentse, majd futtassa újból.

```powershell
# Print the output of the Hive job.
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Ez a parancsmag STDERR feladat feldolgozása során írt olyan információkat ad vissza.

## <a name="summary"></a>Összegzés

Ahogy látja, Azure PowerShell könnyedén futtathat Hive-lekérdezéseket a HDInsight-fürtöt, figyelheti a feladat állapotát és a kimeneti beolvasása.

## <a name="next-steps"></a>További lépések

Általános információk a hdinsight Hive:

* [A Hive használata a hdinsight Hadoop](hdinsight-use-hive.md)

Más módszerekkel kapcsolatos információk a HDInsight Hadoop dolgozhat:

* [A Pig használata a HDInsight Hadoop](hdinsight-use-pig.md)
* [A HDInsight Hadoop MapReduce használata](hdinsight-use-mapreduce.md)
