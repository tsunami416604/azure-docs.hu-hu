---
title: Emberi interakció és időmeghosszabbítása a tartós funkciókban - Azure
description: Ismerje meg, hogyan kezelhető az emberi interakció és az időmegtetések az Azure Functions durable functions bővítményében.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 4e0f71369bc02fdce5625d9c74e1d52264ed86be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335747"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Emberi interakció a tartós funkciókban - Telefon-ellenőrzési minta

Ez a minta bemutatja, hogyan hozhat létre egy [tartós függvények](durable-functions-overview.md) vezénylési, amely magában foglalja az emberi interakció. Ha egy valós személy részt vesz egy automatizált folyamatban, a folyamatnak képesnek kell lennie arra, hogy értesítéseket küldjön a személynek, és aszinkron módon fogadja a válaszokat. Lehetővé kell tennie azt a lehetőséget is, hogy a személy nem érhető el. (Ez az utolsó rész az, ahol az időhosszabbításfontossá válik.)

Ez a minta egy SMS-alapú telefonellenőrző rendszert valósít meg. Az ilyen típusú folyamatokat gyakran használják az ügyfél telefonszámának ellenőrzésekor vagy a többtényezős hitelesítéshez (MFA). Ez egy hatékony példa, mert a teljes végrehajtás történik egy pár kis funkciókat. Nincs szükség külső adattárra, például adatbázisra.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Forgatókönyv áttekintése

A telefonos ellenőrzés annak ellenőrzésére szolgál, hogy az alkalmazás végfelhasználói nem spamelők-e, és hogy azok-e, akiknek mondják őket. A többtényezős hitelesítés gyakori használati eset a felhasználói fiókok hackerekkel szembeni védelmére. A kihívás végrehajtása a saját telefon ellenőrzése, hogy szükség van egy **impozáns kölcsönhatás** egy emberi lény. A végfelhasználó általában megadott valamilyen kódot (például egy négyjegyű számot), és ésszerű időn át kell **válaszolnia.**

A szokásos Azure-függvények állapot nélküliek (csakúgy, mint sok más felhővégpont más platformokon), így az ilyen típusú interakciók magában foglalja az állapot kifejezett kezelését egy adatbázisban vagy más állandó tárolóban. Ezenkívül a kölcsönhatást több, összehangolható függvényre kell felbontani. Például legalább egy funkcióra van szüksége a kód meghatározásához, a kód megőrzéséhez és a felhasználó telefonjára való elküldéséhez. Ezenkívül legalább egy másik függvényre van szüksége ahhoz, hogy választ kapjon a felhasználótól, és valahogy visszaképezze az eredeti függvényhíváshoz a kódérvényesítés elvégzéséhez. Az időhosszabbítás szintén fontos szempont a biztonság biztosításához. Ez kap elég bonyolult gyorsan.

A tartós függvények használata esetén jelentősen csökken a forgatókönyv összetettsége. Ebben a példában látható, egy orchestrator függvény kezelheti az állapotalapú interakció könnyen és külső adattárak bevonása nélkül. Mivel az orchestrator függvények *tartósak,* ezek az interaktív folyamatok is rendkívül megbízhatóak.

## <a name="configuring-twilio-integration"></a>Twilio-integráció konfigurálása

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>A funkciók

Ez a cikk a mintaalkalmazás következő függvényeit ismerteti:

* `E4_SmsPhoneVerification`: A telefon ellenőrzési folyamatát végző [orchestrator-függvény,](durable-functions-bindings.md#orchestration-trigger) beleértve az időbeli megtorások és az újrapróbálkozások kezelését.
* `E4_SendSmsChallenge`: Olyan [tevékenységfüggvény,](durable-functions-bindings.md#activity-trigger) amely szöveges üzenetben küld egy kódot.

### <a name="e4_smsphoneverification-orchestrator-function"></a>E4_SmsPhoneVerification orchestrator függvény

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs?range=17-70)]

> [!NOTE]
> Lehet, hogy először nem nyilvánvaló, de ez az orchestrator függvény teljesen determinisztikus. Determinisztikus, `CurrentUtcDateTime` mert a tulajdonság az időzítő lejárati idejének kiszámításához használatos, és ugyanazt az értéket adja vissza az orchestrator-kód ezen pontján minden visszajátszáskor. Ez a viselkedés fontos annak `winner` érdekében, hogy `Task.WhenAny`ugyanazaz eredmények et minden ismételt hívás.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A **E4_SmsPhoneVerification** függvény a standard *function.json* függvényt használja az orchestrator függvényekhez.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/function.json)]

Itt van a kód amit eszköz a függvény:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

> [!NOTE]
> Lehet, hogy először nem nyilvánvaló, de ez az orchestrator függvény teljesen determinisztikus. Determinisztikus, `currentUtcDateTime` mert a tulajdonság az időzítő lejárati idejének kiszámításához használatos, és ugyanazt az értéket adja vissza az orchestrator-kód ezen pontján minden visszajátszáskor. Ez a viselkedés fontos annak `winner` érdekében, hogy `context.df.Task.any`ugyanazaz eredmények et minden ismételt hívás.

---

Az indítás után ez az orchestrator függvény a következőket teszi:

1. Kap egy telefonszámot, amelyre *elküldi* az SMS értesítést.
2. Felhívja **E4_SendSmsChallenge,** hogy küldjön egy SMS-t a felhasználónak, és visszatér a várt négyjegyű kihíváskód.
3. Tartós időzítőt hoz létre, amely az aktuális időtől 90 másodpercre aktiválódik.
4. Az időzítővel párhuzamosan megvárja a felhasználó **SmsChallengeResponse** eseményét.

A felhasználó egy négyjegyű kóddal ellátott SMS-üzenetet kap. 90 másodpercük van arra, hogy ugyanazt a négyjegyű kódot visszaküldjék az orchestrator függvénypéldánynak az ellenőrzési folyamat befejezéséhez. Ha rossz kódot adnak be, további három próbálkozást kapnak, hogy helyesen járjanak el (ugyanazon a 90 másodperces ablakon belül).

> [!WARNING]
> Fontos, hogy [törölje az időzítőket,](durable-functions-timers.md) ha már nincs szüksége rájuk a lejárathoz, mint a fenti példában, amikor egy kihívásválaszt elfogad.

## <a name="e4_sendsmschallenge-activity-function"></a>E4_SendSmsChallenge tevékenység függvénye

A **E4_SendSmsChallenge** függvény a Twilio-kötés segítségével küldje el az SMS-üzenetet a négyjegyű kódot a végfelhasználónak.

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs?range=72-89)]

> [!NOTE]
> A mintakód `Microsoft.Azure.WebJobs.Extensions.Twilio` futtatásához telepítenie kell a Nuget csomagot.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A *function.json* a következőképpen határozható meg:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/function.json)]

És itt van a kód, amely létrehozza a négyjegyű kihívás kódot, és elküldi az SMS-t:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

---

## <a name="run-the-sample"></a>Minta futtatása

A mintában szereplő HTTP-aktivált függvények használatával a vezénylést a következő HTTP POST-kérelem elküldésével indíthatja el:

```
POST http://{host}/orchestrators/E4_SmsPhoneVerification
Content-Length: 14
Content-Type: application/json

"+1425XXXXXXX"
```

```
HTTP/1.1 202 Accepted
Content-Length: 695
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"id":"741c65651d4c40cea29acdd5bb47baf1","statusQueryGetUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","sendEventPostUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","terminatePostUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

Az orchestrator függvény megkapja a megadott telefonszámot, és azonnal küld neki egy SMS-t egy véletlenszerűen generált 4 számjegyű ellenőrző kóddal, &mdash; például *2168*. A függvény ezután 90 másodpercet vár a válaszra.

A kóddal történő válaszadáshoz használhatja `{eventName}` `SmsChallengeResponse` [ `RaiseEventAsync` a (.NET) vagy `raiseEvent` a (JavaScript)](durable-functions-instance-management.md) függvényt egy másik függvényben, vagy meghívhatja a **sendEventUrl** HTTP POST webhookot, amelyre a fenti 202 válaszban hivatkozunk, és az esemény nevére cserélve, :

```
POST http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Ha ezt az időzítő lejárta előtt elküldi, a `output` vezénylés befejeződik, és a mező be van állítva `true`, jelezve a sikeres ellenőrzést.

```
GET http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Ha hagyja, hogy az időzítő lejárjon, vagy ha négyszer rossz kódot ad meg, lekérdezheti az állapotot, és megnézhet egy `false` vezénylési függvény kimenetét, jelezve, hogy a telefon ellenőrzése sikertelen volt.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="next-steps"></a>További lépések

Ez a minta bemutatta a tartós függvények `WaitForExternalEvent` néhány `CreateTimer` speciális képességeit, nevezetesen az API-kat. Látta, hogyan lehet ezeket `Task.WaitAny` kombinálni, hogy végre egy megbízható időtúltöltési rendszer, amely gyakran hasznos kölcsönhatásban valódi emberekkel. A Durable Functions használatáról további információval egészül ki, ha elolvas egy cikksorozatot, amely részletes enciklést kínál az egyes témakörökről.

> [!div class="nextstepaction"]
> [Ugrás a sorozat első cikkére](durable-functions-bindings.md)
