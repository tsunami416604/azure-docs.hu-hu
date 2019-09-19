---
title: Apache Hive Zeppelin-tolmács Zookeeper hibát ad az Azure HDInsight
description: Az Apache Zeppelin-struktúra JDBC-értelmező nem megfelelő URL-címre mutat az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 185d8e4954091d9b54e0604fc2bd3f3d010f926e
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091322"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>Forgatókönyv: Apache Hive Zeppelin-tolmács Zookeeper hibát ad az Azure HDInsight

Ez a cikk az interaktív lekérdezési összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Egy Apache Hive LLAP-fürtön a Zeppelin-értelmező a következő hibaüzenetet adja meg a lekérdezés végrehajtásának megkísérlése során:

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>Ok

A Zeppelin-struktúra JDBC-értelmező nem megfelelő URL-címre mutat.

## <a name="resolution"></a>Megoldás:

1. Navigáljon a kaptár összetevő összegzéséhez, és másolja a "kaptár JDBC URL-címét" a vágólapra.

1. Navigáljon a`https://clustername.azurehdinsight.net/zeppelin/#/interpreter`

1. A JDBC beállításainak szerkesztése: frissítse a kaptár. URL értékét a kaptár JDBC URL-címére, amelyet az 1. lépésben másolt.

1. Mentse, majd próbálkozzon újra a lekérdezéssel

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők. [@AzureSupport](https://twitter.com/azuresupport)

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
