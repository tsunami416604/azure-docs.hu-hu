---
title: Durable Functions előzetes verziójú funkciók – az Azure Functions
description: További információk az előzetes verziójú funkciók Durable Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.date: 04/23/2019
ms.author: azfuncdf
ms.openlocfilehash: 8ceb84ab9e9c41ff6a9cbde62571fb12ae67d790
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65596078"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Durable Functions 2.0 – előzetes verzió (az Azure Functions)

*Durable Functions* kiterjesztése [Azure Functions](../functions-overview.md) és [Azure webjobs-feladatok](../../app-service/web-sites-create-web-jobs.md) , amellyel írási állapot-nyilvántartó functions egy kiszolgáló nélküli környezetben. A bővítmény automatikusan kezeli az állapotokat, az ellenőrzőpontokat és az újraindításokat. Ha még nem ismeri a Durable Functions, tekintse meg a [áttekintése dokumentáció](durable-functions-overview.md).

Durable Functions az Azure Functions szolgáltatása általánosan elérhető (az általánosan elérhető), de több alfunkció, amely jelenleg nyilvános előzetes verzióban is tartalmaz. Ez a cikk ismerteti az újonnan kiadott előzetes verziójú funkciók és lépnek részletek hogyan működnek és hogyan megkezdheti őket.

> [!NOTE]
> Durable Functions 2.0 kiadás, amely jelenleg ezen előzetes verziójú funkciók tartoznak egy **kiadás az alfa minőségi** a több kompatibilitástörő változásokat. Az Azure Functions tartós kiterjesztési csomagot hoz létre található formájában verzióival nuget.org webhelyen **2.0.0-alpha**. Ezek a buildek nem megfelelő az éles számítási feladatokat, és későbbi kiadásokban további kompatibilitástörő változásokat tartalmazhat.

## <a name="breaking-changes"></a>Kompatibilitástörő változások

Durable Functions 2.0 számos kompatibilitástörő változásokat jelennek meg. Meglévő alkalmazások kódmódosítás nélkül Durable Functions 2.0-val kompatibilis nem várt. Ez a szakasz néhány változását ismerteti:

### <a name="dropping-net-framework-support"></a>.NET-keretrendszer támogatási eldobása

Durable Functions 2.0 támogatása, .NET-keretrendszer (és így függvények 1.0) el lett dobva. Az elsődleges oka nem Windows közreműködők hozhat létre és tesztelheti a módosításokat a macOS és Linux platformokon vállalnak Durable Functions engedélyezéséhez. A másodlagos okból azt javasoljuk a fejlesztők számára, hogy helyezze át az Azure Functions futtatókörnyezet legújabb verziójára.

### <a name="hostjson-schema"></a>Host.json schema

Az alábbi kódrészlet bemutatja az új host.json sémája. Érdemes figyelembe vennie a fő módosítás az új `"storageProvider"` szakaszt, és a `"azureStorage"` szakasz alatta. Ez a módosítás azért volt szükség, támogatja a [az eredetitől eltérő tárolási szolgáltatók](durable-functions-preview.md#alternate-storage-providers).

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
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "traceInputAndOutputs": <bool?>,
      "eventGridTopicEndpoint": <string?>,
      "eventGridKeySettingName": <string?>,
      "eventGridPublishRetryCount": <string?>,
      "eventGridPublishRetryInterval": <hh:mm:ss?>,
      "eventGridPublishRetryHttpStatus": <int[]?>,
      "eventgridPublishEventTypes": <string[]?>,
      "customLifeCycleNotificationHelperType"
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "logReplayEvents": <bool?>
  }
}
```

Durable Functions 2.0 kerülési továbbra is fennáll, ahogy további módosításokat vezeti be a `durableTask` host.json szakaszban. További információ ezekről a változásokról, tekintse meg a [a GitHub-problémát](https://github.com/Azure/azure-functions-durable-extension/issues/641).

### <a name="public-interface-changes"></a>Nyilvános felület módosítása

A különböző "környezet" objektumok Durable Functions által támogatott egységtesztelés felhasználásra szánt absztrakt alaposztályok rendelkezett. Durable Functions 2.0 része ezek absztrakt alaposztályok vette felületek. A típusnak közvetlenül használó függvénykód nem érinti.

Az alábbi táblázat a legfontosabb változások jelöli:

| Régi típusú | Új típus |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |

Abban az esetben, ha az egy absztrakt alaposztálya tartalmazott virtuální metody, ezek a virtuális módszerek meghatározott bővítő metódusok lettek cserélve `DurableContextExtensions`.

## <a name="entity-functions"></a>Entitás funkciók

Entitás függvények határozzák meg a műveletek olvasása és frissítése kisebb kódrészletek, más néven az állapotban *tartós entitások*. Az orchestrator-funkciók, például entitás funkciók a functions és a egy speciális trigger típusa *entitás eseményindító*. Az orchestrator-funkciók, ellentétben entitás függvények nem rendelkezik konkrét kódot korlátozások. Entitás funkciók is állapot kezelése helyett explicit módon implicit módon jelző állapot átvitelvezérlés keresztül.

Az alábbi kód egy egyszerű entitás függvény, amely meghatározza a példája egy *számláló* entitás. A függvény meghatározza a három műveleti `add`, `subtract`, és `reset`, minden, ami frissíteni egy egész számot `currentValue`.

```csharp
[FunctionName("Counter")]
public static async Task Counter(
    [EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();
    int operand = ctx.GetInput<int>();

    switch (ctx.OperationName)
    {
        case "add":
            currentValue += operand;
            break;
        case "subtract":
            currentValue -= operand;
            break;
        case "reset":
            await SendResetNotificationAsync();
            currentValue = 0;
            break;
    }

    ctx.SetState(currentValue);
}
```

Entitás *példányok* érhetők el egy egyedi azonosítót a *Entitásazonosító*. Egy entitás csomagazonosítója egyszerűen két karakterláncot, amely egyértelműen azonosít egy entitáspéldányt. Ez a következőkből áll:

1. egy **entitásnév**: egy nevet, amely azonosítja az entitást típusa (például, "Számláló")
2. egy **entitáskulcs**: egy karakterlánc, amely egyedileg azonosítja az entitást, minden más entitásét az azonos nevű (például egy GUID-azonosítója) között

Ha például egy *számláló* entitás függvény használhatók például egy online játéka pontszám tartja. A játék minden példánya lesz egy egyedi entitás azonosítója például `@Counter@Game1`, `@Counter@Game2`, és így tovább.

### <a name="comparison-with-virtual-actors"></a>A virtuális actors összehasonlítása

Tartós entitások kialakítása jelentősen befolyásolja a [actor modell](https://en.wikipedia.org/wiki/Actor_model). Ha már ismeri a actors, tartós entitások fogalmakat ismerős lehet. Különösen tartós entitások hasonlóak a [virtuális actors](https://research.microsoft.com/en-us/projects/orleans/) számos módon:

* Tartós entitások címezhető keresztül legyenek egy *Entitásazonosító*.
* Tartós entitás műveletek végrehajtása tárolókonfigurációhoz, egyenként, olyan versenyhelyzetekben elkerülése érdekében.
* Tartós entitásokat automatikusan jönnek létre, ha nevű vagy jelzést.
* Nem műveletek végrehajtásakor, tartós entitások memóriából csendes módban.

Van néhány fontos eltérés azonban, amelyek megjegyezni:

* Tartós entitások tiszta funkciók vannak modellezve. Ez a kialakítás különbözik a legtöbb actors osztályok, tulajdonságok és metódusok használatával a nyelvspecifikus támogatási képviselő objektum-orientált keretrendszereket.
* Tartós entitások rangsorolhatja *tartóssági* keresztül *késés*, és így nem lehet szigorú késési követelményekkel rendelkező alkalmazások esetében.
* Entitások között küldött üzenetek megbízható és sorrendben érkeznek.
* Tartós entitások tartós vezénylések együtt is használható, és elosztott zárolásokat, ez a cikk egy későbbi része ismerteti, amely alapul szolgálhat.
* Az entitások kérés/válasz minták vezénylések korlátozódnak. Entitás kommunikációhoz (a más néven "jelzés") csak egyirányú üzenetek engedélyezve vannak, mint az eredeti actor modell. Ez a viselkedés megakadályozza, hogy elosztott holtpontok.

### <a name="durable-entity-apis"></a>Tartós entitás API-k

Entitás-támogatás magában foglalja a több API-t. Egy van egy új API definiálása entitások funkciók, jelennek meg, amely adja meg, hogy mi történjen, ha a művelet meghívása egy entitására. Emellett a meglévő API-k, az ügyfelek és a vezénylések frissítve lett-e az új funkciókkal entitások való együttműködéshez szükséges.

### <a name="implementing-entity-operations"></a>Entitás műveletek végrehajtása

Egy entitás egy művelet végrehajtását ezekről a tagokról meghívhatja a környezeti objektumon (`IDurableEntityContext` a .NET-ben):

* **OperationName**: a művelet nevét olvassa be.
* **GetInput\<T >** : a bemeneti adatok beolvasása a művelethez.
* **GetState\<T >** : az entitás aktuális állapotát.
* **SetState**: frissíti az entitás állapotát.
* **SignalEntity**: egyirányú üzenetet küld az entitáshoz.
* **Önkiszolgáló**: lekérdezi az entitás azonosítója.
* **Vissza**: az ügyfél vagy az orchestration, amelynek a neve, a művelet egy értéket ad vissza.
* **IsNewlyConstructed**: ad vissza `true` , ha az entitás nem létezik. a művelet előtt.
* **DestructOnExit**: az entitás törlése a művelet befejezése után.

Kevesebb korlátozott, mint a vezénylések műveletek a következők:

* Műveletek segítségével meghívhatja a külső I/O (azt javasoljuk, hogy csak az aszinkron azokat) szinkron vagy aszinkron API-k használatával.
* Lehet, hogy a műveletek nem determinisztikus. Ha például biztonságosan meg lehet hívni `DateTime.UtcNow`, `Guid.NewGuid()` vagy `new Random()`.

### <a name="accessing-entities-from-clients"></a>Az ügyfelek a entitások elérése

Tartós entitásokat is elindítható a normál funkciók segítségével a `orchestrationClient` kötés (`IDurableOrchestrationClient` a .NET-ben). A következő módszereket támogatja:

* **ReadEntityStateAsync\<T >** : beolvassa egy entitás állapotát.
* **SignalEntityAsync**: egyirányú üzenetet küld egy entitás, és megvárja, hogy a várólistán lévő el.

Ezek a metódusok teljesítmény rangsorolhatja konzisztencia keresztül: `ReadEntityStateAsync` térhet vissza a régi értéket, és `SignalEntityAsync` adhatnak vissza, mielőtt befejezte a műveletet. Ezzel szemben a vezénylések megismernie entitások (következő) módon erősen konzisztens.

### <a name="accessing-entities-from-orchestrations"></a>A vezénylések entitások elérése

Vezénylések férhetnek hozzá a context objektumot használatával. Ezek egyirányú kommunikációs választhat (indul el, és felejtse el) és a kétirányú kommunikáció (kérések és válaszok). A megfelelő módszerek

* **SignalEntity**: egyirányú üzenetet küld az entitáshoz.
* **CallEntityAsync**: egy üzenetet küld egy entitás, és megvárja, amíg egy választ, amely jelzi, hogy a művelet befejeződött.
* **CallEntityAsync\<T >** : egy üzenetet küld egy entitás, és megvárja, amíg egy választ, amely tartalmazza a T. típusú eredményt

Kétirányú kommunikáció használatakor az olyan kivételek, a művelet végrehajtása közben lépett fel is továbbítják a hívó vezénylési és rethrown. Ezzel szemben a fire és elfelejt használatakor az kivételek nem érvényesek.

### <a name="locking-entities-from-orchestrations"></a>A vezénylések zárolási entitások

Vezénylések zárolható entitásokat. Ez a képesség nyújt egy egyszerű lehet akadályozni, hogy a nem kívánt végigszaladsz használatával *kritikus szakaszok*.

A context objektumot az alábbi módszereket biztosítja:

* **LockAsync**: szerez be egy vagy több entitás zárolása.
* **IsLocked**: hamis értéket ad vissza IGAZ, ha jelenleg az egyik kritikus szakaszban, ellenkező esetben.

A kritikus szakasza véget ér, és az összes zárolás kiadott, amikor az orchestration ér véget. A .NET-ben `LockAsync` adja vissza egy `IDisposable` , amely befejezi a kritikus szakasza eldobva, amikor együtt használható egy `using` a kritikus szakasza szintaktikai alak záradékot.

Például érdemes lehet folyamattevékenységek vezénylése, amely annak megállapítására, hogy két játékos érhetők el kell, és hozzárendelheti azokat játék mindkettőt. Ez a feladat segítségével valósítható meg a kritikus szakasza a következő:

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

A kritikus fontosságú területen belül mindkét player entitások zárolva vannak, ami azt jelenti, hogy azok nem futtatja azokat a műveleteket, amelyek a kritikus fontosságú szakaszokon belül meghívni a eltérő). Ez a viselkedés megakadályozza, hogy az ütköző műveletek végigszaladsz, például a játékosok érvényessége a felhasználóhoz egy másik játék-, vagy aláírási kikapcsolva.

Több korlátozás vonatkozik írnak elő, hogy hogyan kritikus szakaszokat is használható. Ezek a korlátozások holtpontok és – újbóli belépés megakadályozására szolgál.

* Kritikus fontosságú szakaszok nem ágyazhatók egymásba.
* Kritikus fontosságú szakaszok suborchestrations nem hozható létre.
* Kritikus fontosságú szakaszok meghívhat csak azok zárolt entitások.
* Kritikus fontosságú szakaszok nelze volat használatával több párhuzamos hívások ugyanahhoz az entitáshoz.
* Kritikus fontosságú szakaszok is jelezze csak azok nem zárolt entitások.

## <a name="alternate-storage-providers"></a>Alternatív tárolási szolgáltatók

Tartós feladat keretében támogatja több tárolási szolgáltató még ma, beleértve a [Azure Storage](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage), [Azure Service Bus](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus), egy [memórián belüli emulátor](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator), és a egy kísérleti [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) szolgáltató. Azonban eddig a tartós tevékenység kiterjesztése az Azure Functions csak támogatott az Azure Storage-szolgáltató. Durable Functions 2.0, alternatív tárolási szolgáltatók támogatása kezdve ad hozzá, a Redis-szolgáltató indítása.

> [!NOTE]
> Durable Functions 2.0 csak a .NET Standard 2.0-kompatibilis szolgáltatók támogatja. Írása idején az Azure Service Bus-szolgáltató nem támogatja a .NET Standard 2.0, és ezért nem érhető el, mint egy másik szolgáltató.

### <a name="emulator"></a>Emulátor

A [DurableTask.Emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) szolgáltató egy helyi memória, helyi tesztelés szituációkra nem tartós tárolási szolgáltatót. Az alábbi minimális konfigurálható **host.json** séma:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>A redis (kísérleti funkció)

A [DurableTask.Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) szolgáltató továbbra is fennáll, az összes vezénylési állapot konfigurált Redis-fürttel.

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

A `connectionStringName` hivatkoznia kell egy alkalmazás beállítás vagy a környezeti változó neve. Adott alkalmazás beállítás vagy a környezeti változó tartalmaznia kell egy Redis kapcsolati karakterlánc értékét formájában *server: port*. Ha például `localhost:6379` helyi Redis-fürttel való kapcsolódáshoz.

> [!NOTE]
> A Redis-szolgáltató jelenleg kísérleti, és csak az egyetlen csomóponton futó függvény alkalmazásokat támogatja.
