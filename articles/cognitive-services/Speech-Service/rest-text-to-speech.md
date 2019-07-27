---
title: Szöveg – beszéd API-referenciák (REST) – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan használhatja a szöveg – beszéd REST API. Ebben a cikkben megismerkedhet engedélyezési beállítások, a lekérdezési beállítások, struktúra kérést és választ kapnak.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: b0a0d788c9fadd13b9a37f541a81945c86b37c29
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559168"
---
# <a name="text-to-speech-rest-api"></a>Szöveg – beszéd REST API

A beszédfelismerési szolgáltatások lehetővé teszik [szöveg konvertálását szintetizált beszédre](#convert-text-to-speech) , és egy adott régióhoz tartozó [támogatott hangok listáját](#get-a-list-of-voices) a REST API-k használatával. Minden elérhető végpont egy régióhoz van társítva. A használni kívánt végpont/régió előfizetési kulcsának megadása kötelező.

A szöveg-beszéd REST API támogatja a neurális és a szabványos szöveg-beszéd hangokat, amelyek mindegyike egy adott nyelvet és dialektust támogat, területi beállítással azonosítva.

* A hangok teljes listáját lásd: [nyelvi támogatás](language-support.md#text-to-speech).
* További információ a regionális elérhetőségről: [régiók](regions.md#text-to-speech).

> [!IMPORTANT]
> A költségek a standard, az egyéni és a neurális hangok esetében eltérőek. További információ: [díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Az API használata előtt Ismerje meg a következőket:

* A szöveg-hang transzformációs REST API-t igényel engedélyeztetési fejléc. Ez azt jelenti, hogy a szolgáltatás eléréséhez egy jogkivonatcsere végrehajtásához szükséges. További információért lásd: [Hitelesítés](#authentication).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Hangok listájának beolvasása

A `voices/list` végpont lehetővé teszi, hogy egy adott régióhoz vagy végponthoz tartozó hangokat teljes listát kapjon.

### <a name="regions-and-endpoints"></a>Régiók és végpontok

| Régió | Végpont |
|--------|----------|
| Kelet-Ausztrália | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Dél-Brazília | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Közép-Kanada | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA középső régiója | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Kelet-Ázsia | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| East US | `https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA 2. keleti régiója | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Közép-Franciaország | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Közép-India | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Kelet-Japán | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Korea középső régiója | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA északi középső régiója | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Észak-Európa | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA déli középső régiója | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Délkelet-Ázsia | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Az Egyesült Királyság déli régiója | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Nyugat-Európa | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA nyugati régiója | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA nyugati régiója, 2. | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

### <a name="request-headers"></a>Kérelemfejlécek

Ez a táblázat a szöveg-beszéd kérelmekhez szükséges és nem kötelező fejléceket sorolja fel.

| Fejléc | Leírás | Kötelező / választható |
|--------|-------------|---------------------|
| `Authorization` | Egy engedélyezési jogkivonatot előzi meg a word `Bearer`. További információért lásd: [Hitelesítés](#authentication). | Kötelező |

### <a name="request-body"></a>A kérés törzse

A végpontra `GET` irányuló kérésekhez nincs szükség törzsre.

### <a name="sample-request"></a>Mintakérelem

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
        "Locale": "ar-EG"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)",
        "ShortName": "ar-SA-Naayf",
        "Gender": "Male",
        "Locale": "ar-SA"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)",
        "ShortName": "bg-BG-Ivan",
        "Gender": "Male",
        "Locale": "bg-BG"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)",
        "ShortName": "ca-ES-HerenaRUS",
        "Gender": "Female",
        "Locale": "ca-ES"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (cs-CZ, Jakub)",
        "ShortName": "cs-CZ-Jakub",
        "Gender": "Male",
        "Locale": "cs-CZ"
    },

    ...

]
```

### <a name="http-status-codes"></a>HTTP-állapotkódok

A HTTP-állapotkód: minden válasz azt jelzi, hogy a sikeres vagy gyakori hibák.

| HTTP-állapotkód | Leírás | Lehetséges ok |
|------------------|-------------|-----------------|
| 200 | OK | A kérelem sikeres volt. |
| 400 | Hibás kérelem | Egy kötelező paraméter hiányzik, üres vagy null értékű. Másik lehetőségként átadott vagy egy kötelező vagy választható paraméter értéke érvénytelen. Egy gyakori probléma egy fejlécet, amely túl hosszú. |
| 401 | Nem engedélyezett | A kérelem nem engedélyezett. Ellenőrizze, hogy az előfizetési kulcs, vagy a jogkivonat érvényes, és a megfelelő régióban. |
| 429 | Túl sok kérelem | Túllépte a kvótát, vagy engedélyezett az előfizetéséhez kérelmek száma. |
| 502 | Hibás átjáró | Hálózati vagy kiszolgálóoldali probléma. Érvénytelen fejlécek is jelezhet. |


## <a name="convert-text-to-speech"></a>Szöveg átalakítása beszéddé

A `v1` végpont lehetővé teszi szöveg-beszéd átalakítását a [Speech szintézis Markup Language (SSML)](speech-synthesis-markup.md)használatával.

### <a name="regions-and-endpoints"></a>Régiók és végpontok

Ezekben a régiókban támogatottak, a szöveg-hang transzformációs a REST API használatával. Győződjön meg arról, hogy a végpontot, amely megfelel az előfizetés régiót választja.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Kérelemfejlécek

Ez a táblázat a szöveg-beszéd kérelmekhez szükséges és nem kötelező fejléceket sorolja fel.

| Fejléc | Leírás | Kötelező / választható |
|--------|-------------|---------------------|
| `Authorization` | Egy engedélyezési jogkivonatot előzi meg a word `Bearer`. További információért lásd: [Hitelesítés](#authentication). | Kötelező |
| `Content-Type` | A megadott szöveg a tartalom típusát határozza meg. Elfogadott érték: `application/ssml+xml`. | Szükséges |
| `X-Microsoft-OutputFormat` | A hangkimeneti formátum meghatározása. Elfogadott értékek teljes listáját lásd: [hang kimenetek](#audio-outputs). | Szükséges |
| `User-Agent` | Az alkalmazás neve. A megadott értéknek 255 karakternél rövidebbnek kell lennie. | Kötelező |

### <a name="audio-outputs"></a>Hang kimenetek

Ez a lista az egyes kérelmek, a küldött támogatott hangformátumok a `X-Microsoft-OutputFormat` fejléc. Minden egyes magában foglalja egy átviteli sebesség és a kódolási típusként. A Speech Services 24 kHz, 16 kHz és 8 kHz audió kimenetet támogat.

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
> Ha a kiválasztott hang- és kimeneti formátum különböző átviteli sebességet, a hanganyag szükség szerint módosítva a felbontása. Azonban a 24 kHz-es hangok nem `audio-16khz-16kbps-mono-siren` támogatják `riff-16khz-16kbps-mono-siren` a és a kimeneti formátumokat.

### <a name="request-body"></a>A kérés törzse

Az egyes `POST` kérések törzse a [Speech szintézis Markup Language (SSML) nyelven](speech-synthesis-markup.md)lesz elküldve. A SSML lehetővé teszi a szöveg-beszéd szolgáltatás által visszaadott szintetizált beszéd hangjának és nyelvének kiválasztását. A támogatott hangok teljes listáját a [nyelvi támogatás](language-support.md#text-to-speech)című témakörben tekintheti meg.

> [!NOTE]
> Egyéni hang használata esetén a kérelem törzse egyszerű szövegként (ASCII vagy UTF-8) is elküldhető.

### <a name="sample-request"></a>Mintakérelem

A HTTP-kérelem SSML beszédfelismerési és nyelvi megadásához használja. A szervezet legfeljebb 1000 karakter.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='en-US-JessaRUS'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

Tekintse meg a nyelvspecifikus példákat:

* [.NET Core,C#](quickstart-dotnet-text-to-speech.md)
* [Python](quickstart-python-text-to-speech.md)
* [Node.js](quickstart-nodejs-text-to-speech.md)

### <a name="http-status-codes"></a>HTTP-állapotkódok

A HTTP-állapotkód: minden válasz azt jelzi, hogy a sikeres vagy gyakori hibák.

| HTTP-állapotkód | Leírás | Lehetséges ok |
|------------------|-------------|-----------------|
| 200 | OK | A kérelem sikeres volt. a választörzs hangfájl mérete. |
| 400 | Hibás kérelem | Egy kötelező paraméter hiányzik, üres vagy null értékű. Másik lehetőségként átadott vagy egy kötelező vagy választható paraméter értéke érvénytelen. Egy gyakori probléma egy fejlécet, amely túl hosszú. |
| 401 | Nem engedélyezett | A kérelem nem engedélyezett. Ellenőrizze, hogy az előfizetési kulcs, vagy a jogkivonat érvényes, és a megfelelő régióban. |
| 413 | Kérelem az entitás túl nagy | A SSML bemeneti adat 1024 karakternél hosszabb. |
| 415 | Nem támogatott adathordozó-típus | Lehetséges, hogy a helytelen `Content-Type` volt megadva. `Content-Type`értékre kell állítani `application/ssml+xml`. |
| 429 | Túl sok kérelem | Túllépte a kvótát, vagy engedélyezett az előfizetéséhez kérelmek száma. |
| 502 | Hibás átjáró | Hálózati vagy kiszolgálóoldali probléma. Érvénytelen fejlécek is jelezhet. |

Ha a HTTP-állapot `200 OK`, a válasz törzse tartalmazza a kért formátumban hangfájl. Ez a fájl átvitele, a puffer mentett, vagy egy fájlba menti, lejátszhatók.

## <a name="next-steps"></a>További lépések

- [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
