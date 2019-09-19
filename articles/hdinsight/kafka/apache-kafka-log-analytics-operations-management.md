---
title: Apache Kafka-Azure-HDInsight Azure Monitor naplófájljai
description: Megtudhatja, hogyan elemezheti Azure Monitor naplókat az Azure HDInsight lévő Apache Kafka-fürtök naplófájljainak elemzéséhez.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2019
ms.openlocfilehash: 5739883984d4087d2b2a1bda66c01ff3cfa10eb0
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122599"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Apache Kafka naplók elemzése a HDInsight

Megtudhatja, hogyan használhatja a Azure Monitor naplókat a HDInsight Apache Kafka által generált naplók elemzéséhez.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Azure Monitor naplók engedélyezése Apache Kafka számára

A HDInsight Azure Monitor naplófájljainak engedélyezéséhez szükséges lépések megegyeznek az összes HDInsight-fürtön. Az alábbi hivatkozások segítségével megismerheti, hogyan hozhatja létre és konfigurálhatja a szükséges szolgáltatásokat:

1. Log Analytics munkaterület létrehozása. További információkért tekintse meg a [naplókat Azure monitor](../../azure-monitor/platform/data-platform-logs.md) dokumentumban.

2. Hozzon létre egy Kafka-t a HDInsight-fürtön. További információ: [Start with apache Kafka on HDInsight](apache-kafka-get-started.md) Document.

3. Konfigurálja a Kafka-fürtöt Azure Monitor naplók használatára. További információ: [Azure monitor naplók használata a HDInsight-dokumentumok figyeléséhez](../hdinsight-hadoop-oms-log-analytics-tutorial.md) .

> [!IMPORTANT]  
> Előfordulhat, hogy a rendszer körülbelül 20 percet vesz igénybe, mielőtt Azure Monitor naplók számára elérhetővé válik az adatnapló.

## <a name="query-logs"></a>Lekérdezési naplók

1. A [Azure Portal](https://portal.azure.com)válassza ki log Analytics munkaterületét.

2. A bal oldali menü **általános**területén válassza a **naplók**lehetőséget. Itt kereshet a Kafkaből gyűjtött adatok között. Adjon meg egy lekérdezést a lekérdezési ablakban, majd válassza a **Futtatás**lehetőséget. Az alábbiakban néhány példát keresünk:

* Lemezhasználat:

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

* Bejövő üzenetek másodpercenként:

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
    > Cserélje le a lekérdezési értékeket a fürtre vonatkozó információkkal. A nevet például `ClusterName_s` a fürt nevére kell beállítani. `HostName_s`a fürt munkavégző csomópontjának tartománynevére kell beállítani.

    Megadhatja `*` az összes naplózott típus keresését is. Jelenleg a következő naplók érhetők el a lekérdezésekhez:

    | Napló típusa | Leírás |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Kafka Broker Server. log |
    | log\_kafkacontroller\_CL | Kafka Broker Controller. log |
    | mérőszámok\_KafkaCL\_ | Kafka JMX metrics |

    ![Apache Kafka log Analytics CPU-használat](./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png)

## <a name="next-steps"></a>További lépések

További információ a Azure Monitorről: [Azure monitor áttekintés](../../log-analytics/log-analytics-get-started.md)és [Azure monitor naplók lekérdezése a HDInsight-fürtök figyeléséhez](../hdinsight-hadoop-oms-log-analytics-use-queries.md).

A Apache Kafka használatáról az alábbi dokumentumokban talál további információt:

* [Tükrözött Apache Kafka HDInsight-fürtök között](apache-kafka-mirroring.md)
* [Apache Kafka méretezhetőségének növelése a HDInsight](apache-kafka-scalability.md)
* [Apache Spark streaming (DStreams) használata a Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
* [Apache Spark strukturált adatfolyam használata Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
