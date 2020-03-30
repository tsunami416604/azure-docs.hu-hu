---
title: Parancsfájl-művelet A Python-csomagok jupyter az Azure HDInsight
description: A parancsfájlművelet használatával konfigurálhatja a HDInsight Spark-fürtökkel elérhető Jupyter-jegyzetfüzeteket a külső python-csomagok használatára.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 659af8b85cb3736d663e79676b04af8041aeabfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129602"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Python-környezet biztonságos kezelése az Azure HDInsightban szkriptműveletekkel

> [!div class="op_single_selector"]
> * [A sejtmágia használata](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Parancsfájl-művelet használata](apache-spark-python-package-installation.md)

A HDInsight két beépített Python-telepítéssel rendelkezik a Spark-fürtben, az Anaconda Python 2.7-ben és a Python 3.5-ben. Bizonyos esetekben az ügyfeleknek testre kell szabniuk a Python-környezetet, például külső Python-csomagok vagy egy másik Python-verzió telepítését. Ebben a cikkben bemutatjuk az ajánlott eljárás a Python-környezetek biztonságos kezelése egy [Apache Spark-fürt](./apache-spark-overview.md) HDInsight.In this article, we be a best practice for safely of a

## <a name="prerequisites"></a>Előfeltételek

Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md). Ha még nem rendelkezik egy Spark-fürt hdinsight, parancsfájlműveletek et futtathat a fürt létrehozása során. Keresse fel az [egyéni parancsfájlműveletek használatáról szóló dokumentációt.](../hdinsight-hadoop-customize-cluster-linux.md)

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>A HDInsight-fürtökön használt nyílt forráskódú szoftverek támogatása

A Microsoft Azure HDInsight szolgáltatás az Apache Hadoop körül kialakított nyílt forráskódú technológiák ökoszisztémáját használja. A Microsoft Azure általános szintű támogatást nyújt a nyílt forráskódú technológiákhoz. További információ: [Azure Support GYQ website](https://azure.microsoft.com/support/faq/). A HDInsight szolgáltatás további támogatási szintet biztosít a beépített összetevők számára.

A HDInsight szolgáltatásban kétféle nyílt forráskódú összetevő érhető el:

|Összetevő |Leírás |
|---|---|
|Beépített|Ezek az összetevők előre telepítve vannak a HDInsight-fürtökön, és a fürt alapvető funkcióit biztosítják. Például az Apache Hadoop YARN Resource Manager, az Apache Hive lekérdezési nyelv (HiveQL) és a Mahout-kódtár ebbe a kategóriába tartozik. A fürtösszetevők teljes listája a [HDInsight által biztosított Apache Hadoop fürtverziók újdonságai között](../hdinsight-component-versioning.md)érhető el.|
|Egyéni|Ön, mint a fürt felhasználója, telepítheti vagy használhatja a munkaterhelésben a közösségben elérhető vagy Ön által létrehozott bármely összetevőt.|

> [!IMPORTANT]
> A HDInsight-fürthöz mellékelt összetevők teljes mértékben támogatottak. A Microsoft támogatási szolgálata segít az összetevőkkel kapcsolatos problémák elkülönítéséhez és megoldásához.
>
> Az egyéni összetevők üzletileg ésszerű támogatást kapnak a probléma további elhárításához. A Microsoft támogatási szolgálata meg oldhatja a problémát, vagy megkérhetik, hogy vegyen részt a nyílt forráskódú technológiák elérhető csatornáival, ahol az adott technológiával kapcsolatos mély szakértelem található. Például számos közösségi webhely használható, például: [MSDN fórum a HDInsighthoz](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Szintén Apache projektek projekt [https://apache.org](https://apache.org)oldalak , például: [Hadoop](https://hadoop.apache.org/).

## <a name="understand-default-python-installation"></a>Az alapértelmezett Python-telepítés ismertetése

A HDInsight Spark-fürt az Anaconda telepítésével jön létre. A fürtben két Python-telepítés található, az Anaconda Python 2.7 és a Python 3.5. Az alábbi táblázat a Spark, a Livy és a Jupyter alapértelmezett Python-beállításait mutatja be.

| |Python 2,7|Python 3,5|
|----|----|----|
|Útvonal|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark|Az alapértelmezett érték 2,7|N/A|
|Livy|Az alapértelmezett érték 2,7|N/A|
|Jupyter|PySpark kernel|PySpark3 kernel|

## <a name="safely-install-external-python-packages"></a>Külső Python-csomagok biztonságos telepítése

A HDInsight-fürt a beépített Python-környezettől függ, mind a Python 2.7,mind a Python 3.5-től. Az egyéni csomagok közvetlen telepítése ezekben az alapértelmezett beépített környezetekben váratlan könyvtárverzió-módosításokat okozhat, és tovább szakíthatja a fürtöt. Annak érdekében, hogy biztonságosan telepítheti az egyéni külső Python-csomagok at a Spark-alkalmazások, kövesse az alábbi lépéseket.

1. Hozzon létre Python virtuális környezetet conda használatával. A virtuális környezet elszigetelt helyet biztosít a projektek számára anélkül, hogy másokat megtörne. A Python virtuális környezet létrehozásakor megadhatja a használni kívánt python-verziót. Vegye figyelembe, hogy továbbra is létre kell hoznia a virtuális környezetet, még akkor is, ha a Python 2.7-et és a 3.5-öt szeretné használni. Ezzel győződjön meg arról, hogy a fürt alapértelmezett környezete nem sérül meg. Parancsfájlműveletek futtatása a fürtön az alábbi parancsfájlokkal rendelkező összes csomóponton egy Python virtuális környezet létrehozásához.

    -   `--prefix`olyan útvonalat határoz meg, ahol egy conda virtuális környezet él. Az itt megadott elérési út alapján több konfigurációt is meg kell változtatni. Ebben a példában a py35new-t használjuk, mivel a fürt rendelkezik egy meglévő virtuális környezettel, amelyet py35-nek hívnak.
    -   `python=`a virtuális környezet Python-verzióját adja meg. Ebben a példában a 3.5-ös verziót használjuk, ugyanazt a verziót, mint a beépített fürt. Más Python-verziók at is használhat a virtuális környezet létrehozásához.
    -   `anaconda`megadja az Anaconda csomagok virtuális környezetben való telepítéséhez package_spec.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes
    ```

2. Telepítse a külső Python-csomagokat a létrehozott virtuális környezetben, ha szükséges. Parancsfájlműveletek futtatása a fürtön az alábbi parancsfájlokkal rendelkező összes csomóponton a külső Python-csomagok telepítéséhez. Be kell, hogy sudo jogosultság itt annak érdekében, hogy írjon fájlokat a virtuális környezet mappába.

    A [csomagindexben](https://pypi.python.org/pypi) megkeresheti az elérhető csomagok teljes listáját. Más forrásokból is beszerezheti a rendelkezésre álló csomagok listáját. Telepítheti például a [conda-forge-on](https://conda-forge.org/feedstocks/)keresztül elérhetővé tett csomagokat.

    Az alábbi parancsot akkor használja, ha a legújabb verzióval rendelkező tárat szeretne telepíteni:

    - Conda csatorna használata:

        -   `seaborn`a telepíteni kívánt csomagnév.
        -   `-n py35new`adja meg a virtuális környezet nevét, amely csak jön létre. Győződjön meg arról, hogy a nevet ennek megfelelően módosítsa a virtuális környezet létrehozása alapján.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
        ```

    - Vagy használja PyPi repo, változás `seaborn` és `py35new` ennek megfelelően:
        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install seaborn
        ```

    Az alábbi parancsot akkor használja, ha egy adott verziójú tárat szeretne telepíteni:

    - Conda csatorna használata:

        -   `numpy=1.16.1`a telepíteni kívánt csomag neve és verziója.
        -   `-n py35new`adja meg a virtuális környezet nevét, amely csak jön létre. Győződjön meg arról, hogy a nevet ennek megfelelően módosítsa a virtuális környezet létrehozása alapján.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install numpy=1.16.1 -n py35new --yes
        ```

    - Vagy használja PyPi repo, változás `numpy==1.16.1` és `py35new` ennek megfelelően:

        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install numpy==1.16.1
        ```

    ha nem ismeri a virtuális környezet nevét, ssh-t a fürt fő `/usr/bin/anaconda/bin/conda info -e` csomópontjára futtathat, és futtathatja az összes virtuális környezet megjelenítéséhez.

3. Módosítsa a Spark és a Livy konfigurációkat, és mutasson a létrehozott virtuális környezetre.

    1. Nyissa meg az Ambari felhasználói felületet, nyissa meg a Spark2 lapot, a Configs lapot.

        ![Változás Spark és Livy config keresztül Ambari](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)

    2. Bontsa ki az Advanced livy2-env-t, adja hozzá az alábbi állításokat az alján. Ha a virtuális környezetet más előtaggal telepítette, ennek megfelelően módosítsa az elérési utat.

        ```
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![Változás Livy config keresztül Ambari](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. Bontsa ki a Speciális szikra2-env csomópontot, és cserélje le a meglévő exportálási PYSPARK_PYTHON utasítást. Ha a virtuális környezetet más előtaggal telepítette, ennek megfelelően módosítsa az elérési utat.

        ```
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![Spark-konfiguráció módosítása az Ambari-n keresztül](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. Mentse a módosításokat, és indítsa újra az érintett szolgáltatásokat. Ezek a módosítások a Spark2 szolgáltatás újraindítását kell igényelni. Az Ambari felhasználói felülete kérni fogja a szükséges újraindítási emlékeztetőt, majd az Újraindítás gombra kattintva indítsa újra az összes érintett szolgáltatást.

        ![Spark-konfiguráció módosítása az Ambari-n keresztül](./media/apache-spark-python-package-installation/ambari-restart-services.png)

4. Ha szeretné használni az új létrehozott virtuális környezet jupyter. Meg kell változtatni Jupyter configs és indítsa újra Jupyter. Parancsfájlműveletek futtatása az összes fejléccsomóponton az alábbi utasítással, hogy a Jupyter az új létrehozott virtuális környezetre mutasson. Győződjön meg arról, hogy módosítja a virtuális környezetben megadott előtag elérési útját. A parancsfájlművelet futtatása után indítsa újra a Jupyter szolgáltatást az Ambari felhasználói felületén keresztül, hogy elérhetővé tegye ezt a módosítást.

    ```bash
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    A Jupyter Notebook python-környezetének dupla megerősítését az alábbi kód futtatásával erősítheti meg:

    ![Python-verzió ellenőrzése a Jupyter notebookban](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>Ismert probléma

Van egy ismert hiba az Anaconda 4.7.11, 4.7.12 és 4.8.0 verziójához. Ha látja, hogy a `"Collecting package metadata (repodata.json): ...working..."` parancsfájlműveletek `"Python script has been killed due to timeout after waiting 3600 secs"`lógnak és sikertelenek a segítségével. Letöltheti [ezt a parancsfájlt,](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) és futtathatja parancsfájlműveletekként az összes csomóponton a probléma megoldásához.

Az Anaconda verziójának ellenőrzéséhez ssh-t futtathat `/usr/bin/anaconda/bin/conda --v`a fürt fejléccsomójára, és futtathatja a futtassa.

## <a name="next-steps"></a>További lépések

* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)
* [Apache Spark bi-val: Interaktív adatelemzés a Spark használatával a HDInsightban az üzletiintelligencia-eszközökkel](apache-spark-use-bi-tools.md)
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
