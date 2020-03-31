---
title: Apache Storm topológiák a Visual Studio és a C# segítségével – Azure HDInsight
description: Ismerje meg, hogyan hozhat létre Storm topológiákat C#-ban. Hozzon létre egy szószám-topológiát a Visual Studio alkalmazásban a Visual Studio Hadoop eszközeivel.
ROBOTS: NOINDEX
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/31/2019
ms.openlocfilehash: 1903c2faab865152d1f3666f3c9dadd745058b56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75612291"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>C# topológiák fejlesztése apache storm hoz a Data Lake eszközök visual studio

Ismerje meg, hogyan hozhat létre C# Apache Storm topológiát az Azure Data Lake (Apache Hadoop) eszközök használatával a Visual Studio számára. Ez a dokumentum végigvezeti a Storm-projekt visual studióban történő létrehozásának folyamatát, helyi tesztelést, valamint az Apache Storm üzembe helyezését az Azure HDInsight-fürtön.

Azt is megtudhatja, hogyan hozhat létre hibrid topológiákat, amelyek C# és Java összetevőket használnak.

A C# topológiák a .NET 4.5,és a Monó használatával futnak a HDInsight-fürtön. A lehetséges inkompatibilitásokról a [Monó kompatibilitás](https://www.mono-project.com/docs/about-mono/compatibility/)című témakörben talál. C# topológia használatához frissítenie kell `Microsoft.SCP.Net.SDK` a projekt által használt NuGet csomagot a 0.10.0.6-os vagy újabb verzióra. A csomag verziójának a HDInsightban telepített Storm főverziójával is egyeznie kell.

| HDInsight verzió | Apache Storm verzió | SCP.NET verzió | Alapértelmezett monó verzió |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.4 | 0.10.0.x | 0.10.0.x | 3.2.8 |
| 3.5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

## <a name="prerequisite"></a>Előfeltétel

Apache Storm-fürt a HDInsighton. Lásd: [Apache Hadoop-fürtök létrehozása az Azure Portalon,](../hdinsight-hadoop-create-linux-clusters-portal.md) és válassza a **Storm** for **Cluster típus**lehetőséget.

## <a name="install-visual-studio"></a>A Visual Studio telepítése

A [Visual Studio](https://visualstudio.microsoft.com/downloads/)segítségével C# topológiákat fejleszthet SCP.NET. Az itt található utasítások a Visual Studio 2019-et használják, de a Visual Studio korábbi verzióit is használhatja.

## <a name="install-data-lake-tools-for-visual-studio"></a>A Data Lake eszközeinek telepítése a Visual Studio-hoz

A Data Lake-eszközök Visual Studio-hoz való telepítéséhez kövesse az Első lépések a [Visual Studio Data Lake eszközeinek használatába](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio)című témakört.

## <a name="install-java"></a>Java telepítése

Amikor Storm-topológiát küld a Visual Studio programból, SCP.NET létrehoz egy zip-fájlt, amely tartalmazza a topológiát és a függőségeket. A Java-t használják ezeknek a zip fájloknak a létrehozásához, mivel olyan formátumot használ, amely jobban kompatibilis a Linux-alapú fürtökkel.

1. Telepítse a Java Developer Kit (JDK) 7-es vagy újabb fejlesztői környezetét. Az Oracle JDK-t az [Oracle-től](https://openjdk.java.net/)szerezheti be. Más Java [disztribúciók](/java/azure/jdk/)is használhatók.

2. Állítsa `JAVA_HOME` a környezeti változót a Java-t tartalmazó könyvtárra.

3. Állítsa `PATH` be a környezeti `%JAVA_HOME%\bin` változót úgy, hogy tartalmazza a könyvtárat.

A következő C# konzolalkalmazást hozhat létre és futtathatja annak ellenőrzésére, hogy a Java és a JDK megfelelően van-e telepítve:

```csharp
using System;
using System.IO;
namespace ConsoleApplication2
{
   class Program
   {
       static void Main(string[] args)
       {
           string javaHome = Environment.GetEnvironmentVariable("JAVA_HOME");
           if (!string.IsNullOrEmpty(javaHome))
           {
               string jarExe = Path.Combine(javaHome + @"\bin", "jar.exe");
               if (File.Exists(jarExe))
               {
                   Console.WriteLine("JAVA Is Installed properly");
                    return;
               }
               else
               {
                   Console.WriteLine("A valid JAVA JDK is not found. Looks like JRE is installed instead of JDK.");
               }
           }
           else
           {
             Console.WriteLine("A valid JAVA JDK is not found. JAVA_HOME environment variable is not set.");
           }
       }  
   }
}
```

## <a name="apache-storm-templates"></a>Apache Storm sablonok

A Visual Studio Data Lake eszközei a következő sablonokat biztosítják:

| Projekt típusa | Útmutató ehhez: |
| --- | --- |
| Storm alkalmazás |Egy üres Storm topológia projekt. |
| Storm Azure SQL Writer minta |Írás az Azure SQL Database-be. |
| Storm Azure Cosmos DB olvasó minta |Olvasás az Azure Cosmos DB-ből. |
| Storm Azure Cosmos DB íróminta |Írás az Azure Cosmos DB-be írása. |
| Storm EventHub-olvasó minta |Olvasás az Azure Event Hubs-ból. |
| Storm EventHub írói minta |Írás az Azure Event Hubs szolgáltatásba. |
| Storm HBase olvasó minta |Hogyan lehet olvasni a HBase hdinsight-fürtökön. |
| Storm HBase író minta |Hogyan írjon a HBase hdinsight-fürtökön. |
| Vihar hibrid minta |Java-összetevő használata. |
| Vihar minta |Egy alapszó szám topológia. |

> [!WARNING]  
> Nem minden sablon működik linuxos HDInsight-alapú. Előfordulhat, hogy a sablonok által használt NuGet csomagok nem kompatibilisek a Monóval. A lehetséges problémák azonosításáról a [Monó kompatibilitás](https://www.mono-project.com/docs/about-mono/compatibility/) című témakörben található, és használja a [.NET hordozhatósági elemzőt.](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis)

Ebben a dokumentumban az alapvető Storm-alkalmazás projekttípust használja egy topológia létrehozásához.

### <a name="apache-hbase-templates"></a>Apache HBase sablonok

A HBase olvasó- és írósablonjai a HBase REST API-t, nem pedig a HBase Java API-t használják a HDInsight-fürt HBase-bázisával való kommunikációhoz.

### <a name="eventhub-templates"></a>EventHub-sablonok

> [!IMPORTANT]  
> Előfordulhat, hogy az EventHub Reader-sablonban található Java-alapú EventHub spout összetevő nem működik a Storm hdinsight 3.5-ös vagy újabb verziójában. Az összetevő frissített verziója elérhető a [GitHubon.](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib)

Egy példa topológiát, amely ezt az összetevőt használja, és együttműködik a Storm hdInsight 3.5-ön, lásd: [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>C# topológia létrehozása

C# topológiaprojekt létrehozása a Visual Studio-ban:

1. Nyissa meg a Visual Studiót.

1. A **Start** ablakban válassza **az Új projekt létrehozása**lehetőséget.

1. Az **Új projekt létrehozása** ablakban görgessen a **Storm Alkalmazás elemre,** és válassza a **Tovább**lehetőséget.

1. Az **Új projekt konfigurálása** ablakban adja meg a *WordCount* **projektnevét,** nyissa meg vagy hozza létre a projekt **helykönyvtárának** elérési útját, majd válassza a **Létrehozás lehetőséget.**

    ![Storm alkalmazás, Az új projekt párbeszédpanel konfigurálása, Visual Studio](./media/apache-storm-develop-csharp-visual-studio-topology/apache-storm-new-project.png)

A projekt létrehozása után a következő fájlokkal kell rendelkeznie:

* *Program.cs:* A projekt topológiadefiníciója. Alapértelmezés szerint egy kifolyóból és egy csavarból álló alapértelmezett topoológia jön létre.

* *Spout.cs*: Egy példa kifolyó, amely véletlenszerű számokat bocsát ki.

* *Bolt.cs*: Példacsavar, amely megtartja a kifolyó által kibocsátott számok számát.

A projekt létrehozásakor a NuGet letölti a legújabb [SCP.NET csomagot.](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/)

### <a name="implement-the-spout"></a>A kifolyó megvalósítása

Ezután adja hozzá a kódot a spout, amely adatok olvasására szolgál egy külső forrásból származó topológiában. Ez a kifolyó véletlenszerűen kibocsát egy mondatot a topológiába.

1. Nyissa *meg Spout.cs.* A kifolyó fő összetevői a következők:

   * `NextTuple`: A Storm hívta meg, ha a kifolyó új tuples-t bocsáthat ki.

   * `Ack`(csak tranzakciós topológia): Kezeli a spoutból küldött tuple-k topológiájának más összetevői által indított nyugtákat. A tuple nyugtázása lehetővé teszi, hogy a spout tudja, hogy az alsóbb rétegbeli összetevők sikeresen feldolgozták.

   * `Fail`(csak tranzakciós topológia): Kezeli azokat a tuple-okat, amelyek a topológia más összetevőit nem dolgozzák fel. A `Fail` módszer megvalósítása lehetővé teszi a törzs újbóli kiváltását, hogy újra feldolgozhassa.

2. Cserélje le az `Spout` osztály tartalmát a következő szövegre:

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

### <a name="implement-the-bolts"></a>A csavarok megvalósítása

Most hozzon létre két Storm csavart ebben a példában:

1. Törölje a meglévő *Bolt.cs* fájlt a projektből.

2. A **Megoldáskezelőben**kattintson a jobb gombbal a projektre, és válassza az Új elem **hozzáadása** > **parancsot.** A listában válassza a **Storm Bolt**lehetőséget, és írja be *Splitter.cs* nevet. Az új fájl kódjában módosítsa a névtér nevét `WordCount`. Ezután ismételje meg ezt a folyamatot, hogy hozzon létre egy második csavar nevű *Counter.cs*.

   * *Splitter.cs:* Olyan csavart valósít meg, amely a mondatokat különálló szavakra osztja, és új szófolyamot bocsát ki.

   * *Counter.cs:* Olyan csavart valósít meg, amely minden szót megszámol, és új szófolyamot és az egyes szavak számát bocsátja ki.

     > [!NOTE]  
     > Ezek a csavarok írni és olvasni az adatfolyamok, de egy csavar is kommunikálhat a források, például egy adatbázis vagy szolgáltatás.

3. Nyissa meg *Splitter.cs.* Alapértelmezés szerint csak egy `Execute`módszerrel rendelkezik: . A `Execute` metódus akkor jön létre, amikor a csavar feldolgozásra egy-egy tőzet kap. Itt elolvashatja és feldolgozhatja a bejövő tuples-t, és kimenő tuple-okat bocsáthat ki.

4. Cserélje le az `Splitter` osztály tartalmát a következő kódra:

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

5. Nyissa *meg a Counter.cs*, és cserélje le az osztály tartalmát a következő kódra:

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

A spoutok és a csavarok egy grafikonon vannak elrendezve, amely meghatározza, hogy az adatok hogyan áramlanak az összetevők között. Ehhez a topológia, a grafikon a következő:

![Kifolyó és csavar komponens elrendezése diagram, Storm topológia](./media/apache-storm-develop-csharp-visual-studio-topology/word-count-topology1.png)

A kifolyó olyan mondatokat bocsát ki, amelyek a Splitter-csavar példányai között vannak elosztva. A Splitter csavar a mondatokat szavakra bontja, amelyek a számlálócsavarra kerülnek.

Mivel a számláló példány helyileg tartalmazza a szavak számát, győződjön meg arról, hogy az adott szavak ugyanabba a Counter boltpéldányba áramlanak. Minden példány nyomon követi az egyes szavakat. Mivel a Splitter csavar nem tart fenn állam, ez tényleg nem számít, melyik példányban az osztó kap, amely mondatot.

Nyissa meg *a Program.cs.* A fontos `GetTopologyBuilder`módszer a , amely a Stormnak küldött topológia meghatározására szolgál. Cserélje le `GetTopologyBuilder` a tartalmát a következő kódra a korábban leírt topológia megvalósításához:

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder(
    "WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

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

## <a name="submit-the-topology"></a>A topológia beküldése

Most már készen áll a topológia elküldésére a HDInsight-fürtbe.

1. Keresse meg a**Kiszolgálókezelő** **megtekintése** > .

1. Kattintson **Azure**a jobb gombbal az Azure-ra, válassza **a Csatlakozás a Microsoft Azure-előfizetéshez lehetőséget...** és fejezze be a bejelentkezési folyamatot.

1. A **Megoldáskezelőben**kattintson a jobb gombbal a projektre, és válassza **a Küldés a viharba parancsot a HDInsight on**.

1. A **Topológia küldése** párbeszédpanel **Storm Cluster** legördülő listájában válassza ki a Storm on HDInsight-fürtöt, majd kattintson a **Küldés gombra.** A Kimenet ablaktábla megtekintésével ellenőrizheti, hogy a küldés sikeres **volt-e.**

    A topológia sikeres elküldése után meg kell jelennie a fürt **Storm Topologies View** ablakának. A futó topológiával kapcsolatos információk megtekintéséhez válassza ki a listából a **WordCount** topológiát.

    ![Storm topológia nézet ablak, HDInsight klaszter, Visual Studio](./media/apache-storm-develop-csharp-visual-studio-topology/storm-topology-view.png)

    > [!NOTE]  
    > A Storm **Topológiákat** a **Server Explorer**programból is megtekintheti. Bontsa ki **az Azure** > **HDInsight csomópontot,** kattintson a jobb gombbal egy Storm elemre a HDInsight-fürtön, és válassza **a Storm-topológiák megtekintése parancsot.**

    A topológia összetevőivel kapcsolatos információk megtekintéséhez jelöljön ki egy összetevőt a diagramon.

1. A **Topológia összegzése** csoportban válassza a **Leállítás** lehetőséget a topológia leállításához.

    > [!NOTE]  
    > A storm topológiák mindaddig futnak, amíg inaktiválják őket, vagy a fürtet nem törlik.

## <a name="transactional-topology"></a>Tranzakciós topológia

Az előző topoológia nem tranzakciós. A topológia összetevői nem valósítanak meg funkciókat az üzenetek visszajátszásához. Egy tranzakciós topológiára példa, hozzon létre egy projektet, és projekttípusként válassza a **Viharminta** lehetőséget.

A tranzakciós topológiák az adatok visszajátszásának támogatására a következőket valósítják meg:

* **Metaadatok gyorsítótárazása**: A spoutnak a kibocsátott adatok metaadatait kell tárolnia, hogy hiba esetén az adatok újra leolvashatók és kibocsáthatók legyenek. Mivel a minta által kibocsátott adatok kicsik, az egyes tuple-k nyers adatait egy szótár tárolja a visszajátszáshoz.

* **Ack**: A topológiában minden csavar hívhat, `this.ctx.Ack(tuple)` hogy nyugtázza, hogy sikeresen feldolgozta a tuple-t. Ha az összes csavar elismerte a tuple, a `Ack` módszer a spout meghívja. A `Ack` metódus lehetővé teszi, hogy a spout távolítsa el a visszajátszáshoz gyorsítótárazott adatokat.

* **Sikertelen**: Minden `this.ctx.Fail(tuple)` csavar hívhatja meg annak jelzését, hogy a feldolgozás nem sikerült egy tuple esetén. A hiba a `Fail` spout metódusára terjed, ahol a biztonsági oldal gyorsítótárazott metaadatok használatával visszajátszható.

* **Szekvenciaazonosító:** A rekordok kibocsátásakor egyedi szekvencia-azonosító adható meg. Ez az érték azonosítja a visszajátszás`Ack` `Fail`( és ) feldolgozás ának ttétét. A **Storm Sample** projektben például a következő metódushívást használja az adatok kivetítésekor:

  `this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);`

  Ez a kód olyan tuple-t bocsát ki, amely az alapértelmezett `lastSeqId`adatfolyamra vonatkozó mondatot tartalmaz, és a sorozatazonosító értékét a tartalmazza. Ebben a `lastSeqId` példában minden kibocsátott tuple-ra növekszik.

Amint azt a **Storm Minta** projekt, hogy egy összetevő tranzakciós lehet beállítani futásidőben, a konfiguráció alapján.

## <a name="hybrid-topology-with-c-and-java"></a>Hibrid topológia C# és Java

A Data Lake-eszközök kelésével hibrid topológiákat is létrehozhat, ahol egyes összetevők C# és mások Java.You can also use Data Lake tools for Visual Studio touse hybrid topologies, where some components are C# and others are Java.

Egy hibrid topológia példájával hozzon létre egy projektet, és válassza a **Storm Hybrid Sample lehetőséget.** Ez a mintatípus a következő fogalmakat mutatja be:

* **Java kifolyó** és **C# csavar:** Az `HybridTopology_javaSpout_csharpBolt` osztályban definiálva.

  A tranzakciós verzió az `HybridTopologyTx_javaSpout_csharpBolt` osztályban van definiálva.

* **C# spout** és **Java csavar:** Az `HybridTopology_csharpSpout_javaBolt` osztályban definiálva.

  A tranzakciós verzió az `HybridTopologyTx_csharpSpout_javaBolt` osztályban van definiálva.

  > [!NOTE]  
  > Ez a verzió azt is bemutatja, hogyan kell használni clojure kódot egy szöveges fájl, mint a Java összetevő.

A projekt elküldésekor használt topológia váltásához helyezze `[Active(true)]` át a kimutatást a használni kívánt topológiába, mielőtt elküldenék a fürtbe.

> [!NOTE]  
> A projekt részeként a *JavaDependency* mappában található összes szükséges Java-fájl meg van adva.

Hibrid topológia létrehozásakor és beküldésekor vegye figyelembe a következőket:

* Java-osztály példányának létrehozására használható `JavaComponentConstructor` kifolyóhoz vagy csavarhoz.

* Segítségével `microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer` szerializálhatja az adatokat java-összetevőkbe vagy java-objektumokból JSON-ba.

* Amikor elküldi a topológiát a kiszolgálónak, a **További konfigurációk** lehetőséget kell használnia a **Java fájl elérési útvonalának**megadásához. A megadott elérési út nak annak a könyvtárnak kell lennie, amely tartalmazza a Java-osztályokat tartalmazó JAR-fájlokat.

### <a name="azure-event-hubs"></a>Azure Event Hubs

SCP.NET 0.9.4.203-as verzió egy új osztályt és módszert vezet be, amely kifejezetten az Event Hub kifolyóval (az Event Hubs-ból beolvasásra szolgáló Java-kifolyó) való munkához szolgál. Ha olyan topológiát hoz létre, amely egy Event Hub-kifolyót használ (például a **Storm EventHub Reader mintasablonhasználatával),** használja a következő API-kat:

* `EventHubSpoutConfig`class: Létrehoz egy objektumot, amely tartalmazza a spout összetevő konfigurációját.

* `TopologyBuilder.SetEventHubSpout`metódus: Hozzáadja az Event Hub spout összetevőt a topológiához.

> [!NOTE]  
> Továbbra is a `CustomizedInteropJSONSerializer` spout által előállított adatok szerializálására kell használnia.

## <a name="use-configurationmanager"></a>A ConfigurationManager használata

Ne használja **a ConfigurationManager** segítségével a konfigurációs értékek et a bolt- és kifolyó-összetevőkből. Ha így tesz, null mutatókivételt okozhat. Ehelyett adja át a projekt konfigurációját a Storm topológiába kulcsként és értékpárként a topológia környezetben. Minden egyes összetevőnek, amely konfigurációs értékekre támaszkodik, le kell kérnie azokat a környezetből az inicializálás során.

A következő kód bemutatja, hogyan lehet beolvasni ezeket az értékeket:

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
            this.configuration = parms[Constants.USER_CONFIG] 
                as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

Ha az `Get` összetevő egy példányának visszaadására használ módszert, gondoskodnia `Dictionary<string, Object>` kell arról, hogy az a konstruktornak és a `Context` paramétereknek is átadja a paramétert. A következő példa `Get` egy olyan alapvető módszer, amely megfelelően továbbítja ezeket az értékeket:

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>A SCP.NET frissítése

A SCP.NET támogatási csomag frissítésének legújabb kiadásai a NuGet-en keresztül. Ha új frissítés érhető el, frissítési értesítést kap. A frissítés manuális ellenőrzéséhez kövesse az alábbi lépéseket:

1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **NuGet-csomagok kezelése** lehetőséget.

2. A csomagkezelőben válassza a **Frissítések lehetőséget.** Ha elérhető a SCP.NET támogatási csomag frissítése, az megjelenik a listában. Válassza a **Frissítés** lehetőséget a csomaghoz, majd a **Módosítások megtekintése** párbeszédpanelen a telepítéshez válassza az **OK** gombot.

> [!IMPORTANT]  
> Ha a projektet a SCP.NET egy korábbi verziójával hozták létre, amely nem használta a NuGet-et, az alábbi lépéseket kell végrehajtania az újabb verzióra való frissítéshez:
>
> 1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **NuGet-csomagok kezelése** lehetőséget.
> 2. A **Keresés** mező használatával keresse meg, `Microsoft.SCP.Net.SDK` majd adja hozzá a projekthez.

## <a name="troubleshoot-common-issues-with-topologies"></a>A topológiával kapcsolatos gyakori problémák elhárítása

### <a name="null-pointer-exceptions"></a>Null mutató kivételek

Ha Egy C# topológia egy Linux-alapú HDInsight-fürt, bolt és spout összetevők, amelyek a **ConfigurationManager** segítségével olvasni konfigurációs beállításokat futásidőben adhat null mutató kivételek.

A projekt konfigurációja a Storm topológiájába kerül kulcsként és értékpárként a topológia környezetben. Az összetevőknek az inicializáláskor átadott szótárobjektumból olvasható be.

További információt a dokumentum [ConfigurationManager használata](#use-configurationmanager) című szakaszában talál.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

Ha C# topológiát használ egy Linux-alapú HDInsight-fürttel, előfordulhat, hogy a következő hibával találkozik:

`System.TypeLoadException: Failure has occurred while loading a type.`

Ez a hiba akkor fordul elő, ha olyan bináris fájlt használ, amely nem kompatibilis a .NET mono által támogatott verziójával.

Linux alapú HDInsight-fürtök esetén győződjön meg arról, hogy a projekt a .NET 4.5-höz összeállított bináris fájlokat használja.

### <a name="test-a-topology-locally"></a>Topológia tesztelése helyileg

Bár a topológia telepítése egyszerűen telepíthető egy fürtre, bizonyos esetekben előfordulhat, hogy helyileg kell tesztelnie egy topológiát. Az alábbi lépésekkel futtassa és tesztelje a példa topológiát ebben a cikkben helyileg a fejlesztői környezetben.

> [!WARNING]  
> A helyi tesztelés csak alapszintű, Csak C#-topológiák esetén működik. A hibrid topológiák és több adatfolyamot használó topológiák nem használhatók helyi teszteléshez.

1. A **Megoldáskezelőben**kattintson a jobb gombbal a projektre, és válassza a **Tulajdonságok parancsot.** A projekt tulajdonságaiközött. Ezután módosítsa a **Kimenet típusát** **konzolalkalmazásra**.

   ![HDInsight Storm alkalmazás, projekttulajdonságok, Kimenet típusa](./media/apache-storm-develop-csharp-visual-studio-topology/hdi-output-type-window.png)

   > [!NOTE]
   > Ne felejtse el visszamódosítani a **Kimenet típust** **osztálytárra,** mielőtt a topológiát fürtre telepítene.

1. A **Megoldáskezelőben**kattintson a jobb gombbal a projektre, majd válassza az Új elem **hozzáadása** > **parancsot.** Válassza **az Osztály**lehetőséget, és írja be *LocalTest.cs* osztálynévként. Végül válassza a **Hozzáadás lehetőséget.**

1. Nyissa *meg LocalTest.cs*, `using` és adja hozzá a következő utasítást a lap tetejére:

    ```csharp
    using Microsoft.SCP;
    ```

1. Az `LocalTest` osztály tartalmaként a következő kódot használja:

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

    Szánjon egy kis időalatt, hogy olvassa el a kódot megjegyzéseket. Ez a `LocalContext` kód a fejlesztési környezetösszetevőinek futtatására használja. Megőrzi az összetevők közötti adatfolyamot a helyi meghajtón lévő szöveges fájlokhoz.

1. Nyissa *meg a Program.cs,* és `Main` adja hozzá a következő kódot a metódushoz:

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

1. Mentse a módosításokat, majd válassza az **F5** lehetőséget, vagy válassza a **Hibakeresés** > **indítása lehetőséget** a projekt elindításához. Meg kell jelennie egy konzolablaknak, és a tesztek előrehaladása kora alatt naplózva állapotát. Amikor `Tests finished` megjelenik, az ablak bezárásához jelöljön ki egy billentyűt.

1. A **Windows Intéző** segítségével keresse meg a projektet tartalmazó könyvtárat. (Például: *\\C: A felhasználók\\\<your_user_name>\\\\forrásadattárból\\\\WordCount WordCount*.) Ezután ebben a könyvtárban nyissa meg a *Bin*lehetőséget, majd válassza *a Debug*lehetőséget. A tesztek futtatásakor létrehozott szövegfájloknak meg kell jelennie: *sentences.txt*, *counter.txt*és *splitter.txt*. Nyissa meg az egyes szövegfájlokat, és vizsgálja meg az adatokat.

   > [!NOTE]  
   > A karakterlánc-adatok tizedesértékek tömbjeként maradnak meg ezekben a fájlokban. A `[[97,103,111]]` **splitter.txt** fájlban például a szó *ezelőtti*.

> [!NOTE]  
> A Project típusát állítsa vissza a **Projektkönyvtárba** a projekttulajdonságokban, mielőtt egy Storm-fürtre települne a HDInsight-fürtön. **Class Library**

### <a name="log-information"></a>Naplózási adatok

A segítségével `Context.Logger`egyszerűen naplózhat adatokat a topológia összetevőiből. A következő parancs például létrehoz egy információs naplóbejegyzést:

`Context.Logger.Info("Component started");`

A naplózott adatok a **Kiszolgálókezelőben**található **Hadoop-szolgáltatásnaplóból**tekinthetők meg. Bontsa ki a Storm a HDInsight-fürtön, majd a **Hadoop-szolgáltatásnapló**csomópontot. Végül jelölje ki a megtekinteni kívánt naplófájlt.

> [!NOTE]  
> A naplók a fürt által használt Azure storage-fiókban tárolódnak. A Naplók megtekintéséhez a Visual Studio, be kell jelentkeznie az Azure-előfizetés, amely a tárfiók tulajdonosa.

### <a name="view-error-information"></a>Hibainformációk megtekintése

A futó topológiában előforduló hibák megtekintéséhez kövesse az alábbi lépéseket:

1. A **Kiszolgálókezelőben**kattintson a jobb gombbal a Storm elemre a HDInsight-fürtön, és válassza **a Storm Topológiák megtekintése parancsot.**

   A **spout** és **a boltok**esetében az **Utolsó hiba** oszlop az utolsó hibára vonatkozó információkat tartalmazza.

2. Válassza ki a **kifolyó azonosítót** vagy **a boltazonosítót** a hibás összetevőhöz. A részletek lap további hibainformációkat jelenít meg a lap alján található **Hibák** szakaszban.

3. További információkért válasszon ki egy **portot** a **végrehajtók** szakasza a lap, a Storm munkavégző napló az elmúlt néhány percben.

### <a name="errors-submitting-topologies"></a>Topológiák elküldése hibák

Ha hibákat talál a TOPológia HDInsight ba küldésével kapcsolatban, megtalálhatja a topológia beküldését a HDInsight-fürtön kezelő kiszolgálóoldali összetevők naplóit. A naplók letöltéséhez használja a következő parancsot a parancssorból:

```cmd
scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .
```

Cserélje le *a ssh felhasználófiókot* a fürthöz. Cserélje le a *fürtnevet* a HDInsight-fürt nevére. A HDInsight `scp` használatával `ssh` és használatával kapcsolatos további tudnivalókért olvassa el [az SSH használata a HDInsight segítségével című témakört.](../hdinsight-hadoop-linux-use-ssh-unix.md)

A beküldések több okból is sikertelenek lehetnek:

* A JDK nincs telepítve, vagy nincs az elérési úton.
* A szükséges Java-függőségek nem szerepelnek a beküldésben.
* A függőségek nem kompatibilisek.
* A topológianevek duplikálódnak.

Ha a *hdinsight-scpwebapi.out* naplófájl tartalmaz egy `FileNotFoundException`, a kivételt a következő feltételek okozhatják:

* A JDK nincs az elérési út a fejlesztési környezetben. Ellenőrizze, hogy a JDK telepítve van-e a fejlesztői környezetben, és ez `%JAVA_HOME%/bin` az elérési út.
* Hiányzik egy Java-függőség. Győződjön meg arról, hogy a beküldés részeként tartalmazza a szükséges .jar fájlokat.

## <a name="next-steps"></a>További lépések

Az Event Hubs-ból származó adatok feldolgozásának például az [Azure Event Hubs-ból származó események feldolgozása a HDInsight-on stormtal című témakört.](apache-storm-develop-csharp-event-hub-topology.md)

Egy c# topológiára például, amely több adatfolyamra osztja az adatokat, lásd: [C# Storm példa.](https://github.com/Blackmist/csharp-storm-example)

A C# topológiák létrehozásával kapcsolatos további információkért lásd: [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

A HDInsight és a Storm hdinsight-mintákon való további kezeléséről az alábbi dokumentumokban nyújt unk be:

**Microsoft SCP.NET**

* [SCP programozási útmutató az Apache Stormhoz az Azure HDInsightban](apache-storm-scp-programming-guide.md)

**HDInsight-alapú Apache Storm**

* [Apache Storm-topológiák üzembe helyezése és kezelése az Azure HDInsightban](apache-storm-deploy-monitor-topology-linux.md)
* [Példa Apache Storm-topológiákra az Azure HDInsightban](apache-storm-example-topology.md)

**Apache Hadoop a HDInsighton**

* [Mi az Apache Hive és a HiveQL az Azure HDInsightban?](../hadoop/hdinsight-use-hive.md)
* [A MapReduce használata a HDInsight-alapú Apache Hadoopban](../hadoop/hdinsight-use-mapreduce.md)

**HDInsight-alapú Apache HBase**

* [Az Apache HBase használata az Azure HDInsightban](../hbase/apache-hbase-tutorial-get-started-linux.md)
