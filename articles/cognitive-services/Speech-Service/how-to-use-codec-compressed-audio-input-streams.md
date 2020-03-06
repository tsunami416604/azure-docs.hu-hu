---
title: Stream codec tömörített hang a Speech SDK-Speech szolgáltatással
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan továbbíthat tömörített hangot a beszédfelismerési szolgáltatásnak a Speech SDK-val. Elérhető for C++, C#, és Java for Linux, Java Androidon és Objective-C iOS-ben.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: amishu
ms.openlocfilehash: 2b530da06b02091ce66ff7c116f3e17ddcc22497
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331110"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>A codec tömörített hangbemenetének használata a Speech SDK-val

A Speech SDK **tömörített hangbemeneti stream** API-val a PullStream vagy a PushStream segítségével továbbíthatja a tömörített hangokat a beszédfelismerési szolgáltatásba.

> [!IMPORTANT]
> A streaming tömörített bemeneti hang jelenleg a C++, C#a és a Java Linux rendszeren (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8) támogatott. A [Java az](how-to-use-codec-compressed-audio-input-streams-android.md) iOS platformon is támogatja az Android és [Objective-C](how-to-use-codec-compressed-audio-input-streams-ios.md) nyelveken.
> A Speech SDK 1.7.0 vagy újabb verziója szükséges (1.10.0 vagy újabb verzió a RHEL 8, CentOS 8 esetében).

A WAV/PCM esetében tekintse meg a fővonal beszédének dokumentációját.  A WAV/PCM-en kívül a következő codec tömörített bemeneti formátumok támogatottak:

- MP3
- OPUS/OGG
- FLAC
- ATÖRVÉNY a WAV-tárolóban
- MULAW a WAV-tárolóban

## <a name="prerequisites"></a>Előfeltételek

A tömörített hang kezelésére a [GStreamer](https://gstreamer.freedesktop.org)használatával kerül sor. A licencelési okok miatt a GStreamer bináris fájljai nincsenek lefordítva és társítva a Speech SDK-val. Így az alkalmazás fejlesztője a következőt kell telepítenie a 18,04, a 16,04 és a Debian 9 rendszerre a tömörített bemeneti hang használatához.

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

RHEL/CentOS 8 rendszeren:

```sh
sudo yum install gstreamer1 gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-plugins-bad-free gstreamer1-plugins-ugly-free
```

> [!NOTE]
> A RHEL/CentOS 8 rendszeren kövesse az [OpenSSL Linux rendszerhez való konfigurálásának](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)utasításait.

## <a name="example-code-using-codec-compressed-audio-input"></a>Kód tömörített hangbemenetet használó programkód

Ha tömörített hangformátumot szeretne továbbítani a beszédfelismerési szolgáltatásba, hozzon létre `PullAudioInputStream` vagy `PushAudioInputStream`. Ezután hozzon létre egy `AudioConfig` a stream osztály egy példányáról, és adja meg az adatfolyam tömörítési formátumát.

Tegyük fel, hogy rendelkezik egy `myPushStream` nevű bemeneti adatfolyam-osztállyal, és az OPUS/OGG-t használja. A kód így néz ki:

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

## <a name="next-steps"></a>Következő lépések

- [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [Lásd: a beszédfelismerés felismerése Java-ban](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
