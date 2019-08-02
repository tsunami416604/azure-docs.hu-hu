---
title: Az Apache Kafka-fürtből adatokat olvasó Apache Spark folyamatos átviteli feladatok meghiúsulnak az Azure HDInsight NoClassDefFoundError
description: Az Apache Kafka-fürtből adatokat olvasó Apache Spark folyamatos átviteli feladatok meghiúsulnak az Azure HDInsight NoClassDefFoundError
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 986b1dd2e749a0968c744f861feb0ac0bf2376e8
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68700482"
---
# <a name="scenario-apache-spark-streaming-job-that-reads-data-from-an-apache-kafka-cluster-fails-with-a-noclassdeffounderror-in-azure-hdinsight"></a>Forgatókönyv: Az Apache Kafka-fürtből adatokat olvasó Apache Spark folyamatos átviteli feladatok meghiúsulnak az Azure HDInsight NoClassDefFoundError

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

Ez a hiba oka lehet a futtatott Kafka-fürt verziójától eltérő `spark-streaming-kafka` jar-fájl verziójának megadásával.

Ha például egy Kafka-fürt 0.10.1 verzióját futtatja, a következő parancs hibát eredményez:

```
spark-submit \
--packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.2.0
--conf spark.executor.instances=16 \
...
~/Kafka_Spark_SQL.py <bootstrap server details>
```

## <a name="resolution"></a>Megoldás:

Használja a Spark-Submit parancsot a `–packages` kapcsolóval, és győződjön meg arról, hogy a Spark-streaming-Kafka jar-fájl verziója megegyezik a futtatott Kafka-fürt verziójával.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők. [@AzureSupport](https://twitter.com/azuresupport)

* Ha további segítségre van szüksége, támogatási kérést küldhet a Azure Portaltól [](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
