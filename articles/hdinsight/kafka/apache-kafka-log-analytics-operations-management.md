---
title: Az Apache kafka platformmal – Azure HDInsight az Azure Monitor naplóira
description: Ismerje meg, hogyan használhatja az Azure Monitor naplóira az Apache Kafka-fürtöt az Azure HDInsight-naplók elemzése.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/15/2018
ms.openlocfilehash: 281b4b8d20957cbbbf0f4ff52166e8c3a78b3e7d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58108137"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>A HDInsight-beli Apache kafka-naplók elemzése

Ismerje meg, hogyan használható az Azure Monitor naplóira Apache Kafka on HDInsight által előállított naplók elemzéséhez.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Az Apache Kafka az Azure Monitor naplók engedélyezése

Az Azure Monitor naplóira ahhoz, hogy a HDInsight lépései megegyeznek az összes HDInsight-fürt. Az alábbi hivatkozások segítségével megtudhatja, hogyan hozhat létre és konfigurálja a szükséges szolgáltatások:

1. Hozzon létre egy Log Analytics-munkaterületet. További információkért lásd: a [Ismerkedés a Log Analytics-munkaterület](https://docs.microsoft.com/azure/log-analytics) dokumentumot.

2. Hozzon létre egy Kafka HDInsight-fürtön. További információkért lásd: a [a HDInsight Apache Kafka használatának első lépései](apache-kafka-get-started.md) dokumentumot.

3. A Kafka-fürt az Azure Monitor naplóira használatára konfigurálja. További információkért lásd: a [használata az Azure Monitor naplózza a HDInsight figyelése](../hdinsight-hadoop-oms-log-analytics-tutorial.md) dokumentumot.

    > [!NOTE]  
    > A fürt használni az Azure Monitor naplóira használatával is konfigurálhatja a `Enable-AzureRmHDInsightOperationsManagementSuite` parancsmagot. Ez a parancsmag megköveteli a következő információkat:
    >
    > * A HDInsight-fürt neve.
    > * A munkaterület Azonosítóját, az Azure Monitor-naplók. A munkaterület-Azonosítót a Log Analytics-munkaterületen találhatja meg.
    > * A log analytics-kapcsolat elsődleges kulcsának. Keresse meg az elsődleges kulcs, nyissa meg a munkaterület az Azure Portalon, válassza a __speciális beállítások__ a bal oldali menüből. Speciális beállításainak megadásához válassza __csatlakoztatott források__>__Linux-kiszolgálók__.


> [!IMPORTANT]  
> Mielőtt adatokat érhető el az Azure Monitor-naplók körülbelül 20 percet igénybe vehet.

## <a name="query-logs"></a>Lekérdezések naplói

1. Az a [az Azure portal](https://portal.azure.com), válassza ki a Log Analytics-munkaterületre.

2. Válassza ki __naplóbeli keresés__. Itt megkeresheti a Kafka begyűjtött adatokat. Az alábbiakban néhány példa keresések:

   * Használat: `Perf | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

   * CPU-használat: `Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

   * Másodpercenkénti bejövő üzenetek: `metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)`

   * Bejövő bájtok másodpercenként: `metrics_kafka_CL | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

   * Kimenő bájtok másodpercenként: `metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

     > [!IMPORTANT]  
     > A lekérdezési értékeket cserélje le a fürtre jellemző információkat. Ha például `ClusterName_s` kell állítani a fürt nevére. `HostName_s` a fürt egyik munkavégző csomópontjához tartomány nevére kell állítani.

     Is megadhat `*` naplózott összes típusok keresése. Jelenleg a következő naplók kapcsolódnak érhetők el a lekérdezésekhez:

     | Napló típusa | Leírás |
     | ---- | ---- |
     | log\_kafkaserver\_CL | Kafka broker server.log |
     | napló\_kafkacontroller\_CL | A Kafka-közvetítő controller.log |
     | metrikák\_kafka\_CL | Kafka JMX metrics |

     ![A CPU-használat keresés képe](./media/apache-kafka-log-analytics-operations-management/kafka-cpu-usage.png)
 
## <a name="next-steps"></a>További lépések

Az Azure Monitor további információkért lásd: a [Azure Monitor áttekintése](../../log-analytics/log-analytics-get-started.md) dokumentumot.

Apache Kafka használatával kapcsolatos további információkért tekintse meg a következő dokumentumokat:

 * [Tükrözött Apache Kafka HDInsight-fürtök között](apache-kafka-mirroring.md)
 * [A kapacitás bővítése érdekében az Apache Kafka on HDInsight](apache-kafka-scalability.md)
 * [Apache Spark Stream (DStreams) az Apache kafka platformmal](../hdinsight-apache-spark-with-kafka.md)
 * [Az Apache Spark strukturált Stream az Apache kafka platformmal használata](../hdinsight-apache-kafka-spark-structured-streaming.md)
