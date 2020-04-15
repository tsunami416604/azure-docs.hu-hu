---
title: 'Azure Toolkit for IntelliJ: Spark alkalmazás – HDInsight'
description: Az Azure Toolkit for IntelliJ használatával scala nyelven írt Spark-alkalmazásokat fejleszthet, és elküldheti őket egy HDInsight Spark-fürtbe.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/13/2020
ms.openlocfilehash: a3884fdfbbc215c305053d8615d690880f4026ea
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314148"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-hdinsight-cluster"></a>Az Azure Toolkit for IntelliJ segítségével hozlétre Apache Spark-alkalmazásokat HDInsight-fürthöz

Ez a cikk bemutatja, hogyan fejleszthet Apache Spark-alkalmazásokat az Azure HDInsightban az Azure **Toolkit** beépülő modul használatával az IntelliJ IDE-hez. [Az Azure HDInsight](../hdinsight-overview.md) egy felügyelt, nyílt forráskódú elemzési szolgáltatás a felhőben. A szolgáltatás lehetővé teszi a nyílt forráskódú keretrendszerek, például a Hadoop, az Apache Spark, az Apache Hive és az Apache Kafka használatát.

Az Azure **Toolkit beépülő** modult többféleképpen is használhatja:

* Scala Spark-alkalmazást fejleszthet és küldhet el egy HDInsight Spark-fürtnek.
* Az Azure HDInsight Spark-fürt erőforrásainak elérése.
* Scala Spark-alkalmazást fejleszthet és futtathat helyileg.

Ebben a cikkben az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Az Azure Toolkit for IntelliJ beépülő modul használata
> * Apache Spark-alkalmazások fejlesztése
> * Alkalmazás küldése az Azure HDInsight-fürtbe

## <a name="prerequisites"></a>Előfeltételek

* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

* [Oracle Java Fejlesztési készlet](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).  Ez a cikk a Java 8.0.202-es verzióját használja.

* IntelliJ ÖTLET. Ez a cikk használ [IntelliJ IDEA Közösségi ver.  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure Toolkit for IntelliJ.  Lásd: [Az Azure Toolkit telepítése az IntelliJ-hez című témakört.](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable)

## <a name="install-scala-plugin-for-intellij-idea"></a>Scala beépülő modul az IntelliJ IDEA-hoz

A Scala bővítmény telepítésének lépései:

1. Nyissa meg az IntelliJ IDEA-t.

2. Az üdvözlőképernyőn válassza a Bővítmények **konfigurálása** > **Plugins** lehetőséget a Bővítmények ablak **megnyitásához.**

    ![IntelliJ IDEA lehetővé teszi scala plugin](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin1.png)

3. Válassza a **Telepítés** lehetőséget az új ablakban szereplő Scala bővítményhez.  

    ![IntelliJ IDEA telepíti scala plugin](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. A beépülő modul sikeres telepítését követően újra kell indítania az IDE-t.

## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>Spark Scala-alkalmazás létrehozása HDInsight Spark-fürthöz

1. Indítsa el az IntelliJ IDEA programot, és válassza az **Új projekt létrehozása lehetőséget** az Új **projekt** ablak megnyitásához.

2. Válassza ki az **Azure Spark/HDInsight** a bal oldali ablaktáblában.

3. Válassza ki a **Spark Project (Scala)** a főablakban.

4. A **Build eszköz** legördülő listájában válasszon az alábbi lehetőségek közül:
   * **Maven** a Scala projektkészítő varázsló támogatásához.
   * **SBT** a függőségek kezeléséhez és a Scala projekt létrehozásához.

     ![IntelliJ IDEA Új projekt párbeszédpanel](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. Kattintson a **Tovább** gombra.

6. Az **Új projekt** ablakban adja meg a következő információkat:  

    |  Tulajdonság   | Leírás   |  
    | ----- | ----- |  
    |Projektnév| Írjon be egy nevet.  Ez a `myApp`cikk a .|  
    |Projekt&nbsp;helye| Adja meg a projekt mentési helyét.|
    |Projekt SDK| Ez a mező üres lehet az IDEA első használatakor.  Válassza az **Új lehetőséget...** és keresse meg a JDK-t.|
    |Spark-verzió|A létrehozási varázsló integrálja a Spark SDK és a Scala SDK megfelelő verzióját. Ha a Spark-fürt verziója 2.0-nál korábbi, válassza a **Spark 1.x** lehetőséget. Máskülönben válassza a **Spark2.x** lehetőséget. Ez a példa **a Spark 2.3.0 (Scala 2.11.8) .**|

    ![Az Apache Spark SDK kiválasztása](./media/apache-spark-intellij-tool-plugin/intellij-new-project.png)

7. Válassza a **Finish** (Befejezés) elemet.  Eltarthat néhány percig, amíg a projekt elérhetővé válik.

8. A Spark-projekt automatikusan létrehoz egy műtárgyat. Az műtermék megtekintéséhez tegye a következő lépéseket:

   a. A menüsorból lépjen a > **Fájlprojekt-struktúra elemre...**. **File**

   b. A **Projektszerkezet ablakban** válassza az **Eltérések**lehetőséget.  

   c. Válassza **a Mégse** gombot az műtermék megtekintése után.

      ![Műtermék-adatok a párbeszédpanelen](./media/apache-spark-intellij-tool-plugin/default-artifact-dialog.png)

9. Adja hozzá az alkalmazás forráskódját az alábbi lépésekkel:

    a. A Projectből keresse meg a **myApp** > **src** > **fő** > **scala**.  

    b. Kattintson a jobb gombbal **a scala**elemre, majd keresse meg az **Új** > **scala osztályt.**

   ![Scala-osztály létrehozására szolgáló parancsok a Projectből](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   c. Az **Új Scala osztály létrehozása** párbeszédpanelen adjon meg egy nevet, válassza az **Objektum** elemet a **Természet beállítás** legördülő listában, majd kattintson az **OK gombra.**

     ![Új Scala osztály létrehozása párbeszédpanel](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. Ekkor megnyílik a **myApp.scala** fájl a főnézetben. Cserélje le az alapértelmezett kódot az alábbi kódra:  

    ```scala
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
    ```

    A kód beolvassa az adatokat HVAC.csv (elérhető az összes HDInsight Spark-fürtök), lekéri a sorokat, amelyek csak `/HVACOut` egy számjegy a hetedik oszlopban a CSV-fájlban, és írja a kimenetet az alapértelmezett tároló a fürt.

## <a name="connect-to-your-hdinsight-cluster"></a>Csatlakozás a HDInsight-fürthöz

A felhasználó [bejelentkezhet az Azure-előfizetésbe](#sign-in-to-your-azure-subscription), vagy [összekapcsolhat egy HDInsight-fürtöt.](#link-a-cluster) A HDInsight-fürthöz való csatlakozáshoz az Ambari felhasználónév/jelszó vagy a tartományhoz csatlakozó hitelesítő adatok használatával csatlakozzon.

### <a name="sign-in-to-your-azure-subscription"></a>Bejelentkezés Azure-előfizetésbe

1. A menüsorban keresse meg a**Windows** > **Azure Explorer**eszköz **megtekintése** > című eszközt.

   ![IntelliJ IDEA mutatja az azure explorer](./media/apache-spark-intellij-tool-plugin/show-azure-explorer1.png)

2. Az Azure Explorerben kattintson a jobb gombbal az Azure-csomópontra, majd válassza **a Bejelentkezés parancsot.** **Azure**

   ![IntelliJ IDEA explorer jobb gombbal az azure](./media/apache-spark-intellij-tool-plugin/explorer-rightclick-azure.png)

3. Az **Azure Bejelentkezés** párbeszédpanelén válassza az **Eszközbejelentkezés**lehetőséget, majd kattintson **a Bejelentkezés gombra.**

    !['IntelliJ IDEA azure bejelentkezési eszköz bejelentkezése'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer2.png)

4. Az **Azure-eszközbejelentkezés párbeszédpanelen** kattintson **a&másolása megnyitás gombra.**

   !["IntelliJ IDEA azure eszköz bejelentkezés"](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer5.png)

5. A böngészőfelületen illessze be a kódot, majd kattintson a **Tovább**gombra.

   !["A Microsoft írja be a HDI kódpárbeszédpanelét"](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer6.png)

6. Adja meg az Azure-hitelesítő adatait, majd zárja be a böngészőt.

   !["A Microsoft írja be a HDI e-mail párbeszédpaneljét"](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer7.png)

7. Miután bejelentkezett, az **Előfizetések kiválasztása** párbeszédpanel felsorolja a hitelesítő adatokhoz társított összes Azure-előfizetést. Válassza ki az előfizetést, majd a **Kijelölés** gombot.

    ![Az Előfizetések kiválasztása párbeszédpanel](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

8. Az **Azure Explorerből**bontsa ki a **HDInsight-alkalmazást** az előfizetésekben lévő HDInsight Spark-fürtök megtekintéséhez.

    ![IntelliJ IDEA Azure Explorer főnézete](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer3.png)

9. A fürthöz társított erőforrások (például tárfiókok) megtekintéséhez tovább bővítheti a fürtnév-csomópontot.

    ![Azure Explorer tárfiókok](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer4.png)

### <a name="link-a-cluster"></a>Fürt csatolása

A HDInsight-fürtöket az Apache Ambari által felügyelt felhasználónévvel kapcsolhatja össze. Hasonlóképpen, a tartományhoz csatlakozó HDInsight-fürt esetén a tartomány és a `user1@contoso.com`felhasználónév használatával is összekapcsolható, például . A Livy Service-fürthöz is csatolhatja.

1. A menüsorban keresse meg a**Windows** > **Azure Explorer**eszköz **megtekintése** > című eszközt.

1. Az Azure Explorerben kattintson a jobb gombbal a **HDInsight-csomópontra,** majd válassza a Fürt **csatolása parancsot.**

   ![Az Azure Explorer hivatkozási fürt helyi menüje](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

1. **A Fürt csatolása** ablakban rendelkezésre álló beállítások attól függően változnak, hogy melyik értéket választja ki az **Erőforrástípus csatolása** legördülő listából.  Írja be az értékeket, majd kattintson **az OK gombra.**

    * **HDInsight-fürt**  
  
        |Tulajdonság |Érték |
        |----|----|
        |Erőforrástípus csatolása|Válassza ki a **HDInsight-fürtöt** a legördülő listából.|
        |Fürt neve/URL-címe| Adja meg a fürt nevét.|
        |Hitelesítés típusa| Kilépés **alapfokú hitelesítésként**|
        |Felhasználónév| Adja meg a fürt felhasználónevét, az alapértelmezett rendszergazda.|
        |Jelszó| Adja meg a felhasználónév jelszavát.|

        ![Az IntelliJ IDEA fürtpárbeszéd et kapcsol össze](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Livy szolgáltatás**  
  
        |Tulajdonság |Érték |
        |----|----|
        |Erőforrástípus csatolása|Válassza a **Livy Szolgáltatás** elemet a legördülő listából.|
        |Livy végpont| Livy végpont beírása|
        |Fürt neve| Adja meg a fürt nevét.|
        |Fonalvégpont|Választható.|
        |Hitelesítés típusa| Kilépés **alapfokú hitelesítésként**|
        |Felhasználónév| Adja meg a fürt felhasználónevét, az alapértelmezett rendszergazda.|
        |Jelszó| Adja meg a felhasználónév jelszavát.|

        ![IntelliJ IDEA link Livy cluster párbeszédablak](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

1. A csatolt fürt a **HDInsight-csomópontról** látható.

   ![Az Azure Explorer hez csatolt fürt1](./media/apache-spark-intellij-tool-plugin/hdinsight-linked-cluster.png)

1. A fürtöt le is választhatja az **Azure Explorerből.**

   ![Az Azure Explorer nem csatolt fürtje](./media/apache-spark-intellij-tool-plugin/hdi-unlinked-cluster.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Spark Scala-alkalmazás futtatása HDInsight Spark-fürtön

A Scala-alkalmazás létrehozása után elküldheti azt a fürtnek.

1. A Projectből keresse meg a **myApp** > **src** > **fő** > **scala** > **myApp -t**.  Kattintson a jobb gombbal a **myApp**elemre, és válassza **a Spark-alkalmazás küldése parancsot** (valószínűleg a lista alján lesz).

      ![A Spark-alkalmazás küldése a HDInsightba parancs](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. A **Spark-alkalmazás küldése** párbeszédpanelen válassza az **1 lehetőséget. Spark a HDInsighton**.

3. A **Konfiguráció szerkesztése** ablakban adja meg a következő értékeket, majd kattintson az **OK gombra:**

    |Tulajdonság |Érték |
    |----|----|
    |Spark-fürtök (csak Linux esetén)|Válassza ki azt a HDInsight Spark-fürtöt, amelyen futtatni szeretné az alkalmazást.|
    |A beküldni kívánt műtermék kiválasztása|Hagyja meg az alapértelmezett beállítást.|
    |Fő osztály neve|Az alapértelmezett érték a kijelölt fájl fő osztálya. Meg lehet változtatni az osztály kiválasztásával a három pont(**...**)  és válasszon egy másik osztályt.|
    |Feladatkonfigurációk|Módosíthatja az alapértelmezett kulcsokat és vagy értékeket. További információ: [Apache Livy REST API.](https://livy.incubator.apache.org/docs/latest/rest-api.html)|
    |Parancssori argumentumok|Szükség esetén a főosztály térközével elválasztott argumentumokat is megadhat.|
    |Hivatkozott tégelyek és hivatkozott fájlok|Megadhatja a hivatkozott jarok és fájlok elérési útjait, ha vannak ilyenek. Az Azure virtuális fájlrendszerben is tallózhat fájlokközött, amelyek jelenleg csak az ADLS Gen 2 fürtöt támogatják. További információ: [Apache Spark-konfiguráció](https://spark.apache.org/docs/latest/configuration.html#runtime-environment).  Lásd még: [Erőforrások feltöltése a fürtbe.](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer)|
    |Feladat feltöltésének tárolása|Bontsa ki, hogy felfedje a további lehetőségeket.|
    |Tárhelytípusa|Válassza az Azure Blob használata a legördülő listából **való feltöltéshez** lehetőséget.|
    |Tárfiók|Adja meg tárfiókját.|
    |Tárolókulcs|Adja meg a tárolókulcsot.|
    |Tároló tároló|Válassza ki a tárolótárolót a legördülő listából, miután **a tárfiók** és **a tárolási kulcs** beírása után.|

    ![A Szikraküldés párbeszédpanel](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

4. Válassza a **SparkJobRun** lehetőséget a projekt nek a kijelölt fürtbe való elküldéséhez. A **Fürt távoli spark-feladata** a feladat végrehajtási folyamatát jeleníti meg alul. A piros gombra kattintva leállíthatja az alkalmazást.

    ![Az Apache Spark beküldéseablak](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Apache Spark-alkalmazások hibakeresése helyileg vagy távolról EGY HDInsight-fürtön

Azt is javasoljuk, hogy egy másik módja a Spark-alkalmazás a fürthöz. Ehhez a paraméterek et a **Run/Debug konfigurációk** IDE. Lásd: [Debug Apache Spark-alkalmazások helyileg vagy távolról egy HDInsight-fürtön az Azure Toolkit for IntelliJ ssh-on keresztül.](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>HDInsight Spark-fürtök elérése és kezelése az Azure IntelliJ eszközkészletével

Az Azure Toolkit for IntelliJ használatával különböző műveleteket tehet meg.  A legtöbb művelet az **Azure Explorerből**indul el.  A menüsorban keresse meg a**Windows** > **Azure Explorer**eszköz **megtekintése** > című eszközt.

### <a name="access-the-job-view"></a>A feladatnézet elérése

1. Az Azure Explorerben keresse meg a **HDInsight** > \<A fürt> > **feladatok**.

    ![IntelliJ Azure Explorer feladatnézet-csomópontja](./media/apache-spark-intellij-tool-plugin/intellij-job-view-node.png)

2. A jobb oldali ablaktáblában a **Spark feladatnézete** lapon a fürtön futtatott összes alkalmazás jelenik meg. Válassza ki annak az alkalmazásnak a nevét, amelynek további részleteit szeretné látni.

    ![A Spark-feladatnézet alkalmazásának részletei](./media/apache-spark-intellij-tool-plugin/intellij-view-job-logs.png)

3. Az alapvető futó feladatadatok megjelenítéséhez mutasson a feladatgrafikonra. A szakaszok grafikonjának és a minden feladat által létrehozott információk megtekintéséhez jelöljön ki egy csomópontot a feladatgrafikonon.

    ![A Spark-feladatmegtekintésefeladat fázisának részletei](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. A gyakran használt naplók, például *az Illesztőprogram stderr*, *az illesztőprogram stdout*és *a könyvtárinformáció*megtekintéséhez válassza a **Napló** lapot.

    ![Spark-feladat nézetnapló részletei](./media/apache-spark-intellij-tool-plugin/intellij-job-log-info.png)

5. Megtekintheti a Spark előzmények ui és a YARN uI (az alkalmazás szintjén). Jelöljön ki egy hivatkozást az ablak tetején.

### <a name="access-the-spark-history-server"></a>A Spark előzménykiszolgálójának elérése

1. Az Azure Explorerben bontsa ki a **HDInsight**csomópontot, kattintson a jobb gombbal a Spark-fürt nevére, majd válassza **a Spark-előzmények megnyitása felhasználói felületét.**  
2. Amikor a rendszer kéri, adja meg a fürt rendszergazdai hitelesítő adatait, amelyeket a fürt beállításakor adott meg.

3. A Spark előzménykiszolgáló irányítópultján az alkalmazás nevével megkeresheti az éppen most végzett futatot. Az előző kódban az alkalmazás nevét `val conf = new SparkConf().setAppName("myApp")`a használatával állíthatja be. A Spark alkalmazás neve **myApp**.

### <a name="start-the-ambari-portal"></a>Az Ambari portál indítása

1. Az Azure Explorerben bontsa ki a **HDInsight**csomópontot, kattintson a jobb gombbal a Spark-fürt nevére, majd válassza **a Fürtkezelési portál(Ambari) megnyitása parancsot.**  

2. Amikor a rendszer kéri, adja meg a fürt rendszergazdai hitelesítő adatait. Ezeket a hitelesítő adatokat a fürt beállítási folyamata során adta meg.

### <a name="manage-azure-subscriptions"></a>Azure-előfizetések kezelése

Alapértelmezés szerint az Azure Toolkit for IntelliJ felsorolja a Spark-fürtök az összes Azure-előfizetések. Szükség esetén megadhatja az elérni kívánt előfizetéseket.  

1. Az Azure Explorerben kattintson a jobb gombbal az **Azure** gyökércsomópontjára, és válassza **az Előfizetések kiválasztása parancsot.**  

2. Az **Előfizetések kiválasztása** ablakban törölje a jelet a nem elérni kívánt előfizetések melletti jelölőnégyzetből, majd válassza a **Bezárás**gombot.

## <a name="spark-console"></a>Spark-konzol

Futtathatja a Spark Local Console(Scala) vagy a Spark Livy Interactive Session Console(Scala) futtatását.

### <a name="spark-local-consolescala"></a>Spark helyi konzol (Scala)

Győződjön meg róla, hogy elégedett a WINUTILS.Ensure you've satisfied the WINUTILS. EXE előfeltétel.

1. A menüsorból keresse meg a **Konfigurációk szerkesztése** > **parancsot...**.

2. A **Futtatás/Hibakeresés ablakban** a bal oldali ablaktáblában keresse meg az **Apache Spark ot a HDInsight** > **[Spark on HDInsight] myApp alkalmazásban.**

3. A főablakban válassza **`Locally Run`** a lapot.

4. Adja meg a következő értékeket, majd kattintson **az OK gombra:**

    |Tulajdonság |Érték |
    |----|----|
    |Feladat fő osztálya|Az alapértelmezett érték a kijelölt fájl fő osztálya. Meg lehet változtatni az osztály kiválasztásával a három pont(**...**)  és válasszon egy másik osztályt.|
    |Környezeti változók|Győződjön meg arról, hogy a HADOOP_HOME értéke helyes.|
    |WINUTILS.exe hely|Ellenőrizze, hogy az elérési út helyes-e.|

    ![Helyi konzolkészlet konfigurációja](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. A Projectből keresse meg a **myApp** > **src** > **fő** > **scala** > **myApp -t**.  

6. A menüsorban keresse meg az **Eszközök** > **Spark Console** > **Spark-konzol futtatása Spark Helyi konzol (Scala)** című területet.

7. Ezután két párbeszéd ablak jelenhet meg, hogy megkérdezze, szeretné-e automatikusan kijavítani a függőségeket. Ha igen, válassza az **Automatikus javítás lehetőséget.**

    ![IntelliJ IDEA Spark Automatikus javítás párbeszédpanel1](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix1.png)

    ![IntelliJ IDEA Spark Automatikus javítás párbeszédpanel2](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix2.png)

8. A konzolnak az alábbi képhez hasonlóan kell kinéznie. A konzolablak `sc.appName`ban írja be a billentyűt, majd nyomja le a ctrl+Enter billentyűkombinációt.  Megjelenik az eredmény. A helyi konzolt a piros gombra kattintva fejezheti be.

    ![IntelliJ IDEA helyi konzol eredménye](./media/apache-spark-intellij-tool-plugin/local-console-result.png)

### <a name="spark-livy-interactive-session-consolescala"></a>Spark Livy interaktív munkamenet-konzol (Scala)

1. A menüsorból keresse meg a **Konfigurációk szerkesztése** > **parancsot...**.

2. A **Futtatás/Hibakeresés ablakban** a bal oldali ablaktáblában keresse meg az **Apache Spark ot a HDInsight** > **[Spark on HDInsight] myApp alkalmazásban.**

3. A főablakban válassza **`Remotely Run in Cluster`** a lapot.

4. Adja meg a következő értékeket, majd kattintson **az OK gombra:**

    |Tulajdonság |Érték |
    |----|----|
    |Spark-fürtök (csak Linux esetén)|Válassza ki azt a HDInsight Spark-fürtöt, amelyen futtatni szeretné az alkalmazást.|
    |Fő osztály neve|Az alapértelmezett érték a kijelölt fájl fő osztálya. Meg lehet változtatni az osztály kiválasztásával a három pont(**...**)  és válasszon egy másik osztályt.|

    ![Interaktív konzolkonfiguráció](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. A Projectből keresse meg a **myApp** > **src** > **fő** > **scala** > **myApp -t**.  

6. A menüsorban keresse meg az **Eszközök** > **Spark Console** > **Spark Livy interaktív munkamenetkonzoljának (Scala) futtatását.**

7. A konzolnak az alábbi képhez hasonlóan kell kinéznie. A konzolablak `sc.appName`ban írja be a billentyűt, majd nyomja le a ctrl+Enter billentyűkombinációt.  Megjelenik az eredmény. A helyi konzolt a piros gombra kattintva fejezheti be.

    ![IntelliJ IDEA interaktív konzol eredménye](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Kijelölés küldése a Spark-konzolra

Kényelmes, ha előre látja a parancsfájl eredményét úgy, hogy valamilyen kódot küld a helyi konzolra vagy a Livy Interactive Session Console(Scala) konzolra. Kiemelhet néhány kódot a Scala-fájlban, majd kattintson a jobb gombbal **a Kijelölés küldése a Spark-konzolra parancsra.** A kiválasztott kódot a rendszer elküldi a konzolnak. Az eredmény a kód után jelenik meg a konzolon. A konzol ellenőrzi a hibákat, ha léteznek.  

   ![Kijelölés küldése a Spark-konzolra](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>Integráció a HDInsight Identity Broker (HIB) alkalmazással

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>Csatlakozás hdinsight ESP-fürthöz az ID Broker (HIB) segítségével

A szokásos lépéseket követve jelentkezzen be az Azure-előfizetésbe, hogy csatlakozzon a HDInsight ESP-fürthöz az ID Broker (HIB) segítségével. A bejelentkezés után megjelenik a fürtlista az Azure Explorerben. További információt a [Csatlakozás a HDInsight-fürthöz (Csatlakozás a HDInsight-fürthöz) témakörben talál.](#connect-to-your-hdinsight-cluster)

### <a name="run-a-spark-scala-application-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Spark Scala-alkalmazás futtatása HDInsight ESP-fürtön id brokerrel (HIB)

A szokásos lépéseket követve elküldheti a feladatot a HDInsight ESP-fürtbe az ID Broker (HIB) segítségével. További útmutatásért tekintse meg [a Spark Scala-alkalmazás futtatása EGY HDInsight Spark-fürtön.](#run-a-spark-scala-application-on-an-hdinsight-spark-cluster)

Feltöltjük a szükséges fájlokat egy mappába, amelyet a bejelentkezési fiókjával neveztek el, és a konfigurációs fájlban láthatja a feltöltési útvonalat.

   ![feltöltési útvonal a konfigurációban](./media/apache-spark-intellij-tool-plugin/upload-path-in-the-configuration.png)

### <a name="spark-console-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Spark-konzol HDInsight ESP-fürtön ID Broker (HIB) segítségével

Futtathatja a Spark Local Console(Scala) vagy a Spark Livy Interactive Session Console(Scala) futtatását egy HDInsight ESP-fürtön az ID Broker (HIB) segítségével. További útmutatásért olvassa el a [Spark-konzolt.](#spark-console)

   > [!NOTE]  
   > A HDInsight ESP-fürt id broker (HIB) esetén [a fürt csatolása](#link-a-cluster) és az [Apache Spark-alkalmazások távoli hibakeresése](#debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster) jelenleg nem támogatott.

## <a name="reader-only-role"></a>Csak olvasói szerepkör

Amikor a felhasználók csak olvasói szerepkör-engedéllyel rendelkező fürtnek küldik el a feladatot, ambari hitelesítő adatokra van szükség.

### <a name="link-cluster-from-context-menu"></a>Fürt csatolása a helyi menüből

1. Jelentkezzen be csak olvasószerepkör-fiókkal.

2. Az **Azure Explorerből**bontsa ki a **HDInsight-alkalmazást** az előfizetésében lévő HDInsight-fürtök megtekintéséhez. A **"Role:Reader"** jelölésű fürtök csak olvasói szerepkör-engedéllyel rendelkeznek.

    !['IntelliJ Azure Explorer szerepkör:Olvasó'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer15.png)

3. Kattintson a jobb gombbal a csak olvasószerepkör-engedéllyel rendelkező fürtre. Válassza **a Fürt csatolása a** helyi menüből a fürt összekapcsolására lehetőséget. Adja meg az Ambari felhasználónevét és jelszavát.

    ![Az IntelliJ Azure Explorer összekapcsolja ezt a fürtöt](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer11.png)

4. Ha a fürt sikeresen kapcsolódik, a HDInsight frissül.
   A fürt szakasza összekapcsolódik.
  
    ![IntelliJ Azure Explorer csatolt párbeszédpanel](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>Fürt összekapcsolása a Feladatok csomópont kibontásával

1. Kattintson **a Feladatok** csomópont ra, és megjelenik **a fürtfeladat-hozzáférés megtagadva** ablaka.

2. Kattintson **a Fürt csatolása** elemre a fürt összekapcsolására.

    ![fürtfeladat-hozzáférés megtagadva párbeszédpanel](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer9.png)

### <a name="link-cluster-from-rundebug-configurations-window"></a>Fürt csatolása a Konfigurációk futtatása/törlése ablakból

1. Hozzon létre egy HDInsight-konfigurációt. Ezután válassza **a Távoli futtatás a fürtben**lehetőséget.

2. Válasszon ki egy fürtöt, amely csak olvasószerepkör-engedéllyel rendelkezik a **Spark-fürtökhöz (csak Linux)**. Megjelenik egy figyelmeztető üzenet. A **fürt csatolása** gombra kattintva összekapcsolhatja a fürtöt.

   ![IntelliJ IDEA futtatási/hibakeresési konfiguráció létrehozása](./media/apache-spark-intellij-tool-plugin/create-configuration.png)

### <a name="view-storage-accounts"></a>Tárfiókok megtekintése

* A csak olvasószerepkörrel rendelkező fürtök esetében kattintson **a Tárfiókok** csomópont elemre, és megjelenik **a Storage Access Denied** ablak. Az **Azure Storage Explorer megnyitása** elemre kattintva megnyithatja a Storage Explorert.

   !["IntelliJ IDEA Storage Access Megtagadva"](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer14.png)

   ![IntelliJ IDEA Storage Access Megtagadva gomb](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer10.png)

* Csatolt fürtök esetén kattintson **a Tárfiókok** csomópont ra, és megjelenik a **Storage Access Denied** ablak. Az **Azure Storage megnyitása** elemre kattintva megnyithatja a Storage Explorert.

   !["IntelliJ IDEA Storage Access Denied2"](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer13.png)

   ![IntelliJ IDEA Storage Access Denied2 gomb](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer12.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Meglévő IntelliJ IDEA alkalmazások átalakítása az Azure Toolkit for IntelliJ használatára

Az IntelliJ IDEA alkalmazásban létrehozott meglévő Spark Scala-alkalmazásokat konvertálhatja az Azure Toolkit for IntelliJ eszközkészlettel való kompatibilitásra. Ezután a beépülő modul segítségével elküldheti az alkalmazásokat egy HDInsight Spark-fürtbe.

1. Az IntelliJ IDEA segítségével létrehozott meglévő Spark Scala-alkalmazások esetén nyissa meg a társított `.iml` fájlt.

2. A gyökér szinten egy **modulelem,** mint a következő szöveg:

        ```
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">
        ```

   A hozzáadni `UniqueKey="HDInsightTool"` kívánt elem szerkesztése úgy, hogy a **modulelem** a következő szöveghez hasonlóan jelenjen meg:

        ```
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">
        ```

3. Mentse a módosításokat. Az alkalmazásnak most már kompatibilisnek kell lennie az Azure Toolkit for IntelliJ alkalmazáskészlettel. A tesztet úgy teheti meg, hogy a project nevére kattint a Project programban. A legördülő menümost már a **Spark-alkalmazás küldése a HDInsightba**lehetőséget kínál.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem fogja tovább használni ezt az alkalmazást, törölje a következő lépésekkel létrehozott fürtöt:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. A felső **keresőmezőbe** írja be a **HDInsight kifejezést.**

1. Válassza a **HDInsight-fürtök et a** **Szolgáltatások csoportban.**

1. A megjelenő HDInsight-fürtök listájában válassza ki a **cikkhez** létrehozott fürt melletti ... elemet.

1. Válassza a **Törlés** elemet. Válassza az **Igen** lehetőséget.

![Az Azure Portal törli a HDInsight-fürtöt](./media/apache-spark-intellij-tool-plugin/hdinsight-azure-portal-delete-cluster.png "HDInsight-fürt törlése")

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan használhatja az Azure Toolkit for IntelliJ beépülő modult a [Scala-ban](https://www.scala-lang.org/)írt Apache Spark-alkalmazások fejlesztéséhez. Ezután elküldte őket egy HDInsight Spark-fürthöz közvetlenül az IntelliJ integrált fejlesztői környezetből (IDE). A következő cikkben megtekintheti, hogy az Apache Sparkban regisztrált adatok hogyan behúzhatók egy BI-elemző eszközbe, például a Power BI-ba.

> [!div class="nextstepaction"]
> [Apache Spark-adatok elemzése a Power BI használatával](apache-spark-use-bi-tools.md)
