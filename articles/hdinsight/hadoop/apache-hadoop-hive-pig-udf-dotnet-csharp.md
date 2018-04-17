---
title: C# az Hive és a HDInsight - Azure hadoop Pig használata |} Microsoft Docs
description: Ismerje meg a Hive és az Azure HDInsight streaming Pig használata a C# felhasználói függvény (UDF).
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d83def76-12ad-4538-bb8e-3ba3542b7211
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: b776b214b9d46293a3ab97ad226dffd3107f3430
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="use-c-user-defined-functions-with-hive-and-pig-streaming-on-hadoop-in-hdinsight"></a>C# felhasználó által definiált függvények használata a Hive és a HDInsight hadoop streamelési Pig használatával

Útmutató Apache Hive és a HDInsight Pig C# felhasználói függvény (UDF) használhat.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések működéséhez mind a Linux-alapú, és a Windows-alapú HDInsight-fürtökkel. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További információkért lásd: [HDInsight-összetevők verziószámozása](../hdinsight-component-versioning.md).

Mind a Hive és a Pig teljen el az adatok külső alkalmazások feldolgozásához. Ezt a folyamatot nevezik _streaming_. A .NET-alkalmazások használata esetén az adatok átadódik az alkalmazás STDIN, és az alkalmazás STDOUT-on az eredményeket ad vissza. Írási és olvasási STDIN és a STDOUT, használhatja a `Console.ReadLine()` és `Console.WriteLine()` konzol alkalmazás.

## <a name="prerequisites"></a>Előfeltételek

* Egy írást, és C#-kódban, amelynek célpontja a .NET-keretrendszer 4.5 felépítése ismeretét.

    * Bármilyen IDE szeretne használni. Ajánlott [Visual Studio](https://www.visualstudio.com/vs) 2015-ös, 2017, vagy [Visual Studio Code](https://code.visualstudio.com/). A jelen dokumentumban leírt lépések a Visual Studio 2017 használja.

* Az .exe fájlok feltöltése a fürthöz, és futtassa a Pig és Hive feladat lehetőséget. A Data Lake Tools for Visual Studio, Azure PowerShell és az Azure parancssori felület javasoljuk. Ez a dokumentum használja a Data Lake Tools for Visual Studio töltse fel a fájlokat, és futtassa a példa lépései Hive lekérdezés.

    Egyéb lehetőségeiről futtatásához a Hive a lekérdezéseket és Pig-feladatokhoz, lásd a következő dokumentumokat:

    * [Apache Hive használata a Hdinsightban](hdinsight-use-hive.md)

    * [Apache Pig használata a HDInsight](hdinsight-use-pig.md)

* A Hadoop on HDInsight-fürt. A fürtök létrehozásáról további információk: [HDInsight-fürtök létrehozása](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>A HDInsight .NET

* __Linux-alapú HDInsight__ használó fürtök [monó (https://mono-project.com) ](https://mono-project.com) .NET-alkalmazások futtatására. Monó verzió 4.2.1 3.6 verzió HDInsight része.

    A .NET-keretrendszer verzióival is kompatibilisek monó további információkért lásd: [monó kompatibilitási](http://www.mono-project.com/docs/about-mono/compatibility/).

    Monó adott verzióját használja, tekintse meg a [telepítés vagy frissítés monó](../hdinsight-hadoop-install-mono.md) dokumentum.

* __Windows-alapú HDInsight__ fürtök használata a Microsoft .NET CLR-beli .NET-alkalmazások futtatására.

A .NET-keretrendszer és a HDInsight-verziókról mellékelt monó verziójának további információkért lásd: [HDInsight összetevő verziók](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Hozzon létre a C\# projektek

### <a name="hive-udf"></a>Hive UDF-ben

1. Nyissa meg a Visual Studio és a megoldás létrehozása. Válassza ki a projekttípus **Konzolalkalmazás (.NET-keretrendszer)**, és az új projekt neve **HiveCSharp**.

    > [!IMPORTANT]
    > Válassza ki __.NET-keretrendszer 4.5__ egy Linux-alapú HDInsight-fürt használata. A .NET-keretrendszer verzióival is kompatibilisek monó további információkért lásd: [monó kompatibilitási](http://www.mono-project.com/docs/about-mono/compatibility/).

2. Cserélje le a tartalmát **Program.cs** az alábbi kódra:

    ```csharp
    using System;
    using System.Security.Cryptography;
    using System.Text;
    using System.Threading.Tasks;

    namespace HiveCSharp
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Parse the string, trimming line feeds
                    // and splitting fields at tabs
                    line = line.TrimEnd('\n');
                    string[] field = line.Split('\t');
                    string phoneLabel = field[1] + ' ' + field[2];
                    // Emit new data to stdout, delimited by tabs
                    Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                }
            }
            /// <summary>
            /// Returns an MD5 hash for the given string
            /// </summary>
            /// <param name="input">string value</param>
            /// <returns>an MD5 hash</returns>
            static string GetMD5Hash(string input)
            {
                // Step 1, calculate MD5 hash from input
                MD5 md5 = System.Security.Cryptography.MD5.Create();
                byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                byte[] hash = md5.ComputeHash(inputBytes);

                // Step 2, convert byte array to hex string
                StringBuilder sb = new StringBuilder();
                for (int i = 0; i < hash.Length; i++)
                {
                    sb.Append(hash[i].ToString("x2"));
                }
                return sb.ToString();
            }
        }
    }
    ```

3. A projekt felépítése.

### <a name="pig-udf"></a>Pig UDF-ben

1. Nyissa meg a Visual Studio és a megoldás létrehozása. Válassza ki a projekttípus **Konzolalkalmazás**, és az új projekt neve **PigUDF**.

2. Cserélje le a tartalmát a **Program.cs** fájl a következő kóddal:

    ```csharp
    using System;

    namespace PigUDF
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Fix formatting on lines that begin with an exception
                    if(line.StartsWith("java.lang.Exception"))
                    {
                        // Trim the error info off the beginning and add a note to the end of the line
                        line = line.Remove(0, 21) + " - java.lang.Exception";
                    }
                    // Split the fields apart at tab characters
                    string[] field = line.Split('\t');
                    // Put fields back together for writing
                    Console.WriteLine(String.Join("\t",field));
                }
            }
        }
    }
    ```

    Ez a kód a Pig küldött sorok elemzi és kezdődő sorok újraformázza `java.lang.Exception`.

3. Mentés **Program.cs**, majd a projekt felépítéséhez és.

## <a name="upload-to-storage"></a>Töltse fel a tároló

1. A Visual Studióban nyissa meg a **Server Explorer**.

2. Bontsa ki az **Azure** elemet, majd bontsa ki a **HDInsight** elemet.

3. Ha a rendszer kéri, adja meg Azure-előfizetés hitelesítő adatait, és kattintson **bejelentkezés**.

4. Bontsa ki a kívánja telepíteni az alkalmazást a HDInsight-fürthöz. A szöveg bejegyzés __(alapértelmezett Tárfiók)__ szerepel.

    ![A tárfiók a fürt megjelenítő Server Explorer](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

    * Ez a bejegyzés bővíthetők, ha az egy __Azure Storage-fiók__ a fürt alapértelmezett tárolóként. A fürt az alapértelmezett tároló a fájlok megtekintéséhez bontsa ki a bejegyzést, és majd kattintson duplán a __(alapértelmezett tároló)__.

    * Ez a bejegyzés nem bonthatók ki, ha az __Azure Data Lake Store__ a fürt alapértelmezett tárolóként. A fürt az alapértelmezett tároló a fájlok megtekintéséhez kattintson duplán a __(alapértelmezett Tárfiók)__ bejegyzés.

6. Az .exe fájlok feltöltéséhez használja a következő módszerek egyikét:

    * Ha használ egy __Azure Storage-fiók__, kattintson a Feltöltés ikonra, és keresse a **bin\debug** mappát a **HiveCSharp** projekt. Végül válassza ki a **HiveCSharp.exe** fájlt, és kattintson a **Ok**.

        ![Töltse fel az ikon](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/upload.png)
    
    * Ha használ __Azure Data Lake Store__, kattintson a jobb gombbal egy üres területre a fájl az átjáróra a listában, majd válassza ki __feltöltése__. Végül válassza ki a **HiveCSharp.exe** fájlt, és kattintson a **nyitott**.

    Egyszer a __HiveCSharp.exe__ befejezte a feltöltést, ismételje meg a feltöltési folyamat számára a __PigUDF.exe__ fájlt.

## <a name="run-a-hive-query"></a>Hive-lekérdezések futtatása

1. A Visual Studióban nyissa meg a **Server Explorer**.

2. Bontsa ki az **Azure** elemet, majd bontsa ki a **HDInsight** elemet.

3. Kattintson a jobb gombbal a fürt központilag telepített a **HiveCSharp** alkalmazás számára, és adja **Hive lekérdezés írása**.

4. A Hive-lekérdezést használja a következő szöveget:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasb:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Store
    -- add file adl:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > Állítsa vissza a `add file` utasítást, amely megfelel a fürthöz használt alapértelmezett tárolási típusát.

    Ez a lekérdezés kiválasztja a `clientid`, `devicemake`, és `devicemodel` mezőit a `hivesampletable`, és adja át a mezők a HiveCSharp.exe alkalmazás. A lekérdezés vár az alkalmazás három mező, mint a tárolt `clientid`, `phoneLabel`, és `phoneHash`. A lekérdezés is vár HiveCSharp.exe az alapértelmezett tároló gyökerében található.

5. Kattintson a **Submit** elküldeni a feladatot a HDInsight-fürthöz. A **Hive feladat összegzése** ablak nyílik meg.

6. Kattintson a **frissítése** frissítése csak az Összegzés **feladat állapota** vált **befejezve**. A feladat kimenetére megtekintéséhez kattintson **Job Output**.

## <a name="run-a-pig-job"></a>A Pig-feladat futtatása

1. A HDInsight-fürthöz való kapcsolódáshoz használja a következő módszerek egyikét:

    * Ha használ egy __Linux-alapú__ HDInsight fürt esetén az SSH használata. Például: `ssh sshuser@mycluster-ssh.azurehdinsight.net`. További információkért lásd: [az SSH withHDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)
    
    * Ha használ egy __Windows-alapú__ HDInsight-fürtjéhez, [a fürt távoli asztal használatával csatlakozhat](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)

2. Egyet a következő paranccsal a Pig parancssor elindításához:

        pig

    > [!IMPORTANT]
    > Ha egy Windows-alapú fürtöt használ, az alábbi parancsokat használja:
    > ```
    > cd %PIG_HOME%
    > bin\pig
    > ```

    A `grunt>` kérdés megjelenik.

3. Adja meg a következőket kell futtatnia a .NET-keretrendszer alkalmazás által a Pig feladatot:

        DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
        LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    A `DEFINE` utasítás hoz létre az alias `streamer` pigudf.exe alkalmazásokhoz és `CACHE` betölti azt a fürt alapértelmezett tárolóból. Később `streamer` használják a `STREAM` a naplóban található egyetlen sorok feldolgozni, és térjen vissza az adatokat az oszlopok sorozataként operátor.

    > [!NOTE]
    > Az alkalmazás nevét, az adatfolyamként történő használt kell lennie.%n a \` (backtick) karakter mikor aliasnevet, és ' (aposztróf) használata esetén `SHIP`.

4. Után utolsó sora bevitelét, a feladat elindul. Azt adja vissza kimenet az alábbihoz hasonló:

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

## <a name="next-steps"></a>További lépések

Ebből a dokumentumból megismerte rendelkezik a .NET-keretrendszer alkalmazás a Hive és a Pig használata a HDInsight. Ha szeretné a Hive és a Pig használata a Python, lásd: [Python használni a Hive és a hdinsight Pig](python-udf-hdinsight.md).

Más módokon Pig és a Hive segítségével, és tájékozódhat a MapReduce használata tekintse meg a következő dokumentumokat:

* [A Hive használata a HDInsightban](hdinsight-use-hive.md)
* [A Pig használata a HDInsightban](hdinsight-use-pig.md)
* [Use MapReduce with HDInsight](hdinsight-use-mapreduce.md)
