---
title: Interaktív lekérdezés használata az Azure HDInsight |} Microsoft Docs
description: Megtudhatja, hogyan interaktív lekérdezés (Hive LLAP) használata a hdinsight eszközzel.
keywords: ''
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 0957643c-4936-48a3-84a3-5dc83db4ab1a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: 9450f60b88c63e2cd818130521d67daf435f0b17
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31407105"
---
# <a name="use-interactive-query-with-hdinsight"></a>Interaktív lekérdezés használata a hdinsight eszközzel
Interaktív lekérdezés (más néven a Hive LLAP vagy [hosszú Live és a folyamat](https://cwiki.apache.org/confluence/display/Hive/LLAP)) van egy Azure HDInsight [típusú fürt](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types). Interaktív lekérdezés támogatja a memórián belüli gyorsítótárazáshoz, ami lehetővé teszi, hogy gyorsabb és sokkal több interaktív Hive-lekérdezéseket.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)] 

Az interaktív lekérdezés fürt abban különbözik a Hadoop-fürthöz. Csak a Hive szolgáltatást tartalmaz. 

> [!NOTE]
> A Hive fürtszolgáltatás interaktív lekérdezés csak az Ambari Hive View Beeline és a Microsoft Hive ODBC-illesztőprogram (Hive ODBC) keresztül érheti el. Nem érhető el, a Hive-konzol, lépni a Templeton, az Azure parancssori eszköz (Azure CLI) vagy az Azure PowerShell használatával. 
> 
> 

## <a name="create-an-interactive-query-cluster"></a>Az interaktív lekérdezés fürt létrehozása
A HDInsight-fürt létrehozásával kapcsolatos további információkért lásd: [Hadoop létrehozása a HDInsight-fürtök](../hdinsight-hadoop-provision-linux-clusters.md). Válassza ki a interaktív lekérdezés fürt.

## <a name="execute-hive-queries-from-interactive-query"></a>Interaktív lekérdezés Hive-lekérdezéseket hajt végre
Hive-lekérdezések végrehajtásához a következő lehetőségei vannak:

* Használja a Power bi-ban

    Lásd: [adatok interaktív Hive lekérdezés megjelenítheti a Power BI Azure hdinsight](./apache-hadoop-connect-hive-power-bi-directquery.md) lásd [big-adatok ábrázolása a Power bi-ban az Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
 
* A Zeppelin használata

    Lásd: [Azure hdinsight Hive-lekérdezések futtatásához használja Zeppelin ](../hdinsight-connect-hive-zeppelin.md).

* A Visual Studio használata

    Lásd: [csatlakozás az Azure HDInsight és Hive-lekérdezések futtatása a Data Lake Tools for Visual Studio használatával](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-hive-queries).

* A Visual Studio Code használata

    Lásd: [használja a Visual Studio Code a Hive, LLAP vagy pySpark](../hdinsight-for-vscode.md).
* Futtassa a Hive Ambari Hive nézet használatával.
  
    Lásd: [használni az Azure HDInsight Hadoop Hive nézete](../hadoop/apache-hadoop-use-hive-ambari-view.md).
* Futtassa a Hive Beeline használatával.
  
    Lásd: [a Hive használata a hadooppal a Hdinsightban az Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
  
    Az átjárócsomópont vagy egy üres élcsomópontot Beeline használható. Az egy üres élcsomópontot Beeline használatát javasoljuk. HDInsight-fürtök létrehozása egy üres élcsomópontot használatával kapcsolatos információkért lásd: [üres peremhálózati csomópontok használata a Hdinsightban](../hdinsight-apps-use-edge-node.md).
* Futtassa a Hive Hive ODBC használatával.
  
    Lásd: [kapcsolódás Excel és a Microsoft Hive ODBC-illesztőprogram Hadoop](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

A Java adatbázis kapcsolat (JDBC) kapcsolati karakterlánc megkeresése:

1. Ambari jelentkezzen be a következő URL-cím segítségével: https://\<fürtnév\>. AzureHDInsight.net.
2. A bal oldali menüben válassza ki a **Hive**.
3. Az URL-cím másolásához jelölje ki a vágólap ikon:
   
   ![HDInsight Hadoop interaktív lekérdezés LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan [interaktív lekérdezés fürtök létrehozása a Hdinsightban](../hdinsight-hadoop-provision-linux-clusters.md).
* Megtudhatja, hogyan [big-adatok ábrázolása a Power bi-ban az Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Megtudhatja, hogyan [Zeppelin segítségével az Azure HDInsight Hive-lekérdezések futtatása ](../hdinsight-connect-hive-zeppelin.md).
* Megtudhatja, hogyan [futtathat Hive-lekérdezéseket a Data Lake Tools for Visual Studio használatával](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-hive-queries).
* Megtudhatja, hogyan [használhat HDInsight eszközöket a Visual Studio Code](../hdinsight-for-vscode.md).
* Megtudhatja, hogyan [nézet Hive használata a hadooppal a Hdinsightban](../hadoop/apache-hadoop-use-hive-ambari-view.md)
* Megtudhatja, hogyan [Beeline elküldeni a hdinsight Hive-lekérdezések használata](../hadoop/apache-hadoop-use-hive-beeline.md).
* Megtudhatja, hogyan [és a Hadoop Excel csatlakoztatása a Microsoft Hive ODBC-illesztőprogram](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

