---
title: Egyéni beszédmodell végpont – Custom Speech Service használatára
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogy egy hang-szöveg transzformációs egyéni végpont használata a Custom Speech Service.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 98c1b58e58490199b0258dfcc8df183c3fe9a8bd
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223372"
---
# <a name="use-a-custom-speech-to-text-endpoint"></a>Egyéni beszédfelismerési végpont használata
Akkor is végpontra kérést küldhet egy Azure Custom Speech Service hang-szöveg transzformációs, hasonló módon, mint az alapértelmezett a Cognitive Services beszédfelismerő végpontra. Ezeket a végpontokat funkcionálisan megegyeznek a beszédfelismerő API alapértelmezett végpontjait. Ugyanazokat a funkciókat, amelyek az ügyféloldali kódtár vagy a REST API, beszédfelismerő API-n keresztül érhető el, az egyéni végpont is érhető el.

A végpontok a szolgáltatás használatával hoz létre egyidejű kéréseket a különböző számú tud feldolgozni. A kötet attól függ, hogy az előfizetéséhez tartozó tarifacsomagot. Ha túl sok kérelem érkezik, akkor hiba történik. Az ingyenes csomag esetében a havi kérelmek.

A szolgáltatás azt feltételezi, hogy valós időben továbbított adatok. Gyorsabban továbbítja, ha a kérés minősül, amíg a valós idejű hang időtartam letelte.

> [!NOTE]
> Támogatjuk a [új websockets](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/websocketprotocol) még. Ha azt tervezi, az egyéni beszéd-végpont használata a web sockets, az alábbi utasításokat.
>
> Az új [REST API-val](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest) támogatása hamarosan elérhető lesz. Ha azt tervezi, hívja meg az egyéni beszédfelismerési végpont HTTP-n keresztül, az alábbi utasításokat.
>

## <a name="send-requests-by-using-the-speech-client-library"></a>Kérelmek küldése a beszédfelismerő ügyfélkódtárának használatával

Az egyéni végpontra kérést küldhet a beszédfelismerő ügyfélkódtárának használatával, indítsa el a felismerés ügyfél. Az ügyfél SDK-t beszédet használhat a [NuGet](http://nuget.org/). Keresse meg *beszédfelismerés*, és válassza ki a speech recognition csomag a Microsoft a platformhoz. Néhány mintakódját találhatók [GitHub](https://github.com/Microsoft/Cognitive-Speech-STT-Windows). Az ügyfél beszéd SDK egy gyári osztályt biztosít a **SpeechRecognitionServiceFactory**, amely az alábbi módszereket kínál:

  *   ```CreateDataClient(...)```: A felismerés ügyfél.
  *   ```CreateDataClientWithIntent(...)```: Adatok felismerés rendelkező ügyfelet szándékot.
  *   ```CreateMicrophoneClient(...)```: A mikrofon felismerés ügyfél.
  *   ```CreateMicrophoneClientWithIntent(...)```: Egy mikrofon felismerése szándékot az ügyfél.

Részletes dokumentációjáért lásd: a [Bing Speech API](https://docs.microsoft.com/azure/cognitive-services/speech/home). A Custom Speech Service végpontok szolgáltatásokhoz közös SDK támogatja.

Az adatok felismerés ügyfél megfelelő beszédfelismerési adatokból, például egy fájl vagy más hangforrásról. A mikrofon felismerés ügyfél alkalmas a Beszédfelismerés a mikrofon. Leképezés vagy-ügyfél használatát strukturált szándék eredményeinek térhet vissza a [Language Understanding Intelligent Service](https://www.luis.ai/) (LUIS), ha létrehozott egy LUIS-alkalmazást a forgatókönyvhöz.

Négy típusú ügyfelek kétféle módon lehet létrehozni. Az első konfigurációja a standard szintű Cognitive Services beszédfelismerő API használja. A második módszer lehetővé teszi egy URL-címet, amely megfelel a Custom Speech Service segítségével létrehozott egyéni végpontra.

Létrehozhat például egy **DataRecognitionClient** , amely kérelmet küld az egyéni végponthoz a következő módon:

```csharp
public static DataRecognitionClient CreateDataClient(SpeeechRecognitionMode speechRecognitionMode, string language, string primaryOrSecondaryKey, **string url**);
```

A **your_subscriptionId** és **endpointURL** tekintse meg az előfizetési kulcsot és a web sockets URL-cím, az a **központi telepítési információk** lap.

A **AuthenticationUri** fogadhatnak jogkivonatot a hitelesítési szolgáltatás szolgál. Ez az URI, ahogyan az alábbi mintakód külön kell beállítani.

A mintakód bemutatja, hogyan használja az ügyfél-SDK:

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
> Használata esetén **létrehozás** módszerek az SDK-ban, meg kell adnia az előfizetés-Azonosítót a túlterhelés miatt kétszer a **létrehozás** módszereket.
>

A Custom Speech Service rövid és hosszú felismerése két különböző URL-címet használ. Mindkettő szerepel a **központi telepítések** lapot. A megfelelő végpontra URL-cím használata az adott űrlap létrehozásához használni szeretne.

Egyéni végpontra a különböző felismerés ügyfeleket meghívása kapcsolatos további információkért lásd: a [SpeechRecognitionServiceFactory](https://www.microsoft.com/cognitive-services/Speech-api/documentation/GetStarted/GetStartedCSharpDesktop) osztály. A dokumentáció ezen az oldalon akusztikai modellbetanítás hivatkozik, de a Custom Speech Service segítségével létrehozott összes végpontokra vonatkozik.

## <a name="send-requests-by-using-the-speech-protocol"></a>Kérelmek küldése a Speech protokollal

A végpontok látható a [Speech protokoll](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/websocketprotocol) olyan végpontok, a forrás Web Socket Speech protokoll nevű nyílt.

Az ügyfél csak hivatalos végrehajtása jelenleg a [JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript). Ha azt szeretné, a következővel kell kezdődnie az itt elérhető mintát a következő módosításokat a kódot, és hozza létre újra a mintát:

1. A _src\sdk\speech.browser\SpeechConnectionFactory.ts_, a gazdagép neve "wss://speech.platform.bing.com" cserélje le a gazdagép nevét, a központi telepítés a részletek oldalán látható. Ne jelölje be a teljes URI itt azonban csak a *wss* protokoll sémát és a gazdagép nevét. Példa:

    ```JavaScript
    private get Host(): string {
        return Storage.Local.GetOrAdd("Host", "wss://<your_key_goes_here>.api.cris.ai");
    }
    ```

2. Állítsa be a _recognitionMode_ paraméter _samples\browser\Samples.html_ az igényeknek megfelelően:
    * _RecognitionMode.Interactive_ támogatja kérelmek legfeljebb 15 másodperc.
    * _RecognitionMode.Conversation_ és _RecognitionMode.Dictation_ (mindkettő egyenértékű a Custom Speech Service) támogatási kérések akár 10 percet.

3. A minta létrehozása a "build gulp használata szolgáltatásban" előtt.

> [!NOTE]
> Győződjön meg arról, hogy a megfelelő URI-t használja ezt a protokollt. A szükséges rendszer *wss* (nem *http* hasonlóan az ügyfél protokoll). 

További információkért lásd: a [Bing Speech API](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedclientlibraries) dokumentációját.

## <a name="send-requests-by-using-http"></a>Kérelmek küldése a HTTP protokoll használatával

Egyéni végpontra kérést küld egy HTTP post használatával a HTTP-alapú kérést küld a Cognitive Services a Bing Speech API hasonló. Módosítsa az URL-cím, hogy az egyéni üzembe helyezés a címet.

A Cognitive Services beszéd végpont és az ezzel a szolgáltatással létrehozott egyéni végpontok a HTTP Protokollon keresztül küldött kérelmeket a bizonyos korlátozások vonatkoznak. A HTTP-kérelem a felismerés közben nem adhat vissza részleges eredményeket. Ezenkívül a kérelmek időtartamát korlátozódik hanganyaga 10 másodpercet, és átfogó 14 másodperc.

Egy post-kérés létrehozásához kövesse a Cognitive Services beszédfelismerő API használja ugyanazt a folyamatot.

1. Az előfizetés-azonosító. a hozzáférési jogkivonat beszerzése Ez a token a végpontokon eléréséhez szükséges. 10 percig felhasználhatók.

    ```
    curl -X POST --header "Ocp-Apim-Subscription-Key:<subscriptionId>" --data "" "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    ```
      A **subscriptionId** kell megadni az előfizetés-Azonosítót használja a központi telepítéshez. A válasz a egyszerű token a következő kérelmet van szüksége.

2. A végpontnak hang közzététele POST újra használatával.

    ```
    curl -X POST --data-binary @example.wav -H "Authorization: Bearer <token>" -H "Content-Type: application/octet-stream" "<https_endpoint>"
    ```

    A **token** van az előző hívás a kapott hozzáférési jogkivonat. A **https_endpoint** egyéni hang-szöveg transzformációs végpontra, látható a teljes címe az **központi telepítési információk** lapot.

HTTP post-paramétereket és a válaszformátum kapcsolatos további információkért lásd: a [Microsoft Cognitive Services a Bing Speech HTTP API](https://www.microsoft.com/cognitive-services/speech-api/documentation/API-Reference-REST/BingVoiceRecognition#SampleImplementation).

## <a name="send-requests-by-using-the-service-library"></a>Kérelmek küldése a szolgáltatási kódtár használatával
A szolgáltatási kódtára lehetővé teszi, hogy a szolgáltatás használata a Microsoft Speech beszédátírási felhő a használja a beszélt nyelv szöveggé alakítani a valós idejű, így az ügyfélalkalmazás hang küldhet és fogadhat részleges felismerési eredményeket biztonsági párhuzamosan és aszinkron módon. A SDK-val – részletes nézet található [Itt](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedcsharpservicelibrary)

> [!NOTE]
> A szolgáltatási kódtár használata esetén módosítania kell az URI-ját az engedélyezési szolgáltató végrehajtásának **IAuthorizationProvider** való https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken.

## <a name="next-steps"></a>További lépések
* A pontosság növeléséhez a [importálni akusztikai modell](cognitive-services-custom-speech-create-acoustic-model.md).
* A pontosság növeléséhez egy [egyéni nyelvi modell](cognitive-services-custom-speech-create-language-model.md).
