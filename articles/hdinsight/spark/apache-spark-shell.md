---
title: Az Azure HDInsight egy interaktív Spark-rendszerhéj használata |} Microsoft Docs
description: Egy interaktív Spark rendszerhéj egyszerre parancsok egy Spark fut, és az eredmények megtekintése olvasási hajtható végre nyomtatási folyamat nyújt.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: maxluk
ms.openlocfilehash: d2b65980516a7ae1857711f2e58d9cd0a8e8ec9a
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="run-spark-from-the-spark-shell"></a>Spark futtassa a Spark-rendszerhéj

Egy interaktív Spark rendszerhéj egyszerre parancsok egy Spark fut, és megtekintheti az eredményeket egy REPL (olvasás hajtható végre nyomtatás hurok) környezetet biztosít. Ez a folyamat akkor hasznos, fejlesztési és a hibakeresést. A Spark egy rendszerhéj biztosít a támogatott nyelvek mindegyikét: Scala, a Python és az R.

## <a name="get-to-a-spark-shell-with-ssh"></a>A Spark rendszerhéj SSH az beszerzése

A hdinsight Spark rendszerhéjat érhető SSH használata a fürt elsődleges átjárócsomópontjához csatlakozik:

     ssh <sshusername>@<clustername>-ssh.azurehdinsight.net

Kaphat a teljes SSH-parancs a fürt az Azure-portálon:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Nyissa meg a panelen a HDInsight Spark-fürt számára.
3. Válassza ki Secure Shell (SSH).

    ![HDInsight ablaktábla Azure-portálon](./media/apache-spark-shell/hdinsight-spark-blade.png)

4. A megjelenített SSH-parancs másolásával, és futtassa azt a terminálon.

    ![HDInsight SSH ablaktábla Azure-portálon](./media/apache-spark-shell/hdinsight-spark-ssh-blade.png)

További részletek az SSH használatával kapcsolódni HDInsight: [az SSH a Hdinsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run-a-spark-shell"></a>Indítsa el a Spark

Spark Scala (spark-felület), a Python (pyspark) és az R (sparkR) ismertetése nyújt. Az SSH-munkamenetet, a HDInsight-fürt átjárócsomópontjához adja meg a következő parancsok egyikét:

    ./bin/spark-shell
    ./bin/pyspark
    ./bin/sparkR

Most Spark-parancsokat adhat meg a megfelelő nyelven.

## <a name="sparksession-and-sparkcontext-instances"></a>SparkSession és SparkContext példányok

Alapértelmezés szerint való futtatásakor a Spark rendszerhéj SparkSession és SparkContext példányai vannak automatikusan létrehozva meg.

A SparkSession példányt szeretne használni, adja meg a `spark`. A SparkContext példányt szeretne használni, adja meg a `sc`.

## <a name="important-shell-parameters"></a>Fontos rendszerhéj paraméterek

A Spark rendszerhéj parancs (`spark-shell`, `pyspark`, vagy `sparkR`) számos parancssori paramétereket támogatja. Paraméterek teljes listájának megtekintéséhez indítsa el a Spark rendszerhéj kapcsolóját `--help`. Vegye figyelembe, hogy ezek a paraméterek némelyike előfordulhat, hogy csak a alkalmazásra `spark-submit`, amely a Spark felületet burkolja.

| Váltás | leírás | Példa |
| --- | --- | --- |
| --MASTER_URL fő | Meghatározza a fő URL-címet. A Hdinsightban, ez az érték mindig van `yarn`. | `--master yarn`|
| --jars JAR_LIST | Helyi JAR-fájlok kivételével meg az illesztőprogram és végrehajtó classpaths vesszővel tagolt listája. A Hdinsightban ebben a listában az alapértelmezett fájlrendszer Azure Storage vagy a Data Lake Store elérési útjait tevődik össze. | `--jars /path/to/examples.jar` |
| --MAVEN_COORDS csomagok | Maven koordinátáit JAR-fájlok kivételével meg az illesztőprogram és végrehajtó classpaths vesszővel tagolt listája. A helyi maven-tárházban, majd a maven központi keres, és minden további távoli adattárak megadott `--repositories`. A formátum a koordináták *groupId*:*artifactid szakaszát*:*verzió*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --PE-fájlok listája | Python csak .zip, .egg vagy .py fájlok számára, hogy a PYTHONPATH vesszővel tagolt listája. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>További lépések

- Lásd: [a Spark on Azure HDInsight bemutatása](apache-spark-overview.md) áttekintése.
- Lásd: [Apache Spark-fürt létrehozása az Azure HDInsight](apache-spark-jupyter-spark-sql.md) Spark-fürtök és a sparksql-hez.
- Lásd: [Újdonságok Spark strukturált Streaming?](apache-spark-streaming-overview.md) alkalmazások írását, amelyek a Spark streamelési adatok feldolgozása.

