---
title: 'Azure Cosmos DB: Graph elemzés végrehajtása a Spark és Apache TinkerPop Gremlin használatával |} Microsoft Docs'
description: Ez a cikk bemutatja beállíthatja és futtathatja a graph elemzés és párhuzamos számítási az Azure Cosmos Adatbázisba Spark és TinkerPop SparkGraphComputer vonatkozó utasításokat.
services: cosmosdb
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.custom: quick start connect
ms.devlang: gremlin
ms.topic: conceptual
ms.date: 09/08/2017
ms.author: sngun
ms.openlocfilehash: 7cc68bfabff5a3afafb89bdb6bb393480946a7a7
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34796039"
---
# <a name="azure-cosmos-db-perform-graph-analytics-by-using-spark-and-apache-tinkerpop-gremlin"></a>Azure Cosmos DB: Graph elemzés végrehajtása a Spark és Apache TinkerPop Gremlin használatával

[Az Azure Cosmos DB](introduction.md) Microsoft globálisan elosztott, több modellre adatbázis-szolgáltatás. Hozzon létre, és a dokumentum, a kulcs/érték és a graph-adatbázisok mindegyike kihasználhassa a globális terjesztési és vízszintes méretű képességek Azure Cosmos DB középpontjában lekérdezése. Az Azure Cosmos DB támogatja az online tranzakció-feldolgozási (OLTP) graph használó munkaterhelések [Apache TinkerPop Gremlin](graph-introduction.md).

[Spark](http://spark.apache.org/) Apache szoftver Foundation projekt, amely az általános célú online analitikus feldolgozási (OLAP) adatok feldolgozási összpontosít. Spark hibrid a memória vagy lemez-alapú elosztott számítási modellt biztosít a Hadoop-MapReduce-modell hasonló. A felhőbeli Apache Spark on segítségével telepíthet [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

Azure Cosmos DB és Spark kombinálásával végezheti el az OLTP és OLAP munkaterhelések Gremlin használatakor. A gyors üzembe helyezési cikk bemutatja, hogyan Azure Cosmos DB Gremlin lekérdezéseinek futtatásához egy Azure HDInsight Spark-fürtön.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt futtathatná ezt a mintát, rendelkeznie kell a következő előfeltételekkel:
* Az Azure HDInsight Spark-fürt 2.0
* JDK 1.8 + (Ha még nem rendelkezik JDK, futtassa `apt-get install default-jdk`.)
* Maven (Ha még nem rendelkezik Maven, `apt-get install maven`.)
* Azure-előfizetés ([!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)])

Egy Azure HDInsight Spark-fürt beállításával kapcsolatos információkért lásd: [kiépítés HDInsight-fürtök](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-an-azure-cosmos-db-database-account"></a>Egy Azure Cosmos DB adatbázisfiók létrehozása

Először hozzon létre egy adatbázis-fiók a Graph API-t a következő módon:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-collection"></a>Gyűjtemény hozzáadása

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-graph.md)]

## <a name="get-apache-tinkerpop"></a>Apache TinkerPop beolvasása

Apache TinkerPop érhető el a következő módon:

1. A fő csomópontra a HDInsight-fürt távoli `ssh tinkerpop3-cosmosdb-demo-ssh.azurehdinsight.net`.

2. Klónozza a TinkerPop3 forráskódot, összeállítani, helyileg, és telepítse Maven-gyorsítótárba.

    ```bash
    git clone https://github.com/apache/tinkerpop.git
    cd tinkerpop
    mvn clean install
    ```

3. A Spark-Gremlin beépülő modul telepítése 

    a. A telepítés, a beépülő modul szőlőmust kezeli. A tárolóhelyekkel kapcsolatos információi szőlőmust, hogy letölthesse a beépülő modult és annak függőségeit feltöltéséhez. 

      A szőlőmust konfigurációs fájl létrehozása, ha nincs jelen az `~/.groovy/grapeConfig.xml`. A következő beállításokkal:

    ```xml
    <ivysettings>
    <settings defaultResolver="downloadGrapes"/>
    <resolvers>
        <chain name="downloadGrapes">
        <filesystem name="cachedGrapes">
            <ivy pattern="${user.home}/.groovy/grapes/[organisation]/[module]/ivy-[revision].xml"/>
            <artifact pattern="${user.home}/.groovy/grapes/[organisation]/[module]/[type]s/[artifact]-[revision].[ext]"/>
        </filesystem>
        <ibiblio name="codehaus" root="http://repository.codehaus.org/" m2compatible="true"/>
        <ibiblio name="central" root="http://central.maven.org/maven2/" m2compatible="true"/>
        <ibiblio name="jitpack" root="https://jitpack.io" m2compatible="true"/>
        <ibiblio name="java.net2" root="http://download.java.net/maven/2/" m2compatible="true"/>
        <ibiblio name="apache-snapshots" root="http://repository.apache.org/snapshots/" m2compatible="true"/>
        <ibiblio name="local" root="file:${user.home}/.m2/repository/" m2compatible="true"/>
        </chain>
    </resolvers>
    </ivysettings>
    ``` 

    b. Indítsa el a Gremlin konzol `bin/gremlin.sh`.
        
    c. A Spark-Gremlin beépülő modul telepítése verziójával 3.3.0-SNAPSHOT, amely az előző lépésben parancsfájlkezelő:

    ```bash
    $ bin/gremlin.sh

            \,,,/
            (o o)
    -----oOOo-(3)-oOOo-----
    plugin activated: tinkerpop.server
    plugin activated: tinkerpop.utilities
    plugin activated: tinkerpop.tinkergraph
    gremlin> :install org.apache.tinkerpop spark-gremlin 3.3.0-SNAPSHOT
    ==>loaded: [org.apache.tinkerpop, spark-gremlin, 3.3.0-SNAPSHOT] - restart the console to use [tinkerpop.spark]
    gremlin> :q
    $ bin/gremlin.sh

            \,,,/
            (o o)
    -----oOOo-(3)-oOOo-----
    plugin activated: tinkerpop.server
    plugin activated: tinkerpop.utilities
    plugin activated: tinkerpop.tinkergraph
    gremlin> :plugin use tinkerpop.spark
    ==>tinkerpop.spark activated
    ```

4. Ellenőrizze, hogy `Hadoop-Gremlin` , aktivált `:plugin list`. Ez a beépülő modul, mert azt sikerült zavarják a Spark-Gremlin beépülő modul letiltása `:plugin unuse tinkerpop.hadoop`.

## <a name="prepare-tinkerpop3-dependencies"></a>Készítse elő a TinkerPop3 függőségek

Ha az előző lépésben a TinkerPop3 beépített, a folyamat is lekért összes jar-függőség a célkönyvtár a Spark- és a Hadoop. Használja a JAR-fájlok, amelyek előre HDI együtt vannak telepítve, és lekéréses a további függőségek csak szükség szerint kivételével.

1. Keresse fel a Gremlin konzol tároló könyvtár: `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone`. 

2. Helyezze át az összes JAR-fájlok kivételével a `ext/` való `lib/`: `find ext/ -name '*.jar' -exec mv {} lib/ \;`.

3. Távolítsa el a szalagtárak jar összes `lib/` , amelyek nem a következők:

    ```bash
    # TinkerPop3
    gremlin-console-3.3.0-SNAPSHOT.jar
    gremlin-core-3.3.0-SNAPSHOT.jar       
    gremlin-groovy-3.3.0-SNAPSHOT.jar     
    gremlin-shaded-3.3.0-SNAPSHOT.jar     
    hadoop-gremlin-3.3.0-SNAPSHOT.jar     
    spark-gremlin-3.3.0-SNAPSHOT.jar      
    tinkergraph-gremlin-3.3.0-SNAPSHOT.jar

    # Gremlin depedencies
    asm-3.2.jar                                
    avro-1.7.4.jar                             
    caffeine-2.3.1.jar                         
    cglib-2.2.1-v20090111.jar                  
    gbench-0.4.3-groovy-2.4.jar                
    gprof-0.3.1-groovy-2.4.jar                 
    groovy-2.4.9-indy.jar                      
    groovy-2.4.9.jar                           
    groovy-console-2.4.9.jar                   
    groovy-groovysh-2.4.9-indy.jar             
    groovy-json-2.4.9-indy.jar                 
    groovy-jsr223-2.4.9-indy.jar               
    groovy-sql-2.4.9-indy.jar                  
    groovy-swing-2.4.9.jar                     
    groovy-templates-2.4.9.jar                 
    groovy-xml-2.4.9.jar                       
    hadoop-yarn-server-nodemanager-2.7.2.jar   
    hppc-0.7.1.jar                             
    javatuples-1.2.jar                         
    jaxb-impl-2.2.3-1.jar                      
    jbcrypt-0.4.jar                            
    jcabi-log-0.14.jar                         
    jcabi-manifests-1.1.jar                    
    jersey-core-1.9.jar                        
    jersey-guice-1.9.jar                       
    jersey-json-1.9.jar                        
    jettison-1.1.jar                           
    scalatest_2.11-2.2.6.jar                   
    servlet-api-2.5.jar                        
    snakeyaml-1.15.jar                         
    unused-1.0.0.jar                           
    xml-apis-1.3.04.jar                        
    ```

## <a name="get-the-azure-cosmos-db-spark-connector"></a>Az Azure Cosmos DB Spark-összekötő beolvasása

1. Az Azure Cosmos DB Spark-összekötő beolvasása `azure-cosmosdb-spark-0.0.3-SNAPSHOT.jar` és Cosmos DB Java SDK `azure-documentdb-1.12.0.jar` a [Azure Cosmos DB Spark-összekötő a Githubon](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark-0.0.3_2.0.2_2.11).

2. Másik lehetőségként hozhat létre, helyileg. A Spark-Gremlin legújabb verzióját a következővel történt 1.6.1-es Spark, és nem kompatibilis a 2.0.2, Spark az Azure Cosmos DB Spark-összekötő jelenleg használt, mert a legújabb TinkerPop3 kód lefordításához, és manuálisan telepíti a JAR-fájlok kivételével. Tegye a következőket:

    a. Az Azure Cosmos DB Spark-összekötő klónozását.

    b. Build TinkerPop3 (a korábbi lépésekben tette meg). Telepítse az összes TinkerPop 3.3.0-SNAPSHOT JAR-fájlok kivételével helyileg.

    ```bash
    mvn install:install-file -Dfile="gremlin-core-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-core -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar
    mvn install:install-file -Dfile="gremlin-groovy-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-groovy -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="gremlin-shaded-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-shaded -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="hadoop-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=hadoop-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="spark-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=spark-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="tinkergraph-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=tinkergraph-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    ```

    c. Frissítés `tinkerpop.version` `azure-documentdb-spark/pom.xml` való `3.3.0-SNAPSHOT`.
    
    d. A Maven build. A szükséges JAR-fájlok kivételével kerülnek `target` és `target/alternateLocation`.

    ```bash
    git clone https://github.com/Azure/azure-cosmosdb-spark.git
    cd azure-documentdb-spark
    mvn clean package
    ```

3. Másolja a korábban említett JAR-fájlok kivételével egy helyi, ~ / azure-documentdb-spark:

    ```bash
    $ azure-documentdb-spark:
    mkdir ~/azure-documentdb-spark
    cp target/azure-documentdb-spark-0.0.3-SNAPSHOT.jar ~/azure-documentdb-spark
    cp target/alternateLocation/azure-documentdb-1.10.0.jar ~/azure-documentdb-spark
    ```

## <a name="distribute-the-dependencies-to-the-spark-worker-nodes"></a>A Spark munkavégző csomópontokhoz függőségek terjesztése 

1. Mivel az graph adatok átalakítása TinkerPop3 függ, el kell juttatnia minden Spark munkavégző csomópontokhoz kapcsolódó függőségek.

2. Másolja a korábban említett Gremlin függőségek, a CosmosDB Spark összekötő jar és CosmosDB Java SDK a feldolgozó csomópontok a következő módon:

    a. Másolja az összes a JAR-fájlok kivételével az `~/azure-documentdb-spark`.

    ```bash
    $ /home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone:
    cp lib/* ~/azure-documentdb-spark
    ```

    b. A Spark feldolgozó csomópontjaihoz, amely az Ambari irányítópultján a listájának lekérdezése a `Spark2 Clients` listájában a `Spark2` szakasz.

    c. A csomópontok másolja könyvtárhoz.

    ```bash
    scp -r ~/azure-documentdb-spark sshuser@wn0-cosmos:/home/sshuser
    scp -r ~/azure-documentdb-spark sshuser@wn1-cosmos:/home/sshuser
    ...
    ```
    
## <a name="set-up-the-environment-variables"></a>A környezeti változók beállítása

1. A Spark-fürt HDP verziója található. A címtár-név alapján `/usr/hdp/` (például 2.5.4.2-7).

2. Állítsa be az összes csomópont hdp.version. Ambari irányítópulton Ugrás **YARN szakasz** > **Configs** > **speciális**, majd tegye a következőket: 
 
    a. A `Custom yarn-site`, adja hozzá az új tulajdonság `hdp.version` a fő csomóponton levő HDP verzió értékét. 
     
    b. A konfigurációk mentéséhez. Nincsenek figyelmeztetések, amely figyelmen kívül hagyhatja. 
     
    c. Az értesítési ikonok jelzik, indítsa újra a YARN és az Oozie-szolgáltatás.

3. Állítsa be az alábbi környezeti változókat a fő csomóponton (cserélje le az értékeket szükség szerint):

    ```bash
    export HADOOP_GREMLIN_LIBS=/home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/ext/spark-gremlin/lib
    export CLASSPATH=$CLASSPATH:$HADOOP_CONF_DIR:/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    export HDP_VERSION=2.5.4.2-7
    export HADOOP_HOME=${HADOOP_HOME:-/usr/hdp/current/hadoop-client}
    ```

## <a name="prepare-the-graph-configuration"></a>Készítse elő a graph-konfiguráció

1. Egy konfigurációs fájl létrehozása az Azure Cosmos DB kapcsolódási paraméterek és a Spark-beállításokkal, és hogy a `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/conf/hadoop/gremlin-spark.properties`.

    ```
    gremlin.graph=org.apache.tinkerpop.gremlin.hadoop.structure.HadoopGraph
    gremlin.hadoop.jarsInDistributedCache=true
    gremlin.hadoop.defaultGraphComputer=org.apache.tinkerpop.gremlin.spark.process.computer.SparkGraphComputer

    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    gremlin.hadoop.graphWriter=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBOutputRDD

    ####################################
    # SparkGraphComputer Configuration #
    ####################################
    spark.master=yarn
    spark.executor.memory=3g
    spark.executor.instances=6
    spark.serializer=org.apache.spark.serializer.KryoSerializer
    spark.kryo.registrator=org.apache.tinkerpop.gremlin.spark.structure.io.gryo.GryoRegistrator
    gremlin.spark.persistContext=true

    # Classpath for the driver and executors
    spark.driver.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    spark.executor.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    
    ######################################
    # DocumentDB Spark connector         #
    ######################################
    spark.documentdb.connectionMode=Gateway
    spark.documentdb.schema_samplingratio=1.0
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    spark.documentdb.preferredRegions=FILLIN
    ```

2. Frissítés a `spark.driver.extraClassPath` és `spark.executor.extraClassPath` tartalmazza a JAR-fájlok, amelyek ebben az esetben terjesztve az előző lépésben kivételével könyvtárának `/home/sshuser/azure-documentdb-spark/*`.

3. Azure Cosmos DB adja meg a következő adatokat:

    ```
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    # Optional
    #spark.documentdb.preferredRegions=West\ US;West\ US\ 2
    ```
   
## <a name="load-the-tinkerpop-graph-and-save-it-to-azure-cosmos-db"></a>A TinkerPop graph betölteni, és mentse azt az Azure Cosmos-Adatbázishoz
Bemutatják, hogyan lehet egy grafikonon megőrizni az Azure Cosmos DB, ebben a példában a használja a TinkerPop TinkerPop modern graph előre definiált. A grafikon Kryo formátumban tárolja, és a TinkerPop tárház találhatók.

1. Mert Gremlin YARN módban futnak, meg kell nyitnia a Diagramadatok a Hadoop-fájlrendszer érhető el. Az alábbi parancsokkal könyvtár és a helyi graph fájl másolása. 

    ```bash
    $ tinkerpop:
    hadoop fs -mkdir /graphData
    hadoop fs -copyFromLocal ~/tinkerpop/data/tinkerpop-modern.kryo /graphData/tinkerpop-modern.kryo
    ```

2. Ideiglenesen frissítése a `gremlin-spark.properties` használandó `GryoInputFormat` olvasni a diagramon. Is utalhat `inputLocation` a könyvtárként hoz létre, mint a következőket:

    ```
    gremlin.hadoop.graphReader=org.apache.tinkerpop.gremlin.hadoop.structure.io.gryo.GryoInputFormat
    gremlin.hadoop.inputLocation=/graphData/tinkerpop-modern.kryo
    ```

3. Indítsa el az Gremlin konzolját, és hozza létre az megőrizni az adatokat a konfigurált Azure Cosmos DB gyűjteményhez a következő számítási lépéseket:  

    a. A grafikon létrehozása `graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")`.

    b. Használja a SparkGraphComputer írásra `graph.compute(SparkGraphComputer.class).result(GraphComputer.ResultGraph.NEW).persist(GraphComputer.Persist.EDGES).program(TraversalVertexProgram.build().traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)),"gremlin-groovy","g.V()").create(graph)).submit().get()`.

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[gryoinputformat->documentdboutputrdd]
    gremlin> hg = graph.
                compute(SparkGraphComputer.class).
                result(GraphComputer.ResultGraph.NEW).
                persist(GraphComputer.Persist.EDGES).
                program(TraversalVertexProgram.build().
                    traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)), "gremlin-groovy", "g.V()").
                    create(graph)).
                submit().
                get() 
    ==>result[hadoopgraph[documentdbinputrdd->documentdboutputrdd],memory[size:1]]
    ```

4. Az adatkezelő ellenőrizheti, hogy az adatokat az Azure Cosmos Adatbázishoz nincs megőrizve.

## <a name="load-the-graph-from-azure-cosmos-db-and-run-gremlin-queries"></a>A grafikon betöltése az Azure Cosmos-Adatbázisból, és Gremlin lekérdezések futtatása

1. A grafikon betöltéséhez szerkesztése `gremlin-spark.properties` beállítása `graphReader` való `DocumentDBInputRDD`:

    ```
    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    ```

2. A grafikon betöltése, haladnak át az adatokat és Gremlin lekérdezéseket futtathat vele a következő módon:

    a. Indítsa el a Gremlin konzolt `bin/gremlin.sh`.

    b. Hozzon létre a diagramon a konfigurációs `graph = GraphFactory.open('conf/hadoop/gremlin-spark.properties')`.

    c. Hozzon létre egy grafikonon átjárás SparkGraphComputer `g = graph.traversal().withComputer(SparkGraphComputer)`.

    d. A következő Gremlin graph lekérdezések futtatása:

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[documentdbinputrdd->documentdboutputrdd]
    gremlin> g = graph.traversal().withComputer(SparkGraphComputer)
    ==>graphtraversalsource[hadoopgraph[documentdbinputrdd->documentdboutputrdd], sparkgraphcomputer]
    gremlin> g.V().count()
    ==>6
    gremlin> g.E().count()
    ==>6
    gremlin> g.V(1).out().values('name')
    ==>josh
    ==>vadas
    ==>lop
    gremlin> g.V().hasLabel('person').coalesce(values('nickname'), values('name'))
    ==>josh
    ==>peter
    ==>vadas
    ==>marko
    gremlin> g.V().hasLabel('person').
            choose(values('name')).
                option('marko', values('age')).
                option('josh', values('name')).
                option('vadas', valueMap()).
                option('peter', label())
    ==>josh
    ==>person
    ==>[name:[vadas],age:[27]]
    ==>29
    ```

> [!NOTE]
> Részletesebb naplózás, állítsa be a napló szintjén lévő `conf/log4j-console.properties` részletesebb szintjét.
>

## <a name="next-steps"></a>További lépések

A gyors üzembe helyezési cikkben Azure Cosmos DB és Spark kombinálásával diagramjait munkavégzés hogy megismerte.

> [!div class="nextstepaction"]
