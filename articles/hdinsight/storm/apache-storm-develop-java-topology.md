---
title: Apache Storm példa Java topológia - Azure HDInsight
description: Ismerje meg, hogyan hozhat létre Apache Storm topológiákat Java-ban egy példa szószámláló topológia létrehozásával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/14/2019
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 75100b47ddf8f36ed9a22ff3073c439f8ad9040b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083298"
---
# <a name="create-an-apache-storm-topology-in-java"></a>Apache Storm topológia létrehozása Java nyelven

Ismerje meg, hogyan hozhat létre Java-alapú topológiát az [Apache Storm](https://storm.apache.org/)számára. Itt hozzon létre egy Storm-topológiát, amely egy szószámláló alkalmazást valósít meg. Az [Apache Maven](https://maven.apache.org/) segítségével hozhat létre és csomagolhatja a projektet. Ezután megtudhatja, hogyan definiálhatja a topológia az [Apache Storm Flux](https://storm.apache.org/releases/2.0.0/flux.html) keretrendszer használatával.

Miután elvégezte a jelen dokumentumlépéseit, telepítheti a topológiát az Apache Storm hdinsight-on.

> [!NOTE]  
> A dokumentumban létrehozott Storm topológiapéldák befejezett [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount)verziója a következő helyen érhető el: .

## <a name="prerequisites"></a>Előfeltételek

* [Java Developer Kit (JDK) 8-as verzió](https://aka.ms/azure-jdks)

* [Apache Maven](https://maven.apache.org/download.cgi) megfelelően [telepítve](https://maven.apache.org/install.html) szerint Apache.  Maven egy projekt épít rendszer Java projektek.

## <a name="test-environment"></a>Vizsgálati környezet

A cikkhez használt környezet egy Windows 10-et futtató számítógép volt.  A parancsok végrehajtása a parancssorban történt, és a különböző fájlokat a Jegyzettömbbel szerkesztették.

A parancssorból adja meg az alábbi parancsokat a munkakörnyezet létrehozásához:

```cmd
mkdir C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Maven-projekt létrehozása

A **WordCount**nevű Maven-projekt létrehozásához írja be a következő parancsot:

```cmd
mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

cd WordCount
mkdir resources
```

Ez a parancs `WordCount` létrehoz egy könyvtárat, amelyet az aktuális helyen neveznek el, és amely egy alapvető Maven-projektet tartalmaz. A második parancs a jelenlegi `WordCount`munkakönyvtárat . A harmadik parancs új `resources`könyvtárat hoz létre, amelyet később fog használni.  A `WordCount` könyvtár a következő elemeket tartalmazza:

* `pom.xml`: A Maven projekt beállításait tartalmazza.
* `src\main\java\com\microsoft\example`: Tartalmazza az alkalmazás kódját.
* `src\test\java\com\microsoft\example`: Az alkalmazás tesztjeit tartalmazza.  

### <a name="remove-the-generated-example-code"></a>A létrehozott példakód eltávolítása

Törölje a létrehozott teszt- `AppTest.java`és `App.java` alkalmazásfájlokat, és írja be az alábbi parancsokat:

```cmd
DEL src\main\java\com\microsoft\example\App.java
DEL src\test\java\com\microsoft\example\AppTest.java
```

## <a name="add-maven-repositories"></a>Maven-adattárak hozzáadása

A HDInsight a Hortonworks Data Platformon (HDP) alapul, ezért azt javasoljuk, hogy a Hortonworks-tárház használatával töltse le az Apache Storm-projektek függőségeit.  

Nyissa `pom.xml` meg az alábbi parancs megadásával:

```cmd
notepad pom.xml
```

Ezután adja hozzá `<url>https://maven.apache.org</url>` a következő XML-t a sor után:

```xml
<repositories>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPReleases</id>
        <name>HDP Releases</name>
        <url>https://repo.hortonworks.com/content/repositories/releases/</url>
        <layout>default</layout>
    </repository>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPJetty</id>
        <name>Hadoop Jetty</name>
        <url>https://repo.hortonworks.com/content/repositories/jetty-hadoop/</url>
        <layout>default</layout>
    </repository>
</repositories>
```

## <a name="add-properties"></a>Tulajdonságok hozzáadása

A Maven lehetővé teszi a tulajdonságoknak nevezett projektszintű értékek meghatározását. A `pom.xml`alkalmazásban a `</repositories>` sor után a következő szöveget kell megadni:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--
    This is a version of Storm from the Hortonworks repository that is compatible with HDInsight 3.6.
    -->
    <storm.version>1.1.0.2.6.1.9-1</storm.version>
</properties>
```

Ezt az értéket most már használhatja `pom.xml`a szakaszának más szakaszaiban is. Ha például a Storm-összetevők verzióját adja meg, az érték szigorú kódolása helyett használható. `${storm.version}`

## <a name="add-dependencies"></a>Függőségek hozzáadása

Adjon hozzá egy függőséget a Storm-összetevőkhöz. A `pom.xml`alkalmazásban adja hozzá `<dependencies>` a következő szöveget a szakaszba:

```xml
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-core</artifactId>
    <version>${storm.version}</version>
    <!-- keep storm out of the jar-with-dependencies -->
    <scope>provided</scope>
</dependency>
```

Fordításkor a Maven ezeket az `storm-core` információkat használja a Maven-tárházban való felkereséshez. Először a helyi számítógép tárházában jelenik meg. Ha a fájlok nincsenek ott, a Maven letölti őket a nyilvános Maven tárházból, és tárolja őket a helyi tárházban.

> [!NOTE]  
> Figyelje `<scope>provided</scope>` meg a vonalat ebben a szakaszban. Ez a beállítás arra utasítja a Maven-t, hogy zárja ki a **storm-core-t** a létrehozott JAR-fájlokból, mert azt a rendszer biztosítja.

## <a name="build-configuration"></a>Konfiguráció létrehozása

A Maven beépülő modulok lehetővé teszik a projekt építési szakaszainak testreszabását. Például a projekt fordításának vagy jar fájlba való csomagolásának módját. A `pom.xml`alkalmazásban adja hozzá a `</project>` következő szöveget közvetlenül a sor fölé.

```xml
<build>
    <plugins>
    </plugins>
    <resources>
    </resources>
</build>
```

Ez a szakasz beépülő modulok, erőforrások és egyéb build-konfigurációs beállítások hozzáadására szolgál. A fájl teljes `pom.xml` körű hivatkozását lásd: [https://maven.apache.org/pom.html](https://maven.apache.org/pom.html).

### <a name="add-plug-ins"></a>Beépülő modulok hozzáadása

* **Exec Maven beépülő modul**

    A Java-ban megvalósított Apache Storm topológiák esetében az [Exec Maven plugin](https://www.mojohaus.org/exec-maven-plugin/) azért hasznos, mert lehetővé teszi a topológia helyi futtatását a fejlesztői környezetben. Adja hozzá a `<plugins>` következőt `pom.xml` a fájl azon részéhez, amely tartalmazza az Exec Maven bővítményt:

    ```xml
    <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.6.0</version>
        <executions>
            <execution>
                <goals>
                    <goal>exec</goal>
                </goals>
            </execution>
        </executions>
        <configuration>
            <executable>java</executable>
            <includeProjectDependencies>true</includeProjectDependencies>
            <includePluginDependencies>false</includePluginDependencies>
            <classpathScope>compile</classpathScope>
            <mainClass>${storm.topology}</mainClass>
            <cleanupDaemonThreads>false</cleanupDaemonThreads>
        </configuration>
    </plugin>
    ```

* **Apache Maven fordító beépülő modul**

    Egy másik hasznos plug-in az [Apache Maven Compiler Plugin](https://maven.apache.org/plugins/maven-compiler-plugin/), amelyet a fordítási beállítások megváltoztatására használnak. Módosítsa a Java-verzió, amely maven használja a forrás és a cél az alkalmazáshoz.

  * A HDInsight __3.4-es vagy korábbi__verziójában állítsa a forrást és a java verziót __1.7-re.__

  * A HDInsight __3.5__esetében állítsa a forrást és a cél Java-verziót __1.8-ra.__

  Adja hozzá a `<plugins>` következő szöveget a fájl szakaszában, `pom.xml` hogy tartalmazza az Apache Maven Compiler plugint. Ez a példa 1.8-at ad meg, így a cél HDInsight-verzió 3.5.

  ```xml
  <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.8.1</version>
    <configuration>
            <source>1.8</source>
            <target>1.8</target>
    </configuration>
  </plugin>
  ```

### <a name="configure-resources"></a>Erőforrások konfigurálása

Az erőforrások szakasz lehetővé teszi, hogy nem kódos erőforrásokat, például az összetevők által a topológiába szükséges konfigurációs fájlokat vegyen fel. Ebben a példában adja hozzá `<resources>` a `pom.xml` következő szöveget a fájl szakaszába. Ezután mentse és zárja be a fájlt.

```xml
<resource>
    <directory>${basedir}/resources</directory>
    <filtering>false</filtering>
    <includes>
            <include>log4j2.xml</include>
    </includes>
</resource>
```

Ez a példa a projekt (`${basedir}`) gyökérkönyvtárában lévő erőforráskönyvtárat erőforrásokat `log4j2.xml`tartalmazó helyként adja hozzá, és tartalmazza a . Ez a fájl a topoológia által naplózott információk konfigurálására szolgál.

## <a name="create-the-topology"></a>A topológia létrehozása

A Java-alapú Apache Storm topológia három összetevőből áll, amelyeket függőségként kell megszerzőnek (vagy hivatkozásnak) lennie.

* **Spouts**: Beolvassa a külső forrásokból származó adatokat, és adatfolyamokat bocsát ki a topológiába.

* **Csavarok:** A feldolgozást a spoutok vagy más csavarok által kibocsátott adatfolyamokon hajtja végre, és egy vagy több adatfolyamot bocsát ki.

* **Topológia:** Meghatározza, hogy a spouts és a csavarok vannak elrendezve, és biztosítja a belépési pontot a topológia.

### <a name="create-the-spout"></a>A kifolyó létrehozása

A külső adatforrások beállításának csökkentése érdekében a következő spout egyszerűen véletlenszerű mondatokat bocsát ki. Ez egy módosított változata a kifolyó, amely a [Storm-Starter példákat.](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter)  Bár ez a topológia csak egy spoutot használ, mások több olyan adatot is tartalmazhatnak, amelyek különböző forrásokból származó adatokat táplálnak be a topológiába.

Adja meg az alábbi parancsot `RandomSentenceSpout.java`egy új fájl létrehozásához és megnyitásához:

```cmd
notepad src\main\java\com\microsoft\example\RandomSentenceSpout.java
```

Ezután másolja be az alábbi java kódot az új fájlba.  Ezután zárja be a fájlt.

```java
package com.microsoft.example;

import org.apache.storm.spout.SpoutOutputCollector;
import org.apache.storm.task.TopologyContext;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseRichSpout;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Values;
import org.apache.storm.utils.Utils;

import java.util.Map;
import java.util.Random;

//This spout randomly emits sentences
public class RandomSentenceSpout extends BaseRichSpout {
  //Collector used to emit output
  SpoutOutputCollector _collector;
  //Used to generate a random number
  Random _rand;

  //Open is called when an instance of the class is created
  @Override
  public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
  //Set the instance collector to the one passed in
    _collector = collector;
    //For randomness
    _rand = new Random();
  }

  //Emit data to the stream
  @Override
  public void nextTuple() {
  //Sleep for a bit
    Utils.sleep(100);
    //The sentences that are randomly emitted
    String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
        "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
    //Randomly pick a sentence
    String sentence = sentences[_rand.nextInt(sentences.length)];
    //Emit the sentence
    _collector.emit(new Values(sentence));
  }

  //Ack is not implemented since this is a basic example
  @Override
  public void ack(Object id) {
  }

  //Fail is not implemented since this is a basic example
  @Override
  public void fail(Object id) {
  }

  //Declare the output fields. In this case, an sentence
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("sentence"));
  }
}
```

> [!NOTE]  
> Egy külső adatforrásból beolvasást tartalmazó spout példáját az alábbi példák egyike ként talál:
>
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): Egy példa spout, amely a Twitterről olvas.
> * [Storm-Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): A kifolyó, hogy felolvassa a Kafka.

### <a name="create-the-bolts"></a>A csavarok létrehozása

A csavarok kezelik az adatfeldolgozást. A csavarok bármit megtehetnek, például számítást, perzisztenciát vagy külső összetevőkkel való beszélgetést. Ez a topológia két csavart használ:

* **SplitSentence**: A **RandomSentenceSpout** által kibocsátott mondatokat felosztja egyes szavakra.

* **WordCount**: Megszámolja, hogy az egyes szavak hányszor történtek.

#### <a name="splitsentence"></a>SplitSentence (Osztott mondat)

Adja meg az alábbi parancsot `SplitSentence.java`egy új fájl létrehozásához és megnyitásához:

```cmd
notepad src\main\java\com\microsoft\example\SplitSentence.java
```

Ezután másolja be az alábbi java kódot az új fájlba.  Ezután zárja be a fájlt.

```java
package com.microsoft.example;

import java.text.BreakIterator;

import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class SplitSentence extends BaseBasicBolt {

  //Execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //Get the sentence content from the tuple
    String sentence = tuple.getString(0);
    //An iterator to get each word
    BreakIterator boundary=BreakIterator.getWordInstance();
    //Give the iterator the sentence
    boundary.setText(sentence);
    //Find the beginning first word
    int start=boundary.first();
    //Iterate over each word and emit it to the output stream
    for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
      //get the word
      String word=sentence.substring(start,end);
      //If a word is whitespace characters, replace it with empty
      word=word.replaceAll("\\s+","");
      //if it's an actual word, emit it
      if (!word.equals("")) {
        collector.emit(new Values(word));
      }
    }
  }

  //Declare that emitted tuples contain a word field
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word"));
  }
}
```

#### <a name="wordcount"></a>WordCount (Szószám)

Adja meg az alábbi parancsot `WordCount.java`egy új fájl létrehozásához és megnyitásához:

```cmd
notepad src\main\java\com\microsoft\example\WordCount.java
```

Ezután másolja be az alábbi java kódot az új fájlba.  Ezután zárja be a fájlt.

```java
package com.microsoft.example;

import java.util.HashMap;
import java.util.Map;
import java.util.Iterator;

import org.apache.storm.Constants;
import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;
import org.apache.storm.Config;

// For logging
import org.apache.logging.log4j.Logger;
import org.apache.logging.log4j.LogManager;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class WordCount extends BaseBasicBolt {
  //Create logger for this class
  private static final Logger logger = LogManager.getLogger(WordCount.class);
  //For holding words and counts
  Map<String, Integer> counts = new HashMap<String, Integer>();
  //How often to emit a count of words
  private Integer emitFrequency;

  // Default constructor
  public WordCount() {
      emitFrequency=5; // Default to 60 seconds
  }

  // Constructor that sets emit frequency
  public WordCount(Integer frequency) {
      emitFrequency=frequency;
  }

  //Configure frequency of tick tuples for this bolt
  //This delivers a 'tick' tuple on a specific interval,
  //which is used to trigger certain actions
  @Override
  public Map<String, Object> getComponentConfiguration() {
      Config conf = new Config();
      conf.put(Config.TOPOLOGY_TICK_TUPLE_FREQ_SECS, emitFrequency);
      return conf;
  }

  //execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //If it's a tick tuple, emit all words and counts
    if(tuple.getSourceComponent().equals(Constants.SYSTEM_COMPONENT_ID)
            && tuple.getSourceStreamId().equals(Constants.SYSTEM_TICK_STREAM_ID)) {
      for(String word : counts.keySet()) {
        Integer count = counts.get(word);
        collector.emit(new Values(word, count));
        logger.info("Emitting a count of " + count + " for word " + word);
      }
    } else {
      //Get the word contents from the tuple
      String word = tuple.getString(0);
      //Have we counted any already?
      Integer count = counts.get(word);
      if (count == null)
        count = 0;
      //Increment the count and store it
      count++;
      counts.put(word, count);
    }
  }

  //Declare that this emits a tuple containing two fields; word and count
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word", "count"));
  }
}
```

### <a name="define-the-topology"></a>A topológia meghatározása

A topológia a spoutokat és a csavarokat egy grafikonhoz köti össze, amely meghatározza az összetevők közötti adatfolyamokat. Azt is biztosítja a párhuzamosság tippeket, hogy a Storm használ példányai nak létrehozásakor az összetevők a fürtön belül.

Az alábbi kép a topológiához szükséges összetevők grafikonjának alapdiagramja.

![a kifolyók és csavarok elrendezését megjelenítő ábra](./media/apache-storm-develop-java-topology/word-count-topology1.png)

A topológia megvalósításához írja be az alábbi parancsot egy új fájl `WordCountTopology.java`létrehozásához és megnyitásához:

```cmd
notepad src\main\java\com\microsoft\example\WordCountTopology.java
```

Ezután másolja be az alábbi java kódot az új fájlba.  Ezután zárja be a fájlt.

```java
package com.microsoft.example;

import org.apache.storm.Config;
import org.apache.storm.LocalCluster;
import org.apache.storm.StormSubmitter;
import org.apache.storm.topology.TopologyBuilder;
import org.apache.storm.tuple.Fields;

import com.microsoft.example.RandomSentenceSpout;

public class WordCountTopology {

  //Entry point for the topology
  public static void main(String[] args) throws Exception {
  //Used to build the topology
    TopologyBuilder builder = new TopologyBuilder();
    //Add the spout, with a name of 'spout'
    //and parallelism hint of 5 executors
    builder.setSpout("spout", new RandomSentenceSpout(), 5);
    //Add the SplitSentence bolt, with a name of 'split'
    //and parallelism hint of 8 executors
    //shufflegrouping subscribes to the spout, and equally distributes
    //tuples (sentences) across instances of the SplitSentence bolt
    builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
    //Add the counter, with a name of 'count'
    //and parallelism hint of 12 executors
    //fieldsgrouping subscribes to the split bolt, and
    //ensures that the same word is sent to the same instance (group by field 'word')
    builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

    //new configuration
    Config conf = new Config();
    //Set to false to disable debug information when
    // running in production on a cluster
    conf.setDebug(false);

    //If there are arguments, we are running on a cluster
    if (args != null && args.length > 0) {
      //parallelism hint to set the number of workers
      conf.setNumWorkers(3);
      //submit the topology
      StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
    }
    //Otherwise, we are running locally
    else {
      //Cap the maximum number of executors that can be spawned
      //for a component to 3
      conf.setMaxTaskParallelism(3);
      //LocalCluster is used to run locally
      LocalCluster cluster = new LocalCluster();
      //submit the topology
      cluster.submitTopology("word-count", conf, builder.createTopology());
      //sleep
      Thread.sleep(10000);
      //shut down the cluster
      cluster.shutdown();
    }
  }
}
```

### <a name="configure-logging"></a>Naplózás konfigurálása

Storm az [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) segítségével naplózza az adatokat. Ha nem konfigurálja a naplózást, a topológia diagnosztikai információkat bocsát ki. A naplózott fájlok szabályozásához `log4j2.xml` hozzon `resources` létre egy, a könyvtárban megnevezett fájlt az alábbi parancs megadásával:

```cmd
notepad resources\log4j2.xml
```

Ezután másolja és illessze be az alábbi XML-szöveget az új fájlba.  Ezután zárja be a fájlt.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
    <Appenders>
        <Console name="STDOUT" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
    </Appenders>
    <Loggers>
        <Logger name="com.microsoft.example" level="trace" additivity="false">
            <AppenderRef ref="STDOUT"/>
        </Logger>
        <Root level="error">
            <Appender-Ref ref="STDOUT"/>
        </Root>
    </Loggers>
</Configuration>
```

Ez az XML egy új `com.microsoft.example` naplózót konfigurál az osztályhoz, amely tartalmazza a példatopológiában szereplő összetevőket. A szint nyomon követésére van beállítva ehhez a naplózóhoz, amely rögzíti az ebben a topológiában az összetevők által kibocsátott naplózási adatokat.

A `<Root level="error">` szakasz úgy konfigurálja a naplózás `com.microsoft.example`gyökérszintjét (minden, ami nincs benne) úgy, hogy csak a hibaadatokat naplózza.

A Log4j 2 naplózásának konfigurálásáról [https://logging.apache.org/log4j/2.x/manual/configuration.html](https://logging.apache.org/log4j/2.x/manual/configuration.html)további információt a.

> [!NOTE]  
> Storm verzió 0.10.0 és újabb használata Log4j 2.x. A Storm régebbi verziói a Log4j 1.x-et használták, amely más formátumot használt a naplókonfigurációhoz. A régebbi konfigurációról a [https://wiki.apache.org/logging-log4j/Log4jXmlFormat](https://wiki.apache.org/logging-log4j/Log4jXmlFormat)témakörben talál további információt.

## <a name="test-the-topology-locally"></a>A topológia helyi tesztelése

A fájlok mentése után a következő paranccsal helyileg tesztelheti a topológiát.

```cmd
mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology
```

Futás közben a topológia megjeleníti az indítási információkat. A következő szöveg egy példa a szavak számának kimenetére:

    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow

Ez a példanapló azt jelzi, hogy az "és" szó 113-szor lett kiadva. A számláló továbbra is felmegy, amíg a topológia fut, mert a kifolyó folyamatosan ugyanazokat a mondatokat bocsátja ki.

Van egy 5 másodperces intervallum a szavak és a számok kibocsátása között. A **WordCount** összetevő úgy van beállítva, hogy csak akkor bocsátjon ki információt, amikor egy tick-tuple megérkezik. Azt kéri, hogy a tick tuples csak öt másodpercenként kézbesítve.

## <a name="convert-the-topology-to-flux"></a>A topológia átalakítása Flux-ra

[Flux](https://storm.apache.org/releases/2.0.0/flux.html) egy új keretrendszer elérhető Storm 0.10.0 és magasabb, amely lehetővé teszi, hogy külön konfiguráció megvalósítása. Az összetevők továbbra is Java-ban vannak definiálva, de a topológia yaml-fájl használatával van definiálva. Az alapértelmezett topológiadefiníciót becsomagolhatja a projekttel, vagy önálló fájlt használhat a topológia elküldésekor. A topológia Storm nak történő beküldésekor környezeti változók vagy konfigurációs fájlok segítségével feltöltheti az értékeket a YAML topológia definíciójában.

A YAML-fájl határozza meg a topológia és a közöttük lévő adatfolyam számára használandó összetevőket. A jar fájl részeként yaml-fájlt is felvehet, vagy használhat külső YAML-fájlt is.

A Fluxról további információt a [Flux keretrendszer ( .https://storm.apache.org/releases/current/flux.html)](https://storm.apache.org/releases/current/flux.html)

> [!WARNING]  
> Hiba miatt [(ahttps://issues.apache.org/jira/browse/STORM-2055) ](https://issues.apache.org/jira/browse/STORM-2055) Storm 1.0.1-es esetén előfordulhat, hogy telepítenie kell egy [Storm-fejlesztői környezetet](https://storm.apache.org/releases/current/Setting-up-development-environment.html) a Flux topológiák helyi futtatásához.

1. Korábban `WordCountTopology.java` definiálta a topológiát, de nincs szükség a Flux. Törölje a fájlt a következő paranccsal:

    ```cmd
    DEL src\main\java\com\microsoft\example\WordCountTopology.java
    ```

1. Adja meg az alábbi parancsot `topology.yaml`egy új fájl létrehozásához és megnyitásához:

    ```cmd
    notepad resources\topology.yaml
    ```

    Ezután másolja be az alábbi szöveget az új fájlba.  Ezután zárja be a fájlt.

    ```yaml
    name: "wordcount"       # friendly name for the topology

    config:                 # Topology configuration
           topology.workers: 1     # Hint for the number of workers to create
  
    spouts:                 # Spout definitions
    - id: "sentence-spout"
           className: "com.microsoft.example.RandomSentenceSpout"
           parallelism: 1      # parallelism hint

    bolts:                  # Bolt definitions
    - id: "splitter-bolt"
           className: "com.microsoft.example.SplitSentence"
           parallelism: 1

    - id: "counter-bolt"
           className: "com.microsoft.example.WordCount"
           constructorArgs:
             - 10
           parallelism: 1

    streams:                # Stream definitions
    - name: "Spout --> Splitter" # name isn't used (placeholder for logging, UI, etc.)
           from: "sentence-spout"       # The stream emitter
           to: "splitter-bolt"          # The stream consumer
           grouping:                    # Grouping type
             type: SHUFFLE

    - name: "Splitter -> Counter"
           from: "splitter-bolt"
           to: "counter-bolt"
           grouping:
             type: FIELDS
             args: ["word"]           # field(s) to group on
    ```

1. Írja be az `pom.xml` alábbi parancsot, hogy nyissa meg, hogy a leírt módosításokat az alábbi:

    ```cmd
    notepad pom.xml
    ```

   1. Adja hozzá a következő `<dependencies>` új függőséget a szakaszban:

        ```xml
        <!-- Add a dependency on the Flux framework -->
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>flux-core</artifactId>
            <version>${storm.version}</version>
        </dependency>
        ```

   1. Adja hozzá a `<plugins>` következő bővítményt a szakaszhoz. Ez a plugin kezeli a létrehozását egy csomag (jar fájl) a projekt, és alkalmazza néhány átalakítások kifejezetten Flux létrehozásakor a csomagot.

        ```xml
        <!-- build an uber jar -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>3.2.1</version>
            <configuration>
                <transformers>
                    <!-- Keep us from getting a "can't overwrite file error" -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer" />
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer" />
                    <!-- We're using Flux, so refer to it as main -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                        <mainClass>org.apache.storm.flux.Flux</mainClass>
                    </transformer>
                </transformers>
                <!-- Keep us from getting a bad signature error -->
                <filters>
                    <filter>
                        <artifact>*:*</artifact>
                        <excludes>
                            <exclude>META-INF/*.SF</exclude>
                            <exclude>META-INF/*.DSA</exclude>
                            <exclude>META-INF/*.RSA</exclude>
                        </excludes>
                    </filter>
                </filters>
            </configuration>
            <executions>
                <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
        ```

   1. Az Exec Maven Plugin szakaszban keresse meg `<configuration>`  >  `<mainClass>` a és módosítsa `${storm.topology}` a . `org.apache.storm.flux.Flux` Ez a beállítás lehetővé teszi, hogy a Flux kezelje a topológia helyi futtatását a fejlesztés során.

   1. A `<resources>` szakaszban adja hozzá `<includes>`a következőt a szakaszhoz. Ez az XML tartalmazza azt a YAML-fájlt, amely a topológiát a projekt részeként definiálja.

        ```xml
        <include>topology.yaml</include>
        ```

## <a name="test-the-flux-topology-locally"></a>A fluxus topológia helyi tesztelése

1. Írja be a következő parancsot a Flux topológia Maven használatával történő fordításához és végrehajtásához:

    ```cmd
    mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    ```

    > [!WARNING]  
    > Ha a topológiája storm 1.0.1 biteket használ, ez a parancs sikertelen lesz. Ezt a hibát [https://issues.apache.org/jira/browse/STORM-2055](https://issues.apache.org/jira/browse/STORM-2055)a okozza. Ehelyett [telepítse a Stormot a fejlesztői környezetben,](https://storm.apache.org/releases/current/Setting-up-development-environment.html) és kövesse az alábbi lépéseket:
    >
    > Ha [a Storm programot telepítette a fejlesztői környezetben,](https://storm.apache.org/releases/current/Setting-up-development-environment.html)a következő parancsokat használhatja:
    >
    > ```cmd
    > mvn compile package
    > storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml
    > ```

    A `--local` paraméter helyi módban futtatja a topológiát a fejlesztői környezetben. A `-R /topology.yaml` paraméter `topology.yaml` a jar fájlból származó fájlerőforrást használja a topológia meghatározásához.

    Futás közben a topológia megjeleníti az indítási információkat. A következő szöveg egy példa a kimenetre:

    ```
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    ```

    10 másodperces késleltetés van a naplózott adatok között.

2. Hozzon létre egy új topológia yaml a projektből.

    1. Írja be az `topology.xml`alábbi parancsot a megnyitáshoz:

    ```cmd
    notepad resources\topology.yaml
    ```

    1. Keresse meg a következő szakaszt, és módosítsa a `10` `5`értékét. Ez a módosítás 10 másodpercről 5-re módosítja a szószámlálások sorozatainak kieresztése közötti időtartamot.  

    ```yaml
    - id: "counter-bolt"
           className: "com.microsoft.example.WordCount"
           constructorArgs:
             - 5
           parallelism: 1  
    ```

    1. A fájl `newtopology.yaml`mentése másként.

3. A topológia futtatásához írja be a következő parancsot:

    ```cmd
    mvn exec:java -Dexec.args="--local resources/newtopology.yaml"
    ```

    Vagy, ha storm a fejlesztési környezetben:

    ```cmd
    storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local resources/newtopology.yaml
    ```

    Ez a `newtopology.yaml` parancs a topológia definícióját használja. Mivel nem tartalmaztuk `compile` a paramétert, a Maven a projekt előző lépésekben beépített verzióját használja.

    A topológia megkezdése után vegye észre, hogy a kibocsátott kötegek közötti `newtopology.yaml`idő a program értékét tükrözően változik. Így láthatja, hogy a beállításokat egy YAML-fájlon keresztül módosíthatja anélkül, hogy újra kellene fordítania a topológiát.

A Flux keretrendszer ezen és egyéb jellemzőiről a [Flux ( .https://storm.apache.org/releases/current/flux.html)](https://storm.apache.org/releases/current/flux.html)

## <a name="trident"></a>Trident

[Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) egy magas szintű absztrakció, amely által nyújtott Storm. Támogatja az állapotalapú feldolgozást. Az elsődleges előnye a Trident, hogy tudja garantálni, hogy minden üzenetet, hogy belép a topológia feldolgozása csak egyszer. A Trident használata nélkül a topológiája csak azt tudja garantálni, hogy az üzenetek feldolgozása legalább egyszer megtörtén. Vannak más különbségek is, például beépített összetevők, amelyek csavarok létrehozása helyett használhatók. Valójában a csavarokat kevésbé általános összetevők, például szűrők, vetítések és függvények helyettesítik.

Trident alkalmazások at lehet létrehozni segítségével Maven projektek. Ugyanazokat az alapvető lépéseket használja, mint korábban a cikkben bemutatottak – csak a kód különbözik. Trident is nem (jelenleg) használható a Flux keret.

A Tridenttel kapcsolatos további információkért tekintse meg a [Trident API áttekintését.](https://storm.apache.org/releases/current/Trident-API-Overview.html)

## <a name="next-steps"></a>Következő lépések

Megtanulta, hogyan hozhat létre Apache Storm topológiát Java használatával. Most megtanulják, hogyan kell:

* [Apache Storm-topológiák telepítése és kezelése a HDInsighton](apache-storm-deploy-monitor-topology-linux.md)

* [C# topológiák fejlesztése apache stormhoz a HDInsighton a Visual Studio használatával](apache-storm-develop-csharp-visual-studio-topology.md)

Az Apache Storm további topológiáit az [Apache Storm példatopológiái a HDInsight-on](apache-storm-example-topology.md)találja.
