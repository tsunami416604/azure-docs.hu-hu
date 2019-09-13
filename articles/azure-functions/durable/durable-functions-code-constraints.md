---
title: Tartós Orchestrator-kódokra vonatkozó korlátozások – Azure Functions
description: A koordináló függvény újrajátszása és a kód megkötései az Azure Durable Functions számára.
author: cgillum
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/18/2019
ms.author: azfuncdf
ms.openlocfilehash: 87851a4879760c76950f765d05de4662ecb04bea
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935767"
---
# <a name="orchestrator-function-code-constraints"></a>Orchestrator megkötések

Durable Functions a [Azure functions](../functions-overview.md) kiterjesztése, amely lehetővé teszi az állapot-nyilvántartó alkalmazások kiépítését. A [Orchestrator függvénnyel](durable-functions-orchestrations.md) más tartós függvények végrehajtását is elvégezheti a Function alkalmazásban. A Orchestrator-függvények állapot-, megbízható és potenciálisan hosszú ideig futók.

## <a name="orchestrator-code-constraints"></a>Orchestrator-kód megkötései

A Orchestrator függvények az [események beszerzését](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) használják a megbízható végrehajtás és a helyi változó állapotának fenntartása érdekében. A Orchestrator kód [újrajátszása](durable-functions-orchestrations.md#reliability) a Orchestrator függvényben írható kód típusára vonatkozó korlátozásokat hoz létre. A Orchestrator-kódnak például *determinisztikus*kell lennie.  A Orchestrator függvények többször is lejátszhatók, és minden alkalommal ugyanazt az eredményt kell létrehozni.

A következő szakaszokban néhány egyszerű útmutatást talál a kód determinisztikus biztosításához.

### <a name="using-deterministic-apis"></a>Determinisztikus API-k használata

A Orchestrator függvények tetszőleges API-t hívhatnak meg a célként megadott nyelven. Fontos azonban, hogy a Orchestrator functions csak a *determinisztikus* API-kat hívja meg. A *DETERMINISZTIKUS API* egy olyan API, amely mindig ugyanazt az értéket adja vissza, amely ugyanazt a bemenetet adta, függetlenül attól, hogy mikor vagy milyen gyakran hívják.

Az alábbi példák olyan API-kat mutatnak be, amelyeket el kell kerülni, mert *nem* determinisztikus. Ezek a korlátozások csak a Orchestrator függvényekre érvényesek. Más függvények típusai nem rendelkeznek ilyen korlátozásokkal.

| API-kategória | Reason | Áthidaló megoldás |
| ------------ | ------ | ---------- |
| Dátumok és időpontok  | Azok az API-k, amelyek az _aktuális_ dátumot vagy időpontot adják vissza, nem determinisztikus, mert a visszaadott érték eltérő lesz minden újrajátszás esetén. | Használja a [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) (.net) vagy `currentUtcDateTime` a (JavaScript) API-t, amely biztonságos az újrajátszás számára. |
| GUID azonosítók/UUID-ket  | A _véletlenszerű_ GUID/UUID értéket visszaadó API-k nem determinisztikus, mert a generált érték minden visszajátszás esetében eltérő lesz. | Véletlenszerű GUID [](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_NewGuid) azonosítók biztonságos létrehozásához `newGuid` használja a NewGuid (.net) vagy (JavaScript). |
| Véletlenszerű számok | A véletlenszerű számokat visszaadó API-k nem determinisztikus, mert a generált érték minden visszajátszás esetében eltérő lesz. | Egy tevékenység függvénnyel véletlenszerű számokat adhat vissza egy előkészítési művelethez. A tevékenység-függvények visszatérési értékei mindig biztonságosak a visszajátszás esetén. |
| Kötések | A bemeneti és kimeneti kötések általában I/O-műveletek, és nem determinisztikus. Még a előkészítési [ügyfelet](durable-functions-bindings.md#orchestration-client) és az [entitás-ügyfél](durable-functions-bindings.md#entity-client) kötéseit sem szabad közvetlenül egy Orchestrator függvénnyel használni. | Bemeneti és kimeneti kötések használata az ügyfélen vagy a tevékenységi függvényeken belül. |
| Network (Hálózat) | A hálózati hívások külső rendszereket érintenek, és nem determinisztikus. | Hálózati hívások létrehozásához használja a Activity functions funkciót. Ha HTTP-hívást kell létrehoznia a Orchestrator függvényből, lehetősége van a [tartós http API](durable-functions-http-features.md#consuming-http-apis)-k használatára is. |
| API-k blokkolása | Az API `Thread.Sleep` -k (például .net) vagy más hasonló API-k blokkolása a Orchestrator függvények teljesítményére és skálázására is kényszerítheti a problémákat. A Azure Functions-használati tervben a végrehajtásuk szükségtelenül is járhat. | Alternatívákat használhat az API-k letiltására `CreateTimer` , például a munkafolyamatok végrehajtásának késésének bevezetéséhez. A [tartós időzítő](durable-functions-timers.md) késései nem számítanak bele egy Orchestrator függvény végrehajtási idejébe. |
| Aszinkron API-k | A Orchestrator-kódnak **soha nem kell aszinkron műveletet kezdeményeznie** , kivéve a [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) API vagy `context.df` az objektum API-ját. `Task.Run`Például: nem ,`setTimeout()` .net `Task.Delay` vagy`setInterval()` JavaScript esetén. `HttpClient.SendAsync` A tartós feladat-keretrendszer egyetlen szálon hajtja végre a Orchestrator-kódot, és nem tud kommunikálni más aszinkron API-k által ütemezhető más szálakkal. | Egy Orchestrator függvény csak *tartós* aszinkron hívásokat hajthat végre. Minden más aszinkron API-hívást el kell végezni a tevékenységi függvényekből. |
| Aszinkron JavaScript-függvények | A JavaScript Orchestrator függvények nem `async` lehetnek, mert a Node. js futtatókörnyezet nem garantálja, hogy az aszinkron függvények determinisztikus. | A JavaScript Orchestrator függvényeket szinkron létrehozó függvényekként kell deklarálni. |
| Többszálú API-k | A tartós feladat-keretrendszer egyetlen szálon hajtja végre a Orchestrator-kódot, és nem tud más szálakkal kommunikálni. Az új szálaknak egy előkészítési folyamatba való bevezetéséhez nem determinisztikus végrehajtás vagy holtpont is vezethet. | A Threading API-kat szinte soha nem szabad használni a Orchestrator functions szolgáltatásban. Ha szükségesek, csak a tevékenységi funkciókra korlátozódnak. |
| Statikus változók | A nem állandó statikus változókat el kell kerülni a Orchestrator functions szolgáltatásban, mert az értékek idővel változhatnak, ami a nem determinisztikus végrehajtás viselkedését eredményezi. | Használjon állandókat, vagy korlátozza a statikus változók használatát a tevékenység funkcióihoz. |
| Környezeti változók | Ne használjon környezeti változókat a Orchestrator functions szolgáltatásban. Az értékek az idő múlásával változhatnak, ami a nem determinisztikus végrehajtási viselkedést eredményezi. | A környezeti változókat csak az ügyfél-vagy a tevékenység-függvényekből lehet hivatkozni. |
| Végtelen hurkok | A végtelen hurkokat el kell kerülni a Orchestrator functions szolgáltatásban. A tartós feladat-keretrendszer elmenti a végrehajtási előzményeket, ahogy az előkészítési művelet folyamatban van, így egy végtelen hurok miatt előfordulhat, hogy egy Orchestrator-példány elfogyott a memóriából. | Végtelen hurkos forgatókönyvek esetén használjon API-kat (például [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) ( `continueAsNew` .net) vagy (JavaScript)) a függvény végrehajtásának újraindításához és a korábbi végrehajtási előzmények elvetéséhez. |

Habár ezek a megkötések először is ijesztőnek tűnhet, a gyakorlatban nem nehéz követni őket. A tartós feladat-keretrendszer megkísérli a fenti szabályok megsértésének észlelését, és egy `NonDeterministicOrchestrationException`. Ez az észlelési viselkedés azonban a legjobb erőfeszítés, és nem függ tőle.

## <a name="versioning"></a>Verziókezelés

A tartós előkészítés napokon, hónapokban, években vagy akár [örökké](durable-functions-eternal-orchestrations.md)is elvégezhető. A még nem befejezett munkafolyamatokat befolyásoló Durable Functions alkalmazásokra vonatkozó összes kód megszakíthatja az újrajátszás viselkedését. Ezért fontos, hogy körültekintően tervezze meg a kód frissítését. A kód verziószámozásának részletes ismertetését a [verziószámozást](durable-functions-versioning.md) ismertető cikkben találja.

## <a name="durable-tasks"></a>Tartós feladatok

> [!NOTE]
> Ez a szakasz a tartós feladatok keretrendszerének belső megvalósítási részleteit ismerteti. A Durable Functions az információk ismerete nélkül is használhatja. Ez csak az újrajátszás viselkedésének megismerésére szolgál.

A Orchestrator függvényekben biztonságosan megtekinthető feladatok időnként *tartós feladatoknak*is nevezik. Ezeket a feladatokat a tartós feladatok keretrendszere hozza létre és kezeli. Ilyenek például a, `CallActivityAsync` `WaitForExternalEvent`a és `CreateTimer` a .net Orchestrator függvények által visszaadott feladatok.

Ezek a *tartós feladatok* belsőleg kezelhetők a .net- `TaskCompletionSource` objektumok listájának használatával. Az újrajátszás során ezek a feladatok a Orchestrator-kód végrehajtásának részeként jönnek létre, és a kiosztók a megfelelő előzmény-események enumerálásával lesznek végrehajtva. A végrehajtás szinkronban történik egyetlen szál használatával, amíg az összes előzmény újra nem lett játszva. Az előzmények végének újrajátszása által nem befejezett tartós feladatok megfelelő műveleteket hajtanak végre. Előfordulhat például, hogy egy várólistán lévő hívni egy üzenetet.

Az itt ismertetett végrehajtási viselkedésnek meg kell ismernie, hogy miért nem `await` vagy `yield` nem tartós feladat a Orchestrator funkció kódja: a diszpécser szál nem várja meg, amíg befejeződik, és az adott feladat által visszaadott visszahívások esetlegesen sérült a Orchestrator függvény nyomkövetési állapota. A rendszer néhány futásidejű ellenőrzést végez, hogy megpróbálja felderíteni ezeket a szabálysértéseket.

Ha további információra van szüksége arról, hogy az állandó feladati keretrendszer hogyan hajtja végre a Orchestrator functions szolgáltatást, a [](https://github.com/Azure/durabletask)legjobb megoldás, ha az állandó feladathoz tartozó forráskódot a githubon tekinti át. Különösen lásd: [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) és [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Megtudhatja, hogyan hívhat meg alfolyamatokat](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Útmutató a verziószámozás kezeléséhez](durable-functions-versioning.md)
