---
title: RpcTimeoutException for Apache Spark takarékosság – Azure HDInsight
description: A nagyméretű adatkészletek Apache Spark takarékossági kiszolgáló használatával történő feldolgozásakor a 502-es hiba jelenik meg
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: a29d36c5ba6fdd51de27afa3ab4dfe1258332200
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85208416"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Forgatókönyv: RpcTimeoutException for Apache Spark takarékossági kiszolgáló az Azure HDInsight

Ez a cikk a Apache Spark-összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A Spark-alkalmazás egy `org.apache.spark.rpc.RpcTimeoutException` kivétellel és egy üzenettel meghiúsul: `Futures timed out` az alábbi példában látható módon:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError`és a `overhead limit exceeded` hibák a következő példában is szerepelhetnek `sparkthriftdriver.log` :

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Ok

Ezeket a hibákat az adatfeldolgozás során rendelkezésre álló memória-erőforrások hiánya okozza. Ha a Java-adatgyűjtési folyamat elindul, előfordulhat, hogy a Spark-alkalmazás nem válaszol. A lekérdezések megkezdik az időkorlátot, és leállítják a feldolgozást. A `Futures timed out` hiba súlyos terhelés alatt álló fürtöt jelez.

## <a name="resolution"></a>Megoldás:

Növelje a fürt méretét több munkavégző csomópont hozzáadásával vagy a meglévő fürtcsomópontok memória-kapacitásának növelésével. Az adatfolyamatot úgy is beállíthatja, hogy csökkentse a egyszerre feldolgozott adatmennyiséget.

A `spark.network.timeout` meghatározza az összes hálózati kapcsolat időtúllépését. A hálózati időkorlát növelése több időt is igénybe vehet néhány kritikus művelet befejezésére, de ez nem oldja meg teljesen a problémát.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az [@AzureSupport](https://twitter.com/azuresupport) Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
