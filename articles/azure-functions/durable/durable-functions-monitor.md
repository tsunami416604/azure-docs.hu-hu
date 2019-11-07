---
title: Figyelők a Durable Functionsban – Azure
description: Ismerje meg, hogyan implementálhat egy állapotfigyelőt a Azure Functions Durable Functions-bővítményének használatával.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 5cb4602ac0431e09208953122f13b30124ab77f5
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614755"
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Figyelő forgatókönyv Durable Functions-Weather Watcher minta

A figyelő minta egy munkafolyamatban egy rugalmas *ismétlődő* folyamatra utal – például az egyes feltételek teljesülése esetén történő lekérdezésre. Ez a cikk a figyelés megvalósítását [Durable functions](durable-functions-overview.md) használó mintát ismerteti.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Forgatókönyv áttekintése

Ez a minta figyeli a hely aktuális időjárási feltételeit, és SMS-ben figyelmezteti a felhasználót, amikor az égbolt törölve van. Egy normál időzítő által aktivált függvény használatával ellenőrizhető az időjárási idő, és riasztásokat küldhet. Az ezzel a módszerrel kapcsolatos probléma azonban az **élettartam kezelése**. Ha csak egy riasztást kell elküldeni, a figyelőnek le kell tiltania magát a törlési idő észlelése után. A figyelési minta az egyéb előnyök mellett saját végrehajtást is elvégezheti:

* A figyelők időközönként futnak, és nem ütemezhetnek: időzítő-trigger óránként *fut* ; a figyelő egy órát *vár* a műveletek között. A figyelő műveletei nem lesznek átfedésben, ha meg vannak adva, ami fontos lehet a hosszan futó feladatok esetében.
* A figyelők lehetnek dinamikus időközök: a várakozási idő bizonyos feltételek alapján változhat.
* A figyelők leállhatnak, ha valamilyen feltétel teljesül, vagy egy másik folyamat leállítja azt.
* A figyelők paramétereket hozhatnak. A minta azt mutatja be, hogyan alkalmazható az időjárás-figyelési folyamat a kért helyekre és telefonszámokra.
* A figyelők méretezhetők. Mivel minden figyelő egy előkészítési példány, több figyelő is létrehozható anélkül, hogy új funkciókat kellene létrehoznia, vagy további kódokat kellene megadnia.
* A monitorok könnyen integrálhatók a nagyobb munkafolyamatokban. Egy figyelő lehet egy összetettebb összehangoló függvény, vagy egy [alfolyamatok](durable-functions-sub-orchestrations.md)egyik szakasza is.

## <a name="configuring-twilio-integration"></a>Twilio-integráció konfigurálása

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="configuring-weather-underground-integration"></a>Az időjárási földalatti integráció konfigurálása

Ez a példa a Weather Underground API-t használja a hely aktuális időjárási feltételeinek vizsgálatához.

Az első dolog, amire szüksége van egy időjárási Underground-fiók. A [https://www.wunderground.com/signup](https://www.wunderground.com/signup)címen ingyenesen létrehozhat egyet. Ha már rendelkezik fiókkal, meg kell adnia egy API-kulcsot. Ehhez látogasson el [https://www.wunderground.com/weather/api](https://www.wunderground.com/weather/api/?MR=1), majd válassza a Key Settings (Alapbeállítások) lehetőséget. Az Stratus fejlesztői terve ingyenes, és elegendő a minta futtatásához.

Ha már rendelkezik API-kulccsal, adja hozzá a következő **alkalmazás-beállítást** a Function alkalmazáshoz.

| Alkalmazás-beállítás neve | Érték leírása |
| - | - |
| **WeatherUndergroundApiKey**  | Az időjárási Underground API-kulcsa. |

## <a name="the-functions"></a>A függvények

Ez a cikk a minta alkalmazás következő funkcióit ismerteti:

* `E3_Monitor`: egy Orchestrator-függvény, amely rendszeres időközönként meghívja a `E3_GetIsClear`. Ha a `E3_GetIsClear` igaz értéket ad vissza, meghívja a `E3_SendGoodWeatherAlert`.
* `E3_GetIsClear`: egy tevékenység-függvény, amely egy adott hely aktuális időjárási feltételeit ellenőrzi.
* `E3_SendGoodWeatherAlert`: egy tevékenység-függvény, amely SMS-üzenetet küld a Twilio-on keresztül.

A következő szakaszokban ismertetjük a C# parancsfájlok futtatásához és a javascripthez használt konfigurációt és kódokat. A Visual Studio-fejlesztés kódja a cikk végén látható.

## <a name="the-weather-monitoring-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Időjárás-figyelési előkészítés (Visual Studio Code és Azure Portal mintakód)

A **E3_Monitor** függvény a standard *function. JSON* fájlt használja a Orchestrator függvényekhez.

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_Monitor/function.json)]

Itt látható a függvényt megvalósító kód:

### <a name="c-script"></a>C#Parancsfájl

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Monitor/run.csx)]

### <a name="javascript-functions-20-only"></a>JavaScript (csak functions 2,0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/index.js)]

Ez a Orchestrator-függvény a következő műveleteket hajtja végre:

1. Lekérdezi a figyelni kívánt *helyet* tartalmazó **MonitorRequest** , valamint azt a *telefonszámot* , amelyre SMS-értesítést fog küldeni.
2. Meghatározza a figyelő lejárati idejét. A minta egy nehezen kódolt értéket használ a rövidség kedvéért.
3. Meghívja a **E3_GetIsClear** annak megállapítására, hogy van-e egyértelmű égbolt a kért helyen.
4. Ha az időjárás egyértelmű, a meghívja a **E3_SENDGOODWEATHERALERT** SMS-értesítés küldését a kért telefonszámra.
5. Tartós időzítőt hoz létre, amely a következő lekérdezési időszakban folytatja a koordinálást. A minta egy nehezen kódolt értéket használ a rövidség kedvéért.
6. A továbbra is fut, amíg a `CurrentUtcDateTime` (.NET) vagy `currentUtcDateTime` (JavaScript) át nem adja a figyelő lejárati idejét, vagy SMS-riasztást küld.

Több Orchestrator-példány is futhat egyszerre több **MonitorRequests**küldésével. Megadható a figyelni kívánt hely és a telefonszám, amely SMS-riasztást küld.

## <a name="strongly-typed-data-transfer-net-only"></a>Szigorúan beírt adatátvitel (csak .NET)

A Orchestrator több adatra van szükség, ezért [megosztott poco-objektumokat](../functions-reference-csharp.md#reusing-csx-code) használ a szigorúan beírt adatátvitelhez a C# és C# a parancsfájlban:  
[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/MonitorRequest.csx)]

[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/Location.csx)]

A JavaScript-minta szabványos JSON-objektumokat használ paraméterként.

## <a name="helper-activity-functions"></a>Segítő tevékenység functions

Más mintákhoz hasonlóan a segítő tevékenység funkciói a `activityTrigger` trigger kötést használó rendszeres függvények. A **E3_GetIsClear** függvény az időjárási földalatti API használatával beolvassa az aktuális időjárási feltételeket, és meghatározza, hogy az ég tiszta-e. A *function. JSON* a következőképpen van definiálva:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/function.json)]

Itt pedig a megvalósítás. Az adatátvitelhez használt POCOs-hez hasonlóan az API-hívás kezeléséhez és a válasz JSON értelmezéséhez tartozó logika is egy megosztott osztályba van beosztva C#. A [Visual Studio-mintakód](#run-the-sample)részeként is megtalálhatja.

### <a name="c-script"></a>C#Parancsfájl

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/run.csx)]

### <a name="javascript-functions-20-only"></a>JavaScript (csak functions 2,0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/index.js)]

A **E3_SendGoodWeatherAlert** függvény a Twilio kötés használatával küld SMS-üzenetet, amely értesíti a felhasználót arról, hogy jó idő van egy sétára. A *function. JSON* egyszerű:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/function.json)]

Itt látható az SMS-üzenetet küldő kód:

### <a name="c-script"></a>C#Parancsfájl

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/run.csx)]

### <a name="javascript-functions-20-only"></a>JavaScript (csak functions 2,0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/index.js)]

## <a name="run-the-sample"></a>Minta futtatása

A mintában szereplő HTTP-triggerű függvények használatával a következő HTTP POST-kérelem elküldésével elindíthatja a koordinálást:

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

A **E3_Monitor** példány elindul, és lekérdezi a kért hely aktuális időjárási feltételeit. Ha az időjárás törölve van, akkor egy tevékenység függvényt hív meg, amely riasztást küld; Ellenkező esetben egy időzítőt állít be. Ha az időzítő lejár, a rendszer folytatja a koordinálást.

A munkafolyamatok tevékenysége a Azure Functions portálon megjelenő függvények naplói szerint látható.

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

A rendszer az időtúllépés elérésekor vagy az égbolt észlelésének törlésével [leáll](durable-functions-instance-management.md) . Egy másik függvényben `TerminateAsync` (.NET) vagy `terminate` (JavaScript) is használhat, vagy meghívhatja a fenti 202-válaszban hivatkozott **terminatePostUri** http post webhookot, és lecseréli az `{text}`t a lemondási ok miatt:

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="visual-studio-sample-code"></a>Visual Studio-mintakód

Íme egy Visual Studio-projekt egyetlen C# fájlja:

> [!NOTE]
> Az alábbi mintakód futtatásához telepítenie kell a `Microsoft.Azure.WebJobs.Extensions.Twilio` Nuget csomagot.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs)]

## <a name="next-steps"></a>További lépések

Ez a példa azt mutatja be, hogyan használható a Durable Functions egy külső forrás állapotának figyelésére [tartós időzítők](durable-functions-timers.md) és feltételes logika használatával. A következő minta bemutatja, hogyan használhatók a külső események és a [tartós időzítők](durable-functions-timers.md) az emberi interakció kezelésére.

> [!div class="nextstepaction"]
> [Az emberi interakciós minta futtatása](durable-functions-phone-verification.md)
