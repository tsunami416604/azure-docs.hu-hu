---
title: Hang-szöveg transzformációs API-referencia (REST) – beszédszolgáltatások
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használja a hang-szöveg transzformációs REST API-t. Ebben a cikkben megismerkedhet engedélyezési beállítások, a lekérdezési beállítások, struktúra kérést és választ kapnak.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: baaa7b1068e13863293e0968cb0bf1ffb198882b
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2019
ms.locfileid: "57894968"
---
# <a name="speech-to-text-rest-api"></a>Hang-szöveg transzformációs REST API-val

Alternatív megoldásként a [beszéd SDK](speech-sdk.md), beszédszolgáltatások engedélyezése alakíthatja át a hang-szöveg transzformációs egy REST API használatával. Minden elérhető végpontot nem egy régió tartozik. Az alkalmazás egy előfizetési kulcsot szeretné használni a végpont szükséges.

A hang-szöveg transzformációs REST API-val, mielőtt megismerése:
* A REST API-kérelmeket csak hang 10 másodperces tartalmazhat.
* A hang-szöveg transzformációs REST API-t csak végső eredményt adja vissza. Részleges eredményeket nem tartozik.

Küldenek, hosszabb hang esetében az alkalmazás követelmény, fontolja meg a [beszéd SDK](speech-sdk.md) vagy [beszédátírási batch](batch-transcription.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Régiók és végpontok

Ezekben a régiókban támogatottak hang-szöveg transzformációs beszédátírási a REST API használatával. Győződjön meg arról, hogy a végpontot, amely megfelel az előfizetés régiót választja.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

## <a name="query-parameters"></a>Lekérdezési paraméterek

Ezeket a paramétereket a lekérdezési karakterláncban a REST-kérés szerepelhet.

| Paraméter | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| `language` | A felismert használja a beszélt nyelv azonosítja. Lásd: [támogatott nyelvek](language-support.md#speech-to-text). | Szükséges |
| `format` | Megadja a eredményformátum. Elfogadott értékek a következők `simple` és `detailed`. Egyszerű eredmények tartalmazzák a `RecognitionStatus`, `DisplayText`, `Offset`, és `Duration`. Részletes válaszok megbízhatósági értékeket és a négy különböző reprezentációinak több eredmények belefoglalása. Az alapértelmezett beállítás `simple`. | Optional |
| `profanity` | Adja meg a felismerési eredményeket cenzúrázása kezelése. Elfogadott értékek a következők `masked`, csillag, amely lecseréli cenzúrázása `removed`, amely minden cenzúrázása az eredményt, távolítsa el vagy `raw`, amely tartalmazza a vulgáris, az eredmény. Az alapértelmezett beállítás `masked`. | Optional |

## <a name="request-headers"></a>Kérelemfejlécek

Ez a táblázat felsorolja a szükséges és választható fejlécek a hang-szöveg transzformációs kéréseket.

|Fejléc| Leírás | Kötelező / választható |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Az előfizetési kulcs beszédszolgáltatások. | Vagy a fejléc vagy `Authorization` megadása kötelező. |
| `Authorization` | Egy engedélyezési jogkivonatot előzi meg a word `Bearer`. További információért lásd: [Hitelesítés](#authentication). | Vagy a fejléc vagy `Ocp-Apim-Subscription-Key` megadása kötelező. |
| `Content-type` | A formátum és a megadott hang adatok kodek ismerteti. Elfogadott értékek a következők `audio/wav; codecs=audio/pcm; samplerate=16000` és `audio/ogg; codecs=opus`. | Szükséges |
| `Transfer-Encoding` | Megadja, hogy darabolt hang adatot küld, ahelyett, hogy egyetlen fájlt. Csak akkor használja ezt a fejlécet, ha hang adatokat. | Optional |
| `Expect` | Ha használja a darabolásos átvitel, küldjön `Expect: 100-continue`. A beszédszolgáltatások igazolja a kiindulási kérelemhez, és további adatokat várja.| Szükséges darabolt hang adatot küldenek. |
| `Accept` | Ha meg van adva, kell lennie `application/json`. A beszédszolgáltatásai JSON eredményez. Bizonyos webes kérés keretrendszereket, adjon meg egy nem kompatibilis alapértelmezett értéket, ha nincs megadva, így az mindig célszerű tartalmazzák `Accept`. | Nem kötelező, de ajánlott. |

## <a name="audio-formats"></a>Hangformátumok

Hang küldése a HTTP törzsében `POST` kérelmet. A jelen táblázatban lévő formátumok valamelyikében kell lennie:

| Formátum | Kodek | Átviteli sebesség | Mintavételi frekvencia |
|--------|-------|---------|-------------|
| WAV | A PCM | 16-bit | 16 kHz, mono |
| OGG | OPUS | 16-bit | 16 kHz, mono |

>[!NOTE]
>A fenti formátumok REST API-t és a Speech Services WebSocket támogatottak. A [beszéd SDK](speech-sdk.md) jelenleg csak támogatja a WAV PCM kodekkel formázása.

## <a name="sample-request"></a>Mintakérelem

Ez az általános HTTP-kérés. Az alábbi mintát is tartalmaz, a gazdagépnév és a kötelező fejlécet. Fontos megjegyezni, hogy a szolgáltatás emellett arra számít hívásaiból, amely nem érhető el ebben a példában. Leírtak szerint korábban darabolás ajánlott, azonban nem kötelező.

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
| 400 | Hibás kérés | Nyelvkód nem biztosított, vagy nem támogatott nyelvet; hangfájl érvénytelen. |
| 401 | Nem engedélyezett | Az előfizetői vagy engedélyezési jogkivonat érvénytelen a megadott régióban, vagy érvénytelen végpont. |
| 403 | Tiltott | Hiányzik az előfizetési kulcs vagy engedélyezési jogkivonat. |

## <a name="chunked-transfer"></a>Darabolásos átvitel

Darabolásos átvitel (`Transfer-Encoding: chunked`) segít minimálisra csökkenteni a felismerés késés, mert lehetővé teszi a beszédszolgáltatások feldolgozza a hangfájl folyamatban átvitel közben. A REST API-t nem biztosít részleges vagy köztes eredményeket. Ez a beállítás kizárólag növelni a válaszkészséget a funkcionalitást.

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
|`DisplayText`|A felismert szöveget után nagybetűk, írásjelek, más néven Inverz szöveg normalizálási (átalakítása a kimondott szöveg rövidebb űrlapok, például a 200-as "kétszáz" vagy "Dr. János""orvos Smith"), és a vulgáris maszkolási. Jelenleg csak a sikeres.|
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

A `detailed` formátuma azonos adatokat tartalmaz a `simple` formájában, valamint a `NBest`, felismerés ugyanaz az eredmény az alternatív értelmezések listáját. Ezeket az eredményeket a legnagyobb valószínűséggel sorrendje valószínűleg legalább az első bejegyzés ugyanaz, mint a fő felismerés eredményét.  Használatakor a `detailed` formátumban, `DisplayText` verzióként `Display` az egyes eredményez a `NBest` listája.

Az egyes objektumok a `NBest` lista a következőket tartalmazza:

| Paraméter | Leírás |
|-----------|-------------|
| `Confidence` | A megbízhatósági pontszám a bejegyzés a 0,0 (nincs megbízhatóság) 1.0-s (teljes megbízhatósági) |
| `Lexical` | A felismert szöveget lexikai formájában: a tényleges szavak ismerhető fel. |
| `ITN` | Inverz szöveg normalizált ("kanonikus") formájában a felismert szöveget, phone számok, rövidítések ("orvos smith", "dr smith") és egyéb átalakítások alkalmazza. |
| `MaskedITN` | A vulgáris maszkolási alkalmazza, ha a rendszer kéri fel űrlapján. |
| `Display` | A megjelenítési formája a felismert szöveget, írásjelek és a nagybetűk hozzáadva. Ez a paraméter megegyezik a `DisplayText` feltéve ha formátum beállítása `simple`. |

## <a name="sample-responses"></a>Minta válaszok

Ez a jellemző választ `simple` felismerése.

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Ez a jellemző választ `detailed` felismerése.

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
