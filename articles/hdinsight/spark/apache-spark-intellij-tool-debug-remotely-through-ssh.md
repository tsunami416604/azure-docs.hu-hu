---
title: 'Azure Toolkit for IntelliJ: Spark-alkalmazások hibakeresése SSH-HDInsight'
description: Részletes útmutató a HDInsight-eszközök használatáról a Azure Toolkit for IntelliJban az alkalmazások távoli HDInsight-fürtökön keresztül SSH-n keresztül történő hibakereséséhez
keywords: távoli IntelliJ hibakeresése, távoli hibakeresés IntelliJ, SSH, IntelliJ, hdinsight, hibakeresési IntelliJ, hibakeresés
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/25/2017
ms.openlocfilehash: 82aaead87fad0ed9fc7b715baf3dc5ebbd4941be
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494611"
---
# <a name="debug-apache-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Apache Spark-alkalmazások hibakeresése HDInsight-fürtön Azure Toolkit for IntelliJ SSH-n keresztül

Ez a cikk részletes útmutatást nyújt arról, hogyan használhatók a [Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) HDInsight eszközei az alkalmazások távoli HDInsight-fürtökön való hibakereséséhez. A projekt hibakereséséhez a [HDInsight Spark-alkalmazásokat Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) videóval is megtekintheti.

**Előfeltételek**
* **Azure Toolkit for IntelliJ HDInsight eszközei**. Ez az eszköz a Azure Toolkit for IntelliJ részét képezi. További információ: [Install Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation). És **Azure Toolkit for IntelliJ**. Ezzel az eszközkészlettel Apache Spark-alkalmazásokat hozhat létre egy HDInsight-fürthöz. További információért kövesse az [Azure Toolkit for IntelliJ használata Apache Spark alkalmazások HDInsight-fürthöz való létrehozásához](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin)című témakör utasításait.

* **HDINSIGHT SSH-szolgáltatást felhasználónévvel és jelszó-kezeléssel**. További információ: [Kapcsolódás a HDInsight (Apache Hadoop) az SSH használatával](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) és az [SSH-alagút használata a Ambari webes felhasználói felület, a JobHistory, a NameNode, az Apache Oozie és más webes felület eléréséhez](https://docs.microsoft.com/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 
## <a name="learn-how-to-perform-local-run-and-debugging"></a>Útmutató helyi Futtatás és hibakeresés végrehajtásához
### <a name="scenario-1-create-a-spark-scala-application"></a>1\. forgatókönyv: Spark Scala-alkalmazás létrehozása 

1. Indítsa el az IntelliJ IDEA-t, majd hozzon létre egy projektet. A **New Project** (Új projekt) párbeszédablakban hajtsa végre az alábbi lépéseket:

   a. Válassza az **Azure Spark/HDInsight**lehetőséget. 

   b. Válasszon ki egy Java-vagy Scala-sablont a kívánt beállítások alapján. Válasszon a következő lehetőségek közül:

   - **Spark-projekt (Java)**

   - **Spark-projekt (Scala)**

   - **Spark-projekt mintákkal (Scala)**

   - **Spark-projekt sikertelen feladat-hibakeresési mintákkal (előzetes verzió) (Scala)**

     Ez a példa egy **Spark-projektet használ a Samples (Scala)** sablonnal.

   c. A **Build tool** (Létrehozási eszköz) listában válassza ki az alábbiak közül az igényeinek megfelelőt:

   - **Maven**, a Scala projekt-létrehozás varázsló támogatásához.

   - **SBT**a Scala-projekt függőségeinek kezeléséhez és létrehozásához.

     ![IntelliJ – új projekt létrehozása Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   d. Kattintson a **Tovább** gombra.

1. A következő **új projekt** ablakban tegye a következőket:

   ![Új IntelliJ-projekt kiválasztása Spark-verzió](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   a. Adja meg a projekt nevét és a projekt helyét.

   b. A **Project SDK** legördülő listában válassza a **Java 1,8** a **Spark 2. x** fürthöz lehetőséget, vagy válassza a **Java 1,7** a **Spark 1. x** fürthöz lehetőséget.

   c. A **Spark-verzió** legördülő listában a Scala projekt létrehozása varázsló a Spark SDK és a Scala SDK megfelelő verzióját integrálja. Ha a Spark-fürt verziója 2,0-nál korábbi, válassza a **Spark 1. x**elemet. Ellenkező esetben válassza a **Spark 2. x elemet.** Ez a példa a következő verziót használja: **Spark 2.0.2 (Scala 2.11.8)** .

   d. Válassza a **Finish** (Befejezés) elemet.

1. Válassza a **src** > **Main** > **Scala** lehetőséget a kód a projektben való megnyitásához. Ez a példa a **SparkCore_wasbloTest** parancsfájlt használja.

### <a name="prerequisite-for-windows"></a>Előfeltételek a Windows rendszerhez
Ha a helyi Spark Scala alkalmazást Windows rendszerű számítógépen futtatja, előfordulhat, hogy a [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356)című részben leírtak szerint kivételt tapasztal. A kivétel oka, hogy a WinUtils. exe fájl hiányzik a Windows rendszerből.

A hiba elhárításához [töltse le a végrehajtható fájlt](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) egy olyan helyre, mint például a **C:\WinUtils\bin**. Ezután adja hozzá a **HADOOP_HOME**környezeti változót, és állítsa a változó értékét **C:\WinUtils**értékre.

### <a name="scenario-2-perform-local-run"></a>2\. forgatókönyv: helyi futtatás végrehajtása

1. Nyissa meg a **SparkCore_wasbloTest** parancsfájlt, kattintson a jobb gombbal a parancsfájl-szerkesztőre, majd válassza a **"[Spark Job] xxx"** parancsot a helyi Futtatás végrehajtásához.

1. A helyi Futtatás befejezése után a kimeneti fájl mentése a jelenlegi Project Explorer- **adatokat** >  **__alapértelmezett értékre__** .

    ![IntelliJ projekt helyi futtatásának eredménye](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-local-run-result.png)

1. Az eszközök a helyi Futtatás és a helyi hibakeresés végrehajtásakor automatikusan beállították az alapértelmezett helyi futtatási konfigurációt. A jobb felső sarokban található **[Spark on HDInsight] XXX** megnyitásával megtekintheti a **[Spark on HDInsight] XXX** -t, amelyet a **HDInsight**-ben már Apache Spark létrehoztak. Váltson a **helyi Futtatás** lapra.

    ![IntelliJ helyi futtatású hibakeresési konfigurációk futtatása](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)

    - [Környezeti változók](#prerequisite-for-windows): Ha már beállította a **HADOOP_HOME** rendszerkörnyezeti változót a **C:\WinUtils**-re, akkor az automatikusan felismeri, hogy nincs szükség a manuális hozzáadására.
    - [WinUtils. exe helye](#prerequisite-for-windows): Ha nem állította be a rendszerkörnyezeti változót, a gombra kattintva megkeresheti a helyet.
    - Csupán két lehetőség közül választhat, és a MacOS és a Linux rendszeren nem szükséges.

1. A konfigurációt manuálisan is beállíthatja a helyi Futtatás és a helyi hibakeresés végrehajtása előtt. Az előző képernyőképen válassza ki a plusz jelet ( **+** ). Ezután válassza ki a **Apache Spark a HDInsight** beállításnál. Adja meg a **név**, a **fő osztály nevét** , a menteni kívánt adatokat, majd kattintson a helyi Futtatás gombra.

### <a name="scenario-3-perform-local-debugging"></a>3\. forgatókönyv: helyi hibakeresés végrehajtása
1. Nyissa meg a **SparkCore_wasbloTest** parancsfájlt, és állítsa be a töréspontokat.
1. A helyi hibakeresés végrehajtásához kattintson a jobb gombbal a parancsfájl-szerkesztőre, majd válassza a **"[Spark on HDInsight] xxx"** lehetőséget.

## <a name="learn-how-to-perform-remote-run-and-debugging"></a>Útmutató a távoli Futtatás és a hibakeresés végrehajtásához
### <a name="scenario-1-perform-remote-run"></a>1\. forgatókönyv: távoli futtatás végrehajtása

1. A **konfigurációk szerkesztése** menü megnyitásához kattintson a jobb felső sarokban található ikonra. Ebből a menüből létrehozhat vagy szerkesztheti a távoli hibakeresés konfigurációit.

   ![HDI IntelliJ-szerkesztési konfigurációk](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png)

1. A **Futtatás/hibakeresés konfiguráció** párbeszédpanelen válassza a pluszjelet ( **+** ). Ezután válassza ki a **Apache Spark a HDInsight** beállításnál.

   ![IntelliJ új konfiguráció hozzáadása](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)

1. Váltson **távoli futtatásra a fürt** lapon. adja meg a **név**, a **Spark-fürt**és a **fő osztály nevét**. Ezután kattintson a **Speciális konfiguráció (távoli hibakeresés)** elemre. Eszközeink támogatják a **végrehajtókkal**való hibakeresést. A **numExectors**az alapértelmezett érték 5. Nagyobb, mint 3.

   ![Hibakeresési konfigurációk futtatása IntelliJ](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. A **Speciális konfiguráció (távoli hibakeresés)** részben válassza a **Spark távoli hibakeresés engedélyezése**lehetőséget. Adja meg az SSH-felhasználónevet, majd írjon be egy jelszót, vagy használjon titkos kulcsot tartalmazó fájlt. Ha távoli hibakeresést szeretne végezni, be kell állítania azt. Ha csak a távoli futtatást szeretné használni, nem kell beállítania.

   ![IntelliJ speciális konfiguráció a Spark távoli hibakeresésének engedélyezése](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. A konfiguráció mostantól a megadott névvel lett mentve. A konfiguráció részleteinek megtekintéséhez válassza ki a konfiguráció nevét. A módosítások elvégzéséhez válassza a **konfigurációk szerkesztése**lehetőséget.

1. A konfigurációk beállításainak elvégzése után futtathatja a projektet a távoli fürtön, vagy távoli hibakeresést végezhet.

   ![IntelliJ hibakeresése távoli Spark-feladatok távoli Futtatás gombja](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run-button.png)

1. Kattintson a **Leválasztás** gombra, hogy a beküldési naplók ne jelenjenek meg a bal oldali panelen. Azonban továbbra is fut a háttérön.

   ![IntelliJ hibakeresése távoli Spark-feladatok távoli futtatásának eredménye](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-remote-run-result.png)

### <a name="scenario-2-perform-remote-debugging"></a>2\. forgatókönyv: távoli hibakeresés végrehajtása
1. Állítsa be a törési pontokat, majd kattintson a **távoli hibakeresés** ikonra. A távoli beküldéssel való különbség az, hogy az SSH-felhasználónevet/jelszót be kell állítani.

   ![IntelliJ hibakeresése távoli Spark-feladatok hibakeresési ikonja](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. Amikor a program végrehajtása eléri a feltörési pontot, megjelenik egy **illesztőprogram** lap és két **végrehajtó** lap a **hibakereső** ablaktáblán. Válassza a **program folytatása** ikont a kód futtatásának folytatásához, amely ezután eléri a következő töréspontot. A hibakereséshez a megfelelő **végrehajtó** lapra kell váltania. A végrehajtási naplókat a megfelelő **konzol** lapon tekintheti meg.

   ![IntelliJ hibakeresése távoli Spark-feladatok hibakeresése lap](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>3\. forgatókönyv: távoli hibakeresés végrehajtása és hibajavítás

1. Állítson be két töréspontot, majd válassza a **hibakeresés** ikont a Távoli hibakeresési folyamat elindításához.

1. A kód az első feltörési ponton leáll, és a paraméterek és a változó információk a **változók** ablaktáblán jelennek meg.

1. A folytatáshoz válassza a **program folytatása** ikont. A kód a második ponton leáll. A kivételt a rendszer a várt módon kapja meg.

   ![IntelliJ hibakeresése távoli Spark-feladatokkal – hiba](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png)

1. Válassza újra a **program folytatása** ikont. A **HDInsight Spark beküldési** ablakában a "sikertelen feladatok futtatása" hibaüzenet jelenik meg.

   ![IntelliJ hibakeresése távoli Spark-feladatokkal kapcsolatos hibák elküldése](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png)

1. Ha a IntelliJ hibakeresési funkciójával szeretné dinamikusan frissíteni a változó értékét, válassza a **hibakeresés** újra lehetőséget. A **változók** ablaktábla ismét megjelenik.

1. Kattintson a jobb gombbal a cél elemre a **hibakeresés** lapon, majd válassza az **érték beállítása**lehetőséget. Ezután adjon meg egy új értéket a változóhoz. Ezután válassza az **ENTER billentyűt** az érték mentéséhez.

   ![IntelliJ hibakeresése távoli Spark-feladatütemezés értéke](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value1.png)

1. Válassza a **program folytatása** ikont a program futtatásának folytatásához. Ezúttal egyetlen kivétel sem kerül kifogásra. Láthatja, hogy a projekt kivételek nélkül fut sikeresen.

   ![IntelliJ hibakeresése távoli Spark-feladatokhoz kivétel nélkül](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Következő lépések

* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="demo"></a>Bemutató
* Scala-projekt létrehozása (videó): [Apache Spark Scala-alkalmazások létrehozása](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Távoli hibakeresés (videó): a [Azure Toolkit for IntelliJ használata Apache Spark alkalmazások távoli hibakereséséhez egy HDInsight-fürtön](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Alkalmazási helyzetek
* [Apache Spark BI: interaktív adatelemzés végrehajtása a Spark on HDInsight és a BI Tools használatával](apache-spark-use-bi-tools.md)
* [Apache Spark a Machine Learningrel: a Spark in HDInsight használatával elemezze az építési hőmérsékletet a HVAC-adataival](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learning használatával: az élelmiszer-ellenőrzési eredmények előrejelzéséhez használja a Spark in HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Webhely-naplózási elemzés Apache Spark használatával a HDInsight-ben](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](../hdinsight-apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [Azure Toolkit for IntelliJ használata Apache Spark-alkalmazások HDInsight-fürthöz való létrehozásához](apache-spark-intellij-tool-plugin.md)
* [Apache Spark-alkalmazások távoli hibakeresése a Azure Toolkit for IntelliJ használatával VPN-en keresztül](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Spark alkalmazások létrehozásához használja a Azure Toolkit for Eclipse HDInsight-eszközeit](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Apache Zeppelin notebookok használata Apache Spark-fürttel a HDInsight-on](apache-spark-zeppelin-notebook.md)
* [Apache Spark a Jupyter notebookhoz elérhető kernelek a HDInsight-fürtön a](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
