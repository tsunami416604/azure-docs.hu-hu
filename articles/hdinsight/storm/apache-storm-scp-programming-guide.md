---
title: SCP.NET programozási útmutató a Storm in Azure HDInsight
description: Útmutató a SCP.NET létrehozásához. NET-alapú Storm-topológiák az Azure HDInsight-ben futó Storm használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: f462fd88acf04fc8dced3db739a555c371c184ab
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154482"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>SCP programozási útmutató az Azure HDInsight Apache Storm

Az SCP egy olyan platform, amellyel valós idejű, megbízható, konzisztens és nagy teljesítményű adatfeldolgozási alkalmazások hozhatók létre. [Apache Stormra](https://storm.incubator.apache.org/) épül – az OSS-Közösségek által tervezett adatfolyam-feldolgozó rendszerre. A Storm a Nathan Marz tervezte, és a Twitter nyílt forráskódú. [Apache ZooKeeper](https://zookeeper.apache.org/), egy másik Apache-projektet használ, amely lehetővé teszi a megbízható elosztott koordinációt és az állami felügyeletet.

Nem csak a Windows rendszerű, de a Windows-ökoszisztémához hozzáadott bővítményeket és testreszabási projekteket is felvettük. A bővítmények közé tartozik a .NET fejlesztői felület és a kódtárak, a Testreszabás magában foglalja a Windows-alapú telepítést.

A bővítmény és a Testreszabás olyan módon történik, hogy nincs szükségünk az OSS-projektek elágazására, és a Storm-re épülő származtatott ökoszisztémákat is felhasználhatjuk.

## <a name="processing-model"></a>Feldolgozási modell

Az SCP-ben lévő adatmennyiség a rekordok folytonos adatfolyamként van modellezve. A rekordok jellemzően először egy várólistára áramlanak, majd felveszik és átalakították a Storm-topológiában üzemeltetett üzleti logikát, végül a kimenetet egy másik SCP-rendszerbe rekordok, vagy elkötelezettnek kell lennie az olyan áruházakban, mint az elosztott fájlrendszer vagy adatbázisok például SQL Server.

![Egy adattárat tároló üzenetsor-diagram, amely adattárakat táplál](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

A Storm-ben az alkalmazás topológiája a számítási diagramot határozza meg. A topológia minden csomópontja feldolgozási logikát tartalmaz, a csomópontok közötti kapcsolatok pedig az adatfolyamot jelölik. A bemeneti adatokat a topológiába beinjektáló csomópontokat _kiöntőnak_nevezzük, amely az adatok lefordítására használható. A bemeneti adatok a naplófájlokban, a tranzakciós adatbázisban, a rendszerteljesítmény-számlálóban és egyebekben találhatók. A bemeneti és kimeneti adatfolyamatokkal rendelkező csomópontok a tényleges adatszűrést és a kiválasztásokat és összesítéseket is tartalmazó _csavarok_.

Az SCP a legjobb erőfeszítéseket támogatja, legalább egyszer és pontosan egyszer, az adatfeldolgozás során. Egy elosztott adatfolyam-feldolgozó alkalmazásban különféle hibák merülhetnek fel az adatfeldolgozás során, például a hálózati leállás, a gép meghibásodása vagy a felhasználói hibakód miatt. A legalább egyszeri feldolgozási funkció biztosítja, hogy az összes adatfeldolgozás legalább egyszer megtörténjen, ha a hiba miatt automatikusan megtörténik az ismételt lejátszás. A legalább egyszeri feldolgozás egyszerű és megbízható, és számos alkalmazásra illeszkedik. Ha azonban egy alkalmazásnak pontos számlálásra van szüksége, akkor a legalább egyszeri feldolgozás nem elegendő, mert ugyanazokat az adatmennyiségeket lehet lejátszani az alkalmazás topológiájában. Ebben az esetben pontosan egyszer a feldolgozás úgy lett kialakítva, hogy az eredmény helyes legyen, még akkor is, ha az adatok többször is lejátszhatók és feldolgozhatók.

Az SCP lehetővé teszi a .NET-fejlesztők számára, hogy valós idejű adatfeldolgozási alkalmazásokat alakítsanak ki a Java virtuális gép (JVM) szolgáltatással való kihasználása közben. A .NET és a JVM TCP helyi szoftvercsatornán keresztül kommunikál. Alapvetően minden kiöntő/bolt egy .NET/Java-folyamat pár, ahol a felhasználói logika beépülő modulként fut a .NET-folyamatban.

Az SCP-re épülő adatfeldolgozási alkalmazás létrehozásához több lépésre van szükség:

* Tervezze meg és implementálja az adatok várólistából való lekéréséhez szükséges kiöntőt.
* Megtervezheti és implementálhatja a bemeneti adatok feldolgozására szolgáló csavarokat, és adatokat menthet külső tárolóba, például egy adatbázisba.
* Tervezze meg a topológiát, majd küldje el és futtassa a topológiát. A topológia meghatározza a csúcspontokat és a csúcspontok közötti adatforgalmat. Az SCP megtartja a topológia specifikációját, és telepíti azt egy Storm-fürtön, ahol minden csúcspont egy logikai csomóponton fut. A feladatátvételt és a skálázást a Storm Feladatütemező fogja intézni.

Ez a dokumentum néhány egyszerű példát mutat be, amely végigvezeti az adatfeldolgozási alkalmazások SZOLGÁLTATÁSKAPCSOLÓDÁSI ponttal való létrehozásán.

## <a name="scp-plugin-interface"></a>SZOLGÁLTATÁSKAPCSOLÓDÁSI pont beépülő modul felülete

Az SCP beépülő modulok (vagy alkalmazások) önálló EXEs, amelyek a Visual Studióban is futtathatók a fejlesztési fázisban, és az éles környezetbe való üzembe helyezésük után a Storm-folyamathoz is csatlakoztathatók. A SZOLGÁLTATÁSKAPCSOLÓDÁSI pont beépülő moduljának írása ugyanúgy történik, mint bármely más szabványos Windows-konzolos alkalmazás írásakor. A SCP.NET platform deklarál néhány felületet a kiöntő/bolt számára, és a felhasználói beépülő modul kódjának meg kell valósítania ezeket a csatolókat. Ennek a kialakításnak a fő célja, hogy a felhasználó saját üzleti logikára összpontosítson, és más dolgokat is el kell SCP.NET platformmal.

A felhasználói beépülőmodul-kódnak a következő felületek egyikét kell megvalósítani, attól függ, hogy a topológia tranzakciós vagy nem tranzakciós, és hogy az összetevő kiöntő vagy bolt.

* ISCPSpout
* ISCPBolt
* ISCPTxSpout
* ISCPBatchBolt

### <a name="iscpplugin"></a>ISCPPlugin

A ISCPPlugin a különböző beépülő modulok közös kezelőfelülete. Jelenleg ez egy dummy felület.

    public interface ISCPPlugin 
    {
    }

### <a name="iscpspout"></a>ISCPSpout

A ISCPSpout a nem tranzakciós kiöntő felülete.

     public interface ISCPSpout : ISCPPlugin                    
     {
         void NextTuple(Dictionary<string, Object> parms);         
         void Ack(long seqId, Dictionary<string, Object> parms);   
         void Fail(long seqId, Dictionary<string, Object> parms);  
     }

`NextTuple()` hívásakor a C# felhasználói kód egy vagy több rekordok tud kibocsátani. Ha nincs probléma a kibocsátással, ez a módszer bármilyen kibocsátása nélkül visszatérhet. Érdemes megjegyezni, hogy `NextTuple()`, `Ack()`és `Fail()` mindegyike szoros hurokban van meghívva egyetlen szálból a C# folyamatban. Ha nincs rekordok a kibocsátáshoz, akkor udvarias, hogy rövid idő alatt NextTuple alvó állapotba (például 10 ezredmásodpercre), hogy ne pazarolja a túl sok PROCESSZORt.

a `Ack()` és a `Fail()` csak akkor hívható, ha az ACK mechanizmus engedélyezve van a spec fájlban. A `seqId` a visszaigazolt vagy sikertelen rekord azonosítására szolgál. Így ha az ACK engedélyezve van a nem tranzakciós topológiában, a következő kiosztási függvényt kell használni a kifolyóban:

    public abstract void Emit(string streamId, List<object> values, long seqId); 

Ha az ACK nem támogatja a nem tranzakciós topológiát, akkor a `Ack()` és `Fail()` üres függvényként maradhat.

A függvények `parms` bemeneti paramétere egy üres szótár, amely későbbi használatra van fenntartva.

### <a name="iscpbolt"></a>ISCPBolt

A ISCPBolt a nem tranzakciós bolt felülete.

    public interface ISCPBolt : ISCPPlugin 
    {
    void Execute(SCPTuple tuple);           
    }

Ha új rekord érhető el, a rendszer a `Execute()` függvényt hívja meg.

### <a name="iscptxspout"></a>ISCPTxSpout

A ISCPTxSpout a tranzakciós kiöntő felülete.

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);  
        void Ack(long seqId, Dictionary<string, Object> parms);         
        void Fail(long seqId, Dictionary<string, Object> parms);        
    }

Ugyanúgy, mint a nem tranzakciós számláló, a `NextTx()`, a `Ack()`és a `Fail()` a C# folyamat egyetlen szálának szoros huroka. Ha nincsenek kibocsátható adatmennyiség, udvarias, hogy rövid idő alatt `NextTx` aludni (10 ezredmásodperc), így nem kell túl sok PROCESSZORt elpazarolni.

`NextTx()` új tranzakció elindítására van szükség, a kimenő paraméter `seqId` a tranzakció azonosítására szolgál, amely `Ack()` és `Fail()`is használatos. `NextTx()`a felhasználó az adatkibocsátást Java-oldalra is kibocsáthatja. A rendszer a ZooKeeper tárolja az adatvisszajátszás támogatásához. Mivel a ZooKeeper kapacitása korlátozott, a felhasználónak csak metaadatokat kell kibocsátania, nem tömeges adatokat a tranzakciós kiöntőben.

A Storm automatikusan visszafogja a tranzakciót, ha az sikertelen, így `Fail()` nem hívható meg normál esetben. Ha azonban az SCP képes megtekinteni a tranzakciós kiöntő által kibocsátott metaadatokat, akkor meghívhatja `Fail()`, ha a metaadatok érvénytelenek.

A függvények `parms` bemeneti paramétere egy üres szótár, amely későbbi használatra van fenntartva.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt

A ISCPBatchBolt a tranzakciós bolt felülete.

    public interface ISCPBatchBolt : ISCPPlugin           
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);  
    }

a `Execute()` akkor lesz meghívva, amikor új rekord érkezik a boltba. a `FinishBatch()` a tranzakció befejezése után hívja meg. A `parms` input paraméter későbbi használatra van fenntartva.

A tranzakciós topológia esetében van egy fontos fogalom – `StormTxAttempt`. Két mezőből áll: `TxId` és `AttemptId`. `TxId` egy adott tranzakció azonosítására szolgál, és egy adott tranzakció esetében több próbálkozás is lehet, ha a tranzakció meghiúsul, és a rendszer újra lejátssza. A SCP.NET egy új ISCPBatchBolt objektumot hoz létre az egyes `StormTxAttempt`feldolgozásához, akárcsak a Storm a javában. Ennek a kialakításnak a célja a párhuzamos tranzakciók feldolgozásának támogatása. A felhasználónak szem előtt kell tartania, hogy ha a tranzakciós kísérlet elkészült, a rendszer megsemmisíti a megfelelő ISCPBatchBolt objektumot, és begyűjti a szemetet.

## <a name="object-model"></a>Objektummodell

A SCP.NET egy egyszerű, a fejlesztők számára elérhető billentyűparancsokat is biztosít a programhoz. Ezek a **kontextus**, a **Állapottárolója**és a **SCPRuntime**. Ezeket a szakasz további részében tárgyaljuk.

### <a name="context"></a>Környezet

A környezet egy futó környezetet biztosít az alkalmazás számára. Minden ISCPPlugin-példánynak (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) megfelelő környezeti példánya van. A környezet által biztosított funkciók két részre oszthatók: (1) a statikus rész, amely a teljes C# folyamat során elérhető, (2) a dinamikus rész, amely csak az adott környezeti példány esetében érhető el.

### <a name="static-part"></a>Statikus rész

    public static ILogger Logger = null;
    public static SCPPluginType pluginType;                      
    public static Config Config { get; set; }                    
    public static TopologyContext TopologyContext { get; set; }  

a `Logger` naplózási célra van megadva.

`pluginType` a C# folyamat beépülő moduljának típusát jelöli. Ha a C# folyamat helyi tesztelési módban fut (Java nélkül), a beépülő modul típusa `SCP_NET_LOCAL`.

    public enum SCPPluginType 
    {
        SCP_NET_LOCAL = 0,       
        SCP_NET_SPOUT = 1,       
        SCP_NET_BOLT = 2,        
        SCP_NET_TX_SPOUT = 3,   
        SCP_NET_BATCH_BOLT = 4  
    }

a `Config` a Java-oldal konfigurációs paramétereinek beszerzésére szolgál. A paraméterek a Java-oldalról lesznek C# átadva, ha a beépülő modul inicializálva van. A `Config` paraméterek két részre vannak osztva: `stormConf` és `pluginConf`.

    public Dictionary<string, Object> stormConf { get; set; }  
    public Dictionary<string, Object> pluginConf { get; set; }  

`stormConf` a Storm és a `pluginConf` által meghatározott paraméterek a SCP által definiált paraméterek. Példa:

    public class Constants
    {
        … …

        // constant string for pluginConf
        public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

        // constant string for stormConf
        public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";           
        public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";                 
    }

`TopologyContext` a topológiai környezet beszerzéséhez van megadva, ez a leghasznosabb a több párhuzamosságtal rendelkező összetevőknél. Például:

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

### <a name="dynamic-part"></a>Dinamikus rész

A következő felületek egy adott környezeti példányhoz tartoznak. A környezeti példányt a SCP.NET platform hozza létre, és a következő felhasználói kódnak adta át:

    // Declare the Output and Input Stream Schemas

    public void DeclareComponentSchema(ComponentStreamSchema schema);   

    // Emit tuple to default stream.
    public abstract void Emit(List<object> values);                   

    // Emit tuple to the specific stream.
    public abstract void Emit(string streamId, List<object> values);  

A nem tranzakciós kiöntő támogató ACK-k esetében a következő módszert kell megadnia:

    // for non-transactional Spout which supports ack
    public abstract void Emit(string streamId, List<object> values, long seqId);  

Az ACK-t támogató nem tranzakciós bolt esetében explicit módon `Ack()` vagy `Fail()` a kapott rekordot. Az új rekord kibocsátásakor az új rekord horgonyait is meg kell adnia. A következő módszereket kell megadnia.

    public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values); 
    public abstract void Ack(SCPTuple tuple);
    public abstract void Fail(SCPTuple tuple);

### <a name="statestore"></a>Állapottárolója

a `StateStore` metaadat-szolgáltatásokat, monoton sorozatot generál, és várakozás nélküli koordinációt biztosít. A magasabb szintű elosztott egyidejűségi absztrakciók a `StateStore`ra épülnek, beleértve az elosztott zárolásokat, az elosztott várólistákat, a korlátozásokat és a tranzakciós szolgáltatásokat is.

Az SCP-alkalmazások a `State` objektum használatával tarthatnak fenn néhány információt [Apache ZooKeeperban](https://zookeeper.apache.org/), különösen a tranzakciós topológia esetében. Így ha a tranzakciós kiöntő összeomlik és újraindul, lekérheti a szükséges információkat a ZooKeeper, majd újraindítja a folyamatot.

A `StateStore` objektum főleg a következő módszerekkel rendelkezik:

    /// <summary>
    /// Static method to retrieve a state store of the given path and connStr 
    /// </summary>
    /// <param name="storePath">StateStore Path</param>
    /// <param name="connStr">StateStore Address</param>
    /// <returns>Instance of StateStore</returns>
    public static StateStore Get(string storePath, string connStr);

    /// <summary>
    /// Create a new state object in this state store instance
    /// </summary>
    /// <returns>State from StateStore</returns>
    public State Create();

    /// <summary>
    /// Retrieve all states that were previously uncommitted, excluding all aborted states 
    /// </summary>
    /// <returns>Uncommitted States</returns>
    public IEnumerable<State> GetUnCommitted();

    /// <summary>
    /// Get all the States in the StateStore
    /// </summary>
    /// <returns>All the States</returns>
    public IEnumerable<State> States();

    /// <summary>
    /// Get state or registry object
    /// </summary>
    /// <param name="info">Registry Name(Registry only)</param>
    /// <typeparam name="T">Type, Registry or State</typeparam>
    /// <returns>Return Registry or State</returns>
    public T Get<T>(string info = null);

    /// <summary>
    /// List all the committed states
    /// </summary>
    /// <returns>Registries contain the Committed State </returns> 
    public IEnumerable<Registry> Committed();

    /// <summary>
    /// List all the Aborted State in the StateStore
    /// </summary>
    /// <returns>Registries contain the Aborted State</returns>
    public IEnumerable<Registry> Aborted();

    /// <summary>
    /// Retrieve an existing state object from this state store instance 
    /// </summary>
    /// <returns>State from StateStore</returns>
    /// <typeparam name="T">stateId, id of the State</typeparam>
    public State GetState(long stateId)

A `State` objektum főleg a következő módszerekkel rendelkezik:

    /// <summary>
    /// Set the status of the state object to commit 
    /// </summary>
    public void Commit(bool simpleMode = true); 

    /// <summary>
    /// Set the status of the state object to abort 
    /// </summary>
    public void Abort();

    /// <summary>
    /// Put an attribute value under the give key 
    /// </summary>
    /// <param name="key">Key</param> 
    /// <param name="attribute">State Attribute</param> 
    public void PutAttribute<T>(string key, T attribute); 

    /// <summary>
    /// Get the attribute value associated with the given key 
    /// </summary>
    /// <param name="key">Key</param> 
    /// <returns>State Attribute</returns>               
    public T GetAttribute<T>(string key);                    

A `Commit()` metódus esetében, ha a simpleMode értéke TRUE (igaz), akkor a ZooKeeper-ben törli a megfelelő ZNode. Ellenkező esetben törli az aktuális ZNode, és új csomópontot ad hozzá a véglegesített\_ÚTVONALhoz.

### <a name="scpruntime"></a>SCPRuntime

A SCPRuntime a következő két módszert biztosítja:

    public static void Initialize();

    public static void LaunchPlugin(newSCPPlugin createDelegate);  

`Initialize()` az SCP futásidejű környezetének inicializálására szolgál. Ebben a metódusban a C# folyamat a Java-oldalhoz kapcsolódik, és beolvassa a konfigurációs paramétereket és a topológiai környezetet.

`LaunchPlugin()` az üzenet feldolgozási ciklusának kiindítására szolgál. Ebben a hurokban a C# beépülő modul a Java-oldal (beleértve a rekordok és a vezérlési jeleket is) formájában fogad üzeneteket, majd feldolgozza az üzeneteket, például a felhasználói kód által biztosított illesztőfelület-metódus meghívásával. Az `LaunchPlugin()` metódus Bemeneti paramétere olyan delegált, amely ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt felületet implementáló objektumot tud visszaadni.

    public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary\<string, Object\> parms); 

A ISCPBatchBolt `StormTxAttempt` `parms`ból, és felhasználhatja annak megítélésére, hogy egy visszajátszott kísérlet. Az újrapróbálkozási kísérleteket gyakran a véglegesítő Bolton hajtják végre, és a `HelloWorldTx` példa mutatja be.

Általánosságban elmondható, hogy az SCP beépülő modul két módban is futhat:

1. Helyi tesztelési mód: ebben a módban az SCP-bővítmények C# (a felhasználói kód) a Visual Studióban futnak a fejlesztési fázisban. a `LocalContext` használható ebben a módban, amely módszert biztosít a kibocsátott rekordok helyi fájlokra való szerializálásához, és a memóriába való visszaolvasásához.

        public interface ILocalContext
        {
            List\<SCPTuple\> RecvFromMsgQueue();
            void WriteMsgQueueToFile(string filepath, bool append = false);  
            void ReadFromFileToMsgQueue(string filepath);                    
        }

2. Normál mód: ebben a módban az SCP-bővítményeket a Storm Java-folyamat indította el.

    Íme egy példa a SCP beépülő modul indítására:

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

## <a name="topology-specification-language"></a>Topológia specifikációjának nyelve

Az SCP-topológia specifikációja az SCP-topológiák leírására és konfigurálására szolgáló, tartományra jellemző nyelv. Ez a Storm Clojure DSL (<https://storm.incubator.apache.org/documentation/Clojure-DSL.html>) szolgáltatáson alapul, és az SCP-vel van kiterjesztve.

A topológiai specifikációkat közvetlenül a Storm-fürthöz lehet elküldeni a ***runspec*** parancs használatával történő végrehajtáshoz.

A SCP.NET a következő függvényeket adta hozzá a tranzakciós topológiák definiálásához:

| Új függvények | Paraméterek | Leírás |
| --- | --- | --- |
| TX – topolopy |topológia – név<br />kiöntő – Térkép<br />bolt – Térkép |Tranzakciós topológia definiálása a topológia nevével, &nbsp;kiöntő definíciós Térkép és a csavarok definíciós térképe |
| SCP – TX-kiöntő |exec-név<br />args<br />mezők |Tranzakciós kiöntő definiálása. Az alkalmazást az ***argumentumok***használatával futtatja az ***exec Name*** paranccsal.<br /><br />A ***mezők*** a kiöntő kimeneti mezői |
| SCP-TX-batch-bolt |exec-név<br />args<br />mezők |Tranzakciós batch-csavar definiálása. Az alkalmazást az ***argumentumok*** használatával futtatja az ***exec Name*** paranccsal.<br /><br />A mezők a bolt kimeneti mezői. |
| SCP-TX-commit-bolt |exec-név<br />args<br />mezők |Tranzakciós véglegesítő bolt definiálása. Az alkalmazást az ***argumentumok***használatával futtatja az ***exec Name*** paranccsal.<br /><br />A ***mezők*** a bolt kimeneti mezői |
| nontx-topolopy |topológia – név<br />kiöntő – Térkép<br />bolt – Térkép |Nem tranzakciós topológia definiálása a topológia nevével,&nbsp; kiöntő definíciós Térkép és a csavarok definíciós térképe |
| SCP – kiöntő |exec-név<br />args<br />mezők<br />paraméterek |Nem tranzakciós kiöntő definiálása. Az alkalmazást az ***argumentumok***használatával futtatja az ***exec Name*** paranccsal.<br /><br />A ***mezők*** a kiöntő kimeneti mezői<br /><br />A ***Paraméterek*** nem kötelezőek, a használatával megadhatók bizonyos paraméterek, például a "nem tranzakciós. ACK. enabled". |
| SCP – bolt |exec-név<br />args<br />mezők<br />paraméterek |Nem tranzakciós csavar definiálása. Az alkalmazást az ***argumentumok***használatával futtatja az ***exec Name*** paranccsal.<br /><br />A ***mezők*** a bolt kimeneti mezői<br /><br />A ***Paraméterek*** nem kötelezőek, a használatával megadhatók bizonyos paraméterek, például a "nem tranzakciós. ACK. enabled". |

A SCP.NET a következő kulcsszavakat definiálja:

| Kulcsszavak | Leírás |
| --- | --- |
| : név |A topológia nevének megadása |
| : topológia |Adja meg a topológiát az előző függvények használatával, és építsen be is. |
| :p |Adja meg a párhuzamossági mutatót az egyes kiöntő vagy a boltokhoz. |
| : konfiguráció |Adja meg a configure paramétert, vagy frissítse a meglévőket |
| : séma |Adja meg a stream sémáját. |

És gyakran használt paraméterek:

| Paraméter | Leírás |
| --- | --- |
| "plugin.name" |a C# beépülő modul exe-fájljának neve |
| "beépülő modul. ARG" |beépülő modul argumentumai |
| "output. Schema" |Kimeneti séma |
| "nem tranzakciós. ACK. enabled" |Azt jelzi, hogy a ACK engedélyezve van-e a nem tranzakciós topológia esetében |

A runspec parancs a BITS szolgáltatással együtt települ, a használat például a következő:

    .\bin\runSpec.cmd
    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
    ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target

Az ***erőforrás-dir*** paraméter nem kötelező, meg kell adnia azt, amikor egy C# alkalmazást szeretne csatlakoztatni, és ez a könyvtár tartalmazza az alkalmazást, a függőségeket és a konfigurációkat.

A ***osztályútvonal*** paraméter szintén nem kötelező. A Java-osztályútvonal megadására szolgál, ha a spec fájl Java kiöntőt vagy boltot tartalmaz.

## <a name="miscellaneous-features"></a>Egyéb funkciók

### <a name="input-and-output-schema-declaration"></a>Bemeneti és kimeneti séma deklarációja

A felhasználók rekordok a C# folyamatokban, a platformnak a rekordot a [] bájtba kell szerializálnia, át kell vinnie a Java oldalára, a Storm pedig átadja ezt a rekordot a céloknak. Az alsóbb rétegbeli összetevőkben a C# folyamatok a Java oldalról kapnak rekordok, és az eredeti típusokra konvertálják a platform alapján, ezeket a műveleteket a platform elrejti.

A szerializálás és a deszerializálás támogatásához a felhasználói kódnak be kell jelentenie a bemenetek és kimenetek sémáját.

A bemeneti/kimeneti adatfolyam sémája szótárként van definiálva. A kulcs a Streamazonosítója. Az érték az oszlopok típusai. Az összetevőhöz több Stream is deklarálva van.

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


A környezeti objektumban a következő API-t vesszük fel:

    public void DeclareComponentSchema(ComponentStreamSchema schema)

A fejlesztőknek biztosítaniuk kell, hogy a kibocsátott rekordok engedelmeskednek az adott adatfolyamhoz definiált sémának, ellenkező esetben a rendszer futásidejű kivételt fog kiváltani.

### <a name="multi-stream-support"></a>Több adatfolyamos támogatás

A SZOLGÁLTATÁSKAPCSOLÓDÁSI pont támogatja a felhasználói kód használatát, amely egyszerre több különböző streamet bocsát ki vagy fogad. A támogatás tükrözi a környezeti objektumot, mivel a kibocsátás módszere egy opcionális stream ID paramétert vesz igénybe.

A SCP.NET környezeti objektum két metódust adott hozzá. A rendszer a rekord-vagy rekordok létrehozásához használja a Streamazonosítója megadására. A Streamazonosítója karakterlánc, és konzisztensnek kell lennie a C# és a topológia definíciójának specifikációjában is.

    /* Emit tuple to the specific stream. */
    public abstract void Emit(string streamId, List<object> values);

    /* for non-transactional Spout only */
    public abstract void Emit(string streamId, List<object> values, long seqId);

A nem létező adatfolyamok kibocsátása futásidejű kivételeket okoz.

### <a name="fields-grouping"></a>Mezők csoportosítása

A Storm-csoportosítás beépített mezői nem működnek megfelelően a SCP.NET-ben. A Java-proxy oldalon az összes mező adattípusa valójában bájt [], a csoportosítási mezők pedig a byte [] Object kivonatoló kódot használják a csoportosítás végrehajtásához. A bájt [] objektum kivonatának kódja az objektum címe a memóriában. Így a csoportosítás nem lesz megfelelő a kétbájtos objektumok esetében, amelyek ugyanazt a tartalmat használják, de nem ugyanaz a címe.

A SCP.NET egy testreszabott csoportosítási módszert használ, és a ([]) bájt tartalmát használja a csoportosítás végrehajtásához. A **spec** fájl szintaxisa a következőhöz hasonló:

    (bolt-spec
        {
            "spout_test" (scp-field-group :non-tx [0,1])
        }
        …
    )

itt

1. "SCP-Field-Group": "az SCP által megvalósított, testreszabott mezők csoportosítása".
2. ": TX" vagy ": non-TX" azt jelenti, hogy tranzakciós topológia van-e. Ezekre az adatokra azért van szükség, mert a kezdő index eltér a TX és a nem TX topológiák között.
3. [0, 1] a mezőértékek kivonata 0 alapján kezdődik.

### <a name="hybrid-topology"></a>Hibrid topológia

A natív Storm Java nyelven íródott. A SCP.NET pedig kijavította, C# hogy lehetővé tegye C# a fejlesztők számára az üzleti logikájuk kezelésére szolgáló kód írását. Ugyanakkor támogatja a hibrid topológiákat is, amelyek nem csak C# kiöntőt/csavarokat tartalmaznak, hanem a Java kiöntő/-boltokat is.

### <a name="specify-java-spoutbolt-in-spec-file"></a>Java kiöntő/bolt meghatározása a spec fájlban

A specifikációban a "SCP-kiöntő" és a "SCP-bolt" is használható a Java kiöntő és a csavarok megadására, íme egy példa:

    (spout-spec 
      (microsoft.scp.example.HybridTopology.Generator.)           
      :p 1)

Itt `microsoft.scp.example.HybridTopology.Generator` a Java kiöntő osztály neve.

### <a name="specify-java-classpath-in-runspec-command"></a>A Java-Osztályútvonal meghatározása a runSpec parancsban

Ha Java kiöntőt vagy csavarokat tartalmazó topológiát szeretne beküldeni, először le kell fordítania a Java-kiöntőt vagy-csavarokat, és le kell kérnie a jar-fájlokat. Ezután meg kell adnia azt a Java-osztályútvonal, amely a topológia elküldésekor a jar-fájlokat tartalmazza. Például:

    bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*

**Példa\\HybridTopology\\java\\target\\** a Java kiöntő/bolt jar-fájlt tartalmazó mappa.

### <a name="serialization-and-deserialization-between-java-and-c"></a>A Java és az közötti szerializálás és deszerializálásC#

Az SCP-összetevő tartalmazza a C# Java oldalt és az oldalt. A natív Java-kiöntő/-csavarokkal való interakcióhoz a szerializálási/deszerializálási műveleteket a Java-oldal és C# az oldal között kell végrehajtani, ahogyan az a következő ábrán látható.

![Java-összetevők küldésére szolgáló, a Java-összetevőnek küldött, SCP-összetevőnek küldött diagram](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

1. Szerializálás a Java-oldalon és a deszerializálás C# az oldalon

   Először is biztosítunk alapértelmezett implementációt a Java-és a C# deszerializált oldalról a szerializáláshoz. A Java oldalon a szerializálási módszer megadható a SPEC fájlban:

       (scp-bolt
           {
               "plugin.name" "HybridTopology.exe"
               "plugin.args" ["displayer"]
               "output.schema" {}
               "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
           })

   Az C# oldalon a deszerializálási módszert meg kell adni a C# felhasználói kódban:

       Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
       inputSchema.Add("default", new List<Type>() { typeof(Person) });
       this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
       this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());            

   Az alapértelmezett implementációnak a legtöbb esetben meg kell birkóznia, ha az adattípus nem túl összetett. Bizonyos esetekben, vagy azért, mert a felhasználói adattípus túl összetett, vagy mert az alapértelmezett implementáció teljesítménye nem felel meg a felhasználó követelményének, a felhasználók saját maguk is csatlakozhatnak a megvalósításhoz.

   A Java oldalon a szerializálási felület a következőképpen van definiálva:

       public interface ICustomizedInteropJavaSerializer {
           public void prepare(String[] args);
           public List<ByteBuffer> serialize(List<Object> objectList);
       }

   Az C# oldalon található deszerializáló felület a következőképpen van definiálva:

   nyilvános interfész ICustomizedInteropCSharpDeserializer

       public interface ICustomizedInteropCSharpDeserializer
       {
           List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
       }
2. Szerializálás az C# oldalon és a deszerializálás Java-oldalon

   A (z) C# oldali szerializálási módszert a C# felhasználói kódban kell megadni:

       this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 

   A Java-oldalon a deszerializálási módszert a SPEC fájlban kell megadni:

    ```
    (scp-spout
       {
         "plugin.name" "HybridTopology.exe"
         "plugin.args" ["generator"]
         "output.schema" {"default" ["person"]}
         "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
       }
    )
    ```

   Itt a "Microsoft. scp. Storm. multilang. CustomizedInteropJSONDeserializer" a deszerializáló neve, a "Microsoft. scp. example. HybridTopology. person" pedig az az érték, amelyet a rendszer deszerializál.

   A felhasználó a C# szerializáló és a Java deszerializáló saját implementációját is képes csatlakoztatni. Ez a kód a C# szerializáló felülete:

       public interface ICustomizedInteropCSharpSerializer
       {
           List<byte[]> Serialize(List<object> dataList);
       }

   Ez a kód a Java deszerializáló felülete:

       public interface ICustomizedInteropJavaDeserializer {
           public void prepare(String[] targetClassNames);
           public List<Object> Deserialize(List<ByteBuffer> dataList);
       }

## <a name="scp-host-mode"></a>SCP-gazdagép üzemmód

Ebben a módban a felhasználó lefordíthatja a kódokat a DLL-re, és az SCP által biztosított SCPHost. exe segítségével elküldheti a topológiát. A spec fájl a következő kódhoz hasonlít:

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

Itt `plugin.name` van megadva `SCPHost.exe` SZOLGÁLTATÁSKAPCSOLÓDÁSI pont SDK által megadott módon. A SCPHost. exe három paramétert fogad el:

1. Az első a DLL neve, amely `"HelloWorld.dll"` ebben a példában.
2. A második az osztálynév, amely `"Scp.App.HelloWorld.Generator"` ebben a példában.
3. A harmadik egy nyilvános statikus metódus neve, amely meghívható a ISCPPlugin egy példányának lekérésére.

A gazda módban a felhasználói kód DLL-ként van lefordítva, és az SCP-platform hívja meg. Így az SCP-platform teljes körűen vezérelheti a teljes feldolgozási logikát. Ezért javasoljuk ügyfeleinknek, hogy SCP-gazdagépi módban küldjenak be topológiát, mivel ez egyszerűbbé teheti a fejlesztési élményt, és nagyobb rugalmasságot és jobb visszamenőleges kompatibilitást biztosít a későbbi kiadásokhoz is.

## <a name="scp-programming-examples"></a>SCP-programozási példák

### <a name="helloworld"></a>HelloWorld

A **HelloWorld** egy egyszerű példa arra, hogy megjelenjen a SCP.net íze. Nem tranzakciós topológiát használ, és egy **generátor**nevű kiöntőt, valamint **két, elválasztó és** **számláló**nevű csavart használ. A kiöntő **generátor** véletlenszerűen generál mondatokat, és kibocsátja ezeket a mondatokat az **elosztóba**. A bolt * * osztó feldarabolja a mondatokat a szavakra, és kibocsátja **ezeket a szavakat a boltba** . A "számláló" az egyes szavak előfordulási számának rögzítésére szolgáló szótárt használ.

Ebben a példában két specifikációs fájl létezik: **HelloWorld. spec** és **HelloWorld\_EnableAck. spec** . A C# kódban megtudhatja, hogy az ACK engedélyezve van-e a pluginConf Java-oldalról való beolvasásával.

    /* demo how to get pluginConf info */
    if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
    {
        enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
    }
    Context.Logger.Info("enableAck: {0}", enableAck);

Ha a kifolyóban az ACK engedélyezve van, a rendszer egy szótárt használ a nem nyugtázott rekordok gyorsítótárazásához. Ha a Fail () metódust hívja meg, a rendszer visszajátssza a meghiúsult rekordot:

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

### <a name="helloworldtx"></a>HelloWorldTx

A **HelloWorldTx** példa bemutatja a tranzakciós topológia megvalósításának módját. Egy **generátor**nevű kiöntő, egy **részleges számú**batch-bolt, valamint egy **Count-Sum**nevű véglegesítő bolt tartozik hozzá. Három előre létrehozott txt-fájl is létezik: **DataSource0. txt**, **DataSource1. txt**és **DataSource2. txt**.

Az egyes tranzakciókban a kiöntő **generátor** véletlenszerűen kijelöl két fájlt az előre létrehozott három fájlból, és a két fájlnevet a **részleges számú** boltba bocsátja ki. A **részlegesen** megjelenő bolt neve beolvassa a fájl nevét a kapott rekordból, majd megnyitja a fájlt, és megszámolja a fájlban szereplő szavak számát, végül pedig kibocsátja a számot a **Count-Sum** boltba. A **Count összegű** bolt összefoglalja a teljes darabszámot.

Ha **pontosan egyszer** szeretné elérni a szemantikai teszteket, a beküldési csavarok **száma – az összegnek** meg kell ítélnie, hogy egy visszajátszott tranzakció. Ebben a példában egy statikus tag változót tartalmaz:

    public static long lastCommittedTxId = -1; 

A ISCPBatchBolt-példányok létrehozásakor a rendszer beolvassa a `txAttempt` a bemeneti paraméterekből:

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

`FinishBatch()` hívásakor a rendszer frissíti a `lastCommittedTxId`, ha nem egy újrajátszott tranzakció.

    public void FinishBatch(Dictionary<string, Object> parms)
    {
        /* judge whether it is a replayed transaction? */
        bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

        if (!replay)
        {
            /* If it is not replayed, update the totalCount and lastCommittedTxId value */
            totalCount = totalCount + this.count;
            lastCommittedTxId = this.txAttempt.TxId;
        }
        … …
    }

### <a name="hybridtopology"></a>HybridTopology

Ez a topológia egy Java kiöntőt és C# egy boltot tartalmaz. Az SCP platform által biztosított alapértelmezett szerializálási és deszerializálási implementációt használja. Tekintse meg a **HybridTopology. spec** **példákat\\HybridTopology** mappában a spec file details, és a **SubmitTopology. bat** fájlban, hogy miként kell megadnia a Java osztályútvonal.

### <a name="scphostdemo"></a>SCPHostDemo

Ez a példa ugyanaz, mint a HelloWorld lényegében. Az egyetlen különbség, hogy a felhasználói kód DLL-ként van lefordítva, és a topológiát a SCPHost. exe használatával küldi el a rendszer. Részletesebb magyarázatért tekintse meg a "SCP-gazdagép mód" című szakaszt.

## <a name="next-steps"></a>Következő lépések

Az SCP használatával létrehozott Apache Storm topológiák példáinak megtekintéséhez tekintse meg a következő dokumentumokat:

* [A C# Visual Studio használatával HDInsight Apache Storm-topológiák fejlesztése](apache-storm-develop-csharp-visual-studio-topology.md)
* [Események feldolgozása az Azure Event Hubs és az Apache Storm on HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Jármű-érzékelő adatainak feldolgozása a Event Hubs Apache Storm használatával a HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Kinyerési, átalakítási és betöltési (ETL) az Azure Event Hubs az Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
