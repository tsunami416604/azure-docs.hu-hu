---
title: Durable Functions – Azure figyelőket
description: Ismerje meg, hogy a Durable Functions bővítmény használata az Azure Functions állapota figyelő megvalósítása.
services: functions
author: kashimiz
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 07/11/2018
ms.author: azfuncdf
ms.openlocfilehash: 02c068fc70748584583b2c71659b1a1abdc0a46d
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035771"
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Durable Functions - időjárási megfigyelő minta forgatókönyv figyelése

A figyelő minta hivatkozik egy rugalmas *ismétlődő* folyamat például egy munkafolyamat - lekérdezés csak bizonyos feltételek teljesülnek-e. Ez a cikk azt ismerteti, használó minta [Durable Functions](durable-functions-overview.md) figyelés megvalósításához.

## <a name="prerequisites"></a>Előfeltételek

* [Durable Functions telepítése](durable-functions-install.md).
* Végezze el a [Hello feladatütemezési](durable-functions-sequence.md) forgatókönyv.

## <a name="scenario-overview"></a>Forgatókönyv áttekintése

Ez a minta egy hely aktuális időjárási feltételek figyeli, és riasztást küld a felhasználó SMS, ha a skies is törölje. Egy normál időzítő által aktivált függvényt használhatja az időjárás és riasztások küldése. Azonban ezt a módszert használja az egyik probléma van **életciklusának kezelését**. Ha csak egy riasztást kell küldeni, a figyelő le kell tiltania maga után törölje időjárási észlel. A figyelési minta fejezheti be a saját végrehajtási, többek között:

* Figyelők futtatásához időközönként, nem ütemezi: egy időzítő indítófeltételt *fut* óránként; egy a figyelő *vár* egy óra közötti műveleteket. Egy figyelő műveletek nem átfedésben lesznek, hacsak nincs megadva, amely lehet fontos a hosszan futó feladatokat.
* Figyelők dinamikus intervallumokat is rendelkeznek: a várakozási idő módosíthatók valamilyen feltétel alapján.
* Figyelők leállíthatja, ha bizonyos feltétel teljesül, vagy leállít egy másik folyamat.
* Figyelők paramétereket is igénybe vehet. A minta bemutatja, hogyan időjárás-figyelés eljárást minden kért hellyel és telefonszám alkalmazható.
* Figyelők is méretezhető. Mivel minden egyes figyelő egy vezénylési példányt, több monitor is létrehozható, hozzon létre új funkciók és további kód megadása nélkül.
* Figyelők könnyen integrálható a nagyobb munkafolyamatok. Egy figyelő lehet egy szakasz egy összetettebb vezénylési függvény vagy egy [alárendelt vezénylési](https://docs.microsoft.com/azure/azure-functions/durable-functions-sub-orchestrations).

## <a name="configuring-twilio-integration"></a>Twilio-integráció konfigurálása

[!INCLUDE [functions-twilio-integration](../../includes/functions-twilio-integration.md)]

## <a name="configuring-weather-underground-integration"></a>Időjárási Dark integrációjának konfigurálása

Ez a minta magában foglalja a szolgáltatás emellett az illegális időjárási API használatával történő ellenőrizze a hely aktuális időjárási feltételek.

Az első lépésként létre kell időjárási Dark fiók. Létrehozhat egy ingyenes [ https://www.wunderground.com/signup ](https://www.wunderground.com/signup). Ha már rendelkezik fiókkal, szüksége lesz egy API-kulcs beszerzése. Ehhez funkcionáló [ https://www.wunderground.com/weather/api ](https://www.wunderground.com/weather/api), majd kiválasztja a kulcs beállításait. A Stratus fejlesztői csomag az ingyenes és a minta futtatásához elegendő.

Miután egy API-kulcsot, adja hozzá a következő **Alkalmazásbeállítás** a függvényalkalmazáshoz.

| Alkalmazásbeállítás neve | Érték Leírás |
| - | - |
| **WeatherUndergroundApiKey**  | Az időjárási Dark API-kulcsot. |

## <a name="the-functions"></a>Az funkciók

Ez a cikk ismerteti a mintaalkalmazást a következő funkciókat:

* `E3_Monitor`: Egy orchestrator-függvény, amely meghívja a `E3_GetIsClear` rendszeres időközönként. Meghívja `E3_SendGoodWeatherAlert` Ha `E3_GetIsClear` igaz értéket ad vissza.
* `E3_GetIsClear`: Egy tevékenység-függvény, amely ellenőrzi az aktuális időjárási feltételek egy adott helyre vonatkozóan.
* `E3_SendGoodWeatherAlert`: Egy tevékenység-függvény, amely elküldi az SMS-t, Twilio-n keresztül.

Az alábbi szakaszok ismertetik a konfiguráció és a C# a parancsfájlkezelést, és a JavaScript által használt kódot. A Visual Studio fejlesztési kód a cikk végén található meg.
 
## <a name="the-weather-monitoring-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Az időjárás, orchestration (Visual Studio Code és az Azure portal mintakódot) figyelése

A **E3_Monitor** a funkció a standard *function.json* az orchestrator-funkciók.

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_Monitor/function.json)]

A kód, amely megvalósítja a függvény a következő:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Monitor/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript (csak függvények v2)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/index.js)]

Az orchestrator-funkció az alábbi műveleteket hajtja végre:

1. Lekérdezi a **MonitorRequest** álló a *hely* figyelése és a *telefonszám* , amely küld egy SMS-értesítést.
2. Meghatározza, hogy a figyelő a lejárati időt. A példa egy kódolt érték kihagytuk.
3. Hívások **E3_GetIsClear** meghatározni, hogy vannak-e skies törölje az adott helyen.
4. Ha az időjárás nincs bejelölve, a hívások **E3_SendGoodWeatherAlert** egy SMS-értesítést küldeni a kért telefonszám.
5. Létrehoz egy tartós időzítőt, amely a készítsen, a következő lekérdezési időköze folytathatja. A példa egy kódolt érték kihagytuk.
6. Továbbra is fut, amíg a [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) pass lejárati idő a figyelése, vagy SMS-riasztást küld.

Több orchestrator példányai egyidejűleg futtatható több küldésével **MonitorRequests**. A hely figyelése és a egy SMS-riasztás küldése a telefonszám adható meg.

## <a name="strongly-typed-data-transfer-net-only"></a>Szigorú típusmegadású adatátvitel (csak a .NET)

Az orchestrator adatokra van szükség több adatot, ezért [megosztott POCO objektumok](functions-reference-csharp.md#reusing-csx-code) szigorú típusmegadású adatátvitel C# és a C#-szkript használatosak: [!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/MonitorRequest.csx)]

[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/Location.csx)]

A JavaScript példa JSON-objektumok rendszeres paraméterekként.

## <a name="helper-activity-functions"></a>Segédfüggvények tevékenység

Az egyéb minták a tevékenység segédfüggvények használó normál funkciók vannak a `activityTrigger` kötés aktiválásához. A **E3_GetIsClear** függvény beolvassa az aktuális időjárási feltételek, a szolgáltatás emellett az illegális időjárási API használatával, és határozza meg, hogy a sky törlése. A *function.json* a következők:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/function.json)]

Ez pedig a megvalósítás. A POCOs az adatátvitelhez használni, például kezelni az API-hoz logikai hívja, és a JSON kiveszik a C#-ban egy megosztott osztályba válasz elemzéséhez. Részeként megtalálja a [Visual Studio-mintakód](#run-the-sample).

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript (csak függvények v2)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/index.js)]

A **E3_SendGoodWeatherAlert** függvény a Twilio-kötést használja, hogy-e egy útmutató egy jó ideje a végfelhasználó értesítése SMS üzenet küldése. A *function.json* egyszerű:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/function.json)]

Ez pedig a kódot, amely az SMS-üzenet küldése:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript (csak függvények v2)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/index.js)]

## <a name="run-the-sample"></a>Minta futtatása

Használja a HTTP-eseményindítókkal aktivált függvényeket, a mintában szereplő, elkezdheti a vezénylési a következő HTTP POST-kérelem küldésével:

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "Location": { "City": "Redmond", "State": "WA" }, "Phone": "+1425XXXXXXX" }
```
```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

   > [!NOTE]
   > Jelenleg a JavaScript vezénylési alapszintű funkciók nesmí vracet hodnotu példány felügyeleti URI-k. Ez a funkció egy későbbi kiadásban fog bővülni.

A **E3_Monitor** példány elindul, és lekérdezi az aktuális időjárási feltételek a kért hely. Ha az időjárás nincs bejelölve, meghívja a riasztást; küldése tevékenység függvényének Ellenkező esetben beállít egy számlálót. Amikor az időzítő lejár, a vezénylési folytatódik.

A vezénylési megnézzük a függvény Tevékenységnaplók az Azure Functions portálon tekintheti meg.

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

A vezénylési fog [leállítása](durable-functions-instance-management.md#terminating-instances) skies észlelése után eléri, vagy törölje annak időkorlátja. Is `TerminateAsync` másik függvényt, vagy hívja a **terminatePostUri** felett, és cserélje le a 202-es válaszban hivatkozott HTTP POST-webhook `{text}` a felmondás oka:

```
POST https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="visual-studio-sample-code"></a>A Visual Studio-mintakód

Az orchestration-fájlként egyetlen C# Visual Studio-projektet a következő:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs)]

## <a name="next-steps"></a>További lépések

Ez a példa bemutatta, hogy egy külső forrás állapotának figyelése Durable Functions használatával használatával [tartós időzítők](durable-functions-timers.md) és feltételes logika. A következő minta bemutatja, hogyan használja a külső eseményeket és [tartós időzítők](durable-functions-timers.md) kezelésére, emberi beavatkozást igényel.

> [!div class="nextstepaction"]
> [Az emberi beavatkozás minta futtatása](durable-functions-phone-verification.md)
