---
title: Log Analytics segítségével figyelheti az Azure HDInsight-fürtök |} Microsoft Docs
description: 'Útmutató: Azure Naplóelemzés a figyelheti a HDInsight-fürtök a futó feladatok.'
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: e11c9672e2e96f3370c69b33f57a007f7d63ccf2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters"></a>A HDInsight-fürtök figyelése Azure Log Analytics segítségével

Útmutató: Azure Naplóelemzés a figyelheti a HDInsight Hadoop-fürt működését.

[Naplófájl Analytics](../log-analytics/log-analytics-overview.md) egy szolgáltatás, amely figyeli a felhőalapú és helyszíni környezetek karbantartásához azok rendelkezésre állását és teljesítményét. A felhőben és a helyszíni környezetben található erőforrások által létrehozott, valamint egyéb figyelési eszközök által biztosított adatokat gyűjtésével biztosítsa elemzést több forráson. 

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Az oktatóanyag elindításához Azure-előfizetéssel kell rendelkeznie. Lásd: [Ingyenes Azure-fiók létrehozása még ma](https://azure.microsoft.com/free).

* **Egy Azure HDInsight fürt**. Jelenleg a következő HDInsight-fürt típusú Naplóelemzési is használhatja:

    * Hadoop
    * HBase
    * Interaktív lekérdezés
    * Kafka
    * Spark
    * Storm

    A HDInsight-fürtök létrehozásával kapcsolatos utasításokért lásd: [Ismerkedés az Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **A Naplóelemzési munkaterület**. A munkaterület olyan egyedi Naplóelemzési dolgozik, amelyben a saját adattárház, az adatforrások és a megoldások, tulajdonképpen. Rendelkeznie kell egy ilyen munkaterületet hozott létre, amely az Azure HDInsight-fürtök társíthatja. Útmutatásért lásd: [Naplóelemzési munkaterület létrehozása](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace).

## <a name="enable-log-analytics-by-using-the-portal"></a>A Naplóelemzési engedélyezése a portál használatával

Ebben a szakaszban konfigurál egy meglévő HDInsight Hadoop-fürthöz az Azure Log Analytics-munkaterülethez a figyelheti a feladat, a hibakeresési naplókat, stb.

1. Nyissa meg a HDInsight-fürtök az Azure portálon.
2. Kattintson a bal oldali ablaktáblában **figyelés**.
3. Kattintson a jobb oldali ablaktáblában **engedélyezése**, válasszon meglévő Naplóelemzési munkaterületet, és kattintson a **mentése**.

    ![A HDInsight-fürtök figyelésének engedélyezése](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "a HDInsight-fürtök figyelésének engedélyezése")

    A beállítás mentése, néhány percet vesz igénybe.  Ha ezzel végzett, megjelenik egy **nyitott OMS irányítópult** felső gombjára. 

    ![Nyissa meg az Operations Management Suite irányítópult](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-open-workspace.png "nyitott OMS irányítópult")

5. Kattintson a **nyitott OMS irányítópult**.
6. Ha a rendszer kéri, adja meg Azure hitelesítő adatait.

    ![Az Operations Management Suite portálját](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-oms-portal.png "Operations Management Suite portálját")

## <a name="enable-log-analytics-by-using-azure-powershell"></a>A Naplóelemzési engedélyezése az Azure PowerShell használatával

A Naplóelemzési Azure PowerShell használatával engedélyezheti. A parancsmag a következő:

```powershell
Enable-AzureRmHDInsightOperationsManagementSuite
```

See [Enable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/Enable-AzureRmHDInsightOperationsManagementSuite?view=azurermps-5.0.0).

Le szeretné tiltani, a parancsmag van 

```powershell
Disable-AzureRmHDInsightOperationsManagementSuite
```

See [Disable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/disable-azurermhdinsightoperationsmanagementsuite?view=azurermps-5.0.0).


## <a name="next-steps"></a>További lépések
* [HDInsight-fürt felügyeleti megoldások hozzáadni a Naplóelemzési](hdinsight-hadoop-oms-log-analytics-management-solutions.md)
