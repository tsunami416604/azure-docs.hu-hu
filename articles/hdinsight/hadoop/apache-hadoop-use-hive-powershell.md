---
title: Az Apache Hive használata a PowerShelllel a HDInsightban – Azure
description: Apache Hive-lekérdezések futtatása a PowerShell használatával az Apache Hadoopban az Azure HDInsightban
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: deaa934b257fab74830d75e308a283e7608dc590
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552593"
---
# <a name="run-apache-hive-queries-using-powershell"></a>Apache Hive-lekérdezések futtatása a PowerShell használatával

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Ez a dokumentum egy példa az Azure PowerShell használatával apache hive-lekérdezések futtatásához egy Apache Hadoop HDInsight-fürtön.

> [!NOTE]  
> Ez a dokumentum nem nyújt részletes leírást arról, hogy a példákban használt HiveQL-utasítások mit tartalmaznak. A példában használt HiveQL-ről az [Apache Hive használata az Apache Hadoop használatával a HDInsight-on című](hdinsight-use-hive.md)témakörben talál további információt.

## <a name="prerequisites"></a>Előfeltételek

* Apache Hadoop-fürt a HDInsighton. Lásd: [Első lépések a HDInsight linuxos alkalmazásával.](./apache-hadoop-linux-tutorial-get-started.md)

* A PowerShell [Az modul](https://docs.microsoft.com/powershell/azure/overview) telepítve van.

## <a name="run-a-hive-query"></a>Hive-lekérdezések futtatása

Az Azure PowerShell *olyan parancsmagokat* biztosít, amelyek lehetővé teszik a HIVe-lekérdezések távoli futtatását a HDInsighton. Belsőleg a parancsmagok REST-hívásokat kezdeményeznek a [WEBHCat-nek](https://cwiki.apache.org/confluence/display/Hive/WebHCat) a HDInsight-fürtön.

A következő parancsmagok a Hive-lekérdezések távoli HDInsight-fürtön történő futtatásakor használatosak:

* `Connect-AzAccount`: Hitelesíti az Azure PowerShellt az Azure-előfizetésben.
* `New-AzHDInsightHiveJobDefinition`: A megadott HiveQL utasítások használatával hoz létre *feladatdefiníciót.*
* `Start-AzHDInsightJob`: Elküldi a feladatdefiníciót a HDInsightnak, és elindítja a feladatot. A *rendszer egy feladatobjektumot* ad vissza.
* `Wait-AzHDInsightJob`: A feladatobjektum mal a feladat állapotának ellenőrzésére használja. Megvárja, amíg a feladat befejeződik, vagy a várakozási idő túllépi.
* `Get-AzHDInsightJobOutput`: A feladat kimenetének lekéréséhez használható.
* `Invoke-AzHDInsightHiveJob`: HiveQL utasítások futtatására szolgál. Ez a parancsmag blokkolja a lekérdezés befejeződik, majd visszaadja az eredményeket.
* `Use-AzHDInsightCluster`: Beállítja a `Invoke-AzHDInsightHiveJob` parancshoz használni az aktuális fürtöt.

Az alábbi lépések bemutatják, hogyan használhatja ezeket a parancsmagokat egy feladat futtatásához a HDInsight-fürtben:

1. Szerkesztő használatával mentse a következő `hivejob.ps1`kódot a módon.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Nyisson meg egy új **Azure PowerShell-parancssort.** Módosítsa a könyvtárakat a `hivejob.ps1` fájl helyére, majd a következő paranccsal futtassa a parancsfájlt:

        .\hivejob.ps1

    Amikor a parancsfájl fut, a rendszer kéri, hogy adja meg a fürt nevét és a HTTPS/Cluster felügyeleti fiók hitelesítő adatait. Előfordulhat, hogy az Azure-előfizetésbe való bejelentkezésre is rákérdez.

3. Amikor a feladat befejeződik, a következő höz hasonló információkat ad vissza:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Mint korábban `Invoke-Hive` említettük, lehet használni, hogy futtasson egy lekérdezést, és várja meg a választ. Az Invoke-Hive metódus működésének megtekintéséhez használja a következő parancsfájlt:

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    A kimenet a következő szövegnek tűnik:

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]  
   > Hosszabb HiveQL-lekérdezések esetén használhatja az Azure PowerShell **Here-Karakterláncok** parancsmag vagy HiveQL parancsfájlok. A következő kódrészlet bemutatja, `Invoke-Hive` hogyan használhatja a parancsmagot a HiveQL parancsfájl futtatásához. A HiveQL parancsfájlt fel kell tölteni a wasbs://.
   >
   > `Invoke-AzHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Az itt **karakterláncokról**további információt a <a href="https://technet.microsoft.com/library/ee692792.aspx" target="_blank">Windows PowerShell használata itt-karakterláncok című témakörben talál.</a>

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a feladat befejezésekor nem ad vissza információt, tekintse meg a hibanaplókat. A feladat hibaadatainak megtekintéséhez adja hozzá a `hivejob.ps1` következőket a fájl végéhez, mentse, majd futtassa újra.

```powershell
# Print the output of the Hive job.
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Ez a parancsmag a feladat feldolgozása során az STDERR-nek írt információkat adja vissza.

## <a name="summary"></a>Összefoglalás

Mint látható, az Azure PowerShell egyszerű módot biztosít a Hive-lekérdezések futtatására egy HDInsight-fürtben, a feladat állapotának figyelésére és a kimenet lekérésére.

## <a name="next-steps"></a>További lépések

A HIVe-ről a HDInsightban található általános tudnivalókért:

* [Az Apache Hive használata az Apache Hadoop segítségével a HDInsighton](hdinsight-use-hive.md)

A Hadoop hdinsight-on való egyéb működéséről a következőket szeretné tudni:

* [A MapReduce használata az Apache Hadoop segítségével a HDInsighton](hdinsight-use-mapreduce.md)
