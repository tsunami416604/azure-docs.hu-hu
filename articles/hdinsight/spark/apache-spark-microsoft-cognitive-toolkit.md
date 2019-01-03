---
title: A Microsoft Cognitive Toolkit deep learning az Azure HDInsight Spark-
description: Ismerje meg, hogyan lehet alkalmazni egy Microsoft Cognitive Toolkit deep learning betanított modellt egy adatkészletet, a Spark Python API használatával az Azure HDInsight Spark-fürt.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: hrasheed
ms.openlocfilehash: 76fc2c9a0864417e380471f8de59d148e0fbf7b4
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2018
ms.locfileid: "53651827"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>A Microsoft Cognitive Toolkit deep learning-modell az Azure HDInsight Spark-fürt használata

Ebben a cikkben hajtsa végre a következő lépéseket.

1. Egyéni parancsfájllal történő telepítés [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) egy Azure HDInsight Spark-fürtön.

2. Töltse fel egy [Jupyter Notebook](https://jupyter.org/) , a [Apache Spark](https://spark.apache.org/) fürt alkalmazása egy Microsoft Cognitive Toolkit deep learning betanított modellt egy Azure Blob Storage-fiókra a fájlok megtekintéséhez a [ A Spark Python API-t (PySpark)](https://spark.apache.org/docs/0.9.0/python-programming-guide.html)

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Az oktatóanyag elindításához Azure-előfizetéssel kell rendelkeznie. Lásd: [Ingyenes Azure-fiók létrehozása még ma](https://azure.microsoft.com/free).

* **Az Azure HDInsight Spark-fürt**. Ebben a cikkben egy Spark 2.0-fürt létrehozása. Útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="how-does-this-solution-flow"></a>Hogyan nem ez a megoldás flow?

Ebben a megoldásban ez a cikk és a egy Jupyter notebookot, ez az oktatóanyag részeként feltöltött között oszlik meg. Ebben a cikkben, kövesse az alábbi lépéseket:

* Szkriptműveletet futtatunk egy HDInsight Spark-fürt telepítése a Microsoft Cognitive Toolkit- és Python-csomagokat.
* Töltse fel a Jupyter notebookot, amely a megoldás fut, hogy a HDInsight Spark-fürtön.

A Jupyter notebook ismerkedhet meg a következő hátralévő lépéseket.

- Töltse be a Spark Resiliant elosztott adatkészlet vagy RDD mintát.
   - Modulok betöltése, és adja meg a szolgáltatás.
   - Töltse le az adatkészlet helyileg, a Spark-fürtön.
   - Az adatkészlet átalakítása az RDD.
- Pontszám a képek, a Cognitive Toolkit betanított modell használatával.
   - Töltse le a Cognitive Toolkit betanított modell a Spark-fürtön.
   - Adja meg a feldolgozó csomópontok által használt funkciók.
   - A képek pontszám a feldolgozó csomópontokat.
   - Értékelje ki a modell pontosságát.


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
* [Áttekintés: Az Apache Spark on Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek
* [Az Apache Spark és BI: Spark on HDInsight használatával, BI-eszközökkel interaktív adatelemzés végrehajtása](apache-spark-use-bi-tools.md)
* [Az Apache Spark és Machine Learning: A Spark használata a HDInsight HVAC-adatok épület-hőmérséklet elemzésére](apache-spark-ipython-notebook-machine-learning.md)
* [Az Apache Spark és Machine Learning: A HDInsight Spark használata az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [A webhelynapló elemzése a HDInsight az Apache Spark használatával](apache-spark-custom-library-website-log-analysis.md)
* [Application Insights telemetriai adatainak elemzése Apache Spark on HDInsight használatával](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az Apache Spark-alkalmazások távoli hibakeresése az IntelliJ IDEA HDInsight-eszközei beépülő használata](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Az Apache Zeppelin notebookok használata a HDInsight Apache Spark-fürt](apache-spark-zeppelin-notebook.md)
* [Notebookokhoz elérhető kernelek Jupyter a HDInsight az Apache Spark-fürt](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md
