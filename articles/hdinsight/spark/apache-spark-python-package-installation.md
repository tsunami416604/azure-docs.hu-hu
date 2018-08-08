---
title: Parancsfájlművelet - jupyterrel, Cellafunkciók az Azure HDInsight telepítse a Python-csomagok
description: Lépésenkénti útmutató konfigurálása elérhető Jupyter notebookok a HDInsight Spark-fürtök használatával külső python-csomagok használata.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: jasonh
ms.openlocfilehash: 36e727a59b91303c8c62c5525f72c328e2792ad6
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39619175"
---
# <a name="use-script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Parancsfájlművelet használata a HDInsight Apache Spark-fürtök Jupyter notebookokhoz külső Python-csomagok telepítése
> [!div class="op_single_selector"]
> * [Használatával](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Szkriptműveletek használatával](apache-spark-python-package-installation.md)
>
>

Szkriptműveletek használata a HDInsight (Linux), külső, a Közösség által biztosított használata Apache Spark-fürt konfigurálása **python** csomagokat, amelyek nem tartalmazza a fürt-a-beépített.

> [!NOTE]
> Beállíthatja a Jupyter notebook használatával `%%configure` Magic Quadrant külső csomagok használata. Útmutatásért lásd: [külső csomagok használata a HDInsight Apache Spark-fürtök Jupyter-notebookjait](apache-spark-jupyter-notebook-use-external-packages.md).
> 
> 

Kereshet a [csomagindexet](https://pypi.python.org/pypi) a teljes listát az elérhető csomagokat. Elérhető csomagok listáját a más forrásokból is beszerezheti. Például telepíthet keresztül elérhető csomagok [Anaconda](https://docs.continuum.io/anaconda/pkg-docs) vagy [conda-forge](https://conda-forge.org/feedstocks/).

Ebben a cikkben megismerheti, hogyan telepítheti a [TensorFlow](https://www.tensorflow.org/) csomag szkriptműveletekkel a fürtön, és ezzel a Jupyter notebook használatával.

## <a name="prerequisites"></a>Előfeltételek
Az alábbiakkal kell rendelkeznie:

* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]
   > Ha Ön még nem rendelkezik a HDInsight linuxon futó Spark-fürt, szkriptműveletek futtathatja a fürt létrehozása során. Keresse meg a dokumentációt a [egyéni parancsfájl-műveletek használata](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).
   > 
   > 

## <a name="use-external-packages-with-jupyter-notebooks"></a>Külső csomagok használata Jupyter notebookokkal

1. Az [Azure portál](https://portal.azure.com/) kezdőpultján kattintson a Spark-fürthöz tartozó csempére (ha rögzítette azt a kezdőpulton). A fürtöt a következő helyről is megkeresheti: **Browse All (Összes tallózása)** > **HDInsight Clusters** (HDInsight-fürtök).   

2. A Spark-fürt panelén kattintson **Parancsfájlműveletek** a bal oldali ablaktáblán. Futtassa az egyéni művelet, amely tensorflow-hoz telepíti az átjárócsomópontokhoz és a feldolgozó csomópontok. A bash-szkript a lehet hivatkozni: https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh látogasson el a dokumentációt a [egyéni parancsfájl-műveletek használata](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

   > [!NOTE]
   > Nincsenek két python-telepítés esetén a fürt a. A Spark fogja használni a Anaconda python-telepítés található `/usr/bin/anaconda/bin`. A telepítést, az az egyéni műveletek keresztül hivatkozhat `/usr/bin/anaconda/bin/pip` és `/usr/bin/anaconda/bin/conda`.
   > 
   > 

3. A PySpark Jupyter notebook megnyitása

    ![Új Jupyter notebook létrehozása](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "Új Jupyter notebook létrehozása")

4. Az új notebook létrejött, és Untitled.pynb néven nyílt meg. A felső részen kattintson a notebook nevére, és adjon meg egy könnyen megjegyezhető nevet.

    ![Adjon nevet a notebooknak](./media/apache-spark-python-package-installation/hdinsight-spark-name-notebook.png "Adjon nevet a notebooknak")

5. Mostantól `import tensorflow` és a egy hello world példa futtatásához. 

    Kód másolása:

        import tensorflow as tf
        hello = tf.constant('Hello, TensorFlow!')
        sess = tf.Session()
        print(sess.run(hello))

    Az eredmény a következőhöz hasonló:
    
    ![TensorFlow kódfuttatás](./media/apache-spark-python-package-installation/execution.png "végrehajtása TensorFlow-kód")

## <a name="seealso"></a>Lásd még:
* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek
* [Spark és BI: Interaktív adatelemzés végrehajtása a Spark on HDInsight használatával, BI-eszközökkel](apache-spark-use-bi-tools.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az épület-hőmérséklet elemzésére HVAC-adatok alapján](apache-spark-ipython-notebook-machine-learning.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [A webhelynapló elemzése a Spark on HDInsight használatával](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Spark-fürtön a Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [Külső csomagok használata Jupyter notebookok a HDInsight az Apache Spark-fürtök](apache-spark-jupyter-notebook-use-external-packages.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark-alkalmazások távoli hibaelhárításához](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Zeppelin notebookok használata Spark-fürttel HDInsighton](apache-spark-zeppelin-notebook.md)
* [Jupyter notebookokhoz elérhető kernelek a HDInsight Spark-fürtjében](apache-spark-jupyter-notebook-kernels.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
