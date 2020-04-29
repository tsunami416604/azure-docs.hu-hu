---
title: Interaktív Spark-rendszerhéj használata az Azure HDInsight
description: Az interaktív Spark-rendszerhéj olvasási végrehajtást tesz lehetővé a Spark-parancsok egyszerre történő futtatásához és az eredmények megjelenítéséhez.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/10/2020
ms.openlocfilehash: f8737f645df2aefbf9ce544199f0cc45ce6a3d60
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77162803"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Apache Spark futtatása a Spark shellből

Az interaktív [Apache Spark](https://spark.apache.org/) RENDSZERHÉJ egy repl (olvasási végrehajtás – nyomtatott hurok) környezetet biztosít a Spark-parancsok egyszerre történő futtatásához és az eredmények megjelenítéséhez. Ez a folyamat a fejlesztéshez és a hibakereséshez hasznos. A Spark egyetlen rendszerhéjt biztosít minden támogatott nyelvéhez: Scala, Python és R.

## <a name="run-an-apache-spark-shell"></a>Apache Spark rendszerhéj futtatása

1. A fürthöz való kapcsolódáshoz használja az [SSH-parancsot](../hdinsight-hadoop-linux-use-ssh-unix.md) . Szerkessze az alábbi parancsot az CLUSTERNAME helyére a fürt nevével, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A Spark a Scala (Spark-Shell) és a Python (pyspark) számára biztosít rendszerhéjakat. Az SSH-munkamenetben adja meg a következő parancsok *egyikét* :

    ```bash
    spark-shell

    # Optional configurations
    # spark-shell --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4
    ```

    ```bash
    pyspark

    # Optional configurations
    # pyspark --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4
    ```

    Ha nem kötelező konfigurációt szeretne használni, győződjön meg róla, hogy először tekintse át [a Apache Spark működése OutOfMemoryError-kivételt](./apache-spark-troubleshoot-outofmemory.md).

1. Néhány alapszintű példa parancs. Válassza ki a megfelelő nyelvet:

    ```spark-shell
    val textFile = spark.read.textFile("/example/data/fruits.txt")
    textFile.first()
    textFile.filter(line => line.contains("apple")).show()
    ```

    ```pyspark
    textFile = spark.read.text("/example/data/fruits.txt")
    textFile.first()
    textFile.filter(textFile.value.contains("apple")).show()
    ```

1. CSV-fájl lekérdezése. Figyelje meg, hogy az alábbi `spark-shell` nyelv `pyspark`a és a esetében működik.

    ```scala
    spark.read.csv("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv").show()
    ```

1. CSV-fájl lekérdezése és az eredmények tárolása változóban:

    ```spark-shell
    var data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")
    ```

    ```pyspark
    data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")
    ```

1. Eredmények megjelenítése:

    ```spark-shell
    data.show()
    data.select($"BuildingID", $"Country").show(10)
    ```

    ```pyspark
    data.show()
    data.select("BuildingID", "Country").show(10)
    ```

1. Kilépés

    ```spark-shell
    :q
    ```

    ```pyspark
    exit()
    ```

## <a name="sparksession-and-sparkcontext-instances"></a>SparkSession-és SparkContext-példányok

Alapértelmezés szerint a Spark rendszerhéj futtatásakor a rendszer automatikusan létrehozza a SparkSession és a SparkContext példányait.

A SparkSession-példány eléréséhez írja `spark`be a következőt:. A SparkContext-példány eléréséhez írja `sc`be a következőt:.

## <a name="important-shell-parameters"></a>Fontos rendszerhéj-paraméterek

A Spark Shell parancs (`spark-shell`, vagy `pyspark`) számos parancssori paramétert támogat. A paraméterek teljes listájának megjelenítéséhez indítsa el a Spark shellt a kapcsolóval `--help`. Ezen paraméterek némelyike csak a Spark Shell `spark-submit`által betakart alkalmazásokra vonatkozik.

| kapcsoló | leírás | például |
| --- | --- | --- |
| --Master MASTER_URL | Megadja a fő URL-címet. A HDInsight-ben ez az érték `yarn`mindig. | `--master yarn`|
| --Tégelyek JAR_LIST | Az illesztőprogramon és a végrehajtó classpaths található helyi tégelyek vesszővel tagolt listája. A HDInsight-ben ez a lista az Azure Storage-ban vagy Data Lake Storage-ban található alapértelmezett fájlrendszer elérési útjaiból áll. | `--jars /path/to/examples.jar` |
| --csomagok MAVEN_COORDS | Az edényben található, az illesztőprogramra és a végrehajtó classpaths kiterjedő Maven-koordináták vesszővel tagolt listája. Megkeresi a helyi Maven-tárházat, a Maven Central-t `--repositories`, majd a által meghatározott további távoli adattárakat. A koordináták formátuma *GroupID*:*artifactId*:*Version*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --fájlok listája | Csak Python esetén a PYTHONPATH elhelyezni kívánt. zip-,. Egg-vagy.-fájlok vesszővel tagolt listája. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>További lépések

- Tekintse [meg az Azure HDInsight Apache Spark bemutatása](apache-spark-overview.md) című témakört.
- Lásd: [Apache Spark-fürt létrehozása az Azure HDInsight-ben](apache-spark-jupyter-spark-sql.md) Spark-fürtökkel és-SparkSQL való együttműködéshez.
- Lásd: [Mi az Apache Spark strukturált streaming?](apache-spark-streaming-overview.md) az adatfolyamot a sparktal feldolgozó alkalmazások írásához.
