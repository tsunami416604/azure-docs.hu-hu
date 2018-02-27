---
title: "A HDInsight fürt megoldások hozzáadása az Azure Naplóelemzés szolgáltatáshoz |} Microsoft Docs"
description: "Útmutató: Azure Log Analytics segítségével egyéni nézeteket a HDInsight-fürtök létrehozása."
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
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: f8a073fbf40feaef97053bb1165d63bbca3e3feb
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="add-hdinsight-cluster-management-solutions-to-log-analytics"></a>HDInsight-fürt felügyeleti megoldások hozzáadni a Naplóelemzési

HDInsight fürt-specifikus felügyeleti megoldásokat az Azure Naplóelemzés adhat hozzá. [Megoldások](../log-analytics/log-analytics-add-solutions.md) hozzá a funkciót a [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md), további adatok és elemzésére szolgáló eszközöket biztosít a szolgáltatáshoz. Ezek a megoldások fontos teljesítménybeli a HDInsight-fürtök adatainak begyűjtése, és adja meg az eszközök a metrikák kereséséhez. Ezek a megoldások képi megjelenítések és irányítópultok is a Hdinsightban támogatott a legtöbb fürt típusok biztosítják. A metrikák gyűjtött megoldás segítségével létrehozhat egyéni ellenőrzési szabályok és értesítések. 

Ebből a cikkből megtanulhatja a fürtökkel kapcsolatos megoldások hozzáadása az Operations Management Suite-munkaterülethez.

## <a name="prerequisites"></a>Előfeltételek

* Konfigurálnia kell egy HDInsight-fürt használata az Azure Naplóelemzés. Útmutatásért lásd: [használata Azure Naplóelemzés HDInsight-fürtökkel](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="add-cluster-specific-management-solutions"></a>Fürt-specifikus megoldások hozzáadása

Ebben a szakaszban hozzáadja egy HBase-fürt felügyeleti megoldás egy meglévő Operations Management Suite-munkaterülettel.

1. Nyissa meg az Azure-portálon HDInsigt fürtöt kattintson **figyelés**, és kattintson a **nyitott OMS irányítópult**.

    ![Nyissa meg az Operations Management Suite irányítópult](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-log-analytics-open-oms-dashboard.png "nyitott OMS irányítópult")

1. Az OMS-irányítópulton kattintson **megoldások gyűjtemény** vagy a **adatforrásnézet-tervezőből** ikonra a bal oldali ablaktáblán.

    ![Adja hozzá a felügyeleti megoldás az Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-add-management-solution-oms-portal.png "adja hozzá a felügyeleti megoldás az Operations Management Suite szolgáltatásban")

2. Az megoldások oldalon kattintson a következő csempék találhatók:

    - HDInsight Hadoop figyelése
    - HDInsight HBase figyelése (előzetes verzió)
    - HDInsight Kafka Monitoring
    - A HDInsight alatt futó Storm figyelése
    - A HDInsight Spark figyelése

3. A következő képernyőn kattintson **Hozzáadás**.  Az alábbi képernyőfelvételen látható a Hozzáadás gombra a HBase-figyeléshez.

     ![Adja hozzá a HBase-kezelési megoldás](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/add-hbase-management-solution.png "adja hozzá a HBase-kezelési megoldás")

4. Egy csempe a HBase-kezelési megoldás az OMS-irányítópulton látható. Ha a fürt (Ez a cikk előfeltételeivel részeként) Operations Management Suite társított HBase-fürtöt, a csempe a fürt nevét, valamint a fürt a csomópontok számát mutatja.

    ![A HBase-kezelési megoldás hozzáadott](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/added-hbase-management-solution.png "hozzáadott HBase-kezelési megoldás")

## <a name="next-steps"></a>További lépések

* [A HDInsight-fürtök figyelése az Azure Naplóelemzés lekérdezése](hdinsight-hadoop-oms-log-analytics-use-queries.md)

## <a name="see-also"></a>Lásd még

* [Az Operations Management Suite Naplóelemzési használata](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [A Naplóelemzési riasztási szabályok létrehozása](../log-analytics/log-analytics-alerts-creating.md)
