---
title: A JDBC/ODBC & Apache takarékossági keretrendszerrel kapcsolatos problémák – Azure HDInsight
description: Nem sikerült letölteni a nagyméretű adatkészleteket a JDBC/ODBC és az Apache takarékosság szoftver-keretrendszer használatával az Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 05/14/2020
ms.openlocfilehash: a8dcd6ae844810213ed6706002cdb9a31de94f60
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653585"
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

Ezt a kivételt a szerializálási folyamat okozta, hogy a megengedettnél több pufferméret legyen felhasználva. A Spark 2.0.0-ben a osztály az `org.apache.spark.serializer.KryoSerializer` objektumok szerializálásához használatos, amikor az adatelérés az Apache takarékosság szoftver-keretrendszeren keresztül történik. Egy másik osztályt használ a hálózaton keresztül küldendő vagy szerializált formában gyorsítótárazott adathoz.

## <a name="resolution"></a>Megoldás:

Növelje meg a `Kryoserializer` puffer értékét. Adjon hozzá egy nevű kulcsot, `spark.kryoserializer.buffer.max` és állítsa be a `2047` spark2 config alatt `Custom spark2-thrift-sparkconf` . Indítsa újra az összes érintett összetevőt.

> [!IMPORTANT]
> A értékének `spark.kryoserializer.buffer.max` 2048-nál kisebbnek kell lennie. A tört értékek nem támogatottak.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az [@AzureSupport](https://twitter.com/azuresupport) Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
