---
title: Stream codec tömörített hang a Speech SDK-Speech szolgáltatással
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan továbbíthatja az Azure Speech Services tömörített hangját a Speech SDK-val. Elérhető for C++, C#, és Java for Linux.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.openlocfilehash: b29b42dea9522526d49c1bda017a522855946def
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559547"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>A codec tömörített hangbemenetének használata a Speech SDK-val

A Speech SDK **tömörített hangbemeneti stream** API-val a PullStream vagy a PushStream segítségével továbbíthatja a tömörített hangokat a beszédfelismerési szolgáltatásba.

> [!IMPORTANT]
> A streaming tömörített hang csak a C++, C#a és a Java Linux rendszeren (Ubuntu 16,04, Ubuntu 18,04, Debian 9) támogatott.
> A Speech SDK 1.4.0 vagy újabb verziója szükséges.

A WAV/PCM esetében tekintse meg a fővonal beszédének dokumentációját.  A WAV/PCM-en kívül a következő codec tömörített bemeneti formátumok támogatottak:

- MP3
- OPUS/OGG

## <a name="prerequisites-to-using-codec-compressed-audio-input"></a>A kodek tömörített hangbemenetének használatának előfeltételei

Ezeket a további függőségeket a Linux Speech SDK-val tömörített hangbemenetek használatával telepítheti:

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="example-code-using-codec-compressed-audio-input"></a>Kód tömörített hangbemenetet használó programkód

Ha tömörített hangformátumot szeretne továbbítani a Speech servicesbe, hozzon `PushAudioInputStream`létre `PullAudioInputStream` vagy. Ezután hozzon létre `AudioConfig` egy példányt a stream osztály egy példányáról, és adja meg az adatfolyam tömörítési formátumát.

Tegyük fel, hogy rendelkezik egy nevű `myPushStream` bemeneti stream-osztállyal, és az Opus/OGG-t használja. A kód így néz ki:

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

// Create an audio config specifying the compressed audio format and the instance of your input stream class.
var audioFormat = AudioStreamFormat.GetCompressedFormat(AudioStreamContainerFormat.OGG_OPUS);
var audioConfig = AudioConfig.FromStreamInput(myPushStream, audioFormat);

var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

var result = await recognizer.RecognizeOnceAsync();

var text = result.GetText();
```

## <a name="next-steps"></a>További lépések

- [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [A beszédfelismerést a C#-ban való használatáról](quickstart-csharp-dotnet-windows.md)
