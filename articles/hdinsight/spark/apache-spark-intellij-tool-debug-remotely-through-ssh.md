---
title: "Az IntelliJ Azure eszköztára: Debug Spark alkalmazások távolról SSH |} Microsoft Docs"
description: "Részletes útmutatás Azure eszköztára IntelliJ HDInsight eszközök segítségével távolról a HDInsight-alkalmazások hibakeresését fürtök SSH-n keresztül"
keywords: "Debug távolról intellij, távoli hibakeresés intellij, ssh, az intellij hdinsight, a debug intellij, hibakeresés"
services: hdinsight
documentationcenter: 
author: jejiang
manager: DJ
editor: Jenny Jiang
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 11/25/2017
ms.author: jejiang
ms.openlocfilehash: 6f9259ae5e8f382c6714d468004624c2cbcbbc33
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="debug-spark-applications-locally-or-remotely-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>A Spark-alkalmazások hibakeresését helyileg vagy távolról az Azure-eszközkészlet a HDInsight-fürtök az IntelliJ SSH-n keresztül

Ez a cikk nyújt részletes útmutatást Azure eszköztára IntelliJ HDInsight eszközök segítségével távolról a HDInsight-fürtök-alkalmazások hibakeresését. A projekt hibakeresési, megtekintheti a [az IntelliJ Debug HDInsight Spark Azure eszközkészlet alkalmazások](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) videó.

**Előfeltételek**
* **A HDInsight Tools az intellij-t Azure eszköztára**. Ez az eszköz az intellij-t Azure Toolkit részét képezi. További információkért lásd: [Azure eszközkészlet telepítése az IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation). És **IntelliJ Azure eszköztára**. Ez az eszközkészlet használata Spark-alkalmazások a HDInsight-fürtök létrehozása. További információkért kövesse az utasításokat a [használata Azure eszköztára Spark-alkalmazások a HDInsight-fürtök létrehozása az IntelliJ](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).

* **HDInsight SSH szolgáltatást a felhasználónév és jelszó felügyeleti**. További információkért lásd: [HDInsight (Hadoop) SSH használatával csatlakozhat](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) és [az SSH tunneling elérni az Ambari webes felhasználói felület, JobHistory, NameNode, Oozie és egyéb web UI](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 
## <a name="learn-how-to-perform-local-run-and-debugging"></a>Megtudhatja, hogyan hajtsa végre a helyi Futtatás és hibakeresés
### <a name="scenario-1-create-a-spark-scala-application"></a>1. forgatókönyv: A Spark Scala-alkalmazás létrehozása 

1. Indítsa el az IntelliJ IDEA, és ezután a projekt létrehozásához. Az a **új projekt** párbeszédpanelen tegye a következőket:

   a. Válassza ki **HDInsight**. 

   b. A Sablonválasztás Java vagy Scala a beállítások alapján. Az alábbi lehetőségek közül választhat:

      - **A Spark on HDInsight (Scala)**

      - **A Spark on HDInsight (Java)**

      - **A Spark on HDInsight minta (Scala)**

      Ez a példa egy **a Spark on HDInsight minta (Scala)** sablont.

   c. Az a **Build eszköz** listára, válassza ki, az igényeknek megfelelően az alábbiak valamelyikét:

      - **Maven**, Scala-projekt létrehozása varázsló támogatásához

      -  **SBT**, a függőségek kezelésére, és a Scala-projekt létrehozása 

      ![Hibakeresési-projekt létrehozása](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   d. Válassza ki **következő**.     
 
2. A következő **új projekt** ablakban tegye a következőket:

   ![Válassza ki a Spark SDK](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   a. Adja meg a projekt nevét és a projekt helyére.

   b. Az a **projekt SDK** legördülő listában válassza **Java 1.8** a **Spark 2.x** fürt, vagy válasszon **Java 1.7** a **Spark 1.x**  fürt.

   c. Az a **Spark verzió** legördülő listából válassza ki, a Scala-projekt létrehozása varázsló Spark SDK és Scala SDK integrálja a megfelelő verziójával. Ha a spark-fürt verziója korábbi, mint 2,0, válassza ki a **Spark 1.x**. Máskülönben válassza **Spark 2.x.** Ez a példa **Spark 2.0.2 (Scala 2.11.8)**.

   d. Válassza a **Finish** (Befejezés) elemet.

3. Válassza ki **src** > **fő** > **scala** az ebben a projektben nyissa meg a kódot. Ez a példa a **SparkCore_wasbloTest** parancsfájl.

### <a name="prerequisite-for-windows"></a>Előfeltételként szükséges windows
A helyi Spark Scala-alkalmazások Windows rendszerű számítógépeken futtatása, közben kivételt, kaphat, ahogy [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). A kivétel történt, mert WinUtils.exe hiányzik a Windows. 

Ez a hiba megoldásához [töltse le a végrehajtható fájl](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) egy olyan helyre, például **C:\WinUtils\bin**. Adja hozzá a következő környezeti változó **HADOOP_HOME**, és állítsa be a változó értékének **C:\WinUtils**.

### <a name="scenario-2-perform-local-run"></a>2. forgatókönyv: A helyi futtatás végrehajtása
1. Nyissa meg a **SparkCore_wasbloTest** parancsfájl, kattintson a jobb gombbal a parancsprogram-szerkesztő és a választhatja **futtassa a "[Spark feladat] XXX"** végrehajtásához a helyi Futtatás.
2. Egyszer helyi futtatás befejeződött, megjelenik a kimeneti fájlba menti az aktuális project explorer **adatok** > **__alapértelmezett__**.

    ![Helyi futtatás eredménye](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-result.png)
3. Az eszközök az alapértelmezett helyi futtatási konfigurációs automatikusan végrehajtásakor a helyi lefutott, és helyi hibakeresése van beállítva. Nyissa meg a konfigurációs **[Spark feladat] XXX** jobb felső sarokban, megtekintheti a **[Spark feladat] XXX** már létrehozott **Azure HDInsight Spark-feladatot**. Váltás **helyileg futtassa** fülre.

    ![Helyi Futtatás konfiguráció](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)
    - [Környezeti változók](#prerequisite-for-windows): Ha állította be a rendszer környezeti változó **HADOOP_HOME** való **C:\WinUtils**, azt is automatikus észleléséhez, hogy nem kell manuálisan adja hozzá.
    - [WinUtils.exe hely](#prerequisite-for-windows): Ha nem állított be a rendszer környezeti változó, a gombra kattintva a helyen található.
    - Csak válasszon a két lehetőség közül, és ezek nem szükségesek a MacOS és a Linux.
4. Beállíthatja a konfiguráció helyi lefutott, és helyi hibakeresési végrehajtása előtt manuálisan is. A fenti képernyőfelvételen látható, kattintson a plusz ikonra (**+**). Válassza ki a **Azure HDInsight Spark-feladatot** lehetőséget. Adja meg a információkat **neve**, **fő osztálynév** mentéséhez, majd kattintson a helyi Futtatás gombra.

### <a name="scenario-3-perform-local-debugging"></a>3. forgatókönyv: A helyi Hibakeresés végrehajtásához
1. Nyissa meg a **SparkCore_wasbloTest** parancsfájl, a set töréspontok.
2. Kattintson a jobb gombbal a parancsprogram-szerkesztő, és válassza ki a lehetőséget **Debug "[Spark feladat] XXX"** helyi Hibakeresés végrehajtásához.   



## <a name="learn-how-to-perform-remote-run-and-debugging"></a>Megtudhatja, hogyan hajtsa végre a távoli Futtatás és hibakeresés
### <a name="scenario-1-perform-remote-run"></a>1. forgatókönyv: Hajtsa végre a távoli Futtatás

1. Hozzáférés a **szerkesztése konfigurációk** menüben válassza ki a ikonra a jobb felső sarokban. Ebből a menüből is készít vagy szerkeszt a konfigurációk a távoli hibakereséshez.

   ![Konfigurációk szerkesztése](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png) 

2. Az a **Futtatás/Debug konfigurációk** párbeszédpanelen jelölje ki a plusz jelre (**+**). Válassza ki a **Azure HDInsight Spark-feladatot** lehetőséget.

   ![Adja hozzá az új konfiguráció](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)
3. Váltás **távolról ugyanúgy futtathatják a fürt** fülre. Adja meg a információkat **neve**, **Spark-fürt**, és **fő osztálynév**. Válassza ki **speciális konfigurációs**. Az eszközök támogatják a hibakeresési **végrehajtója**. A **numExectors**, az alapértelmezett érték 5. Jobb nem állíthat 3-nál nagyobb.

   ![Futtassa a hibakeresési konfigurációk](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

4. Az a **Spark küldésének speciális konfiguráció** párbeszédpanelen jelölje ki **engedélyezése Spark távoli hibakeresési**. Adja meg az SSH-felhasználónév, majd adjon meg egy jelszót vagy titkos kulcsfájlt. A konfiguráció mentéséhez, válassza ki a **OK**. Ha szeretne távoli hibakeresési végezni, akkor be kell. Nincs szükség az állítsa be, ha szeretné használni a távoli futtatása.

   ![Spark távoli hibakeresés engedélyezése](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

5. A konfiguráció most menti a megadott névvel. A konfigurációs részletek megtekintéséhez válassza ki a konfiguráció nevét. A módosításokat, válassza ki a **szerkesztése konfigurációk**. 

6. A konfigurációs beállítások elvégzése után a projekt futtatni a távoli fürt, vagy hajtsa végre a távoli hibakeresés.
   
   ![Távoli futási gomb](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run.png)

7. Ha nem szeretné a futó naplójában, a jobb oldali panelen, kattintson a **Disconnect** gombra. Azonban továbbra is fut a háttérkiszolgálón, és az eredmény megjelenik a bal oldali panelen.

   ![Távoli futási gomb](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/remote-run-result.png)



### <a name="scenario-2-perform-remote-debugging"></a>2. forgatókönyv: A távoli hibakeresés végrehajtásához
1. Állítsa be a legfrissebb pontok, és kattintson a **távoli hibakeresési** ikonra. A távoli küldésének a különbség, amelyen SSH felhasználónév/jelszó kell konfigurálni.

   ![Válassza ki a hibakeresési ikon](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

2. A program végrehajtását a legfrissebb pontot ér el, ha megjelenik egy **illesztőprogram** lapra, és két **végrehajtó** adhatja a **hibakereső** ablaktáblán. Válassza ki a **folytatása Program** ikonra kattintva továbbra is fut a kód, amely a következő töréspont ér el. A megfelelő vált **végrehajtó** lapon található a cél végrehajtó hibakereséséhez. A feladatvégrehajtási naplók tekintheti meg a megfelelő **konzol** fülre.

   ![Hibakeresés lap](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>3. forgatókönyv: Hajtsa végre a távoli hibakereséssel és a hiba elhárítása
1. Két legfrissebb pontok beállítása, majd válassza ki a **Debug** ikonra a távoli hibakeresési megkezdéséhez.

2. A kód nem az első legfrissebb ponton, és a paraméter és a változó információk jelennek meg a **változók** ablaktáblán. 

3. Válassza ki a **folytatása Program** ikon a folytatáshoz. A kódot a második ponton leáll. A kivétel várt módon.

   ![A throw hiba](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png) 

4. Válassza ki a **folytatása Program** újra ikonra. A **HDInsight Spark küldésének** ablak egy "feladat futtatása nem sikerült" hibaüzenet jelenik meg.

   ![Hibaüzenet küldése](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png) 

5. Dinamikus frissítése a változó értéke az IntelliJ-hibakeresés funkció használatával, jelölje be **Debug** újra. A **változók** ablaktáblán jelenik meg újra. 

6. Kattintson a jobb gombbal a cél a **Debug** lapra, majd válassza ki **érték beállítása**. Ezután adja meg az új értéket a változóhoz. Válassza ki **Enter** mentheti az értéket. 

   ![Érték beállítása](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value.png) 

7. Válassza ki a **folytatása Program** ikonra kattintva továbbra is futtassa a programot. Nincs kivétel ebben az esetben. Láthatja, hogy a projekt sikeresen kivételek nélkül futtatja.

   ![Kivétel nélkül hibakeresése](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Következő lépések
* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="demo"></a>Bemutató
* Hozzon létre Scala project (videó): [Spark Scala-alkalmazások létrehozása](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Távoli hibakeresési (videó): [IntelliJ a Spark-alkalmazások távolról a HDInsight-fürtök használata Azure eszköztára](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Forgatókönyvek
* [Spark és BI: interaktív adatelemzés végrehajtása a Spark hdinsight BI-eszközökkel](apache-spark-use-bi-tools.md)
* [Spark és Machine Learning: Spark on HDInsight HVAC-adatok épület-hőmérséklet elemzésére használata](apache-spark-ipython-notebook-machine-learning.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [Spark Streaming: Spark on HDInsight használata valós idejű streamelési alkalmazásokat hozhatnak létre](apache-spark-eventhub-streaming.md)
* [A webhelynapló elemzése a Spark on HDInsight használatával](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](../hdinsight-apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Spark-fürtön a Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [Az IntelliJ Azure eszköztára használata Spark-alkalmazások a HDInsight-fürtök létrehozása](apache-spark-intellij-tool-plugin.md)
* [Az intellij-t Azure eszközkészlet segítségével VPN-en keresztül távolról Spark-alkalmazások](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Használja a HDInsight Tools for IntelliJ a Hortonworks védőfal](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Az Eclipse Azure eszközkészlet a HDInsight Tools használatával Spark-alkalmazások létrehozása](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Zeppelin notebookok használata Spark-fürttel HDInsighton](apache-spark-zeppelin-notebook.md)
* [A HDInsight Spark-fürt Jupyter notebookokhoz elérhető kernelek](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
