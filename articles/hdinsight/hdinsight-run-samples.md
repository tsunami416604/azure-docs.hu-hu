---
title: A HDInsight - Azure Hadoop-minták futtatása
description: Ismerkedjen meg a megadott minták az Azure HDInsight szolgáltatással. MapReduce-programok data-fürtökön futó PowerShell-parancsfájlokat használja.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: 84c28eebc2bcf1bfdfcb0995f1caa05ed19629e2
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50250835"
---
# <a name="run-hadoop-mapreduce-samples-in-windows-based-hdinsight"></a>Hadoop MapReduce-minták futtatása a Windows-alapú HDInsight
[!INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Minták segítséget nyújt az első lépések futó MapReduce-feladatok használata Azure HDInsight Hadoop-fürtök a van megadva. Ezek a minták a HDInsight által felügyelt fürtöket, Ön által létrehozott minden egyes végrehajtott érhető el. Ezek a minták futtatása révén megismerkedhet a feladatok futtatásához a Hadoop-fürtök Azure PowerShell-parancsmagok használatával.

* [**Szószámlálás**][hdinsight-sample-wordcount]: előfordulásait számláló egy szövegfájlba.
* [**C# word-count streamelési**][hdinsight-sample-csharp-streaming]: előfordulásait számláló egy szövegfájlba, a Hadoop streamelési felületén.
* [**A pi estimator**][hdinsight-sample-pi-estimator]: használja a statisztikai (látszólagos Monte Carlo) módszer a pi értékét.
* [**10 GB-os Graysort**][hdinsight-sample-10gb-graysort]: egy általános célú GraySort futtatása egy 10 GB-os fájlt a HDInsight használatával. Három feladat futtatásához: létrehozza az adatokat, Terasort az adatokat, és ellenőrizze, hogy az adatok megfelelően rendezett Teravalidate Teragen.

> [!NOTE]
> A forráskód megtalálható a függelékben.

A Hadoop-kapcsolódó technológiák, például a Java-alapú MapReduce programozási és a streamelési és a használt Windows PowerShell-parancsmagokkal kapcsolatos dokumentáció a weben létezik sokkal további dokumentáció parancsfájlok. Ezekkel az erőforrásokkal kapcsolatban további információkért lásd:

* [Java MapReduce programok fejlesztése a HDInsight Hadoop számára](hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Hadoop-feladatok elküldése a HDInsightban](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Az Azure HDInsight bemutatása][hdinsight-introduction]

Napjainkban sok ember válassza a Hive és Pig MapReduce keresztül.  További információkért lásd:

* [Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)
* [A Pig használata a HDInsight](hadoop/hdinsight-use-pig.md)

**Előfeltételek**:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Egy HDInsight-fürt**. A különböző módszereket, amelyben az ilyen fürtök lehet létrehozni, lásd: [Hadoop-fürtök létrehozása a HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* **Munkaállomás Azure PowerShell-lel**.

    > [!IMPORTANT]
    > A HDInsight-erőforrások Azure Service Managerrel történő kezelésének Azure PowerShell-támogatása **elavult**, így 2017. január 1-től megszűnik. A jelen dokumentumban leírt lépések az új HDInsight-parancsmagokat használják, amelyek az Azure Resource Managerrel működnek.
    >
    > Kövesse a [Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs) telepítése az Azure PowerShell legújabb verzióját. Ha vannak olyan parancsprogramjai, amelyeket módosítani kell használni az új parancsmagok, amelyek együttműködnek az Azure Resource Manager, lásd: [Fejlesztőeszközök az Azure Resource Manager-alapú HDInsight-fürtök-ba való Migrálás](hdinsight-hadoop-development-using-azure-resource-manager.md).

## <a name="hdinsight-sample-wordcount"></a>Szószámlálás – Java
Küldje el egy MapReduce-projektet, először hozzon létre egy MapReduce-feladat definíciója. A feladat-definícióban, a MapReduce program jar-fájlra és a jar-fájlt, amely a helyének megadásához **wasb:///example/jars/hadoop-mapreduce-examples.jar**, az osztály nevét, és az argumentumokat.  A wordcount MapReduce-programot két argumentumot: a forrásfájl, amellyel szavakat, és a kimeneti helyet száma.

A forráskód megtalálható a [függelék](#apendix-a---the-word-count-MapReduce-program-in-java).

Az eljárás egy Java MapReduce fejlődő programok, lásd: - [fejlesztés Java MapReduce programok számára a HDInsight hadoop-keretrendszer](hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)

**A word száma MapReduce-feladatok elküldése**

1. Nyissa meg **Windows PowerShell ISE-ben**. Útmutatásért lásd: [telepítse és konfigurálja az Azure Powershellt][powershell-install-configure].
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

    A MapReduce-feladatot hoz létre egy fájlt *. rész – az r-00000*, amely szavakat és a számát tartalmazza. A szkript a **findstr** paranccsal listát készíthet minden szót tartalmazó *"there"*.
3. Az első három változókat, és futtassa a szkriptet.

## <a name="hdinsight-sample-csharp-streaming"></a>Word-count – C# streaming
Hadoop MapReduce, ami lehetővé teszi a térkép írni, és csökkentheti a függvényeket más nyelveken biztosít egy olyan streamelési API.

> [!NOTE]
> A jelen oktatóanyagban szereplő lépések Windows-alapú HDInsight-fürtök csak a alkalmazni. Egy Linux-alapú HDInsight-fürtök streamelési példa: [fejlesztés Python-streamprogramok for HDInsight](hadoop/apache-hadoop-streaming-python.md).

A példában a hozzárendelést és a nyomáscsökkentő is olvassa a bemeneti a végrehajtható fájlok [stdin] [ stdin-stdout-stderr] (--soronként) és a kimenetet kibocsátható [stdout] [ stdin-stdout-stderr]. A program megszámolja a szöveget az összes szó.

Ha egy végrehajtható fájl van megadva **leképező**, eseményleképező feladatokon külön folyamatként indít a végrehajtható fájlt, amikor a teljesítményleképező inicializálva van. A teljesítményleképező feladat futása alakítja át a bemeneti sorok, és -csatornák a sorokat a [stdin] [ stdin-stdout-stderr] a folyamat.

Addig is a teljesítményleképező sor alapú kimenete a folyamat az stdout adatsorból gyűjti. Az egyes sorok alakít egy kulcs/érték pár, amely leképezőjét kimenete gyűjtése történik. Alapértelmezés szerint az előtag legfeljebb lap első karaktere egy vonal a kulcsot, és a sor (kivéve a tabulátor karakter) további része az az érték. Ha nem található a sor nem tabulátor karakter, teljes sorban a kulcs minősül, és az értéke null.

Ha egy végrehajtható fájl van megadva **csökkentő**, minden egyes nyomáscsökkentő feladat külön folyamatként indít a végrehajtható fájlt, amikor a nyomáscsökkentő inicializálva van. Ahogy a nyomáscsökkentő feladat fut, a bemeneti kulcs/érték párok alakítja sorokat, és azt hírcsatornák, hogy a sorokat a [stdin] [ stdin-stdout-stderr] a folyamat.

Addig is a nyomáscsökkentő gyűjti a sor alapú kimenete a [stdout] [ stdin-stdout-stderr] a folyamat. Az egyes sorok konvertál egy kulcs/érték pár, amelyek gyűjtése történik a nyomáscsökkentő kimenetét. Alapértelmezés szerint az előtag legfeljebb lap első karaktere egy vonal a kulcsot, és a sor (kivéve a tabulátor karakter) további része az az érték.

**Egy C# streamelési word-count feladat elküldése**

* Kövesse a [Szószámlálás – Java](#word-count-java), és cserélje le a feladat definícióját a következő sort:

    ```powershell
    $mrJobDefinition = New-AzureRmHDInsightStreamingMapReduceJobDefinition `
                            -Files "/example/apps/cat.exe","/example/apps/wc.exe" `
                            -Mapper "cat.exe" `
                            -Reducer "wc.exe" `
                            -InputPath "/example/data/gutenberg/davinci.txt" `
                            -OutputPath "/example/data/StreamingOutput/wc.txt"
    ```

    A kimeneti fájlba kell:

        example/data/StreamingOutput/wc.txt/part-00000

## <a name="hdinsight-sample-pi-estimator"></a>A PI estimator
A pi estimator használ egy statisztikai (látszólagos Monte Carlo) módszer a pi értékét. Véletlenszerű helyezni egy egységet pontok négyszögletes is tartoznak, hogy szögletes belül a kör területét egyenlő valószínűséggel ütemtervben kör pi/4-es. A pi értékét megbecsülhető 4R, ahol a R belüli belül a szögletes pontok száma a kör pontok aránya az értékét. Minél nagyobb a minta használt pontokat, annál jobb a becslés van.

Ehhez a mintához megadott parancsfájl egy Hadoop-jar feladatot, és értéke legfeljebb 16 maps, amelyek mindegyike szükséges számítási 10 millió minta pontok által a paraméterértékek értékkel futtassa. Ezek a paraméterértékek pi becsült értékének javítására is módosítható. Referencia az első 10 tizedesjegyek pi 3.1415926535.

**A pi estimator feladatok elküldése**

* Kövesse a [Szószámlálás – Java](#word-count-java), és cserélje le a feladat definícióját a következő sort:

    ```powershell
    $mrJobJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "pi" `
                                -Arguments "16", "10000000"
    ```

## <a name="hdinsight-sample-10gb-graysort"></a>10 GB-os Graysort
Ez a minta egy szerény 10 GB adatot használja, így viszonylag gyorsan futtatható. A MapReduce alkalmazások Owen O'Malley és Arun Murthy, amely az általános célú ("daytona") terabájt éves rendezési teljesítményteszt megnyert 0.578 TB/perc (100 TB-os 173 percek) arány 2009 által fejlesztett használ. Erről és más rendezési referenciaalapokhoz képest történő további információkért lásd: a [Sortbenchmark](http://sortbenchmark.org/) hely.

Ebben a példában három különböző MapReduce-programok:

1. **TeraGen** van egy MapReduce-programot, amely a sorok rendezése adatok létrehozására használhatja.
2. **TeraSort** MapReduce-minták a bemeneti adatokat és segítségével rendezze az adatokat egy teljes rendelés. TeraSort egy standard rendezési MapReduce függvények, egy egyéni partitioner mintavételezés N-1 kulcsok, amelyek meghatározzák az egyes csökkentse a tartományok rendezett listáját használó kivételével. Ebben az esetben, minden kulcs ilyen mintavételezik a [i-1] < kulcs = < [i] minta érkeznek i csökkentése érdekében. Ez garantálja, hogy a kimeneteket, csökkentse az i az összes, kevesebb, mint a kimenetét csökkentése i + 1.
3. **TeraValidate** egy MapReduce-programot, amely ellenőrzi, hogy a kimenet globálisan rendezett van. A kimeneti könyvtárat hoz létre egy térkép fájlonként, és minden egyes térkép biztosítja, hogy minden egyes kulcs kisebb vagy egyenlő, mint az előzőre. A térkép függvény is hoz létre az első és utolsó kulcsok minden egyes fájl rögzíti, és csökkentse a függvény gondoskodik róla, hogy az első kulcsot fájl i nagyobb, mint az utolsó fájl i-1 kulcsa. Problémák az üzemen kívüli kulcsot a csökkentse kimeneteként jelenti.

A bemeneti és kimeneti formátum, mindhárom alkalmazás által használt beolvassa és a szöveges fájlokat a megfelelő formátumban. Csökkentse a kimenetét, a replikáció értéke 1, 3, alapértelmezett helyett, mert a teljesítményteszt verseny nem igényel, hogy a kimeneti adatok továbbítása több csomóponton replikálja.

Három feladat a példaszkript, egyes megfelelő bevezető ismertetett MapReduce-programok által van szükség:

1. Az adatok rendezése futtatásával létrehozása a **TeraGen** MapReduce-feladatot.
2. Rendezze az adatokat futtatásával a **TeraSort** MapReduce-feladatot.
3. Győződjön meg arról, hogy az adatok megfelelően rendezett futtatásával a **TeraValidate** MapReduce-feladatot.

**A feladatok elküldése**

* Kövesse a [Szószámlálás – Java](#word-count-java), és használja a következő definíciók:

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

## <a name="next-steps"></a>További lépések
Ez a cikk és az egyes, a minták a cikkeket útmutatóból megtudhatta, hogyan az Azure PowerShell-lel a HDInsight-fürtök a minta futtatásához. A Pig, Hive és a MapReduce használata a HDInsight kapcsolatos oktatóanyagok és az alábbi témakörökben található:

* [Hadoop első lépései a HDInsight Hive-val elemzéséhez mobil kézibeszélőt használata][hdinsight-get-started]
* [A Pig használata a HDInsight Hadoop-keretrendszerrel][hdinsight-use-pig]
* [A Hive használata a HDInsight Hadoop-keretrendszerrel][hdinsight-use-hive]
* [A HDInsight Hadoop-feladatok elküldése][hdinsight-submit-jobs]

## <a name="appendix-a---the-word-count-source-code"></a>A függelék – a Word száma forráskód

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

## <a name="appendix-b---the-word-count-streaming-source-code"></a>B függelék – a forráskód streamelési word száma
A MapReduce-programot cat.exe alkalmazást leképezés adapterként közvetítése a szöveget a konzolon vagy a wc.exe alkalmazását, csökkentse felületként megszámolja a szavak dokumentum átvitt használja. A hozzárendelést és a nyomáscsökkentő standardní vstupní proud (stdin) karaktereket,--soronkénti, olvasni, és a standard kimeneti adatfolyamba (stdout) írási.

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

A teljesítményleképező kód a cat.cs fájlban egy [StreamReader] [ streamreader] olvassa el a karaktereket a konzolt, majd írja a streamet, a standard kimeneti adatfolyamba, a statikus bejövőadatfolyam-objektum[ Console.WriteLine("a(z)] [ console-writeline] metódust.

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

A nyomáscsökkentő kód a wc.cs fájlban egy [StreamReader] [ streamreader] karakterek olvasni a standardní vstupní proud, amelyeket a cat.exe eseményleképező kimenete objektum. Olvassa be, hogy az a karakter, a [Console.WriteLine("a(z)] [ console-writeline] metódus számolja a szavakat leltár a tárolóhelyek és a sor végén karakterek végén található minden egyes szó szerint. A standard kimeneti adatfolyamba, a teljes majd ír a [Console.WriteLine("a(z)] [ console-writeline] metódust.

## <a name="appendix-c---the-pi-estimator-source-code"></a>C függelék – a Pi estimator forráskód
A pi estimator Java-kóddal, amely tartalmazza a teljesítményleképező és nyomáscsökkentő függvények az alábbi ellenőrzés érhető el. A eseményleképező program véletlenszerű helyezni egy egység szögletes pontok megadott számú állít elő, és majd megszámlálja azokat a körben pontokat. A nyomáscsökkentő program a leképező által számlált pontok gyűlnek, és ezután becslése a képlet 4R, ahol az R a belül belül a szögletes pontok száma a kör számítanak pontok aránya a pi értékét.

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
//@param out output {true->numInside, false->numOutside}
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

## <a name="appendix-d---the-10gb-graysort-source-code"></a>D függelék – a 10 GB-os graysort forráskód
A kód a TeraSort MapReduce-programot a vizsgálatra ebben a szakaszban jelennek meg.

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
