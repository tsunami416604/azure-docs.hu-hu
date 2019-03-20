---
title: Használat C# az Apache Hive és a HDInsight - Azure Apache hadoop Apache Pig
description: Ismerje meg, hogyan használható C# felhasználó által definiált függvények (UDF) az Apache Hive és a streamelési Azure HDInsight az Apache Pig.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: hrasheed
ms.openlocfilehash: df8196389ecb92229ff9495002c4bce394d7c048
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202707"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-streaming-on-apache-hadoop-in-hdinsight"></a>Használat C# az Apache Hive és a HDInsight az Apache hadoop streamelési Apache Pig, felhasználó által definiált függvények

Ismerje meg, hogyan használható C# felhasználó által definiált függvények (UDF) az Apache Hive és a HDInsight Apache Pig.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések mind a Linux-alapú és a Windows-alapú HDInsight-fürtökkel működik. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További információkért lásd: [HDInsight összetevők verziószámozása](../hdinsight-component-versioning.md).

Mind a Hive és Pig adhat át adatokat a feldolgozáshoz külső alkalmazások. Ez a folyamat más néven _streamelési_. .NET-alkalmazás használata esetén az adatok átadott az alkalmazás a STDIN, és az alkalmazás STDOUT-on az eredményeket adja vissza. Írási és olvasási STDIN és STDOUT, használhatja a `Console.ReadLine()` és `Console.WriteLine()` a egy konzolalkalmazást.

## <a name="prerequisites"></a>Előfeltételek

* Írás és létrehozása a C#-kódot, amely a .NET-keretrendszer 4.5 célozza ismeretét.

    * Bármilyen kívánt IDE használata. Javasoljuk, hogy [Visual Studio](https://www.visualstudio.com/vs) 2015, 2017-es, vagy [Visual Studio Code](https://code.visualstudio.com/). A jelen dokumentumban leírt lépések a Visual Studio 2017-et használja.

* Oly módon, .exe fájlok feltöltése a fürtöt, és futtassa a Pig and Hive-feladatokat. Javasoljuk, hogy a Data Lake Tools for Visual Studio, az Azure PowerShell-lel és az Azure klasszikus parancssori felület. Ez a dokumentum használatát a Data Lake Tools for Visual Studio használatával töltse fel a fájlokat, és futtassa a példa lépései Hive-lekérdezés.

    Információ más különböző módon futtathatja a Hive-lekérdezések és a Pig-feladatokhoz, az alábbi dokumentumokban talál:

    * [Az Apache Hive használata a HDInsight](hdinsight-use-hive.md)

    * [Az Apache Pig használata a HDInsight](hdinsight-use-pig.md)

* A Hadoop HDInsight-fürtön. Fürt létrehozásával kapcsolatos további információkért lásd: [hozzon létre egy HDInsight-fürt](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>.NET on HDInsight

* __Linux-alapú HDInsight__ -fürtök használatával [Mono (https://mono-project.com) ](https://mono-project.com) .NET-alkalmazások futtatására. Monó verzió 4.2.1 megtalálható a HDInsight 3.6-os verzióját.

    A .NET-keretrendszer-verziókat monó kompatibilitást további információkért lásd: [monó kompatibilitási](https://www.mono-project.com/docs/about-mono/compatibility/).

    Mono egy adott verzióját használja, tekintse meg a [telepítés vagy frissítés Mono](../hdinsight-hadoop-install-mono.md) dokumentumot.

* __Windows-alapú HDInsight__ fürtök használata a Microsoft .NET CLR-beli .NET-alkalmazások futtatására.

A verzió a .NET-keretrendszer és a HDInsight-verziók mellékelt Mono további információkért lásd: [HDInsight összetevő verziók](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Hozzon létre a C\# projektek

### <a name="apache-hive-udf"></a>Apache Hive UDF

1. Nyissa meg a Visual Studio és a egy megoldás létrehozásához. A projekt típusaként válassza **Console App (.NET Framework)**, és nevezze el az új projekt **HiveCSharp**.

    > [!IMPORTANT]
    > Válassza ki __.NET-keretrendszer 4.5__ egy Linux-alapú HDInsight-fürt használata. A .NET-keretrendszer-verziókat monó kompatibilitást további információkért lásd: [monó kompatibilitási](https://www.mono-project.com/docs/about-mono/compatibility/).

2. Cserélje le a tartalmát **Program.cs** a következő kóddal:

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

### <a name="apache-pig-udf"></a>Apache Pig UDF

1. Nyissa meg a Visual Studio és a egy megoldás létrehozásához. A projekt típusaként válassza **Konzolalkalmazás**, és nevezze el az új projekt **PigUDF**.

2. Cserélje le a tartalmát a **Program.cs** fájlt a következő kóddal:

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

    Ez a kód a sorokat, a Pig küldött elemzi, és kezdődő sorokat újraformázza `java.lang.Exception`.

3. Mentés **Program.cs**, és ezután hozza létre a projektet.

## <a name="upload-to-storage"></a>A storage feltöltése

1. A Visual Studióban nyissa meg a **Server Explorer**.

2. Bontsa ki az **Azure** elemet, majd bontsa ki a **HDInsight** elemet.

3. Ha a rendszer kéri, adja meg az Azure-előfizetés hitelesítő adatait, és kattintson **bejelentkezés**.

4. Bontsa ki a HDInsight-fürt, akinél szeretné az alkalmazást. Egy bejegyzés az a szöveg __(alapértelmezett Tárfiók)__ szerepel a listán.

    ![A storage-fiók a fürt megjelenítése a Server Explorerben](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

    * Ez a bejegyzés bővíthetők ki, ha az egy __Azure Storage-fiók__ a fürt alapértelmezett tárolóként. Az alapértelmezett tároló a fürt számára a fájlok megtekintését, bontsa ki a bejegyzést, és kattintson duplán a __(alapértelmezett tároló)__.

    * Ha ez a bejegyzés nem bonthatók ki, használja a __Azure Data Lake Storage__ az alapértelmezett tárolóként, a fürt számára. A fájlok a fürt számára az alapértelmezett tároló megjelenítéséhez kattintson duplán a __(alapértelmezett Tárfiók)__ bejegyzés.

6. Az .exe fájlok feltöltéséhez használja a következő módszerek egyikét:

   * Ha használja egy __Azure Storage-fiók__, kattintson a Feltöltés ikonra, és keresse meg a **bin\debug** mappát a **HiveCSharp** projekt. Végül válassza ki a **HiveCSharp.exe** fájlt, és kattintson a **Ok**.

       ![ikon feltöltése](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/upload.png)
    
   * Ha használ __Azure Data Lake Storage__, kattintson a jobb gombbal egy üres területre a fájl listáján, és válassza __feltöltése__. Végül válassza ki a **HiveCSharp.exe** fájlt, és kattintson a **nyílt**.

     Miután a __HiveCSharp.exe__ feltöltés befejeződött, a feltöltési folyamat esetében ismételje meg a __PigUDF.exe__ fájl.

## <a name="run-an-apache-hive-query"></a>Az Apache Hive-lekérdezés futtatása

1. A Visual Studióban nyissa meg a **Server Explorer**.

2. Bontsa ki az **Azure** elemet, majd bontsa ki a **HDInsight** elemet.

3. Kattintson a jobb gombbal a fürt üzembe helyezett a **HiveCSharp** alkalmazáshoz, és adja meg **egy Hive-lekérdezés írása**.

4. A Hive-lekérdezést használja a következő szöveget:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasb:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen1
    -- add file adl:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen2
    -- add file abfs:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > Állítsa vissza a `add file` , amely a fürt alapértelmezett tárolási típus megfelel kivonat.

    Ez a lekérdezés kiválasztja a `clientid`, `devicemake`, és `devicemodel` mezőit a `hivesampletable`, és átadja a mezőket a HiveCSharp.exe alkalmazás. A lekérdezés vár az alkalmazás három mezőt, amelyeket a rendszer, visszaadandó `clientid`, `phoneLabel`, és `phoneHash`. A lekérdezés is vár HiveCSharp.exe az alapértelmezett tároló gyökerében található.

5. Kattintson a **küldés** elküldeni a feladatot a HDInsight-fürthöz. A **Hive feladat összegzése** ablak nyílik meg.

6. Kattintson a **frissítése** frissítse az összefoglalást, amíg a **feladatállapot** vált **befejezve**. Feladat kimenetének megtekintéséhez kattintson **Feladatkimenet**.

## <a name="run-an-apache-pig-job"></a>Az Apache Pig-feladat futtatása

1. Az SSH használata a HDInsight-fürthöz való csatlakozáshoz. Például: `ssh sshuser@mycluster-ssh.azurehdinsight.net`. További információkért lásd: [az SSH withHDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)

2. Egyet a következő paranccsal a Pig parancssor elindításához:

        pig

    > [!IMPORTANT]
    > Ha egy Windows-alapú fürtöt használ, használja a következő parancsokat:
    > ```
    > cd %PIG_HOME%
    > bin\pig
    > ```

    A `grunt>` kérdés megjelenik.

3. Adja meg a következőket kell futtatnia, amely a .NET-keretrendszer alkalmazás használja a Pig feladatot:

        DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
        LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    A `DEFINE` utasítás létrehoz egy aliast `streamer` pigudf.exe alkalmazásokhoz, és `CACHE` tölt be a fürt alapértelmezett tárolója. Később `streamer` együtt használják a `STREAM` operátor dolgozza fel a naplóban szereplő egyetlen sorok és oszlopok sorozataként állítja vissza az adatokat.

    > [!NOTE]
    > Az alkalmazás nevét, hogy használt streamelési kell lennie.%n a \` (használni kívánt szintaxiskiemelést) karaktert, amikor aliasnévvel, és a "(szimpla idézőjel) együtt használva `SHIP`.

4. Miután megadta az utolsó sort, a feladat indításának. Ez hasonló kimenetet ad vissza a következő szöveget:

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

## <a name="next-steps"></a>További lépések

Ebben a dokumentumban megtanulhatta, a .NET-keretrendszer alkalmazás Hive és a Pig használata a HDInsight. Ha szeretné, hogyan Python használata a Hive és Pig, lásd: [Python használata az Apache Hive és a HDInsight Apache Pig](python-udf-hdinsight.md).

Egyéb módjaival, a Pig and Hive használja, és további információ a MapReduce a következő dokumentumokban talál:

* [Az Apache Hive használata a HDInsight](hdinsight-use-hive.md)
* [Az Apache Pig használata a HDInsight](hdinsight-use-pig.md)
* [A MapReduce használata a HDInsight](hdinsight-use-mapreduce.md)
