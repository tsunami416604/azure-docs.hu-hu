---
title: "Az IntelliJ Azure eszköztára: Spark-alkalmazások a HDInsight-fürtök létrehozása |} Microsoft Docs"
description: "Az IntelliJ Azure eszköztára használata Spark scalában írt alkalmazások fejlesztéséhez, és egy HDInsight Spark-fürt küldheti el ezeket."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 73304272-6c8b-482e-af7c-cd25d95dab4d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2017
ms.author: maxluk,jejiang
ms.openlocfilehash: 4eecaf76773927f96f0e4d79d795f0ffe8033a66
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="use-azure-toolkit-for-intellij-to-create-spark-applications-for-an-hdinsight-cluster"></a>Az IntelliJ Azure eszköztára használata Spark-alkalmazások a HDInsight-fürtök létrehozása

Az Azure-eszközkészlet használata az IntelliJ beépülő modul scalában írt Spark-alkalmazások fejlesztéséhez és majd küldheti el ezeket a HDInsight Spark-fürt közvetlenül a az IntelliJ integrált fejlesztési környezeti (IDE). Használhatja a beépülő modul néhány módon:

* Fejleszthet, és küldje el a Scala Spark alkalmazás egy HDInsight Spark-fürtön.
* Az Azure HDInsight Spark-fürt erőforrások eléréséhez.
* Fejleszthet, és egy Scala Spark alkalmazás helyileg történő futtatása.

A projekt létrehozásához tekintse meg a [Spark-alkalmazások létrehozása az intellij-t Azure eszközkészlete](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) videó.

> [!IMPORTANT]
> Segítségével ez a beépülő modul létrehozása, és küldje el az alkalmazásokat csak egy HDInsight Spark-fürt Linux rendszeren.
> 

## <a name="prerequisites"></a>Előfeltételek

- HDInsight Linux rendszeren Apache Spark-fürt. Útmutatásért lásd: [létrehozása az Apache Spark on Azure hdinsight clusters](apache-spark-jupyter-spark-sql.md).
- Oracle Java fejlesztői készlet. Telepítheti azt a [Oracle webhely](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- IntelliJ IDEA. Ez a cikk 2017.1 verzióját használja. Telepítheti azt a [JetBrains webhely](https://www.jetbrains.com/idea/download/).

## <a name="install-azure-toolkit-for-intellij"></a>Az intellij-t Azure eszközkészlet telepítése
A telepítési utasításokért lásd: [Azure eszközkészlet telepítése az IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="sign-in-to-your-azure-subscription"></a>Jelentkezzen be az Azure-előfizetésébe

1. Indítsa el az IntelliJ IDE, és nyissa meg az Azure-kezelővel. Az a **nézet** menüjében válassza **eszköz Windows**, majd válassza ki **Azure Explorer**.
       
   ![Az Azure-kezelővel hivatkozás](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

2. Kattintson a jobb gombbal a **Azure** csomópont, és válassza **bejelentkezés**.

3. Az a **Azure bejelentkezés** párbeszédpanelen jelölje ki **bejelentkezés**, és írja be Azure hitelesítő adatait.

    ![Az Azure bejelentkezés párbeszédpanel](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

4. Miután bejelentkezett a, a **előfizetések kiválasztása** párbeszédpanel megjeleníti az összes Azure-előfizetések társított hitelesítő adatok. Válassza ki a **válasszon** gombra.

    ![Az előfizetések kiválasztása párbeszédpanel](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

5. Az a **Azure Explorer** lapján bontsa ki a **HDInsight** az előfizetés a HDInsight Spark-fürtjei megtekintéséhez.
   
    ![HDInsight Spark-fürtjei az Azure-kezelővel](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

6. A fürt kapcsolódó erőforrások (például storage-fiókok) megtekintéséhez ennél jobban is kibonthatja a fürtnév csomópont.
   
    ![Egy bővített fürtnév csomópont](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Futtassa a Spark Scala-alkalmazások HDInsight Spark-fürt

1. Indítsa el az IntelliJ IDEA, és ezután a projekt létrehozásához. Az a **új projekt** párbeszédpanelen tegye a következőket: 

   a. Válassza ki **HDInsight** > **a Spark on HDInsight (Scala)**.

   b. Az a **Build eszköz** listára, válassza ki, az igényeknek megfelelően az alábbiak valamelyikét:

      * **Maven**, Scala-projekt létrehozása varázsló támogatásához
      * **SBT**, a függőségek kezelésére, és a Scala-projekt létrehozása

    ![Az új projekt párbeszédpanel](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

2. Válassza ki **következő**.

3. A Scala-projekt létrehozása varázsló automatikusan észleli, hogy telepítette-e a Scala beépülő modult. Válassza ki **telepítése**.

   ![Scala beépülő modul ellenőrzése](./media/apache-spark-intellij-tool-plugin/Scala-Plugin-check-Reminder.PNG) 

4. A beépülő modul Scala letöltéséhez, jelölje be az **OK**. Az utasítások az IntelliJ indítsa újra. 

   ![A Scala beépülő modul telepítése párbeszédpanel](./media/apache-spark-intellij-tool-plugin/Choose-Scala-Plugin.PNG)

5. Az a **új projekt** ablakban tegye a következőket:  

    ![A Spark SDK kiválasztása](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

   a. Adja meg a projekt nevét és helyét.

   b. Az a **projekt SDK** legördülő listában válassza **Java 1.8** a Spark-fürt 2.x, vagy válassza ki a **Java 1.7** a Spark 1.x fürthöz.

   c. Az a **Spark verzió** legördülő listából válassza ki, Scala-projekt létrehozása varázsló Spark SDK és Scala SDK integrálja a megfelelő verzióját. Ha a Spark-fürt verziója korábbi, mint 2,0, válassza ki a **Spark 1.x**. Máskülönben válassza **Spark2.x**. Ez a példa **Spark 2.0.2 (Scala 2.11.8)**.

6. Válassza a **Finish** (Befejezés) elemet.

7. A Spark-projekt automatikusan létrehoz egy összetevő. Az összetevő megtekintéséhez tegye a következőket:

   a. Az a **fájl** menü **szerkezetének**.

   b. Az a **szerkezetének** párbeszédpanelen jelölje ki **összetevők** létrehozott alapértelmezett összetevő megtekintéséhez. A saját összetevő is létrehozhat; ehhez válassza a plusz jelre (**+**).

      ![Összetevő információ párbeszédpanel](./media/apache-spark-intellij-tool-plugin/default-artifact.png)
      
8. Adja hozzá az alkalmazás forráskódjához, az alábbi lépésekkel:

   a. A Project Explorer, kattintson a jobb gombbal **src**, mutasson a **új**, majd válassza ki **Scala osztály**.
      
      ![Parancsok a Project Explorer Scala osztály létrehozása](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   b. Az a **hozzon létre új Scala osztály** párbeszédpanel mezőben adjon meg egy nevet, válasszon **objektum** a a **jellegű** mezőbe, majd válassza ki **OK**.
      
      ![Hozzon létre új Scala osztály párbeszédpanel](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   c. Az a **MyClusterApp.scala** fájlt, az alábbi kódot. A kód beolvassa az adatokat HVAC.csv (az összes HDInsight Spark-fürtjei elérhető), lekéri a sorokat, csak egy számot a CSV-fájlt az hetedik oszlop és ír a kimeneti **/HVACOut** alatt az alapértelmezett tároló, a fürt számára.

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

9. Futtassa az alkalmazást egy HDInsight Spark-fürtön a következő módon:

   a. A Project Explorer, kattintson a jobb gombbal a projekt nevét, és válassza **küldje el a Spark-alkalmazást, amely**.
      
      ![A HDInsight parancs küldje el a Spark alkalmazás](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

   b. Azure-előfizetés hitelesítő adatait kéri. Az a **Spark küldésének** párbeszédpanelen adja meg a következő értékeket, és válassza **Submit**.
      
      * A **Spark-fürtök (csak Linux)**, válassza ki a HDInsight Spark-fürt, amelyen szeretné futtatni az alkalmazást.

      * Az IntelliJ projekt összetevő válasszon, vagy válasszon ki egy, a merevlemez-meghajtóról.

      * Az a **fő osztálynév** jelölje ki a három pont (**...** ), a fő osztályban található az alkalmazás forráskódjához, majd válassza ki és **OK**.

        ![A fő osztály kiválasztása párbeszédpanel](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)

      * Mivel ebben a példában az alkalmazás kódja nem igényel parancssori argumentumot vagy hivatkozás JAR-fájlok kivételével vagy fájlokat, hagyhatja, a többi mező üres. Miután megadta az adatokat, a párbeszédpanelen az alábbi képen kell hasonlítania.
        
        ![A Spark küldésének párbeszédpanel](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

   c. A **Spark küldésének** fülre az ablak alján kell kezdenie a folyamatban lévő megjelenítése. Az alkalmazás a piros gombra kattintva is leállíthatja a **Spark küldésének** ablak.
      
      ![A Spark küldésének ablak](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)
      
      A feladat kimenetére elérésére, lásd: a "hozzáférés és a HDInsight Spark-fürtök kezelése az intellij-t Azure eszközkészlet használatával" című szakaszban ebben a cikkben található.

## <a name="debug-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>A Spark-alkalmazások hibakeresését helyileg vagy távolról a HDInsight-fürtök 
Javasoljuk továbbá egy másik módszer a Spark alkalmazás fürtre elküldése. A paraméterek beállításával is teheti a **Futtatás/Debug konfigurációk** IDE. További információkért lásd: [Spark-alkalmazások hibakeresését helyileg vagy távolról az Azure-eszközkészlet a HDInsight-fürtök az SSH-n keresztül IntelliJ](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Férhessen hozzá és felügyelhesse a HDInsight Spark-fürtjei IntelliJ Azure eszközkészlet használatával
Az intellij-t Azure eszközkészlet használatával különféle műveleteket hajthat végre.

### <a name="access-the-job-view"></a>Hozzáférés a feladat megtekintése
1. Az Azure Explorerben bontsa ki a **HDInsight**, bontsa ki a Spark-fürt nevét, majd válassza ki **feladatok**.  

    ![Feladatok nézet csomópont](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

2. A jobb oldali ablaktáblában a **Spark feladat megtekintése** lap megjeleníti a fürtön futó összes alkalmazást. Válassza ki, amelynek meg szeretné tekinteni a további részleteket az alkalmazás nevét.

    ![Az alkalmazás részletei](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)

3. Alapszintű futó feladat adatainak megjelenítéséhez vigye a feladat ábra. A szakaszok grafikon és információt, amely minden feladatot hoz létre megtekintéséhez válasszon ki egy csomópontot a feladat ábra a.

    ![Feladat szakasz részletei](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Megtekintheti a gyakran használt naplókat, például a *illesztőprogram Stderr*, *illesztőprogram Stdout*, és *Directory Info*, jelölje be a **napló** fülre.

    ![Naplófájl részletei](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

5. A Spark-előzmények felhasználói felület és a YARN felhasználói felületen (az alkalmazás szintjén) hivatkozás az ablak tetején kiválasztásával is megtekintheti.

### <a name="access-the-spark-history-server"></a>A Spark-előzmények kiszolgáló elérhető
1. Az Azure Explorerben bontsa ki a **HDInsight**, kattintson a jobb gombbal a Spark-fürt nevét, majd válassza ki **nyissa meg a Spark feladatelőzmények felhasználói felület**. 

2. Amikor a rendszer kéri, adja meg a fürt rendszergazdai hitelesítő adataival, amely a fürt telepítésekor megadott.

3. A Spark előzmények server irányítópult az alkalmazás neve segítségével keresse meg az alkalmazás csak futása befejeződött. Az előző kódban használatával beállíthatja az alkalmazás neve `val conf = new SparkConf().setAppName("MyClusterApp")`. A Spark alkalmazásnév ezért **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Indítsa el az Ambari portálon
1. Az Azure Explorerben bontsa ki a **HDInsight**, kattintson a jobb gombbal a Spark-fürt nevét, majd válassza ki **nyitott fürt Management Portal (Ambari)**. 

2. Amikor a rendszer kéri, adja meg a fürt rendszergazdai hitelesítő adatokat. Ezeket a hitelesítő adatokat adott meg a fürt telepítés során.

### <a name="manage-azure-subscriptions"></a>Az Azure-előfizetések kezelése
Alapértelmezés szerint az intellij-t Azure eszköztára a Spark-fürtök az összes Azure-előfizetések sorolja fel. Ha szükséges, megadhat olyan előfizetést, amely az elérni kívánt. 

1. Az Azure-kezelővel, kattintson a jobb gombbal a **Azure** gyökércsomópont, és válassza ki **előfizetések kezelése oldalt**. 

2. A párbeszédpanelen törölje az előfizetéseket, amelyet szeretne elérni, és jelölje be a jelölőnégyzeteket **Bezárás**. Igény szerint kiválaszthatja **Kijelentkezés** Ha azt szeretné, hogy kijelentkezik, az Azure-előfizetéshez.

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Alakítsa át a meglévő IntelliJ IDEA alkalmazások az Azure-eszközkészlet az intellij-t
A meglévő Spark Scala használatával bármikor átalakítható létrehozott IntelliJ IDEA való kompatibilitás érdekében az IntelliJ Azure eszköztára alkalmazások. Ezután használhatja a beépülő modul elküldeni az alkalmazásokat a HDInsight Spark-fürt.

1. Egy meglévő Spark Scala-alkalmazás, amely az IntelliJ IDEA használatával hozták létre nyissa meg a társított .iml fájlt.

2. Gyökerében szintje a **modul** elem a következő:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Az elem hozzáadása szerkesztése `UniqueKey="HDInsightTool"` , hogy a **modul** elem a következőhöz hasonló:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

3. Mentse a módosításokat. Az alkalmazás most már Azure eszköztára IntelliJ kompatibilisnek kell lennie. Kattintson a jobb gombbal a projekt nevére a Project Explorer tesztelheti. A legördülő menü most már a beállítás **küldje el a Spark-alkalmazást, amely**.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="error-in-local-run-please-use-a-larger-heap-size"></a>Hiba történt a helyi futtatáskor: *használjon egy nagyobb halommemória mérete*
A Spark 1.6 használata egy 32 bites Java SDK helyi futtatás során esetleg felmerülő hibák a következők:

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

Ezek a hibák akkor fordulhat elő, mert a halommemória mérete nem elég nagy Spark futtatásához. Spark legalább 471 MB terület szükséges. (További információkért lásd: [SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081).) Egy egyszerű megoldást, hogy egy 64 bites Java SDK-t használja. Az IntelliJ JVM beállításait is módosíthatja adja hozzá a következő beállításokat:

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Beállítások hozzáadása a "Virtuális gép beállításai" mezőben az intellij-t](./media/apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="faq"></a>GYIK
Azure Data Lake Store kérelmet elküldéséhez válassza **interaktív** mód az Azure bejelentkezési folyamat során. Ha **automatikus** mód, hibaüzenetet kaphat.

![interaktív bejelentkezés](./media/apache-spark-intellij-tool-plugin/interative-signin.png)

Most azt feloldotta azt. Az Azure Data Lake fürt elküldeni az alkalmazás egyik bejelentkezési módszer kiválasztása

## <a name="feedback-and-known-issues"></a>Visszajelzések és ismert problémák
Spark kimenetek közvetlenül megtekintése jelenleg nem támogatott.

Ha javaslata vagy visszajelzést, vagy ha ez a beépülő modul használatakor bármely problémákat tapasztal, e-mail nekünk az hdivstool@microsoft.com.

## <a name="seealso"></a>Következő lépések
* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="demo"></a>Bemutató
* Hozzon létre Scala project (videó): [Spark Scala-alkalmazások létrehozása](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Távoli hibakeresési (videó): [IntelliJ a Spark-alkalmazások távolról a HDInsight-fürt használata Azure eszköztára](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Forgatókönyvek
* [Spark és BI: interaktív adatelemzés végrehajtása a Spark hdinsight BI-eszközökkel](apache-spark-use-bi-tools.md)
* [Spark és Machine Learning: Spark on HDInsight HVAC-adatok épület-hőmérséklet elemzésére használata](apache-spark-ipython-notebook-machine-learning.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [Spark Streaming: Spark on HDInsight használata valós idejű streamelési alkalmazásokat hozhatnak létre](apache-spark-eventhub-streaming.md)
* [A webhelynapló elemzése a Spark on HDInsight használatával](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Létrehozása és alkalmazások futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Spark-fürtön a Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [Az intellij-t Azure eszközkészlet segítségével VPN-en keresztül távolról Spark-alkalmazások](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Az intellij-t Azure eszközkészlet segítségével SSH keresztül távolról Spark-alkalmazások](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Használja a HDInsight Tools for IntelliJ a Hortonworks védőfal](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Az Eclipse Azure eszközkészlet a HDInsight Tools használatával Spark-alkalmazások létrehozása](apache-spark-eclipse-tool-plugin.md)
* [Zeppelin notebookok használata Spark-fürttel HDInsighton](apache-spark-zeppelin-notebook.md)
* [Jupyter notebookokhoz elérhető kernelek a HDInsight Spark-fürtjében](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)

