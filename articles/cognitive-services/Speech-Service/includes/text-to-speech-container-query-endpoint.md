---
title: Szöveg – beszéd tároló végpontjának lekérdezése
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 8460ddca5cff2b3da540b5fa8cf66e0687892789
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491171"
---
A tároló [REST-alapú Endpoint API-kat](../rest-text-to-speech.md)biztosít. A platformhoz, a keretrendszerhez és a nyelvi változatokhoz számos [minta forráskód-projekt](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/) áll rendelkezésre.

A *normál szöveg-beszéd* tárolóval a letöltött Képcímke területi beállítását és hangját kell használnia. Ha például letöltötte a `latest` címkét, az alapértelmezett területi beállítás `en-US` és a `JessaRUS` hangja. Ezután a `{VOICE_NAME}` argumentum [`en-US-JessaRUS`](../language-support.md#standard-voices). Tekintse meg az alábbi példában SSML:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Az *egyéni szöveg-beszéd* kapcsolathoz azonban az [Egyéni hangportálon](https://aka.ms/custom-voice-portal)kell megszereznie a **hangfelvételt/modellt** . Az egyéni modell neve szinonimája a hang nevét. Navigáljon a **képzés** lapra, és másolja a `{VOICE_NAME}` argumentumként használni kívánt **hang/modellt** .
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

Hozzon létre egy HTTP POST-kérelmet, amely néhány fejlécet és adattartalomot biztosít. Cserélje le a `{VOICE_NAME}` helyőrzőt a saját értékére.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

Ez a parancs:

* Egy HTTP POST-kérelmet hoz létre az `speech/synthesize/cognitiveservices/v1` végponthoz.
* `audio/*` `Accept` fejlécének megadása
* `application/ssml+xml``Content-Type` fejlécének megadása, további információ: [kérelem törzse](../rest-text-to-speech.md#request-body).
* `riff-16khz-16bit-mono-pcm``X-Microsoft-OutputFormat` fejlécét adja meg, további beállításokért lásd: [hang kimenete](../rest-text-to-speech.md#audio-outputs).
* Elküldi a [Speech szintézis Markup Language (SSML)](../speech-synthesis-markup.md) kérést a végpontnak `{VOICE_NAME}`.