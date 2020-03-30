---
title: Mi az interaktív lekérdezés az Azure HDInsightban?
description: Bevezetés az Interaktív lekérdezésbe, más néven Apache Hive LLAP, Az Azure HDInsight-ban
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive
ms.date: 03/03/2020
ms.openlocfilehash: e133e08e333cb478269a93cce963566e195d6949
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78271955"
---
# <a name="what-is-interactive-query-in-azure-hdinsight"></a>Mi az interaktív lekérdezés az Azure HDInsightban?

Az interaktív lekérdezés (más néven Apache Hive LLAP vagy [Low Latency Analytical Processing)](https://cwiki.apache.org/confluence/display/Hive/LLAP)egy Azure HDInsight-fürttípus. [cluster type](../hdinsight-hadoop-provision-linux-clusters.md#cluster-type) Az Interaktív lekérdezés támogatja a memórián belüli gyorsítótárazást, ami gyorsabbá és sokkal interaktívabbá teszi az Apache Hive-lekérdezéseket. Az ügyfelek az Interaktív lekérdezés segítségével szupergyors módon lekérdezhetik az Azure Data Lake Storage-ban tárolt adatokat, & az Azure Data Lake Storage-ban tárolt adatokat. Az interaktív lekérdezés megkönnyíti a fejlesztők és az adattudós számára, hogy a legjobban kedvelt üzletiintelligencia-eszközökkel dolgoznak a big data-adatokkal. A HDInsight Interactive Query számos eszközt támogat a big data egyszerű eléréséhez.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Az interaktív lekérdezési fürt eltér az Apache Hadoop-fürttől. Csak a Hive szolgáltatást tartalmazza.

A Hive szolgáltatás az interaktív lekérdezési fürtben csak az Apache Ambari Hive View, beeline és a Microsoft Hive Open Database Connectivity driver (Hive ODBC) segítségével érhető el. Nem érheti el a Hive konzolon, Templeton, az Azure Classic CLI vagy az Azure PowerShell keresztül.

## <a name="create-an-interactive-query-cluster"></a>Interaktív lekérdezési fürt létrehozása

A HDInsight-fürtök létrehozásáról az [Apache Hadoop-fürtök létrehozása a HDInsightban](../hdinsight-hadoop-provision-linux-clusters.md)című témakörben talál további információt. Válassza az Interaktív lekérdezés fürttípusát.

> [!IMPORTANT]
> Az interaktív lekérdezési fürtök minimális headnode mérete Standard_D13_v2. További információkért tekintse meg az [Azure virtuális gép méretezési diagramját.](../../cloud-services/cloud-services-sizes-specs.md#dv2-series)

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Apache Hive-lekérdezések végrehajtása az interaktív lekérdezésből

A Hive-lekérdezések végrehajtásához a következő lehetőségek közül választhat:

|Módszer |Leírás |
|---|---|
|Microsoft Power BI|Lásd: [Visualize Interactive Query Apache Hive-adatok a Power BI-val az Azure HDInsightban,](./apache-hadoop-connect-hive-power-bi-directquery.md)és [a big data megjelenítése a Power BI-val az Azure HDInsightban.](../hadoop/apache-hadoop-connect-hive-power-bi.md)|
|Visual Studio|Lásd: [Csatlakozás az Azure HDInsighthoz, és apache Hive-lekérdezések futtatása a Data Lake Tools for Visual Studio használatával.](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries)|
|Visual Studio Code|Lásd: [Visual Studio-kód használata Apache Hive, LLAP vagy pySpark esetén.](../hdinsight-for-vscode.md)|
|Apache Ambari Hive nézet|Lásd: [Apache Hive-nézet használata az Apache Hadoop használatával az Azure HDInsightban.](../hadoop/apache-hadoop-use-hive-ambari-view.md) A Hive view nem érhető el a HDInsight 4.0-s verzióhoz.|
|Apache méh|Lásd: [Apache Hive használata az Apache Hadoop segítségével a HDInsight ban a Beeline segítségével.](../hadoop/apache-hadoop-use-hive-beeline.md) Használhatja a Beeline-t a főcsomópontból vagy egy üres peremcsomópontból. Javasoljuk, hogy a Beeline-t egy üres peremcsomópontból használja. A HDInsight-fürt üres peremhálózati csomópont használatával történő létrehozásáról a [HDInsight üres peremhálózati csomópontjainak használata](../hdinsight-apps-use-edge-node.md)című témakörben talál további információt.|
|Hive ODBC|Lásd: [Csatlakozás az Excelhez az Apache Hadoophoz a Microsoft Hive ODBC illesztőprogrammal.](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)|

A Java Database Connectivity (JDBC) kapcsolati karakterláncának megkeresése:

1. Egy webböngészőből keresse `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`meg `CLUSTERNAME` a , ahol a fürt neve.
1. Az URL másolásához jelölje ki a vágólap ikonját:

   ![HDInsight Hadoop interaktív lekérdezés LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogy [miként hozhat létre interaktív lekérdezési fürtöket a HDInsight ban.](../hdinsight-hadoop-provision-linux-clusters.md)
* Megtudhatja, hogy [miként jelenítheti meg a big data-adatokat az Azure HDInsight Power BI szolgáltatásával.](../hadoop/apache-hadoop-connect-hive-power-bi.md)
* Ismerje meg, hogyan [futtathat Apache Zeppelin használatával Apache Hive-lekérdezéseket az Azure HDInsightban.](../interactive-query/hdinsight-connect-hive-zeppelin.md)
