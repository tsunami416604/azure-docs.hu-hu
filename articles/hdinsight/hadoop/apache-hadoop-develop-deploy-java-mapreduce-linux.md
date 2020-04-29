---
title: Java-MapReduce létrehozása a Apache Hadoophoz – Azure HDInsight
description: Ismerje meg, hogyan hozhat létre egy Java-alapú MapReduce-alkalmazást az Apache Maven használatával, majd hogyan futtathatja a Hadoop az Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/16/2020
ms.openlocfilehash: a37a8bb45c11d5b74f3059a153806e3d083cf452
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76311954"
---
# <a name="develop-java-mapreduce-programs-for-apache-hadoop-on-hdinsight"></a>Java MapReduce-programok fejlesztése a HDInsight Apache Hadoop

Ismerje meg, hogyan hozhat létre egy Java-alapú MapReduce-alkalmazást az Apache Maven használatával, majd hogyan futtathatja Apache Hadoop az Azure HDInsight.

## <a name="prerequisites"></a>Előfeltételek

* A [Java Developer Kit (JDK) 8-as verziója](https://aka.ms/azure-jdks).

* Az [Apache Maven](https://maven.apache.org/download.cgi) megfelelően [van telepítve](https://maven.apache.org/install.html) az Apache-ban.  A Maven egy projekt-összeállítási rendszer Java-projektekhez.

## <a name="configure-development-environment"></a>A fejlesztési környezet konfigurálása

A cikkben használt környezet a Windows 10 rendszert futtató számítógép volt. A parancsok végrehajtása egy parancssorban történt, a különböző fájlok pedig a Jegyzettömb alkalmazásban lettek szerkesztve. Ennek megfelelően módosítsa a környezetét.

A parancssorba írja be az alábbi parancsokat egy működő környezet létrehozásához:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Maven-projekt létrehozása

1. Adja meg a következő parancsot egy **wordcountjava**nevű Maven-projekt létrehozásához:

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    Ez a parancs egy könyvtárat hoz létre a `artifactID` paraméterben megadott névvel (ebben a példában a**wordcountjava** ). Ez a könyvtár a következő elemeket tartalmazza:

    * `pom.xml`– A projekt felépítéséhez használt információkat és konfigurációs adatokat tartalmazó [Project Object Model (POM)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html) .
    * src\main\java\org\apache\hadoop\examples: az alkalmazás kódját tartalmazza.
    * src\test\java\org\apache\hadoop\examples: az alkalmazáshoz tartozó teszteket tartalmazza.

1. Távolítsa el a generált példa kódját. Törölje a generált teszt-és alkalmazásfájl `AppTest.java`-fájlokat `App.java` , és írja be az alábbi parancsokat:

    ```cmd
    cd wordcountjava
    DEL src\main\java\org\apache\hadoop\examples\App.java
    DEL src\test\java\org\apache\hadoop\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>A projekt-objektum modell frissítése

A Pom. xml fájl teljes referenciáját lásd: https://maven.apache.org/pom.html. A `pom.xml` megnyitásához írja be az alábbi parancsot:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Függőségek hozzáadása

A `pom.xml`alkalmazásban adja hozzá a következő szöveget `<dependencies>` a szakaszhoz:

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

Ez határozza meg a szükséges kódtárakat &lt;(\>a artifactId belül) egy adott verzióval &lt;(\>a verzióban felsorolva). A fordítás ideje alatt ezek a függőségek az alapértelmezett Maven-tárházból tölthetők le. További információkért a [Maven-tárház keresésében](https://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) olvashat.

A `<scope>provided</scope>` Maven azt jelzi, hogy ezek a függőségek nem tölthetők be az alkalmazásba, mivel a HDInsight-fürt futási időben megadja őket.

> [!IMPORTANT]
> A használt verziónak meg kell egyeznie a fürtben lévő Hadoop-verzióval. További információ a verziókról: HDInsight- [összetevő verziószámozási](../hdinsight-component-versioning.md) dokumentuma.

### <a name="build-configuration"></a>Konfiguráció létrehozása

A Maven beépülő moduljai lehetővé teszik a projekt összeállítási szakaszainak testreszabását. Ez a szakasz beépülő modulok, erőforrások és egyéb Build-konfigurációs beállítások hozzáadására szolgál.

Adja hozzá a következő kódot a `pom.xml` fájlhoz, majd mentse és zárjuk be a fájlt. Ennek a szövegnek a `<project>...</project>` címkén belül kell lennie a fájlban, például `</dependencies>` a `</project>`és a között.

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

Ez a szakasz az Apache Maven Compiler beépülő modult és az Apache Maven Shade beépülő modulját konfigurálja. A fordító beépülő modul a topológia fordítására szolgál. Az árnyékolt beépülő modullal megakadályozható a licencek duplikálása a Maven által készített JAR-csomagban. Ez a beépülő modul a HDInsight-fürtön futó "duplikált licencek" hibájának megakadályozására szolgál. A Maven-Shade-beépülő modullal a `ApacheLicenseResourceTransformer` megvalósítás megakadályozza a hibát.

A Maven-Shade-beépülő modul egy über jar-t is létrehoz, amely az alkalmazás által igényelt összes függőséget tartalmazza.

Mentse a `pom.xml` fájlt.

## <a name="create-the-mapreduce-application"></a>A MapReduce alkalmazás létrehozása

1. Új fájl `WordCount.java`létrehozásához és megnyitásához írja be az alábbi parancsot. Új fájl létrehozásához válassza az **Igen** lehetőséget.

    ```cmd
    notepad src\main\java\org\apache\hadoop\examples\WordCount.java
    ```

2. Ezután másolja és illessze be az alábbi Java-kódot az új fájlba. Ezután zárjuk be a fájlt.

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

    Figyelje meg, hogy a `org.apache.hadoop.examples` csomag neve, az osztály `WordCount`neve pedig. Ezeket a neveket a MapReduce-feladatok elküldésekor használja.

## <a name="build-and-package-the-application"></a>Az alkalmazás létrehozása és becsomagolása

A `wordcountjava` címtárból használja az alábbi parancsot egy olyan jar-fájl létrehozásához, amely tartalmazza az alkalmazást:

```cmd
mvn clean package
```

Ez a parancs törli az előző Build-összetevőket, letölti a még nem telepített függőségeket, majd felépíti és csomagolja ki az alkalmazást.

A parancs befejeződése után a `wordcountjava/target` könyvtár tartalmaz egy nevű `wordcountjava-1.0-SNAPSHOT.jar`fájlt.

> [!NOTE]
> A `wordcountjava-1.0-SNAPSHOT.jar` fájl egy uberjar, amely nem csak a WordCount feladatot tartalmazza, hanem olyan függőségeket is, amelyeket a feladatoknak futásidőben kell megadniuk.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>A JAR és a Run feladatok (SSH) feltöltése

A következő lépésekkel `scp` másolhatja a jar-t az Apache HBase elsődleges fő csomópontjára a HDInsight-fürtön. A `ssh` parancs ezután csatlakozik a fürthöz, és a példát közvetlenül a fő csomóponton futtatja.

1. Töltse fel a jar-t a fürtbe. Cserélje `CLUSTERNAME` le a értéket a HDInsight-fürt nevére, majd írja be a következő parancsot:

    ```cmd
    scp target/wordcountjava-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Kapcsolódjon a fürthöz. Cserélje `CLUSTERNAME` le a értéket a HDInsight-fürt nevére, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Az SSH-munkamenetben használja a következő parancsot a MapReduce alkalmazás futtatásához:

   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```

    Ez a parancs elindítja a WordCount MapReduce alkalmazást. A bemeneti fájl `/example/data/gutenberg/davinci.txt`a és a kimeneti könyvtár `/example/data/wordcountout`. A bemeneti fájl és a kimenet is a fürt alapértelmezett tárolójában tárolódik.

1. A feladatok befejezése után a következő paranccsal tekintheti meg az eredményeket:

   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    Meg kell kapnia a szavak és számok listáját, az alábbi szöveghez hasonló értékekkel:

    ```output
    zeal    1
    zelus   1
    zenith  2
    ```

## <a name="next-steps"></a>További lépések

Ebből a dokumentumból megtanulta, hogyan fejleszthet Java MapReduce-feladatot. A HDInsight használatával kapcsolatos egyéb módszerekről a következő dokumentumokban talál további információt.

* [Apache Hive használata a HDInsight](hdinsight-use-hive.md)
* [A MapReduce használata a HDInsight](hdinsight-use-mapreduce.md)
* [Java fejlesztői központ](https://azure.microsoft.com/develop/java/)
