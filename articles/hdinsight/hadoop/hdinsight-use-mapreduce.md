---
title: A hdinsight Hadoop MapReduce |} Microsoft Docs
description: "Útmutató a Hadoop MapReduce-feladatok futtatása a HDInsight-fürtök."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7f321501-d62c-4ffc-b5d6-102ecba6dd76
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/20/2017
ms.author: larryfr
ms.openlocfilehash: b267f5ce5ec76a89327ac58ca76895f8bcc4696b
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="use-mapreduce-in-hadoop-on-hdinsight"></a>A HDInsight Hadoop MapReduce használata

Útmutató a HDInsight-fürtökön MapReduce-feladatok futtatásához. A különböző módszereket, hogy használható-e a MapReduce felderítése a következő táblázat segítségével a hdinsight eszközzel:

| **Ezzel**... | **...fenti ehhez** | és mivel ez **fürt operációs rendszer** | ...from ez **ügyfél operációs rendszer** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |A Hadoop paranccsal keresztül **SSH** |Linux |Linux, Unix, Mac OS X vagy Windows |
| [REST](apache-hadoop-use-mapreduce-curl.md) |A feladat elküldéséhez távolról használatával **REST** (példák használata cURL) |Linux- vagy Windows |Linux, Unix, Mac OS X vagy Windows |
| [A Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |A feladat elküldéséhez távolról használatával **Windows PowerShell** |Linux- vagy Windows |Windows |
| [A távoli asztal](apache-hadoop-use-mapreduce-remote-desktop.md) (HDInsight 3.2-es és 3.3-as) |A Hadoop paranccsal keresztül **távoli asztal** |Windows |Windows |

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="whatis"></a>Mi az a MapReduce

Hadoop-MapReduce egy szoftveres keretrendszer feladatok, amelyek feldolgozzák a nagy mennyiségű adat írásához. A bemeneti adatok független adattömbökbe van osztva. Egyes adattömbök feldolgozása párhuzamosan a fürt csomópontjai között. A MapReduce feladatot két funkciókat foglalja magában:

* **Eseményleképező**: bemeneti adatforrástól, elemzi azokat (általában a szűrési és rendezési műveletek) és bocsát ki a listának (kulcs-érték párok)

* **Nyomáscsökkentő**: a leképező által kibocsátott rekordokat használ fel, és hozza létre a kisebb, kombinált eredményeként a leképező adatokból összefoglaló műveletet hajt végre

Egy alapszintű word száma MapReduce feladatot például az alábbi ábrán látható:

![HDI. WordCountDiagram][image-hdi-wordcountdiagram]

Ez a feladat eredménye hányszor minden szó történt a szöveges számát.

* A leképező bemeneti adatokként a bemeneti szöveg soronként vesz igénybe, és elindítja azt szavakat. Az megfelelően kibocsát egy kulcs/érték pár minden alkalommal, amikor egy word akkor fordul elő, a Word követi egy 1. A kimeneti rendezése nyomáscsökkentő felé.
* A nyomáscsökkentő ezeket a minden egyes szó egyedi számokat összegzi, és megfelelően kibocsát egy egyetlen kulcs/érték pár, amely tartalmazza a word, az előfordulások összege követ.

MapReduce különböző nyelveken valósítható meg. Java leggyakoribb megvalósítása, és a jelen dokumentum bemutatási célokra szolgál.

## <a name="development-languages"></a>Fejlesztési nyelveket

Nyelvet vagy keretrendszert, Java és a Java virtuális gépen alapuló közvetlenül, a MapReduce feladatot is futott. Ebben a dokumentumban bemutatott példában egy Java-MapReduce-alkalmazás. Nem-Java nyelven, például a C#, Python vagy önálló végrehajtható fájlok, a Hadoop streamelési kell használnia.

Hadoop streamelési keresztül kommunikál a hozzárendelést és nyomáscsökkentő STDIN és a STDOUT. A leképező nyomáscsökkentő STDIN egyszerre egy sor adatokat olvasni és írni a kimeneti STDOUT. Minden sor olvasása vagy a hozzárendelést és nyomáscsökkentő által kibocsátott tabulátor határolt egy kulcs/érték pár formátumban kell lennie:

    [key]/t[value]

További információkért lásd: [Hadoop Streamelési](http://hadoop.apache.org/docs/r1.2.1/streaming.html).

Hadoop-Stream és a HDInsight együttes használatával tekintse meg a következő dokumentumokat:

* [C# MapReduce-feladatok fejlesztése](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Python-MapReduce-feladatok fejlesztése](apache-hadoop-streaming-python.md)

## <a id="data"></a>Példa adatok

A HDInsight lehetővé különböző példa adatkészletek, amelyek tárolása a `/example/data` és `/HdiSamples` könyvtár. Ezeket a könyvtárakat az alapértelmezett tároló, a fürt szerepelnek. Ebben a dokumentumban használjuk a `/example/data/gutenberg/davinci.txt` fájlt. Ez a fájl Leonardo Da Vinci jegyzetfüzet tartalmazza.

## <a id="job"></a>Példa MapReduce

MapReduce word-count alkalmazás például a HDInsight-fürt része. Ebben a példában a következő helyen található `/example/jars/hadoop-mapreduce-examples.jar` az alapértelmezett tároló, a fürt számára.

A következő Java-kódot egy a MapReduce alkalmazás szerepel a `hadoop-mapreduce-examples.jar` fájlt:

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

A saját MapReduce alkalmazások írását, lásd: a következő dokumentumokat:

* [A HDInsight Java MapReduce-alkalmazások fejlesztéséhez](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [A HDInsight Python MapReduce alkalmazások fejlesztéséhez](apache-hadoop-streaming-python.md)

## <a id="run"></a>A MapReduce futtatása

A HDInsight HiveQL feladatok futtatásához különböző módszerekkel. A következő táblázat segítségével döntse el, melyik módszert részesíti az Ön számára legmegfelelőbb, majd kövesse a hivatkozást útmutatást.

| **Ezzel**... | **...fenti ehhez** | és mivel ez **fürt operációs rendszer** | ...from ez **ügyfél operációs rendszer** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |A Hadoop paranccsal keresztül **SSH** |Linux |Linux, Unix, Mac OS X vagy Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |A feladat elküldéséhez távolról használatával **REST** |Linux- vagy Windows |Linux, Unix, Mac OS X vagy Windows |
| [A Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |A feladat elküldéséhez távolról használatával **Windows PowerShell** |Linux- vagy Windows |Windows |
| [A távoli asztal](apache-hadoop-use-mapreduce-remote-desktop.md) (HDInsight 3.2-es és 3.3-as) |A Hadoop paranccsal keresztül **távoli asztal** |Windows |Windows |

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="nextsteps"></a>Következő lépések

A HDInsight-adatokkal végzett munka kapcsolatos további tudnivalókért tekintse meg a következő dokumentumokat:

* [Java-MapReduce programok fejlesztése a HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [A HDInsight MapReduce programok streaming Python fejlesztése](apache-hadoop-streaming-python.md)

* [A Hive használata a HDInsightban][hdinsight-use-hive]

* [A Pig használata a HDInsightban][hdinsight-use-pig]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-pig]:hdinsight-use-pig.md


[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif
