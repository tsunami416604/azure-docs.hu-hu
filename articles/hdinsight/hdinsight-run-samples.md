---
title: "Futtassa a Hadoop-minták a HDInsight - Azure |} Microsoft Docs"
description: "Ismerkedés az Azure HDInsight szolgáltatással megadott a mintákkal. Amely MapReduce programok adatok fürtökön PowerShell-parancsfájlok használata."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: bf76d452-abb4-4210-87bd-a2067778c6ed
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 6e4dbf1fa32f916a206e4f8d796218ab809835ad
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="run-hadoop-mapreduce-samples-in-windows-based-hdinsight"></a>Windows-alapú HDInsight Hadoop-MapReduce minták futtatása
[!INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Segítségnyújtáshoz elindított futó MapReduce-feladatok az Azure HDInsight Hadoop-fürtök biztosított mintákat mutat be. Ezeket a mintákat elérhetővé válnak a felügyelt HDInsight-fürtök az Ön által létrehozott minden egyes. Ezek a minták futtatása megismerkedhet a Hadoop-fürtök feladat futtatható Azure PowerShell-parancsmagok használatával.

* [**Word-count**][hdinsight-sample-wordcount]: szövegfájl word előfordulások száma.
* [**C# szószámot streaming**][hdinsight-sample-csharp-streaming]: a Hadoop streamelési felületén szövegfájl word előfordulások száma.
* [**A pi négyzetgyökének**][hdinsight-sample-pi-estimator]: használja a statisztikai (látszólagos Monte Carlo) módszer a pi értékét.
* [**10 GB-os Graysort**][hdinsight-sample-10gb-graysort]: egy általános célú GraySort futtatnak egy 10 GB-os fájl HDInsight használatával. Három feladat futtatásához: létrehozza az adatokat, Terasort adatait, és annak ellenőrzéséhez, hogy az adatok megfelelően lett rendezve Teravalidate Teragen.

> [!NOTE]
> A forráskód függelékében található.

Sokkal további dokumentáció létezik-e az interneten a Hadoop-kapcsolódó technológiákkal, például Java-alapú MapReduce programozási és adatfolyam- és a használt Windows PowerShell-parancsmagokkal dokumentáció parancsfájlok. Ezekkel az erőforrásokkal kapcsolatos további információkért lásd:

* [A hdinsight Hadoop Java MapReduce programok fejlesztése](hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Hadoop-feladatok elküldése a HDInsightban](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Az Azure HDInsight bemutatása][hdinsight-introduction]

Napjainkban sokan válassza a Hive és a Pig MapReduce keresztül.  További információkért lásd:

* [A Hive hdinsight használata](hadoop/hdinsight-use-hive.md)
* [A Pig használata a Hdinsightban](hadoop/hdinsight-use-pig.md)

**Előfeltételek**:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **HDInsight-fürtök**. A különböző módszereket, amelyben a fürtök hozhatók létre, lásd: [Hadoop létrehozása a HDInsight-fürtök](hdinsight-hadoop-provision-linux-clusters.md).
* **Munkaállomás Azure PowerShell-lel**.

    > [!IMPORTANT]
    > A HDInsight-erőforrások Azure Service Managerrel történő kezelésének Azure PowerShell-támogatása **elavult**, így 2017. január 1-től megszűnik. A jelen dokumentumban leírt lépések az új HDInsight-parancsmagokat használják, amelyek az Azure Resource Managerrel működnek.
    >
    > Kövesse a [Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs) Azure PowerShell legújabb verziójának telepítéséhez. Ha vannak olyan parancsprogramjai, amelyeket módosítani kell az új parancsmagok együtt használható Azure Resource Manager, lásd: [Fejlesztőeszközök Azure Resource Manager-alapú HDInsight-fürtök történő](hdinsight-hadoop-development-using-azure-resource-manager.md).

## <a name="hdinsight-sample-wordcount"></a>Word-count - Java
Elküldeni a MapReduce-projekt, először hoz létre a MapReduce feladat definíciójához. A feladat definíciójához, adja meg a MapReduce program jar-fájlra, valamint a jar-fájlra, amely az helyének **wasb:///example/jars/hadoop-mapreduce-examples.jar**, az argumentumok és az osztály nevét.  A wordcount MapReduce program két argumentummal: a forrásfájl, amellyel szavak, és a kimeneti helyének száma.

A forráskód megtalálhatók a [függelék](#apendix-a---the-word-count-MapReduce-program-in-java).

Az eljárás egy Java MapReduce fejlődő program című - [fejlesztése Java MapReduce programok a Hadoop a Hdinsightban](hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)

**Elküldeni a MapReduce feladatot word száma**

1. Nyissa meg **Windows PowerShell ISE**. Útmutatásért lásd: [telepítse és konfigurálja az Azure Powershellt][powershell-install-configure].
2. Illessze be a következő PowerShell-parancsfájlt:

    ```powershell
    $subscriptionName = "<Azure Subscription Name>"
    $resourceGroupName = "<Resource Group Name>"
    $clusterName = "<HDInsight cluster name>"             # HDInsight cluster name

    Select-AzureRmSubscription -SubscriptionName $subscriptionName

    # Define the MapReduce job
    $mrJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "wordcount" `
                                -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

    # Submit the job and wait for job completion
    $cred = Get-Credential -Message "Enter the HDInsight cluster HTTP user credential:"
    $mrJob = Start-AzureRmHDInsightJob `
                        -ResourceGroupName $resourceGroupName `
                        -ClusterName $clusterName `
                        -HttpCredential $cred `
                        -JobDefinition $mrJobDefinition

    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -HttpCredential $cred `
        -JobId $mrJob.JobId

    # Get the job output
    $cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
    $defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount)[0].Value
    $defaultStorageContainer = $cluster.DefaultStorageContainer

    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -HttpCredential $cred `
        -DefaultStorageAccountName $defaultStorageAccount `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultStorageContainer  `
        -JobId $mrJob.JobId `
        -DisplayOutputType StandardError

    # Download the job output to the workstation
    $storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey
    Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    # Display the output file
    cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"
    ```

    A MapReduce feladatot nevű fájlt hoz létre *rész-r-00000*, amely szavak és a számát tartalmazza. A parancsfájl használja a **findstr** paranccsal listát készíthet a szavakat tartalmazó *"nincs"*.
3. Az első három változók értékét, és futtassa a parancsfájlt.

## <a name="hdinsight-sample-csharp-streaming"></a>Word-count - C# adatfolyam
Hadoop streamelési API MapReduce, így térkép írása, és csökkentheti a funkciók Java kívül más nyelveken is biztosít.

> [!NOTE]
> A jelen oktatóanyagban szereplő lépések csak a Windows-alapú HDInsight-fürtök esetében érvényes. Például a Linux-alapú HDInsight-fürtök adatfolyamként való továbbítására, [programok streaming hdinsight fejlesztése Python](hadoop/apache-hadoop-streaming-python.md).

A példában a hozzárendelést és a nyomáscsökkentő a végrehajtható fájlok, hogy olvassa a bemeneti [stdin] [ stdin-stdout-stderr] (--soronként) és a kibocsátás kimenet [stdout] [ stdin-stdout-stderr]. A program megjeleníti a szövegben a szavakat.

Ha egy végrehajtható fájl van megadva, az **mappers**, minden leképező feladat különálló folyamatként indít a végrehajtható fájlt, amikor a leképező inicializálva van. A leképező feladat fut, azt a bemeneti alakítja sorok, és a sorok hírcsatornák a [stdin] [ stdin-stdout-stderr] a folyamat.

Időközben a leképező sor alapú kimenetét a folyamat az stdout adatsorból gyűjti. Minden egyes sorban alakítja a kulcs/érték pár, amelyek a leképező eredményének gyűjtése történik. Alapértelmezés szerint egy vonal, akár az első lap karakter előtag a kulcs, és a sor (kivéve a lapon megadott) további része az az érték. Ha a sorban nincs lapon karakter, teljes sor a kulcs minősül, és az értéke null.

Ha egy végrehajtható fájl van megadva, az **szűkítő**, minden nyomáscsökkentő feladatot indít a végrehajtható fájl különálló folyamatként, amikor a nyomáscsökkentő inicializálva van. A nyomáscsökkentő feladat fut, a bemeneti kulcs/érték párok alakítja sorok, és azt a sorok hírcsatornák a [stdin] [ stdin-stdout-stderr] a folyamat.

Időközben a nyomáscsökkentő gyűjti a sor alapú kimenetét a [stdout] [ stdin-stdout-stderr] a folyamat. Átalakítja soronként egy kulcs/érték pár, amelyek a nyomáscsökkentő eredményének gyűjtése történik. Alapértelmezés szerint egy vonal, akár az első lap karakter előtag a kulcs, és a sor (kivéve a lapon megadott) további része az az érték.

**Elküldeni egy C# streaming word-count feladat**

* Hajtsa végre az [Word-count - Java](#word-count-java), és a feladat definíciójához cserélje le a következő sort:

    ```powershell
    $mrJobDefinition = New-AzureRmHDInsightStreamingMapReduceJobDefinition `
                            -Files "/example/apps/cat.exe","/example/apps/wc.exe" `
                            -Mapper "cat.exe" `
                            -Reducer "wc.exe" `
                            -InputPath "/example/data/gutenberg/davinci.txt" `
                            -OutputPath "/example/data/StreamingOutput/wc.txt"
    ```

    A kimeneti fájl a következők:

        example/data/StreamingOutput/wc.txt/part-00000

## <a name="hdinsight-sample-pi-estimator"></a>A PI négyzetgyökének
A pi négyzetgyökének használja a statisztikai (látszólagos Monte Carlo) módszer a pi értékét. Véletlenszerű helyezni egy egység pontok négyzetes is tartoznak a kör területére egyenlő valószínűséggel belül, hogy szögletes ütemtervben kör pi/4. A pi értékét becsülhető 4R, ahol R az pontok, amelyek a teljes számú négyzetét belüli pontra körben aránya az értékétől. Minél nagyobb a mintában használt pontok, annál pontosabb becslést van.

Ez a minta a megadott parancsfájl egy Hadoop jar feladatot, amelynek beállítása legfeljebb 16 maps, amelyek mindegyike szükséges számítási 10 millió minta pontok által a paraméterértékek értékű futtassa. A paramétert értékek akkor módosíthatók pi becsült értékének növelése érdekében. Összehasonlításul az első 10 tizedesjegyek pi 3.1415926535.

**A pi négyzetgyökének feladat elküldése**

* Hajtsa végre az [Word-count - Java](#word-count-java), és a feladat definíciójához cserélje le a következő sort:

    ```powershell
    $mrJobJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "pi" `
                                -Arguments "16", "10000000"
    ```

## <a name="hdinsight-sample-10gb-graysort"></a>10 GB-os Graysort
Ez a minta egy mérsékelt 10GB adatot használ, így viszonylag gyorsan futtatása. A MapReduce alkalmazások Owen O'Malley és Arun Murthy, amely az általános célú ("daytona") terabájt éves rendezési teljesítményteszt nyert 2009 sebességet 0.578 TB/perc (173 percben 100 TB) használja. Erről és más rendezési referenciaalapok további információkért lásd: a [Sortbenchmark](http://sortbenchmark.org/) hely.

A példa a MapReduce programok három különböző használja:

1. **TeraGen** a MapReduce program, amely a sorok rendezése adatok létrehozásához használhatja.
2. **TeraSort** MapReduce-minták a bemeneti adatok és rendezze az adatokat egy teljes rendelés segítségével. TeraSort MapReduce funkciók, kivéve olyan egyéni particionálóval, amely minden csökkentse kulcs tartományának megadása mintát N-1 kulcsok rendezett listáját használó szabványos rendezési. Ebben az esetben, minden kulcsok ilyen mintát [i-1] < kulcs = < minta [i] kerülnek i csökkentése érdekében. Ez garantálja, hogy a kimenetének csökkentése i segédanyagokra-nál kisebb kimenete csökkentése i + 1.
3. **TeraValidate** a MapReduce program, amely ellenőrzi, hogy a kimeneti globálisan rendezett. Fájlonként egy leképezést a kimeneti könyvtár hozna létre, és minden leképezés biztosítja, hogy minden kulcs kisebb vagy egyenlő, mint az előzőt. A térkép funkció is, ami az első és utolsó kulcsok minden egyes fájl rögzíti, és a reduce függvény biztosítja, hogy fájl i első kulcsát nagyobb, mint az utolsó fájl i-1 kulcsa. Problémák a reduce kimeneteként jelenti a kulcsokkal, amelyek nem megfelelő sorrendben.

A bemeneti és kimeneti formátumot, mindhárom alkalmazás által használt beolvassa és a szöveges fájlt a megfelelő formátumban. A reduce kimenetét van a replikáció értéke 1, 3, alapértelmezett helyett, mivel a referenciaalap környezetével nem szükséges, hogy a kimeneti adatok továbbítása több csomópont replikálni.

A mintát, minden egyes bevezetése ismertetett MapReduce-programok három feladatok szükségesek:

1. Az adatok rendezése futtatásával létrehozása a **TeraGen** MapReduce feladatot.
2. Az adatok rendezéséhez futtatásával a **TeraSort** MapReduce feladatot.
3. Győződjön meg arról, hogy az adatok rendelkezik megfelelően vannak rendezve futtatásával a **TeraValidate** MapReduce feladatot.

**A feladatok küldéséhez**

* Hajtsa végre az [Word-count - Java](#word-count-java), és használja a következő feladatdefiníciók:

    ```powershell
    $teragen = New-AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "teragen" `
                                -Arguments "-Dmapred.map.tasks=50", "100000000", "/example/data/10GB-sort-input"

    $terasort = New-AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "terasort" `
                                -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-input", "/example/data/10GB-sort-output"

    $teravalidate = New-AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "teravalidate" `
                                -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-output", "/example/data/10GB-sort-validate"
    ```

## <a name="next-steps"></a>Következő lépések
Ez a cikk és a cikkek a minták mindegyikén megtudta, hogyan Azure PowerShell használatával a HDInsight-fürtökkel minták futtatásához. Oktatóanyagok Pig, a Hive és a MapReduce használata a hdinsight eszközzel a következő témakörökben található:

* [Hadoop használatának megkezdésében a hdinsight Hive elemzéséhez mobil kézibeszélő használata][hdinsight-get-started]
* [A Pig használata a HDInsight Hadoop][hdinsight-use-pig]
* [A Hive használata a hdinsight Hadoop][hdinsight-use-hive]
* [Küldje el a Hadoop-feladatokat a Hdinsightban][hdinsight-submit-jobs]
* [Az Azure HDInsight SDK-dokumentáció][hdinsight-sdk-documentation]

## <a name="appendix-a---the-word-count-source-code"></a>A függelék – kódot Word száma

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

## <a name="appendix-b---the-word-count-streaming-source-code"></a>B függelék – a forráskód streaming szószámot
A MapReduce program cat.exe alkalmazása egy felhasználói kezelőfelületet, hogy adatfolyamként küldje el a szöveget a konzol és a dokumentum átvitt szavak számát a reduce felület wc.exe alkalmazása. A hozzárendelést és a nyomáscsökkentő karakterek,--soronként, az adatfolyamból beolvasott szabványos bemeneti (stdin), és a szabványos kimeneti adatfolyam (stdout) írni.

```csharp
// The source code for the cat.exe (Mapper).

using System;
using System.IO;

namespace cat
{
    class cat
    {
        static void Main(string[] args)
        {
            if (args.Length > 0)
            {
                Console.SetIn(new StreamReader(args[0]));
            }

            string line;
            char[] separators = { ' ', '\n'};
            while ((line = Console.ReadLine()) != null)
            {
                string[] words = line.Split(separators);
                foreach (var word in words)
                {
                    Console.WriteLine("{0}\t1", word);
                }
            }
        }
    }
}
```

A leképező a fájlban lévő kódot cat.cs használ egy [StreamReader] [ streamreader] olvassa el a karaktereket a bejövő streamből a konzolt, majd a statikus astandardkimenetiadatfolyambavalóírásaazadatfolyam-objektum[ Console.WriteLine("a(z)] [ console-writeline] metódust.

```csharp
// The source code for wc.exe (Reducer) is:

using System;
using System.IO;
using System.Linq;
using System.Collections;

namespace wc
{
    class wc
    {
        static void Main(string[] args)
        {
            string line;

            if (args.Length > 0)
            {
                Console.SetIn(new StreamReader(args[0]));
            }

            Hashtable wordCount = new Hashtable();
            while ((line = Console.ReadLine()) != null)
            {
                string[] words = line.Split('\t');

                string key = words[0];

                if (wordCount.ContainsKey(key) == true)
                {
                    int n = Convert.ToInt32(wordCount[key]);
                    wordCount[key] = Convert.ToString(n + 1);
                }
                else
                {
                    wordCount[key] = words[1];
                }
            }
            foreach (var key in wordCount.Keys)
            {
                Console.WriteLine("{0} {1}", key, wordCount[key]);
            }
        }
    }
}
```

A nyomáscsökkentő a fájlban lévő kódot wc.cs használ egy [StreamReader] [ streamreader] karakterek az adatfolyamból beolvasott szabványos bemeneti, kimeneti a cat.exe hozzárendelő már objektumot. Mivel a karakterből állhat, és olvassa be a [Console.WriteLine("a(z)] [ console-writeline] módszer, megszámlálja a szavakat alapján szóközök és a sor végén karakterek minden szó végén. Ezután ír az összes a szabványos kimeneti adatfolyam a [Console.WriteLine("a(z)] [ console-writeline] metódust.

## <a name="appendix-c---the-pi-estimator-source-code"></a>C függelék – a Pi négyzetgyökének forráskód
A pi négyzetgyökének Java-kódot, amely tartalmazza a hozzárendelést és nyomáscsökkentő funkciók az alábbi hálózatfelügyeleti érhető el. A leképező program hoz létre a megadott számú véletlenszerű helyezni egy egységre négyszög pontok, és majd megszámlálása ezen pontok, amelyek a körben. A nyomáscsökkentő program pontok által a mappers megszámlált összesít, és ezután becslése a képlet 4R, ahol R a teljes számú négyzetét belüli pontra körben számítanak pontok száma aránya a pi értékét.

```java
/**
* Licensed to the Apache Software Foundation (ASF) under one
* or more contributor license agreements. See the NOTICE file
* distributed with this work for additional information
* regarding copyright ownership. The ASF licenses this file
* to you under the Apache License, Version 2.0 (the
* "License"); you may not use this file except in compliance
* with the License. You may obtain a copy of the License at
*
* http://www.apache.org/licenses/LICENSE-2.0
*
* Unless required by applicable law or agreed to in writing, software
* distributed under the License is distributed on an "AS IS" BASIS,
* WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or     implied.
* See the License for the specific language governing permissions and
* limitations under the License.
*/

package org.apache.hadoop.examples;

import java.io.IOException;
import java.math.BigDecimal;
import java.util.Iterator;

import org.apache.hadoop.conf.Configured;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.BooleanWritable;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.SequenceFile;
import org.apache.hadoop.io.Writable;
import org.apache.hadoop.io.WritableComparable;
import org.apache.hadoop.io.SequenceFile.CompressionType;
import org.apache.hadoop.mapred.FileInputFormat;
import org.apache.hadoop.mapred.FileOutputFormat;
import org.apache.hadoop.mapred.JobClient;
import org.apache.hadoop.mapred.JobConf;
import org.apache.hadoop.mapred.MapReduceBase;
import org.apache.hadoop.mapred.Mapper;
import org.apache.hadoop.mapred.OutputCollector;
import org.apache.hadoop.mapred.Reducer;
import org.apache.hadoop.mapred.Reporter;
import org.apache.hadoop.mapred.SequenceFileInputFormat;
import org.apache.hadoop.mapred.SequenceFileOutputFormat;
import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;

//A Map-reduce program to estimate the value of Pi
//using quasi-Monte Carlo method.
//
//Mapper:
//Generate points in a unit square
//and then count points inside/outside of the inscribed circle of the square.
//
//Reducer:
//Accumulate points inside/outside results from the mappers.
//Let numTotal = numInside + numOutside.
//The fraction numInside/numTotal is a rational approximation of
//the value (Area of the circle)/(Area of the square),
//where the area of the inscribed circle is Pi/4
//and the area of unit square is 1.
//Then, Pi is estimated value to be 4(numInside/numTotal).
//

public class PiEstimator extends Configured implements Tool {
//tmp directory for input/output
static private final Path TMP_DIR = new Path(
PiEstimator.class.getSimpleName() + "_TMP_3_141592654");

//2-dimensional Halton sequence {H(i)},
//where H(i) is a 2-dimensional point and i >= 1 is the index.
//Halton sequence is used to generate sample points for Pi estimation.
private static class HaltonSequence {
// Bases
static final int[] P = {2, 3};
//Maximum number of digits allowed
static final int[] K = {63, 40};

private long index;
private double[] x;
private double[][] q;
private int[][] d;

//Initialize to H(startindex),
//so the sequence begins with H(startindex+1).
HaltonSequence(long startindex) {
index = startindex;
x = new double[K.length];
q = new double[K.length][];
d = new int[K.length][];
for(int i = 0; i < K.length; i++) {
q[i] = new double[K[i]];
d[i] = new int[K[i]];
}

for(int i = 0; i < K.length; i++) {
long k = index;
x[i] = 0;

for(int j = 0; j < K[i]; j++) {
q[i][j] = (j == 0? 1.0: q[i][j-1])/P[i];
d[i][j] = (int)(k % P[i]);
k = (k - d[i][j])/P[i];
x[i] += d[i][j] * q[i][j];
}
}
}

//Compute next point.
//Assume the current point is H(index).
//Compute H(index+1).
//@return a 2-dimensional point with coordinates in [0,1)^2
double[] nextPoint() {
index++;
for(int i = 0; i < K.length; i++) {
for(int j = 0; j < K[i]; j++) {
d[i][j]++;
x[i] += q[i][j];
if (d[i][j] < P[i]) {
break;
}
d[i][j] = 0;
x[i] -= (j == 0? 1.0: q[i][j-1]);
}
}
return x;
}
}

//Mapper class for Pi estimation.
//Generate points in a unit square and then
//count points inside/outside of the inscribed circle of the square.
public static class PiMapper extends MapReduceBase
implements Mapper<LongWritable, LongWritable, BooleanWritable, LongWritable> {

//Map method.
//@param offset samples starting from the (offset+1)th sample.
//@param size the number of samples for this map
//@param out output {ture->numInside, false->numOutside}
//@param reporter
public void map(LongWritable offset,
LongWritable size,
OutputCollector<BooleanWritable, LongWritable> out,
Reporter reporter) throws IOException {

final HaltonSequence haltonsequence = new HaltonSequence(offset.get());
long numInside = 0L;
long numOutside = 0L;

for(long i = 0; i < size.get(); ) {
//generate points in a unit square
final double[] point = haltonsequence.nextPoint();

//count points inside/outside of the inscribed circle of the square
final double x = point[0] - 0.5;
final double y = point[1] - 0.5;
if (x*x + y*y > 0.25) {
numOutside++;
} else {
numInside++;
}

//report status
i++;
if (i % 1000 == 0) {
reporter.setStatus("Generated " + i + " samples.");
}
}

//output map results
out.collect(new BooleanWritable(true), new LongWritable(numInside));
out.collect(new BooleanWritable(false), new LongWritable(numOutside));
}
}

//Reducer class for Pi estimation.
//Accumulate points inside/outside results from the mappers.
public static class PiReducer extends MapReduceBase
implements Reducer<BooleanWritable, LongWritable, WritableComparable<?>, Writable> {

private long numInside = 0;
private long numOutside = 0;
private JobConf conf; //configuration for accessing the file system

//Store job configuration.
@Override
public void configure(JobConf job) {
conf = job;
}

// Accumulate number of points inside/outside results from the mappers.
// @param isInside Is the points inside?
// @param values An iterator to a list of point counts
// @param output dummy, not used here.
// @param reporter

public void reduce(BooleanWritable isInside,
Iterator<LongWritable> values,
OutputCollector<WritableComparable<?>, Writable> output,
Reporter reporter) throws IOException {
if (isInside.get()) {
for(; values.hasNext(); numInside += values.next().get());
} else {
for(; values.hasNext(); numOutside += values.next().get());
}
}

//Reduce task done, write output to a file.
@Override
public void close() throws IOException {
//write output to a file
Path outDir = new Path(TMP_DIR, "out");
Path outFile = new Path(outDir, "reduce-out");
FileSystem fileSys = FileSystem.get(conf);
SequenceFile.Writer writer = SequenceFile.createWriter(fileSys, conf,
outFile, LongWritable.class, LongWritable.class,
CompressionType.NONE);
writer.append(new LongWritable(numInside), new LongWritable(numOutside));
writer.close();
}
}

//Run a map/reduce job for estimating Pi.
//@return the estimated value of Pi.
public static BigDecimal estimate(int numMaps, long numPoints, JobConf jobConf
)
throws IOException {
//setup job conf
jobConf.setJobName(PiEstimator.class.getSimpleName());

jobConf.setInputFormat(SequenceFileInputFormat.class);

jobConf.setOutputKeyClass(BooleanWritable.class);
jobConf.setOutputValueClass(LongWritable.class);
jobConf.setOutputFormat(SequenceFileOutputFormat.class);

jobConf.setMapperClass(PiMapper.class);
jobConf.setNumMapTasks(numMaps);

jobConf.setReducerClass(PiReducer.class);
jobConf.setNumReduceTasks(1);

// turn off speculative execution, because DFS doesn't handle
// multiple writers to the same file.
jobConf.setSpeculativeExecution(false);

//setup input/output directories
final Path inDir = new Path(TMP_DIR, "in");
final Path outDir = new Path(TMP_DIR, "out");
FileInputFormat.setInputPaths(jobConf, inDir);
FileOutputFormat.setOutputPath(jobConf, outDir);

final FileSystem fs = FileSystem.get(jobConf);
if (fs.exists(TMP_DIR)) {
throw new IOException("Tmp directory " + fs.makeQualified(TMP_DIR)
+ " already exists. Remove it first.");
}
if (!fs.mkdirs(inDir)) {
throw new IOException("Cannot create input directory " + inDir);
}

//generate an input file for each map task
try {
for(int i=0; i < numMaps; ++i) {
final Path file = new Path(inDir, "part"+i);
final LongWritable offset = new LongWritable(i * numPoints);
final LongWritable size = new LongWritable(numPoints);
final SequenceFile.Writer writer = SequenceFile.createWriter(
fs, jobConf, file,
LongWritable.class, LongWritable.class, CompressionType.NONE);
try {
writer.append(offset, size);
} finally {
writer.close();
}
System.out.println("Wrote input for Map #"+i);
}

//start a map/reduce job
System.out.println("Starting Job");
final long startTime = System.currentTimeMillis();
JobClient.runJob(jobConf);
final double duration = (System.currentTimeMillis() - startTime)/1000.0;
System.out.println("Job Finished in " + duration + " seconds");

//read outputs
Path inFile = new Path(outDir, "reduce-out");
LongWritable numInside = new LongWritable();
LongWritable numOutside = new LongWritable();
SequenceFile.Reader reader = new SequenceFile.Reader(fs, inFile, jobConf);
try {
reader.next(numInside, numOutside);
} finally {
reader.close();
}

//compute estimated value
return BigDecimal.valueOf(4).setScale(20)
.multiply(BigDecimal.valueOf(numInside.get()))
.divide(BigDecimal.valueOf(numMaps))
.divide(BigDecimal.valueOf(numPoints));
} finally {
fs.delete(TMP_DIR, true);
}
}

//Parse arguments and then runs a map/reduce job.
//Print output in standard out.
//@return a non-zero if there is an error. Otherwise, return 0.
public int run(String[] args) throws Exception {
if (args.length != 2) {
System.err.println("Usage: "+getClass().getName()+" <nMaps> <nSamples>");
ToolRunner.printGenericCommandUsage(System.err);
return -1;
}

final int nMaps = Integer.parseInt(args[0]);
final long nSamples = Long.parseLong(args[1]);

System.out.println("Number of Maps = " + nMaps);
System.out.println("Samples per Map = " + nSamples);

final JobConf jobConf = new JobConf(getConf(), getClass());
System.out.println("Estimated value of Pi is "
+ estimate(nMaps, nSamples, jobConf));
return 0;
}

//main method for running it as a stand alone command.
public static void main(String[] argv) throws Exception {
System.exit(ToolRunner.run(null, new PiEstimator(), argv));
}
}
```

## <a name="appendix-d---the-10gb-graysort-source-code"></a>D – 10 GB-os graysort forráskódját függelék
A kódját a TeraSort MapReduce program számára jelenik meg a vizsgálathoz ebben a szakaszban.

```java
/**
    * Licensed to the Apache Software Foundation (ASF) under one
    * or more contributor license agreements.  See the NOTICE file
    * distributed with this work for additional information
    * regarding copyright ownership.  The ASF licenses this file
    * to you under the Apache License, Version 2.0 (the
    * "License"); you may not use this file except in compliance
    * with the License.  You may obtain a copy of the License at
    *
    *     http://www.apache.org/licenses/LICENSE-2.0
    *
    * Unless required by applicable law or agreed to in writing, software
    * distributed under the License is distributed on an "AS IS" BASIS,
    * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    * See the License for the specific language governing permissions and
    * limitations under the License.
    */

package org.apache.hadoop.examples.terasort;

import java.io.IOException;
import java.io.PrintStream;
import java.net.URI;
import java.util.ArrayList;
import java.util.List;

import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.apache.hadoop.conf.Configured;
import org.apache.hadoop.filecache.DistributedCache;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.NullWritable;
import org.apache.hadoop.io.SequenceFile;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapred.FileOutputFormat;
import org.apache.hadoop.mapred.JobClient;
import org.apache.hadoop.mapred.JobConf;
import org.apache.hadoop.mapred.Partitioner;
import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;

/**
    * Generates the sampled split points, launches the job,
    * and waits for it to finish.
    * <p>
    * To run the program:
    * <b>bin/hadoop jar hadoop-examples-*.jar terasort in-dir out-dir</b>
    */

public class TeraSort extends Configured implements Tool {
    private static final Log LOG = LogFactory.getLog(TeraSort.class);

    /**
    * A partitioner that splits text keys into roughly equal
    * partitions in a global sorted order.
    */

    static class TotalOrderPartitioner implements Partitioner<Text,Text>{
    private TrieNode trie;
    private Text[] splitPoints;

    /**
        * A generic trie node
        */
    static abstract class TrieNode {
        private int level;
        TrieNode(int level) {
        this.level = level;
        }
        abstract int findPartition(Text key);
        abstract void print(PrintStream strm) throws IOException;
        int getLevel() {
        return level;
        }
    }

    /**
        * An inner trie node that contains 256 children based on the next
        * character.
        */
    static class InnerTrieNode extends TrieNode {
        private TrieNode[] child = new TrieNode[256];

        InnerTrieNode(int level) {
        super(level);
        }
        int findPartition(Text key) {
        int level = getLevel();
        if (key.getLength() <= level) {
            return child[0].findPartition(key);
        }
        return child[key.getBytes()[level]].findPartition(key);
        }
        void setChild(int idx, TrieNode child) {
        this.child[idx] = child;
        }
        void print(PrintStream strm) throws IOException {
        for(int ch=0; ch < 255; ++ch) {
            for(int i = 0; i < 2*getLevel(); ++i) {
            strm.print(' ');
            }
            strm.print(ch);
            strm.println(" ->");
            if (child[ch] != null) {
            child[ch].print(strm);
            }
        }
        }
    }

    /**
        * A leaf trie node that does string compares to figure out where the given
        * key belongs between lower..upper.
        */
    static class LeafTrieNode extends TrieNode {
        int lower;
        int upper;
        Text[] splitPoints;
        LeafTrieNode(int level, Text[] splitPoints, int lower, int upper) {
        super(level);
        this.splitPoints = splitPoints;
        this.lower = lower;
        this.upper = upper;
        }
        int findPartition(Text key) {
        for(int i=lower; i<upper; ++i) {
            if (splitPoints[i].compareTo(key) >= 0) {
            return i;
            }
        }
        return upper;
        }
        void print(PrintStream strm) throws IOException {
        for(int i = 0; i < 2*getLevel(); ++i) {
            strm.print(' ');
        }
        strm.print(lower);
        strm.print(", ");
        strm.println(upper);
        }
    }

    /**
        * Read the cut points from the given sequence file.
        * @param fs the file system
        * @param p the path to read
        * @param job the job config
        * @return the strings to split the partitions on
        * @throws IOException
        */
    private static Text[] readPartitions(FileSystem fs, Path p,
                                            JobConf job) throws IOException {
        SequenceFile.Reader reader = new SequenceFile.Reader(fs, p, job);
        List<Text> parts = new ArrayList<Text>();
        Text key = new Text();
        NullWritable value = NullWritable.get();
        while (reader.next(key, value)) {
        parts.add(key);
        key = new Text();
        }
        reader.close();
        return parts.toArray(new Text[parts.size()]);
    }

    /**
        * Given a sorted set of cut points, build a trie that will find the correct
        * partition quickly.
        * @param splits the list of cut points
        * @param lower the lower bound of partitions 0..numPartitions-1
        * @param upper the upper bound of partitions 0..numPartitions-1
        * @param prefix the prefix that we have already checked against
        * @param maxDepth the maximum depth we will build a trie for
        * @return the trie node that will divide the splits correctly
        */
    private static TrieNode buildTrie(Text[] splits, int lower, int upper,
                                        Text prefix, int maxDepth) {
        int depth = prefix.getLength();
        if (depth >= maxDepth || lower == upper) {
        return new LeafTrieNode(depth, splits, lower, upper);
        }
        InnerTrieNode result = new InnerTrieNode(depth);
        Text trial = new Text(prefix);
        // append an extra byte on to the prefix
        trial.append(new byte[1], 0, 1);
        int currentBound = lower;
        for(int ch = 0; ch < 255; ++ch) {
        trial.getBytes()[depth] = (byte) (ch + 1);
        lower = currentBound;
        while (currentBound < upper) {
            if (splits[currentBound].compareTo(trial) >= 0) {
            break;
            }
            currentBound += 1;
        }
        trial.getBytes()[depth] = (byte) ch;
        result.child[ch] = buildTrie(splits, lower, currentBound, trial,
                                        maxDepth);
        }
        // pick up the rest
        trial.getBytes()[depth] = 127;
        result.child[255] = buildTrie(splits, currentBound, upper, trial,
                                    maxDepth);
        return result;
    }

    public void configure(JobConf job) {
        try {
        FileSystem fs = FileSystem.getLocal(job);
        Path partFile = new Path(TeraInputFormat.PARTITION_FILENAME);
        splitPoints = readPartitions(fs, partFile, job);
        trie = buildTrie(splitPoints, 0, splitPoints.length, new Text(), 2);
        } catch (IOException ie) {
        throw new IllegalArgumentException("can't read paritions file", ie);
        }
    }

    public TotalOrderPartitioner() {
    }

    public int getPartition(Text key, Text value, int numPartitions) {
        return trie.findPartition(key);
    }

    }

    public int run(String[] args) throws Exception {
    LOG.info("starting");
    JobConf job = (JobConf) getConf();
    Path inputDir = new Path(args[0]);
    inputDir = inputDir.makeQualified(inputDir.getFileSystem(job));
    Path partitionFile = new Path(inputDir, TeraInputFormat.PARTITION_FILENAME);
    URI partitionUri = new URI(partitionFile.toString() +
                                "#" + TeraInputFormat.PARTITION_FILENAME);
    TeraInputFormat.setInputPaths(job, new Path(args[0]));
    FileOutputFormat.setOutputPath(job, new Path(args[1]));
    job.setJobName("TeraSort");
    job.setJarByClass(TeraSort.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(Text.class);
    job.setInputFormat(TeraInputFormat.class);
    job.setOutputFormat(TeraOutputFormat.class);
    job.setPartitionerClass(TotalOrderPartitioner.class);
    TeraInputFormat.writePartitionFile(job, partitionFile);
    DistributedCache.addCacheFile(partitionUri, job);
    DistributedCache.createSymlink(job);
    job.setInt("dfs.replication", 1);
    TeraOutputFormat.setFinalSync(job, true);
    JobClient.runJob(job);
    LOG.info("done");
    return 0;
    }

    /**
    * @param args
    */

    public static void main(String[] args) throws Exception {
    int res = ToolRunner.run(new JobConf(), new TeraSort(), args);
    System.exit(res);
    }
}
```

[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hadoop/submit-apache-hadoop-jobs-programmatically.md
[hdinsight-introduction]:hadoop/apache-hadoop-introduction.md

[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: #hdinsight-sample-10gb-graysort
[hdinsight-sample-csharp-streaming]: #hdinsight-sample-csharp-streaming
[hdinsight-sample-pi-estimator]: #hdinsight-sample-pi-estimator
[hdinsight-sample-wordcount]: #hdinsight-sample-wordcount

[hdinsight-use-hive]: hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]: hadoop/hdinsight-use-pig.md

[streamreader]: http://msdn.microsoft.com/library/system.io.streamreader.aspx
[console-writeline]: http://msdn.microsoft.com/library/system.console.writeline
[stdin-stdout-stderr]: https://msdn.microsoft.com/library/3x292kth.aspx
