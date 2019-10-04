---
title: Apache Hive használata a PowerShell-lel a HDInsight-Azure-ban
description: Apache Hive-lekérdezések futtatása a PowerShell használatával Apache Hadoop az Azure HDInsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: 89fa7976b922ba0e40e97b72de5d4eb9a02f0dfd
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736066"
---
# <a name="run-apache-hive-queries-using-powershell"></a>Apache Hive lekérdezések futtatása a PowerShell használatával
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Ez a dokumentum egy példát mutat be Azure PowerShell használatára az Azure erőforráscsoport módban, hogy a HDInsight-fürtben lévő Apache Hadoopon futtassa a kaptár-lekérdezéseket.

> [!NOTE]  
> Ez a dokumentum nem tartalmazza a példákban használt HiveQL-utasítások részletes leírását. Az ebben a példában használt HiveQL kapcsolatos további információkért tekintse meg a [Apache Hive Apache Hadoop használata a HDInsight](hdinsight-use-hive.md)-ben című témakört.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Linux-alapú Apache Hadoop a HDInsight-fürt 3,4-es vagy újabb verziójára.

* Azure PowerShell-ügyféllel rendelkező ügyfél.

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

## <a name="run-a-hive-query"></a>Hive-lekérdezések futtatása

Azure PowerShell olyan *parancsmagokat* biztosít, amelyek lehetővé teszik a kaptár-lekérdezések távoli futtatását a HDInsight. Belsőleg a parancsmagok REST-hívásokat végeznek a HDInsight-fürt [webhcaten](https://cwiki.apache.org/confluence/display/Hive/WebHCat) .

A következő parancsmagok használhatók a kaptár-lekérdezések távoli HDInsight-fürtben való futtatásakor:

* `Connect-AzAccount`: Azure PowerShell hitelesíti az Azure-előfizetését.
* `New-AzHDInsightHiveJobDefinition`: Létrehoz egy *feladatdefiníció* a megadott HiveQL utasítások használatával.
* `Start-AzHDInsightJob`: A HDInsight elküldi a feladatot, és elindítja a feladatot. A rendszer egy *feladatütemezés* visszaadását adja vissza.
* `Wait-AzHDInsightJob`: A feladattípust használja a feladatok állapotának vizsgálatához. Megvárja, amíg a feladatok befejeződik, vagy túllépi a várakozási időt.
* `Get-AzHDInsightJobOutput`: A feladatok kimenetének beolvasására szolgál.
* `Invoke-AzHDInsightHiveJob`: HiveQL utasítások futtatására szolgál. Ez a parancsmag blokkolja a lekérdezés befejeződését, majd visszaadja az eredményeket.
* `Use-AzHDInsightCluster`: Beállítja a `Invoke-AzHDInsightHiveJob` parancshoz használandó aktuális fürtöt.

A következő lépések bemutatják, hogyan használhatja ezeket a parancsmagokat a feladatok futtatásához a HDInsight-fürtben:

1. Szerkesztő használatával mentse a következő kódot `hivejob.ps1`.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Nyisson meg egy új **Azure PowerShell** parancssort. Módosítsa a könyvtárakat a `hivejob.ps1` fájl helyére, majd futtassa a következő parancsot a szkript futtatásához:

        .\hivejob.ps1

    A parancsfájl futtatásakor a rendszer felszólítja, hogy adja meg a fürt nevét és a HTTPS/fürt rendszergazdai fiókjának hitelesítő adatait. A rendszer kérheti, hogy jelentkezzen be az Azure-előfizetésbe.

3. Amikor a feladatok befejeződik, az a következő szöveghez hasonló adatokat ad vissza:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Ahogy azt korábban említettük, `Invoke-Hive` használható egy lekérdezés futtatására és a válaszra való várakozásra. A következő parancsfájl használatával megtekintheti, hogyan működik a Meghívási struktúra:

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    A kimenet a következő szöveghez hasonlít:

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]  
   > A hosszú HiveQL lekérdezések esetében használhatja a Azure PowerShell **itt-Strings** parancsmagot vagy a HiveQL parancsfájl-fájlokat. A következő kódrészlet azt mutatja be, hogyan `Invoke-Hive` használható a parancsmag egy HiveQL parancsfájl futtatásához. A HiveQL parancsfájlt fel kell tölteni a wasb://-be.
   >
   > `Invoke-AzHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > További információ az **itt-sztringekről**: a <a href="https://technet.microsoft.com/library/ee692792.aspx" target="_blank">Windows PowerShell használata itt – karakterláncok</a>.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a rendszer nem ad vissza információt a feladatok befejezésekor, tekintse meg a hibák naplóit. Ha meg szeretné tekinteni a feladattal kapcsolatos hibákat, adja hozzá a következőt `hivejob.ps1` a fájl végéhez, mentse, majd futtassa újra.

```powershell
# Print the output of the Hive job.
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Ez a parancsmag a STDERR írt adatokat adja vissza a feladatok feldolgozásakor.

## <a name="summary"></a>Összegzés

Amint láthatja, a Azure PowerShell egyszerű módszert kínál a kaptár-lekérdezések futtatására egy HDInsight-fürtben, figyelheti a feladat állapotát, és lekérheti a kimenetet.

## <a name="next-steps"></a>További lépések

Általános információk a HDInsight-struktúrával kapcsolatban:

* [Apache Hive használata a HDInsight Apache Hadoop használatával](hdinsight-use-hive.md)

További információ a Hadoop a HDInsight-ben való használatával kapcsolatos egyéb módszerekről:

* [Az Apache Pig használata a Apache Hadoop on HDInsight](hdinsight-use-pig.md)
* [A MapReduce használata a HDInsight Apache Hadoop használatával](hdinsight-use-mapreduce.md)
