---
title: A cluster Management ajánlott eljárásai – Azure HDInsight
description: Ismerje meg a HDInsight-fürtök kezelésének ajánlott eljárásait.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 3010c1d597907e7d5c7f82c8b42721dc1f934f6f
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74782020"
---
# <a name="hdinsight-cluster-management-best-practices"></a>HDInsight-fürt kezelése – ajánlott eljárások

Ismerje meg a HDInsight-fürtök kezelésének ajánlott eljárásait.

## <a name="how-do-i-create-hdinsight-clusters"></a>Hogyan hozzon létre HDInsight-fürtöket?

| Lehetőség | Dokumentumok |
|---|---|
| Azure Data Factory | [Igény szerinti Apache Hadoop-fürtök létrehozása a HDInsight-ben Azure Data Factory használatával](./hdinsight-hadoop-create-linux-clusters-adf.md) |
| Egyéni Resource Manager-sablon | [Apache Hadoop-fürtök létrehozása a HDInsight-ben Resource Manager-sablonok használatával](./hdinsight-hadoop-create-linux-clusters-arm-templates.md) |
| Gyorssablonok | [HDInsight gyors üzembe helyezési sablonok](https://azure.microsoft.com/resources/templates/?term=hdinsight) |
| Azure-minták | [HDInsight Azure-minták](https://docs.microsoft.com/samples/browse/?products=azure-hdinsight) |
| Azure Portal | [Linux-alapú fürtök létrehozása a HDInsight-ben a Azure Portal használatával](./spark/apache-spark-intellij-tool-plugin.md) |
| Azure parancssori felület (CLI) | [HDInsight-fürtök létrehozása az Azure CLI használatával](./hdinsight-hadoop-create-linux-clusters-azure-cli.md) |
| Azure PowerShell | [Linux-alapú fürtök létrehozása a HDInsight-ben a Azure PowerShell használatával](./hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |
| cURL | [Apache Hadoop-fürtök létrehozása az Azure REST API használatával](./hdinsight-hadoop-create-linux-clusters-curl-rest.md) |
| SDK-k (.NET, Python, Java) | [.Net](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet), [Python](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python), [Java](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable), [Go](https://docs.microsoft.com/azure/hdinsight/hdinsight-go-sdk-overview) |

> [!Note]
> Ha egy korábban létrehozott fürtből hoz létre fürtöt, és újból felhasználja a fürtöt, várjon, amíg a fürt létrehozása előtt a fürt korábbi törlése be nem fejeződik.

## <a name="how-do-i-customize-hdinsight-clusters"></a>Hogyan testreszabja a HDInsight-fürtöket?

| Lehetőség | Dokumentumok |
|---|---|
| Parancsfájlok műveletei | [Azure HDInsight-fürtök testreszabása parancsfájl-műveletek használatával](./hdinsight-hadoop-customize-cluster-linux.md) |
| Rendszerindítási | [HDInsight-fürtök testreszabása a bootstrap használatával](./hdinsight-hadoop-customize-cluster-bootstrap.md) |
| Külső metaadattárak | [Külső metaadattárak használata az Azure HDInsightban](./hdinsight-use-external-metadata-stores.md) |
| Egyéni Ambari-adatbázis | [HDInsight-fürtök beállítása egyéni Ambari-ADATBÁZISsal](./hdinsight-custom-ambari-db.md) |

## <a name="what-are-some-errors-i-might-face-when-creating-clusters"></a>Milyen hibák merülhetnek fel fürtök létrehozásakor?

| Hiba | További információ |
|---|---|
| Nincs kvóta | Az előfizetésben létrehozható kvóták száma az egyes régiókban. További információ [: kapacitás megtervezése: kvóták](./hdinsight-capacity-planning.md). |
| Nem érhető el több IP-cím | Minden VNet korlátozott számú IP-címmel rendelkezik. HDInsight-fürt létrehozásakor az egyes csomópontok (beleértve a Zookeeper és az átjáró csomópontokat is) a kiosztott IP-címek némelyikét használják. Ha az összes IP-cím használatban van, ezt a hibát fogja tapasztalni.  |
| A hálózati biztonsági csoport (NSG) szabályai nem teszik lehetővé a HDInsight erőforrás-szolgáltatókkal való kommunikációt | Ha NSG vagy felhasználó által definiált útvonalakat (UDR) használ a HDInsight-fürt bejövő forgalmának vezérlésére, akkor biztosítania kell, hogy a fürt képes legyen kommunikálni a kritikus Azure-beli állapot-és felügyeleti szolgáltatásokkal. További információ: [hálózati biztonsági csoport (NSG) szolgáltatás címkéi az Azure HDInsight](./hdinsight-service-tags.md) |
| A fürt nevének újbóli használata | Ha olyan fürtöt használ, amelyet korábban használt, akkor a fürt újbóli létrehozása előtt meg kell várnia a percek számát. Ellenkező esetben egy üzenet jelenik meg, amely szerint az erőforrás már létezik. |

## <a name="how-do-i-manage-running-hdinsight-clusters"></a>Hogyan a futó HDInsight-fürtök kezelése?

| Lehetőség | Dokumentumok |
|---|---|
| Automatikus méretezés | [Azure HDInsight-fürtök automatikus méretezése](./hdinsight-autoscale-clusters.md) |
| Manuális skálázás | [Azure HDInsight-fürtök méretezése](./hdinsight-scaling-best-practices.md) |
| Figyelés a Ambari| [A fürt teljesítményének figyelése az Azure HDInsight](./hdinsight-key-scenarios-to-monitor.md) |
| Figyelés Azure Monitor naplókkal | [Azure Monitor-naplók használata HDInsight-fürtök monitorozásához](./hdinsight-hadoop-oms-log-analytics-tutorial.md) |

## <a name="how-do-i-check-on-deleted-hdinsight-clusters"></a>Hogyan a törölt HDInsight-fürtök ellenőrzését?

### <a name="azure-monitor-logs"></a>Azure Monitor-naplók

A törölt fürtök figyeléséhez használja a következő lekérdezést Azure Monitor naplókkal.

```loganalytics
AzureActivity
| where ResourceProvider == "Microsoft.HDInsight" and (OperationName == "Create or Update Cluster" or OperationName == "Delete Cluster") and ActivityStatus == "Succeeded"
```

## <a name="next-steps"></a>Következő lépések

* [HDInsight-fürtök kapacitástervezése](./hdinsight-capacity-planning.md)
* [Mik az Azure HDInsight alapértelmezett és ajánlott csomópont-konfigurációi?](./hdinsight-supported-node-configuration.md)