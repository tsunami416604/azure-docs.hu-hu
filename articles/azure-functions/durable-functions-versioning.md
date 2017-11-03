---
title: "A tartós funkciók - Azure Versioning"
description: "Útmutató az Azure Functions a tartós funkciók bővítmény versioning valósít meg."
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
ms.openlocfilehash: 0a86e4a87f5ec23c284aa4e5cfb2c67622b3ebe9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="versioning-in-durable-functions-azure-functions"></a>A tartós függvények (az Azure Functions) Versioning

Célszerű előbb vagy utóbb elkerülhetetlenné, hogy funkciók hozzáadott, eltávolított, és az alkalmazások életciklusa alatt változott. [Tartós funkciók](durable-functions-overview.md) lehetővé teszi, hogy a láncolás működik együtt, hogy nem korábban lehetséges, és a láncolás van hatással, hogyan kezelheti versioning.

## <a name="how-to-handle-breaking-changes"></a>Jelentős változásokat kezelésének módját

Nincsenek jelentős változásokat ismernie néhány példa. Ez a cikk ismerteti, amelyek a leggyakrabban. A fő téma az összes mögött, hogy mindkét új és meglévő függvény álló üzenettípusok összehangolását funkció módosítását is hatással van.

### <a name="changing-activity-function-signatures"></a>Tevékenység függvény aláírások módosítása

Aláírás módosítását a neve, bemeneti vagy kimeneti egy függvény változása hivatkozik. Ha egy tevékenység függvény ilyen típusú változás történik, azt érvénytelenítheti az orchestrator függvény, amely tőle függ. Az orchestrator működnek, mint a változásnak megfelelően, frissítésekor érvénytelenítheti a meglévő üzenetsoroktól példányok.

Tegyük fel tegyük fel, a következő függvény van.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

A simplistic függvény argumentuma eredményeinek **PEL** , és továbbítja azokat a **sáv**. Tegyük fel, igazolnia kell a visszatérési értékének módosítása **PEL** a `bool` való `int` eredmény értékek többféle támogatásához. Az eredmény így néz ki:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Ez a változás az orchestrator függvény minden új példányának remekül működik, de üzenetsoroktól példányok megszakítja. Vegye figyelembe például az esetben, ha meghívja a vezénylési példány **PEL**, megkapja egy logikai értéket, majd az ellenőrzőpontokat. Ha a aláírás módosítás van telepítve ezen a ponton, a alkulcsaihoz példány sikertelen lesz, azonnal folytatja amikor azt és hívása replays `context.CallActivityAsync<int>("Foo")`. Ez azért, mert a tábla eredménye `bool` , de az új kódot próbál deszerializálhatja azt a `int`.

Ez az csak az egyik számos különböző módja, hogy a aláírás módosítását meglévő példányok meghibásodásához vezethet. Általában egy orchestrator módosíthatja, miként kell meghívja a következő függvényt, majd a módosítás várhatóan jelent problémát.

### <a name="changing-orchestrator-logic"></a>Az orchestrator logika módosítása

Versioning problémák más osztály határozza meg, hogy a visszajátszás logika üzenetsoroktól példányok confuses orchestrator függvény kódjának a módosítása.

Vegye figyelembe a következő orchestrator-funkció:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Most tegyük fel szeretné hozzáadni egy másik függvény hívásához szükséges látszólag tűnve módosítja.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

Ez a változás hozzáadja az új függvény hívása **SendNotification** közötti **PEL** és **sáv**. Nincsenek aláírás módosítások. A probléma merül fel, ha hívása után folytatja működését egy meglévő példányát **sáv**. Ismétlési, ha az eredeti hívása során **PEL** visszaadott `true`, majd az orchestrator ismétlési fel fogja hívni a **SendNotification** amely nem szerepel a futtatási előzményei. Ennek eredményeképpen a tartós feladat keretrendszer meghiúsul, és egy `NonDeterministicOrchestrationException` hívásakor észlelt, ezért **SendNotification** mikor várható hívása megjelenítéséhez **sáv**.

## <a name="mitigation-strategies"></a>Megoldás stratégiák

Íme néhány a versioning kihívást kezelésével kapcsolatos olyan stratégiák:

* Nem történik semmi.
* Az üzenetsoroktól példányainak leállítása
* Egymás melletti központi telepítések

### <a name="do-nothing"></a>Nem történik semmi.

A legegyszerűbb kezelni használhatatlanná tévő változást ahhoz, hogy az üzenetsoroktól vezénylési példányok sikertelen. Új példányok sikeresen futtatni a módosított kódot.

E probléma attól függ, hogy a üzenetsoroktól példányok fontosságát. Ha az aktív fejlesztés és üzenetsoroktól példányok nem érdeklik, ez lehet elegendő helyes. Szüksége lesz azonban kivételeket és a diagnosztika feldolgozási hibák. Ha el szeretné kerülni ezeket a beállításokat, vegye figyelembe a más versioning lehetőségeket.

### <a name="stop-all-in-flight-instances"></a>Az üzenetsoroktól példányainak leállítása

Egy másik lehetőség egy összes üzenetsoroktól példány leállítása. Ez a belső tartalma törlésével végezhető **vezérlő-várólista** és **munkaelem-várólista** várólisták. A példányok fog végtelen elakadt a hol vannak, de azok nem lesznek megzavarhatják a telemetriai adatok hibaüzenetek. Ez a gyors Prototípus fejlesztési ideális.

> [!WARNING]
> Ezek a várólisták részleteit adott idő alatt, így ez a módszer a termelési számítási feladatokhoz ne hagyatkozzon módosíthatja.

### <a name="side-by-side-deployments"></a>Egymás melletti központi telepítések

A legtöbb sikertelen távra győződjön meg arról, hogy biztonságosan vannak-e telepítve a jelentős változásokat módja-mellé a korábbi verzióival való központi telepítésével. Ehhez használja a következő eljárások valamelyikét:

* Teljesen új funkciók, a frissítések központi telepítéséhez (új neve).
* A frissítések telepítése egy új funkció alkalmazást egy másik tárfiókot.
* Központi telepítése egy új példányt a függvény alkalmazás, de egy frissített `TaskHub` nevét. Ez az ajánlott eljárás.

### <a name="how-to-change-task-hub-name"></a>Feladat központnevet módosítása

A feladat központ konfigurálható a *host.json* fájlt az alábbiak szerint:

```json
{
    "durableTask": {
        "HubName": "MyTaskHubV2"
    }
}
```

Az alapértelmezett érték `DurableFunctionsHub`.

Minden Azure Storage entitás neve alapján a `HubName` konfigurációs érték. Adjon meg egy új nevet a feladat hub, akkor győződjön meg arról, hogy az alkalmazás új verziójának külön várólisták és előzménytábla jöttek létre.

Javasoljuk, hogy a függvény alkalmazás új verziójának telepít egy új [üzembe helyezési pont](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/). Üzembe helyezési engedélyezi, hogy a többszörös lemásolását, a függvény alkalmazás – párhuzamos Futtatás csak az egyiket a aktív *éles* tárolóhely. Teszi közzé az új vezénylési logikai a meglévő infrastruktúra készen áll, ha azok más dolga, mint az új verzió csere azokat az éles webalkalmazásra.

> [!NOTE]
> Ezt a stratégiát működik optimálisan, ha a HTTP és a webhook eseményindítók használata az orchestrator funkciók. Nem HTTP-eseményindítók, például várólisták vagy az Event Hubs az eseményindító definícióját, hogy a Csere műveletet részeként frissül Alkalmazásbeállítás a kell származtatni.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Megtudhatja, hogyan kezeli a teljesítmény, a méretezés problémák](durable-functions-perf-and-scale.md)
