---
title: Parancsfájlművelet - jupyterrel, Cellafunkciók az Azure HDInsight telepítse a Python-csomagok
description: Lépésenkénti útmutató konfigurálása elérhető Jupyter notebookok a HDInsight Spark-fürtök használatával külső python-csomagok használata.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: hrasheed
ms.openlocfilehash: 8bc44949d804349de37796a2695edbdc64693edf
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518677"
---
# <a name="use-script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Parancsfájlművelet használata a HDInsight Apache Spark-fürtök Jupyter notebookokhoz külső Python-csomagok telepítése
> [!div class="op_single_selector"]
> * [Használatával](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Szkriptműveletek használatával](apache-spark-python-package-installation.md)

Ismerje meg, hogyan Parancsfájlműveletek segítségével konfigurálhatja egy [Apache Spark](https://spark.apache.org/) külső, a Közösség által biztosított használatára a HDInsight-fürt **python** csomagokat, amelyek nem tartalmazza a fürt-a-beépített.

> [!NOTE]  
> Beállíthatja a Jupyter notebook használatával `%%configure` Magic Quadrant külső csomagok használata. Útmutatásért lásd: [külső csomagok használata a HDInsight Apache Spark-fürtök Jupyter-notebookjait](apache-spark-jupyter-notebook-use-external-packages.md).

Kereshet a [csomagindexet](https://pypi.python.org/pypi) a teljes listát az elérhető csomagokat. Elérhető csomagok listáját a más forrásokból is beszerezheti. Például telepíthet keresztül elérhető csomagok [conda-forge](https://conda-forge.org/feedstocks/).

Ebben a cikkben megismerheti, hogyan telepítheti a [TensorFlow](https://www.tensorflow.org/) csomag szkriptműveletekkel a fürtön, és ezzel példaként a Jupyter notebook használatával.

## <a name="prerequisites"></a>Előfeltételek
Az alábbiakkal kell rendelkeznie:

* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]  
   > Ha Ön még nem rendelkezik a HDInsight linuxon futó Spark-fürt, szkriptműveletek futtathatja a fürt létrehozása során. Keresse meg a dokumentációt a [egyéni parancsfájl-műveletek használata](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).
   
## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>A HDInsight-fürtökön használt nyílt forráskódú szoftverek támogatása

A Microsoft Azure HDInsight szolgáltatást használ az ökoszisztéma formátumú körül Apache Hadoop nyílt forráskódú technológiák. A Microsoft Azure nyílt forráskódú technológiák egy általános szintű támogatást biztosít. További információkért lásd: a **támogatás hatóköre** szakaszában a [Azure támogatás – gyakori kérdések webhely](https://azure.microsoft.com/support/faq/). A HDInsight szolgáltatás egy további szintű támogatást biztosít a beépített összetevők.

A HDInsight szolgáltatásban elérhető nyílt forráskódú összetevőket két típusa van:

* **Beépített összetevők** – ezek az összetevők a HDInsight-fürtök előre telepítve vannak, és adja meg a fürt fő funkciói. Ha például az Apache Hadoop YARN ResourceManager, az Apache Hive-lekérdezés (HiveQL) nyelv és a Mahout kódtár tartoznak ebbe a kategóriába. Kiszolgálófürt-összetevők teljes listája megtalálható [a HDInsight által biztosított az Apache Hadoop-fürtverziók újdonságai](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).
* **Egyéni összetevők** -, a fürt felhasználói telepítése vagy használata az alkalmazások és szolgáltatások valamelyik összetevő a Közösségben elérhető vagy Ön által létrehozott.

> [!IMPORTANT]   
> A HDInsight-fürthöz megadott összetevők teljes mértékben támogatottak. Support segít elkülöníteni, és ezeket az összetevőket kapcsolatos problémák megoldásához.
>
> Egyéni összetevők annak érdekében, hogy a probléma további hibaelhárításához üzletileg ésszerű támogatást kapnak. Lehet, hogy a probléma megoldásához a Microsoft ügyfélszolgálatához, vagy előfordulhat, hogy megadását is végezhetnek elérhető csatornák a nyílt forráskódú technológiák, ahol található részletes szakértelmét, hogy a technológiát. Például sok, használható, például közösségi helyek vannak: [A HDInsight az MSDN-fórumokra](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [ https://stackoverflow.com ](https://stackoverflow.com). Is Apache projektek rendelkeznek projekt helyek [ https://apache.org ](https://apache.org), például: [Hadoop](https://hadoop.apache.org/).


## <a name="use-external-packages-with-jupyter-notebooks"></a>Külső csomagok használata Jupyter notebookokkal

1. Az a [az Azure portal](https://portal.azure.com/), keresse meg a fürthöz.  

2. A kiválasztott, a bal oldali ablaktáblán, a fürttel **beállítások**válassza **Szkriptműveletek**.

3. Válassza ki **+ új küldés**.

4. Adja meg a következő értékeket a **Szkriptművelet** ablakban:  


    |Paraméter | Érték |
    |---|---|
    |Szkripttípus | Válassza ki **– az egyéni** a legördülő listából.|
    |Name (Név) |Adja meg `tensorflow` a szövegmezőben.|
    |Bash parancsfájl URI azonosítója |Adja meg `https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh` a szövegmezőben. |
    |Csomóponttípus(ok) | Válassza ki a **fő**, és **feldolgozó** jelölőnégyzeteket. |

    `tensorflowinstall.sh` tartalmazza a következő parancsokat:

    ```bash
    #!/usr/bin/env bash
    /usr/bin/anaconda/bin/conda install -c conda-forge tensorflow
    ```

5. Kattintson a **Létrehozás** gombra.  Keresse meg a dokumentációt a [egyéni parancsfájl-műveletek használata](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

6. Várjon, amíg a parancsfájl végrehajtására.  A **Szkriptműveletek** panel fog állapot **új Parancsfájlműveletek az aktuális fürtművelet befejeződése után küldhetők** a parancsfájl végrehajtása közben.  Egy folyamatjelző tekintheti meg az Ambari felhasználói felületén **Háttérműveletekhez** ablak.

7. A PySpark Jupyter notebook megnyitása.  Lásd: [Jupyter notebook létrehozása a Spark HDInsight](./apache-spark-jupyter-notebook-kernels.md#create-a-jupyter-notebook-on-spark-hdinsight) lépéseit.

    ![Új Jupyter notebook létrehozása](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "Új Jupyter notebook létrehozása")

8. Mostantól `import tensorflow` és a egy hello world példa futtatásához. Írja be a következő kódot:

    ```
    import tensorflow as tf
    hello = tf.constant('Hello, TensorFlow!')
    sess = tf.Session()
    print(sess.run(hello))
    ```

    Az eredmény a következőhöz hasonló:
    
    ![TensorFlow kódfuttatás](./media/apache-spark-python-package-installation/execution.png "végrehajtása TensorFlow-kód")

> [!NOTE]  
> Nincsenek két python-telepítés esetén a fürt a. A Spark fogja használni a Anaconda python-telepítés található `/usr/bin/anaconda/bin` , és alapértelmezés szerint a Python 2.7-es környezetben. Python 3.x és telepítési csomagok használata a PySpark3 kernel az elérési útját használja a `conda` végrehajtható, hogy a környezetben, és használja a `-n` paraméterrel adja meg a környezetben. Ha például a parancs `/usr/bin/anaconda/envs/py35/bin/conda install -c conda-forge ggplot -n py35`, telepíti a `ggplot` csomagot a Python 3.5-ös környezetben történő a `conda-forge` csatorna.

## <a name="seealso"></a>Lásd még:
* [Áttekintés: Az Apache Spark on Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek
* [Az Apache Spark és BI: Spark on HDInsight használatával, BI-eszközökkel interaktív adatelemzés végrehajtása](apache-spark-use-bi-tools.md)
* [Az Apache Spark és Machine Learning: A Spark használata a HDInsight HVAC-adatok épület-hőmérséklet elemzésére](apache-spark-ipython-notebook-machine-learning.md)
* [Az Apache Spark és Machine Learning: A HDInsight Spark használata az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [A webhelynapló elemzése a HDInsight az Apache Spark használatával](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [Külső csomagok használata Jupyter notebookok a HDInsight az Apache Spark-fürtök](apache-spark-jupyter-notebook-use-external-packages.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az Apache Spark-alkalmazások távoli hibakeresése az IntelliJ IDEA HDInsight-eszközei beépülő használata](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Az Apache Zeppelin notebookok használata a HDInsight Apache Spark-fürt](apache-spark-zeppelin-notebook.md)
* [Notebookokhoz elérhető kernelek Jupyter a HDInsight az Apache Spark-fürt](apache-spark-jupyter-notebook-kernels.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
