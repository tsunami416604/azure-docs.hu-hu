---
title: RpcTimeoutException apache spark-i takarékossághoz – Azure HDInsight
description: 502 hibát lát, amikor nagy adatkészleteket dolgoz fel apache spark takarékossági kiszolgálóval
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: b15ac80295a0113eb0c384e1cc3185f3304c39c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894287"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Eset: RpcTimeoutException apache spark-kiszolgálóhoz az Azure HDInsightban

Ez a cikk az Apache Spark-összetevők Azure HDInsight-fürtökben való használata során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A Spark-alkalmazás `org.apache.spark.rpc.RpcTimeoutException` egy kivétellel `Futures timed out`és egy üzenettel sikertelen: , ahogy a következő példában is:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError`és `overhead limit exceeded` a hibák is `sparkthriftdriver.log` megjelenhetnek a mint a következő példában:

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Ok

Ezeket a hibákat az adatfeldolgozás során a memória-erőforrások hiánya okozza. Ha a Java szemétgyűjtési folyamat elindul, ez vezethet a Spark-alkalmazás akasztás. A lekérdezések időhosszabbítást kezdenek, és leállítják a feldolgozást. A `Futures timed out` hiba súlyos terhelés alatt álló fürtre utal.

## <a name="resolution"></a>Megoldás:

Növelje a fürt méretét további munkavégző csomópontok hozzáadásával vagy a meglévő fürtcsomópontok memóriakapacitásának növelésével. Az adatfolyamatot is módosíthatja az egyszerre feldolgozott adatok mennyiségének csökkentése érdekében.

Az `spark.network.timeout` összes hálózati kapcsolat időidejét az határozza meg. A hálózati időtúlkiadás növelése több időt vehet igénybe néhány kritikus művelet befejezéséhez, de ez nem oldja meg teljesen a problémát.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal, amely javítja az ügyfélélményt azáltal, hogy az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse át az Azure-támogatási kérelem létrehozása című, [továbbcímű tájékoztatót.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
