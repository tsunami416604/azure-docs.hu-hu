---
title: Szövegfelolvasó API-hivatkozás (REST) – Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használhatja a szöveg-beszéd REST API-t. Ebben a cikkben megtudhatja, engedélyezési beállítások, lekérdezési beállítások, hogyan strukturálhatja a kérelmet, és választ kap.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: dapine
ms.openlocfilehash: 977c6ec9aa1cd6a8b8a545d123c5308bb8c51651
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409023"
---
# <a name="text-to-speech-rest-api"></a>Szövegfelolvasás REST API

A beszédfelismerési szolgáltatás lehetővé teszi a [szöveg szintetizált beszédté alakítását,](#convert-text-to-speech) és egy régió [támogatott hanglistájának leolvasását](#get-a-list-of-voices) a REST API-k készlete használatával. Minden elérhető végpont egy régióhoz van társítva. A használni kívánt végponthoz/régióhoz előfizetési kulcs szükséges.

A szövegfelolvasó REST API támogatja a neurális és a szabványos szöveg-beszéd hangokat, amelyek mindegyike támogat egy adott nyelvet és dialektust, amelyet a területi beállítások azonosítanak.

* A hangok teljes listáját a [nyelvi támogatás .](language-support.md#text-to-speech)
* A területi elérhetőségről a régiók című [témakörben talál](regions.md#text-to-speech)további információt.

> [!IMPORTANT]
> A költségek eltérőek a szabványos, egyéni és neurális hangok esetében. További információ: [Pricing](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Az API használata előtt a következőket értse meg:

* A szövegfelolvasó REST API-hoz engedélyezési fejléc szükséges. Ez azt jelenti, hogy a szolgáltatás eléréséhez végre kell töltenie egy jogkivonat-cserét. További információért lásd: [Hitelesítés](#authentication).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>A hangok listájának beszereznie

A `voices/list` végpont lehetővé teszi, hogy egy adott régió/végpont hangok teljes listáját.

### <a name="regions-and-endpoints"></a>Régiók és végpontok

| Régió | Végpont |
|--------|----------|
| Kelet-Ausztrália | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Dél-Brazília | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Közép-Kanada | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA középső régiója | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
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
| USA nyugati régiója, 2. | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

### <a name="request-headers"></a>Kérésfejlécek

Ez a táblázat a szövegfelolvasásra vonatkozó kérelmekhez szükséges és választható fejléceket sorolja fel.

| Fejléc | Leírás | Kötelező / Nem kötelező |
|--------|-------------|---------------------|
| `Authorization` | Engedélyezési jogkivonat, amelyet `Bearer`a szó előz meg. További információért lásd: [Hitelesítés](#authentication). | Kötelező |

### <a name="request-body"></a>A kérés törzse

A végpontra vonatkozó `GET` kérelmekhez nem szükséges törzs.

### <a name="sample-request"></a>Mintakérelem

Ez a kérelem csak egy engedélyezési fejlécet igényel.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Authorization: Bearer [Base64 access_token]
```

### <a name="sample-response"></a>Mintaválasz

Ez a válasz csonkolva van, hogy bemutassa a válasz szerkezetét.

> [!NOTE]
> A hang elérhetősége régiónként/végpontonként változik.

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

Az egyes válaszok HTTP-állapotkódja sikeres vagy gyakori hibákat jelez.

| HTTP-állapotkód | Leírás | Lehetséges ok |
|------------------|-------------|-----------------|
| 200 | OK | A kérés sikeres volt. |
| 400 | Hibás kérés | Hiányzik, üres vagy null értékű egy szükséges paraméter. Vagy a kötelező vagy választható paraméternek átadott érték érvénytelen. Gyakori probléma a túl hosszú fejléc. |
| 401 | Nem engedélyezett | A kérés nem engedélyezett. Ellenőrizze, hogy az előfizetési kulcs vagy jogkivonat érvényes-e a megfelelő régióban. |
| 429 | Túl sok kérelem | Túllépte az előfizetéséhez engedélyezett kérelmek kvótáját vagy díját. |
| 502 | Hibás átjáró    | Hálózati vagy kiszolgálóoldali probléma. Érvénytelen fejléceket is jelezhet. |


## <a name="convert-text-to-speech"></a>Szöveg átalakítása beszéddé

A `v1` végpont lehetővé teszi a szövegfelolvasás konvertálását [a beszédszintetizáló nyelvi (SSML)](speech-synthesis-markup.md)használatával.

### <a name="regions-and-endpoints"></a>Régiók és végpontok

Ezek a régiók a REST API használatával támogatottak a szövegfelolvasáshoz. Győződjön meg arról, hogy válassza ki a végpontot, amely megfelel az előfizetési régió.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Kérésfejlécek

Ez a táblázat a szövegfelolvasásra vonatkozó kérelmekhez szükséges és választható fejléceket sorolja fel.

| Fejléc | Leírás | Kötelező / Nem kötelező |
|--------|-------------|---------------------|
| `Authorization` | Engedélyezési jogkivonat, amelyet `Bearer`a szó előz meg. További információért lásd: [Hitelesítés](#authentication). | Kötelező |
| `Content-Type` | Megadja a megadott szöveg tartalomtípusát. Elfogadott érték: `application/ssml+xml`. | Kötelező |
| `X-Microsoft-OutputFormat` | A hangkimeneti formátumot adja meg. Az elfogadott értékek teljes listáját a [hangkimenetek ben láthatja.](#audio-outputs) | Kötelező |
| `User-Agent` | Az alkalmazás neve. A megadott értéknek 255 karakternél kisebbnek kell lennie. | Kötelező |

### <a name="audio-outputs"></a>Hangkimenetek

Ez a lista a támogatott hangformátumok, amelyek küldött `X-Microsoft-OutputFormat` minden kérelmet, mint a fejléc. Mindegyik tartalmaz egy bitráta és kódolási típus. A Beszédszolgáltatás támogatja a 24 kHz-es, 16 kHz-es és 8 kHz-es hangkimeneteket.

|||
|-|-|
| `raw-16khz-16bit-mono-pcm` | `raw-8khz-8bit-mono-mulaw` |
| `riff-8khz-8bit-mono-alaw` | `riff-8khz-8bit-mono-mulaw` |
| `riff-16khz-16bit-mono-pcm` | `audio-16khz-128kbitrate-mono-mp3` |
| `audio-16khz-64kbitrate-mono-mp3` | `audio-16khz-32kbitrate-mono-mp3` |
| `raw-24khz-16bit-mono-pcm` | `riff-24khz-16bit-mono-pcm` |
| `audio-24khz-160kbitrate-mono-mp3` | `audio-24khz-96kbitrate-mono-mp3` |
| `audio-24khz-48kbitrate-mono-mp3` | |

> [!NOTE]
> Ha a kiválasztott hang- és kimeneti formátum eltérő átviteli sebességgel rendelkezik, a hang szükség szerint újramintavételezésre kerül. A 24 kHz-es `audio-16khz-16kbps-mono-siren` hangok `riff-16khz-16kbps-mono-siren` azonban nem támogatják a kimeneti formátumokat.

### <a name="request-body"></a>A kérés törzse

Az egyes `POST` kérelmek törzse [beszédszintetizáló nyelvi (SSML)](speech-synthesis-markup.md)néven kerül elküldésre. Az SSML lehetővé teszi a szövegfelolvasó szolgáltatás által visszaadott szintetizált beszéd hangjának és nyelvének kiválasztását. A támogatott hangok teljes listáját a nyelvi támogatás című témakörben [tési területen láthatja.](language-support.md#text-to-speech)

> [!NOTE]
> Ha egyéni hangot használ, a kérelem törzse egyszerű szövegként elküldhető (ASCII vagy UTF-8).

### <a name="sample-request"></a>Mintakérelem

Ez a HTTP-kérelem SSML-t használ a hang és a nyelv megadásához. Ha a test hossza hosszú, és az eredményül kapott hang meghaladja a 10 percet - akkor 10 percre csonkolja. Más szóval a hanghossz nem haladhatja meg a 10 percet.

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

Tekintse meg rövid útmutatóinkat a nyelvspecifikus példákért:

* [.NET mag, C #](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=dotnetcore)
* [Python](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-python)
* [Node.js](quickstart-nodejs-text-to-speech.md)

### <a name="http-status-codes"></a>HTTP-állapotkódok

Az egyes válaszok HTTP-állapotkódja sikeres vagy gyakori hibákat jelez.

| HTTP-állapotkód | Leírás | Lehetséges ok |
|------------------|-------------|-----------------|
| 200 | OK | A kérés sikeres volt; a választörzs egy hangfájl. |
| 400 | Hibás kérés | Hiányzik, üres vagy null értékű egy szükséges paraméter. Vagy a kötelező vagy választható paraméternek átadott érték érvénytelen. Gyakori probléma a túl hosszú fejléc. |
| 401 | Nem engedélyezett | A kérés nem engedélyezett. Ellenőrizze, hogy az előfizetési kulcs vagy jogkivonat érvényes-e a megfelelő régióban. |
| 413 | A kérelem entitása túl nagy | Az SSML-bemenet 1024 karakternél hosszabb. |
| 415 | Nem támogatott adathordozó-típus | Lehetséges, hogy a `Content-Type` rossz volt biztosított. `Content-Type`a beállítását `application/ssml+xml`a beállításra kell állítani. |
| 429 | Túl sok kérelem | Túllépte az előfizetéséhez engedélyezett kérelmek kvótáját vagy díját. |
| 502 | Hibás átjáró    | Hálózati vagy kiszolgálóoldali probléma. Érvénytelen fejléceket is jelezhet. |

Ha a HTTP-állapot a `200 OK`, a válasz törzse a kért formátumban lévő hangfájlt tartalmaz. Ez a fájl átvihető, pufferbe menthető vagy fájlba menthető.

## <a name="next-steps"></a>További lépések

- [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services)
- [Aszinkron szintézis a hosszú formátumú hang](quickstarts/text-to-speech/async-synthesis-long-form-audio.md)
- [Bevezetés a Custom Voice szolgáltatásba](how-to-custom-voice.md)
