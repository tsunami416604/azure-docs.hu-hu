---
title: Az Azure HDInsight interaktív lekérdezés használata
description: Útmutató a HDInsight interaktív lekérdezés (Hive LLAP) használata.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.openlocfilehash: ae0264c918bb550affe8e694cdb3b9891edea531
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56875446"
---
# <a name="use-interactive-query-with-hdinsight"></a>HDInsight interaktív lekérdezés használata
Interaktív lekérdezés (más néven az Apache Hive LLAP, vagy [alacsony késés Analytical Processing](https://cwiki.apache.org/confluence/display/Hive/LLAP)) van egy Azure HDInsight [fürt típusa](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types). Interaktív lekérdezés támogatja a memórián belüli gyorsítótárazáshoz, ami lehetővé teszi az Apache Hive-lekérdezések gyorsabb és interaktívabb sokkal.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)] 

Az interaktív lekérdezési fürt különbözik az Apache Hadoop-fürt. Csak a Hive-szolgáltatást tartalmaz. 

> [!NOTE]  
> A Hive-szolgáltatást, az az interaktív lekérdezési fürt csak az Apache Ambari, Hive-nézet, Beeline és a Microsoft Hive adatforrásnevet illesztőprogram (Hive ODBC) keresztül érheti el. Nem férhet hozzá, a Hive konzolt, templeton eszközön keresztül végzett, a klasszikus Azure CLI vagy az Azure PowerShell használatával. 

## <a name="create-an-interactive-query-cluster"></a>Az interaktív lekérdezési fürt létrehozása
A HDInsight-fürt létrehozásával kapcsolatos információkért lásd: [Apache Hadoop-fürtök létrehozása a HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Válassza ki az interaktív lekérdezési fürtöt.

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Apache Hive-lekérdezések végrehajtása az interaktív lekérdezés
Hive-lekérdezések végrehajtásához a következő lehetőségek állnak rendelkezésére:

* Microsoft Power BI használata

    Lásd: [megjelenítése interaktív lekérdezés Apache Hive-adatok Power BI segítségével az Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) lásd [megjelenítése a Power bi használatával az Azure HDInsight a big Data típusú adatok](../hadoop/apache-hadoop-connect-hive-power-bi.md).
 
* Az Apache Zeppelin használata

    Lásd: [Apache Hive-lekérdezések futtatásához az Azure HDInsight használata az Apache Zeppelin](../hdinsight-connect-hive-zeppelin.md).

* A Visual Studio használata

    Lásd: [csatlakozhat az Azure HDInsight és az Apache Hive-lekérdezések futtatását a Data Lake Tools for Visual Studio használatával](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).

* A Visual Studio kód használata

    Lásd: [a Visual Studio Code az Apache Hive, LLAP vagy pySpark](../hdinsight-for-vscode.md).
* Futtassa az Apache Hive Apache Ambari Hive-nézet használatával.
  
    Lásd: [Apache Hive-nézet használata Azure HDInsight az Apache Hadoop](../hadoop/apache-hadoop-use-hive-ambari-view.md).

* Futtassa az Apache Hive Beeline használatával.
  
    Lásd: [Apache Hive használata a Beeline a HDInsight az Apache Hadoop](../hadoop/apache-hadoop-use-hive-beeline.md).
  
    A Beeline az átjárócsomóponthoz, illetve üres élcsomópontot használhatja. Az üres élcsomópontot Beeline használatát javasoljuk. Egy HDInsight-fürt létrehozása az üres élcsomópontot használatával kapcsolatos információkért lásd: [üres élcsomópontok használata a HDInsight](../hdinsight-apps-use-edge-node.md).
* Futtassa az Apache Hive Hive ODBC segítségével.
  
    Lásd: [Excel csatlakoztatása a Microsoft Hive ODBC-illesztő az Apache Hadoop a](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

A Java adatbázis-kapcsolat (JDBC) kapcsolati karakterlánc megkeresése:

1. Jelentkezzen be az Apache Ambari az alábbi URL-címet: https://\<fürtnév\>. Azurehdinsight.NET formátumban van.
2. A bal oldali menüben válassza ki a **Hive**.
3. Másolja az URL-CÍMÉT, válassza a vágólapra ikont:
   
   ![A HDInsight Hadoop interaktív lekérdezés LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [HDInsight interaktív lekérdezési fürtökhöz létrehozása](../hdinsight-hadoop-provision-linux-clusters.md).
* Ismerje meg, hogyan [megjelenítése a Power bi használatával az Azure HDInsight a big Data típusú adatok](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Ismerje meg, hogyan [Apache a Zeppelin használata Azure HDInsight az Apache Hive-lekérdezések futtatásához](../hdinsight-connect-hive-zeppelin.md).
* Ismerje meg, hogyan [a Data Lake Tools for Visual Studio használatával, az Apache Hive-lekérdezések futtatása](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).
* Ismerje meg, hogyan [HDInsight Tools for Visual Studio Code használata](../hdinsight-for-vscode.md).
* Ismerje meg, hogyan [Apache Hive-nézet használata a HDInsight Apache Hadoop-keretrendszerrel](../hadoop/apache-hadoop-use-hive-ambari-view.md)
* Ismerje meg, hogyan [a Beeline használata a HDInsight Apache Hive-lekérdezések elküldéséhez](../hadoop/apache-hadoop-use-hive-beeline.md).
* Ismerje meg, hogyan [Excel csatlakoztatása a Microsoft Hive ODBC-illesztő az Apache Hadoop](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

