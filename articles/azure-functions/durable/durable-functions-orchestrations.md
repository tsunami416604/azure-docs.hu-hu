---
title: Tartós vezénylések – Azure-függvények
description: Bevezetés az Azure Durable Functions vezénylési funkciójába.
author: cgillum
ms.topic: overview
ms.date: 09/08/2019
ms.author: azfuncdf
ms.openlocfilehash: caa62483373a240991cfec96437cea7849d9b19c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241359"
---
# <a name="durable-orchestrations"></a>Tartós vezénylések

A Durable Functions az [Azure Functions](../functions-overview.md)kiterjesztése. Egy *orchestrator függvény* taszthat ja vezénylheti a függvényalkalmazáson belüli egyéb tartós függvények végrehajtását. Az Orchestrator függvények a következő jellemzőkkel rendelkeznek:

* Az Orchestrator függvényei eljárási kód használatával definiálják a függvénymunkafolyamatokat. Nincs szükség deklaratív sémákra vagy tervezőkre.
* Az Orchestrator-függvények szinkron és aszinkron módon hívhatnak meg más tartós függvényeket is. Az úgynevezett függvények kimenete megbízhatóan menthető a helyi változókba.
* Az Orchestrator függvények tartósak és megbízhatóak. A végrehajtás folyamata automatikusan ellenőrzőpont, amikor a funkció "vár" vagy "hozamok". A helyi állapot soha nem vész el, ha a folyamat újrahasznosítja, vagy a virtuális gép újraindul.
* Az Orchestrator függvények hosszú ideig futóak lehetnek. Egy *vezénylési példány* teljes élettartama lehet másodperc, nap, hónap vagy soha véget nem érő.

Ez a cikk áttekintést nyújt az orchestrator függvényeiről, és arról, hogyan segíthetnek a különböző alkalmazásfejlesztési kihívások megoldásában. Ha még nem ismeri a Durable Functions alkalmazásban elérhető függvénytípusokat, először olvassa el a [Tartós függvénytípusok](durable-functions-types-features-overview.md) című cikket.

## <a name="orchestration-identity"></a>Vezénylési identitás

A vezénylés minden *példánya* rendelkezik egy példányazonosítóval (más néven *példányazonosítóval).* Alapértelmezés szerint minden példányazonosító egy automatikusan generált GUID. A példányazonosítók azonban bármilyen felhasználó által létrehozott karakterlánc-érték is lehet. Minden vezénylési példányazonosítónak egyedinek kell lennie a [feladatközpontban.](durable-functions-task-hubs.md)

A példányazonosítókra vonatkozó szabályok az alábbiakban találhatók:

* A példányazonosítók nak 1 és 256 karakter között kell lenniük.
* A példányazonosítók nem `@`kezdődnek .
* A példányazonosítók nem `/` `\`tartalmazhatnak , , `#`, vagy `?` karaktereket.
* A példányazonosítók nem tartalmazhatnak vezérlőkaraktereket.

> [!NOTE]
> Általában ajánlott automatikusan generált példányazonosítókat használni, amikor csak lehetséges. A felhasználó által létrehozott példányazonosítók olyan esetekben, ahol egy-az-egyhez leképezés egy vezénylési példány és néhány külső alkalmazás-specifikus entitás, például egy beszerzési rendelés vagy egy dokumentum között.

A vezénylési példányazonosító a legtöbb példánykezelési művelet szükséges [paramétere.](durable-functions-instance-management.md) A diagnosztika szempontjából is fontosak, például az Application Insights [vezénylési nyomon követési adatainak kereséséhez](durable-functions-diagnostics.md#application-insights) hibaelhárítási vagy elemzési célokra. Ezért ajánlott a létrehozott példányazonosítókat menteni valamilyen külső helyre (például egy adatbázisba vagy az alkalmazásnaplókba), ahol később könnyen hivatkozhatnak.

## <a name="reliability"></a>Megbízhatóság

Az Orchestrator függvények megbízhatóan karbantartják a végrehajtási állapotukat az [eseményforrás tervezési](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) mintájának használatával. Ahelyett, hogy közvetlenül tárolja a vezénylési, a tartós feladat keretrendszer egy csak hozzáfűző tároló thasználja a függvény vezénylési által végrehajtott műveletek teljes sorozatának rögzítéséhez. A csak hozzáfűző üzlet számos előnnyel jár a teljes futásidejű állapot "dömpingeléséhez" képest. Az előnyök közé tartozik a nagyobb teljesítmény, a méretezhetőség és a válaszkészség. A tranzakciós adatok, valamint a teljes naplózási naplók és előzmények végleges konzisztenciáját is megkapja. Az ellenőrzési nyomvonalak megbízható kompenzációs műveleteket támogatnak.

A Durable Functions transzparens módon használja az eseménybeszerzést. A színfalak `await` mögött a (C#) vagy `yield` (JavaScript) operátor egy orchestrator függvény hozamok ellenőrzése az orchestrator szál vissza a tartós feladat keretrendszer diszpécser. A diszpécser ezután véglegesíti az orchestrator függvény által ütemezett új műveleteket (például egy vagy több gyermekfüggvény hívását vagy egy tartós időzítő ütemezését) a tárolóba. Az átlátszó véglegesítési művelet hozzáfűzi a vezénylési példány végrehajtási előzményeihez. Az előzmények egy tárolótáblában tárolóasztalon tárolóasztalon tárolók. A commit művelet ezután üzeneteket ad a várólistához a tényleges munka ütemezéséhez. Ezen a ponton az orchestrator függvény memóriából eltávolítható.

Ha egy vezénylési függvény több munkát kap (például egy válaszüzenet érkezik, vagy egy tartós időzítő lejár), az orchestrator felébred, és újra végrehajtja a teljes függvényt az elejétől a helyi állapot újraépítéséhez. A visszajátszás során, ha a kód megpróbál meghívni egy függvényt (vagy bármilyen más async munkát végezni), a tartós feladatkeretrendszer az aktuális vezénylési műveletek végrehajtási előzményeit olvassa be. Ha úgy találja, hogy a [tevékenység függvény](durable-functions-types-features-overview.md#activity-functions) már végrehajtott, és eredményt hozott, akkor visszajátssza a függvény eredményét, és az orchestrator kód továbbra is fut. A visszajátszás addig folytatódik, amíg a függvénykód be nem fejeződik, vagy amíg nem ütemezett új aszinkron munkát.

> [!NOTE]
> Annak érdekében, hogy a visszajátszási minta megfelelően és megbízhatóan működjön, az orchestrator függvénykódnak *determinisztikusnak*kell lennie. Az orchestrator függvények kódkorlátozásairól az [orchestrator függvénykód-megkötések](durable-functions-code-constraints.md) témakörben olvashat bővebben.

> [!NOTE]
> Ha egy orchestrator függvény naplóüzeneteket bocsát ki, a visszajátszási viselkedés ismétlődő naplóüzenetek kibocsátását okozhatja. A [naplózási](durable-functions-diagnostics.md#logging) témakörben további információ arról, hogy miért fordul elő ez a viselkedés, és hogyan kerüli meg a problémát.

## <a name="orchestration-history"></a>Vezénylési előzmények

A tartós feladatkeretrendszer esemény-forrás viselkedése szorosan kapcsolódik az ön által írt orchestrator függvénykódhoz. Tegyük fel, hogy rendelkezik egy tevékenységláncoló orchestrator függvény, mint például a következő orchestrator függvény:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const output = [];
    output.push(yield context.df.callActivity("E1_SayHello", "Tokyo"));
    output.push(yield context.df.callActivity("E1_SayHello", "Seattle"));
    output.push(yield context.df.callActivity("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return output;
});
```

---

Minden `await` (C#) `yield` vagy (JavaScript) utasítás, a durable task framework ellenőrzőpontok a függvény végrehajtási állapotát néhány tartós tárolási háttérrendszer (általában az Azure Table storage). Ezt az állapotot nevezik *vezénylési előzményeknek.*

### <a name="history-table"></a>Előzmények tábla

Általánosságban elmondható, hogy a tartós feladatkeretrendszer minden ellenőrzőponton a következőket teszi:

1. Végrehajtási előzményeket ment az Azure Storage-táblákba.
2. Várólistán helyezi az üzeneteket az orchestrator által meghívni kíván.
3. Enqueues üzeneteket az orchestrator magát, &mdash; például a tartós időzítő üzeneteket.

Az ellenőrzőpont befejezése után az orchestrator függvény szabadon eltávolítható a memóriából, amíg nincs több munka, hogy nem.

> [!NOTE]
> Az Azure Storage nem nyújt tranzakciós garanciát a táblatárolóba és a várólistákba történő adatmentés között. A hibák kezeléséhez a Durable Functions tárolószolgáltató *a végleges konzisztenciamintákat* használja. Ezek a minták biztosítják, hogy ne vesszenek el adatok, ha egy ellenőrzőpont közepén összeomlik vagy megszakad a kapcsolat.

Befejezését követően a korábban megjelenített függvény előzményei az alábbi táblázathoz hasonlóan néznek ki az Azure Table Storage-ban (rövidítve illusztrációs célokra):

| PartitionKey (instanceid)                     | EventType (Eseménytípus)             | Időbélyeg               | Input (Bemenet) | Név             | Eredmény                                                    | status |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | Végrehajtás Elindítva      | 2017-05-05T18:45:28.852Z | null  | E1_HelloSequence |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     |
| eaee885b | Ütemezett feladat         | 2017-05-05T18:45:32.670Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | Orchestrator Kész | 2017-05-05T18:45:32.670Z |       |                  |                                                           |                     |
| eaee885b | Feladat befejezve         | 2017-05-05T18:45:34.201z |       |                  | "Helló Tokió!"                                        |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     |
| eaee885b | Ütemezett feladat         | 2017-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | Orchestrator Kész | 2017-05-05T18:45:34.435Z |       |                  |                                                           |                     |
| eaee885b | Feladat befejezve         | 2017-05-05T18:45:34.763z |       |                  | ""Helló Seattle!""                                      |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | Ütemezett feladat         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | Orchestrator Kész | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | Feladat befejezve         | 2017-05-05T18:45:34.919Z |       |                  | "Helló London!""                                       |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     |
| eaee885b | Orchestrator Kész | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     |
| eaee885b | Végrehajtás befejezve    | 2017-05-05T18:45:35.044Z |       |                  | "["Hello Tokyo!",""Hello Seattle!",""Hello London!"""""""Hello London!"""" | Befejezve           |

Néhány megjegyzés az oszlopértékekről:

* **PartitionKey**: A vezénylés példányazonosítóját tartalmazza.
* **EventType**: Az esemény típusát jelöli. A következő típusok egyike lehet:
  * **OrchestrationStarted**: Az orchestrator függvény egy várakozásból folytatódik, vagy először fut. Az `Timestamp` oszlop a `CurrentUtcDateTime` (.NET) és `currentUtcDateTime` a (JavaScript) API-k determinisztikus értékének feltöltésére szolgál.
  * **ExecutionStarted**: Az orchestrator függvény első alkalommal kezdte meg a végrehajtást. Ez az esemény az oszlopban lévő `Input` függvénybemenetet is tartalmazza.
  * **TaskScheduled**: Egy tevékenységfüggvény ütemezve volt. A tevékenységfüggvény neve az `Name` oszlopban lesz rögzítve.
  * **Feladat befejezve**: Egy tevékenységfüggvény befejeződött. A függvény eredménye az `Result` oszlopban található.
  * **TimerCreated**: Egy tartós időzítő jött létre. Az `FireAt` oszlop az időzítő lejáratának ütemezett UTC-idejét tartalmazza.
  * **TimerFired**: Egy tartós időzítő tüzelt.
  * **EventRaised**: Egy külső esemény lett elküldve a vezénylési példány. Az `Name` oszlop rögzíti az esemény nevét, az `Input` oszlop pedig az esemény hasznos adatát.
  * **OrchestratorCompleted**: Az orchestrator függvény várt.
  * **ContinueAsNew**: Az orchestrator függvény befejeződött, és újraindult magát az új állapottal. Az `Result` oszlop tartalmazza az értéket, amely az újraindított példány bemeneteként használatos.
  * **ExecutionCompleted**: Az orchestrator függvény a befejezésig futott (vagy nem sikerült). A függvény kimenetei vagy a hiba részletei `Result` az oszlopban tárolódnak.
* **Időbélyeg**: Az előzményesemény UTC-időbélyege.
* **Név**: A meghívott függvény neve.
* **Bemenet**: A függvény JSON-formátumú bemenete.
* **Eredmény**: A függvény kimenete; azaz a visszatérési értéke.

> [!WARNING]
> Bár hibakereső eszközként hasznos, ne vegyen igénybe semmilyen függőséget ebben a táblában. A Tartós függvények bővítmény fejlődésével változhat.

Minden alkalommal, amikor a `await` függvény egy `yield` (C#) vagy (JavaScript) rendszerből folytatódik, a Tartós feladatkeretrendszer teljesen újrafuttatja az orchestrator függvényt. Minden ismétléskor áttekinti a végrehajtási előzményeket, és megállapítja, hogy az aktuális aszinkron művelet megtörtént-e.  Ha a művelet megtörtént, a keretrendszer azonnal visszajátssza a művelet `await` kimenetét, és `yield` továbblép a következő (C#) vagy (JavaScript) értékre. Ez a folyamat addig folytatódik, amíg a teljes előzményeket vissza nem játssza. Az aktuális előzmények visszalejátszása után a helyi változók visszaállnak a korábbi értékekre.

## <a name="features-and-patterns"></a>Jellemzők és minták

A következő szakaszok ismertetik az orchestrator függvények jellemzőit és mintáit.

### <a name="sub-orchestrations"></a>Alvezénylések

Az Orchestrator függvények meghívhatnak tevékenységfüggvényeket, de más orchestrator függvényeket is. Például hozhat létre egy nagyobb vezénylési ki egy könyvtár orchestrator függvények. Vagy futtathat több példányt egy orchestrator függvény párhuzamosan.

További információkért és példákért lásd az [Alvezénylések](durable-functions-sub-orchestrations.md) cikket.

### <a name="durable-timers"></a>Tartós időzítők

Vezénylések ütemezheti *tartós időzítők* késések megvalósításához, vagy az aszinkron műveletek idő-kapcsolat kezelésének beállítása. Használjon tartós időzítők orchestrator `Thread.Sleep` `Task.Delay` függvények helyett `setTimeout()` és `setInterval()` (C#) vagy (JavaScript).

További információkért és példákért tekintse meg a [Tartós időzítők](durable-functions-timers.md) cikket.

### <a name="external-events"></a>Külső események

Az Orchestrator függvények megvárhatja, hogy a külső események frissítsék a vezénylési példányt. Ez a tartós funkciók funkció gyakran hasznos emberi interakció vagy más külső visszahívások kezeléséhez.

További információkért és példákért lásd a [Külső események](durable-functions-external-events.md) című cikket.

### <a name="error-handling"></a>Hibakezelés

Az Orchestrator függvényei használhatják a programozási nyelv hibakezelési jellemzőit. A meglévő `try` / `catch` minták, mint a vezénylési kód támogatja.

Az Orchestrator-függvények újrapróbálkozási házirendeket is hozzáadhatnak az általuk megnevezett tevékenység- vagy alvezénytorfüggvényekhez. Ha egy tevékenység vagy sub-orchestrator függvény sikertelen egy kivétellel, a megadott újrapróbálkozási házirend automatikusan késleltetheti, és újra a végrehajtás térhet el a megadott számú alkalommal.

> [!NOTE]
> Ha egy orchestrator függvényben van egy nem kezelt kivétel, az `Failed` vezénylési példány egy állapotban fejeződik be. Egy vezénylési példány nem kísérelhető meg újra, ha nem sikerült.

További információt és példákat a [Hibakezelés](durable-functions-error-handling.md) című cikkben talál.

### <a name="critical-sections-durable-functions-2x-currently-net-only"></a>Kritikus szakaszok (Durable Functions 2.x, jelenleg csak .NET)

Vezénylési példányok egyszálas, így nem szükséges aggódni a versenyfeltételek egy vezénylési *belül.* A versenyfeltételek azonban akkor lehetségesek, ha a vezénylések külső rendszerekkel lépnek kapcsolatba. A versenykörülmények csökkentése érdekében, amikor külső rendszerekkel kommunikálnak, az orchestrator függvények *kritikus szakaszokat definiálhatnak* a `LockAsync` .NET metódushasználatával.

A következő mintakód egy kritikus szakaszt definiáló orchestrator függvényt jelenít meg. A módszerrel lép be `LockAsync` a kritikus szakaszba. Ehhez a módszerhez egy vagy több hivatkozást kell átadni egy [tartós entitásra,](durable-functions-entities.md)amely tartósan kezeli a zárolási állapotot. Ennek a vezénylési időnek csak egy példánya futtathatja a kódot a kritikus szakaszban.

```csharp
[FunctionName("Synchronize")]
public static async Task Synchronize(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var lockId = new EntityId("LockEntity", "MyLockIdentifier");
    using (await context.LockAsync(lockId))
    {
        // critical section - only one orchestration can enter at a time
    }
}
```

A `LockAsync` beszerzi a tartós zár(oka)t, és az ártalmatlanításkor visszaadja `IDisposable` a kritikus szakaszt. Ez `IDisposable` az eredmény egy `using` blokkdal együtt használható a kritikus szakasz szintaktikai ábrázolásának leállításához. Amikor egy orchestrator függvény kritikus szakaszba lép, csak egy példány hajthatja végre ezt a kódblokkot. Minden más példányok, amelyek megpróbálnak belépni a kritikus szakasz blokkolva lesz, amíg az előző példány kilép a kritikus szakasz.

A kritikus szakasz funkció is hasznos a tartós entitások változásainak koordinálása. A kritikus szakaszokról további információt a [Tartós entitások "Entitáskoordináció"](durable-functions-entities.md#entity-coordination) témakörben talál.

> [!NOTE]
> A kritikus szakaszok a Durable Functions 2.0 és újabb verziókban érhetők el. Jelenleg csak a .NET vezénylések valósítják meg ezt a funkciót.

### <a name="calling-http-endpoints-durable-functions-2x"></a>HTTP-végpontok hívása (durable functions 2.x)

Az Orchestrator függvények nem tehetnek I/O-t, ahogy azt az [orchestrator függvénykód-megkötések ismertetik.](durable-functions-code-constraints.md) Ennek a korlátozásnak a tipikus megoldása az, hogy minden olyan kódot bekell csomagolni, amelynek egy tevékenységi funkcióban i/o-t kell tennie. Vezénylések, amelyek együttműködnek a külső rendszerekkel gyakran használja a tevékenységfüggvények HTTP-hívások at, és adja vissza az eredményt a vezénylési.

# <a name="c"></a>[C #](#tab/csharp)

A gyakori minta egyszerűsítése érdekében `CallHttpAsync` az orchestrator függvények a módszer segítségével közvetlenül meghívhathttp API-kat.

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const url = context.df.getInput();
    var res = yield context.df.callHttp("GET", url);
    if (res.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```

---

Az alapvető kérési/válaszminták támogatása mellett a módszer támogatja a közös aszinkron HTTP 202 lekérdezési minták automatikus kezelését, valamint a [felügyelt identitások](../../active-directory/managed-identities-azure-resources/overview.md)használatával végzett külső szolgáltatásokkal való hitelesítést is.

További információkért és részletes példákért lásd a [HTTP-szolgáltatások](durable-functions-http-features.md) ról szóló cikket.

> [!NOTE]
> Http-végpontok közvetlenhívása az orchestrator függvények a Durable Functions 2.0 és újabb.

### <a name="passing-multiple-parameters"></a>Több paraméter átadása

Nem lehet közvetlenül átadni egy tevékenységfüggvénynek több paramétert. A javaslat az, hogy adja át egy tömb objektumok vagy összetett objektumok.

# <a name="c"></a>[C #](#tab/csharp)

A .NET-ben [ValueTuples objektumokat](https://docs.microsoft.com/dotnet/csharp/tuples) is használhat. A következő minta a [C# 7-tel](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples)hozzáadott [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) új funkcióit használja:

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<object> RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    object courseRecommendations = await context.CallActivityAsync<object>(
        "CourseRecommendations",
        (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<object> Mapper([ActivityTrigger] IDurableActivityContext inputs)
{
    // parse input for student's major and year in university
    (string Major, int UniversityYear) studentInfo = inputs.GetInput<(string, int)>();

    // retrieve and return course recommendations by major and university year
    return new
    {
        major = studentInfo.Major,
        universityYear = studentInfo.UniversityYear,
        recommendedCourses = new []
        {
            "Introduction to .NET Programming",
            "Introduction to Linux",
            "Becoming an Entrepreneur"
        }
    };
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

#### <a name="orchestrator"></a>Orchestrator

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const location = {
        city: "Seattle",
        state: "WA"
    };
    const weather = yield context.df.callActivity("GetWeather", location);

    // ...
};
```

#### <a name="activity"></a>Tevékenység

```javascript
module.exports = async function (context, location) {
    const {city, state} = location; // destructure properties into variables

    // ...
};
```

---

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Vezénylői kódkorlátozások](durable-functions-code-constraints.md)
