---
title: A C# MapReduce használata a Hadoop-ben a HDInsight-Azure-ban
description: Megtudhatja, C# hogyan hozhat létre MapReduce-megoldásokat az Azure HDInsight Apache Hadoop használatával.
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: hrasheed
ms.openlocfilehash: 5784fb4f4ab0f46d2db7e5e8cfe9deeafabb4e90
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066946"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>Használat C# a MapReduce streaming Apache Hadoop a HDInsight-ben

Megtudhatja, C# hogyan hozhat létre MapReduce-megoldást a HDInsight-ben.

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További információ: HDInsight- [összetevő verziószámozása](../hdinsight-component-versioning.md).

A Apache Hadoop streaming egy olyan segédprogram, amely lehetővé teszi, hogy MapReduce-feladatokat futtasson egy parancsfájl vagy egy végrehajtható fájl használatával. Ebben a példában a .NET használatával lehet megvalósítani a leképező és a szűkítőt a Word Count megoldásban.

## <a name="net-on-hdinsight"></a>.NET on HDInsight

A __Linux-alapú HDInsight-__ fürtök a MONO-t használják [(https://mono-project.com) ](https://mono-project.com) .NET-alkalmazások futtatására. A HDInsight 3,6-es verziója tartalmazza a Mono 4.2.1-es verzióját. További információ a HDInsight által tartalmazott mono-verzióról: HDInsight- [összetevő verziója](../hdinsight-component-versioning.md). 

További információ a .NET-keretrendszer verzióival való monó kompatibilitásról: [monó kompatibilitás](https://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Az Hadoop streaming működése

A dokumentumban a folyamatos átvitelhez használt alapszintű folyamat a következő:

1. A Hadoop az adatleképezést (ebben a példában a mapper. exe fájlt) az STDIN-re továbbítja.
2. A Mapper dolgozza fel az adatfeldolgozást, és tabulátorral tagolt kulcs/érték párokat bocsát ki az STDOUT-ra.
3. A kimenetet a Hadoop olvassa be, majd átadja a (z) szűkítőnek (ez a példa a redukáló. exe fájlnak) a STDIN-ben.
4. A redukáló beolvassa a tabulátorral tagolt kulcs/érték párokat, feldolgozza az adatokat, majd tabulátorral tagolt kulcs/érték párokat bocsát ki az STDOUT-on.
5. A kimenetet a Hadoop olvassa be, és a kimeneti könyvtárba írja.

További információ a folyamatos átvitelről: [Hadoop streaming](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Előfeltételek

* A .NET-keretrendszer 4,5-es C# példányát tároló kód írásával és összeállításával kapcsolatos ismeretek. A jelen dokumentumban ismertetett lépések a Visual Studio 2017-et használják.

* Az. exe fájlok a fürtbe való feltöltésének módja. A jelen dokumentumban szereplő lépések a Visual studióhoz készült Data Lake Tools használatával töltik fel a fájlokat a fürt elsődleges tárolójába.

* Azure PowerShell vagy SSH-ügyfél.

* Egy Hadoop a HDInsight-fürtön. A fürtök létrehozásáról további információt a HDInsight- [fürt létrehozása](../hdinsight-hadoop-provision-linux-clusters.md)című témakörben talál.

## <a name="create-the-mapper"></a>A Mapper létrehozása

A Visual Studióban hozzon létre egy új, __Mapper__nevű __Console-alkalmazást__ . Használja az alábbi kódot az alkalmazáshoz:

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

Az alkalmazás létrehozása után hozza létre, hogy létrehozza a `/bin/Debug/mapper.exe` fájlt a projekt könyvtárában.

## <a name="create-the-reducer"></a>A szűkítő létrehozása

A Visual Studióban hozzon létre egy új, __szűkítő__nevű __Console-alkalmazást__ . Használja az alábbi kódot az alkalmazáshoz:

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

Az alkalmazás létrehozása után hozza létre, hogy létrehozza a `/bin/Debug/reducer.exe` fájlt a projekt könyvtárában.

## <a name="upload-to-storage"></a>Feltöltés tárolóba

1. A Visual Studióban nyissa meg a **Server Explorert**.

2. Bontsa ki az **Azure** elemet, majd bontsa ki a **HDInsight** elemet.

3. Ha a rendszer kéri, adja meg az Azure-előfizetés hitelesítő adatait, majd kattintson **a bejelentkezés**elemre.

4. Bontsa ki azt a HDInsight-fürtöt, amelyre telepíteni kívánja az alkalmazást. Megjelenik egy bejegyzés a szöveggel __(alapértelmezett Storage-fiók)__ .

    ![A fürthöz tartozó Storage-fiókot megjelenítő Server Explorer](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

    * Ha ezt a bejegyzést ki lehet bővíteni, az __Azure Storage-fiókot__ használja alapértelmezett tárolóként a fürt számára. Ha meg szeretné tekinteni a fürt alapértelmezett tárolójában lévő fájlokat, bontsa ki a bejegyzést, majd kattintson duplán a __(alapértelmezett tároló)__ elemre.

    * Ha ezt a bejegyzést nem lehet kibontani, a __Azure Data Lake Storage__ használja a fürt alapértelmezett tárolójának. A fürt alapértelmezett tárolójában lévő fájlok megtekintéséhez kattintson duplán az __(alapértelmezett Storage-fiók)__ bejegyzésre.

5. Az. exe fájlok feltöltéséhez használja az alábbi módszerek egyikét:

   * Ha __Azure Storage-fiókot__használ, kattintson a feltöltés ikonra, majd keresse meg a **bin\debug** mappát a **Mapper** projekthez. Végül válassza ki a **mapper. exe** fájlt, majd kattintson **az OK**gombra.

        ![HDInsight feltöltése ikon a Mapper számára](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)
    
   * Ha __Azure Data Lake Storage__használ, kattintson a jobb gombbal egy üresre a fájl listázása területen, majd válassza a __feltöltés__lehetőséget. Végül válassza ki a **mapper. exe** fájlt, és kattintson a **Megnyitás**gombra.

     A __mapper. exe__ feltöltésének befejezése után ismételje meg a feltöltési folyamatot a __csökkentő. exe__ fájlhoz.

## <a name="run-a-job-using-an-ssh-session"></a>Feladatok futtatása: SSH-munkamenet használata

1. Az SSH használatával csatlakozzon a HDInsight-fürthöz. További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. A MapReduce-feladatok elindításához használja a következő parancsok egyikét:

   * __Data Lake Storage Gen2__ használata alapértelmezett tárolóként:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files abfs:///mapper.exe,abfs:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```

   * __Data Lake Storage Gen1__ használata alapértelmezett tárolóként:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files adl:///mapper.exe,adl:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```
    
   * Ha az __Azure Storage__ -t használja alapértelmezett tárolóként:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files wasb:///mapper.exe,wasb:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```

     A következő lista ismerteti, hogy mit tesz az egyes paraméterek:

   * `hadoop-streaming.jar`: A streaming MapReduce funkciót tartalmazó jar-fájl.
   * `-files`: Hozzáadja a `mapper.exe` és `reducer.exe` a fájlokat a feladatokhoz. A `abfs:///`,`adl:///` vagy`wasb:///` minden fájl előtt az alapértelmezett tároló gyökerének elérési útja a fürthöz.
   * `-mapper`: Meghatározza, hogy melyik fájl implementálja a leképezést.
   * `-reducer`: Meghatározza, hogy melyik fájl implementálja a szűkítőt.
   * `-input`: A bemeneti adatok.
   * `-output`: A kimeneti könyvtár.

3. A MapReduce-feladatok befejezése után a következő paranccsal tekintheti meg az eredményeket:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    A következő szöveg egy példa a parancs által visszaadott adatmennyiségre:

        you     1128
        young   38
        younger 1
        youngest        1
        your    338
        yours   4
        yourself        34
        yourselves      3
        youth   17

## <a name="run-a-job-using-powershell"></a>Feladatok futtatása: A PowerShell használata

A következő PowerShell-szkripttel futtasson egy MapReduce-feladatot, és töltse le az eredményeket.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Ez a parancsfájl kéri a fürt bejelentkezési fiókjának nevét és jelszavát, valamint a HDInsight-fürt nevét. A feladatok befejezése után a rendszer letölti a kimenetet egy nevű `output.txt`fájlba. A következő szöveg egy példa `output.txt` a fájlban található fájlokra:

    you     1128
    young   38
    younger 1
    youngest        1
    your    338
    yours   4
    yourself        34
    yourselves      3
    youth   17

## <a name="next-steps"></a>További lépések

A MapReduce és a HDInsight használatával kapcsolatos további információkért lásd: a [MapReduce használata a HDInsight](hdinsight-use-mapreduce.md).

További információ a kaptár C# és a Pig használatáról: [ C# felhasználó által definiált függvény használata Apache Hive és Apache Pig](apache-hadoop-hive-pig-udf-dotnet-csharp.md)használatával.

A Storm on HDInsight C# használatával kapcsolatos információkért lásd: a [HDInsight C# -Apache Storm hez készült topológiák fejlesztése](../storm/apache-storm-develop-csharp-visual-studio-topology.md).
