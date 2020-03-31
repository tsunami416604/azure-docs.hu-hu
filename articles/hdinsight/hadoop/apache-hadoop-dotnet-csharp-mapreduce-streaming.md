---
title: C# használata a MapReduce segítségével a Hadoopon a HDInsightban – Azure
description: Ismerje meg, hogyan hozhat létre MapReduce megoldásokat az Azure HDInsightban az Apache Hadoop segítségével a C# használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: 025b5c5c1e3b8543111e112202906ef6f1fdb482
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74561803"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>C# használata a MapReduce streameléssel az Apache Hadoopon a HDInsightban

Ismerje meg, hogyan használhatja a C# segítségével mapreduce megoldás létrehozása a HDInsight.

Az Apache Hadoop streaming egy olyan segédprogram, amely lehetővé teszi a MapReduce feladatok futtatását parancsfájl vagy végrehajtható fájl használatával. Ebben a példában a .NET a szószámláló megoldás leképező jének és szűkítőjének megvalósítására szolgál.

## <a name="net-on-hdinsight"></a>.NET a HDInsight-on

A HDInsight-fürtök a [Mono (ahttps://mono-project.com) ](https://mono-project.com) .NET alkalmazások futtatásához) használatosak. A 4.2.1-es monó a HDInsight 3.6-os verziójához tartozik. A HDInsight ban található Mono verziójáról további információt az [Apache Hadoop különböző HDInsight-verziókkal elérhető összetevői](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)című témakörben talál.

A Mono kompatibilitásról a .NET Framework verziókkal kapcsolatos további tudnivalókért tanulmányozza a Mono compatibility című [témakört.](https://www.mono-project.com/docs/about-mono/compatibility/)

## <a name="how-hadoop-streaming-works"></a>A Hadoop streaming működése

A dokumentumban a streameléshez használt alapvető folyamat a következő:

1. A Hadoop adatokat ad át az STDIN leképezőjének ( ebben a példában a*mapper.exe).*
2. A leképező feldolgozza az adatokat, és tabulátorral tagolt kulcs-/értékpárokat bocsát ki az STDOUT-ra.
3. A kimenetet a Hadoop olvassa be, majd átadja a szűkítőnek ( ebben a példában a*reducer.exe)* az STDIN-en.
4. A szűkítő beolvassa a tabulátorral tagolt kulcs/érték párokat, feldolgozza az adatokat, majd az eredményt tabulátorral tagolt kulcs/érték párokként bocsátja ki az STDOUT-on.
5. A kimenetet a Hadoop olvassa be, és a kimeneti könyvtárba írja.

A streameléssel kapcsolatos további információkért lásd: [Hadoop Streaming](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Előfeltételek

* Visual Studio.

* A .NET Framework 4.5-öt megkísérő C# kód írásának és létrehozásának ismerete.

* Az .exe fájlok fürtbe való feltöltésének módja. A jelen dokumentum lépései a Data Lake Tools for Visual Studio segítségével tölthetik fel a fájlokat a fürt elsődleges tárolójába.

* A PowerShell használata esetén szüksége lesz az [Az modulra.](https://docs.microsoft.com/powershell/azure/overview)

* SSH-ügyfél (nem kötelező). További információ: [Csatlakozás a HDInsighthoz (Apache Hadoop) az SSH használatával.](../hdinsight-hadoop-linux-use-ssh-unix.md)

* Apache Hadoop-fürt a HDInsighton. Lásd: [Első lépések a HDInsight linuxos alkalmazásával.](../hadoop/apache-hadoop-linux-tutorial-get-started.md)

* A fürtök [elsődleges tárolóURI-séma.](../hdinsight-hadoop-linux-information.md#URI-and-scheme) Ez az `wasb://` Azure Storage, `abfs://` az Azure Data `adl://` Lake Storage Gen2 vagy az Azure Data Lake Storage Gen1 esetében lesz. Ha az Azure Storage vagy a Data Lake Storage Gen2 biztonságos átvitele engedélyezve van, az URI a biztonságos átvitel lesz, `wasbs://` illetve `abfss://`a Lásd még: biztonságos [átvitel.](../../storage/common/storage-require-secure-transfer.md)


## <a name="create-the-mapper"></a>A leképező létrehozása

A Visual Studio-ban hozzon létre *mapper*egy új . Az alkalmazáshoz a következő kódot használja:

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

Az alkalmazás létrehozása után hozza létre a */bin/Debug/mapper.exe* fájl létrehozásához a projektkönyvtárban.

## <a name="create-the-reducer"></a>A szűkítő létrehozása

A Visual Studio-ban hozzon létre egy *új*. Az alkalmazáshoz a következő kódot használja:

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

Az alkalmazás létrehozása után hozza létre a */bin/Debug/reducer.exe* fájl létrehozásához a projektkönyvtárban.

## <a name="upload-to-storage"></a>Feltöltés tárolóba

Ezután fel kell töltenie a *leképező* és *a szűkítő* alkalmazásokat a HDInsight-tárolóba.

1. A Visual Studióban válassza a**Kiszolgálókezelő** **megtekintése** > lehetőséget.

1. Kattintson **Azure**a jobb gombbal az Azure-ra, válassza **a Csatlakozás a Microsoft Azure-előfizetéshez lehetőséget...** és fejezze be a bejelentkezési folyamatot.

1. Bontsa ki azt a HDInsight-fürtöt, amelybe az alkalmazást telepíteni szeretné. A listában szerepel egy szöveget tartalmazó bejegyzés **(Alapértelmezett tárfiók).**

   ![Tárfiók, HDInsight-fürt, Kiszolgálókezelő, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

   * Ha az **(alapértelmezett tárfiók)** bejegyzés kibontható, az **Azure Storage-fiókot** használja a fürt alapértelmezett tárolójaként. A fürt alapértelmezett tárolóján lévő fájlok megtekintéséhez bontsa ki a bejegyzést, majd kattintson duplán **az (Alapértelmezett tároló)** elemre.

   * Ha az **(alapértelmezett tárfiók)** bejegyzés nem bontható ki, az **Azure Data Lake Storage-t** használja a fürt alapértelmezett tárolójaként. A fürt alapértelmezett tárolóján lévő fájlok megtekintéséhez kattintson duplán az **(Alapértelmezett tárfiók)** bejegyzésre.

1. Az .exe fájlok feltöltéséhez használja az alábbi módszerek egyikét:

    * Ha **Azure Storage-fiókot**használ, válassza a **Blob feltöltése** ikont.

        ![A HDInsight feltöltési ikonja a Visual Studio mapperéhez](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)

        Az **Új fájl feltöltése** párbeszédpanel **Fájlnév területén**válassza a **Tallózás**gombot. A **Blob feltöltése** párbeszédpanelen nyissa meg a *leképező* projekt *bin\debug* mappáját, és válassza a *mapper.exe* fájlt. Végül válassza **a Megnyitás,** majd **az OK gombot** a feltöltés befejezéséhez.

    * Az **Azure Data Lake Storage**esetében kattintson a jobb gombbal egy üres területre a fájllistában, majd válassza a **Feltöltés parancsot.** Végül jelölje ki a *mapper.exe* fájlt, majd kattintson **a Megnyitás gombra.**

    Miután a *mapper.exe* feltöltés befejeződött, ismételje meg a *reducer.exe* fájl feltöltési folyamatát.

## <a name="run-a-job-using-an-ssh-session"></a>Feladat futtatása: SSH-munkamenet használata

Az alábbi eljárás azt ismerteti, hogyan futtathatok Egy MapReduce feladatot SSH-munkamenethasználatával:

1. Az [ssh paranccsal](../hdinsight-hadoop-linux-use-ssh-unix.md) csatlakozhat a fürthöz. Az alábbi parancs szerkesztésével cserélje le a CLUSTERNAME-t a fürt nevére, majd írja be a parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A MapReduce feladat elindításához használja az alábbi parancsok egyikét:

   * Ha az alapértelmezett tárhely az **Azure Storage:**

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files wasbs:///mapper.exe,wasbs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

    * Ha az alapértelmezett tároló a **Data Lake Storage Gen1**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files adl:///mapper.exe,adl:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   * Ha az alapértelmezett tároló a **Data Lake Storage Gen2**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files abfs:///mapper.exe,abfs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   Az alábbi lista azt ismerteti, hogy az egyes paraméterek és beállítások mit jelentenek:

   * *hadoop-streaming.jar*: Megadja a streamelt MapReduce funkciót tartalmazó jar fájlt.
   * `-files`: Megadja a feladat *mapper.exe* és *reducer.exe fájljait.* A `wasbs:///` `adl:///`, `abfs:///` vagy protokolldeklaráció minden fájl előtt a fürt alapértelmezett tárolójának gyökéréhez vezető elérési út.
   * `-mapper`: Megadja a leképezőt megvalósító fájlt.
   * `-reducer`: Megadja a szűkítőt megvalósító fájlt.
   * `-input`: Megadja a bemeneti adatokat.
   * `-output`: Megadja a kimeneti könyvtárat.

3. A MapReduce feladat befejezése után a következő paranccsal tekintse meg az eredményeket:

   ```bash
   hdfs dfs -text /example/wordcountout/part-00000
   ```

   A következő szöveg egy példa a parancs által visszaadott adatokra:

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

## <a name="run-a-job-using-powershell"></a>Feladat futtatása: PowerShell használata

A következő PowerShell-parancsfájl használatával futtasson egy MapReduce feladatot, és töltse le az eredményeket.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Ez a parancsfájl a fürt bejelentkezési fiókjának nevét és jelszavát, valamint a HDInsight-fürt nevét kéri. A feladat befejezése után a kimenet egy *output.txt*nevű fájlba töltődik le. A következő szöveg egy példa a `output.txt` fájlban lévő adatokra:

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

## <a name="next-steps"></a>További lépések

A MapReduce és a HDInsight használatáról a [MapReduce használata az Apache Hadoopban a HDInsight ban című témakörben talál](hdinsight-use-mapreduce.md)további információt.

A C# hive-vel és pig-szel való használatáról a [C# felhasználó által definiált függvény használata az Apache Hive és](apache-hadoop-hive-pig-udf-dotnet-csharp.md)az Apache Pig használatával című témakörben talál.

A C# és a Storm használata a HDInsight-on című témakörben talál további információt az [Apache Storm C# topológiáinak fejlesztése a HDInsight-on című témakörben.](../storm/apache-storm-develop-csharp-visual-studio-topology.md)
