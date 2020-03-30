---
title: C#, Apache Hive & Apache Pig apache hadoopon – Azure HDInsight
description: Ismerje meg, hogyan használhatja a C# felhasználó által definiált függvényeket (UDF) az Apache Hive és az Apache Pig streamelésével az Azure HDInsightban.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 9ef9eada9b9aec50642a8bf357edab0677868817
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74949389"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-on-apache-hadoop-in-hdinsight"></a>C# felhasználó által definiált függvények használata Apache Hive és Apache Pig segítségével az Apache Hadoop on HDInsight

Ismerje meg, hogyan használhatja a C# felhasználó által definiált függvényeket (UDF) [az Apache Hive](https://hive.apache.org) és az [Apache Pig](https://pig.apache.org) hdinsight-on.

> [!IMPORTANT]
> A jelen dokumentum lépései Linux-alapú HDInsight-fürtökkel működnek. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További információt a [HDInsight-összetevő verziószámozása](../hdinsight-component-versioning.md)című témakörben talál.

A Hive és a Pig is továbbíthat adatokat a külső alkalmazások feldolgozásra. Ezt a folyamatot _streamelésnek nevezzük._ Ha egy .NET-alkalmazást használ, az adatok átaz alkalmazás STDIN, és az alkalmazás visszaadja az eredményeket STDOUT. Az STDIN és az STDOUT segítségével `Console.ReadLine()` és `Console.WriteLine()` konzolalkalmazásból is olvashat és írhat.

## <a name="prerequisites"></a>Előfeltételek

* A .NET Framework 4.5-öt megkísérő C# kód írásának és létrehozásának ismerete.

    Használja, amit IDE akarsz. A [Visual Studio](https://www.visualstudio.com/vs) vagy a [Visual Studio Code használatát](https://code.visualstudio.com/)javasoljuk. A jelen dokumentum lépései a Visual Studio 2019-et használják.

* Az .exe fájlok fürtbe való feltöltésének és a Pig és Hive feladatok futtatásának módja. A [Data Lake Tools for Visual Studio, az](../../data-lake-analytics/data-lake-analytics-data-lake-tools-install.md) [Azure PowerShell](/powershell/azure)és az Azure [CLI szolgáltatást](/cli/azure/install-azure-cli?view=azure-cli-latest)ajánljuk. A jelen dokumentum lépései a Data Lake Tools for Visual Studio segítségével tölthetik fel a fájlokat, és futtathatják a példa Hive-lekérdezést.

    A Hive-lekérdezések futtatásának egyéb módjairól a [Mi az Apache Hive és a HiveQL az Azure HDInsightban című témakörben.](hdinsight-use-hive.md)

* Hadoop a HDInsight-fürtön. A fürt létrehozásáról további információt a [HDInsight-fürtök létrehozása című témakörben talál.](../hdinsight-hadoop-provision-linux-clusters.md)

## <a name="net-on-hdinsight"></a>.NET a HDInsight-on

*A Linux-alapú HDInsight-fürtök* [a Mono (ahttps://mono-project.com) ](https://mono-project.com) .NET alkalmazások futtatásához) használatosak. A 4.2.1-es monó a HDInsight 3.6-os verziójához tartozik.

A Mono kompatibilitásról a .NET Framework verziókkal kapcsolatos további tudnivalókért tanulmányozza a Mono compatibility című [témakört.](https://www.mono-project.com/docs/about-mono/compatibility/)

A . [HDInsight component versions](../hdinsight-component-versioning.md)

## <a name="create-the-c-projects"></a>A C\# projektek létrehozása

A következő szakaszok ismertetik, hogyan hozhat létre egy C# projektet a Visual Studio egy Apache Hive UDF és egy Apache Pig UDF.

### <a name="apache-hive-udf"></a>Apache Hive UDF

C# projekt létrehozása Apache Hive UDF-hez:

1. Indítsa el a Visual Studiót.

2. Válassza **az Új projekt létrehozása**lehetőséget.

3. Az **Új projekt létrehozása** ablakban válassza a **Console App (.NET Framework)** sablont (a C# verziót). Ezután válassza a **Tovább**gombot.

4. Az **Új projekt konfigurálása** ablakban adja meg a *HiveCSharp* **projektnevét,** és keresse meg vagy hozza meg a **helyet** az új projekt mentéséhez. Ezután válassza **a Létrehozás lehetőséget.**

5. A Visual Studio IDE-ben cserélje le *Program.cs* tartalmát a következő kódra:

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

6. A menüsorban válassza a Build Solution **összeállítása** > **Build Solution** lehetőséget a projekt létrehozásához.

7. Zárja be a megoldást.

### <a name="apache-pig-udf"></a>Apache Sertés UDF

C# projekt létrehozása Apache Hive UDF-hez:

1. Nyissa meg a Visual Studiót.

2. A **Start** ablakban válassza **az Új projekt létrehozása**lehetőséget.

3. Az **Új projekt létrehozása** ablakban válassza a **Console App (.NET Framework)** sablont (a C# verziót). Ezután válassza a **Tovább**gombot.

4. Az **Új projekt konfigurálása** ablakban adja meg a *PigUDF* **projektnevét,** és lépjen az új projekt mentéséhez, vagy hozzon létre egy **helyet.** Ezután válassza **a Létrehozás lehetőséget.**

5. A Visual Studio IDE-ben cserélje le *Program.cs* tartalmát a következő kódra:

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

    Ez a kód elemzi a Pig-ből küldött sorokat, és újraformázza a `java.lang.Exception`.

6. A menüsorban válassza a Build Solution **összeállítása** > **Build Solution** lehetőséget a projekt létrehozásához.

7. Hagyja nyitva a megoldást.

## <a name="upload-to-storage"></a>Feltöltés tárolóba

Ezután töltse fel a Hive- és a Pig UDF-alkalmazásokat egy HDInsight-fürt öntárolóba.

1. A Visual Studióban keresse meg a **Kiszolgálókezelő megtekintése** > című**nézetet.**

1. A **Kiszolgálókezelőben**kattintson a jobb gombbal az **Azure-ra,** válassza a Csatlakozás a Microsoft **Azure-előfizetéshez**lehetőséget, és fejezze be a bejelentkezési folyamatot.

1. Bontsa ki azt a HDInsight-fürtöt, amelybe az alkalmazást telepíteni szeretné. A listában szerepel egy szöveget tartalmazó bejegyzés **(Alapértelmezett tárfiók).**

    ![Alapértelmezett tárfiók, HDInsight-fürt, Kiszolgálókezelő](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-storage-account.png)

    * Ha ez a bejegyzés bővíthető, egy **Azure Storage-fiókot** használ a fürt alapértelmezett tárolójaként. A fürt alapértelmezett tárolóján lévő fájlok megtekintéséhez bontsa ki a bejegyzést, majd kattintson duplán az **(Alapértelmezett tároló)** elemre.

    * Ha ez a bejegyzés nem bontható ki, az **Azure Data Lake Storage-t** használja a fürt alapértelmezett tárolójaként. A fürt alapértelmezett tárolóján lévő fájlok megtekintéséhez kattintson duplán az **(Alapértelmezett tárfiók)** bejegyzésre.

1. Az .exe fájlok feltöltéséhez használja az alábbi módszerek egyikét:

    * Ha **Azure Storage-fiókot**használ, válassza a **Blob feltöltése** ikont.

        ![A HDInsight feltöltési ikonja az új projekthez](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-upload-icon.png)

        Az **Új fájl feltöltése** párbeszédpanel **Fájlnév területén**válassza a **Tallózás**gombot. A **Blob feltöltése** párbeszédpanelen nyissa meg a *HiveCSharp* projekt *bin\debug* mappáját, és válassza a *HiveCSharp.exe* fájlt. Végül válassza **a Megnyitás,** majd **az OK gombot** a feltöltés befejezéséhez.

    * Ha **az Azure Data Lake Storage-t**használja, kattintson a jobb gombbal egy üres területre a fájllistában, majd válassza a **Feltöltés parancsot.** Végül válassza a *HiveCSharp.exe* fájlt, és válassza a **Megnyitás**gombot.

    Miután a *HiveCSharp.exe* feltöltés befejeződött, ismételje meg a *PigUDF.exe* fájl feltöltési folyamatát.

## <a name="run-an-apache-hive-query"></a>Apache Hive-lekérdezés futtatása

Most futtathat egy Hive-lekérdezést, amely a Hive UDF-alkalmazást használja.

1. A Visual Studióban keresse meg a **Kiszolgálókezelő megtekintése** > című**nézetet.**

2. Bontsa ki az **Azure** elemet, majd bontsa ki a **HDInsight** elemet.

3. Kattintson a jobb gombbal arra a fürtre, amelybe a *HiveCSharp* alkalmazást telepítette, majd válassza **a Hive-lekérdezés írása parancsot.**

4. A Hive-lekérdezéshez használja a következő szöveget:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasbs:///HiveCSharp.exe;
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
    > A fürthöz használt alapértelmezett tárolótípusnak megfelelő `add file` utasítás megjegyzésének kimegjegyzéstása.

    Ez a lekérdezés `clientid` `devicemake`a `devicemodel` , `hivesampletable`és a mezőket a alkalmazásban választja ki, majd továbbítja a mezőket a *HiveCSharp.exe* alkalmazásnak. A lekérdezés azt várja az alkalmazástól, hogy `clientid`három `phoneLabel`mezőt ad vissza, amelyek et tárolnak , és `phoneHash`. A lekérdezés azt is elvárja, hogy a *HiveCSharp.exe* az alapértelmezett tároló gyökerében található.

5. Váltson az alapértelmezett **interaktív** értékre **batch**értékre, majd válassza a **Küldés** lehetőséget a feladat HDInsight-fürtbe való elküldéséhez. Megnyílik **a Hive feladat összegzése** ablak.

6. A **Frissítés gombra** választva frissíti az összegzést, amíg a **feladat állapota** **befejezettre nem változik.** A feladat kimenetének megtekintéséhez válassza a **Projekt kimenet lehetőséget.**

## <a name="run-an-apache-pig-job"></a>Apache Pig-feladat futtatása

A Pig UDF-alkalmazást használó Pig-feladat is futtatható.

1. Az SSH segítségével csatlakozzon a HDInsight-fürthöz. (Például futtassa `ssh sshuser@<clustername>-ssh.azurehdinsight.net`a parancsot .) További információ: [SSH használata HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md)

2. A Pig parancssor elindításához használja a következő parancsot:

    ```shell
    pig
    ```

    Megjelenik egy `grunt>` üzenet.

3. A .

    ```pig
    DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
    LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
    DUMP DETAILS;
    ```

    Az `DEFINE` utasítás létrehoz `streamer` egy aliast a *PigUDF.exe* alkalmazáshoz, és `CACHE` betölti azt a fürt alapértelmezett tárolójából. Később `streamer` az `STREAM` operátorral együtt feldolgozza az `LOG` egyes sorokat, és az adatokat oszlopsorozatként adja vissza.

    > [!NOTE]
    > A streameléshez használt alkalmazásnevet alias \` esetén a (backtick) karakternek, a használatával a használatával `SHIP`használva pedig a " (egyszeres idézőjel) karakternek kell körülvenni.

4. Az utolsó sor megadása után a feladatnak el kell kezdődnie. A következő szöveghez hasonló kimenetet ad vissza:

    ```output
    (2019-07-15 16:43:25 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
    (2019-07-15 16:43:25 SampleClass5 [DEBUG] detail for id 1976092771)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1317358561)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1737534798)
    (2019-07-15 16:43:25 SampleClass7 [DEBUG] detail for id 1475865947)
    ```

5. A `exit` malacból való kilépéshez használható.

## <a name="next-steps"></a>További lépések

Ebben a dokumentumban megtanulta, hogyan használhatja a Hive és a Pig . Ha meg szeretné tudni, hogyan használhatja a Pythont hive-vel és pig-el, olvassa el [a Python használata Apache Hive-val és Apache Pig használatával a HDInsight ban.](python-udf-hdinsight.md)

A Hive használatának egyéb módjait és a MapReduce használatát az alábbi cikkekben olvashatja:

* [Az Apache Hive használata a HDInsight segítségével](hdinsight-use-hive.md)
* [A MapReduce használata a HDInsightsegítségével](hdinsight-use-mapreduce.md)
* [Pig Latin alapjai](https://pig.apache.org/docs/latest/basic.html)