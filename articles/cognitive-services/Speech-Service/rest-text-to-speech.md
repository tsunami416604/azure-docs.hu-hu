---
title: Szöveg – beszéd API-referenciák (REST) – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan használhatja a szöveg – beszéd REST API. Ebben a cikkben megismerheti az engedélyezési lehetőségeket, a lekérdezési lehetőségeket, a kérések strukturálása és a válasz fogadását.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.openlocfilehash: 77a8321ba8bac0ecaf577bce6c3c05d10508128e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2020
ms.locfileid: "96020160"
---
# <a name="text-to-speech-rest-api"></a>Szövegfelolvasás REST API

A beszédfelismerési szolgáltatás lehetővé teszi, hogy [szövegeket Konvertáljon a szintetizált beszédbe](#convert-text-to-speech) , és [lekérje a támogatott hangok LISTÁJÁT](#get-a-list-of-voices) egy régió REST API-k használatával. Minden elérhető végpont egy régióhoz van társítva. A használni kívánt végpont/régió előfizetési kulcsának megadása kötelező.

A szöveg-beszéd REST API támogatja a neurális és a szabványos szöveg-beszéd hangokat, amelyek mindegyike egy adott nyelvet és dialektust támogat, területi beállítással azonosítva.

* A hangok teljes listáját lásd: [nyelvi támogatás](language-support.md#text-to-speech).
* További információ a regionális elérhetőségről: [régiók](regions.md#text-to-speech).

> [!IMPORTANT]
> A költségek a standard, az egyéni és a neurális hangok esetében eltérőek. További információ: [díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Az API használata előtt Ismerje meg a következőket:

* A szöveg-beszéd REST API engedélyezési fejlécet igényel. Ez azt jelenti, hogy a szolgáltatás eléréséhez ki kell fejeznie egy jogkivonat-Exchange-t. További információért lásd: [Hitelesítés](#authentication).

> [!TIP]
> Lásd: az Azure Government [dokumentációja](../../azure-government/compare-azure-government-global-azure.md) a Government Cloud (FairFax) végpontokhoz.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Hangok listájának beolvasása

A `voices/list` végpont lehetővé teszi, hogy egy adott régióhoz vagy végponthoz tartozó hangokat teljes listát kapjon.

### <a name="regions-and-endpoints"></a>Régiók és végpontok

| Region | Végpont |
|--------|----------|
| Kelet-Ausztrália | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Dél-Brazília | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Közép-Kanada | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Az USA középső régiója | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Kelet-Ázsia | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA keleti régiója | `https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA 2. keleti régiója | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Közép-Franciaország | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Közép-India | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Kelet-Japán | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Dél-Korea középső régiója | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA északi középső régiója | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Észak-Európa | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA déli középső régiója | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Délkelet-Ázsia | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Az Egyesült Királyság déli régiója | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Nyugat-Európa | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA nyugati régiója | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA 2. nyugati régiója | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

### <a name="request-headers"></a>Kérésfejlécek

Ez a táblázat a szöveg-beszéd kérelmekhez szükséges és nem kötelező fejléceket sorolja fel.

| Fejléc | Leírás | Kötelező/nem kötelező |
|--------|-------------|---------------------|
| `Authorization` | A szó előtt egy engedélyezési jogkivonat `Bearer` . További információért lásd: [Hitelesítés](#authentication). | Kötelező |

### <a name="request-body"></a>A kérés törzse

A `GET` végpontra irányuló kérésekhez nincs szükség törzsre.

### <a name="sample-request"></a>Példa a kérelemre

Ehhez a kérelemhez csak engedélyezési fejléc szükséges.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Authorization: Bearer [Base64 access_token]
```

### <a name="sample-response"></a>Mintaválasz

Ez a válasz a válasz szerkezetének szemléltetésére lett csonkítva.

> [!NOTE]
> A hangalapú rendelkezésre állás régiónként vagy végpont szerint változik.

```json
[
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
        "ShortName": "ar-EG-Hoda",
        "Gender": "Female",
        "Locale": "ar-EG",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)",
        "ShortName": "ar-SA-Naayf",
        "Gender": "Male",
        "Locale": "ar-SA",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)",
        "ShortName": "bg-BG-Ivan",
        "Gender": "Male",
        "Locale": "bg-BG",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)",
        "ShortName": "ca-ES-HerenaRUS",
        "Gender": "Female",
        "Locale": "ca-ES",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (zh-CN, XiaoxiaoNeural)",
        "ShortName": "zh-CN-XiaoxiaoNeural",
        "Gender": "Female",
        "Locale": "zh-CN",
        "SampleRateHertz": "24000",
        "VoiceType": "Neural"
    },

    ...
]
```

### <a name="http-status-codes"></a>HTTP-állapotkódok

Az egyes válaszok HTTP-állapotkód sikeres vagy gyakori hibákat jelez.

| HTTP-állapotkód | Leírás | Lehetséges ok |
|------------------|-------------|-----------------|
| 200 | OK | A kérelem sikeres volt. |
| 400 | Hibás kérés | Egy kötelező paraméter hiányzik, üres vagy NULL értékű. Vagy a kötelező vagy választható paraméternek átadott érték érvénytelen. Gyakori probléma egy túl hosszú fejléc. |
| 401 | Nem engedélyezett | A kérés nincs engedélyezve. Győződjön meg arról, hogy az előfizetési kulcs vagy token érvényes, és a megfelelő régióban található. |
| 429 | Túl sok kérelem | Túllépte az előfizetéshez engedélyezett kvótát vagy kérelmek arányát. |
| 502 | Hibás átjáró    | Hálózati vagy kiszolgálóoldali probléma. Érvénytelen fejléceket is jelezhet. |


## <a name="convert-text-to-speech"></a>Szöveg átalakítása beszéddé

A `v1` végpont lehetővé teszi szöveg-beszéd átalakítását a [Speech szintézis Markup Language (SSML)](speech-synthesis-markup.md)használatával.

### <a name="regions-and-endpoints"></a>Régiók és végpontok

Ezek a régiók a REST API használatával történő szöveg-beszéd kommunikációhoz támogatottak. Győződjön meg arról, hogy az előfizetési régiójának megfelelő végpontot választotta.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Kérésfejlécek

Ez a táblázat a szöveg-beszéd kérelmekhez szükséges és nem kötelező fejléceket sorolja fel.

| Fejléc | Leírás | Kötelező/nem kötelező |
|--------|-------------|---------------------|
| `Authorization` | A szó előtt egy engedélyezési jogkivonat `Bearer` . További információért lásd: [Hitelesítés](#authentication). | Kötelező |
| `Content-Type` | Megadja a megadott szöveg tartalomtípusát. Elfogadott érték: `application/ssml+xml` . | Kötelező |
| `X-Microsoft-OutputFormat` | Megadja az audió kimeneti formátumot. Az elfogadott értékek teljes listájáért lásd: [hang kimenetek](#audio-outputs). | Kötelező |
| `User-Agent` | Az alkalmazás neve. A megadott értéknek 255 karakternél rövidebbnek kell lennie. | Kötelező |

### <a name="audio-outputs"></a>Hangkimenetek

Az egyes kérésekben a fejlécként elküldhető támogatott hangformátumok listája `X-Microsoft-OutputFormat` . Mindegyik tartalmaz egy bitrátát és egy kódolási típust. A Speech Service 24 kHz, 16 kHz és 8 kHz audió kimenetet támogat.

```output
raw-16khz-16bit-mono-pcm            raw-8khz-8bit-mono-mulaw
riff-8khz-8bit-mono-alaw            riff-8khz-8bit-mono-mulaw
riff-16khz-16bit-mono-pcm           audio-16khz-128kbitrate-mono-mp3
audio-16khz-64kbitrate-mono-mp3     audio-16khz-32kbitrate-mono-mp3
raw-24khz-16bit-mono-pcm            riff-24khz-16bit-mono-pcm
audio-24khz-160kbitrate-mono-mp3    audio-24khz-96kbitrate-mono-mp3
audio-24khz-48kbitrate-mono-mp3     ogg-24khz-16bit-mono-opus
```

> [!NOTE]
> Ha a kiválasztott hang-és kimeneti formátum eltérő átviteli sebességű, akkor a rendszer szükség szerint újratervezi a hangot. az OGG-24khz-16bit-mono-Opus dekódolása [Opus codec](https://opus-codec.org/downloads/) használatával

### <a name="request-body"></a>A kérés törzse

Az egyes kérések törzse a `POST` [Speech szintézis Markup Language (SSML) nyelven](speech-synthesis-markup.md)lesz elküldve. A SSML lehetővé teszi a szöveg-beszéd szolgáltatás által visszaadott szintetizált beszéd hangjának és nyelvének kiválasztását. A támogatott hangok teljes listáját a [nyelvi támogatás](language-support.md#text-to-speech)című témakörben tekintheti meg.

> [!NOTE]
> Egyéni hang használata esetén a kérelem törzse egyszerű szövegként (ASCII vagy UTF-8) is elküldhető.

### <a name="sample-request"></a>Példa a kérelemre

Ez a HTTP-kérelem SSML használ a hang és a nyelv megadásához. Ha a törzs hossza hosszú, és az eredményül kapott hang meghaladja a 10 percet, a rendszer 10 percre csonkolja. Más szóval az audió hossza nem haladhatja meg a 10 percet.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='en-US-AriaRUS'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

Tekintse meg a nyelvspecifikus példákat:

* [.NET Core, C #](./get-started-text-to-speech.md?pivots=programming-language-csharp&tabs=dotnetcore)
* [Python](./get-started-text-to-speech.md?pivots=programming-language-python)
* [Node.js](./get-started-text-to-speech.md)

### <a name="http-status-codes"></a>HTTP-állapotkódok

Az egyes válaszok HTTP-állapotkód sikeres vagy gyakori hibákat jelez.

| HTTP-állapotkód | Leírás | Lehetséges ok |
|------------------|-------------|-----------------|
| 200 | OK | A kérelem sikeres volt; a válasz törzse egy hangfájl. |
| 400 | Hibás kérés | Egy kötelező paraméter hiányzik, üres vagy NULL értékű. Vagy a kötelező vagy választható paraméternek átadott érték érvénytelen. Gyakori probléma egy túl hosszú fejléc. |
| 401 | Nem engedélyezett | A kérés nincs engedélyezve. Győződjön meg arról, hogy az előfizetési kulcs vagy token érvényes, és a megfelelő régióban található. |
| 413 | A kérelem entitása túl nagy | A SSML bemenete hosszabb 1024 karakternél. |
| 415 | Nem támogatott adathordozó-típus | Lehetséges, hogy a helytelen `Content-Type` volt megadva. `Content-Type` értékre kell állítani `application/ssml+xml` . |
| 429 | Túl sok kérelem | Túllépte az előfizetéshez engedélyezett kvótát vagy kérelmek arányát. |
| 502 | Hibás átjáró    | Hálózati vagy kiszolgálóoldali probléma. Érvénytelen fejléceket is jelezhet. |

Ha a HTTP-állapot értéke `200 OK` , a válasz törzse egy hangfájlt tartalmaz a kért formátumban. Ezt a fájlt áthelyezték, pufferbe mentve vagy fájlba mentve lehet lejátszani.

## <a name="next-steps"></a>További lépések

- [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/free/cognitive-services/)
- [Aszinkron szintézis a hosszú formátumú hangokhoz](./long-audio-api.md)
- [Bevezetés a Custom Voice szolgáltatásba](how-to-custom-voice.md)