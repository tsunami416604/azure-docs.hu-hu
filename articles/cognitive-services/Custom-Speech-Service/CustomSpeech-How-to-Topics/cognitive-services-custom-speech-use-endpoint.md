---
title: Egyéni beszéd végpont használatára az Azure-on egyéni beszéd szolgáltatással |} Microsoft Docs
description: Ismerje meg, hogy egy egyéni beszéd-szöveg endpoint használatáról kognitív szolgáltatásban az egyéni beszéd szolgáltatással.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: d28065d7962ee660cafd4b3321abdd6a8f94abcb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347159"
---
# <a name="use-a-custom-speech-to-text-endpoint"></a>Egyéni beszéd-szöveg végpont használatára
Ön kéréseket küldhetnek egy Azure egyéni beszéd beszéd-szöveg szolgáltatásvégpont, hasonló módon, mint a alapértelmezett kognitív szolgáltatások diktálásfelismerési végpontjához. Ezeket a végpontokat funkcionálisan megegyeznek a hang transzformációs API az alapértelmezett végpontok. Ugyanezeket a funkciókat, amelyek az ügyféloldali kódtár vagy a hang transzformációs API REST API használatával érhető el, így a egyéni végponthoz is érhető el.

Ez a szolgáltatás használatával hoz létre a végpontok különböző számú egyidejű kérelmet tud feldolgozni. A kötet attól függ, hogy az Ön előfizetéséhez rendelve az árképzési szint. Túl sok kérelem érkezik, ha hiba történik. Ingyenes szint kérelmek havi legfeljebb rendelkezik.

A szolgáltatás azt feltételezi, hogy adatai átkerülnek a valós idejű. Ha gyorsabb továbbítja, a kérelem nem működését, amíg a rendszer megfelelt a valós idejű hang időtartamát.

> [!NOTE]
> Támogatjuk a [új webes szoftvercsatornák](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/websocketprotocol) még. Ha webes szoftvercsatornák használata a egyéni beszéd-végpontot, kövesse az utasításokat itt.
>
> Az új [REST API](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest) támogatása hamarosan elérhető. Ha azt tervezi, a HTTP Protokollon keresztül egyéni beszéd-végpontot hívására, kövesse az utasításokat itt.
>

## <a name="send-requests-by-using-the-speech-client-library"></a>Kérelmek küldése a beszédfelismerés ügyféloldali kódtár használatával

Kérelmek küldése a egyéni végponthoz a beszédfelismerés ügyféloldali kódtár használatával, a felismerési ügyfél elindítása. Az ügyfél beszéd SDK a [NuGet](http://nuget.org/). Keresse meg *beszédfelismerés*, és válassza ki a beszédfelismerés felismerés csomagot a Microsoft a platformra. Néhány mintakód található [GitHub](https://github.com/Microsoft/Cognitive-Speech-STT-Windows). Az ügyfél beszéd SDK biztosít a gyári osztály **SpeechRecognitionServiceFactory**, amely az alábbi módszereket kínál:

  *   ```CreateDataClient(...)```: A felismerési ügyfél.
  *   ```CreateDataClientWithIntent(...)```: Egy adatok használatát az ügyfél biztonsági mentés.
  *   ```CreateMicrophoneClient(...)```: A mikrofon használatát az ügyfél.
  *   ```CreateMicrophoneClientWithIntent(...)```: A mikrofon használatát az ügyfél biztonsági mentés.

Részletes dokumentációt, tekintse meg a [Bing Diktálásfelismerési API](https://docs.microsoft.com/azure/cognitive-services/speech/home). Az egyéni beszéd Szolgáltatásvégpontok az azonos SDK támogatja.

Az adatok felismerés ügyfél nem megfelelő beszédfelismerési adatokból, például fájlt vagy más hang forrás. A mikrofon használatát ügyfél a mikrofon a beszédfelismerés megfelelő. Bármelyik ügyfél szándéka használatát strukturált leképezési eredményeinek lépjen vissza a [nyelvi ismertetése intelligens szolgáltatás](https://www.luis.ai/) (LUIS), ha létrehozott egy LUIS alkalmazást a forgatókönyvéhez.

Négy típusú ügyfelek kétféle módon lehet létrehozni. Az első módszer a szabványos kognitív szolgáltatások Diktálásfelismerési API-t használja. A második módja lehetővé teszi egy URL-cím, amely megfelel az egyéni beszéd szolgáltatással létrehozott egyéni végpontot.

Létrehozhat például egy **DataRecognitionClient** , amely kérést küld egy egyéni végpont a következő módszerrel:

```csharp
public static DataRecognitionClient CreateDataClient(SpeeechRecognitionMode speechRecognitionMode, string language, string primaryOrSecondaryKey, **string url**);
```

A **your_subscriptionId** és **végponti URL-cím** tekintse meg az Előfizetés kulcs és a webes szoftvercsatornák URL-címe, a a **központi telepítési információk** lap.

A **AuthenticationUri** fogadhatnak jogkivonatot a hitelesítési szolgáltatás használt. Ezt az URI külön, ahogy az az alábbi példakód be kell állítani.

A mintakód bemutatja, hogyan használhatja a SDK:

```csharp
var dataClient = SpeechRecognitionServiceFactory.CreateDataClient(
  SpeechRecognitionMode.LongDictation,
  "en-us",
  "your_subscriptionId",
  "your_subscriptionId",
  "endpointURL");
// set the authorization Uri
dataClient.AuthenticationUri = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
```

> [!NOTE]
> Használata esetén **létrehozása** módszerek az SDK-ban, meg kell adnia az előfizetés-azonosító kétszer túl van terhelve mert a **létrehozása** módszerek.
>

Az egyéni beszéd szolgáltatás rövid és hosszú felismerés két különböző URL-címet használ. Mindkét szerepel a **központi telepítések** lap. A használni kívánt adott űrlap a megfelelő végpont URL-címet használni.

A különböző felismerés ügyfelek az egyéni végponttal meghívása kapcsolatos további információkért tekintse meg a [SpeechRecognitionServiceFactory](https://www.microsoft.com/cognitive-services/Speech-api/documentation/GetStarted/GetStartedCSharpDesktop) osztály. A dokumentáció ezen az oldalon akusztikus modell kiigazítása hivatkozik, de az egyéni beszéd szolgáltatás használatával létrehozott összes végpontok vonatkozik.

## <a name="send-requests-by-using-the-speech-protocol"></a>Kérelmek küldése a beszédfelismerés protokoll használatával

A végpontok látható a [beszéd protokoll](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/websocketprotocol) olyan végpontok, a forrás webes szoftvercsatorna beszéd protokollhoz.

A csak hivatalos ügyfélmegvalósítással kaphatók jelenleg a [JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript). Ha azt szeretné, kezdje a minta, feltéve, hogy, a kód a következő módosításokat, és újra létre a minta:

1. A _src\sdk\speech.browser\SpeechConnectionFactory.ts_, a gazdagép neve "wss://speech.platform.bing.com" cserélje le a gazdagép neve a központi telepítés részleteit megjelenítő oldalon látható. Ne jelölje be a teljes URI itt azonban csak a *wss* protokoll rendszer és az állomás neve. Példa:

    ```JavaScript
    private get Host(): string {
        return Storage.Local.GetOrAdd("Host", "wss://<your_key_goes_here>.api.cris.ai");
    }
    ```

2. Állítsa be a _recognitionMode_ paraméterének _samples\browser\Samples.html_ a követelményeknek megfelelően:
    * _RecognitionMode.Interactive_ támogatja kérelmek legfeljebb 15 másodperc.
    * _RecognitionMode.Conversation_ és _RecognitionMode.Dictation_ (mindkettő egyenértékű egyéni beszéd szolgáltatásban) támogatása akár 10 percet igényel.

3. A minta létrehozása a "gulp build" használatba vétel előtt.

> [!NOTE]
> Győződjön meg arról, hogy a helyes URI ehhez a protokollhoz. A szükséges rendszer *wss* (nem *http* hasonlóan az ügyfél protokoll). 

További információkért lásd: a [Bing Diktálásfelismerési API](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedclientlibraries) dokumentációját.

## <a name="send-requests-by-using-http"></a>Kérelmek küldése a HTTP-n keresztül

Az egyéni végpontjának a HTTP post kérést küld hasonlít a HTTP-alapú kérést küld az kognitív szolgáltatások Bing Diktálásfelismerési API-t. Módosítsa az URL-cím, az egyéni telepítési címét megfelelően.

Nincsenek a kognitív szolgáltatások beszéd végpont és az ezzel a szolgáltatással létrehozott egyéni végpontokat a HTTP Protokollon keresztül küldött kérelmeket a bizonyos korlátozások vonatkoznak. A HTTP-kérelem nem adhat vissza részleges eredmények a felismerési folyamat során. A kérelmek időtartama pedig legfeljebb 10 másodperces tartalmát, és átfogó 14 másodperc.

Post kérelem létrehozásához kövesse az kognitív szolgáltatások Diktálásfelismerési API-t használja.

1. Olyan hozzáférési jogkivonatot elvégezni az előfizetés-azonosító. Ez a token a felismerési végpont eléréséhez szükséges. 10 percig felhasználhatók.

    ```
    curl -X POST --header "Ocp-Apim-Subscription-Key:<subscriptionId>" --data "" "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    ```
      A **subscriptionId** kell megadni az előfizetés-azonosító megadása a központi telepítéshez használja. A rendszer a egyszerű jogkivonat a következő kérés van szüksége a választ.

2. A végpontnak hang küldje újra a FELADÁS egy vagy több.

    ```
    curl -X POST --data-binary @example.wav -H "Authorization: Bearer <token>" -H "Content-Type: application/octet-stream" "<https_endpoint>"
    ```

    A **token** van a kapott hozzáférési jogkivonat segítségével az előző hívása. A **https_endpoint** a egyéni beszéd-szöveg-végpontot, jelenik meg a teljes címe a **központi telepítési információk** lap.

További információ a HTTP post-paraméterek és a válasz formátumát: a [Microsoft kognitív szolgáltatások Bing Diktálásfelismerési HTTP API](https://www.microsoft.com/cognitive-services/speech-api/documentation/API-Reference-REST/BingVoiceRecognition#SampleImplementation).

## <a name="send-requests-by-using-the-service-library"></a>Kérelmek küldése a szolgáltatás-könyvtár használatával
A Service Library lehetővé teszi, hogy a szolgáltatást, hogy a Microsoft Speech írjanak elő felhőalapú szóbeli nyelvi szöveggé alakítani a valós idejű állapotának ellenőrzéséhez, hogy az ügyfélalkalmazás hang küldhet és fogadhat részleges felismerési eredmények biztonsági egyidejűleg és aszinkron módon. SDK-szolgáltatás részletes található [Itt](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedcsharpservicelibrary)

> [!NOTE]
> A szolgáltatás szalagtár használata esetén módosítania kell az URI-azonosítója a engedélyezési szolgáltató végrehajtásának **IAuthorizationProvider** való https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken.

## <a name="next-steps"></a>További lépések
* A pontosság növeléséhez a [egyéni akusztikus modell](cognitive-services-custom-speech-create-acoustic-model.md).
* A pontosság növeléséhez egy [egyéni nyelvi modell](cognitive-services-custom-speech-create-language-model.md).
