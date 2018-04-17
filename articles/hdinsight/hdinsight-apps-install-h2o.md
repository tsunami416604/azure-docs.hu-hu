---
title: Telepítés közzétett alkalmazás - H2O készült vízjel - Azure HDInsight |} Microsoft Docs
description: Telepítheti és használhatja a H2O készült vízjel külső Hadoop alkalmazás.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 9a03588b3327c3ab231f5c2cae17488f4d63bde7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="install-published-application---h2o-sparkling-water"></a>Telepítse a közzétett alkalmazás - H2O készült vízjel

Ez a cikk ismerteti, hogyan telepíthetnek és futtathatnak a [H20 készült vízjel](http://www.h2o.ai/) on Azure HDInsight Hadoop-alkalmazások közzététele. A HDInsight-alkalmazás platform áttekintését, és a rendelkezésre álló független szoftverszállító (ISV) listáját közzétett alkalmazások: [külső Hadoop-alkalmazások telepítése](hdinsight-apps-install-applications.md). A saját alkalmazások telepítéséről az [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md) című cikk tartalmaz útmutatást.

## <a name="about-h2o-sparkling-water"></a>Kapcsolatos H2O készült vízjel

H2O készült vízjel egy nyílt forráskódú, teljes elosztott memórián belüli machine learning platform lineáris méretezhetőség. H2O készült vízjel lehetővé teszik, hogy gyors és méretezhető gépi tanulási algoritmusok H2O Spark lehetőségeinek kombinálni. Vízjel készült, a felhasználók is meghajtó a számítási Scala R és Python a felhasználói felületen H2O Flow.

H2O készült vízjel biztosítja:

* **Könnyen használható WebUI és ismerős felületek** – állítsa be, és gyorsan vagy H2O intuitív webes Flow grafikus felhasználói felülettel vagy programozási környezetekben például R, Python, Java, Scala, JSON és a H2O API-k első lépései.
* **Minden közös adatbázist és a fájl típusú adatok-független támogatása** – egyszerűen vizsgálatát, és a Microsoft Excel, R Studio, a Tableau és több Big Data modell. Csatlakoztassa HDFS, S3, SQL és NoSQL adatforrásokból származó adatok.
* **Nagymértékben méretezhető Big Data típusú adatok munging és elemzések** – H2O nagy illesztések 7 x gyorsabb, mint az R data.table műveletek végrehajtása, és a lineárisan méretezhető, 10 milliárd x 10 milliárd sor illesztéseket.
* **Valós idejű adatok pontozása** – gyors modellek telepítése éles egyszerű-régi Java-objektumok (pojo-vá) modell optimalizált Java objektumok (MOJO), vagy a H2O REST API-t.

### <a name="resource-links"></a>Erőforrás-hivatkozások

* [H2O.ai mérnöki terv](https://jira.h2o.ai/)
* [H2O.ai kezdőlapja](http://www.h2o.ai/)
* [H2O.ai dokumentáció](http://docs.h2o.ai/)
* [H2O.ai támogatása](https://support.h2o.ai/)
* [H2O.ai nyílt forráskódú Codebase](https://github.com/h2oai/)

## <a name="prerequisites"></a>Előfeltételek

Ez az alkalmazás egy új HDInsight-fürtöt, vagy egy meglévő fürt telepítéséhez a következő konfigurációval kell rendelkeznie:

* A fürt tier(s): Standard vagy prémium
* Fürt típusa: Spark
* A fürt verzió(k): 3.5-ös vagy 3.6.

## <a name="install-the-h2o-sparkling-water-published-application"></a>Telepítse a H2O készült vízjel közzétett alkalmazás

Ez, és más elérhető ISV alkalmazások telepítésével kapcsolatos részletes útmutatás olvasható [külső Hadoop-alkalmazások telepítése](hdinsight-apps-install-applications.md).

## <a name="launch-h2o-sparkling-water"></a>Indítsa el a H2O készült vízjel

1. A telepítés után megkezdheti H2O készült vízjel (h2o-sparklingwater) Azure-portálon fürtről Jupyter notebookok megnyitásával (`https://<ClusterName>.azurehdinsight.net/jupyter`). Akkor is kapni a Jupyter **fürt irányítópult** a fürt panelről a portálon, jelölje be **Jupyter Notebook**. A hitelesítő adatok megadását kéri. Adja meg a fürt Hadoop hitelesítő adatokat a fürt létrehozásakor megadott.

2. Jupyter, három mappa látható: H2O-PySparkling-példák, a PySpark példákat és a Scala példák. Válassza ki a **H2O-PySparkling-példák** mappát.

    ![Otthoni Jupyter notebookok](./media/hdinsight-apps-install-h2o/jupyter-home.png)

3. Az első lépés egy új notebook létrehozása esetén a Spark-környezet konfigurálása. Ez az információ szerepel a **Sentiment_analysis_with_Sparkling_Water** példa. A Spark környezet konfigurálásakor ügyeljen arra, hogy az a megfelelő jar használja, és adja meg az első kimenete által megadott IP-cím.

    ![Otthoni Jupyter notebookok](./media/hdinsight-apps-install-h2o/spark-config.png)

4. A H2O fürt elindításához.

    ![Indítsa el a fürt](./media/hdinsight-apps-install-h2o/start-cluster.png)

5. Miután a H2O fürt megfelelően működik, és, nyissa meg H2O Flow címen **`https://<ClusterName>-h2o.apps.azurehdinsight.net:443`**.

    > [!NOTE]
    > Ha nem tudja megnyitni a H2O Flow, próbálja meg törölni a gyorsítótárban. Ha még mindig nem lehet elérni, valószínűleg nincs elég erőforrás a fürtön. Próbálja növelni a feldolgozó csomópontok száma a **méretezési fürt** lehetőséget a fürt ablaktáblán.

    ![H2O Flow irányítópult](./media/hdinsight-apps-install-h2o/h2o-flow.png)

6. Válassza ki a **Million_Songs.flow** példa a jobb oldali menüből. Figyelmeztetés megjelenésekor kattintson **terhelés Notebook**. Ez a bemutató futtatásra tervezték, használja az adatok Valóságosak, néhány perc múlva. A cél az, hogy az adatokból megjósolható, hogy a dal előtt vagy után 2004 kiadott bináris besorolásával.

    ![Válassza ki a Million_Songs.flow](./media/hdinsight-apps-install-h2o/million-songs.png)

7. Az elérési utat tartalmazó található **milsongs-cls-train.csv.gz**, és cserélje le a teljes útvonalat **https://h2o-public-test-data.s3.amazonaws.com/bigdata/laptop/milsongs/milsongs-cls-train.csv.gz**.

8. Az elérési utat tartalmazó található **milsongs-cls-test.csv.gz** , és cserélje le **https://h2o-public-test-data.s3.amazonaws.com/bigdata/laptop/milsongs/milsongs-cls-test.csv.gz**.

9. A notebook cellákon belüli összes utasítást végrehajtani, válassza ki a **minden** gomb az eszköztáron.

    ![Az összes futtatása](./media/hdinsight-apps-install-h2o/run-all.png)

10. Néhány perc elteltével megtekintheti a következőhöz hasonló kimenetnek.

    ![Kimenet](./media/hdinsight-apps-install-h2o/output.png)

Ennyi az egész! Mesterséges eszközintelligencia Spark percek belül már harnessed. Most ismerje meg a további példákat a H2O Flow, amelyek bemutatják a különböző típusú gépi tanulási algoritmus.

## <a name="next-steps"></a>További lépések

* [H2O dokumentáció](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/index.html)
* [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md): telepítése egy közzé nem tett HDInsight-alkalmazást.
* [HDInsight-alkalmazások közzététele](hdinsight-apps-publish-applications.md): Megtudhatja, hogyan teheti közzé egyéni HDInsight-alkalmazásait az Azure Piactéren.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: HDInsight-alkalmazás telepítése): Megtudhatja, hogyan adhat meg HDInsight-alkalmazásokat.
* [Parancsfájlművelet Linux-alapú HDInsight-fürtök testreszabása](hdinsight-hadoop-customize-cluster-linux.md): megtudhatja, hogyan telepíthet további alkalmazásokat parancsfájl művelettel.
* [Üres peremhálózati csomópontok használata a Hdinsightban](hdinsight-apps-use-edge-node.md): egy üres élcsomópontot használata a HDInsight-fürtök eléréséhez, és a teszteléshez és a HDInsight-alkalmazások üzemeltetéséhez.
