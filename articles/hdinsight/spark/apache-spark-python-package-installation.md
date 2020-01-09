---
title: Parancsfájl-művelet a Python-csomagokhoz az Azure HDInsight Jupyter
description: Részletes útmutató a HDInsight Spark-fürtökkel elérhető Jupyter notebookok külső Python-csomagok használatához való konfigurálásához a parancsfájl-művelet használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 109ac20d8a3d3dc87b4a83165c0e6c24808c1340
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75529643"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Python-környezet biztonságos kezelése az Azure HDInsightban szkriptműveletekkel

> [!div class="op_single_selector"]
> * [A Cell Magic használata](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Parancsfájl-művelet használata](apache-spark-python-package-installation.md)

A HDInsight két beépített Python-telepítéssel rendelkezik a Spark-fürtben, az anaconda Python 2,7 és a Python 3,5. Bizonyos esetekben az ügyfeleknek testre kell szabnia a Python-környezetet, például külső Python-csomagokat vagy más Python-verziót kell telepíteni. Ebben a cikkben azt mutatjuk be, hogyan kezelheti biztonságosan a Python-környezeteket [Apache Spark](https://spark.apache.org/) -fürtön a HDInsight-on.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]  
   > Ha még nem rendelkezik Spark-fürttel a HDInsight Linuxon, a fürt létrehozása során parancsfájl-műveleteket is futtathat. Tekintse meg az [egyéni parancsfájl-műveletek használatát ismertető](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)dokumentációt.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>A HDInsight-fürtökön használt nyílt forráskódú szoftverek támogatása

A Microsoft Azure HDInsight szolgáltatás a Apache Hadoop által létrehozott nyílt forráskódú technológiák ökoszisztémáját használja. A Microsoft Azure a nyílt forráskódú technológiák általános támogatását biztosítja. További információ: Azure- [támogatás – gyakori kérdések webhelye](https://azure.microsoft.com/support/faq/). A HDInsight szolgáltatás további szintű támogatást biztosít a beépített összetevőkhöz.

A HDInsight szolgáltatásban kétféle nyílt forráskódú összetevő érhető el:

* **Beépített összetevők** – ezek az összetevők előre telepítve vannak a HDInsight-fürtökön, és alapvető funkciókat biztosítanak a fürt számára. Például Apache Hadoop a fonal Resource Manager, a Apache Hive lekérdezési nyelv (HiveQL) és a Mahout könyvtár ehhez a kategóriához tartozik. A fürt összetevőinek teljes listája a [HDInsight által biztosított Apache Hadoop-fürt verziójának újdonságai](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)című részében érhető el.
* **Egyéni összetevők** – a fürt felhasználója a munkaterhelésen belül bármely, a Közösségben elérhető vagy Ön által létrehozott összetevő használatával telepítheti vagy használhatja a számítási feladatot.

> [!IMPORTANT]
> A HDInsight-fürthöz biztosított összetevők teljes mértékben támogatottak. Microsoft ügyfélszolgálata segít elkülöníteni és elhárítani ezeket az összetevőket érintő problémákat.
>
> Az egyéni összetevők kereskedelmileg ésszerű támogatást kapnak a probléma további megoldásához. A Microsoft támogatási szolgálata megoldhatja a problémát, vagy megkérheti, hogy a nyílt forráskódú technológiák számára elérhető csatornákat adjon meg, ahol az adott technológia mélyreható szaktudása található. Többek között számos közösségi webhely használható, például a [következőhöz: HDInsight MSDN-fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) [https://stackoverflow.com](https://stackoverflow.com). Emellett az Apache-projektek [https://apache.org on ](https://apache.org)is rendelkeznek projekt-webhelyekkel, például: [Hadoop](https://hadoop.apache.org/).

## <a name="understand-default-python-installation"></a>A Python alapértelmezett telepítésének ismertetése

A HDInsight Spark-fürt az anaconda telepítésével jön létre. Két Python-telepítés van a fürtben, az anaconda Python 2,7 és a Python 3,5. Az alábbi táblázat a Spark, a Livy és a Jupyter alapértelmezett Python-beállításait mutatja be.

| |Python 2.7|Python 3,5|
|----|----|----|
|Útvonal|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark|Alapértelmezett érték 2,7|–|
|Livy|Alapértelmezett érték 2,7|–|
|Jupyter|PySpark kernel|PySpark3 kernel|

## <a name="safely-install-external-python-packages"></a>Külső Python-csomagok biztonságos telepítése

A HDInsight-fürt a Python 2,7 és a Python 3,5 beépített Python-környezettől függ. Ha közvetlenül telepíti az egyéni csomagokat az alapértelmezett beépített környezetekben, előfordulhat, hogy a függvénytár-verziók váratlanul módosulnak, és tovább bontják a fürtöt. A Spark-alkalmazásokhoz tartozó egyéni külső Python-csomagok biztonságos telepítéséhez kövesse az alábbi lépéseket.

1. Python virtuális környezet létrehozása a Conda használatával. A virtuális környezetek elkülönített lemezterületet biztosítanak a projektekhez, és nem kell másokat feltörni. A Python virtuális környezet létrehozásakor megadhatja a használni kívánt Python-verziót. Ne feledje, hogy a Python 2,7 és a 3,5 használatához még a virtuális környezet létrehozása is szükséges. Ezzel a beállítással meggyőződhet arról, hogy a fürt alapértelmezett környezete nem szakad meg. Hozzon létre parancsfájl-műveleteket a fürtön az alábbi szkripttel rendelkező összes csomóponton egy Python virtuális környezet létrehozásához. 

    -   `--prefix` megadja azt az elérési utat, ahol a Conda virtuális környezete él. Az itt megadott elérési út alapján több konfigurációt is meg kell változtatni. Ebben a példában a py35new használjuk, mivel a fürtön már létezik egy py35 nevű meglévő virtuális környezet.
    -   `python=` megadja a virtuális környezet Python-verzióját. Ebben a példában az 3,5-es verziót használjuk, amely megegyezik a fürt beépített verziójával. A virtuális környezet létrehozásához más Python-verziókat is használhat.
    -   `anaconda` megadja az anaconda-csomagok virtuális környezetben való telepítéséhez szükséges package_spec.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes 
    ```

2. Szükség esetén telepítsen külső Python-csomagokat a létrehozott virtuális környezetbe. A külső Python-csomagok telepítéséhez futtassa parancsfájl-műveleteket a fürtön az alábbi szkripttel rendelkező összes csomóponthoz. Ahhoz, hogy fájlokat lehessen írni a virtuális környezet mappájába, a sudo jogosultsággal kell rendelkeznie.

    A [csomag indexében](https://pypi.python.org/pypi) a rendelkezésre álló csomagok teljes listáját is megkeresheti. Lekérheti az egyéb forrásokból származó elérhető csomagok listáját is. Telepítheti például a [Conda-Forge](https://conda-forge.org/feedstocks/)használatával elérhetővé tett csomagokat.

    -   `seaborn` a telepíteni kívánt csomag neve.
    -   `-n py35new` adja meg az imént létrehozott virtuális környezet nevét. Ügyeljen arra, hogy a virtuális környezet létrehozása alapján megfelelően módosítsa a nevet.

    ```bash
    sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
    ```

    Ha nem ismeri a virtuális környezet nevét, az SSH-t használhatja a fürt fő csomópontjára, és `/usr/bin/anaconda/bin/conda info -e` futtathatja az összes virtuális környezet megjelenítéséhez.

3. Módosítsa a Spark és a Livy konfigurációját, és mutasson a létrehozott virtuális környezetre.

    1. Nyissa meg a Ambari felhasználói felületét, lépjen a Spark2 lapra, és válassza a konfigurációk lapot.
    
        ![A Spark és a Livy konfiguráció módosítása a Ambari használatával](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)
 
    2. Bontsa ki a speciális livy2-env elemet, majd a lenti utasításokat. Ha egy másik előtaggal telepítette a virtuális környezetet, módosítsa az elérési utat megfelelően.

        ```
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![Livy-konfiguráció módosítása a Ambari használatával](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. Bontsa ki a speciális spark2 – env elemet, cserélje le a meglévő export PYSPARK_PYTHON utasítást alulra. Ha egy másik előtaggal telepítette a virtuális környezetet, módosítsa az elérési utat megfelelően.

        ```
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![Spark-konfiguráció módosítása a Ambari használatával](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. Mentse a módosításokat, és indítsa újra az érintett szolgáltatásokat. Ezeknek a változásoknak a Spark2 szolgáltatás újraindítására van szükségük. A Ambari felhasználói felülete kérni fogja a szükséges újraindítási emlékeztetőt, majd kattintson az Újraindítás gombra az összes érintett szolgáltatás újraindításához.

        ![Spark-konfiguráció módosítása a Ambari használatával](./media/apache-spark-python-package-installation/ambari-restart-services.png)
 
4.  Ha az újonnan létrehozott virtuális környezetet szeretné használni a Jupyter-on. Módosítania kell a Jupyter-konfigurációkat, és újra kell indítania a Jupyter. Futtasson parancsfájl-műveleteket minden olyan fejléc-csomóponton az alábbi utasítással, hogy az új, létrehozott virtuális környezethez Jupyter. Ügyeljen arra, hogy módosítsa a virtuális környezethez megadott előtag elérési útját. A parancsfájl futtatása után indítsa újra a Jupyter szolgáltatást a Ambari felhasználói felületén, hogy elérhetővé tegye ezt a módosítást.

    ```
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    Az alábbi kód futtatásával megerősítheti Jupyter Notebook Python-környezetét:

    ![Python-verzió keresése Jupyter Notebook](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>Ismert probléma

A Anaconda 4.7.11 és 4.7.12-verziójának ismert hibája van. Ha a parancsfájl műveletei a `"Collecting package metadata (repodata.json): ...working..."`on, és a `"Python script has been killed due to timeout after waiting 3600 secs"`nem sikerül. [Ezt a parancsfájlt](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) letöltheti, és parancsfájl-műveletekként futtathatja az összes csomóponton a probléma megoldásához.

A anaconda verziójának megkereséséhez az SSH-t használhatja a fürt fejlécének csomópontjára, és futtathatja `/usr/bin/anaconda/bin/conda --v`.

## <a name="seealso"></a>Lásd még:

* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Alkalmazási helyzetek

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
