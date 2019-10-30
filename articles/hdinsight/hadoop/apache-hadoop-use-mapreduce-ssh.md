---
title: MapReduce és SSH-kapcsolatok Apache Hadoop-Azure HDInsight
description: Ismerje meg, hogy az SSH használatával hogyan futtathat MapReduce-feladatokat a HDInsight Apache Hadoop használatával.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: b4075de1a184896d598c11d09ae2b2bda5e257ed
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044511"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>A MapReduce használata a HDInsight-mel az SSH-val Apache Hadoop

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Ismerje meg, hogyan küldhet MapReduce-feladatokat egy Secure Shell-(SSH-) kapcsolatból a HDInsight-be.

> [!NOTE]
> Ha már ismeri a Linux-alapú Apache Hadoop-kiszolgálók használatát, de most ismerkedik a HDInsight-mel, tekintse meg a [Linux-alapú HDInsight kapcsolatos tippeket](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Előfeltételek

* Linux-alapú HDInsight (Hadoop on HDInsight)-fürt

* Egy SSH-ügyfél. További információ: az [SSH használata a HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)

## <a id="ssh"></a>Az SSH-val való kapcsolat

Kapcsolódjon a fürthöz SSH használatával. Például a következő parancs egy **myhdinsight** nevű fürthöz csatlakozik a **sshuser** fiókban:

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.net
```

**Ha az SSH-hitelesítéshez tanúsítvány-kulcsot használ**, előfordulhat, hogy meg kell adnia a titkos kulcs helyét az ügyfél rendszerén, például:

```bash
ssh -i ~/mykey.key sshuser@myhdinsight-ssh.azurehdinsight.net
```

**Ha az SSH-hitelesítéshez jelszót használ**, meg kell adnia a jelszót, amikor a rendszer kéri.

Az SSH és a HDInsight használatával kapcsolatos további információkért lásd: az [SSH használata a HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="hadoop"></a>Hadoop parancsok használata

1. Miután csatlakozott a HDInsight-fürthöz, a következő paranccsal indíthatja el a MapReduce-feladatot:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Ez a parancs elindítja a `wordcount` osztályt, amely a `hadoop-mapreduce-examples.jar` fájlban található. A `/example/data/gutenberg/davinci.txt`-dokumentumot bemenetként használja, és a kimenetet `/example/data/WordCountOutput`tárolja.

    > [!NOTE]
    > További információ erről a MapReduce-feladatról és a példában szereplő adatokról: a [MapReduce használata a HDInsight-on Apache Hadoop](hdinsight-use-mapreduce.md).

2. A feladatnak a folyamat során kibocsátja a részleteket, és a következő szöveghez hasonló adatokat ad vissza, amikor a feladatok befejeződik:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. Amikor a feladatok befejeződik, a következő paranccsal listázhatja ki a kimeneti fájlokat:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Ez a parancs két fájlt jelenít meg, `_SUCCESS` és `part-r-00000`. A `part-r-00000` fájl tartalmazza a feladatokhoz tartozó kimenetet.

    > [!NOTE]  
    > Egyes MapReduce-feladatok több **rész-r-#** # # # # fájlra oszthatják meg az eredményeket. Ha igen, használja a # # # # # utótagot, hogy jelezze a fájlok sorrendjét.

4. A kimenet megtekintéséhez használja a következő parancsot:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Ez a parancs megjeleníti a **wasb://example/Data/Gutenberg/DaVinci.txt** fájlban található szavak listáját, valamint az egyes szavak előfordulási idejét. A következő szöveg egy példa a fájlban található fájlokra:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>Summary (Összefoglalás)

Amint láthatja, a Hadoop parancsai egyszerűen futtathatják a MapReduce-feladatokat egy HDInsight-fürtön, majd megtekinthetik a feladat kimenetét.

## <a id="nextsteps"></a>Következő lépések

Általános információk a MapReduce-feladatokról a HDInsight-ben:

* [A MapReduce használata a HDInsight-Hadoop](hdinsight-use-mapreduce.md)

További információ a Hadoop a HDInsight-ben való használatával kapcsolatos egyéb módszerekről:

* [Apache Hive használata a HDInsight Apache Hadoop használatával](hdinsight-use-hive.md)
* [Az Apache Pig használata a Apache Hadoop on HDInsight](hdinsight-use-pig.md)
