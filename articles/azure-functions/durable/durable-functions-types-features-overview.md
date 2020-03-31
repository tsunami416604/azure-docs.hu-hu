---
title: Függvénytípusok az Azure durable functions-ben
description: Ismerje meg, hogy milyen típusú függvények és szerepkörök, amelyek támogatják a függvény-funkció kommunikáció egy tartós függvények vezénylési az Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: azfuncdf
ms.openlocfilehash: 35ef9d8731e169e890f5985ce01215fec5d6e3de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277881"
---
# <a name="durable-functions-types-and-features"></a>Tartós funkciók típusai és szolgáltatásai

A Durable Functions az [Azure Functions](../functions-overview.md)kiterjesztése. A tartós függvények a függvényvégrehajtás állapotalapú vezénylése használható. A tartós függvényalkalmazás olyan megoldás, amely különböző Azure-függvényekből áll. A függvények különböző szerepköröket játszhatnak egy tartós függvény vezénylésében. 

Jelenleg négy tartós függvénytípus van az Azure Functionsben: tevékenység, orchestrator, entitás és ügyfél. A szakasz további részletei a vezénylési funkciók típusairól.

## <a name="orchestrator-functions"></a>Orchestrator függvények

Az Orchestrator függvények a műveletek végrehajtását és a műveletek végrehajtását leíró sorrendet írják le. Az orchestrator függvények a kódban (C# vagy JavaScript) történő vezénylést írják le a [Durable Functions alkalmazásmintákban](durable-functions-overview.md#application-patterns)látható módon. A vezénylési műveletek számos különböző típusú műveletet használhatnak, beleértve [a tevékenységfüggvényeket,](#activity-functions) [az alvezőtevékenységeket](durable-functions-orchestrations.md#sub-orchestrations), [a külső eseményekre, a](durable-functions-orchestrations.md#external-events) [HTTP-t](durable-functions-http-features.md)és [az időzítőket.](durable-functions-orchestrations.md#durable-timers) Az Orchestrator függvényei entitásfüggvényekkel is kölcsönhatásba [léphetnek.](#entity-functions)

> [!NOTE]
> Orchestrator függvények írják a szokásos kódot, de vannak szigorú követelmények, hogyan kell írni a kódot. Pontosabban, orchestrator függvénykód kell *determinisztikus*. Ezeknek a determinizmuskövetelményeknek a be nem hagyása azt eredményezheti, hogy az orchestrator függvényei nem futnak megfelelően. Ezekről a követelményekről és azok megkerülésének módjáról részletes információk találhatók a [kódmegkötések](durable-functions-code-constraints.md) témakörében.

Az orchestrator-függvényekkel és azok jellemzőivel kapcsolatos részletesebb információkért tekintse meg a [Tartós vezénylések](durable-functions-orchestrations.md) cikket.

## <a name="activity-functions"></a>Tevékenységfüggvények

A tevékenységfüggvények a tartós függvényvezénylés alapvető munkaegysége. A tevékenységfüggvények a folyamatban vezénylendő függvények és feladatok. Létrehozhat például egy orchestrator függvényt egy rendelés feldolgozásához. A feladatok közé tartozik a készlet ellenőrzése, a vevő terhelése és a szállítmány létrehozása. Minden feladat külön tevékenységfüggvény lenne. Ezek a tevékenységfüggvények sorozatosan, párhuzamosan vagy a kettő valamilyen kombinációjával hajthatók végre.

Az orchestrator függvényeivel ellentétben a tevékenységfüggvények nincsenek korlátozva a bennük elvégzett munka típusában. A tevékenységfüggvényeket gyakran használják hálózati hívások kezdeményezésére vagy cpu-igényes műveletek futtatására. Egy tevékenységfüggvény is visszaadhat adatokat az orchestrator függvény. A tartós feladat keretrendszer garantálja, hogy minden egyes megnevezett tevékenység függvény végrehajtása *legalább egyszer* egy vezénylési végrehajtása során.

> [!NOTE]
> Mivel a tevékenységfüggvények csak *legalább egyszer* garantálják a végrehajtást, azt javasoljuk, hogy a tevékenységfüggvény *logikai logikai idempotens,* amikor csak lehetséges.

[Tevékenységeseményindító](durable-functions-bindings.md#activity-trigger) használatával definiáljon egy tevékenységfüggvényt. A .NET `DurableActivityContext` függvények paraméterként kapnak. Az eseményindítót bármely más JSON-szerializálható objektumhoz is kötheti, hogy a függvény bemeneteit átadja. JavaScript ben az `<activity trigger binding name>` [ `context.bindings` objektum](../functions-reference-node.md#bindings)on keresztül is hozzáférhet egy bemenethez. A tevékenységfüggvények csak egyetlen értéket adhatnak át nekik. Több érték átadásához törzseket, tömböket vagy összetett típusokat kell használnia.

> [!NOTE]
> Tevékenységfüggvényt csak egy orchestrator függvényből indíthat el.

## <a name="entity-functions"></a>Entitásfüggvények

Az entitásfüggvények meghatározzák a kis állapotdarabok olvasására és frissítésére szolgáló műveleteket. Gyakran hivatkozunk ezekre az állapotozó entitásokra, mint *tartós entitásokra.* Az orchestrator függvényeihez hasonlóan az entitásfüggvények is speciális eseményindító-típussal rendelkező függvények, *entitáseseményindító*. Ügyfélfüggvények vagy orchestrator függvények is meghívhatók. Az orchestrator függvényeivel ellentétben az entitásfüggvények nem rendelkeznek konkrét kódmegkötések. Az entitásfüggvények explicit módon is kezelik az állapotot, nem pedig implicit módon az állapotot jelölik a vezérlési folyamaton keresztül.

> [!NOTE]
> Az entitásfüggvények és a kapcsolódó funkciók csak a Durable Functions 2.0 és újabb verziókban érhetők el.

Az entitásfüggvényekről a [Tartós entitások](durable-functions-entities.md) című cikkben olvashat bővebben.

## <a name="client-functions"></a>Ügyfélfüggvények

Az orchestrator függvényeit egy [vezénylési eseményindító kötés](durable-functions-bindings.md#orchestration-trigger) váltja ki, az entitásfüggvényeket pedig egy [entitásesemény-kötés.](durable-functions-bindings.md#entity-trigger) Mindkét eseményindító úgy működik, hogy reagál a [feladatközpontba](durable-functions-task-hubs.md)várólistára helyezett üzenetekre. Az üzenetek kézbesítésének elsődleges módja egy [orchestrator ügyfélkötés](durable-functions-bindings.md#orchestration-client) vagy egy [entitásügyfél-kötés](durable-functions-bindings.md#entity-client) használata egy *ügyfélfüggvényen*belül. Bármely nem orchestrator függvény lehet *ügyfélfüggvény.* Például az orchestrator egy HTTP-aktivált függvény, egy Azure Event Hub aktivált függvény, stb. Mi teszi a függvényt *ügyfélfunkcióvá,* az a tartós ügyfélkimeneti kötés használata.

> [!NOTE]
> Más függvénytípusoktól eltérően az orchestrator és az entitásfüggvények nem aktiválhatók közvetlenül az Azure Portal on-in található gombok használatával. Ha azt szeretné, hogy tesztelje az orchestrator vagy entitás függvényaz Azure Portalon, ehelyett futtatnia kell egy *ügyfélfüggvényt,* amely elindítja az orchestrator vagy entitás függvény t a megvalósítás részeként. A legegyszerűbb tesztelési élmény érdekében *kézi trigger* funkció ajánlott.

Az orchestrator vagy entitásfüggvények aktiválása mellett a *tartós ügyfélkötés* használható futó vezénylési és entitások. Például vezénylések lekérdezhető, megszüntethető, és eseményeket emelhetnek rájuk. Az vezénylések és entitások kezeléséről további információt a [Példánykezelés](durable-functions-instance-management.md) című cikkben talál.

## <a name="next-steps"></a>További lépések

A kezdéshez hozza létre az első tartós függvényt [C#](durable-functions-create-first-csharp.md) vagy [JavaScript nyelven.](quickstart-js-vscode.md)

> [!div class="nextstepaction"]
> [További információ a tartós függvények vezényléséről](durable-functions-orchestrations.md)