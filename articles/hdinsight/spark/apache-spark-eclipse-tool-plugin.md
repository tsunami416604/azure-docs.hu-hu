---
title: 'Azure Toolkit for Eclipse: Scala-alkalmazások létrehozása a HDInsight Sparkhoz '
description: Az Azure Toolkit for Eclipse HDInsight eszközeivel fejlesztheti a Scalaban írt Spark-alkalmazásokat, és beküldheti azokat egy HDInsight Spark-fürtbe közvetlenül az Eclipse IDE-ből.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/30/2017
ms.openlocfilehash: 1ebea83d87cd169f61c91a93f092fa277f5017a7
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203800"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Azure Toolkit for Eclipse használata Apache Spark-alkalmazások HDInsight-fürthöz való létrehozásához

Az [Eclipse](https://www.eclipse.org/) -hez készült Azure Toolkit HDInsight eszközeivel fejlesztheti [Apache Spark](https://spark.apache.org/) a [scalaban](https://www.scala-lang.org/) írt alkalmazásokat, és beküldheti azokat egy Azure HDInsight Spark-fürtbe közvetlenül az Eclipse ide-ből. A HDInsight-eszközök beépülő modult többféleképpen is használhatja:

* Scala Spark-alkalmazás fejlesztése és beküldése egy HDInsight Spark-fürtön.
* A Azure HDInsight Spark-fürt erőforrásainak eléréséhez.
* Egy Scala Spark-alkalmazás helyi fejlesztése és futtatása.

> [!IMPORTANT]  
> Ezzel az eszközzel csak a Linux rendszerű HDInsight Spark-fürtökhöz hozhat létre és küldhet alkalmazásokat.

## <a name="prerequisites"></a>Előfeltételek

* Apache Spark fürt a HDInsight-on. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).
* Az Oracle Java Development Kit 8-as verziója, amely az Eclipse IDE futtatókörnyezethez használatos. Letöltheti az [Oracle webhelyéről](https://aka.ms/azure-jdks).
* Eclipse IDE. Ez a cikk az Eclipse neont használja. Telepítheti az [Eclipse webhelyéről](https://www.eclipse.org/downloads/).

## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse-and-the-scala-plug-in"></a>HDInsight-eszközök telepítése Azure Toolkit for Eclipse és a Scala beépülő modulban

### <a name="install-azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse telepítése

Az Eclipse-hez készült HDInsight Tools a Azure Toolkit for Eclipse részeként érhető el. A telepítési utasításokért lásd: [Azure Toolkit for Eclipse telepítése](https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-installation).

### <a name="install-the-scala-plug-in"></a>A Scala beépülő modul telepítése

Az Eclipse megnyitásakor a HDInsight eszköz automatikusan észleli, hogy telepítette-e a Scala beépülő modult. A folytatáshoz kattintson az **OK** gombra, majd kövesse az utasításokat a beépülő modul az Eclipse piactérről való telepítéséhez.

![A Scala beépülő modul automatikus telepítése](./media/apache-spark-eclipse-tool-plugin/auto-installation-scala1.png)

A felhasználó [bejelentkezhet az Azure-előfizetésbe](#sign-in-to-your-azure-subscription), vagy [összekapcsolhat egy HDInsight-fürtöt](#link-a-cluster) a Ambari Felhasználónév/jelszó vagy a tartományhoz csatlakoztatott hitelesítő adatok használatával a kezdéshez.

## <a name="sign-in-to-your-azure-subscription"></a>Jelentkezzen be az Azure-előfizetésébe

1. Indítsa el az Eclipse IDE-t, és nyissa meg az Azure Explorert. Az **ablak** menüben válassza a **nézet megjelenítése**lehetőséget, majd válassza az **egyéb**lehetőséget. A megnyíló párbeszédpanelen bontsa ki az **Azure**elemet, válassza az **Azure Explorer**lehetőséget, majd kattintson **az OK gombra**.

   ![Apache Spark Eclispse nézet megjelenítése](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer1.png)

1. Kattintson a jobb gombbal az **Azure** -csomópontra, majd válassza a **Bejelentkezés**lehetőséget.
1. Az **Azure bejelentkezési** párbeszédpanelen válassza ki a hitelesítési módszert, válassza a **Bejelentkezés**lehetőséget, és adja meg az Azure-beli hitelesítő adatait.

   ![Apache Spark Eclispse Azure-bejelentkezés](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer2.png)

1. Miután bejelentkezett, az **előfizetések kiválasztása** párbeszédpanel felsorolja a hitelesítő adatokhoz társított összes Azure-előfizetést. A párbeszédpanel bezárásához kattintson a **kiválasztás** gombra.

   ![Előfizetések kiválasztása párbeszédpanel](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)

1. Az **Azure Explorer** lapon bontsa ki a **HDInsight** elemet, és tekintse meg az előfizetéshez tartozó HDInsight Spark-fürtöket.

   ![HDInsight Spark-fürtök az Azure Explorer3](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer3.png)

1. A fürthöz társított erőforrások (például Storage-fiókok) megtekintéséhez tovább bővítheti a fürt neve csomópontot.

   ![A fürt nevének kibontása az erőforrások megjelenítéséhez](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer4.png)

## <a name="link-a-cluster"></a>Fürt csatolása

A Ambari által felügyelt Felhasználónév használatával összekapcsolhatja a normál fürtöt. Hasonlóképpen, a tartományhoz csatlakoztatott HDInsight-fürtökhöz a tartomány és a Felhasználónév használatával is kapcsolódhat, például `user1@contoso.com`:.

1. Válassza **a fürt csatolása** az **Azure Explorerben**lehetőséget.

   ![fürt helyi menüjének csatolása](./media/apache-spark-eclipse-tool-plugin/link-a-cluster-context-menu.png)

1. Adja meg a **fürt nevét**, a **felhasználónevet** és a **jelszót**, majd kattintson az OK gombra a fürt csatolásához. Szükség esetén megadhatja a Storage-fiókot, a Storage-kulcsot, majd kiválaszthatja a Storage Explorer tárolási tárolóját a bal oldali fanézetben való működéshez.

   ![fürt csatolása párbeszédpanel](./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog1.png)

   > [!NOTE]  
   > A csatolt Storage-kulcsot, a felhasználónevet és a jelszót használjuk, ha a fürt az Azure-előfizetésben is be van jelentkezve, és egy fürthöz kapcsolódik.
   > ![Storage Explorer az Eclipse-ben](./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png)

1. Ha a bemeneti adatok helyesek, az OK gombra kattintás után megtekintheti a csatolt fürtöt a **HDInsight** -csomópontban. Most elküldheti az alkalmazást ehhez a csatolt fürthöz.

   ![csatolt fürt](./media/apache-spark-eclipse-tool-plugin/hdinsight-linked-cluster.png)

1. A fürtöket az **Azure Explorerben**is leválaszthatja.

   ![nem összekapcsolt fürt](./media/apache-spark-eclipse-tool-plugin/hdi-unlinked-cluster.png)

## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Spark Scala-projekt beállítása HDInsight Spark-fürthöz

1. Az Eclipse IDE munkaterületen válassza a **fájl**lehetőséget, válassza az **új**, majd a **projekt**elemet.

1. Az új projekt varázslóban bontsa ki a **HDInsight**csomópontot, válassza a **Spark on HDInsight (Scala)** elemet, majd kattintson a **tovább**gombra.

   ![A Spark on HDInsight (Scala) projekt kiválasztása](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)

1. A Scala projekt létrehozása varázsló automatikusan észleli, hogy telepítette-e a Scala beépülő modult. A Scala beépülő modul letöltésének folytatásához kattintson az **OK gombra** , majd kövesse az utasításokat az Eclipse újraindításához.

   ![Scala-vizsgálat](./media/apache-spark-eclipse-tool-plugin/auto-installation-scala2.png)

1. Az **új HDInsight Scala projekt** párbeszédpanelen adja meg a következő értékeket, majd válassza a Next ( **tovább**) gombot:
   * Adja meg a projekt nevét.
   * A **JRE** területen győződjön meg arról, hogy a **végrehajtási környezeti JRE használata** **javase-1,7** vagy újabb értékre van beállítva.
   * A **Spark-könyvtár** területen válassza a Maven használata lehetőséget a **Spark SDK konfigurálásához** .  Az eszköz a Spark SDK és a Scala SDK megfelelő verzióját integrálja. Választhatja a **Spark SDK manuális hozzáadása** lehetőséget is, és manuálisan is letöltheti és hozzáadhatja a Spark SDK-t.

   ![Új HDInsight Scala-projekt párbeszédpanel](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)

1. A következő párbeszédpanelen válassza a **Befejezés**lehetőséget.

## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Scala-alkalmazás létrehozása HDInsight Spark-fürthöz

1. Az Eclipse IDE-ből válassza ki a Package Explorer alkalmazást, és bontsa ki a korábban létrehozott projektet, kattintson a jobb gombbal az **src**elemre, mutasson az **új**elemre, majd válassza az **egyéb**lehetőséget.

1. A **varázsló kiválasztása** párbeszédpanelen bontsa ki a **Scala varázslók**elemet, válassza a **Scala objektum**lehetőséget, majd kattintson a **tovább**gombra.

   ![Varázsló kiválasztása párbeszédpanel](./media/apache-spark-eclipse-tool-plugin/create-scala-project1.png)
1. Az **új fájl létrehozása** párbeszédpanelen adja meg az objektum nevét, majd kattintson a **Befejezés gombra**.

   ![Új fájl létrehozása párbeszédpanel](./media/apache-spark-eclipse-tool-plugin/create-scala-project2.png)
1. Illessze be a következő kódot a szövegszerkesztőbe:

    ```scala
    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    object MyClusterApp{
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("MyClusterApp")
        val sc = new SparkContext(conf)

        val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        //find the rows that have only one digit in the seventh column in the CSV
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        rdd1.saveAsTextFile("wasb:///HVACOut")
        }        
    }
    ```

1. Futtassa az alkalmazást egy HDInsight Spark-fürtön:

   a. A Package Explorerben kattintson a jobb gombbal a projekt nevére, majd válassza a **Spark-alkalmazás elküldése a HDInsight**lehetőséget.

   b. A **Spark beküldése** párbeszédpanelen adja meg a következő értékeket, majd válassza a **Submit (Küldés**) lehetőséget:

   * A **fürt neve**mezőben válassza ki azt a HDInsight Spark-fürtöt, amelyen futtatni szeretné az alkalmazást.
   * Válasszon ki egy összetevőt az Eclipse-projektből, vagy válasszon egy merevlemezből. Az alapértelmezett érték attól függ, hogy melyik elemre kattint a jobb gombbal a Package Explorerben.
   * A **Főosztály neve** legördülő listában a beküldési varázsló megjeleníti a projekt összes objektumának nevét. Válassza ki vagy adja meg az egyiket, amelyet futtatni szeretne. Ha az összetevőt egy merevlemezről választotta, akkor manuálisan kell megadnia a fő osztály nevét. 
   * Mivel ebben a példában az alkalmazás kódjának nincs szüksége parancssori argumentumokra vagy hivatkozási Tégelyekre vagy fájlokra, a fennmaradó szövegmezőket üresen hagyhatja.

     ![Spark beküldése párbeszédpanel](./media/apache-spark-eclipse-tool-plugin/create-scala-project3.png)

1. A **Spark beküldési** lapján meg kell kezdeni a folyamat megjelenítését. Az alkalmazás leállításához kattintson a **Spark beküldési** ablakban a vörös gombra. Megtekintheti az adott alkalmazáshoz tartozó naplókat is, ha kiválasztja a földgömb ikont (a képen a kék mező jelzi).

   ![Spark-beküldési ablak](./media/apache-spark-eclipse-tool-plugin/create-scala-project4.png)

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>HDInsight Spark-fürtök elérése és kezelése a Azure Toolkit for Eclipse HDInsight eszközeinek használatával

A HDInsight-eszközök használatával különböző műveleteket hajthat végre, beleértve a feladatok kimenetének elérését is.

### <a name="access-the-job-view"></a>A feladatok nézet elérése

1. Az Azure Explorerben bontsa ki a **HDInsight**csomópontot, bontsa ki a Spark-fürt nevét, majd válassza a **feladatok**lehetőséget.

   ![Nyomtatási nézet csomópontja](./media/apache-spark-eclipse-tool-plugin/eclipse-job-view-node.png)

1. Válassza a **feladatok** csomópontot. Ha a Java-verzió a **1,8**-nál kisebb, a HDInsight-eszközök automatikusan emlékeztetik az **E (FX) klipek** beépülő moduljának telepítésére. A folytatáshoz kattintson az **OK gombra** , majd kövesse a varázslót az Eclipse piactéren való telepítéshez és az Eclipse újraindításához. 

   ![Az E (FX) klipek telepítése](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

1. Nyissa meg a feladat nézetet a **feladatok** csomópontból. A jobb oldali ablaktáblán a **Spark-feladatok nézet** lapon a fürtön futó összes alkalmazás látható. Válassza ki annak az alkalmazásnak a nevét, amelyhez további részleteket szeretne látni.

   ![Alkalmazás adatai](./media/apache-spark-eclipse-tool-plugin/eclipse-view-job-logs.png)

   Ezután a következő műveletek bármelyikét elvégezheti:

   * Vigye a kurzort a feladatok gráfra. A futó feladatra vonatkozó alapszintű információkat jeleníti meg. Válassza ki a feladathoz tartozó gráfot, és tekintse meg az egyes feladatok által generált szakaszokat és információkat.

     ![Apache Spark a feladatok gráf szakaszának adatai](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * A **napló** lapon megtekintheti a gyakran használt naplókat, beleértve az **illesztőprogram-stderr**, az **illesztőprogram StdOut**-ját és a **címtár-információkat**.

     ![Napló részletei](./media/apache-spark-eclipse-tool-plugin/eclipse-job-log-info.png)

   * Nyissa meg a Spark előzmények felhasználói felületét és a Apache Hadoop fonal felhasználói felületét (az alkalmazás szintjén) az ablak tetején található hiperhivatkozások kiválasztásával.

### <a name="access-the-storage-container-for-the-cluster"></a>A fürt tárolójának elérése

1. Az Azure Explorerben bontsa ki a **HDInsight** csomópontot, és tekintse meg az elérhető HDInsight Spark-fürtök listáját.

1. Bontsa ki a fürt nevét a Storage-fiók és a fürt alapértelmezett tárolójának megtekintéséhez.

   ![Storage-fiók és alapértelmezett tároló](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer5.png)

1. Válassza ki a fürthöz társított Storage-tároló nevét. A jobb oldali ablaktáblán kattintson duplán a **HVACOut** mappára. Az alkalmazás kimenetének megjelenítéséhez nyissa meg az egyik **rész –** fájlok lehetőséget.

### <a name="access-the-spark-history-server"></a>A Spark-előzmények kiszolgálójának elérése

1. Az Azure Explorerben kattintson a jobb gombbal a Spark-fürt nevére, majd válassza a **Spark-előzmények megnyitása felhasználói felület**lehetőséget. Amikor a rendszer kéri, adja meg a fürt rendszergazdai hitelesítő adatait. Ezeket a fürt kiépítés közben adta meg.

1. A Spark verziótörténete-kiszolgáló irányítópultján az alkalmazás neve segítségével keresse meg az éppen futó alkalmazást. Az előző kódban a paranccsal `val conf = new SparkConf().setAppName("MyClusterApp")`állíthatja be az alkalmazás nevét. Így a Spark-alkalmazás neve **MyClusterApp**volt.

### <a name="start-the-apache-ambari-portal"></a>Az Apache Ambari portál elindítása

1. Az Azure Explorerben kattintson a jobb gombbal a Spark-fürt nevére, majd válassza a **fürt felügyeleti portál megnyitása (Ambari)** lehetőséget.

1. Amikor a rendszer kéri, adja meg a fürt rendszergazdai hitelesítő adatait. Ezeket a fürt kiépítés közben adta meg.

### <a name="manage-azure-subscriptions"></a>Azure-előfizetések kezelése

Alapértelmezés szerint a HDInsight eszköz a Azure Toolkit for Eclipse felsorolja a Spark-fürtöket az összes Azure-előfizetésből. Ha szükséges, megadhatja azokat az előfizetéseket, amelyekhez el szeretné érni a fürtöt.

1. Az Azure Explorerben kattintson a jobb gombbal az **Azure** -legfelső szintű csomópontra, majd válassza az **Előfizetések kezelése**lehetőséget.

1. A párbeszédpanelen törölje a nem kívánt előfizetéshez tartozó jelölőnégyzeteket, majd kattintson a **Bezárás**gombra. A kijelentkezés lehetőséget is választhatja, **Ha ki szeretné** jelentkezni az Azure-előfizetésből.

## <a name="run-a-spark-scala-application-locally"></a>Spark Scala-alkalmazás helyi futtatása

A Azure Toolkit for Eclipse HDInsight eszközeivel helyileg is futtathat Spark Scala-alkalmazásokat a munkaállomáson. Ezeknek az alkalmazásoknak általában nincs szükségük a fürt erőforrásaihoz, például a tárolóhoz való hozzáféréshez, és a helyileg futtathatja és tesztelheti is azokat.

### <a name="prerequisite"></a>Előfeltétel

Miközben a helyi Spark Scala alkalmazást futtatja egy Windows rendszerű számítógépen, előfordulhat, hogy kivételt tapasztal a [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356). Ez a kivétel azért fordul elő, mert a **WinUtils. exe** hiányzik a Windows rendszerből.

A hiba megoldásához [le kell töltenie a végrehajtható fájlt](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) egy olyan helyre, mint a **C:\WinUtils\bin**, majd hozzá kell adnia a **HADOOP_HOME** környezeti változót, és a változó értékét **C\WinUtils**értékre kell állítania.

### <a name="run-a-local-spark-scala-application"></a>Helyi Spark Scala-alkalmazás futtatása

1. Indítsa el az Eclipse-t és hozzon létre egy projektet. Az **új projekt** párbeszédpanelen hajtsa végre a következő beállításokat, majd válassza a **tovább**lehetőséget.

   * A bal oldali panelen válassza ki a **HDInsight** elemet.
   * A jobb oldali ablaktáblában válassza a **Spark lehetőséget a HDInsight helyi futtatási minta (Scala)** elemnél.

   ![Új projekt varázsló kiválasztása párbeszédpanel](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)

1. A projekt részleteinek megadásához kövesse a [HDInsight Spark-fürthöz tartozó Spark Scala-projekt beállítása](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster)a korábbi szakasz 3 – 6. lépését.

1. A sablon egy mintakód (**LogQuery**) hozzáadására szolgál az **src** mappában, amelyet helyileg futtathat a számítógépen.

   ![A LogQuery helye](./media/apache-spark-eclipse-tool-plugin/local-scala-application.png)

1. Kattintson a jobb gombbal a **LogQuery** alkalmazásra, mutasson a **Futtatás másként**elemre, majd válassza az **1 Scala-alkalmazás**lehetőséget. A következőhöz hasonló kimenet jelenik meg a **konzol** lapon:

   ![Spark-alkalmazás helyi futtatásának eredménye](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="reader-only-role"></a>Csak olvasó szerepkör

Ha a felhasználók a csak olvasási szerepkörrel rendelkező fürthöz küldenek feladatokat, a Ambari hitelesítő adatai szükségesek.

### <a name="link-cluster-from-context-menu"></a>Fürt csatolása a helyi menüből

1. Jelentkezzen be csak olvasó szerepkörrel rendelkező fiókkal.

2. Az **Azure Explorerben**bontsa ki a **HDInsight** elemet az előfizetésében található HDInsight-fürtök megtekintéséhez. A **"szerepkör: olvasó"** jelölésű fürtökön csak a csak olvasási jogosultsággal rendelkező szerepkör jogosult.

    ![HDInsight Spark-fürtök az Azure Explorer szerepkör-olvasóban](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer6.png)

3. Kattintson a jobb gombbal a fürtre, és csak olvasó szerepkörre vonatkozó engedéllyel. Válassza a **fürt csatolása** a helyi menüből lehetőséget a fürt összekapcsolásához. Adja meg a Ambari felhasználónevét és jelszavát.

    ![HDInsight Spark-fürtök az Azure Explorer-ben – hivatkozás](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer7.png)

4. Ha a fürt sikeresen csatolva van, a rendszer frissíti a HDInsight.
   A fürt szakasza csatolva lesz.
  
    ![HDInsight Spark-fürtök az Azure Explorerben kapcsolódva](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>Fürt csatolása a feladatok csomópont kibontásával

1. Kattintson a **feladatok** csomópontra, majd a **fürthöz való hozzáférés megtagadva** ablakra.

2. Kattintson **a fürt összekapcsolása** elemre a fürt csatolásához.

    ![HDInsight Spark-fürtök az Azure Explorer9](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer9.png)

### <a name="link-cluster-from-spark-submission-window"></a>Fürt csatolása a Spark beküldési ablakból

1. Hozzon létre egy HDInsight-projektet.

2. Kattintson a jobb gombbal a csomagra. Ezután válassza **a Spark-alkalmazás küldése a HDInsight**lehetőséget.

   ![HDInsight Spark-fürtök az Azure Explorer-ben – küldés](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer11.png)

3. Válasszon ki egy olyan fürtöt, amely csak olvasási szerepkörrel rendelkezik a **fürt nevéhez**. Figyelmeztető üzenet jelenik meg. A fürt csatolásához kattintson **a fürt csatolása** lehetőségre.

   ![HDInsight Spark-fürtök az Azure Explorerben](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer15.png)

### <a name="view-storage-accounts"></a>Storage-fiókok megtekintése

* A csak olvasási szerepkörrel rendelkező fürtök esetében kattintson a **Storage-fiókok** csomópontra, majd a **tárterület-hozzáférés megtagadva** ablakra.

   ![HDInsight Spark-fürtök az Azure Explorer Storage-ban](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer13.png)

   ![HDInsight Spark-fürtök az Azure Explorerben megtagadva](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer12.png)

* A csatolt fürtök esetében kattintson a **Storage-fiókok** csomópontra, majd a tárterület- **hozzáférés megtagadva** ablak jelenik meg.

   ![HDInsight Spark-fürtök az Azure Explorer denied2](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer14.png)

## <a name="known-problems"></a>Ismert problémák

Fürt csatolásakor azt javasoljuk, hogy adja meg a tárterület hitelesítő adatait.

![Interaktív bejelentkezés](./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png)

Két mód van a feladatok elküldésére. Ha megadja a tárolási hitelesítő adatokat, a rendszer a kötegelt módot használja a feladatok elküldéséhez. Ellenkező esetben az interaktív mód lesz használatban. Ha a fürt foglalt, előfordulhat, hogy az alábbi hibaüzenet jelenik meg.

![Eclipse Get hiba, ha a fürt foglalt](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png "Eclipse Get hiba, ha a fürt foglalt")

![Eclipse Get hiba, ha a fürt foglalt fonala](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png "Eclipse Get hiba, ha a fürt foglalt fonala")

## <a name="seealso"></a>Lásd még:

* [Áttekintés Apache Spark az Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek

* [Apache Spark BI-val: Interaktív adatelemzés végrehajtása a Spark on HDInsight és a BI Tools használatával](apache-spark-use-bi-tools.md)
* [Apache Spark a Machine Learningkal: A Spark in HDInsight használata az építési hőmérséklet elemzésére a HVAC-adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learningkal: Az élelmiszer-vizsgálati eredmények előrejelzése a Spark in HDInsight használatával](apache-spark-machine-learning-mllib-ipython.md)
* [Webhely-naplózási elemzés Apache Spark használatával a HDInsight-ben](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Alkalmazások létrehozása és futtatása

* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények

* [A Azure Toolkit for IntelliJ használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Apache Spark-alkalmazások távoli hibakeresése a Azure Toolkit for IntelliJ használatával VPN-en keresztül](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Spark-alkalmazások távoli hibakeresése az Azure Toolkit for IntelliJ használatával SSH-n keresztül](../hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Apache Zeppelin notebookok használata Apache Spark-fürttel a HDInsight-on](apache-spark-zeppelin-notebook.md)
* [Jupyter notebookokhoz elérhető kernelek Apache Spark-fürtben HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Erőforrások kezelése

* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
