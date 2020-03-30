---
title: Interaktív Spark-rendszerhéj használata az Azure HDInsightban
description: Az interaktív Spark Shell egy olvasási-végrehajtási-nyomtatási folyamatot biztosít a Spark-parancsok egyenkénti futtatásához és az eredmények megtekintéséhez.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/10/2020
ms.openlocfilehash: f8737f645df2aefbf9ce544199f0cc45ce6a3d60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162803"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Az Apache Spark futtatása a Spark Shellből

Az interaktív [Apache Spark](https://spark.apache.org/) Shell repl (read-execute-print loop) környezetet biztosít a Spark-parancsok egyenkénti futtatásához és az eredmények megtekintéséhez. Ez a folyamat fejlesztési és hibakeresési célokra hasznos. A Spark egy-egy rendszerhéjat biztosít minden támogatott nyelvhez: a Scala, a Python és az R.

## <a name="run-an-apache-spark-shell"></a>Apache Spark-rendszerhéj futtatása

1. Az [ssh paranccsal](../hdinsight-hadoop-linux-use-ssh-unix.md) csatlakozhat a fürthöz. Az alábbi parancs szerkesztésével cserélje le a CLUSTERNAME-t a fürt nevére, majd írja be a parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A Spark rendszerhéjakat biztosít a Scala (spark-shell) és a Python (pyspark) számára. Az SSH-munkamenetben írja be az alábbi parancsok *egyikét:*

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

    Ha bármilyen választható konfigurációt kíván használni, győződjön meg arról, hogy először áttekinti [az OutOfMemoryError kivételt az Apache Spark számára.](./apache-spark-troubleshoot-outofmemory.md)

1. Néhány alapvető példaparancs. Válassza ki a megfelelő nyelvet:

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

1. CSV-fájl lekérdezése. Megjegyzés: az alábbi `spark-shell` `pyspark`nyelv működik, és .

    ```scala
    spark.read.csv("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv").show()
    ```

1. CSV-fájl lekérdezése és tárolási eredmények lekérdezése változóban:

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

## <a name="sparksession-and-sparkcontext-instances"></a>SparkSession és SparkContext példányok

Alapértelmezés szerint a Spark Shell futtatásakor a SparkSession és a SparkContext példányai automatikusan példányosodnak.

A SparkSession példány eléréséhez írja be a t. `spark` A SparkContext-példány eléréséhez írja be a t. `sc`

## <a name="important-shell-parameters"></a>Fontos rendszerhéj-paraméterek

A Spark Shell`spark-shell`parancs `pyspark`( vagy ) számos parancssori paramétert támogat. A paraméterek teljes listájának megtekintéséhez indítsa el `--help`a Spark Shellt a kapcsolóval. Ezek közül a paraméterek közül `spark-submit`néhány csak a , amely a Spark Shell pakolások.

| Kapcsoló | leírás | Példa |
| --- | --- | --- |
| --mester MASTER_URL | Megadja a fő URL-címet. A HDInsightban ez `yarn`az érték mindig . | `--master yarn`|
| --üvegek JAR_LIST | Vesszővel tagolt helyi üvegek listája, amelyeket a vezető és a végrehajtó osztályvezető között kell szerepelni. A HDInsightban ez a lista az Azure Storage vagy a Data Lake Storage alapértelmezett fájlrendszeréhez vezető elérési utakból áll. | `--jars /path/to/examples.jar` |
| --csomagok MAVEN_COORDS | Vesszővel tagolt listája maven koordinátái üvegek tartalmazza a vezető és végrehajtó classpaths. Megkeresi a helyi maven repo, majd maven central, majd `--repositories`minden további távoli tárolók megadott . A koordináták formátuma *groupId*:*artifactId*:*version.* | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --py-fájlok LISTÁJA | Csak Python esetén a .zip, .egg vagy .py fájlok vesszővel elválasztott listája a PYTHONPATH-on. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>További lépések

- [Az Azure HDInsight –bevezetés az Apache Sparkba](apache-spark-overview.md) című témakörben nyújt áttekintést.
- Tekintse [meg az Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md) című témakört a Spark-fürtökkel és a SparkSQL-szel való munkához.
- Lásd: [Mi az Apache Spark strukturált streamelése?](apache-spark-streaming-overview.md)
