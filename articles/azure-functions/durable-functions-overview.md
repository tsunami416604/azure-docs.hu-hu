---
title: "Tartós funkciók áttekintés – Azure (előzetes verzió)"
description: "Bevezetés az Azure Functions a tartós funkciók bővítmény."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: b5269bb51c787c927b4224b3520d5514b6d24501
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
---
# <a name="durable-functions-overview-preview"></a>Tartós Functions áttekintése (előzetes verzió)

*Tartós funkciók* kiterjesztése [Azure Functions](functions-overview.md) és [Azure webjobs-feladatok](../app-service/web-sites-create-web-jobs.md) , amely lehetővé teszi az állapotalapú írást egy kiszolgáló nélküli környezetben. A bővítmény állapotát, az ellenőrzőpontok és újraindul kezeli az Ön.

A bővítmény lehetővé teszi az állapotalapú munkafolyamatok definiálhatja a hívott függvény új típusú egy *orchestrator függvény*. Az alábbiakban néhány orchestrator funkciók előnyeit:

* Munkafolyamatok meghatározzák a kódban. JSON-sémák vagy tervezők nem szükségesek.
* Ezek más függvényeinek meghívása szinkron és aszinkron módon. Helyi változók mentését hívott függvények kimenete.
* Ezek automatikusan ellenőrzőpontot a folyamatban, amikor a függvény várja. Helyi állapot soha nem elvész, ha a folyamat újrahasznosítása vagy a virtuális gép újraindul.

> [!NOTE]
> Tartós függvények jelenleg előzetes verzióban érhető, és az Azure Functions, amely nem megfelelő összes alkalmazás speciális bővítménye. Ez a cikk többi feltételezi, hogy rendelkezik-e egy erős ismeretét [Azure Functions](functions-overview.md) fogalmakat és kihívásai kiszolgáló nélküli alkalmazásfejlesztés részt.

Az elsődleges használati eset tartós függvények van egyszerűsítése összetett, állapot-nyilvántartó koordinációs felmerülő problémák kiszolgáló nélküli. A következő szakaszok ismertetik az egyes alkalmazások mintázatok tartós funkciók előnyeit is.

## <a name="pattern-1-function-chaining"></a>#1. minta: Működéséhez láncolás

*Függvény láncolás* is funkciók sorozata lehet meghatározott sorrendben mintát hivatkozik. A kimenet egy függvény gyakran kell másik függvény a bemeneti alkalmazható.

![Függvény titkosításblokkoló diagramja](media/durable-functions-overview/function-chaining.png)

Tartós funkciók kódban ebben a mintában tömören teszi lehetővé.

```cs
public static async Task<object> Run(DurableOrchestrationContext ctx)
{
    try
    {
        var x = await ctx.CallActivityAsync<object>("F1");
        var y = await ctx.CallActivityAsync<object>("F2", x);
        var z = await ctx.CallActivityAsync<object>("F3", y);
        return  await ctx.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // error handling/compensation goes here
    }
}
```

A "F1", "F2", "F3" és "F4" értékei egyéb funkciók, a függvény alkalmazás nevét. Folyamatábrán szerkezetek kódolási normál imperatív segítségével van megvalósítva. Ez azt jelenti, hogy a kód felülről lefelé végrehajtja, és meglévő nyelvi ellenőrzési folyamata szemantikáját, például conditionals és hurkok magába foglaló.  Hiba történt az logika kezelése try vagy catch vagy finally blokkok tartalmazhat.

A `ctx` paraméter ([DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html)) egyéb funkciók meghívása neve, a paraméterek átadása, és vissza a függvény kimeneti metódusokat biztosít. Minden alkalommal, amikor a kód hívások `await`, a tartós funkciók keretrendszer *ellenőrzőpontokat* az aktuális függvény példány előrehaladását. Ha a folyamat vagy a virtuális gép újraindul parancsból félúton, a függvény példány folytatja az előző `await` hívható meg. Viselkedés később bővebben újraindításához.

## <a name="pattern-2-fan-outfan-in"></a>#2. minta: Fan-kimenő/fan-a

*Fan-kimenő/fan-lévő* párhuzamosan több funkciók végrehajtása, és az összes befejezéséhez majd várakozás mintát hivatkozik.  Gyakran néhány összesítési munkát a függvény által visszaadott eredmények.

![Fan-kimenő/fan-lévő diagramja](media/durable-functions-overview/fan-out-fan-in.png)

Normál funkciók, a szellőztető végezhető el több üzenetek küldése egy üzenetsorba funkciót ellátó. Vissza a szellőztető azonban sokkal több kihívást. Kód írása nyomon követésére, amikor a várólista-eseményindítókkal aktivált függvényeket végén, és tárolja a függvény kimenetek kellene. A tartós funkciók bővítmény kezeli az ebben a mintában viszonylag egyszerű kóddal.

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    var parallelTasks = new List<Task<int>>();
 
    // get a list of N work items to process in parallel
    object[] workBatch = await ctx.CallActivityAsync<object[]>("F1");
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = ctx.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }
 
    await Task.WhenAll(parallelTasks);
 
    // aggregate all N outputs and send result to F3
    int sum = parallelTasks.Sum(t => t.Result);
    await ctx.CallActivityAsync("F3", sum);
}
```

A kívánt szétterítési munkahelyi terjesztése függvény több példánya `F2`, és követi nyomon a munkahelyi feladatokat dinamikus listáját használja. A .NET `Task.WhenAll` API hívása várja meg az összes, a hívott függvény befejezéséhez. Ezt követően a `F2`függvény a dinamikus feladatlista összesítik és a átadva, és kiírja a `F3` függvény.

Az automatikus ellenőrzőpont-készítés, amely történik meg a `await` hívható meg `Task.WhenAll` biztosítja, hogy bármely összeomlási vagy újraindítást félúton keresztül nem szükséges újra kell indítani a már befejeződött feladatok.

## <a name="pattern-3-async-http-apis"></a>#3. minta: Aszinkron HTTP API-k

A harmadik minta nem minden a hosszan futó műveletek olyan külső ügyfelek állapotának koordináló problémáról. A közös valósítja meg az ebben a mintában módja azzal, hogy a hosszú futású művelet egy HTTP hívás által indított és az ügyfél ezután átirányítására egy állapot végpontot, amelyhez azokat lekérdezheti a további, a művelet befejezését.

![HTTP API diagramja](media/durable-functions-overview/async-http-api.png)

Tartós funkciókat biztosít a beépített API-k, amelyek megkönnyítik a hosszan futó függvény végrehajtások való interakció írt kódot. A [minták](durable-functions-install.md) megjelenítése egy egyszerű REST-parancs használható elindítani az új orchestrator-funkció példányai. Egy példányát az elindítása után a bővítmény webhook HTTP API-k, hogy a lekérdezés az orchestrator függvény állapotát mutatja meg. A következő példa bemutatja a REST-parancsok az orchestrator elindításához és állapotának lekérdezéséhez. Az átláthatóság érdekében egyes adatok hiányoznak a példa.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2017-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2017-03-16T21:20:57Z", ...}
```

Az állapot kezeli a tartós Functions futtatókörnyezete, mert nincs saját állapotának nyomon követése mechanizmus végrehajtásához.

Annak ellenére, hogy a tartós funkciók bővítmény rendelkezik hosszú futású álló üzenettípusok összehangolását kezelésére szolgáló beépített webhookokkal, valósíthat meg ebben a mintában a saját függvény eseményindítók (pl. HTTP, a várólista vagy az Eseményközpont kiválasztásával) segítségével saját és a `orchestrationClient` kötés. Egy üzenetsor-üzenetet használhatja például a indító lezárást.  Vagy használhat egy HTTP-eseményindítóval helyett a beépített webhookokkal, amely egy létrehozott kulcsot a hitelesítéshez használt Azure Active Directory hitelesítési házirend által védett. 

```cs
// HTTP-triggered function to start a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    TraceWriter log)
{
    // Function name comes from the request URL.
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);
    
    log.Info($"Started orchestration with ID = '{instanceId}'.");
    
    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

A [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` paraméter értéke az a `orchestrationClient` kimeneti, kötelező, amely a tartós funkciók bővítmény része. Módszerek kezdési, küldő az események, leáll, és lekérdezi-e új vagy meglévő orchestrator-funkció példányok biztosít. A fenti példában egy HTTP indított-függvény veszi a `functionName` értéket a bejövő URL-cím és, hogy egy érték fázisok [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). A kötés API majd visszaküldi a választ, amely tartalmazza a `Location` fejléc és a példányon, amely később segítségével keressen további információt a elindított példány állapotának regisztrálnia, vagy állítsa le azt.

## <a name="pattern-4-monitoring"></a>#4 mintát: figyelése

A figyelő mintát hivatkozik egy rugalmas *ismétlődő* folyamat például a munkafolyamat - lekérdezés csak bizonyos feltételek teljesülnek. Egy rendszeres időzítő indítófeltételt kezelheti egy egyszerű forgatókönyv, például a rendszeres karbantartási feladat, de az intervallumon statikus, példány élettartamát kezelése bonyolult válik. Tartós funkciók lehetővé teszi a rugalmas ismételt időszakok, a feladat életciklusának kezelését és a használatával hozhat létre több monitor folyamatok egyetlen vezénylési lehetőséget.

Példa a korábbi aszinkron HTTP API forgatókönyv volna lehet felcserélni. Ahelyett, hogy az ilyen végpont egy külső ügyfél figyelését egy hosszú ideig futó művelet, a hosszan futó figyelő használ fel külső végpont néhány állapotváltozás vár.

![A figyelő diagramja](media/durable-functions-overview/monitor.png)

Tartós funkciókat használ, több monitor, ahol a tetszőleges végpontot néhány sornyi kód hozhatók létre. A figyelők végrehajtásának befejezése bizonyos feltétel teljesül, vagy azonnali hatállyal a [DurableOrchestrationClient](durable-functions-instance-management.md), és a várakozási időközt módosíthatja a bizonyos feltétel (például az exponenciális leállítási.) A következő kód egy alapszintű figyelő valósítja meg.

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    int jobId = ctx.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();
    
    while (ctx.CurrentUtcDateTime < expiryTime) 
    {
        var jobStatus = await ctx.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform action when condition met
            await ctx.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration will sleep until this time
        var nextCheck = ctx.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await ctx.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform further work here, or let the orchestration end
}
```

Amikor egy kérelem érkezik, egy új vezénylési példány létrehozásakor a feladat azonosítóját. A példány állapota kérdezi le, amíg a feltétel teljesül, és a hurok van kilépett. Tartós időzítő segítségével szabályozhatja a lekérdezési időközt. További munkára majd hajtható végre, vagy az orchestration fejezheti. Ha a `ctx.CurrentUtcDateTime` meghaladja a `expiryTime`, a figyelő végpontok.

## <a name="pattern-5-human-interaction"></a>#5. minta: Emberi beavatkozást igényel

Folyamatok emberi beavatkozás vonatkozhat. A legbonyolultabb számítógépben az érintő emberek egy automatizált folyamatban, hogy személyek nem mindig a magas rendelkezésre állású és rugalmas felhőalapú szolgáltatásként. Automatizált folyamatok engedélyeznie kell a, és gyakran ehhez időtúllépések és kompenzációs logika használatával.

Például egy üzleti folyamat, amely magában foglalja a emberi beavatkozást igényel a jóváhagyási folyamatot. Például kezelőjéből jóváhagyásra lehet szükség, amelynek hossza meghaladja a bizonyos költség jelentés. Ha a kezelő 72 óra (lehet, hogy azok hiba szabadságon) belül nem hagyja jóvá, az eszkalációs folyamat jóváhagyása lekérése valaki más (lehet, hogy a kezelő-kezelője) lép működésbe.

![Emberi beavatkozást igényel diagramja](media/durable-functions-overview/approval.png)

Ebben a mintában az orchestrator függvény használatával valósítható meg. Az orchestrator szeretné használni a [tartós időzítő](durable-functions-timers.md) jóváhagyás-igénylést és időtúllépés esetén az ilyen. A várakoznia egy [külső esemény](durable-functions-external-events.md), amely az egyes emberi beavatkozást igényel által létrehozott értesítés lenne.

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    await ctx.CallActivityAsync("RequestApproval");
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = ctx.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = ctx.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = ctx.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await ctx.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await ctx.CallActivityAsync("Escalate");
        }
    }
}
```

Hozza létre a tartós időzítő hívása `ctx.CreateTimer`. Az értesítés érkezik `ctx.WaitForExternalEvent`. És `Task.WhenAny` határozza meg, hogy az ilyen beszerzését (időtúllépés történik első) jóváhagyási folyamat (a jóváhagyási időtúllépés érkezik).

## <a name="the-technology"></a>A technológia

A beépített a tartós funkciók bővítmény a háttérben a [tartós feladat keretrendszer](https://github.com/Azure/durabletask), egy nyílt forráskódú könyvtár a Githubon történő megtervezésével tartós feladat álló üzenettípusok összehangolását. Például az Azure Functions Mitől Azure webjobs-feladatok kiszolgáló nélküli fejlődéséhez, sokkal tartós funkciók tartós feladat keretében kiszolgáló nélküli fejlődéséhez. A tartós feladat keretében segítségével fokozottan Microsoft belül és kívül is kritikus fontosságú folyamatok automatizálása. Legyen egy természetes beválik, ha a kiszolgáló nélküli Azure Functions környezet.

### <a name="event-sourcing-checkpointing-and-replay"></a>Esemény forrás, az ellenőrzőpontok létrehozása és a visszajátszás

Az orchestrator funkciók képesek megbízhatóan fenntartani egy úgynevezett felhőkialakítási mintájában a végrehajtási állapotot [esemény forrás](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing). Helyett közvetlenül a *aktuális* az orchestration, a tartós bővítmény állapotának rögzítéséhez egy csak append tárolót használja a *műveletsorozattal teljes* által a függvény vezénylési. Számos előnyt, beleértve a teljesítmény, méretezhetőség és reakcióidőt "vonatkozó" teljes futásidejű állapot képest javítása azt. Más előnyöket nyújtja, és a végleges konzisztencia biztosít a tranzakciós adatokat, és teljes naplózási előzmények kezelése és az előzmények karbantartása. A napló ellenőrzését maguk megbízható kompenzációs műveletek engedélyezése.

Az esemény forrás a bővítmény által használata átlátszó. A színfalak a `await` operátor egy orchestrator függvényben a tartós feladat keretrendszer kézbesítő vissza a vezérlő az orchestrator szál adja eredményül. A kézbesítő majd érvényesítése bármely új műveletek az orchestrator függvény ütemezett (például egy vagy több alárendelt függvényt hívja, vagy egy tartós időzítő ütemezés) tárhelyre. A transzparens véglegesítési művelet hozzáfűzi a *futtatási előzményei* az orchestration-példány. Az előzmények tárolási tábla tárolja. A véglegesítési művelet üzenetek a tényleges munkát ütemezése várólista hozzáadja. Ezen a ponton az orchestrator függvény memóriából lehet. Az elszámolási leállítja használata az Azure funkciók fogyasztás megtervezése.  Ehhez több munka esetén, a függvény újraindul, és állapotában újraépíti.

Miután egy vezénylési függvényben megadott van ehhez a további munkahelyi (például egy válaszüzenet érkezett vagy tartós idő letelte után), az orchestrator felébred újra és újra végrehajtja a teljes funkció elejéről. ahhoz, hogy a helyi állapot építse újra. Ha a visszajátszás közben a kód megpróbálja meghívni a függvényt (vagy bármely más aszinkron munkahelyi), tartós feladat keretében olvas, és a *futtatási előzményei* , az aktuális vezénylési. Ha úgy találja, hogy a tevékenység függvény már által végrehajtott eredményezte néhány eredményt, replays a függvény eredménye és az orchestrator kód továbbra is futnak. Ez továbbra is fennáll, addig, amíg a függvény kód lekér egy pontra, ahol befejeződött vagy ütemezett új aszinkron feladatot van folyamatban.

### <a name="orchestrator-code-constraints"></a>Az orchestrator kód megkötések

A visszajátszás viselkedés kódot, amely egy orchestrator függvényben csak írható típusú megkötéseket hoz létre. Például az orchestrator kódjának különböznie kell determinisztikus, több alkalommal üzenetet fog, és ki kell dolgoznia ugyanazt az eredményt minden alkalommal, amikor. A megkötések teljes listája megtalálható a [Orchestrator kód megkötések](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints) szakasza a **ellenőrzőpontokat, és indítsa újra a** cikk.

## <a name="language-support"></a>Nyelvi támogatás

C# jelenleg az egyetlen támogatott nyelvi a tartós funkcióihoz. Ez magában foglalja a tevékenységet és az orchestrator függvények. A jövőben minden nyelven, amely támogatja az Azure Functions támogatni fogják azt. Tekintse meg az Azure Functions [GitHub tárház problémák listájába](https://github.com/Azure/azure-functions-durable-extension/issues) legfrissebb állapotát a további nyelv támogatja tekintheti meg.

## <a name="monitoring-and-diagnostics"></a>Monitorozás és diagnosztika

A tartós funkciók bővítmény automatikusan bocsát ki a strukturált nyomon követési adatok [Application Insights](functions-monitoring.md) Ha a függvény alkalmazást az Application Insights instrumentation kulccsal van konfigurálva. Nyomon követési adatok viselkedést és a álló üzenettípusok összehangolását állapotának figyelésére használható.

Íme egy példa hogyan a tartós funkciók nyomon követés meg az Application Insights portálon [Application Insights Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics):

![App Insights lekérdezés eredményei](media/durable-functions-overview/app-insights-1.png)

A csomagolt hasznos a strukturált adatok sok a `customDimensions` minden naplóbejegyzés mezőbe. Íme egy példa egy ilyen bejegyzés bonthatók ki teljesen.

![App Insights lekérdezés customDimensions mezője](media/durable-functions-overview/app-insights-2.png)

A visszajátszás jelenséget az a tartós feladat keretrendszer kézbesítő várhatóan redundáns naplóbejegyzése mértékéig megismételt műveletek megjelenítéséhez. Ez lehet a visszajátszás viselkedését az alapvető vezérlőprogramjával működésének megértése. A [diagnosztika](durable-functions-diagnostics.md) a cikk bemutatja, hogy kiszűrhetők a visszajátszás naplók hívjuk fel a "valós idejű" naplók mintalekérdezések.

## <a name="storage-and-scalability"></a>Tárolás és méretezhetőség

A tartós funkciók bővítmény megőrizni a végrehajtási előzményei állapotát és eseményindító függvény végrehajtása Azure tárolási sorok, a táblák és a blobokat használ. Az alapértelmezett tárfiókot, a függvény alkalmazáshoz használható, vagy beállíthatja, hogy egy külön tárfiókot. Érdemes lehet egy külön fiókot tárolási átviteli sebességének korlátai miatt. Az orchestrator írt kódot nem kell (és nem kell) működnie ezekre a tárfiókokra entitást. Az entitások kezeli közvetlenül tartós feladat keretében, egy megvalósítási részletei.

Az orchestrator funkciók tevékenység funkciók ütemezése, és a válaszok keresztül belső üzenetsor-üzeneteket fogadni. Egy függvény alkalmazás futtatásakor az Azure Functions használat csomagban, ezek a várólisták által figyelt a [Azure Functions méretezése vezérlő](functions-scale.md#how-the-consumption-plan-works) és új számítási példányt igény szerint adja hozzá. Horizontálisan több virtuális géphez, amikor az orchestrator függvény által futtatható egy virtuális gép tevékenység funkciók meghívja számos különböző virtuális gépek az futtatása közben. További részleteket talál a skálázási viselkedés tartós funkciók [teljesítmény és méretezhetőség](durable-functions-perf-and-scale.md).

A TABLE storage a futtatási előzményei orchestrator fiókok tárolására szolgál. Amikor egy példány rehydrates egy adott virtuális gépen, azt beolvassa a futtatási előzményei a table storage a úgy, hogy azt a helyi állapotában használható. A Table storage-ban rendelkezésre álló előzmények kapcsolatos kényelmes dolog, hogy tekintse meg, és tekintse meg a álló eszközökkel, például a üzenettípusok összehangolását előzményeinek [Microsoft Azure Tártallózó](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Az Azure Tártallózó képernyőkép](media/durable-functions-overview/storage-explorer.png)

> [!WARNING]
> Is könnyen és kényelmes, a table storage-ban végrehajtási előzményei között találja, elkerülése érdekében ebben a táblázatban minden függőségi véve. A tartós funkciók bővítmény fejlődésének meg.

## <a name="known-issues-and-faq"></a>Ismert problémák és gyakran ismételt kérdések

Általában minden ismert problémákat kell követhető nyomon a [GitHub problémák](https://github.com/Azure/azure-functions-durable-extension/issues) listája. Ha problémába ütközik, és a probléma nem találja a Githubon, nyisson meg egy új problémát, és a probléma részletes leírását tartalmazza. Akkor is, ha egyszerűen kívánt tegyen fel kérdést, nyugodtan nyissa meg a GitHub probléma címkével kérdésként.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Továbbra is a tartós funkciók dokumentációja olvasása](durable-functions-bindings.md)

> [!div class="nextstepaction"]
> [Telepítse a tartós funkciók bővítményt, és minták](durable-functions-install.md)

