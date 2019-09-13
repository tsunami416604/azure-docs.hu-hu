---
title: 'Oktatóanyag – Azure Toolkit for IntelliJ: Spark-alkalmazás a HDInsight-fürthöz'
description: Oktatóanyag – a Azure Toolkit for IntelliJ használatával fejlesztheti a Scala-ben írt Spark-alkalmazásokat, és beküldheti azokat egy HDInsight Spark-fürtbe.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: hrasheed
ms.openlocfilehash: b13eb6f61aed37344e0df3c864e021f68a4ca85b
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900287"
---
# <a name="tutorial-use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-hdinsight-cluster"></a>Oktatóanyag: Apache Spark alkalmazások létrehozása a HDInsight-fürthöz Azure Toolkit for IntelliJ használatával

Ez az oktatóanyag azt mutatja be, hogyan használható a Azure Toolkit for IntelliJ beépülő modul a [Scala](https://www.scala-lang.org/)-ben írt Apache Spark alkalmazások fejlesztéséhez, majd a HDInsight Spark-fürtbe való beküldéshez közvetlenül a IntelliJ integrált fejlesztői környezetből (ide). A beépülő modult többféleképpen is használhatja:

* Scala Spark-alkalmazás fejlesztése és beküldése egy HDInsight Spark-fürtön.
* Hozzáférés a Azure HDInsight Spark-fürt erőforrásaihoz.
* A Scala Spark alkalmazást helyileg fejlesztheti és futtathatja.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * A Azure Toolkit for IntelliJ beépülő modul használata
> * Apache Spark alkalmazások fejlesztése
> * Alkalmazás beküldése az Azure HDInsight-fürtbe

## <a name="prerequisites"></a>Előfeltételek

* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

* [Oracle Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).  Ez az oktatóanyag a Java-verzió 8.0.202 használja.

* IntelliJ IDEA. Ez a cikk [a IntelliJ IDEA Community ver-t használja.  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure Toolkit for IntelliJ.  Lásd: [a Azure Toolkit for IntelliJ telepítése](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).

## <a name="install-scala-plugin-for-intellij-idea"></a>Scala beépülő modul az IntelliJ IDEA-hoz

A Scala beépülő modul telepítéséhez hajtsa végre a következő lépéseket:

1. Nyissa meg az IntelliJ IDEA-t.

2. Az üdvözlőképernyőn kattintson a**plugins** **konfigurálása** > elemre a **plugins** ablak megnyitásához.
   
    ![A Scala beépülő modul engedélyezése](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin.png)

3. Válassza a **telepítés** lehetőséget az új ablakban Kiemelt Scala beépülő modulhoz.  

    ![A Scala beépülő modul telepítése](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. A beépülő modul sikeres telepítését követően újra kell indítania az IDE-t.

## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>Spark Scala-alkalmazás létrehozása HDInsight Spark-fürthöz

1. Indítsa el a IntelliJ ÖTLETET, és válassza az **új projekt létrehozása** elemet az **új projekt** ablak megnyitásához.

2. Válassza ki az **Azure Spark/HDInsight** elemet a bal oldali panelen.

3. Válassza ki a **Spark Project (Scala)** elemet a főablakból.

4. A **Build eszköz** legördülő listából válassza ki a következők egyikét:
   * **Maven** a Scala projekt-létrehozás varázsló támogatásához.
   * A **SBT** kezelése és a Scala-projekt kiépítése.

     ![A New project (Új projekt) párbeszédablak.](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. Kattintson a **Tovább** gombra.

6. Az **új projekt** ablakban adja meg a következő információkat:  

    |  Tulajdonság   | Leírás   |  
    | ----- | ----- |  
    |Projektnév| Adjon meg egy nevet.  Ebben az oktatóanyagban a következőt használjuk: `myApp`.|  
    |Projekt&nbsp;helye| Adja meg a kívánt helyet a projekt mentéséhez.|
    |Projekt SDK| Előfordulhat, hogy az első ÖTLETE üres.  Válassza az **új...** lehetőséget, és navigáljon a JDK-hez.|
    |Spark-verzió|A létrehozás varázsló a Spark SDK és a Scala SDK megfelelő verzióját integrálja. Ha a Spark-fürt verziója 2.0-nál korábbi, válassza a **Spark 1.x** lehetőséget. Máskülönben válassza a **Spark2.x** lehetőséget. Ez a példa a **Spark 2.3.0 (Scala 2.11.8)** használja.|

    ![A Spark SDK kiválasztása](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

7. Válassza a **Finish** (Befejezés) elemet.  A projekt elérhetővé válása néhány percet igénybe vehet.

8. A Spark-projekt automatikusan létrehoz egy összetevőt. Az összetevő megtekintéséhez tegye a következőket:

   a. A menüsávban navigáljon a **fájl** > **projekt szerkezete...** elemre.

   b. A **projekt szerkezete** **ablakban válassza az**összetevők elemet.  

   c. A lelet megtekintése után válassza a **Mégse** lehetőséget.

      ![Az összetevő adatai a párbeszédpanelen](./media/apache-spark-intellij-tool-plugin/default-artifact.png)

9. Adja hozzá az alkalmazás forráskódját a következő módon:

    a. A projekt területen navigáljon a **SajátPr** > **src** > **Main** > **Scala**webhelyre.  

    b. Kattintson a jobb gombbal a **Scala**elemre, majd navigáljon az **új** > **Scala osztályhoz**.

   ![A Scala-osztály projektből való létrehozásának parancsai](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   c. Az **új Scala-osztály létrehozása** párbeszédpanelen adjon meg egy nevet, válassza ki **az objektum** elemet a **típus** legördülő listából, majd kattintson **az OK gombra**.

     ![Új Scala-osztály létrehozása párbeszédpanel](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. A **SajátPr. Scala** fájl ekkor megnyílik a fő nézetben. Cserélje le az alapértelmezett kódot az alább található kódra:  

        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
    
        object myApp{
            def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("myApp")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows that have only one digit in the seventh column in the CSV file
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasbs:///HVACOut")
            }
    
        }

    A kód beolvassa az adatokat a HVAC. csv fájlból (az összes HDInsight Spark-fürtön elérhető), lekéri azokat a sorokat, amelyek csak egy számjegytel rendelkeznek a CSV-fájl hetedik `/HVACOut` oszlopában, és a kimenetet a fürt alapértelmezett tárolójában írja.

## <a name="connect-to-your-hdinsight-cluster"></a>Kapcsolódás a HDInsight-fürthöz
A felhasználó [bejelentkezhet az Azure-előfizetésbe](#sign-in-to-your-azure-subscription), vagy [összekapcsolhat egy HDInsight-fürtöt](#link-a-cluster) a Ambari Felhasználónév/jelszó vagy a tartományhoz csatlakoztatott hitelesítő adatok használatával a HDInsight-fürthöz való csatlakozáshoz.

### <a name="sign-in-to-your-azure-subscription"></a>Jelentkezzen be az Azure-előfizetésébe

1. A menüsávban navigáljon a **Megtekintés** > **eszközre a Windows** > **Azure Explorerben**.
       
   ![Az Azure Explorer megjelenítése](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

2. Az Azure Explorerben kattintson a jobb gombbal az **Azure** -csomópontra, majd válassza a **Bejelentkezés**lehetőséget.
   
   ![Explorer jobb gombbal kattintson az Azure elemre](./media/apache-spark-intellij-tool-plugin/explorer-rightclick-azure.png)

3. Az **Azure bejelentkezési** párbeszédpanelen válassza az **eszköz bejelentkezés**lehetőséget, majd válassza a **Bejelentkezés**lehetőséget.

    ![Explorer 2 megtekintése](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

4. Az **Azure-eszköz bejelentkezési** párbeszédpanelén kattintson a **Másolás & Megnyitás**elemre.
   
   ![Explorer 5 megtekintése](./media/apache-spark-intellij-tool-plugin/view-explorer-5.png)

5. A böngésző felületén illessze be a kódot, majd kattintson a **tovább**gombra.
   
   ![Explorer 6 megtekintése](./media/apache-spark-intellij-tool-plugin/view-explorer-6.png)

6. Adja meg az Azure-beli hitelesítő adatait, majd zárjuk be a böngészőt.
   
   ![Explorer 7 megtekintése](./media/apache-spark-intellij-tool-plugin/view-explorer-7.png)

7. Miután bejelentkezett, az **előfizetések kiválasztása** párbeszédpanel felsorolja az összes olyan Azure-előfizetést, amely társítva van a hitelesítő adatokhoz. Válassza ki az előfizetését, majd kattintson a **kiválasztás** gombra.

    ![Az előfizetések kiválasztása párbeszédpanel](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

8. Az **Azure Explorerben**bontsa ki a **HDInsight** elemet, és tekintse meg az előfizetésekben található HDInsight Spark-fürtöket.

    ![Explorer 3 megtekintése](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

9.  Ha meg szeretné tekinteni a fürthöz társított erőforrásokat (például Storage-fiókokat), akkor tovább bővítheti a fürt neve csomópontot.

    ![Kibontott fürt – név csomópont](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

### <a name="link-a-cluster"></a>Fürt csatolása

Egy HDInsight-fürtöt az Apache Ambari Managed username használatával kapcsolhat össze. Hasonlóképpen, a tartományhoz csatlakoztatott HDInsight-fürtökhöz a tartomány és a Felhasználónév használatával is kapcsolódhat, például `user1@contoso.com`:. Emellett a Livy Service-fürtöt is összekapcsolhatja.

1. A menüsávban navigáljon a **Megtekintés** > **eszközre a Windows** > **Azure Explorerben**.

2. Az Azure Explorerben kattintson a jobb gombbal a **HDInsight** csomópontra, majd válassza a **fürt csatolása**lehetőséget.

   ![fürt helyi menüjének csatolása](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

3. A **fürt csatolása** ablakban elérhető beállítások attól függően változnak, hogy melyik értéket választotta ki az **Erőforrás típusa** legördülő listából.  Adja meg az értékeket, majd kattintson **az OK gombra**.

    * **HDInsight-fürt**  
  
        |Tulajdonság |Value |
        |----|----|
        |Erőforrás típusának csatolása|Válassza ki a **HDInsight-fürtöt** a legördülő listából.|
        |Fürt neve/URL-címe| Adja meg a fürt nevét.|
        |Hitelesítés típusa| Szabadság **alapszintű hitelesítésként**|
        |Felhasználónév| Adja meg a fürt felhasználói nevét, az alapértelmezett érték a rendszergazda.|
        |Windows 10| Adja meg a felhasználónévhez tartozó jelszót.|
    
        ![HDInsight-fürt csatolása párbeszédpanel](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Livy szolgáltatás**  
  
        |Tulajdonság |Value |
        |----|----|
        |Erőforrás típusának csatolása|Válassza ki a **Livy szolgáltatást** a legördülő listából.|
        |Livy végpont| Livy-végpont megadása|
        |Fürt neve| Adja meg a fürt nevét.|
        |Fonal végpontja|Nem kötelező.|
        |Hitelesítés típusa| Szabadság **alapszintű hitelesítésként**|
        |Felhasználónév| Adja meg a fürt felhasználói nevét, az alapértelmezett érték a rendszergazda.|
        |Windows 10| Adja meg a felhasználónévhez tartozó jelszót.|

        ![Livy-fürt csatolása párbeszédpanel](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

1. A csatolt fürtöt a **HDInsight** csomóponton tekintheti meg.

   ![csatolt fürt](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

2. A fürtöket az **Azure Explorerben**is leválaszthatja.

   ![nem összekapcsolt fürt](./media/apache-spark-intellij-tool-plugin/hdi-unlinked-cluster.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Spark Scala-alkalmazás futtatása HDInsight Spark-fürtön

A Scala-alkalmazás létrehozása után elküldheti azt a fürtnek.

1. A projekt területen navigáljon **a SajátPr** > **src** > **Main** > **Scala** > -**SajátPr**.  Kattintson a jobb gombbal a **SajátPr**elemre, majd válassza a **Spark-alkalmazás beküldése** lehetőséget (ez valószínűleg a lista alján található).
    
      ![A Spark-alkalmazás beküldése a HDInsight parancsba](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. A **Spark-alkalmazás elküldése** párbeszédpanelen válassza az **1 lehetőséget. Spark on HDInsight**.

3. A **konfiguráció szerkesztése** ablakban adja meg a következő értékeket, majd kattintson **az OK gombra**:

    |Tulajdonság |Value |
    |----|----|
    |Spark-fürtök (csak Linux)|Válassza ki azt a HDInsight Spark-fürtöt, amelyen futtatni szeretné az alkalmazást.|
    |Válassza ki a elküldeni kívánt összetevőt|Hagyja meg az alapértelmezett beállítást.|
    |Fő osztály neve|Az alapértelmezett érték a kijelölt fájl fő osztálya. Az osztályt a három pont ( **..** .) lehetőség kiválasztásával módosíthatja.  és válasszon másik osztályt.|
    |Feladatok konfigurációi|Módosíthatja az alapértelmezett kulcsokat és/vagy értékeket. További információ: [Apache Livy REST API](https://livy.incubator.apache.org./docs/latest/rest-api.html).|
    |Parancssori argumentumok|Szükség esetén megadhat argumentumokat szóközzel elválasztva a Main osztály számára.|
    |Hivatkozott tégelyek és hivatkozott fájlok|Megadhatja a hivatkozott tégelyek és fájlok elérési útját, ha van ilyen. Tallózással is tallózhat az Azure-beli virtuális fájlrendszerben, amely jelenleg csak a ADLS Gen 2 fürtöt támogatja. További információk: [Apache Spark konfiguráció](https://spark.apache.org/docs/latest/configuration.html#runtime-environment).  Lásd még: [erőforrások feltöltése a fürtbe](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer).|
    |Feladatok feltöltési tárolója|Kibontás a további beállítások megjelenítéséhez.|
    |Tárolási típus|Válassza az **Azure Blob használata a feltöltéshez** lehetőséget a legördülő listából.|
    |Tárfiók|Adja meg a Storage-fiókját.|
    |Tárkulcs|Adja meg a tárolási kulcsot.|
    |Storage-tároló|A Storage- **fiók** és a **Storage-kulcs** megadása után válassza ki a tárolót a legördülő listából.|

    ![A Spark beküldése párbeszédpanel](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

4. Válassza a **SparkJobRun** lehetőséget a projektnek a kiválasztott fürthöz való elküldéséhez. A **fürt távoli Spark-feladata** lapon a feladatok végrehajtási folyamata látható alul. Az alkalmazást a vörös gombra kattintva állíthatja le. A feladatok kimenetének eléréséről a jelen cikk későbbi, "a HDInsight Spark-fürtök elérése és kezelése a Azure Toolkit for IntelliJ használatával" című szakaszában talál további információt.  
      
    ![A Spark beküldési ablaka](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Apache Spark alkalmazások hibakeresése helyileg vagy távolról egy HDInsight-fürtön 

Azt is javasoljuk, hogy a Spark-alkalmazást a fürtön küldje el. Ehhez állítsa be a paramétereket a **Run/debug konfigurációk** ide. További információkért lásd: [Apache Spark alkalmazások hibakeresése helyileg vagy távolról egy HDInsight-fürtön az SSH-n keresztüli Azure Toolkit for IntelliJ használatával](apache-spark-intellij-tool-debug-remotely-through-ssh.md).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>HDInsight Spark-fürtök elérése és kezelése Azure Toolkit for IntelliJ használatával

Azure Toolkit for IntelliJ használatával különböző műveleteket hajthat végre.  A műveletek többsége az **Azure Explorerben**indítható el.  A menüsávban navigáljon a **Megtekintés** > **eszközre a Windows** > **Azure Explorerben**.

### <a name="access-the-job-view"></a>A feladatok nézet elérése

1. Az Azure Explorerben navigáljon a fürt > > **feladatok** **HDInsight** > .\<

    ![Nyomtatási nézet csomópontja](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

2. A jobb oldali ablaktáblán a **Spark-feladatok nézet** lapon a fürtön futó összes alkalmazás látható. Válassza ki annak az alkalmazásnak a nevét, amelyhez további részleteket szeretne látni.

    ![Alkalmazás adatai](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)

3. Az alapszintű futó feladat adatainak megjelenítéséhez vigye a kurzort a feladat gráf fölé. Ha meg szeretné tekinteni a szakaszok gráfját és az összes feladathoz tartozó információt, válasszon ki egy csomópontot a feladathoz tartozó diagramon.

    ![A feladatok szakaszának részletei](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. A gyakran használt naplók, például az *illesztőprogram-stderr*, az *illesztőprogram StdOut*és a *könyvtár adatainak*megtekintéséhez válassza a **napló** lapot.

    ![Napló részletei](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

5. Megtekintheti a Spark előzmények felhasználói felületét és a fonal felhasználói felületét (az alkalmazás szintjén) az ablak tetején található hivatkozásra kattintva.

### <a name="access-the-spark-history-server"></a>A Spark-előzmények kiszolgálójának elérése

1. Az Azure Explorerben bontsa ki a **HDInsight**csomópontot, kattintson a jobb gombbal a Spark-fürt nevére, majd válassza a **Spark-előzmények megnyitása felhasználói felület**lehetőséget.  
2. Amikor a rendszer kéri, adja meg a fürt rendszergazdai hitelesítő adatait, amelyet a fürt beállításakor adott meg.

3. A Spark History Server irányítópultján használhatja az alkalmazás nevét, hogy megkeresse az éppen futó alkalmazást. Az előző kódban a paranccsal `val conf = new SparkConf().setAppName("myApp")`állíthatja be az alkalmazás nevét. Ezért a Spark-alkalmazás neve **SajátPr**.

### <a name="start-the-ambari-portal"></a>A Ambari-portál elindítása

1. Az Azure Explorerben bontsa ki a **HDInsight**csomópontot, kattintson a jobb gombbal a Spark-fürt nevére, majd válassza a **fürt felügyeleti portál megnyitása (Ambari)** elemet.  

2. Amikor a rendszer kéri, adja meg a fürt rendszergazdai hitelesítő adatait. Ezeket a hitelesítő adatokat a fürt telepítési folyamata során adta meg.

### <a name="manage-azure-subscriptions"></a>Azure-előfizetések kezelése

Alapértelmezés szerint a Azure Toolkit for IntelliJ felsorolja a Spark-fürtöket az összes Azure-előfizetésből. Szükség esetén megadhatja az elérni kívánt előfizetéseket.  

1. Az Azure Explorerben kattintson a jobb gombbal az **Azure** -legfelső szintű csomópontra, majd válassza az **előfizetések kiválasztása**lehetőséget.  

2. Az **előfizetések kiválasztása** ablakban törölje a jelölőnégyzet jelölését azon előfizetések mellett, amelyeket nem szeretne elérni, majd válassza a **Bezárás**lehetőséget.

## <a name="spark-console"></a>Spark-konzol

Futtathatja a Spark helyi konzolt (Scala), vagy futtathatja a Spark Livy interaktív munkamenet-konzolt (Scala).

### <a name="spark-local-consolescala"></a>Spark helyi konzol (Scala)

Győződjön meg arról, hogy meggyőződött a WINUTILS. Az EXE előfeltétele.

1. A menüsávban navigáljon > a**konfigurációk szerkesztése..** . lehetőségre.

2. A **Futtatás/hibakeresés konfigurációk** ablak bal oldali ablaktábláján navigáljon **Apache Spark a HDInsight** >  **[Spark on HDInsight] SajátPr**.

3. A fő ablakban válassza a **helyileg futtatott** lapot.

4. Adja meg a következő értékeket, majd kattintson **az OK gombra**:

    |Tulajdonság |Value |
    |----|----|
    |Job Main osztály|Az alapértelmezett érték a kijelölt fájl fő osztálya. Az osztályt a három pont ( **..** .) lehetőség kiválasztásával módosíthatja.  és válasszon másik osztályt.|
    |Környezeti változók|Győződjön meg arról, hogy a HADOOP_HOME értéke helyes.|
    |WINUTILS. exe helye|Győződjön meg arról, hogy az elérési út helyes.|

    ![Helyi konzol beállítása – konfiguráció](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. A projekt területen navigáljon **a SajátPr** > **src** > **Main** > **Scala** > -**SajátPr**.  

6. A menüsávban navigáljon az **eszközök** > **Spark-konzol** > **Futtatás Spark helyi konzol (Scala)** elemre.

7. Ezután két párbeszédpanel jelenik meg, amely megkérdezi, hogy szeretné-e automatikusan kijavítani a függőségeket. Ha igen, válassza az **Automatikus javítás**lehetőséget.

    ![Spark automatikus Fix1](./media/apache-spark-intellij-tool-plugin/console-auto-fix1.png)

    ![Spark automatikus Fix2](./media/apache-spark-intellij-tool-plugin/console-auto-fix2.png)

8. A konzolnak az alábbi ábrához hasonlóan kell kinéznie. A konzol ablakában írja `sc.appName`be a következőt:, majd nyomja le a CTRL + ENTER billentyűkombinációt.  Ekkor megjelenik az eredmény. A helyi konzolt a vörös gombra kattintva állíthatja le.

    ![Helyi konzol eredménye](./media/apache-spark-intellij-tool-plugin/local-console-result.png)

### <a name="spark-livy-interactive-session-consolescala"></a>Spark Livy interaktív munkamenet-konzol (Scala)

Csak a 2018,2-es és a 2018,3-es IntelliJ esetén támogatott.

1. A menüsávban navigáljon > a**konfigurációk szerkesztése..** . lehetőségre.

2. A **Futtatás/hibakeresés konfigurációk** ablak bal oldali ablaktábláján navigáljon **Apache Spark a HDInsight** >  **[Spark on HDInsight] SajátPr**.

3. A főablakban válassza a **távoli Futtatás a fürtben** lapot.

4. Adja meg a következő értékeket, majd kattintson **az OK gombra**:

    |Tulajdonság |Value |
    |----|----|
    |Spark-fürtök (csak Linux)|Válassza ki azt a HDInsight Spark-fürtöt, amelyen futtatni szeretné az alkalmazást.|
    |Fő osztály neve|Az alapértelmezett érték a kijelölt fájl fő osztálya. Az osztályt a három pont ( **..** .) lehetőség kiválasztásával módosíthatja.  és válasszon másik osztályt.|

    ![Interaktív konzol beállított konfigurációja](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. A projekt területen navigáljon **a SajátPr** > **src** > **Main** > **Scala** > -**SajátPr**.  

6. A menüsávban navigáljon az **eszközök** > **Spark-konzolon** > a**Spark Livy interaktív munkamenet-konzolja (Scala)** elemre.

7. A konzolnak az alábbi ábrához hasonlóan kell kinéznie. A konzol ablakában írja `sc.appName`be a következőt:, majd nyomja le a CTRL + ENTER billentyűkombinációt.  Ekkor megjelenik az eredmény. A helyi konzolt a vörös gombra kattintva állíthatja le.

    ![Interaktív konzol eredménye](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Kijelölés küldése a Spark-konzolra

A szkript eredményét úgy teheti meg, hogy elküld egy kódot a helyi konzolon vagy a Livy interaktív munkamenet-konzolján (Scala). Kiemelheti a Scala-fájlban lévő egyes kódokat, majd kattintson a jobb gombbal a **kijelölés küldése a Spark-konzolra**lehetőségre. A rendszer elküldi a kiválasztott kódot a konzolra, és elvégzi. Az eredmény a konzolon a kód után fog megjelenni. Ha már van ilyen, a konzolon ellenőrizze a hibákat.  

   ![Kijelölés küldése a Spark-konzolra](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="reader-only-role"></a>Csak olvasó szerepkör

Ha a felhasználók a csak olvasási szerepkörrel rendelkező fürthöz küldenek feladatokat, a Ambari hitelesítő adatai szükségesek.

### <a name="link-cluster-from-context-menu"></a>Fürt csatolása a helyi menüből

1. Jelentkezzen be csak olvasó szerepkörrel rendelkező fiókkal.
       
2. Az **Azure Explorerben**bontsa ki a **HDInsight** elemet az előfizetésében található HDInsight-fürtök megtekintéséhez. A **"szerepkör: olvasó"** jelölésű fürtökön csak a csak olvasási jogosultsággal rendelkező szerepkör jogosult.

    ![az Explorer 15 megtekintése](./media/apache-spark-intellij-tool-plugin/view-explorer-15.png)

3. Kattintson a jobb gombbal a fürtre, csak a csak olvasó szerepkörre vonatkozó engedéllyel. Válassza a **fürt csatolása** a helyi menüből lehetőséget a fürt összekapcsolásához. Adja meg a Ambari felhasználónevét és jelszavát.

  
    ![Explorer 11 megtekintése](./media/apache-spark-intellij-tool-plugin/view-explorer-11.png)

4. Ha a fürt sikeresen csatolva van, a rendszer frissíti a HDInsight.
   A fürt szakasza csatolva lesz.
  
    ![Explorer 8 megtekintése](./media/apache-spark-intellij-tool-plugin/view-explorer-8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>Fürt csatolása a feladatok csomópont kibontásával

1. Kattintson a **feladatok** csomópontra, majd a **fürthöz való hozzáférés megtagadva** ablakra.
   
2. Kattintson **a fürt összekapcsolása** elemre a fürt csatolásához.
   
    ![Explorer 9 megtekintése](./media/apache-spark-intellij-tool-plugin/view-explorer-9.png)

### <a name="link-cluster-from-rundebug-configurations-window"></a>Fürt csatolása a futtatási/hibakeresési konfigurációk ablakból

1. Hozzon létre egy HDInsight-konfigurációt. Ezután válassza **a távoli Futtatás a fürtben**lehetőséget.
   
2. Válasszon ki egy olyan fürtöt, amely csak a csak a Linux rendszerre vonatkozó szerepkör-engedéllyel rendelkezik a **Spark-fürtökhöz**. Figyelmeztető üzenet jelenik meg. A fürt csatolásához kattintson **a fürt csatolása** lehetőségre.
   
   ![1\. konfiguráció létrehozása](./media/apache-spark-intellij-tool-plugin/create-config-1.png)
   
### <a name="view-storage-accounts"></a>Storage-fiókok megtekintése

* A csak olvasási szerepkörrel rendelkező fürtök esetében kattintson a **Storage-fiókok** csomópontra, majd a **tárterület-hozzáférés megtagadva** ablakra. A **Azure Storage Explorer** megnyitása lehetőségre kattintva megnyithatja Storage Explorer.
     
   ![Explorer 14 megtekintése](./media/apache-spark-intellij-tool-plugin/view-explorer-14.png)

   ![Explorer 10 megtekintése](./media/apache-spark-intellij-tool-plugin/view-explorer-10.png)

* A csatolt fürtök esetében kattintson a **Storage-fiókok** csomópontra, majd a tárterület- **hozzáférés megtagadva** ablak jelenik meg. Az **Azure Storage megnyitása** lehetőségre kattintva megnyithatja Storage Explorer.
     
   ![Explorer 13 megtekintése](./media/apache-spark-intellij-tool-plugin/view-explorer-13.png)

   ![Explorer 12 megtekintése](./media/apache-spark-intellij-tool-plugin/view-explorer-12.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Meglévő IntelliJ IDEA-alkalmazások konvertálása Azure Toolkit for IntelliJ használatára

A IntelliJ IDEA-ben létrehozott meglévő Spark Scala-alkalmazások a Azure Toolkit for IntelliJsal való kompatibilitás érdekében átalakíthatók. Ezután a beépülő modullal elküldheti az alkalmazásokat egy HDInsight Spark-fürtbe.

1. A IntelliJ IDEA használatával létrehozott meglévő Spark Scala-alkalmazáshoz nyissa meg a társított. IML fájlt.

2. A gyökérszintű szinten a következőhöz hasonló **modul** -elem:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Szerkessze a hozzáadandó `UniqueKey="HDInsightTool"` elemet úgy, hogy a **modul** eleme a következőképpen néz ki:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

3. Mentse a módosításokat. Az alkalmazásnak mostantól kompatibilisnek kell lennie Azure Toolkit for IntelliJsal. Teszteléshez kattintson a jobb gombbal a projekt nevére a projektben. Az előugró menüben már elérhető a **Spark-alkalmazás küldése a HDInsight**.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje a létrehozott fürtöt a következő lépésekkel:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. A felső **keresőmezőbe** írja be a **HDInsight**kifejezést.

1. Válassza ki a **HDInsight-fürtök** elemet a **szolgáltatások**területen.

1. A megjelenő HDInsight-fürtök listájában kattintson a **...** elemre az oktatóanyaghoz létrehozott fürt mellett.

1. Válassza a **Törlés** elemet. Válassza az **Igen**lehetőséget.

![HDInsight-fürt törlése](./media/apache-spark-intellij-tool-plugin/hdinsight-azure-portal-delete-cluster.png "HDInsight-fürt törlése")

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja a Azure Toolkit for IntelliJ beépülő modult a [Scala](https://www.scala-lang.org/)-ban írt Apache Spark alkalmazások fejlesztéséhez, majd beküldheti őket egy HDInsight Spark-fürtbe közvetlenül a IntelliJ integrált fejlesztési környezetből ( IDE). A következő cikkből megtudhatja, hogy a Apache Sparkban regisztrált információk Hogyan tölthetők be egy BI Analytics-eszközbe, például Power BIba.

> [!div class="nextstepaction"]
> [Adatok elemzése BI-eszközökkel](apache-spark-use-bi-tools.md)
