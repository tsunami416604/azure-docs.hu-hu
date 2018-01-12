---
title: "Apache Spark streamelési az Event Hubs az Azure HDInsight használata |} Microsoft Docs"
description: "Build Apache Spark streaming minta adatfolyam küldeni az Azure Event Hubs és a HDInsight Spark-fürt scala-alkalmazások segítségével a ezeket az eseményeket fogadni."
keywords: "Apache spark streaming, spark streamelési, spark minta, apache spark streamelési, például az esemény hub azure-minta, spark-minta"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 68894e75-3ffa-47bd-8982-96cdad38b7d0
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: jgao
ms.openlocfilehash: 43ae956ca284485cc68f8120a31af1c493c0b254
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2018
---
# <a name="apache-spark-streaming-process-data-from-azure-event-hubs-with-spark-cluster-on-hdinsight"></a>Apache Spark streaming: a HDInsight fürt adatfeldolgozásra Spark az Azure Event hubs

Ebben a cikkben hozzon létre egy Apache Spark streaming mintát, amely a következő lépésekből áll:

1. Egy önálló alkalmazás segítségével üzenetek betöltési Azure eseményközpontba.

2. A két különböző szempontok, akkor a üzeneteket beolvasni az Event Hubs a valós idejű Azure HDInsight Spark-fürtön futó alkalmazást használ.

3. Build a streaming adatok különböző tárolórendszerek megőrzéséhez elemzőfolyamatokat, vagy mélyebb betekintés az adatok menet közben.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* A HDInsight az Apache Spark-fürt. Útmutatásért lásd: [létrehozása az Apache Spark on Azure hdinsight clusters](apache-spark-jupyter-spark-sql.md).

## <a name="spark-streaming-concepts"></a>Spark Streaming fogalmak

A Spark streamelési részletes ismertetése, [Apache Spark streaming áttekintése](http://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). HDInsight biztosítható a az Azure Spark-fürt azonos adatfolyam-továbbítási funkciókat.  

## <a name="what-does-this-solution-do"></a>Mire ehhez a megoldáshoz?

Ebben a cikkben egy Spark streaming példa létrehozásához hajtsa végre az alábbi lépéseket:

1. Hozzon létre egy Azure Event hubs Eseményközpontot, akik szintén megkapják az események adatfolyam.

2. Futtassa egy helyi önálló alkalmazás, amely eseményeket hoz létre, és az Azure Event hubs leküldéses értesítések azt. A mintaalkalmazás, amely ezt a közzétett [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples).

3. Távolról futtatni egy adatfolyam-továbbítási alkalmazást egy Spark-fürt, amely az Azure Event Hubs olvassa be az esemény streamelését és végezhetnek különböző adatfeldolgozási /.

## <a name="create-an-azure-event-hub"></a>Hozzon létre egy Azure Event Hubs

1. Jelentkezzen be a [Azure Portal](https://ms.portal.azure.com), és kattintson a **új** , a képernyő bal felső.

2. Kattintson az **Eszközök internetes hálózata**, majd az **Event Hubs** lehetőségre.

    ![Spark streaming példa eseményközpont létrehozása](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "Spark streamelési példa eseményközpont létrehozása")

3. A **Névtér létrehozása** panelen adja meg a névtér nevét. Válassza ki a tarifacsomag (alapszintű vagy Standard). Valamint válassza ki azt az Azure-előfizetést, erőforráscsoportot és helyet, amellyel az erőforrást létre kívánja hozni. A névtér létrehozásához kattintson a **Létrehozás** parancsra.

      ![Adjon meg a Spark streamelési példa event hub nevet](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "adja meg az eseményközpont neveként a Spark streamelési – példa")

    > [!NOTE]
    > Ki kell jelölni a azonos **hely** , az Apache Spark-fürt hdinsightban késés és a költségek csökkentése érdekében.
    >
    >

4. Az Event Hubs névtérlistájában kattintson az újonnan létrehozott névtérre.      


5. A névtér paneljén kattintson **Event Hubs**, és kattintson a **+ Eseményközpont** egy új Eseményközpont létrehozásához.
   
    ![Spark streaming példa eseményközpont létrehozása](./media/apache-spark-eventhub-streaming/hdinsight-open-event-hubs-blade-for-spark-streaming-example.png "Spark streamelési példa eseményközpont létrehozása")

6. Adjon meg egy nevet az Eseményközpont, állítsa be a partíciók száma 10 és üzenet megőrzési 1. Jelenleg nem archiválni az üzenetek ebben a megoldásban, a többi alapértelmezett maradjon, és kattintson **létrehozása**.
   
    ![Adja meg a Spark streaming példa event hub-adatok](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "Spark streamelési példa event hub-adatok megadása")

7. Az újonnan létrehozott Eseményközpont, az Event Hubs panel szerepel.
    
     ![Az Event Hubs szeretné megtekinteni a Spark streaming példa](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-for-spark-streaming-example.png "nézet Eseményközpont a Spark streaming – példa")

8. Térjen vissza a névtér panelre (nem az adott eseményközpont panelére), kattintson a **Megosztott elérési házirendek**, majd a **RootManageSharedAccessKey** elemre.
    
     ![Az Event Hubs házirendjeinek beállítása a Spark streaming példa](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "állítsa be az Event Hubs házirendeket a Spark streaming – példa")

9. A Másolás gombra kattintva másolja a **RootManageSharedAccessKey** elsődleges kulcs és a kapcsolati karakterláncot a vágólapra. Mentse őket az oktatóanyag későbbi részében használni.
    
     ![Az Event Hubs házirend kulcsok megtekintéséhez a Spark streaming példa](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "a Spark streamelési például kulcsok megtekintése az Event Hubs házirend")

## <a name="send-messages-to-azure-event-hub-using-a-sample-scala-application"></a>Üzenetek küldése az Azure Event Hubs egy mintaalkalmazás Scala használatával

Ebben a szakaszban egy különálló helyi Scala alkalmazás, amely események adatfolyam hoz létre, és elküldi azt korábban létrehozott Azure Event Hubs használja. Ez az alkalmazás érhető el a Githubon: [https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer). Itt a lépések azt feltételezik, hogy Ön rendelkezik már ágazik el a GitHub-tárházban.

1. Győződjön meg arról, hogy ezt az alkalmazást futtató számítógépen a következő.

    * Oracle Java fejlesztői készlet. A későbbiekben telepítheti az [Itt](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
    * Apache Maven. Letöltheti a [Itt](https://maven.apache.org/download.cgi). Útmutatás Maven telepítendő érhető el [Itt](https://maven.apache.org/install.html).

2. Nyisson meg egy parancssort, és keresse meg a helyet, a GitHub-tárház Scala mintaalkalmazás és futni építenie az alkalmazást a következő parancsot a klónozott.

        mvn package

3. Az alkalmazás a kimeneti jar **com-microsoft-azure-eventhubs-client-example-0.2.0.jar**, alatt létrejön **/target** könyvtár. Ez a cikk későbbi részében JAR segítségével tesztelheti a teljes megoldás.

## <a name="create-application-to-receive-messages-from-event-hub-into-a-spark-cluster"></a>Üzenetek fogadása az Event Hubs egy Spark-fürt az alkalmazás létrehozása 

Spark Streaming és az Azure Event Hubs, címzett-alapú kapcsolat és közvetlen DStream-alapú kapcsolat két módszer van. Jan 2017, a közvetlen DStream-alapú megjelent a 2.0.3 kiadásában. Lecseréli az eredeti fogadó-alapú kapcsolatot, mert az több performant feltételezett és erőforrás-hatékony. További részletek találhatók [https://github.com/hdinsight/spark-eventhubs](https://github.com/hdinsight/spark-eventhubs). Közvetlen DStream csak a Spark 2.0 + támogatja.

### <a name="build-applications-with-the-dependency-to-spark-eventhubs-connector"></a>A spark-eventhubs összekötő a függőséggel rendelkező alkalmazások

Azt is közzétesz a Spark-EventHubs átmeneti verzióját a Githubon. A Spark-EventHubs átmeneti verzióját használja, az első lépés GitHub jelzi a forrás-tárház, a következő bejegyzés hozzáadásával pom.xml:

```xml
<repository>
      <id>spark-eventhubs</id>
      <url>https://raw.github.com/hdinsight/spark-eventhubs/maven-repo/</url>
      <snapshots>
        <enabled>true</enabled>
        <updatePolicy>always</updatePolicy>
      </snapshots>
</repository>
```

A következő függőségi majd hozzá a projekthez az előzetes verzióra érvénybe.

Maven-függőség

```xml
<!-- https://mvnrepository.com/artifact/com.microsoft.azure/spark-streaming-eventhubs_2.11 -->
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spark-streaming-eventhubs_2.11</artifactId>
    <version>2.0.4</version>
</dependency>
```

SBT függőség

```
// https://mvnrepository.com/artifact/com.microsoft.azure/spark-streaming-eventhubs_2.11
libraryDependencies += "com.microsoft.azure" % "spark-streaming-eventhubs_2.11" % "2.0.4"
```

### <a name="direct-dstream-connection"></a>Közvetlen DStream kapcsolat

A letölthető egy előre elkészített jar-fájlt tartalmazó példák segítségével közvetlen DStream [http://central.maven.org/maven2/com/microsoft/azure/spark-streaming-eventhubs_2.11/2.0.4/spark-streaming-eventhubs_2.11-2.0.4.jar](http://central.maven.org/maven2/com/microsoft/azure/spark-streaming-eventhubs_2.11/2.0.4/spark-streaming-eventhubs_2.11-2.0.4.jar).

A jar-fájlt tartalmaz három példák, amelyek a forráskód érhetők el [https://github.com/hdinsight/spark-eventhubs/tree/master/examples/src/main/scala/com/microsoft/spark/streaming/examples/directdstream](https://github.com/hdinsight/spark-eventhubs/tree/master/examples/src/main/scala/com/microsoft/spark/streaming/examples/directdstream).

Véve [WindowingWordCount](https://github.com/hdinsight/spark-eventhubs/blob/master/examples/src/main/scala/com/microsoft/spark/streaming/examples/directdstream/WindowingWordCount.scala) példa:

```scala
private def createStreamingContext(
  sparkCheckpointDir: String,
  batchDuration: Int,
  namespace: String,
  eventHunName: String,
  eventhubParams: Map[String, String],
  progressDir: String) = {
val ssc = new StreamingContext(new SparkContext(), Seconds(batchDuration))
ssc.checkpoint(sparkCheckpointDir)
val inputDirectStream = EventHubsUtils.createDirectStreams(
  ssc,
  namespace,
  progressDir,
  Map(eventHunName -> eventhubParams))

inputDirectStream.map(receivedRecord => (new String(receivedRecord.getBody), 1)).
  reduceByKeyAndWindow((v1, v2) => v1 + v2, (v1, v2) => v1 - v2, Seconds(batchDuration * 3),
    Seconds(batchDuration)).print()

ssc
}

def main(args: Array[String]): Unit = {

if (args.length != 8) {
  println("Usage: program progressDir PolicyName PolicyKey EventHubNamespace EventHubName" +
    " BatchDuration(seconds) Spark_Checkpoint_Directory maxRate")
  sys.exit(1)
}

val progressDir = args(0)
val policyName = args(1)
val policykey = args(2)
val namespace = args(3)
val name = args(4)
val batchDuration = args(5).toInt
val sparkCheckpointDir = args(6)
val maxRate = args(7)

val eventhubParameters = Map[String, String] (
  "eventhubs.policyname" -> policyName,
  "eventhubs.policykey" -> policykey,
  "eventhubs.namespace" -> namespace,
  "eventhubs.name" -> name,
  "eventhubs.partition.count" -> "32",
  "eventhubs.consumergroup" -> "$Default",
  "eventhubs.maxRate" -> s"$maxRate"
)

val ssc = StreamingContext.getOrCreate(sparkCheckpointDir,
  () => createStreamingContext(sparkCheckpointDir, batchDuration, namespace, name,
    eventhubParameters, progressDir))

ssc.start()
ssc.awaitTermination()
}
```

A fenti példában `eventhubParameters` EventHubs egypéldányos vonatkoznak a paramétereket, és végre kell hajtania, hogy a `createDirectStreams` API-hoz létre egy közvetlen DStream objektum leképezésének egy Event Hubs-névtérhez. Spark Streamelési API keretrendszer által biztosított DStream API-k hívása az közvetlen DStream objektum. Ebben a példában azt az utolsó 3 micro kötegelt intervallumok belül minden szó gyakoriságát számítható ki.

### <a name="receiver-based-connection"></a>Fogadó-alapú kapcsolat

A Spark streaming scalában, amely fogadja a eseményeket, és a különböző célok továbbítani, írt mintaalkalmazás érhető el: [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples). Az Event Hubs konfigurációját az alkalmazás frissítése és a kimeneti jar létrehozása az alábbi lépésekkel.

1. Indítsa el az IntelliJ IDEA, és válassza ki az indítóképernyő **tekintse meg a verziókövetés** majd **Git**.
   
    ![Apache Spark streaming példa - get forrásokból származó Git](./media/apache-spark-eventhub-streaming/spark-streaming-example-get-source-from-git.png "Apache Spark streaming példa - get forrásokból származó Git")

2. Az a **klónozott tárház** párbeszédpanelen adja meg a klónozandó, adja meg a könyvtárat a klón, és kattintson a Git-tárház URL-címe **Klónozás**.
   
    ![Apache Spark streaming példa - klón a Git](./media/apache-spark-eventhub-streaming/spark-streaming-example-clone-from-git.png "Apache Spark streaming példa - klón a Git")
3. Kövesse az utasításokat, amíg a projekt teljesen klónozták. Nyomja le az **Alt + 1** megnyitásához a **Projektnézetében**. A következő kell hasonlítania.
   
    ![Apache Spark streaming példa - projekt nézetben](./media/apache-spark-eventhub-streaming/spark-streaming-example-project-view.png "Apache Spark streaming példa - projekt nézetben")
4. Győződjön meg arról, hogy az alkalmazás kódjának fordítása során Java8. Győződjön meg arról, ez, kattintson a **fájl**, kattintson **szerkezetének**, és a a **projekt** lapra, győződjön meg arról, hogy projekt nyelvi szintje **8 - típus jegyzeteket, stb. lambda kifejezések**.
   
    ![Apache Spark streaming példa - beállítása fordító](./media/apache-spark-eventhub-streaming/spark-streaming-example-java-8-compiler.png "Apache Spark streaming példa - beállítása fordító")
5. Nyissa meg a **pom.xml** és ellenőrizze, hogy a Spark verziója megfelelő. A `<properties>` csomópont, keresse meg a következő kódrészletet, és a Spark-verziójának ellenőrzése.

        <scala.version>2.11.8</scala.version>
        <scala.compat.version>2.11.8</scala.compat.version>
        <scala.binary.version>2.11</scala.binary.version>
        <spark.version>2.0.0</spark.version>

6. Az alkalmazás futtatásához szükséges egy függőségi jar nevű **JDBC illesztőprogram jar**. Ez szükséges az üzenetek az Event Hubs kapott egy Azure SQL-adatbázisba írni. Letöltheti a jar (v4.1-es vagy újabb) a [Itt](https://msdn.microsoft.com/sqlserver/aa937724.aspx). Adja hozzá a jar hivatkozást a projekt könyvtárában. Hajtsa végre a következő lépéseket:
     
     1. IntelliJ IDEA ablakban nyissa meg az alkalmazás esetében, kattintson a **fájl**, kattintson a **szerkezetének**, és kattintson a **szalagtárak**. 
     2. Kattintson a Hozzáadás ikonra (![Hozzáadás ikon](./media/apache-spark-eventhub-streaming/add-icon.png)), kattintson a **Java**, és navigáljon arra a helyre, amelybe letöltötte a JDBC illesztőprogram jar. Kövesse az utasításokat a jar-fájlra felvétele a projekt könyvtárba.

         ![Adja hozzá a Hiányzó függőségek](./media/apache-spark-eventhub-streaming/add-missing-dependency-jars.png "adja hozzá a hiányzó függőség JAR-fájlok kivételével")
     3. Kattintson az **Alkalmaz** gombra.

7. Hozzon létre a kimeneti jar-fájlra. A következő lépésekkel.

   1. Az a **szerkezetének** párbeszédpanel, kattintson a **összetevők** és kattintson a plusz jelre. Az előugró párbeszédpanelen kattintson **JAR**, és kattintson a **a függőségekkel rendelkező modulok**.      
       
       ![Apache Spark streamelési példa - JAR létrehozása](./media/apache-spark-eventhub-streaming/spark-streaming-example-create-jar.png "Apache Spark streamelési példa - JAR létrehozása")
   2. Az a **modulokban létrehozása JAR** párbeszédpanel párbeszédpanelen kattintson a három pont (![három pont](./media/apache-spark-eventhub-streaming/ellipsis.png)) szemben a **fő osztály**.
   3. Az a **fő osztály kiválasztása** párbeszédpanel mezőbe, jelölje be a rendelkezésre álló osztályok bármelyikét, majd kattintson **OK**.
      
       ![Apache Spark streaming példa - jar válassza osztály](./media/apache-spark-eventhub-streaming/spark-streaming-example-select-class-for-jar.png "Apache Spark streaming példa - jar válassza osztály")
   4. Az a **modulokban létrehozása JAR** párbeszédpanelen győződjön meg arról, hogy lehetőség **bontsa ki a cél JAR** van kiválasztva, és kattintson **OK**. Ez minden függőség egyetlen JAR hoz létre.
      
       ![Apache Spark streamelési példa - modulok jar létrehozása](./media/apache-spark-eventhub-streaming/spark-streaming-example-create-jar-from-modules.png "Apache Spark streamelési példa - modulok jar létrehozása")
   5. A **kimeneti elrendezés** lap felsorolja az összes a JAR-fájlok kivételével, amelyek tartalmazzák a Maven project a. Válassza ki, és törölheti azokat, amelyeken a Scala alkalmazásnak nincs közvetlen függőség. Itt létrehozzuk az alkalmazás is távolítja el a legutóbb (**spark-adatfolyam-adatok-adatmegőrzési-példák fordítási kimeneti**). A JAR-fájlok kivételével törli, majd válassza ki a **törlése** ikon (![törlés ikonja](./media/apache-spark-eventhub-streaming/delete-icon.png)).
      
       ![Apache Spark streaming példa - kibontott delete JAR-fájlok kivételével](./media/apache-spark-eventhub-streaming/spark-streaming-example-delete-output-jars.png "Apache Spark streaming - kibontott delete JAR-fájlok kivételével – példa")
      
       Győződjön meg arról, hogy **győződjön Build** be van jelölve, amely biztosítja, hogy a jar minden alkalommal létrejön a projekt beépített vagy frissíteni. Kattintson az **Alkalmaz** gombra.
   6. Az a **kimeneti elrendezés** lapon jobb alján a **rendelkezésre álló elemek** mezőben van az SQL JDBC jar a projekt könyvtárhoz korábban hozzáadott. Hozzá kell adnia ezt a **kimeneti elrendezés** fülre. Kattintson a jobb gombbal a jar-fájlra, és kattintson **bontsa ki a kimeneti gyökér**.
      
       ![Apache Spark streaming példa - kivonat függőségi jar](./media/apache-spark-eventhub-streaming/spark-streaming-example-extract-dependency-jar.png "Apache Spark streaming példa - kivonat függőségi jar")  
      
       A **kimeneti elrendezés** lapon most példához hasonló.
      
       ![Apache Spark streaming példa - végső kimenetet lapon](./media/apache-spark-eventhub-streaming/spark-streaming-example-final-output-tab.png "Apache Spark streaming példa - végső kimenetet lap")        
      
       A a **szerkezetének** párbeszédpanel, kattintson a **alkalmaz** majd **OK**.    
   7. A menüsávban kattintson **Build**, és kattintson a **ellenőrizze projekt**. Is **Build összetevők** a jar létrehozásához. A kimeneti jar alatt létrejön **\classes\artifacts**.
      
       ![Apache Spark streamelési példa - kimeneti JAR](./media/apache-spark-eventhub-streaming/spark-streaming-example-output-jar.png "Apache Spark streamelési példa - kimeneti JAR")

## <a name="run-the-application-remotely-on-a-spark-cluster-using-livy"></a>Az alkalmazás távoli futtatása Spark-fürtön Livy használatával

Ebben a cikkben Livy futtatásához használt az Apache Spark streaming alkalmazás távolról Spark-fürtön. HDInsight Spark-fürtön a Livy használatával részletes leírását lásd: [küldés feladatok távolról Apache Spark on Azure HDInsight fürt](apache-spark-livy-rest-interface.md). Mielőtt megkezdené a Spark streaming alkalmazás fut, van néhány dolgot kell tennie:

1. Indítsa el a helyi önálló alkalmazás események generálásához, és küldése eseményközpontba. A következő paranccsal teheti:

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "mysbnamespace" --eventhubs-name "myeventhub" --policy-name "mysendpolicy" --policy-key "<policy key>" --message-length 32 --thread-count 32 --message-count -1

2. Másolja át a folyamatos átviteli jar (**spark-adatfolyam-adatok-adatmegőrzési-examples.jar**) a fürthöz tartozó Azure blobtárolóba. Így a jar Livy érhető el. Használhat [ **AzCopy**](../../storage/common/storage-use-azcopy.md), parancssori segédprogram, ehhez. Nincsenek sok más ügyfelek segítségével feltölteni az adatokat. A rájuk vonatkozó további található [feltölteni az adatokat a HDInsight Hadoop-feladatok](../hdinsight-upload-data.md).
3. Telepítse a CURL ezeket az alkalmazásokat futtató számítógépre. CURL a Livy végpontokat a feladatok távoli futtatása meghívása használjuk.

### <a name="run-the-spark-streaming-application-to-receive-the-events-into-an-azure-storage-blob-as-text"></a>A Spark streaming az események fogadásához szövegként egy Azure Storage-Blobból az alkalmazás futtatása

Nyisson meg egy parancssort, keresse meg a CURL telepítési könyvtárát, és futtassa a következő parancsot (a név felülírandó felhasználónév/jelszó és a fürt neve):

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputBlob.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

A paraméterek, a fájl **inputBlob.txt** az alábbiak szerint definiáltuk:

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsEventCount", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Ossza meg velünk megismeréséhez a bemeneti fájl paraméterek:

* **fájl** elérési útját a fürthöz társított Azure storage-fiók alkalmazás jar-fájlra.
* **Osztálynév** az osztály a jar a neve.
* **argumentum** van az osztály által igényelt argumentumok listájának megjelenítése
* **numExecutors** adatfolyam futtatása Spark által használt magok száma. Mindig legyen legalább kétszerese az Event Hubs partíciók száma.
* **executorMemory**, **executorCores**, **driverMemory** szükséges erőforrások hozzárendelése az adatfolyam-továbbítási alkalmazást használt paraméterek.

> [!NOTE]
> Nem kell létrehozni a kimeneti mappák (EventCheckpoint, EventCount/EventCount10), amelyek paraméterekként. Az adatfolyam-továbbítási alkalmazás létrehozza azt.
>
>

A parancs futtatásakor a következőhöz hasonló kimenetnek kell megjelennie:

    < HTTP/1.1 201 Created
    < Content-Type: application/json; charset=UTF-8
    < Location: /18
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Tue, 01 Dec 2015 05:39:10 GMT
    < Content-Length: 37
    <
    {"id":1,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

Jegyezze fel a Kötegazonosító (a példában az értéke "1") a kimenet utolsó sora a. Győződjön meg arról, hogy az alkalmazás sikeres futtatása, hogy vessen egy pillantást a a fürthöz tartozó Azure-tárfiókot, és megjelenítheti a **/EventCount/EventCount10** létrehozott mappába. Ebben a mappában kell tartalmaznia, amely rögzíti az idő megadott időtartamon belül a paraméter a feldolgozott események száma blobok **batch-időköz-a-(másodperc)**.

A Spark streaming alkalmazás továbbra is futnak, amíg nem állítsa le. Ehhez használja a következő parancsot:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/1"

### <a name="run-the-applications-to-receive-the-events-into-an-azure-storage-blob-as-json"></a>Az alkalmazások az események fogadásához az Azure Storage-Blobba JSON-ként történő futtatása
Nyisson meg egy parancssort, keresse meg a CURL telepítési könyvtárát, és futtassa a következő parancsot (a név felülírandó felhasználónév/jelszó és a fürt neve):

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputJSON.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

A paraméterek, a fájl **inputJSON.txt** az alábbiak szerint definiáltuk:

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureBlobAsJSON", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-store-folder", "/EventStore10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

A paraméterek a szöveges kimenet az előző lépésben megadott hasonlóak. Ebben az esetben nem kell létrehozni a kimeneti mappa (EventCheckpoint, EventCount/EventCount10) paraméterekként használt. Az adatfolyam-továbbítási alkalmazás létrehozza azt.

 Után futtassa a parancsot, megtalálhatja a a fürthöz tartozó Azure-tárfiókot, és megjelenítheti a **/EventStore10** létrehozott mappába. Nyissa meg a fájl a következő előtaggal **rész -** és a JSON formátumban a feldolgozott események kell megjelennie.

### <a name="run-the-applications-to-receive-the-events-into-a-hive-table"></a>A Hive tábla az események fogadásához az alkalmazások futtatása
A Spark streaming alkalmazás, amely egy Hive táblába az események adatfolyamokat futtatásához szükség van néhány további összetevőket. Ezek a következők:

* datanucleus-api-jdo-3.2.6.jar
* datanucleus-rdbms-3.2.9.jar
* datanucleus-core-3.2.10.jar
* Hive-site.xml

A **.jar** fájlok érhetők el: a HDInsight Spark-fürt `/usr/hdp/current/spark-client/lib`. A **hive-site.xml** érhető el `/usr/hdp/current/spark-client/conf`.

Használhat [WinScp](http://winscp.net/eng/download.php) ezeket a fájlokat másolja a fürtről a helyi számítógépen. Eszközök segítségével ezeket a fájlokat másolja át a tárfiókhoz a fürthöz tartozó majd. Fájlok feltöltése a tárfiókba kapcsolatos további információkért lásd: [feltölteni az adatokat a HDInsight Hadoop-feladatok](../hdinsight-upload-data.md).

Miután átmásolta a fájlokat az Azure storage-fiókjába, nyisson meg egy parancssort, keresse meg a CURL telepítési könyvtárát, és futtassa a következő parancsot (a név felülírandó felhasználónév/jelszó és a fürt neve):

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputHive.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

A paraméterek, a fájl **inputHive.txt** az alábbiak szerint definiáltuk:

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToHiveTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-hive-table", "EventHiveTable10" ], "jars":["wasb:///example/jars/datanucleus-api-jdo-3.2.6.jar", "wasb:///example/jars/datanucleus-rdbms-3.2.9.jar", "wasb:///example/jars/datanucleus-core-3.2.10.jar"], "files":["wasb:///example/jars/hive-site.xml"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

A paraméterek a szöveges kimenet az előző lépésben megadott hasonlóak. Ebben az esetben nem kell létrehozni a kimeneti mappa (EventCheckpoint, EventCount/EventCount10) vagy a kimeneti paraméterként használt Hive tábla (EventHiveTable10). Az adatfolyam-továbbítási alkalmazás létrehozza azt. Vegye figyelembe, hogy a **JAR-fájlok kivételével** és **fájlok** beállítás a .jar fájlt és a hive-site.xml felülírt a tárfiók elérési útjai tartalmaz.

Győződjön meg arról, hogy a hive tábla létrehozása sikeresen megtörtént, használja a [Ambari Hive nézete](../hadoop/apache-hadoop-use-hive-ambari-view.md). Nincs, a SELECT lekérdezés futtatásával tekintheti meg a tábla tartalmát.

    SELECT * FROM eventhivetable10 LIMIT 10;

A következőhöz hasonló kimenetnek kell megjelennie:

    ZN90apUSQODDTx7n6Toh6jDbuPngqT4c
    sor2M7xsFwmaRW8W8NDwMneFNMrOVkW1
    o2HcsU735ejSi2bGEcbUSB4btCFmI1lW
    TLuibq4rbj0T9st9eEzIWJwNGtMWYoYS
    HKCpPlWFWAJILwR69MAq863nCWYzDEw6
    Mvx0GQOPYvPR7ezBEpIHYKTKiEhYammQ
    85dRppSBSbZgThLr1s0GMgKqynDUqudr
    5LAWkNqorLj3ZN9a2mfWr9rZqeXKN4pF
    ulf9wSFNjD7BZXCyunozecov9QpEIYmJ
    vWzM3nvOja8DhYcwn0n5eTfOItZ966pa
    Time taken: 4.434 seconds, Fetched: 10 row(s)


### <a name="run-the-applications-to-receive-the-events-into-an-azure-sql-database-table"></a>Az események fogadásához az Azure SQL adatbázis táblába az alkalmazások futtatása
Ez a lépés futtatása előtt győződjön meg arról, hogy az Azure SQL-adatbázis létrehozása. Útmutatásért lásd: [SQL-adatbázis létrehozása percben](../../sql-database/sql-database-get-started-portal.md). Ez a szakasz befejezéséhez kell értékek adatbázisnév, adatbázis-kiszolgáló nevét és az adatbázis rendszergazdájának hitelesítő adatait a paraméterekként. Nem kell a adatbázistábla létrehozása. A Spark streaming alkalmazást hoz létre, amely meg.

Nyisson meg egy parancssort, keresse meg a CURL telepítési könyvtárát, és futtassa a következő parancsot:

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputSQL.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

A paraméterek, a fájl **inputSQL.txt** az alábbiak szerint definiáltuk:

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureSQLTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--sql-server-fqdn", "<database-server-name>.database.windows.net", "--sql-database-name", "mysparkdatabase", "--database-username", "sparkdbadmin", "--database-password", "<put-password-here>", "--event-sql-table", "EventContent" ], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Győződjön meg arról, hogy az alkalmazás sikeres futtatása, akkor is képes csatlakozni az Azure SQL-adatbázis SQL Server Management Studio használatával. Ehhez útmutatást lásd: [Csatlakozás SQL Database adatbázishoz az SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md). Miután csatlakozott az adatbázishoz, lépjen a **EventContent** táblázatot, amely az adatfolyam-továbbítási alkalmazást hozta létre. Az adatok lekérése a táblából gyors lekérdezés futtatása. Futtassa a következő lekérdezést:

    SELECT * FROM EventCount

A következőhöz hasonló kimenetnek kell megjelennie:

    00046b0f-2552-4980-9c3f-8bba5647c8ee
    000b7530-12f9-4081-8e19-90acd26f9c0c
    000bc521-9c1b-4a42-ab08-dc1893b83f3b
    00123a2a-e00d-496a-9104-108920955718
    0017c68f-7a4e-452d-97ad-5cb1fe5ba81b
    001KsmqL2gfu5ZcuQuTqTxQvVyGCqPp9
    001vIZgOStka4DXtud0e3tX7XbfMnZrN
    00220586-3e1a-4d2d-a89b-05c5892e541a
    0029e309-9e54-4e1b-84be-cd04e6fce5ec
    003333cf-874f-4045-9da3-9f98c2b4ea49
    0043c07e-8d73-420a-9af7-1fcb94575356
    004a11a9-0c2c-4bc0-a7d5-2e0ebd947ab9


## <a name="seealso"></a>Lásd még:
* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)
* [Fogadó-alapú kapcsolat és a közvetlen DStream tervezése](https://www.slideshare.net/NanZhu/seattle-sparkmeetup032317)

### <a name="scenarios"></a>Forgatókönyvek
* [Spark és BI: Interaktív adatelemzés végrehajtása a Spark on HDInsight használatával, BI-eszközökkel](apache-spark-use-bi-tools.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az épület-hőmérséklet elemzésére HVAC-adatok alapján](apache-spark-ipython-notebook-machine-learning.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [A webhelynapló elemzése a Spark on HDInsight használatával](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](../hdinsight-apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Spark-fürtön a Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark-alkalmazások távoli hibaelhárításához](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Zeppelin notebookok használata Spark-fürttel HDInsighton](apache-spark-zeppelin-notebook.md)
* [Jupyter notebookokhoz elérhető kernelek a HDInsight Spark-fürtjében](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]: ../storage-create-storage-account/
