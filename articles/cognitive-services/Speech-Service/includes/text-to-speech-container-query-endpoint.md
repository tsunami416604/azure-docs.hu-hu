---
title: Szöveg – beszéd tároló végpontjának lekérdezése
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: b2a621a23a81e4fb4f47e7c99d780211973e30a0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81274991"
---
A tároló [REST-alapú Endpoint API-kat](../rest-text-to-speech.md)biztosít. A platformhoz, a keretrendszerhez és a nyelvi változatokhoz számos [minta forráskód-projekt](https://github.com/Azure-Samples/Cognitive-Speech-TTS) áll rendelkezésre.

A *normál szöveg-beszéd* tárolóval a letöltött Képcímke területi beállítását és hangját kell használnia. Ha például letöltötte a `latest` címkét, az alapértelmezett területi beállítás a `en-US` és a `JessaRUS` hang. Az `{VOICE_NAME}` argumentum ekkor a következő [`en-US-JessaRUS`](../language-support.md#standard-voices):. Tekintse meg az alábbi példában SSML:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Az *egyéni szöveg-beszéd* kapcsolathoz azonban az [Egyéni hangportálon](https://aka.ms/custom-voice-portal)kell megszereznie a **hangfelvételt/modellt** . Az egyéni modell neve szinonimája a hang nevét. Navigáljon a **képzés** lapra, és másolja a használni kívánt **hang/modell** `{VOICE_NAME}` argumentumként.
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="Egyéni hangmodell – hang neve":::

Tekintse meg az alábbi példában SSML:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Hozzon létre egy HTTP POST-kérelmet, amely néhány fejlécet és adattartalomot biztosít. Cserélje le `{VOICE_NAME}` a helyőrzőt a saját értékére.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

A parancs a következőket hajtja végre:

* Egy HTTP POST-kérelmet hoz létre a `speech/synthesize/cognitiveservices/v1` végponthoz.
* Egy `Accept` fejlécet ad meg`audio/*`
* A `Content-Type` fejlécének megadása `application/ssml+xml`, további információ: [kérelem törzse](../rest-text-to-speech.md#request-body).
* A `X-Microsoft-OutputFormat` fejlécét adja `riff-16khz-16bit-mono-pcm`meg, további beállításokért lásd: [hangkimenet](../rest-text-to-speech.md#audio-outputs).
* A [Speech szintézis Markup Language (SSML)](../speech-synthesis-markup.md) kérelem `{VOICE_NAME}` küldése a végpontnak.