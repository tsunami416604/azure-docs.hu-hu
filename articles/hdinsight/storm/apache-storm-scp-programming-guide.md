---
title: Az Azure HDInsight alatt futó stormra SCP.NET – programozási útmutató
description: Megtudhatja, hogyan hozhat létre az SCP.NET használatával. NET-alapú Storm-topológiák az Azure HDInsight-ban futó Storm használható.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2016
ms.openlocfilehash: 848663c509fd3635b33b8e7735feb940da215bfa
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57441814"
---
# <a name="scp-programming-guide"></a>Szolgáltatáskapcsolódási pont programozási útmutató
Szolgáltatáskapcsolódási pont platformot hozhat létre valós idejű, megbízható és konzisztens, és nagy teljesítményű adatokat feldolgozó alkalmazást. Be van építve a [Apache Storm](https://storm.incubator.apache.org/) – egy streamfeldolgozó rendszer, a nyílt Forráskódú Közösségek által tervezték. A Storm Nathan Marz által készült, és a nyílt forráskódú lett a Twitteren. A modul [Apache ZooKeeper](https://zookeeper.apache.org/), magas megbízhatóságú engedélyezése egy másik Apache-projecttel elosztott koordinálása és állapot-kezelés. 

Nem csak az SCP-projekt már a Windows-alapú Storm, de is hozzá a projekthez, bővítmények és a Windows-ökoszisztéma testreszabást. A bővítmények közé tartozik a .NET-fejlesztők számára, és a szalagtárak, a Testreszabás tartalmazza a Windows-alapú központi telepítés. 

A bővítmény és a testreszabási úgy, hogy azt nem kell ágaztatnia a nyílt Forráskódú projektek, és hogy hasznosíthatja a Storm-ra épülő származtatott rendszereit történik.

## <a name="processing-model"></a>A modell feldolgozása
Az adatok SCP-ben a rekordok folyamatos adatfolyamokat van modellezve. Általában a rekordok felsorolásának folyamat néhány várólistába először, majd gridtől, és alakíthatók át, a Storm-topológia hálózatán futó üzleti logikát, végül a kimeneti sikerült átadható olyan parancsoknak, a rekordok egy másik SCP-rendszeren, vagy lehet fontos, hogy a tárolókat, mint az elosztott fájlrendszer vagy adatbázisok például az SQL Server.

![A feldolgozás, amely adattár hírcsatornák adatok számból üzenetsor ábrája](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

A Storm az alkalmazás topológiájának egy számítási grafikont határozza meg. Minden egyes csomópont topológiában feldolgozási logikáját tartalmazza, és a csomópontok közötti hivatkozások azt jelzik az adatfolyam. A bemeneti adatok behelyezése a topológia csomópontok nevezzük _spoutok_, az adatok előkészítéséhez használható. A bemeneti adatok sikerült várakozó fájl naplókat, a tranzakciós adatbázisok, a rendszer teljesítményszámláló stb. A csomópontok mindkét bemeneti és kimeneti adatok folyamatot nevezik _boltok_, amelyek ezt a tényleges adatok szűrése és a beállításokat és összesítésének megadására szolgál.

SCP-JE támogatja az gyakorlati erőfeszítések, legalább egyszeri és pontosan-adatok feldolgozása után. Egy elosztott alkalmazásban streamelési feldolgozási több hiba fordulhat elő, során adatokat feldolgozó, például a hálózati kimaradások, a gép hiba vagy a felhasználói kód hiba stb. A legalább egyszeri az eljárás biztosítja, hogy az összes adat fogja feldolgozni, legalább egyszer automatikusan ugyanazokat az adatokat visszajátszásával, ha hiba történik. A legalább egyszeri feldolgozási egyszerű és megbízható, és leginkább megfelelő is számos alkalmazás. Azonban egy alkalmazást a pontos leltár van szüksége, legalább egyszeri feldolgozási esetén elegendő óta ugyanazokat az adatokat esetleg játszható az alkalmazás topológiában. Ebben az esetben, pontosan – Miután feldolgozási úgy tervezték, hogy ellenőrizze, hogy az eredmény helyességét még ha az adatok játssza vissza és feldolgozása történhet az több alkalommal.

SCP-je lehetővé teszi, hogy a .NET-fejlesztők számára a Java virtuális gép (JVM) a Storm költséghatékonyhatékonyságából valójában valós idejű adatok folyamat alkalmazások fejlesztéséhez. A .NET és a JVM helyi TCP-szoftvercsatornák keresztül kommunikálnak. Gyakorlatilag minden egyes Spout vagy Bolt .net vagy Java folyamat két, ahol a felhasználó logic, a beépülő modul .net folyamatként futtatja.

Az SCP felett adatfeldolgozó alkalmazás létrehozása, több lépésre van szükség:

* Tervezése és megvalósítása a Spoutok használatával kérhetők le adatok üzenetsorból.
* Tervezése és megvalósítása dolgozzák fel a bemeneti adatok feldolgozásához, és adatok mentése külső áruházak, például egy adatbázisba.
* A topológia megtervezése, majd küldje el, és a topológia. A topológia meghatározása a csúcspontok és az adatok csomópontok közötti folyamatok. Szolgáltatáskapcsolódási pont a topológia meghatározása igénybe, és a telepítenie a Storm-fürt, ahol minden csúcspont fut egy logikai csomóponton. A feladatátvétel és kell elvégzi, a Storm Feladatütemező méretezési lesz.

Ez a dokumentum néhány egyszerű példák bemutatják, hogyan hozhat létre a szolgáltatáskapcsolódási pont adatokat feldolgozó alkalmazást használ.

## <a name="scp-plugin-interface"></a>SCP Plugin Interface
SCP beépülő modulok (vagy alkalmazásokat), amelyeket különálló exe is futtatható belül a Visual Studio fejlesztési fázisában, és a Storm-folyamat az éles üzembe helyezés után kell csatlakoztatni. A szolgáltatáskapcsolódási pont beépülő modul írása ugyanúgy mint bármely más szabványos Windows console-alkalmazások írására van. SCP.NET platform deklarálja spout vagy bolt néhány felületet, és a felhasználói beépülő modul kód valósítsa meg ezeket az adaptereket. A fő Ez a kialakítás célja, hogy a felhasználó koncentrálhat saját üzleti logics, változatlanul hagyása mellett további tevékenység következik, SCP.NET platformon kell kezelnie.

A felhasználó beépülő modul kódot kell megvalósítania, a következőket felületek egyike, attól függ, hogy a topológia tranzakciós vagy nem tranzakciós-e, és hogy az összetevő adott spout vagy bolt.

* ISCPSpout
* ISCPBolt
* ISCPTxSpout
* ISCPBatchBolt

### <a name="iscpplugin"></a>ISCPPlugin
ISCPPlugin az a közös felület különféle beépülő modulok esetében. Jelenleg egy helyőrző felületet.

    public interface ISCPPlugin 
    {
    }

### <a name="iscpspout"></a>ISCPSpout
ISCPSpout a nem tranzakcióalapú spout felületet.

     public interface ISCPSpout : ISCPPlugin                    
     {
         void NextTuple(Dictionary<string, Object> parms);         
         void Ack(long seqId, Dictionary<string, Object> parms);   
         void Fail(long seqId, Dictionary<string, Object> parms);  
     }

Amikor `NextTuple()` nevezzük, a C\# felhasználói kód kibocsátható egy vagy több rekord. Ha semmi sem kibocsátható, ez a módszer kibocsátó semmit nem kell visszaadnia. Érdemes megjegyezni, hogy `NextTuple()`, `Ack()`, és `Fail()` összes nevezzük az egyetlen szálból c. szoros hurokba\# folyamat. Vannak nem rekordok kibocsátható, esetén NextTuple alvó rendelkeznie rövid időn (például 10 ezredmásodperc) számára, hogy ne túl sok CPU pazarlom udvarias.

`Ack()` és `Fail()` csak engedélyezésekor ack mechanizmus dokumentumspecifikáció fájlban nevezzük. A `seqId` azonosítja a rekord, amely arra vonatkozik, vagy nem sikerült. Így ha ack nem tranzakciós topológiában engedélyezve van, a következő kibocsátása függvényt kell használni a Spout:

    public abstract void Emit(string streamId, List<object> values, long seqId); 

Ha ack nem támogatott nem tranzakciós topológiában a `Ack()` és `Fail()` is hagyható, az empty függvény.

A `parms` bemeneti paraméter ezeket a funkciókat az egy üres szótárban, a későbbi használatra van fenntartva.

### <a name="iscpbolt"></a>ISCPBolt
ISCPBolt a nem tranzakcióalapú bolt felületet.

    public interface ISCPBolt : ISCPPlugin 
    {
    void Execute(SCPTuple tuple);           
    }

Amikor új rekord érhető el, a `Execute()` függvény feldolgozni azt nevezzük.

### <a name="iscptxspout"></a>ISCPTxSpout
ISCPTxSpout a tranzakciós spout felületet.

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);  
        void Ack(long seqId, Dictionary<string, Object> parms);         
        void Fail(long seqId, Dictionary<string, Object> parms);        
    }

Akárcsak a nem tranzakcióalapú másik szerepét `NextTx()`, `Ack()`, és `Fail()` összes nevezzük az egyetlen szálból c. szoros hurokba\# folyamat. Ha nem állnak rendelkezésre adatok kibocsátható, hogy udvarias `NextTx` egy rövid időn (10 ezredmásodperc), hogy ne túl sok CPU pazarlom az alvó állapotba lépni.

`NextTx()` egy új tranzakciót az out paraméterben elindításához nevezzük `seqId` azonosítja a tranzakcióban, amelyet `Ack()` és `Fail()`. A `NextTx()`, felhasználói kibocsátható Java ügyféloldali adatok. Az adatok visszajátszását támogatásához ZooKeeper tárolja. ZooKeeper kapacitása korlátozott, mert a felhasználó kell csak gridre bocsáthatja ki az metaadatok, a tranzakciós spout adatok tömeges.

A Storm fog játszani automatikusan egy tranzakció, ha sikertelen, így `Fail()` normál esetben nem hívható. De ha a szolgáltatáskapcsolódási pont ellenőrizheti a metaadatok tranzakciós spout által kibocsátott, meghívhatja `Fail()` érvénytelen a metaadatok esetén.

A `parms` bemeneti paraméter ezeket a funkciókat az egy üres szótárban, a későbbi használatra van fenntartva.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt
ISCPBatchBolt a tranzakciós bolt felületet.

    public interface ISCPBatchBolt : ISCPPlugin           
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);  
    }

`Execute()` Ha a bolt érkező új rekord neve. `FinishBatch()` van meghívva, ha a tranzakció véget ér. A `parms` bemeneti paraméter későbbi használatra van fenntartva.

Tranzakciós topológia egyik fontos vonása – nincs `StormTxAttempt`. Rendelkezik két mezőt `TxId` és `AttemptId`. `TxId` egy adott tranzakció azonosítására szolgál, és egy adott tranzakció előfordulhat tett kísérletet a tranzakció sikertelen, és ha játssza vissza. SCP.NET új objektumot hoz létre ISCPBatchBolt feldolgozni az egyes `StormTxAttempt`hasonlóan Storm funkciója Java-környezetben. Ez a kialakítás az a célja, hogy támogatja a párhuzamos tranzakciókat feldolgozása. Felhasználói érdemes megtartani, vegye figyelembe, hogy ha a tranzakció kísérlet befejeződött, a megfelelő ISCPBatchBolt objektum megsemmisülésekor és szemétgyűjtés.

## <a name="object-model"></a>Hálózatiobjektum-modellje
SCP.NET is biztosít egy egyszerű objektumok a fejlesztők számára a program. Ezek **környezet**, **Állapottárolója**, és **SCPRuntime**. Ezek a többi részét ez a szakasz tárgyalja.

### <a name="context"></a>Környezet
Környezet az alkalmazás futó környezetet biztosít. Minden egyes ISCPPlugin példány (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) rendelkezik egy megfelelő környezet-példányt. A környezet által biztosított funkciókat két részre oszthatók: (1) a statikus része, amely a teljes C\# feldolgozásához, (2) a dinamikus része, amely csak az adott környezet példány érhető el.

### <a name="static-part"></a>Statikus része
    public static ILogger Logger = null;
    public static SCPPluginType pluginType;                      
    public static Config Config { get; set; }                    
    public static TopologyContext TopologyContext { get; set; }  

`Logger` naplózására van megadva.

`pluginType` a beépülő modul típusa a C jelzi\# folyamat. Ha a C\# folyamat (nélküli Java) tesztcélú helyi módban fut, a beépülő modul típusa `SCP_NET_LOCAL`.

    public enum SCPPluginType 
    {
        SCP_NET_LOCAL = 0,       
        SCP_NET_SPOUT = 1,       
        SCP_NET_BOLT = 2,        
        SCP_NET_TX_SPOUT = 3,   
        SCP_NET_BATCH_BOLT = 4  
    }

`Config` konfigurációs paraméterek beolvasni a Java ügyféloldali biztosítunk. A Java ügyféloldali átadott paraméterek amikor C\# beépülő modul inicializálása. A `Config` paraméterek két részre oszthatók: `stormConf` és `pluginConf`.

    public Dictionary<string, Object> stormConf { get; set; }  
    public Dictionary<string, Object> pluginConf { get; set; }  

`stormConf` a Storm által meghatározott paraméterek és `pluginConf` az az SCP által meghatározott paraméterek. Példa:

    public class Constants
    {
        … …

        // constant string for pluginConf
        public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

        // constant string for stormConf
        public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";           
        public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";                 
    }

`TopologyContext` biztosítja a topológia környezet lekéréséhez esetén a leghasznosabb összetevők több párhuzamosság együtt. Például:

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

### <a name="dynamic-part"></a>A dinamikus része
A következő felületek egységükre bizonyos helyi példányára. A helyi példány SCP.NET platform által létrehozott és a felhasználói kód átadott:

    // Declare the Output and Input Stream Schemas

    public void DeclareComponentSchema(ComponentStreamSchema schema);   

    // Emit tuple to default stream.
    public abstract void Emit(List<object> values);                   

    // Emit tuple to the specific stream.
    public abstract void Emit(string streamId, List<object> values);  

A nem tranzakcióalapú spout ack támogató a következő metódust áll rendelkezésre:

    // for non-transactional Spout which supports ack
    public abstract void Emit(string streamId, List<object> values, long seqId);  

Nem tranzakciós bolt ack támogató, az azt kell explicit módon `Ack()` vagy `Fail()` a rekord kapott. És amikor új rekord kibocsátó, meg kell adnia az új összetevője a központi jellegűek. A következő módszerek állnak rendelkezésre.

    public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values); 
    public abstract void Ack(SCPTuple tuple);
    public abstract void Fail(SCPTuple tuple);

### <a name="statestore"></a>StateStore
`StateStore` metaadat-szolgáltatások, a monoton sorozat mentén hozza. generációs és a várakozási ingyenes koordinációs biztosít. Elosztott feldolgozási magasabb szintű absztrakciók is épülő `StateStore`, beleértve az elosztott zárolásokat, elosztott várólisták, korlátok és tranzakciós szolgáltatások.

Szolgáltatáskapcsolódási pont alkalmazások használhatják a `State` objektum bizonyos információkat is tartalmaz [Apache ZooKeeper](https://zookeeper.apache.org/), különösen a tranzakciós topológia. Ennek tranzakciós spout összeomlik, és indítsa újra, ha a szükséges adatok lekérését ZooKeeper, és indítsa újra a folyamat során.

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

Az a `Commit()` módszer, ha simpleMode értéke igaz, akkor törli a megfelelő ZNode a ZooKeeper. Ellenkező esetben törli a jelenlegi ZNode, és a egy új csomópont hozzáadása a LEKÖTÖTT a\_elérési ÚTJA.

### <a name="scpruntime"></a>SCPRuntime
SCPRuntime az alábbi két módszer biztosítja:

    public static void Initialize();

    public static void LaunchPlugin(newSCPPlugin createDelegate);  

`Initialize()` a szolgáltatáskapcsolódási pont futásidejű környezet inicializálása szolgál. Ennél a módszernél a C\# folyamat a Java ügyféloldali csatlakozik, és lekéri a konfigurációs paramétereket és a környezeti topológia.

`LaunchPlugin()` a feldolgozási üzenetciklusa indíthat szolgál. Ez hurokba került, a C\# beépülő modul fogad üzeneteket űrlap Java ügyféloldali (beleértve a rekordok és -vezérlés jelek), és majd feldolgozása az üzeneteket, például a felület metódus meghívásának adja meg a felhasználói kód. A bemeneti paraméter metódus `LaunchPlugin()` egy meghatalmazott, amely ISCPSpout/IScpBolt/ISCPTxSpout ISCPBatchBolt felületet megvalósító objektum adhat vissza.

    public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary\<string, Object\> parms); 

A ISCPBatchBolt, kapunk `StormTxAttempt` a `parms`, és ennek segítségével megítélhető, hogy-e egy megismételt kísérlet után. A véglegesítési bolt gyakran teheti meg egy ismétlési kísérlet keresése, és azt mutatják be a `HelloWorldTx` példa.

A szolgáltatáskapcsolódási pont beépülő modulok általánosan fogalmazva, itt két módban futhat:

1. Tesztcélú helyi mód: Ebben a módban a szolgáltatáskapcsolódási pont beépülő modulok (a C\# felhasználói kód) Visual Studio rendszerén belül a fejlesztési fázis során futtassa. `LocalContext` Ebben a módban a kibocsátott rekordokat tartalmazó helyi fájlokba történő szerializálásához módszer, olvassa el őket vissza a memóriát biztosít használható.
   
        public interface ILocalContext
        {
            List\<SCPTuple\> RecvFromMsgQueue();
            void WriteMsgQueueToFile(string filepath, bool append = false);  
            void ReadFromFileToMsgQueue(string filepath);                    
        }
2. Normál módú: Ebben a módban a szolgáltatáskapcsolódási pont beépülő modulok storm java folyamat indul el.
   
    Íme egy példa SCP beépülő modul elindítása:
   
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

## <a name="topology-specification-language"></a>Topológia specifikációs nyelv
Szolgáltatáskapcsolódási pont topológia meghatározása az egy tartomány-specifikus nyelv, kivizsgáljuk a problémát, és az SCP topológiák konfigurálásához. A Storm Clojure DSL-alapú (<https://storm.incubator.apache.org/documentation/Clojure-DSL.html>) és az SCP bővített.

Közvetlenül a storm-fürt végrehajtási keresztül küldheti topológia specifikációk a ***runspec*** parancsot.

SCP.NET-e hozzáadni a következő funkciók tranzakciós topológiák megadása:

| **Új funkciók** | **Paraméterek** | **Leírás** |
| --- | --- | --- |
| **Tx-topolopy** |topológia – név<br />spout-map<br />bolt-map |Adja meg a topológia nevét, a tranzakciós topológiákat &nbsp;spoutok definíció térkép és a boltok definíció térkép |
| **scp-tx-spout** |exec-name<br />args<br />mezők |Adjon meg egy tranzakciós spout. Az alkalmazás futása ***exec-name*** használatával ***args***.<br /><br />A ***mezők*** spout kimeneti mezőjét van |
| **scp-tx-batch-bolt** |exec-name<br />args<br />mezők |Adjon meg egy tranzakciós Batch Bolt. Az alkalmazás futása ***exec-name*** használatával ***argumentum.***<br /><br />A mezők a bolt kimeneti mezőjét. |
| **scp-tx-commit-bolt** |exec-name<br />args<br />mezők |Adjon meg egy tranzakciós véglegesítési bolt. Az alkalmazás futása ***exec-name*** használatával ***args***.<br /><br />A ***mezők*** bolt kimeneti mezőjét van |
| **nontx-topolopy** |topológia – név<br />spout-map<br />bolt-map |Adja meg a topológia nevét, a tranzakciós topológiákat&nbsp; spoutok definíció térkép és a boltok definíció térkép |
| **scp-spout** |exec-name<br />args<br />mezők<br />paraméterek |Adjon meg egy nem tranzakciós spout. Az alkalmazás futása ***exec-name*** használatával ***args***.<br /><br />A ***mezők*** spout kimeneti mezőjét van<br /><br />A ***paraméterek*** megadása nem kötelező, és adja meg az egyes paraméterek, például a "nontransactional.ack.enabled". |
| **scp-bolt** |exec-name<br />args<br />mezők<br />paraméterek |Adjon meg egy nem tranzakciós Bolt. Az alkalmazás futása ***exec-name*** használatával ***args***.<br /><br />A ***mezők*** bolt kimeneti mezőjét van<br /><br />A ***paraméterek*** megadása nem kötelező, és adja meg az egyes paraméterek, például a "nontransactional.ack.enabled". |

SCP.NET van definiálva a következő kulcsszavakat:

| **a kulcsszavak** | **Leírás** |
| --- | --- |
| **: név** |Határozza meg topológia nevét |
| **: topológia** |Adja meg a topológia az előző függvények használatával, és készíthet azokról a. |
| **: p** |Adja meg az egyes spout vagy bolt a párhuzamosság mutató. |
| **:config** |Adja meg konfigurálja a paraméter vagy frissítse a meglévőket |
| **:schema** |Adja meg a Stream sémáját. |

És a gyakran használt paraméterek:

| **A paraméter** | **Leírás** |
| --- | --- |
| **"plugin.name"** |a C# beépülő modul exe fájl neve |
| **"plugin.args"** |beépülő modul argumentum |
| **"output.schema"** |Kimeneti sémája |
| **"nontransactional.ack.enabled"** |Nyugtázási engedélyezve van-e nem tranzakciós topológia |

A runspec parancs van telepítve, a bits együtt, például a használat értéke:

    .\bin\runSpec.cmd
    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
    ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target

A ***erőforrás-dir*** paraméter nem kötelező, meg kell adni, ha meg szeretné csatlakoztatni a C\# alkalmazáshoz, és ez a könyvtár tartalmazza az alkalmazást, a függőségeket és konfigurációkat.

A ***osztályútvonal*** paramétert is nem kötelező megadni. Adja meg a Java osztályútvonal, ha a konfigurációk fájl tartalmazza a Java Spout vagy Bolt szolgál.

## <a name="miscellaneous-features"></a>Egyéb szolgáltatások
### <a name="input-and-output-schema-declaration"></a>Bemeneti és kimeneti séma Deklarace
Felhasználók kibocsátható C lévő rekordokat\# folyamatok, a platform szerializálni a rekord be byte [], a Java ügyféloldali át kell, és a Storm a rekord átkerül a célokat. Eközben az alsóbb rétegbeli összetevők C\# folyamatok rekordokat tartalmazó vissza fogadását java ügyféloldali, illetve alakíthatja át az eredeti típusok platform, ezeket a műveleteket a Platform szerint el vannak rejtve.

Támogatja a szerializálást és deszerializálást, felhasználói kód kell a bemeneti és kimeneti sémája deklarálja.

A bemeneti és kimeneti stream séma számít, ha egy szótárban. A kulcs a StreamId a. Az oszlopok milyen típusú érték. Az összetevő deklarálni több adatfolyamot is rendelkezhet.

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


A Context objektumot a következő API-t hozzá van:

    public void DeclareComponentSchema(ComponentStreamSchema schema)

A fejlesztők biztosítania kell, hogy a rekordok felsorolásának kibocsátott betartani a sémát, hogy a stream definiált, ellenkező esetben a rendszer kivételt fogja kijelezni futásidejű kivételt.

### <a name="multi-stream-support"></a>Több Stream-támogatás
SCP-JE támogatja a felhasználói kód gridre bocsáthatja ki, vagy egyszerre több különböző Stream érkezik. A támogatást tükrözi a Context objektumot, a kibocsátása metódus egy nem kötelező stream azonosító paraméter szükséges.

Két módszer az SCP.NET Context objektumot hozzáadni. Generuje rekordot vagy rekordokat tartalmazó StreamId megadásához használhatók. A StreamId egy karakterláncot, és mindkét C konzisztensnek kell\# és a topológia definíciójának specifikációja.

        /* Emit tuple to the specific stream. */
        public abstract void Emit(string streamId, List<object> values);

        /* for non-transactional Spout only */
        public abstract void Emit(string streamId, List<object> values, long seqId);

Futásidejű kivételek a nem létező adatfolyam vezérlés okoz.

### <a name="fields-grouping"></a>Mezők csoportosítás
A beépített mezők csoportosítása a Storm az SCP.NET nem működik megfelelően. A Java-Proxy oldalon a mezők adattípusok ténylegesen byte [], és a csoportosítási mezők byte [] objektum kivonatkódját segítségével hajtsa végre a csoportosítást. A byte [] objektum kivonatoló kódot az a cím az objektum a memóriában. Ezért a csoportosítás két byte [] objektumok, amelyek ugyanahhoz a tartalomhoz, de nem ugyanazt a címet helytelen lesz.

SCP.NET hozzáad egy testre szabott csoportosítási módszert, és ehhez a csoportosítás használ a byte [] tartalmát. A **specifikációja** fájlt, a szintaxis hasonlít:

    (bolt-spec
        {
            "spout_test" (scp-field-group :non-tx [0,1])
        }
        …
    )


itt

1. "scp-mezőt – csoport" azt jelenti, hogy a "Testre szabott mező csoportosítási SCP által megvalósított".
2. ": tx"vagy": nem tx" azt jelenti, hogy a tranzakciós topológia esetén. Mivel a kezdőindex és a nem tx topológiák tx az eltérő kell ezt az információt.
3. [0,1] azt jelenti, hogy mező azonosítóval, 0-tól kezdődően kivonatoló készletét.

### <a name="hybrid-topology"></a>Hibrid topológia
A natív Storm Java nyelven van megírva. SCP.Net továbbfejlesztett C aktiválásához és\# fejlesztői írhatnak C\# kezelni a saját üzleti logika kódot. De is támogatja a hibrid topológiák, amely tartalmaz, nem csak a C\# spoutok és boltok, hanem a Java Spout/dolgozzák fel.

### <a name="specify-java-spoutbolt-in-spec-file"></a>Adja meg a Java Spout vagy Bolt dokumentumspecifikáció fájlban
Dokumentumspecifikáció fájlban "scp-spout" és "scp-bolt" is használható a Java Spoutok és boltok adja meg, például:

    (spout-spec 
      (microsoft.scp.example.HybridTopology.Generator.)           
      :p 1)

Itt `microsoft.scp.example.HybridTopology.Generator` a Java Spout osztály neve.

### <a name="specify-java-classpath-in-runspec-command"></a>Adja meg a Java Osztályútvonal runSpec parancs
Ha szeretné elküldeni a Java Spoutok és boltok tartalmazó topológia, először fordítsa le a Java Spoutok és boltok és a Jar-fájlok szüksége. Majd adjon meg topológia elküldésekor a Jar-fájlokat tartalmazó a java osztályútvonalába. Például:

    bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*

Itt **példák\\HybridTopology\\java\\cél\\**  a Java Spout vagy Bolt Jar-fájlt tartalmazó mappa.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Szerializálást és Deszerializálást között a Java- és C\#
SCP összetevő is tartalmaz, a Java és a C\# oldalán. Annak érdekében, hogy a natív Java Spoutok és boltok interakciót, szerializálás/deszerializálás el kell végezni között a Java és a C\# oldalán, az alábbi diagramon szemléltetett módon.

![java-összetevő küld a szolgáltatáskapcsolódási pont összetevő Java összetevő küldését bemutató ábra](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

1. **A Java ügyféloldali és a C deszerializálás szerializálási\# oldalán**
   
   Először alapértelmezett megvalósítása a Java ügyféloldali szerializálást és deszerializálást C nyelven biztosítunk\# oldalán. A szerializálási metódus, a Java ügyféloldali DOKUMENTUMSPECIFIKÁCIÓ fájlban adható meg:
   
       (scp-bolt
           {
               "plugin.name" "HybridTopology.exe"
               "plugin.args" ["displayer"]
               "output.schema" {}
               "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
           })
   
   A deszerializálás metódus a C\# oldalon meg kell határozni a C\# felhasználói kódban:
   
       Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
       inputSchema.Add("default", new List<Type>() { typeof(Person) });
       this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
       this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());            
   
   Alapértelmezett megvalósítása kezelje a legtöbb esetben a megadott adattípus nem túl bonyolult. Bizonyos esetekben, mert a felhasználói adatok típusa túl összetett, vagy mert alapértelmezett megvalósítása teljesítményét nem felel meg a felhasználói követelmény, a felhasználók is beépülő modul saját végrehajtása.
   
   A java ügyféloldali felület szerializálása határozza meg:
   
       public interface ICustomizedInteropJavaSerializer {
           public void prepare(String[] args);
           public List<ByteBuffer> serialize(List<Object> objectList);
       }
   
   A C deserialize felület\# ügyféloldali típusúként van definiálva:
   
   public interface ICustomizedInteropCSharpDeserializer
   
       public interface ICustomizedInteropCSharpDeserializer
       {
           List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
       }
2. **A C szerializálási\# ügyféloldali és a Java ügyféloldali deszerializálás**
   
   A szerializálási metódus a C\# oldalon meg kell határozni a C\# felhasználói kódban:
   
       this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
   
   A deszerializálás módszer a Java ügyféloldali DOKUMENTUMSPECIFIKÁCIÓ fájlban adható meg:
   
     (scp-spout
   
       {
         "plugin.name" "HybridTopology.exe"
         "plugin.args" ["generator"]
         "output.schema" {"default" ["person"]}
         "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
       })
   
   Itt "microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" deszerializáló nevét, és "microsoft.scp.example.HybridTopology.Person" a célosztály az adatokat a rendszer deszerializálni.
   
   Felhasználói is csatlakoztatható a saját C megvalósítását\# szerializáló és Java deszerializáló. Ez a kód az a felület, a c nyelvhez készült\# szerializáló:
   
       public interface ICustomizedInteropCSharpSerializer
       {
           List<byte[]> Serialize(List<object> dataList);
       }
   
   Ez a kód a Java-deszerializáló felülete:
   
       public interface ICustomizedInteropJavaDeserializer {
           public void prepare(String[] targetClassNames);
           public List<Object> Deserialize(List<ByteBuffer> dataList);
       }

## <a name="scp-host-mode"></a>SCP Host Mode
Ebben a módban a kódokat a dll-fájl összeállításához, és a felhasználónak topológia küldhetnek a szolgáltatáskapcsolódási pont által biztosított SCPHost.exe használatával. A konfigurációk fájlt ehhez a kódhoz hasonlóan néz ki:

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

Itt `plugin.name` megadott `SCPHost.exe` SCP SDK által biztosított. SCPHost.exe három paramétert fogad el:

1. Az első rekordon a dll-fájl neve, amely `"HelloWorld.dll"` ebben a példában.
2. A rendszer az osztály neve, amely `"Scp.App.HelloWorld.Generator"` ebben a példában.
3. A harmadik egyik nyilvános statikus metódus, amely ISCPPlugin egy példányát is lekérhető az neve lesz.

Állomás üzemmódban a felhasználói kód lefordított DLL-t, és SCP platform hív. Ezért SCP platform kérheti le a teljes feldolgozási logikáját teljes hozzáférés. Ezért javasoljuk, hogy ügyfeleink számára, hogy küldje el a topológia SCP állomás üzemmódban, mivel a fejlesztői élmény egyszerűsítése érdekében és velünk a kapcsolatot, valamint az újabb kiadás használata nagyobb rugalmasságot és hatékonyabb előző verziókkal való kompatibilitás.

## <a name="scp-programming-examples"></a>Szolgáltatáskapcsolódási pont programozási példák
### <a name="helloworld"></a>HelloWorld
**HelloWorld** egy egyszerű példa az SCP.Net kipróbálni megjelenítéséhez. Nem tranzakciós topológia, használja a spout nevű **generátor**, és két boltok nevű **felosztó** és **számláló**. A spout **generátor** véletlenszerűen állít elő mondatokat, és ezeket a mondatok kibocsátható **felosztó**. A bolt **felosztó** bontja a mondatok szavakat, és gridre bocsáthatja ki az ezeknek a szavaknak **számláló** bolt. A bolt "számláló" egy szótár segítségével rögzítheti az egyes szavak előfordulását számát.

Két dokumentumspecifikáció fájl, **HelloWorld.spec** és **HelloWorld\_EnableAck.spec** ebben a példában. A c\# kódot, azt talál ack engedélyezve van-e a Java-oldaláról a pluginConf lekérésével.

    /* demo how to get pluginConf info */
    if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
    {
        enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
    }
    Context.Logger.Info("enableAck: {0}", enableAck);

A spout a ack engedélyezve van, ha egy szótár segítségével gyorsítótárazzák a rekordokat, amelyek nem nyugtázták. Ha Fail() nevezzük, akkor a hibás rekord játssza vissza:

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
A **HelloWorldTx** példa bemutatja, hogyan valósíthat meg topológia tranzakciós. Rendelkezik egy spout nevű **generátor**, egy batch bolt nevű **részleges-count**, és a egy véglegesítési bolt **számláló – összeg**. Még nincsenek három előre létrehozott txt-fájlokat: **DataSource0.txt**, **DataSource1.txt**, és **DataSource2.txt**.

Az egyes tranzakciók, a spout **generátor** véletlenszerűen kiválaszt két fájlt az előre elkészített három fájlt, és gridre bocsáthatja ki a két nevet, hogy a **részleges-count** bolt. A bolt **részleges-count** beolvassa a fájl nevezze el a kapott rekord, a majd nyissa meg a fájlt, és ezt a fájlt a szavak számát, és végül küldik a word szám, a **számláló – összeg** bolt. A **számláló – összeg** bolt foglalja össze a teljes száma.

Eléréséhez **pontosan egyszer** szemantikáját, a véglegesítés bolt **számláló – összeg** értékelhető verseny, akár egy megismételt tranzakció szükség. Ebben a példában egy statikus tag változóban van:

    public static long lastCommittedTxId = -1; 

ISCPBatchBolt példány jön létre, amikor a `txAttempt` bemeneti paraméterek közül:

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

Amikor `FinishBatch()` nevezzük, a `lastCommittedTxId` frissül, ha nem egy megismételt tranzakció.

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
Ez a topológia tartalmaz egy Java-Spout és a egy C\# Bolt. Akkor használja, SCP platform által biztosított alapértelmezett szerializálást és deszerializálást megvalósítása. Tekintse meg a **HybridTopology.spec** a **példák\\HybridTopology** mappáját a dokumentumspecifikáció fájl részleteit, és **SubmitTopology.bat** for Java megadása osztályútvonalába.

### <a name="scphostdemo"></a>SCPHostDemo
Ebben a példában megegyezik a HelloWorld lényegében. Az egyetlen különbség, hogy a felhasználói kód lefordított DLL-t, és a topológia elküldésekor SCPHost.exe használatával. Részletes ismertetése "SCP állomás módban" című szakaszában talál.

## <a name="next-steps"></a>További lépések
Apache Storm-topológiák SCP-je használatával létrehozott példákért lásd az alábbi dokumentumokat:

* [Az Apache Storm on HDInsight Visual Studio használatával C#-topológiák fejlesztése](apache-storm-develop-csharp-visual-studio-topology.md)
* [Események feldolgozása az Azure Event hubs Eseményközpontokból a HDInsight-alapú Apache Storm](apache-storm-develop-csharp-event-hub-topology.md)
* [Járműérzékelő-adatok feldolgozása az Event hubs Eseményközpontokból a HDInsight Apache Storm használatával](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Kinyerés, átalakítás és betöltés (ETL) az Azure Event Hubs, az Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
