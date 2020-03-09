---
title: Beszéd és szöveg közötti API-referenciák (REST) – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan használhatja a beszéd – szöveg REST API. Ebben a cikkben megismerkedhet engedélyezési beállítások, a lekérdezési beállítások, struktúra kérést és választ kapnak.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: erhopf
ms.openlocfilehash: 873898ce321100edbaa800d2436d0413c06ce175
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390806"
---
# <a name="speech-to-text-rest-api"></a>Diktálás REST API

A Speech [SDK](speech-sdk.md)alternatívájaként a beszédfelismerési szolgáltatás lehetővé teszi, hogy REST API használatával alakítsa át a beszédfelismerést a szöveggé. Minden elérhető végpontot nem egy régió tartozik. Az alkalmazás egy előfizetési kulcsot szeretné használni a végpont szükséges.

A beszéd – szöveg REST API használata előtt értse fel a következőket:

* A REST APIt használó és a hang közvetlen továbbítására szolgáló kérelmek legfeljebb 60 másodperces hangot tartalmazhatnak.
* A hang-szöveg transzformációs REST API-t csak végső eredményt adja vissza. Részleges eredményeket nem tartozik.

Ha a hosszú hang küldése az alkalmazásra vonatkozó követelmény, érdemes lehet használni a [SPEECH SDK](speech-sdk.md) -t vagy egy file-alapú REST API, például a [Batch átírását](batch-transcription.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Régiók és végpontok

A REST API végpontjának formátuma a következő:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Cserélje le a `<REGION_IDENTIFIER>` az előfizetés régiójának megfelelő azonosítóra a következő táblázatból:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> A nyelvi paramétert az URL-címhez kell hozzáfűzni, hogy ne kapjon 4xx HTTP-hibát. Az USA nyugati végpontját használó angol nyelv például a következő: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`.

## <a name="query-parameters"></a>Lekérdezési paraméterek

Ezeket a paramétereket a lekérdezési karakterláncban a REST-kérés szerepelhet.

| Paraméter | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| `language` | A felismert használja a beszélt nyelv azonosítja. Lásd: [támogatott nyelvek](language-support.md#speech-to-text). | Kötelező |
| `format` | Megadja a eredményformátum. Az elfogadott értékek `simple` és `detailed`. Az egyszerű eredmények közé tartozik a `RecognitionStatus`, a `DisplayText`, a `Offset`és a `Duration`. Részletes válaszok megbízhatósági értékeket és a négy különböző reprezentációinak több eredmények belefoglalása. Az alapértelmezett beállítás a `simple`. | Optional |
| `profanity` | Adja meg a felismerési eredményeket cenzúrázása kezelése. Az elfogadott értékek `masked`, amelyek felváltják a csillagokkal való káromkodást, `removed`, ami eltávolítja az eredményből az összes káromkodást, vagy `raw`, amely magában foglalja a káromkodást az eredményben. Az alapértelmezett beállítás a `masked`. | Optional |
| `cid` | Ha egyéni modelleket hoz létre a [Custom Speech-portálon](how-to-custom-speech.md) , egyéni modelleket használhat a **telepítési** oldalon található **végpont-azonosítón** keresztül. Használja a **VÉGPONT azonosítóját** a `cid` lekérdezési karakterlánc paraméterének argumentumként. | Optional |

## <a name="request-headers"></a>Kérelemfejlécek

Ez a táblázat felsorolja a szükséges és választható fejlécek a hang-szöveg transzformációs kéréseket.

|Fejléc| Leírás | Kötelező / választható |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | A beszédfelismerési szolgáltatás előfizetési kulcsa. | Ezt a fejlécet vagy `Authorization` kötelező megadni. |
| `Authorization` | A `Bearer`szó előtt egy engedélyezési jogkivonat. További információért lásd: [Hitelesítés](#authentication). | Ezt a fejlécet vagy `Ocp-Apim-Subscription-Key` kötelező megadni. |
| `Content-type` | A formátum és a megadott hang adatok kodek ismerteti. Az elfogadott értékek `audio/wav; codecs=audio/pcm; samplerate=16000` és `audio/ogg; codecs=opus`. | Kötelező |
| `Transfer-Encoding` | Megadja, hogy darabolt hang adatot küld, ahelyett, hogy egyetlen fájlt. Csak akkor használja ezt a fejlécet, ha hang adatokat. | Optional |
| `Expect` | Ha darabolásos átvitelt használ, küldjön `Expect: 100-continue`. A beszédfelismerési szolgáltatás tudomásul veszi a kezdeti kérést, és további adatra vár.| Szükséges darabolt hang adatot küldenek. |
| `Accept` | Ha meg van adni, `application/json`nak kell lennie. A beszédfelismerési szolgáltatás az eredményeket JSON-formátumban jeleníti meg. Néhány kérelem-keretrendszer nem kompatibilis alapértelmezett értéket biztosít. Javasoljuk, hogy mindig vegyen fel `Accept`. | Nem kötelező, de ajánlott. |

## <a name="audio-formats"></a>Hangformátumok

A hang a HTTP `POST` kérelem törzsében lesz elküldve. A jelen táblázatban lévő formátumok valamelyikében kell lennie:

| Formátum | Kodek | Átviteli sebesség | Mintavételi frekvencia  |
|--------|-------|---------|--------------|
| WAV    | A PCM   | 16-bit  | 16 kHz, mono |
| OGG    | OPUS  | 16-bit  | 16 kHz, mono |

>[!NOTE]
>A fenti formátumok a Speech Service REST API és WebSocket szolgáltatásán keresztül támogatottak. A [SPEECH SDK](speech-sdk.md) jelenleg a WAV formátumot támogatja a PCM-kodekkel és [más formátumokkal](how-to-use-codec-compressed-audio-input-streams.md).

## <a name="sample-request"></a>Mintakérelem

Az alábbi mintát is tartalmaz, a gazdagépnév és a kötelező fejlécet. Fontos megjegyezni, hogy a szolgáltatás emellett arra számít hívásaiból, amely nem érhető el ebben a példában. Leírtak szerint korábban darabolás ajánlott, azonban nem kötelező.

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

A HTTP-állapotkód: minden válasz azt jelzi, hogy a sikeres vagy gyakori hibák.

| HTTP-állapotkód | Leírás | Lehetséges ok |
|------------------|-------------|-----------------|
| `100` | Folytatás | A kezdeti kérés elfogadva. Folytassa a többi adatot küld. (Darabolásos átvitelsel használatos) |
| `200` | OK | A kérelem sikeres volt. a választörzs mérete a JSON-objektum. |
| `400` | Hibás kérés | A nyelvi kód nincs megadva, nem támogatott nyelv, érvénytelen hangfájl stb. |
| `401` | Nem engedélyezett | Az előfizetői vagy engedélyezési jogkivonat érvénytelen a megadott régióban, vagy érvénytelen végpont. |
| `403` | Forbidden | Hiányzik az előfizetési kulcs vagy engedélyezési jogkivonat. |

## <a name="chunked-transfer"></a>Darabolásos átvitel

A darabolásos átvitel (`Transfer-Encoding: chunked`) segítségével csökkentheti az elismerés késését. Lehetővé teszi, hogy a beszédfelismerési szolgáltatás megkezdje a hangfájl feldolgozását a továbbítás során. A REST API-t nem biztosít részleges vagy köztes eredményeket.

A mintakód bemutatja, hogyan hang tömbökben küldése. Csak az első adatrészletben kell tartalmaznia a hangfájl fejléc. a `request` egy `HttpWebRequest` objektum, amely a megfelelő REST-végponthoz van csatlakoztatva. `audioFile` a lemezen lévő hangfájl elérési útja.

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

## <a name="response-parameters"></a>Válasz paraméterek

Eredményei JSON-fájlként. A `simple` formátum tartalmazza ezeket a legfelső szintű mezőket.

| Paraméter | Leírás  |
|-----------|--------------|
|`RecognitionStatus`|Állapot, például `Success` a sikeres felismeréshez. Lásd a következő táblázatot.|
|`DisplayText`|A felismert szöveg a tőkésítés, a központozás, az inverz szöveg normalizálása (a szóbeli szöveg konvertálása rövidebb formátumokra, például 200 a "200" vagy "Dr. Smith" kifejezésre a "Doctor Smith" esetében) és a káromkodás maszkolása után. Jelenleg csak a sikeres.|
|`Offset`|Az idő (100 nanoszekundumos egységek), amelyen a felismert speech az audio-adatfolyam kezdődik.|
|`Duration`|Az időtartam (100 nanoszekundumos egység) a felismert beszéd, a hang adatfolyamban.|

A `RecognitionStatus` mező a következő értékeket tartalmazhatja:

| status | Leírás |
|--------|-------------|
| `Success` | Az felismerés sikeres volt, és a `DisplayText` mező van jelen. |
| `NoMatch` | Beszéd az audio-adatfolyam észlelt, de nincs szó azoktól a Célnyelv sem felel meg is. Általában azt jelenti, a beszédfelismerési nyelv, a felhasználó van, és beszéljen egy másik nyelven. |
| `InitialSilenceTimeout` | A hang stream elején szereplő csak csend, és a szolgáltatás, beszédfelismerés várakozás túllépte az időkorlátot. |
| `BabbleTimeout` | A hang stream elején szereplő csak zaj, és a szolgáltatás, beszédfelismerés várakozás túllépte az időkorlátot. |
| `Error` | A beszédfelismerést szolgáltatás belső hibába ütközött, és nem lehetett folytatni. Próbálja meg újra, ha lehetséges. |

> [!NOTE]
> Ha a hang csak a káromkodásból áll, és a `profanity` lekérdezési paraméter értéke `remove`, a szolgáltatás nem ad vissza beszédfelismerési eredményt.

A `detailed` formátum ugyanazokat az adat`simple` formátumot tartalmazza, mint a `NBest`, valamint az azonos felismerési eredmény alternatív értelmezéseit tartalmazó lista. Ezek az eredmények a legvalószínűbbtől a legkevésbé valószínűtől lesznek rangsorolva. Az első bejegyzés ugyanaz, mint a fő felismerési eredmény.  A `detailed` formátum használatakor a rendszer a `NBest` lista minden egyes eredményének `Display`ként `DisplayText`.

A `NBest` listán szereplő összes objektum a következőket tartalmazza:

| Paraméter | Leírás |
|-----------|-------------|
| `Confidence` | A megbízhatósági pontszám a bejegyzés a 0,0 (nincs megbízhatóság) 1.0-s (teljes megbízhatósági) |
| `Lexical` | A felismert szöveget lexikai formájában: a tényleges szavak ismerhető fel. |
| `ITN` | Inverz szöveg normalizált ("kanonikus") formájában a felismert szöveget, phone számok, rövidítések ("orvos smith", "dr smith") és egyéb átalakítások alkalmazza. |
| `MaskedITN` | A vulgáris maszkolási alkalmazza, ha a rendszer kéri fel űrlapján. |
| `Display` | A megjelenítési formája a felismert szöveget, írásjelek és a nagybetűk hozzáadva. Ez a paraméter ugyanaz, mint a `DisplayText`, ha a formátum értéke `simple`. |

## <a name="sample-responses"></a>Minta válaszok

Egy tipikus válasz a `simple` felismerésre:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Egy tipikus válasz a `detailed` felismerésre:

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

## <a name="next-steps"></a>Következő lépések

- [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
