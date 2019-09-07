---
title: Emberi interakció és időtúllépés a Durable Functions-ben – Azure
description: Ismerje meg, hogyan kezelheti az emberi interakciókat és időtúllépéseket a Azure Functions Durable Functions bővítményében.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 4d8955517450ce3b4efdf30e2790e4be678dfc7b
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735181"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Emberi interakció Durable Functions – telefonos ellenőrzési minta

Ez a példa azt mutatja be, hogyan hozhat létre olyan [Durable functions](durable-functions-overview.md) -előkészítést, amely emberi beavatkozást is magában foglal. Amikor egy valós személy részt vesz egy automatizált folyamatban, a folyamatnak képesnek kell lennie értesítéseket küldeni a személynek, és aszinkron módon fogadnia a válaszokat. Azt is lehetővé kell tenni, hogy a személy nem érhető el. (Ez utóbbi részben az időtúllépések fontosak lesznek.)

Ez a példa egy SMS-alapú telefonos ellenőrzési rendszert valósít meg. Az ilyen típusú folyamatokat gyakran használják az ügyfél telefonszámának vagy a többtényezős hitelesítés (MFA) ellenőrzéséhez. Ez egy hatékony példa, mivel a teljes implementáció pár kis függvény használatával történik. Nincs szükség külső adattárra, például egy adatbázisra.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Forgatókönyv áttekintése

A telefonos ellenőrzés segítségével ellenőrizheti, hogy az alkalmazás végfelhasználói nem levélszemétek-e, és hogy kik azok, akik azt mondják. A többtényezős hitelesítés gyakori használati eset a felhasználói fiókok támadók általi védelméhez. A saját telefonos ellenőrzés megvalósításának kihívása az, hogy a rendszer **állapot-nyilvántartó interakciót** igényel egy emberi lényrel. A végfelhasználók általában bizonyos kódokat (például egy 4 számjegyű számot) adnak meg, és **ésszerű**időn belül válaszolni kell rájuk.

A szokásos Azure Functions állapot nélküliek (mint a más platformokon futó más Felhőbeli végpontok), ezért az ilyen típusú interakciók az adatbázisban vagy valamilyen más állandó tárolóban kifejezetten az állapot felügyeletét vonják maguk után. Emellett az interakciót több, egymással koordinálható függvénybe kell bontani. Például szükség van legalább egy függvényre a kód kiválasztásához, a megtartáshoz és a felhasználó telefonjára való elküldéséhez. Emellett szükség van legalább egy másik függvényre, hogy választ kapjon a felhasználótól, és valahogy vissza kell képeznie az eredeti függvény hívására, hogy el lehessen végezni a kód érvényesítését. Az időkorlát szintén fontos szempont a biztonság biztosításához. Ez elég bonyolult lehet.

A forgatókönyv összetettsége nagy mértékben csökkent a Durable Functions használatakor. Ahogy az ebben a példában is látható, egy Orchestrator-függvény egyszerűen, külső adattárak nélkül kezelheti az állapot-nyilvántartó interakciókat. Mivel a Orchestrator függvények *tartósak*, az interaktív folyamatok is nagyon megbízhatóak.

## <a name="configuring-twilio-integration"></a>Twilio-integráció konfigurálása

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>A függvények

Ez a cikk végigvezeti a minta alkalmazás következő funkcióiról:

* **E4_SmsPhoneVerification**
* **E4_SendSmsChallenge**

A következő szakaszokban ismertetjük a C# parancsfájlok és a JavaScriptek konfigurációját és kódját. A Visual Studio-fejlesztés kódja a cikk végén látható.

## <a name="the-sms-verification-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Az SMS-ellenőrzés összehangolása (Visual Studio Code és Azure Portal mintakód)

A **E4_SmsPhoneVerification** függvény a standard *function. JSON* fájlt használja a Orchestrator függvényekhez.

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/function.json)]

Itt látható a függvényt megvalósító kód:

### <a name="c-script"></a>C#Parancsfájl

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

Az indítás után a Orchestrator függvény a következő műveleteket végzi el:

1. Beolvas egy telefonszámot, amelyre az SMS-értesítést *küldi* .
2. Meghívja a **E4_SendSmsChallenge** , hogy SMS-üzenetet küldjön a felhasználónak, és visszaadja a várt 4 számjegyű Challenge-kódot.
3. Egy tartós időzítőt hoz létre, amely az aktuális időpontból 90 másodpercet indít el.
4. Az időzítővel párhuzamosan vár egy **SmsChallengeResponse** eseményt a felhasználótól.

A felhasználó egy négyjegyű kóddal rendelkező SMS-üzenetet kap. Az ellenőrzési folyamat elvégzéséhez 90 másodperc áll rendelkezésre, hogy ugyanazt a 4 számjegyű kódot vissza lehessen küldeni a Orchestrator függvény példányára. Ha nem a megfelelő kódot küldi el, a rendszer további három kísérletet tesz arra, hogy jobbat kapjon (ugyanazon a 90-másodperces ablakban).

> [!NOTE]
> Előfordulhat, hogy először nem nyilvánvaló, de ez a Orchestrator függvény teljesen determinisztikus. Ennek az az oka `CurrentUtcDateTime` , hogy a ( `currentUtcDateTime` .net) és a (JavaScript) tulajdonságokat használja az időzítő lejárati idejének kiszámításához, és ezek a tulajdonságok ugyanazt az értéket adják vissza a Orchestrator-kód ezen pontján lévő összes visszajátszás esetében. Ez fontos annak biztosítása érdekében, hogy a `winner` `Task.WhenAny` (.net) vagy `context.df.Task.any` a (JavaScript) minden ismétlődő hívása ugyanaz legyen.

> [!WARNING]
> Fontos [megszakítani az időzítőt](durable-functions-timers.md) , ha már nincs rájuk szükség a lejárathoz, ahogy a fenti példában a kérdéses válasz elfogadásakor is.

## <a name="send-the-sms-message"></a>SMS-üzenet küldése

A **E4_SendSmsChallenge** függvény a Twilio kötés használatával küldi el az SMS-üzenetet a 4 számjegyű kóddal a végfelhasználónak. A *function. JSON* a következőképpen van definiálva:

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/function.json)]

Itt látható a 4 számjegyből álló hibakódot generáló kód, amely az SMS-üzenetet küldi el:

### <a name="c-script"></a>C#Parancsfájl

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

Ez a **E4_SendSmsChallenge** függvény csak egyszer hívható meg, még akkor is, ha a folyamat összeomlik vagy újra le lesz játszva. Ez azért hasznos, mert nem szeretné, hogy a végfelhasználó több SMS-üzenetet is beolvasson. A `challengeCode` visszatérési érték automatikusan megmarad, így a Orchestrator függvény mindig tudja, hogy mi a helyes kód.

## <a name="run-the-sample"></a>Minta futtatása

A mintában szereplő HTTP-triggerű függvények használatával a következő HTTP POST-kérelem elküldésével elindíthatja a koordinálást:

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
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"id":"741c65651d4c40cea29acdd5bb47baf1","statusQueryGetUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","sendEventPostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","terminatePostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

A Orchestrator függvény a megadott telefonszámot fogadja, és azonnal SMS-üzenetet küld egy véletlenszerűen generált 4 számjegyű ellenőrző kóddal &mdash; , például *2168*. A függvény ezután 90 másodpercet vár a válaszra.

A kóddal való válaszadáshoz használhatja `{eventName}` [ `RaiseEventAsync` a (.net) vagy `raiseEvent` a (JavaScript)](durable-functions-instance-management.md) függvényt egy másik függvényen belül, vagy meghívhatja a fenti 202-válaszban hivatkozott **sendEventUrl** http post webhookot, a helyére pedig a `SmsChallengeResponse`esemény:

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Ha ezt az időzítő lejárata előtt küldi el, a rendszer `output` `true`befejeződik, és a mező értéke, amely sikeres ellenőrzést jelez.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Ha engedélyezte az időzítő érvényességét, vagy ha négyszer adja meg a kódot, akkor lekérdezheti az állapotot, és megtekintheti `false` az előkészítési függvény kimenetét, ami azt jelzi, hogy a telefon ellenőrzése nem sikerült.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="visual-studio-sample-code"></a>Visual Studio-mintakód

Íme egy Visual Studio-projekt egyetlen C# fájlja:

> [!NOTE]
> Az alábbi mintakód futtatásához telepítenie kell a `Microsoft.Azure.WebJobs.Extensions.Twilio` Nuget csomagot.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs)]

## <a name="next-steps"></a>További lépések

Ez a példa a Durable functions speciális képességeit mutatja be, például `WaitForExternalEvent` és. `CreateTimer` Megismerte, hogy ezek hogyan kombinálhatók a `Task.WaitAny` szolgáltatással egy megbízható időtúllépési rendszer megvalósításához, ami gyakran hasznos a valós emberekkel való interakcióhoz. Ha többet szeretne megtudni a Durable Functions használatáról, tekintse meg az egyes témakörök részletes lefedettségét biztosító cikkek sorozatát.

> [!div class="nextstepaction"]
> [Ugrás a sorozat első cikkére](durable-functions-bindings.md)
