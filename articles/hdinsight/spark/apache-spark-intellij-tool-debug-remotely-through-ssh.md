---
title: 'Azure Toolkit for IntelliJ: Debug Spark-alkalmazások SSH-val – HDInsight'
description: Részletes útmutatás a HDInsight-eszközök in Azure Toolkit for IntelliJ használatával az alkalmazások távoli hibakereséséhez a HDInsight-fürtökön az SSH-n keresztül
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/23/2019
ms.openlocfilehash: a012c3ce8f7c9e105a42d8383a502f3608c84070
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732908"
---
# <a name="debug-apache-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Apache Spark-alkalmazások hibakeresése HDInsight-fürtön az IntelliJ-hez készült Azure Toolkit for SSH segítségével

Ez a cikk lépésenként útmutatást nyújt a HDInsight-eszközök in [Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) használatával a HDInsight-fürtön lévő alkalmazások távoli hibakereséséhez. A projekt hibakereséséhez megtekintheti a [Debug HDInsight Spark-alkalmazásokat az Azure Toolkit for IntelliJ videóval.](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

## <a name="prerequisites"></a>Előfeltételek

* Apache Spark-fürt megléte a HDInsightban. Lásd: [Apache Spark-fürt létrehozása.](../spark/apache-spark-jupyter-spark-sql-use-portal.md)

* Windows-felhasználók számára: A helyi Spark Scala alkalmazás Windows rendszeren történő futtatása közben kivételt kaphat, ahogy azt a [SPARK-2356 ismerteti.](https://issues.apache.org/jira/browse/SPARK-2356) A kivétel oka az, hogy a WinUtils.exe hiányzik a Windows rendszerből.

    A hiba megoldásához töltse le a [Winutils.exe](https://github.com/steveloughran/winutils) webhelyre, például **a C:\WinUtils\bin mappába.** Ezután adja hozzá a **HADOOP_HOME**környezeti változót, és állítsa a változó értékét **C:\WinUtils értékre.**

* [IntelliJ IDEA](https://www.jetbrains.com/idea/download/#section=windows) (A közösségi kiadás ingyenes.).

* [Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/installation).

* [Scala plugin az IntelliJ](../spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea).

* Egy SSH-ügyfél. További információ: [Csatlakozás a HDInsighthoz (Apache Hadoop) az SSH használatával.](../hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="create-a-spark-scala-application"></a>Spark Scala-alkalmazás létrehozása

1. Indítsa el az IntelliJ IDEA programot, és válassza az **Új projekt létrehozása lehetőséget** az Új **projekt** ablak megnyitásához.

1. Válassza az **Apache Spark/HDInsight** lehetőséget a bal oldali ablaktáblában.

1. Válassza ki a **Spark-projekt minták (Scala)** a főablakban.

1. A **Build eszköz** legördülő listájában válasszon az alábbi a következők közül:

    * **Maven** a Scala projektkészítő varázsló támogatásához.
    * **SBT** a függőségek kezeléséhez és a Scala projekt létrehozásához.

     ![Intellij Új Project Spark létrehozása](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

1. Kattintson a **Tovább** gombra.

1. A következő **Új projekt** ablakban adja meg a következő információkat:

    |Tulajdonság |Leírás |
    |---|---|
    |Projektnév|Írjon be egy nevet. Ez a `myApp`séta használ .|
    |Projekt helye|Adja meg a kívánt helyet a projekt mentéséhez.|
    |Projekt SDK|Ha üres, válassza az **Új lehetőséget...** és keresse meg a JDK-t.|
    |Spark-verzió|A létrehozási varázsló integrálja a Spark SDK és a Scala SDK megfelelő verzióját. Ha a Spark-fürt verziója 2.0-nál korábbi, válassza a **Spark 1.x** lehetőséget. Ellenkező esetben válassza a **Spark 2.x. lehetőséget.** Ez a példa **a Spark 2.3.0 (Scala 2.11.8) .**|

   ![Intellij Új projekt válassza spark verzió](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

1. Válassza a **Finish** (Befejezés) elemet. Eltarthat néhány percig, amíg a projekt elérhetővé válik. Nézd meg a jobb alsó sarokban a haladást.

1. Bontsa ki a projektet, és keresse meg **az src** > **fő** > **scala** > **mintáját.** Kattintson duplán **a SparkCore_WasbIOTest**.

## <a name="perform-local-run"></a>Helyi futtatás végrehajtása

1. A **SparkCore_WasbIOTest** parancsfájlban kattintson a jobb gombbal a parancsfájlszerkesztőre, majd válassza a **"SparkCore_WasbIOTest" futtatása** lehetőséget a helyi futtatás végrehajtásához.

1. Miután a helyi futtatás befejeződött, láthatja a kimeneti fájl mentését az aktuális projektkezelő > **__adataihoz.__** **data**

    ![Intellij Project helyi futtatási eredménye](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-local-run-result.png)

1. Eszközeink a helyi futtatás és a helyi hibakeresés végrehajtásakor automatikusan beállították az alapértelmezett helyi futtatási konfigurációt. Nyissa meg a **konfigurációt [Spark on HDInsight] XXX** a jobb felső sarokban, láthatjuk a **[Spark on HDInsight]XXX** már létrehozott **Apache Spark a HDInsight**. Váltson a **Helyi futtatás** lapra.

    ![Intellij Futtatás a hibakeresési konfigurációk helyi futtatása](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)

    - [Környezeti változók](#prerequisites): Ha már beállította a **HADOOP_HOME** rendszerkörnyezeti változót **C:\WinUtils**értékre, automatikusan észleli, hogy nincs szükség manuális anamnézisre.
    - [WinUtils.exe hely:](#prerequisites)Ha nem állította be a rendszerkörnyezeti változót, a helyére kattintva megtalálhatja a helyet.
    - Csak válasszon két lehetőség közül, és nincs rájuk szükség MacOS és Linux rendszeren.

1. A konfigurációt manuálisan is beállíthatja a helyi futtatás és a helyi hibakeresés végrehajtása előtt. Az előző képernyőképen válassza ki**+** a pluszjelet ( ). Ezután válassza az **Apache Spark a HDInsight-on** lehetőséget. Adja meg a Menteni a **Név**, **A főosztály nevének** adatait, majd kattintson a helyi futtatás gombra.

## <a name="perform-local-debugging"></a>Helyi hibakeresés végrehajtása

1. Nyissa meg a **SparkCore_wasbloTest** parancsfájlt, állítsa be a töréspontokat.

1. Kattintson a jobb gombbal a parancsfájlszerkesztőre, majd a helyi hibakeresés végrehajtásához válassza a **Debug '[Spark on HDInsight]XXX"** lehetőséget.

## <a name="perform-remote-run"></a>Távoli futtatás végrehajtása

1. Keresse meg a **Konfigurációk** > **szerkesztése futtatását...**. Ebből a menüből létrehozhatja vagy szerkesztheti a távoli hibakeresés konfigurációit.

1. A **Futtatás/Hibakeresés konfigurációk** párbeszédpanelen jelölje ki**+** a pluszjelet ( ). Ezután válassza az **Apache Spark a HDInsight-on** lehetőséget.

   ![Intellij Új konfiguráció hozzáadása](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)

1. Váltson **a Távoli futtatás a Fürtlapon** lapra. **Main class name** **Name** **Spark cluster** Ezután kattintson **a Speciális konfiguráció (Távoli hibakeresés) gombra.** Eszközeink támogatják a debug **a végrehajtók**. A **numEctors**, az alapértelmezett érték 5. Jobb, ha nem állítasz 3-nál magasabbra.

   ![Intellij Hibakeresési konfigurációk futtatása](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. A **Speciális konfiguráció (távoli hibakeresés)** részben válassza a **Spark távoli hibakeresés engedélyezése**lehetőséget. Írja be az SSH felhasználónevét, majd írjon be egy jelszót, vagy használjon személyes kulcsfájlt. Ha távoli hibakeresést szeretne végezni, be kell állítania. Nincs szükség a beállításra, ha csak távoli futtatást szeretne használni.

   ![Intellij Speciális konfiguráció engedélyezése szikra távoli hibakeresés](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. A rendszer most menti a konfigurációt a megadott névvel. A konfiguráció részleteinek megtekintéséhez válassza ki a konfiguráció nevét. A módosításokhoz válassza a **Konfigurációk szerkesztése**lehetőséget.

1. A konfigurációs beállítások befejezése után futtathatja a projektet a távoli fürtön, vagy távoli hibakeresést hajthat végre.

   ![Intellij Debug Távoli Spark Feladat Távoli fuss gomb](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run-button.png)

1. Kattintson a **Kapcsolat bontása** gombra, amelyen a beküldési naplók nem jelennek meg a bal oldali panelen. Azonban még mindig fut a háttérben.

   ![Intellij Debug Távoli Spark Feladat Távoli fuss eredmény](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-remote-run-result.png)

## <a name="perform-remote-debugging"></a>Távoli hibakeresés végrehajtása

1. Állítsa be a töréspontokat, majd kattintson a **Távoli hibakeresés** ikonra. A különbség a távoli beküldés, hogy SSH felhasználónevet / jelszót kell konfigurálni.

   ![Az Intellij Debug Remote Spark Feladat hibakeresési ikonja](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. Amikor a program végrehajtása eléri a töréspontot, a **Hibakereső** ablaktáblán megjelenik egy **Illesztőprogram** és két **Végrehajtó** lap. A kód futtatásának folytatásához válassza a **Folytatás iont,** amely ezután eléri a következő töréspontot. Át kell váltania a megfelelő **Végrehajtó** lapra, hogy megtalálja a célvégrehajtót a hibakereséshez. A végrehajtási naplókat a megfelelő **Konzol** lapon tekintheti meg.

   ![Intellij Hibakeresés távoli Spark feladat hibakeresés lap](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="perform-remote-debugging-and-bug-fixing"></a>Távoli hibakeresés és hibajavítás végrehajtása

1. Állítson be két töréspontot, majd a Távoli hibakeresési folyamat elindításához kattintson a **Hibakeresés** ikonra.

1. A kód az első töréspontnál megáll, és a paraméter- és változóadatok a **Változók** ablaktáblában jelennek meg.

1. A folytatáshoz válassza a **Program folytatása** ikont. A kód megáll a második pontnál. A kivétel a várt módon lesz elkessben.

   ![Intellij Debug Távoli Spark Feladat dobja hiba](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png)

1. Ismét válassza a **Program folytatása** ikont. A **HDInsight Spark-küldés** ablak "sikertelen feladatfuttatása" hibaüzenetet jelenít meg.

   ![Intellij Debug Távoli Spark feladat hiba benyújtása](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png)

1. Ha dinamikusan szeretné frissíteni a változó értékét az IntelliJ hibakeresési funkcióval, válassza ismét a **Debug** lehetőséget. Ismét megjelenik **a Változók** ablaktábla.

1. Kattintson a jobb gombbal a célra a **Hibakeresés** lapon, és válassza az **Érték beállítása parancsot.** Ezután adjon meg egy új értéket a változóhoz. Ezután az Érték mentéséhez válassza az **Enter** lehetőséget.

   ![Intellij Debug Távoli Spark feladat készlet értéke](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value1.png)

1. A program futtatásához válassza a **Program folytatása** ikont. Ezúttal nincs kivétel. Láthatja, hogy a projekt kivétel nélkül sikeresen fut.

   ![Intellij Debug Távoli Spark feladat kivétel nélkül](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="next-steps"></a>További lépések

* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="demo"></a>Bemutató

* Scala-projekt létrehozása (videó): [Apache Spark Scala alkalmazások létrehozása](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Távoli hibakeresés (videó): [Az Azure Toolkit for IntelliJ használatával távolról debugot ihat az Apache Spark-alkalmazásokat egy HDInsight-fürtön](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Forgatókönyvek

* [Apache Spark bi-val: Interaktív adatelemzés végezhet a Spark használatával a HDInsightban az üzletiintelligencia-eszközökkel](apache-spark-use-bi-tools.md)
* [Apache Spark a Machine Learningsegítségével: A Spark használata a HDInsightban az épület hőmérsékletének elemzéséhez a fűtés- és légtechnikai adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learningsegítségével: A Spark használata a HDInsightban az élelmiszer-ellenőrzési eredmények előrejelzéséhez](apache-spark-machine-learning-mllib-ipython.md)
* [Webhelynapló-elemzés az Apache Spark használatával a HDInsightban](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása

* [Önálló alkalmazás létrehozása a Scala használatával](../hdinsight-apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények

* [Az Azure Toolkit for IntelliJ segítségével apache Spark-alkalmazásokat hozhat létre EGY HDInsight-fürthöz](apache-spark-intellij-tool-plugin.md)
* [Az Azure Toolkit for IntelliJ segítségével távolról debugolhat Apache Spark-alkalmazásokat VPN-en keresztül](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Spark-alkalmazások létrehozásához használja a HDInsight-eszközöket az Azure Eclipse eszközkészletében](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Apache Zeppelin-jegyzetfüzetek használata Apache Spark-fürttel a HDInsighton](apache-spark-zeppelin-notebook.md)
* [A Jupyter-jegyzetfüzethez elérhető kernelek a HDInsight-hoz létrehozott Apache Spark-fürtben](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése

* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
