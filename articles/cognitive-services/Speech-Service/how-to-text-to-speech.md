---
title: Használjon szöveg-beszéd átalakítás beszéd szolgáltatásokkal |} Microsoft Docs
description: Megtudhatja, hogyan használja szöveget beszéddé használja a beszédfelismerés szolgáltatásban.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 1ed2ee73b32f71d2e1ca34c6de9d1cb2649d7f0c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349714"
---
# <a name="use-text-to-speech-in-speech-service"></a>Használja a "Szöveg-beszéd átalakítás" beszéd szolgáltatásban

A beszédfelismerés szolgáltatás szöveg-beszéd átalakítás funkció egy egyszerű HTTP-kérelem keresztül biztosít. A szöveget, a megfelelő végpontnak KÖZZÉTESZ, és a szolgáltatás hang adja vissza (`.wav`) tartalmazó beszédszintetizátorral. Az alkalmazás ezután használhatja a hang kedveli azt.

A szervezet a POST kérelem szöveg-beszéd átalakítás lehet egyszerű szöveges (ASCII vagy UTF8), vagy egy [SSML](speech-synthesis-markup.md) dokumentum. Egyszerű szöveges kérelmek alapértelmezett hangot az éppen kiejtett. A legtöbb esetben használni kívánt az SSML-törzsében. A HTTP-kérelem tartalmaznia kell egy engedélyezési jogkivonatot. 

A regionális szöveg-beszéd átalakítás végpontok itt látható. Használhatja a megfelelő az előfizetéséhez.

Régió| Végpont
-|-
USA nyugati régiója| `https://westus.tts.speech.microsoft.com/cognitiveservices/v1`
Kelet-Ázsia| `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1`
Észak-Európa| `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1`

> [!NOTE]
> Ha létrehozott egy egyedi Hangüzenetek betűkészletet, használja a végpontot, ekkor létrehozott fenti helyett.

## <a name="specify-a-voice"></a>Adjon meg egy hang

Egy hang megadásához használja a `<voice>` [SSML](speech-synthesis-markup.md) címke. Példa:

```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
  <voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Hello, world!
  </voice>
</speak>
```

Lásd: [szöveg-beszéd átalakítás hangok](supported-languages.md#text-to-speech) az elérhető hangok és a nevek listája.

## <a name="make-a-request"></a>A kérelem

A szöveg-beszéd átalakítás HTTP kérelem szöveget a FELADÁS egy vagy több módban az a kérelem törzsében elhangzani. A HTTP-kérés törzsében hossza legfeljebb 1024 karakter lehet. A kérelem a következő fejlécek kell rendelkeznie: 

Fejléc|Értékek|Megjegyzések
-|-|-
|`Content-Type` | `application/ssml+xml` | A bemeneti szöveg formátumban.
|`X-Microsoft-OutputFormat`|     `raw-16khz-16bit-mono-pcm`<br>`audio-16khz-16kbps-mono-siren`<br>`riff-16khz-16kbps-mono-siren`<br>`riff-16khz-16bit-mono-pcm`<br>`audio-16khz-128kbitrate-mono-mp3`<br>`audio-16khz-64kbitrate-mono-mp3`<br>`audio-16khz-32kbitrate-mono-mp3`<br>`raw-24khz-16bit-mono-pcm`<br>`riff-24khz-16bit-mono-pcm`<br>`audio-24khz-160kbitrate-mono-mp3`<br>`audio-24khz-96kbitrate-mono-mp3`<br>`audio-24khz-48kbitrate-mono-mp3` | A kimeneti hangformátum.
|`User-Agent`   |Alkalmazásnév | Az alkalmazás neve szükség, és legfeljebb 255 karakterből állhat.
| `Authorization`   | Engedélyezési jogkivonatot kapott az Előfizetés kulcs a jogkivonat-szolgáltatás segítségével. A tokenek érvénytelen tíz perc. Lásd: [REST API-k: hitelesítési](rest-apis.md#authentication).

> [!NOTE]
> Ha a kijelölt hang- és kimeneti formátumot különböző átviteli sebességet, a hang szükség szerint módosítva a felbontása. 24khz hangok nem támogatják a `audio-16khz-16kbps-mono-siren` és `riff-16khz-16kbps-mono-siren` kimeneti formátumot. 

Az alábbiakban látható egy minta kérelem.

```xml
POST /cognitiveservices/v1
HTTP/1.1
Host: westus.tts.speech.microsoft.com
X-Microsoft-OutputFormat: riff-24khz-16bit-mono-pcm
Content-Type: application/ssml+xml
User-Agent: Test TTS application
Authorization: (authorization token)

<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Hello, world!
</voice> </speak>
```

200-as állapotú adott válasz törzsének tartalmazza a megadott kimeneti formátum hang.

```
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

Ha hiba lép fel, az alábbi állapotkódok használja. A hiba a választörzs is a probléma leírását tartalmazza.

|Kód|Leírás|Probléma|
|-|-|-|
400 |Hibás kérelem |Egy kötelező paraméter hiányzik, üres vagy null. Vagy a átadott vagy egy kötelező vagy választható paraméter értéke érvénytelen. Általános hiba túl hosszú fejléc.
401|Nem engedélyezett |A kérelem nem engedélyezett. Győződjön meg arról, hogy az Előfizetés kulcs, vagy a token érvényes.
413|A kérelem túl nagy|Az SSML-bemenet 1024 karakternél hosszabb.
|502|Hibás átjáró    | Hálózati vagy kiszolgálóoldali probléma. Érvénytelen fejlécek is utalhat.

A szöveg beszéd REST API-t a további információkért lásd: [REST API-k](rest-apis.md#text-to-speech).

## <a name="next-steps"></a>További lépések

- [Beszéd próbaverziós előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [A C# beszéd felismerésére](quickstart-csharp-windows.md)
