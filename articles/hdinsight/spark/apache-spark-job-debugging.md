---
title: Az Azure HDInsight futó Apache Spark-feladatok hibakeresése
description: Az Azure HDInsight-beli Spark-fürtön futó feladatok nyomon követése és hibakeresése a fonal felhasználói felület, a Spark felhasználói felület és a Spark-előzmények kiszolgáló használatával
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 110a8e86fc1916254ab914630ce10d2b7ae073b7
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74775333"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Az Azure HDInsight futó Apache Spark-feladatok hibakeresése

Ebből a cikkből megtudhatja, hogyan követheti nyomon és hibakeresést [Apache Spark](https://spark.apache.org/) a HDInsight-fürtökön futó feladatokat a [Apache Hadoop a fonal](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) felhasználói felületén, a Spark felhasználói felületen és a Spark-előzmények kiszolgálóján keresztül. Egy Spark-feladatot a Spark-fürttel elérhető jegyzetfüzettel indíthat el, **gépi tanulás: az élelmiszer-ellenőrzési adatok prediktív elemzése a MLLib használatával**. A következő lépésekkel követheti nyomon a bármely más megközelítéssel elküldött alkalmazást, például a **Spark-submitt**is.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

* Meg kell kezdenie a jegyzetfüzet futtatását, **[gépi tanulás: prediktív elemzés az élelmiszer-ellenőrzési adataihoz a MLLib használatával](apache-spark-machine-learning-mllib-ipython.md)** . A jegyzetfüzet futtatásával kapcsolatos utasításokért kövesse a hivatkozást.  

## <a name="track-an-application-in-the-yarn-ui"></a>Alkalmazás nyomon követése a fonal felhasználói felületén

1. Indítsa el a fonal felhasználói felületét. Válasszon **fonalat** a **fürt irányítópultok**területen.

    ![Azure Portal a fonal felhasználói felületének indítása](./media/apache-spark-job-debugging/launch-apache-yarn-ui.png)

   > [!TIP]  
   > Azt is megteheti, hogy a Ambari felhasználói felületéről is elindítja a fonal felhasználói felületét. A Ambari felhasználói felületének elindításához válassza a **Ambari Kezdőlap** lehetőséget a **fürt irányítópultok**területen. A Ambari felhasználói felületén navigáljon a **fonal** > **Gyorshivatkozások** > az Active Resource Manager > **Resource Manager felhasználói felületén**.

2. Mivel a Spark-feladatot a Jupyter-jegyzetfüzetek használatával indította el, az alkalmazás neve **remotesparkmagics** (a jegyzetfüzetekről indított összes alkalmazás neve). A feladattal kapcsolatos további információk megtekintéséhez válassza ki az alkalmazás AZONOSÍTÓját az alkalmazás nevében. Ez elindítja az alkalmazás nézetét.

    ![Spark-előzmények kiszolgálója Spark-alkalmazás AZONOSÍTÓjának keresése](./media/apache-spark-job-debugging/find-application-id1.png)

    A Jupyter-jegyzetfüzetekről indított alkalmazások esetében az állapot mindig **fut** , amíg ki nem lép a jegyzetfüzetből.

3. Az alkalmazás nézetből részletesebben is megtudhatja, hogy az alkalmazáshoz és a naplókhoz társított tárolók (StdOut/stderr) találhatók-e. A Spark felhasználói felületet úgy is elindíthatja, hogy a **nyomkövetési URL-címhez**tartozó hivatkozásra kattint az alább látható módon.

    ![A Spark History Server letöltési tárolójának naplói](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Alkalmazás követése a Spark felhasználói felületén

A Spark felhasználói felületén megtekintheti azokat a Spark-feladatokat, amelyeket a korábban elindított alkalmazás szül.

1. A Spark felhasználói felület elindításához az alkalmazás nézetből válassza ki a **nyomkövetési URL-címre**mutató hivatkozást, ahogy az a fenti képernyőfelvételen látható. A Jupyter notebookon futó alkalmazás által indított összes Spark-feladat megtekinthető.

    ![Spark History Server-feladatok lap](./media/apache-spark-job-debugging/view-apache-spark-jobs.png)

2. Válassza a **végrehajtók** fület az egyes végrehajtók feldolgozási és tárolási adatainak megtekintéséhez. A hívási verem lekéréséhez válassza a **szál memóriaképe** hivatkozást.

    ![Spark History Server-végrehajtók lap](./media/apache-spark-job-debugging/view-spark-executors.png)

3. A **szakaszok** lapon megtekintheti az alkalmazáshoz társított szakaszokat.

    ![A Spark History Server szakaszai lap](./media/apache-spark-job-debugging/view-apache-spark-stages.png "Spark-szakaszok megtekintése")

    Az egyes szakaszok több feladattal is rendelkezhetnek, amelyekhez a végrehajtás statisztikáit tekintheti meg, például alább látható.

    ![A Spark History Server szakaszai lap részletei](./media/apache-spark-job-debugging/view-spark-stages-details.png "Spark-szakaszok részleteinek megtekintése")

4. A fázis részletei lapon a DAG vizualizációt indíthatja el. Bontsa ki a **Dag vizualizáció** hivatkozását az oldal tetején, az alábbi ábrán látható módon.

    ![Spark-szakaszok megtekintése – DAG vizualizáció](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)

    A DAG vagy a Direct Aclyic gráf az alkalmazás különböző szakaszait jelöli. A gráf minden kék mezője az alkalmazásból meghívott Spark-műveletet jelöli.

5. A szakasz részletei lapon elindíthatja az alkalmazás ütemtervének nézetét is. Bontsa ki az **esemény idővonalának** hivatkozását az oldal tetején, az alábbi ábrán látható módon.

    ![Spark-szakaszok esemény idővonalának megtekintése](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)

    Ez megjeleníti a Spark-eseményeket idősor formájában. Az Idősor nézet három szinten, a feladatok között, a feladatokon belül és egy fázison belül érhető el. A fenti kép rögzíti egy adott fázis idővonal-nézetét.

   > [!TIP]  
   > Ha bejelöli a **Nagyítás engedélyezése** jelölőnégyzetet, a bal oldali és a jobb oldali görgetést is görgetheti az idősor nézetben.

6. A Spark felhasználói felületének többi lapja is hasznos információkat nyújt a Spark-példányról.

   * Storage lap – ha az alkalmazás létrehoz egy RDD, a tárolás lapon talál információt ezekről.
   * Környezet lap – ezen a lapon hasznos információkat olvashat a Spark-példányról, például:
     * Scala verziója
     * A fürthöz társított Eseménynapló-könyvtár
     * Az alkalmazás végrehajtó magjainak száma
     * Stb.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>A befejezett feladatokkal kapcsolatos információk megkeresése a Spark History Server használatával

A feladatok elvégzése után a rendszer megőrzi a feladattal kapcsolatos információkat a Spark History-kiszolgálón.

1. A Spark-előzmények kiszolgálójának elindításához az **Áttekintés** lapon válassza a **Spark History Server** elemet a **fürt irányítópultok**területen.

    ![Azure Portal a Spark-előzmények kiszolgálójának elindítása](./media/apache-spark-job-debugging/launch-spark-history-server.png "A Spark History Kiszolgáló1 elindítása")

   > [!TIP]  
   > Azt is megteheti, hogy a Spark History Server felhasználói felületét is elindítja a Ambari felhasználói felületén. A Ambari felhasználói felületének elindításához az Áttekintés panelen válassza a **Ambari Kezdőlap** elemet a **fürt irányítópultok**területen. A Ambari felhasználói felületén navigáljon a **Spark2** > **Gyorshivatkozások** > **Spark2 History Server felhasználói felületéhez**.

2. Megjelenik a felsorolt összes befejezett alkalmazás. Válassza ki az alkalmazás AZONOSÍTÓját, hogy további információkat adjon meg egy alkalmazásban.

    ![A Spark History Server által befejezett alkalmazások](./media/apache-spark-job-debugging/view-completed-applications.png "A Spark History Kiszolgáló2 elindítása")

## <a name="see-also"></a>Lásd még:

* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark feladatok hibakeresése kiterjesztett Spark History Server használatával](apache-azure-spark-history-server.md)

### <a name="for-data-analysts"></a>Adatelemzők számára

* [Apache Spark a Machine Learning használatával: a Spark in HDInsight használata az építési hőmérséklet elemzésére a HVAC-adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learning használatával: az élelmiszer-ellenőrzési eredmények előrejelzéséhez használja a Spark in HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Webhely-naplózási elemzés Apache Spark használatával a HDInsight-ben](apache-spark-custom-library-website-log-analysis.md)
* [Az Application Insight telemetria-adatelemzési szolgáltatásának használata a HDInsight-ben Apache Spark](apache-spark-analyze-application-insight-logs.md)
* [A Cafe on Azure HDInsight Spark használata elosztott mély tanuláshoz](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>Spark-fejlesztőknek

* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata a Apache Spark alkalmazások távoli hibakereséséhez](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin notebookok használata Apache Spark-fürttel a HDInsight-on](apache-spark-zeppelin-notebook.md)
* [Jupyter notebookokhoz elérhető kernelek Apache Spark-fürtben HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)
