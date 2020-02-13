---
title: Azure HDInsight-fürtök figyelése Azure Monitor naplók használatával
description: Megtudhatja, hogyan használhatók Azure Monitor naplók a HDInsight-fürtön futó feladatok figyeléséhez.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: e4b33e132e660fba7d06ff33c7db06c7727dd26c
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162786"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>HDInsight-fürtök figyelése Azure Monitor naplók használatával

Megtudhatja, hogyan engedélyezheti Azure Monitor naplókat a Hadoop figyeléséhez a HDInsight-ben, és hogyan adhat hozzá HDInsight-figyelési megoldást.

A [Azure monitor naplók](../log-analytics/log-analytics-overview.md) szolgáltatás a Azure monitorban, amely figyeli a Felhőbeli és a helyszíni környezeteket a rendelkezésre állás és a teljesítmény fenntartása érdekében. A felhőben és a helyszíni környezetben található erőforrások által létrehozott, valamint egyéb figyelési eszközök által biztosított adatokat gyűjtésével biztosítsa elemzést több forráson.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Egy Log Analytics-munkaterület. Ezt a munkaterületet a saját adattárházával, adatforrásaival és megoldásaival kapcsolatos egyedi Azure Monitor-naplózási környezetnek tekintheti át. Az utasításokért lásd: [log Analytics munkaterület létrehozása](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* Egy Azure-beli HDInsight-fürt. Jelenleg a következő HDInsight-fürtökkel rendelkező Azure Monitor naplókat használhatja:

  * Hadoop
  * HBase
  * Interaktív lekérdezés
  * Kafka
  * Spark
  * Storm

  A HDInsight-fürtök létrehozásával kapcsolatos útmutatásért lásd: Ismerkedés [Az Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* Azure PowerShell az modul.  Lásd: [az új Azure PowerShell bemutatása az Module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az). Győződjön meg arról, hogy a legújabb verzióval rendelkezik. Ha szükséges, futtassa `Update-Module -Name Az`.

> [!NOTE]  
> Javasoljuk, hogy a HDInsight-fürt és a Log Analytics-munkaterületen helyezze a jobb teljesítmény érdekében ugyanabban a régióban. Azure Monitor naplók nem érhetők el az összes Azure-régióban.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Azure Monitor naplók engedélyezése a portál használatával

Ebben a szakaszban konfigurálja egy meglévő HDInsight Hadoop-fürtöt az Azure Log Analytics-munkaterületet a figyelheti a feladatokat, hibakeresési naplók, stb.

1. A [Azure Portal](https://portal.azure.com/)válassza ki a fürtöt.  Lásd: [fürtök listázása és megjelenítése](./hdinsight-administer-use-portal-linux.md#showClusters) az utasításokhoz. A fürt megnyílik egy új portál oldalon.

1. A bal oldalon a **figyelés**területen válassza a **Azure monitor**lehetőséget.

1. A fő nézet **Azure monitor integráció**területén válassza az **Engedélyezés**lehetőséget.

1. A **munkaterület kiválasztása** legördülő listából válasszon ki egy meglévő log Analytics munkaterületet.

1. Kattintson a **Mentés** gombra.  A beállítás mentéséhez teljesítése pár percet vesz igénybe.

    ![HDInsight-fürtök figyelésének engedélyezése](./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png "HDInsight-fürtök figyelésének engedélyezése")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Azure Monitor naplók engedélyezése Azure PowerShell használatával

Azure Monitor naplókat az Azure PowerShell az Module [enable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightmonitoring) parancsmag használatával engedélyezheti.

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

A letiltásához használja a [disable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) parancsmagot:

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
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

A felügyeleti megoldás telepítésével kapcsolatos utasításokért lásd: [felügyeleti megoldások az Azure-ban](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). A kísérlethez telepítsen egy HDInsight Hadoop-figyelési megoldást. Ha elkészült, megjelenik egy **HDInsightHadoop** csempe az **Összefoglalás**területen. Válassza a **HDInsightHadoop** csempét. A HDInsightHadoop megoldás hasonlóan néz ki:

![HDInsight figyelési megoldás megtekintése](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Mivel a fürt egy teljesen új fürt, a jelentés nem jelenik meg semmilyen tevékenységet.

## <a name="configuring-performance-counters"></a>Teljesítményszámlálók konfigurálása

Az Azure monitor a fürt csomópontjaihoz tartozó teljesítménymutatók összegyűjtését és elemzését is támogatja. A szolgáltatás engedélyezésével és konfigurálásával kapcsolatos további információkért tekintse meg a [Linux teljesítmény adatforrásait Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#linux-performance-counters).

## <a name="cluster-auditing"></a>Fürt naplózása

A HDInsight a következő típusú naplók importálásával támogatja a fürt naplózását Azure Monitor naplókkal:

* `log_gateway_audit_CL` – ez a táblázat a sikeres és sikertelen bejelentkezési kísérleteket bemutató fürtcsomópont-csomópontok naplóit tartalmazza.
* `log_auth_CL` – ez a táblázat a sikeres és sikertelen bejelentkezési kísérletekkel rendelkező SSH-naplókat tartalmaz.
* `log_ambari_audit_CL` – ez a tábla naplókat biztosít a Ambari.
* `log_ranger_audti_CL` – ez a táblázat az Apache Rangerből származó, ESP-fürtökön található naplókat tartalmaz.

## <a name="next-steps"></a>Következő lépések

* [Azure Monitor naplók lekérdezése HDInsight-fürtök figyeléséhez](hdinsight-hadoop-oms-log-analytics-use-queries.md)
