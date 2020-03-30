---
title: MapReduce és SSH kapcsolat az Apache Hadoop-mal – Azure HDInsight
description: Ismerje meg, hogyan futtathat SSH-t a MapReduce feladatok futtatásához az Apache Hadoop használatával a HDInsight on HDInsight használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/10/2020
ms.openlocfilehash: 543bc29adc85bd767de9479607d067fadf7b0078
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934704"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>A MapReduce használata az Apache Hadoop segítségével a HDInsight on SSH segítségével

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Ismerje meg, hogyan küldhet mapreduce feladatokat egy biztonságos rendszerhéj (SSH) kapcsolat HDInsight.

> [!NOTE]
> Ha már ismeri a Linux-alapú Apache Hadoop kiszolgálók használatát, de még nem ismeri a HDInsight-kiszolgálókat, olvassa el a [Linux-alapú HDInsight-tippek című témakört.](../hdinsight-hadoop-linux-information.md)

## <a name="prerequisites"></a>Előfeltételek

Apache Hadoop-fürt a HDInsighton. Lásd: [Apache Hadoop-fürtök létrehozása az Azure Portalon.](../hdinsight-hadoop-create-linux-clusters-portal.md)

## <a name="use-hadoop-commands"></a>A Hadoop-parancsok használata

1. Az [ssh paranccsal](../hdinsight-hadoop-linux-use-ssh-unix.md) csatlakozhat a fürthöz. Az alábbi parancs szerkesztésével cserélje le a CLUSTERNAME-t a fürt nevére, majd írja be a parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Miután csatlakozott a HDInsight-fürthöz, a következő paranccsal elindíthat egy MapReduce feladatot:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Ez a `wordcount` parancs elindítja a `hadoop-mapreduce-examples.jar` fájlban található osztályt. A `/example/data/gutenberg/davinci.txt` dokumentumot bemenetként használja, és `/example/data/WordCountOutput`a kimenet a helyen tárolódik.

    > [!NOTE]
    > Erről a MapReduce feladatról és a példaadatokról a MapReduce használata az [Apache Hadoopban a HDInsight ban című témakörben talál](hdinsight-use-mapreduce.md)további információt.

    A feladat a folyamat során részleteket bocsát ki, és a következő szöveghez hasonló információkat ad vissza, amikor a feladat befejeződik:

    ```output
    File Input Format Counters
    Bytes Read=1395666
    File Output Format Counters
    Bytes Written=337623
    ```

1. A feladat befejezéseután a következő paranccsal sorolja fel a kimeneti fájlokat:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Ez a parancs `_SUCCESS` két `part-r-00000`fájlt jelenít meg, és . A `part-r-00000` fájl tartalmazza a feladat kimenetét.

    > [!NOTE]  
    > Egyes MapReduce feladatok több **rész-r-#####** fájlra is feloszthatják az eredményeket. Ha igen, a ##### utótaggal jelezze a fájlok sorrendjét.

1. A kimenet megtekintéséhez használja a következő parancsot:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Ez a parancs megjeleníti a **wasbs://example/data/gutenberg/davinci.txt** fájlban található szavak listáját és az egyes szavak előfordulásának számát. A következő szöveg a fájlban található adatokra mutat be példát:

    ```output
    wreathed        3
    wreathing       1
    wreaths         1
    wrecked         3
    wrenching       1
    wretched        6
    wriggling       1
    ```

## <a name="next-steps"></a>További lépések

Amint láthatja, a Hadoop-parancsok segítségével egyszerűen futtathatja a MapReduce feladatokat egy HDInsight-fürtben, majd megtekintheti a feladat kimenetét. A Hadoop hdinsight-on való egyéb működéséről a következőket szeretné tudni:

* [A MapReduce használata a HDInsight Hadoopon](hdinsight-use-mapreduce.md)
* [Az Apache Hive használata az Apache Hadoop segítségével a HDInsighton](hdinsight-use-hive.md)
