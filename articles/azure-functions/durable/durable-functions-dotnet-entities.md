---
title: Fejlesztői útmutató a tartós entitásokhoz a .NET - Azure Functions szolgáltatásban
description: Hogyan működik a tartós entitások a .NET a Durable Functions bővítmény az Azure Functions.
author: sebastianburckhardt
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: azfuncdf
ms.openlocfilehash: 01e07eaee705634b03cc4462c4058e290daa8bc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278128"
---
# <a name="developers-guide-to-durable-entities-in-net"></a>Fejlesztői útmutató a .NET tartós entitásaihoz

Ebben a cikkben részletesen ismertetjük a .NET-mel rendelkező tartós entitások fejlesztéséhez rendelkezésre álló felületeket, példákkal és általános tanácsokkal. 

Az entitásfüggvények kényelmes módot biztosítanak a kiszolgáló nélküli alkalmazásfejlesztők számára az alkalmazásállapot részletes entitások gyűjteményeként történő rendszerezésére. Az alapul szolgáló fogalmakról további részleteket a [Tartós entitások: Fogalmak](durable-functions-entities.md) című cikkben olvashat.

Jelenleg két API-t kínálunk az entitások meghatározásához:

- Az **osztályalapú szintaxis** osztályokként és metódusként jelöli az entitásokat és műveleteket. Ez a szintaxis könnyen olvasható kódot eredményez, és lehetővé teszi a műveletek típus-ellenőrzött módon történő meghívását az interfészeken keresztül. 

- A **függvényalapú szintaxis** egy alacsonyabb szintű felület, amely függvényként jelöli az entitásokat. Pontos ellenőrzést biztosít az entitásműveletek feladása és az entitásállapot kezelésének módját.  

Ez a cikk elsősorban az osztályalapú szintaxisra összpontosít, mivel elvárjuk, hogy a legtöbb alkalmazás számára jobban megfeleljen. A [függvényalapú szintaxis](#function-based-syntax) azonban megfelelő lehet olyan alkalmazások esetében, amelyek saját absztrakcióikat kívánják meghatározni vagy kezelni az entitásállapotés műveletek esetében. Az is megfelelő lehet olyan tárak megvalósításához, amelyek általánosságát jelenleg nem támogatja az osztályalapú szintaxis. 

> [!NOTE]
> Az osztályalapú szintaxis csak egy réteg a függvényalapú szintaxis tetején, így mindkét változat szinonimaként használható ugyanabban az alkalmazásban. 
 
## <a name="defining-entity-classes"></a>Entitásosztályok meghatározása

A következő példa egy `Counter` olyan entitás implementációja, amely egyetlen egész típusú `Add`értéket `Reset` `Get`tárol, és négy műveletet kínál , , és `Delete`.

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int Value { get; set; }

    public void Add(int amount) 
    {
        this.Value += amount;
    }

    public Task Reset() 
    {
        this.Value = 0;
        return Task.CompletedTask;
    }

    public Task<int> Get() 
    {
        return Task.FromResult(this.Value);
    }

    public void Delete() 
    {
        Entity.Current.DeleteState();
    }

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

A `Run` funkció tartalmazza az osztályalapú szintaxis használatához szükséges sablont. Statikus *static* Azure-függvénynek kell lennie. Az entitás által feldolgozott minden egyes műveletüzenethez egyszer hajt végre. Amikor `DispatchAsync<T>` a neve, és az entitás még nem a memóriában, létrehoz egy objektum típusú, `T` és feltölti a mezőket az utolsó megőrzött JSON található tároló (ha van ilyen). Ezután meghívja a metódust a megfelelő névvel.

> [!NOTE]
> Az osztályalapú entitás állapota **implicit módon jön létre,** mielőtt az entitás feldolgozna egy `Entity.Current.DeleteState()`műveletet, és egy műveletben explicit módon **törölhető** a hívással.

### <a name="class-requirements"></a>Osztálykövetelmények
 
Az entitásosztályok olyan POCOs -ok (egyszerű régi CLR-objektumok), amelyek nem igényelnek speciális szuperosztályokat, felületeket vagy attribútumokat. Azonban:

- Az osztálynak kivitelezhetőnek kell lennie [(lásd: Entitás építése](#entity-construction)).
- Az osztálynak JSON-szerializálhatónak kell lennie [(lásd: Entitás szerializálás ).](#entity-serialization)

Továbbá minden olyan módszernek, amelyet műveletként kívánnak meghívni, további követelményeknek is meg kell felelnie:

- Egy műveletnek legfeljebb egy argumentumkal kell rendelkeznie, és nem lehetnek túlterhelések vagy általános típusargumentumok.
- Egy felületet használó vezénylési `Task` műveletnek vissza kell adnia a vagya. `Task<T>`
- Az argumentumok és a visszatérési értékek szerializálható értékek vagy objektumok.

### <a name="what-can-operations-do"></a>Mire képesek a műveletek?

Minden entitásművelet képes olvasni és frissíteni az entitás állapotát, és az állapot módosításai automatikusan megőrződnek a tárolóban. Ezenkívül a műveletek külső I/O-vagy más számításokat is végrehajthatnak az összes Azure-függvény közös általános korlátain belül.

A műveletek a `Entity.Current` környezet által biztosított funkciókhoz is hozzáférnek:

* `EntityName`: a jelenleg végrehajtó entitás neve.
* `EntityKey`: a jelenleg végrehajtó entitás kulcsa.
* `EntityId`: a jelenleg végrehajtó entitás azonosítója (tartalmazza a nevet és a kulcsot).
* `SignalEntity`: egyirányú üzenetet küld egy entitásnak.
* `CreateNewOrchestration`: új vezénylést indít.
* `DeleteState`: törli az entitás állapotát.

Módosíthatjuk például a számláló entitást, így elindít egy vezénylést, amikor a számláló eléri a 100-at, és bemeneti argumentumként adja át az entitásazonosítót:

```csharp
    public void Add(int amount) 
    {
        if (this.Value < 100 && this.Value + amount > 100)
        {
            Entity.Current.StartNewOrchestration("MilestoneReached", Entity.Current.EntityId)
        }
        this.Value += amount;      
    }
```

## <a name="accessing-entities-directly"></a>Az entitások közvetlen elérése

Az osztályalapú entitások közvetlenül is elérhetők, az entitás és műveletei explicit karakterláncneveinek használatával. Az alábbiakban néhány példát mutatunk be; az alapul szolgáló fogalmak (például a jelek és a hívások) mélyebb magyarázatát lásd az [Access entitások](durable-functions-entities.md#access-entities)című témakörben. 

> [!NOTE]
> Ahol lehetséges, azt javasoljuk, hogy [az entitásokat felületeken keresztül érje el,](#accessing-entities-through-interfaces)mert több típusellenőrzést biztosít.

### <a name="example-client-signals-entity"></a>Példa: ügyféljelek entitás

A következő Azure Http függvény rest-konvenciók használatával implementálja a DELETE műveletet. Törlési jelet küld annak a számlálóentitásnak, amelynek kulcsa átkerül az URL-elérési úton.

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync(entityId, "Delete");    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

### <a name="example-client-reads-entity-state"></a>Példa: az ügyfél beolvassa az entitás állapotát

A következő Azure Http függvény rest-konvenciók használatával valósítja meg a GET-műveletet. Beolvassa annak a számlálóentitásnak az aktuális állapotát, amelynek kulcsa átkerül az URL-elérési úton.

```csharp
[FunctionName("GetCounter")]
public static async Task<HttpResponseMessage> GetCounter(
    [HttpTrigger(AuthorizationLevel.Function, "get", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    var state = await client.ReadEntityStateAsync<Counter>(entityId); 
    return req.CreateResponse(state);
}
```

> [!NOTE]
> A visszaadott `ReadEntityStateAsync` objektum csak egy helyi példányt, azaz egy pillanatképet az entitás állapota egy korábbi időpontban. Különösen lehet elavult, és az objektum módosítása nincs hatással a tényleges entitásra. 

### <a name="example-orchestration-first-signals-then-calls-entity"></a>Példa: vezénylési először jelek, majd kéri entitás

A következő vezénylési jelzi a számláló entitás növekmény, és ezután meghívja ugyanazt az entitást, hogy olvassa el a legújabb értéket.

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");

    // One-way signal to the entity - does not await a response
    context.SignalEntity(entityId, "Add", 1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");

    return currentValue;
}
```

## <a name="accessing-entities-through-interfaces"></a>Entitások elérése összeköttetéseken keresztül

A felületek segítségével entitásokat érhet el létrehozott proxyobjektumokon keresztül. Ez a megközelítés biztosítja, hogy a művelet neve és argumentumtípusa megegyezik a megvalósítottművelettel. Azt javasoljuk, hogy az entitások eléréséhez lehetőség szerint használjon felületeket.

Például a következőképpen módosíthatjuk a számláló példát:

```csharp
public interface ICounter
{
    void Add(int amount);
    Task Reset();
    Task<int> Get();
    void Delete();
}

public class Counter : ICounter
{
    ...
}
```

Az entitásosztályok és az entitásinterfészek hasonlóak az [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/)által népszerűsített szemcsékhez és gabonafelületekhez. A tartós entitások és orleansi entitások közötti hasonlóságokról és különbségekről a [Virtuális szereplőkkel való összehasonlítás](durable-functions-entities.md#comparison-with-virtual-actors)című témakörben talál további információt.

A típusellenőrzés mellett a kapcsolódási pontok hasznosak az alkalmazáson belüli aggályok jobb elkülönítése érdekében. Például, mivel egy entitás több felületet is megvalósíthat, egyetlen entitás több szerepkört is kiszolgálhat. Továbbá, mivel egy felületet több entitás is megvalósíthat, az általános kommunikációs minták újrafelhasználható kódtárakként is megvalósíthatók.

### <a name="example-client-signals-entity-through-interface"></a>Példa: ügyfél jelek entitás felületen keresztül

Az ügyfélkód `SignalEntityAsync<TEntityInterface>` segítségével jeleket küldhet `TEntityInterface`a megvalósító entitásoknak. Példa:

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync<ICounter>(entityId, proxy => proxy.Delete());    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

Ebben a példában a `proxy` paraméter a dinamikusan generált példánya, `ICounter`amely `Delete` belsőleg fordítja a hívást egy jel.

> [!NOTE]
> Az `SignalEntityAsync` API-k csak egyirányú műveletekhez használhatók. Még ha egy `Task<T>`művelet függvényt `T` is visszaad, a paraméter értéke mindig null lesz, vagy `default`a tényleges eredmény.
Például nincs értelme jelezni a `Get` műveletet, mivel nem ad vissza értéket. Ehelyett az ügyfelek `ReadStateAsync` használhatják vagy a számláló állapotának közvetlen eléréséhez, `Get` vagy elindíthatnak egy orchestrator függvényt, amely meghívja a műveletet. 

### <a name="example-orchestration-first-signals-then-calls-entity-through-proxy"></a>Példa: vezénylési először jeleket, majd meghívja entitás proxy

Egy entitás hívása vagy jelzése `CreateEntityProxy` egy vezénylési területen belül, a kapcsolat típusával együtt proxy létrehozásához használható az entitás számára. Ez a proxy ezután használható hívás vagy jel műveletek:

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");
    var proxy = context.CreateEntityProxy<ICounter>(entityId);

    // One-way signal to the entity - does not await a response
    proxy.Add(1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await proxy.Get();

    return currentValue;
}
```

Implicit módon a visszaküldött `void` műveletek et jelzi, és `Task` minden `Task<T>` olyan műveletet, amely visszatér vagy megvan hívva. Lehet változtatni ezt az alapértelmezett viselkedést, és a jel műveletek `SignalEntity<IInterfaceType>` akkor is, ha vissza Task, a módszer használatával explicit módon.

### <a name="shorter-option-for-specifying-the-target"></a>Rövidebb beállítás a cél megadásához

Ha egy entitást interfésszel hív vagy jelez, az első argumentumnak meg kell adnia a célentitást. A cél megadható az entitásazonosító megadásával, vagy olyan esetekben, amikor csak egy osztály valósítja meg az entitást, csak az entitáskulcsot:

```csharp
context.SignalEntity<ICounter>(new EntityId(nameof(Counter), "myCounter"), ...);
context.SignalEntity<ICounter>("myCounter", ...);
```

Ha csak az entitáskulcs van megadva, és egy egyedi `InvalidOperationException` megvalósítás nem található futásidőben, a rendszer eldobja. 

### <a name="restrictions-on-entity-interfaces"></a>Az entitásillesztések korlátozásai

A szokásos módon minden paraméter- és visszatérési típusnak JSON-szerializálhatónak kell lennie. Ellenkező esetben a szerializálási kivételek futásidőben kerülnek kidobásra.

További szabályokat is betartatunk:
* Az entitásillesztések csak metódusokat határoznak meg.
* Az entitásfelületek nem tartalmazhatnak általános paramétereket.
* Az entitásillesztő metódusai nem rendelkezhetnek egynél több paraméterrel.
* Az entitásillesztő `void` `Task`metódusainak vissza kell térniük , vagy`Task<T>` 

Ha a szabályok bármelyikét `InvalidOperationException` megsértik, a rendszer futásidőben eldobja az `SignalEntity` `CreateProxy`at, amikor a felületet a vagya típusargumentumaként használják. A kivételüzenet ismerteti, hogy melyik szabály sérült.

> [!NOTE]
> A visszaküldött `void` illesztőfelületi metódusok csak jelzésre (egyirányúak), nem hívhatók (kétirányúak). A felületi módszerek visszatérnek, `Task` vagy `Task<T>` meghívhatók vagy jelezhetők. Ha hívják, akkor vissza adja a művelet eredményét, vagy újra dobja a művelet által okozott kivételeket. Ha azonban jelzésre van jelezve, nem a tényleges eredményt vagy kivételt adják vissza a műveletből, hanem csak az alapértelmezett értéket.

## <a name="entity-serialization"></a>Entitás szerializálása

Mivel egy entitás állapota tartósan megmarad, az entitásosztálynak szerializálhatónak kell lennie. A Durable Functions futásidejű használja a [Json.NET](https://www.newtonsoft.com/json) könyvtár erre a célra, amely támogatja a számos házirendek és attribútumok ellenőrzésére a szerializálási és deszerializálási folyamat. A leggyakrabban használt C# adattípusok (beleértve a tömböket és a gyűjteménytípusokat) már szerializálhatók, és könnyen használhatók a tartós entitások állapotának meghatározásához.

A Json.NET például könnyen szerializálhatja és deszerializálhatja a következő osztályt:

```csharp
[JsonObject(MemberSerialization = MemberSerialization.OptIn)]
public class User
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("yearOfBirth")]
    public int YearOfBirth { get; set; }

    [JsonProperty("timestamp")]
    public DateTime Timestamp { get; set; }

    [JsonProperty("contacts")]
    public Dictionary<Guid, Contact> Contacts { get; set; } = new Dictionary<Guid, Contact>();

    [JsonObject(MemberSerialization = MemberSerialization.OptOut)]
    public struct Contact
    {
        public string Name;
        public string Number;
    }

    ...
}
```

### <a name="serialization-attributes"></a>Szerializálási attribútumok

A fenti példában úgy döntöttünk, hogy több attribútumot is felvesszük, hogy az alapul szolgáló szerializálás láthatóbbá váljon:
- Mi jegyzetekkel `[JsonObject(MemberSerialization.OptIn)]` az osztály, hogy emlékeztessen minket, hogy az osztály kell szerializálható, és továbbra is csak a tagok, amelyek kifejezetten jelölt JSON tulajdonságait.
-  Jegyzetekkel látjuk el a megtartandó `[JsonProperty("name")]` mezőket, hogy emlékeztessen minket arra, hogy egy mező a megőrzött entitásállapot része, és megadjuk a JSON-ábrázolásban használandó tulajdonságnevet.

Ezek az attribútumok azonban nem szükségesek; más konvenciók vagy attribútumok megengedettek, amennyiben Json.NET. Használhatunk például `[DataContract]` attribútumokat, vagy egyáltalán nem lehet attribútumot használni:

```csharp
[DataContract]
public class Counter
{
    [DataMember]
    public int Value { get; set; }
    ...
}

public class Counter
{
    public int Value;
    ...
}
```

Alapértelmezés szerint az osztály neve *nem* tárolja a JSON-ábrázolás részeként: `TypeNameHandling.None` azaz alapértelmezett beállításként használjuk. Ez az alapértelmezett viselkedés felülbírálható `JsonProperty` az attribútumok használatával vagy használatával. `JsonObject`

### <a name="making-changes-to-class-definitions"></a>Az osztálydefiníciók módosítása

Némi gondossággal kell eljárni, ha egy alkalmazás futtatása után módosítja az osztálydefiníciót, mert előfordulhat, hogy a tárolt JSON-objektum már nem felel meg az új osztálydefiníciónak. Mégis, gyakran lehet helyesen kezelni a változó adatformátumokat, amíg az ember megérti `JsonConvert.PopulateObject`a deszerializálási folyamat által használt .

Íme például néhány példa a változásokra és azok hatására:

1. Ha új tulajdonságot ad hozzá, amely nincs jelen a tárolt JSON-ban, akkor az alapértelmezett értéket veszi fel.
1. Ha eltávolít egy tulajdonságot, amely a tárolt JSON-ban található, az előző tartalom elvész.
1. Ha egy tulajdonság ot átnevez, a hatás olyan, mintha eltávolítaná a régit, és újat adn a hozzá.
1. Ha egy tulajdonság típusa megváltozik, így a tárolt JSON-ból már nem lehet deszerializálható, kivétel történik.
1. Ha egy tulajdonság típusa megváltozik, de a tárolt JSON-ból továbbra is deszerializálható, akkor ezt fogja tenni.

Számos lehetőség áll rendelkezésre a Json.NET viselkedésének testreszabására. Ha például egy kivételt szeretne kényszeríteni, ha a tárolt JSON olyan mezőt `JsonObject(MissingMemberHandling = MissingMemberHandling.Error)`tartalmaz, amely nem szerepel az osztályban, adja meg az attribútumot. Lehetőség van egyéni kód írására is a deszerializáláshoz, amely tetszőleges formátumban tárolt JSON-t képes olvasni.

## <a name="entity-construction"></a>Entitás építése

Néha azt szeretnénk, hogy gyakoroljon több ellenőrzést, hogyan entitás objektumok épülnek. Most antól számos lehetőséget ismertetünk az alapértelmezett viselkedés módosítására az entitásobjektumok összeállításakor. 

### <a name="custom-initialization-on-first-access"></a>Egyéni inicializálás az első hozzáféréskor

Esetenként speciális inicializálást kell végrehajtanunk, mielőtt egy műveletet olyan entitásnak küldenénk, amelyhez soha nem volt hozzáférés, vagy amelyet töröltek. A viselkedés megadásához feltételest adhat `DispatchAsync`hozzá a következőhöz:

```csharp
[FunctionName(nameof(Counter))]
public static Task Run([EntityTrigger] IDurableEntityContext ctx)
{
    if (!ctx.HasState)
    {
        ctx.SetState(...);
    }
    return ctx.DispatchAsync<Counter>();
}
```

### <a name="bindings-in-entity-classes"></a>Kötések entitásosztályokban

A rendszeres függvényekkel ellentétben az entitásosztály metódusai nem rendelkeznek közvetlen hozzáféréssel a bemeneti és kimeneti kötésekhez. Ehelyett a kötési adatokat rögzíteni kell a belépési pont `DispatchAsync<T>` függvénydeklarációban, majd át kell adni a metódusnak. Az átadott `DispatchAsync<T>` objektumok argumentumként automatikusan átkerülnek az entitásosztály konstruktorába.

A következő példa `CloudBlobContainer` bemutatja, hogyan lehet egy hivatkozást a [blob bemeneti kötés](../functions-bindings-storage-blob-input.md) érhető el egy osztály-alapú entitás.

```csharp
public class BlobBackedEntity
{
    [JsonIgnore]
    private readonly CloudBlobContainer container;

    public BlobBackedEntity(CloudBlobContainer container)
    {
        this.container = container;
    }

    // ... entity methods can use this.container in their implementations ...
    
    [FunctionName(nameof(BlobBackedEntity))]
    public static Task Run(
        [EntityTrigger] IDurableEntityContext context,
        [Blob("my-container", FileAccess.Read)] CloudBlobContainer container)
    {
        // passing the binding object as a parameter makes it available to the
        // entity class constructor
        return context.DispatchAsync<BlobBackedEntity>(container);
    }
}
```

Az Azure Functions kötéseiről az [Azure Functions eseményindítók és kötések](../functions-triggers-bindings.md) dokumentációjában olvashat bővebben.

### <a name="dependency-injection-in-entity-classes"></a>Függőségi injektálás entitásosztályokban

Az entitásosztályok támogatják [az Azure Functions függőségi injektálást.](../functions-dotnet-dependency-injection.md) A következő példa bemutatja, `IHttpClientFactory` hogyan regisztrálhat egy szolgáltatást egy osztályalapú entitásba.

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
        }
    }
}
```

A következő kódrészlet bemutatja, hogyan építheti be az injektált szolgáltatást az entitásosztályba.

```csharp
public class HttpEntity
{
    [JsonIgnore]
    private readonly HttpClient client;

    public class HttpEntity(IHttpClientFactory factory)
    {
        this.client = factory.CreateClient();
    }

    public Task<int> GetAsync(string url)
    {
        using (var response = await this.client.GetAsync(url))
        {
            return (int)response.StatusCode;
        }
    }

    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> A szerializálással kapcsolatos problémák elkerülése érdekében zárja ki a szerializálásból a befecskendezett értékek tárolására szánt mezőket.

> [!NOTE]
> Ellentétben a konstruktor-injektálás rendszeres .NET Azure Functions használatával, `static`az osztályalapú entitások függvénybelépési pont módszerét deklarálni *kell.* Deklarálása nem statikus függvény belépési pont ütközéseket okozhat a normál Azure Functions objektum inicializáló és a tartós entitások objektum inicializáló.

## <a name="function-based-syntax"></a>Függvényalapú szintaxis

Eddig az osztályalapú szintaxisra összpontosítottunk, mivel elvárjuk, hogy a legtöbb alkalmazáshoz jobban megfeleljen. A függvényalapú szintaxis azonban megfelelő lehet olyan alkalmazások hoz, amelyek saját absztrakcióikat szeretnék meghatározni vagy kezelni az entitásállapothoz és a műveletekhez. Az általános ságot igénylő, az osztályalapú szintaxis által jelenleg nem támogatott tárak megvalósításakor is helyénvaló lehet. 

A függvényalapú szintaxissal az Entitás függvény explicit módon kezeli a művelet feladása, és explicit módon kezeli az entitás állapotát. A következő kód például a függvényalapú szintaxissal megvalósított *Számláló* entitást jeleníti meg.  

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>()));
            break;
        case "delete":
            ctx.DeleteState();
            break;
    }
}
```

### <a name="the-entity-context-object"></a>Az entitás környezetobjektuma

Az entitásspecifikus funkciók egy típusú `IDurableEntityContext`környezetobjektumon keresztül érhetők el. Ez a környezeti objektum az entitásfüggvény paramétereként és az `Entity.Current`aszinkron-helyi tulajdonságon keresztül érhető el.

A következő tagok információt nyújtanak az aktuális műveletről, és lehetővé teszik számunkra, hogy megadjuk a visszatérési értéket. 

* `EntityName`: a jelenleg végrehajtó entitás neve.
* `EntityKey`: a jelenleg végrehajtó entitás kulcsa.
* `EntityId`: a jelenleg végrehajtó entitás azonosítója (tartalmazza a nevet és a kulcsot).
* `OperationName`: az aktuális művelet neve.
* `GetInput<TInput>()`: lekéri az aktuális művelet bemenetét.
* `Return(arg)`: visszaad egy értéket a műveletnek nevezett vezénylési műveletnek.

A következő tagok kezelik az entitás állapotát (létrehozás, olvasás, frissítés, törlés). 

* `HasState`: az entitás nak van-e valamilyen állapota. 
* `GetState<TState>()`: az entitás aktuális állapotát kapja. Ha még nem létezik, akkor létrejön.
* `SetState(arg)`: létrehozza vagy frissíti az entitás állapotát.
* `DeleteState()`: törli az entitás állapotát, ha létezik. 

Ha a visszaadott `GetState` állapot egy objektum, akkor közvetlenül módosítható az alkalmazáskóddal. Nem kell újra `SetState` hívni a végén (de nem árt). Ha `GetState<TState>` többször is meghívják, ugyanazt a típust kell használni.

Végül a következő tagok jeleznek más entitások, vagy új vezénylések indítására:

* `SignalEntity(EntityId, operation, input)`: egyirányú üzenetet küld egy entitásnak.
* `CreateNewOrchestration(orchestratorFunctionName, input)`: új vezénylést indít.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ az entitásfogalmakról](durable-functions-entities.md)
