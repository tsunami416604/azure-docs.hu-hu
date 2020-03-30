---
title: 'Rövid útmutató: Események fogadása az Apache Storm használatával – Azure Event Hubs'
description: 'Rövid útmutató: Ez a cikk tájékoztatást nyújt arról, hogyan fogadhat eseményeket az Azure Event Hubs-tól az Apache Storm használatával.'
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: java
ms.devlang: multiple
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: 90293da07d3a7ef1c32e5f82d35198d4ffa536b1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "73717604"
---
# <a name="quickstart-receive-events-from-event-hubs-using-apache-storm"></a>Rövid útmutató: Események fogadása az Event Hubs-tól az Apache Storm használatával

[Az Apache Storm](https://storm.incubator.apache.org) egy elosztott valós idejű számítási rendszer, amely leegyszerűsíti a határtalan adatfolyamok megbízható feldolgozását. Ez a szakasz bemutatja, hogyan használhatja az Azure Event Hubs Storm spout események fogadására event hubs. Az Apache Storm használatával az eseményeket több, különböző csomópontokon üzemeltetett folyamat között oszthatja fel. Az Event Hubs stormdal való integrációja leegyszerűsíti az eseményfelhasználást azáltal, hogy a Storm Zookeeper-telepítése, az állandó ellenőrzőpontok és az Event Hubs-tól érkező párhuzamos fogadások használatával átláthatómódon ellenőrzőpontokat használ.

Az Eseményközpontok fogadási mintáiról az [Eseményközpontok áttekintése című témakörben olvashat bővebben.][Event Hubs overview]

## <a name="prerequisites"></a>Előfeltételek
A rövid útmutató megkezdése előtt **hozzon létre egy Event Hubs névteret és egy eseményközpontot.** Az [Azure Portal](https://portal.azure.com) használatával hozzon létre egy Event Hubs típusú névteret, és szerezze be az okat a felügyeleti hitelesítő adatokat, amelyeket az alkalmazásnak az eseményközponttal való kommunikációhoz szüksége kell. Névtér és eseményközpont létrehozásához kövesse a [cikkben](event-hubs-create.md)található eljárást. 

## <a name="create-project-and-add-code"></a>Projekt létrehozása és kód hozzáadása

Ez az oktatóanyag egy [HDInsight][HDInsight Storm] Storm-telepítést használ, amely az Event Hubs kifolyómár elérhető.

1. Kövesse a [HDInsight Storm – Első lépések](../hdinsight/storm/apache-storm-overview.md) eljárást egy új HDInsight-fürt létrehozásához, és csatlakozzon hozzá a Távoli asztalon keresztül.
2. Másolja `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` a fájlt a helyi fejlesztési környezetbe. Ez tartalmazza az események-storm-spout.
3. A következő paranccsal telepítheti a csomagot a helyi Maven tárolóba. Ez lehetővé teszi, hogy egy későbbi lépésben adja hozzá hivatkozásként a Storm projektben.

    ```shell
    mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar
    ```
4. Az Eclipse alkalmazásban hozzon létre egy új Maven-projektet (kattintson **a Fájl**, majd **az Új**, majd a **Project parancsra**).
   
    ![Fájl -> Új -> Projekt][12]
5. Válassza **az Alapértelmezett munkaterületi hely használata**lehetőséget, majd kattintson a **Tovább** gombra.
6. Jelölje ki a **maven-archetípus-rövidindítási** archetípust, majd kattintson a **Tovább** gombra.
7. **GroupId** és **ArtifactId beszúrása,** majd kattintson a **Befejezés** gombra
8. A **pom.xml**fájlban adja hozzá `<dependency>` a következő függőségeket a csomóponthoz.

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

9. Az **src** mappában hozzon létre egy **Config.properties** nevű fájlt, `receive rule key` és `event hub name` másolja a következő tartalmat, helyettesítve a és az értékeket:

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
    Az **eventhub.receiver.credits** értéke határozza meg, hogy hány esemény kötegelt, mielőtt a Storm-folyamatra adná őket. Az egyszerűség kedvéért ez a példa ezt az értéket 10-re állítja. A termelés, általában meg kell állítani, hogy magasabb értékeket; például 1024.
10. Hozzon létre egy **loggerbolt** nevű új osztályt a következő kóddal:
    
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
    
    Ez a Storm bolt naplózza a fogadott események tartalmát. Ez könnyen kiterjeszthető a tárolószolgáltatásban lévő tuple-k tárolására. A [HDInsight Storm az Event Hub példával] ugyanezt a megközelítést használja az Azure Storage és a Power BI adatok tárolására.
11. Hozzon létre egy **LogTopology** nevű osztályt a következő kóddal:
    
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

    Ez az osztály létrehoz egy új Event Hubs spout, a konfigurációs fájl tulajdonságainak használatával példányosítani. Fontos megjegyezni, hogy ez a példa annyi spouts feladatokat hoz létre, mint a partíciók száma az eseményközpontban, annak érdekében, hogy az eseményközpont által engedélyezett maximális párhuzamosság.

## <a name="next-steps"></a>További lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Eseményközpontok – áttekintés][Event Hubs overview]
* [Eseményközpont létrehozása](event-hubs-create.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight Storm]: ../hdinsight/storm/apache-storm-overview.md
[HDInsight Storm az Event Hub példával]: https://github.com/Azure-Samples/hdinsight-java-storm-eventhub

<!-- Images -->

[12]: ./media/event-hubs-get-started-receive-storm/create-storm1.png
