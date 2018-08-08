---
title: 'IntelliJ-hez készült Azure-eszközkészlet: Spark-alkalmazások HDInsight-fürt létrehozása '
description: Spark Scalában írt alkalmazások fejlesztése az IntelliJ-hez készült Azure-eszközkészlet használatával, és küldheti el ezeket a HDInsight Spark-fürt.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/25/2017
ms.author: maxluk
ms.openlocfilehash: c3442a95bed4f589ca8f939767bb0d0581095927
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39618425"
---
# <a name="use-azure-toolkit-for-intellij-to-create-spark-applications-for-an-hdinsight-cluster"></a>IntelliJ-hez készült Azure eszközkészlet használata Spark-alkalmazások egy HDInsight-fürt létrehozása

Az Azure-eszközkészlet használata beépülő modul az IntelliJ-hez írt Scala Spark-alkalmazások fejlesztéséhez, és majd küldheti el ezeket a HDInsight Spark-fürt közvetlenül a az IntelliJ integrált fejlesztőkörnyezetben (IDE). Használhatja a beépülő modul több módon is:

* Fejlesztés, és küldje el a Scala Spark-alkalmazását egy HDInsight Spark-fürtön.
* Az Azure HDInsight Spark fürterőforrásokat eléréséhez.
* Fejlesztés és a Scala Spark-alkalmazás helyileg történő futtatása.

A projekt létrehozásához tekintse meg a [Spark-alkalmazások létrehozása az IntelliJ-hez készült Azure-eszközkészlet](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) videó.

> [!IMPORTANT]
> Használhatja ez a beépülő modul létrehozása és elküldése az alkalmazások csak a HDInsight Spark-fürt Linux rendszeren.
> 

## <a name="prerequisites"></a>Előfeltételek

- Apache Spark-fürtök HDInsight Linux rendszeren. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).
- Oracle Java Development Kitet. A telepítheti a [Oracle webhely](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- IntelliJ IDEA. Ez a cikk 2017.1 verzióját használja. A telepítheti a [eszközt webhely](https://www.jetbrains.com/idea/download/).

## <a name="install-azure-toolkit-for-intellij"></a>IntelliJ-hez készült Azure-eszközkészlet telepítése
A telepítési utasításokért lásd: [IntelliJ-hez készült Azure eszközkészlet telepítése](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="get-started"></a>Első lépések
Felhasználói lehetőségek közül választhat [jelentkezzen be Azure-előfizetés](#sign-in-to-your-azure-subscription), vagy [egy HDInsight-fürtöt](#link-a-cluster) az Ambari felhasználónév/jelszó és a tartományhoz csatlakoztatott credential elindításához.


## <a name="sign-in-to-your-azure-subscription"></a>Jelentkezzen be az Azure-előfizetésébe

1. Indítsa el az intellij-vel IDE, és nyissa meg az Azure Explorert. Az a **nézet** menüjében válassza **eszköz Windows**, majd válassza ki **Azure Explorer**.
       
   ![Az Azure Explorer hivatkozás](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

1. Kattintson a jobb gombbal a **Azure** csomópontot, és válassza ki **bejelentkezés**.

1. Az a **Azure bejelentkezési** párbeszédpanelen jelölje ki **jelentkezzen be a**, majd adja meg Azure hitelesítő adatait.

    ![Az Azure bejelentkezési párbeszédpanel](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

1. Miután jelentkezett be, a **kiválasztása az előfizetések** párbeszédpanelen megjelenik az összes Azure-előfizetések tartoznak a hitelesítő adatokat. Válassza ki a **kiválasztása** gombra.

    ![Az előfizetések kiválasztása párbeszédpanel](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

1. Az a **Azure Explorer** lapon, bontsa ki a **HDInsight** megtekintéséhez a HDInsight Spark-fürtök, amelyek az előfizetésében.
   
    ![HDInsight Spark-fürtök Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

1. A fürthöz társított erőforrások (például storage-fiókok) megtekintéséhez, ennél jobban is kibonthatja a fürtnév csomópont.
   
    ![Egy kibontott fürtnév csomópontok](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="link-a-cluster"></a>Egy fürtöt
Kapcsolat egy normál HDInsight-fürt Ambari felügyelt felhasználónév használatával. Hasonlóképpen, egy tartományhoz csatlakoztatott HDInsight-fürtöt, a kapcsolat a tartomány és felhasználónév, mint például a user1@contoso.com.

1. Válassza ki **egy fürtöt** a **Azure Explorer**.

   ![hivatkozás fürt helyi menü](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)


1. Adja meg **fürt neve**, **felhasználónév** és **jelszó**. Ellenőrizze a felhasználónevet és jelszót, ha a hitelesítési hiba van szüksége. Szükség esetén adja meg a Tárfiókot, a Tárfiók kulcsát, majd kiválaszthatja a tárolót a Storage-tárolóból. A bal oldali fában a storage Explorer van tárolással
   
   ![hivatkozás fürt párbeszédpanel](./media/apache-spark-intellij-tool-plugin/link-a-cluster-dialog.png)

   > [!NOTE]
   > Ha a fürt egyaránt bejelentkezett az Azure-előfizetés és a fürthöz társított a társított storage-kulcs, a felhasználónevet és jelszót használunk.
   > ![az intellij-ben a Storage Explorerben](./media/apache-spark-intellij-tool-plugin/storage-explorer-in-IntelliJ.png)

   
1. Láthatja, hogy a társított fürt **HDInsight** csomópont, ha a bemeneti adatok megfelelő. Ehhez a fürthöz társított alkalmazás most már küldhet.

   ![a csatolt fürt](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

1. Fürt is megszüntetheti **Azure Explorer**.
   
   ![nem összekapcsolt fürt](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Futtathatja a Spark Scala alkalmazásokat egy HDInsight Spark-fürtön

1. Indítsa el az IntelliJ IDEA-t, majd hozzon létre egy projektet. A **New Project** (Új projekt) párbeszédablakban hajtsa végre az alábbi lépéseket: 

   a. Válassza ki a **HDInsight** > **Spark on HDInsight (Scala)** lehetőséget.

   b. A **Build tool** (Létrehozási eszköz) listában válassza ki az alábbiak közül az igényeinek megfelelőt:

      * **Maven**, a Scala projektlétrehozási varázsló támogatásához
      * **SBT**, a függőségek kezeléséhez és a Scala-projekt létrehozásához

    ![A New project (Új projekt) párbeszédablak.](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

1. Kattintson a **Tovább** gombra.

1. A Scala projektlétrehozási varázsló automatikusan észleli, hogy telepítette-e a Scala beépülő modulját. Válassza az **Install** (Telepítés) lehetőséget.

   ![A Scala beépülő moduljának ellenőrzése](./media/apache-spark-intellij-tool-plugin/Scala-Plugin-check-Reminder.PNG) 

1. Az Scala beépülő moduljának letöltéséhez válassza az **OK** elemet. Az IntelliJ újraindításához kövesse az utasításokat. 

   ![A Scala beépülő moduljának telepítési párbeszédablaka](./media/apache-spark-intellij-tool-plugin/Choose-Scala-Plugin.PNG)

1. A **New Project** (Új projekt) ablakban hajtsa végre az alábbi lépéseket:  

    ![A Spark SDK kiválasztása](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

   a. Adja meg a projekt nevét és helyét.

   b. A **Project SDK** (Projekt SDK) legördülő listából Spark 2.x-fürt esetében válassza a **Java 1.8**, Spark 1.x-fürt esetében pedig a **Java 1.7** lehetőséget.

   c. A **Spark version** (Spark-verzió) legördülő listában a Scala projektlétrehozási varázsló integrálja a Spark SDK-hoz és Scala SDK-hoz megfelelő verziót. Ha a Spark-fürt verziója 2.0-nál korábbi, válassza a **Spark 1.x** lehetőséget. Máskülönben válassza a **Spark2.x** lehetőséget. Ez a példa a következő verziót használja: **Spark 2.0.2 (Scala 2.11.8)**.

1. Válassza a **Finish** (Befejezés) elemet.

1. A Spark-projekt automatikusan létrehoz egy összetevő. A lehívandó összetevő megtekintéséhez tegye a következőket:

   a. Az a **fájl** menüjében válassza **Projektstruktúra**.

   b. Az a **Projektstruktúra** párbeszédpanelen jelölje ki **összetevők** létrehozott alapértelmezett összetevő megtekintéséhez. A plusz jelre kattintva is létrehozhat saját összetevő (**+**).

      ![A párbeszédpanel összetevő adatai](./media/apache-spark-intellij-tool-plugin/default-artifact.png)
      
1. Adja hozzá az alkalmazás forráskódjának az alábbiak szerint:

   a. A Project Explorer, kattintson a jobb gombbal **src**, mutasson a **új**, majd válassza ki **Scala osztály**.
      
      ![Parancsok a Project Explorer Scala osztály létrehozása](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   b. Az a **hozzon létre új Scala osztály** párbeszédpanel mezőben adjon meg egy nevet, válassza ki **objektum** a a **Kind** mezőbe, majd válassza ki **OK**.
      
      ![Hozzon létre új Scala osztály párbeszédpanel](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   c. Az a **MyClusterApp.scala** fájlt, az alábbi kódot. A kód beolvassa az adatokat a HVAC.csv (az összes HDInsight Spark-fürtökön elérhető), a hetedik oszlopban a CSV-fájlban csak egy számot tartalmazó sorok beolvasása és ír a kimenetet **/HVACOut** alatt az alapértelmezett tároló a fürt.

        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
    
        object MyClusterApp{
            def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows that have only one digit in the seventh column in the CSV file
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasb:///HVACOut")
            }
    
        }

1. Futtassa az alkalmazást egy HDInsight Spark-fürtön a következő módon:

   a. A Project Explorer, kattintson a jobb gombbal a projekt nevét, és válassza **Spark-alkalmazás elküldéséhez HDInsight**.
      
      ![Az HDInsight parancs elküldése Spark alkalmazás](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

   b. Az Azure-előfizetés hitelesítő adatainak megadását kéri. Az a **Spark küldésének** párbeszédpanelen adja meg az alábbi értékeket, és válassza **küldés**.
      
      * A **Spark-fürtök (csak Linux)**, válassza ki a HDInsight Spark-fürtöt, amelyen szeretné, az alkalmazás futtatásához.

      * Válasszon ki egy összetevőt az IntelliJ-projekt, vagy válasszon egyet a merevlemez-meghajtóról.

      * Az a **Main osztály neve** jelölje ki a három pontra (**...** ), a fő osztályban az alkalmazáskódban forrás, majd válassza ki és **OK**.

        ![A Main osztály kiválasztása párbeszédpanel](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)

      * Mivel ebben a példában az alkalmazás kódja nem igényel parancssori argumentumot vagy hivatkozás JAR-fájlok kivételével, vagy a fájlokat, akkor a többi mező üresen hagyhatja. Miután megadta az összes információt, a párbeszédpanelen az alábbi képen hasonlóan kell kinéznie.
        
        ![A Spark küldésének párbeszédpanel](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

   c. A **Spark küldésének** fülre az ablak alján el kell indulnia, megjelenítés, a folyamat állapotát. Az alkalmazás a piros gomb kiválasztásával is leállíthatja a **Spark küldésének** ablak.
      
     ![A Spark küldésének ablak](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)
      
      Feladat kimenetének elérésével kapcsolatban lásd: a "hozzáférés és a HDInsight Spark-fürtök kezelése az IntelliJ-hez készült Azure-eszközkészlet használatával" szakasz a cikk későbbi részében.

## <a name="debug-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Hibakeresés Spark-alkalmazások helyben vagy távolról egy HDInsight-fürtön 
Javasoljuk továbbá egy másik módja a fürtön a Spark-alkalmazás be. Ehhez a paraméterek beállításával a **Futtatás és hibakeresés konfigurációk** IDE. További információkért lásd: [hibakeresés Spark-alkalmazások helyben vagy távolról egy HDInsight-fürtön az Azure-eszközkészlet ssh-n keresztül az IntelliJ-hez](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).



## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Hozzáférés és a HDInsight Spark-fürtök kezelése az IntelliJ-hez készült Azure-eszközkészlet használatával
IntelliJ-hez készült Azure-eszközkészlet használatával különféle műveleteket végezhet.

### <a name="access-the-job-view"></a>Hozzáférés a feladatnézetben
1. Az Azure Explorerben bontsa ki a **HDInsight**, bontsa ki a Spark-fürt nevét, és válassza **feladatok**.  

    ![Feladat megtekintése csomópont](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

1. A jobb oldali ablaktáblán a **Spark-feladat nézet** lap megjeleníti a fürtön futó összes alkalmazást. Válassza ki, amelynek meg szeretné tekinteni a további részleteket az alkalmazás nevét.

    ![Alkalmazás adatai](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)
    >Megjegyzés
    >

1. Alapszintű futó feladat adatainak megjelenítéséhez vigye a kurzort a feladatgrafikon fölé. A szakaszok graph és minden feladat által létrehozott információk megtekintéséhez válasszon ki egy csomópontot, a feladat ábra a.

    ![Feladat fázis részletei](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

1. Megtekintheti a gyakran használt naplókat, például *illesztőprogram Stderr*, *illesztőprogram Stdout*, és *Directory Info*, jelölje be a **Log** fülre.

    ![Napló részletei](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

1. A Spark-előzmények felhasználói felület és a YARN felhasználói felületén (az alkalmazás szintjén) egy hivatkozást az ablak tetején lévő kiválasztásával is megtekintheti.

### <a name="access-the-spark-history-server"></a>Hozzáférés a Spark-előzménykiszolgáló
1. Az Azure Explorerben bontsa ki a **HDInsight**, és kattintson a jobb gombbal a Spark-fürt nevét, majd válassza ki **nyissa meg a Spark feladatelőzmények felhasználói felület**. 

1. Amikor a rendszer kéri, adja meg a fürt rendszergazdai hitelesítő adataival, amelyet a fürt üzembe helyezésekor meg.

1. A Spark előzmények kiszolgáló irányítópultján használhatja az alkalmazás nevét és tekintse meg az alkalmazás csak futtatása befejeződött. A fenti kóddal, a használatával megadhatja az alkalmazás neve `val conf = new SparkConf().setAppName("MyClusterApp")`. A Spark-alkalmazás neve ezért **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Indítsa el az Ambari-portálon
1. Az Azure Explorerben bontsa ki a **HDInsight**, és kattintson a jobb gombbal a Spark-fürt nevét, majd válassza ki **fürt felügyeleti portál megnyitása (Ambari)**. 

1. Amikor a rendszer kéri, adja meg a rendszergazdai hitelesítő adatok a fürt. Ezeket a hitelesítő adatokat adott meg a fürt beállítása során.

### <a name="manage-azure-subscriptions"></a>Azure-előfizetések kezelése
Alapértelmezés szerint az IntelliJ-hez a Spark-fürtök, az összes Azure-előfizetések sorolja fel. Ha szükséges, megadhatja az elérni kívánt előfizetéseket. 

1. Az Azure Explorerben, kattintson a jobb gombbal a **Azure** gyökércsomópont, és válassza ki **előfizetések kezelése**. 

1. A párbeszédpanelen törölje a jelölőnégyzet jelölését, amelyet szeretne elérni, és válassza ki az előfizetések mellett **Bezárás**. Lehetőség kiválasztásával **Kijelentkezés** Ha jelentkezzen ki az Azure-előfizetéshez szeretne.

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Átalakíthatja az IntelliJ-hez készült Azure-eszközkészlet használata meglévő az IntelliJ IDEA-alkalmazásokat
Átalakíthatja a meglévő Scala Spark alkalmazásokat az IntelliJ IDEA-kompatibilis, az IntelliJ-hez létrehozott. Ezután használhatja a beépülő modul az alkalmazásokat a HDInsight Spark-fürt elküldéséhez.

1. Egy meglévő Spark Scala-alkalmazáshoz, amely az IntelliJ IDEA használatával hozták létre nyissa meg a társított .iml fájlt.

1. A gyökérszintű szintje egy **modul** elemet a következőhöz hasonló:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Adja hozzá az elem szerkesztése `UniqueKey="HDInsightTool"` úgy, hogy a **modul** elemet a következőhöz hasonló:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

1. Mentse a módosításokat. Az alkalmazás már kompatibilis az IntelliJ-hez készült Azure-eszközkészlet kell lennie. Kattintson a jobb gombbal a projekt nevére a Project Explorer tesztelheti. Az előugró menüben mostantól van lehetőség **Spark-alkalmazás elküldéséhez HDInsight**.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="error-in-local-run-please-use-a-larger-heap-size"></a>Hiba történt a helyi Futtatás: *használja egy nagyobb. generace*
A Spark 1.6-os Ha egy 32 bites Java SDK-t használ a helyi futtatás során esetleg felmerülő hibák a következők:

    Exception in thread "main" java.lang.IllegalArgumentException: System memory 259522560 must be at least 4.718592E8. Please use a larger heap size.
        at org.apache.spark.memory.UnifiedMemoryManager$.getMaxMemory(UnifiedMemoryManager.scala:193)
        at org.apache.spark.memory.UnifiedMemoryManager$.apply(UnifiedMemoryManager.scala:175)
        at org.apache.spark.SparkEnv$.create(SparkEnv.scala:354)
        at org.apache.spark.SparkEnv$.createDriverEnv(SparkEnv.scala:193)
        at org.apache.spark.SparkContext.createSparkEnv(SparkContext.scala:288)
        at org.apache.spark.SparkContext.<init>(SparkContext.scala:457)
        at LogQuery$.main(LogQuery.scala:53)
        at LogQuery.main(LogQuery.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:606)
        at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)

Ezek a hibák az oka, hogy a halommemória mérete nem elég nagy a Spark végrehajtásához. A Spark legalább 471 MB szükséges. (További információkért lásd: [SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081).) Egy egyszerű megoldás, ha egy 64 bites Java SDK-val. Adja hozzá a következő beállításokat is módosíthatja a JVM-beállításokat, az intellij-ben:

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Az intellij-ben a "Virtuális gép options" mezőben beállítások hozzáadása](./media/apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="faq"></a>GYIK
Amikor a fürt hivatkozásra, e javasolna, hogy adja meg a storage hitelesítő adatai.

![-Fürtöt, a tároló hitelesítő adatok megadása](./media/apache-spark-intellij-tool-plugin/link-cluster-with-storage-credential-intellij.png)

Két módja a feladatok elküldéséhez. Ha a tároló hitelesítő adat áll rendelkezésre, kötegelt mód használandó a feladat elküldéséhez. Ellenkező esetben az interaktív mód lesz használható. Ha a fürt foglalt, kaphat az alábbi hiba.

![Intellij jelenik meg a hiba esetén a foglalt fürt](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-upload.png)

![Intellij jelenik meg a hiba esetén a foglalt fürt](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-submit.png)

## <a name="feedback-and-known-issues"></a>Visszajelzés és ismert problémák
Közvetlenül a Spark kimenetek megtekintése jelenleg nem támogatott.

Ha javaslata vagy visszajelzés rendelkezik, vagy ha problémákat tapasztal, amikor ez a beépülő modul használata, e-mailt küldjön hdivstool@microsoft.com.

## <a name="seealso"></a>Következő lépések
* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="demo"></a>Bemutató
* Létrehozás Scala project (videó): [Spark Scala-alkalmazások létrehozása](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Távoli hibakeresés (videó): [távolról a HDInsight-fürtön a Spark-alkalmazások hibakeresése az IntelliJ-hez készült Azure eszközkészlet használata](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Forgatókönyvek
* [Spark és BI: interaktív adatelemzés végrehajtása a Spark on HDInsight használatával, BI-eszközökkel](apache-spark-use-bi-tools.md)
* [Spark és Machine Learning: a Spark on HDInsight HVAC-adatok épület-hőmérséklet elemzésére a használata](apache-spark-ipython-notebook-machine-learning.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [A webhelynapló elemzése a Spark on HDInsight használatával](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Létrehozása és alkalmazások futtatásához
* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Spark-fürtön a Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [VPN-en keresztül távolról Spark-alkalmazások hibakeresése az IntelliJ-hez készült Azure eszközkészlet használata](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Ssh-n keresztül távolról Spark-alkalmazások hibakeresése az IntelliJ-hez készült Azure eszközkészlet használata](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [HDInsight Tools for IntelliJ with hortonworks – tesztkörnyezet használata](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Az Eclipse-hez készült Azure-eszközkészlet HDInsight Tools használata Spark-alkalmazások létrehozásához](apache-spark-eclipse-tool-plugin.md)
* [Zeppelin notebookok használata Spark-fürttel HDInsighton](apache-spark-zeppelin-notebook.md)
* [Jupyter notebookokhoz elérhető kernelek a HDInsight Spark-fürtjében](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
