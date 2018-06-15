---
title: Hozzon létre Java MapReduce a Hadoop - az Azure HDInsight |} Microsoft Docs
description: Útmutató Apache Maven használatával hozzon létre egy Java-alapú MapReduce-alkalmazást, majd az Azure HDInsight Hadoop futtassák azt.
services: hdinsight
editor: cgronlun
manager: jhubbard
author: Blackmist
documentationcenter: ''
tags: azure-portal
ms.assetid: 9ee6384c-cb61-4087-8273-fb53fa27c1c3
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: Java
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: c245b30a48be73f189dcf48502134cf36e93ba20
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32169540"
---
# <a name="develop-java-mapreduce-programs-for-hadoop-on-hdinsight"></a>A hdinsight Hadoop Java MapReduce programok fejlesztése

Útmutató Apache Maven használatával hozzon létre egy Java-alapú MapReduce-alkalmazást, majd az Azure HDInsight Hadoop futtassák azt.

> [!NOTE]
> Ebben a példában a HDInsight 3.6 legutóbb teszteltük.

## <a name="prerequisites"></a>Előfeltételek

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 8 vagy újabb (vagy egy azzal egyenértékű, például OpenJDK).
    
    > [!NOTE]
    > HDInsight-verziókról 3.4 és korábbi Java 7 használja. HDInsight 3.5-ös és nagyobb Java 8 használja.

* [Apache Maven](http://maven.apache.org/)

## <a name="configure-development-environment"></a>A fejlesztési környezet konfigurálása

A következő környezeti változók Java és a JDK telepítésekor lehet beállítani. Azonban ellenőrizni kell, hogy léteznek, illetve a rendszer a megfelelő értékeket tartalmaz.

* `JAVA_HOME` -érdemes mutasson a mappát, ahová a Java-futtatókörnyezet (JRE) telepítve van. Például az OS X, Unix vagy Linux rendszeren nem rendelkezhet hasonló érték `/usr/lib/jvm/java-7-oracle`. A Windows rendszerben kellene hasonló érték `c:\Program Files (x86)\Java\jre1.7`

* `PATH` -a következő elérési utakat kell tartalmaznia:
  
  * `JAVA_HOME` (vagy ezzel egyenértékű elérési útja)

  * `JAVA_HOME\bin` (vagy ezzel egyenértékű elérési útja)

  * A mappát, ahová a Maven telepítve van

## <a name="create-a-maven-project"></a>Maven-projekt létrehozása

1. Egy terminál-munkamenetet, vagy a parancssori a fejlesztési környezetben lépjen arra a helyre a projekt tárolni szeretné.

2. Használja a `mvn` parancsot, amely Maven együtt települ, a állványok a projekt létrehozásához.

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    > [!NOTE]
    > Ha a PowerShell használata esetén kell tenni a `-D` az idézőjelek közé foglalt paraméterek.
    >
    > `mvn archetype:generate "-DgroupId=org.apache.hadoop.examples" "-DartifactId=wordcountjava" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`

    Ez a parancs által megadott nevű könyvtár létrehozása a `artifactID` paraméter (**wordcountjava** ebben a példában.) Ez a könyvtár a következő elemeket tartalmazza:

   * `pom.xml` -A [projekt Object Model (POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html) , amely tartalmazza a projekt felépítéséhez használt információkat és a konfigurációs adatait.

   * `src` -A könyvtárban, amely tartalmazza az alkalmazás.

3. Törölje a `src/test/java/org/apache/hadoop/examples/apptest.java` fájlt. Ebben a példában nem szolgál.

## <a name="add-dependencies"></a>Adja hozzá a függőségek

1. Szerkessze a `pom.xml` fájlt, és adja hozzá a következő szöveget a `<dependencies>` szakasz:
   
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

    Ez határozza meg a szükséges kódtárak (belül felsorolt &lt;artifactid szakaszát\>) megadott verzióval (belül felsorolt &lt;verzió\>). Fordítási időben a függőségek az alapértelmezett Maven tárházból lesznek letöltve. Használhatja a [Maven tárház keresési](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) megjelenítése.
   
    A `<scope>provided</scope>` közli Maven, hogy az ezen függőségekhez nem csomagolható az alkalmazásban, a HDInsight-fürt futásidőben meghatározott.

    > [!IMPORTANT]
    > Használt verzió található meg a fürt Hadoop verziójának egyeznie kell. -Verziókban további információkért lásd: a [HDInsight-összetevők verziószámozása](../hdinsight-component-versioning.md) dokumentum.

2. Adja hozzá a következőt a `pom.xml` fájlt. Ez a szöveg belül kell lennie a `<project>...</project>` címkék a fájlban, például közötti `</dependencies>` és `</project>`.

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

    Az első beépülő modul konfigurálása a [Maven árnyalatát beépülő modul](http://maven.apache.org/plugins/maven-shade-plugin/), amelyek segítségével hozhatók létre olyan uberjar (más néven a fatjar), amely tartalmazza az alkalmazáshoz szükséges függőségek. Megakadályozza a párhuzamos licencek belül a jar-csomagot, amely az egyes rendszerek problémákat okozhat.

    A második beépülő modul konfigurálása a cél Java-verziót.

    > [!NOTE]
    > HDInsight 3.4-es és korábbi használat Java 7. HDInsight 3.5-ös és nagyobb Java 8 használja.

3. Mentse a `pom.xml` fájlt.

## <a name="create-the-mapreduce-application"></a>A MapReduce-alkalmazás létrehozása

1. Lépjen a `wordcountjava/src/main/java/org/apache/hadoop/examples` könyvtár, és nevezze át a `App.java` fájlt `WordCount.java`.

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
   
    Figyelje meg, a csomag neve `org.apache.hadoop.examples` és az osztálynév `WordCount`. Amikor a MapReduce feladatot használhatja ezeket a neveket.

3. Mentse a fájlt.

## <a name="build-the-application"></a>Az alkalmazás létrehozása

1. Módosítsa a `wordcountjava` könyvtárat, ha nem már létezik.

2. A következő paranccsal hozhat létre az alkalmazást tartalmazó JAR-fájlt:

   ```
   mvn clean package
   ```

    Ez a parancs törli az előző build összetevőihez, töltse le a bármely függőségek, amelyek nem már telepítve van, és a majd buildek és a csomag az alkalmazás.

3. Ha a parancs a `wordcountjava/target` directory nevű fájlt tartalmaz `wordcountjava-1.0-SNAPSHOT.jar`.
   
   > [!NOTE]
   > A `wordcountjava-1.0-SNAPSHOT.jar` fájl egy uberjar, nem csak a WordCount feladat, de is, hogy a feladat futási időben szükséges függőségek tartalmazó.

## <a id="upload"></a>Töltse fel a jar

Az alábbi parancs segítségével töltse fel a jar-fájlra a HDInsight headnode:

   ```bash
   scp target/wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:
   ```

    Replace __USERNAME__ with your SSH user name for the cluster. Replace __CLUSTERNAME__ with the HDInsight cluster name.

Ez a parancs másolja át a fájlokat a helyi rendszer az átjárócsomóponthoz. További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run"></a>A MapReduce feladatot futtatni a Hadoop

1. Csatlakozzon a HDInsight SSH használatával. További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Az SSH-munkamenetből használja a MapReduce alkalmazás futtatásához a következő parancsot:
   
   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```
   
    A WordCount MapReduce alkalmazás indítja el. A bemeneti fájl `/example/data/gutenberg/davinci.txt`, és a kimeneti könyvtár `/example/data/wordcountout`. A bemeneti fájl- és a kimeneti tárolódnak a fürt alapértelmezett tárolóhelyét.

3. Amikor a feladat befejeződik, használja a következő parancsot az eredmények megtekintése:
   
   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    Szavak és számát, az alábbihoz hasonló értékekkel kell megjelennie:
   
        zeal    1
        zelus   1
        zenith  2

## <a id="nextsteps"></a>Következő lépések

Ez a dokumentum rendelkezik megtanulta, hogyan fejleszthet Java MapReduce feladatot. Lásd a következő dokumentumokat egyéb módjai HDInsight használata.

* [A Hive használata a HDInsightban](hdinsight-use-hive.md)
* [A Pig használata a HDInsightban](hdinsight-use-pig.md)
* [Use MapReduce with HDInsight](hdinsight-use-mapreduce.md)

További információ: a [Java fejlesztői központ](https://azure.microsoft.com/develop/java/).

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]:apache-hadoop-connect-excel-power-query.md

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-power-query]:apache-hadoop-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx

