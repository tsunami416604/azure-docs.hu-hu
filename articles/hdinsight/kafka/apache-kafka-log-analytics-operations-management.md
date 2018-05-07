---
title: Elemzés keresse meg a Apache Kafka – az Azure HDInsight |} Microsoft Docs
description: Útmutató Apache Kafka fürtből on Azure HDInsight naplóinak elemzése a Log Analytics segítségével.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2018
ms.author: larryfr
ms.openlocfilehash: 9f366631ced4392831ad9ed97898a88b3290cd22
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>A HDInsight az Apache Kafka naplóinak elemzése

Útmutató Apache Kafka a HDInsight által létrehozott naplók elemzése a Log Analytics segítségével.

## <a name="enable-log-analytics-for-kafka"></a>A Naplóelemzési Kafka engedélyezése

A Naplóelemzési ahhoz, hogy a HDInsight lépései megegyeznek a HDInsight-fürtök esetében. Az alábbi hivatkozások segítségével megtudhatja, hogyan hozza létre és konfigurálja a szükséges szolgáltatások:

1. A Naplóelemzési munkaterület létrehozása. További információkért lásd: a [Ismerkedjen meg a Naplóelemzési munkaterület](https://docs.microsoft.com/azure/log-analytics) dokumentum.

2. Hozzon létre egy Kafka HDInsight-fürthöz. További információkért lásd: a [indítsa el a HDInsight Apache Kafka](apache-kafka-get-started.md) dokumentum.

3. Konfigurálja a Kafka fürt Naplóelemzési. További információkért lásd: a [HDInsight figyeléséhez használja a Naplóelemzési](../hdinsight-hadoop-oms-log-analytics-tutorial.md) dokumentum.

    > [!NOTE]
    > A fürt Naplóelemzési használatával is konfigurálhatja a `Enable-AzureRmHDInsightOperationsManagementSuite` parancsmag. Ehhez a parancsmaghoz szükséges az alábbi adatokat:
    >
    > * A HDInsight-fürt nevét.
    > * A Naplóelemzési munkaterület azonosítója. A munkaterület Azonosítóját a Naplóelemzési munkaterület találja meg.
    > * A Naplóelemzési kapcsolat elsődleges kulcsát. Az elsődleges kulcs található, válassza ki a Naplóelemzési példányát, majd __OMS-portálon__. Az OMS-portálon, válassza ki __beállítások__, __csatlakoztatott források__, majd __Linux kiszolgálók__.


> [!IMPORTANT]
> Mielőtt adatok Naplóelemzési nagyjából 20 percet is igénybe vehet.

## <a name="query-logs"></a>Lekérdezés-naplók

1. Az a [Azure-portálon](https://portal.azure.com), válassza ki a Naplóelemzési munkaterület.

2. Válassza ki __keresési jelentkezzen__. Itt kereshet a Kafka gyűjtött adatokat. Az alábbiakban néhány keresési példák:

    * Lemezterület: `Perf | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

    * CPU-használat: `Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

    * A bejövő üzenetek / másodperc: `metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)`

    * Másodpercenként bejövő bájtok: `metrics_kafka_CL | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

    * Kimenő bájt / mp: `metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

    > [!IMPORTANT]
    > A lekérdezési értékeinek cserélje le a fürtre jellemző információkat. Például `ClusterName_s` a fürt nevére kell beállítani. `HostName_s` a tartománynév, a munkavégző csomópont a fürt értékre kell állítani.

    Megadhat `*` naplózott összes típus kereséséhez. Jelenleg a következő elérhetők naplók lekérdezéseket:

    | Napló típusa | Leírás |
    | ---- | ---- |
    | napló\_kafkaserver\_CL | Kafka broker Server.log elérési úton található |
    | napló\_kafkacontroller\_CL | Kafka broker controller.log |
    | metrikák\_kafka\_CL | Kafka JMX metrikák |

    ![A CPU-használat keresési képe](./media/apache-kafka-log-analytics-operations-management/kafka-cpu-usage.png)
 
 ## <a name="next-steps"></a>További lépések

A Naplóelemzési további információkért lásd: a [Ismerkedjen meg a Naplóelemzési munkaterület](../../log-analytics/log-analytics-get-started.md) dokumentum.

Kafka használatával kapcsolatos további információkért lásd a következő dokumentumokat:

 * [Tükrözött Kafka a HDInsight-fürtök között](apache-kafka-mirroring.md)
 * [A HDInsight Kafka, a méretezhetőség javítása érdekében](apache-kafka-scalability.md)
 * [Spark streaming (DStreams) rendelkező Kafka használata](../hdinsight-apache-spark-with-kafka.md)
 * [Válassza a Spark strukturált Kafka Stream továbbítása](../hdinsight-apache-kafka-spark-structured-streaming.md)
