---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 02227a0d4b9bf436632c5179696228ae865ff866
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943749"
---
Ha tömörített hangformátumot szeretne továbbítani a beszédfelismerési szolgáltatásba, hozzon létre `PullAudioInputStream` vagy `PushAudioInputStream`. Ezután hozzon létre egy `AudioConfig` a stream osztály egy példányáról, és adja meg az adatfolyam tömörítési formátumát.

Tegyük fel, hogy rendelkezik egy `pushStream` nevű bemeneti adatfolyam-osztállyal, és az OPUS/OGG-t használja. A kód így néz ki:

```cpp
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;

// ... omitted for brevity

 auto config =
    SpeechConfig::FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion"
    );

auto audioFormat =
    AudioStreamFormat::GetCompressedFormat(
        AudioStreamContainerFormat::OGG_OPUS
    );
auto audioConfig =
    AudioConfig::FromStreamInput(
        pushStream,
        audioFormat
    );

auto recognizer = SpeechRecognizer::FromConfig(config, audioConfig);
auto result = recognizer->RecognizeOnceAsync().get();

auto text = result->Text;
```