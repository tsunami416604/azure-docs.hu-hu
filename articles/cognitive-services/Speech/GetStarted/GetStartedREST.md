---
title: Ismerkedés a Bing Speech felismerési API-val REST használatával | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: A REST használatával férhet hozzá a Speech Recognition API-hoz Microsoft Cognitive Services a beszélt hang szöveggé alakításához.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e962a12c6c27737f95e78e80036e51bac41147d5
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965786"
---
# <a name="quickstart-use-the-bing-speech-recognition-rest-api"></a>Gyors útmutató: A Bing Speech felismerés használata REST API

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

A felhőalapú Bing Speech szolgáltatással olyan alkalmazásokat fejleszthet, amelyekkel a REST API segítségével konvertálhatja a beszélt hangot szövegre.

## <a name="prerequisites"></a>Előfeltételek

### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Feliratkozás a Speech API-ra, és ingyenes próbaverziós előfizetési kulcs beszerzése

A Speech API Cognitive Services (korábban Project Oxford) része. A [Cognitive Services előfizetés](https://azure.microsoft.com/try/cognitive-services/) oldaláról ingyenes próbaverziós előfizetési kulcsokat szerezhet be. Miután kiválasztotta a Speech API-t, válassza az **API-kulcs beolvasása** elemet a kulcs lekéréséhez. Egy elsődleges és egy másodlagos kulcsot ad vissza. Mindkét kulcs ugyanahhoz a kvótához van kötve, így bármelyik kulcsot használhatja.

> [!IMPORTANT]
>* Előfizetési kulcs beszerzése. A REST API elérése előtt rendelkeznie kell egy [előfizetési kulccsal](https://azure.microsoft.com/try/cognitive-services/).
>
>* Használja az előfizetési kulcsot. A következő REST-mintákban cserélje le a YOUR_SUBSCRIPTION_KEY-et a saját előfizetési kulcsára.
>
>* Az előfizetési kulcs beszerzéséhez tekintse meg a [hitelesítés](../how-to/how-to-authentication.md) lapot.

### <a name="prerecorded-audio-file"></a>Előre rögzített hangfájl

Ebben a példában egy rögzített hangfájlt használunk a REST API használatának szemléltetésére. Rögzítsen egy hangfájlt egy rövid kifejezéssel. Tegyük fel például, hogy "mi a mai Időjárás?" vagy "keresse meg a vicces filmeket a megtekintéshez". A Speech Recognition API a külső mikrofon bemenetét is támogatja.

> [!NOTE]
> A példához az szükséges, hogy a hanganyag WAV-fájlként legyen rögzítve **PCM Single Channel (mono), 16 kHz**.

## <a name="build-a-recognition-request-and-send-it-to-the-speech-recognition-service"></a>Hozzon létre egy felismerési kérést, és küldje el a beszédfelismerési szolgáltatásnak

A beszédfelismerés következő lépése egy POST-kérelem küldése a beszédfelismerési HTTP-végpontoknak a megfelelő kérelem fejlécével és Törzsével.

### <a name="service-uri"></a>Szolgáltatás URI-ja

A beszédfelismerési szolgáltatás URI-ja a [felismerési módok](../concepts.md#recognition-modes) és a [felismerési nyelvek](../concepts.md#recognition-languages)alapján van definiálva:

```HTTP
https://speech.platform.bing.com/speech/recognition/<RECOGNITION_MODE>/cognitiveservices/v1?language=<LANGUAGE_TAG>&format=<OUTPUT_FORMAT>
```

`<RECOGNITION_MODE>`meghatározza a felismerési módot, és az alábbi értékek egyikének kell `interactive`lennie: `dictation`, `conversation`, vagy. Ez egy szükséges erőforrás-útvonal az URI-ban. További információ: [felismerési módok](../concepts.md#recognition-modes).

`<LANGUAGE_TAG>`egy kötelező paraméter a lekérdezési karakterláncban. Meghatározza a hangátalakítás céljának nyelvét: `en-US` például angol (Egyesült Államok). További információ: [felismerési nyelvek](../concepts.md#recognition-languages).

`<OUTPUT_FORMAT>`egy opcionális paraméter a lekérdezési karakterláncban. Az engedélyezett értékek `simple` : és `detailed`. Alapértelmezés szerint a szolgáltatás `simple` formátumban adja vissza az eredményeket. További információ: [kimeneti formátum](../concepts.md#output-format).

A következő táblázatban néhány példa a szolgáltatási URI-ra.

| Felismerési mód  | Nyelv | Kimeneti formátum | Szolgáltatás URI-ja |
|---|---|---|---|
| `interactive` | pt-BR | Alapértelmezett | https:\//Speech.platform.Bing.com/Speech/Recognition/Interactive/cognitiveservices/v1?Language=pt-br |
| `conversation` | en-US | Részletes | https:\//Speech.platform.Bing.com/Speech/Recognition/Conversation/cognitiveservices/v1?Language=en-us&Format=Detailed |
| `dictation` | FR-FR | Egyszerű | https:\//Speech.platform.Bing.com/Speech/Recognition/dictation/cognitiveservices/v1?language=fr-fr&Format=Simple |

> [!NOTE]
> A szolgáltatás URI-ja csak akkor szükséges, ha az alkalmazás REST API-kat használ a beszédfelismerési szolgáltatás meghívásához. Ha az egyik [ügyféloldali kódtárat](GetStartedClientLibraries.md)használja, általában nem kell tudnia, hogy melyik URI-t használja. Az ügyféloldali kódtárak más szolgáltatás-URI-ket is használhatnak, amelyek csak egy adott ügyfél-függvénytárra érvényesek. További információkért tekintse meg az Ön által választott ügyféloldali könyvtárat.

### <a name="request-headers"></a>Kérelemfejlécek

A kérelem fejlécében a következő mezőket kell megadni:

- `Ocp-Apim-Subscription-Key`: Minden alkalommal, amikor meghívja a szolgáltatást, át kell adnia az előfizetési kulcsot `Ocp-Apim-Subscription-Key` a fejlécben. A beszédfelismerési szolgáltatás az előfizetési kulcsok helyett az engedélyezési jogkivonatok átadását is támogatja. További információért lásd: [Hitelesítés](../How-to/how-to-authentication.md).
- `Content-type`: A `Content-type` mező a hangadatfolyam formátumát és kodekét írja le. Jelenleg csak a WAV-fájl és a PCM monó 16000-kódolás támogatott. A formátum `audio/wav; codec=audio/pcm; samplerate=16000`tartalomtípus értéke:.

A `Transfer-Encoding` mező kitöltése nem kötelező. Ha ezt a mezőt a `chunked`értékre állítja, a hanganyagot kis adattömbökbe is felvágja. További információ: [darabolásos átvitel](../How-to/how-to-chunked-transfer.md).

A következő egy példa a kérelem fejlécére:

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="send-a-request-to-the-service"></a>Kérelem küldése a szolgáltatásnak

Az alábbi példa bemutatja, hogyan küldhet beszédfelismerési kérést a beszédfelismerési REST-végpontoknak. A `interactive` felismerési módot használja.

> [!NOTE]
> Cserélje le `YOUR_AUDIO_FILE` a korábban rögzített hangfájl az elérési útját. Cserélje `YOUR_SUBSCRIPTION_KEY` le a t a saját előfizetési kulcsára.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authorization token returned by the token service.
$RecoRequestHeader = @{
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY';
  'Transfer-Encoding' = 'chunked';
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[Curl](#tab/curl)

A példa a curlot használja a Linuxon a bash használatával. Ha nem érhető el a platformon, lehet, hogy telepítenie kell a curlot. A példa a Cygwin on Windows, a git bash, a zsh és más rendszerhéjok esetében is működik.

> [!NOTE]
> Az előre `@` rögzített hangfájl elérési útját `YOUR_AUDIO_FILE` lecserélve tartsa meg a hangfájl neve előtt, mivel azt `--data-binary` jelzi, hogy az érték az adatok helyett fájlnév.

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

// Send an audio file by 1024 byte chunks
using (FileStream fs = new FileStream(YOUR_AUDIO_FILE, FileMode.Open, FileAccess.Read))
{

    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```

---

## <a name="process-the-speech-recognition-response"></a>A beszédfelismerési válasz feldolgozása

A kérelem feldolgozása után a beszédfelismerési szolgáltatás JSON formátumú válaszként adja vissza az eredményeket.

> [!NOTE]
> Ha az előző kód hibát ad vissza, tekintse meg a lehetséges okokat a [Hibaelhárítás](../troubleshooting.md) című témakörben.

Az alábbi kódrészlet egy példát mutat be arra, hogyan olvashatja el a válaszát az adatfolyamból.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell
# show the response in JSON format
ConvertTo-Json $RecoResponse
```

# <a name="curltabcurl"></a>[Curl](#tab/curl)

Ebben a példában a curl közvetlenül visszaadja a válaszüzenetet egy karakterláncban. Ha JSON formátumban szeretné megjeleníteni, további eszközöket is használhat, például jQ.

```
curl -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE | jq
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
/*
* Get the response from the service.
*/
Console.WriteLine("Response:");
using (WebResponse response = request.GetResponse())
{
    Console.WriteLine(((HttpWebResponse)response).StatusCode);

    using (StreamReader sr = new StreamReader(response.GetResponseStream()))
    {
        responseString = sr.ReadToEnd();
    }

    Console.WriteLine(responseString);
    Console.ReadLine();
}
```

---

A következő minta egy JSON-Válasz:

```json
OK
{
  "RecognitionStatus": "Success",
  "Offset": 22500000,
  "Duration": 21000000,
  "NBest": [{
    "Confidence": 0.941552162,
    "Lexical": "find a funny movie to watch",
    "ITN": "find a funny movie to watch",
    "MaskedITN": "find a funny movie to watch",
    "Display": "Find a funny movie to watch."
  }]
}
```

## <a name="limitations"></a>Korlátozások

A REST API korlátozásai:

- Csak 15 másodpercig támogatja a hangadatfolyamot.
- Az elismerés során nem támogatja a köztes eredményeket. A felhasználók csak a végső felismerés eredményét kapják meg.

A korlátozások eltávolításához használja a beszédfelismerési [ügyféloldali kódtárakat](GetStartedClientLibraries.md). Vagy közvetlenül is dolgozhat a [Speech WebSocket protokollal](../API-Reference-REST/websocketprotocol.md).

## <a name="whats-next"></a>További teendők

- Ha szeretné megtudni, hogyan használhatja a REST API C#-t, javát stb., tekintse meg ezeket a [példákat](../samples.md).
- A hibák megkereséséhez és javításához lásd: [Hibaelhárítás](../troubleshooting.md).
- További speciális funkciók használatához tekintse meg a beszédfelismerési [ügyféloldali kódtárak](GetStartedClientLibraries.md)használatának első lépéseit ismertető témakört.

### <a name="license"></a>Licenc

Az SDK-k és a minták minden Cognitive Services licence az MIT licenccel rendelkezik. További információ: [License](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
