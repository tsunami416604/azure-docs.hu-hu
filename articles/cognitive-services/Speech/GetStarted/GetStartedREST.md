---
title: Ismerkedés a Microsoft beszéd felismerés API REST használatával |} Microsoft Docs
description: Használja a többi kognitív Microsoft-szolgáltatások szóbeli hang szöveggé alakítani a beszéd-felismerési API eléréséhez.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 53785cdfd75c23910802f2be20e6305817b3b097
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347463"
---
# <a name="get-started-with-speech-recognition-by-using-the-rest-api"></a>A REST API használatával beszédfelismerés használatába

Beszéd szolgáltatással felhőalapú alkalmazásokat fejleszthet szóbeli hang szöveggé alakítani a REST API használatával.

## <a name="prerequisites"></a>Előfeltételek

### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Fizessen elő a hang transzformációs API-t, és egy ingyenes próba-előfizetés kulcs beszerzése

A Diktálásfelismerési API (korábban projekt Oxford) kognitív szolgáltatások részét képezi. Ingyenes próba-előfizetés kulcsokat beszerezheti a [kognitív szolgáltatások előfizetés](https://azure.microsoft.com/try/cognitive-services/) lap. Miután kiválasztotta a hang transzformációs API-t, jelölje ki a **API-kulcs beolvasása** a kulcs beszerzése. Egy elsődleges és másodlagos kulcsot adja vissza. Mindkét kulcsot, vagy kulccsal a azonos kvóta vannak társítva.

> [!IMPORTANT]
>* Egy előfizetés kulcs beszerzése. A REST API próbál hozzáférni, rendelkeznie kell egy [előfizetés kulcs](https://azure.microsoft.com/try/cognitive-services/).
>
>* Az előfizetés-kulcsot használ. A következő REST-mintákat cserélje le a saját előfizetés kulcs YOUR_SUBSCRIPTION_KEY. 
>
>* Tekintse meg a [hitelesítési](../how-to/how-to-authentication.md) lapján az Előfizetés-kulcs beszerzése.

### <a name="prerecorded-audio-file"></a>Korábban rögzített hangfájl

Ebben a példában használjuk a rögzített hangfájl bemutatják, hogyan használható a REST API-t. Jegyezze fel a saját kezűleg közli, hogy rövid kifejezés hangfájl. Tegyük fel például, "Mi az az időjárási például ma?" vagy a "Bemutató vicces filmek talál." A beszédfelismerés API támogatja a külső mikrofon bemeneti is.

> [!NOTE]
> A példánál az szükséges, hogy a hang rögzítése a WAV-fájlként **PCM egy csatornán (monó), 16 KHz**.

## <a name="build-a-recognition-request-and-send-it-to-the-speech-recognition-service"></a>Build felismerés kérelmet, és a beszédfelismerés felismerés szolgáltatásnak

A következő lépés a beszédfelismerés az, hogy egy POST kérést küld a beszédfelismerés HTTP-végpontokról a megfelelő fejléc és a fő.

### <a name="service-uri"></a>Szolgáltatás URI-ja

A beszédfelismerés felismerési szolgáltatás URI van definiálva alapján [felismerés módok](../concepts.md#recognition-modes) és [nyelveket](../concepts.md#recognition-languages):

```HTTP
https://speech.platform.bing.com/speech/recognition/<RECOGNITION_MODE>/cognitiveservices/v1?language=<LANGUAGE_TAG>&format=<OUTPUT_FORMAT>
```

`<RECOGNITION_MODE>` Megadja a mód és a következő értékek egyike lehet: `interactive`, `conversation`, vagy `dictation`. A szükséges erőforrás URI elérési utat is. További információkért lásd: [felismerés módok](../concepts.md#recognition-modes).

`<LANGUAGE_TAG>` az egyik kötelező paraméter a lekérdezési karakterláncban. Meghatározza a cél nyelvi hang átalakításához: például `en-US` az angol (Egyesült Államok). További információkért lásd: [nyelveket](../concepts.md#recognition-languages).

`<OUTPUT_FORMAT>` nem kötelező paraméter a lekérdezési karakterláncban. Az engedélyezett értékek a következők `simple` és `detailed`. Alapértelmezés szerint a szolgáltatás az eredményeket ad vissza `simple` formátumban. További információkért lásd: [kimeneti formátum](../concepts.md#output-format).

Néhány példa a szolgáltatás URI-azonosítók a következő táblázatban láthatók.

| Mód  | Nyelv | Kimeneti formátum | Szolgáltatás URI-ja |
|---|---|---|---|
| `interactive` | pt-BR | Alapértelmezett | https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| `conversation` | hu-HU | Részletes |https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed |
| `dictation` | FR-FR | Egyszerű | https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR&format=simple |

> [!NOTE]
> A szolgáltatás URI csak akkor, ha az alkalmazás REST API-k használatával meghívja a beszédfelismerés felismerési szolgáltatást van szükség. Ha valamelyik használja a [klienskódtárak](GetStartedClientLibraries.md), általában nem kell tudni, hogy mely URI azonosító. A klienskódtárak segítségével használhatja a különböző szolgáltatás URI-azonosítók, amelyek csak egy adott ügyféloldali kódtár alkalmazható. További információkért tekintse meg az ügyféloldali kódtár az Ön által választott.

### <a name="request-headers"></a>Kérelemfejlécek

A következő mezők a kérelem fejlécében kell beállítani:

- `Ocp-Apim-Subscription-Key`: Minden alkalommal, amikor a szolgáltatás hívása át kell adnia az Előfizetés kulcs a `Ocp-Apim-Subscription-Key` fejléc. Beszéd szolgáltatás is támogatja az engedélyezési jogkivonatok előfizetés kulcsok helyett. További információkért lásd: [hitelesítési](../How-to/how-to-authentication.md).
- `Content-type`: A `Content-type` mező formátuma és a hangadatfolyam kodek ismerteti. Jelenleg csak WAV-fájlok és PCM monó 16000 kódolás támogatott. A Content-type ezt a formátumot értéke `audio/wav; codec=audio/pcm; samplerate=16000`.

A `Transfer-Encoding` mező kitöltése nem kötelező. Ha ez a mező `chunked`, akkor is a hang kis adattömbökbe részeket. További információkért lásd: [darabolásos átviteli](../How-to/how-to-chunked-transfer.md).

A következő egy minta fejléc:

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="send-a-request-to-the-service"></a>A szolgáltatási kérelem küldése

A következő példa bemutatja, hogyan kérelmet kell küldenie a beszédfelismerés felismerés beszéd REST-végpontok. Használja a `interactive` mód.

> [!NOTE]
> Cserélje le `YOUR_AUDIO_FILE` az elérési útját a korábban rögzített hangfájl. Cserélje le `YOUR_SUBSCRIPTION_KEY` saját előfizetés kulccsal.

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

# <a name="curltabcurl"></a>[curl](#tab/curl)

A példa curl Linux bash együtt. Ha nem érhető el a platformon, szükség lehet a curl telepítéséhez. A példa is működik a Cygwin Windows, a Git bash eszközt, a zsh és az egyéb ismertetése.

> [!NOTE]
> Tartsa a `@` cseréjekor hang fájl neve elé `YOUR_AUDIO_FILE` fájl elérési útját a korábban rögzített hang, ahogy azt jelzi, hogy értékének `--data-binary` egy fájlnév adatok helyett.

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

## <a name="process-the-speech-recognition-response"></a>A beszédfelismerés felismerés válasz feldolgozása

A kérelem feldolgozása után beszéd szolgáltatás eredményeket ad vissza a választ JSON formátumban.

> [!NOTE]
> Ha az előző kód hibát ad vissza, lásd: [hibaelhárítás](../troubleshooting.md) lehetséges oka kereséséhez.

A következő kódrészletet azt szemlélteti, hogyan érheti el a válasz az adatfolyamból.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/Powershell)

```Powershell
# show the response in JSON format
ConvertTo-Json $RecoResponse
```

# <a name="curltabcurl"></a>[curl](#tab/curl)

Ebben a példában a curl közvetlenül egy válaszüzenetben adja vissza. Ha szeretné szerepel, hogy a JSON formátumban, további eszközök, például jq is használhatja.

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

Az alábbi minta egy JSON-válasz:

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

A REST API bizonyos korlátozásokkal rendelkezik:

- Támogatja a hangadatfolyam csak legfeljebb 15 másodperc.
- Nem támogatja a köztes eredmények felismerés során. Csak a végső felismerési eredmények a felhasználók megkapják.

Ezek a korlátozások eltávolításához használja a beszédfelismerés [klienskódtárak](GetStartedClientLibraries.md). Vagy dolgozhat közvetlenül a [beszéd WebSocket protokoll](../API-Reference-REST/websocketprotocol.md).

## <a name="whats-next"></a>A következő lépések

- A C#, Java, stb. a REST API használatával, olvassa el ezeket [mintaalkalmazást](../samples.md).
- Keresse meg és javítsa a hibákat, [hibaelhárítás](../troubleshooting.md).
- Összetettebb funkciók használatához tekintse meg az első lépések a beszédfelismerés [klienskódtárak](GetStartedClientLibraries.md).

### <a name="license"></a>Licenc

Minden kognitív Services SDK-k és minták a MIT licenccel rendelkező licencét. További információkért lásd: [licenc](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
