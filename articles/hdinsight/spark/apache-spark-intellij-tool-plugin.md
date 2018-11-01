---
title: 'IntelliJ-hez készült Azure-eszközkészlet: Spark-alkalmazások HDInsight-fürt létrehozása '
description: Spark Scalában írt alkalmazások fejlesztése az IntelliJ-hez készült Azure-eszközkészlet használatával, és küldheti el ezeket a HDInsight Spark-fürt.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: maxluk
ms.openlocfilehash: 51e1e7696ece46e63358b2ed6efa55bbf6ab01fd
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50420778"
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
- Oracle Java Development Kitet. A telepítheti a [Oracle webhely](https://aka.ms/azure-jdks).
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
Kapcsolat egy normál HDInsight-fürt Ambari felügyelt felhasználónév használatával. Hasonlóképpen, egy tartományhoz csatlakoztatott HDInsight-fürtöt, a kapcsolat a tartomány és felhasználónév, mint például a user1@contoso.com. Livy-Service-fürt is kapcsolhatja.

1. Válassza ki **egy fürtöt** a **Azure Explorer**.

   ![hivatkozás fürt helyi menü](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

2. Fürtök összekapcsolása két lehetősége van. 

   * Csatolás a HDInsight-fürt, válassza a **HDInsight-fürt** mezőben **fürt adatai**, adja meg **fürt nevét vagy URL-címe**, **felhasználónév**, és **Jelszó**.

      ![hivatkozás a hdinsight-fürt párbeszédpanel](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

   * Kapcsolásának Livy-Service-fürt, válassza a **Livy szolgáltatás** mezőben **fürt adatai**, adja meg **Livy végpont**, **fürtnév**. **Yarn-végpont** nem kötelező. A mezőben **hitelesítési**, két lehetősége van megadva. Ezek **alapszintű hitelesítés** és **nincs hitelesítés**. Ha bejelöli **alapszintű hitelesítés**, **felhasználónév** és **jelszó** meg kell adni. Ellenőrizni kell a felhasználónevet és jelszót, ha a hitelesítési hiba van.
      
      ![hivatkozás livy fürt párbeszédpanel](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)
   
3. Láthatja, hogy a társított fürt **HDInsight** csomópont, ha a bemeneti adatok megfelelő. Ehhez a fürthöz társított alkalmazás most már küldhet.

   ![a csatolt fürt](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

4. Fürt is megszüntetheti **Azure Explorer**.
   
   ![nem összekapcsolt fürt](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="create-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Egy HDInsight Spark-fürtön a Spark Scala-alkalmazások létrehozása

1. Indítsa el az IntelliJ IDEA-t, majd hozzon létre egy projektet. Az a **új projekt** párbeszédpanelen kövesse az alábbi lépéseket: 

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

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Futtathatja a Spark Scala alkalmazásokat egy HDInsight Spark-fürtön
Miután létrehozta a Scala-alkalmazások, elküldheti a fürthöz.

1. A Project Explorer keresse meg a Java vagy a Scala-fájlt, majd **Spark-alkalmazás elküldéséhez HDInsight** a helyi menü.
    
      ![Az HDInsight parancs elküldése Spark alkalmazás](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. Konfigurációs párbeszédpanelen adja meg a következő értékeket, majd kattintson a **SparkJobRun**.

      ![A Spark küldésének párbeszédpanel](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)
      
    * A **Spark-fürtök (csak Linux)**, válassza ki a HDInsight Spark-fürtöt, amelyen szeretné, az alkalmazás futtatásához.

    * Válasszon ki egy összetevőt az IntelliJ-projekt, vagy válasszon egyet a merevlemez-meghajtóról.

    * **Fő osztálynév** mező: az alapértelmezett értéke a választott fájl a fő osztályt. A három pontra kattintva módosíthatja az osztály (**...** ) és a egy másik osztály kiválasztása.   

    * **Feladat konfigurációk** mező: az alapértelmezett értékek vannak beállítva, a fenti képen látható. Módosítsa az értéket, vagy adja hozzá az új kulcs-érték a a feladatok elküldéséhez. További információ: [Apache Livy REST API](http://livy.incubator.apache.org./docs/latest/rest-api.html)

      ![A Spark küldésének párbeszédpanel box-konfigurációs jelentése](./media/apache-spark-intellij-tool-plugin/submit-job-configurations.png)

    * **Parancssori argumentumok** mező: a hely a fő osztály a felosztás, szükség esetén argumentumok értékeket adhat meg.

    * **JAR-fájlok kivételével hivatkozott** és **hivatkozott fájlok** mezők: adhat meg az elérési utak a hivatkozott JAR-fájlok kivételével, és a fájlok, ha van ilyen. További információ: [Spark-konfigurációja](https://spark.apache.org/docs/latest/configuration.html#runtime-environment) 

      ![A Spark küldésének párbeszédpanelen be jar-fájlok jelentése](./media/apache-spark-intellij-tool-plugin/jar-files-meaning.png)

       > [!NOTE]
       > A hivatkozott JARs és a hivatkozott fájlok feltöltését, tekintse meg: [erőforrások a fürt feltöltése](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer)
                         
    * **Töltse fel az elérési út**: adhatja meg, a Jar- vagy a Scala Projekt erőforrások elküldése tárolási helyét. Három tárolási típust támogat: **Azure Blob**, **interaktív használata Spark-munkamenetre összetevők feltöltése**, és **használja a fürt alapértelmezett tárfiók**. Az alábbi képernyőfelvétel, amelyek az Azure Blob.

        ![A Spark küldésének párbeszédpanel](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-upload-storage-types.png)

        ![A Spark küldésének párbeszédpanel](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-upload-storage-blob.png)

3. Kattintson a **SparkJobRun** a projekthez, hogy a kijelölt fürt elküldéséhez. A **fürt távoli Spark-feladat** lap alján a feladat végrehajtási folyamatának jeleníti meg. A piros gombra kattintva leállíthatja az alkalmazást. Feladat kimenetének elérésével kapcsolatban lásd: a "hozzáférés és a HDInsight Spark-fürtök kezelése az IntelliJ-hez készült Azure-eszközkészlet használatával" szakasz a cikk későbbi részében.
      
     ![A Spark küldésének ablak](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)


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

## <a name="spark-console"></a>Spark-konzol
Futtathat Spark helyi Console(Scala) vagy Spark Livy interaktív munkamenet Console(Scala) futtatni.

### <a name="spark-local-consolescala"></a>A Spark helyi Console(Scala)
1. A függőségek manuálisan kell hozzáadni. Menü **fájl**->**projekt struktúrák**->**Projektbeállítások**->**kódtárak**, Kattintson a **+**, válassza a **a Maven...** . Majd adja meg **org.apache.spark:spark-repl_2.11:2.1.0** az előugró párbeszédpanelen. A függőségek felvett szalagtárak esetén az első sorba kell áthelyezése a függőségi **modulok** a **projekt struktúrák** ablak. Mielőtt, módosítsa a **hatókör** való **futásidejű**.

    ![Helyi konzol függőségi kódtárak hozzáadása](./media/apache-spark-intellij-tool-plugin/local-console-add-dependency-libraries.png)

    ![Első sor a helyi konzol áthelyezése](./media/apache-spark-intellij-tool-plugin/local-console-move-first-line.png)

2. Ha nem rendelkezik ilyennel, mielőtt, állítsa be a konfigurációt. A **Futtatás és hibakeresés konfigurációk** ablakban kattintson a **+** -> **Azure HDInsight Spark**, jelölje be lapon **helyileg futtassa**, válassza ki a fő osztályban, majd kattintson a **OK**.

    ![Helyi konzol konfigurációjának beállítása](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)
 
3. Nyissa meg a fájlt a fő osztályban fájlba megfelelő, és kattintson a jobb gombbal **Spark konzol**, majd kattintson a **futtatása Spark helyi Console(Scala)**. Menü, vagy **eszközök**->**Spark konzol**->**futtatása Spark helyi Console(Scala)** , indítsa el a konzolt.

    ![A Spark helyi belépési pont](./media/apache-spark-intellij-tool-plugin/spark-console-local-entry-script.png)

4. Helyi indítását követően sikeresen konzol. Tűnik alatt. Hiba a kívánt teheti meg. Adja meg például **sc.appName**, nyomja le a ctrl + Enter, akkor az eredmény megjelenik.

    ![Helyi konzol eredménye](./media/apache-spark-intellij-tool-plugin/local-console-result.png)

### <a name="spark-livy-interactive-session-consolescala"></a>Spark-Livy interaktív munkamenet Console(Scala)
Az intellij-vel 2018.2 csak támogatott.

1. Ha nem rendelkezik ilyennel, mielőtt, állítsa be a konfigurációt. A **Futtatás és hibakeresés konfigurációk** ablakban kattintson a **+** -> **Azure HDInsight Spark**válassza lapon **távolról futtatni a fürt** , válassza ki a fürt neve és a fő osztályban, majd kattintson a **OK**.

    ![Interaktív konzol konfigurációs bejegyzés hozzáadása](./media/apache-spark-intellij-tool-plugin/interactive-console-add-config-entry.png)

    ![Interaktív konzol konfigurációjának beállítása](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

2. Nyissa meg a megfelelő a fő osztályban fájlba, és kattintson a jobb gombbal **Spark konzol**, majd kattintson a **futtatása Spark Livy interaktív munkamenet Console(Scala)**. Menü, vagy **eszközök**, majd kattintson a **Spark konzol**, majd **futtatása Spark Livy interaktív munkamenet Console(Scala)** , indítsa el a konzolt.

3. Sikerült elindítani a konzolt, miután valami azt szeretné, elvégezheti. Adja meg például **sc.appName**, nyomja le a ctrl + Enter, akkor az eredmény megjelenik.

    ![Interaktív konzol eredménye](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

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
