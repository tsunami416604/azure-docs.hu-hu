---
title: Durable Functions – Azure verziószámozást
description: Útmutató a Durable Functions bővítmény megvalósítására az Azure Functions szolgáltatáshoz.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: azfuncdf
ms.openlocfilehash: a90a6811b1c59538db98b85d950906dba2969d57
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53338243"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Durable Functions (az Azure Functions) a verziókezelés

Fontos, hogy funkciók fog adni, eltávolítva, és módosítani az alkalmazás teljes élettartama alatt elkerülhetetlen. [Durable Functions](durable-functions-overview.md) láncolási működik együtt, hogy korábban nem, és a láncolási van hatással, miként kezelheti a verziókezelés lehetővé teszi.

## <a name="how-to-handle-breaking-changes"></a>Kompatibilitástörő változások kezelése

Nincsenek kompatibilitástörő változásokat, érdemes figyelembe vennie néhány példa. Ez a cikk ismerteti a leggyakrabban. A fő téma mindegyiknek mögött, hogy mindkét új és meglévő függvény vezénylések negatív függvénykód módosítása hatással van.

### <a name="changing-activity-function-signatures"></a>Tevékenység függvény aláírások módosítása

Aláírás változást a nevét, bemeneti vagy kimeneti függvény változás hivatkozik. Ha egy tevékenység-függvényt az ilyen típusú változás történik, azt érvénytelenítheti az orchestrator-függvény, amely ettől függ. Ha frissíti az orchestrator függvény e változáshoz igazodva, érvénytelenítheti a meglévő szükségszerű példányok.

Például tegyük fel, hogy rendelkezünk a következő függvényt.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Egyszerűsített függvény eredményét veszi **Foo** , és átadja azokat a **sáv**. Tegyük fel, a visszaadott értékét módosítani kell **Foo** a `bool` való `int` eredmény érték választéka támogatásához. Az eredmény a következőhöz hasonló:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Ez a változás jól működik, ha az orchestrator függvény összes új példánya esetén, de szükségszerű példányok működésképtelenné válik. Vegyük példaként a helyzet, ha meghívja a vezénylési példány **Foo**, visszakap egy logikai értéket, majd az ellenőrzőpontokat. Ha a aláírás módosítás van telepítve ezen a ponton, a alkulcsaihoz példány sikertelen lesz, azonnal folytatja mikor, és visszajátssza a hívást `context.CallActivityAsync<int>("Foo")`. Ennek az az oka az eredmény az előzménytábla `bool` , de az új kódot próbál deszerializálhatja azt be `int`.

Ez az, hogy egy aláírás módosítása tönkretehetik a meglévő példányok számos különböző módon csak egyike. Általában egy orchestrator kell megváltoztatni meghívja a függvényt, akkor a módosítás várhatóan problémás lehet.

### <a name="changing-orchestrator-logic"></a>Az orchestrator logikai módosítása

A másik osztály versioning problémák módosítaná az orchestrator függvényt úgy, hogy az átvitel alatt példányok visszajátszását logikáját confuses származnak.

Vegye figyelembe a következő orchestrator függvényt:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Most tegyük fel, adjon hozzá egy másik függvény hívásához szükséges látszólag álcázva módosítást szeretne.

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

Ez a módosítás hozzáadja az új függvény hívása **SendNotification** között **Foo** és **sáv**. Nem változtak aláírás. A probléma merül fel, ha a meglévő példány folytatja a hívásából származó **sáv**. Ismétlési, ha az eredeti hívás során **Foo** visszaadott `true`, majd az orchestrator visszajátszását fog hívni **SendNotification** amely nem szerepel a futtatási előzményei. Ennek eredményeképpen a tartós feladat keretrendszer meghiúsul, és egy `NonDeterministicOrchestrationException` , ezért hívása **SendNotification** mikor várható hívása megtekintéséhez **sáv**.

## <a name="mitigation-strategies"></a>Kockázatcsökkentési stratégia

Íme néhány a verziókezelés kihívások többé vesződnie a sérült stratégiák:

* Nem tesz semmit
* Összes szükségszerű példány leállítása
* Egymás melletti központi telepítések

### <a name="do-nothing"></a>Nem tesz semmit

A legegyszerűbben úgy kezeli a használhatatlanná tévő változást ahhoz, hogy az átvitel alatt vezénylési példányok sikertelen. Új példány sikeresen futtatni a módosított kódot.

E probléma attól függ, hogy az átvitel alatt példányok fontosságát. Ha aktív fejlesztés alatt állnak, és az átvitel alatt a példányok nem érdeklik, ez is használatos. Azonban szüksége lesz a diagnosztikai folyamatot hibák és kivételek kezelése. Ha meg szeretné kerülni ezeket a dolgokat, fontolja meg a többi versioning beállítást.

### <a name="stop-all-in-flight-instances"></a>Összes szükségszerű példány leállítása

Egy másik lehetőség, ha leállítja az összes szükségszerű példányt. Ezt megteheti a tartalmát a belső törlésével **várólista-ellenőrzés** és **munkaelem-várólista** üzenetsorok. A példányok lesznek örökre elakadt a hol vannak, de nem fogja telezsúfolni a telemetriai adatok hibaüzenetek. Ez ideális gyors prototípus-fejlesztés alatt.

> [!WARNING]
> Ezek a várólisták részleteit idővel, ezért ezt a technikát éles számítási feladatok esetében ne használjon változhat.

### <a name="side-by-side-deployments"></a>Egymás melletti központi telepítések

A legtöbb sikertelen koncepció győződjön meg arról, hogy biztonságosan vannak-e telepítve a kompatibilitástörő változásokat módja mivel egymás mellett a régebbi verzióival. Ezt megteheti az alábbi módszerek bármelyikével:

* Teljesen új funkciók, a frissítések telepítése (új név).
* A frissítések üzembe egy új függvényalkalmazást egy másik tárfiókhoz.
* Üzembe helyezése egy új példányt a függvényalkalmazás, de a frissített `TaskHub` nevét. Ez az az ajánlott eljárás.

### <a name="how-to-change-task-hub-name"></a>Tevékenység a központnév módosítása

A feladat hub konfigurálható a *host.json* fájlt az alábbiak szerint:

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "durableTask": {
        "HubName": "MyTaskHubV2"
    }
}
```

#### <a name="functions-2x"></a>Functions 2.x

Az alapértelmezett érték `DurableFunctionsHub`.

Minden Azure Storage-entitás alapján vannak elnevezve a `HubName` konfigurációs érték. Azzal, hogy a feladat hub egy új nevet, akkor győződjön meg arról, hogy külön üzenetsorokat és előzménytábla jönnek létre az alkalmazás új verziójának.

Azt javasoljuk, hogy egy új verzióját, a függvényalkalmazás üzembe [üzembe helyezési pont](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/). Üzembe helyezési pontok lehetővé teszik több másolatot a függvény alkalmazás – párhuzamos Futtatás csak az egyiket az aktív *éles* tárolóhely. Elérhetővé az új vezénylési logika, a meglévő infrastruktúra készen áll, amikor az új verzió érvényesítheti az üzemelési egyszerűen lehet.

> [!NOTE]
> Ez a stratégia akkor működik a legjobban, ha HTTP- és webhook eseményindítók használata az orchestrator-funkciók. A-HTTP eseményindítók, például üzenetsorokat vagy az Event Hubs az eseményindító definíciójában Alkalmazásbeállítás, amely frissül, és a felcserélési művelet részeként típusból kell származtatni.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan kezeli a teljesítmény és skálázhatja a problémák](durable-functions-perf-and-scale.md)
