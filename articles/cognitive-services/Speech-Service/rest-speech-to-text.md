---
title: Beszéd és szöveg közötti API-referenciák (REST) – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan használhatja a beszéd – szöveg REST API. Ebben a cikkben megismerheti az engedélyezési lehetőségeket, a lekérdezési lehetőségeket, a kérések strukturálása és a válasz fogadását.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: yinhew
ms.openlocfilehash: 2f102199c14ba9611a83e3ed3b31ebcd189624d6
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82978620"
---
# <a name="speech-to-text-rest-api"></a>Diktálás REST API

A Speech [SDK](speech-sdk.md)alternatívájaként a beszédfelismerési szolgáltatás lehetővé teszi, hogy REST API használatával alakítsa át a beszédfelismerést a szöveggé. Minden elérhető végpont egy régióhoz van társítva. Az alkalmazáshoz szükség van egy előfizetési kulcsra a használni kívánt végponthoz. A REST API nagyon korlátozott, és csak abban az esetben használható, ha a [SPEECH SDK](speech-sdk.md) nem.

A beszéd – szöveg REST API használata előtt értse fel a következőket:

* A REST APIt használó és a hang közvetlen továbbítására szolgáló kérelmek legfeljebb 60 másodperces hangot tartalmazhatnak.
* A beszéd – szöveg REST API csak a végső eredményeket adja vissza. A részleges eredmények nincsenek megadva.

Ha a hosszú hang küldése az alkalmazásra vonatkozó követelmény, érdemes lehet használni a [SPEECH SDK](speech-sdk.md) -t vagy egy file-alapú REST API, például a [Batch átírását](batch-transcription.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Régiók és végpontok

A REST API végpontjának formátuma a következő:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Cserélje `<REGION_IDENTIFIER>` le az elemet az előfizetés régiójának megfelelő azonosítóra ebből a táblából:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> A nyelvi paramétert az URL-címhez kell hozzáfűzni, hogy ne kapjon 4xx HTTP-hibát. Az USA nyugati végpontját használó angol nyelv például a következő: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`.

## <a name="query-parameters"></a>Lekérdezési paraméterek

Ezek a paraméterek szerepelhetnek a REST-kérelem lekérdezési karakterláncában.

| Paraméter | Leírás | Kötelező/nem kötelező |
|-----------|-------------|---------------------|
| `language` | Azonosítja a felismert nyelvet. Lásd: [támogatott nyelvek](language-support.md#speech-to-text). | Kötelező |
| `format` | Megadja az eredmény formátumát. Az `simple` elfogadott értékek: `detailed`és. Az egyszerű eredmények `RecognitionStatus`közé `DisplayText`tartoznak `Offset`a következők `Duration`:,, és. A részletes válaszok a megjelenítendő szöveg négy különböző ábrázolását tartalmazzák. Az alapértelmezett beállítás: `simple`. | Optional |
| `profanity` | Meghatározza, hogyan kezelhető a káromkodás az eredmények felismerésében. Az elfogadott értékek `masked`olyanok, amelyek a káromkodást helyettesítik a csillagokkal, `removed`ami eltávolítja az eredményből az `raw`összes trágár elemet, vagy az eredménybe beletartozik a káromkodás is. Az alapértelmezett beállítás: `masked`. | Optional |
| `pronunciationScoreParams` | Azokat a paramétereket adja meg, amelyekkel megjelenítheti a kiejtési pontszámokat az elismerés eredményei között, amelyek felmérik a beszédfelismerés kiejtési minőségét, pontossággal, gördülékenyen, teljességgel stb. Ez a paraméter egy Base64 kódolású JSON, amely több részletes paramétert tartalmaz. A paraméter kiépítéséhez lásd a [kiejtés értékelési paramétereit](#pronunciation-assessment-parameters) . | Optional |
| `cid` | Ha egyéni modelleket hoz létre a [Custom Speech-portálon](how-to-custom-speech.md) , egyéni modelleket használhat a **telepítési** oldalon található **végpont-azonosítón** keresztül. Használja a **VÉGPONT azonosítóját** a `cid` lekérdezési karakterlánc paraméter argumentumaként. | Optional |

## <a name="request-headers"></a>Kérésfejlécek

Ez a táblázat a beszédfelismerési kérelmekhez szükséges és nem kötelező fejléceket sorolja fel.

|Fejléc| Leírás | Kötelező/nem kötelező |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | A beszédfelismerési szolgáltatás előfizetési kulcsa. | Ez a fejléc vagy `Authorization` kötelező. |
| `Authorization` | A szó `Bearer`előtt egy engedélyezési jogkivonat. További információért lásd: [Hitelesítés](#authentication). | Ez a fejléc vagy `Ocp-Apim-Subscription-Key` kötelező. |
| `Content-type` | A megadott hangadatok formátumát és kodekjét ismerteti. Az `audio/wav; codecs=audio/pcm; samplerate=16000` elfogadott értékek: `audio/ogg; codecs=opus`és. | Kötelező |
| `Transfer-Encoding` | Meghatározza, hogy a rendszer a darabolásos hangadatokat egyetlen fájl helyett elküldje. Csak a hangadatok darabolásakor használja ezt a fejlécet. | Optional |
| `Expect` | Ha darabolásos átvitelt használ, `Expect: 100-continue`küldje el. A beszédfelismerési szolgáltatás tudomásul veszi a kezdeti kérést, és további adatra vár.| A darabolásos hangadatok küldéséhez szükséges. |
| `Accept` | Ha meg van adni, azt `application/json`kötelező megadni. A beszédfelismerési szolgáltatás az eredményeket JSON-formátumban jeleníti meg. Néhány kérelem-keretrendszer nem kompatibilis alapértelmezett értéket biztosít. Célszerű mindig belefoglalni `Accept`. | Nem kötelező, de ajánlott. |

## <a name="audio-formats"></a>Hangformátumok

A hang a HTTP `POST` -kérelem törzsében lesz elküldve. Ennek a táblázatnak az egyik formátumában kell szerepelnie:

| Formátum | Codec | Átviteli sebesség | Mintavételezési arány  |
|--------|-------|----------|--------------|
| WAV    | PCM   | 256 kbps | 16 kHz, monó |
| VORBIS    | OPUS  | 256 kpbs | 16 kHz, monó |

>[!NOTE]
>A fenti formátumok a Speech Service REST API és WebSocket szolgáltatásán keresztül támogatottak. A [SPEECH SDK](speech-sdk.md) jelenleg a WAV formátumot támogatja a PCM-kodekkel és [más formátumokkal](how-to-use-codec-compressed-audio-input-streams.md).

## <a name="pronunciation-assessment-parameters"></a>Kiejtés-értékelési paraméterek

Ez a táblázat a kiejtés értékeléséhez szükséges és választható paramétereket sorolja fel.

| Paraméter | Leírás | Kötelező/nem kötelező |
|-----------|-------------|---------------------|
| ReferenceText | A kiejtés kiértékeléséhez használandó szöveg | Kötelező |
| GradingSystem | A pontszám kalibrációs pontjának rendszere. Az `FivePoint` elfogadott értékek: `HundredMark`és. Az alapértelmezett beállítás: `FivePoint`. | Optional |
| Részletesség | A kiértékelés részletessége. Az elfogadott értékek `Phoneme`a teljes szöveges, a Word-és a fonéma-szinten `Word`látható pontszám, amely a teljes szöveg és a szó szintjén `FullText`mutatja a pontszámot, amely a teljes szöveges szint pontszámát mutatja. Az alapértelmezett beállítás: `Phoneme`. | Optional |
| Dimenzió | Meghatározza a kimeneti feltételeket. Az elfogadott értékek `Basic`, amelyek csak a pontossági pontszámot mutatják `Comprehensive` , több dimenzióban jelenítik meg a pontszámokat (pl. a teljes szöveges szinten a teljességi pontszám és a teljesség pontszáma, a hiba típusa a szó szintjén). Ellenőrizze a [Válasz paramétereit](#response-parameters) , és tekintse meg a különböző pontszám-dimenziók és a Word-hibák definícióit. Az alapértelmezett beállítás: `Basic`. | Optional |
| EnableMiscue | Engedélyezi a gikszer számítását. Ha ez a beállítás engedélyezve van, a rendszer összehasonlítja a kiejtett szavakat a hivatkozási szöveggel, és az összehasonlítás alapján kihagyási/beszúrási művelettel jelöli meg őket. Az `False` elfogadott értékek: `True`és. Az alapértelmezett beállítás: `False`. | Optional |
| ScenarioId | Egy testreszabott pont rendszerét jelző GUID. | Optional |

Alább látható egy példa a kiejtés-értékelési paramétereket tartalmazó JSON-ra:

```json
{
  "ReferenceText": "Good morning.",
  "GradingSystem": "HundredMark",
  "Granularity": "FullText",
  "Dimension": "Comprehensive"
}
```

Az alábbi mintakód bemutatja, hogyan építheti ki a kiejtés-értékelési paramétereket az URL-lekérdezési paraméterbe:

```csharp
var pronunciationScoreParamsJson = $"{{\"ReferenceText\":\"Good morning.\",\"GradingSystem\":\"HundredMark\",\"Granularity\":\"FullText\",\"Dimension\":\"Comprehensive\"}}";
var pronunciationScoreParamsBytes = Encoding.UTF8.GetBytes(pronunciationScoreParamsJson);
var pronunciationScoreParams = Convert.ToBase64String(pronunciationScoreParamsBytes);
```

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
| `100` | Folytatás | A kezdeti kérelem el lett fogadva. Folytassa a további adatok küldésével. (Darabolásos átvitelsel használatos) |
| `200` | OK | A kérelem sikeres volt; a válasz törzse egy JSON-objektum. |
| `400` | Hibás kérelem | A nyelvi kód nincs megadva, nem támogatott nyelv, érvénytelen hangfájl stb. |
| `401` | Nem engedélyezett | Az előfizetési kulcs vagy az engedélyezési jogkivonat érvénytelen a megadott régióban, vagy érvénytelen végpont. |
| `403` | Forbidden | Hiányzó előfizetési kulcs vagy engedélyezési jogkivonat. |

## <a name="chunked-transfer"></a>Darabolásos átvitel

A darabolásos átvitel`Transfer-Encoding: chunked`() segítségével csökkentheti az elismerés késését. Lehetővé teszi, hogy a beszédfelismerési szolgáltatás megkezdje a hangfájl feldolgozását a továbbítás során. A REST API nem biztosít részleges vagy időközi eredményeket.

Ez a mintakód azt mutatja be, hogyan lehet hangot küldeni a darabokban. Csak az első részletnek kell tartalmaznia a hangfájl fejlécét. `request`egy `HttpWebRequest` olyan objektum, amely a megfelelő Rest-végponthoz van csatlakoztatva. `audioFile`a lemezen lévő hangfájl elérési útja.

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

Az eredmények JSON-ként vannak megadva. A `simple` formátum tartalmazza ezeket a legfelső szintű mezőket.

| Paraméter | Leírás  |
|-----------|--------------|
|`RecognitionStatus`|Állapot, például `Success` sikeres felismeréshez. Lásd a következő táblázatot.|
|`DisplayText`|A felismert szöveg a tőkésítés, a központozás, az inverz szöveg normalizálása (a szóbeli szöveg konvertálása rövidebb formátumokra, például 200 a "200" vagy "Dr. Smith" kifejezésre a "Doctor Smith" esetében) és a káromkodás maszkolása után. Csak sikeres.|
|`Offset`|Az az idő (100-ns egységben), amikor a felismert beszéd az audio streamben kezdődik.|
|`Duration`|Az audio streamben felismert beszéd időtartama (100-ns egységben).|

A `RecognitionStatus` mező a következő értékeket tartalmazhatja:

| status | Leírás |
|--------|-------------|
| `Success` | Az felismerés sikeres volt, és `DisplayText` a mező jelen van. |
| `NoMatch` | A rendszer beszédet észlelt az audio streamben, de a célnyelv nem felelt meg a szavaknak. Általában azt jelenti, hogy az elismerés nyelve egy másik nyelv, amelyet a felhasználó beszél. |
| `InitialSilenceTimeout` | A hangadatfolyam kezdete csak csendet foglalt, és a szolgáltatás a beszédfelismerésre való várakozás során időtúllépést várt. |
| `BabbleTimeout` | A hangadatfolyam elején csak a zaj szerepel, és a szolgáltatás a beszédfelismerésre való várakozás során időtúllépést okozott. |
| `Error` | A felismerési szolgáltatás belső hibát észlelt, és nem folytatható. Próbálkozzon újra, ha lehetséges. |

> [!NOTE]
> Ha a hang csak a káromkodásból áll, és a `profanity` lekérdezési paraméter értéke `remove`, akkor a szolgáltatás nem ad vissza beszédfelismerési eredményt.

A `detailed` formátum a felismert eredmények további formáit is tartalmazza.
A `detailed` formátum használatakor a `DisplayText` a `NBest` lista minden `Display` eredményének megfelelően van megadva.

A `NBest` listában szereplő objektum a következőket tartalmazhatja:

| Paraméter | Leírás |
|-----------|-------------|
| `Confidence` | A 0,0-es bejegyzés megbízhatósági pontszáma (nincs megbízhatóság) 1,0-re (teljes biztonság) |
| `Lexical` | A felismert szöveg lexikális formája: a tényleges szavak felismerése. |
| `ITN` | A felismert szöveg, a telefonszámok, a számok, a rövidítések ("doktor Smith" – "Dr Smith") és az alkalmazott egyéb átalakítások inverz szövege. |
| `MaskedITN` | A ITN űrlap a káromkodás maszkolásával lett alkalmazva, ha szükséges. |
| `Display` | A felismert szöveg megjelenítési formája, írásjelekkel és nagybetűkkel kiegészítve. Ez a paraméter megegyezik a formátum `DisplayText` beállításakor megadott értékkel `simple`. |
| `AccuracyScore` | A megadott beszéd kiejtési pontosságát jelző pontszám |
| `FluencyScore` | A megadott beszéd folyékonyan megjelenő pontszáma. |
| `CompletenessScore` | Az adott beszéd teljességét jelző pontszám a Kiejtett szavak arányának a teljes bemenetre való kiszámításával. |
| `PronScore` | Az adott beszéd kiejtési minőségét jelző összesített pontszám. A számítás a `AccuracyScore` `FluencyScore` és `CompletenessScore` a súlyozás alapján történik. |
| `ErrorType` | Ez az érték azt jelzi, hogy egy szó ki van-e hagyva, be `ReferenceText`van-e jelölve vagy rosszul van-e kiválasztva. A lehetséges értékek `None` : (azaz nincs hiba ezen a szónak `Omission`) `Insertion` , `Mispronunciation`és. |

## <a name="sample-responses"></a>Mintául szolgáló válaszok

Egy tipikus válasz az `simple` elismerésre:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Egy tipikus válasz az `detailed` elismerésre:

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
      }
  ]
}
```

Egy tipikus válasz az elismeréshez a kiejtés értékelésével:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "400000",
  "Duration": "11000000",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "good morning",
        "ITN" : "good morning",
        "MaskedITN" : "good morning",
        "Display" : "Good morning.",
        "PronScore" : 84.4,
        "AccuracyScore" : 100.0,
        "FluencyScore" : 74.0,
        "CompletenessScore" : 100.0,
        "Words": [
            {
              "Word" : "Good",
              "AccuracyScore" : 100.0,
              "ErrorType" : "None",
              "Offset" : 500000,
              "Duration" : 2700000
            },
            {
              "Word" : "morning",
              "AccuracyScore" : 100.0,
              "ErrorType" : "None",
              "Offset" : 5300000,
              "Duration" : 900000
            }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>További lépések

- [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
