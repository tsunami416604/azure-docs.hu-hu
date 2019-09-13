---
title: Tartós entitások – Azure Functions
description: Ismerje meg, mi a tartós entitások és hogyan használhatók a Azure Functions Durable Functions-bővítményében.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: 419505404214d60a2c928770a34384f80c8446cb
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935872"
---
# <a name="entity-functions-preview"></a>Entity functions (előzetes verzió)

Az Entity functions olyan műveleteket határoz meg, amelyek olyan kis méretű állapotok olvasására és frissítésére szolgálnak, amelyek *tartós entitások*. A Orchestrator függvényekhez hasonlóan az Entity functions is egy speciális trigger típussal, az *entitások triggerével*működik. A Orchestrator függvényektől eltérően az Entity functions nem rendelkezik konkrét kód megkötésekkel. Az Entity functions emellett explicit módon kezeli az állapotot, nem pedig implicit módon jelképezi az állapotot a vezérlési folyamaton keresztül.

> [!NOTE]
> Az Entity functions és a kapcsolódó funkciók csak Durable Functions 2,0-es és újabb verziókban érhetők el. Az Entity functions szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.

## <a name="entity-identity"></a>Entitás identitása

Az entitások (más néven Entity *instances*) egy egyedi azonosítóval, az *entitás azonosítójának*használatával érhetők el. Az entitás-AZONOSÍTÓk egyszerűen olyan karakterláncok, amelyek egyedileg azonosítanak egy entitás-példányt. A következőkből áll:

* Az **entitás neve**: az entitás típusát azonosító név (például "számláló").
* **Entitás kulcsa**: olyan karakterlánc, amely egyedileg azonosítja az entitást az azonos nevű entitások között (például egy GUID azonosító).

Előfordulhat például, hogy egy *számláló* entitás függvényt egy online játékban kell tartani. A játék minden példánya egyedi azonosítóval fog rendelkezni, például `@Counter@Game1`, `@Counter@Game2`, stb. Egy adott entitást megcélzó összes művelethez meg kell adni egy entitás AZONOSÍTÓját paraméterként.

## <a name="programing-models"></a>Programozási modellek

A tartós entitások két különböző programozási modellt támogatnak. Az első modell egy dinamikus "funkcionális" modell, amelyben az entitás egyetlen függvénnyel van definiálva. A második modell egy objektumorientált modell, amelyben az entitást osztály és módszerek határozzák meg. Ezeket a modelleket és az entitásokkal való interakció programozási modelljét a következő szakaszokban ismertetjük.

### <a name="defining-entities"></a>Entitások definiálása

A tartós entitások létrehozásához két választható programozási modell szükséges. A következő kód egy egyszerű, szabványos függvényként megvalósított számlálós entitásra mutat példát. Ez a függvény három *műveletet* `add` `reset`határoz meg:, `get`és, amelyek `currentValue`mindegyike egész állapot értékre van kialakítva.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += operand;
            break;
        case "reset":
            currentValue = 0;
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }

    ctx.SetState(currentValue);
}
```

Ez a modell az egyszerű entitások megvalósításához, illetve dinamikus műveletekkel rendelkező megvalósításokhoz használható. Használhat azonban olyan osztály alapú programozási modellt is, amely olyan entitások esetében hasznos, amelyek statikusak, de összetettebb implementációval rendelkeznek. A következő példa az `Counter` entitás egyenértékű implementációját osztályok és metódusok használatával.

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;
    
    public void Reset() => this.CurrentValue = 0;
    
    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

> [!NOTE]
> A függvény belépési pontjának `[FunctionName]` metódusát *az attribútummal* deklarálni `static` kell az Entity classs használatakor. A nem statikus belépési pontok metódusai több objektum inicializálását és esetleg más nem definiált viselkedést okozhatnak.

Az osztály alapú programozási modellben az `IDurableEntityContext` objektum a `Entity.Current` statikus tulajdonságban érhető el.

Az osztály alapú modell hasonló a [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/)által népszerűsítette programozási modellhez. Ebben a modellben az entitás típusa .NET-osztályként van definiálva. Az osztály minden metódusa egy olyan művelet, amelyet külső ügyfél hívhat meg. A Orleanstól eltérően azonban a .NET-felületek nem kötelezőek. Az előző *számláló* példa nem használ felületet, de más függvények vagy http API-hívások útján is meghívható.

> [!NOTE]
> Az entitás-trigger függvények Durable Functions 2,0-es és újabb verziókban érhetők el. Az entitás-trigger függvények jelenleg csak a .NET-függvények alkalmazásai számára érhetők el.

### <a name="accessing-entities-from-clients"></a>Entitások elérése az ügyfelektől

A tartós entitásokat meghívhatja vagy lekérdezheti a szokásos függvényekből – más néven az *ügyfél-függvényekből* – az [entitás-ügyfél kimeneti kötésének](durable-functions-bindings.md#entity-client)használatával. Az alábbi példa egy üzenetsor által aktivált függvényt mutat be, amely a kötést használó entitást *jelez* .

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

> [!NOTE]
> A .NET-függvények mind a lazán beírt, mind a Type-Safe metódusokat támogatják az entitások jelzéséhez. A részletekért tekintse meg az [entitás-ügyfél kötési](durable-functions-bindings.md#entity-client-usage) referenciájának dokumentációját.

A *jel* kifejezés azt jelenti, hogy az entitás API-hívása egyirányú és aszinkron. Nem lehetséges, hogy az *ügyfél függvénye* megtudja, mikor dolgozza fel a műveletet az entitás, és nem is lehetséges, hogy az Entity függvény értéket ad vissza egy ügyfél-függvénynek. Az egyirányú üzenetsor-alapú üzenetküldés szándékos kialakítású volt a tartós entitások számára a teljesítmény fontosságának előtérbe helyezése érdekében. Ez a kialakítás a tartós entitások egyike, más hasonló technológiákhoz képest. A következő szakaszban leírtak szerint jelenleg csak a bevezetések képesek kezelni az entitások visszatérési értékeit.

Az ügyfél-függvények az entitások állapotát is lekérhetik, ahogy az az alábbi példában is látható:

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    JObject state = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, state);
}
```

Az entitások állapotának lekérdezéseit a rendszer a tartós nyomkövetési tárolóba küldi, és az entitás legutóbb megőrzött *állapotát adja* vissza. Előfordulhat, hogy a visszaadott állapot elavult, mint az entitás memóriában tárolt állapota. A következő szakaszban leírtak szerint csak a bevezetések tudják olvasni az entitás memóriában lévő állapotát.

### <a name="accessing-entities-from-orchestrations"></a>Entitások elérése a munkafolyamatokból

A Orchestrator függvények API-k használatával férhetnek hozzá az entitásokhoz a előkészítési [trigger kötésében](durable-functions-bindings.md#orchestration-trigger). A Orchestrator függvények egyirányú kommunikációt (tüzet és elfelejteni, más néven *jelzést*) és kétirányú kommunikációt (kérést és választ, más néven *hívót*) is választhatnak. A következő példa egy Orchestrator-függvényt mutat be, amely egy *számláló* entitást *hív* meg és *jelez* .

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Synchronous call to the entity which returns a value
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // Asynchronous call which updates the value
        await context.SignalEntityAsync<int>(entityId, "Add", 1);
    }
}
```

Csak a bevezetések képesek az entitások meghívására és a válasz lekérésére, ami lehet visszatérési érték vagy kivétel. Az [ügyfél-kötést](durable-functions-bindings.md#entity-client) használó ügyfél-függvények csak az entitásokat *jelezhetik* .

> [!NOTE]
> Egy entitás orchestrtor-függvényből való meghívása hasonló a Orchestrator függvény egy [tevékenységi függvényének](durable-functions-types-features-overview.md#activity-functions) meghívásához. A fő különbség az, hogy az Entity functions olyan tartós objektumok, amelyeknek van egy címe (az *entitás azonosítója*), és támogatják a művelet nevének megadását. A Activity functions azonban állapot nélküli, és nem rendelkezik a műveletek fogalmával.

### <a name="dependency-injection-in-entity-classes-net"></a>Függőség injekció az Entity classs (.NET) szolgáltatásban

Az entitás osztályok támogatják [Azure functions függőségi injekciót](../functions-dotnet-dependency-injection.md). Az alábbi példa bemutatja, hogyan regisztrálhat egy `IHttpClientFactory` szolgáltatást egy osztály alapú entitásba.

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

Az alábbi kódrészlet bemutatja, hogyan építheti be a befecskendezett szolgáltatást az Entity osztályba.

```csharp
public class HttpEntity
{
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

    // The function entry point must be declared static
    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> A normál .NET-Azure Functions konstruktorának használatakor a függvények belépési pontjának metódusát be kell jelenteni `static`az osztály alapú entitásokhoz. A nem statikus függvény belépési pontjának deklarálása ütközést okozhat a normál Azure Functions objektum-inicializáló és a tartós entitások objektum-inicializáló között.

### <a name="bindings-in-entity-classes-net"></a>Kötések az entitás osztályaiban (.NET)

A normál függvényektől eltérően az Entity Class metódusok nem rendelkeznek közvetlen hozzáféréssel a bemeneti és kimeneti kötésekhez. Ehelyett a kötési adatrögzítést a belépési pont függvény deklarációjában kell rögzíteni, majd át kell `DispatchAsync<T>` adni a metódusnak. A `DispatchAsync<T>` rendszer az összes átadott objektumot argumentumként automatikusan átadja az entitás osztály konstruktorának.

Az alábbi példa azt szemlélteti, `CloudBlobContainer` hogyan lehet elérhetővé tenni egy, a [blob bemeneti kötésből](../functions-bindings-storage-blob.md#input) származó hivatkozást egy osztály alapú entitás számára.

```csharp
public class BlobBackedEntity
{
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

A Azure Functions-kötésekkel kapcsolatos további információkért tekintse meg a [Azure functions triggerek és kötések](../functions-triggers-bindings.md) dokumentációját.

## <a name="entity-coordination"></a>Entitások koordinálása

Előfordulhatnak olyan időpontok, amikor több entitáson belül kell összehangolni a műveleteket. Előfordulhat például, hogy egy banki alkalmazásban az egyes bankszámlákat képviselő entitások vannak. Ha az egyik fiókból a másikba helyezi át a forrásokat, gondoskodnia kell arról, hogy a _forrásoldali_ fióknak elegendő pénze legyen, és hogy a _forrás_ -és a _Célhelyek_ frissítései tranzakciós szempontból konzisztens módon történjenek.

### <a name="transfer-funds-example-in-c"></a>Példa az adatátvitelreC#

A következő példában a kód egy Orchestrator függvény használatával átviszi az alapokat a két _fiók_ entitások között. Az entitások frissítéseinek koordinálásához `LockAsync` a metódust kell használnia a _kritikus szakasz_ létrehozásához a koordinációban:

> [!NOTE]
> Az egyszerűség kedvéért ez a példa a `Counter` korábban definiált entitást használja. Egy valós alkalmazásban azonban jobb lenne inkább egy részletesebb `BankAccount` entitást meghatározni.

```csharp
// This is a method called by an orchestrator function
public static async Task<bool> TransferFundsAsync(
    string sourceId,
    string destinationId,
    int transferAmount,
    IDurableOrchestrationContext context)
{
    var sourceEntity = new EntityId(nameof(Counter), sourceId);
    var destinationEntity = new EntityId(nameof(Counter), destinationId);

    // Create a critical section to avoid race conditions.
    // No operations can be performed on either the source or
    // destination accounts until the locks are released.
    using (await context.LockAsync(sourceEntity, destinationEntity))
    {
        ICounter sourceProxy = 
            context.CreateEntityProxy<ICounter>(sourceEntity);
        ICounter destinationProxy =
            context.CreateEntityProxy<ICounter>(destinationEntity);

        int sourceBalance = await sourceProxy.Get();

        if (sourceBalance >= transferAmount)
        {
            await sourceProxy.Add(-transferAmount);
            await destinationProxy.Add(transferAmount);

            // the transfer succeeded
            return true;
        }
        else
        {
            // the transfer failed due to insufficient funds
            return false;
        }
    }
}
```

A .net `LockAsync` -ben egy `IDisposable` olyan értéket ad vissza, amely a kritikus szakaszt elveti. Ez `IDisposable` az eredmény együtt használható egy `using` blokkal a kritikus szakasz szintaktikai ábrázolásának beolvasásához.

Az előző példában egy Orchestrator-függvény a _forrás_ entitásból egy _célként megadott_ entitásba ruházta át a forrásokat. A `LockAsync` metódus a _forrás_ és a _cél_ fiók entitásait is zárolta. Ez a zárolás gondoskodik arról, hogy egyetlen másik ügyfél sem tudja lekérdezni vagy módosítani a fiók állapotát, amíg az `using` utasítás végén kilépett a _kritikus szakaszra_ . Ez gyakorlatilag megakadályozta a _forrás_ fiókból való túllépés lehetőségét.

### <a name="critical-section-behavior"></a>Kritikus szakasz viselkedése

A `LockAsync` metódus _kritikus szakaszt_ hoz létre egy előkészítési folyamatban. Ezekkel a _kritikus részekkel_ megakadályozható, hogy más összehangolt módosításokat hozzon létre egy adott entitáson. Belsőleg az `LockAsync` API "zárolási" műveleteket küld az entitásoknak, és visszatér, ha "zárolási beszerzett" válaszüzenetet kap ezekről az entitásokról. A *zárolás* és a *feloldás* is az összes entitás által támogatott beépített művelet.

A más ügyfelektől érkező műveletek nem engedélyezettek egy entitáson, amíg zárolt állapotban van. Ez a viselkedés garantálja, hogy egyszerre csak egy összehangoló példány tud zárolni egy entitást. Ha egy hívó olyan műveletet próbál megszólítani egy entitáson, amely egy eljárással zárolva van, akkor a művelet egy *függőben lévő műveleti várólistába*kerül. A függőben lévő műveletek addig nem lesznek feldolgozva, amíg a Holding-előkészítés fel nem oldja a zárolást.

> [!NOTE] 
> Ez némileg eltér a legtöbb programozási nyelvben használt szinkronizálási primitívtől, például a `lock` utasításban. C# Például a alkalmazásban C#az utasítást `lock` az összes szálnak kell használnia, hogy biztosítsa a megfelelő synchonization több szál között. Az entitásoknak azonban nincs szükségük arra, hogy minden hívó explicit módon _zárolja_ az entitásokat. Ha bármelyik hívó zárol egy entitást, az adott entitáson végzett összes más művelet le lesz tiltva, és a zárolás mögött is sor kerül.

Az entitásokra vonatkozó zárolások tartósak, így azok akkor is megmaradnak, ha a végrehajtó folyamat újra van hajtva. A zárolások belsőleg megmaradnak az entitás tartós állapotának részeként.

### <a name="critical-section-restrictions"></a>Kritikus szakasz korlátozásai

Számos korlátozást alkalmazunk a kritikus fontosságú profilok használatára. Ezek a korlátozások a holtpontok és a újbóli belépés megakadályozására szolgálnak.

* A kritikus szakaszt nem lehet beágyazni.
* A kritikus szakaszban nem hozhatók létre alfolyamatok.
* A kritikus szakaszt csak a zárolt entitások hívhatják meg.
* A kritikus szakaszban nem hívható meg ugyanaz az entitás több párhuzamos hívás használatával.
* A kritikus szakaszt csak azok az entitások jelezhetik, amelyek nincsenek zárolva.

## <a name="comparison-with-virtual-actors"></a>Összehasonlítás virtuális szereplőkkel

A tartós entitások számos funkcióját a [színészi modell](https://en.wikipedia.org/wiki/Actor_model)ihlette. Ha már ismeri a szereplőkkel való részvételt, akkor a jelen cikkben ismertetett fogalmak közül sokat is felismer. A tartós entitások többek között a [virtuális szereplőkhöz](https://research.microsoft.com/projects/orleans/) hasonlóak:

* A tartós entitások az *entitás azonosítóján*keresztül címezhető.
* A tartós entitások műveletei a verseny feltételeinek megelőzése érdekében egyszerre hajtanak végre sorosan.
* A tartós entitásokat a rendszer automatikusan hozza létre, amikor meghívja őket vagy jelezték.
* Ha nem hajtja végre a műveleteket, a tartós entitások csendesen törlődnek a memóriából.

Van azonban néhány fontos különbség, amelyeket érdemes megjegyezni:

* A tartós entitások a késleltetést rangsorolják a *késéshez*képest, ezért nem feltétlenül alkalmazhatók szigorú késési követelményekkel rendelkező alkalmazások esetében.
* Az entitások között küldött üzenetek megbízhatóan és sorrendben lesznek kézbesítve.
* A tartós entitások tartós felépítéssel együtt használhatók, és támogatják az elosztott zárolási mechanizmusokat is.
* Az entitásokban a kérelmek és válaszok mintája csak az előkészítésre korlátozódik. Az *ügyfelek közötti* *és az entitások* közötti kommunikációhoz csak egyirányú üzenetküldés (más néven "jelzés") engedélyezett, ahogy az eredeti modellben is. Ez a viselkedés megakadályozza az elosztott holtpontokat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudnivalók a feladatok hubokról](durable-functions-task-hubs.md)