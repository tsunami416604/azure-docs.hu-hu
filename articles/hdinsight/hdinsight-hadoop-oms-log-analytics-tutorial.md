---
title: Az Azure HDInsight-fürtök monitorozása a Log Analytics használatával
description: Ismerje meg, hogyan használható az Azure Log Analytics egy HDInsight-fürtön futó feladatok figyelése.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: hrasheed
ms.openlocfilehash: cd129ea68315223516ac1cd3e7577b5ee4bf92e5
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005114"
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters"></a>HDInsight-fürtök figyelése az Azure Log Analytics használatával

Ismerje meg az Azure Log Analytics figyelése a HDInsight Hadoop-fürt műveletek engedélyezése, és hogyan adhat hozzá egy figyelési megoldás futtató HDInisght.

[Log Analytics](../log-analytics/log-analytics-overview.md) egy szolgáltatás az Azure Monitor, amely figyeli a felhőbeli és helyszíni környezeteket a rendelkezésre állás és teljesítmény fenntartása érdekében. A felhőben és a helyszíni környezetben található erőforrások által létrehozott, valamint egyéb figyelési eszközök által biztosított adatokat gyűjtésével biztosítsa elemzést több forráson.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* **Log Analytics-munkaterület**. Ez a munkaterület egy saját adattárházzal, adatforrások és megoldások egyedi Log Analytics-környezetet is felfoghatók. Az utasításokért lásd: [hozzon létre egy Log Analytics-munkaterület](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* **Egy Azure HDInsight-fürt**. Jelenleg a következő HDInsight-fürttípusok a Log Analytics is használhatja:

  * Hadoop
  * HBase
  * Interaktív lekérdezés
  * Kafka
  * Spark
  * Storm

  Egy HDInsight-fürt létrehozásával kapcsolatos utasításokért lásd: [Azure HDInsight – első lépések](hadoop/apache-hadoop-linux-tutorial-get-started.md).

> [!NOTE]  
> Javasoljuk, hogy a HDInsight-fürt és a Log Analytics-munkaterületen helyezze a jobb teljesítmény érdekében ugyanabban a régióban. Az Azure Log Analytics az összes Azure-régióban nem érhető el.

## <a name="enable-log-analytics-by-using-the-portal"></a>A Log Analytics engedélyezése a portál használatával

Ebben a szakaszban konfigurálja egy meglévő HDInsight Hadoop-fürtöt az Azure Log Analytics-munkaterületet a figyelheti a feladatokat, hibakeresési naplók, stb.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A bal oldali menüben válassza ki a **minden szolgáltatás**.

3. A **ANALYTICS**válassza **HDInsight-fürtök**.

4. A bal oldali alatt **figyelés**válassza **Operations Management Suite**.

5. A fő nézetből alatt **OMS-Monitorozással**válassza **engedélyezése**.

6. Az a **válasszon ki egy munkaterületet** legördülő listára, válassza ki egy meglévő Log Analytics-munkaterületet.

7. Kattintson a **Mentés** gombra.

    ![Engedélyezze a monitorozást az HDInsight-fürtök](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "engedélyezze a monitorozást az HDInsight-fürtök")

    A beállítás mentéséhez teljesítése pár percet vesz igénybe.

## <a name="enable-log-analytics-by-using-azure-powershell"></a>A Log Analytics engedélyezése az Azure PowerShell-lel

A Log Analytics az Azure PowerShell használatával engedélyezheti. A parancsmag a következő:

```powershell
Enable-AzureRmHDInsightOperationsManagementSuite
      [-Name] <CLUSTER NAME>
      [-WorkspaceId] <LOG ANALYTICS WORKSPACE NAME>
      [-PrimaryKey] <LOG ANALYTICS WORKSPACE PRIMARY KEY>
      [-ResourceGroupName] <RESOURCE GROUIP NAME>
```

See [Enable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/Enable-AzureRmHDInsightOperationsManagementSuite?view=azurermps-5.0.0).

Letilthatja, a parancsmag a következő:

```powershell
Disable-AzureRmHDInsightOperationsManagementSuite
       [-Name] <CLUSTER NAME>
       [-ResourceGroupName] <RESOURCE GROUP NAME>
```

See [Disable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/disable-azurermhdinsightoperationsmanagementsuite?view=azurermps-5.0.0).

## <a name="install-hdinsight-cluster-management-solutions"></a>Telepítse a HDInsight-fürt felügyeleti megoldások

HDInsight-fürtre jellemző kezelési megoldásokkal szemben is hozzáadhat az Azure Log Analytics biztosít. [Felügyeleti megoldások](../log-analytics/log-analytics-add-solutions.md) további funkciókkal bővítik a Log Analytics, további adat- és elemzőeszközöket biztosítva. Ezek a megoldások fontos teljesítmény-mérőszámok gyűjtését a HDInsight-fürtök, és adja meg az eszközöket, a metrikák keresése. Ezeket a megoldásokat nyújt a legtöbb fürt esetében támogatja a HDInsight vizualizációkat és irányítópultokat is. A megoldás a gyűjtött metrikák használatával létrehozhat egyéni figyelési szabályokkal és riasztásokkal.

Az elérhető HDInsight-megoldások a következők:

* HDInsight Hadoop-monitorozás
* HDInsight HBase-figyelés
* HDInsight interaktív lekérdezések figyelése
* HDInsight Kafka-figyelés
* HDInsight Spark-figyelés
* HDInsight Storm-monitorozás

Az utasításokat követve telepítse a felügyeleti megoldás, lásd: [felügyeleti megoldások az Azure-ban](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Kísérlet, telepíthet egy HDInsight Hadoop Monotiring megoldás. Amikor kész van, megjelenik egy **HDInsightHadoop** alatt felsorolt csempe **összefoglalás**. Válassza ki a **HDInsightHadoop** csempére. A HDInsightHadoop megoldás hasonlóan néz ki:

![HDInsight figyelési megoldás megtekintése](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Mivel a fürt egy teljesen új fürt, a jelentés nem jelenik meg semmilyen tevékenységet.

## <a name="next-steps"></a>További lépések

* [A HDInsight-fürtök figyelése az Azure Log Analytics lekérdezési](hdinsight-hadoop-oms-log-analytics-use-queries.md)
