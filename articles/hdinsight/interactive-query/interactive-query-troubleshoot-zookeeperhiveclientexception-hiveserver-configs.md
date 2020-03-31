---
title: Apache Hive Zeppelin értelmező hiba – Azure HDInsight
description: Az Apache Zeppelin Hive JDBC értelmező nem a megfelelő URL-címre mutat az Azure HDInsightban
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 20309babb9ece0ae20e7442543b0d378f9a51060
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895066"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>Eset: Az Apache Hive Zeppelin-értelmező Zookeeper-hibát ad az Azure HDInsightban

Ez a cikk az Azure HDInsight-fürtök interaktív lekérdezési összetevőinek használatával kapcsolatos problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Apache Hive LLAP-fürt esetén a Zeppelin-értelmező a következő hibaüzenetet adja a lekérdezés végrehajtásakor:

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>Ok

A Zeppelin Hive JDBC értelmező rossz URL-re mutat.

## <a name="resolution"></a>Megoldás:

1. Keresse meg a Hive-összetevő összegzését, és másolja a "Hive JDBC URL-t" a vágólapra.

1. Navigálás a`https://clustername.azurehdinsight.net/zeppelin/#/interpreter`

1. A JDBC-beállítások szerkesztése: frissítse a hive.url értékét az 1.

1. Mentés, majd a lekérdezés újrapróbálkozása

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal, amely javítja az ügyfélélményt azáltal, hogy az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse át az Azure-támogatási kérelem létrehozása című, [továbbcímű tájékoztatót.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
