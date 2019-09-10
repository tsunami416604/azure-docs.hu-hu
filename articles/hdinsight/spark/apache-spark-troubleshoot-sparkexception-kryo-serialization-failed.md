---
title: Nem sikerült letölteni a nagyméretű adatkészleteket a JDBC/ODBC és az Apache takarékosság szoftver-keretrendszer használatával az Azure HDInsight
description: Nem sikerült letölteni a nagyméretű adatkészleteket a JDBC/ODBC és az Apache takarékosság szoftver-keretrendszer használatával az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 4283de9d9046cc63ee10731c05b70850648ff981
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668884"
---
# <a name="scenario-unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-azure-hdinsight"></a>Forgatókönyv: Nem sikerült letölteni a nagyméretű adatkészleteket a JDBC/ODBC és az Apache takarékosság szoftver-keretrendszer használatával az Azure HDInsight

Ez a cikk a Apache Spark-összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Ha a nagyméretű adatkészleteket a JDBC/ODBC és az Apache takarékosság szoftver-keretrendszer használatával próbálja meg letölteni az Azure HDInsight, a következőhöz hasonló hibaüzenet jelenik meg:

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>Ok

Ezt a kivételt a szerializálási folyamat okozta, hogy a megengedettnél több pufferméret legyen felhasználva. A Spark 2.0.0-ben a `org.apache.spark.serializer.KryoSerializer` osztály az objektumok szerializálásához használatos, amikor az adatelérés az Apache takarékosság szoftver-keretrendszeren keresztül történik. Egy másik osztályt használ a hálózaton keresztül küldendő vagy szerializált formában gyorsítótárazott adathoz.

## <a name="resolution"></a>Megoldás:

Növelje meg `Kryoserializer` a puffer értékét. Adjon hozzá egy nevű `spark.kryoserializer.buffer.max` kulcsot, és állítsa `2048` be a spark2 config `Custom spark2-thrift-sparkconf`alatt.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők. [@AzureSupport](https://twitter.com/azuresupport)

* Ha további segítségre van szüksége, támogatási kérést küldhet a Azure Portaltól [](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
