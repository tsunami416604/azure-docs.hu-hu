---
title: Az Azure Monitor naplóinak használata az Azure HDInsight-fürtök figyeléséhez
description: Ismerje meg, hogyan használhatja az Azure Monitor naplók hdinsight-fürtben futó feladatok figyelésére.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: e4b33e132e660fba7d06ff33c7db06c7727dd26c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162786"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Azure Monitor-naplók használata HDInsight-fürtök monitorozásához

Megtudhatja, hogyan engedélyezheti az Azure Monitor-naplók at a Hadoop-fürt műveleteinek felügyeletére a HDInsightban, és hogyan adhat hozzá HDInsight figyelési megoldást.

[Az Azure Monitor naplói](../log-analytics/log-analytics-overview.md) egy olyan szolgáltatás az Azure Monitorban, amely figyeli a felhőbeli és a helyszíni környezeteket, hogy fenntartsák azok rendelkezésre állását és teljesítményét. A felhőben és a helyszíni környezetben található erőforrások által létrehozott, valamint egyéb figyelési eszközök által biztosított adatokat gyűjtésével biztosítsa elemzést több forráson.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

* Egy Log Analytics-munkaterület. Ezt a munkaterületet úgy is felhasználhatja, mint egy egyedi Azure Monitor-naplózó környezetet saját adattárral, adatforrásokkal és megoldásokkal. Az utasításokat a [Log Analytics-munkaterület létrehozása című témakörben találja.](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace)

* Egy Azure-beli HDInsight-fürt. Jelenleg az Azure Monitor naplóit a következő HDInsight-fürttípusokkal használhatja:

  * Hadoop
  * HBase
  * Interaktív lekérdezés
  * Kafka
  * Spark
  * Storm

  A HDInsight-fürtök létrehozásáról az Azure HDInsight – Első lépések című [témakörben talál.](hadoop/apache-hadoop-linux-tutorial-get-started.md)  

* Azure PowerShell Az modul.  Lásd: [Az új Azure PowerShell Az modul bemutatása.](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) Győződjön meg arról, hogy a legújabb verzióval rendelkezik. Ha szükséges, `Update-Module -Name Az`futtassa a futtassa a futtassa a

> [!NOTE]  
> A jobb teljesítmény érdekében ajánlott a HDInsight-fürt és a Log Analytics-munkaterület ugyanabban a régióban elhelyezni. Az Azure Monitor naplói nem érhetők el minden Azure-régióban.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Az Azure Monitor-naplók engedélyezése a portál használatával

Ebben a szakaszban egy meglévő HDInsight-Hadoop-fürtöt konfigurál, hogy egy Azure Log Analytics-munkaterületet használjon a feladatok, a hibakeresési naplók stb.

1. Az [Azure Portalon](https://portal.azure.com/)válassza ki a fürtöt.  Az utasításokat a [Fürtök listája és megjelenítése](./hdinsight-administer-use-portal-linux.md#showClusters) című témakörben találja. A fürt egy új portállapon nyílik meg.

1. A bal oldalon a **Figyelés**csoportban válassza az **Azure Monitor**lehetőséget.

1. A fő nézetben az **Azure Monitor-integráció**csoportban válassza az **Engedélyezés lehetőséget.**

1. Válasszon **munkaterületlegördülő** listából egy meglévő Log Analytics-munkaterületet.

1. Kattintson a **Mentés** gombra.  A beállítás mentése néhány percet vesz igénybe.

    ![HDInsight-fürtök figyelésének engedélyezése](./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png "HDInsight-fürtök figyelésének engedélyezése")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Az Azure Monitor-naplók engedélyezése az Azure PowerShell használatával

Az Azure Monitor-naplók az Azure PowerShell Az modul [Enable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightmonitoring) parancsmag használatával engedélyezheti az Azure Monitor-naplókat.

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster
```

A letiltáshoz használja az [Disable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) parancsmast:

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>HDInsight fürtkezelési megoldások telepítése

A HDInsight fürtspecifikus felügyeleti megoldásokat kínál, amelyeket hozzáadhat az Azure Monitor-naplókhoz. [A felügyeleti megoldások](../log-analytics/log-analytics-add-solutions.md) funkciókat biztosítanak az Azure Monitor naplóihoz, további adatokat és elemzési eszközöket biztosítva. Ezek a megoldások fontos teljesítménymutatókat gyűjtenek a HDInsight-fürtökből, és biztosítják a metrikák kereséséhez szükséges eszközöket. Ezek a megoldások vizualizációkat és irányítópultokat is biztosítanak a HDInsight által támogatott legtöbb fürttípushoz. A megoldással gyűjtött mérőszámok használatával egyéni figyelési szabályokat és riasztásokat hozhat létre.

Ezek a rendelkezésre álló HDInsight megoldások:

* HDInsight Hadoop figyelése
* HDInsight HBase figyelés
* HDInsight interaktív lekérdezésfigyelés
* HDInsight Kafka figyelés
* HDInsight Spark figyelése
* HDInsight viharfigyelés

A felügyeleti megoldás telepítésével kapcsolatos utasításokat az [Azure felügyeleti megoldásai című témakörben találja.](../azure-monitor/insights/solutions.md#install-a-monitoring-solution) A kísérletezéshez telepítsen egy HDInsight Hadoop figyelési megoldást. Ha ez megtörtént, megjelenik egy **HDInsightHadoop** csempe az **Összegzés listában.** Válassza ki a **HDInsightHadoop** csempét. A HDInsightHadoop megoldás így néz ki:

![HDInsight figyelési megoldás nézet](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Mivel a fürt egy teljesen új fürt, a jelentés nem jelenít meg semmilyen tevékenységet.

## <a name="configuring-performance-counters"></a>Teljesítményszámlálók konfigurálása

Az Azure Monitor is támogatja a fürt csomópontjainak teljesítménymetrikák gyűjtését és elemzését. A funkció engedélyezéséről és konfigurálásáról további információt a [Linux teljesítményadatforrásai az Azure Monitorban](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#linux-performance-counters)talál.

## <a name="cluster-auditing"></a>Fürt naplózása

A HDInsight a következő típusú naplók importálásával támogatja a fürtnaplózást az Azure Monitor-naplókkal:

* `log_gateway_audit_CL`- ez a tábla naplót biztosít a fürtátjáró-csomópontokból, amelyek sikeres és sikertelen bejelentkezési kísérleteket mutatnak.
* `log_auth_CL`- ez a táblázat sikeres és sikertelen bejelentkezési kísérleteket biztosít az SSH-naplók számára.
* `log_ambari_audit_CL`- ez a táblázat az Ambari naplóit tartalmazza.
* `log_ranger_audti_CL`- ez a tábla az Apache Ranger esp-fürtökön lévő naplóit tartalmazza.

## <a name="next-steps"></a>További lépések

* [Az Azure Monitor naplóinak lekérdezése a HDInsight-fürtök figyeléséhez](hdinsight-hadoop-oms-log-analytics-use-queries.md)
