---
title: Az Apache kafka platformmal – Azure HDInsight az Azure Monitor naplóira
description: Ismerje meg, hogyan használhatja az Azure Monitor naplóira az Apache Kafka-fürtöt az Azure HDInsight-naplók elemzése.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2019
ms.openlocfilehash: 1e141aea3b22bfdcb981513f03e595b6c2f15466
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65147973"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>A HDInsight-beli Apache kafka-naplók elemzése

Ismerje meg, hogyan használható az Azure Monitor naplóira Apache Kafka on HDInsight által előállított naplók elemzéséhez.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Az Apache Kafka az Azure Monitor naplók engedélyezése

Az Azure Monitor naplóira ahhoz, hogy a HDInsight lépései megegyeznek az összes HDInsight-fürt. Az alábbi hivatkozások segítségével megtudhatja, hogyan hozhat létre és konfigurálja a szükséges szolgáltatások:

1. Hozzon létre egy Log Analytics-munkaterületet. További információkért lásd: a [naplók az Azure Monitor](../../azure-monitor/platform/data-platform-logs.md) dokumentumot.

2. Hozzon létre egy Kafka HDInsight-fürtön. További információkért lásd: a [a HDInsight Apache Kafka használatának első lépései](apache-kafka-get-started.md) dokumentumot.

3. A Kafka-fürt az Azure Monitor naplóira használatára konfigurálja. További információkért lásd: a [használata az Azure Monitor naplózza a HDInsight figyelése](../hdinsight-hadoop-oms-log-analytics-tutorial.md) dokumentumot.

> [!IMPORTANT]  
> Mielőtt adatokat érhető el az Azure Monitor-naplók körülbelül 20 percet igénybe vehet.

## <a name="query-logs"></a>Lekérdezések naplói

1. Az a [az Azure portal](https://portal.azure.com), válassza ki a Log Analytics-munkaterületre.

2. A bal oldali menüből alatt **általános**válassza **naplók**. Itt megkeresheti a Kafka begyűjtött adatokat. Adjon meg egy lekérdezést a lekérdezési ablakban, majd **futtatása**. Az alábbiakban néhány példa keresések:

* Használat:

    ```kusto
    Perf 
    | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* CPU-használat:

    ```kusto
    Perf 
    | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* Másodpercenkénti bejövő üzenetek:

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* Bejövő bájtok másodpercenként:

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* Kimenő bájtok másodpercenként:

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

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

További információ az Azure Monitor: [Azure Monitor áttekintése](../../log-analytics/log-analytics-get-started.md), és [lekérdezést az Azure Monitor naplózza a HDInsight-fürtök figyelése](../hdinsight-hadoop-oms-log-analytics-use-queries.md).

Apache Kafka használatával kapcsolatos további információkért tekintse meg a következő dokumentumokat:

* [Tükrözött Apache Kafka HDInsight-fürtök között](apache-kafka-mirroring.md)
* [A kapacitás bővítése érdekében az Apache Kafka on HDInsight](apache-kafka-scalability.md)
* [Apache Spark Stream (DStreams) az Apache kafka platformmal](../hdinsight-apache-spark-with-kafka.md)
* [Az Apache Spark strukturált Stream az Apache kafka platformmal használata](../hdinsight-apache-kafka-spark-structured-streaming.md)
