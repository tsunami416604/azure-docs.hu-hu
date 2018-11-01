---
title: 'IntelliJ-hez készült Azure-eszközkészlet: a HDInsight Spark-alkalmazások '
description: Ismerje meg, hogyan használja VPN-kapcsolaton keresztül HDInsight-fürtökön futó Spark-alkalmazások távoli hibakeresése az IntelliJ-hez HDInsight Tools.
services: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.openlocfilehash: 55f32b498b83203a3823999a2e31024266bf37bc
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50413264"
---
# <a name="use-azure-toolkit-for-intellij-to-debug-spark-applications-remotely-in-hdinsight-through-vpn"></a>A VPN-kapcsolaton keresztül a HDInsight Spark-alkalmazások hibakeresése az IntelliJ-hez készült Azure eszközkészlet használata

Azt javasoljuk, hogy hibakeresés spark-alkalmazások távoli ssh-n keresztül. Útmutatásért lásd: [távoli hibakeresés Spark-alkalmazások az Azure-eszközkészlet egy HDInsight-fürtön ssh-n keresztül az IntelliJ-hez](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).

Ebben a cikkben részletes útmutatást nyújt az IntelliJ-hez és a egy HDInsight Spark-fürtön a Spark-feladatok elküldése majd az asztali számítógép a távoli hibakeresés, a HDInsight Tools használata. A feladatok elvégzéséhez a következő magas szintű lépéseket hajtsa végre:

1. Hozzon létre egy helyek közötti és pont – hely az Azure virtual network. Ez a dokumentum lépései azt feltételezik, hogy egy helyek közötti hálózati.
1. Hozzon létre egy Spark-fürt a HDInsight, amely a helyek közötti virtuális hálózat része.
1. Ellenőrizze a kapcsolatot a fürt átjárócsomópontjával, és az asztal között.
1. A Scala-alkalmazások létrehozása az IntelliJ IDEA, és végezze el a távoli hibakeresés hajtható végre.
1. Futtassa, és az alkalmazás hibakereséséhez.

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**. További információkért lásd: [az Azure ingyenes próbaverzió beszerzése](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **A HDInsight Apache Spark-fürt**. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).
* **Oracle Java development Kitet**. A telepítheti a [Oracle webhely](https://aka.ms/azure-jdks).
* **Az IntelliJ IDEA**. Ez a cikk 2017.1 verzióját használja. A telepítheti a [eszközt webhely](https://www.jetbrains.com/idea/download/).
* **HDInsight-eszközök az IntelliJ-hez készült Azure-eszközkészlet**. IntelliJ-hez készült HDInsight eszközök érhetők el az IntelliJ-hez készült Azure-eszközkészlet részeként. Azure-eszközkészlet telepítésével kapcsolatos útmutatásért lásd: [IntelliJ-hez készült Azure eszközkészlet telepítése](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation).
* **Jelentkezzen be az Azure-előfizetéshez az IntelliJ IDEA**. Kövesse a [Spark-alkalmazások HDInsight-fürt létrehozása az IntelliJ-hez készült Azure eszközkészlet használata](apache-spark-intellij-tool-plugin.md).
* **Kivétel megoldás**. A Spark Scala-alkalmazás távoli hibakereséshez a Windows-számítógépen fut, miközben kaphat kivételt. Ez a kivétel ezen [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) és a egy hiányzó WinUtils.exe fájlt Windows miatt következik be. Ez a hiba elkerüléséhez kell [a végrehajtható fájl letöltése](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) egy olyan helyre, például **C:\WinUtils\bin**. Adjon hozzá egy **HADOOP_HOME** környezeti változót, majd állítsa be a változó értékét **C\WinUtils**.

## <a name="step-1-create-an-azure-virtual-network"></a>1. lépés: Egy Azure virtuális hálózat létrehozása
Kövesse az utasításokat az alábbi hivatkozásokat követve hozhat létre Azure-beli virtuális hálózathoz, és ellenőrizze a kapcsolatot az asztali számítógép és a virtuális hálózat között:

* [Virtuális hálózat létrehozása helyek közötti VPN-kapcsolattal az Azure portal használatával](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Virtuális hálózat létrehozása helyek közötti VPN-kapcsolat PowerShell-lel](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [PowerShell-lel virtuális hálózathoz pont – hely kapcsolat konfigurálása](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>2. lépés: A HDInsight Spark-fürt létrehozása
Javasoljuk, hogy is hozzon létre egy Apache Spark-fürt az Azure HDInsight, amely az Ön által létrehozott Azure virtuális hálózat része. A rendelkezésre álló információk [a HDInsight-fürtök létrehozása Linux-alapú](../hdinsight-hadoop-provision-linux-clusters.md). Választható konfiguráció részeként válassza ki az előző lépésben létrehozott Azure virtuális hálózatok.

## <a name="step-3-verify-the-connectivity-between-the-cluster-head-node-and-your-desktop"></a>3. lépés: A fürt átjárócsomópontjával, és az asztal közötti kapcsolat ellenőrzése
1. Az átjárócsomópont IP-címének lekéréséhez. Nyissa meg a fürt Ambari felhasználói felületén. Válassza ki a fürt paneljén **irányítópult**.

    ![Válassza ki az irányítópultot az Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-ambari-ui.png)
1. Az Ambari felhasználói felületén, válassza ki **gazdagépek**.

    ![Az Ambari gazdagépek kiválasztása](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-hosts.png)
1. Fő csomópontok, a munkavégző csomópontok és a zookeeper-csomópontok listáját láthatja. Az átjárócsomópontokhoz rendelkezik egy **hn*** előtag. Válassza ki az első fő csomópontot.

    ![A fő csomópont található az Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/cluster-headnodes.png)
1. Az a **összegzése** ablaktábla alján, a megnyíló oldalon, másolatot a **IP-cím** a fő csomópont és a **állomásnév**.

    ![Az Ambari az IP-cím keresése](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address.png)
1. Adja hozzá az IP-cím és a fő csomópont állomásnevét a **gazdagépek** fájlt a számítógépen kívánja futtatni, és távoli hibakeresés végrehajtása a Spark-feladat. Ez lehetővé teszi, hogy az IP-cím, valamint a hostname használatával kommunikálnak a fő csomópontot.

   a. Nyisson meg egy jegyzettömbfájlt emelt szintű engedélyekkel. Az a **fájl** menüjében válassza **nyílt**, majd keresse meg a hosts fájl helyét. A Windows-számítógépen a hely a **C:\Windows\System32\Drivers\etc\hosts**.

   b. Adja hozzá a következő információkat a **gazdagépek** fájlt:

           # For headnode0
           192.xxx.xx.xx hn0-nitinp
           192.xxx.xx.xx hn0-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net

           # For headnode1
           192.xxx.xx.xx hn1-nitinp
           192.xxx.xx.xx hn1-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
1. A számítógépről, amelyhez csatlakozik az Azure virtuális hálózat, amely a HDInsight-fürt által használt győződjön meg arról, hogy a fő csomópontok megpingelheti használatával, az IP-cím, valamint az állomásnevet.
1. SSH-val utasításait követve csatlakozhat az átjárócsomóponthoz [egy HDInsight-fürthöz SSH használatával csatlakozhat](../hdinsight-hadoop-linux-use-ssh-unix.md). A fürt fő csomópontjának pingelése az asztali számítógép IP-címét. A kapcsolat mindkét számítógéphez rendelt IP-címek tesztelése:

    - Egy, a hálózati kapcsolat
    - Egy, az Azure virtuális hálózat

1. Ismételje meg a lépéseket a fő csomópontot.

## <a name="step-4-create-a-spark-scala-application-by-using-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>4. lépés: A Spark Scala-alkalmazások létrehozása a HDInsight Tools használatával Azure-eszközkészlet az IntelliJ-hez, és konfigurálja a távoli hibakeresés
1. Nyissa meg az IntelliJ IDEA, és hozzon létre egy új projektet. A **New Project** (Új projekt) párbeszédablakban hajtsa végre az alábbi lépéseket:

    ![Az IntelliJ IDEA az új webesprojekt-sablon kiválasztása](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-hdi-scala-app.png)

    a. Válassza ki a **HDInsight** > **Spark on HDInsight (Scala)** lehetőséget.

    b. Kattintson a **Tovább** gombra.
1. A következő **új projekt** párbeszédpanelen tegye a következőket, és válassza **Befejezés**:

    - Adja meg a projekt nevét és helyét.

    - A **Project SDK** (Projekt SDK) legördülő listából Spark 2.x-fürt esetében válassza a **Java 1.8**, Spark 1.x-fürt esetében pedig a **Java 1.7** lehetőséget.

    - Az a **Spark-verzió** legördülő listából válassza ki, a Scala-projekt létrehozása varázsló együttműködik a megfelelő verziót a Spark SDK és a Scala SDK-t. Ha a Spark-fürt verziója 2.0-nál korábbi, válassza a **Spark 1.x** lehetőséget. Máskülönben válassza a **Spark2.x** lehetőséget. Ez a példa a következő verziót használja: **Spark 2.0.2 (Scala 2.11.8)**.
  
   ![A projekt SDK-t és a Spark-verziónak a kiválasztása](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)
  
1. A Spark-projekt automatikusan létrehoz egy összetevő. A lehívandó összetevő megtekintéséhez tegye a következőket:

    a. A **File** (Fájl) menüben válassza a **Project Structure** (Projektstruktúra) lehetőséget.

    b. Az a **Projektstruktúra** párbeszédpanelen jelölje ki **összetevők** létrehozott alapértelmezett összetevő megtekintéséhez. A plusz jelre kattintva is létrehozhat saját összetevő (**+**).

   ![JAR-fájl létrehozása](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/default-artifact.png)


1. Kódtárak hozzáadása a projekthez. A tár hozzáadásához tegye a következőket:

    a. Kattintson a jobb gombbal a projekt nevére a projekt fanézetben, és válassza **nyissa meg a modul a beállítások**. 

    b. Az a **Projektstruktúra** párbeszédpanelen jelölje ki **kódtárak**, jelölje be a (**+**) szimbólum, és válassza ki **a Maven** .

    ![Egy könyvtár hozzáadása](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/add-library.png)

    c. Az a **kódtár letöltése a Maven tárházból** párbeszédpanel, keresse meg és a következő kódtárakra hozzáadásához:

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`
1. Másolás `yarn-site.xml` és `core-site.xml` a fürtről csomópont átjárócsomópont és hozzáadása a projekthez. Az alábbi parancsokkal másolja a fájlokat. Használhat [Cygwin](https://cygwin.com/install.html) , futtassa a következő `scp` átjárócsomópontokhoz másolja a fájlokat a fürt-parancsokat:

        scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .

    Mivel már felvettük a fürt fő csomópontjának IP-címét és az asztalon a hosts fájl állomásnevek, használhatjuk a `scp` parancsok a következő módon:

        scp sshuser@hn0-nitinp:/etc/hadoop/conf/core-site.xml .
        scp sshuser@hn0-nitinp:/etc/hadoop/conf/yarn-site.xml .

    Ezek a fájlok hozzáadása a projekthez, másolja át azokat a **/src** mappát a projekt fán, például `<your project directory>\src`.
1. Frissítés a `core-site.xml` fájlt a következő módosításokat:

   a. Cserélje le a titkosított kulcsot. A `core-site.xml` fájl tartalmazza a titkosított kulcsot a fürthöz társított tárfiókba. Az a `core-site.xml` hozzáadott fájlra a projekthez, a titkosított kulcsot cserélje le az alapértelmezett tárfiók társított tényleges tárterület kulcsot. További információkért lásd: [a tárelérési kulcsok kezelése](../../storage/common/storage-account-manage.md#access-keys).

           <property>
                 <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
                 <value>access-key-associated-with-the-account</value>
           </property>
   b. Távolítsa el a következő bejegyzéseket a `core-site.xml`:

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
   c. Mentse a fájlt.
1. Adja hozzá az alkalmazás fő osztály. Az a **Project Explorer**, kattintson a jobb gombbal **src**, mutasson a **új**, majd válassza ki **Scala osztály**.

    ![Válassza ki a fő osztályban](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)
1. Az a **hozzon létre új Scala osztály** párbeszédpanel mezőben adjon meg egy nevet, válassza ki **objektum** a a **Kind** mezőbe, majd válassza ki **OK**.

    ![Hozzon létre új Scala-osztály](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)
1. Az a `MyClusterAppMain.scala` fájlt, az alábbi kódot. Ez a kód létrehozza a Spark környezet, és megnyílik egy `executeJob` módszerrel a `SparkSample` objektum.

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

1. Ismételje meg a 8. és 9 nevű új Scala objektum hozzáadása `*SparkSample`. Adja hozzá a következő kódot az osztály. Ez a kód olvassa be az adatokat a HVAC.csv (az összes HDInsight Spark-fürtök érhető el). Lekéri a sorokat, amelyek csak egy számot a CSV-fájl hetedik oszlopban, és ezután ír a kimenetet **/HVACOut** alatt az alapértelmezett tárolókat a fürt számára.

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
1. Ismételje meg a 8. és 9, vegyen fel egy új osztály nevű `RemoteClusterDebugging`. Ez az osztály megvalósítja a Spark-teszt keretrendszer, amellyel az alkalmazások hibakeresését. Adja hozzá a következő kódot a `RemoteClusterDebugging` osztály:

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

     Van néhány fontos tudnivaló, vegye figyelembe:

      * A `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`, ellenőrizze, hogy a Spark-szerelvény JAR érhető el a fürttároló, a megadott elérési úton.
      * A `setJars`, adja meg a helyet, ahol a lehívandó összetevő JAR létrejön. Jellemzően a `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar`.
1. Az a`*RemoteClusterDebugging` osztály, kattintson a jobb gombbal a `test` kulcsszót, és válassza ki **RemoteClusterDebugging konfiguráció létrehozása**.

    ![Hozzon létre egy távoli konfigurálása](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

1. Az a **RemoteClusterDebugging konfiguráció létrehozása** párbeszédpanelen adja meg a konfiguráció nevét, és válassza **típusú tesztelése** , a **tesztnév**. A többi értéket hagyja az alapértelmezett beállításokat. Válassza az **Apply** (Alkalmaz) lehetőséget, majd kattintson az **OK** gombra.

    ![A konfigurációs adatainak hozzáadása](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)
1. Meg kell jelennie egy **futtatása távoli** konfigurációs legördülő lista menüsávjában.

    ![A távoli futtatási legördülő lista](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/config-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>5. lépés: Az alkalmazás hibakeresési módban futtatja.
1. Az IntelliJ IDEA-projektben nyissa meg a `SparkSample.scala` , és hozzon létre egy töréspontot a `val rdd1`. Az a **létrehozása töréspontot** előugró menüből válassza **sort a függvény executeJob**.

    ![Töréspont hozzáadása](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-breakpoint.png)
1. Az alkalmazás futtatásához, válassza ki a **futtassa hibakeresés** megjelenítő gombra a **távoli Futtatás** konfigurációs legördülő listából.

    ![Válassza ki a hibakeresési Futtatás gombra](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode.png)
1. Amikor a program végrehajtását elérte a töréspontot, megjelenik egy **hibakereső** lap alsó ablaktábláján.

    ![A hibakereső lapon](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch.png)
1. Egy figyelési hozzáadásához válassza a (**+**) ikonra.

    ![Válassza ki a + ikon](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

    Ebben a példában az alkalmazás változó előtt érvénytelenítése `rdd1` lett létrehozva. A figyelés segítségével, láthatja az első öt sort a változóban `rdd`. Nyomja le az **Enter** billentyűt.

    ![A program hibakeresési módban futtatja](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

    Az előző képen látható, hogy futásidőben, lekérdezheti, ha lehet, hogy több terabájtnyi adat- és hibakeresési módját az alkalmazás különböző fázisokon halad. Például az előző képen látható a kimenetben láthatja, hogy az első sorát a kimenetet egy fejlécet. Alapján ez a kimenet, módosíthatja az alkalmazás kódjának a fejlécsor kihagyhatja, ha szükséges.
1. Most kiválaszthatja a **folytatása Program** folytatja az alkalmazás futtatása ikonra.

    ![Válassza ki a Program folytatása](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-run.png)
1. Ha az alkalmazás sikeres befejezését követően az alábbihoz hasonló kimenetnek kell megjelennie:

    ![Konzolkimenet](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete.png)

## <a name="seealso"></a>Következő lépések
* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="demo"></a>Bemutató
* Létrehozás Scala project (videó): [Spark Scala-alkalmazások létrehozása](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Távoli hibakeresés (videó): [távolról egy HDInsight-fürtön a Spark-alkalmazások hibakeresése az IntelliJ-hez készült Azure eszközkészlet használata](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Forgatókönyvek
* [Spark és BI: interaktív adatelemzés végrehajtása a Spark on HDInsight használatával, BI-eszközökkel](apache-spark-use-bi-tools.md)
* [Spark és Machine Learning: a Spark on HDInsight HVAC-adatok épület-hőmérséklet elemzésére a használata](apache-spark-ipython-notebook-machine-learning.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [A webhelynapló elemzése a Spark on HDInsight használatával](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](../hdinsight-apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Spark-fürtön a Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [IntelliJ-hez készült Azure eszközkészlet használata Spark-alkalmazások egy HDInsight-fürt létrehozása](apache-spark-intellij-tool-plugin.md)
* [Ssh-n keresztül távolról Spark-alkalmazások hibakeresése az IntelliJ-hez készült Azure eszközkészlet használata](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [HDInsight Tools for IntelliJ with hortonworks – tesztkörnyezet használata](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Az Eclipse-hez készült Azure-eszközkészlet HDInsight Tools használata Spark-alkalmazások létrehozásához](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Zeppelin notebookok használata a HDInsight Spark-fürt](apache-spark-zeppelin-notebook.md)
* [HDInsight notebookokhoz elérhető kernelek Jupyter egy Spark-fürtjében](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [A HDInsight az Apache Spark-fürt futó feladatok nyomon követése és hibakeresése](apache-spark-job-debugging.md)
