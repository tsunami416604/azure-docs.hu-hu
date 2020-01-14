---
title: MapReduce and SSH connection with Apache Hadoop - Azure HDInsight
description: Learn how to use SSH to run MapReduce jobs using Apache Hadoop on HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/10/2020
ms.openlocfilehash: 543bc29adc85bd767de9479607d067fadf7b0078
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934704"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>Use MapReduce with Apache Hadoop on HDInsight with SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Learn how to submit MapReduce jobs from a Secure Shell (SSH) connection to HDInsight.

> [!NOTE]
> If you are already familiar with using Linux-based Apache Hadoop servers, but you are new to HDInsight, see [Linux-based HDInsight tips](../hdinsight-hadoop-linux-information.md).

## <a name="prerequisites"></a>Előfeltételek

An Apache Hadoop cluster on HDInsight. See [Create Apache Hadoop clusters using the Azure portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="use-hadoop-commands"></a>Use Hadoop commands

1. Use [ssh command](../hdinsight-hadoop-linux-use-ssh-unix.md) to connect to your cluster. Edit the command below by replacing CLUSTERNAME with the name of your cluster, and then enter the command:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. After you are connected to the HDInsight cluster, use the following command to start a MapReduce job:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    This command starts the `wordcount` class, which is contained in the `hadoop-mapreduce-examples.jar` file. It uses the `/example/data/gutenberg/davinci.txt` document as input, and output is stored at `/example/data/WordCountOutput`.

    > [!NOTE]
    > For more information about this MapReduce job and the example data, see [Use MapReduce in Apache Hadoop on HDInsight](hdinsight-use-mapreduce.md).

    The job emits details as it processes, and it returns information similar to the following text when the job completes:

    ```output
    File Input Format Counters
    Bytes Read=1395666
    File Output Format Counters
    Bytes Written=337623
    ```

1. When the job completes, use the following command to list the output files:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    This command display two files, `_SUCCESS` and `part-r-00000`. The `part-r-00000` file contains the output for this job.

    > [!NOTE]  
    > Some MapReduce jobs may split the results across multiple **part-r-#####** files. Ha igen, használja a # # # # # utótagot, hogy jelezze a fájlok sorrendjét.

1. A kimenet megtekintéséhez használja a következő parancsot:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Ez a parancs megjeleníti a **wasbs://example/Data/Gutenberg/DaVinci.txt** fájlban található szavak listáját, valamint az egyes szavak előfordulási idejét. A következő szöveg egy példa a fájlban található fájlokra:

    ```output
    wreathed        3
    wreathing       1
    wreaths         1
    wrecked         3
    wrenching       1
    wretched        6
    wriggling       1
    ```

## <a name="next-steps"></a>Következő lépések

Amint láthatja, a Hadoop parancsai egyszerűen futtathatják a MapReduce-feladatokat egy HDInsight-fürtön, majd megtekinthetik a feladat kimenetét. További információ a Hadoop a HDInsight-ben való használatával kapcsolatos egyéb módszerekről:

* [A MapReduce használata a HDInsight-Hadoop](hdinsight-use-mapreduce.md)
* [Apache Hive használata a HDInsight Apache Hadoop használatával](hdinsight-use-hive.md)
