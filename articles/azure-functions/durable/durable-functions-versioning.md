---
title: Verziószámozás a Durable Functionsban – Azure
description: Megtudhatja, hogyan implementálhatja a verziószámozást a Azure Functions Durable Functions-bővítményében.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: azfuncdf
ms.openlocfilehash: ef64a43cbed7f033a938351506b7f78142ff044c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097622"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Verziószámozás Durable Functions (Azure Functions)

Elkerülhetetlen, hogy a rendszer felveszi, eltávolítja és megváltoztatja a függvényeket az alkalmazások élettartama során. [Durable functions](durable-functions-overview.md) lehetővé teszi a láncolási függvények összekapcsolását olyan módokon, amelyek korábban nem voltak lehetségesek, és ez a láncolás befolyásolja a verziószámozás kezelését.

## <a name="how-to-handle-breaking-changes"></a>A feltörési változások kezelése

Több példa is van arra, hogy miként lehet a módosításokat feltörni. Ez a cikk a leggyakoribb kérdéseket tárgyalja. Az összes közülük az egyik fő téma az, hogy az új és a meglévő függvények is hatással vannak a funkció kódjának változásaira.

### <a name="changing-activity-function-signatures"></a>Tevékenység-függvények aláírásának módosítása

Az aláírás módosítása a függvények nevének, bemenetének vagy kimenetének változására utal. Ha ez a fajta változás egy tevékenységi függvényre vonatkozik, akkor az a Orchestrator függvényt is megszakítja, amely attól függ. Ha úgy frissíti a Orchestrator függvényt, hogy az megfeleljen ennek a változásnak, megszakíthatja a meglévő, repülés közbeni példányokat.

Tegyük fel például, hogy a következő függvényt használjuk.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Ez a leegyszerűsítő függvény a **foo** eredményeit veszi át, és a **sáv**felé továbbítja azt. Tegyük fel, hogy módosítani kell a **foo** értékének visszatérési `bool` értékét `int` a-ből a-re, hogy támogassa az eredmények szélesebb választékát. Az eredmény így néz ki:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Ez a változás jól működik a Orchestrator függvény összes új példányán, de a repülés közbeni példányokat is megszakítja. Vegyük például azt az esetet, amikor egy összehangoló példány egy **foo**-t hív meg, és egy logikai értéket, majd ellenőrzőpontokat kap vissza. Ha az aláírás módosítása ezen a ponton történik, az ellenőrzőponttal rendelkező példány azonnal meghiúsul, amikor folytatja, és visszajátssza a hívást `context.CallActivityAsync<int>("Foo")`. Ennek az az oka, hogy az előzmények tábla eredménye `bool` , de az új kód megpróbálja deszerializálni a- `int`ba.

Ez csak az egyik módja annak, hogy az aláírás módosítása megszakítja a meglévő példányokat. Általánosságban elmondható, hogy ha egy Orchestrator módosítania kell egy függvény meghívásának módját, akkor a változás valószínűleg problematikus lesz.

### <a name="changing-orchestrator-logic"></a>Orchestrator-logika módosítása

A verziószámozási problémák másik osztálya úgy változik, hogy a Orchestrator függvény kódját úgy változtatja meg, hogy megzavarja a fedélzeti példányok újrajátszása logikáját.

Vegye figyelembe a következő Orchestrator függvényt:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Most tegyük fel, hogy látszólag ártatlan változást kíván tenni egy másik függvény hívásának hozzáadásához.

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

Ez a változás egy új függvény hívását adja hozzá a **SendNotification** a **foo** és a **Bar**között. Nincsenek aláírási változások. A probléma akkor fordul elő, amikor egy meglévő példány folytatja a hívást a **sávra**. Ha a visszajátszáskor a rendszer az eredeti foo `true`-hívást adta vissza, akkor a Orchestrator visszajátszás olyan **SendNotification** fog hívni, amely nem szerepel a végrehajtás előzményeiben. Ennek eredményeképpen a tartós feladathoz tartozó keretrendszer meghiúsul `NonDeterministicOrchestrationException` , mert a **SendNotification** meghívását észlelte a rendszer, amikor a vártnál a **sáv**hívása látható.

## <a name="mitigation-strategies"></a>Kockázatcsökkentő stratégiák

Íme néhány, a verziószámozási kihívásokkal foglalkozó stratégia:

* Nincs teendő
* Az összes fedélzeti példány leállítása
* Párhuzamos üzembe helyezések

### <a name="do-nothing"></a>Nincs teendő

A feltörési változások kezelésének legegyszerűbb módja a fedélzeti előkészítési példányok meghibásodása. Az új példányok sikeresen futtatták a módosított kódot.

Ez a probléma attól függ, hogy a fedélzeti példányok mennyire fontosak. Ha aktív fejlesztést végez, és nem érdeklik a fedélzeti példányok, ez elég jó lehet. Azonban a diagnosztika folyamatában a kivételekkel és a hibákkal kell foglalkoznia. Ha el szeretné kerülni ezeket a dolgokat, tekintse át a többi verziószámozási lehetőséget.

### <a name="stop-all-in-flight-instances"></a>Az összes fedélzeti példány leállítása

Egy másik lehetőség az összes fedélzeti példány leállítása. Ezt úgy teheti meg, hogy törli a belső **vezérlő – üzenetsor** és a **Munkatétel-várólista** várólistájának tartalmát. A példányok örökre megakadnak, ahol vannak, de nem fogják a telemetria a hibaüzenetekkel együtt. Ez ideális megoldás a gyors prototípus-fejlesztéshez.

> [!WARNING]
> A várólisták részletei idővel változhatnak, ezért ne használja ezt a technikát éles számítási feladatokhoz.

### <a name="side-by-side-deployments"></a>Párhuzamos üzembe helyezések

A legtöbb sikertelen működést biztosító módszer, amellyel biztosítható, hogy a megszakított változások biztonságosan üzembe helyezhetők legyenek, ha a régebbi verziókkal párhuzamosan telepíti őket. Ezt a következő módszerek bármelyikével teheti meg:

* Telepítse az összes frissítést teljes mértékben új függvényként (új nevek).
* Telepítse az összes frissítést új Function-alkalmazásként egy másik Storage-fiókkal.
* Telepítse a Function alkalmazás egy új példányát, de egy frissített `TaskHub` névvel. Ez az ajánlott eljárás.

### <a name="how-to-change-task-hub-name"></a>A feladat központ nevének módosítása

A feladat hub a következő módon konfigurálható a *Host. JSON* fájlban:

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

Az összes Azure Storage-entitás neve a `HubName` konfigurációs érték alapján történik. Ha egy új nevet ad a Task hub számára, akkor az alkalmazás új verziójához külön üzenetsor-és előzmény-táblázatot kell létrehoznia.

Javasoljuk, hogy a Function alkalmazás új verzióját egy új [üzembe helyezési](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/)pontra telepítse. Az üzembe helyezési pontok lehetővé teszik, hogy a Function alkalmazás több példányát is futtassa, csak az egyiket aktív *üzemi* tárolóhelyként. Ha készen áll arra, hogy elérhetővé tegye az új előkészítési logikát a meglévő infrastruktúrájában, akkor olyan egyszerű lehet, hogy az új verziót az éles tárolóhelyre cseréli.

> [!NOTE]
> Ez a stratégia akkor működik a legjobban, ha HTTP-és webhook-eseményindítókat használ a Orchestrator functions szolgáltatáshoz. A nem HTTP-alapú eseményindítók, például a várólisták vagy a Event Hubs esetében az eseményindító definíciójának [olyan alkalmazás-beállításból](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings) kell származnia, amely a swap művelet részeként frissül.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan kezelheti a teljesítménnyel és a skálázással kapcsolatos problémákat](durable-functions-perf-and-scale.md)
