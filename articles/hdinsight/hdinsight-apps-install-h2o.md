---
title: Közzétett alkalmazás – H2O Sparkling Water – Azure HDInsight telepítése
description: Telepítheti és használhatja a H2O Sparkling Water külső Hadoop-alkalmazásokat.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 4be346163fd54c0c5f962d15bc2433c7fab49e0b
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2018
ms.locfileid: "53650943"
---
# <a name="install-published-application---h2o-sparkling-water"></a>Közzétett alkalmazás – H2O Sparkling Water telepítése

Ez a cikk bemutatja, hogyan telepítheti és futtathatja a [H20 Sparkling Water](https://www.h2o.ai/) közzétett [Apache Hadoop](https://hadoop.apache.org/) alkalmazás az Azure HDInsight a. A HDInsight-alkalmazásplatform áttekintése és a egy listát az elérhető független szoftverszállító (ISV) közzétett alkalmazások létrehozásáról: [külső gyártótól származó Hadoop-alkalmazások telepítése](hdinsight-apps-install-applications.md). A saját alkalmazások telepítéséről az [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md) című cikk tartalmaz útmutatást.

## <a name="about-h2o-sparkling-water"></a>Tudnivalók a H2O Sparkling Water

H2O Sparkling Water egy nyílt forráskódú, teljes mértékben elosztott memóriabeli gépek tanulási platform lineáris méretezhetőség. H2O Sparkling Water lehetővé teszik a gyors, skálázható gépi tanulási algoritmusok H2O lehetőségeinek egyesítése [Apache Spark](https://spark.apache.org/). Sparkling Water, a felhasználók ösztönözheti a számítási [Scala](https://www.scala-lang.org/), R és Python nyelven a H2O Flow felhasználói felület használatával.

H2O Sparkling Water biztosítja:

* **Mire használható egyszerű és ismerős felületek** – állítson be beállítása és első lépések gyorsan vagy H2O intuitív webes Flow grafikus felhasználói felületen vagy a programozási környezetei, mint például az R, Python, Java, Scala, JSON és a H2O API-k.
* **Adat-független támogatása az összes gyakori adatbázis és a fájl** – könnyen feltárhatja, és a Microsoft Excel, az R Studio, a Tableau és további Big Data modell. A HDFS, S3, SQL és nosql-alapú adatforrásokhoz csatlakozhat adataihoz.
* **Rugalmasan méretezhető big Data típusú adatok munging és elemzési** – H2O big Data típusú összekapcsolások 7 x gyorsabb, mint az R data.table műveleteket végrehajtani, és 10 milliárd x 10 milliárd sor illesztések lineárisan méretezhető.
* **Valós idejű pontozási** – gyorsan egyszerű – régi Java-objektumok (pojo-vá) (MOJO), a Java-objektumok modell optimalizált üzemi modellek üzembe helyezése vagy a H2O REST API-t.

### <a name="resource-links"></a>Erőforrás-hivatkozások

* [H2O.ai mérnöki ütemterve](http://jira.h2o.ai/)
* [H2O.ai kezdőlapja](https://www.h2o.ai/)
* [H2O.ai dokumentációja](http://docs.h2o.ai/)
* [H2O.ai támogatása](https://support.h2o.ai/)
* [H2O.ai nyílt forráskódú kódbázis](https://github.com/h2oai/)

## <a name="prerequisites"></a>Előfeltételek

Telepíti az alkalmazást egy új HDInsight-fürtöt, vagy egy meglévő fürthöz, a következő konfigurációval kell rendelkeznie:

* Fürtszint(ek): Standard vagy prémium szintű
* Fürt típusa: Spark
* Fürtverzió(k): 3.5-ös és 3.6-os

## <a name="install-the-h2o-sparkling-water-published-application"></a>A közzétett alkalmazás telepítése a H2O Sparkling Water

Ezzel és más elérhető ISV-alkalmazások telepítésének lépésenkénti útmutatójáért olvassa el a [külső Apache Hadoop-alkalmazások telepítése](hdinsight-apps-install-applications.md).

## <a name="launch-h2o-sparkling-water"></a>Indítsa el a H2O Sparkling Water

1. A telepítés után megkezdheti H2O Sparkling Water (h2o-sparklingwater) az Azure Portalon a fürtről megnyitásával [Jupyter notebookok](https://jupyter.org/) (`https://<ClusterName>.azurehdinsight.net/jupyter`). Akkor is elérhető Jupyter kiválasztásával **fürt irányítópultja** a fürt panelről a portálon, majd válassza **Jupyter Notebook**. A hitelesítő adatok megadását kéri. Adja meg a fürt Hadoop hitelesítő adatokat a fürt létrehozásakor megadott.

2. A Jupyter három mappa jelenik meg: H2O-PySparkling-példák, PySpark példákat és a Scala példákat. Válassza ki a **H2O-PySparkling-példák** mappát.

    ![Otthoni Jupyter-notebookok](./media/hdinsight-apps-install-h2o/jupyter-home.png)

3. Az első lépés egy új jegyzetfüzet létrehozása során, hogy a Spark környezet beállításához. Ez az információ tartalmazza a **Sentiment_analysis_with_Sparkling_Water** példa. A Spark környezet konfigurálásakor ügyeljen arra, használja a megfelelő fájlt, és adja meg az első olyan cellára kimenete által megadott IP-címen.

    ![Otthoni Jupyter-notebookok](./media/hdinsight-apps-install-h2o/spark-config.png)

4. A H2O fürt elindításához.

    ![Indítsa el a fürt](./media/hdinsight-apps-install-h2o/start-cluster.png)

5. Miután a H2O fürt működik és elérhető, nyissa meg a H2O Flow a **`https://<ClusterName>-h2o.apps.azurehdinsight.net:443`**.

    > [!NOTE]  
    > Ha nem tudja megnyitni a H2O Flow, törölje a böngésző gyorsítótárát. Ha még mindig nem lehet elérni, valószínűleg nem rendelkezik elegendő erőforrás a fürtön. Próbálja ki a munkavégző csomópontok számának növelése a **fürt méretezése** beállítást a fürt ablaktáblán.

    ![A Flow H2O irányítópult](./media/hdinsight-apps-install-h2o/h2o-flow.png)

6. Válassza ki a **Million_Songs.flow** példa a jobb oldali menüből. Figyelmeztetés megjelenésekor kattintson **terhelés Notebook**. Ez a bemutató történő használatával az adatok néhány perc múlva futtatásra van tervezve. A célja, hogy az adatok alapján előre jelezni-e a dal fel lett oldva előtt vagy után 2004 bináris osztályozási használatával.

    ![Válassza ki a Million_Songs.flow](./media/hdinsight-apps-install-h2o/million-songs.png)

7. Keresse meg az elérési utat tartalmazó **milsongs-cls-train.csv.gz**, és cserélje le a teljes útvonalon **https://h2o-public-test-data.s3.amazonaws.com/bigdata/laptop/milsongs/milsongs-cls-train.csv.gz**.

8. Keresse meg az elérési utat tartalmazó **milsongs-cls-test.csv.gz** , és cserélje le a következőre **https://h2o-public-test-data.s3.amazonaws.com/bigdata/laptop/milsongs/milsongs-cls-test.csv.gz**.

9. Hajtsa végre a jegyzetfüzetet cellákban összes utasításokat, jelölje be a **összes futtatása** gombra az eszköztáron.

    ![Az összes futtatása](./media/hdinsight-apps-install-h2o/run-all.png)

10. Néhány perc múlva, az alábbihoz hasonló kimenetnek kell megjelennie.

    ![Kimenet](./media/hdinsight-apps-install-h2o/output.png)

Ennyi az egész! Mesterséges intelligencia Spark csupán néhány percen belül már harnessed. Most megismerheti, amelyek bemutatják a különböző típusú gépi tanulási algoritmusok a H2O Flow további példákat.

## <a name="next-steps"></a>További lépések

* [H2O dokumentációja](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/index.html)
* [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md): Ismerje meg, hogyan lehet HDInsight közzé nem tett HDInsight-alkalmazás üzembe helyezése.
* [HDInsight-alkalmazások közzététele](hdinsight-apps-publish-applications.md): Ismerje meg, hogyan teheti közzé egyéni HDInsight-alkalmazásait akár az Azure Marketplace-en.
* [MSDN: Egy HDInsight-alkalmazás telepítése](https://msdn.microsoft.com/library/mt706515.aspx): Ismerje meg, hogyan adhat meg HDInsight-alkalmazások.
* [Szkriptműveletek használatával Linux-alapú HDInsight-fürtök testre szabása](hdinsight-hadoop-customize-cluster-linux.md): Ismerje meg, hogyan telepíthet további alkalmazásokat használ.
* [Üres élcsomópontok használata a HDInsight](hdinsight-apps-use-edge-node.md): Ismerje meg, hogy egy üres élcsomópontot használata a HDInsight-fürtök eléréséhez, és a teszteléshez és a HDInsight-alkalmazások üzemeltetése.
