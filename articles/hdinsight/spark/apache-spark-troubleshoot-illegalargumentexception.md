---
title: IllegalArgumentException Apache Spark tevékenységhez az Azure HDInsight
description: IllegalArgumentException Apache Spark-tevékenységhez az Azure HDInsight for Azure Data Factory
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 54e07a840aac1e754db68d9a14403750757f4bcf
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620843"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>Forgatókönyv: IllegalArgumentException Apache Spark tevékenységhez az Azure HDInsight

Ez a cikk a Apache Spark-összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A következő kivétel jelenik meg, amikor a Spark-tevékenységet egy Azure Data Factory-folyamatban próbálja végrehajtani:

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>Ok

A Spark-feladatok sikertelenek lesznek, ha az alkalmazás jar-fájlja nem a Spark-fürt alapértelmezett/elsődleges tárolójában található.

Ez egy ismert probléma, amelyet a Spark nyílt forráskódú keretrendszerében követett el a hiba: [A Spark-feladatok sikertelenek, ha az FS. defaultFS és az Application jar eltérő URL-cím](https://issues.apache.org/jira/browse/SPARK-22587).

Ezt a problémát a Spark 2.3.0-ben oldottuk meg.

## <a name="resolution"></a>Megoldás:

Győződjön meg arról, hogy az alkalmazás jar a HDInsight-fürt alapértelmezett/elsődleges tárolójában van tárolva. Azure Data Factory esetén győződjön meg arról, hogy az ADF társított szolgáltatás a HDInsight alapértelmezett tárolóra mutat, nem pedig másodlagos tárolóként.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők. [@AzureSupport](https://twitter.com/azuresupport)

* Ha további segítségre van szüksége, támogatási kérést küldhet a Azure Portaltól [](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
