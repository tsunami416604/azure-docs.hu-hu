---
title: Azure HDInsighton futó Apache Spark-feladatok hibakeresése
description: A YARN felhasználói felület, a Spark felhasználói felülete és a Spark History kiszolgáló használatával nyomon követheti és debugolhatják az Azure HDInsight Spark-fürtjein futó feladatokat
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: bcf2f97e855126c86dbb1d74cd430704e2af3af1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932138"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Azure HDInsighton futó Apache Spark-feladatok hibakeresése

Ebben a cikkben megtudhatja, hogyan követheti és debug [Apache Spark-feladatok](https://spark.apache.org/) futó HDInsight-fürtök az [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) ui, Spark UI és a Spark History Server használatával. Spark-feladatot indít el a Spark-fürt, **a Gépi tanulás: Az élelmiszer-vizsgálati adatok prediktív elemzése**az MLLib használatával elérhető jegyzetfüzet használatával. A következő lépésekkel nyomon követheti a más megközelítéssel beküldött alkalmazásokat is, például **a spark-submit**.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

* Meg kellett volna kezdeni e notebook, **[Machine learning: Prediktív elemzés az élelmiszer-vizsgálati adatok MLLib](apache-spark-machine-learning-mllib-ipython.md)** használatával . A jegyzetfüzet futtatásával kapcsolatos tudnivalókért kövesse a hivatkozást.  

## <a name="track-an-application-in-the-yarn-ui"></a>Alkalmazás nyomon követése a YARN felhasználói felületen

1. Indítsa el a YARN felhasználói felületét. Válassza **a Fonal** lehetőséget a **Fürtirányítópultok csoportban.**

    ![Az Azure Portal elindítja a YARN felhasználói felületét](./media/apache-spark-job-debugging/launch-apache-yarn-ui.png)

   > [!TIP]  
   > Másik lehetőségként az Ambari felhasználói felületéről is elindíthatja a YARN felhasználói felületet. Az Ambari felhasználói felület elindításához válassza az **Ambari home** lehetőséget a **Fürt irányítópultjai**csoportban. Az Ambari felhasználói felületén keresse meg a **YARN** > **gyorshivatkozások at,** > az aktív Erőforrás-kezelő > **erőforrás-kezelő felhasználói felületét.**

2. Mivel a Spark-feladat jupyter-jegyzetfüzetek használatával kezdte el, az alkalmazás a **name remotesparkmagics** (ez a neve az összes alkalmazás, amely a notebookok indított). Válassza ki az alkalmazás azonosítóját az alkalmazás nevéhez, hogy további információt kapjon a feladatról. Ezzel elindítja az alkalmazásnézetet.

    ![Spark-előzménykiszolgáló Spark-alkalmazásazonosítójának keresése](./media/apache-spark-job-debugging/find-application-id1.png)

    A Jupyter-jegyzetfüzetekből indított ilyen alkalmazások esetén az állapot mindig **fut,** amíg ki nem lép a jegyzetfüzetből.

3. Az alkalmazás nézetben tovább részletezheti az alkalmazáshoz társított tárolók és a naplók (stdout/stderr) tovább. A Spark felhasználói felületét a **követési URL-nek**megfelelő hivatkozásra kattintva is elindíthatja, ahogy az alábbiakban látható.

    ![Spark-előzmények kiszolgáló letöltési tárolónaplói](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Alkalmazás nyomon követése a Spark felhasználói felületén

A Spark felhasználói felületén leáshat a Spark-feladatok, amelyek a korábban elindított alkalmazás által kiváltott.

1. A Spark felhasználói felületének elindításához az alkalmazásnézetből válassza ki a **követési URL-címhez**mutató hivatkozást, ahogy az a fenti képernyőfelvételen látható. Láthatja az összes Spark-feladatok, amelyek a Jupyter-jegyzetfüzetben futó alkalmazás által indított összes.

    ![Spark-előzmények kiszolgálói feladatok lapja](./media/apache-spark-job-debugging/view-apache-spark-jobs.png)

2. Válassza a Végrehajtók lapot az egyes **végrehajtók** feldolgozási és tárolási adatainak megtekintéséhez. A hívásverem a **szálkiírási** hivatkozás kiválasztásával is beolvasható.

    ![A Spark-előzmények kiszolgálóinak végrehajtói lapja](./media/apache-spark-job-debugging/view-spark-executors.png)

3. A **Szakaszok** lapon megtekintheti az alkalmazáshoz társított szakaszokat.

    ![A Spark előzményeikiszolgáló szakaszai lap](./media/apache-spark-job-debugging/view-apache-spark-stages.png "Spark-szakaszok megtekintése")

    Minden szakasznak több feladata is lehet, amelyekhez megtekintheti a végrehajtási statisztikákat, mint például az alábbiakban látható.

    ![A Spark-előzmények kiszolgálójának szakaszainak lapja részletei](./media/apache-spark-job-debugging/view-spark-stages-details.png "A Spark-szakaszok részleteinek megtekintése")

4. A színpad részletei oldalon elindíthatja a DAG Visualization-t. Bontsa ki a **DAG Visualization** hivatkozást az oldal tetején, az alábbiak szerint.

    ![A Spark fázisainak megtekintése DAG-megjelenítés](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)

    A DAG vagy a Direct Aclyic Graph az alkalmazás különböző szakaszait jelöli. A diagram minden kék mezője egy Spark-műveletet jelöl az alkalmazásból.

5. A színpad részletei lapon elindíthatja az alkalmazás idővonal nézetét is. Bontsa ki az **Esemény idővonala** hivatkozást az oldal tetején, az alábbiak szerint.

    ![A Spark-szakaszok eseményütemtervének megtekintése](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)

    Ez a Spark-eseményeket egy idővonal formájában jeleníti meg. Az idővonal nézet három szinten érhető el, a feladatok között, a feladaton belül és egy szakaszon belül. A fenti kép rögzíti egy adott szakasz idővonalnézetét.

   > [!TIP]  
   > Ha bejelöli a **Nagyítás engedélyezése jelölőnégyzetet,** balra és jobbra görgethet az idővonal nézetben.

6. A Spark felhasználói felületének más lapjai is hasznos információkat nyújtanak a Spark-példányról.

   * Tárolás lap – Ha az alkalmazás rdd-t hoz létre, a Tárolás lapon talál információkat ezekről.
   * Környezet lap – Ez a lap hasznos információkat tartalmaz a Spark-példányról, például a következőkről:
     * Scala verzió
     * A fürthöz társított eseménynapló-könyvtár
     * Az alkalmazás végrehajtó magjainak száma
     * Etc.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>A befejezett feladatokról a Spark Előzmények kiszolgálóhasználatával kapcsolatos információk keresése

A feladat befejezése után a feladatra vonatkozó információk megmaradnak a Spark-előzmények kiszolgálón.

1. A Spark-előzmények kiszolgáló elindításához az **Áttekintés** lapon válassza a **Spark-előzmények kiszolgáló** ját a **Fürtirányítópultok**csoportban.

    ![Az Azure Portal elindítja a Spark előzménykiszolgálóját](./media/apache-spark-job-debugging/launch-spark-history-server.png "A Spark-előzmények kiszolgálójának indítása1")

   > [!TIP]  
   > Azt is megteheti, hogy elindítja a Spark History Server felhasználói felületét az Ambari felhasználói felületéről. Az Ambari felhasználói felület elindításához az Áttekintés panelen válassza az **Ambari home** lehetőséget a **Fürt irányítópultjai**csoportban. Az Ambari felhasználói felületén keresse meg a **Spark2** > **Gyorshivatkozások** > **Spark2 Előzménykiszolgáló felhasználói felületét.**

2. Az összes befejezett alkalmazás megjelenik a listában. További információért válassza ki az alkalmazásazonosítót, amelyet le szeretne fúrni egy alkalmazásba.

    ![A Spark előzménykiszolgálója befejezte az alkalmazásokat](./media/apache-spark-job-debugging/view-completed-applications.png "A Spark-előzmények kiszolgálójának indítása2")

## <a name="see-also"></a>Lásd még

* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-feladatok hibakeresése a spark-előzmények kiszolgálójának használatával](apache-azure-spark-history-server.md)

### <a name="for-data-analysts"></a>Adatelemzőknek

* [Apache Spark gépi tanulással: Használja a Sparkot a HDInsightban az épület hőmérsékletének elemzéséhez a fűtés- és légtechnikai adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learningsegítségével: A Spark használata a HDInsightban az élelmiszer-ellenőrzési eredmények előrejelzéséhez](apache-spark-machine-learning-mllib-ipython.md)
* [Webhelynapló-elemzés az Apache Spark használatával a HDInsightban](apache-spark-custom-library-website-log-analysis.md)
* [Az Application Insight telemetriai adatok elemzése az Apache Spark használatával a HDInsightban](apache-spark-analyze-application-insight-logs.md)


### <a name="for-spark-developers"></a>Spark-fejlesztőknek

* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight eszközök beépülő moduljával távolról debugelje az Apache Spark alkalmazásokat](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin-jegyzetfüzetek használata Apache Spark-fürttel a HDInsighton](apache-spark-zeppelin-notebook.md)
* [A Jupyter notebookhoz elérhető kernelek az Apache Spark-fürtHDInsighthoz](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)
