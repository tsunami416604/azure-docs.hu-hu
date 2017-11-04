---
title: "Hibaelhárítási útmutatók az Azure HDInsight |} Microsoft Docs"
description: "Végezzen hibaelhárítást a Hadoop-munkaterhelések Azure HDInsight használatával. Részletes dokumentációja bemutatja, hogyan használja a HDInsight Hive, a Spark, a YARN, a HBase, a HDFS és a Storm gyakori problémák megoldására."
services: hdinsight
author: arijitt
manager: arijitt
layout: LandingPage
ms.assetid: 
ms.service: hdinsight
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing - page
ms.date: 7/06/2017
ms.author: arijitt
ms.openlocfilehash: 1a8657549e9e36ac6f876da2fd20456ba690533a
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="troubleshoot-by-using-azure-hdinsight"></a>Hibaelhárítás Azure HDInsight segítségével

| Apache munkaterhelés | Felső kérdések |
|---|---|
|![HBase](./media/hdinsight-troubleshoot-guide/HBASE.png)<br>[A HBase hibaelhárítása](hbase/apache-troubleshoot-hbase.md)|<br>[Hogyan futtathatok hbck parancs jelentéseket a ki nem osztott több régióba](hbase/apache-troubleshoot-hbase.md#how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions)<br><br>[Hogyan oldja meg időtúllépés problémák, régió hozzárendelések hbck parancsok használata esetén](hbase/apache-troubleshoot-hbase.md#how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments)<br><br>[Hogyan tegye I kényszerített – letiltása HDFS csökkentett módban egy fürtön](hbase/apache-troubleshoot-hbase.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)<br><br>[Hogyan állítsa helyre JDBC vagy SQLLine elérhetőségét Apache Phoenix problémái](hbase/apache-troubleshoot-hbase.md#how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix)<br><br>[Mi okozhatja, hogy elindítsa a fő kiszolgáló](hbase/apache-troubleshoot-hbase.md#what-causes-a-master-server-to-fail-to-start)<br><br>[Mi újraindítás hibát okoz a terület-kiszolgálón](hbase/apache-troubleshoot-hbase.md#what-causes-a-restart-failure-on-a-region-server)|
|![HDFS](./media/hdinsight-troubleshoot-guide/HDFS.png)<br>[HDFS hibaelhárítása](hdinsight-troubleshoot-hdfs.md)|<br>[Hogyan érhető el a fürtben található a helyi HDFS](hdinsight-troubleshoot-hdfs.md#how-do-i-access-local-hdfs-from-inside-a-cluster)<br><br>[Hogyan tegye I kényszerített – letiltása HDFS csökkentett módban egy fürtön](hdinsight-troubleshoot-hdfs.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)|
|![Hive](./media/hdinsight-troubleshoot-guide/HIVE.png)<br>[A HBase hibaelhárítása](hdinsight-troubleshoot-hive.md)|<br>[Hogyan egy Hive metaadattárhoz exportálhatja és importálhatja azokat a fürt egy másik](hdinsight-troubleshoot-hive.md#how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster)<br><br>[Hogyan keresik meg Hive naplók fürt](hdinsight-troubleshoot-hive.md#how-do-i-locate-hive-logs-on-a-cluster)<br><br>[Hogyan indítsa el a fürt adott konfigurációjú a Hive rendszerhéjat](hdinsight-troubleshoot-hive.md#how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster)<br><br>[Hogyan elemezheti a fürt kritikus útvonalra Tez DAG adatokat](hdinsight-troubleshoot-hive.md#how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path)<br><br>[Hogyan le Tez DAG adatok fürtből](hdinsight-troubleshoot-hive.md#how-do-i-download-tez-dag-data-from-a-cluster)|
|![Spark](./media/hdinsight-troubleshoot-guide/SPARK.png)<br>[Spark hibaelhárítása](hdinsight-troubleshoot-SPARK.md)|<br>[Hogyan konfigurálhatom a Spark-alkalmazások fürtök az Ambari használatával](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-ambari-on-clusters)<br><br>[Hogyan konfigurálhatom a Spark-alkalmazások fürtökön Jupyter notebook használatával](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-a-jupyter-notebook-on-clusters)<br><br>[Hogyan konfigurálhatom a Spark-alkalmazások fürtökön Livy használatával](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-livy-on-clusters)<br><br>[Hogyan konfigurálhatók a alkalmazás használatával spark-elküldeni egy Spark-fürtökön](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-spark-submit-on-clusters)<br><br>[Mi okozza a Spark OutOfMemoryError Alkalmazáskivétel](spark/apache-troubleshoot-spark.md#what-causes-a-spark-application-outofmemoryerror-exception)|
|![A Storm](./media/hdinsight-troubleshoot-guide/STORM.png)<br>[A Storm hibaelhárítása](hdinsight-troubleshoot-STORM.md)|<br>[Hogyan érhetem el az egy fürtön a Storm felhasználói felülete](storm/apache-troubleshoot-storm.md#how-do-i-access-the-storm-ui-on-a-cluster)<br><br>[Hogyan tegye I átvitele Storm hub spout ellenőrzőpont eseményadatok egy topológia között](storm/apache-troubleshoot-storm.md#how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another)<br><br>[Hogyan keresse meg a Storm bináris fürt](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-binaries-on-a-cluster)<br><br>[Hogyan állapítható meg a telepítési topológia a Storm-fürt](storm/apache-troubleshoot-storm.md#how-do-i-determine-the-deployment-topology-of-a-storm-cluster)<br><br>[Hogyan keresse meg a Storm event hub spout bináris fejlesztési](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-event-hub-spout-binaries-for-development)<br><br>[Hogyan keresse meg a Storm Log4J konfigurációs fájlok fürtökön](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-log4j-configuration-files-on-clusters)|
|![YARN](./media/hdinsight-troubleshoot-guide/YARN.png)<br>[YARN hibaelhárítása](hdinsight-troubleshoot-YARN.md)|<br>[Hogyan hozzon létre egy új YARN várólistát egy fürtön](hdinsight-troubleshoot-yarn.md#how-do-i-create-a-new-yarn-queue-on-a-cluster)<br><br>[Hogyan le a YARN naplóit fürtből](hdinsight-troubleshoot-yarn.md#how-do-i-download-yarn-logs-from-a-cluster)|

## <a name="hdinsight-troubleshooting-resources"></a>HDInsight-hibaelhárítási erőforrások

| További információ | Ezek a cikkek lásd: |
| --- | --- |
| HDInsight Linux-és optimalizálása | - [Információ a HDInsight használata Linux rendszeren](hdinsight-hadoop-linux-information.md)<br>- [Hadoop memória és a teljesítmény hibaelhárítása](hdinsight-hadoop-stack-trace-error-messages.md)<br>- [Hive-lekérdezések teljesítményét](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) |
| Naplók és memóriaképek | - [Linux bejelentkezik hozzáférést Hadoop YARN alkalmazás](hdinsight-hadoop-access-yarn-app-logs-linux.md)<br>- [Halommemória memóriaképek Linux Hadoop-szolgáltatások engedélyezése](hdinsight-hadoop-collect-debug-heap-dump-linux.md)<br>- [HDInsight naplóinak elemzése](hdinsight-debug-jobs.md)|
| Hibák | - [Ismertetés és hárítsa el a WebHCat hibákat](hdinsight-hadoop-templeton-webhcat-debug-errors.md)<br>- [Hive beállítások OutofMemory hiba elhárításához](hdinsight-hadoop-hive-out-of-memory-error-oom.md) |
| Eszközök | - [Az Ambari nézetek használata Tez feladatokhoz](hdinsight-debug-ambari-tez-view.md)<br>- [Hive-lekérdezések optimalizálása](hdinsight-hadoop-optimize-hive-query.md) |
