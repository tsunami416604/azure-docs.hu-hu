---
title: Problémák a JDBC/ODBC & Apache Takarékosság keretrendszerrel – Azure HDInsight
description: Nem lehet nagy adatkészleteket letölteni a JDBC/ODBC és az Apache Thrift szoftverkeretrendszer keretében az Azure HDInsightban
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 23693dcae2f361b88440ec88ca39fd8ed229d85a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894261"
---
# <a name="unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-hdinsight"></a>Nem lehet nagy adatkészleteket letölteni a JDBC/ODBC és az Apache Thrift szoftverkeretrendszer segítségével a HDInsightban

Ez a cikk az Apache Spark-összetevők Azure HDInsight-fürtökben való használata során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Amikor nagy adatkészleteket próbál letölteni a JDBC/ODBC és az Apache Thrift szoftverkeretrendszer használatával az Azure HDInsightban, az alábbi hibaüzenet jelenik meg:

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>Ok

Ezt a kivételt az okozza, hogy a szerializálási folyamat a megengedettnél több pufferterületet próbál felhasználni. A Spark 2.0.0-s rendszerben az osztály `org.apache.spark.serializer.KryoSerializer` az objektumok szerializálására szolgál, amikor az adatok az Apache Thrift szoftverkeretrendszeren keresztül érhetők el. A rendszer egy másik osztályt használ a hálózaton keresztül küldendő vagy szerializált formában gyorsítótárba helyezett adatokhoz.

## <a name="resolution"></a>Megoldás:

Növelje `Kryoserializer` a pufferértéket. Adjon hozzá `spark.kryoserializer.buffer.max` egy elnevezett `2048` kulcsot, és `Custom spark2-thrift-sparkconf`állítsa a spark2 config-ban a területen.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal, amely javítja az ügyfélélményt azáltal, hogy az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse át az Azure-támogatási kérelem létrehozása című, [továbbcímű tájékoztatót.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
