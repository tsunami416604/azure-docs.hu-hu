---
title: "SCP.NET programozási útmutatója |} Microsoft Docs"
description: "Megtudhatja, hogyan lehet SCP.NET használatával létrehozni. A NET-alapú Storm-topológiák a HDInsight alatt futó Storm használható."
services: hdinsight
documentationcenter: 
author: raviperi
manager: jhubbard
editor: cgronlun
ms.assetid: 34192ed0-b1d1-4cf7-a3d4-5466301cf307
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/16/2016
ms.author: raviperi
ms.openlocfilehash: a0ce92ba58fbcda812a3d4e5e275178b73400d6c
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="scp-programming-guide"></a>Szolgáltatáskapcsolódási pont programozási útmutató
Szolgáltatáskapcsolódási pont platformot valós idejű, megbízható és konzisztens, létrehozásához, és az adatfeldolgozás nagy teljesítményű alkalmazás. Be van építve a [alatt futó Apache Storm](http://storm.incubator.apache.org/) – a streamfeldolgozási szerint a OSS Közösségek rendszer. Storm Nathan Marz lett tervezve, és nyílt forrása volt Twitter által. Ez a módszer a [Apache ZooKeeper](http://zookeeper.apache.org/), egy másik Apache projekt engedélyezése nagymértékben megbízható elosztott problémakoordinálás és állapotát. 

Nem csak a szolgáltatáskapcsolódási pont projekt legelterjedtebb Windows alatt futó Storm, hanem szintén hozzáadott a projekt a bővítmények és a Windows-ökoszisztéma testreszabása. A bővítmények közé tartozik a .NET-fejlesztők számára, és a szalagtárak, a Testreszabás tartalmazza a Windows-alapú telepítést. 

A bővítmény és a Testreszabás úgy, hogy azt nem kell oszthatja ketté a OSS-projektek, és azt sikerült kihasználhatják a Storm épülő származtatott ökoszisztéma történik.

## <a name="processing-model"></a>Folyamatmodell
A szolgáltatáskapcsolódási pont az adatok folyamatos listának van modellezve. Általában a rekordokat egymást követő néhány várólistán először, majd fel, és át legyenek-e a Storm-topológia belül található, végül a kimeneti másik SCP rendszerbe rekordokat, sikertelen lehet adatcsatornán, vagy tárolja, például elosztott fájlrendszerhez vagy például az SQL Server adatbázisok véglegesíteni üzleti logika.

![Adatok feldolgozása, amely adattárat-adatcsatornákat takarmányozására várólista ábrázoló diagram](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

Egy alkalmazás topológia a Storm, egy számítási grafikont határozza meg. Minden csomópont-topológiában feldolgozási logikát tartalmaz, és csomópontok közötti kapcsolatok jelölése. A bemeneti adatok behelyezése a topológia csomópontok nevezzük _spoutok_, amely akkor használható az adatok előkészítése. A bemeneti adatok volt várakozó fájl naplókat, a tranzakciós adatbázis, a rendszer teljesítményszámláló stb. A csomópontok mindkét bemeneti és kimeneti adatok folyamatokkal nevezzük _boltok_, amelyek ezt a tényleges adatok szűrést és a beállításokat és összesítési beállítások.

SCP-JE támogatja az ajánlott azon törekvéseit, a következő legalább egyszeri és pontosan-egyszer adatok feldolgozása. Az elosztott adatfolyam feldolgozása alkalmazásokban több hiba fordulhat elő, során az adatfeldolgozás, például a hálózati kimaradás, a gép hibájának vagy a felhasználói kód hiba stb. Következő legalább egyszeri feldolgozási biztosítja, hogy minden adat legalább egyszer általi feldolgozásának automatikusan ugyanazokat az adatokat visszajátszását, ha hiba történik. A legalább egyszeri feldolgozási egyszerű és megbízható, és megfelelő is számos alkalmazás. Azonban egy alkalmazás megkövetel egy pontos leltár, legalább egyszeri feldolgozási esetén elegendő óta ugyanazokat az adatokat potenciálisan szerepeltek a alkalmazás topológia. Ebben az esetben, pontosan-után feldolgozási arra tervezték, hogy ellenőrizze, hogy az eredmény helyességét még ha az adatokat a rendszer játssza és feldolgozása történhet többször.

SCP-je lehetővé teszi, hogy a .NET-fejlesztők számára, miközben a Java virtuális gép (JVM) Storm használhatja a színfalak valós idejű adatok folyamat alkalmazások fejlesztéséhez. A .NET és a JVM TCP helyi sockets keresztül kommunikálnak. Alapvetően minden Spout vagy Bolt .net/Java folyamat két, a felhasználó programot futtató .net folyamatban, a beépülő modul.

Hozható létre olyan adatfeldolgozási alkalmazás SCP fölött, több lépésre van szükség:

* Tervezése és megvalósítása a spoutokkal kapcsolatban való várólistából adatok lekérésére.
* Tervezése és megvalósítása a bemeneti adatok feldolgozására Boltokhoz, és adatok mentése külső áruházak, például egy adatbázis.
* A topológia megtervezésére, majd küldje el, és futtassa a topológia. A topológia meghatározása csomópontok és az adatokat a csomópontok közötti forgalom. Szolgáltatáskapcsolódási pont a topológia meghatározása fogad, majd azt egy Storm-fürt, minden egyes csúcspont futtató egy logikai csomóponton telepítette. A feladatátvétel és a Storm Feladatütemező kell venni megvagyunk méretezési lesz.

Ez a dokumentum egyszerű példák hogyan hozható létre a szolgáltatáskapcsolódási pont az adatfeldolgozás alkalmazás bízná használja.

## <a name="scp-plugin-interface"></a>Szolgáltatáskapcsolódási pont beépülő modul felület
Szolgáltatáskapcsolódási pont beépülő modulok (vagy alkalmazások) olyan önálló exe is futtatható belül a Visual Studio fejlesztői fázis során, és a Storm-feldolgozási folyamat alkalmazást éles környezetben kell csatlakoztatni. A szolgáltatáskapcsolódási pont beépülő modul írása ugyanúgy, mint korábban mint bármely más szabványos Windows konzol biztosító alkalmazások írására van. SCP.NET platform deklarál spout vagy bolt néhány felületet, és a beépülő modul a programkód inkább a konfigurációkezelővel. A fő a tervezési célja, hogy a felhasználó összpontosíthatnak, a saját üzleti logics és egyebek SCP.NET platform által kezelt hagyja.

A beépülő modul a programkód meg kell valósítania a következőket felületek egyikét, attól függ, hogy a topológia tranzakciós vagy nem tranzakciós-e, és hogy az összetevő adott spout vagy bolt.

* ISCPSpout
* ISCPBolt
* ISCPTxSpout
* ISCPBatchBolt

### <a name="iscpplugin"></a>ISCPPlugin
ISCPPlugin a közös felület különféle beépülő modulok. Azt jelenleg egy üres felületet.

    public interface ISCPPlugin 
    {
    }

### <a name="iscpspout"></a>ISCPSpout
ISCPSpout rendszer a nem tranzakciós spout,

     public interface ISCPSpout : ISCPPlugin                    
     {
         void NextTuple(Dictionary<string, Object> parms);         
         void Ack(long seqId, Dictionary<string, Object> parms);   
         void Fail(long seqId, Dictionary<string, Object> parms);  
     }

Ha `NextTuple()` neve, a C\# felhasználói kód el tudná küldeni egy vagy több rekordokat. Nincs mit hozható létre, ha ez a módszer kibocsátó semmit nem kell visszaadnia. Fontos megjegyezni, hogy `NextTuple()`, `Ack()`, és `Fail()` összes nevezzük egy egyetlen szálon c. szoros ismétlődő\# folyamat. Ha nem hozható létre rekordokat, udvarias NextTuple alvó kaphatnak a rövid időn (például 10 ezredmásodperc), hogy ne hulladék túl sok CPU.

`Ack()`és `Fail()` hívják, csak akkor, ha a nyugtázási mechanizmus fájlmegadásában fájlban engedélyezve van. A `seqId` alapján határozza meg a rekord, amely korrektúrák, vagy nem sikerült. Így ha nyugtázási nem tranzakciós topológia engedélyezve van, a következő kibocsátása függvény Spout kell használni:

    public abstract void Emit(string streamId, List<object> values, long seqId); 

Ha nyugtázási nem támogatott nem tranzakciós topológia a `Ack()` és `Fail()` maradhatnak, üres függvényében.

A `parms` ezeket a funkciókat a bemeneti paraméter egy üres szótárban, a későbbi használatra van fenntartva.

### <a name="iscpbolt"></a>ISCPBolt
ISCPBolt rendszer a nem tranzakciós bolt,

    public interface ISCPBolt : ISCPPlugin 
    {
    void Execute(SCPTuple tuple);           
    }

Ha új rekordot érhető el, a `Execute()` függvény hívása esetén feldolgozni azt.

### <a name="iscptxspout"></a>ISCPTxSpout
ISCPTxSpout a tranzakciós spout felületet.

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);  
        void Ack(long seqId, Dictionary<string, Object> parms);         
        void Fail(long seqId, Dictionary<string, Object> parms);        
    }

Akárcsak a nem tranzakciós másik részét `NextTx()`, `Ack()`, és `Fail()` összes nevezzük egy egyetlen szálon c. szoros ismétlődő\# folyamat. Nem állnak rendelkezésre adatok kibocsátásához, esetén szeretné, hogy udvarias `NextTx` a rövid időn (10 ezredmásodperc), hogy ne túl sok CPU hulladék az alvó állapot.

`NextTx()`új tranzakció, a kimeneti paramétert indításához nevezik `seqId` alapján határozza meg a tranzakció, amely is használva van `Ack()` és `Fail()`. A `NextTx()`, felhasználói el tudná küldeni az adatok Java oldalára. Az adatok ismétlési támogatásához ZooKeeper tárolja. ZooKeeper kapacitása korlátozott, mert a felhasználó kell csak számú metaadat, a tranzakciós spout adatok tömeges sikertelen.

A Storm fog visszajátszásos automatikusan egy tranzakció, ha a hiba, így `Fail()` normál esetben nem szabad meghívni. De ha a szolgáltatáskapcsolódási pont ellenőrizheti a metaadatok tranzakciós spout által kibocsátott, akkor meghívhatja `Fail()` érvénytelen a metaadatok esetén.

A `parms` ezeket a funkciókat a bemeneti paraméter egy üres szótárban, a későbbi használatra van fenntartva.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt
ISCPBatchBolt a tranzakciós bolt felületet.

    public interface ISCPBatchBolt : ISCPPlugin           
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);  
    }

`Execute()`Ha új rekordot tartalmaz a bolt érkező neve. `FinishBatch()`Amikor befejeződik a tranzakció neve. A `parms` bemeneti paraméter későbbi használatra van fenntartva.

Tranzakciós topológia, egy fontos tényező – nincs `StormTxAttempt`. Rendelkezik két mező `TxId` és `AttemptId`. `TxId`egy bizonyos tranzakció azonosítására szolgál a megadott tranzakció, előfordulhat, hogy többszöri a tranzakció sikertelen, és ha a rendszer játssza vissza. SCP.NET új objektumot hoz létre ISCPBatchBolt feldolgozni az egyes `StormTxAttempt`, csak a például a Storm funkciója Java nyelven. Ez a kialakítás célja támogatja a párhuzamos tranzakciókat feldolgozása. Felhasználói tartsa azt, hogy ha a tranzakció kísérlet befejeződött, a megfelelő ISCPBatchBolt objektum megsemmisül szem előtt tartva és szemétgyűjtő.

## <a name="object-model"></a>Hálózatiobjektum-modellje
SCP.NET is tartalmaz egy egyszerű objektumok a fejlesztők számára a program. Ezek **környezetben**, **Állapottárolója**, és **SCPRuntime**. Azok a többi részét ez a szakasz ismerteti.

### <a name="context"></a>Környezet
Az alkalmazás futó környezetet biztosít a környezetben. Minden egyes ISCPPlugin példány (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) rendelkezik egy megfelelő adatkörnyezet példányához. A környezet által biztosított funkciókat lehet osztani két részből áll: (1) a statikus részét, amelyik elérhető a teljes c\# feldolgozni, (2) a dinamikus részben, amely csak a helyi példány.

### <a name="static-part"></a>Statikus részében szerepel
    public static ILogger Logger = null;
    public static SCPPluginType pluginType;                      
    public static Config Config { get; set; }                    
    public static TopologyContext TopologyContext { get; set; }  

`Logger`napló célra valósul meg.

`pluginType`jelzi a beépülő modul típusa a C\# folyamat. Ha a C\# folyamat (nélkül Java) tesztcélú helyi módban fut, a beépülő modul típusa `SCP_NET_LOCAL`.

    public enum SCPPluginType 
    {
        SCP_NET_LOCAL = 0,       
        SCP_NET_SPOUT = 1,       
        SCP_NET_BOLT = 2,        
        SCP_NET_TX_SPOUT = 3,   
        SCP_NET_BATCH_BOLT = 4  
    }

`Config`konfigurációs paraméterek lekérése Java ügyféloldali valósul meg. A paraméterek átadása Java oldaláról amikor C\# beépülő modul inicializálása. A `Config` paraméterek oszthatók két részből áll: `stormConf` és `pluginConf`.

    public Dictionary<string, Object> stormConf { get; set; }  
    public Dictionary<string, Object> pluginConf { get; set; }  

`stormConf`a Storm által definiált paraméterek és `pluginConf` a szolgáltatáskapcsolódási pont által megadott paramétereket. Példa:

    public class Constants
    {
        … …

        // constant string for pluginConf
        public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

        // constant string for stormConf
        public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";           
        public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";                 
    }

`TopologyContext`megadott ahhoz, hogy a topológia a környezetben, esetén a leghasznosabb összetevők több párhuzamosság együtt. Például:

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

### <a name="dynamic-part"></a>Dinamikus részében szerepel
A következő kapcsolódási pontok bizonyos környezetben példányra vonatkozó. A helyi példány SCP.NET platform által létrehozott, a felhasználói kód átadott:

    // Declare the Output and Input Stream Schemas

    public void DeclareComponentSchema(ComponentStreamSchema schema);   

    // Emit tuple to default stream.
    public abstract void Emit(List<object> values);                   

    // Emit tuple to the specific stream.
    public abstract void Emit(string streamId, List<object> values);  

Nem tranzakciós spout nyugtázási támogatása a következő metódus biztosítja:

    // for non-transactional Spout which supports ack
    public abstract void Emit(string streamId, List<object> values, long seqId);  

A nem tranzakciós bolt nyugtázási támogató, legyen, vagy ha kifejezetten `Ack()` vagy `Fail()` kapott rekordban. És kibocsátó új rekordot, amikor meg kell adnia az új rekord horgonyok. A következő módszerek állnak rendelkezésre.

    public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values); 
    public abstract void Ack(SCPTuple tuple);
    public abstract void Fail(SCPTuple tuple);

### <a name="statestore"></a>Állapottárolója
`StateStore`metaadatok szolgáltatások, a monoton sorrend létrehozása és a várakozási szabad koordinációs biztosít. Elosztott feldolgozási magasabb szintű absztrakciók építhetők `StateStore`, beleértve az elosztott zárolásokat, elosztott várólisták, korlátok és tranzakciós szolgáltatásokat.

Szolgáltatáskapcsolódási pont alkalmazások is használhatnak a `State` objektum egyes információk ZooKeeper, különösen a tranzakciós topológia megőrzéséhez. Ennek során, ha a tranzakciós spout összeomlik, és indítsa újra, a szükséges adatok lekérését ZooKeeper, és indítsa újra a folyamatot.

A `StateStore` főként objektumnak ezen módszerek:

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
    /// <returns>Uncommited States</returns>
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
    public IEnumerable<Registry> Commited();

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

A `State` főként objektumnak ezen módszerek:

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

Az a `Commit()` metódust, ha simpleMode értéke igaz, törli a ZooKeeper megfelelő ZNode. Ellenkező esetben törli az aktuális ZNode, és egy új csomópont hozzáadása a LEKÖTÖTT a\_elérési ÚTJA.

### <a name="scpruntime"></a>SCPRuntime
SCPRuntime az alábbi két módszert biztosít:

    public static void Initialize();

    public static void LaunchPlugin(newSCPPlugin createDelegate);  

`Initialize()`a szolgáltatáskapcsolódási pont futásidejű környezet inicializálása szolgál. Ennél a módszernél a C\# folyamat a Java oldalon kapcsolódik, és lekérdezi a konfigurációs paraméterek és a topológia a környezetben.

`LaunchPlugin()`az üzenet feldolgozása hurok indítsa szolgál. Ez a ciklus, a C a\# beépülő modul fogadja az üzeneteket űrlap Java ügyféloldali (beleértve a rekordokat és a vezérlő jeleket), és majd feldolgozása az üzeneteket, lehet, hogy az az illesztőfelület-metódust hívja meg a felhasználói kód által. A bemeneti paraméter metódus `LaunchPlugin()` van olyan delegált esetén, amely ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt felületet megvalósító objektum adhat vissza.

    public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary\<string, Object\> parms); 

A ISCPBatchBolt, beszerezheti a Microsoft `StormTxAttempt` a `parms`, és annak segítségével mennyire, hogy-e egy megismételt kísérlet után. A véglegesítési bolt gyakran teheti meg egy ismétlési kísérlet az ellenőrzést, és azt mutatják be a `HelloWorldTx` példa.

A szolgáltatáskapcsolódási pont beépülő modulok általánosan fogalmazva, itt két módban futhat:

1. Helyi tesztelése mód: Ebben a módban, a szolgáltatáskapcsolódási pont beépülő modulok (a C\# felhasználói kód) Visual Studio belül a fejlesztési fázis során futtassa. `LocalContext`Ebben a módban a helyi fájlok kibocsátott rekordokat szerializálni, és vissza memória a olvashatja őket módszert biztosít használható.
   
        public interface ILocalContext
        {
            List\<SCPTuple\> RecvFromMsgQueue();
            void WriteMsgQueueToFile(string filepath, bool append = false);  
            void ReadFromFileToMsgQueue(string filepath);                    
        }
2. Normál mód: Ebben a módban a szolgáltatáskapcsolódási pont beépülő modulok indítja storm java folyamat.
   
    Íme egy példa SCP beépülő modul megnyitása:
   
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

## <a name="topology-specification-language"></a>Topológia nyelv
Szolgáltatáskapcsolódási pont topológia meghatározása az egy tartományspecifikus nyelv és SCP topológiák konfigurálása. A Storm Clojure DSL alapul (<http://storm.incubator.apache.org/documentation/Clojure-DSL.html>) és az SCP bővített.

Topológia specifikációk küldheti el közvetlenül a storm-fürt végrehajtásra keresztül a ***runspec*** parancsot.

SCP.NET hozzáadta a következő funkciók tranzakciós topológiák megadása:

| **Új funkciók** | **Paraméterek** | **Leírás** |
| --- | --- | --- |
| **Tx-topolopy** |topológia-név<br />spout-leképezés<br />bolt-leképezés |Adja meg a topológia néven, a tranzakciós topológia &nbsp;spoutok definition térkép és a boltokhoz definition térkép |
| **SCP-tx-spout** |Exec-név<br />argumentum<br />Mezők |Adja meg a tranzakciós spout. Az alkalmazás futása ***exec-name*** használatával ***argumentum***.<br /><br />A ***mezők*** a spout a kimeneti mezők |
| **SCP-tx-batch-bolt** |Exec-név<br />argumentum<br />Mezők |Adja meg egy olyan tranzakciós kötegben Bolthoz. Az alkalmazás futása ***exec-name*** használatával ***argumentum.***<br /><br />A mezők a mező a kimenetre bolt. |
| **SCP-tx-commit-bolt** |Exec-név<br />argumentum<br />Mezők |Adja meg egy olyan tranzakciós véglegesítési bolthoz. Az alkalmazás futása ***exec-name*** használatával ***argumentum***.<br /><br />A ***mezők*** a bolt a kimeneti mezők |
| **nontx-topolopy** |topológia-név<br />spout-leképezés<br />bolt-leképezés |Adja meg a nem tranzakciós-topológia a topológia nevű&nbsp; spoutok definition térkép és a boltokhoz definition térkép |
| **SCP-spout** |Exec-név<br />argumentum<br />Mezők<br />paraméterek |Adja meg egy nem tranzakciós spout. Az alkalmazás futása ***exec-name*** használatával ***argumentum***.<br /><br />A ***mezők*** a spout a kimeneti mezők<br /><br />A ***paraméterek*** megadása nem kötelező, segítségével adja meg az egyes paraméterek, például "nontransactional.ack.enabled". |
| **SCP-bolt** |Exec-név<br />argumentum<br />Mezők<br />paraméterek |Adja meg egy nem tranzakciós Bolt. Az alkalmazás futása ***exec-name*** használatával ***argumentum***.<br /><br />A ***mezők*** a bolt a kimeneti mezők<br /><br />A ***paraméterek*** megadása nem kötelező, segítségével adja meg az egyes paraméterek, például "nontransactional.ack.enabled". |

SCP.NET van megadva a következő kulcsszó:

| **Kulcsszavak** | **Leírás** |
| --- | --- |
| **: neve** |A topológia nevének meghatározása |
| **: topológia** |Adja meg a topológia a korábbi függvények használatával, és azokat a hozhat létre. |
| **: p** |Adja meg az egyes spout vagy bolt a párhuzamos végrehajtás mutató. |
| **: config** |Adja meg konfigurálása paraméter, vagy módosíthat a már meglévő |
| **: séma** |Az adatfolyam-séma határozza meg. |

És a gyakran használt paraméterek:

| **A paraméter** | **Leírás** |
| --- | --- |
| **"plugin.name"** |a C# beépülő modul exe-fájl neve |
| **"plugin.args"** |beépülő modul argumentum |
| **"output.schema"** |Kimeneti séma |
| **"nontransactional.ack.enabled"** |Nyugtázási engedélyezve van-e a nem tranzakciós topológia |

A runspec parancs együtt a bits központi telepítése, a használati hasonlít:

    .\bin\runSpec.cmd
    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
    ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target

A ***erőforrás-dir*** paraméter nem kötelező, meg kell adnia azt, ha azt szeretné, hogy csatlakoztassák a C\# az alkalmazás és a könyvtár tartalmazza az alkalmazás, a függőségeket és konfigurációkat.

A ***classpath*** paramétert is nem kötelező. Adja meg a Java classpath, ha a fájlmegadásában fájl tartalmaz Java Spout vagy Bolt szolgál.

## <a name="miscellaneous-features"></a>Egyéb szolgáltatások
### <a name="input-and-output-schema-declaration"></a>Bemeneti és kimeneti séma nyilatkozat
Felhasználók el tudná küldeni c. rekordokat\# folyamatok, a platform kell szerializálni a rekord a byte [], Java ügyféloldali átvitele, és a Storm szoftveres át ezt a rekordot. Eközben az alsóbb rétegbeli összetevők C\# folyamatok rekordokat fogadja vissza java oldaláról, és konvertálja az eredeti típusok platform, ezeket a műveleteket a platform rejtett.

A szerializálás és a deszerializálás támogatására, felhasználói kód kell a be- és kimenetekkel sémája deklarálja.

A bemeneti/kimeneti adatfolyam séma dictionary típusúként van definiálva. A kulcs a StreamId. Az oszlopok típusú érték. Az összetevő rendelkezhet több adatfolyamok deklarálva.

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


A környezeti objektumot a következő API, hozzáadott vezetünk be:

    public void DeclareComponentSchema(ComponentStreamSchema schema)

A fejlesztők biztosítania kell, hogy a kibocsátott rekordokat veszi fel az adott adatfolyam definiált séma, ellenkező esetben a rendszer kivételhibát futásidejű kivételt.

### <a name="multi-stream-support"></a>Több adatfolyam-támogatás
Szolgáltatáskapcsolódási pont felhasználói kód kibocsátás / egyszerre több különböző adatfolyam fogadását támogatja. A támogatási által adott jelentéseket tükrözik a Context objektumra, kibocsátása metódus egy választható adatfolyam-azonosító paramétert fogad.

A SCP.NET környezeti objektumot a két módszer hozzá lett adva. Segítségükkel számú rekordot, vagy a rekordokat StreamId adja meg. A StreamId: karakterlánc, és mindkét C konzisztens kell\# és a topológia meghatározása specifikációi.

        /* Emit tuple to the specific stream. */
        public abstract void Emit(string streamId, List<object> values);

        /* for non-transactional Spout only */
        public abstract void Emit(string streamId, List<object> values, long seqId);

Egy nem létező adatfolyam vezérlés futásidejű kivételek okoz.

### <a name="fields-grouping"></a>Mezők csoportosítás
A beépített mezők csoportosításának Strom a SCP.NET nem működik megfelelően. A Java-Proxy oldalon a mezők az összes adattípus ténylegesen byte [], és a csoportosítási mezőket a byte [] objektum kivonatkód segítségével hajtsa végre a csoportosítási. A byte [] objektum kivonatkód az a cím az objektum a memóriában. Ezért a csoportosítás két byte [] objektumok, amelyek ugyanahhoz a tartalomhoz, de nem ugyanazt a címet helytelen lesz.

SCP.NET hozzáadja egy testreszabott csoportosítási módszer, majd azt használja a byte [] tartalmát a csoportosítást. A **SPEC** fájl, a szintaxis hasonlít:

    (bolt-spec
        {
            "spout_test" (scp-field-group :non-tx [0,1])
        }
        …
    )


itt

1. "scp-mező-csoport": "Testre szabott mező csoportosítási szolgáltatáskapcsolódási pont által megvalósított".
2. ": tx"vagy": nem tx" azt jelenti, hogy tranzakciós topológia esetén. Mivel a kezdő index különbözik a tx és nem tx topológiák kell ezt az információt.
3. [0,1] azt jelenti, hogy egy mező azonosítók, 0-től kezdődő kivonatoló készletét.

### <a name="hybrid-topology"></a>Hibrid topológia
A natív Storm Java nyelven van megírva. És SCP.Net továbbfejlesztett C engedélyezéséhez\# írása C fejlesztők\# kezelésére az üzleti logika kódot. De is támogatja. hibrid topológiák, amely tartalmaz, nem csak a C\# spoutokkal kapcsolatban/boltokhoz, emellett pedig Java Spout/Boltokhoz.

### <a name="specify-java-spoutbolt-in-spec-file"></a>Adja meg a Java Spout vagy Bolt fájlmegadásában fájlban
Speciális fájlban "scp-spout" és "scp-bolt" is segítségével adja meg a Java Spoutok és Boltokhoz, például:

    (spout-spec 
      (microsoft.scp.example.HybridTopology.Generator.)           
      :p 1)

Itt `microsoft.scp.example.HybridTopology.Generator` a Java Spout osztály neve.

### <a name="specify-java-classpath-in-runspec-command"></a>Adja meg a Java Classpath runSpec parancs
Ha azt szeretné elküldeni a Java Spoutok vagy Boltokhoz tartalmazó topológia, először a Java Spoutok vagy Boltokhoz fordítása, és a Jar-fájlok szüksége. Majd adja meg a java classpath, amely tartalmazza a Jar-fájlok topológia elküldésekor. Például:

    bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*

Itt **példák\\HybridTopology\\java\\cél\\**  a Java Spout vagy Bolt Jar-fájlt tartalmazó mappa.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Szerializálás és a deszerializálás között a Java és a C\#
Szolgáltatáskapcsolódási pont a következő Java és a C\# oldalán. Ahhoz, hogy kommunikáljanak a natív Java spoutokkal kapcsolatban/Boltokhoz, szerializálással/Deszerializálással el kell végezni között a Java és a C\# oldalán, az alábbi diagramon ismertetett módon.

![java-összetevő küldése a szolgáltatáskapcsolódási pont összetevő Java összetevő küldése ábrája](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

1. **A Java és a deszerializálás c. szerializálási\# oldal**
   
   Először igazolnia implementálásához alapértelmezett szerializálás Java oldal és a deszerializálás c.\# oldalán. A szerializálási metódus a Java oldalon FÁJLMEGADÁSÁBAN fájl adható meg:
   
       (scp-bolt
           {
               "plugin.name" "HybridTopology.exe"
               "plugin.args" ["displayer"]
               "output.schema" {}
               "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
           })
   
   A deszerializálási metódus c.\# oldalon kell megadni C\# felhasználói kód:
   
       Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
       inputSchema.Add("default", new List<Type>() { typeof(Person) });
       this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
       this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());            
   
   Az alapértelmezett implementációja kezelje a legtöbb esetben a megadott adattípus nem túl összetett. Egyes esetekben, mert a felhasználói adatok típusa túl összetett, vagy mert az alapértelmezett implementációja teljesítményének nem felel meg a felhasználói követelményeket, a felhasználók is beépülő modul a saját megvalósítási.
   
   A java ügyféloldali szerializálása felület típusúként van definiálva:
   
       public interface ICustomizedInteropJavaSerializer {
           public void prepare(String[] args);
           public List<ByteBuffer> serialize(List<Object> objectList);
       }
   
   A C deserialize felület\# ügyféloldali típusúként van definiálva:
   
   Nyilvános csatoló ICustomizedInteropCSharpDeserializer
   
       public interface ICustomizedInteropCSharpDeserializer
       {
           List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
       }
2. **Szerializálási c.\# ügyféloldali és a deszerializálás Java oldal**
   
   A szerializálási metódus a C\# oldalon kell megadni C\# felhasználói kód:
   
       this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
   
   A deszerializálási metódus Java oldal FÁJLMEGADÁSÁBAN fájlt kell megadni:
   
     (scp-spout
   
       {
         "plugin.name" "HybridTopology.exe"
         "plugin.args" ["generator"]
         "output.schema" {"default" ["person"]}
         "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
       })
   
   Itt "microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" deszerializáló nevét, és "microsoft.scp.example.HybridTopology.Person" tartozó az adatokat a rendszer deszerializálni.
   
   Felhasználói is csatlakoztatható a saját C végrehajtásának\# szerializáló és Java deszerializáló. Ez a kód a felület C\# szerializáló:
   
       public interface ICustomizedInteropCSharpSerializer
       {
           List<byte[]> Serialize(List<object> dataList);
       }
   
   Ez a kód a Java-deszerializáló felület:
   
       public interface ICustomizedInteropJavaDeserializer {
           public void prepare(String[] targetClassNames);
           public List<Object> Deserialize(List<ByteBuffer> dataList);
       }

## <a name="scp-host-mode"></a>Szolgáltatáskapcsolódási pont a gazdagép módja
Ebben a módban a felhasználó DLL a kód fordítása, és küldje el a topológia a szolgáltatáskapcsolódási pont által biztosított SCPHost.exe segítségével. A speciális fájl néz ki ezt a kódot:

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

Itt `plugin.name` megadott `SCPHost.exe` SCP SDK által biztosított. SCPHost.exe három paramétert fogad:

1. Az első címtárra a dll-fájl neve, amely `"HelloWorld.dll"` ebben a példában.
2. A második érték az osztály nevét, amely `"Scp.App.HelloWorld.Generator"` ebben a példában.
3. A harmadik egy esetén nyilvános statikus metódus, amely ISCPPlugin példányának eléréséhez is elindítható.

Állomás üzemmódban a felhasználói kód, dll-fájl fordítása, és SCP platform által indított. Szolgáltatáskapcsolódási pont platform, teljes körű hozzáférést engedélyezzenek a teljes feldolgozó logika kérheti le. Ezért ajánlott SCP-t állomás üzemmódban topológia elküldeni, mivel a fejlesztési élmény egyszerűsítése és velünk, valamint a későbbi kiadásban kapcsolása nagyobb rugalmasságot és jobban előző verziókkal való kompatibilitás ügyfeleink.

## <a name="scp-programming-examples"></a>Szolgáltatáskapcsolódási pont programozási példák
### <a name="helloworld"></a>HelloWorld
**HelloWorld** egy egyszerű példa, hogy SCP.Net megjelenítéséhez. A nem tranzakciós topológia használ egy nevű spout **generátor**, és két boltokhoz nevű **elválasztó** és **számláló**. A spout **generátor** véletlenszerűen mondat hoz létre, és a mondatok kibocsátás **elválasztó**. A bolt **elválasztó** felosztja a szavakat a mondatok, és ezek a szavakat kibocsátás **számláló** bolt. A bolt "számláló" dictionary használatával jegyezze fel minden szó előfordulási száma.

Két fájlmegadásában fájlt **HelloWorld.spec** és **HelloWorld\_EnableAck.spec** ehhez a példához. A c\# kódot, akkor talál, nyugtázási engedélyezve van-e el a pluginConf Java oldaláról.

    /* demo how to get pluginConf info */
    if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
    {
        enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
    }
    Context.Logger.Info("enableAck: {0}", enableAck);

A spout a nyugtázási engedélyezve van, ha dictionary szolgál a rekordokat, amelyek még nincsenek korrektúrák gyorsítótárazásához. Fail() nevezik, ha a sikertelen rekordot a rendszer játssza vissza:

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
A **HelloWorldTx** példa bemutatja, hogyan tranzakciós topológia végrehajtásához. Rendelkezik egy spout nevű **generátor**, egy kötegelt bolt nevű **részleges-count**, és egy véglegesítési bolt nevű **száma összeg**. Van még három előre létrehozott txt-fájloknál: **DataSource0.txt**, **DataSource1.txt**, és **DataSource2.txt**.

Az egyes tranzakciókra, a spout **generátor** véletlenszerűen kiválaszt két fájlt a három előre létrehozott fájlokat, és hozható létre a két nevet, hogy a **részleges-count** bolt. A bolt **részleges-száma** lekérdezi a fájl a fogadott rekord nevet, majd nyissa meg a fájlt az ebben a fájlban szavak számát, és végül hozható létre a word számát a **száma összeg** bolt. A **száma összeg** bolt a számuk foglalja össze.

Eléréséhez **pontosan egyszer** szemantikáját, a véglegesítési bolt **száma összeg** mennyire, hogy-e egy megismételt tranzakció szükséges. Ebben a példában egy statikus tag változó rendelkezik:

    public static long lastCommittedTxId = -1; 

Amikor ISCPBatchBolt példány létrehozásakor, a `txAttempt` bemeneti paraméterek közül:

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

Ha `FinishBatch()` neve, a `lastCommittedTxId` frissülnek, ha még nincs megismételt tranzakció.

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
Ez a topológia tartalmaz egy Java Spout és egy C\# Bolt. A szolgáltatáskapcsolódási pont platform által biztosított alapértelmezett szerializálása és deszerializálása végrehajtására használ. Tekintse meg a **HybridTopology.spec** a **példák\\HybridTopology** mappában a fájlmegadásában fájlt, és **SubmitTopology.bat** a Java megadása classpath.

### <a name="scphostdemo"></a>SCPHostDemo
Ez a példa megegyezik a HelloWorld lényegében. Az egyetlen különbség, hogy a felhasználói kód lefordított dll-fájl, és a topológia beküldött SCPHost.exe használatával. Részletes ismertetése "SCP-t állomás üzemmódban" című szakaszában talál.

## <a name="next-steps"></a>Következő lépések
Szolgáltatáskapcsolódási pont használatával létrehozott Storm-topológiák példákért lásd a következő dokumentumokat:

* [Visual Studio használatával HDInsight alatt futó Apache Storm a C#-topológiák fejlesztése](apache-storm-develop-csharp-visual-studio-topology.md)
* [Az Azure Event Hubs a HDInsight alatt futó Storm eseményeinek](apache-storm-develop-csharp-event-hub-topology.md)
* [Az Event Hubs a HDInsight alatt futó Storm használatával vehicle érzékelő adatok feldolgozása](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Bontsa ki, átalakítás és betöltés (ETL) az Azure Event Hubs HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
