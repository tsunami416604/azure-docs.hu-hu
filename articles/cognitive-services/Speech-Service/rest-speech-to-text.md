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
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 137ab722df280d17fe5ccc5c07acfd323feb6531
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091208"
---
# <a name="speech-to-text-rest-api"></a>Diktálás REST API

A Speech Services alternatívájaként a beszédfelismerési szolgáltatások lehetővé teszik, hogy REST API használatával alakítsa [át a](speech-sdk.md)beszédfelismerést a szövegre. Minden elérhető végpontot nem egy régió tartozik. Az alkalmazás egy előfizetési kulcsot szeretné használni a végpont szükséges.

A beszéd – szöveg REST API használata előtt értse fel a következőket:

* A REST APIt használó és a hang közvetlen továbbítására szolgáló kérelmek legfeljebb 60 másodperces hangot tartalmazhatnak.
* A hang-szöveg transzformációs REST API-t csak végső eredményt adja vissza. Részleges eredményeket nem tartozik.

Ha a hosszú hang küldése az alkalmazásra vonatkozó követelmény, érdemes lehet használni a [SPEECH SDK](speech-sdk.md) -t vagy egy file-alapú REST API, például a [Batch átírását](batch-transcription.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Régiók és végpontok

Ezekben a régiókban támogatottak hang-szöveg transzformációs beszédátírási a REST API használatával. Győződjön meg arról, hogy a végpontot, amely megfelel az előfizetés régiót választja.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)] 

## <a name="query-parameters"></a>Lekérdezési paraméterek

Ezeket a paramétereket a lekérdezési karakterláncban a REST-kérés szerepelhet.

| Paraméter | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| `language` | A felismert használja a beszélt nyelv azonosítja. Lásd: [támogatott nyelvek](language-support.md#speech-to-text). | Kötelező |
| `format` | Megadja a eredményformátum. Elfogadott értékek a következők `simple` és `detailed`. Egyszerű eredmények tartalmazzák a `RecognitionStatus`, `DisplayText`, `Offset`, és `Duration`. Részletes válaszok megbízhatósági értékeket és a négy különböző reprezentációinak több eredmények belefoglalása. Az alapértelmezett beállítás `simple`. | Optional |
| `profanity` | Adja meg a felismerési eredményeket cenzúrázása kezelése. Az elfogadott értékek `masked`, amelyek felváltják a csillagokkal való káromkodást, `removed`, ami eltávolítja az eredményből az összes káromkodást, vagy `raw`, amely magában foglalja a káromkodást az eredményben. Az alapértelmezett beállítás `masked`. | Optional |

## <a name="request-headers"></a>Kérelemfejlécek

Ez a táblázat felsorolja a szükséges és választható fejlécek a hang-szöveg transzformációs kéréseket.

|Fejléc| Leírás | Kötelező / választható |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | A Speech Services-előfizetési kulcs. | Vagy a fejléc vagy `Authorization` megadása kötelező. |
| `Authorization` | Egy engedélyezési jogkivonatot előzi meg a word `Bearer`. További információért lásd: [Hitelesítés](#authentication). | Vagy a fejléc vagy `Ocp-Apim-Subscription-Key` megadása kötelező. |
| `Content-type` | A formátum és a megadott hang adatok kodek ismerteti. Elfogadott értékek a következők `audio/wav; codecs=audio/pcm; samplerate=16000` és `audio/ogg; codecs=opus`. | Kötelező |
| `Transfer-Encoding` | Megadja, hogy darabolt hang adatot küld, ahelyett, hogy egyetlen fájlt. Csak akkor használja ezt a fejlécet, ha hang adatokat. | Optional |
| `Expect` | Ha használja a darabolásos átvitel, küldjön `Expect: 100-continue`. A Speech Services tudomásul veszi a kezdeti kérést, és további adatra vár.| Szükséges darabolt hang adatot küldenek. |
| `Accept` | Ha meg van adva, kell lennie `application/json`. A Speech Services az eredményeket JSON-formátumban jeleníti meg. Néhány kérelem-keretrendszer nem kompatibilis alapértelmezett értéket biztosít. Javasoljuk, hogy mindig vegyen fel `Accept`. | Nem kötelező, de ajánlott. |

## <a name="audio-formats"></a>Hangformátumok

Hang küldése a HTTP törzsében `POST` kérelmet. A jelen táblázatban lévő formátumok valamelyikében kell lennie:

| Formátum | Kodek | Átviteli sebesség | Mintavételi frekvencia |
|--------|-------|---------|-------------|
| WAV | A PCM | 16-bit | 16 kHz, mono |
| OGG | OPUS | 16-bit | 16 kHz, mono |

>[!NOTE]
>A fenti formátumok a Speech Services REST API és WebSocket szolgáltatásán keresztül támogatottak. A [beszéd SDK](speech-sdk.md) jelenleg csak támogatja a WAV PCM kodekkel formázása.

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
| 100 | Folytatás | A kezdeti kérés elfogadva. Folytassa a többi adatot küld. (Használja a darabolásos átviteli.) |
| 200 | OK | A kérelem sikeres volt. a választörzs mérete a JSON-objektum. |
| 400 | Hibás kérés | A nyelvi kód nincs megadva, nem támogatott nyelv, érvénytelen hangfájl stb. |
| 401 | Nem engedélyezett | Az előfizetői vagy engedélyezési jogkivonat érvénytelen a megadott régióban, vagy érvénytelen végpont. |
| 403 | Forbidden | Hiányzik az előfizetési kulcs vagy engedélyezési jogkivonat. |

## <a name="chunked-transfer"></a>Darabolásos átvitel

A darabolásos átvitel (`Transfer-Encoding: chunked`) segítségével csökkentheti az elismerés késését. Lehetővé teszi, hogy a Speech Services megkezdje a hangfájl feldolgozását a továbbítás során. A REST API-t nem biztosít részleges vagy köztes eredményeket.

A mintakód bemutatja, hogyan hang tömbökben küldése. Csak az első adatrészletben kell tartalmaznia a hangfájl fejléc. `request` egy HTTPWebRequest objektumot a megfelelő REST-végponthoz csatlakozik. `audioFile` a hangfájl lemezen út.

```csharp

    HttpWebRequest request = null;
    request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
    request.SendChunked = true;
    request.Accept = @"application/json;text/xml";
    request.Method = "POST";
    request.ProtocolVersion = HttpVersion.Version11;
    request.Host = host;
    request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
    request.Headers["Ocp-Apim-Subscription-Key"] = args[1];
    request.AllowWriteStreamBuffering = false;

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
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

## <a name="response-parameters"></a>Válasz paraméterek

Eredményei JSON-fájlként. A `simple` formátum tartalmazza ezeket a legfelső szintű mezőket.

| Paraméter | Leírás  |
|-----------|--------------|
|`RecognitionStatus`|Állapot, mint például `Success` sikeres felismeréséhez. Lásd a következő táblázatot.|
|`DisplayText`|A felismert szöveg a tőkésítés, a központozás, az inverz szöveg normalizálása (a szóbeli szöveg konvertálása rövidebb formátumokra, például 200 a "200" vagy "Dr. Smith" kifejezésre a "Doctor Smith" esetében) és a káromkodás maszkolása után. Jelenleg csak a sikeres.|
|`Offset`|Az idő (100 nanoszekundumos egységek), amelyen a felismert speech az audio-adatfolyam kezdődik.|
|`Duration`|Az időtartam (100 nanoszekundumos egység) a felismert beszéd, a hang adatfolyamban.|

A `RecognitionStatus` mező tartalmazza ezeket az értékeket:

| status | Leírás |
|--------|-------------|
| `Success` | Sikeres volt-e a felismerés és az `DisplayText` mező szerepel. |
| `NoMatch` | Beszéd az audio-adatfolyam észlelt, de nincs szó azoktól a Célnyelv sem felel meg is. Általában azt jelenti, a beszédfelismerési nyelv, a felhasználó van, és beszéljen egy másik nyelven. |
| `InitialSilenceTimeout` | A hang stream elején szereplő csak csend, és a szolgáltatás, beszédfelismerés várakozás túllépte az időkorlátot. |
| `BabbleTimeout` | A hang stream elején szereplő csak zaj, és a szolgáltatás, beszédfelismerés várakozás túllépte az időkorlátot. |
| `Error` | A beszédfelismerést szolgáltatás belső hibába ütközött, és nem lehetett folytatni. Próbálja meg újra, ha lehetséges. |

> [!NOTE]
> Ha a hanganyag csak káromkodás tartalmaz, és a `profanity` lekérdezési paraméter értéke `remove`, a szolgáltatás nem ad vissza egy beszéd eredményt.

A `detailed` formátum ugyanazokat az adat`simple` formátumot tartalmazza, mint a `NBest`, valamint az azonos felismerési eredmény alternatív értelmezéseit tartalmazó lista. Ezek az eredmények a legvalószínűbbtől a legkevésbé valószínűtől lesznek rangsorolva. Az első bejegyzés ugyanaz, mint a fő felismerési eredmény.  Használatakor a `detailed` formátumban, `DisplayText` verzióként `Display` az egyes eredményez a `NBest` listája.

Az egyes objektumok a `NBest` lista a következőket tartalmazza:

| Paraméter | Leírás |
|-----------|-------------|
| `Confidence` | A megbízhatósági pontszám a bejegyzés a 0,0 (nincs megbízhatóság) 1.0-s (teljes megbízhatósági) |
| `Lexical` | A felismert szöveget lexikai formájában: a tényleges szavak ismerhető fel. |
| `ITN` | Inverz szöveg normalizált ("kanonikus") formájában a felismert szöveget, phone számok, rövidítések ("orvos smith", "dr smith") és egyéb átalakítások alkalmazza. |
| `MaskedITN` | A vulgáris maszkolási alkalmazza, ha a rendszer kéri fel űrlapján. |
| `Display` | A megjelenítési formája a felismert szöveget, írásjelek és a nagybetűk hozzáadva. Ez a paraméter megegyezik a `DisplayText` feltéve ha formátum beállítása `simple`. |

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
