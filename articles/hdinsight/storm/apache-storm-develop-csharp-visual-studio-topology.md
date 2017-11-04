---
title: "A Visual Studio és a C# - Azure HDInsight alatt futó Apache Storm-topológiák |} Microsoft Docs"
description: "Ismerje meg a C# Storm-topológiák létrehozása. Hozzon létre egy egyszerű word-count topológiához a Visual Studio által a Hadoop tools for Visual Studio használatával."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 380d804f-a8c5-4b20-9762-593ec4da5a0d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/02/2017
ms.author: larryfr
ms.openlocfilehash: d972def582ff8fee74c2eae59f4756eb1dcd0a70
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>C#-topológiák fejlesztése az Apache Storm által a Data Lake tools for Visual Studio használatával

Megtudhatja, hogyan hozhat létre egy C# Storm-topológia az Azure Data Lake (Hadoop) tools for Visual Studio használatával. Ebből a dokumentumból a folyamatot, amely egy Storm-projekt létrehozása a Visual Studio helyi tesztelés és való az Apache Storm on Azure HDInsight-fürt üzembe helyezése.

Azt is megtudhatja, hogyan C# és Java összetevők használó hibrid topológiák létrehozásához.

> [!NOTE]
> A jelen dokumentumban leírt lépések a Visual Studio Windows fejlesztői környezetre támaszkodnak, amíg a lefordított projekt küldheti el a Linux vagy a Windows-alapú HDInsight-fürthöz. Csak a Linux-alapú fürtök létrehozása után 28 2016 októberétől kezdve, SCP.NET topológiákat támogatja.

Egy Linux-alapú fürttel C#-topológiák használatához frissítenie kell a Microsoft.SCP.Net.SDK NuGet-csomagot a projekt által használt 0.10.0.6 verzió vagy újabb. A csomag verziójának a HDInsightban telepített Storm főverziójával is egyeznie kell.

| HDInsight-verzió | A Storm verzióját | SCP.NET verzió | Alapértelmezett monó verzió |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.3 |0.10.x |0.10.x.x</br>(csak a Windows-alapú HDInsight) | NA |
| 3.4 | 0.10.0.x | 0.10.0.x | 3.2.8 |
| 3.5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

> [!IMPORTANT]
> A Linux-alapú fürtök C#-topológiáinak a .NET 4.5-öt kell használnia, és a Mono segítségével futhatnak a HDInsight-fürtön. Ellenőrizze [monó kompatibilitási](http://www.mono-project.com/docs/about-mono/compatibility/) az esetleges kompatibilitási problémák.

## <a name="install-visual-studio"></a>A Visual Studio telepítése

C#-topológiák SCP.NET a Visual Studio következő verzióinak egyikét használva fejleszthet:

* A Visual Studio 2012 [4. frissítés](http://www.microsoft.com/download/details.aspx?id=39305)

* A Visual Studio 2013-as verziójának [4. frissítés](http://www.microsoft.com/download/details.aspx?id=44921) vagy [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?LinkId=517284)

* Visual Studio 2015-öt vagy [Visual Studio 2015-Közösség](https://go.microsoft.com/fwlink/?LinkId=532606)

* A Visual Studio 2017 (minden kiadás)

## <a name="install-data-lake-tools-for-visual-studio"></a>Telepítse a Data Lake Visual Studio eszközök

Telepítheti a Data Lake tools for Visual Studio, kövesse a lépéseket a [Ismerkedés a Data Lake tools for Visual Studio használatával](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="install-java"></a>Java telepítése

A Storm-topológia a Visual Studio eszközből elküldésekor SCP.NET állít elő, a topológia és a függőségeit tartalmazó zip-fájl. Java a zip-fájlok létrehozására szolgál, mert több kompatibilis a Linux-alapú fürtökön formátuma nem használ.

1. A Java fejlesztői készlet (JDK) 7 vagy újabb a fejlesztési környezet telepítése. Az Oracle JDK a kaphat [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html). Is [más Java terjesztéseket](http://openjdk.java.net/).

2. A `JAVA_HOME` környezeti változó Java tartalmazó könyvtárat kell mutatnia.

3. A `PATH` környezeti változó tartalmaznia kell a `%JAVA_HOME%\bin` könyvtár.

A következő C# Konzolalkalmazás használatával ellenőrizze, hogy Java és a JDK megfelelően vannak-e telepítve:

```csharp
using System;
using System.IO;
namespace ConsoleApplication2
{
   class Program
   {
       static void Main(string[] args)
       {
           string javaHome = Environment.GetEnvironmentVariable(“JAVA_HOME”);
           if (!string.IsNullOrEmpty(javaHome))
           {
               string jarExe = Path.Combine(javaHome + @”\bin”, “jar.exe”);
               if (File.Exists(jarExe))
               {
                   Console.WriteLine(“JAVA Is Installed properly”);
                    return;
               }
               else
               {
                   Console.WriteLine(“A valid JAVA JDK is not found. Looks like JRE is installed instead of JDK.”);
               }
           }
           else
           {
             Console.WriteLine(“A valid JAVA JDK is not found. JAVA_HOME environment variable is not set.”);
           }
       }  
   }
}
```

## <a name="storm-templates"></a>A Storm-sablonok

A Data Lake tools for Visual Studio adja meg a következő sablonokat:

| Projekt típusa | Azt mutatja be |
| --- | --- |
| Storm-alkalmazás |Egy üres Storm-topológia projektet. |
| A Storm Azure SQL Writer minta |Megtudhatja, hogyan lehet írni az Azure SQL Database. |
| A Storm Azure Cosmos DB olvasó minta |How Azure Cosmos DB olvasni. |
| A Storm Azure Cosmos DB író minta |How Azure Cosmos DB írni. |
| A Storm EventHub olvasó minta |How Azure Event Hubs olvasni. |
| A Storm EventHub író minta |How Azure Event Hubs írni. |
| A Storm HBase olvasó minta |Hogyan lehet olvasni a HBase a HDInsight-fürtökön. |
| A Storm HBase író minta |Hogyan lehet írni a HBase a HDInsight-fürtökön. |
| A Storm hibrid minta |Hogyan használható egy Java-összetevő. |
| A Storm-minta |Alapszintű word-count topológiához. |

> [!WARNING]
> Nem minden sablonok a Linux-alapú hdinsight eszközzel fog működni. A sablonok által használt Nuget-csomagok nem lehet monó kompatibilis. Ellenőrizze a [monó kompatibilitási](http://www.mono-project.com/docs/about-mono/compatibility/) dokumentum, és használja a [.NET hordozhatóság Analyzer](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis) potenciális problémák azonosításához.

A jelen dokumentumban leírt lépések segítségével a alapszintű Storm-alkalmazás projekt típus olyan topológiák létrehozását is.

### <a name="hbase-templates-notes"></a>A HBase sablonok megjegyzések

A HBase írási és olvasási szerepkörökhöz sablonok a HBase REST API-t nem a HBase Java API használatával kommunikálnak a HBase a HDInsight-fürthöz.

### <a name="eventhub-templates-notes"></a>Az EventHub sablonok megjegyzések

> [!IMPORTANT]
> A Java-alapú EventHub spout-összetevő az EventHub-olvasó sablon nem feltétlenül Storm on HDInsight 3.5-ös vagy újabb verziója. Ez az összetevő frissített verziója érhető el, [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib).

Ezt példatopológia összetevő, és együttműködik Storm on HDInsight 3.5, lásd: [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>Hozzon létre egy C#-topológiák

1. Nyissa meg a Visual Studio, válassza ki **fájl** > **új**, majd válassza ki **projekt**.

2. Az a **új projekt** ablakában bontsa ki a **telepített** > **sablonok**, és válassza ki **Azure Data Lake**. Válassza ki a listáról a sablonok **Storm-alkalmazás**. Adja meg a képernyő alján **WordCount** az alkalmazás nevével.

    ![Képernyőfelvétel az új projekt ablakról](./media/apache-storm-develop-csharp-visual-studio-topology/new-project.png)

3. Miután létrehozta a projekt, rendelkeznie kell a következő fájlokat:

   * **Program.cs**: A fájl határozza meg a topológia a projekthez. Alapértelmezés szerint létrejön egy alapértelmezett topológia, amely egy spout és egy bolt áll.

   * **Spout.cs**: egy példa spout, amely véletlenszerű számok bocsát ki.

   * **Bolt.cs**: egy példa bolthoz, amely a spout által kibocsátott számok számát követi.

     A projekt létrehozásához NuGet letölti a legújabb [SCP.NET csomag](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/).

     [!INCLUDE [scp.net version important](../../../includes/hdinsight-storm-scpdotnet-version.md)]

### <a name="implement-the-spout"></a>A spout megvalósítása

1. Nyissa meg **Spout.cs**. Spoutokkal kapcsolatban használt külső forrásból topológiában adatokat olvasni. Egy spout a fő elemei a következők:

   * **NextTuple**: Storm által meghívva, amikor a spout engedélyezett hozható létre új rekordokat.

   * **Nyugtázási** (csak tranzakciós topológia): a nyugtázás a rekordokat a spout küldött topológiájának más összetevői által kezdeményezett kezeli. Egy rekord igazolása lehetővé teszi, hogy a spout tudja, hogy megtörtént a feldolgozása sikeresen alsóbb rétegbeli összetevők.

   * **Sikertelen** (csak tranzakciós topológia): rekordokat, amelyek vannak sikertelen – a feldolgozás a topológia más összetevők kezeli. A sikertelen metódusának lehetővé teszi, hogy újra dolgozhatók újra létrehozza a rekordban.

2. Cserélje le a tartalmát a **Spout** osztály a következő szöveggel. A spout véletlenszerűen azokat a topológia bocsát ki egy mondat helyett szerepel.

    ```csharp
    private Context ctx;
    private Random r = new Random();
    string[] sentences = new string[] {
        "the cow jumped over the moon",
        "an apple a day keeps the doctor away",
        "four score and seven years ago",
        "snow white and the seven dwarfs",
        "i am at two with nature"
    };

    public Spout(Context ctx)
    {
        // Set the instance context
        this.ctx = ctx;

        Context.Logger.Info("Generator constructor called");

        // Declare Output schema
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // The schema for the default output stream is
        // a tuple that contains a string field
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
    }

    // Get an instance of the spout
    public static Spout Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Spout(ctx);
    }

    public void NextTuple(Dictionary<string, Object> parms)
    {
        Context.Logger.Info("NextTuple enter");
        // The sentence to be emitted
        string sentence;

        // Get a random sentence
        sentence = sentences[r.Next(0, sentences.Length - 1)];
        Context.Logger.Info("Emit: {0}", sentence);
        // Emit it
        this.ctx.Emit(new Values(sentence));

        Context.Logger.Info("NextTuple exit");
    }

    public void Ack(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }
    ```

### <a name="implement-the-bolts"></a>A boltokhoz megvalósítása

1. Törölje a meglévő **Bolt.cs** fájlt a projektben.

2. A **Megoldáskezelőben**, kattintson jobb gombbal a projektre, és válassza ki **Hozzáadás** > **új elem**. Válassza ki a listáról, **Storm Bolt**, és írja be **Splitter.cs** neveként. Ismételje meg a folyamat hozzon létre egy második bolt nevű **Counter.cs**.

   * **Splitter.cs**: egy olyan bolthoz, amely felosztja a mondatok egyedi szót, és megfelelően kibocsát egy új adatfolyam szavak valósítja meg.

   * **Counter.cs**: valósít meg olyan bolthoz, amely megjeleníti minden szó, és megfelelően kibocsát egy új adatfolyam szavak és minden szó számát.

     > [!NOTE]
     > Ezek boltokhoz írási és olvasási adatfolyamokat, de kommunikálni az adatforrások, például egy adatbázis vagy a szolgáltatás egy olyan bolthoz is használhatja.

3. Nyissa meg **Splitter.cs**. Alapértelmezés szerint csak egyetlen módszer van: **Execute**. Az Execute metódus neve feldolgozásra rekordot tartalmaz a bolt fogadásakor. Itt el tudja olvasni és feldolgozni a bejövő rekordokat, és a kibocsátás kimenő rekordokat.

4. Cserélje le a tartalmát a **elválasztó** osztály az alábbi kódra:

    ```csharp
    private Context ctx;

    // Constructor
    public Splitter(Context ctx)
    {
        Context.Logger.Info("Splitter constructor called");
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // Input contains a tuple with a string field (the sentence)
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // Outbound contains a tuple with a string field (the word)
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance of the bolt
    public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Splitter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the sentence from the tuple
        string sentence = tuple.GetString(0);
        // Split at space characters
        foreach (string word in sentence.Split(' '))
        {
            Context.Logger.Info("Emit: {0}", word);
            //Emit each word
            this.ctx.Emit(new Values(word));
        }

        Context.Logger.Info("Execute exit");
    }
    ```

5. Nyissa meg **Counter.cs**, és cserélje ki a osztály tartalmát a következőre:

    ```csharp
    private Context ctx;

    // Dictionary for holding words and counts
    private Dictionary<string, int> counts = new Dictionary<string, int>();

    // Constructor
    public Counter(Context ctx)
    {
        Context.Logger.Info("Counter constructor called");
        // Set instance context
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string field - the word
        inputSchema.Add("default", new List<Type>() { typeof(string) });

        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string and integer field - the word and the word count
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance
    public static Counter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Counter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the word from the tuple
        string word = tuple.GetString(0);
        // Do we already have an entry for the word in the dictionary?
        // If no, create one with a count of 0
        int count = counts.ContainsKey(word) ? counts[word] : 0;
        // Increment the count
        count++;
        // Update the count in the dictionary
        counts[word] = count;

        Context.Logger.Info("Emit: {0}, count: {1}", word, count);
        // Emit the word and count information
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));
        Context.Logger.Info("Execute exit");
    }
    ```

### <a name="define-the-topology"></a>A topológia meghatározása

A grafikus, amely meghatározza, hogyan a közötti adatáramlás összetevők spoutokkal kapcsolatban és boltokhoz vannak rendezve. Ez a topológia a diagram a következőképpen történik:

![Hogyan vannak elrendezve összetevők ábrája](./media/apache-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

A mondatok a spout a kibocsátott, és továbbítja őket a elválasztó bolt példányai. Az elválasztó bolt bontja szavakat, amely továbbítja őket a számláló bolt a mondatok.

Szószámot helyileg használatban van a teljesítményszámláló-példány, mert azt szeretnénk, győződjön meg arról, hogy szavak flow ugyanazon teljesítményszámláló bolt-példányra végzi el. Minden példány nyomon követi a szavakat. Az elválasztó bolt nem tart fenn, mert tényleg nem számít, az elválasztó példányok kap mely mondat helyett szerepel.

Nyissa meg **Program.cs**. A fontos módszer **GetTopologyBuilder**, amely segítségével meghatározhatja, hogy a topológia elküldött Storm legyen. Cserélje le a tartalmát **GetTopologyBuilder** a fentiekben említett topológia végrehajtásához a következő kóddal:

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder("WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

// Add the spout to the topology.
// Name the component 'sentences'
// Name the field that is emitted as 'sentence'
topologyBuilder.SetSpout(
    "sentences",
    Spout.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
    },
    1);
// Add the splitter bolt to the topology.
// Name the component 'splitter'
// Name the field that is emitted 'word'
// Use suffleGrouping to distribute incoming tuples
//   from the 'sentences' spout across instances
//   of the splitter
topologyBuilder.SetBolt(
    "splitter",
    Splitter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
    },
    1).shuffleGrouping("sentences");

// Add the counter bolt to the topology.
// Name the component 'counter'
// Name the fields that are emitted 'word' and 'count'
// Use fieldsGrouping to ensure that tuples are routed
//   to counter instances based on the contents of field
//   position 0 (the word). This could also have been
//   List<string>(){"word"}.
//   This ensures that the word 'jumped', for example, will always
//   go to the same instance
topologyBuilder.SetBolt(
    "counter",
    Counter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
    },
    1).fieldsGrouping("splitter", new List<int>() { 0 });

// Add topology config
topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
{
    {"topology.kryo.register","[\"[B\"]"}
});

return topologyBuilder;
```

## <a name="submit-the-topology"></a>Küldje el a topológia

1. A **Megoldáskezelőben**, kattintson jobb gombbal a projektre, és válassza ki **Submit a HDInsight alatt futó Storm**.

   > [!NOTE]
   > Ha a rendszer kéri, adja meg a hitelesítő adatait az Azure-előfizetéshez. Ha egynél több előfizetéssel rendelkezik, jelentkezzen be, amely tartalmazza a Storm on HDInsight-fürt.

2. Válassza ki a Storm on HDInsight-fürt a a **Storm-fürt** legördülő listából válassza ki, és válassza **Submit**. Ha a küldése sikeres használatával figyelheti a **kimeneti** ablak.

3. Ha a topológia küldése sikeres volt, a **Storm-topológiák** megjelenjen-e a fürt számára. Válassza ki a **WordCount** topológia a futó topológiát vonatkozó információk megtekintése a listából.

   > [!NOTE]
   > Megtekintheti továbbá **Storm-topológiák** a **Server Explorer**. Bontsa ki a **Azure** > **HDInsight**, kattintson a jobb gombbal a Storm on HDInsight-fürt, majd válassza ki **nézet Storm-topológiák**.

    A topológia a összetevőivel kapcsolatos információk megtekintéséhez kattintson duplán a diagramban.

4. Az a **topológia összegzése** megtekintéséhez kattintson az **Kill** a topológia leállítása.

   > [!NOTE]
   > Storm-topológiák továbbra is fut, amíg azok inaktiválása, vagy a fürt törlésekor.

## <a name="transactional-topology"></a>Tranzakciós topológia

Az előző topológia nem tranzakciós. Az összetevők a topológia nem valósítja meg a funkciót a üzenetek visszajátszását. Például egy tranzakciós topológia, hozzon létre egy projektet, és válassza **Storm minta** a projekt típusként.

Tranzakciós topológiák valósítja meg a következő adatok ismétlés támogatásához:

* **Metaadatok gyorsítótárazása**: A spout kell tárolni a metaadatokat, így az adatok lekérése, és újra kibocsátott, ha hiba történik a kibocsátott adatokról. Mivel a minta által kibocsátott adatokról kisméretű, a nyers adatok a táblakonstruktor minden rekordjának a visszajátszás dictionary tárolja.

* **Nyugtázási**: minden bolt a topológia meghívhatja `this.ctx.Ack(tuple)` való megerősíti, hogy sikeresen feldolgozta-rekordot. Ha az összes boltokhoz korrektúrák a rekord a `Ack` a spout metódusában meghívták. A `Ack` módszer lehetővé teszi, hogy eltávolítja a visszajátszás gyorsítótárazott adatokat spout.

* **Sikertelen**: minden bolt meghívhatja `this.ctx.Fail(tuple)` annak jelzésére, hogy a feldolgozás sikertelen volt a rekordot. A hiba propagálás a a `Fail` módja a spout, ahol a rekordot is bejegyzéseit meg kell ismételni a gyorsítótárazott metaadatok.

* **A feladatütemezési azonosító**: egy rekord kibocsátó, amikor egy egyedi Sorozatazonosító adható meg. Ez az érték a rekord a visszajátszás (Ack és sikertelen) feldolgozás azonosítja. Például a spout a **Storm minta** kibocsátó adatainak használja a következő projekt:

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);

    Ez a kód megfelelően kibocsát egy rekord, amely tartalmazza a feladatütemezési azonosítóérték található alapértelmezett adatfolyam mondat **lastSeqId**. Ehhez a példához **lastSeqId** értéke eggyel növekszik, a kibocsátott minden rekordot.

Ahogyan az a **Storm minta** projekt, a tranzakciós-e egy összetevő lehet futásidőben, a konfiguráció alapján állítsa be.

## <a name="hybrid-topology-with-c-and-java"></a>Hibrid C# és Java topológia

Data Lake tools for Visual Studio segítségével hibrid topológiák, ha néhány összetevőt C# és Java.

Példa egy hibrid topológia, hozzon létre egy projektet, és jelölje ki **Storm hibrid minta**. Ez a minta-típus a következő fogalmakat mutatja be:

* **Java spout** és **C# bolt**: az **HybridTopology_javaSpout_csharpBolt**.

    * A tranzakciós verzió van megadva **HybridTopologyTx_javaSpout_csharpBolt**.

* **C# spout** és **Java bolt**: az **HybridTopology_csharpSpout_javaBolt**.

    * A tranzakciós verzió van megadva **HybridTopologyTx_csharpSpout_javaBolt**.

  > [!NOTE]
  > Ebben a verzióban is bemutatja, hogyan használják ki egy szövegfájlból Clojure kód egy Java-összetevő.


Váltás a topológia, amikor a projekthez használt, egyszerűen helyezze át a `[Active(true)]` nyilatkozatot, így a topológia szeretné használni, a fürt való továbbítás előtt.

> [!NOTE]
> A projekt részeként biztosított összes a Java-fájlokat, amelyek szükségesek a **JavaDependency** mappa.

Amikor létrehozása és elküldése a hibrid topológia, vegye figyelembe a következőket:

* Használjon **JavaComponentConstructor** egy spout a Java-osztály példányának létrehozása vagy boltok esetében.

* Használjon **microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer** adatok szerializálása a virtuális gépbe vagy onnan Pojo objektumait JSON-Java összetevőt.

* Ha a topológia a kiszolgálóra történő elküldésekor kell használnia a **további konfigurációs** beállítással adhatja meg a **Java elérési utat**. A megadott elérési utat kell lennie a Java-osztályok tartalmazó JAR-fájlokat tartalmazó könyvtárat.

### <a name="azure-event-hubs"></a>Azure Event Hubs

SCP.NET 0.9.4.203 tartalmazza egy új osztályt és metódus kimondottan az Event Hub spout (a Java spout az Event Hubs olvasó) használata. A topológia egy Event Hub spout használó létrehozásakor az alábbi módszerekkel:

* **EventHubSpoutConfig** osztály: hoz létre egy objektumot, amely tartalmazza a spout összetevő konfigurációját.

* **TopologyBuilder.SetEventHubSpout** módszer: az Event Hub spout összetevő felvétele a topológiát.

> [!NOTE]
> Továbbra is használnia kell a **CustomizedInteropJSONSerializer** szerializálni a spout által visszaadott adatokat.

## <a id="configurationmanager"></a>ConfigurationManager használata

Ne használjon **ConfigurationManager** konfigurációs értékek lekérése bolt és összetevők spout. Így okozhat, null mutató által-kivétel. Ehelyett a projekthez konfigurációs átad a a Storm-topológia a topológia környezetben kulcs-érték párban. Minden egyes összetevő, amely támaszkodik a konfigurációs értékeket kell lekérnie őket a környezetben inicializálása során.

A következő kód bemutatja, hogyan lehet lekérni a ezeket az értékeket:

```csharp
public class MyComponent : ISCPBolt
{
    // To hold configuration information loaded from context
    Configuration configuration;
    ...
    public MyComponent(Context ctx, Dictionary<string, Object> parms)
    {
        // Save a copy of the context for this component instance
        this.ctx = ctx;
        // If it exists, load the configuration for the component
        if(parms.ContainsKey(Constants.USER_CONFIG))
        {
            this.configuration = parms[Constants.USER_CONFIG] as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

Ha egy `Get` metódus egy példány a összetevő visszaadása meg kell győződnie arról, hogy mindkét átadja a `Context` és `Dictionary<string, Object>` a konstruktor paramétereit. A következő példa egy alapvető `Get` módszer, amelynek megfelelően továbbítja ezeket az értékeket:

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>SCP.NET frissítése

SCP.NET újabb kiadásaiban a Nugeten keresztül Csomagfrissítés támogatja. Ha egy új frissítés érhető el, egy frissítési értesítést kap. Frissítés manuálisan ellenőrzéséhez kövesse az alábbi lépéseket:

1. A **Megoldáskezelőben**, kattintson jobb gombbal a projektre, és válassza ki **NuGet-csomagok kezelése**.

2. Válassza ki a package manager **frissítések**. Frissítés érhető el, ha szerepel. Kattintson a **frissítés** a csomag telepítéséhez.

> [!IMPORTANT]
> Ha a projekt, amely nem NuGet SCP.NET korábbi verziójával lett létrehozva, újabb verzióra való frissítése a következő lépéseket kell elvégeznie:
>
> 1. A **Megoldáskezelőben**, kattintson jobb gombbal a projektre, és válassza ki **NuGet-csomagok kezelése**.
> 2. Használja a **keresési** mezőben, keresse meg, és adja hozzá, **Microsoft.SCP.Net.SDK** a projekthez.

## <a name="troubleshoot-common-issues-with-topologies"></a>Topológiák termékkel kapcsolatos gyakori hibák elhárítása

### <a name="null-pointer-exceptions"></a>NULL mutatót kivételek

C#-topológiák használatakor a Linux-alapú HDInsight-fürthöz boltok és összetevőket spout **ConfigurationManager** olvasni a konfigurációs beállításokat futásidőben adhat vissza null mutatót kivételek.

A konfiguráció a projekthez átad a Storm-topológia a topológia környezetében kulcs-érték párban. A rendszer inicializálásakor a összetevők átadott szótárobjektum lekérhetők.

További információkért lásd: a [ConfigurationManager](#configurationmanager) szakasz ebben a dokumentumban.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

Amikor egy Linux-alapú HDInsight-fürthöz C#-topológiák használ, a következő hiba merülhetnek fel:

    System.TypeLoadException: Failure has occurred while loading a type.

Ez akkor fordul elő, ha egy bináris, amely nem kompatibilis a monó támogató .NET verzióját használja.

Linux-alapú HDInsight-fürtök győződjön meg arról, hogy a projekt használ-e a bináris fájlok a .NET 4.5 számára.

### <a name="test-a-topology-locally"></a>A topológia helyi tesztelése

Egyszerűen a topológia telepíthető a fürtre, néhány esetben azonban szükség lehet a topológia helyi tesztelése. Az alábbi lépések segítségével futtathatja és tesztelheti a példa topológia ebben az oktatóanyagban helyileg a fejlesztői környezetben.

> [!WARNING]
> Helyi tesztelése csak akkor működik, a basic, a C#-topológiák csak. A hibrid topológiák vagy topológiák több adatfolyam használó helyi tesztelése nem használható.

1. A **Megoldáskezelőben**, kattintson jobb gombbal a projektre, és válassza ki **tulajdonságok**. A projekt tulajdonságait, módosítsa a **kimeneti típus** való **Konzolalkalmazás**.

    ![Képernyőfelvétel a projekt tulajdonságait, a kiemelt kimeneti típusú](./media/apache-storm-develop-csharp-visual-studio-topology/outputtype.png)

   > [!NOTE]
   > Ne felejtse el módosítani a **kimeneti típus** vissza **Class Library** a topológia egy fürt központi telepítése előtt.

2. A **Megoldáskezelőben**, kattintson jobb gombbal a projektre, majd válassza ki **Hozzáadás** > **új elem**. Válassza ki **osztály**, és írja be **LocalTest.cs** osztály neveként. Végezetül kattintson **Hozzáadás**.

3. Nyissa meg **LocalTest.cs**, és adja hozzá a következő **használatával** felső utasítást:

    ```csharp
    using Microsoft.SCP;
    ```

4. A következő kódot használja, a tartalmát a **LocalTest** osztály:

    ```csharp
    // Drives the topology components
    public void RunTestCase()
    {
        // An empty dictionary for use when creating components
        Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();

        #region Test the spout
        {
            Console.WriteLine("Starting spout");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext spoutCtx = LocalContext.Get();
            // Get a new instance of the spout, using the local context
            Spout sentences = Spout.Get(spoutCtx, emptyDictionary);

            // Emit 10 tuples
            for (int i = 0; i < 10; i++)
            {
                sentences.NextTuple(emptyDictionary);
            }
            // Use LocalContext to persist the data stream to file
            spoutCtx.WriteMsgQueueToFile("sentences.txt");
            Console.WriteLine("Spout finished");
        }
        #endregion

        #region Test the splitter bolt
        {
            Console.WriteLine("Starting splitter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext splitterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);

            // Set the data stream to the data created by the spout
            splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
            Console.WriteLine("Splitter bolt finished");
        }
        #endregion

        #region Test the counter bolt
        {
            Console.WriteLine("Starting counter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext counterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Counter counter = Counter.Get(counterCtx, emptyDictionary);

            // Set the data stream to the data created by splitter bolt
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            counterCtx.WriteMsgQueueToFile("counter.txt");
            Console.WriteLine("Counter bolt finished");
        }
        #endregion
    }
    ```

    A kód megjegyzéseket keresztül néhány percet igénybe vehet. Ezt a kódot használja **LocalContext** futtatni a a fejlesztési környezetet, és továbbra is fennáll, a szöveges fájlt a helyi meghajtón összetevők közötti adatfolyamban.

1. Nyissa meg **Program.cs**, és adja hozzá a következőt a **fő** módszert:

    ```csharp
    Console.WriteLine("Starting tests");
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
    // Initialize the runtime
    SCPRuntime.Initialize();

    //If we are not running under the local context, throw an error
    if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
    {
        throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
    }
    // Create test instance
    LocalTest tests = new LocalTest();
    // Run tests
    tests.RunTestCase();
    Console.WriteLine("Tests finished");
    Console.ReadKey();
    ```

2. Menti a módosításokat, és kattintson a **F5** , vagy válasszon **Debug** > **Start Debugging** a projekt indításához. A konzolablakban kell jelenik meg, és a tesztek állapotát állapot naplózása. Ha **tesztek befejeződött** jelenik meg, nyomja le bármelyik billentyűt az ablak bezárásához.

3. Használjon **Windows Explorer** kereséséhez a könyvtárban, amely tartalmazza a projekthez. Például: **C:\Users\<your_user_name > \Documents\Visual Studio 2013\Projects\WordCount\WordCount**. Ebben a könyvtárban, nyissa meg a **Bin**, és kattintson a **Debug**. A szövegfájlok, amelyeket a vizsgálatok futtatásakor kell megjelennie: sentences.txt counter.txt és splitter.txt. Nyissa meg minden egyes szöveges fájlt, és vizsgálja meg az adatokat.

   > [!NOTE]
   > Karakterlánc típusú adatok továbbra is fennáll, ezeket a fájlokat a decimális értékek ábrázolva. Például \[[97,103,111]] az az **splitter.txt** fájl szó *és*.

> [!NOTE]
> Meg kell adni a **projekttípus** vissza **Class Library** a Storm on HDInsight-fürt üzembe helyezése előtt.

### <a name="log-information"></a>Naplóadatok

Könnyen bejelentkezhet információkat a topológia összetevői használatával `Context.Logger`. Például a következő létrehoz egy tájékoztató naplóbejegyzés:

```csharp
Context.Logger.Info("Component started");
```

A naplózott információk is megtekinthetők a **Hadoop Szolgáltatásnaplót**, amely megtalálható **Server Explorer**. Bontsa ki a bejegyzést a Storm on HDInsight-fürt számára, és végül **Hadoop Szolgáltatásnaplót**. Végül válassza ki a naplófájl megtekintése.

> [!NOTE]
> A naplók az Azure storage-fiók, amely a fürt által használt vannak tárolva. A naplók megtekintéséhez a Visual Studio be kell jelentkeznie Azure-előfizetést, amely a tárfiók tulajdonosa számára.

### <a name="view-error-information"></a>Hiba történt adatainak megtekintése

A futó topológiát a bekövetkezett hibák megtekintéséhez használja a következő lépéseket:

1. A **Server Explorer**, kattintson a jobb gombbal a Storm on HDInsight-fürt, és válassza ki **nézet Storm-topológiák**.

2. Az a **Spout** és **boltok**, a **utolsó hiba** oszlop az utolsó hiba információkat tartalmaz.

3. Válassza ki a **Spout azonosító** vagy **Bolt azonosító** felsorolt hibás összetevő. Amely hiba jelenik meg, további részleteit megjelenítő oldalon információ szerepel a **hibák** szakasz az oldal alján.

4. További információk beszerzéséhez válassza ki egy **Port** a a **végrehajtója** lap, a munkavégző Storm napló megtekintéséhez az elmúlt néhány perc részében.

### <a name="errors-submitting-topologies"></a>Hibák elküldése a topológiák

Ha hibákba ütközik egy HDInsight-topológiát elküldése, naplók a kiszolgálóoldali összetevők, amelyek kezelik a HDInsight-fürt topológiája küldésének találja. Ezek a naplók lekéréséhez használja a következő parancsot a parancssorból:

    scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .

Cserélje le __sshuser__ a fürthöz SSH felhasználói fiókkal. Cserélje le __clustername__ a HDInsight-fürt nevét. További információk az `scp` és `ssh` a hdinsight eszközzel, lásd: [az SSH a Hdinsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

Több okból sikertelen lehet a jelentések:

* JDK nincs telepítve, vagy az elérési út nem tartalmazza.
* Java szükséges függőség nem szerepelnek a küldése.
* Nem kompatibilis függőségek.
* Ismétlődő topológia nevek.

Ha a `hdinsight-scpwebapi.out` a napló tartalmaz egy `FileNotFoundException`, ennek oka lehet az alábbi feltételek:

* A JDK nincs a fejlesztési környezet elérési útját. Győződjön meg arról, hogy a JDK telepítve van-e a fejlesztési környezetet, és hogy `%JAVA_HOME%/bin` az elérési út része.
* Java függősége hiányzik. Ellenőrizze, hogy a küldése részeként beleértve a minden szükséges .jar fájlt.

## <a name="next-steps"></a>Következő lépések

Például az adatok feldolgozása az Event Hubs, [feldolgozni az eseményeket az Azure Event Hubs a HDInsight alatt futó Storm](apache-storm-develop-csharp-event-hub-topology.md).

Például egy C#-topológiák, amely felosztja a több adatfolyam adatfolyam-adatokat, lásd: [C# Storm példa](https://github.com/Blackmist/csharp-storm-example).

C#-topológiák létrehozásával kapcsolatos további információk felderítésére, lásd: [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

További részleteket a HDInsight használata és minták a HDInsight alatt futó Storm további tekintse meg a következő dokumentumokat:

**Microsoft SCP.NET**

* [Szolgáltatáskapcsolódási pont programozási útmutató](apache-storm-scp-programming-guide.md)

**A HDInsight alatt futó Apache Storm**

* [Telepítheti és figyelheti a HDInsight alatt futó Apache Storm topológiák](apache-storm-deploy-monitor-topology.md)
* [HDInsight alatt futó Storm példatopológiái](apache-storm-example-topology.md)

**Apache Hadoop on HDInsight**

* [A Hive használata a hdinsight Hadoop](../hadoop/hdinsight-use-hive.md)
* [A Pig használata a HDInsight Hadoop](../hadoop/hdinsight-use-pig.md)
* [A HDInsight Hadoop MapReduce használata](../hadoop/hdinsight-use-mapreduce.md)

**Apache HBase a HDInsight-on**

* [Bevezetés a hbase a HDInsight eszközzel](../hbase/apache-hbase-tutorial-get-started-linux.md)
