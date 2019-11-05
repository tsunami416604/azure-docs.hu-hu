---
title: Apache Hive megtekintési időtúllépés a lekérdezési eredményből – Azure HDInsight
description: Apache Hive megtekintési időtúllépés az Azure HDInsight lekérdezési eredményének beolvasása során
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 86523a7564220191de252b362e45569116ff3111
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494185"
---
# <a name="scenario-apache-hive-view-times-out-when-fetching-a-query-result-in-azure-hdinsight"></a>Forgatókönyv: Apache Hive megtekintési időtúllépés az Azure HDInsight lekérdezési eredményének beolvasása során

Ez a cikk az interaktív lekérdezési összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Bizonyos lekérdezések Apache Hive nézetből való futtatásakor a következő hiba fordulhat elő:

```
result fetch timed out
java.util.concurrent.TimeoutException: deadline passed
```

## <a name="cause"></a>Ok

A kaptár nézet alapértelmezett időtúllépési értéke nem lehet megfelelő a futtatott lekérdezéshez. A megadott időtartam túl rövid ahhoz, hogy a struktúra nézet beolvassa a lekérdezés eredményét.

## <a name="resolution"></a>Felbontás

Növelje az Apache Ambari-struktúra nézet időtúllépéseit a következő tulajdonságok `/etc/ambari-server/conf/ambari.properties`ban való beállításával.

```
views.ambari.request.read.timeout.millis=300000
views.request.read.timeout.millis=300000
views.ambari.hive<HIVE_VIEW_INSTANCE_NAME>.result.fetch.timeout=300000
```

`HIVE_VIEW_INSTANCE_NAME` értéke a kaptár nézet URL-címének végén érhető el.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás a [@AzureSupporthoz](https://twitter.com/azuresupport) – a hivatalos Microsoft Azure fiók a felhasználói élmény javításához az Azure-Közösség és a megfelelő erőforrások összekapcsolásával: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletes információkat az [Azure-támogatáskérések létrehozásával](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) foglalkozó témakörben talál. Az előfizetés-kezeléssel és számlázással kapcsolatos támogatás a Microsoft Azure-előfizetés részét képezi, míg a technikai támogatást [Azure-támogatási csomagjainkkal](https://azure.microsoft.com/support/plans/) biztosítjuk.
