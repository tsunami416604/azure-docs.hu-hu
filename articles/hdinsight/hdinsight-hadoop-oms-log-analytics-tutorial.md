---
title: Használja az Azure Monitor bejelentkezik az Azure HDInsight-fürtök figyelése
description: Ismerje meg, az Azure az Azure Monitor naplóira használata egy HDInsight-fürtön futó feladatok figyelésére.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: hrasheed
ms.openlocfilehash: 399a6bcb6e0bfd0edaddca471ba2c8e0802d3394
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58904780"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Használja az Azure Monitor naplózza a HDInsight-fürtök figyelése

Ismerje meg, a HDInsight Hadoop-fürt műveletek figyelése az Azure Monitor-naplók engedélyezése és a egy HDInsight-figyelési megoldás hozzáadása.

[Az Azure Monitor naplóira](../log-analytics/log-analytics-overview.md) egy szolgáltatás az Azure Monitor, amely figyeli a felhőbeli és helyszíni környezeteket a rendelkezésre állás és teljesítmény fenntartása érdekében. A felhőben és a helyszíni környezetben található erőforrások által létrehozott, valamint egyéb figyelési eszközök által biztosított adatokat gyűjtésével biztosítsa elemzést több forráson.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* **Log Analytics-munkaterület**. Ez a munkaterület egy egyedi az Azure Monitor naplók környezet a saját adattárházzal, adatforrások és megoldások is felfoghatók. Az utasításokért lásd: [hozzon létre egy Log Analytics-munkaterület](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* **Egy Azure HDInsight-fürt**. Jelenleg a következő típusú a HDInsight-fürtöket az Azure Monitor naplóira is használhatja:

  * Hadoop
  * HBase
  * Interaktív lekérdezés
  * Kafka
  * Spark
  * Storm

  Egy HDInsight-fürt létrehozásával kapcsolatos utasításokért lásd: [Azure HDInsight – első lépések](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* **Az Azure PowerShell Az modul**.  Lásd: [az Azure PowerShell-lel Az új modul bevezetése](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

> [!NOTE]  
> Javasoljuk, hogy a HDInsight-fürt és a Log Analytics-munkaterületen helyezze a jobb teljesítmény érdekében ugyanabban a régióban. Az Azure Monitor naplóira nem érhető el az összes Azure-régióban.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Az Azure Monitor-naplók engedélyezésére a portál használatával

Ebben a szakaszban konfigurálja egy meglévő HDInsight Hadoop-fürtöt az Azure Log Analytics-munkaterületet a figyelheti a feladatokat, hibakeresési naplók, stb.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A bal oldali menüben válassza ki a **minden szolgáltatás**.

3. A **ANALYTICS**válassza **HDInsight-fürtök**.

4. A listáról válassza ki a fürtöt.

5. A bal oldali alatt **figyelés**válassza **Operations Management Suite**.

6. A fő nézetből alatt **OMS-Monitorozással**válassza **engedélyezése**.

7. Az a **válasszon ki egy munkaterületet** legördülő listára, válassza ki egy meglévő Log Analytics-munkaterületet.

8. Kattintson a **Mentés** gombra.  A beállítás mentéséhez teljesítése pár percet vesz igénybe.

    ![Engedélyezze a monitorozást az HDInsight-fürtök](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "engedélyezze a monitorozást az HDInsight-fürtök")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Az Azure Monitor-naplók engedélyezésére az Azure PowerShell-lel

Engedélyezheti az Az Azure PowerShell-modullal az Azure Monitor naplóira [engedélyezése – AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightoperationsmanagementsuite) parancsmagot.

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

Használatának letiltása a [Disable-AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightoperationsmanagementsuite) parancsmagot:

```powershell
Disable-AzHDInsightOperationsManagementSuite -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Telepítse a HDInsight-fürt felügyeleti megoldások

HDInsight-fürtre jellemző kezelési megoldásokkal szemben is hozzáadhat az Azure Monitor-naplók biztosít. [Felügyeleti megoldások](../log-analytics/log-analytics-add-solutions.md) funkciók hozzáadása az Azure Monitor naplóira, további adat- és elemzőeszközöket biztosítva. Ezek a megoldások fontos teljesítmény-mérőszámok gyűjtését a HDInsight-fürtök, és adja meg az eszközöket, a metrikák keresése. Ezeket a megoldásokat nyújt a legtöbb fürt esetében támogatja a HDInsight vizualizációkat és irányítópultokat is. A megoldás a gyűjtött metrikák használatával létrehozhat egyéni figyelési szabályokkal és riasztásokkal.

Az elérhető HDInsight-megoldások a következők:

* HDInsight Hadoop-monitorozás
* HDInsight HBase-figyelés
* HDInsight interaktív lekérdezések figyelése
* HDInsight Kafka-figyelés
* HDInsight Spark-figyelés
* HDInsight Storm-monitorozás

Az utasításokat követve telepítse a felügyeleti megoldás, lásd: [felügyeleti megoldások az Azure-ban](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Kísérlet, telepíthet egy HDInsight Hadoop figyelésére szolgáló megoldás. Amikor kész van, megjelenik egy **HDInsightHadoop** alatt felsorolt csempe **összefoglalás**. Válassza ki a **HDInsightHadoop** csempére. A HDInsightHadoop megoldás hasonlóan néz ki:

![HDInsight figyelési megoldás megtekintése](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Mivel a fürt egy teljesen új fürt, a jelentés nem jelenik meg semmilyen tevékenységet.

## <a name="next-steps"></a>További lépések

* [Lekérdezés az Azure Monitor naplózza a HDInsight-fürtök figyelése](hdinsight-hadoop-oms-log-analytics-use-queries.md)
