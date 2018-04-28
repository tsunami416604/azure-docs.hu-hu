---
title: Figyeli a tartós funkciók – Azure
description: Útmutató a használ a tartós funkciók kiterjesztést az Azure Functions állapotfigyelő végrehajtásához.
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
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: 9cb7a076ea922b9868bd439d160aec96f044e3b6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>A figyelő forgatókönyv tartós funkciókkal - időjárási megfigyelő minta

A figyelő mintát hivatkozik egy rugalmas *ismétlődő* folyamat például a munkafolyamat - lekérdezés csak bizonyos feltételek teljesülnek. Ez a cikk ismerteti egy használó mintaalkalmazás [tartós funkciók](durable-functions-overview.md) figyelés végrehajtásához.

## <a name="prerequisites"></a>Előfeltételek

* [Telepítse a tartós funkciók](durable-functions-install.md).
* Fejezze be a [Hello feladatütemezési](durable-functions-sequence.md) forgatókönyv.

## <a name="scenario-overview"></a>Forgatókönyv áttekintése

Ez a minta egy hely aktuális időjárási feltételek figyeli, és riasztást küld a felhasználó SMS, törölje a jelet a skies esetén. Egy rendszeres időzítő-eseményindítóval aktivált függvény segítségével a időjárása és értesítések küldése. Az egyik probléma ezt a módszert azonban **életciklusának kezelését**. Ha csak egy riasztást küldjön, a figyelő letiltása magát a törlés után kell időjárási észlel. A figyelési mintát saját végrehajtásának, között más előnyöket is befejezése:

* Időközönként futó figyelők nem ütemezi: egy időzítő indítófeltételt *fut* óránként; figyelő *vár* műveletek között egy óra. Egy figyelő műveletek nem átfedik amennyiben szerepel, amely a hosszan futó feladatokat fontos lehet.
* Dinamikus intervallumok lehet: a várakozási idő módosítható bizonyos feltétel alapján.
* Figyelők is leáll, amikor bizonyos feltétel teljesül, vagy egy másik folyamat megszakítása.
* Figyelők paraméterek is igénybe vehet. A példa bemutatja, hogyan ugyanazt az időjárás-figyelési folyamat alkalmazhatók a kért helyét és a telefonszámot.
* Figyelők méretezhetők legyenek. Mivel minden egyes figyelő vezénylési példánya, több monitor hozzon létre új funkciók, vagy további kód megadása nélkül hozhatók létre.
* Monitorok nagyobb munkafolyamatok könnyen integrálható. Egy figyelő lehet egy szakasz egy összetettebb vezénylési függvény, vagy egy [alárendelt vezénylési](https://docs.microsoft.com/azure/azure-functions/durable-functions-sub-orchestrations).

## <a name="configuring-twilio-integration"></a>Twilio-integráció konfigurálása

[!INCLUDE [functions-twilio-integration](../../includes/functions-twilio-integration.md)]

## <a name="configuring-weather-underground-integration"></a>Időjárás föld integrációjának konfigurálása

Ez a minta magában foglalja a föld időjárási API használatával ellenőrizze a hely aktuális időjárási feltételek.

Kell elsőként az egy időjárási föld fiók. Létrehozhat egy ingyenes [ https://www.wunderground.com/signup ](https://www.wunderground.com/signup). Ha van olyan fiókja, szüksége lesz API-kulcs beszerzése. Ehhez látogasson el [ https://www.wunderground.com/weather/api ](https://www.wunderground.com/weather/api), majd a kulcs beállításokat választ. A Stratus fejlesztői csomag a szabad és ez a minta futtatásához megfelelő.

Miután egy API-kulcsot, adja hozzá a következő **Alkalmazásbeállítás** függvény alkalmazása.

| Alkalmazás-beállítás neve | Érték Leírás |
| - | - |
| **WeatherUndergroundApiKey**  | A föld időjárás-API-kulcs. |

## <a name="the-functions"></a>A Funkciók

Ez a cikk ismerteti a mintaalkalmazást a következő funkciókat:

* `E3_Monitor`: Az orchestrator függvény, amely behívja `E3_GetIsClear` rendszeres időközönként. Meghívja `E3_SendGoodWeatherAlert` Ha `E3_GetIsClear` igaz értéket ad vissza.
* `E3_GetIsClear`: Egy tevékenység függvény, amely a hely aktuális időjárási feltételeket ellenőrzi.
* `E3_SendGoodWeatherAlert`: Egy tevékenység függvény, hogy a Twilio keresztül SMS üzenetet küld.

Az alábbi szakaszok ismertetik a konfiguráció és a használt kód a C# parancsfájlok. A Visual Studio fejlesztési kód a cikk végén meg.
 
## <a name="the-weather-monitoring-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>A vezénylési (Visual Studio Code és az Azure portál mintakód) figyelési időjárási

A **E3_Monitor** függvény használja a normál *function.json* az orchestrator függvényekhez.

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_Monitor/function.json)]

Ez a függvény megvalósító kódot:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Monitor/run.csx)]

Az orchestrator függvény a következő műveleteket hajtja végre:

1. Lekérdezi a **MonitorRequest** álló a *hely* figyelése és a *telefonszám* amely akkor lesz SMS szóló értesítés elküldése.
2. Meghatározza, hogy a figyelő az elévülési időt. A minta kivonatosan mutatja kódolt értéket használ.
3. Hívások **E3_GetIsClear** annak meghatározásához, hogy vannak-e egyszerű skies az adott helyen.
4. Nem egyértelmű, ha meghívja a **E3_SendGoodWeatherAlert** SMS szóló értesítés elküldése a kért telefonszáma.
5. Létrehoz egy tartós időzítő folytatásához a következő lekérdezési időköz az orchestration. A minta kivonatosan mutatja kódolt értéket használ.
6. Továbbra is fut, amíg a [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) fázisok lejárati idő a figyelése, vagy SMS-riasztást küld.

Az orchestrator több példánya is futtatható egyidejűleg több elküldésével **MonitorRequests**. A hely figyelése és a telefonszám SMS értesítést kérek adható meg.

## <a name="strongly-typed-data-transfer"></a>Erős típusmegadású adatátvitel

Az orchestrator adatokra van szükség több adatokat, így [megosztott POCO objektumok](functions-reference-csharp.md#reusing-csx-code) szigorú típusmegadású adatátvitel használhatók: [!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/MonitorRequest.csx)]

[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/Location.csx)]

## <a name="helper-activity-functions"></a>Tevékenység súgófunkciókat

Az egyéb minták tevékenység súgófunkciókat használó rendszeres funkciók vannak a `activityTrigger` indítás kötés. A **E3_GetIsClear** függvény lekérdezi az aktuális időjárási feltételek időjárási föld API használatával, és határozza meg, hogy a égbolt törölje a jelet. A *function.json* az alábbiak szerint definiáltuk:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/function.json)]

És ez a megvalósítás. Használ az adatátvitelhez POCOs, például a programot kezelni az API-t hívja, és elemezni a válasz JSON kiveszik, egy megosztott osztályba. Részeként megtalálja a [Visual Studio mintakód](#run-the-sample).

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/run.csx)]

A **E3_SendGoodWeatherAlert** a funkció a Twilio-kötést, amely értesíti a végfelhasználót, hogy a rendszer egy időben a bejárása az SMS-üzenet küldése. A *function.json* egyszerű:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/function.json)]

És az SMS-üzenet küldése kód:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/run.csx)]

## <a name="run-the-sample"></a>Minta futtatása

Használja a HTTP-eseményindítókkal aktivált függvényeket, a mintában szereplő, megkezdheti a vezénylési úgy, hogy a következő HTTP POST-kérelmet küld:

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

A **E3_Monitor** példány elindul, és lekérdezi a kért aktuális időjárási feltételeket. Ha a nem egyértelmű, meghívja-e egy tevékenység működnek, küldjön egy riasztást; Ellenkező esetben meghatároz egy számlálót. Ha az időkorlát lejár, a vezénylési fog folytatódni.

Ha megnézi a függvény a vezénylési tevékenység bejelentkezik az Azure Functions portálon tekintheti meg.

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

A vezénylési fog [leáll](durable-functions-instance-management.md#terminating-instances) után az időtúllépési elérte vagy törölje a jelet skies észlel. Is `TerminateAsync` belül egy másik működik, vagy a meghívása a **terminatePostUri** fent, cseréje 202 válaszul hivatkozott HTTP POST webhook `{text}` záráshoz indokkal:

```
POST https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="visual-studio-sample-code"></a>A Visual Studio mintakód

Íme egy egyetlen C# fájlban található, a Visual Studio-projekt vezénylési:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs)]

## <a name="next-steps"></a>További lépések

Ez a példa azt mutatják, tartós funkciók használata külső forrásból állapotának figyelésére szolgáló használatával [tartós időzítők](durable-functions-timers.md) és feltételes logikához. A következő példa bemutatja, hogyan használja külső eseményeket és [tartós időzítők](durable-functions-timers.md) kezelésére emberi beavatkozást igényel.

> [!div class="nextstepaction"]
> [Futtathatja a emberi beavatkozást igényel](durable-functions-phone-verification.md)
