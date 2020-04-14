---
title: Szövegfelolvasó tároló végpontjának lekérdezése
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: b2a621a23a81e4fb4f47e7c99d780211973e30a0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81274991"
---
A tároló [REST-alapú végpontAPI-kat](../rest-text-to-speech.md)biztosít. A platform-, keretrendszer- és nyelvi változatokhoz számos [mintaforráskód-projekt](https://github.com/Azure-Samples/Cognitive-Speech-TTS) áll rendelkezésre.

A *szabványos szövegfelolvasó* tárolóval a letöltött képcímke területi beállítására és hangjára kell támaszkodnia. Ha például letöltötte `latest` a címkét, `en-US` az `JessaRUS` alapértelmezett területi beállítás és a hang. Az `{VOICE_NAME}` érv ezután lesz [`en-US-JessaRUS`](../language-support.md#standard-voices). Lásd az alábbi SSML példát:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Az *egyéni szövegfelolvasáshoz* azonban be kell szereznie a **Hang/ modell** t az egyéni [hangportálról.](https://aka.ms/custom-voice-portal) Az egyéni modellnév a hangnév szinonimája. Keresse meg a **Betanítás** lapot, és másolja `{VOICE_NAME}` a Hang **/ modellt,** amelyet argumentumként szeretne használni.
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="Egyéni hangmodell - hangnév":::

Lásd az alábbi SSML példát:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Készítsünk egy HTTP POST-kérelmet, amely néhány fejlécet és egy adathasznos adatot biztosít. Cserélje `{VOICE_NAME}` le a helyőrzőt a saját értékére.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

A parancs a következőket hajtja végre:

* HTTP POST-kérelmet hoz `speech/synthesize/cognitiveservices/v1` létre a végponthoz.
* Megadja a `Accept` fejlécet:`audio/*`
* A további `Content-Type` információt `application/ssml+xml`a fejlécben adja meg: [Request body](../rest-text-to-speech.md#request-body).
* A `X-Microsoft-OutputFormat` további beállításokhoz a fejlécet `riff-16khz-16bit-mono-pcm`adja meg, lásd a [hangkimenetet.](../rest-text-to-speech.md#audio-outputs)
* A [beszédszintetizáló nyelvi (SSML)](../speech-synthesis-markup.md) kérést a `{VOICE_NAME}` végpontnak küldi.