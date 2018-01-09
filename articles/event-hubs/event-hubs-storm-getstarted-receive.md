---
title: "Események fogadása az Azure Event Hubs Apache Storm használatának |} Microsoft Docs"
description: "Fogadását az Event Hubs Apache Storm használatának első lépései"
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: java
ms.devlang: multiple
ms.topic: article
ms.date: 08/15/2017
ms.author: sethm
ms.openlocfilehash: 5a120cc38b8ecfc6ec29be35eda12978ca8b035e
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2018
---
# <a name="receive-events-from-event-hubs-using-apache-storm"></a>Események fogadása Apache Storm használatának az Event Hubs

[Apache Storm](https://storm.incubator.apache.org) egy elosztott, valós idejű számítási rendszer, amely egyszerűbbé teszi a megbízható unbounded adatstreamek feldolgozására. Ez a szakasz bemutatja, hogyan használható az Azure Event Hubs Storm spout események fogadásához az Event Hubs. Apache Storm használatának fel események különböző csomópontokon üzemelnek folyamatok között. Az Event Hubs integráció Storm leegyszerűsíti események felhasználásához transzparens módon ellenőrzőpontok használata a telepítés előrehaladását, Storm tartozó Zookeeper telepítést használ, kezeli az állandó ellenőrzőpontokat és párhuzamos fogadásokat az Event Hubs.

További információ az Event Hubs minták kap, tekintse meg a [Event Hubs – áttekintés][Event Hubs overview].

## <a name="create-project-and-add-code"></a>Projekt létrehozása, és adja hozzá a kódot

Ez az oktatóanyag használja egy [HDInsight alatt futó Storm] [ HDInsight Storm] telepítés, amely az Event Hubs spout már elérhető.

1. Kövesse a [HDInsight alatt futó Storm - első lépések](../hdinsight/storm/apache-storm-overview.md) eljárással hozzon létre egy új HDInsight-fürtöt, és a távoli asztalon keresztül kapcsolódni hozzá.
2. Másolás a `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` fájlt a helyi fejlesztési környezetet. Az események-storm-spout tartalmazza.
3. A következő paranccsal telepítse a csomagot a helyi tárolójába Maven. Ez lehetővé teszi, hogy vegye fel a Storm projekt egy későbbi lépésben hivatkozásként.

    ```shell
    mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar
    ```
4. Az eclipse-ben hozzon létre egy új Maven project (kattintson **fájl**, majd **új**, majd **projekt**).
   
    ![][12]
5. Válassza ki **alapértelmezett munkaterület helyet**, majd kattintson a **következő**
6. Válassza ki a **maven-archetype – gyors üzembe helyezés** archetype, majd kattintson a **következő**
7. Helyezze be a **GroupId** és **artifactid szakaszát**, majd kattintson **Befejezés**
8. A **pom.xml**, adja hozzá a következő függőségek a `<dependency>` csomópont.

    ```xml  
    <dependency>
        <groupId>org.apache.storm</groupId>
        <artifactId>storm-core</artifactId>
        <version>0.9.2-incubating</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>com.microsoft.eventhubs</groupId>
        <artifactId>eventhubs-storm-spout</artifactId>
        <version>0.9</version>
    </dependency>
    <dependency>
        <groupId>com.netflix.curator</groupId>
        <artifactId>curator-framework</artifactId>
        <version>1.3.3</version>
        <exclusions>
            <exclusion>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
            </exclusion>
            <exclusion>
                <groupId>org.slf4j</groupId>
                <artifactId>slf4j-log4j12</artifactId>
            </exclusion>
        </exclusions>
        <scope>provided</scope>
    </dependency>
    ```

9. Az a **src** mappa, nevű fájl létrehozása **Config.properties** , és másolja a következő tartalmat, és a `receive rule key` és `event hub name` értékeket:

    ```java
    eventhubspout.username = ReceiveRule
    eventhubspout.password = {receive rule key}
    eventhubspout.namespace = ioteventhub-ns
    eventhubspout.entitypath = {event hub name}
    eventhubspout.partitions.count = 16
       
    # if not provided, will use storm's zookeeper settings
    # zookeeper.connectionstring=localhost:2181
       
    eventhubspout.checkpoint.interval = 10
    eventhub.receiver.credits = 10
    ```
    A következő **eventhub.receiver.credits** határozza meg, hány események vannak kötegelni azokat a Storm-feldolgozási folyamat feloldása előtt. Az egyszerűség kedvéért ebben a példában ez az érték 10 állítja be. Éles akkor általában meg magasabb értékkel; például: 1024.
10. Hozzon létre egy új osztályt **LoggerBolt** az alábbi kódra:
    
    ```java
    import java.util.Map;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import backtype.storm.task.OutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichBolt;
    import backtype.storm.tuple.Tuple;
    
    public class LoggerBolt extends BaseRichBolt {
        private OutputCollector collector;
        private static final Logger logger = LoggerFactory
                  .getLogger(LoggerBolt.class);
    
        @Override
        public void execute(Tuple tuple) {
            String value = tuple.getString(0);
            logger.info("Tuple value: " + value);
   
            collector.ack(tuple);
        }
   
        @Override
        public void prepare(Map map, TopologyContext context, OutputCollector collector) {
            this.collector = collector;
            this.count = 0;
        }
        
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
            // no output fields
        }
    
    }
    ```
    
    A Storm bolt naplózza a fogadott események a tartalmát. Ez egyszerűen bővíthető rekordokat tárolni egy tároló szolgáltatást. A [Eseményközpont például a HDInsight alatt futó Storm] ezt a módszert használja az adatok tárolásához Azure Storage és a Power bi-ban.
11. Hozzon létre egy osztályt **LogTopology** az alábbi kódra:
    
    ```java
    import java.io.FileReader;
    import java.util.Properties;
    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.generated.StormTopology;
    import backtype.storm.topology.TopologyBuilder;
    import com.microsoft.eventhubs.samples.EventCount;
    import com.microsoft.eventhubs.spout.EventHubSpout;
    import com.microsoft.eventhubs.spout.EventHubSpoutConfig;
        
    public class LogTopology {
        protected EventHubSpoutConfig spoutConfig;
        protected int numWorkers;
        
        protected void readEHConfig(String[] args) throws Exception {
            Properties properties = new Properties();
            if (args.length > 1) {
                properties.load(new FileReader(args[1]));
            } else {
                properties.load(EventCount.class.getClassLoader()
                        .getResourceAsStream("Config.properties"));
            }
        
            String username = properties.getProperty("eventhubspout.username");
            String password = properties.getProperty("eventhubspout.password");
            String namespaceName = properties
                    .getProperty("eventhubspout.namespace");
            String entityPath = properties.getProperty("eventhubspout.entitypath");
            String zkEndpointAddress = properties
                    .getProperty("zookeeper.connectionstring"); // opt
            int partitionCount = Integer.parseInt(properties
                    .getProperty("eventhubspout.partitions.count"));
            int checkpointIntervalInSeconds = Integer.parseInt(properties
                    .getProperty("eventhubspout.checkpoint.interval"));
            int receiverCredits = Integer.parseInt(properties
                    .getProperty("eventhub.receiver.credits")); // prefetch count
                                                                // (opt)
            System.out.println("Eventhub spout config: ");
            System.out.println("  partition count: " + partitionCount);
            System.out.println("  checkpoint interval: "
                    + checkpointIntervalInSeconds);
            System.out.println("  receiver credits: " + receiverCredits);
     
            spoutConfig = new EventHubSpoutConfig(username, password,
                    namespaceName, entityPath, partitionCount, zkEndpointAddress,
                    checkpointIntervalInSeconds, receiverCredits);
        
            // set the number of workers to be the same as partition number.
            // the idea is to have a spout and a logger bolt co-exist in one
            // worker to avoid shuffling messages across workers in storm cluster.
            numWorkers = spoutConfig.getPartitionCount();
        
            if (args.length > 0) {
                // set topology name so that sample Trident topology can use it as
                // stream name.
                spoutConfig.setTopologyName(args[0]);
            }
        }
        
        protected StormTopology buildTopology() {
            TopologyBuilder topologyBuilder = new TopologyBuilder();
       
            EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
            topologyBuilder.setSpout("EventHubsSpout", eventHubSpout,
                    spoutConfig.getPartitionCount()).setNumTasks(
                    spoutConfig.getPartitionCount());
            topologyBuilder
                    .setBolt("LoggerBolt", new LoggerBolt(),
                            spoutConfig.getPartitionCount())
                    .localOrShuffleGrouping("EventHubsSpout")
                    .setNumTasks(spoutConfig.getPartitionCount());
            return topologyBuilder.createTopology();
        }
        
        protected void runScenario(String[] args) throws Exception {
            boolean runLocal = true;
            readEHConfig(args);
            StormTopology topology = buildTopology();
            Config config = new Config();
            config.setDebug(false);
        
            if (runLocal) {
                config.setMaxTaskParallelism(2);
                LocalCluster localCluster = new LocalCluster();
                localCluster.submitTopology("test", config, topology);
                Thread.sleep(5000000);
                localCluster.shutdown();
            } else {
                config.setNumWorkers(numWorkers);
                StormSubmitter.submitTopology(args[0], config, topology);
            }
        }
        
        public static void main(String[] args) throws Exception {
            LogTopology topology = new LogTopology();
            topology.runScenario(args);
        }
    }
    ```

    Ez az osztály hoz létre egy új Event Hubs spout, a konfigurációs fájl a tulajdonságok használatával példányosítható azt. Fontos megjegyezni, hogy ez a példa feladatokat hoz létre annyi spoutokkal kapcsolatban a partíciók az eseményközpont számának ahhoz, hogy az adott eseményközpont által engedélyezett maximális párhuzamossági.

## <a name="next-steps"></a>További lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs – áttekintés][Event Hubs overview]
* [Eseményközpont létrehozása](event-hubs-create.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight Storm]: ../hdinsight/storm/apache-storm-overview.md
[Eseményközpont például a HDInsight alatt futó Storm]: https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/

<!-- Images -->

[12]: ./media/event-hubs-get-started-receive-storm/create-storm1.png
