---
title: Tartós összeszerelések – Azure Functions
description: Az Azure Durable Functions előkészítési funkciójának bemutatása.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 09/08/2019
ms.author: azfuncdf
ms.openlocfilehash: 82c4a27ac2491e668c1d99e2a14b870e82ec5665
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935858"
---
# <a name="durable-orchestrations"></a>Tartós összeszerelések

Durable Functions a [Azure functions](../functions-overview.md)kiterjesztése. A *Orchestrator függvénnyel* más tartós függvények végrehajtását is elvégezheti a Function alkalmazásban. A Orchestrator függvények jellemzői a következők:

* A Orchestrator függvények eljárási kóddal határozzák meg a függvények munkafolyamatait. Nincs szükség deklaratív sémára vagy tervezőre.
* A Orchestrator függvények szinkronban és aszinkron módon hívhatják meg a többi tartós funkciót. A hívott függvények kimenete megbízhatóan menthető a helyi változókba.
* A Orchestrator függvények tartósak és megbízhatók. A végrehajtási folyamat automatikusan ellenőrzőpontra kerül, amikor a függvény "vár" vagy "hozam". A folyamat újraindításakor vagy a virtuális gép újraindításakor a helyi állapot soha nem vész el.
* A Orchestrator függvények hosszú ideig is futhatnak. Egy előkészítési *példány* teljes élettartama másodperc, nap, hónap vagy soha vége lehet.

Ez a cikk áttekintést nyújt a Orchestrator functions szolgáltatásról, valamint arról, hogy miként segíthetnek a különböző alkalmazás-fejlesztési problémák megoldásában. Ha még nem ismeri a Durable Functions alkalmazásban elérhető függvények típusát, először olvassa el a [tartós függvények típusait](durable-functions-types-features-overview.md) ismertető cikket.

## <a name="orchestration-identity"></a>Előkészítési identitás

Egy előkészítés minden *példánya* rendelkezik egy példány-azonosítóval (más néven *példány-azonosítóval*). Alapértelmezés szerint minden példány azonosítója egy automatikusan generált GUID. A példányok azonosítói azonban bármilyen felhasználó által generált karakterláncot is tartalmazhatnak. Minden egyes összehangoló példány AZONOSÍTÓjának egyedinek kell lennie a [feladatsoron](durable-functions-task-hubs.md)belül.

Az alábbi szabályok a példányok azonosítóit tárgyalják:

* A példány-azonosítónak 1 és 256 karakter közöttinek kell lennie.
* A példány-azonosítók nem `@`kezdődhetnek együtt.
* A példány-azonosítók `/`nem `\`tartalmazhatnak `#`, `?` , vagy karaktereket.
* A példány-azonosítók nem tartalmazhatnak vezérlő karaktereket.

> [!NOTE]
> Általában ajánlott automatikusan generált példány-azonosítókat használni, amikor csak lehetséges. A felhasználó által generált példányok azonosítói olyan forgatókönyvekhez készültek, ahol az egy-az-egyhez hozzárendelés egy összehangoló példány és egy külső, alkalmazásspecifikus entitás, például egy beszerzési rendelés vagy egy dokumentum között.

Egy előkészítési példány azonosítója egy kötelező paraméter a legtöbb példány- [felügyeleti művelethez](durable-functions-instance-management.md). Emellett a diagnosztika szempontjából is fontosak, például a Application Insights a munkafolyamatok [nyomon követési adatait](durable-functions-diagnostics.md#application-insights) , hibaelhárítási és elemzési célokra. Ezért javasoljuk, hogy a generált példányok azonosítóit egy külső helyre (például egy adatbázisba vagy az alkalmazás naplóiba) mentse, ahol később könnyen lehet hivatkozni.

## <a name="reliability"></a>Megbízhatóság

Az Orchestrator függvények az [esemény-beszerzések](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) kialakítási mintája alapján megbízhatóan karbantartják végrehajtási állapotukat. A folyamat aktuális állapotának közvetlen tárolása helyett az állandó feladathoz tartozó keretrendszer egy csak Hozzáfűzéses tárolót használ a függvények összehangolása által végrehajtott műveletek teljes sorozatának rögzítéséhez. A csak Hozzáfűzéses tároló számos előnnyel jár, mint a teljes futtatókörnyezet állapotának "kiírása". Az előnyök többek között a teljesítmény, a méretezhetőség és a rugalmasság. A tranzakciós és a teljes naplózási nyomvonalak és előzmények végleges konzisztenciáját is biztosítjuk. A naplózási nyomvonalak támogatják a megbízható kompenzáló műveleteket.

A Durable Functions az események beszerzését transzparens módon használja. A színfalak mögött a `await` (C#) vagy `yield` a (JavaScript) operátor egy Orchestrator függvényben a Orchestrator-szál vezérlését eredményezi a tartós feladatokhoz. A diszpécser ezután véglegesít minden olyan új műveletet, amelyet a Orchestrator függvény ütemez (például egy vagy több alárendelt függvény hívása vagy tartós időzítő ütemezése) a tárolóba. Az átlátszó véglegesítő művelet hozzáfűzi a rendszerelőkészítési példány végrehajtási előzményeihez. Az előzmények tárolása egy tárolási táblában történik. A commit művelet ezután üzeneteket hoz létre egy várólistához a tényleges munka időzítése érdekében. Ezen a ponton a Orchestrator függvény eltávolítható a memóriából.

Ha egy összehangoló függvény több munkát tesz elérhetővé (például válaszüzenet érkezik vagy tartós időzítő lejár), a Orchestrator felébred, és újból végrehajtja a teljes függvényt az elejétől a helyi állapot újraépítéséhez. Ha a kód megpróbál meghívni egy függvényt (vagy bármilyen más aszinkron munkát hajt végre), akkor az állandó feladat-keretrendszer az aktuális előkészítés végrehajtási előzményeit kérdezi le. Ha úgy találja, hogy a [tevékenységi függvény](durable-functions-types-features-overview.md#activity-functions) már végre lett hajtva, és eredményként eredményezte, akkor az a függvény eredményét játssza le, és a Orchestrator-kód továbbra is futni fog. A visszajátszás addig folytatódik, amíg a függvény kódja be nem fejeződik, vagy amíg be nem ütemezi az új aszinkron munkát.

> [!NOTE]
> Ahhoz, hogy az újrajátszás mintája megfelelően működjön és megbízható legyen, a Orchestrator függvény kódjának *determinisztikus*kell lennie. A Orchestrator függvények kódjainak korlátozásával kapcsolatos további információkért lásd a [Orchestrator-függvényekre](durable-functions-code-constraints.md) vonatkozó korlátozásokkal foglalkozó témakört.

> [!NOTE]
> Ha egy Orchestrator-függvény naplófájlokat bocsát ki, a visszajátszás viselkedése duplikált üzeneteket eredményezhet. Tekintse meg a [naplózási](durable-functions-diagnostics.md#logging) témakört, amelyből megtudhatja, miért fordul elő ez a viselkedés, és hogyan lehet megkerülni.

## <a name="orchestration-history"></a>Előkészítési előzmények

A tartós feladatok keretrendszerének esemény-beszerzés viselkedése szorosan összekapcsolja az Ön által írt Orchestrator-függvény kódját. Tegyük fel, hogy van egy Orchestrator-függvénye, például a C# következő Orchestrator függvény:

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

Ha JavaScript-kódolást használ, a tevékenység-láncolási Orchestrator funkció az alábbi példában láthatóhoz hasonló lehet:

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

Az állandó `await` feladatC#-keretrendszer `yield` minden () vagy (JavaScript) utasításban lefordítja a függvény végrehajtási állapotát egy tartós tárolási háttérbe (általában az Azure Table Storage-ba). Ezt az állapotot nevezzük a megszervezési *előzményeknek*.

### <a name="history-table"></a>Előzmények táblázat

Általánosságban elmondható, hogy az állandó feladathoz tartozó keretrendszer minden ellenőrzőponton a következőket végzi el:

1. Elmenti a végrehajtási előzményeket az Azure Storage-táblákba.
2. A Orchestrator által meghívott függvények Enqueues üzenetei.
3. A Orchestrator &mdash; Enqueues üzenetei, például tartós időzítő üzenetek.

Az ellenőrzőpont befejezését követően a Orchestrator függvény szabadon eltávolítható a memóriából, amíg még nem működik.

> [!NOTE]
> Az Azure Storage nem biztosít tranzakciós garanciát az adatmentés a Table Storage-ba és a várólistákba. A hibák kezelése érdekében a Durable Functions Storage-szolgáltató *végleges konzisztencia* -mintákat használ. Ezek a minták biztosítják, hogy az ellenőrzőpontok közepén lévő kapcsolat összeomlása vagy elvesztése esetén ne vesszenek el az adatvesztés.

Befejezésekor a korábban bemutatott függvény előzményei a következő táblázathoz hasonlóan jelennek meg az Azure Table Storage (illusztrációs célokra rövidítve):

| PartitionKey (InstanceId)                     | EventType             | Timestamp               | Input (Bemenet) | Name (Név)             | Eredmény                                                    | State |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     |
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852Z | null  | E1_HelloSequence |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201Z |       |                  | "" "Hello Tokyo!" "                                        |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763Z |       |                  | "" "Helló Seattle!" "                                      |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919Z |       |                  | "" "Helló Londonban!" "                                       |                     |
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | "[" "Hello Tokyo!" "," "Helló Seattle!" "," "Hello London!" "]" | Befejeződött           |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     |

Néhány megjegyzés az oszlop értékeihez:

* **PartitionKey**: A koordinálás példányának AZONOSÍTÓját tartalmazza.
* **EventType**: Az esemény típusát jelöli. A következő típusok egyike lehet:
  * **OrchestrationStarted**: A Orchestrator függvény újraindul egy várakozási időszakból, vagy első alkalommal fut. Az `Timestamp` oszlop a [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) API determinisztikus értékének feltöltésére szolgál.
  * **ExecutionStarted**: A Orchestrator függvény első alkalommal indította el a végrehajtást. Ez az esemény az `Input` oszlop függvény bemenetét is tartalmazza.
  * **TaskScheduled**: Egy tevékenység-függvény ütemezése megtörtént. A tevékenység-függvény neve az `Name` oszlopban van rögzítve.
  * **TaskCompleted**: Egy tevékenység-függvény befejeződött. A függvény `Result` eredménye az oszlopban található.
  * **TimerCreated**: Egy tartós időzítő lett létrehozva. Az `FireAt` oszlop azt az ütemezett UTC-időt tartalmazza, amelyen az időzítő lejár.
  * **TimerFired**: Tartós időzítő.
  * **EventRaised**: A rendszer egy külső eseményt kapott a rendszerelőkészítési példánynak. Az `Name` oszlop rögzíti az esemény nevét, és az `Input` oszlop rögzíti az esemény hasznos adatait.
  * **OrchestratorCompleted**: A Orchestrator függvény vár.
  * **ContinueAsNew**: A Orchestrator függvény befejeződött, és új állapottal indult újra. Az `Result` oszlop tartalmazza azt az értéket, amelyet a rendszer az újraindított példány bemenetként használ.
  * **ExecutionCompleted**: A Orchestrator függvény befejeződött (vagy sikertelen). A függvény kimeneteit vagy a hiba részleteit az `Result` oszlopban tárolja a rendszer.
* **Időbélyeg**: Az előzmények eseményének UTC-időbélyege
* **Név**: A meghívott függvény neve.
* **Bemenet**: A függvény JSON formátumú bemenete.
* **Eredmény**: A függvény kimenete; Ez a visszatérési értéke.

> [!WARNING]
> Habár hibakeresési eszközként hasznos, ne tegyen függőséget ezen a táblán. Előfordulhat, hogy a Durable Functions bővítmény fejlődése megváltozhat.

Minden alkalommal, amikor a függvény egy `await` (C#) vagy `yield` (JavaScript) állapotból tér vissza, a tartós feladat-keretrendszer újra lefuttatja a Orchestrator függvényt. Minden újrafuttatáskor megtekinti a végrehajtás előzményeit, és megállapítja, hogy az aktuális aszinkron művelet megtörtént-e.  Ha a művelet lezajlott, a keretrendszer azonnal visszajátssza a művelet kimenetét, és továbblép a következőre `await` (C#) vagy `yield` (JavaScript). Ez a folyamat addig folytatódik, amíg a teljes előzmények újra nem lettek játszva. Az aktuális előzmények újbóli lejátszása után a helyi változók vissza lesznek állítva a korábbi értékekre.

## <a name="features-and-patterns"></a>Funkciók és minták

A következő szakaszok a Orchestrator függvények funkcióit és mintáit ismertetik.

### <a name="sub-orchestrations"></a>Alvezénylések

A Orchestrator függvények meghívhatják a tevékenységek függvényeit, de más Orchestrator-függvényeket is használhatnak. Létrehozhat például egy nagyobb, a Orchestrator-függvények könyvtárainak összeszerelését. A Orchestrator függvény több példányát is futtathatja párhuzamosan.

További információért és példákért tekintse meg az [alfolyamatok](durable-functions-sub-orchestrations.md) című cikket.

### <a name="durable-timers"></a>Tartós időzítők

A munkafolyamatok *tartós időzítőket* ütemezhetnek a késések megvalósításához, illetve az aszinkron műveletek időtúllépési kezelésére vonatkozóan. Használjon tartós időzítőket a és `Thread.Sleep` `Task.Delay` a (C#) vagy `setTimeout()` `setInterval()` a (JavaScript) helyett a Orchestrator függvényekben.

További információért és példákért tekintse meg a [tartós időzítők](durable-functions-timers.md) című cikket.

### <a name="external-events"></a>Külső események

A Orchestrator függvények megvárhatják, hogy külső események frissítsenek egy előkészítési példányt. Ez a Durable Functions funkció gyakran hasznos az emberi interakciók vagy más külső visszahívások kezeléséhez.

További információkat és példákat a [külső események](durable-functions-external-events.md) című cikkben talál.

### <a name="error-handling"></a>Hibakezelés

A Orchestrator függvények használhatják a programozási nyelv hibakezelés funkcióit. A meglévő mintázatok, például `try` / `catch` a hangfelismerési kódban támogatottak.

A Orchestrator függvények újrapróbálkozási házirendeket is hozzáadhatnak a meghívott tevékenységekhez vagy Orchestrator függvényekhez. Ha egy tevékenység vagy egy alorchestratoron függvény kivételt jelez, a megadott újrapróbálkozási szabályzat automatikusan késleltetheti a műveletet, és a megadott számú alkalommal újra próbálkozhat a végrehajtással.

> [!NOTE]
> Ha egy Orchestrator-függvény nem kezelt kivételt ad meg, a koordináló példány egy `Failed` állapotban fejeződik be. Nem lehet újrapróbálkozni egy összehangoló példányban, ha az sikertelen volt.

További információt és példákat [a hibakezelés című cikkben talál](durable-functions-error-handling.md) .

### <a name="critical-sections"></a>Kritikus csoportok

Az előkészítési példányok egyszálas, ezért nem kell aggódnia a versenyhelyzet feltételein *belül* . A versenyhelyzet azonban akkor is lehetséges, ha az előkészítési folyamat külső rendszerekkel kommunikál. Ha a külső rendszerekkel való interakció során csökkenteni szeretné a versenyhelyzet feltételeit, a Orchestrator függvények *kritikus szakaszt* határoznak meg a `LockAsync` .net-beli metódusok használatával.

Az alábbi mintakód egy Orchestrator függvényt mutat be, amely egy kritikus szakaszt határoz meg. A `LockAsync` módszer használatával a kritikus szakasz lép életbe. Ehhez a metódushoz egy vagy több, egy [tartós entitásra](durable-functions-entities.md)mutató hivatkozást kell átadni, amely tartósan kezeli a zárolási állapotot. Ennek a folyamatnak csak egyetlen példánya tudja végrehajtani a kódot a kritikus szakaszban.

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

A `LockAsync` felvásárolja a tartós zárolás (oka) t, `IDisposable` és visszaadja a kritikus szakaszt a használatból. Ez `IDisposable` az eredmény együtt használható egy `using` blokkal a kritikus szakasz szintaktikai ábrázolásának beolvasásához. Ha egy Orchestrator függvény kritikus szakaszt ad meg, csak egy példány hajthatja végre ezt a kódot. Minden más, a kritikus szakaszt beírni próbáló példány le lesz tiltva, amíg az előző példány nem kilép a kritikus szakaszból.

A kritikus szakasz funkció a tartós entitások változásainak koordinálására is használható. A kritikus szakaszokkal kapcsolatos további információkért tekintse meg a [tartós entitások "entitások egyeztetése"](durable-functions-entities.md#entity-coordination) című témakört.

> [!NOTE]
> A kritikus fejezetek Durable Functions 2,0-es és újabb verziókban érhetők el. Jelenleg csak a .NET-eszközök implementálják ezt a funkciót.

### <a name="calling-http-endpoints"></a>HTTP-végpontok hívása

A Orchestrator függvények nem engedélyezettek az I/O-műveletek esetében, ahogy azt a [Orchestrator-függvény kódjainak megkötései](durable-functions-code-constraints.md)című témakör írja le. Ennek a korlátozásnak a leggyakoribb megkerülő megoldás, ha olyan kódokat szeretne becsomagolni, amelyeknek az I/O-műveletekre van szükségük A külső rendszerekkel kommunikáló Összehangolók gyakran használják a tevékenységi funkciókat a HTTP-hívások elvégzéséhez, és az eredménynek az előkészítéshez való visszaküldését.

Az általános minta leegyszerűsítése érdekében a Orchestrator functions `CallHttpAsync` a .net-ben lévő metódust használhatja a http API-k közvetlen meghívásához. Az alapszintű kérelmek/válaszok `CallHttpAsync` támogatása mellett a támogatja a gyakori aszinkron http 202 lekérdezési minták automatikus kezelését, valamint a [felügyelt identitások](../../active-directory/managed-identities-azure-resources/overview.md)használatával támogatja a külső szolgáltatásokkal való hitelesítést is.

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

További információt és részletes példákat a [http-szolgáltatások](durable-functions-http-features.md) című cikkben talál.

> [!NOTE]
> A HTTP-végpontok közvetlenül a Orchestrator függvényekből való meghívása Durable Functions 2,0-es vagy újabb verzióban érhető el. Jelenleg csak a .NET-eszközök implementálják ezt a funkciót.

### <a name="passing-multiple-parameters"></a>Több paraméter átadása

Nem lehet átadni több paramétert egy tevékenységi függvénynek közvetlenül. A javaslat egy objektum tömbjét adja át, vagy [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) objektumokat használ a .net-ben.

A következő minta a [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) új funkcióit használja, amelyeket a [ C# 7](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples):

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<object> RunOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    object courseRecommendations = await context.CallActivityAsync<object>(
        "CourseRecommendations",
        (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<object> Mapper([ActivityTrigger] DurableActivityContext inputs)
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

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Orchestrator-kód megkötései](durable-functions-code-constraints.md)
