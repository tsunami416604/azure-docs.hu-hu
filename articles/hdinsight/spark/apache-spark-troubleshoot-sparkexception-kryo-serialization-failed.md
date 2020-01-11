---
title: A JDBC/ODBC & Apache takarékossági keretrendszerrel kapcsolatos problémák – Azure HDInsight
description: Nem sikerült letölteni a nagyméretű adatkészleteket a JDBC/ODBC és az Apache takarékosság szoftver-keretrendszer használatával az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 23693dcae2f361b88440ec88ca39fd8ed229d85a
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894261"
---
# <a name="unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-hdinsight"></a>Nem sikerült letölteni a nagyméretű adatkészleteket a HDInsight-ben a JDBC/ODBC és az Apache takarékosság szoftver-keretrendszer használatával

Ez a cikk a Apache Spark-összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Ha a nagyméretű adatkészleteket a JDBC/ODBC és az Apache takarékosság szoftver-keretrendszer használatával próbálja meg letölteni az Azure HDInsight, a következőhöz hasonló hibaüzenet jelenik meg:

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>Ok

Ezt a kivételt a szerializálási folyamat okozta, hogy a megengedettnél több pufferméret legyen felhasználva. A Spark 2.0.0-ben a `org.apache.spark.serializer.KryoSerializer` osztály az objektumok szerializálásához használatos, amikor az adatelérés az Apache takarékosság szoftver-keretrendszeren keresztül történik. Egy másik osztályt használ a hálózaton keresztül küldendő vagy szerializált formában gyorsítótárazott adathoz.

## <a name="resolution"></a>Felbontás

Növelje a `Kryoserializer` puffer értékét. Adjon hozzá egy `spark.kryoserializer.buffer.max` nevű kulcsot, és állítsa be úgy, hogy `2048` a spark2 config `Custom spark2-thrift-sparkconf`alatt.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás a [@AzureSupporthoz](https://twitter.com/azuresupport) – a hivatalos Microsoft Azure fiók a felhasználói élmény javításához az Azure-Közösség és a megfelelő erőforrások összekapcsolásával: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
