---
title: Azure HDInsight-fürtök figyelése Azure Monitor naplók használatával
description: Megtudhatja, hogyan használhatók Azure Monitor naplók a HDInsight-fürtön futó feladatok figyeléséhez.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 7d015f485a51ae1f929e2ecaf1a05811d21594a2
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816031"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>HDInsight-fürtök figyelése Azure Monitor naplók használatával

Megtudhatja, hogyan engedélyezheti Azure Monitor naplókat a Hadoop figyeléséhez a HDInsight-ben, és hogyan adhat hozzá HDInsight-figyelési megoldást.

A [Azure monitor naplók](../log-analytics/log-analytics-overview.md) szolgáltatás a Azure monitorban, amely figyeli a Felhőbeli és a helyszíni környezeteket a rendelkezésre állás és a teljesítmény fenntartása érdekében. A felhőben és a helyszíni környezetben található erőforrások által létrehozott, valamint egyéb figyelési eszközök által biztosított adatokat gyűjtésével biztosítsa elemzést több forráson.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* **Log Analytics-munkaterület**. Ezt a munkaterületet a saját adattárházával, adatforrásaival és megoldásaival kapcsolatos egyedi Azure Monitor-naplózási környezetnek tekintheti át. Az utasításokért lásd: [hozzon létre egy Log Analytics-munkaterület](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* **Egy Azure HDInsight-fürt**. Jelenleg a következő HDInsight-fürtökkel rendelkező Azure Monitor naplókat használhatja:

  * Hadoop
  * HBase
  * Interaktív lekérdezés
  * Kafka
  * Spark
  * Storm

  Egy HDInsight-fürt létrehozásával kapcsolatos utasításokért lásd: [Azure HDInsight – első lépések](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* **Azure PowerShell az modul**.  Lásd: [az új Azure PowerShell bemutatása az Module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

> [!NOTE]  
> Javasoljuk, hogy a HDInsight-fürt és a Log Analytics-munkaterületen helyezze a jobb teljesítmény érdekében ugyanabban a régióban. Azure Monitor naplók nem érhetők el az összes Azure-régióban.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Azure Monitor naplók engedélyezése a portál használatával

Ebben a szakaszban konfigurálja egy meglévő HDInsight Hadoop-fürtöt az Azure Log Analytics-munkaterületet a figyelheti a feladatokat, hibakeresési naplók, stb.

1. A [Azure Portal](https://portal.azure.com/)válassza ki a fürtöt.  Lásd: [fürtök listázása és megjelenítése](./hdinsight-administer-use-portal-linux.md#showClusters) az utasításokhoz. A fürt megnyílik egy új portál oldalon.

1. A bal oldalon a **figyelés**területen válassza az **Operations Management Suite**elemet.

1. A fő nézet **OMS figyelés**területén válassza az **Engedélyezés**lehetőséget.

1. A **munkaterület kiválasztása** legördülő listából válasszon ki egy meglévő log Analytics munkaterületet.

1. Kattintson a **Mentés** gombra.  A beállítás mentéséhez teljesítése pár percet vesz igénybe.

    ![Engedélyezze a monitorozást az HDInsight-fürtök](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "engedélyezze a monitorozást az HDInsight-fürtök")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Azure Monitor naplók engedélyezése Azure PowerShell használatával

Azure Monitor naplókat az Azure PowerShell az Module [enable-AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightoperationsmanagementsuite) parancsmag használatával engedélyezheti.

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace -ResourceGroupName $resourceGroup -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace -ResourceGroupName $resourceGroup -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables Operations Management Suite
Enable-AzHDInsightOperationsManagementSuite -ResourceGroupName $resourceGroup -Name $cluster -WorkspaceId $WorkspaceId -PrimaryKey $PrimaryKey
```

A letiltásához használja a [disable-AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightoperationsmanagementsuite) parancsmagot:

```powershell
Disable-AzHDInsightOperationsManagementSuite -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Telepítse a HDInsight-fürt felügyeleti megoldások

A HDInsight olyan fürtözött felügyeleti megoldásokat biztosít, amelyeket hozzáadhat Azure Monitor naplókhoz. A [felügyeleti megoldások](../log-analytics/log-analytics-add-solutions.md) funkciókkal bővítik Azure monitor naplókat, amelyek további adat-és elemzési eszközöket biztosítanak. Ezek a megoldások fontos teljesítmény-mérőszámok gyűjtését a HDInsight-fürtök, és adja meg az eszközöket, a metrikák keresése. Ezeket a megoldásokat nyújt a legtöbb fürt esetében támogatja a HDInsight vizualizációkat és irányítópultokat is. A megoldás a gyűjtött metrikák használatával létrehozhat egyéni figyelési szabályokkal és riasztásokkal.

Az elérhető HDInsight-megoldások a következők:

* HDInsight Hadoop-monitorozás
* HDInsight HBase-figyelés
* HDInsight interaktív lekérdezések figyelése
* HDInsight Kafka-figyelés
* HDInsight Spark-figyelés
* HDInsight Storm-monitorozás

Az utasításokat követve telepítse a felügyeleti megoldás, lásd: [felügyeleti megoldások az Azure-ban](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). A kísérlethez telepítsen egy HDInsight Hadoop-figyelési megoldást. Amikor kész van, megjelenik egy **HDInsightHadoop** alatt felsorolt csempe **összefoglalás**. Válassza ki a **HDInsightHadoop** csempére. A HDInsightHadoop megoldás hasonlóan néz ki:

![HDInsight figyelési megoldás megtekintése](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Mivel a fürt egy teljesen új fürt, a jelentés nem jelenik meg semmilyen tevékenységet.

## <a name="configuring-performance-counters"></a>Teljesítményszámlálók konfigurálása

Az Azure monitor a fürt csomópontjaihoz tartozó teljesítménymutatók összegyűjtését és elemzését is támogatja. A szolgáltatás engedélyezésével és konfigurálásával kapcsolatos további információkért tekintse meg a [Linux teljesítmény adatforrásait Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#linux-performance-counters).

## <a name="next-steps"></a>További lépések

* [Azure Monitor naplók lekérdezése HDInsight-fürtök figyeléséhez](hdinsight-hadoop-oms-log-analytics-use-queries.md)
