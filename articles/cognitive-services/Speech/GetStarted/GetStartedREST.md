---
title: Ismerkedés a Bing Speech Recognition API REST használatával |} A Microsoft Docs
titlesuffix: Azure Cognitive Services
description: A REST használata a Microsoft Cognitive Services a beszélt hangot képes szöveggé alakítani a Speech Recognition API eléréséhez.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: a9f74f4032a78ee51ea2a8f020cd1418bb3330ca
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49345356"
---
# <a name="quickstart-use-the-bing-speech-recognition-rest-api"></a>Gyors útmutató: A Bing Speech recognition REST API használata

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

A Bing Speech felhő alapú szolgáltatás a beszélt hangot képes szöveggé alakítani a REST API használatával is fejleszthet alkalmazásokat.

## <a name="prerequisites"></a>Előfeltételek

### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Fizessen elő a beszédfelismerő API és a egy ingyenes próba-előfizetését kulcs lekérése

A beszédfelismerő API a Cognitive Services-(korábban Project Oxford) részét képezi. Ingyenes próba-előfizetését helyenk beszerezheti a [Cognitive Services-előfizetés](https://azure.microsoft.com/try/cognitive-services/) lapot. Miután kiválasztotta a beszédfelismerő API, válassza ki a **API-kulcs lekérése** a kulcs beszerzése. Egy elsődleges és másodlagos kulcsát adja vissza. Mindkét kulcsot kvóta, így használhatja az egyiket sem vannak társítva.

> [!IMPORTANT]
>* Előfizetési kulcs lekérése. A REST API eléréséhez, rendelkeznie kell egy [előfizetési kulcs](https://azure.microsoft.com/try/cognitive-services/).
>
>* Az előfizetési kulcs használja. Az alábbi REST minta cserélje le a saját előfizetői YOUR_SUBSCRIPTION_KEY. 
>
>* Tekintse meg a [hitelesítési](../how-to/how-to-authentication.md) egy előfizetési kulcsot beszerzése oldalon.

### <a name="prerecorded-audio-file"></a>Korábban rögzített hangfájl

Ebben a példában a rögzített hangfájl megmutatják, hogyan használható a REST API-t használjuk. Jegyezze fel a saját magának egy rövid kifejezés közli, hogy a hangfájl. Tegyük fel például, "Mi az például a mai időjárás?" vagy "Talált vicces filmek megtekintéshez." A beszédfelismerő API támogatja a külső mikrofon bemeneti is.

> [!NOTE]
> A példában szükséges a WAV fájlba rögzíti a hang **PCM egyetlen csatornát (mono), 16 KHz**.

## <a name="build-a-recognition-request-and-send-it-to-the-speech-recognition-service"></a>Elismerési kérelem létrehozása, és küldje el a speech recognition szolgáltatáshoz

A következő lépéssel, beszédfelismerés, hogy egy POST kérést küld a megfelelő kérelem fejléce és a törzs Speech HTTP-végpontokat.

### <a name="service-uri"></a>Szolgáltatás URI-ja

A speech recognition service URI van definiálva alapján [felismerés módok](../concepts.md#recognition-modes) és [nyelveket](../concepts.md#recognition-languages):

```HTTP
https://speech.platform.bing.com/speech/recognition/<RECOGNITION_MODE>/cognitiveservices/v1?language=<LANGUAGE_TAG>&format=<OUTPUT_FORMAT>
```

`<RECOGNITION_MODE>` a beszédfelismerést mód és musí mít jednu z následujících hodnot: `interactive`, `conversation`, vagy `dictation`. Egy szükséges erőforrás elérési útja az URI-ban. További információkért lásd: [felismerés módok](../concepts.md#recognition-modes).

`<LANGUAGE_TAG>` az egyik kötelező paraméter a lekérdezési karakterláncban. Azt határozza meg a Célnyelv számára audiotartalmak átalakítás: például `en-US` az angol (Egyesült Államok). További információkért lásd: [nyelveket](../concepts.md#recognition-languages).

`<OUTPUT_FORMAT>` egy nem kötelező paraméter a lekérdezési karakterláncban. Az engedélyezett értékek a következők `simple` és `detailed`. Alapértelmezés szerint a szolgáltatás az eredményeket ad vissza `simple` formátumban. További információkért lásd: [kimeneti formátum](../concepts.md#output-format).

Néhány példa a szolgáltatás URI-k az alábbi táblázatban láthatók.

| Felismerési mód  | Nyelv | Kimeneti formátum | Szolgáltatás URI-ja |
|---|---|---|---|
| `interactive` | pt-BR | Alapértelmezett | https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| `conversation` | hu-HU | Részletes |https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed |
| `dictation` | FR-FR | Egyszerű | https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR&format=simple |

> [!NOTE]
> A szolgáltatás-URI csak akkor, ha az alkalmazás a REST API-k használatával meghívja a speech recognition service van szükség. Ha valamelyik használja a [klienskódtárak](GetStartedClientLibraries.md), általában nem kell tudni, hogy melyik URI-t használja. A klienskódtárak előfordulhat, hogy használhatja másik szolgáltatás URI-k, amelyek csak egy adott ügyféloldali kódtár alkalmazható. További információkért tekintse meg az ügyféloldali kódtár a választott.

### <a name="request-headers"></a>Kérelemfejlécek

A következő mezőket a a kérelem fejlécében kell megadni:

- `Ocp-Apim-Subscription-Key`: Minden alkalommal, amikor a szolgáltatást hívják meg át kell az előfizetési kulcs található a `Ocp-Apim-Subscription-Key` fejléc. Beszédszolgáltatás is támogatja az engedélyezési jogkivonatok előfizetési kulcsok helyett. További információkért lásd: [hitelesítési](../How-to/how-to-authentication.md).
- `Content-type`: A `Content-type` mező formátumát és a hang Stream kodek ismerteti. Jelenleg csak WAV fájlt, és a PCM Mono 16000 kódolást támogatja. A Content-type érték ehhez a formátumhoz `audio/wav; codec=audio/pcm; samplerate=16000`.

A `Transfer-Encoding` mező kitöltése nem kötelező. Ha ez a mező `chunked`, akkor is a hanganyag részeket felosztása kisebb tömbökre. További információkért lásd: [darabolásos átvitel](../How-to/how-to-chunked-transfer.md).

Az alábbiakban látható egy minta-kérelem fejléce:

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="send-a-request-to-the-service"></a>A szolgáltatás-kérés küldése

Az alábbi példa bemutatja, hogyan speech recognition kérést küldhet a Speech REST-végpontokat. Használja a `interactive` mód.

> [!NOTE]
> Cserélje le `YOUR_AUDIO_FILE` a korábban rögzített hangfájl az elérési útját. Cserélje le `YOUR_SUBSCRIPTION_KEY` a saját előfizetés-kulccsal.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/Powershell)

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

# <a name="curltabcurl"></a>[A curl](#tab/curl)

A példában a curl Linux bash-környezet. Ha nem érhető el a platformon, szüksége lehet a curl telepítéséhez. A példában is működik a Cygwin a Windows, a Git Bash, a zsh és más ismertetése.

> [!NOTE]
> Tartsa a `@` cseréjekor hang fájl neve előtt `YOUR_AUDIO_FILE` az elérési útját a korábban rögzített hangfájl, ahogy azt jelzi, hogy értékét `--data-binary` adatok helyett egy fájl neve.

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

# <a name="ctabcsharp"></a>[C#](#tab/CSharp)

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

## <a name="process-the-speech-recognition-response"></a>A speech recognition válasz feldolgozása

A kérés feldolgozása után beszédszolgáltatás adja vissza a válasz JSON-formátumban.

> [!NOTE]
> Ha az előző kód hibát jelez, tekintse meg a [hibaelhárítás](../troubleshooting.md) keresse meg a lehetséges oka.

A következő kódrészlet azt szemlélteti, hogyan olvashatja a válasz az adatfolyamból.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/Powershell)

```Powershell
# show the response in JSON format
ConvertTo-Json $RecoResponse
```

# <a name="curltabcurl"></a>[A curl](#tab/curl)

Ebben a példában a curl közvetlenül a karakterlánc a válaszüzenetben adja vissza. Ha azt szeretné, annak megjelenítéséhez JSON formátumban, használhatja a további eszközök, például a jq.

```
curl -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE | jq
```

# <a name="ctabcsharp"></a>[C#](#tab/CSharp)

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

A következő mintát a következő JSON-választ:

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

A REST API vannak bizonyos korlátai:

- Hang stream támogatja csak legfeljebb 15 másodperc.
- Elismerés során nem támogatja a köztes eredményeket. A felhasználók csak a végső felismerés eredményét kapnak.

Ezek a korlátozások eltávolításához használja a Speech [klienskódtárak](GetStartedClientLibraries.md). Vagy használhatja a közvetlenül a [Speech WebSocket protokoll](../API-Reference-REST/websocketprotocol.md).

## <a name="whats-next"></a>A következő lépések

- A C#, Java, stb. a REST API használatával, olvassa el ezeket [mintaalkalmazások](../samples.md).
- Keresse meg és javítsa a hibákat: [hibaelhárítás](../troubleshooting.md).
- Összetettebb funkciók használatához tekintse meg az első lépések a beszédfelismerő [klienskódtárak](GetStartedClientLibraries.md).

### <a name="license"></a>Licenc

Az összes Cognitive Services SDK-k és minták az MIT-licenccel rendelkező rendelkezik licenccel. További információkért lásd: [licenc](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
