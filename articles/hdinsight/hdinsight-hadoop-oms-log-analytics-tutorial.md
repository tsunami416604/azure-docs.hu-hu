---
title: Az Azure HDInsight-fürtök monitorozása a Log Analytics használatával
description: Ismerje meg, hogyan használható az Azure Log Analytics egy HDInsight-fürtön futó feladatok figyelése.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: jasonh
ms.openlocfilehash: 87d1593327ab45f064c6f6b19dfbe3882baaa446
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43109516"
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters"></a>HDInsight-fürtök figyelése az Azure Log Analytics használatával

Ismerje meg az Azure Log Analytics figyelése a HDInsight Hadoop-fürt műveletek engedélyezése, és hogyan adhat hozzá egy figyelési megoldás futtató HDInisght.

[Log Analytics](../log-analytics/log-analytics-overview.md) egy szolgáltatás, amely figyeli a felhőbeli és helyszíni környezeteket a rendelkezésre állás és teljesítmény fenntartása érdekében. A felhőben és a helyszíni környezetben található erőforrások által létrehozott, valamint egyéb figyelési eszközök által biztosított adatokat gyűjtésével biztosítsa elemzést több forráson.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* **Log Analytics-munkaterület**. Ez a munkaterület egy saját adattárházzal, adatforrások és megoldások egyedi Log Analytics-környezetet is felfoghatók. Az utasításokért lásd: [hozzon létre egy Log Analytics-munkaterület](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace).

* **Egy Azure HDInsight-fürt**. Jelenleg a következő HDInsight-fürttípusok a Log Analytics is használhatja:

  * Hadoop
  * HBase
  * Interaktív lekérdezés
  * Kafka
  * Spark
  * Storm

  Egy HDInsight-fürt létrehozásával kapcsolatos utasításokért lásd: [Azure HDInsight – első lépések](hadoop/apache-hadoop-linux-tutorial-get-started.md).

> [!NOTE]
> Javasoljuk, hogy a HDInsight-fürt és a Log Analytics-munkaterületen helyezze a jobb teljesítmény érdekében ugyanabban a régióban. Megjegyzés: az Azure Log Analytics az összes Azure-régióban nem érhető el.

## <a name="enable-log-analytics-by-using-the-portal"></a>A Log Analytics engedélyezése a portál használatával

Ebben a szakaszban konfigurálja egy meglévő HDInsight Hadoop-fürtöt az Azure Log Analytics-munkaterületet a figyelheti a feladatokat, hibakeresési naplók, stb.

1. Nyisson meg egy HDInsight-fürtön az Azure Portalon.
2. A bal oldali panelen válassza ki a **figyelés**.
3. A jobb oldali ablaktáblán válassza ki a **engedélyezése**, egy meglévő Log Analytics-munkaterületet, majd válassza ki és **mentése**.

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

Az utasításokat követve telepítse a felügyeleti megoldás, lásd: [felügyeleti megoldások az Azure-ban](../monitoring/monitoring-solutions.md#install-a-management-solution). Kísérlet, telepíthet egy HDInsight Hadoop Monotiring megoldás. Amikor kész van, megjelenik egy **HDInsightHadoop** alatt felsorolt csempe **összefoglalás**. Válassza ki a **HDInsightHadoop** csempére. A HDInsightHadoop megoldás hasonlóan néz ki:

![HDInsight OMS Hadoop-monitorozás megoldás megtekintése](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Mivel a fürt egy teljesen új fürt, a jelentés nem jelenik meg semmilyen tevékenységet.

## <a name="next-steps"></a>További lépések

* [A HDInsight-fürtök figyelése az Azure Log Analytics lekérdezési](hdinsight-hadoop-oms-log-analytics-use-queries.md)
