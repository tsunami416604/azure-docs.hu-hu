---
title: 'Azure-eszközkészlet az IntelliJ-hez: Spark-alkalmazások távoli ssh-n keresztül '
description: Lépésenkénti útmutatóját az IntelliJ-hez a HDInsight Tools használata alkalmazások távolról a HDInsight-fürtön ssh-n keresztül
keywords: távoli hibakeresés az intellij-vel, távoli hibakeresés az intellij, ssh, intellij hdinsight, a Hibakeresés az intellij-vel, hibakeresés
services: hdinsight
ms.service: hdinsight
author: hrasheed
ms.author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/25/2017
ms.openlocfilehash: 87a774fad6b7e45f233102b7fb026aa9663dceb0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58005921"
---
# <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Az Apache Spark-alkalmazások hibakeresését helyileg vagy távolról egy HDInsight-fürtön az Azure-eszközkészlet ssh-n keresztül az IntelliJ-hez

Ebben a cikkben részletes útmutatást nyújt a használatára a HDInsight-eszközei [IntelliJ-hez készült Azure-eszközkészlet](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) alkalmazások távolról egy HDInsight-fürtön. A hibakeresés a projektben, akkor is megtekintheti a [hibakeresése HDInsight Spark-alkalmazások Azure-eszközkészlet az IntelliJ-hez készült](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) videó.

**Előfeltételek**
* **HDInsight-eszközök az IntelliJ-hez készült Azure-eszközkészlet**. Ez az eszköz az IntelliJ-hez készült Azure-eszközkészlet részét képezi. További információkért lásd: [IntelliJ-hez készült Azure eszközkészlet telepítése](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation). És **IntelliJ-hez készült Azure-eszközkészlet**. Ez az eszközkészlet használata egy HDInsight-fürtön az Apache Spark-alkalmazások hozhatók létre. További információkért kövesse a [Apache Spark-alkalmazások egy HDInsight-fürt létrehozása az IntelliJ-hez készült Azure eszközkészlet használata](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).

* **HDInsight SSH szolgáltatás a felhasználónév és jelszó felügyeleti**. További információkért lásd: [HDInsight (az Apache Hadoop) SSH használatával csatlakozhat](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) és [az SSH-bújtatással való eléréséről az Ambari webes felhasználói felület, JobHistory, NameNode, az Apache Oozie és egyéb webes felület...](https://docs.microsoft.com/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 
## <a name="learn-how-to-perform-local-run-and-debugging"></a>Ismerje meg, hogyan hajtsa végre a helyi Futtatás és hibakeresés
### <a name="scenario-1-create-a-spark-scala-application"></a>1. forgatókönyv: Spark Scala-alkalmazás létrehozása 

1. Indítsa el az IntelliJ IDEA-t, majd hozzon létre egy projektet. A **New Project** (Új projekt) párbeszédablakban hajtsa végre az alábbi lépéseket:

   a. Válassza ki **Azure Spark és HDInsight**. 

   b. Válassza ki a Java vagy a Scala sablont, a beállítások alapján. Az alábbi lehetőségek közül választhat:

   - **Spark Project (Java)**

   - **Spark Project (Scala)**

   - **Spark-projektet hoz minták (Scala)**

   - **Hiba a feladat hibakeresési minták (előzetes verzió) (Scala) a Spark-projekt**

     Ez a példa egy **minták (Scala) a Spark projekt** sablont.

   c. A **Build tool** (Létrehozási eszköz) listában válassza ki az alábbiak közül az igényeinek megfelelőt:

   - **Maven**, a Scala projektlétrehozási varázsló támogatásához

   - **SBT**, a függőségek kezeléséhez és a Scala-projekt létrehozásához 

     ![Hibakeresési projekt létrehozása](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   d. Kattintson a **Tovább** gombra.     
 
1. A következő **új projekt** ablakban tegye a következőket:

   ![Válassza ki a Spark SDK](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   a. Adja meg a projekt nevét és a projekt helyére.

   b. Az a **projekt SDK** legördülő listában válassza **Java 1.8-as** a **Spark 2.x** fürt, vagy válasszon **Java 1.7-es** a **Spark 1.x**  fürt.

   c. Az a **Spark-verzió** legördülő listából válassza ki, a Scala-projekt létrehozása varázsló együttműködik a megfelelő verziójú Spark SDK és a Scala SDK-val. Ha a spark-fürt verziója korábbi, mint a 2.0-s, jelölje be **Spark 1.x**. Ellenkező esetben válassza **Spark-2.x.** Ez a példa a következő verziót használja: **Spark 2.0.2 (Scala 2.11.8)**.

   d. Válassza a **Finish** (Befejezés) elemet.

1. Válassza ki **src** > **fő** > **scala** , nyissa meg a kódot a projekthez. Ez a példa a **SparkCore_wasbloTest** parancsfájlt.

### <a name="prerequisite-for-windows"></a>Windows előfeltétel
A helyi Spark Scala-alkalmazások egy Windows-számítógépen futtatja, miközben kaphat kivétel leírtak [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). A kivétel lép fel, mert WinUtils.exe hiányzik a Windows. 

Ez a hiba megoldásához [a végrehajtható fájl letöltése](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) egy olyan helyre, például **C:\WinUtils\bin**. Adja hozzá a környezeti változó **HADOOP_HOME**, és állítsa be a változó értékét **C:\WinUtils**.

### <a name="scenario-2-perform-local-run"></a>2. forgatókönyv: Hajtsa végre a helyi Futtatás
1. Nyissa meg a **SparkCore_wasbloTest** parancsfájl, és kattintson a jobb gombbal a parancsprogram-szerkesztő, majd válassza ki, **futtassa a '[Spark-feladat] XXX'** végrehajtásához a helyi Futtatás.
1. Egyszer helyi futtatás befejeződött, megjelenik a kimeneti fájlba menti az aktuális Projektböngészőben **adatok** > **__alapértelmezett__**.

    ![Helyi futtatás eredménye](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-result.png)
1. Az alapértelmezett helyi futtatási konfigurációs automatikusan végrehajtásakor a helyi Futtatás és helyi hibakeresési eszközeivel állított be. Nyissa meg a konfigurációs **[a Spark on HDInsight] XXX** a jobb felső sarokban látható a **[a Spark on HDInsight] XXX** már létrehozott **Apache Spark on HDInsight**. Váltson **helyileg futtassa** fülre.

    ![Helyi futtatási konfigurációt](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)
    - [A környezeti változók](#prerequisite-for-windows): Ha már beállította a rendszer környezeti változó **HADOOP_HOME** való **C:\WinUtils**, azt is automatikusan észleli, hogy nem kell manuálisan adja hozzá.
    - [WinUtils.exe hely](#prerequisite-for-windows): Ha nem állított be a rendszer környezeti változót, annak helye a gombra kattintva.
    - Egyszerűen kiválaszthatja a két lehetőség közül választhat, és ezek nem szükségesek MacOS és Linux rendszeren.
1. Beállíthatja a konfigurációt kézzel a helyi Futtatás és helyi hibakeresési elvégzése előtt is. Az előző képernyőképen látható, válassza a pluszjelet (**+**). Válassza ki a **Apache Spark on HDInsight** lehetőséget. Adja meg az adatokat **neve**, **Main osztály neve** mentéséhez, majd kattintson a helyi Futtatás gombra.

### <a name="scenario-3-perform-local-debugging"></a>3. forgatókönyv: Hajtsa végre a helyi hibakeresés
1. Nyissa meg a **SparkCore_wasbloTest** szkriptet, set töréspontokat a kiválasztott.
1. Kattintson a jobb gombbal a parancsprogram-szerkesztő, és válassza ki a lehetőséget **Debug a(z) [a Spark on HDInsight] XXX "** helyi Hibakeresés végrehajtásához.   



## <a name="learn-how-to-perform-remote-run-and-debugging"></a>Ismerje meg, hogyan hajtsa végre a távoli Futtatás és hibakeresés
### <a name="scenario-1-perform-remote-run"></a>1. forgatókönyv: Hajtsa végre a távoli Futtatás

1. Hozzáférés a **konfigurációk szerkesztése** menüben kattintson az ikonra a jobb felső sarokban. Ebből a menüből hozzon létre, vagy a távoli hibakeresésre vonatkozó konfigurációk szerkesztése.

   ![Konfigurációk szerkesztése](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png) 

1. Az a **Futtatás és hibakeresés konfigurációk** párbeszédpanelen jelölje ki a plusz jelre (**+**). Válassza ki a **Apache Spark on HDInsight** lehetőséget.

   ![Új konfiguráció hozzáadása](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)
1. Váltson **távolról futtatni a fürt** fülre. Adja meg az adatokat **neve**, **Spark-fürt**, és **Main osztály neve**. Kattintson a **(távoli hibakeresés) speciális konfiguráció**. Az eszköz támogatja a hibakeresési **végrehajtóval**. A **numExectors**, az alapértelmezett értéke 5. Szeretné jobban nem beállíthat egy 3-nál nagyobb.

   ![Hibakeresési konfigurációk futtatása](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. Az a **(távoli hibakeresés) speciális konfiguráció** részen válassza **távoli hibakeresés Spark engedélyezése**. Adja meg az SSH-felhasználónevet és jelszót, vagy a titkos kulcsfájl használata. Ha szeretne távoli hibakeresés végrehajtása, meg kell azt. Hiba esetén nem kell beállítani, ha csak át szeretné használni a távoli futtatása.

   ![Távoli hibakeresés Spark engedélyezése](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. A konfiguráció most már a megadott név lesz mentve. A konfigurációs részletek megtekintéséhez válassza ki a konfiguráció nevét. A módosításokat, válassza ki a **konfigurációk szerkesztése**. 

1. Miután végzett a konfiguráció beállításait, a projekt futtatásához a távoli fürtön, vagy távoli hibakeresésének.
   
   ![Távoli Futtatás gombra](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run.png)

1. Kattintson a **Disconnect** gombot, amely a beküldés naplók nem jelennek meg a bal oldali panelen. Azonban továbbra is fut a háttérkiszolgálón.

   ![Távoli Futtatás gombra](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/remote-run-result.png)



### <a name="scenario-2-perform-remote-debugging"></a>2. forgatókönyv: Távoli hibakeresés végrehajtása
1. Állítsa be a használhatatlanná tévő pontok, és kattintson a **távoli hibakeresési** ikonra. Távoli beküldése az a különbség, hogy az SSH felhasználónév/jelszó kell konfigurálni.

   ![Válassza ki a hibakeresés ikon](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. Amikor a program végrehajtását eléri a használhatatlanná tévő pont, megjelenik egy **illesztőprogram** lapra, és két **végrehajtó** adhatja a **hibakereső** ablaktáblán. Válassza ki a **folytatása Program** ikonra kattintva továbbra is fut a kódra, amely ér el a következő töréspontig. Át kell váltania a megfelelő **végrehajtó** fülre, és keresse meg a cél végrehajtó hibakereséséhez. A feladatvégrehajtási naplók megtekintheti a megfelelő **konzol** fülre.

   ![Hibakeresés lap](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>3. forgatókönyv: Hajtsa végre a távoli hibakeresés és a hiba kijavítása
1. Állítsa be a két használhatatlanná tévő pont, és válassza a **Debug** ikonra kattintva indítsa el a távoli hibakeresési folyamatot.

1. A kód az első jelentős pontot megáll, és a paraméter és a változó információk láthatók a **változók** ablaktáblán. 

1. Válassza ki a **folytatása Program** ikonra a folytatáshoz. A kód a második pont megáll. A kivétel elvárt módon.

   ![Throw hiba](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png) 

1. Válassza ki a **folytatása Program** újra ikonra. A **HDInsight Spark küldésének** ablakban a "feladat futtatása nem sikerült" hibaüzenet jelenik meg.

   ![Hiba beküldése](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png) 

1. Dinamikus frissítése a változó értékét a képesség Hibakeresés az IntelliJ használatával, jelölje be **Debug** újra. A **változók** ekkor újra megjelenik a panelen. 

1. Kattintson a jobb gombbal a cél a **Debug** lapra, és válassza ki **érték beállítása**. Írja be új értéket a változóhoz. Válassza ki **Enter** mentheti az értéket. 

   ![Érték beállítása](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value.png) 

1. Válassza ki a **folytatása Program** ikon továbbra is futtassa a programot. Ebben az esetben nincs kivétel történt. Láthatja, hogy a projekt sikeres futtatása kivételek nélkül.

   ![Kivétel nélkül hibakeresése](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Következő lépések
* [Áttekintés: Az Apache Spark on Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Bemutató
* (Videó) Scala-projekt létrehozása: [Az Apache Spark Scala-alkalmazások létrehozása](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Távoli hibakeresés (videó): [Távolról egy HDInsight-fürtön az Apache Spark-alkalmazások hibakeresése az IntelliJ-hez készült Azure eszközkészlet használata](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Forgatókönyvek
* [Az Apache Spark és BI: Interaktív adatelemzés végrehajtása a Spark on HDInsight használatával, BI-eszközökkel](apache-spark-use-bi-tools.md)
* [Az Apache Spark és Machine Learning: A Spark használata a HDInsight-hőmérséklet building HVAC-adatok elemzése](apache-spark-ipython-notebook-machine-learning.md)
* [Az Apache Spark és Machine Learning: A HDInsight Spark használata az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [A webhelynapló elemzése a HDInsight az Apache Spark használatával](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](../hdinsight-apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [IntelliJ-hez készült Azure eszközkészlet használata egy HDInsight-fürtön az Apache Spark-alkalmazások létrehozása](apache-spark-intellij-tool-plugin.md)
* [IntelliJ-hez készült Azure eszközkészlet használata Apache Spark-alkalmazások VPN-en keresztül távolról](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight Tools for IntelliJ with hortonworks – tesztkörnyezet használata](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [HDInsight Tools használata az Azure Toolkit for Eclipse Apache Spark-alkalmazások létrehozásához](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Az Apache Zeppelin notebookok használata a HDInsight Apache Spark-fürt](apache-spark-zeppelin-notebook.md)
* [Notebookokhoz elérhető kernelek Jupyter a HDInsight az Apache Spark-fürt](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
