---
title: A Visual Studio és C# – Azure HDInsight az Apache Storm-topológiák
description: Ismerje meg a C# Storm-topológiák létrehozása. Hozzon létre egy egyszerű szószámláló topológia a Visual Studio a Hadoop-eszközök a Visual Studio használatával.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: 3844bf101287774e1e4278cabf5a3e5b2c3dfa3c
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43045588"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Az Apache Storm C#-topológiák fejlesztése a Data Lake tools for Visual Studio használatával

Ismerje meg, hogyan hozhat létre egy C# Storm-topológia az Azure Data Lake (Hadoop) tools for Visual Studio használatával. Ez a dokumentum végigvezeti Önt a folyamat egy Storm-projekt létrehozása a Visual Studio, a helyszíni és a egy Apache Storm, az Azure HDInsight-fürtön való üzembe helyezése.

Azt is megtudhatja, hogyan C# és Java-összetevők használó hibrid topológiák létrehozását.

> [!NOTE]
> A jelen dokumentumban leírt lépések egy Windows fejlesztési környezetet a Visual Studióval támaszkodnak, amíg a lefordított projekt beküldhető egy Linux vagy Windows-alapú HDInsight-fürthöz. Csak a Linux-alapú fürtök 2016. október 28. után létrehozott SCP.NET topológiákat támogatja.

Egy Linux-alapú fürtök C#-topológiák használatához frissítenie kell a Microsoft.SCP.Net.SDK NuGet-csomagot a projekt által használt verzió 0.10.0.6 vagy újabb. A csomag verziójának a HDInsightban telepített Storm főverziójával is egyeznie kell.

| HDInsight-verzió | Storm-verzió | SCP.NET-verzió | Monó alapértelmezett verzió |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.3 |0.10.x |0.10.x.x</br>(csak a Windows-alapú HDInsight) | NA |
| 3.4 | 0.10.0.x | 0.10.0.x | 3.2.8 |
| 3.5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

> [!IMPORTANT]
> A Linux-alapú fürtök C#-topológiáinak a .NET 4.5-öt kell használnia, és a Mono segítségével futhatnak a HDInsight-fürtön. Ellenőrizze [monó kompatibilitási](http://www.mono-project.com/docs/about-mono/compatibility/) lehetséges inkompatibilitásokat.

## <a name="install-visual-studio"></a>A Visual Studio telepítése

C#-topológiák SCP.NET is fejleszthet a Visual Studio a következő verziók egyikével:

* A Visual Studio 2012 Update 4

* A Visual Studio 2013 Update 4 vagy [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?LinkId=517284)

* Visual Studio 2015 vagy [Visual Studio 2015 Community](https://go.microsoft.com/fwlink/?LinkId=532606)

* A Visual Studio 2017 (bármely kiadás esetén)

## <a name="install-data-lake-tools-for-visual-studio"></a>Install Data Lake tools for Visual Studio

A Data Lake tools for Visual Studio telepítése, kövesse a lépéseket a [Ismerkedés a Data Lake tools for Visual Studio használatával](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="install-java"></a>Telepítheti a javát

A Visual Studióból Storm-topológia elküldésekor SCP.NET állít elő, a topológia és a függőségeket tartalmazó zip-fájlt. Java ezek a zip-fájlok létrehozására szolgál, mert használja, amely több Linux-alapú fürtök kompatibilis formátumban.

1. Telepítse a Java fejlesztői készlet (JDK) 7 vagy annál újabb verzió a fejlesztési környezetet. Beszerezheti a az Oracle-JDK [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html). Is [más Java felosztások](http://openjdk.java.net/).

2. A `JAVA_HOME` környezeti változó Java tartalmazó könyvtárra kell mutatnia.

3. A `PATH` környezeti változó tartalmaznia kell a `%JAVA_HOME%\bin` könyvtár.

Az alábbi C# Konzolalkalmazás segítségével ellenőrizze, hogy a Java és a JDK megfelelően vannak-e telepítve:

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

A Data Lake tools for Visual Studio adja meg az alábbi sablonok:

| Projekt típusa | Azt ismerteti |
| --- | --- |
| Storm-alkalmazás |Egy üres Storm-topológia projektet. |
| A Storm az Azure SQL Writer minta |Hogyan írhat az Azure SQL Database. |
| A Storm az Azure Cosmos DB-olvasó minta |Hogyan olvashatja be az Azure Cosmos DB-ből. |
| A Storm az Azure Cosmos DB író minta |Hogyan írhat az Azure Cosmos DB-hez. |
| A Storm EventHub olvasó minta |Hogyan olvashatja be az Azure Event hubs Eseményközpontokból. |
| A Storm EventHub író minta |Hogyan írhat az Azure Event Hubsba. |
| A Storm HBase olvasó minta |Hogyan lehet olvasni a HBase a HDInsight-fürtökön. |
| A Storm HBase író minta |Hogyan lehet írni a HBase a HDInsight-fürtökön. |
| A Storm hibrid minta |Hogyan használható a Java-összetevő. |
| A Storm-minta |Egy alapszintű szószámlálási topológiában. |

> [!WARNING]
> Nem minden sablonok Linux-alapú HDInsight működnek. A sablonok által használt NuGet-csomagok nem lehet Mono kompatibilis. Ellenőrizze a [monó kompatibilitási](http://www.mono-project.com/docs/about-mono/compatibility/) dokumentum, és használja a [.NET hordozhatóságot Analyzer](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis) azonosíthatja a potenciális problémákat.

A jelen dokumentumban leírt lépések használatával az alapszintű Storm-alkalmazás projekt típusa olyan topológiák létrehozását.

### <a name="hbase-templates-notes"></a>A HBase sablonok megjegyzések

A HBase írási és olvasási szerepkörökhöz sablonok nem a HBase Java API-t, a HBase REST API használatával kommunikálni egy HBase a HDInsight-fürtön.

### <a name="eventhub-templates-notes"></a>Az EventHub-sablonok megjegyzések

> [!IMPORTANT]
> Az EventHub-olvasó sablon a Java-alapú EventHub spout összetevője nem működnek a Storm on HDInsight 3.5-ös vagy újabb verziója. Ez az összetevő egy frissített verziója érhető el: [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib).

Ez használó példatopológia összetevő, és együttműködik a Storm on HDInsight 3.5-ös verzióját, lásd: [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>C#-topológiák létrehozása

1. Nyissa meg a Visual Studióban, válassza ki **fájl** > **új**, majd válassza ki **projekt**.

2. Az a **új projekt** ablakában bontsa ki a **telepített** > **sablonok**, és válassza ki **az Azure Data Lake**. A sablonok listájából válassza **Storm-alkalmazás**. A képernyő alján adja meg a **WordCount** az alkalmazás neveként.

    ![Képernyőfelvétel az új projekt ablakról](./media/apache-storm-develop-csharp-visual-studio-topology/new-project.png)

3. Miután létrehozta a projektet, rendelkeznie kell a következő fájlokat:

   * **Program.cs**: Ez a fájl határozza meg a topológia a projekthez. Alapértelmezés szerint egy alapértelmezett topológia egy spout és a egy bolthoz jön létre.

   * **Spout.cs**: egy példa spout, amely véletlenszerű számok bocsát ki.

   * **Bolt.cs**: egy példa bolthoz, amely különválasztja a spout által kibocsátott számok számát.

     A projekt létrehozásakor NuGet letölti a legújabb [SCP.NET csomag](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/).

     [!INCLUDE [scp.net version important](../../../includes/hdinsight-storm-scpdotnet-version.md)]

### <a name="implement-the-spout"></a>A spout megvalósítása

1. Nyissa meg **Spout.cs**. Spoutok külső forrásból érkező adatolvasási topológiában szolgálnak. A spout fő összetevői a következők:

   * **NextTuple**: Storm meghívhatja, ha engedélyezett a spout gridre bocsáthatja ki az új rekordokat.

   * **Nyugtázási** (csak tranzakciós topológia): a nyugtázás a más összetevők a spout küldött rekordok topológiájának a kezdeményező kezeli. Egy rekord bosszankodnak lehetővé teszi, hogy tudja, hogy azt feldolgozása sikeres volt alsóbb rétegbeli összetevők a spout.

   * **Sikertelen** (csak tranzakciós topológia): rekordokat, amelyek vannak sikertelen – a feldolgozás a topológia más összetevők kezeli. Sikertelen metódus végrehajtási lehetővé teszi a rekord újbóli kibocsátható, úgy, hogy újból feldolgozva.

2. Cserélje le a tartalmát a **Spout** osztályt a következő szöveggel: A spout véletlenszerűen bocsát ki egy mondatnál a topológia be.

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

### <a name="implement-the-bolts"></a>A boltok megvalósítása

1. Törölje a meglévő **Bolt.cs** fájlt a projektből.

2. A **Megoldáskezelőben**, kattintson a jobb gombbal a projektre, és válassza ki **Hozzáadás** > **új elem**. Válassza ki a listából **Storm Bolt**, és adja meg **Splitter.cs** neveként. Ismételje meg a folyamat hozhat létre egy második bolt nevű **Counter.cs**.

   * **Splitter.cs**: valósít meg olyan bolthoz, amely felosztja a mondatok egyes szavak és bocsát ki egy új adatfolyamot szó.

   * **Counter.cs**: valósít meg egy bolthoz, amely minden szó számolja, és bocsát ki egy új adatfolyamot szavak és az egyes szavak számát.

     > [!NOTE]
     > Ezeket a boltok írási és olvasási adatfolyamok, de egy bolthoz források, például egy adatbázis vagy a szolgáltatás kommunikálni is használhatja.

3. Nyissa meg **Splitter.cs**. Alapértelmezés szerint csak egyik módja van: **Execute**. Az Execute metódus nevezzük, amikor a bolt kap egy rekord feldolgozás céljából. Itt olvashat és dolgozza fel a bejövő rekordokat és gridre bocsáthatja ki az kimenő rekordokat.

4. Cserélje le a tartalmát a **felosztó** osztályban az alábbi kódra:

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

5. Nyissa meg **Counter.cs**, és cserélje le az osztály tartalmát az alábbira:

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

Spoutok és boltok grafikon, amely meghatározza, hogy az összetevők közti adatfolyamok vannak elrendezve. Az ebben a topológiában a grafikon a következőképpen történik:

![Összetevők elrendezésére ábrája](./media/apache-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

Mondatok a spout kibocsátása megtörténik, és oszlanak meg a felosztó bolt példányai. Az elválasztó bolt bontja szavakat, a számláló bolt terjesztettek a mondatok.

A word-count helyileg tárolt a teljesítményszámláló-példány, mert győződjön meg arról, hogy meghatározott szavakat flow ugyanarra a számláló bolt-példányra szeretné. Minden példány nyomon követi az adott szavakat. Az elválasztó bolt nem tart fenn, mert nagyon nem számít, hogy az elválasztó-példányok kap, mely mondat helyett szerepel.

Nyissa meg **Program.cs**. A fontos módszer **GetTopologyBuilder**, amely a Storm, a topológia elküldött meghatározására szolgál. Cserélje le a tartalmát **GetTopologyBuilder** az előzőekben leírt topológia implementálása az alábbi kódra:

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

1. A **Megoldáskezelőben**, kattintson a jobb gombbal a projektre, és válassza ki **Küldés a HDInsight alatt futó Stormmal**.

   > [!NOTE]
   > Ha a rendszer kéri, adja meg a hitelesítő adatokat az Azure-előfizetésében. Ha egynél több előfizetéssel rendelkezik, jelentkezzen be a Storm on HDInsight-fürt tartalmazza.

2. Válassza ki a Storm, a HDInsight-fürtön a **Storm-fürt** legördülő listából válassza ki, és válassza ki **küldés**. Ha a küldés sikeres használatával figyelheti a **kimeneti** ablak.

3. A topológia sikeresen el lett küldve, amikor a **Storm-topológiák** meg kell jelennie a fürt számára. Válassza ki a **WordCount** topológia a listából a működő topológia kapcsolatos információk megtekintéséhez.

   > [!NOTE]
   > Megtekintheti továbbá **Storm-topológiák** a **Server Explorer**. Bontsa ki a **Azure** > **HDInsight**, kattintson a jobb gombbal a Storm on HDInsight-fürtöt, és válassza **Zobrazit Topologie Stormu**.

    A topológiában az összetevőivel kapcsolatos információk megtekintéséhez kattintson duplán az összetevő a diagramban.

4. Az a **topológia összegzése** megtekinteni, kattintson a **Kill** a topológia leállítása.

   > [!NOTE]
   > Storm-topológiák továbbra is futni, amíg azok az inaktiválása, vagy a fürt törlésekor.

## <a name="transactional-topology"></a>Tranzakciós topológia

Az előző a topológia akkor nem tranzakciós. Az összetevők a topológia üzenetek visszajátszása funkció nem hajt végre. Például egy tranzakciós topológia, hozzon létre egy projektet, és válassza ki **Storm minta** a projekt típusaként.

Tranzakciós topológiák megvalósítása visszajátszását az adatok támogatása a következőket:

* **Metaadatok gyorsítótárazása**: A spout bocsát ki, hogy az adatok lekérése, illetve bocsátja ki újra, ha hiba történik az adatok kapcsolatos metaadatokat kell tárolnia. Mivel az adatok a minta által kibocsátott kis méretű, a nyers adatok a rekordok egy szótárban a visszajátszás tárolódik.

* **Nyugtázási**: minden egyes bolt a topológia meghívhatja `this.ctx.Ack(tuple)` gombra annak megerősítéséhez, hogy sikeresen feldolgozta-e egy rekord. Ha az összes boltok nyugtázva a rekord a `Ack` a spout-metódusa meghívásainak. A `Ack` módszer lehetővé teszi, hogy a spout a visszajátszás gyorsítótárazott adatokat.

* **Sikertelen**: minden egyes bolt meghívhatja `this.ctx.Fail(tuple)` jelzi, hogy egy rekord feldolgozása nem sikerült. A hiba propagálás a a `Fail` a spout, ahol a rekord is megismétlését segítségével módszert gyorsítótárazott metaadatok.

* **Feladatütemezési azonosító**: kibocsátó egy rekord, amikor egy egyedi Sorozatazonosító adható meg. Ez az érték a visszajátszás (Ack és feladatátvétel) feldolgozási rekord azonosítja. Ha például a spout a **Storm minta** projekt használja a következő, amikor a kibocsátó adatok:

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);

    Ez a kód bocsát ki egy rekord, amely tartalmazza a feladatütemezési azonosítóérték található egy mondatot az alapértelmezett Stream **lastSeqId**. Ebben a példában **lastSeqId** értéke akkor nő, a kibocsátott minden rekord esetében.

Ahogyan az a **Storm minta** projektre, összetevő rendszer tranzakciós lehet futásidőben, a konfiguráció alapján állítsa be.

## <a name="hybrid-topology-with-c-and-java"></a>A C# és Java hibrid topológia

A Data Lake tools for Visual Studio használatával hozzon létre hibrid topológiák, ahol egyes összetevők C# és mások Java.

Példa egy hibrid topológia, hozzon létre egy projektet, és válassza ki **Storm hibrid minta**. Ez a minta-típus a következő fogalmak mutat be:

* **Java spout** és **C# bolt**: a megadott **HybridTopology_javaSpout_csharpBolt**.

    * Egy tranzakciós verzió meghatározott **HybridTopologyTx_javaSpout_csharpBolt**.

* **C# spout** és **Java bolt**: a megadott **HybridTopology_csharpSpout_javaBolt**.

    * Egy tranzakciós verzió meghatározott **HybridTopologyTx_csharpSpout_javaBolt**.

  > [!NOTE]
  > Ebben a verzióban is bemutatja, hogyan használható Clojure code szöveges fájlból egy Java-összetevő.


A topológia a projekt elküldésekor használt váltson át a `[Active(true)]` utasítással a topológia, mielőtt elküldené azokat a fürthöz használni kívánt.

> [!NOTE]
> Ez a projekt részeként biztosított összes a Java-fájlokat, amelyek szükségesek a **JavaDependency** mappát.

Létrehozása és elküldése egy hibrid topológia, vegye figyelembe a következőket:

* Használat **JavaComponentConstructor** hozzon létre egy példányt a Java-osztály számára egy adott spout vagy bolt.

* Használat **microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer** szerializálni az adatokat, vagy onnan máshová Java-összetevők Java-objektumok JSON-ná.

* A kiszolgáló a topológia elküldésekor kell használnia a **további konfigurációs beállítások** beállítással azt adhatja meg a **Java Fájlelérési utak**. A megadott elérési út a JAR-fájlok, amelyek tartalmazzák a Java-osztályokat tartalmazó könyvtárra kell lennie.

### <a name="azure-event-hubs"></a>Azure Event Hubs

SCP.NET verzió 0.9.4.203 tartalmaz, egy új osztályt, és kifejezetten az Event Hub spout (egy Java spout, amely az Event Hubs olvas) használata a metódus. Ha Ön olyan topológiák létrehozását, amely egy Event Hub spout használja, az alábbi módszerekkel:

* **EventHubSpoutConfig** osztály: létrehoz egy objektumot, amely tartalmazza a spout-összetevő konfigurációját.

* **TopologyBuilder.SetEventHubSpout** metódus: az Event Hub spout összetevőt ad hozzá a topológia.

> [!NOTE]
> Továbbra is kell használnia a **CustomizedInteropJSONSerializer** szerializálni a spout által előállított adatok.

## <a id="configurationmanager"></a>ConfigurationManager használata

Ne használjon **ConfigurationManager** bolt konfigurációs értékek lekérését és a spout-összetevők. Ezt okozhatja egy NULL értékű mutató kivétel. Ehelyett a konfiguráció a projekt átad a Storm-topológia a topológia környezetében kulcs-érték párban. Minden egyes összetevő, amely támaszkodik a konfigurációs értékeket kell kérheti le azokat a környezetből az inicializálás során.

A következő kód bemutatja, hogyan kérhető le ezeket az értékeket:

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

Használatakor egy `Get` metódust ad vissza egy példányát az összetevőt, győződjön meg arról, hogy mindkét továbbítja a `Context` és `Dictionary<string, Object>` paraméterek a konstruktor. Az alábbi példa az alapszintű `Get` módszernek megfelelően továbbítja ezeket az értékeket:

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>SCP.NET frissítése

SCP.NET legutóbbi kiadásai támogatják az Csomagfrissítés Nugeten keresztül. Új frissítés érhető el, ha egy frissítési értesítést kap. Frissítés manuálisan ellenőrizze, kövesse az alábbi lépéseket:

1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **NuGet-csomagok kezelése** lehetőséget.

2. Válassza ki a package manager **frissítések**. Frissítés érhető el, ha szerepel. Kattintson a **frissítés** a csomag telepítéséhez.

> [!IMPORTANT]
> Ha a projekt NuGet nem használó SCP.NET korábbi verziójával lett létrehozva, akkor végezze el egy újabb verzióra való frissítéséhez az alábbi lépéseket:
>
> 1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **NuGet-csomagok kezelése** lehetőséget.
> 2. Használatával a **keresési** mezőt, keressen, és adja hozzá, **Microsoft.SCP.Net.SDK** a projekthez.

## <a name="troubleshoot-common-issues-with-topologies"></a>Topológiák gyakori hibáinak elhárítása

### <a name="null-pointer-exceptions"></a>NULL értékű mutató kivételek

C#-topológiák egy Linux-alapú HDInsight-fürtöt használ, amikor bolt és a spout-összetevők által használt **ConfigurationManager** olvasásához a konfigurációs beállításokat futtatáskor adhat vissza null értékű mutató kivételek.

A konfiguráció a projekt átad a Storm-topológia a topológia környezetében kulcs-érték párban. Az átadott-összetevők megfigyelésével, amikor azok inicializálása szótárobjektum visszakereshetők.

További információkért lásd: a [ConfigurationManager](#configurationmanager) szakasz ebben a dokumentumban.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

C#-topológiák egy Linux-alapú HDInsight-fürtöt használ, amikor a következő hibával találkozhat:

    System.TypeLoadException: Failure has occurred while loading a type.

Ez a hiba akkor fordul elő, ha egy bináris, amely nem kompatibilis a .NET, amely támogatja a Mono verzióját használja.

Linux-alapú HDInsight-fürtök győződjön meg arról, hogy a projekt használja-e a bináris fájlokat, .NET 4.5-ös vagy későbbi verzióra.

### <a name="test-a-topology-locally"></a>A topológia a helyi tesztelése

Bár könnyen topológia üzembe helyezése egy fürtön, bizonyos esetekben az szükség lehet a topológia helyileg tesztelheti. A következő eljárással futtathatja és tesztelheti a példatopológiát ebben az oktatóanyagban helyileg a fejlesztési környezetben.

> [!WARNING]
> Helyi tesztelés csak akkor működik, a basic, a C#-topológiák csak. Hibrid topológiák vagy a topológiák több adatfolyam használó helyi tesztelése nem használható.

1. A **Megoldáskezelőben**, kattintson a jobb gombbal a projektre, és válassza ki **tulajdonságok**. A projekt tulajdonságai között módosíthatja a **kimeneti típus** való **Konzolalkalmazás**.

    ![Képernyőfelvétel a projekt tulajdonságai kiemelve kimeneti típus](./media/apache-storm-develop-csharp-visual-studio-topology/outputtype.png)

   > [!NOTE]
   > Ne felejtse el módosítani a **kimeneti típus** vissza **osztálytár** a topológia a fürt üzembe helyezése előtt.

2. A **Megoldáskezelőben**, és kattintson a jobb gombbal a projektre, majd válassza ki **Hozzáadás** > **új elem**. Válassza ki **osztály**, és adja meg **LocalTest.cs** osztály neveként. Végül kattintson **Hozzáadás**.

3. Nyissa meg **LocalTest.cs**, és adja hozzá a következő **használatával** utasítást a:

    ```csharp
    using Microsoft.SCP;
    ```

4. Használja a következő kódot a tartalmát a **LocalTest** osztály:

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

    A kód megjegyzéseket elolvasandó néhány percet vesz igénybe. Ez a kód **LocalContext** futtatását az összetevőket a fejlesztési környezet, és továbbra is fennáll, szöveges fájlok a helyi meghajtón lévő összetevők közötti.

1. Nyissa meg **Program.cs**, és adja hozzá a következőt a **fő** módszer:

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

2. Mentse a módosításokat, és kattintson a **F5** , vagy válasszon **Debug** > **Start Debugging** a projekt indításához. A konzolablakban kell jelenik meg, és állapot naplózásához a tesztek állapotát. Amikor **tesztek befejeződött** jelenik meg, nyomja le bármelyik billentyűt az ablak bezárásához.

3. Használat **Windows Explorer** keresse meg a projekt tartalmazó könyvtárra. Például: **C:\Users\<your_user_name > \Documents\Visual Studio 2013\Projects\WordCount\WordCount**. Ebben a könyvtárban, nyissa meg a **Bin**, és kattintson a **Debug**. A szöveges fájlokat, amelyeket a vizsgálatok futtatásakor kell megjelennie: sentences.txt counter.txt és splitter.txt. Nyissa meg a szöveges fájlokat, és vizsgálja meg az adatokat.

   > [!NOTE]
   > Ezeket a fájlokat a tizedes értékeket egy tömbként karakterláncadatokat továbbra is fennáll. Ha például \[[97,103,111]] az a **splitter.txt** fájl szó *és*.

> [!NOTE]
> Meg kell adni a **typ Projektu** vissza **osztálytár** egy Storm HDInsight-fürtön való üzembe helyezés előtt.

### <a name="log-information"></a>Naplóadatok

Könnyedén bejelentkezhet információkat a topológia összetevőktől eredő használatával `Context.Logger`. Ha például a következő parancs létrehoz egy tájékoztató naplóbejegyzés:

```csharp
Context.Logger.Info("Component started");
```

A naplózott információk is megtekinthetők a **Hadoop Szolgáltatásnaplót**, amely megtalálható **Server Explorer**. Bontsa ki a bejegyzést a a Storm on HDInsight-fürt, majd bontsa ki a **Hadoop Szolgáltatásnaplót**. Végül válassza ki a naplófájl megtekintéséhez.

> [!NOTE]
> A naplók a fürt által használt Azure storage-fiókban vannak tárolva. A naplók megtekintéséhez a Visual Studióban, jelentkezzen be az Azure-előfizetéshez, amely a tárfiók tulajdonosa.

### <a name="view-error-information"></a>Hiba történt adatainak megtekintése

A futó topológiák fellépő hibák megtekintéséhez használja az alábbi lépéseket:

1. A **Server Explorer**, kattintson a jobb gombbal a Storm on HDInsight-fürtöt, és válassza ki **nézet Storm-topológiák**.

2. Az a **Spout** és **boltok**, a **utolsó hiba** oszlop tartalmaz információt az utolsó hiba.

3. Válassza ki a **Spout azonosítója** vagy **Bolt azonosító** az a komponens, amely a felsorolt hibát tartalmaz. Hiba jelenik meg, további részleteit megjelenítő oldalon információ szerepel a **hibák** szakasz az oldal alján.

4. További információk beszerzéséhez válassza ki egy **Port** a a **végrehajtóval** szakasz az oldal, a Storm-feldolgozó napló megtekintéséhez az elmúlt néhány percig.

### <a name="errors-submitting-topologies"></a>Hibák elküldése a topológiák

A HDInsight-topológia elküldése hibákat észlel, ha a naplók a kiszolgálóoldali összetevők, amelyek kezelik a topológia beküldése a HDInsight-fürtön is megtalálhatja. Ezek a naplók lekéréséhez használja a következő parancsot a parancssorból:

    scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .

Cserélje le __sshuser__ a fürt SSH-felhasználói fiókkal. Cserélje le __clustername__ a HDInsight-fürt nevére. További tájékoztatást `scp` és `ssh` a HDInsight, lásd: [az SSH használata a HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

A jelentkezés több okból meghiúsulhat:

* JDK nincs telepítve vagy nem az elérési út.
* A küldés nem szerepelnek a szükséges Java-függőségek.
* Nem kompatibilis függőségek.
* Ismétlődő topológia nevét.

Ha a `hdinsight-scpwebapi.out` a napló tartalmaz egy `FileNotFoundException`, ennek oka lehet az alábbi feltételek:

* A JDK-kiszolgálón, a fejlesztési környezet nem szerepel. Győződjön meg arról, hogy a JDK telepítve van-e a fejlesztői környezetben, és hogy `%JAVA_HOME%/bin` elérési útvonalán van.
* Hiányzik egy Java-függőséget. Ellenőrizze, hogy a Küldés részeként beleértve a szükséges .jar fájlokat.

## <a name="next-steps"></a>További lépések

Az adatok feldolgozása az Event Hubsból, találhat példát [dolgozza fel az Azure Event hubs Eseményközpontokból a HDInsight alatt futó Stormmal](apache-storm-develop-csharp-event-hub-topology.md).

C#-topológiák, amely felosztja a streamadatokat több adatfolyam-példa: [C# Storm-példa](https://github.com/Blackmist/csharp-storm-example).

C#-topológiák létrehozásával kapcsolatos további információk felderítésére, lásd: [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

További részleteket a HDInsight használata és további Storm on HDInsight minták az alábbi dokumentumokban talál:

**Microsoft SCP.NET**

* [Szolgáltatáskapcsolódási pont programozási útmutató](apache-storm-scp-programming-guide.md)

**Az Apache Storm on HDInsight**

* [Telepítheti és figyelheti a HDInsight Apache Storm-topológiák](apache-storm-deploy-monitor-topology.md)
* [HDInsight alatt futó Storm példatopológiái](apache-storm-example-topology.md)

**Az Apache Hadoop on HDInsight**

* [A Hive használata a HDInsight Hadoop-keretrendszerrel](../hadoop/hdinsight-use-hive.md)
* [A Pig használata a HDInsight Hadoop-keretrendszerrel](../hadoop/hdinsight-use-pig.md)
* [A MapReduce használata a HDInsight Hadoop](../hadoop/hdinsight-use-mapreduce.md)

**Apache HBase on HDInsight**

* [A HDInsight-alapú HBase első lépései](../hbase/apache-hbase-tutorial-get-started-linux.md)
