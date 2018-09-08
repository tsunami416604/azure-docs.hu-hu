---
title: Időtúllépés a Durable Functions – Azure és az emberi beavatkozás
description: Megtudhatja, hogyan képes kezelni az emberi beavatkozás időtúllépések a Durable Functions bővítmény az Azure Functions.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: azfuncdf
ms.openlocfilehash: 54c0c8e07ee4a248565b4d71562400c8f427fa77
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092920"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Durable Functions - Phone minta az emberi beavatkozást igényel

Ez a minta azt ismerteti, hogyan hozhat létre egy [Durable Functions](durable-functions-overview.md) vezénylési, amely magában foglalja az emberi beavatkozást igényel. Mindig, amikor egy valódi személy regisztrál egy automatikus folyamat, a folyamat értesítések küldése a személy, és aszinkron módon válaszokat kaphatnak képesnek kell lennie. Azt is lehetővé kell annak lehetőségét, hogy a személy nem érhető el. (Az utolsó rész az, ahol időtúllépések válnak fontossá.)

Ez a minta egy SMS-alapú phone ellenőrzési rendszerétől valósítja meg. Az ilyen típusú folyamatok gyakran használják a felhasználó telefonszám ellenőrzésekor, vagy a multi-factor authentication (MFA). Ennek oka egy hatékony példa a teljes megvalósítás történik, néhány kisebb függvények használatával. Nincsenek külső adattár, például egy adatbázisba, nem szükséges.

## <a name="prerequisites"></a>Előfeltételek

* [Durable Functions telepítése](durable-functions-install.md).
* Végezze el a [Hello feladatütemezési](durable-functions-sequence.md) forgatókönyv.

## <a name="scenario-overview"></a>Forgatókönyv áttekintése

Telefonos ellenőrzés, hogy a végfelhasználók az alkalmazás nem levélszemétküldők és azok, akik mondják ellenőrzésére szolgál. A multi-factor authentication szolgáltatás egy gyakori alkalmazási helyzet védelme érdekében felhasználói fiókokat a támadókat. A saját telefonos ellenőrzés végrehajtása az a kihívás abban áll, hogy igényel egy **állapot-nyilvántartó interakció** a viselkedjünk kulturáltan. A végfelhasználó általában biztosított néhány kódot (például 4 számjegyből szám), és válaszolnia kell **ésszerű időn belül**.

Normál Azure Functions állapot nélküli (mint sok más felhőbeli végpont más platformokon is), ezért az ilyen típusú kapcsolati járnak, explicit módon kezelése külsőleg állapota egy adatbázisban vagy valamilyen más állandó tárolja. A kapcsolati emellett több függvényt, amely koordinált együtt kell felosztani. Például szüksége legalább egy függvény meghatározza az egy kódot, valahol megőrzése, és a felhasználó telefonja és küldi el. Emellett szüksége lesz legalább egy másik függvényt válasz érkezik a felhasználó és valamilyen módon hozzárendelheti az eredeti függvény hívásához szükséges ahhoz, hogy hajtsa végre a kódérvényesítést. Időtúllépés az is fontos elemét alkotják biztonsága érdekében. Ez is gyorsan viszonylag összetett.

Durable Functions használatakor ez a forgatókönyv bonyolultságát jelentősen csökken. Ebben a példában láthatja, ahogy egy orchestrator-függvényt az állapot-nyilvántartó interakció kezelheti, egyszerűen és bármilyen külső adattárolók bevonása nélkül. Mivel az orchestrator funkciók *tartós*, egyúttal magas megbízhatóságú ezeket interaktív folyamatokat.

## <a name="configuring-twilio-integration"></a>Twilio-integráció konfigurálása

[!INCLUDE [functions-twilio-integration](../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>Az funkciók

Ez a cikk végigvezeti a mintaalkalmazást a következő funkciókat:

* **E4_SmsPhoneVerification**
* **E4_SendSmsChallenge**

Az alábbi szakaszok ismertetik a konfiguráció és a C# a parancsfájlkezelést, és a JavaScript által használt kódot. A Visual Studio fejlesztési kód a cikk végén található meg.
 
## <a name="the-sms-verification-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Az SMS ellenőrzési vezénylési (Visual Studio Code és az Azure portal minta kód) 

A **E4_SmsPhoneVerification** a funkció a standard *function.json* az orchestrator-funkciók.

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/function.json)]

A kód, amely megvalósítja a függvény a következő:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript (csak függvények v2)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

Megkezdése után az orchestrator-funkció a következőket teszi:

1. Lekérdezi egy telefonszámot, amelyre fog *küldése* az SMS-értesítést.
2. Hívások **E4_SendSmsChallenge** küldjön SMS-t, hogy a felhasználó és az értéket ad vissza biztonsági másolatot a várt 4 jegyű ellenőrző kódot.
3. Hoz létre egy tartós időzítőt, hogy az eseményindítók 90 másodperc alatt az aktuális idő.
4. Az időzítő párhuzamosan, megvárja, amíg egy **SmsChallengeResponse** esemény a felhasználó elől.

A felhasználó kap egy SMS-üzenet egy négyjegyű kóddal. 90 másodperc alatt, hogy ugyanolyan 4 számjegyű kód küldi vissza az orchestrator függvény példány az ellenőrzési folyamat elvégzéséhez rendelkeznek. Ha ezek a hibás kódot küldenek, kapnak egy további három megpróbálja beolvasni a jobb oldali (belül ugyanebben a 90 másodperces ablakban).

> [!NOTE]
> Nem lehet első, de az orchestrator nyilvánvaló függvény teljesen determinisztikus. Ennek az az oka az `CurrentUtcDateTime` tulajdonság rendszer kiszámítja az időzítő lejárati időt, és ezt a tulajdonságot minden egyes ismétlés ezen a ponton az orchestrator-kódban a ugyanazt az értéket adja vissza. Ez fontos annak érdekében, hogy azonos `winner` eredménye minden ismétlődő meghívásához `Task.WhenAny`.

> [!WARNING]
> Fontos, hogy [időzítők Mégse](durable-functions-timers.md) Ha már nincs szükség van rájuk jár le, amikor egy kérdés-válasz elfogadja a fenti példában látható módon.

## <a name="send-the-sms-message"></a>Az SMS-üzenet küldése

A **E4_SendSmsChallenge** függvény a Twilio-kötést használja a 4-jegyű kódot tartalmazó SMS-üzenet küldése a végfelhasználónak. A *function.json* a következők:

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/function.json)]

Ez pedig a kódot, amely a 4-jegyű ellenőrző kódot állít elő, és az SMS-üzenet küldése:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript (csak függvények v2)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

Ez **E4_SendSmsChallenge** függvény beolvassa volat pouze jednou, még akkor is, ha a folyamat leáll vagy visszajátszani beolvasása. Ez a jó, mert nem szeretné a felhasználó több SMS-üzenet beolvasása. A `challengeCode` vissza az értéket a rendszer automatikusan megőrzi, így az orchestrator függvény mindig tudja, mit jelent a helyes kódot.

## <a name="run-the-sample"></a>Minta futtatása

Használja a HTTP-eseményindítókkal aktivált függvényeket, a mintában szereplő, elkezdheti a vezénylési a következő HTTP POST-kérelem küldésével:

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

   > [!NOTE]
   > Jelenleg a JavaScript vezénylési alapszintű funkciók nesmí vracet hodnotu példány felügyeleti URI-k. Ez a funkció egy későbbi kiadásban fog bővülni.

Az orchestrator függvény kap a megadott telefonszámot, és azonnal elküldi egy véletlenszerűen létrehozott 4 jegyű ellenőrző kódot tartalmazó SMS-ben &mdash; például *2168*. A függvény majd vár 90 másodpercet a válaszra.

Válasz a kóddal, használhatja `RaiseEventAsync` másik függvényt, vagy hívja a **sendEventUrl** felett, és cserélje le a 202-es válaszban hivatkozott HTTP POST webhook `{eventName}` az esemény nevét `SmsChallengeResponse`:

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Küld ez az időzítő lejárata előtt, ha a vezénylési befejezése és a `output` mező értéke `true`, jelezve sikeres ellenőrzés.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```
```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Ha hagyja, hogy az időzítő lejár, vagy ha négyszer ad meg a megfelelő code, az állapot lekérdezése, és tekintse meg a `false` vezénylési függvény kimeneti, jelezve, hogy telefonos ellenőrzés sikertelen volt.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="visual-studio-sample-code"></a>A Visual Studio-mintakód

Az orchestration-fájlként egyetlen C# Visual Studio-projektet a következő:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs)]

## <a name="next-steps"></a>További lépések

Ez a példa bemutatta, Durable Functions, a fejlett funkciói többek között `WaitForExternalEvent` és `CreateTimer`. Megtudhatta, hogyan ezek kombinálható `Task.WaitAny` egy megbízható időtúllépési rendszer, amely valódi személyek fogják folytatott interakcióra szolgáló gyakran hasznos. Durable Functions használata egy konkrét témakörök részletes lefedettséget kínáló cikksorozat olvasásával többet tudhat meg.

> [!div class="nextstepaction"]
> [Ugrás a sorozat első cikkében](durable-functions-bindings.md)
