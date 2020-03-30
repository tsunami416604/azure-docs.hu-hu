---
title: A Grafana használata az Azure HDInsightban
description: Ismerje meg, hogyan érheti el a Grafana irányítópultot az Apache Hadoop-fürtökkel az Azure HDInsightban
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: cd515bfd1dc57e78a041ed96686e1ba692bf6d3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082863"
---
# <a name="access-grafana-in-azure-hdinsight"></a>A Grafana elérése az Azure HDInsightban

[Grafana](https://grafana.com/) egy népszerű, nyílt forráskódú grafikon és műszerfal építő. Grafana funkciókban gazdag; nem csak lehetővé teszi a felhasználók számára, hogy testre szabható és megosztható irányítópultokat hozzanak létre, hanem sablonos / parancsfájlalapú irányítópultokat, LDAP integrációt, több adatforrást és egyebeket is kínál.

Jelenleg az Azure HDInsightban a Grafana a Spark, a HBase, a Kafka és az Interactive Query fürttípusokkal támogatott. Az Enterprise Security Pack engedélyezve van fürtök esetében nem támogatott.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="create-an-apache-hadoop-cluster"></a>Apache Hadoop-fürt létrehozása

Lásd: [Apache Hadoop-fürtök létrehozása az Azure Portalon.](../hdinsight-hadoop-create-linux-clusters-portal.md) **Fürttípus**esetén válassza a **Spark**, **Kafka**, **HBase**vagy **Interaktív lekérdezés lehetőséget.**

## <a name="access-the-grafana-dashboard"></a>A Grafana irányítópultjának elérése

1. Egy webböngészőből keresse `https://CLUSTERNAME.azurehdinsight.net/grafana/` meg a CLUSTERNAME helyhez a fürt nevét.

1. Adja meg a Hadoop-fürt felhasználói hitelesítő adatait.

1. A Grafana irányítópult jelenik meg, és így néz ki:

    ![HDInsight Grafana webes irányítópult](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "HDInsight Grafana irányítópult")

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem fogja tovább használni ezt az alkalmazást, törölje a következő lépésekkel létrehozott fürtöt:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. A felső **keresőmezőbe** írja be a **HDInsight kifejezést.**

1. Válassza a **HDInsight-fürtök et a** **Szolgáltatások csoportban.**

1. A megjelenő HDInsight-fürtök listájában válassza ki a létrehozott fürt melletti **...** elemet.

1. Válassza a **Törlés** elemet. Válassza az **Igen** lehetőséget.

## <a name="next-steps"></a>További lépések

A HDInsight használatával történő adatelemzésről az alábbi cikkekben talál további információt:

* [Az Apache Hive használata a HDInsight segítségével.](../hadoop/hdinsight-use-hive.md)

* [A MapReduce használata a HDInsight](../hadoop/hdinsight-use-mapreduce.md)segítségével.

* [Ismerkedés a VISUAL Studio Hadoop HDInsight-eszközökkel.](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
