---
title: Szöveg-hang transzformációs API-referencia (REST) – beszédszolgáltatások
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogy a szöveg-hang transzformációs REST API használatával. Ebben a cikkben megismerkedhet engedélyezési beállítások, a lekérdezési beállítások, struktúra kérést és választ kapnak.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: d687665a7f9b18d42dcd45953c15d2fbc6d71d2f
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58335827"
---
# <a name="text-to-speech-rest-api"></a>Szöveg-hang transzformációs REST API-val

A beszédszolgáltatások átalakítandó szöveg-hang transzformációs egy REST API használatával teszi lehetővé. Minden elérhető végpontot nem egy régió tartozik. Az alkalmazás egy előfizetési kulcsot szeretné használni a végpont szükséges. A szöveg-hang transzformációs REST API támogatja a Neurális és a standard szintű szöveg-hang transzformációs beszédhangot, amelyek mindegyike támogatja egy adott nyelvhez és dialect, területi beállítás azonosítjuk.

* Beszédhangot teljes listáját lásd: [nyelvi támogatás](language-support.md#text-to-speech).
* Régiónkénti rendelkezésre állás kapcsolatos információkért lásd: [régiók](regions.md#text-to-speech).

> [!IMPORTANT]
> Költségek szabványos, egyéni és Neurális beszédhangot eltérőek. További információkért lásd: [díjszabási](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Az API-t használ, mielőtt megismerése:

* A szöveg-hang transzformációs REST API-t igényel engedélyeztetési fejléc. Ez azt jelenti, hogy a szolgáltatás eléréséhez egy jogkivonatcsere végrehajtásához szükséges. További információért lásd: [Hitelesítés](#authentication).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Régiók és végpontok

Ezekben a régiókban támogatottak hang-szöveg transzformációs beszédátírási a REST API használatával. Győződjön meg arról, hogy a végpontot, amely megfelel az előfizetés régiót választja.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

## <a name="request-headers"></a>Kérelemfejlécek

Ez a táblázat felsorolja a szükséges és választható fejlécek a hang-szöveg transzformációs kéréseket.

| Fejléc | Leírás | Kötelező / választható |
|--------|-------------|---------------------|
| `Authorization` | Egy engedélyezési jogkivonatot előzi meg a word `Bearer`. További információért lásd: [Hitelesítés](#authentication). | Szükséges |
| `Content-Type` | A megadott szöveg a tartalom típusát határozza meg. Elfogadott érték: `application/ssml+xml`. | Szükséges |
| `X-Microsoft-OutputFormat` | A hangkimeneti formátum meghatározása. Elfogadott értékek teljes listáját lásd: [hang kimenetek](#audio-outputs). | Szükséges |
| `User-Agent` | Az alkalmazás neve. A megadott érték legfeljebb 255 karakter hosszú lehet. | Szükséges |

## <a name="audio-outputs"></a>Hang kimenetek

Ez a lista az egyes kérelmek, a küldött támogatott hangformátumok a `X-Microsoft-OutputFormat` fejléc. Minden egyes magában foglalja egy átviteli sebesség és a kódolási típusként. A Speech Services támogatja a 24 KHz, 16 KHz, és 8 KHz hang adja vissza.

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
> Ha a kiválasztott hang- és kimeneti formátum különböző átviteli sebességet, a hanganyag szükség szerint módosítva a felbontása. Azonban nem támogatja a 24khz beszédhangot `audio-16khz-16kbps-mono-siren` és `riff-16khz-16kbps-mono-siren` kimeneti formátumot.

## <a name="request-body"></a>A kérés törzse

A szervezet minden egyes `POST` kérelem érkezik [Speech összefoglaló Markup Language (SSML)](speech-synthesis-markup.md). SSML lehetővé teszi a hang- és a szöveg-hang transzformációs szolgáltatás által visszaadott szintetizált nyelvének kiválasztását. Támogatott beszédhangot teljes listáját lásd: [nyelvi támogatás](language-support.md#text-to-speech).

> [!NOTE]
> Ha egy egyéni beszédfelismerési használja, a kérelem törzsében elküldött pedig egyszerű szövegként (ASCII vagy UTF-8).

## <a name="sample-request"></a>Mintakérelem

A HTTP-kérelem SSML beszédfelismerési és nyelvi megadásához használja. A szervezet legfeljebb 1000 karakter.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

Gyorsútmutatókat nyelvspecifikus példákért lásd:

* [.NET Core, C#](quickstart-dotnet-text-to-speech.md)
* [Python](quickstart-python-text-to-speech.md)
* [Node.js](quickstart-nodejs-text-to-speech.md)

## <a name="http-status-codes"></a>HTTP-állapotkódok

A HTTP-állapotkód: minden válasz azt jelzi, hogy a sikeres vagy gyakori hibák.

| HTTP-állapotkód | Leírás | Lehetséges ok |
|------------------|-------------|-----------------|
| 200 | OK | A kérelem sikeres volt. a választörzs hangfájl mérete. |
| 400 | Hibás kérelem | Egy kötelező paraméter hiányzik, üres vagy null értékű. Másik lehetőségként átadott vagy egy kötelező vagy választható paraméter értéke érvénytelen. Egy gyakori probléma egy fejlécet, amely túl hosszú. |
| 401 | Nem engedélyezett | A kérelem nem engedélyezett. Ellenőrizze, hogy az előfizetési kulcs, vagy a jogkivonat érvényes, és a megfelelő régióban. |
| 413 | Kérelem az entitás túl nagy | A SSML bemeneti adat 1024 karakternél hosszabb. |
| 429 | Túl sok kérelem | Túllépte a kvótát, vagy engedélyezett az előfizetéséhez kérelmek száma. |
| 502 | Hibás átjáró | Hálózati vagy kiszolgálóoldali probléma. Érvénytelen fejlécek is jelezhet. |

Ha a HTTP-állapot `200 OK`, a válasz törzse tartalmazza a kért formátumban hangfájl. Ez a fájl átvitele, a puffer mentett, vagy egy fájlba menti, lejátszhatók.

## <a name="next-steps"></a>További lépések

- [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
