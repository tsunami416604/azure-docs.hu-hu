---
title: Interaktív Spark-rendszerhéj használata az Azure HDInsight
description: Az interaktív Spark-rendszerhéj olvasási végrehajtást tesz lehetővé a Spark-parancsok egyszerre történő futtatásához és az eredmények megjelenítéséhez.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.openlocfilehash: 7aac2812787a7c14d99377754a4f85e699ef3f09
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68441892"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Apache Spark futtatása a Spark shellből

Az interaktív [Apache Spark](https://spark.apache.org/) RENDSZERHÉJ egy repl (olvasási végrehajtás – nyomtatott hurok) környezetet biztosít a Spark-parancsok egyszerre történő futtatásához és az eredmények megjelenítéséhez. Ez a folyamat a fejlesztéshez és a hibakereséshez hasznos. A Spark egyetlen rendszerhéjt biztosít minden támogatott nyelvéhez: Scala, Python és R.

## <a name="get-to-an-apache-spark-shell-with-ssh"></a>Apache Spark-rendszerhéj beszerzése SSH-val

Hozzáférés egy Apache Spark rendszerhéjhoz a HDInsight-ben a fürt elsődleges fő csomópontjának SSH-val való összekapcsolásával:

     ssh <sshusername>@<clustername>-ssh.azurehdinsight.net

A fürt teljes SSH-parancsát lekérheti a Azure Portal:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Navigáljon a HDInsight Spark-fürthöz tartozó ablaktáblára.
3. Válassza a Secure Shell (SSH) lehetőséget.

    ![HDInsight ablaktábla Azure Portal](./media/apache-spark-shell/hdinsight-spark-blade.png)

4. Másolja a megjelenő SSH-parancsot, és futtassa a terminálon.

    ![HDInsight SSH-ablaktábla Azure Portal](./media/apache-spark-shell/hdinsight-spark-ssh-blade.png)

Az SSH HDInsight-hez való kapcsolódásával kapcsolatos részletekért lásd: az [SSH használata a HDInsight használatával](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run-an-apache-spark-shell"></a>Apache Spark rendszerhéj futtatása

A Spark shelleket biztosít a Scala (Spark-Shell), a Python (pyspark) és az R (sparker) számára. A HDInsight-fürt fő csomópontján lévő SSH-munkamenetben adja meg a következő parancsok egyikét:

    ./bin/spark-shell
    ./bin/pyspark
    ./bin/sparkR

Most megadhatja a Spark-parancsokat a megfelelő nyelven.

## <a name="sparksession-and-sparkcontext-instances"></a>SparkSession-és SparkContext-példányok

Alapértelmezés szerint a Spark rendszerhéj futtatásakor a rendszer automatikusan létrehozza a SparkSession és a SparkContext példányait.

A SparkSession-példány eléréséhez írja `spark`be a következőt:. A SparkContext-példány eléréséhez írja `sc`be a következőt:.

## <a name="important-shell-parameters"></a>Fontos rendszerhéj-paraméterek

A Spark rendszerhéj-parancs`spark-shell`( `pyspark`,, `sparkR`vagy) számos parancssori paramétert támogat. A paraméterek teljes listájának megjelenítéséhez indítsa el a Spark shellt a kapcsolóval `--help`. Vegye figyelembe, hogy a paraméterek némelyike csak a `spark-submit`, a Spark-rendszerhéj által befoglalt értékekre vonatkozik.

| kapcsoló | description | Példa |
| --- | --- | --- |
| --fő MASTER_URL | Megadja a fő URL-címet. A HDInsight-ben ez az érték `yarn`mindig. | `--master yarn`|
| – Tégelyek JAR_LIST | Az illesztőprogramon és a végrehajtó classpaths található helyi tégelyek vesszővel tagolt listája. A HDInsight-ben ez a lista az Azure Storage-ban vagy Data Lake Storage-ban található alapértelmezett fájlrendszer elérési útjaiból áll. | `--jars /path/to/examples.jar` |
| --csomagok MAVEN_COORDS | Az edényben található, az illesztőprogramra és a végrehajtó classpaths kiterjedő Maven-koordináták vesszővel tagolt listája. Megkeresi a helyi Maven-tárházat, a Maven Central-t `--repositories`, majd a által meghatározott további távoli adattárakat. A koordináták formátuma *GroupID*:*artifactId*:*Version*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --fájlok listája | Csak Python esetén a PYTHONPATH elhelyezni kívánt. zip-,. Egg-vagy.-fájlok vesszővel tagolt listája. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>További lépések

- Tekintse [meg az Azure HDInsight Apache Spark bemutatása](apache-spark-overview.md) című témakört.
- Lásd: [Apache Spark-fürt létrehozása az Azure HDInsight-ben](apache-spark-jupyter-spark-sql.md) Spark-fürtökkel és-SparkSQL való együttműködéshez.
- Lásd: [Mi az Apache Spark strukturált streaming?](apache-spark-streaming-overview.md) az adatfolyamot a sparktal feldolgozó alkalmazások írásához.
