---
title: "Hadoop Hive használata a hdinsight - Azure PowerShell |} Microsoft Docs"
description: "HDInsight Hadoop Hive-lekérdezéseket futtatni a PowerShell segítségével."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: cb795b7c-bcd0-497a-a7f0-8ed18ef49195
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/27/2017
ms.author: larryfr
ms.openlocfilehash: 95bfab18a6a8a9ad9eb547179a3205ae4b186213
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="run-hive-queries-using-powershell"></a>PowerShell-lel Hive-lekérdezések futtatása
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Ez a dokumentum az Azure PowerShell használatával az Azure-erőforráscsoport módban a Hive-lekérdezések futtatásához egy Hadoop on HDInsight-fürt példaként szolgál.

> [!NOTE]
> Ez a dokumentum nem biztosít a HiveQL utasításokat a példákban használt mire részletes leírását. Az ebben a példában használt HiveQL információkért lásd: [használata a hdinsight Hadoop Hive](hdinsight-use-hive.md).

**Előfeltételek**

* **Egy Azure HDInsight fürt**: nem számít, hogy a fürt Windows vagy Linux-alapú.

  > [!IMPORTANT]
  > A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Munkaállomás Azure PowerShell-lel**.

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

## <a name="run-hive-queries-using-azure-powershell"></a>Az Azure PowerShell Hive-lekérdezések futtatása

Az Azure PowerShell biztosít *parancsmagok* , amelyek lehetővé teszik, hogy távolról ugyanúgy futtathatják a HDInsight Hive-lekérdezéseket. Belső, a parancsmagok hívások REST való [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) a HDInsight-fürtre.

A következő parancsmagok használhatók egy távoli HDInsight-fürtöt a Hive-lekérdezések futtatásakor:

* **Adja hozzá-AzureRmAccount**: Azure PowerShell hitelesíti az Azure-előfizetéshez.
* **Új AzureRmHDInsightHiveJobDefinition**: létrehoz egy *definition feladat* a megadott HiveQL utasítás használatával.
* **Start-AzureRmHDInsightJob**: a feladat definíciójához küld HDInsight, és elindítja a feladatot. A *feladat* objektumot ad vissza.
* **Várjon, amíg-AzureRmHDInsightJob**: a feladat állapotának ellenőrzése a feladatobjektum használja. Arra vár, amíg a feladat befejeződik, vagy a várakozási ideje lejár.
* **Get-AzureRmHDInsightJobOutput**: a feladat kimenetének beolvasása.
* **Invoke-AzureRmHDInsightHiveJob**: HiveQL utasítás futtatásához használt. Ez a parancsmag blokkolja a lekérdezés befejeződött, majd az eredményeket ad vissza.
* **Használjon-AzureRmHDInsightCluster**: a jelenlegi fürthöz való használatra beállítja a **Invoke-AzureRmHDInsightHiveJob** parancs.

A következő lépések bemutatják, hogyan lehet ezeket a parancsmagokat használja a feladat futtatásához a HDInsight fürt:

1. Egy szerkesztővel, az alábbi kód, Mentés **hivejob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Nyisson meg egy új **Azure PowerShell** parancssort. Módosítsa a könyvtárat, hol található a **hivejob.ps1** fájlt, majd futtassa a parancsfájlt a következő paranccsal:

        .\hivejob.ps1

    A parancsprogram futtatásakor kéri a fürt a fürt neve és a HTTPS/rendszergazdai fiók hitelesítő adatait adja meg. Előfordulhat, hogy is kérni fogja-e jelentkezni az Azure-előfizetéshez.

3. A feladat befejeződik, ha olyan információkat ad vissza az alábbihoz hasonló:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. A korábbiak **Invoke-struktúra** segítségével futtassa a lekérdezést, és a válaszra. A következő parancsfájl segítségével tekintse meg az Invoke-struktúra működése:

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    A kimeneti néz ki a következő szöveget:

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]
   > Hosszabb HiveQL lekérdezések esetén használhatja az Azure PowerShell **ide-karakterláncok** parancsmag vagy a HiveQL parancsfájlok. Az alábbi kódrészletben láthatja, hogyan használható a **Invoke-struktúra** parancsmag HiveQL parancsfájl futtatásához. Fel kell tölteni a HiveQL-parancsfájlt, a wasb: / /.
   >
   > `Invoke-AzureRmHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > További információ **ide-karakterláncok**, lásd: <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">használata a Windows PowerShell ide-karakterláncok</a>.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha nem áll rendelkezésre információ ad vissza, ha a feladat befejeződik, tekintse meg a hibanaplókat. Hiba történt a feladat információinak megtekintése, adja hozzá a következő végének a **hivejob.ps1** fájl, mentse, majd futtassa újból.

```powershell
# Print the output of the Hive job.
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Ez a parancsmag STDERR feladat feldolgozása során írt olyan információkat ad vissza.

## <a name="summary"></a>Összefoglalás

Ahogy látja, Azure PowerShell könnyedén futtathat Hive-lekérdezéseket a HDInsight-fürtöt, figyelheti a feladat állapotát és a kimeneti beolvasása.

## <a name="next-steps"></a>Következő lépések

Általános információk a hdinsight Hive:

* [A Hive használata a hdinsight Hadoop](hdinsight-use-hive.md)

Más módszerekkel kapcsolatos információk a HDInsight Hadoop dolgozhat:

* [A Pig használata a HDInsight Hadoop](hdinsight-use-pig.md)
* [A HDInsight Hadoop MapReduce használata](hdinsight-use-mapreduce.md)
