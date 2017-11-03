---
title: "Emberi beavatkozást igényel, és a tartós funkciók - Azure időtúllépések"
description: "Útmutató az Azure Functions emberi beavatkozást igényel, és a tartós funkciók bővítményben időtúllépések kezeléséhez."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: cfb6758703ebf3ce0458a4e1ad74324a4ccc2ece
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2017
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Emberi beavatkozást igényel a tartós funkciók - telefon ellenőrzési minta

Ez a példa bemutatja, hogyan hozhat létre egy [tartós funkciók](durable-functions-overview.md) vezénylési, amely magában foglalja a emberi beavatkozást igényel. Ha részt vesz egy automatikus folyamat valós személy, a folyamat értesítéseket küldhet a személy és válaszok aszinkron módon képesnek kell lennie. Azt a lehetőséget, hogy az a személy nem érhető el is lehetővé teszi. (Az utolsó része, ahol időtúllépések fontos válik.)

Ez a minta egy SMS-alapú telefonos ellenőrzési rendszerétől valósítja meg. Ezek a típusok flow gyakran használják egy ügyfél telefonszámát ellenőrzésekor, vagy a multi-factor authentication (MFA). Ennek oka egy hatékony példa a teljes megvalósítás elkészült néhány kis függvények használatával. Külső adatok tároló, például adatbázisokba, meg kell adni.

## <a name="prerequisites"></a>Előfeltételek

* Kövesse az utasításokat a [tartós funkciók telepítése](durable-functions-install.md) a minta beállítása.
* Ez a cikk feltételezi, hogy már lezajlott a [Hello feladatütemezési](durable-functions-sequence.md) minta forgatókönyv.

## <a name="scenario-overview"></a>Forgatókönyv áttekintése

Telefonszám ellenőrzése a szolgál egy győződjön meg arról, hogy a végfelhasználók az alkalmazás nem levélszemétküldők és, hogy azok mondja ki, hogy azok ki. Multi-factor authentication egy gyakori használati eset a felhasználói fiókok védelme érdekében a támadóktól. Az ügyfél a saját Telefonszám ellenőrzése a végrehajtási kérdésre szüksége van egy **állapot-nyilvántartó interakció** egy ember rendelkező. A felhasználó általában biztosított néhány kódot (pl. 4-jegyű szám), és kell válaszolnia **elfogadható időn belül**.

Szokásos Azure Functions állapot nélküli (a rendszer sok más felhőalapú végpont platformokon), így ezek a típusok kölcsönhatások tartalmazzon explicit módon kezelése külsőleg állapot egy adatbázisban vagy valamilyen más állandó tárolja. A kapcsolati emellett több funkció, amely koordinált együtt kell felosztva. Legalább egy függvény például eldönti, a kódot, megőrzése ki valahol és a felhasználó telefonjára küldéséhez kell. Emellett egyéb kapott választ a felhasználó és valamilyen módon leképez az eredeti függvény hívásához szükséges művelet a kód érvényességi végrehajtásához legalább egy függvény van szüksége. Időtúllépés az is fontos eleme biztonsága érdekében. Ez elég bonyolult közérthető gyorsan kaphatunk.

Ebben a forgatókönyvben összetettsége jelentős mértékben csökken, ha a tartós funkciók használata. Ahogy látni fogja, ez a példa, egy orchestrator-funkció az állapot-nyilvántartó beavatkozás nagyon egyszerűen és kezelhetők minden külső adattárolókhoz bevonása nélkül. Mivel az orchestrator funkciók *tartós*, interaktív folyamok megtalálhatók nagymértékben megbízható.

## <a name="configuring-twilio-integration"></a>Twilio-integráció konfigurálása

Ez a minta használata szükséges a [Twilio](https://www.twilio.com/) SMS küldése mobiltelefonra szolgáltatást. Az Azure Functions már Twilio keresztül támogatása a [Twilio-kötés](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio), és a mintát használja ezt a szolgáltatást.

A thing először meg kell az, hogy a Twilio-fiók. Létrehozhat egy ingyenes, https://www.twilio.com/try-twilio. Miután egy fiókot, adja hozzá a következő három **Alkalmazásbeállítások** a projekthez.

| Alkalmazás-beállítás neve | Érték Leírás |
| - | - |
| **TwilioAccountSid**  | A SID Twilio-fiókja |
| **TwilioAuthToken**   | A hitelesítési jogkivonat Twilio-fiókja |
| **TwilioPhoneNumber** | A Twilio-fiókjához társított telefonszám. SMS küldése szolgál. |

## <a name="the-functions"></a>A Funkciók

Ez a cikk végigvezeti a mintaalkalmazást a következő funkciókat:

* **E4_SmsPhoneVerification**
* **E4_SendSmsChallenge**

Az alábbi szakaszok ismertetik a konfiguráció és a kódot, amely az Azure portál fejlesztésére szolgálnak. A Visual Studio fejlesztési kód a cikk végén meg.
 
## <a name="the-sms-verification-orchestration"></a>Az SMS-ellenőrzési vezénylési

A **E4_SmsPhoneVerification** függvény használja a normál *function.json* az orchestrator függvényekhez.

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/function.json)]

Ez a függvény megvalósító kódot:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/run.csx)]

Miután elindult, ez a függvény az orchestrator a következőket teszi:

1. Lekérdezi egy telefonszám, amelyre a következőket hajtja végre *küldése* az SMS notification.
2. Hívások **E4_SendSmsChallenge** egy SMS-üzenet küldhető a felhasználó és visszaadja a biztonsági másolatot a várt 4 számjegyből álló kérdéskód.
3. Létrehoz egy tartós számlálót, hogy az eseményindítók 90 másodpercet az aktuális idő.
4. Az időzítő párhuzamosan vár egy **SmsChallengeResponse** esemény a felhasználótól.

A felhasználó kap egy SMS-üzenet egy négyjegyű kóddal. 90 másodpercet, hogy ugyanazt a 4-jegyű kódot küldenek vissza az orchestrator függvény példány az ellenőrzési folyamat elvégzéséhez rendelkeznek. Helytelen kódot elküldenék, elérték egy további három záma jobbra (belül az azonos 90 második ablak) eléréséhez.

> [!NOTE]
> Nem lehet nyilvánvaló első, de az orchestrator, a függvény teljesen determinisztikus. Ennek az az oka a `CurrentUtcDateTime` tulajdonság használatával kiszámításához időzítő lejárati idejét, és ezt a tulajdonságot minden egyes ismétlés ezen a ponton az orchestrator-kódban a ugyanazt az értéket adja vissza. Ez fontos annak érdekében, hogy az azonos `winner` minden ismételt hívása annak az eredménye `Task.WhenAny`.

> [!WARNING]
> Fontos, hogy [szakítsa meg a CancellationTokenSource használatával időzítők](durable-functions-timers.md) Ha már nem szükséges lejár, a fent, amikor egy kérdés-válasz elfogadható példában látható módon.

## <a name="send-the-sms-message"></a>Az SMS-üzenet küldése

A **E4_SendSmsChallenge** a funkció a Twilio-kötés az a 4-jegyű kódot SMS üzenetet küldeni a végfelhasználók. A *function.json* az alábbiak szerint definiáltuk:

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/function.json)]

És itt a kódot, a 4 számjegyből álló ellenőrző kódot állít elő, és elküldi azt az SMS-üzenet:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/run.csx)]

Ez **E4_SendSmsChallenge** függvény csak menüelemnek egyszer, még akkor is, ha a folyamat leállásából eredő vagy lekérdezi a rendszer játssza vissza. Ez a helyes, mert nem szeretné, hogy a végfelhasználó több SMS-üzenet beolvasása. A `challengeCode` vissza az értéket automatikusan maradnak, az orchestrator függvény mindig tudja a helyes kódot van.

## <a name="run-the-sample"></a>A minta futtatásához

Használja a HTTP-eseményindítókkal aktivált függvényeket, a mintában szereplő, megkezdheti a vezénylési úgy, hogy a következő HTTP POST-kérelmet küld.

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

Az orchestrator függvény megkapja a megadott telefonszámot, és azonnal elküldi egy véletlenszerűen generált 4 számjegyből álló visszaigazolási kóddal SMS-üzenet &mdash; például *2168*. A függvény majd választ 90 másodpercet vár.

A kódot válaszban, használhatja `RaiseEventAsync` belül egy másik működik, vagy a meghívása a **sendEventUrl** fent, cseréje 202 válaszul hivatkozott HTTP POST webhook `{eventName}` az esemény nevű `SmsChallengeResponse`:

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Küldi el ez az időzítő lejárata előtt, ha a vezénylési befejezése és a `output` mező értéke `true`, jelző sikeres ellenőrzés.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```
```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Ha engedélyezi a időzítő lejár, vagy négy alkalommal ad meg helytelen kódot, ha az állapot lekérdezése, és tekintse meg a `false` vezénylési függvény kimeneti, jelezve, hogy Telefonszám ellenőrzése sikertelen.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="visual-studio-sample-code"></a>A Visual Studio mintakód

Íme egy egyetlen C# fájlban található, a Visual Studio-projekt vezénylési:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs)]

## <a name="next-steps"></a>Következő lépések

Ez a példa azt mutatják, néhány speciális funkciót, a tartós függvények, nevezetesen `WaitForExternalEvent` és `CreateTimer`. Megtudhatta, hogyan ezek kombinálva `Task.WaitAny` egy megbízható időtúllépés, a rendszer, amely gyakran hasznos a valódi személyek való interakció. Többet tudhat meg adott témákat részletes körét kínáló cikkek olvasásával tartós funkciók használatával kapcsolatos.

> [!div class="nextstepaction"]
> [Ugrás az adatsorozat első cikk](durable-functions-bindings.md)
