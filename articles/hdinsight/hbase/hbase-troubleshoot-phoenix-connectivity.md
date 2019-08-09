---
title: Kapcsolódási problémák Apache Phoenix az Azure HDInsight
description: Kapcsolódási problémák Apache Phoenix az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/07/2019
ms.openlocfilehash: 641d622377bad7a1239efd526b93c6f0f0c08d4a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68887039"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>Forgatókönyv: Kapcsolódási problémák Apache Phoenix az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Nem lehet csatlakozni az Apache HBase a Apache Phoenix használatával. Az okok eltérőek lehetnek.

## <a name="cause-incorrect-ip"></a>Ok: Helytelen IP-cím

Az aktív Zookeeper csomópontjának helytelen IP-címe.

### <a name="resolution"></a>Megoldás:

Az aktív Zookeeper csomópont IP-címe Ambari felhasználói felületről azonosítható, a **HBase-> Gyorshivatkozások – > ZK***  **(aktív) – > Zookeeper információ**hivatkozásait követve. Szükség szerint javítsa ki.

---

## <a name="cause-systemcatalog-table-offline"></a>Ok: Rendszer. Katalógus tábla kapcsolat nélkül

A parancs `!tables`futtatásakor a következőhöz hasonló hibaüzenet jelenik meg:

```
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

A parancs `count 'SYSTEM.CATALOG'`futtatásakor a következőhöz hasonló hibaüzenet jelenik meg:

```
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>Megoldás:

Indítsa újra a HMaster szolgáltatást a Ambari felhasználói felületén lévő összes Zookeeper-csomóponton.

1. Nyissa meg a **HBase-> aktív HBase Master** hivatkozást a HBase összefoglaló szakaszában.

1. Az **összetevők** szakaszban indítsa újra a HBase Master szolgáltatást.

1. Ismételje meg a fenti lépéseket a fennmaradó **készenléti HBase Master** szolgáltatásokhoz.

Akár 5 percet is igénybe vehet, hogy HBase Master szolgáltatás stabilizálja és fejezze be a helyreállítást. Ha a `SYSTEM.CATALOG` tábla visszatér a normális kerékvágásba, a Apache Phoenix kapcsolódási problémáját automatikusan meg kell oldani.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a Azure Portaltól [](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
