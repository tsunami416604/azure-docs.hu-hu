---
title: Tartós entitások – Azure Functions
description: Ismerje meg, mi a tartós entitások és hogyan használhatók a Azure Functions Durable Functions-bővítményében.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 62ca71e1b42e000f7528a2963793f9bf40663bf3
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818503"
---
# <a name="entity-functions"></a>Entitás-függvények

Az Entity functions olyan műveleteket határoz meg, amelyek olyan kis méretű állapotok olvasására és frissítésére szolgálnak, amelyek *tartós entitások*. A Orchestrator függvényekhez hasonlóan az Entity functions is egy speciális trigger típussal, az *entitások triggerével*működik. Az Orchestrator függvényektől eltérően az Entity functions az entitások állapotát explicit módon kezeli, ahelyett, hogy az állapotot a vezérlési folyamaton keresztül implicit módon jelképezi.
Az entitások lehetővé teszik az alkalmazások horizontális felskálázását azáltal, hogy számos entitáson keresztül terjesztik a munkát, és ezek mindegyike szerény méretű állapottal rendelkezik.

> [!NOTE]
> Az Entity functions és a kapcsolódó funkciók csak Durable Functions 2,0-es és újabb verziókban érhetők el.

## <a name="general-concepts"></a>Általános fogalmak

Az entitások olyan kis-és nagyvállalati szolgáltatásokat is tanúsítanak, amelyek üzenetek használatával kommunikálnak. Minden entitás egyedi identitással és belső állapottal rendelkezik (ha létezik). A szolgáltatásokhoz vagy objektumokhoz hasonlóan az entitások is végrehajtják a műveleteket, amikor a rendszer erre kéri. A végrehajtásakor a művelet az entitás belső állapotát is frissítheti. Külső szolgáltatásokat is meghívhat, és megvárhatja a választ. Az entitások a megbízható várólistákon keresztül implicit módon elküldött üzenetek használatával kommunikálnak más entitásokkal, összeszerelésekkel és ügyfelekkel. 

Az ütközések elkerülése érdekében az egyetlen entitáson végrehajtott összes művelet végrehajtása a szerializált, azaz a másik után történik. 

### <a name="entity-id"></a>Entitás azonosítója
Az entitások egyedi azonosítóval, az *entitás azonosítójának*használatával érhetők el. Az entitás-AZONOSÍTÓk egyszerűen olyan karakterláncok, amelyek egyedileg azonosítanak egy entitás-példányt. A következőkből áll:

* Az **entitás neve**: az entitás típusát azonosító név (például "számláló"). A névnek meg kell egyeznie az entitást megvalósító entitás-függvény nevével. Nem érzékeny az esetre.
* **Entitás kulcsa**: olyan karakterlánc, amely egyedileg azonosítja az entitást az azonos nevű entitások között (például egy GUID azonosító).

Előfordulhat például, hogy egy *számláló* entitás függvényt egy online játékban kell tartani. A játék minden példánya egyedi AZONOSÍTÓval fog rendelkezni, például `@Counter@Game1`, `@Counter@Game2`és így tovább. Egy adott entitást megcélzó összes művelethez meg kell adni egy entitás AZONOSÍTÓját paraméterként.

### <a name="entity-operations"></a>Entitások műveletei ###

Egy művelet egy entitáson való meghívásához az egyik a következőt adja meg

* A célként megadott entitás *entitásának azonosítója*
* A *művelet neve*, egy karakterlánc, amely meghatározza a végrehajtandó műveletet. A számláló entitás például támogathatja a "Hozzáadás", a "lekérés" vagy az "alaphelyzetbe állítás" műveletet.
* A *művelet bemenete*, amely egy opcionális bemeneti paraméter a művelethez. Az "add" művelet például egy egész számot vehet igénybe bemenetként.

A műveletek visszaadhatják az eredmény értékét vagy a hiba eredményét (például JavaScript-hibát vagy .NET-kivételt). Ezt az eredményt vagy hibát megfigyelheti a műveletet meghívó Összehangolók.

Az entitások művelete az entitás állapotának létrehozását, olvasását, frissítését és törlését is elvégezheti. Az entitás állapota mindig tartósan marad a tárolóban.

## <a name="defining-entities"></a>Entitások definiálása

Jelenleg két különböző API-t kínálunk az entitások definiálásához.

**Függvény-alapú szintaxis** , amelyben az entitások függvényekként jelennek meg, és az alkalmazás kifejezetten elküldi a műveleteket. Ez a szintaxis jól működik az egyszerű állapottal rendelkező entitások, néhány művelet vagy egy dinamikus műveleti csoport (például az alkalmazási keretrendszerek) esetében. Azonban ez unalmas lehet a karbantartáshoz, mivel a fordítási idő során nem vesz fel gépelési hibát.

**Osztály-alapú szintaxis** , amelyben az entitásokat és a műveleteket osztályok és metódusok jelölik. Ez a szintaxis könnyebben olvasható kódot hoz létre, és lehetővé teszi a műveletek típusos biztonságos módon történő meghívását. Az osztály-alapú szintaxis csak egy vékony réteg a függvény-alapú szintaxison felül, így mindkét változat felhasználható ugyanabban az alkalmazásban is.

### <a name="example-function-based-syntax---c"></a>Példa: függvény-alapú szintaxis-C#

A következő kód egy egyszerű, tartós funkcióként megvalósított *számlálós* entitásra mutat példát. Ez a függvény három műveletet határoz meg, `add`, `reset`és `get`, amelyek mindegyike egész számban működik.

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
            ctx.Return(ctx.GetState<int>());
            break;
    }
}
```

A függvény-alapú szintaxissal és annak használatával kapcsolatos további információkért lásd a [Function-based szintaxist](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="example-class-based-syntax---c"></a>Példa: osztály alapú szintaxis –C#

A következő példa az `Counter` entitás egyenértékű implementációját osztályok és metódusok használatával.

```csharp
[JsonObject(MemberSerialization.OptIn)]
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

Az entitás állapota `Counter`típusú objektum, amely egy olyan mezőt tartalmaz, amely a számláló aktuális értékét tárolja. Ha meg szeretné őrizni ezt az objektumot a tárolóban, a rendszer szerializálja és deszerializálja a [JSON.net](https://www.newtonsoft.com/json) -könyvtár. 

Az osztály-alapú szintaxissal és annak használatával kapcsolatos további információkért lásd: entitás- [osztályok meghatározása](durable-functions-dotnet-entities.md#defining-entity-classes).

### <a name="example-javascript-entity"></a>Példa: JavaScript-entitás

A tartós entitások a `durable-functions` NPM csomag **1.3.0** kezdődően érhetők el a JavaScriptben. A következő kód a *számláló* entitás, amely a JavaScriptben írt tartós függvényként van megvalósítva.

**function. JSON**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**index. js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

## <a name="accessing-entities"></a>Entitások elérése

Az entitások egyirányú vagy kétirányú kommunikációval érhetők el. A következő terminológiát használjuk a megkülönböztetéshez: 

* Az entitások **meghívása** azt jelenti, hogy kétirányú (egyirányú) kommunikációt használunk: a művelet üzenetet küld az entitásnak, majd a folytatás előtt várja meg a válaszüzenetet. A válaszüzenet eredményét vagy a hiba eredményét (például JavaScript-hiba vagy .NET-kivétel) is megadhatja. Ezt az eredményt vagy hibát a hívó észleli.
* Az entitások **jelzése** azt jelenti, hogy egyirányú (tűz-és felejtsds) kommunikációt használunk: egy műveleti üzenetet küldünk, de nem várja meg a választ. Amíg az üzenet garantáltan kézbesíthető, a küldő nem tudja, hogy mikor, és nem figyelheti meg az eredmény értékét vagy hibáit.

Az entitások a Orchestrator függvényeken belül vagy az Entity functions szolgáltatásból érhetők el az ügyfél-függvényekből. Az összes kontextus nem támogatja a kommunikáció összes formáját:

* Az ügyfeleken belül megadhatja az entitásokat, és *elolvashatja* *az entitás állapotát* .
* A folyamatokon belül megadhatja *az entitásokat* , és *meghívhatja* az entitásokat.
* Az *entitásokon belül megadhatja* az entitásokat.

Alább néhány példát mutatunk be, amelyek illusztrálják az entitások elérésének különféle módjait.

> [!NOTE]
> Az egyszerűség kedvéért az alábbi példák a lazán beírt szintaxist mutatják be az entitásokhoz való hozzáféréshez. Általánosságban azt javasoljuk, hogy a [felületeken keresztül hozzáférjenek az entitásokhoz](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) , mivel több típusú ellenőrzést is biztosít.

### <a name="example-client-signals-an-entity"></a>Példa: az ügyfél jelzi az entitást

Az entitások egyszerű Azure-függvényből való eléréséhez – más néven *ügyfél-függvény* – használja az [entitás ügyfél-kimeneti kötését](durable-functions-bindings.md#entity-client). Az alábbi példa egy üzenetsor által aktivált függvényt mutat be, amely a kötést használó entitást *jelez* .

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

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

A *jel* kifejezés azt jelenti, hogy az entitás API-hívása egyirányú és aszinkron. Az *ügyfél* nem tudja tudni, hogy mikor dolgozza fel a műveletet az entitás. Emellett az ügyfél függvény nem tudja megfigyelni az eredmények értékét vagy kivételeit. 

### <a name="example-client-reads-an-entity-state"></a>Példa: az ügyfél beolvas egy entitás állapotát

Az ügyfél functions egy entitás állapotát is lekérdezheti az alábbi példában látható módon:

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

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    return context.df.readEntityState(entityId);
};
```

Az entitások állapotának lekérdezéseit a rendszer a tartós nyomkövetési tárolóba küldi, és az entitás legutóbb megőrzött *állapotát adja* vissza. Ez az állapot mindig "véglegesítve" állapotú, azaz soha nem egy művelet végrehajtásának közepén feltételezhető ideiglenes közbenső állapot. Azonban lehetséges, hogy ez az állapot elavult az entitás memóriában lévő állapotához képest. A következő szakaszban leírtak szerint csak a bevezetések tudják olvasni az entitás memóriában lévő állapotát.

### <a name="example-orchestration-signals-and-calls-an-entity"></a>Példa: előkészítési jelek és entitások meghívása

A Orchestrator függvények API-k használatával férhetnek hozzá az entitásokhoz a előkészítési [trigger kötésében](durable-functions-bindings.md#orchestration-trigger). A következő példa egy Orchestrator-függvényt mutat be, amely egy *számláló* entitást *hív* meg és *jelez* .

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // One-way signal to the entity which updates the value - does not await a response
        context.SignalEntity(entityId, "Add", 1);
    }
}
```

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const entityId = new df.EntityId("Counter", "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    currentValue = yield context.df.callEntity(entityId, "get");
    if (currentValue < 10) {
        // One-way signal to the entity which updates the value - does not await a response
        yield context.df.signalEntity(entityId, "add", 1);
    }
});
```

Csak a bevezetések képesek az entitások meghívására és a válasz lekérésére, ami lehet visszatérési érték vagy kivétel. Az [ügyfél-kötést](durable-functions-bindings.md#entity-client) használó ügyfél-függvények csak az entitásokat *jelezhetik* .

> [!NOTE]
> Egy entitás Orchestrator-függvényből való meghívása hasonló a Orchestrator függvény egy [tevékenységi függvényének](durable-functions-types-features-overview.md#activity-functions) meghívásához. A fő különbség az, hogy az Entity functions olyan tartós objektumok, amelyeknek van egy címe (az *entitás azonosítója*), és támogatják a művelet nevének megadását. A Activity functions azonban állapot nélküli, és nem rendelkezik a műveletek fogalmával.

### <a name="example-entity-signals-an-entity"></a>Példa: az entitás egy entitást jelzi

Az Entity függvény a jeleket más entitásoknak (vagy akár saját maga!) is elküldheti a művelet végrehajtása közben.
Például módosíthatjuk a fenti számláló entitást, így egy "mérföldkő által elért" jelet küld egy figyelő entitásnak, amikor a számláló eléri a 100 értéket:

```csharp
   case "add":
        var currentValue = ctx.GetState<int>();
        var amount = ctx.GetInput<int>();
        if (currentValue < 100 && currentValue + amount >= 100)
        {
            ctx.SignalEntity(new EntityId("MonitorEntity", ""), "milestone-reached", ctx.EntityKey);
        }

        ctx.SetState(currentValue + amount);
        break;
```

```javascript
    case "add":
        const amount = context.df.getInput();
        if (currentValue < 100 && currentValue + amount >= 100) {
            const entityId = new df.EntityId("MonitorEntity", "");
            context.df.signalEntity(entityId, "milestone-reached", context.df.instanceId);
        }
        context.df.setState(currentValue + amount);
        break;
```

## <a name="entity-coordination"></a>Entitások koordinálása

Előfordulhatnak olyan időpontok, amikor több entitáson belül kell összehangolni a műveleteket. Előfordulhat például, hogy egy banki alkalmazásban az egyes bankszámlákat képviselő entitások vannak. Ha az egyik fiókból a másikba helyezi át a forrásokat, gondoskodnia kell arról, hogy a _forrásoldali_ fióknak elegendő pénze legyen, és hogy a _forrás_ -és a _Célhelyek_ frissítései tranzakciós szempontból konzisztens módon történjenek.

### <a name="example-transfer-funds-c"></a>Példa: átutalási alapokC#()

A következő példában a kód egy Orchestrator függvény használatával átviszi az alapokat a két _fiók_ entitások között. Az entitások frissítéseinek koordinálásához a `LockAsync` metódust kell használnia a _kritikus szakasz_ létrehozásához a koordinációban:

> [!NOTE]
> Az egyszerűség kedvéért ez a példa a korábban definiált `Counter` entitást használja. Egy valós alkalmazásban azonban érdemes inkább részletesebb `BankAccount` entitást meghatározni.

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

A .NET-ben a `LockAsync` olyan `IDisposable` ad vissza, amely a kritikus szakaszt a kivezetés során véget ér. Ez a `IDisposable` eredmény `using` blokkmal együtt használható a kritikus szakasz szintaktikai ábrázolásának beolvasásához.

Az előző példában egy Orchestrator-függvény a _forrás_ entitásból egy _célként megadott_ entitásba ruházta át a forrásokat. A `LockAsync` metódus a _forrás_ és a _cél_ fiók entitásait is zárolta. Ez a zárolás biztosítja, hogy egyetlen másik ügyfél sem tudja lekérdezni vagy módosítani a fiók állapotát, amíg a (z) `using` utasítás végén a _kritikus szakaszt_ kilépett. Ez a viselkedés megakadályozza a _forrás_ fiókból való túllépés lehetőségét.

> [!NOTE] 
> Ha egy összehangolás leáll (normál esetben vagy hibával), a folyamatban lévő kritikus lépések implicit módon véget ér, és minden zárolás megjelent.

### <a name="critical-section-behavior"></a>Kritikus szakasz viselkedése

A `LockAsync` metódus _kritikus szakaszt_ hoz létre egy előkészítési folyamatban. Ezekkel a _kritikus részekkel_ megakadályozható, hogy más összehangolt módosításokat hozzon létre egy adott entitáson. Belsőleg a `LockAsync` API "zárolási" műveleteket küld az entitásoknak, és visszatér, ha "zárolási beszerzett" válaszüzenetet kap ezekről az entitásokról. A *zárolás* és a *feloldás* is az összes entitás által támogatott beépített művelet.

A más ügyfelektől érkező műveletek nem engedélyezettek egy entitáson, amíg zárolt állapotban van. Ez a viselkedés garantálja, hogy egyszerre csak egy összehangoló példány tud zárolni egy entitást. Ha egy hívó olyan műveletet próbál megszólítani egy entitáson, amely egy eljárással zárolva van, akkor a művelet egy *függőben lévő műveleti várólistába*kerül. A függőben lévő műveletek addig nem lesznek feldolgozva, amíg a Holding-előkészítés fel nem oldja a zárolást.

> [!NOTE] 
> Ez némileg eltér a legtöbb programozási nyelvben használt szinkronizálási primitívekkel, például a `lock` utasításával C#. Például a-ben C#a `lock` utasítást minden szálnak használnia kell, hogy biztosítsa a megfelelő synchonization több szál között. Az entitásoknak azonban nincs szükségük arra, hogy minden hívó explicit módon _zárolja_ az entitásokat. Ha bármelyik hívó zárol egy entitást, az adott entitáson végzett összes más művelet le lesz tiltva, és a zárolás mögött is sor kerül.

Az entitásokra vonatkozó zárolások tartósak, így azok akkor is megmaradnak, ha a végrehajtó folyamat újra van hajtva. A zárolások belsőleg megmaradnak az entitás tartós állapotának részeként.

A tranzakcióktól eltérően a kritikus részekben nem történik meg automatikusan a változások visszaállítása a hibák esetén. Ehelyett a hibakezelés (visszaállítás, újrapróbálkozás vagy egyéb) explicit módon kell kódolva lennie. például hibák vagy kivételek elfogásával. Ez a tervezési lehetőség szándékos. Az előkészítési folyamat összes hatásának automatikus visszagörgetése általában nehéz vagy lehetetlen, mivel a rendszer tevékenységeket futtathat, és hívásokat végezhet olyan külső szolgáltatásokra, amelyek nem állíthatók vissza. Emellett előfordulhat, hogy a visszaállítási kísérletek sikertelenek lehetnek, és további hibakezelés szükségesek.

### <a name="critical-section-rules"></a>Kritikus szakasz szabályai

Az alacsony szintű zárolási primitívek a legtöbb programozási nyelven eltérően a kritikus fontosságú szakaszt **nem a holtpontra kell biztosítani**. A holtpontok megelőzése érdekében a következő korlátozásokat kell kikényszeríteni: 

* A kritikus szakaszt nem lehet beágyazni.
* A kritikus szakaszban nem hozhatók létre alfolyamatok.
* A kritikus szakaszt csak a zárolt entitások hívhatják meg.
* A kritikus szakaszban nem hívható meg ugyanaz az entitás több párhuzamos hívás használatával.
* A kritikus szakaszt csak azok az entitások jelezhetik, amelyek nincsenek zárolva.

A szabályok megsértése futásidejű hibát okoz (például a .NET-`LockingRulesViolationException`), amely egy üzenetet tartalmaz, amely ismerteti, hogy a szabály milyen módon lett megszakítva.

## <a name="comparison-with-virtual-actors"></a>Összehasonlítás virtuális szereplőkkel

A tartós entitások számos funkcióját a [színészi modell](https://en.wikipedia.org/wiki/Actor_model)ihlette. Ha már ismeri a szereplőkkel való részvételt, akkor a jelen cikkben ismertetett fogalmak közül sokat is felismer. A tartós entitások különösen hasonlók a [virtuális szereplőkkel](https://research.microsoft.com/projects/orleans/)vagy a *gabonához*, ahogyan az [Orleans-projekt](http://dotnet.github.io/orleans/)népszerűsítette. Például:

* A tartós entitások az *entitás azonosítóján*keresztül címezhető.
* A tartós entitások műveletei a verseny feltételeinek megelőzése érdekében egyszerre hajtanak végre sorosan.
* A tartós entitások implicit módon jönnek létre, amikor meghívja őket vagy jelezték.
* Ha nem hajtja végre a műveleteket, a tartós entitások csendesen törlődnek a memóriából.

Van azonban néhány fontos különbség, amelyeket érdemes megjegyezni:

* A tartós entitások a késleltetést rangsorolják a *késéshez* *képest,* ezért nem feltétlenül alkalmazhatók szigorú késési követelményekkel rendelkező alkalmazások esetében.
* A tartós entitások nem rendelkeznek beépített időtúllépéssel az üzenetekhez. Orleans-ban minden üzenet időtúllépést eredményezett a konfigurálható idő után (alapértelmezés szerint 30 másodperc).
* Az entitások között küldött üzenetek megbízhatóan és sorrendben lesznek kézbesítve. Orleans-ban a megbízható vagy rendezett kézbesítés támogatott a streameken keresztül küldött tartalmak esetében, de a gabonák közötti összes üzenet esetében nem garantált.
* Az entitásokban a kérelmek és válaszok mintája csak az előkészítésre korlátozódik. Az entitásokon belül csak egyirányú üzenetküldés (más néven "jelzés") engedélyezett, ahogy az eredeti modellben is, és ellentétben a magokkal a Orleansban. 
* A tartós entitások nem holtpontos. Orleans-ban holtpontok léphetnek fel (és nem oldhatók fel az üzenetek időkorlátja).
* A tartós entitások tartós felépítéssel együtt használhatók, és támogatják az elosztott zárolási mechanizmusokat is. 


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Olvassa el a fejlesztői útmutató a tartós entitásokhoz a .NET-ben](durable-functions-dotnet-entities.md)

> [!div class="nextstepaction"]
> [Tudnivalók a feladatok hubokról](durable-functions-task-hubs.md)
