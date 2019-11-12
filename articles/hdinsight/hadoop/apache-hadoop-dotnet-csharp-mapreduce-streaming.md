---
title: A C# MapReduce használata a Hadoop-ben a HDInsight-Azure-ban
description: Megtudhatja, C# hogyan hozhat létre MapReduce-megoldásokat az Azure HDInsight Apache Hadoop használatával.
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: hrasheed
ms.openlocfilehash: 1cdf029d296bd6ff11b6531cd47dc6a7fd3163c3
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930261"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>Használat C# a MapReduce streaming Apache Hadoop a HDInsight-ben

Megtudhatja, C# hogyan hozhat létre MapReduce-megoldást a HDInsight-ben.

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További információ: [Apache Hadoop Components on HDInsight](../hdinsight-component-versioning.md).

A Apache Hadoop streaming egy olyan segédprogram, amely lehetővé teszi, hogy MapReduce-feladatokat futtasson egy parancsfájl vagy egy végrehajtható fájl használatával. Ebben a példában a .NET használatával lehet megvalósítani a leképező és a szűkítőt a Word Count megoldásban.

## <a name="net-on-hdinsight"></a>.NET on HDInsight

A *Linux-alapú HDInsight-* fürtök a [mono (https://mono-project.com)](https://mono-project.com) használatával futtatják a .NET-alkalmazásokat. A HDInsight 3,6-es verziója tartalmazza a Mono 4.2.1-es verzióját. További információ a HDInsight-mel együtt használt mono verziójával kapcsolatban: [Apache Hadoop különböző HDInsight-verziókban elérhető összetevők](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions). 

További információ a .NET-keretrendszer verzióival való monó kompatibilitásról: [monó kompatibilitás](https://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Az Hadoop streaming működése

A dokumentumban a folyamatos átvitelhez használt alapszintű folyamat a következő:

1. A Hadoop az adatleképezést (ebben a példában a*mapper. exe fájlt* ) az stdin-re továbbítja.
2. A Mapper dolgozza fel az adatfeldolgozást, és tabulátorral tagolt kulcs/érték párokat bocsát ki az STDOUT-ra.
3. A kimenetet a Hadoop olvassa be, majd átadja a (z) szűkítőnek (ez a példa a*redukáló. exe fájlnak* ) a stdin-ben.
4. A redukáló beolvassa a tabulátorral tagolt kulcs/érték párokat, feldolgozza az adatokat, majd tabulátorral tagolt kulcs/érték párokat bocsát ki az STDOUT-on.
5. A kimenetet a Hadoop olvassa be, és a kimeneti könyvtárba írja.

További információ a folyamatos átvitelről: [Hadoop streaming](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Előfeltételek

* Visual Studio.

* A .NET-keretrendszer 4,5-es C# példányát tároló kód írásával és összeállításával kapcsolatos ismeretek.

* Az. exe fájlok a fürtbe való feltöltésének módja. A jelen dokumentumban szereplő lépések a Visual studióhoz készült Data Lake Tools használatával töltik fel a fájlokat a fürt elsődleges tárolójába.

* Azure PowerShell vagy egy Secure Shell-(SSH-) ügyfél.

* Egy Hadoop a HDInsight-fürtön. A fürtök létrehozásáról további információt a HDInsight- [fürt létrehozása](../hdinsight-hadoop-provision-linux-clusters.md)című témakörben talál.

## <a name="create-the-mapper"></a>A Mapper létrehozása

A Visual Studióban hozzon létre egy új .NET Framework Console-alkalmazást a *Mapper*nevű alkalmazásban. Használja az alábbi kódot az alkalmazáshoz:

```csharp
using System;
using System.Text.RegularExpressions;

namespace mapper
{
    class Program
    {
        static void Main(string[] args)
        {
            string line;
            //Hadoop passes data to the mapper on STDIN
            while((line = Console.ReadLine()) != null)
            {
                // We only want words, so strip out punctuation, numbers, etc.
                var onlyText = Regex.Replace(line, @"\.|;|:|,|[0-9]|'", "");
                // Split at whitespace.
                var words = Regex.Matches(onlyText, @"[\w]+");
                // Loop over the words
                foreach(var word in words)
                {
                    //Emit tab-delimited key/value pairs.
                    //In this case, a word and a count of 1.
                    Console.WriteLine("{0}\t1",word);
                }
            }
        }
    }
}
```

Miután létrehozta az alkalmazást, hozza létre a */bin/debug/mapper.exe* -fájl létrehozásához a projekt könyvtárában.

## <a name="create-the-reducer"></a>A szűkítő létrehozása

A Visual Studióban hozzon létre egy *szűkítő*nevű új .NET-keretrendszer-alkalmazást. Használja az alábbi kódot az alkalmazáshoz:

```csharp
using System;
using System.Collections.Generic;

namespace reducer
{
    class Program
    {
        static void Main(string[] args)
        {
            //Dictionary for holding a count of words
            Dictionary<string, int> words = new Dictionary<string, int>();

            string line;
            //Read from STDIN
            while ((line = Console.ReadLine()) != null)
            {
                // Data from Hadoop is tab-delimited key/value pairs
                var sArr = line.Split('\t');
                // Get the word
                string word = sArr[0];
                // Get the count
                int count = Convert.ToInt32(sArr[1]);

                //Do we already have a count for the word?
                if(words.ContainsKey(word))
                {
                    //If so, increment the count
                    words[word] += count;
                } else
                {
                    //Add the key to the collection
                    words.Add(word, count);
                }
            }
            //Finally, emit each word and count
            foreach (var word in words)
            {
                //Emit tab-delimited key/value pairs.
                //In this case, a word and a count of 1.
                Console.WriteLine("{0}\t{1}", word.Key, word.Value);
            }
        }
    }
}
```

Miután létrehozta az alkalmazást, hozza létre a */bin/debug/Reducer.exe* -fájl létrehozásához a projekt könyvtárában.

## <a name="upload-to-storage"></a>Feltöltés tárolóba

Ezután fel kell töltenie a *Mapper* és a *redukáló* alkalmazásokat a HDInsight-tárolóba.

1. A Visual Studióban válassza a > **Server Explorer** **megtekintése** lehetőséget.

2. Bontsa ki az **Azure** elemet, majd bontsa ki a **HDInsight** elemet.

3. Ha a rendszer kéri, adja meg az Azure-előfizetés hitelesítő adatait, majd válassza a **Bejelentkezés**lehetőséget.

4. Bontsa ki azt a HDInsight-fürtöt, amelyre telepíteni kívánja az alkalmazást. Megjelenik egy bejegyzés a szöveggel **(alapértelmezett Storage-fiók)** .

   ![Storage-fiók, HDInsight-fürt, Server Explorer, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

   * Ha a **(alapértelmezett Storage-fiók)** bejegyzést ki lehet bővíteni, az **Azure Storage-fiókot** használja alapértelmezett tárolóként a fürt számára. Ha meg szeretné tekinteni a fürt alapértelmezett tárolójában lévő fájlokat, bontsa ki a bejegyzést, majd kattintson duplán a **(alapértelmezett tároló)** elemre.

   * Ha az **(alapértelmezett Storage-fiók)** bejegyzést nem lehet kibontani, akkor a **Azure Data Lake Storage** a fürt alapértelmezett tárolójának fogja használni. A fürt alapértelmezett tárolójában lévő fájlok megtekintéséhez kattintson duplán az **(alapértelmezett Storage-fiók)** bejegyzésre.

5. Az. exe fájlok feltöltéséhez használja az alábbi módszerek egyikét:

    * Ha **Azure Storage-fiókot**használ, válassza a **blob feltöltése** ikont. 

        ![HDInsight feltöltés ikonja Mapper, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)

        Az **új fájl feltöltése** párbeszédpanel **fájlnév**területén válassza a **Tallózás**lehetőséget. A **blob feltöltése** párbeszédpanelen lépjen a *Mapper* projekt *bin\debug* mappájába, majd válassza ki a *mapper. exe* fájlt. Végül kattintson a **Megnyitás** elemre, majd az **OK gombra** a feltöltés befejezéséhez. 

    * **Azure Data Lake Storage**esetében kattintson a jobb gombbal egy üresre a fájl listázása területen, majd válassza a **feltöltés**lehetőséget. Végül válassza ki a *mapper. exe* fájlt, majd kattintson a **Megnyitás**gombra.

    A *mapper. exe* feltöltésének befejezése után ismételje meg a feltöltési folyamatot a *csökkentő. exe* fájlhoz.

## <a name="run-a-job-using-an-ssh-session"></a>Feladatok futtatása: SSH-munkamenet használata

Az alábbi eljárás azt ismerteti, hogyan futtathat MapReduce-feladatokat SSH-munkamenet használatával:

1. Az SSH használatával csatlakozzon a HDInsight-fürthöz. (Például futtassa a parancsot `ssh sshuser@<clustername>-ssh.azurehdinsight.net`.) További információ: az [SSH használata a HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. A MapReduce-feladatok elindításához használja a következő parancsok egyikét:

   * Ha az alapértelmezett tároló az **Azure Storage**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files wasb:///mapper.exe,wasb:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

    * Ha az alapértelmezett tárterület **Data Lake Storage Gen1**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files adl:///mapper.exe,adl:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   * Ha az alapértelmezett tárterület **Data Lake Storage Gen2**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files abfs:///mapper.exe,abfs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   A következő lista ismerteti, hogy az egyes paraméterek és lehetőségek a következőket jelölik:

   * *Hadoop-streaming. jar*: a folyamatos átviteli MapReduce funkciót tartalmazó jar-fájlt határozza meg.
   * `-files`: a feladatokhoz tartozó *mapper. exe* és *redukáló. exe* fájlt adja meg. A `wasb:///`, `adl:///`vagy `abfs:///` protokoll deklarációja minden fájl előtt a fürt alapértelmezett tárolási gyökerének elérési útja.
   * `-mapper`: a leképezést megvalósító fájlt adja meg.
   * `-reducer`: a szűkítőt megvalósító fájlt adja meg.
   * `-input`: a bemeneti adatokat adja meg.
   * `-output`: a kimeneti könyvtárat adja meg.

3. A MapReduce-feladatok befejezése után a következő parancs használatával tekintheti meg az eredményeket:

   ```bash
   hdfs dfs -text /example/wordcountout/part-00000
   ```

   A következő szöveg egy példa a parancs által visszaadott adatmennyiségre:

   ```output
   you     1128
   young   38
   younger 1
   youngest        1
   your    338
   yours   4
   yourself        34
   yourselves      3
   youth   17
   ```

## <a name="run-a-job-using-powershell"></a>Feladatok futtatása: a PowerShell használatával

A következő PowerShell-szkripttel futtasson egy MapReduce-feladatot, és töltse le az eredményeket.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Ez a parancsfájl kéri a fürt bejelentkezési fiókjának nevét és jelszavát, valamint a HDInsight-fürt nevét. A művelet befejezése után a kimenet egy *output. txt*nevű fájlba lesz letöltve. A következő szöveg egy példa a `output.txt` fájlban található fájlokra:

```output
you     1128
young   38
younger 1
youngest        1
your    338
yours   4
yourself        34
yourselves      3
youth   17
```

## <a name="next-steps"></a>Következő lépések

A MapReduce és a HDInsight használatával kapcsolatos további információkért lásd: a [MapReduce használata a HDInsight-ban Apache Hadoop](hdinsight-use-mapreduce.md).

További információ a kaptár C# és a Pig használatáról: [ C# felhasználó által definiált függvény használata Apache Hive és Apache Pig](apache-hadoop-hive-pig-udf-dotnet-csharp.md)használatával.

A Storm on HDInsight C# használatával kapcsolatos információkért lásd: a [HDInsight C# -Apache Storm hez készült topológiák fejlesztése](../storm/apache-storm-develop-csharp-visual-studio-topology.md).
