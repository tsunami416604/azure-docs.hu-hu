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
ms.date: 09/12/2017
ms.author: nitinme
ms.openlocfilehash: 21b474e37ef0a6037e05ee1fe8e5088cb3e3601d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="add-hdinsight-cluster-management-solutions-to-log-analytics-preview"></a>HDInsight-fürt felügyeleti megoldások hozzáadni a Naplóelemzési (előzetes verzió)

HDInsight fürt-specifikus felügyeleti megoldásokat az Azure Naplóelemzés adhat hozzá. A [Felügyeleti megoldások](../log-analytics/log-analytics-add-solutions.md) további funkciókkal bővítik az OMS-t, további adat- és elemzőeszközöket biztosítva a Log Analytics számára. Ezek a megoldások fontos teljesítménybeli a HDInsight-fürtök adatainak begyűjtése, és adja meg az eszközök a metrikák kereséséhez. Ezek a megoldások képi megjelenítések és irányítópultok is a Hdinsightban támogatott a legtöbb fürt típusok biztosítják. A metrikák gyűjtött megoldás segítségével létrehozhat egyéni ellenőrzési szabályok és értesítések. 

Ebből a cikkből megtanulhatja a fürtökkel kapcsolatos megoldások hozzáadása egy OMS-munkaterület.

## <a name="prerequisites"></a>Előfeltételek

* Konfigurálnia kell egy HDInsight-fürt használata az Azure Naplóelemzés. Útmutatásért lásd: [használata Azure Naplóelemzés HDInsight-fürtökkel](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="add-cluster-specific-management-solutions"></a>Fürt-specifikus megoldások hozzáadása

Ebben a szakaszban egy HBase-fürt felügyeleti megoldás egy meglévő OMS-munkaterület hozzáadhat. Más HDInsight-fürttípusok hasonló megoldások hamarosan elérhető lesz.

1. Nyissa meg az OMS-irányítópultot. Az Azure-portálon az Azure Naplóelemzés társított HDInsight-fürt panelén megnyitásához kattintson a figyelés lapon, és kattintson az **OMS irányítópult megnyitása**.

    ![Nyissa meg OMS irányítópult](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-log-analytics-open-oms-dashboard.png "nyitott OMS irányítópult")

1. Az OMS-irányítópulton kattintson **megoldások gyűjtemény** vagy az adatforrásnézet-tervezőből ikonra a bal oldali ablaktáblán.

    ![Adja hozzá a felügyeleti megoldás az OMS](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-add-management-solution-oms-portal.png "adja hozzá a felügyeleti megoldás az OMS Szolgáltatáshoz")

2. A megoldások gyűjteményben található **HDInsight HBase figyelési**, majd kattintson a csempére.

    ![A HBase felügyeleti megoldást](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/find-hbase-management-solution.png "HBase felügyeleti megoldást")

3. A következő képernyőn kattintson **Hozzáadás**.

     ![Adja hozzá a HBase-kezelési megoldás](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/add-hbase-management-solution.png "adja hozzá a HBase-kezelési megoldás")

4. Meg kell jelennie egy csempe OMS-irányítópulton a HBase-kezelési megoldást. Ha a fürt (Ez a cikk előfeltételeivel részeként) OMS társított HBase-fürtöt, a csempe a fürt nevét, valamint a fürt a csomópontok számát mutatja.

    ![A HBase-kezelési megoldás hozzáadott](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/added-hbase-management-solution.png "hozzáadott HBase-kezelési megoldás")

## <a name="next-steps"></a>Következő lépések

* [A HDInsight-fürtök figyelése az Azure Naplóelemzés lekérdezése](hdinsight-hadoop-oms-log-analytics-use-queries.md)

## <a name="see-also"></a>Lásd még:

* [OMS Naplóelemzési használata](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [A Naplóelemzési riasztási szabályok létrehozása](../log-analytics/log-analytics-alerts-creating.md)
