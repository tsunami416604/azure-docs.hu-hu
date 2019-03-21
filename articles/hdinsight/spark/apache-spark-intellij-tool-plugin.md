---
title: 'Azure-eszközkészlet az IntelliJ-hez: Spark-alkalmazások HDInsight-fürt létrehozása '
description: Spark Scalában írt alkalmazások fejlesztése az IntelliJ-hez készült Azure-eszközkészlet használatával, és küldheti el ezeket a HDInsight Spark-fürt.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: maxluk
ms.openlocfilehash: 8fa3cd79011ab31349ec44edf52b8fd9048d0d37
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58077972"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>IntelliJ-hez készült Azure eszközkészlet használata egy HDInsight-fürtön az Apache Spark-alkalmazások létrehozása

Az IntelliJ beépülő modul az Azure-eszközkészlet használata fejlesztéséhez [Apache Spark](https://spark.apache.org/) írt alkalmazások [Scala](https://www.scala-lang.org/), majd küldheti el ezeket a HDInsight Spark-fürt közvetlenül a az intellij-vel integrált fejlesztési környezet (IDE). Használhatja a beépülő modul több módon is:

* Fejlesztés, és küldje el a Scala Spark-alkalmazását egy HDInsight Spark-fürtön.
* Az Azure HDInsight Spark fürterőforrásokat eléréséhez.
* Fejlesztés és a Scala Spark-alkalmazás helyileg történő futtatása.

## <a name="prerequisites"></a>Előfeltételek

* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).
* [Oracle Java Development Kitet](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).  Ez az oktatóanyag Java 8.0.202 verziót használja.
* IntelliJ IDEA. Ez a cikk [IntelliJ IDEA közösségi ver.  2018.3.4](https://www.jetbrains.com/idea/download/).
* IntelliJ-hez készült Azure eszközkészlet.  Lásd: [IntelliJ-hez készült Azure-eszközkészlet telepítése](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).
* WINUTILS.EXE.  Lásd: [Hadoop futó Windows problémák](https://wiki.apache.org/hadoop/WindowsProblems).

## <a name="install-scala-plugin-for-intellij-idea"></a>Scala beépülő modul az IntelliJ IDEA-hoz
Hajtsa végre a Scala beépülő modul telepítéséhez az alábbi lépéseket:

1. Nyissa meg az IntelliJ IDEA-t.

2. Lépjen az üdvözlőképernyőn **konfigurálása** > **beépülő modulok** megnyitásához a **beépülő modulok** ablak.
   
    ![A Scala beépülő modul engedélyezése](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin.png)

3. Válassza ki **telepítése** Scala beépülő modul, amely megjelent az új ablakban.  

    ![A Scala beépülő modul telepítése](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. A beépülő modul sikeres telepítését követően újra kell indítania az IDE-t.


## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>Egy HDInsight Spark-fürt Spark Scala-alkalmazás létrehozása

1. Indítsa el az IntelliJ IDEA, és válassza ki **új projekt létrehozása** megnyitásához a **új projekt** ablak.

2. Válassza ki **Azure Spark és HDInsight** a bal oldali ablaktáblán.

3. Válassza ki **Spark Project (Scala)** a főablakból.

4. Az a **buildelőeszköze** legördülő listában válasszon a következők:
   * **Maven** Scala project-létrehozási varázsló támogatás.
   * **SBT** kezeléséhez a függőségeket, és a Scala-projekt létrehozásához.

     ![A New project (Új projekt) párbeszédablak.](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. Kattintson a **Tovább** gombra.

6. Az a **új projekt** ablakban adja meg a következő információkat:  

    |  Tulajdonság   | Leírás   |  
    | ----- | ----- |  
    |Projektnév| Adjon meg egy nevet.  Ebben az oktatóanyagban a következőt használjuk: `myApp`.|  
    |Projekt&nbsp;helye| Adja meg a kívánt helyre szeretné menteni a projektet.|
    |Project SDK| Ez lehet üres, az első használatkor hasznos.  Válassza ki **új...**  , és keresse meg a JDK.|
    |Spark-verzió|A létrehozása varázsló együttműködik a megfelelő verziójú Spark SDK és a Scala SDK-val. Ha a Spark-fürt verziója 2.0-nál korábbi, válassza a **Spark 1.x** lehetőséget. Máskülönben válassza a **Spark2.x** lehetőséget. Ez a példa **Spark 2.3.0-át (Scala 2.11.8)**.|

    ![A Spark SDK kiválasztása](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

7. Válassza a **Finish** (Befejezés) elemet.  Eltarthat néhány percig, mielőtt elérhetővé válik a projektet.

8. A Spark-projekt automatikusan létrehoz egy összetevő. A lehívandó összetevő megtekintéséhez tegye a következőket:

   a. Lépjen a menüsoron **fájl** > **Projektstruktúra...** .

   b. Az a **Projektstruktúra** ablakban válassza **összetevők**.  

   c. Válassza ki **Mégse** összetevő megtekintése után.

      ![A párbeszédpanel összetevő adatai](./media/apache-spark-intellij-tool-plugin/default-artifact.png)

9. Adja hozzá az alkalmazás forráskódjának az alábbiak szerint:

    a. A projekt, nyissa meg **myApp** > **src** > **fő** > **scala**.  

    b. Kattintson a jobb gombbal **scala**, és navigáljon arra **új** > **Scala osztály**.

   ![Parancsok a projektből Scala osztály létrehozása](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   c. Az a **hozzon létre új Scala osztály** párbeszédpanel mezőben adjon meg egy nevet, válassza ki **objektum** a a **Kind** legördülő listából válassza ki, és válassza ki **OK**.

     ![Hozzon létre új Scala osztály párbeszédpanel](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. A **myApp.scala** fájlt, majd megnyílik a fő nézetben. Cserélje le az alapértelmezett kód a alatt talált kódot:  

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

    A kód beolvassa az adatokat a HVAC.csv (az összes HDInsight Spark-fürtökön elérhető), a hetedik oszlopban a CSV-fájlban csak egy számot tartalmazó sorok beolvasása és ír a kimenetet `/HVACOut` alatt az alapértelmezett tárolókat a fürt számára.

## <a name="connect-to-your-hdinsight-cluster"></a>A HDInsight-fürthöz való csatlakozáshoz
Felhasználói lehetőségek közül választhat [jelentkezzen be Azure-előfizetés](#sign-in-to-your-azure-subscription), vagy [egy HDInsight-fürtöt](#link-a-cluster) az Ambari felhasználónév/jelszó és a tartományhoz csatlakoztatott hitelesítő adatok a HDInsight-fürthöz való csatlakozáshoz.

### <a name="sign-in-to-your-azure-subscription"></a>Jelentkezzen be az Azure-előfizetésébe

1. Lépjen a menüsoron **nézet** > **eszköz Windows** > **Azure Explorer**.
       
   ![Az Azure Explorer hivatkozás](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

2. Az Azure Explorerből, kattintson a jobb gombbal a **Azure** csomópontot, és válassza ki **bejelentkezés**.

3. Az a **Azure bejelentkezési** párbeszédpanelen jelölje ki **jelentkezzen be a**, majd adja meg Azure hitelesítő adatait.

    ![Az Azure bejelentkezési párbeszédpanel](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

4. Miután jelentkezett be, a **kiválasztása az előfizetések** párbeszédpanelen megjelenik az összes Azure-előfizetések tartoznak a hitelesítő adatokat. Válassza ki az előfizetését, és válassza ki a **kiválasztása** gombra.

    ![Az előfizetések kiválasztása párbeszédpanel](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

5. A **Azure Explorer**, bontsa ki a **HDInsight** megtekintéséhez a HDInsight Spark-fürtök, amelyek az előfizetésében.

    ![HDInsight Spark-fürtök Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

6. A fürthöz társított erőforrások (például storage-fiókok) megtekintéséhez, ennél jobban is kibonthatja a fürtnév csomópont.

    ![Egy kibontott fürtnév csomópontok](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

### <a name="link-a-cluster"></a>Egy fürtöt
Kapcsolat egy HDInsight-fürtöt a felügyelt Apache Ambari felhasználónév használatával. Hasonlóképpen, egy tartományhoz csatlakoztatott HDInsight-fürtöt, a kapcsolat a tartomány és felhasználónév, mint például a user1@contoso.com. Livy-Service-fürt is kapcsolhatja.

1. Lépjen a menüsoron **nézet** > **eszköz Windows** > **Azure Explorer**.

2. Az Azure Explorerből, kattintson a jobb gombbal a **HDInsight** csomópontot, és válassza ki **hivatkozásra A fürt**.

   ![hivatkozás fürt helyi menü](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

3. A rendelkezésre álló beállítások a **hivatkozásra A fürt** ablak eltérő lehet attól függően, melyik értéket választja, a a **hivatkozás erőforrástípus** legördülő listából válassza ki.  Adja meg az értékeket, majd **OK**.

    * **HDInsight-fürt**  
  
        |Tulajdonság |Érték |
        |----|----|
        |Hivatkozás erőforrás típusa|Válassza ki **HDInsight-fürt** a legördülő listából.|
        |Fürt neve vagy URL-címe| Adja meg a fürt nevét.|
        |Hitelesítés típusa| Hagyja meg **alapszintű hitelesítés**|
        |Felhasználónév| Adja meg a fürthöz tartozó felhasználónevet, alapértelmezett érték a rendszergazdával.|
        |Jelszó| Adja meg a jelszót a felhasználó nevét.|
    
        ![hivatkozás a hdinsight-fürt párbeszédpanel](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Livy-szolgáltatás**  
  
        |Tulajdonság |Érték |
        |----|----|
        |Hivatkozás erőforrás típusa|Válassza ki **Livy szolgáltatás** a legördülő listából.|
        |Livy-végpont| Adja meg a Livy-végpont|
        |Fürt neve| Adja meg a fürt nevét.|
        |Yarn-végpont|Választható.|
        |Hitelesítés típusa| Hagyja meg **alapszintű hitelesítés**|
        |Felhasználónév| Adja meg a fürthöz tartozó felhasználónevet, alapértelmezett érték a rendszergazdával.|
        |Jelszó| Adja meg a jelszót a felhasználó nevét.|

        ![hivatkozás livy fürt párbeszédpanel](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

4. Láthatja, hogy a társított fürtöt a **HDInsight** csomópont.

   ![a csatolt fürt](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

5. Fürt is megszüntetheti **Azure Explorer**.

   ![nem összekapcsolt fürt](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Futtathatja a Spark Scala alkalmazásokat egy HDInsight Spark-fürtön
Miután létrehozta a Scala-alkalmazások, elküldheti a fürthöz.

1. A projekt, nyissa meg **myApp** > **src** > **fő** > **scala**  >  **myApp**.  Kattintson a jobb gombbal **myApp**, és válassza ki **Spark-alkalmazás elküldéséhez** (Ez valószínűleg a lista alján található).
    
      ![Az HDInsight parancs elküldése Spark alkalmazás](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. Az a **Spark-alkalmazás elküldéséhez** párbeszédpanelen jelölje be **1. A Spark on HDInsight**.

3. Az a **Upravit konfiguraci** ablakban adja meg az alábbi értékeket, majd **OK**:

    |Tulajdonság |Érték |
    |----|----|
    |A Spark-fürtök (csak Linux)|Válassza ki a HDInsight Spark-fürtöt, amelyen szeretné, az alkalmazás futtatásához.|
    |Válassza ki a elküldeni egy összetevő|Hagyja meg az alapértelmezett beállítást.|
    |Main osztály neve|Az alapértelmezett értéke a választott fájl a fő osztályt. A három pontra kattintva módosíthatja az osztály (**...** ) és a egy másik osztály kiválasztása.|
    |Feladat-konfigurációk|Az alapértelmezett kulcsok és/vagy értékeit módosíthatja. További információkért lásd: [Apache Livy REST API](https://livy.incubator.apache.org./docs/latest/rest-api.html).|
    |Parancssori argumentumok|Szükség esetén a fő osztályban terület elválasztott argumentumok adhat meg.|
    |Hivatkozott JAR-fájlok kivételével, és a hivatkozott fájlok|Megadhatja az elérési utak a hivatkozott JAR-fájlok kivételével, és a fájlokat, ha van ilyen. További információk: [Az Apache Spark-konfiguráció](https://spark.apache.org/docs/latest/configuration.html#runtime-environment).  Lásd még a [hogyan tölthetők fel az erőforrások a fürt](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer).|
    |Feltöltés Feladattárolás|Bontsa ki a további beállítások megjelenítéséhez.|
    |Tárolás típusa|Válassza ki **az Azure Blob feltöltése** a legördülő listából.|
    |Tárfiók|Adja meg a storage-fiókjában.|
    |Tárkulcs|Adja meg a tárfiók-kulcsot.|
    |Tároló|Egyszer a legördülő listából válassza ki a storage-tároló **Tárfiók** és **Tárkulcs** tartozik.|

    ![A Spark küldésének párbeszédpanel](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

4. Válassza ki **SparkJobRun** a projekthez, hogy a kijelölt fürt elküldéséhez. A **fürt távoli Spark-feladat** lap alján a feladat végrehajtási folyamatának jeleníti meg. A piros gombra kattintva leállíthatja az alkalmazást. Feladat kimenetének elérésével kapcsolatban lásd: a "hozzáférés és a HDInsight Spark-fürtök kezelése az IntelliJ-hez készült Azure-eszközkészlet használatával" szakasz a cikk későbbi részében.  
      
    ![A Spark küldésének ablak](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Az Apache Spark-alkalmazások hibakeresését helyileg vagy távolról egy HDInsight-fürtön 
Javasoljuk továbbá egy másik módja a fürtön a Spark-alkalmazás be. Ehhez a paraméterek beállításával a **Futtatás és hibakeresés konfigurációk** IDE. További információkért lásd: [Apache Spark-alkalmazások hibakeresését helyileg vagy távolról egy HDInsight-fürtön az Azure-eszközkészlet ssh-n keresztül az IntelliJ-hez](apache-spark-intellij-tool-debug-remotely-through-ssh.md).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Hozzáférés és a HDInsight Spark-fürtök kezelése az IntelliJ-hez készült Azure-eszközkészlet használatával
IntelliJ-hez készült Azure-eszközkészlet használatával különféle műveleteket végezhet.  A műveletek a legtöbb kezdeményezése **Azure Explorer**.  Lépjen a menüsoron **nézet** > **eszköz Windows** > **Azure Explorer**.

### <a name="access-the-job-view"></a>Hozzáférés a feladatnézetben

1. Az Azure Explorerből, nyissa meg **HDInsight** > \<a fürt >> **feladatok**.

    ![Feladat megtekintése csomópont](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

2. A jobb oldali ablaktáblán a **Spark-feladat nézet** lap megjeleníti a fürtön futó összes alkalmazást. Válassza ki, amelynek meg szeretné tekinteni a további részleteket az alkalmazás nevét.

    ![Alkalmazás adatai](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)

3. Alapszintű futó feladat adatainak megjelenítéséhez vigye a kurzort a feladatgrafikon fölé. A szakaszok graph és minden feladat által létrehozott információk megtekintéséhez válasszon ki egy csomópontot, a feladat ábra a.

    ![Feladat fázis részletei](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Megtekintheti a gyakran használt naplókat, például *illesztőprogram Stderr*, *illesztőprogram Stdout*, és *Directory Info*, jelölje be a **Log** fülre.

    ![Napló részletei](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

5. A Spark-előzmények felhasználói felület és a YARN felhasználói felületén (az alkalmazás szintjén) egy hivatkozást az ablak tetején lévő kiválasztásával is megtekintheti.

### <a name="access-the-spark-history-server"></a>Hozzáférés a Spark-előzménykiszolgáló

1. A Azure Explorer eszközből bontsa ki a **HDInsight**, és kattintson a jobb gombbal a Spark-fürt nevét, majd válassza ki **nyissa meg a Spark feladatelőzmények felhasználói felület**.  
2. Amikor a rendszer kéri, adja meg a fürt rendszergazdai hitelesítő adataival, amelyet a fürt üzembe helyezésekor meg.

3. A Spark előzmények kiszolgáló irányítópultján használhatja az alkalmazás nevét és tekintse meg az alkalmazás csak futtatása befejeződött. A fenti kóddal, a használatával megadhatja az alkalmazás neve `val conf = new SparkConf().setAppName("myApp")`. A Spark-alkalmazás neve ezért **myApp**.

### <a name="start-the-ambari-portal"></a>Indítsa el az Ambari-portálon

1. A Azure Explorer eszközből bontsa ki a **HDInsight**, és kattintson a jobb gombbal a Spark-fürt nevét, majd válassza ki **nyissa meg a fürt felügyeleti Portal(Ambari)**.  

2. Amikor a rendszer kéri, adja meg a rendszergazdai hitelesítő adatok a fürt. Ezeket a hitelesítő adatokat adott meg a fürt beállítása során.

### <a name="manage-azure-subscriptions"></a>Azure-előfizetések kezelése
Alapértelmezés szerint az IntelliJ-hez a Spark-fürtök, az összes Azure-előfizetések sorolja fel. Ha szükséges, megadhatja az elérni kívánt előfizetéseket.  

1. Az Azure Explorerből, kattintson a jobb gombbal a **Azure** gyökércsomópont, és válassza ki **kiválasztása az előfizetések**.  

2. Az a **kiválasztása az előfizetések** ablakában törölje jelölőnégyzetéből az előfizetéseket, amikor nem szeretné elérni, és válassza ki **Bezárás**.

## <a name="spark-console"></a>Spark-konzol
Futtathat Spark helyi Console(Scala) vagy Spark Livy interaktív munkamenet Console(Scala) futtatni.

### <a name="spark-local-consolescala"></a>A Spark helyi Console(Scala)
Győződjön meg arról, hogy teljesült a WINUTILS. EXE-előfeltétel.

1. Lépjen a menüsoron **futtatása** > **konfigurációk szerkesztése...** .

2. Az a **Futtatás és hibakeresés konfigurációk** ablakban a bal oldali panelen lépjen **Apache Spark on HDInsight** > **[a Spark on HDInsight] myApp**.

3. A fő ablakban jelölje ki a **helyileg futtassa** fülre.

4. Adja meg az alábbi értékeket, és válassza ki **OK**:

    |Tulajdonság |Érték |
    |----|----|
    |Feladat main osztály|Az alapértelmezett értéke a választott fájl a fő osztályt. A három pontra kattintva módosíthatja az osztály (**...** ) és a egy másik osztály kiválasztása.|
    |Környezeti változók|Győződjön meg arról, HADOOP_HOME értéke helyes-e.|
    |WINUTILS.exe helye|Győződjön meg arról, az elérési út helyességét.|

    ![Helyi konzol konfigurációjának beállítása](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. A projekt, nyissa meg **myApp** > **src** > **fő** > **scala**  >  **myApp**.  

6. Lépjen a menüsoron **eszközök** > **Spark konzol** > **futtatása Spark helyi Console(Scala)**.

7. Majd két párbeszédpanelek jelenhet meg, kérje meg, ha azt szeretné, automatikus javítás függőségek. Ha igen, válassza ki a **automatikus javítás**.

    ![A Spark automatikus Fix1](./media/apache-spark-intellij-tool-plugin/console-auto-fix1.png)

    ![A Spark automatikus Fix2](./media/apache-spark-intellij-tool-plugin/console-auto-fix2.png)

8. A konzol az alábbi képhez hasonlóan kell kinéznie. Írja be a konzol ablakot `sc.appName`, és nyomja le a ctrl + Enter billentyűkombinációt.  Az eredmény jelenik meg. A helyi konzol piros gombra kattintva leállíthatja.

    ![Helyi konzol eredménye](./media/apache-spark-intellij-tool-plugin/local-console-result.png)


### <a name="spark-livy-interactive-session-consolescala"></a>Spark-Livy interaktív munkamenet Console(Scala)
Az intellij-vel 2018.2 és 2018.3 csak támogatott.

1. Lépjen a menüsoron **futtatása** > **konfigurációk szerkesztése...** .

2. Az a **Futtatás és hibakeresés konfigurációk** ablakban a bal oldali panelen lépjen **Apache Spark on HDInsight** > **[a Spark on HDInsight] myApp**.

3. A fő ablakban jelölje ki a **távolról futtatni a fürt** fülre.

4. Adja meg az alábbi értékeket, és válassza ki **OK**:

    |Tulajdonság |Érték |
    |----|----|
    |A Spark-fürtök (csak Linux)|Válassza ki a HDInsight Spark-fürtöt, amelyen szeretné, az alkalmazás futtatásához.|
    |Main osztály neve|Az alapértelmezett értéke a választott fájl a fő osztályt. A három pontra kattintva módosíthatja az osztály (**...** ) és a egy másik osztály kiválasztása.|

    ![Interaktív konzol konfigurációjának beállítása](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. A projekt, nyissa meg **myApp** > **src** > **fő** > **scala**  >  **myApp**.  

6. Lépjen a menüsoron **eszközök** > **Spark konzol** > **futtatása Spark Livy interaktív munkamenet Console(Scala)**.

7. A konzol az alábbi képhez hasonlóan kell kinéznie. Írja be a konzol ablakot `sc.appName`, és nyomja le a ctrl + Enter billentyűkombinációt.  Az eredmény jelenik meg. A helyi konzol piros gombra kattintva leállíthatja.

    ![Interaktív konzol eredménye](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Kijelölés küldeni a Spark-konzol

Célszerű, hogy a parancsfájl eredménye bizonyulhat, ha a helyi konzol vagy a Livy interaktív munkamenet Console(Scala) néhány kódot küld. Jelöljön ki egy kódrészletet a Scala-fájlban, majd kattintson a jobb gombbal **kijelölés küldése a konzol Spark való**. A kijelölt kód küld a konzolon, és hajtható végre. Az eredmény jelenik meg a konzolon kód után. A konzol ellenőrzi a hibákat, ha meglévő.  

   ![Kijelölés küldeni a Spark-konzol](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Átalakíthatja az IntelliJ-hez készült Azure-eszközkészlet használata meglévő az IntelliJ IDEA-alkalmazásokat

Átalakíthatja a meglévő Scala Spark alkalmazásokat az IntelliJ IDEA-kompatibilis, az IntelliJ-hez létrehozott. Ezután használhatja a beépülő modul az alkalmazásokat a HDInsight Spark-fürt elküldéséhez.

1. Egy meglévő Spark Scala-alkalmazáshoz, amely az IntelliJ IDEA használatával hozták létre nyissa meg a társított .iml fájlt.

1. A gyökérszintű szintje egy **modul** elemet a következőhöz hasonló:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Adja hozzá az elem szerkesztése `UniqueKey="HDInsightTool"` úgy, hogy a **modul** elemet a következőhöz hasonló:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

1. Mentse a módosításokat. Az alkalmazás már kompatibilis az IntelliJ-hez készült Azure-eszközkészlet kell lennie. Kattintson a jobb gombbal a projekt nevére a projekt tesztelheti. Az előugró menüben mostantól van lehetőség **Spark-alkalmazás elküldéséhez HDInsight**.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="error-in-local-run-use-a-larger-heap-size"></a>Hiba történt a helyi futtatáskor: *Használjon egy nagyobb. generace*
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
Ha a fürt foglalt, kaphat az alábbi hiba.

![Intellij jelenik meg a hiba esetén a foglalt fürt](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-upload.png)

![Intellij jelenik meg a hiba esetén a foglalt fürt](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-submit.png)

## <a name="feedback-and-known-issues"></a>Visszajelzés és ismert problémák
Közvetlenül a Spark kimenetek megtekintése jelenleg nem támogatott.

Ha javaslata vagy visszajelzés rendelkezik, vagy ha problémákat tapasztal, amikor ez a beépülő modul használata, e-mailt küldjön hdivstool@microsoft.com.

## <a name="seealso"></a>Következő lépések
* [Áttekintés: Az Apache Spark on Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Bemutató
* (Videó) Scala-projekt létrehozása: [Az Apache Spark Scala-alkalmazások létrehozása](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Távoli hibakeresés (videó): [IntelliJ-hez készült Azure eszközkészlet használata Apache Spark-alkalmazások távolról a HDInsight-fürtön](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Forgatókönyvek
* [Az Apache Spark és BI: Interaktív adatelemzés végrehajtása a Spark on HDInsight használatával, BI-eszközökkel](apache-spark-use-bi-tools.md)
* [Az Apache Spark és Machine Learning: A Spark használata a HDInsight-hőmérséklet building HVAC-adatok elemzése](apache-spark-ipython-notebook-machine-learning.md)
* [Az Apache Spark és Machine Learning: A HDInsight Spark használata az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [A webhelynapló elemzése a HDInsight az Apache Spark használatával](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Létrehozása és alkalmazások futtatásához
* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [IntelliJ-hez készült Azure eszközkészlet használata Apache Spark-alkalmazások VPN-en keresztül távolról](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [IntelliJ-hez készült Azure eszközkészlet használata Apache Spark-alkalmazások távolról az ssh-n keresztül](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [HDInsight Tools for IntelliJ with hortonworks – tesztkörnyezet használata](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [HDInsight Tools használata az Azure Toolkit for Eclipse Apache Spark-alkalmazások létrehozásához](apache-spark-eclipse-tool-plugin.md)
* [Az Apache Zeppelin notebookok használata a HDInsight Apache Spark-fürt](apache-spark-zeppelin-notebook.md)
* [Notebookokhoz elérhető kernelek Jupyter a HDInsight az Apache Spark-fürt](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
