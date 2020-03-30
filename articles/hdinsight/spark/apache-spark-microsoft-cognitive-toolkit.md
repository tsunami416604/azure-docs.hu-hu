---
title: Microsoft Cognitive Toolkit az Apache Sparkkal – Azure HDInsight
description: Ismerje meg, hogyan alkalmazható egy betanított Microsoft Cognitive Toolkit deep learning modell egy azure-beli HDInsight Spark-fürt Spark Python API-jával egy adatkészletre.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/14/2020
ms.openlocfilehash: 1933db624dfef2ffa747ecb043be6730b6b884b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206554"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>A Microsoft Cognitive Toolkit deep learning modelljének használata az Azure HDInsight Spark-fürtjével

Ebben a cikkben a következő lépéseket kell elvégeznie.

1. Egyéni parancsfájl futtatásával telepítse a [Microsoft Cognitive Toolkit-et](https://docs.microsoft.com/cognitive-toolkit/) egy Azure HDInsight Spark-fürtre.

2. [Jupyter-jegyzetfüzet](https://jupyter.org/) feltöltése az Apache Spark-fürtre, hogy megtekintse, hogyan alkalmazhat egy betanított Microsoft Cognitive Toolkit mélytanulási modellt egy Azure Blob Storage-fiókban lévő fájlokra a [Spark Python API (PySpark) használatával.](https://spark.apache.org/docs/latest/api/python/index.html) [Apache Spark](https://spark.apache.org/)

## <a name="prerequisites"></a>Előfeltételek

* Apache Spark-fürt megléte a HDInsightban. Lásd: [Apache Spark-fürt létrehozása.](./apache-spark-jupyter-spark-sql-use-portal.md)

* A Jupyter-notebookok és a HDInsighton futó Spark használatának ismerete. További információ: [Adatok betöltése és lekérdezések futtatása az Apache Spark segítségével a HDInsight on.](./apache-spark-load-data-run-query.md)

## <a name="how-does-this-solution-flow"></a>Hogyan folyik ez a megoldás?

Ez a megoldás meg oszlik a cikk és a cikk részeként feltöltött Jupyter-jegyzetfüzet között. Ebben a cikkben hajtsa végre az alábbi lépéseket:

* Parancsfájl-művelet futtatásával egy HDInsight Spark-fürtön a Microsoft Cognitive Toolkit és a Python-csomagok telepítéséhez.
* Töltse fel a Megoldást a HDInsight Spark-fürtre futó Jupyter-jegyzetfüzetet.

A jupyter-jegyzetfüzet a következő további lépéseket is lefedi.

* Mintaképek betöltése egy Spark-rugalmas elosztott adatkészletbe vagy RDD-be.
  * Modulok betöltése és készletek definiálása.
  * Töltse le az adatkészletet helyileg a Spark-fürtön.
  * Az adatkészlet átalakítása RDD-vé.
* A képek et egy betanított kognitív eszközkészlet modell használatával pontzathatja.
  * Töltse le a betanított kognitív eszközkészlet-modellt a Spark-fürtbe.
  * Munkavégző csomópontok által használandó függvények definiálása.
  * Pontozása a képeket a feldolgozó csomópontokon.
  * Modell pontosságának kiértékelése.

## <a name="install-microsoft-cognitive-toolkit"></a>A Microsoft Cognitive Toolkit telepítése

A Microsoft Cognitive Toolkit egy Spark-fürtre parancsfájlművelet használatával telepíthető. A parancsfájlművelet egyéni parancsfájlok segítségével telepíti azokat az összetevőket a fürtön, amelyek alapértelmezés szerint nem érhetők el. Használhatja az egyéni parancsfájlt az Azure Portalon, a HDInsight .NET SDK használatával vagy az Azure PowerShell használatával. A parancsfájl segítségével telepítheti az eszközkészletet a fürt létrehozásának részeként vagy a fürt üzembe futása után is.

Ebben a cikkben a portál segítségével telepíti az eszközkészletet, miután a fürt létrejött. Az egyéni parancsfájl futtatásának egyéb [módjairól a HDInsight-fürtök testreszabása parancsfájlművelettel című témakörben nyújt további lehetőségeket.](../hdinsight-hadoop-customize-cluster-linux.md)

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

A parancsfájlműveletek futtatásához az Azure Portal használatával kapcsolatos tudnivalókról a [HDInsight-fürtök testreszabása parancsfájlművelettel című témakörben talál.](../hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation) Győződjön meg arról, hogy a Microsoft Cognitive Toolkit telepítéséhez adja meg a következő bemeneteket. Használja a következő értékeket a parancsfájlművelethez:

|Tulajdonság |Érték |
|---|---|
|Parancsfájl típusa|- Egyéni|
|Név| Az MCT telepítése|
|Bash parancsfájl URI|`https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`|
|Csomóponttípus(ok):|Fej, Munkás|
|Paraméterek|None|

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>A Jupyter-jegyzetfüzet feltöltése az Azure HDInsight Spark-fürtre

A Microsoft Cognitive Toolkit és az Azure HDInsight Spark-fürt használatához be kell töltenie a Jupyter-jegyzetfüzet **CNTK_model_scoring_on_Spark_walkthrough.ipynb-t** az Azure HDInsight Spark-fürtbe. Ez a jegyzetfüzet elérhető a [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration)GitHubon a.

1. Töltse le [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration)és csomagolja ki .

1. Egy webböngészőből keresse `https://CLUSTERNAME.azurehdinsight.net/jupyter`meg `CLUSTERNAME` a , ahol a fürt neve.

1. A Jupyter-jegyzetfüzetben válassza a **Feltöltés** lehetőséget a jobb felső `CNTK_model_scoring_on_Spark_walkthrough.ipynb`sarokban, majd keresse meg a letöltést, és válassza ki a fájlt.

    ![Jupyter-jegyzetfüzet feltöltése az Azure HDInsight Spark-fürtbe](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Jupyter-jegyzetfüzet feltöltése az Azure HDInsight Spark-fürtbe")

1. Válassza **ismét a Feltöltés lehetőséget.**

1. A jegyzetfüzet feltöltése után kattintson a jegyzetfüzet nevére, majd kövesse magát a jegyzetfüzetutasításait az adatkészlet betöltésére és a cikk végrehajtására vonatkozóan.

## <a name="see-also"></a>Lásd még

* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek

* [Apache Spark bi-val: Interaktív adatelemzés a Spark használatával a HDInsightban az üzletiintelligencia-eszközökkel](apache-spark-use-bi-tools.md)
* [Apache Spark gépi tanulással: Használja a Sparkot a HDInsightban az épület hőmérsékletének elemzéséhez a fűtés- és légtechnikai adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learningsegítségével: A Spark használata a HDInsightban az élelmiszer-ellenőrzési eredmények előrejelzéséhez](apache-spark-machine-learning-mllib-ipython.md)
* [Webhelynapló-elemzés az Apache Spark használatával a HDInsightban](apache-spark-custom-library-website-log-analysis.md)
* [Az Application Insight telemetriai adatok elemzése az Apache Spark használatával a HDInsightban](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása

* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények

* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight eszközök beépülő moduljával távolról debugelje az Apache Spark alkalmazásokat](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin-jegyzetfüzetek használata Apache Spark-fürttel a HDInsighton](apache-spark-zeppelin-notebook.md)
* [A Jupyter notebookhoz elérhető kernelek az Apache Spark-fürtHDInsighthoz](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése

* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
