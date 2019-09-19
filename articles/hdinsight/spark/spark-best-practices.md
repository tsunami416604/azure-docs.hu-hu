---
title: Apache Spark ajánlott eljárások az Azure HDInsight
description: Ismerje meg az Azure HDInsight Apache Spark használatának ajánlott eljárásait.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 20033e52e862f086d1491c06d38cdf4f2c57ba8d
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106124"
---
# <a name="apache-spark-best-practices"></a>Apache Spark ajánlott eljárások

Ez a cikk az Azure HDInsight Apache Spark használatának különböző ajánlott eljárásait ismerteti.

## <a name="how-do-i-run-or-submit-spark-jobs"></a>Hogyan a Spark-feladatok futtatását vagy elküldését?

| Beállítás | Dokumentumok |
|---|---|
| VSCode | [A Spark &-struktúra eszközeinek használata a Visual Studio Code-hoz](../hdinsight-for-vscode.md) |
| Jupyter-notebookok | [Oktatóanyag: Adatterhelés és lekérdezések futtatása egy Apache Spark-fürtön az Azure HDInsight](./apache-spark-load-data-run-query.md) |
| IntelliJ | [Oktatóanyag: Azure Toolkit for IntelliJ használata Apache Spark-alkalmazások HDInsight-fürthöz való létrehozásához](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [Oktatóanyag: Scala Maven-alkalmazás létrehozása a HDInsight-Apache Sparkhoz a IntelliJ használatával](./apache-spark-create-standalone-application.md) |
| Zeppelin-jegyzetfüzetek | [Apache Zeppelin-notebookok használata Apache Spark-fürtökön az Azure HDInsight rendszerében](./apache-spark-zeppelin-notebook.md) |
| Távoli feladatok beküldése a Livy | [Az Apache Spark REST API használata távoli feladatok küldéséhez egy HDInsight Spark-fürtre](./apache-spark-livy-rest-interface.md) |

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>Hogyan a Spark-feladatok figyelését és hibakeresését?

| Beállítás | Dokumentumok |
|---|---|
| IntelliJ-hez készült Azure-eszközkészlet | [Hiba a Spark-feladatok hibakereséséhez Azure Toolkit for IntelliJ (előzetes verzió)](apache-spark-intellij-tool-failure-debug.md) |
| Azure Toolkit for IntelliJ SSH-n keresztül | [HDInsight-fürtön futó Apache Spark-alkalmazások helyi vagy távoli hibakeresése az IntelliJ-hez készült Azure-eszközkészlettel SSH-n keresztül](apache-spark-intellij-tool-debug-remotely-through-ssh.md) |
| Azure Toolkit for IntelliJ VPN-en keresztül | [Apache Spark-alkalmazások távoli hibakeresése a HDInsight a VPN-en keresztül a Azure Toolkit for IntelliJ használatával](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) |
| A Apache Spark History Serveren futó feladatok gráfja | [Kiterjesztett Apache Spark-előzménykiszolgáló használata az Apache Spark-alkalmazások hibakereséséhez és diagnosztizálásához](./apache-azure-spark-history-server.md) |

## <a name="how-do-i-make-my-spark-jobs-run-more-efficiently"></a>Hogyan, hogy a Spark-feladatok hatékonyabban futnak?

| Beállítás | Dokumentumok |
|---|---|
| IO-gyorsítótár | [Apache Spark számítási feladatok teljesítményének javítása az Azure HDInsight IO cache (előzetes verzió) használatával](./apache-spark-improve-performance-iocache.md) |
| Beállítási lehetőségek | [Apache Spark feladatok optimalizálása](./apache-spark-perf.md) |

## <a name="how-do-i-connect-to-other-azure-services"></a>Hogyan csatlakozni más Azure-szolgáltatásokhoz?

| Beállítás | Dokumentumok |
|---|---|
| Apache Hive hdinsight | [Apache Spark és Apache Hive integrálása a méhkas Warehouse-összekötővel](../interactive-query/apache-hive-warehouse-connector.md) |
| HDInsight-alapú Apache HBase | [Apache HBase-információk olvasása és írása Apache Spark használatával](../hdinsight-using-spark-query-hbase.md) |
| Apache Kafka a HDInsight | [Oktatóanyag: Apache Spark strukturált streaming használata a HDInsight Apache Kafka használatával](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Azure Cosmos DB: Lambda-architektúra implementálása az Azure platformon](../../cosmos-db/lambda-architecture.md) |

## <a name="what-are-my-storage-options"></a>Mik a tárolási lehetőségeim?

| Beállítás | Dokumentumok |
|---|---|
| 2\. generációs Data Lake Storage | [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| 1\. generációs Data Lake Storage | [Data Lake Storage Gen1 használata az Azure HDInsight-fürtökkel](../hdinsight-hadoop-use-data-lake-store.md) |
| Azure Blob Storage | [Az Azure Storage és az Azure HDInsight-fürtök együttes használata](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>További lépések

* [Apache Spark beállítások konfigurálása](apache-spark-settings.md)
* [Apache Spark feladatok optimalizálása a HDInsight-ben](apache-spark-perf.md)
