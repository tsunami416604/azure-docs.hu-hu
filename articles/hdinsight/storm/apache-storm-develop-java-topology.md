---
title: "Apache Storm például Java-topológiák - Azure HDInsight |} Microsoft Docs"
description: "Útmutató Apache Storm-topológiák létrehozása a Java hozzon létre egy példa a word-count topológiához."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
keywords: "Apache storm, apache storm például storm java, a storm topológia – példa"
ms.assetid: a8838f29-9c08-4fd9-99ef-26655d1bf6d7
ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/20/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 2403261f05d9e5aab2e50939720b3eb007aecd6e
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2018
---
# <a name="create-an-apache-storm-topology-in-java"></a>Hozzon létre egy Apache Storm-topológia a Java nyelven

Útmutató az Apache Storm egy Java-alapú topológiák létrehozását is. A Storm-topológia, amely egy word-count alkalmazást hoz létre. Maven használatával hozza létre, és a projekt csomag. Ezt követően megtanulhatja a fluxus keretrendszerrel topológia meghatározása.

A jelen dokumentumban leírt lépések elvégzése után a topológia telepítene a HDInsight alatt futó Apache Storm.

> [!NOTE]
> Ebben a dokumentumban létrehozott Storm-topológia példák befejezett verzióját [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount).

## <a name="prerequisites"></a>Előfeltételek

* [Java fejlesztői készlet (JDK) 8-as verzió](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

* [Maven (https://maven.apache.org/download.cgi)](https://maven.apache.org/download.cgi): Maven rendszer project build Java-projektek esetében.

* Egy szövegszerkesztőben, vagy IDE.

## <a name="configure-environment-variables"></a>Környezeti változók konfigurálása

A következő környezeti változók Java és a JDK telepítésekor lehet beállítani. Azonban ellenőrizni kell, hogy léteznek, illetve a rendszer a megfelelő értékeket tartalmaz.

* **JAVA_HOME** -érdemes mutasson a mappát, ahová a Java-futtatókörnyezet (JRE) telepítve van. Például egy Unix vagy Linux terjesztési nem rendelkezhet hasonló érték `/usr/lib/jvm/java-8-oracle`. A Windows rendszerben kellene hasonló érték `c:\Program Files (x86)\Java\jre1.8`

* **Elérési út** -tartalmaznia kell a következő elérési utak:

  * **JAVA_HOME** (vagy ezzel egyenértékű elérési útja)

  * **JAVA_HOME\bin** (vagy ezzel egyenértékű elérési útja)

  * A mappát, ahová a Maven telepítve van

## <a name="create-a-maven-project"></a>Maven-projekt létrehozása

A parancssorból az alábbi parancs segítségével nevű Maven-projekt létrehozása **WordCount**:

```bash
mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false
```

> [!NOTE]
> Ha a PowerShell használata esetén meg kell helyezze a`-D` az idézőjelek közé foglalt paraméterek.
>
> `mvn archetype:generate "-DarchetypeArtifactId=maven-archetype-quickstart" "-DgroupId=com.microsoft.example" "-DartifactId=WordCount" "-DinteractiveMode=false"`

Ez a parancs létrehoz egy könyvtárat nevű `WordCount` az aktuális helyen, amely tartalmazza egy alapszintű Maven project. A `WordCount` könyvtár a következő elemeket tartalmazza:

* `pom.xml`: A Maven project beállításait tartalmazza.
* `src\main\java\com\microsoft\example`: Az alkalmazás kódját tartalmazza.
* `src\test\java\com\microsoft\example`: Az alkalmazás a tesztek tartalmazza. 

### <a name="remove-the-generated-example-code"></a>A generált kód eltávolítása

A generált vizsgálat és az alkalmazásfájlok törlése:

* **src\test\java\com\microsoft\example\AppTest.java**
* **src\main\java\com\microsoft\example\App.java**

## <a name="add-maven-repositories"></a>Adja hozzá a Maven tárházak

HDInsight Hortonworks Data Platform (HDP) alkalmazásban, alapul, ezért azt javasoljuk, töltse le a függőségek az Apache Storm-projektek a Hortonworks tárház használatával. Az a __pom.xml__ fájlban adja hozzá a következő XML-kód után a `<url>http://maven.apache.org</url>` sor:

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
        <url>http://repo.hortonworks.com/content/repositories/releases/</url>
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
        <url>http://repo.hortonworks.com/content/repositories/jetty-hadoop/</url>
        <layout>default</layout>
    </repository>
</repositories>
```

## <a name="add-properties"></a>Tulajdonságok hozzáadása

Maven lehetővé teszi tulajdonságok nevű projektszintű értékek adhatók meg. Az a __pom.xml__, adja hozzá a következő karakterlánc után a `</repositories>` sor:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--
    This is a version of Storm from the Hortonworks repository that is compatible with HDInsight 3.6.
    -->
    <storm.version>1.1.0.2.6.1.9-1</storm.version>
</properties>
```

Ezután már használhatja ezt az értéket a többi szakasza pedig a `pom.xml`. Például a Storm összetevőiről verzióját meghatározásakor használhatja `${storm.version}` rögzített kódolási érték helyett.

## <a name="add-dependencies"></a>Adja hozzá a függőségek

Vegyen fel egy függőséget Storm-összetevőket. Nyissa meg a `pom.xml` fájlt, és adja hozzá a következő kódot a `<dependencies>` szakasz:

```xml
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-core</artifactId>
    <version>${storm.version}</version>
    <!-- keep storm out of the jar-with-dependencies -->
    <scope>provided</scope>
</dependency>
```

Fordítási időben Maven használja ezt az információt kereséséhez `storm-core` a Maven-tárházban. Először a jelek a tárházban a helyi számítógépen. Ha a fájlokat nem létezik, a Maven letölti azokat a nyilvános Maven tárházból, és tárolja őket a helyi tárházban.

> [!NOTE]
> Figyelje meg a `<scope>provided</scope>` sor ebben a szakaszban. Ez a beállítás közli kizárandó Maven **storm-core** bármely JAR fájlok jönnek létre, mert azt a rendszer biztosítja.

## <a name="build-configuration"></a>Konfiguráció

Maven beépülő modulok lehetővé teszik a létrehozási szakaszokra a projekt testreszabása. Például hogyan fordítását, akkor a projekt vagy csomagolása, a JAR-fájlra. Nyissa meg a `pom.xml` fájlt, és adja hozzá a következő kódot közvetlenül a fenti a `</project>` sor.

```xml
<build>
    <plugins>
    </plugins>
    <resources>
    </resources>
</build>
```

Ez a szakasz segítségével adja hozzá a beépülő modulok, erőforrások és egyéb build-konfigurációs beállítások. A teljes körű referenciáért a **pom.xml** fájl című [http://maven.apache.org/pom.html](http://maven.apache.org/pom.html).

### <a name="add-plug-ins"></a>Beépülő modulok hozzáadása

Az Apache Storm-topológiák megvalósított Java a [Exec Maven beépülő modul](http://www.mojohaus.org/exec-maven-plugin/) akkor hasznos, mivel lehetővé teszi a topológia könnyen helyi futtatásához a fejlesztési környezetet. Adja hozzá a következőt a `<plugins>` szakasza a `pom.xml` a beépülő modul Exec Maven-fájl:

```xml
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>exec-maven-plugin</artifactId>
    <version>1.5.0</version>
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

Egy másik hasznos beépülő modul a [Apache Maven fordító beépülő modul](http://maven.apache.org/plugins/maven-compiler-plugin/), amellyel fordítási beállítások módosítása. A módosítások a Java verzióját használó Maven a forrás és cél az alkalmazás.

* A HDInsight __3.4 vagy korábbi__, állítsa be a forrás és cél a Java-verziót __1.7__.

* A HDInsight __3.5__, állítsa be a forrás és cél a Java-verziót __1.8__.

Adja hozzá a következő szöveget a `<plugins>` szakasza a `pom.xml` fájlt a az Apache Maven fordító beépülő modul. Ez a példa 1.8, határozza meg, így a célverzió HDInsight 3.5-ös verzióját.

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

Az erőforrások szakasz lehetővé teszi, hogy nem kód erőforrások, például a topológia összetevők által igényelt konfigurációs fájlokat. Ennél a példánál adja hozzá a következő szöveget a `<resources>` szakaszában a "pom.xml fájlt.

```xml
<resource>
    <directory>${basedir}/resources</directory>
    <filtering>false</filtering>
    <includes>
        <include>log4j2.xml</include>
    </includes>
</resource>
```

Ebben a példában az erőforrások könyvtárat ad a projekt gyökérkönyvtárában (`${basedir}`) olyan erőforrásokat tartalmaz, és a fájlt tartalmazó helyként `log4j2.xml`. Ez a fájl által a topológia a rendszer milyen információkat naplózza konfigurálására szolgál.

## <a name="create-the-topology"></a>A topológia létrehozása

A Java-alapú Apache Storm-topológia áll meg kell írni három összetevő (vagy hivatkozás) a függőség beállításához.

* **Spoutok**: olvassa be a külső adatokat adatforrásokat, és megfelelően kibocsát adatstreamek azokat a topológia.

* **Boltok**: feldolgozási végez spoutokkal kapcsolatban, vagy más boltokhoz által kibocsátott adatfolyamokat, és megfelelően kibocsát egy vagy több adatfolyamokat.

* **Topológia**: hogyan a spoutokkal kapcsolatban és boltokhoz vannak rendezve, és a belépési pontot nyújt az topológia meghatározása.

### <a name="create-the-spout"></a>A spout létrehozása

Külső adatforrások beállításával kapcsolatos követelmények csökkentése érdekében a következő spout egyszerűen véletlenszerű mondat bocsát ki. A mellékelt spout módosított változatát a [Storm-kezdőpéldák](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter).

> [!NOTE]
> Példa egy egy külső adatforrásból olvasó spout tekintse meg az alábbi példák egyikét:
>
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): egy példa spout, amely Twitter olvassa be
> * [A Storm-Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): egy spout, amely Kafka olvassa be

Hozzon létre egy fájlt a spout `RandomSentenceSpout.java` a a `src\main\java\com\microsoft\example` könyvtárra, és használja a következő Java kód tartalma:

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
> Bár ez a topológia csak egy spout, mások is rendelkezhet, amely adatokat különböző forrásokból történő a topológia több.

### <a name="create-the-bolts"></a>A boltokhoz létrehozása

Szögek kezelni az adatok feldolgozása. Ez a topológia két boltokhoz használja:

* **SplitSentence**: felosztja a mondatok által kibocsátott **RandomSentenceSpout** az egyes szavakat.

* **WordCount**: hányszor történt minden szó található.

> [!NOTE]
> Szögek is végrehajthat, például számítási, adatmegőrzés vagy a külső összetevőkre van szó.

Hozzon létre két új fájl `SplitSentence.java` és `WordCount.java` a a `src\main\java\com\microsoft\example` könyvtár. A fájlok használata a tartalom a következő szöveget:

#### <a name="splitsentence"></a>SplitSentence

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

A topológia kötelékek a spoutokkal kapcsolatban, és a grafikon, amely meghatározza, hogyan közötti adatáramlás a összetevők együttesen boltok. Párhuzamossági mutatók Storm használó a fürtön belül összetevők példányai létrehozásakor is tartalmazza.

Az alábbi képen a grafikon az ebben a topológiában az összetevők egyszerű diagram.

![a spoutokkal kapcsolatban és boltokhoz elrendezéssel bemutató ábra](./media/apache-storm-develop-java-topology/wordcount-topology.png)

A topológia alkalmazásához hozzon létre egy fájlt `WordCountTopology.java` a a `src\main\java\com\microsoft\example` könyvtár. A fájl tartalmát az alábbira Java használata:

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

A Storm az Apache Log4j használatával naplózza az információkat. Ha nem konfigurálja a naplózást, a topológia diagnosztikai adatokat bocsát ki. Szabályozhatja, hogy mi a naplózására akkor kerül sor, hozzon létre egy fájlt `log4j2.xml` a a `resources` könyvtár. Az alábbi XML-fájl használata a fájl tartalmát.

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

Az XML számára egy új naplózó konfigurálja a `com.microsoft.example` osztályt, amely ebben a példában topológiában a összetevőket tartalmazza. A szintje nyomkövetési a a tranzakciónaplókat tartalmazó, amely minden ebben a topológiában-összetevők által kibocsátott naplózási információkat rögzíti.

A `<Root level="error">` szakasz konfigurálja a naplózási gyökérszinten (mindent nem szereplő `com.microsoft.example`) csak a hibák naplózása.

Log4j naplózásának konfigurálásáról további információkért lásd: [http://logging.apache.org/log4j/2.x/manual/configuration.html](http://logging.apache.org/log4j/2.x/manual/configuration.html).

> [!NOTE]
> Storm verzióját 0.10.0-s és magasabb használata Log4j 2.x. A storm régebbi verzióit használja Log4j 1.x, a naplózási konfiguráció más formátumú használt. A régebbi konfigurációtól tudnivalókért lásd: [http://wiki.apache.org/logging-log4j/Log4jXmlFormat](http://wiki.apache.org/logging-log4j/Log4jXmlFormat).

## <a name="test-the-topology-locally"></a>A topológia helyi tesztelése

A fájlok mentése után a következő paranccsal helyileg a topológia teszteléséhez.

```bash
mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology
```

Futtatja, a topológia indítási információit jeleníti meg. A következő szöveget a word-count kimeneti példája:

    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow

Ez a példa napló azt jelzi, hogy a word "és" 113 alkalommal kibocsátását. A szám továbbra is, amíg a topológia fut, mert a spout folyamatosan ugyanazt a mondatok bocsát ki.

Egy 5 másodperces időköze van szó kibocsátási és a számok között. A **WordCount** összetevő adatok csak létrehozása egy rekordot érkezésekor van konfigurálva. Adott listának csak kézbesítési öt másodpercenként osztásjelek kéri.

## <a name="convert-the-topology-to-flux"></a>A topológia átalakítása fluxus

Fluxus egy új keretrendszer elérhető Storm 0.10.0-s vagy újabb, amely lehetővé teszi a megvalósítás konfigurációja külön. Az összetevők továbbra is Java vannak definiálva, de a topológia definíciója YAM-fájllal. Csomagdefiníció egy alapértelmezett topológia a projektet, vagy egy önálló fájlt használja, amikor a topológiához. A topológia a Storm elküldésekor segítségével környezeti változókat vagy konfigurációs fájlok feltöltése az YAM-topológia definícióban szereplő értékek.

A YAM fájl határozza meg a topológia és az adatok az összetevők közötti folyamat. Megadhat egy YAM-fájl részeként a jar-fájlra, vagy egy külső YAM-fájl használatával.

Fluxus további információkért lásd: [fluxus keretrendszer (https://storm.apache.org/releases/0.10.0/flux.html)](https://storm.apache.org/releases/0.10.0/flux.html).

> [!WARNING]
> Oka az, hogy egy [hiba (https://issues.apache.org/jira/browse/STORM-2055)](https://issues.apache.org/jira/browse/STORM-2055) Storm 1.0.1-es, előfordulhat, hogy telepíteni szeretné a [Storm fejlesztőkörnyezet](https://storm.apache.org/releases/1.0.1/Setting-up-development-environment.html) fluxus topológiák helyi futtatásához.

1. Helyezze át a `WordCountTopology.java` fájlt a projekt kívüli. Korábban ezt a fájlt a topológia definiálva, de a fluxus nem szükséges.

2. Az a `resources` könyvtár, hozzon létre egy fájlt `topology.yaml`. Ez a fájl tartalmát a következő szöveg használható.

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

3. A következő módosításokat a `pom.xml` fájlt.
   
   * Adja hozzá a következő új függőséghez a `<dependencies>` szakasz:
     
        ```xml
        <!-- Add a dependency on the Flux framework -->
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>flux-core</artifactId>
            <version>${storm.version}</version>
        </dependency>
        ```
   * Adja hozzá a következő beépülő modult a `<plugins>` szakasz. A beépülő modul a létrehozása a projekthez (jar-fájlt) csomag kezeli, és néhány meghatározott átalakítások fluxus alkalmazza, ha a csomag létrehozása.
     
        ```xml
        <!-- build an uber jar -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>2.3</version>
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

   * Az a **exec-maven-beépülő modul** `<configuration>` területen módosítsa az értéket a `<mainClass>` való `org.apache.storm.flux.Flux`. Ez a beállítás lehetővé teszi, hogy a kezelni a topológia helyben fut a fejlesztési fluxus.

   * Az a `<resources>` területen írja be a következőt a `<includes>`. Az XML-kód tartalmazza a YAM fájlt határozza meg a topológia a projekt részeként.

        ```xml
        <include>topology.yaml</include>
        ```

## <a name="test-the-flux-topology-locally"></a>A fluxus topológia helyi tesztelése

1. Használja a következő fordításához és a fluxus topológia Maven használatával hajtható végre:

    ```bash
    mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    ```

    Ha a PowerShell használata esetén a következő paranccsal:

    ```bash
    mvn compile exec:java "-Dexec.args=--local -R /topology.yaml"
    ```

    > [!WARNING]
    > Ha a topológia a Storm 1.0.1-es bits használ, ez a parancs sikertelen lesz. Ez a hiba oka [https://issues.apache.org/jira/browse/STORM-2055](https://issues.apache.org/jira/browse/STORM-2055). Ehelyett [Storm a fejlesztési környezet telepítése](http://storm.apache.org/releases/0.10.0/Setting-up-development-environment.html) és kövesse az alábbi lépéseket:
    >
    > Ha rendelkezik [Storm a fejlesztési környezetben telepített](http://storm.apache.org/releases/0.10.0/Setting-up-development-environment.html), ehelyett a következő parancsokat használhatja:
    >
    > ```bash
    > mvn compile package
    > storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml
    > ```

    A `--local` paraméter fut a topológia helyi módban a fejlesztési környezetet. A `-R /topology.yaml` paramétert használja a `topology.yaml` erőforrás fájlt a jar-fájlra a topológia meghatározása.

    Futtatja, a topológia indítási információit jeleníti meg. A következő szöveget a kimeneti példája:

        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
        17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs

    Van egy 10 másodperces késleltetési kötegek naplózott adatok között.

2. Másolatot készít a `topology.yaml` fájlt a projektben. Nevezze el az új fájlt `newtopology.yaml`. Az a `newtopology.yaml` fájlt, keresse meg a következő szakaszt, és módosítsa az értéket a `10` való `5`. Ez a módosítás a kibocsátás kötegekben word számok 10 másodperc 5 közötti távolság változik.

    ```yaml
    - id: "counter-bolt"
    className: "com.microsoft.example.WordCount"
    constructorArgs:
    - 5
    parallelism: 1
    ```yaml

3. To run the topology, use the following command:

    ```bash
    mvn exec:java -Dexec.args="--local /path/to/newtopology.yaml"
    ```

    Vagy ha a fejlesztési környezet Storm rendelkezik:

    ```bash
    storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local /path/to/newtopology.yaml
    ```

    Módosítsa a `/path/to/newtopology.yaml` elérési útját az előző lépésben létrehozott newtopology.yaml fájlt. Ez a parancs a newtopology.yaml használja, mint a topológia meghatározása. Mivel jelenleg nem tartozik a `compile` paraméter, a Maven az előző lépésben létrehozott projekt verzióját használja.

    Ha a topológia elindul, kell figyelje meg, hogy a kibocsátott kötegek között eltelő idő megfelelően newtopology.yaml értéke megváltozott. Így tudja módosítani a konfigurációs fájl YAM anélkül, hogy le kell fordítani a topológia látható.

Ezeket és más szolgáltatások fluxus keretrendszer további információkért lásd: [fluxus (https://storm.apache.org/releases/0.10.0/flux.html)](https://storm.apache.org/releases/0.10.0/flux.html).

## <a name="trident"></a>Trident

Trident egy magas szintű absztrakció, Storm által biztosított. Állapot-nyilvántartó feldolgozási támogatja. Az elsődleges Trident előnye, hogy azt is garantálja, hogy a topológia minden üzenetet csak egyszer dolgozza fel. A Trident ezzel szemben nélkül a topológia is csak garantálja, hogy az üzenetek legalább egyszer fel. Is más különbségek vannak, például a beépített összetevők boltokhoz létrehozása helyett használható. Valójában boltokhoz kevésbé általános összetevők, például a szűrőket, a leképezések és a funkciók helyébe lép.

Trident alkalmazások Maven-projektek használatával is létrehozható. Az alapvető lépéseket használja, az ebben a cikkben bemutatott – csak a kód nem egyezik. Trident is nem (jelenleg) használható fluxus keretében.

A Trident kapcsolatos további információkért tekintse meg a [Trident API – áttekintés](http://storm.apache.org/documentation/Trident-API-Overview.html).

## <a name="next-steps"></a>További lépések

Rendelkezik megtudta, hogyan hozhat létre a Storm-topológia Java használatával. Most megtudhatja, hogyan:

* [Központi telepítése és kezelése a HDInsight alatt futó Apache Storm-topológiák](apache-storm-deploy-monitor-topology.md)

* [Visual Studio használatával HDInsight alatt futó Apache Storm a C#-topológiák fejlesztése](apache-storm-develop-csharp-visual-studio-topology.md)

Található további példa Storm-topológiák ellátogatva [a HDInsight alatt futó Storm](apache-storm-example-topology.md).

