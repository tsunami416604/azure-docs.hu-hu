---
title: NoClassDefFoundError – Apache Spark az Azure HDInsight-ban Apache Kafka adattal
description: Az Apache Kafka-fürtből adatokat olvasó Apache Spark folyamatos átviteli feladatok meghiúsulnak az Azure HDInsight NoClassDefFoundError
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: c523dbc4612ebfcebf3923900b31f3a2a7b0c5c6
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287779"
---
# <a name="apache-spark-streaming-job-that-reads-apache-kafka-data-fails-with-noclassdeffounderror-in-hdinsight"></a>Apache Kafka adatokat olvasó Apache Spark folyamatos átviteli feladattal nem sikerül a NoClassDefFoundError a HDInsight-ben

Ez a cikk a Apache Spark-összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A Apache Spark-fürt egy Spark streaming-feladatot futtat, amely egy Apache Kafka-fürt adatait olvassa be. A Spark streaming feladata meghiúsul, ha a Kafka-adatfolyam tömörítése be van kapcsolva. Ebben az esetben a Spark streaming fonal-alkalmazás application_1525986016285_0193 sikertelen volt, a következő hiba miatt:

```
18/05/17 20:01:33 WARN YarnAllocator: Container marked as failed: container_e25_1525986016285_0193_01_000032 on host: wn87-Scaled.2ajnsmlgqdsutaqydyzfzii3le.cx.internal.cloudapp.net. Exit status: 50. Diagnostics: Exception from container-launch.
Container id: container_e25_1525986016285_0193_01_000032
Exit code: 50
Stack trace: ExitCodeException exitCode=50: 
 at org.apache.hadoop.util.Shell.runCommand(Shell.java:944)
```

## <a name="cause"></a>Ok

Ez a hiba oka lehet a `spark-streaming-kafka` futtatott Kafka-fürt verziójától eltérő jar-fájl verziójának megadásával.

Ha például egy Kafka-fürt 0.10.1 verzióját futtatja, a következő parancs hibát eredményez:

```
spark-submit \
--packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.2.0
--conf spark.executor.instances=16 \
...
~/Kafka_Spark_SQL.py <bootstrap server details>
```

## <a name="resolution"></a>Feloldás

Használja a Spark-Submit parancsot a `–packages` kapcsolóval, és győződjön meg arról, hogy a Spark-streaming-Kafka jar-fájl verziója megegyezik a futtatott Kafka-fürt verziójával.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]