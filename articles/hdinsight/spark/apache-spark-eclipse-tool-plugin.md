---
title: 'Azure Toolkit for Eclipse: Scala-alkalmazások létrehozása a HDInsight Sparkhoz'
description: Az Azure Toolkit for Eclipse HDInsight-eszközeivel scala nyelven írt Spark-alkalmazásokat fejleszthet, és elküldheti őket egy HDInsight Spark-fürtbe, közvetlenül az Eclipse IDE-ből.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/13/2019
ms.openlocfilehash: 07ed22879180d8126711eba9af0a2cac5b7b2953
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272109"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Az Azure Toolkit for Eclipse segítségével apache Spark-alkalmazásokat hozhat létre HDInsight-fürthöz

Az Azure Toolkit for [Eclipse](https://www.eclipse.org/) HDInsight-eszközeivel [scala](https://www.scala-lang.org/) nyelven írt [Apache Spark-alkalmazásokat](https://spark.apache.org/) fejleszthet, és elküldheti őket egy Azure HDInsight Spark-fürtbe, közvetlenül az Eclipse IDE-ből. A HDInsight Eszközök beépülő modult többféleképpen is használhatja:

* Scala Spark-alkalmazás fejlesztése és küldése hdinsight spark-fürtön.
* Az Azure HDInsight Spark-fürt erőforrásainak elérése.
* A Scala Spark-alkalmazás helyi fejlesztése és futtatása.

## <a name="prerequisites"></a>Előfeltételek

* Apache Spark-fürt a HDInsighton. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

* [Java Developer Kit (JDK) 8-as verzió](https://aka.ms/azure-jdks).

* [Eclipse IDE](https://www.eclipse.org/downloads/). Ez a cikk az Eclipse IDE for Java Developers-et használja.

## <a name="install-required-plug-ins"></a>A szükséges beépülő modulok telepítése

### <a name="install-azure-toolkit-for-eclipse"></a>Az Azure Toolkit for Eclipse telepítése

A telepítési útmutatót az [Azure Toolkit for Eclipse telepítése című](https://docs.microsoft.com/azure/java/eclipse/azure-toolkit-for-eclipse-installation)témakörben találja.

### <a name="install-the-scala-plug-in"></a>A Scala beépülő modul telepítése

Az Eclipse megnyitásakor a HDInsight-eszközök automatikusan észlelik, hogy telepítette-e a Scala beépülő modult. A folytatáshoz válassza az **OK gombot,** majd kövesse az utasításokat a beépülő modul eclipse-piactérről történő telepítéséhez. A telepítés befejezése után indítsa újra az IDE-t.

![A Scala beépülő modul automatikus telepítése](./media/apache-spark-eclipse-tool-plugin/auto-installation-scala1.png)

### <a name="confirm-plug-ins"></a>Beépülő modulok megerősítése

1. Keresse meg a **Súgó** > **Eclipse Piactér ...**.

1. Válassza a **Telepített** lapot.

1. Látnia kell legalább:
    * Az Azure Toolkit for Eclipse \<> verzió.
    * Scala \<IDE verzió>.

## <a name="sign-in-to-your-azure-subscription"></a>Bejelentkezés Azure-előfizetésbe

1. Indítsa el az Eclipse IDE-t.

1. Navigálás az **Ablak** >  **megtekintése** > **egyéb...**  >  **Jelentkezzen be. . .**

1. A **Nézet megjelenítése** párbeszédpanelen keresse meg az **Azure** > **Azure Explorer**t, és válassza a **Megnyitás**lehetőséget.

   ![Apache Spark Eclipse show nézet](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer1.png)

1. Az **Azure Explorerben**kattintson a jobb gombbal az **Azure-csomópontra,** majd válassza a **Bejelentkezés parancsot.**

1. Az **Azure Bejelentkezés** párbeszédpanelen válassza ki a hitelesítési módszert, válassza **a Bejelentkezés**lehetőséget, és fejezze be a bejelentkezési folyamatot.

   ![Apache Spark Eclipse Azure Sign](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer2.png)

1. Miután bejelentkezett, az **Előfizetések** párbeszédpanel felsorolja a hitelesítő adatokhoz társított összes Azure-előfizetést. A párbeszédpanel bezárásához nyomja le a **Kijelölés** gombot.

   ![Előfizetések kiválasztása párbeszédpanel](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)

1. Az **Azure Explorerből**keresse meg az **Azure** >  **HDInsight** ban, hogy megtekintse az előfizetése alatti HDInsight Spark-fürtöket.

   ![HDInsight Spark-fürtök az Azure Explorerben3](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer3.png)

1. Tovább bővítheti a fürt névcsomópontját a fürthöz társított erőforrások (például tárfiókok) megtekintéséhez.

   ![Fürtnév kibontása az erőforrások megtekintéséhez](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer4.png)

## <a name="link-a-cluster"></a>Fürt csatolása

Normál fürtaz Ambari felügyelt felhasználónévvel kapcsolható össze. Hasonlóképpen, a tartományhoz csatlakozó HDInsight-fürt esetén a tartomány és a `user1@contoso.com`felhasználónév használatával is összekapcsolható, például .

1. Az **Azure Explorer ben**kattintson a jobb gombbal a **HDInsight**elemre, és válassza **a Fürt csatolása parancsot.**

   ![Az Azure Explorer hivatkozási fürtjének menüje](./media/apache-spark-eclipse-tool-plugin/link-a-cluster-context-menu.png)

1. Írja be **a Fürtnév**, **A felhasználónév**és **a Jelszó**értéket, majd kattintson az **OK gombra.** Szükség esetén adja meg a Tárfiókot, a tárolókulcsot, majd válassza a Storage Container for Storage Explorer (Tárolótároló tárolótároló) lehetőséget a bal oldali fanézetben való munkához.

   ![Új HDInsight-fürt csatolása párbeszédpanel](./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog1.png)

   > [!NOTE]  
   > A csatolt tárolási kulcsot, felhasználónevet és jelszót akkor használjuk, ha a fürt az Azure-előfizetésben és a Fürthöz csatolt formában is bejelentkezett.
   > ![Azure Explorer tárfiókok](./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png)
   >
   > Ha csak a billentyűzet csak felhasználója, ha az aktuális fókusz a **Tárolóbillentyű,** a **Ctrl+TAB** billentyűkombinációval kell összpontosítania a párbeszédpanel következő mezőjére.

1. A csatolt fürt a **HDInsight csoportban**látható. Most már elküldhet egy alkalmazást ehhez a csatolt fürthöz.

   ![Azure Explorer hdi csatolt fürt](./media/apache-spark-eclipse-tool-plugin/hdinsight-linked-cluster.png)

1. A fürtöt le is választhatja az **Azure Explorerből.**

   ![Az Azure Explorer nem csatolt fürtje](./media/apache-spark-eclipse-tool-plugin/hdi-unlinked-cluster.png)

## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Spark Scala-projekt beállítása HDInsight Spark-fürthöz

1. Az Eclipse IDE munkaterületen válassza az**Új** >  **projekt fájlja** > **lehetőséget.**

1. Az **Új projekt** varázslóban válassza a HDInsight > **(Scala)** **HDInsight-projektspark**lehetőséget. Ezután válassza a **Tovább**gombot.

   ![A Spark kiválasztása a HDInsight (Scala) projekten](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)

1. Az **Új HDInsight Scala Projekt** párbeszédpanelen adja meg az alábbi értékeket, majd válassza a **Tovább**gombot:
   * Adjon meg a projekt nevét.
   * A **JRE** területen győződjön meg arról, hogy **a végrehajtási környezet használata JRE** van beállítva **JavaSE-1.7** vagy újabb.
   * A **Spark Library** területen a **Spark SDK-beállítás konfigurálásához a Maven használata** lehetőséget választhatja.  Eszközünk integrálja a Spark SDK és a Scala SDK megfelelő verzióját. A Spark **SDK hozzáadása manuálisan** is választhatja a beállítást, letöltheti és manuálisan hozzáadhatja a Spark SDK-t.

   ![Új HDInsight Scala Projekt párbeszédpanel](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)

1. A következő párbeszédpanelen tekintse át a részleteket, majd kattintson a **Befejezés gombra.**

## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Scala-alkalmazás létrehozása HDInsight Spark-fürthöz

1. A **Csomagkezelőből**bontsa ki a korábban létrehozott projektet. Kattintson a jobb gombbal **az src-re,** válassza **az Új** > **egyéb...** parancsot.

1. A **Varázsló kiválasztása** párbeszédpanelen válassza a **Scala Wizards** > **Scala Object lehetőséget.** Ezután válassza a **Tovább**gombot.

   ![Kijelölegy varázsló Scala objektum létrehozása](./media/apache-spark-eclipse-tool-plugin/create-scala-project1.png)

1. Az **Új fájl létrehozása** párbeszédpanelen adja meg az objektum nevét, majd kattintson a **Befejezés gombra.** Megnyílik egy szövegszerkesztő.

   ![Új fájl varázsló új fájl létrehozása](./media/apache-spark-eclipse-tool-plugin/create-scala-project2.png)

1. A szövegszerkesztőben cserélje le az aktuális tartalmat az alábbi kódra:

    ```scala
    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    object MyClusterApp{
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("MyClusterApp")
        val sc = new SparkContext(conf)

        val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        //find the rows that have only one digit in the seventh column in the CSV
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        rdd1.saveAsTextFile("wasbs:///HVACOut")
        }
    }
    ```

1. Futtassa az alkalmazást egy HDInsight Spark-fürtön:

   a. A Csomagkezelőben kattintson a jobb gombbal a projekt nevére, majd válassza **a Spark-alkalmazás küldése a HDInsightba parancsot.**

   b. A **Spark-küldés** párbeszédpanelen adja meg a következő értékeket, majd válassza a **Küldés**lehetőséget:

   * A **fürtneve,** válassza ki a HDInsight Spark-fürt, amelyen futtatni szeretné az alkalmazást.
   * Jelöljön ki egy műtárgyat az Eclipse projektből, vagy jelöljön ki egyet a merevlemezről. Az alapértelmezett érték attól függ, hogy a Csomagkezelőben a jobb gombbal melyik elemre kattint.
   * A **Főosztály név** legördülő listában a küldésvarázsló megjeleníti a projekt összes objektumnevét. Jelölje ki vagy írja be a futtatni kívánt at. Ha egy műtárgyat merevlemezről választott ki, manuálisan kell megadnia a főosztály nevét. 
   * Mivel ebben a példában az alkalmazáskód nem igényel parancssori argumentumokat, illetve hivatkozási szintű jars-t vagy fájlokat, a fennmaradó szövegmezőket üresen hagyhatja.

     ![Az Apache Spark beküldése párbeszédpanel](./media/apache-spark-eclipse-tool-plugin/create-scala-project3.png)

1. A **Spark-küldés** lapon meg kell kezdeni e haladást. Az alkalmazás leállításához válassza ki a piros gombot a **Spark-beküldés** ablakban. Megtekintheti az adott alkalmazás naplóit is, ha kiválasztja a földgömb ikont (amelyet a kép kék négyzete jelöl).

   ![Az Apache Spark beküldéseablak](./media/apache-spark-eclipse-tool-plugin/create-scala-project4.png)

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>HDInsight Spark-fürtök elérése és kezelése az Azure Eclipse eszközkészletének HDInsight-eszközeivel

Különböző műveleteket hajthat végre a HDInsight-eszközök használatával, beleértve a feladat kimenetének elérését is.

### <a name="access-the-job-view"></a>A feladatnézet elérése

1. Az **Azure Explorerben**bontsa ki a **HDInsight**, majd a Spark-fürt nevét, majd válassza a **Feladatok**lehetőséget.

   ![Az Azure Explorer Eclipse feladatnézet-csomópontja](./media/apache-spark-eclipse-tool-plugin/eclipse-job-view-node.png)

1. Válassza ki a **Feladatok** csomópontot. Ha a Java verzió alacsonyabb, mint **1,8**, a HDInsight Tools automatikusan emlékezteti az **E(fx)clipse** beépülő modul telepítésére. A folytatáshoz válassza **az OK gombot,** majd a varázsló követésével telepítse az Eclipse piactérről, majd indítsa újra az Eclipse programot.

   ![Telepítse a hiányzó plugin E(fx)clipse](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

1. Nyissa meg a Projekt nézetet a **Feladatok** csomópontról. A jobb oldali ablaktáblában a **Spark feladatnézete** lapon a fürtön futtatott összes alkalmazás jelenik meg. Válassza ki annak az alkalmazásnak a nevét, amelynek további részleteit szeretné látni.

   ![Az Apache Eclipse nézetfeladat-naplók részletei](./media/apache-spark-eclipse-tool-plugin/eclipse-view-job-logs.png)

   Ezután az alábbi műveletek bármelyikét végrehajthatja:

   * Mutasson a feladatgrafikonra. A futó feladatalapvető adatait jeleníti meg. Válassza ki a feladatgrafikont, és megtekintheti az egyes feladat által létrehozott szakaszokat és információkat.

     ![Apache Spark feladatgrafikon-diagramszínpadi információk](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * A **Napló** lapon megtekintheti a gyakran használt naplókat, például **a Driver Stderr,** **a Driver Stdout**és **a Directory Info naplót.**

     ![Apache Spark Eclipse feladatnapló adatai](./media/apache-spark-eclipse-tool-plugin/eclipse-job-log-info.png)

   * Nyissa meg a Spark előzményfelhasználói felületét és az Apache Hadoop YARN felhasználói felületét (az alkalmazás szintjén) az ablak tetején található hivatkozások kiválasztásával.

### <a name="access-the-storage-container-for-the-cluster"></a>A fürt tárolótárolójának elérése

1. Az Azure Explorerben bontsa ki a **HDInsight** gyökércsomópontját az elérhető HDInsight Spark-fürtök listájának megtekintéséhez.

1. Bontsa ki a fürt nevét a tárfiók és a fürt alapértelmezett tárolójának megtekintéséhez.

   ![Tárfiók és alapértelmezett tároló](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer5.png)

1. Válassza ki a fürthöz társított tárolótároló nevét. A jobb oldali ablaktáblában kattintson duplán a **HVACOut** mappára. Nyissa meg az egyik **részfájlt** az alkalmazás kimenetének megtekintéséhez.

### <a name="access-the-spark-history-server"></a>A Spark előzménykiszolgálójának elérése

1. Az Azure Explorerben kattintson a jobb gombbal a Spark-fürt nevére, majd válassza **a Spark-előzmények megnyitása felhasználói felületét.** Amikor a rendszer kéri, adja meg a fürt rendszergazdai hitelesítő adatait. Ezeket a fürt kiépítése során adta meg.

1. A Spark előzmények kiszolgáló irányítópultján az alkalmazás nevét használja a futásbefejezéséhez használt alkalmazás megkereséséhez. Az előző kódban az alkalmazás nevét `val conf = new SparkConf().setAppName("MyClusterApp")`a használatával állíthatja be. Tehát a Spark alkalmazás neve **MyClusterApp**volt.

### <a name="start-the-apache-ambari-portal"></a>Az Apache Ambari portál indítása

1. Az Azure Explorerben kattintson a jobb gombbal a Spark-fürt nevére, majd válassza **a Fürtkezelési portál megnyitása (Ambari) parancsot.**

1. Amikor a rendszer kéri, adja meg a fürt rendszergazdai hitelesítő adatait. Ezeket a fürt kiépítése során adta meg.

### <a name="manage-azure-subscriptions"></a>Azure-előfizetések kezelése

Alapértelmezés szerint az Azure Toolkit for Eclipse HDInsight-eszköze felsorolja az összes Azure-előfizetésből származó Spark-fürtöket. Szükség esetén megadhatja azokat az előfizetéseket, amelyekhez hozzá szeretne férni a fürthöz.

1. Az Azure Explorerben kattintson a jobb gombbal az **Azure** gyökércsomópontjára, majd válassza **az Előfizetések kezelése parancsot.**

1. A párbeszédpanelen törölje a jelet annak az előfizetésnek a jelölőnégyzetéből, amelyet nem szeretne elérni, majd kattintson a **Bezárás gombra.** **A Kijelentkezés** lehetőséget is választhatja, ha ki szeretne jelentkezni az Azure-előfizetésből.

## <a name="run-a-spark-scala-application-locally"></a>Spark Scala-alkalmazás helyi futtatása

Az Azure Toolkit for Eclipse HDInsight-eszközeivel helyileg futtathatja a Spark Scala-alkalmazásokat a munkaállomásán. Ezek az alkalmazások általában nem kell hozzáférni a fürt erőforrások, például egy tárolótároló, és futtathatja és tesztelheti őket helyileg.

### <a name="prerequisite"></a>Előfeltétel

Windows rendszerű számítógépen futtatja a helyi Spark Scala alkalmazást, ezért kivételt kaphat a [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356)című részben leírtak szerint. Ez a kivétel azért fordul elő, mert a **WinUtils.exe** hiányzik a Windows rendszerből.

A hiba megoldásához a [Winutils.exe](https://github.com/steveloughran/winutils) programnak a **C:\WinUtils\bin**mappához kell, majd adja hozzá a **környezeti változót HADOOP_HOME,** és állítsa a változó értékét **C\WinUtils**értékre.

### <a name="run-a-local-spark-scala-application"></a>Helyi Spark Scala-alkalmazás futtatása

1. Indítsa el az Eclipse programot, és hozzon létre egy projektet. Az **Új projekt** párbeszédpanelen adja meg a következő lehetőségeket, majd válassza a **Tovább**gombot.

1. Az **Új projekt** varázslóban válassza a **HDInsight Project** > **Spark lehetőséget a HDInsight helyi futtatási mintán (Scala)**. Ezután válassza a **Tovább**gombot.

   ![Az új projekt kijelöl egy varázslópárbeszédpanelt](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)

1. A projekt részleteinek megadásához kövesse a [3–6.](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster)

1. A sablon hozzáad egy mintakódot **(LogQuery)** az **src** mappához, amelyet helyileg futtathat a számítógépen.

   ![A LogQuery helyi scala alkalmazásának helye](./media/apache-spark-eclipse-tool-plugin/local-scala-application.png)

1. Kattintson a jobb gombbal **a LogQuery.scala** fájlra, és válassza **a** > Futtatás**1 Scala alkalmazásként parancsot.** Az ilyen kimenet a **Konzol** lapon jelenik meg:

   ![Spark-alkalmazás helyi futtatási eredménye](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="reader-only-role"></a>Csak olvasói szerepkör

Amikor a felhasználók csak olvasói szerepkör-engedéllyel rendelkező fürtnek küldik el a feladatot, ambari hitelesítő adatokra van szükség.

### <a name="link-cluster-from-context-menu"></a>Fürt csatolása a helyi menüből

1. Jelentkezzen be csak olvasószerepkör-fiókkal.

2. Az **Azure Explorerből**bontsa ki a **HDInsight-alkalmazást** az előfizetésében lévő HDInsight-fürtök megtekintéséhez. A **"Role:Reader"** jelölésű fürtök csak olvasói szerepkör-engedéllyel rendelkeznek.

    ![HDInsight Spark-fürtök az Azure Explorer szerepkör-olvasójában](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer6.png)

3. Kattintson a jobb gombbal a csak olvasószerepkör-engedéllyel rendelkező fürtre. Válassza **a Fürt csatolása a** helyi menüből a fürt összekapcsolására lehetőséget. Adja meg az Ambari felhasználónevét és jelszavát.

    ![HDInsight Spark-fürtök az Azure Explorerben hivatkozás](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer7.png)

4. Ha a fürt sikeresen kapcsolódik, a HDInsight frissül.
   A fürt szakasza összekapcsolódik.
  
    ![HDInsight Spark-fürtök az Azure Explorerben](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>Fürt összekapcsolása a Feladatok csomópont kibontásával

1. Kattintson **a Feladatok** csomópont ra, és megjelenik **a fürtfeladat-hozzáférés megtagadva** ablaka.

2. Kattintson **a Fürt csatolása** elemre a fürt összekapcsolására.

    ![HDInsight Spark-fürtök az Azure Explorerben9](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer9.png)

### <a name="link-cluster-from-spark-submission-window"></a>Fürt csatolása a Spark-küldés ablakból

1. Hozzon létre egy HDInsight-projektet.

2. Kattintson a jobb gombbal a csomagra. Ezután válassza **a Spark-alkalmazás küldése a HDInsightba**lehetőséget.

   ![HdInsight Spark-fürtök az Azure Explorerben](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer11.png)

3. Jelöljön ki egy fürtöt, amely csak olvasószerepkör-engedéllyel rendelkezik a **fürtnévhez.** Megjelenik egy figyelmeztető üzenet. **A Fürt csatolása** a fürt csatolásához lehetőségre kattintva.

   ![Az Azure Explorer BEN található HDInsight Spark-fürtök ezt a kapcsolatot](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer15.png)

### <a name="view-storage-accounts"></a>Tárfiókok megtekintése

* A csak olvasószerepkörrel rendelkező fürtök esetében kattintson **a Tárfiókok** csomópont elemre, és megjelenik **a Storage Access Denied** ablak.

   ![HDInsight Spark-fürtök az Azure Explorer tárhelyén](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer13.png)

   ![HdInsight Spark-fürtök megtagadva az Azure Explorerben](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer12.png)

* Csatolt fürtök esetén kattintson **a Tárfiókok** csomópont ra, és megjelenik a **Storage Access Denied** ablak.

   ![HdInsight Spark-fürtök megtagadva az Azure Explorerben2](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer14.png)

## <a name="known-problems"></a>Ismert problémák

Használata során **Link A Cluster**, Azt javasoljuk, hogy adja meg a hitelesítő adatokat a tárolás.

![fürt csatolása tárolóhitelesítő adatok napfogyatkozásával](./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png)

A feladatok elküldéséhez két mód létezik. Ha meg van adva a tárolási hitelesítő adatok, a feladat elküldéséhez kötegelt módban lesz a rendszer. Ellenkező esetben interaktív mód lesz használva. Ha a fürt foglalt, előfordulhat, hogy az alábbi hibaüzenet jelenik meg.

![napfogyatkozás hibaüzenetet kap, ha fürt foglalt](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png "napfogyatkozás hibaüzenetet kap, ha fürt foglalt")

![napfogyatkozás kap hiba, ha klaszter foglalt fonal](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png "napfogyatkozás kap hiba, ha klaszter foglalt fonal")

## <a name="see-also"></a>Lásd még

* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek

* [Apache Spark bi-val: Interaktív adatelemzés a Spark használatával a HDInsightban az üzletiintelligencia-eszközökkel](apache-spark-use-bi-tools.md)
* [Apache Spark gépi tanulással: Használja a Sparkot a HDInsightban az épület hőmérsékletének elemzéséhez a fűtés- és légtechnikai adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learningsegítségével: A Spark használata a HDInsightban az élelmiszer-ellenőrzési eredmények előrejelzéséhez](apache-spark-machine-learning-mllib-ipython.md)
* [Webhelynapló-elemzés az Apache Spark használatával a HDInsightban](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Alkalmazások létrehozása és futtatása

* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények

* [Spark Scala-alkalmazások létrehozásához és elküldéséhez használja az Azure Toolkit for IntelliJ-t](apache-spark-intellij-tool-plugin.md)
* [Az Azure Toolkit for IntelliJ segítségével távolról debugolhat Apache Spark-alkalmazásokat VPN-en keresztül](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Az Azure Toolkit for IntelliJ segítségével távolról debugelje az Apache Spark-alkalmazásokat az SSH-n keresztül](../hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Apache Zeppelin-jegyzetfüzetek használata Apache Spark-fürttel a HDInsighton](apache-spark-zeppelin-notebook.md)
* [A Jupyter notebookhoz elérhető kernelek az Apache Spark-fürtHDInsighthoz](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Erőforrások kezelése

* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
