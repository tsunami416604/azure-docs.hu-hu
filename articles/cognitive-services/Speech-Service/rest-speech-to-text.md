---
title: Beszéd-szöveg API hivatkozás (REST) – Beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használhatja a beszéd-szöveg REST API.Learn how to use the speech-to-text REST API. Ebben a cikkben megtudhatja, engedélyezési beállítások, lekérdezési beállítások, hogyan strukturálhatja a kérelmet, és választ kap.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: trbye
ms.openlocfilehash: fbb4d114d1fee21d7950e53b06fc16c96b5c930b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400181"
---
# <a name="speech-to-text-rest-api"></a>Diktálás REST API

A [beszédfelismerési SDK](speech-sdk.md)alternatívájaként a beszédfelismerési szolgáltatás lehetővé teszi a beszéd-szöveg konvertálást rest API használatával. Minden elérhető végpont egy régióhoz van társítva. Az alkalmazás hoz egy előfizetési kulcsot a végpont használni kívánt. A REST API nagyon korlátozott, és csak olyan esetekben használható, ha a [beszédskó SDK](speech-sdk.md) nem.

A beszédfelismerési REST API használata előtt a következőket értse meg:

* A REST API-t használó és közvetlenül továbbító kérések legfeljebb 60 másodpercnyi hangot tartalmazhatnak.
* A beszéd-szöveg REST API csak a végső eredményeket adja vissza. Részleges eredmények nem állnak rendelkezésre.

Ha hosszabb hang küldése az alkalmazás egyik követelménye, fontolja meg a [beszédfelismerési SDK](speech-sdk.md) vagy egy fájlalapú REST API használatát, például [a kötegelt átírást.](batch-transcription.md)

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Régiók és végpontok

A REST API végpontja a következő formátummal rendelkezik:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Cserélje `<REGION_IDENTIFIER>` le az előfizetés régiójának megfelelő azonosítóra ebből a táblából:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> A nyelvi paramétert hozzá kell fűzni az URL-címhez, hogy ne kapjon 4xx HTTP-hibát. Az USA nyugati végpontját használó amerikai angol nyelv `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`például a következő: .

## <a name="query-parameters"></a>Lekérdezési paraméterek

Ezek a paraméterek szerepelhetnek a REST-kérelem lekérdezési karakterláncában.

| Paraméter | Leírás | Kötelező / Nem kötelező |
|-----------|-------------|---------------------|
| `language` | A felismert beszélt nyelvet azonosítja. Lásd: [Támogatott nyelvek](language-support.md#speech-to-text). | Kötelező |
| `format` | Megadja az eredményformátumot. Az elfogadott `simple` `detailed`értékek és a. Az egyszerű `RecognitionStatus` `DisplayText`eredmények `Offset`közé `Duration`tartozik a , , és . A részletes válaszok több találatot tartalmaznak megbízhatósági értékekkel és négy különböző ábrázolással. Az alapértelmezett beállítás: `simple`. | Optional |
| `profanity` | A káromkodás kezelésének módját határozza meg a felismerési eredményekben. Az elfogadott `masked`értékek a következők, amelyek a `removed`káromkodást csillagokkal helyettesítik, és eltávolítják az összes káromkodást az eredményből, vagy `raw`, amely magában foglalja a káromkodást az eredményben. Az alapértelmezett beállítás: `masked`. | Optional |
| `cid` | Ha az [egyéni beszédportálon](how-to-custom-speech.md) egyéni modelleket hoz létre, egyéni modelleket használhat a **Központi telepítés** lapon található **végpontazonosítójukon** keresztül. A **végpontazonosítót** használja a `cid` lekérdezési karakterlánc paraméter argumentumaként. | Optional |

## <a name="request-headers"></a>Kérésfejlécek

Ez a táblázat a beszéd-szöveg kérésekhez szükséges és választható fejléceket sorolja fel.

|Fejléc| Leírás | Kötelező / Nem kötelező |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | A beszédszolgáltatás-előfizetési kulcs. | Vagy ez `Authorization` a fejléc, vagy kötelező. |
| `Authorization` | Engedélyezési jogkivonat, amelyet `Bearer`a szó előz meg. További információért lásd: [Hitelesítés](#authentication). | Vagy ez `Ocp-Apim-Subscription-Key` a fejléc, vagy kötelező. |
| `Content-type` | A megadott hangadatok formátumát és kodekját ismerteti. Az elfogadott `audio/wav; codecs=audio/pcm; samplerate=16000` `audio/ogg; codecs=opus`értékek és a. | Kötelező |
| `Transfer-Encoding` | Itt adhatja meg, hogy a program egyetlen fájl helyett a tömbbel, de a tömbbel, a hangadatokkal való elküldött adatokat küldje el. Csak akkor használja ezt a fejlécet, ha hangadatokat darabol. | Optional |
| `Expect` | Ha darabolt átvitelt `Expect: 100-continue`használ, küldje el a ot. A beszédszolgáltatás nyugtázza a kezdeti kérést, és további adatokra vár.| Szükséges, ha darabolt hangadatokat küld. |
| `Accept` | Ha rendelkezésre áll, akkor azt meg kell. `application/json` A beszédfelismerési szolgáltatás json-ban biztosít eredményeket. Egyes kérési keretrendszerek nem kompatibilis alapértelmezett értéket biztosítanak. Jó gyakorlat, hogy `Accept`mindig tartalmazza . | Nem kötelező, de ajánlott. |

## <a name="audio-formats"></a>Hangformátumok

A rendszer a HTTP-kérelem `POST` törzsében küldi el a hangot. A táblázat egyik formátumában kell lennie:

| Formátum | Codec | Bitráta | Mintavételi arány  |
|--------|-------|---------|--------------|
| WAV    | PCM   | 16 bites  | 16 kHz, monó |
| Ogg    | Opus  | 16 bites  | 16 kHz, monó |

>[!NOTE]
>A fenti formátumok a REST API és a WebSocket a speech szolgáltatás ban támogatott. A [Speech SDK](speech-sdk.md) jelenleg támogatja a WAV formátumot PCM kodekekkel és [más formátumokkal.](how-to-use-codec-compressed-audio-input-streams.md)

## <a name="sample-request"></a>Mintakérelem

Az alábbi minta tartalmazza az állomásnevet és a szükséges fejléceket. Fontos megjegyezni, hogy a szolgáltatás hangadatokat is vár, amelyek nem szerepelnek ebben a mintában. Mint korábban említettük, darabolás ajánlott, azonban nem szükséges.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

## <a name="http-status-codes"></a>HTTP-állapotkódok

Az egyes válaszok HTTP-állapotkódja sikeres vagy gyakori hibákat jelez.

| HTTP-állapotkód | Leírás | Lehetséges ok |
|------------------|-------------|-----------------|
| `100` | Folytatás | Az eredeti kérést elfogadták. Folytassa a többi adat elküldésével. (Darabolt átvitellel használható) |
| `200` | OK | A kérés sikeres volt; a választörzs egy JSON objektum. |
| `400` | Rossz kérés | Nyelvkód nincs megadva, nem támogatott nyelv, érvénytelen hangfájl stb. |
| `401` | Nem engedélyezett | Az előfizetési kulcs vagy az engedélyezési jogkivonat érvénytelen a megadott régióban, vagy érvénytelen a végpont. |
| `403` | Forbidden | Hiányzik az előfizetési kulcs vagy az engedélyezési jogkivonat. |

## <a name="chunked-transfer"></a>Darabolt átvitel

A darabolt`Transfer-Encoding: chunked`átvitel ( ) segíthet csökkenteni a felismerés késését. Lehetővé teszi, hogy a beszédszolgáltatás megkezdje a hangfájl feldolgozását a továbbítás során. A REST API nem nyújt részleges vagy köztes eredményeket.

Ez a kódminta bemutatja, hogyan küldhet hangot adattömbökben. Csak az első adattömbnek kell tartalmaznia a hangfájl fejlécét. `request`a `HttpWebRequest` megfelelő REST-végponthoz csatlakoztatott objektum. `audioFile`a lemezen lévő hangfájl elérési útja.

```csharp
var request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = host;
request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";
request.AllowWriteStreamBuffering = false;

using (var fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    // Open a request stream and write 1024 byte chunks in the stream one at a time.
    byte[] buffer = null;
    int bytesRead = 0;
    using (var requestStream = request.GetRequestStream())
    {
        // Read 1024 raw bytes from the input audio file.
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        requestStream.Flush();
    }
}
```

## <a name="response-parameters"></a>Válasz paraméterei

Az eredmények JSON-ként vannak megadva. A `simple` formátum ezeket a legfelső szintű mezőket tartalmazza.

| Paraméter | Leírás  |
|-----------|--------------|
|`RecognitionStatus`|Állapot, például `Success` a sikeres felismeréshez. Lásd a következő táblázatot.|
|`DisplayText`|A felismert szöveg nagybetűs írásjelek, írásjelek, inverz szöveg normalizálása (a kimondott szöveg rövidebb formákra konvertálása, például 200 a "kétszáz" vagy "Dr. Smith" a "doctor smith" esetében) és a káromkodás maszkolása után. Csak a sikerről van jelen.|
|`Offset`|Az az idő (100 nanoszekundumos egységben), amikor a felismert beszéd megkezdődik a hangfolyamban.|
|`Duration`|A felismert beszéd időtartama (100 nanoszekundumos egységben) a hangfolyamban.|

A `RecognitionStatus` mező a következő értékeket tartalmazhatja:

| status | Leírás |
|--------|-------------|
| `Success` | Az elismerés sikeres `DisplayText` volt, és a mező jelen van. |
| `NoMatch` | A rendszer beszédfelismerést észlelt a hangfolyamban, de a célnyelvből származó szavak nem egyeztek. Általában azt jelenti, hogy a felismerés nyelve eltér attól, amit a felhasználó beszél. |
| `InitialSilenceTimeout` | A hangfolyam kezdete csak csendet tartalmazott, és a szolgáltatás időzítve várta a beszédet. |
| `BabbleTimeout` | A hangfolyam kezdete csak zajt tartalmazott, és a szolgáltatás időzött a beszédre várva. |
| `Error` | A felismerési szolgáltatás belső hibát észlelt, és nem tudta folytatni. Próbálja újra, ha lehetséges. |

> [!NOTE]
> Ha a hang csak káromkodásból áll, `profanity` és a `remove`lekérdezési paraméter a beállítás, a szolgáltatás nem ad vissza beszéderedményt.

A `detailed` formátum ugyanazokat az `simple` adatokat `NBest`tartalmazza, mint a formátum, valamint az ugyanazon elismerés eredményének alternatív értelmezéseit. Ezek az eredmények rangsorolják a legvalószínűbb, hogy a legkevésbé valószínű. Az első tétel megegyezik a fő elismerés eredményének.  A formátum `detailed` használata `DisplayText` kor `Display` a `NBest` lista minden egyes eredményének megfelelően van megadva.

A lista `NBest` minden objektuma a következőket tartalmazza:

| Paraméter | Leírás |
|-----------|-------------|
| `Confidence` | A belépés megbízhatósági pontszáma 0,0-ról (nincs bizalom) 1,0-ra (teljes megbízhatóság) |
| `Lexical` | A felismert szöveg lexikális formája: a felismert szavak. |
| `ITN` | A felismert szöveg inverz szövegnormalizált ("kanonikus") formája, telefonszámokkal, számokkal, rövidítésekkel ("doctor smith" a "dr smith")) és egyéb átalakítások alkalmazásával. |
| `MaskedITN` | Az ITN-űrlap káromkodásmaszkolással, kérésre alkalmazva. |
| `Display` | A felismert szöveg megjelenítési formája, írásjellel és nagybetűvel kiegészítve. Ez a paraméter `DisplayText` megegyezik a megadott `simple`a formátum beállításakor. |

## <a name="sample-responses"></a>Mintaválaszok

Tipikus válasz `simple` a felismerésre:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Tipikus válasz `detailed` a felismerésre:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="next-steps"></a>További lépések

- [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
