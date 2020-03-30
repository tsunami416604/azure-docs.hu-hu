---
title: Az Apache Phoenix kapcsolódási problémái az Azure HDInsightban
description: Kapcsolódási problémák az Apache HBase és az Apache Phoenix között az Azure HDInsightban
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: b886f51bcb2bb7308c49c76563dcb70148bbc583
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887291"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>Eset: Apache Phoenix kapcsolódási problémák az Azure HDInsightban

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Nem lehet csatlakozni az Apache HBase-hez az Apache Phoenix segítségével. Az okok eltérőek lehetnek.

## <a name="cause-incorrect-ip"></a>Ok: Helytelen IP-cím

Az aktív Zookeeper-csomópont helytelen IP-címe.

### <a name="resolution"></a>Megoldás:

Az aktív Zookeeper csomópont IP-címe az Ambari felhasználói felületről azonosítható a **HBase** > **Quick Links** > **ZK (Aktív)** > **Zookeeper Info elemre**mutató hivatkozások alapján. Szükség szerint javítsa ki az IP-címet.

---

## <a name="cause-systemcatalog-table-offline"></a>Ok: RENDSZER. CATALOG tábla offline módban

Parancsok futtatásakor, például `!tables`a :

```output
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

Parancsok futtatásakor, például `count 'SYSTEM.CATALOG'`a :

```output
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>Megoldás:

Az Apache Ambari felhasználói felületén hajtsa végre az alábbi lépéseket a HMaster szolgáltatás újraindításához az összes ZooKeeper csomóponton:

1. A HBase **Összefoglaló** szakaszában nyissa meg a **HBase** > Active HBase Master című**szakaszát.**

1. Az **Összetevők szakaszban** indítsa újra a HBase Master szolgáltatást.

1. Ismételje meg ezeket a lépéseket az összes fennmaradó **Készenléti HBase Master** szolgáltatás esetében.

A HBase Master szolgáltatás stabilizálása és befejezése akár öt percet is igénybe vehet. Miután `SYSTEM.CATALOG` a tábla visszatért a normál, a kapcsolódási probléma az Apache Phoenix automatikusan megoldódik.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében. Az Azure-közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse [át az Azure-támogatási kérelem létrehozása című áttekintést.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
