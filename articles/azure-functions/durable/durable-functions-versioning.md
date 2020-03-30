---
title: Verziószámozás tartós függvényekben – Azure
description: Ismerje meg, hogyan valósíthatja meg a verziószámozást az Azure Functions Durable Functions bővítményében.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 87cbb94dbab241630dc7585bdf4314d858d5b4da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232752"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Verziószámozás tartós függvényekben (Azure-függvények)

Elkerülhetetlen, hogy a függvények az alkalmazás élettartama alatt hozzáadódnak, törlődjenek és megváltozzon. [A Durable Functions](durable-functions-overview.md) lehetővé teszi a funkciók összeláncolását olyan módon, amely korábban nem volt lehetséges, és ez a láncolás befolyásolja a verziószámozás kezelését.

## <a name="how-to-handle-breaking-changes"></a>A törési módosítások kezelése

Számos példa van arra, hogy a változásokat meg kell szakítani. Ez a cikk ismerteti a leggyakoribbak. A fő téma mögött az összes, hogy mind az új, mind a meglévő függvény vezénylések hatással vannak a függvénykód változásai.

### <a name="changing-activity-or-entity-function-signatures"></a>Tevékenység- vagy entitásfüggvény-aláírások módosítása

Az aláírásmódosítása egy függvény nevének, bemenetének vagy kimenetének változására utal. Ha ilyen típusú módosítás történik egy tevékenység vagy entitás függvény, ez megszakíthatja az orchestrator függvény, amely attól függ. Ha frissíti az orchestrator függvényt a módosítás nak megfelelően, megszakíthatja a meglévő fedélzeti példányokat.

Például tegyük fel, hogy a következő orchestrator függvény.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Ez a leegyszerűsítő funkció veszi az eredményeket a **Foo** és átadja azt **Bar**. Tegyük fel, hogy meg kell változtatnunk `bool` a `int` **Foo** visszatérési értékét az eredményértékek szélesebb körének támogatása érdekében. Az eredmény így néz ki:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> Az előző C# példák a Durable Functions 2.x-et célozzák meg. A tartós függvények 1.x `DurableOrchestrationContext` esetén `IDurableOrchestrationContext`a helyett a ot kell használnia. A verziók közötti különbségekről a [Durable Functions verziók ról](durable-functions-versions.md) szóló cikkben olvashat bővebben.

Ez a módosítás jól működik az orchestrator függvény minden új példányához, de megszakítja a repülés közbeni példányokat. Vegyük például azt az esetet, amikor `Foo`egy vezénylési példány meghívja a függvény nevű függvényt, logikai értéket kap vissza, majd ellenőrzőpontokat. Ha az aláírás módosítása ezen a ponton van telepítve, az ellenőrzőponton lévő példány azonnal `context.CallActivityAsync<int>("Foo")`sikertelen lesz, amikor folytatja, és visszajátssza a hívást. Ez a hiba azért fordul elő, mert az előzménytábla eredménye, de az `bool` új kód megpróbálja deszerializálni a rendszerbe. `int`

Ez a példa csak egy a sok különböző módon, hogy egy aláírás változás a meglévő példányok megszakítása. Általában, ha egy orchestrator kell változtatni, ahogy meghívja a függvényt, akkor a változás valószínűleg problémás.

### <a name="changing-orchestrator-logic"></a>Az orchestrator logikájának módosítása

A verziószámozási problémák másik osztálya az orchestrator függvénykódjának oly módon történő módosításából származik, amely összezavarja a repülés közbeni példányok visszajátszási logikáját.

Vegye figyelembe a következő orchestrator függvényt:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Most tegyük fel, hogy egy látszólag ártatlan változtatást szeretne tenni egy másik függvényhívás hozzáadásához.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> Az előző C# példák a Durable Functions 2.x-et célozzák meg. A tartós függvények 1.x `DurableOrchestrationContext` esetén `IDurableOrchestrationContext`a helyett a ot kell használnia. A verziók közötti különbségekről a [Durable Functions verziók ról](durable-functions-versions.md) szóló cikkben olvashat bővebben.

Ez a módosítás új függvényhívást ad hozzá a **SendNotification** hez **a Foo** és **a Bar**között. Nincs aláírás-módosítás. A probléma akkor merül fel, amikor egy **Bar**meglévő példány folytatódik a bar hívásról. A visszajátszás során, ha az eredeti `true`hívás **Foo** vissza , majd az orchestrator visszajátszás a **SendNotification**, amely nem szerepel a végrehajtási előzményeket. Ennek eredményeképpen a tartós feladatkeretrendszer sikertelen, `NonDeterministicOrchestrationException` mert a **SendNotification** hívása közben hívást észlelt a Bar **hívásra.** Ugyanez a probléma akkor fordulhat elő, ha bármilyen hívást `CreateTimer`hozzáad a "tartós" API-khoz, beleértve a , `WaitForExternalEvent`stb.

## <a name="mitigation-strategies"></a>Kockázatcsökkentési stratégiák

Íme néhány stratégia a verzióváltási kihívások kezelésére:

* Ne tegyen semmit
* Az összes repülés közbeni példány leállítása
* Egymás melletti telepítések

### <a name="do-nothing"></a>Ne tegyen semmit

A törési módosítás kezelésének legegyszerűbb módja, ha hagyja, hogy a fedélzeti vezénylési példányok meghibásodjanak. Az új példányok sikeresen futtatták a módosított kódot.

Az, hogy ez a fajta hiba probléma-e, a repülés közbeni példányok fontosságától függ. Ha ön -ban aktivál fejlődés és dont' gond körülbelül -ban- repülés példa, ez erő lenni jó elég. Azonban a diagnosztikai folyamat kivételekkel és hibákkal kell foglalkoznia. Ha el szeretné kerülni ezeket a dolgokat, fontolja meg a többi verziószámozási lehetőséget.

### <a name="stop-all-in-flight-instances"></a>Az összes repülés közbeni példány leállítása

Egy másik lehetőség az összes repülés közbeni példány leállítása. Az összes példány leállítása a belső **vezérlő-várólista** és a **munkatétel-várólista-várólisták** tartalmának törlésével végezhető el. Az esetek örökre megragadt, ahol vannak, de nem rendetlenséget a naplók at hiba üzeneteket. Ez a megközelítés ideális a gyors prototípus fejlesztés.

> [!WARNING]
> Ezek a várólisták részletei idővel változhatnak, ezért ne támaszkodjon erre a technikára az éles számítási feladatokhoz.

### <a name="side-by-side-deployments"></a>Egymás melletti telepítések

A hibabiztos módja annak, hogy a biztonsági módosítások biztonságosüzembe helyezése a régebbi verziókkal együtt történő üzembe helyezéssel történjen. Ezt az alábbi technikák bármelyikével teheti meg:

* Az összes frissítést teljesen új függvényként telepíti, így a meglévő függvények a jelenlegi állapotban maradnak. Ez bonyolult lehet, mert az új függvényverziók hívóit is frissíteni kell, ugyanazokat az irányelveket követve.
* Telepítse az összes frissítést egy új függvényalkalmazásként egy másik tárfiókkal.
* Telepítse a függvényalkalmazás új példányát ugyanazzal a `taskHub` tárfiókkal, de frissített névvel. Egymás melletti központi telepítések az ajánlott technika.

### <a name="how-to-change-task-hub-name"></a>A feladatközpont nevének módosítása

A feladatközpont a *host.json* fájlban a következőképpen konfigurálható:

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "durableTask": {
        "hubName": "MyTaskHubV2"
    }
}
```

#### <a name="functions-20"></a>Funkciók 2.0

```json
{
    "extensions": {
        "durableTask": {
            "hubName": "MyTaskHubV2"
        }
    }
}
```

A Durable Functions v1.x `DurableFunctionsHub`alapértelmezett értéke a . A Durable Functions v2.0-tól kezdve az alapértelmezett feladatközpont neve megegyezik a függvényalkalmazás nevével az Azure-ban, vagy `TestHubName` ha az Azure-on kívül fut.

Az összes Azure Storage-entitás `hubName` neve a konfigurációs érték alapján. Ha új nevet ad a feladatközpontnak, biztosítja, hogy az alkalmazás új verziójához külön várólisták és előzménytábla jön létre. A függvényalkalmazás azonban leállítja az események feldolgozását vezénylések vagy az előző feladatközpont neve alatt létrehozott entitások számára.

Azt javasoljuk, hogy telepítse a függvényalkalmazás új verzióját egy új [központi telepítési helyre.](../functions-deployment-slots.md) A központi telepítési tárolóhelyek lehetővé teszik, hogy a függvényalkalmazás több példányát egymás mellett futtassa, és csak az egyiket az aktív *éles* tárolóhelyként futtassa. Ha készen áll az új vezénylési logika a meglévő infrastruktúra, lehet olyan egyszerű, mint az új verzió cseréje az éles tárolóhelyre.

> [!NOTE]
> Ez a stratégia akkor működik a legjobban, ha HTTP és webhook-eseményindítókat használ az orchestrator függvényekhez. A nem HTTP-eseményindítók, például a várólisták vagy az eseményközpontok esetében az eseményindító-definíciónak [egy olyan alkalmazásbeállításból kell származnia,](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings) amely a csereművelet részeként frissül.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a teljesítmény- és méretezési problémák kezeléséről](durable-functions-perf-and-scale.md)
