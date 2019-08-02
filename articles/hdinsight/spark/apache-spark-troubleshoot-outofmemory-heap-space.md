---
title: A Java-tárterülettel kapcsolatos hiba történt, amikor a rendszer megpróbálta megnyitni Apache Spark History Servert az Azure HDInsight
description: Az Apache Livy Server nem tud elindulni a Java. lang. működése OutOfMemoryError az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: 5c2ae90bdca8512802c845a5ac58c3c4aeedd5b7
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667806"
---
# <a name="scenario-java-heap-space-error-when-trying-to-open-apache-spark-history-server-in-azure-hdinsight"></a>Forgatókönyv: A Java-tárterülettel kapcsolatos hiba történt, amikor a rendszer megpróbálta megnyitni Apache Spark History Servert az Azure HDInsight

Ez a cikk a Apache Spark-összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A Spark History Server eseményeinek megnyitásakor a következő hibaüzenet jelenik meg:

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

## <a name="cause"></a>Ok

Ezt a problémát gyakran az erőforrások hiánya okozza a nagyméretű Spark-Event fájlok megnyitásakor. Alapértelmezés szerint a Spark heap mérete 1 GB-ra van beállítva, de a nagyméretű Spark-eseményeknél ennél többre lehet szükség.

Ha ellenőrizni szeretné a betölteni kívánt fájlok méretét, hajtsa végre a következő parancsokat:

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

## <a name="resolution"></a>Megoldás:

A Spark-előzmények kiszolgálójának memóriáját a Spark- `SPARK_DAEMON_MEMORY` konfigurációban található tulajdonság szerkesztésével és az összes szolgáltatás újraindításával növelheti.

Ezt a Ambari böngésző felhasználói felületén végezheti el a Spark2/config/Advanced Spark2-env szakasz kiválasztásával.

![Speciális spark2 – env szakasz](./media/apache-spark-ts-outofmemory-heap-space/image01.png)

Adja hozzá a következő tulajdonságot a Spark History-kiszolgáló memóriájának a 1g- `SPARK_DAEMON_MEMORY=4g`ről 4G-re való módosításához:.

![Spark-tulajdonság](./media/apache-spark-ts-outofmemory-heap-space/image02.png)

Győződjön meg arról, hogy az összes érintett szolgáltatást újraindítja a Ambari.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők. [@AzureSupport](https://twitter.com/azuresupport)

* Ha további segítségre van szüksége, támogatási kérést küldhet a Azure Portaltól [](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
