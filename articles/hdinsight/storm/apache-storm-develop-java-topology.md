---
title: Az Apache Storm például Java-topológia – Azure HDInsight
description: Ismerje meg az Apache Storm-topológiák létrehozása a Java-példában szószámláló topológia létrehozásával.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
keywords: az Apache storm, az apache storm-példa, a storm java, a storm-topológia példa
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 32a8c81cd7b2078f4866d4c7311ea5ac0d0b3439
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58448297"
---
# <a name="create-an-apache-storm-topology-in-java"></a>Az Apache Storm-topológia létrehozása Java nyelven

Ismerje meg, hogyan hozhat létre egy Java-alapú topológia [Apache Storm](https://storm.apache.org/). Itt hozzon létre egy word-count alkalmazás megvalósító Storm-topológia. Használhat [Apache Maven](https://maven.apache.org/) felépítéséhez és becsomagolásához a projektet. Ezután megtudhatja, hogyan határozza meg a topológia használatával a [Apache Storm fluxus](https://storm.apache.org/releases/2.0.0-SNAPSHOT/flux.html) keretrendszer.

Ebben a dokumentumban a lépések elvégzése után telepítheti a topológia az Apache Storm on HDInsight.

> [!NOTE]  
> A Storm-topológia a jelen dokumentum létrehozott példák egy befejezett verziója érhető el: [ https://github.com/Azure-Samples/hdinsight-java-storm-wordcount ](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount).

## <a name="prerequisites"></a>Előfeltételek

* [Java fejlesztői készlet (JDK) 8-as verzió](https://aka.ms/azure-jdks)

* [Az Apache Maven](https://maven.apache.org/download.cgi) megfelelően [telepített](https://maven.apache.org/install.html) Apache megfelelően.  Maven egy projektet a Java-projektek rendszert hozhat létre.

## <a name="test-environment"></a>Tesztkörnyezet
Az ebben a cikkben használt környezet a Windows 10 rendszerű számítógép volt.  A parancsok végrehajtódtak parancsot, és a különböző fájlok is szerkeszthetők a Jegyzettömb alkalmazásban.

Egy parancssorból adja meg a munkakörnyezet létrehozásához az alábbi parancsokat:

```cmd
mkdir C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Hozzon létre egy Maven-projektet

Adja meg a következő parancsot egy nevű Maven-projekt létrehozása **WordCount**:

```cmd
mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

cd WordCount
mkdir resources
```

Ez a parancs létrehoz egy könyvtárat nevű `WordCount` az aktuális helyen, amely tartalmaz egy alapszintű Maven-projektet. A második parancs módosítja a jelen munkakönyvtárból abba `WordCount`. A harmadik parancs létrehoz egy új könyvtárat `resources`, amelyek később fogja használni.  A `WordCount` könyvtár a következő elemeket tartalmazza:

* `pom.xml`: A Maven-projektet tartalmazza.
* `src\main\java\com\microsoft\example`: Az alkalmazás kódját tartalmazza.
* `src\test\java\com\microsoft\example`: Az alkalmazás tesztek tartalmazza.  

### <a name="remove-the-generated-example-code"></a>Távolítsa el a generált példakód

Törölje a létrehozott tesztelési és alkalmazásfájlok `AppTest.java`, és `App.java` írja be az alábbi parancsokat:

```cmd
DEL src\main\java\com\microsoft\example\App.java
DEL src\test\java\com\microsoft\example\AppTest.java
```

## <a name="add-maven-repositories"></a>Adja hozzá a Maven-adattárak

HDInsight alapul a a Hortonworks Data Platform (HDP), ezért javasoljuk, hogy a Hortonworks tárház használatával töltse le az Apache Storm-projektek függőségeit.  

Nyissa meg `pom.xml` az alábbi parancs beírásával:

```cmd
notepad pom.xml
```

Majd adja hozzá a következő XML formátumú után a `<url> https://maven.apache.org</url>` sor:

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

Maven megadhatók tulajdonságok nevű projekt szintű értékeket. A `pom.xml`, hozzáadása után a következő szöveget a `</repositories>` sor:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--
    This is a version of Storm from the Hortonworks repository that is compatible with HDInsight 3.6.
    -->
    <storm.version>1.1.0.2.6.1.9-1</storm.version>
</properties>
```

Mostantól használhatja ezt az értéket a többi szakasza a `pom.xml`. Például a Storm-összetevők verziója megadásakor használható `${storm.version}` sorszámozás érték helyett.

## <a name="add-dependencies"></a>Függőségek hozzáadása

Adjon hozzá egy függőségi Storm-összetevőket. A `pom.xml`, adja hozzá a következő szöveget a `<dependencies>` szakaszban:

```xml
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-core</artifactId>
    <version>${storm.version}</version>
    <!-- keep storm out of the jar-with-dependencies -->
    <scope>provided</scope>
</dependency>
```

A fordítás során, Maven ezen információk segítségével kereshet `storm-core` a Maven-tárházban. Először tűnik, a helyi számítógépen a tárházban. Ha a fájlokat nem létezik, a Maven a nyilvános Maven tárházból letölti azokat, és tárolja azokat a helyi adattárban.

> [!NOTE]  
> Figyelje meg a `<scope>provided</scope>` sor ebben a szakaszban. Ez a beállítás arra utasítja a maven használatával zárhat ki **storm-core** hoz létre, mert azt a rendszer által biztosított JAR-fájlokat.

## <a name="build-configuration"></a>Konfigurace buildu

Maven beépülő modulok lehetővé teszik a project build fázisa testreszabása. Például módját a projekt fordítását, vagy hogyan Becsomagolhatja azt egy JAR-fájlba. A `pom.xml`, adja hozzá a következő szöveget a fenti közvetlenül a `</project>` sor.

```xml
<build>
    <plugins>
    </plugins>
    <resources>
    </resources>
</build>
```

Ez a szakasz segítségével adja hozzá a beépülő modulok, erőforrások és más build-konfigurációs beállítások. A teljes hivatkozás a `pom.xml` fájlt [ https://maven.apache.org/pom.html ](https://maven.apache.org/pom.html).

### <a name="add-plug-ins"></a>Beépülő modulok hozzáadása

* **Exec Maven bővítménnyel**

    Az Apache Storm-topológiák megvalósítva a Java a [Exec Maven bővítménnyel](https://www.mojohaus.org/exec-maven-plugin/) akkor hasznos, mert lehetővé teszi, hogy a topológia egyszerűen helyileg történő futtatása a fejlesztési környezetet. Adja hozzá a következőt a `<plugins>` szakaszában a `pom.xml` a Exec Maven bővítménnyel-fájl:
    
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

* **Az Apache Maven fordító beépülő modul**

    Egy másik hasznos beépülő modul a [Apache Maven fordító beépülő modul](https://maven.apache.org/plugins/maven-compiler-plugin/), amely fordítási beállításainak módosítására szolgál. Módosítsa a Mavent használja a forrás- és az alkalmazás a Java-verzió.
    
  * A HDInsight __3.4-es vagy korábbi__, állítsa be a forrás és cél a Java-verzió __1.7-es__.
    
  * A HDInsight __3.5__, állítsa be a forrás és cél a Java-verzió __1.8-as__.
    
    Adja hozzá a következő szöveget a `<plugins>` szakaszában a `pom.xml` -fájl az Apache Maven fordító beépülő modult. Ebben a példában 1.8-as, adja meg, így a célverzió HDInsight 3.5-ös verzióját.
    
    ```xml
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>3.3</version>
      <configuration>
      <source>1.8</source>
      <target>1.8</target>
      </configuration>
    </plugin>
    ```

### <a name="configure-resources"></a>Erőforrások konfigurálása

Az erőforrás szakasz lehetővé teszi nem kód erőforrások, például az összetevőket a topológia számára szükséges konfigurációs fájlokat. Ebben a példában adja hozzá a következő szöveget a `<resources>` szakaszában a `pom.xml` fájlt.

```xml
<resource>
    <directory>${basedir}/resources</directory>
    <filtering>false</filtering>
    <includes>
        <include>log4j2.xml</include>
    </includes>
</resource>
```

Ebben a példában az erőforrások könyvtár hozzáadása a projekt gyökérkönyvtárában található (`${basedir}`) olyan erőforrásokat tartalmaz, és a nevű fájlt tartalmazó hely `log4j2.xml`. Ez a fájl a topológia milyen információt naplózta konfigurálására szolgál.

## <a name="create-the-topology"></a>A topológia létrehozása

Java-alapú Apache Storm-topológia áll mindhárom összetevő, amely kell létrehoznia (vagy referencia) függőségként.

* **Spoutok**: Külső forrásból származó adatokat olvasó és bocsát ki mutatnak be a topológia.

* **Boltok**: Spoutok vagy más boltok streameket feldolgozási hajt végre, valamint bocsát ki egy vagy több streamet.

* **Topológia**: Határozza meg, hogyan a spoutok és boltok vannak rendezve, és a topológia a belépési pontot nyújt.

### <a name="create-the-spout"></a>A spout létrehozása

Külső adatforrások beállításával kapcsolatos követelmények csökkentése érdekében a következő spout egyszerűen véletlenszerű mondatokat bocsát ki. A spout-biztosított egy módosított verziója a [Storm-Starter példák](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter).  Bár ez a topológia csak egy spout, mások előfordulhat, hogy több különböző forrásokból származó adatok hírcsatorna-a topológia be.

Adja meg a parancsot hozhat létre, és nyisson meg egy új fájlt az alábbi `RandomSentenceSpout.java`:

```cmd
notepad src\main\java\com\microsoft\example\RandomSentenceSpout.java
```

Ezután másolja és illessze be az alábbi java-kódot az új fájlt.  Zárja be a fájlt.

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
> Példa egy spout, amely beolvas egy külső adatforrásból tekintse meg az alábbi példák egyikét:
>
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): Egy példa spout, amely beolvassa a Twitteren.
> * [Storm-Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): A spout, amely beolvassa a Kafka.


### <a name="create-the-bolts"></a>A boltok létrehozása

A boltok az adatfeldolgozás kezelni. A boltok bármit, például számítási, adatmegőrzés vagy külső összetevők folytatott kommunikációra. Ez a topológia két boltok használja:

* **SplitSentence**: Felosztja a mondatok projektsablon által kibocsátott **RandomSentenceSpout** az egyes szavak.

* **WordCount**: Számít, hogy hányszor történt minden szó.


#### <a name="splitsentence"></a>SplitSentence

Adja meg a parancsot hozhat létre, és nyisson meg egy új fájlt az alábbi `SplitSentence.java`:

```cmd
notepad src\main\java\com\microsoft\example\SplitSentence.java
```

Ezután másolja és illessze be az alábbi java-kódot az új fájlt.  Zárja be a fájlt.

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

#### <a name="wordcount"></a>WordCount

Adja meg a parancsot hozhat létre, és nyisson meg egy új fájlt az alábbi `WordCount.java`:

```cmd
notepad src\main\java\com\microsoft\example\WordCount.java
```

Ezután másolja és illessze be az alábbi java-kódot az új fájlt.  Zárja be a fájlt.

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

A topológia összeköt a spoutok és boltok együttesen, egy grafikon, amely meghatározza, hogy az összetevők közti adatfolyamok. Párhuzamosság mutatók, amelyek a fürtön belül az összetevők példányai létrehozásakor használ a Storm is tartalmazza.

Az alábbi képen a gráf ebben a topológiában az összetevők egy egyszerű diagram.

![spoutok és boltok elrendezésének bemutató ábra.](./media/apache-storm-develop-java-topology/wordcount-topology.png)

A topológia implementálása, adja meg a hozhat létre, és nyisson meg egy új fájlt az alábbi parancs `WordCountTopology.java`:

```cmd
notepad src\main\java\com\microsoft\example\WordCountTopology.java
```

Ezután másolja és illessze be az alábbi java-kódot az új fájlt.  Zárja be a fájlt.

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

Használja a Storm [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) naplózza az adatokat. Ha nem konfigurálja a naplózást, a topológia diagnosztikai adatokat bocsát ki. Szabályozhatja, hogy mi a naplózására akkor kerül sor, hozzon létre egy fájlt `log4j2.xml` a a `resources` könyvtárat, az alábbi parancs beírásával:

```cmd
notepad resources\log4j2.xml
```

Ezután másolja és illessze be az alábbi XML-szöveget az új fájlt.  Zárja be a fájlt.

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

Az XML konfigurálja az új naplózó a `com.microsoft.example` osztály, amely ebben a példában a topológiában a összetevőket tartalmazza. A szint nyomkövetési értéke: a naplózó, amely minden ebben a topológiában összetevők által kibocsátott naplózási információkat rögzíti.

A `<Root level="error">` szakaszban konfigurálja a legfelső szintű a naplózási szint (minden nem `com.microsoft.example`) csak a hibák naplózása.

Log4j 2 naplózásának konfigurálásáról további információkért lásd: [ https://logging.apache.org/log4j/2.x/manual/configuration.html ](https://logging.apache.org/log4j/2.x/manual/configuration.html).

> [!NOTE]  
> Storm-verzió: 0.10.0-s és magasabb használata Log4j 2.x. Régebbi verzióit a storm Log4j használt 1.x, amelyet a különböző naplózási konfiguráció. A régebbi konfigurációs információkért lásd: [ https://wiki.apache.org/logging-log4j/Log4jXmlFormat ](https://wiki.apache.org/logging-log4j/Log4jXmlFormat).

## <a name="test-the-topology-locally"></a>A topológia helyi tesztelése

Után mentse a fájlokat, használja a következő parancsot helyileg a topológia teszteléséhez.

```cmd
mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology
```

Annak futtatása során a topológia indítási információit jeleníti meg. A következő szöveg egy példa a word száma kimenetre:

    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow

Ez a példa a napló azt jelzi, hogy a szó "és" 113 többször lett kibocsátva. A szám továbbra is mindaddig, amíg a topológia fut, mert a spout folyamatosan bocsát ki az azonos mondatokat.

Van egy 5 másodperces időköze kibocsátási szavak és számok között. A **WordCount** összetevő csak gridre bocsáthatja ki az adatokat egy-egy rekordórajelet érkezésekor van konfigurálva. A rekordok csak kézbesítési öt másodpercenként osztásjelek kéri.

## <a name="convert-the-topology-to-flux"></a>A topológia átalakítása fluxus

[Fluxus](https://storm.apache.org/releases/2.0.0-SNAPSHOT/flux.html) egy új keretrendszer érhető el, a Storm 0.10.0-s vagy újabb, amely lehetővé teszi, hogy külön konfigurációt az implementáció. Az összetevők továbbra is vannak meghatározva a Java, de a topológia van definiálva egy YAML-fájllal. Egy alapértelmezett topológia definíciójának csomagot a projekthez, vagy egy önálló fájlt használja, a topológia elküldésekor. A Storm-topológia elküldésekor környezeti változókat vagy konfigurációs fájlok használatával töltse fel a YAML-topológia definíciójának értékeit.

A YAML-fájl határozza meg a topológia és az adatok az összetevők közötti őket. Megadhat egy YAML-fájlt a jar-fájl részeként, vagy egy külső YAML-fájlt is használhat.

Fluxus további információkért lásd: [fluxus keretrendszer (https://storm.apache.org/releases/1.0.6/flux.html)](https://storm.apache.org/releases/1.0.6/flux.html).

> [!WARNING]  
> Oka az, hogy egy [bug (https://issues.apache.org/jira/browse/STORM-2055) ](https://issues.apache.org/jira/browse/STORM-2055) a Storm 1.0.1, előfordulhat, hogy telepítenie kell egy [Storm fejlesztési környezet](https://storm.apache.org/releases/current/Setting-up-development-environment.html) fluxus topológiák helyi futtatásához.

1. Korábban a `WordCountTopology.java` a topológia definiálva, de nem szükséges fluxus. Törölje a fájlt a következő paranccsal:

    ```cmd
    DEL src\main\java\com\microsoft\example\WordCountTopology.java
    ```

2. Adja meg a parancsot hozhat létre, és nyisson meg egy új fájlt az alábbi `topology.yaml`:

    ```cmd
    notepad resources\topology.yaml
    ```

    Ezután másolja és illessze be az alábbi szöveget egy új fájlba.  Zárja be a fájlt.

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

3. Adja meg az alábbi parancsot megnyitásához `pom.xml` , hogy az alább ismertetett változatok:

    ```cmd
    notepad pom.xml
    ```

   * Adja hozzá a következő új függőséget a `<dependencies>` szakaszban:

        ```xml
        <!-- Add a dependency on the Flux framework -->
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>flux-core</artifactId>
            <version>${storm.version}</version>
        </dependency>
        ```

   * Adja hozzá a következő beépülő modult a `<plugins>` szakaszban. Ez a beépülő modul egy csomagot (jar-fájl), a projekt létrehozásának kezeli, és meghatározott átalakítások fluxus érvényes, ha a csomag létrehozása.

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

   * Az a **exec-maven-bővítménnyel** `<configuration>` területen módosítsa az értéket a `<mainClass>` a `${storm.topology}` való `org.apache.storm.flux.Flux`. Ezzel a beállítással fluxus a topológia helyben fut a fejlesztési kezelésére.

   * Az a `<resources>` területen adja hozzá a következőt `<includes>`. Az XML tartalmazza a YAML-fájlt, amely meghatározza a topológia a projekt részeként.

        ```xml
        <include>topology.yaml</include>
        ```

## <a name="test-the-flux-topology-locally"></a>Tesztelje helyben a fluxus topológia

1. Adja meg a fordításához és a Maven használatával szeretné fluxus topológia hajtsa végre a következő parancsot:

    ```cmd
    mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    ```

    > [!WARNING]  
    > Ha a topológia a Storm 1.0.1 BITS szolgáltatást használja, ez a parancs sikertelen lesz. Ez a hiba okozza [ https://issues.apache.org/jira/browse/STORM-2055 ](https://issues.apache.org/jira/browse/STORM-2055). Ehelyett [Storm telepítse a fejlesztési környezet](https://storm.apache.org/releases/current/Setting-up-development-environment.html) , és kövesse az alábbi lépéseket:
    >
    > Ha rendelkezik [Storm a fejlesztési környezetben telepített](https://storm.apache.org/releases/current/Setting-up-development-environment.html), használhatja helyette az alábbi parancsokat:
    >
    > ```cmd
    > mvn compile package
    > storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml
    > ```

    A `--local` paraméter fut a topológia helyi módban a fejlesztési környezetet. A `-R /topology.yaml` paraméter használja a `topology.yaml` erőforrás fájlt a jar-fájlt a topológia meghatározása.

    Annak futtatása során a topológia indítási információit jeleníti meg. A következő egy példa a kimenetre a következő szöveget:

        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
        17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs

    Van egy 10 másodperces késleltetést kötegek naplózott adatok között.

2. Hozzon létre egy új topológia yaml a projektből.
 
    a. Adja meg az alábbi parancsot megnyitásához `topology.xml`:

    ```cmd
    notepad resources\topology.yaml
    ```

    b. Keresse meg a következő szakaszban, és módosítsa az értékét `10` való `5`. Ez a módosítás kibocsátását váró word száma 10 másodperc és 5 közötti időtartam változik.  

    ```yaml
    - id: "counter-bolt"
      className: "com.microsoft.example.WordCount"
      constructorArgs:
        - 5
      parallelism: 1  
    ```  

    c. Fájl mentése másként `newtopology.yaml`.

3. A topológia futtatásához adja meg a következő parancsot:

    ```cmd
    mvn exec:java -Dexec.args="--local resources/newtopology.yaml"
    ```

    Vagy, ha a fejlesztési környezetet a Storm rendelkezik:

    ```cmd
    storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local resources/newtopology.yaml
    ```

     Ez a parancs a `newtopology.yaml` , a topológiadefiníció. Mivel nem tartalmazza a `compile` paramétert, a Maven az előző lépésekben létrehozott projekt a verzióját használja.

    A topológia megkezdéséről kell tapasztalnia, hogy a kibocsátott kötegek közötti idő értékének megfelelően módosult `newtopology.yaml`. Így láthatja, hogy módosíthatja a konfigurációt keresztül egy YAML-fájlt a topológia újrafordítottuk nélkül.

Ezeket és más szolgáltatások fluxus keretrendszer további információkért lásd: [fluxus (https://storm.apache.org/releases/current/flux.html)](https://storm.apache.org/releases/current/flux.html).

## <a name="trident"></a>A Trident

[A Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) egy magas szintű absztrakció, Storm által biztosított. Állapotalapú feldolgozás támogatja. Az elsődleges Trident előnye, hogy azt tud garantálni, hogy a topológia összes üzenetet csak egyszer dolgozza fel. A Trident nélkül, a topológia is csak garantálja, hogy üzenetek feldolgozása során legalább egyszer. Például a beépített összetevők boltok létrehozása helyett használható egyéb különbségek is vannak. Valójában a boltok kevesebb az általános összetevők, például a szűrőket, a leképezések és a functions helyébe lép.

Trident alkalmazások Maven projects használatával hozható létre. A cikk korábbi részeiben bemutatott egyszerű lépéseket használhatja – csak a kód nem egyezik. A Trident is (jelenleg) csak akkor használható a fluxus keretrendszer.

A Trident kapcsolatos további információkért lásd: a [Trident API – áttekintés](https://storm.apache.org/releases/current/Trident-API-Overview.html).

## <a name="next-steps"></a>További lépések

Megtanulhatta, hogyan lehet az Apache Storm-topológia létrehozása Java használatával. Most megtudhatja, hogyan lehet:

* [Üzembe helyezés és kezelés a HDInsight Apache Storm-topológiák](apache-storm-deploy-monitor-topology-linux.md)

* [Az Apache Storm on HDInsight Visual Studio használatával C#-topológiák fejlesztése](apache-storm-develop-csharp-visual-studio-topology.md)

Annak további példa az Apache Storm-topológiák funkcionáló [a HDInsight Apache Storm példatopológiái](apache-storm-example-topology.md).
