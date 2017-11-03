---
title: "Log Analytics segítségével figyelheti az Azure HDInsight-fürtök |} Microsoft Docs"
description: "Útmutató: Azure Naplóelemzés a figyelheti a HDInsight-fürtök a futó feladatok."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: nitinme
ms.openlocfilehash: c1c74c797449c2fa6d76438f9ec33eaf0fe51af4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters-preview"></a>Azure Log Analytics segítségével figyelheti a HDInsight-fürtök (előzetes verzió)

Ebből a cikkből megismerheti, hogyan Azure Naplóelemzés a figyelheti a HDInsight Hadoop-fürtök műveletek.

A Naplóelemzési rendszer szolgáltatása [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) , amely figyeli a felhőben és a helyszíni környezetek karbantartásához azok rendelkezésre állását és teljesítményét. A felhőben és a helyszíni környezetben található erőforrások által létrehozott, valamint egyéb figyelési eszközök által biztosított adatokat gyűjtésével biztosítsa elemzést több forráson. 

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Az oktatóanyag elindításához Azure-előfizetéssel kell rendelkeznie. Lásd: [Ingyenes Azure-fiók létrehozása még ma](https://azure.microsoft.com/free).

* **Egy Azure HDInsight fürt**. Jelenleg a következő HDInsight-fürt típusú Azure OMS is használhatja:
    * Hadoop
    * Spark
    * HBase
    * Storm
    * Kafka
    * Interaktív struktúra

    HDInsight-fürtök létrehozásával kapcsolatos utasításokért lásd: [Ismerkedés az Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).


* **A Naplóelemzési munkaterület**. A munkaterület olyan egyedi Naplóelemzési dolgozik, amelyben a saját adattárház, az adatforrások és a megoldások, tulajdonképpen. Rendelkeznie kell egy ilyen munkaterületet hozott létre, amely az Azure HDInsight-fürtök társíthatja. Útmutatásért lásd: [Naplóelemzési munkaterület létrehozása](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace).

## <a name="configure-hdinsight-cluster-to-use-azure-log-analytics"></a>HDInsight-fürt használata az Azure Naplóelemzés konfigurálása

Ebben a szakaszban konfigurál egy meglévő HDInsight Hadoop-fürthöz az Azure Log Analytics-munkaterülethez a figyelheti a feladat, a hibakeresési naplókat, stb.

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **a HDInsight-fürtök**, majd kattintson az Azure Naplóelemzés konfigurálni kívánt fürt nevét.

2. A bal oldali ablaktáblán, a fürt paneljén kattintson **figyelés**.

3. Kattintson a jobb oldali ablaktáblában **engedélyezése**, majd válassza ki egy meglévő Naplóelemzési munkaterület. Kattintson a **Save** (Mentés) gombra.

    ![A HDInsight-fürtök figyelésének engedélyezése](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "a HDInsight-fürtök figyelésének engedélyezése")

4. Miután a fürt figyelés Naplóelemzési használatára van konfigurálva, megjelenik egy **nyitott OMS irányítópult** a lap tetején. Kattintson a gombra.

    ![Nyissa meg OMS irányítópult](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-open-workspace.png "nyitott OMS irányítópult")

5. Ha a rendszer kéri, adja meg Azure hitelesítő adatait. A Microsoft OMS irányítópult van irányítva.

    ![Az Operations Management Suite portálját](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-oms-portal.png "Operations Management Suite portálját")

## <a name="next-steps"></a>Következő lépések
* [HDInsight-fürt felügyeleti megoldások hozzáadni a Naplóelemzési](hdinsight-hadoop-oms-log-analytics-management-solutions.md)
