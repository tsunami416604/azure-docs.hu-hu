---
title: Használat C# a Apache Hive és az Apache Pig használatával Apache Hadoop HDInsight-Azure-ban
description: Ismerje meg, hogyan C# használhatók a felhasználó által definiált függvények (UDF) Apache Hive és Apache Pig streamingtel az Azure HDInsight-ben.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: hrasheed
ms.openlocfilehash: c05e72cd28c78b26b7c23a123b133d10147a4421
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810784"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-on-apache-hadoop-in-hdinsight"></a>Felhasználó C# által definiált függvények használata az Apache Hive és az Apache Pig használatával Apache Hadoop HDInsight

Ismerje meg, hogyan C# használhatók a felhasználó által definiált függvények (UDF) Apache Hive és Apache Pig használatával a HDInsight.

> [!IMPORTANT]
> A jelen dokumentumban ismertetett lépések a Linux-alapú és a Windows-alapú HDInsight-fürtökkel is működnek. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További információ: HDInsight- [összetevő verziószámozása](../hdinsight-component-versioning.md).

A kaptár és a Pig is továbbíthat az adatfeldolgozásra külső alkalmazásoknak. Ezt a folyamatot _adatfolyamként_nevezzük. .NET-alkalmazás használatakor a rendszer az adatok átadását a STDIN-re továbbítja az alkalmazásnak, és az alkalmazás az STDOUT-on lévő eredményeket adja vissza. Az stdin-ből és az stdout `Console.ReadLine()` -ból való olvasásra és írásra használhatja a és `Console.WriteLine()` a konzolt.

## <a name="prerequisites"></a>Előfeltételek

* A .NET-keretrendszer 4,5-es C# példányát tároló kód írásával és összeállításával kapcsolatos ismeretek.

    * Tetszőleges IDE-t használhat. A [Visual studio](https://www.visualstudio.com/vs) 2015, a 2017 vagy a [Visual Studio Code](https://code.visualstudio.com/)használatát javasoljuk. A jelen dokumentumban ismertetett lépések a Visual Studio 2017-et használják.

* Az. exe fájlok feltöltése a fürtbe, valamint a Pig és a kaptár feladatok futtatása. Javasoljuk, hogy a Visual Studio, a Azure PowerShell és a klasszikus Azure CLI Data Lake eszközeit. A jelen dokumentumban szereplő lépések a Visual studióhoz készült Data Lake Tools használatával töltik fel a fájlokat, és futtatják a példa kaptár-lekérdezést.

    A kaptár-lekérdezések és a Pig-feladatok futtatásának egyéb módjaival kapcsolatos további információkért tekintse meg a következő dokumentumokat:

    * [Apache Hive használata a HDInsight](hdinsight-use-hive.md)

    * [Az Apache Pig és a HDInsight használata](hdinsight-use-pig.md)

* Egy Hadoop a HDInsight-fürtön. A fürtök létrehozásáról további információt a HDInsight- [fürt létrehozása](../hdinsight-hadoop-provision-linux-clusters.md)című témakörben talál.

## <a name="net-on-hdinsight"></a>.NET on HDInsight

* __Linux-alapú HDInsight-__ fürtök [mono használatával https://mono-project.com) (](https://mono-project.com) .NET-alkalmazások futtatásához). A HDInsight 3,6-es verziója tartalmazza a Mono 4.2.1-es verzióját.

    További információ a .NET-keretrendszer verzióival való monó kompatibilitásról: [monó kompatibilitás](https://www.mono-project.com/docs/about-mono/compatibility/).

* A __Windows-alapú HDInsight-__ fürtök a Microsoft .net CLR-t használják .NET-alkalmazások futtatására.

A .NET-keretrendszer és a Mono HDInsight-verziókhoz tartozó verziójával kapcsolatos további információkért lásd: [HDInsight-összetevő verziói](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>A C\# projektek létrehozása

### <a name="apache-hive-udf"></a>UDF Apache Hive

1. Nyissa meg a Visual studiót, és hozzon létre egy megoldást. A projekt típusa beállításnál válassza a **konzolos alkalmazás (.NET-keretrendszer)** lehetőséget, és nevezze el az új projekt **HiveCSharp**.

    > [!IMPORTANT]
    > Ha Linux-alapú HDInsight-fürtöt használ, válassza a __.NET-keretrendszer 4,5__ lehetőséget. További információ a .NET-keretrendszer verzióival való monó kompatibilitásról: [monó kompatibilitás](https://www.mono-project.com/docs/about-mono/compatibility/).

2. Cserélje le a **program.cs** tartalmát a következő kódra:

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

1. Nyissa meg a Visual studiót, és hozzon létre egy megoldást. A projekt típusa beállításnál válassza a **konzol alkalmazás**lehetőséget, és nevezze el az új projekt **PigUDF**.

2. Cserélje le a **program.cs** fájl tartalmát a következő kódra:

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

    Ez a kód elemzi a sertésből eljuttatott sorokat, és újraformázza a `java.lang.Exception`-val kezdődő sorokat.

3. Mentse a **program.cs**, majd hozza létre a projektet.

## <a name="upload-to-storage"></a>Feltöltés tárolóba

1. A Visual Studióban nyissa meg a **Server Explorert**.

2. Bontsa ki az **Azure** elemet, majd bontsa ki a **HDInsight** elemet.

3. Ha a rendszer kéri, adja meg az Azure-előfizetés hitelesítő adatait, majd kattintson **a bejelentkezés**elemre.

4. Bontsa ki azt a HDInsight-fürtöt, amelyre telepíteni kívánja az alkalmazást. Megjelenik egy bejegyzés a szöveggel __(alapértelmezett Storage-fiók)__ .

    ![A fürthöz tartozó Storage-fiókot megjelenítő Server Explorer](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

    * Ha ezt a bejegyzést ki lehet bővíteni, az __Azure Storage-fiókot__ használja alapértelmezett tárolóként a fürt számára. Ha meg szeretné tekinteni a fürt alapértelmezett tárolójában lévő fájlokat, bontsa ki a bejegyzést, majd kattintson duplán a __(alapértelmezett tároló)__ elemre.

    * Ha ezt a bejegyzést nem lehet kibontani, a __Azure Data Lake Storage__ használja a fürt alapértelmezett tárolójának. A fürt alapértelmezett tárolójában lévő fájlok megtekintéséhez kattintson duplán az __(alapértelmezett Storage-fiók)__ bejegyzésre.

6. Az. exe fájlok feltöltéséhez használja az alábbi módszerek egyikét:

   * Ha __Azure Storage-fiókot__használ, kattintson a feltöltés ikonra, majd keresse meg a **Bin\debug** mappát a **HiveCSharp** projekthez. Végül válassza ki a **HiveCSharp. exe** fájlt, és kattintson **az OK**gombra.

       ![ikon feltöltése](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/upload.png)
    
   * Ha __Azure Data Lake Storage__használ, kattintson a jobb gombbal egy üresre a fájl listázása területen, majd válassza a __feltöltés__lehetőséget. Végül válassza ki a **HiveCSharp. exe** fájlt, és kattintson a **Megnyitás**gombra.

     A __HiveCSharp. exe__ feltöltésének befejezése után ismételje meg a __PigUDF. exe__ fájl feltöltési folyamatát.

## <a name="run-an-apache-hive-query"></a>Apache Hive lekérdezés futtatása

1. A Visual Studióban nyissa meg a **Server Explorert**.

2. Bontsa ki az **Azure** elemet, majd bontsa ki a **HDInsight** elemet.

3. Kattintson a jobb gombbal arra a fürtre, amelyre a **HiveCSharp** alkalmazást telepítette, majd válassza a **struktúra-lekérdezés írása**lehetőséget.

4. Használja a következő szöveget a kaptár-lekérdezéshez:

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
    > A fürthöz `add file` használt alapértelmezett tároló típusának megfelelő utasítás megjegyzésének visszaírása.

    Ez a lekérdezés kiválasztja `clientid`a `devicemake`, a `devicemodel` és a `hivesampletable`mezőket a ból, majd átadja a mezőket a HiveCSharp. exe alkalmazásnak. A lekérdezés azt várja, hogy az alkalmazás három mezőt ad vissza, amelyek a `clientid`következő `phoneLabel`módon vannak `phoneHash`tárolva:, és. A lekérdezés a HiveCSharp. exe fájlt is megkeresi az alapértelmezett tároló gyökerében.

5. Kattintson a **Submit (Küldés** ) gombra a HDInsight-fürthöz való elküldéséhez. Megnyílik a **kaptár-feladatok összegző** ablaka.

6. Kattintson a **frissítés** gombra az összefoglalás frissítéséhez, amíg a **feladatok állapota** **Befejezve**állapotúra nem változik. A feladatok kimenetének megtekintéséhez kattintson a **feladatok kimenete**lehetőségre.

## <a name="run-an-apache-pig-job"></a>Apache Pig-feladatok futtatása

1. Az SSH használatával csatlakozzon a HDInsight-fürthöz. Például: `ssh sshuser@mycluster-ssh.azurehdinsight.net`. További információ: az [SSH-WithHDInsight használata](../hdinsight-hadoop-linux-use-ssh-unix.md)

2. A Pig parancssor elindításához használja az alábbi parancsot:

        pig

    > [!IMPORTANT]
    > Ha Windows-alapú fürtöt használ, Ehelyett használja a következő parancsokat:
    > ```
    > cd %PIG_HOME%
    > bin\pig
    > ```

    Egy `grunt>` üzenet jelenik meg.

3. A .NET-keretrendszer alkalmazást használó Pig-feladatok futtatásához adja meg a következőt:

        DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
        LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    Az `DEFINE` utasítás `CACHE` aliast hoz létre a pigudf. exe alkalmazásokhoz, és betölti azt az alapértelmezett tárolóból a fürt számára. `streamer` Később a `STREAM` kezelővel együtt feldolgozza a naplóban szereplő egyetlen sort, és az adatsorokat oszlopként jeleníti meg. `streamer`

    > [!NOTE]
    > A folyamatos átvitelhez használt alkalmazás nevének a (z) (kezdő) \` karakternek kell lennie, ha az alias, a (z) pedig a ( `SHIP`z) használatakor a (z).

4. Az utolsó sor megadása után a feladattípusnak indulnia kell. Az alábbi szöveghez hasonló kimenetet ad vissza:

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

## <a name="next-steps"></a>További lépések

Ebből a dokumentumból megtudhatta, hogyan használható a .NET-keretrendszer alkalmazása a kaptárból és a Pig on HDInsight. Ha szeretné megtudni, hogyan használhatja a Pythont a kaptárral és a Malactal, tekintse meg [a Python használata Apache Hive és az Apache Pig használatát a HDInsight-ben](python-udf-hdinsight.md)című témakört.

A Pig és a kaptár használatának egyéb módjaival, valamint a MapReduce használatával kapcsolatos további tudnivalókért tekintse meg a következő dokumentumokat:

* [Apache Hive használata a HDInsight](hdinsight-use-hive.md)
* [Az Apache Pig és a HDInsight használata](hdinsight-use-pig.md)
* [A MapReduce használata a HDInsight](hdinsight-use-mapreduce.md)
