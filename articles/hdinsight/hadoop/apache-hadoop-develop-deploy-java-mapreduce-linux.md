---
title: Java MapReduce Apache Hadoop – Azure HDInsight létrehozása
description: Ismerje meg, hogyan hozzon létre egy Java-alapú MapReduce-alkalmazást, majd futtathatja a Hadoop Azure HDInsight Apache Maven segítségével.
services: hdinsight
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: 42fc92b0cee20679ab72a389cc11522c0ca4e0fd
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53013754"
---
# <a name="develop-java-mapreduce-programs-for-apache-hadoop-on-hdinsight"></a>Java MapReduce programok fejlesztése a HDInsight Apache hadoop

Ismerje meg, hogyan hozzon létre egy Java-alapú MapReduce-alkalmazást, majd futtassa az Apache Hadoop az Azure HDInsight Apache Maven segítségével.

> [!NOTE]
> Ebben a példában a HDInsight 3.6-os legutóbb tesztelés.

## <a name="prerequisites"></a>Előfeltételek

* [Java JDK](https://www.oracle.com/technetwork/java/javase/downloads/) 8 vagy újabb (vagy egy azzal egyenértékű, például az OpenJDK).
    
    > [!NOTE]
    > HDInsight-verziók 3.4-es és korábbi használható a Java 7. A HDInsight 3.5-ös és újabb Java 8 használ.

* [Apache Maven](https://maven.apache.org/)

## <a name="configure-development-environment"></a>A fejlesztési környezet konfigurálása

Az alábbi környezeti változókat a Java és a JDK telepítésekor lehet beállítani. Érdemes azonban ellenőriznie, hogy a fentiek léteznek-e, és a rendszer számára megfelelő értékeket tartalmaznak-e.

* `JAVA_HOME` -kell mutatnia a könyvtárban, ahol a Java-futtatókörnyezet (JRE) telepítve van. Például az OS X-, Unix- vagy Linux rendszeren, értékkel kell rendelkeznie egy hasonló `/usr/lib/jvm/java-7-oracle`. Windows, a következőhöz hasonló érték kellene `c:\Program Files (x86)\Java\jre1.7`

* `PATH` – a következő elérési utakat kell tartalmaznia:
  
  * `JAVA_HOME` (vagy ezzel egyenértékű elérési útja)

  * `JAVA_HOME\bin` (vagy ezzel egyenértékű elérési útja)

  * A könyvtárban, ahol telepítve van a maven használatával

## <a name="create-a-maven-project"></a>Hozzon létre egy Maven-projektet

1. Egy terminál-munkamenetben, vagy a fejlesztési környezet parancssorában lépjen arra a helyre szeretné tárolni a projekthez.

2. Használja a `mvn` parancsot, amely a Mavennel telepítve van, a keret létrehozásához a projekt létrehozásához.

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    > [!NOTE]
    > Ha a Powershellt használ, meg kell tenni a `-D` paramétereket a dupla idézőjelek között.
    >
    > `mvn archetype:generate "-DgroupId=org.apache.hadoop.examples" "-DartifactId=wordcountjava" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`

    Ez a parancs által a megadott néven létrehoz egy könyvtárat a `artifactID` paraméter (**wordcountjava** ebben a példában.) Ez a könyvtár a következő elemeket tartalmazza:

   * `pom.xml` -A [projekt Object Model (POM)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html) , amely tartalmazza a projekt felépítéséhez használt információk és a konfiguráció részleteit.

   * `src` – A könyvtár, amely tartalmazza az alkalmazást.

3. Törölje a `src/test/java/org/apache/hadoop/examples/apptest.java` fájlt. Ebben a példában nem használatos.

## <a name="add-dependencies"></a>Függőségek hozzáadása

1. Szerkessze a `pom.xml` fájlt, és adja hozzá a következő szöveget a `<dependencies>` szakaszban:
   
   ```xml
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-mapreduce-examples</artifactId>
        <version>2.7.3</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-mapreduce-client-common</artifactId>
        <version>2.7.3</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-common</artifactId>
        <version>2.7.3</version>
        <scope>provided</scope>
    </dependency>
   ```

    Ez határozza meg a szükséges kódtárak (sorolva &lt;artifactId\>) egy adott verziójával (sorolva &lt;verzió\>). A fordítás során a függőségeket a alapértelmezett Maven tárházból letöltődnek. Használhatja a [Maven tárház keresési](https://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) megjelenítése.
   
    A `<scope>provided</scope>` arra utasítja a Maven, hogy az ezeket a függőségeket nem csomagolható az alkalmazás a futásidőben a HDInsight-fürt által meghatározott.

    > [!IMPORTANT]
    > A használt verziója található a fürtön futó Hadoop verziójának egyeznie kell. A verziókról további információkért lásd: a [HDInsight összetevők verziószámozása](../hdinsight-component-versioning.md) dokumentumot.

2. Adja a következőket az `pom.xml` fájlhoz. Ez a szöveg belül kell lennie a `<project>...</project>` címkék a fájlban, pl. közötti `</dependencies>` és `</project>`.

   ```xml
    <build>
        <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>2.3</version>
            <configuration>
            <transformers>
                <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                </transformer>
            </transformers>
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
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.6.1</version>
            <configuration>
            <source>1.8</source>
            <target>1.8</target>
            </configuration>
        </plugin>
        </plugins>
    </build>
   ```

    Az első beépülő modul konfigurálása a [Maven Shade beépülő modul](https://maven.apache.org/plugins/maven-shade-plugin/), amely segítségével hozhat létre egy uberjar (más néven egy fatjar), amely szükséges az alkalmazás függőségeit tartalmazza. Megakadályozza a licencek a jar-csomag, amely egyes rendszerek problémákat okozhat a párhuzamos is.

    A második beépülő modul konfigurálása a cél Java-verzió.

    > [!NOTE]
    > HDInsight 3.4-es és korábbi használat Java 7. A HDInsight 3.5-ös és újabb Java 8 használ.

3. Mentse a `pom.xml` fájlt.

## <a name="create-the-mapreduce-application"></a>A MapReduce-alkalmazás létrehozása

1. Nyissa meg a `wordcountjava/src/main/java/org/apache/hadoop/examples` könyvtárat, és nevezze át a `App.java` fájlt `WordCount.java`.

2. Nyissa meg a `WordCount.java` fájlt egy szövegszerkesztőben, és cserélje ki annak tartalmát a következő szöveget:
   
    ```java
    package org.apache.hadoop.examples;

    import java.io.IOException;
    import java.util.StringTokenizer;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.IntWritable;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapreduce.Job;
    import org.apache.hadoop.mapreduce.Mapper;
    import org.apache.hadoop.mapreduce.Reducer;
    import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
    import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
    import org.apache.hadoop.util.GenericOptionsParser;

    public class WordCount {

        public static class TokenizerMapper
            extends Mapper<Object, Text, Text, IntWritable>{

        private final static IntWritable one = new IntWritable(1);
        private Text word = new Text();

        public void map(Object key, Text value, Context context
                        ) throws IOException, InterruptedException {
            StringTokenizer itr = new StringTokenizer(value.toString());
            while (itr.hasMoreTokens()) {
            word.set(itr.nextToken());
            context.write(word, one);
            }
        }
    }

    public static class IntSumReducer
            extends Reducer<Text,IntWritable,Text,IntWritable> {
        private IntWritable result = new IntWritable();

        public void reduce(Text key, Iterable<IntWritable> values,
                            Context context
                            ) throws IOException, InterruptedException {
            int sum = 0;
            for (IntWritable val : values) {
            sum += val.get();
            }
            result.set(sum);
            context.write(key, result);
        }
    }

    public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
        if (otherArgs.length != 2) {
            System.err.println("Usage: wordcount <in> <out>");
            System.exit(2);
        }
        Job job = new Job(conf, "word count");
        job.setJarByClass(WordCount.class);
        job.setMapperClass(TokenizerMapper.class);
        job.setCombinerClass(IntSumReducer.class);
        job.setReducerClass(IntSumReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
        FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
        }
    }
    ```
   
    Figyelje meg, hogy a csomag neve `org.apache.hadoop.examples` és az osztálynév `WordCount`. Ezeket a neveket használja, a MapReduce feladat elküldésekor.

3. Mentse a fájlt.

## <a name="build-the-application"></a>Az alkalmazás létrehozása

1. Módosítsa a `wordcountjava` könyvtárat, ha Ön még nem létezik.

2. Használja a következő parancsot egy JAR-fájlt, amely tartalmazza az alkalmazás létrehozásához:

   ```
   mvn clean package
   ```

    Ez a parancs törli a bármely korábbi build-összetevőket, bármely függőségeit, amelyek még nem lett telepítette, és a majd buildek és a csomag az alkalmazás letölti.

3. Ha befejezte a parancsot, a `wordcountjava/target` könyvtár tartalmaz egy fájlt `wordcountjava-1.0-SNAPSHOT.jar`.
   
   > [!NOTE]
   > A `wordcountjava-1.0-SNAPSHOT.jar` fájl egy uberjar, nem csak a WordCount-feladat, hanem függőségek, a feladat futási időben igénylő tartalmazó.

## <a id="upload"></a>Töltse fel a fájlt

A következő paranccsal töltse fel a jar-fájlt a HDInsight átjárócsomóponthoz:

   ```bash
   scp target/wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:
   ```

Cserélje le __felhasználónév__ a fürt SSH-felhasználónevet. Cserélje le __CLUSTERNAME__ a HDInsight-fürt nevéhez.

Ez a parancs másolja a fájlokat a helyi rendszerről a fő csomópontot. További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run"></a>Hadoop MapReduce feladat futtatása

1. A HDInsight SSH használatával csatlakozhat. További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Az SSH-munkamenetben használja a következő parancsot futtatja a MapReduce-alkalmazást:
   
   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```
   
    Ez a parancs elindítja a MapReduce a WordCount alkalmazás. A bemeneti fájl `/example/data/gutenberg/davinci.txt`, és a kimeneti könyvtár `/example/data/wordcountout`. A bemeneti fájl- és a kimeneti tárolódnak, a a fürt alapértelmezett tárolója.

3. Miután a feladat befejeződik, használja a következő parancsot az eredmények megtekintése:
   
   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    Szavak és számát, az alábbi szöveghez hasonló értékekkel kell kapnia:
   
        zeal    1
        zelus   1
        zenith  2

## <a id="nextsteps"></a>Következő lépések

Ebben a dokumentumban megtanulhatta, hogyan hozhat létre egy Java MapReduce-feladatot. Dolgozunk a HDInsight egyéb módjaira vonatkozóan az alábbi dokumentumokban talál.

* [A Hive használata a HDInsightban](hdinsight-use-hive.md)
* [A Pig használata a HDInsightban](hdinsight-use-pig.md)
* [A MapReduce használata a HDInsight](hdinsight-use-mapreduce.md)

További információ: a [Java fejlesztői központ](https://azure.microsoft.com/develop/java/).

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]:apache-hadoop-connect-excel-power-query.md

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-power-query]:apache-hadoop-connect-excel-power-query.md

[powershell-PSCredential]: https://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx

