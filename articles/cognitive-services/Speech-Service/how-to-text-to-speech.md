---
title: Használat szöveg-beszéd átalakítás beszédszolgáltatások használata |} A Microsoft Docs
description: Ismerje meg, hogyan használja szöveget beszéddé használja a Speech service-ben.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 5eb0839cb973ba2f8bc56dc240fc60817b24b3b6
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39068862"
---
# <a name="use-text-to-speech-in-speech-service"></a>Használja a "Szöveg-beszéd átalakítás" Speech service-ben

A beszédfelismerési szolgáltatás szöveg-beszéd átalakítás funkciókat egy egyszerű HTTP-kérés biztosít. A szöveget, a megfelelő végpontra könyvelés és a szolgáltatás egy hangfájlt adja vissza (`.wav`) tartalmazó beszédszintetizátorral. Az alkalmazás használhatja ezt a hang, akkor kedveli.

Szöveg-beszéd átalakítás lehet egyszerű szöveges (ASCII vagy UTF8), vagy egy kérelmet, a bejegyzés törzse [SSML](speech-synthesis-markup.md) dokumentumot. Egyszerű szöveges kérelmek egy alapértelmezett hangjával vannak szóbeli. A legtöbb esetben érdemes egy SSML szerv használja. A HTTP-kérelem tartalmaznia kell egy [engedélyezési](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#authentication) token. 

A regionális szöveg-beszéd átalakítás végpontok Itt jelennek meg. Használja az egyik szükséges az előfizetéshez.

[!include[](includes/endpoints-text-to-speech.md)]

## <a name="specify-a-voice"></a>Adjon meg egy hang

A hang megadásához használja a `<voice>` [SSML](speech-synthesis-markup.md) címke. Példa:

```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
  <voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Hello, world!
  </voice>
</speak>
```

Lásd: [szöveg-beszéd átalakítás beszédhangot](supported-languages.md#text-to-speech) listáját az elérhető hangok és nevét.

## <a name="make-a-request"></a>Kérés

Egy szöveg-beszéd átalakítás HTTP kérés érkezik POST módban a szöveget kell beszélt a kérelem törzsében. A HTTP-kérés törzse hossza legfeljebb 1024 karakter. A kérés a következő fejléceket kell lennie: 

Fejléc|Értékek|Megjegyzések
-|-|-
|`Content-Type` | `application/ssml+xml` | A bemeneti szöveges formátum.
|`X-Microsoft-OutputFormat`|     `raw-16khz-16bit-mono-pcm`<br>`audio-16khz-16kbps-mono-siren`<br>`riff-16khz-16kbps-mono-siren`<br>`riff-16khz-16bit-mono-pcm`<br>`audio-16khz-128kbitrate-mono-mp3`<br>`audio-16khz-64kbitrate-mono-mp3`<br>`audio-16khz-32kbitrate-mono-mp3`<br>`raw-24khz-16bit-mono-pcm`<br>`riff-24khz-16bit-mono-pcm`<br>`audio-24khz-160kbitrate-mono-mp3`<br>`audio-24khz-96kbitrate-mono-mp3`<br>`audio-24khz-48kbitrate-mono-mp3` | A kimeneti audio formátum.
|`User-Agent`   |Alkalmazásnév | Az alkalmazásnév megadása kötelező, és legfeljebb 255 karakterből állhat.
| `Authorization`   | A jogkivonat-szolgáltatás az előfizetési kulcs szabályzatkérelem kapott engedélyezési jogkivonatot. Minden tokenhez a tíz percig érvényes. Lásd: [REST API-k: hitelesítés](rest-apis.md#authentication).

> [!NOTE]
> Ha a kiválasztott hang- és kimeneti formátum különböző átviteli sebességet, a hanganyag szükség szerint módosítva a felbontása. nem támogatja a 24khz beszédhangot `audio-16khz-16kbps-mono-siren` és `riff-16khz-16kbps-mono-siren` kimeneti formátumot. 

Az alábbiakban látható egy mintakérelmet.

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

200-as állapotú a válasz törzse tartalmazza a megadott kimeneti formátum hang.

```
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

Ha hiba történik, az alábbi állapotkódok használja. A válasz törzse a hibát a probléma leírását is tartalmaz.

|Kód|Leírás|Probléma|
|-|-|-|
400 |Hibás kérelem |Egy kötelező paraméter hiányzik, üres vagy null értékű. Másik lehetőségként átadott vagy egy kötelező vagy választható paraméter értéke érvénytelen. Egy gyakori probléma egy fejlécet, amely túl hosszú.
401|Nem engedélyezett |A kérelem nem engedélyezett. Ellenőrizze, hogy érvényes-e az előfizetés, vagy egy token.
413|Kérelem az entitás túl nagy|A SSML bemeneti adat 1024 karakternél hosszabb.
|502|Hibás átjáró    | Hálózati vagy kiszolgálóoldali probléma. Érvénytelen fejlécek is jelezhet.

A szöveg Speech REST API további információkért lásd: [REST API-k](rest-apis.md#text-to-speech).

## <a name="next-steps"></a>További lépések

- [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [A C++ beszédfelismerést](quickstart-cpp-windows.md)
- [A beszédfelismerést a C#-ban](quickstart-csharp-dotnet-windows.md)
- [A beszédfelismerést Java nyelven](quickstart-java-android.md)
