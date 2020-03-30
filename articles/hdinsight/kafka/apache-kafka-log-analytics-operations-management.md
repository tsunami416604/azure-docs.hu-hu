---
title: Az Azure Monitor naplói az Apache Kafka számára – Azure HDInsight
description: Ismerje meg, hogyan elemezheti az Azure-figyelő naplóival az Apache Kafka-fürt naplóit az Azure HDInsightban.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/17/2020
ms.openlocfilehash: 3f8ff3cbc24f6e3a7e0eccf1b18e01941c9584b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471180"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Apache Kafka on HDInsight-naplók elemzése

Ismerje meg, hogyan elemezheti az Apache Kafka által a HDInsighton létrehozott naplókat az Azure Monitor-naplók használatával.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="logs-location"></a>Naplók helye

Apache Kafka naplók a fürtben `/var/log/kafka`található . A Kafka-naplók nem kerülnek mentésre vagy a fürt életciklusa során megőrzött állapotba, függetlenül attól, hogy felügyelt lemezeket használnak-e. Az alábbi táblázat a rendelkezésre álló naplókat mutatja be.

|Napló |Leírás |
|---|---|
|kafka.out|a Kafka-folyamat stdout és stderr. Ebben a fájlban megtalálja a Kafka indítási és leállítási naplóit.|
|kiszolgáló.log|A fő Kafka szerver napló. Minden Kafka bróker napló ja.|
|vezérlő.log|Controller naplók, ha a bróker jár, mint vezérlő.|
|statechange.log|A fájlban a brókerek állapotmódosítási eseményeit is naplózza a rendszer.|
|kafka-gc.log|Kafka Garbage Collection statisztika.|

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Az Azure Monitor-naplók engedélyezése az Apache Kafka számára

Az Azure Monitor-naplók HDInsight-naplóinak engedélyezéséhez az összes HDInsight-fürt esetében ugyanazok. Az alábbi hivatkozások segítségével megtudhatja, hogyan hozhat létre és konfigurálhat a szükséges szolgáltatásokat:

1. Hozzon létre egy Log Analytics-munkaterületet. További információkért tekintse meg a Naplók az [Azure Monitorban](../../azure-monitor/platform/data-platform-logs.md) dokumentum.

2. Hozzon létre egy Kafka a HDInsight-fürtön. További információ: [Start with Apache Kafka on HDInsight](apache-kafka-get-started.md) document.

3. Konfigurálja a Kafka-fürt az Azure Monitor naplók használatára. További információ: [Az Azure Monitor naplók használata hdinsight-dokumentum figyeléséhez.](../hdinsight-hadoop-oms-log-analytics-tutorial.md)

> [!IMPORTANT]  
> Az Azure Monitor-naplókhoz az adatok elérhetővé nem válik, körülbelül 20 percet is igénybe vehet.

## <a name="query-logs"></a>Lekérdezési naplók

1. Az [Azure Portalon](https://portal.azure.com)válassza ki a Log Analytics-munkaterületet.

2. A bal oldali menü **Általános**területén válassza **a Naplók**lehetőséget. Innen kereshet a Kafka-tól gyűjtött adatokközött. Írjon be egy lekérdezést a lekérdezési ablakba, majd válassza a **Futtatás lehetőséget.** Az alábbiakban néhány példa keres:

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

* Bejövő üzenetek másodpercenként: (Cserélje le `your_kafka_cluster_name` a fürt nevét.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* Bejövő bájt másodpercenként: (Csere `wn0-kafka` egy munkavégző csomópont állomásnevére.)

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* Kimenő bájt másodpercenként: (Cserélje `your_kafka_cluster_name` le a fürt nevét.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

    Azt is `*` megadhatja, hogy keressen az összes naplózott típus. Jelenleg a következő naplók érhetők el a lekérdezésekhez:

    | Napló típusa | Leírás |
    | ---- | ---- |
    | napló\_kafkaserver\_CL | Kafka bróker server.log |
    | log\_kafkacontroller\_CL | Kafka bróker controller.log |
    | mérőszámok\_kafka\_CL | Kafka JMX mérőszámok |

    ![Apache kafka log analytics cpu használat](./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png)

## <a name="next-steps"></a>További lépések

Az Azure Monitorról az [Azure Monitor áttekintése](../../log-analytics/log-analytics-get-started.md)című témakörben, valamint a [HDInsight-fürtök figyeléséhez az Azure-figyelő naplóiban olvashat bővebben.](../hdinsight-hadoop-oms-log-analytics-use-queries.md)

Az Apache Kafka együttműködésével kapcsolatos további információkért tekintse meg az alábbi dokumentumokat:

* [Az Apache Kafka tükrözése a HDInsight-fürtök között](apache-kafka-mirroring.md)
* [Az Apache Kafka méretének növelése a HDInsighton](apache-kafka-scalability.md)
* [Az Apache Spark streamelésének (DStreams) használata az Apache Kafka segítségével](../hdinsight-apache-spark-with-kafka.md)
* [Az Apache Spark strukturált streamelésének használata az Apache Kafka segítségével](../hdinsight-apache-kafka-spark-structured-streaming.md)
