---
title: Durable Functions előzetes verzió funkciói – Azure Functions
description: További információ a Durable Functions előzetes verziójának szolgáltatásairól.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: article
ms.date: 07/08/2019
ms.author: azfuncdf
ms.openlocfilehash: a64276de3e535c8b7724927ce2e257542cc9e01a
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164410"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Durable Functions 2,0 előzetes verzió (Azure Functions)

A *Durable Functions* [Azure functions](../functions-overview.md) és [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) bővítménye, amely lehetővé teszi az állapot-nyilvántartó függvények írását kiszolgáló nélküli környezetben. A bővítmény automatikusan kezeli az állapotokat, az ellenőrzőpontokat és az újraindításokat. Ha még nem ismeri a Durable Functionst, tekintse meg az [Áttekintés dokumentációját](durable-functions-overview.md).

Durable Functions 1. x a Azure Functions GA (általánosan elérhető) funkciója, de több olyan alszolgáltatást is tartalmaz, amelyek jelenleg nyilvános előzetes verzióban érhetők el. Ez a cikk az újonnan kiadott előzetes verziójú funkciókat ismerteti, és részletesen bemutatja, hogyan használhatók a működésük, és hogyan kezdhetik meg a használatot.

> [!NOTE]
> Ezek az előzetes verziójú funkciók egy Durable Functions 2,0 kiadás részét képezik, amely jelenleg az **előzetes verzió minőségi kiadása** , amely több feltörési változást tartalmaz. A Azure Functions tartós bővítmények csomagja a nuget.org **2.0.0-betaX**formátumú verziókkal érhető el. Ezek a buildek nem üzemi számítási feladatokhoz készültek, és a későbbi kiadásokban további feltörési változások is előfordulhatnak.

## <a name="breaking-changes"></a>Kompatibilitástörő változások

A Durable Functions 2,0-es verzióban több törési változást is bevezetünk. A meglévő alkalmazások nem lesznek kompatibilisek a Durable Functions 2,0-mel a kód módosítása nélkül. Ez a szakasz néhány változást felsorol:

### <a name="hostjson-schema"></a>Host. JSON séma

A következő kódrészlet a Host. JSON új sémáját mutatja be. Az új alszakaszok a legfontosabb változások, hogy tisztában legyenek a következőkkel:

* `"storageProvider"`(és az `"azureStorage"` alszakasz) a Storage-specifikus konfigurációhoz
* `"tracking"`a nyomon követés és a naplózás konfigurálásához
* `"notifications"`(és az `"eventGrid"` alszakasz) az Event Grid értesítési konfigurációjához

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "azureStorage": {
          "connectionStringName": <string>,
          "controlQueueBatchSize": <int?>,
          "partitionCount": <int?>,
          "controlQueueVisibilityTimeout": <hh:mm:ss?>,
          "workItemQueueVisibilityTimeout": <hh:mm:ss?>,
          "trackingStoreConnectionStringName": <string?>,
          "trackingStoreNamePrefix": <string?>,
          "maxQueuePollingInterval": <hh:mm:ss?>
        }
      },
      "tracking": {
        "traceInputsAndOutputs": <bool?>,
        "traceReplayEvents": <bool?>,
      },
      "notifications": {
        "eventGrid": {
          "topicEndpoint": <string?>,
          "keySettingName": <string?>,
          "publishRetryCount": <string?>,
          "publishRetryInterval": <hh:mm:ss?>,
          "publishRetryHttpStatus": <int[]?>,
          "publishEventTypes": <string[]?>,
        }
      },
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "customLifeCycleNotificationHelperType": <string?>
  }
}
```

Mivel a Durable functions 2,0 továbbra is stabilizálódik, a `durableTask` Host. JSON szakasz további módosításait fogja bevezetni. A változásokkal kapcsolatos további információkért tekintse meg [ezt a GitHub-problémát](https://github.com/Azure/azure-functions-durable-extension/issues/641).

### <a name="public-interface-changes"></a>Nyilvános interfész módosításai

A Durable Functions által támogatott különböző környezeti objektumok absztrakt alaposztályok voltak, amelyek az egység tesztelése során használhatók. A Durable Functions 2,0 részeként ezek az absztrakt alaposztályok le lettek cserélve a csatolókkal. A konkrét típusokat használó függvény kódját nem érinti a rendszer.

A fő módosításokat a következő táblázat mutatja be:

| Régi típus | Új típus |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |

Abban az esetben, ha egy absztrakt alaposztály virtuális metódusokat tartalmaz, ezeket a virtuális metódusokat a ben `DurableContextExtensions`definiált kiterjesztési módszerek váltották fel.

## <a name="entity-functions"></a>Entitás-függvények

Az Entity functions olyan műveleteket határoz meg, amelyek olyan kis méretű állapotok olvasására és frissítésére szolgálnak, amelyek *tartós entitások*. A Orchestrator függvényekhez hasonlóan az Entity functions is egy speciális trigger típussal, az *entitások triggerével*működik. A Orchestrator függvényektől eltérően az Entity functions nem rendelkezik konkrét kód megkötésekkel. Az Entity functions emellett explicit módon kezeli az állapotot, nem pedig implicit módon jelképezi az állapotot a vezérlési folyamaton keresztül.

### <a name="net-programing-models"></a>.NET-programozási modellek

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

Ez a modell az egyszerű entitások megvalósításához, illetve dinamikus műveletekkel rendelkező megvalósításokhoz használható. Van azonban olyan osztály-alapú programozási modell is, amely olyan entitások esetében hasznos, amelyek statikusak, de összetettebb implementációval rendelkeznek. A következő példa az `Counter` entitás egyenértékű implementációját használja .net-osztályok és-metódusok használatával.

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

Az osztály alapú modell hasonló a [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/)által népszerűsítette programozási modellhez. Ebben a modellben az entitás típusa .NET-osztályként van definiálva. Az osztály minden metódusa egy olyan művelet, amelyet külső ügyfél hívhat meg. A Orleanstól eltérően azonban a .NET-felületek nem kötelezőek. Az előző *számláló* példa nem használ felületet, de más függvények vagy http API-hívások útján is meghívható.

Az entitás-példányok egy egyedi azonosítóval, az *entitás azonosítójának*használatával érhetők el. Az entitás-AZONOSÍTÓk egyszerűen olyan karakterláncok, amelyek egyedileg azonosítanak egy entitás-példányt. A következőkből áll:

* Az **entitás neve**: az entitás típusát azonosító név (például "számláló").
* **Entitás kulcsa**: olyan karakterlánc, amely egyedileg azonosítja az entitást az azonos nevű entitások között (például egy GUID azonosító).

Előfordulhat például, hogy egy *számláló* entitás függvényt egy online játékban kell tartani. A játék minden példánya egyedi azonosítóval fog rendelkezni, például `@Counter@Game1`, `@Counter@Game2`, stb.

### <a name="comparison-with-virtual-actors"></a>Összehasonlítás virtuális szereplőkkel

A tartós entitások kialakítását nagy mértékben befolyásolja a [színészi modell](https://en.wikipedia.org/wiki/Actor_model). Ha már ismeri a szereplőket, akkor a tartós entitások mögötti fogalmakat ismernie kell. A tartós entitások többek között a [virtuális szereplőkhöz](https://research.microsoft.com/projects/orleans/) hasonlóak:

* A tartós entitások az *entitás azonosítóján*keresztül címezhető.
* A tartós entitások műveletei a verseny feltételeinek megelőzése érdekében egyszerre hajtanak végre sorosan.
* A tartós entitásokat a rendszer automatikusan hozza létre, amikor meghívja őket vagy jelezték.
* Ha nem hajtja végre a műveleteket, a tartós entitások csendesen törlődnek a memóriából.

Van azonban néhány fontos különbség, amelyeket érdemes megjegyezni:

* A tartós entitások a késleltetést rangsorolják a *késéshez*képest, ezért nem feltétlenül alkalmazhatók szigorú késési követelményekkel rendelkező alkalmazások esetében.
* Az entitások között küldött üzenetek megbízhatóan és sorrendben lesznek kézbesítve.
* A tartós entitások tartós előkészítésekkel együtt használhatók, és elosztott zárolásként szolgálnak, amelyek a jelen cikk későbbi részében olvashatók.
* Az entitásokban a kérelmek és válaszok mintája csak az előkészítésre korlátozódik. Az entitások közötti kommunikációhoz csak egy egyirányú üzenet (más néven "jelzés") engedélyezett, ahogy az eredeti modellben is. Ez a viselkedés megakadályozza az elosztott holtpontokat.

### <a name="durable-entity-net-apis"></a>Tartós entitás .NET API-k

Az entitások támogatása több API-t is magában foglal. Egy új API az Entity functions definiálásához, ahogy az a fentiekben is látható, amely azt határozza meg, hogy mi történjen a művelet egy entitáson való meghívásakor. Emellett a meglévő API-k az ügyfelekhez és a munkafolyamatokhoz új funkciókkal frissültek az entitásokkal való interakcióhoz.

#### <a name="implementing-entity-operations"></a>Entitások műveleteinek implementálása

Egy entitáson végzett művelet végrehajtása meghívja ezeket a tagokat a környezeti objektumon (`IDurableEntityContext` .net):

* **OperationName**: lekéri a művelet nevét.
* **GetInput\<TInput >** : lekéri a művelet bemenetét.
* **GetState\<TState->** : az entitás aktuális állapotát kapja meg.
* **SetState**: frissíti az entitás állapotát.
* **SignalEntity**: egyirányú üzenet küldése egy entitásnak.
* **Saját**: az entitás azonosítóját kapja meg.
* **Return**: egy értéket ad vissza a műveletnek nevezett ügyfélnek vagy összehangolás értékének.
* **IsNewlyConstructed**: azt `true` adja vissza, hogy az entitás nem létezett-e a művelet előtt.
* **DestructOnExit**: a művelet befejezése után törli az entitást.

A műveletek kevésbé vannak korlátozva, mint a hangszerelések:

* A műveletek külső I/O-műveleteket is meghívhatnak szinkron vagy aszinkron API-k használatával (csak aszinkron módon ajánlott).
* A műveletek lehetnek nem determinisztikus. Például nyugodtan hívható `DateTime.UtcNow` `Guid.NewGuid()` vagy `new Random()`.

#### <a name="accessing-entities-from-clients"></a>Entitások elérése az ügyfelektől

A tartós entitásokat a szokásos függvényekből lehet meghívni `orchestrationClient` a kötés`IDurableOrchestrationClient` használatával (.net-ben). A következő módszerek támogatottak:

* **ReadEntityStateAsync\<T >** : egy entitás állapotának beolvasása.
* **SignalEntityAsync**: egyirányú üzenetet küld egy entitásnak, és megvárja, amíg a várólistán lévő.
* **A\<SignalEntityAsync T >** : ugyanaz `SignalEntityAsync` , mint a generált proxy típusú `T`objektum.

Az előző `SignalEntityAsync` híváshoz meg kell adni az entitás `string` -művelet nevét és a művelet `object`hasznos adatait. Az alábbi mintakód példa erre a mintára:

```csharp
EntityId id = // ...
object amount = 5;
context.SignalEntityAsync(id, "Add", amount);
```

A típus – biztonságos hozzáféréshez is létrehozhat egy proxy objektumot. Egy típus – biztonságos proxy létrehozásához az entitás típusának meg kell valósítania egy felületet. Tegyük fel például, `Counter` hogy a korábban említett entitás `ICounter` egy felületet adott meg, amelyet a következőképpen határoz meg:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    int Get();
}

public class Counter : ICounter
{
    // ...
}
```

Az ügyfél kódja ezután felhasználható `SignalEntityAsync<T>` , és a Type paraméterként megadható az `ICounter` illesztőfelület típus-biztonságos proxy létrehozásához. A típus-biztonságos proxyk használatát a következő mintakód mutatja be:

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [OrchestrationClient] IDurableOrchestrationClient client)
{
    int amount = int.Parse(message);
    var target = new EntityId(nameof(Counter), "MyCounter");
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

Az előző példában `proxy` a paraméter a dinamikusan generált `ICounter`példánya, amely belsőleg `Add` lefordítja a hívást az `SignalEntityAsync`egyenértékű (nem típusos) hívásra.

A Type paraméter `SignalEntityAsync<T>` a következő korlátozásokkal rendelkezik:

* A Type paraméternek illesztőfelületnek kell lennie.
* Csak metódusok határozhatók meg az illesztőfelületen. A tulajdonságok nem támogatottak.
* Minden metódusnak definiálnia kell egy vagy több paramétert.
* Minden metódusnak a következőt `Task`kell visszaadnia `void`:, vagy `Task<T>` ha `T` egy bizonyos JSON-serializeable típus.
* Az illesztőfelületet pontosan egy típussal kell megvalósítani a csatoló szerelvényén belül.

A legtöbb esetben a fenti követelményeknek meg nem felelő felületek futtatókörnyezeti kivételt eredményeznek.

> [!NOTE]
> Fontos megjegyezni, hogy a és `ReadEntityStateAsync` `SignalEntityAsync` a módszer `IDurableOrchestrationClient` a teljesítmény prioritással való rangsorolására szolgál. `ReadEntityStateAsync`egy elavult értéket adhat vissza, és `SignalEntityAsync` visszatérhet a művelet befejeződése előtt.

#### <a name="accessing-entities-from-orchestrations"></a>Entitások elérése a munkafolyamatokból

Az objektumok az objektum használatával férhetnek hozzá `IDurableOrchestrationContext` az entitásokhoz. Választhatnak egyirányú kommunikáció (tűz és felejtsd) és kétirányú kommunikáció (kérelem és válasz) között. A megfelelő módszerek a következők:

* **SignalEntity**: egyirányú üzenet küldése egy entitásnak.
* **CallEntityAsync**: üzenetet küld egy entitásnak, és megvárja a választ, amely jelzi, hogy a művelet befejeződött.
* **CallEntityAsync\<T >** : üzenetet küld egy entitásnak, és megvárja a T típusú eredményt tartalmazó választ.

Kétirányú kommunikáció használatakor a művelet végrehajtása során felmerülő kivételeket a rendszer visszaküldi a hívási előkészítésbe, és újradobta a műveletet. Ezzel szemben, ha tűz-és elfelejti a használatot, a rendszer nem figyeli a kivételeket.

A típus-biztonságos hozzáféréshez az összehangoló függvények egy felületen alapuló proxykat is létrehozhatnak. A `CreateEntityProxy` kiterjesztési módszer erre a célra használható:

```csharp
public interface IAsyncCounter
{
    Task AddAsync(int amount);
    Task ResetAsync();
    Task<int> GetAsync();
}

[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...
    IAsyncCounter proxy = context.CreateEntityProxy<IAsyncCounter>("MyCounter");
    await proxy.AddAsync(5);
    int newValue = await proxy.GetAsync();
    // ...
}
```

Az előző példában egy "számláló" entitást feltételeztek, amely megvalósítja a `IAsyncCounter` felületet. A rendszer ezután a `IAsyncCounter` típus definíciójának használatával létrehoz egy proxy-típust az entitással való szinkron interakcióhoz.

### <a name="locking-entities-from-orchestrations"></a>Entitások zárolásának kiszervezése

A munkafolyamatok zárolják az entitásokat. Ez a funkció egyszerű módszert kínál a nemkívánatos versenyek *kritikus részekkel*való elkerülésére.

A környezeti objektum a következő módszereket biztosítja:

* **LockAsync**: zárolások beszerzése egy vagy több entitáson.
* **IsLocked**: igaz értéket ad vissza, ha jelenleg kritikus szakaszban van, ellenkező esetben false.

A kritikus szakasz véget ér, és minden zárolás megjelent, amikor a folyamat véget ér. A .net `LockAsync` -ben egy `IDisposable` olyan értéket ad vissza, amely a kritikus szakaszt a használaton kívüli állapotba helyezi, amely együtt `using` használható záradékkal a kritikus szakasz szintaktikai megjelenítésének lekéréséhez.

Vegyünk például egy olyan előkészítést, amelynek meg kell vizsgálnia, hogy két játékos elérhető-e, majd rendelje hozzá őket a játékhoz. Ez a feladat a következő kritikus szakasz használatával valósítható meg:

```csharp
[FunctionName("Orchestrator")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext ctx)
{
    EntityId player1 = /* ... */;
    EntityId player2 = /* ... */;

    using (await ctx.LockAsync(player1, player2))
    {
        bool available1 = await ctx.CallEntityAsync<bool>(player1, "is-available");
        bool available2 = await ctx.CallEntityAsync<bool>(player2, "is-available");

        if (available1 && available2)
        {
            Guid gameId = ctx.NewGuid();

            await ctx.CallEntityAsync(player1, "assign-game", gameId);
            await ctx.CallEntityAsync(player2, "assign-game", gameId);
        }
    }
}
```

A kritikus szakaszban a lejátszó entitások is zárolva vannak, ami azt jelenti, hogy a kritikus szakaszban meghívott semmilyen műveletet nem hajtanak végre. Ez a viselkedés megakadályozza az ütköző műveletekkel rendelkező versenyeket, például a játékosok egy másik játékhoz való hozzárendelését vagy a kijelentkezést.

Számos korlátozást alkalmazunk a kritikus fontosságú profilok használatára. Ezek a korlátozások a holtpontok és a újbóli belépés megakadályozására szolgálnak.

* A kritikus szakaszt nem lehet beágyazni.
* A kritikus szakaszban nem hozhatók létre alfolyamatok.
* A kritikus szakaszt csak a zárolt entitások hívhatják meg.
* A kritikus szakaszban nem hívható meg ugyanaz az entitás több párhuzamos hívás használatával.
* A kritikus szakaszt csak azok az entitások jelezhetik, amelyek nincsenek zárolva.

## <a name="alternate-storage-providers"></a>Alternatív tároló szolgáltatók

A tartós feladathoz tartozó keretrendszer több tárolási szolgáltatót is támogat, [](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus)beleértve az [Azure Storage](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage)-t, a Azure Service Bust, [a memóriában lévő emulátort](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)és egy kísérleti [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) -szolgáltatót. Eddig azonban a Azure Functions tartós feladatának kiterjesztése csak az Azure Storage-szolgáltatót támogatja. A Durable Functions 2,0-től kezdve a helyettesítő tároló-szolgáltatók támogatása a Redis-szolgáltatótól kezdődően történik.

> [!NOTE]
> A Durable Functions 2,0 csak a .NET Standard 2,0-kompatibilis szolgáltatók használatát támogatja. Az írás időpontjában a Azure Service Bus-szolgáltató nem támogatja a .NET Standard 2,0-es kiadását, ezért nem érhető el alternatív tárolási szolgáltatóként.

### <a name="emulator"></a>Emulátor

A [DurableTask. Emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) szolgáltató egy helyi memória, nem tartós tárolási szolgáltató, amely helyi tesztelési forgatókönyvekhez alkalmas. A következő minimális **gazdagép. JSON** séma használatával konfigurálható:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { "enabled": true }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>Redis (kísérleti)

A [DurableTask. Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) szolgáltató az összes előkészítési állapotot egy konfigurált Redis-fürtön tartja fenn.

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "redis": {
          "connectionStringName": <string>,
        }
      }
    }
  }
}
```

Az `connectionStringName` alkalmazásnak egy Alkalmazásbeállítások vagy környezeti változó nevére kell hivatkoznia. Az Alkalmazásbeállítások vagy a környezeti változónak a Redis-karakterlánc értékének kell lennie a *kiszolgáló: Port*formában. Például `localhost:6379` egy helyi Redis-fürthöz való csatlakozáshoz.

> [!NOTE]
> A Redis szolgáltató jelenleg kísérleti jellegű, és csak egyetlen csomóponton futó Function apps-alkalmazásokat támogatja. Nem garantált, hogy a Redis szolgáltató általánosan elérhetővé válik, és előfordulhat, hogy egy későbbi kiadásban is el lesz távolítva.
