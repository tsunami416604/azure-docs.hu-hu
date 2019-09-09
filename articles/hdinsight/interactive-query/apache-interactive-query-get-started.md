---
title: Mi az az interaktív lekérdezés az Azure HDInsight?
description: Bevezetés az interaktív lekérdezésbe, más néven Apache Hive LLAP, az Azure HDInsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 06/14/2019
ms.openlocfilehash: 29b2a9378abaaa697d2d869145d5e912d6c06d6c
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70811595"
---
# <a name="what-is-interactive-query-in-azure-hdinsight"></a>Mi az az interaktív lekérdezés az Azure HDInsight-ben?

Az interaktív lekérdezés (más néven Apache Hive LLAP vagy [alacsony késésű analitikai feldolgozás](https://cwiki.apache.org/confluence/display/Hive/LLAP)) egy Azure HDInsight- [fürt típusú](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types). Az interaktív lekérdezés támogatja a memóriabeli gyorsítótárazást, ami gyorsabb és sokkal több interaktív Apache Hive-lekérdezéseket tesz lehetővé. Az ügyfelek interaktív lekérdezéssel kérdezik le az Azure Storage & Azure Data Lake Storageban tárolt adatlekérdezéseket rendkívül gyors módon. Az interaktív lekérdezés megkönnyíti a fejlesztők és az adatfejlesztők számára, hogy a leginkább kedvelt BI-eszközökkel működjenek együtt a big data. A HDInsight interaktív lekérdezése több eszközt is támogat, amelyekkel könnyen elérhetővé big data.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Egy interaktív lekérdezési fürt különbözik egy Apache Hadoop fürttől. Csak a kaptár szolgáltatást tartalmazza.

Az interaktív lekérdezési fürtben csak az Apache Ambari kaptár View, a Beeline és a Microsoft kaptár Open Database Connectivity Driver (kaptár ODBC) használatával férhet hozzá a kaptár szolgáltatáshoz. Nem fér hozzá a kaptár-konzolon, a Templeton, a klasszikus Azure CLI-n vagy a Azure PowerShellon keresztül.

## <a name="create-an-interactive-query-cluster"></a>Interaktív lekérdezési fürt létrehozása

További információ a HDInsight-fürtök létrehozásáról: [Apache Hadoop-fürtök létrehozása a HDInsight-ben](../hdinsight-hadoop-provision-linux-clusters.md). Válassza ki az interaktív lekérdezési fürt típusát.

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Apache Hive lekérdezések végrehajtása interaktív lekérdezésből

A kaptár-lekérdezések végrehajtásához a következő lehetőségek állnak rendelkezésre:

* A Microsoft Power BI használata

    Lásd: az [interaktív lekérdezési Apache Hive az Azure-beli Power BIekkel való HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) lásd: [Big Data megjelenítése az Azure HDInsight Power bi](../hadoop/apache-hadoop-connect-hive-power-bi.md).

* A Visual Studio használata

    Lásd: [Kapcsolódás az Azure HDInsight és Apache Hive lekérdezések futtatása a Visual studióhoz készült Data Lake Tools használatával](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).

* A Visual Studio Code használata

    Lásd: [a Visual Studio Code használata Apache Hive, LLAP vagy pySpark](../hdinsight-for-vscode.md).
* Apache Hive futtatása az Apache Ambari kaptár nézet használatával.
  
    Lásd: [Apache Hive nézet használata az Azure HDInsight Apache Hadoopával](../hadoop/apache-hadoop-use-hive-ambari-view.md).

* Apache Hive futtatása a Beeline használatával.
  
    Lásd: a [Apache Hive használata a HDInsight-ben való Apache Hadoop a Beeline paranccsal](../hadoop/apache-hadoop-use-hive-beeline.md).
  
    A Beeline a fő csomópontból vagy egy üres peremhálózati csomópontból is használható. Azt javasoljuk, hogy a Beeline üres peremhálózati csomópontról legyen használatban. A HDInsight-fürtök üres peremhálózati csomóponttal való létrehozásával kapcsolatos információkért lásd: [üres peremhálózati csomópontok használata a HDInsight-ben](../hdinsight-apps-use-edge-node.md).
* Apache Hive futtatása a kaptár ODBC használatával.
  
    Lásd: [az Excel Összekapcsolásának Apache Hadoop a Microsoft kaptár ODBC-illesztővel](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

A Java Database Connectivity (JDBC) kapcsolati karakterláncának megkeresése:

1. Jelentkezzen be az Apache Ambari a következő URL-cím `https://<cluster name>.AzureHDInsight.net`használatával:.
2. A bal oldali menüben válassza a **struktúra**elemet.
3. Az URL-cím másolásához válassza a vágólap ikont:

   ![HDInsight Hadoop interaktív lekérdezési LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [hozhat létre interaktív lekérdezési fürtöket a HDInsight-ben](../hdinsight-hadoop-provision-linux-clusters.md).
* Ismerje meg, hogyan [jelenítheti meg Big dataeit az Azure HDInsight Power BIával](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Ismerje meg, hogyan [futtathat Apache Hive-lekérdezéseket az Azure HDInsight az Apache Zeppelin használatával](../interactive-query/hdinsight-connect-hive-zeppelin.md).
