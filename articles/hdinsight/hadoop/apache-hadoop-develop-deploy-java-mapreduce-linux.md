---
title: Java MapReduce létrehozása Apache Hadoophoz – Azure HDInsight
description: Ismerje meg, hogyan hozhat létre egy Java-alapú MapReduce alkalmazást az Apache Mavennel, majd futtassa azt a Hadoop segítségével az Azure HDInsight-on.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/16/2020
ms.openlocfilehash: a37a8bb45c11d5b74f3059a153806e3d083cf452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76311954"
---
# <a name="develop-java-mapreduce-programs-for-apache-hadoop-on-hdinsight"></a>Java MapReduce programok fejlesztése az Apache Hadoop számára a HDInsight on HDInsight

Ismerje meg, hogyan hozhat létre egy Java-alapú MapReduce alkalmazást az Apache Maven használatával, majd futtassa azt az Apache Hadoop segítségével az Azure HDInsight szolgáltatásán.

## <a name="prerequisites"></a>Előfeltételek

* [Java Developer Kit (JDK) 8-as verzió](https://aka.ms/azure-jdks).

* [Apache Maven](https://maven.apache.org/download.cgi) megfelelően [telepítve](https://maven.apache.org/install.html) szerint Apache.  Maven egy projekt épít rendszer Java projektek.

## <a name="configure-development-environment"></a>A fejlesztési környezet konfigurálása

A cikkhez használt környezet egy Windows 10-et futtató számítógép volt. A parancsok végrehajtása a parancssorban történt, és a különböző fájlokat a Jegyzettömbbel szerkesztették. Módosítsa ennek megfelelően a környezethez.

A parancssorból adja meg az alábbi parancsokat a munkakörnyezet létrehozásához:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Maven-projekt létrehozása

1. Írja be a következő parancsot egy **wordcountjava**nevű Maven projekt létrehozásához:

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    Ez a parancs létrehoz egy könyvtárat `artifactID` a paraméter (**wordcountjava** ebben a példában) által megadott névvel.) Ez a könyvtár a következő elemeket tartalmazza:

    * `pom.xml`- A projekt létrehozásához használt információkat és konfigurációs részleteket tartalmazó [Project Object Model (POM).](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)
    * src\main\java\org\apache\hadoop\examples: Tartalmazza az alkalmazás kódját.
    * src\test\java\org\apache\hadoop\examples: Az alkalmazás tesztjeit tartalmazza.

1. Távolítsa el a létrehozott példakódot. Törölje a létrehozott teszt- `AppTest.java`és `App.java` alkalmazásfájlokat, és írja be az alábbi parancsokat:

    ```cmd
    cd wordcountjava
    DEL src\main\java\org\apache\hadoop\examples\App.java
    DEL src\test\java\org\apache\hadoop\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>A projektobjektum-modell frissítése

A pom.xml fájl teljes körű https://maven.apache.org/pom.htmlhivatkozását lásd: . Nyissa `pom.xml` meg az alábbi parancs megadásával:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Függőségek hozzáadása

A `pom.xml`alkalmazásban adja hozzá `<dependencies>` a következő szöveget a szakaszba:

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

Ez határozza meg a szükséges &lt;könyvtárakat\>(az artifactId-ben &lt;\>felsoroltakat) egy adott verzióval (a verzióban felsoroltak). Fordításkor ezek a függőségek az alapértelmezett Maven-tárházból töltődnek le. A [Maven tárház keresés](https://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) segítségével további atekinthetéshez.

A `<scope>provided</scope>` azt mondja Maven, hogy ezeket a függőségeket nem kell csomagolni az alkalmazással, mivel azokat a HDInsight-fürt biztosítja futásidőben.

> [!IMPORTANT]
> A használt verziónak meg kell egyeznie a fürtön található Hadoop verziójával. A verziókról további információt a [HDInsight-összetevő verziószámozási](../hdinsight-component-versioning.md) dokumentuma tartalmaz.

### <a name="build-configuration"></a>Konfiguráció létrehozása

A Maven beépülő modulok lehetővé teszik a projekt építési szakaszainak testreszabását. Ez a szakasz beépülő modulok, erőforrások és egyéb build-konfigurációs beállítások hozzáadására szolgál.

Adja hozzá a `pom.xml` következő kódot a fájlhoz, majd mentse és zárja be a fájlt. Ennek a szövegnek `<project>...</project>` a fájl címkéiközött `</dependencies>` `</project>`kell lennie, például a és a között.

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

Ez a rész konfigurálja az Apache Maven Compiler Plugin és az Apache Maven Shade Plugin. A fordító beépülő modulja a topológia fordítására szolgál. Az árnyékbeépülő beépülő modul a Maven által készített JAR csomag licencduplikálásának megakadályozására szolgál. Ez a beépülő modul a HDInsight-fürt futási idején "ismétlődő licencfájlok" hibájának megelőzésére szolgál. A maven-shade-plugin `ApacheLicenseResourceTransformer` használata a megvalósítással megakadályozza a hibát.

A maven-shade-plugin is létrehoz egy uber jar, amely tartalmazza az alkalmazás által igényelt összes függőséget.

Mentse a `pom.xml` fájlt.

## <a name="create-the-mapreduce-application"></a>A MapReduce alkalmazás létrehozása

1. Új fájl létrehozásához és megnyitásához `WordCount.java`írja be az alábbi parancsot. Új fájl létrehozásához válassza az **Igen** lehetőséget a kérdésnél.

    ```cmd
    notepad src\main\java\org\apache\hadoop\examples\WordCount.java
    ```

2. Ezután másolja be az alábbi java kódot az új fájlba. Ezután zárja be a fájlt.

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

    Figyelje meg, `org.apache.hadoop.examples` hogy a `WordCount`csomag neve és az osztály neve . Ezeket a neveket a MapReduce feladat elküldésekor használja.

## <a name="build-and-package-the-application"></a>Az alkalmazás létrehozása és csomagolása

A `wordcountjava` könyvtárból a következő paranccsal hozzon létre egy JAR-fájlt, amely az alkalmazást tartalmazza:

```cmd
mvn clean package
```

Ez a parancs megtisztítja a korábbi buildösszetevőket, letölti a még nem telepített függőségeket, majd létrehozza és csomagolja az alkalmazást.

A parancs befejezése után `wordcountjava/target` a könyvtár `wordcountjava-1.0-SNAPSHOT.jar`egy .

> [!NOTE]
> A `wordcountjava-1.0-SNAPSHOT.jar` fájl egy uberjar, amely nem csak a WordCount feladatot tartalmazza, hanem a feladat által futásidőben igényelt függőségeket is.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>Töltse fel a JAR-t és futtasson feladatokat (SSH)

A következő `scp` lépésekkel másolja a JAR az elsődleges fő csomópont az Apache HBase HDInsight-fürtön. A `ssh` parancs ezután a fürthöz való csatlakozásra és a példa közvetlenül a főcsomópontra való futtatására szolgál.

1. Töltse fel az üveget a fürtbe. Cserélje `CLUSTERNAME` le a HDInsight-fürt nevét, majd írja be a következő parancsot:

    ```cmd
    scp target/wordcountjava-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Csatlakozzon a fürthöz. Cserélje `CLUSTERNAME` le a HDInsight-fürt nevét, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Az SSH-munkamenetben a következő paranccsal futtassa a MapReduce alkalmazást:

   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```

    Ez a parancs elindítja a WordCount MapReduce alkalmazást. A bemeneti `/example/data/gutenberg/davinci.txt`fájl , a `/example/data/wordcountout`kimeneti könyvtár pedig a. Mind a bemeneti fájl, mind a kimenet a fürt alapértelmezett tárolójában tárolódik.

1. A feladat befejezése után a következő paranccsal tekintse meg az eredményeket:

   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    Meg kell kapnia a szavak és a számlálók listáját, a következő höz hasonló értékekkel:

    ```output
    zeal    1
    zelus   1
    zenith  2
    ```

## <a name="next-steps"></a>További lépések

Ebben a dokumentumban megtanulta, hogyan fejleszthet Java MapReduce feladatot. A HDInsight egyéb munkamódszereit az alábbi dokumentumokból olvassa el.

* [Az Apache Hive használata a HDInsight segítségével](hdinsight-use-hive.md)
* [A MapReduce használata a HDInsightsegítségével](hdinsight-use-mapreduce.md)
* [Java fejlesztői központ](https://azure.microsoft.com/develop/java/)
