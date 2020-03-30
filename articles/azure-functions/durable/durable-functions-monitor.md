---
title: Monitorok tartós függvényekben – Azure
description: Ismerje meg, hogyan valósíthat meg állapotfigyelőt az Azure Functions Durable Functions bővítmény használatával.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: ed92156df9d8e1e07b56cea4b1e64edee11d68d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562122"
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Monitor forgatókönyv tartós funkciók - Időjárás figyelő minta

A monitorminta egy rugalmas *ismétlődő* folyamatra utal egy munkafolyamatban – például a lekérdezés, amíg bizonyos feltételek nem teljesülnek. Ez a cikk egy mintát, amely [tartós függvények](durable-functions-overview.md) segítségével monitoring megvalósításához.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Forgatókönyv áttekintése

Ez a minta figyeli a hely aktuális időjárási viszonyait, és sms-ben figyelmezteti a felhasználót, ha az ég tiszta. Használhatja a rendszeres időzítő által aktivált funkció, hogy ellenőrizze az időjárás, és küldjön riasztásokat. Ezzel a megközelítéssel azonban az egyik probléma az **élethosszig tartó kezelés**. Ha csak egy riasztást kell küldeni, a figyelőnek le kell tiltania magát a tiszta időjárás észlelése után. A figyelési minta véget vethet a saját végrehajtását, többek között:

* A figyelők időközönként futnak, nem ütemezések szerint: az időzítő eseményindítója óránként *fut;* a monitor egy órát *vár a* műveletek között. A figyelő lépései csak akkor fedik át egymást, ha nincs megadva, ami fontos lehet a hosszú ideig futó feladatok esetében.
* A monitorok dinamikus időközöket is használhatnak: a várakozási idő bizonyos feltételek alapján változhat.
* A figyelők akkor fejeződhetnek be, ha bizonyos feltételek teljesülnek, vagy egy másik folyamat leállítja őket.
* A monitorok paramétereket vehetnek igénybe. A minta azt mutatja be, hogy ugyanaz az időjárás-figyelési folyamat hogyan alkalmazható bármely kért helyre és telefonszámra.
* A monitorok méretezhetők. Mivel minden figyelő vezénylési példány, több figyelő kérhető új függvények létrehozása vagy további kód definiálása nélkül.
* A monitorok könnyen integrálhatók a nagyobb munkafolyamatokba. A figyelő lehet egy összetettebb vezénylési függvény egy szakasza, vagy egy [alvezénylés.](durable-functions-sub-orchestrations.md)

## <a name="configuration"></a>Konfiguráció

### <a name="configuring-twilio-integration"></a>Twilio-integráció konfigurálása

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

### <a name="configuring-weather-underground-integration"></a>Időjárás underground integráció konfigurálása

Ez a minta magában foglalja a Weather Underground API-t egy adott hely aktuális időjárási viszonyának ellenőrzéséhez.

Az első dolog, amire szüksége van egy Weather Underground számla. Létrehozhat egyet ingyen a. [https://www.wunderground.com/signup](https://www.wunderground.com/signup) Miután rendelkezik egy fiókkal, be kell szereznie egy API-kulcsot. Ezt a felkereséssel [https://www.wunderground.com/weather/api](https://www.wunderground.com/weather/api/?MR=1)teheti meg, majd válassza a Kulcsbeállítások lehetőséget. A Stratus fejlesztői terv ingyenes és elegendő a minta futtatásához.

Miután rendelkezik egy API-kulcs, adja hozzá a következő **alkalmazásbeállítást** a függvényalkalmazáshoz.

| Alkalmazásbeállítás neve | Érték leírása |
| - | - |
| **IdőjárásUndergroundApiKey**  | A Weather Underground API kulcs. |

## <a name="the-functions"></a>A funkciók

Ez a cikk a mintaalkalmazás következő funkcióit ismerteti:

* `E3_Monitor`: Rendszeresen hív `E3_GetIsClear` [vezénylő függvény.](durable-functions-bindings.md#orchestration-trigger) Akkor `E3_SendGoodWeatherAlert` hív, ha `E3_GetIsClear` igaz.
* `E3_GetIsClear`: Olyan [tevékenységfüggvény,](durable-functions-bindings.md#activity-trigger) amely egy adott hely aktuális időjárási viszonyait ellenőrzi.
* `E3_SendGoodWeatherAlert`: Egy tevékenységfunkció, amely SMS-üzenetet küld a Twilio-n keresztül.

### <a name="e3_monitor-orchestrator-function"></a>E3_Monitor orchestrator függvény

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=41-78,97-115)]

Az orchestrator megköveteli a figyelési helyet és egy telefonszámot, amelynek üzenetet kell küldenie, amikor a hely egyértelművé válik. Ezeket az adatokat erősen beírt objektumként továbbítja `MonitorRequest` az orchestrator.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A **E3_Monitor** függvény a standard *function.json* függvényt használja az orchestrator függvényekhez.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/function.json)]

Itt van a kód amit eszköz a függvény:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/index.js)]

---

Ez az orchestrator-függvény a következő műveleteket hajtja végre:

1. Beszerzi a **monitorkérelem,** amely a figyelni való *hely* és a *telefonszám,* amelyre küld egy SMS értesítést.
2. A figyelő lejárati idejét határozza meg. A minta a rövidség hez egy kódolt értéket használ.
3. A hívások **E3_GetIsClear** annak megállapítására, hogy van-e tiszta égbolt a kért helyen.
4. Ha az időjárás tiszta, a hívások **E3_SendGoodWeatherAlert,** hogy sms-értesítést küldjenek a kért telefonszámra.
5. Tartós időzítőt hoz létre a vezénylés folytatásához a következő lekérdezési időközzel. A minta a rövidség hez egy kódolt értéket használ.
6. Addig fut, amíg az aktuális UTC-idő el nem múlik a figyelő lejárati idejét, vagy sms-riasztást nem küld.

Több orchestrator-példány oka egyszerre is futtatható az orchestrator függvény többszöri hívásával. Megadhatja a figyelniendő helyet és az SMS-értesítést küldő telefonszámot.

### <a name="e3_getisclear-activity-function"></a>E3_GetIsClear tevékenység függvénye

Más mintákhoz is a segítő tevékenységfüggvények rendszeres `activityTrigger` függvények, amelyek az eseményindító kötést használják. A **E3_GetIsClear** függvény lekérdezi az aktuális időjárási körülményeket a Weather Underground API használatával, és meghatározza, hogy az ég tiszta-e.

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=80-85)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

A *function.json* a következőképpen határozható meg:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/function.json)]

És itt van a végrehajtás.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/index.js)]

---

### <a name="e3_sendgoodweatheralert-activity-function"></a>E3_SendGoodWeatherAlert tevékenység függvénye

A **E3_SendGoodWeatherAlert** függvény a Twilio-kötés thasználja egy SMS-üzenet küldésére, amely értesíti a végfelhasználót, hogy ez egy jó ideje egy séta.

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=87-96,140-205)]

> [!NOTE]
> A mintakód `Microsoft.Azure.WebJobs.Extensions.Twilio` futtatásához telepítenie kell a Nuget csomagot.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A *function.json* egyszerű:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/function.json)]

És itt van a kód amit küld a SMS üzenet:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/index.js)]

---

## <a name="run-the-sample"></a>Minta futtatása

A mintában szereplő HTTP-aktivált függvények használatával a vezénylést a következő HTTP POST-kérelem elküldésével indíthatja el:

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "location": { "city": "Redmond", "state": "WA" }, "phone": "+1425XXXXXXX" }
```

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

A **E3_Monitor** példány elindul, és lekérdezi a kért hely aktuális időjárási feltételeit. Ha az időjárás tiszta, akkor egy tevékenységfüggvényt hív meg riasztás küldésére; ellenkező esetben időzítőt állít be. Amikor az időzítő lejár, a vezénylésfolytatódik.

Megtekintheti a vezénylési tevékenység az Azure Functions portálon a függvénynaplók megtekintésével.

```
2018-03-01T01:14:41.649 Function started (Id=2d5fcadf-275b-4226-a174-f9f943c90cd1)
2018-03-01T01:14:42.741 Started orchestration with ID = '1608200bb2ce4b7face5fc3b8e674f2e'.
2018-03-01T01:14:42.780 Function completed (Success, Id=2d5fcadf-275b-4226-a174-f9f943c90cd1, Duration=1111ms)
2018-03-01T01:14:52.765 Function started (Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb)
2018-03-01T01:14:52.890 Received monitor request. Location: Redmond, WA. Phone: +1425XXXXXXX.
2018-03-01T01:14:52.895 Instantiating monitor for Redmond, WA. Expires: 3/1/2018 7:14:52 AM.
2018-03-01T01:14:52.909 Checking current weather conditions for Redmond, WA at 3/1/2018 1:14:52 AM.
2018-03-01T01:14:52.954 Function completed (Success, Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb, Duration=189ms)
2018-03-01T01:14:53.226 Function started (Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859)
2018-03-01T01:14:53.808 Function completed (Success, Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859, Duration=582ms)
2018-03-01T01:14:53.967 Function started (Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c)
2018-03-01T01:14:53.996 Next check for Redmond, WA at 3/1/2018 1:44:53 AM.
2018-03-01T01:14:54.030 Function completed (Success, Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c, Duration=62ms)
```

A vezénylési [leáll,](durable-functions-instance-management.md) ha az időtúllépés elérésekor, vagy tiszta égbolt észlelése. Használhatja `TerminateAsync` a (.NET) `terminate` vagy a (JavaScript) függvényt egy másik függvényben, vagy meghívhatja a `{text}` **terminatePostUri** HTTP POST webhookot, amely a fenti 202 válaszban szerepel, és a megszüntetés okát helyettesíti:

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="next-steps"></a>További lépések

Ez a minta bemutatja, hogyan használható a durable functions egy külső forrás állapotának figyelésére [tartós időzítők](durable-functions-timers.md) és feltételes logika használatával. A következő minta bemutatja, hogyan használhatja a külső események et és [a tartós időzítőket](durable-functions-timers.md) az emberi interakció kezeléséhez.

> [!div class="nextstepaction"]
> [Az emberi interakcióminta futtatása](durable-functions-phone-verification.md)
