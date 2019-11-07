---
title: Parancsfájl-művelet a Python-csomagokhoz az Azure HDInsight Jupyter
description: Részletes útmutató a HDInsight Spark-fürtökkel elérhető Jupyter notebookok külső Python-csomagok használatához való konfigurálásához a parancsfájl-művelet használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/05/2019
ms.openlocfilehash: e344035f05e192de1779a60fc99a7e0144566654
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682187"
---
# <a name="script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-on-hdinsight"></a>Parancsfájl-művelet külső Python-csomagok telepítéséhez a Jupyter notebookokhoz a HDInsight-on Apache Spark

> [!div class="op_single_selector"]
> * [A Cell Magic használata](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Parancsfájl-művelet használata](apache-spark-python-package-installation.md)

Megtudhatja, hogyan használhat parancsfájl-műveleteket egy [Apache Spark](https://spark.apache.org/) -fürt HDInsight való konfigurálásához olyan külső, Közösség által támogatott **Python** -csomagok használatára, amelyek nem szerepelnek a fürtben.

> [!NOTE]  
> Jupyter notebookot úgy is konfigurálhat, hogy `%%configure` Magic használatával külső csomagokat használ. Útmutatásért lásd: [külső csomagok használata Jupyter notebookokkal Apache Spark-fürtökön a HDInsight-ben](apache-spark-jupyter-notebook-use-external-packages.md).

A [csomag indexében](https://pypi.python.org/pypi) a rendelkezésre álló csomagok teljes listáját is megkeresheti. Lekérheti az egyéb forrásokból származó elérhető csomagok listáját is. Telepítheti például a [Conda-Forge](https://conda-forge.org/feedstocks/)használatával elérhetővé tett csomagokat.

Ebből a cikkből megtudhatja, hogyan telepítheti a [TensorFlow](https://www.tensorflow.org/) -csomagot a fürtön lévő parancsfájl-művelettel, és hogyan használhatja a Jupyter notebookon keresztül példaként.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]  
   > Ha még nem rendelkezik Spark-fürttel a HDInsight Linuxon, a fürt létrehozása során parancsfájl-műveleteket is futtathat. Tekintse meg az [egyéni parancsfájl-műveletek használatát ismertető](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)dokumentációt.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>A HDInsight-fürtökön használt nyílt forráskódú szoftverek támogatása

A Microsoft Azure HDInsight szolgáltatás a Apache Hadoop által létrehozott nyílt forráskódú technológiák ökoszisztémáját használja. A Microsoft Azure a nyílt forráskódú technológiák általános támogatását biztosítja. További információ: Azure- [támogatás – gyakori kérdések webhelye](https://azure.microsoft.com/support/faq/). A HDInsight szolgáltatás további szintű támogatást biztosít a beépített összetevőkhöz.

A HDInsight szolgáltatásban kétféle nyílt forráskódú összetevő érhető el:

* **Beépített összetevők** – ezek az összetevők előre telepítve vannak a HDInsight-fürtökön, és alapvető funkciókat biztosítanak a fürt számára. Például Apache Hadoop fonal erőforráskezelő, a Apache Hive lekérdezési nyelve (HiveQL) és a Mahout-könyvtár ebbe a kategóriába tartozik. A fürt összetevőinek teljes listája a [HDInsight által biztosított Apache Hadoop-fürt verziójának újdonságai](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)című részében érhető el.
* **Egyéni összetevők** – a fürt felhasználója a munkaterhelésen belül bármely, a Közösségben elérhető vagy Ön által létrehozott összetevő használatával telepítheti vagy használhatja a számítási feladatot.

> [!IMPORTANT]
> A HDInsight-fürthöz biztosított összetevők teljes mértékben támogatottak. Microsoft ügyfélszolgálata segít elkülöníteni és elhárítani ezeket az összetevőket érintő problémákat.
>
> Az egyéni összetevők kereskedelmileg ésszerű támogatást kapnak a probléma további megoldásához. A Microsoft támogatási szolgálata megoldhatja a problémát, vagy megkérheti, hogy a nyílt forráskódú technológiák számára elérhető csatornákat adjon meg, ahol az adott technológia mélyreható szaktudása található. Többek között számos közösségi webhely használható, például a [következőhöz: HDInsight MSDN-fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) [https://stackoverflow.com](https://stackoverflow.com). Emellett az Apache-projektek [https://apache.orgon ](https://apache.org)is rendelkeznek projekt-webhelyekkel, például: [Hadoop](https://hadoop.apache.org/).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Külső csomagok használata Jupyter notebookokkal

1. A [Azure Portal](https://portal.azure.com/)navigáljon a fürthöz.  

2. Ha kiválasztotta a fürtöt, a bal oldali ablaktábla **Beállítások**területén válassza a **parancsfájlok műveletek**elemet.

3. Válassza a **+ Küldés új**lehetőséget.

4. Adja meg a következő értékeket a **parancsfájl elküldése művelet** ablakhoz:  

    |Paraméter | Érték |
    |---|---|
    |Parancsfájl típusa | Válassza a **-Custom** elemet a legördülő listából.|
    |Name (Név) |Írja be a `tensorflow` a szövegmezőbe.|
    |Bash-parancsfájl URI-ja |Írja be a `https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh` a szövegmezőbe. |
    |Csomópont típusa (i) | Jelölje be a **Head**és a **Worker** (feldolgozó) jelölőnégyzetet. |

    `tensorflowinstall.sh` a következő parancsokat tartalmazza:

    ```bash
    #!/usr/bin/env bash
    /usr/bin/anaconda/bin/conda install -c conda-forge tensorflow
    ```

5. Kattintson a **Létrehozás** gombra.  Tekintse meg az [egyéni parancsfájl-műveletek használatát ismertető](../hdinsight-hadoop-customize-cluster-linux.md)dokumentációt.

6. Várjon, amíg a parancsfájl be nem fejeződik.  A **parancsfájl műveletei** ablaktáblán meg **kell adni az új parancsfájl-műveleteket, miután a fürt aktuális művelete befejeződött** , miközben a parancsfájl végrehajtása folyamatban van.  A folyamatjelző sáv a Ambari felhasználói felületének **hátterében lévő műveletek** ablakból tekinthető meg.

7. Nyisson meg egy PySpark Jupyter notebookot.  Lásd: [Jupyter-jegyzetfüzet létrehozása Spark-HDInsight](./apache-spark-jupyter-notebook-kernels.md#create-a-jupyter-notebook-on-spark-hdinsight) a lépések végrehajtásához.

    ![Új Jupyter-jegyzetfüzet létrehozása](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "Új Jupyter notebook létrehozása")

8. Most `import tensorflow` és egy Hello World példát fog futtatni. Írja be a következő kódot:

    ```
    import tensorflow as tf
    hello = tf.constant('Hello, TensorFlow!')
    sess = tf.Session()
    print(sess.run(hello))
    ```

    Az eredmény így néz ki:
    
    ![TensorFlow-kód végrehajtása](./media/apache-spark-python-package-installation/tensorflow-execution.png "TensorFlow-kód végrehajtása")

> [!NOTE]  
> Két Python-telepítés van a fürtben. A Spark a `/usr/bin/anaconda/bin` címen található anaconda Python-telepítést fogja használni, és alapértelmezés szerint a Python 2,7 környezetbe kerül. A Python 3. x és a PySpark3 kernel telepítési csomagjainak használatához használja az adott környezethez tartozó `conda` végrehajtható fájl elérési útját, és a `-n` paraméter használatával határozza meg a környezetet. A `/usr/bin/anaconda/envs/py35/bin/conda install -c conda-forge ggplot -n py35`parancs például telepíti a `ggplot` csomagot a Python 3,5 környezetbe a `conda-forge` csatornán keresztül.

## <a name="seealso"></a>Lásd még:

* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek

* [Apache Spark BI: interaktív adatelemzés végrehajtása a Spark on HDInsight és a BI Tools használatával](apache-spark-use-bi-tools.md)
* [Apache Spark a Machine Learning használatával: a Spark in HDInsight használata az építési hőmérséklet elemzésére a HVAC-adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learning használatával: az élelmiszer-ellenőrzési eredmények előrejelzéséhez használja a Spark in HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Webhely-naplózási elemzés Apache Spark használatával a HDInsight-ben](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása

* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények

* [Külső csomagok használata Jupyter notebookokkal Apache Spark-fürtökben a HDInsight-ben](apache-spark-jupyter-notebook-use-external-packages.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata a Apache Spark alkalmazások távoli hibakereséséhez](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin notebookok használata Apache Spark-fürttel a HDInsight-on](apache-spark-zeppelin-notebook.md)
* [Jupyter notebookokhoz elérhető kernelek Apache Spark-fürtben HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése

* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
