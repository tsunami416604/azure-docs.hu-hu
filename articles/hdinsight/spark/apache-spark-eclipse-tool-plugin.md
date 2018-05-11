---
title: 'Az Eclipse Azure eszközkészlet: HDInsight Spark Scala létrehozása alkalmazások |} Microsoft Docs'
description: Az Eclipse eszköztára Azure HDInsight Tools használatával scalában írt Spark-alkalmazások fejlesztéséhez és küldheti el ezeket a HDInsight Spark-fürt az Eclipse IDE-ről.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f6c79550-5803-4e13-b541-e86c4abb420b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 11/30/2017
ms.author: nitinme
ms.openlocfilehash: cd54c4abeaa58c1b78f67c55eb5e8856dc5bb0c4
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="use-azure-toolkit-for-eclipse-to-create-spark-applications-for-an-hdinsight-cluster"></a>Azure eszköztára Eclipse használata Spark-alkalmazások a HDInsight-fürtök létrehozása

Az Eclipse eszköztára Azure HDInsight Tools használatával scalában írt Spark-alkalmazások fejlesztéséhez és küldheti el ezeket az Azure HDInsight Spark-fürt az Eclipse IDE-ről. A HDInsight Tools beépülő modul néhány különböző módokon használhatja:

* Fejlesztésére, és küldje el a Scala Spark-alkalmazás egy HDInsight Spark-fürt
* Az Azure HDInsight Spark-fürt erőforrások eléréséhez.
* Fejlesztésére és Scala Spark-alkalmazás helyileg történő futtatása

> [!IMPORTANT]
> Az eszköz segítségével hozzon létre, és küldje el az alkalmazásokat csak egy HDInsight Spark-fürt Linux rendszeren.
> 
> 

## <a name="prerequisites"></a>Előfeltételek

* Apache Spark-fürt a hdinsight platformon. Útmutatásért lásd: [létrehozása az Apache Spark on Azure hdinsight clusters](apache-spark-jupyter-spark-sql.md).
* Oracle Java fejlesztői készlet 8-as verzió, az Eclipse IDE futásidejű használt. Letöltheti azt a [Oracle webhely](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* Eclipse IDE. Ebben a cikkben az Eclipse Neonfény. Telepítheti azt a [Eclipse webhely](https://www.eclipse.org/downloads/).



## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse-and-the-scala-plug-in"></a>A HDInsight Tools az eclipse-ben és a beépülő modul Scala Azure eszközkészlet telepítése
### <a name="install-azure-toolkit-for-eclipse"></a>Az Eclipse Azure eszközkészlet telepítése
A HDInsight Tools for eclipse-ben érhető el az Eclipse Azure eszköztára részeként. A telepítési utasításokért lásd: [Azure eszközkészlet telepítése az Eclipse](https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-installation).
### <a name="install-the-scala-plug-in"></a>A beépülő modul Scala telepítése
Eclipse megnyitásakor a HDInsight eszközzel automatikusan észleli, hogy telepítette-e a Scala beépülő modult. Válassza ki **OK** a folytatáshoz, majd kövesse az utasításokat az Eclipse piactérről a beépülő modul telepítéséhez.

![A beépülő modul Scala automatikus telepítése](./media/apache-spark-eclipse-tool-plugin/auto-install-scala.png)

Felhasználói lehetőségek közül választhat [jelentkezzen be Azure-előfizetés](#Sign-in-to-your-Azure-subscription), vagy [hivatkozás egy HDInsight-fürt](#Link-a-cluster) Ambari használatával felhasználónév/jelszó adatokat vagy tartományhoz csatlakoztatott indítsa el a hitelesítő adatot. 

## <a name="sign-in-to-your-azure-subscription"></a>Jelentkezzen be az Azure-előfizetésébe
1. Indítsa el az Eclipse IDE, és nyissa meg az Azure-kezelővel. Az a **ablak** menüjében válassza **nézet megjelenítése**, majd válassza ki **más**. A megnyíló párbeszédpanelen bontsa ki a **Azure**, jelölje be **Azure Explorer**, majd válassza ki **OK**.

   ![Nézet párbeszédpanel megjelenítése](./media/apache-spark-eclipse-tool-plugin/view-explorer-1.png)
2. Kattintson a jobb gombbal a **Azure** csomópont, és válassza **bejelentkezés**.
3. Az a **Azure bejelentkezés** párbeszédpanel mezőben válassza ki a hitelesítési módszert, válasszon **jelentkezzen be a**, és az Azure hitelesítő adatait.
   
   ![Az Azure bejelentkezési párbeszédpanel](./media/apache-spark-eclipse-tool-plugin/view-explorer-2.png)
4. Miután bejelentkezett a, a **előfizetések kiválasztása** párbeszédpanel megjeleníti az összes Azure-előfizetések társított hitelesítő adatok. Kattintson a **válasszon** a párbeszédpanel bezárásához.

   ![Válassza ki az előfizetések párbeszédpanel](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)
5. Az a **Azure Explorer** lapján bontsa ki a **HDInsight** a HDInsight Spark-fürtök az előfizetéshez tartozó megjelenítéséhez.
   
   ![HDInsight Spark-fürtjei az Azure-kezelővel](./media/apache-spark-eclipse-tool-plugin/view-explorer-3.png)
6. Ennél jobban is kibonthatja az erőforrások (például storage-fiókok) a fürthöz tartozó megjelenítéséhez fürtcsomópont nevét.
   
   ![A fürt nevét cikkekben találhat kibontása](./media/apache-spark-eclipse-tool-plugin/view-explorer-4.png)

<h2 id="linkcluster">A fürt hivatkozás</h2>
Egy normál fürt hivatkozás segítségével felügyelt Ambari felhasználónév, is hivatkozásra a biztonsági hadoop-fürthöz tartományi felhasználónevet használatával (például: user1@contoso.com).
1. Kattintson a **fürt hivatkozás** a **Azure Explorer**.

   ![hivatkozás fürt helyi menü](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

2. Adja meg **fürtnév**, **felhasználónév** és **jelszó**, majd kattintson az OK gombra, ha szeretné kapcsolni a fürtöt. Megadhatja a Tárfiókot, a kulcs, és válassza ki a bal oldali fanézetben működéséhez Tártallózó a tároló
   
   ![hivatkozás fürt párbeszédpanel](./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog.png)
   
   > [!NOTE]
   > Ha a fürt egyaránt bejelentkezett az Azure-előfizetés és a fürthöz kapcsolódó csatolt biztonságitár-kulcs, felhasználónevet és jelszót használunk.
   > ![a Tártallózó az eclipse-ben](./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png)

3. Láthatja, hogy a társított fürt **HDInsight** csomópont után az OK gombra kattintás, ha a bemeneti adatokat megfelelőek. Most is elküldhetik a csatolt fürthöz kérelmet.

   ![csatolt fürt](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

4. Is megszüntetheti a fürtöt **Azure Explorer**.
   
   ![a fürt megszüntetése](./media/apache-spark-intellij-tool-plugin/unlink.png)


## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>A Spark Scala-projektjét, amely egy HDInsight Spark-fürt beállítása

1. Az Eclipse IDE munkaterületen válassza ki a **fájl**, jelölje be **új**, majd válassza ki **projekt**. 
2. Bontsa ki az új projekt varázsló **HDInsight**, jelölje be **a Spark on HDInsight (Scala)**, majd válassza ki **következő**.

   ![A Spark on HDInsight (Scala) projekt kiválasztása](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)
3. A Scala-projekt létrehozása varázsló automatikusan észleli, hogy telepítette-e a Scala beépülő modult. Válassza ki **OK** folytatja a beépülő modul Scala letöltése, és kövesse az utasításokat az Eclipse újraindítását.

   ![scala ellenőrzése](./media/apache-spark-eclipse-tool-plugin/auto-install-scala-2.png)
4. Az a **új HDInsight Scala projekt** párbeszédpanelen adja meg a következő értékeket, és válassza **következő**:
   * Adja meg a projekt nevét.
   * A a **JRE** területen győződjön meg arról, hogy **használja a végrehajtási környezet JRE** értéke **JavaSE-1.7** vagy újabb.
   * A a **Spark könyvtár** területen kiválaszthatja **Spark SDK konfigurálására használható Maven** lehetőséget.  Az eszköz megfelelő verzióját a Spark SDK és a Scala SDK integrálható. Másik lehetőségként **Spark SDK manuális hozzáadása** lehetőséget, töltse le, és adja hozzá manuálisan a Spark SDK által.

   ![Új HDInsight Scala projekt párbeszédpanel](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)
5. Jelölje ki a következő párbeszédpanel **Befejezés**. 
   
  
## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>A HDInsight Spark-fürtök Scala-alkalmazás létrehozása

1. Az Eclipse IDE a csomag Explorer eszközből bontsa ki a korábban létrehozott projekt, kattintson a jobb gombbal **src**, mutasson a **új**, majd válassza ki **más**.
2. Az a **válassza ki a varázsló** párbeszédpanelen bontsa ki **Scala varázslók**, jelölje be **Scala objektum**, majd válassza ki **következő**.
   
   ![Válassza ki a varázsló párbeszédpanelje](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)
3. Az a **hozzon létre új fájl** párbeszédpanelen adja meg az objektum nevét, majd válassza ki **Befejezés**.
   
   ![Hozzon létre új fájl párbeszédpanel](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)
4. Illessze be a következő kódot a szövegszerkesztőben:
   
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
5. Futtassa az alkalmazást egy HDInsight Spark-fürt:
   
   a. A csomag Explorer, kattintson a jobb gombbal a projekt nevére, majd válassza ki **küldje el a Spark-alkalmazást, amely**.        
   b. Az a **Spark küldésének** párbeszédpanelen adja meg a következő értékeket, és válassza **Submit**:
      
      * A **fürtnév**, válassza ki a HDInsight Spark-fürt, amelyen szeretné futtatni az alkalmazást.
      * Válasszon egy összetevő az Eclipse-projekt, vagy válasszon ki egy, a merevlemez-meghajtóról. Az alapértelmezett érték a jobb gombbal a csomag Explorer elemet függ.
      * Az a **fő osztálynév** legördülő listából válassza ki, a küldésének varázsló megjeleníti a projektből az összes objektum nevét. Válassza ki vagy adjon meg egy másikat, amely futtatja. Ha egy közbülső merevlemez-meghajtóról, a fő osztálynév manuálisan kell megadnia. 
      * Mivel ebben a példában az alkalmazás kódja nem igényel parancssori argumentumokat vagy hivatkozás JAR-fájlok kivételével vagy fájlokat, üresen többi mezőbe.
        
      ![Spark küldésének párbeszédpanel](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)
6. A **Spark küldésének** lapon kell kezdenie a folyamatban lévő megjelenítése. A piros gombra kattintva leállíthatja az alkalmazás a **Spark küldésének** ablak. A naplókat, ha a (az ábrán kék mező jelölik) földgömb ikon jelöli az adott alkalmazáshoz is megtekintheti.
      
   ![Spark küldésének ablak](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)


## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Férhessen hozzá és felügyelhesse a HDInsight Spark-fürtjei a HDInsight Tools használatával Azure eszközkészlet az eclipse-ben
A feladat kimenetére történő hozzáféréshez, a HDInsight Tools használatával különféle műveleteket hajthat végre.

### <a name="access-the-job-view"></a>Hozzáférés a feladat megtekintése
1. Az Azure Explorerben bontsa ki a **HDInsight**, bontsa ki a Spark-fürt nevét, majd válassza ki **feladatok**. 

   ![Feladatok nézet csomópont](./media/apache-spark-eclipse-tool-plugin/job-view-node.png)

2. Válassza ki a **feladatok** csomópont. Ha Java verziószáma alacsonyabb, mint **1.8**, a HDInsight Tools automatikusan emlékeztető telepítése a **E (fx) clipse** beépülő modult. Válassza ki **OK** a folytatáshoz, majd kövesse a varázsló az Eclipse piactérről telepítse, majd indítsa újra az eclipse-ben. 

   ![E (fx) clipse telepítése](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

3. Nyissa meg a feladat nézetet a **feladatok** csomópont. A jobb oldali ablaktáblában a **Spark feladat megtekintése** lap megjeleníti a fürtön futó összes alkalmazást. Válassza ki, amelynek meg szeretné tekinteni a további részleteket az alkalmazás nevét.

   ![Alkalmazás adatai](./media/apache-spark-eclipse-tool-plugin/view-job-logs.png)

   Ezek a műveletek majd készíthet:

   * Vigye a mutatót a feladat ábra. A futó feladat kapcsolatos alapvető adatokat jeleníti meg. Válassza ki a feladat ábra, és megtekintheti a szakaszok és adatait, amely minden feladatot hoz létre.

     ![Feladat szakasz részletei](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * Válassza ki a **napló** fülre kattintva megtekintheti a gyakran használt naplókat, beleértve a **illesztőprogram Stderr**, **illesztőprogram Stdout**, és **Directory információ**.

     ![Naplófájl részletei](./media/apache-spark-eclipse-tool-plugin/Job-log-info.png)

   * Nyissa meg a Spark-előzmények felhasználói felület és a YARN felhasználói felületen (az alkalmazás szintjén); ehhez válassza a hivatkozásokat az ablak tetején.

### <a name="access-the-storage-container-for-the-cluster"></a>A tároló hozzáférés a fürthöz
1. Azure-kezelővel, bontsa ki a **HDInsight** gyökércsomópont elérhető HDInsight Spark-fürtjei listájának megjelenítéséhez.
2. Bontsa ki a fürt nevét, a tárfiók, és az alapértelmezett tároló, a fürt számára.
   
   ![Storage-fiókot és az alapértelmezett tároló](./media/apache-spark-eclipse-tool-plugin/view-explorer-5.png)
3. Válassza ki a fürthöz rendelt tárolási tároló nevét. A jobb oldali ablaktáblában kattintson duplán a **HVACOut** mappa. Nyissa meg az egyik a **rész -** fájlok, hogy az alkalmazás kimenete.

### <a name="access-the-spark-history-server"></a>A Spark-előzmények kiszolgáló elérhető
1. Az Azure-kezelővel, kattintson a jobb gombbal a Spark-fürt nevét, majd válassza ki **nyissa meg a Spark feladatelőzmények felhasználói felület**. Amikor a rendszer kéri, adja meg a fürt rendszergazdai hitelesítő adatokat. Ezek a fürt kiépítése során adott meg.
2. A Spark előzmények server Irányítópult segítségével az alkalmazás neve keresse meg az alkalmazás csak futása befejeződött. Az előző kódban használatával beállíthatja az alkalmazás neve `val conf = new SparkConf().setAppName("MyClusterApp")`. Igen, a Spark alkalmazásnév lett **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Indítsa el az Ambari portálon
1. Az Azure-kezelővel, kattintson a jobb gombbal a Spark-fürt nevét, majd válassza ki **nyitott fürt Management Portal (Ambari)**. 
2. Amikor a rendszer kéri, adja meg a fürt rendszergazdai hitelesítő adatokat. Ezek a fürt kiépítése során adott meg.

### <a name="manage-azure-subscriptions"></a>Az Azure-előfizetések kezelése
Alapértelmezés szerint a HDInsight eszközzel az Eclipse Azure eszköztára a Spark-fürtök az összes Azure-előfizetések sorolja fel. Ha szükséges, megadhatja az előfizetéseket, amelyekhez a fürt eléréséhez. 

1. Az Azure-kezelővel, kattintson a jobb gombbal a **Azure** gyökércsomópont, és válassza ki **előfizetések kezelése oldalt**. 
2. A párbeszédpanelen jelölőnégyzetek, amelyet szeretne elérni, és válassza ki az előfizetés **Bezárás**. Igény szerint kiválaszthatja **Kijelentkezés** Ha azt szeretné, hogy kijelentkezik, az Azure-előfizetéshez.

## <a name="run-a-spark-scala-application-locally"></a>A Spark Scala alkalmazás helyileg történő futtatása
Azure eszköztára eclipse-ben a HDInsight Tools használatával Spark Scala-alkalmazások helyi futtatása a munkaállomáson. Általában ezek az alkalmazások nem szükséges hozzáférési jogot a fürterőforrások például a tárolót, és futtatja, és helyben tesztelheti őket.

### <a name="prerequisite"></a>Előfeltétel
A helyi Spark Scala-alkalmazások Windows rendszerű számítógépeken futtatása, közben kivétel kaphat, ahogy [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). Ez a kivétel akkor fordul elő, mert **WinUtils.exe** Windows hiányzik. 

Ez a hiba elhárításához szükséges [töltse le a végrehajtható fájl](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) egy olyan helyre, például a **C:\WinUtils\bin**, majd adja hozzá a következő környezeti változó **HADOOP_HOME** és értékének beállítása a változó **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>A helyi Spark Scala-alkalmazások futtatása
1. Indítsa el az Eclipse, és hozzon létre egy projektet. Az a **új projekt** párbeszédpanelen válassza a következőket, majd válassza ki **következő**.
   
   * A bal oldali panelen válassza ki a **HDInsight** elemet.
   * A jobb oldali ablaktáblában jelölje ki a **a Spark on HDInsight helyi futtatása minta (Scala)**.

   ![A New Project (Új projekt) párbeszédpanel](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)
   
2. Arra, hogy a projekt részleteit, hajtsa végre a 3-6. lépéseket a korábbi szakaszában [beállítani a Spark Scala-projektjét, amely egy HDInsight Spark-fürt](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

3. A sablon hozzáadása egy mintakód (**LogQuery**) alatt a **src** mappát, amelyet a számítógépen helyileg futtathat.
   
   ![LogQuery helye](./media/apache-spark-eclipse-tool-plugin/local-app.png)
   
4. Kattintson a jobb gombbal a **LogQuery** alkalmazás, pont **futtató**, majd válassza ki **1 Scala alkalmazás**. A kimeneti hasonlóan ez jelenik meg a **konzol** lapon:
   
   ![Spark alkalmazás helyi futtatás eredménye](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="known-problems"></a>Ismert problémák
Amikor a fürt hivatkozásra, akkor kérem, hogy adja meg a tároló hitelesítő adatai.

![Interaktív bejelentkezés](./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png)

A feladatok küldéséhez két módja van. Ha tárolási hitelesítő adat áll rendelkezésre, kötegelt módban használandó a feladat elküldéséhez. Más interaktív módban lesz érvényben. Ha a fürt foglalt, kaphat a hiba alább látható.

![a hibaüzenet eclipse-ben amikor fürt foglalt](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png)

![a hibaüzenet eclipse-ben amikor fürt foglalt](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png)

## <a name="feedback"></a>Visszajelzés
Ha rendelkezik olyan visszajelzést, vagy bármely egyéb problémákat tapasztal, amikor ezzel az eszközzel, küldjön egy e-mailt hdivstool@microsoft.com.

## <a name="seealso"></a>Lásd még:
* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek
* [Spark és BI: Interaktív adatelemzés végrehajtása a Spark on HDInsight használatával, BI-eszközökkel](apache-spark-use-bi-tools.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az épület-hőmérséklet elemzésére HVAC-adatok alapján](apache-spark-ipython-notebook-machine-learning.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [A webhelynapló elemzése a Spark on HDInsight használatával](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Létrehozása és alkalmazások futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Spark-fürtön a Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [Az intellij-t Azure eszközkészlet segítségével hozza létre, és küldje el a Spark Scala-alkalmazások](apache-spark-intellij-tool-plugin.md)
* [Az intellij-t Azure eszközkészlet segítségével VPN-en keresztül távolról Spark-alkalmazások](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Az intellij-t Azure eszközkészlet segítségével SSH keresztül távolról Spark-alkalmazások](../hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Használja a HDInsight Tools for IntelliJ a Hortonworks védőfal](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Zeppelin notebookok használata Spark-fürttel HDInsighton](apache-spark-zeppelin-notebook.md)
* [Jupyter notebookokhoz elérhető kernelek a HDInsight Spark-fürtjében](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)

