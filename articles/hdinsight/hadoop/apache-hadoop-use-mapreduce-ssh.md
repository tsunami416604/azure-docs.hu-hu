---
title: "A HDInsight - Azure Hadoop MapReduce és SSH kapcsolatot |} Microsoft Docs"
description: "Útmutató az SSH használata a HDInsight Hadoop használatával MapReduce-feladatok futtatásához."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 844678ba-1e1f-4fda-b9ef-34df4035d547
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2018
ms.author: larryfr
ms.openlocfilehash: 08c67168cde60c1b7551806da8542c98dcb82f55
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="use-mapreduce-with-hadoop-on-hdinsight-with-ssh"></a>Az SSH hdinsight Hadoop MapReduce használata

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Útmutató a HDInsight a Secure Shell (SSH) kapcsolatról MapReduce-feladatok elküldése.

> [!NOTE]
> Ha már ismeri a Linux-alapú Hadoop-kiszolgálókat használ, de még nem ismeri a HDInsight, [Linux-alapú HDInsight tippek](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Előfeltételek

* (A HDInsight Hadoop) a Linux-alapú HDInsight-fürt

  > [!IMPORTANT]
  > A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Egy SSH-ügyfél. További információkért lásd: [az SSH a Hdinsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md)

## <a id="ssh"></a>Csatlakozzon SSH

Csatlakozzon a fürthöz SSH használatával. A következő parancs például nevű fürthöz csatlakozó **myhdinsight**:

```bash
ssh admin@myhdinsight-ssh.azurehdinsight.net
```

**Ha egy tanúsítvány-kulcsot használ SSH hitelesítés**, adja meg a titkos kulcs helyét az ügyfélrendszeren szeretne:

```bash
ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net
```

**Ha jelszót használhat SSH hitelesítés**, meg kell adnia a jelszót.

Az SSH és a HDInsight együttes használatával további információkért lásd: [az SSH a Hdinsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="hadoop"></a>Hadoop-parancsok használata

1. Miután csatlakozott a HDInsight-fürthöz, a következő parancs segítségével indítsa el a MapReduce feladatot:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    A paranccsal elindítja a `wordcount` osztályt, amely tartalmazza a `hadoop-mapreduce-examples.jar` fájlt. Használja a `/example/data/gutenberg/davinci.txt` bemeneti és kimeneti dokumentumot tárolódik `/example/data/WordCountOutput`.

    > [!NOTE]
    > A MapReduce feladatot és a példaadatokat kapcsolatos további információkért lásd: [használata MapReduce a Hadoop on HDInsight](hdinsight-use-mapreduce.md).

2. A feladat részletei bocsát ki, akkor feldolgozza, és olyan információkat ad vissza az alábbihoz hasonló a feladat befejezése után:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. A feladat befejezése után, használja a következő parancsot a kimeneti fájlok listázásához:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Ez a parancs két fájlt megjelenítése `_SUCCESS` és `part-r-00000`. A `part-r-00000` fájl tartalmazza a kimenet ehhez a feladathoz.

    > [!NOTE]
    > Bizonyos MapReduce-feladatok az eredmények lehet, hogy e osztani több **rész-r-###** fájlokat. Ha igen, használja a ### utótag fájlok sorrendjét.

4. A kimenet megtekintéséhez használja a következő parancsot:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Ez a parancs megjeleníti a szavakat, amelyek szerepelnek a **wasb://example/data/gutenberg/davinci.txt** fájl- és a szám, ahányszor minden szó történt. A következő szöveget a fájlban található adatok példája:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>Summary (Összefoglalás)

Ahogy látja, a Hadoop parancsok MapReduce-feladatok futtatása a HDInsight-fürtöt, és nézze meg a feladat kimenetére egyszerűen adja meg.

## <a id="nextsteps"></a>Következő lépések

Általános információk a hdinsight MapReduce-feladatok:

* [A HDInsight Hadoop MapReduce használata](hdinsight-use-mapreduce.md)

Más módszerekkel kapcsolatos információk a HDInsight Hadoop dolgozhat:

* [A Hive használata a hdinsight Hadoop](hdinsight-use-hive.md)
* [A Pig használata a HDInsight Hadoop](hdinsight-use-pig.md)
