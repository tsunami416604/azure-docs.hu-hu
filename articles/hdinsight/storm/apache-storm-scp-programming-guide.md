---
title: SCP.NET programozási útmutató a Storm hoz az Azure HDInsightban
description: További információ a SCP.NET létrehozásához. NET-alapú Storm-topológiák az Azure HDInsightban futó Storm-szal való használatra.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: ddf69a75a39911293277a4a4189cf4e79256e09d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77186863"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>SCP programozási útmutató az Apache Stormhoz az Azure HDInsightban

Az SCP egy olyan platform, amely valós idejű, megbízható, konzisztens és nagy teljesítményű adatfeldolgozó alkalmazásokat hoz fel. Az [Apache Storm](https://storm.incubator.apache.org/)ra épül, amely egy nyílt forráskódú szoftverközösségek által tervezett stream-feldolgozó rendszer. Nathan Marz hozta létre stormot. Nyílt forrásként tette közzé a Twitteren. Storm apache [zookeeper](https://zookeeper.apache.org/)-, amely egy másik Apache projekt, amely lehetővé teszi a rendkívül megbízható elosztott koordináció és az állapotkezelés.

Az SCP projekt nem csak a Stormot portolta a Windows rendszeren, hanem a projekt által hozzáadott bővítményeket és a Windows környezet testreszabását is. A bővítmények közé tartoznak a .NET fejlesztői élmény és a .NET-tárak. A testreszabás magában foglalja a Windows-alapú telepítést.

A bővítmények és a testreszabás, nem kell elágazás a nyílt forráskódú szoftverprojektek. A Storm ra épülő származtatott környezeteket is használhat.

## <a name="processing-model"></a>Feldolgozási modell

Az SCP-ben lévő adatok folyamatos tuple-patakokként vannak modellezve. Jellemzően, a tuples:

1. Befolyás egy várólistába.
1. Vannak felvette, és átalakítja az üzleti logika házigazdája belül Storm topológia.
1. Vagy a kimeneti piped egy másik SCP-rendszer, vagy elkötelezett a tárolók, mint az elosztott fájlrendszerek és adatbázisok, mint az SQL Server.

![Az adatok feldolgozásba való betáplálása során az adatokat tápláló várólista diagramja, amely viszont egy adattárba kerül](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

A Storm ban egy alkalmazástopológia határozza meg a számítási grafikont. A topológia minden csomópontja feldolgozási logikát tartalmaz. A csomópontok közötti kapcsolatok az adatfolyamot jelzik.

Azokat a csomópontokat, amelyek bemeneti adatokat fecskendeznek a topológiába, _spouts-nak nevezzük._ Az adatok sorrendjeként használhatja őket. A bemeneti adatok származhatnak olyan forrásból, mint a fájlnaplók, a tranzakciós adatbázis vagy a rendszer teljesítményszámlálója.

A bemeneti és kimeneti adatfolyamokkal egyaránt rendelkező csomópontokat _csavarnak nevezzük._ Ők végeznek a tényleges adatszűrést, a kijelöléseket és az összesítést.

Az SCP támogatja a legjobb erőfeszítéseket, legalább egyszer, és pontosan egyszer az adatfeldolgozást.

Egy elosztott adatfolyam-feldolgozási alkalmazásban hibák fordulhatnak elő az adatfeldolgozás során. Az ilyen hibák közé tartozik a hálózati kimaradás, a számítógép meghibásodása, vagy a kód hibája. Legalább egyszeri feldolgozás biztosítja az összes adat feldolgozását legalább egyszer azáltal, hogy automatikusan visszajátssza ugyanazokat az adatokat, ha hiba történik.

A legalább egyszeri feldolgozás egyszerű és megbízható, és számos alkalmazáshoz megfelel. De ha egy alkalmazás pontos számlálást igényel, legalább egyszeri feldolgozás nem elegendő, mert ugyanazokat az adatokat lehet visszajátszani az alkalmazás topológiájában. Ebben az esetben pontosan egyszer feldolgozás biztosítja, hogy az eredmény helyes akkor is, ha az adatok visszajátszott és feldolgozott többször.

Az SCP lehetővé teszi a .NET-fejlesztők számára, hogy valós idejű adatfeldolgozó alkalmazásokat hozzanak létre java virtuális gép (JVM) stormdal való használata közben. A JVM és a .NET a TCP helyi szoftvercsatornákon keresztül kommunikálnak. Minden kifolyó/csavar egy .NET/Java folyamatpár, ahol a felhasználói logika beépülő modulként fut a .NET folyamatban.

Ha az SCP-n felül szeretne adatfeldolgozási alkalmazást készíteni, kövesse az alábbi lépéseket:

1. Tervezzen és valósítson meg spoutokat, hogy adatokat szeretne beszedni a várólistákból.
1. Tervezzen és valósítson meg olyan csavarokat, amelyek feldolgozzák a bemeneti adatokat, és azokat külső tárolókba, például adatbázisba mentik.
1. Tervezze meg a topológiát, majd küldje el és futtassa.

A topológia határozza meg a csúcspontokat és a közöttük folyó adatokat. Az SCP egy topológia-specifikációt vesz fel, és egy Storm-fürtön telepíti, ahol minden csúcspont egy logikai csomóponton fut. A Storm feladatütemező gondoskodik a feladatátvétel és a skálázás.

Ez a cikk néhány egyszerű példát használ, hogy végigvezeti, hogyan hozhat létre adatfeldolgozó alkalmazások SCP.

## <a name="scp-plug-in-interface"></a>SCP beépülő modul felülete

Az SCP beépülő modulok önálló alkalmazások. A fejlesztés során futtathatók a Visual Studio-ban, és az éles környezet üzembe helyezése után csatlakoztathatók a Storm-folyamathoz.

Az SCP beépülő modul írása megegyezik bármely más Windows konzolalkalmazás írásával. A SCP.NET platform deklarál néhány felületet a kifolyóhoz/csavarhoz. A beépülő modul kódja megvalósítja ezeket az interfészeket. Ennek a kialakításnak a fő célja, hogy lehetővé tegye az üzleti logikára való összpontosítást, miközben hagyja, hogy a SCP.NET platform más dolgokat kezeljen.

A beépülő modul kódja az alábbi felületek egyikét valósítja meg. Melyik felület attól függ, hogy a topoológia tranzakciós vagy nem tranzakciós, és hogy az összetevő egy spout vagy egy csavar.

* **ISCPSpout**
* **ISCPBOLT**
* **ISCPTxSpout**
* **ISCPBatchBolt**

### <a name="iscpplugin"></a>ISCPPlugin

**ISCPPlugin** a közös felület sok plug-inek. Jelenleg, ez egy dummy felület.

```csharp
public interface ISCPPlugin
{
}
```

### <a name="iscpspout"></a>ISCPSpout

**Az ISCPSpout** egy nem tranzakciós kifolyó felülete.

```csharp
public interface ISCPSpout : ISCPPlugin
{
    void NextTuple(Dictionary<string, Object> parms);
    void Ack(long seqId, Dictionary<string, Object> parms); 
    void Fail(long seqId, Dictionary<string, Object> parms);
}
```

A **NextTuple** megnevezésekor a C# kód egy vagy több tuple-t bocsáthat ki. Ha nincs mit kibocsátanom, ennek a módszernek vissza kell térnie anélkül, hogy bármit is kibocsátana.

A **NextTuple**, **Ack**és **Fail** metódusok mindegyike szűk ciklusban van meghívva egy C# folyamat egyetlen szálában. Ha nincsenek kibocsátandó tuple-ok, a **NextTuple** alvó állapotban van egy rövid ideig, például 10 ezredmásodpercig. Ez az alvás segít elkerülni a PROCESSZOR rendelkezésre állásának elhasználását.

Az **Ack** és **fail** metódusok csak akkor vannak megnevezve, ha egy specifikációs fájl engedélyezi a nyugtázási mechanizmust. A *seqId* paraméter azonosítja a nyugtázott vagy sikertelen t. Ha a nyugtázás engedélyezve van egy nem tranzakciós topológiában, a következő **Emit** függvényt kell használni a kifolyóban:

```csharp
public abstract void Emit(string streamId, List<object> values, long seqId);
```

Ha egy nem tranzakciós topológia nem támogatja a nyugtázást, **az Ack** és **a Fail** üres függvényként maradhat.

A *parms* bemeneti paraméter ezekben a függvényekben egy üres szótárt határoz meg, és későbbi használatra van fenntartva.

### <a name="iscpbolt"></a>ISCPBOLT

**Az ISCPBolt** egy nem tranzakciós csavar illesztője.

```csharp
public interface ISCPBolt : ISCPPlugin
{
void Execute(SCPTuple tuple);
}
```

Ha egy új beállítás érhető el, a **Végrehajtás** függvény tág kódolni.

### <a name="iscptxspout"></a>ISCPTxSpout

**Az ISCPTxSpout** a tranzakciós kifolyó felülete.

```csharp
public interface ISCPTxSpout : ISCPPlugin
{
    void NextTx(out long seqId, Dictionary<string, Object> parms);  
    void Ack(long seqId, Dictionary<string, Object> parms);         
    void Fail(long seqId, Dictionary<string, Object> parms);        
}
```

Csakúgy, mint a nem tranzakciós társaik, **NextTx**, **Ack**, és **nem** minden hívják egy szűk hurok egyetlen szál a C # folyamat. Ha nincsenek kibocsátandó tuples, a **NextTx** rövid ideig, például 10 ezredmásodpercig alvó állapotban van. Ez az alvás segít elkerülni a PROCESSZOR rendelkezésre állásának elhasználását.

Amikor **a NextTx-et** új tranzakció indítására hívják, a *seqId* kimeneti paraméter azonosítja a tranzakciót. A tranzakció t az **Ack** és a **Fail**is használja. A **NextTx** metódus adatokat bocsáthat ki a Java oldalon. Az adatok a ZooKeeper tárolja, hogy támogassa a visszajátszás. Mivel a ZooKeeper korlátozott kapacitással rendelkezik, a kód csak metaadatokat bocsáthat ki, és nem tömeges adatokat egy tranzakciós kifolyóban.

Mivel a Storm automatikusan visszajátszik egy sikertelen tranzakciót, a **Fail** általában nem lesz meghívva. De ha az SCP ellenőrizni tudja a tranzakciós spout által kibocsátott metaadatokat, akkor **sikertelen** nek hívhatja meg, ha a metaadatok érvénytelenek.

A *parms* bemeneti paraméter ezekben a függvényekben egy üres szótárt határoz meg, és későbbi használatra van fenntartva.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt

**Az ISCPBatchBolt** a tranzakciós csavar oka.

```csharp
public interface ISCPBatchBolt : ISCPPlugin
{
    void Execute(SCPTuple tuple);
    void FinishBatch(Dictionary<string, Object> parms);  
}
```

Az **Execute** metódus akkor jön létre, amikor egy új tavas érkezik a csavarhoz. A Program a **Befejezésbatch** metódust akkor nevezi meg, amikor ez a tranzakció véget ér. A *parms* bemeneti paraméter későbbi használatra van fenntartva.

Tranzakciós topológia, **StormTxAttempt** fontos osztály. Két tagja van: **TxId** és **AttemptId**. A **TxId** tag egy adott tranzakciót azonosít. A tranzakció többször is megkísérelhető, ha sikertelen, és visszajátssza.

SCP.NET létrehoz egy új **ISCPBatchBolt** objektumot az egyes **StormTxAttempt** objektumok feldolgozásához, ugyanúgy, mint amit a Storm java-ban tesz. Ennek a tervnek a célja a párhuzamos tranzakciók feldolgozásának támogatása. A tranzakciós kísérlet befejezése után a megfelelő **ISCPBatchBolt** objektum megsemmisül, és a szemétgyűjtés történik.

## <a name="object-model"></a>Objektummodell

SCP.NET is biztosít egy egyszerű kulcs objektumok a fejlesztők számára, hogy a program. Az objektumok **a környezet**, a **StateStore**és az **SCPRuntime**. Ebben a részben tárgyaljuk őket.

### <a name="context"></a>Környezet

A **Környezeti** objektum futó környezetet biztosít egy alkalmazás számára. Az **ISCPSpout,** **ISCPBolt**, **ISCPTxSpout**vagy **ISCPBatchBolt** **minden ISCPPlugin** példánya rendelkezik egy megfelelő **környezeti** példánysal. A **Context** által nyújtott funkciók a következőkre oszthatók:

* A statikus rész, amely elérhető az egész C# folyamat
* A dinamikus rész, amely csak az adott **környezetpéldányhoz** érhető el

### <a name="static-part"></a>Statikus rész

```csharp
public static ILogger Logger = null;
public static SCPPluginType pluginType;
public static Config Config { get; set; }
public static TopologyContext TopologyContext { get; set; }  
```

A **Naplózó** objektum naplózási célokra szolgál.

Az **pluginType** objektum a C# folyamat beépülő modul típusát jelzi. Ha a folyamat java nélkül, helyi tesztmódban fut, a beépülő modul típusa **SCP_NET_LOCAL**.

```csharp
public enum SCPPluginType 
{
    SCP_NET_LOCAL = 0,
    SCP_NET_SPOUT = 1,
    SCP_NET_BOLT = 2,
    SCP_NET_TX_SPOUT = 3,
    SCP_NET_BATCH_BOLT = 4  
    }
```

A **Config** tulajdonság leveszi a konfigurációs paramétereket a Java oldalról, amely átmegy, ha egy C# beépülő modul inicializálása kor. A **Config** paraméterek két részre oszthatók: **stormConf** és **pluginConf**.

```csharp
public Dictionary<string, Object> stormConf { get; set; }  
public Dictionary<string, Object> pluginConf { get; set; }  
```

A **stormConf** rész a Storm által meghatározott paraméterek, az **pluginConf** rész pedig az SCP által meghatározott paraméterek. Például:

```csharp
public class Constants
{
    … …

    // constant string for pluginConf
    public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

    // constant string for stormConf
    public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";
    public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";
}
```

A **TopologyContext** típus leteszi a topológia környezetét. Ez a leghasznosabb több párhuzamos összetevő. Például:

```csharp
//demo how to get TopologyContext info
if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
{
    Context.Logger.Info("TopologyContext info:");
    TopologyContext topologyContext = Context.TopologyContext;
    Context.Logger.Info("taskId: {0}", topologyContext.GetThisTaskId());
    taskIndex = topologyContext.GetThisTaskIndex();
    Context.Logger.Info("taskIndex: {0}", taskIndex);
    string componentId = topologyContext.GetThisComponentId();
    Context.Logger.Info("componentId: {0}", componentId);
    List<int> componentTasks = topologyContext.GetComponentTasks(componentId);  
    Context.Logger.Info("taskNum: {0}", componentTasks.Count);
}
```

### <a name="dynamic-part"></a>Dinamikus rész

A következő felületek egy bizonyos **környezetpéldányhoz** tartoznak, amelyet a SCP.NET platform hoz létre, és amelyet a kódnak továbbítanak:

```csharp
// Declare the Output and Input Stream Schemas

public void DeclareComponentSchema(ComponentStreamSchema schema);

// Emit tuple to default stream.
public abstract void Emit(List<object> values);

// Emit tuple to the specific stream.
public abstract void Emit(string streamId, List<object> values);  
```

A nyugtázást támogató nem tranzakciós kifolyó esetében a következő módszer áll rendelkezésre:

```csharp
// for nontransactional spout that supports ack
public abstract void Emit(string streamId, List<object> values, long seqId);  
```

A nyugtázást támogató nem tranzakciós csavarnak explicit módon meg kell hívnia az **Ack** vagy **a Sikertelen** a kapott toldalékkal. Új rögzítés kiváltásakor a csavarnak meg kell adnia a rögzített horgonyait is. A következő módszerek állnak rendelkezésre:

```csharp
public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
public abstract void Ack(SCPTuple tuple);
public abstract void Fail(SCPTuple tuple);
```

### <a name="statestore"></a>StateStore (Állami tároló)

A **StateStore** objektum metaadat-szolgáltatásokat, monoton szekvencia-generálást és várakozásmentes koordinációt biztosít. Magasabb szintű elosztott egyidejűségi absztrakciókat hozhat létre a **StateStore-on.** Ezek az absztrakciók közé tartoznak az elosztott zárolások, az elosztott várólisták, a korlátok és a tranzakciós szolgáltatások.

Az SCP-alkalmazások az **Állapot** objektum segítségével szerializálhatják az információkat az [Apache ZooKeeper](https://zookeeper.apache.org/)alkalmazásban. Ez a képesség különösen értékes a tranzakciós topológia. Ha egy tranzakciós kifolyó nem válaszol, és újraindul, **az állapot** lekérheti a szükséges információkat a ZooKeeper-től, és újraindíthatja a folyamatot.

A **StateStore** objektum a következő alapvető módszerekkel rendelkezik:

```csharp
/// <summary>
/// Static method to retrieve a state store of the given path and connStr 
/// </summary>
/// <param name="storePath">StateStore path</param>
/// <param name="connStr">StateStore address</param>
/// <returns>Instance of StateStore</returns>
public static StateStore Get(string storePath, string connStr);

/// <summary>
/// Create a new state object in this state store instance
/// </summary>
/// <returns>State from StateStore</returns>
public State Create();

/// <summary>
/// Retrieve all states that were previously uncommitted, excluding all exited states
/// </summary>
/// <returns>Uncommitted states</returns>
public IEnumerable<State> GetUnCommitted();

/// <summary>
/// Get all the states in the StateStore
/// </summary>
/// <returns>All the states</returns>
public IEnumerable<State> States();

/// <summary>
/// Get state or registry object
/// </summary>
/// <param name="info">Registry name (registry only)</param>
/// <typeparam name="T">Type, registry or state</typeparam>
/// <returns>Return registry or state</returns>
public T Get<T>(string info = null);

/// <summary>
/// List all the committed states
/// </summary>
/// <returns>Registries containing the committed state </returns>
public IEnumerable<Registry> Committed();

/// <summary>
/// List all the exited states in the StateStore
/// </summary>
/// <returns>Registries containing the exited states</returns>
public IEnumerable<Registry> Aborted();

/// <summary>
/// Retrieve an existing state object from this state store instance 
/// </summary>
/// <returns>State from StateStore</returns>
/// <typeparam name="T">stateId, id of the State</typeparam>
public State GetState(long stateId)
```

Az **állapot** objektum a következő alapvető módszerekkel rendelkezik:

```csharp
/// <summary>
/// Set the status of the state object to commit
/// </summary>
public void Commit(bool simpleMode = true);

/// <summary>
/// Set the status of the state object to exit
/// </summary>
public void Abort();

/// <summary>
/// Put an attribute value under the given key
/// </summary>
/// <param name="key">Key</param>
/// <param name="attribute">State attribute</param>
    public void PutAttribute<T>(string key, T attribute);

/// <summary>
/// Get the attribute value associated with the given key
/// </summary>
/// <param name="key">Key</param>
/// <returns>State attribute</returns>
    public T GetAttribute<T>(string key);
```

Ha **a simpleMode** értéke **igaz,** a **Commit** metódus törli a megfelelő ZNode-ot a ZooKeeper alkalmazásban. Ellenkező esetben a metódus törli az aktuális ZNode-ot,\_és hozzáad egy új csomópontot a COMMITTED PATH-hoz.

### <a name="scpruntime"></a>SCPRuntime (SCPRuntime)

Az **SCPRuntime** osztály a következő két módszert biztosítja:

```csharp
public static void Initialize();

public static void LaunchPlugin(newSCPPlugin createDelegate);  
```

Az **Inicializálás** imázsa inicializálja az SCP futásidejű környezetet. Ebben a módszerben a C# folyamat csatlakozik a Java oldalon a konfigurációs paraméterek és a topológia környezetben.

A **LaunchPlugin** metódus elindítja az üzenetfeldolgozó hurkot. Ebben a ciklusban a C# beépülő modul üzeneteket fogad a Java oldalról. Ezek az üzenetek közé tartozik a tuples és a vezérlő jeleket. A beépülő modul ezután feldolgozza az üzeneteket, esetleg a kód által biztosított felületi módszer hívásával.

A **LaunchPlugin** bemeneti paramétere egy meghatalmazott. A metódus visszaadhat egy objektumot, amely az **ISCPSpout**, **ISCPBolt**, **ISCPTxSpout**vagy **ISCPBatchBolt** összeköttetést valósít meg.

```csharp
public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);
```

Az **ISCPBatchBolt**, lekaphat egy **StormTxAttempt** objektumot a *parms* paraméterből, és azt használhatja annak megítélésére, hogy a kísérlet visszajátszási kísérlet-e. A visszajátszási kísérlet ellenőrzése gyakran a véglegesítési csavarnál történik. A HelloWorldTx példa később ebben a cikkben bemutatja ezt az ellenőrzést.

Az SCP beépülő modulok általában két módban futhatnak: helyi teszt módban és normál módban.

#### <a name="local-test-mode"></a>Helyi tesztmód

Ebben a módban a C# kódban lévő SCP beépülő modulok a Visual Studio-ban futnak a fejlesztési fázisban. Ebben a módban használhatja az **ILocalContext** illesztőfelületet. A felület módszereket biztosít a kibocsátott tuples a helyi fájlokat, és olvassa vissza a RAM.The interface provides methods to serialize the mitted tuples to local files and read them back into RAM.

```csharp
public interface ILocalContext
{
    List<SCPTuple> RecvFromMsgQueue();
    void WriteMsgQueueToFile(string filepath, bool append = false);  
    void ReadFromFileToMsgQueue(string filepath);
}
```

#### <a name="regular-mode"></a>Normál mód

Ebben a módban a Storm Java folyamat futtatja az SCP beépülő modulokat. Íme egy példa:

```csharp
namespace Scp.App.HelloWorld
{
public class Generator : ISCPSpout
{
    … …
    public static Generator Get(Context ctx, Dictionary<string, Object> parms)
    {
    return new Generator(ctx);
    }
}

class HelloWorld
{
    static void Main(string[] args)
    {
    /* Setting the environment variable here can change the log file name */
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "HelloWorld");

    SCPRuntime.Initialize();
    SCPRuntime.LaunchPlugin(new newSCPPlugin(Generator.Get));
    }
}
}
```

## <a name="topology-specification-language"></a>Topológia specifikációnyelve

Az SCP Topology Specification egy tartományspecifikus nyelv (DSL) az SCP-topológiák leírásához és konfigurálásához. Ez alapján [Storm Clojure DSL](https://storm.incubator.apache.org/documentation/Clojure-DSL.html) és kiterjesztette SCP.

A topológia specifikációk közvetlenül a Storm-fürt a **runSpec** parancs segítségével küldheti el topológia specifikációk.

SCP.NET a következő függvényekkel adta meg a tranzakciós topológiákat:

| Új függvény | Paraméterek | Leírás |
| --- | --- | --- |
| **tx-topolopy** |*topológia-név*<br />*spout-térkép*<br />*bolt-térkép* |Tranzakciós topológiát határoz meg a topológia nevével, a definíciós leképezéssel és a csavarok definíciós leképezésével. |
| **scp-tx-kifolyó** |*exec-név*<br />*args*<br />*Mezők* |Tranzakciós kifolyót határoz meg. A függvény az *exec-name* által megadott alkalmazást futtatja, és *args-t*használ.<br /><br />A *fields* paraméter a spout kimeneti mezőit adja meg. |
| **scp-tx-batch-csavar** |*exec-név*<br />*args*<br />*Mezők* |Tranzakciós kötegcsavart határoz meg. A függvény az *exec-name* által megadott alkalmazást futtatja, és *argokat használ.*<br /><br />A *mezőparaméter* a csavar kimeneti mezőit adja meg. |
| **scp-tx-commit-csavar** |*exec-név*<br />*args*<br />*Mezők* |Tranzakciós véglegesítési zárat határoz meg. A függvény az *exec-name* által megadott alkalmazást futtatja, és *args-t*használ.<br /><br />A *mezőparaméter* a csavar kimeneti mezőit adja meg. |
| **nontx-topológia** |*topológia-név*<br />*spout-térkép*<br />*bolt-térkép* |Nem tranzakciós topológiát határoz meg a topológia nevével, a definíciós leképezéssel és a csavarok definíciós leképezésével. |
| **scp-kifolyó** |*exec-név*<br />*args*<br />*Mezők*<br />*Paraméterek* |Nem tranzakciós kifolyót határoz meg. A függvény az *exec-name* által megadott alkalmazást futtatja, és *args-t*használ.<br /><br />A *fields* paraméter a spout kimeneti mezőit adja meg.<br /><br />A *paraméterparaméter* nem kötelező. Ezzel megadhatja a "nontransactional.ack.enabled" paramétereket. |
| **scp-csavar** |*exec-név*<br />*args*<br />*Mezők*<br />*Paraméterek* |Nem tranzakciós csavart határoz meg. A függvény az *exec-name* által megadott alkalmazást futtatja, és *args-t*használ.<br /><br />A *mezők* paramétere a csavar kimeneti mezőit adja meg.<br /><br />A *paraméterparaméter* nem kötelező. Ezzel megadhatja a "nontransactional.ack.enabled" paramétereket. |

SCP.NET a következő kulcsszavakat határozza meg:

| Kulcsszó | Leírás |
| --- | --- |
| **:név** |A topológia neve |
| **:topológia** |Az előző táblázat funkcióit és a beépített függvényeket használó topológia |
| **:p** |A párhuzamosság tipp minden kifolyó vagy csavar |
| **:config** |A paraméterek konfigurálása vagy a meglévők frissítése |
| **:séma** |Az adatfolyam sémája |

SCP.NET a leggyakrabban használt paramétereket is meghatározza:

| Paraméter | Leírás |
| --- | --- |
| "plugin.name" |A C# beépülő modul .exe fájlneve |
| "plugin.args" |A beépülő modul argumentumai |
| "output.schema" |A kimeneti séma |
| "nontransactional.ack.enabled" |Azt jelzi, hogy engedélyezve van-e a nyugtázás nem tranzakciós topológiához |

A **runSpec** parancs a bitekkel együtt van telepítve. Itt van a parancs használat:

```csharp
.\bin\runSpec.cmd
usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target
```

Az *erőforrás-dir* paraméter megadása nem kötelező. Adja meg, ha c# alkalmazást szeretne csatlakoztatni. A megadott könyvtár tartalmazza az alkalmazást, a függőségeket és a konfigurációkat.

A *classpath* paraméter szintén nem kötelező. Megadja a Java-osztályelérési utat, ha a specifikációs fájl Java kifolyót vagy csavart tartalmaz.

## <a name="miscellaneous-features"></a>Egyéb jellemzők

### <a name="input-and-output-schema-declarations"></a>Bemeneti és kimeneti sémadeklarációk

A C# folyamatok tuple-okat bocsáthatnak ki. Ehhez a platform szerializálja a tuples **byte[]** objektumokat, és átviszi az objektumokat a Java oldalra. Storm ezután átviszi ezeket a tuples-t a célpontokhoz.

Az alsóbb rétegbeli összetevők c# folyamatok kap nak tuples vissza a Java oldalon, és konvertálni őket a platform eredeti típusai. Mindezek a műveletek rejtve vannak a platform.

A szerializálás és a deszerializálás támogatásához a kódnak deklarálnia kell a bemeneti és kimeneti sémát. A séma szótárként van definiálva. Az adatfolyam-azonosító a szótárkulcs. A kulcs értéke az oszlopok típusa. Egy összetevő több adatfolyamot deklarálhat.

```csharp
public class ComponentStreamSchema
{
    public Dictionary<string, List<Type>> InputStreamSchema { get; set; }
    public Dictionary<string, List<Type>> OutputStreamSchema { get; set; }
    public ComponentStreamSchema(Dictionary<string, List<Type>> input, Dictionary<string, List<Type>> output)
    {
        InputStreamSchema = input;
        OutputStreamSchema = output;
    }
}
```

A következő függvény hozzáadódik egy **környezeti** objektumhoz:

```csharp
public void DeclareComponentSchema(ComponentStreamSchema schema)
```

A fejlesztőknek gondoskodniuk kell arról, hogy a kibocsátott tuples engedelmeskedjen az adatfolyamhoz definiált sémának. Ellenkező esetben a rendszer futásidejű kivételt fog kivetni.

### <a name="multistream-support"></a>Többcsatornás támogatás

Az SCP lehetővé teszi, hogy a kód egyszerre több különböző adatfolyamot bocsát ki vagy fogadjon. A **Context** objektum ezt a támogatást tükrözi az **Emit** metódus nem kötelező adatfolyam-azonosító paramétereként.

A **SCP.NET context** objektumkét metódusa lett hozzáadva. Egy vagy több tuple-t bocsátanak ki bizonyos adatfolyamokra. A *streamId* paraméter egy karakterlánc. Az értéknek meg kell egyeznie mind a C# kódban, mind a topológia-definíciós specifikációban.

```csharp
/* Emit tuple to the specific stream. */
public abstract void Emit(string streamId, List<object> values);

/* for nontransactional spout only */
public abstract void Emit(string streamId, List<object> values, long seqId);
```

Nem létező adatfolyamba történő kivezetését futásidejű kivételek eredményezi.

### <a name="fields-grouping"></a>Mezők csoportosítása

A Storm beépített mezőcsoportosítása nem működik megfelelően SCP.NET. A Java proxy oldalon az összes mező adattípusa valójában **bájt[]**. A mezők csoportosítása a **bájt[]** objektum kivonatkódját használja a csoportosításhoz. A kivonatoló kód az objektum címe a RAM-ban. Így a csoportosítás hibás lesz az azonos tartalmú, de nem azonos nevű többbájtos objektumok esetében.

SCP.NET hozzáad egy testreszabott csoportosítási módszert, és a **bájt[]** objektum tartalmát használja a csoportosításhoz. Egy specifikációs fájlban a szintaxis a következő példához hasonlóan néz ki:

```csharp
(bolt-spec
    {
        "spout_test" (scp-field-group :non-tx [0,1])
    }
    …
)
```

Az előző specifikációs fájlban:

* `scp-field-group`itt adható meg, hogy a csoportosítás az SCP által végrehajtott testreszabott mezőcsoport.
* `:tx`vagy `:non-tx` megadja, hogy a topoológia tranzakciós-e. Erre az információra azért van szüksége, mert a kezdő index különbözik a tranzakciós és a nem tranzakciós topológiák között.
* `[0,1]`a nullával kezdődő mezőazonosítók kivonatkészletét adja meg.

### <a name="hybrid-topology"></a>Hibrid topológia

Native Storm kód van írva a Java. SCP.NET továbbfejlesztett Storm segítségével írni C # kódot kezelni az üzleti logika. De SCP.NET is támogatja a hibrid topológiák, amelyek nem csak a C # spouts/csavarok, hanem a Java spouts /csavarok.

### <a name="specify-java-spoutbolt-in-a-specification-file"></a>Java kifolyó/csavar megadása specifikációs fájlban

Az **scp-spout** és **az scp-bolt** segítségével java spoutokat és csavarokat adhat meg egy specifikációs fájlban. Például:

```csharp
(spout-spec 
  (microsoft.scp.example.HybridTopology.Generator.)
  :p 1)
```

Itt `microsoft.scp.example.HybridTopology.Generator` van a Neve a Java kifolyó osztály.

### <a name="specify-the-java-classpath-in-a-runspec-command"></a>Adja meg a Java-osztályelérési utat egy runSpec parancsban

Ha Java kifolyókat vagy csavarokat tartalmazó topológiát szeretne beküldeni, először fordítsa le őket jar fájlok létrehozásához. Ezután adja meg a JAVA osztályelérési utat, amely a TOPológia beküldésekor tartalmazza a JAR-fájlokat. Például:

```csharp
bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*
```

Itt `examples\HybridTopology\java\target\` található a Java spout/bolt JAR fájlt tartalmazó mappa.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Szerializálás és deszerializálás Java és C között #

Az SCP-összetevő tartalmazza a Java és a C# oldalt. A natív Java spoutokkal/csavarokkal való interakcióhoz szerializációnak és deszerializálásnak a Java és a C# oldal között kell történnie, amint azt az alábbi ábra is szemlélteti:

![Java-összetevő diagramja Az SCP-összetevőnek küldő, amely aztán egy másik Java-összetevőnek küldi el a](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

#### <a name="serialization-in-the-java-side-and-deserialization-in-the-c-side"></a>Szerializálás a Java oldalon és deszerializálás a C# oldalon

Először adja meg az alapértelmezett megvalósítása szerializálás a Java oldalon és deszerializálás a C# oldalon.

Adja meg a Java oldal szerializálási módszerét egy specifikációs fájlban.

```csharp
(scp-bolt
    {
        "plugin.name" "HybridTopology.exe"
        "plugin.args" ["displayer"]
        "output.schema" {}
        "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
    })
```

Adja meg a C# oldal deszerializálási módszerét a C# kódban.

```csharp
Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
inputSchema.Add("default", new List<Type>() { typeof(Person) });
this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());
```  

Ha az adattípus nem túl összetett, ez az alapértelmezett megvalósítás a legtöbb esetet kezeli. Az alábbiakban az alábbiakban saját implementációt csatlakoztathat:

* Az adattípus túl bonyolult az alapértelmezett megvalósításhoz.
* Az alapértelmezett implementáció teljesítménye nem felel meg az Ön igényeinek.

A Java oldalon található szerializálási felület a következőképpen határozható meg:

```csharp
public interface ICustomizedInteropJavaSerializer {
    public void prepare(String[] args);
    public List<ByteBuffer> serialize(List<Object> objectList);
}
```

A C# oldalon a deszerializációs felület a következőképpen határozható meg:

```csharp
public interface ICustomizedInteropCSharpDeserializer
{
    List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
}
```

#### <a name="serialization-in-the-c-side-and-deserialization-in-the-java-side"></a>Szerializálás a C# oldalon és deszerializálás a Java oldalon

Adja meg a C# oldal szerializálási módszerét a C# kódban.

```csharp
this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
```

Adja meg a Java oldal deszerializálási módszerét egy specifikációs fájlban.

```csharp
(scp-spout
   {
     "plugin.name" "HybridTopology.exe"
     "plugin.args" ["generator"]
     "output.schema" {"default" ["person"]}
     "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
   }
)
```

Itt `"microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer"` van a deszerializáló `"microsoft.scp.example.HybridTopology.Person"` neve, és az a célosztály, amelybe az adatok deszerializálódnak.

A C# szerializáló és a Java deszerializáló saját implementációját is csatlakoztathatja.

Ez a kód a C# szerializáló felülete:

```csharp
public interface ICustomizedInteropCSharpSerializer
{
    List<byte[]> Serialize(List<object> dataList);
}
```

Ez a kód a Java deszerializáló felülete:

```csharp
public interface ICustomizedInteropJavaDeserializer {
    public void prepare(String[] targetClassNames);
    public List<Object> Deserialize(List<ByteBuffer> dataList);
}
```

## <a name="scp-host-mode"></a>SCP állomás mód

Ebben a módban lefordíthatja a kódot DLL-ként, és az SCP.exe által biztosított SCP.exe használatával topológiát küldhet be. A specifikációs fájl így néz ki:

```csharp
(scp-spout
  {
    "plugin.name" "SCPHost.exe"
    "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
    "output.schema" {"default" ["sentence"]}
  })
```

Itt `"plugin.name"` az SCP `"SCPHost.exe"`SDK által biztosított , . Az SCPHost.exe három paramétert fogad el a következő sorrendben:

1. A DLL-név, `"HelloWorld.dll"` amely ebben a példában található.
1. Az osztály neve, `"Scp.App.HelloWorld.Generator"` amely ebben a példában található.
1. Az **ISCPPlugin**egy példányának lehívható nyilvános statikus metódusának neve.

Állomás módban fordítsa le a kódot DLL-ként az SCP platform általi meghíváshoz. Mivel a platform ezután teljes mértékben szabályozhatja a teljes feldolgozási logikát, javasoljuk, hogy scp-állomás módban küldje el a topológiát. Ezzel leegyszerűsíti a fejlesztési élményt. Emellett nagyobb rugalmasságot és jobb visszamenőleges kompatibilitást biztosít a későbbi kiadásokhoz.

## <a name="scp-programming-examples"></a>Példák SCP programozási

### <a name="helloworld"></a>HelloVilág

A következő egyszerű HelloWorld példa mutatja ízelítőt a SCP.NET. Ez használ egy nem tranzakciós topológia egy kifolyó nevű **generátor** és két csavarok nevű **splitter** és **számláló**. A **generátor** kifolyó véletlenszerűen generál mondatokat, és kibocsátja ezeket a mondatokat **splitter**. Az **osztócsavar** szavakra osztja a mondatokat, és ezeket a szavakat a **számlálócsavarra** bocsátja. A **számlálócsavar** szótársegítségével rögzíti az egyes szavak előfordulását.

Ebben a példában két specifikációs fájl\_található: HelloWorld.spec és HelloWorld EnableAck.spec. A C# kód megtudja, hogy a nyugtázás engedélyezve van-e, ha az objektumot a `pluginConf` Java oldalról kapja.

```csharp
/* demo how to get pluginConf info */
if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
{
    enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
}
Context.Logger.Info("enableAck: {0}", enableAck);
```

Ha a nyugtázás engedélyezve van a spout, a szótár gyorsítótárazza a tuples, hogy nem nyugtázott. Ha `Fail` meghívják, a sikertelen taszat a lejátszás visszajátssza.

```csharp
public void Fail(long seqId, Dictionary<string, Object> parms)
{
    Context.Logger.Info("Fail, seqId: {0}", seqId);
    if (cachedTuples.ContainsKey(seqId))
    {
        /* get the cached tuple */
        string sentence = cachedTuples[seqId];

        /* replay the failed tuple */
        Context.Logger.Info("Re-Emit: {0}, seqId: {1}", sentence, seqId);
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), seqId);
    }
    else
    {
        Context.Logger.Warn("Fail(), can't find cached tuple for seqId {0}!", seqId);
    }
}
```

### <a name="helloworldtx"></a>HelloWorldTx

A következő HelloWorldTx példa bemutatja, hogyan valósítható meg a tranzakciós topológia. A példában van egy **generátor**nevű spout , egy **részleges szám**nak nevezett kötegcsavar és egy **count-sum**nevű véglegesítési csavar. A példában három meglévő szövegfájl is található: DataSource0.txt, DataSource1.txt és DataSource2.txt.

Minden tranzakcióban a **generátor** kifolyó véletlenszerűen kiválaszt két fájlt a meglévő három fájlból, és a két fájlnevet a **részleges számlálócsavarba** bocsátja. A **részleges számláló** csavar:

1. Bekésel egy fájlnevet a fogadott t.
1. Megnyitja a megfelelő fájlt.
1. Megszámolja a fájlban lévő szavak számát.
1. A számszót a **szám-összegre** bocsátja.

A **count-sum** bolt összegzi a teljes darabszámot.

Ahhoz, hogy pontosan egyszer szemantika, a **count-sum** commit bolt kell megítélni, hogy ez egy visszajátszott tranzakció. Ebben a példában a következő statikus tagváltozóval rendelkezik:

```csharp
public static long lastCommittedTxId = -1; 
```

**ISCPBatchBolt** példány létrehozásakor az objektum értékét `txAttempt` a bemeneti paraméterekből kapja meg.

```csharp
public static CountSum Get(Context ctx, Dictionary<string, Object> parms)
{
    /* for transactional topology, we can get txAttempt from the input parms */
    if (parms.ContainsKey(Constants.STORM_TX_ATTEMPT))
    {
        StormTxAttempt txAttempt = (StormTxAttempt)parms[Constants.STORM_TX_ATTEMPT];
        return new CountSum(ctx, txAttempt);
    }
    else
    {
        throw new Exception("null txAttempt");
    }
}
```

Amikor `FinishBatch` a `lastCommittedTxId` neve, frissül, ha nem egy visszajátszott tranzakció.

```csharp
public void FinishBatch(Dictionary<string, Object> parms)
{
    /* judge whether it is a replayed transaction */
    bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

    if (!replay)
    {
        /* If it is not replayed, update the totalCount and lastCommittedTxId value */
        totalCount = totalCount + this.count;
        lastCommittedTxId = this.txAttempt.TxId;
    }
    … …
}
```

### <a name="hybridtopology"></a>Hibridtopológia

Ez a topológia java kifolyót és C# csavart tartalmaz. Az SCP platform által biztosított alapértelmezett szerializálási és deszerializálási implementációt használja. Tekintse meg a fájlt HybridTopology.spec a példák\\HybridTopology mappában a specifikáció s részletekért. A Java classpath megadásáról a SubmitTopology.bat című témakörben is szerepel.

### <a name="scphostdemo"></a>SCPHostdemo

Ez a példa lényegében ugyanaz, mint a HelloWorld. Az egyetlen különbség az, hogy a kód dll-ként van lefordítva, és a topológiát az SCPHost.exe használatával küldi el. Részletesebb magyarázatot az SCP állomásmód című szakaszban található.

## <a name="next-steps"></a>További lépések

Az SCP használatával létrehozott Apache Storm-topológiákra vonatkozó példákat az alábbi cikkekben talál:

* [C# topológiák fejlesztése apache stormhoz a HDInsighton a Visual Studio használatával](apache-storm-develop-csharp-visual-studio-topology.md)
* [Események feldolgozása az Azure Event Hubs-ból az Apache Storm segítségével a HDInsighton](apache-storm-develop-csharp-event-hub-topology.md)
* [Az Event Hubs járműérzékelőadatainak feldolgozása az Apache Storm használatával a HDInsighton](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Kibontás, átalakítás és betöltés (ETL) az Azure Event Hubs-ból az Apache HBase-be](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
