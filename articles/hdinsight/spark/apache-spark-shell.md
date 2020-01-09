---
title: Interaktív Spark-rendszerhéj használata az Azure HDInsight
description: Az interaktív Spark-rendszerhéj olvasási végrehajtást tesz lehetővé a Spark-parancsok egyszerre történő futtatásához és az eredmények megjelenítéséhez.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/12/2019
ms.openlocfilehash: f088b8210b8170d22e84d131f0a72f5f8caa3b92
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435226"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Apache Spark futtatása a Spark shellből

Az interaktív [Apache Spark](https://spark.apache.org/) RENDSZERHÉJ egy repl (olvasási végrehajtás – nyomtatott hurok) környezetet biztosít a Spark-parancsok egyszerre történő futtatásához és az eredmények megjelenítéséhez. Ez a folyamat a fejlesztéshez és a hibakereséshez hasznos. A Spark egyetlen rendszerhéjt biztosít minden támogatott nyelvéhez: Scala, Python és R.

## <a name="run-an-apache-spark-shell"></a>Apache Spark rendszerhéj futtatása

1. A fürthöz való kapcsolódáshoz használja az [SSH-parancsot](../hdinsight-hadoop-linux-use-ssh-unix.md) . Szerkessze az alábbi parancsot az CLUSTERNAME helyére a fürt nevével, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A Spark a Scala (Spark-Shell) és a Python (pyspark) számára biztosít rendszerhéjakat. Az SSH-munkamenetben adja meg a következő parancsok egyikét:

    ```bash
    spark-shell
    pyspark
    ```

    Most megadhatja a Spark-parancsokat a megfelelő nyelven.

1. Néhány alapszintű példa parancs:

    ```scala
    // Load data
    var data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")

    // Show data
    data.show()

    // Select certain columns
    data.select($"BuildingID", $"Country").show(10)

    // exit shell
    :q
    ```

## <a name="sparksession-and-sparkcontext-instances"></a>SparkSession-és SparkContext-példányok

Alapértelmezés szerint a Spark rendszerhéj futtatásakor a rendszer automatikusan létrehozza a SparkSession és a SparkContext példányait.

A SparkSession-példány eléréséhez adja meg a `spark`. A SparkContext-példány eléréséhez adja meg a `sc`.

## <a name="important-shell-parameters"></a>Fontos rendszerhéj-paraméterek

A Spark Shell parancs (`spark-shell`vagy `pyspark`) számos parancssori paramétert támogat. A paraméterek teljes listájának megjelenítéséhez indítsa el a Spark shellt a kapcsoló `--help`. Ezen paraméterek némelyike csak a Spark-rendszerhéj által beburkolt `spark-submit`re vonatkozik.

| kapcsoló | leírás | Például |
| --- | --- | --- |
| --Master MASTER_URL | Megadja a fő URL-címet. A HDInsight-ben ez az érték mindig `yarn`. | `--master yarn`|
| --Tégelyek JAR_LIST | Az illesztőprogramon és a végrehajtó classpaths található helyi tégelyek vesszővel tagolt listája. A HDInsight-ben ez a lista az Azure Storage-ban vagy Data Lake Storage-ban található alapértelmezett fájlrendszer elérési útjaiból áll. | `--jars /path/to/examples.jar` |
| --csomagok MAVEN_COORDS | Az edényben található, az illesztőprogramra és a végrehajtó classpaths kiterjedő Maven-koordináták vesszővel tagolt listája. Megkeresi a helyi Maven-tárházat, a Maven Central-t, majd a `--repositories`által meghatározott további távoli adattárakat. A koordináták formátuma *GroupID*:*artifactId*:*Version*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --fájlok listája | Csak Python esetén a PYTHONPATH elhelyezni kívánt. zip-,. Egg-vagy.-fájlok vesszővel tagolt listája. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Következő lépések

- Tekintse [meg az Azure HDInsight Apache Spark bemutatása](apache-spark-overview.md) című témakört.
- Lásd: [Apache Spark-fürt létrehozása az Azure HDInsight-ben](apache-spark-jupyter-spark-sql.md) Spark-fürtökkel és-SparkSQL való együttműködéshez.
- Lásd: [Mi az Apache Spark strukturált streaming?](apache-spark-streaming-overview.md) az adatfolyamot a sparktal feldolgozó alkalmazások írásához.
