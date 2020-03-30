---
title: A Spark-feladat debug-feladata az IntelliJ Azure Toolkit segítségével (előzetes verzió) - HDInsight
description: Útmutató a HDInsight-eszközök használatával az Azure Toolkit for IntelliJ alkalmazásban az alkalmazások hibakereséséhez
keywords: debug távolról intellij, távoli hibakeresés intellij, ssh, intellij, hdinsight, debug intellij, hibakeresés
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 1a0a6cf5a26854539dc4bbb0ae0254bbf08dad1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73494589"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>Hiba szikrafeladat-hibakeresés az Azure Toolkit for IntelliJ segítségével (előzetes verzió)

Ez a cikk lépésenként útmutatást nyújt a HDInsight-eszközök használatával az [Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) **spark-hibahiba-alkalmazások** futtatásához.

## <a name="prerequisites"></a>Előfeltételek

* [Oracle Java Fejlesztési készlet](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). Ez az oktatóanyag a Java 8.0.202-es verzióját használja.
  
* IntelliJ ÖTLET. Ez a cikk [az IntelliJ IDEA Community ver. 2019.1.3- at](https://www.jetbrains.com/idea/download/#section=windows)használja.
  
* Azure Toolkit for IntelliJ. Lásd: [Az Azure Toolkit telepítése az IntelliJ-hez című témakört.](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable)

* Csatlakozzon a HDInsight-fürthöz. Lásd: [Csatlakozás a HDInsight-fürthöz](apache-spark-intellij-tool-plugin.md).

* Microsoft Azure Storage Explorer. Lásd: [Microsoft Azure Storage Explorer letöltése.](https://azure.microsoft.com/features/storage-explorer/)

## <a name="create-a-project-with-debugging-template"></a>Projekt létrehozása hibakereső sablonnal

Hozzon létre egy spark2.3.2-es projektet a hibahiba folytatásához, a dokumentumban lévő mintafájl hibahiba-hibakeresését.

1. Nyissa meg az IntelliJ IDEA-t. Nyissa meg az **Új projekt** ablakot.

   a. Válassza ki az **Azure Spark/HDInsight** a bal oldali ablaktáblában.

   b. Válassza ki a **Spark-projekt sikertelen feladathibakeresés minta(előzetes) (Scala)** a főablakban.

     ![Intellij Hibakeresési projekt létrehozása](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   c. Válassza a **Tovább lehetőséget.**

2. Az **Új projekt** ablakban tegye a következő lépéseket:

   ![Intellij Új projekt válassza spark verzió](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   a. Adja meg a projekt nevét és helyét.

   b. A **Project SDK** legördülő listájában válassza a **Java 1.8** a **Spark 2.3.2-es** fürthöz.

   c. A **Spark-verzió** legördülő listájában válassza a **Spark 2.3.2(Scala 2.11.8)** lehetőséget.

   d. Válassza a **Finish** (Befejezés) elemet.

3. Válassza **ki az src** > **fő** > **scala** lehetőséget a kód megnyitásához a projektben. Ez a példa a **AgeMean_Div()** parancsfájlt használja.

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>Spark Scala/Java-alkalmazás futtatása HDInsight-fürtön

Hozzon létre egy spark Scala/Java-alkalmazást, majd futtassa az alkalmazást egy Spark-fürtön az alábbi lépésekkel:

1. Kattintson **a Konfiguráció hozzáadása** gombra a **Futtatás/Hibakeresés ablak** megnyitásához.

   ![HDI Intellij Konfiguráció hozzáadása](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png)

2. A **Futtatás/Hibakeresés konfigurációk** párbeszédpanelen jelölje ki**+** a pluszjelet ( ). Ezután válassza az **Apache Spark a HDInsight-on** lehetőséget.

   ![Intellij Új konfiguráció hozzáadása](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. Váltson **a Távoli futtatás a Fürtlapon** lapra. **Main class name** **Name** **Spark cluster** Eszközeink támogatják a debug **a végrehajtók**. A **numExectors**, az alapértelmezett érték 5, és jobb, ha nem állít be magasabb, mint 3. A futási idő csökkentése érdekében hozzáadhatja a **spark.yarn.maxAppAttempts** értéket a **feladatkonfigurációkhoz,** és az értéket 1-re állíthatja. A konfiguráció mentéséhez kattintson az **OK** gombra.

   ![Intellij Run debug konfigurációk új](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. A rendszer most menti a konfigurációt a megadott névvel. A konfiguráció részleteinek megtekintéséhez válassza ki a konfiguráció nevét. A módosításokhoz válassza a **Konfigurációk szerkesztése**lehetőséget.

5. A konfigurációs beállítások befejezése után futtathatja a projektet a távoli fürtön.

   ![Intellij Debug Távoli Spark Feladat Távoli fuss gomb](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. Az alkalmazásazonosítót a kimeneti ablakból ellenőrizheti.

   ![Intellij Debug Távoli Spark Feladat Távoli fuss eredmény](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)

## <a name="download-failed-job-profile"></a>Sikertelen feladatprofil letöltése

Ha a feladatbeküldések sikertelenek, letöltheti a sikertelen feladatprofilt a helyi számítógépre további hibakereséscéljából.

1. Nyissa meg a **Microsoft Azure Storage Exploreralkalmazást,** keresse meg a fürt HDInsight-fiókját a sikertelen feladathoz, töltse le a sikertelen feladat-erőforrásokat a megfelelő helyről: **\hdp\spark2-events\\.spark-failures\\\<alkalmazásazonosítót>** egy helyi mappába. A **tevékenységek** ablakban megjelenik a letöltési folyamat.

   ![Az Azure Storage Explorer letöltési hibája](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![Sikeresen letölthető az Azure Storage Explorer](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>Helyi hibakeresési környezet konfigurálása és hibakeresés hiba esetén

1. Nyissa meg az eredeti projektet, vagy hozzon létre egy új projektet, és társítsa azt az eredeti forráskódhoz. Jelenleg csak a spark2.3.2-es verzió támogatott a hibakereséshez.

1. Az IntelliJ IDEA, hozzon létre egy **Spark Failure Debug** konfigurációs fájlt, válassza ki az FTD-fájlt a korábban letöltött sikertelen feladat-erőforrások a **Spark-feladat sikertelen környezete mezőben.**

   ![krétai hiba konfigurációja](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

1. Kattintson a helyi futtatás gombra az eszköztáron, és a hiba megjelenik a Futtatás ablakban.

   ![futtatási hiba-konfiguráció1](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![futtatási hiba-konfiguráció2](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

1. Állítsa be a töréspontot, ahogy a napló jelzi, majd kattintson a helyi hibakeresés gombra a helyi hibakereséshez, ugyanúgy, mint a normál Scala / Java projektek az IntelliJ-ben.

1. Hibakeresés után, ha a projekt sikeresen befejeződik, újra elküldheti a sikertelen feladat a spark hdinsight-fürtön.

## <a name="next-steps"></a><a name="seealso"></a>További lépések

* [Áttekintés: Az Apache Spark alkalmazások hibakeresése](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

### <a name="demo"></a>Bemutató

* Scala-projekt létrehozása (videó): [Apache Spark Scala alkalmazások létrehozása](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Távoli hibakeresés (videó): [Az Azure Toolkit for IntelliJ használatával távolról debugot ihat az Apache Spark-alkalmazásokat egy HDInsight-fürtön](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Forgatókönyvek

* [Apache Spark üzleti intelligencia használatával: Interaktív adatelemzés a Spark hdinsightban az üzletiintelligencia-eszközökkel](apache-spark-use-bi-tools.md)
* [Apache Spark a Machine Learningsegítségével: A Spark használata a HDInsightban az épület hőmérsékletének elemzéséhez a fűtés- és légtechnikai adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learningsegítségével: A Spark használata a HDInsightban az élelmiszer-ellenőrzési eredmények előrejelzéséhez](apache-spark-machine-learning-mllib-ipython.md)
* [Webhelynapló-elemzés az Apache Spark használatával a HDInsightban](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása

* [Önálló alkalmazás létrehozása a Scala használatával](../hdinsight-apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények

* [Az Azure Toolkit for IntelliJ segítségével apache Spark-alkalmazásokat hozhat létre EGY HDInsight-fürthöz](apache-spark-intellij-tool-plugin.md)
* [Az Azure Toolkit for IntelliJ segítségével távolról debugolhat Apache Spark-alkalmazásokat VPN-en keresztül](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HdInsight-eszközök használata az IntelliJ-hez a Hortonworks sandboxsegítségével](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Apache Spark-alkalmazások létrehozásához használja a HDInsight-eszközöket az Azure Eclipse eszközkészletében](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Apache Zeppelin-jegyzetfüzetek használata Apache Spark-fürttel a HDInsighton](apache-spark-zeppelin-notebook.md)
* [A Jupyter-jegyzetfüzethez elérhető kernelek a HDInsight-hoz létrehozott Apache Spark-fürtben](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése

* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
