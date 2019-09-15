---
title: Az Azure HDInsight futó Apache Spark-feladatok hibakeresése
description: Az Azure HDInsight-beli Spark-fürtön futó feladatok nyomon követése és hibakeresése a fonal felhasználói felület, a Spark felhasználói felület és a Spark-előzmények kiszolgáló használatával
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: hrasheed
ms.openlocfilehash: c71bf9be94799c2b109d57393e70e99a0710e252
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2019
ms.locfileid: "70995542"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Az Azure HDInsight futó Apache Spark-feladatok hibakeresése

Ebből a cikkből megtudhatja, hogyan követheti nyomon és hibakeresést [Apache Spark](https://spark.apache.org/) a HDInsight-fürtökön futó feladatokat a [Apache Hadoop a fonal](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) felhasználói felületén, a Spark felhasználói felületen és a Spark-előzmények kiszolgálóján keresztül. Egy Spark-feladatot a Spark-fürttel elérhető jegyzetfüzettel indíthat el **, gépi tanulás: Az élelmiszer-ellenőrzési adataival kapcsolatos prediktív elemzés**a MLLib használatával. A következő lépésekkel követheti nyomon a bármely más megközelítéssel elküldött alkalmazást, például a **Spark-submitt**is.

## <a name="prerequisites"></a>Előfeltételek
A következőkkel kell rendelkeznie:

* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).
* A jegyzetfüzet  **[futtatásának megkezdése előtt a Machine learning: Az élelmiszer-ellenőrzési adataival kapcsolatos prediktív elemzés](apache-spark-machine-learning-mllib-ipython.md)a MLLib**használatával. A jegyzetfüzet futtatásával kapcsolatos utasításokért kövesse a hivatkozást.  

## <a name="track-an-application-in-the-yarn-ui"></a>Alkalmazás nyomon követése a fonal felhasználói felületén
1. Indítsa el a fonal felhasználói felületét. Kattintson a **szál** elemre a **fürt irányítópultok**területen.
   
    ![A fonal felhasználói felületének indítása](./media/apache-spark-job-debugging/launch-apache-yarn-ui.png)
   
   > [!TIP]  
   > Azt is megteheti, hogy a Ambari felhasználói felületéről is elindítja a fonal felhasználói felületét. A Ambari felhasználói felületének elindításához kattintson a **Ambari** elemre a **fürt irányítópultok**területén. A Ambari felhasználói felületén kattintson a **fonal**elemre, kattintson a **Gyorshivatkozások**lehetőségre, majd az Active Resource Manager elemre, végül pedig a **Resource Manager felhasználói felület**elemre. 

2. Mivel a Spark-feladatot a Jupyter-jegyzetfüzetek használatával indította el, az alkalmazás neve **remotesparkmagics** (a jegyzetfüzetekről indított összes alkalmazás neve). Az alkalmazás nevére kattintva további információkat kaphat a feladatról. Ez elindítja az alkalmazás nézetét.
   
    ![Spark-alkalmazás AZONOSÍTÓjának megkeresése](./media/apache-spark-job-debugging/find-application-id1.png)
   
    A Jupyter-jegyzetfüzetekről indított alkalmazások esetében az állapot mindig **fut** , amíg ki nem lép a jegyzetfüzetből.
3. Az alkalmazás nézetből részletesebben is megtudhatja, hogy az alkalmazáshoz és a naplókhoz társított tárolók (StdOut/stderr) találhatók-e. A Spark felhasználói felületet úgy is elindíthatja, hogy a **nyomkövetési URL-címhez**tartozó hivatkozásra kattint az alább látható módon. 
   
    ![Tárolónaplók letöltése](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Alkalmazás követése a Spark felhasználói felületén
A Spark felhasználói felületén megtekintheti azokat a Spark-feladatokat, amelyeket a korábban elindított alkalmazás szül.

1. A Spark felhasználói felület indításához az alkalmazás nézetből kattintson a **nyomkövetési URL-címre**mutató hivatkozásra, ahogy az a fenti képernyőfelvételen látható. A Jupyter notebookon futó alkalmazás által indított összes Spark-feladat megtekinthető.
   
    ![Spark-feladatok megtekintése](./media/apache-spark-job-debugging/view-apache-spark-jobs.png)
2. Kattintson a **végrehajtók** lapra az egyes végrehajtók feldolgozási és tárolási adatainak megtekintéséhez. A hívási verem lekéréséhez kattintson a **szál kiírására** szolgáló hivatkozásra.
   
    ![Spark-végrehajtók megtekintése](./media/apache-spark-job-debugging/view-spark-executors.png)
3. A **szakaszok** lapra kattintva megtekintheti az alkalmazáshoz társított szakaszokat.
   
    ![Spark-szakaszok megtekintése](./media/apache-spark-job-debugging/view-apache-spark-stages.png "Spark-szakaszok megtekintése")
   
    Az egyes szakaszok több feladattal is rendelkezhetnek, amelyekhez a végrehajtás statisztikáit tekintheti meg, például alább látható.
   
    ![Spark-szakaszok részleteinek megtekintése](./media/apache-spark-job-debugging/view-spark-stages-details.png "Spark-szakaszok részleteinek megtekintése") 
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
   * Környezet lap – ezen a lapon számos hasznos információ olvasható a Spark-példányról, például a 
     * Scala verziója
     * A fürthöz társított Eseménynapló-könyvtár
     * Az alkalmazás végrehajtó magjainak száma
     * Etc.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>A befejezett feladatokkal kapcsolatos információk megkeresése a Spark History Server használatával
A feladatok elvégzése után a rendszer megőrzi a feladattal kapcsolatos információkat a Spark History-kiszolgálón.

1. A Spark-előzmények kiszolgálójának elindításához az Áttekintés panelen kattintson a **Spark History-kiszolgáló** elemre a **fürt irányítópultok**területen.
   
    A ![Spark History Kiszolgáló1 elindítása] A (./media/apache-spark-job-debugging/launch-spark-history-server.png "Spark History Kiszolgáló1 elindítása")
   
   > [!TIP]  
   > Azt is megteheti, hogy a Spark History Server felhasználói felületét is elindítja a Ambari felhasználói felületén. A Ambari felhasználói felületének elindításához az Áttekintés panelen kattintson a **Ambari** elemre a **fürt irányítópultok**területen. A Ambari felhasználói felületén kattintson a **Spark**elemre, majd a **Gyorshivatkozások**elemre, végül pedig a **Spark History Server felhasználói felület**elemre.

2. Megjelenik a felsorolt összes befejezett alkalmazás. További információért kattintson egy alkalmazás-AZONOSÍTÓra az alkalmazásban való részletezéshez.
   
    A ![Spark History Kiszolgáló2 elindítása] A (./media/apache-spark-job-debugging/view-completed-applications.png "Spark History Kiszolgáló2 elindítása")

## <a name="see-also"></a>Lásd még
*  [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
*  [Apache Spark feladatok hibakeresése kiterjesztett Spark History Server használatával](apache-azure-spark-history-server.md)

### <a name="for-data-analysts"></a>Adatelemzők számára

* [Apache Spark a Machine Learningkal: A Spark in HDInsight használata az építési hőmérséklet elemzésére a HVAC-adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learningkal: Az élelmiszer-vizsgálati eredmények előrejelzése a Spark in HDInsight használatával](apache-spark-machine-learning-mllib-ipython.md)
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
