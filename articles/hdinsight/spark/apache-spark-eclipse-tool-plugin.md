---
title: 'Azure-eszközkészlet az Eclipse-hez: A HDInsight Spark Scala-alkalmazások létrehozása '
description: Az Azure Toolkit for Eclipse HDInsight Tools használatával írt Scala Spark alkalmazásokat fejleszthet, és küldheti el ezeket a HDInsight Spark-fürt közvetlenül az Eclipse IDE-ből.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/30/2017
ms.author: hrasheed
ms.openlocfilehash: 123c05470f8f162095b92c0894716c7b5d2a92b2
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648771"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Eclipse-hez készült Azure eszközkészlet használata egy HDInsight-fürtön az Apache Spark-alkalmazások létrehozása

HDInsight Tools használata az Azure-eszközkészlet [Eclipse](https://www.eclipse.org/) fejleszthet [Apache Spark](https://spark.apache.org/) írt alkalmazások [Scala](https://www.scala-lang.org/) , és küldje el őket az Azure HDInsight Spark-fürt közvetlenül az Eclipse IDE. A HDInsight eszközök beépülő modul a különböző módokon használhatja:

* És beküldésének Scala Spark-alkalmazását egy HDInsight Spark-fürtön.
* Az Azure HDInsight Spark fürterőforrásokat eléréséhez.
* Fejlesztés és a Scala Spark-alkalmazás helyileg történő futtatása.

> [!IMPORTANT]  
> Ez az eszköz segítségével létrehozása és elküldése az alkalmazások csak a HDInsight Spark-fürt Linux rendszeren.
> 
> 

## <a name="prerequisites"></a>Előfeltételek

* A HDInsight Apache Spark-fürt. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).
* Oracle Java Development Kit 8-as verzió, az Eclipse IDE-modul használt. A letöltheti a [Oracle webhely](https://aka.ms/azure-jdks).
* Az eclipse IDE. Ez a cikk az Eclipse Neon használja. A telepítheti a [Eclipse webhely](https://www.eclipse.org/downloads/).



## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse-and-the-scala-plug-in"></a>Azure-eszközkészlet HDInsight eszközök telepítéséhez az eclipse-ben és a Scala beépülő modul

### <a name="install-azure-toolkit-for-eclipse"></a>Eclipse-hez készült Azure-eszközkészlet telepítése
HDInsight Tools for eclipse-ben érhető el az Azure Toolkit for Eclipse részeként. A telepítési utasításokért lásd: [Azure-eszközkészlet telepítése az Eclipse-hez](https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-installation).

### <a name="install-the-scala-plug-in"></a>A Scala beépülő modul telepítése
Amikor megnyitja az Eclipse, a HDInsight eszköz automatikusan észleli, hogy telepítette-e a beépülő modul Scala. Válassza ki **OK** a folytatáshoz, majd kövesse az utasításokat követve telepítse a beépülő modul az Eclipse Marketplace-ről.

![A beépülő modul Scala automatikus telepítése](./media/apache-spark-eclipse-tool-plugin/auto-install-scala.png)

Felhasználói lehetőségek közül választhat [jelentkezzen be Azure-előfizetés](#sign-in-to-your-azure-subscription), vagy [egy HDInsight-fürtöt](#link-a-cluster) az Ambari felhasználónév/jelszó és a tartományhoz csatlakoztatott credential elindításához. 

## <a name="sign-in-to-your-azure-subscription"></a>Jelentkezzen be az Azure-előfizetésébe
1. Indítsa el az Eclipse IDE, és nyissa meg az Azure Explorert. Az a **ablak** menüjében válassza **nézet megjelenítése**, majd válassza ki **más**. A megnyíló párbeszédpanelen bontsa ki a **Azure**válassza **Azure Explorer**, majd válassza ki **OK**.

   ![Nézet párbeszédpanel megjelenítése](./media/apache-spark-eclipse-tool-plugin/view-explorer-1.png)
1. Kattintson a jobb gombbal a **Azure** csomópontot, és válassza ki **jelentkezzen be a**.
1. Az a **Azure bejelentkezési** párbeszédpanel mezőben válassza ki a hitelesítési módszert, válassza ki **jelentkezzen be a**, és adja meg Azure hitelesítő adatait.
   
   ![Az Azure bejelentkezési párbeszédpanel](./media/apache-spark-eclipse-tool-plugin/view-explorer-2.png)
1. Miután jelentkezett be, a **kiválasztása az előfizetések** párbeszédpanelen megjelenik az összes Azure-előfizetést társított hitelesítő adatokat. Kattintson a **kiválasztása** a párbeszédpanel bezárásához.

   ![Kiválasztása az előfizetések párbeszédpanel](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)
1. Az a **Azure Explorer** lapon, bontsa ki a **HDInsight** megtekintéséhez, a HDInsight Spark-fürtök az előfizetéséhez.
   
   ![HDInsight Spark-fürtök Azure Explorer](./media/apache-spark-eclipse-tool-plugin/view-explorer-3.png)
1. Tovább bővítheti, egy fürtcsomópont nevét a fürthöz társított erőforrások (például storage-fiókok) megtekintéséhez.
   
   ![A fürt nevét források kibontása](./media/apache-spark-eclipse-tool-plugin/view-explorer-4.png)

## <a name="link-a-cluster"></a>Egy fürtöt
Kapcsolat egy normál fürt kezelése az Ambari felhasználónév használatával. Hasonlóképpen, egy tartományhoz csatlakoztatott HDInsight-fürtöt, a kapcsolat a tartomány és felhasználónév, mint például a user1@contoso.com.

1. Válassza ki **egy fürtöt** a **Azure Explorer**.

   ![hivatkozás fürt helyi menü](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

1. Adja meg **fürtnév**, **felhasználónév** és **jelszó**, majd kattintson az OK gombra kattintva a fürtöt. Szükség esetén adja meg a Tárfiókot, a Tárfiók kulcsát, és válassza ki a tároló dolgozhat a bal oldali fanézetben a storage Explorer
   
   ![hivatkozás fürt párbeszédpanel](./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog.png)
   
   > [!NOTE]  
   > Ha a fürt egyaránt bejelentkezett az Azure-előfizetés és a fürthöz társított a társított storage-kulcs, a felhasználónevet és jelszót használunk.
   > ![az eclipse-ben a Storage Explorerben](./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png)

1. Láthatja, hogy a társított fürt **HDInsight** OK gombra kattintunk, ha a bemeneti adatokat a megfelelő csomópont. Ehhez a fürthöz társított alkalmazás most már küldhet.

   ![a csatolt fürt](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

1. Fürt is megszüntetheti **Azure Explorer**.
   
   ![nem összekapcsolt fürt](./media/apache-spark-intellij-tool-plugin/unlink.png)


## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Egy Spark Scala-projektet egy HDInsight Spark-fürt beállítása

1. Az Eclipse IDE munkaterületen válassza ki a **fájl**válassza **új**, majd válassza ki **projekt**. 
1. Bontsa ki az új projekt varázslóval **HDInsight**válassza **a Spark on HDInsight (Scala)**, majd válassza ki **tovább**.

   ![A Spark on HDInsight (Scala) projekt kiválasztása](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)
1. A Scala-projekt létrehozása varázsló automatikusan észleli, hogy telepítette-e a beépülő modul Scala. Válassza ki **OK** folytatja a beépülő modul Scala letöltése, és kövesse az utasításokat az Eclipse újraindítását.

   ![Scala ellenőrzése](./media/apache-spark-eclipse-tool-plugin/auto-install-scala-2.png)
1. Az a **új HDInsight Scala projekt** párbeszédpanelen adja meg az alábbi értékeket, és válassza **tovább**:
   * Adja meg a projekt nevét.
   * Az a **JRE** területen győződjön meg arról, hogy **JRE végrehajtási környezet használata** értékre van állítva **JavaSE – 1.7-es** vagy újabb.
   * Az a **Spark könyvtár** területen kiválaszthatja **konfigurálhatja a Spark SDK-t használja Maven** lehetőséget.  Az eszköz együttműködik a megfelelő verziójú Spark SDK és a Scala SDK-val. Azt is beállíthatja **manuálisan adja hozzá az Spark SDK** lehetőséget, töltse le és adja hozzá manuálisan a Spark-SDK által.

   ![Új HDInsight Scala projekt párbeszédpanel](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)
1. Jelölje ki a következő párbeszédpanel **Befejezés**. 
   
  
## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Egy HDInsight Spark-fürt Scala-alkalmazás létrehozása

1. Az Eclipse IDE-ben a Package Explorerben bontsa ki a korábban létrehozott projekt, kattintson a jobb gombbal **src**, mutasson a **új**, majd válassza ki **más**.
1. Az a **válassza ki a varázsló** párbeszédpanelen bontsa ki **Scala a varázslókban**, jelölje be **Scala objektum**, majd válassza ki **tovább**.
   
   ![Válassza ki a varázsló párbeszédpanelje](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)
1. Az a **hozzon létre új fájl** párbeszédpanelen adja meg az objektum nevét, és válassza **Befejezés**.
   
   ![Hozzon létre új fájl párbeszédpanel](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)
1. Illessze be az alábbi kódot a szövegszerkesztőben:
   
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
1. Futtassa az alkalmazást egy HDInsight Spark-fürtön:
   
   a. A Package Explorerben kattintson a jobb gombbal a projekt nevét, és válassza **Spark-alkalmazás elküldéséhez HDInsight**.        
   b. Az a **Spark küldésének** párbeszédpanelen adja meg az alábbi értékeket, és válassza **küldés**:
      
   * A **fürtnév**, válassza ki a HDInsight Spark-fürtöt, amelyen szeretné, az alkalmazás futtatásához.
   * Válasszon ki egy összetevőt az Eclipse-projektet, vagy válasszon egyet a merevlemez-meghajtóról. Az alapértelmezett érték, amely a Package Explorerben kattintson jobb gombbal az elem függ.
   * Az a **Main osztály neve** legördülő listából válassza ki, a beküldési varázsló megjeleníti az összes objektum nevét a projektből. Válassza ki, vagy adjon meg egy másikat, amelyet futtatni szeretne. Ha egy összetevő egy merevlemez-meghajtóról, a fő osztálynév manuálisan kell megadnia. 
   * Mivel ebben a példában az alkalmazás kódja nem igényel parancssori argumentumokat vagy hivatkozás JAR-fájlok kivételével, vagy a fájlokat, akkor a többi mezőbe üresen hagyhatja.
        
     ![A Spark küldésének párbeszédpanel](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)
1. A **Spark küldésének** lapon el kell indulnia, megjelenítés, a folyamat állapotát. A piros gombra kattintva leállíthatja az alkalmazás a **Spark küldésének** ablak. A naplók az adott alkalmazás futtatásához a (a kék mezőben az a képen kimaradásával) földgömb ikon kiválasztásával is megtekintheti.
      
   ![A Spark küldésének ablak](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)


## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Hozzáférés és a HDInsight Spark-fürtök kezelése a HDInsight Tools használatával Azure-eszközkészlet az Eclipse-hez
HDInsight eszközök, többek között a feladat kimenetének elérése használatával különféle műveleteket hajthat végre.

### <a name="access-the-job-view"></a>Hozzáférés a feladatnézetben
1. Az Azure Explorerben bontsa ki a **HDInsight**, bontsa ki a Spark-fürt nevét, és válassza **feladatok**. 

   ![Feladat megtekintése csomópont](./media/apache-spark-eclipse-tool-plugin/job-view-node.png)

1. Válassza ki a **feladatok** csomópont. Ha alacsonyabb, mint a Java-verzió **1.8-as**, HDInsight eszközök automatikusan telepít emlékeztető a **E (fx) clipse** beépülő modul. Válassza ki **OK** a folytatáshoz, majd kövesse a varázsló telepítheti az Eclipse-piacteréről, és indítsa újra az eclipse-ben. 

   ![E (fx) clipse telepítése](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

1. Nyissa meg a feladat nézet a **feladatok** csomópont. A jobb oldali ablaktáblán a **Spark-feladat nézet** lap megjeleníti a fürtön futó összes alkalmazást. Válassza ki, amelynek meg szeretné tekinteni a további részleteket az alkalmazás nevét.

   ![Alkalmazás adatai](./media/apache-spark-eclipse-tool-plugin/view-job-logs.png)

   Ezután elvégezhető ezen műveletek egyikét:

   * Vigye a mutatót a feladatgrafikon. A futó feladat alapvető információ jeleníti meg. Válassza ki a feladatábra csúcsaira, és láthatja a szakaszok és minden feladat által létrehozott adatokat.

     ![Feladat fázis részletei](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * Válassza ki a **Log** fülre kattintva megtekintheti a gyakran használt naplókat, beleértve a **illesztőprogram Stderr**, **illesztőprogram Stdout**, és **Directory Info**.

     ![Napló részletei](./media/apache-spark-eclipse-tool-plugin/Job-log-info.png)

   * Nyissa meg a Spark-előzmények felhasználói felület és az Apache Hadoop YARN felhasználói felületén (az alkalmazás szintjén) a az ablak tetején lévő hivatkozások kiválasztásával.

### <a name="access-the-storage-container-for-the-cluster"></a>A storage-tárolót, a fürt eléréséhez
1. Az Azure Explorerben bontsa ki a **HDInsight** gyökércsomópont egy HDInsight Spark-fürtökön elérhető listájának megtekintéséhez.
1. Bontsa ki a fürt nevét, a storage-fiókot és az alapértelmezett tárolókat a fürt számára.
   
   ![Storage-fiók és az alapértelmezett tároló](./media/apache-spark-eclipse-tool-plugin/view-explorer-5.png)
1. Válassza ki a fürthöz társított tároló nevét. A jobb oldali ablaktáblában kattintson duplán a **HVACOut** mappát. Nyissa meg az egyik a **. rész –** fájlok, hogy az alkalmazás kimenete.

### <a name="access-the-spark-history-server"></a>Hozzáférés a Spark-előzménykiszolgáló
1. Az Azure Explorerben, kattintson a jobb gombbal a Spark-fürt nevét, és válassza **nyissa meg a Spark feladatelőzmények felhasználói felület**. Amikor a rendszer kéri, adja meg a rendszergazdai hitelesítő adatok a fürt. Ezek a fürt kiépítése során adott meg.
1. A Spark előzmények server Irányítópult segítségével az alkalmazás neve keresse meg az alkalmazás csak futtatása befejeződött. A fenti kóddal, a használatával megadhatja az alkalmazás neve `val conf = new SparkConf().setAppName("MyClusterApp")`. Így a Spark-alkalmazás neve lett **MyClusterApp**.

### <a name="start-the-apache-ambari-portal"></a>Az Apache Ambari portal indítása
1. Az Azure Explorerben, kattintson a jobb gombbal a Spark-fürt nevét, és válassza **fürt felügyeleti portál megnyitása (Ambari)**. 
1. Amikor a rendszer kéri, adja meg a rendszergazdai hitelesítő adatok a fürt. Ezek a fürt kiépítése során adott meg.

### <a name="manage-azure-subscriptions"></a>Azure-előfizetések kezelése
Alapértelmezés szerint a HDInsight eszközzel, az Eclipse-hez készült Azure-eszközkészlet a Spark-fürtök, az összes Azure-előfizetések sorolja fel. Ha szükséges, megadhatja az előfizetéseket, amelyekhez a fürt eléréséhez. 

1. Az Azure Explorerben, kattintson a jobb gombbal a **Azure** gyökércsomópont, és válassza ki **előfizetések kezelése**. 
1. A párbeszédpanelen törölje az előfizetést, amelyhez nem szeretne elérni, és jelölje be a jelölőnégyzeteket **Bezárás**. Lehetőség kiválasztásával **Kijelentkezés** Ha jelentkezzen ki az Azure-előfizetéshez szeretne.

## <a name="run-a-spark-scala-application-locally"></a>Helyileg futtathatja a Spark Scala alkalmazásokat
Az Azure Toolkit for Eclipse HDInsight Tools használatával Spark Scala-alkalmazások helyi futtatása a munkaállomáson. Rendszerint ezeket az alkalmazásokat nem kell a hozzáférést a fürt erőforrásai, például a storage-tárolókat, és futtathat, és helyben tesztelheti őket.

### <a name="prerequisite"></a>Előfeltétel
A helyi Spark Scala-alkalmazások egy Windows-számítógépen futtatja, miközben kaphat kivétel leírtak [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). Ez a kivétel lép fel, mert **WinUtils.exe** Windows hiányzik. 

Ez a hiba elhárításához szükséges [a végrehajtható fájl letöltése](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) egy olyan helyre, például **C:\WinUtils\bin**, majd adja hozzá a környezeti változó **HADOOP_HOME** , és állítsa az értékét a változó **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Egy helyi Spark Scala-alkalmazások futtatása
1. Indítsa el az eclipse-ben, és hozzon létre egy projektet. Az a **új projekt** párbeszédpanelen válassza a következőket, és válassza **tovább**.
   
   * A bal oldali panelen válassza ki a **HDInsight** elemet.
   * A jobb oldali ablaktáblán válassza ki a **a Spark on HDInsight helyi futtatása minta (Scala)**.

   ![A New Project (Új projekt) párbeszédpanel](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)
   
1. Adja meg a projekt adatait, hajtsa végre a 3-6. lépést a korábbi szakaszban [egy Spark Scala-projektet egy HDInsight Spark-fürt beállítása](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

1. A sablon hozzáad egy mintakód (**LogQuery**) alatt a **src** mappát, amely futtatása helyileg a számítógépen.
   
   ![LogQuery helye](./media/apache-spark-eclipse-tool-plugin/local-app.png)
   
1. Kattintson a jobb gombbal a **LogQuery** alkalmazás, pont **futtató**, majd válassza ki **1 Scala alkalmazás**. Kimeneti Ez jelenik meg, mint a **konzol** lapon:
   
   ![A Spark-alkalmazás helyi futtatás eredménye](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="known-problems"></a>Ismert problémák
Amikor a fürt hivatkozásra, e javasolna, hogy adja meg a storage hitelesítő adatai.

![Interaktív bejelentkezés](./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png)

Két módja a feladatok elküldéséhez. Ha a tároló hitelesítő adat áll rendelkezésre, kötegelt mód használandó a feladat elküldéséhez. Ellenkező esetben az interaktív mód lesz használható. Ha a fürt foglalt, kaphat az alábbi hiba.

![eclipse-ben jelenik meg a hiba esetén a foglalt fürt](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png)

![eclipse-ben jelenik meg a hiba esetén a foglalt fürt](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png)

## <a name="feedback"></a>Visszajelzés
Ha bármilyen visszajelzése van, vagy bármely egyéb problémákat tapasztal, amikor ezzel az eszközzel, küldjön egy e-mailt hdivstool@microsoft.com.

## <a name="seealso"></a>Lásd még:
* [Áttekintés: Az Apache Spark on Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek
* [Az Apache Spark és BI: Spark on HDInsight használatával, BI-eszközökkel interaktív adatelemzés végrehajtása](apache-spark-use-bi-tools.md)
* [Az Apache Spark és Machine Learning: A Spark használata a HDInsight HVAC-adatok épület-hőmérséklet elemzésére](apache-spark-ipython-notebook-machine-learning.md)
* [Az Apache Spark és Machine Learning: A HDInsight Spark használata az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [A webhelynapló elemzése a HDInsight az Apache Spark használatával](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Létrehozása és alkalmazások futtatásához
* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [IntelliJ-hez készült Azure-eszközkészlet használatával hozzon létre, és küldje el a Spark Scala-alkalmazások](apache-spark-intellij-tool-plugin.md)
* [IntelliJ-hez készült Azure eszközkészlet használata Apache Spark-alkalmazások VPN-en keresztül távolról](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [IntelliJ-hez készült Azure eszközkészlet használata Apache Spark-alkalmazások távolról az ssh-n keresztül](../hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [HDInsight Tools for IntelliJ with hortonworks – tesztkörnyezet használata](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Az Apache Zeppelin notebookok használata a HDInsight Apache Spark-fürt](apache-spark-zeppelin-notebook.md)
* [Notebookokhoz elérhető kernelek Jupyter a HDInsight az Apache Spark-fürt](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)

