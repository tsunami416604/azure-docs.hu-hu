---
title: Beszéd és szöveg közötti API-referenciák (REST) – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan használhatja a beszéd – szöveg REST API. Ebben a cikkben megismerheti az engedélyezési lehetőségeket, a lekérdezési lehetőségeket, a kérések strukturálása és a válasz fogadását.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: ea37dc9ee6c9249aa9d18f7ee7ab1fdbe1230930
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975839"
---
# <a name="speech-to-text-rest-api"></a>Diktálás REST API

A Speech [SDK](speech-sdk.md)alternatívájaként a beszédfelismerési szolgáltatás lehetővé teszi, hogy REST API használatával alakítsa át a beszédfelismerést a szöveggé. Minden elérhető végpont egy régióhoz van társítva. Az alkalmazáshoz szükség van egy előfizetési kulcsra a használni kívánt végponthoz.

A beszéd – szöveg REST API használata előtt értse fel a következőket:

* A REST APIt használó és a hang közvetlen továbbítására szolgáló kérelmek legfeljebb 60 másodperces hangot tartalmazhatnak.
* A beszéd – szöveg REST API csak a végső eredményeket adja vissza. A részleges eredmények nincsenek megadva.

Ha a hosszú hang küldése az alkalmazásra vonatkozó követelmény, érdemes lehet használni a [SPEECH SDK](speech-sdk.md) -t vagy egy file-alapú REST API, például a [Batch átírását](batch-transcription.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Régiók és végpontok

Ezek a régiók a REST API használatával történő beszéd-szöveges átíráshoz támogatottak. Győződjön meg arról, hogy az előfizetési régiójának megfelelő végpontot választotta.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)] 

## <a name="query-parameters"></a>Lekérdezési paraméterek

Ezek a paraméterek szerepelhetnek a REST-kérelem lekérdezési karakterláncában.

| Paraméter | Leírás | Kötelező/nem kötelező |
|-----------|-------------|---------------------|
| `language` | Azonosítja a felismert nyelvet. Lásd: [támogatott nyelvek](language-support.md#speech-to-text). | Szükséges |
| `format` | Megadja az eredmény formátumát. Az elfogadott értékek `simple` és `detailed`. Az egyszerű eredmények közé tartozik a `RecognitionStatus`, a `DisplayText`, a `Offset`és a `Duration`. A részletes válaszok többek között megbízhatósági értékekkel és négy különböző képviselettel rendelkeznek. Az alapértelmezett beállítás: `simple`. | Választható |
| `profanity` | Meghatározza, hogyan kezelhető a káromkodás az eredmények felismerésében. Az elfogadott értékek `masked`, amelyek felváltják a csillagokkal való káromkodást, `removed`, ami eltávolítja az eredményből az összes káromkodást, vagy `raw`, amely magában foglalja a káromkodást az eredményben. Az alapértelmezett beállítás: `masked`. | Választható |

## <a name="request-headers"></a>Kérésfejlécek

Ez a táblázat a beszédfelismerési kérelmekhez szükséges és nem kötelező fejléceket sorolja fel.

|Fejléc| Leírás | Kötelező/nem kötelező |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | A beszédfelismerési szolgáltatás előfizetési kulcsa. | Ezt a fejlécet vagy `Authorization` kötelező megadni. |
| `Authorization` | A `Bearer`szó előtt egy engedélyezési jogkivonat. További információért lásd: [Hitelesítés](#authentication). | Ezt a fejlécet vagy `Ocp-Apim-Subscription-Key` kötelező megadni. |
| `Content-type` | A megadott hangadatok formátumát és kodekjét ismerteti. Az elfogadott értékek `audio/wav; codecs=audio/pcm; samplerate=16000` és `audio/ogg; codecs=opus`. | Szükséges |
| `Transfer-Encoding` | Meghatározza, hogy a rendszer a darabolásos hangadatokat egyetlen fájl helyett elküldje. Csak a hangadatok darabolásakor használja ezt a fejlécet. | Választható |
| `Expect` | Ha darabolásos átvitelt használ, küldjön `Expect: 100-continue`. A beszédfelismerési szolgáltatás tudomásul veszi a kezdeti kérést, és további adatra vár.| A darabolásos hangadatok küldéséhez szükséges. |
| `Accept` | Ha meg van adni, `application/json`nak kell lennie. A beszédfelismerési szolgáltatás az eredményeket JSON-formátumban jeleníti meg. Néhány kérelem-keretrendszer nem kompatibilis alapértelmezett értéket biztosít. Javasoljuk, hogy mindig vegyen fel `Accept`. | Nem kötelező, de ajánlott. |

## <a name="audio-formats"></a>Hangformátumok

A hang a HTTP `POST` kérelem törzsében lesz elküldve. Ennek a táblázatnak az egyik formátumában kell szerepelnie:

| Formátum | Codec | Sávszélességű | Mintavételezési arány |
|--------|-------|---------|-------------|
| WAV | PCM | 16 bites | 16 kHz, monó |
| VORBIS | OPUS | 16 bites | 16 kHz, monó |

>[!NOTE]
>A fenti formátumok a Speech Service REST API és WebSocket szolgáltatásán keresztül támogatottak. A [SPEECH SDK](speech-sdk.md) jelenleg csak a WAV formátumot támogatja a PCM-kodekkel.

## <a name="sample-request"></a>Példa a kérelemre

Az alábbi minta tartalmazza az állomásnév és a szükséges fejléceket. Fontos megjegyezni, hogy a szolgáltatás a hangadatokat is várja, amelyek nem szerepelnek ebben a mintában. Ahogy korábban említettük, a darabolás javasolt, de nem kötelező.

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

Az egyes válaszok HTTP-állapotkód sikeres vagy gyakori hibákat jelez.

| HTTP-állapotkód | Leírás | Lehetséges ok |
|------------------|-------------|-----------------|
| 100 | Folytatás | A kezdeti kérelem el lett fogadva. Folytassa a további adatok küldésével. (Darabolásos átvitelsel használatos.) |
| 200 | OK | A kérelem sikeres volt; a válasz törzse egy JSON-objektum. |
| 400 | Hibás kérelem | A nyelvi kód nincs megadva, nem támogatott nyelv, érvénytelen hangfájl stb. |
| 401 | Nem engedélyezett | Az előfizetési kulcs vagy az engedélyezési jogkivonat érvénytelen a megadott régióban, vagy érvénytelen végpont. |
| 403 | Forbidden | Hiányzó előfizetési kulcs vagy engedélyezési jogkivonat. |

## <a name="chunked-transfer"></a>Darabolásos átvitel

A darabolásos átvitel (`Transfer-Encoding: chunked`) segítségével csökkentheti az elismerés késését. Lehetővé teszi, hogy a beszédfelismerési szolgáltatás megkezdje a hangfájl feldolgozását a továbbítás során. A REST API nem biztosít részleges vagy időközi eredményeket.

Ez a mintakód azt mutatja be, hogyan lehet hangot küldeni a darabokban. Csak az első részletnek kell tartalmaznia a hangfájl fejlécét. `request` egy HTTPWebRequest objektum, amely a megfelelő REST-végponthoz van csatlakoztatva. `audioFile` a lemezen lévő hangfájl elérési útja.

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

## <a name="response-parameters"></a>Válasz paraméterei

Az eredmények JSON-ként vannak megadva. A `simple` formátum tartalmazza ezeket a legfelső szintű mezőket.

| Paraméter | Leírás  |
|-----------|--------------|
|`RecognitionStatus`|Állapot, például `Success` a sikeres felismeréshez. Lásd a következő táblázatot.|
|`DisplayText`|A felismert szöveg a tőkésítés, a központozás, az inverz szöveg normalizálása (a szóbeli szöveg konvertálása rövidebb formátumokra, például 200 a "200" vagy "Dr. Smith" kifejezésre a "Doctor Smith" esetében) és a káromkodás maszkolása után. Csak sikeres.|
|`Offset`|Az az idő (100-ns egységben), amikor a felismert beszéd az audio streamben kezdődik.|
|`Duration`|Az audio streamben felismert beszéd időtartama (100-ns egységben).|

A `RecognitionStatus` mező a következő értékeket tartalmazhatja:

| Állapot | Leírás |
|--------|-------------|
| `Success` | Az felismerés sikeres volt, és a `DisplayText` mező van jelen. |
| `NoMatch` | A rendszer beszédet észlelt az audio streamben, de a célnyelv nem felelt meg a szavaknak. Általában azt jelenti, hogy az elismerés nyelve egy másik nyelv, amelyet a felhasználó beszél. |
| `InitialSilenceTimeout` | A hangadatfolyam kezdete csak csendet foglalt, és a szolgáltatás a beszédfelismerésre való várakozás során időtúllépést várt. |
| `BabbleTimeout` | A hangadatfolyam elején csak a zaj szerepel, és a szolgáltatás a beszédfelismerésre való várakozás során időtúllépést okozott. |
| `Error` | A felismerési szolgáltatás belső hibát észlelt, és nem folytatható. Próbálkozzon újra, ha lehetséges. |

> [!NOTE]
> Ha a hang csak a káromkodásból áll, és a `profanity` lekérdezési paraméter értéke `remove`, a szolgáltatás nem ad vissza beszédfelismerési eredményt.

A `detailed` formátum ugyanazokat az adat`simple` formátumot tartalmazza, mint a `NBest`, valamint az azonos felismerési eredmény alternatív értelmezéseit tartalmazó lista. Ezek az eredmények a legvalószínűbbtől a legkevésbé valószínűtől lesznek rangsorolva. Az első bejegyzés ugyanaz, mint a fő felismerési eredmény.  A `detailed` formátum használatakor a rendszer a `NBest` lista minden egyes eredményének `Display`ként `DisplayText`.

A `NBest` listán szereplő összes objektum a következőket tartalmazza:

| Paraméter | Leírás |
|-----------|-------------|
| `Confidence` | A 0,0-es bejegyzés megbízhatósági pontszáma (nincs megbízhatóság) 1,0-re (teljes biztonság) |
| `Lexical` | A felismert szöveg lexikális formája: a tényleges szavak felismerése. |
| `ITN` | A felismert szöveg, a telefonszámok, a számok, a rövidítések ("doktor Smith" – "Dr Smith") és az alkalmazott egyéb átalakítások inverz szövege. |
| `MaskedITN` | A ITN űrlap a káromkodás maszkolásával lett alkalmazva, ha szükséges. |
| `Display` | A felismert szöveg megjelenítési formája, írásjelekkel és nagybetűkkel kiegészítve. Ez a paraméter ugyanaz, mint a `DisplayText`, ha a formátum értéke `simple`. |

## <a name="sample-responses"></a>Mintául szolgáló válaszok

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
