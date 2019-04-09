---
title: Tömörített Stream hang-, beszéd SDK – beszédszolgáltatások
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan streamelhet tömörített hang Azure beszédszolgáltatások beszédfelismerési SDK-val való. C++, a rendelkezésre álló C#, és a Linux rendszeren Java használatával.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: amishu
ms.openlocfilehash: 2066dc3e20ab9fc92b23fd071728ea6a920d3324
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012422"
---
# <a name="stream-compressed-audio-with-the-speech-sdk"></a>Tömörített Stream hang, a beszéd SDK-val

A beszédfelismerés SDK **tömörített hang bemeneti Stream** API lehetővé teszi a tömörített hangot a beszédfelismerési szolgáltatás adatfolyam PullStream vagy PushStream használatával.

> [!IMPORTANT]
> Tömörített hang streamelési csak a támogatott C++ C#, és a Java használatával linuxon (Ubuntu 16.04 vagy Ubuntu 18.04).
> Támogatási MP3 és OPUS/OGG korlátozódik.

## <a name="prerequisites"></a>Előfeltételek

Telepítenie kell ezeket a függőségeket, tömörített hangbemenet használata a Speech SDK Linux rendszeren:

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="streaming-compressed-audio"></a>Hang tömörített adatfolyam

A Speech Services hang tömörített stream, hozzon létre `PullAudioInputStream` vagy `PushAudioInputStream`. Ezután hozzon létre egy `AudioConfig` a stream osztály egy példányát a adja meg a Stream a tömörítési formátumot.

Tegyük fel, hogy rendelkezik-e egy bemeneti stream nevű osztályt `myPushStream` és OPUS/OGG használják. Ez a kód előfordulhat, hogy kinézni: 

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

* [A beszédfelismerés próbaverziós előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [A beszédfelismerést a C#-ban való használatáról](quickstart-csharp-dotnet-windows.md)
