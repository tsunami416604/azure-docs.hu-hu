---
title: Microsoft Azure HDInsight Spark mély biztonságával és kognitív eszközkészlet |} Microsoft Docs
description: Ismerje meg, hogyan kognitív eszközkészlet Microsoft mély tanulási modell betanítását alkalmazhatja a Spark Python API használatát egy Azure HDInsight Spark-fürt dataset.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: jgao
ms.openlocfilehash: 7afb891642e3e53da5eb1e17ee654fb5fb42c313
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31518535"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Microsoft kognitív eszközkészlet mély tanulása Azure HDInsight Spark-fürt használatára

Ez a cikk a hajtsa végre az alábbi lépéseket.

1. Microsoft kognitív eszközkészlet telepítése egy Azure HDInsight Spark-fürt egyéni parancsfájl futtatása.

2. Jupyter notebook feltölteni a Microsoft kognitív eszközkészlet mély tanulási modell betanítását alkalmazhat a fájlokra, az Azure Blob Storage-fiók használatával történő Spark-fürtön a [Spark Python API (PySpark)](https://spark.apache.org/docs/0.9.0/python-programming-guide.html)

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Az oktatóanyag elindításához Azure-előfizetéssel kell rendelkeznie. Lásd: [Ingyenes Azure-fiók létrehozása még ma](https://azure.microsoft.com/free).

* **Az Azure HDInsight Spark-fürt**. Ez a cikk a Spark 2.0 fürt létrehozása. Útmutatásért lásd: [Azure HDInsight létrehozása Apache Spark-fürt](apache-spark-jupyter-spark-sql.md).

## <a name="how-does-this-solution-flow"></a>Hogyan nem flow ehhez a megoldáshoz?

Ebben a megoldásban ez a cikk és ebben az oktatóanyagban részeként feltöltött Jupyter notebook között oszlik meg. Ebben a cikkben végezze el a következő lépéseket:

* Futtassa a parancsfájlművelet egy HDInsight Spark-fürt telepítése a Microsoft kognitív eszközkészlet és Python-csomagokat.
* Töltse fel a Jupyter notebook futtató a megoldás a HDInsight Spark-fürt.

A következő fennmaradó lépéseit lásd: a Jupyter notebook.

- Egy Spark Resiliant elosztott adatkészlet vagy RDD minta képek betöltése
   - Modulok be, és a készletek meghatározása
   - Töltse le a dataset helyileg a Spark-fürtön
   - Az adatkészlethez konvertálása egy RDD
- Pontszám modell betanítását kognitív eszközkészlet a képeket
   - Töltse le a betanított modell kognitív eszközkészlet a Spark-fürt
   - Adja meg a munkavégző csomópontokhoz által használt funkciók
   - A lemezképek pontozása a feldolgozó csomópontok
   - Értékelje ki a modell pontosságát


## <a name="install-microsoft-cognitive-toolkit"></a>Microsoft kognitív eszközkészlet telepítése

Telepítheti a Microsoft kognitív eszközkészlet parancsfájlművelet használata Spark-fürtön. Parancsfájlművelet összetevőinek telepítése a fürt nem alapértelmezés szerint rendelkezésre álló egyéni parancsfájlokat használ. Használhatja az Azure portálról egyéni parancsfájl HDInsight .NET SDK használatával, vagy az Azure PowerShell használatával. A parancsfájl segítségével az eszközkészlet telepítése vagy részeként a fürt létrehozása, vagy a fürt megfelelően működik, és után is. 

Ez a cikk a portál használjuk az eszközkészlet telepítése a fürt létrehozása után. Egyéb módjai a egyéni parancsfájl futtatásához, lásd: [testreszabása HDInsight-fürtök használata parancsfájlművelet](../hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Az Azure portál használata

Az Azure portál használata parancsfájl művelet, lásd: [testreszabása HDInsight-fürtök használata parancsfájlművelet](../hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation). Ellenőrizze, hogy megadja a következő bemenet Microsoft kognitív eszközkészlet telepítése.

* Adjon meg egy értéket a parancsfájlművelet nevét.

* A **Bash parancsfájlok URI**, adja meg `https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`.

* Győződjön meg arról, hogy csak a head és a feldolgozó csomópontok futtassa a parancsfájlt, és törölje a más jelölést.

* Kattintson a **Create** (Létrehozás) gombra.

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Töltse fel a Jupyter notebook Azure HDInsight Spark-fürt

A Microsoft kognitív eszközkészlet használata az Azure HDInsight Spark-fürt, be kell tölteni a Jupyter notebook **CNTK_model_scoring_on_Spark_walkthrough.ipynb** az Azure HDInsight Spark-fürtre. A notebook érhető el a Githubon: [ https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration ](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration).

1. A GitHub-tárház klónozása [ https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration ](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration). Klónozás útmutatásért lásd: [egy tárház klónozása](https://help.github.com/articles/cloning-a-repository/).

2. Az Azure portálról nyissa meg az Ön már kiépített, kattintson a Spark-fürt panelén **fürt irányítópult**, és kattintson a **Jupyter notebook**.

    A Jupyter notebook indítsa el az URL-cím megnyitásával `https://<clustername>.azurehdinsight.net/jupyter/`. Cserélje le \<clustername > kifejezést a HDInsight-fürt nevére.

3. Kattintson a Jupyter notebook **feltöltése** a jobb felső sarokban, és navigáljon arra a helyre, ahol a GitHub-tárházban klónozott.

    ![Jupyter notebook feltöltése az Azure HDInsight Spark-fürt](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "feltöltése Jupyter notebook használatához Azure HDInsight Spark-fürt")

4. Kattintson a **feltöltése** újra.

5. A notebook feltöltése, után kattintson a notebook neve, és kövesse a saját magát a notebook töltse be az adatokat, és hajtsa végre az oktatóanyag utasításait.

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
