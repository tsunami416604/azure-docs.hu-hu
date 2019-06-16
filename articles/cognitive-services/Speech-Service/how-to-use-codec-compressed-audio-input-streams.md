---
title: Stream kodek tömörített hang-, beszéd SDK – beszédszolgáltatások
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan streamelhet tömörített hang Azure beszédszolgáltatások beszédfelismerési SDK-val való. C++, a rendelkezésre álló C#, és a Linux rendszeren Java használatával.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: amishu
ms.openlocfilehash: 8717dad27786450e4275c9fd91523c0e9379f375
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073060"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>A beszédfelismerés SDK-val hangbemenet kodekkel tömörített

A beszédfelismerés SDK **tömörített hang bemeneti Stream** API lehetővé teszi a tömörített hangot a beszédfelismerési szolgáltatás adatfolyam PullStream vagy PushStream használatával.

> [!IMPORTANT]
> Tömörített hang streamelési csak a támogatott C++, C#, és a Java használatával linuxon (Ubuntu 16.04, Ubuntu 18.04, Debian 9).
> Beszéd SDK 1.4.0-s verzió vagy újabb verziója szükséges.

A PCM/wav a által speech dokumentációjában talál.  Wav/PCM, kívül a következő kodek tömörített bemeneti formátumokat támogatja:

- MP3
- OPUS/OGG

## <a name="prerequisites-to-using-codec-compressed-audio-input"></a>Előfeltételek kodekkel tömörített hangbemenet

A tömörített hangbemenet használata a Speech SDK linuxhoz további függőségek telepítése:

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="example-code-using-codec-compressed-audio-input"></a>Kódpéldákat kodekkel tömörített hangbemenet

A Speech Services hang tömörített stream, hozzon létre `PullAudioInputStream` vagy `PushAudioInputStream`. Ezután hozzon létre egy `AudioConfig` a stream osztály egy példányát a adja meg a Stream a tömörítési formátumot.

Tegyük fel, hogy rendelkezik-e egy bemeneti stream nevű osztályt `myPushStream` és OPUS/OGG használják. A kód előfordulhat, hogy néznek ki:

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
