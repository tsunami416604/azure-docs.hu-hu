---
title: A Microsoft Cognitive Toolkit deep learning az Azure HDInsight Spark-
description: Ismerje meg, hogyan lehet alkalmazni egy Microsoft Cognitive Toolkit deep learning betanított modellt egy adatkészletet, a Spark Python API használatával az Azure HDInsight Spark-fürt.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: jasonh
ms.openlocfilehash: cc36c68f4867b9b450703c881a13a65f17ebad4b
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43049451"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>A Microsoft Cognitive Toolkit deep learning-modell az Azure HDInsight Spark-fürt használata

Ebben a cikkben hajtsa végre a következő lépéseket.

1. A Microsoft Cognitive Toolkit telepítése egy Azure HDInsight Spark-fürt egyéni parancsfájl futtatása.

2. Jupyter notebook a Spark-fürtöt, hogyan alkalmazhatja a Microsoft Cognitive Toolkit deep learning betanított modell egy Azure Blob Storage-fiókra a fájlok megtekintéséhez töltse fel a [Spark Python API (PySpark)](https://spark.apache.org/docs/0.9.0/python-programming-guide.html)

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Az oktatóanyag elindításához Azure-előfizetéssel kell rendelkeznie. Lásd: [Ingyenes Azure-fiók létrehozása még ma](https://azure.microsoft.com/free).

* **Az Azure HDInsight Spark-fürt**. Ebben a cikkben egy Spark 2.0-fürt létrehozása. Útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="how-does-this-solution-flow"></a>Hogyan nem ez a megoldás flow?

Ebben a megoldásban ez a cikk és a egy Jupyter notebookot, ez az oktatóanyag részeként feltöltött között oszlik meg. Ebben a cikkben, kövesse az alábbi lépéseket:

* Szkriptműveletet futtatunk egy HDInsight Spark-fürt telepítése a Microsoft Cognitive Toolkit- és Python-csomagokat.
* Töltse fel a Jupyter notebookot, amely a megoldás fut, hogy a HDInsight Spark-fürtön.

A Jupyter notebook ismerkedhet meg a következő hátralévő lépéseket.

- Mintaképeket betöltése a Spark Resiliant elosztott adatkészlet vagy RDD
   - Modulokat betölteni és készletek definiálása
   - Töltse le az adatkészlet helyileg, a Spark-fürt
   - Az adatkészlet átalakítása az RDD
- A Cognitive Toolkit betanított modell használatával képek pontszám
   - Töltse le a Cognitive Toolkit betanított modell a Spark-fürt
   - Adja meg a feldolgozó csomópontok által használt funkciók
   - A képek pontszám a munkavégző csomópontok
   - Pontosság kiértékelése


## <a name="install-microsoft-cognitive-toolkit"></a>Telepítse a Microsoft Cognitive Toolkittel

Telepítheti a Microsoft Cognitive Toolkit szkriptműveletek használatával Spark-fürtön. Parancsfájlműveletekkel egyéni parancsfájlok segítségével összetevők telepíthetők a fürtön, amelyek nem érhetők el alapértelmezés szerint. Az egyéni szkriptet az Azure Portalról, használhatja a HDInsight .NET SDK-val, illetve az Azure PowerShell használatával. Az eszközkészlet telepítésével vagy a fürt létrehozásakor, vagy ha a fürt működik és részeként is használhatja a parancsfájl. 

Ez a cikk a portál használjuk az eszközkészlet telepítése a fürt létrehozása után. Más módszerekkel egyéni szkript futtatásához lásd: [testreszabása HDInsight-fürtök szkriptműveletekkel](../hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Az Azure portál használata

Parancsprogram-művelet futtatása az Azure Portal használatával, lásd: [testreszabása HDInsight-fürtök szkriptműveletekkel](../hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation). Győződjön meg arról, adja meg a következő bemeneti adatokat a Microsoft Cognitive Toolkit telepítése.

* Adja meg egy értéket a parancsfájlművelet nevét.

* A **Bash parancsfájl URI azonosítója**, adja meg `https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`.

* Győződjön meg arról, hogy csak a fő- és munkavégző csomópontok futtassa a parancsprogramot, és törölje a többi jelölést.

* Kattintson a **Create** (Létrehozás) gombra.

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Töltse fel a Jupyter notebook az Azure HDInsight Spark-fürt

A Microsoft Cognitive Toolkit használata az Azure HDInsight Spark-fürt, be kell tölteni a Jupyter notebook **CNTK_model_scoring_on_Spark_walkthrough.ipynb** az Azure HDInsight Spark-fürthöz. Ez a jegyzetfüzet érhető el a Githubon található [ https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration ](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration).

1. Klónozza a GitHub-adattárát [ https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration ](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration). A klónozni kívánt útmutatásért lásd: [a tárház klónozása](https://help.github.com/articles/cloning-a-repository/).

2. Az Azure Portalról nyissa meg a már üzembe helyezett, kattintson a Spark-fürt panelén **fürt irányítópultja**, és kattintson a **Jupyter notebook**.

    A Jupyter notebook indítsa el az URL-címen `https://<clustername>.azurehdinsight.net/jupyter/`. Cserélje le \<clustername > a HDInsight-fürt nevére.

3. A Jupyter notebook kattintson **feltöltése** a jobb felső sarokban, és navigáljon arra a helyre, amelybe klónozta a GitHub-adattárban.

    ![Töltse fel a Jupyter notebook az Azure HDInsight Spark-fürt](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "feltöltése Jupyter notebook az Azure HDInsight Spark-fürt")

4. Kattintson a **feltöltése** újra.

5. A notebook követően kattintson a notebook nevére, és kövesse az utasításokat, magát a jegyzetfüzet töltse be az adatokat, és hajtsa végre az oktatóanyag.

## <a name="see-also"></a>Lásd még
* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek
* [Spark és BI: Interaktív adatelemzés végrehajtása a Spark on HDInsight használatával, BI-eszközökkel](apache-spark-use-bi-tools.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az épület-hőmérséklet elemzésére HVAC-adatok alapján](apache-spark-ipython-notebook-machine-learning.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [A webhelynapló elemzése a Spark on HDInsight használatával](apache-spark-custom-library-website-log-analysis.md)
* [Az Application Insights telemetriai adatainak elemzése a Spark on HDInsight használatával](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Spark-fürtön a Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark-alkalmazások távoli hibaelhárításához](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Zeppelin notebookok használata Spark-fürttel HDInsighton](apache-spark-zeppelin-notebook.md)
* [Jupyter notebookokhoz elérhető kernelek a HDInsight Spark-fürtjében](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md
