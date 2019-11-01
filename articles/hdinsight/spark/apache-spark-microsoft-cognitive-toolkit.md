---
title: Microsoft Cognitive Toolkit Apache Spark-Azure HDInsight
description: Megtudhatja, hogyan alkalmazhat egy betanított Microsoft Cognitive Toolkit mély tanulási modellt a Spark Python API-val egy Azure HDInsight Spark-fürtben.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: hrasheed
ms.openlocfilehash: 0f4172c7a5b287c85c0548c7fe9812305a1ee1e6
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241534"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Microsoft Cognitive Toolkit Deep learning-modell használata Azure HDInsight Spark-fürttel

Ebben a cikkben a következő lépéseket hajtja végre.

1. Futtasson egyéni parancsfájlt [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) telepítéséhez Azure HDInsight Spark-fürtön.

2. Töltsön fel egy [Jupyter Notebookt](https://jupyter.org/) a [Apache Spark](https://spark.apache.org/) -fürtbe, és Ismerje meg, hogyan alkalmazhat egy betanított Microsoft Cognitive Toolkit mély tanulási modellt egy Azure Blob Storage-fiókban lévő fájlokra a [Spark Python API (PySpark) használatával.](https://spark.apache.org/docs/0.9.0/python-programming-guide.html)

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. A cikk elkezdése előtt Azure-előfizetéssel kell rendelkeznie. Lásd: [Ingyenes Azure-fiók létrehozása még ma](https://azure.microsoft.com/free).

* **Azure HDInsight Spark-fürt**. Ehhez a cikkhez hozzon létre egy Spark 2,0-fürtöt. Útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsight-ben](apache-spark-jupyter-spark-sql.md).

## <a name="how-does-this-solution-flow"></a>Hogyan működik ez a megoldás?

Ez a megoldás a jelen cikk és a jelen cikk részeként feltöltött Jupyter-jegyzetfüzetek között oszlik meg. Ebben a cikkben a következő lépéseket hajtja végre:

* Parancsfájl-művelet futtatása HDInsight Spark-fürtön Microsoft Cognitive Toolkit és Python-csomagok telepítéséhez.
* Töltse fel a megoldást futtató Jupyter-jegyzetfüzetet a HDInsight Spark-fürtre.

A következő további lépéseket a Jupyter-jegyzetfüzet ismerteti.

- Betöltheti a mintaképeket egy Spark Resiliant elosztott adatkészletbe vagy RDD.
   - Modulok betöltése és beállításkészlet megadása
   - Töltse le az adatkészletet helyileg a Spark-fürtön.
   - Alakítsa át az adatkészletet egy RDD.
- A képeket a betanított Cognitive Toolkit modell használatával szerzi be.
   - Töltse le a betanított Cognitive Toolkit modellt a Spark-fürtre.
   - A munkavégző csomópontok által használandó függvények definiálása.
   - A lemezképek kiértékelése a munkavégző csomópontokon.
   - Modell pontosságának kiértékelése.


## <a name="install-microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit telepítése

A Microsoft Cognitive Toolkitt a Spark-fürtön parancsfájl-művelettel telepítheti. A parancsfájl-művelet egyéni parancsfájlokat használ a fürtön olyan összetevők telepítéséhez, amelyek alapértelmezés szerint nem érhetők el. Az egyéni szkriptet használhatja a Azure Portal a HDInsight .NET SDK vagy a Azure PowerShell használatával. Az eszközkészletet a fürt létrehozása vagy a fürt üzembe helyezése után is telepítheti a parancsfájl segítségével. 

Ebben a cikkben a portál használatával telepítjük az eszközkészletet a fürt létrehozása után. Az egyéni szkript futtatásának egyéb módjaival kapcsolatban lásd: [HDInsight-fürtök testreszabása parancsfájl-művelet használatával](../hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

Az Azure Portal parancsfájl-művelet futtatására való használatával kapcsolatos utasításokért lásd: [HDInsight-fürtök testreszabása parancsfájl-művelettel](../hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation). Győződjön meg arról, hogy a következő adatokat adja meg a Microsoft Cognitive Toolkit telepítéséhez.

* Adjon meg egy értéket a parancsfájl művelet neveként.

* A **bash parancsfájl URI-ja**mezőben adja meg a `https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`.

* Győződjön meg arról, hogy csak a Head és a Worker csomóponton futtatja a parancsfájlt, és törölje az összes többi jelölőnégyzet jelölését.

* Kattintson a  **Create** (Létrehozás) gombra.

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>A Jupyter notebook feltöltése Azure HDInsight Spark fürtre

Ahhoz, hogy a Microsoft Cognitive Toolkitt a Azure HDInsight Spark fürttel használhassa, be kell töltenie a Jupyter notebook **CNTK_model_scoring_on_Spark_walkthrough. ipynb** a Azure HDInsight Spark-fürtre. Ez a jegyzetfüzet a GitHubon [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration)címen érhető el.

1. A GitHub-adattár klónozása [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration). A klónozással kapcsolatos utasításokért lásd: [adattár klónozása](https://help.github.com/articles/cloning-a-repository/).

2. A Azure Portal nyissa meg a már kiépített Spark-fürt panelt, kattintson a **fürt irányítópultja**, majd a **Jupyter notebook**elemre.

    A Jupyter notebookot a `https://<clustername>.azurehdinsight.net/jupyter/`URL-címére kattintva is elindíthatja. Cserélje le \<clustername >t a HDInsight-fürt nevére.

3. A Jupyter jegyzetfüzetből kattintson a jobb felső sarokban található **feltöltés** elemre, majd Navigáljon arra a helyre, ahol a GitHub-tárházat klónozotta.

    ![Jupyter-jegyzetfüzet feltöltése Azure HDInsight Spark-fürtre](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Jupyter-jegyzetfüzet feltöltése Azure HDInsight Spark-fürtre")

4. Kattintson a **feltöltés** gombra.

5. A jegyzetfüzet feltöltése után kattintson a jegyzetfüzet nevére, majd kövesse a jegyzetfüzetben megjelenő utasításokat az adathalmaz betöltéséhez és a cikk végrehajtásához.

## <a name="see-also"></a>Lásd még:
* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Alkalmazási helyzetek
* [Apache Spark BI: interaktív adatelemzés végrehajtása a Spark on HDInsight és a BI Tools használatával](apache-spark-use-bi-tools.md)
* [Apache Spark a Machine Learning használatával: a Spark in HDInsight használata az építési hőmérséklet elemzésére a HVAC-adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learning használatával: az élelmiszer-ellenőrzési eredmények előrejelzéséhez használja a Spark in HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Webhely-naplózási elemzés Apache Spark használatával a HDInsight-ben](apache-spark-custom-library-website-log-analysis.md)
* [Az Application Insight telemetria-adatelemzési szolgáltatásának használata a HDInsight-ben Apache Spark](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata a Apache Spark alkalmazások távoli hibakereséséhez](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin notebookok használata Apache Spark-fürttel a HDInsight-on](apache-spark-zeppelin-notebook.md)
* [Jupyter notebookokhoz elérhető kernelek Apache Spark-fürtben HDInsight](apache-spark-jupyter-notebook-kernels.md)
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
