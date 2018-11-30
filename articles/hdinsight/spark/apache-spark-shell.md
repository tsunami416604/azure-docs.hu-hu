---
title: Az Azure HDInsight interaktív Spark Shell használata
description: Interaktív Spark Shell biztosít egy olvasási végrehajtása nyomtatási folyamat egyszerre egy parancsok futtatása a Spark és az eredmények jelennek meg.
services: hdinsight
ms.service: hdinsight
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.openlocfilehash: f8c296fddaafb8364cc5e0f07b54d850606322d9
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52581425"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Futtassa az Apache Spark a Spark-Shell

Egy interaktív [Apache Spark](https://spark.apache.org/) rendszerhéj egyszerre egy parancsok futtatása a Spark és az eredmények jelennek meg (olvasási végrehajtása print-loop) REPL környezetet biztosít. Ez a folyamat hasznos fejlesztés és hibakeresés. A Spark egy parancshéj biztosít minden, a támogatott nyelvek: Scala, Python és az r segítségével

## <a name="get-to-an-apache-spark-shell-with-ssh"></a>Az Apache Spark-Shell az ssh-val való beolvasása

Az Apache Spark-Shell a HDInsight a fürthöz SSH használatával, az elsődleges átjárócsomóponthoz csatlakozik hozzá:

     ssh <sshusername>@<clustername>-ssh.azurehdinsight.net

A fürt teljes SSH-parancs kaphat az Azure Portalról:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Keresse meg a HDInsight Spark-fürthöz tartozó panel.
3. Válassza ki a Secure Shell (SSH).

    ![HDInsight panel az Azure Portalon](./media/apache-spark-shell/hdinsight-spark-blade.png)

4. A megjelenített SSH-parancs másolásával, és futtassa a terminálban.

    ![HDInsight SSH panel az Azure Portalon](./media/apache-spark-shell/hdinsight-spark-ssh-blade.png)

SSH használatával csatlakozhat a HDInsight a részletekért lásd: [az SSH használata a HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run-an-apache-spark-shell"></a>Az Apache Spark-Shell futtatása

Spark – ismertetése (spark-shell) Scala, Python (pyspark) és az R (sparkR) biztosít. Az SSH-munkamenet, a HDInsight-fürt fő csomópontjának adja meg a következő parancsok egyikét:

    ./bin/spark-shell
    ./bin/pyspark
    ./bin/sparkR

Mostantól a megfelelő nyelven Spark-parancsokat adhat meg.

## <a name="sparksession-and-sparkcontext-instances"></a>SparkSession és sparkcontext elemet példányok

Alapértelmezés szerint a Spark-Shell futtatásakor SparkSession és sparkcontext elemet példányait automatikusan példányosítása az Ön számára.

Adja meg a SparkSession-példány eléréséhez, `spark`. Adja meg a sparkcontext elemet-példány eléréséhez, `sc`.

## <a name="important-shell-parameters"></a>Fontos rendszerhéj-paraméterek

A Spark-Shell-parancs (`spark-shell`, `pyspark`, vagy `sparkR`) számos parancssori paramétereket támogatja. Paraméterek teljes listájának megtekintéséhez indítsa el a Spark-Shell váltást `--help`. Vegye figyelembe, hogy ezek a paraméterek némelyike csak érvényesek az `spark-submit`, amely a Spark-Shell burkolja.

| Kapcsoló | leírás | Példa |
| --- | --- | --- |
| --MASTER_URL fő | Meghatározza a fő URL-CÍMÉT. A HDInsight, az értéke mindig `yarn`. | `--master yarn`|
| --jars JAR_LIST | Helyi JAR-fájlok kivételével az illesztőprogram és feladat-végrehajtó osztályútvonalakkal elhelyezni a vesszővel tagolt listája. A HDInsight a lista elérési útját az Azure Storage vagy a Data Lake Store az alapértelmezett fájlrendszer tevődik össze. | `--jars /path/to/examples.jar` |
| --MAVEN_COORDS csomagok | Az illesztőprogram és feladat-végrehajtó osztályútvonalakkal elhelyezni a JAR-fájlok kivételével a maven-koordináták vesszővel tagolt listája. A helyi maven-tárházban, akkor a maven központi, keres, akkor minden további távoli tárházak megadott `--repositories`. A formátum a koordináták *groupId*:*artifactId*:*verzió*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --py-fájlok listája | Pythonhoz készült csak .zip, .egg vagy .py fájlok számára, hogy a PYTHONPATH vesszővel elválasztott listáját. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>További lépések

- Lásd: [Apache Spark on Azure HDInsight Spark bemutatása](apache-spark-overview.md) áttekintését.
- Lásd: [Apache Spark-fürt létrehozása az Azure HDInsight](apache-spark-jupyter-spark-sql.md) -alapú Spark-fürtök és a sparksql-hez.
- Lásd: [Mi az Apache Spark strukturált Stream?](apache-spark-streaming-overview.md) alkalmazások írását, amelyek a Spark streamelési adatok dolgozhatók.

