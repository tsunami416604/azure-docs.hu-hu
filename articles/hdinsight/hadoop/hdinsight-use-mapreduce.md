---
title: A HDInsight az Apache Hadoop MapReduce
description: Ismerje meg, hogyan lehet Apache Hadoop MapReduce-feladatok futtatása a HDInsight-fürtök.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: c9a4a0944c51cb8c6f6d91cd153580a17a9b7898
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51632657"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>A HDInsight az Apache Hadoop MapReduce használata

Útmutató MapReduce-feladatok futtatása a HDInsight-fürtökön. Fedezze fel, hogy használható-e a MapReduce különböző módjait az alábbi táblázat segítségével a HDInsight:

| **Ezzel**... | **...fenti ehhez** | .. során ez **fürt operációs rendszerének** | ...from ez **ügyfél operációs rendszer** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |A Hadoop paranccsal keresztül **SSH** |Linux |Linux, Unix, Mac OS X vagy Windows |
| [REST](apache-hadoop-use-mapreduce-curl.md) |A feladat elküldéséhez távolról használatával **REST** (példák a cURL használatával) |Linux vagy Windows |Linux, Unix, Mac OS X vagy Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |A feladat elküldéséhez távolról használatával **Windows PowerShell** |Linux vagy Windows |Windows |

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>

## <a id="whatis"></a>Mit jelent a MapReduce

Az Apache Hadoop MapReduce feladatok, amelyek nagy mennyiségű adatot ír egy szoftveres keretrendszer. A bemeneti adatok van felosztva, amelyek független adattömböket. Minden egyes adattömbbel párhuzamos feldolgozása a fürtben található csomópontok között. Két függvényt tartalmaz egy MapReduce-feladatot:

* **Eseményleképező**: a bemeneti adatokat feldolgozó, elemzi azokat (általában a szűrési és rendezési műveleteket) és bocsát ki a rekord (kulcs-érték párok)

* **Nyomáscsökkentő**: Leképezőjét által kibocsátott rekordokat használ fel, és, amely kisebb, összesített eredményt hoz létre az Eseményleképező adatok összegzési műveletet hajt végre

Egy alapszintű szószámlálási számláló MapReduce feladat például az alábbi ábra mutatja be:

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]

Ez a feladat kimenete a szöveg a következő minden szó hány alkalommal számát.

* A teljesítményleképező minden sor a bemeneti szöveg bemenetként fogadja, és megszakítja a szó be. Ez bocsát ki egy kulcs/érték pár minden alkalommal, amikor egy szót akkor fordul elő, a word, az azt követő 1. A kimenet van rendezve, mielőtt elküldené nyomáscsökkentő.
* A nyomáscsökkentő átlagot számolna ezeket egyéni száma minden egyes szó, és egy egyetlen kulcs/érték pár, amely tartalmazza a szó egyezik meg az előfordulások és bocsát ki.

A MapReduce implementálható különböző nyelveken. Java megvalósítása a leggyakrabban használt, és a jelen dokumentum bemutatási célokra szolgál.

## <a name="development-languages"></a>Fejlesztői nyelvek

Nyelvek és keretrendszerek, a Java és a Java virtuális gép alapuló közvetlenül, egy MapReduce-feladatot is futott. Az itt bemutatott példában egy olyan Java MapReduce-alkalmazás. Nem – Java nyelven, például C#, Python vagy önálló végrehajtható fájlok, kell használnia **Hadoop streamelési**.

Hadoop streamelési keresztül kommunikál a a teljesítményleképező és nyomáscsökkentő STDIN és STDOUT. A hozzárendelést és nyomáscsökkentő STDIN egyszerre egy vonal-adatok olvasását és STDOUT kiírhatja a kimenetet. Az egyes sorok olvasása vagy leképező és nyomáscsökkentő által kibocsátott egy kulcs/érték pár, amelyet tabulátorkarakter formátumúnak kell lennie:

    [key]/t[value]

További információkért lásd: [Hadoop Streamelési](http://hadoop.apache.org/docs/r1.2.1/streaming.html).

Példák Stream használata a HDInsight a hadoop a következő dokumentumokban talál:

* [C# MapReduce feladatok fejlesztése](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Python MapReduce feladatok fejlesztése](apache-hadoop-streaming-python.md)

## <a id="data"></a>Példa adatok

HDInsight biztosít különböző példa adatkészletek, amelyek tárolása a `/example/data` és `/HdiSamples` könyvtár. Ezek a könyvtárak a a fürt alapértelmezett tárolója találhatók. Ebben a dokumentumban használjuk a `/example/data/gutenberg/davinci.txt` fájlt. Ez a fájl Leonardo Da Vinci jegyzetfüzet tartalmazza.

## <a id="job"></a>A példában a MapReduce

A MapReduce word-count alkalmazás például a HDInsight-fürt része. Ebben a példában a következő helyen található `/example/jars/hadoop-mapreduce-examples.jar` a fürt alapértelmezett tárolására.

A következő Java-kódot az a MapReduce-alkalmazás található forrása a `hadoop-mapreduce-examples.jar` fájlt:

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

A saját MapReduce-alkalmazások írására, útmutatásért lásd a következő dokumentumokat:

* [A HDInsight Java MapReduce-alkalmazások fejlesztése](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [A HDInsight Python MapReduce-alkalmazások fejlesztése](apache-hadoop-streaming-python.md)

## <a id="run"></a>A MapReduce futtatása

HDInsight HiveQL feladatok futtatásához különböző módszerek használatával. A következő táblázat segítségével döntse el, melyik módszer a legmegfelelőbb Önnek, majd kövesse a hivatkozást bemutató.

| **Ezzel**... | **...fenti ehhez** | .. során ez **fürt operációs rendszerének** | ...from ez **ügyfél operációs rendszer** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |A Hadoop paranccsal keresztül **SSH** |Linux |Linux, Unix, Mac OS X vagy Windows |
| [A curl](apache-hadoop-use-mapreduce-curl.md) |A feladat elküldéséhez távolról használatával **REST** |Linux vagy Windows |Linux, Unix, Mac OS X vagy Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |A feladat elküldéséhez távolról használatával **Windows PowerShell** |Linux vagy Windows |Windows |

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="nextsteps"></a>Következő lépések

A HDInsight adatok kezelésével kapcsolatos további tudnivalókért tekintse meg a következő dokumentumokat:

* [Java MapReduce programok fejlesztése a HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Python-streamelés HDInsight MapReduce-programok fejlesztése](apache-hadoop-streaming-python.md)

* [A Hive használata a HDInsightban][hdinsight-use-hive]

* [A Pig használata a HDInsightban][hdinsight-use-pig]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-pig]:hdinsight-use-pig.md


[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif
