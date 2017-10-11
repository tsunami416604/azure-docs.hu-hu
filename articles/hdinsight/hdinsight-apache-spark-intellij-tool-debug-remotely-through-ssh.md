---
title: "Az IntelliJ Azure eszköztára: Debug Spark alkalmazások távolról SSH |} Microsoft Docs"
description: "Részletes útmutatás Azure eszköztára IntelliJ HDInsight eszközök segítségével távolról a HDInsight-alkalmazások hibakeresését fürtök SSH-n keresztül"
keywords: "Debug távolról intellij, távoli hibakeresés intellij, ssh, az intellij hdinsight, a debug intellij, hibakeresés"
services: hdinsight
documentationcenter: 
author: jejiang
manager: DJ
editor: Jenny Jiang
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 08/24/2017
ms.author: Jenny Jiang
ms.openlocfilehash: 19053e31d6eb097bc91a04ef9c6af5772aaa16da
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="debug-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Az SSH-n keresztül az IntelliJ HDInsight-fürtök az Azure-eszközkészlet a Spark-alkalmazások

Ez a cikk nyújt részletes útmutatást Azure eszköztára IntelliJ HDInsight eszközök segítségével távolról a HDInsight-fürtök-alkalmazások hibakeresését. A projekt hibakeresési, megtekintheti a [az IntelliJ Debug HDInsight Spark Azure eszközkészlet alkalmazások](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) videó.

**Előfeltételek**

* **A HDInsight Tools az intellij-t Azure eszköztára**. Ez az eszköz az intellij-t Azure Toolkit részét képezi. További információkért lásd: [Azure eszközkészlet telepítése az IntelliJ](https://docs.microsoft.com/en-us/azure/azure-toolkit-for-intellij-installation).
* **Az IntelliJ Azure eszköztára**. Ez az eszközkészlet használata Spark-alkalmazások a HDInsight-fürtök létrehozása. További információkért kövesse az utasításokat a [használata Azure eszköztára Spark-alkalmazások a HDInsight-fürtök létrehozása az IntelliJ](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).
* **HDInsight SSH szolgáltatást a felhasználónév és jelszó felügyeleti**. További információkért lásd: [HDInsight (Hadoop) SSH használatával csatlakozhat](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) és [az SSH tunneling elérni az Ambari webes felhasználói felület, JobHistory, NameNode, Oozie és egyéb web UI](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 

## <a name="create-a-spark-scala-application-and-configure-it-for-remote-debugging"></a>Spark Scala-alkalmazás létrehozása, és konfigurálja a távoli hibakeresés

1. Indítsa el az IntelliJ IDEA, és ezután a projekt létrehozásához. Az a **új projekt** párbeszédpanelen tegye a következőket:

   a. Válassza ki **HDInsight**. 

   b. A Sablonválasztás Java vagy Scala a beállítások alapján. Az alábbi lehetőségek közül választhat:

      - **A Spark on HDInsight (Scala)**

      - **A Spark on HDInsight (Java)**

      - **A Spark on HDInsight-fürt futtatási minta (Scala)**

      Ez a példa egy **a Spark on HDInsight fürt futtatása minta (Scala)** sablont.

   c. Az a **Build eszköz** listára, válassza ki, az igényeknek megfelelően az alábbiak valamelyikét:

      - **Maven**, Scala-projekt létrehozása varázsló támogatásához

      -  **SBT**, a függőségek kezelésére, és a Scala-projekt létrehozása 

      ![Hibakeresési-projekt létrehozása](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-create-projectfor-debug-remotely.png)

   d. Válassza ki **következő**.     
 
3. A következő **új projekt** ablakban tegye a következőket:

   ![Válassza ki a Spark SDK](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-new-project.png)

   a. Adja meg a projekt nevét és a projekt helyére.

   b. Az a **projekt SDK** legördülő listában válassza **Java 1.8** a **Spark 2.x** fürt, vagy válasszon **Java 1.7** a **Spark 1.x**  fürt.

   c. Az a **Spark verzió** legördülő listából válassza ki, a Scala-projekt létrehozása varázsló Spark SDK és Scala SDK integrálja a megfelelő verziójával. Ha a spark-fürt verziója korábbi, mint 2,0, válassza ki a **Spark 1.x**. Máskülönben válassza **Spark 2.x.** Ez a példa **Spark 2.0.2 (Scala 2.11.8)**.

   d. Válassza a **Finish** (Befejezés) elemet.

4. Válassza ki **src** > **fő** > **scala** az ebben a projektben nyissa meg a kódot. Ez a példa a **SparkCore_wasbloTest** parancsfájl.

5. Hozzáférés a **szerkesztése konfigurációk** menüben válassza ki a ikonra a jobb felső sarokban. Ebből a menüből is készít vagy szerkeszt a konfigurációk a távoli hibakereséshez.

   ![Konfigurációk szerkesztése](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-edit-configurations.png) 

6. Az a **Futtatás/Debug konfigurációk** párbeszédpanelen jelölje ki a plusz jelre (**+**). Válassza ki a **Spark feladat elküldése** lehetőséget.

   ![Adja hozzá az új konfiguráció](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-add-new-Configuration.png)
7. Adja meg a információkat **neve**, **Spark-fürt**, és **fő osztálynév**. Válassza ki **speciális konfigurációs**. 

   ![Futtassa a hibakeresési konfigurációk](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-debug-configurations.png)

8. Az a **Spark küldésének speciális konfiguráció** párbeszédpanelen jelölje ki **engedélyezése Spark távoli hibakeresési**. Adja meg az SSH-felhasználónév, majd adjon meg egy jelszót vagy titkos kulcsfájlt. A konfiguráció mentéséhez, válassza ki a **OK**.

   ![Spark távoli hibakeresés engedélyezése](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-enable-spark-remote-debug.png)

9. A konfiguráció most menti a megadott névvel. A konfigurációs részletek megtekintéséhez válassza ki a konfiguráció nevét. A módosításokat, válassza ki a **szerkesztése konfigurációk**. 

10. A konfigurációs beállítások elvégzése után a projekt futtatni a távoli fürt, vagy hajtsa végre a távoli hibakeresés.

## <a name="learn-how-to-perform-remote-debugging"></a>Ismerje meg, hogyan hajthat végre a távoli hibakeresés
### <a name="scenario-1-perform-remote-run"></a>1. forgatókönyv: Hajtsa végre a távoli Futtatás

Ebben a szakaszban azt mutatja be az illesztőprogramok és végrehajtója hibakeresését.

    import org.apache.spark.{SparkConf, SparkContext}

    object LogQuery {
      val exampleApacheLogs = List(
        """10.10.10.10 - "FRED" [18/Jan/2013:17:56:07 +1100] "GET http://images.com/2013/Generic.jpg
          | HTTP/1.1" 304 315 "http://referall.com/" "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1;
          | GTB7.4; .NET CLR 2.0.50727; .NET CLR 3.0.04506.30; .NET CLR 3.0.04506.648; .NET CLR
          | 3.5.21022; .NET CLR 3.0.4506.2152; .NET CLR 1.0.3705; .NET CLR 1.1.4322; .NET CLR
          | 3.5.30729; Release=ARP)" "UD-1" - "image/jpeg" "whatever" 0.350 "-" - "" 265 923 934 ""
          | 62.24.11.25 images.com 1358492167 - Whatup""".stripMargin.lines.mkString,
        """10.10.10.10 - "FRED" [18/Jan/2013:18:02:37 +1100] "GET http://images.com/2013/Generic.jpg
          | HTTP/1.1" 304 306 "http:/referall.com" "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1;
          | GTB7.4; .NET CLR 2.0.50727; .NET CLR 3.0.04506.30; .NET CLR 3.0.04506.648; .NET CLR
          | 3.5.21022; .NET CLR 3.0.4506.2152; .NET CLR 1.0.3705; .NET CLR 1.1.4322; .NET CLR
          | 3.5.30729; Release=ARP)" "UD-1" - "image/jpeg" "whatever" 0.352 "-" - "" 256 977 988 ""
          | 0 73.23.2.15 images.com 1358492557 - Whatup""".stripMargin.lines.mkString
      )
      def main(args: Array[String]) {
        val sparkconf = new SparkConf().setAppName("Log Query")
        val sc = new SparkContext(sparkconf)
        val dataSet = sc.parallelize(exampleApacheLogs)
        // scalastyle:off
        val apacheLogRegex =
          """^([\d.]+) (\S+) (\S+) \[([\w\d:/]+\s[+\-]\d{4})\] "(.+?)" (\d{3}) ([\d\-]+) "([^"]+)" "([^"]+)".*""".r
        // scalastyle:on
        /** Tracks the total query count and number of aggregate bytes for a particular group. */
        class Stats(val count: Int, val numBytes: Int) extends Serializable {
          def merge(other: Stats): Stats = new Stats(count + other.count, numBytes + other.numBytes)
          override def toString: String = "bytes=%s\tn=%s".format(numBytes, count)
        }
        def extractKey(line: String): (String, String, String) = {
          apacheLogRegex.findFirstIn(line) match {
            case Some(apacheLogRegex(ip, _, user, dateTime, query, status, bytes, referer, ua)) =>
              if (user != "\"-\"") (ip, user, query)
              else (null, null, null)
            case _ => (null, null, null)
          }
        }
        def extractStats(line: String): Stats = {
          apacheLogRegex.findFirstIn(line) match {
            case Some(apacheLogRegex(ip, _, user, dateTime, query, status, bytes, referer, ua)) =>
              new Stats(1, bytes.toInt)
            case _ => new Stats(1, 0)
          }
        }
        
        dataSet.map(line => (extractKey(line), extractStats(line)))
          .reduceByKey((a, b) => a.merge(b))
          .collect().foreach{
          case (user, query) => println("%s\t%s".format(user, query))}

        sc.stop()
      }
    }


1. A legfrissebb pontok beállítása, majd válassza ki a **Debug** ikonra.

   ![Válassza ki a hibakeresési ikon](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-icon.png)

2. A program végrehajtását a legfrissebb pontot ér el, ha megjelenik egy **illesztőprogram** lapra, és két **végrehajtó** adhatja a **hibakereső** ablaktáblán. Válassza ki a **folytatása Program** továbbra is fut a kód, amely ezután a következő töréspont eléri, és elsősorban a megfelelő ikonra **végrehajtó** fülre. Tekintse át a végrehajtási naplók a megfelelő **konzol** fülre.

   ![Hibakeresés lap](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debugger-tab.png)

### <a name="scenario-2-perform-remote-debugging-and-bug-fixing"></a>2. forgatókönyv: Hajtsa végre a távoli hibakereséssel és a hiba elhárítása
Ez a szakasz azt mutatja be dinamikus frissítése a változó értéke egy egyszerű javítás az IntelliJ hibakeresési funkció használatával. Az alábbi példakódban kivételt vált ki, mert a célfájl már létezik.
  
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext

        object SparkCore_WasbIOTest {
          def main(arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("SparkCore_WasbIOTest")
            val sc = new SparkContext(conf)
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

            // Find the rows that have only one digit in the sixth column.
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)

            try {
              var target = "wasb:///HVACout2_testdebug1";
              rdd1.saveAsTextFile(target);
            } catch {
              case ex: Exception => {
                throw ex;
              }
            }
          }
        }


#### <a name="to-perform-remote-debugging-and-bug-fixing"></a>Távoli hibakereséssel és a hiba elhárítása
1. Két legfrissebb pontok beállítása, majd válassza ki a **Debug** ikonra a távoli hibakeresési megkezdéséhez.

2. A kód nem az első legfrissebb ponton, és a paraméter és a változó információk jelennek meg a **változók** ablaktáblán. 

3. Válassza ki a **folytatása Program** ikon a folytatáshoz. A kódot a második ponton leáll. A kivétel várt módon.

  ![A throw hiba](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-throw-error.png) 

4. Válassza ki a **folytatása Program** újra ikonra. A **HDInsight Spark küldésének** ablak egy "feladat futtatása nem sikerült" hibaüzenet jelenik meg.

  ![Hibaüzenet küldése](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-error-submission.png) 

5. Dinamikus frissítése a változó értéke az IntelliJ-hibakeresés funkció használatával, jelölje be **Debug** újra. A **változók** ablaktáblán jelenik meg újra. 

6. Kattintson a jobb gombbal a cél a **Debug** lapra, majd válassza ki **érték beállítása**. Ezután adja meg az új értéket a változóhoz. Válassza ki **Enter** mentheti az értéket. 

  ![Érték beállítása](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-set-value.png) 

7. Válassza ki a **folytatása Program** ikonra kattintva továbbra is futtassa a programot. Nincs kivétel ebben az esetben. Láthatja, hogy a projekt sikeresen kivételek nélkül futtatja.

  ![Kivétel nélkül hibakeresése](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Következő lépések
* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](hdinsight-apache-spark-overview.md)

### <a name="demo"></a>Bemutató
* Hozzon létre Scala project (videó): [Spark Scala-alkalmazások létrehozása](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Távoli hibakeresési (videó): [IntelliJ a Spark-alkalmazások távolról a HDInsight-fürtök használata Azure eszköztára](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Forgatókönyvek
* [Spark és BI: interaktív adatelemzés végrehajtása a Spark hdinsight BI-eszközökkel](hdinsight-apache-spark-use-bi-tools.md)
* [Spark és Machine Learning: Spark on HDInsight HVAC-adatok épület-hőmérséklet elemzésére használata](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark Streaming: Spark on HDInsight használata valós idejű streamelési alkalmazásokat hozhatnak létre](hdinsight-apache-spark-eventhub-streaming.md)
* [A webhelynapló elemzése a Spark on HDInsight használatával](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](hdinsight-apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Spark-fürtön a Livy használatával](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [Az IntelliJ Azure eszköztára használata Spark-alkalmazások a HDInsight-fürtök létrehozása](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Az intellij-t Azure eszközkészlet segítségével VPN-en keresztül távolról Spark-alkalmazások](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Használja a HDInsight Tools for IntelliJ a Hortonworks védőfal](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Az Eclipse Azure eszközkészlet a HDInsight Tools használatával Spark-alkalmazások létrehozása](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Zeppelin notebookok használata Spark-fürttel HDInsighton](hdinsight-apache-spark-zeppelin-notebook.md)
* [A HDInsight Spark-fürt Jupyter notebookokhoz elérhető kernelek](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](hdinsight-apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](hdinsight-apache-spark-job-debugging.md)
