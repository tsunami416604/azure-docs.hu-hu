---
title: Tartós entitások – Azure-függvények
description: Ismerje meg, mik a tartós entitások, és hogyan használhatja őket az Azure Functions tartós függvények bővítményében.
author: cgillum
ms.topic: overview
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 4f45ac40e7df865bdb4722d086325096c377cd59
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877542"
---
# <a name="entity-functions"></a>Entitásfüggvények

Az entitásfüggvények a kis állapotdarabok, más néven tartós entitások olvasására és frissítésére szolgáló *műveleteket határozzák meg.* Az orchestrator függvényeihez hasonlóan az entitásfüggvények is speciális eseményindító-típussal rendelkező függvények, az *entitás eseményindítója.* Az orchestrator függvényekkel ellentétben az entitás függvényei explicit módon kezelik az entitás állapotát, nem pedig implicit módon az állapotot jelölik a vezérlési folyamaton keresztül.
Entitások biztosítják az alkalmazások horizontális felskálázása a munka elosztásával számos entitások között, mindegyik egy szerény méretű állapotban.

> [!NOTE]
> Az entitásfüggvények és a kapcsolódó funkciók csak a Durable Functions 2.0 és újabb verziókban érhetők el.

## <a name="general-concepts"></a>Általános fogalmak

Az entitások úgy viselkednek, mint az apró szolgáltatások, amelyek üzeneteken keresztül kommunikálnak. Minden entitás egyedi identitással és belső állapottal rendelkezik (ha létezik). A szolgáltatásokhoz vagy objektumokhoz hasonlóan az entitások is műveleteket hajtanak végre, amikor erre kérik őket. Amikor egy művelet végrehajtása, lehet, hogy frissíti az entitás belső állapotát. Előfordulhat, hogy külső szolgáltatásokat is hív, és várja meg a választ. Az entitások kommunikálnak más entitásokkal, vezénylésekkel és ügyfelekkel a megbízható várólistákon keresztül implicit módon küldött üzenetek használatával. 

Az ütközések elkerülése érdekében az egyetlen entitáson végrehajtott összes művelet garantáltan sorosan, azaz egymás után hajt végre. 

### <a name="entity-id"></a>Entitás azonosítója
Az entitások egy egyedi azonosítón keresztül érhetők el, az *entitásazonosítón*keresztül. Az entitásazonosító egyszerűen karakterláncok párja, amely egyedileg azonosítja az entitáspéldányokat. Ez a következőkből áll:

* **Az entitás neve**, amely az entitás típusát azonosító név. Erre példa a "Számláló". Ennek a névnek meg kell egyeznie az entitást megvalósító entitásfüggvény nevével. Nem érzékeny a kis- és nagybetűkre.
* **Entitáskulcs**, amely egy karakterlánc, amely egyedileg azonosítja az entitást az összes többi azonos nevű entitás között. Erre példa a GUID.

Egy `Counter` entitásfüggvény például egy online játék pontszámának megőrzésére használható. A játék minden példánya egyedi entitásazonosítóval `@Counter@Game1` `@Counter@Game2`rendelkezik, például és . Minden olyan művelet, amely egy adott entitást céloz meg, paraméterként meg kell adnia egy entitásazonosítót.

### <a name="entity-operations"></a>Entitás-műveletek ###

Entitáson lévő művelet meghívásához adja meg a következőt:

* A célentitás **entitásazonosítója.**
* **A művelet neve**, amely a végrehajtandó műveletet megadva. Az `Counter` entitás például `add`támogathat , `get`vagy `reset` műveleteket.
* **A műveletbemenet**, amely a művelet nem kötelező bemeneti paramétere. Például az add művelet bemenetként egész mennyiséget vehet igénybe.
* **Ütemezett idő**, amely a művelet szállítási idejének megadására nem kötelező paraméter. Például egy művelet megbízhatóan ütemezhető több nap futtatásához a jövőben.

A műveletek eredményértéket vagy hibaeredményt, például JavaScript-hibát vagy .NET kivételt adhatnak vissza. Ezt az eredményt vagy hibát a műveletnek nevezett vezénylések is megfigyelhetik.

Az entitásművelet az entitás állapotát is létrehozhatja, olvashatja, frissítheti és törölheti. Az entitás állapota mindig tartósan megmarad a tárolóban.

## <a name="define-entities"></a>Entitások meghatározása

Jelenleg a két különböző API-k meghatározása entitások a következők:

**A függvényalapú szintaxist,** ahol az entitásokat függvényként képviseli, és az alkalmazás kifejezetten küldi a műveleteket. Ez a szintaxis jól működik az egyszerű állapotú, kevés művelettel vagy dinamikus műveletkészlettel rendelkező entitások esetében, például az alkalmazáskeretekben. Ez a szintaxis lehet unalmas fenntartani, mert nem fog típus hibák összeállításakor időben.

**Osztályalapú szintaxis (csak.NET),** ahol az entitásokat és műveleteket osztályok és metódusok jelölik. Ez a szintaxis könnyebben olvasható kódot eredményez, és lehetővé teszi a műveletek típusbiztos módon való meghívását. Az osztályalapú szintaxis egy vékony réteg a függvényalapú szintaxis tetején, így mindkét változat szinonimaként használható ugyanabban az alkalmazásban.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="example-function-based-syntax---c"></a>Példa: Függvényalapú szintaxis - C #

A következő kód egy példa `Counter` egy egyszerű entitás tartós függvényként megvalósított. Ez a függvény három `add`műveletet határoz meg, , `reset`és `get`mindegyike egész állapotban működik.

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

A függvényalapú szintaxisról és annak használatáról a [Függvényalapú szintaxis](durable-functions-dotnet-entities.md#function-based-syntax)című témakörben talál további információt.

### <a name="example-class-based-syntax---c"></a>Példa: Osztályalapú szintaxis - C #

A következő példa az entitás `Counter` azonos implementációja osztályok és módszerek használatával.

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

Ennek az entitásnak az állapota `Counter`egy típusú objektum, amely a számláló aktuális értékét tároló mezőt tartalmaz. Az objektum tárolása megőrzéséhez a [Json.NET](https://www.newtonsoft.com/json) könyvtárszeriszializálódik és deszerializálódik. 

Az osztályalapú szintaxisról és használatáról az [Entitásosztályok definiálása](durable-functions-dotnet-entities.md#defining-entity-classes)című témakörben talál további információt.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="example-javascript-entity"></a>Példa: JavaScript entitás

A tartós entitások JavaScript-ben érhetők el az `durable-functions` npm-csomag **1.3.0-s** verziójától kezdve. A következő kód `Counter` a JavaScript-ben írt tartós függvényként megvalósított entitás.

**Számláló/function.json**
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

**Számláló/index.js**
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

---

## <a name="access-entities"></a>Hozzáférés entitások

Az entitások egyirányú vagy kétirányú kommunikációval érhetők el. A következő terminológia különbözteti meg a kommunikáció két formáját: 

* Az entitás **hívása** kétirányú (oda-vissza) kommunikációt használ. A folytatás előtt küld egy műveletüzenetet az entitásnak, majd megvárja a válaszüzenetet. A válaszüzenet eredményértéket vagy hibaeredményt, például JavaScript-hibát vagy .NET kivételt adhat meg. Ezt az eredményt vagy hibát a hívó ezt követően figyeli.
* Az entitás **jelzése** egyirányú (tűz és felejtés) kommunikációt használ. Küld egy műveletüzenetet, de ne várjon választ. Bár az üzenet garantáltan kézbesítésre kerül, a feladó nem tudja, mikor, és nem tudja megfigyelni az eredmény értékét vagy a hibákat.

Az entitások az ügyfélfüggvényeken belülről, az orchestrator függvényeken belülről vagy az entitásfüggvényeken belül érhetők el. Nem minden kommunikációs formát támogat minden kontextus:

* Az ügyfeleken belül jelezheti az entitásokat, és elolvashatja az entitásállapotot.
* A vezényléseken belül jelezheti az entitásokat, és meghívhat entitásokat.
* Az entitásokon belül jelezheti az entitásokat.

Az alábbi példák bemutatják az entitások elérésének különböző módjait.

### <a name="example-client-signals-an-entity"></a>Példa: Az ügyfél egy entitást jelez

Az entitások eléréséhez egy közönséges Azure-függvényből, amely et ügyfélfüggvénynek is neveznek, használja az [entitásügyfél-kötést.](durable-functions-bindings.md#entity-client) A következő példa egy várólista által aktivált függvényt mutat be, amely egy entitást jelez ezzel a kötéssel.

# <a name="c"></a>[C#](#tab/csharp)

> [!NOTE]
> Az egyszerűség kedvéért az alábbi példák az entitások elérésének lazán beírt szintaxisát mutatják be. Általában azt javasoljuk, hogy [az entitásokat a felületeken keresztül érje el,](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) mert több típusellenőrzést biztosít.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await client.signalEntity(entityId, "add", 1);
};
```

---

A *jel* kifejezés azt jelenti, hogy az entitás API-meghívásegyirányú és aszinkron. Az ügyfélfüggvény nem tudja, hogy az entitás mikor dolgozta fel a műveletet. Emellett az ügyfélfüggvény nem tud eredményértékeket vagy kivételeket megfigyelni. 

### <a name="example-client-reads-an-entity-state"></a>Példa: Az ügyfél entitásállapotot olvas

Az ügyfélfüggvények lekérdezhetik egy entitás állapotát is, ahogy az a következő példában látható:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    EntityStateResponse<JObject> stateResponse = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, stateResponse.EntityState);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    const stateResponse = await client.readEntityState(entityId);
    return stateResponse.entityState;
};
```

---

Az entitásállapot-lekérdezések a tartós nyomon követési tárolóba kerülnek, és az entitás legutóbb megőrzött állapotát adják vissza. Ez az állapot mindig egy "elkötelezett" állapot, azaz soha nem egy ideiglenes köztes állapot, amelyet egy művelet végrehajtása során feltételeznek. Azonban lehetséges, hogy ez az állapot elavult az entitás memóriában lévő állapotához képest. Csak vezénylések olvashatják az entitás memórián belüli állapotát, a következő szakaszban leírtak szerint.

### <a name="example-orchestration-signals-and-calls-an-entity"></a>Példa: Vezénylési jelek és hívások entitás

Az Orchestrator függvények a [vezénylési eseményindító kötésen](durable-functions-bindings.md#orchestration-trigger)lévő API-k használatával férhetnek hozzá az entitásokhoz. A következő példakód egy orchestrator függvény `Counter` hívása és jelzése egy entitás.

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const entityId = new df.EntityId("Counter", "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    currentValue = yield context.df.callEntity(entityId, "get");
});
```

> [!NOTE]
> A JavaScript jelenleg nem támogatja az entitás vezénylési üzenetből történő jelzését. A `callEntity` használható helyette.

---

Csak vezénylések képesek az entitások hívására és válasz fogadására, amely lehet visszatérési érték vagy kivétel. Az [ügyfélkötést](durable-functions-bindings.md#entity-client) használó ügyfélfüggvények csak entitásokat jelezhetnek.

> [!NOTE]
> Egy entitás hívása egy orchestrator függvényhasonló egy [tevékenységfüggvény](durable-functions-types-features-overview.md#activity-functions) egy orchestrator függvényből. A fő különbség az, hogy az entitásfüggvények tartós objektumok egy címmel, amely az entitás azonosítója. Az entitásfüggvények támogatják a művelet nevének megadását. A tevékenységfüggvények viszont állapot nélküliek, és nem rendelkeznek a műveletek fogalmával.

### <a name="example-entity-signals-an-entity"></a>Példa: Az entitás jelez egy entitásnak

Egy entitásfüggvény jeleket küldhet más entitásoknak, vagy akár magának is, miközben végrehajt egy műveletet.
Például módosíthatjuk az `Counter` előző entitás példát, hogy küldjön egy "mérföldkő elérte" jelet néhány figyelésentitás, amikor a számláló eléri a 100 értéket.

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

---

## <a name="entity-coordination-currently-net-only"></a><a name="entity-coordination"></a>Entitáskoordináció (jelenleg csak .NET esetén)

Előfordulhatnak olyan esetek, amikor több entitás közötti műveleteket kell koordinálnia. Egy banki alkalmazásban például előfordulhat, hogy olyan entitások vannak, amelyek egyedi bankszámlákat képviselnek. Amikor pénzt utal át egyik számláról a másikra, gondoskodnia kell arról, hogy a forrásszámla elegendő tőkével rendelkezzen. Azt is biztosítania kell, hogy a forrás- és a célfiókok frissítései tranzakciósan konzisztens módon legyenek elvégezve.

### <a name="example-transfer-funds-c"></a>Példa: Átutalás (C#)

A következő példa kód átutalások alapok két számla entitások egy orchestrator függvény használatával. Koordináló entitás frissítések `LockAsync` et igényel a módszer használatával, hogy hozzon létre egy _kritikus szakaszt_ a vezénylési.

> [!NOTE]
> Az egyszerűség kedvéért ez `Counter` a példa újrafelhasználja a korábban definiált entitást. Egy valós alkalmazásban jobb lenne egy részletesebb `BankAccount` entitásmeghatározása.

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

A .NET `LockAsync` `IDisposable`sorban a visszatérési értéket adja vissza, amely a kritikus szakaszt az ártalmatlanításkor befejezi. Ez `IDisposable` az eredmény egy `using` blokkdal együtt használható a kritikus szakasz szintaktikai ábrázolásának leállításához.

Az előző példában egy orchestrator függvény átutalt a pénzeszközöket egy forrás entitásegy cél entitás. A `LockAsync` metódus zárolta a forrás- és a célszámla-entitásokat is. Ez a zárolás biztosította, hogy egyetlen más ügyfél sem tudta lekérdezni vagy módosítani bármelyik fiók állapotát, amíg a vezénylési logika ki nem lép a kritikus szakaszból az `using` utasítás végén. Ez a viselkedés megakadályozza a folyószámlaolás lehetőségét a forrásfiókból.

> [!NOTE] 
> Amikor egy vezénylési leáll, akár normál, akár egy hiba, a folyamatban lévő kritikus szakaszok implicit módon véget ér, és az összes zárolás felszabadul.

### <a name="critical-section-behavior"></a>Kritikus szakasz működése

A `LockAsync` metódus létrehoz egy kritikus szakaszt egy vezénylési. Ezek a kritikus szakaszok megakadályozzák, hogy más vezénylések egymást átfedő módosításokat hajtsanak végre az entitások megadott készletén. Belsőleg az `LockAsync` API "lock" műveleteket küld az entitások és visszatér, amikor kap egy "lock beszerzett" válaszüzenetet minden egyes ugyanezen entitások. A zárolás és a feloldás egyaránt az összes entitás által támogatott beépített műveletek.

Más ügyfelektől származó műveletek nem engedélyezettek egy entitáson, amíg zárolt állapotban van. Ez a viselkedés biztosítja, hogy egyszerre csak egy vezénylési példány zárolhat egy entitást. Ha egy hívó megpróbál meghívni egy műveletet egy entitáson, miközben egy vezénylés zárolja, a művelet egy függőben lévő műveleti várólistába kerül. A függőben lévő műveletek feldolgozása addig nem lesz feldolgozva, amíg a tároló vezénylési folyamata fel nem oldja a zárolást.

> [!NOTE] 
> Ez a viselkedés némileg eltér a legtöbb programozási nyelvben használt `lock` szinkronizálási primitívektől, például a C#utasításban. A C#-ban például az `lock` utasítást minden szálnak használnia kell a megfelelő szinkronizálás érdekében több szál között. Entitások, azonban nem követeli meg az összes hívó explicit zárolása entitás. Ha bármelyik hívó zárol egy entitást, az adott entitás összes többi művelete le lesz tiltva, és a zárolás mögött várakozik.

Az entitások zárolása tartós, így akkor is megmaradnak, ha a végrehajtó folyamat újralett dolgozva. A zárolások az entitás tartós állapotának részeként belsőleg megmaradnak.

A tranzakcióktól eltérően a kritikus szakaszok nem gördítik vissza automatikusan a módosításokat hibák esetén. Ehelyett minden hibakezelést, például a visszaállítást vagy az újrapróbálkozást explicit módon kell kódolni, például hibák vagy kivételek felolvasásával. Ez a tervezési választás szándékos. A vezénylés összes hatásának automatikus visszaállítása általában nehéz vagy lehetetlen, mivel a vezénylések tevékenységeket futtathatnak, és olyan külső szolgáltatásokhívásait kezdeményezhetik, amelyek nem állíthatók vissza. A visszaállítási kísérletek önmagukban is sikertelenek lehetnek, és további hibakezelést igényelnek.

### <a name="critical-section-rules"></a>Kritikus szakasz szabályai

A legtöbb programozási nyelv alacsony szintű zárolási primitívjeivel ellentétben a kritikus szakaszok *garantáltan nem holtpontra jutnak.* A holtpontok elkerülése érdekében a következő korlátozásokat érvényesítjük: 

* A kritikus szakaszok nem ágyazhatók egymásba.
* A kritikus szakaszok nem hozhatnak létre alvegyezéseket.
* A kritikus szakaszok csak zárolt entitásokat hívhatnak meg.
* A kritikus szakaszok nem hívhatják meg ugyanazt az entitást több párhuzamos hívással.
* A kritikus szakaszok csak azokat az entitásokat jelezhetik, amelyeket nem zároltak.

A szabályok bármilyen megsértése futásidejű hibát `LockingRulesViolationException` okoz, például a .NET-ben, amely egy üzenetet tartalmaz, amely elmagyarázza, hogy melyik szabály volt megszegve.

## <a name="comparison-with-virtual-actors"></a>Összehasonlítás a virtuális szereplőkkel

Sok a tartós entitások funkciók ihlette az [aktor modell](https://en.wikipedia.org/wiki/Actor_model). Ha már ismeri a színészeket, előfordulhat, hogy felismeri a cikkben ismerte tett fogalmak nagy részét. Tartós szervezetek különösen hasonlóak a [virtuális szereplők](https://research.microsoft.com/projects/orleans/), vagy szemek, ahogy népszerűsítette az [Orleans projekt](http://dotnet.github.io/orleans/). Például:

* A tartós entitások egy entitásazonosítón keresztül címezhetők.
* A tartós entitásműveletek sorozatosan, egyenként, a versenykörülmények megelőzése érdekében hajthatók végre.
* A tartós entitások implicit módon jönnek létre, amikor meghívják vagy jelzik őket.
* Ha nem hajt végre műveleteket, a tartós entitások csendben törlődnek a memóriából.

Van néhány fontos különbség, amit érdemes megjegyezni:

* A tartós entitások előnyben részesítik a tartósságot a késéssel szemben, és ezért előfordulhat, hogy nem megfelelőek a szigorú késési követelményekkel rendelkező alkalmazásokhoz.
* A tartós entitások nem rendelkeznek beépített időtúltöltésekkel az üzenetekhez. Orleansban minden üzenet időtelenül egy konfigurálható idő után. Az alapértelmezett érték 30 mp.
* Az entitások között küldött üzenetek kézbesítése megbízhatóan és sorrendben történt. Orleansban a megbízható vagy megrendelt kézbesítés támogatott az adatfolyamokon keresztül küldött tartalmak esetében, de nem garantált a szemek közötti összes üzenet esetében.
* Az entitások kérelem-válasz mintázatai vezénylési műveletekre korlátozódnak. Belül entitások, csak egyirányú üzenetküldés (más néven jelző) megengedett, mint az eredeti színész modell, és ellentétben a szemek Orleans. 
* A tartós entitások nem holtpontra jutnak. Orleansban patthelyzet léphet fel, és nem oldódik meg, amíg az üzenetek időtúlóráznak.
* A tartós entitások tartós vezénylési műveletekkel együtt használhatók, és támogatják az elosztott zárolási mechanizmusokat. 


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Olvassa el a fejlesztői útmutatót a .NET tartós entitásaihoz](durable-functions-dotnet-entities.md)

> [!div class="nextstepaction"]
> [További információ a feladatközpontokról](durable-functions-task-hubs.md)
