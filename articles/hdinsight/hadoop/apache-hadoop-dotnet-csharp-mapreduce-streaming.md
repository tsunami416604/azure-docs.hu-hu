---
title: C# használata a HDInsight - Azure hadoop MapReduce
description: Ismerje meg, hogyan használható C# Azure HDInsight az Apache Hadoop MapReduce-megoldásokat hozhat létre.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: hrasheed
ms.openlocfilehash: 0c3525b73560d3edee5bffa7a391fcedeaaa1e48
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545094"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>Használat C# streamelési HDInsight az Apache hadoop MapReduce-

Ismerje meg, hogyan használhatja a C# MapReduce megoldás létrehozása a HDInsight.

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További információkért lásd: [HDInsight összetevők verziószámozása](../hdinsight-component-versioning.md).

Az Apache Hadoop streamelési egy segédprogram, amely lehetővé teszi, hogy egy parancsfájl vagy végrehajtható fájl használatával MapReduce-feladatok futtatásához. Ebben a példában a .NET szolgál a teljesítményleképező és nyomáscsökkentő word-count megoldások megvalósításához.

## <a name="net-on-hdinsight"></a>.NET on HDInsight

__Linux-alapú HDInsight__ -fürtök használata [Mono (https://mono-project.com) ](https://mono-project.com) .NET-alkalmazások futtatására. Monó verzió 4.2.1 megtalálható a HDInsight 3.6-os verzióját. További információ a Mono HDInsight mellékelt verzióját, lásd: [HDInsight összetevő verziók](../hdinsight-component-versioning.md). 

A .NET-keretrendszer-verziókat monó kompatibilitást további információkért lásd: [monó kompatibilitási](https://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Hadoop streamelési működése

Az ebben a dokumentumban streamelési használt alapvető folyamat a következőképpen történik:

1. Hadoop (ebben a példában mapper.exe) leképezőjét STDIN az adatokat továbbítja.
2. A teljesítményleképező feldolgozza az adatokat, és a STDOUT tabulátorral elválasztott kulcs/érték párok bocsát ki.
3. A kimenet hadoop olvassa el, és STDIN a nyomáscsökkentő (reducer.exe ebben a példában), majd továbbítja.
4. A nyomáscsökkentő a tabulátorral tagolt kulcs/érték párok beolvassa, feldolgozza az adatokat, és majd bocsát ki az eredmény tabulátorral elválasztott kulcs/érték párokként STDOUT-on.
5. A kimenet Hadoop által olvasott és írt a kimeneti könyvtárba.

A streamelési további információkért lásd: [Hadoop Streamelési](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Előfeltételek

* Írás és létrehozása a C#-kódot, amely a .NET-keretrendszer 4.5 célozza ismeretét. A jelen dokumentumban leírt lépések a Visual Studio 2017-et használja.

* Oly módon, .exe fájlok feltöltése a fürtöt. A jelen dokumentumban leírt lépések a Data Lake Tools for Visual Studio használatával a fájlok feltöltése a fürt elsődleges tároló.

* Az Azure PowerShell vagy SSH-ügyfelet.

* A Hadoop HDInsight-fürtön. Fürt létrehozásával kapcsolatos további információkért lásd: [hozzon létre egy HDInsight-fürt](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-the-mapper"></a>Hozzon létre a leképezője

A Visual Studióban hozzon létre egy új __Konzolalkalmazás__ nevű __eseményleképező__. Az alkalmazás a következő kód használatával:

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

Miután létrehozta az alkalmazást, építhet ki, hogy a `/bin/Debug/mapper.exe` fájlt a projekt könyvtárában.

## <a name="create-the-reducer"></a>A nyomáscsökkentő létrehozása

A Visual Studióban hozzon létre egy új __Konzolalkalmazás__ nevű __nyomáscsökkentő__. Az alkalmazás a következő kód használatával:

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

Miután létrehozta az alkalmazást, építhet ki, hogy a `/bin/Debug/reducer.exe` fájlt a projekt könyvtárában.

## <a name="upload-to-storage"></a>Feltöltés tárolóba

1. A Visual Studióban nyissa meg a **Server Explorer**.

2. Bontsa ki az **Azure** elemet, majd bontsa ki a **HDInsight** elemet.

3. Ha a rendszer kéri, adja meg az Azure-előfizetés hitelesítő adatait, és kattintson **bejelentkezés**.

4. Bontsa ki a HDInsight-fürt, akinél szeretné az alkalmazást. Egy bejegyzés az a szöveg __(alapértelmezett Tárfiók)__ szerepel a listán.

    ![A storage-fiók a fürt megjelenítése a Server Explorerben](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/storage.png)

    * Ez a bejegyzés bővíthetők ki, ha az egy __Azure Storage-fiók__ a fürt alapértelmezett tárolóként. Az alapértelmezett tároló a fürt számára a fájlok megtekintését, bontsa ki a bejegyzést, és kattintson duplán a __(alapértelmezett tároló)__.

    * Ha ez a bejegyzés nem bonthatók ki, használja a __Azure Data Lake Storage__ az alapértelmezett tárolóként, a fürt számára. A fájlok a fürt számára az alapértelmezett tároló megjelenítéséhez kattintson duplán a __(alapértelmezett Tárfiók)__ bejegyzés.

5. Az .exe fájlok feltöltéséhez használja a következő módszerek egyikét:

   * Használata egy __Azure Storage-fiók__, kattintson a Feltöltés ikonra, és keresse meg a **bin\debug** mappáját a **eseményleképező** projekt. Végül válassza ki a **mapper.exe** fájlt, és kattintson a **Ok**.

       ![ikon feltöltése](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/upload.png)
    
   * Ha használ __Azure Data Lake Storage__, kattintson a jobb gombbal egy üres területre a fájl listáján, és válassza __feltöltése__. Végül válassza ki a **mapper.exe** fájlt, és kattintson a **nyílt**.

     Miután a __mapper.exe__ feltöltés befejeződött, a feltöltési folyamat esetében ismételje meg a __reducer.exe__ fájl.

## <a name="run-a-job-using-an-ssh-session"></a>A feladat futtatása: Egy SSH-munkamenet használata

1. Az SSH használata a HDInsight-fürthöz való csatlakozáshoz. További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. A MapReduce feladat indításához használja a következő parancsok egyikét:

   * Ha használ __Data Lake Storage Gen2__ az alapértelmezett tároló:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files abfs:///mapper.exe,abfs:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```

   * Ha használ __Data Lake Storage Gen1__ az alapértelmezett tároló:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files adl:///mapper.exe,adl:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```
    
   * Ha használ __Azure Storage__ az alapértelmezett tároló:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files wasb:///mapper.exe,wasb:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```

     Az alábbi lista ismerteti, hogy minden paraméter leírása:

   * `hadoop-streaming.jar`: A jar-fájlt, amely tartalmazza a folyamatos átviteli MapReduce-szolgáltatásokat.
   * `-files`: Hozzáadja a `mapper.exe` és `reducer.exe` fájlokat a feladatot. A `abfs:///`,`adl:///` vagy `wasb:///` előtt minden egyes fájl elérési útját a fürt alapértelmezett tárolója gyökerében.
   * `-mapper`: Itt adhatja meg, melyik fájl leképezőjét valósítja meg.
   * `-reducer`: Itt adhatja meg, melyik fájlt a nyomáscsökkentő valósítja meg.
   * `-input`: A bemeneti adatok.
   * `-output`: A kimeneti könyvtárba.

3. A MapReduce feladat befejezése után az eredmények megtekintéséhez használja a következő:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    A következő szöveg Ez a parancs által visszaadott adatok egy példát:

        you     1128
        young   38
        younger 1
        youngest        1
        your    338
        yours   4
        yourself        34
        yourselves      3
        youth   17

## <a name="run-a-job-using-powershell"></a>A feladat futtatása: A PowerShell használata

Használja a következő PowerShell-parancsfájl futtatása egy MapReduce-feladatot, és töltse le az eredményeket.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Ez a szkript kéri a fürt bejelentkezési fiók nevét és jelszavát, és a HDInsight-fürt neve. A feladat befejezése után a kimenet nevű fájl töltődik le `output.txt`. A következő szöveget a példázza az adatok a `output.txt` fájlt:

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

A MapReduce használata a HDInsight további információkért lásd: [MapReduce használata a HDInsight-](hdinsight-use-mapreduce.md).

Használatával kapcsolatos információk C# tekintse meg a Hive és a Piggel [használatát egy C# felhasználó által definiált függvény az Apache Hive- és Apache Pig](apache-hadoop-hive-pig-udf-dotnet-csharp.md).

Tájékoztatást C# a HDInsight alatt futó Stormmal, lásd: [Develop C# a HDInsight Apache Storm-topológiák](../storm/apache-storm-develop-csharp-visual-studio-topology.md).
