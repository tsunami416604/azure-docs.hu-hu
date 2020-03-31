---
title: A fürtkezelés gyakorlati tanácsai – Azure HDInsight
description: Ismerje meg a HDInsight-fürtök kezelésével kapcsolatos gyakorlati tanácsokat.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 3010c1d597907e7d5c7f82c8b42721dc1f934f6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74782020"
---
# <a name="hdinsight-cluster-management-best-practices"></a>A HDInsight fürtkezelési gyakorlati tanácsok

Ismerje meg a HDInsight-fürtök kezelésével kapcsolatos gyakorlati tanácsokat.

## <a name="how-do-i-create-hdinsight-clusters"></a>Hogyan hozhatok létre HDInsight-fürtöket?

| Beállítás | Dokumentumok |
|---|---|
| Azure Data Factory | [Igény szerinti Apache Hadoop-fürtök létrehozása a HDInsightban az Azure Data Factory használatával](./hdinsight-hadoop-create-linux-clusters-adf.md) |
| Egyéni Erőforrás-kezelő sablon | [Apache Hadoop-fürtök létrehozása a HDInsightban erőforrás-kezelősablonok használatával](./hdinsight-hadoop-create-linux-clusters-arm-templates.md) |
| Gyorsindítási sablonok | [HDInsight gyorsútmutató-sablonok](https://azure.microsoft.com/resources/templates/?term=hdinsight) |
| Azure-minták | [HDInsight Azure-minták](https://docs.microsoft.com/samples/browse/?products=azure-hdinsight) |
| Azure portál | [Linux alapú fürtök létrehozása a HDInsightban az Azure Portal használatával](./spark/apache-spark-intellij-tool-plugin.md) |
| Azure CLI | [HDInsight-fürtök létrehozása az Azure CLI használatával](./hdinsight-hadoop-create-linux-clusters-azure-cli.md) |
| Azure PowerShell | [Linuxalapú fürtök létrehozása a HDInsightban az Azure PowerShell használatával](./hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |
| cURL | [Apache Hadoop-fürtök létrehozása az Azure REST API használatával](./hdinsight-hadoop-create-linux-clusters-curl-rest.md) |
| SDK-k (.NET, Python, Java) | [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet), [Python](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python), [Java](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable), [Go](https://docs.microsoft.com/azure/hdinsight/hdinsight-go-sdk-overview) |

> [!Note]
> Ha fürtöt hoz létre, és újra használja a fürtnevét egy korábban létrehozott fürtből, a fürt létrehozása előtt várja meg, amíg befejeződik az előző fürttörlés.

## <a name="how-do-i-customize-hdinsight-clusters"></a>Hogyan szabhatók testre a HDInsight-fürtök?

| Beállítás | Dokumentumok |
|---|---|
| Parancsfájl-műveletek | [Az Azure HDInsight-fürtök testreszabása parancsfájlműveletek használatával](./hdinsight-hadoop-customize-cluster-linux.md) |
| Rendszerindítás | [A HDInsight-fürtök testreszabása a Bootstrap használatával](./hdinsight-hadoop-customize-cluster-bootstrap.md) |
| Külső metaboltok | [Külső metaadattárak használata az Azure HDInsightban](./hdinsight-use-external-metadata-stores.md) |
| Egyéni Ambari-adatbázis | [HDInsight-fürtök beállítása egyéni Ambari DB-vel](./hdinsight-custom-ambari-db.md) |

## <a name="what-are-some-errors-i-might-face-when-creating-clusters"></a>Milyen hibákkal szembesülhetek a fürtök létrehozásakor?

| Hiba | További információ |
|---|---|
| Nincs kvóta | Az egyes régiókban az előfizetésen létrehozható kvóták száma kvótákat. További információ: [Kapacitástervezés: kvóták](./hdinsight-capacity-planning.md). |
| Nincs több IP-cím elérhető | Minden virtuális hálózat korlátozott számú IP-címmel rendelkezik. HDInsight-fürt létrehozásakor minden csomópont (beleértve az állattartót és az átjárócsomópontokat is) ezen ip-címek némelyikét használja. Amikor az összes IP-cím használatban van, ez a hiba fog bekövetkezni.  |
| A hálózati biztonsági csoport (NSG) szabályai nem engedélyezik a HDInsight-erőforrás-szolgáltatókkal való kommunikációt | Ha NSG-k vagy a felhasználó által definiált útvonalak (UDRs) a HDInsight-fürt bejövő forgalom szabályozására, biztosítania kell, hogy a fürt képes kommunikálni a kritikus Azure-állapot-és felügyeleti szolgáltatások. További információ: [Network security group (NSG) service tags for Azure HDInsight](./hdinsight-service-tags.md) |
| A fürtnév újrafelhasználása | Ha olyan fürtnevet használ, amelyet korábban használt, a fürt újbóli létrehozása előtt x percet kell várnia. Ellenkező esetben megjelenik egy üzenet arról, hogy az erőforrás már létezik. |

## <a name="how-do-i-manage-running-hdinsight-clusters"></a>Hogyan kezelhetem a HDInsight-fürtök futtatását?

| Beállítás | Dokumentumok |
|---|---|
| Automatikus méretezés | [Az Azure HDInsight-fürtök automatikus méretezése](./hdinsight-autoscale-clusters.md) |
| Manuális skálázás | [Azure HDInsight-fürtök méretezése](./hdinsight-scaling-best-practices.md) |
| Monitorozás Ambari-val| [Fürtteljesítmény figyelése az Azure HDInsightban](./hdinsight-key-scenarios-to-monitor.md) |
| Figyelés az Azure Monitor naplóival | [Azure Monitor-naplók használata HDInsight-fürtök monitorozásához](./hdinsight-hadoop-oms-log-analytics-tutorial.md) |

## <a name="how-do-i-check-on-deleted-hdinsight-clusters"></a>Hogyan ellenőrizhetem a törölt HDInsight-fürtöket?

### <a name="azure-monitor-logs"></a>Azure Monitor-naplók

A törölt fürtök figyeléséhez használhatja a következő lekérdezést az Azure Monitor naplóival.

```loganalytics
AzureActivity
| where ResourceProvider == "Microsoft.HDInsight" and (OperationName == "Create or Update Cluster" or OperationName == "Delete Cluster") and ActivityStatus == "Succeeded"
```

## <a name="next-steps"></a>További lépések

* [HDInsight-fürtök kapacitástervezése](./hdinsight-capacity-planning.md)
* [Melyek az Azure HDInsight alapértelmezett és ajánlott csomópont-konfigurációi?](./hdinsight-supported-node-configuration.md)