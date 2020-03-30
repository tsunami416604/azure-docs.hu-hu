---
title: MapReduce az Apache Hadoop segítségével a HDInsighton
description: Ismerje meg, hogyan futtathat Apache MapReduce feladatokat az Apache Hadoop on HDInsight-fürtökben.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 238f37cc1e8cbc8d3260693181d6e54ac5f592f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435708"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>A MapReduce használata a HDInsight-alapú Apache Hadoopban

Ismerje meg, hogyan futtatható mapreduce feladatok HDInsight-fürtökön.

## <a name="example-data"></a>Példaadatok

A HDInsight különböző példaadatkészleteket biztosít, `/example/data` amelyek `/HdiSamples` a és a címtárban tárolódnak. Ezek a könyvtárak a fürt alapértelmezett tárolójában vannak. Ebben a dokumentumban `/example/data/gutenberg/davinci.txt` használjuk a fájlt. Ez a fájl Leonardo da Vinci jegyzetfüzeteit tartalmazza.

## <a name="example-mapreduce"></a>Példa MapReduce

A HDInsight-fürt egy példa mapreduce szószámláló alkalmazás. Ez a példa `/example/jars/hadoop-mapreduce-examples.jar` a fürt alapértelmezett tárolóján található.

A következő Java-kód a `hadoop-mapreduce-examples.jar` fájlban található MapReduce alkalmazás forrása:

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

A saját MapReduce alkalmazások írására vonatkozó utasításokat a [Java MapReduce alkalmazások fejlesztése hdinsighthoz.](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

## <a name="run-the-mapreduce"></a>A MapReduce futtatása

A HDInsight különböző módszerekkel futtathatja a HiveQL-feladatokat. Az alábbi táblázat segítségével eldöntheti, hogy melyik módszer felel meg Önnek, majd kövesse a forgatókönyvhivatkozást.

| **Használja ezt a**... | **... hogy ezt** |  ... ebből az **ügyféloperációs rendszerből** |
|:--- |:--- |:--- |:--- |
| [Ssh](apache-hadoop-use-mapreduce-ssh.md) |A Hadoop parancs használata az **SSH-n** keresztül |Linux, Unix, Mac OS X vagy Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |A feladat távolról történő elküldése a **REST** használatával |Linux, Unix, Mac OS X vagy Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |A feladat távoli elküldése a **Windows PowerShell** használatával  |Windows |

## <a name="next-steps"></a>További lépések

Ha többet szeretne tudni a HDInsight adatainak munkálatairól, olvassa el az alábbi dokumentumokat:

* [Java MapReduce programok fejlesztése a HDInsight hoz](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Az Apache Hive használata a HDInsight segítségével](./hdinsight-use-hive.md)
