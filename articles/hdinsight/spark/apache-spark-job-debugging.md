---
title: "Azure hdinsighton futó Apache Spark feladatok hibakeresése |} Microsoft Docs"
description: "Nyomon követése és hibakeresése az Azure HDInsight Spark-fürtön futó feladatok YARN felhasználói felületen, a Spark felhasználói felület és a Spark előzményei server segítségével"
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 59af05a7-2bd9-44b0-b55f-2438d294198b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2017
ms.author: jgao
ms.openlocfilehash: fb2487ec854260bacf98789bd1be482172ead6a7
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Azure hdinsighton futó Apache Spark feladatok hibakeresése

Ez a cikk, útmutató nyomon követése és hibakeresése a HDInsight-fürtök a YARN felhasználói felületen, Spark felhasználói felület és a Spark előzmények kiszolgálón futó Spark feladatok. Ebben a cikkben azt a Spark-fürtön elérhető Jegyzetfüzet használata Spark feladat indítása **gépi tanulás: prediktív elemzési étele ellenőrző adatokat, és MLLib a**. Az alábbi lépéseket segítségével nyomon követheti a beküldött bármely más megközelítéssel is, például egy alkalmazás **spark-nyújt**.

## <a name="prerequisites"></a>Előfeltételek
Az alábbiakkal kell rendelkeznie:

* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* A HDInsight az Apache Spark-fürt. Útmutatásért lásd: [létrehozása az Apache Spark on Azure hdinsight clusters](apache-spark-jupyter-spark-sql.md).
* Meg kell kezdeni a notebook futtató  **[gépi tanulás: prediktív elemzési a étele ellenőrző adatokat, és MLLib](apache-spark-machine-learning-mllib-ipython.md)**. A notebook futtatásához útmutatást kövesse a hivatkozást.  

## <a name="track-an-application-in-the-yarn-ui"></a>Nyomon követheti az alkalmazás a YARN felhasználói felületen
1. A YARN felhasználói felületének indítása. A fürt paneljén kattintson **fürt irányítópult**, és kattintson a **YARN**.
   
    ![Indítsa el a YARN felhasználói felületen](./media/apache-spark-job-debugging/launch-yarn-ui.png)
   
   > [!TIP]
   > Másik lehetőségként is indítja el a YARN felhasználói felületen a az Ambari felhasználói Felületéről. Indítsa el az Ambari felhasználói felületén, a fürt paneljén kattintson a **fürt irányítópult**, és kattintson a **HDInsight fürt irányítópult**. Az Ambari felhasználói felületén kattintson **YARN**, kattintson a **Gyorshivatkozások**, kattintson az aktív erőforrás-kezelő, majd **erőforrás-kezelő felhasználói felületén**.    
   > 
   > 
2. Megkezdődött a Jupyter notebookok használata Spark-feladatot, mert az alkalmazás rendelkezik ezzel a névvel **remotesparkmagics** (azt a nevet minden olyan alkalmazásnál, amely a notebookok való indítása). Kattintson az Alkalmazásazonosítót a feladattal kapcsolatos további információkért az alkalmazás neve alapján. Ekkor elindul az alkalmazás megtekintése.
   
    ![Keresés a külső alkalmazás azonosítója](./media/apache-spark-job-debugging/find-application-id.png)
   
    Az ilyen alkalmazások, amelyek a Jupyter notebookok indítható, az állapot mindig van **futtató** mindaddig, amíg a notebook kilép.
3. Az alkalmazás nézetből részletezve megtudhatja, az alkalmazás és a naplók (stdout/stderr) társított tárolók tovább. A Spark felhasználói felületen indítsa el a hivatkozási megfelelő kattintva a **követési URL-cím**lent látható módon. 
   
    ![Tároló naplók letöltése](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>A Spark felhasználói felületén kérelmet nyomon követése
A Spark felhasználói felületén részletezhető le a Spark feladatok, amelyek a korábbi lépések alkalmazás által indított vannak.

1. Indítsa el a külső felhasználói felületén, az alkalmazás nézetben kattintson a hivatkozásra, szemben a **követési URL-cím**, a fenti képernyőfelvételen látható módon. A Spark feladatokat a Jupyter notebook futó alkalmazás működését tekintheti meg.
   
    ![A Spark-feladatok megtekintése](./media/apache-spark-job-debugging/view-spark-jobs.png)
2. Kattintson a **végrehajtója** minden végrehajtó az adatfeldolgozás és -tárolás Részletek lap. Kattintva is kérheti le a hívási veremben a **szál Dump** hivatkozásra.
   
    ![Spark végrehajtója megtekintése](./media/apache-spark-job-debugging/view-spark-executors.png)
3. Kattintson a **szakaszból** lapon, láthatja az alkalmazáshoz kapcsolódó szakaszok.
   
    ![A Spark-állapotok megjelenítése](./media/apache-spark-job-debugging/view-spark-stages.png)
   
    Minden szakaszhoz rendelkezhet több feladatot, amely találhatja meg a végrehajtási statisztika, például alább látható módon.
   
    ![A Spark-állapotok megjelenítése](./media/apache-spark-job-debugging/view-spark-stages-details.png) 
4. A szakasz részleteit megjelenítő oldalon is elindíthatja, DAG képi megjelenítés. Bontsa ki a **DAG képi megjelenítés** hivatkozásra az oldal tetején a lent látható módon.
   
    ![Spark szakaszból DAG képi megjelenítés megtekintése](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)
   
    DAG vagy közvetlen Aclyic Graph jelenti. az alkalmazás különböző szakaszokat. Minden egyes kék mező a grafikonon egy Spark művelet meghívása az alkalmazásból jelöli.
5. A szakasz részleteit megjelenítő oldalon is elindíthatja a alkalmazás idősor nézetének megnyitására. Bontsa ki a **esemény ütemterv** hivatkozásra az oldal tetején a lent látható módon.
   
    ![Spark szakaszból esemény ütemterv megtekintése](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)
   
    Ez megjeleníti a Spark események ütemterv formájában. Az Ütemterv nézetben érhető el három szinten különböző feladatokat, a feladatok és egy szakaszon belül. A fenti kép rögzíti adott szakaszában idősor nézetének megnyitására.
   
   > [!TIP]
   > Ha bejelöli a **nagyításához engedélyezése** jelölőnégyzetet, görgetve bal és jobb között idősor nézetének megnyitására.
   > 
   > 
6. A Spark felhasználói felületének más lapjaira nyújtanak hasznos információkat, valamint a Spark-példányban.
   
   * Tárolási lap – Ha az alkalmazás létrehoz egy RDDs található tárolás fülön lévőket kapcsolatos információkat.
   * Környezet lap – ezen a lapon számos hasznos információt a Spark-példányban, mint a 
     * Scala verzió
     * A fürt társított Eseménynapló könyvtár
     * Az alkalmazás végrehajtó magok száma
     * Stb.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Befejezett feladatokhoz a Spark-előzmények kiszolgáló használatával kapcsolatos információk megkereséséhez
Ha egy feladat befejeződött, a feladat információi a Spark-előzmények kiszolgálón megőrződjenek.

1. Indítsa el a Spark előzmények kiszolgáló, a fürt paneljén kattintson a **fürt irányítópult**, és kattintson a **Spark előzmények Server**.
   
    ![Indítsa el a Spark előzmények kiszolgáló](./media/apache-spark-job-debugging/launch-spark-history-server.png)
   
   > [!TIP]
   > Másik lehetőségként is indítja el a Spark előzmények kiszolgáló felhasználói felülete a az Ambari felhasználói felületén. Indítsa el az Ambari felhasználói felületén, a fürt paneljén kattintson a **fürt irányítópult**, és kattintson a **HDInsight fürt irányítópult**. Az Ambari felhasználói felületén kattintson **Spark**, kattintson a **Gyorshivatkozások**, és kattintson a **Spark előzmények kiszolgáló felhasználói felülete**.
   > 
   > 
2. Látni fogja a felsorolt összes befejeződött alkalmazásokkal. Kattintson az alkalmazás további információt a feltárásához Azonosítói.
   
    ![Indítsa el a Spark előzmények kiszolgáló](./media/apache-spark-job-debugging/view-completed-applications.png)

## <a name="see-also"></a>Lásd még
*  [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)

### <a name="for-data-analysts"></a>Az adatok elemző

* [Spark és Machine Learning: A Spark on HDInsight használata az épület-hőmérséklet elemzésére HVAC-adatok alapján](apache-spark-ipython-notebook-machine-learning.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [A webhelynapló elemzése a Spark on HDInsight használatával](apache-spark-custom-library-website-log-analysis.md)
* [Az Application Insights telemetriai adatainak elemzése a Spark on HDInsight használatával](apache-spark-analyze-application-insight-logs.md)
* [Az Azure HDInsight Spark Caffe elosztott mély tanulási használata](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>A Spark-fejlesztőknek

* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Spark-fürtön a Livy használatával](apache-spark-livy-rest-interface.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Spark Streaming: A Spark on HDInsight használata valós idejű streamelési alkalmazások összeállítására](apache-spark-eventhub-streaming.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark-alkalmazások távoli hibaelhárításához](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Zeppelin notebookok használata Spark-fürttel HDInsighton](apache-spark-zeppelin-notebook.md)
* [Jupyter notebookokhoz elérhető kernelek a HDInsight Spark-fürtjében](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)


