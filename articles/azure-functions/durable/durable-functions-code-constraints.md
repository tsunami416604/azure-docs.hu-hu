---
title: Tartós orchestrator kódmegkötések - Azure Functions
description: Vezénylési függvény visszajátszása és kódmegkötések az Azure durable functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 4ed604302ca187ad4953e865d68dc73030a37c02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562139"
---
# <a name="orchestrator-function-code-constraints"></a>Orchestrator függvénykód-megkötések

A Durable Functions az [Azure Functions](../functions-overview.md) bővítménye, amely lehetővé teszi az állapotalapú alkalmazások készítését. Egy [orchestrator függvény](durable-functions-orchestrations.md) taszthat ja vezénylheti a függvényalkalmazáson belüli egyéb tartós függvények végrehajtását. Az Orchestrator-függvények állapotalapúak, megbízhatóak és potenciálisan hosszú ideig futnak.

## <a name="orchestrator-code-constraints"></a>Vezénylői kódkorlátozások

Az Orchestrator függvények [eseményforrást](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) használnak a megbízható végrehajtás és a helyi változó állapotának fenntartása érdekében. Az orchestrator kód [visszajátszási viselkedése](durable-functions-orchestrations.md#reliability) megkötéseket hoz létre az orchestrator függvényben írható kód típusára. Például az orchestrator függvényeknek *determinisztikusnak*kell lenniük: az orchestrator függvénytöbbszöri visszajátszásra kerül, és minden alkalommal ugyanazt az eredményt kell produkálnia.

### <a name="using-deterministic-apis"></a>Determinisztikus API-k használata

Ez a szakasz néhány egyszerű útmutatást tartalmaz, amelyek biztosítják, hogy a kód determinisztikus legyen.

Az Orchestrator függvények bármely API-t meghívhatnak a célnyelveken. Fontos azonban, hogy az orchestrator függvények csak determinisztikus API-kat hívjanak meg. A *determinisztikus API* egy API, amely mindig ugyanazt az értéket adja vissza, ha ugyanazt a bemenetet kapja, függetlenül attól, hogy mikor és milyen gyakran hívják meg.

Az alábbi táblázat példákat mutat be olyan API-kra, amelyeket el kell kerülnie, mert *nem* determinisztikusak. Ezek a korlátozások csak az orchestrator függvényekre vonatkoznak. Más függvénytípusok nem rendelkeznek ilyen korlátozásokkal.

| API-kategória | Ok | Áthidaló megoldás |
| ------------ | ------ | ---------- |
| Dátumok és időpontok  | Az aktuális dátumot vagy időpontot visszaadó API-k nem determinisztikusak, mivel a visszaadott érték minden visszajátszásnál eltérő. | Használja`CurrentUtcDateTime` az API-t `currentUtcDateTime` a .NET vagy az API JavaScript, amelyek biztonságos a visszajátszás. |
| GUID-k és UUID-k  | A véletlenszerű GUID-ot vagy UUID-t visszaadó API-k nem determinisztikusak, mivel a generált érték minden visszajátszásnál eltérő. | Használja `NewGuid` a .NET vagy a JavaScript biztonságosan véletlenszerű GUID.Use in .NET or `newGuid` in JavaScript to safely generate random GUIDs. |
| Véletlen számok | A véletlen számokat visszaadó API-k nem determinisztikusak, mivel a generált érték minden visszajátszásnál eltérő. | Egy tevékenységfüggvény segítségével adja vissza a véletlenszerű számokat egy vezénylési. A tevékenységfüggvények visszatérési értékei mindig biztonságosak a visszajátszáshoz. |
| Kötések | A bemeneti és kimeneti kötések általában i/o-t tesznek ki, és nem determinisztikusak. Az orchestrator függvény nem használhatja közvetlenül még a [vezénylési ügyfél-](durable-functions-bindings.md#orchestration-client) és [entitásügyfél-kötéseket](durable-functions-bindings.md#entity-client) sem. | Az ügyfél- vagy tevékenységfüggvényeken belüli bemeneti és kimeneti kötések használata. |
| Network (Hálózat) | A hálózati hívások külső rendszereket érintenek, és nem determinisztikusak. | Tevékenységfüggvények használatával kezdeményezzen hálózati hívásokat. Ha HTTP-hívást kell kezdeményeznie az orchestrator függvényből, használhatja a [tartós HTTP API-kat](durable-functions-http-features.md#consuming-http-apis)is. |
| API-k blokkolása | Blokkoló API-k, mint `Thread.Sleep` a .NET és hasonló API-k teljesítmény- és méretezési problémákat okozhat az orchestrator függvények, és el kell kerülni. Az Azure Functions consumptioni csomagban akár szükségtelen futásidejű díjakat is eredményezhetnek. | Használjon alternatívákat az API-k letiltására, ha azok elérhetők. Például a `CreateTimer` vezénylési végrehajtás késleltetésének bevezetéséhez használható. [A tartós időzítő](durable-functions-timers.md) késések nem számítanak bele az orchestrator függvény végrehajtási idejébe. |
| Aszinkron API-k | Az Orchestrator-kód soha nem indíthat el `IDurableOrchestrationContext` aszinkron `context.df` műveletet, kivéve az API vagy az objektum API-jának használatával. Például `Task.Run`nem használhatja a `Task.Delay`, `HttpClient.SendAsync` és a `setTimeout` `setInterval` . A tartós feladatkeretrendszer egyetlen szálon futtatja az orchestrator-kódot. Nem tud más szálakkal együttműködni, amelyeket más aszinkron API-k hívhatnak meg. | Az orchestrator függvénycsak tartós aszinkron hívásokat kell kezdeményeznie. A tevékenységfüggvényeknek minden más aszinkron API-hívást kell kezdeményezniük. |
| JavaScript-függvények aszinkron | A JavaScript orchestrator függvények `async` nem deklarálhatók, mert a node.js futásidejű nem garantálja, hogy az aszinkron függvények determinisztikusak. | JavaScript orchestrator függvények deklarálása szinkron generátor függvényként. |
| Szálas API-k | A tartós feladatkeretrendszer egyetlen szálon futtatja az orchestrator-kódot, és nem tud más szálakkal együttműködni. Új szálak bevezetése a vezénylési végrehajtás vezethet nem determinisztikus végrehajtás vagy holtpontra jutott. | Orchestrator függvények szinte soha nem használja threading API-kat. A .NET-ben például `ConfigureAwait(continueOnCapturedContext: false)`ne használja a; ez biztosítja, hogy a feladatok folytatása az `SynchronizationContext`orchestrator függvény eredeti énjein fusson. Ha ilyen API-k szükségesek, korlátozza azok használatát csak tevékenységfunkciókra. |
| Statikus változók | Ne használjon nem állandó statikus változókat az orchestrator függvényekben, mert értékeik idővel változhatnak, ami nem determinisztikus futásidejű viselkedést eredményez. | Használjon állandókat, vagy korlátozza a statikus változók használatát a tevékenységfüggvényekre. |
| Környezeti változók | Ne használjon környezeti változókat az orchestrator függvényekben. Értékeik idővel változhatnak, ami nem determinisztikus futásidejű viselkedést eredményez. | A környezeti változókra csak az ügyfélfüggvényekvagy tevékenységfüggvények ből lehet hivatkozni. |
| Végtelen hurkok | Kerülje a végtelen hurkok orchestrator függvények. Mivel a tartós feladatkeretrendszer a vezénylési függvény előrehaladtával menti a végrehajtási előzményeket, a végtelen ciklus miatt az orchestrator példány ból kifogy a memória. | Végtelen ciklusforgatókönyvek esetén a `ContinueAsNew` .NET vagy `continueAsNew` a JavaScript hez hasonló API-k használatával indítsa újra a függvény-végrehajtást, és vesse el a korábbi végrehajtási előzményeket. |

Bár ezek a korlátok alkalmazása elsőre nehéznek tűnhet, a gyakorlatban könnyen követhetőek.

A tartós feladatkeretrendszer megpróbálja észlelni az előző szabályok megsértését. Ha megsérül, a keretrendszer egy **NonDeterministicOrchestrationException** kivételt okoz. Ez az észlelési viselkedés azonban nem fogja elkapni az összes jogsértést, és nem függhet tőle.

## <a name="versioning"></a>Verziókezelés

A tartós vezénylési lehet futtatni folyamatosan napokig, hónapokig, évekig, vagy akár [örökké](durable-functions-eternal-orchestrations.md). A Tartós függvények alkalmazások, amelyek befolyásolják a befejezetlen vezénylési hatással lehet megtörni a vezénylések visszajátszási viselkedését. Ezért fontos, hogy gondosan tervezze meg a kód frissítéseket. A kód verziószámozásának részletesebb leírását a [verziószámozásról szóló cikkben](durable-functions-versioning.md)olvashatja.

## <a name="durable-tasks"></a>Tartós feladatok

> [!NOTE]
> Ez a szakasz a tartós feladatkeret belső megvalósítási részleteit ismerteti. A tartós függvényeket az adatok ismerete nélkül is használhatja. Ez csak az, hogy segítsen megérteni a visszajátszás viselkedését.

Az orchestrator függvényekben biztonságosan várakozó feladatokat esetenként tartós feladatoknak is *nevezik.* A tartós feladatkeretrendszer létrehozza és kezeli ezeket a feladatokat. Ilyenek például a **CallActivityAsync**, **a WaitForExternalEvent**és a **CreateTimer** által a .NET orchestrator függvényekben visszaadott feladatok.

Ezeket a tartós feladatokat belsőleg kezeli a .NET objektumainak `TaskCompletionSource` listája. A visszajátszás során ezek a feladatok az orchestrator kódvégrehajtásának részeként jönnek létre. A diszpécser a megfelelő előzményesemények et sorolja fel.

A feladatok végrehajtása szinkron módon egyetlen szál használatával történik, amíg az összes előzményt vissza nem játssza. Az előzményismétlés végéig be nem fejezett tartós feladatok megfelelő műveleteket hajtanak végre. Előfordulhat például, hogy egy üzenet várólistára kerül egy tevékenységfüggvény hívásához.

Ez a szakasz leírása a futásidejű viselkedés segít megérteni, hogy miért `await` `yield` egy orchestrator függvény nem használható, vagy egy nem tartós feladat. Ennek két oka van: a diszpécser szál nem tud várni a feladat befejezéséhez, és minden visszahívás, hogy a feladat potenciálisan sérült a követési állapotát az orchestrator függvény. Néhány futásidejű ellenőrzés van érvényben, hogy segítsen felismerni ezeket a jogsértéseket.

Ha többet szeretne megtudni arról, hogy a tartós feladatkeretrendszer hogyan hajtja végre az orchestrator-függvényeket, olvassa el a [Tartós feladat forráskódját a GitHubon.](https://github.com/Azure/durabletask) Lásd különösen [a TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) és [TaskOrchestrationContext.cs.](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ az alvezőműveletek meghívásáról](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [További információ a verziószámozás kezeléséről](durable-functions-versioning.md)
