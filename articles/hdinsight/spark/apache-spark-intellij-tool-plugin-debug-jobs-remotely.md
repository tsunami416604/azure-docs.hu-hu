---
title: 'Azure Toolkit: Távolról debug Apache Spark-alkalmazások – Azure HDInsight'
description: Megtudhatja, hogy miként használhatja a HDInsight-eszközöket az Azure IntelliJ-eszközkészletében a HDInsight-fürtökön vpn-en keresztül futó Spark-alkalmazások távoli hibakereséséhez.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.openlocfilehash: 393356bd8604f6e7622acd778817681aad31f1f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76935027"
---
# <a name="use-azure-toolkit-for-intellij-to-debug-apache-spark-applications-remotely-in-hdinsight-through-vpn"></a>Az Azure Toolkit for IntelliJ segítségével távolról debugolhat apache Spark-alkalmazásokat a HDInsightban VPN-en keresztül

Javasoljuk, hogy távolról debugging [Apache Spark](https://spark.apache.org/) alkalmazások ssh-n keresztül. További információt az [Apache Spark-alkalmazások távoli hibakeresése egy HDInsight-fürtön az Azure Toolkit for IntelliJ sSH-n keresztül című témakörben talál.](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh)

Ez a cikk lépésenként útmutatást nyújt arról, hogyan használhatja az IntelliJ-hez készült Azure Toolkit HDInsight-eszközeit egy Spark-feladat elküldéséhez egy HDInsight Spark-fürtön, majd távolról az asztali számítógépről. A feladatok végrehajtásához a következő magas szintű lépéseket kell végrehajtania:

1. Hozzon létre egy helyek közötti vagy pont-to-site Azure virtuális hálózat. A dokumentum lépései feltételezik, hogy helyek közötti hálózatot használ.
1. Hozzon létre egy Spark-fürthdInsight, amely része a helyek közötti virtuális hálózat.
1. Ellenőrizze a fürtfőcsomópont és az asztal közötti kapcsolatot.
1. Hozzon létre egy Scala-alkalmazást az IntelliJ IDEA alkalmazásban, majd konfigurálja távoli hibakeresésre.
1. Futtassa és hibakeresés az alkalmazást.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. További információ: [Az Azure ingyenes próbaverziója.](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)
* **Apache Spark-fürt a HDInsightban.** További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).
* **Oracle Java fejlesztőkészlet**. Telepítheti az [Oracle honlapján](https://aka.ms/azure-jdks).
* **IntelliJ IDEA**. Ez a cikk a 2017.1-es verziót használja. Telepítheti azt a [JetBrains honlapján](https://www.jetbrains.com/idea/download/).
* **HDInsight-eszközök az Azure Toolkit for IntelliJ szolgáltatásban.** Az IntelliJ HDInsight-eszközei az Azure Toolkit for IntelliJ részeként érhetők el. Az Azure Toolkit telepítésével kapcsolatos tudnivalókért olvassa el [az Azure Toolkit for IntelliJ telepítését.](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation)
* **Jelentkezzen be Azure-előfizetésébe az IntelliJ IDEA szolgáltatásból.** Kövesse az IntelliJ Azure-eszközkészlet használata című útmutató utasításait, [ha Apache Spark-alkalmazásokat hozhat létre egy HDInsight-fürthöz.](apache-spark-intellij-tool-plugin.md)
* **Kivétel megoldás .** A Spark Scala alkalmazás távoli hibakeresés windowsos számítógépen történő futtatása közben kivételt kaphat. Ezt a kivételt a [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) ismerteti, és a Windows egyik hiányzó WinUtils.exe fájlja miatt következik be. A hiba kerülő megoldásához le kell töltenie a [Winutils.exe](https://github.com/steveloughran/winutils) webhelyre a **C:\WinUtils\bin**. Adjon hozzá egy **HADOOP_HOME** környezeti változót, majd állítsa a változó értékét **C\WinUtils értékre.**

## <a name="step-1-create-an-azure-virtual-network"></a>1. lépés: Azure virtuális hálózat létrehozása

Kövesse az alábbi hivatkozásokra vonatkozó utasításokat az Azure virtuális hálózat létrehozásához, majd ellenőrizze az asztali számítógép és a virtuális hálózat közötti kapcsolatot:

* [Virtuális hálózat létrehozása helyek közötti VPN-kapcsolattal az Azure Portalhasználatával](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Virtuális hálózat létrehozása helyek közötti VPN-kapcsolattal a PowerShell használatával](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Virtuális hálózattal létesített pont-hely kapcsolat konfigurálása a PowerShell használatával](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>2. lépés: HDInsight Spark-fürt létrehozása

Azt javasoljuk, hogy hozzon létre egy Apache Spark-fürtöt az Azure HDInsightban, amely a létrehozott Azure virtuális hálózat része. Használja a [HDInsight Linux-alapú fürtök létrehozása című csoportjában](../hdinsight-hadoop-provision-linux-clusters.md)elérhető információkat. A választható konfiguráció részeként válassza ki az előző lépésben létrehozott Azure virtuális hálózatot.

## <a name="step-3-verify-the-connectivity-between-the-cluster-head-node-and-your-desktop"></a>3. lépés: A fürtfőcsomópont és az asztal közötti kapcsolat ellenőrzése

1. A főcsomópont IP-címének beszerezése. Nyissa meg az Ambari felhasználói felületét a fürthöz. A fürtpanelen válassza az **Irányítópult**lehetőséget.

    ![Irányítópult kiválasztása az Apache Ambari ban](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-apache-ambari.png)

1. Az Ambari felhasználói felületén válassza a **Hosts**lehetőséget.

    ![Állomások kiválasztása az Apache Ambari ban](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/apache-ambari-hosts1.png)

1. A főcsomópontok, a feldolgozócsomópontok és az állattartó csomópontok listája látható. A főcsomópontok **hn*** előtaggal rendelkeznek. Válassza ki az első főcsomópontot.

    ![A főcsomópont megkeresése az Apache Ambari ban](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-cluster-headnodes.png)

1. A megnyíló lap alján található **Összegzés** ablaktáblából másolja a főcsomópont ÉS az **Állomásnév** **IP-címét.**

    ![Az IP-cím megkeresése az Apache Ambari ban](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address1.png)

1. Adja hozzá az IP-címet és a főcsomópont állomásnevét a **hosts** fájlhoz azon a számítógépen, amelyen futtatni szeretné, és távolról hibakeresés a Spark-feladat. Ez lehetővé teszi, hogy az IP-cím, valamint az állomásnév használatával kommunikáljon a főcsomóponttal.

   a. Nyisson meg egy emelt szintű engedélyekkel rendelkező Jegyzettömb-fájlt. A **Fájl menüben** válassza a **Megnyitás**parancsot, majd keresse meg a hosts fájl helyét. Windows rendszerű számítógépen a hely **a C:\Windows\System32\Drivers\etc\hosts**.

   b. Adja hozzá a következő adatokat a **hosts** fájlhoz:

    ```
    # For headnode0
    192.xxx.xx.xx nitinp
    192.xxx.xx.xx nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
    
    # For headnode1
    192.xxx.xx.xx nitinp
    192.xxx.xx.xx nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
    ```

1. A HDInsight-fürt által használt Azure virtuális hálózathoz csatlakoztatott számítógépről ellenőrizze, hogy pingelheti-e a főcsomópontokat az IP-cím, valamint az állomásnév használatával.

1. Az SSH segítségével csatlakozzon a fürt főcsomópontjához a [Csatlakozás HDInsight-fürthöz ssh használatával](../hdinsight-hadoop-linux-use-ssh-unix.md)című részben található utasításokat követve. A fürtfőcsomópontról pingelje az asztali számítógép IP-címét. Tesztelje a számítógéphez rendelt mindkét IP-cím kapcsolódását:

    - Az egyik a hálózati kapcsolat
    - Az egyik az Azure virtuális hálózat

1. Ismételje meg a lépéseket a másik főcsomóponton.

## <a name="step-4-create-an-apache-spark-scala-application-by-using-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>4. lépés: Hozzon létre egy Apache Spark Scala-alkalmazást a HDInsight-eszközök használatával az Azure Toolkit for IntelliJ alkalmazásban, és konfigurálja azt távoli hibakereséshez

1. Nyissa meg az IntelliJ IDEA-t, és hozzon létre egy új projektet. A **New Project** (Új projekt) párbeszédablakban hajtsa végre az alábbi lépéseket:

    ![Válassza ki az új projektsablont az IntelliJ IDEA-ban](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-hdi-scala-app.png)

    a. Válassza a **HDInsight** > **Spark lehetőséget a HDInsight (Scala) területen.**

    b. Válassza a **Tovább lehetőséget.**
1. A következő **Új projekt** párbeszédpanelen tegye a következőket, majd válassza a **Befejezés**gombot:

    - Adja meg a projekt nevét és helyét.

    - A **Project SDK** (Projekt SDK) legördülő listából Spark 2.x-fürt esetében válassza a **Java 1.8**, Spark 1.x-fürt esetében pedig a **Java 1.7** lehetőséget.

    - A **Spark verziólegördülő** listájában a Scala-projekt létrehozó varázsló integrálja a megfelelő verziót a Spark SDK és a Scala SDK. Ha a Spark-fürt verziója 2.0-nál korábbi, válassza a **Spark 1.x** lehetőséget. Máskülönben válassza a **Spark2.x** lehetőséget. Ez a példa a következő verziót használja: **Spark 2.0.2 (Scala 2.11.8)**.
  
   ![Válassza ki a projekt SDK és Spark-verzióját](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)
  
1. A Spark-projekt automatikusan létrehoz egy műtárgyat. Az műtermék megtekintéséhez tegye a következőket:

    a. A **File** (Fájl) menüben válassza a **Project Structure** (Projektstruktúra) lehetőséget.

    b. A **Projektszerkezet** **párbeszédpanelen** válassza az Eltérések lehetőséget a létrehozott alapértelmezett műtermék megtekintéséhez. Saját műtárgyat is létrehozhat a pluszjel (**+** kiválasztásával.

   ![IntelliJ IDEA leletek létre jar](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-default-artifact.png)

1. Könyvtárak hozzáadása a projekthez. Tár hozzáadásához tegye a következőket:

    a. Kattintson a jobb gombbal a projekt nevére a projektfában, majd válassza a **Modulbeállítások megnyitása parancsot.**

    b. A **Projektszerkezet** párbeszédpanelen **Libraries**válassza a Könyvtárak**+** lehetőséget, jelölje ki a ( ) szimbólumot, majd a **Maven-től**lehetőséget.

    ![IntelliJ IDEA letöltési könyvtár](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-add-library.png)

    c. A **Könyvtár letöltése a Maven-tárházból** párbeszédpanelen keresse meg és adja hozzá a következő könyvtárakat:

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`

1. Másolja `yarn-site.xml` `core-site.xml` és adja hozzá a fürtfőcsomópontot, és adja hozzá őket a projekthez. A fájlok másolásához használja az alábbi parancsokat. A [Cygwin](https://cygwin.com/install.html) segítségével a `scp` következő parancsokat futtathatja a fájlok fürtfőcsomópontokról történő másolásához:

    ```bash
    scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .
    ```

    Mivel már hozzáadtuk a fürtfőcsomópont IP-címét és állomásneveit az asztalon `scp` lévő hosts fájlhoz, a parancsokat a következő módon használhatjuk:

    ```bash
    scp sshuser@nitinp:/etc/hadoop/conf/core-site.xml .
    scp sshuser@nitinp:/etc/hadoop/conf/yarn-site.xml .
    ```

    Ha ezeket a fájlokat hozzá szeretné adni a projekthez, másolja `<your project directory>\src`őket a projektfa **/src** mappájába, például .

1. Frissítse `core-site.xml` a fájlt a következő módosításokhoz:

   a. Cserélje ki a titkosított kulcsot. A `core-site.xml` fájl tartalmazza a fürthöz társított tárfiók titkosított kulcsát. A `core-site.xml` projekthez hozzáadott fájlban cserélje le a titkosított kulcsot az alapértelmezett tárfiókhoz társított tényleges tárolókulcsra. További információt a [Tárfiók hozzáférési kulcsainak kezelése című témakörben talál.](../../storage/common/storage-account-keys-manage.md)

    ```xml
    <property>
            <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
            <value>access-key-associated-with-the-account</value>
    </property>
    ```

   b. Távolítsa el a `core-site.xml`következő bejegyzéseket:

    ```xml
    <property>
            <name>fs.azure.account.keyprovider.hdistoragecentral.blob.core.windows.net</name>
            <value>org.apache.hadoop.fs.azure.ShellDecryptionKeyProvider</value>
    </property>

    <property>
            <name>fs.azure.shellkeyprovider.script</name>
            <value>/usr/lib/python2.7/dist-packages/hdinsight_common/decrypt.sh</value>
    </property>

    <property>
            <name>net.topology.script.file.name</name>
            <value>/etc/hadoop/conf/topology_script.py</value>
    </property>
    ```

   c. Mentse a fájlt.

1. Adja hozzá az alkalmazás fő osztályát. A **Project Explorer**jobb gombbal kattintson **az src**pontjára, mutasson az **Új**pontra, majd válassza a **Scala osztály t.**

    ![IntelliJ IDEA Válassza ki a fő osztály](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)

1. Az **Új Scala-osztály létrehozása** párbeszédpanelen adjon meg egy nevet, válassza az **Objektum** elemet a **Szöveg** mezőben, majd kattintson az **OK**gombra.

    ![IntelliJ IDEA Új Scala osztály létrehozása](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)

1. A `MyClusterAppMain.scala` fájlba illessze be a következő kódot. Ez a kód létrehozza a `executeJob` Spark-környezetet, és megnyit egy metódust az `SparkSample` objektumból.

    ```scala
    import org.apache.spark.{SparkConf, SparkContext}

    object SparkSampleMain {
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("SparkSample")
                                    .set("spark.hadoop.validateOutputSpecs", "false")
        val sc = new SparkContext(conf)

        SparkSample.executeJob(sc,
                            "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
                            "wasb:///HVACOut")
        }
    }
    ```

1. Ismételje meg a `*SparkSample`8. Adja hozzá a következő kódot az osztályhoz. Ez a kód beolvassa az adatokat a HVAC.csv (elérhető az összes HDInsight Spark-fürtök). Lekéri azokat a sorokat, amelyek csak egy számjegyet tartalmaznak a CSV-fájl hetedik oszlopában, majd a kimenetet a **/HVACOut** könyvtárba írja a fürt alapértelmezett tárolója alatt.

    ```scala
    import org.apache.spark.SparkContext

    object SparkSample {
        def executeJob (sc: SparkContext, input: String, output: String): Unit = {
        val rdd = sc.textFile(input)

        //find the rows which have only one digit in the 7th column in the CSV
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        val s = sc.parallelize(rdd.take(5)).cartesian(rdd).count()
        println(s)

        rdd1.saveAsTextFile(output)
        //rdd1.collect().foreach(println)
         }
    }
    ```

1. Ismételje meg a `RemoteClusterDebugging`8. Ez az osztály megvalósítja a Spark-teszt keretrendszert, amely az alkalmazások hibakereséséhez használatos. Adja hozzá a `RemoteClusterDebugging` következő kódot az osztályhoz:

    ```scala
        import org.apache.spark.{SparkConf, SparkContext}
        import org.scalatest.FunSuite

        class RemoteClusterDebugging extends FunSuite {

         test("Remote run") {
           val conf = new SparkConf().setAppName("SparkSample")
                                     .setMaster("yarn-client")
                                     .set("spark.yarn.am.extraJavaOptions", "-Dhdp.version=2.4")
                                     .set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")
                                     .setJars(Seq("""C:\workspace\IdeaProjects\MyClusterApp\out\artifacts\MyClusterApp_DefaultArtifact\default_artifact.jar"""))
                                     .set("spark.hadoop.validateOutputSpecs", "false")
           val sc = new SparkContext(conf)

           SparkSample.executeJob(sc,
             "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
             "wasb:///HVACOut")
         }
        }
    ```

     Van néhány fontos dolog, hogy vegye figyelembe:

      * A `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`, győződjön meg arról, hogy a Spark-szerelvény JAR elérhető a fürttárolón a megadott elérési úton.
      * A `setJars`területen adja meg azt a helyet, ahol a JAR összetevő létrejön. Jellemzően, hogy `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar`.

1. Az`*RemoteClusterDebugging` osztályban kattintson `test` a jobb gombbal a kulcsszóra, majd válassza **a RemoteClusterDebugging konfiguráció létrehozása parancsot.**

    ![IntelliJ IDEA Hozzon létre egy távoli konfigurációt](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

1. A **RemoteClusterDebugging konfiguráció létrehozása** párbeszédpanelen adja meg a konfiguráció nevét, majd válassza a **Tesztnév** lehetőséget. **Test name** Hagyja az összes többi értéket alapértelmezett beállításként. Válassza az **Apply** (Alkalmaz) lehetőséget, majd kattintson az **OK** gombra.

    ![RemoteClusterDebugging-konfiguráció létrehozása](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)

1. Most egy **távoli futtatási** konfigurációs legördülő listát kell látnia a menüsorban.

    ![IntelliJ A távoli futtatás legördülő lista](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-config-remote-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>5. lépés: Az alkalmazás futtatása hibakeresési módban

1. Az IntelliJ IDEA projektben nyisson meg `SparkSample.scala` `val rdd1`és hozzon létre egy töréspontot a mellett. A **Töréspont létrehozása** előugró menüben válassza a **sort a függvény végrehajtóFeladat parancsában.**

    ![IntelliJ IDEA Töréspont hozzáadása](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-create-breakpoint.png)

1. Az alkalmazás futtatásához kattintson a **Debug Run (Hibakeresés futtatása)** gombra a **Távoli futtatás** konfigurációs legördülő lista mellett.

    ![IntelliJ IDEA Válassza ki a Debug Futtatás gombot](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode-button.png)

1. Amikor a program végrehajtása eléri a töréspontot, az alsó ablaktáblán megjelenik egy **Hibakereső** lap.

    ![IntelliJ IDEA A hibakereső lap megtekintése](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-debugger-tab.png)

1. Óra hozzáadásához kattintson**+** a ( ) ikonra.

    ![IntelliJ debug-add-watch-változó](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

    Ebben a példában az alkalmazás `rdd1` a változó létrehozása előtt megszakadt. Ezzel az órával láthatjuk a változó `rdd`első öt sorát. Nyomja le az **Enter** billentyűt.

    ![IntelliJ A program futtatása hibakeresési módban](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

    Az előző képen látható az, hogy futásidőben lekérdezheti a terabájtnyi adatot, és hibakeresést tehet az alkalmazás előrehaladásáról. Az előző képen látható kimenetben például látható, hogy a kimenet első sora fejléc. A kimenet alapján módosíthatja az alkalmazáskódot, hogy szükség esetén kihagyja a fejlécsort.

1. Most már kiválaszthatja a **Folytatási program** ikont az alkalmazás futtatásához.

    ![IntelliJ IDEA Válassza újrakezdési program](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-remote-run.png)

1. Ha az alkalmazás sikeresen befejeződik, a következőhez hasonló kimenetjelenik meg:

    ![IntelliJ IDEA hibakereső konzol kimenet](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete-window.png)

## <a name="next-steps"></a><a name="seealso"></a>További lépések

* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="demo"></a>Bemutató

* Scala-projekt létrehozása (videó): [Apache Spark Scala alkalmazások létrehozása](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Távoli hibakeresés (videó): [Az Azure Toolkit for IntelliJ használatával távolról debugot ihat az Apache Spark-alkalmazásokat egy HDInsight-fürtön](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Forgatókönyvek

* [Apache Spark bi-val: Interaktív adatelemzés végezhet a Spark használatával a HDInsightban az üzletiintelligencia-eszközökkel](apache-spark-use-bi-tools.md)
* [Apache Spark a Machine Learningsegítségével: A Spark használata a HDInsightban az épület hőmérsékletének elemzéséhez a fűtés- és légtechnikai adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learningsegítségével: A Spark használata a HDInsightban az élelmiszer-ellenőrzési eredmények előrejelzéséhez](apache-spark-machine-learning-mllib-ipython.md)
* [Webhelynapló-elemzés az Apache Spark használatával a HDInsightban](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása

* [Önálló alkalmazás létrehozása a Scala használatával](../hdinsight-apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények

* [Az Azure Toolkit for IntelliJ segítségével apache Spark-alkalmazásokat hozhat létre EGY HDInsight-fürthöz](apache-spark-intellij-tool-plugin.md)
* [Az Azure Toolkit for IntelliJ segítségével távolról debugelje az Apache Spark-alkalmazásokat az SSH-n keresztül](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Apache Spark-alkalmazások létrehozásához használja a HDInsight-eszközöket az Azure Eclipse eszközkészletében](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Apache Zeppelin-jegyzetfüzetek használata Apache Spark-fürttel a HDInsightban](apache-spark-zeppelin-notebook.md)
* [A Jupyter-jegyzetfüzethez elérhető kernelek egy Apache Spark-fürthdinsighthoz](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése

* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
