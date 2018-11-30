---
title: Futó Azure HDInsight az Apache Spark-feladatok hibakereséséhez
description: YARN felhasználói felületén, a Spark felhasználói felület és a Spark-előzménykiszolgáló használatával nyomon követése és hibakeresése az Azure HDInsight Spark-fürtön futó feladatok
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: hrasheed
ms.openlocfilehash: 6afb54caca572988c566ab7c6325d511e77fbd3e
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582088"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Futó Azure HDInsight az Apache Spark-feladatok hibakereséséhez

Ebből a cikkből elsajátíthatja, hogyan nyomon követése és hibakeresése [Apache Spark](https://spark.apache.org/) futó feladatok HDInsight-fürtök használatával a [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) felhasználói felület, a Spark felhasználói felület és a Spark-Előzménykiszolgáló. A Spark-fürt rendelkezésre álló Jegyzetfüzet használata Spark-feladat indítása **Machine learning: élelmiszervizsgálati egészségügyi adatok MLLib segítségével prediktív elemzési**. A következő lépések segítségével nyomon követheti az elküldött megközelítéssel bármilyen más, például egy alkalmazás **spark-submit szkripttel**.

## <a name="prerequisites"></a>Előfeltételek
Az alábbiakkal kell rendelkeznie:

* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).
* Meg kell kezdeni a notebook futó  **[Machine learning: élelmiszervizsgálati egészségügyi adatok MLLib segítségével prediktív elemzési](apache-spark-machine-learning-mllib-ipython.md)**. Ez a jegyzetfüzet futtatásához útmutatást kövesse a hivatkozást.  

## <a name="track-an-application-in-the-yarn-ui"></a>Nyomon követheti az alkalmazás a YARN felhasználói felületén
1. Nyissa meg a YARN felhasználói felületén. Kattintson a **fürt irányítópultja**, és kattintson a **YARN**.
   
    ![Indítsa el a YARN felhasználói felületén](./media/apache-spark-job-debugging/launch-yarn-ui.png)
   
   > [!TIP]
   > Másik lehetőségként az Ambari felhasználói felületén, a YARN felhasználói felületén is megnyithatja. Az Ambari felhasználói felületén indításához kattintson **fürt irányítópultja**, és kattintson a **HDInsight-fürt irányítópultja**. Az Ambari felhasználói felületén kattintson **YARN**, kattintson a **Gyorshivatkozások**, kattintson az aktív erőforrás-kezelő, majd **erőforrás-kezelő felhasználói felületén**.    
   > 
   > 
2. A Jupyter notebookok használata Spark-feladat indítása, mert az alkalmazás rendelkezik ezzel a névvel **remotesparkmagics** (Ez a minden alkalmazás, amely a notebookok való indítása a nevét). Kattintson az Alkalmazásazonosítót, szemben az alkalmazás nevét a feladattal kapcsolatos további információért. Ezzel elindítja az alkalmazás megtekintése.
   
    ![Keresse meg a Spark-alkalmazás azonosítója](./media/apache-spark-job-debugging/find-application-id.png)
   
    Az ilyen alkalmazások, melyeket a Jupyter notebookokból származó, az állapot mindig van **FUTÓ** a notebook bezárásáig.
3. Az alkalmazás a nézetben végezhet részletes elemzést tovább ismerje meg az alkalmazás és a naplók (stdout/stderr) társított tárolókat. A Spark felhasználói felületen indítsa el a hivatkozási megfelelő kattintva a **követési URL-cím**lent látható módon. 
   
    ![Töltse le a tároló naplóit](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Nyomon követheti az alkalmazások a Spark felhasználói felület
A Spark felhasználói felületén részletezhető le a Spark-feladatok, amelyek a rendszer létrehozta az alkalmazás korábban megkezdése.

1. Indítsa el a Spark felhasználói felületén az alkalmazás a nézetben kattintson a hivatkozásra, szemben a **követési URL-cím**a fenti képernyőfelvételen látható módon. Láthatja, hogy az alkalmazás futtatása a Jupyter notebook indult minden Spark-feladatok.
   
    ![Spark-feladatok megtekintése](./media/apache-spark-job-debugging/view-spark-jobs.png)
2. Kattintson a **végrehajtóval** lapján megtekintheti az egyes végrehajtó kapcsolatos feldolgozási és tárolási. A hívási veremben kattintva is lekérhet a **hozzászóláslánc Dump** hivatkozásra.
   
    ![A Spark végrehajtóval megtekintése](./media/apache-spark-job-debugging/view-spark-executors.png)
3. Kattintson a **szakaszok** lapján megtekintheti a szakaszában az alkalmazáshoz kapcsolódó.
   
    ![Spark-állapotok megjelenítése](./media/apache-spark-job-debugging/view-spark-stages.png)
   
    Minden egyes szakaszhoz különböző számítási feladatokhoz, amelyek találhatja meg a végrehajtási statisztika, például rendelkezhet alább látható módon.
   
    ![Spark-állapotok megjelenítése](./media/apache-spark-job-debugging/view-spark-stages-details.png) 
4. A fázis részleteit megjelenítő oldalon DAG Vizualizáció indíthatja el. Bontsa ki a **DAG Vizualizáció** hivatkozásra az oldal tetején lévő alább látható módon.
   
    ![Spark szakaszok DAG Vizualizáció megtekintése](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)
   
    DAG- vagy közvetlen Aclyic Graph jelöli meg a különböző szakaszaiban az alkalmazásban. A gráf összes kék mezőben egy Spark-művelet meghívása az alkalmazásból jelöli.
5. A fázis részleteit megjelenítő oldalon is elindíthat, az alkalmazás idővonalnézetét. Bontsa ki a **esemény ütemterv** hivatkozásra az oldal tetején lévő alább látható módon.
   
    ![Spark szakaszok esemény idősor megtekintése](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)
   
    Ez a Spark eseményeket egy idővonalon formájában jeleníti meg. Az Ütemterv nézetben érhető el három szinten, a feladatokon belül, és a egy szakaszon belül a feladatok között. A fenti képen az idősor nézetének adott szakaszában rögzíti.
   
   > [!TIP]
   > Ha a **nagyítás engedélyezése** jelölőnégyzet bejelölésével, görgethet bal és jobb idősor nézetének között.
   > 
   > 
6. A Spark felhasználói felület többi lapján adja meg a Spark-példányt, valamint hasznos információkat.
   
   * Tároló lap – az alkalmazás létrehoz egy rdd-kként megkeresni tárolás fülön lévőket kapcsolatos információkat.
   * Környezet lap – ezen a lapon számos hasznos információkat a Spark-példányt például a 
     * Scala-verzió
     * A fürthöz társított Eseménynapló könyvtár
     * Az alkalmazás végrehajtó magok száma
     * Stb.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Információ a befejezett feladatok használata a Spark-Előzménykiszolgáló
Ha egy feladat befejeződött, a feladat információi a Spark-Előzménykiszolgáló a rendszer megőrzi.

1. Indítsa el a Spark-Előzménykiszolgáló, a fürt panelén kattintson a **fürt irányítópultja**, és kattintson a **Spark-Előzménykiszolgáló**.
   
    ![Indítsa el a Spark-Előzménykiszolgáló](./media/apache-spark-job-debugging/launch-spark-history-server.png)
   
   > [!TIP]
   > Másik lehetőségként a Spark előzmények kiszolgáló felhasználói felülete a az Ambari felhasználói felületén is megnyithatja. Indítsa el a Ambari felhasználói felületén, a fürt panelén kattintson a **fürt irányítópultja**, és kattintson a **HDInsight-fürt irányítópultja**. Az Ambari felhasználói felületén kattintson **Spark**, kattintson a **Gyorshivatkozások**, és kattintson a **Spark előzmények kiszolgáló felhasználói felülete**.
   > 
   > 
2. A befejezett alkalmazásokkal felsorolt láthatja. Kattintson egy Alkalmazásazonosítót az alkalmazás további információk feltárásához.
   
    ![Indítsa el a Spark-Előzménykiszolgáló](./media/apache-spark-job-debugging/view-completed-applications.png)

## <a name="see-also"></a>Lásd még
*  [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
*  [Kiterjesztett Spark-Előzménykiszolgáló használata Apache Spark-feladatok hibakeresése](apache-azure-spark-history-server.md)

### <a name="for-data-analysts"></a>Az adatelemzők számára

* [Az Apache Spark és Machine Learning: a Spark on HDInsight HVAC-adatok épület-hőmérséklet elemzésére a használata](apache-spark-ipython-notebook-machine-learning.md)
* [Az Apache Spark és Machine Learning: használja a Spark on HDInsight az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [A webhelynapló elemzése a HDInsight az Apache Spark használatával](apache-spark-custom-library-website-log-analysis.md)
* [Application Insights telemetriai adatainak elemzése Apache Spark on HDInsight használatával](apache-spark-analyze-application-insight-logs.md)
* [Caffe elosztott deep learning az Azure HDInsight Spark használata](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>Spark-fejlesztőknek

* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az Apache Spark-alkalmazások távoli hibakeresése az IntelliJ IDEA HDInsight-eszközei beépülő használata](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Az Apache Zeppelin notebookok használata a HDInsight Apache Spark-fürt](apache-spark-zeppelin-notebook.md)
* [Notebookokhoz elérhető kernelek Jupyter a HDInsight az Apache Spark-fürt](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)


