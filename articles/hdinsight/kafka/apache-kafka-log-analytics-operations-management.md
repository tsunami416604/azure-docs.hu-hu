---
title: Apache Kafka-Azure-HDInsight Azure Monitor naplófájljai
description: Megtudhatja, hogyan elemezheti Azure Monitor naplókat az Azure HDInsight lévő Apache Kafka-fürtök naplófájljainak elemzéséhez.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/17/2020
ms.openlocfilehash: 3f8ff3cbc24f6e3a7e0eccf1b18e01941c9584b9
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471180"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Apache Kafka naplók elemzése a HDInsight

Megtudhatja, hogyan használhatja a Azure Monitor naplókat a HDInsight Apache Kafka által generált naplók elemzéséhez.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="logs-location"></a>Naplók helye

A fürt Apache Kafka naplófájljai a következő helyen találhatók: `/var/log/kafka`. A kafka-naplók nem tárolódnak és nem maradnak meg a fürt életciklusa között, függetlenül attól, hogy a rendszer felügyelt lemezeket használ-e. A következő táblázat az elérhető naplókat mutatja be.

|Napló |Leírás |
|---|---|
|Kafka. out|a Kafka-folyamat stdout és stderr. Ebben a fájlban megtalálja a Kafka indítási és leállítási naplóit.|
|Server. log|A Kafka-kiszolgáló fő naplója. A Kafka-átvitelszervező összes naplója itt fejeződik be.|
|vezérlő. log|A vezérlő naplófájljai, ha a közvetítő vezérlőként működik.|
|statechange. log|A rendszer naplózza a fájlban lévő összes állapotváltozás-eseményt.|
|Kafka-GC. log|Kafka-Garbage gyűjtemény statisztikái.|

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

* Bejövő üzenetek másodpercenként: (cserélje `your_kafka_cluster_name`t a fürt nevére.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* Bejövő bájtok másodpercenként: (`wn0-kafka` cseréje a munkavégző csomópont állomásneve.)

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* Kimenő bájtok másodpercenként: (cserélje le a `your_kafka_cluster_name`t a fürt nevére.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

    Az összes naplózott típus kereséséhez `*` is megadhat. Jelenleg a következő naplók érhetők el a lekérdezésekhez:

    | Napló típusa | Leírás |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Kafka Broker Server. log |
    | log\_kafkacontroller\_CL | Kafka Broker Controller. log |
    | mérőszámok\_Kafka-\_CL | Kafka JMX metrics |

    ![Apache Kafka log Analytics CPU-használat](./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png)

## <a name="next-steps"></a>Következő lépések

További információ a Azure Monitorről: [Azure monitor áttekintés](../../log-analytics/log-analytics-get-started.md)és [Azure monitor naplók lekérdezése a HDInsight-fürtök figyeléséhez](../hdinsight-hadoop-oms-log-analytics-use-queries.md).

A Apache Kafka használatáról az alábbi dokumentumokban talál további információt:

* [Tükrözött Apache Kafka HDInsight-fürtök között](apache-kafka-mirroring.md)
* [Növelje Apache Kafka méretét a HDInsight](apache-kafka-scalability.md)
* [Apache Spark streaming (DStreams) használata a Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
* [Apache Spark strukturált adatfolyam használata Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
