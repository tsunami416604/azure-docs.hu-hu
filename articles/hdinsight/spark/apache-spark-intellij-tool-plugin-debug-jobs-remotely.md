---
title: "Az IntelliJ Azure eszköztára: távolról a HDInsight Spark-alkalmazások hibakeresését |} Microsoft Docs"
description: "Megtudhatja, hogyan használhat HDInsight eszközöket az intellij-t Azure eszköztára távolról a VPN-en keresztül a HDInsight-fürtök a futó Spark-alkalmazások hibakeresését."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 55fb454f-c7dc-46de-a978-e242e9a94f4c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 4a87f1c6ba82edc0a762d9e02542a7756383ed82
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="use-azure-toolkit-for-intellij-to-debug-spark-applications-remotely-in-hdinsight-through-vpn"></a>Az intellij-t Azure eszközkészlet segítségével távolról a VPN-en keresztül a HDInsight Spark-alkalmazások

Azt javasoljuk, hogy hibakeresési SSH keresztül távolról spark-alkalmazások. Útmutatásért lásd: [távolról az IntelliJ SSH-n keresztül a Azure eszközkészlet a HDInsight-fürtök a Spark-alkalmazások hibakeresését](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).

Ez a cikk részletes útmutatást a HDInsight Tools használatával IntelliJ Azure eszköztára a Spark on HDInsight Spark-fürt a feladat elküldése, és ezután az asztali számítógépről távolról hibakeresési azt. E feladatok végrehajtásához a következő magas szintű lépéseket kell végrehajtani:

1. Pont-pont vagy a pont-pont Azure virtuális hálózat létrehozása. A jelen dokumentumban leírt lépések azt feltételezik, hogy a pont-pont hálózatot használ.
2. A pont-pont virtuális hálózat részét képező hdinsight Spark-fürt létrehozása.
3. Ellenőrizze a fürt átjárócsomópontjával, és az asztal közötti kapcsolatot.
4. Scala-alkalmazás létrehozása az IntelliJ IDEA, és adja meg azt a távoli hibakereséshez.
5. Futtassa, és az alkalmazás hibakeresése.

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**. További információkért lásd: [Azure ingyenes próbaverzió beszerzése](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Az Apache Spark-fürt hdinsightban**. Útmutatásért lásd: [létrehozása az Apache Spark on Azure hdinsight clusters](apache-spark-jupyter-spark-sql.md).
* **Oracle Java fejlesztői készlet**. Telepítheti azt a [Oracle webhely](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* **Az IntelliJ IDEA**. Ez a cikk 2017.1 verzióját használja. Telepítheti azt a [JetBrains webhely](https://www.jetbrains.com/idea/download/).
* **A HDInsight Tools az intellij-t Azure eszköztára**. A HDInsight tools for IntelliJ érhetők el az intellij-t Azure eszköztára részeként. Azure eszközkészlet telepítése, lásd: [Azure eszközkészlet telepítése az IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation).
* **Jelentkezzen be az Azure-előfizetéshez az IntelliJ IDEA**. Kövesse az utasításokat a [használata Azure eszköztára Spark-alkalmazások a HDInsight-fürtök létrehozása az IntelliJ](apache-spark-intellij-tool-plugin.md).
* **Kivétel megoldás**. A távoli hibakereséshez a Windows rendszerű számítógépeken a Spark Scala alkalmazás futtatásakor kaphat kivételt. Ez a kivétel esetén, tekintse meg a [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) és a Windows hiányzó WinUtils.exe fájl következik be. Ez a hiba megoldása érdekében kell [töltse le a végrehajtható fájl](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) egy olyan helyre, például **C:\WinUtils\bin**. Adja hozzá egy **HADOOP_HOME** környezeti változó, és utána állítsa be a változó értékének **C\WinUtils**.

## <a name="step-1-create-an-azure-virtual-network"></a>1. lépés: Az Azure virtuális hálózat létrehozása
Kövesse az alábbi hivatkozásokra kattintva hozzon létre egy Azure virtuális hálózatra utasításait, és ellenőrizze az asztali számítógép és a virtuális hálózat közötti kapcsolat:

* [Hozzon létre egy Vnetet az Azure portál használatával pont-pont VPN-kapcsolattal](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [VNet létrehozása a PowerShell használatával pont-pont VPN-kapcsolattal](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [PowerShell virtuális hálózat egy pont – hely kapcsolat beállítása](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>2. lépés: Egy HDInsight Spark-fürt létrehozása
Azt javasoljuk, hogy is fürtöt hoz létre az Apache Spark on Azure hdinsight létrehozott Azure virtuális hálózat részét képező. Olvassa el a rendelkezésre álló [hdinsight létrehozása Linux-alapú fürtökön](../hdinsight-hadoop-provision-linux-clusters.md). Választható konfiguráció részeként válassza ki az előző lépésben létrehozott Azure-beli virtuális hálózat.

## <a name="step-3-verify-the-connectivity-between-the-cluster-head-node-and-your-desktop"></a>3. lépés: Ellenőrizze a fürt átjárócsomópontjával, és az asztal közötti kapcsolat
1. Az IP-címét az átjárócsomópont beolvasása. Ambari felhasználói felületének megnyitásához a fürthöz. Válassza ki a fürt paneljén **irányítópult**.

    ![Válassza ki az irányítópultot az Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-ambari-ui.png)
2. Válassza ki az Ambari felhasználói felületén, **állomások**.

    ![Válasszon olyan gazdagépet, az Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-hosts.png)
3. Az átjárócsomópontokkal, a feldolgozó csomópontok és a zookeeper csomópontok listáját láthatja. Az átjárócsomópontokkal rendelkezik egy **hn*** előtag. Jelölje ki az első központi csomópontot.

    ![Az átjárócsomópont található Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/cluster-headnodes.png)
4. Az a **összegzés** ablaktábla a lap alján a megnyíló másolása a **IP-cím** a head csomópont és a **állomásnév**.

    ![Az IP-cím található Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address.png)
5. Az IP-cím és a központi csomópont állomásnevét adja hozzá a **állomások** fájlt azon a számítógépen, amelyre futtatásához, és távolról debug a Spark-feladatot. Ez lehetővé teszi, hogy az átjárócsomópont az IP-címet, valamint az állomásnév használatával kommunikálnak.

   a. Nyisson meg egy jegyzettömbfájlt emelt szintű engedélyekkel. Az a **fájl** menüjében válassza **nyitott**, majd keresse meg a hosts fájl helyét. A Windows-számítógépen a hely a következő **C:\Windows\System32\Drivers\etc\hosts**.

   b. Adja hozzá a következő információkat a **állomások** fájlt:

           # For headnode0
           192.xxx.xx.xx hn0-nitinp
           192.xxx.xx.xx hn0-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net

           # For headnode1
           192.xxx.xx.xx hn1-nitinp
           192.xxx.xx.xx hn1-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
6. A számítógépről, amelyhez csatlakozik az Azure virtuális hálózat, a HDInsight-fürt által használt győződjön meg arról, hogy az átjárócsomópontokkal pingelhető az IP-címet, valamint az állomásnév használatával.
7. Az SSH használata a csatlakozás az átjárócsomóponthoz utasításait követve [egy HDInsight-fürthöz SSH használatával csatlakozhat](../hdinsight-hadoop-linux-use-ssh-unix.md). A fürt átjárócsomópontjába, a ping az asztali számítógép IP-címét. A számítógéphez rendelt IP-címe. a kapcsolat teszteléséhez:

    - Egy, a hálózati kapcsolat
    - Egy, az Azure virtuális hálózat

8. A más központi csomópontján ismételje meg a lépést.

## <a name="step-4-create-a-spark-scala-application-by-using-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>4. lépés: A Spark Scala-alkalmazások létrehozása a HDInsight Tools használatával Azure eszközkészlet az intellij-t, és konfigurálja a távoli hibakeresés
1. Nyissa meg az IntelliJ IDEA, és hozzon létre egy új projektet. Az a **új projekt** párbeszédpanelen tegye a következőket:

    ![Válassza ki az új projekt sablont az IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-hdi-scala-app.png)

    a. Válassza ki **HDInsight** > **a Spark on HDInsight (Scala)**.

    b. Válassza ki **következő**.
2. A következő **új projekt** párbeszédpanelen tegye a következőket, majd válassza ki **Befejezés**:

    - Adja meg a projekt nevét és helyét.

    - Az a **projekt SDK** legördülő listában válassza **Java 1.8** a Spark-fürt 2.x, vagy válassza ki a **Java 1.7** a Spark 1.x fürthöz.

    - Az a **Spark verzió** legördülő listából válassza ki, a Scala-projekt létrehozása varázsló integrálja a megfelelő verzióját a Spark SDK és a Scala SDK. Ha a Spark-fürt verziója korábbi, mint 2,0, válassza ki a **Spark 1.x**. Máskülönben válassza **Spark2.x**. Ez a példa **Spark 2.0.2 (Scala 2.11.8)**.
  
   ![Válassza ki a projekt SDK-t és a Spark verzióját](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)
  
3. A Spark-projekt automatikusan létrehoz egy összetevő. Az összetevő megtekintéséhez tegye a következőket:

    a. Az a **fájl** menü **szerkezetének**.

    b. Az a **szerkezetének** párbeszédpanelen jelölje ki **összetevők** létrehozott alapértelmezett összetevő megtekintéséhez. A saját összetevő is létrehozhat; ehhez válassza a plusz jelre (**+**).

   ![Hozzon létre JAR](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/default-artifact.png)


4. Szalagtárak hozzáadása a projekthez. Vegyen fel egy gyűjteményt, tegye a következőket:

    a. Kattintson a jobb gombbal a projekt nevét a projekt csomópontjára, majd válassza ki **beállítások modul megnyitása**. 

    b. Az a **szerkezetének** párbeszédpanelen jelölje ki **szalagtárak**, jelölje be a (**+**) szimbólum, és válassza ki **a Maven** .

    ![A könyvtár hozzáadása](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/add-library.png)

    c. Az a **letöltése könyvtár Maven tárházból** párbeszédpanelen keresse meg, és adja hozzá a következő könyvtárak:

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`
5. Másolás `yarn-site.xml` és `core-site.xml` a fürtből head csomópont, és adja hozzá a projekthez. A következő parancsok segítségével másolja a fájlokat. Használhat [Cygwin](https://cygwin.com/install.html) futtatásához a következő `scp` parancsok a fájlok másolását a fürt átjárócsomópontokat:

        scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .

    A fürt átjárócsomópontjához IP-cím és a hosts fájl az asztalon állomásnevekkel már hozzáadott, mert is használhatók a `scp` parancsok a következő módon:

        scp sshuser@hn0-nitinp:/etc/hadoop/conf/core-site.xml .
        scp sshuser@hn0-nitinp:/etc/hadoop/conf/yarn-site.xml .

    Ezek a fájlok hozzáadása a projekthez, másolja őket a a **/src** mappa a projekt csomópontjára, például `<your project directory>\src`.
6. Frissítés a `core-site.xml` fájl a következő módosításokat:

   a. Cserélje le a titkosított kulcsot. A `core-site.xml` fájl tartalmazza a titkosított kulcsot a fürthöz tartozó tárfiókba. Az a `core-site.xml` korábban hozzáadott a projekthez, a titkosított kulcs cserélje le a tényleges kulcsot az alapértelmezett tárfiók társított. További információkért lásd: [a tárelérési kulcsok kezelése](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

           <property>
                 <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
                 <value>access-key-associated-with-the-account</value>
           </property>
   b. Távolítsa el a következő bejegyzéseket `core-site.xml`:

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
7. Adja hozzá az alkalmazás fő osztályban. Az a **Project Explorer**, kattintson a jobb gombbal **src**, mutasson a **új**, majd válassza ki **Scala osztály**.

    ![Válassza ki a fő osztály](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)
8. Az a **hozzon létre új Scala osztály** párbeszédpanel mezőben adjon meg egy nevet, válasszon **objektum** a a **jellegű** mezőbe, majd válassza ki **OK**.

    ![Hozzon létre új Scala osztály](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)
9. Az a `MyClusterAppMain.scala` fájlt, az alábbi kódot. Ezt a kódot hoz létre a Spark, a környezetben, és megnyílik egy `executeJob` metódust a `SparkSample` objektum.

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

10. Ismételje meg a 8. és 9 nevű új Scala objektum hozzáadása `*SparkSample`. Adja hozzá a következő kódot az osztály. Ez a kód a adatokat olvas a HVAC.csv (az összes HDInsight Spark-fürtjei érhető el). Lekéri a sor csak egy számot a CSV-fájlban szereplő hetedik oszlopban, és ezután ír a kimeneti **/HVACOut** alatt az alapértelmezett tároló, a fürt számára.

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
11. Ismételje meg a 8. és a 9-adjon hozzá egy új osztályt `RemoteClusterDebugging`. Ez az osztály megvalósítja az Spark keretrendszeréhez, amellyel az alkalmazások hibakeresését. Adja hozzá a következő kódot a `RemoteClusterDebugging` osztály:

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

     Van néhány fontos ügyeljen a következőkre:

      * A `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`, ellenőrizze, hogy a külső szerelvény JAR érhető el a fürttároló, a megadott elérési úton.
      * A `setJars`, adja meg a helyet, ahol összetevő JAR jön létre. Általában `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar`.
12. Az a`*RemoteClusterDebugging` osztály, kattintson a jobb gombbal a `test` kulcsszót, és válassza **létrehozása RemoteClusterDebugging konfigurációs**.

    ![A távoli konfiguráció létrehozása](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

13. Az a **létrehozása RemoteClusterDebugging konfigurációs** párbeszédpanelen adja meg a konfiguráció nevét, majd válassza ki **jellegű tesztelése** , a **teszt neve**. A többi érték hagyja meg az alapértelmezett beállításokat. Válassza ki **alkalmaz**, majd válassza ki **OK**.

    ![A konfigurációs részletek megadása](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)
14. Ekkor megjelenik egy **távoli Futtatás** konfigurációs legördülő listából válassza ki a menüsávon.

    ![A távoli futtatási legördülő lista](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/config-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>5. lépés: Futtassa az alkalmazást hibakeresési módban
1. Az IntelliJ IDEA projektben nyissa meg `SparkSample.scala` , és hozzon létre a töréspont `val rdd1`. Az a **töréspont létrehozása a** előugró menüből válassza **függvény executeJob sor**.

    ![Töréspont](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-breakpoint.png)
2. Futtassa az alkalmazást, válassza ki a **Debug futtassa** megjelenítő gombra a **távoli Futtatás** konfigurációs legördülő listát.

    ![Válassza ki a hibakeresési futtatása gombra](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode.png)
3. Ha a program végrehajtását eléri a töréspont, megjelenik egy **hibakereső** fülre az alsó ablaktáblában.

    ![A hibakereső lapon](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch.png)
4. A figyelési hozzáadásához válassza a (**+**) ikonra.

    ![Válassza ki a + ikonra](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

    Ebben a példában az alkalmazás túllépte a változó előtt `rdd1` lett létrehozva. A figyelés segítségével láthatja a változó első öt sora `rdd`. Válassza ki **meg**.

    ![A program futtatása a hibakeresési módban](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

    Az előző ábrán láthatók az, hogy futásidőben, előfordulhat, hogy kérdezze le az adatok és a hibakeresési terabájt hogyan az alkalmazások időtartamára. Például a kimenet az előző ábrán látható, láthatja, hogy a kimeneti első sora-e egy fejlécet. A kimeneti alapján, módosíthatja az alkalmazás kódjában a fejlécsor kihagyhatja, ha szükséges.
5. Mostantól kiválaszthatja a **folytatása Program** ikon gombra az alkalmazás futtatásához.

    ![Válassza ki a Program folytatása](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-run.png)
6. Ha az alkalmazás futtatása sikeresen befejeződött, a következőhöz hasonló kimenetnek kell megjelennie:

    ![Konzolkimenet](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete.png)

## <a name="seealso"></a>Következő lépések
* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="demo"></a>Bemutató
* Hozzon létre Scala project (videó): [Spark Scala-alkalmazások létrehozása](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Távoli hibakeresési (videó): [IntelliJ a Spark-alkalmazások távolról a HDInsight-fürtök használata Azure eszköztára](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Forgatókönyvek
* [Spark és BI: interaktív adatelemzés végrehajtása a Spark hdinsight BI-eszközökkel](apache-spark-use-bi-tools.md)
* [Spark és Machine Learning: Spark on HDInsight HVAC-adatok épület-hőmérséklet elemzésére használata](apache-spark-ipython-notebook-machine-learning.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [Spark Streaming: Spark on HDInsight használata valós idejű streamelési alkalmazásokat hozhatnak létre](apache-spark-eventhub-streaming.md)
* [A webhelynapló elemzése a Spark on HDInsight használatával](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](../hdinsight-apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Spark-fürtön a Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [Az IntelliJ Azure eszköztára használata Spark-alkalmazások a HDInsight-fürtök létrehozása](apache-spark-intellij-tool-plugin.md)
* [Az intellij-t Azure eszközkészlet segítségével SSH keresztül távolról Spark-alkalmazások](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Használja a HDInsight Tools for IntelliJ a Hortonworks védőfal](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Az Eclipse Azure eszközkészlet a HDInsight Tools használatával Spark-alkalmazások létrehozása](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Zeppelin notebookok használata Spark-fürt hdinsightban](apache-spark-zeppelin-notebook.md)
* [A HDInsight Spark-fürt Jupyter notebookokhoz elérhető kernelek](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Nyomon követése és hibakeresése, Apache Spark-fürt hdinsightban futó feladatok](apache-spark-job-debugging.md)
