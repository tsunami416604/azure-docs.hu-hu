---
title: Az Apache Spark bevált módszerei az Azure HDInsightban
description: Ismerje meg az Apache Spark azure HDInsightban való használatával kapcsolatos gyakorlati tanácsokat.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 20033e52e862f086d1491c06d38cdf4f2c57ba8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71106124"
---
# <a name="apache-spark-best-practices"></a>Az Apache Spark bevált módszerei

Ez a cikk az Apache Spark Azure HDInsight-alapú használatának különböző gyakorlati tanácsait ismerteti.

## <a name="how-do-i-run-or-submit-spark-jobs"></a>Hogyan futtathatom vagy küldhetem el a Spark-feladatokat?

| Beállítás | Dokumentumok |
|---|---|
| VSCode | [A Spark & Hive-eszközök használata a Visual Studio-kódhoz](../hdinsight-for-vscode.md) |
| Jupyter-notebookok | [Oktatóanyag: Adatok betöltése és lekérdezések futtatása egy Apache Spark-fürtön az Azure HDInsightban](./apache-spark-load-data-run-query.md) |
| IntelliJ | [Oktatóanyag: Az Azure Toolkit for IntelliJ használatával Apache Spark-alkalmazásokat hozhat létre egy HDInsight-fürthöz](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [Oktatóanyag: Hozzon létre egy Scala Maven alkalmazást az Apache Spark hoz a HDInsightban az IntelliJ használatával](./apache-spark-create-standalone-application.md) |
| Zeppelin-notebookok | [Apache Zeppelin-notebookok használata Apache Spark-fürtökön az Azure HDInsight rendszerében](./apache-spark-zeppelin-notebook.md) |
| Távoli feladatbeküldések livyvel | [Az Apache Spark REST API használata távoli feladatok küldéséhez egy HDInsight Spark-fürtre](./apache-spark-livy-rest-interface.md) |

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>Hogyan figyelhetem és debug spark-feladatok?

| Beállítás | Dokumentumok |
|---|---|
| Az Azure Toolkit for IntelliJ | [Hiba szikrafeladat-hibakeresés az Azure Toolkit for IntelliJ segítségével (előzetes verzió)](apache-spark-intellij-tool-failure-debug.md) |
| Azure Toolkit for IntelliJ az SSH-n keresztül | [HDInsight-fürtön futó Apache Spark-alkalmazások helyi vagy távoli hibakeresése az IntelliJ-hez készült Azure-eszközkészlettel SSH-n keresztül](apache-spark-intellij-tool-debug-remotely-through-ssh.md) |
| Azure Toolkit for IntelliJ VPN-en keresztül | [Az Azure Toolkit for IntelliJ segítségével távolról debugolhat apache Spark-alkalmazásokat a HDInsightban VPN-en keresztül](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) |
| Feladatgrafikon az Apache Spark előzménykiszolgálóján | [Kiterjesztett Apache Spark-előzménykiszolgáló használata az Apache Spark-alkalmazások hibakereséséhez és diagnosztizálásához](./apache-azure-spark-history-server.md) |

## <a name="how-do-i-make-my-spark-jobs-run-more-efficiently"></a>Hogyan tudom hatékonyabban futtatni a Spark-feladatokat?

| Beállítás | Dokumentumok |
|---|---|
| I/O-gyorsítótár | [Az Apache Spark-munkaterhelések teljesítményének javítása az Azure HDInsight IO-gyorsítótár (előzetes verzió) használatával](./apache-spark-improve-performance-iocache.md) |
| Beállítási lehetőségek | [Az Apache Spark-feladatok optimalizálása](./apache-spark-perf.md) |

## <a name="how-do-i-connect-to-other-azure-services"></a>Hogyan csatlakozhatok más Azure-szolgáltatásokhoz?

| Beállítás | Dokumentumok |
|---|---|
| Apache Hive a HDInsighton | [Integrálja az Apache Sparkot és az Apache Hive-t a Hive Raktár összekötővel](../interactive-query/apache-hive-warehouse-connector.md) |
| HDInsight-alapú Apache HBase | [Az Apache Spark használata Apache HBase-adatok írására és olvasására](../hdinsight-using-spark-query-hbase.md) |
| Apache Kafka a HDInsight-on | [Oktatóanyag: Az Apache Spark strukturált stream használata az Apache Kafkával a HDInsighton](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Azure Cosmos DB: Lambda-architektúra megvalósítása az Azure platformon](../../cosmos-db/lambda-architecture.md) |

## <a name="what-are-my-storage-options"></a>Milyen tárolási lehetőségeim vannak?

| Beállítás | Dokumentumok |
|---|---|
| 2. generációs Data Lake Storage | [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| 1. generációs Data Lake Storage | [A Data Lake Storage Gen1 használata az Azure HDInsight-fürtökkel](../hdinsight-hadoop-use-data-lake-store.md) |
| Azure Blob Storage | [Az Azure Storage és az Azure HDInsight-fürtök együttes használata](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>További lépések

* [Az Apache Spark beállításainak konfigurálása](apache-spark-settings.md)
* [Az Apache Spark-feladatok optimalizálása a HDInsightban](apache-spark-perf.md)
